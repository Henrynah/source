CheckPlotPosition1 = Vector3.new(259, -7, 450)
CheckPlotPosition2 = Vector3.new(242, -7, 411)
CheckPlotFarming = false

function checkPlotAndFarm()
    local char = LocalPlayer.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    
    local savedPos = hrp.CFrame
    
    local spawnCFrame = CFrame.new(hrp.Position + Vector3.new(2, 0, 0))
    ToySpawn:InvokeServer("FoodHamburger", spawnCFrame, Vector3.new(0, -113, 0))
    task.wait(0.3)
    
    local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if not spawnedToys then return end
    
    local burger = nil
    for _, item in ipairs(spawnedToys:GetChildren()) do
        if item.Name == "FoodHamburger" then
            burger = item
            break
        end
    end
    
    if not burger then
        OrionLib:MakeNotification({
            Name = "Check Plot",
            Content = "Failed to spawn burger!",
            Image = "rbxassetid://4483345998",
            Time = 3
        })
        return
    end
    
    local holdPart = burger:FindFirstChild("HoldPart")
    if not holdPart then return end
    
    local holdFunc = holdPart:FindFirstChild("HoldItemRemoteFunction")
    if not holdFunc then return end
    
    holdFunc:InvokeServer(burger, char)
    task.wait(0.05)
    
    local UseEvent = ReplicatedStorage:FindFirstChild("HoldEvents") and ReplicatedStorage.HoldEvents:FindFirstChild("Use")
    if UseEvent then
        UseEvent:FireServer(burger)
    end
    task.wait(0.1)
    
    hrp.CFrame = CFrame.new(CheckPlotPosition1)
    task.wait(0.3)
    
    if not burger or not burger.Parent then
        OrionLib:MakeNotification({
            Name = "Check Plot",
            Content = "Start break plot",
            Image = "rbxassetid://4483345998",
            Time = 3
        })
        
        hrp.CFrame = CFrame.new(CheckPlotPosition2)
        task.wait(0.2)
        
        checkPlotCycle4Fast()
    else
        OrionLib:MakeNotification({
            Name = "Check Plot",
            Content = "Plot already broken!",
            Image = "rbxassetid://4483345998",
            Time = 3
        })
        
        ReplicatedStorage.MenuToys.DestroyToy:FireServer(burger)
        
        task.wait(0.1)
        hrp.CFrame = savedPos
    end
end

function checkPlotCycle4Fast()
    local character = LocalPlayer.Character
    if not character then return end
    
    local hrp = character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    
    hrp.CFrame = CFrame.new(CheckPlotPosition2)
    task.wait(0.15)
    
    for i = 1, 4 do
        ToySpawn:InvokeServer(
            "FoodHamburger",
            CFrame.new(hrp.Position + Vector3.new(i * 2, 0, 0)),
            Vector3.new(0, -113, 0)
        )
        task.wait(0.03)
    end
    
    task.wait(0.2)
    
    local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if not spawnedToys then return end
    
    local burgers = {}
    for _, item in ipairs(spawnedToys:GetChildren()) do
        if item.Name == "FoodHamburger" then
            table.insert(burgers, item)
        end
    end
    
    for i, burger in ipairs(burgers) do
        if not burger or not burger.Parent then continue end
        
        local holdPart = burger:FindFirstChild("HoldPart")
        if not holdPart then continue end
        
        local holdFunc = holdPart:FindFirstChild("HoldItemRemoteFunction")
        if not holdFunc then continue end
        
        holdFunc:InvokeServer(burger, character)
        task.wait(0.05)
        
        local UseEvent = ReplicatedStorage:FindFirstChild("HoldEvents") and ReplicatedStorage.HoldEvents:FindFirstChild("Use")
        if UseEvent then
            UseEvent:FireServer(burger)
        end
        task.wait(0.1)
        
        hrp.CFrame = CFrame.new(CheckPlotPosition1)
        task.wait(0.1)
        
        ReplicatedStorage.MenuToys.DestroyToy:FireServer(burger)
        task.wait(0.05)
        
        if i < #burgers then
            hrp.CFrame = CFrame.new(CheckPlotPosition2)
            task.wait(0.1)
        end
    end
    
    OrionLib:MakeNotification({
        Name = "Check Plot",
        Content = "Plot should be broken now little skid",
        Image = "rbxassetid://4483345998",
        Time = 3
    })
end

SettingsTab:AddButton({
    Name = "Check Plot Broken or No",
    Callback = function()
        checkPlotAndFarm()
    end
})
