-- filename: 
-- version: lua51
-- line: [0, 0] id: 0
print("blocked auto-update | .gg/25ms")

--[[local r0_0 = "1"
local r1_0, r2_0 = pcall(function()
  -- line: [0, 0] id: 168
  return game:HttpGet("https://pastebin.com/raw/QXM1ngn8")
end)
if r1_0 then
  if r2_0 ~= r0_0 then
    loadstring(game:HttpGet("https://raw.githubusercontent.com/BlizTBr/scripts/main/FTAP.lua"))()
    return 
    return 
  end
]]--

if getgenv().werutygf8342 == false then
  game:GetService("StarterGui"):SetCore("SendNotification", { 
	  Title = "antiblitz";
	  Text = "check console";
    Duration = 5;
  })

   print("===========================================")
   print("this crack was made by (test) | .gg/25ms")
   print("run script again to actually load it")
   print("===========================================")
   getgenv().werutygf8342 = true
   return
end

getgenv().key == "Xana"


if not getgenv().d84jdnmasjdh43d and game.PlaceId == 6961824067 then
  getgenv().d84jdnmasjdh43d = true
  if getgenv().key ~= "Xana" then
    keySystem = loadstring(game:HttpGet("https://raw.githubusercontent.com/BlizTBr/scripts/main/Key%20System"))()
  end
  local r3_0 = loadstring(game:HttpGet("https://raw.githubusercontent.com/BlizTBr/scripts/main/Orion%20X"))()
  local r4_0 = game:GetService("Players")
  local r5_0 = game:GetService("Debris")
  local r6_0 = game:GetService("Workspace")
  local r7_0 = game:GetService("Lighting")
  local r8_0 = game:GetService("TweenService")
  local r9_0 = game:GetService("UserInputService")
  local r10_0 = game:GetService("ReplicatedStorage")
  local r11_0 = game:GetService("ReplicatedFirst")
  local r12_0 = game:GetService("ContextActionService")
  local r13_0 = game:GetService("RunService")
  local r14_0 = game:GetService("VirtualUser")
  local r15_0 = r10_0:WaitForChild("CharacterEvents")
  local r16_0 = r4_0.LocalPlayer
  local r17_0 = r16_0:WaitForChild("PlayerGui")
  local r18_0 = r16_0:GetMouse()
  local r19_0 = r6_0:WaitForChild(r16_0.Name .. "SpawnedInToys")
  local r20_0 = r16_0:WaitForChild("InPlot")
  local r21_0 = r16_0:WaitForChild("ToysLimitCap")
  SpawnToyRF = r10_0:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
  DeleteToyRE = r10_0:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
  BuyToy = r10_0:WaitForChild("MenuToys"):WaitForChild("BuyToyRemoteFunction")
  BombEvents = r10_0:WaitForChild("BombEvents")
  typeAnimation = r11_0.Typing.Type
  flailAnimation = r11_0.ThrowPlayers.Flail
  local r22_0 = r10_0:WaitForChild("GrabEvents"):WaitForChild("CreateGrabLine")
  local r23_0 = r10_0:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
  local r24_0 = r10_0:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
  local r25_0 = r10_0:WaitForChild("GrabEvents"):WaitForChild("ExtendGrabLine")
  local r26_0 = r15_0:WaitForChild("RagdollRemote")
  local r27_0 = r10_0:WaitForChild("DataEvents"):WaitForChild("UpdateLineColorsEvent")
  local r28_0 = r16_0:WaitForChild("IsHeld")
  local r29_0 = r16_0:WaitForChild("PlayerScripts")
  local r30_0 = nil
  local r31_0 = r15_0:WaitForChild("Struggle")
  anticreatelinelocalscript = r29_0:WaitForChild("CharacterAndBeamMove")
  r16_0.Changed:Connect(function(r0_92)
    -- line: [0, 0] id: 92
    -- notice: unreachable block#3
    -- error: decompile function#92: generate_block
    -- 
    -- Caused by:
    --     0: generate_block(1)
    --     1: collect_logic_branch(1)
    --     2: Condition failed: `block.is_if() || block.is_while()`
  end)
  local function r32_0(r0_124)
    -- line: [0, 0] id: 124
    Chat:FireServer(r0_124, "All")
  end
  local r33_0 = nil
  local function r34_0(r0_302)
    -- line: [0, 0] id: 302
    r3_0:MakeNotification({
      Name = "Bliz_T HUB",
      Content = r0_302,
      Image = "rbxassetid://16570630989",
      Time = 5,
    })
  end
  local function r35_0()
    -- line: [0, 0] id: 21
    r3_0:MakeNotification({
      Name = "Bliz_T HUB",
      Content = "Only for premium users! Buy premium in my discord server!",
      Image = "rbxassetid://16570630989",
      Time = 2,
    })
  end
  function IsSolara()
    -- line: [0, 0] id: 19
    if getexecutorname then
      local r0_19 = getexecutorname()
      if r0_19 and string.find(r0_19, "Solara") then
        return true
      end
    end
  end
  IsUsingSolara = IsSolara()
  if IsUsingSolara then
    print("new proximity promp created!")
    getgenv().fireproximityprompt = function(r0_256)
      -- line: [0, 0] id: 256
      if r0_256.Name == "ProximityPrompt" then
        local r1_256 = r0_256.HoldDuration
        local r2_256 = r0_256.MaxActivationDistance
        r0_256.MaxActivationDistance = math.huge
        r0_256.HoldDuration = 0
        r0_256:InputHoldBegin()
        r0_256:InputHoldEnd()
        r0_256.HoldDuration = r1_256
        r0_256.MaxActivationDistance = r2_256
      else
        error("retard: " .. Obj.Name)
      end
    end
  end
  local r36_0 = {}
  function checkadminData(r0_191)
    -- line: [0, 0] id: 191
    if table.find(r36_0, r0_191) then
      return true
    end
  end
  spawnToyThread = coroutine.create(function()
    -- line: [0, 0] id: 160
    -- notice: unreachable block#2
    while true do
      local r0_160 = coroutine.yield()
      if typeof(r0_160) == "table" then
        SpawnToyRF:InvokeServer(unpack(r0_160))
      end
    end
  end)
  function SpawnToy(r0_333)
    -- line: [0, 0] id: 333
    coroutine.resume(spawnToyThread, r0_333)
  end
  local function r37_0(r0_297, r1_297)
    -- line: [0, 0] id: 297
    if typeof(r0_297) == "Instance" and r0_297.Parent then
      local r2_297 = r0_297:GetAttribute("LastTimeRankUpdate")
      if not r2_297 or r2_297 and 300 <= os.clock() - r2_297 then
        local r3_297 = r0_297:GetRankInGroup(r1_297)
       --[[ if r3_297 == 255 then
          r0_297:SetAttribute("Rank", "Leader")
        elseif r3_297 == 4 then
          r0_297:SetAttribute("Rank", "High Rank Admin")
        elseif r3_297 == 3 then
          r0_297:SetAttribute("Rank", "Low Rank Admin")
        elseif r3_297 == 2 then
          r0_297:SetAttribute("Rank", "Goon")
        elseif r3_297 == 0 or r3_297 == 1 then
          r0_297:SetAttribute("Rank", "Common")
        end]]--
        r0_297:SetAttribute("Rank", "Common")
        
        print("blitz_t is a total idiot and can control ur game, we prevent that.")

        r0_297:SetAttribute("LastTimeRankUpdate", os.clock())
      else
        
      end
    end
  end
  local function r38_0(r0_75)
    -- line: [0, 0] id: 75
    local r1_75 = nil	-- notice: implicit variable refs by block#[8, 9, 14, 15]
    if typeof(r0_75) == "Instance" then
      if r0_75:IsA("Model") and r0_75:FindFirstChildOfClass("Humanoid") and r4_0:GetPlayerFromCharacter(r0_75) then
        r1_75 = r4_0:GetPlayerFromCharacter(r0_75)
      elseif r0_75:IsA("Player") then
        r1_75 = r0_75
      else
        return 
      end
    end
    local r2_75 = false
    if r1_75 then
      local r3_75 = r37_0(r1_75, 16168861)
      if r3_75 == "Leader" or r3_75 == "High Rank Admin" or r3_75 == "Low Rank Admin" or r3_75 == "Goon" then
        r2_75 = false
      end
      if checkadminData(r1_75.Name) and not r36_0[r1_75.Name].Protection then
        r2_75 = false
      end
      return r2_75
    end
  end
  function tableAlphabeticOrder(r0_200, r1_200)
    -- line: [0, 0] id: 200
    return r0_200:lower() < r1_200:lower()
  end
  local function r39_0(r0_40)
    -- line: [0, 0] id: 40
    local r1_40 = {}
    for r5_40, r6_40 in pairs(r4_0:GetPlayers()) do
      if r6_40.UserId ~= r16_0.UserId then
        table.insert(r1_40, r6_40.Name .. " " .. "(" .. r6_40.DisplayName .. ")")
      end
    end
    table.sort(r1_40, tableAlphabeticOrder)
    r0_40:Refresh(r1_40, true)
  end
  local r40_0 = {}
  local r41_0 = {}
  local function r42_0(r0_13, r1_13)
    -- line: [0, 0] id: 13
    local r2_13 = {}
    for r6_13, r7_13 in pairs(r1_13) do
      if typeof(r7_13) == "string" then
        table.insert(r2_13, r7_13)
      end
    end
    r0_13:Refresh(r2_13, true)
  end
  local function r43_0(r0_251)
    -- line: [0, 0] id: 251
    local r1_251 = {}
    for r5_251, r6_251 in pairs(r4_0:GetPlayers()) do
      table.insert(r1_251, r6_251.Name .. " " .. "(" .. r6_251.DisplayName .. ")")
    end
    table.sort(r1_251, tableAlphabeticOrder)
    r0_251:Refresh(r1_251, true)
  end
  function lookAt(r0_173, r1_173)
    -- line: [0, 0] id: 173
    local r2_173 = (r1_173 - r0_173).Unit
    local r4_173 = r2_173:Cross(Vector3.new(0, 1, 0))
    return CFrame.fromMatrix(r0_173, r4_173, r4_173:Cross(r2_173))
  end
  local function r44_0(r0_287, r1_287, r2_287)
    -- line: [0, 0] id: 287
    if r0_287 == "Spawn Toy (TAB)" and r1_287 == Enum.UserInputState.Begin then
      SpawnToyRF:InvokeServer(unpack({
        [1] = _G.SelectedToy,
        [2] = r16_0.Character.CamPart.CFrame,
        [3] = Vector3.new(0, r16_0.Character.CamPart.Orientation.Y, 0),
      }))
    end
  end
  function teleportfunc()
    -- line: [0, 0] id: 131
    local r0_131 = _G.ControllingCreature or r16_0.Character
    local r1_131 = nil
    if _G.ControllingCreature then
      r1_131 = "Head"
    elseif r16_0.Character then
      r1_131 = "CamPart"
    end
    local r2_131 = Ray.new(r0_131[r1_131].Position, r16_0.Character.CamPart.CFrame.lookVector * 5000)
    local r3_131 = r6_0
    local r3_131, r4_131 = r3_131:FindPartOnRayWithIgnoreList(r2_131, {
      r0_131
    })
    if r3_131 then
      r0_131.HumanoidRootPart.CFrame = CFrame.new(r4_131.X, r4_131.Y + 5, r4_131.Z)
    end
  end
  local function r45_0(r0_373, r1_373, r2_373)
    -- line: [0, 0] id: 373
    if r0_373 == "Teleport(Z)" and r1_373 == Enum.UserInputState.Begin then
      teleportfunc()
    end
  end
  local function r46_0(r0_20)
    -- line: [0, 0] id: 20
    if table.find(r41_0, r0_20) then
      return true
    end
  end
  local r47_0 = nil
  local r48_0 = nil
  Noclip2 = nil
  Clip2 = nil
  local function r49_0()
    -- line: [0, 0] id: 23
    if not r47_0 then
      r48_0 = false
      r47_0 = r13_0.Stepped:Connect(function()
        -- line: [0, 0] id: 24
        if r48_0 == false and game.Players.LocalPlayer.Character ~= nil then
          for r3_24, r4_24 in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
            if r4_24:IsA("BasePart") and r4_24.CanCollide and r4_24.Name ~= floatName then
              r4_24.CanCollide = false
            end
          end
        end
        wait(0.21)
      end)
    end
  end
  local function r50_0()
    -- line: [0, 0] id: 298
    if not _G.NoclipToggle then
      if r47_0 then
        r47_0:Disconnect()
        r47_0 = nil
      end
      r48_0 = true
    end
  end
  function countToys(r0_45)
    -- line: [0, 0] id: 45
    local r1_45 = 0
    for r5_45, r6_45 in pairs(r19_0:GetChildren()) do
      if r6_45.Name == r0_45 then
        r1_45 = r1_45 + 1
      end
    end
    return r1_45
  end
  function CheckNetworkOwnerShipOnPlayer(r0_218, r1_218)
    -- line: [0, 0] id: 218
    if typeof(r0_218) == "Instance" and r0_218:IsA("Player") and r0_218.Character and r0_218.Character:FindFirstChild("Head") and r0_218.Character.Head:FindFirstChild("PartOwner") and r0_218.Character.Head.PartOwner.Value == r16_0.Name then
      if r1_218 then
        return r0_218.Character.Head.PartOwner
      end
      return true
    end
  end
  function CheckNetworkOwnerShipPermanentOnPlayer(r0_69, r1_69)
    -- line: [0, 0] id: 69
    if typeof(r0_69) == "Instance" and r0_69:IsA("Player") and r0_69.Character and r0_69.Character:FindFirstChild("HumanoidRootPart") and r0_69.Character.HumanoidRootPart:FindFirstChild("FirePlayerPart") and r0_69.Character.HumanoidRootPart.FirePlayerPart:FindFirstChild("PartOwner") and r0_69.Character.HumanoidRootPart.FirePlayerPart.PartOwner.Value == r16_0.Name then
      if r1_69 then
        return r0_69.Character.HumanoidRootPart.FirePlayerPart.PartOwner
      end
      return true
    end
  end
  function CheckNetworkOwnerShipOnPart(r0_310, r1_310)
    -- line: [0, 0] id: 310
    if typeof(r0_310) == "Instance" and r0_310:FindFirstChild("PartOwner") and r0_310.PartOwner.Value == r16_0.Name then
      if r1_310 then
        return r0_310.PartOwner
      end
      return true
    end
  end
  function SNOWship(r0_252)
    -- line: [0, 0] id: 252
    if r0_252 and typeof(r0_252) == "Instance" then
      local r1_252 = r16_0:DistanceFromCharacter(r0_252.Position)
      if r16_0.Character and r16_0.Character:FindFirstChild("HumanoidRootPart") and r1_252 <= 30 then
        r24_0:FireServer(r0_252, lookAt(r16_0.Character.HumanoidRootPart.Position, r0_252.Position))
      end
    end
  end
  function IsPlayerInsideSafeZone(r0_201)
    -- line: [0, 0] id: 201
    if typeof(r0_201) == "Instance" and r0_201:IsA("Player") and r0_201:FindFirstChild("InPlot") and r0_201.InPlot.Value then
      return true
    end
  end
  function IsPlayerFloating(r0_103)
    -- line: [0, 0] id: 103
    if typeof(r0_103) == "Instance" and r0_103:IsA("Player") and r0_103.Character and r0_103.Character:FindFirstChildOfClass("Humanoid") and r0_103.Character:FindFirstChildOfClass("Humanoid").FloorMaterial == Enum.Material.Air then
      return true
    end
  end
  function SNOWshipOnce(r0_189)
    -- line: [0, 0] id: 189
    local r1_189 = r16_0:DistanceFromCharacter(r0_189.Position)
    if r16_0.Character and r16_0.Character:FindFirstChild("HumanoidRootPart") then
      if CheckNetworkOwnerShipOnPart(r0_189) then
        return true
      end
      if r1_189 <= 30 then
        r24_0:FireServer(r0_189, lookAt(r16_0.Character.HumanoidRootPart.Position, r0_189.Position))
      end
    end
  end
  function SNOWshipOnceAndDelete(r0_120)
    -- line: [0, 0] id: 120
    local r1_120 = r16_0:DistanceFromCharacter(r0_120.Position)
    local r2_120 = r0_120:GetAttribute("Connected")
    local r3_120 = r0_120:GetAttribute("CreatedConnected")
    if r16_0.Character and r16_0.Character:FindFirstChild("HumanoidRootPart") then
      if CheckNetworkOwnerShipOnPart(r0_120) then
        r0_120:SetAttribute("Connected", true)
        r23_0:FireServer(r0_120)
        if not r3_120 then
          r0_120:SetAttribute("CreatedConnected", true)
          print("Create Connection")
          r0_120.ChildAdded:Connect(function(r0_121)
            -- line: [0, 0] id: 121
            if r0_121.Name == "PartOwner" and r0_121.Value ~= r16_0.Name then
              r0_120:SetAttribute("Connected", false)
            end
          end)
        end
      elseif r1_120 <= 30 and not r2_120 then
        r24_0:FireServer(r0_120, lookAt(r16_0.Character.HumanoidRootPart.Position, r0_120.Position))
      end
    end
  end
  function SNOWshipPlayer(r0_359, r1_359)
    -- line: [0, 0] id: 359
    if r16_0.Character and r16_0.Character:FindFirstChild("HumanoidRootPart") and typeof(r0_359) == "Instance" and r0_359:IsA("Player") and r0_359.Character and r0_359.Character:FindFirstChild("HumanoidRootPart") then
      local r2_359 = r0_359.Character.HumanoidRootPart
      local r3_359 = r16_0:DistanceFromCharacter(r2_359.Position)
      if CheckNetworkOwnerShipOnPlayer(r0_359) then
        if type(r1_359) == "function" then
          r1_359()
        end
        return true
      end
      if r3_359 <= 30 then
        r24_0:FireServer(r2_359, lookAt(r16_0.Character.HumanoidRootPart.Position, r2_359.Position))
      end
    end
  end
  function SNOWshipPermanentPlayer(r0_36, r1_36)
    -- line: [0, 0] id: 36
    if r16_0.Character and r16_0.Character:FindFirstChild("HumanoidRootPart") and typeof(r0_36) == "Instance" and r0_36:IsA("Player") and r0_36.Character and r0_36.Character:FindFirstChild("HumanoidRootPart") and r0_36.Character.HumanoidRootPart:FindFirstChild("FirePlayerPart") then
      local r2_36 = r0_36.Character.HumanoidRootPart.FirePlayerPart
      local r3_36 = r16_0:DistanceFromCharacter(r2_36.Position)
      if type(r1_36) == "function" then
        r1_36()
      end
      if r3_36 <= 30 then
        r24_0:FireServer(r2_36, lookAt(r16_0.Character.HumanoidRootPart.Position, r2_36.Position))
        return true
      end
    end
  end
  function GetPlayerCharacter()
    -- line: [0, 0] id: 222
    if r16_0.Character and r16_0.Character:FindFirstChild("HumanoidRootPart") and r16_0.Character:FindFirstChildOfClass("Humanoid") then
      return r16_0.Character
    end
  end
  function TeleportPlayer(r0_65)
    -- line: [0, 0] id: 65
    local r1_65 = GetPlayerCharacter()
    local r2_65 = nil
    local r3_65 = nil
    if r1_65 and not _G.TeleportingToNetworkOwnership and typeof(r0_65) == "CFrame" then
      r3_65 = r1_65.HumanoidRootPart
      r2_65 = r1_65:FindFirstChildOfClass("Humanoid")
      r3_65.CFrame = r3_65.CFrame.Rotation + r0_65.Position
      if r2_65.SeatPart == nil or tostring(r2_65.SeatPart.Parent) ~= "CreatureBlobman" then
        r2_65.Sit = false
      end
    end
  end
  function GetPlayerCFrame()
    -- line: [0, 0] id: 248
    local r0_248 = GetPlayerCharacter()
    if r0_248 then
      return r0_248.HumanoidRootPart.CFrame
    end
  end
  function GetPlayerRoot()
    -- line: [0, 0] id: 277
    local r0_277 = GetPlayerCharacter()
    if r0_277 then
      return r0_277.HumanoidRootPart
    end
  end
  function Getdistancefromcharacter(r0_295)
    -- line: [0, 0] id: 295
    return r16_0:DistanceFromCharacter(r0_295)
  end
  AnchoredObjects = {}
  CompiledGroups = {}
  local r51_0 = Instance.new("Attachment")
  local r52_0 = Instance.new("Sound", r51_0)
  local r53_0 = Instance.new("ParticleEmitter", r51_0)
  r52_0.Name = "soundeffect"
  r52_0.SoundId = "rbxassetid://1091083826"
  r53_0.LightInfluence = 1
  r53_0.Lifetime = NumberRange.new(2, 3)
  r53_0.Texture = "rbxassetid://15668608167"
  r53_0.Transparency = NumberSequence.new(0, 1)
  r53_0.Speed = NumberRange.new(6, 6)
  r53_0.Size = NumberSequence.new(0, 1)
  r53_0.SpreadAngle = Vector2.new(360, 360)
  r53_0.Rate = 20
  r53_0.Enabled = false
  r53_0.Name = "particle"
  function anchorobjecteffect(r0_356)
    -- line: [0, 0] id: 356
    local r1_356 = r51_0:Clone()
    r1_356.Parent = r0_356
    r1_356.soundeffect:Play()
    r1_356.particle:Emit(25)
    r5_0:AddItem(r1_356)
  end
  function autosetownership()
    -- line: [0, 0] id: 272
    for r3_272, r4_272 in pairs(AnchoredObjects) do
      if typeof(r4_272.PartAnchored) == "Instance" and not r3_272:GetAttribute("AnchorOwnership") and SNOWshipOnce(r4_272.PartAnchored) then
        r3_272:SetAttribute("AnchorOwnership", true)
      end
    end
  end
  NilModel = Instance.new("Model", r6_0)
  function unAnchorObject(r0_341)
    -- line: [0, 0] id: 341
    if typeof(r0_341) == "Instance" and r0_341.Parent and (r0_341.Parent:IsA("Model") or r0_341.Parent:IsA("Folder")) then
      local r1_341 = r0_341
      local r2_341 = r1_341.Parent
      local r3_341 = r2_341:GetAttribute("IsAnchored")
      local r4_341 = r2_341:GetAttribute("GluePrimary")
      if r2_341:IsA("Folder") or r2_341 == r6_0 then
        r2_341 = r1_341
      end
      if AnchoredObjects[r2_341] and r3_341 then
        local r5_341 = AnchoredObjects[r2_341]
        r5_341.BodyPosition.Parent = r2_341
        r5_341.BodyGyro.Parent = r2_341
        if not r4_341 then
          r5_341.HighLightAnchor.Adornee = NilModel
        end
        r5_341.PartAnchored = nil
        for r9_341, r10_341 in pairs(r5_341.Connections) do
          r10_341:Disconnect()
        end
        r5_341.Connections = nil
        r2_341:SetAttribute("IsAnchored", false)
        print("UnAnchored")
      end
    end
  end
  function setanchorObject(r0_114)
    -- line: [0, 0] id: 114
    if typeof(r0_114) == "Instance" and r0_114.Parent and (r0_114.Parent:IsA("Model") or r0_114.Parent:IsA("Folder")) then
      local r1_114 = r0_114
      local r2_114 = r1_114.Parent
      if r2_114:IsA("Folder") or r2_114 == r6_0 then
        r2_114 = r1_114
      end
      local r3_114 = r2_114:GetAttribute("IsAnchored")
      local r4_114 = r2_114:GetAttribute("Glue") and not r2_114:GetAttribute("GluePrimary")
      if not r3_114 and not r4_114 then
        local r5_114 = r2_114:FindFirstChild("AnchorPositionBody") or r1_114:FindFirstChild("AnchorPositionBody") or Instance.new("BodyPosition")
        local r6_114 = r2_114:FindFirstChild("AnchorGyroBody") or r1_114:FindFirstChild("AnchorGyroBody") or Instance.new("BodyGyro")
        local r7_114 = r2_114:FindFirstChild("AnchorHighlight") or Instance.new("Highlight", r2_114)
        local r8_114 = {}
        local r9_114 = Vector3.new(math.huge, math.huge, math.huge)
        local r10_114 = Vector3.new(0, 0, 0)
        local r11_114 = r1_114.Position
        local r12_114 = nil
        local r13_114 = Color3.new(0, 1, 0)
        local r14_114 = Color3.fromRGB(255, 218, 58)
        local r15_114 = Color3.fromRGB(255, 130, 58)
        local r16_114 = Color3.new(0, 0, 0.5)
        r5_114.Name = "AnchorPositionBody"
        r5_114.Position = r1_114.Position
        r5_114.Parent = r1_114
        r6_114.Name = "AnchorGyroBody"
        r6_114.Parent = r1_114
        r6_114.CFrame = r1_114.CFrame
        r6_114.D = 950
        r6_114.P = 40000
        r5_114.P = 40000
        r5_114.D = 950
        r7_114.Name = "AnchorHighlight"
        r7_114.Parent = r2_114
        r7_114.Adornee = r2_114
        r7_114.FillTransparency = 0.7
        r7_114.DepthMode = Enum.HighlightDepthMode.Occluded
        r7_114.Enabled = true
        local function r17_114()
          -- line: [0, 0] id: 119
          if r2_114:GetAttribute("IsAnchored") or r2_114:GetAttribute("Glue") then
            r6_114.MaxTorque = r9_114
            r5_114.MaxForce = r9_114
          end
          if r2_114:GetAttribute("GluePrimary") and not r2_114:GetAttribute("IsAnchored") then
            r7_114.FillColor = r13_114
            r7_114.OutlineColor = r13_114
          elseif r2_114:GetAttribute("Glue") and not r2_114:GetAttribute("IsAnchored") then
            r7_114.OutlineColor = r14_114
            r7_114.FillColor = r15_114
          else
            r7_114.FillColor = r16_114
            r7_114.OutlineColor = r16_114
          end
        end
        local function r18_114()
          -- line: [0, 0] id: 117
          r6_114.MaxTorque = Vector3.new()
          r5_114.MaxForce = Vector3.new()
          r7_114.FillColor = Color3.new(1, 0, 0)
          r7_114.OutlineColor = Color3.new(1, 0, 0)
          r2_114:SetAttribute("AnchorOwnership", false)
        end
        r8_114[1] = r2_114.DescendantAdded:Connect(function(r0_118)
          -- line: [0, 0] id: 118
          if r0_118.Name == "PartOwner" then
            if r0_118.Value == r16_0.Name then
              r12_114 = r0_118
              r17_114()
            else
              r18_114()
            end
          end
        end)
        r8_114[2] = r2_114.DescendantRemoving:Connect(function(r0_116)
          -- line: [0, 0] id: 116
          if r0_116.Name == "PartOwner" and r0_116.Value == r16_0.Name and r0_116.Value == r16_0.Name then
            r12_114 = nil
            r17_114()
          end
        end)
        task.spawn(function()
          -- line: [0, 0] id: 115
          while r5_114.Parent do
            local r0_115 = r2_114:GetAttribute("Glue")
            if not r0_115 then
              r0_115 = r2_114:GetAttribute("IsAnchored")
              if r0_115 then
                r6_114.MaxTorque = r9_114
                r0_115 = r5_114
                r0_115.MaxForce = r9_114
              else
                r6_114.MaxTorque = r10_114
                r0_115 = r5_114
                r0_115.MaxForce = r10_114
              end
              r5_114.Position = r11_114 + Vector3.new(0, 0.001, 0)
              task.wait()
              r0_115 = r5_114
              r0_115.Position = r11_114
            else
              break
            end
          end
          print("breaked")
        end)
        AnchoredObjects[r2_114] = {
          BodyPosition = r5_114,
          BodyGyro = r6_114,
          PartAnchored = r1_114,
          HighLightAnchor = r7_114,
          Connections = r8_114,
          Model = r2_114,
        }
        anchorobjecteffect(r1_114)
        r2_114:SetAttribute("IsAnchored", true)
        r17_114()
        -- close: r5_114
      else
        unAnchorObject(r0_114)
      end
      -- close: r1_114
    end
  end
  function anchorfunc()
    -- line: [0, 0] id: 340
    local r0_340 = r6_0:FindFirstChild("GrabParts")
    local r1_340 = nil
    if r0_340 then
      r1_340 = r0_340.GrabPart.WeldConstraint.Part1
      if r1_340 and not r1_340:IsDescendantOf(r6_0.Map) and not r1_340.Anchored then
        setanchorObject(r1_340)
      end
    end
  end
  function anchorobject(r0_374, r1_374, r2_374)
    -- line: [0, 0] id: 374
    if r0_374 == "AnchorK" and r1_374 == Enum.UserInputState.Begin then
      anchorfunc()
    end
  end
  local function r54_0(r0_232)
    -- line: [0, 0] id: 232
    for r4_232, r5_232 in ipairs(CompiledGroups) do
      if r5_232.primaryPart and r5_232.primaryPart == r0_232 then
        for r9_232, r10_232 in ipairs(r5_232.group) do
          if r10_232.model ~= r0_232 then
            local r11_232 = r10_232.bodypos
            local r12_232 = r10_232.bodygyro
            local r13_232 = r0_232.PrimaryPart or r0_232:FindFirstChildOfClass("BasePart")
            if r13_232 and r0_232 then
              if r11_232 then
                r11_232.P = 40000
                r11_232.D = 200
                r11_232.Position = (r13_232.CFrame * r10_232.offset).Position
                task.wait()
                r11_232.Position = r11_232.Position + Vector3.new(0, 0.002, 0)
              end
              if r12_232 then
                r12_232.P = 40000
                r12_232.D = 200
                r12_232.CFrame = r13_232.CFrame * r10_232.offset
              end
            end
          end
        end
      end
    end
  end
  function IsHoldingAnchoredPart()
    -- line: [0, 0] id: 169
    local r0_169 = r6_0:FindFirstChild("GrabParts")
    local r1_169 = nil
    local r2_169 = nil
    if r0_169 then
      r1_169 = r0_169.GrabPart.WeldConstraint.Part1
      if r1_169 then
        for r6_169, r7_169 in pairs(AnchoredObjects) do
          if r1_169:IsDescendantOf(r6_169) then
            r2_169 = r7_169.Model
            break
          end
        end
      end
    end
    return r2_169
  end
  function IsHoldingPrimaryCompiledObject()
    -- line: [0, 0] id: 17
    local r0_17 = r6_0:FindFirstChild("GrabParts")
    local r1_17 = nil
    local r2_17 = nil
    if r0_17 then
      r1_17 = r0_17.GrabPart.WeldConstraint.Part1
      if r1_17 then
        for r6_17, r7_17 in pairs(AnchoredObjects) do
          if r1_17:IsDescendantOf(r6_17) and r6_17:GetAttribute("GluePrimary") then
            r2_17 = true
            break
          end
        end
      end
    end
    return r2_17
  end
  function CreateNoCollisionConstraintsCompile(r0_336)
    -- line: [0, 0] id: 336
    for r4_336, r5_336 in ipairs(CompiledGroups) do
      if r5_336.primaryPart and r5_336.primaryPart == r0_336 then
        local r6_336 = r5_336
        for r10_336, r11_336 in pairs(r5_336.group) do
          local r12_336 = r11_336.model
          if r12_336 == r0_336 and r12_336 and r0_336 then
            for r16_336, r17_336 in ipairs(r12_336:GetChildren()) do
              if r17_336:IsA("BasePart") then
                for r21_336, r22_336 in pairs(r5_336.group) do
                  for r27_336, r28_336 in ipairs(r22_336.model:GetChildren()) do
                    if r28_336:IsA("BasePart") then
                      local r29_336 = Instance.new("NoCollisionConstraint", r17_336)
                      r29_336.Part0 = r17_336
                      r29_336.Part1 = r28_336
                      r29_336.Enabled = true
                      table.insert(r5_336.Nc_Group, r29_336)
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
  function IsInCompileGroup(r0_77)
    -- line: [0, 0] id: 77
    local r1_77 = false
    for r5_77, r6_77 in ipairs(CompiledGroups) do
      if r6_77.primaryPart then
        for r10_77, r11_77 in pairs(r6_77.group) do
          local r12_77 = r11_77.model
          if r12_77 and r12_77 == r0_77 and (r12_77:GetAttribute("Glue") or r12_77:GetAttribute("GluePrimary")) and not r12_77:GetAttribute("IsAnchored") then
            r1_77 = true
            break
          end
        end
      end
    end
    return r1_77
  end
  function CheckPrimaryPartOnCompileGroup(r0_267)
    -- line: [0, 0] id: 267
    local r1_267 = false
    for r5_267, r6_267 in ipairs(CompiledGroups) do
      if r6_267.primaryPart and r6_267.primaryPart == r0_267 and r6_267.primaryPart:GetAttribute("IsAnchored") then
        r1_267 = true
        break
      end
    end
    return r1_267
  end
  function RemoveCompileGroup(r0_110)
    -- line: [0, 0] id: 110
    for r4_110, r5_110 in ipairs(CompiledGroups) do
      if r5_110.primaryPart and r5_110.primaryPart == r0_110 then
        for r9_110, r10_110 in pairs(r5_110.Nc_Group) do
          r10_110:Destroy()
        end
        for r9_110, r10_110 in pairs(r5_110.gC) do
          r10_110:Disconnect()
          print("Disconnected!")
        end
        for r9_110, r10_110 in pairs(r5_110.group) do
          local r11_110 = r10_110.model
          r11_110:SetAttribute("Glue", false)
          r11_110:SetAttribute("GluePrimary", false)
          r11_110:SetAttribute("IsAnchored", false)
        end
        table.remove(CompiledGroups, r4_110)
      end
    end
  end
  local function r55_0()
    -- line: [0, 0] id: 257
    local r0_257 = 0
    local r1_257 = {}
    for r5_257, r6_257 in pairs(AnchoredObjects) do
      if not IsInCompileGroup(r5_257) then
        r0_257 = r0_257 + 1
      end
    end
    print(r0_257)
    if r0_257 == 0 then
      r3_0:MakeNotification({
        Name = "Error",
        Content = "No anchored parts found",
        Image = "rbxassetid://4483345998",
        Time = 5,
      })
      return 
    end
    if r0_257 == 1 then
      r3_0:MakeNotification({
        Name = "Error",
        Content = "Needs at least 2 anchored objects",
        Image = "rbxassetid://4483345998",
        Time = 5,
      })
      return 
    end
    local r2_257 = IsHoldingAnchoredPart()
    if not r2_257 then
      r3_0:MakeNotification({
        Name = "Error",
        Content = "You need to hold one of your anchored object",
        Image = "rbxassetid://4483345998",
        Time = 5,
      })
      return 
    end
    r3_0:MakeNotification({
      Name = "Success",
      Content = "Compiled " .. r0_257 .. " Toys together",
      Image = "rbxassetid://4483345998",
      Time = 5,
    })
    for r6_257, r7_257 in pairs(AnchoredObjects) do
      local r8_257 = IsInCompileGroup(r6_257)
      if not r8_257 then
        r8_257 = CheckPrimaryPartOnCompileGroup(r6_257)
        if r8_257 then
          RemoveCompileGroup(r6_257)
        end
      end
    end
    local r3_257 = {}
    for r7_257, r8_257 in pairs(AnchoredObjects) do
      local r9_257 = r8_257.Model
      local r10_257 = r8_257.BodyPosition
      local r11_257 = r8_257.BodyGyro
      if not IsInCompileGroup(r9_257) then
        local r12_257 = r8_257.PartAnchored
        local r13_257 = r2_257.PrimaryPart.CFrame:toObjectSpace(r12_257.CFrame)
        r9_257:SetAttribute("IsAnchored", false)
        if r9_257 == r2_257 then
          r8_257.BodyGyro.MaxTorque = Vector3.new()
          r8_257.BodyPosition.MaxForce = Vector3.new()
          r9_257:SetAttribute("GluePrimary", true)
          r8_257.HighLightAnchor.OutlineColor = Color3.new(0, 1, 0)
          r8_257.HighLightAnchor.FillColor = Color3.new(0, 1, 0)
        else
          r9_257:SetAttribute("Glue", true)
          r8_257.HighLightAnchor.OutlineColor = Color3.fromRGB(255, 218, 58)
          r8_257.HighLightAnchor.FillColor = Color3.fromRGB(255, 130, 58)
        end
        table.insert(r3_257, {
          model = r9_257,
          part = r12_257,
          offset = r13_257,
          bodypos = r10_257,
          bodygyro = r11_257,
        })
      end
    end
    table.insert(CompiledGroups, {
      primaryPart = r2_257,
      group = r3_257,
      Nc_Group = {},
      gC = r1_257,
    })
    local r4_257 = r13_0.Heartbeat:Connect(function()
      -- line: [0, 0] id: 258
      r54_0(r2_257)
    end)
    CreateNoCollisionConstraintsCompile(r2_257)
    table.insert(r1_257, r4_257)
  end
  function fireBombs(r0_146, r1_146, r2_146)
    -- line: [0, 0] id: 146
    if r0_146 == "FireBomb" and r1_146 == Enum.UserInputState.Begin then
      _G.FireBomb = true
    elseif r0_146 == "FireBomb" and r1_146 == Enum.UserInputState.End then
      _G.FireBomb = false
    end
  end
  function GodModeFTry(r0_10, r1_10, r2_10)
    -- line: [0, 0] id: 10
    if r0_10 == "Godmode" and r1_10 == Enum.UserInputState.Begin then
      _G.GodModeTrying = true
      local r3_10 = GetPlayerCharacter()
      local r4_10 = nil
      if r3_10 then
        r4_10 = r3_10:FindFirstChild("HumanoidRootPart")
      end
      if r4_10 then
        while _G.GodModeTrying do
          r26_0:FireServer(r4_10, 0)
          wait(0)
        end
      end
    elseif r0_10 == "Godmode" and r1_10 == Enum.UserInputState.End then
      _G.GodModeTrying = false
    end
  end
  _G.ControllingCreature = nil
  function makeCharacterNotGrabbable(r0_18)
    -- line: [0, 0] id: 18
    for r4_18, r5_18 in pairs(r0_18:GetChildren()) do
      if r5_18:IsA("Part") then
        r5_18.CanQuery = false
      end
    end
  end
  function makeCharacterGrabbable(r0_178)
    -- line: [0, 0] id: 178
    for r4_178, r5_178 in pairs(r0_178:GetChildren()) do
      if r5_178:IsA("Part") then
        r5_178.CanQuery = true
      end
    end
  end
  controlsoundeffect = Instance.new("Sound", r6_0)
  controlsoundeffect.SoundId = "rbxassetid://9126228625"
  controlsoundeffect.PlaybackSpeed = 1.25
  controleffectsatur = Instance.new("ColorCorrectionEffect", r7_0)
  controleffectsatur.Enabled = false
  controltween1 = r8_0:Create(r6_0.CurrentCamera, TweenInfo.new(0.3, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, 0, true), {
    FieldOfView = 120,
  })
  controltween2 = r8_0:Create(controleffectsatur, TweenInfo.new(0.3, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {
    TintColor = Color3.fromRGB(210, 218, 255),
  })
  controltween3 = r8_0:Create(controleffectsatur, TweenInfo.new(1, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {
    Brightness = -0.1,
  })
  controltween4 = r8_0:Create(controleffectsatur, TweenInfo.new(0.3, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {
    TintColor = Color3.new(1, 1, 1),
    Brightness = 0,
  })
  function controlcreatureeffectIn()
    -- line: [0, 0] id: 348
    controleffectsatur.Enabled = true
    controleffectsatur.TintColor = Color3.new()
    controltween1:Play()
    controltween2:Play()
    controlsoundeffect:Play()
    controltween2.Completed:Once(function()
      -- line: [0, 0] id: 349
      controltween3:Play()
    end)
  end
  function controlcreatureeffectOut()
    -- line: [0, 0] id: 127
    controltween4:Play()
    controltween4.Completed:Once(function()
      -- line: [0, 0] id: 128
      controleffectsatur.Enabled = false
    end)
  end
  function controlCreature(r0_206)
    -- line: [0, 0] id: 206
    if typeof(r0_206) == "Instance" and r0_206:IsA("Model") then
      local r1_206 = r0_206
      local r2_206 = r1_206:FindFirstChildOfClass("Humanoid")
      local r3_206 = r1_206:FindFirstChild("HumanoidRootPart")
      local r4_206 = r1_206:FindFirstChild("Head")
      local r5_206 = nil
      local r6_206 = (function()
        -- line: [0, 0] id: 207
        if not r4_0:GetPlayerFromCharacter(r0_206) and (r0_206.Name == "YouDecoy" or r0_206.Name == "CreatureBlobman" or tostring(r0_206.Parent.Name) == "Robloxians") then
          return true
        end
      end)()
      if r3_206 then
        r5_206 = r3_206
      end
      if r1_206 and r2_206 and r5_206 then
        local r7_206 = {}
        local function r8_206()
          -- line: [0, 0] id: 208
          for r3_208, r4_208 in pairs(r7_206) do
            if typeof(r4_208) == "RBXScriptConnection" then
              r4_208:Disconnect()
              print("Desconectado!")
            end
          end
          table.clear(r7_206)
        end
        _G.ControllingCreature = r1_206
        r2_206.WalkSpeed = 0
        r2_206.JumpPower = 24
        r2_206.CameraOffset = Vector3.new(0, 0, -0.7)
        r7_206[1] = r2_206.Died:Connect(function()
          -- line: [0, 0] id: 210
          _G.ControllingCreature = nil
        end)
        local r9_206 = nil
        local r10_206 = nil
        local r11_206 = nil
        local r12_206 = nil
        local r13_206 = Instance.new("BodyVelocity", r3_206)
        local r14_206 = Instance.new("BodyVelocity")
        r14_206.MaxForce = Vector3.new(0, math.huge, 0)
        r14_206.Velocity = Vector3.new()
        r13_206.MaxForce = Vector3.new(math.huge, 0, math.huge)
        makeCharacterNotGrabbable(r1_206)
        task.spawn(function()
          -- line: [0, 0] id: 209
          r49_0()
          while r1_206.Parent do
            local r0_209 = _G.ControllingCreature
            if r0_209 ~= nil then
              r0_209 = r6_206
              if r0_209 then
                SNOWshipOnceAndDelete(r4_206)
              else
                SNOWshipOnce(r4_206)
              end
              r2_206.AutoRotate = true
              task.wait()
            else
              break
            end
          end
        end)
        r6_0.CurrentCamera.CameraSubject = r2_206
        controlcreatureeffectIn()
        local r15_206 = GetPlayerCharacter()
        r9_206 = r15_206
        if r9_206 then
          r10_206 = r9_206:FindFirstChildOfClass("Humanoid")
          r11_206 = r9_206:FindFirstChild("HumanoidRootPart")
          r14_206.Parent = r11_206
          r7_206[2] = r10_206.Died:Connect(function()
            -- line: [0, 0] id: 215
            _G.ControllingCreature = nil
          end)
          r7_206[3] = r9_0.JumpRequest:Connect(function()
            -- line: [0, 0] id: 211
            r2_206:ChangeState("Jumping")
          end)
          r7_206[5] = r10_206.Changed:Connect(function(r0_212)
            -- line: [0, 0] id: 212
            if r0_212 == "MoveDirection" then
              r13_206.Velocity = r10_206.MoveDirection * 20
            end
          end)
          r7_206[6] = workspace.CurrentCamera.Changed:Connect(function(r0_214)
            -- line: [0, 0] id: 214
            if r0_214 == "CameraSubject" then
              r6_0.CurrentCamera.CameraSubject = r2_206
            end
          end)
          r15_206 = nil
          r7_206[7] = r4_206.Changed:Connect(function(r0_213)
            -- line: [0, 0] id: 213
            if r0_213 == "CFrame" then
              r15_206 = r6_0.CurrentCamera.CFrame.lookVector
              r2_206.CameraOffset = -Vector3.new(r15_206.X, 5, r15_206.Z) * 1.7
            end
          end)
          r2_206:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
          -- close: r15_206
        end
        while true do
          r15_206 = r1_206.Parent
          if r15_206 then
            r15_206 = _G
            r15_206 = r15_206.ControllingCreature
            if r15_206 ~= nil and r9_206 then
              r15_206 = r9_206.Parent
              if r15_206 then
                r15_206 = TeleportPlayer
                r15_206(CFrame.new(r3_206.Position + Vector3.new(0, -10, 0)))
                r15_206 = task
                r15_206 = r15_206.wait
                r15_206()
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
        r15_206 = r8_206
        r15_206()
        r15_206 = r50_0
        r15_206()
        r15_206 = TeleportPlayer
        r15_206(CFrame.new(r3_206.Position + Vector3.new(5, 15, 5)))
        r15_206 = makeCharacterGrabbable
        r15_206(r1_206)
        r13_206:Destroy()
        r14_206:Destroy()
        r15_206 = r6_0
        r15_206 = r15_206.CurrentCamera
        r15_206.CameraSubject = r10_206
        r15_206 = _G
        r15_206.ControllingCreature = nil
        r15_206 = Vector3
        r15_206 = r15_206.new
        r15_206 = r15_206()
        r11_206.Velocity = r15_206
        r15_206 = controlcreatureeffectOut
        r15_206()
        -- close: r7_206
      end
      -- close: r1_206
    end
  end
  CharacterRaycastFilter = RaycastParams.new()
  CharacterRaycastFilter.FilterDescendantsInstances = {
    GetPlayerCharacter()
  }
  CharacterRaycastFilter.FilterType = Enum.RaycastFilterType.Exclude
  function controlBindF()
    -- line: [0, 0] id: 216
    local r0_216 = GetPlayerCharacter()
    if r0_216 then
      local r3_216 = r0_216:FindFirstChildOfClass("Humanoid")
      local r4_216 = nil
      r4_216 = r6_0:Raycast(r0_216.Head.Position, r6_0.CurrentCamera.CFrame.lookVector * 50, CharacterRaycastFilter)
      if r4_216 and r3_216 and 0 < r3_216.Health then
        local r5_216 = r4_216.Instance.Parent
        print(r4_216.Instance, r5_216)
        local r6_216 = r5_216:FindFirstChildOfClass("Humanoid")
        local r7_216 = nil
        if r6_216 then
          if r4_0:GetPlayerFromCharacter(r5_216) and getgenv().key ~= "Xana" then
            r34_0("Only premium users can control players! Buy premium in my discord server!")
            return 
          end
          controlCreature(r5_216)
        end
      end
    end
  end
  function controlBind(r0_2, r1_2, r2_2)
    -- line: [0, 0] id: 2
    if r0_2 == "Control(C)" and r1_2 == Enum.UserInputState.Begin then
      if not _G.ControllingCreature then
        controlBindF()
      else
        _G.ControllingCreature = nil
      end
    end
  end
  _G.PlayerToLongGrab = nil
  _G.TargetAura = nil
  _G.SuperStrength = nil
  _G.AntiGrab = nil
  _G.AntiExplosion = nil
  _G.AntiBurn = nil
  _G.Poison_Grab = nil
  _G.Burn_Grab = nil
  _G.Radiactive_Grab = nil
  _G.Death_Grab = nil
  _G.SuperSpeed = nil
  _G.InfiniteJump = nil
  _G.TeleportKey = nil
  _G.KickAura = nil
  _G.KickAuraDebounce = nil
  getgenv().Multiplier = 0.15
  _G.Strength = nil
  local function r56_0(r0_192, r1_192)
    -- line: [0, 0] id: 192
    local r2_192 = nil	-- notice: implicit variable refs by block#[4]
    for r6_192, r7_192 in pairs(r1_192) do
      if r7_192 == r0_192 then
        r2_192 = true
        break
      end
    end
    return r2_192
  end
  local r57_0 = {
    "HumanoidRootPart",
    "Head",
    "Torso",
    "Left Arm",
    "Right Arm",
    "Left Leg",
    "Right Leg"
  }
  local function r58_0(r0_31)
    -- line: [0, 0] id: 31
    if type(r0_31) == "string" then
      local r1_31 = nil
      local r2_31 = r37_0(r16_0, 16168861)
      if r0_31:lower() == "all" or r0_31:lower() == r16_0.Name:sub(1, r0_31:len()):lower() then
        r1_31 = false
      end
      if r2_31 == "High Rank Admin" or r2_31 == "Low Rank Admin" or r2_31 == "Leader" then
        r1_31 = false
      end
      return r1_31
    end
  end
  if isfile("sblist.txt") then
    for r63_0, r64_0 in pairs(string.split(readfile("sblist.txt"), "\n")) do
      if r64_0 == game.JobId then
        while true do
          print("L")
        end
      end
    end
  end
  function DevJoinEffect()
    print("a blitz_t dev joined but he cant do anything to u lol")
  end
  local function r59_0(r0_226, r1_226, r2_226)
    -- line: [0, 0] id: 226
    if r1_226 == "LowRank" and getgenv().key == "Xana" then
      return 
    end
    r0_226 = string.split(r0_226, " ")
    local r3_226 = r0_226[1]:lower()
    if r58_0(r0_226[2]) then
      if r1_226 == "Leader" and r3_226 == ":premium" then
        getgenv().key = "Xana"
      end
      if r1_226 == "HighRank" or r1_226 == "Leader" then
        if r3_226 == ":kick" then
          print("L to the devs lol")
        elseif r3_226 == ":ban" then
          if not isfile("sblist.txt") then
          else
          end
        end
      end
      if r1_226 == "LowRank" or r1_226 == "HighRank" or r1_226 == "Leader" then
        if r3_226 == ":kill" then
          --r16_0.Character:FindFirstChildOfClass("Humanoid").Health = 0
        elseif r3_226 == ":freeze" then
          --_G.FreezeLoop = true
          while _G.FreezeLoop do
            local r4_226 = r16_0.Character:FindFirstChild("HumanoidRootPart")
            if r4_226 then
             -- r4_226 = r16_0.Character.HumanoidRootPart
              --r4_226.Anchored = true
            end
            task.wait()
          end
        elseif r3_226 == ":unfreeze" then
         -- _G.FreezeLoop = false
         -- r16_0.Character.HumanoidRootPart.Anchored = false
        elseif r3_226 == ":loopkill" then
         -- _G.DevLoopKillCMD = true
          while _G.DevLoopKillCMD do
         --   local r4_226 = r16_0.Character:FindFirstChildOfClass("Humanoid")
            if r4_226 then
        --      r4_226 = r16_0.Character.Humanoid
         --     r4_226.Health = 0
            end
            task.wait()
          end
        elseif r3_226 == ":unloopkill" then
         -- _G.DevLoopKillCMD = false
        elseif r3_226 == ":reveal" then
          warn("a blitz_t admin tried revealing u, we prevented that.")
        elseif r3_226 == ":chat" then
         -- local r4_226 = nil
          for r8_226 = 3, #r0_226, 1 do
            if r4_226 then
            --  r4_226 = r4_226 .. " " .. r0_226[r8_226]
            else
            --  r4_226 = r0_226[r8_226]
            end
          end
          for r8_226 = 0, #r4_226, 1 do
           -- wait(0.05)
          end
         -- Chat:FireServer(r4_226, "All")
        elseif r3_226 == ":bring" then
          --TeleportPlayer(r4_0[r2_226].Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, 5))
        end
      end
    end
    if r3_226 == ":antigrab" then
      --r36_0[r2_226].AntiGrab = true
    elseif r3_226 == ":unantigrab" then
     -- r36_0[r2_226].AntiGrab = false
    elseif r3_226 == ":p" then
    --  print("Protection Actived!")
     -- r36_0[r2_226].Protection = true
    elseif r3_226 == ":unp" then
      --print("Protection Desactived!")
     -- r36_0[r2_226].Protection = false
    end
  end
  local function r60_0(r0_303, r1_303)
    -- line: [0, 0] id: 303
    if type(r0_303) == "string" and type(r1_303) == "string" then
      r0_303 = {
        Message = r0_303,
        FromSpeaker = r4_0:FindFirstChild(r1_303),
      }
      local r2_303, r3_303 = string.find(r0_303.Message, ":")
      if r2_303 then
        r0_303.Message = string.sub(r0_303.Message, r2_303, r0_303.Message:len())
      end
      r1_303 = r0_303.FromSpeaker
      if r1_303 then
        local r4_303 = r37_0(r1_303, 16168861)
        if r4_303 == "Leader" then
        --  r59_0(r0_303.Message, "Leader", r1_303.Name)
        elseif r4_303 == "High Rank Admin" then
       --   r59_0(r0_303.Message, "HighRank", r1_303.Name)
        elseif r4_303 == "Low Rank Admin" then
       --   r59_0(r0_303.Message, "LowRank", r1_303.Name)
        end
      end
    end
  end
  task.spawn(function()
    -- line: [0, 0] id: 235
    while task.wait(1) do
      local r0_235 = pairs
      for r3_235, r4_235 in r0_235(r4_0:GetPlayers()) do
        if r4_235 ~= r16_0 and r38_0(r4_235) and not r4_235:GetAttribute("Inject") then
        --  r4_235:SetAttribute("Inject", true)
        --  r36_0[r4_235.Name] = {
        --    AntiGrab = true,
        --    Protection = true,
        --  }
          r4_235.Chatted:Connect(function(r0_236)
            -- line: [0, 0] id: 236
           -- r60_0(r0_236, r4_235.Name)
          end)
        end
        -- close: r3_235
      end
    end
  end)
  local r61_0 = r6_0.Map.Hole.PoisonBigHole.PoisonHurtPart
  local r62_0 = r6_0.Map.Hole.PoisonSmallHole.PoisonHurtPart
  local r63_0 = r6_0.Map.FactoryIsland.PoisonContainer.PoisonHurtPart
  local r64_0 = Vector3.new(2, 2, 2)
  local r65_0 = Vector3.new(2, 2, 2)
  r63_0.Size = Vector3.new(2, 2, 2)
  r62_0.Size = r65_0
  r61_0.Size = r64_0
  r64_0 = Vector3.new(0, -50, 0)
  r65_0 = Vector3.new(0, -50, 0)
  r63_0.Position = Vector3.new(0, -50, 0)
  r62_0.Position = r65_0
  r61_0.Position = r64_0
  function SetModelProperties(r0_347)
    -- line: [0, 0] id: 347
    for r4_347, r5_347 in pairs(r0_347:GetDescendants()) do
      if r5_347:IsA("BasePart") then
        r5_347.CanCollide = false
      end
    end
  end
  function SetAimPart(r0_338)
    -- line: [0, 0] id: 338
    for r4_338, r5_338 in pairs(r0_338:GetDescendants()) do
      if r5_338:IsA("BasePart") then
        r5_338.CanQuery = false
        r5_338.Transparency = 1
        r5_338.CanCollide = false
      elseif r5_338:IsA("SurfaceGui") then
        r5_338.Enabled = false
      end
    end
    local r1_338 = r0_338:WaitForChild("Center", 1)
    if r1_338 then
      local r2_338 = Instance.new("BillboardGui")
      local r3_338 = Instance.new("ImageLabel")
      local r4_338 = Instance.new("Sound", r6_0)
      r4_338.SoundId = "rbxassetid://9119713951"
      r4_338.PlaybackSpeed = 1.5
      local r5_338 = false
      r2_338.ClipsDescendants = true
      r2_338.Brightness = 3.5
      r2_338.Size = UDim2.new(1.5, 18, 1.5, 18)
      r2_338.Adornee = Part
      r2_338.AlwaysOnTop = true
      r2_338.Active = true
      r2_338.Parent = r1_338
      r3_338.BorderSizePixel = 0
      r3_338.Transparency = 1
      r3_338.BackgroundColor3 = Color3.new(1, 1, 1)
      r3_338.Image = "rbxassetid://12717676115"
      r3_338.Size = UDim2.new(1, 0, 1, 0)
      r3_338.BorderColor3 = Color3.new(0, 0, 0)
      r3_338.BackgroundTransparency = 1
      r3_338.ImageColor3 = Color3.new(0.333333, 1, 0)
      r3_338.Parent = r2_338
      task.spawn(function()
        -- line: [0, 0] id: 339
        while r0_338.Parent do
          local r0_339 = _G.CanExplodeBombs
          if r0_339 then
            r0_339 = r5_338
            if not r0_339 then
              r3_338.ImageColor3 = Color3.new(0.333333, 1, 0)
              r4_338:Play()
              r0_339 = true
              r5_338 = r0_339
            end
          else
            r0_339 = _G.CanExplodeBombs
            if not r0_339 then
              r0_339 = r5_338
              if r0_339 then
                r5_338 = false
                r0_339 = r3_338
                r0_339.ImageColor3 = Color3.new(1, 0, 0)
              end
            end
          end
          wait()
        end
      end)
      -- close: r2_338
    end
  end
  function r64_0(r0_12)
    -- line: [0, 0] id: 12
    if typeof(r0_12) == "Instance" and r0_12:IsA("Model") and r0_12:FindFirstChildOfClass("Humanoid") then
      local r1_12 = r0_12.HumanoidRootPart
      local r2_12 = nil
      local r3_12 = OverlapParams.new()
      r3_12.FilterDescendantsInstances = {
        r0_12
      }
      r3_12.FilterType = Enum.RaycastFilterType.Exclude
      local r4_12 = r6_0:GetPartBoundsInRadius(r1_12.Position, 10, r3_12)
      for r8_12, r9_12 in pairs(r4_12) do
        if r9_12.Parent:IsA("Model") then
          local r10_12 = r9_12.Parent:FindFirstChild("StickyPart")
          if r10_12 and r10_12:FindFirstChildOfClass("TouchTransmitter") == nil and r10_12:FindFirstChild("StickyWeld") then
            local r11_12 = r10_12.StickyWeld
            if r11_12.Part1 and r11_12.Part1:IsDescendantOf(r0_12) then
              r2_12 = true
              break
            end
          end
        end
      end
      table.clear(r4_12)
      r4_12 = nil
      return r2_12
    end
  end
  COAroundPParams = OverlapParams.new()
  COAroundPParams.FilterDescendantsInstances = {
    GetPlayerCharacter(),
    r6_0.Map,
    r6_0.Plots,
    r6_0.Waypoints,
    r6_0.Slots
  }
  COAroundPParams.FilterType = Enum.RaycastFilterType.Exclude
  function IsItemInPlayerPlot(r0_88)
    -- line: [0, 0] id: 88
    if r0_88:IsDescendantOf(r6_0.PlotItems) then
      local r1_88 = _G.RemainingTimeInHouse
      if r1_88 and r1_88.Parent then
        local r2_88 = r1_88.Parent.Parent.Parent.Parent.Name
        if r2_88 and r0_88:IsDescendantOf(r6_0.PlotItems[r2_88]) then
          return true
          return true
        end
      end
    else

    end
  end
  function GetTeslaCoilFromPlayerPlot()
    -- line: [0, 0] id: 186
    local r0_186 = _G.RemainingTimeInHouse
    if r0_186 and r0_186.Parent and IsPlayerInsideSafeZone(r16_0) then
      return r0_186.Parent.Parent.Parent.Parent.TeslaCoil.ZapPart
    end
  end
  function CheckObjectsAroundPlayer()
    -- line: [0, 0] id: 163
    local r0_163 = GetPlayerRoot()
    if r0_163 then
      local r1_163 = {}
      local r2_163 = nil
      local r3_163 = nil
      local function r4_163(r0_164)
        -- line: [0, 0] id: 164
        if not r0_164:IsDescendantOf(r6_0.Map) and not r0_164:IsDescendantOf(r6_0.Plots) and not r0_164:IsDescendantOf(r6_0.Waypoints) and not r0_164:IsDescendantOf(r6_0.Slots) and r0_164.Parent and r0_164.Parent:IsA("Model") and (r0_164.Parent:FindFirstChildOfClass("BasePart") or r0_164.Parent:FindFirstChildOfClass("Part") or r0_164.Parent:FindFirstChildOfClass("MeshPart")) then
          local r1_164 = r0_164.Parent
          if not IsItemInPlayerPlot(r1_164) then
            return false
          end
          r2_163 = GetTeslaCoilFromPlayerPlot()
          local r2_164 = r1_164:FindFirstChildOfClass("Humanoid")
          local r3_164 = nil
          if r2_164 then
            r3_164 = r4_0:GetPlayerFromCharacter(r1_164)
          end
          if not r3_164 then
            return true
          end
        end
      end
      for r8_163, r9_163 in pairs(r6_0:GetPartBoundsInRadius(r0_163.Position, 28, COAroundPParams)) do
        if r4_163(r9_163) then
          local r10_163 = r9_163.Parent
          if not table.find(r1_163, r10_163) then
            table.insert(r1_163, r10_163)
            print(r10_163.Name)
          end
        end
      end
      return r1_163, r2_163
    end
  end
  r65_0 = nil
  local function r66_0()
    -- line: [0, 0] id: 174
    local r1_174 = GetPlayerCFrame()
    local r0_174 = nil	-- notice: implicit variable refs by block#[9, 13, 14, 15, 16, 17, 18]
    for r5_174, r6_174 in pairs(r19_0:GetChildren()) do
      if r6_174.Name == "SprayCanWD" and r6_174:FindFirstChild("StickyRemoverPart") and r6_174.PrimaryPart and Getdistancefromcharacter(r6_174.PrimaryPart.Position) < 30 then
        if not r6_174.StickyRemoverPart:FindFirstChildOfClass("TouchTransmitter") then
          DeleteToyRE:FireServer(r6_174)
        else
          r0_174 = r6_174
        end
      end
    end
    if not r0_174 then
      if r1_174 then
        SpawnToy({
          [1] = "SprayCanWD",
          [2] = CFrame.new(r1_174.Position.X, r1_174.Position.Y, r1_174.Position.Z, -0.133750245, -0.471861839, 0.871468484, -0.0000000037252903, 0.879369617, 0.476139903, -0.991015136, 0.0636838302, -0.117615893),
          [3] = Vector3.new(0, 97.69000244140625, 0),
        })
      end
      BuyToy:InvokeServer("SprayCanWD")
    end
    if r0_174 and r0_174:FindFirstChild("StickyRemoverPart") and r0_174.StickyRemoverPart:FindFirstChildOfClass("TouchTransmitter") and not r0_174:GetAttribute("Connected") then
      local r2_174 = nil
      r2_174 = r0_174.DescendantAdded:Connect(function(r0_175)
        -- line: [0, 0] id: 175
        if r0_175.Name == "PartOwner" and r0_175.Value ~= r16_0.Name then
          r0_174:SetAttribute("AlreadySetOwnerShip", false)
        end
      end)
      local r3_174 = r0_174:FindFirstChild("SoundPart")
      task.spawn(function()
        -- line: [0, 0] id: 176
        while r0_174.Parent do
          local r0_176 = r3_174:FindFirstChildOfClass("TouchTransmitter")
          if not r0_176 then
            DeleteToyRE:FireServer(r0_174)
          end
          task.wait(5)
        end
        print("Pew!")
      end)
      task.spawn(function()
        -- line: [0, 0] id: 177
        while r0_174.Parent do
          local r0_177 = r0_174:GetAttribute("AlreadySetOwnerShip")
          if not r0_177 then
            r0_177 = SNOWshipOnce(r3_174)
            if r0_177 then
              r0_174:SetAttribute("AlreadySetOwnerShip", true)
            else
              r0_177 = Getdistancefromcharacter(r3_174.Position)
              if r0_177 > 30 then
                DeleteToyRE:FireServer(r0_174)
              end
            end
          end
          task.wait(0.1)
        end
        r3_174 = nil
        r65_0 = nil
        r0_174 = nil
        r2_174:Disconnect()
        print("Pew!")
      end)
      r0_174:SetAttribute("Connected", true)
      -- close: r2_174
    end
    r65_0 = r0_174
  end
  local function r67_0()
    -- line: [0, 0] id: 259
    if r65_0 and r65_0.Parent ~= nil then
      return r65_0
    end
    r66_0()
  end
  local function r68_0(r0_15)
    -- line: [0, 0] id: 15
    local r1_15 = r67_0()
    local r2_15 = nil
    local r3_15 = nil
    local r4_15 = nil
    local r5_15 = r16_0.Character and r5_15:FindFirstChild("Head")
    if r1_15 then
      r4_15 = r1_15.PrimaryPart
    end
    if r1_15 and r5_15 and r4_15 then
      r2_15 = r1_15:FindFirstChild("StickyRemoverPart")
      if not r4_15:FindFirstChild("SprayPosRemove") and r1_15:GetAttribute("AlreadySetOwnerShip") then
        SetModelProperties(r1_15)
        r3_15 = Instance.new("BodyPosition", r4_15)
        r3_15.Name = "SprayPosRemove"
        r3_15.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        local r6_15 = Vector3.new(-453, math.random(50, 100), 1081)
        task.spawn(function()
          -- line: [0, 0] id: 16
          while r1_15.Parent do
            r3_15.Position = r5_15.Position + Vector3.new(10, 500, 0)
            task.wait()
          end
        end)
      end
      if r2_15 and r1_15:GetAttribute("AlreadySetOwnerShip") then
        r2_15.Position = r0_15.Position
        task.wait()
        r2_15.Position = r4_15.Position
      end
    end
  end
  local r69_0 = nil
  local r70_0 = nil
  local function r71_0(r0_47)
    -- line: [0, 0] id: 47
    local r1_47 = r0_47
    local r2_47 = nil
    if r1_47 then
      local r3_47 = r1_47:FindFirstChild("EdiblePart")
      local r4_47 = r1_47:FindFirstChild("HoldPart") and r4_47.RigidConstraint.Attachment1
      if not r3_47 and not r4_47 then
        return true
      end
    end
  end
  local function r72_0()
    -- line: [0, 0] id: 66
    local r1_66 = GetPlayerCFrame()
    local r0_66 = nil	-- notice: implicit variable refs by block#[6, 23, 24, 25, 26, 27, 28]
    for r5_66, r6_66 in pairs(r19_0:GetChildren()) do
      if r6_66.Name == "FoodBanana" and r6_66:GetAttribute("RagdollToy") and r71_0(r6_66) then
        r0_66 = r6_66
      end
    end
    if not r0_66 then
      local r2_66 = r19_0:FindFirstChild("FoodBanana")
      if r2_66 then
        if r71_0(r2_66) then
          r2_66:SetAttribute("RagdollToy", true)
        else
          local r3_66 = r2_66:FindFirstChild("EdiblePart")
          local r4_66 = r2_66.HoldPart
          local r5_66 = r4_66.RigidConstraint
          if r3_66 and not r5_66.Attachment1 then
            r4_66.HoldItemRemoteFunction:InvokeServer(unpack({
              [1] = r2_66,
              [2] = r16_0.Character,
            }))
          elseif r3_66 and r5_66.Attachment1 and r5_66.Attachment1:IsDescendantOf(r16_0.Character) and not r4_66.EatingSound.IsPlaying then
            r10_0.HoldEvents.Use:FireServer(r2_66)
            task.wait(0.5)
          elseif not r3_66 and r5_66.Attachment1 and r5_66.Attachment1:IsDescendantOf(r16_0.Character) then
            r4_66.DropItemRemoteFunction:InvokeServer(unpack({
              [1] = r2_66,
              [2] = CFrame.new(r1_66.Position.X, r1_66.Position.Y, r1_66.Position.Z, -0.133750245, -0.471861839, 0.871468484, -0.0000000037252903, 0.879369617, 0.476139903, -0.991015136, 0.0636838302, -0.117615893),
              [3] = Vector3.new(0, 97.69000244140625, 0),
            }))
          end
        end
      else
        SpawnToy({
          [1] = "FoodBanana",
          [2] = CFrame.new(508.073517, 67.2614441, -261.901917, -0.133750245, -0.471861839, 0.871468484, -0.0000000037252903, 0.879369617, 0.476139903, -0.991015136, 0.0636838302, -0.117615893),
          [3] = Vector3.new(0, 97.69000244140625, 0),
        })
        BuyToy:InvokeServer("FoodBanana")
      end
    end
    if r0_66 and r0_66:FindFirstChild("HoldPart") and r0_66.HoldPart:FindFirstChild("RigidConstraint") and not r0_66:GetAttribute("Connected") then
      local r2_66 = nil
      r2_66 = r0_66.DescendantAdded:Connect(function(r0_67)
        -- line: [0, 0] id: 67
        if r0_67.Name == "PartOwner" and r0_67.Value ~= r16_0.Name then
          r0_66:SetAttribute("AlreadySetOwnerShip", nil)
        end
      end)
      local r3_66 = r0_66:FindFirstChild("HitboxPart")
      task.spawn(function()
        -- line: [0, 0] id: 68
        while r0_66.Parent do
          local r0_68 = r0_66:GetAttribute("AlreadySetOwnerShip")
          if not r0_68 then
            r0_68 = SNOWshipOnce(r3_66)
            if r0_68 then
              for r3_68 = 1, 15, 1 do
                r23_0:FireServer(r3_66)
                task.wait()
              end
              r0_66:SetAttribute("AlreadySetOwnerShip", true)
            else
              r0_68 = Getdistancefromcharacter(r3_66.Position)
              if r0_68 > 30 then
                DeleteToyRE:FireServer(r0_66)
              end
            end
          end
          task.wait(0.1)
        end
        r2_66:Disconnect()
        r69_0 = nil
        r70_0 = nil
        r3_66 = nil
      end)
      r0_66:SetAttribute("Connected", true)
      -- close: r2_66
    end
    r69_0 = r0_66
  end
  local function r73_0()
    -- line: [0, 0] id: 345
    if r69_0 and r69_0.Parent ~= nil then
      return r69_0
    end
    r72_0()
  end
  local function r74_0(r0_314)
    -- line: [0, 0] id: 314
    local r1_314 = r73_0()
    local r2_314 = nil
    local r3_314 = nil
    local r4_314 = nil
    local r5_314 = r16_0.Character and r5_314:FindFirstChild("Head")
    if r1_314 then
      r4_314 = r1_314.PrimaryPart
    end
    if r1_314 and r5_314 and r4_314 then
      if not r70_0 then
        for r9_314, r10_314 in pairs(r1_314:GetChildren()) do
          if r10_314.Name == "BananaPeel" and r10_314:FindFirstChildOfClass("TouchTransmitter") then
            r70_0 = r10_314
          end
        end
        print("Done!")
      end
      r2_314 = r70_0
      r2_314.Size = Vector3.new(2, 2, 2)
      r2_314.Transparency = 1
      if not r4_314:FindFirstChild("FoodBananaPosRemove") and r1_314:GetAttribute("AlreadySetOwnerShip") then
        SetModelProperties(r1_314)
        r3_314 = Instance.new("BodyPosition", r1_314.PrimaryPart)
        r3_314.Name = "FoodBananaPosRemove"
        r3_314.MaxForce = Vector3.new(12500, 12500, 12500)
        task.spawn(function()
          -- line: [0, 0] id: 315
          while r1_314.Parent do
            r3_314.Position = r5_314.Position + Vector3.new(0, 500, 0)
            task.wait()
          end
        end)
      end
      if r2_314 and r0_314 and r1_314:GetAttribute("AlreadySetOwnerShip") then
        r2_314.Position = r0_314.Position
        task.wait()
        r2_314.Position = r4_314.Position
      end
    end
  end
  local r75_0 = nil
  holdfirePartFound = nil
  function checkHoldFirePart()
    -- line: [0, 0] id: 227
    local r0_227 = nil	-- notice: implicit variable refs by block#[6, 8]
    for r4_227, r5_227 in pairs(r19_0:GetChildren()) do
      if r5_227.Name == "Campfire" and not r5_227:GetAttribute("FirePlayerPart") and r5_227.FirePlayerPart.CanBurn.Value then
        r0_227 = r5_227
      end
    end
    if not r0_227 then
      SpawnToy({
        [1] = "Campfire",
        [2] = CFrame.new(508.073517, 67.2614441, -261.901917, -0.133750245, -0.471861839, 0.871468484, -0.0000000037252903, 0.879369617, 0.476139903, -0.991015136, 0.0636838302, -0.117615893),
        [3] = Vector3.new(0, 97.69000244140625, 0),
      })
      BuyToy:InvokeServer("Campfire")
    end
    holdfirePartFound = r0_227
  end
  local function r76_0()
    -- line: [0, 0] id: 220
    if holdfirePartFound and holdfirePartFound.Parent ~= nil then
      return holdfirePartFound
    end
    checkHoldFirePart()
  end
  local function r77_0()
    -- line: [0, 0] id: 26
    local r1_26 = GetPlayerCFrame()
    local r2_26 = nil
    local r0_26 = nil	-- notice: implicit variable refs by block#[7, 11, 12, 13, 14, 15, 16]
    for r6_26, r7_26 in pairs(r19_0:GetChildren()) do
      if r7_26.Name == "Campfire" and r7_26.PrimaryPart and Getdistancefromcharacter(r7_26.PrimaryPart.Position) < 30 and r7_26.FirePlayerPart.CanBurn.Value then
        r0_26 = r7_26
      end
    end
    if not r0_26 then
      if r1_26 then
        SpawnToy({
          [1] = "Campfire",
          [2] = CFrame.new(r1_26.Position.X, r1_26.Position.Y, r1_26.Position.Z, -0.133750245, -0.471861839, 0.871468484, -0.0000000037252903, 0.879369617, 0.476139903, -0.991015136, 0.0636838302, -0.117615893),
          [3] = Vector3.new(0, 97.69000244140625, 0),
        })
      end
      BuyToy:InvokeServer("Campfire")
    end
    if r0_26 and r0_26:FindFirstChild("FirePlayerPart") and r0_26.FirePlayerPart:FindFirstChild("CanBurn") and not r0_26:GetAttribute("Connected") then
      local r3_26 = nil
      r3_26 = r0_26.DescendantAdded:Connect(function(r0_27)
        -- line: [0, 0] id: 27
        if r0_27.Name == "PartOwner" and r0_27.Value ~= r16_0.Name then
          r0_26:SetAttribute("AlreadySetOwnerShip", false)
        end
      end)
      task.spawn(function()
        -- line: [0, 0] id: 28
        lastpos = GetPlayerCFrame()
        r2_26 = r0_26.FirePlayerPart
        while r0_26.Parent do
          local r0_28 = r0_26.FirePlayerPart.CanBurn.Value
          if not r0_28 then
            r0_28 = r76_0()
            if r0_28 then
              r2_26.Position = r0_28.FirePlayerPart.Position
            end
          end
          r0_28 = r0_26:GetAttribute("AlreadySetOwnerShip")
          if not r0_28 then
            r0_28 = SNOWshipOnce(r2_26)
            if r0_28 then
              r0_26:SetAttribute("AlreadySetOwnerShip", true)
            else
              r0_28 = Getdistancefromcharacter(r2_26.Position)
              if r0_28 > 30 then
                DeleteToyRE:FireServer(r0_26)
              end
            end
          end
          task.wait(0.1)
        end
        r3_26:Disconnect()
        print("Pew!")
      end)
      r0_26:SetAttribute("Connected", true)
      -- close: r3_26
    end
    r75_0 = r0_26
  end
  local function r78_0()
    -- line: [0, 0] id: 275
    if r75_0 and r75_0.Parent ~= nil then
      return r75_0
    end
    r77_0()
  end
  local function r79_0(r0_342)
    -- line: [0, 0] id: 342
    local r1_342 = r78_0()
    local r2_342 = nil
    local r3_342 = nil
    local r4_342 = nil
    local r5_342 = r16_0.Character and r5_342:FindFirstChild("Head")
    if r1_342 then
      r4_342 = r1_342.PrimaryPart
    end
    if r1_342 and r5_342 and r4_342 then
      r2_342 = r1_342:FindFirstChild("FirePlayerPart")
      r3_342 = r4_342:FindFirstChild("CampfirePosRemove")
      r2_342.Size = Vector3.new(2, 2, 2)
      if not r3_342 and r1_342:GetAttribute("AlreadySetOwnerShip") then
        SetModelProperties(r1_342)
        r3_342 = Instance.new("BodyPosition", r1_342.PrimaryPart)
        r3_342.Name = "CampfirePosRemove"
        r3_342.MaxForce = Vector3.new(12500, 12500, 12500)
        local r6_342 = Vector3.new(-453, math.random(50, 100), 1081)
        task.spawn(function()
          -- line: [0, 0] id: 343
          while r1_342.Parent do
            r3_342.Position = r5_342.Position + Vector3.new(5, 500, 0)
            task.wait()
          end
        end)
      end
      if r2_342 and r0_342 and r1_342:GetAttribute("AlreadySetOwnerShip") and r4_342 then
        r2_342.Position = r0_342.Position
        task.wait()
        r2_342.Position = r4_342.Position
      end
    end
  end
  smalldiceToyFound = nil
  function CheckFakeAim()
    -- line: [0, 0] id: 85
    local r1_85 = GetPlayerCFrame()
    local r0_85 = nil	-- notice: implicit variable refs by block#[7, 11, 12, 13, 14, 15]
    for r5_85, r6_85 in pairs(r19_0:GetChildren()) do
      if r6_85.Name == "DiceSmall" and r6_85:FindFirstChild("Center") and r6_85.PrimaryPart and Getdistancefromcharacter(r6_85.PrimaryPart.Position) < 30 then
        r0_85 = r6_85
      end
    end
    if not r0_85 then
      if r1_85 then
        SpawnToy({
          [1] = "DiceSmall",
          [2] = CFrame.new(r1_85.Position.X, r1_85.Position.Y, r1_85.Position.Z, -0.133750245, -0.471861839, 0.871468484, -0.0000000037252903, 0.879369617, 0.476139903, -0.991015136, 0.0636838302, -0.117615893),
          [3] = Vector3.new(0, 97.69000244140625, 0),
        })
      end
      BuyToy:InvokeServer("DiceSmall")
    end
    if r0_85 and r0_85:FindFirstChild("Center") and not r0_85:GetAttribute("Connected") then
      local r2_85 = nil
      r2_85 = r0_85.DescendantAdded:Connect(function(r0_86)
        -- line: [0, 0] id: 86
        if r0_86.Name == "PartOwner" and r0_86.Value ~= r16_0.Name then
          r0_85:SetAttribute("AlreadySetOwnerShip", false)
        end
      end)
      local r3_85 = r0_85:FindFirstChild("SoundPart")
      task.spawn(function()
        -- line: [0, 0] id: 87
        while r0_85.Parent do
          local r0_87 = r0_85:GetAttribute("AlreadySetOwnerShip")
          if not r0_87 then
            r0_87 = SNOWshipOnce(r3_85)
            if r0_87 then
              r0_85:SetAttribute("AlreadySetOwnerShip", true)
            else
              r0_87 = Getdistancefromcharacter(r3_85.Position)
              if r0_87 > 30 then
                DeleteToyRE:FireServer(r0_85)
              end
            end
          end
          r0_87 = _G.FireworkEffectSpam
          if not r0_87 then
            DeleteToyRE:FireServer(r0_85)
          end
          task.wait(0.1)
        end
        r3_85 = nil
        smalldiceToyFound = nil
        r0_85 = nil
        r2_85:Disconnect()
        print("Pew!")
      end)
      r0_85:SetAttribute("Connected", true)
      -- close: r2_85
    end
    smalldiceToyFound = r0_85
  end
  function GetFakeAim()
    -- line: [0, 0] id: 94
    if smalldiceToyFound and smalldiceToyFound.Parent ~= nil then
      return smalldiceToyFound
    end
    CheckFakeAim()
  end
  function GetFakeAim2()
    -- line: [0, 0] id: 29
    local r0_29 = GetFakeAim()
    local r1_29 = nil
    local r2_29 = nil
    local r3_29 = r16_0.Character
    if r0_29 then
      r2_29 = r0_29.PrimaryPart
    end
    if r0_29 and r3_29 and r2_29 then
      hitpart = r0_29:FindFirstChild("StickyRemoverPart")
      if not r2_29:FindFirstChild("AimPosRemove") and r0_29:GetAttribute("AlreadySetOwnerShip") then
        SetAimPart(r0_29)
        r1_29 = Instance.new("BodyPosition", r2_29)
        r1_29.Name = "AimPosRemove"
        r1_29.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        r1_29.P = 40000
        r1_29.D = 950
        local r4_29 = nil
        local r5_29 = nil
        local r6_29 = nil
        task.spawn(function()
          -- line: [0, 0] id: 30
          while r0_29.Parent do
            local r0_30 = r16_0.Character
            if r0_30 then
              r0_30 = r16_0.Character:FindFirstChild("CamPart")
              if r0_30 then
                r4_29 = Ray.new(r16_0.Character.CamPart.Position, r16_0.Character.CamPart.CFrame.lookVector * 5000)
                r0_30 = r6_0
                r5_29, r6_29 = r0_30:FindPartOnRayWithIgnoreList(r4_29, {
                  r16_0.Character,
                  r19_0
                })
                r0_30 = r5_29
                if r0_30 then
                  r0_30 = r6_29
                  if r0_30 then
                    r0_30 = r1_29
                    r0_30.Position = r6_29
                  end
                end
              end
            end
            task.wait()
          end
        end)
        -- close: r4_29
      end
      return r2_29
    end
  end
  local r80_0 = nil
  local function r81_0()
    -- line: [0, 0] id: 112
    local r2_112 = GetPlayerCharacter()
    local r0_112 = nil	-- notice: implicit variable refs by block#[4, 8]
    for r6_112, r7_112 in pairs(r19_0:GetChildren()) do
      if r7_112.Name == "CreatureBlobman" then
        r0_112 = r7_112
      end
    end
    if not r0_112 then
      if r19_0:FindFirstChild("CreatureBlobman") then
        r0_112 = r19_0.CreatureBlobman
      else
        SpawnToy({
          [1] = "CreatureBlobman",
          [2] = CFrame.new(r2_112.Head.Position),
          [3] = Vector3.new(0, 97.69000244140625, 0),
        })
        BuyToy:InvokeServer("CreatureBlobman")
      end
    end
    r80_0 = r0_112
  end
  local function r82_0()
    -- line: [0, 0] id: 83
    if r80_0 and r80_0.Parent then
      return r80_0
    end
    r81_0()
  end
  local r85_0 = "MakeWindow"
  r85_0 = {
    Name = "Fling Things and People",
    HidePremium = true,
    SaveConfig = true,
    ConfigFolder = "FTAPConfig",
    IntroEnabled = false,
    KeyToOpenWindow = "M",
    FreeMouse = true,
  }
  local r83_0 = r3_0:[r85_0](r85_0)
  local r86_0 = "MakeTab"
  r86_0 = {
    Name = "Combat",
    Icon = "rbxassetid://7485051715",
    PremiumOnly = false,
  }
  local r84_0 = r83_0:[r86_0](r86_0)
  local r87_0 = "MakeTab"
  r87_0 = {
    Name = "Blobman Grab",
    Icon = "rbxassetid://7734058599",
    PremiumOnly = false,
  }
  LongReachGrab_Player = r83_0:[r87_0](r87_0)
  r87_0 = "MakeTab"
  r87_0 = {
    Name = "Invincibility",
    Icon = "rbxassetid://7734056608",
    PremiumOnly = false,
  }
  r85_0 = r83_0:[r87_0](r87_0)
  local r88_0 = "MakeTab"
  r88_0 = {
    Name = "Player",
    Icon = "rbxassetid://7743871002",
    PremiumOnly = false,
  }
  r86_0 = r83_0:[r88_0](r88_0)
  local r89_0 = "MakeTab"
  r89_0 = {
    Name = "ESP",
    Icon = "rbxassetid://7733774602",
    PremiumOnly = false,
  }
  Esp_Tab = r83_0:[r89_0](r89_0)
  r89_0 = "MakeTab"
  r89_0 = {
    Name = "Explosions",
    Icon = "rbxassetid://17837704089",
    PremiumOnly = false,
  }
  r87_0 = r83_0:[r89_0](r89_0)
  local r90_0 = "MakeTab"
  r90_0 = {
    Name = "Teleport",
    Icon = "rbxassetid://7733992829",
    PremiumOnly = false,
  }
  r88_0 = r83_0:[r90_0](r90_0)
  local r91_0 = "MakeTab"
  r91_0 = {
    Name = "Custom Line",
    Icon = "rbxassetid://7734022107",
    PremiumOnly = false,
  }
  r89_0 = r83_0:[r91_0](r91_0)
  local r92_0 = "MakeTab"
  r92_0 = {
    Name = "Grab Auras",
    Icon = "rbxassetid://7733955740",
    PremiumOnly = false,
  }
  r90_0 = r83_0:[r92_0](r92_0)
  local r93_0 = "MakeTab"
  r93_0 = {
    Name = "Keybinds",
    Icon = "rbxassetid://11710306232",
    PremiumOnly = false,
  }
  r91_0 = r83_0:[r93_0](r93_0)
  local r94_0 = "MakeTab"
  r94_0 = {
    Name = "Loop Players",
    Icon = "rbxassetid://7733964640",
    PremiumOnly = false,
  }
  r92_0 = r83_0:[r94_0](r94_0)
  local r95_0 = "MakeTab"
  r95_0 = {
    Name = "Auto",
    Icon = "rbxassetid://7733916988",
    PremiumOnly = false,
  }
  r93_0 = r83_0:[r95_0](r95_0)
  local r96_0 = "MakeTab"
  r96_0 = {
    Name = "Misc",
    Icon = "rbxassetid://7733917120",
    PremiumOnly = false,
  }
  r94_0 = r83_0:[r96_0](r96_0)
  local r97_0 = "MakeTab"
  r97_0 = {
    Name = "Discord Server",
    Icon = "rbxassetid://16570630989",
    PremiumOnly = false,
  }
  r95_0 = r83_0:[r97_0](r97_0)
  local r98_0 = "MakeTab"
  r98_0 = {
    Name = "Config",
    Icon = "rbxassetid://7734053495",
    PremiumOnly = false,
  }
  r96_0 = r83_0:[r98_0](r98_0)
  local r99_0 = "MakeTab"
  r99_0 = {
    Name = "Premium Info",
    Icon = "rbxassetid://7734053495",
    PremiumOnly = false,
  }
  r97_0 = r83_0:[r99_0](r99_0)
  local r100_0 = "MakeTab"
  r100_0 = {
    Name = "Credits",
    Icon = "rbxassetid://7733687281",
    PremiumOnly = false,
  }
  r98_0 = r83_0:[r100_0](r100_0)
  r99_0 = nil
  task.spawn(function()
    -- line: [0, 0] id: 133
    local r0_133, r1_133 = pcall(function()
      -- line: [0, 0] id: 134
      return loadstring(game:HttpGet("https://pastebin.com/raw/Q4iUTG48"))()
    end)
    if r0_133 then
      r99_0 = r1_133[4]
    else
      r99_0 = "Not Found"
    end
    local r2_133 = r95_0:AddSection({
      Name = "Discord Server",
    })
    r2_133:AddLabel(r99_0)
    r2_133:AddButton({
      Name = "Copy Discord Server Link",
      Callback = function()
        -- line: [0, 0] id: 135
        setclipboard("discord.gg/25ms")
        r34_0("Copied to your clipboard")
      end,
    })
    r2_133:AddLabel("Join my discord server to see updates!")
  end)
  local r102_0 = "AddSection"
  r102_0 = {
    Name = "1# Medal credits",
  }
  r100_0 = r98_0:[r102_0](r102_0)
  local r103_0 = "AddSection"
  r103_0 = {
    Name = "2# Medal credits",
  }
  local r101_0 = r98_0:[r103_0](r103_0)
  local r104_0 = "AddSection"
  r104_0 = {
    Name = "3# Medal credits",
  }
  r102_0 = r98_0:[r104_0](r104_0)
  r103_0 = game:GetService("UserService")
  r104_0 = {
    90063030,
    2298910483,
    1030559478,
    1762306425,
    542649826,
    237152138,
    1390422876,
    3089724826,
    882860613,
    7280113503
  }
  local r105_0 = {}
  local r106_0, r107_0 = ypcall(function()
    -- line: [0, 0] id: 279
    return r103_0:GetUserInfosByUserIdsAsync(r104_0)
  end)
  for r111_0, r112_0 in pairs(r104_0) do
    local r113_0 = pairs
    local r114_0 = r107_0
    for r116_0, r117_0 in r113_0(r114_0) do
      if r117_0.Id == r112_0 then
        table.insert(r105_0, r117_0)
      end
    end
  end
  r107_0 = r105_0
  for r111_0, r112_0 in pairs(r104_0) do
    local r113_0 = r107_0[r111_0]
    if not r113_0 then
      r113_0 = {}
      r113_0.DisplayName = "deleted"
      local r114_0 = "Username"
      r113_0[r114_0] = "deleted"
      r105_0[r111_0] = r113_0
    end
  end
  if r106_0 then
    local r110_0 = "AddParagraph"
    r110_0 = r107_0[1].DisplayName .. " (test guy)"
    r100_0:[r110_0](r110_0, "i cracked the script and stuff")
    r110_0 = "AddParagraph"
    r110_0 = r107_0[2].DisplayName .. " (" .. r107_0[2].Username .. ")"
    r100_0:[r110_0](r110_0, "Thanks for giving me inspiration to create the blobman functions, Massless Grab and Line color changer script!")
    r110_0 = "AddParagraph"
    r110_0 = r107_0[3].DisplayName .. " (" .. r107_0[3].Username .. ") " .. "and " .. r107_0[6].DisplayName .. " (" .. r107_0[6].Username .. ")"
    r100_0:[r110_0](r110_0, "Thanks for sharing the Attraction Aura, Silent Aim, Further Extend scripts for me!")
    r110_0 = "AddParagraph"
    r110_0 = r107_0[7].DisplayName .. " (" .. r107_0[7].Username .. ")"
    r100_0:[r110_0](r110_0, "Thanks for helping me to fix kick stuff and my anti-blobman")
    r110_0 = "AddParagraph"
    r110_0 = r107_0[8].DisplayName .. " (" .. r107_0[8].Username .. ")"
    r100_0:[r110_0](r110_0, "Thanks for explosion stuff, fireproximityprompt fix and script updater")
    r110_0 = "AddParagraph"
    r110_0 = r107_0[9].DisplayName .. " (" .. r107_0[9].Username .. ")"
    r100_0:[r110_0](r110_0, "Thanks for laggy stuff!")
    r110_0 = "AddParagraph"
    r110_0 = r107_0[10].DisplayName .. " (" .. r107_0[10].Username .. ")"
    r100_0:[r110_0](r110_0, "Thanks for Anchor Objects Glue/Compile")
    r110_0 = "AddParagraph"
    r110_0 = r107_0[4].DisplayName .. " (" .. r107_0[4].Username .. ")"
    r101_0:[r110_0](r110_0, "Thanks for releasing my script!")
    r110_0 = "AddParagraph"
    r110_0 = r107_0[5].DisplayName .. " (" .. r107_0[5].Username .. ")"
    r102_0:[r110_0](r110_0, "Thanks for testing my scripts")
  end
  PerspectiveEffect = Instance.new("ScreenGui")
  ImageLabel = Instance.new("ImageLabel")
  PerspectiveSaturation = Instance.new("ColorCorrectionEffect", r7_0)
  PerspectiveEffect.Name = "PerspectiveEffect"
  PerspectiveEffect.DisplayOrder = -5
  PerspectiveEffect.Enabled = true
  PerspectiveEffect.IgnoreGuiInset = true
  PerspectiveEffect.ResetOnSpawn = false
  PerspectiveEffect.Parent = r16_0.PlayerGui
  ImageLabel.Parent = PerspectiveEffect
  ImageLabel.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
  ImageLabel.BackgroundTransparency = 1
  ImageLabel.BorderColor3 = Color3.fromRGB(0, 0, 0)
  ImageLabel.BorderSizePixel = 0
  ImageLabel.Size = UDim2.new(1, 0, 1, 0)
  ImageLabel.Image = "rbxassetid://8586979842"
  ImageLabel.ImageTransparency = 1
  PerspectiveSaturation.Enabled = true
  PerspectiveSaturation.Saturation = 0
  imagestransparencyeffect = 0.65
  saturationvalue = -0.3
  t1p = TweenInfo.new(0.6, Enum.EasingStyle.Linear, Enum.EasingDirection.In, 0, false, 0)
  t2p = TweenInfo.new(0.3, Enum.EasingStyle.Linear, Enum.EasingDirection.In, 0, false, 0)
  perspectiveON_effect1 = r8_0:Create(ImageLabel, t1p, {
    ImageTransparency = imagestransparencyeffect,
  })
  perspectiveON_effect2 = r8_0:Create(PerspectiveSaturation, t1p, {
    Saturation = saturationvalue,
  })
  perspectiveOff_effect1 = r8_0:Create(ImageLabel, t2p, {
    ImageTransparency = 1,
  })
  perspectiveOff_effect2 = r8_0:Create(PerspectiveSaturation, t2p, {
    Saturation = 0,
  })
  function PerspectiveOnEffect()
    -- line: [0, 0] id: 5
    perspectiveON_effect1:Play()
    perspectiveON_effect2:Play()
  end
  function PerspectiveOffEffect()
    -- line: [0, 0] id: 247
    perspectiveOff_effect1:Play()
    perspectiveOff_effect2:Play()
  end
  local function r108_0(r0_358)
    -- line: [0, 0] id: 358
    if r0_358 and _G.PerspectiveEffectsAllow then
      PerspectiveOnEffect()
    else
      PerspectiveOffEffect()
    end
  end
  gui = Instance.new("ScreenGui")
  gui.ResetOnSpawn = false
  local r111_0 = "FindFirstChild"
  r111_0 = "ContextActionGui"
  CAG = r16_0.PlayerGui:[r111_0](r111_0)
  if r9_0.TouchEnabled then
    gui.Parent = r16_0.PlayerGui
  end
  if CAG then
    CAG.DescendantAdded:Connect(function(r0_143)
      -- line: [0, 0] id: 143
      if _G.FutherExtend and r0_143:IsA("ImageButton") then
        local r1_143 = r0_143:WaitForChild("ActionIcon")
        if r1_143.Image == "rbxassetid://9603826756" or r1_143.Image == "rbxassetid://9603831913" then
          r1_143.Parent.Visible = false
        end
      end
    end)
  end
  scriptToGetSenv = nil
  senv = nil
  minDistance = 3
  pcDistance = 0
  imageButton = Instance.new("ImageButton")
  imageButton.Size = UDim2.new(0, 45, 0, 45)
  imageButton.Position = UDim2.new(1, -70, 1, -259)
  imageButton.Image = "rbxassetid://97166444"
  imageButton.BackgroundTransparency = 1
  imageButton.ImageTransparency = 0.2
  imageButton.Visible = false
  imageButton.ImageColor3 = Color3.fromRGB(142, 142, 142)
  imageButton.Parent = gui
  imageLabel = Instance.new("ImageLabel")
  imageLabel.Size = UDim2.new(1, 0, 1, 0)
  imageLabel.Image = "rbxassetid://9603831913"
  imageLabel.BackgroundTransparency = 1
  imageLabel.Parent = imageButton
  imageButtonDe = Instance.new("ImageButton")
  imageButtonDe.Size = UDim2.new(0, 45, 0, 45)
  imageButtonDe.Position = UDim2.new(1, -70, 1, -211)
  imageButtonDe.Image = "rbxassetid://97166444"
  imageButtonDe.BackgroundTransparency = 1
  imageButtonDe.ImageTransparency = 0.2
  imageButtonDe.Visible = false
  imageButtonDe.ImageColor3 = Color3.fromRGB(142, 142, 142)
  imageButtonDe.Parent = gui
  imageLabelDe = Instance.new("ImageLabel")
  imageLabelDe.Size = UDim2.new(1, 0, 1, 0)
  imageLabelDe.Image = "rbxassetid://9603826756"
  imageLabelDe.BackgroundTransparency = 1
  imageLabelDe.Parent = imageButtonDe
  IncreaseLineExtend = 0
  function buttonClicked()
    -- line: [0, 0] id: 304
    if senv and senv.distance and _G.FutherExtend then
      senv.distance = (senv.distance or 0) + IncreaseLineExtend
      if senv.distance < minDistance then
        senv.distance = minDistance
      end
    end
  end
  function buttonClickedDE()
    -- line: [0, 0] id: 250
    if senv and senv.distance and _G.FutherExtend then
      senv.distance = (senv.distance or 0) - IncreaseLineExtend
      if senv.distance < minDistance then
        senv.distance = minDistance
      end
    end
  end
  function toggleButtonState(r0_371)
    -- line: [0, 0] id: 371
    if r0_371 and _G.FutherExtend then
      imageButton.Visible = true
      imageButton.Active = true
      imageButtonDe.Visible = true
      imageButtonDe.Active = true
    else
      imageButton.Visible = false
      imageButton.Active = false
      imageButtonDe.Visible = false
      imageButtonDe.Active = false
    end
  end
  r6_0.ChildAdded:Connect(function(r0_97)
    -- line: [0, 0] id: 97
    if r0_97.Name == "GrabParts" and r0_97:IsA("Model") then
      local r1_97 = _G.FutherExtend
      if r1_97 then
        r1_97 = r9_0.MouseEnabled
        if r1_97 then
          r1_97 = r0_97
          local r2_97 = GetPlayerCharacter()
          r1_97:WaitForChild("GrabPart")
          r1_97:WaitForChild("DragPart")
          local r3_97 = Instance.new("BodyPosition", r1_97.GrabPart)
          r3_97.MaxForce = Vector3.new(275000, 275000, 275000)
          r3_97.P = 20000
          r3_97.D = 950
          r3_97.Position = r1_97.GrabPart.WeldConstraint.Part1.Position
          pcDistance = (r1_97.GrabPart.Position - r6_0.CurrentCamera.CFrame.Position).Magnitude
          r1_97.DragPart.AlignPosition.Enabled = false
          task.spawn(function()
            -- line: [0, 0] id: 98
            while r1_97.Parent do
              r3_97.Position = r6_0.Camera.CFrame.Position + r6_0.Camera.CFrame.LookVector * pcDistance
              task.wait()
            end
            pcDistance = 0
            r3_97:Destroy()
          end)
          -- close: r1_97
        end
      end
      r1_97 = toggleButtonState
      r1_97(true)
    end
  end)
  workspace.ChildRemoved:Connect(function(r0_225)
    -- line: [0, 0] id: 225
    if r0_225.Name == "GrabParts" and r0_225:IsA("Model") then
      toggleButtonState(false)
    end
  end)
  local r109_0 = false
  local function r110_0()
    -- line: [0, 0] id: 159
    while r109_0 do
      buttonClicked()
      wait(0.1)
    end
  end
  function r111_0()
    -- line: [0, 0] id: 33
    while r109_0 do
      buttonClickedDE()
      wait(0.1)
    end
  end
  local r112_0 = r9_0
  imageButton.InputBegan:Connect(function(r0_296, r1_296)
    -- line: [0, 0] id: 296
    if not r1_296 and r112_0.TouchEnabled and r0_296.UserInputType == Enum.UserInputType.Touch then
      r109_0 = true
      r110_0()
    end
  end)
  imageButton.InputEnded:Connect(function(r0_3)
    -- line: [0, 0] id: 3
    if r112_0.TouchEnabled and r0_3.UserInputType == Enum.UserInputType.Touch then
      r109_0 = false
    end
  end)
  imageButtonDe.InputBegan:Connect(function(r0_246, r1_246)
    -- line: [0, 0] id: 246
    if not r1_246 and r112_0.TouchEnabled and r0_246.UserInputType == Enum.UserInputType.Touch then
      r109_0 = true
      r111_0()
    end
  end)
  imageButtonDe.InputEnded:Connect(function(r0_148)
    -- line: [0, 0] id: 148
    if r112_0.TouchEnabled and r0_148.UserInputType == Enum.UserInputType.Touch then
      r109_0 = false
    end
  end)
  r9_0.InputChanged:Connect(function(r0_305)
    -- line: [0, 0] id: 305
    if r0_305.UserInputType == Enum.UserInputType.MouseWheel then
      if pcDistance < 11 then
        pcDistance = 11
      end
      if r0_305.Position.Z > 0 then
        pcDistance = pcDistance + IncreaseLineExtend
      elseif r0_305.Position.Z < 0 then
        pcDistance = pcDistance - IncreaseLineExtend
      end
    end
  end)
  getgenv().Settings = {
    Fov = 150,
    Hitbox = {
      "Head",
      "Torso",
      "Left Leg",
      "Right Leg"
    },
    FovCircle = false,
  }
  local r113_0 = r4_0
  local r115_0 = r10_0
  local r116_0 = r16_0
  local r117_0 = r6_0.CurrentCamera
  local r118_0 = r116_0:GetMouse()
  local r119_0 = nil
  local function r120_0(r0_181)
    -- line: [0, 0] id: 181
    local r2_181 = math.huge
    local r1_181 = nil	-- notice: implicit variable refs by block#[11]
    for r6_181, r7_181 in pairs(r113_0:GetPlayers()) do
      if r7_181.Name ~= r116_0.Name and r7_181.Character and r116_0 and r116_0.Character and r116_0.Character:FindFirstChild("HumanoidRootPart") then
        local r8_181 = r7_181.Character:FindFirstChild("HumanoidRootPart")
        if r8_181 then
          local r9_181 = r116_0.Character.HumanoidRootPart.Position
          local r10_181, r11_181 = r117_0:WorldToScreenPoint(r8_181.Position)
          if r11_181 then
            local r12_181 = (r9_181 - r8_181.Position).magnitude
            if r12_181 < r2_181 then
              r2_181 = r12_181
              r1_181 = r7_181
            end
          end
        end
      end
    end
    return r1_181
  end
  local r121_0 = nil
  local r122_0 = nil
  local r123_0 = nil
  local r124_0 = nil
  local r125_0 = Drawing.new("Circle")
  local r126_0 = Drawing.new("Circle")
  r13_0.RenderStepped:Connect(function()
    -- line: [0, 0] id: 327
    if r125_0 then
      r125_0.Radius = getgenv().Settings.Fov
      r125_0.Thickness = 2
      r125_0.Position = Vector2.new(r117_0.ViewportSize.X / 2, r117_0.ViewportSize.Y / 2 + 36)
      r125_0.Transparency = 1
      r125_0.Filled = false
      r125_0.Color = Color3.fromRGB(255, 255, 255)
      r125_0.Visible = getgenv().Settings.FovCircle
      r125_0.ZIndex = 2
    end
    if r126_0 then
      r126_0.Radius = getgenv().Settings.Fov
      r126_0.Thickness = 4
      r126_0.Position = Vector2.new(r117_0.ViewportSize.X / 2, r117_0.ViewportSize.Y / 2 + 36)
      r126_0.Transparency = 1
      r126_0.Filled = false
      r126_0.Color = Color3.new()
      r126_0.Visible = getgenv().Settings.FovCircle
      r126_0.ZIndex = 1
    end
    r121_0 = r120_0(getgenv().Settings.Fov)
  end)
  local function r127_0(r0_1, r1_1, r2_1)
    -- line: [0, 0] id: 1
    return ((r1_1 - r0_1)).Unit * r2_1
  end
  local r128_0 = hookmetamethod
  if r128_0 then
    r128_0 = nil
    r128_0 = hookmetamethod(game, "__namecall", function(...)
      -- line: [0, 0] id: 307
      local r1_307 = {
        ...
      }
      local r3_307 = getnamecallmethod()
      if r1_307[1] == workspace and not checkcaller() and r3_307 == "Raycast" and r121_0 and r121_0.Character and r121_0.Character.HumanoidRootPart and r116_0.Character.HumanoidRootPart and r121_0.Character.Humanoid and 0 < r121_0.Character.Humanoid.Health and not r121_0.InPlot.Value and _G.SilentAim then
        r122_0 = math.random(1, #getgenv().Settings.Hitbox)
        r123_0 = getgenv().Settings.Hitbox[r122_0]
        r124_0 = r121_0.Character[r123_0]
        if (r116_0.Character.HumanoidRootPart.Position - r121_0.Character.HumanoidRootPart.Position).magnitude <= r119_0 and r124_0 then
          r1_307[3] = r127_0(r1_307[2], r121_0.Character[r123_0].Position, 1000)
          r1_307[4] = RaycastParams.new()
          r1_307[4].FilterDescendantsInstances = {
            r121_0.Character
          }
          r1_307[4].FilterType = Enum.RaycastFilterType.Include
          r122_0 = nil
          r123_0 = nil
          r124_0 = nil
        end
      end
      return r128_0(unpack(r1_307))
    end)
    -- close: r128_0
  end
  function r128_0()
    -- line: [0, 0] id: 142
    local r0_142 = nil	-- notice: implicit variable refs by block#[5]
    for r4_142, r5_142 in pairs(r6_0.Slots:GetChildren()) do
      if r5_142.SlotHandle.LightBall.Material == Enum.Material.Neon then
        r0_142 = true
      else
        r0_142 = false
        break
      end
    end
    return r0_142
  end
  local function r129_0(r0_292)
    -- line: [0, 0] id: 292
    local r1_292 = nil	-- notice: implicit variable refs by block#[4, 5, 7, 8]
    if r16_0.Character and r16_0.Character:FindFirstChild("HumanoidRootPart") then
      r1_292 = r16_0.Character.HumanoidRootPart
    end
    if r0_292 == "Spin" and r1_292 then
      _G.SavedPositionInSpin = r1_292.CFrame
    elseif r0_292 == "House" and r1_292 then
      _G.SavedPositionOutHouse = r1_292.CFrame
    end
  end
  local function r130_0(r0_161)
    -- line: [0, 0] id: 161
    local r1_161 = nil	-- notice: implicit variable refs by block#[4, 5, 7, 8]
    if r16_0.Character and r16_0.Character:FindFirstChild("HumanoidRootPart") then
      r1_161 = r16_0.Character.HumanoidRootPart
    end
    if r0_161 == "Spin" and r1_161 then
      r1_161.CFrame = _G.SavedPositionInSpin
    elseif r0_161 == "House" and r1_161 then
      r1_161.CFrame = _G.SavedPositionOutHouse
    end
  end
  local r133_0 = "AddSection"
  r133_0 = {
    Name = "Auto Get Coins",
  }
  local r131_0 = r93_0:[r133_0](r133_0)
  local r134_0 = "AddSection"
  r134_0 = {
    Name = "Auto Time-Reset",
  }
  local r132_0 = r93_0:[r134_0](r134_0)
  local r135_0 = "AddSection"
  r135_0 = {
    Name = "Auto Claim-Plot",
  }
  r133_0 = r93_0:[r135_0](r135_0)
  timelefttextlabelingame = r6_0.Slots.Slots.Screen.SlotGui.TimeLeftFrame.TimeText
  local r136_0 = "AddToggle"
  r136_0 = {
    Name = "Auto-Spin",
    Default = false,
    Callback = function(r0_331)
      -- line: [0, 0] id: 331
      _G.AutoFarmCoins = r0_331
      if r0_331 then
        while _G.AutoFarmCoins do
          local r1_331 = r128_0()
          local r2_331 = nil	-- notice: implicit variable refs by block#[10]
          if r1_331 then
            r129_0("Spin")
            r1_331 = nil
            r2_331 = nil
            local r3_331 = task.spawn(function()
              -- line: [0, 0] id: 332
              -- notice: unreachable block#3
              while true do
                if r2_331 then
                  TeleportPlayer(r2_331.CFrame + Vector3.new(0, 5, 0))
                  task.wait(0.2)
                  SNOWship(r2_331)
                end
                task.wait()
              end
            end)
            for r7_331, r8_331 in pairs(r6_0.Slots:GetChildren()) do
              r1_331 = r8_331
              r2_331 = r1_331.SlotHandle.Handle
              r2_331.CanCollide = false
              for r12_331 = 1, 5, 1 do
                task.wait(0.2)
              end
              r2_331.CanCollide = true
              if not r128_0() then
                break
              end
            end
            task.cancel(r3_331)
            newtask = nil
            r130_0("Spin")
            -- close: r1_331
          end
          r2_331 = 5
          task.wait(r2_331)
        end
      end
    end,
    Save = true,
    Flag = "autofarmcoins_toggle",
  }
  r131_0:[r136_0](r136_0)
  r136_0 = "AddLabel"
  r136_0 = "Time Remaining: 0:00"
  TimeRemainingLabel = r131_0:[r136_0](r136_0)
  r136_0 = "AddLabel"
  r136_0 = "Coins Won: 0"
  CoinsWonLabel = r131_0:[r136_0](r136_0)
  timelefttextlabelingame.Changed:Connect(function(r0_25)
    -- line: [0, 0] id: 25
    if r0_25 == "Text" then
      TimeRemainingLabel:Set("Time Remaining: " .. timelefttextlabelingame.Text)
    end
  end)
  task.spawn(function()
    -- line: [0, 0] id: 375
    for r3_375, r4_375 in pairs(r6_0.Slots:GetDescendants()) do
      if r4_375.Name == "CoinAmount" and tostring(r4_375.Parent) == "CoinsFrame" then
        r4_375.Changed:Connect(function(r0_376)
          -- line: [0, 0] id: 376
          local r1_376 = r4_375.Parent.Parent.SpinningFrame.PlayerName
          if r0_376 == "Text" and r1_376.Text == r16_0.DisplayName and CoinsWonLabel then
            CoinsWonLabel:Set(r4_375.Text)
          end
        end)
      end
      -- close: r3_375
    end
    r6_0.Plots.DescendantAdded:Connect(function(r0_377)
      -- line: [0, 0] id: 377
      if r0_377.Name == "Value" and tostring(r0_377.Parent) == "ThisPlotsOwners" then
        local r1_377 = nil
        if r0_377.Value == r16_0.Name then
          RTime = r0_377:WaitForChild("TimeRemainingNum", 1)
          if RTime then
            RTime.Changed:Connect(function(r0_378)
              -- line: [0, 0] id: 378
              TimeInHouseLabel:Set("Time: " .. r0_378)
            end)
          end
        end
      end
    end)
  end)
  task.spawn(function()
    -- line: [0, 0] id: 122
    for r3_122, r4_122 in pairs(r6_0.Plots:GetDescendants()) do
      if r4_122.Name == "TimeRemainingNum" and r4_122.Parent.Value == r16_0.Name then
        _G.RemainingTimeInHouse = r4_122
        r4_122.Changed:Connect(function(r0_123)
          -- line: [0, 0] id: 123
          TimeInHouseLabel:Set("Time: " .. r0_123)
        end)
      end
    end
  end)
  r135_0 = nil
  local r138_0 = "AddToggle"
  r138_0 = {
    Name = "Preserve Time",
    Default = false,
    Callback = function(r0_357)
      -- line: [0, 0] id: 357
      _G.AutoSaveHouseTime = r0_357
      if r0_357 then
        while _G.AutoSaveHouseTime do
          local r1_357 = r16_0.InfiniteHouseTime.Value
          if r1_357 then
            r135_0:Set(false)
            r3_0:MakeNotification({
              Name = "Stop being greedy!",
              Content = "You already own infinity house gamepass!",
              Image = "rbxassetid://4483345998",
              Time = 5,
            })
            break
          else
            r1_357 = _G.RemainingTimeInHouse
            if typeof(r1_357) == "Instance" and r1_357:IsDescendantOf(r6_0) and r1_357:IsA("IntValue") then
              local r2_357 = _G.RemainingTimeInHouse.Parent.Parent.Parent.Parent:FindFirstChild("PlotArea")
              if r1_357.Value < 20 then
                r129_0("House")
                task.wait()
                while true do
                  TeleportPlayer(CFrame.new(r2_357.Position))
                  task.wait(0.156)
                  if r1_357.Parent == nil then
                    local r3_357 = _G.AutoSaveHouseTime
                    if not r3_357 then
                      break
                    else
                      r3_357 = r1_357.Value
                      if r3_357 > 15 then
                        break
                      end
                    end
                  else
                    break
                  end
                end
                r1_357 = nil
                r130_0("House")
              end
            end
            task.wait(2)
          end
        end
      end
    end,
    Save = true,
    Flag = "autosavehousetimeremaining_toggle",
  }
  r135_0 = r132_0:[r138_0](r138_0)
  r138_0 = "AddLabel"
  r138_0 = "Plot Time: 0"
  TimeInHouseLabel = r132_0:[r138_0](r138_0)
  r136_0 = Instance.new("IntValue")
  PlotWorkspace = r6_0.Plots:GetDescendants()
  function GetPlotModel(r0_293)
    -- line: [0, 0] id: 293
    local r1_293 = r6_0.Plots
    local r2_293 = _G.PlotName
    if r2_293 == "Witch House" then
      r1_293 = r1_293:FindFirstChild("Plot3")
    elseif r2_293 == "Lumber House" then
      r1_293 = r1_293:FindFirstChild("Plot2")
    elseif r2_293 == "Common House" then
      r1_293 = r1_293:FindFirstChild("Plot1")
    elseif r2_293 == "American House" then
      r1_293 = r1_293:FindFirstChild("Plot4")
    elseif r2_293 == "Chinese House" then
      r1_293 = r1_293:FindFirstChild("Plot5")
    end
    return r1_293
  end
  function ClaimPlot()
    -- line: [0, 0] id: 363
    if not IsThereOwnerOnPlot() then
      local r0_363 = GetPlotModel(_G.PlotName)
      if r0_363 then
        r0_363 = r0_363.PlotSign
        local function r1_363()
          -- line: [0, 0] id: 364
          local r0_364 = false
          for r4_364, r5_364 in pairs(r0_363.ThisPlotsOwners:GetChildren()) do
            if r5_364.Value == r16_0.Name then
              r0_364 = true
            end
          end
          return r0_364
        end
        for r5_363, r6_363 in pairs(r0_363:GetChildren()) do
          if r1_363() then
            break
          elseif r6_363.Name == "Sign" then
            local r7_363 = r6_363.Plus.PlusGrabPart
            TeleportPlayer(r7_363.CFrame * CFrame.new(-5, 0, -5))
            for r11_363 = 0, 15, 1 do
              SNOWship(r7_363)
              wait()
            end
          end
        end
      end
      -- close: r0_363
    end
  end
  function UpdatePlotOwner()
    -- line: [0, 0] id: 282
    for r4_282, r5_282 in pairs(PlotWorkspace) do
      if r5_282.Name == "PlayerRole" then
        local r6_282 = r5_282.Parent.PlayerDisplayName
        local r7_282 = r5_282
        local r8_282 = r5_282.Parent
        local r9_282 = nil
        local r10_282 = false
        local function r11_282()
          -- line: [0, 0] id: 284
          r10_282 = false
          r9_282 = GetPlotModel(_G.PlotName)
          if r9_282 and r7_282:IsDescendantOf(r9_282) and r7_282.Text == "Owner" and r8_282.Visible then
            wait()
            for r3_284, r4_284 in pairs(r4_0:GetPlayers()) do
              if r4_284.DisplayName == r6_282.Text then
                r10_282 = true
              end
            end
            if PlotOwner and r10_282 then
              PlotOwner:Set("Plot Owner: " .. r6_282.Text)
            else
              PlotOwner:Set("Plot Available!")
            end
          end
        end
        r7_282.Changed:Connect(function(r0_283)
          -- line: [0, 0] id: 283
          if r0_283 == "Text" then
            r11_282()
          end
        end)
        r136_0.Changed:Connect(function(r0_285)
          -- line: [0, 0] id: 285
          r11_282()
        end)
        r11_282()
        -- close: r6_282
      end
    end
  end
  function IsThereOwnerOnPlot()
    -- line: [0, 0] id: 32
    local r0_32 = GetPlotModel()
    if r0_32 and r0_32.PlotSign.ThisPlotsOwners:FindFirstChild("Value") then
      return true
    end
  end
  function UpdatePeopleInPlot()
    -- line: [0, 0] id: 183
    for r4_183, r5_183 in pairs(PlotWorkspace) do
      if r5_183.Name == "ThisPlotsOwners" then
        local function r6_183()
          -- line: [0, 0] id: 185
          local r1_185 = GetPlotModel(_G.PlotName)
          local r2_185 = r5_183:GetChildren()
          if r1_185 and r5_183:IsDescendantOf(r1_185) then
            r2_185 = table.getn(r2_185)
            if PlayersInPlot then
              PlayersInPlot:Set("Players in Plot: " .. r2_185)
            end
            if r2_185 == 0 and PlotOwner then
              PlotOwner:Set("Plot Available!")
            end
          end
        end
        r136_0.Changed:Connect(function(r0_184)
          -- line: [0, 0] id: 184
          r6_183()
        end)
        r5_183.ChildAdded:Connect(r6_183)
        r5_183.ChildRemoved:Connect(r6_183)
        r6_183()
        -- close: r6_183
      end
      -- close: r4_183
    end
  end
  local r139_0 = "AddDropdown"
  r139_0 = {
    Name = "Plot",
    Default = "Witch House",
    Options = {
      "Witch House",
      "Lumber House",
      "Common House",
      "American House",
      "Chinese House"
    },
    Callback = function(r0_111)
      -- line: [0, 0] id: 111
      _G.PlotName = r0_111
      r136_0.Value = r136_0.Value + 1
    end,
  }
  r133_0:[r139_0](r139_0)
  task.spawn(function()
    -- line: [0, 0] id: 288
    UpdatePlotOwner()
    task.wait()
    UpdatePeopleInPlot()
  end)
  r139_0 = "AddLabel"
  r139_0 = "Plot Owner:"
  PlotOwner = r133_0:[r139_0](r139_0)
  r139_0 = "AddLabel"
  r139_0 = "Players in Plot: 0"
  PlayersInPlot = r133_0:[r139_0](r139_0)
  r139_0 = "AddButton"
  r139_0 = {
    Name = "Claim Plot!",
    Callback = function()
      -- line: [0, 0] id: 355
      ClaimPlot()
    end,
  }
  r133_0:[r139_0](r139_0)
  function ExplodeSb(r0_229)
    -- line: [0, 0] id: 229
    BombEvents.BombExplode:FireServer(unpack({
      [1] = {
        Radius = 17.5,
        TimeLength = 0.1,
        Hitbox = r0_229:FindFirstChild("SoundPart"),
        ExplodesByFire = true,
        MaxForcePerStudSquared = -100,
        DestroysModel = true,
        Model = r0_229,
        ExplodesByPointy = false,
        ImpactSpeed = 100,
        PositionPart = r16_0.Character.HumanoidRootPart,
      },
      [2] = r16_0.Character.HumanoidRootPart.Position,
    }))
  end
  getgenv().MaxSize = 15
  local r137_0 = r16_0
  r138_0 = r19_0
  r139_0 = {}
  local r140_0 = 0
  local r141_0 = nil
  snowballEffectConnection = nil
  snowballMaxAmmount = 20
  if r21_0.Value == 200 then
    snowballMaxAmmount = 40
  end
  function checkSize(r0_370)
    -- line: [0, 0] id: 370
    while _G.SnowbalEffectSpam do
      if r0_370 then
        local r3_370 = r0_370:IsDescendantOf(r6_0)
        if r3_370 then
          r3_370 = r0_370:FindFirstChild("SoundPart")
          if r3_370 then
            local r2_370 = r0_370:FindFirstChild("SoundPart")
            local r1_370 = r2_370.Size
            r3_370 = r1_370.X
            if MaxSize <= r3_370 then
              r3_370 = r1_370.Y
              if MaxSize <= r3_370 then
                r3_370 = r1_370.Z
                if MaxSize <= r3_370 then
                  r3_370 = r139_0[r2_370]
                  if not r3_370 then
                    r3_370 = r139_0
                    r3_370[r2_370] = true
                    break
                  end
                end
              end
            end
          end
        end
      else
        task.wait()
      end
    end
  end
  function checkSnowBall(r0_63)
    -- line: [0, 0] id: 63
    if r0_63 and r0_63:FindFirstChild("SoundPart") then
      local r1_63 = r0_63.SoundPart
      local r2_63 = RaycastParams.new()
      local r3_63 = nil
      r2_63.FilterDescendantsInstances = {
        r0_63
      }
      r2_63.FilterType = Enum.RaycastFilterType.Exclude
      r3_63 = r6_0:Raycast(r1_63.Position, Vector3.new(0, -100, 0), r2_63)
      if r3_63 and r3_63.Material == Enum.Material.Sand then
        return true
      end
    end
  end
  lastpossb = nil
  function holdOwnership()
    -- line: [0, 0] id: 9
    while _G.SnowbalEffectSpam do
      local r1_9 = pairs
      for r4_9, r5_9 in r1_9(r138_0:GetChildren()) do
        if _G.SnowbalEffectSpam then
          if r5_9 and r5_9.Name == "BallSnowball" and r5_9:FindFirstChild("SoundPart") then
            local r0_9 = r5_9:FindFirstChild("SoundPart")
            if not CheckNetworkOwnerShipOnPart(r0_9) then
              if not lastpossb then
                lastpossb = GetPlayerCFrame()
              end
              for r9_9 = 1, 10, 1 do
                if SNOWshipOnce(r0_9) then
                  r0_9.CanTouch = false
                  r0_9.CanCollide = false
                  break
                else
                  TeleportPlayer(CFrame.new(r0_9.Position + Vector3.new(0, -10, 0)))
                  task.wait(0.1)
                end
              end
              TeleportPlayer(lastpossb)
              lastpossb = nil
            end
          end
        else
          break
        end
      end
      task.wait()
    end
  end
  function CountGrownSnowsballs()
    -- line: [0, 0] id: 44
    local r0_44 = 0
    for r4_44, r5_44 in pairs(r139_0) do
      if r4_44:IsDescendantOf(r6_0) then
        r0_44 = r0_44 + 1
      else
        r139_0[r4_44] = nil
      end
    end
    r141_0:Set("Grown Snowballs: " .. r0_44)
    return r0_44
  end
  function modify(r0_199)
    -- line: [0, 0] id: 199
    local r1_199 = CFrame.new(-410, 228.394, 510, -0.246182978, 0.00000000322764193, -0.96922338, 0.000000012914926, 1, 0.0000000000497377278, 0.96922338, -0.000000012505204, -0.246182978)
    local r2_199 = nil
    local r3_199 = nil
    while _G.SnowbalEffectSpam and r0_199 do
      local r4_199 = r0_199:FindFirstChild("SoundPart")
      if r4_199 then
        r2_199 = r0_199.SoundPart
        r3_199 = r2_199:FindFirstChild("FarmSnowball")
        r4_199 = CheckNetworkOwnerShipOnPart(r2_199)
        if r4_199 then
          if r3_199 then
            r4_199 = r139_0[r2_199]
            if r4_199 then
              r4_199 = Vector3.new(math.random(-10000, 10000), 10000, math.random(-10000, 10000))
              r3_199.Position = r4_199
            else
              r3_199.Position = r1_199.Position + Vector3.new(25, 0, 0) + Vector3.new(0, r2_199.Size.X / 2 - 0.65, 0)
              wait(0.5)
              r3_199.Position = r1_199.Position + Vector3.new(-25, 0, 0) + Vector3.new(0, r2_199.Size.X / 2 - 0.65, 0)
              wait(0.5)
              r4_199 = r1_199.Position + Vector3.new(0, r2_199.Size.X / 2 - 0.65, 0)
              r3_199.Position = r4_199
            end
          else
            r3_199 = Instance.new("BodyPosition", r2_199)
            r3_199.MaxForce = Vector3.new(12500, 12500, 12500)
            r3_199.Name = "FarmSnowball"
            r4_199 = r2_199.Position
            r3_199.Position = r4_199
          end
        end
      end
      wait()
    end
  end
  function newSnowball(r0_350)
    -- line: [0, 0] id: 350
    if r0_350.Name == "BallSnowball" and _G.SnowbalEffectSpam then
      task.spawn(function()
        -- line: [0, 0] id: 352
        checkSize(r0_350)
      end)
      task.spawn(function()
        -- line: [0, 0] id: 351
        modify(r0_350)
      end)
    end
  end
  task.spawn(function()
    -- line: [0, 0] id: 362
    while task.wait() do
      CountGrownSnowsballs()
    end
  end)
  local r144_0 = "AddSection"
  r144_0 = {
    Name = "Snowball",
  }
  local r142_0 = r87_0:[r144_0](r144_0)
  local r145_0 = "AddSlider"
  r145_0 = {
    Name = "Ammount",
    Min = 5,
    Max = snowballMaxAmmount,
    Default = 5,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 1,
    ValueName = "Snowballs you want to make to explode them!",
    Callback = function(r0_157)
      -- line: [0, 0] id: 157
      r140_0 = r0_157
    end,
    Save = true,
    Flag = "ammountsnowballtomake_slider",
  }
  r142_0:[r145_0](r145_0)
  automakesnowballtoggle = nil
  r145_0 = "AddToggle"
  r145_0 = {
    Name = "Auto Make Snowball",
    Default = false,
    Callback = function(r0_239)
      -- line: [0, 0] id: 239
      _G.SnowbalEffectSpam = r0_239
      if r0_239 then
        snowballEffectConnection = r138_0.ChildAdded:Connect(newSnowball)
        task.spawn(function()
          -- line: [0, 0] id: 241
          while _G.SnowbalEffectSpam do
            local r0_241 = countToys("BallSnowball")
            if r140_0 > r0_241 then
              SpawnToy({
                [1] = "BallSnowball",
                [2] = CFrame.new(-389, 228, 550, -0.3092496991157532, 0.2610282301902771, -0.9144555330276489, 0, 0.9615919589996338, 0.2744831442832947, 0.9509809017181396, 0.08488383144140244, -0.2973720133304596),
                [3] = Vector3.new(0, 97.69000244140625, 0),
              })
              task.wait(0.1)
            end
            r0_241 = CountGrownSnowsballs()
            if r140_0 <= r0_241 then
              automakesnowballtoggle:Set(false)
            end
            task.wait()
          end
        end)
        task.spawn(function()
          -- line: [0, 0] id: 240
          holdOwnership()
        end)
        for r4_239, r5_239 in ipairs(r138_0:GetChildren()) do
          newSnowball(r5_239)
        end
      elseif snowballEffectConnection then
        snowballEffectConnection:Disconnect()
      end
    end,
    Save = true,
    Flag = "autofarmsnowball_toggle",
  }
  automakesnowballtoggle = r142_0:[r145_0](r145_0)
  r145_0 = "AddLabel"
  r145_0 = "Grown Snowballs:"
  r141_0 = r142_0:[r145_0](r145_0)
  r145_0 = "AddButton"
  r145_0 = {
    Name = "Explode Snowballs",
    Callback = function()
      -- line: [0, 0] id: 140
      for r3_140, r4_140 in pairs(r139_0) do
        if r3_140:IsDescendantOf(r6_0) then
          ExplodeSb(r3_140.Parent)
        end
      end
    end,
  }
  r142_0:[r145_0](r145_0)
  spamexplosiontype = nil
  spamexplosiontarget = 0
  bombsammountoexplode = 1
  reachedrightammount = false
  explosionInterval = nil
  canExplode = false
  maxBombstoexplode = 8
  if r21_0.Value == 200 then
    maxBombstoexplode = 18
  end
  r145_0 = "BindAction"
  r145_0 = "FireBomb"
  r12_0:[r145_0](r145_0, fireBombs, false, Enum.KeyCode.F)
  function ExplodeFw()
    -- line: [0, 0] id: 369
    for r3_369, r4_369 in pairs(r19_0:GetChildren()) do
      if r4_369.Name == spamexplosiontype then
        local r6_369 = {
          [1] = {
            Radius = 17.5,
            TimeLength = 0.5,
            Hitbox = r4_369:FindFirstChild("PartHitDetector"),
            ExplodesByFire = true,
            MaxForcePerStudSquared = 225,
            DestroysModel = true,
            Model = r4_369,
            ExplodesByPointy = false,
            ImpactSpeed = 20,
            PositionPart = workspace.SpawnLocation,
          },
          [2] = Vector3.new(0, -10, 0),
        }
        if spamexplosiontype == "BombBalloon" then
          r6_369[1].Hitbox = r4_369.Balloon
        elseif spamexplosiontype == "PresentBig" or spamexplosiontype == "PresentSmall" then
          r6_369[1].Hitbox = r4_369.Box
        end
        if spamexplosiontarget == 0 then
          r6_369[1].PositionPart = workspace.SpawnLocation
          r6_369[2] = Vector3.new(math.random(-10, 10), math.random(-10, 10), math.random(-10, 10))
        elseif spamexplosiontarget == 1 then
          local r7_369 = nil
          local r8_369 = nil
          if _G.TargetToBombPlayer then
            r7_369 = r4_0:FindFirstChild(_G.TargetToBombPlayer)
          end
          if r7_369 and not IsPlayerInsideSafeZone(r7_369) and r7_369.Character and r7_369.Character:FindFirstChild("HumanoidRootPart") then
            r8_369 = r7_369.Character.HumanoidRootPart
            r6_369[1].PositionPart = r8_369
            r6_369[2] = r8_369.Position
          end
        elseif spamexplosiontarget == 2 then
          local r7_369 = GetFakeAim2()
          if r7_369 then
            r6_369[1].PositionPart = r7_369
            r6_369[2] = r7_369.Position
          end
        end
        BombEvents.BombExplode:FireServer(unpack(r6_369))
      end
      if explosionInterval > 0 then
        task.wait(explosionInterval)
      end
    end
  end
  r145_0 = "AddSection"
  r145_0 = {
    Name = "Explosions Spam",
  }
  firework_section = r87_0:[r145_0](r145_0)
  r145_0 = "AddSection"
  r145_0 = {
    Name = "FAQ about (Explosions Spam)",
  }
  explosionexplanation = r87_0:[r145_0](r145_0)
  r145_0 = "AddToggle"
  r145_0 = {
    Name = "Explode",
    Default = false,
    Callback = function(r0_150)
      -- line: [0, 0] id: 150
      _G.FireworkEffectSpam = r0_150
      if r0_150 then
        task.spawn(function()
          -- line: [0, 0] id: 153
          while _G.FireworkEffectSpam do
            local r0_153 = GetPlayerCFrame()
            if bombsammountoexplode > countToys(spamexplosiontype) and not reachedrightammount and (spamexplosiontarget ~= 2 or GetFakeAim()) and r0_153 then
              SpawnToy({
                [1] = spamexplosiontype,
                [2] = CFrame.new(r0_153.Position.X, r0_153.Position.Y, r0_153.Position.Z, -0.3092496991157532, 0.2610282301902771, -0.9144555330276489, 0, 0.9615919589996338, 0.2744831442832947, 0.9509809017181396, 0.08488383144140244, -0.2973720133304596),
                [3] = Vector3.new(0, 97.69000244140625, 0),
              })
            end
            task.wait()
          end
        end)
        task.spawn(function()
          -- line: [0, 0] id: 151
          while _G.FireworkEffectSpam do
            local r0_151 = pairs
            for r3_151, r4_151 in r0_151(r19_0:GetChildren()) do
              if r4_151.Name == spamexplosiontype then
                local r5_151 = nil
                if spamexplosiontype == "BombDarkMatter" then
                  r5_151 = r4_151:FindFirstChild("Pyramid")
                elseif spamexplosiontype == "BombMissile" then
                  r5_151 = r4_151:FindFirstChild("Body")
                elseif spamexplosiontype == "BombBalloon" then
                  r5_151 = r4_151:FindFirstChild("Balloon")
                elseif spamexplosiontype == "FireworkMissile" then
                  r5_151 = r4_151:FindFirstChild("Hitbox")
                elseif spamexplosiontype == "PresentBig" or spamexplosiontype == "PresentSmall" then
                  r5_151 = r4_151:FindFirstChild("Box")
                end
                if r5_151 and not SNOWshipOnce(r5_151) and 30 < r16_0:DistanceFromCharacter(r5_151.Position) then
                  DeleteToyRE:FireServer(r4_151)
                  print("Deletado!")
                elseif r5_151 and CheckNetworkOwnerShipOnPart(r5_151) and not r4_151:GetAttribute("MissileTeleported") then
                  wait()
                  if r4_151.PrimaryPart then
                    Instance.new("BodyVelocity", r4_151.PrimaryPart).Velocity = Vector3.new(10000, 10000, 10000)
                    r4_151:SetPrimaryPartCFrame(CFrame.new(math.random(-1000, 1000), 10000, math.random(-1000, 1000)))
                    r4_151:SetAttribute("MissileTeleported", true)
                  end
                  print("ownershipped!")
                end
              end
            end
            task.wait(0.1)
          end
        end)
        task.spawn(function()
          -- line: [0, 0] id: 152
          while _G.FireworkEffectSpam do
            local r0_152 = countToys(spamexplosiontype)
            if bombsammountoexplode <= r0_152 then
              r0_152 = spamexplosiontarget
              if r0_152 == 2 then
                r0_152 = _G.FireBomb
                if r0_152 then
                  r0_152 = true
                  canExplode = r0_152
                end
              else
                r0_152 = spamexplosiontarget
                if r0_152 ~= 2 then
                  r0_152 = true
                  canExplode = r0_152
                end
              end
              _G.CanExplodeBombs = true
              r0_152 = canExplode
              if r0_152 then
                ExplodeFw()
                reachedrightammount = false
                r0_152 = false
                canExplode = r0_152
              end
            else
              r0_152 = _G
              r0_152.CanExplodeBombs = false
            end
            task.wait()
          end
        end)
        task.spawn(function()
          -- line: [0, 0] id: 154
          while _G.FireworkEffectSpam do
            local r0_154 = spamexplosiontarget
            if r0_154 == 2 then
              GetFakeAim2()
            end
            wait(0.1)
          end
        end)
      end
    end,
  }
  firework_section:[r145_0](r145_0)
  local r143_0 = firework_section
  r145_0 = "AddDropdown"
  r145_0 = {
    Name = "Explosion Type",
    Default = "Firework",
    Options = {
      "Firework",
      "Missile",
      "Void",
      "Ballon",
      "Small Present",
      "Big Present"
    },
    Callback = function(r0_243)
      -- line: [0, 0] id: 243
      if r0_243 == "Firework" then
        spamexplosiontype = "FireworkMissile"
      elseif r0_243 == "Missile" then
        spamexplosiontype = "BombMissile"
      elseif r0_243 == "Void" then
        spamexplosiontype = "BombDarkMatter"
      elseif r0_243 == "Ballon" then
        spamexplosiontype = "BombBalloon"
      elseif r0_243 == "Small Present" then
        spamexplosiontype = "PresentSmall"
      elseif r0_243 == "Big Present" then
        spamexplosiontype = "PresentBig"
      end
    end,
  }
  r143_0:[r145_0](r145_0)
  r145_0 = "AddSlider"
  r145_0 = {
    Name = "Ammount to Explode",
    Min = 1,
    Max = maxBombstoexplode,
    Default = 1,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 1,
    ValueName = "to explode the player brutally",
    Callback = function(r0_308)
      -- line: [0, 0] id: 308
      bombsammountoexplode = r0_308
    end,
  }
  firework_section:[r145_0](r145_0)
  r145_0 = "AddSlider"
  r145_0 = {
    Name = "Delay",
    Min = 0,
    Max = 1,
    Default = 0,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 0.1,
    ValueName = "interval between every explosion",
    Callback = function(r0_271)
      -- line: [0, 0] id: 271
      explosionInterval = r0_271
    end,
  }
  firework_section:[r145_0](r145_0)
  r143_0 = firework_section
  r145_0 = "AddDropdown"
  r145_0 = {
    Name = "Target",
    Default = "Spawn",
    Options = {
      "Spawn",
      "Player",
      "Mouse"
    },
    Callback = function(r0_261)
      -- line: [0, 0] id: 261
      if r0_261 == "Spawn" then
        spamexplosiontarget = 0
      elseif r0_261 == "Player" then
        spamexplosiontarget = 1
      elseif r0_261 == "Mouse" then
        spamexplosiontarget = 2
      end
    end,
  }
  r143_0:[r145_0](r145_0)
  r143_0 = firework_section
  r145_0 = "AddDropdown"
  r145_0 = {
    Name = "Select Player",
    Default = "Macaco (negro)",
    Options = {
      ""
    },
    Callback = function(r0_89)
      -- line: [0, 0] id: 89
      _G.TargetToBombPlayer = string.split(r0_89, " ")[1]
    end,
  }
  PlayerToTarget = r143_0:[r145_0](r145_0)
  r145_0 = "AddParagraph"
  r145_0 = "How to use target mouse?"
  explosionexplanation:[r145_0](r145_0, "Press/Hold the keybind (F) and then BOOM!")
  r145_0 = "AddParagraph"
  r145_0 = "How to target player?"
  explosionexplanation:[r145_0](r145_0, "Select Target to Player and then select the player you want to target")
  r145_0 = "AddParagraph"
  r145_0 = "How to change the explosive"
  explosionexplanation:[r145_0](r145_0, "Click on Explosive Type and select any type")
  r145_0 = "connect"
  function r145_0()
    -- line: [0, 0] id: 242
    r14_0:Button2Down(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
    wait(1)
    r14_0:Button2Up(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
  end
  r16_0.Idled:[r145_0](r145_0)
  r145_0 = "AddSection"
  r145_0 = {
    Name = "Silent-Aim",
  }
  r143_0 = r94_0:[r145_0](r145_0)
  local r146_0 = "AddToggle"
  r146_0 = {
    Name = "Silent Aim",
    Default = false,
    Callback = function(r0_260)
      -- line: [0, 0] id: 260
      _G.SilentAim = r0_260
    end,
    Save = true,
    Flag = "SilentAim_toggle",
  }
  r143_0:[r146_0](r146_0)
  r146_0 = "AddSlider"
  r146_0 = {
    Name = "Silent-Aim Range",
    Min = 0,
    Max = 50,
    Default = 50,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 1,
    ValueName = "",
    Callback = function(r0_141)
      -- line: [0, 0] id: 141
      r119_0 = r0_141
    end,
    Save = true,
    Flag = "silentaimrange_slider",
  }
  r143_0:[r146_0](r146_0)
  r146_0 = "AddSection"
  r146_0 = {
    Name = "Line Extender",
  }
  r144_0 = r89_0:[r146_0](r146_0)
  local r147_0 = "AddToggle"
  r147_0 = {
    Name = "Further Extend",
    Default = false,
    Callback = function(r0_269)
      -- line: [0, 0] id: 269
      _G.FutherExtend = r0_269
    end,
    Save = true,
    Flag = "FurtherLineExtend_toggle",
  }
  r144_0:[r147_0](r147_0)
  MaxExtendLine = 0
  MinExtendLine = 0
  if r9_0.TouchEnabled then
    MinExtendLine = 3
    MaxExtendLine = 25
  elseif r9_0.MouseEnabled then
    MinExtendLine = 3
    MaxExtendLine = 25
  end
  r147_0 = "AddSlider"
  r147_0 = {
    Name = "Increase Extend",
    Min = MinExtendLine,
    Max = MaxExtendLine,
    Default = 3,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 1,
    ValueName = "Ammount",
    Callback = function(r0_82)
      -- line: [0, 0] id: 82
      IncreaseLineExtend = r0_82
    end,
    Save = true,
    Flag = "FurtherLineExtend_slider",
  }
  r144_0:[r147_0](r147_0)
  r147_0 = "AddSection"
  r147_0 = {
    Name = "Normal Auras",
  }
  r145_0 = r90_0:[r147_0](r147_0)
  local r148_0 = "AddSection"
  r148_0 = {
    Name = "Fling Aura",
  }
  r146_0 = r90_0:[r148_0](r148_0)
  local r149_0 = "AddSection"
  r149_0 = {
    Name = "Kick Aura",
  }
  r147_0 = r90_0:[r149_0](r149_0)
  local r150_0 = "AddSection"
  r150_0 = {
    Name = "Auras Whitelist",
  }
  r148_0 = r90_0:[r150_0](r150_0)
  function r149_0()
    -- line: [0, 0] id: 22
    local r0_22 = r16_0.Character
    local r1_22 = nil
    if r0_22 then
      r1_22 = r0_22:FindFirstChildOfClass("Humanoid")
    end
    if r0_22 and r1_22 and r1_22.Sit and r1_22.SeatPart ~= nil and tostring(r1_22.SeatPart.Parent) == "CreatureBlobman" then
      _G.LastBlobmanWasSeat = r1_22.SeatPart.Parent
      return true
    end
    return false
  end
  function r150_0(r0_139)
    -- line: [0, 0] id: 139
    local r1_139 = false
    local r2_139 = r4_0:FindFirstChild(r0_139)
    if r149_0() and _G.LoopKick then
      for r6_139, r7_139 in pairs(r40_0) do
        if r0_139 == r7_139 then
          r1_139 = true
        end
      end
    end
    return r1_139
  end
  local function r151_0(r0_125)
    -- line: [0, 0] id: 125
    if typeof(r0_125) == "Instance" and r0_125 ~= r16_0 and not r38_0(r0_125) and r0_125.Character and r0_125.Character:IsDescendantOf(r6_0) and r0_125.Character:FindFirstChild("HumanoidRootPart") and r0_125.Character:FindFirstChildOfClass("Humanoid") and 0 < r0_125.Character.Humanoid.Health then
      return true
    end
  end
  local function r152_0(r0_223)
    -- line: [0, 0] id: 223
    if r151_0(r0_223) and not IsPlayerInsideSafeZone(r0_223) then
      return true
    end
  end
  local function r153_0(r0_91)
    -- line: [0, 0] id: 91
    if r151_0(r0_91) and (not r46_0(r0_91.Name) or not _G.WhitelistFriends) and not r150_0(r0_91.Name) and not r0_91.Character:GetAttribute("Kicking") and not _G.KickAura then
      return true
    end
  end
  local function r154_0(r0_14)
    -- line: [0, 0] id: 14
    if r151_0(r0_14) and (not r46_0(r0_14.Name) or not _G.WhitelistFriends) and not r150_0(r0_14.Name) and not r0_14.Character:GetAttribute("Kicking") then
      return true
    end
  end
  local function r155_0(r0_37)
    -- line: [0, 0] id: 37
    if r151_0(r0_37) and (not r46_0(r0_37.Name) or not _G.WhitelistFriends3) and not r150_0(r0_37.Name) and not r0_37.Character:GetAttribute("Kicking") then
      return true
    end
  end
  local function r156_0(r0_268)
    -- line: [0, 0] id: 268
    if r151_0(r0_268) and (not r46_0(r0_268.Name) or not _G.WhitelistFriends3) and not IsPlayerInsideSafeZone(r0_268) then
      return true
    end
  end
  local function r157_0(r0_76)
    -- line: [0, 0] id: 76
    if r151_0(r0_76) and (not r46_0(r0_76.Name) or not _G.WhitelistFriends3) and not IsPlayerInsideSafeZone(r0_76) and not IsPlayerFloating(r0_76) then
      return true
    end
  end
  function CreateSkyVelocity(r0_313)
    -- line: [0, 0] id: 313
    if not r0_313:FindFirstChild("SkyVelocity") then
      local r1_313 = nil
      r1_313 = Instance.new("BodyVelocity", r0_313)
      r1_313.Name = "SkyVelocity"
      r1_313.Velocity = Vector3.new(0, 100000000000000, 0)
      r1_313.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    end
  end
  local r160_0 = "FindFirstChild"
  r160_0 = "OuterUFO"
  local r158_0 = r6_0.Map.AlwaysHereTweenedObjects:[r160_0](r160_0)
  if r158_0 then
    local r161_0 = "FindFirstChild"
    r161_0 = "Object"
    if r158_0:[r161_0](r161_0) then
      r161_0 = "FindFirstChild"
      r161_0 = "ObjectModel"
      if r158_0.Object:[r161_0](r161_0) then
        r158_0 = r158_0.Object.ObjectModel.PaintPlayerPart
        r158_0:WaitForChild("WeldConstraint").Enabled = false
        r158_0.Anchored = true
        r158_0.Shape = Enum.PartType.Block
        r158_0.Transparency = 1
        r158_0.Size = Vector3.new(0.5, 0.5, 0.5)
        r158_0.Position = Vector3.new(0, -50, 0)
      end
    end
  end
  local r161_0 = "AddToggle"
  r161_0 = {
    Name = "Poison Aura",
    Default = false,
    Callback = function(r0_147)
      -- line: [0, 0] id: 147
      _G.Poison_Aura = r0_147
      if r0_147 then
        while _G.Poison_Aura do
          local r1_147 = pairs
          for r4_147, r5_147 in r1_147(r4_0:GetPlayers()) do
            if r153_0(r5_147) then
              local r6_147 = r5_147.Character:FindFirstChild("Head")
              local r7_147 = SNOWshipPlayer(r5_147)
              if r6_147 and r7_147 then
                r61_0.CFrame = r6_147.CFrame
                r62_0.CFrame = r6_147.CFrame
                r63_0.CFrame = r6_147.CFrame
                task.wait()
                r63_0.Position = Vector3.new(0, -50, 0)
                r62_0.Position = Vector3.new(0, -50, 0)
                r61_0.Position = Vector3.new(0, -50, 0)
              end
            end
          end
          task.wait()
        end
      end
    end,
    Save = true,
    Flag = "poisonaura_toggle",
  }
  r145_0:[r161_0](r161_0)
  r161_0 = "AddToggle"
  r161_0 = {
    Name = "Death Aura",
    Default = false,
    Callback = function(r0_99)
      -- line: [0, 0] id: 99
      _G.DeathAura = r0_99
      if r0_99 then
        while _G.DeathAura do
          local r1_99 = pairs
          for r4_99, r5_99 in r1_99(r4_0:GetPlayers()) do
            if r153_0(r5_99) then
              local r6_99 = r5_99.Character
              local r7_99 = r6_99:FindFirstChild("HumanoidRootPart")
              local r8_99 = r6_99:FindFirstChildOfClass("Humanoid")
              local r9_99 = SNOWshipPlayer(r5_99)
              if r7_99 and r8_99 and r9_99 then
                r23_0:FireServer(r7_99)
                CreateSkyVelocity(r7_99)
                r8_99.BreakJointsOnDeath = false
                r8_99:ChangeState(Enum.HumanoidStateType.Dead)
                r8_99.Jump = true
                r8_99.Sit = false
                if r8_99:GetStateEnabled(Enum.HumanoidStateType.Dead) then
                  r23_0:FireServer(r7_99)
                end
              end
            end
          end
          task.wait()
        end
      end
    end,
    Save = true,
    Flag = "deathaura_toggle",
  }
  r145_0:[r161_0](r161_0)
  if r158_0 then
    r161_0 = "AddToggle"
    r161_0 = {
      Name = "Radioactive Aura",
      Default = false,
      Callback = function(r0_166)
        -- line: [0, 0] id: 166
        _G.RadioactiveAura = r0_166
        if r0_166 then
          while _G.RadioactiveAura do
            local r1_166 = pairs
            for r4_166, r5_166 in r1_166(r4_0:GetPlayers()) do
              if r153_0(r5_166) then
                local r7_166 = r5_166.Character:FindFirstChild("HumanoidRootPart")
                local r8_166 = SNOWshipPlayer(r5_166)
                if r7_166 and r8_166 then
                  r158_0.Position = r7_166.Position
                  task.wait()
                  r158_0.Position = Vector3.new(0, -50, 0)
                end
              end
            end
            task.wait()
          end
        end
      end,
      Save = true,
      Flag = "radioaura_toggle",
    }
    r145_0:[r161_0](r161_0)
  end
  r161_0 = "AddToggle"
  r161_0 = {
    Name = "Burn Aura",
    Default = false,
    Callback = function(r0_291)
      -- line: [0, 0] id: 291
      _G.BurnAura = r0_291
      if r0_291 then
        while _G.BurnAura do
          local r1_291 = pairs
          for r4_291, r5_291 in r1_291(r4_0:GetPlayers()) do
            if r153_0(r5_291) then
              local r7_291 = r5_291.Character:FindFirstChild("HumanoidRootPart")
              if r7_291 and r16_0:DistanceFromCharacter(r7_291.Position) < 30 then
                r79_0(r7_291)
              end
            end
          end
          task.wait()
        end
      end
    end,
    Save = true,
    Flag = "burnaura_toggle",
  }
  r145_0:[r161_0](r161_0)
  r161_0 = "AddToggle"
  r161_0 = {
    Name = "Fling Aura",
    Default = false,
    Callback = function(r0_145)
      -- line: [0, 0] id: 145
      _G.FlingAura = r0_145
      if r0_145 then
        local r1_145 = nil
        while _G.FlingAura do
          local r2_145 = _G.FlingTarget
          if r2_145 ~= 2 then
            r2_145 = _G.FlingTarget
            if r2_145 == 3 then
              ::label_17::
              local r2_145, r3_145 = CheckObjectsAroundPlayer()
              if r2_145 then
                for r7_145, r8_145 in pairs(r2_145) do
                  attempts = 0
                  if r8_145 then
                    local r9_145 = r8_145:FindFirstChild("Head")
                    for r13_145, r14_145 in pairs(r8_145:GetChildren()) do
                      if r14_145:IsA("BasePart") and r14_145.CanQuery then
                        local r15_145 = SNOWshipOnce(r14_145)
                        local r16_145 = GetPlayerRoot()
                        local r17_145 = nil
                        if not r15_145 and r9_145 then
                          r15_145 = CheckNetworkOwnerShipOnPart(r9_145)
                        end
                        if r15_145 and r16_145 then
                          if not r3_145 then
                            if not r14_145:FindFirstChild("FlingAuraVelocity") then
                              local r18_145 = lookAt(r16_145.Position, r14_145.Position)
                              local r19_145 = Instance.new("BodyVelocity", r14_145)
                              r19_145.Name = "FlingAuraVelocity"
                              r19_145.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                              r19_145.Velocity = Vector3.new(r18_145.lookVector.X, 0.5, r18_145.lookVector.Z) * math.clamp(_G.FlingStrength, 400, 600)
                              r5_0:AddItem(r19_145)
                            end
                          else
                            local r18_145 = r3_145.Position
                            r3_145.Position = r14_145.Position
                            task.wait()
                            r3_145.Position = r18_145
                          end
                          attempts = attempts + 1
                        end
                        if attempts >= 3 then
                          break
                        end
                      end
                    end
                  end
                end
              end
            end
          else

          end
          r2_145 = _G.FlingTarget
          if r2_145 ~= 1 then
            r2_145 = _G.FlingTarget
            if r2_145 == 3 then
              ::label_139::
              r2_145 = pairs
              for r5_145, r6_145 in r2_145(r4_0:GetPlayers()) do
                if r153_0(r6_145) then
                  local r8_145 = r6_145.Character:FindFirstChild("HumanoidRootPart")
                  local r9_145 = SNOWshipPlayer(r6_145)
                  local r10_145 = GetPlayerCharacter()
                  local r11_145 = nil
                  if r8_145 and r9_145 and r10_145 and not r8_145:FindFirstChild("FlingAuraVelocity") then
                    local r12_145 = lookAt(r10_145.HumanoidRootPart.Position, r8_145.Position)
                    local r13_145 = Instance.new("BodyVelocity", r8_145)
                    r13_145.Name = "FlingAuraVelocity"
                    r13_145.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    r13_145.Velocity = Vector3.new(r12_145.lookVector.X, 0.5, r12_145.lookVector.Z) * _G.FlingStrength
                    r5_0:AddItem(r13_145)
                  end
                end
              end
            end
          else

          end
          task.wait(0.1)
        end
      end
    end,
    Save = true,
    Flag = "flingaura_toggle",
  }
  r146_0:[r161_0](r161_0)
  r161_0 = "AddSlider"
  r161_0 = {
    Name = "Strength",
    Min = 400,
    Max = 10000,
    Default = 400,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 100,
    ValueName = "",
    Callback = function(r0_230)
      -- line: [0, 0] id: 230
      _G.FlingStrength = r0_230
    end,
    Save = true,
    Flag = "flingstrengthvalue_toggle",
  }
  r146_0:[r161_0](r161_0)
  r161_0 = "AddDropdown"
  r161_0 = {
    Name = "Target",
    Default = "Players",
    Options = {
      "Players",
      "Objects",
      "Players and Objects"
    },
    Callback = function(r0_100)
      -- line: [0, 0] id: 100
      if r0_100 == "Players" then
        _G.FlingTarget = 1
      elseif r0_100 == "Objects" then
        _G.FlingTarget = 2
      elseif r0_100 == "Players and Objects" then
        _G.FlingTarget = 3
      end
    end,
    Save = true,
    Flag = "flingtarget_dropdown",
  }
  r146_0:[r161_0](r161_0)
  r161_0 = "AddToggle"
  r161_0 = {
    Name = "Attraction Aura",
    Default = false,
    Callback = function(r0_170)
      -- line: [0, 0] id: 170
      _G.AttractionAura = r0_170
      if r0_170 then
        while _G.AttractionAura do
          local r1_170 = pairs
          for r4_170, r5_170 in r1_170(r4_0:GetPlayers()) do
            if r153_0(r5_170) then
              local r6_170 = r5_170.Character
              local r7_170 = r6_170:FindFirstChild("HumanoidRootPart")
              local r8_170 = r6_170:FindFirstChildOfClass("Humanoid")
              local r9_170 = GetPlayerCharacter()
              local r10_170 = nil
              if r8_170 and r7_170 and r9_170 then
                SNOWship(r7_170)
                r8_170.Sit = false
                r8_170.WalkSpeed = 25
                r8_170:MoveTo(r9_170.HumanoidRootPart.Position)
              end
            end
          end
          task.wait()
        end
      end
    end,
    Save = true,
    Flag = "attractaura_toggle",
  }
  r145_0:[r161_0](r161_0)
  kickauratoggle = nil
  KickTypesList = {
    "Silent",
    "Float",
    "Sky Anchor"
  }
  function CreateKickPhysical(r0_106, r1_106, r2_106)
    -- line: [0, 0] id: 106
    local r3_106 = r1_106:FindFirstChild("KickAuraP")
    if not r3_106 then
      r3_106 = nil
      local r4_106 = nil
      r3_106 = Instance.new("BodyPosition", r1_106)
      r3_106.Name = "KickAuraP"
      r3_106:SetAttribute("TypeFunction", r2_106)
      r4_106 = Instance.new("BodyVelocity", r1_106)
      r4_106.Name = "KickAuraP1"
      r4_106.Velocity = Vector3.new(0, 400, 0)
      task.spawn(function()
        -- line: [0, 0] id: 107
        local r1_107 = nil	-- notice: implicit variable refs by block#[0]
        local r0_107 = nil	-- notice: implicit variable refs by block#[0]
        local r2_107 = Vector3.new(0, -100, 0)
        local r3_107 = Vector3.new(0, 0, 0)
        local r4_107 = Vector3.new(0, 12500, 0)
        local r5_107 = Vector3.new(4000, 4000, 4000)
        local r6_107 = Vector3.new(math.random(50, 250), 250, math.random(50, 250))
        local r7_107 = RaycastParams.new()
        r7_107.FilterDescendantsInstances = {
          r0_106
        }
        r7_107.FilterType = Enum.RaycastFilterType.Exclude
        local function r8_107(r0_108)
          -- line: [0, 0] id: 108
          if r0_108 == "Silent" then
            r3_106.MaxForce = r4_107
            r4_106.MaxForce = r3_107
            r0_107 = r1_106.Position
            r1_107 = r6_0:Raycast(r0_107, r2_107, r7_107)
            if r1_107 then
              r3_106.Position = r1_107.Position + Vector3.new(0, 5, 0)
            end
          elseif r0_108 == "Float" then
            r4_106.MaxForce = r5_107
            r3_106.MaxForce = r3_107
          elseif r0_108 == "Sky Anchor" then
            r3_106.MaxForce = r5_107
            r3_106.Position = r6_107
            r4_106.MaxForce = r3_107
          end
        end
        while r3_106.Parent do
          local r9_107 = r0_106.Parent
          if r9_107 then
            r2_106 = r3_106:GetAttribute("TypeFunction")
            r9_107 = r2_106
            if r9_107 ~= "Aura" then
              r9_107 = r2_106
              if not r9_107 then
                ::label_81::
                r9_107 = _G.KickAura
                if r9_107 then
                  r8_107(_G.KickAuraType)
                else
                  break
                end
              else
                r9_107 = r2_106
                if r9_107 == "Counter" then
                  r9_107 = _G.AutoAttacker
                  if not r9_107 then
                    break
                  else
                    r8_107(_G.KickCounterType)
                  end
                else
                  r9_107 = r2_106
                  if r9_107 == "Kick_All" then
                    r9_107 = _G.KickAll
                    if not r9_107 then
                      break
                    else
                      r8_107(_G.KickAllType)
                    end
                  else
                    r9_107 = r2_106
                    if r9_107 == "LoopKick" then
                      r9_107 = _G.LoopKickOwnership
                      if r9_107 then
                        r8_107(_G.LoopKickOwnerType)
                      else
                        break
                      end
                    end
                  end
                end
              end
            else

            end
            task.wait()
          else
            break
          end
        end
        r3_106:Destroy()
        r4_106:Destroy()
      end)
      -- close: r3_106
    else
      r3_106 = r1_106.KickAuraP
      r3_106:SetAttribute("TypeFunction", r2_106)
    end
  end
  r161_0 = "AddToggle"
  r161_0 = {
    Name = "Kick Aura",
    Default = false,
    Callback = function(r0_130)
      -- line: [0, 0] id: 130
      _G.KickAura = r0_130
      if r0_130 then
        while _G.KickAura do
          local r1_130 = getgenv().key
          if r1_130 ~= "Xana" then
            kickauratoggle:Set(false)
            r34_0("Only for premium users! Buy premium in my discord server!")
            break
          else
            r1_130 = pairs
            for r4_130, r5_130 in r1_130(r4_0:GetPlayers()) do
              if r154_0(r5_130) then
                local r6_130 = r5_130.Character
                local r7_130 = r6_130:FindFirstChild("HumanoidRootPart")
                local r8_130 = r6_130:FindFirstChildOfClass("Humanoid")
                local r9_130 = r7_130:FindFirstChild("FirePlayerPart")
                local r10_130 = SNOWshipPlayer(r5_130)
                if r7_130 and r8_130 and r9_130 and r10_130 then
                  CreateSkyVelocity(r7_130)
                  r23_0:FireServer(r7_130)
                end
              end
            end
            task.wait()
          end
        end
      end
    end,
  }
  kickauratoggle = r147_0:[r161_0](r161_0)
  r161_0 = "AddDropdown"
  r161_0 = {
    Name = "Kick Type",
    Default = "Go to the heaven!",
    Options = {
      "Go to the heaven!"
    },
    Callback = function(r0_337)
      -- line: [0, 0] id: 337
      _G.KickAuraType = r0_337
    end,
    Save = true,
    Flag = "kickauratype_dropdown",
  }
  r147_0:[r161_0](r161_0)
  r161_0 = "AddToggle"
  r161_0 = {
    Name = "Whitelist Friends",
    Default = false,
    Callback = function(r0_93)
      -- line: [0, 0] id: 93
      _G.WhitelistFriends = r0_93
    end,
    Save = true,
    Flag = "whitelistaura_toggle",
  }
  r148_0:[r161_0](r161_0)
  r161_0 = "AddSection"
  r161_0 = {
    Name = "Strength",
  }
  local r159_0 = r84_0:[r161_0](r161_0)
  local r162_0 = "AddSection"
  r162_0 = {
    Name = "Others",
  }
  r160_0 = r84_0:[r162_0](r162_0)
  local r163_0 = "AddSection"
  r163_0 = {
    Name = "Perspective",
  }
  r161_0 = r84_0:[r163_0](r163_0)
  local r164_0 = "AddToggle"
  r164_0 = {
    Name = "Super Strength",
    Default = false,
    Callback = function(r0_233)
      -- line: [0, 0] id: 233
      _G.SuperStrength = r0_233
    end,
    Save = true,
    Flag = "superstrengthgrab_toggle",
  }
  r159_0:[r164_0](r164_0)
  r164_0 = "AddSlider"
  r164_0 = {
    Name = "Strength",
    Min = 400,
    Max = 10000,
    Default = 400,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 100,
    ValueName = "",
    Callback = function(r0_217)
      -- line: [0, 0] id: 217
      _G.Strength = r0_217
    end,
    Save = true,
    Flag = "superstrengthvalue_toggle",
  }
  r159_0:[r164_0](r164_0)
  r164_0 = "AddToggle"
  r164_0 = {
    Name = "Poison Grab",
    Default = false,
    Callback = function(r0_274)
      -- line: [0, 0] id: 274
      _G.Poison_Grab = r0_274
    end,
    Save = true,
    Flag = "poisongrab_toggle",
  }
  r160_0:[r164_0](r164_0)
  r164_0 = "AddToggle"
  r164_0 = {
    Name = "Burn Grab",
    Default = false,
    Callback = function(r0_38)
      -- line: [0, 0] id: 38
      _G.Burn_Grab = r0_38
    end,
    Save = true,
    Flag = "burngrab_toggle",
  }
  r160_0:[r164_0](r164_0)
  r164_0 = "AddToggle"
  r164_0 = {
    Name = "Death Grab",
    Default = false,
    Callback = function(r0_64)
      -- line: [0, 0] id: 64
      _G.Death_Grab = r0_64
    end,
    Save = true,
    Flag = "deathgrab_toggle",
  }
  r160_0:[r164_0](r164_0)
  r164_0 = "AddToggle"
  r164_0 = {
    Name = "Massless Grab",
    Default = false,
    Callback = function(r0_187)
      -- line: [0, 0] id: 187
      _G.MasslessGrab = r0_187
    end,
    Save = true,
    Flag = "masslessgrab_toggle",
  }
  r160_0:[r164_0](r164_0)
  if r158_0 then
    r164_0 = "AddToggle"
    r164_0 = {
      Name = "Radiactive Grab",
      Default = false,
      Callback = function(r0_265)
        -- line: [0, 0] id: 265
        _G.Radiactive_Grab = r0_265
      end,
      Save = true,
      Flag = "radiactivegrab_toggle",
    }
    r160_0:[r164_0](r164_0)
  end
  r164_0 = "AddToggle"
  r164_0 = {
    Name = "Noclip Grab",
    Default = false,
    Callback = function(r0_299)
      -- line: [0, 0] id: 299
      _G.NoclipGrab = r0_299
    end,
    Save = true,
    Flag = "noclipgrab_toggle",
  }
  r160_0:[r164_0](r164_0)
  r162_0 = nil
  r163_0 = 50
  kickgrabtoggle = nil
  local r166_0 = "AddToggle"
  r166_0 = {
    Name = "Perspective Grab",
    Default = false,
    Callback = function(r0_43)
      -- line: [0, 0] id: 43
      _G.PerspectiveGrab = r0_43
    end,
    Save = true,
    Flag = "perspectivegrab_toggle",
  }
  r161_0:[r166_0](r166_0)
  r166_0 = "AddSlider"
  r166_0 = {
    Name = "Speed",
    Min = 50,
    Max = 150,
    Default = 50,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 1,
    ValueName = "",
    Callback = function(r0_101)
      -- line: [0, 0] id: 101
      r163_0 = r0_101
    end,
    Save = true,
    Flag = "perspectivespeedvalue_toggle",
  }
  r161_0:[r166_0](r166_0)
  r164_0 = nil
  local r167_0 = "AddSection"
  r167_0 = {
    Name = "Annoy Players",
  }
  local r165_0 = r94_0:[r167_0](r167_0)
  local r168_0 = "AddSection"
  r168_0 = {
    Name = "Kick All",
  }
  r166_0 = r94_0:[r168_0](r168_0)
  local r169_0 = "AddSection"
  r169_0 = {
    Name = "Whitelist",
  }
  r167_0 = r94_0:[r169_0](r169_0)
  r164_0 = nil
  local r170_0 = "AddToggle"
  r170_0 = {
    Name = "Fire All",
    Default = false,
    Callback = function(r0_156)
      -- line: [0, 0] id: 156
      _G.FireAllPlayers = r0_156
      if r0_156 then
        while _G.FireAllPlayers do
          local r1_156 = getgenv().key
          if r1_156 ~= "Xana" then
            r164_0:Set(false)
            r34_0("Only for premium users! Buy premium in my discord server!")
            break
          else
            r1_156 = pairs
            for r4_156, r5_156 in r1_156(r4_0:GetPlayers()) do
              if r155_0(r5_156) then
                local r6_156 = r5_156
                local r7_156 = r6_156.Character
                local r8_156 = r6_156.Character:FindFirstChild("HumanoidRootPart")
                local r9_156 = nil
                if r8_156:FindFirstChild("FirePlayerPart") and r8_156.FirePlayerPart:FindFirstChild("CanBurn") then
                  r9_156 = r8_156.FirePlayerPart.CanBurn.Value
                end
                if r8_156 and r6_156 and not IsPlayerInsideSafeZone(r6_156) and not r9_156 then
                  r79_0(r8_156)
                  task.wait(0.015)
                end
              end
            end
            task.wait()
          end
        end
      end
    end,
  }
  r164_0 = r165_0:[r170_0](r170_0)
  r170_0 = "AddToggle"
  r170_0 = {
    Name = "Ragdoll All",
    Default = false,
    Callback = function(r0_346)
      -- line: [0, 0] id: 346
      _G.AnnoyAllPlayers = r0_346
      if r0_346 then
        while _G.AnnoyAllPlayers do
          local r1_346 = getgenv().key
          if r1_346 ~= "Xana" then
            annoyalltoggle:Set(false)
            r34_0("Only for premium users! Buy premium in my discord server!")
            break
          else
            r1_346 = pairs
            for r4_346, r5_346 in r1_346(r4_0:GetPlayers()) do
              if r155_0(r5_346) then
                local r6_346 = r5_346
                local r8_346 = r6_346.Character:FindFirstChild("HumanoidRootPart")
                local r9_346 = r6_346.Character:FindFirstChildOfClass("Humanoid"):FindFirstChild("Ragdolled")
                if r8_346 and r9_346 and not r9_346.Value then
                  r74_0(r8_346)
                  task.wait(0.015)
                end
              end
            end
            task.wait()
          end
        end
      end
    end,
  }
  annoyalltoggle = r165_0:[r170_0](r170_0)
  r170_0 = "AddToggle"
  r170_0 = {
    Name = "Kill All",
    Default = false,
    Callback = function(r0_11)
      -- line: [0, 0] id: 11
      _G.KillAll = r0_11
      if r0_11 then
        if getgenv().key ~= "Xana" then
          _G.KillAll = false
          killalltoggle:Set(false)
          r34_0("Only for premium users! Buy premium in my discord server!")
          return 
        end
        while _G.KillAll do
          ipos = GetPlayerCFrame()
          local r1_11 = pairs
          for r4_11, r5_11 in r1_11(r4_0:GetPlayers()) do
            if r156_0(r5_11) then
              local r6_11 = r5_11
              local r7_11 = r6_11.Character:FindFirstChild("HumanoidRootPart")
              local r8_11 = r6_11.Character:FindFirstChild("Humanoid")
              if r6_11 and r7_11 and r8_11 then
                for r12_11 = 0, 50, 1 do
                  r49_0()
                  SNOWship(r7_11)
                  if not r156_0(r6_11) or not _G.KillAll or CheckNetworkOwnerShipOnPlayer(r6_11) or 500 < r7_11.AssemblyLinearVelocity.Magnitude then
                    CreateSkyVelocity(r7_11)
                    r23_0:FireServer(r7_11)
                    break
                  else
                    task.wait()
                    if r7_11.Position.Y > -12 then
                      TeleportPlayer(CFrame.new(r7_11.Position + Vector3.new(0, -10, -10)))
                    else
                      TeleportPlayer(CFrame.new(r7_11.Position + Vector3.new(0, 5, -15)))
                    end
                    r8_11.BreakJointsOnDeath = false
                    r8_11:ChangeState(Enum.HumanoidStateType.Dead)
                    r8_11.Jump = true
                    r8_11.Sit = false
                  end
                end
              end
            end
          end
          TeleportPlayer(ipos)
          task.wait(0.2)
        end
        r50_0()
        TeleportPlayer(ipos)
      end
    end,
  }
  killalltoggle = r165_0:[r170_0](r170_0)
  r170_0 = "AddToggle"
  r170_0 = {
    Name = "Kick All",
    Default = false,
    Callback = function(r0_309)
      -- line: [0, 0] id: 309
      _G.KickAll = r0_309
      if r0_309 then
        if getgenv().key ~= "Xana" then
          _G.KickAll = false
          kickalltoggle:Set(false)
          r34_0("Only for premium users! Buy premium in my discord server!")
          return 
        end
        while _G.KickAll do
          ipos = GetPlayerCFrame()
          local r1_309 = pairs
          for r4_309, r5_309 in r1_309(r4_0:GetPlayers()) do
            if r157_0(r5_309) then
              local r6_309 = r5_309
              local r7_309 = r6_309.Character:FindFirstChild("HumanoidRootPart")
              if r6_309 and r7_309 then
                for r11_309 = 0, 50, 1 do
                  r49_0()
                  SNOWship(r7_309)
                  if not r157_0(r6_309) or not _G.KickAll or CheckNetworkOwnerShipOnPlayer(r6_309) or 500 < r7_309.AssemblyLinearVelocity.Magnitude then
                    CreateSkyVelocity(r7_309)
                    r23_0:FireServer(r7_309)
                    break
                  else
                    task.wait()
                    if r7_309.Position.Y > -12 then
                      TeleportPlayer(CFrame.new(r7_309.Position + Vector3.new(0, -10, -10)))
                    else
                      TeleportPlayer(CFrame.new(r7_309.Position + Vector3.new(0, 5, -15)))
                    end
                  end
                end
              end
            end
          end
          TeleportPlayer(ipos)
          task.wait(0.2)
        end
        r50_0()
        TeleportPlayer(ipos)
      end
    end,
  }
  kickalltoggle = r166_0:[r170_0](r170_0)
  r170_0 = "AddDropdown"
  r170_0 = {
    Name = "Kick Type",
    Default = "Go to the heaven!",
    Options = {
      "Go to the heaven!"
    },
    Callback = function(r0_204)
      -- line: [0, 0] id: 204
      _G.KickAllType = r0_204
    end,
    Save = true,
    Flag = "kickalltype_dropdown",
  }
  r166_0:[r170_0](r170_0)
  r170_0 = "AddToggle"
  r170_0 = {
    Name = "Whitelist Friends",
    Default = false,
    Callback = function(r0_245)
      -- line: [0, 0] id: 245
      _G.WhitelistFriends3 = r0_245
    end,
    Save = true,
    Flag = "whitelistfriends3_toggle",
  }
  r167_0:[r170_0](r170_0)
  r170_0 = "AddSection"
  r170_0 = {
    Name = "Invulnerability",
  }
  r168_0 = r85_0:[r170_0](r170_0)
  local r171_0 = "AddSection"
  r171_0 = {
    Name = "Counter-Attack",
  }
  r169_0 = r85_0:[r171_0](r171_0)
  local r172_0 = "AddToggle"
  r172_0 = {
    Name = "Anti-Grab",
    Default = false,
    Callback = function(r0_221)
      -- line: [0, 0] id: 221
      _G.AntiGrab = r0_221
      if r0_221 and not r38_0(r30_0) then
        r31_0:FireServer(r16_0)
      end
    end,
    Save = true,
    Flag = "antigrab_toggle",
  }
  r168_0:[r172_0](r172_0)
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "Anti-Burn",
    Default = false,
    Callback = function(r0_84)
      -- line: [0, 0] id: 84
      _G.AntiBurn = r0_84
    end,
    Save = true,
    Flag = "antiburn_toggle",
  }
  r168_0:[r172_0](r172_0)
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "Anti-Explosion",
    Default = false,
    Callback = function(r0_96)
      -- line: [0, 0] id: 96
      _G.AntiExplosion = r0_96
    end,
    Save = true,
    Flag = "antiexplosion_toggle",
  }
  r168_0:[r172_0](r172_0)
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "Auto-Attacker",
    Default = false,
    Callback = function(r0_7)
      -- line: [0, 0] id: 7
      _G.AutoAttacker = r0_7
    end,
    Save = true,
    Flag = "rinnegan_toggle",
  }
  r169_0:[r172_0](r172_0)
  counterdropdownselection = nil
  r172_0 = "AddDropdown"
  r172_0 = {
    Name = "Counter Mode",
    Default = "Repulsion",
    Options = {
      "Repulsion",
      "Freeze",
      "Death",
      "Kick"
    },
    Callback = function(r0_202)
      -- line: [0, 0] id: 202
      if r0_202 == "Kick" and key ~= "Xana" then
        counterdropdownselection:Set("Repulsion")
        r34_0("Only for premium users! Buy premium in my discord server!")
        return 
      end
      _G.CounterMode = r0_202
    end,
  }
  counterdropdownselection = r169_0:[r172_0](r172_0)
  floppadialogo = Instance.new("ScreenGui")
  Floppa = Instance.new("ImageLabel")
  Bubble_chat = Instance.new("ImageLabel")
  BubbleTextchat = Instance.new("TextLabel")
  typingsoundeffect = Instance.new("Sound", r6_0)
  typingsoundeffect2 = Instance.new("Sound", r6_0)
  typingsoundeffect.SoundId = "rbxassetid://" .. 9120299506
  typingsoundeffect.Volume = 0.345
  typingsoundeffect2.SoundId = "rbxassetid://" .. 9118870964
  typingsoundeffect2.Volume = 1
  typingsoundeffect2.PlaybackSpeed = 1.5
  floppadialogo.IgnoreGuiInset = true
  floppadialogo.ScreenInsets = Enum.ScreenInsets.DeviceSafeInsets
  floppadialogo.Name = "floppadialogo"
  floppadialogo.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
  floppadialogo.Parent = r17_0
  floppadialogo.DisplayOrder = 10
  floppadialogo.Enabled = false
  floppadialogo.ResetOnSpawn = false
  Floppa.ZIndex = 0
  Floppa.BorderSizePixel = 0
  Floppa.BackgroundColor3 = Color3.new(1, 1, 1)
  Floppa.Image = "rbxassetid://15668608167"
  Floppa.Size = UDim2.new(0.195372716, 0, 0.305668026, 0)
  Floppa.BorderColor3 = Color3.new(0, 0, 0)
  Floppa.Position = UDim2.new(0.0185752641, 0, 0.661330521, 0)
  Floppa.Name = "Floppa"
  Floppa.Parent = floppadialogo
  Bubble_chat.BorderSizePixel = 0
  Bubble_chat.Transparency = 1
  Bubble_chatBackgroundColor3 = Color3.new(1, 1, 1)
  Bubble_chat.Image = "rbxassetid://1395860348"
  Bubble_chat.Size = UDim2.new(1.03356743, 0, 0.79455024, 0)
  Bubble_chat.BorderColor3 = Color3.new(0, 0, 0)
  Bubble_chat.BackgroundTransparency = 1
  Bubble_chat.Position = UDim2.new(0.678329766, 0, -0.292054504, 0)
  Bubble_chat.Name = "Bubble chat"
  Bubble_chat.Parent = Floppa
  BubbleTextchat.TextWrapped = true
  BubbleTextchat.BorderSizePixel = 0
  BubbleTextchat.Transparency = 1
  BubbleTextchat.TextScaled = true
  BubbleTextchat.BackgroundColor3 = Color3.new(1, 1, 1)
  BubbleTextchat.TextSize = 14
  BubbleTextchat.Size = UDim2.new(0.634431362, 0, 0.268763244, 0)
  BubbleTextchat.TextColor3 = Color3.new(0, 0, 0)
  BubbleTextchat.BorderColor3 = Color3.new(0, 0, 0)
  BubbleTextchat.Text = "I saved you from falling on the void, my son!"
  BubbleTextchat.Font = Enum.Font.SourceSans
  BubbleTextchat.Position = UDim2.new(0.18163082, 0, 0.365639389, 0)
  BubbleTextchat.BackgroundTransparency = 1
  BubbleTextchat.TextTransparency = 0
  BubbleTextchat.Parent = Bubble_chat
  floppatweeninfo1 = TweenInfo.new(1, Enum.EasingStyle.Linear, Enum.EasingDirection.In, 0, false, 0)
  floppatween = r8_0:Create(Floppa, floppatweeninfo1, {
    Position = UDim2.new(0.0185752641, 0, 0.661330521, 0),
  })
  floppamessageoncooldown = false
  function antivoidmesssage()
    -- line: [0, 0] id: 367
    if not floppamessageoncooldown then
      Floppa.Position = UDim2.new(0.0185752641, 0, 2, 0)
      floppadialogo.Enabled = true
      Floppa.Visible = true
      Bubble_chat.Visible = false
      BubbleTextchat.Visible = false
      floppamessageoncooldown = true
      floppatween:Play()
      floppatween.Completed:Connect(function(r0_368)
        -- line: [0, 0] id: 368
        if r0_368 == Enum.PlaybackState.Completed then
          Bubble_chat.Visible = true
          BubbleTextchat.Visible = true
          BubbleTextchat.Text = ""
          local r1_368 = "I saved you from falling on the void, my son!"
          for r5_368 = 0, #r1_368, 1 do
            BubbleTextchat.Text = string.sub(r1_368, 1, r5_368)
            typingsoundeffect:Play()
            task.wait(0.05)
          end
          task.wait(1)
          typingsoundeffect2:Play()
          floppadialogo.Enabled = false
          floppamessageoncooldown = false
        end
      end)
    end
  end
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "Anti-Void",
    Default = false,
    Callback = function(r0_70)
      -- line: [0, 0] id: 70
      _G.AntiVoid = r0_70
      if r0_70 then
        r6_0.FallenPartsDestroyHeight = -1000
        local r1_70 = nil
        while _G.AntiVoid do
          local r2_70 = GetPlayerCharacter()
          r1_70 = r2_70
          if r1_70 then
            r2_70 = r1_70.HumanoidRootPart.Position.Y
            if r2_70 < -800 then
              r1_70:SetPrimaryPartCFrame(CFrame.new(0, 0, 0))
              antivoidmesssage()
            end
          end
          wait(0.1)
        end
      else
        r6_0.FallenPartsDestroyHeight = -100
      end
    end,
    Save = true,
    Flag = "antivoid_toggle",
  }
  r168_0:[r172_0](r172_0)
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "Anti-Lag",
    Default = false,
    Callback = function(r0_6)
      -- line: [0, 0] id: 6
      anticreatelinelocalscript.Disabled = r0_6
    end,
    Save = true,
    Flag = "antilag_toggle",
  }
  r168_0:[r172_0](r172_0)
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "Anti-Kick",
    Default = false,
    Callback = function(r0_366)
      -- line: [0, 0] id: 366
      if getgenv().key ~= "Xana" then
        _G.AntiKick = false
        if r0_366 then
          antikicktoggle:Set(false)
          r34_0("Only for premium users! Buy premium in my discord server!")
        end
      else
        _G.AntiKick = r0_366
      end
    end,
    Save = true,
    Flag = "antikick_toggle",
  }
  antikicktoggle = r168_0:[r172_0](r172_0)
  playersCharFolder = Instance.new("Model", r6_0)
  playersCharFolder.Name = "Characters"
  highlightesp = Instance.new("Highlight")
  highlightesp.Enabled = true
  r172_0 = "AddSection"
  r172_0 = {
    Name = "ESP Highlight",
  }
  ESP_Section1 = Esp_Tab:[r172_0](r172_0)
  r172_0 = "AddSection"
  r172_0 = {
    Name = "ESP Billboard",
  }
  ESP_Section2 = Esp_Tab:[r172_0](r172_0)
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "ESP (Highlight)",
    Default = false,
    Callback = function(r0_253)
      -- line: [0, 0] id: 253
      _G.ESP_Hightlight = r0_253
      if r0_253 then
        local r1_253 = {}
        highlightesp.Parent = playersCharFolder
        local function r2_253(r0_255)
          -- line: [0, 0] id: 255
          if r0_255 ~= r16_0 then
            local r1_255 = r0_255.Character
            if r1_255 then
              r1_255.Parent = playersCharFolder
            end
          end
        end
        local function r3_253()
          -- line: [0, 0] id: 254
          for r3_254, r4_254 in pairs(r4_0:GetPlayers()) do
            r2_253(r4_254)
          end
        end
        r3_253()
        while _G.ESP_Hightlight do
          r3_253()
          wait(2)
        end
        highlightesp.Parent = nil
        -- close: r1_253
      end
    end,
  }
  ESP_Section1:[r172_0](r172_0)
  r172_0 = "AddColorpicker"
  r172_0 = {
    Name = "Fill Color",
    Default = Color3.fromRGB(255, 0, 0),
    Callback = function(r0_8)
      -- line: [0, 0] id: 8
      highlightesp.FillColor = r0_8
    end,
    Save = true,
    Flag = "espHighlightFillcolor_picker",
  }
  ESP_Section1:[r172_0](r172_0)
  r172_0 = "AddSlider"
  r172_0 = {
    Name = "Fill Transparency",
    Min = 0,
    Max = 1,
    Default = 0.5,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 0.1,
    ValueName = "Fill color transparency:",
    Callback = function(r0_311)
      -- line: [0, 0] id: 311
      highlightesp.FillTransparency = r0_311
    end,
    Save = true,
    Flag = "espHighlightFillTransparency_slider",
  }
  ESP_Section1:[r172_0](r172_0)
  r172_0 = "AddColorpicker"
  r172_0 = {
    Name = "Outline Color",
    Default = Color3.fromRGB(255, 0, 0),
    Callback = function(r0_312)
      -- line: [0, 0] id: 312
      highlightesp.OutlineColor = r0_312
    end,
    Save = true,
    Flag = "espHighlightOutlinecolor_picker",
  }
  ESP_Section1:[r172_0](r172_0)
  r172_0 = "AddSlider"
  r172_0 = {
    Name = "Outline Transparency",
    Min = 0,
    Max = 1,
    Default = 0.5,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 0.1,
    ValueName = "Outline color transparency:",
    Callback = function(r0_329)
      -- line: [0, 0] id: 329
      highlightesp.OutlineTransparency = r0_329
    end,
    Save = true,
    Flag = "espHighlightOutlineTransparency_slider",
  }
  ESP_Section1:[r172_0](r172_0)
  r170_0 = ESP_Section1
  r172_0 = "AddDropdown"
  r172_0 = {
    Name = "Highlight Mode",
    Default = "AlwaysOnTop",
    Options = {
      "AlwaysOnTop",
      "Occluded"
    },
    Callback = function(r0_301)
      -- line: [0, 0] id: 301
      highlightesp.DepthMode = Enum.HighlightDepthMode[r0_301]
    end,
    Save = true,
    Flag = "espHighlightMode_dropdown",
  }
  r170_0:[r172_0](r172_0)
  function ESPIconCreation()
    -- line: [0, 0] id: 162
    local r0_162 = Instance.new("BillboardGui")
    local r1_162 = Instance.new("ImageButton")
    local r2_162 = Instance.new("UICorner")
    local r3_162 = Instance.new("TextLabel")
    local r4_162 = Instance.new("UITextSizeConstraint")
    local r5_162 = Instance.new("UIAspectRatioConstraint")
    r0_162.Name = "ESP"
    r0_162.Parent = nil
    r0_162.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    r0_162.Active = true
    r0_162.Adornee = nil
    r0_162.AlwaysOnTop = true
    r0_162.ExtentsOffset = Vector3.new(0, 10, 0)
    r0_162.Size = UDim2.new(3, 50, 3, 45)
    r1_162.Name = "UserImage"
    r1_162.Parent = r0_162
    r1_162.AnchorPoint = Vector2.new(0.5, 0.5)
    r1_162.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    r1_162.BackgroundTransparency = 1
    r1_162.BorderColor3 = Color3.fromRGB(0, 0, 0)
    r1_162.BorderSizePixel = 0
    r1_162.Position = UDim2.new(0.5, 0, 0.300000012, 0)
    r1_162.Size = UDim2.new(0.5, 5, 0.5, 5)
    r1_162.Image = ""
    r2_162.CornerRadius = UDim.new(2, 0)
    r2_162.Parent = r1_162
    r3_162.Name = "Username"
    r3_162.Parent = r0_162
    r3_162.AnchorPoint = Vector2.new(0.5, 0.5)
    r3_162.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    r3_162.BackgroundTransparency = 1
    r3_162.BorderColor3 = Color3.fromRGB(0, 0, 0)
    r3_162.BorderSizePixel = 0
    r3_162.Position = UDim2.new(0.5, 0, 0.75999999, 0)
    r3_162.Size = UDim2.new(1, 5, 0.340000004, 5)
    r3_162.Font = Enum.Font.SourceSans
    r3_162.Text = ""
    r3_162.TextColor3 = Color3.fromRGB(255, 255, 255)
    r3_162.TextScaled = true
    r3_162.TextSize = 35
    r3_162.TextStrokeTransparency = 0
    r3_162.TextWrapped = true
    r4_162.Parent = r3_162
    r4_162.MaxTextSize = 35
    r4_162.MinTextSize = 15
    r5_162.Parent = r0_162
    r5_162.AspectRatio = 1.043
    return r0_162
  end
  ESPIconCreation = ESPIconCreation()
  function CreateIconOnPlayer(r0_104)
    -- line: [0, 0] id: 104
    if r0_104.Character then
      local r1_104 = r0_104.Character
      local r2_104 = r1_104:WaitForChild("Head", 1)
      if not r1_104:FindFirstChild("ESP") and r2_104 then
        local r3_104 = ESPIconCreation:Clone()
        r3_104.Parent = r1_104
        r3_104.Adornee = r2_104
        r3_104.Username.Text = r0_104.Name
        r3_104.UserImage.Image = "https://www.roblox.com/headshot-thumbnail/image?userId=" .. r0_104.UserId .. "&width=420&height=420&format=png"
        task.spawn(function()
          -- line: [0, 0] id: 105
          while r1_104.Parent do
            local r0_105 = _G.ESP_Icon
            if r0_105 then
              task.wait(0.25)
            else
              break
            end
          end
          r3_104:Destroy()
        end)
        -- close: r3_104
      end
      -- close: r1_104
    end
  end
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "ESP (Icon)",
    Default = false,
    Callback = function(r0_193)
      -- line: [0, 0] id: 193
      _G.ESP_Icon = r0_193
      if r0_193 then
        local r1_193 = nil
        local r2_193 = {}
        local function r3_193()
          -- line: [0, 0] id: 197
          for r3_197, r4_197 in pairs(r2_193) do
            if typeof(r4_197) == "RBXScriptConnection" then
              r4_197:Disconnect()
              print("Desconectado!")
            end
          end
          table.clear(r2_193)
        end
        local function r4_193(r0_194)
          -- line: [0, 0] id: 194
          if r0_194 ~= r16_0 and (r0_194.Character or r0_194.CharacterAdded:Wait()) then
            CreateIconOnPlayer(r0_194)
            r2_193[#r2_193 + 1] = r0_194.CharacterAdded:Connect(function(r0_195)
              -- line: [0, 0] id: 195
              CreateIconOnPlayer(r0_194)
            end)
          end
        end
        r1_193 = r4_0.PlayerAdded:Connect(function(r0_198)
          -- line: [0, 0] id: 198
          r4_193(r0_198)
        end)
        (function()
          -- line: [0, 0] id: 196
          for r3_196, r4_196 in pairs(r4_0:GetPlayers()) do
            r4_193(r4_196)
          end
        end)()
        while _G.ESP_Icon do
          wait(0.1)
        end
        r1_193:Disconnect()
        r3_193()
        -- close: r1_193
      end
    end,
  }
  ESP_Section2:[r172_0](r172_0)
  r172_0 = "AddSection"
  r172_0 = {
    Name = "Place TP",
  }
  MapTeleport_Section = r88_0:[r172_0](r172_0)
  r172_0 = "AddSection"
  r172_0 = {
    Name = "Player TP",
  }
  PlayerTeleport_Section = r88_0:[r172_0](r172_0)
  placeLocations = {
    ["Green House"] = CFrame.new(-352, 99, 354),
    ["Green Safe-House"] = CFrame.new(-584, -6, 93),
    ["Chinese Safe-House"] = CFrame.new(579, 124, -94),
    ["Farm House"] = CFrame.new(-234, 83, -324),
    Spawn = CFrame.new(4, -7, -3),
    ["Blue Safe-House"] = CFrame.new(538, 96, -372),
    ["Secret Big Cave"] = CFrame.new(17, -7, 539),
    ["Secret Train Cave"] = CFrame.new(500, 62, -307),
    ["Mine Cave"] = CFrame.new(-254, -7, 518),
    ["Witch Safe-House"] = CFrame.new(296, -4, 494),
    ["Red Safe-House"] = CFrame.new(-516, -6, -162),
  }
  r170_0 = MapTeleport_Section
  r172_0 = "AddDropdown"
  r172_0 = {
    Name = "Place to Teleport",
    Default = "Green House",
    Options = {
      "Green House",
      "Chinese Safe-House",
      "Spawn",
      "Blue Safe-House",
      "Secret Big Cave",
      "Secret Train Cave",
      "Mine Cave",
      "Farm House",
      "Witch Safe-House",
      "Green Safe-House",
      "Red Safe-House"
    },
    Callback = function(r0_353)
      -- line: [0, 0] id: 353
      _G.PlaceToTeleport = r0_353
    end,
  }
  r170_0:[r172_0](r172_0)
  r172_0 = "AddButton"
  r172_0 = {
    Name = "Teleport",
    Callback = function()
      -- line: [0, 0] id: 334
      TeleportPlayer(placeLocations[_G.PlaceToTeleport])
    end,
  }
  MapTeleport_Section:[r172_0](r172_0)
  r170_0 = PlayerTeleport_Section
  r172_0 = "AddDropdown"
  r172_0 = {
    Name = "Select Player",
    Default = "",
    Options = {
      ""
    },
    Callback = function(r0_238)
      -- line: [0, 0] id: 238
      _G.PlayerToTeleport = string.split(r0_238, " ")[1]
    end,
  }
  PlayerToTeleport = r170_0:[r172_0](r172_0)
  function teleportplayerfunctionoffset(r0_231, r1_231, r2_231, r3_231)
    -- line: [0, 0] id: 231
    local r4_231 = nil	-- notice: implicit variable refs by block#[17]
    if _G.PlayerToTeleportDirection == "Behind" then
      r4_231 = CFrame.new(r0_231.Position - r0_231.lookVector * (TeleportPlayerOffset + 1))
    elseif _G.PlayerToTeleportDirection == "Front" then
      r4_231 = CFrame.new(r0_231.Position + r0_231.lookVector * (TeleportPlayerOffset + 1))
    elseif _G.PlayerToTeleportDirection == "Right" then
      r4_231 = CFrame.new(r0_231.Position + r0_231.rightVector * (TeleportPlayerOffset + 1))
    elseif _G.PlayerToTeleportDirection == "Left" then
      r4_231 = CFrame.new(r0_231.Position - r0_231.rightVector * (TeleportPlayerOffset + 1))
    elseif _G.PlayerToTeleportDirection == "Rotate" then
      local r5_231 = 0
      local r6_231 = r1_231
      local r7_231 = r2_231
      if r6_231 and r2_231 then
        while _G.PlayerToTeleportDirection == "Rotate" do
          local r8_231 = _G.LoopPlayerTP
          if r8_231 then
            r8_231 = r7_231:IsDescendantOf(r6_0)
            if r8_231 then
              r8_231 = _G.PlayerToTeleport
              if r3_231 == r8_231 then
                r5_231 = r5_231 + 0.1
                r4_231 = CFrame.new(r6_231.Position + Vector3.new(math.clamp(math.cos(r5_231), -1, 1), 0, math.clamp(math.sin(r5_231), -1, 1)) * (TeleportPlayerOffset + 1), r6_231.Position)
                TeleportPlayer(r4_231)
                task.wait()
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
      end
    end
    if _G.PlayerToTeleportDirection ~= "Rotate" then
      TeleportPlayer(r4_231)
    end
  end
  r172_0 = "AddButton"
  r172_0 = {
    Name = "Teleport",
    Callback = function()
      -- line: [0, 0] id: 330
      local r0_330 = r4_0:FindFirstChild(_G.PlayerToTeleport)
      local r1_330 = GetPlayerRoot()
      local r2_330 = nil
      local r3_330 = nil
      if r0_330 and r0_330.Character and r1_330 then
        r3_330 = r0_330.Character:FindFirstChild("HumanoidRootPart")
        if r3_330 then
          teleportplayerfunctionoffset(r3_330.CFrame, r1_330)
        end
      end
    end,
  }
  PlayerTeleport_Section:[r172_0](r172_0)
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "Loop Teleport",
    Default = false,
    Callback = function(r0_280)
      -- line: [0, 0] id: 280
      _G.LoopPlayerTP = r0_280
      if r0_280 then
        while _G.LoopPlayerTP do
          local r1_280 = r4_0:FindFirstChild(_G.PlayerToTeleport)
          local r2_280 = nil
          local r3_280 = nil
          if r1_280 and r1_280.Character then
            r2_280 = r1_280.Character
            r3_280 = r2_280:FindFirstChild("HumanoidRootPart")
            if r3_280 then
              teleportplayerfunctionoffset(r3_280.CFrame, r3_280, r2_280, r1_280.Name)
            end
          elseif not r1_280 then
            if PlayerLoopTeleport then
              PlayerLoopTeleport:Set(false)
            end
            _G.LoopPlayerTP = false
          end
          task.wait()
        end
      end
    end,
  }
  PlayerLoopTeleport = PlayerTeleport_Section:[r172_0](r172_0)
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "Lock Camera",
    Default = false,
    Callback = function(r0_137)
      -- line: [0, 0] id: 137
      _G.LockCameraOnPlayer = r0_137
      if r0_137 then
        local r1_137 = nil
        local r2_137 = nil
        local r3_137 = nil
        local r4_137 = nil
        local r5_137 = nil
        r5_137 = r13_0.RenderStepped:Connect(function()
          -- line: [0, 0] id: 138
          r1_137 = r4_0:FindFirstChild(_G.PlayerToTeleport)
          r4_137 = r6_0.CurrentCamera
          if not _G.LockCameraOnPlayer then
            r5_137:Disconnect()
          end
          if r1_137 and r1_137.Character and r4_137 then
            r3_137 = r1_137.Character
            r2_137 = r3_137:FindFirstChild("HumanoidRootPart")
            if r2_137 then
              r4_137.CFrame = CFrame.lookAt(r4_137.CFrame.Position, r2_137.CFrame.Position + Vector3.new(0, 1, 0))
            end
          elseif not r1_137 then
            if PlayerLockCamera then
              PlayerLockCamera:Set(false)
            end
            _G.LockCameraOnPlayer = false
          end
          task.wait()
        end)
        -- close: r1_137
      end
    end,
  }
  PlayerLockCamera = PlayerTeleport_Section:[r172_0](r172_0)
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "View",
    Default = false,
    Callback = function(r0_281)
      -- line: [0, 0] id: 281
      _G.ViewCameraOnPlayer = r0_281
      if r0_281 then
        local r1_281 = r6_0.CurrentCamera
        local r2_281 = r1_281.CameraSubject
        while _G.ViewCameraOnPlayer do
          local r3_281 = r4_0:FindFirstChild(_G.PlayerToTeleport)
          local r4_281 = nil
          local r5_281 = nil
          if r3_281 and r3_281.Character and r1_281 then
            r4_281 = r3_281.Character:FindFirstChildOfClass("Humanoid")
            if r4_281 then
              r1_281.CameraSubject = r4_281
            end
          elseif not r3_281 then
            if PlayerViewCamera then
              PlayerViewCamera:Set(false)
            end
            _G.ViewCameraOnPlayer = false
          end
          wait()
        end
        r1_281.CameraSubject = r2_281
      end
    end,
  }
  PlayerViewCamera = PlayerTeleport_Section:[r172_0](r172_0)
  r172_0 = "AddSlider"
  r172_0 = {
    Name = "Offset",
    Min = 1,
    Max = 20,
    Default = 1,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 1,
    ValueName = "Teleport Offset",
    Callback = function(r0_167)
      -- line: [0, 0] id: 167
      TeleportPlayerOffset = r0_167
    end,
    Save = true,
    Flag = "speed_slider",
  }
  PlayerTeleport_Section:[r172_0](r172_0)
  r170_0 = PlayerTeleport_Section
  r172_0 = "AddDropdown"
  r172_0 = {
    Name = "Behavior",
    Default = "Behind",
    Options = {
      "Behind",
      "Left",
      "Right",
      "Front",
      "Rotate"
    },
    Callback = function(r0_365)
      -- line: [0, 0] id: 365
      _G.PlayerToTeleportDirection = r0_365
    end,
  }
  r170_0:[r172_0](r172_0)
  r172_0 = "AddSection"
  r172_0 = {
    Name = "Walkspeed",
  }
  WS_Section = r86_0:[r172_0](r172_0)
  r172_0 = "AddSection"
  r172_0 = {
    Name = "Infinite Power Jump",
  }
  JP_Section = r86_0:[r172_0](r172_0)
  r172_0 = "AddSection"
  r172_0 = {
    Name = "Noclip",
  }
  NC_Section = r86_0:[r172_0](r172_0)
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "Walkspeed",
    Default = false,
    Callback = function(r0_289)
      -- line: [0, 0] id: 289
      _G.SuperSpeed = r0_289
    end,
    Save = true,
    Flag = "walkspeed_toggle",
  }
  WS_Section:[r172_0](r172_0)
  r172_0 = "AddSlider"
  r172_0 = {
    Name = "Speed",
    Min = 0.1,
    Max = 5,
    Default = 0.1,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 0.01,
    ValueName = "",
    Callback = function(r0_113)
      -- line: [0, 0] id: 113
      Multiplier = r0_113
    end,
    Save = true,
    Flag = "speed_slider",
  }
  WS_Section:[r172_0](r172_0)
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "Infinite Jump",
    Default = false,
    Callback = function(r0_234)
      -- line: [0, 0] id: 234
      _G.InfiniteJump = r0_234
    end,
    Save = true,
    Flag = "infinitejump_toggle",
  }
  JP_Section:[r172_0](r172_0)
  r172_0 = "AddSlider"
  r172_0 = {
    Name = "Jump Power",
    Min = 24,
    Max = 1000,
    Default = 24,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 10,
    ValueName = "",
    Callback = function(r0_224)
      -- line: [0, 0] id: 224
      _G.InfiniteJumpPower = r0_224
      r16_0.Character:FindFirstChildOfClass("Humanoid").JumpPower = r0_224
    end,
    Save = true,
    Flag = "jumppower_slider",
  }
  JP_Section:[r172_0](r172_0)
  r172_0 = "AddToggle"
  r172_0 = {
    Name = "Noclip",
    Default = false,
    Callback = function(r0_102)
      -- line: [0, 0] id: 102
      _G.NoclipToggle = r0_102
      if r0_102 then
        r49_0()
      else
        r50_0()
      end
    end,
    Save = true,
    Flag = "noclip_toggle",
  }
  NC_Section:[r172_0](r172_0)
  r170_0 = {
    [1] = Color3.new(1, 0, 0),
    [2] = Color3.new(1, 0, 0),
    [3] = Color3.new(1, 0, 0),
    [4] = Color3.new(1, 0, 0),
    [5] = Color3.new(1, 0, 0),
    [6] = Color3.new(1, 0, 0),
    [7] = Color3.new(1, 0, 0),
    [8] = Color3.new(1, 0, 0),
    [9] = Color3.new(1, 0, 0),
    [10] = Color3.new(1, 0, 0),
  }
  local r173_0 = "AddSection"
  r173_0 = {
    Name = "Change your entire line color",
  }
  r171_0 = r89_0:[r173_0](r173_0)
  local r174_0 = "AddSection"
  r174_0 = {
    Name = "Line Effects",
  }
  r172_0 = r89_0:[r174_0](r174_0)
  local r175_0 = "AddSection"
  r175_0 = {
    Name = "Stress Server",
  }
  r173_0 = r89_0:[r175_0](r175_0)
  LagServerToggle = nil
  local r176_0 = "AddToggle"
  r176_0 = {
    Name = "Lag Server",
    Default = false,
    Callback = function(r0_180)
      -- line: [0, 0] id: 180
      laggg = r0_180
      while laggg do
        local r1_180 = getgenv().key
        if r1_180 ~= "Xana" then
          LagServerToggle:Set(false)
          r34_0("Only for premium users! Buy premium in my discord server!")
          break
        else
          for r4_180 = 0, Lag_Intensity, 1 do
            for r8_180, r9_180 in ipairs(game:GetService("Players"):GetPlayers()) do
              if r9_180.Character.Torso ~= nil then
                r22_0:FireServer(r9_180.Character.Torso, r9_180.Character.Torso.CFrame)
              end
            end
          end
          wait(1)
        end
      end
    end,
  }
  LagServerToggle = r173_0:[r176_0](r176_0)
  r176_0 = "AddSlider"
  r176_0 = {
    Name = "Lag Intensity",
    Min = 1,
    Max = 400,
    Default = 150,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 1,
    ValueName = "This can have you kicked or kick someone in the server!",
    Save = true,
    Flag = "Lag-Intensity",
    Callback = function(r0_264)
      -- line: [0, 0] id: 264
      Lag_Intensity = r0_264
    end,
  }
  r173_0:[r176_0](r176_0)
  r176_0 = "AddColorpicker"
  r176_0 = {
    Name = "Choose the color",
    Default = Color3.fromRGB(255, 0, 0),
    Callback = function(r0_190)
      -- line: [0, 0] id: 190
      _G.LineColorChangeValue = r0_190
    end,
    Save = true,
    Flag = "changelinecolor_picker",
  }
  r171_0:[r176_0](r176_0)
  r176_0 = "AddButton"
  r176_0 = {
    Name = "Apply Colors",
    Callback = function()
      -- line: [0, 0] id: 90
      for r3_90, r4_90 in pairs(r170_0) do
        if r3_90 == 1 then
          r170_0[r3_90] = ColorSequence.new(_G.LineColorChangeValue, 1)
        else
          r170_0[r3_90] = Color3.new(_G.LineColorChangeValue.R / 255, _G.LineColorChangeValue.G / 255, _G.LineColorChangeValue.B / 255)
        end
      end
      r27_0:FireServer(unpack(r170_0))
    end,
  }
  r171_0:[r176_0](r176_0)
  r176_0 = "AddToggle"
  r176_0 = {
    Name = "Crazy Line (Soft Lag)",
    Default = false,
    Callback = function(r0_372)
      -- line: [0, 0] id: 372
      if r0_372 then
        _G.CrazyLine = r0_372
        while _G.CrazyLine do
          local r1_372 = pairs
          for r4_372, r5_372 in r1_372(r4_0:GetPlayers()) do
            if r5_372 and r5_372 ~= r16_0 and r5_372.Character and r5_372.Character:FindFirstChild("Torso") then
              r22_0:FireServer(r5_372.Character:FindFirstChild("Torso"), CFrame.new(0.12640380859375, 0.9606337547302246, -0.5000009536743164, 0.9985212683677673, 0, -0.05436277016997337, -0.0000000064805472099749295, 1, -0.00000011903301100346653, 0.05436277016997337, 0.00000005960464477539063, 0.9985212683677673))
            end
            task.wait()
          end
        end
      else
        _G.CrazyLine = r0_372
      end
    end,
    Save = true,
    Flag = "softlagline_toggle",
  }
  r172_0:[r176_0](r176_0)
  r176_0 = "AddToggle"
  r176_0 = {
    Name = "Invisible Line",
    Default = false,
    Callback = function(r0_290)
      -- line: [0, 0] id: 290
      if r0_290 then
        _G.InvisibleLine = r0_290
      else
        _G.InvisibleLine = r0_290
      end
    end,
    Save = true,
    Flag = "invisLine_toggle",
  }
  r172_0:[r176_0](r176_0)
  r176_0 = "BindAction"
  r176_0 = "Godmode"
  r12_0:[r176_0](r176_0, GodModeFTry, false, Enum.KeyCode.T)
  r176_0 = "AddParagraph"
  r176_0 = "Note!"
  r172_0:[r176_0](r176_0, "You can\'t see the effects line, but others player can see it. And Invisible Line won\'t work if Crazy Line is Enabled")
  gui2 = Instance.new("ScreenGui")
  gui2.ResetOnSpawn = false
  gui2.Name = "CAG2"
  if r9_0.TouchEnabled then
    gui2.Parent = r16_0.PlayerGui
  end
  imageButtonTeleport = Instance.new("ImageButton")
  imageButtonTeleport.Size = UDim2.new(0, 70, 0, 70)
  imageButtonTeleport.Position = UDim2.new(1, -267, 1, -90)
  imageButtonTeleport.Image = "rbxassetid://97166444"
  imageButtonTeleport.BackgroundTransparency = 1
  imageButtonTeleport.ImageTransparency = 0.2
  imageButtonTeleport.ImageColor3 = Color3.fromRGB(142, 142, 142)
  imageButtonTeleport.Parent = gui2
  imageTLabel = Instance.new("ImageLabel")
  imageTLabel.Size = UDim2.new(1, 0, 1, 0)
  imageTLabel.Image = "rbxassetid://6723742952"
  imageTLabel.BackgroundTransparency = 1
  imageTLabel.Parent = imageButtonTeleport
  imageButtonControl = Instance.new("ImageButton")
  imageButtonControl.Size = UDim2.new(0, 50, 0, 50)
  imageButtonControl.Position = UDim2.new(1, -378, 1, -80)
  imageButtonControl.Image = "rbxassetid://97166444"
  imageButtonControl.BackgroundTransparency = 1
  imageButtonControl.ImageTransparency = 0.2
  imageButtonControl.ImageColor3 = Color3.fromRGB(142, 142, 142)
  imageButtonControl.Parent = gui2
  imageCLabel = Instance.new("ImageLabel")
  imageCLabel.Size = UDim2.new(1, 0, 1, 0)
  imageCLabel.Image = "rbxassetid://14436167187"
  imageCLabel.BackgroundTransparency = 1
  imageCLabel.Parent = imageButtonControl
  imageButtonAnchor = Instance.new("ImageButton")
  imageButtonAnchor.Size = UDim2.new(0, 50, 0, 50)
  imageButtonAnchor.Position = UDim2.new(1, -325, 1, -80)
  imageButtonAnchor.Image = "rbxassetid://97166444"
  imageButtonAnchor.BackgroundTransparency = 1
  imageButtonAnchor.ImageTransparency = 0.2
  imageButtonAnchor.ImageColor3 = Color3.fromRGB(142, 142, 142)
  imageButtonAnchor.Parent = gui2
  imageKLabelDe = Instance.new("ImageLabel")
  imageKLabelDe.Size = UDim2.new(1, 0, 1, 0)
  imageKLabelDe.Image = "rbxassetid://3040311268"
  imageKLabelDe.BackgroundTransparency = 1
  imageKLabelDe.Parent = imageButtonAnchor
  imageButtonAnchor.InputBegan:Connect(function(r0_379, r1_379)
    -- line: [0, 0] id: 379
    if not r1_379 and r112_0.TouchEnabled and r0_379.UserInputType == Enum.UserInputType.Touch then
      anchorfunc()
    end
  end)
  imageButtonTeleport.InputBegan:Connect(function(r0_95, r1_95)
    -- line: [0, 0] id: 95
    if not r1_95 and r112_0.TouchEnabled and r0_95.UserInputType == Enum.UserInputType.Touch then
      teleportfunc()
    end
  end)
  imageButtonControl.InputBegan:Connect(function(r0_203, r1_203)
    -- line: [0, 0] id: 203
    if not r1_203 and r112_0.TouchEnabled and r0_203.UserInputType == Enum.UserInputType.Touch then
      controlBind("Control(C)", Enum.UserInputState.Begin)
    end
  end)
  r176_0 = "AddSection"
  r176_0 = {
    Name = "Teleport",
  }
  r174_0 = r91_0:[r176_0](r176_0)
  local r177_0 = "AddSection"
  r177_0 = {
    Name = "Spawn Toy",
  }
  r175_0 = r91_0:[r177_0](r177_0)
  local r178_0 = "AddSection"
  r178_0 = {
    Name = "Anchor",
  }
  r176_0 = r91_0:[r178_0](r178_0)
  local r179_0 = "AddSection"
  r179_0 = {
    Name = "Control Player/NPC",
  }
  r177_0 = r91_0:[r179_0](r179_0)
  local r180_0 = "AddToggle"
  r180_0 = {
    Name = "Anchor (K)",
    Default = false,
    Callback = function(r0_276)
      -- line: [0, 0] id: 276
      imageButtonAnchor.Visible = r0_276
      imageButtonAnchor.Active = r0_276
      if r0_276 then
        r12_0:BindAction("AnchorK", anchorobject, false, Enum.KeyCode.K)
      else
        r12_0:UnbindAction("AnchorK")
      end
    end,
    Save = true,
    Flag = "anchorbind_toggle",
  }
  r176_0:[r180_0](r180_0)
  r180_0 = "AddButton"
  r180_0 = {
    Name = "Compile Parts",
    Callback = function()
      -- line: [0, 0] id: 42
      r55_0()
    end,
  }
  r176_0:[r180_0](r180_0)
  r180_0 = "AddToggle"
  r180_0 = {
    Name = "Teleport (Z)",
    Default = false,
    Callback = function(r0_335)
      -- line: [0, 0] id: 335
      imageButtonTeleport.Visible = r0_335
      imageButtonTeleport.Active = r0_335
      if r0_335 then
        r12_0:BindAction("Teleport(Z)", r45_0, false, Enum.KeyCode.Z)
      else
        r12_0:UnbindAction("Teleport(Z)")
      end
    end,
    Save = true,
    Flag = "teleportbind_toggle",
  }
  r174_0:[r180_0](r180_0)
  r180_0 = "AddToggle"
  r180_0 = {
    Name = "Control (C)",
    Default = false,
    Callback = function(r0_244)
      -- line: [0, 0] id: 244
      imageButtonControl.Visible = r0_244
      imageButtonControl.Active = r0_244
      if r0_244 then
        r12_0:BindAction("Control(C)", controlBind, false, Enum.KeyCode.C)
      else
        r12_0:UnbindAction("Control(C)")
      end
    end,
    Save = true,
    Flag = "controlbind_toggle",
  }
  r177_0:[r180_0](r180_0)
  r180_0 = "AddDropdown"
  r180_0 = {
    Name = "Select Toy",
    Default = "Pallet",
    Options = {
      "Pallet",
      "BombMissile"
    },
    Callback = function(r0_354)
      -- line: [0, 0] id: 354
      if r0_354 == "Pallet" then
        _G.SelectedToy = "PalletLightBrown"
      else
        _G.SelectedToy = r0_354
      end
    end,
    Save = true,
    Flag = "selecttoy_dropdown",
  }
  r175_0:[r180_0](r180_0)
  r180_0 = "AddToggle"
  r180_0 = {
    Name = "Spawn Toy (TAB)",
    Default = false,
    Callback = function(r0_266)
      -- line: [0, 0] id: 266
      if r0_266 then
        r12_0:BindAction("Spawn Toy (TAB)", r44_0, false, Enum.KeyCode.Tab)
        r12_0:SetImage("Spawn Toy (TAB)", "rbxassetid://6723742952")
        r12_0:SetPosition("Spawn Toy (TAB)", UDim2.new(1, -367, 1, -90))
        local r1_266 = r12_0:GetButton("Spawn Toy (TAB)")
        if r1_266 then
          r1_266.Size = UDim2.new(0, 70, 0, 70)
        end
      else
        r12_0:UnbindAction("Spawn Toy (TAB)")
      end
    end,
    Save = true,
    Flag = "spawntoy_toggle",
  }
  r175_0:[r180_0](r180_0)
  r180_0 = "AddSection"
  r180_0 = {
    Name = "Whitelist",
  }
  r178_0 = r96_0:[r180_0](r180_0)
  local r181_0 = "AddDropdown"
  r181_0 = {
    Name = "Select Player",
    Default = "",
    Options = {
      ""
    },
    Callback = function(r0_165)
      -- line: [0, 0] id: 165
      if r0_165 then
        _G.PlayerToAddWhitelist = string.split(r0_165, " ")[1]
      end
    end,
  }
  r179_0 = r178_0:[r181_0](r181_0)
  r180_0 = nil
  local r183_0 = "AddButton"
  r183_0 = {
    Name = "Add",
    Callback = function()
      -- line: [0, 0] id: 4
      if not r46_0(_G.PlayerToAddWhitelist) then
        table.insert(r41_0, _G.PlayerToAddWhitelist)
        r42_0(r180_0, r41_0)
      end
    end,
  }
  r178_0:[r183_0](r183_0)
  r183_0 = "AddDropdown"
  r183_0 = {
    Name = "Players in Whitelist",
    Default = "",
    Options = {
      ""
    },
    Callback = function(r0_126)
      -- line: [0, 0] id: 126
      _G.PlayerToRemoveWhitelist = r0_126
    end,
  }
  r180_0 = r178_0:[r183_0](r183_0)
  r183_0 = "AddButton"
  r183_0 = {
    Name = "Remove",
    Callback = function()
      -- line: [0, 0] id: 144
      for r3_144, r4_144 in pairs(r41_0) do
        if r4_144 == _G.PlayerToRemoveWhitelist then
          r41_0[r3_144] = nil
        end
      end
      r42_0(r180_0, r41_0)
    end,
  }
  r178_0:[r183_0](r183_0)
  r183_0 = "AddSection"
  r183_0 = {
    Name = "Blobman Loopkick",
  }
  r181_0 = r96_0:[r183_0](r183_0)
  local r184_0 = "AddSection"
  r184_0 = {
    Name = "Perspective",
  }
  local r182_0 = r96_0:[r184_0](r184_0)
  local r185_0 = "AddSection"
  r185_0 = {
    Name = "Anchor Objects",
  }
  r183_0 = r96_0:[r185_0](r185_0)
  local r186_0 = "AddToggle"
  r186_0 = {
    Name = "Auto Ownership",
    Default = false,
    Callback = function(r0_300)
      -- line: [0, 0] id: 300
      _G.AutoOwnershipAnchor = r0_300
      if r0_300 then
        while _G.AutoOwnershipAnchor do
          autosetownership()
          task.wait(0.2)
        end
      end
    end,
    Save = true,
    Flag = "autoownershipanchorconfig_toggle",
  }
  r183_0:[r186_0](r186_0)
  r186_0 = "AddToggle"
  r186_0 = {
    Name = "Heavy Blobman",
    Default = false,
    Callback = function(r0_35)
      -- line: [0, 0] id: 35
      _G.RockBlobman = r0_35
    end,
    Save = true,
    Flag = "heavyblobmanconfig_toggle",
  }
  r181_0:[r186_0](r186_0)
  _G.PerspectiveEffectsAllow = true
  r186_0 = "AddToggle"
  r186_0 = {
    Name = "Teleport to Camera Position",
    Default = true,
    Callback = function(r0_328)
      -- line: [0, 0] id: 328
      _G.PerspectiveTeleportToCameraPos = r0_328
    end,
    Save = true,
    Flag = "perspectiveconfig1_toggle",
  }
  r182_0:[r186_0](r186_0)
  r186_0 = "AddDropdown"
  r186_0 = {
    Name = "Camera Effect",
    Default = "Default",
    Options = {
      "Default",
      "Old TV"
    },
    Callback = function(r0_228)
      -- line: [0, 0] id: 228
      if r0_228 == "Default" then
        ImageLabel.BorderColor3 = Color3.fromRGB(0, 0, 0)
        ImageLabel.BorderSizePixel = 0
        ImageLabel.Size = UDim2.new(1, 0, 1, 0)
        ImageLabel.Image = "rbxassetid://5945121255"
        ImageLabel.ImageColor3 = Color3.new(0, 0, 0)
        imagestransparencyeffect = 0.45
        saturationvalue = -0.6
        perspectiveON_effect1 = r8_0:Create(ImageLabel, t1p, {
          ImageTransparency = imagestransparencyeffect,
        })
        perspectiveON_effect2 = r8_0:Create(PerspectiveSaturation, t1p, {
          Saturation = saturationvalue,
        })
      elseif r0_228 == "Old TV" then
        ImageLabel.BorderColor3 = Color3.fromRGB(0, 0, 0)
        ImageLabel.BorderSizePixel = 0
        ImageLabel.Size = UDim2.new(1, 0, 1, 0)
        ImageLabel.Image = "rbxassetid://8586979842"
        ImageLabel.ImageColor3 = Color3.fromRGB(255, 255, 255)
        imagestransparencyeffect = 0.7
        saturationvalue = -0.3
        perspectiveON_effect1 = r8_0:Create(ImageLabel, t1p, {
          ImageTransparency = imagestransparencyeffect,
        })
        perspectiveON_effect2 = r8_0:Create(PerspectiveSaturation, t1p, {
          Saturation = saturationvalue,
        })
      end
    end,
    Save = true,
    Flag = "perspectivevisualeffect_dropdown",
  }
  r182_0:[r186_0](r186_0)
  r186_0 = "AddSection"
  r186_0 = {
    Name = "Loop Players",
  }
  r184_0 = r92_0:[r186_0](r186_0)
  local r187_0 = "AddSection"
  r187_0 = {
    Name = "Players in Loop",
  }
  r185_0 = r92_0:[r187_0](r187_0)
  local r188_0 = "AddSection"
  r188_0 = {
    Name = "Loop Kill Functions",
  }
  r186_0 = r92_0:[r188_0](r188_0)
  local r189_0 = "AddSection"
  r189_0 = {
    Name = "Loop Kick (Blobman)",
  }
  r187_0 = r92_0:[r189_0](r189_0)
  local r190_0 = "AddDropdown"
  r190_0 = {
    Name = "Select Player",
    Default = "",
    Options = {
      ""
    },
    Callback = function(r0_62)
      -- line: [0, 0] id: 62
      if r0_62 then
        _G.PlayerToAdd = string.split(r0_62, " ")[1]
      end
    end,
  }
  r188_0 = r184_0:[r190_0](r190_0)
  r189_0 = nil
  r190_0 = nil
  if getgenv().key == "Xana" then
    r190_0 = 999999
  else
    r190_0 = 3
  end
  local r193_0 = "AddButton"
  r193_0 = {
    Name = "Add",
    Callback = function()
      -- line: [0, 0] id: 237
      if not table.find(r40_0, _G.PlayerToAdd) then
        if r190_0 > #r40_0 then
          table.insert(r40_0, _G.PlayerToAdd)
          r42_0(r189_0, r40_0)
        else
          r34_0("You reached the max ammount of players in loop, buy premium to unlock more space!")
        end
      end
    end,
  }
  r184_0:[r193_0](r193_0)
  r193_0 = "AddDropdown"
  r193_0 = {
    Name = "Players in Loop",
    Default = "",
    Options = {
      ""
    },
    Callback = function(r0_286)
      -- line: [0, 0] id: 286
      _G.PlayerToRemove = r0_286
    end,
  }
  r189_0 = r185_0:[r193_0](r193_0)
  r193_0 = "AddButton"
  r193_0 = {
    Name = "Remove",
    Callback = function()
      -- line: [0, 0] id: 205
      for r3_205, r4_205 in pairs(r40_0) do
        if r4_205 == _G.PlayerToRemove then
          r40_0[r3_205] = nil
        end
      end
      r42_0(r189_0, r40_0)
    end,
  }
  r185_0:[r193_0](r193_0)
  local r191_0 = 0
  local function r192_0()
    -- line: [0, 0] id: 182
    if typeof(_G.LastBlobmanWasSeat) == "Instance" and _G.LastBlobmanWasSeat.Parent then
      local r0_182 = GetPlayerCharacter()
      local r2_182 = _G.LastBlobmanWasSeat:FindFirstChild("VehicleSeat")
      local r3_182 = nil
      local r4_182 = nil
      if r2_182 then
        r3_182 = r2_182:FindFirstChild("ProximityPrompt")
        r4_182 = r2_182:FindFirstChildOfClass("Weld")
      end
      if r16_0:DistanceFromCharacter(r2_182.Position) >= 150 then
        DeleteToyRE:FireServer(_G.LastBlobmanWasSeat)
        return 
      end
      if r0_182 and r4_182 and r4_182.Part1 and not r4_182.Part1:IsDescendantOf(r0_182) then
        SNOWshipPlayer(r4_0:GetPlayerFromCharacter(r4_182.Part1.Parent))
      end
      if r3_182 and r2_182 then
        fireproximityprompt(r3_182)
        TeleportPlayer(r2_182.CFrame + Vector3.new(0, 3.5, 0))
      end
    else
      _G.LastBlobmanWasSeat = r82_0()
    end
  end
  function CountRealNumberPlayersInLoop()
    -- line: [0, 0] id: 132
    local r0_132 = 0
    for r4_132, r5_132 in pairs(r40_0) do
      if r4_0:FindFirstChild(r5_132) then
        r0_132 = r0_132 + 1
      end
    end
    return r0_132
  end
  function IsThereAnyPlayersInLoopAlive()
    -- line: [0, 0] id: 39
    local r0_39 = false
    for r4_39, r5_39 in pairs(r40_0) do
      if r4_0:FindFirstChild(r5_39) and r5_39.Character and r5_39.Character:FindFirstChildOfClass("Humanoid") and 0 < r5_39.Character.Humanoid.Health then
        r0_39 = true
      end
    end
    return r0_39
  end
  function ResetCharacterStats()
    -- line: [0, 0] id: 129
    for r3_129, r4_129 in pairs(r40_0) do
      local r5_129 = r4_0:FindFirstChild(r4_129)
      if r5_129 and r5_129.Character and r5_129.Character:FindFirstChild("HumanoidRootPart") then
        local r6_129 = r5_129.Character.HumanoidRootPart
        r5_129.Character:SetAttribute("Kick", 0)
        r5_129.Character:SetAttribute("Kicking", nil)
        r5_129.Character:SetAttribute("Kicking2", nil)
        if r6_129:FindFirstChild("KickAuraVelocity") then
          r6_129.KickAuraVelocity:Destroy()
        end
      end
    end
  end
  function verifyPlayerinBlobmanHand()
    -- line: [0, 0] id: 155
    local r1_155 = r16_0.Character:FindFirstChildOfClass("Humanoid")
    if r149_0() then
      local r4_155 = r1_155.SeatPart.Parent:WaitForChild("LeftDetector"):WaitForChild("LeftWeld").Attachment0
      local r5_155 = nil
      if r4_155 and r4_155.Parent then
        r5_155 = r4_0:GetPlayerFromCharacter(r4_155.Parent.Parent)
        if r5_155 then
          return r5_155
        end
      end
    end
  end
  r193_0 = nil
  local r196_0 = "AddToggle"
  r196_0 = {
    Name = "Loop Kill",
    Default = false,
    Callback = function(r0_344)
      -- line: [0, 0] id: 344
      _G.LoopKill = r0_344
      if r0_344 then
        while _G.LoopKill do
          r193_0 = GetPlayerCFrame()
          local r1_344 = pairs
          for r4_344, r5_344 in r1_344(r40_0) do
            r5_344 = r4_0:FindFirstChild(r5_344)
            if r152_0(r5_344) then
              local r6_344 = r5_344
              local r7_344 = r6_344.Character:FindFirstChild("HumanoidRootPart")
              local r8_344 = nil
              local r9_344 = r6_344.Character:FindFirstChild("Head")
              local r10_344 = r6_344.Character:FindFirstChild("Humanoid")
              if r6_344 and r7_344 and r9_344 then
                for r14_344 = 0, 50, 1 do
                  r49_0()
                  SNOWship(r7_344)
                  if not r152_0(r6_344) or not _G.LoopKill or CheckNetworkOwnerShipOnPlayer(r6_344) or 500 < r7_344.AssemblyLinearVelocity.Magnitude then
                    r23_0:FireServer(r7_344)
                    CreateSkyVelocity(r7_344)
                    break
                  else
                    task.wait()
                    if r7_344.Position.Y > -12 then
                      TeleportPlayer(CFrame.new(r7_344.Position + Vector3.new(0, -10, -10)))
                    else
                      TeleportPlayer(CFrame.new(r7_344.Position + Vector3.new(0, 5, -15)))
                    end
                    r10_344.BreakJointsOnDeath = false
                    r10_344:ChangeState(Enum.HumanoidStateType.Dead)
                    r10_344.Jump = true
                    r10_344.Sit = false
                  end
                end
              end
            end
          end
          TeleportPlayer(r193_0)
          task.wait(0.2)
        end
        r50_0()
        TeleportPlayer(r193_0)
        print("End LoopKill")
      end
    end,
    Save = true,
    Flag = "lk_toggle",
  }
  r186_0:[r196_0](r196_0)
  r196_0 = "AddSection"
  r196_0 = {
    Name = "Loop Kick (Ownership)",
  }
  local r194_0 = r92_0:[r196_0](r196_0)
  local r197_0 = "AddToggle"
  r197_0 = {
    Name = "Loop Kick",
    Default = false,
    Callback = function(r0_46)
      -- line: [0, 0] id: 46
      _G.LoopKickOwnership = r0_46
      if r0_46 then
        while _G.LoopKickOwnership do
          local r1_46 = getgenv().key
          if r1_46 ~= "Xana" then
            _G.LoopKickOwnership = false
            r34_0("Only for premium users! Buy premium in my discord server!")
            loopkickownertoggle:Set(false)
            break
          else
            r193_0 = GetPlayerCFrame()
            r1_46 = pairs
            for r4_46, r5_46 in r1_46(r40_0) do
              r5_46 = r4_0:FindFirstChild(r5_46)
              if r152_0(r5_46) then
                local r6_46 = r5_46
                local r7_46 = r6_46.Character:FindFirstChild("HumanoidRootPart")
                local r8_46 = r6_46.Character:FindFirstChild("Head")
                local r9_46 = r6_46.Character:FindFirstChild("Humanoid")
                if r6_46 and r7_46 and r8_46 then
                  for r13_46 = 0, 50, 1 do
                    r49_0()
                    SNOWship(r7_46)
                    if not r152_0(r6_46) or not _G.LoopKickOwnership or CheckNetworkOwnerShipOnPlayer(r6_46) or 500 < r7_46.AssemblyLinearVelocity.Magnitude then
                      r23_0:FireServer(r7_46)
                      wait()
                      CreateSkyVelocity(r7_46)
                      break
                    else
                      task.wait()
                      if r7_46.Position.Y > -12 then
                        TeleportPlayer(CFrame.new(r7_46.Position + Vector3.new(0, -10, -10)))
                      else
                        TeleportPlayer(CFrame.new(r7_46.Position + Vector3.new(0, 5, -15)))
                      end
                    end
                  end
                end
              end
            end
            TeleportPlayer(r193_0)
            task.wait(0.2)
          end
        end
        r50_0()
        TeleportPlayer(r193_0)
      end
    end,
    Save = true,
    Flag = "lkickowner_toggle",
  }
  loopkickownertoggle = r194_0:[r197_0](r197_0)
  r197_0 = "AddDropdown"
  r197_0 = {
    Name = "Kick Type",
    Default = "Go to the heaven!",
    Options = {
      "Go to the heaven!"
    },
    Callback = function(r0_149)
      -- line: [0, 0] id: 149
      _G.LoopKickOwnerType = r0_149
    end,
    Save = true,
    Flag = "loopkickownershiptype_dropdown",
  }
  r194_0:[r197_0](r197_0)
  r197_0 = "AddToggle"
  r197_0 = {
    Name = "Loop Ragdoll",
    Default = false,
    Callback = function(r0_171)
      -- line: [0, 0] id: 171
      _G.LoopRagdoll = r0_171
      if r0_171 then
        while _G.LoopRagdoll do
          local r1_171 = getgenv().key
          if r1_171 ~= "Xana" then
            loopRagdoll:Set(false)
            _G.LoopRagdoll = false
            r34_0("Only for premium users! Buy premium in my discord server!")
            break
          else
            r1_171 = pairs
            for r4_171, r5_171 in r1_171(r40_0) do
              r5_171 = r4_0:FindFirstChild(r5_171)
              if r155_0(r5_171) then
                local r6_171 = r5_171
                local r8_171 = r6_171.Character:FindFirstChild("HumanoidRootPart")
                local r9_171 = r6_171.Character:FindFirstChildOfClass("Humanoid"):FindFirstChild("Ragdolled")
                if r8_171 and r9_171 and not r9_171.Value then
                  r74_0(r8_171)
                  task.wait(0.015)
                end
              end
            end
            task.wait()
          end
        end
      end
    end,
  }
  loopRagdoll = r186_0:[r197_0](r197_0)
  r197_0 = "AddToggle"
  r197_0 = {
    Name = "Loop Fire",
    Default = false,
    Callback = function(r0_109)
      -- line: [0, 0] id: 109
      _G.LoopFire = r0_109
      if r0_109 then
        while _G.LoopFire do
          local r1_109 = getgenv().key
          if r1_109 ~= "Xana" then
            loopFire:Set(false)
            _G.LoopFire = false
            r34_0("Only for premium users! Buy premium in my discord server!")
            break
          else
            r1_109 = pairs
            for r4_109, r5_109 in r1_109(r40_0) do
              r5_109 = r4_0:FindFirstChild(r5_109)
              if r155_0(r5_109) then
                local r6_109 = r5_109
                local r7_109 = r6_109.Character
                local r8_109 = r6_109.Character:FindFirstChild("HumanoidRootPart")
                local r9_109 = nil
                if r8_109:FindFirstChild("FirePlayerPart") and r8_109.FirePlayerPart:FindFirstChild("CanBurn") then
                  r9_109 = r8_109.FirePlayerPart.CanBurn.Value
                end
                if r8_109 and r6_109 and not IsPlayerInsideSafeZone(r6_109) and not r9_109 then
                  r79_0(r8_109)
                  task.wait(0.015)
                end
              end
            end
            task.wait()
          end
        end
      end
    end,
  }
  
  loopFire = r186_0:[r197_0](r197_0)
  local function r195_0(r0_270, r1_270)
    -- line: [0, 0] id: 270
    local r3_270 = r16_0.Character:FindFirstChildOfClass("Humanoid")
    local r4_270 = nil
    local r5_270 = nil
    if r149_0() then
      r4_270 = r3_270.SeatPart.Parent
      local r6_270 = nil
      local r7_270 = nil
      local r8_270 = nil
      local r9_270 = nil
      local r10_270 = r4_0:FindFirstChild(r0_270)
      if r10_270 and r10_270.Character and r10_270.Character:FindFirstChild("HumanoidRootPart") and r4_270 and not r38_0(r10_270) then
        r8_270 = {
          r4_270.LeftDetector,
          r10_270.Character.HumanoidRootPart,
          r4_270.LeftDetector.LeftWeld
        }
        r9_270 = {
          r4_270.LeftDetector.LeftWeld,
          r10_270.Character.HumanoidRootPart
        }
        CreatureGrab = r4_270.BlobmanSeatAndOwnerScript.CreatureGrab
        r7_270 = r4_270.BlobmanSeatAndOwnerScript.CreatureDrop
        if r4_270 then
          if r1_270 == 1 then
            if r4_270.Parent == r19_0 then
              task.wait(0.2)
              DeleteToyRE:FireServer(r4_270)
            else
              r3_0:MakeNotification({
                Name = "You need to be seated on Blobman",
                Content = "The Blobman needs to be your own toy",
                Image = "rbxassetid://4483345998",
                Time = 5,
              })
            end
          elseif r1_270 == 2 then
            CreatureGrab:FireServer(unpack(r8_270))
            task.wait(0.155)
            r3_270.Sit = false
          elseif r1_270 == 3 and not r10_270.Character:GetAttribute("Kicking") and not r10_270.Character:GetAttribute("Kicking2") then
            local r11_270 = r4_0:FindFirstChild(r0_270)
            local r12_270 = r11_270.Character
            local r13_270 = r12_270.HumanoidRootPart
            local r14_270 = r12_270.Head
            local r15_270 = r12_270:FindFirstChildOfClass("Humanoid")
            local r16_270 = nil
            local r17_270 = nil
            local r18_270 = nil
            r12_270:SetAttribute("Kicking", true)
            if r13_270:FindFirstChild("FlingAuraVelocity") then
              r13_270.FlingAuraVelocity:Destroy()
            end
            print("Kick")
            for r22_270 = 0, 50, 1 do
              if r149_0() then
                if CheckNetworkOwnerShipOnPlayer(r11_270) then
                  break
                elseif verifyPlayerinBlobmanHand() == r11_270 then
                  r7_270:FireServer(unpack(r9_270))
                  break
                else
                  CreatureGrab:FireServer(unpack(r8_270))
                  task.wait()
                end
              else
                break
              end
            end
            print("End Loop Here!")
            for r22_270 = 0, 25, 1 do
              if SNOWshipPlayer(r11_270) then
                if not r13_270:FindFirstChild("KickAuraVelocity") then
                  r17_270 = Instance.new("BodyVelocity", r13_270)
                  r17_270.Name = "KickAuraVelocity"
                  r17_270.MaxForce = Vector3.new(0, 12500, 0)
                  r17_270.Velocity = Vector3.new(0, 100, 0)
                end
                local r23_270 = 0
                while r149_0() and 100 > r23_270 do
                  local r24_270 = r15_270.FloorMaterial
                  if r24_270 == Enum.Material.Air then
                    r24_270 = r16_0:DistanceFromCharacter(r13_270.Position)
                    if r24_270 > 100 then
                      r12_270:SetAttribute("Kicking2", true)
                      r23_0:FireServer(r13_270)
                      CreatureGrab:FireServer(unpack(r8_270))
                      print("Destroyed!")
                      break
                    end
                  else
                    SNOWshipPlayer(r11_270)
                    r23_270 = r23_270 + 1
                    task.wait()
                  end
                end
              elseif not r149_0() then
                break
              else
                task.wait()
              end
            end
            if r17_270 then
              r17_270:Destroy()
            end
            r12_270:SetAttribute("Kicking", nil)
          elseif not r1_270 then
            CreatureGrab:FireServer(unpack(r8_270))
          end
        end
      end
    else
      r3_0:MakeNotification({
        Name = "You need to be seated on Blobman",
        Content = "Please, sit on any Blobman",
        Image = "rbxassetid://4483345998",
        Time = 5,
      })
    end
  end
  local r198_0 = "AddToggle"
  r198_0 = {
    Name = "Loop Kick (Blobman)",
    Default = false,
    Callback = function(r0_262)
      -- line: [0, 0] id: 262
      if r0_262 then
        _G.LoopKick = r0_262
        local r1_262 = nil
        local r2_262 = nil
        while _G.LoopKick do
          local r3_262 = pairs
          for r6_262, r7_262 in r3_262(r40_0) do
            if r4_0:FindFirstChild(r7_262) then
              if r149_0() then
                r195_0(r7_262, 3)
              else
                r192_0()
              end
            end
          end
          task.wait()
        end
      else
        _G.LoopKick = r0_262
      end
    end,
    Save = true,
    Flag = "lkick_toggle",
  }
  r187_0:[r198_0](r198_0)
  function blobmangraball()
    -- line: [0, 0] id: 136
    for r3_136, r4_136 in pairs(r4_0:GetPlayers()) do
      if not r38_0(r4_136) and r4_136 ~= r16_0 and r4_136.Character and r4_136.Character:FindFirstChild("HumanoidRootPart") and (not r46_0(r4_136.Name) or not _G.WhitelistFriends2) then
        local r5_136 = nil
        if r16_0.Character and r16_0.Character:FindFirstChildOfClass("Humanoid") then
          r5_136 = r16_0.Character:FindFirstChildOfClass("Humanoid").SeatPart.Parent
          r5_136:WaitForChild("BlobmanSeatAndOwnerScript"):WaitForChild("CreatureGrab"):FireServer(unpack({
            [1] = r5_136:WaitForChild("LeftDetector"),
            [2] = r4_136.Character:FindFirstChild("HumanoidRootPart"),
            [3] = r5_136:WaitForChild("LeftDetector"):WaitForChild("LeftWeld"),
          }))
        end
      end
      task.wait()
    end
  end
  r196_0 = LongReachGrab_Player
  r198_0 = "AddDropdown"
  r198_0 = {
    Name = "Select Player",
    Default = "",
    Options = {
      ""
    },
    Callback = function(r0_179)
      -- line: [0, 0] id: 179
      _G.PlayerToLongGrab = string.split(r0_179, " ")[1]
    end,
  }
  PlayerToSelect = r196_0:[r198_0](r198_0)
  r198_0 = "AddButton"
  r198_0 = {
    Name = "Lock",
    Callback = function()
      -- line: [0, 0] id: 273
      r195_0(_G.PlayerToLongGrab, 2)
    end,
  }
  LongReachGrab_Player:[r198_0](r198_0)
  r198_0 = "AddButton"
  r198_0 = {
    Name = "Bring",
    Callback = function()
      -- line: [0, 0] id: 294
      r195_0(_G.PlayerToLongGrab)
    end,
  }
  LongReachGrab_Player:[r198_0](r198_0)
  r198_0 = "AddButton"
  r198_0 = {
    Name = "Kick",
    Callback = function()
      -- line: [0, 0] id: 306
      r195_0(_G.PlayerToLongGrab, 3)
    end,
  }
  LongReachGrab_Player:[r198_0](r198_0)
  r198_0 = "AddSection"
  r198_0 = {
    Name = "Destroy Everything",
  }
  r196_0 = LongReachGrab_Player:[r198_0](r198_0)
  r197_0 = nil
  local r200_0 = "AddToggle"
  r200_0 = {
    Name = "Destroy Server",
    Default = false,
    Callback = function(r0_219)
      -- line: [0, 0] id: 219
      if r0_219 then
        _G.BringAllLongReach = true
        if getgenv().key ~= "Xana" and r20_0.Value then
          r197_0:Set(false)
          r34_0("You can\'t use destroy server inside a house!, buy premium to be able to do that!")
          return 
        end
        if r149_0() then
          while _G.BringAllLongReach do
            local r1_219 = r149_0()
            if r1_219 then
              blobmangraball()
            else
              task.wait(1)
            end
          end
        else
          r197_0:Set(false)
          r3_0:MakeNotification({
            Name = "You need to be seated on Blobman",
            Content = "Please, sit on any Blobman",
            Image = "rbxassetid://4483345998",
            Time = 5,
          })
        end
      else
        _G.BringAllLongReach = false
      end
    end,
    Save = true,
    Flag = "BringAllLongReach_toggle",
  }
  r197_0 = r196_0:[r200_0](r200_0)
  r200_0 = "AddToggle"
  r200_0 = {
    Name = "Whitelist Friends",
    Default = false,
    Callback = function(r0_34)
      -- line: [0, 0] id: 34
      _G.WhitelistFriends2 = r0_34
    end,
    Save = true,
    Flag = "Whitelistfreinds2_toggle",
  }
  r197_0 = r196_0:[r200_0](r200_0)
  apagarfogo = r6_0.Map.Hole.PoisonBigHole.ExtinguishPart
  apagarfogo.Size = Vector3.new(0.5, 0.5, 0.5)
  apagarfogo.Transparency = 1
  apagarfogo.Tex.Transparency = 1
  r6_0.ChildAdded:Connect(function(r0_48)
    -- line: [0, 0] id: 48
    if r0_48.Name == "GrabParts" then
      local r1_48 = r0_48.GrabPart.WeldConstraint.Part1
      local r2_48 = nil
      local r3_48 = nil
      if r1_48 then
        if r38_0(r1_48.Parent) then
          return 
        end
        if _G.InvisibleLine then
          r22_0:FireServer()
        end
        if _G.SuperStrength then
          r2_48 = Instance.new("BodyVelocity", r1_48)
          r2_48.MaxForce = Vector3.new(0, 0, 0)
          r2_48.Velocity = Vector3.new()
          r2_48.Name = "SuperStrength"
        end
        if _G.MasslessGrab then
          task.spawn(function()
            -- line: [0, 0] id: 50
            local r0_50 = r0_48.DragPart.AlignOrientation
            local r1_50 = r0_48.DragPart.AlignPosition
            while _G.MasslessGrab do
              r0_50.MaxTorque = 10000000000000000000000000000000000000000000000
              r0_50.Responsiveness = 20099
              r1_50.MaxForce = 1000000000000000000000000000000000000000000000000000
              r1_50.Responsiveness = 20099
              task.wait(0.245)
            end
            r0_50.MaxTorque = 600000
            r0_50.Responsiveness = 30
            r1_50.MaxForce = 60000
            r1_50.Responsiveness = 40
          end)
        end
        if _G.NoclipGrab and not r1_48.Anchored then
          task.spawn(function()
            -- line: [0, 0] id: 54
            if r1_48.Parent and r1_48.Parent:IsA("Model") then
              local r0_54 = {}
              local r1_54 = r1_48.Parent:GetDescendants()
              local r2_54 = r1_48.Parent:FindFirstChildOfClass("Humanoid")
              for r6_54, r7_54 in pairs(r1_54) do
                if r7_54:IsA("BasePart") or r7_54:IsA("Part") or r7_54:IsA("MeshPart") then
                  r0_54[r7_54] = r7_54.CanCollide
                end
              end
              while r0_48.Parent do
                local r3_54 = pairs
                local r4_54 = r1_54
                for r6_54, r7_54 in r3_54(r4_54) do
                  if r7_54:IsA("BasePart") or r7_54:IsA("Part") or r7_54:IsA("MeshPart") then
                    r7_54.CanCollide = false
                  end
                end
                wait(0.214)
              end
              if r2_54 then
                task.wait(0.5)
              end
              for r6_54, r7_54 in pairs(r1_54) do
                if r7_54:IsA("BasePart") or r7_54:IsA("Part") or r7_54:IsA("MeshPart") then
                  r7_54.CanCollide = r0_54[r7_54]
                end
              end
            end
          end)
        end
        if _G.PerspectiveGrab and not r1_48.Anchored then
          task.spawn(function()
            -- line: [0, 0] id: 58
            local r0_58 = GetPlayerCharacter()
            local r1_58 = nil
            local r2_58 = nil
            local r3_58 = nil
            r22_0:FireServer()
            if r0_58 then
              r1_58 = r0_58:FindFirstChildOfClass("Humanoid")
              r2_58 = r0_58:FindFirstChild("HumanoidRootPart")
            end
            local r4_58 = Instance.new("Part", r6_0)
            r4_58.Anchored = true
            r4_58.CanCollide = false
            r4_58.Transparency = 1
            r4_58.CanQuery = false
            r4_58.Size = Vector3.new()
            r4_58.CFrame = workspace.CurrentCamera.CFrame
            workspace.CurrentCamera.CameraType = Enum.CameraType.Follow
            workspace.CurrentCamera.CameraSubject = r4_58
            if r162_0 then
              r162_0:Disconnect()
            end
            if r1_58 and r2_58 then
              r3_58 = GetPlayerCFrame()
              r108_0(true)
              local r5_58 = nil
              local r6_58 = nil
              local r7_58 = nil
              local r8_58 = nil
              local r9_58 = nil
              local r10_58 = nil
              local r11_58 = nil
              r162_0 = r13_0.Heartbeat:Connect(function(r0_59)
                -- line: [0, 0] id: 59
                r5_58 = r1_58.MoveDirection * r163_0 * r0_59
                r6_58 = r4_58.CFrame
                r7_58 = workspace.CurrentCamera.CFrame
                r8_58 = r6_58:ToObjectSpace(r7_58).Position
                r7_58 = r7_58 * CFrame.new(-r8_58.X, -r8_58.Y, (-r8_58.Z + 1))
                r9_58 = r7_58.Position
                r10_58 = r6_58.Position
                r11_58 = CFrame.new(r9_58, Vector3.new(r10_58.X, r9_58.Y, r10_58.Z)):VectorToObjectSpace(r5_58)
                r4_58.CFrame = CFrame.new(r10_58) * (r7_58 - r9_58) * CFrame.new(r11_58)
                r2_58.CFrame = CFrame.new(527, 123, -376)
              end)
              while r0_48.Parent do
                task.wait()
              end
              local r12_58 = workspace.CurrentCamera.CFrame
              r108_0(false)
              workspace.CurrentCamera.CameraSubject = r16_0.Character:FindFirstChildOfClass("Humanoid")
              workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
              if r162_0 then
                r162_0:Disconnect()
              end
              if _G.PerspectiveTeleportToCameraPos then
                r2_58.CFrame = r12_58
              else
                r2_58.CFrame = r3_58
              end
              -- close: r5_58
            end
          end)
        end
        task.spawn(function()
          -- line: [0, 0] id: 51
          if r2_48 then
            local r0_51 = nil
            local r1_51 = nil
            local r2_51 = nil
            if r16_0.PlayerGui:FindFirstChild("ContextActionGui") then
              while r0_51 == nil do
                local r3_51 = r0_48.Parent
                if r3_51 then
                  r3_51 = pairs
                  for r6_51, r7_51 in r3_51(game.Players.LocalPlayer.PlayerGui.ContextActionGui:GetDescendants()) do
                    if r7_51:IsA("ImageLabel") and r7_51.Image == "http://www.roblox.com/asset/?id=9603678090" then
                      r0_51 = r7_51.Parent
                    end
                  end
                  task.wait()
                else
                  break
                end
              end
            else
              return 
            end
            r0_51.Active = true
            if r0_51 then
              r1_51 = r0_51.MouseButton1Down:Connect(function()
                -- line: [0, 0] id: 53
                print("Launched Mobile!")
                pressedStrength = true
                r2_48.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                r2_48.Velocity = workspace.CurrentCamera.CFrame.lookVector * _G.Strength
              end)
            end
            r2_51 = r0_48:GetPropertyChangedSignal("Parent"):Connect(function()
              -- line: [0, 0] id: 52
              if not r0_48.Parent then
                r5_0:AddItem(r2_48, 1)
                if r1_51 then
                  r1_51:Disconnect()
                end
                r2_51:Disconnect()
              end
            end)
            -- close: r0_51
          end
        end)
        task.spawn(function()
          -- line: [0, 0] id: 56
          if r2_48 then
            local r0_56 = nil
            local r1_56 = nil
            r1_56 = r0_48:GetPropertyChangedSignal("Parent"):Connect(function()
              -- line: [0, 0] id: 57
              if not r0_48.Parent then
                if r9_0:GetLastInputType() == Enum.UserInputType.MouseButton2 and _G.SuperStrength then
                  print("Launched!")
                  pressedStrength = true
                  r2_48.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                  r2_48.Velocity = workspace.CurrentCamera.CFrame.lookVector * _G.Strength
                  r5_0:AddItem(r2_48, 1)
                elseif r9_0:GetLastInputType() == Enum.UserInputType.MouseButton1 then
                  r2_48:Destroy()
                end
                r1_56:Disconnect()
              end
            end)
            -- close: r0_56
          end
        end)
        if _G.Poison_Grab then
          task.spawn(function()
            -- line: [0, 0] id: 49
            if r1_48.Parent:FindFirstChildOfClass("Humanoid") then
              local r0_49 = r1_48.Parent.Head
              while r0_48.Parent do
                local r1_49 = _G.Poison_Grab
                if r1_49 then
                  r61_0.CFrame = r0_49.CFrame
                  r62_0.CFrame = r0_49.CFrame
                  r63_0.CFrame = r0_49.CFrame
                  task.wait()
                  r63_0.Position = Vector3.new(0, -50, 0)
                  r62_0.Position = Vector3.new(0, -50, 0)
                  r1_49 = r61_0
                  r1_49.Position = Vector3.new(0, -50, 0)
                else
                  break
                end
              end
            end
          end)
        end
        if _G.Burn_Grab then
          task.spawn(function()
            -- line: [0, 0] id: 61
            while r0_48.Parent do
              local r1_61 = _G.Burn_Grab
              if r1_61 then
                r1_61 = r1_48.Parent:FindFirstChildOfClass("Humanoid")
                if r1_61 then
                  r79_0(r1_48.Parent.HumanoidRootPart)
                else
                  r1_61 = r1_48.Parent:FindFirstChild("FireDetector")
                  if r1_61 then
                    r79_0(r1_48.Parent.FireDetector)
                  else
                    r79_0(r1_48)
                  end
                end
                task.wait()
              else
                break
              end
            end
          end)
        end
        if _G.Radiactive_Grab then
          task.spawn(function()
            -- line: [0, 0] id: 60
            if r1_48.Parent:FindFirstChildOfClass("Humanoid") then
              while r0_48.Parent do
                local r0_60 = _G.Radiactive_Grab
                if r0_60 then
                  r158_0.Position = r1_48.Position
                  task.wait()
                else
                  break
                end
              end
              r158_0.Position = Vector3.new(0, -50, 0)
            end
          end)
        end
        if _G.Death_Grab then
          task.spawn(function()
            -- line: [0, 0] id: 55
            if r1_48.Parent:FindFirstChildOfClass("Humanoid") then
              local r0_55 = r1_48.Parent:FindFirstChildOfClass("Humanoid")
              local r1_55 = r1_48.Parent.HumanoidRootPart
              while r1_48.Parent do
                local r2_55 = CheckNetworkOwnerShipOnPlayer(r4_0:GetPlayerFromCharacter(r1_48.Parent))
                if r2_55 then
                  r0_55.BreakJointsOnDeath = false
                  r0_55:ChangeState(Enum.HumanoidStateType.Dead)
                  r0_55.Jump = true
                  r0_55.Sit = false
                  r2_55 = r0_55:GetStateEnabled(Enum.HumanoidStateType.Dead)
                  if r2_55 then
                    r23_0:FireServer(r1_48)
                  end
                end
                task.wait()
              end
            end
          end)
        end
      end
      -- close: r1_48
    end
  end)
  workspace.DescendantAdded:Connect(function(r0_361)
    -- line: [0, 0] id: 361
    if r0_361.Name == "PartOwner" and r0_361.Parent.Name == "Head" then
      local r2_361 = r0_361.Parent.Parent:FindFirstChild("HumanoidRootPart")
      if r2_361:FindFirstChild("KickAuraP") then
        r2_361.KickAuraP:Destroy()
      end
      if r2_361:FindFirstChild("KickAuraP1") then
        r2_361.KickAuraP1:Destroy()
      end
      if r2_361:FindFirstChild("SkyVelocity") then
        r2_361.SkyVelocity:Destroy()
      end
    end
    if r0_361.Name == "TimeRemainingNum" and r0_361.Parent.Value == r16_0.Name then
      _G.RemainingTimeInHouse = r0_361
    end
  end)
  r28_0.Changed:Connect(function(r0_80)
    -- line: [0, 0] id: 80
    if r0_80 == true and not r38_0(r4_0:FindFirstChild(r30_0)) and _G.AntiGrab then
      local r2_80 = (r16_0.Character or r16_0.CharacterAdded:Wait()):WaitForChild("HumanoidRootPart")
      if r28_0.Value then
        local r3_80 = nil
        r3_80 = r13_0.Heartbeat:Connect(function()
          -- line: [0, 0] id: 81
          if r28_0.Value then
            r2_80.Velocity = Vector3.new()
            r2_80.Anchored = true
            r31_0:FireServer(r16_0)
            r26_0:FireServer(r2_80, 0)
          else
            r2_80.Velocity = Vector3.new()
            r2_80.Anchored = false
            r3_80:Disconnect()
          end
        end)
        -- close: r3_80
      end
      -- close: r1_80
    end
  end)
  function IsReallyBeingHeld()
    -- line: [0, 0] id: 172
    if r28_0.Value and not _G.AntiGrab then
      return true
    end
    if r28_0.Value and r38_0(r4_0:FindFirstChild(r30_0)) then
      return true
    end
  end
  function setMasslessFalse(r0_41)
    -- line: [0, 0] id: 41
    for r4_41, r5_41 in ipairs(r0_41:GetDescendants()) do
      if r5_41:IsA("BasePart") then
        r5_41.Massless = false
      end
    end
  end
  function enforceMasslessFalse(r0_71)
    -- line: [0, 0] id: 71
    r0_71.DescendantAdded:Connect(function(r0_72)
      -- line: [0, 0] id: 72
      if r0_72:IsA("BasePart") then
        r0_72:GetPropertyChangedSignal("Massless"):Connect(function()
          -- line: [0, 0] id: 73
          if r0_72.Massless then
            r0_72.Massless = false
          end
        end)
      end
    end)
    for r4_71, r5_71 in ipairs(r0_71:GetDescendants()) do
      if r5_71:IsA("BasePart") then
        r5_71:GetPropertyChangedSignal("Massless"):Connect(function()
          -- line: [0, 0] id: 74
          if r5_71.Massless then
            r5_71.Massless = false
          end
        end)
      end
      -- close: r4_71
    end
  end
  function reconnect()
    -- line: [0, 0] id: 316
    local r0_316 = r16_0.Character or r16_0.CharacterAdded:Wait()
    local r1_316 = r0_316:FindFirstChildWhichIsA("Humanoid") or r0_316:WaitForChild("Humanoid")
    local r2_316 = nil
    local r3_316 = r0_316:WaitForChild("HumanoidRootPart")
    local r4_316 = r0_316:WaitForChild("Head")
    CharacterRaycastFilter.FilterDescendantsInstances[1] = r0_316
    COAroundPParams.FilterDescendantsInstances[1] = r0_316
    scriptToGetSenv = r0_316:WaitForChild("GrabbingScript")
    if scriptToGetSenv and getsenv then
      senv = getsenv(scriptToGetSenv)
    end
    local r5_316 = r3_316:WaitForChild("FirePlayerPart"):WaitForChild("CanBurn")
    local r6_316 = r1_316:WaitForChild("Ragdolled")
    local r7_316 = getgenv().key
    if r7_316 == "Xana" then
      if r3_316 then
        r7_316 = r3_316:FindFirstChild("RootAttachment")
        if r7_316 then
          task.delay(1, function()
            -- line: [0, 0] id: 317
            r7_316:Destroy()
          end)
        end
        -- close: r7_316
      end
      r7_316 = r0_316.DescendantAdded
      r7_316:Connect(function(r0_324)
        -- line: [0, 0] id: 324
        if r0_324.Name == "PartOwner" and r0_324.Parent.Name ~= "Head" and _G.AntiKick then
          r26_0:FireServer(r3_316, 0)
        end
      end)
      r7_316 = setMasslessFalse
      r7_316(r0_316)
      r7_316 = enforceMasslessFalse
      r7_316(r0_316)
    end
    r7_316 = Instance
    r7_316 = r7_316.new
    r7_316 = r7_316("BodyPosition", r3_316)
    r7_316.MaxForce = Vector3.new(0, 0, 0)
    r1_316.JumpPower = _G.InfiniteJumpPower
    if _G.NoclipToggle then
      r49_0()
    end
    r0_316.DescendantAdded:Connect(function(r0_319)
      -- line: [0, 0] id: 319
      if r0_319.Name == "PartOwner" then
        r30_0 = tostring(r0_319.Value)
        if _G.AutoAttacker then
          local r1_319 = r4_0:FindFirstChild(r30_0)
          local r2_319 = nil
          local r3_319 = nil
          local r4_319 = nil
          if r1_319 and r1_319.Character then
            r2_319 = r1_319.Character
            if r2_319 then
              r3_319 = r2_319:FindFirstChildOfClass("Humanoid")
              r4_319 = r2_319:FindFirstChild("HumanoidRootPart")
            end
          end
          if r1_319 and r38_0(r1_319) == false and r1_319 ~= r16_0 then
            local r5_319 = nil
            local r6_319 = nil
            local r7_319 = false
            if _G.CounterMode == "Repulsion" or not _G.CounterMode then
              function r5_319()
                -- line: [0, 0] id: 321
                r6_319 = lookAt(r16_0.Character.HumanoidRootPart.Position, r4_319.Position)
                local r0_321 = Instance.new("BodyVelocity", r1_319.Character.HumanoidRootPart)
                r0_321.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                r0_321.Velocity = Vector3.new(r6_319.lookVector.X, 0.5, r6_319.lookVector.Z) * 100
                wait()
                r0_321:Destroy()
                r23_0:FireServer(r4_319)
              end
            elseif _G.CounterMode == "Freeze" then
              function r5_319()
                -- line: [0, 0] id: 320
                local r0_320 = r3_319
                if r0_320 then
                  r0_320.WalkSpeed = 0
                  r0_320.Sit = false
                  r0_320.JumpPower = 0
                end
              end
            elseif _G.CounterMode == "Kick" then
              function r5_319()
                -- line: [0, 0] id: 323
                CreateSkyVelocity(r4_319)
                wait(1)
                r23_0:FireServer(r4_319)
              end
            elseif _G.CounterMode == "Death" then
              function r5_319()
                -- line: [0, 0] id: 322
                local r0_322 = r3_319
                if r0_322 then
                  CreateSkyVelocity(r4_319)
                  for r4_322 = 0, 20, 1 do
                    r0_322.BreakJointsOnDeath = false
                    r0_322:ChangeState(Enum.HumanoidStateType.Dead)
                    r0_322.Jump = true
                    r0_322.Sit = true
                  end
                  task.wait()
                  r23_0:FireServer(r4_319)
                end
              end
            end
            if not r7_319 then
              for r11_319 = 1, 50, 1 do
                if not SNOWshipPlayer(r1_319, r5_319) then
                  task.wait()
                end
              end
            else
              for r11_319 = 1, 50, 1 do
                SNOWshipPermanentPlayer(r1_319, r5_319)
                task.wait()
              end
            end
            -- close: r5_319
          end
          -- close: r1_319
        end
      end
    end)
    r5_316.Changed:Connect(function(r0_326)
      -- line: [0, 0] id: 326
      if r0_326 and _G.AntiBurn then
        while r5_316.Value do
          local r1_326 = firetouchinterest
          if r1_326 then
            firetouchinterest(r3_316.FirePlayerPart, apagarfogo, 0)
            task.wait()
            firetouchinterest(r3_316.FirePlayerPart, apagarfogo, 1)
          else
            apagarfogo.CFrame = r3_316.FirePlayerPart.CFrame * CFrame.new(math.random(-1, 1), math.random(-1, 1), math.random(-1, 1))
            task.wait()
            r1_326 = apagarfogo
            r1_326.Position = Vector3.new(0, -100, 0)
          end
        end
      end
    end)
    r6_316.Changed:Connect(function(r0_325)
      -- line: [0, 0] id: 325
      if r0_325 and _G.AntiExplosion then
        while r6_316.Value do
          local r1_325 = IsReallyBeingHeld()
          if not r1_325 then
            r3_316.Anchored = true
            r1_325 = r3_316
            r1_325.Velocity = Vector3.new()
          else
            r1_325 = r3_316
            r1_325.Anchored = false
          end
          task.wait()
        end
        r3_316.Velocity = Vector3.new()
        r3_316.Anchored = false
      end
    end)
    r1_316.Changed:Connect(function(r0_318)
      -- line: [0, 0] id: 318
      if r0_318 == "Sit" and r1_316.Sit == true and r1_316.SeatPart == nil and _G.AntiGrab then
        r1_316:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
        r1_316.Sit = false
      end
      if r0_318 == "SeatPart" and r1_316.SeatPart == nil then
        ResetCharacterStats()
        if r3_316:FindFirstChild("BodyPositionFloat") then
          r3_316.BodyPositionFloat:Destroy()
        end
        r7_316.MaxForce = Vector3.new(0, 0, 0)
      end
      if r0_318 == "MoveDirection" and _G.RockBlobman and r149_0() then
        r7_316.Position = r3_316.Position
        if r1_316.MoveDirection.Magnitude > 0 then
          r7_316.MaxForce = Vector3.new(0, 0, 0)
        else
          r7_316.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        end
      end
      -- warn: not visited block [5]
      -- block#5:
      -- _u1.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
      -- _u1.Position = _u2.Position

    end)
    if r1_316 then
      r2_316 = r1_316:WaitForChild("Animator", 1)
      if r2_316 then
        TypeAnimation = r2_316:LoadAnimation(typeAnimation)
        FlailAnimation = r2_316:LoadAnimation(flailAnimation)
      end
    end
  end
  r9_0.JumpRequest:Connect(function()
    -- line: [0, 0] id: 158
    if _G.InfiniteJump then
      r16_0.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
    end
  end)
  r13_0.Heartbeat:Connect(function()
    -- line: [0, 0] id: 249
    if _G.SuperSpeed then
      r16_0.Character.HumanoidRootPart.CFrame = r16_0.Character.HumanoidRootPart.CFrame + r16_0.Character:FindFirstChildOfClass("Humanoid").MoveDirection * Multiplier
    end
  end)
  function CanRemoveStickyPart(r0_78, r1_78, r2_78)
    -- line: [0, 0] id: 78
    local r3_78 = nil	-- notice: implicit variable refs by block#[2]
    if r1_78:GetAttribute("Kicking2") then
      r3_78 = true
    end
    return r3_78
  end
  task.spawn(function()
    -- line: [0, 0] id: 263
    while task.wait() do
      local r0_263 = pairs
      for r3_263, r4_263 in r0_263(r4_0:GetPlayers()) do
        if r151_0(r4_263) then
          local r5_263 = r4_263
          local r6_263 = r5_263.Character
          local r7_263 = r5_263.Character:FindFirstChild("HumanoidRootPart")
          if r5_263 and r6_263 and r7_263 and CanRemoveStickyPart(r5_263, r6_263, r7_263) then
            r68_0(r7_263)
          end
        end
      end
    end
  end)
  function PlayerRemoving_Added(r0_278)
    -- line: [0, 0] id: 278
    r39_0(PlayerToSelect)
    r39_0(r188_0)
    r39_0(r179_0)
    r39_0(PlayerToTeleport)
    r43_0(PlayerToTarget)
  end
  r33_0 = PlayerRemoving_Added
  r4_0.PlayerAdded:Connect(PlayerRemoving_Added)
  r4_0.PlayerRemoving:Connect(PlayerRemoving_Added)
  task.spawn(PlayerRemoving_Added)
  task.spawn(reconnect)
  r4_0.PlayerAdded:Connect(function(r0_360)
    -- line: [0, 0] id: 360
    if r0_360:IsFriendsWith(r16_0.UserId) and not r46_0(r0_360.Name) then
      table.insert(r41_0, r0_360.Name)
    end
    r42_0(r180_0, r41_0)
  end)
  task.spawn(function()
    -- line: [0, 0] id: 188
    for r3_188, r4_188 in pairs(r4_0:GetPlayers()) do
      if r4_188:IsFriendsWith(r16_0.UserId) then
        table.insert(r41_0, r4_188.Name)
      end
    end
    r42_0(r180_0, r41_0)
  end)
  r16_0.CharacterAdded:Connect(reconnect)
  r3_0:Init()
  -- close: r3_0
end
