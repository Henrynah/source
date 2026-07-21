function sno(part)
    if not part or not part.Parent then return end
    pcall(function()
        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(part, part.CFrame)
    end)
end

-- Variables to store the target
local TargetPlayer = nil
local HRP = nil
local kickLoopEnabled = false

-- Function to get the closest player from cursor
local function GetClosestPlayerFromCursor()
    local targetPart = Mouse.Target
    if not targetPart then return nil end

    local character = targetPart:FindFirstAncestorOfClass("Model")
    if not character then return nil end

    return Players:GetPlayerFromCharacter(character)
end

local kickLoopEnabled = false

LoopTab:CreateToggle({
    Name = "Loop Grab Kick (new one)",
    CurrentValue = false,
    Flag = "LoopGrabKickToggle",
    Callback = function(Value)
        kickLoopEnabled = Value
        
        if kickLoopEnabled then
            if not SelectedPlayer or SelectedPlayer == "" then
                kickLoopEnabled = false
                print("Error: huh??")
                return
            end
            
            task.spawn(function()
                local targetplr = Players:FindFirstChild(SelectedPlayer)
                if not targetplr then 
                    kickLoopEnabled = false
                    print("error: ok what the frik")
                    return 
                end
                
                print("kick that boi " .. targetplr.Name)
                
                while kickLoopEnabled and RunService.Heartbeat:Wait() do
                    local char = LocalPlayer.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    local head = char and char:FindFirstChild("Head")
                    
                    if not (char and hrp and head) then 
                        task.wait(0)
                        continue 
                    end

                    targetplr = Players:FindFirstChild(SelectedPlayer)
                    if not targetplr or not targetplr.Character then 
                        task.wait(0)
                        continue 
                    end
                    
                    local Head = targetplr.Character:FindFirstChild("Head")
                    local Hum = targetplr.Character:FindFirstChild("Humanoid")
                    
                    if not (Head and Hum) or Hum.Health == 0 then
                        task.wait(0)
                        continue
                    end
                    
                    local BodyPos = Head:FindFirstChild("BodyPosition")
                    
                    if (Head.Position - hrp.Position).Magnitude <= 30 then
                        if not BodyPos then
                            BodyPos = Instance.new("BodyPosition")
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
                            Head = newChar:WaitForChild("Head", 2)
                            Hum = newChar:FindFirstChild("Humanoid")
                        end
                        
                        repeat
                            if not kickLoopEnabled then break end
                            
                            pcall(function()
                                char:PivotTo(Head.CFrame * CFrame.new(0, 10, 0))
                            end)
                            
                            sno(Head)
                            task.wait(0)
                            
                        until not Head 
                            or (Head:FindFirstChild("PartOwner") and targetplr:FindFirstChild("IsHeld") and targetplr.IsHeld.Value) 
                            or not kickLoopEnabled
                            
                        if Head and Head:FindFirstChild("PartOwner") then
                            task.defer(function()
                                for i = 1, 59 do
                                    if not kickLoopEnabled or not Head.Parent then break end
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
    
                print("disable")
                
                if targetplr and targetplr.Character then
                    local Head = targetplr.Character:FindFirstChild("Head")
                    if Head then
                       
                        local BodyPos = Head:FindFirstChild("BodyPosition")
                        if BodyPos then
                            BodyPos:Destroy()
                        end
                        pcall(function()
                            ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(Head)
                        end)
                    end
                end
                
                print("aa")
            end)
        else
            kickLoopEnabled = false
            print("disabled")
        end
    end    
})
