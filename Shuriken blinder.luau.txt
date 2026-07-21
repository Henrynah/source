local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")

local plr = Players.LocalPlayer
local folder = workspace:WaitForChild(plr.Name.."SpawnedInToys", 10)

local Enabled = false
local TargetPlayer = nil
local currentHouseS = 0
local TrackedShuriken = nil
local StickLoop = nil

local MAGNET_RANGE = 50
local STICK_REPEATS = 3
local STICK_DELAY = 0.1
local MAX_DISTANCE = 100

local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
local Window = Rayfield:CreateWindow({
   Name = "Ninja Shuriken Sticker",
   LoadingTitle = "Loading...",
   LoadingSubtitle = "by Script",
})

local MainTab = Window:CreateTab("Main", nil)

function safe(f, ...) 
    local s, r = pcall(f, ...) 
    if s then return r end 
end

function destroy(k)
    if k and k.Parent then
        safe(function()
            ReplicatedStorage.MenuToys.DestroyToy:FireServer(k)
        end)
    end
end

function destroyAdvanced(k)
    if k and k.Parent then
        safe(function()
            local sticky = k:FindFirstChild("StickyPart")
            if sticky then
                ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(sticky, sticky.CFrame)
            end
            ReplicatedStorage.MenuToys.DestroyToy:FireServer(k)
        end)
    end
end

function spawnShuriken()
    local char = plr.Character
    if not char or not char.PrimaryPart then return end
    local cf = char.PrimaryPart.CFrame * CFrame.new(0, 0, -5)
    return safe(function()
        return ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("NinjaShuriken", cf, Vector3.new(0, 48, 0))
    end)
end

function getShuriken()
    if currentHouseS > 0 then
        local p = workspace.PlotItems:FindFirstChild("Plot"..currentHouseS)
        if p then
            for _, v in ipairs(p:GetChildren()) do
                if v.Name == "NinjaShuriken" and v:IsA("Model") and v.Parent then
                    return v
                end
            end
        end
    else
        for _, v in ipairs(folder:GetChildren()) do
            if v.Name == "NinjaShuriken" and v:IsA("Model") and v.Parent then
                return v
            end
        end
    end
    return nil
end

function updateCurrentHouse()
    currentHouseS = 0
    local char = plr.Character
    if not char then return end
    if char:IsDescendantOf(workspace.PlotItems.PlayersInPlots) then
        for _, e in workspace.Plots:GetChildren() do
            for _, owner in e.PlotSign.ThisPlotsOwners:GetChildren() do
                if owner.Value == plr.Name then
                    local plotName = owner.Parent.Parent.Parent.Name
                    currentHouseS = tonumber(plotName:match("%d+")) or 0
                end
            end
        end
    end
end

function teleportToTarget()
    if not TargetPlayer then return end
    local targetChar = TargetPlayer.Character
    local myChar = plr.Character
    
    if targetChar and myChar and targetChar:FindFirstChild("HumanoidRootPart") and myChar:FindFirstChild("HumanoidRootPart") then
        local targetRoot = targetChar.HumanoidRootPart
        local myRoot = myChar.HumanoidRootPart
        
        myRoot.CFrame = targetRoot.CFrame + Vector3.new(0, 0, -5)
        myRoot.Velocity = Vector3.new(0, 0, 0)
    end
end

function stickShurikenToHead()
    if not TargetPlayer then return end
    
    local targetChar = TargetPlayer.Character
    if not targetChar then return end
    
    local targetHead = targetChar:FindFirstChild("Head")
    if not targetHead then return end
    
    local shuriken = getShuriken()
    if not shuriken then
        spawnShuriken()
        task.wait(0.3)
        shuriken = getShuriken()
    end
    
    if not shuriken then return end
    
    local stickyPart = shuriken:FindFirstChild("StickyPart")
    if not stickyPart then return end
    
    safe(function()
        ReplicatedStorage.PlayerEvents.StickyPartEvent:FireServer(
            stickyPart,
            targetHead,
            CFrame.new(0, 0, 0) * CFrame.Angles(0, 0, 0)
        )
    end)
    
    task.wait(0.1)
    
    TrackedShuriken = shuriken
    
    task.defer(function()
        if stickyPart and stickyPart.Parent then
            for _, part in pairs(shuriken:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = false
                end
            end
            
            local bodyPosition = Instance.new("BodyPosition")
            bodyPosition.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bodyPosition.P = 10000
            bodyPosition.D = 500
            bodyPosition.Parent = stickyPart
            
            StickLoop = RunService.Heartbeat:Connect(function()
                if not (Enabled and TargetPlayer and stickyPart and stickyPart.Parent) then
                    if bodyPosition then bodyPosition:Destroy() end
                    if StickLoop then StickLoop:Disconnect() end
                    return
                end
                
                local tChar = TargetPlayer.Character
                local tHead = tChar and tChar:FindFirstChild("Head")
                
                if tHead then
                    stickyPart.AssemblyAngularVelocity = Vector3.new(
                        math.random(-100, 100) * 50,
                        math.random(-100, 100) * 50,
                        math.random(-100, 100) * 50
                    )
                    
                    bodyPosition.Position = Vector3.new(
                        tHead.Position.X,
                        tHead.Position.Y - 2,
                        tHead.Position.Z
                    )
                    
                    task.wait(0.0001)
                    
                    bodyPosition.Position = Vector3.new(
                        tHead.Position.X,
                        tHead.Position.Y + 2,
                        tHead.Position.Z
                    )
                end
            end)
        end
    end)
end

function getPlayerList()
    local list = {}
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= plr then
            table.insert(list, p.Name)
        end
    end
    return list
end

function stopSticking()
    Enabled = false
    if StickLoop then
        StickLoop:Disconnect()
        StickLoop = nil
    end
    if TrackedShuriken then
        destroyAdvanced(TrackedShuriken)
        TrackedShuriken = nil
    end
end


local PlayerDropdown = MainTab:CreateDropdown({
   Name = "Select Target Player",
   Options = getPlayerList(),
   CurrentOption = {"None"},
   MultipleOptions = false,
   Flag = "PlayerDropdown",
   Callback = function(Option)
        local selectedName = Option[1]
        TargetPlayer = Players:FindFirstChild(selectedName)
   end,
})

MainTab:CreateButton({
   Name = "Refresh Players",
   Callback = function()
        PlayerDropdown:Refresh(getPlayerList(), true)
   end,
})

MainTab:CreateToggle({
   Name = "Stick Shuriken to Target Head",
   CurrentValue = false,
   Flag = "StickerToggle",
   Callback = function(Value)
        Enabled = Value
        
        if Enabled then
            if not TargetPlayer then
                Rayfield:Notify({
                   Title = "Error",
                   Content = "Please select a target player first!",
                   Duration = 3,
                })
                Enabled = false
                return
            end
            
            updateCurrentHouse()
            teleportToTarget()
            task.wait(0.5)
            stickShurikenToHead()
        else
            stopSticking()
        end
   end,
})

MainTab:CreateSlider({
   Name = "Magnet Range",
   Range = {10, 200},
   Increment = 10,
   CurrentValue = MAGNET_RANGE,
   Flag = "MagnetRange",
   Callback = function(Value)
        MAGNET_RANGE = Value
   end,
})

folder.ChildAdded:Connect(function(c)
    if Enabled and c.Name == "NinjaShuriken" then
        local sticky = c:WaitForChild("StickyPart", 5)
        if sticky then
            ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(sticky, sticky.CFrame)
        end
    end
end)

Players.PlayerRemoving:Connect(function(p)
    if p == TargetPlayer then
        stopSticking()
    end
end)

Rayfield:LoadConfiguration()
