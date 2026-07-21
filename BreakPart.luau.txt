-- (to make somePlayer instantly destroyed when ragdolled) --
-- BreakPart(somePlayer.Character["Right Arm"].RagdollLimbPart)

-- (to break map part)
-- BreakPart(someGroundPartOrSmth)

---- services ----
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Camera = workspace.CurrentCamera

---- players ----
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local Inventory = workspace:FindFirstChild(`{LocalPlayer.Name}SpawnedInToys`)

---- replicated storage ----
local GrabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
local SNO = GrabEvents:FindFirstChild("SetNetworkOwner")

local PlayerEvents = ReplicatedStorage:FindFirstChild("PlayerEvents")
local SPE = PlayerEvents:FindFirstChild("StickyPartEvent")

local MenuToys = ReplicatedStorage:FindFirstChild("MenuToys")
local STE = MenuToys:FindFirstChild("SpawnToyRemoteFunction")
local DTE = MenuToys:FindFirstChild('DestroyToy')

---- ui ----
local repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"
local Library = loadstring(game:HttpGet(repo .. "Library.lua"))()

---- funcs ----
local function Char()
    local Character = LocalPlayer.Character
    if not Character then return end

    return Character, Character:FindFirstChild("HumanoidRootPart"), Character:FindFirstChild("Humanoid")
end

local function SpawnToy(name)
    local _, HRP = Char()
    if not HRP then return end

    local Toy

    local conn; conn = Inventory.ChildAdded:Connect(function(child)
        if child:IsA("Model") and child.Name == name then
            Toy = child
            conn:Disconnect()
        end
    end)

    task.spawn(function() STE:InvokeServer(name, CFrame.new(HRP.Position) + Vector3.new(5, 0, 20), Vector3.new()) end)

    local Start = tick()
    repeat task.wait() until Toy or tick() - Start > 1

    return Toy, conn:Disconnect()
end

local function GetTarget()
    if not Library.IsMobile and Mouse then return Mouse.Target end

    local size = Camera.ViewportSize
    local ray = Camera:ViewportPointToRay(size.X / 2, size.Y / 2)

    local params = RaycastParams.new()
    params.FilterType = Enum.RaycastFilterType.Exclude

    local Char = Char()

    if Char then
        params.FilterDescendantsInstances = {Char}
    end

    local result = workspace:Raycast(ray.Origin, ray.Direction * 1000, params)
    return result and result.Instance
end

-- !!---- the magic ----!!
local function BreakPart(Part)
    if not Part or not Part:IsA('BasePart') then return end

    local Shuriken = SpawnToy("NinjaShuriken")
    local StickyPart = Shuriken and Shuriken:WaitForChild("StickyPart", 0.5)
    
    if not StickyPart then return end
    SNO:FireServer(StickyPart, StickyPart.CFrame)

    local Start = tick()
    repeat task.wait() until (StickyPart:FindFirstChild("PartOwner") and StickyPart.PartOwner.Value == LocalPlayer.Name) or tick() - Start > 0.5

    if tick() - Start > 0.5 then return DTE:FireServer(Shuriken) end
    SPE:FireServer(StickyPart, Part, CFrame.new(1e15, 1e15, 1e15)) -- <-- the important part !!! look at start of script for usage
end

---- window ----
local win = Library:CreateWindow({
    Title = "Map Part Breaker",
    ToggleKeybind = Enum.KeyCode.RightShift,
    Center = true,
    AutoShow = true
})

local MainTab = win:AddTab("Break parts")
MainTab = MainTab:AddLeftGroupbox("Break parts")

---- elements of MainTab ----
MainTab:AddLabel("Break Part"):AddKeyPicker("BreakPart", {
    Text = "Break Part",
    Mode = "Press",
    Callback = function()
        local target = GetTarget()
        BreakPart(target)
    end
})

local button
MainTab:AddButton({
    Text = "Create Button",
    Func = function()
        button = Library:AddDraggableButton("Break Part", function()
            local target = GetTarget()
            BreakPart(target)
        end)
    end
}):AddButton({
    Text = "Remove Button",
    Func = function()
        if button and button.Button then button:Destroy() end
    end
})
