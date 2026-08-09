local u = loadstring(game:HttpGet(('https://raw.githubusercontent.com/fiiremax/Scripts/refs/heads/master/Scriptnew/GitHub/Scripts/Module.lua')))()

local var      = getgenv().var
local IsAround = u.IsAround
local GPNames  = u.GPNames
local inpast   = u.inpast
local ippp     = u.IPPP
local SICF     = u.SICF
local FINF     = u.FINF
local SPE      = u.SPE 
local MBP      = u.MBP
local wfc      = u.wfc
local fps      = u.fps
local FMC      = u.FMC
local timer    = u.tmr
local cm       = u.cm
local tp       = u.tp
local tm       = u.tm
local p        = u.p
--sep                                                                  
--# VariablesUI(Front-End)

var({
    CE     = game:GetService("ReplicatedStorage").CharacterEvents,
    CAS    = game:GetService("ContextActionService"),
    VIM    = game:GetService("VirtualInputManager"),
    p      = game:GetService("Players").LocalPlayer,
    RS     = game:GetService("ReplicatedStorage"),
    UIS    = game:GetService("UserInputService"),
    TXS    = game:GetService("TextChatService"),
    RF     = game:GetService("ReplicatedFirst"),
    PS     = game:GetService("PhysicsService"),
    TS     = game:GetService("TweenService"),
    RunS   = game:GetService("RunService"),
    SG     = game:GetService("StarterGui"),
    L      = game:GetService("Lighting"),
    ps     = game:GetService("Players"),
    Debris = game:GetService("Debris")
})

var("Remotes", {
    setowner = var("RS")["GrabEvents"]["SetNetworkOwner"],
    dgl      = var("RS")["GrabEvents"]["DestroyGrabLine"],
    cgl      = var("RS")["GrabEvents"]["CreateGrabLine"],
    egl      = var("RS")["GrabEvents"]["ExtendGrabLine"],
    dt       = var("RS")["MenuToys"]["DestroyToy"],
    use      = var("RS")["HoldEvents"]["Use"], 
    rr       = var("CE")["RagdollRemote"],
    strg     = var("CE")["Struggle"]
})

var("ov", {
    char  = var("p").Character or var("p").CharacterAdded:Wait(),
    inv   = workspace[var("p").Name .. "SpawnedInToys"],
    cam   = workspace.CurrentCamera,
    mouse = var("p"):GetMouse()
})

var("Valores", {})
var("Toggle", {})
var("Events", {})
var("DropsI", {})
var("DropsO", {})
var("Drops", {})
var("paths", {})
var("Conns", {})
var("Lists", {})
var("v", {})

cm(var("p"), "CharacterAdded", function(char)
    var("ov").char = char
end, "CharA")
--sep                                                                  
--# Helpers

--? game(General)
function ping()
    return game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValue()
end

--? game(Script Workspace)

workspace.FallenPartsDestroyHeight = -50000
--//
function verify(part, mode)
    if mode == 2 then
        if part:FindFirstChild("PartOwner") and part["PartOwner"].Value == var("p").Name then
            return true
        else 
            return false
        end
    end
    return not part:IsGrounded() and part.AssemblyRootPart.ReceiveAge == 0
end
--//
function dgl(part)
    var("Remotes").dgl:FireServer(part)
end
--//
function setowner(part, mode)
    if mode == "Safe" then 
        if not IsAround(part, 30) then return end
        var("Remotes").setowner:FireServer(part, part.CFrame)
        return
    else
        var("Remotes").setowner:FireServer(part, part.CFrame)
    end
end
--//
function predictPos(part)
    local vel = part.AssemblyLinearVelocity
    if vel.Magnitude < 2 then
        return part.Position
    end
    
    local pingMs = game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValue()
    local predTime = (pingMs / 1000) * 0.8 + 0.05
    
    local gravity = Vector3.new(0, - workspace.Gravity * 0.5, 0)
    local predPos = part.Position + (vel * predTime) + (gravity * predTime * predTime * 0.5)
    
    return predPos
end

function verify(part, mode)
    if mode == 2 then
        if part:FindFirstChild("PartOwner") and part:FindFirstChild("PartOwner").Value == var("p").Name then
            return true
        else
            return false
        end
    end
    return not part:IsGrounded() and part.AssemblyRootPart.ReceiveAge == 0
end

function dssetowner(part, range, mode, callback)
    if not part or not part.Parent then return end
    var("Remotes").strg:FireServer()
    
    local hrp = MBP("HumanoidRootPart")
    if not hrp then return end
    
    local stop = false
    
    task.spawn(function()
        repeat
            task.wait(0.01)
            if mode == 2 then
                local acc = part
                while acc do
                    local player = game.Players:GetPlayerFromCharacter(acc)
                    if player then
                        local head = acc:FindFirstChild("Head")
                        if verify(head, 2) then
                            stop = true
                        end
                        break
                    end
                    acc = acc.Parent
                end
            elseif mode == 3 then
                if verify(part, 2) then
                    stop = true
                    break
                end
            else
                if verify(part) then stop = true end
            end
        until stop
    end)
    
    if IsAround(part, range or 29) then
        for i = 1, 12 do
            if stop then return end
            setowner(part)
            task.wait(0.02)
        end

        if callback then
            callback()
        end
    else
        local origPos = hrp.CFrame
        task.wait(0.03)
        
        for i = 1, 18 do
            if stop then break end
            hrp.CFrame = CFrame.new(predictPos(part)) * CFrame.new(0, 10, 0)
            task.wait(0.01)
            setowner(part)
        end
        task.wait(0.01)
        if callback then
            callback()
        end
        hrp.CFrame = origPos
    end
    
    stop = true
end
--//
function hdrem(b, mode)
    if not b or not mode then return end
    local toy = b:WaitForChild("HoldPart")
    task.spawn(function()
        if mode == "Hold" then 
            toy:WaitForChild("HoldItemRemoteFunction", 2):InvokeServer(b, var("ov").char)
        elseif mode == "Drop" then
            toy:WaitForChild("DropItemRemoteFunction", 2):InvokeServer(b, CFrame.new(0, 60000, 0), Vector3.new(0, 0, 0))
        elseif mode == "Perm" then
            toy:WaitForChild("DropItemRemoteFunction", 2):InvokeServer(b, MBP("HumanoidRootPart").Position + Vector3.new(0, 13, 20), nil)
        end
    end)
end
--//
function GucciLoop()
    task.spawn(function()
        cm(var("RunS"), "Heartbeat", function()
            if var("Toggle").tGucciAnti then
                var("Remotes").rr:FireServer(MBP("HumanoidRootPart"), 0)
                if MBP("Humanoid").Sit == true and var("Toggle").Autott then
                end
            end
        end, "GLooCn")
    end)
end

GucciLoop()
--sep                                                                  
-- # Lists

var("Lists").Loc2 = {
    ["My Front"] = MBP("HumanoidRootPart").CFrame * CFrame.new(0, 0, -5),
    ["Green House"] = CFrame.new(-520.812134, -7.37385082, 69.9078217, -0.337429404, -2.63777817e-08, 0.941350818, 1.26098882e-08, 1, 3.25412479e-08, -0.941350818, 2.28507027e-08, -0.337429404),
    ["Red House"] = CFrame.new(-453.869141, -7.37385082, -140.031342, 0.322511047, 6.03401702e-08, 0.946565688, -6.97522964e-08, 1, -3.99806197e-08, -0.946565688, -5.31309396e-08, 0.322511047),
    ["Farm"] = CFrame.new(-148.77948, 59.7546234, -240.74884, 0.701087117, -9.27232904e-08, 0.713075638, 3.49749101e-08, 1, 9.56459942e-08, -0.713075638, -4.21164188e-08, 0.701087117),
    ["Blue House"] = CFrame.new(479.195953, 83.3133392, -274.814117, 0.950938761, -2.70165685e-08, -0.309379101, 3.71699791e-08, 1, 2.69242655e-08, 0.309379101, -3.71029429e-08, 0.950938761),
    ["Chinese House"] = CFrame.new(499.552246, 123.312851, -92.730896, 0.00229457533, -1.02152534e-07, -0.999997377, 1.72069736e-08, 1, -1.02113319e-07, 0.999997377, -1.69726206e-08, 0.00229457533),
    ["Purple House"] = CFrame.new(256.983032, -7.37385082, 420.039124, -0.888950348, 3.12136894e-09, -0.458003581, -3.55322272e-10, 1, 7.50481632e-09, 0.458003581, 6.83414791e-09, -0.888950348),
    ["Green House2"] = CFrame.new(-259.680664, 80.6256104, 291.477905, -0.847916007, 1.20325598e-08, 0.530130625, 1.26154482e-08, 1, -2.51960364e-09, -0.530130625, 4.55142324e-09, -0.847916007),
    ["--------------------"] = nil,
    ["Ice Mountain"] = CFrame.new(-415.797089, 230.62204, 485.993652, -0.951203406, -9.42748883e-08, 0.308564603, -8.24027566e-08, 1, 5.15065857e-08, -0.308564603, 2.35666633e-08, -0.951203406),
    ["Secret1"] = CFrame.new(123.518555, -7.37385178, 630.845032, 0.0814697519, 7.4923669e-08, 0.996675789, -1.28182638e-08, 1, -7.41257793e-08, -0.996675789, -6.73664458e-09, 0.0814697519),
    ["Secret2"] = CFrame.new(-92.2682343, 14.6014919, -312.274841, 0.258291036, 2.64963411e-08, -0.966067135, -1.99043946e-08, 1, 2.21053096e-08, 0.966067135, 1.35193785e-08, 0.258291036),
    ["Sky Island"] = CFrame.new(57.6084671, 346.170319, 348.660156, -0.0260010846, 2.62326605e-08, -0.999661922, -3.2673011e-08, 1, 2.70913532e-08, 0.999661922, 3.33663692e-08, -0.0260010846),
    ["Broken Bridge"] = CFrame.new(451.625885, 163.315277, 207.367813, -0.756038666, -2.54844643e-08, 0.654526949, -5.0771658e-09, 1, 3.30711067e-08, -0.654526949, 2.16798952e-08, -0.756038666),
    ["Spawn"] = CFrame.new(0, 5, 0),
    ["Sky"] = CFrame.new(Vector3.new(10000, 10000, 10000)),
    ["Void"] = CFrame.new(Vector3.new(0, -49980, 0)),
    ["Cofre"] = CFrame.new(595.460266, 153.338593, -99.6081314, -0.0882067308, -3.03222025e-09, 0.996102214, 5.92441829e-08, 1, 8.2902698e-09, -0.996102214, 5.97445151e-08, -0.0882067308),
    ["Hide"] = CFrame.new(542.482483, -7.35040379, 11.1989021, -0.997272909, -8.61511182e-08, 0.0738017634, -9.21108736e-08, 1, -7.73499096e-08, -0.0738017634, -8.39369179e-08, -0.997272909)
}
--sep                                                                  
-- # Verifications(Necessary)
var("Events").rb = nil

if not p("Revent", var("RS")) then 
    var("Events").rb = Instance.new("BindableEvent", var("RS"))
    var("Events").rb.Name = "Revent"
else 
    var("Events").rb = p("Revent", var("RS"))
end
--//
pcall(function()
    for _, i in pairs(var("p").PlayerGui.GameCorrectionsGui.TweenFrame:GetChildren()) do
        if i:IsA("Frame") and i.Name ~= "Flying" then
            i:Destroy()
        end
    end
end)
--//
if var("RS").GrabEvents:FindFirstChild("EndGrabEarly") then
    local rm = var("RS").GrabEvents.EndGrabEarly:Clone()
    rm.Parent = var("RS").GrabEvents.EndGrabEarly.Parent
    var("RS").GrabEvents.EndGrabEarly:Destroy()
end
--//
var("Toggle").Isowner = false
if var("p").Name == "Anti_Cheatbf" then var("Toggle").Isowner = true end
--sep                                                                  
-- # Functions(Back-End)

var("alins", {})

function Alpos(on, alv, pos)
    local alnp = nil
    local a2 = nil
    local a1 = nil
    local wl = nil
    
    if alv then
        if alv:IsA("Part") then
            wl = alv.AssemblyRootPart
        elseif alv.Character:FindFirstChild("HumanoidRootPart") then
            wl = alv.Character.HumanoidRootPart
        end
    end
    
    if not on and alv then
        if wl:FindFirstChild("apos") then  
            wl["apos"]:Destroy()
        end
        if MBP("HumanoidRootPart"):FindFirstChild("aln") then
            MBP("HumanoidRootPart"):FindFirstChild("aln"):Destroy()
        end
        return
    elseif not on and not alv and not pos then return end

    a2 = Instance.new("Attachment", wl)
    a2.Name = "apos"
    
    if not MBP("HumanoidRootPart"):FindFirstChild("aposm") then
        a1 = Instance.new("Attachment", MBP("HumanoidRootPart"))
        a1.Name = "aposm"
        a1.Position = pos
    else
        a1 = MBP("HumanoidRootPart"):FindFirstChild("aposm")
        a1.Position = pos
    end
    
    if not MBP("HumanoidRootPart"):FindFirstChild("aln") then
        alnp = Instance.new("AlignPosition", MBP("HumanoidRootPart"))
        alnp.Name = "aln"
        alnp.MaxAxesForce = Vector3.new(math.huge, math.huge, math.huge)
        alnp.MaxForce = math.huge
        alnp.RigidityEnabled = true 
        alnp.Responsiveness = 200
    else
        alnp = MBP("HumanoidRootPart"):FindFirstChild("aln")
        alnp.RigidityEnabled = true  
    end
    
    alnp.Attachment0 = a2
    alnp.Attachment1 = a1
    
    tm.Add(var("alins"), alnp)
    
    cm(a2, function()
        if alnp then alnp:Destroy() end
        tm.Remove(var("alins"), alnp)
    end)
    
    cm(alnp, function()
        tm.Remove(var("alins"), alnp)
    end)
end

function bringp(p, mode)
    if not p then return end
    dssetowner(p.Torso, 24, 3, function()
        task.spawn(function()
            repeat task.wait() until verify(p.Head) or not p
            p.Torso.CFrame = MBP("HumanoidRootPart").CFrame + (MBP("HumanoidRootPart").CFrame.LookVector * 5)
            p.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
        end)
    end)
    if mode ~= 2 then
        dgl(p.Torso)
    end
end

function UPDDrops()
    for _, dp in pairs(var("Drops")) do
        if dp and not dp.tg then
            dp:Refresh(GPNames(), true)  
        else
            dp:Refresh(mwtlist(nil, "Out"), true)
        end
    end
    for _, dp in pairs(var("DropsI")) do
        if dp then
            dp:Refresh(mwtlist(nil, "In"), true)
        end
    end
    for _, dp in pairs(var("DropsO")) do
        if dp then
            dp:Refresh(mwtlist(nil, "Out"), true)
        end
    end
end

var("invsconn", {})

function invpfunc()
    task.spawn(function()
        for _, i in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
            if i.Name ~= "CamPart" and i.Name ~= "HumanoidRootPart" and i:IsA("Part") or i:IsA("Accessory") then
                if i:IsA("Accessory") and i["Handle"].Transparency then
                    cm(i["Handle"], "PropertyChanged", "Transparency", function()
                        i["Handle"].Transparency = 0
                    end, "InvHandle_"..i["Handle"]:GetFullName())
                    i["Handle"].Transparency = 1
                    continue
                end
                cm(i, "PropertyChanged", "Transparency", function()
                    i.Transparency = 0
                end, "InvPart_"..i:GetFullName())
                i.Transparency = 1
            end
        end
    end)
end

invpfunc()

cm(var("p"), "CharacterAppearanceLoaded", invpfunc, "CharAppLoad")
--//
function nocol(model, grp)
    if not model or not grp then return end
    for _, i in pairs(model:GetChildren()) do
        if i:IsA("BasePart") then
            i.CollisionGroup = grp
        end
        task.wait()
    end
end
--//
var("paths").Toys = {}
var("paths").Toyp = var("p").PlayerGui.MenuGui.Menu.TabContents.Toys.Contents

task.spawn(function()
    for _, i in pairs(var("paths").Toyp:GetChildren()) do
        if i:IsA("Frame") then
            tm.Add(var("paths").Toys, i.Name)
        end
    end
end)

cm(var("paths").Toyp, "ChildAdded", function(i)
    if i:IsA("Frame") then
        tm.Add(var("paths").Toys, i.Name)
    end
end, "newtyfunc")

--//
var("Toggle").KillGrabTg = false

function KillGrabFunc()
    if not p("GrabParts", workspace) or not var("Toggle").KillGrabTg then return end
    local gp = wfc(p("GrabParts", workspace), "GrabPart")["WeldConstraint"].Part1
    pcall(function()
        if gp and gp.Parent["Humanoid"] then
            gp.Parent["Humanoid"].BreakJointsOnDeath = false
            gp.Parent["Humanoid"]:ChangeState(Enum.HumanoidStateType.Dead)
        end
        cm(gp.Parent["Humanoid"].Ragdolled, "PropertyChanged", "Value", function()
            if gp.Parent["Humanoid"].Ragdolled.Value == true then
                gp.Parent["Humanoid"]:ChangeState(Enum.HumanoidStateType.Dead)
            end
        end, "once")
    end)
end

KillGrabFunc()

cm(workspace, "ChildAdded", KillGrabFunc, "WCA")
--//
var("Toggle").NoClipGTg = false
var("Valores", "NCtble", {})

function NoClipGFunc()
    if not p("GrabParts", workspace) or not var("Toggle").NoClipGTg then return end
    local gp = wfc(p("GrabParts", workspace), "GrabPart")["WeldConstraint"].Part1
    if ippp(gp) then return end 
    
    pcall(function()
        if gp.Parent:FindFirstChild("Humanoid") then
            for _, i in pairs(gp.Parent:GetChildren()) do
                if p("RagdollLimbPart", i) then 
                    cm(i["RagdollLimbPart"], "PropertyChanged", "CanCollide", function()
                        i["RagdollLimbPart"].CanCollide = false
                    end, tostring(i["RagdollLimbPart"]:GetFullName()) .. "Conn")
                    i["RagdollLimbPart"].CanCollide = false
                end
                if i.Name == "Head" then
                    i.CanCollide = false
                end
            end
            
            if gp.Parent:FindFirstChild("Torso") then
                gp.Parent["HumanoidRootPart"].CanCollide = false
                cm(gp.Parent["Torso"], "PropertyChanged", "CanCollide", function()
                    gp.Parent["Torso"].CanCollide = false
                end, "TempCCConn")
                gp.Parent["Torso"].CanCollide = false
                
                repeat
                    task.wait()
                until not workspace:FindFirstChild("GrabParts") or not var("Toggle").NoClipGTg
                
                cm("TempCCConn", "disc")
                gp.Parent["Torso"].CanCollide = true
            end
            
            for _, i in pairs(gp.Parent:GetChildren()) do
                if p("RagdollLimbPart", i) then 
                    cm(tostring(i["RagdollLimbPart"]:GetFullName()) .. "Conn", "disc")
                end
            end
        else
            local model = gp:FindFirstAncestorWhichIsA("Model")
            if model then
                for _, i in pairs(model:GetChildren()) do
                    if i:IsA("BasePart") then
                        if i.CanCollide == true then
                            tm.Add(var("Valores").NCtble, i)
                            i.CanCollide = false
                        end
                    end
                end
                
                repeat
                    task.wait()
                until not workspace:FindFirstChild("GrabParts") or not var("Toggle").NoClipGTg
                
                for _, i in pairs(var("Valores").NCtble) do
                    i.CanCollide = true
                end
                tm.Clear(var("Valores").NCtble)
            end
        end
    end)
end

NoClipGFunc()

cm("WCA", "Add", NoClipGFunc)
--//
var("Toggle").InvsGrabTg = false
var("Toggle").OnAtg = false

function InvsGrabFunc()
    if not p("GrabParts", workspace) then return end
    local bp = p("GrabParts", workspace):FindFirstChild("BeamPart")
    local dp = p("GrabParts", workspace):FindFirstChild("DragPart")
    local gp = p("GrabParts", workspace):FindFirstChild("GrabPart")
    
    if bp and dp and gp and var("Toggle").InvsGrabTg then
        bp:Destroy()
        dp.Transparency = 1
        gp.Transparency = 1
        var("Remotes").cgl:FireServer()
        var("p").PlayerScripts.CharacterAndBeamMove.GrabNotifyEvent:Fire(false)

    elseif bp and dp and gp and not var("Toggle").InvsGrabTg and var("Toggle").OnAtg then
        bp:WaitForChild("GrabBeam").Attachment0 = MBP("Right Arm").RightGripAttachment
    end
end

cm(var("p"), "PropertyChanged", "CameraMode", function()
    if var("p").CameraMode == Enum.CameraMode.Classic then
        var("Toggle").OnAtg = true
    else
        var("Toggle").OnAtg = false
    end
    if p("GrabParts", workspace) then
        local bp = p("GrabParts", workspace):FindFirstChild("BeamPart")
        local dp = p("GrabParts", workspace):FindFirstChild("DragPart")
        local gp = p("GrabParts", workspace):FindFirstChild("GrabPart")
        
        if bp and dp and gp and not var("Toggle").InvsGrabTg and var("Toggle").OnAtg then
            bp:WaitForChild("GrabBeam").Attachment0 = MBP("Right Arm").RightGripAttachment
        elseif bp and dp and gp and not var("Toggle").InvsGrabTg and not var("Toggle").OnAtg then
            bp:WaitForChild("GrabBeam").Attachment0 = MBP("CamPart").Attachment
        end
    end
end)

cm("WCA", "Add", InvsGrabFunc)
--//
function SuperStrengthF()
    if not var("Toggle").SSTG then return end 
    if not p("GrabParts", workspace) then return end

    local gb = p("GrabParts", workspace)["GrabPart"]["WeldConstraint"].Part1
    if not gb then return end

    cm(p("GrabParts", workspace), function()
        local Uinput = var("UIS"):GetLastInputType()
        if Uinput ~= Enum.UserInputType.MouseButton2 then return end
        local Bvec = Instance.new("BodyVelocity", gb)

        Bvec.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        Bvec.Velocity = var("ov").cam.CFrame.LookVector * var("Valores").SSVal
        var("Debris"):AddItem(Bvec, 4)
        
        cm(var("RunS"), "Stepped", function()
            Bvec.Velocity = Bvec.Velocity * 1.01
        end, "SSStep")
        
        task.delay(4, function()
            cm("SSStep", "disc")
        end)
    end)
end

task.spawn(function()
    if p("GrabParts", workspace) then
        SuperStrengthF() 
    end
    cm("WCA", "Add", SuperStrengthF)
end)
--//
function TPme()
    if var("ov").mouse.Target then
        MBP("HumanoidRootPart").AssemblyLinearVelocity = Vector3.new(MBP("HumanoidRootPart").AssemblyLinearVelocity.X, 0, MBP("HumanoidRootPart").AssemblyLinearVelocity.Z)
        tp(var("ov").mouse.Hit.Position)
    end
end
--//
var("Valores", "ALpos", {})
var("Toggle").AntiLoopTg = false

var("Valores").ALpos.X = -535
var("Valores").ALpos.Y =   20
var("Valores").ALpos.Z = -167

function AntiLoopF()
    if not var("Toggle").AntiLoopTg then return end
    MBP("HumanoidRootPart").CFrame = CFrame.new(var("Valores").ALpos.X, var("Valores").ALpos.Y, var("Valores").ALpos.Z)
end

cm("CharAppLoad", "Add", AntiLoopF)
--//
function gpd()
    for _, plr in ipairs(var("ps"):GetPlayers()) do
        if plr == var("p") then continue end
        if not plr.Character then continue end

        if p("GrabParts", plr.Character) then
            p("GrabParts", plr.Character):Destroy()
        end
    end
end

function AntiLagF(val)
    p("CharacterAndBeamMove", var("p").PlayerScripts).Disabled = val
    if p("CharacterAndBeamMove", var("p").PlayerScripts).Disabled then
        tasak.spawn(function()
            for _, t in ipairs(MBP("Humanoid").Animator:GetPlayingAnimationTracks()) do
                if t.Animation.AnimationId:find("8194033993") then
                    t:Stop()
                end
            end
            gpd()
        end)
    end
end
--//
local AntiGrabDrp = nil
var("Toggle").AntiLag = false
local tmr, gtmr = 0, 0

cm(game:GetService("ReplicatedStorage").GrabEvents.CreateGrabLine, "OnClientEvent", function(plr)
    local name = typeof(plr) == "Instance" and plr.DisplayName or tostring(plr)
    var("Vars", "gcach" .. name, (var("gcach" .. name) or 0) + 1)
end, "grbspm")

cm(var("RunS"), "RenderStepped", function(dt)
	if fps() < 10 then
		tmr += dt
		if tmr >= 0.2 and not var("Toggle").AntiLag then
			if AntiGrabDrp then AntiGrabDrp:Set("Lag", true) else AntiLagF(true) end
			local tpplr, tcnt = nil, 0
			for k, v in pairs(var("Vars", "ref")) do
				if k:find("gcach") and v > tcnt then tpplr, tcnt = k:gsub("gcach", ""), v end
			end
			if tpplr then
				notify("Lag Detected!", tpplr .. " is causing lag", nil, 8)
			else
				notify("Note!", "Lag Detected, Anti Lag Enabled")
			end
		end
	else
		tmr = 0
	end

	gtmr += dt
	if gtmr >= 1 then
		gtmr = 0
		for k in pairs(var("Vars", "ref")) do
			if k:find("gcach") then var("Vars", "ref")[k] = nil end
		end
	end
end, "AutoAntiLag")
--//
var("Toggle").SpeedCFTg = false
var("Valores").SpeedMltVal = 0.01

cm(var("RunS"), "RenderStepped", function()
    if var("Toggle").SpeedCFTg  then
        MBP("HumanoidRootPart").CFrame += MBP("Humanoid").MoveDirection * var("Valores").SpeedMltVal
    end
end, "SpeedCF")
--//
var("Toggle").InfJumpTg = false

var("UIS").JumpRequest:Connect(function()
    if var("Toggle").InfJumpTg and MBP("Humanoid") and MBP("Humanoid"):GetState() == Enum.HumanoidStateType.Freefall then
        MBP("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

--//
var("Toggle").AntiExpTG = false
var("Toggle").AntiGbTG = false
var("paths").held = var("p")["IsHeld"]

function M6DConns()
    for _, i in pairs(MBP("Torso"):GetChildren()) do
        if i:IsA("Motor6D") then
            cm(i, "PropertyChanged", "Enabled", function()
                if var("Toggle").AntiExpTG or var("Toggle").AntiGbTG then 
                    i.Enabled = true
                end
            end, tostring(i:GetFullName() .. "Conn"))
        end
    end
end

task.spawn(M6DConns)

var("Toggle").SitConnExp = false    

function pchanged()
    cm(MBP("Humanoid"), "PropertyChanged", "Sit", function()
        if not var("Toggle").Autott then
            if not MBP("Humanoid").SeatPart and ((var("Toggle").AntiExpTG and var("Toggle").SitConnExp) or var("Toggle").AntiGbTG) and not timer("stlim") then
                MBP("Humanoid"):ChangeState(Enum.HumanoidStateType.GettingUp)
                MBP("Humanoid").Sit = false
            end
        end
    end, "HMSitConn")

    cm(MBP("Humanoid"), "PropertyChanged", "AutoRotate", function()
        if not MBP("Humanoid").AutoRotate and (var("Toggle").AntiExpTG or var("Toggle").AntiGbTG) then
            MBP("Humanoid").AutoRotate = true
        end
    end, "ARoConn")

    cm(MBP("Humanoid").Ragdolled, "PropertyChanged", "Value", function()
        if var("Toggle").AntiGbTG then

            MBP("Humanoid").Ragdolled.Value = false
        end
    end, "Rgdoll")

    cm(MBP("Humanoid").Animator, "AnimationPlayed", function(track)
        if track.Animation.AnimationId == "rbxassetid://7047322890" and var("Toggle").AntiGbTG then
            track:Stop()
        end
    end, "Animatorcnn")

    cm(MBP("HumanoidRootPart"), "PropertyChanged", "Massless", function()
        if MBP("HumanoidRootPart").Massless == true and MBP("Humanoid").SeatPart == nil or MBP("Humanoid").Sit == false then
            MBP("HumanoidRootPart").Massless = false
        end
    end, "MSlessCOnn")

    cm(MBP("HumanoidRootPart"), "PropertyChanged", "CollisionGroup", function()
        AntiFling()
    end, "CGSlessCOnn")

    cm(MBP("Humanoid"), "PropertyChanged", "SeatPart", function()
        if not MBP("Humanoid").SeatPart then return end
        if not MBP("Humanoid").SeatPart.Parent.Name == "CreautreBlobman" then return end
        local mdl = MBP("Humanoid").SeatPart.Parent
        local a = false

        if mdl.HumanoidRootPart.CollisionGroup ~= "PlotItems" then
            nocol(mdl, "PlotItems")
        else
            a = true
        end
        task.spawn(function()
            repeat task.wait() until MBP("Humanoid").SeatPart == nil
            if mdl.Parent ~= nil and not a then
                nocol(mdl, "Items")
            end
        end)
    end, "SPConn")
end

pchanged()

task.spawn(function()
    cm("WCA", "Add", function(i)
        if i.Name == "Part" and IsAround(i, 18) and var("Toggle").AntiExpTG then
            task.spawn(function()
                var("Toggle").SitConnExp = true
                MBP("Humanoid").Sit = not MBP("Humanoid").Sit
                MBP("HumanoidRootPart").Anchored = true
                MBP("Humanoid").Ragdolled.Value = false
                var("CAS"):UnbindAction("JumpRemover")
                MBP("HumanoidRootPart").Anchored = false
                MBP("Humanoid").AutoRotate = true
                cm(i, function()
                    var("Toggle").SitConnExp = false
                end)
            end)
        end
    end)
end)

cm("CharAppLoad", "Add", function()
    task.spawn(M6DConns)
end)

cm(game:GetService("RunService"), "RenderStepped", function()
    if MBP("HumanoidRootPart") and MBP("HumanoidRootPart").ReceiveAge ~= 0 and var("Toggle").AntiGbTG then
        MBP("HumanoidRootPart").Anchored = true
        var("paths").held.Value = false
        task.spawn(function()
            var("CE").Struggle:FireServer()
            var("CE").RagdollRemote:FireServer(MBP("HumanoidRootPart"), 0)
        end)
        var("CS"):UnbindAction("JumpRemover")
        MBP("Humanoid").AutoRotate = true
        MBP("HumanoidRootPart").RootJoint.Enabled = true
        MBP("HumanoidRootPart").Anchored = false
    elseif MBP("HumanoidRootPart").ReceiveAge == 0 then
        MBP("HumanoidRootPart").Anchored = false
    end
end, "Ag")
--//
var("Toggle").AntiFireTG = false

function AntiFireFunc()
    local CB = MBP("HumanoidRootPart").FirePlayerPart:WaitForChild("CanBurn", 5)
    if not CB then return end    
    
    cm(CB, "PropertyChanged", "Value", function()
        if CB.Value == true and var("Toggle").AntiFireTG then
            firetouchinterest(MBP("HumanoidRootPart").FirePlayerPart, workspace.Map.Hole.PoisonSmallHole.ExtinguishPart, 0)
        end
    end, "AntiFireConn")
end

cm(MBP("Humanoid"), "Died", function()
        cm("AntiFireConn", "disc")
        cm("HMSitConn", "disc")
end, "HmDied")

task.spawn(AntiFireFunc)

cm("CharAppLoad", "Add", function()
    pchanged()
    AntiFling()
    task.spawn(M6DConns)
    AntiFireFunc()
end)
--//
var("Toggle").AntiVoidTG = false

cm(var("RunS"), "Heartbeat", function()
    if MBP("HumanoidRootPart") and MBP("HumanoidRootPart").Position.Y <= -300 and var("Toggle").AntiVoidTG then
        if var("Lists").Loc2["Spawn"] then
            MBP("HumanoidRootPart").AssemblyLinearVelocity = Vector3.zero
            MBP("HumanoidRootPart").CFrame = var("Lists").Loc2["Spawn"] * CFrame.new(0, 10, 0)
        end
    end
end, "AntiVoidConn")
--//
var("Toggle").pcld = false

cm(var("Events").rb, "Event", function()
    task.spawn(function()
        timer("saferes", 5)
    end)

    notify("Event!", "Safe Reset(Beta) Fired!", "rbxassetid://7734056411")
    local safe = false
    cm(MBP("Humanoid"), "Died", function()
        safe = true
    end, "Once","HmDied")
    if not var("Toggle").pcld then
        var("ov").char:BreakJoints()
    else
        repeat
            task.spawn(function()
                var("Remotes").strg:FireServer()
            end)
            var("ov").char:BreakJoints()
            task.wait()
        until safe or not timer("saferes")
    end
    if var("Toggle").pcld then
        MBP("HumanoidRootPart").CFrame = CFrame.new(4000, 10e10, 0)
    end
    if var("Toggle").pcld then
        var("p").CharacterAppearanceLoaded:Wait()
        var("ov").char:BreakJoints()
    end
end, "SafeReset")

var("SG"):SetCore("ResetButtonCallback", var("Events").rb) 
--//
var("paths").hole = workspace.Map.FactoryIsland.PoisonContainer.PoisonHurtPart

function pfunc(plr)
    if not plr then return end
    if not plr.Character and plr.Character.Parent ~= workspace then 
        notify("Note", plr.DisplayName .. " In House!")
        return 
    end
    local cf = plr.Character.HumanoidRootPart.CFrame
    dssetowner(plr.Character.Head, nil , 3)
    repeat task.wait() until verify(plr.Character.Head, 2)
    var("paths").hole.CanTouch = true
    plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
    local lh = plr.Character.Humanoid.Health

    plr.Character.HumanoidRootPart.CFrame = var("paths").hole.CFrame * CFrame.new(0, 2, 0)
    plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(10, 0, 0)

    cm(plr.Character.Humanoid, "PropertyChanged", "Health", function()
        local ch = plr.Character.Humanoid.Health
    
        if ch < lh then
            var("paths").hole.CanTouch = false
            task.wait(ping()/1000)
            plr.Character.HumanoidRootPart.CFrame = cf
            plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            dgl(plr.Character.Head)
            var("paths").hole.CanTouch = true
            cm("HmPCh", "disc")
        end
    
        lh = ch
    end, "HmPCh")
    task.delay(3, function() 
        if verify(plr.Character.Head, 2) then
            plr.Character.HumanoidRootPart.CFrame = cf
            var("paths").hole.CanTouch = true
            dgl(plr.Character.Head)
            cm("HmPCh", "disc")
        end
    end)
end
--//
var("Valores").fillc = Color3.fromRGB(255, 255, 255)
var("Valores").outc  = Color3.fromRGB(0, 0, 0)
var("Valores").fillt = 0.7
var("Valores").outt  = 0
var("Valores").depth = Enum.HighlightDepthMode.AlwaysOnTop

var("paths").Hlights = {}

function hglt(part, del)
    if not part then return end
    local hg = nil
    del   = del or false

    if part:FindFirstChildWhichIsA("Highlight") then
        hg = part:FindFirstChildWhichIsA("Highlight") 
    elseif not del then
        hg = Instance.new("Highlight", part)
    end
    if hg and del then
        hg:Destroy()
        tm.Remove(var("paths").Hlights, hg)
    end

    tm.Add(var("paths").Hlights, hg)
    hg.FillColor           = var("Valores").fillc
    hg.OutlineColor        = var("Valores").outc
    hg.FillTransparency    = var("Valores").fillt
    hg.OutlineTransparency = var("Valores").outt
    hg.DepthMode           = var("Valores").depth
end

function updall() 
    for _, hg in pairs(var("paths").Hlights) do 
        if hg and hg.Parent then 
            hg.FillColor = var("Valores").fillc 
            hg.OutlineColor = var("Valores").outc 
            hg.FillTransparency = var("Valores").fillt 
            hg.OutlineTransparency = var("Valores").outt 
            hg.DepthMode = var("Valores").depth 
        end 
    end 
end

var("paths").PlrsWH = {}
var("Toggle").TPHigh = false
var("Valores").OnAur = {}

task.spawn(function()
---@diagnostic disable-next-line: undefined-global
    function hndl(plr)
        cm(plr, "CharacterAppearanceLoaded", function()
            if tm.Find(var("paths").PlrsWH, plr) then
                hglt(plr.Character, false)
            elseif var("Toggle").TPHigh then
                hglt(plr.Character, false) 
            else
                hglt(plr.Character, true) 
            end
        end, plr.Name .. "Conn")
    end
    for _, i in pairs(var("ps"):GetPlayers()) do
        hndl(i)
    end

    cm(var("ps"), "PlayerAdded", function(i)
        hndl(i)
    end, "plradded")
end)

function setp(plr, on)
    if not plr and not on then 
        var("Toggle").TPHigh = on
        for _, hg in pairs(var("paths").Hlights) do 
            if hg and hg.Parent then 
                hg:Destroy()
            end
        end
        return
    elseif not plr and on then
        var("Toggle").TPHigh = on
        for _, i in pairs(var("ps"):GetPlayers()) do
            if i.Character then
                hglt(i.Character)
            end
        end
        return
    end
    hglt(plr.Character, not on)
end
--//
var("Toggle").PCLDESP = false
var("paths").SelBoxs = {}

task.spawn(function()
    for _, i in pairs(workspace:GetChildren()) do
        if i:FindFirstChild("selbox") and i.Name == "PlayerCharacterLocationDetector" then
            i:FindFirstChild("selbox"):Destroy()
        end

        if not i:FindFirstChild("selbox") and i.Name == "PlayerCharacterLocationDetector" then
            local u = Instance.new("SelectionBox", i)
            u.Name = "selbox"
            u.Adornee = i
            u.LineThickness = 0.03
            u.Color3 = Color3.fromRGB(255, 0, 0)
            u.Visible = var("Toggle").PCLDESP
            tm.Add(var("paths").SelBoxs, u)
        end
    end
end)

cm(workspace, "ChildAdded", function(i)
    if i.Name == "PlayerCharacterLocationDetector" then
        local u = Instance.new("SelectionBox", i)
        u.Name = "selbox"
        u.Adornee = i
        u.LineThickness = 0.03
        u.Color3 = Color3.fromRGB(255, 0, 0)
        u.Visible = var("Toggle").PCLDESP
        tm.Add(var("paths").SelBoxs, u)
    end
end, "PCLDrConn")
--//
cm(var("ov").inv, "ChildAdded", function(m)
    if m.Name ~= "BallSnowball" then return end
    if var("Toggle").Dstyed then
        var("Toggle").Dstyed = false
    end
    if var("Toggle").RagdollVal then
        timer("snbllch", 2)
        repeat task.wait() until not timer("snbllch") or not var("Toggle").RagdollVal
        if var("Toggle").RagdollVal and not var("Toggle").Dstyed then
            var("Remotes").dt:FireServer(inpast("BallSnowball"))
            var("Toggle").SragdollRn = false
        end
    end
end)

function Sragdoll(plr)
    if not plr then return end
    if var("Toggle").SragdollRn == true then return end
    
    if not tm.Find(var("paths").Toys, "BallSnowball") then
        notify("Note", "You Dont Have SnowBall", "rbxassetid://7733911828")
    end

    if not inpast("BallSnowball") then SICF("BallSnowball", nil, "Head") end
    local i = 0

    repeat 
        task.wait()
    until inpast("BallSnowball")

    cm(p("SoundPart", ball), function()
        var("Toggle").Dstyed = true
    end)

    local ball = inpast("BallSnowball")

    cm(p("SoundPart", ball), "ChildAdded", function()
        p("SoundPart", ball).CFrame = CFrame.new(plr.Character.HumanoidRootPart.Position)
        p("SoundPart", ball).AssemblyLinearVelocity = Vector3.new(0, -300, 0)
        var("Toggle").SragdollRn = false
    end, "once")

    repeat
        i += 1
        local sp = ball and ball:FindFirstChild("SoundPart")
        if sp then
            setowner(sp, "Safe")
        end
    
        task.wait(0.01)
    until i == 10 or not soundPart or var("Toggle").SragdollRn == false
end
--//
function KillP(plr)
    if not plr then return end
    if not plr.Character or plr.Character.Parent ~= workspace then return end
    if wfc("Humanoid", plr.Character, 1):GetState() == Enum.HumanoidStateType.Dead then return end
    timer("KillP", 5)
    if not verify(wfc("Head", plr.Character, 1), 2) then
        dssetowner(wfc("HumanoidRootPart", plr.Character, 1), 24, 2)
    end

    repeat 
        task.wait()
    until verify(wfc("Head", plr.Character, 1), 2) or not timer("KillP")

    if verify(wfc("Head", plr.Character, 1), 2) then
        task.spawn(function()
            for i=1, 3 do 
                dgl(wfc("HumanoidRootPart", plr.Character, 1))
            end
        end)
        wfc("Humanoid", plr.Character, 1).BreakJointsOnDeath = false
        wfc("Humanoid", plr.Character, 1):ChangeState(Enum.HumanoidStateType.Dead)
        wfc("HumanoidRootPart", plr.Character, 1).CFrame = CFrame.new(0, 1e20, 0)
    end
end
--//
cm(var("p").PlayerGui.ControlsGui.ToggleControlsGuiVisibility, "Event", function(m)
    if not m and timer("gccrandmnm") then
        repeat task.wait() until not MBP("Humanoid").Sit == true
        task.wait(0.04)
        var("CAS"):UnbindAction("RightGrab")
        var("CAS"):UnbindAction("LeftGrab")
        task.wait()
        MBP("Humanoid").Parent.GrabbingScript.ToggleMobileButtonVisibility:Fire(true)
        var("p").PlayerGui.ControlsGui.ToggleControlsGuiVisibility:Fire(true)
    end
end, "gcctgcnn")

function pins(item, rm)
    task.spawn(function()
        if not item then return end
        local ptPart = item:WaitForChild("HumanoidRootPart", 1)
        if rm then
            if ptPart:FindFirstChild("velup") then
                ptPart.velup:Destroy()
            end
            return
        end
        local bv = Instance.new("BodyVelocity")
        bv.Name = "velup"
        bv.Velocity = Vector3.new(0, 99e20, 0)
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Parent = ptPart
    end)
end

var("Toggle").BKgc = true
if not _G.Brkhs then
    _G.Brkhs = false
end

function gtcplot()
    if var("p").InPlot.Value then
        local nms = {
            ["Green House"]   = "Plot1",

            ["Red House"]     = "Plot2",
            ["Purple House"]  = "Plot3",
            ["Blue House"]    = "Plot4",
            ["Chinese House"] = "Plot5"
        }
    
        for nm, cf in pairs(var("Lists").Loc2) do
            if nms[nm] then
                if (MBP("HumanoidRootPart").Position - cf.Position).Magnitude < 150 then
                    return workspace["PlotItems"][nms[nm]]
                end
            end
        end
    end
end

function GucciAnti()
    local hum = MBP("Humanoid")
    local hrp = MBP("HumanoidRootPart")
    if not hum or not hrp then return end
    var("Toggle").tGucciAnti = true 
    task.spawn(function()
        var("Remotes").strg:FireServer()
    end)
    timer("gccrandmnm", 5)
    timer("gucciprep", 3)
    local fl
    if var("p").InOwnedPlot.Value then
        fl = gtcplot()
    end
    function spwn(mode)
        if mode then
            if fl then
                pins(fl:FindFirstChild("CreatureBlobman"), true)
                fl:FindFirstChild("CreatureBlobman").HumanoidRootPart.Anchored = true
                fl:FindFirstChild("CreatureBlobman").HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
                fl:FindFirstChild("CreatureBlobman").HumanoidRootPart.CFrame = MBP("HumanoidRootPart").CFrame + (MBP("HumanoidRootPart").CFrame.LookVector * 5)
                fl:FindFirstChild("CreatureBlobman").HumanoidRootPart.Anchored = false
                task.wait(ping()/900)
                return
            end
            var("RS").MenuToys.DestroyToy:FireServer(inpast("CreatureBlobman"))
            task.wait(ping()/900)
            spwn()
            return
        end
        if var("ov").char.Parent == workspace then
            task.spawn(function()
                var("RS").MenuToys.SpawnToyRemoteFunction:InvokeServer("CreatureBlobman", hrp.CFrame * CFrame.new(0, 1000, 0), Vector3.new(0, 0, 0))
            end)
        else
            if not var("p").InOwnedPlot.Value then
                if _G.Brkhs then
                    local h = MBP("HumanoidRootPart").CFrame
                    
                    MBP("HumanoidRootPart").CFrame = CFrame.new(0, 10, 0)
                    repeat task.wait() until var("p").InPlot.Value == false
                    
                    SICF("CreatureBlobman", h)
                    onhs = true
                    MBP("HumanoidRootPart").CFrame = h
                else
                    notify("Note!", "Need To break barrier.")
                end
            else
                SICF("CreatureBlobman", nil, "Front")
            end

        end
        task.wait(ping()/900)
    end

    MBP("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
    if fl then
        if not fl:FindFirstChild("CreatureBlobman") then
            spwn()
        else 
            spwn(true)
        end
    else
        if not inpast("CreatureBlobman") then
            spwn()
        else 
            spwn(true)
        end
    end
    var("Valores").autoguccipos = hrp.CFrame

    local blb
    if not fl then
        repeat task.wait() until inpast("CreatureBlobman") or not timer("gucciprep")
        blb = inpast("CreatureBlobman")
    else
        print("AA")
        print(fl)
        repeat task.wait() until fl:FindFirstChild("CreatureBlobman") or not timer("gucciprep")
        print("BB")
        blb = fl:FindFirstChild("CreatureBlobman")
    end
    if not blb then return end
    task.spawn(function()
        repeat
            task.wait()
        until not timer("gucciprep")
        var("Toggle").tGucciAnti = false
        var("Toggle").Autott = false
    end)
    local seat
    repeat task.wait() until p("VehicleSeat", blb) or not timer("gucciprep")
    seat = p("VehicleSeat", blb)
    if blb.Parent == nil or seat.Parent == nil then return end
    task.spawn(function()
        timer("Guccitmr", 2)
    end)
    repeat 
        if blb.Parent == nil or not seat.Parent then break end
        task.spawn(function()
            seat:Sit(hum)
        end)
        
        if fps() >= 180 then task.wait(0.02) else task.wait(0.04) end
    until seat.Occupant == MBP("Humanoid") and blb or not seat.Parent == nil or not timer("Guccitmr")
    var("Toggle").Autott = true
    var("Toggle").tGucciAnti = false
    var("Toggle").Autott = false
    pins(blb)

    task.spawn(function()
        if var("Valores").autoguccipos and hrp then
            MBP("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)
            hrp.CFrame = var("Valores").autoguccipos
            task.spawn(function()
                cm(seat, "PropertyChanged", "Occupant", function()
                    if seat.Occupant ~= nil and seat.Occupant.Parent ~= var("ov").char then
                        notify("Gucci Note!", var("ps"):GetPlayerFromCharacter(seat.Occupant.Parent).DisplayName .. " Seated on your blob")
                    end
                end, "gccsoc")
            end)
            task.spawn(function()
                repeat task.wait() until blb.Parent ~= var("ov").inv
                if not timer("stlim") then
                    notify("Gucci Note!", "Blob Got Destroyed")
                    if var("Toggle").BKgc then
                        brkowngc()
                    end
                end
                cm("gccsocv", "disc")
            end)
        end
    end)
end
--//
function breakgucci(plr)
    var("paths").breakgccpos = MBP("HumanoidRootPart").CFrame
    for _, i in pairs(workspace[plr.Name .. "SpawnedInToys"]:GetChildren()) do
        if i.Name == "CreatureBlobman" or i.Name == "TractorGreen" then
            if i.Name == "TractorGreen" then
                plr.Character.HumanoidRootPart.Massless = false
                task.wait(0.2)
                MBP("HumanoidRootPart").CFrame = plr.Character.HumanoidRootPart.CFrame
                task.wait(ping()/1000)
                setowner(plr.Character.HumanoidRootPart)
                task.wait(ping()/1000)
            else
                if not IsAround(plr.Character.HumanoidRootPart, 30) then
                    MBP("HumanoidRootPart").CFrame = plr.Character.HumanoidRootPart.CFrame
                    task.wait(ping()/1000)
                end
                setowner(plr.Character.HumanoidRootPart)
            end
            if p("VehicleSeat", i, 2) then
                local st = p("VehicleSeat", i, 2)
                task.spawn(function()
                    st:Sit(MBP("Humanoid"))
                end)
                task.spawn(function()
                    MBP("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)
                end)
   
                task.wait(ping()/2000)
            end
        end
    end
    task.wait(ping()/900)
    MBP("HumanoidRootPart").CFrame = var("paths").breakgccpos
    var("paths").breakgccpos = nil
    if MBP("Humanoid").SeatPart then
        repeat
            task.wait()
            task.spawn(function()
                MBP("Humanoid").Sit = true
                task.wait(ping()/1000)
                MBP("Humanoid").Sit = false
            end)
        until not MBP("Humanoid").SeatPart
    end

    var("CAS"):UnbindAction("RightGrab")
    var("CAS"):UnbindAction("LeftGrab")
    task.wait()
    MBP("Humanoid").Parent:WaitForChild("GrabbingScript"):WaitForChild("ToggleMobileButtonVisibility"):Fire(true)
    var("p"):WaitForChild("PlayerGui"):WaitForChild("ControlsGui"):WaitForChild("ToggleControlsGuiVisibility"):Fire(true)
end
--//
var("Toggle").AntiBlob = false

function AntiBlob()
---@diagnostic disable-next-line: undefined-global
    function hdl(blb)
        if blb.Parent == nil then return end 
        if not p("LeftDetector", blb) or not p("RightDetector", blb) then return end
        local lw = p("LeftWeld", p("LeftDetector", blb))
        local lo = p("LeftAlignOrientation", p("LeftDetector", blb))
        local rw = p("RightWeld", p("RightDetector", blb))
        local ro = p("RightAlignOrientation", p("RightDetector", blb))
        
        cm(lw, "PropertyChanged", "Attachment0", function()
            if lw.Attachment0.Parent == MBP("HumanoidRootPart") or lw.Attachment0.Parent == nil and var("Toggle").AntiBlob then
                lw.Attachment0 = nil
            end
        end)

        cm(lo, "PropertyChanged", "Attachment0", function()
            if lo.Attachment0.Parent == MBP("HumanoidRootPart") or lo.Attachment0.Parent == nil and var("Toggle").AntiBlob then
                lo.Attachment0 = nil
            end
        end)

        cm(rw, "PropertyChanged", "Attachment0", function()
            if (rw.Attachment0.Parent == MBP("HumanoidRootPart") or rw.Attachment0.Parent == nil) and var("Toggle").AntiBlob then
                rw.Attachment0 = nil
            end
        end)

        cm(ro, "PropertyChanged", "Attachment0", function()
            if (ro.Attachment0.Parent == MBP("HumanoidRootPart") or ro.Attachment0.Parent == nil) and var("Toggle").AntiBlob then
                ro.Attachment0 = nil
            end
        end)
    end
---@diagnostic disable-next-line: undefined-global
    function hdlp(pst)
        for _, u in pairs(pst:GetChildren()) do
            if u.Name == "CreatureBlobman" then
                hdl(u)
            end
        end

        cm(pst, "ChildAdded", function(i)
            if i.Name == "CreatureBlobman" then
                task.wait(0.3)
                hdl(i)
            end
        end, pst.Name .. "Conn")
    end
    
    for _, i in pairs(var("ps"):GetPlayers()) do
        if workspace[i.Name .. "SpawnedInToys"] then
            hdlp(workspace[i.Name .. "SpawnedInToys"])
        end
    end

    cm("plradded", "Add", function(i)
        task.wait(0.2)
        hdlp(workspace[i.Name .. "SpawnedInToys"])
    end)
end

task.spawn(function()
    AntiBlob()
end)

cm("CharAppLoad", "Add", function()
    task.wait(0.8)
    MBP("HumanoidRootPart").RootAttachment:Destroy()
end)
--//
var("Toggle").AntiDsync = true
task.spawn(function()
    for _, i in pairs(var("ps"):GetPlayers()) do
        if i.Character and p("HumanoidRootPart", i.Character) and i ~= var("p") then
            cm(p("HumanoidRootPart", i.Character), "PropertyChanged", "Massless", function()
                if  var("Toggle").AntiDsync then
                    p("HumanoidRootPart", i.Character).Massless = false
                end
            end, i.Name .. "syncconn")
            p("HumanoidRootPart", i.Character).Massless = true
            p("HumanoidRootPart", i.Character).Massless = false
        end
    end
end)

cm("plradded", "Add", function(i)
    task.wait(0.2)
    i.CharacterAppearanceLoaded:Wait()
    cm(p("HumanoidRootPart", i.Character), "PropertyChanged", "Massless", function()
        if p("HumanoidRootPart", i.Character).Massless == true and var("Toggle").AntiDsync then
            p("HumanoidRootPart", i.Character).Massless = false
        end
    end, i.Name .. "syncconn")
    
    p("HumanoidRootPart", i.Character).Massless = true
    p("HumanoidRootPart", i.Character).Massless = false
end)
--//
function bring()
    local part = FINF()
    if not part then return end
    
    if ippp(part) then 
        return 
    end
    
    if p("Humanoid", part:FindFirstAncestorWhichIsA("Model")) then
        bringp(part:FindFirstAncestorWhichIsA("Model"))
    else
        dssetowner(part, nil, 3)
        repeat task.wait() until verify(part, 2)
        part.AssemblyRootPart.CFrame = MBP("HumanoidRootPart").CFrame + MBP("HumanoidRootPart").CFrame.LookVector * 5
        dgl(part.AssemblyRootPart)
    end
end
--//
var("Valores").Ttle = 1
local lpd2 = nil

function lkick(on, p)
    if not p then return end
    var("Toggle").lkick = on
    for i = 1, 6 do cm("kickconn"..i, "disc") end
    cm("kickconn", "disc")

    if not on then
        if p.Character and p.Character.HumanoidRootPart then Alpos(false, p) end
        return
    end
    if p.Character and p.Character.Parent ~= workspace then
        notify("Note", p.DisplayName .. " in House!")
        var("Toggle").Ddnote = true
        repeat task.wait() until not p.Character or p.Character.Parent == workspace or not on
    end
    task.spawn(function()
        repeat
            if p.Character and p.Character.HumanoidRootPart then
                FMC(15, function(m)
                    if m.Name == "NinjaKunai" or m.Name == "NinjaShuriken" and m.Parent ~= var("ov").inv then
                        local sp = m:FindFirstChild("SoundPart")
                        if sp then setowner(sp, "Safe") end
                    end
                end, p.Character.HumanoidRootPart)
            end
            task.wait(0.1)
        until not var("Toggle").lkick or not p
    end)
    if not on then Alpos(on, p) end
    var("Toggle").lkick2 = true
    Alpos(true, p, Vector3.new(0, 20, 0))
    task.wait(0.1) 
    bringp(p.Character, 2)
    task.wait(0.5) 
    dgl(p.Character.Torso)
    task.wait(0.1)
    if not on then Alpos(on, p) end

    local ly, brgg = nil, false

    function hdl()
        if not var("Toggle").lkick then
            Alpos(false, p)
            for _, i in pairs(MBP("HumanoidRootPart"):GetChildren()) do if i.Name == "aln" then i:Destroy() end end
            cm("kickconn", "disc") for i = 1, 6 do cm("kickconn"..i, "disc") end return
        elseif not var("ps"):FindFirstChild(p.Name) and lpd2 then
            notify("Note!", p.DisplayName .. " Kicked, Turning Off")
            lpd2:Set(false) cm("kickconn", "disc") for i = 1, 6 do cm("kickconn"..i, "disc") end return
        end
        local char = p.Character
        if not char or char.Parent ~= workspace then
            if not var("Toggle").Ddnote then var("Toggle").Ddnote = true notify("Note", p.DisplayName .. " in House!") end
            return
        end
        if char.Humanoid and char.Humanoid:GetState() == Enum.HumanoidStateType.Dead or not char.Torso then return end
        if not char.HumanoidRootPart:FindFirstChild("apos") then 
            Alpos(true, p, Vector3.new(0, 20, 0)) 
            var("Toggle").lkick2 = true 
            return 
        end
        if brgg then return end
        if IsAround(char.Torso, 30) then
            var("Toggle").Brngng = false setowner(char.Torso) dgl(char.Torso)
        elseif not var("Toggle").Brngng then
            bringp(char, 2) var("Toggle").Brngng = true
        end
    end

    cm(var("RunS"), "RenderStepped", function()
        if not var("Toggle").tmrrr then var("Valores").Ttle = fps() >= 400 and 2 or fps() >= 200 and 1 or 0 end
        if ping() > 500 then return end
        if fps() <= 60 then
            if cm("kickconn2", "status") == "none" then
                for i = 2, 5 do 
                    cm(var("RunS"), "RenderStepped", hdl, "kickconn"..i) 
                end
                cm(var("RunS"), "Stepped", hdl, "kickconn6")
            end
        else
            for i = 2, 6 do 
                cm("kickconn"..i, "disc") 
            end
        end
        local cy = p.Character and p.Character.HumanoidRootPart and p.Character.HumanoidRootPart.Position.Y
        if not cy then return end
        if ly and not brgg and math.abs(cy - ly) > 5 then
            brgg = true 
            bringp(p.Character, 2)
            task.delay(0.5, function() var("Toggle").tmrrr = true brgg = false task.wait(1) var("Toggle").tmrrr = false end)
        end
        ly = cy
        hdl()
    end, {ref = var("Valores"), key = "Ttle"}, "kickconn")
end
--//
var("Toggle").AntBnn = false
var("Valores").Bnns = {}

function hdlb(i)
    task.spawn(function()
        if not var("Toggle").AntBnn then
            tm.Add(var("Valores").Bnns, i)
            return
        end

        local hp = p("HoldPart", i, 2)
        if not hp then return end
        local gr = p("HoldItemRemoteFunction", hp)
        local dr = p("DropItemRemoteFunction", hp)
        if p("RigidConstraint", hp, 2).Attachment1 then 
            repeat task.wait() until not p("RigidConstraint", hp).Attachment1 or i.Parent == nil
        end
        
        if dr and gr then
            repeat 
                task.wait()
                task.spawn(function()
                    gr:InvokeServer(i, var("ov").char)
                end)
                dr:InvokeServer(i, CFrame.new(0, -51000, 0), Vector3.zero)
            until i.Parent == nil or not var("Toggle").AntBnn
            if i.Parent ~= nil then 
                tm.Add(var("Valores").Bnns, i)
            end
        end
    end)
end

function AntiBanana() 
    function hdlp(pst, mode)
        for _, i in pairs(pst:GetChildren()) do
            if i.Name ~= "FoodBanana" then continue end
            hdlb(i)
        end

        if mode == 2 then
            cm(pst, "ChildAdded", function(u)
                if u.Name ~= "FoodBanana" then return end
                task.wait(ping()/900)
                hdlb(u)
            end, pst.Name .. "Conn")
            return
        end
        cm(pst.Name .. "Conn", "Add", function(u)
            if u.Name ~= "FoodBanana" then return end
            task.wait(ping()/700)
            hdlb(u)
        end)
    end

    for _, a in pairs(var("ps"):GetPlayers()) do
        hdlp(workspace[a.Name .. "SpawnedInToys"])
    end

    for _, r in pairs(workspace.PlotItems:GetChildren()) do
        if r.Name == "PlayersInPlots" then continue end
        hdlp(r, 2)
    end
    
    cm("plradded", "Add", function(t)
        hdlp(workspace[t.Name .. "SpawnedInToys"])
    end)
end

AntiBanana()
--//
var("Toggle").Hvlpk = false

function lpkh(on, plr)
    var("Toggle").Hvlpk = on

    if not var("Toggle").Hvlpk or not var("LPDrod") == plr then return end

    task.spawn(function()
        repeat
            task.wait(ping()/1000)
            if not (plr.Character.HumanoidRootPart.AssemblyLinearVelocity.Y >= 10000) then 
                dssetowner(plr.Character.Torso, 24, 2)
                repeat task.wait(0.05) until verify(plr.Character.Head, 2) or not plr.Character or plr.Character.Parent ~= workspace
                dgl(plr.Character.Torso)
                plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 99e30, 0)
            end
        until not var("Toggle").Hvlpk or not plr.Character or plr.Character.Humanoid:GetState() == Enum.HumanoidStateType.Dead
    end)

    cm(plr.Name .. "Conn", "Add", function()
        if not var("Toggle").Hvlpk or not var("LPDrod") == plr then return end
        repeat
            task.wait(ping()/1500)
            if not (plr.Character.HumanoidRootPart.AssemblyLinearVelocity.Y >= 10000) then 
                dssetowner(plr.Character.Torso, 24, 2)
                repeat task.wait(0.05) until verify(plr.Character.Head, 2) or not plr.Character or plr.Character.Parent ~= workspace
                dgl(plr.Character.Torso)
                plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 99e30, 0)
            end
        until not var("Toggle").Hvlpk or not plr.Character or plr.Character.Humanoid:GetState() == Enum.HumanoidStateType.Dead
    end)
end
--//
function gblob(mode)
    if mode == 2 then
        timer("gbst", 0.3)
        if MBP("Humanoid").SeatPart then
            if MBP("Humanoid").SeatPart.Parent.Name == "CreatureBlobman" then return end
            MBP("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)
        end
        if inpast("CreatureBlobman") then
            var("Remotes").dt:FireServer(inpast("CreatureBlobman"))
            repeat task.wait() until not inpast("CreatureBlobman")
        end
        SICF("CreatureBlobman", nil, "Front")
        repeat task.wait() until inpast("CreatureBlobman")
        wfc("VehicleSeat", inpast("CreatureBlobman")):Sit(MBP("Humanoid"))
        task.wait(ping()/900)
        repeat task.wait() until wfc("VehicleSeat", inpast("CreatureBlobman")).Occupant == MBP("Humanoid") or not timer("gbst")
        return
    end
    if not MBP("Humanoid") then return false end
    if not  MBP("Humanoid").Sit == true and not MBP("Humanoid").SeatPart ~= nil then return false end
    if not MBP("Humanoid").SeatPart.Parent.Name == "CreatureBlobman" then return false end
    return MBP("Humanoid").SeatPart.Parent
end

function firegrb(blb, hrp, mode, rght)
    if not blb then return end
    if not hrp then return end

    local hnd = rght and blb.RightDetector or blb.LeftDetector
    local scrpt = blb.BlobmanSeatAndOwnerScript
    local dctw = nil

    if hnd == blb.LeftDetector then 
        dctw = blb.LeftDetector.LeftWeld 
    else 
        dctw = blb.RightDetector.RightWeld
    end
    task.spawn(function()
        if mode == "grb" then
            scrpt.CreatureGrab:FireServer(hnd, hrp, dctw)
        elseif mode == "drp" then
            scrpt.CreatureDrop:FireServer(hnd, hrp)
        elseif mode == "perm" then
            task.spawn(function()
                scrpt.CreatureRelease:FireServer(dctw, hrp)
            end)
        end
    end)
end

function delblob(char1, cb)
    local a = nil
    if char1:IsA("Model") then
        a = char1
    else
        a = char1.Character
    end
    if not a.Humanoid.SeatPart then
        cb(a) 
        return
    end
    
    local seat = a.Humanoid.SeatPart
    seat.AssemblyRootPart.CFrame = CFrame.new(0, -49000, 2000)

    repeat 
        task.wait()  
    until seat.AssemblyRootPart.ReceiveAge == 0 
    
    repeat
        firegrb(gblob(), a.HumanoidRootPart, "grb", true)
        firegrb(gblob(), a.HumanoidRootPart, "perm", true)
        task.wait(0.1)
        seat.AssemblyRootPart.AssemblyLinearVelocity = Vector3.new(0, -50000, 0)
    until not a.Humanoid.SeatPart
    
    if a.Humanoid.SeatPart then
        permown(var("BPDrod"), function()
            delblob(char1, cb)
        end)
    else
        cb(a)
    end
end

function permown(plr, callback, inst)
    if not plr then return end
    if not callback then return end
    local hrp = wfc("HumanoidRootPart", plr:IsA("Model") and plr or plr.Character, 2)
    if not hrp then return end
    if wfc("Humanoid", hrp.Parent):GetState() == Enum.HumanoidStateType.Dead then return end
     
    if not gblob() then
        timer("pmowns", 4)
        var("Remotes").dt:FireServer(inpast("CreatureBlobman"))
        task.wait(ping()/900)
        SICF("CreatureBlobman", nil, "Front")
        repeat task.wait() until inpast("CreatureBlobman")
        local st = wfc("VehicleSeat", inpast("CreatureBlobman"), 3)
        repeat task.wait() until st ~= nil or not timer("pmowns")
        repeat
            task.wait(ping()/1000)
            st:Sit(MBP("Humanoid"))
        until MBP("Humanoid").Sit == true and MBP("Humanoid").SeatPart or not timer("pmowns")
    end
    timer("pmowng", 5)
    
    local pos = nil
    task.spawn(function()
        repeat
            if not IsAround(hrp, 40) then
                if not pos then
                    pos = MBP("HumanoidRootPart").CFrame
                end
                MBP("HumanoidRootPart").CFrame = hrp.CFrame
                MBP("HumanoidRootPart").AssemblyLinearVelocity = Vector3.zero
            end
            task.wait(0.05)
        until verify(hrp) or not timer("pmowng") or not gblob()
    end)

    repeat 
        task.wait()
        firegrb(gblob(), hrp, "grb", true)
        firegrb(gblob(), hrp, "perm", true)
        if inst then
            callback(hrp.Parent)
        end
    until verify(hrp) or not timer("pmowng") or not gblob()
    
    
    if pos then
        MBP("HumanoidRootPart").CFrame = pos
        pos = nil
    end

    if verify(hrp) and not inst then
        task.spawn(function()
            callback(hrp.Parent)
        end)
    end
end
--//
function AntiFling()
    if var("p").InOwnedPlot.Value or var("Toggle").AntiFling then
        nocol(MBP("Head").Parent, "PlotPlayers")
    elseif not var("p").InOwnedPlot.Value then
        nocol(MBP("Head").Parent, "Players")
    end
end

cm(var("p").InOwnedPlot, "PropertyChanged", "Value", function()
    AntiFling()
end, "iownpc")

cm("WCA", "Add", function(i)
    task.spawn(function()
        task.wait(0.02)
        if var("Toggle").AntiFling then
            if not p("GrabParts", workspace) then return end
            local gp = wfc(p("GrabParts", workspace), "GrabPart")["WeldConstraint"].Part1
            if ippp(gp.Parent) then return end
            nocol(gp.Parent, "PlotItems")
    
            repeat task.wait() until not p("GrabParts", workspace)
            nocol(gp.Parent, "Items")
        end
    end)  
end)
--//
function fbexp()
    cm(workspace, "ChildAdded", function(i)
        if _G.Brkhs then cm("fbexpconn", "disc") end
        if i.Name == "Part" and (i.Position - Vector3.new(263.4, -4.79, 466.8)).Magnitude <= 2 then
           _G.Brkhs = true
            notify("Note!", "Destroyed Houses Barrier")
            for _, i in pairs(workspace.Plots:GetChildren()) do
                for _, i in pairs(i.Barrier:GetChildren()) do
                    if i:IsA("BasePart") and i.CanCollide == true then
                        i.CanCollide = false
                    end
                end
            end
            cm("fbexpconn", "disc")
        end
    end, "fbexpconn")
end

function breakhouse(mode)
    if not tm.Find(var("paths").Toys, "BallSnowball") then
        notify("Note", "You Dont Have SnowBall", "rbxassetid://7733911828")
        return
    end
    if not mode then return end
    fbexp()
    timer("brkhs", 10)
    repeat
        SICF("BallSnowball", CFrame.new(263.5, -4.5, 486.9))
        repeat task.wait() until var("p").CanSpawnToy.Value
    until _G.Brkhs or not timer("brkhs")
end
--//
var("Valores").WhtList = {}

function mwtlist(plr, mode, drop)
    if not mode then return end 
    local pnames = {}

    if mode == "In" then
        for _, p in pairs(var("ps"):GetPlayers()) do
            if p ~= var("p") and tm.Find(var("Valores").WhtList, p.Name) then
                table.insert(pnames, p.Name .. " (" .. p.DisplayName .. ")")
            end
        end
        return pnames

    elseif mode == "Out" then
        for _, p in pairs(var("ps"):GetPlayers()) do
            if p ~= var("p") and not tm.Find(var("Valores").WhtList, p.Name) then
                table.insert(pnames, p.Name .. " (" .. p.DisplayName .. ")")
            end
        end
        return pnames

    elseif mode == "Add" then
        if not plr then return end
        tm.Add(var("Valores").WhtList, plr)
        UPDDrops()
        if drop then
            drop:Set("")
        end

    elseif mode == "Remove" then
        if not plr then return end
        tm.Remove(var("Valores").WhtList, plr)
        UPDDrops()
        if drop then
            drop:Set("")
        end

    elseif mode == "Find" then
        if not plr then return end
        return tm.Find(var("Valores").WhtList, plr)

    elseif mode == "Clear" then
        tm.Clear(var("Valores").WhtList)
        UPDDrops()
    end
end
--//
function brkowngc()
    task.spawn(function()
        timer("stlim", 5)
        
        for i=1, 10 do
            task.spawn(function()
                MBP("Humanoid").Sit = true
            end)
            repeat task.wait() until MBP("Humanoid").Sit == true or not timer("stlim")
            MBP("Humanoid").Sit = false
        end
        repeat task.wait() until not MBP("Humanoid").Ragdolled.Value
    end)
    task.wait(0.3)
end
--//
function tpprt()
    task.spawn(function()
        local part = FINF()
    
        if ippp(part) then return end
        local pt
        local pt2 
        if p("Humanoid", part.Parent) or p("HumanoidCreature", part.Parent) then
            pt = p("Head", part.Parent)
            if p("HumanoidCreature", part.Parent) then
                pt2 = p("HumanoidRootPart", part.Parent)
            end
        else
            pt = part
        end
    
        if not pt then return end
        dssetowner(pt2 and pt2 or pt, nil, 3)
        repeat 
            task.wait()
            if IsAround(pt, 30) then
                setowner(pt, "Safe")
            end
        until verify(pt, 2)
        pt.CFrame = var("Lists").Loc2[var("Valores").tpprtloc]
    end)
end
--//
var("Toggle").AntiKick = false

local akickcf = CFrame.new(0, 0.1, 0.322, 0, 0, -1, 0, 1, 0, 1, 0, 0)
local akickang = CFrame.Angles(0, 0, 190)

function AntiKick()
---@diagnostic disable-next-line: undefined-global
    function shspwn()
        if MBP("Humanoid"):GetState() == Enum.HumanoidStateType.Dead or not var("Toggle").AntiKick then
            var("Remotes").dt:FireServer(inpast("NinjaShuriken"))
            return 
        end
        task.spawn(function()
            if inpast("NinjaShuriken") then
                local sh = inpast("NinjaShuriken")
                local sp = sh:WaitForChild("StickyPart")
                local pw = sp:FindFirstChild("PartOwner")
                
                if pw and pw.Value == var("p").Name then 
                    SPE(p("StickyPart", sh, 2), MBP("HumanoidRootPart")["FirePlayerPart"], akickcf, akickang)
                    task.spawn(function()
                        repeat
                            task.wait()
                            local k = inpast("NinjaShuriken")
                            if not k then break end
                            
                            local sp = k:FindFirstChild("StickyPart")
                            if not sp then break end
                            
                            local po = sp:FindFirstChild("PartOwner")
                            if not po then break end

                            if not IsAround(sp, 5) then
                                break
                            end 
                            
                            if po.Value ~= var("p").Name then break end
                            if not var("Toggle").AntiKick then break end
                            if MBP("Humanoid"):GetState() == Enum.HumanoidStateType.Dead then break end
                        until false
                        AntiKick()
                    end)
                else
                    var("Remotes").dt:FireServer(sh)
                    var("ov").inv.ChildRemoved:Wait()
                    AntiKick()
                end
            else
                if var("ov").char.Parent ~= workspace then
                    repeat task.wait() until var("ov").char.Parent == workspace
                    if MBP("Humanoid"):GetState() == Enum.HumanoidStateType.Dead then return end
                end
                
                var("ov").inv.ChildAdded:Once(function(i)
                    if not var("Toggle").AntiKick then return end
                    if i.Name == "NinjaShuriken" then
                        local sp = i:WaitForChild("StickyPart", 3)   
                        if sp then
                            repeat 
                                task.wait(0.05)
                                setowner(sp)    
                            until (sp:FindFirstChild("PartOwner") and sp["PartOwner"].Value == var("p").Name) or not var("Toggle").AntiKick  or not IsAround(sp, 30)
                            
                            if sp:FindFirstChild("PartOwner") and sp["PartOwner"].Value == var("p").Name then
                                AntiKick()
                            else
                                var("Remotes").dt:FireServer(i)
                                task.wait(0.5)
                                if var("Toggle").AntiKick then
                                    AntiKick()
                                end
                            end
                        end
                    end
                end)
                repeat
                    SICF("NinjaShuriken", nil, "Head")
                    task.wait(0.1)
                until inpast("NinjaShuriken") or not var("Toggle").AntiKick
            end
        end)
    end
    shspwn()
end
cm("CharAppLoad","Add", AntiKick)
--//
function pbkaura()
    FMC(40, function(m)
        local plr = var("ps"):GetPlayerFromCharacter(m)
        if mwtlist(plr.Name, "Find") then return end
        if m.Humanoid:GetState() == Enum.HumanoidStateType.Dead then return end
        gblob(2)
        permown(m, function(a)
            delblob(m, function(a)
                a.Humanoid.BreakJointsOnDeath = false
                a.Humanoid:ChangeState(Enum.HumanoidStateType.Dead)
            end)
        end)
    end, "plrs")
end
--//
function pbkiaura()
    FMC(30, function(m)
        local plr = var("ps"):GetPlayerFromCharacter(m)
        if mwtlist(plr.Name, "Find") then return end
        if m.Parent ~= workspace then return end
        gblob(2)
        repeat task.wait() until MBP("Humanoid").Sit == true
        if not var("Toggle").LKickb then return end
        if m.Humanoid:GetState() == Enum.HumanoidStateType.Dead then return end
        if tm.Find(var("Valores").OnAur, var("ps"):GetPlayerFromCharacter(m)) then return end
        task.spawn(function()
            timer(m.Name .. "tm", 1)
            dssetowner(m.Head, 30, 3)
            repeat task.wait() until verify(m.Head, 2) or not timer(m.Name .. "tm")
            if not verify(m.Head, 2) then return end

            local bp = Instance.new("BodyPosition")

            bp.Position = m.HumanoidRootPart.Position + Vector3.new(0, 15, 0)
            bp.MaxForce = Vector3.new(0, math.huge, 0)
            bp.P = 1e5
            bp.D = 1e3
            bp.Parent = m.HumanoidRootPart
            task.wait(ping()/1000)
            dgl(m.HumanoidRootPart)
            task.wait(ping()/1500)
            firegrb(gblob(), m.HumanoidRootPart, "grb", true)
            tm.Add(var("Valores").OnAur, var("ps"):GetPlayerFromCharacter(m))
            repeat task.wait() until not verify(m.HumanoidRootPart) or m.Humanoid:GetState() == Enum.HumanoidStateType.Died
            if bp and bp.Parent then bp:Destroy() end
            tm.Remove(var("Valores").OnAur, var("ps"):GetPlayerFromCharacter(m))
        end)
    end, "plrs")
end
--//
function pbkoaura()
    FMC(30, function(m)
        task.spawn(function()
            timer("pkoaura", 2)
            local plr = var("ps"):GetPlayerFromCharacter(m)
            if mwtlist(plr.Name, "Find") then return end
            if m.Humanoid:GetState() == Enum.HumanoidStateType.Dead then return end
    
            setowner(m.HumanoidRootPart,  "Safe")
            repeat task.wait() until verify(m.Head, 2) or not timer("pkoaura")
            m.Humanoid.BreakJointsOnDeath = false
            m.Humanoid:ChangeState(Enum.HumanoidStateType.Dead)
            dgl(m.HumanoidRootPart)
        end)
    end, "plrs")
end
--//
function pbkkoaura()
    FMC(30, function(m)
        task.spawn(function()
            timer("pkkoaura", 2)
            local plr = var("ps"):GetPlayerFromCharacter(m)
            if mwtlist(plr.Name, "Find") then return end
            if m.Humanoid:GetState() == Enum.HumanoidStateType.Dead then return end
    
            setowner(m.HumanoidRootPart,  "Safe")
            repeat task.wait() until verify(m.Head, 2) or not timer("pkkoaura")
            dgl(m.HumanoidRootPart)
            m.HumanoidRootPart.CFrame = CFrame.new(0, 99e30, 0 )
        end)
    end, "plrs")
end
--//
var("Toggle").ghpt = false
var("Lists").ghpL = {}
var("Lists").tbrk = {}

function ghp(mdl)
    if not mdl then return end

    if not string.find(mdl.Name, "Food") and not string.find(mdl.Name, "Instrument") and not string.find(mdl.Name, "Poop") and not string.find(mdl.Name, "Cup") then
        return
    end
    if tm.Find(var("Lists").tbrk, mdl) then return end

    local hp = wfc(mdl, "HoldPart")
    local dr = wfc(hp, "DropItemRemoteFunction")
    local hr = wfc(hp, "HoldItemRemoteFunction")

    if hp.RigidConstraint.Attachment1 then
        repeat task.wait() until not hp.RigidConstraint.Attachment1
    end

    if not hp.RigidConstraint.Attachment1 then
        task.spawn(function()
            hr:InvokeServer(mdl, var("ov").char)
        end)
        dr:InvokeServer(mdl)
        if tm.Find(var("Lists").ghpL, mdl) then
            tm.Remove(var("Lists").ghpL, mdl)
        end
        tm.Add(var("Lists").tbrk, mdl)
    end
end

function hdllgp(mdl, rec)
    if not var("Toggle").ghpt then
        if mdl then
            tm.Add(var("Lists").ghpL, mdl)
        end
        return
    end
    if rec then
        if #var("Lists").ghpL > 0 then
            for _, sm in ipairs(var("Lists").ghpL) do
                task.spawn(function()
                    pcall(function()
                        ghp(sm)
                    end)
                end)
            end
        end
        return
    end
    if mdl then
        ghp(mdl)
    end
end

task.spawn(function()
    for _, i in pairs(var("ps"):GetPlayers()) do
        cm(workspace[i.Name .. "SpawnedInToys"], "ChildAdded", function(m)
        end, i.Name .. "SpawnedInToys" .. "Conns")
    end
end)

function rnaa()
    for _, i in pairs(var("ps"):GetPlayers()) do
        cm(i.Name .. "SpawnedInToys" .. "Conns", "Add", function(m)
            hdllgp(m)
        end)
    end

    if not var("Toggle").ghpt then
        for _, i in pairs(var("ps"):GetPlayers()) do
            for _, u in pairs(workspace[i.Name .. "SpawnedInToys"]:GetChildren()) do
                tm.Add(var("Lists").ghpL, u)
            end
            task.wait(0.05)
        end
    end
end

task.spawn(rnaa)
--//
var("Conns").lag = {}
var("Toggle").Lag = false

function lag()
    local num
    local newnum

    local function hdll()
        if not num then return end

        for _, conn in pairs(var("Conns").lag) do
            pcall(function() conn:Disconnect() end) 
        end
        var("Conns").lag = {}

        for i = 1, num do
            local index = "Conn" .. tostring(i)
            local conn

            conn = var("RunS").Heartbeat:Connect(function()
                if not var("Toggle").Lag then
                    pcall(function()
                        conn:Disconnect()
                        var("Conns").lag[index] = nil
                    end)
                    return
                end

                local head = MBP("Head")
                if head then
                    var("Remotes").cgl:FireServer(MBP("Head"), MBP("Head").CFrame)
                end
            end)

            var("Conns").lag[index] = conn
        end
    end

    function hdllf()
        local count = #var("ps"):GetPlayers()

        if count > 10 then
            newnum = 3
        else
            newnum = 2
        end

        if not num then
            num = newnum
        end

        if num ~= newnum then
            num = newnum
            if var("Toggle").Lag then
                hdll()
            end
        end
    end

    hdllf()
    hdll()

    repeat
        hdllf()
        task.wait(2)
    until not var("Toggle").Lag
end
--//
var("Valores").VRS = 2
var("Toggle").pkl = false
var("Valores").pklv = 500

cm(var("RunS"), "RenderStepped", function()
    if var("Toggle").pkl then
        var("Remotes").egl:FireServer(string.rep("𒐫𓂀𗀀𖤐𐕣𒀀𓆣𒐫𗃼𒐫𖨆𐘋", var("Valores").pklv))
    end    
end, {ref = var("Valores"), key = "VRS"}, "pkl")
--//
function NmG()
    local mdl = workspace:FindFirstChild("GrabParts")
    local gp = wfc("WeldConstraint", wfc("GrabPart", mdl)).Part1

    if gp then
        if gp.AssemblyRootPart.ReceiveAge ~= 0 then
            timer("Ege", ping()/1000 + 0.05)
            repeat task.wait() until gp.AssemblyRootPart.ReceiveAge == 0 or not timer("Ege")
        end

        if gp.AssemblyRootPart.ReceiveAge == 0 then
            repeat task.wait() until gp.AssemblyRootPart.ReceiveAge ~= 0 or mdl.Parent ~= workspace
            if mdl.Parent == workspace then
                var("VIM"):SendMouseButtonEvent(0, 0, 0, true, game, 1)
                var("VIM"):SendMouseButtonEvent(0, 0, 0, false, game, 1)
            end
        end
    end
end

cm("WCA", "Add", NmG)
--//
var("Valores").fcontlim = 15

function AntiPerm(a)
    if not a then
        SICF("FoodBread", var("Lists").Loc2["Hide"])
        return
    end
    
    task.spawn(function()
        if a then
            if a:FindFirstChild("SoundPart") then
                for _, i in pairs(a:GetChildren()) do 
                    if i.Name ~= "HoldPart" and i.Name ~= "PlayerValue" and i.Name ~= "ThisToysNumber" and i.Name ~= "HoldAndEatFood" then
                        i:Destroy()
                    end
                end
            end
            hdrem(a, "Hold")
            hdrem(a, "Drop")
        end
    end)
end
--sep                                                                  
--# Orion Config

local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/fiiremax/Scripts/refs/heads/master/Scriptnew/GitHub/Scripts/Loader.lua')))()
OrionLib.UMouseMode = "ThirdPerson"

local Window = OrionLib:MakeWindow({
    TagText = var("Toggle").Isowner and "Owner" or "User",
    IntroIcon = "rbxassetid://114143041236784",
    Icon = "rbxassetid://114143041236784",
    IntroText = "Welcome to Fire Hub",
    IconColorChange = true,
    IntroEnabled = false,
    HidePremium = false,
    SaveConfig = false,
    Name = "Fire Hub",
    FreeMouse = true,
    SearchBar = true,
    ShowIcon = true
})

---@diagnostic disable-next-line: undefined-global
function notify(N, C, I, T)
    OrionLib:MakeNotification({
        Name = N or "Note!",
        Content = C or "Message",
        Image = I or "rbxassetid://8798704474",     
        Time = T or 5
    })
end
--sep                                                                  
--# Tabs 

local Grab = Window:MakeTab({
    Name = "Grab",
    Icon = "rbxassetid://13300916613"
})
--///                                                                  
local Antis = Window:MakeTab({
    Name = "Anti",
    Icon = "rbxassetid://8932822968"
})
--///                                                                  
local Aura = Window:MakeTab({
    Name = "Auras",
    Icon = "rbxassetid://9168485886"
})
--///                                                                  
local Loop = Window:MakeTab({
    Name = "Loop",
    Icon = "rbxassetid://9613508061"
})
--///                                                                  
local Blob = Window:MakeTab({
    Name = "Blob",
    Icon = "rbxassetid://10152135063"
})
--///                                                                  
local Bind = Window:MakeTab({
    Name = "Bind",
    Icon = "rbxassetid://120215363736817"
})

--///                                                                  
local Esp = Window:MakeTab({
    Name = "ESP",
    Icon = "rbxassetid://7734042234"
})
--///                                                                  
local Config = Window:MakeTab({
    Name = "Config",
    Icon = "rbxassetid://13300915301"
})
--sep                                                                  
--# UI(Front-End)

Grab:ColorLabel("Grab Tab", nil, "Center")
Grab:AddSection()

Grab:AddToggle({
    Name = "Kill",
    Callback = function(Value)
        var("Toggle", "KillGrabTg", Value)
    end
})

Grab:AddToggle({
    Name = "NoClip",
    Callback = function(Value)
        var("Toggle", "NoClipGTg", Value)
    end
})

Grab:AddToggle({
    Name = "Invisible",
    Callback = function(Value)
        var("Toggle", "InvsGrabTg", Value)
    end
})

Grab:AddSection()

Grab:AddToggle({
    Name = "Strength",
    Callback = function(Value)
        var("Toggle", "SSTG", Value)
    end,
})

Grab:AddSlider({
    Name = "Strength Value",
    Min = 0,
    Max = 5000,
    Default = 5,
    Increment = 1,
    ValueName = "Value",
    Save = true,
    Block = {"Toggle", "SSTG"},
    varFunc = var,
    Callback = function(Value)
        var("Valores", "SSVal", Value)
    end
})
--///                                                                  
Antis:ColorLabel("Anti Tab", nil, "Center")
Antis:AddSection()

Antis:AddParagraph(
"Antis\n |\n v",
"These antis help protect the player and reduce the risk of unintended behavior.",
"Center"
)

AntiGrabDrp = Antis:AddDropdown({
    Name = "Options",
    Multi = true,
    Searchable = true,
    Call = true,
    Options = {
        "Explosion",
        "Banana",
        "Fling",
        "Items",
        "Grab",
        "Kick",
        "Blob",
        "Fire",
        "Void",
        "Pcld",
        "Lag"
    },    Callback = function(Value) 
        task.spawn(function()
            var("Toggle").AntiLag     = table.find(Value, "Lag") ~= nil
            var("Toggle").AntiFireTG  = table.find(Value, "Fire") ~= nil
            var("Toggle").AntiBlob    = table.find(Value, "Blob") ~= nil
            var("Toggle").AntiVoidTG  = table.find(Value, "Void") ~= nil
            var("Toggle").AntiKick    = table.find(Value, "Kick") ~= nil
            var("Toggle").AntiGbTG    = table.find(Value, "Grab") ~= nil
            var("Toggle").pcld        = table.find(Value, "Pcld") ~= nil
            var("Toggle").ghpt        = table.find(Value, "Items") ~= nil
            var("Toggle").AntiFling   = table.find(Value, "Fling") ~= nil
            var("Toggle").AntBnn      = table.find(Value, "Banana") ~= nil
            var("Toggle").AntiExpTG   = table.find(Value, "Explosion") ~= nil

            if var("Toggle").AntiFireTG and MBP("HumanoidRootPart").FirePlayerPart.CanBurn.Value then
                firetouchinterest(MBP("HumanoidRootPart").FirePlayerPart, workspace.Map.Hole.PoisonSmallHole.ExtinguishPart, 0)
            end

            AntiFling()

            if var("Toggle").AntiKick == true then
                AntiKick()
            end

            if var("Toggle").ghpt then
                hdllgp(nil, true)
            end

            if var("Toggle").AntBnn then
                for _, i in pairs(var("Valores").Bnns) do
                    hdlb(i)
                end
            end

            AntiLagF(var("Toggle").AntiLag)
        end)
    end
})

Antis:AddButton({
    Name = "Gucci",
    Icon = "rbxassetid://3944703587",
    Callback = function()
        brkowngc()
        task.spawn(function()
            pcall(GucciAnti) 
        end)
    end    
})

Antis:AddSection()

var("paths").lposb = nil

Antis:AddToggle({
    Name = "Loop",
    Default = false,
    Callback = function(Value)
        var("Toggle", "AntiLoopTg", Value)
        var("paths").lposb:toggle()
    end
})

var("paths").lposb = Antis:AddPbind({
    Name = "Loop Position",
    DefaultX = "",
    DefaultY = "",
    DefaultZ = "",
    Callback = function(x, y, z)
        if x == "" and y == "" and z == "" then
            var("Valores").ALpos.X = -535
            var("Valores").ALpos.Y = 20
            var("Valores").ALpos.Z = -167
        else
            var("Valores").ALpos.X = (x ~= "" and tonumber(x)) or 0
            var("Valores").ALpos.Y = (y ~= "" and tonumber(y)) or 0
            var("Valores").ALpos.Z = (z ~= "" and tonumber(z)) or 0
        end
    end
})

var("paths").lposb:toggle()

Antis:AddSection()

var("Drops").MDpdown = Antis:AddDropdown({
    Name = "Select Player",
    Default = "",
    Options = GPNames(),
    Searchable = true,
    PlrLeftNote = true,
    Callback = function(Value)
        var("MscDpdown", var("ps")[string.match(Value, "^(.-)%s*%(")]) 
    end
})

Antis:AddDropdown({
    Name = "Options",
    Call = true,
    Default = "Teleport",
    Options = {
        "Break Gucci",
        "Teleport",
        "Ragdoll",
        "Poison",
        "Kill",
        "Bring"
    },
    Callback = function(Value)
        var("Valores").MscDPdownc = Value
    end
})

Antis:AddButton({
    Name = "Fire",
    Icon = "rbxassetid://3944703587",
    Callback = function()
        if not var("MscDpdown") then 
            notify("Note!", "Please Select the Person!.")
        end

        if var("Valores").MscDPdownc == "Break Gucci" then
            breakgucci(var("MscDpdown"))
        elseif var("Valores").MscDPdownc == "Teleport" then
            MBP("HumanoidRootPart").AssemblyLinearVelocity = Vector3.zero
            MBP("HumanoidRootPart").CFrame = var("MscDpdown").Character.HumanoidRootPart.CFrame * CFrame.new(0, 10, 0)
        elseif var("Valores").MscDPdownc == "Ragdoll" then
            Sragdoll(var("MscDpdown"))
        elseif var("Valores").MscDPdownc == "Poison" then
            pfunc(var("MscDpdown"))
        elseif var("Valores").MscDPdownc == "Kill" then
            KillP(var("MscDpdown"))
        elseif var("Valores").MscDPdownc == "Bring" then
            if var("MscDpdown") and var("MscDpdown").Character and var("MscDpdown").Character.Parent ~= workspace then 
                notify("Note!", "Player In House.")
                return
            end
            bringp(var("MscDpdown").Character)
        end
    end    
})

Antis:AddSection()
Antis:AddParagraph(
"Anti Input",
"Prevents Perm Owner On You and helps alot of functions", "Center"
)

Antis:AddDropdown({
    Name = "Velocity",
    Default = "Default(Invisible)",
    Options = {"Default(Invisible)", "Fast"},
    Callback = function(Value)
        var("Valores").APM = Value
        var("Valores").fcontlim = (var("Valores").APM == "Default(Invisible)") and 8 or 2
    end
})

var("Valores").AIToy = "FoodBread"

Antis:AddToggle({
    Name = "Input",
    Default = false,
    Callback = function(Value)
        var("Toggle", "AntiPermOwn", Value)
        if not tm.Find(var("paths").Toys, "FoodBread") then
            notify("Note", "You Dont Have FoodBread", "rbxassetid://7733911828")
            var("Valores").AIToy = "InstrumentWoodwindOcarina"
            return
        end
        if Value then
            cm(var("RunS"), "Heartbeat", function()
                if not var("Toggle").AntiPermOwn then
                    cm("HBAntPm", "disc")
                    return
                end
                AntiPerm(inpast(var("Valores").AIToy))
            end,{ref = var("Valores"), key = "fcontlim"}, "HBAntPm")
        end
    end,
})

Antis:AddSection()
Antis:AddParagraph(
"Sync",
"Prevents players appear invisible or in the wrong place", "Center"
)

Antis:AddToggle({
    Name = "Sync",
    Default = true,
    Callback = function(Value)
        var("Toggle").AntiDsync = Value
        if Value then
            for _, i in pairs(var("ps"):GetPlayers()) do
                if i.Character and p("HumanoidRootPart", i.Character) then
                    p("HumanoidRootPart", i.Character).Massless = true
                    p("HumanoidRootPart", i.Character).Massless = false
                end
            end
        end
    end
})

Antis:AddSection()

local spdval = nil
local jupval = nil

Antis:AddToggle({
    Name = "Speed",
    Default = false,
    Callback = function(Value)
        var("Toggle").SpeedCFTg = Value
        if Value and cm("SpeedCF", "status") ~= "active" then
            cm("SpeedCF", "resume")
        elseif not Value and cm("SpeedCF", "status") ~= "paused" then
            cm("SpeedCF", "pause")
        end
        if not Value then
            task.defer(function()
                spdval:Set(0.01)
            end)
        end
    end
})

spdval = Antis:AddSlider({
    Name = "Speed Value",
    Min = 0.01,
    Max = 0.5,
    Default = 0,
    Increment = 0.01,
    ValueName = "Value",
    Callback = function(Value)
        var("Valores", "SpeedMltVal", Value)
    end,
    Block = {"Toggle", "SpeedCFTg"},
    varFunc = var
})

Antis:AddSection()

Antis:AddToggle({
    Name = "Infinite Jump",
    Default = false,
    Callback = function(Value)
        var("Toggle").InfJumpTg = Value
    end
})

Antis:AddToggle({
    Name = "Jump",
    Default = false,
    Callback = function(Value)
        var("Toggle").JumpTG = Value
        if not Value then
            task.defer(function()
                jupval:Set(24)
            end)
        end
    end
})

jupval = Antis:AddSlider({
    Name = "Jump Value",
    Min = 24,
    Max = 100,
    Default = 24,
    Increment = 1,
    ValueName = "Value",
    Callback = function(Value)
        MBP("Humanoid").JumpPower = Value
    end,
    Block = {"Toggle", "JumpTG"},
    varFunc = var
})
--///                                                                  
Aura:ColorLabel("Aura Tab", nil, "Center")
Aura:AddSection()

Aura:ColorLabel("Ownership (O)", nil, "Center")
Aura:AddToggle({
    Name = "Kill(O)",
    Default = false,
    Callback = function(Value)
        var("Toggle").LoKill = Value
        repeat
            task.wait(0.05)
            pbkoaura()
        until not var("Toggle").LoKill
    end
})

Aura:AddToggle({
    Name = "Kick(O)",
    Default = false,
    Callback = function(Value)
        var("Toggle").LKoKill = Value
        repeat
            task.wait(0.1)
            pbkkoaura()
        until not var("Toggle").LKoKill
    end
})

Aura:AddSection()
Aura:ColorLabel("PermOwnership (PO)", nil, "Center")

Aura:AddToggle({
    Name = "Kill(PO)",
    Default = false,
    Callback = function(Value)
        var("Toggle").LKill = Value
        repeat
            task.wait(0.3)
            pbkaura()
        until not var("Toggle").LKill
    end
})

Aura:AddToggle({
    Name = "Kick(PO)",
    Default = false,
    Callback = function(Value)
        var("Toggle").LKickb = Value
        if Value then
            repeat
                task.wait(0.3)
                pbkiaura()
            until not var("Toggle").LKickb
        end
        tm.Clear(var("Valores").OnAur)
    end
})
--///                                                                  
Blob:ColorLabel("Blob Tab", nil, "Center")
Blob:AddSection()

var("Drops").BDpdown = Blob:AddDropdown({
    Name = "Select Player",
    Default = "",
    Options = GPNames(),
    Searchable = true,
    Callback = function(Value)
        var("BPDrod", var("ps")[string.match(Value, "^(.-)%s*%(")])
    end
})

Blob:AddSection()

Blob:AddDropdown({
    Name = "Options",
    Call = true,
    Default = "Kick(Once)",
    Options = {
        "Kick(Once)",
        "bring",
        "Kill",
        "Lock"
    },
    Callback = function(Value)
        var("Valores").BlbPDropdwn = Value
    end
})

Blob:AddButton({
    Name = "Fire",
    Icon = "rbxassetid://3944703587",
    Callback = function()
        if not var("BPDrod") then 
            notify("Note!", "Please Select the Person!.")
        end
        
        if not gblob() then
            brkowngc()
        end

        if var("Valores").BlbPDropdwn == "Kill" then
            permown(var("BPDrod"), function(i)
                delblob(var("BPDrod"), function(i)
                    i.Humanoid.BreakJointsOnDeath = false
                    i.Humanoid:ChangeState(Enum.HumanoidStateType.Dead)
                end)
            end, true)
        elseif var("Valores").BlbPDropdwn == "Lock" then
            permown(var("BPDrod"), function(i)
                delblob(var("BPDrod"), function(i)
                    i.HumanoidRootPart.Anchored = false
                    i.HumanoidRootPart.CFrame = CFrame.new(0, -1000, 0)
                    task.wait(ping()/900)
                    i.HumanoidRootPart.Anchored = true
                end)
            end)
        elseif var("Valores").BlbPDropdwn == "bring" then
            permown(var("BPDrod"), function(i)
                delblob(var("BPDrod"), function(i)
                    var("Toggle").cnbring = false
                    for _, i in pairs(i:GetChildren()) do
                        if i:IsA("BasePart") and i.Name ~= "CamPart" then
                            task.spawn(function()
                                if i.Name == "Torso" then
                                    repeat 
                                        task.wait()
                                        i.CanCollide = false
                                    until var("Toggle").cnbring
                                else
                                    i.CanCollide = false
                                end
                            end)
                        end
                    end
                    local bp
                    if i.Parent ~= workspace then
                        i.HumanoidRootPart.Position = MBP("HumanoidRootPart").Position + MBP("HumanoidRootPart").CFrame.LookVector * 5
                        task.wait(ping()/900)
                    else
                        if i.HumanoidRootPart:FindFirstChild("BodyPosition") then
                            bp = i.HumanoidRootPart:FindFirstChild("BodyPosition")
                        else
                            bp = Instance.new("BodyPosition", i.HumanoidRootPart)
                            bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            bp.D = 300
                            bp.P = 1000
                        end
                        bp.Position = MBP("HumanoidRootPart").Position + MBP("HumanoidRootPart").CFrame.LookVector * 5
                    end
    
                    task.spawn(function()
                        timer("bbringp", 5)
                        repeat task.wait() until i.Head:FindFirstChild("PartOwner") or not timer("bbringp")
                        bp:Destroy()
                        var("Toggle").cnbring = true
                        for _, i in pairs(i:GetChildren()) do
                            if i:IsA("BasePart") and i.Name ~= "CamPart" then
                                i.CanCollide = true
                            end
                        end
                    end)
                end)
            end)
        elseif var("Valores").BlbPDropdwn == "Kick(Once)" then
            permown(var("BPDrod"), function(i)
                delblob(var("BPDrod"), function(i)
                    local bp = Instance.new("BodyPosition", i.HumanoidRootPart)
                    bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    bp.P = 50000
                    bp.D = 500
                    bp.Position = MBP("HumanoidRootPart").Position + Vector3.new(0, 100, 0)
    
                    task.wait(ping()/1000 + 0.15)
                    dssetowner(i.HumanoidRootPart, 30, 2, function()
                        dgl(i.HumanoidRootPart)
                        task.wait(ping()/1000)
                        firegrb(gblob(), i.HumanoidRootPart, "grb", true)
                    end)
                    task.delay(10, function()
                        bp:Destroy()
                    end)
                    var("Remotes").dt:FireServer(gblob())
                end)
            end)
        end
    end    
})

Blob:AddSection()

Blob:AddToggle({
    Name = "Remove WL Players",
    Default = false,
    Callback = function(Value)
        var("Drops").BDpdown.tg = Value
        UPDDrops()
    end    
})
--///                                                                  
Loop:ColorLabel("Loop Tab", nil, "Center")
Loop:AddSection()

var("Drops").LPDropdown = Loop:AddDropdown({
    Name = "Select Player",
    Default = "",
    Options = GPNames(),
    Searchable = true,
    Callback = function(Value)
        var("LPDrod", var("ps")[string.match(Value, "^(.-)%s*%(")])
    end
})

Loop:AddSection()

local lpd = nil

lpd = Loop:AddDropdown({
    Name = "Options",
    Call = true,
    Searchable = true,
    Default = "Ragdoll",
    Options = {
        "Kick(OwnerShip Heaven)",
        "Kick(OwnerShip)",
        "Ragdoll",
        "Kill",
        "------------------",
        "Kill (Blob)",
    },
    Callback = function(Value)
        if Value == "------------------"  then
            notify("Note!", "This is just a space :D")
            lpd:Set("Ragdoll")
        end
        var("Valores").LpDdrpdV = Value
    end
})

var("Toggle").bkconn = false

lpd2 = Loop:AddToggle({
    Name = "Fire",
    Default = false,
    Callback = function(Value)
        if not var("LPDrod") then
            lpd2:Set(false)
            notify("Note", "Select a person first.")
        end
        if var("Valores").LpDdrpdV == "Kick(OwnerShip)" then
            lkick(Value, var("LPDrod"))
            if not var("LPDrod") and Value then
                lpd2:Set(false)
                notify("Note!", "Please Select a player")
                return
            end
            if not Value then
                for _, i in pairs(MBP("HumanoidRootPart"):GetChildren()) do
                    if i.Name == "aln" then
                        i:Destroy()
                    end
                end
            end
        elseif var("Valores").LpDdrpdV == "Kick(OwnerShip Heaven)" then
            if not var("LPDrod") and Value then
                lpd2:Set(false)
                notify("Note!", "Please Select a player")
                return
            end
            if Value then
                brkowngc()
                if var("LPDrod").Character.Parent ~= workspace then
                    notify("Note", "Person in Safe. Waiting ...", "rbxassetid://7733911828")
                    repeat
                        task.wait()
                    until var("LPDrod").Character.Parent == workspace or not var("Toggle").Hvlpk
                end
                lpkh(Value, var("LPDrod"))
            else
                lpkh(Value, var("LPDrod"))
            end
        elseif var("Valores").LpDdrpdV == "Ragdoll" then
            var("Toggle").RagdollVal = Value
            if not var("LPDrod") and Value then
                lpd2:Set(false)
                notify("Note!", "Please Select a player")
                return
            end
            if Value then
                repeat
                    if var("p").CanSpawnToy then
                        task.spawn(function()
                            pcall(function()
                                Sragdoll(var("LPDrod"))
                            end)
                        end)
                    end
                    task.wait()
                until not var("Toggle").RagdollVal
            end
        elseif var("Valores").LpDdrpdV == "Kill" then
            var("Toggle").Lkill = Value
            if not var("LPDrod") and Value then
                lpd2:Set(false)
                notify("Note!", "Please Select a player")
                return
            end
            if Value then
                local a = false
                if var("LPDrod").Character.Parent ~= workspace then
                    notify("Note", "Person in Safe. Waiting ...", "rbxassetid://7733911828")
                    repeat
                        task.wait()
                    until var("LPDrod").Character.Parent == workspace or not var("Toggle").Lkill
                end
                repeat
                    KillP(var("LPDrod"))
                    task.wait(0.1)
                until not var("Toggle").Lkill or not var("LPDrod").Character or a
                cm(var("LPDrod"), "CharactedAdded", function()
                    if not var("Toggle").Lkill then return end
                    repeat
                        KillP(var("LPDrod"))
                        task.wait(0.1)
                    until not var("Toggle").Lkill or not var("LPDrod").Character
                end, "KillConn")
            else
                cm("KillConn", "disc") 
            end
        elseif var("Valores").LpDdrpdV == "Kill (Blob)" then
            var("Toggle").Bkill = Value
            if not var("LPDrod") and Value then
                lpd2:Set(false)
                notify("Note!", "Please Select a player")
                return
            end
            if Value then
                permown(var("LPDrod"), function(i)
                    i.Humanoid.BreakJointsOnDeath = false
                    i.Humanoid:ChangeState(Enum.HumanoidStateType.Dead)
                end, true)
                cm(var("LPDrod"), "CharacterAdded", function()
                    var("LPDrod").CharacterAppearanceLoaded:Wait()
                    task.wait(0.4)
                    permown(var("LPDrod"), function(i)
                        i.Humanoid.BreakJointsOnDeath = false
                        i.Humanoid:ChangeState(Enum.HumanoidStateType.Dead)
                    end, true)
                end, "BlbConn")   
            else
                cm("BlbConn", "disc")
            end
        end
    end    
})

Loop:AddSection()

Loop:AddToggle({
    Name = "Remove WL Players",
    Default = false,
    Callback = function(Value)
        var("Drops").LPDropdown.tg = Value
        UPDDrops()
    end    
})
Loop:AddSection()

Loop:ColorLabel("Misc", nil, "Center")
Loop:AddToggle({
    Name = "Soft Lag",
    Default = false,
    Callback = function(Value)
        var("Toggle").Lag = Value
        if Value then
            lag()
        end
    end    
})

Loop:AddSection()
Loop:AddToggle({
    Name = "Packet Send",
    Default = false,
    Callback = function(Value)
        var("Toggle").pkl = Value
    end    
})

Loop:AddTextbox({
    Name = "Packet Strings",
    Default = "1000",
    TextDisappear = false,
    BackGrountText = "Default(1000)",
    Callback = function(Value)
        if Value and tonumber(Value) ~= nil and tonumber(Value) <= 1800 then
            var("Valores").pklv = Value
            if tonumber(Value) > 1000 then
                print("A")
                notify("Note!", "Number above 1k keep an eye on the ping.")
            end
        elseif Value ~= "" and tonumber(Value) == nil then
            notify("Note!", "Just Numbers Please")
        elseif tonumber(Value) >= 1800 then
            notify("Note!", "Number is too High")
        else
            var("Valores").pklv = 1000
        end
    end
})
--///                                                                  
Bind:ColorLabel("Bind Tab", nil, "Center")
Bind:AddSection()

Bind:AddBind({
    Name = "Tp",
    Default = Enum.KeyCode.Z,
    Callback = function()
        TPme()
    end
})

Bind:AddBind({
    Name = "Bring",
    Default = Enum.KeyCode.N,
    Callback = function()
        task.spawn(bring)
    end
})

Bind:AddBind({
    Name = "ThirdParty",
    Default = Enum.KeyCode.T,
    Callback = function()
        var("Toggle").TPTg = not var("Toggle").TPTg
        if var("p").CameraMode == Enum.CameraMode.Classic and var("Toggle").TPTg then
            return
        end
        var("UIS").MouseIconEnabled = var("Toggle").TPTg 
        var("UIS").MouseBehavior = var("Toggle").TPTg and Enum.MouseBehavior.Default or Enum.MouseBehavior.LockCenter
        var("p").CameraMaxZoomDistance = var("Toggle").TPTg and OrionLib.maxds or 0.5
        var("p").CameraMinZoomDistance = var("Toggle").TPTg and OrionLib.minds or 0.5
        var("p").CameraMode = var("Toggle").TPTg and Enum.CameraMode.Classic or Enum.CameraMode.LockFirstPerson
    end
})

Bind:AddBind({
    Name = "Break Houses",
    Default = Enum.KeyCode.Y,
    Callback = function()
        breakhouse("auto")
    end
})

var("ov").cam.FieldOfView = 70

Bind:AddBind({
    Name = "Zoom",
    Default = Enum.UserInputType.MouseButton3,
    Callback = function()
        if zmtg then
            zmtg = false
            return
        end
        zmtg = true

        cm(var("UIS"), "InputChanged", function(input)
            if zmtg and input.UserInputType == Enum.UserInputType.MouseWheel then
                var("ov").cam.FieldOfView = math.clamp(
                    var("ov").cam.FieldOfView - (input.Position.Z * 3),
                    1, 70
                )
            end
        end, "zmscrl")

        repeat
            task.wait()
        until not zmtg

        cm("zmscrl", "disc")

        repeat
            var("ov").cam.FieldOfView += (70 - var("ov").cam.FieldOfView) * 0.2
            task.wait(0.01)
        until math.abs(var("ov").cam.FieldOfView - 70) <= 0.1
        var("ov").cam.FieldOfView = 70
    end
})

Bind:AddBind({
    Name = "Kill Aura (Once)",
    Default = Enum.KeyCode.H,
    Callback = function()
        pbkaura()
    end
})

Bind:AddSection()

Bind:AddBind({
    Name = "TP part",
    Default = Enum.KeyCode.F7,
    Callback = function()
        tpprt()
    end
})

local tpplocd

tpplocd = Bind:AddDropdown({
    Name = "Select Location",
    Default = "Void",
    Options = {"Spawn", "Green House", "Red House", "Farm", "Blue House", "Chinese House", "Purple House", "Green House2", "--------------------", "Ice Mountain", "Secret1", "Secret2", "Sky Island", "Broken Bridge", "Cofre", "Sky", "Void", "Hide"},
    Callback = function(value)
        if value == "--------------------" then
            notify("Note!", "This is just a space :D")
            tpplocd:Set("Void")
        else
            var("Valores").tpprtloc = value
        end
    end
})

--///                                                                  
Esp:ColorLabel("ESP Tab", nil, "Center")
Esp:AddSection()

var("Drops").ETDpdown = Esp:AddDropdown({
    Name = "Select Player",
    Default = "",
    Options = GPNames(),
    Searchable = true,
    Callback = function(Value)
        var("ESPVdown", var("ps")[string.match(Value, "^(.-)%s*%(")]) 
    end
})

Esp:AddToggle({
    Name = "Esp Player",
    Default = false,
    Callback = function(Value)
        if Value then
                tm.Add(var("paths").PlrsWH, var("ESPVdown"))
            setp(var("ESPVdown"), true)
        else
            tm.Remove(var("paths").PlrsWH, var("ESPVdown"))
            setp(var("ESPVdown"), false)
        end
    end
})

Esp:AddToggle({
    Name = "Esp All",
    Default = false,
    Callback = function(Value)
        if Value then
            setp(nil, true)
        else
            setp(nil, false)
        end
    end
})

Esp:AddSection()

var("Valores").ColorDp = "FillColor"

Esp:AddDropdown({
    Name = "Color",
    Default = "Fill",
    Options = {"Fill", "Outline"},
    Callback = function(Value)
        var("Valores").ColorDp = Value
    end
})

Esp:AddColorpicker({
    Name = "Color Picker",
    Default = Color3.fromRGB(0, 0, 0),
    Callback = function(Value)
        if var("Valores").ColorDp == "Fill" then
            var("Valores").fillc = Value
        else
            var("Valores").outc = Value
        end
        updall()
    end
})

Esp:AddSection()

var("Valores").TransDp = "FillTransparency"

Esp:AddDropdown({
    Name = "Transparency",
    Default = "FillT",
    Options = {"FillT", "Outline"},
    Callback = function(Value)
        var("Valores").TransDp = Value 
    end
})

Esp:AddSlider({
    Name = "Transparency",
    Min = 0,
    Max = 1,
    Default = 0.7,
    Increment = 0.1,
    ValueName = "value",
    Callback = function(Value)
        if var("Valores").TransDp == "FillT" then
            var("Valores").fillt = Value
        else
            var("Valores").outt = Value
        end
        updall()
    end
})

Esp:AddSection()

Esp:AddDropdown({
    Name = "DepthMode",
    Default = "AlwaysOnTop",
    Options = {"AlwaysOnTop", "Occluded"},
    Callback = function(Value)
        var("Valores").depth = Enum.HighlightDepthMode[Value]
    end
})

Esp:AddSection()

Esp:AddToggle({
    Name = "PCLD",
    Default = false,
    Callback = function(Value)
        var("Toggle", "PCLDESP", Value)
        for _, i in ipairs(var("paths").SelBoxs) do
            i.Visible = Value
        end
    end
})
--///                                                                  
Config:ColorLabel("Config Tab", nil, "Center")
Config:AddSection()

Config:ColorLabel("UI Customization", nil, "Center")
Config:FreeMouseDrp()

Config:AddUiBind()

Config:AddSection()
Config:AddTextbox({
    Name = "Window Icon",
    Default = "",
    TextDisappear = false,
    BackGrountText = "rbxassetid://...",
    Callback = function(Value)
        if Value == "" then
            Window:ChangeIcon("114143041236784")
            return
        end
        Window:ChangeIcon(tostring(Value))
    end
})

Config:AddTextbox({
    Name = "Window Name",
    Default = "",
    TextDisappear = false,
    BackGrountText = "Name",    
    Callback = function(Value)
        if Value == "" then
            Window:SetName(
                {"Fire Hub", "#FFFFFF"}
            )
            return
        end
        Window:SetName(
            {tostring(Value), "#FFFFFF"}
        )
    end
})
Config:AddSection()
Config:AddSmartTheme()
Config:AddSection()

Config:ColorLabel("Whitelist", nil, "Center")

var("DropsO").OwhtDrop = Config:AddDropdown({
    Name = "Select Player",
    Default = "",
    Options = mwtlist(nil, "Out"),
    Searchable = true,
    Callback = function(Value)
        local pname = string.match(Value, "^(.-)%s*%(")
        mwtlist(pname, "Add", var("DropsO").OwhtDrop)
    end
})

var("DropsI").IwhtDrop = Config:AddDropdown({
    Name = "Whitelist",
    Default = "",
    Options = mwtlist(nil, "In"),
    Searchable = true,
    Callback = function(Value)
        local pname = string.match(Value, "^(.-)%s*%(")
        mwtlist(pname, "Remove", var("DropsI").IwhtDrop)
    end
})
Config:AddSection()
Config:ColorLabel("Map / Configs", nil, "Center")

Config:AddToggle({
    Name = "Void Kill",
    Default = false,
    Callback = function(Value)
        workspace.FallenPartsDestroyHeight = Value and -100 or -50000
    end
})

Config:AddToggle({
    Name = "Break Gucci(On Destroyed Blob)",
    Default = true,
    Callback = function(Value)
        var("Toggle").BKgc = Value
    end
})

Config:AddSection()
Config:AddParagraph(
    "Lasts Updates\n |\n v",
    "Version 1.9 -- Whitelist\nVersion 2 -- Alot..", "Center"
)
Config:AddSection("Version 2.1", "Right", 10)
cm("plradded", "Add", UPDDrops)
OrionLib:Init() 
--! Creator :: firemax
--todo order // dropdowns