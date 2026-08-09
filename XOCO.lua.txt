game.Players.LocalPlayer.PlayerScripts.CharacterAndBeamMove.Enabled = false
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local CoreGui = game:GetService("CoreGui")
loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/katnaa-debug/SolarisUI/refs/heads/main/Library1.lua"))()

-- Unlock MouseMouseBehavior = Enum.MouseBehavior.Default

-- Global state storage
local ToggleStates = {}
function SetToggleState(flag, value) ToggleStates[flag] = value end
function GetToggleState(flag) return ToggleStates[flag] or false end

local Options = Library.Items or Library.Flags or {}
local Toggles = Library.Flags or Library.Items or {}
local Window = Library:CreateWindow({
	Title = "XOCU",
	Theme = "Void",
	ToggleKey = Enum.KeyCode.RightShift,
	Transparency = 0.25,
	ShowWatermark = { Enabled = true, Title = true, User = true, FPS = true, Duration = false, Ping = true },
	AutoSave = true,
	ConfigFolder = "XOCU_Config"
})
local Tabs = {
	Defense = Window:CreateTab("defense", true, "7461510456"),
	Target = Window:CreateTab("target", true, "107058246184363"),
	Grab = Window:CreateTab("grab", true, "85607241723723"), -- РќРѕРІР° С–РєРѕРЅРєР° Drag
	Player = Window:CreateTab("player", true, "124871982298256"),
	Misc = Window:CreateTab("misc", true, "114167292947807"), -- РќРѕРІР° С–РєРѕРЅРєР° Sparkles
	Keybinds = Window:CreateTab("keybinds", true, "4814130203"), -- РќРѕРІР° С–РєРѕРЅРєР° BadgeLogo
	Visuals  = Window:CreateTab("visuals",  true, "7733955511"),
}
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local StarterGui = game:GetService("StarterGui")
local PS = game:GetService("Players")
local RS = game:GetService("ReplicatedStorage")
local R = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = workspace
local Player = PS.LocalPlayer
local Camera = Workspace.CurrentCamera
local CE = RS:WaitForChild("CharacterEvents", 10)
local BeingHeld = Player:WaitForChild("IsHeld", 10)
local StruggleEvent = CE and CE:WaitForChild("Struggle")
function notify(title, content, duration)
	Library:Notify({ Title = title or "Notification", Content = content or "", Duration = duration or 5,
	 })
end
function sendHubLoadedMessage()
	local message = " Owner Version | XOCU loaded. "
	local sent = false
	pcall(function()
		local chatEvents = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents")
		if chatEvents then
			local say = chatEvents:FindFirstChild("SayMessageRequest")
			if say and typeof(say.FireServer) == "function" then
				say:FireServer(message, "All")
				sent = true
			end
		end
	end)
	if not sent then
		pcall(function()
			StarterGui:SetCore("ChatMakeSystemMessage", {
				Name = message;
				Color = Color3.fromRGB(255, 170, 0);
				Font = Enum.Font.SourceSansBold;
				FontSize = Enum.FontSize.Size18;
			})
		end)
	end
end
task.spawn(function()
	task.wait(1)
	sendHubLoadedMessage()
end)
local paintPartsBackup = {}
local paintConnections = {}
function deleteAllPaintParts()
	for _, obj in ipairs(Workspace:GetDescendants()) do
		if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
			local clone = obj:Clone()
			clone.Archivable = true
			paintPartsBackup[obj:GetDebugId()] = {
				clone = clone,
				parent = obj.Parent
			}
			obj:Destroy()
		end
	end
end
local function restorePaintParts()
	for _, data in pairs(paintPartsBackup) do
		if data.clone and data.parent then
			data.clone.Parent = data.parent
		end
	end
	paintPartsBackup = {}
end
local function watchNewPaintParts()
	table.insert(paintConnections, Workspace.DescendantAdded:Connect(function(obj)
		if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
			task.defer(function()
				if obj and obj.Parent then
					local clone = obj:Clone()
					clone.Archivable = true
					paintPartsBackup[obj:GetDebugId()] = {
						clone = clone,
						parent = obj.Parent
					}
					obj:Destroy()
				end
			end)
		end
	end))
end
local function disconnectWatchers()
	for _, conn in ipairs(paintConnections) do
		if conn.Connected then
			conn:Disconnect()
		end
	end
	paintConnections = {}
end
local function setTouchQuery(state)
	local char = Workspace:FindFirstChild(Player.Name)
	if not char then
		return
	end
	for _, v in ipairs(char:GetChildren()) do
		if v:IsA("Part") or v:IsA("BasePart") then
			v.CanTouch = state
			v.CanQuery = state
		end
	end
end
local antiGucciConnection
local safePosition
local restoreFrames = 0
local function spawnBlobman()
	local args = {
		[1] = "CreatureBlobman",
		[2] = CFrame.new(0, 5000000, 0),
		[3] = Vector3.new(0, 60, 0)
	}
	pcall(function()
		ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
	end)
	local folder = Workspace:WaitForChild(Player.Name .. "SpawnedInToys", 5)
	if folder and folder:FindFirstChild("CreatureBlobman") then
		local blob = folder.CreatureBlobman
		if blob:FindFirstChild("Head") then
			blob.Head.CFrame = CFrame.new(0, 50000, 0)
			blob.Head.Anchored = true
		end
		notify("Success", "Blobman Spawned!", 3)
	end
end
local function startAntiGucci()
	local character = Player.Character or Player.CharacterAdded:Wait()
	local humanoid = character:WaitForChild("Humanoid")
	local rootPart = character:WaitForChild("HumanoidRootPart")
	safePosition = rootPart.Position
	local folder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
	local blob = folder and folder:FindFirstChild("CreatureBlobman")
	local seat = blob and blob:FindFirstChild("VehicleSeat")
	if not blob then
		spawnBlobman()
		task.wait(0.3)
		folder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
		blob = folder and folder:FindFirstChild("CreatureBlobman")
		seat = blob and blob:FindFirstChild("VehicleSeat")
	end
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
	if antiGucciConnection then
		antiGucciConnection:Disconnect()
	end
	antiGucciConnection = R.Heartbeat:Connect(function()
		if not rootPart or not humanoid then
			return
		end
		ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
		if restoreFrames > 0 then
			rootPart.CFrame = CFrame.new(safePosition)
			restoreFrames = restoreFrames - 1
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
local function stopAntiGucci()
	if antiGucciConnection then
		antiGucciConnection:Disconnect()
		antiGucciConnection = nil
	end
	-- Unsit humanoid first so the server releases the seat
	local char = Player.Character
	local hum = char and char:FindFirstChild("Humanoid")
	if hum then
		hum.Sit = false
		pcall(function() hum:ChangeState(Enum.HumanoidStateType.GettingUp) end)
	end
	local blobFolder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
	if blobFolder and blobFolder:FindFirstChild("CreatureBlobman") then
		local blob = blobFolder.CreatureBlobman
		-- Fire server-side destroy remote so it fully removes server-side
		pcall(function()
			ReplicatedStorage.MenuToys.DestroyToy:FireServer(blob)
		end)
		task.wait(0.1)
		-- Fallback local destroy in case remote didn't work
		if blob and blob.Parent then
			blob:Destroy()
		end
	end
end
local antiGucciConnectionTrain
local safePositionTrain
local restoreFramesTrain = 0
local function startAntiGucciTrain()
	local character = Player.Character or Player.CharacterAdded:Wait()
	local humanoid = character:WaitForChild("Humanoid")
	local rootPart = character:WaitForChild("HumanoidRootPart")
	safePositionTrain = rootPart.Position
	local folder = workspace.Map.AlwaysHereTweenedObjects
	local train = folder and folder:FindFirstChild("Train")
	local seat
	if train then
		for _, d in ipairs(train:GetDescendants()) do
			if d:IsA("Seat") then
				seat = d
				break
			end
		end
	end
	if seat then
		rootPart.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
		seat:Sit(humanoid)
	end
	humanoid:GetPropertyChangedSignal("Jump"):Connect(function()
		if humanoid.Jump and humanoid.Sit then
			restoreFramesTrain = 15
			safePositionTrain = rootPart.Position
		end
	end)
	if antiGucciConnectionTrain then
		antiGucciConnectionTrain:Disconnect()
	end
	antiGucciConnectionTrain = R.Heartbeat:Connect(function()
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
		task.wait(0.5)
		rootPart.CFrame = CFrame.new(safePositionTrain)
	end)
end
local function stopAntiGucciTrain()
	if antiGucciConnectionTrain then
		antiGucciConnectionTrain:Disconnect()
		antiGucciConnectionTrain = nil
	end
	local trainFolder = workspace.Map.AlwaysHereTweenedObjects
	if trainFolder and trainFolder:FindFirstChild("Train") then
		ResetPlayer(game.Players.LocalPlayer)
	end
end
local DefenseGroup = Tabs.Defense:CreateBlock({Name = "Defense Main", Side = "Left"})
local DefenseExtra = Tabs.Defense:CreateBlock({Name = "Extra Defense", Side = "Right"})
local antiGrabExplosionConn, antiGrabHeldConn, antiGrabStruggleConn, antiGrabHumConn, antiGrabAnchorConn
local antiGrabRootCF, antiGrabRootPos, antiGrabHardFreeze = nil, nil, false
local function antiGrabUnfreeze(char)
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if hrp then
		hrp.Anchored = false
		if hrp:FindFirstChild("FreezeJoint") then
			hrp.FreezeJoint:Destroy()
		end
	end
	antiGrabHardFreeze = false
	if antiGrabAnchorConn then
		antiGrabAnchorConn:Disconnect()
		antiGrabAnchorConn = nil
	end
end
local function antiGrabFreezeInPlace(char)
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if not hrp then
		return
	end
	antiGrabRootCF = hrp.CFrame
	antiGrabRootPos = hrp.Position
	antiGrabHardFreeze = true
	if not hrp:FindFirstChild("FreezeJoint") then
		local align = Instance.new("AlignPosition")
		align.Name = "FreezeJoint"
		align.Mode = Enum.PositionAlignmentMode.OneAttachment
		align.MaxForce = 1e6
		align.MaxVelocity = 0
		align.Responsiveness = 200
		local att = Instance.new("Attachment", hrp)
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
local function antiGrabReconnect()
	local char = Player.Character or Player.CharacterAdded:Wait()
	local hum = char:WaitForChild("Humanoid")
	local hrp = char:WaitForChild("HumanoidRootPart")
	local fp = hrp:FindFirstChild("FirePlayerPart")
	if fp then
		fp:Destroy()
	end
	if antiGrabHumConn then
		antiGrabHumConn:Disconnect()
	end
	antiGrabHumConn = hum.Changed:Connect(function(p)
		if p == "Sit" and hum.Sit then
			if not (hum.SeatPart and tostring(hum.SeatPart.Parent) == "CreatureBlobman") then
				hum:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
				hum.Sit = false
			end
		end
	end)
end
local autoStruggleConn = nil
local AntiGrabEnabled = false
local HeldConnection = nil
-- Anti Grab [BETA]
do
    local AntiGrab = false
    local AntiGrabProc = false
    local AGWalk = false
    local Cons = {}
    
    local function DiscAll()
        for k, v in pairs(Cons) do
            if v then v:Disconnect() end
        end
        table.clear(Cons)
    end

    local function ApplyAntiGrab(char)
        if not char or not AntiGrab then return end
        
        -- Use WaitForChild to prevent script breaking if parts haven't loaded yet
        local hrp = char:WaitForChild("HumanoidRootPart", 5)
        local hum = char:WaitForChild("Humanoid", 5)
        local head = char:WaitForChild("Head", 5)
        if not (hrp and hum and head) then return end

        -- Initial constraint disabling
        for _, v in pairs(char:GetChildren()) do
            if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
                v.BallSocketConstraint.Enabled = false
                if v:FindFirstChild("RagdollLimbPart") then
                    v.RagdollLimbPart.WeldConstraint.Enabled = false
                end
            end
        end

        Cons["AGHead"] = head.ChildAdded:Connect(function(PartOwner)
            if PartOwner.Name == "PartOwner" then
                if not AntiGrabProc then
                    AntiGrabProc = true
                    hum.Sit = false
                    StruggleEvent:FireServer(Player)
                    
                    task.spawn(function() 
                        while (head and head:FindFirstChild("PartOwner")) or BeingHeld.Value do
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
        
        local ragdolled = hum:WaitForChild("Ragdolled", 5)
        if ragdolled then
            Cons["AGRagdoll"] = ragdolled.Changed:Connect(function()
                if hum.Ragdolled.Value then
                    for _, v in pairs(char:GetChildren()) do
                        if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
                            v.BallSocketConstraint.Enabled = false
                            if v:FindFirstChild("RagdollLimbPart") then
                                v.RagdollLimbPart.WeldConstraint.Enabled = false
                            end
                        end
                    end
                end
            end)
        end
        
        local weldHRP = hrp:WaitForChild("WeldHRP", 5)
        if weldHRP then
            Cons["AGWeld"] = weldHRP.Changed:Connect(function()
                if hrp.WeldHRP.Enabled then
                    while not hum.Sit do task.wait() end
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

    DefenseGroup:CreateToggle({
        Name = "Anti Grab [BEST (use solo)]",
        Flag = "AntiGrab",
        Default = false,
        Callback = function(Value)
            AntiGrab = Value
            DiscAll()
            
            if AntiGrab then
                ApplyAntiGrab(Player.Character)
                Cons["AGChar"] = Player.CharacterAdded:Connect(ApplyAntiGrab)
            else
                local char = Player.Character
                if char then
                    for _, v in pairs(char:GetChildren()) do
                        if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
                            v.BallSocketConstraint.Enabled = false
                            if v:FindFirstChild("RagdollLimbPart") then
                                v.RagdollLimbPart.WeldConstraint.Enabled = true
                            end
                        end
                    end
                end
            end
        end
    })
end

-- =========================================================================
-- AntiGrab V3: INTEGRATED & REINFORCED DEFENSE
-- =========================================================================
do
    local Players = game:GetService("Players")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local RunService = game:GetService("RunService")
    local plr = Players.LocalPlayer
    local Player = plr

    local antiGrabConn = nil
    local AntiGrabEnabled = false 
    local HeldConnection = nil

    local isHeld = plr:WaitForChild("IsHeld", 10)
    local BeingHeld = isHeld
    if not isHeld then return end

    local struggleEvent = ReplicatedStorage:WaitForChild("CharacterEvents", 10)
    if struggleEvent then struggleEvent = struggleEvent:WaitForChild("Struggle", 10) end
    local StruggleEvent = struggleEvent

    -- =====================================================================
    -- MODIFIED LOGIC: REMOVED POSITION SAVING/TELEPORTING
    -- =====================================================================
    local function onHeldChanged(heldState)
        if not AntiGrabEnabled then return end 
        local char = plr.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        
        if heldState then
            if hrp then 
                hrp.Anchored = true 
            end
            task.spawn(function()
                while isHeld.Value do 
                    StruggleEvent:FireServer(plr)
                    task.wait() 
                end
                if hrp then 
                    hrp.Anchored = false 
                end
            end)
        else
            if hrp then 
                hrp.Anchored = false 
            end
        end
    end

    antiGrabConn = isHeld.Changed:Connect(onHeldChanged)
    if isHeld.Value then onHeldChanged(true) end

    -- =====================================================================
    -- REINFORCED V3 LOGIC (CLEANED)
    -- =====================================================================
    local function StopAntiGrab()
        AntiGrabEnabled = false
        if HeldConnection then HeldConnection:Disconnect(); HeldConnection = nil end
        local Root = Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
        if Root then Root.Anchored = false end
        
        -- Restore Collision
        for _, part in pairs(Player.Character:GetDescendants()) do 
            if part:IsA("BasePart") then part.CanCollide = true end 
        end
    end

    local function StartAntiGrab()
        if HeldConnection then HeldConnection:Disconnect() end
        HeldConnection = BeingHeld:GetPropertyChangedSignal("Value"):Connect(function()
            if not AntiGrabEnabled or not BeingHeld.Value then return end
            local Char = Player.Character
            if not Char then return end
            
            local Root = Char:FindFirstChild("HumanoidRootPart")
            local Hum = Char:FindFirstChildOfClass("Humanoid")

            -- Collision Ghosting
            for _, part in pairs(Char:GetDescendants()) do 
                if part:IsA("BasePart") then part.CanCollide = false end 
            end

            -- LOOP 1: Server Defense
            task.spawn(function()
                while AntiGrabEnabled and BeingHeld.Value do
                    pcall(function()
                        StruggleEvent:FireServer()
                        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(Root, 0)
                        ReplicatedStorage.GameCorrectionEvents.StopAllVelocity:FireServer()
                    end)
                    task.wait()
                end
            end)

            -- LOOP 2: Physics State Enforcement
            task.spawn(function()
                while AntiGrabEnabled and BeingHeld.Value do
                    pcall(function()
                        Hum.Sit = false
                        Hum:ChangeState(Enum.HumanoidStateType.GettingUp)
                        Hum:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
                        if Root then
                            Root.Anchored = true
                            Root.AssemblyLinearVelocity = Vector3.zero
                            Root.AssemblyAngularVelocity = Vector3.zero
                        end
                    end)
                    task.wait()
                end
                if Root then Root.Anchored = false end
            end)
            
            -- TELEPORTATION REMOVED: 
            -- The RenderStepped connection and MoveDirection loops that 
            -- forced your CFrame back have been deleted.
        end)
    end

    -- UI Integration
    DefenseGroup:CreateToggle({
        Name = "Anti Grab V2 (anti perm die)",
        Flag = "Anti Grab V2 (Full Defense)",
        Default = false,
        Callback = function(Value)
            SetToggleState("Anti Grab V2 (Full Defense)", Value)
            AntiGrabEnabled = Value
            if Value then StartAntiGrab() else StopAntiGrab() end
        end
    })
    
    -- Character Watchdog
    Player.CharacterAdded:Connect(function(char)
        task.wait(1)
        if AntiGrabEnabled and BeingHeld.Value then StartAntiGrab() end
    end)
end

-- Anti Banana [SIT]
do
    local antibananaSit = false

    DefenseGroup:CreateToggle({
        Name = "Anti Banana [SIT]",
        Flag = "AntiBananaSit",
        Default = false,
        Callback = function(Value)
            antibananaSit = Value
            
            -- Running in a dedicated thread to ensure it doesn't yield the UI
            task.spawn(function()
                while antibananaSit do
                    local char = Player.Character
                    if char then
                        local hum = char:FindFirstChildOfClass("Humanoid")
                        local hrp = char:FindFirstChild("HumanoidRootPart")
                        if hum and hrp and hum.Health > 0 then 
                            hum.Sit = true
                            hum:ChangeState(Enum.HumanoidStateType.Running)
                            local Vec = Camera.CFrame.LookVector
                            hrp.CFrame = CFrame.new(hrp.Position, hrp.Position + Vector3.new(Vec.X, 0, Vec.Z))
                        end
                    end
                    task.wait()
                end
            end)
        end
    })
end

-- Anti Ragdoll (On Blob)
do
    local AntiRagBlob = false
    local RagdolledSit = false
    local Cons = {}

    local function ApplyAntiRagdoll(char)
        if not char or not AntiRagBlob then return end
        
        local hum = char:WaitForChild("Humanoid", 5)
        local HRP = char:WaitForChild("HumanoidRootPart", 5)
        if not (hum and HRP) then return end
        
        if Cons["ARSeat"] then Cons["ARSeat"]:Disconnect() end
        Cons["ARSeat"] = hum:GetPropertyChangedSignal("SeatPart"):Connect(function()
            if hum.SeatPart and hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman" and not RagdolledSit then
                RagdolledSit = true
                local Seat = hum.SeatPart
                while not hum.Sit do task.wait() end
                
                ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, 3)
                
                local ragdolledVal = hum:FindFirstChild("Ragdolled")
                while ragdolledVal and not ragdolledVal.Value and not hum.Sit do task.wait() end
                
                task.wait(0.4)
                hum.Sit = false
                Seat:Sit(hum)
                
                task.delay(0.25, function()
                    while hum and hum.SeatPart do
                        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, 1)
                        task.wait(0.05)
                    end
                    RagdolledSit = false
                end)
            end
        end)
    end

    DefenseGroup:CreateToggle({
        Name = "Anti Ragdoll (On Blob)",
        Flag = "AntiRagdoll",
        Default = false,
        Callback = function(Value)
            AntiRagBlob = Value
            RagdolledSit = false
            
            if Cons["ARChar"] then Cons["ARChar"]:Disconnect() end
            if Cons["ARSeat"] then Cons["ARSeat"]:Disconnect() end
            
            if AntiRagBlob then
                ApplyAntiRagdoll(Player.Character)
                Cons["ARChar"] = Player.CharacterAdded:Connect(ApplyAntiRagdoll)
            end
        end
    })
end

DefenseGroup:CreateToggle({
    Name = "anti snowball",
    Flag = "LoopRagdoll",
    Default = false,
    Callback = function(Value)
        SetToggleState("LoopRagdoll", Value)
        loopRagdoll = Value
        
        if Value then
            task.spawn(function()
                while loopRagdoll and task.wait(0.05) do
                    pcall(function()
                        local char = Player.Character
                        local hrp = char and char:FindFirstChild("HumanoidRootPart")
                        if hrp then
                            ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0.5)
                        end
                    end)
                end
            end)
        end
    end
})
antiblob = false
antiblobConnection = nil
truePosPart = nil

DefenseGroup:CreateToggle({
    Name = "Auto Reset",
        Flag = "Auto Reset",
    Default = false,
    Callback = function(v)
        SetToggleState("Auto Reset", Value)
        -- Clear old connection
        if _G.AutoResetCon then _G.AutoResetCon:Disconnect() end

        if v then
            _G.AutoResetCon = game:GetService("ReplicatedStorage").GameCorrectionEvents.GameCorrectionsNotify.OnClientEvent:Connect(function(r)
                if r == "Flying" then
                    local char = game:GetService("Players").LocalPlayer.Character
                    local hum = char and char:FindFirstChildOfClass("Humanoid")
                    
                    if hum then
                        Library:Notify("Resetting to prevent Ban", 4)
                        -- Break Joint/Health is more reliable for "Defense" than ChangeState
                        char:BreakJoints() 
                        hum.Health = 0
                    end
                end
            end)
        end
    end
})
DefenseGroup:CreateToggle({
    Name = "Auto Leave ",
    Flag = "Auto Leave",
    Default = false,
    Callback = function(v)
        SetToggleState("Auto Leave", v)
        
        -- Clear old connection to prevent memory leaks or duplicate firing
        if _G.AutoLeaveCon then _G.AutoLeaveCon:Disconnect() end

        if v then
            local warnTimestamps = {} -- Table to track when warnings happen

            _G.AutoLeaveCon = game:GetService("ReplicatedStorage").GameCorrectionEvents.GameCorrectionsNotify.OnClientEvent:Connect(function(r)
                if r == "Flying" then
                    local currentTime = os.clock()
                    table.insert(warnTimestamps, currentTime)

                    -- Clean up timestamps that are older than 1 second
                    for i = #warnTimestamps, 1, -1 do
                        if currentTime - warnTimestamps[i] > 1 then
                            table.remove(warnTimestamps, i)
                        end
                    end

                    -- If 3 or more warnings happened in the last second, auto-leave
                    if #warnTimestamps >= 3 then
                        game:GetService("Players").LocalPlayer:Kick("XOCU Safety: Disconnected to prevent ban.")
                    end
                end
            end)
        end
    end
})
DefenseGroup:CreateToggle({
    Name = "Anti Void",
        Flag = "Anti Void",
    Default = false,
    Callback = function(v)
        SetToggleState("Anti Void", Value)
        if v then
            workspace.FallenPartsDestroyHeight = 0/0
        else
            workspace.FallenPartsDestroyHeight = -100
        end
    end
})

antiblob = false

DefenseGroup:CreateToggle({
    Name = "Anti Blob",
    Flag = "AntiBlobKick",
    Default = false,
    Callback = function(Value)
        antiblob = Value
        local player = game:GetService("Players").LocalPlayer
        local replicatedStorage = game:GetService("ReplicatedStorage")
        local workspace = game:GetService("Workspace")

        if antiblob and player.Character then
            if not player.Character:FindFirstChild("TruePositionPart") then
                local truePosPart = Instance.new("Part")
                truePosPart.Parent = player.Character
                truePosPart.Name = "TruePositionPart"
                truePosPart.Anchored = true
                truePosPart.Transparency = 0.8
                truePosPart.CanCollide = false
                truePosPart.Size = Vector3.new(0.1, 0.1, 0.1)
                truePosPart.CFrame = CFrame.new(0, -10000000, 0)
            end
        end

        -- Placed inside task.spawn so the while loop doesn't yield/freeze your UI
        task.spawn(function()
            while antiblob and task.wait() do
                if player.Character then
                    local hrp = player.Character:FindFirstChild("HumanoidRootPart")
                    local truePosPart = player.Character:FindFirstChild("TruePositionPart")
                    
                    if hrp and truePosPart then
                        -- Reparent RootAttachment
                        local rootAttachment = hrp:FindFirstChild("RootAttachment")
                        if rootAttachment and rootAttachment.Parent == hrp then 
                            rootAttachment.Parent = truePosPart 
                        end
                        
                        local isGrabbed = false
                        
                        -- Check for massless parts (usually indicates you are grabbed)
                        for _, part in pairs(player.Character:GetChildren()) do
                            if part:IsA("Part") and part.Massless then
                                part.Massless = false
                                isGrabbed = true
                            end
                        end

                        -- If grabbed, apply velocity and fire remote events
                        if isGrabbed then
                            
                            -- [ADDED VELOCITY HERE]
                            -- Applies a massive upward force to break the blob's physics weld/grip.
                            -- You can change these numbers if it flings you too high or not enough.
                            hrp.AssemblyLinearVelocity = Vector3.new(0, 15000000, 0) 

                            -- Helper function to fire the drop remotes to avoid repeating code
                            local function fireDrop(item)
                                local blobScript = item:FindFirstChild("BlobmanSeatAndOwnerScript")
                                local rightDetector = item:FindFirstChild("RightDetector")
                                local leftDetector = item:FindFirstChild("LeftDetector")

                                if blobScript and rightDetector and leftDetector then
                                    local dropEvent = blobScript:FindFirstChild("CreatureDrop")
                                    local rightWeld = rightDetector:FindFirstChild("RightWeld")
                                    local leftWeld = leftDetector:FindFirstChild("LeftWeld")
                                    
                                    if dropEvent then
                                        if rightWeld then dropEvent:FireServer(rightWeld, hrp) end
                                        if leftWeld then dropEvent:FireServer(leftWeld, hrp) end
                                    end
                                    
                                    if replicatedStorage:FindFirstChild("CharacterEvents") then
                                        local struggleEvent = replicatedStorage.CharacterEvents:FindFirstChild("Struggle")
                                        if struggleEvent then struggleEvent:FireServer(player) end
                                    end
                                end
                            end

                            -- Check Plot Items
                            for _, plot in pairs(workspace.PlotItems:GetChildren()) do
                                if plot.Name ~= "PlayersInPlots" then
                                    for _, item in pairs(plot:GetChildren()) do
                                        if item.Name == "CreatureBlobman" then
                                            fireDrop(item)
                                        end
                                    end
                                end
                            end

                            -- Check Spawned Toys
                            for _, plr in pairs(game:GetService("Players"):GetPlayers()) do
                                local toyFolder = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                                if toyFolder then
                                    for _, item in pairs(toyFolder:GetChildren()) do
                                        if item.Name == "CreatureBlobman" then
                                            fireDrop(item)
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end
        end)

        -- Cleanup when turned off
        if not antiblob and player.Character then
            local hrp = player.Character:FindFirstChild("HumanoidRootPart")
            local truePosPart = player.Character:FindFirstChild("TruePositionPart")
            if hrp and truePosPart then
                local rootAttachment = truePosPart:FindFirstChild("RootAttachment")
                if rootAttachment then rootAttachment.Parent = hrp end
                truePosPart:Destroy()
            end
        end
    end,
})
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer

-- Fix 1: Pull helper functions OUTSIDE the callback to prevent memory leaks
local function OAA_getCharacter(player)
    return player.Character
end

local function OAA_getHumanoidRootPart(character)
    return character and character:FindFirstChild("HumanoidRootPart")
end

local function OAA_getHumanoid(character)
    return character and character:FindFirstChild("Humanoid")
end

local function OAA_getDistance(part1, part2)
    return (part1.Position - part2.Position).Magnitude
end

local function OAA_setNetworkOwner(part, cframe)
    task.spawn(function()
        -- Use FindFirstChild/WaitForChild safely
        local grabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
        if grabEvents then
            local setNetworkOwnerRemote = grabEvents:FindFirstChild("SetNetworkOwner")
            if setNetworkOwnerRemote then
                setNetworkOwnerRemote:FireServer(part, cframe)
            end
        end
    end)
end

-- Fix 2: Prevent DescendantAdded from stacking multiple connections
local antiBlob1T = false
local blobConnection = nil

local function antiBlob1F()
    antiBlob1T = true
    if not blobConnection then
        blobConnection = workspace.DescendantAdded:Connect(function(toy)
            if toy.Name == "CreatureBlobman" and antiBlob1T then
                -- Wait for child prevents errors if the detectors haven't loaded the exact microsecond the model spawns
                local leftDetector = toy:WaitForChild("LeftDetector", 3)
                local rightDetector = toy:WaitForChild("RightDetector", 3)
                
                if leftDetector then leftDetector:Destroy() end
                if rightDetector then rightDetector:Destroy() end
            end
        end)
    end
end

-- Fix 3: Use a connection variable to cleanly start/stop the Aura loop
local auraConnection = nil

DefenseGroup:CreateToggle({
    Name = "Anti-Blobman Aura",
    Flag = "Anti-Blobman Aura",
    Default = false,
    Callback = function(enabled)
        -- Fix 4: Changed 'Value' to 'enabled'
        if SetToggleState then
            SetToggleState("Anti-Blobman Aura", enabled)
        end

        if enabled then
            -- Clean up old loop just in case
            if auraConnection then auraConnection:Disconnect() end
            
            -- Use Heartbeat for smooth, constant checking without freezing the UI thread
            auraConnection = RunService.Heartbeat:Connect(function()
                local myCharacter = OAA_getCharacter(LocalPlayer)
                local myRootPart = OAA_getHumanoidRootPart(myCharacter)

                if not myRootPart then return end -- Skip if we are dead/respawning

                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer then
                        local playerCharacter = OAA_getCharacter(player)
                        local playerRootPart = OAA_getHumanoidRootPart(playerCharacter)
                        local playerHumanoid = OAA_getHumanoid(playerCharacter)

                        if playerRootPart and playerHumanoid and playerHumanoid.SeatPart then
                            local seatParent = playerHumanoid.SeatPart.Parent
                            
                            -- Check if riding Blobman and within range
                            if seatParent and seatParent.Name == "CreatureBlobman" then
                                if OAA_getDistance(playerRootPart, myRootPart) <= 19 then
                                    OAA_setNetworkOwner(playerRootPart, playerRootPart.CFrame)
                                end
                            end
                        end
                    end
                end
            end)
        else
            -- Disconnect the loop cleanly when toggled off
            if auraConnection then
                auraConnection:Disconnect()
                auraConnection = nil
            end
        end
    end,
})
local antiExplodeT = false
local function antiExplodeF()
	antiExplodeT = true
	local char = Player.Character
	if not char then
		return
	end
	local hrp = char:WaitForChild("HumanoidRootPart")
	workspace.ChildAdded:Connect(function(model)
		if model.Name == "Part" and antiExplodeT then
			local mag = (model.Position - hrp.Position).Magnitude
			if mag <= 20 then
				hrp.Anchored = true
				wait(0.01)
				while char["Right Arm"].RagdollLimbPart.CanCollide do
					wait(0.001)
				end
				hrp.Anchored = false
			end
		end
	end)
end
DefenseGroup:CreateToggle({
	Name = "Anti Explosion",
        Flag = "Anti Explosion", 
	Default = false,
	Callback = function(on)
        SetToggleState("Anti Explosion", on)
		if on then
			antiExplodeF()
		else
			antiExplodeT = false
		end
	end
})
local hookBurnConn
local function hookBurn(char)
	local hum = char:WaitForChild("Humanoid")
	local hrp = char:WaitForChild("HumanoidRootPart")
	char.PrimaryPart = hrp
	if hookBurnConn then
		hookBurnConn:Disconnect()
	end
	hookBurnConn = hum.FireDebounce.Changed:Connect(function(isBurning)
		if isBurning then
			local me = char
			local oldCF = hrp.CFrame
			local plots = workspace:FindFirstChild("Plots")
			if plots and plots:FindFirstChild("Plot2") then
				local plot2 = plots.Plot2
				local barrier = plot2:FindFirstChild("Barrier")
				local pb = barrier and barrier:FindFirstChild("PlotBarrier")
				if pb and pb:IsA("BasePart") then
					local safeCF = pb.CFrame * CFrame.new(0, 6, 0)
					me:SetPrimaryPartCFrame(safeCF)
					task.wait(0.3)
					local firePart = me:FindFirstChild("FirePlayerPart", true)
					if firePart then
						for _, obj in ipairs(firePart:GetChildren()) do
							if obj:IsA("Sound") then
								obj:Stop()
							end
							if obj:IsA("Light") or obj:IsA("ParticleEmitter") then
								obj.Enabled = false
							end
						end
						if firePart:FindFirstChild("CanBurn") then
							firePart.CanBurn.Value = false
						end
						if hum:FindFirstChild("FireDebounce") then
							hum.FireDebounce.Value = false
						end
					end
					task.wait(0.6)
					if me and me.PrimaryPart then
						me:SetPrimaryPartCFrame(oldCF)
					end
				end
			end
		end
	end)
end
DefenseGroup:CreateToggle({
	Name = "Anti Burn",
        Flag = "Anti Burn",
	Default = false,
	Callback = function(on)
        SetToggleState("Anti Burn", on)
		if on then
			hookBurn(Player.Character)
		elseif hookBurnConn then
			hookBurnConn:Disconnect()
		end
	end
})

local antiStickyT = false
DefenseGroup:CreateToggle({
	Name = "Anti Sticky",
        Flag = "Anti Sticky",
	Default = false,
	Callback = function(Value)
        SetToggleState("Anti Sticky", Value)
		antiStickyT = Value
		if Player.PlayerScripts:FindFirstChild("StickyPartsTouchDetection") then
			Player.PlayerScripts.StickyPartsTouchDetection.Disabled = Value
		end
	end,
})
local createGrabLineCopy, extendGrabLineCopy
local grabFolder = ReplicatedStorage:FindFirstChild("GrabEvents")
if grabFolder then
	local originalCreate = grabFolder:FindFirstChild("CreateGrabLine")
	local originalExtend = grabFolder:FindFirstChild("ExtendGrabLine")
	if originalCreate then
		createGrabLineCopy = originalCreate:Clone()
	end
	if originalExtend then
		extendGrabLineCopy = originalExtend:Clone()
	end
end
local plr = game:GetService("Players").LocalPlayer
local RunService = game:GetService("RunService")
if not _G.cons then _G.cons = {} end 

DefenseGroup:CreateToggle({
    Name = "Anti Loop Kill",
        Flag = "Anti Loop Kill",
    Default = false,
    Callback = function(v)
        SetToggleState("Anti Loop Kill", Value)
        if _G.cons["antiloopkill"] then 
            _G.cons["antiloopkill"]:Disconnect() 
            _G.cons["antiloopkill"] = nil 
        end

        if v then
            _G.cons["antiloopkill"] = plr.CharacterAdded:Connect(function(char)
                local hrp = char:WaitForChild("HumanoidRootPart", 5)
                if hrp then
                    -- Р–РґРµРј Р±СѓРєРІР°Р»СЊРЅРѕ РѕРґРёРЅ РєР°РґСЂ РїРµСЂРµРґ С‚РµР»РµРїРѕСЂС‚РѕРј
                    RunService.RenderStepped:Wait()
                    
                    local target = CFrame.new(524.703979, 93.7120056, -375.040985)
                    hrp.CFrame = target
                    
                    -- Р‘С‹СЃС‚СЂР°СЏ РїРѕРґСЃС‚СЂР°С…РѕРІРєР° РЅР° СЃР»РµРґСѓСЋС‰РёРµ 2 РєР°РґСЂР°
                    for i = 1, 2 do
                        RunService.RenderStepped:Wait()
                        hrp.CFrame = target
                    end
                end
            end)
        end
    end
})

DefenseGroup:CreateToggle({
	Name = "Anti Lag",
        Flag = "Anti Lag",
	Default = false,
	Callback = function(Value)
        SetToggleState("Anti Lag", Value)
		if Value then
			local grabFolder = ReplicatedStorage:FindFirstChild("GrabEvents")
            -- ... (original deletion logic) ...
		else
			-- ... (original restoration logic) ...
		end
	end,
})

local PCLD_Auto_Active = false

DefenseGroup:CreateToggle({
    Name = "Auto PCLD Break",
    Flag = "AutoPCLDBreakSmart",
    Default = false,
    Callback = function(Value)
        SetToggleState("AutoPCLDBreakSmart", Value)
        PCLD_Auto_Active = Value
        
        if Value then
            task.spawn(function()
                local plr = game:GetService("Players").LocalPlayer
                local isFirstCycle = true
                
                while PCLD_Auto_Active do
                    -- 1. Get current character and wait if it's dead
                    local char = plr.Character or plr.CharacterAdded:Wait()
                    local hrp = char:WaitForChild("HumanoidRootPart", 5)
                    local hum = char:WaitForChild("Humanoid", 5)
                    
                    if not hrp or not hum then 
                        task.wait(0.5)
                        continue 
                    end

                    if hum.Health <= 0 then
                        char = plr.CharacterAdded:Wait()
                        hrp = char:WaitForChild("HumanoidRootPart", 5)
                        hum = char:WaitForChild("Humanoid", 5)
                    end
                    
                    if not hrp or not hum then continue end

                    if isFirstCycle then
                        -- ==========================================
                        -- INITIAL BREAK: Teleport High & Double Kill
                        -- ==========================================
                        local savedCFrame = hrp.CFrame
                        
                        -- Kill 1: Teleport high
                        hrp.CFrame = CFrame.new(hrp.Position.X, 50000, hrp.Position.Z)
                        task.wait(0.05)
                        hum.Health = 0

                        -- Wait for respawn
                        char = plr.CharacterAdded:Wait()
                        if not PCLD_Auto_Active then break end
                        
                        hrp = char:WaitForChild("HumanoidRootPart", 5)
                        hum = char:WaitForChild("Humanoid", 5)
                        
                        -- Kill 2: Teleport back
                        if hrp and hum then
                            task.wait(0.1)
                            hrp.CFrame = savedCFrame
                            task.wait(0.05)
                            hum.Health = 0
                        end
                        
                        isFirstCycle = false
                    else
                        -- ==========================================
                        -- NEXT TIMES: Kill 1 time on respawn
                        -- ==========================================
                        task.wait(0.1) -- Small buffer so the spawn fully registers
                        if hum then
                            hum.Health = 0
                            pcall(function() char:BreakJoints() end)
                        end
                    end

                    -- ==========================================
                    -- WAIT FOR PLAYABLE RESPAWN & NATURAL DEATH
                    -- ==========================================
                    char = plr.CharacterAdded:Wait()
                    if not PCLD_Auto_Active then break end
                    
                    hum = char:WaitForChild("Humanoid", 5)

                    if hum then
                        -- Pauses the loop here while you play. 
                        -- Once you die naturally, the loop restarts and executes the 1-time kill.
                        hum.Died:Wait()
                    end
                end
            end)
        end
    end
})
local AutoDeleteLegsActive = false
local DeleteLegsConnection = nil

local function PerformLegDeletion(char)
    -- Wait a brief moment to ensure the character is fully loaded
    task.wait(0.5) 
    
    if not AutoDeleteLegsActive then return end
    
    local hrp = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChild("Humanoid")
    local torso = char:FindFirstChild("Torso")
    local ll = char:FindFirstChild("Left Leg")
    local rl = char:FindFirstChild("Right Leg")
    
    if hrp and hum and torso and ll and rl then
        local void = workspace.FallenPartsDestroyHeight
        local pos = torso.CFrame
        
        workspace.FallenPartsDestroyHeight = -100
        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 2)
        task.wait(0.5)
        
        if ll and rl then
            rl.CFrame = CFrame.new(0, -10000, 0)
            ll.CFrame = CFrame.new(0, -10000, 0)
        end
        
        task.wait(0.3)
        if torso then torso.CFrame = CFrame.new(0, -9970, 0) end
        
        task.wait(0.5)
        if torso then torso.CFrame = pos end
        
        task.wait(0.5)
        workspace.FallenPartsDestroyHeight = void
        
        -- Safe HipHeight adjustment loop
        task.spawn(function()
            while AutoDeleteLegsActive and char.Parent and hum.Health > 0 and not char:FindFirstChild("Left Leg") and not char:FindFirstChild("Right Leg") do
                pcall(function()
                    local controls = Player.PlayerGui:FindFirstChild("ControlsGui")
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
        end)
    end
end

DefenseGroup:CreateToggle({
    Name = "Auto Delete Legs",
    Flag = "Auto Delete Legs",
    Default = false,
    Callback = function(Value)
        SetToggleState("Auto Delete Legs", Value)
        AutoDeleteLegsActive = Value
        
        if Value then
            -- 1. Execute on the current character
            task.spawn(function()
                PerformLegDeletion(Player.Character)
            end)
            
            -- 2. Execute automatically upon respawn
            if not DeleteLegsConnection then
                DeleteLegsConnection = Player.CharacterAdded:Connect(function(newChar)
                    if AutoDeleteLegsActive then
                        task.spawn(function()
                            PerformLegDeletion(newChar)
                        end)
                    end
                end)
            end
        else
            -- Clean up the connection if toggled off
            if DeleteLegsConnection then
                DeleteLegsConnection:Disconnect()
                DeleteLegsConnection = nil
            end
            
            -- Reverting requires resetting to get your legs back
            local char = Player.Character
            local hum = char and char:FindFirstChild("Humanoid")
            if hum and hum.Health > 0 and (not char:FindFirstChild("Left Leg") or not char:FindFirstChild("Right Leg")) then
                Library:Notify({ Title = "System", Content = "Legs cannot be restored locally. Reset your character.", Duration = 4 })
            end
        end
    end
})
DefenseExtra:CreateToggle({
	Name = "Anti Paint",
        Flag = "Anti Paint",
	Default = false,
	Callback = function(state)
        SetToggleState("Anti Paint", state)
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
-- =========================================================================
-- PLATFORM TP (Adapted for Script 6)
-- =========================================================================
local platformTPToggle = false
local platformTPActive = false
local platformPart = nil
local oldPlatformPos = nil

local function SetupPlatform()
    if not platformPart then
        platformPart = Instance.new("Part", workspace)
        platformPart.Name = "SkyBase"
        platformPart.Anchored = true
        platformPart.Size = Vector3.new(1500, 2, 1500)
        platformPart.CFrame = CFrame.new(0, 1000000, 0)
        workspace.FallenPartsDestroyHeight = -9999999
    end
end

DefenseExtra:CreateToggle({
    Name = "Enable Platform TP",
    Flag = "PlatformTPToggle",
    Default = false,
    Callback = function(Value)
        SetToggleState("PlatformTPToggle", Value)
        platformTPToggle = Value
        if Value then
            SetupPlatform()
        else
            platformTPActive = false
        end
    end
})

DefenseExtra:CreateKeybind({
    Name = "Platform TP Execute",
    Flag = "PlatformTPKey",
    Default = "X",
    Callback = function()
        if not platformTPToggle then return end
        
        local char = Player.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not root then return end

        platformTPActive = not platformTPActive

        if platformTPActive then
            oldPlatformPos = root.CFrame
            root.CFrame = platformPart.CFrame + Vector3.new(0, 5, 0)
            root.AssemblyLinearVelocity = Vector3.zero
            root.AssemblyAngularVelocity = Vector3.zero
        else
            if oldPlatformPos then
                root.CFrame = oldPlatformPos
            end
        end
    end
})
-- =========================================================================
-- GUCCI BINDER (Adapted for Script 6)
-- =========================================================================
local gucciRunId = 0

local function FWC(Parent, Name, Time) 
    return Parent:FindFirstChild(Name) or Parent:WaitForChild(Name, Time or 3) 
end

local function grab(prt) 
    RS.GrabEvents.SetNetworkOwner:FireServer(prt, prt.CFrame) 
end

local function toy_spawn_gucci(name, cframe, vector)
    local ToySpawn = RS.MenuToys.SpawnToyRemoteFunction
    local InPlot, InOwnerPlot, CanSpawn = Player.InPlot, Player.InOwnedPlot, Player.CanSpawnToy

    while InPlot.Value and not InOwnerPlot.Value and not CanSpawn.Value do
        task.wait(0.01)
    end

    task.spawn(function()
        ToySpawn:InvokeServer(name, cframe, vector or Vector3.new())
    end)
    
    local BackPack = workspace:FindFirstChild(Player.Name .. 'SpawnedInToys')
    local SpawnedToy
    BackPack.ChildAdded:Once(function(toy)
        if toy.Name == name and toy:IsA("Model") then
            SpawnedToy = toy
        end
    end)
    
    local time = tick()
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
    gucciRunId = gucciRunId + 1
    local MyId = gucciRunId
    
    local char = Player.Character or Player.CharacterAdded:Wait()
    local hum = FWC(char, "Humanoid")
    
    hum.Sit = true
    task.wait(0.02)
    hum.Sit = false
    task.wait(0.02)
    
    task.spawn(function()
        local time = tick()
        while tick()-time < 0.8 do
            for _,v in pairs(char:GetChildren()) do
                if v:IsA('BasePart') then
                    v.Velocity = Vector3.new()
                end
            end
            task.wait(0.01)
        end
    end)
    
    local autoGucciT, sitJumpT, Blob, BHead = true, false, nil, nil
    
    task.spawn(function()
        while not Blob and MyId == gucciRunId do
            task.wait(0.01)
        end
        if MyId ~= gucciRunId then return end
        
        BHead = FWC(Blob, "Head")
        local HitBox = FWC(Blob, "GrabbableHitbox")
        
        while MyId == gucciRunId and BHead and
        (not BHead:FindFirstChild("PartOwner") or BHead.PartOwner.Value ~= Player.Name) do
            grab(HitBox)
            task.wait(0.01)
        end
    end)
    
    local hrp = FWC(char, "HumanoidRootPart")
    Blob = toy_spawn_gucci(
        "CreatureBlobman",
        hrp.CFrame * CFrame.new(0, 0, -5),
        Vector3.new(0, -15.716, 0)
    )
    
    if not Blob then return end
    
    local Seat = FWC(Blob, "VehicleSeat")
    
    task.defer(function()
        if not(char or hum) then return end
        
        local startTime = tick()
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
                RS.CharacterEvents.RagdollRemote:FireServer(hrp, 0.095)
            end
            task.wait(0.01)
        end
    end)
    
    local index
    task.wait(0.4)
    if MyId ~= gucciRunId then return end
    
    hum.Sit = false
    Blob.Name = "Gucci"
    
    local BackPack = workspace:FindFirstChild(Player.Name .. 'SpawnedInToys')
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
    
    local success, contents = pcall(function()
        return Player.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents
    end)
    
    if success and contents and index then
        for i,v in ipairs(contents:GetChildren()) do
            if v.Name == "CreatureBlobman" and i == index then
                local view = v.ViewItemButton
                view.Text = "GUCCI"
                view.TextScaled = true
                view.LowResImage.Image = ""
            end
        end
    end
end

DefenseExtra:CreateKeybind({
    Name = "Gucci Binder",
    Flag = "GucciBinderKey",
    Default = "J",
    Callback = function()
        GucciAntiGrab()
    end
})

do
    -- Services
    local Players = game:GetService("Players")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local RunService = game:GetService("RunService")
    local Workspace = game:GetService("Workspace")

    local LocalPlayer = Players.LocalPlayer

    -- State Configuration
    local Type1 = {
        systemOn = false,
        autoRespawnEnabled = false,
        currentSeat = nil,
        ragdollConnection = nil,
        sitConnection = nil,
        characterAddedConnection = nil, -- Tracks the character respawn listener
        Remotes = { ragdoll = nil, spawn = nil, destroy = nil }
    }

    -- Cache Remote Events & Functions
    local function Type1_CacheRemotes()
        if not Type1.Remotes.ragdoll then
            local charEvents = ReplicatedStorage:WaitForChild("CharacterEvents", 5)
            if charEvents then
                Type1.Remotes.ragdoll = charEvents:FindFirstChild("RagdollRemote")
            end
        end
        if not Type1.Remotes.spawn or not Type1.Remotes.destroy then
            local menuToys = ReplicatedStorage:WaitForChild("MenuToys", 5)
            if menuToys then
                Type1.Remotes.spawn = menuToys:WaitForChild("SpawnToyRemoteFunction", 5)
                Type1.Remotes.destroy = menuToys:FindFirstChild("DestroyToy")
            end
        end
    end

    -- Ragdoll Fire Event Loop
    local function Type1_StartRagdollSpam(rootPart)
        if Type1.ragdollConnection then Type1.ragdollConnection:Disconnect() end
        if not Type1.Remotes.ragdoll then return end
        
        Type1.ragdollConnection = RunService.Heartbeat:Connect(function()
            if not Type1.systemOn or not rootPart then return end
            pcall(function() Type1.Remotes.ragdoll:FireServer(rootPart, 2) end)
        end)
    end

    -- Seat Placement Loop
    local function Type1_StartSitSpam(humanoid, seat)
        if Type1.sitConnection then Type1.sitConnection:Disconnect() end
        
        Type1.sitConnection = RunService.Heartbeat:Connect(function()
            if not Type1.systemOn or not seat or not seat.Parent then return end
            seat:Sit(humanoid)
        end)
    end

    -- Clean Legacy Spawned Blobmen
    local function Type1_CleanUpOldToys()
        if not Type1.Remotes.destroy then return end
        local folder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
        if not folder then return end
        for _, toy in pairs(folder:GetChildren()) do
            if toy.Name == "CreatureBlobman" then
                pcall(function() Type1.Remotes.destroy:FireServer(toy) end)
            end
        end
    end

    -- Main Gucci Engine Sequence
    local function Type1_RunStableGucci()
        if Type1.systemOn then return end
        
        local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local hum = char:WaitForChild("Humanoid", 3)
        local root = char:WaitForChild("HumanoidRootPart", 3)
        
        if not (hum and root) then 
            Type1.systemOn = false
            return 
        end
        
        Type1.systemOn = true
        Type1_CleanUpOldToys()
        
        local savedCFrame = root.CFrame
        
        local spawnCFrame = CFrame.new(103.85, -7.45, -538.58)
        task.spawn(function()
            if Type1.Remotes.spawn then
                Type1.Remotes.spawn:InvokeServer("CreatureBlobman", spawnCFrame, Vector3.zero)
            end
        end)
        
        local blobman = nil
        local folder = Workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys", 5)
        if not folder then
            Type1.systemOn = false
            return
        end
        
        local startTick = tick()
        while not blobman and tick() - startTick < 3 do
            local target = folder:FindFirstChild("CreatureBlobman")
            if target then blobman = target break end
            RunService.Heartbeat:Wait()
        end
        
        if not blobman then
            Type1.systemOn = false
            return
        end
        
        local head = blobman:FindFirstChild("Head")
        if head then head.Anchored = true end
        
        local seat = blobman:WaitForChild("VehicleSeat", 2)
        if not seat then Type1.systemOn = false return end
        Type1.currentSeat = seat
        
        Type1_StartRagdollSpam(root)
        
        root.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
        seat:Sit(hum)
        
        Type1_StartSitSpam(hum, seat)
        
        task.wait(0.6)
        
        hum.Sit = false
        hum.PlatformStand = true
        
        if seat then 
            seat.Disabled = true 
            pcall(function() seat:Destroy() end)
        end
        
        if blobman then
            for _, v in pairs(blobman:GetDescendants()) do
                if v:IsA("Weld") or v:IsA("Snap") then v:Destroy() end
            end
        end
        
        root.CFrame = savedCFrame
        root.Velocity = Vector3.zero
        hum.PlatformStand = false
        
        task.wait(0.5)
        
        if Type1.sitConnection then Type1.sitConnection:Disconnect() Type1.sitConnection = nil end
        if Type1.ragdollConnection then Type1.ragdollConnection:Disconnect() Type1.ragdollConnection = nil end
        
        task.wait(0.5)
        
        Type1.systemOn = false
        
        Library:Notify({ Title = "Anti Gucci", Description = "Blob Man Gucci (Automatic) 완료!", Time = 2 })
    end

    -- Stop System Execution
    local function Type1_StopSystem()
        Type1.systemOn = false
        Type1.autoRespawnEnabled = false
        Type1.currentSeat = nil
        if Type1.ragdollConnection then Type1.ragdollConnection:Disconnect() Type1.ragdollConnection = nil end
        if Type1.sitConnection then Type1.sitConnection:Disconnect() Type1.sitConnection = nil end
    end

    -- Connect Character Death Handler
    local function Type1_ConnectDeathEvent(char)
        local hum = char:WaitForChild("Humanoid", 5)
        if not hum then return end
        hum.Died:Connect(function()
            if Type1.autoRespawnEnabled then
                Type1.systemOn = false
                Type1_CleanUpOldToys()
            end
        end)
    end

    -- Initialize Remote Caching
    Type1_CacheRemotes()

    -- Create Toggle Interface
    DefenseExtra:CreateToggle({
        Name = "Anti Gucci (Blobman)",
        Flag = "Anti Gucci (Blobman)",
        Default = false,
        Callback = function(Value)
            if Value then
                -- System Active
                Type1.autoRespawnEnabled = true
                
                if LocalPlayer.Character then
                    Type1_ConnectDeathEvent(LocalPlayer.Character)
                end
                
                task.spawn(Type1_RunStableGucci)
                
                -- Setup persistent looping upon respawning
                if not Type1.characterAddedConnection then
                    Type1.characterAddedConnection = LocalPlayer.CharacterAdded:Connect(function(newChar)
                        if Type1.autoRespawnEnabled then
                            Type1_ConnectDeathEvent(newChar)
                            task.wait(1) 
                            task.spawn(Type1_RunStableGucci)
                        end
                    end)
                end
            else
                -- System Deactivated
                Type1_StopSystem()
                Type1_CleanUpOldToys()
                
                if Type1.characterAddedConnection then
                    Type1.characterAddedConnection:Disconnect()
                    Type1.characterAddedConnection = nil
                end
            end
        end
    })
end
DefenseExtra:CreateToggle({
    Name = "Gucci(Invisible)",
        Flag = "Gucci(Invisible)",
    Default = false,
    Callback = function(v)
        SetToggleState("Gucci(Invisible)", Value)
        if v then
            local blobb
			local plr = game.Players.LocalPlayer
			local HRP = plr.Character:WaitForChild("HumanoidRootPart")
            pcall(function()
                local pal, pal2
                pal2 = plr.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents.ChildAdded:Connect(function(c)
                    if c.Name == "TractorGreen" then
                        pal = c
                        task.wait()
                        pal2:Disconnect()
                        pal2 = nil
                    end
                end)
                spawn(function()
                    task.wait(1)
					if pal and pal:FindFirstChild("ViewItemButton") then
						local mess = pal.ViewItemButton:FindFirstChild("NewMessage")
						if mess then
							local clonedMess = mess:Clone()
							clonedMess.Name = "Gucci2"
							clonedMess.TextColor3 = Color3.fromRGB(255, 255, 255)
							clonedMess.Name = "Anti Gucci"
							clonedMess.Visible = true
							clonedMess.Parent = pal.ViewItemButton
						end
					end
                end)
            end)
            local RS_gucci = game:GetService("ReplicatedStorage")
            local RunService_gucci = game:GetService("RunService")
            -- Save player's real position before anything
            local savedGucciCF = HRP.CFrame
            -- Spawn tractor in sky
            RS_gucci.MenuToys.SpawnToyRemoteFunction:InvokeServer("TractorGreen", CFrame.new(0, 50000, 0), Vector3.new())
            local inv = workspace:WaitForChild(plr.Name .. "SpawnedInToys")
			blobb = inv:WaitForChild("TractorGreen", 3)
			if blobb then
				blobb.Name = "tractorgucci"
				local humanoid = plr.Character:WaitForChild("Humanoid")
				local seat = blobb:WaitForChild("VehicleSeat", 3)
				if seat then
					-- Teleport player to seat in sky to register sit
					seat.CFrame = CFrame.new(0, 50000, 0)
					HRP.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
					task.wait(0.05)
					seat:Sit(humanoid)
					-- Fire RagdollRemote a few times to register sit
					for _ = 1, 10 do
						RS_gucci.CharacterEvents.RagdollRemote:FireServer(HRP, 0)
						task.wait()
					end
					-- Wait until seated
					local t0 = tick()
					while seat.Occupant ~= humanoid and tick() - t0 < 3 do
						HRP.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
						seat:Sit(humanoid)
						task.wait()
					end
					-- Teleport player back to real position now that sit is registered
					HRP.CFrame = savedGucciCF
					-- Continuous Heartbeat loop: keep seat in sky, keep player at real position
					if getgenv().gucciInvisConn then getgenv().gucciInvisConn:Disconnect() end
					getgenv().gucciInvisConn = RunService_gucci.Heartbeat:Connect(function()
						if not HRP or not HRP.Parent then return end
						RS_gucci.CharacterEvents.RagdollRemote:FireServer(HRP, 0)
						-- Keep seat anchored in sky (invisible)
						if seat and seat.Parent then
							seat.CFrame = CFrame.new(0, 50000, 0)
						end
						-- Lock player back to real position so they don't drift to sky
						if humanoid and humanoid.Sit then
							HRP.CFrame = savedGucciCF
						end
					end)
				end
			end
        else
			-- Stop the Heartbeat loop first
			if getgenv().gucciInvisConn then
				getgenv().gucciInvisConn:Disconnect()
				getgenv().gucciInvisConn = nil
			end
			local plr = game.Players.LocalPlayer
			local RS = game:GetService("ReplicatedStorage")
			local DestroyToy = RS.MenuToys.DestroyToy

			-- Step 1: Force-unseat the humanoid FIRST so server allows destroy
			local hum = plr.Character and plr.Character:FindFirstChild("Humanoid")
			if hum then
				hum.Sit = false
				hum:ChangeState(Enum.HumanoidStateType.GettingUp)
				task.wait(0.1)
			end

			-- Step 2: Destroy by known renamed name
			local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
			if inv then
				local toy = inv:FindFirstChild("tractorgucci") or inv:FindFirstChild("TractorGreen")
				if toy then
					pcall(function() DestroyToy:FireServer(toy) end)
					task.wait(0.1)
					-- If still exists, fire again
					if toy and toy.Parent then
						pcall(function() DestroyToy:FireServer(toy) end)
					end
					task.wait(0.05)
					-- Last resort: destroy locally
					if toy and toy.Parent then
						pcall(function() toy:Destroy() end)
					end
				end
			end

			-- Step 3: Final unsit cleanup in case still seated
			if hum then
				for i = 1, 10 do
					hum.Sit = false
					task.wait()
				end
			end
        end
    end
})
do
    local autoGucciEnabled = false
    local autoGucciConn = nil
    local autoGucciSpamTask = nil
    local autoGucciDestroyConn = nil

    local function cleanupGucciTasks()
        if autoGucciSpamTask then
            task.cancel(autoGucciSpamTask)
            autoGucciSpamTask = nil
        end
        if autoGucciDestroyConn then
            autoGucciDestroyConn:Disconnect()
            autoGucciDestroyConn = nil
        end
    end

    -- Native toy spawner replacing the missing 'SpawnToy' function
    local function SpawnGucciToy(toyName, hrp)
        local inv = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
        if not inv then return nil end
        
        local spawnCF = hrp.CFrame * CFrame.new(0, 14, 20)
        task.spawn(function()
            pcall(function()
                RS.MenuToys.SpawnToyRemoteFunction:InvokeServer(toyName, spawnCF, Vector3.zero)
            end)
        end)

        local t = tick()
        local spawnedToy = nil
        repeat
            task.wait(0.1)
            spawnedToy = inv:FindFirstChild(toyName)
        until spawnedToy or (tick() - t > 3)
        
        return spawnedToy
    end

    DefenseExtra:CreateToggle({
        Name = "auto Gucci(Invisible)",
        Flag = "AutoGucci",
        Default = false,
        Callback = function(Value)
            SetToggleState("AutoGucci", Value)
            autoGucciEnabled = Value
            
            if Value then
                local GucciThing = nil
                local isActive = false
                
                local function gucci()
                    if not autoGucciEnabled then return end
                    isActive = true
                    
                    local char = Player.Character
                    if not char then isActive = false return end
                    
                    local hrp = char:FindFirstChild("HumanoidRootPart")
                    local hum = char:FindFirstChild("Humanoid")
                    local head = char:FindFirstChild("Head")
                    local inv = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
                    
                    if not (hrp and hum and head and inv) then isActive = false return end

                    cleanupGucciTasks()
                    
                    -- Clear out any existing instances to prevent clutter
                    for _, v in pairs(inv:GetChildren()) do
                        if v.Name == "AutoGucci" or v.Name == "TractorGreen" then
                            pcall(function() RS.MenuToys.DestroyToy:FireServer(v) end)
                        end
                    end
                    
                    -- Wait for ragdoll or being held to finish
                    local ragdolled = hum:FindFirstChild("Ragdolled")
                    while (ragdolled and ragdolled.Value) or BeingHeld.Value do 
                        task.wait() 
                    end
                    
                    -- Force unsit
                    for _ = 1, 100 do hum.Sit = true end
                    task.wait(0.1)
                    hum.Sit = false
                    
                    -- Spawn the tractor
                    GucciThing = SpawnGucciToy("TractorGreen", hrp)
                    while not GucciThing and autoGucciEnabled do
                        task.wait(0.25)
                        GucciThing = SpawnGucciToy("TractorGreen", hrp)
                    end
                    
                    if not GucciThing then isActive = false return end
                    GucciThing.Name = "AutoGucci"
                    
                    local seat = GucciThing:WaitForChild("VehicleSeat", 3)
                    if not seat then isActive = false return end
                    
                    -- Anti-grab spam loop
                    autoGucciSpamTask = task.spawn(function()
                        local endTime = tick() + 0.5
                        while tick() < endTime and task.wait() and isActive do
                            pcall(function() RS.CharacterEvents.RagdollRemote:FireServer(hrp, 0) end)
                        end
                    end)
                    
                    -- Ensure the player sits on it
                    local lastSitAttempt = 0
                    while not (hum.SeatPart or BeingHeld.Value) and autoGucciEnabled do
                        if tick() - lastSitAttempt > 0.1 then
                            seat:Sit(hum)
                            lastSitAttempt = tick()
                        end
                        task.wait()
                    end
                    
                    hum.Sit = false
                    hum:ChangeState(Enum.HumanoidStateType.Jumping)
                    hrp.Anchored = true
                    
                    -- Banish the toy to the shadow realm (skybox)
                    task.spawn(function()
                        repeat task.wait() until not seat:FindFirstChild("SeatWeld")
                        GucciThing:PivotTo(CFrame.new(0, 1e6, 0))
                        local bodyPos = Instance.new("BodyPosition")
                        bodyPos.Position = Vector3.new(0, 1e6, 0)
                        bodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                        bodyPos.Parent = GucciThing.PrimaryPart
                    end)
                    
                    hrp.Anchored = false
                    autoGucciDestroyConn = GucciThing.Destroying:Once(function()
                        if autoGucciEnabled then gucci() end
                    end)
                    
                    isActive = false
                end
                
                -- Initial execution
                gucci()
                
                -- Constant watchdog loop
                if autoGucciConn then autoGucciConn:Disconnect() end
                autoGucciConn = RunService.Heartbeat:Connect(function()
                    if not autoGucciEnabled then 
                        if autoGucciConn then autoGucciConn:Disconnect() end
                        return 
                    end
                    if isActive then return end
                    
                    local char = Player.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    local hum = char and char:FindFirstChild("Humanoid")
                    
                    -- Respawn handler
                    if not (hrp and hum) or hum.Health <= 0 then
                        isActive = true
                        local newChar = Player.CharacterAdded:Wait()
                        task.wait(0.5)
                        gucci()
                        return
                    end
                    
                    -- Failsafe: Re-execute if network ownership is stolen, grabbed, or sat anomalously
                    local isNetworkOwner = true
                    pcall(function() isNetworkOwner = isnetworkowner(hrp) end)
                    
                    if (not hrp.Anchored and not isNetworkOwner) or BeingHeld.Value or hum.Sit then
                        gucci()
                    end
                end)
                
            else
                -- Complete cleanup when toggled off
                cleanupGucciTasks()
                
                if autoGucciConn then
                    autoGucciConn:Disconnect()
                    autoGucciConn = nil
                end
                
                local inv = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
                if inv then
                    for _, v in pairs(inv:GetChildren()) do
                        if v.Name == "AutoGucci" or v.Name == "TractorGreen" then
                            pcall(function() RS.MenuToys.DestroyToy:FireServer(v) end)
                        end
                    end
                end
            end
        end
    })
end
local autoGucciActiveTrain =  false
DefenseExtra:CreateToggle({
	Name = "Anti Gucci (Train)",
        Flag = "Anti Gucci (Train)",
	Default = false,
	Callback = function(Value)
        SetToggleState("Anti Gucci (Train)", Value)
		autoGucciActiveTrain = Value
		if Value then
			startAntiGucciTrain()
			notify("system", "Gucci active (monitoring)", 3)
			task.spawn(function()
				while autoGucciActiveTrain do
					local trainFolder = workspace.Map.AlwaysHereTweenedObjects
					local trainExists = trainFolder and trainFolder:FindFirstChild("Train")
					if not trainExists then
						stopAntiGucciTrain()
						notify("System", "Train lost", 3)
						local retries = 0
						repeat
							task.wait(0.2)
							retries = retries + 1
							trainFolder = workspace.Map.AlwaysHereTweenedObjects
						until (trainFolder and trainFolder:FindFirstChild("Train")) or retries > 25 or not autoGucciActiveTrain
						if autoGucciActiveTrain and trainFolder and trainFolder:FindFirstChild("Train") then
							startAntiGucciTrain()
							notify("System", "Train restored.", 3)
						end
					end
					task.wait(0.5)
				end
			end)
		else
			autoGucciActiveTrain = false
			stopAntiGucciTrain()
			notify("System", "Gucci disabled.", 3)
		end
	end
})
DefenseExtra:CreateButton({
    Name = "Delete Legs",
        Flag = "Delete Legs",
    Callback = function()
        local char = Player.Character
        if not char then return end
        if char:FindFirstChild("Left Leg") and char:FindFirstChild("Right Leg") then
            local ll = char:FindFirstChild("Left Leg")
            local rl = char:FindFirstChild("Right Leg")
            local void = workspace.FallenPartsDestroyHeight
            local pos = char.Torso.CFrame
            workspace.FallenPartsDestroyHeight = -100
            ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(char.HumanoidRootPart, 2)
            task.wait(0.5)
            rl.CFrame = CFrame.new(0, -10000, 0)
            ll.CFrame = CFrame.new(0, -10000, 0)
            task.wait(0.3)
            char.Torso.CFrame = CFrame.new(0, -9970, 0)
            task.wait(0.5)
            char.Torso.CFrame = pos
            task.wait(0.5)
            workspace.FallenPartsDestroyHeight = void
            task.spawn(function()
                if not char:FindFirstChild("Left Leg") and not char:FindFirstChild("Right Leg") then
                    while task.wait() do
                        if Player.PlayerGui.ControlsGui.PCFrame.Stand.Visible == false then
                            char.Humanoid.HipHeight = 2
                        else
                            char.Humanoid.HipHeight = 0
                        end
                    end
                end
            end)
        end
    end
})
-- =========================================================================
-- INPUT LAG SETTINGS
-- =========================================================================
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

-- Auto-selects "Burger" by default ("FoodHamburger")
local SelectedToy = ToyList["Burger"] or ToyList[DropdownValues[1]]

DefenseExtra:CreateDropdown({
    Name = "Select Input Lag Toy",
    Flag = "InputLagToyDropdown",
    Items = DropdownValues,
    Default = "Burger", -- Auto selection set to Burger
    Callback = function(Value)
        SelectedToy = ToyList[Value]
    end
})

DefenseExtra:CreateToggle({
    Name = "Anti-Input",
    Flag = "InstantInputLag",
    Default = false,
    Callback = function(Value)
        SetToggleState("InstantInputLag", Value)
        _G.InstantLagActive = Value

        if Value then
            task.spawn(function()
                local plr = game.Players.LocalPlayer
                local RS = game:GetService("ReplicatedStorage")
                local SpawnRemote = RS:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")

                local HoldDuration = 0.02
                local CycleSpeed = 0.02

                while _G.InstantLagActive do
                    local char = plr.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    
                    if hrp then
                        local toysFolder = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                        local name = SelectedToy 
                        local item = toysFolder and toysFolder:FindFirstChild(name)

                        -- Safe Shuriken custom collisions handler
                        for _, obj in pairs(workspace:GetChildren()) do
                            if obj.Name == "Shuriken" and obj:IsA("Model") then
                                for _, part in pairs(obj:GetDescendants()) do
                                    if part:IsA("BasePart") then 
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

                                task.wait(HoldDuration) 

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
                    task.wait(CycleSpeed) 
                end
            end)
        end
    end
})
do
    -- Services & Local Variables
    local Players = game:GetService("Players")
    local RS = game:GetService("ReplicatedStorage")
    local RunService = game:GetService("RunService")
    local plr = Players.LocalPlayer

    -- State
    local AntiKickItemActive = false
    local MyPCLD = nil
    local pcldConn = nil
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

    local SelectedToy = ToyList["Spooky Candle"] or ToyList[DropdownValues[1]]

    DefenseExtra:CreateDropdown({
        Name = "anti kick item",
        Flag = "Input Lag Item",
        Items = DropdownValues,
        Default = "Spooky Candle", 
        Callback = function(Value)
            SelectedToy = ToyList[Value]
        end
    })

    -- =========================================================================
    -- HELPER FUNCTIONS FOR ANTI-KICK ITEM
    -- =========================================================================
    local function GetMagnitude(Part1, Part2)
        return (Part1.Position - Part2.Position).Magnitude
    end

    local function FWD(parent, part, timeOffset)
        return parent:FindFirstChild(part) or parent:WaitForChild(part, timeOffset or 1)
    end

    local function CFP(parent, part)
        return parent:FindFirstChild(part) ~= nil  
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

    -- =========================================================================
    -- ANTI-KICK ITEM TOGGLE
    -- =========================================================================
    DefenseExtra:CreateToggle({
        Name = "Anti Kick [ITEM]",
        Flag = "AntiKickItemFlag",
        Default = false,
        Callback = function(Val)
            if SetToggleState then SetToggleState("AntiKickItemFlag", Val) end
            AntiKickItemActive = Val 
            
            if Val then
                task.spawn(function()
                    local Item, SoundPart
                    while AntiKickItemActive and task.wait() do 
                        local char = plr.Character
                        local hrp = char and char:FindFirstChild("HumanoidRootPart")
                        local hum = char and char:FindFirstChild("Humanoid")
                        local inPlot = plr:FindFirstChild("InPlot")
                        local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                        local destroyToy = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("DestroyToy")
                        
                        -- Safety Checks
                        if not hrp or not hum or hum.Health <= 0 or not inv then continue end  
                        if inPlot and inPlot.Value then continue end 
                        
                        -- Initiate PCLD Tracking if needed
                        if not MyPCLD and not pcldConn then
                            FindPCLD(hrp)
                        end

                        Item = inv:FindFirstChild("AntiKickItem") 
                        SoundPart = Item and Item:FindFirstChild("Hitbox")
                        
                        -- Spawning Logic
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
                                    v.Color = Color3.fromRGB(0, 255, 255) -- Makes the item Cyan
                                end
                            end
                            
                            Item.Name = "AntiKickItem"
                        end
                        
                        -- Ownership Maintenance
                        if SoundPart and not CheckNetworkOwnerOnPart(SoundPart) then 
                            sno(SoundPart)
                        end
                        
                        -- Server-Synced Movement Logic
                        local targetPart = MyPCLD or hrp:FindFirstChild("FirePlayerPart") or hrp
                        if SoundPart and targetPart then
                            SoundPart.CFrame = targetPart.CFrame
                            SoundPart.AssemblyLinearVelocity = Vector3.zero
                            SoundPart.AssemblyAngularVelocity = Vector3.zero
                        end
                    end
                end)
            else
                -- Cleanup when toggled off
                if pcldConn then pcldConn:Disconnect() pcldConn = nil end
                MyPCLD = nil
                
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

    -- Watchdog to reset PCLD tracker when character dies/respawns
    plr.CharacterAdded:Connect(function(char)
        if AntiKickItemActive then
            MyPCLD = nil
            local hrp = char:WaitForChild("HumanoidRootPart", 5)
            if hrp then FindPCLD(hrp) end
        end
    end)
end

-- =========================================================================
-- SHURIKEN ANTI KICK (Adapted for Script 6)
-- =========================================================================
DefenseExtra:CreateToggle({
    Name = "Anti Kick",
    Flag = "ShurikenAntiKick",
    Default = false,
    Callback = function(Value)
        SetToggleState("ShurikenAntiKick", Value)
        _G.ShurikenAntiKick = Value
        
        local function ClearKunai()
            local inv = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
            local destroyrem = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("DestroyToy")
            if inv and destroyrem then
                for _, v in pairs(inv:GetChildren()) do
                    if v.Name == "AntiKick" or v.Name == "NinjaShuriken" then
                        pcall(function() destroyrem:FireServer(v) end)
                    end
                end
            end
        end

        if Value then
            task.spawn(function()
                local setOwner = RS:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
                local stickyEvent = RS:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")
                local spawnRemote = RS:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
                local canSpawn = Player:WaitForChild("CanSpawnToy")

                local function getHRP()
                    if Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
                        return Player.Character.HumanoidRootPart
                    else
                        return Player.CharacterAdded:Wait():WaitForChild("HumanoidRootPart")
                    end
                end

                local function CheckForHome()
                    if not workspace.PlotItems.PlayersInPlots:FindFirstChild(Player.Name) then return false end
                    for _, v in pairs(workspace.Plots:GetChildren()) do
                        local sign = v:FindFirstChild("PlotSign")
                        local owners = sign and sign:FindFirstChild("ThisPlotsOwners")
                        if owners then
                            for _, b in pairs(owners:GetChildren()) do
                                if b.Value == Player.Name then
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
                        if not kunai.SoundPart:FindFirstChild("PartOwner") or kunai.SoundPart.PartOwner.Value ~= Player.Name then 
                            setOwner:FireServer(kunai.SoundPart, kunai.SoundPart.CFrame)
                        end
                    end
                    
                    local firePart = currentHRP:FindFirstChild("FirePlayerPart") or currentHRP:WaitForChild("FirePlayerPart", 5)
                    if firePart then
                        stickyEvent:FireServer(kunai.StickyPart, firePart, CFrame.new(0,0,0) * CFrame.Angles(0,math.rad(90),math.rad(90)))
                    end
                    
                    for _, obj in pairs(kunai:GetChildren()) do
                        if obj.Name == "Pyramid" then
                            obj.CanTouch = false; obj.CanCollide = false; obj.CanQuery = false; obj.Transparency = 0
                            if not obj:FindFirstChild("Highlight") then
                                local high = Instance.new("Highlight", obj)
                                high.FillColor = Color3.fromRGB(0, 0, 0)
                            end
                        elseif obj.Name == "Main" then
                            obj.CanTouch = false; obj.CanCollide = false; obj.CanQuery = false; obj.Transparency = 0
                            if not obj:FindFirstChild("Highlight") then
                                local high = Instance.new("Highlight", obj)
                                high.FillColor = Color3.fromRGB(255, 255, 255)
                            end
                        elseif obj:IsA("BasePart") then
                            obj.CanTouch = false; obj.CanCollide = false; obj.CanQuery = false; obj.Transparency = 1
                        end
                    end
                end

                local function SpawnToy(name)
                    local t = tick()
                    while not canSpawn.Value do
                        if not _G.ShurikenAntiKick or tick() - t > 5 then return nil end
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
                    local inv = workspace:FindFirstChild(Player.Name.."SpawnedInToys")
                    if boolik and house then 
                        return house:WaitForChild(name, 2)
                    elseif not workspace.PlotItems.PlayersInPlots:FindFirstChild(Player.Name) and inv then 
                        return inv:WaitForChild(name, 2)
                    end
                    return nil
                end

                while _G.ShurikenAntiKick do 
                    task.wait(0.005)
                    if not Player.Character or not Player.Character:FindFirstChild("Humanoid") or Player.Character.Humanoid.Health <= 0 then 
                        continue 
                    end
                    
                    local inv = workspace:FindFirstChild(Player.Name.."SpawnedInToys")
                    local kunai = inv and inv:FindFirstChild("NinjaShuriken")
                    
                    if workspace.PlotItems.PlayersInPlots:FindFirstChild(Player.Name) then 
                        local boolik, house = CheckForHome()
                        if boolik and house and workspace.Plots:FindFirstChild(house.Name) then
                            local sign = workspace.Plots[house.Name]:FindFirstChild("PlotSign")
                            if sign and sign.ThisPlotsOwners.Value.TimeRemainingNum.Value > 89 then 
                                kunai = SpawnToy("NinjaShuriken")
                                if kunai == nil then continue end
                                kunai.Name = "AntiKick" 
                                StickKunai(kunai)
                            end
                        end
                    end
                    
                    if not kunai then
                        if workspace.PlotItems.PlayersInPlots:FindFirstChild(Player.Name) then continue end 
                        kunai = SpawnToy("NinjaShuriken")
                        if kunai == nil then continue end 
                        kunai.Name = "AntiKick"
                        if not kunai then continue end 
                    end
                    
                    repeat
                        if kunai and kunai:FindFirstChild("StickyPart") and kunai.StickyPart.CanTouch == true then
                            StickKunai(kunai)
                            kunai.Name = "AntiKick"
                        end
                        task.wait(0.3)
                    until not kunai or not _G.ShurikenAntiKick or not kunai:FindFirstChild("StickyPart") or kunai.StickyPart.CanTouch == false 
                        or not Player.Character or not Player.Character:FindFirstChild("HumanoidRootPart") 
                        or not kunai:FindFirstChild("StickyPart") 
                        or (Player.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20
                        
                    if not kunai or not kunai:FindFirstChild("StickyPart") or not Player.Character or not Player.Character:FindFirstChild("HumanoidRootPart") or (Player.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20 then 
                        ClearKunai()
                    end 
                    
                    pcall(function()
                        repeat task.wait(0.05) until not _G.ShurikenAntiKick or not Player.Character or not Player.Character:FindFirstChild("Humanoid") or not kunai or not kunai:FindFirstChild("StickyPart") or not kunai.StickyPart:FindFirstChild("StickyWeld") or not kunai.StickyPart.StickyWeld.Part1
                        if not kunai or not kunai:FindFirstChild("StickyPart") or (Player.Character and Player.Character:FindFirstChild("Humanoid") and Player.Character.Humanoid.Health <= 0) or not kunai["StickyPart"]:FindFirstChild("StickyWeld").Part1 then 
                            ClearKunai()
                        end
                    end)
                end
                ClearKunai()
            end)
        else
            _G.ShurikenAntiKick = false
            ClearKunai()
        end
    end
})

Player.CharacterAdded:Connect(function()
    if _G.ShurikenAntiKick then
        task.wait(1)
    end
end)

-- AUTO-RESPAWN LOGIC: Re-runs the script loop when you die and respawn
plr.CharacterAdded:Connect(function()
    if _G.ShurikenAntiKick then
        task.wait(1) -- Wait for character to load properly
        -- The loop in the toggle will naturally pick up the new HRP
    end
end)
DefenseExtra:CreateToggle({
	Name = "Loop TP",
        Flag = "Loop TP",
	Default = false,
	Callback = function(Value)
        SetToggleState("Loop TP", Value)
		tpActive = Value
		local char = Player.Character or Player.CharacterAdded:Wait()
		local hrp = char:WaitForChild("HumanoidRootPart")
		local hum = char:FindFirstChildOfClass("Humanoid")
		if Value then
			if hum then
				hum.PlatformStand = true
			end
			task.spawn(function()
				while tpActive and hrp do
					local x = math.random(-500, 500)
					local y = math.random(30, 480)
					local z = math.random(-500, 500)
					hrp.CFrame = CFrame.new(x, y, z)
					task.wait(0.03)
				end
			end)
		else
			if hum then
				hum.PlatformStand = false
			end
		end
	end,
})
local PS = game:GetService("Players")
local Player = PS.LocalPlayer

-- Variables to store state
local selectedKickPlayer = nil
local kickLoopEnabled = false
local kickLoopConnection = nil
local savedKickPos = nil
local currentKickTargetChar = nil

-- // Helper Functions \\ --

-- Formats the list as "Display Name (@Username)"
local function getPlayerList()
    local list = {}
    for _, plr in ipairs(PS:GetPlayers()) do
        if plr ~= Player then
            table.insert(list, plr.DisplayName .. " (@" .. plr.Name .. ")")
        end
    end
    return list
end

-- Extracts the username from the "Display Name (@Username)" string
local function getPlayerFromSelection(selection)
    if not selection or selection == "" then return nil end
    local username = selection:match("@(.-)%)")
    if username then
        return PS:FindFirstChild(username)
    end
    return nil
end

-- // UI Setup \\ --

-- Assuming 'Tabs' is defined in your main script setup
local TargetGroup = Tabs.Target:CreateBlock({Name = "Target Interaction", Side = "Left"})
local BlobGroup = Tabs.Target:CreateBlock({Name = "Blobman Kick", Side = "Right"})
local TelekinesisGroup = Tabs.Target:CreateBlock({Name = "Telekinesis", Side = "Right"})


local vu390 = {
    localPlayer = game:GetService("Players").LocalPlayer,
    Players = game:GetService("Players"),
    auraRadius = 25,
    SetNetworkOwner = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
}

local vu12 = { CurrentCamera = workspace.CurrentCamera }

vu390.localPlayer.CharacterAdded:Connect(function(p403)
    vu390.playerCharacter = p403
end)

local function startHellSendAura()
    vu390.gravityCoroutine = coroutine.create(function()
        while true do
            local v421, v422 = pcall(function()
                local v404 = vu390.localPlayer.Character
                if v404 and v404:FindFirstChild("HumanoidRootPart") then
                    local v405 = v404.HumanoidRootPart
                    local v406 = vu12.CurrentCamera
                    for _, v410 in pairs(vu390.Players:GetPlayers()) do
                        if v410 ~= vu390.localPlayer and v410.Character then
                            local v411 = v410.Character
                            local v412 = v411:FindFirstChild("Torso") or v411:FindFirstChild("UpperTorso")
                            if v412 and (v412.Position - v405.Position).Magnitude <= vu390.auraRadius then
                                vu390.SetNetworkOwner:FireServer(v412, v405.CFrame)
                                for _, v416 in ipairs(v411:GetDescendants()) do
                                    if v416:IsA("BasePart") then
                                        v416.CanCollide = false
                                    end
                                end
                                local v417 = v412:FindFirstChild("HellAuraPos") or Instance.new("BodyPosition")
                                v417.Name = "HellAuraPos"
                                v417.MaxForce = Vector3.new(100000, 100000, 100000)
                                v417.D = 500
                                v417.P = 50000
                                v417.Parent = v412
                                local v418 = v412:FindFirstChild("HellAuraGyro") or Instance.new("BodyGyro")
                                v418.Name = "HellAuraGyro"
                                v418.MaxTorque = Vector3.new(100000, 100000, 100000)
                                v418.D = 500
                                v418.P = 50000
                                v418.Parent = v412
                                local v419 = v406.CFrame.LookVector
                                local v420 = Vector3.new(0, 5, 0)
                                v417.Position = v405.Position + v419 * 15 + v420
                                v418.CFrame = CFrame.new(v412.Position, v405.Position)
                            end
                        end
                    end
                end
            end)
            if not v421 then
                warn("Error in Hell Send Aura: " .. tostring(v422))
            end
            task.wait(0.05)
        end
    end)
    coroutine.resume(vu390.gravityCoroutine)
end

local function stopHellSendAura()
    if vu390.gravityCoroutine then
        coroutine.close(vu390.gravityCoroutine)
        vu390.gravityCoroutine = nil
    end
end

TelekinesisGroup:CreateToggle({
    Name = "Telekinesis Aura",
        Flag = "Telekinesis Aura",
    Default = false,
    Callback = function(Value)
        SetToggleState("Telekinesis Aura", Value)
        if Value then
            startHellSendAura()
        else
            stopHellSendAura()
        end
    end
})

local deathConnection = nil
local vu29 = { Death_Aura = false }
local vu6 = {
    SetNetworkOwner = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"),
    DestroyGrabLine = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
}

local function death(p424)
    if deathConnection then
        deathConnection:Disconnect()
        deathConnection = nil
    end
    if p424 then
        vu29.Death_Aura = true
        deathConnection = game:GetService("RunService").Heartbeat:Connect(function()
            for _, v429 in ipairs(game:GetService("Players"):GetPlayers()) do
                if v429 ~= LocalPlayer and v429.Character then
                    local vu430 = v429.Character:FindFirstChild("HumanoidRootPart")
                    local vu431 = v429.Character:FindFirstChild("Head")
                    local vu432 = v429.Character:FindFirstChildOfClass("Humanoid")
                    if vu430 and vu431 and vu432 and vu432.Health > 0 and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                        if (vu430.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 25 then
                            pcall(function()
                                vu6.SetNetworkOwner:FireServer(vu430, vu430.CFrame)
                                task.wait(0.1)
                                vu6.DestroyGrabLine:FireServer(vu430)
                                if vu431:FindFirstChild("PartOwner") and vu431.PartOwner.Value == LocalPlayer.Name then
                                    for _, v436 in pairs(vu432.Parent:GetChildren()) do
                                        if v436:IsA("BasePart") then
                                            v436.CFrame = CFrame.new(-1000000000, 1000000000, -1000000000)
                                        end
                                    end
                                    task.wait()
                                    for _, v440 in pairs(vu432.Parent:GetChildren()) do
                                        if v440:IsA("BasePart") then
                                            v440.CFrame = CFrame.new(-1000000000, 1000000000, -1000000000)
                                        end
                                    end
                                    local vu441 = Instance.new("BodyVelocity")
                                    vu441.Velocity = Vector3.new(0, -9999999, 0)
                                    vu441.MaxForce = Vector3.new(9000000000, 9000000000, 9000000000)
                                    vu441.P = 100000075
                                    vu441.Parent = vu430
                                    vu432.Sit = false
                                    vu432.Jump = true
                                    vu432.BreakJointsOnDeath = false
                                    vu432:ChangeState(Enum.HumanoidStateType.Dead)
                                    task.delay(2, function()
                                        if vu441 and vu441.Parent then
                                            vu441:Destroy()
                                        end
                                    end)
                                end
                            end)
                        end
                    end
                end
            end
        end)
    else
        vu29.Death_Aura = false
    end
end

TelekinesisGroup:CreateToggle({
    Name = "Death Aura",
        Flag = "Death Aura",
    Default = false,
    Callback = death
})
-- [Kick Aura OP PREMIUM removed]
do
    -- // Services & Variables \\ --
    local playersService = game:GetService("Players")
    local workspaceService = game:GetService("Workspace")
    local debrisService = game:GetService("Debris")
    local localPlayer = playersService.LocalPlayer

    -- Remote Events required for network ownership
    local setNetworkOwnerEvent = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")

    -- Global Variables used by the Fling Aura UI
    _G.FlingAura = false
    _G.FlingStrength = 400
    _G.FlingTarget = 1 -- 1 = Players, 2 = Objects, 3 = Players and Objects

    -- // Helper Functions \\ --
    
    -- Calculates the CFrame needed to point the fling velocity at the target
    local function lookAt(startPosition, targetPosition)
        local directionVector = (targetPosition - startPosition).Unit
        local rightVector = directionVector:Cross((Vector3.new(0, 1, 0)))
        local upVector = rightVector:Cross(directionVector)
        return CFrame.fromMatrix(startPosition, rightVector, upVector)
    end

    local function GetPlayerCharacter()
        if localPlayer.Character and (localPlayer.Character:FindFirstChild("HumanoidRootPart") and localPlayer.Character:FindFirstChildOfClass("Humanoid")) then
            return localPlayer.Character
        end
    end

    local function GetPlayerRoot()
        local playerHumanoidRootPart = GetPlayerCharacter()
        if playerHumanoidRootPart then
            return playerHumanoidRootPart.HumanoidRootPart
        end
    end

    -- Network Ownership Checks
    local function CheckNetworkOwnerShipOnPart(potentialPart, condition)
        if typeof(potentialPart) == "Instance" and (potentialPart:FindFirstChild("PartOwner") and potentialPart.PartOwner.Value == localPlayer.Name) then
            return not condition and true or potentialPart.PartOwner
        end
    end

    local function CheckNetworkOwnerShipOnPlayer(potentialPlayer, condition)
        if typeof(potentialPlayer) == "Instance" and (potentialPlayer:IsA("Player") and potentialPlayer.Character) and (potentialPlayer.Character:FindFirstChild("Head") and (potentialPlayer.Character.Head:FindFirstChild("PartOwner") and potentialPlayer.Character.Head.PartOwner.Value == localPlayer.Name)) then
            return not condition and true or potentialPlayer.Character.Head.PartOwner
        end
    end

    local function SNOWshipPlayer(otherPlayer, callbackFunction)
        if localPlayer.Character and (localPlayer.Character:FindFirstChild("HumanoidRootPart") and (typeof(otherPlayer) == "Instance" and (otherPlayer:IsA("Player") and otherPlayer.Character)) and otherPlayer.Character:FindFirstChild("HumanoidRootPart")) then
            local otherPlayerHumanoidRootPart = otherPlayer.Character.HumanoidRootPart
            local distanceFromOtherPlayer = localPlayer:DistanceFromCharacter(otherPlayerHumanoidRootPart.Position)
            if CheckNetworkOwnerShipOnPlayer(otherPlayer) then
                if type(callbackFunction) == "function" then
                    callbackFunction()
                end
                return true
            end
            if distanceFromOtherPlayer <= 30 then
                setNetworkOwnerEvent:FireServer(otherPlayerHumanoidRootPart, lookAt(localPlayer.Character.HumanoidRootPart.Position, otherPlayerHumanoidRootPart.Position))
            end
        end
    end

    local function SNOWshipTrack(targetPart)
        if targetPart.Parent and targetPart.Parent:IsA("Model") then
            local targetModel = targetPart.Parent
            local isOwnershipTrackConnected = targetModel:GetAttribute("OwnershipTrackConnected")
            local isCreatedConnected2 = targetModel:GetAttribute("CreatedConnected2")
            if localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local distanceFromCharacter = localPlayer:DistanceFromCharacter(targetPart.Position)
                if isCreatedConnected2 then
                    if isOwnershipTrackConnected then
                        return true
                    end
                    if distanceFromCharacter <= 30 then
                        setNetworkOwnerEvent:FireServer(targetPart, lookAt(localPlayer.Character.HumanoidRootPart.Position, targetPart.Position))
                    end
                else
                    targetModel:SetAttribute("CreatedConnected2", true)
                    targetModel.DescendantAdded:Connect(function(attribute)
                        if attribute.Name ~= "PartOwner" or attribute.Value ~= localPlayer.Name then
                            if attribute.Name == "PartOwner" and attribute.Value ~= localPlayer.Name then
                                targetModel:SetAttribute("OwnershipTrackConnected", false)
                            end
                        else
                            targetModel:SetAttribute("OwnershipTrackConnected", true)
                        end
                    end)
                end
            end
        end
    end

    -- Target Validation Checks
    local function CheckPlayer(potentialPlayer)
        if typeof(potentialPlayer) == "Instance" and (potentialPlayer ~= localPlayer and potentialPlayer.Character) and (potentialPlayer.Character:IsDescendantOf(workspaceService) and (potentialPlayer.Character:FindFirstChild("HumanoidRootPart") and (potentialPlayer.Character:FindFirstChildOfClass("Humanoid") and potentialPlayer.Character.Humanoid.Health > 0))) then
            return true
        end
    end

    local function CheckPlayerAuras(potentialKickedPlayer1)
        if CheckPlayer(potentialKickedPlayer1) and not potentialKickedPlayer1.Character:GetAttribute("Kicking") then
            return true
        end
    end

    -- Spatial parameters for finding loose objects
    local COAroundPParams = OverlapParams.new()
    COAroundPParams.FilterType = Enum.RaycastFilterType.Exclude

    local function CheckObjectsAroundPlayer()
        -- Ensure this list dynamically updates locally within the function
        COAroundPParams.FilterDescendantsInstances = {
            GetPlayerCharacter(),
            workspaceService.Map,
            workspaceService.Plots,
            workspaceService.Waypoints,
            workspaceService.Slots
        }
        
        local playerRoot = GetPlayerRoot()
        if playerRoot then
            local connectedPartsList = {}
            local teslaCoil = nil
            local function isPartConnectable(part)
                if not part:IsDescendantOf(workspaceService.Map) and (not part:IsDescendantOf(workspaceService.Plots) and (not part:IsDescendantOf(workspaceService.Waypoints) and (not part:IsDescendantOf(workspaceService.Slots) and part.Parent))) and (part.Parent:IsA("Model") and (part.Parent:FindFirstChildOfClass("BasePart") or (part.Parent:FindFirstChildOfClass("Part") or part.Parent:FindFirstChildOfClass("MeshPart")))) then
                    local partParent = part.Parent
                    local isConnected2 = partParent:GetAttribute("Connected2")
                    
                    local playerFromCharacter
                    if partParent:FindFirstChildOfClass("Humanoid") then
                        playerFromCharacter = playersService:GetPlayerFromCharacter(partParent)
                    else
                        playerFromCharacter = nil
                    end
                    if not (playerFromCharacter or isConnected2) then
                        return true
                    end
                end
            end
            local partsInRadius = workspaceService:GetPartBoundsInRadius(playerRoot.Position, 28, COAroundPParams)
            local iterator, partIndex, index = pairs(partsInRadius)
            while true do
                local instance
                index, instance = iterator(partIndex, index)
                if index == nil then
                    break
                end
                if isPartConnectable(instance) then
                    local instanceParent = instance.Parent
                    if not table.find(connectedPartsList, instanceParent) then
                        table.insert(connectedPartsList, instanceParent)
                    end
                end
            end
            return connectedPartsList, teslaCoil
        end
    end

    -- // TelekinesisGroup UI Mapping \\ --

    TelekinesisGroup:CreateToggle({
        Name = "Fling Aura",
        Flag = "flingaura_toggle",
        Default = false,
        Callback = function(flingAuraEnabled)
            -- Apply typical UI State mapping
            if SetToggleState then SetToggleState("flingaura_toggle", flingAuraEnabled) end
            
            _G.FlingAura = flingAuraEnabled
            if flingAuraEnabled then
                -- Wrap in task.spawn to prevent yielding the main UI thread
                task.spawn(function()
                    while _G.FlingAura do
                        -- FLING OBJECTS
                        if _G.FlingTarget == 2 or _G.FlingTarget == 3 then
                            local objectsAroundPlayer, flingTargetPart = CheckObjectsAroundPlayer()
                            if objectsAroundPlayer then
                                local pairsIterator, pairsState, pairsIndex = pairs(objectsAroundPlayer)
                                while true do
                                    local childObject
                                    pairsIndex, childObject = pairsIterator(pairsState, pairsIndex)
                                    if pairsIndex == nil then
                                        break
                                    end
                                    local retryCount1 = 0
                                    if childObject then
                                        local headPart = childObject:FindFirstChild("Head")
                                        local childPairsIterator, iteratorValue7, childPairsIndex = pairs(childObject:GetChildren())
                                        while true do
                                            local childPart
                                            childPairsIndex, childPart = childPairsIterator(iteratorValue7, childPairsIndex)
                                            if childPairsIndex == nil then
                                                break
                                            end
                                            if childPart:IsA("BasePart") and childPart.CanQuery then
                                                local networkOwnership = SNOWshipTrack(childPart)
                                                local playerRootPart = GetPlayerRoot()
                                                if not networkOwnership and headPart then
                                                    networkOwnership = CheckNetworkOwnerShipOnPart(headPart)
                                                end
                                                if networkOwnership and playerRootPart then
                                                    if flingTargetPart then
                                                        local currentPosition = flingTargetPart.Position
                                                        flingTargetPart.Position = childPart.Position
                                                        task.wait()
                                                        flingTargetPart.Position = currentPosition
                                                    elseif not childPart:FindFirstChild("FlingAuraVelocity") then
                                                        local lookAtCFrame = lookAt(playerRootPart.Position, childPart.Position)
                                                        local flingBodyVelocity = Instance.new("BodyVelocity", childPart)
                                                        flingBodyVelocity.Name = "FlingAuraVelocity"
                                                        flingBodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                                        flingBodyVelocity.Velocity = Vector3.new(lookAtCFrame.lookVector.X, 0.5, lookAtCFrame.lookVector.Z) * math.clamp(_G.FlingStrength, 400, 600)
                                                        debrisService:AddItem(flingBodyVelocity)
                                                    end
                                                    retryCount1 = retryCount1 + 1
                                                end
                                                if retryCount1 >= 3 then
                                                    break
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end

                        -- FLING PLAYERS
                        if _G.FlingTarget == 1 or _G.FlingTarget == 3 then
                            local playerPairsIterator, iteratorValue8, playerPairsIndex = pairs(playersService:GetPlayers())
                            while true do
                                local otherPlayer
                                playerPairsIndex, otherPlayer = playerPairsIterator(iteratorValue8, playerPairsIndex)
                                if playerPairsIndex == nil then
                                    break
                                end
                                if CheckPlayerAuras(otherPlayer) then
                                    local otherPlayerRootPart = otherPlayer.Character:FindFirstChild("HumanoidRootPart")
                                    local snowshipPlayer = SNOWshipPlayer(otherPlayer)
                                    local localPlayerCharacter = GetPlayerCharacter()
                                    if otherPlayerRootPart and (snowshipPlayer and (localPlayerCharacter and not otherPlayerRootPart:FindFirstChild("FlingAuraVelocity"))) then
                                        local flingDirectionCFrame = lookAt(localPlayerCharacter.HumanoidRootPart.Position, otherPlayerRootPart.Position)
                                        local flingBodyVelocity = Instance.new("BodyVelocity", otherPlayerRootPart)
                                        flingBodyVelocity.Name = "FlingAuraVelocity"
                                        flingBodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                        flingBodyVelocity.Velocity = Vector3.new(flingDirectionCFrame.lookVector.X, 0.5, flingDirectionCFrame.lookVector.Z) * _G.FlingStrength
                                        debrisService:AddItem(flingBodyVelocity)
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

    TelekinesisGroup:CreateSlider({
        Name = "Strength",
        Flag = "flingstrengthvalue_toggle",
        Min = 400,
        Max = 10000,
        Default = 400,
        Rounding = 0,
        Callback = function(flingStrength)
            _G.FlingStrength = flingStrength
        end
    })

    TelekinesisGroup:CreateDropdown({
        Name = "Target",
        Flag = "flingtarget_dropdown",
        Items = {
            "Players",
            "Objects",
            "Players and Objects"
        },
        Default = "Players",
        Callback = function(flingTargetType)
            if flingTargetType == "Players" then
                _G.FlingTarget = 1
            elseif flingTargetType == "Objects" then
                _G.FlingTarget = 2
            elseif flingTargetType == "Players and Objects" then
                _G.FlingTarget = 3
            end
        end
    })
end
-- 1. Target Interaction Dropdown
local PlayerDropdown = TargetGroup:CreateDropdown({
    Name = "Select player for kick",
    List = getPlayerList(),
    Default = nil,
    Callback = function(Value)
        selectedKickPlayer = getPlayerFromSelection(Value)
    end,
})

-- // Automatic Refresh Logic \\ --

local function updateDropdown()
    local newList = getPlayerList()
    
    if PlayerDropdown then
        -- We use 'false' here so your current selection doesn't reset 
        -- every time a random person joins the server.
        PlayerDropdown:Refresh(newList, false)
    end
    
    -- Safety: If the target left the game, clear the variable
    if selectedKickPlayer and not selectedKickPlayer.Parent then
        selectedKickPlayer = nil
    end
end

-- // Event Connections \\ --

-- These listen for server changes to trigger the UI update
PS.PlayerAdded:Connect(updateDropdown)
PS.PlayerRemoving:Connect(updateDropdown)

-- Initial run to populate the list correctly on startup
updateDropdown()
-- These listeners make the list update automatically
local addedConn = PS.PlayerAdded:Connect(updateDropdown)
local removedConn = PS.PlayerRemoving:Connect(updateDropdown)

-- Ensure the script cleans up if the UI is destroyed/reloaded
Player.CharacterRemoving:Connect(function()
    addedConn:Disconnect()
    removedConn:Disconnect()
end)

local customKickHeight = 25
local kickLoopEnabled = false

-- 1. THE INPUT BOX (Where you type the height)
TargetGroup:CreateInput({
    Name = "Custom Kick Height",
        Flag = "Custom Kick Height",
    Default = "25",
    Placeholder = "Enter height (e.g. 50)",
    Numeric = true, -- Only allows numbers
    Finished = true, -- Updates when you press Enter
    Callback = function(Value)
        local num = tonumber(Value)
        if num then
            customKickHeight = num
        else
            customKickHeight = 25 -- Fallback if input is empty or invalid
        end
    end
})

do
    BlobGroup:CreateToggle({
        Name = "Auto Sit Blobman",
        Flag = "Auto Sit Blobman",
        Default = false,
        Callback = function(Value)
        SetToggleState("Auto Sit Blobman", Value)
            if Value then
                task.spawn(function()
                    while GetToggleState("Auto Sit Blobman") do
                        local Char = Player.Character
                        local Hum = Char and Char:FindFirstChildOfClass("Humanoid")
                        local Root = Char and Char:FindFirstChild("HumanoidRootPart")
                        if Hum and Root and not Hum.SeatPart then
                            local folder = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
                            local blob = folder and folder:FindFirstChild("CreatureBlobman")

                            -- Pas de blob, on en spawn un
                            if not blob then
                                pcall(function()
                                    RS.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                                        "CreatureBlobman",
                                        Root.CFrame * CFrame.new(0, 5, 5),
                                        Vector3.zero
                                    )
                                end)
                                -- Attend que le blob apparaisse
                                local t0 = tick()
                                repeat
                                    R.Heartbeat:Wait()
                                    folder = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
                                    blob = folder and folder:FindFirstChild("CreatureBlobman")
                                until blob or tick() - t0 > 5 or not GetToggleState("Auto Sit Blobman")
                            end

                            -- Sit sur le blob
                            if blob then
                                local seat = blob:FindFirstChildWhichIsA("VehicleSeat")
                                if seat then
                                    Root.CFrame = seat.CFrame * CFrame.new(0, 1, 0)
                                    Root.Velocity = Vector3.zero
                                    seat:Sit(Hum)
                                end
                            end
                        end
                        task.wait(0.1)
                    end
                end)
            end
        end
    })
end
-- -- State Variables -- --
local OrbitSpeed = 5
local OrbitRadius = 15
local OrbitRotation = 0
local OrbitActive = false
local OrbitConnection, OrbitLookConnection

-- -- Helper to find your current Blobman -- --
local function getMyBlob()
    local char = Player.Character
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    local seat = hum and hum.SeatPart
    if seat and seat.Parent and seat.Parent.Name == "CreatureBlobman" then
        return seat.Parent
    end
    return nil
end

BlobGroup:CreateToggle({
    Name = "XOCU (grab + blob)",
        Flag = "XOCU (grab + blob)",
    Default = false,
    Callback = function(on)
        SetToggleState("XOCU (grab + blob)", on)
        kickLoopEnabled = on
        loopKickDualActive = on
        
        if on then
            -- Initial Target Check
            if not selectedKickPlayer then
                notify("Error", "Select target first", 3)
                -- Toggle XOCUBlobKickToggle disabled
                return
            end

            task.spawn(function()
                local RS = game:GetService("ReplicatedStorage")
                local RunService = game:GetService("RunService")
                local GE = RS:FindFirstChild("GrabEvents")
                
                local myChar = Player.Character
                local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                
                if not myRoot then 
                    -- Toggle XOCUBlobKickToggle disabled
                    return 
                end

                local savedPos = myRoot.CFrame
                local dragging = false
                local grabStartTime = 0

                while kickLoopEnabled do
                    local target = selectedKickPlayer
                    if not target or not target.Parent or not target.Character then break end
                    
                    local tChar = target.Character
                    local tRoot = tChar:FindFirstChild("HumanoidRootPart")
                    local tHum = tChar:FindFirstChild("Humanoid")
                    
                    -- Blobman Logic (Dual Hand Spam)
                    local seat = myChar and myChar.Humanoid.SeatPart
                    
                    if tRoot and tHum and tHum.Health > 0 then
                        -- Reset Target Physics
                        tRoot.AssemblyLinearVelocity = Vector3.zero
                        tRoot.Velocity = Vector3.zero

                        -- 1. BLOBMAN SPAM (The Dual Hand Method)
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

                        -- 2. POSITIONING LOGIC (The High Kick Method)
                        if not dragging then
                            myRoot.CFrame = tRoot.CFrame
                            if GE then
                                pcall(function()
                                    tHum.PlatformStand = true
                                    GE.SetNetworkOwner:FireServer(tRoot, myRoot.CFrame)
                                    GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                                end)
                            end
                            
                            if grabStartTime == 0 then grabStartTime = tick() end
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

                -- Cleanup
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
    end
})

-- 2. THE MAIN TOGGLE SCRIPT
BlobGroup:CreateToggle({
    Name = "XOCU spam blob loop",
        Flag = "XOCU spam blob loop", 
    Default = false,
    Callback = function(on)
        SetToggleState("XOCU spam blob loop", on)
        loopKickDualActive = on
        if on then
            if not selectedKickPlayer then
                notify("Error", "Select target first", 3)
                -- Toggle DualHandLoopKick disabled
                return
            end
            
            task.spawn(function()
                while loopKickDualActive do
                    local target = selectedKickPlayer
                    local char = Player.Character
                    local seat = char and char.Humanoid.SeatPart
                    
                    if not seat or not target or not target.Character then
                        task.wait(0.5)
                        continue
                    end
                    
                    local blobman = seat.Parent
                    local remoteFolder = blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
                    local grab = remoteFolder and remoteFolder:FindFirstChild("CreatureGrab")
                    local drop = remoteFolder and remoteFolder:FindFirstChild("CreatureDrop")
                    
                    local targetHRP = target.Character:FindFirstChild("HumanoidRootPart")
                    local L_Det = blobman:FindFirstChild("LeftDetector")
                    local R_Det = blobman:FindFirstChild("RightDetector")
                    
                    -- Dynamic Weld Check (Handles both old Welds and new RigidConstraints)
                    local L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChild("RigidConstraint"))
                    local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChild("RigidConstraint"))

                    if targetHRP and grab and drop and L_Weld and R_Weld then
                        -- HYPER DUAL SPAM (No Teleport)
                        -- We fire all 4 remotes (Grab L+R, Drop L+R) in one frame
                        pcall(function()
                            -- BATCH GRAB
                            grab:FireServer(L_Det, targetHRP, L_Weld)
                            grab:FireServer(R_Det, targetHRP, R_Weld)
                            
                            -- IMMEDIATE DROP (No wait for maximum physics glitching)
                            drop:FireServer(L_Weld, targetHRP)
                            drop:FireServer(R_Weld, targetHRP)
                        end)
                    end
                    
                    -- Crucial: 'task.wait()' with no number is the fastest possible loop (1/60th of a second)
                    task.wait() 
                end
            end)
        else
            loopKickDualActive = false
        end
    end
})

BlobGroup:CreateToggle({
    Name = "XOCU Kill Blob [Fast]",
        Flag = "XOCU Kill Blob [Fast]", 
    Default = false,
    Callback = function(Value)
        SetToggleState("XOCU Kill Blob [Fast]", Value)
        _G.LoopKillBlobActive = Value
        
        if Value then
            local tt = selectedKickPlayer -- Your target variable
            
            if not tt or not tt.Character or not tt.Character:FindFirstChild("HumanoidRootPart") then
                Library:Notify("Error: Select a valid target!", 3)
                -- Toggle DualHandKillBlob disabled
                return
            end

            task.spawn(function()
                while _G.LoopKillBlobActive do
                    pcall(function()
                        local lp = game.Players.LocalPlayer
                        local char = lp.Character
                        local seat = char and char.Humanoid.SeatPart
                        local Blob = seat and seat.Parent
                        
                        -- Verify you are in the Blobman
                        if Blob and Blob.Name == "CreatureBlobman" then
                            local remotes = Blob:FindFirstChild("BlobmanSeatAndOwnerScript")
                            local CG = remotes and remotes:FindFirstChild("CreatureGrab")
                            local CD = remotes and remotes:FindFirstChild("CreatureRelease")
                            local weld = Blob.RightDetector:FindFirstChild("RightWeld") or Blob.RightDetector:FindFirstChild("RigidConstraint")
                            
                            local HRP = Blob.HumanoidRootPart
                            local pos = HRP.CFrame -- Save original position

                            if tt.Character and tt.Character:FindFirstChild("HumanoidRootPart") and tt.Character.Humanoid.Health > 0 then
                                -- 1. SNAP TO TARGET
                                HRP.CFrame = tt.Character.HumanoidRootPart.CFrame
                                task.wait(0.05) -- Reduced delay for speed
                                
                                -- 2. REPEAT UNTIL YOU OWN THEIR PHYSICS
                                -- This is the 'Instant' part: it spams until the server gives up control
                                local startTime = tick()
                                repeat 
                                    CG:FireServer(nil, tt.Character.HumanoidRootPart, weld)
                                    CD:FireServer(weld)
                                    
                                    -- Keep glued to them while waiting for ownership
                                    HRP.CFrame = tt.Character.HumanoidRootPart.CFrame
                                    task.wait() 
                                until not _G.LoopKillBlobActive or isnetworkowner(tt.Character.HumanoidRootPart) or (tick() - startTime > 2)
                                
                                -- 3. KILL & RESET
                                tt.Character.Humanoid:ChangeState("Dead")
                                if stvel then 
                                    stvel(HRP) 
                                end
                                
                                HRP.CFrame = pos -- Return to original spot
                            end
                        end
                    end)

                    if not _G.LoopKillBlobActive then break end
                    task.wait(0.1) -- Small breather before next check
                end
            end)
        end
    end
})
do
    -- =========================================================================
    -- TEST BOMBA (MASS SILENT KICK)
    -- =========================================================================

    -- Helper: Snap Blob to a target CFrame using AlignPosition/AlignOrientation
    local function snapBlobTo(blob, targetCFrame)
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

    -- Helper: Clean up AlignPosition constraints from Blob
    local function releaseBlobAlign(blob)
        local hrp = blob:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        if hrp:FindFirstChild("BlobAlignPos") then hrp.BlobAlignPos:Destroy() end
        if hrp:FindFirstChild("BlobAlignOri") then hrp.BlobAlignOri:Destroy() end
        if hrp:FindFirstChild("BlobAttachment") then hrp.BlobAttachment:Destroy() end
        local targetPart = workspace:FindFirstChild("BlobTarget")
        if targetPart then targetPart:Destroy() end
    end

    -- Helper: Find the Blobman toy
    local function getBlobman()
        local blobFolder = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
        if blobFolder then
            return blobFolder:FindFirstChild("CreatureBlobman")
        end
        return nil
    end

    BlobGroup:CreateButton({
        Name = "Test Bomba (Mass Kick)",
        Callback = function()
            local blob = getBlobman()
            if not blob then
                Library:Notify({ Title = "Error", Content = "Blobman not found!", Duration = 3 })
                return
            end

            local players = {}
            for _, p in ipairs(PS:GetPlayers()) do
                if p ~= Player then
                    table.insert(players, p.Name)
                end
            end

            if #players == 0 then
                Library:Notify({ Title = "Error", Content = "No players to kick!", Duration = 3 })
                return
            end

            Library:Notify({ Title = "Test Bomba", Content = "Starting silent mass kick...", Duration = 3 })

            local grab = blob.BlobmanSeatAndOwnerScript.CreatureGrab
            local release = blob.BlobmanSeatAndOwnerScript.CreatureRelease
            local drop = blob.BlobmanSeatAndOwnerScript.CreatureDrop
            local set = RS.GrabEvents.SetNetworkOwner
            local del = RS.GrabEvents.DestroyGrabLine

            local detector = blob.LeftDetector
            local weld = blob.LeftDetector.LeftWeld
            local localHrp = Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
            
            if not localHrp then return end

            -- Step 1: Grab & Release loop for all players to bring them into processing range
            for _, playerName in ipairs(players) do
                local plr = PS:FindFirstChild(playerName)
                if plr and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                    local targetHrp = plr.Character.HumanoidRootPart
                    snapBlobTo(blob, targetHrp.CFrame)
                    task.wait(0.15)
                    grab:FireServer(blob.RightDetector, targetHrp, blob.RightDetector.RightWeld)
                    release:FireServer(blob.RightDetector.RightWeld)
                    task.wait(0.05)
                end
            end

            -- Step 2: Move Blob to the center of the map
            snapBlobTo(blob, CFrame.new(Vector3.new(0, 100, 0)))
            task.wait(0.1)

            -- Step 3: Process players in a circular formation
            local center = Vector3.new(0, 100, 0)
            local baseRadius = 40
            local radius = baseRadius + (#players * 5) -- Expands circle based on player count
            local numPlayers = #players
            local angleIncrement = (2 * math.pi) / numPlayers
            
            for i, playerName in ipairs(players) do
                local angle = angleIncrement * (i - 1)
                local x = center.X + radius * math.cos(angle)
                local z = center.Z + radius * math.sin(angle)
                local blobPosition = Vector3.new(x, center.Y, z)
                
                -- Move Blob to circle position
                snapBlobTo(blob, CFrame.new(blobPosition))
                task.wait(0.1)
                
                -- Process the targeted player
                local plr = PS:FindFirstChild(playerName)
                if plr and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                    local targetHrp = plr.Character.HumanoidRootPart
                    local blobHrp = blob:FindFirstChild("HumanoidRootPart")
                    
                    if blobHrp then
                        -- Elevate player above Blob
                        targetHrp.CFrame = CFrame.new(blobHrp.Position.X, blobHrp.Position.Y + 20, blobHrp.Position.Z)
                        task.wait(0.15)
                        
                        -- Change Ownership & Destroy grab line
                        set:FireServer(targetHrp, targetHrp.CFrame)
                        del:FireServer(targetHrp)

                        -- Apply Silent Kick Effect
                        grab:FireServer(detector, localHrp, weld)
                        grab:FireServer(detector, targetHrp, weld)
                        drop:FireServer(weld, targetHrp.RootAttachment)
                    end
                end
                task.wait(0)
            end
            
            task.wait(1)
            releaseBlobAlign(blob)
            Library:Notify({ Title = "Success", Content = "Test Bomba execution complete!", Duration = 3 })
        end
    })
end
do
    local SpamSetOwner = {
        AutoRagdoll = false,
        Segments = 8,
        ImpactPower = 10
    }

    TargetGroup:CreateToggle({
        Name = "Ragdoll Spam (Pallet Hammer)",
        Flag = "RagdollSpamHammer",
        Default = false,
        Callback = function(Value)
            SetToggleState("RagdollSpamHammer", Value)
            SpamSetOwner.AutoRagdoll = Value
            
            local RS = game:GetService("ReplicatedStorage")
            local RunService = game:GetService("RunService")
            local Player = game:GetService("Players").LocalPlayer
            
            if Value then
                if not selectedKickPlayer then
                    Library:Notify({ Title = "Error", Content = "Select a target player first!", Duration = 3 })
                    SpamSetOwner.AutoRagdoll = false
                    return
                end

                task.spawn(function()
                    -- 1. Fetch necessary remotes
                    local MenuToys = RS:WaitForChild("MenuToys")
                    local GrabEvents = RS:WaitForChild("GrabEvents")
                    
                    local rSpawn = MenuToys:WaitForChild("SpawnToyRemoteFunction")
                    local rDestroy = MenuToys:WaitForChild("DestroyToy")
                    local rOwner = GrabEvents:WaitForChild("SetNetworkOwner")

                    -- 2. Spawn the Pallet
                    if rSpawn and Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
                        task.spawn(function() 
                            rSpawn:InvokeServer("PalletLightBrown", Player.Character.HumanoidRootPart.CFrame * CFrame.new(0, 5, 0), Vector3.zero) 
                        end)
                    end
                    
                    -- 3. Wait for Pallet to load locally
                    local toyFolder = workspace:WaitForChild(Player.Name .. "SpawnedInToys", 5)
                    if not toyFolder then return end
                    
                    local palletModel = toyFolder:WaitForChild("PalletLightBrown", 5)
                    if not palletModel then return end
                    
                    local palletPart = palletModel:WaitForChild("SoundPart", 5)
                    if not palletPart then return end
                    
                    -- Claim initial ownership
                    if rOwner then 
                        rOwner:FireServer(palletPart, palletPart.CFrame) 
                    end
                    
                    local hammerGoingDown = true
                    local segmentIndex = 0
                    local lastOwnerTime = tick()
                    
                    -- 4. Main Hammer Loop
                    while SpamSetOwner.AutoRagdoll and palletModel.Parent do
                        RunService.Heartbeat:Wait()
                        
                        local targetChar = selectedKickPlayer and selectedKickPlayer.Character
                        local targetHrp = targetChar and targetChar:FindFirstChild("HumanoidRootPart")
                        
                        if targetHrp and rOwner then
                            -- Re-claim network ownership periodically to fight desync
                            if tick() - lastOwnerTime > 1.0 then 
                                rOwner:FireServer(palletPart, palletPart.CFrame) 
                                lastOwnerTime = tick() 
                            end
                            
                            local startPos = targetHrp.Position + Vector3.new(0, 50000, 0)
                            local endPos = targetHrp.Position
                            
                            if hammerGoingDown then
                                segmentIndex = segmentIndex + 1
                                local alpha = segmentIndex / SpamSetOwner.Segments
                                local nextPos = startPos:Lerp(endPos, alpha)
                                
                                palletPart.CFrame = CFrame.new(nextPos)
                                palletPart.AssemblyLinearVelocity = Vector3.new(0, -50000, 0)
                                palletPart.AssemblyAngularVelocity = Vector3.zero
                                
                                if segmentIndex >= SpamSetOwner.Segments then
                                    palletPart.AssemblyLinearVelocity = Vector3.new(0, -SpamSetOwner.ImpactPower, 0)
                                    hammerGoingDown = false
                                end
                            else
                                palletPart.CFrame = CFrame.new(startPos)
                                palletPart.AssemblyLinearVelocity = Vector3.zero
                                segmentIndex = 0
                                hammerGoingDown = true
                            end
                        else
                            -- If target dies or is missing, idle the pallet above your own head
                            if Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
                                palletPart.CFrame = Player.Character.HumanoidRootPart.CFrame * CFrame.new(0, 10, 0)
                                palletPart.AssemblyLinearVelocity = Vector3.zero
                            end
                        end
                    end
                    
                    -- 5. Cleanup when toggled off
                    if rDestroy and palletModel then 
                        rDestroy:FireServer(palletModel) 
                    end
                end)
            end
        end
    })
end
TargetGroup:CreateToggle({
    Name = "Pallet Ragdoll (Invis)",
    Flag = "Ragdoll Target",
    Default = false,
    Callback = function(Value)
        SetToggleState("Ragdoll Target", Value)
        local RS = game:GetService("ReplicatedStorage")
        local RunService = game:GetService("RunService")
        local DestroyToy = RS:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
        local SetNetOwner = RS:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
        local DestroyLine = RS:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
        local toysFolder = workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")
        local lpName = LocalPlayer.Name

        -- Clean up existing frame connections
        local function clearAttackLoop()
            if getgenv().ragdollSteppedConn then
                getgenv().ragdollSteppedConn:Disconnect()
                getgenv().ragdollSteppedConn = nil
            end
        end

        if Value then
            if not selectedKickPlayer then
                Library:Notify("Select target first", 3)
                return
            end

            getgenv().palletRagdollActive = true
            getgenv().PalletForRagdoll = nil
            
            if getgenv().palletCacheConn then
                getgenv().palletCacheConn:Disconnect()
            end
            clearAttackLoop()

            -- 1. Cache and Setup Pallet
            getgenv().palletCacheConn = toysFolder.ChildAdded:Connect(function(child)
                if not getgenv().palletRagdollActive then return end
                if child.Name ~= "PalletLightBrown" and child.Name ~= "PalletForRagdoll" then return end

                local soundPart = child:WaitForChild("SoundPart", 3)
                if not soundPart then return end

                -- Claim network ownership instantly
                pcall(function()
                    SetNetOwner:FireServer(soundPart, soundPart.CFrame)
                    DestroyLine:FireServer(soundPart)
                end)

                local partOwner = soundPart:WaitForChild("PartOwner", 1)
                if partOwner and partOwner.Value == lpName then
                    -- Make fully invisible and non-collidable for local player
                    for _, v in pairs(child:GetChildren()) do
                        if v:IsA("BasePart") then
                            v.CanCollide = false
                            v.CanQuery = false
                            v.Transparency = 1 
                        end
                    end

                    child.Name = "PalletForRagdoll"
                    getgenv().PalletForRagdoll = child

                    -- Toggle flag for the alternating strike directions
                    local strikePhase = false

                    -- 2. Engine-Synced Attack Loop (Stepped runs right before physics simulation)
                    getgenv().ragdollSteppedConn = RunService.Stepped:Connect(function()
                        if not getgenv().palletRagdollActive or not child.Parent then 
                            clearAttackLoop()
                            return 
                        end

                        local tChar = selectedKickPlayer and selectedKickPlayer.Character
                        local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                        local tHum = tChar and tChar:FindFirstChildOfClass("Humanoid")

                        if tRoot and tHum and soundPart.Parent and tHum.Health > 0 then
                            local ragdolledVal = tHum:FindFirstChild("Ragdolled")
                            local isRagdolled = ragdolledVal and ragdolledVal.Value or false

                            if not isRagdolled then
                                -- Alternating hyper-velocity strikes every single frame
                                strikePhase = not strikePhase
                                if strikePhase then
                                    soundPart.CFrame = tRoot.CFrame * CFrame.new(0, 2, 0)
                                    soundPart.AssemblyLinearVelocity = Vector3.new(0, -9e5, 0)
                                else
                                    soundPart.CFrame = tRoot.CFrame * CFrame.new(0, -1, 0)
                                    soundPart.AssemblyLinearVelocity = Vector3.new(0, 9e5, 0)
                                end
                            else
                                -- Instantly pull away to reduce lag once ragdolled
                                soundPart.CFrame = CFrame.new(0, 9e9, 0)
                                soundPart.AssemblyLinearVelocity = Vector3.zero
                            end
                        else
                            soundPart.CFrame = CFrame.new(0, 9e9, 0)
                            soundPart.AssemblyLinearVelocity = Vector3.zero
                        end
                    end)

                    -- Handle respawn/destruction
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

            -- 3. Toy Spawner Function
            getgenv().spawnNewPallet = function()
                if not getgenv().palletRagdollActive then return end
                if getgenv().PalletForRagdoll and getgenv().PalletForRagdoll.Parent then return end
                
                local c = LocalPlayer.Character
                local h = c and c:FindFirstChild("HumanoidRootPart")
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
            -- Clean up everything completely
            getgenv().palletRagdollActive = false
            clearAttackLoop()

            if getgenv().palletCacheConn then
                getgenv().palletCacheConn:Disconnect()
                getgenv().palletCacheConn = nil
            end

            local pallet = getgenv().PalletForRagdoll
            if pallet and pallet.Parent then
                pcall(function() DestroyToy:FireServer(pallet) end)
            end

            getgenv().PalletForRagdoll = nil

            if toysFolder:FindFirstChild("PalletForRagdoll") then
                pcall(function() DestroyToy:FireServer(toysFolder.PalletForRagdoll) end)
            end
        end
    end,
})
TargetGroup:CreateToggle({
    Name = "lock (Ragdoll grab)",
        Flag = "lock (Ragdoll grab)",
    Default = false,
    Callback = function(on)
        SetToggleState("lock (Ragdoll grab)", on)
        kickLoopEnabled = on
        if not on then return end

        task.spawn(function()
            local RS = game:GetService("ReplicatedStorage")
            local RunService = game:GetService("RunService")
            local Players = game:GetService("Players")
            local Player = Players.LocalPlayer
            local GE = RS:WaitForChild("GrabEvents")
            
            local myChar = Player.Character
            local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if not myRoot then 
                -- Toggle LoopKickGrabToggle disabled
                return 
            end

            -- [[ THE LOCK ]]
            local savedPos = myRoot.CFrame
            local dragging = false
            local grabStartTime = 0
            local holdHeight = 30

            while kickLoopEnabled do
                local target = selectedKickPlayer
                if not target or not target.Parent or not target.Character then break end
                
                local tChar = target.Character
                local tRoot = tChar:FindFirstChild("HumanoidRootPart")
                local tHum = tChar:FindFirstChild("Humanoid")

                if tRoot and tHum and tHum.Health > 0 then
                    if not dragging then
                        -- PHASE 1: THE TELEPORT GRAB
                        -- You must touch them for a split second to start the grab
                        myRoot.CFrame = tRoot.CFrame
                        
                        pcall(function()
                            tHum.PlatformStand = true
                            tHum.Sit = true
                            GE.SetNetworkOwner:FireServer(tRoot, myRoot.CFrame)
                            GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                        end)

                        if grabStartTime == 0 then grabStartTime = tick() end
                        -- 0.6s is the "sweet spot" for high ping to register the grab
                        if tick() - grabStartTime > 0.6 then
                            dragging = true
                        end
                    else
                        -- PHASE 2: THE SIMULTANEOUS LOCK & KICK
                        -- Teleport YOU back to your starting spot
                        myRoot.CFrame = savedPos
                        myRoot.AssemblyLinearVelocity = Vector3.zero
                        
                        -- THE KICK: Your random angle jitter logic
                        local randomAngle = CFrame.Angles(
                            math.rad(math.random(-180, 180)),
                            math.rad(math.random(-180, 180)),
                            math.rad(math.random(-180, 180))
                        )
                        
                        -- TELEPORT THEM: Force them 17 studs above your lock
                        local lockCFrame = (savedPos * CFrame.new(0, holdHeight, 0)) * randomAngle
                        tRoot.CFrame = lockCFrame
                        
                        pcall(function()
                            tHum.PlatformStand = true
                            tHum.Sit = false -- Set to false for the "Kick" ragdoll effect
                            GE.SetNetworkOwner:FireServer(tRoot, lockCFrame)
                            GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                        end)
                    end
                else
                    dragging = false
                    grabStartTime = 0
                end
                
                RunService.Heartbeat:Wait()
            end

            -- [[ CLEANUP ]]
            -- When you turn it off, they just drop where they are. No throwing.
            if myRoot then myRoot.CFrame = savedPos end
            
            local target = selectedKickPlayer
            if target and target.Character then
                local tRoot = target.Character:FindFirstChild("HumanoidRootPart")
                if tRoot then
                    pcall(function()
                        GE.DestroyGrabLine:FireServer(tRoot)
                    end)
                end
            end
            
            dragging = false
            kickLoopEnabled = false
            -- Toggle LoopKickGrabToggle disabled
        end)
    end
})
-- ——————————————————————————————————————————————————————————————————————————————————————
-- Fling (Grab) Logic
-- ——————————————————————————————————————————————————————————————————————————————————————
do
    local KillHB = nil
    local HEIGHT_LIMIT = 100000
    local TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)

    -- Camera Management
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
        local char = Player.Character
        if char and char:FindFirstChild("Humanoid") then
            cam.CameraSubject = char.Humanoid
        else
            cam.CameraType = Enum.CameraType.Custom
        end
    end
    local cameraAnchor = CameraAnchor.new()

    -- Helper Functions
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
        local char = Player.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if hrp then char:SetAttribute("OriginalPosition", hrp:GetPivot()) end
    end

    local function getOriginalPos()
        local char = Player.Character
        return char and char:GetAttribute("OriginalPosition") or nil
    end

    local function scheduleReturnHome()
        local originalPos = getOriginalPos()
        if not originalPos then return end
        local conn
        conn = R.Heartbeat:Connect(function()
            local char = Player.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if hrp then
                hrp:PivotTo(originalPos)
                if getgenv().originalFallenHeight then
                    workspace.FallenPartsDestroyHeight = getgenv().originalFallenHeight
                end
                char:SetAttribute("SavingOriginalPos", false)
            end
            cameraAnchor:detach()
            conn:Disconnect()
        end)
    end

    local function findBlobman()
        local toys = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
        return toys and toys:FindFirstChild("CreatureBlobman") or nil
    end

    local function ensureBlobman()
        local b = findBlobman()
        if b then return b end
        -- Re-using existing RS definition from Script 6.lua
        RS.MenuToys.SpawnToyRemoteFunction:InvokeServer(
            "CreatureBlobman",
            Player.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5),
            Vector3.new(0, -15, 0)
        )
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

        local plr = PS:GetPlayerFromCharacter(hum.Parent)
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

    local function performKill()
        -- Re-using selectedKickPlayer variable from your existing Target logic
        if not selectedKickPlayer then return end
        local target = selectedKickPlayer
        local tChar = target and target.Character
        local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
        local tHum = tChar and tChar:FindFirstChild("Humanoid")
        local tHead = tChar and tChar:FindFirstChild("Head")
        
        if not (target and tRoot and tHum and tHead) then return end
        if isTooHigh(target) then return end
        if tHum:GetState() == Enum.HumanoidStateType.Dead then return end

        local char = Player.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if not (char and hrp) then return end

        if not char:GetAttribute("SavingOriginalPos") then
            saveOriginalPos()
        end
        char:SetAttribute("SavingOriginalPos", true)
        getgenv().originalFallenHeight = workspace.FallenPartsDestroyHeight
        workspace.FallenPartsDestroyHeight = 0/0

        local originalPos = getOriginalPos()
        if originalPos then cameraAnchor:attach(originalPos) end

        hrp:PivotTo(CFrame.new(tRoot.Position + TELEPORT_OFFSET))
        setNoCollideChar(tChar)
        RS.GrabEvents.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
        task.wait(0.05)
        RS.GrabEvents.DestroyGrabLine:FireServer(tRoot)
        task.wait(0.05)

        if tHead:FindFirstChild("PartOwner") and tHead.PartOwner.Value == Player.Name then
            task.wait(0.05)
            modifyTarget(tRoot, tHum)
        end
        scheduleReturnHome()
    end

    -- Toggle implementation for TargetGroup
    TargetGroup:CreateToggle({
        Name = "loop kill",
        Flag = "FlingGrab",
        Default = false,
        Callback = function(Value)
            SetToggleState("FlingGrab", Value)
            if Value then
                if KillHB then KillHB:Disconnect() end
                KillHB = R.Heartbeat:Connect(performKill)
            else
                if KillHB then KillHB:Disconnect() KillHB = nil end
                cameraAnchor:detach()
            end
        end
    })
end

do
    local ownershipKickActive = false
    local ownershipKickTask = nil

    -- The core logic function
    local function OwnershipKickFunction(targetName)
        local target = PS:FindFirstChild(targetName)
        if not target then return end

        local GE = RS:WaitForChild("GrabEvents")
        local myChar = Player.Character
        local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
        if not myRoot then return end

        local savedPos = myRoot.CFrame
        local dragging = false
        local grabStartTime = 0
        local checkStartTime = 0

        local currentFPS = 60
        local fpsConn = R.RenderStepped:Connect(function(dt)
            currentFPS = 1 / dt
        end)

        local bodyPos = nil
        local bodyGyro = nil

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

        while ownershipKickActive do
            local currentTarget = PS:FindFirstChild(target.Name)
            if not currentTarget or not currentTarget.Parent then
                cleanupBodies()
                break
            end

            myChar = Player.Character
            myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
            local tChar = currentTarget.Character
            local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
            local tHum = tChar and tChar:FindFirstChild("Humanoid")

            if tRoot and tHum and tHum.Health > 0 and myRoot then
                if not dragging then
                    myRoot.CFrame = tRoot.CFrame * CFrame.new(0, 0, 3)
                    cleanupBodies()
                    checkStartTime = 0

                    pcall(function()
                        tHum.PlatformStand = true
                        tHum.Sit = true
                        GE.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
                        GE.DestroyGrabLine:FireServer(tRoot)
                    end)

                    myRoot.AssemblyLinearVelocity = Vector3.zero
                    myRoot.AssemblyAngularVelocity = Vector3.zero

                    if grabStartTime == 0 then grabStartTime = tick() end
                    if tick() - grabStartTime > 0.35 then
                        dragging = true
                        grabStartTime = 0
                        checkStartTime = tick()
                        local lockPos = savedPos * CFrame.new(0, 15, 0)
                        createBodies(tRoot, lockPos.Position)
                    end
                else
                    myRoot.CFrame = savedPos
                    local lockPos = savedPos * CFrame.new(6, 9, 0)

                    myRoot.AssemblyLinearVelocity = Vector3.zero
                    myRoot.AssemblyAngularVelocity = Vector3.zero

                    if bodyPos and bodyPos.Parent then
                        bodyPos.Position = lockPos.Position
                        if bodyGyro then bodyGyro.CFrame = lockPos end
                    else
                        createBodies(tRoot, lockPos.Position)
                    end

                    tHum.PlatformStand = true

                    pcall(function()
                        GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                        GE.DestroyGrabLine:FireServer(tRoot)
                    end)

                    if checkStartTime > 0 and tick() - checkStartTime > 0.30 then
                        local currentDist = (tRoot.Position - lockPos.Position).Magnitude
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
            
            R.Heartbeat:Wait()
        end

        fpsConn:Disconnect()
        cleanupBodies()
        if myRoot then myRoot.CFrame = savedPos end
    end

    -- New UI Implementation
    TargetGroup:CreateToggle({
        Name = "fastest kick but bad",
        Flag = "fastest_kick_bad", -- Fixed the missing quote
        Default = false,
        Callback = function(on)
            ownershipKickActive = on
            
            if on then
                -- Assuming 'selectedKickPlayer' is defined globally in your script
                if not selectedKickPlayer then
                    -- notify() depends on your UI library's notification function
                    if notify then notify("Error", "Select target first", 3) end
                    return
                end
                
                ownershipKickTask = task.spawn(function()
                    OwnershipKickFunction(selectedKickPlayer.Name)
                end)
            else
                ownershipKickActive = false
                if ownershipKickTask then
                    task.cancel(ownershipKickTask)
                    ownershipKickTask = nil
                end
                
                -- Cleanup logic for the target player
                if selectedKickPlayer and selectedKickPlayer.Character then
                    local tRoot = selectedKickPlayer.Character:FindFirstChild("HumanoidRootPart")
                    if tRoot then
                        for _, v in pairs(tRoot:GetChildren()) do
                            if v:IsA("BodyPosition") or v:IsA("BodyGyro") then
                                pcall(function() v:Destroy() end)
                            end
                        end
                    end
                end
            end
        end
    })
end
do
    -- Services
    local Players = game:GetService("Players")
    local RS = game:GetService("ReplicatedStorage")
    local RunService = game:GetService("RunService")
    local Debris = game:GetService("Debris")

    -- Locals & Tables
    local plr = Players.LocalPlayer
    
    -- Using dynamic character fetching is safer for loops, but we establish initial refs here
    local char = plr.Character or plr.CharacterAdded:Wait()
    local hrp = char:FindFirstChild("HumanoidRootPart") or char:WaitForChild("HumanoidRootPart")
    
    local bool = {
        DoRagdoll = true 
    }
    local etc = {}

    -- Remotes
    local GrabEvents = RS:FindFirstChild("GrabEvents") or RS:WaitForChild("GrabEvents")
    local MenuToys = RS:FindFirstChild("MenuToys") or RS:WaitForChild("MenuToys")
    local HoldEvents = RS:FindFirstChild("HoldEvents") or RS:WaitForChild("HoldEvents")
    
    local SetNetworkOwner = GrabEvents:FindFirstChild("SetNetworkOwner") or GrabEvents:WaitForChild("SetNetworkOwner")
    local DestroyGrabLine = GrabEvents:FindFirstChild("DestroyGrabLine") or GrabEvents:WaitForChild("DestroyGrabLine")
    local DestroyToy = MenuToys:FindFirstChild("DestroyToy") or MenuToys:WaitForChild("DestroyToy")
    local SpawnToyRemote = MenuToys:FindFirstChild("SpawnToyRemoteFunction") or MenuToys:WaitForChild("SpawnToyRemoteFunction")

    -- Helper Functions
    local function FWD(parent, part, time)
        return parent:FindFirstChild(part) or parent:WaitForChild(part, time or 5)
    end

    local function CFP(parent, part)
        return parent:FindFirstChild(part) ~= nil  
    end

    local function sno(part) 
        pcall(function() SetNetworkOwner:FireServer(part, part.CFrame) end)
    end

    local function unsno(part) 
        pcall(function() DestroyGrabLine:FireServer(part) end)
    end

    local function CheckNetworkOwnerOnPart(Part) 
        return CFP(Part, "PartOwner") and Part:FindFirstChild("PartOwner").Value == plr.Name
    end

    local function CheckForPartOwner(Head)
        local PartOwner = Head:FindFirstChild("PartOwner")
        return PartOwner and PartOwner.Value == plr.Name
    end 

    local function SpawnToy(ToyName)
        local InPlot = plr:FindFirstChild("InPlot")
        local InOwnedPlot = plr:FindFirstChild("InOwnedPlot")
        local CanSpawnToy = plr:FindFirstChild("CanSpawnToy")

        if InPlot and InPlot.Value and InOwnedPlot and not InOwnedPlot.Value then 
            InPlot:GetPropertyChangedSignal("Value"):Wait()
        end 
        if CanSpawnToy and not CanSpawnToy.Value then 
            CanSpawnToy:GetPropertyChangedSignal("Value"):Wait()
        end

        local currentHRP = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
        if not currentHRP then return nil end

        local SpawnCF = currentHRP.CFrame * CFrame.new(0, 14, 20)
        local Container = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
        
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
                SpawnToyRemote:InvokeServer(ToyName, SpawnCF, Vector3.zero)
            end)
        end)

        local start = tick()
        repeat task.wait() until spawnedObject or (tick() - start) > 2.5

        if connection then connection:Disconnect() end
        return spawnedObject
    end

    -- ==========================================
    -- TARGET GROUP UI INTEGRATION
    -- ==========================================

    TargetGroup:CreateToggle({
        Name = 'LoopGrab V2',
        Flag = "LoopGrabKickExtra",
        Default = false,
        Callback = function(Value)
            bool.LoopGrabKick = Value  
            if bool.LoopGrabKick then 
                task.spawn(function()
                    -- Hooks to existing selectedKickPlayer from main script targeting
                    local targetPlayerName = selectedKickPlayer and selectedKickPlayer.Name
                    etc.TargetPLR = targetPlayerName and Players:FindFirstChild(targetPlayerName)
                    etc.Head = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Head")
                    etc.Root = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")

                    while bool.LoopGrabKick and RunService.RenderStepped:Wait() do 
                        targetPlayerName = selectedKickPlayer and selectedKickPlayer.Name
                        etc.TargetPLR = targetPlayerName and Players:FindFirstChild(targetPlayerName)
                        if not etc.TargetPLR then continue end
                        
                        etc.Head = etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Head")
                        etc.Root = etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                        etc.Hum = etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Humanoid")
                        
                        local currentHRP = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
                        if not currentHRP then continue end

                        local BodyPos = etc.Root and etc.Root:FindFirstChild("BodyPosition")
                        
                        if (etc.Head and etc.Hum and etc.Root and etc.Hum.Health > 0) and (currentHRP.Position - etc.Root.Position).Magnitude <= 30 then  
                            if not BodyPos then 
                                BodyPos = Instance.new("BodyPosition")
                                BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                BodyPos.Parent = etc.Root
                                BodyPos.P = 75000
                                BodyPos.D = 12000
                            end
                            BodyPos.Position = currentHRP.Position + Vector3.new(0, 15, 0)
                            
                            local isHeld = etc.TargetPLR:FindFirstChild("IsHeld")
                            if (etc.Root.Position.Y <= currentHRP.Position.Y + 15) and (isHeld and isHeld.Value) then
                                for _, v in pairs(etc.TargetPLR.Character:GetChildren()) do
                                    if v:IsA("Part") then
                                        v.CFrame = currentHRP.CFrame * CFrame.new(0, 15, 0)
                                    end
                                end
                            end
                            
                            etc.Root.AssemblyAngularVelocity = Vector3.zero
                            etc.Root.AssemblyLinearVelocity = Vector3.zero
                            etc.Hum.PlatformStand = true
                            
                            sno(etc.Root)
                            RunService.RenderStepped:Wait()
                            sno(etc.Root)
                            unsno(etc.Root)
                            RunService.RenderStepped:Wait()
                            sno(etc.Root)
                            RunService.RenderStepped:Wait()
                            unsno(etc.Root)
                        else 
                            if not etc.Head or not etc.Hum or etc.Hum.Health <= 0 then  
                                etc.TargetPLR.CharacterAdded:Wait()
                                etc.Head = FWD(etc.TargetPLR.Character, "Head", 5)
                                etc.Hum = FWD(etc.TargetPLR.Character, "Humanoid", 5)
                                etc.Root = FWD(etc.TargetPLR.Character, "HumanoidRootPart", 5)
                                
                                if etc.Root then
                                    BodyPos = Instance.new("BodyPosition")
                                    BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                    BodyPos.Parent = etc.Root
                                    BodyPos.P = 75000
                                    BodyPos.D = 12000
                                    BodyPos.Position = currentHRP.Position + Vector3.new(0, 15, 0)
                                end
                            end
                            
                            local oldCF = currentHRP.CFrame
                            while (etc.Head and etc.Hum and etc.Hum.Health > 0) and task.wait(0.01) and bool.LoopGrabKick do 
                                targetPlayerName = selectedKickPlayer and selectedKickPlayer.Name
                                etc.TargetPLR = targetPlayerName and Players:FindFirstChild(targetPlayerName)
                                if not etc.TargetPLR then break end
                                
                                local inPlot = etc.TargetPLR:FindFirstChild("InPlot")
                                if inPlot and inPlot.Value then continue end
                                
                                etc.Head = etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Head")
                                etc.Hum = etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Humanoid")
                                if not etc.Head or not etc.Hum then break end 
                                
                                plr.Character:PivotTo(etc.Head.CFrame * CFrame.new(0, 10, 0))
                                sno(etc.Head)
                                
                                if CheckForPartOwner(etc.Head) then 
                                    break
                                end
                            end
                            currentHRP.CFrame = oldCF
                            
                            if etc.Head and CheckForPartOwner(etc.Head) then 
                                for _, v in pairs(etc.TargetPLR.Character:GetChildren()) do
                                    if v:IsA("BasePart") then
                                        v.CFrame = currentHRP.CFrame * CFrame.new(0, 15, 0)
                                    end
                                end
                            end
                        end
                    end
                    
                    if etc.Root then 
                        for _, v in pairs(etc.Root:GetChildren()) do 
                            if v:IsA("BodyPosition") then 
                                v:Destroy()
                            end
                        end
                    end
                end)
            end
        end    
    })

    TargetGroup:CreateToggle({
        Name = "Loop Banana Ragdoll",
        Flag = "LoopBananaRagdoll",
        Default = false,
        Callback = function(Value)
            bool.LoopRagdoll = Value 
            if Value then 
                task.spawn(function()
                    local banana, SoundPart
                    local targetPlayerName = selectedKickPlayer and selectedKickPlayer.Name
                    etc.TargetPLR = targetPlayerName and Players:FindFirstChild(targetPlayerName)
                    
                    if not etc.TargetPLR then 
                        Library:Notify({ Title = "System", Content = "Error: Target does not exist!", Duration = 3 })
                        return 
                    end 
                    
                    etc.Root = etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Left Leg")
                    local AlignPos
                    local AtachNew
                    
                    while bool.LoopRagdoll and task.wait() do 
                        etc.Root = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Left Leg")
                        if not etc.Root then continue end 
                        
                        local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                        if not inv then continue end

                        banana = inv:FindFirstChild("FoodBanana")
                        SoundPart = banana and banana:FindFirstChild("SoundPart")
                        
                        if not SoundPart then 
                            for _, v in pairs(inv:GetChildren()) do 
                                if v.Name == "FoodBanana" then 
                                    pcall(function() DestroyToy:FireServer(v) end)
                                end 
                            end 
                            
                            banana = SpawnToy("FoodBanana")
                            if not banana then continue end
                            
                            SoundPart = FWD(banana, "SoundPart", 5)
                            if not SoundPart then continue end
                            
                            local holdPart = FWD(banana, "HoldPart", 5)
                            if holdPart then
                                local holdRemote = FWD(holdPart, "HoldItemRemoteFunction", 5)
                                if holdRemote then
                                    pcall(function() holdRemote:InvokeServer(banana, plr.Character) end)
                                end
                            end

                            pcall(function() HoldEvents.Use:FireServer(banana) end)
                            while CFP(banana, "EdiblePart") and bool.LoopRagdoll do task.wait() end
                            pcall(function() HoldEvents.Use:FireServer(banana) end)

                            if holdPart then
                                local dropRemote = FWD(holdPart, "DropItemRemoteFunction", 5)
                                if dropRemote and plr.Character then
                                    pcall(function() dropRemote:InvokeServer(banana, plr.Character:GetPivot() * CFrame.new(0, 15, -10), Vector3.zero) end)
                                end
                            end
                            
                            repeat 
                                task.wait(0.01)
                                SoundPart = banana and banana:FindFirstChild("SoundPart")
                                if not SoundPart then break end 
                                sno(SoundPart)
                            until not SoundPart or CFP(SoundPart, "PartOwner") or not bool.LoopRagdoll
                            
                            unsno(SoundPart)
                            local Atach = Instance.new("Attachment")
                            Atach.Parent = SoundPart
                            
                            AlignPos = Instance.new("AlignPosition")
                            AlignPos.Responsiveness = 100
                            AlignPos.Parent = SoundPart
                            AlignPos.Attachment0 = Atach
                        end
                        
                        for _, v in pairs(banana:GetChildren()) do 
                            if CFP(v, "PartOwner") and not CheckNetworkOwnerOnPart(v) then 
                                pcall(function() DestroyToy:FireServer(banana) end) 
                                banana = nil 
                                break 
                            end
                        end
                        
                        if not banana then continue end 
                        AlignPos = SoundPart:FindFirstChild("AlignPosition")
                        
                        if not AlignPos then 
                            pcall(function() DestroyToy:FireServer(banana) end) 
                            banana = nil 
                            continue 
                        end
                        
                        AtachNew = etc.Root and etc.Root:FindFirstChild("LeftFootAttachment")
                        if not AtachNew then continue end 
                        AlignPos.Attachment1 = AtachNew
                    end 
                end)
            else 
                -- Cleanup
                pcall(function()
                    local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                    local banana = inv and inv:FindFirstChild("FoodBanana")
                    if banana then 
                        local SoundPart = banana:FindFirstChild("SoundPart")
                        local AlignPos = SoundPart and SoundPart:FindFirstChild("AlignPosition")
                        if AlignPos then AlignPos:Destroy() end 
                        DestroyToy:FireServer(banana)
                    end
                end)
            end 
        end    
    })
end
do
    local rs = game:GetService("ReplicatedStorage")
    local RunService = game:GetService("RunService")
    local Players = game:GetService("Players")
    local Workspace = game:GetService("Workspace")

    local DestroyLine = rs:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
    local SetNetOwner = rs:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
    local DestroyToy = rs:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
    local SpawnToy = rs:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")

    local plr = Players.LocalPlayer
    local offset = CFrame.new(0, 15, 0)
    
    local applymethod = false
    local spamGrabKickTask = nil

    -- // Helper Functions \\ --
    local function gblob(hum)
        if hum and hum.SeatPart then
            if hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman" then
                return hum.SeatPart.Parent
            end
        end
        return nil
    end

    local function spawntoy(toy, cf)
        local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
        if not inv then return nil end
        
        local canSpawn = plr:FindFirstChild("CanSpawnToy")
        if canSpawn and not canSpawn.Value then
            canSpawn.Changed:Wait()
        end
        
        local t
        local toyadded = inv.ChildAdded:Connect(function(c)
            if c.Name == toy then
                t = c
            end
        end)
        
        task.spawn(function()
            pcall(function()
                SpawnToy:InvokeServer(toy, cf, Vector3.new(0,0,0))
            end)
        end)
        
        local time = tick() + 2
        repeat task.wait() until t or tick() > time
        toyadded:Disconnect()
        return t
    end

    local function stvel(part)
        part.AssemblyLinearVelocity = Vector3.zero
        part.AssemblyAngularVelocity = Vector3.zero
    end

    local function tp(obj1, obj2)
        local dataPing = 50 -- fallback ping
        pcall(function()
            dataPing = game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValue()
        end)
        obj1.CFrame = CFrame.new(
            obj2.Position + obj2.Velocity * (dataPing / 1000) * 5
        )
    end

    local function sno(obj)
        pcall(function() SetNetOwner:FireServer(obj, obj.CFrame) end)
    end

    -- // UI Integration \\ --
    TargetGroup:CreateToggle({
        Name = "Spam Grab Kick (Blobman)",
        Flag = "SpamGrabKickBlobman",
        Default = false,
        Callback = function(Value)
            if SetToggleState then SetToggleState("SpamGrabKickBlobman", Value) end
            applymethod = Value

            if Value then
                if not selectedKickPlayer then
                    if Library and Library.Notify then
                        Library:Notify({ Title = "Error", Content = "Select a target first!", Duration = 3 })
                    end
                    applymethod = false
                    -- Depending on your UI library, you might need to force the toggle back to false here.
                    return
                end

                spamGrabKickTask = task.spawn(function()
                    local kickbp, kickbg
                    
                    while applymethod and RunService.RenderStepped:Wait() do
                        local char = plr.Character
                        local HRP = char and char:FindFirstChild("HumanoidRootPart")
                        local hum = char and char:FindFirstChild("Humanoid")
                        local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                        
                        local tt = selectedKickPlayer
                        if not tt or not tt.Character or not tt.Character:FindFirstChild("HumanoidRootPart") or not HRP or not inv then
                            break
                        end
                        
                        local Sets = {
                            Char = tt.Character,
                            HRP = tt.Character.HumanoidRootPart,
                            Name = tt.Name
                        }
                        
                        local pos = HRP.CFrame

                        -- Process out-of-workspace character Logic
                        if Sets.Char.Parent ~= workspace then
                            local blob = gblob(hum) or inv:FindFirstChild("CreatureBlobman") or spawntoy("CreatureBlobman", HRP.CFrame * CFrame.new(5, 5, 20))
                            if blob then
                                repeat task.wait() until (blob:FindFirstChild("RightDetector") and blob:FindFirstChild("VehicleSeat")) or not applymethod
                                if not applymethod then break end
                                
                                blob.Name = "cringe"
                                
                                repeat task.wait() pcall(function() blob.VehicleSeat:Sit(hum) end) until hum.SeatPart == blob.VehicleSeat or not applymethod
                                if not applymethod then break end

                                local rFolder = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
                                local CG = rFolder and rFolder:FindFirstChild("CreatureGrab")
                                local CD = rFolder and rFolder:FindFirstChild("CreatureRelease")
                                
                                if CG and CD and blob:FindFirstChild("HumanoidRootPart") then
                                    blob.HumanoidRootPart.CFrame = Sets.HRP.CFrame
                                    task.wait(0.2)
                                    
                                    local isNetOwner = false
                                    repeat
                                        task.wait()
                                        pcall(function()
                                            local rightWeld = blob.RightDetector:FindFirstChild("RightWeld") or blob.RightDetector:FindFirstChild("RigidConstraint")
                                            CG:FireServer(blob.RightDetector, Sets.HRP, rightWeld)
                                            CD:FireServer(rightWeld)
                                            if type(isnetworkowner) == "function" then
                                                isNetOwner = isnetworkowner(Sets.HRP)
                                            else
                                                isNetOwner = true -- Fallback if executor lacks isnetworkowner
                                            end
                                        end)
                                    until isNetOwner or not applymethod
                                    
                                    task.wait(0.2)
                                    Sets.HRP.CFrame = pos * offset
                                    HRP.CFrame = pos
                                    pcall(function() DestroyToy:FireServer(inv:FindFirstChild("cringe")) end)
                                    task.wait(0.5)
                                end
                            end
                        end
                        
                        -- Process standard grab/tp logic
                        if Sets.HRP and hum and HRP then
                            pcall(function() DestroyLine:FireServer(Sets.HRP) end)
                            RunService.RenderStepped:Wait()
                            pcall(function() SetNetOwner:FireServer(Sets.HRP, Sets.HRP.CFrame) end)
                            pcall(function() DestroyLine:FireServer(Sets.HRP) end)
                            RunService.RenderStepped:Wait()
                            pcall(function() SetNetOwner:FireServer(Sets.HRP, Sets.HRP.CFrame) end)
                            pcall(function() DestroyLine:FireServer(Sets.HRP) end)
                            RunService.RenderStepped:Wait()
                            pcall(function() SetNetOwner:FireServer(Sets.HRP, Sets.HRP.CFrame) end)
                            pcall(function() DestroyLine:FireServer(Sets.HRP) end)
                            RunService.RenderStepped:Wait()
                            pcall(function() SetNetOwner:FireServer(Sets.HRP, Sets.HRP.CFrame) end)
                            
                            -- TP Failsafe Distance Check
                            if (Sets.HRP.Position - HRP.Position).Magnitude >= 29 and Sets.Char.Parent == workspace then
                                task.wait(0.1)
                                tp(HRP, Sets.HRP)
                                task.wait(0.2)
                                sno(Sets.HRP)
                                task.wait()
                                HRP.CFrame = pos
                                task.wait(0.2)
                                for i,v in pairs(Sets.Char:GetChildren()) do
                                    if v:IsA("Part") and v.Name ~= "Humanoid" then
                                        v.CFrame = pos * offset
                                    end
                                end
                            end
                            
                            -- Y-Level Drop Check
                            if Sets.HRP.Position.Y < HRP.Position.Y + 4 and Sets.Char.Parent == workspace then
                                local targetHead = Sets.Char:FindFirstChild("Head")
                                if targetHead then
                                    repeat task.wait() sno(Sets.HRP) until targetHead:FindFirstChild("PartOwner") or not applymethod
                                end
                                HRP.CFrame = pos
                                Sets.HRP.CFrame = HRP.CFrame * offset
                            end
                            
                            -- BodyPosition & BodyGyro Locking
                            if not kickbp or kickbp.Parent ~= Sets.HRP then
                                if Sets.HRP:FindFirstChild("XOCU_BlobBP") then Sets.HRP.XOCU_BlobBP:Destroy() end
                                kickbp = Instance.new("BodyPosition")
                                kickbp.Name = "XOCU_BlobBP"
                                kickbp.Parent = Sets.HRP
                                kickbp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                kickbp.D = 200
                                kickbp.Position = HRP.Position + Vector3.new(0,5,0)
                            end
                            
                            if not kickbg or kickbg.Parent ~= Sets.HRP then
                                if Sets.HRP:FindFirstChild("XOCU_BlobBG") then Sets.HRP.XOCU_BlobBG:Destroy() end
                                kickbg = Instance.new("BodyGyro")
                                kickbg.Name = "XOCU_BlobBG"
                                kickbg.Parent = Sets.HRP
                                kickbg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                                kickbg.D = 100
                                kickbg.CFrame = CFrame.new(0, 0, 0)
                            end
                            
                            task.spawn(function()
                                for i,v in pairs(Sets.Char:GetChildren()) do
                                    if v:IsA("BasePart") then
                                        stvel(v)
                                        v.Velocity = Vector3.zero
                                    end
                                end
                            end)
                            
                            kickbp.Position = HRP.Position + Vector3.new(offset.X, offset.Y, offset.Z)
                        end
                    end

                    -- Loop Cleanup
                    local tt = selectedKickPlayer
                    if tt and tt.Character then
                        local tHRP = tt.Character:FindFirstChild("HumanoidRootPart")
                        if tHRP then
                            local oldBp = tHRP:FindFirstChild("XOCU_BlobBP")
                            local oldBg = tHRP:FindFirstChild("XOCU_BlobBG")
                            if oldBp then oldBp:Destroy() end
                            if oldBg then oldBg:Destroy() end
                        end
                    end
                    if kickbp then kickbp:Destroy() end
                    if kickbg then kickbg:Destroy() end
                end)
            else
                -- Toggled Off Cleanup
                applymethod = false
                if spamGrabKickTask then
                    task.cancel(spamGrabKickTask)
                    spamGrabKickTask = nil
                end
                
                local tt = selectedKickPlayer
                if tt and tt.Character then
                    local tHRP = tt.Character:FindFirstChild("HumanoidRootPart")
                    if tHRP then
                        local oldBp = tHRP:FindFirstChild("XOCU_BlobBP")
                        local oldBg = tHRP:FindFirstChild("XOCU_BlobBG")
                        if oldBp then oldBp:Destroy() end
                        if oldBg then oldBg:Destroy() end
                    end
                end
            end
        end
    })
end

local LGK_OAT_kickLoopEnabled = false
local LGK_OAT_sno = function(part)
    if not part or not part.Parent then return end
    pcall(function()
        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(part, part.CFrame)
    end)
end
TargetGroup:CreateToggle({
    Name = "Loop Grab Kick V3",
    Flag = "Loop Grab Kick (BEST)",
    Default = false,
    Callback = function(on)
        SetToggleState("Loop Grab Kick (BEST)", on)
        LGK_OAT_kickLoopEnabled = on
        if not on then return end

        task.spawn(function()
            local target = selectedKickPlayer 
            
            if not target or not target.Parent then
                LGK_OAT_kickLoopEnabled = false
                Library:Notify({ Title = "Oats Kick", Content = "No target selected.", Duration = 3 })
                return
            end

            local myChar = LocalPlayer.Character
            local myHRP  = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if not (myChar and myHRP) then
                LGK_OAT_kickLoopEnabled = false
                return
            end

            local savedPos = myHRP.CFrame
            local lastRemoteFire = tick()

            while LGK_OAT_kickLoopEnabled and RunService.Heartbeat:Wait() do
                target = selectedKickPlayer 
                if not target or not target.Parent then break end

                myChar = LocalPlayer.Character
                myHRP  = myChar and myChar:FindFirstChild("HumanoidRootPart")
                local myHead = myChar and myChar:FindFirstChild("Head")

                local tChar = target.Character
                local tHRP  = tChar and tChar:FindFirstChild("HumanoidRootPart")
                local tHum  = tChar and tChar:FindFirstChild("Humanoid")

                if not (myChar and myHRP and myHead) or not (tHRP and tHum) or tHum.Health <= 0 then
                    continue
                end

                local dist = (tHRP.Position - myHRP.Position).Magnitude

                if dist <= 30 then
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

                    if type(LGK_OAT_sno) == "function" then LGK_OAT_sno(tHRP) end

                    local align = tHRP:FindFirstChild("KickAlign")
                    if align and align.Attachment1 then
                        align.Attachment1.WorldPosition = myHead.Position + Vector3.new(0, 15, 0)
                    end

                    local rot = tHRP:FindFirstChild("KickRot")
                    if rot then
                        rot.CFrame = CFrame.Angles(0, 0, 0)
                    end

                    if tick() - lastRemoteFire > 0.05 then
                        pcall(function()
                            ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(tHRP)
                        end)
                        lastRemoteFire = tick()
                    end
                else
                    pcall(function()
                        myChar:PivotTo(tHRP.CFrame * CFrame.new(0, 2, 4))
                    end)
                    if type(LGK_OAT_sno) == "function" then LGK_OAT_sno(tHRP) end
                end
            end

            if target and target.Character then
                local tH = target.Character:FindFirstChild("HumanoidRootPart")
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
                        ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(tH)
                    end)
                end
            end

            pcall(function()
                if LocalPlayer.Character then
                    LocalPlayer.Character:PivotTo(savedPos)
                end
            end)

            LGK_OAT_kickLoopEnabled = false
        end)
    end,
})
TargetGroup:CreateToggle({
    Name = "Loop Grab Kick V3 (tp back)",
    Flag = "Loop Grab Kick (BEST)",
    Default = false,
    Callback = function(on)
        SetToggleState("Loop Grab Kick (BEST)", on)
        LGK_OAT_kickLoopEnabled = on
        if not on then return end

        task.spawn(function()
            local RunService = game:GetService("RunService")
            local ReplicatedStorage = game:GetService("ReplicatedStorage")
            local target = selectedKickPlayer 
            
            if not target or not target.Parent then
                LGK_OAT_kickLoopEnabled = false
                Library:Notify({ Title = "Oats Kick", Content = "No target selected.", Duration = 3 })
                return
            end

            local myChar = LocalPlayer.Character
            local myHRP  = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if not (myChar and myHRP) then
                LGK_OAT_kickLoopEnabled = false
                return
            end

            -- Save your original plot position
            local savedPos = myHRP.CFrame
            local lastRemoteFire = tick()

            while LGK_OAT_kickLoopEnabled and RunService.Heartbeat:Wait() do
                target = selectedKickPlayer 
                if not target or not target.Parent then break end

                myChar = LocalPlayer.Character
                myHRP  = myChar and myChar:FindFirstChild("HumanoidRootPart")
                local myHead = myChar and myChar:FindFirstChild("Head")

                local tChar = target.Character
                local tHRP  = tChar and tChar:FindFirstChild("HumanoidRootPart")
                local tHum  = tChar and tChar:FindFirstChild("Humanoid")

                if not (myChar and myHRP and myHead) or not (tHRP and tHum) or tHum.Health <= 0 then
                    continue
                end

                local dist = (tHRP.Position - myHRP.Position).Magnitude

                -- FETCH LOGIC
                if dist > 30 then
                    pcall(function()
                        myChar:PivotTo(tHRP.CFrame * CFrame.new(0, 2, 4))
                    end)
                    
                    if type(LGK_OAT_sno) == "function" then LGK_OAT_sno(tHRP) end

                    -- Instantly apply physics constraints
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

                    -- Fixed Wait Logic: Wait exactly 0.3 seconds while firing remotes
                    local grabStartTime = tick()
                    while (tick() - grabStartTime) < 0.3 do
                        task.wait(0.05)
                        if type(LGK_OAT_sno) == "function" then LGK_OAT_sno(tHRP) end
                        pcall(function()
                            ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(tHRP)
                        end)
                        
                        -- Keep updating target attachment position while waiting
                        local align = tHRP:FindFirstChild("KickAlign")
                        if myHead and align and align.Attachment1 then
                            align.Attachment1.WorldPosition = myHead.Position + Vector3.new(0, 15, 0)
                        end
                    end

                    -- Teleport back together
                    pcall(function()
                        myChar:PivotTo(savedPos)
                        tHRP.CFrame = savedPos * CFrame.new(0, 15, 0)
                    end)
                    
                    continue
                end

                -- STANDARD GRAB & KICK LOOP (When distance <= 30)
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

                if type(LGK_OAT_sno) == "function" then LGK_OAT_sno(tHRP) end

                local align = tHRP:FindFirstChild("KickAlign")
                if align and align.Attachment1 then
                    align.Attachment1.WorldPosition = myHead.Position + Vector3.new(0, 15, 0)
                end

                local rot = tHRP:FindFirstChild("KickRot")
                if rot then 
                    rot.CFrame = CFrame.Angles(0, 0, 0) 
                end

                if tick() - lastRemoteFire > 0.05 then
                    pcall(function()
                        ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(tHRP)
                    end)
                    lastRemoteFire = tick()
                end
            end

            -- CLEANUP
            if target and target.Character then
                local tH = target.Character:FindFirstChild("HumanoidRootPart")
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
                        ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(tH)
                    end)
                end
            end

            LGK_OAT_kickLoopEnabled = false
        end)
    end,
})

TargetGroup:CreateToggle({
    Name = "Ragdoll Snowball",
        Flag = "Ragdoll Snowball",
    Default = false,
    Callback = function(Value)
        SetToggleState("Ragdoll Snowball", Value)
        _G.RagdollSnowballActive = Value -- Global flag to control the loop externally
        
        if not Value then return end -- Exit if we just turned it off

        local Players = game:GetService("Players")
        local RS = game:GetService("ReplicatedStorage")
        local Workspace = game:GetService("Workspace")
        local RunService = game:GetService("RunService")
        
        local Player = Players.LocalPlayer
        local SpawnRemote = RS:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
        local ToyFolderName = Player.Name .. "SpawnedInToys"

        task.spawn(function()
            while _G.RagdollSnowballActive do
                -- Use a local reference for the target to ensure consistency in this iteration
                local target = selectedKickPlayer 
                
                if target and target.Character then
                    local tChar = target.Character
                    local torso = tChar:FindFirstChild("HumanoidRootPart") or tChar:FindFirstChild("UpperTorso") or tChar:FindFirstChild("Torso")

                    if torso then
                        -- 1. Spawn logic (Try-Catch with pcall)
                        pcall(function()
                            local offset = Vector3.new(
                                math.random(-5, 5) / 10, 
                                math.random(-5, 5) / 10, 
                                math.random(-5, 5) / 10
                            )
                            -- We use task.spawn here so the RemoteFunction doesn't hang the whole loop
                            task.spawn(function()
                                SpawnRemote:InvokeServer("BallSnowball", torso.CFrame * CFrame.new(offset), Vector3.zero)
                            end)
                        end)

                        -- 2. Teleport existing snowballs to target
                        local folder = Workspace:FindFirstChild(ToyFolderName)
                        if folder then
                            for _, snowball in ipairs(folder:GetChildren()) do
                                if snowball.Name == "BallSnowball" then
                                    local part = snowball:IsA("BasePart") and snowball or snowball.PrimaryPart or snowball:FindFirstChildWhichIsA("BasePart")
                                    if part then
                                        part.CFrame = torso.CFrame
                                        part.AssemblyLinearVelocity = Vector3.zero
                                    end
                                end
                            end
                        end
                    end
                end
                
                -- Heartbeat is good, but adding a tiny wait prevents "Script Exhaustion"
                RunService.Heartbeat:Wait()
            end
        end)
    end
})


local playerFlingActive = false
local flingBAV = nil
local originalPos = nil
TargetGroup:CreateToggle({
	Name = "Fling",
        Flag = "Fling",
	Default = false,
	Callback = function(on)
        SetToggleState("Fling", on)
		playerFlingActive = on
		if on then
			if not selectedKickPlayer then
				notify("System", "Select target first!", 3)
				-- Toggle PlayerFlingBtn disabled
				return
			end
			local RunService = game:GetService("RunService")
			local MyChar = Player.Character
			local MyRoot = MyChar and MyChar:FindFirstChild("HumanoidRootPart")
			if MyRoot then
				originalPos = MyRoot.CFrame
			end
			notify("Maestro", "Fling Mode Activated.", 3)
			task.spawn(function()
				while playerFlingActive do
					local target = selectedKickPlayer
					local char = Player.Character
					local hrp = char and char:FindFirstChild("HumanoidRootPart")
					local hum = char and char:FindFirstChild("Humanoid")
					if not hrp or not hum then
						task.wait(0.5)
						continue
					end
					if target and target.Parent then
						local tChar = target.Character
						local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
						local tHum = tChar and tChar:FindFirstChild("Humanoid")
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
							local loop = RunService.Heartbeat:Connect(function()
								if not playerFlingActive or not tRoot or not tRoot.Parent then
									return
								end
								hrp.CFrame = tRoot.CFrame
								hrp.Velocity = Vector3.zero
							end)
							local startTime = tick()
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
						-- Toggle PlayerFlingBtn disabled
					end
					task.wait(0.1)
				end
				if flingBAV then
					flingBAV:Destroy()
					flingBAV = nil
				end
				local char = Player.Character
				if char then
					for _, part in pairs(char:GetDescendants()) do
						if part:IsA("BasePart") then
							part.CanCollide = true
						end
					end
					local hrp = char:FindFirstChild("HumanoidRootPart")
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
			local char = Player.Character
			local hrp = char and char:FindFirstChild("HumanoidRootPart")
			if hrp then
				hrp.RotVelocity = Vector3.zero
				hrp.Velocity = Vector3.zero
			end
		end
	end
})

game:GetService("UserInputService").InputBegan:Connect(function(input, processed)
	if not processed and input.KeyCode == Enum.KeyCode.T and _G.AutoSitBlobT then
		local plr = game.Players.LocalPlayer
		local char = plr.Character
		local hrp = char and char:FindFirstChild("HumanoidRootPart")
		local hum = char and char:FindFirstChild("Humanoid")
		if not hrp or not hum then
			return
		end
		local folderName = plr.Name .. "SpawnedInToys"
		local folder = workspace:FindFirstChild(folderName)
		local blob = folder and folder:FindFirstChild("CreatureBlobman")
		if not blob then
			task.spawn(function()
				pcall(function()
					game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("CreatureBlobman", hrp.CFrame, Vector3.zero)
				end)
			end)
			if not folder then
				folder = workspace:WaitForChild(folderName, 5)
			end
			if folder then
				blob = folder:WaitForChild("CreatureBlobman", 5)
			end
		end
		if blob then
			local seat = blob:WaitForChild("VehicleSeat", 5)
			if seat then
				local t = tick()
				repeat
					if not hum.SeatPart then
						hrp.CFrame = seat.CFrame + Vector3.new(0, 1, 0)
						hrp.Velocity = Vector3.zero
						seat:Sit(hum)
					end
					game:GetService("RunService").Heartbeat:Wait()
				until hum.SeatPart == seat or tick() - t > 1.5
			end
		end
	end
end)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if not gameProcessed and input.KeyCode == Enum.KeyCode.R then
		if blobMasterSwitch then
			blobFlyActive = not blobFlyActive
			if not blobFlyActive then
				if bvInstance then
					bvInstance:Destroy()
					bvInstance = nil
				end
				if bgInstance then
					bgInstance:Destroy()
					bgInstance = nil
				end
			end
		end
	end
end)
local function GetBlobRoot()
	local char = Player.Character
	local hum = char and char:FindFirstChild("Humanoid")
	if hum and hum.SeatPart and hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman" then
		return hum.SeatPart.Parent:FindFirstChild("HumanoidRootPart") or hum.SeatPart.Parent.PrimaryPart
	end
	local folder = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
	if folder then
		local blob = folder:FindFirstChild("CreatureBlobman")
		if blob then
			return blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart
		end
	end
	return nil
end
game:GetService("RunService").Heartbeat:Connect(function()
	if not blobFlyActive or not blobMasterSwitch then
		if bvInstance then
			bvInstance:Destroy()
			bvInstance = nil
		end
		if bgInstance then
			bgInstance:Destroy()
			bgInstance = nil
		end
		return
	end
	local root = GetBlobRoot()
	if root then
		if not root:FindFirstChild("BlobFlyVelocity") then
			bvInstance = Instance.new("BodyVelocity")
			bvInstance.Name = "BlobFlyVelocity"
			bvInstance.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
			bvInstance.P = 10000
			bvInstance.Parent = root
		else
			bvInstance = root.BlobFlyVelocity
		end
		if not root:FindFirstChild("BlobFlyGyro") then
			bgInstance = Instance.new("BodyGyro")
			bgInstance.Name = "BlobFlyGyro"
			bgInstance.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
			bgInstance.P = 20000
			bgInstance.D = 100
			bgInstance.Parent = root
		else
			bgInstance = root.BlobFlyGyro
		end
		local cam = workspace.CurrentCamera
		local moveDir = Vector3.zero
		if UserInputService:IsKeyDown(Enum.KeyCode.W) then
			moveDir = moveDir + cam.CFrame.LookVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.S) then
			moveDir = moveDir - cam.CFrame.LookVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.A) then
			moveDir = moveDir - cam.CFrame.RightVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.D) then
			moveDir = moveDir + cam.CFrame.RightVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
			moveDir = moveDir + Vector3.new(0, 1, 0)
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
			moveDir = moveDir - Vector3.new(0, 1, 0)
		end
		if bvInstance then
			bvInstance.Velocity = moveDir * blobFlySpeed
		end
		if bgInstance then
			bgInstance.CFrame = cam.CFrame
		end
	else
		if bvInstance then
			bvInstance:Destroy()
			bvInstance = nil
		end
		if bgInstance then
			bgInstance:Destroy()
			bgInstance = nil
		end
	end
end)
local DestroyGucciActive = false
local DestroyTargetGucciActive = false
local DestroyTargetGucciActive = false
local DestroyTargetGucciActive = false
local DestroyTargetGucciActive = false
local DestroyTargetGucciActive = false
local DestroyTargetGucciActive = false
TargetGroup:CreateToggle({
    Name = "Destroy Gucci (sit)",
        Flag = "Destroy Gucci (sit)",
    Default = false,
    Callback = function(Value)
        SetToggleState("Destroy Gucci (sit)", Value)
        DestroyTargetGucciActive = Value
        if Value then
            if not selectedKickPlayer then
                notify("Error", "Error", 3)
                -- Toggle DestroyTargetGucci disabled
                return
            end
            local char = Player.Character
            local root = char and char:FindFirstChild("HumanoidRootPart")
            if not root then
                return
            end
            local SafeSpot = root.CFrame
            local RunService = game:GetService("RunService")
            local folderName = selectedKickPlayer.Name .. "SpawnedInToys"
            notify("System", "spawn toy " .. folderName, 3)
            
            task.spawn(function()
                while DestroyTargetGucciActive do
                    if not selectedKickPlayer or not selectedKickPlayer.Parent then
                        notify("System", "Activated", 3)
                        DestroyTargetGucciActive = false
                        -- Toggle DestroyTargetGucci disabled
                        break
                    end
                    
                    local toysFolder = workspace:FindFirstChild(folderName)
                    if not toysFolder then
                        task.wait(1)
                    else
                        local foundTarget = false
                        for _, obj in ipairs(toysFolder:GetChildren()) do
                            if not DestroyTargetGucciActive then
                                break
                            end
                            
                            -- Logic to target both Blobman and Tractor
                            if obj.Name == "CreatureBlobman" or obj.Name == "TractorGreen" then
                                foundTarget = true
                                local seat = obj:FindFirstChild("VehicleSeat") or obj:FindFirstChildWhichIsA("VehicleSeat", true)
                                
                                if seat then
                                    local myChar = Player.Character
                                    local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                                    local myHum = myChar and myChar:FindFirstChild("Humanoid")
                                    
                                    if myRoot and myHum then
                                        if myHum.SeatPart ~= seat then
                                            notify("Target", "Targeting: " .. obj.Name, 1)
                                            local magnetConnection
                                            magnetConnection = RunService.Stepped:Connect(function()
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
                                                if not DestroyTargetGucciActive then break end
                                                if myHum.SeatPart == seat then break end
                                                seat:Sit(myHum)
                                                task.wait()
                                            end
                                            
                                            if magnetConnection then
                                                magnetConnection:Disconnect()
                                            end
                                            
                                            if myHum.SeatPart == seat then
                                                task.wait(0.3)
                                                myHum.Sit = false
                                                myHum.Jump = true
                                                task.wait(0.05)
                                                myRoot.CFrame = SafeSpot
                                                myRoot.Velocity = Vector3.zero
                                                notify("Success", obj.Name .. " has been removed", 1)
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
                end
            end)
        else
            DestroyTargetGucciActive = false
            notify("System", "remove Gucci off", 2)
        end
    end
})

	--// Allowed items
local AllowedItems = {
    -- Food
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

    -- Instruments
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

    -- Cups
	CupMugWhite = true,
	CupMugBrown = true,

    -- Poop
	PoopPile = true,
	PoopPileSparkle = true,
}

local antiAntiLagEnabled = false

TargetGroup:CreateToggle({
	Name = "Remove Anti Input Lag",
        Flag = "Remove Anti Input Lag",
	Default = false,
	Callback = function(on)
        SetToggleState("Remove Anti Input Lag", on)
		antiAntiLagEnabled = on
		if not on then
			antiAntiLagEnabled = false
			return
		end
		task.spawn(function()
			local plr = game.Players.LocalPlayer
			local char = plr.Character
			local hrp = char:FindFirstChild("HumanoidRootPart")
			if not hrp then
				return
			end
			local burgers = {}
			for _, v in ipairs(workspace:GetDescendants()) do
				if AllowedItems[v.Name] and v:IsA("Model") and v:FindFirstChild("HoldPart") then
					burgers[#burgers + 1] = v
				end
			end
			workspace.DescendantAdded:Connect(function(obj)
				if AllowedItems[obj.Name] and obj:IsA("Model") then
					task.spawn(function()
						local hp = obj:WaitForChild("HoldPart", 3)
						if hp then
							burgers[#burgers + 1] = obj
						end
					end)
				end
			end)
			while antiAntiLagEnabled do
				for i = #burgers, 1, -1 do
					local b = burgers[i]
					if not b or not b.Parent or not b:FindFirstChild("HoldPart") then
						table.remove(burgers, i)
					else
						local hp = b.HoldPart
						pcall(function()
							hp.HoldItemRemoteFunction:InvokeServer(b, char)
						end)
						task.wait()
						pcall(function()
							hp.DropItemRemoteFunction:InvokeServer(
                                b,
                                CFrame.new(hrp.Position + Vector3.new(0, -2000, 0)),
                                Vector3.new(0, 0, 0)
                            )
						end)
					end
				end
				task.wait()
			end
		end)
	end
})

local GrabGroup = Tabs.Grab:CreateBlock({Name = "Grab Customization", Side = "Left"})
_G.strength = 750
local strengthConnection
GrabGroup:CreateSlider({
	Name = "Power",
        Flag = "Power",
	Default = 750,
	Min = 1,
	Max = 20000,
	Rounding = 0,
	Callback = function(value)
		_G.strength = value
	end
})
GrabGroup:CreateToggle({
	Name = "Strength",
        Flag = "Strength",
	Default = false,
	Callback = function(enabled)
        SetToggleState("Strength", Value)
		if enabled then
			strengthConnection = workspace.ChildAdded:Connect(function(model)
				if model.Name == "GrabParts" then
					local partToImpulse = model.GrabPart.WeldConstraint.Part1
					if partToImpulse then
						local velocityObj = Instance.new("BodyVelocity", partToImpulse)
						model:GetPropertyChangedSignal("Parent"):Connect(function()
							if not model.Parent then
								if UserInputService:GetLastInputType() == Enum.UserInputType.MouseButton2 then
									velocityObj.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
									velocityObj.Velocity = workspace.CurrentCamera.CFrame.LookVector * _G.strength
									game:GetService("Debris"):AddItem(velocityObj, 1)
								else
									velocityObj:Destroy()
								end
							end
						end)
					end
				end
			end)
		elseif strengthConnection then
			strengthConnection:Disconnect()
		end
	end
})
local killGrabEnabled = false
local function killGrabFunction()
	workspace.ChildAdded:Connect(function(v)
		if v:IsA("Model") and v.Name == "GrabParts" and killGrabEnabled then
			task.wait(0.05)
			local grabPart = v:FindFirstChild("GrabPart")
			if grabPart and grabPart:FindFirstChild("WeldConstraint") then
				local part1 = grabPart.WeldConstraint.Part1
				if part1 and part1.Parent and part1.Parent ~= Player.Character then
					local targetChar = part1.Parent
					local targetHum = targetChar:FindFirstChildOfClass("Humanoid")
					if targetHum and targetChar then
						pcall(function()
							targetHum.Health = 0
							targetChar:BreakJoints()
						end)
					end
				end
			end
		end
	end)
end
killGrabFunction()
GrabGroup:CreateToggle({
	Name = "Kill Grab",
        Flag = "Kill Grab",
	Default = false,
	Callback = function(Value)
        SetToggleState("Kill Grab", Value)
		killGrabEnabled = Value
	end
})
local PlayerView = Tabs.Player:CreateBlock({Name = "View & Movement", Side = "Left"})
local PlayerESP = Tabs.Player:CreateBlock({Name = "ESP", Side = "Right"})
local PlayerPerf = Tabs.Player:CreateBlock({Name = "Performance", Side = "Right"})
local function enableThirdPerson()
	Player.CameraMode = Enum.CameraMode.Classic
	Camera.CameraType = Enum.CameraType.Custom
	Camera.CameraSubject = Player.Character:WaitForChild("Humanoid")
	Player.CameraMaxZoomDistance = 16456456546
	Player.CameraMinZoomDistance = 0.5
end
local function disableThirdPerson()
	Player.CameraMode = Enum.CameraMode.LockFirstPerson
	Camera.CameraType = Enum.CameraType.Custom
	Camera.CameraSubject = Player.Character:WaitForChild("Humanoid")
	Player.CameraMaxZoomDistance = 0
	Player.CameraMinZoomDistance = 0
end
PlayerView:CreateToggle({
	Name = "3rd Person View",
        Flag = "3rd Person View",
	Default = false,
	Callback = function(Value)
        SetToggleState("3rd Person View", Value)
		if Value then
			enableThirdPerson()
		else
			disableThirdPerson()
		end
	end
})
local spinningConnection
local spinSpeed = 5
PlayerView:CreateToggle({
	Name = "Spin Character",
        Flag = "Spin Character",
	Default = false,
	Callback = function(Value)
        SetToggleState("Spin Character", Value)
		if Value then
			spinningConnection = R.Heartbeat:Connect(function()
				local character = Player.Character
				local root = character and character:FindFirstChild("HumanoidRootPart")
				if root then
					root.CFrame = root.CFrame * CFrame.Angles(0, math.rad(spinSpeed), 0)
				end
			end)
		else
			if spinningConnection then
				spinningConnection:Disconnect()
				spinningConnection = nil
			end
		end
	end
})
PlayerView:CreateSlider({
	Name = "Spin Speed",
        Flag = "Spin Speed",
	Default = 5,
	Min = 1,
	Max = 50,
	Rounding = 0,
	Callback = function(Value)
		spinSpeed = Value
	end
})



local PL_SpeedEnabled = false
local PL_SpeedValue   = 16
local PL_SpeedConn    = nil

-- CFrame-based walkspeed (Fire Hub / Posral pattern)
PlayerView:CreateInput({
    Name = "Walk Speed",
        Flag = "Walk Speed",
    Default     = "16",
    Placeholder = "e.g. 16",
    Numeric     = true,
    Callback    = function(v)
        local n = tonumber(v)
        if n then PL_SpeedValue = n end
    end,
})

PlayerView:CreateToggle({
    Name = "Enable Speed (CFrame)",
        Flag = "Enable Speed (CFrame)",
    Default = false,
    Callback = function(Value)
        SetToggleState("Enable Speed (CFrame)", Value)
        PL_SpeedEnabled = Value
        if Value then
            if PL_SpeedConn then PL_SpeedConn:Disconnect() end
            PL_SpeedConn = RunService.RenderStepped:Connect(function()
                if not PL_SpeedEnabled then return end
                local char = LocalPlayer.Character
                local hrp  = char and char:FindFirstChild("HumanoidRootPart")
                local hum  = char and char:FindFirstChild("Humanoid")
                if hrp and hum then
                    hrp.CFrame = hrp.CFrame + hum.MoveDirection * (PL_SpeedValue * 0.1)
                end
            end)
        else
            if PL_SpeedConn then
                PL_SpeedConn:Disconnect()
                PL_SpeedConn = nil
            end
        end
    end,
})
PlayerView:CreateToggle({
    Name = "Water Walk",
        Flag = "Water Walk",
    Default = false,
    Callback = function(v)
        SetToggleState("Water Walk", Value)
        for i,vv in pairs(workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel:GetChildren()) do
            if vv.Name == "Ocean" then
                vv.CanCollide = v
            end
        end
    end
})
UserInputService.JumpRequest:Connect(function()
	if infJump then
		local character = Player.Character
		if character and character:FindFirstChildOfClass("Humanoid") then
			character:FindFirstChildOfClass("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)
		end
	end
end)
local espEnabled = false
local espBoxes = {}
local targetNames = {
	"partesp",
	"playercharacterlocationdetector"
}
local function IsTarget(obj)
	if not obj:IsA("BasePart") then
		return false
	end
	for _, name in ipairs(targetNames) do
		if string.lower(obj.Name) == string.lower(name) then
			return true
		end
	end
	return false
end
function AddBoxESP(obj)
	if espBoxes[obj] then
		return
	end
	local box = Instance.new("BoxHandleAdornment")
	box.Adornee = obj
	box.AlwaysOnTop = true
	box.ZIndex = 5
	box.Color3 = Color3.fromRGB(255, 255, 255)
	box.Transparency = 0.5
	box.Size = obj.Size
	box.Parent = game.CoreGui
	espBoxes[obj] = box
	obj.AncestryChanged:Connect(function(_, parent)
		if not parent and espBoxes[obj] then
			espBoxes[obj]:Destroy()
			espBoxes[obj] = nil
		end
	end)
end
function RemoveAllBoxes()
	for obj, box in pairs(espBoxes) do
		if box then
			box:Destroy()
		end
	end
	espBoxes = {}
end
function Scan()
	for _, obj in ipairs(workspace:GetDescendants()) do
		if espEnabled and IsTarget(obj) then
			AddBoxESP(obj)
		end
	end
end
workspace.DescendantAdded:Connect(function(obj)
	if espEnabled and IsTarget(obj) then
		AddBoxESP(obj)
	end
end)
PlayerESP:CreateToggle({
	Name = "PCLD View",
        Flag = "PCLD View",
	Default = false,
	Callback = function(Value)
        SetToggleState("PCLD View", Value)
		espEnabled = Value
		if espEnabled then
			Scan()
		else
			RemoveAllBoxes()
		end
	end
})
PlayerESP:CreateToggle({
    Name = "Nickname Esp",
    Flag = "Nickname Esp",
    Default = false,
    Callback = function(Value)
        SetToggleState("Nickname Esp", Value)
        
        function createESP(plr)
            -- Don't put ESP on yourself
            if plr == Player then return end
            
            -- Wait for character and HRP to exist
            local char = plr.Character or plr.CharacterAdded:Wait()
            local hrp = char:WaitForChild("HumanoidRootPart", 5)
            
            if hrp then
                -- Remove existing one to prevent duplicates
                if hrp:FindFirstChild("NameESP") then
                    hrp.NameESP:Destroy()
                end

                local billboard = Instance.new("BillboardGui")
                billboard.Name = "NameESP"
                billboard.Adornee = hrp
                billboard.Size = UDim2.new(0, 100, 0, 30)
                billboard.StudsOffset = Vector3.new(0, 3, 0)
                billboard.AlwaysOnTop = true
                billboard.Parent = hrp

                local textLabel = Instance.new("TextLabel")
                textLabel.Size = UDim2.new(1, 0, 1, 0)
                textLabel.BackgroundTransparency = 1
                textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
                textLabel.TextStrokeTransparency = 0
                textLabel.TextScaled = true
                
                -- THE FIX: Assign the actual text here
                textLabel.Text = plr.DisplayName -- Shows their Nickname/Display Name
                
                textLabel.Parent = billboard
            end
        end

        if Value then
            -- Run for current players
            for _, plr in pairs(PS:GetPlayers()) do
                createESP(plr)
                -- Update ESP when they respawn
                plr.CharacterAdded:Connect(function()
                    if GetToggleState("Nickname Esp") then
                        task.wait(0.5) -- Small wait for character to load fully
                        createESP(plr)
                    end
                end)
            end
            
            -- Run for players who join later
            PS.PlayerAdded:Connect(function(plr)
                plr.CharacterAdded:Connect(function()
                    if GetToggleState("Nickname Esp") then
                        task.wait(0.5)
                        createESP(plr)
                    end
                end)
            end)
        else
            -- Clean up all ESP when toggled off
            for _, plr in pairs(PS:GetPlayers()) do
                if plr.Character then
                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                    if hrp and hrp:FindFirstChild("NameESP") then
                        hrp.NameESP:Destroy()
                    end
                end
            end
        end
    end
})
local oldProperties = {}
PlayerPerf:CreateButton({
	Name = "boost fps",
        Flag = "boost fps",
	Callback = function()
		local Lighting = game:GetService("Lighting")
		for _, v in pairs(Workspace:GetDescendants()) do
			if v:IsA("BasePart") then
				if not oldProperties[v] then
					oldProperties[v] = {
						Material = v.Material,
						Reflectance = v.Reflectance,
						CastShadow = v.CastShadow
					}
				end
				v.Material = Enum.Material.Plastic
				v.Reflectance = 0
				v.CastShadow = false
			elseif v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Smoke") or v:IsA("Fire") then
				if not oldProperties[v] then
					oldProperties[v] = {
						Enabled = v.Enabled
					}
				end
				v.Enabled = false
			end
		end
		for _, plr in pairs(PS:GetPlayers()) do
			if plr.Character then
				for _, part in pairs(plr.Character:GetDescendants()) do
					if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
						if not oldProperties[part] then
							oldProperties[part] = {
								Material = part.Material,
								Reflectance = part.Reflectance,
								CastShadow = part.CastShadow
							}
						end
						part.Material = Enum.Material.Plastic
						part.Reflectance = 0
						part.CastShadow = false
					end
				end
			end
		end
		if not oldProperties["Lighting"] then
			oldProperties["Lighting"] = {
				GlobalShadows = Lighting.GlobalShadows,
				FogEnd = Lighting.FogEnd,
				Brightness = Lighting.Brightness
			}
		end
		Lighting.GlobalShadows = false
		Lighting.FogEnd = 100000
		Lighting.Brightness = 2
	end
})
PlayerPerf:CreateButton({
	Name = "delete boost fps",
        Flag = "delete boost fps",
	Callback = function()
		local Lighting = game:GetService("Lighting")
		for obj, props in pairs(oldProperties) do
			if typeof(obj) == "Instance" and obj.Parent then
				for prop, value in pairs(props) do
					obj[prop] = value
				end
			elseif obj == "Lighting" then
				for prop, value in pairs(props) do
					Lighting[prop] = value
				end
			end
		end
		oldProperties = {}
	end
})

-- =========================================================================
-- PACKET DETECTOR (INTEGRATED FOR SCRIPT 6)
-- =========================================================================
local KB_THRESHOLD = 5
local BYTE_THRESHOLD = KB_THRESHOLD * 1024
local COOLDOWN = 30
local lastNotify = 0
local packetConnections = {}

-- Utility Functions using Script 6 UI's notification style
local function packetNotify(title, text)
    pcall(function()
        Library:Notify({
            Title = title or "Packet Detected",
            Content = text or "",
            Duration = 7
        })
    end)
end
-- =========================================================================
-- PACKET DETECTOR (INTEGRATED INTO MISCGROUP)
-- =========================================================================
local MiscGroup = Tabs.Misc:CreateBlock({Name = "Packet Detector", Side = "Left"})
-- =========================================================================
-- PACKET DETECTOR (LOCALIZED & ISOLATED)
-- =========================================================================
do
    local Players = game:GetService("Players")
    local LocalPlayer = Players.LocalPlayer
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local RunService = game:GetService("RunService")

    local MiscGroup = Tabs.Misc:CreateBlock({Name = "Packet Detector", Side = "Left"})

    local packetConnections = {}
    local lastNotify = 0
    local COOLDOWN = 0.5 -- 0.5-second stream buffer to prevent notification stacking

    local function resolveSender(args)
        for _, v in ipairs(args) do
            if typeof(v) == "Instance" then
                if v:IsA("Player") then
                    return v
                end
                local model = v:IsA("Model") and v or v:FindFirstAncestorOfClass("Model")
                if model then
                    local plr = Players:GetPlayerFromCharacter(model)
                    if plr then return plr end
                end
            end
        end
        return LocalPlayer
    end

    local function shortenString(str)
        if #str <= 80 then return str end
        return str:sub(1, 80) .. "... (+" .. tostring(#str - 80) .. " chars)"
    end

    local function summarizeTable(tbl)
        local preview = {}
        local count = 0
        for _, v in pairs(tbl) do
            count = count + 1
            if count <= 5 then
                local s, val = pcall(tostring, v)
                table.insert(preview, s and val or "unknown")
            end
        end
        return "table[" .. count .. "] { " .. table.concat(preview, ", ") .. (count > 5 and " ... }" or " }")
    end

    local function compressArgs(args)
        local seen = {}
        local summary = {}

        for _, v in ipairs(args) do
            local key
            if typeof(v) == "string" then
                key = "str:" .. shortenString(v)
            elseif typeof(v) == "Instance" then
                local className = "Unknown"
                local name = "Unknown"
                pcall(function() className = v.ClassName end)
                pcall(function() name = v.Name end)
                key = "inst:" .. className .. "(" .. name .. ")"
            elseif typeof(v) == "table" then
                key = "tbl:" .. summarizeTable(v)
            else
                local success, strVal = pcall(tostring, v)
                key = typeof(v) .. ":" .. (success and strVal or "unprintable")
            end
            seen[key] = (seen[key] or 0) + 1
        end

        for k, count in pairs(seen) do
            if count > 1 then
                table.insert(summary, k .. " x" .. count)
            else
                table.insert(summary, k)
            end
        end

        return summary
    end

    local function handlePacketEvent(eventType, remoteName, ...)
        local args = {...}
        local totalBytes = 0
        
        for _, v in ipairs(args) do
            if typeof(v) == "string" then
                totalBytes = totalBytes + #v
            end
        end

        if tick() - lastNotify < COOLDOWN then return end
        lastNotify = tick()

        local sender = resolveSender(args)
        local senderName = "Unknown"
        if sender then
            local s, name = pcall(function() return sender.DisplayName or sender.Name end)
            if s then senderName = name end
            if sender == LocalPlayer then
                senderName = senderName .. " (You)"
            end
        end

        local mbSize = totalBytes / (1024 * 1024)
        local summarized = compressArgs(args)
        local argsStr = #summarized > 0 and table.concat(summarized, "\n") or "None"

        -- Uses the main library directly to guarantee notification delivery
        Library:Notify({
            Title = string.format("[%s] %s", eventType, remoteName),
            Content = string.format("Player: %s\nSize: %.4f MB\nArgs:\n%s", senderName, mbSize, argsStr),
            Duration = 6
        })
    end

    local function checkAndHookBlobRemote(child)
        if child:IsA("RemoteEvent") and child.Name == "RelayClientAnimation" then
            local parent = child.Parent
            if parent and parent.Name == "BlobmanAnimations" then
                local grandParentName = parent.Parent and parent.Parent.Name or "Unknown"
                table.insert(packetConnections, child.OnClientEvent:Connect(function(...) 
                    handlePacketEvent("Blob", grandParentName, ...) 
                end))
            end
        end
    end

    local function startPacketDetector()
        if #packetConnections > 0 then return end

        task.spawn(function()
            local grabEvents = ReplicatedStorage:WaitForChild("GrabEvents", 5)
            if grabEvents then
                local grabRemote = grabEvents:WaitForChild("ExtendGrabLine", 5)
                if grabRemote then
                    table.insert(packetConnections, grabRemote.OnClientEvent:Connect(function(...) 
                        handlePacketEvent("Grab", "ExtendGrabLine", ...) 
                    end))
                end
            end
        end)

        for _, child in ipairs(workspace:GetDescendants()) do
            checkAndHookBlobRemote(child)
        end

        table.insert(packetConnections, workspace.DescendantAdded:Connect(checkAndHookBlobRemote))
    end

    local function stopPacketDetector()
        for _, conn in ipairs(packetConnections) do
            if typeof(conn) == "RBXScriptConnection" then
                conn:Disconnect()
            end
        end
        table.clear(packetConnections)
    end

    MiscGroup:CreateToggle({
        Name = "Detect packets",
        Flag = "GrabRemoteDetector",
        Default = false,
        Callback = function(Value)
            SetToggleState("GrabRemoteDetector", Value)
            if Value then
                startPacketDetector()
                Library:Notify({ Title = "Enabled", Content = "Packet detector active", Duration = 4 })
            else
                stopPacketDetector()
                Library:Notify({ Title = "Disabled", Content = "Packet detector off", Duration = 4 })
            end
        end
    })
end
do
    -- =========================================================================
    -- PLOT BARRIER BYPASS (MISCGROUP)
    -- =========================================================================

    local function disablePlotBarriers()
        local plotsFolder = Workspace:FindFirstChild("Plots")
        if not plotsFolder then
            notify("Error", "Plots folder not found!", 3)
            return
        end
        
        local barrierCount = 0
        
        for _, plot in ipairs(plotsFolder:GetDescendants()) do
            if plot:IsA("BasePart") and plot.Name == "PlotBarrier" then
                plot.CanCollide = false
                plot.CanTouch = false
                plot.CanQuery = false
                barrierCount = barrierCount + 1
            end
        end
        
        notify("Success", barrierCount .. " plot barriers disabled.", 3)
    end

    local function hamburgerTeleport()
        local char = Player.Character or Player.CharacterAdded:Wait()
        local hrp = char:FindFirstChild("HumanoidRootPart")
        
        if not hrp then
            notify("Error", "HumanoidRootPart not found!", 3)
            return
        end

        local originalCFrame = hrp.CFrame
        local MenuToys = RS:FindFirstChild("MenuToys")
        local SpawnToyRemoteFunction = MenuToys and MenuToys:FindFirstChild("SpawnToyRemoteFunction")
        
        if not SpawnToyRemoteFunction then
            notify("Error", "SpawnToyRemoteFunction not found!", 3)
            return
        end

        -- Spawn Hamburger
        SpawnToyRemoteFunction:InvokeServer("FoodHamburger", hrp.CFrame, Vector3.zero)

        local spawnedToys = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
        if not spawnedToys then
            notify("Error", "SpawnedInToys folder not found!", 3)
            return
        end

        local FoodHamburger = spawnedToys:WaitForChild("FoodHamburger", 5)
        if not FoodHamburger then
            notify("Error", "Hamburger object not found!", 3)
            return
        end

        local HoldPart = FoodHamburger:FindFirstChild("HoldPart")
        local HoldItemRemoteFunction = HoldPart and HoldPart:FindFirstChild("HoldItemRemoteFunction")
        
        if not HoldItemRemoteFunction then
            notify("Error", "HoldItemRemoteFunction not found!", 3)
            return
        end

        -- Equip the Hamburger
        HoldItemRemoteFunction:InvokeServer(FoodHamburger, char)

        local plotsFolder = Workspace:FindFirstChild("Plots")
        local plot3 = plotsFolder and plotsFolder:FindFirstChild("Plot3")
        local plotArea = plot3 and plot3:FindFirstChild("PlotArea")
        
        if not plotArea then
            notify("Error", "Plot area not found!", 3)
            return
        end

        -- Teleport sequence to bypass
        hrp.CFrame = plotArea.CFrame
        task.wait(0.1)
        hrp.CFrame = originalCFrame

        -- Cleanup
        local DestroyToy = MenuToys and MenuToys:FindFirstChild("DestroyToy")
        if DestroyToy then
            DestroyToy:FireServer(FoodHamburger)
        end

        notify("Success", "Teleport loop executed!", 3)
    end

    MiscGroup:CreateButton({
        Name = "Disable Plot Barriers",
        Callback = function()
            notify("System", "Attempting to break barriers...", 2)
            hamburgerTeleport()
            task.wait(0.5)
            disablePlotBarriers()
        end,
    })
end

MiscGroup:CreateToggle({
	Name = "Dreamy Night Shader",
        Flag = "Dreamy Night Shader",
	Default = false,
	Callback = function(on)
        SetToggleState("Dreamy Night Shader", on)
		local Lighting = game:GetService("Lighting")
		if not _G.DreamyNightEffects then
			_G.DreamyNightEffects = {}

            -- РЎРР›Р¬РќР«Р™ Blur (РіР»Р°РІРЅРѕРµ!)
			local Blur = Instance.new("BlurEffect")
			Blur.Size = 6
			Blur.Enabled = false
			Blur.Parent = Lighting

            -- Glow / Bloom (Р·РІС‘Р·РґС‹ Рё СЃРІРµС‚)
			local Bloom = Instance.new("BloomEffect")
			Bloom.Intensity = 1.6
			Bloom.Size = 90
			Bloom.Threshold = 1.4
			Bloom.Enabled = false
			Bloom.Parent = Lighting

            -- ColorCorrection (РЅРѕС‡СЊ + РјСЏРіРєРѕСЃС‚СЊ)
			local Color = Instance.new("ColorCorrectionEffect")
			Color.Brightness = 0.15
			Color.Contrast = -0.1
			Color.Saturation = 0.25
			Color.TintColor = Color3.fromRGB(210, 220, 255)
			Color.Enabled = false
			Color.Parent = Lighting

            -- SunRays (Р»С‘РіРєРѕРµ СЃРІРµС‡РµРЅРёРµ)
			local SunRays = Instance.new("SunRaysEffect")
			SunRays.Intensity = 0.05
			SunRays.Spread = 0.6
			SunRays.Enabled = false
			SunRays.Parent = Lighting

            -- Atmosphere (Р·РІС‘Р·РґРЅРѕРµ РЅРµР±Рѕ + haze)
			local Atmosphere = Instance.new("Atmosphere")
			Atmosphere.Density = 0.45
			Atmosphere.Offset = 0.1
			Atmosphere.Color = Color3.fromRGB(180, 190, 255)
			Atmosphere.Decay = Color3.fromRGB(120, 130, 180)
			Atmosphere.Glare = 0.15
			Atmosphere.Haze = 3
			Atmosphere.Enabled = false
			Atmosphere.Parent = Lighting
			_G.DreamyNightEffects = {
				Blur,
				Bloom,
				Color,
				SunRays,
				Atmosphere
			}
		end
		for _, effect in ipairs(_G.DreamyNightEffects) do
			effect.Enabled = on
		end
		if on then
			Lighting.ClockTime = 0.5
			Lighting.GlobalShadows = false
			Lighting.Brightness = 2
			Lighting.EnvironmentDiffuseScale = 0.2
			Lighting.EnvironmentSpecularScale = 0.1
			Lighting.FogEnd = 200000
		end
	end
})

local Triggerbot = {
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
	lastCheck = 0
}
local rayParams = RaycastParams.new()
rayParams.FilterType = Enum.RaycastFilterType.Exclude
task.spawn(function()
	local success, result = pcall(function()
		return RS.GamepassEvents.CheckForGamepass:InvokeServer(20837132)
	end)
	if success and result then
		Triggerbot.maxDistance = 29.3
	end
end)
if RS:FindFirstChild("GamepassEvents") and RS.GamepassEvents:FindFirstChild("FurtherReachBoughtNotifier") then
	RS.GamepassEvents.FurtherReachBoughtNotifier.OnClientEvent:Connect(function()
		Triggerbot.maxDistance = 29.3
	end)
end
function Triggerbot:GetTarget()
	local c = Player.Character
	if not c or not c:FindFirstChild("HumanoidRootPart") then
		return
	end
	if Workspace:FindFirstChild("GrabParts") then
		return
	end
	local origin, dir = Camera.CFrame.Position, Camera.CFrame.LookVector
	rayParams.FilterDescendantsInstances = {
		c,
		Workspace.Terrain
	}
	local result = Workspace:Raycast(origin, dir * 1000, rayParams)
	if not result then
		local dirs = {
			dir,
			(dir + Vector3.new(0, 0.075, 0)).Unit,
			(dir - Vector3.new(0, 0.075, 0)).Unit
		}
		for _, d in ipairs(dirs) do
			result = Workspace:Raycast(origin, d * 1000, rayParams)
			if result then
				break
			end
		end
	end
	if not result then
		return
	end
	local hit = result.Instance
	local model = hit:FindFirstAncestorOfClass("Model")
	if not model or not model:FindFirstChildOfClass("Humanoid") or model == c then
		return
	end
	local hum = model:FindFirstChildOfClass("Humanoid")
	if hum.Health <= 0 then
		return
	end
	local root = model:FindFirstChild("HumanoidRootPart")
	if not root then
		return
	end
	local dist = (c.HumanoidRootPart.Position - root.Position).Magnitude
	if dist > self.maxDistance then
		return
	end
	return model
end
function Triggerbot:OnHeartbeat()
	if not self.Enabled or not self.canGrab then
		return
	end
	if UserInputService:GetFocusedTextBox() then
		return
	end
	if tick() - self.lastCheck < self.checkThrottle then
		return
	end
	self.lastCheck = tick()
	local t = self:GetTarget()
	if t then
		self.lastTarget = t
		self.lastHitTime = tick()
	elseif self.lastTarget and tick() - self.lastHitTime > self.targetMemoryDuration then
		self.lastTarget = nil
	end
	local c = Player.Character
	local root = self.lastTarget and self.lastTarget:FindFirstChild("HumanoidRootPart")
	if not (self.lastTarget and c and c:FindFirstChild("HumanoidRootPart") and root) then
		return
	end
	if (c.HumanoidRootPart.Position - root.Position).Magnitude > self.maxDistance then
		self.lastTarget = nil
		return
	end
	if self.lastTarget then
		self.canGrab = false
		task.spawn(function()
			task.wait(self.preGrabDelay)
			pcall(mouse1press)
			local t0 = tick()
			repeat
				task.wait(0.02)
			until not Workspace:FindFirstChild("GrabParts") or tick() - t0 > 1.6
			task.wait(self.postGrabDelay)
			self.canGrab = true
			self.lastTarget = nil
		end)
	end
end

MiscGroup:CreateToggle({
	Name = "Ignore House Barriers",
        Flag = "Ignore House Barriers",
	Default = false,
	Callback = function(Value)
        SetToggleState("Ignore House Barriers", Value)
		local plots = workspace:FindFirstChild("Plots")
		if not plots then
			return
		end
		for _, plot in ipairs(plots:GetChildren()) do
			local barrier = plot:FindFirstChild("Barrier")
			if barrier then
				for _, obj in ipairs(barrier:GetDescendants()) do
					if obj:IsA("BasePart") then
						obj.CanCollide = not Value
					end
				end
			end
		end
	end
})
do
    -- Monster Lag Toggle Logic
    _G.MonsterLagEnabled = false -- Initialize global state

    MiscGroup:CreateToggle({
        Name = "Monster Lag",
        Flag = "MonsterLagToggle",
        Default = false,
        Callback = function(Value)
            _G.MonsterLagEnabled = Value
            
            if Value then
                -- Start the lag loop
                task.spawn(function()
                    local RepS = game:GetService("ReplicatedStorage")
                    local WS = game:GetService("Workspace")
                    local LP = game:GetService("Players").LocalPlayer
                    local GrabEvents = RepS:FindFirstChild("GrabEvents")
                    local CreateLine = GrabEvents and GrabEvents:FindFirstChild("CreateGrabLine")

                    while _G.MonsterLagEnabled and CreateLine do
                        local spawnLocation = WS:FindFirstChild("SpawnLocation") 
                            or WS:FindFirstChild("Spawn") 
                            or (LP.Character and LP.Character:FindFirstChild("HumanoidRootPart"))

                        if spawnLocation then
                            local randomX = math.random(-9e9, 9e9)
                            local randomZ = math.random(-9e9, 9e9)
                            CreateLine:FireServer(spawnLocation, CFrame.new(randomX, 0, randomZ))
                        end
                        task.wait() -- Adjust this to task.wait(0.01) if you want it faster
                    end
                end)
                Library:Notify({ Title = "Monster Lag", Content = "Enabled", Duration = 2 })
            else
                Library:Notify({ Title = "Monster Lag", Content = "Disabled", Duration = 2 })
            end
        end
    })
end
MiscGroup:CreateSlider({
    Name     = "Packet Amount",
    Flag     = "PacketSpamAmount",
    Default  = 900,
    Min      = 10,
    Max      = 5000,
    Rounding = 0,
    Callback = function(v)
        PacketSpamAmount = v
    end
})

MiscGroup:CreateToggle({
    Name     = "Enable Packet Lag",
    Flag     = "PacketLagActive",
    Default  = false,
    Callback = function(Value)
        SetToggleState("PacketLagActive", Value)
        _G.PacketLagActive = Value
        
        if Value then
            task.spawn(function()
                -- Safety check for specific player
                for _, p in ipairs(PS:GetPlayers()) do
                    if p.Name == "MaybeFlashh" then
                        notify("Safety Alert", "MaybeFlashh detected. Packet lag disabled for safety.", 5)
                        _G.PacketLagActive = false
                        return
                    end
                end
                
                local GrabEvent = RS:WaitForChild("GrabEvents", 5):WaitForChild("ExtendGrabLine", 5)
                if not GrabEvent then 
                    notify("Error", "Grab event not found.", 3)
                    return 
                end

                while _G.PacketLagActive do
                    pcall(function()
                        -- Sending large strings via ExtendGrabLine to stress the server/client
                        GrabEvent:FireServer(string.rep("Genesis Was Here", PacketSpamAmount))
                    end)
                    task.wait()
                end
            end)
        end
    end
})
MiscGroup:CreateToggle({
	Name = "Auto Reset",
        Flag = "Auto Reset",
	Default = false,
	Callback = function(on)
        SetToggleState("Auto Reset", on)
		autoResetEnabled = on
		if not on then
			autoResetEnabled = false
			return
		end
		task.spawn(function()
			local plr = game.Players.LocalPlayer
			while autoResetEnabled do
				local char = plr.Character
				local hum = char and char:FindFirstChild("Humanoid")
				if hum and hum.Health > 0 then
					hum.Health = 0
				end
				task.wait(0.5)
			end
		end)
	end
})

MiscGroup:CreateToggle({
	Name = "Trigger Bot",
        Flag = "Trigger Bot",
	Default = Triggerbot.Enabled,
	Callback = function(value)
        SetToggleState("Trigger Bot", Value)
		Triggerbot.Enabled = value
		if Triggerbot.Enabled and not Triggerbot.Connection then
			Triggerbot.Connection = R.Heartbeat:Connect(function()
				Triggerbot:OnHeartbeat()
			end)
		elseif not Triggerbot.Enabled and Triggerbot.Connection then
			Triggerbot.Connection:Disconnect()
			Triggerbot.Connection = nil
		end
	end
})
MiscGroup:CreateSlider({
	Name = "FOV",
        Flag = "FOV",
	Default = 90,
	Min = 1,
	Max = 120,
	Rounding = 0,
	Suffix = "В°",
	Callback = function(value)
		game.Workspace.CurrentCamera.FieldOfView = value
	end
})
PS.PlayerAdded:Connect(function(plr)
	if plr:IsFriendsWith(Player.UserId) then
		notify("Notify friend", plr.Name .. " joined", 5)
	end
end)
do
local Players = game:GetService("Players")
local variants = {
	"BlackHole",
	"Black_Hole",
	"Blackhole",
	"Black-Hole",
	"BHole",
	"BH",
	"VoidHole",
	"Void",
	"VoidSphere",
	"DarkHole",
	"DarkSphere",
	"DarkOrb",
	"GravityHole",
	"GravityOrb",
	"SpaceHole",
	"SpaceOrb",
	"Singularity",
	"SingularityOrb",
	"EventHorizon",
	"BlackSphere",
	"Anomaly",
	"AnomalyHole",
	"SupermassiveHole",
	"QuantumHole"
}

-- ===============================
-- RAGALIC CLIENT вЂў KICK NOTIFY
-- ===============================

local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local SoundService = game:GetService("SoundService")

local LocalPlayer = Players.LocalPlayer

-- ===============================
-- SOUND (BELL)
-- ===============================
function playKickSound()
	local s = Instance.new("Sound")
	s.SoundId = "rbxassetid://79150789336480" -- Bell (Deltarune)
	s.Volume = 5
	s.PlayOnRemove = true
	s.Parent = SoundService
	s:Destroy()
end

-- ===============================
-- NOTIFY (XOCU)
-- ===============================
function notifyKick(displayName, username)
	Library:Notify({ Title = "XOCU ", Content = displayName .. " (" .. username .. ") has been kicked", Duration = 6,
	 })
end

-- ===============================
-- HELPERS
-- ===============================
function getClosestPlayer(pos)
	local closestPlr = nil
	local closestDist = math.huge
	for _, plr in ipairs(Players:GetPlayers()) do
		if plr ~= LocalPlayer and plr.Character then
			local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
			if hrp then
				local dist = (hrp.Position - pos).Magnitude
				if dist < closestDist then
					closestDist = dist
					closestPlr = plr
				end
			end
		end
	end
	return closestPlr
end

-- ===============================
-- BLACK HOLE DETECT
-- ===============================
Workspace.ChildAdded:Connect(function(obj)
	if obj.Name == "BlackHoleKick" or obj.Name == "BlackHoleDetected" then
		task.wait(0.05)
		local pos
		if obj:IsA("BasePart") then
			pos = obj.Position
		elseif obj:IsA("Model") and obj.PrimaryPart then
			pos = obj.PrimaryPart.Position
		end
		if not pos then
			return
		end
		local plr = getClosestPlayer(pos)
		if not plr then
			return
		end
		playKickSound()
		notifyKick(plr.DisplayName, plr.Name)
	end
end)
end

do
local FanGroup = Tabs.Misc:CreateBlock({Name = "Troll", Side = "Left"})
-- ===========================
-- Toggle "Jerk Off" (Fan в†’ Troll)
-- ===========================

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local playJerkOffActive = false
local jerkOffAnimTrack = nil
local jerkOffAnimId = "rbxassetid://168268306" -- Р°РЅРёРјР°С†РёСЏ
local selectedKey = Enum.KeyCode.Q -- РєР»Р°РІРёС€Р° РїРѕ СѓРјРѕР»С‡Р°РЅРёСЋ

-- в–¶ Р·Р°РїСѓСЃРє Р°РЅРёРјР°С†РёРё
function startJerkOff()
	local plr = Players.LocalPlayer
	local char = plr.Character or plr.CharacterAdded:Wait()
	local hum = char:FindFirstChildOfClass("Humanoid")
	if not hum then
		return
	end
	local animator = hum:FindFirstChildOfClass("Animator")
	if not animator then
		animator = Instance.new("Animator")
		animator.Parent = hum
	end
	local anim = Instance.new("Animation")
	anim.AnimationId = jerkOffAnimId
	jerkOffAnimTrack = animator:LoadAnimation(anim)
	jerkOffAnimTrack.Priority = Enum.AnimationPriority.Action
	jerkOffAnimTrack:Play()
	task.spawn(function()
		while playJerkOffActive do
			task.wait(0.1)
			if jerkOffAnimTrack and jerkOffAnimTrack.IsPlaying then
				jerkOffAnimTrack.TimePosition = 0.3
			end
		end
	end)
end

-- вЏ№ РѕСЃС‚Р°РЅРѕРІРєР°
function stopJerkOff()
	if jerkOffAnimTrack then
		jerkOffAnimTrack:Stop()
		jerkOffAnimTrack = nil
	end
end
FanGroup:CreateToggle({
    Name = "Coconut Penis",  
    Default = false,
    Flag = "CoconutOrbit",
    Callback = function(Value)
        _G.En = Value
        
        if Value then
            task.spawn(function()
                local Me = game.Players.LocalPlayer
                local RunService = game:GetService("RunService")
                local ReplicatedStorage = game:GetService("ReplicatedStorage")
                
                local SetNetworkOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner
                local SpawnToy = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
                local DestroyToy = ReplicatedStorage.MenuToys.DestroyToy
                
                local Offsets = {
                    [1] = CFrame.new(-0.45, -1.2, -0.7),
                    [2] = CFrame.new(0.45, -1.2, -0.7),
                    [3] = CFrame.new(0, -1, 0.8)
                }
                
                local Length = 10
                local Coconuts = {}
                
                while _G.En do
                    local Char = Me.Character
                    local Root = Char and Char:FindFirstChild("HumanoidRootPart")
                    
                    if not Root then 
                      task.wait(0.1)
                      continue 
                    end
                    
                    Coconuts = {}
                    for _, toy in pairs(workspace[Me.Name.."SpawnedInToys"]:GetChildren()) do
                        if toy.Name == "FoodCoconut" then
                            table.insert(Coconuts, toy)
                        end
                    end
                    
                    if #Coconuts < (Length + 2) then
                        task.spawn(function()
                            SpawnToy:InvokeServer("FoodCoconut", Root.CFrame * CFrame.new(-5, 0, 10), Vector3.zero)
                        end)
                    end
                    
                    for i, Coco in ipairs(Coconuts) do
                        local Part = Coco:FindFirstChild("SoundPart")
                        local HoldPart = Coco:FindFirstChild("HoldPart")
                        local Rigid = HoldPart and HoldPart:FindFirstChild("RigidConstraint")
                        local PartOwner = Part and Part:FindFirstChild("PartOwner")
                        
                        if Part and HoldPart and Rigid then
                            if PartOwner and PartOwner.Value == Me.Name then
                                if i <= 2 then
                                    Part.CFrame = Root.CFrame * Offsets[i] * CFrame.new(Root.Velocity / 100)
                                else
                                    Part.CFrame = Root.CFrame * Offsets[3] * CFrame.new(Root.Velocity / 100) * CFrame.new(0, 0, Offsets[3].Z - (i + 0.2))
                                end
                                Part.Velocity = Vector3.zero
                            end
                            
                            if not PartOwner or PartOwner.Value ~= Me.Name then
                                SetNetworkOwner:FireServer(Part, Part.CFrame)
                            end
                            
                            if Rigid.Attachment1 then
                                DestroyToy:FireServer(Coco)
                            end
                            
                            for _, part in pairs(Coco:GetChildren()) do
                                if part:IsA("BasePart") then
                                    part.CanCollide = false
                                    part.CanQuery = false
                                    if part.Transparency ~= 1 then
                                        part.Transparency = 0
                                    end
                                end
                            end
                        end
                    end
                    
                    task.wait(0.01)
                end
            end)
        end
    end
})
-- рџ” Toggle РІ Fan в†’ Animations
FanGroup:CreateToggle({
	Name = "Jerk Off",
        Flag = "Jerk Off",
	Default = false,
	Callback = function(on)
        SetToggleState("Jerk Off", on)
		playJerkOffActive = on
		if on then
			startJerkOff()
		else
			stopJerkOff()
		end
	end
})
-- [Fake Headless + Korblox removed]
-- вЊЁпёЏ Dropdown РІС‹Р±РѕСЂР° РєР»Р°РІРёС€Рё
FanGroup:CreateDropdown({
	Name = "Toggle Key",
        Flag = "Toggle Key",
	Items = {
		"Q",
		"E",
		"R",
		"T"
	},
	Default = "Q",
	Callback = function(v)
		selectedKey = Enum.KeyCode[v]
	end
})

-- вЊЁпёЏ РљРµР№Р±РёРЅРґ
UserInputService.InputBegan:Connect(function(input, gp)
	if gp then
		return
	end
	if input.KeyCode == selectedKey then
		playJerkOffActive = not playJerkOffActive
		if playJerkOffActive then
			startJerkOff()
		else
			stopJerkOff()
		end
	end
end)
end
-- Auras Group



-- ===========================
-- РџРµСЂРµРјРµРЅРЅС‹Рµ СЃРѕСЃС‚РѕСЏРЅРёСЏ
-- ===========================
local removeAntiKickAuraActive = false
local removeAntiKickAuraConnection = nil
local removeAntiKickRadius = 15
local useWhitelistRemoveAntiKick = true

-- ===========================
-- Radius Dropdown
-- ===========================
TelekinesisGroup:CreateDropdown({
	Name = "Anti Kick Aura Radius",
        Flag = "Anti Kick Aura Radius",
	Items = {
		"10",
		"12",
		"14",
		"16",
		"18",
		"20"
	},
	Default = "15",
	Callback = function(value)
		removeAntiKickRadius = tonumber(value)
	end
})


-- ===========================
-- Main Aura Toggle
-- ===========================
TelekinesisGroup:CreateToggle({
	Name = "Remove Anti Kick Aura",
        Flag = "Remove Anti Kick Aura",
	Default = false,
	Callback = function(on)
        SetToggleState("Remove Anti Kick Aura", on)
		removeAntiKickAuraActive = on
		if not on then
			if removeAntiKickAuraConnection then
				removeAntiKickAuraConnection:Disconnect()
				removeAntiKickAuraConnection = nil
			end
			return
		end
		task.spawn(function()
			local RS = game:GetService("ReplicatedStorage")
			local Players = game:GetService("Players")
			local RunService = game:GetService("RunService")
			local LocalPlayer = Players.LocalPlayer
			local GrabEvents = RS:WaitForChild("GrabEvents")
			local SetNetOwner = GrabEvents:WaitForChild("SetNetworkOwner")
			removeAntiKickAuraConnection = RunService.Heartbeat:Connect(function()
				local myChar = LocalPlayer.Character
				local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
				if not myRoot then
					return
				end
				for _, target in ipairs(Players:GetPlayers()) do
					if target ~= LocalPlayer then
						local tChar = target.Character
						local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
						if not tRoot then
							continue
						end

                        -- whitelist
						if useWhitelistRemoveAntiKick
                            and LocalPlayer:IsFriendsWith(target.UserId) then
							continue
						end

                        -- radius
						if (tRoot.Position - myRoot.Position).Magnitude <= removeAntiKickRadius then
							local spawned = workspace:FindFirstChild(
                                target.Name .. "SpawnedInToys"
                            )
							if spawned then
								for _, toyName in ipairs({
									"NinjaKunai",
									"NinjaShuriken",
									"AntiKick"
								}) do
									local toy = spawned:FindFirstChild(toyName)
									if toy then
										local part = toy:FindFirstChild("SoundPart")
										if part then
											pcall(function()
												SetNetOwner:FireServer(
                                                    part,
                                                    part.CFrame
                                                )
											end)
											if part:FindFirstChild("PartOwner")
                                                and part.PartOwner.Value == LocalPlayer.Name then
												part.CFrame = CFrame.new(0, 1000, 0)
											end
										end
									end
								end
							end
						end
					end
				end
			end)
		end)
	end
})
local BuildGroup = Tabs.Misc:CreateBlock({Name = "Build", Side = "Right"})

local heartHighRun = false
local heartConnection = nil
local heartToy = nil

BuildGroup:CreateToggle({
	Name = "Heart",
        Flag = "Heart",
	Default = false,
	Callback = function(Value)
        SetToggleState("Heart", Value)
		heartHighRun = Value
		local RS = game:GetService("ReplicatedStorage")
		local RunService = game:GetService("RunService")
		local Players = game:GetService("Players")
		local player = Players.LocalPlayer
		if Value then
			task.spawn(function()
				if not player.Character then
					return
				end
				local hrp = player.Character:FindFirstChild("HumanoidRootPart")
				if not hrp then
					return
				end

                -- spawn sparkler
				pcall(function()
					RS.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                        "FireworkSparkler",
                        hrp.CFrame * CFrame.new(0, 50, 0),
                        Vector3.zero
                    )
				end)
				local folder = workspace:WaitForChild(
                    player.Name .. "SpawnedInToys",
                    5
                )
				if not folder then
					return
				end
				heartToy = folder:WaitForChild("FireworkSparkler", 5)
				if not heartToy then
					return
				end
				local part =
                    heartToy:FindFirstChild("Handle")
                    or heartToy:FindFirstChildWhichIsA("BasePart")
				if not part then
					return
				end
				task.wait(0.2)

                -- cleanup physics
				for _, v in ipairs(heartToy:GetDescendants()) do
					if v:IsA("BasePart") then
						v.Anchored = false
						v.CanCollide = false
						v.Massless = true
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
				local t = 0
				if heartConnection then
					heartConnection:Disconnect()
				end
				heartConnection = RunService.Heartbeat:Connect(function(dt)
					if not heartHighRun then
						if heartConnection then heartConnection:Disconnect() end
						return
					end
					-- If toy is gone, respawn it without stopping
					if not part or not part.Parent then
						if heartToy and heartToy.Parent then
							pcall(function() heartToy:Destroy() end)
						end
						heartToy = nil
						task.spawn(function()
							if not heartHighRun then return end
							local char2 = player.Character
							local hrp2 = char2 and char2:FindFirstChild("HumanoidRootPart")
							if not hrp2 then return end
							pcall(function()
								RS.MenuToys.SpawnToyRemoteFunction:InvokeServer("FireworkSparkler", hrp2.CFrame * CFrame.new(0, 50, 0), Vector3.zero)
							end)
							local folder2 = workspace:FindFirstChild(player.Name .. "SpawnedInToys")
							if not folder2 then return end
							local newToy = folder2:WaitForChild("FireworkSparkler", 5)
							if not newToy then return end
							heartToy = newToy
							local newPart = newToy:FindFirstChild("Handle") or newToy:FindFirstChildWhichIsA("BasePart")
							if not newPart then return end
							for _, v in ipairs(newToy:GetDescendants()) do
								if v:IsA("BasePart") then v.Anchored = false; v.CanCollide = false; v.Massless = true end
							end
							newPart:BreakJoints()
							local bp2 = Instance.new("BodyPosition"); bp2.MaxForce = Vector3.new(math.huge, math.huge, math.huge); bp2.P = 20000; bp2.D = 500; bp2.Parent = newPart
							local bg2 = Instance.new("BodyGyro"); bg2.MaxTorque = Vector3.new(math.huge, math.huge, math.huge); bg2.P = 3000; bg2.CFrame = CFrame.new(); bg2.Parent = newPart
							-- update outer references
							part = newPart
							bp = bp2
							bg = bg2
						end)
						return
					end
					local char = player.Character
					local currentHrp =
                        char and char:FindFirstChild("HumanoidRootPart")
					if not currentHrp then
						return
					end
					pcall(function()
						RS.GrabEvents.SetNetworkOwner:FireServer(
                            part,
                            part.CFrame
                        )
					end)
					t = t + (8 * dt)

                    -- heart math
					local scale = 1.5
					local x = 16 * math.sin(t) ^ 3
					local y =
                        13 * math.cos(t)
                        - 5 * math.cos(2 * t)
                        - 2 * math.cos(3 * t)
                        - math.cos(4 * t)
					local relPos = Vector3.new(
                        x * scale,
                        (y * scale) + 25,
                        3
                    )
					bp.Position =
                        currentHrp.CFrame:PointToWorldSpace(relPos)
					bg.CFrame = currentHrp.CFrame
				end)
			end)
		else
			if heartConnection then
				heartConnection:Disconnect()
				heartConnection = nil
			end
			if heartToy then
				pcall(function()
					heartToy:Destroy()
				end)
				heartToy = nil
			end
		end
	end
})
local KeybindsGroup = Tabs.Keybinds:CreateBlock({Name = "Keybinds", Side = "Left"})
local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")

local Player = Players.LocalPlayer
local Mouse = Player:GetMouse()

local tpEnabled = true -- РјРѕР¶РЅРѕ СѓР±СЂР°С‚СЊ, РµСЃР»Рё РЅРµ РЅСѓР¶РµРЅ on/off

KeybindsGroup:CreateKeybind({
	Name = "Teleport to Mouse",
	Flag = "TPKeybind",
	Default = "X",
	Callback = function()
		if not tpEnabled then
			return
		end
		local character = Player.Character
		local hrp = character and character:FindFirstChild("HumanoidRootPart")
		if not hrp then
			return
		end
		local targetPos = Mouse.Hit.Position
		hrp.CFrame = CFrame.new(targetPos + Vector3.new(0, 3, 0))
	end
})
-- =========================================================================
-- LOOPGRAB POSE [DOG] (INTEGRATED INTO KEYBINDSGROUP)
-- =========================================================================
do
    local Players = game:GetService("Players")
    local RunService = game:GetService("RunService")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local LocalPlayer = Players.LocalPlayer

    local loopGrabDogActive = false
    local LoopGrabDogConn = nil
    local SpamChar = nil

    local function stopDogPose()
        loopGrabDogActive = false
        if LoopGrabDogConn then
            LoopGrabDogConn:Disconnect()
            LoopGrabDogConn = nil
        end
        
        -- Restore collisions and velocities
        if SpamChar and SpamChar.Parent and SpamChar:FindFirstChild("Head") then 
            for _, v in pairs(SpamChar:GetChildren()) do 
                if v:IsA("BasePart") then 
                    v.AssemblyLinearVelocity = Vector3.zero
                    v.AssemblyAngularVelocity = Vector3.zero
                    v.CanCollide = true
                end
            end
        end
        SpamChar = nil
    end

    local function startDogPose()
        local Mouse = LocalPlayer:GetMouse()
        local target = Mouse.Target
        if not target then 
            Library:Notify({ Title = "System", Content = "No target found under mouse!", Duration = 3 })
            return 
        end
        
        SpamChar = target.Parent
        local Head = SpamChar:FindFirstChild("Head")
        local Torso = SpamChar:FindFirstChild("Torso") or SpamChar:FindFirstChild("UpperTorso")
        local Hum = SpamChar:FindFirstChildOfClass("Humanoid")
        
        if not (Torso and Head and Hum) then 
            SpamChar = nil
            Library:Notify({ Title = "System", Content = "Invalid character target!", Duration = 3 })
            return 
        end
        
        loopGrabDogActive = true
        local snoRemote = ReplicatedStorage:WaitForChild("GrabEvents", 5) and ReplicatedStorage.GrabEvents:FindFirstChild("SetNetworkOwner")
        local myChar = LocalPlayer.Character
        local hrp = myChar and myChar:FindFirstChild("HumanoidRootPart")
        
        Library:Notify({ Title = "Dog Pose", Content = "Locked onto " .. SpamChar.Name, Duration = 3 })

        LoopGrabDogConn = RunService.Heartbeat:Connect(function()
            if not loopGrabDogActive or not hrp or not SpamChar or not SpamChar.Parent then
                stopDogPose()
                return
            end
            
            Torso = SpamChar:FindFirstChild("Torso") or SpamChar:FindFirstChild("UpperTorso")
            Head = SpamChar:FindFirstChild("Head")
            if not Torso or not Head then 
                stopDogPose()
                return 
            end
            
            -- Claim Network Ownership
            if snoRemote then
                pcall(function() snoRemote:FireServer(Head, Head.CFrame) end)
            end
            
            -- Ghosting collisions
            for _, x in pairs(SpamChar:GetDescendants()) do 
                if x:IsA("BasePart") then 
                    x.CanCollide = false
                end
            end
            
            Hum.Health = 100 -- Prevent dying from physics glitches
            
            -- Force Dog Pose CFrames relative to your HRP
            Torso.CFrame = hrp.CFrame * CFrame.new(0, -1, -2) * CFrame.Angles(math.rad(-90), 0, math.rad(180))
            Head.CFrame = Torso.CFrame * CFrame.new(0, 1, 0) * CFrame.Angles(math.rad(90), 0, 0)
            
            local lArm = SpamChar:FindFirstChild("Left Arm")
            if lArm then lArm.CFrame = Torso.CFrame * CFrame.new(-1, 0.5, 0) * CFrame.Angles(math.rad(60), 0, math.rad(-30)) end
            
            local rArm = SpamChar:FindFirstChild("Right Arm")
            if rArm then rArm.CFrame = Torso.CFrame * CFrame.new(1, 0.5, 0) * CFrame.Angles(math.rad(60), 0, math.rad(30)) end
            
            local lLeg = SpamChar:FindFirstChild("Left Leg")
            if lLeg then lLeg.CFrame = Torso.CFrame * CFrame.new(-0.5, -1, 0) * CFrame.Angles(math.rad(40), 0, 0) end
            
            local rLeg = SpamChar:FindFirstChild("Right Leg")
            if rLeg then rLeg.CFrame = Torso.CFrame * CFrame.new(0.5, -1, 0) * CFrame.Angles(math.rad(40), 0, 0) end
        end)
    end
-- =========================================================================
-- LOOPGRAB POSE [DOG] (REFINED ANATOMY)
-- =========================================================================
do
    local Players = game:GetService("Players")
    local RunService = game:GetService("RunService")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local LocalPlayer = Players.LocalPlayer

    local loopGrabDogActive = false
    local LoopGrabDogConn = nil
    local SpamChar = nil

    local function stopDogPose()
        loopGrabDogActive = false
        if LoopGrabDogConn then
            LoopGrabDogConn:Disconnect()
            LoopGrabDogConn = nil
        end
        if SpamChar and SpamChar.Parent then 
            for _, v in pairs(SpamChar:GetDescendants()) do 
                if v:IsA("BasePart") then 
                    v.AssemblyLinearVelocity = Vector3.zero
                    v.CanCollide = true
                end
            end
        end
        SpamChar = nil
    end

    local function startDogPose()
        local Mouse = LocalPlayer:GetMouse()
        local target = Mouse.Target
        if not target or not target.Parent:FindFirstChild("Humanoid") then return end
        
        SpamChar = target.Parent
        loopGrabDogActive = true
        
        local snoRemote = ReplicatedStorage:FindFirstChild("GrabEvents") and ReplicatedStorage.GrabEvents:FindFirstChild("SetNetworkOwner")
        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        
        LoopGrabDogConn = RunService.Heartbeat:Connect(function()
            if not loopGrabDogActive or not hrp or not SpamChar:FindFirstChild("Torso") then stopDogPose() return end
            
            local Torso = SpamChar.Torso
            local Head = SpamChar:FindFirstChild("Head")
            
            -- Network Ownership
            if snoRemote then pcall(function() snoRemote:FireServer(Head, Head.CFrame) end) end
            
            -- Dog Math: Torso horizontal, limbs tucked under
            Torso.CFrame = hrp.CFrame * CFrame.new(0, -1.2, -2.5) * CFrame.Angles(math.rad(-90), 0, 0)
            
            if Head then 
                Head.CFrame = Torso.CFrame * CFrame.new(0, 1.3, -0.2) * CFrame.Angles(math.rad(45), 0, 0) 
            end
            
            -- Limbs as Paws
            local LArm = SpamChar:FindFirstChild("Left Arm")
            local RArm = SpamChar:FindFirstChild("Right Arm")
            local LLeg = SpamChar:FindFirstChild("Left Leg")
            local RLeg = SpamChar:FindFirstChild("Right Leg")
            
            if LArm then LArm.CFrame = Torso.CFrame * CFrame.new(-0.8, 0.5, 0.5) * CFrame.Angles(math.rad(90), 0, math.rad(20)) end
            if RArm then RArm.CFrame = Torso.CFrame * CFrame.new(0.8, 0.5, 0.5) * CFrame.Angles(math.rad(90), 0, math.rad(-20)) end
            if LLeg then LLeg.CFrame = Torso.CFrame * CFrame.new(-0.6, -1.2, 0.5) * CFrame.Angles(math.rad(90), 0, math.rad(10)) end
            if RLeg then RLeg.CFrame = Torso.CFrame * CFrame.new(0.6, -1.2, 0.5) * CFrame.Angles(math.rad(90), 0, math.rad(-10)) end
            
            -- Disable collisions so they don't bounce
            for _, p in pairs(SpamChar:GetDescendants()) do if p:IsA("BasePart") then p.CanCollide = false end end
        end)
    end

    KeybindsGroup:CreateKeybind({
        Name = "Dog Pose Quick V1",
        Flag = "DogPoseKey",
        Default = "T",
        Callback = function()
            if loopGrabDogActive then
                stopDogPose()
            else
                startDogPose()
            end
        end
    })
end
    -- Auto-bind for T (Spam keybind) within KeybindsGroup
    KeybindsGroup:CreateKeybind({
        Name = "Dog Pose Quick V2",
        Flag = "DogPoseKey",
        Default = "T",
        Callback = function()
            local newState = not loopGrabDogActive
            SetToggleState("LoopGrab_Dog", newState)
            
            -- If you want the toggle visually updated in the UI, you may need to call Options["LoopGrab_Dog"]:Set(newState) depending on your UI library.
            
            if newState then
                startDogPose()
            else
                stopDogPose()
                Library:Notify({ Title = "Dog Pose", Content = "Deactivated", Duration = 3 })
            end
        end
    })
end
do
    -- // Block Setup \\ --
    local FigureMain = Tabs.Grab:CreateBlock({Name = "Figure Grab Main", Side = "Left"})
    local FigurePoses = Tabs.Grab:CreateBlock({Name = "Figure Poses", Side = "Right"})
    local FigureOffsets = Tabs.Grab:CreateBlock({Name = "Limb Offsets", Side = "Right"})

    -- // Module Initialization \\ --
    getgenv().FigureGrabModule = getgenv().FigureGrabModule or {}
    local FGM = getgenv().FigureGrabModule

    FGM.Players = game:GetService("Players")
    FGM.RunService = game:GetService("RunService")
    FGM.ReplicatedStorage = game:GetService("ReplicatedStorage")
    FGM.UserInputService = game:GetService("UserInputService")

    FGM.LocalPlayer = FGM.Players.LocalPlayer
    FGM.Mouse = FGM.LocalPlayer:GetMouse()

    FGM.GrabEvents = FGM.ReplicatedStorage:WaitForChild("GrabEvents")
    FGM.SetNetworkOwner = FGM.GrabEvents:WaitForChild("SetNetworkOwner")
    
    -- Toys variables for AutoRagdoll
    FGM.MenuToys = FGM.ReplicatedStorage:WaitForChild("MenuToys")
    FGM.ToySpawn = FGM.MenuToys:WaitForChild("SpawnToyRemoteFunction")
    FGM.DestroyToy = FGM.MenuToys:WaitForChild("DestroyToy")

    FGM.State = {
        FigureGrabEnabled = false,
        FigureGrabConnection = nil,
        TargetCharacter = nil,
        AnimationCopyEnabled = false,
        VectorZero = Vector3.new(0, 0, 0),
        
        -- Auto Ragdoll State
        AutoRagdollToggle = false,
        AutoRagdollEnabled = false,
        AutoRagdollConnection = nil,
        RagdollPallet = nil,
        RagdollSoundPart = nil,
        SeveralEnabled = false,
        SeveralTargets = {}
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
        JojoStand = {HoldPosition={X=-4.5,Y=0.5,Z=-1.5},HoldRotation={X=8,Y=349,Z=0},LeftArmPosition={X=1.5,Y=0,Z=0},LeftArmRotation={X=15,Y=62,Z=41},RightArmPosition={X=-1.5,Y=0.5,Z=-0.5},RightArmRotation={X=65,Y=149,Z=6},LeftLegPosition={X=-0.5,Y=-2,Z=0},LeftLegRotation={X=349,Y=0,Z=360},RightLegPosition={X=0.5,Y=-2,Z=0},RightLegRotation={X=345,Y=360,Z=10},HeadPosition={X=0,Y=1.5,Z=0},HeadRotation={X=0,Y=344,Z=0}}
    }

    -- // Core Functions \\ --

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
            if FGM.State.RagdollPallet then
                pcall(function() FGM.DestroyToy:FireServer(FGM.State.RagdollPallet) end)
            end
            FGM.State.RagdollPallet = nil
            FGM.State.RagdollSoundPart = nil
            return
        end
        
        task.spawn(function()
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
            local soundPart = pallet:WaitForChild("SoundPart", 5)
            if not soundPart then return end
            
            local t2 = tick() + 3
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
                Library:Notify({Title = "Error", Content = "Aim at a player first", Duration = 3})
                return
            end
            
            FGM.State.TargetCharacter = MouseTarget.Parent
            local MyCharacter = FGM.GetCharacter(FGM.LocalPlayer)
            
            if not FGM.State.TargetCharacter or not MyCharacter then
                Library:Notify({Title = "Error", Content = "Invalid target", Duration = 3})
                return
            end
            
            local BodyParts = {"Head", "Left Arm", "Right Arm", "Left Leg", "Right Leg"}
            local TargetTorso = FGM.State.TargetCharacter:FindFirstChild("Torso")
            
            if not TargetTorso then
                Library:Notify({Title = "Error", Content = "Torso not found", Duration = 3})
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
                if not FGM.State.TargetCharacter or not MyCharacter then
                    FGM.State.FigureGrabEnabled = false
                    if FGM.State.FigureGrabConnection then
                        FGM.State.FigureGrabConnection:Disconnect()
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
                                    math.rad(FGM.Configuration[rotKey].X), math.rad(FGM.Configuration[rotKey].Y), math.rad(FGM.Configuration[rotKey].Z)
                                )
                                part.Velocity = FGM.State.VectorZero
                                part.RotVelocity = FGM.State.VectorZero
                            end
                        end
                    end
                end
                
                FGM.SetNetworkOwner:FireServer(MouseTarget, holdCFrame)
            end)
            
            -- Trigger Auto Ragdoll if UI toggle is active
            if FGM.State.AutoRagdollToggle then
                FGM.ToggleAutoRagdoll(true)
            end
            
            Library:Notify({Title = "Figure Grab", Content = "Figure Grab Activated", Duration = 3})
        else
            FGM.State.FigureGrabEnabled = false
            FGM.State.AnimationCopyEnabled = false
            if FGM.State.FigureGrabConnection then
                FGM.State.FigureGrabConnection:Disconnect()
                FGM.State.FigureGrabConnection = nil
            end
            
            -- Stop Auto Ragdoll
            FGM.ToggleAutoRagdoll(false)
            
            Library:Notify({Title = "Figure Grab", Content = "Figure Grab Deactivated", Duration = 3})
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


    -- // UI Elements (XOCU) \\ --

    FigureMain:CreateKeybind({
        Name = "Toggle Figure Grab (Aim at target)",
        Flag = "FG_ToggleKeybind",
        Default = "V",
        Callback = function()
            FGM.ToggleFigureGrab()
        end,
    })

    FigureMain:CreateToggle({
        Name = "Auto Ragdoll Target",
        Flag = "FG_AutoRagdollToggle",
        Default = false,
        Callback = function(Value)
            FGM.State.AutoRagdollToggle = Value
            if FGM.State.FigureGrabEnabled then
                FGM.ToggleAutoRagdoll(Value)
            end
        end,
    })

    FigureMain:CreateToggle({
        Name = "Copy My Animations to Target",
        Flag = "FG_AnimCopyToggle",
        Default = false,
        Callback = function(Value)
            FGM.State.AnimationCopyEnabled = Value
            if Value then
                Library:Notify({Title = "Animation Copy", Content = "Now copying your animations!", Duration = 3})
            else
                Library:Notify({Title = "Animation Copy", Content = "Manual control restored", Duration = 3})
            end
        end,
    })

    -- Saves / Presets
    FigurePoses:CreateButton({ Name = "Reset Pose", Callback = function() FGM.ResetPose() end })
    FigurePoses:CreateButton({ Name = "Pose 1 Jesus", Callback = function() FGM.ApplyPreset("Pose1") end })
    FigurePoses:CreateButton({ Name = "Pose 2 Dog", Callback = function() FGM.ApplyPreset("Pose2") end })
    FigurePoses:CreateButton({ Name = "Pose 3 L", Callback = function() FGM.ApplyPreset("Pose3") end })
    FigurePoses:CreateButton({ Name = "Pose 4 Head Hold", Callback = function() FGM.ApplyPreset("Pose4") end })
    FigurePoses:CreateButton({ Name = "Pose 5 Handstand", Callback = function() FGM.ApplyPreset("Pose5") end })
    FigurePoses:CreateButton({ Name = "Pose 6 Stand 1", Callback = function() FGM.ApplyPreset("Pose6") end })
    FigurePoses:CreateButton({ Name = "Pose 7 T-Pose", Callback = function() FGM.ApplyPreset("Pose7") end })
    FigurePoses:CreateButton({ Name = "Pose 8 Stand 2", Callback = function() FGM.ApplyPreset("JojoStand") end })

    -- Offset Sliders
    local function CreateLimbSliders(limbName, configKey)
        for _, axis in ipairs({"X", "Y", "Z"}) do
            FigureOffsets:CreateSlider({
                Name = limbName .. " Pos " .. axis, Flag = "FG_" .. configKey .. "Pos" .. axis,
                Min = -50, Max = 50, Default = 0, Rounding = 1,
                Callback = function(value) FGM.UpdateConfig(configKey .. "Position", axis, value) end
            })
            FigureOffsets:CreateSlider({
                Name = limbName .. " Rot " .. axis, Flag = "FG_" .. configKey .. "Rot" .. axis,
                Min = 0, Max = 360, Default = 0, Rounding = 0,
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

end
TargetGroup:CreateToggle({
	Name = "Remove Anti Kick",
        Flag = "Remove Anti Kick",
	Default = false,
	Callback = function(Value)
        SetToggleState("Remove Anti Kick", Value)
		antiAntiKickActive = Value
		if Value then
			task.spawn(function()
				local SetNetOwner = game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner
				local LocalPlayer = game.Players.LocalPlayer
				function invis_touch(part, cf)
					SetNetOwner:FireServer(part, cf)
				end
				function CheckAndYeet(toy)
					local part = toy:FindFirstChild("SoundPart")
					if part then
						invis_touch(part, part.CFrame)
						if part:FindFirstChild("PartOwner") and part.PartOwner.Value == LocalPlayer.Name then
							part.CFrame = CFrame.new(0, 1000, 0)
						end
					end
				end
				while antiAntiKickActive do
					local target = selectedKickPlayer
					if target then
						local spawned = workspace:FindFirstChild(target.Name .. "SpawnedInToys")
						if spawned then
							if spawned:FindFirstChild("NinjaKunai") then
								CheckAndYeet(spawned.NinjaKunai)
							end
							if spawned:FindFirstChild("NinjaShuriken") then
								CheckAndYeet(spawned.NinjaShuriken)
							end
							if spawned:FindFirstChild("AntiKick") then
								CheckAndYeet(spawned.AntiKick)
							end
						end
					end
					task.wait(0.1)
				end
			end)
		else
			antiAntiKickActive = false
		end
	end
})

-- ==========================================
-- TELEKINESIS AURA & DEATH AURA INTEGRATION
-- ==========================================

-- =========================
-- ANIMATION PLAYER (FULL)
do
-- =========================

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")

local Player = Players.LocalPlayer

-- =========================
-- UI GROUP
-- =========================
local AnimationsGroup = Tabs.Misc:CreateBlock({Name = "Animations", Side = "Right"})

-- =========================
-- STATE
-- =========================
local animEnabled = false
local currentTrack = nil
local selectedAnimName = "Crazy"
local selectedKey = Enum.KeyCode.Q

-- =========================
-- WORKING ANIMATIONS ONLY
-- =========================
local Animations = {
	["Crazy"]    = "rbxassetid://248263260",
	["Insane"]   = "rbxassetid://35654637",
	["Collapse"] = "rbxassetid://35154961",
	["Zombie"]   = "rbxassetid://33796059",
}

-- =========================
-- PLAY
-- =========================
function playAnimation()
	local char = Player.Character or Player.CharacterAdded:Wait()
	local hum = char:FindFirstChildOfClass("Humanoid")
	if not hum then
		return
	end
	local animator = hum:FindFirstChildOfClass("Animator")
	if not animator then
		animator = Instance.new("Animator")
		animator.Parent = hum
	end
	if currentTrack then
		currentTrack:Stop()
		currentTrack = nil
	end
	local anim = Instance.new("Animation")
	anim.AnimationId = Animations[selectedAnimName]
	currentTrack = animator:LoadAnimation(anim)
	currentTrack.Priority = Enum.AnimationPriority.Action
	currentTrack.Looped = true
	currentTrack:Play()

    -- рџ”Ѓ FORCE LOOP (flight safe)
	task.spawn(function()
		while animEnabled and currentTrack do
			if currentTrack.TimePosition > 0.9 then
				currentTrack.TimePosition = 0.3
			end
			task.wait(0.05)
		end
	end)
end

-- =========================
-- STOP
-- =========================
function stopAnimation()
	if currentTrack then
		currentTrack:Stop()
		currentTrack = nil
	end
end

-- =========================
-- TOGGLE
-- =========================
AnimationsGroup:CreateToggle({
	Name = "Play Animation",
        Flag = "Play Animation",
	Default = false,
	Callback = function(on)
        SetToggleState("Play Animation", on)
		animEnabled = on
		if on then
			playAnimation()
		else
			stopAnimation()
		end
	end
})

-- =========================
-- ANIMATION DROPDOWN
-- =========================
AnimationsGroup:CreateDropdown({
	Name = "Animation",
        Flag = "Animation",
	Items = {
		"Crazy",
		"Insane",
		"Collapse",
		"Zombie",
	},
	Default = "Q",
	Callback = function(v)
		selectedAnimName = v
		if animEnabled then
			playAnimation()
		end
	end
})

-- =========================
-- KEYBIND DROPDOWN вњ…
-- =========================
AnimationsGroup:CreateDropdown({
	Name = "Toggle Key",
        Flag = "Toggle Key",
	Items = {
		"Q",
		"E",
		"R",
		"T",
		"F",
		"Z",
		"X",
		"C"
	},
	Default = "Q",
	Callback = function(v)
		selectedKey = Enum.KeyCode[v]
	end
})
-- =========================
-- SIT ON NEAREST BLOBMAN
-- =========================

local Players = game:GetService("Players")
local Player = Players.LocalPlayer

function getNearestBlobman(maxDist)
	local char = Player.Character
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if not hrp then
		return
	end
	local nearest, dist = nil, maxDist or 50
	for _, model in ipairs(workspace:GetDescendants()) do
		if model:IsA("Model") and model.Name == "CreatureBlobman" then
			local root = model:FindFirstChild("HumanoidRootPart") or model.PrimaryPart
			if root then
				local d = (root.Position - hrp.Position).Magnitude
				if d < dist then
					dist = d
					nearest = model
				end
			end
		end
	end
	return nearest
end

function SitOnBlobman()
	local char = Player.Character
	if not char then
		return
	end
	local hum = char:FindFirstChildOfClass("Humanoid")
	local hrp = char:FindFirstChild("HumanoidRootPart")
	if not hum or not hrp then
		return
	end

    -- СѓР¶Рµ СЃРёРґРёРј
	if hum.SeatPart then
		return
	end

    -- РёС‰РµРј Р‘Р›РР–РђР™РЁР•Р“Рћ
	local blob = getNearestBlobman(40)
	if not blob then
		warn("Blobman not found nearby")
		return
	end

    -- РёС‰РµРј СЃРёРґ
	local seat =
        blob:FindFirstChildWhichIsA("Seat", true)
        or blob:FindFirstChildWhichIsA("VehicleSeat", true)
	if not seat then
		warn("Blobman seat not found")
		return
	end

    -- С‚РµР»РµРїРѕСЂС‚ Р РЇР”РћРњ СЃ Р±Р»РѕР±РѕРј (РЅРµ РІ РµР±РµРЅСЏ)
	hrp.CFrame = seat.CFrame * CFrame.new(0, 1.2, -1)
	task.wait(0.05)

    -- РџР РРќРЈР”РРўР•Р›Р¬РќРђРЇ РџРћРЎРђР”РљРђ
	pcall(function()
		seat:Sit(hum)
	end)
end

-- =========================
-- KEYBIND
-- =========================

KeybindsGroup:CreateKeybind({
	Name = "Sit on nearest Blobman",
	Flag = "SitBlobmanKey",
	Default = "Z",
	Callback = function()
		SitOnBlobman()
	end
})
AnimationsGroup:CreateToggle({
	Name = "Follow & Stare",
        Flag = "Follow & Stare",
	Default = false,
	Callback = function(on)
        SetToggleState("Follow & Stare", on)
		follow = on
		task.spawn(function()
			local lp = game.Players.LocalPlayer
			while follow do
				local target = game.Players:GetPlayers()[math.random(#game.Players:GetPlayers())]
				if target ~= lp and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
					local hrp = lp.Character.HumanoidRootPart
					local thrp = target.Character.HumanoidRootPart
					hrp.CFrame = CFrame.new(thrp.Position + thrp.CFrame.LookVector * -2, thrp.Position)
				end
				task.wait(0.3)
			end
		end)
	end
})
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

AnimationsGroup:CreateToggle({
	Name = "Fake Death",
        Flag = "Fake Death",
	Default = false,
	Callback = function(on)
        SetToggleState("Fake Death", on)
		local char = LocalPlayer.Character
		if not char then
			return
		end
		local hum = char:FindFirstChildOfClass("Humanoid")
		if not hum then
			return
		end
		if on then
            -- РїР°РґР°РµРј РєР°Рє РјС‘СЂС‚РІС‹Р№
			hum:ChangeState(Enum.HumanoidStateType.Physics)
			hum.PlatformStand = true
		else
            -- РІСЃС‚Р°С‘Рј РѕР±СЂР°С‚РЅРѕ
			hum.PlatformStand = false
			hum:ChangeState(Enum.HumanoidStateType.GettingUp)
		end
	end
})
local fakeLagConn
AnimationsGroup:CreateToggle({
	Name = "Fake Lag",
        Flag = "Fake Lag",
	Default = false,
	Callback = function(on)
        SetToggleState("Fake Lag", on)
		if fakeLagConn then
			fakeLagConn:Disconnect()
			fakeLagConn = nil
		end
		if not on then
			return
		end
		fakeLagConn = RunService.Heartbeat:Connect(function()
			local root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
			if not root then
				return
			end
			if math.random(1, 5) == 1 then
				root.CFrame = root.CFrame * CFrame.new(math.random(-2, 2) / 10, 0, math.random(-2, 2) / 10)
			end
		end)
	end
})
end
do
local TrollExtraGroup = Tabs.Misc:CreateBlock({Name = "Troll", Side = "Right"})

-- ===========================
-- Toggle "Bang" (Slow)
-- ===========================

local Players = game:GetService("Players")

local playBangActive = false
local bangAnimTrack = nil
local bangAnimId = "rbxassetid://148840371" -- Bang РёР· Infinite Yield
local bangSpeed = 10-- рџ”Ґ РЎРљРћР РћРЎРўР¬ (1 = РЅРѕСЂРјР°Р»СЊРЅРѕ, 0.3вЂ“0.5 РјРµРґР»РµРЅРЅРѕ)

-- в–¶ Р·Р°РїСѓСЃРє Р°РЅРёРјР°С†РёРё
function startBang()
	local plr = Players.LocalPlayer
	local char = plr.Character or plr.CharacterAdded:Wait()
	local hum = char:FindFirstChildOfClass("Humanoid")
	if not hum then
		return
	end
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
	bangAnimTrack:AdjustSpeed(bangSpeed) -- рџђў Р·Р°РјРµРґР»РµРЅРёРµ

    -- Infinite Yield loop
	task.spawn(function()
		while playBangActive do
			task.wait(0.1)
			if bangAnimTrack and bangAnimTrack.IsPlaying then
				bangAnimTrack.TimePosition = 0.1
			end
		end
	end)
end

-- вЏ№ РѕСЃС‚Р°РЅРѕРІРєР°
function stopBang()
	if bangAnimTrack then
		bangAnimTrack:Stop()
		bangAnimTrack = nil
	end
end

-- рџ” Toggle
TrollExtraGroup:CreateToggle({
	Name = "Bang (Slow)",
        Flag = "Bang (Slow)",
	Default = false,
	Callback = function(on)
        SetToggleState("Bang (Slow)", on)
		playBangActive = on
		if on then
			startBang()
		else
			stopBang()
		end
	end
})
TrollExtraGroup:CreateToggle({
    Name = "DynamicWater",
    Flag = "RealisticWater",
    Default = false,

    Callback = function(state)
        local terrain = workspace.Terrain
        local model = workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel

        for _, part in ipairs(model:GetChildren()) do
            if part:IsA("Part") then
                local size = part.Size
                local cf = part.CFrame

                local region = Region3.new(
                    cf.Position - size/2,
                    cf.Position + size/2
                ):ExpandToGrid(4)

                terrain:FillRegion(region, 4, Enum.Material.Water)

                part:Destroy()
            end
        end
    end
})

GrabGroup:CreateToggle({
	Name = "MassLess Grab",
        Flag = "MassLess Grab",
	Default = false,
	Callback = function(Value)
        SetToggleState("MassLess Grab", Value)
		_G.MassLessGrab = Value
		if not _G.MassLessGrab then
			if _G.MLConn then
				_G.MLConn:Disconnect()
				_G.MLConn = nil
			end
			return
		end
		if _G.MLConn then
			_G.MLConn:Disconnect()
			_G.MLConn = nil
		end
		_G.MLSense = _G.MLSense or 200
		_G.MLConn = game:GetService("RunService").Heartbeat:Connect(function()
			if not _G.MassLessGrab then
				return
			end
			local gp = workspace:FindFirstChild("GrabParts")
			if not gp then
				return
			end
			local dp = gp:FindFirstChild("DragPart")
			if not dp then
				return
			end
			local ap = dp:FindFirstChild("AlignPosition")
			local ao = dp:FindFirstChild("AlignOrientation")
			if ap then
				ap.Responsiveness = _G.MLSense
				ap.MaxForce = math.huge
				ap.MaxVelocity = math.huge
			end
			if ao then
				ao.Responsiveness = _G.MLSense
				ao.MaxTorque = math.huge
			end
		end)
	end
})
end





-- =========================================================================
-- VISUALS ENGINE (wrapped in do...end to stay under 200-local Lua limit)
-- =========================================================================
do
    local Lighting = game:GetService("Lighting")
    local Visuals = {}

    Visuals.DefaultLighting = {
        Brightness=Lighting.Brightness, ClockTime=Lighting.ClockTime,
        GlobalShadows=Lighting.GlobalShadows, OutdoorAmbient=Lighting.OutdoorAmbient,
        Ambient=Lighting.Ambient, FogStart=Lighting.FogStart, FogEnd=Lighting.FogEnd,
        FogColor=Lighting.FogColor, ExposureCompensation=Lighting.ExposureCompensation,
    }
    Visuals.DefaultSkySettings = {}
    local defaultSky = Lighting:FindFirstChildOfClass("Sky")
    if defaultSky then
        Visuals.DefaultSkySettings = {
            SkyboxBk=defaultSky.SkyboxBk, SkyboxDn=defaultSky.SkyboxDn,
            SkyboxFt=defaultSky.SkyboxFt, SkyboxLf=defaultSky.SkyboxLf,
            SkyboxRt=defaultSky.SkyboxRt, SkyboxUp=defaultSky.SkyboxUp,
        }
    end

    Visuals.HatEnabled=false; Visuals.HatTransparency=0.3; Visuals.HatRainbow=false
    Visuals.HatColor=Color3.fromRGB(0,255,255); Visuals.HatParts={}
    Visuals.TrailEnabled=false; Visuals.TrailGradient=false; Visuals.TrailLifetime=0.5
    Visuals.TrailTransparencyStart=0; Visuals.TrailRainbow=false
    Visuals.TrailColorStatic=Color3.fromRGB(0,255,255)
    Visuals.TrailGradient1=Color3.fromRGB(0,86,255); Visuals.TrailGradient2=Color3.fromRGB(255,0,0)
    Visuals.TrailParts={}
    Visuals.SkinTrailEnabled=false; Visuals.SkinTrailColor=Color3.fromRGB(255,0,0); Visuals.SkinTrailLife=0.5
    Visuals.ForceFieldEnabled=false; Visuals.ForceFieldColor=Color3.fromRGB(128,128,128)
    Visuals.ForceFieldRainbow=false; Visuals.OriginalColors={}
    Visuals.AuraEnabled=false; Visuals.AuraType="Godly"; Visuals.CustomAuraID=""
    Visuals.CurrentAuraModel=nil; Visuals.AuraEffects={}
    Visuals.WorldTimeEnabled=false; Visuals.WorldTimeValue=12; Visuals.FullBrightEnabled=false
    Visuals.NebulaEnabled=false; Visuals.NebulaThemeColor=Color3.fromRGB(173,216,230)
    Visuals.CurrentSkybox="HD"; Visuals.CustomSkyEnabled=false
    Visuals.ScreenEnabled=false; Visuals.ScreenIntensity=0; Visuals.ScreenConnection=nil
    Visuals.AnimeImageEnabled=false; Visuals.AnimeImageGui=nil

    Visuals.AuraModels = {
        Godly="rbxassetid://16699750981",["Super Sayien"]="rbxassetid://116109508364297",
        ["North Star"]="rbxassetid://83945069652732",["Blue Lord"]="rbxassetid://10974316799",
        ["Pink Aura"]="rbxassetid://115980859615239",["Angel Wing"]="rbxassetid://90022969696073",
        ["Sweet Heart"]="rbxassetid://91724768175470",["Ethereal Aura"]="rbxassetid://97041568674250",
    }

    Visuals.SkyboxAssets = {
        ["Black Storm"]={Bk="rbxassetid://15502511288",Dn="rbxassetid://15502508460",Ft="rbxassetid://15502510289",Lf="rbxassetid://15502507918",Rt="rbxassetid://15502509398",Up="rbxassetid://15502511911"},
        HD={Bk="http://www.roblox.com/asset/?id=16553658937",Dn="http://www.roblox.com/asset/?id=16553660713",Ft="http://www.roblox.com/asset/?id=16553662144",Lf="http://www.roblox.com/asset/?id=16553664042",Rt="http://www.roblox.com/asset/?id=16553665766",Up="http://www.roblox.com/asset/?id=16553667750"},
        Snow={Bk="http://www.roblox.com/asset/?id=155657655",Dn="http://www.roblox.com/asset/?id=155674246",Ft="http://www.roblox.com/asset/?id=155657609",Lf="http://www.roblox.com/asset/?id=155657671",Rt="http://www.roblox.com/asset/?id=155657619",Up="http://www.roblox.com/asset/?id=155674931"},
        ["Blue Space"]={Bk="rbxassetid://15536110634",Dn="rbxassetid://15536112543",Ft="rbxassetid://15536116141",Lf="rbxassetid://15536114370",Rt="rbxassetid://15536118762",Up="rbxassetid://15536117282"},
        Realistic={Bk="rbxassetid://653719502",Dn="rbxassetid://653718790",Ft="rbxassetid://653719067",Lf="rbxassetid://653719190",Rt="rbxassetid://653718931",Up="rbxassetid://653719321"},
        Stormy={Bk="http://www.roblox.com/asset/?id=18703245834",Dn="http://www.roblox.com/asset/?id=18703243349",Ft="http://www.roblox.com/asset/?id=18703240532",Lf="http://www.roblox.com/asset/?id=18703237556",Rt="http://www.roblox.com/asset/?id=18703235430",Up="http://www.roblox.com/asset/?id=18703232671"},
        Pink={Bk="rbxassetid://12216109205",Dn="rbxassetid://12216109875",Ft="rbxassetid://12216109489",Lf="rbxassetid://12216110170",Rt="rbxassetid://12216110471",Up="rbxassetid://12216108877"},
        Sunset={Bk="rbxassetid://600830446",Dn="rbxassetid://600831635",Ft="rbxassetid://600832720",Lf="rbxassetid://600886090",Rt="rbxassetid://600833862",Up="rbxassetid://600835177"},
        Arctic={Bk="http://www.roblox.com/asset/?id=225469390",Dn="http://www.roblox.com/asset/?id=225469395",Ft="http://www.roblox.com/asset/?id=225469403",Lf="http://www.roblox.com/asset/?id=225469450",Rt="http://www.roblox.com/asset/?id=225469471",Up="http://www.roblox.com/asset/?id=225469481"},
        Space={Bk="http://www.roblox.com/asset/?id=166509999",Dn="http://www.roblox.com/asset/?id=166510057",Ft="http://www.roblox.com/asset/?id=166510116",Lf="http://www.roblox.com/asset/?id=166510092",Rt="http://www.roblox.com/asset/?id=166510131",Up="http://www.roblox.com/asset/?id=166510114"},
        ["Roblox Default"]={Bk="rbxasset://textures/sky/sky512_bk.tex",Dn="rbxasset://textures/sky/sky512_dn.tex",Ft="rbxasset://textures/sky/sky512_ft.tex",Lf="rbxasset://textures/sky/sky512_lf.tex",Rt="rbxasset://textures/sky/sky512_rt.tex",Up="rbxasset://textures/sky/sky512_up.tex"},
        ["Red Night"]={Bk="http://www.roblox.com/asset/?id=401664839",Dn="http://www.roblox.com/asset/?id=401664862",Ft="http://www.roblox.com/asset/?id=401664960",Lf="http://www.roblox.com/asset/?id=401664881",Rt="http://www.roblox.com/asset/?id=401664901",Up="http://www.roblox.com/asset/?id=401664936"},
        ["Deep Space 1"]={Bk="http://www.roblox.com/asset/?id=149397692",Dn="http://www.roblox.com/asset/?id=149397686",Ft="http://www.roblox.com/asset/?id=149397697",Lf="http://www.roblox.com/asset/?id=149397684",Rt="http://www.roblox.com/asset/?id=149397688",Up="http://www.roblox.com/asset/?id=149397702"},
        ["Pink Skies"]={Bk="http://www.roblox.com/asset/?id=151165214",Dn="http://www.roblox.com/asset/?id=151165197",Ft="http://www.roblox.com/asset/?id=151165224",Lf="http://www.roblox.com/asset/?id=151165191",Rt="http://www.roblox.com/asset/?id=151165206",Up="http://www.roblox.com/asset/?id=151165227"},
        ["Purple Sunset"]={Bk="rbxassetid://264908339",Dn="rbxassetid://264907909",Ft="rbxassetid://264909420",Lf="rbxassetid://264909758",Rt="rbxassetid://264908886",Up="rbxassetid://264907379"},
        ["Blue Night"]={Bk="http://www.roblox.com/asset/?id=12064107",Dn="http://www.roblox.com/asset/?id=12064152",Ft="http://www.roblox.com/asset/?id=12064121",Lf="http://www.roblox.com/asset/?id=12063984",Rt="http://www.roblox.com/asset/?id=12064115",Up="http://www.roblox.com/asset/?id=12064131"},
        ["Blossom Daylight"]={Bk="http://www.roblox.com/asset/?id=271042516",Dn="http://www.roblox.com/asset/?id=271077243",Ft="http://www.roblox.com/asset/?id=271042556",Lf="http://www.roblox.com/asset/?id=271042310",Rt="http://www.roblox.com/asset/?id=271042467",Up="http://www.roblox.com/asset/?id=271077958"},
        ["Blue Nebula"]={Bk="http://www.roblox.com/asset?id=135207744",Dn="http://www.roblox.com/asset?id=135207662",Ft="http://www.roblox.com/asset?id=135207770",Lf="http://www.roblox.com/asset?id=135207615",Rt="http://www.roblox.com/asset?id=135207695",Up="http://www.roblox.com/asset?id=135207794"},
        ["Blue Planet"]={Bk="rbxassetid://218955819",Dn="rbxassetid://218953419",Ft="rbxassetid://218954524",Lf="rbxassetid://218958493",Rt="rbxassetid://218957134",Up="rbxassetid://218950090"},
        ["Deep Space 2"]={Bk="http://www.roblox.com/asset/?id=159248188",Dn="http://www.roblox.com/asset/?id=159248183",Ft="http://www.roblox.com/asset/?id=159248187",Lf="http://www.roblox.com/asset/?id=159248173",Rt="http://www.roblox.com/asset/?id=159248192",Up="http://www.roblox.com/asset/?id=159248176"},
        Summer={Bk="rbxassetid://16648590964",Dn="rbxassetid://16648617436",Ft="rbxassetid://16648595424",Lf="rbxassetid://16648566370",Rt="rbxassetid://16648577071",Up="rbxassetid://16648598180"},
        Galaxy={Bk="rbxassetid://15983968922",Dn="rbxassetid://15983966825",Ft="rbxassetid://15983965025",Lf="rbxassetid://15983967420",Rt="rbxassetid://15983966246",Up="rbxassetid://15983964246"},
        Stylized={Bk="rbxassetid://18351376859",Dn="rbxassetid://18351374919",Ft="rbxassetid://18351376800",Lf="rbxassetid://18351376469",Rt="rbxassetid://18351376457",Up="rbxassetid://18351377189"},
        Minecraft={Bk="rbxassetid://8735166756",Dn="http://www.roblox.com/asset/?id=8735166707",Ft="http://www.roblox.com/asset/?id=8735231668",Lf="http://www.roblox.com/asset/?id=8735166755",Rt="http://www.roblox.com/asset/?id=8735166751",Up="http://www.roblox.com/asset/?id=8735166729"},
        ["Cloudy Rain"]={Bk="http://www.roblox.com/asset/?id=4498828382",Dn="http://www.roblox.com/asset/?id=4498828812",Ft="http://www.roblox.com/asset/?id=4498829917",Lf="http://www.roblox.com/asset/?id=4498830911",Rt="http://www.roblox.com/asset/?id=4498830417",Up="http://www.roblox.com/asset/?id=4498831746"},
        ["Black Cloudy Rain"]={Bk="http://www.roblox.com/asset/?id=149679669",Dn="http://www.roblox.com/asset/?id=149681979",Ft="http://www.roblox.com/asset/?id=149679690",Lf="http://www.roblox.com/asset/?id=149679709",Rt="http://www.roblox.com/asset/?id=149679722",Up="http://www.roblox.com/asset/?id=149680199"},
    }

    -- Hat
    function Visuals.removeHat(c) local h=Visuals.HatParts[c]; if h then h:Destroy(); Visuals.HatParts[c]=nil end end
    function Visuals.addHat(c) task.wait(0.1); local head=c and c:FindFirstChild("Head"); if not head then return end; Visuals.removeHat(c); local hat=Instance.new("Part"); hat.Name="Hat"; hat.Transparency=Visuals.HatTransparency; hat.Color=Visuals.HatColor; hat.Material=Enum.Material.Neon; hat.CanCollide=false; hat.CanTouch=false; hat.CanQuery=false; hat.Massless=true; local m=Instance.new("SpecialMesh"); m.MeshId="rbxassetid://1033714"; m.Scale=Vector3.new(2.4,1.6,2.4); m.Parent=hat; local w=Instance.new("WeldConstraint"); w.Part0=head; w.Part1=hat; w.Parent=hat; hat.CFrame=head.CFrame*CFrame.new(0,1.1,0); hat.Parent=c; Visuals.HatParts[c]=hat end
    function Visuals.updateHats() for c,h in pairs(Visuals.HatParts) do if h and h.Parent and c==Player.Character then h.Transparency=Visuals.HatTransparency; h.Color=Visuals.HatRainbow and Color3.fromHSV((tick()%5)/5,1,1) or Visuals.HatColor end end end

    -- Trail
    function Visuals.removeTrail(c) if Visuals.TrailParts[c] then Visuals.TrailParts[c]:Destroy(); Visuals.TrailParts[c]=nil end; local t=c and c:FindFirstChild("HumanoidRootPart"); if t then local a0=t:FindFirstChild("TrailAttach0"); local a1=t:FindFirstChild("TrailAttach1"); if a0 then a0:Destroy() end; if a1 then a1:Destroy() end end end
    function Visuals.addTrail(c) local t=c and c:FindFirstChild("HumanoidRootPart"); if not t then return end; Visuals.removeTrail(c); local a0=Instance.new("Attachment"); a0.Name="TrailAttach0"; a0.Position=Vector3.new(0,2,0); a0.Parent=t; local a1=Instance.new("Attachment"); a1.Name="TrailAttach1"; a1.Position=Vector3.new(0,-2,0); a1.Parent=t; local tr=Instance.new("Trail"); tr.Attachment0=a0; tr.Attachment1=a1; tr.Lifetime=Visuals.TrailLifetime; tr.LightEmission=0.2; tr.Enabled=true; tr.Transparency=NumberSequence.new({NumberSequenceKeypoint.new(0,Visuals.TrailTransparencyStart),NumberSequenceKeypoint.new(1,1)}); tr.Color=Visuals.TrailGradient and ColorSequence.new(Visuals.TrailGradient1,Visuals.TrailGradient2) or ColorSequence.new(Visuals.TrailColorStatic); tr.Parent=c; Visuals.TrailParts[c]=tr end
    function Visuals.updateTrails() for c,tr in pairs(Visuals.TrailParts) do if tr and tr.Parent and c==Player.Character then tr.Lifetime=Visuals.TrailLifetime; tr.Transparency=NumberSequence.new({NumberSequenceKeypoint.new(0,Visuals.TrailTransparencyStart),NumberSequenceKeypoint.new(1,1)}); local col=Visuals.TrailRainbow and Color3.fromHSV((tick()%5)/5,1,1) or Visuals.TrailColorStatic; tr.Color=Visuals.TrailGradient and ColorSequence.new(Visuals.TrailGradient1,Visuals.TrailGradient2) or ColorSequence.new(col) end end end

    -- Skin Trail
    function Visuals.toggleSkinTrail(en) local c=Player.Character; if not c then return end; local hrp=c:FindFirstChild("HumanoidRootPart"); if not hrp then return end; for _,p in ipairs(c:GetChildren()) do if p:IsA("BasePart") and p~=hrp then if en then if not p:FindFirstChild("SkinTrail") then local tr=Instance.new("Trail"); tr.Name="SkinTrail"; tr.Texture="rbxassetid://1390780157"; tr.Color=ColorSequence.new(Visuals.SkinTrailColor); tr.Lifetime=Visuals.SkinTrailLife; tr.Parent=p; local p1=Instance.new("Attachment"); p1.Name="SkinPointer1"; p1.Parent=p; local p2=Instance.new("Attachment"); p2.Name="SkinPointer2"; p2.Parent=hrp; tr.Attachment0=p1; tr.Attachment1=p2 end else local tr=p:FindFirstChild("SkinTrail"); local p1=p:FindFirstChild("SkinPointer1"); if tr then tr:Destroy() end; if p1 then p1:Destroy() end end end end; if not en then local p2=hrp:FindFirstChild("SkinPointer2"); if p2 then p2:Destroy() end end end
    function Visuals.updateSkinTrail() local c=Player.Character; if not c then return end; for _,d in ipairs(c:GetDescendants()) do if d:IsA("Trail") and d.Name=="SkinTrail" then d.Color=ColorSequence.new(Visuals.SkinTrailColor); d.Lifetime=Visuals.SkinTrailLife end end end

    -- ForceField
    function Visuals.saveOriginalColors(c) Visuals.OriginalColors[c]={} for _,p in ipairs(c:GetDescendants()) do if p:IsA("BasePart") and p.Name~="Hat" then Visuals.OriginalColors[c][p]={Color=p.Color,Material=p.Material} end end end
    function Visuals.applyForceField(c) Visuals.saveOriginalColors(c); for _,p in ipairs(c:GetDescendants()) do if p:IsA("BasePart") and p.Name~="Hat" then p.Color=Visuals.ForceFieldColor; p.Material=Enum.Material.ForceField end end end
    function Visuals.removeForceField(c) local orig=Visuals.OriginalColors[c]; if not orig then return end; for p,d in pairs(orig) do if p and p.Parent and p:IsA("BasePart") then p.Color=d.Color; p.Material=d.Material end end; Visuals.OriginalColors[c]=nil end
    function Visuals.updateForceField() if not(Player.Character and Visuals.ForceFieldEnabled) then return end; for _,p in ipairs(Player.Character:GetDescendants()) do if p:IsA("BasePart") and p.Name~="Hat" and p.Material==Enum.Material.ForceField then p.Color=Visuals.ForceFieldRainbow and Color3.fromHSV((tick()%5)/5,1,1) or Visuals.ForceFieldColor end end end

    -- Aura
    function Visuals.disableAura() for _,o in ipairs(Visuals.AuraEffects) do if o and o.Parent then o:Destroy() end end; table.clear(Visuals.AuraEffects) end
    function Visuals.enableAura(c) Visuals.disableAura(); if not Visuals.CurrentAuraModel then return end; local tmp=Visuals.CurrentAuraModel:Clone(); for _,o in ipairs(tmp:GetDescendants()) do if not o:IsA("BasePart") then local cl=o:Clone(); local pn=o.Parent and o.Parent.Name; local tgt=pn and c:FindFirstChild(pn) or c:FindFirstChildWhichIsA("BasePart"); if tgt and not tgt:FindFirstChild(cl.Name) then cl.Parent=tgt; table.insert(Visuals.AuraEffects,cl) end end end; tmp:Destroy() end
    function Visuals.updateAuraLogic() local id=Visuals.CustomAuraID~="" and("rbxassetid://"..Visuals.CustomAuraID:gsub("%D","")) or Visuals.AuraModels[Visuals.AuraType]; if not id then return end; local ok,m=pcall(function() return game:GetObjects(id)[1] end); if ok and m then Visuals.CurrentAuraModel=m; if Visuals.AuraEnabled and Player.Character then Visuals.enableAura(Player.Character) end end end

    -- Skybox/World
    function Visuals.applySkybox(n) local s=Visuals.SkyboxAssets[n]; if not s then return end; local sky=Lighting:FindFirstChildOfClass("Sky") or Instance.new("Sky",Lighting); sky.Name="Sky"; sky.SkyboxBk=s.Bk; sky.SkyboxDn=s.Dn; sky.SkyboxFt=s.Ft; sky.SkyboxLf=s.Lf; sky.SkyboxRt=s.Rt; sky.SkyboxUp=s.Up end
    function Visuals.restoreDefaultSky() local sky=Lighting:FindFirstChildOfClass("Sky"); if sky and Visuals.DefaultSkySettings.SkyboxBk then sky.SkyboxBk=Visuals.DefaultSkySettings.SkyboxBk; sky.SkyboxDn=Visuals.DefaultSkySettings.SkyboxDn; sky.SkyboxFt=Visuals.DefaultSkySettings.SkyboxFt; sky.SkyboxLf=Visuals.DefaultSkySettings.SkyboxLf; sky.SkyboxRt=Visuals.DefaultSkySettings.SkyboxRt; sky.SkyboxUp=Visuals.DefaultSkySettings.SkyboxUp elseif sky then sky:Destroy() end end
    function Visuals.setNebulaEnabled(en) Visuals.NebulaEnabled=en; if en then local bl=Lighting:FindFirstChild("NebulaBloom") or Instance.new("BloomEffect"); bl.Name="NebulaBloom"; bl.Intensity=0.7; bl.Size=24; bl.Threshold=1; bl.Parent=Lighting; local cc=Lighting:FindFirstChild("NebulaColorCorrection") or Instance.new("ColorCorrectionEffect"); cc.Name="NebulaColorCorrection"; cc.Saturation=0.5; cc.Contrast=0.2; cc.TintColor=Visuals.NebulaThemeColor; cc.Parent=Lighting; local atm=Lighting:FindFirstChild("NebulaAtmosphere") or Instance.new("Atmosphere"); atm.Name="NebulaAtmosphere"; atm.Density=0.4; atm.Offset=0.25; atm.Glare=1; atm.Haze=2; atm.Color=Visuals.NebulaThemeColor; atm.Decay=Color3.fromRGB(173,216,230); atm.Parent=Lighting; Lighting.Ambient=Visuals.NebulaThemeColor; Lighting.OutdoorAmbient=Visuals.NebulaThemeColor; Lighting.FogStart=100; Lighting.FogEnd=500; Lighting.FogColor=Visuals.NebulaThemeColor else for _,nm in ipairs({"NebulaBloom","NebulaColorCorrection","NebulaAtmosphere"}) do local o=Lighting:FindFirstChild(nm); if o then o:Destroy() end end; Lighting.Ambient=Visuals.DefaultLighting.Ambient; Lighting.OutdoorAmbient=Visuals.DefaultLighting.OutdoorAmbient; Lighting.FogStart=Visuals.DefaultLighting.FogStart; Lighting.FogEnd=Visuals.DefaultLighting.FogEnd; Lighting.FogColor=Visuals.DefaultLighting.FogColor end end
    function Visuals.setFullBrightEnabled(en) Visuals.FullBrightEnabled=en; if not en then Lighting.Brightness=Visuals.DefaultLighting.Brightness; Lighting.GlobalShadows=Visuals.DefaultLighting.GlobalShadows; Lighting.OutdoorAmbient=Visuals.DefaultLighting.OutdoorAmbient; Lighting.ExposureCompensation=Visuals.DefaultLighting.ExposureCompensation end end
    function Visuals.setScreenEnabled(en) Visuals.ScreenEnabled=en; if en then if Visuals.ScreenConnection then Visuals.ScreenConnection:Disconnect() end; Visuals.ScreenConnection=game:GetService("RunService").RenderStepped:Connect(function() local cam=workspace.CurrentCamera; if cam then cam.CFrame=cam.CFrame*CFrame.new(0,0,0,1,0,0,0,0.65+Visuals.ScreenIntensity,0,0,0,1) end end) elseif Visuals.ScreenConnection then Visuals.ScreenConnection:Disconnect(); Visuals.ScreenConnection=nil end end
    function Visuals.toggleAnimeImage(en) Visuals.AnimeImageEnabled=en; if en then if Visuals.AnimeImageGui then Visuals.AnimeImageGui:Destroy() end; local g=Instance.new("ScreenGui"); g.Name="AnimeImageGui"; g.ResetOnSpawn=false; g.Parent=game.Players.LocalPlayer:WaitForChild("PlayerGui"); local img=Instance.new("ImageLabel"); img.Name="AnimeImage"; img.Image="http://www.roblox.com/asset/?id=117783035423570"; img.Size=UDim2.new(0,350,0,400); img.Position=UDim2.new(1,-25,0,10); img.AnchorPoint=Vector2.new(1,0); img.BackgroundTransparency=1; img.Parent=g; Visuals.AnimeImageGui=g elseif Visuals.AnimeImageGui then Visuals.AnimeImageGui:Destroy(); Visuals.AnimeImageGui=nil end end

    -- Respawn reapply
    function vReapply(c) task.wait(1); if Visuals.HatEnabled then Visuals.addHat(c) end; if Visuals.TrailEnabled then Visuals.addTrail(c) end; if Visuals.ForceFieldEnabled then Visuals.applyForceField(c) end; if Visuals.AuraEnabled then Visuals.enableAura(c) end; if Visuals.SkinTrailEnabled then Visuals.toggleSkinTrail(true) end; if Visuals.AnimeImageEnabled then Visuals.toggleAnimeImage(true) end end
    game.Players.LocalPlayer.CharacterAdded:Connect(vReapply)
    if game.Players.LocalPlayer.Character then task.defer(function() vReapply(game.Players.LocalPlayer.Character) end) end

    -- Heartbeat
    game:GetService("RunService").Heartbeat:Connect(function()
        if Visuals.HatEnabled then Visuals.updateHats() end
        if Visuals.TrailEnabled then Visuals.updateTrails() end
        if Visuals.ForceFieldEnabled then Visuals.updateForceField() end
        if Visuals.WorldTimeEnabled then Lighting.ClockTime=Visuals.WorldTimeValue end
        if Visuals.FullBrightEnabled then Lighting.Brightness=3; Lighting.GlobalShadows=false; Lighting.OutdoorAmbient=Color3.new(1,1,1); Lighting.ExposureCompensation=0.3 end
    end)

    -- в”Ђв”Ђ UI в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    local VisHatTrail=Tabs.Visuals:CreateBlock({Name="Hat & Trail",Side="Left"})
    local VisSkinAura=Tabs.Visuals:CreateBlock({Name="Skin & Aura",Side="Right"})
    local VisWorld=Tabs.Visuals:CreateBlock({Name="World",Side="Left"})
    local VisScreen=Tabs.Visuals:CreateBlock({Name="Screen & Other",Side="Right"})

    VisHatTrail:CreateToggle({Name="Chinese Hat",Flag="VisualHat",Default=false,Callback=function(v) Visuals.HatEnabled=v; if v and game.Players.LocalPlayer.Character then Visuals.addHat(game.Players.LocalPlayer.Character) elseif game.Players.LocalPlayer.Character then Visuals.removeHat(game.Players.LocalPlayer.Character) end end})
    VisHatTrail:CreateToggle({Name="Rainbow Hat",Flag="VisualHatRainbow",Default=false,Callback=function(v) Visuals.HatRainbow=v end})
    VisHatTrail:CreateSlider({Name="Hat Transparency",Flag="VisualHatTrans",Min=0,Max=100,Default=30,Callback=function(v) Visuals.HatTransparency=v/100 end})
    VisHatTrail:CreateToggle({Name="Trail",Flag="VisualTrail",Default=false,Callback=function(v) Visuals.TrailEnabled=v; if v and game.Players.LocalPlayer.Character then Visuals.addTrail(game.Players.LocalPlayer.Character) elseif game.Players.LocalPlayer.Character then Visuals.removeTrail(game.Players.LocalPlayer.Character) end end})
    VisHatTrail:CreateToggle({Name="Trail Gradient Mode",Flag="VisualTrailGrad",Default=false,Callback=function(v) Visuals.TrailGradient=v; if Visuals.TrailEnabled and game.Players.LocalPlayer.Character then Visuals.addTrail(game.Players.LocalPlayer.Character) end end})
    VisHatTrail:CreateToggle({Name="Trail Rainbow",Flag="VisualTrailRainbow",Default=false,Callback=function(v) Visuals.TrailRainbow=v end})
    VisHatTrail:CreateSlider({Name="Trail Lifetime",Flag="VisualTrailLife",Min=1,Max=30,Default=5,Callback=function(v) Visuals.TrailLifetime=v/10 end})
    VisHatTrail:CreateSlider({Name="Trail Transparency",Flag="VisualTrailTrans",Min=0,Max=100,Default=0,Callback=function(v) Visuals.TrailTransparencyStart=v/100 end})

    VisSkinAura:CreateToggle({Name="ForceField Skin",Flag="VisualFF",Default=false,Callback=function(v) Visuals.ForceFieldEnabled=v; local c=game.Players.LocalPlayer.Character; if c then if v then Visuals.applyForceField(c) else Visuals.removeForceField(c) end end end})
    VisSkinAura:CreateToggle({Name="Rainbow ForceField",Flag="VisualFFRainbow",Default=false,Callback=function(v) Visuals.ForceFieldRainbow=v end})
    VisSkinAura:CreateToggle({Name="Skin Trail",Flag="VisualSkinTrail",Default=false,Callback=function(v) Visuals.SkinTrailEnabled=v; Visuals.toggleSkinTrail(v) end})
    VisSkinAura:CreateSlider({Name="Skin Trail Life",Flag="VisualSkinTrailLife",Min=1,Max=30,Default=5,Callback=function(v) Visuals.SkinTrailLife=v/10; if Visuals.SkinTrailEnabled then Visuals.updateSkinTrail() end end})
    VisSkinAura:CreateToggle({Name="Local Aura",Flag="VisualAura",Default=false,Callback=function(v) Visuals.AuraEnabled=v; if v then if not Visuals.CurrentAuraModel then Visuals.updateAuraLogic() end; local c=game.Players.LocalPlayer.Character; if c then Visuals.enableAura(c) end else Visuals.disableAura() end end})
    do
        local ai={} for k in pairs(Visuals.AuraModels) do table.insert(ai,k) end; table.sort(ai)
        VisSkinAura:CreateDropdown({Name="Aura Type",Flag="VisualAuraType",Items=ai,Default="Godly",Callback=function(v) Visuals.AuraType=v; Visuals.CustomAuraID=""; if Visuals.AuraEnabled then Visuals.updateAuraLogic() end end})
    end
    VisSkinAura:CreateInput({Name="Custom Aura ID",Flag="VisualCustomAura",Default="",Placeholder="Asset ID...",Finished=true,Callback=function(v) Visuals.CustomAuraID=v:match("^%s*(.-)%s*$") or ""; if Visuals.AuraEnabled and Visuals.CustomAuraID~="" then Visuals.updateAuraLogic() end end})

    do
        local si={} for k in pairs(Visuals.SkyboxAssets) do table.insert(si,k) end; table.sort(si)
        VisWorld:CreateDropdown({Name="Skybox",Flag="VisualSkybox",Items=si,Default="HD",Callback=function(v) Visuals.CurrentSkybox=v; Visuals.CustomSkyEnabled=true; Visuals.applySkybox(v) end})
    end
    VisWorld:CreateToggle({Name="Enable Custom Skybox",Flag="VisualSkyboxToggle",Default=false,Callback=function(v) Visuals.CustomSkyEnabled=v; if v then Visuals.applySkybox(Visuals.CurrentSkybox) else Visuals.restoreDefaultSky() end end})
    VisWorld:CreateToggle({Name="Nebula Theme",Flag="VisualNebula",Default=false,Callback=function(v) Visuals.setNebulaEnabled(v) end})
    VisWorld:CreateToggle({Name="Full Bright",Flag="VisualFullBright",Default=false,Callback=function(v) Visuals.setFullBrightEnabled(v) end})
    VisWorld:CreateToggle({Name="Time Changer",Flag="VisualTimeToggle",Default=false,Callback=function(v) Visuals.WorldTimeEnabled=v end})
    VisWorld:CreateSlider({Name="World Time (0-24)",Flag="VisualTimeVal",Min=0,Max=24,Default=12,Callback=function(v) Visuals.WorldTimeValue=v end})
    VisWorld:CreateSlider({Name="FOV",Flag="VisualFOV",Min=40,Max=120,Default=70,Callback=function(v) local cam=workspace.CurrentCamera; if cam then cam.FieldOfView=v end end})

    VisScreen:CreateToggle({Name="Screen Stretch Effect",Flag="VisualScreenFX",Default=false,Callback=function(v) Visuals.setScreenEnabled(v) end})
    VisScreen:CreateSlider({Name="Screen Intensity",Flag="VisualScreenInt",Min=0,Max=20,Default=0,Callback=function(v) Visuals.ScreenIntensity=v/100 end})
    VisScreen:CreateToggle({Name="Anime Image",Flag="VisualAnimeImg",Default=false,Callback=function(v) Visuals.toggleAnimeImage(v) end})

    -- =====================================================================
    -- CUSTOM EFFECTS BLOCK  (dropdown selector)
    -- =====================================================================
    local VisEffects = Tabs.Visuals:CreateBlock({Name="Custom Effects", Side="Left"})

    -- =====================================================================
    -- All effect start/stop functions
    -- =====================================================================
    Visuals.FX = {}
    Visuals.FX.ActiveEffect  = "None"
    Visuals.FX.ActiveEnabled = false
    Visuals.FX.Connections   = {}
    Visuals.FX.Parts         = {}

    function FX_cleanup()
        for _, c in ipairs(Visuals.FX.Connections) do pcall(function() c:Disconnect() end) end
        Visuals.FX.Connections = {}
        for _, p in ipairs(Visuals.FX.Parts) do pcall(function() p:Destroy() end) end
        Visuals.FX.Parts = {}
    end

    local FX_defs = {}

    -- в”Ђв”Ђ 1. Orbit Rings в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Orbit Rings"] = function()
        local char = Player.Character
        local hrp  = char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        local ringData, count = {}, 3
        for i = 1, count do
            local ring = Instance.new("Part")
            ring.Name="XOCUFXPart"; ring.Size=Vector3.new(7,.18,.18)
            ring.Material=Enum.Material.Neon; ring.CanCollide=false
            ring.CanTouch=false; ring.CanQuery=false; ring.Massless=true
            ring.Anchored=true; ring.Parent=char
            table.insert(Visuals.FX.Parts, ring)
            table.insert(ringData,{
                part=(ring),
                offset=(i-1)*(math.pi*2/count),
                tilt=(i-1)*(math.pi/count),
            })
        end
        local t=0
        local conn = R.Heartbeat:Connect(function(dt)
            t=t+dt*2.2
            local h2 = Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
            if not h2 then return end
            for _,d in ipairs(ringData) do
                local ang=t+d.offset
                d.part.Color=Color3.fromHSV(((t*.08+d.offset)%(math.pi*2))/(math.pi*2),1,1)
                d.part.CFrame=h2.CFrame*CFrame.Angles(d.tilt,0,0)*CFrame.Angles(0,ang,0)*CFrame.new(3.6,0,0)*CFrame.Angles(0,math.pi/2,0)
            end
        end)
        table.insert(Visuals.FX.Connections, conn)
    end

    -- в”Ђв”Ђ 2. Lightning Body в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Lightning Body"] = function()
        local char = Player.Character
        if not char then return end
        local limbs={"HumanoidRootPart","Head","Left Arm","Right Arm","Left Leg","Right Leg"}
        for _,name in ipairs(limbs) do
            local part=char:FindFirstChild(name)
            if part and part:IsA("BasePart") then
                local a0=Instance.new("Attachment"); a0.Position=Vector3.new(0,part.Size.Y/2,0); a0.Parent=part
                local a1=Instance.new("Attachment"); a1.Position=Vector3.new(0,-part.Size.Y/2,0); a1.Parent=part
                local bolt=Instance.new("Beam")
                bolt.Attachment0=a0; bolt.Attachment1=a1; bolt.FaceCamera=true
                bolt.Width0=.06; bolt.Width1=.06; bolt.Segments=12
                bolt.LightEmission=1; bolt.LightInfluence=0
                bolt.TextureLength=1; bolt.TextureSpeed=4
                bolt.Color=ColorSequence.new({ColorSequenceKeypoint.new(0,Color3.fromRGB(120,60,255)),ColorSequenceKeypoint.new(.5,Color3.fromRGB(200,160,255)),ColorSequenceKeypoint.new(1,Color3.fromRGB(120,60,255))})
                bolt.Transparency=NumberSequence.new({NumberSequenceKeypoint.new(0,.2),NumberSequenceKeypoint.new(.5,0),NumberSequenceKeypoint.new(1,.2)})
                bolt.Parent=part
                table.insert(Visuals.FX.Parts,a0); table.insert(Visuals.FX.Parts,a1); table.insert(Visuals.FX.Parts,bolt)
                local alive=true
                table.insert(Visuals.FX.Connections,{Disconnect=function() alive=false end})
                task.spawn(function()
                    while alive and bolt and bolt.Parent do
                        bolt.Segments=math.random(6,18); bolt.Width0=math.random(3,9)/100; bolt.Width1=bolt.Width0
                        task.wait(math.random(2,8)/100)
                    end
                end)
            end
        end
    end

    -- в”Ђв”Ђ 3. Glitch Effect в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Glitch Effect"] = function()
        local conn = R.Heartbeat:Connect(function()
            local char=Player.Character
            local hrp=char and char:FindFirstChild("HumanoidRootPart")
            if not hrp or hrp.Anchored then return end
            if math.random(1,8)==1 then
                local orig=hrp.CFrame
                hrp.CFrame=orig+Vector3.new((math.random()-.5)*.55,(math.random()-.5)*.3,(math.random()-.5)*.55)
                task.defer(function() if hrp and hrp.Parent then hrp.CFrame=orig end end)
            end
        end)
        table.insert(Visuals.FX.Connections, conn)
    end

    -- в”Ђв”Ђ 4. Fire Aura в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Fire Aura"] = function()
        local char=Player.Character
        if not char then return end
        local targets={"HumanoidRootPart","Head","Left Arm","Right Arm","Left Leg","Right Leg"}
        for _,name in ipairs(targets) do
            local p=char:FindFirstChild(name)
            if p then
                local fire=Instance.new("Fire")
                fire.Size=4; fire.Heat=6
                fire.Color=Color3.fromRGB(255,80,0)
                fire.SecondaryColor=Color3.fromRGB(255,200,0)
                fire.Parent=p
                table.insert(Visuals.FX.Parts,fire)
            end
        end
    end

    -- в”Ђв”Ђ 5. Rainbow Body в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Rainbow Body"] = function()
        local char=Player.Character
        if not char then return end
        local origColors={}
        for _,p in ipairs(char:GetChildren()) do
            if p:IsA("BasePart") then origColors[p]=p.Color end
        end
        local t=0
        local conn=R.Heartbeat:Connect(function(dt)
            t=t+dt*.5
            local c2=Player.Character
            if not c2 then return end
            for i,p in ipairs(c2:GetChildren()) do
                if p:IsA("BasePart") then
                    p.Color=Color3.fromHSV((t+i*.1)%1,1,1)
                    p.Material=Enum.Material.Neon
                end
            end
        end)
        table.insert(Visuals.FX.Connections,conn)
        -- restore on cleanup via parts table (store sentinel)
        local sentinel={_restore=origColors, Destroy=function(self)
            local c=Player.Character
            if not c then return end
            for _,p in ipairs(c:GetChildren()) do
                if p:IsA("BasePart") then
                    p.Color=self._restore[p] or Color3.fromRGB(163,162,165)
                    p.Material=Enum.Material.SmoothPlastic
                end
            end
        end}
        table.insert(Visuals.FX.Parts, sentinel)
    end

    -- в”Ђв”Ђ 6. Bubble Shield в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Bubble Shield"] = function()
        local char=Player.Character
        local hrp=char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        local sphere=Instance.new("Part")
        sphere.Name="XOCUFXPart"; sphere.Size=Vector3.new(8,8,8)
        sphere.Shape=Enum.PartType.Ball
        sphere.Material=Enum.Material.Glass
        sphere.Transparency=0.65
        sphere.Color=Color3.fromRGB(100,200,255)
        sphere.CanCollide=false; sphere.CanTouch=false; sphere.CanQuery=false
        sphere.Massless=true; sphere.Anchored=true; sphere.CastShadow=false
        sphere.Parent=char
        table.insert(Visuals.FX.Parts, sphere)
        local t=0
        local conn=R.Heartbeat:Connect(function(dt)
            t=t+dt
            local h2=Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
            if not h2 then return end
            sphere.CFrame=h2.CFrame
            sphere.Color=Color3.fromHSV((t*.15)%1,0.6,1)
            sphere.Transparency=0.55+math.sin(t*3)*.1
        end)
        table.insert(Visuals.FX.Connections, conn)
    end

    -- в”Ђв”Ђ 7. Star Burst в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Star Burst"] = function()
        local char=Player.Character
        local hrp=char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        local starData={}
        local count=8
        for i=1,count do
            local s=Instance.new("Part")
            s.Name="XOCUFXPart"; s.Size=Vector3.new(.4,.4,.4)
            s.Material=Enum.Material.Neon; s.Shape=Enum.PartType.Ball
            s.CanCollide=false; s.CanTouch=false; s.CanQuery=false
            s.Massless=true; s.Anchored=true; s.Parent=char
            table.insert(Visuals.FX.Parts,s)
            table.insert(starData,{part=s, phase=(i-1)*(math.pi*2/count), radius=3+math.random()*2, height=math.sin((i-1)*1.2)*2})
        end
        local t=0
        local conn=R.Heartbeat:Connect(function(dt)
            t=t+dt*3
            local h2=Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
            if not h2 then return end
            for _,d in ipairs(starData) do
                local ang=t+d.phase
                local x=math.cos(ang)*d.radius
                local z=math.sin(ang)*d.radius
                local y=math.sin(t*1.5+d.phase)*d.height
                d.part.Color=Color3.fromHSV(((t*.05+d.phase)%(math.pi*2))/(math.pi*2),1,1)
                d.part.CFrame=h2.CFrame*CFrame.new(x,y,z)
                local scale=.3+math.sin(t*2+d.phase)*.15
                d.part.Size=Vector3.new(scale,scale,scale)
            end
        end)
        table.insert(Visuals.FX.Connections,conn)
    end

    -- в”Ђв”Ђ 8. Ice Shards в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Ice Shards"] = function()
        local char=Player.Character
        local hrp=char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        local shards={}
        for i=1,6 do
            local s=Instance.new("Part")
            s.Name="XOCUFXPart"; s.Size=Vector3.new(.3,1.4+math.random()*.8,.3)
            s.Material=Enum.Material.Ice; s.Color=Color3.fromRGB(180,230,255)
            s.Transparency=0.25; s.CanCollide=false; s.CanTouch=false
            s.CanQuery=false; s.Massless=true; s.Anchored=true; s.Parent=char
            table.insert(Visuals.FX.Parts,s)
            table.insert(shards,{part=s, phase=(i-1)*(math.pi*2/6), r=2.5+math.random()})
        end
        local t=0
        local conn=R.Heartbeat:Connect(function(dt)
            t=t+dt*.8
            local h2=Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
            if not h2 then return end
            for _,d in ipairs(shards) do
                local ang=t+d.phase
                local x=math.cos(ang)*d.r; local z=math.sin(ang)*d.r
                d.part.CFrame=h2.CFrame*CFrame.new(x,-1,z)*CFrame.Angles(0,ang,math.pi*.18)
            end
        end)
        table.insert(Visuals.FX.Connections,conn)
    end

    -- в”Ђв”Ђ 9. Shadow Clones в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Shadow Clones"] = function()
        local char=Player.Character
        if not char then return end
        local clones={}
        local offsets={
            Vector3.new(-3,0,0), Vector3.new(3,0,0),
            Vector3.new(0,0,-3), Vector3.new(0,0,3),
        }
        for i,off in ipairs(offsets) do
            local clone=char:Clone()
            clone.Name="XOCUShadowClone"
            -- strip scripts/humanoid from clone so it's just visual
            for _,v in ipairs(clone:GetDescendants()) do
                if v:IsA("Script") or v:IsA("LocalScript") or v:IsA("Humanoid") then v:Destroy() end
            end
            for _,p in ipairs(clone:GetDescendants()) do
                if p:IsA("BasePart") then
                    p.Transparency=0.65; p.Color=Color3.fromRGB(30,0,60)
                    p.Material=Enum.Material.Neon; p.Anchored=true
                    p.CanCollide=false; p.CanTouch=false; p.CanQuery=false
                end
            end
            clone.Parent=workspace
            table.insert(Visuals.FX.Parts,clone)
            table.insert(clones,{model=clone, off=off})
        end
        local conn=R.Heartbeat:Connect(function()
            local h2=Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
            if not h2 then return end
            for _,d in ipairs(clones) do
                local hrpClone=d.model:FindFirstChild("HumanoidRootPart")
                if hrpClone then hrpClone.CFrame=h2.CFrame+d.off end
                -- sync all parts
                for _,origP in ipairs(Player.Character:GetChildren()) do
                    if origP:IsA("BasePart") then
                        local cp=d.model:FindFirstChild(origP.Name)
                        if cp then cp.CFrame=origP.CFrame+(d.off) end
                    end
                end
            end
        end)
        table.insert(Visuals.FX.Connections,conn)
    end

    -- в”Ђв”Ђ 10. Meteor Rain в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Meteor Rain"] = function()
        local alive=true
        local sentinel={Destroy=function() alive=false end}
        table.insert(Visuals.FX.Parts,sentinel)
        task.spawn(function()
            while alive and Visuals.FX.ActiveEnabled do
                local hrp=Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
                if hrp then
                    local ox=(math.random()-.5)*20
                    local oz=(math.random()-.5)*20
                    local m=Instance.new("Part")
                    m.Name="XOCUMeteor"; m.Size=Vector3.new(1,1,1)
                    m.Shape=Enum.PartType.Ball
                    m.Material=Enum.Material.Neon
                    m.Color=Color3.fromRGB(255,math.random(50,150),0)
                    m.CanCollide=false; m.CanTouch=false; m.CanQuery=false
                    m.Anchored=false
                    m.CFrame=CFrame.new(hrp.Position+Vector3.new(ox,25,oz))
                    m.AssemblyLinearVelocity=Vector3.new(0,-80,0)
                    m.Parent=workspace
                    game:GetService("Debris"):AddItem(m,2)
                end
                task.wait(.12)
            end
        end)
    end

    -- =====================================================================
    -- Dropdown + Toggle to select & enable any effect
    -- =====================================================================
    local FX_names = {}
    for k in pairs(FX_defs) do table.insert(FX_names, k) end
    table.sort(FX_names)
    table.insert(FX_names, 1, "None")

    Visuals.FX.ActiveEffect = "None"

    VisEffects:CreateDropdown({
        Name    = "Select Effect",
        Flag    = "VisualFXSelect",
        Items   = FX_names,
        Default = "None",
        Callback = function(v)
            Visuals.FX.ActiveEffect = v
            -- If already enabled, hot-swap to new effect
            if Visuals.FX.ActiveEnabled then
                FX_cleanup()
                if v ~= "None" and FX_defs[v] then FX_defs[v]() end
            end
        end,
    })

    VisEffects:CreateToggle({
        Name    = "Enable Effect",
        Flag    = "VisualFXEnable",
        Default = false,
        Callback = function(v)
            Visuals.FX.ActiveEnabled = v
            if v then
                local name = Visuals.FX.ActiveEffect
                if name and name ~= "None" and FX_defs[name] then
                    FX_cleanup()
                    FX_defs[name]()
                    -- Re-apply on respawn
                    local respawnConn
                    respawnConn = Player.CharacterAdded:Connect(function()
                        task.wait(0.5)
                        if Visuals.FX.ActiveEnabled and Visuals.FX.ActiveEffect == name then
                            FX_cleanup()
                            FX_defs[name]()
                        else
                            respawnConn:Disconnect()
                        end
                    end)
                    table.insert(Visuals.FX.Connections, respawnConn)
                end
            else
                FX_cleanup()
            end
        end,
    })
end
-- Create a new block in your Visuals Tab
local BlackHoleSettings = Tabs.Visuals:CreateBlock({Name = "Black Hole Customizer", Side = "Right"})
-- =========================================================================
-- BLACK HOLE SETTINGS & CONFIGURATION
-- =========================================================================

local BHK_Settings = {
    ColorMode      = "Default",
    NeonGlow       = false,
    Silent         = false,
    ReverbEnabled  = true,
    BeamWidth0     = 1,
    BeamWidth1     = 1,
    BillboardSize  = 10,
    HideBillboard  = false,
    RainbowActive  = false,
    RainbowConn    = nil,
    WatcherConn    = nil,
    BeamTransparency = 0,
}

local customBH = false
local bhConnection = nil

local BHK_ColorTable = {
    ["Default"]   = { hole = Color3.fromRGB(0,   0,   0),   beam = ColorSequence.new(Color3.fromRGB(170, 0, 255)), gui = Color3.fromRGB(150, 0, 255) },
    ["White Hole"]= { hole = Color3.fromRGB(255, 255, 255), beam = ColorSequence.new(Color3.fromRGB(255,255,255)), gui = Color3.fromRGB(255,255,255) },
    ["Red Hole"]  = { hole = Color3.fromRGB(180,  0,   0),  beam = ColorSequence.new(Color3.fromRGB(255, 50, 50)), gui = Color3.fromRGB(200, 30, 30) },
    ["Blue Hole"] = { hole = Color3.fromRGB(0,   50, 180),  beam = ColorSequence.new(Color3.fromRGB(50, 120,255)), gui = Color3.fromRGB(30,  80,220) },
    ["Green Hole"]= { hole = Color3.fromRGB(0,  120,  30),  beam = ColorSequence.new(Color3.fromRGB(50, 255,100)), gui = Color3.fromRGB(20, 180, 60) },
    ["Gold Hole"] = { hole = Color3.fromRGB(180,140,   0),  beam = ColorSequence.new(Color3.fromRGB(255,220, 50)), gui = Color3.fromRGB(220,180, 20) },
    ["Cyan Hole"] = { hole = Color3.fromRGB(0,  180, 200),  beam = ColorSequence.new(Color3.fromRGB(50, 230,255)), gui = Color3.fromRGB(0,  200,230) },
    ["Pink Hole"] = { hole = Color3.fromRGB(220, 50, 180),  beam = ColorSequence.new(Color3.fromRGB(255,100,220)), gui = Color3.fromRGB(230, 60,200) },
}

-- =========================================================================
-- HELPER FUNCTIONS
-- =========================================================================

-- Legacy White Hole Visual Applier
local function applyWhiteHoleVisuals(model)
    if not model or not model:FindFirstChild("Hole") then return end
    local hole = model.Hole
    
    if _G.WhiteHoleEnabled then
        hole.Color = Color3.fromRGB(255, 255, 255)
        hole.Material = Enum.Material.Neon 
        
        local beam = hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
        if beam then
            beam.Color = ColorSequence.new(Color3.fromRGB(255, 255, 255))
        end
        
        local gui = hole:FindFirstChild("BillboardGui")
        if gui then
            if gui:FindFirstChild("Large") then gui.Large.ImageColor3 = Color3.fromRGB(255, 255, 255) end
            if gui:FindFirstChild("Small") then gui.Small.ImageColor3 = Color3.fromRGB(255, 255, 255) end
        end
    else
        hole.Color = Color3.fromRGB(0, 0, 0)
        hole.Material = Enum.Material.Plastic 
        
        local beam = hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
        if beam then
            beam.Color = ColorSequence.new(Color3.fromRGB(170, 0, 255)) 
        end
        
        local gui = hole:FindFirstChild("BillboardGui")
        if gui then
            if gui:FindFirstChild("Large") then gui.Large.ImageColor3 = Color3.fromRGB(150, 0, 255) end
            if gui:FindFirstChild("Small") then gui.Small.ImageColor3 = Color3.fromRGB(150, 0, 255) end
        end
    end
end

-- Realistic Black Hole Applier
local function applyRealisticBH(model)
    if not model then return end

    local success, realisticModel = pcall(function()
        return game:GetObjects("rbxassetid://16797584940")[1]
    end)
    if not success or not realisticModel then return end
    
    local hole = model:FindFirstChild("Hole")
    if not hole then return end

    for _, obj in pairs(realisticModel:GetDescendants()) do
        if obj:IsA("ParticleEmitter") or obj:IsA("Beam") or obj:IsA("Sound") or obj:IsA("Trail") then
            local clone = obj:Clone()
            clone.Parent = hole
        end
        if obj:IsA("BillboardGui") then
            local currentGui = hole:FindFirstChild("BillboardGui")
            if currentGui then
                local newGui = obj:Clone()
                newGui.Parent = hole
                if currentGui:FindFirstChild("Large") and newGui:FindFirstChild("Large") then
                    currentGui.Large.Image = newGui.Large.Image
                end
                if currentGui:FindFirstChild("Small") and newGui:FindFirstChild("Small") then
                    currentGui.Small.Image = newGui.Small.Image
                end
                newGui:Destroy()
            end
        end
    end
    
    realisticModel:Destroy()
end

-- Extra Visuals Main Applier
local function BHK_ApplyToModel(model)
    if not model then return end
    local hole = model:FindFirstChild("Hole")
    if not hole then return end

    -- Material
    hole.Material = BHK_Settings.NeonGlow and Enum.Material.Neon or Enum.Material.Plastic

    -- Color (skip if rainbow is managing it)
    if not BHK_Settings.RainbowActive then
        local ct = BHK_ColorTable[BHK_Settings.ColorMode]
        if ct then
            hole.Color = ct.hole
            local beam = hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
            if beam then beam.Color = ct.beam end
            local gui = hole:FindFirstChild("BillboardGui")
            if gui then
                if gui:FindFirstChild("Large")  then gui.Large.ImageColor3  = ct.gui end
                if gui:FindFirstChild("Small")  then gui.Small.ImageColor3  = ct.gui end
            end
        end
    end

    -- Beam width & transparency
    local beam = hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
    if beam then
        beam.Width0 = BHK_Settings.BeamWidth0
        beam.Width1 = BHK_Settings.BeamWidth1
        beam.Transparency = NumberSequence.new(BHK_Settings.BeamTransparency / 100)
    end

    -- Billboard size & visibility
    local gui = hole:FindFirstChild("BillboardGui")
    if gui then
        gui.Size = UDim2.new(BHK_Settings.BillboardSize, 0, BHK_Settings.BillboardSize, 0)
        gui.Enabled = not BHK_Settings.HideBillboard
    end

    -- Sounds
    local drone  = hole:FindFirstChild("Drone")
    local scream = hole:FindFirstChild("Scream")
    if drone  then drone.Volume  = BHK_Settings.Silent and 0 or 1 end
    if scream then scream.Volume = BHK_Settings.Silent and 0 or 1 end
    if scream then
        local reverb = scream:FindFirstChildOfClass("ReverbSoundEffect")
        if reverb then reverb.Enabled = BHK_Settings.ReverbEnabled end
    end
end

local function BHK_ApplyCurrent()
    BHK_ApplyToModel(workspace:FindFirstChild("BlackHoleKick"))
end

local function BHK_SetupWatcher()
    if BHK_Settings.WatcherConn then BHK_Settings.WatcherConn:Disconnect() end
    BHK_Settings.WatcherConn = workspace.ChildAdded:Connect(function(child)
        if child.Name == "BlackHoleKick" then
            task.wait(0.1)
            BHK_ApplyToModel(child)
        end
    end)
end

-- Initialize automated watcher
BHK_SetupWatcher()

-- =========================================================================
-- UI ELEMENTS / INTERFACE CONTROLS
-- =========================================================================

-- Legacy White Hole Toggle
BlackHoleSettings:CreateToggle({
    Name = "White Hole Mode",
    Flag = "WhiteHoleKickMode",
    Default = false,
    Callback = function(enabled)
        _G.WhiteHoleEnabled = enabled
        
        local current = workspace:FindFirstChild("BlackHoleKick")
        if current then
            applyWhiteHoleVisuals(current)
        end
        
        if _G.BlackHoleWatcher then _G.BlackHoleWatcher:Disconnect() end
        if enabled then
            _G.BlackHoleWatcher = workspace.ChildAdded:Connect(function(child)
                if child.Name == "BlackHoleKick" then
                    task.wait(0.1)
                    applyWhiteHoleVisuals(child)
                end
            end)
        end
    end
})

-- Realistic Black Hole Toggle
BlackHoleSettings:CreateToggle({
    Name = "Realistic Black Hole",
    Flag = "BHKRealisticMode",
    Default = false,
    Callback = function(Value)
        customBH = Value
        
        if Value then
            local current = workspace:FindFirstChild("BlackHoleKick")
            if current then
                applyRealisticBH(current)
            end
            
            bhConnection = workspace.ChildAdded:Connect(function(child)
                if child.Name == "BlackHoleKick" then
                    task.wait()
                    applyRealisticBH(child)
                end
            end)
        else
            if bhConnection then bhConnection:Disconnect(); bhConnection = nil end
        end
    end
})

-- Color Mode Dropdown
BlackHoleSettings:CreateDropdown({
    Name    = "Hole Color Mode",
    Flag    = "BHKColorMode",
    Items   = {"Default", "White Hole", "Red Hole", "Blue Hole", "Green Hole", "Gold Hole", "Cyan Hole", "Pink Hole"},
    Default = "Default",
    Callback = function(v)
        BHK_Settings.ColorMode = v
        BHK_Settings.RainbowActive = false
        if BHK_Settings.RainbowConn then BHK_Settings.RainbowConn:Disconnect(); BHK_Settings.RainbowConn = nil end
        BHK_ApplyCurrent()
    end
})

-- Neon Glow Toggle
BlackHoleSettings:CreateToggle({
    Name    = "Neon Glow",
    Flag    = "BHKNeonGlow",
    Default = false,
    Callback = function(v)
        BHK_Settings.NeonGlow = v
        BHK_ApplyCurrent()
    end
})

-- Rainbow Mode Toggle
BlackHoleSettings:CreateToggle({
    Name    = "Rainbow Mode",
    Flag    = "BHKRainbow",
    Default = false,
    Callback = function(v)
        BHK_Settings.RainbowActive = v
        if BHK_Settings.RainbowConn then BHK_Settings.RainbowConn:Disconnect(); BHK_Settings.RainbowConn = nil end
        if v then
            local hue = 0
            BHK_Settings.RainbowConn = game:GetService("RunService").Heartbeat:Connect(function(dt)
                hue = (hue + dt * 0.3) % 1
                local c = Color3.fromHSV(hue, 1, 1)
                local model = workspace:FindFirstChild("BlackHoleKick")
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
        end
    end
})

-- Beam Width Sliders
BlackHoleSettings:CreateSlider({
    Name    = "Beam Width (Inner)",
    Flag    = "BHKBeamW0",
    Min     = 0,
    Max     = 20,
    Default = 1,
    Callback = function(v)
        BHK_Settings.BeamWidth0 = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local beam = hole and hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
            if beam then beam.Width0 = v end
        end
    end
})

BlackHoleSettings:CreateSlider({
    Name    = "Beam Width (Outer)",
    Flag    = "BHKBeamW1",
    Min     = 0,
    Max     = 20,
    Default = 1,
    Callback = function(v)
        BHK_Settings.BeamWidth1 = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local beam = hole and hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
            if beam then beam.Width1 = v end
        end
    end
})

-- Beam Transparency Slider
BlackHoleSettings:CreateSlider({
    Name    = "Beam Transparency",
    Flag    = "BHKBeamTransp",
    Min     = 0,
    Max     = 100,
    Default = 0,
    Callback = function(v)
        BHK_Settings.BeamTransparency = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local beam = hole and hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
            if beam then
                beam.Transparency = NumberSequence.new(v / 100)
            end
        end
    end
})

-- Billboard Size Slider
BlackHoleSettings:CreateSlider({
    Name    = "Billboard Size",
    Flag    = "BHKBillboardSize",
    Min     = 2,
    Max     = 40,
    Default = 10,
    Callback = function(v)
        BHK_Settings.BillboardSize = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local gui  = hole and hole:FindFirstChild("BillboardGui")
            if gui then gui.Size = UDim2.new(v, 0, v, 0) end
        end
    end
})

-- Hide Billboard Toggle
BlackHoleSettings:CreateToggle({
    Name    = "Hide Billboard (Stealth)",
    Flag    = "BHKHideBillboard",
    Default = false,
    Callback = function(v)
        BHK_Settings.HideBillboard = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local gui  = hole and hole:FindFirstChild("BillboardGui")
            if gui then gui.Enabled = not v end
        end
    end
})

-- Silent Black Hole Toggle
BlackHoleSettings:CreateToggle({
    Name    = "Silent Black Hole",
    Flag    = "BHKSilent",
    Default = false,
    Callback = function(v)
        BHK_Settings.Silent = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole   = model:FindFirstChild("Hole")
            local drone  = hole and hole:FindFirstChild("Drone")
            local scream = hole and hole:FindFirstChild("Scream")
            if drone  then drone.Volume  = v and 0 or 1 end
            if scream then scream.Volume = v and 0 or 1 end
        end
    end
})

-- Reverb Toggle
BlackHoleSettings:CreateToggle({
    Name    = "Scream Reverb Effect",
    Flag    = "BHKReverb",
    Default = true,
    Callback = function(v)
        BHK_Settings.ReverbEnabled = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole   = model:FindFirstChild("Hole")
            local scream = hole and hole:FindFirstChild("Scream")
            if scream then
                local reverb = scream:FindFirstChildOfClass("ReverbSoundEffect")
                if reverb then reverb.Enabled = v end
            end
        end
    end
})
