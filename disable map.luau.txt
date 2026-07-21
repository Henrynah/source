local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LocalPlayer = Players.LocalPlayer

local SpawnToyRemoteFunction = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
local GrabEvent = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
local DestroyToy = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")

local Character, HumanoidRootPart
local folder

local toys = 5
local activePencils = {}

local function GetHighestGrabbableHitbox(blob)
    local highest = nil
    local highestY = -math.huge

    for _, v in ipairs(blob:GetDescendants()) do
        if v.Name == "GrabbableHitbox" and v:IsA("BasePart") then
            local y = v.Position.Y

            if y > highestY then
                highestY = y
                highest = v
            end
        end
    end

    return highest
end

local function SetupCharacter(char)
    Character = char
    HumanoidRootPart = char:WaitForChild("HumanoidRootPart")
    folder = workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")
end

SetupCharacter(LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait())
LocalPlayer.CharacterAdded:Connect(SetupCharacter)

local function SpawnPencil()
    local pos = HumanoidRootPart.CFrame * CFrame.Angles(-0.605224, -0.321753, 0)

    task.spawn(function()
        SpawnToyRemoteFunction:InvokeServer(
            "ToolPencil",
            pos,
            Vector3.new(0, 0, 0)
        )
    end)
end

local function WaitForNewPencil(timeout)
    timeout = timeout or 6

    local result = nil
    local done = false

    local connection
    connection = folder.ChildAdded:Connect(function(child)
        if child.Name == "ToolPencil" then
            result = child
            done = true
        end
    end)

    local start = tick()

    while not done and tick() - start < timeout do
        task.wait()
    end

    connection:Disconnect()

    return result
end

local function ApplyPermanentLift(pencil)
    local soundPart = pencil:FindFirstChild("SoundPart")
    if not soundPart then
        return
    end

    for _, v in ipairs(soundPart:GetChildren()) do
        if v:IsA("LinearVelocity") then
            v:Destroy()
        end
    end

    local att = Instance.new("Attachment")
    att.Parent = soundPart
end

local function CreateOnePencil()
    while true do
        SpawnPencil()

        local pencil = WaitForNewPencil()
        if not pencil then
            task.wait(0.2)
            continue
        end

        task.wait(0.19)
        pencil.StickyPart.CanTouch = false

        local soundPart = pencil:FindFirstChild("SoundPart")
        if not soundPart then
            continue
        end

        GrabEvent:FireServer(soundPart, soundPart.CFrame)

        task.wait(0.15)

        local owner = soundPart:FindFirstChild("PartOwner")

        if owner and owner.Value == LocalPlayer.Name then
            table.insert(activePencils, pencil)

            ApplyPermanentLift(pencil)

            return true
        else
            DestroyToy:FireServer(pencil)
        end

        task.wait(0.2)
    end
end

function KickPlayerOnBlob(blob)
    local target = 1
    local created = 0

    while created < target do
        if CreateOnePencil() then
            created += 1
        end

        task.wait(0.4)
    end

    for _, pencil in ipairs(activePencils) do
        if pencil and pencil.Parent then
            game:GetService("ReplicatedStorage").GrabEvents.DestroyGrabLine:FireServer(pencil:FindFirstChildOfClass("BasePart"))

            local args = {
                [1] = pencil.StickyPart,
                [2] = blob:GetChildren()[20],
                [3] = CFrame.new(1e45, math.huge, 1e98)
            }

            game:GetService("ReplicatedStorage").PlayerEvents.StickyPartEvent:FireServer(unpack(args))
        end
    end
end

function KickPlayerOnBlob12Kunai(blob)
    local target = 12
    local created = 0

    while created < target do
        if CreateOnePencil() then
            created += 1
        end

        task.wait(0.4)
    end

    for _, pencil in ipairs(activePencils) do
        if pencil and pencil.Parent then
            game:GetService("ReplicatedStorage").GrabEvents.DestroyGrabLine:FireServer(pencil:FindFirstChildOfClass("BasePart"))

            local args = {
                [1] = pencil.StickyPart,
                [2] = blob:GetChildren()[20],
                [3] = CFrame.new(1e45, math.huge, 1e98)
            }

            game:GetService("ReplicatedStorage").PlayerEvents.StickyPartEvent:FireServer(unpack(args))
        end
    end
end

function BreakMap()
    local target = 1
    local created = 0

    while created < target do
        if CreateOnePencil() then
            created += 1
        end

        task.wait(0.4)
    end

    for _, pencil in ipairs(activePencils) do
        if pencil and pencil.Parent then
            game:GetService("ReplicatedStorage").GrabEvents.DestroyGrabLine:FireServer(pencil:FindFirstChildOfClass("BasePart"))

            local args = {
                [1] = pencil.StickyPart,
                [2] = workspace.Map.BaseGround:GetChildren()[154],
                [3] = CFrame.new(1e45, math.huge, 1e98)
            }

            game:GetService("ReplicatedStorage").PlayerEvents.StickyPartEvent:FireServer(unpack(args))
        end
    end
end

function BreakPlot(...)
    local plots = { ... }
    local target = #plots

    local created = 0
    local pencils = {}

    while created < target do
        if CreateOnePencil() then
            created += 1

            local pencil = activePencils[#activePencils]
            table.insert(pencils, pencil)
        end

        task.wait(0.4)
    end

    for i, plot in ipairs(plots) do
        local pencil = pencils[i]

        if typeof(pencil) == "Instance" and pencil.Parent then
            game:GetService("ReplicatedStorage").GrabEvents.DestroyGrabLine:FireServer(
                pencil:FindFirstChildOfClass("BasePart")
            )

            game:GetService("ReplicatedStorage").PlayerEvents.StickyPartEvent:FireServer(
                pencil.StickyPart,
                plot.PlotArea,
                CFrame.new(0 / 0, math.huge, 0 / 0)
            )
        end
    end
end

workspace.Plots.Plot3
