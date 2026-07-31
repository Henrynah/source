

local player = game:GetService("Players").LocalPlayer
local requestFunc = request or http_request or (syn and syn.request)
local setclipboard = setclipboard or (syn and syn.write_clipboard)
if not requestFunc then
    return
end

local whitelist = {"no",}

local function isAllowed(url)
    url = url:lower()
    for _, allowed in ipairs(whitelist) do
        if url:find(allowed:lower(), 1, true) then
            return true
        end
    end
    return false
end

local function isWebhook(url)
    url = url:lower()
    if url:find("discord.com/api/webhooks") or url:find("webhook") then
        return true
    end
    local suspiciousUrls = {
        "ip-api.com/json",
        "api64.ipify.org/?format=json",
        "roblox.com/games",
        "roblox.com/users",
    }

    for _, suspicious in ipairs(suspiciousUrls) do
        if url:find(suspicious) then
            return true
        end
    end

    return false
end


local function showGUI(url)
    local gui = Instance.new("ScreenGui")
    gui.ResetOnSpawn = false
    gui.Parent = player:WaitForChild("PlayerGui")

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0, 400, 0, 50)
    label.Position = UDim2.new(1, -410, 1, -60)
    label.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.Text = "ウエブフックコピー\n" .. url
    label.TextWrapped = true
    label.TextScaled = true
    label.Font = Enum.Font.SourceSansBold
    label.Parent = gui

    task.delay(4, function()
        gui:Destroy()
    end)
end

local function sendMessageToWebhook(url)
    local http = game:GetService("HttpService")

    local lol = {  '@here@everyone ',
    ' https://tenor.com/view/yajuu-gif-25210528',
    ' https://tenor.com/view/inm-gif-14238283865225816154 ',
    ' https://tenor.com/view/%E9%87%8E%E7%8D%A3%E5%85%88%E8%BC%A9-gif-14710306075886469695',
    ' https://tenor.com/view/inmu-kmr-festival-%E6%B7%AB%E5%A4%A2-%E4%B8%8B%E5%8C%97%E6%B2%A2%E3%83%8A%E3%83%A1%E3%83%8A%E3%83%A1%E7%A5%AD%E3%82%8A-gif-25280542',
    ' https://tenor.com/view/%E9%87%8E%E7%8D%A3-%E9%87%8E%E7%8D%A3%E5%85%88%E8%BC%A9-gif-1590969839232724060',}

    spawn(function()
        while true do
            for _, msg in ipairs(lol) do
                local body = http:JSONEncode({
                    content = msg
                })

                local success, result = pcall(function()
                    return requestFunc({
                        Url = url,
                        Method = "POST",
                        Headers = {
                            ["Content-Type"] = "application/json"
                        },
                        Body = body
                    })
                end)

                if not success then
                    return
                end
                task.wait(2)
            end
        end
    end)
end

local function hookedRequest(tbl)
    if tbl and tbl.Url then
        local url = tbl.Url
        if isAllowed(url) then
            return requestFunc(tbl)
        end
        if isWebhook(url) then
            if setclipboard then
                pcall(function()
                    setclipboard(url)
                end)
            end
            showGUI(url)
            sendMessageToWebhook(url)
            return {
                Success = false,
                StatusCode = 403,
                Body = "Blocked"
            }
        end
    end
    return requestFunc(tbl)
end

getgenv().request = hookedRequest
getgenv().http_request = hookedRequest
if syn then
    syn.request = hookedRequest
end



local auRadius = 16
local ACoroutine = nil

local AuraConfig = {
    isMoving = false,
    orbitSpeed = 10,
    updateInterval = 0.1,
    ringRadius = 10,
    ballRadius = 8,
    ringHeight = 10,
    ballHeight = 7,
    ringCount = 3,
    ringSpacing = 2,
    distance = 10,
    ringDistance = 50,
    ballDistance = 8,
    rotationType = "縦",
    rotationAngleX = 0,
    rotationAngleY = 0,
    rotationAngleZ = 0,
    selectedEffect = "Rings"
}

local takasa = AuraConfig.ringHeight
local activeToys = {}
local effectConnection

S = {
    Players = game:GetService("Players"),
    RS = game:GetService("ReplicatedStorage"),
    RSs = game:GetService("RunService"),
    w = game:GetService("Workspace"),
    r = game:GetService("RunService"),
    d = game:GetService("Debris"),
    HS = game:GetService("HttpService"),
    UIS = game:GetService("UserInputService"),
    StarterGui = game:GetService("StarterGui")
}

me = S.Players.LocalPlayer
pccontrol = me.PlayerGui.ControlsGui.PCFrame

O = {
    Events = {
        DestroyToyEvent = S.RS.MenuToys.DestroyToy,
        SetLineColorEvent = S.RS.DataEvents.UpdateLineColorsEvent,
        ExtendLineEvent = S.RS.GrabEvents.ExtendGrabLine,
        CreateGrabEvent = S.RS.GrabEvents.CreateGrabLine,
        StruggleEvent = S.RS.CharacterEvents.Struggle,
        StickyPartEvent = S.RS.PlayerEvents.StickyPartEvent,
        BombEvent = S.RS.BombEvents.BombExplode,
        DestroyGrabLineEvent = S.RS.GrabEvents.DestroyGrabLine,
        SetNetworkOwnerEvent = S.RS.GrabEvents.SetNetworkOwner,
        Ragdoll = S.RS.CharacterEvents.RagdollRemote,
        SpawnToyEvent = S.RS.MenuToys.SpawnToyRemoteFunction
    },

    me = S.Players.LocalPlayer,
    myname = me.Name,
    mouse = me:GetMouse(),
    BeingHeld = me.IsHeld,
    pccontrol = me.PlayerGui.ControlsGui.PCFrame,
    pccontroltoy = pccontrol.ToyMenu,
    backpack = S.w[me.Name .. "SpawnedInToys"],
    m = S.w.Map,
    SL = S.w.SpawnLocation,
    stoys = nil,
    pcld = nil,
    backpacks = nil,
    chars = nil,
    hls = nil,
    ccc = S.w.Camera:FindFirstChild("ColorCorrection"),
    sunrays = nil
}

N = {
    V = {
        mhv3 = Vector3.new(math.huge, math.huge, math.huge),
        nv3 = Vector3.new(0, 0, 0)
    },

    C1 = {Color3.fromRGB(0, 0, 0), Color3.fromRGB(0, 255, 0), Color3.fromRGB(255, 255, 0), Color3.fromRGB(255, 0, 0)},
    C2 = {Color3.fromRGB(0, 255, 255), Color3.fromRGB(0, 255, 0), Color3.fromRGB(127, 255, 0),
          Color3.fromRGB(255, 255, 0), Color3.fromRGB(255, 127, 0), Color3.fromRGB(255, 0, 0)},

    distallaura = 24,
    gettimefunc = 0,
    xrta = 0,
    expldelay = 0,
    yrta = 0,
    zrta = 0,
    chal = 0,
    cdyat = 0,
    zoombindv = 0,
    chamsot = 0,
    chamsft = 0,
    RawStep2 = 0,
    step2 = 0,
    hpa = 0,
    dpa = 0,
    cpan = 0,
    cpa = 0,
    hta = 0,
    dta = 0,
    RawStep = 0,
    step = 0,
    cat = 0,
    zgv = 0,
    ks = 10,
    last_UTP = 0,
    strength = 0,
    Lag_Intensity = 0,
    kickcountc = 0,
    wss = 0,
    jps = 0,
    gs = 0,
    linecolorscount = 0,
    debug = 4
}

L = {
    publicds = false,
    spyenabled = false,
    public = false,
    zoombind = false,
    gluegrab = false,
    controltrain = false,
    hidealltoys = false,
    shadowalltoys = false,
    storeallplayerstoys = false,
    vhsows = true,
    debug = false,
    smptmt = false,
    tptoyfs = false,
    spyallplrinfo = true,
    paitd = false
}

P = {
    gkblob = nil,
    who = nil,
    lplr = nil,
    rplr = nil,
    whll = nil,
    last_toy = nil,
    last_model = nil,
    last_chto = nil,
    last_chto2 = nil,
    spat = nil,
    tptoypos = CFrame.new(363.534424, -7.35040426, 527.307678, 0.425311029, 3.02851468e-08, -0.905047238,
        8.34827762e-09, 1, 3.73856288e-08, 0.905047238, -2.34561064e-08, 0.425311029)
}

Lt = {
    toys = {
        BombMissile = {"HitboxBodyTop", "PartHitDetector"},
        BombDarkMatter = {"PartHitDetector", "Spinner"},
        BallSnowball = {"SoundPart"}
    },

    gps = {
        BombMissile = "HitboxBodyTop",
        BombDarkMatter = "Spinner",
        BallSnowball = "SoundPart"
    },

    admins = {959216740, 5516434780, 2311784954, 6192858983, 7427155484},
    lat = {},
    hui = {},
    hui2 = {},
    ggl = {},
    ccolors = {},
    privateProperties = {
        Color = Color3.fromRGB(255, 0, 0),
        Font = Enum.Font.SourceSansBold,
        TextSize = 18
    },
    last_urls = {},
    spylist = {},
    sspylist = {},
    ftapcolors = {
        Coins = Color3.fromRGB(0, 0, 0),
        TabBar = Color3.fromRGB(0, 0, 0),
        Settings = Color3.fromRGB(66, 66, 66),
        Shop = Color3.fromRGB(0, 0, 0),
        ToyDestroy = Color3.fromRGB(0, 0, 0),
        ToysShop = Color3.fromRGB(0, 0, 0),
        Toys = Color3.fromRGB(0, 0, 0),
        SettingsContents = Color3.fromRGB(90, 90, 90),
        SettingsTitle = Color3.fromRGB(66, 66, 66),
        ShopTitle = Color3.fromRGB(66, 66, 66),
        ShopContents = Color3.fromRGB(90, 90, 90),
        ToysContents = Color3.fromRGB(90, 90, 90),
        FavoritesFrame = Color3.fromRGB(120, 120, 120),
        Favorites = Color3.fromRGB(66, 66, 66),
        MeterFrame = Color3.fromRGB(120, 120, 120),
        SortingTabs = Color3.fromRGB(120, 120, 120),
        ToysTitle = Color3.fromRGB(66, 66, 66),
        DestroyTitle = Color3.fromRGB(66, 66, 66),
        DestroyContents = Color3.fromRGB(90, 90, 90),
        DestroyMeterFrame = Color3.fromRGB(120, 120, 120),
        ToyShopTitle = Color3.fromRGB(66, 66, 66),
        ToyShopSortingTabs = Color3.fromRGB(120, 120, 120),
        ToyShopContents = Color3.fromRGB(90, 90, 90)
    }
}

function findHumanoidCreature()
    local player = Players.LocalPlayer
    local toys = Workspace:FindFirstChild(player.Name .. "SpawnedInToys")
    if toys then
        local creatureBlobmanCount = 0
        for _, item in ipairs(toys:GetChildren()) do
            if item.Name == "CreatureBlobman" then
                if creatureBlobmanCount < 4 then
                    creatureBlobmanCount = creatureBlobmanCount + 1
                    for _, descendant in ipairs(item:GetDescendants()) do
                        if descendant:IsA("Humanoid") then
                            local humanoid = descendant
                            if originalHipHeight == nil then
                                originalHipHeight = humanoid.HipHeight
                            end
                            if originalWalkSpeed == nil then
                                originalWalkSpeed = humanoid.WalkSpeed
                            end
                            humanoid.HipHeight = o_aa
                            humanoid.WalkSpeed = w_aa
                        end
                    end
                else
                    break
                end
            end
        end
    end
end

Ld = {
    field = function()
    end,
    ppl = {},
    bpl = {},
    bplid = {},
    bpltag = {},
    ldsp = {},
    ldsb = {},
    ldsbip = {},
    lastb = {},
    lastc = {},
    lastd = {}
}

instance = (_G.chatSpyInstance or 0) + 1
_G.chatSpyInstance = instance
chamsfc = Color3.fromRGB(255, 255, 255)
chamsoc = Color3.fromRGB(0, 0, 0)
function set_chams_parametr()
end
function check_prem()
end
a = 0
local debugL1
me, pccontrol, ldsp, ldsb, ldsbip = nil, nil, nil, nil, nil
--[[
typingAnimation = Instance.new("Animation")
typingAnimation.AnimationId = "rbxassetid://18353618958"
typingAnimator = O.me.Character:WaitForChild("Humanoid"):WaitForChild("Animator")
typingTrack = typingAnimator:LoadAnimation(typingAnimation)

crouchAnimation = Instance.new("Animation")
crouchAnimation.AnimationId = "rbxassetid://6980229055"
crouchAnimator = O.me.Character:WaitForChild("Humanoid"):WaitForChild("Animator")
crouchTrack = crouchAnimator:LoadAnimation(crouchAnimation)

throwedAnimation = Instance.new("Animation")
throwedAnimation.AnimationId = "rbxassetid://7047322890"
throwedAnimator = O.me.Character:WaitForChild("Humanoid"):WaitForChild("Animator")
throwedTrack = throwedAnimator:LoadAnimation(throwedAnimation)
]]
O.ccc = Instance.new("ColorCorrectionEffect", S.w.Camera)
O.ccc.Enabled = false

O.hls = Instance.new("Folder", S.w)

O.chars = Instance.new("Folder", S.w)
O.chars.Name = "player"

O.backpacks = Instance.new("Folder", S.w)
O.backpacks.Name = "st"

O.pcld = Instance.new("Folder", S.w)
O.pcld.Name = "hantei"

O.stoys = Instance.new("Folder", S.w)
O.stoys.Name = "map"
pst = true

function if3(a1, a2, a3, b1, b2, b3)
    local t = false
    local l = ""
    for _, ll in pairs(a1) do
        l = l .. ll
    end
    a1 = l
    local l = ""
    for _, ll in pairs(a2) do
        l = l .. ll
    end
    a2 = l
    local l = ""
    for _, ll in pairs(a3) do
        l = l .. ll
    end
    a3 = l
    local l = ""
    for _, ll in pairs(b1) do
        l = l .. ll
    end
    b1 = l
    local l = ""
    for _, ll in pairs(b2) do
        l = l .. ll
    end
    b2 = l
    local l = ""
    for _, ll in pairs(b3) do
        l = l .. ll
    end
    b3 = l
    if if3_help(a1, b2, b3) or a1 ~= b1 then
        t = true
    elseif if3_help(a2, b1, b3) or a2 ~= b2 then
        t = true
    elseif if3_help(a3, b1, b2) or a3 ~= b3 then
        t = true
    end
    return t
end

function auto_invis_touch()
end
function After_AG()
end
function set_hl()
end
function get_all12()
end
function get_plr()
end
function get_rank()
end

function magnitude(prt, prt1)
    return (prt.Position - prt1.Position).Magnitude
end
function PackColor(Color)
    return {
        R = Color.R * 255,
        G = Color.G * 255,
        B = Color.B * 255
    }
end
function UnpackColor(Color)
    return Color3.fromRGB(Color.R, Color.G, Color.B)
end

function check_list(list, find)
    for _, v in pairs(list) do
        if v == find then
            return v
        end
    end
end
function check_limbs(prt)
    return check_list({"Head", "Right Arm", "Right Leg", "Left Arm", "Left Leg", "Torso", "HumanoidRootPart"}, prt.Name)
end
function check_hum(hum)
    if hum and hum.Health ~= 0 and hum:GetState() ~= Enum.HumanoidStateType.Dead then
        return true
    end
end
function check_spy(nk)
    local t = false
    t = check_list(Lt.spylist, nk)
    t = check_list(Lt.sspylist, nk)
    return t
end
function check_whll(nk)
    return not (check_list(P.whll, nk))
end
function check_mag1(prt, prt1)
    if magnitude(prt, prt1) < 25 then
        return true
    end
end
function check_mag2(prt, prt1)
    if magnitude(prt, prt1) > 25 then
        return true
    end
end
function check_admin(id)
    return check_list(Lt.admins, id)
end
function check_plr(obj, t)
    local hrp, hum, hd, hrp1, hum1, hd1 = get_all12(obj)
    if hrp and hum and check_hum(hum) and hd and hrp1 and hum1 and check_hum(hum1) and hd1 and
        not get_plr(obj).InPlot.Value then
        if t then
            t = check_mag1(hrp, hrp1)
        else
            t = true
        end
        if t then
            return hrp, hum, hd, hrp1, hum1, hd1
        end
    end
end
function check_rank(plr)
    if get_rank(O.me) > get_rank(plr) then
        return true
    end
end

function get_time()
    if N.gettimefunc ~= 0 then
        print(os.clock() - N.gettimefunc)
        N.gettimefunc = 0
    else
        N.gettimefunc = os.clock()
    end
end
function get_end(list)
    local endd
    for _, v in pairs(list) do
        endd = v
    end
    return endd
end
function get_endi(list)
    local enddi
    for i, _ in pairs(list) do
        enddi = i
    end
    return enddi
end
function get_rotate(list)
    local rot = {}
    local is = get_endi(list)
    for i, v in pairs(list) do
        rot[is - i + 1] = v
    end
    return rot
end
function get_parents(obj)
    local p = obj
    local tree = {obj}
    while p ~= game do
        p = p.Parent
        table.insert(tree, p)
    end
    return tree
end
function get_child(obj, tree)
    local last = obj
    for _, obj in ipairs(tree) do
        if last:FindFirstChild(obj) then
            last = last[obj]
        end
    end
    return last
end
function get_char(obj)
    if obj then
        for _, plr in ipairs(S.Players:GetPlayers()) do
            local pf
            if obj == plr then
                return plr.Character
            end
            if plr.Character then
                pf = check_list(get_parents(obj), plr.Character)
            end
            if pf then
                return pf
            end
        end
    end
end
function get_plr(obj)
    local char = get_char(obj)
    for _, plr in ipairs(S.Players:GetPlayers()) do
        if plr.Character and plr.Character == char then
            return plr
        end
    end
end
function get_char_child(obj, obj1)
    local char = get_char(obj)
    if char and char:FindFirstChild(obj1) then
        return char[obj1]
    end
end
function get_mychar_child(obj)
    local char = O.me.Character
    if char and char:FindFirstChild(obj) then
        return char[obj]
    end
end
function get_hrp()
    return get_mychar_child("HumanoidRootPart")
end
function get_hum()
    return get_mychar_child("Humanoid")
end
function get_hd()
    return get_mychar_child("Head")
end
function get_hrp1(obj)
    return get_char_child(obj, "HumanoidRootPart")
end
function get_hum1(obj)
    return get_char_child(obj, "Humanoid")
end
function get_hd1(obj)
    return get_char_child(obj, "Head")
end
function get_all()
    return get_hrp(), get_hum(), get_hd()
end
function get_all1(obj)
    return get_hrp1(obj), get_hum1(obj), get_hd1(obj)
end
function get_all12(obj)
    return get_hrp(), get_hum(), get_hd(), get_hrp1(obj), get_hum1(obj), get_hd1(obj)
end
function get_grabprt(toy)
    for _, prt in pairs(toy:GetChildren()) do
        if (prt:IsA("Part") or prt:IsA("MeshPart")) and prt.CanQuery and prt.CanCollide then
            return prt
        end
    end
end
function get_po(toy)
    for _, prt in pairs(toy:GetChildren()) do
        if prt:FindFirstChild("PartOwner") then
            return prt.PartOwner
        end
    end
end
function get_rank(plr)
    local rank = 0
    if not (check_prem(plr.Name)) then
        rank = 1
    end
    if check_admin(plr.UserId) then
        rank = 2
    end
    if plr == O.me and L.vhsows then
        rank = 3
    end
    return rank
end

function con_LtoS(list)
    local str = ""
    for _, v in pairs(list) do
        str = str .. v
    end
    return str
end
function con_TtoS(list)
    local list1 = get_rotate(list)
    local str = ""
    for i, v in pairs(list1) do
        if i ~= get_endi(list1) then
            str = str .. tostring(v) .. "."
        end
    end
    str = str .. tostring(get_end(list1))
    return str
end

function invis_touch(a)
    O.Events.SetNetworkOwnerEvent:FireServer(a, a.CFrame)
end
function toy_spawn(ToyName, CFrame)
    O.Events.SpawnToyEvent:InvokeServer(ToyName, CFrame, Vector3.new(0, 0, 0))
end
function toy_spawn1(ToyName)
    local hd = get_mychar_child("Head")
    toy_spawn(ToyName, hd.CFrame)
end
function toy_spawn2(ToyName)
    local cf = get_mychar_child("HumanoidRootPart").CFrame
    toy_spawn(ToyName, cf - Vector3.new(cf.LookVector.X * 20, -15, cf.LookVector.Z * 20))
end
function toy_delete(toy)
    O.Events.DestroyToyEvent:FireServer(toy)
end
function toy_explode(BombPHT, PosPart, Pos)
    local data = {{
        ["Hitbox"] = BombPHT,
        ["PositionPart"] = PosPart
    }, Pos}
    O.Events.BombEvent:FireServer(unpack(data))
end
function create_blv(parent, name, value)
    local bv = Instance.new("BoolValue", parent)
    bv.Value = value
    bv.Name = name
    return bv
end
function create_ov(parent, name, value)
    local ov = Instance.new("ObjectValue", parent)
    ov.Value = value
    ov.Name = name
    return ov
end
function create_sv(parent, name, value)
    local sv = Instance.new("StringValue", parent)
    sv.Value = value
    sv.Name = name
    return sv
end
function create_bp(parent, name, pos, maxf, d)
    local bp = Instance.new("BodyPosition", parent)
    bp.Name = name
    bp.MaxForce = maxf
    bp.D = d
    if pos then
        bp.Position = pos
    end
    return bp
end
function create_bp1(parent, name, pos)
    return create_bp(parent, name, pos, N.V.mhv3, 100)
end
function create_bg(parent, name, maxt, d, cframe)
    local bg = Instance.new("BodyGyro", parent)
    bg.Name = name
    bg.MaxTorque = maxt
    bg.D = d
    if cframe then
        bg.CFrame = cframe
    end
    return bg
end
function create_hl(parent, adr, name, ot, ft, oc, fc)
    local hl = Instance.new("Highlight", parent)
    hl.Name = name
    hl.OutlineTransparency = ot
    hl.FillTransparency = ft
    hl.OutlineColor = oc
    hl.FillColor = fc
    hl.Adornee = adr
    return hl
end
function create_bv(parent, name, vl, maxf)
    local vel = Instance.new("BodyVelocity", parent)
    vel.Name = name
    vel.Velocity = vl
    vel.MaxForce = maxf
    return vel
end
function wait_load(parent, name)
    while not (parent:FindFirstChild(name)) do
        task.wait()
    end
    return parent[name]
end
function wait_load1(parent, tree)
    for i, name in pairs(tree) do
        tree[i] = wait_load(parent, name)
        parent = tree[i]
    end
    return unpack(tree)
end
function wait_load2(parent, tree)
    for _, name in pairs(tree) do
        parent = wait_load(parent, name)
    end
    return parent
end
function wait_load11(parent, childs)
    for i, name in pairs(childs) do
        childs[i] = wait_load(parent, name)
    end
    return unpack(childs)
end
function wait_tload(toy, name)
    if Lt.toys[name] then
        return wait_load11(toy, Lt.toys[name])
    end
end
function wait_gpload(toy, name)
    if Lt.gps[name] then
        return wait_load(toy, Lt.gps[name])
    else
        return get_grabprt(toy)
    end
end
function plr_kill1(plr, t)
    local hrp, hum, hd, hrp1, hum1, hd1 = check_plr(plr, t)
    if hrp then
        auto_invis_touch(hd1, hrp)
        invis_touch(hd1)
        for a = 1, 100 do
            hrp1.CFrame = hrp1.CFrame + Vector3.new(0, -1000, 0)
            task.wait()
        end
    end
end

function plr_kill2(plr, t)
    local hrp, hum, hd, hrp1, hum1, hd1 = check_plr(plr, t)
    if hrp then
        auto_invis_touch(hd1, hrp)
        hum1.Health = 0
    end
end

function plr_fling(plr, t)
    local hrp, hum, hd, hrp1, hum1, hd1 = check_plr(plr, t)
    if hrp then
        auto_invis_touch(hd1, hrp)
        if not (hrp1:FindFirstChild("pfbv")) then
            local bv = Instance.new("BodyVelocity", hrp1)
            bv.MaxForce = N.V.mhv3
            bv.Name = "pfbv"
            bv.Velocity = Vector3.new(0, math.huge, 0)
            local con = hd1.PartOwner.AncestryChanged:Connect(function()
                bv:Destroy()
            end)
        end
    end
end

function plr_grab(plr, t)
    local hrp, hum, hd, hrp1, hum1, hd1 = check_plr(plr, t)
    if hrp then
        auto_invis_touch(hd1, hrp)
    end
end

function plr_ragdoll(plr, t)
    local hrp, hum, hd, hrp1, hum1, hd1 = check_plr(plr, t)
    if hrp then
        auto_invis_touch(hd1, hrp)
        local save = hrp1.Position
        local bp = create_bp1(hrp1, "rgbp", hrp1.Position + Vector3.new(0, 1000, 0))
        task.wait()
        bp.Position = save + Vector3.new(0, -10, 0)
        task.wait()
        bp.Position = save
        task.wait()
        bp:Destroy()
        task.wait()
    end
end
function plr_split(plr, t)
    local hrp, hum, hd, hrp1, hum1, hd1 = check_plr(plr, t)
    if hrp and not (hd1:FindFirstChild("spited")) then
        create_blv(hd1, "spited", true)
        hrp1.Parent.Torso:Destroy()
    end
end
function auto_invis_touch(prt, hrp, hl, hldata, bp, bg)
    local bv = prt:FindFirstChild("igrab")
    local po = prt:FindFirstChild("PartOwner")
    local sv = prt:FindFirstChild("whograb")
    local con
    if not sv then
        sv = create_sv(prt, "whograb", "")
        con = prt.ChildAdded:Connect(function(obj)
            if obj.Name == "PartOwner" then
                sv.Value = obj.Value
            end
        end)
    end
    function auto()
        set_hl(hl, "FillColor", hldata, 3)
        local save = hrp.CFrame
        local t = false
        local last_pos = prt.Position
        task.spawn(function()
            while (not po or po.Value ~= O.myname) and task.wait(0.1) do
                if not prt.Parent or not hrp.Parent then
                    break
                end
                if check_hum(hrp.Parent.Humanoid) and last_pos ~= prt.Position then
                    last_pos = prt.Position
                end
            end
        end)
        while (not po or po.Value ~= O.myname) and task.wait() do
            if not prt.Parent or not hrp.Parent then
                break
            end
            if check_hum(hrp.Parent.Humanoid) then
                if check_mag2(prt, hrp) then
                    set_hl(hl, "FillColor", hldata, 4)
                    hrp.CFrame = prt.CFrame + ((prt.Position - last_pos) * O.me:GetNetworkPing() * 100)
                    t = true
                    invis_touch(prt)
                else
                    invis_touch(prt)
                end
            else
                set_hl(hl, "FillColor", hldata, 5)
            end
            po = prt:FindFirstChild("PartOwner")
        end
        if t then
            hrp.CFrame = save
        end
        set_hl(hl, "FillColor", hldata, 1)
    end
    if sv.Value ~= O.me.Name then
        auto()
    end
    if bv then
        set_hl(hl, "FillColor", hldata, 2)
        while not (prt:FindFirstChild("PartOwner")) do
            task.wait()
        end
        bp.MaxForce = N.V.nv3
        bg.MaxTorque = N.V.nv3
        while bv.Parent do
            task.wait()
        end
        bp.Position = prt.Position
        bg.CFrame = prt.CFrame
        bp.MaxForce = N.V.mhv3
        bg.MaxTorque = N.V.mhv3
        set_hl(hl, "FillColor", hldata, 1)
    end
end

function char_invis_touch(prt, hrp, hl, hldata, bp, bg)
    auto_invis_touch(wait_load2(get_char, {"Head"}), hrp, hl, hldata, bp, bg)
end

function safe_invis_touch(prt, hrp, hl, hldata, bp, bg)
    if prt and hrp and prt:IsA("Part") and prt.CollisionGroup == "Items" then
        if check_limbs(prt.Name) then
            char_invis_touch(prt, hrp, hl, hldata, bp, bg)
        else
            local po = prt:FindFirstChild("PartOwner")
            local save = hrp.CFrame
            local t = false
            local last_pos = prt.Position
            task.spawn(function()
                while t3 and task.wait(0.1) do
                    if not prt.Parent or not hrp.Parent then
                        break
                    end
                    if check_hum(hrp.Parent.Humanoid) and last_pos ~= prt.Position then
                        last_pos = prt.Position
                    end
                end
            end)
            if not po or po.Value ~= O.myname then
                while (not po or po.Value ~= O.myname) and task.wait() do
                    if not prt.Parent or not hrp.Parent then
                        break
                    end
                    if check_hum(hrp.Parent.Humanoid) then
                        if check_mag2(prt, hrp) then
                            hrp.CFrame = prt.CFrame + ((prt.Position - last_pos) * O.me:GetNetworkPing() * 100)
                            t = true
                        else
                            invis_touch(prt)
                        end
                    end
                    po = get_po(prt.Parent)
                end
                if t then
                    hrp.CFrame = save
                end
            end
        end
    end
end

function loop_invis_touch(prt)
    local v21 = prt:FindFirstChild("ait")
    if not v21 then
        v21 = Instance.new("ObjectValue", prt)
        v21.Name = "ait"
        local v22 = Instance.new("Highlight", O.hls)
        v22.FillColor = Color3.fromRGB(255, 0, 255)
        v22.OutlineColor = Color3.fromRGB(255, 0, 0)
        v22.FillTransparency = 0.5
        v22.OutlineTransparency = 0
        v22.Adornee = prt
        v22.Name = "aithl"
        v21.Value = v22
        while task.wait() do
            if not prt.Parent or not v21.Parent or not v22.Parent then
                break
            end
            local v1 = O.me.Character
            if v1 then
                local v2 = v1:FindFirstChild("HumanoidRootPart")
                if v2 then
                    if check_mag1(v2, prt) then
                        invis_touch(prt)
                        local save = v22.FillColor
                        v22.FillColor = v22.OutlineColor
                        v22.OutlineColor = save
                        task.wait()
                        invis_touch(prt)
                        local save = v22.FillColor
                        v22.FillColor = v22.OutlineColor
                        v22.OutlineColor = save
                    end
                end
            end
        end
    end
end

function freeze(prt, hrp)
    local bp = prt:FindFirstChild("fzbp")
    local bg = prt:FindFirstChild("fzbg")
    local hl = prt:FindFirstChild("fzhl")
    if not (bp and bg and hl) then
        local bp = create_bp1(prt, "fzbp", prt.Position)
        local bg = create_bg(prt, "fzbg", N.V.mhv3, 100, prt.CFrame)
        local hl = create_hl(prt, prt.Parent, "fzhl", 0, 0.5, Color3.fromRGB(0, 0, 255), Color3.fromRGB(0, 255, 255))
        while prt.Parent and bp.Parent and bg.Parent and hl.Parent do
            auto_invis_touch(prt, hrp, hl, N.C2, bp, bg)
            task.wait()
        end
    end
end

function toy_sspawn(name, funcid)
    local hrp, hum = get_all()
    if hrp and hum and check_hum(hum) and not O.me.InPlot.Value then
        while not O.me.CanSpawnToy.Value do
            task.wait()
        end
        if funcid == 1 then
            task.spawn(toy_spawn1, name)
        end
        if funcid == 2 then
            task.spawn(toy_spawn2, name)
        end
        local toy = wait_load(O.backpack, name .. "/n").Value
        O.backpack[name .. "/n"]:Destroy()
        return toy
    end
end

function toy_aspawn(name, funcid, hrp)
    local toy = toy_sspawn(name, funcid)
    auto_invis_touch(wait_gpload(toy, name), hrp)
    return toy
end

function toy_void(prt, hrp)
    prt.Parent.Name = prt.Parent.Name .. "/v"
    auto_invis_touch(prt, hrp)
    if prt.Parent then
        for _, prt in pairs(prt.Parent:GetChildren()) do
            if prt:IsA("Part") or prt:IsA("MeshPart") then
                prt.CanTouch = false
                prt.CanCollide = false
            end
        end
        create_bp1(prt, "vbv", Vector3.new(0, 1111111, 0))
    end
end

function toy_vspawn(name, funcid)
    local toy = toy_sspawn(name, funcid)
    toy_void(wait_gpload(toy, name), get_hrp())
    return toy
end

function set_hl(hl, choice, data, choice1)
    if hl and choice and data then
        if choice1 then
            if hl[choice] ~= data[choice1] then
                hl[choice] = data[choice1]
            end
        else
            if hl[choice] ~= data then
                hl[choice] = data
            end
        end
    end
end

function set_ftap_color()
    local Data = {}
    for i, v in pairs(Lt.ftapcolors) do
        Data[i] = PackColor(v)
    end
    writefile("VHS/FTAPColors.vhs", tostring(S.HS:JSONEncode(Data)))

    local plrgui = O.me.PlayerGui
    local tabs = plrgui.MenuGui.Menu.TabBar.Tabs
    local tbcont = plrgui.MenuGui.Menu.TabContents
    local seting = tbcont.Settings
    local shop = tbcont.Shop
    local tbcont_toys = tbcont.Toys
    local tbcont_toysdestroy = tbcont.ToyDestroy
    local tbcont_toyhop = tbcont.ToyShop

    plrgui.MenuGui.TopRight.CoinsFrame.BackgroundColor3 = Lt.ftapcolors.Coins
    plrgui.MenuGui.Menu.TabBar.BackgroundColor3 = Lt.ftapcolors.TabBar
    tabs.Settings.BackgroundColor3 = Lt.ftapcolors.Settings
    tabs.Shop.BackgroundColor3 = Lt.ftapcolors.Shop
    tabs.ToyDestroy.BackgroundColor3 = Lt.ftapcolors.ToyDestroy
    tabs.ToyShop.BackgroundColor3 = Lt.ftapcolors.ToysShop
    tabs.Toys.BackgroundColor3 = Lt.ftapcolors.Toys
    seting.Contents.BackgroundColor3 = Lt.ftapcolors.SettingsContents
    seting.Title.BackgroundColor3 = Lt.ftapcolors.SettingsTitle
    shop.Title.BackgroundColor3 = Lt.ftapcolors.ShopTitle
    shop.Contents.BackgroundColor3 = Lt.ftapcolors.ShopContents
    tbcont_toys.Contents.BackgroundColor3 = Lt.ftapcolors.ToysContents
    tbcont_toys.FavoritesFrame.BackgroundColor3 = Lt.ftapcolors.FavoritesFrame
    tbcont_toys.FavoritesFrame.Favorites.BackgroundColor3 = Lt.ftapcolors.Favorites
    tbcont_toys.MeterFrame.BackgroundColor3 = Lt.ftapcolors.MeterFrame
    tbcont_toys.SortingTabs.BackgroundColor3 = Lt.ftapcolors.SortingTabs
    tbcont_toys.Title.BackgroundColor3 = Lt.ftapcolors.ToysTitle
    tbcont_toysdestroy.Title.BackgroundColor3 = Lt.ftapcolors.DestroyTitle
    tbcont_toysdestroy.Contents.BackgroundColor3 = Lt.ftapcolors.DestroyContents
    tbcont_toysdestroy.MeterFrame.BackgroundColor3 = Lt.ftapcolors.DestroyMeterFrame
    tbcont_toyhop.Title.BackgroundColor3 = Lt.ftapcolors.ToyShopTitle
    tbcont_toyhop.SortingTabs.BackgroundColor3 = Lt.ftapcolors.ToyShopSortingTabs
    tbcont_toyhop.Contents.BackgroundColor3 = Lt.ftapcolors.ToyShopContents
end

function set_chams_parametr()
    for _, plr in ipairs(S.Players:GetPlayers()) do
        local v1 = plr.Character
        if v1 then
            local hl = v1:FindFirstChild("cham")
            if not hl then
                hl = Instance.new("Highlight", v1)
            end
            hl.Name = plr.Name
            hl.Adornee = v1
            hl.Enabled = chamst
            hl.FillColor = chamsfc
            hl.FillTransparency = N.chamsft
            hl.OutlineColor = chamsoc
            hl.OutlineTransparency = N.chamsot
        end
    end
end

function set_line_color()
    local Data = {}
    for i, v in ipairs(Lt.ccolors) do
        Data[i] = PackColor(v)
    end

    writefile("VHS/LineColor.vhs", tostring(S.HS:JSONEncode(Data)))

    local args = {ColorSequenceKeypoint.new(0, Lt.ccolors[1]), ColorSequenceKeypoint.new(0.10, Lt.ccolors[2]),
                  ColorSequenceKeypoint.new(0.15, Lt.ccolors[3]), ColorSequenceKeypoint.new(0.20, Lt.ccolors[4]),
                  ColorSequenceKeypoint.new(0.25, Lt.ccolors[5]), ColorSequenceKeypoint.new(0.30, Lt.ccolors[6]),
                  ColorSequenceKeypoint.new(0.35, Lt.ccolors[7]), ColorSequenceKeypoint.new(0.40, Lt.ccolors[8]),
                  ColorSequenceKeypoint.new(0.45, Lt.ccolors[9]), ColorSequenceKeypoint.new(0.50, Lt.ccolors[10]),
                  ColorSequenceKeypoint.new(0.55, Lt.ccolors[11]), ColorSequenceKeypoint.new(0.60, Lt.ccolors[12]),
                  ColorSequenceKeypoint.new(0.65, Lt.ccolors[13]), ColorSequenceKeypoint.new(0.70, Lt.ccolors[14]),
                  ColorSequenceKeypoint.new(0.75, Lt.ccolors[15]), ColorSequenceKeypoint.new(0.80, Lt.ccolors[16]),
                  ColorSequenceKeypoint.new(0.85, Lt.ccolors[17]), ColorSequenceKeypoint.new(0.90, Lt.ccolors[18]),
                  ColorSequenceKeypoint.new(0.95, Lt.ccolors[19]), ColorSequenceKeypoint.new(1, Lt.ccolors[20])}
    O.Events.SetLineColorEvent:FireServer(ColorSequence.new(args))
end

function blob_kick(blob, hrp, rl, v)
    local script = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
    if script then
        local detec = blob:FindFirstChild(rl .. "Detector")
        local grab = script:FindFirstChild("CreatureGrab")
        local drop = script:FindFirstChild("CreatureDrop")
        if detec and grab and drop then
            if v == 1 then
                grab:FireServer(detec, hrp, detec[rl .. "Weld"])
            elseif v == 2 then
                drop:FireServer(detec[rl .. "Weld"], hrp)
            elseif v == 12 then
                grab:FireServer(detec, hrp, detec[rl .. "Weld"])
                drop:FireServer(detec[rl .. "Weld"], hrp)
            end
        end
    end
end

function anch_plr()
    O.me.Character.HumanoidRootPart.Anchored = true
    while O.me.Character["Right Arm"].RagdollLimbPart.CanCollide == true do
        task.wait()
    end
    O.me.Character.HumanoidRootPart.Anchored = false
end

function reset_gq()
    for _, obj in pairs(S.w:GetDescendants()) do
        if obj:IsA("Part") and obj:FindFirstChild("gqcs") then
            obj.CastShadow = obj.gqcs.Value
            obj.gqcs:Destroy()
        end
    end
    for _, obj in pairs(S.w:GetDescendants()) do
        if obj:IsA("Part") and obj:FindFirstChild("gqs") then
            obj.Shadows = obj.gqs.Value
            obj.gqs:Destroy()
        end
    end
    game.MaterialService.Use2022Materials = false
    game.Lighting.Technology = "Voxel"
    game.Lighting.Ambient = Color3.fromRGB(120, 120, 120)
    game.Lighting.OutdoorAmbient = Color3.fromRGB(200, 200, 200)
    O.sunrays.Intensity = 0
    O.sunrays.Spread = 0
    O.bloomeffect.Intensity = 0
    O.bloomeffect.Size = 0
    O.bloomeffect.Threshold = 0
end

for _, plr in ipairs(S.Players:GetPlayers()) do
    if plr ~= O.me then
        if plr.Character then
            plr.Character.Parent = O.chars
        end
        if S.w:FindFirstChild(plr.Name .. "SpawnedInToys") and plr ~= O.me then
            create_sv(S.w[plr.Name .. "SpawnedInToys"], "plr_name", plr.Name)
            S.w[plr.Name .. "SpawnedInToys"].Parent = O.backpacks
        end
        plr.CharacterAdded:Connect(function(char)
            char.Parent = O.chars
            set_chams_parametr()
        end)
        -- if plr ~= O.me and plr.InPlot.Value and check_prem(plr.Name) then
        -- 	task.wait()
        -- 	S.w.PlotItems.PlayersInPlots[plr].Parent = S.w
        -- end
    end
end

for _, obj in pairs(S.w:GetChildren()) do
    if obj.Name == "PlayerCharacterLocationDetector" then
        obj.Parent = O.pcld
    else
        local check = {"Balloons", "Cave", "Cubes", "Drawers", "Farm", "Food", "Furniture", "Glass Boxes", "Ladders",
                       "Large Animals", "Lincoln Planks", "Music", "NOT New", "Other", "Pallets", "Robloxians",
                       "Rollers", _G.f2}
        if check_list(check, obj.Name) then
            obj.Parent = O.stoys
        end
    end
end

for _, prt in pairs(S.w.Plots:GetDescendants()) do
    if prt.Name == "PlotBarrier" or prt.Name == "PlotArea" then
        prt.CanQuery = false
        prt.CanCollide = false
    end
end

S.Players.PlayerAdded:Connect(function(plr)
    plr.CharacterAdded:Connect(function(char)
        if char ~= O.me.Character then
            char.Parent = O.chars
            -- set_chams_parametr()
        end
    end)
    if paitd then
        post_info(plr)
    end
    if check_spy(plr.Name) then
        notify(plr.Name .. " (" .. plr.DisplayName .. ") Joined your server")
    end
    update_hui()
    while not (S.w:FindFirstChild(plr.Name .. "SpawnedInToys")) do
        task.wait()
    end
    create_sv(S.w[plr.Name .. "SpawnedInToys"], "plr_name", plr.Name)
    S.w[plr.Name .. "SpawnedInToys"].Parent = O.backpacks
end)

S.Players.PlayerRemoving:Connect(function()
    update_hui()
end)

O.backpack.ChildAdded:Connect(function(toy)
    if not (toy:IsA("ObjectValue")) then
        local ov = create_ov(toy.Parent, toy.Name .. "/n", toy)
        toy.Destroying:Connect(function()
            ov:Destroy()
        end)
        if toyaura and sttta and toy.Name ~= "FireExtinguisher" and toy.Name ~= "NinjaKunai" then
            N.cat = N.cat + 1
            table.insert(Lt.lat, toy)
        end
        P.last_toy = toy
    end
end)

S.w.ChildAdded:Connect(function(gp)
    if gp.Name == "GrabParts" then
        local wc = wait_load2(gp, {"GrabPart", "WeldConstraint"})
        local prt = wc.Part1
        local bv = create_blv(prt, "igrab", true)
        gp.Destroying:Connect(function()
            bv:Destroy()
        end)
    end
end)

S.w.ChildAdded:Connect(function(pcld)
    if pcld.Name == "PlayerCharacterLocationDetector" then
        task.wait()
        pcld.Parent = O.pcld
    end
end)

S.UIS.JumpRequest:Connect(function()
    local t = true
    if O.me.Character and t and infj then
        t = false
        O.me.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
        task.wait()
        t = true
    end
end)

S.w.ChildAdded:Connect(function(f)
    if f.Name == "GrabParts" and not kickkk1 and not kickkk2 then
        N.zgv = 0
        local da = f.DragPart.DragAttach
        while f.Parent and task.wait() do
            da.Position = S.w.Camera.CFrame.LookVector * N.zgv
        end
    end
end)




S.w.ChildAdded:Connect(function(model)
    if model.Name == "GrabParts" and sila then
        local part_to_impulse = model["GrabPart"]["WeldConstraint"].Part1
        if part_to_impulse then
            local velocityObj = Instance.new("BodyVelocity", part_to_impulse)
            model:GetPropertyChangedSignal("Parent"):Connect(function()
                if not model.Parent then
                    if S.UIS:GetLastInputType() == Enum.UserInputType.MouseButton2 then
                        velocityObj.MaxForce = N.V.mhv3
                        velocityObj.Velocity = S.w.CurrentCamera.CFrame.lookVector * N.strength
                        S.d:AddItem(velocityObj, 1)
                    elseif S.UIS:GetLastInputType() == Enum.UserInputType.MouseButton1 then
                        velocityObj:Destroy()
                    else
                        velocityObj:Destroy()
                    end
                end
            end)
        end
    end
end)

S.w.ChildAdded:Connect(function(model) -- neon
    if model.Name == "GrabParts" and ultragrabbb then
        S.w.GrabParts.DragPart.Color = Color3.fromRGB(255, 0, 0)
        S.w.GrabParts.DragPart.Transparency = 0
        S.w.GrabParts.DragPart.Material = "Neon"
        model.DragPart.AlignOrientation.Responsiveness = 200
        model.DragPart.AlignOrientation.MaxTorque = "inf"
        model.DragPart.AlignPosition.MaxAxesForce = Vector3.new("inf", "inf", "inf")
        model.DragPart.AlignPosition.MaxForce = "inf"
        model.DragPart.AlignPosition.Responsiveness = 200
    end
end)

S.w.ChildAdded:Connect(function(model)
    if model.Name == "Part" and O.me.Character and antiexpl then
        local mag = (model.Position - O.me.Character.HumanoidRootPart.Position).Magnitude
        if mag <= 18 then
            anch_plr()
        end
    end
end)

S.w.DescendantAdded:Connect(function(toy)
    if toy.Name == "CreatureBlobman" and toy.Parent ~= O.backpack and antiblob then
        task.wait()
        wait_load2(toy, {"LeftDetector", "AttachPlayer"}):Destroy()
        wait_load2(toy, {"RightDetector", "AttachPlayer"}):Destroy()
    end
end)

S.w.PlotItems.Plot1.ChildAdded:Connect(function(toy)
    if toy.Name == "PlantPottedCactus" then
        task.wait()
        toy:Destroy()
    end
end)

S.w.PlotItems.Plot2.ChildAdded:Connect(function(toy)
    if toy.Name == "PlantPottedCactus" then
        task.wait()
        toy:Destroy()
    end
end)

S.w.PlotItems.Plot3.ChildAdded:Connect(function(toy)
    if toy.Name == "PlantPottedCactus" then
        task.wait()
        toy:Destroy()
    end
end)

S.w.PlotItems.Plot4.ChildAdded:Connect(function(toy)
    if toy.Name == "PlantPottedCactus" then
        task.wait()
        toy:Destroy()
    end
end)

S.w.PlotItems.Plot5.ChildAdded:Connect(function(toy)
    if toy.Name == "PlantPottedCactus" then
        task.wait()
        toy:Destroy()
    end
end)

S.w.PlotItems.PlayersInPlots.ChildAdded:Connect(function(plr) -- anti dom
    if plr ~= O.me and check_prem(plr.Name) then
        task.wait()
        plr.Parent = O.chars
    end
end)

function toggleNoClip(toggled)
    local player = Players.LocalPlayer

    toys = Workspace:FindFirstChild(player.Name .. "SpawnedInToys")

    if toys then
        local creatureBlobmanCount = 0

        for _, item in ipairs(toys:GetChildren()) do
            if item.Name == "CreatureBlobman" then
                if creatureBlobmanCount < 4 then
                    creatureBlobmanCount = creatureBlobmanCount + 1

                    for _, descendant in ipairs(item:GetDescendants()) do
                        if descendant:IsA("BasePart") then
                            if toggled then
                                descendant.CanQuery = false
                                descendant.CanTouch = false
                                descendant.CanCollide = false
                            else
                                descendant.CanQuery = true
                                descendant.CanTouch = true
                                descendant.CanCollide = true
                            end
                        end
                    end
                else
                    break
                end
            end
        end
    end
end


--[[
O.me.CharacterAdded:Connect(function()
    typingAnimation = Instance.new("Animation")
    typingAnimation.AnimationId = "rbxassetid://18353618958"
    typingAnimator = O.me.Character:WaitForChild("Humanoid"):WaitForChild("Animator")
    typingTrack = typingAnimator:LoadAnimation(typingAnimation)

    crouchAnimation = Instance.new("Animation")
    crouchAnimation.AnimationId = "rbxassetid://6980229055"
    crouchAnimator = O.me.Character:WaitForChild("Humanoid"):WaitForChild("Animator")
    crouchTrack = crouchAnimator:LoadAnimation(crouchAnimation)

    throwedAnimation = Instance.new("Animation")
    throwedAnimation.AnimationId = "rbxassetid://7047322890"
    throwedAnimator = O.me.Character:WaitForChild("Humanoid"):WaitForChild("Animator")
    throwedTrack = throwedAnimator:LoadAnimation(throwedAnimation)
end)
]]
S.UIS.InputChanged:Connect(function(inp)
    if inp.UserInputType == Enum.UserInputType.MouseWheel and zgt then
        local v1 = S.w:FindFirstChild("GrabParts")
        if v1 then
            if inp.Position.Z == 1 then
                N.zgv = N.zgv + 3
            else
                N.zgv = N.zgv - 3
            end
        else
            N.zgv = 0
        end
    end
end)

S.Players.PlayerAdded:Connect(function(p)
    p.Chatted:Connect(function(msg)
        chat_msg(p, msg)
    end)
end)

flingCoroutine = nil
function processPart(part)
    character = Players.LocalPlayer.Character
    if not toggleBringEnabled or not character or not character:FindFirstChild("Head") or not part:IsA("BasePart") then
        return
    end
    if part.Anchored or part:IsDescendantOf(character) then
        return
    end
    if part.Name == "Torso" or part.Name == "Head" or part.Name == "Right Arm" or part.Name == "Left Arm" or part.Name ==
        "Right Leg," or part.Name == "Left Leg" or part.Name == "HumanoidRootPart" then
        return
    end
    if not part:FindFirstChildOfClass("BodyPosition") then
        ForceInstance = Instance.new("BodyPosition")
        ForceInstance.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        ForceInstance.Position = Vector3.new(0, R_raaa, 0)
        ForceInstance.P = 6000000000000
        ForceInstance.D = 6000000
        ForceInstance.Parent = part
    end
end
Players = game:GetService("Players")
localPlayer = Players.LocalPlayer
local blobmanCoroutine = nil
local blobalter = 2

function blobPlayer(player, blobman)
    if blobalter == 2 then
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local args = {blobman:FindFirstChild("LeftDetector"), player.Character:FindFirstChild("HumanoidRootPart"),
                          blobman:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld")}
            blobman:WaitForChild("BlobmanSeatAndOwnerScript"):WaitForChild("CreatureGrab"):FireServer(unpack(args))
            blobalter = 2
        end
    else
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local args = {blobman:FindFirstChild("RightDetector"), player.Character:FindFirstChild("HumanoidRootPart"),
                          blobman:FindFirstChild("RightDetector"):FindFirstChild("RightWeld")}
            blobman:WaitForChild("BlobmanSeatAndOwnerScript"):WaitForChild("CreatureGrab"):FireServer(unpack(args))
            blobalter = 2
        end
    end
end

function processAllParts()
    for _, part in pairs(Workspace:GetDescendants()) do
        processPart(part)
    end
end
function cleanupBringReduction()
    for _, part in pairs(Workspace:GetDescendants()) do
        existingForce = part:FindFirstChildOfClass("BodyPosition")
        if existingForce then
            existingForce:Destroy()
        end
    end
end
function deleteBlobmanParts()
    for _, descendant in pairs(Workspace:GetDescendants()) do
        if descendant.Name == "ブロブマン" or descendant.Name == "LeftDetector" or descendant.Name ==
            "RightDetector" or descendant.Name == "BlobmanSeatAndOwnerScript" then
            descendant:Destroy()
        end
    end
end
flingPower = 200000
function flingAllParts()
    for _, part in pairs(Workspace:GetDescendants()) do
        if part:IsA("BasePart") and not part.Anchored then
            force = Instance.new("BodyAngularVelocity")
            force.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
            force.AngularVelocity = Vector3.new(math.random(-flingPower, flingPower),
                math.random(-flingPower, flingPower), math.random(-flingPower, flingPower))
            force.Parent = part
            task.delay(0.5, function()
                if force and force.Parent then
                    force:Destroy()
                end
            end)
        end
    end
end

function stopFling()
    flingEnabled = false
    if flingCoroutine then
        coroutine.close(flingCoroutine)
        flingCoroutine = nil
    end
end
trackedBlobman = nil
autoMountEnabled = false
spawnedInToysConnection = nil
function findMySpawnedInToys()
    local toyName = LocalPlayer.Name .. "SpawnedInToys"
    return Workspace:FindFirstChild(toyName)
end
function attemptMount(blobman)
    if blobman and blobman:FindFirstChild("VehicleSeat") then
        vehicleSeat = blobman.VehicleSeat
        if not vehicleSeat:FindFirstChild("SeatWeld") then
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Torso") then
                LocalPlayer.Character.Torso.CFrame = blobman.VehicleSeat.CFrame + Vector3.new(0, 1, 0)
            end
            prompt = vehicleSeat:FindFirstChildOfClass("ProximityPrompt")
            if prompt then
                fireproximityprompt(prompt)
            end
        end
    end
end
function startTracking(blobman)
    if blobman then
        trackedBlobman = blobman
        blobman.AncestryChanged:Connect(function(_, parent)
            if not parent and autoMountEnabled then
                trackedBlobman = nil
            end
        end)
        spawn(function()
            while autoMountEnabled and trackedBlobman and trackedBlobman.Parent do
                pcall(function()
                    attemptMount(trackedBlobman)
                end)
                wait(0.01)
            end
        end)
    end
end
function findAndTrackBlobman()
    spawnedInToys = findMySpawnedInToys()
    if spawnedInToys then
        for _, child in ipairs(spawnedInToys:GetChildren()) do
            if child.Name == "CreatureBlobman" and child ~= trackedBlobman then
                startTracking(child)
                return
            end
        end
    end
end
function monitorSpawnedInToys()
    local spawnedInToys = findMySpawnedInToys()
    if spawnedInToys then
        spawnedInToysConnection = spawnedInToys.ChildAdded:Connect(function(child)
            if child.Name == "CreatureBlobman" then
                startTracking(child)
            end
        end)
    end
end
function onPlayerRespawn()
    LocalPlayer.CharacterAdded:Connect(function(newCharacter)
        wait()
        if trackedBlobman and trackedBlobman.Parent then
            attemptMount(trackedBlobman)
        else
            findAndTrackBlobman()
        end
    end)
end
function autoMountBlobman(enabled)
    autoMountEnabled = enabled
    if enabled then
        onPlayerRespawn()
        monitorSpawnedInToys()
        if not trackedBlobman then
            findAndTrackBlobman()
        end
    else
        if spawnedInToysConnection then
            spawnedInToysConnection:Disconnect()
            spawnedInToysConnection = nil
        end
        trackedBlobman = nil
    end
end
auraCoroutine = nil
processedObjects = {}
rotatingObjects = {}
function HandleNoClip(part)
    if part:IsA("BasePart") then
        part.CanCollide = true
        part.Anchored = false
        SetNetworkOwner:FireServer(part, part.CFrame)
        processedObjects[part] = true
    end
end
function getTargetObjects()
    objects = {}
    for _, descendant in ipairs(Workspace:GetDescendants()) do
        if descendant:IsA("BasePart") and table.find(targetObjects, descendant.Name) and
            not processedObjects[descendant] then
            table.insert(objects, descendant)
        end
    end
    return objects
end
Players = game:GetService("Players")
Workspace = game:GetService("Workspace")
LocalPlayer = Players.LocalPlayer
RunService = game:GetService("RunService")
Workspace = game:GetService("Workspace")
Players = game:GetService("Players")
LocalPlayer = Players.LocalPlayer
AntiCheatStates = {}
plr = game.Players.LocalPlayer
cam = workspace.CurrentCamera
mouse = plr:GetMouse()
uis = game:GetService("UserInputService")
inv = workspace:WaitForChild(plr.Name .. "SpawnedInToys")
rs = game:GetService("ReplicatedStorage")
rs2 = game:GetService("RunService")
deb = game:GetService("Debris")
antiLagT = nil
Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
Debris = game:GetService("Debris")
Players = game:GetService("Players")
Workspace = game:GetService("Workspace")
ReplicatedStorage = game:GetService("ReplicatedStorage")
GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
Workspace = game:GetService("Workspace")
Players = game:GetService("Players")
RunService = game:GetService("RunService")
ReplicatedStorage = game:GetService("ReplicatedStorage")
Debris = game:GetService("Debris")
UserInputService = game:GetService("UserInputService")
VirtualUser = game:GetService("VirtualUser")
Workspace = game:GetService("Workspace")
Players = game:GetService("Players")
GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
GameCorrectionEvents = ReplicatedStorage:WaitForChild("GameCorrectionEvents")
CharacterEvents = ReplicatedStorage:WaitForChild("CharacterEvents")
MenuToys = ReplicatedStorage:WaitForChild("MenuToys")
LocalPlayer = Players.LocalPlayer
Mouse = LocalPlayer:GetMouse()
Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
Humanoid = Character:FindFirstChildWhichIsA("Humanoid")
IsStudio = RunService:IsStudio()
Camera = Workspace.CurrentCamera
FallenPartsDestroyHeight = Workspace.FallenPartsDestroyHeight
SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
CreateGrabLine = GrabEvents:WaitForChild("CreateGrabLine")
DestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
StopAllVelocity = GameCorrectionEvents:WaitForChild("StopAllVelocity")
GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
Players = game:GetService("Players")
local playerNames = {}
Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
Debris = game:GetService("Debris")
GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
Players = game:GetService("Players")
local gameName = game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name
HttpService = game:GetService("HttpService")
RunService = game:GetService("RunService")
Players = game:GetService("Players")
UserInputService = game:GetService("UserInputService")
ReplicatedStorage = game:GetService("ReplicatedStorage")
GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
MenuToys = ReplicatedStorage:WaitForChild("MenuToys")
CharacterEvents = ReplicatedStorage:WaitForChild("CharacterEvents")
SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
Struggle = CharacterEvents:WaitForChild("Struggle")
CreateLine = GrabEvents:WaitForChild("CreateGrabLine")
DestroyLine = GrabEvents:WaitForChild("DestroyGrabLine")
DestroyToy = MenuToys:WaitForChild("DestroyToy")
plrrr = game:GetService("Players")
Player = plrrr.LocalPlayer
Character = Player.Character or Player.CharacterAdded:Wait()
RS = game:GetService("ReplicatedStorage")
CE = RS:WaitForChild("CharacterEvents")
BeingHeld = Player:WaitForChild("IsHeld")
PlayerScripts = Player:WaitForChild("PlayerScripts")
local bodyvel_Name = "FlingVelocity"
userinputs = game:GetService("UserInputService")
Players = game:GetService("Players")
w = game:GetService("Workspace")
r = game:GetService("RunService")
d = game:GetService("Debris")
plr = game.Players.LocalPlayer
cam = workspace.CurrentCamera
mouse = plr:GetMouse()
uis = game:GetService("UserInputService")
inv = workspace:WaitForChild(plr.Name .. "SpawnedInToys")
rs = game:GetService("ReplicatedStorage")
rs2 = game:GetService("RunService")
deb = game:GetService("Debris")
w = game:GetService("Workspace")
me = game.Players.LocalPlayer

enabled = true
localPlayer = Players.LocalPlayer
playerCharacter = localPlayer.Character or localPlayer.CharacterAdded:Wait()
localPlayer.CharacterAdded:Connect(function(character)
    playerCharacter = character
end)
local poisonCoroutines = {}
local compiledGroups = {}
local compileConnections = {}
local connections = {}
local renderSteppedConnections = {}
local lightbitpos = {}
local lightbitparts = {}
local bodyPositions = {}
local alignOrientations = {}
local antilaggg = false
local lightbit = 0.3125
local lightbitoffset = 1
local lightbitradius = 20
local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/jadpy/suki/refs/heads/main/orion"))()
local Player = game.Players.LocalPlayer
local toysFolder = workspace:FindFirstChild(localPlayer.Name .. "SpawnedInToys")
local playerList = {}
local selection
local blobman
local ownedToys = {}
local PP_dd = 100
local G_pg = 100
local selectedPlayer
function isDescendantOf(target, other)
    local currentParent = target.Parent
    while currentParent do
        if currentParent == other then
            return true
        end
        currentParent = currentParent.Parent
    end
    return false
end

function ragdollSpamF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    while ragdollSpamT and not ragdollSpamD and not permRagdollT do
        ragdollSpamD = true
        local args = {
            [1] = hrp,
            [2] = 0
        }
        rs:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote"):FireServer(unpack(args))
        task.wait(0.02)
        ragdollSpamD = false
    end
end

function setRagdollF(state)
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    if char and char:FindFirstChild("HumanoidRootPart") then
        rs:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote"):FireServer(hrp, state and 1 or 0)
        if hum then
            hum.PlatformStand = state
        end
    end
end

function permRagdollLoopF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    if permRagdollRunningS then
        return
    end
    permRagdollRunningS = true
    while permRagdollT do
        setRagdollF(true)
        task.wait(0)
    end
    permRagdollRunningS = false
    setRagdollF(false)
end

function DestroyT(toy)
    local toy = toy or toysFolder:FindFirstChildWhichIsA("Model")
    DestroyToy:FireServer(toy)
end
function getDescendantParts(descendantName)
    local parts = {}
    for _, descendant in ipairs(workspace.Map:GetDescendants()) do
        if descendant:IsA("Part") and descendant.Name == descendantName then
            table.insert(parts, descendant)
        end
    end
    return parts
end
local poisonHurtParts = getDescendantParts("PoisonHurtPart")
local paintPlayerParts = getDescendantParts("PaintPlayerPart")
function updatePlayerList()
    playerList = {}
    for _, player in ipairs(Players:GetPlayers()) do
        table.insert(playerList, player.Name)
    end
end
function onPlayerAdded(player)
    table.insert(playerList, player.Name)
end
function onPlayerRemoving(player)
    for i, name in ipairs(playerList) do
        if name == player.Name then
            table.remove(playerList, i)
            break
        end
    end
end
Players.PlayerAdded:Connect(onPlayerAdded)
Players.PlayerRemoving:Connect(onPlayerRemoving)
for i, v in pairs(localPlayer:WaitForChild("PlayerGui"):WaitForChild("MenuGui"):WaitForChild("Menu"):WaitForChild(
    "TabContents"):WaitForChild("Toys"):WaitForChild("Contents"):GetChildren()) do
    if v.Name ~= "UIGridLayout" then
        ownedToys[v.Name] = true
    end
end


function updateCurrentHouseF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    if char.Parent == workspace then
        currentHouseS = 0
    elseif char.Parent.Name == "PlayersInPlots" then
        for i, e in workspace.Plots:GetChildren() do
            for i, e in e.PlotSign.ThisPlotsOwners:GetChildren() do
                if e.Value == plr.Name then
                    if e.Parent.Parent.Parent.Name == "Plot1" then
                        currentHouseS = 1
                    elseif e.Parent.Parent.Parent.Name == "Plot2" then
                        currentHouseS = 2
                    elseif e.Parent.Parent.Parent.Name == "Plot3" then
                        currentHouseS = 3
                    elseif e.Parent.Parent.Parent.Name == "Plot4" then
                        currentHouseS = 4
                    elseif e.Parent.Parent.Parent.Name == "Plot5" then
                        currentHouseS = 5
                    end
                end
            end
        end
    end
end

function getBlobmanF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    updateCurrentHouseF()
    if currentHouseS == 0 then
        if inv then
            return inv:FindFirstChild("CreatureBlobman")
        end
        return nil
    else
        return workspace.PlotItems:FindFirstChild("Plot" .. currentHouseS):FindFirstChild("CreatureBlobman")
    end
end

function spawnBlobmanF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    local spawnRemote = rs:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
    if spawnRemote then
        pcall(function()
            spawnRemote:InvokeServer("CreatureBlobman", hrp.CFrame * CFrame.new(0, 0, -5), Vector3.new(0, -15.716, 0))
        end)
        task.wait(0)
        blobmanInstanceS = getBlobmanF()
    end
end

function destroyBlobmanF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    if blobmanInstanceS and destroyAutoGucciT then
        if currentHouseS == 0 then
            local args = {
                [1] = blobmanInstanceS
            }
            local destroyRemote = rs:FindFirstChild("MenuToys") and rs.MenuToys:FindFirstChild("DestroyToy")
            if destroyRemote then
                pcall(function()
                    destroyRemote:FireServer(unpack(args))
                end)
            end
            blobmanInstanceS = nil
        else
            blobmanInstanceS.HumanoidRootPart.CFrame = workspace.Plots:FindFirstChild("Plot" .. currentHouseS).TeslaCoil
                                                           .ZapPart.CFrame
            blobmanInstanceS = nil
        end
    end
end

function ragdollLoopF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    if ragdollLoopD then
        return
    end
    ragdollLoopD = true
    while sitJumpT do
        if char and hrp then
            local args = {
                [1] = hrp,
                [2] = 0
            }
            rs:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote"):FireServer(unpack(args))
        end
        task.wait(0)
    end
    ragdollLoopD = false
end

function sitJumpF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    if not char or not hum then
        return
    end
    local startTime = tick()
    while autoGucciT and tick() - startTime < 0.9 do
        if blobmanInstanceS then
            local seat = blobmanInstanceS:FindFirstChildWhichIsA("VehicleSeat")
            if seat and seat.Occupant ~= hum then
                seat:Sit(hum)
            end
        end
        task.wait(0)
        if char and hum then
            hum:ChangeState(Enum.HumanoidStateType.Jumping)
        end
        task.wait(0)
    end
    if blobmanInstanceS then
        destroyBlobmanF()
    end
    autoGucciT = false
    sitJumpT = false
end

function getNearestPlayer()
    local nearestPlayer
    local nearestDistance = math.huge
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= localPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local distance =
                (playerCharacter.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
            if distance < nearestDistance then
                nearestDistance = distance
                nearestPlayer = player
            end
        end
    end
    return nearestPlayer
end
function cleanupConnections(connectionTable)
    for _, connection in ipairs(connectionTable) do
        connection:Disconnect()
    end
    connectionTable = {}
end
function spawnItem(itemName, position, orientation)
    task.spawn(function()
        local cframe = CFrame.new(position)
        local rotation = Vector3.new(0, 90, 0)
        ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(itemName, cframe, rotation)
    end)
end
function arson(part)
    if not toysFolder:FindFirstChild("Campfire") then
        spawnItem("Campfire", Vector3.new(-72.9304581, -5.96906614, -265.543732))
    end
    local campfire = toysFolder:FindFirstChild("Campfire")
    burnPart = campfire:FindFirstChild("FirePlayerPart") or campfire.FirePlayerPart
    burnPart.Size = Vector3.new(7, 7, 7)
    burnPart.Position = part.Position
    task.wait(0.3)
    burnPart.Position = Vector3.new(0, -50, 0)
end
function grabHandler(grabType)
    while true do
        local success, err = pcall(function()
            local child = workspace:FindFirstChild("GrabParts")
            if child and child.Name == "GrabParts" then
                local grabPart = child:FindFirstChild("GrabPart")
                local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                local head = grabbedPart.Parent:FindFirstChild("Head")
                if head then
                    while workspace:FindFirstChild("GrabParts") do
                        local partsTable = grabType == "poison" and poisonHurtParts or paintPlayerParts
                        for _, part in pairs(partsTable) do
                            part.Size = Vector3.new(2, 2, 2)
                            part.Transparency = 1
                            part.Position = head.Position
                        end
                        wait()
                        for _, part in pairs(partsTable) do
                            part.Position = Vector3.new(0, -200, 0)
                        end
                    end
                    for _, part in pairs(partsTable) do
                        part.Position = Vector3.new(0, -200, 0)
                    end
                end
            end
        end)
        wait()
    end
end
function fireGrab()
    while true do
        local success, err = pcall(function()
            local child = workspace:FindFirstChild("GrabParts")
            if child and child.Name == "GrabParts" then
                local grabPart = child:FindFirstChild("GrabPart")
                local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                local head = grabbedPart.Parent:FindFirstChild("Head")
                if head then
                    arson(head)
                end
            end
        end)
        wait()
    end
end
function noclipGrab()
    while true do
        local success, err = pcall(function()
            local child = workspace:FindFirstChild("GrabParts")
            if child and child.Name == "GrabParts" then
                local grabPart = child:FindFirstChild("GrabPart")
                local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                local character = grabbedPart.Parent
                if character.HumanoidRootPart then
                    while workspace:FindFirstChild("GrabParts") do
                        for _, part in pairs(character:GetChildren()) do
                            if part:IsA("BasePart") then
                                part.CanCollide = false
                            end
                        end
                        wait()
                    end
                    for _, part in pairs(character:GetChildren()) do
                        if part:IsA("BasePart") then
                            part.CanCollide = true
                        end
                    end
                end
            end
        end)
        wait()
    end
end
function spawnItemCf(itemName, cframe)
    task.spawn(function()
        local rotation = Vector3.new(0, 0, 0)
        ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(itemName, cframe, rotation)
    end)
end

function fireAll()
    while true do
        local success, err = pcall(function()
            if toysFolder:FindFirstChild("Campfire") then
                DestroyT(toysFolder:FindFirstChild("Campfire"))
                wait(0.5)
            end
            spawnItemCf("Campfire", playerCharacter.Head.CFrame)
            local campfire = toysFolder:WaitForChild("Campfire")
            local firePlayerPart
            for _, part in pairs(campfire:GetChildren()) do
                if part.Name == "FirePlayerPart" then
                    part.Size = Vector3.new(10, 10, 10)
                    firePlayerPart = part
                    break
                end
            end
            local originalPosition = playerCharacter.Torso.Position
            SetNetworkOwner:FireServer(firePlayerPart, firePlayerPart.CFrame)
            playerCharacter:MoveTo(firePlayerPart.Position)
            wait(0.3)
            playerCharacter:MoveTo(originalPosition)
            local bodyPosition = Instance.new("BodyPosition")
            bodyPosition.P = 20000
            bodyPosition.Position = playerCharacter.Head.Position + Vector3.new(0, 600, 0)
            bodyPosition.Parent = campfire.Main
            local Players = game:GetService("Players")
            local localPlayer = Players.LocalPlayer
            local playerCharacter = localPlayer.Character

            while true do
                for _, player in pairs(Players:GetChildren()) do
                    pcall(function()
                        bodyPosition.Position = playerCharacter.Head.Position + Vector3.new(0, 600, 0)

                        if player.Character and player.Character.HumanoidRootPart and player.Character ~=
                            playerCharacter then
                            local isFriend = _G.furend and localPlayer:IsFriendsWith(player.UserId)

                            if not isFriend then
                                firePlayerPart.Position = player.Character.HumanoidRootPart.Position or
                                                              player.Character.Head.Position
                                wait()
                            end
                        end
                    end)
                end
                wait()
            end
        end)
        if not success then
        end
        wait()
    end
end

function updateBodyMovers(primaryPart)
    for _, group in ipairs(compiledGroups) do
        if group.primaryPart and group.primaryPart == primaryPart then
            for _, data in ipairs(group.group) do
                local bodyPosition = data.part:FindFirstChild("BodyPosition")
                local bodyGyro = data.part:FindFirstChild("BodyGyro")
                if bodyPosition then
                    bodyPosition.Position = (primaryPart.CFrame * data.offset).Position
                end
                if bodyGyro then
                    bodyGyro.CFrame = primaryPart.CFrame * data.offset
                end
            end
        end
    end
end
function cleanupCompiledGroups()
    for _, groupData in ipairs(compiledGroups) do
        for _, data in ipairs(groupData.group) do
            if data.part then
                if data.part:FindFirstChild("BodyPosition") then
                    data.part.BodyPosition:Destroy()
                end
                if data.part:FindFirstChild("BodyGyro") then
                    data.part.BodyGyro:Destroy()
                end
            end
        end
        if groupData.primaryPart and groupData.primaryPart.Parent then
            local highlight = groupData.primaryPart:FindFirstChild("Highlight") or
                                  groupData.primaryPart.Parent:FindFirstChild("Highlight")
            if highlight then
                highlight:Destroy()
            end
        end
    end
    cleanupConnections(compileConnections)
    cleanupConnections(renderSteppedConnections)
    compiledGroups = {}
end
function compileCoroutineFunc()
    while true do
        pcall(function()
            for _, groupData in ipairs(compiledGroups) do
                updateBodyMovers(groupData.primaryPart)
            end
        end)
        wait()
    end
end

function ragdollAll()
    while true do
        local success, err = pcall(function()
            if not toysFolder:FindFirstChild("FoodBanana") then
                spawnItem("FoodBanana", Vector3.new(0, 0, 0))
            end
            local banana = toysFolder:WaitForChild("FoodBanana")
            local bananaPeel
            for _, part in pairs(banana:GetChildren()) do
                if part.Name == "BananaPeel" and part:FindFirstChild("TouchInterest") then
                    part.Size = Vector3.new(7, 7, 7)
                    part.Transparency = 1
                    bananaPeel = part
                    break
                end
            end
            local bodyPosition = Instance.new("BodyPosition")
            bodyPosition.P = 20000
            bodyPosition.Parent = banana.Main

            while true do
                for _, player in pairs(Players:GetChildren()) do
                    pcall(function()
                        if player.Character and player.Character ~= playerCharacter then
                            local isFriend = _G.furend and localPlayer:IsFriendsWith(player.UserId)

                            if not isFriend then
                                bananaPeel.Position = player.Character.HumanoidRootPart.Position or
                                                          player.Character.Head.Position
                                bodyPosition.Position = Vector3.new(300, 600, 0)
                                wait()
                            end
                        end
                    end)
                end
                wait()
            end
        end)
        if not success then
        end
        wait()
    end
end

function setupAntiExplosion(character)
    local partOwner = character:WaitForChild("Humanoid"):FindFirstChild("Ragdolled")
    if partOwner then
        local partOwnerChangedConn
        partOwnerChangedConn = partOwner:GetPropertyChangedSignal("Value"):Connect(function()
            if partOwner.Value then
                for _, part in ipairs(character:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.Anchored = true
                    end
                end
            else
                for _, part in ipairs(character:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.Anchored = false
                    end
                end
            end
        end)
        antiExplosionConnection = partOwnerChangedConn
    end
end


antifireV2 = false
function antifireV2()
    while antifireV2 do
        local success, err = pcall(function()
            if toysFolder:FindFirstChild("FireExtinguisher") then
                DestroyT(toysFolder:FindFirstChild("FireExtinguisher"))
                wait(0.5)
            end
            spawnItemCf("FireExtinguisher", playerCharacter.Head.CFrame)
            local campfire = toysFolder:WaitForChild("FireExtinguisher")
            local ExtinguishPart
            for _, part in pairs(campfire:GetChildren()) do
                if part.Name == "ExtinguishPart" then
                    part.Size = Vector3.new(10, 10, 10)
                    ExtinguishPart = part
                    break
                end
            end
            local originalPosition = playerCharacter.Torso.Position
            SetNetworkOwner:FireServer(ExtinguishPart, ExtinguishPart.CFrame)
            wait()
            playerCharacter:MoveTo(originalPosition)
            local bodyPosition = Instance.new("BodyPosition")
            bodyPosition.P = 20000
            bodyPosition.Position = Vector3.new(0, 600, 0)
            bodyPosition.Parent = campfire.Main
            while antifireV2 do
                for _, player in pairs(Players:GetChildren()) do
                    pcall(function()
                        bodyPosition.Position = Vector3.new(0, 600, 0)
                        if player.Character and player.Character.HumanoidRootPart and player.Character ~=
                            playerCharacter then
                            ExtinguishPart.Position = player.Character.HumanoidRootPart.Position or
                                                          player.Character.Head.Position
                            wait()
                        end
                    end)
                end
                wait()
            end
        end)
        if not success then
        end
        wait()
    end
end

function findSpawnedObject(objectName)
    local folder = workspace:FindFirstChild(localPlayer.Name .. "SpawnedInToys")
    if folder then
        for _, item in pairs(folder:GetChildren()) do
            if item.Name == objectName then
                return item
            end
        end
    end
    return nil
end

function sp(objectName)
    task.spawn(function()
        local rotation = Vector3.new(0, 0, 0)
        local playerCharacter = localPlayer.Character
        if not playerCharacter or not playerCharacter:FindFirstChild("Head") then
            return
        end
        pcall(function()
            game:GetService("ReplicatedStorage").MenuToys.SpawnToyRemoteFunction:InvokeServer(objectName,
                playerCharacter.Head.CFrame, rotation)
        end)
    end)
end

function FF4(item)
    if item and item:IsA("Model") then
        for _, part in pairs(item:GetDescendants()) do
        end

        local E = item:FindFirstChild("ExtinguishPart")
        if E then
            E.Size = Vector3.new(1500, 1500, 1500)
        end
    end
end

function ch()
    if FF5 and localPlayer.Character and localPlayer.Character.Parent == workspace then
        if not FF2 then
            sp(FF3)
            task.wait(0.001)
            FF2 = findSpawnedObject(FF3)
        end
        if FF2 then
            FF4(FF2)
        else
            sp(FF3)
        end
    end
end

RunService.Heartbeat:Connect(ch)

local Window = OrionLib:MakeWindow({
    Name = "いやん",
    HidePremium = false,
    SaveConfig = true,
    ConfigFolder = "iyanhub",
    IntroEnabled = true,
    IntroText = "load",
    IntroIcon = "",
    Icon = ""
})
OrionLib:MakeNotification({
    Name = ".",
    Content = "ロード中" .. Player.Name .. ".",
    Image = "rbxassetid://4483345998",
    Time = 5
})
OrionLib:MakeNotification({
    Name = "hello",
    Content = "hey you iyanhub? Pls join Discord Server https://discord.gg/https://discord.gg/YqmhW4Se8B",
    Image = "rbxassetid://4483345998",
    Time = 5
})
local GrabTab = Window:MakeTab({
    Name = "掴む",
    Icon = "rbxassetid://73570431850302",
    PremiumOnly = false
})
local objTab = Window:MakeTab({
    Name = "オブジェクト系",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local DefenseTab = Window:MakeTab({
    Name = "アンチ",
    Icon = "rbxassetid://129017321982695",
    PremiumOnly = false
})
local freezeATab = Window:MakeTab({
    Name = "無敵突破",
    Icon = "rbxassetid://111725691127581",
    PremiumOnly = false
})
local killTab = Window:MakeTab({
    Name = "リスキル系",
    Icon = "rbxassetid://117381520745599",
    PremiumOnly = false
})
local BlobmanTab = Window:MakeTab({
    Name = "ブロブマン掴む",
    Icon = "rbxassetid://88840235976282",
    PremiumOnly = false
})
local ServerTab = Window:MakeTab({
    Name = "ラグサーバー",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local AuraobjTab = Window:MakeTab({
    Name = "オブジェクトオーラ",
    Icon = "rbxassetid://117381520745599",
    PremiumOnly = false
})

local AuraTab = Window:MakeTab({
    Name = "オーラ",
    Icon = "rbxassetid://117381520745599",
    PremiumOnly = false
})
local CharacterTab = Window:MakeTab({
    Name = "キャラクター",
    Icon = "rbxassetid://76482243432173",
    PremiumOnly = false
})
local arasuTab = Window:MakeTab({
    Name = "荒らす用",
    Icon = "rbxassetid://73570431850302",
    PremiumOnly = false
})
local ESPTab = Window:MakeTab({
    Name = "ESP",
    Icon = "rbxassetid://111725691127581",
    PremiumOnly = false
})
local SlotTab = Window:MakeTab({
    Name = "テレポート",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})
local ScriptTab = Window:MakeTab({
    Name = "スクリプト",
    Icon = "rbxassetid://80041061669763",
    PremiumOnly = false
})
local FlingTab = Window:MakeTab({
    Name = "フリングスクリプト",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})
local ATab = Window:MakeTab({
    Name = "ためし",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})
local HomeTab = Window:MakeTab({
    Name = "情報",
    Icon = "rbxassetid://111725691127581",
    PremiumOnly = false
})

local iyanTab = Window:MakeTab({
    Name = "いやんはぶグループ",
    Icon = "rbxassetid://111725691127581",
    PremiumOnly = false
})
local GTab = Window:MakeTab({
    Name = "リンク",
    Icon = "rbxassetid://111725691127581",
    PremiumOnly = false
})
local PATab = Window:MakeTab({
    Name = "アップデートログ",
    Icon = "rbxassetid://111725691127581",
    PremiumOnly = false
})

_G.strength = 400

local randomSeed = Random.new()
local Players = game:GetService("Players")
local playerNames = {}
local nameMap = {}
local selectedPlayer
local PlayerDropdown

for _, player in ipairs(Players:GetPlayers()) do
    local display = player.DisplayName .. " (" .. player.Name .. ")"
    table.insert(playerNames, display)
    nameMap[display] = player.Name
end

PlayerDropdown = ServerTab:AddDropdown({
    Name = "当てたいプレイヤーを選ぶ",
    Options = playerNames,
    Callback = function(selectedDisplay)
        selectedPlayer = nameMap[selectedDisplay]
    end
})

local function RefreshPlayer()
    playerNames = {}
    nameMap = {}
    for _, player in ipairs(Players:GetPlayers()) do
        local display = player.DisplayName .. " (" .. player.Name .. ")"
        table.insert(playerNames, display)
        nameMap[display] = player.Name
    end
    if PlayerDropdown then
        PlayerDropdown:Refresh(playerNames, true)
    end
end

Players.PlayerAdded:Connect(RefreshPlayer)
Players.PlayerRemoving:Connect(RefreshPlayer)

ServerTab:AddToggle({
    Name = "選択したプレイヤーに当てる",
    Default = false,
    Callback = function(LagServer1)
        ragServer = LagServer1
        if ragServer then
            coroutine.wrap(function()
                while ragServer do
                    if selectedPlayer then
                        local playerCharacter = game.Players:FindFirstChild(selectedPlayer) and
                                                    game.Players[selectedPlayer].Character
                        if playerCharacter then
                            for i = 1, aiueo_d do
                                game:GetService("ReplicatedStorage").GrabEvents.CreateGrabLine:FireServer(
                                    playerCharacter:FindFirstChildOfClass("Part"),
                                    CFrame.new(math.random(-0, 0), math.random(-0, 0), math.random(-0, 0)))
                            end
                        else
                        end
                    else
                    end
                    wait()
                end
            end)()
        else
        end
    end
})
ServerTab:AddSlider({
    Name = "選択したプレイヤーに当てる、ライン量",
    Min = 1,
    Max = 100,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 1,
    Default = 5,
    Callback = function(value)
        aiueo_d = value
    end
})
getgenv().LineRBG = false
getgenv().invisline = false
local v85
local v86

N.linecolorscount = 1

ServerTab:AddToggle({
    Name = "カラーライン",
    Default = false,
    Save = false,
    Callback = function(Value)
        rgb = Value
        local save = O.me.BeamColor.ColorSequenceHolder.Color
        while rgb do
            if N.linecolorscount == 1 then
                O.Events.SetLineColorEvent:FireServer(ColorSequence.new(
                    Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))))
                task.wait()
            elseif N.linecolorscount == 2 then
                O.Events.SetLineColorEvent:FireServer(ColorSequence.new(
                    {ColorSequenceKeypoint.new(0, Color3.fromRGB(math.random(0, 255), math.random(0, 255),
                        math.random(0, 255))),
                     ColorSequenceKeypoint.new(1, Color3.fromRGB(math.random(0, 255), math.random(0, 255),
                        math.random(0, 255)))}))
                task.wait()
            elseif N.linecolorscount == cora then
                local args = {
                    [1] = ColorSequence.new({ColorSequenceKeypoint.new(0, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.05, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.10, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.15, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.20, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.25, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.30, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.35, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.40, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.45, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.50, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.55, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.60, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.65, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.70, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.75, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.80, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.85, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(0.90, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255))),
                                             ColorSequenceKeypoint.new(1, Color3.fromRGB(math.random(0, 255),
                        math.random(0, 255), math.random(0, 255)))})
                }
                O.Events.SetLineColorEvent:FireServer(unpack(args))
                task.wait()
            else
                local colors = {ColorSequenceKeypoint.new(0, Color3.fromRGB(math.random(0, 255), math.random(0, 255),
                    math.random(0, 255)))}
                for colorscount = 0, N.linecolorscount do
                    if colorscount ~= 1 then
                        if colorscount ~= N.linecolorscount then
                            table.insert(colors, ColorSequenceKeypoint.new(1 / N.linecolorscount * colorscount,
                                Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))))
                        else
                            table.insert(colors, ColorSequenceKeypoint.new(1, Color3.fromRGB(math.random(0, 255),
                                math.random(0, 255), math.random(0, 255))))
                        end
                    end
                end
                O.Events.SetLineColorEvent:FireServer(ColorSequence.new(colors))
                task.wait()
            end
        end
        O.Events.SetLineColorEvent:FireServer(save)
    end
})

ServerTab:AddSlider({
    Name = "カラーライン",
    Min = 1,
    Max = 25,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 1,
    Default = 1,
    Callback = function(value)
        cora = value
    end
})
ServerTab:AddToggle({
    Name = "透明ライン",
    CurrentValue = false,
    Callback = function(Value)
        getgenv().invisline = Value
        if Value then
            invsline = task.spawn(function()
                while getgenv().invisline do
                    task.wait()
                    game:GetService("ReplicatedStorage").GrabEvents.CreateGrabLine:FireServer()
                end
            end)
        else
            if invsline then
                task.cancel(invsline)
            end
        end
    end
})
local selectedPlayer
local function updateDropdown()
    local players = game:GetService("Players"):GetPlayers()
    local playerNames = {}
    for _, player in ipairs(players) do
        table.insert(playerNames, player.Name)
    end
    return playerNames
end
local d
SlotTab:AddButton({
    Name = "プレイヤーのリストを更新",
    Callback = function()
        d:Refresh(updateDropdown(), true)
    end
})
d = SlotTab:AddDropdown({
    Name = "プレイヤーを選択",
    Default = "",
    Options = updateDropdown(),
    Callback = function(value)
        selectedPlayer = value
    end
})
local function teleportToPlayer(playerName)
    local player = game:GetService("Players"):FindFirstChild(playerName)
    if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local hrp = player.Character.HumanoidRootPart
        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = hrp.CFrame
    end
end
local function teleportToPlayer2(playerName)
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = playerName
end
SlotTab:AddButton({
    Name = "プレイヤーにテレポート",
    Callback = function()
        if selectedPlayer then
            teleportToPlayer(selectedPlayer)
        else
        end
    end
})
ServerTab:AddButton({
    Name = "サーバークラッシュ",
    Callback = function()
        local startTime = tick()
        local isLooping = true
        while isLooping do
            for i = 1, 200000 do
                game:GetService("ReplicatedStorage").GrabEvents.CreateGrabLine:FireServer(
                    workspace:FindFirstChildOfClass("Part"),
                    CFrame.new(math.random(-0, 0), math.random(-0, 0), math.random(-0, 0)))
            end
            wait()
            if tick() - startTime >= wait(0.01) then
                isLooping = false
            end
        end
    end
})
ServerTab:AddButton({
    Name = "弱いラグサーバー",
    Callback = function()
        local startTime = tick()
        local isLooping = true
        while isLooping do
            for i = 750, 6500 do
                game:GetService("ReplicatedStorage").GrabEvents.CreateGrabLine:FireServer(
                    workspace:FindFirstChildOfClass("Part"),
                    CFrame.new(math.random(-0, 0), math.random(-0, 667666666666), math.random(-0, 0)))
            end
            wait(0.0000000001)
            if tick() - startTime >= wait(0.1) then
                isLooping = false
            end
        end
    end
})

function pingF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    while task.wait() and pingT do
        rs.GrabEvents.ExtendGrabLine:FireServer(string.rep("Balls Balls Balls Balls", 1000))
    end
end
ServerTab:AddToggle({
    Name = "ラグサバ V3",
    Default = false,
    Callback = function(Value)
        pingT = Value
        pingF()
    end
})

ServerTab:AddToggle({
    Name = "クレイジーライン",
    Default = false,
    Callback = function(state)
        running = state
        if running then
            coroutine.wrap(function()
                while running do
                    for i = 1, Go_g do
                        local players = game:GetService("Players"):GetPlayers()
                        if #players > 0 then
                            local randomPlayer = players[math.random(2, #players)]
                            game:GetService("ReplicatedStorage").GrabEvents.CreateGrabLine:FireServer(
                                randomPlayer.Character and randomPlayer.Character:FindFirstChild("HumanoidRootPart") or
                                    nil, CFrame.new(0, 0, 0))
                        end
                    end
                    wait()
                end
            end)()
        else
        end
    end
})
ServerTab:AddSlider({
    Name = "クレイジーライン、ライン量",
    Min = 1,
    Max = 25,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 1,
    Default = Go_g,
    Callback = function(value)
        Go_g = value
    end
})
ServerTab:AddToggle({
    Name = "ラグサーバー",
    Default = false,
    Callback = function(LagServer1)
        ragServer = LagServer1
        if ragServer then
            coroutine.wrap(function()
                while ragServer do
                    for i = 1, PP_dd do
                        game:GetService("ReplicatedStorage").GrabEvents.CreateGrabLine:FireServer(
                            workspace:FindFirstChildOfClass("Part"),
                            CFrame.new(math.random(-0, 0), math.random(-0, 0), math.random(-0, 0)))
                    end
                    wait(0)
                end
            end)()
        else
        end
    end
})
ServerTab:AddSlider({
    Name = "ラグサーバー、ライン量",
    Min = 50,
    Max = 5000,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 1,
    Default = PP_dd,
    Callback = function(value)
        PP_dd = value
    end
})

local UserInputService = game:GetService("UserInputService")
local Debris = game:GetService("Debris")

GrabTab:AddToggle({
    Name = "投げる",
    Default = false,
    Save = true,
    Flag = "Strength",
    Callback = function(enabled)
        if enabled then
            strengthConnection = workspace.ChildAdded:Connect(function(model)
                if model.Name == "GrabParts" then
                    local partToImpulse = model.GrabPart.WeldConstraint.Part1
                    if partToImpulse then
                        local velocityObj = Instance.new("BodyVelocity", partToImpulse)

                        model:GetPropertyChangedSignal("Parent"):Connect(function()
                            if not model.Parent then
                                local lastInput = UserInputService:GetLastInputType()
                                if lastInput == Enum.UserInputType.MouseButton2 or lastInput == Enum.UserInputType.Touch then
                                    velocityObj.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                    velocityObj.Velocity = workspace.CurrentCamera.CFrame.LookVector * (nageru or 100)
                                    Debris:AddItem(velocityObj, 1)
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

GrabTab:AddSlider({
    Name = "投げる強さ",
    Min = 300,
    Max = 4000,
    ValueName = ".",
    Increment = 1,
    Default = 400,
    Save = false,
    Flag = "nageru",
    Callback = function(value)
        nageru = value
    end
})
local UltraGrab = GrabTab:AddToggle({
    Name = "ライン曲がりなし",
    CurrentValue = false,
    Callback = function(Value)
        ultragrabbb = Value
    end
})
GrabTab:AddToggle({
    Name = "毒掴む",
    Default = false,
    Save = false,
    Flag = "PoisonGrab",
    Callback = function(enabled)
        if enabled then
            poisonGrabCoroutine = coroutine.create(function()
                grabHandler("poison")
            end)
            coroutine.resume(poisonGrabCoroutine)
        else
            if poisonGrabCoroutine then
                coroutine.close(poisonGrabCoroutine)
                poisonGrabCoroutine = nil
                for _, part in pairs(poisonHurtParts) do
                    part.Position = Vector3.new(0, -200, 0)
                end
            end
        end
    end
})
GrabTab:AddToggle({
    Name = "Radioactive 掴む",
    Default = false,
    Save = false,
    Flag = "RadioactiveGrab",
    Callback = function(enabled)
        if enabled then
            ufoGrabCoroutine = coroutine.create(function()
                grabHandler("radioactive")
            end)
            coroutine.resume(ufoGrabCoroutine)
        else
            if ufoGrabCoroutine then
                coroutine.close(ufoGrabCoroutine)
                ufoGrabCoroutine = nil
                for _, part in pairs(paintPlayerParts) do
                    part.Position = Vector3.new(0, -200, 0)
                end
            end
        end
    end
})
GrabTab:AddToggle({
    Name = "燃やす掴む",
    Default = false,
    Save = false,
    Flag = "FireGrab",
    Callback = function(enabled)
        if enabled then
            fireGrabCoroutine = coroutine.create(fireGrab)
            coroutine.resume(fireGrabCoroutine)
        else
            if fireGrabCoroutine then
                coroutine.close(fireGrabCoroutine)
                fireGrabCoroutine = nil
            end
        end
    end
})
GrabTab:AddToggle({
    Name = "ノクリップ掴む",
    Default = false,
    Save = false,
    Flag = "NoclipGrab",
    Callback = function(enabled)
        if enabled then
            noclipGrabCoroutine = coroutine.create(noclipGrab)
            coroutine.resume(noclipGrabCoroutine)
        else
            if noclipGrabCoroutine then
                coroutine.close(noclipGrabCoroutine)
                noclipGrabCoroutine = nil
            end
        end
    end
})

arasuTab:AddToggle({
    Name = "全員燃やす",
    Default = false,
    Save = false,
    Callback = function(enabled)
        if enabled then
            fireAllCoroutine = coroutine.create(fireAll)
            coroutine.resume(fireAllCoroutine)
        else
            if fireAllCoroutine then
                coroutine.close(fireAllCoroutine)
                fireAllCoroutine = nil
            end
        end
    end
})
arasuTab:AddLabel("バナナを食べてからバナナを捨ててオンにして、バナナを、掴む")
arasuTab:AddToggle({
    Name = "ラグドールALL",
    Default = false,
    Save = false,
    Callback = function(enabled)
        if enabled then
            ragdollAllCoroutine = coroutine.create(ragdollAll)
            coroutine.resume(ragdollAllCoroutine)
        else
            if ragdollAllCoroutine then
                coroutine.close(ragdollAllCoroutine)
                ragdollAllCoroutine = nil
            end
        end
    end
})
local RS = game:GetService("ReplicatedStorage")
local RF = game:GetService("ReplicatedFirst")

local plr = game:GetService("Players").LocalPlayer
local character = plr.Character
local HRP = character:FindFirstChild("HumanoidRootPart")

local autoSpawnBlobman = true
local newBlobman = workspace:FindFirstChild(plr.Name.."SpawnedInToys"):FindFirstChild("CreatureBlobman")

local spawnFunction = RS.MenuToys:FindFirstChild("SpawnToyRemoteFunction")

plr.CharacterAdded:Connect(function(char)
    character = char
    HRP = character:FindFirstChild("HumanoidRootPart")
end)

workspace:FindFirstChild(plr.Name.."SpawnedInToys").ChildAdded:Connect(function(toy)
    if toy.Name == "CreatureBlobman" then
        newBlobman = toy
    end
end)

local function BreakUFOs(Blobman)
    local SeatAndOwnerScript = Blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
    local CD = SeatAndOwnerScript:FindFirstChild("CreatureDrop")
    CD:FireServer(workspace.Map.AlwaysHereTweenedObjects.OuterUFO.Object.FollowThisPart.AlignPosition, HRP)
    CD:FireServer(workspace.Map.AlwaysHereTweenedObjects.OuterUFO.Object.FollowThisPart.AlignOrientation, HRP)
    CD:FireServer(workspace.Map.AlwaysHereTweenedObjects.InnerUFO.Object.FollowThisPart.AlignPosition, HRP)
    CD:FireServer(workspace.Map.AlwaysHereTweenedObjects.InnerUFO.Object.FollowThisPart.AlignOrientation, HRP)
end

local function BreakTrain(Blobman)
    local SeatAndOwnerScript = Blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
    local CD = SeatAndOwnerScript:FindFirstChild("CreatureDrop")
    CD:FireServer(workspace.Map.AlwaysHereTweenedObjects.Train.Object.FollowThisPart.AlignPosition, HRP)
    CD:FireServer(workspace.Map.AlwaysHereTweenedObjects.Train.Object.FollowThisPart.AlignOrientation, HRP)
end


local BreakUFOsButton = arasuTab:AddButton({
    Name = "UFO落とし",
    Callback = function()
        if autoSpawnBlobman == true then 
            if not newBlobman then
                spawnFunction:InvokeServer("CreatureBlobman", HRP.CFrame * CFrame.new(0,0,0),Vector3.zero)
                task.wait(.5)
                newBlobman = workspace:FindFirstChild(plr.Name.. "SpawnedInToys"):FindFirstChild("CreatureBlobman")
            end
            local HRP = character:FindFirstChild("HumanoidRootPart")
            local Hum = character:WaitForChild("Humanoid")

            local oldPosition = HRP.CFrame
            
            local seat = newBlobman:WaitForChild("VehicleSeat")
            seat:Sit(Hum)
            task.wait(.5)
            BreakUFOs(newBlobman)
            task.wait(.2)
            Hum.Sit = false
            HRP.CFrame = oldPosition
        else
            if not newBlobman then  return end
            if not newBlobman.Parent then return end
            local HRP = character:FindFirstChild("HumanoidRootPart")
            local Hum = character:WaitForChild("Humanoid")

            local oldPosition = HRP.CFrame
            local seat = newBlobman:WaitForChild("VehicleSeat", 1)
            seat:Sit(Hum)
            task.wait(.5)
            BreakUFOs(newBlobman)
            task.wait(.2)
            Hum.Sit = false
            HRP.CFrame = oldPosition
        end
    end,
}) 


local BreakTrainButton = arasuTab:AddButton({
    Name = "列車落とし",
    Callback = function()
        if autoSpawnBlobman == true then
            if not newBlobman then
                spawnFunction:InvokeServer("CreatureBlobman", HRP.CFrame * CFrame.new(0,0,0),Vector3.zero)
                task.wait(.5)
                newBlobman = workspace:FindFirstChild(plr.Name.. "SpawnedInToys"):FindFirstChild("CreatureBlobman")
            end
            local HRP = character:FindFirstChild("HumanoidRootPart")
            local Hum = character:WaitForChild("Humanoid")

            local oldPosition = HRP.CFrame
            local seat = newBlobman:WaitForChild("VehicleSeat")
            seat:Sit(Hum)
            task.wait(.5)
            BreakTrain(newBlobman)
            task.wait(.2)
            Hum.Sit = false
            HRP.CFrame = oldPosition
        else
            if not newBlobman then return end
            if not newBlobman.Parent then return end
            local HRP = character:FindFirstChild("HumanoidRootPart")
            local Hum = character:WaitForChild("Humanoid")

            local oldPosition = HRP.CFrame
            local seat = newBlobman:WaitForChild("VehicleSeat", 1)
            seat:Sit(Hum)
            task.wait(.5)
            BreakTrain(newBlobman)
            task.wait(.2)
            Hum.Sit = false
            HRP.CFrame = oldPosition
        end
    end,
}) 


arasuTab:AddButton({
    Name = "デメリット高い荒らす用",
    Callback = function()
       local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local HRP = Character:WaitForChild("HumanoidRootPart")

local SpawnRemote = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
local RagdollRemote = ReplicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote")

SpawnRemote:InvokeServer("CreatureBlobman", HRP.CFrame * CFrame.new(0, 0, -5), Vector3.new(0, -16.157, 0))

task.spawn(function()
    while true do
        if workspace:WaitForChild(LocalPlayer.Name.."SpawnedInToys"):WaitForChild("CreatureBlobman"):WaitForChild("VehicleSeat").Occupant ~= Humanoid then
            workspace:WaitForChild(LocalPlayer.Name.."SpawnedInToys"):WaitForChild("CreatureBlobman"):WaitForChild("VehicleSeat"):Sit(Humanoid)
        end
        task.wait(0.1)
    end
end)

task.spawn(function()
    while true do
        pcall(function()
            LocalPlayer.IsHeld.Value = true
        end)
        task.wait()
    end
end)

task.spawn(function()
    while true do
        for _ = 1, 12 do
            RagdollRemote:FireServer(HRP, 0)
        end
        task.wait()
    end
end)
    end,
})
D_p = 0.000005
DefenseTab:AddToggle({
    Name = "アンチ掴む",
    Default = false,
    Save = false,
    Flag = "antiGrab",
    Callback = function(enabled)
        if enabled then
            autoStruggleCoroutine = RunService.Heartbeat:Connect(function()
                local character = localPlayer.Character
                if character and character:FindFirstChild("Head") then
                    local head = character.Head
                    local partOwner = head:FindFirstChild("PartOwner")
                    if partOwner then
                        Struggle:FireServer()
                        ReplicatedStorage.GameCorrectionEvents.StopAllVelocity:FireServer()
                        for _, part in pairs(character:GetChildren()) do
                            if part:IsA("BasePart") then
                                part.Anchored = true
                            end
                        end
                        while localPlayer.IsHeld.Value do
                            wait()
                        end
                        for _, part in pairs(character:GetChildren()) do
                            if part:IsA("BasePart") then
                                part.Anchored = false
                            end
                        end
                    end
                end
            end)
        else
            if autoStruggleCoroutine then
                autoStruggleCoroutine:Disconnect()
                autoStruggleCoroutine = nil
            end
        end
    end
})

blobmanInstanceS = nil

local autoGucciToggle = DefenseTab:AddToggle({
    Name = "アンチ掴む　V２",
    CurrentValue = false,
    Callback = function(Value)
        autoGucciT = Value
        if autoGucciT then
            spawnBlobmanF()
            task.wait(0)
            if not sitJumpT then
                coroutine.wrap(sitJumpF)()
                sitJumpT = true
            end
            coroutine.wrap(ragdollLoopF)()
        else
            sitJumpT = false
        end
    end
})

local running = false

local function antikickK()
    while running do
        local success, err = pcall(function()
            if toysFolder:FindFirstChild("SprayCanWD") then
                DestroyT(toysFolder:FindFirstChild("SprayCanWD"))
                wait(0.5)
            end
            spawnItemCf("SprayCanWD", playerCharacter.Head.CFrame)
            local campfire = toysFolder:WaitForChild("SprayCanWD")
            local StickyRemoverPart
            for _, part in pairs(campfire:GetChildren()) do
                if part.Name == "StickyRemoverPart" then
                    part.Size = Vector3.new(10, 10, 10)
                    StickyRemoverPart = part
                    break
                end
            end
            local originalPosition = playerCharacter.Torso.Position
            SetNetworkOwner:FireServer(StickyRemoverPart, StickyRemoverPart.CFrame)
            wait(0.001)
            playerCharacter:MoveTo(originalPosition)
            local bodyPosition = Instance.new("BodyPosition")
            bodyPosition.P = 20000
            bodyPosition.Position = Vector3.new(0, 600, 0)
            bodyPosition.Parent = campfire.Main
            while running do
                for _, player in pairs(Players:GetChildren()) do
                    pcall(function()
                        bodyPosition.Position = Vector3.new(300, 600, 0)
                        if player.Character and player.Character.HumanoidRootPart and player.Character ~=
                            playerCharacter then
                            StickyRemoverPart.Position = player.Character.HumanoidRootPart.Position or
                                                             player.Character.Head.Position
                            wait(0.001)
                        end
                    end)
                end
                wait(0.001)
            end
        end)
        if not success then
        end
        wait(0.001)
    end
end

local antikickKCoroutine
DefenseTab:AddToggle({
    Name = "アンチキック解除",
    Default = false,
    Callback = function(enabled)
        if enabled then
            if not running then
                running = true
                antikickKCoroutine = coroutine.create(antikickK)
                coroutine.resume(antikickKCoroutine)
            end
        else
            if running then
                running = false
            end
        end
    end
})









function SetAntiBlobman(enabled)
    local function processPart(part)
        local invalid = part.Name == "LeftWeld" or part.Name == "RightWeld" or part.Name == "LeftAlignOrientation" or
                            part.Name == "RightAlignOrientation"

        if invalid and part.Parent and part.Parent.Parent and part.Parent.Parent.Parent ~=
            Workspace:FindFirstChild(LocalPlayer.Name) then
            part.Enabled = not enabled
        end
    end
    for _, part in pairs(Workspace:GetDescendants()) do
        processPart(part)
    end
    if enabled then
        if AntiCheatStates.blobConn then
            AntiCheatStates.blobConn:Disconnect()
        end
        AntiCheatStates.blobConn = Workspace.DescendantAdded:Connect(function(part)
            processPart(part)
        end)
    elseif AntiCheatStates.blobConn then
        AntiCheatStates.blobConn:Disconnect()
        AntiCheatStates.blobConn = nil
    end
end
DefenseTab:AddToggle({
    Name = "アンチブロブマン",
    Default = false,
    Callback = function(Value)
        SetAntiBlobman(Value)
    end
})

local Players = game.Players
local Workspace = game.Workspace

function bp()
    local pl = Workspace:FindFirstChild("PlotItems")
    if not pl then
        return
    end

    -- plot
    for i = 1, 5 do
        local pt = pl:FindFirstChild("Plot" .. i)
        if pt then
            local bc = pt:FindFirstChild("CreatureBlobman")
            if bc and bc:IsA("Folder") then
                for _, ude in ipairs(bc:GetChildren()) do
                    if ude.Name == "LeftDetector" or ude.Name == "RightDetector" then
                        ude:Destroy()
                    end
                end
            end
        end
    end
end

function antib1()
    for _, allp in ipairs(Players:GetPlayers()) do
        if allp ~= Players.LocalPlayer then
            local st1 = Workspace:FindFirstChild("st")
            local st2 = st1:FindFirstChild(allp.Name .. "SpawnedInToys")
            local wo = Workspace:FindFirstChild(allp.Name .. "SpawnedInToys")

            -- workspace
            if wo and wo:IsA("Folder") then
                for _, antib1 in ipairs(wo:GetChildren()) do
                    if antib1.Name == "CreatureBlobman" then
                        for _, LD1 in ipairs(antib1:GetChildren()) do
                            if LD1.Name == "LeftDetector" then
                                LD1:Destroy()
                            end
                        end

                        for _, RD1 in ipairs(antib1:GetChildren()) do
                            if RD1.Name == "RightDetector" then
                                RD1:Destroy()
                            end
                        end
                    end
                end
            end

            -- st
            if st2 and st2:IsA("Folder") then
                for _, antib2 in ipairs(st2:GetChildren()) do
                    if antib2.Name == "CreatureBlobman" then
                        for _, LD in ipairs(antib2:GetChildren()) do
                            if LD.Name == "LeftDetector" then
                                LD:Destroy()
                            end
                        end

                        for _, RD in ipairs(antib2:GetChildren()) do
                            if RD.Name == "RightDetector" then
                                RD:Destroy()
                            end
                        end
                    end
                end
            end
        end
    end
end

coroutine.wrap(function()
    while true do
        if antiblob then
            bp()
            antib1()
        end
        task.wait(0.5)
    end
end)()

DefenseTab:AddToggle({
    Name = "アンチブロブマンv2",
    Default = false,
    Callback = function(value)
        antiblob = value
    end
})

local Players = game:GetService("Players")

function up_all()
	for _, plr in ipairs(Players:GetPlayers()) do
		if plr ~= Players.LocalPlayer then
			local char = plr.Character
			if char then
				local hrp = char:FindFirstChild(HRP_NAME)
				if hrp then
					local inv
					local success = pcall(function()
						local st = workspace:WaitForChild("st")
						local st1 = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
						local st2 = st:FindFirstChild(plr.Name .. "SpawnedInToys")
						inv = st1 or st2
					end)
					if success and inv then
						for _, blobs in ipairs(inv:GetChildren()) do
							if blobs.Name == BLOB_NAME
								and blobs:FindFirstChild(SEAT_NAME)
								and blobs[SEAT_NAME]:FindFirstChild(WELD_NAME)
								and blobs[SEAT_NAME][WELD_NAME].Part1 == hrp then

								currentBlobS = blobs
								break
							end
						end
					end
				end
			end
		end
	end
end

function drop(blob, target)
	for _, side in ipairs(DETECTOR_SUFFIXES) do
		local weld = blob:FindFirstChild(side .. "Detector")
		weld = weld and weld:FindFirstChild(side .. "Weld")
		if
			weld
			and blob:FindFirstChild("BlobmanSeatAndOwnerScript")
			and blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureDrop")
		then
			blob.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(weld, target)
		end
	end
end

local RunService = game:GetService("RunService")
local Players = game:GetService("Players")

local connection

function loopdrop()
	connection = RunService.Heartbeat:Connect(function()
		up()
		if currentBlobS then
			local blob = currentBlobS
			for _, player in ipairs(Players:GetPlayers()) do
				if player ~= Players.LocalPlayer and player.Character then
					local hrp = player.Character:FindFirstChild(HRP_NAME)
					if hrp then
						drop(blob, hrp)
					end
				end
			end
		end
	end)
end

DefenseTab:AddToggle({
	Name = "アンチブリングtest",
	Default = false,
	Callback = function(value)
		blobLoopT2 = value
		if blobLoopT2 then
			loopdrop()
		else
			if connection then
				connection:Disconnect()
				connection = nil
			end
		end
	end,
})

DefenseTab:AddToggle({
    Name = "ラグドール起こし",
    CurrentValue = false,
    Flag = "Ragdoll",
    Callback = function(Value)
        permRagdollT = Value
        if permRagdollT and not permRagdollRunningS then
            coroutine.wrap(permRagdollLoopF)()
        elseif not permRagdollT then
            permRagdollRunningS = false
        end
    end
})

local connections = {}

local function clearConnections()
    for _, conn in ipairs(connections) do
        conn:Disconnect()
    end
    connections = {}
end

function protectHumanoid(humanoid)
    humanoid.BreakJointsOnDeath = false
    humanoid.AutoRotate = true
    humanoid.PlatformStand = false

    table.insert(connections, humanoid.HealthChanged:Connect(function(health)
        if protectionEnabled and health <= 0 then
            humanoid.Health = 1
        end
    end))

    table.insert(connections, humanoid:GetPropertyChangedSignal("AutoRotate"):Connect(function()
        if protectionEnabled and humanoid.AutoRotate == false then
            humanoid.AutoRotate = true
        end
    end))

    table.insert(connections, humanoid:GetPropertyChangedSignal("PlatformStand"):Connect(function()
        if protectionEnabled and humanoid.PlatformStand == true then
            humanoid.PlatformStand = false
        end
    end))

    table.insert(connections, game:GetService("RunService").RenderStepped:Connect(function()
        if protectionEnabled then
            if humanoid.Sit and humanoid.SeatPart == nil then
                humanoid.Sit = false
            end
        end
    end))
end

function onCharacter(char)
    local humanoid = char:WaitForChild("Humanoid", 5)
    if humanoid and protectionEnabled then
        protectHumanoid(humanoid)
    end
end

game:GetService("Players").LocalPlayer.CharacterAdded:Connect(onCharacter)
if game:GetService("Players").LocalPlayer.Character then
    onCharacter(game:GetService("Players").LocalPlayer.Character)
end

local function setProtection(state)
    protectionEnabled = state
    if protectionEnabled then
        local humanoid = game:GetService("Players").LocalPlayer.Character and
                             game:GetService("Players").LocalPlayer.Character:FindFirstChild("Humanoid")
        if humanoid then
            protectHumanoid(humanoid)
        end
    else
        clearConnections()
    end
end

DefenseTab:AddToggle({
    Name = "アンチラグドールテスト",
    Default = false,
    Callback = function(Value)
        setProtection(Value)
    end
})

local RS = game:GetService("ReplicatedStorage")
local RF = game:GetService("ReplicatedFirst")
local Players = game:GetService("Players")

local plr = Players.LocalPlayer
local character = plr.Character or plr.CharacterAdded:Wait()
local HRP = character:WaitForChild("HumanoidRootPart")

plr.CharacterAdded:Connect(function(char)
    character = char
    HRP = character:WaitForChild("HumanoidRootPart")
end)

local spawnFunction = RS:WaitForChild("MenuToys"):FindFirstChild("SpawnToyRemoteFunction")
local newBlobman = workspace:FindFirstChild(plr.Name .. "SpawnedInToys") and workspace[plr.Name .. "SpawnedInToys"]:FindFirstChild("CreatureBlobman")

workspace.ChildAdded:Connect(function(child)
    if child.Name == plr.Name .. "SpawnedInToys" then
        child.ChildAdded:Connect(function(toy)
            if toy.Name == "CreatureBlobman" then
                newBlobman = toy
            end
        end)
    end
end)

local function RagdollKill(blobman)
    local scriptObj = blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
    local drop = scriptObj and scriptObj:FindFirstChild("CreatureDrop")
    if drop then
        drop:FireServer(RF.ThrowPlayers.RagdollTemplate.Head.BallSocketConstraint, HRP)
        for _, player in ipairs(Players:GetPlayers()) do
            if player.Character and player.Character:FindFirstChild("Head") then
                local bsc = player.Character.Head:FindFirstChild("BallSocketConstraint")
                if bsc then
                    drop:FireServer(bsc, HRP)
                end
            end
        end
    end
end

DefenseTab:AddButton({
    Name = "てすと",
    Callback = function()

        if not newBlobman then
            spawnFunction:InvokeServer("CreatureBlobman", HRP.CFrame * CFrame.new(0, 0, 0), Vector3.zero)
            task.wait(0.5)
            newBlobman = workspace:FindFirstChild(plr.Name .. "SpawnedInToys"):FindFirstChild("CreatureBlobman")
        end
        if not newBlobman then return end

        local Hum = character:WaitForChild("Humanoid")
        local oldPosition = HRP.CFrame

        local seat = newBlobman:WaitForChild("VehicleSeat", 1)
        seat:Sit(Hum)
        task.wait(0.5)
        RagdollKill(newBlobman)
        task.wait(1)
        Hum.Sit = false
        HRP.CFrame = oldPosition
    end,
})


DefenseTab:AddToggle({
    Name = "奈落",
    Default = false,
    Callback = function(Value)
        local antivoid = Value
        if antivoid then
            game.Workspace.FallenPartsDestroyHeight = 0 / 0
        else
            game.Workspace.FallenPartsDestroyHeight = -100
        end
    end
})

local AntiBurn = DefenseTab:AddToggle({
    Name = "アンチファイヤ",
    CurrentValue = false,
    Flag = "AntiBurn",
    Callback = function(Value)
        antiburn = Value
        while antiburn do
            if O.me.Character then
                local v1 = O.me.Character:FindFirstChild("HumanoidRootPart")
                local v4 = O.me.Character:FindFirstChild("Humanoid")
                if v1 and v4 then
                    local v2 = v1:FindFirstChild("FireLight")
                    if v2 and check_hum(v4) then
                        local v3 = O.backpack:FindFirstChild("FireExtinguisher")
                        if v3 then
                            local save = v3.ExtinguishPart.Position
                            while v1:FindFirstChild("FireLight") do
                                v3.ExtinguishPart.Position = O.me.Character.HumanoidRootPart.Position
                                task.wait()
                                v3.ExtinguishPart.Position = save
                            end
                            O.backpack.FireExtinguisher.ExtinguishPart.Position = save
                        elseif not O.me.InPlot.Value and O.me.CanSpawnToy.Value then
                            task.spawn(toy_spawn2, "FireExtinguisher")
                            while O.backpack:FindFirstChild("FireExtinguisher") == nil do
                                task.wait()
                            end
                        end
                    end
                end
            end
            task.wait()
        end
    end
})


DefenseTab:AddToggle({
    Name = "アンチ爆発",
    Default = false,
    Save = false,
    Flag = "AntiExplosion",
    Callback = function(enabled)
        local localPlayer = game.Players.LocalPlayer
        if enabled then
            if localPlayer.Character then
                setupAntiExplosion(localPlayer.Character)
            end
            characterAddedConn = localPlayer.CharacterAdded:Connect(function(character)
                if antiExplosionConnection then
                    antiExplosionConnection:Disconnect()
                end
                setupAntiExplosion(character)
            end)
        else
            if antiExplosionConnection then
                antiExplosionConnection:Disconnect()
                antiExplosionConnection = nil
            end
            if characterAddedConn then
                characterAddedConn:Disconnect()
                characterAddedConn = nil
            end
        end
    end
})
local FF2 = nil
local FF3 = "FireExtinguisher"
DefenseTab:AddToggle({
    Name = "アンチファイヤall",
    Default = false,
    Save = true,
    Callback = function(enabled)
        FF5 = enabled
        if not enabled then
            FF2 = nil
        end

        if enabled then
            FCoroutine = coroutine.create(function()
                while true do
                    local success, err = pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local root = character.HumanoidRootPart

                            for _, player in pairs(Players:GetPlayers()) do
                                local toysFolder = workspace:FindFirstChild(player.Name .. "SpawnedInToys")
                                if toysFolder then
                                    for _, obj in pairs(toysFolder:GetChildren()) do
                                        coroutine.wrap(function()
                                            if obj:IsA("Model") and obj.Name == "FireExtinguisher" then
                                                local soundPart = obj:FindFirstChild("SoundPart")
                                                if soundPart and soundPart:IsA("BasePart") then
                                                    local dist = (soundPart.Position - root.Position).Magnitude
                                                    if dist <= aaaa then
                                                        SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
                                                        task.wait(0)
                                                        local velocity =
                                                            soundPart:FindFirstChild("l") or
                                                                Instance.new("BodyVelocity", soundPart)
                                                        velocity.Name = "l"
                                                        velocity.Velocity = Vector3.new(0, 0, 0)
                                                        velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                                        Debris:AddItem(velocity, 0)
                                                    end
                                                end
                                            end
                                        end)()
                                    end
                                end
                            end
                        end
                    end)
                    wait()
                end
            end)
            coroutine.resume(FCoroutine)
        else
            if FCoroutine then
                coroutine.close(FCoroutine)
                FCoroutine = nil
            end
        end
    end
})


function antiLagF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    if antiLagT == true then
        plr.PlayerScripts.CharacterAndBeamMove.Disabled = true
    elseif antiLagT == false then
        plr.PlayerScripts.CharacterAndBeamMove.Enabled = true
    end
end

DefenseTab:AddToggle({
    Name = "アンチラグ",
    Default = false,
    Callback = function(Value)
        antiLagT = Value
        antiLagF()
    end
})
local AntiKick = DefenseTab:AddToggle({
    Name = "アンチキック",
    CurrentValue = false,
    Flag = "AntiKick",
    Callback = function(Value)
        antikick = Value
        local function f()
            if S.w:FindFirstChild(O.myname) then
                local hrp, hum = get_all()
                local v5 = hrp.Parent["Right Leg"]
                local v6 = hum
                local v7 = hrp
                if v5 and v6 and v7 then
                    if v6.Health ~= 0 and v6:GetState() ~= Enum.HumanoidStateType.Dead and not O.me.InPlot.Value then
                        local v1 = O.backpack:FindFirstChild("NinjaKunai")
                        if v1 then
                            local v2 = v1:FindFirstChild("StickyPart")
                            if v2 then
                                local v3 = v2:FindFirstChild("StickyWeld")
                                if v3 then
                                    local v4 = v3.Part1
                                    if v4 then
                                        if v4 ~= v5 then
                                            O.Events.DestroyToyEvent:FireServer(v1)
                                            task.wait()
                                            f()
                                        end
                                    else
                                        O.Events.DestroyToyEvent:FireServer(v1)
                                        task.wait()
                                        f()
                                    end
                                else
                                    O.Events.DestroyToyEvent:FireServer(v1)
                                    task.wait()
                                    f()
                                end
                            else
                                O.Events.DestroyToyEvent:FireServer(v1)
                                task.wait()
                                f()
                            end
                        elseif not O.me.InPlot.Value and O.me.CanSpawnToy.Value then
                            if lastt then
                                lastt = false
                                task.wait(0.5)
                            end
                            task.spawn(toy_spawn2, "NinjaKunai")
                            while not (O.backpack:FindFirstChild("NinjaKunai")) do
                                task.wait()
                            end
                            local v8 = O.backpack:FindFirstChild("NinjaKunai").StickyPart
                            local v9 = v8.StickyWeld
                            auto_invis_touch(v8, v7)
                            while v9.Part1 == nil do
                                O.Events.StickyPartEvent:FireServer(v8, v5,
                                    CFrame.new(0.0490287527, 0.5, 0.00000000, -0.00000000, 0.00739139877, -0.999561906,
                                        -0.998452604, -0.0478846952, 0.0282763243, -0.0476547107, 0.99882561,
                                        0.00000000000) * CFrame.Angles(0, 0, 0))
                                task.wait()
                            end
                        elseif O.me.InPlot.Value then
                            lastt = true
                        end
                    end
                end
            end
        end
        local lastt = false
        while antikick do
            f()
            task.wait()
        end
    end
})

DefenseTab:AddButton({
    Name = " アンチリスキル",
    Callback = function()
        local Players = game:GetService("Players")
        local RunService = game:GetService("RunService")
        local LocalPlayer = Players.LocalPlayer
        local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local HRP = Character:WaitForChild("HumanoidRootPart")

        local flingSpeed = 3000000
        pcall(function()
            for _, part in pairs(Character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part:SetNetworkOwner(LocalPlayer)
                end
            end
        end)

        RunService.Stepped:Connect(function()
            HRP.Velocity = Vector3.new(100, 20, 100)
            HRP.RotVelocity = Vector3.new(flingSpeed, flingSpeed, flingSpeed)
        end)
    end
})

DefenseTab:AddButton({
    Name = " アンチ雪玉",
    Callback = function()
        local replicatedStorage = game:GetService("ReplicatedStorage")
        local bombEvents = replicatedStorage:FindFirstChild("BombEvents")
        if bombEvents then
            bombEvents:Destroy()
        end
        local Players = game:GetService("Players")
        local Workspace = game:GetService("Workspace")
        local RunService = game:GetService("RunService")
        local LocalPlayer = Players.LocalPlayer

        local alreadyProcessed = {}

        local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local rangeRadius = 50

        local function isInRange(position)
            return (position - character.HumanoidRootPart.Position).Magnitude <= rangeRadius
        end

        RunService.RenderStepped:Connect(function()
            for _, player in ipairs(Players:GetPlayers()) do
                if player ~= LocalPlayer then
                    local toyFolder = Workspace:FindFirstChild(player.Name .. "SpawnedInToys")
                    if toyFolder and toyFolder:IsA("Folder") then
                        for _, toy in ipairs(toyFolder:GetChildren()) do
                            if toy.Name == "BallSnowball" then
                                for _, part in ipairs(toy:GetChildren()) do
                                    if (part.Name == "SnowRagdollPart" or part.Name == "SoundPart") and
                                        not alreadyProcessed[part] then
                                        if isInRange(part.Position) then
                                            part.CanCollide = false
                                            part.CanQuery = false
                                            part.CanTouch = false
                                            part.Size = Vector3.zero
                                            alreadyProcessed[part] = true
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end
        end)
    end
})

function spawnItem(itemName, position, orientation)
    task.spawn(function()
        local cframe = CFrame.new(position)
        local rotation = Vector3.new(0, 90, 0)
        ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(itemName, cframe, rotation)
    end)
end

function arson(part)
    if not toysFolder:FindFirstChild("Campfire") then
        spawnItem("Campfire", Vector3.new(0, 0, 0))
    end
    local campfire = toysFolder:FindFirstChild("Campfire")
    burnPart = campfire:FindFirstChild("FirePlayerPart") or campfire.FirePlayerPart
    burnPart.Size = Vector3.new(7, 7, 7)
    burnPart.Position = part.Position
    task.wait()
    burnPart.Position = Vector3.new(0, 0, 0)
end

local function spawnItemCf(itemName, cframe)
    task.spawn(function()
        local rotation = Vector3.new(0, 0, 0)

        ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(itemName, cframe, rotation)
    end)
end

local RunService = game:GetService("RunService")

local function BSAT()
    local success, err = pcall(function()
        local myToysFolder = workspace:FindFirstChild(localPlayer.Name .. "SpawnedInToys")
        if myToysFolder then
            if myToysFolder:FindFirstChild("Campfire") then
                DestroyT(myToysFolder:FindFirstChild("Campfire"))
                wait()
            end

            spawnItemCf("Campfire", playerCharacter.Head.CFrame)
            local campfire = myToysFolder:WaitForChild("Campfire")
            campfire.PrimaryPart = campfire:FindFirstChild("Main")

            local firePlayerPart
            for _, part in pairs(campfire:GetChildren()) do
                if part.Name == "FirePlayerPart" then
                    part.Size = Vector3.new(0, 0, 0)
                    firePlayerPart = part
                    break
                end
            end

            local originalPosition = playerCharacter.Torso.Position
            SetNetworkOwner:FireServer(firePlayerPart, firePlayerPart.CFrame)

            playerCharacter:MoveTo(firePlayerPart.Position)
            wait()
            playerCharacter:MoveTo(originalPosition)

            local bodyPosition = Instance.new("BodyPosition")
            bodyPosition.P = 200000
            bodyPosition.Position = playerCharacter.Head.Position + Vector3.new(0, 600, 0)
            bodyPosition.Parent = campfire.Main

            local bodyVelocity = Instance.new("BodyVelocity")
            bodyVelocity.MaxForce = Vector3.new(0, 0, 0)
            bodyVelocity.Velocity = Vector3.new(0, 0, 0)
            bodyVelocity.Parent = firePlayerPart

            local function getRandomShake()
                return Vector3.new(math.random(-0, 0), math.random(-0, 0), math.random(-0, 0))
            end

            RunService.Heartbeat:Connect(function()
                for _, player in ipairs(Players:GetPlayers()) do
                    if player ~= localPlayer then
                        local folder = workspace:FindFirstChild("Folder")
                        local toysFolder = (folder and folder:FindFirstChild(player.Name .. "SpawnedInToys")) or
                                               workspace:FindFirstChild(player.Name .. "SpawnedInToys")
                        if toysFolder then
                            for _, snowball in ipairs(toysFolder:GetChildren()) do
                                pcall(function()
                                    if snowball:IsA("Model") and snowball.Name == "BallSnowball" then
                                        local targetPart = snowball.PrimaryPart or
                                                               snowball:FindFirstChildWhichIsA("BasePart")
                                        if targetPart then
                                            campfire:SetPrimaryPartCFrame(CFrame.new(targetPart.Position))
                                            local direction = (targetPart.Position - firePlayerPart.Position).unit
                                            bodyVelocity.Velocity = direction * 0
                                            bodyVelocity.Velocity = bodyVelocity.Velocity + getRandomShake()

                                            bodyPosition.Position = Vector3.new(0, 600, 0)
                                        end
                                    end
                                end)
                            end
                        end
                    end
                end
            end)
        end
    end)

    if not success then
    end
end

aaaa = 20
DefenseTab:AddToggle({
    Name = "アンチ雪玉V2",
    Default = false,
    Save = true,
    Callback = function(enabled)
        if enabled then
            BSATCoroutine = coroutine.create(BSAT)
            coroutine.resume(BSATCoroutine)

            SSCoroutine = coroutine.create(function()
                while true do
                    local success, err = pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local root = character.HumanoidRootPart

                            for _, player in pairs(Players:GetPlayers()) do
                                local toysFolder = workspace:FindFirstChild(player.Name .. "SpawnedInToys")
                                if toysFolder then
                                    for _, obj in pairs(toysFolder:GetChildren()) do
                                        coroutine.wrap(function()
                                            if obj:IsA("Model") and obj.Name == "Campfire" then
                                                local soundPart = obj:FindFirstChild("SoundPart")
                                                if soundPart and soundPart:IsA("BasePart") then
                                                    local dist = (soundPart.Position - root.Position).Magnitude
                                                    if dist <= aaaa then
                                                        SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
                                                        task.wait()
                                                        local velocity =
                                                            soundPart:FindFirstChild("l") or
                                                                Instance.new("BodyVelocity", soundPart)
                                                        velocity.Name = "l"
                                                        velocity.Velocity = Vector3.new(0, 0, 0)
                                                        velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                                        Debris:AddItem(velocity, 0)
                                                    end
                                                end

                                                local FirePlayerPart = obj:FindFirstChild("FirePlayerPart")
                                                if FirePlayerPart and FirePlayerPart:IsA("BasePart") then
                                                    local dist = (FirePlayerPart.Position - root.Position).Magnitude
                                                    if dist <= aaaa then
                                                        SetNetworkOwner:FireServer(FirePlayerPart, FirePlayerPart.CFrame)
                                                        task.wait()
                                                        local velocity =
                                                            FirePlayerPart:FindFirstChild("l") or
                                                                Instance.new("BodyVelocity", FirePlayerPart)
                                                        velocity.Name = "l"
                                                        velocity.Velocity = Vector3.new(0, 0, 0)
                                                        velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                                        Debris:AddItem(velocity, 0)
                                                    end
                                                end
                                            end
                                        end)()
                                    end
                                end
                            end
                        end
                    end)
                    wait()
                end
            end)
            coroutine.resume(SSCoroutine)
        else
            if BSATCoroutine then
                coroutine.close(BSATCoroutine)
                BSATCoroutine = nil
            end
            if SSCoroutine then
                coroutine.close(SSCoroutine)
                SSCoroutine = nil
            end
        end
    end
})

DefenseTab:AddButton({
    Name = " 直接突破無効,雪玉 ",
    Callback = function()
        local Players = game:GetService("Players")
        local Workspace = game:GetService("Workspace")
        local RunService = game:GetService("RunService")
        local LocalPlayer = Players.LocalPlayer

        local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local head = character:WaitForChild("Head")
        local function createPart(size, color, transparency, anchored, collide)
            local part = Instance.new("Part")
            part.Size = size
            part.Anchored = anchored
            part.CanCollide = collide
            part.Transparency = transparency
            part.BrickColor = BrickColor.new(color)
            part.Parent = Workspace
            return part
        end

        local basketSize = 30
        local halfSize = basketSize / 2

        local transparency = 1

        local platform = createPart(Vector3.new(basketSize, 0.1, basketSize), "Bright blue", transparency, true, true)

        local posts = {}
        for i = 1, 4 do
            local post = createPart(Vector3.new(0.4, 4, 0.4), "Bright blue", transparency, true, true)
            table.insert(posts, post)
        end

        local walls = {}
        for i = 1, 4 do
            local wallSize = (i <= 2) and Vector3.new(basketSize, 4, 0.2) or Vector3.new(0.2, 4, basketSize)
            local wall = createPart(wallSize, "Bright blue", transparency, true, true)
            table.insert(walls, wall)
        end

        RunService.RenderStepped:Connect(function()
            if character and head then
                local basePos = head.Position + Vector3.new(0, 0.4, 0)
                platform.Position = basePos

                posts[1].Position = basePos + Vector3.new(halfSize - 0.05, 2, halfSize - 0.05)
                posts[2].Position = basePos + Vector3.new(-halfSize + 0.05, 2, halfSize - 0.05)
                posts[3].Position = basePos + Vector3.new(halfSize - 0.05, 2, -halfSize + 0.05)
                posts[4].Position = basePos + Vector3.new(-halfSize + 0.05, 2, -halfSize + 0.05)

                walls[1].Position = basePos + Vector3.new(0, 2, halfSize)
                walls[2].Position = basePos + Vector3.new(0, 2, -halfSize)
                walls[3].Position = basePos + Vector3.new(halfSize, 2, 0)
                walls[4].Position = basePos + Vector3.new(-halfSize, 2, 0)
            end
        end)
    end
})

DefenseTab:AddToggle({
    Name = "Bring軽減",
    Default = false,
    Callback = function(state)
        toggleBringEnabled = state
        if state then
            processAllParts()
        else
            cleanupBringReduction()
        end
    end
})
DefenseTab:AddSlider({
    Name = "Bring軽減座標",
    Min = -34000000,
    Max = 34000000,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 1,
    Default = -30,
    Callback = function(value)
        R_raaa = value
    end
})

DefenseTab:AddToggle({
    Name = "掴んだやつを飛ばす",
    Default = false,
    Save = false,
    Flag = "SelfDefenseKick",
    Callback = function(enabled)
        if enabled then
            autoDefendKickCoroutine = coroutine.create(function()
                while enabled do
                    local character = localPlayer.Character
                    if character and character:FindFirstChild("HumanoidRootPart") then
                        local humanoidRootPart = character.HumanoidRootPart
                        local head = character:FindFirstChild("Head")
                        if head then
                            local partOwner = head:FindFirstChild("PartOwner")
                            if partOwner then
                                local attacker = Players:FindFirstChild(partOwner.Value)
                                if attacker and attacker.Character then
                                    Struggle:FireServer()
                                    SetNetworkOwner:FireServer(attacker.Character.HumanoidRootPart.FirePlayerPart,
                                        attacker.Character.HumanoidRootPart.FirePlayerPart.CFrame)
                                    task.wait()
                                    if not attacker.Character.HumanoidRootPart.FirePlayerPart:FindFirstChild(
                                        "BodyVelocity") then
                                        local bodyVelocity = Instance.new("BodyVelocity")
                                        bodyVelocity.Name = "BodyVelocity"
                                        bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                        bodyVelocity.Velocity = Vector3.new(0, 20, 0)
                                        bodyVelocity.Parent = attacker.Character.HumanoidRootPart.FirePlayerPart
                                    end
                                end
                            end
                        end
                    end
                    wait()
                end
            end)
            coroutine.resume(autoDefendKickCoroutine)
        else
            if autoDefendKickCoroutine then
                coroutine.close(autoDefendKickCoroutine)
                autoDefendKickCoroutine = nil
            end
        end
        if enabled then
            autoDefendCoroutine = coroutine.create(function()
                while wait() do
                    local character = localPlayer.Character
                    if character and character:FindFirstChild("Head") then
                        local head = character.Head
                        local partOwner = head:FindFirstChild("PartOwner")
                        if partOwner then
                            local attacker = Players:FindFirstChild(partOwner.Value)
                            if attacker and attacker.Character then
                                Struggle:FireServer()
                                SetNetworkOwner:FireServer(attacker.Character.Head or attacker.Character.Torso,
                                    attacker.Character.HumanoidRootPart.FirePlayerPart.CFrame)
                                task.wait()
                                local target = attacker.Character:FindFirstChild("Torso")
                                if target then
                                    local velocity = target:FindFirstChild("l") or Instance.new("BodyVelocity")
                                    velocity.Name = "l"
                                    velocity.Parent = target
                                    velocity.Velocity = Vector3.new(0, 2000, 0)
                                    velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                    Debris:AddItem(velocity, 0)
                                end
                            end
                        end
                    end
                end
            end)
            coroutine.resume(autoDefendCoroutine)
        else
            if autoDefendCoroutine then
                coroutine.close(autoDefendCoroutine)
                autoDefendCoroutine = nil
            end
        end
    end
})

local blobmanCoroutine = nil
local blobman = nil
local ude = "両手"
local function blobGrabtarget(player, blobman)
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        if ude == "両手" or ude == "左手" then
            local argsLeft = {
                [1] = blobman:FindFirstChild("LeftDetector"),
                [2] = player.Character:FindFirstChild("HumanoidRootPart"),
                [3] = blobman:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld")
            }
            blobman:WaitForChild("BlobmanSeatAndOwnerScript"):WaitForChild("CreatureGrab"):FireServer(unpack(argsLeft))
        end
        if ude == "両手" or ude == "右手" then
            local argsRight = {
                [1] = blobman:FindFirstChild("RightDetector"),
                [2] = player.Character:FindFirstChild("HumanoidRootPart"),
                [3] = blobman:FindFirstChild("RightDetector"):FindFirstChild("RightWeld")
            }
            blobman:WaitForChild("BlobmanSeatAndOwnerScript"):WaitForChild("CreatureGrab"):FireServer(unpack(argsRight))
        end
    end
end

local Players = game:GetService("Players")
local playerNames = {}
local nameMap = {}
local TargetPlayer

for _, player in ipairs(Players:GetPlayers()) do
    local display = player.DisplayName .. " (" .. player.Name .. ")"
    table.insert(playerNames, display)
    nameMap[display] = player.Name
end
local playerDropdown = BlobmanTab:AddDropdown({
    Name = "Bringしたいプレイヤーを選択",
    Options = playerNames,
    Callback = function(selectedDisplay)
        local realName = nameMap[selectedDisplay]
        TargetPlayer = Players:FindFirstChild(realName)
    end
})

function RefreshPlayerList()
    playerNames = {}
    nameMap = {}
    for _, player in ipairs(Players:GetPlayers()) do
        local display = player.DisplayName .. " (" .. player.Name .. ")"
        table.insert(playerNames, display)
        nameMap[display] = player.Name
    end
    playerDropdown:Refresh(playerNames, true)
end

Players.PlayerAdded:Connect(RefreshPlayerList)
Players.PlayerRemoving:Connect(RefreshPlayerList)


local plr = game:GetService("Players").LocalPlayer
local BLOB_NAME = "CreatureBlobman"
local SEAT_NAME = "VehicleSeat"
local WELD_NAME = "SeatWeld"
local HRP_NAME = "HumanoidRootPart"
local DETECTOR_SUFFIXES = { "Right", }

local currentBlobman = nil
local isGrabLoopActive = false

local function FindOwnedBlobman()
	local char = plr.Character
	if not char then return end

	local hrp = char:FindFirstChild(HRP_NAME)
	if not hrp then return end

	local toysFolder
	pcall(function()
		toysFolder = workspace:WaitForChild(plr.Name .. "SpawnedInToys")
	end)
	if not toysFolder then return end

	for _, blob in ipairs(toysFolder:GetChildren()) do
		if
			blob.Name == BLOB_NAME
			and blob:FindFirstChild(SEAT_NAME)
			and blob[SEAT_NAME]:FindFirstChild(WELD_NAME)
			and blob[SEAT_NAME][WELD_NAME].Part1 == hrp
		then
			currentBlobman = blob
			break
		end
	end
end

local function GrabTargetHRP(blobman, targetHRP)
	for _, side in ipairs(DETECTOR_SUFFIXES) do
		local detector = blobman:FindFirstChild(side .. "Detector")
		local weld = detector and detector:FindFirstChild(side .. "Weld")
		if
			detector
			and weld
			and blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
			and blobman.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab")
		then
			blobman.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(detector, targetHRP, weld)
		end
	end
end

local function LoopGrabTargetPlayer()
	while isGrabLoopActive do
		FindOwnedBlobman()
		if currentBlobman and TargetPlayer and TargetPlayer.Character then
			local hrp = TargetPlayer.Character:FindFirstChild(HRP_NAME)
			if hrp then
				GrabTargetHRP(currentBlobman, hrp)
			end
		end
		task.wait()
	end
end

BlobmanTab:AddToggle({
	Name = "選択したプレイヤーをbling",
	Default = false,
	Callback = function(enabled)
		isGrabLoopActive = enabled
		if enabled then
			task.spawn(LoopGrabTargetPlayer)
		end
	end,
})




local blobman1
blobman1 = BlobmanTab:AddToggle({
    Name = "キックオール",
    Default = false,
    Callback = function(enabled)
        if enabled then
            blobmanCoroutine = coroutine.create(function()
                local foundBlobman = false

                for i, v in pairs(game.Workspace:GetDescendants()) do
                    if v.Name == "CreatureBlobman" then
                        if v:FindFirstChild("VehicleSeat") and v.VehicleSeat:FindFirstChild("SeatWeld") and
                           isDescendantOf(v.VehicleSeat.SeatWeld.Part1, localPlayer.Character) then
                            blobman = v
                            foundBlobman = true
                            break
                        end
                    end
                end

                if not foundBlobman then
                    blobman1:Set(false)
                    blobman = nil
                    coroutine.close(blobmanCoroutine)
                    blobmanCoroutine = nil
                    return
                end
                while true do
                    pcall(function()
                        while wait() do
                            for i, v in pairs(Players:GetChildren()) do
                                if blobman and v ~= localPlayer then
                                    local isWhitelisted = _G.WhitelistedPlayers and table.find(_G.WhitelistedPlayers, v.Name)
                                    local isFriend = _G.friendModeEnabled and localPlayer:IsFriendsWith(v.UserId)

                                    if not (isWhitelisted or isFriend) then
                                        local plots = game.Workspace:FindFirstChild("Plots")
                                        local plotItems = game.Workspace:FindFirstChild("PlotItems")

                                        if plots and plotItems and plotItems:FindFirstChild("PlayersInPlots") then
                                            local playersInPlots = plotItems.PlayersInPlots

                                            for _, plot in pairs(plots:GetChildren()) do
                                                if string.match(plot.Name, "^plot[1-5]$") then
                                                    local playerInPlot = playersInPlots:FindFirstChild(v.Name)
                                                    if playerInPlot then
                                                        local character = v.Character
                                                        if character and character:FindFirstChild("HumanoidRootPart") and blobman.PrimaryPart then
                                                            local targetPos = character.HumanoidRootPart.Position
                                                            blobman:SetPrimaryPartCFrame(
                                                                CFrame.new(targetPos.X, targetPos.Y - 10, targetPos.Z)
                                                            )
                                                        end
                                                    end
                                                end
                                            end
                                        end
                                        blobGrabtarget(v, blobman)
                                    end
                                end
                                wait(allspeed)
                            end
                        end
                    end)
                    wait(allspeed)
                end
            end)
            coroutine.resume(blobmanCoroutine)

        else
            if blobmanCoroutine then
                coroutine.close(blobmanCoroutine)
                blobmanCoroutine = nil
                blobman = nil
            end
        end
    end
})

BlobmanTab:AddSlider({
    Name = "オールの速度",
    Min = 0,
    Max = 1,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 0.01,
    Default = 0.5,
    Callback = function(value)
        allspeed = value
    end
})

--[[
local blobman1
blobman1 = BlobmanTab:AddToggle({
    Name = "滑るオール",
    Default = false,
    Callback = function(enabled)
        if enabled then
            blobmanCoroutine = coroutine.create(function()
                local foundBlobman = false
                for i, v in pairs(game.Workspace:GetDescendants()) do
                    if v.Name == "CreatureBlobman" then
                        if v:FindFirstChild("VehicleSeat") and v.VehicleSeat:FindFirstChild("SeatWeld") and
                            isDescendantOf(v.VehicleSeat.SeatWeld.Part1, localPlayer.Character) then
                            blobman = v
                            foundBlobman = true
                            break
                        end
                    end
                end
                if not foundBlobman then
                    blobman1:Set(false)
                    blobman = nil
                    coroutine.close(blobmanCoroutine)
                    blobmanCoroutine = nil
                    return
                end
                while true do
                    pcall(function()
                        while wait() do
                            for i, v in pairs(Players:GetChildren()) do
                                if blobman and v ~= localPlayer then
                                    local isWhitelisted = _G.WhitelistedPlayers and
                                                              table.find(_G.WhitelistedPlayers, v.Name)
                                    local isFriend = _G.friendModeEnabled and localPlayer:IsFriendsWith(v.UserId)
                                    if not (isWhitelisted or isFriend) then
                                        blobGrabtarget(v, blobman)
                                    end
                                end
                            end
                        end
                    end)
                end
            end)
            coroutine.resume(blobmanCoroutine)
        else
            if blobmanCoroutine then
                coroutine.close(blobmanCoroutine)
                blobmanCoroutine = nil
                blobman = nil
            end
        end
    end
})
    ]]

local plr = game:GetService("Players").LocalPlayer
local BLOB_NAME = "CreatureBlobman"
local SEAT_NAME = "VehicleSeat"
local WELD_NAME = "SeatWeld"
local HRP_NAME = "HumanoidRootPart"
local DETECTOR_SUFFIXES = { "Left", "Right" }

local currentBlobS = nil
local blobLoopT2 = false

function updateCurrentBlobmanF()
	local char = plr.Character
	if not char then return end

	local hrp = char:FindFirstChild(HRP_NAME)
	if not hrp then return end

	local st = workspace:FindFirstChild("st")

	for _, player in ipairs(game.Players:GetPlayers()) do
		if st then
			local toysFolderSt = st:FindFirstChild(player.Name .. "SpawnedInToys")
			if toysFolderSt then
				for _, blobs in ipairs(toysFolderSt:GetChildren()) do
					if
						blobs.Name == BLOB_NAME and
						blobs:FindFirstChild(SEAT_NAME) and
						blobs[SEAT_NAME]:FindFirstChild(WELD_NAME) and
						blobs[SEAT_NAME][WELD_NAME].Part1 == hrp
					then
						currentBlobS = blobs
						return
					end
				end
			end
		end

		local toysFolderWS = workspace:FindFirstChild(player.Name .. "SpawnedInToys")
		if toysFolderWS then
			for _, blobs in ipairs(toysFolderWS:GetChildren()) do
				if
					blobs.Name == BLOB_NAME and
					blobs:FindFirstChild(SEAT_NAME) and
					blobs[SEAT_NAME]:FindFirstChild(WELD_NAME) and
					blobs[SEAT_NAME][WELD_NAME].Part1 == hrp
				then
					currentBlobS = blobs
					return
				end
			end
		end
	end
end


function blobGrabF(blob, target)
	for _, side in ipairs(DETECTOR_SUFFIXES) do
		local detector = blob:FindFirstChild(side .. "Detector")
		local weld = detector and detector:FindFirstChild(side .. "Weld")
		if
			detector
			and weld
			and blob:FindFirstChild("BlobmanSeatAndOwnerScript")
			and blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab")
		then
			blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(detector, target, weld)
		end
	end
end

function blobDropF(blob, target)
	for _, side in ipairs(DETECTOR_SUFFIXES) do
		local weld = blob:FindFirstChild(side .. "Detector")
		weld = weld and weld:FindFirstChild(side .. "Weld")
		if
			weld
			and blob:FindFirstChild("BlobmanSeatAndOwnerScript")
			and blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureDrop")
		then
			blob.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(weld, target)
		end
	end
end

function loopAllPlayersBlobF()
	while blobLoopT2 do
		updateCurrentBlobmanF()
		if currentBlobS then
			local blob = currentBlobS
			for _, player in ipairs(game.Players:GetPlayers()) do
				if player ~= plr and player.Character then
					local hrp = player.Character:FindFirstChild(HRP_NAME)
					if hrp then
						local isWhitelisted = table.find(_G.WhitelistedPlayers or {}, player.Name)
						local isFriend = _G.friendModeEnabled and plr:IsFriendsWith(player.UserId)

						if not isWhitelisted and not isFriend then
							blobGrabF(blob, hrp)
							task.wait()
							blobDropF(blob, hrp)
						end
					end
				end
			end
		end
		task.wait(kousokuSpeed)
	end
end



BlobmanTab:AddToggle({
	Name = "高速all",
	Default = false,
	Callback = function(value)
		blobLoopT2 = value
		if blobLoopT2 then
			task.spawn(loopAllPlayersBlobF)
		end
	end,
})


BlobmanTab:AddSlider({
    Name = "高速allの速度",
    Min = 0,
    Max = 1,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 0.01,
    Default = 0.5,
    Callback = function(value)
        kousokuSpeed = value
    end
})


BlobmanTab:AddDropdown({
    Name = "オールのモード",
    Default = "両手",
    Options = {"左手", "両手", "右手"},
    Callback = function(selected)
        ude = selected
    end
})
_G.WhitelistedPlayers = _G.WhitelistedPlayers or {}
_G.friendModeEnabled = _G.friendModeEnabled or false
function IsInWhitelist(playerName)
    local player = game:GetService("Players"):FindFirstChild(playerName)
    if _G.friendModeEnabled then
        return player and (player:IsFriendsWith(game.Players.LocalPlayer.UserId) or _G.WhitelistedPlayers[playerName])
    else
        return _G.WhitelistedPlayers[playerName]
    end
end
local Players = game:GetService("Players")
local localPlayer = Players.LocalPlayer
local selectedPlayer = nil
local whitelistDropdown = BlobmanTab:AddDropdown({
    Name = "ホワリスに入れたいプレイヤーを選ぶ",
    Options = {},
    Callback = function(player)
        if player then
            selectedPlayer = player:match("^(.-) %(") or player
        end
    end
})
local whitelistedDropdown = BlobmanTab:AddDropdown({
    Name = "ホワリスに入ってるプレイヤー",
    Options = {},
    Callback = function(player)
        if player then
            selectedPlayer = player:match("^(.-) %(") or player
        end
    end
})
function updateWhitelistDropdown()
    local playerNames = {}
    for playerName in pairs(_G.WhitelistedPlayers) do
        table.insert(playerNames, playerName)
    end
    whitelistedDropdown:Refresh(playerNames or {}, true)
end
function updatePlayerDropdown()
    local playerOptions = {}
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= localPlayer and not _G.WhitelistedPlayers[player.Name] then
            local displayOption = player.Name .. " (" .. player.DisplayName .. ")"
            table.insert(playerOptions, displayOption)
        end
    end
    whitelistDropdown:Refresh(playerOptions or {}, true)
end
function clearSelectedPlayer()
    selectedPlayer = nil
end
Players.PlayerAdded:Connect(updatePlayerDropdown)
Players.PlayerRemoving:Connect(function()
    updatePlayerDropdown()
    clearSelectedPlayer()
end)
updatePlayerDropdown()
updateWhitelistDropdown()
BlobmanTab:AddButton({
    Name = "ホワリスに追加",
    Callback = function()
        if selectedPlayer then
            _G.WhitelistedPlayers[selectedPlayer] = true
            if _G.friendModeEnabled then
                game.Players.LocalPlayer:AddFriend(game.Players[selectedPlayer])
            end
            updateWhitelistDropdown()
            updatePlayerDropdown()
            clearSelectedPlayer()
        end
    end
})
BlobmanTab:AddButton({
    Name = "ホワリス削除",
    Callback = function()
        if selectedPlayer then
            _G.WhitelistedPlayers[selectedPlayer] = nil
            updateWhitelistDropdown()
            updatePlayerDropdown()
            clearSelectedPlayer()
        end
    end
})
BlobmanTab:AddToggle({
    Name = "ホワイトフレンド",
    Default = _G.friendModeEnabled,
    Callback = function(state)
        _G.friendModeEnabled = state
        updateWhitelistDropdown()
    end
})

BlobmanTab:AddToggle({
    Name = "自動お座り",
    Default = false,
    Callback = function(enabled)
        autoMountBlobman(enabled)
    end
})

function findBlobman()
    pla = game.Players.LocalPlayer
    INN = workspace:WaitForChild(pla.Name .. "SpawnedInToys")
    for _, v in pairs(INN:GetDescendants()) do
        if v.Name == "CreatureBlobman" then
            if v:FindFirstChild("VehicleSeat") and v.VehicleSeat:FindFirstChild("SeatWeld") and
                v.VehicleSeat.SeatWeld.Part1:IsDescendantOf(game.Players.LocalPlayer.Character) then
                return v
            end
        end
    end
    return nil
end

BlobmanTab:AddButton({
    Name = "座標",
    Callback = function()
        local blobman = findBlobman()
        if blobman then
            if not blobman.PrimaryPart then
                blobman.PrimaryPart =
                    blobman:FindFirstChild("HumanoidRootPart") or blobman:FindFirstChild("MainPart") or
                        blobman:FindFirstChildWhichIsA("BasePart")
            end
            if blobman.PrimaryPart then
                local newPosition = Vector3.new(G_get, G_get, G_get)
                blobman:SetPrimaryPartCFrame(CFrame.new(newPosition))
            else
            end
        else
        end
    end
})
BlobmanTab:AddSlider({
    Name = "座標",
    Min = -90000000,
    Max = 90000000,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 1000000,
    Default = G_get,
    Callback = function(value)
        G_get = value
    end
})

BlobmanTab:AddButton({
    Name = "38桁座標いもり",
    Callback = function()
        local function findBlobman()
            player = game.Players.LocalPlayer
            IN = workspace:WaitForChild(player.Name .. "SpawnedInToys")
            for _, v in pairs(IN:GetDescendants()) do
                if v.Name == "CreatureBlobman" then
                    if v:FindFirstChild("VehicleSeat") and v.VehicleSeat:FindFirstChild("SeatWeld") and
                        v.VehicleSeat.SeatWeld.Part1:IsDescendantOf(game.Players.LocalPlayer.Character) then
                        return v
                    end
                end
            end
            return nil
        end
        local blobman = findBlobman()
        if blobman then
            if not blobman.PrimaryPart then
                blobman.PrimaryPart =
                    blobman:FindFirstChild("HumanoidRootPart") or blobman:FindFirstChild("MainPart") or
                        blobman:FindFirstChildWhichIsA("BasePart")
            end
            if blobman.PrimaryPart then
                local positions = {Vector3.new(5e+33, 0, 0), Vector3.new(5e+34, 0, 0), Vector3.new(0, 5e+38, 0),
                                   Vector3.new(0, 0, 5e+33), Vector3.new(0, 0, 5e+34), Vector3.new(-12, -40, 7)}
                local index = 1
                while true do
                    task.wait()
                    if index > #positions then
                        index = 1
                    end
                    local newPosition = positions[index]
                    blobman:SetPrimaryPartCFrame(CFrame.new(newPosition))
                    index = index + 1
                end
            else
            end
        else
        end
    end
})
BlobmanTab:AddButton({
    Name = "準突破無効",
    Callback = function()
        function findBlobman()
            playe = game.Players.LocalPlayer
            SI = workspace:WaitForChild(playe.Name .. "SpawnedInToys")
            for _, v in pairs(SI:GetDescendants()) do
                if v.Name == "CreatureBlobman" then
                    if v:FindFirstChild("VehicleSeat") and v.VehicleSeat:FindFirstChild("SeatWeld") and
                        v.VehicleSeat.SeatWeld.Part1:IsDescendantOf(game.Players.LocalPlayer.Character) then
                        return v
                    end
                end
            end
            return nil
        end
        local blobman = findBlobman()
        if blobman then
            if not blobman.PrimaryPart then
                blobman.PrimaryPart =
                    blobman:FindFirstChild("HumanoidRootPart") or blobman:FindFirstChild("MainPart") or
                        blobman:FindFirstChildWhichIsA("BasePart")
            end
            if blobman.PrimaryPart then
                while true do
                    task.wait()
                    local randomPosition = Vector3.new(math.random(-35000000, 35000000) or math.random(100, 35000000),
                        math.random(-35000000, 35000000) or math.random(100, 35000000),
                        math.random(-35000000, 35000000) or math.random(100, 35000000))
                    blobman:SetPrimaryPartCFrame(CFrame.new(randomPosition))
                end
            end
        end
    end
})

AuraTab:AddSlider({
    Name = "半径",
    Min = 0,
    Max = 25,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 1,
    Default = auraRadius,
    Callback = function(value)
        auraRadius = value
    end
})

local runningAura = false

AuraTab:AddToggle({
    Name = "掴むオーラ",
    Default = false,
    Save = false,
    Flag = "FlingAura",
    Callback = function(enabled)
        runningAura = enabled
        if enabled then
            coroutine.wrap(function()
                while runningAura do
                    local success, err = pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("Head") and
                            character:FindFirstChild("HumanoidRootPart") then
                            local humanoidRootPart = character.HumanoidRootPart
                            for _, player in pairs(Players:GetPlayers()) do
                                if player ~= localPlayer and player.Character then
                                    local isFriend = _G.Aurahowait and localPlayer:IsFriendsWith(player.UserId)
                                    if not isFriend then
                                        local targetChar = player.Character
                                        local targetTorso = targetChar:FindFirstChild("Torso")
                                        local targetRoot = targetChar:FindFirstChild("HumanoidRootPart")
                                        if targetTorso and targetRoot then
                                            local distance =
                                                (targetTorso.Position - humanoidRootPart.Position).Magnitude
                                            if distance <= auraRadius then
                                                SetNetworkOwner:FireServer(targetTorso, targetRoot.CFrame)
                                                task.wait(0.1)
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end)
                    wait()
                end
            end)()
        end
    end
})

AuraTab:AddToggle({
    Name = "上に上がるオーラ",
    Default = false,
    Save = false,
    Flag = "skyAura",
    Callback = function(enabled)
        runningSkyAura = enabled
        if enabled then
            coroutine.wrap(function()
                while runningSkyAura do
                    pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local root = character.HumanoidRootPart
                            for _, player in pairs(Players:GetPlayers()) do
                                if player ~= localPlayer and player.Character then
                                    local isFriend = _G.Aurahowait and localPlayer:IsFriendsWith(player.UserId)
                                    if not isFriend then
                                        local targetChar = player.Character
                                        local torso = targetChar:FindFirstChild("Torso")
                                        local targetRoot = targetChar:FindFirstChild("HumanoidRootPart")
                                        if torso and targetRoot then
                                            local distance = (torso.Position - root.Position).Magnitude
                                            if distance <= auraRadius then
                                                local velocity = torso:FindFirstChild("l") or
                                                                     Instance.new("BodyVelocity", torso)
                                                velocity.Name = "l"
                                                velocity.Velocity = Vector3.new(0, 30, 0)
                                                velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                                Debris:AddItem(velocity, 2)
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end)
                    wait()
                end
            end)()
        end
    end
})

AuraTab:AddToggle({
    Name = "フリングオーラ",
    Default = false,
    Save = false,
    Flag = "FlingAura",
    Callback = function(enabled)
        runningFlingAura = enabled
        if enabled then
            coroutine.wrap(function()
                while runningFlingAura do
                    pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local root = character.HumanoidRootPart
                            for _, player in pairs(Players:GetPlayers()) do
                                if player ~= localPlayer and player.Character then
                                    local isFriend = _G.Aurahowait and localPlayer:IsFriendsWith(player.UserId)
                                    if not isFriend then
                                        local targetChar = player.Character
                                        local torso = targetChar:FindFirstChild("Torso")
                                        local targetRoot = targetChar:FindFirstChild("HumanoidRootPart")
                                        if torso and targetRoot then
                                            local distance = (torso.Position - root.Position).Magnitude
                                            if distance <= auraRadius then
                                                local velocity = torso:FindFirstChild("l") or
                                                                     Instance.new("BodyVelocity", torso)
                                                velocity.Name = "l"
                                                velocity.Velocity = Vector3.new(5e10, 5e15, 5e10)
                                                velocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                                Debris:AddItem(velocity, 2)
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end)
                    wait()
                end
            end)()
        end
    end
})


AuraTab:AddToggle({
    Name = "フリーズオーラ",
    Default = false,
    Save = false,
    Flag = "FreezeAura",
    Callback = function(enabled)
        runningFreezeAura = enabled

        if enabled then
            coroutine.wrap(function()
                while runningFreezeAura do
                    pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local root = character.HumanoidRootPart
                            for _, player in pairs(Players:GetPlayers()) do
                                if player ~= localPlayer and player.Character then
                                    local isFriend = _G.Aurahowait and localPlayer:IsFriendsWith(player.UserId)
                                    if not isFriend then
                                        local targetChar = player.Character
                                        local torso = targetChar:FindFirstChild("Torso")
                                        if torso then
                                            local distance = (torso.Position - root.Position).Magnitude
                                            if distance <= auraRadius then
                                                local velocity = torso:FindFirstChild("l") or
                                                                     Instance.new("BodyVelocity")
                                                velocity.Name = "l"
                                                velocity.Velocity = Vector3.new(0, 0, 0)
                                                velocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                                velocity.Parent = torso
                                                Debris:AddItem(velocity, 2)
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end)
                    task.wait(0.1)
                end
            end)()
        end
    end
})


AuraTab:AddToggle({
    Name = "スピンオーラ",
    Default = false,
    Save = false,
    Flag = "SpinAura",
    Callback = function(enabled)
        runningSpinAura = enabled

        if enabled then
            coroutine.wrap(function()
                while runningSpinAura do
                    pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local root = character.HumanoidRootPart
                            for _, player in pairs(Players:GetPlayers()) do
                                if player ~= localPlayer and player.Character then
                                    local isFriend = _G.Aurahowait and localPlayer:IsFriendsWith(player.UserId)
                                    if not isFriend then
                                        local targetChar = player.Character
                                        local targetRoot = targetChar:FindFirstChild("HumanoidRootPart")
                                        if targetRoot then
                                            local distance = (targetRoot.Position - root.Position).Magnitude
                                            if distance <= auraRadius then
                                                local spin = targetRoot:FindFirstChild("SpinGyro") or Instance.new("BodyAngularVelocity")
                                                spin.Name = "SpinGyro"
                                                spin.AngularVelocity = Vector3.new(0, 100, 200)
                                                spin.MaxTorque = Vector3.new(0, math.huge, 0)
                                                spin.P = 1250
                                                spin.Parent = targetRoot
                                                Debris:AddItem(spin, 2)
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end)
                    task.wait(0.1)
                end
            end)()
        end
    end
})

AuraTab:AddToggle({
    Name = "毒オーラ",
    Default = false,
    Save = false,
    Callback = function(enabled)
        runningPoisonAura = enabled

        if enabled then
            coroutine.wrap(function()
                while runningPoisonAura do
                    pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local root = character.HumanoidRootPart
                            for _, player in pairs(Players:GetPlayers()) do
                                if player ~= localPlayer and player.Character then
                                    local isFriend = _G.Aurahowait and localPlayer:IsFriendsWith(player.UserId)
                                    if not isFriend then
                                        local targetChar = player.Character
                                        local torso = targetChar:FindFirstChild("Torso")
                                        local head = targetChar:FindFirstChild("Head")
                                        if torso and head then
                                            local distance = (torso.Position - root.Position).Magnitude
                                            if distance <= auraRadius then
                                                for _, part in pairs(poisonHurtParts) do
                                                    part.Size = Vector3.new(1, 3, 1)
                                                    part.Transparency = 1
                                                    part.Position = head.Position
                                                end
                                                wait(0.1)
                                                for _, part in pairs(poisonHurtParts) do
                                                    part.Position = Vector3.new(0, -200, 0)
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end)
                    wait()
                end
            end)()
        else
            for _, part in pairs(poisonHurtParts) do
                part.Position = Vector3.new(0, -200, 0)
            end
        end
    end
})

AuraTab:AddToggle({
    Name = "radioactiveオーラ",
    Default = false,
    Save = false,
    Callback = function(enabled)
        runningRadioactiveAura = enabled

        if enabled then
            coroutine.wrap(function()
                while runningRadioactiveAura do
                    pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local root = character.HumanoidRootPart
                            for _, player in pairs(Players:GetPlayers()) do
                                if player ~= localPlayer and player.Character then
                                    local isFriend = _G.Aurahowait and localPlayer:IsFriendsWith(player.UserId)
                                    if not isFriend then
                                        local targetChar = player.Character
                                        local torso = targetChar:FindFirstChild("Torso")
                                        local head = targetChar:FindFirstChild("Head")
                                        if torso and head then
                                            local distance = (torso.Position - root.Position).Magnitude
                                            if distance <= auraRadius then
                                                for _, part in pairs(paintPlayerParts) do
                                                    part.Size = Vector3.new(1, 3, 1)
                                                    part.Transparency = 1
                                                    part.Position = head.Position
                                                end
                                                wait(0.1)
                                                for _, part in pairs(paintPlayerParts) do
                                                    part.Position = Vector3.new(0, -200, 0)
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end)
                    wait()
                end
            end)()
        else
            for _, part in pairs(paintPlayerParts) do
                part.Position = Vector3.new(0, -200, 0)
            end
        end
    end
})

AuraTab:AddToggle({
    Name = "相手が埋まる",
    Default = false,
    Save = false,
    Flag = "narakuGrab",
    Callback = function(enabled)
        local Players = game:GetService("Players")
        local localPlayer = Players.LocalPlayer

        local runningNarakuGrab = enabled

        if enabled then
            coroutine.wrap(function()
                while runningNarakuGrab do
                    pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local humanoidRootPart = character.HumanoidRootPart
                            for _, player in pairs(Players:GetPlayers()) do
                                if player ~= localPlayer and player.Character then
                                    local isFriend = _G.Aurahowait and localPlayer:IsFriendsWith(player.UserId)
                                    if not isFriend then
                                        local playerCharacter = player.Character
                                        local playerTorso = playerCharacter:FindFirstChild("Torso")
                                        if playerTorso then
                                            local distance =
                                                (playerTorso.Position - humanoidRootPart.Position).Magnitude
                                            if distance <= auraRadius then
                                                SetNetworkOwner:FireServer(playerTorso, humanoidRootPart.CFrame)

                                                local force =
                                                    playerTorso:FindFirstChild("GravityForce") or
                                                        Instance.new("BodyForce")
                                                force.Name = "GravityForce"
                                                force.Parent = playerTorso
                                                force.Force = Vector3.new(0, 600, 0)

                                                for _, part in ipairs(playerCharacter:GetDescendants()) do
                                                    if part:IsA("BasePart") then
                                                        part.CanCollide = false
                                                    end
                                                end
                                            else
                                                local force = playerTorso and playerTorso:FindFirstChild("GravityForce")
                                                if force then
                                                    force:Destroy()
                                                end
                                                for _, part in ipairs(playerCharacter:GetDescendants()) do
                                                    if part:IsA("BasePart") then
                                                        part.CanCollide = true
                                                    end
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end)
                    wait()
                end
                for _, player in pairs(Players:GetPlayers()) do
                    if player.Character then
                        local playerTorso = player.Character:FindFirstChild("Torso")
                        if playerTorso then
                            local force = playerTorso:FindFirstChild("GravityForce")
                            if force then
                                force:Destroy()
                            end
                            for _, part in ipairs(player.Character:GetDescendants()) do
                                if part:IsA("BasePart") then
                                    part.CanCollide = true
                                end
                            end
                        end
                    end
                end
            end)()
        else
            runningNarakuGrab = false
        end
    end
})

AuraTab:AddToggle({
    Name = "ホワイトフレンド",
    Default = false,
    Callback = function(Value)
        _G.Aurahowait = Value
    end
})

CharacterTab:AddSlider({
    Name = "FPS上限解放",
    Min = 2,
    Max = 2000,
    Default = 300,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 1,
    ValueName = "FPS",
    Callback = function(fpsCap1)
        setfpscap(fpsCap1)
    end
})
CharacterTab:AddButton({
    Name = "三人称",
    Callback = function()
        game.Players.LocalPlayer.CameraMaxZoomDistance = 8000000
        game.Players.LocalPlayer.CameraMode = "Classic"
    end
})

CharacterTab:AddSlider({
    Name = "fov",
    Min = 0,
    Max = 120,
    Default = 80,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 1,
    ValueName = "siya",
    Callback = function(siya)
    game.Workspace.CurrentCamera.FieldOfView = siya
    end
})

local asss = false
CharacterTab:AddToggle({
    Name = "キャラクターの移動",
    Default = false,
    Callback = function(Value)
        asss = Value
    end
})
game:GetService("RunService").Heartbeat:Connect(function()
    if asss then
        if me.Character and me.Character:FindFirstChild("HumanoidRootPart") and me.Character:FindFirstChild("Humanoid") then
            local humanoidRootPart = me.Character:WaitForChild("HumanoidRootPart")
            humanoidRootPart.CFrame = humanoidRootPart.CFrame + me.Character.Humanoid.MoveDirection * (wss / 10)
        end
    else
        if me.Character and me.Character:FindFirstChild("Humanoid") then
            me.Character.Humanoid.WalkSpeed = 16
        end
    end
end)

CharacterTab:AddSlider({
    Name = "キャラクターのスピード",
    Min = 0,
    Max = 200,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 10,
    Default = 20,
    Save = false,
    Flag = "b",
    Callback = function(value)
        wss = value
    end
})

local N = {
    jps = 50
}
local O = game.Players.LocalPlayer
local JumpPowerToggle = CharacterTab:AddToggle({
    Name = "ジャンプ",
    CurrentValue = false,
    Flag = "JumpPowerToggle",
    Callback = function(Value)
        local jpt = Value
        if jpt then
            if O.Character and O.Character:FindFirstChild("Humanoid") then
                O.Character.Humanoid.JumpPower = N.jps
            end
        else
            if O.Character and O.Character:FindFirstChild("Humanoid") then
                O.Character.Humanoid.JumpPower = 24
            end
        end
    end
})

CharacterTab:AddSlider({
    Name = "ジャンプの高さ",
    Min = 0,
    Max = 1000,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 10,
    Default = 16,
    Save = false,
    Flag = "b",
    Callback = function(Value)
        N.jps = Value
        if O.Character and O.Character:FindFirstChild("Humanoid") then
            O.Character.Humanoid.JumpPower = N.jps
        end
    end
})
CharacterTab:AddSlider({
    Name = "キャラクターの高さ",
    Min = -50,
    Max = 20,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 0.1,
    Default = 0,
    Save = false,
    Flag = "Charactertakasa",
    Callback = function(value)
        game:GetService("Players").LocalPlayer.Character.Humanoid.HipHeight = value
    end
})

InfiniteJumpEnabled = false
CharacterTab:AddToggle({
    Name = "無限ジャンプ",
    Default = false,
    Callback = function(value)
        InfiniteJumpEnabled = value
    end
})
UserInputService = game:GetService("UserInputService")
UserInputService.JumpRequest:Connect(function()
    if InfiniteJumpEnabled then
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
            player.Character:FindFirstChildOfClass("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end
end)
ScriptTab:AddButton({
    Name = "無敵",
    Callback = function()
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer
        local playerGui = LocalPlayer:WaitForChild("PlayerGui")
        local function getScreenSize()
            local viewportSize = workspace.CurrentCamera.ViewportSize
            return viewportSize.X, viewportSize.Y
        end
        local function setAnchored(character, anchored)
            for _, part in ipairs(character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.Anchored = anchored
                end
            end
        end
        local screenGui = Instance.new("ScreenGui")
        screenGui.Name = "AnchorControlGui"
        screenGui.ResetOnSpawn = false
        screenGui.Parent = playerGui
        local button = Instance.new("TextButton")
        button.Name = "AnchorButton"
        button.Size = UDim2.new(0, 50, 0, 50)
        button.Text = "無敵OFF"
        button.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        button.Font = Enum.Font.SourceSansBold
        button.TextSize = 14
        button.Parent = screenGui
        local toggleGuiButton = Instance.new("TextButton")
        toggleGuiButton.Name = "ToggleGuiButton"
        toggleGuiButton.Size = UDim2.new(0, 150, 0, 50)
        toggleGuiButton.Position = UDim2.new(0, 10, 1, -350)
        toggleGuiButton.Text = "GUI: ON"
        toggleGuiButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        toggleGuiButton.Font = Enum.Font.SourceSansBold
        toggleGuiButton.TextSize = 14
        toggleGuiButton.Parent = screenGui
        local function setButtonPosition()
            local screenWidth, screenHeight = getScreenSize()
            button.Position = UDim2.new(1, -60, 0.5, -45)
        end
        local isAnchored = false
        button.MouseButton1Click:Connect(function()
            local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
            isAnchored = not isAnchored
            setAnchored(character, isAnchored)
            if isAnchored then
                button.Text = "無敵 ON"
                button.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
            else
                button.Text = "無敵 OFF"
                button.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
            end
        end)
        LocalPlayer.CharacterAdded:Connect(function(character)
            character:WaitForChild("HumanoidRootPart")
            setAnchored(character, isAnchored)
        end)
        setButtonPosition()
        workspace.CurrentCamera:GetPropertyChangedSignal("ViewportSize"):Connect(function()
            setButtonPosition()
        end)
        local guiVisible = true
        toggleGuiButton.MouseButton1Click:Connect(function()
            guiVisible = not guiVisible
            if guiVisible then
                toggleGuiButton.Text = "GUI: ON"
                toggleGuiButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
                button.Visible = true
            else
                toggleGuiButton.Text = "GUI: OFF"
                toggleGuiButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
                button.Visible = false
            end
        end)
    end
})
ScriptTab:AddButton({
    Name = "ブロブマン無効",
    Callback = function()
        local Players = game:GetService("Players")
        local player = Players.LocalPlayer
        local playerGui = player:WaitForChild("PlayerGui")
        local Workspace = game:GetService("Workspace")
        local screenGui = Instance.new("ScreenGui")
        screenGui.ResetOnSpawn = false
        screenGui.Parent = playerGui
        local frame = Instance.new("Frame")
        frame.Size = UDim2.new(0.2, 0, 0.15, 0)
        frame.Position = UDim2.new(0.3, 0, 0.4, 0)
        frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
        frame.Active = true
        frame.Parent = screenGui
        local button = Instance.new("TextButton")
        button.Size = UDim2.new(0.8, 0, 0.5, 0)
        button.Position = UDim2.new(0.1, 0, 0.25, 0)
        button.Text = "ブロブマン無効"
        button.BackgroundColor3 = Color3.fromRGB(150, 150, 150)
        button.TextColor3 = Color3.fromRGB(255, 255, 255)
        button.Parent = frame
        local function deletePlots()
            local plotItems = Workspace:FindFirstChild("PlotItems")
            if not plotItems then
                return
            end
            for i = 1, 5 do
                local plot = plotItems:FindFirstChild("Plot" .. i)
                if plot then
                    plot:Destroy()
                end
            end
        end
        local Players = game.Players
        local Workspace = game.Workspace

        local function a()
            for _, targetPlayer in ipairs(Players:GetPlayers()) do
                if targetPlayer ~= Players.LocalPlayer then
                    local toyName = targetPlayer.Name .. "SpawnedInToys"
                    local toysFolder = Workspace:FindFirstChild(toyName)
                    if toysFolder and toysFolder:IsA("Folder") then
                        for _, targetObject in ipairs(toysFolder:GetChildren()) do
                            if targetObject.Name == "CreatureBlobman" then
                                for _, leftDetector in ipairs(targetObject:GetChildren()) do
                                    if leftDetector.Name == "LeftDetector" then
                                        leftDetector:Destroy()
                                    end
                                end

                                for _, rightDetector in ipairs(targetObject:GetChildren()) do
                                    if rightDetector.Name == "RightDetector" then
                                        rightDetector:Destroy()
                                    end
                                end
                            end
                        end
                    else
                    end
                end
            end
        end

        button.MouseButton1Click:Connect(function()
            pcall(function()
                deletePlots()
                a()
            end)
        end)
        local dragging = false
        local dragStart, startPos
        local function onInputBegan(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = true
                dragStart = input.Position
                startPos = frame.Position
            end
        end
        local function onInputChanged(input)
            if dragging and
                (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType ==
                    Enum.UserInputType.Touch) then
                local delta = input.Position - dragStart
                frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale,
                    startPos.Y.Offset + delta.Y)
            end
        end
        local function onInputEnded(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = false
            end
        end
        frame.InputBegan:Connect(onInputBegan)
        frame.InputChanged:Connect(onInputChanged)
        frame.InputEnded:Connect(onInputEnded)
        pcall(function()
            deletePlots()
            a()
        end)
    end
})
ScriptTab:AddButton({
    Name = "座標v2",
    Callback = function()
        local Players = game:GetService("Players")
        local RunService = game:GetService("RunService")
        local UserInputService = game:GetService("UserInputService")
        local localPlayer = Players.LocalPlayer
        local playerGui = localPlayer:WaitForChild("PlayerGui")
        local MainScreenGui = Instance.new("ScreenGui")
        MainScreenGui.Name = "PlayerCoordinatesGui"
        MainScreenGui.ResetOnSpawn = false
        MainScreenGui.Parent = playerGui
        local MainFrame = Instance.new("ScrollingFrame")
        MainFrame.Name = "MainFrame"
        MainFrame.Size = UDim2.new(0, 400, 0, 300)
        MainFrame.Position = UDim2.new(0.5, -200, 0.5, -150)
        MainFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
        MainFrame.BorderSizePixel = 2
        MainFrame.Active = true
        MainFrame.ScrollBarThickness = 10
        MainFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
        MainFrame.Parent = MainScreenGui
        local UIListLayout = Instance.new("UIListLayout")
        UIListLayout.Parent = MainFrame
        UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
        local playerLabels = {}
        local function updateCanvasSize()
            local layout = MainFrame:FindFirstChildOfClass("UIListLayout")
            if layout then
                MainFrame.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y)
            end
        end
        local function updatePlayerLabels()
            for _, player in pairs(Players:GetPlayers()) do
                if not playerLabels[player] then
                    local label = Instance.new("TextLabel")
                    label.Name = player.Name
                    label.Size = UDim2.new(1, 0, 0, 30)
                    label.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
                    label.TextColor3 = Color3.fromRGB(255, 255, 255)
                    label.Font = Enum.Font.SourceSansBold
                    label.TextSize = 14
                    label.Text = player.Name .. ": Loading..."
                    label.Parent = MainFrame
                    playerLabels[player] = label
                end
                local character = player.Character
                local humanoidRootPart = character and character:FindFirstChild("HumanoidRootPart")
                if humanoidRootPart then
                    local position = humanoidRootPart.Position
                    playerLabels[player].Text = string.format("%s: X: %.1f, Y: %.1f, Z: %.1f", player.Name, position.X,
                        position.Y, position.Z)
                else
                    playerLabels[player].Text = player.Name .. ": プレイヤーが死んだ"
                end
            end
            updateCanvasSize()
        end
        Players.PlayerRemoving:Connect(function(player)
            if playerLabels[player] then
                playerLabels[player]:Destroy()
                playerLabels[player] = nil
                updateCanvasSize()
            end
        end)
        RunService.RenderStepped:Connect(updatePlayerLabels)
        local dragging = false
        local dragStart
        local startPos
        MainFrame.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = true
                dragStart = input.Position
                startPos = MainFrame.Position
            end
        end)
        MainFrame.InputChanged:Connect(function(input)
            if dragging and
                (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType ==
                    Enum.UserInputType.Touch) then
                local delta = input.Position - dragStart
                MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale,
                    startPos.Y.Offset + delta.Y)
            end
        end)
        MainFrame.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = false
            end
        end)
    end
})

ScriptTab:AddButton({
    Name = "DEX",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/infyiff/backup/main/dex.lua"))()
    end
})

ScriptTab:AddButton({
    Name = "FOVスクリプト(midori)",
    Callback = function()
        local player = game.Players.LocalPlayer
        local camera = workspace.CurrentCamera
        local UIS = game:GetService("UserInputService")
        local RunService = game:GetService("RunService")

        local minFOV, maxFOV, defaultFOV = 40, 100, 70

        local screenGui = Instance.new("ScreenGui")
        screenGui.Name = "FOVSliderGui"
        screenGui.ResetOnSpawn = false
        screenGui.Parent = player:WaitForChild("PlayerGui")

        local frame = Instance.new("Frame")
        frame.Size = UDim2.new(0, 300, 0, 100)
        frame.Position = UDim2.new(0.5, -150, 1, -120)
        frame.BackgroundTransparency = 1
        frame.BorderSizePixel = 0
        frame.ZIndex = 1
        frame.Parent = screenGui

        Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 12)

        local frameBackground = Instance.new("ImageLabel")
        frameBackground.Size = UDim2.new(1, 0, 1, 0)
        frameBackground.BackgroundTransparency = 1
        frameBackground.Image = ""
        frameBackground.ZIndex = 0
        frameBackground.Parent = frame

        local gradient = Instance.new("UIGradient")
        gradient.Rotation = 90
        gradient.Color = ColorSequence.new({ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 200, 200)),
                                            ColorSequenceKeypoint.new(1, Color3.fromRGB(200, 200, 255))})
        gradient.Parent = frameBackground

        local shadow = Instance.new("ImageLabel")
        shadow.Image = "rbxassetid://1316045217"
        shadow.Size = UDim2.new(1, 20, 1, 20)
        shadow.Position = UDim2.new(0, -10, 0, -10)
        shadow.BackgroundTransparency = 1
        shadow.ImageColor3 = Color3.new(0, 0, 0)
        shadow.ImageTransparency = 0.7
        shadow.ZIndex = 0
        shadow.ScaleType = Enum.ScaleType.Slice
        shadow.SliceCenter = Rect.new(10, 10, 118, 118)
        shadow.Parent = frame

        local titleLabel = Instance.new("TextLabel")
        titleLabel.Size = UDim2.new(1, -20, 0, 20)
        titleLabel.Position = UDim2.new(0, 10, 0, -24)
        titleLabel.BackgroundTransparency = 1
        titleLabel.Text = "100均のFovチェンジャー😃"
        titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        titleLabel.Font = Enum.Font.GothamBold
        titleLabel.TextScaled = true
        titleLabel.ZIndex = 2
        titleLabel.Parent = frame

        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(0.5, 0, 0, 20)
        label.Position = UDim2.new(0, 10, 0, 10)
        label.BackgroundTransparency = 1
        label.TextColor3 = Color3.new(1, 1, 1)
        label.TextScaled = true
        label.Font = Enum.Font.SourceSansBold
        label.Text = "FOV: " .. (defaultFOV - 70)
        label.ZIndex = 2
        label.TextXAlignment = Enum.TextXAlignment.Left
        label.Parent = frame

        local minimizeButton = Instance.new("TextButton")
        minimizeButton.Size = UDim2.new(0, 24, 0, 24)
        minimizeButton.Position = UDim2.new(1, -28, 0, 4)
        minimizeButton.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
        minimizeButton.Text = "×"
        minimizeButton.Font = Enum.Font.SourceSansBold
        minimizeButton.TextColor3 = Color3.new(1, 1, 1)
        minimizeButton.TextScaled = true
        minimizeButton.ZIndex = 2
        minimizeButton.Parent = frame
        Instance.new("UICorner", minimizeButton).CornerRadius = UDim.new(0, 6)

        local resetButton = Instance.new("TextButton")
        resetButton.Size = UDim2.new(0, 100, 0, 24)
        resetButton.Position = UDim2.new(1, -135, 0, 40)
        resetButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        resetButton.Text = "視野角を戻す"
        resetButton.Font = Enum.Font.SourceSans
        resetButton.TextColor3 = Color3.new(1, 1, 1)
        resetButton.TextScaled = true
        resetButton.ZIndex = 2
        resetButton.Parent = frame
        Instance.new("UICorner", resetButton).CornerRadius = UDim.new(0, 6)

        local sliderBar = Instance.new("Frame")
        sliderBar.Size = UDim2.new(1, -40, 0, 10)
        sliderBar.Position = UDim2.new(0, 20, 1, -30)
        sliderBar.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
        sliderBar.BorderSizePixel = 0
        sliderBar.ZIndex = 2
        sliderBar.Parent = frame

        local sliderHandle = Instance.new("Frame")
        sliderHandle.Size = UDim2.new(0, 20, 0, 36)
        sliderHandle.Position = UDim2.new((defaultFOV - minFOV) / (maxFOV - minFOV), -10, 0.5, -18)
        sliderHandle.AnchorPoint = Vector2.new(0.5, 0.5)
        sliderHandle.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
        sliderHandle.BorderSizePixel = 0
        sliderHandle.ZIndex = 3
        sliderHandle.Parent = sliderBar
        Instance.new("UICorner", sliderHandle).CornerRadius = UDim.new(0, 6)

        local reopenButton = Instance.new("TextButton")
        reopenButton.Size = UDim2.new(0, 40, 0, 40)
        reopenButton.Position = UDim2.new(1, -50, 1, -50)
        reopenButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
        reopenButton.Text = "+"
        reopenButton.Font = Enum.Font.SourceSansBold
        reopenButton.TextColor3 = Color3.new(1, 1, 1)
        reopenButton.TextScaled = true
        reopenButton.Visible = false
        reopenButton.ZIndex = 2
        reopenButton.Parent = screenGui
        Instance.new("UICorner", reopenButton).CornerRadius = UDim.new(1, 0)

        reopenButton.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
                local dragStart = input.Position
                local startPos = reopenButton.Position
                local conn
                conn = input.Changed:Connect(function()
                    if input.UserInputState == Enum.UserInputState.End then
                        conn:Disconnect()
                    end
                end)
                UIS.InputChanged:Connect(function(moveInput)
                    if moveInput.UserInputType == input.UserInputType then
                        local delta = moveInput.Position - dragStart
                        reopenButton.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
                            startPos.Y.Scale, startPos.Y.Offset + delta.Y)
                    end
                end)
            end
        end)

        local dragging = false

        sliderHandle.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = true
            end
        end)

        UIS.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = false
            end
        end)

        RunService.RenderStepped:Connect(function()
            if dragging then
                local mousePos = UIS:GetMouseLocation().X
                local barPos = sliderBar.AbsolutePosition.X
                local barSize = sliderBar.AbsoluteSize.X
                local newPos = math.clamp((mousePos - barPos) / barSize, 0, 1)
                sliderHandle.Position = UDim2.new(newPos, -10, 0.5, -18)
                local newFOV = math.floor(minFOV + (maxFOV - minFOV) * newPos)
                camera.FieldOfView = newFOV
                label.Text = "FOV: " .. (newFOV - 70)
            end
        end)

        resetButton.MouseButton1Click:Connect(function()
            camera.FieldOfView = defaultFOV
            local newPos = (defaultFOV - minFOV) / (maxFOV - minFOV)
            sliderHandle.Position = UDim2.new(newPos, -10, 0.5, -18)
            label.Text = "FOV: " .. (defaultFOV - 70)
        end)

        minimizeButton.MouseButton1Click:Connect(function()
            frame.Visible = false
            reopenButton.Visible = true
        end)

        reopenButton.MouseButton1Click:Connect(function()
            frame.Visible = true
            reopenButton.Visible = false
        end)
    end
})

ScriptTab:AddButton({
    Name = "テレポートスクリプトv1 (cheeky)",
    Callback = function()
        local player = game.Players.LocalPlayer
        local UIS = game:GetService("UserInputService")
        local cam = workspace.CurrentCamera

        local gui = Instance.new("ScreenGui", player.PlayerGui)
        gui.Name = "DashUI"
        gui.ResetOnSpawn = false

        local btn = Instance.new("TextButton", gui)
        btn.Size = UDim2.new(0, 80, 0, 30)
        btn.Position = UDim2.new(1, -90, 0, 10)
        btn.AnchorPoint = Vector2.new(0, 0)
        btn.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
        btn.Text = "Teleport"
        btn.TextSize = 16
        btn.Font = Enum.Font.SourceSansBold
        btn.TextColor3 = Color3.new(1, 1, 1)
        btn.BackgroundTransparency = 0.05
        btn.BorderSizePixel = 0
        btn.AutoButtonColor = true
        btn.Draggable = false
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 12)

        local function teleport()
            local char = player.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not hrp then
                return
            end

            local ray = Ray.new(cam.CFrame.Position, cam.CFrame.LookVector * 10000)
            local part, pos, normal = workspace:FindPartOnRay(ray, char)
            if part and pos and normal then
                hrp.CFrame = CFrame.new(pos + normal * 3, pos + normal * 3 + cam.CFrame.LookVector)
            end
        end

        btn.MouseButton1Click:Connect(teleport)
        UIS.InputBegan:Connect(function(input, processed)
            if not processed and input.UserInputType == Enum.UserInputType.Keyboard and input.KeyCode == Enum.KeyCode.T then
                teleport()
            end
        end)
    end
})

ScriptTab:AddButton({
    Name = "テレポートスクリプトv2 (はろ)",
    Callback = function()
        repeat
            wait()
        until game:IsLoaded()
        local p = game.Players.LocalPlayer
        repeat
            wait()
        until p and p.Character and p:FindFirstChild("PlayerGui")
        local g = Instance.new("ScreenGui", p:WaitForChild("PlayerGui"))
        g.Name = "TeleportUI"
        g.ResetOnSpawn = false
        local f = Instance.new("Frame", g)
        f.Size = UDim2.new(0, 300, 0, 360)
        f.Position = UDim2.new(0, 10, 0, 10)
        f.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
        f.BackgroundTransparency = 0.5
        f.Active = true
        f.Draggable = true
        f.Visible = false
        local t = Instance.new("TextLabel", f)
        t.Size = UDim2.new(1, 0, 0, 30)
        t.Text = "テレポート先を選択"
        t.TextColor3 = Color3.fromRGB(255, 255, 255)
        t.BackgroundTransparency = 1
        t.TextSize = 20
        local c = Instance.new("TextButton", f)
        c.Size = UDim2.new(0, 30, 0, 30)
        c.Position = UDim2.new(1, -30, 0, 0)
        c.Text = "×"
        c.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        c.TextColor3 = Color3.fromRGB(255, 255, 255)
        c.MouseButton1Click:Connect(function()
            f.Visible = false
        end)
        local tg = Instance.new("TextButton", g)
        tg.Size = UDim2.new(0, 50, 0, 50)
        tg.Position = UDim2.new(0, 10, 0, 10)
        tg.Text = "+"
        tg.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        tg.TextColor3 = Color3.fromRGB(255, 255, 255)
        tg.TextSize = 30
        tg.MouseButton1Click:Connect(function()
            f.Visible = not f.Visible
        end)
        local co = {{
            X = 256.14,
            Y = -7.23,
            Z = 465.11
        }, {
            X = -532.59,
            Y = -7.35,
            Z = 95.94
        }, {
            X = -490.75,
            Y = -7.35,
            Z = -162.50
        }, {
            X = 555.52,
            Y = 123.34,
            Z = -77.16
        }, {
            X = 511.66,
            Y = 83.34,
            Z = -345.15
        }}
        local tp = false
        local rtp = false
        local tc = nil
        local rs = game:GetService("RunService")
        rs.RenderStepped:Connect(function()
            if p.Character then
                local r = p.Character:FindFirstChild("HumanoidRootPart")
                if r then
                    local s = r:FindFirstAncestorWhichIsA("VehicleSeat") or r:FindFirstAncestorWhichIsA("Seat")
                    local target = s and s:IsA("BasePart") and s or r
                    if tp and tc then
                        target.CFrame = CFrame.new(tc.X, tc.Y, tc.Z)
                    elseif rtp then
                        local c = co[math.random(1, #co)]
                        target.CFrame = CFrame.new(c.X, c.Y, c.Z)
                    end
                end
            end
        end)
        local st = Instance.new("TextButton", f)
        st.Size = UDim2.new(0, 280, 0, 30)
        st.Position = UDim2.new(0, 10, 0, 230)
        st.Text = "連続テレポート停止"
        st.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        st.TextColor3 = Color3.fromRGB(255, 255, 255)
        st.MouseButton1Click:Connect(function()
            tp = false
            rtp = false
        end)
        local ra = Instance.new("TextButton", f)
        ra.Size = UDim2.new(0, 280, 0, 30)
        ra.Position = UDim2.new(0, 10, 0, 270)
        ra.Text = "ランダム高速テレポート切替"
        ra.BackgroundColor3 = Color3.fromRGB(0, 0, 255)
        ra.TextColor3 = Color3.fromRGB(255, 255, 255)
        ra.MouseButton1Click:Connect(function()
            rtp = not rtp
            tp = false
        end)
        local afe = true
        local afb = Instance.new("TextButton", f)
        afb.Size = UDim2.new(0, 280, 0, 30)
        afb.Position = UDim2.new(0, 10, 0, 310)
        afb.Text = "アンチフリング：ON"
        afb.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
        afb.TextColor3 = Color3.fromRGB(255, 255, 255)
        afb.MouseButton1Click:Connect(function()
            afe = not afe
            afb.Text = "アンチフリング：" .. (afe and "ON" or "OFF")
        end)
        for i, c in ipairs(co) do
            local b = Instance.new("TextButton", f)
            b.Size = UDim2.new(0, 280, 0, 30)
            b.Position = UDim2.new(0, 10, 0, 30 + (i - 1) * 40)
            b.Text = string.format("座標%d: X=%.2f Y=%.2f Z=%.2f", i, c.X, c.Y, c.Z)
            b.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
            b.TextColor3 = Color3.fromRGB(0, 0, 0)
            b.MouseButton1Click:Connect(function()
                tc = c
                tp = true
                rtp = false
            end)
        end
        local lp = game:GetService("Players").LocalPlayer
        local rs = game:GetService("RunService")
        local t, z, pp = {}, Vector3.zero, PhysicalProperties.new(0, 0, 0, 0, 0)
        local function a(v)
            if v:IsA("Part") and v.Name == "HumanoidRootPart" and v.Parent ~= lp.Character and not v.Anchored then
                t[#t + 1] = v
            end
        end
        for _, v in ipairs(workspace:GetDescendants()) do
            a(v)
        end
        workspace.DescendantAdded:Connect(a)
        rs.Heartbeat:Connect(function()
            if afe then
                for i = #t, 1, -1 do
                    local v = t[i]
                    if v and v.Parent then
                        v.Velocity = z
                        v.RotVelocity = z
                        v.CanCollide = false
                        v.CustomPhysicalProperties = pp
                    else
                        t[i] = nil
                    end
                end
            end
        end)
    end
})

ScriptTab:AddButton({
    Name = "えりしうむ",
    Callback = function()
        loadstring(game:HttpGet(
            "https://raw.githubusercontent.com/VerbalHubz/Verbal-Hub/refs/heads/main/ElysiumKeySystemCracked", true))()
    end
})

ScriptTab:AddButton({
    Name = "Ghostハブ",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/GhostPlayer352/Test4/main/GhostHub"))()
    end
})
ScriptTab:AddButton({
    Name = "IY",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source"))()
    end
})
ScriptTab:AddButton({
    Name = "奈落",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/jadpy/suki/refs/heads/main/lol"))()
    end
})

ScriptTab:AddButton({
    Name = "vfly",
    Callback = function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/jadpy/suki/refs/heads/main/vfly"))()
    end
})
ScriptTab:AddButton({
    Name = "座標分かる",
    Callback = function()
        local S = Instance.new("ScreenGui")
        local L = Instance.new("Frame")
        local D = Instance.new("Frame")
        local T = Instance.new("TextButton")
        local X = Instance.new("TextBox")
        local Z = Instance.new("TextBox")
        local Y = Instance.new("TextBox")
        local V = Instance.new("TextLabel")
        local H = Instance.new("TextLabel")
        local C = Instance.new("TextButton")
        local P = game.Players.LocalPlayer.Character.HumanoidRootPart
        local CF = P.CFrame
        local Cx, Cy, Cz, m11, m12, m13, m21, m22, m23, m31, m32, m33 = CF:components()
        S.Name = "S"
        S.Parent = game:WaitForChild("CoreGui")
        S.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
        L.Name = "L"
        L.Parent = S
        L.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
        L.BorderColor3 = Color3.fromRGB(110, 110, 110)
        L.Position = UDim2.new(0.321100891, 0, 0.282937378, 0)
        L.Size = UDim2.new(0, 350, 0, 200)
        L.Active = true
        L.Draggable = true
        D.Name = "D"
        D.Parent = L
        D.BackgroundColor3 = Color3.fromRGB(34, 34, 34)
        D.BorderColor3 = Color3.fromRGB(110, 110, 110)
        D.Position = UDim2.new(0.042857144, 0, 0.200000003, 0)
        D.Size = UDim2.new(0, 320, 0, 145)
        T.Name = "T"
        T.Parent = D
        T.BackgroundColor3 = Color3.fromRGB(34, 34, 34)
        T.BorderColor3 = Color3.fromRGB(84, 92, 38)
        T.Position = UDim2.new(0.046875, 0, 0.551724136, 0)
        T.Size = UDim2.new(0, 289, 0, 50)
        T.Font = Enum.Font.Gotham
        T.Text = "Teleport"
        T.TextColor3 = Color3.fromRGB(255, 255, 255)
        T.TextSize = 15.000
        T.MouseButton1Click:connect(function()
            P.CFrame = CFrame.new(X.Text, Y.Text, Z.Text)
        end)
        X.Name = "X"
        X.Parent = D
        X.BackgroundColor3 = Color3.fromRGB(34, 34, 34)
        X.BorderColor3 = Color3.fromRGB(96, 12, 12)
        X.Position = UDim2.new(0.046875, 0, 0.103448279, 0)
        X.Size = UDim2.new(0, 86, 0, 50)
        X.Font = Enum.Font.Gotham
        X.PlaceholderText = Cx
        X.Text = ""
        X.TextColor3 = Color3.fromRGB(255, 255, 255)
        X.TextSize = 14.000
        Z.Name = "Z"
        Z.Parent = D
        Z.BackgroundColor3 = Color3.fromRGB(34, 34, 34)
        Z.BorderColor3 = Color3.fromRGB(96, 12, 12)
        Z.Position = UDim2.new(0.681249976, 0, 0.103448279, 0)
        Z.Size = UDim2.new(0, 86, 0, 50)
        Z.Font = Enum.Font.Gotham
        Z.PlaceholderText = Cz
        Z.Text = ""
        Z.TextColor3 = Color3.fromRGB(255, 255, 255)
        Z.TextSize = 14.000
        Y.Name = "Y"
        Y.Parent = D
        Y.BackgroundColor3 = Color3.fromRGB(34, 34, 34)
        Y.BorderColor3 = Color3.fromRGB(96, 12, 12)
        Y.Position = UDim2.new(0.365624994, 0, 0.103448279, 0)
        Y.Size = UDim2.new(0, 85, 0, 50)
        Y.Font = Enum.Font.Gotham
        Y.PlaceholderText = Cy
        Y.Text = ""
        Y.TextColor3 = Color3.fromRGB(255, 255, 255)
        Y.TextSize = 14.000
        V.Name = "V"
        V.Parent = L
        V.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        V.BackgroundTransparency = 1.000
        V.BorderSizePixel = 0
        V.Position = UDim2.new(0.042857144, 0, 0, 0)
        V.Size = UDim2.new(0, 140, 0, 40)
        V.Font = Enum.Font.GothamBold
        V.Text = "o u s e V 3 r m"
        V.TextColor3 = Color3.fromRGB(255, 255, 255)
        V.TextSize = 14.000
        H.Name = "H"
        H.Parent = L
        H.BackgroundColor3 = Color3.fromRGB(186, 3, 3)
        H.BackgroundTransparency = 1.000
        H.BorderSizePixel = 0
        H.Position = UDim2.new(0.042857144, 0, 0, 0)
        H.Size = UDim2.new(0, 25, 0, 40)
        H.Font = Enum.Font.GothamBold
        H.Text = "H"
        H.TextColor3 = Color3.fromRGB(186, 3, 3)
        H.TextSize = 16.000
        C.Name = "C"
        C.Parent = L
        C.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        C.BackgroundTransparency = 1.000
        C.BorderSizePixel = 0
        C.Position = UDim2.new(0.885714352, 0, 0, 0)
        C.Size = UDim2.new(0, 40, 0, 40)
        C.Font = Enum.Font.GothamBold
        C.Text = "X"
        C.TextColor3 = Color3.fromRGB(255, 255, 255)
        C.TextSize = 14.000
        C.MouseButton1Click:connect(function()
            S:Destroy()
        end)
        while true do
            function update()
                local P = game.Players.LocalPlayer.Character.HumanoidRootPart
                local CF = P.CFrame
                local Cx, Cy, Cz, m11, m12, m13, m21, m22, m23, m31, m32, m33 = CF:components()
                X.PlaceholderText = math.floor(Cx + 0.5)
                Y.PlaceholderText = math.floor(Cy + 0.5)
                Z.PlaceholderText = math.floor(Cz + 0.5)
            end
            pcall(update)
            wait()
        end
    end
})
SlotTab:AddToggle({
    Name = "オートテレポート家",
    Default = false,
    Callback = function(value)
        autoTeleportOnFall = value
    end
})
game:GetService("RunService").Heartbeat:Connect(function()
    if autoTeleportOnFall then
        local player = game.Players.LocalPlayer
        if player and player.Character and player.Character.PrimaryPart and player.Character.PrimaryPart.Position.Y < 20 then
            player.Character:SetPrimaryPartCFrame(CFrame.new(255, -8, 449))
        end
    end
end)
game.Workspace.Gravity = 100
CharacterTab:AddSlider({
    Name = "重力スライド式",
    Min = 0,
    Max = 1000,
    Default = 100,
    Color = Color3.fromRGB(255, 255, 255),
    Increment = 1,
    ValueName = "重力",
    Callback = function(Value)
        game.Workspace.Gravity = Value
    end
})
local GrabConnections = {}
local GrabToggles = {
    KillGrab = false,
    SkyGrab = false,
    DownGrab = false,
    StableGrab = false
}
local function handleGrabPart(model, mode)
    if model.Name == "GrabParts" then
        local part_to_impulse = model:FindFirstChild("GrabPart")
        if part_to_impulse and part_to_impulse:FindFirstChild("WeldConstraint") then
            if mode == "Kill" then
                local target = part_to_impulse.WeldConstraint.Part1
                if target then
                    target.Parent:BreakJoints()
                end
            else
                local bodyVelocity = Instance.new("BodyVelocity")
                bodyVelocity.Parent = part_to_impulse
                if mode == "Sky" then
                    bodyVelocity.Velocity = Vector3.new(0, 20, 0)
                elseif mode == "Down" then
                    bodyVelocity.Velocity = Vector3.new(0, -20, 0)
                else
                    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
                end
                bodyVelocity.MaxForce = Vector3.new(0, math.huge, 0)
            end
        end
    end
end
local function toggleGrab(flag, mode)
    if GrabToggles[flag] then
        if GrabConnections[flag] then
            GrabConnections[flag]:Disconnect()
        end
        GrabConnections[flag] = game:GetService("Workspace").ChildAdded:Connect(function(model)
            handleGrabPart(model, mode)
        end)
    else
        if GrabConnections[flag] then
            GrabConnections[flag]:Disconnect()
            GrabConnections[flag] = nil
        end
    end
end
GrabTab:AddToggle({
    Name = "キル掴む",
    Default = false,
    Save = false,
    Flag = "KillGrab",
    Callback = function(Value)
        GrabToggles.KillGrab = Value
        toggleGrab("KillGrab", "Kill")
    end
})
GrabTab:AddToggle({
    Name = "上に上がる掴む",
    Default = false,
    Save = false,
    Flag = "SkyGrab",
    Callback = function(Value)
        GrabToggles.SkyGrab = Value
        toggleGrab("SkyGrab", "Sky")
    end
})
GrabTab:AddToggle({
    Name = "下に下がる掴む",
    Default = false,
    Save = false,
    Flag = "DownGrab",
    Callback = function(Value)
        GrabToggles.DownGrab = Value
        toggleGrab("DownGrab", "Down")
    end
})
GrabTab:AddToggle({
    Name = "高さを保ちながら掴む",
    Default = false,
    Save = false,
    Flag = "StableGrab",
    Callback = function(Value)
        GrabToggles.StableGrab = Value
        toggleGrab("StableGrab", "Stable")
    end
})
SlotTab:AddButton({
    Name = "座標",
    Callback = function()
        for _, part in pairs(game:GetService("Workspace"):GetDescendants()) do
            if game.Players.LocalPlayer.Character:FindFirstChild("Head") and
                part:IsA("BasePart" or "UnionOperation" or "Model") and part.Anchored == false and
                not part:IsDescendantOf(game.Players.LocalPlayer.Character) and part.Name == "Torso" == false and
                part.Name == "Head" == false and part.Name == "Right Arm" == false and part.Name == "Left Arm" == false and
                part.Name == "Right Leg" == false and part.Name == "Left Leg" == false and part.Name ==
                "HumanoidRootPart" == false and part.Name == "CreatureBlobman" == false then
                local ForceInstance = Instance.new("BodyPosition")
                for i, c in pairs(part:GetChildren()) do
                    if c:IsA("BodyPosition") or c:IsA("BodyGyro") then
                        c:Destroy()
                    end
                end
                for i = 1,5 do
                ForceInstance.Parent = part
                ForceInstance.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                ForceInstance.Position = Vector3.new(G_y, G_y, G_y)
            end
          end
        end
    end
})
SlotTab:AddSlider({
    Name = "座標",
    Min = -60000000,
    Max = 60000000,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 1000000,
    Default = G_y,
    Save = false,
    Flag = "zahyu",
    Callback = function(value)
        G_y = value
    end
})

SlotTab:AddButton({
    Name = "紫の家",
    Callback = function()
        local me = game.Players.LocalPlayer
        local murasaki = Vector3.new(255, -8, 449)
        if me.Character then
            me.Character:MoveTo(murasaki)
        end
    end
})
SlotTab:AddButton({
    Name = "緑の家",
    Callback = function()
        local me = game.Players.LocalPlayer
        local midori = Vector3.new(-534, -8, 93)
        if me.Character then
            me.Character:MoveTo(midori)
        end
    end
})
SlotTab:AddButton({
    Name = "青の家",
    Callback = function()
        local me = game.Players.LocalPlayer
        local ao = Vector3.new(512, 82, -343)
        if me.Character then
            me.Character:MoveTo(ao)
        end
    end
})
SlotTab:AddButton({
    Name = "オレンジ？の家",
    Callback = function()
        local me = game.Players.LocalPlayer
        local orennzi = Vector3.new(548, 122, -73)
        if me.Character then
            me.Character:MoveTo(orennzi)
        end
    end
})

SlotTab:AddButton({
    Name = "赤の家",
    Callback = function()
          local me = game.Players.LocalPlayer
        local aka = Vector3.new(-493, -8, -165)
        if me.Character then
            me.Character:MoveTo(aka)
        end
    end
})
SlotTab:AddButton({
    Name = "リス地",
    Callback = function()
        local player = game.Players.LocalPlayer
        local targetPosition = Vector3.new(0, -7, 0)
        if player.Character then
            player.Character:MoveTo(targetPosition)
        end
    end
})
SlotTab:AddButton({
    Name = "山の緑の家",
    Callback = function()
        local player = game.Players.LocalPlayer
        local targetPosition = Vector3.new(-278, 147, 310)
        if player.Character then
            player.Character:MoveTo(targetPosition)
        end
    end
})
SlotTab:AddButton({
    Name = "赤い畑の家",
    Callback = function()
        local player = game.Players.LocalPlayer
        local targetPosition = Vector3.new(-203, 84, -292)
        if player.Character then
            player.Character:MoveTo(targetPosition)
        end
    end
})
SlotTab:AddButton({
    Name = "普通の洞窟",
    Callback = function()
        local player = game.Players.LocalPlayer
        local targetPosition = Vector3.new(-261, -7, 533)
        if player.Character then
            player.Character:MoveTo(targetPosition)
        end
    end
})
SlotTab:AddButton({
    Name = "スロットの裏の洞窟",
    Callback = function()
        local player = game.Players.LocalPlayer
        local targetPosition = Vector3.new(-34, -7, -299)
        if player.Character then
            player.Character:MoveTo(targetPosition)
        end
    end
})
SlotTab:AddButton({
    Name = "毒井戸",
    Callback = function()
        local player = game.Players.LocalPlayer
        local targetPosition = Vector3.new(106, -25, 279)
        if player.Character then
            player.Character:MoveTo(targetPosition)
        end
    end
})
SlotTab:AddButton({
    Name = "雪山",
    Callback = function()
        local player = game.Players.LocalPlayer
        local targetPosition = Vector3.new(-414, 231, 480)
        if player.Character then
            player.Character:MoveTo(targetPosition)
        end
    end
})
SlotTab:AddButton({
    Name = "Slot1",
    Callback = function()
        local player = game.Players.LocalPlayer
        local targetPosition = Vector3.new(54, -7, -115)
        if player.Character then
            player.Character:MoveTo(targetPosition)
        end
    end
})
SlotTab:AddButton({
    Name = "Slot2",
    Callback = function()
        local player = game.Players.LocalPlayer
        local targetPosition = Vector3.new(170, -8, 527)
        if player.Character then
            player.Character:MoveTo(targetPosition)
        end
    end
})
SlotTab:AddButton({
    Name = "Slot3",
    Callback = function()
        local player = game.Players.LocalPlayer
        local targetPosition = Vector3.new(-213, 83, 421)
        if player.Character then
            player.Character:MoveTo(targetPosition)
        end
    end
})
SlotTab:AddButton({
    Name = "Slot4",
    Callback = function()
        local player = game.Players.LocalPlayer
        local targetPosition = Vector3.new(-540, -6, -40)
        if player.Character then
            player.Character:MoveTo(targetPosition)
        end
    end
})
FlingTab:AddButton({
    Name = "フリングスクリプト",
    Callback = function()
loadstring(game:HttpGet("https://raw.githubusercontent.com/jadpy/suki/refs/heads/main/fling%20script"))()
    end
})
GTab:AddButton({
    Name = "ディスコードリンク",
    Callback = function()
        setclipboard("https://discord.gg/YqmhW4Se8B")
        toclipboard("https://discord.gg/YqmhW4Se8B")
    end
})
HomeTab:AddParagraph("ユーザーname", game.Players.LocalPlayer.Name)
HomeTab:AddParagraph("表示の名前", game.Players.LocalPlayer.DisplayName)
HomeTab:AddParagraph("ユーザーid ", tostring(game.Players.LocalPlayer.UserId))
HomeTab:AddParagraph("アカウントの時間", tostring(game.Players.LocalPlayer.AccountAge))
HomeTab:AddParagraph("ゲームの名前", gameName)
HomeTab:AddParagraph("ゲームid", tostring(game.PlaceId))
HomeTab:AddParagraph("最大人数", tostring(game.Players.MaxPlayers))
PATab:AddLabel("これはまだテストです。追加済み")
PATab:AddLabel("アンチウエブフック追加。いやんはぶ起動時に自動で起動")
PATab:AddLabel("アンチウエブフック内容")
PATab:AddLabel("ウエブフックの情報を送信するのを防ぐ")
PATab:AddLabel("ウエブフック逆探知。ウエブフックのリンクコピーをコピーする")

iyanTab:AddLabel("いやんはぶのグループ")
iyanTab:AddLabel("id : gegdgmvs35..表示名suu")
iyanTab:AddLabel("id : Hello838338383")
iyanTab:AddLabel("id : MidoriMiroru_73816")
iyanTab:AddLabel("id : ftapplayer_LOL")
arasuTab:AddLabel("うんこは自分でお願いします…")
arasuTab:AddLabel("食べたらオンにしてゲロかけてください")
arasuTab:AddToggle({
    Name = "ゲロかけALL",
    Default = false,
    Callback = function(value)
        bringingEnabled = value
        if bringingEnabled then
            bringCoroutine = coroutine.create(function()
                local localPlayer = game.Players.LocalPlayer
                local Players = game:GetService("Players")
                local localPlayer = Players.LocalPlayer

                while bringingEnabled do
                    local targetPosition = localPlayer.Character.HumanoidRootPart.Position + Vector3.new(1.5, 0, 0)

                    for _, player in ipairs(Players:GetPlayers()) do
                        if player ~= localPlayer and player.Character and
                            player.Character:FindFirstChild("HumanoidRootPart") then
                            local isFriend = _G.furend and localPlayer:IsFriendsWith(player.UserId)

                            if not isFriend then
                                player.Character.HumanoidRootPart.CFrame = CFrame.new(targetPosition)
                            end
                        end
                    end
                    wait()
                end
            end)
            coroutine.resume(bringCoroutine)
        else
            if bringCoroutine then
                bringingEnabled = false
                bringCoroutine = nil
            end
        end
    end
})

arasuTab:AddToggle({
    Name = "ホワイトフレンド",
    Default = false,
    Callback = function(state)
        _G.furend = state
    end
})

local Players = game:GetService("Players")
local playerNames = {}
local SelectedPlayer
local isFrozen = false
local PlayerDropdown
local currentCoroutine = nil
local bringPosition = Vector3.new(0, 50, 0)
local Players = game:GetService("Players")
local playerNames = {}
local nameMap = {}
local SelectedPlayer
local PlayerDropdown

local function RefreshList()
    playerNames = {}
    nameMap = {}
    for _, player in ipairs(Players:GetPlayers()) do
        local display = player.DisplayName .. " (" .. player.Name .. ")"
        table.insert(playerNames, display)
        nameMap[display] = player.Name
    end
    if PlayerDropdown then
        PlayerDropdown:Refresh(playerNames, true)
    end
end

PlayerDropdown = freezeATab:AddDropdown({
    Name = "どこでも突破するプレイヤーを選ぶ",
    Options = playerNames,
    Callback = function(selectedDisplay)
        SelectedPlayer = nameMap[selectedDisplay]
    end
})

Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        if SelectedPlayer == player.Name then
            toggleFreezeAndBring(player)
        end
    end)
    RefreshList()
end)

Players.PlayerRemoving:Connect(RefreshList)
RefreshList()

function toggleFreezeAndBring(player)
    if player then
        local targetPlayer = player
        if targetPlayer and targetPlayer.Character then
            local humanoidRootPart = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
            if humanoidRootPart then
                humanoidRootPart.CFrame = CFrame.new(bringPosition)
            end
            for _, part in pairs(targetPlayer.Character:GetChildren()) do
                if part:IsA("BasePart") then
                    part.Anchored = isFrozen
                end
            end
        end
    end
end
function infiniteLoop()
    while isFrozen do
        if SelectedPlayer then
            local targetPlayer = Players:FindFirstChild(SelectedPlayer)
            if targetPlayer then
                toggleFreezeAndBring(targetPlayer)
            end
        end
        wait(0.01)
    end
end
freezeATab:AddToggle({
    Name = "どこでも突破",
    Default = false,
    Callback = function(toggleState)

        isFrozen = toggleState

        if SelectedPlayer then
            local targetPlayer = Players:FindFirstChild(SelectedPlayer)
            if targetPlayer then
                toggleFreezeAndBring(targetPlayer)
            end
        end

        if isFrozen then
            if currentCoroutine then
                currentCoroutine = nil
            end
            currentCoroutine = coroutine.wrap(infiniteLoop)()
        else
            if currentCoroutine then
                currentCoroutine = nil
            end
            for _, playerName in ipairs(playerNames) do
                local targetPlayer = Players:FindFirstChild(playerName)
                if targetPlayer and targetPlayer.Character then
                    for _, part in pairs(targetPlayer.Character:GetChildren()) do
                        if part:IsA("BasePart") then
                            part.Anchored = false
                        end
                    end
                end
            end
        end
    end
})



freezeATab:AddButton({
    Name = "リスト更新",
    Callback = RefreshList
})
RefreshList()
function esp(p, cr)
    if not ESPEnabled then
        return
    end
    local h = cr:WaitForChild("Humanoid")
    local hrp = cr:WaitForChild("HumanoidRootPart")
    local text = Drawing.new("Text")
    text.Visible = false
    text.Center = true
    text.Outline = true
    text.Font = 2
    text.Color = Color3.fromRGB(255, 255, 255)
    text.Size = 17
    local c1, c2, c3
    local function cleanup()
        text.Visible = false
        text:Remove()
        if c1 then
            c1:Disconnect()
            c1 = nil
        end
        if c2 then
            c2:Disconnect()
            c2 = nil
        end
        if c3 then
            c3:Disconnect()
            c3 = nil
        end
    end
    c1 = game:GetService("RunService").RenderStepped:Connect(function()
        if not ESPEnabled then
            cleanup()
            return
        end
        local hrp_pos, hrp_on_screen = workspace.CurrentCamera:WorldToViewportPoint(hrp.Position)
        if hrp_on_screen then
            text.Position = Vector2.new(hrp_pos.X, hrp_pos.Y)
            text.Text = string.format("%s (%.1f m)", p.Name,
                (hrp.Position - workspace.CurrentCamera.CFrame.Position).Magnitude)
            text.Visible = true
        else
            text.Visible = false
        end
    end)
    c2 = cr.AncestryChanged:Connect(function(_, parent)
        if not parent then
            cleanup()
        end
    end)
    c3 = h.HealthChanged:Connect(function(health)
        if health <= 0 then
            cleanup()
        end
    end)
end
function playerAdded(p)
    if p.Character then
        esp(p, p.Character)
    end
    p.CharacterAdded:Connect(function(cr)
        esp(p, cr)
    end)
end
ESPTab:AddToggle({
    Name = "ESP",
    Default = false,
    Callback = function(state)
        ESPEnabled = state
        if ESPEnabled then
            for _, p in ipairs(game:GetService("Players"):GetPlayers()) do
                if p ~= game:GetService("Players").LocalPlayer then
                    playerAdded(p)
                end
            end
            game:GetService("Players").PlayerAdded:Connect(playerAdded)
        end
    end
})

local Players = game:GetService("Players")
local playerNames = {}
local nameMap = {}
local targetPlayerName

function update1()
    playerNames = {}
    nameMap = {}
    for _, player in ipairs(Players:GetPlayers()) do
        local display = player.DisplayName .. " (" .. player.Name .. ")"
        table.insert(playerNames, display)
        nameMap[display] = player.Name
    end
end

update1()

local playerDropdown = freezeATab:AddDropdown({
    Name = "無敵突破したいプレイヤーを選択",
    Options = playerNames,
    Default = nil,
    Callback = function(selectedDisplay)
        targetPlayerName = nameMap[selectedDisplay]
    end
})

Players.PlayerAdded:Connect(function()
    update1()
    playerDropdown:Refresh(playerNames, true)
end)

Players.PlayerRemoving:Connect(function()
    update1()
    playerDropdown:Refresh(playerNames, true)
end)

local localPlayer = game.Players.LocalPlayer
playerCharacter = localPlayer.Character or localPlayer.CharacterAdded:Wait()

localPlayer.CharacterAdded:Connect(function(character)
    playerCharacter = character
end)

toysFolder = workspace:FindFirstChild(localPlayer.Name .. "SpawnedInToys")

autoyukidama = false
targetPlayerName = ""
selectedObjectName = "BallSnowball"

function yukidamafling(min, max)
    return Vector3.new(math.random(min, max), math.random(min, max), math.random(min, max))
end

function spawnyukidama(objectName)
    task.spawn(function()
        local rotation = Vector3.new(0, 9, 10)
        pcall(function()
            game:GetService("ReplicatedStorage").MenuToys.SpawnToyRemoteFunction:InvokeServer(objectName,
                playerCharacter.Head.CFrame, rotation)
        end)
    end)
end

function yukidamasupon(objectName)
    if not toysFolder then
        toysFolder = workspace:FindFirstChild(localPlayer.Name .. "SpawnedInToys")
    end

    if toysFolder then
        for _, item in pairs(toysFolder:GetChildren()) do
            if item.Name == objectName then
                return item
            end
        end
    end
    return nil
end

targetBodyPartName = ""

function teleportyukidama(item, targetPlayer)
    if item and targetPlayer and targetPlayer.Character then
        local targetPart = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
        if targetPart then
            local targetPos = targetPart.CFrame
            item.CFrame = targetPos
            item.AssemblyLinearVelocity = yukidamafling(-100, 130)
            item.AssemblyAngularVelocity = yukidamafling(-500, 500)
        end
    end
end
function yukidamaspeed()
    while autoyukidama do
        pcall(function()
            local obj = yukidamasupon(selectedObjectName)
            if not obj then
                spawnyukidama(selectedObjectName)
                wait(0.08)
            else
                local targetPlayer = game.Players:FindFirstChild(targetPlayerName)
                if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    for _, child in pairs(obj:GetChildren()) do
                        if child:IsA("BasePart") then
                            teleportyukidama(child, targetPlayer)
                        end
                    end
                end
            end
        end)
        wait(0.08)
    end
end

function freeyukidama(enabled)
    autoyukidama = enabled
    if enabled then
        task.spawn(yukidamaspeed)
    end
end

local auRadius = 20
local ACoroutine = nil

freezeATab:AddToggle({
    Name = "無敵突破",
    Default = false,
    Callback = function(enabled)
        if enabled then
            freeyukidama(true)

            ACoroutine = coroutine.create(function()
                while true do
                    local success, err = pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local root = character.HumanoidRootPart

                            for _, player in pairs(Players:GetPlayers()) do
                                local toy = workspace:FindFirstChild(player.Name .. "SpawnedInToys")
                                if toy then
                                    for _, obj in pairs(toy:GetChildren()) do
                                        coroutine.wrap(function()
                                            if obj:IsA("Model") and obj.Name == "BallSnowball" then
                                                local soundPart = obj:FindFirstChild("SoundPart")
                                                if soundPart and soundPart:IsA("BasePart") then
                                                    local dist = (soundPart.Position - root.Position).Magnitude
                                                    if dist <= auRadius then
                                                        SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
                                                        task.wait(0)
                                                        local velocity =
                                                            soundPart:FindFirstChild("l") or
                                                                Instance.new("BodyVelocity", soundPart)
                                                        velocity.Name = "l"
                                                        velocity.Velocity = Vector3.new(0, 0, 0)
                                                        velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                                        Debris:AddItem(velocity, 0)
                                                    end
                                                end
                                            end
                                        end)()
                                    end
                                end
                            end
                        end
                    end)

                    local pl = Workspace:FindFirstChild("PlotItems")
                    for i = 1, 5 do
                        local pt = pl:FindFirstChild("Plot" .. i)
                    end
                    if pt then
                        for _, obj in pairs(pt:GetChildren()) do
                            coroutine.wrap(function()
                                if obj:IsA("Model") and obj.Name == "BallSnowball" then
                                    local soundPart = obj:FindFirstChild("SoundPart")
                                    if soundPart and soundPart:IsA("BasePart") then
                                        local dist = (soundPart.Position - root.Position).Magnitude
                                        if dist <= auRadius then
                                            SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
                                            task.wait(0)
                                            local velocity =
                                                soundPart:FindFirstChild("l") or Instance.new("BodyVelocity", soundPart)
                                            velocity.Name = "l"
                                            velocity.Velocity = Vector3.new(0, 0, 0)
                                            velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                            Debris:AddItem(velocity, 0)
                                        end
                                    end
                                end
                            end)()
                        end
                    end
                    wait()
                end
            end)
            coroutine.resume(ACoroutine)
        else
            freeyukidama(false)
            if ACoroutine then
                coroutine.close(ACoroutine)
                ACoroutine = nil
            end
        end
    end
})

objectOptions = {"BallSnowball"}
freezeATab:AddDropdown({
    Name = "当てるオブジェクトを選ぶ",
    Options = objectOptions,
    Default = "BallSnowball",
    Callback = function(selectedObject)
        selectedObjectName = selectedObject
    end
})

local runningCoroutine = nil
local Clipon = false

CharacterTab:AddToggle({
    Name = "ノクリップ",
    Default = false,
    Callback = function(value)
        Clipon = value
        local Plr = Players.LocalPlayer
        local Players = game:GetService("Players")
        local Workspace = game:GetService("Workspace")
        local LocalPlayer = Players.LocalPlayer

        local function setupToyMonitor(player)
            local toyFolderName = player.Name .. "SpawnedInToys"
            local toyFolder = Workspace:FindFirstChild(toyFolderName)
            if toyFolder and toyFolder:IsA("st") then
                for _, toy in ipairs(toyFolder:GetChildren()) do
                    if toy.Name == "BallSnowball" then
                        for _, part in ipairs(toy:GetChildren()) do
                            if part.Name == "SnowRagdollPart" then
                                part.CanCollide = false
                                part.CanQuery = false
                                part.CanTouch = false
                                part.Size = Vector3.new(0, 0, 0)
                                for _, weld in ipairs(part:GetChildren()) do
                                    if weld:IsA("WeldConstraint") then
                                        weld:Destroy()
                                    end
                                end
                            end
                        end
                        toy.ChildAdded:Connect(function(child)
                            if child.Name == "SnowRagdollPart" and child:IsA("BasePart") then
                                child.CanCollide = false
                                child.CanQuery = false
                                child.CanTouch = false
                                child.Size = Vector3.new(0, 0, 0)
                                for _, weld in ipairs(child:GetChildren()) do
                                    if weld:IsA("WeldConstraint") then
                                        weld:Destroy()
                                    end
                                end
                            end
                        end)
                    end
                end
                toyFolder.ChildAdded:Connect(function(child)
                    if child.Name == "BallSnowball" then
                        for _, part in ipairs(child:GetChildren()) do
                            if part.Name == "SnowRagdollPart" then
                                part.CanCollide = false
                                part.CanQuery = false
                                part.CanTouch = false
                                part.Size = Vector3.new(0, 0, 0)
                                for _, weld in ipairs(part:GetChildren()) do
                                    if weld:IsA("WeldConstraint") then
                                        weld:Destroy()
                                    end
                                end
                            end
                        end
                        child.ChildAdded:Connect(function(grandchild)
                            if grandchild.Name == "SnowRagdollPart" and grandchild:IsA("BasePart") then
                                grandchild.CanCollide = false
                                grandchild.CanQuery = false
                                grandchild.CanTouch = false
                                grandchild.Size = Vector3.new(0, 0, 0)
                                for _, weld in ipairs(grandchild:GetChildren()) do
                                    if weld:IsA("WeldConstraint") then
                                        weld:Destroy()
                                    end
                                end
                            end
                        end)
                    end
                end)
            end
        end

        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LocalPlayer then
                setupToyMonitor(player)
            end
        end

        Players.PlayerAdded:Connect(function(player)
            if player ~= LocalPlayer then
                player.CharacterAdded:Wait()
                task.wait()
                setupToyMonitor(player)
            end
        end)
        if Clipon then
            if runningCoroutine then
                runningCoroutine = nil
            end

            runningCoroutine = coroutine.create(function()
                while Clipon do
                    for _, obj in pairs(Workspace:GetChildren()) do
                        if obj.Name == Plr.Name then
                            for _, part in pairs(obj:GetChildren()) do
                                if part:IsA("BasePart") then
                                    part.CanCollide = false
                                    part.CanQuery = false
                                    part.CanTouch = false
                                end
                            end

                            local humanoidRootPart = obj:FindFirstChild("HumanoidRootPart")
                            if humanoidRootPart and humanoidRootPart:IsA("BasePart") then
                                humanoidRootPart.CanCollide = false
                                humanoidRootPart.CanQuery = false
                                humanoidRootPart.CanTouch = false

                                local firePlayerPart = humanoidRootPart:FindFirstChild("FirePlayerPart")
                                if firePlayerPart and firePlayerPart:IsA("BasePart") then
                                    firePlayerPart.CanCollide = false
                                    firePlayerPart.CanQuery = false
                                    firePlayerPart.CanTouch = false
                                    firePlayerPart.Size = Vector3.new(0, 0, 0)
                                end

                                local ragdollTouchedHitbox = humanoidRootPart:FindFirstChild("RagdollTouchedHitbox")
                                if ragdollTouchedHitbox and ragdollTouchedHitbox:IsA("BasePart") then
                                    ragdollTouchedHitbox.CanCollide = false
                                    ragdollTouchedHitbox.CanQuery = false
                                    ragdollTouchedHitbox.CanTouch = false
                                    ragdollTouchedHitbox.Size = Vector3.new(0, 0, 0)
                                end
                            end
                        end
                    end
                    wait()
                end
            end)
            coroutine.resume(runningCoroutine)
        else
            if runningCoroutine then
                Clipon = false
                runningCoroutine = nil
            end
        end
    end
})

local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")

function toggleAnchored(toggled)
    local player = Players.LocalPlayer
    local toys = Workspace:FindFirstChild(player.Name .. "SpawnedInToys")

    if not toys then
        return
    end
    local creature1 = toys:FindFirstChild("CreatureBlobman")
    local creature2, creature3, creature4

    if creature1 then
        local all = toys:GetChildren()
        local found = 0

        for i = 1, #all do
            if all[i].Name == "CreatureBlobman" then
                found = found + 1
                if found == 2 then
                    creature2 = all[i]
                end
                if found == 3 then
                    creature3 = all[i]
                end
                if found == 4 then
                    creature4 = all[i]
                end
                if found == 4 then
                    break
                end
            end
        end
    end

    local function anchorParts(model)
        if not model then
            return
        end
        local parts = {model:FindFirstChild("Head", true), model:FindFirstChild("Torso", true),
                       model:FindFirstChild("LeftArm", true), model:FindFirstChild("RightArm", true),
                       model:FindFirstChild("LeftLeg", true), model:FindFirstChild("RightLeg", true)}
        for _, part in ipairs(parts) do
            if part and part:IsA("BasePart") then
                part.Anchored = toggled
            end
        end
    end

    anchorParts(creature1)
    anchorParts(creature2)
    anchorParts(creature3)
    anchorParts(creature4)
end

CharacterTab:AddLabel("ブロブマンのスピード。高さ、ノクリップを決めれます")

CharacterTab:AddToggle({
    Name = "ブロブマン、アンカー",
    Default = false,
    Callback = function(blobanchor)
        if blobanchor then
            toggleAnchored(true)
        else
            toggleAnchored(false)
        end
    end
})

noClipCoroutine = nil
noClipEnabled = false

CharacterTab:AddToggle({
    Name = "ブロブマン、ノクリップ",
    Default = false,
    Callback = function(aiueo)
        if aiueo then
            if not noClipCoroutine then
                noClipEnabled = true
                noClipCoroutine = coroutine.create(function()
                    while noClipEnabled do
                        toggleNoClip(true)
                        wait()
                    end
                end)
                coroutine.resume(noClipCoroutine)
            end
        else
            noClipEnabled = false
            if noClipCoroutine then
                coroutine.resume(noClipCoroutine)
                noClipCoroutine = nil
            end
            toggleNoClip(false)
        end
    end
})

originalHipHeight = nil
originalWalkSpeed = nil

findHumanoidCoroutine = nil
findHumanoidEnabled = false
CharacterTab:AddToggle({
    Name = "ブロブマン、スピード、高さ",
    Default = false,
    Callback = function(aiueo)
        if aiueo then
            if not findHumanoidCoroutine then
                findHumanoidEnabled = true
                findHumanoidCoroutine = coroutine.create(function()
                    while findHumanoidEnabled do
                        findHumanoidCreature()
                        wait(0)
                    end
                end)
                coroutine.resume(findHumanoidCoroutine)
            end
        else
            findHumanoidEnabled = false
            if findHumanoidCoroutine then
                coroutine.resume(findHumanoidCoroutine)
                findHumanoidCoroutine = nil
            end

            local toys = Workspace:FindFirstChild(Players.LocalPlayer.Name .. "SpawnedInToys")
            if toys then
                for _, item in ipairs(toys:GetChildren()) do
                    if item.Name == "CreatureBlobman" then
                        for _, descendant in ipairs(item:GetDescendants()) do
                            if descendant:IsA("Humanoid") then
                                local humanoid = descendant

                                if originalHipHeight then
                                    humanoid.HipHeight = originalHipHeight
                                end
                                if originalWalkSpeed then
                                    humanoid.WalkSpeed = originalWalkSpeed
                                end
                            end
                        end
                    end
                end
            end
        end
    end
})

CharacterTab:AddSlider({
    Name = "ブロブマンの高さ",
    Min = -500,
    Max = 500,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 10,
    Default = 0,
    Save = false,
    Flag = "a",
    Callback = function(value)
        o_aa = value
    end
})

CharacterTab:AddSlider({
    Name = "ブロブマンのスピード",
    Min = 0,
    Max = 300,
    Color = Color3.fromRGB(255, 255, 255),
    ValueName = ".",
    Increment = 10,
    Default = 20,
    Save = false,
    Flag = "b",
    Callback = function(value)
        w_aa = value
    end
})

local Players = game:GetService("Players")
local camera = workspace.CurrentCamera

local targetPlayerName = nil
local targetPartName = "Humanoid"

function orosu(targetPlayer)
    if targetPlayer and targetPlayer.Character then
        local targetPart = targetPlayer.Character:FindFirstChild(targetPartName)
        if targetPart then
            isViewing = true
            camera.CameraType = Enum.CameraType.Custom
            camera.CameraSubject = targetPart
        end
    end
end

function stop()
    isViewing = false
    camera.CameraType = Enum.CameraType.Custom
    camera.CameraSubject = Players.LocalPlayer.Character.Humanoid
end

local playerNames = {}
local nameMap = {}
local targetPlayerName

for _, player in ipairs(Players:GetPlayers()) do
    local display = player.DisplayName .. " (" .. player.Name .. ")"
    table.insert(playerNames, display)
    nameMap[display] = player.Name
end

local playerDropdown = ESPTab:AddDropdown({
    Name = "視点を見たいプレイヤーを選択",
    Options = playerNames,
    Callback = function(selectedDisplay)
        targetPlayerName = nameMap[selectedDisplay]
    end
})

local bodyParts = {"Humanoid", "Head", "Torso", "Left Arm", "Right Arm", "Left Leg", "Right Leg", "HumanoidRootPart"}

local partDropdown = ESPTab:AddDropdown({
    Name = "見る部位",
    Options = bodyParts,
    Callback = function(selectedPart)
        targetPartName = selectedPart
    end
})

function RefreshPlayerList()
    playerNames = {}
    nameMap = {}
    for _, player in ipairs(Players:GetPlayers()) do
        local display = player.DisplayName .. " (" .. player.Name .. ")"
        table.insert(playerNames, display)
        nameMap[display] = player.Name
    end
    playerDropdown:Refresh(playerNames, true)
end

Players.PlayerAdded:Connect(RefreshPlayerList)
Players.PlayerRemoving:Connect(RefreshPlayerList)

ESPTab:AddToggle({
    Name = "プレイヤーの視点をみる",
    Default = false,
    Callback = function(value)
        if value and targetPlayerName then
            local targetPlayer = Players:FindFirstChild(targetPlayerName)
            if targetPlayer then
                orosu(targetPlayer)
            end
        elseif not value then
            stop()
        end
    end
})
local Players = game:GetService("Players")
local localPlayer = Players.LocalPlayer

local anchoredParts = {}
local anchoredConnections = {}
local anchorGrabCoroutine = nil

local toggleBringEnabled = true  
local R_raaa = 10             

-- 指定された親の子孫かどうか判定
local function isDescendantOf(target, other)
    local currentParent = target.Parent
    while currentParent do
        if currentParent == other then
            return true
        end
        currentParent = currentParent.Parent
    end
    return false
end

local function getNearestPlayer()
    local nearestPlayer
    local nearestDistance = math.huge
    local playerCharacter = localPlayer.Character
    if not playerCharacter or not playerCharacter:FindFirstChild("HumanoidRootPart") then
        return nil
    end

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= localPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local distance = (playerCharacter.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
            if distance < nearestDistance then
                nearestDistance = distance
                nearestPlayer = player
            end
        end
    end

    return nearestPlayer
end

local function cleanupConnections(connectionTable)
    for i = #connectionTable, 1, -1 do
        connectionTable[i]:Disconnect()
        table.remove(connectionTable, i)
    end
end
local function createHighlight(parent)
    local highlight = Instance.new("Highlight")
    highlight.DepthMode = Enum.HighlightDepthMode.Occluded
    highlight.FillTransparency = 1
    highlight.Name = "Highlight"
    highlight.OutlineColor = Color3.new(0, 0, 1)
    highlight.OutlineTransparency = 0.5
    highlight.Parent = parent
    return highlight
end

local function createBodyMovers(part, position, rotation)
    local bodyPosition = Instance.new("BodyPosition")
    local bodyGyro = Instance.new("BodyGyro")

    bodyPosition.P = 15000
    bodyPosition.D = 200
    bodyPosition.MaxForce = Vector3.new(5000000, 5000000, 5000000)
    bodyPosition.Position = position
    bodyPosition.Parent = part

    bodyGyro.P = 15000
    bodyGyro.D = 200
    bodyGyro.MaxTorque = Vector3.new(5000000, 5000000, 5000000)
    bodyGyro.CFrame = rotation
    bodyGyro.Parent = part
end

local function anchorGrab()
    while true do
        pcall(function()
            local grabParts = workspace:FindFirstChild("GrabParts")
            if not grabParts then
                return
            end

            local grabPart = grabParts:FindFirstChild("GrabPart")
            if not grabPart then
                return
            end

            local weldConstraint = grabPart:FindFirstChild("WeldConstraint")
            if not weldConstraint or not weldConstraint.Part1 then
                return
            end

            local parentModel = weldConstraint.Part1.Parent
            if not parentModel then
                return
            end

            local primaryPart = parentModel.PrimaryPart
            if not primaryPart then
                return
            end

            if isDescendantOf(primaryPart, workspace.Map) then
                return
            end

            for _, player in pairs(Players:GetPlayers()) do
                if player.Character and isDescendantOf(primaryPart, player.Character) then
                    return
                end
            end

            if not table.find(anchoredParts, primaryPart) then
                local highlight = createHighlight(primaryPart.Parent)
                table.insert(anchoredParts, primaryPart)

                local connection = primaryPart.Parent.DescendantAdded:Connect(function(descendant)
                    if onPartOwnerAdded then
                        onPartOwnerAdded(descendant, primaryPart)
                    end
                end)
                table.insert(anchoredConnections, connection)
            end

            for _, child in ipairs(primaryPart:GetChildren()) do
                if child:IsA("BodyPosition") or child:IsA("BodyGyro") then
                    child:Destroy()
                end
            end

            while workspace:FindFirstChild("GrabParts") do
                wait()
            end
            createBodyMovers(primaryPart, primaryPart.Position, primaryPart.CFrame)
        end)
        wait()
    end
end

local function cleanupAnchoredParts()
    for _, part in ipairs(anchoredParts) do
        if part then
            if part:FindFirstChild("BodyPosition") then
                part.BodyPosition:Destroy()
            end
            if part:FindFirstChild("BodyGyro") then
                part.BodyGyro:Destroy()
            end
            if part.Parent and part.Parent:FindFirstChild("Highlight") then
                part.Parent.Highlight:Destroy()
            end
        end
    end

    cleanupConnections(anchoredConnections)
    anchoredParts = {}
end
local function processPart(part)
    local character = Players.LocalPlayer.Character
    if not toggleBringEnabled or not character or not character:FindFirstChild("Head") or not part:IsA("BasePart") then
        return
    end
    if part.Anchored or part:IsDescendantOf(character) then
        return
    end
    if part.Name == "Torso" or part.Name == "Head" or part.Name == "Right Arm" or part.Name == "Left Arm" or
       part.Name == "Right Leg" or part.Name == "Left Leg" or part.Name == "HumanoidRootPart" then
        return
    end
    if not part:FindFirstChildOfClass("BodyPosition") then
        local ForceInstance = Instance.new("BodyPosition")
        ForceInstance.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        ForceInstance.Position = Vector3.new(0, R_raaa, 0)
        ForceInstance.P = 6000000000000
        ForceInstance.D = 6000000
        ForceInstance.Parent = part
    end
end


objTab:AddToggle({
    Name = "アンカー掴む",
    Default = false,
    Callback = function(enabled)
        if enabled then
            if not anchorGrabCoroutine or coroutine.status(anchorGrabCoroutine) == "dead" then
                anchorGrabCoroutine = coroutine.create(anchorGrab)
                coroutine.resume(anchorGrabCoroutine)
            end
        else
            anchorGrabCoroutine = nil
        end
    end
})

objTab:AddButton({
    Name = "アンカー解除",
    Callback = function()
        cleanupAnchoredParts()
    end
})

Players = game:GetService("Players")

local playerNames = {}
local nameMap = {}
local playerDropdown
local P = {}

for _, player in ipairs(Players:GetPlayers()) do
    local display = player.DisplayName .. " (" .. player.Name .. ")"
    table.insert(playerNames, display)
    nameMap[display] = player.Name
end

function RefreshPlayerList()
    playerNames = {}
    nameMap = {}
    for _, player in ipairs(Players:GetPlayers()) do
        local display = player.DisplayName .. " (" .. player.Name .. ")"
        table.insert(playerNames, display)
        nameMap[display] = player.Name
    end
    if playerDropdown then
        playerDropdown:Refresh(playerNames, true)
    end
end

Players.PlayerAdded:Connect(RefreshPlayerList)
Players.PlayerRemoving:Connect(RefreshPlayerList)
playerDropdown = killTab:AddDropdown({
    Name = "リスキルしたいプレイヤーを選ぶ",
    Options = playerNames,
    Callback = function(Option)
        P.who = {}
        local realName = nameMap[Option]
        local target = Players:FindFirstChild(realName)
        if target then
            table.insert(P.who, target)
        end
    end
})


killTab:AddToggle({
    Name = "リスキル",
    CurrentValue = false,
    Callback = function(Value)
        loopkill = Value
        task.spawn(function()
            while loopkill and task.wait() do
                for _, target in pairs(P.who or {}) do
                    if target and target.Parent then
                        if typeof(plr_kill2) == "function" then
                            plr_kill2(target)
                        end
                    end
                end
            end
        end)
    end
})


Players = game:GetService("Players")

killTab:AddToggle({
    Name = "キルオール",
    CurrentValue = false,
    Callback = function(Value)
        loopkill = Value
        task.spawn(function()
            while loopkill and task.wait() do
                for _, target in ipairs(Players:GetPlayers()) do
                    if target ~= Players.LocalPlayer and target.Parent then
                        if typeof(plr_kill2) == "function" then
                            plr_kill2(target)
                        end
                    end
                end
            end
        end)
    end
})


local Auraobj = 20
AuraobjTab:AddToggle({
    Name = "オブジェクト掴むオーラ",
    Default = false,
    Callback = function(enabled)
        if enabled then
            gvCoroutine = coroutine.create(function()
                while true do
                    pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local root = character.HumanoidRootPart

                            function processModelFolder(folder)
                                for _, model in pairs(folder:GetChildren()) do
                                    if model:IsA("Model") then
                                        for _, part in pairs(model:GetDescendants()) do
                                            if part:IsA("Part") or part:IsA("SoundPart") then
                                                local dist = (part.Position - root.Position).Magnitude
                                                if dist <= Auraobj then
                                                    SetNetworkOwner:FireServer(part, part.CFrame)
                                                    task.wait()
                                                    local velocity = Instance.new("BodyVelocity", part)
                                                    velocity.Name = "l"
                                                    velocity.Velocity = Vector3.new(0, 0, 0)
                                                    velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                                    Debris:AddItem(velocity, 0.5)
                                                end
                                            end
                                        end
                                    end
                                end
                            end

                            for _, player in pairs(Players:GetPlayers()) do
                                local toysFolders = {workspace:FindFirstChild(player.Name .. "SpawnedInToys"),
                                                     workspace:FindFirstChild("st") and
                                    workspace.st:FindFirstChild(player.Name .. "SpawnedInToys")}
                                for _, folder in pairs(toysFolders) do
                                    if folder then
                                        processModelFolder(folder)
                                    end
                                end
                            end

                            local mapFolder = workspace:FindFirstChild("map")
                            if mapFolder then
                                local foldersToCheck = {"Balloons", "Cave", "Cubes", "Draawers", "Farm", "Food",
                                                        "Furniture", "Glass Boxes", "Ladders", "Large Animals", "Music",
                                                        "NOT New", "Other", "Pallets", "Robloxians", "Rollers"}
                                for _, folderName in ipairs(foldersToCheck) do
                                    local folder = mapFolder:FindFirstChild(folderName)
                                    if folder then
                                        processModelFolder(folder)
                                    end
                                end
                            end
                        end
                    end)
                    task.wait(0.1)
                end
            end)
            coroutine.resume(gvCoroutine)
        else
            if gvCoroutine then
                coroutine.close(gvCoroutine)
                gvCoroutine = nil
            end
        end
    end
})
AuraobjTab:AddToggle({
    Name = "オブジェクト削除オーラ",
    Default = false,
    Callback = function(enabled)
        if enabled then
            dsCoroutine = coroutine.create(function()
                while true do
                    pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local root = character.HumanoidRootPart

                            function processModelFolder(folder)
                                for _, model in pairs(folder:GetChildren()) do
                                    if model:IsA("Model") then
                                        for _, part in pairs(model:GetDescendants()) do
                                            if part:IsA("Part") or part:IsA("SoundPart") then
                                                local dist = (part.Position - root.Position).Magnitude
                                                if dist <= Auraobj then
                                                    SetNetworkOwner:FireServer(part, part.CFrame)
                                                    task.wait()
                                                    local velocity = Instance.new("BodyVelocity", part)
                                                    velocity.Name = "l"
                                                    velocity.Velocity = Vector3.new(0, 80000, 0)
                                                    velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                                    Debris:AddItem(velocity, 1)
                                                end
                                            end
                                        end
                                    end
                                end
                            end

                            for _, player in pairs(Players:GetPlayers()) do
                                local toysFolders = {workspace:FindFirstChild(player.Name .. "SpawnedInToys"),
                                                     workspace:FindFirstChild("st") and
                                    workspace.st:FindFirstChild(player.Name .. "SpawnedInToys")}
                                for _, folder in pairs(toysFolders) do
                                    if folder then
                                        processModelFolder(folder)
                                    end
                                end
                            end

                            local mapFolder = workspace:FindFirstChild("map")
                            if mapFolder then
                                local foldersToCheck = {"Balloons", "Cave", "Cubes", "Draawers", "Farm", "Food",
                                                        "Furniture", "Glass Boxes", "Ladders", "Large Animals", "Music",
                                                        "NOT New", "Other", "Pallets", "Robloxians", "Rollers"}
                                for _, folderName in ipairs(foldersToCheck) do
                                    local folder = mapFolder:FindFirstChild(folderName)
                                    if folder then
                                        processModelFolder(folder)
                                    end
                                end
                            end
                        end
                    end)
                    task.wait(0.1)
                end
            end)
            coroutine.resume(dsCoroutine)
        else
            if dsCoroutine then
                coroutine.close(dsCoroutine)
                dsCoroutine = nil
            end
        end
    end
})

function createBodyMovers(part, position, rotation)
    local bodyPosition = part:FindFirstChild("BodyPosition") or Instance.new("BodyPosition", part)
    bodyPosition.P = 2000
    bodyPosition.D = 400
    bodyPosition.MaxForce = Vector3.new(5000000, 5000000, 5000000)
    bodyPosition.Position = position

    local bodyGyro = part:FindFirstChild("BodyGyro") or Instance.new("BodyGyro", part)
    bodyGyro.P = 2000
    bodyGyro.D = 400
    bodyGyro.MaxTorque = Vector3.new(5000000, 5000000, 5000000)
    bodyGyro.CFrame = rotation
    part.Anchored = false
end

function disableCollision(model)
    for _, part in pairs(model:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = true
        end
    end
end

function enableCollision(model)
    for _, part in pairs(model:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = true
        end
    end
end

function collectModelsInFolder(folder)
    for _, toy in ipairs(folder:GetDescendants()) do
        if toy:IsA("Model") then
            local basePart = toy:FindFirstChildWhichIsA("BasePart")
            if basePart and not activeToys[toy] then
                activeToys[toy] = {
                    angleOffset = math.random(0, 360),
                    ringIndex = math.random(1, AuraConfig.ringCount),
                    part = basePart
                }
                disableCollision(toy)
            end
        end
    end
end

function addToysToAura()
    for _, player in ipairs(Players:GetPlayers()) do
        local playerFolder = Workspace:FindFirstChild(player.Name .. "SpawnedInToys")
        if playerFolder then
            collectModelsInFolder(playerFolder)
        end

        local stFolder = Workspace:FindFirstChild("st")
        if stFolder then
            local stPlayerFolder = stFolder:FindFirstChild(player.Name .. "SpawnedInToys")
            if stPlayerFolder then
                collectModelsInFolder(stPlayerFolder)
            end
        end
    end

    local mapFolder = Workspace:FindFirstChild("map")
    if mapFolder then
        local foldersToCheck = {"Balloons", "Cave", "Cubes", "Draawers", "Farm", "Food", "Furniture", "Glass Boxes",
                                "Ladders", "Large Animals", "Music", "NOT New", "Other", "Pallets", "Robloxians",
                                "Rollers"}
        for _, folderName in ipairs(foldersToCheck) do
            local folder = mapFolder:FindFirstChild(folderName)
            if folder then
                collectModelsInFolder(folder)
            end
        end
    end
end

function clearEffects()
    for toy, _ in pairs(activeToys) do
        for _, child in ipairs(toy:GetDescendants()) do
            if child:IsA("BodyMover") or child:IsA("BodyGyro") then
                child:Destroy()
            end
        end
        enableCollision(toy)
    end
    activeToys = {}
end

function ringEffect()
    local totalToys = 0
    for _ in pairs(activeToys) do
        totalToys = totalToys + 1
    end
    if totalToys == 0 then
        return
    end

    local index = 0
    local centerPosition = Vector3.new(0, takasa, 0)

    for toy, data in pairs(activeToys) do
        local part = data.part
        if part then
            index = index + 1

            local baseAngleDeg = ((index - 1) / totalToys) * 360
            local angleRad = math.rad(baseAngleDeg + AuraConfig.orbitSpeed * (os.clock() % 360))
            local radius = AuraConfig.ringDistance

            local offsetPos
            if AuraConfig.rotationType == "縦" then
                offsetPos = Vector3.new(radius * math.cos(angleRad), 0, radius * math.sin(angleRad))
            elseif AuraConfig.rotationType == "横" then
                offsetPos = Vector3.new(0, radius * math.cos(angleRad), radius * math.sin(angleRad))
            elseif AuraConfig.rotationType == "前" then
                offsetPos = Vector3.new(radius * math.cos(angleRad), radius * math.sin(angleRad), 0)
            else
                offsetPos = Vector3.new(radius * math.cos(angleRad), 0, radius * math.sin(angleRad))
            end

            local pos = centerPosition + offsetPos

            local adjustRot = CFrame.Angles(math.rad(AuraConfig.rotationAngleX), math.rad(AuraConfig.rotationAngleY),
                math.rad(AuraConfig.rotationAngleZ))
            local finalCFrame = CFrame.new(pos) * adjustRot

            createBodyMovers(part, finalCFrame.Position, finalCFrame)
        end
    end
end

function combinedEffect()
    if AuraConfig.selectedEffect == "Rings" then
        ringEffect()
    end
end

function toggleEffect()
    if AuraConfig.isMoving then
        addToysToAura()
        if effectCoroutine then
            coroutine.close(effectCoroutine)
        end
        effectCoroutine = coroutine.create(function()
            while AuraConfig.isMoving do
                combinedEffect()
                task.wait(0.3)
            end
        end)
        coroutine.resume(effectCoroutine)

    else
        if effectCoroutine then
            coroutine.close(effectCoroutine)
            effectCoroutine = nil
        end
        clearEffects()
    end
end

AuraobjTab:AddToggle({
    Name = "リング",
    Default = false,
    Callback = function(state)
        AuraConfig.isMoving = state
        toggleEffect()
    end
})

AuraobjTab:AddDropdown({
    Name = "回転方向",
    Options = {"縦", "横", "前"},
    Default = "縦",
    Callback = function(selected)
        AuraConfig.rotationType = selected
    end
})

AuraobjTab:AddSlider({
    Name = "リングの範囲",
    Min = 5,
    Max = 150,
    Default = 50,
    Callback = function(value)
        AuraConfig.ringDistance = value
    end
})

AuraobjTab:AddSlider({
    Name = "リングの高さ",
    Min = 0,
    Max = 200,
    Default = 10,
    Callback = function(value)
        takasa = value
    end
})

AuraobjTab:AddSlider({
    Name = "回転スピード",
    Min = 10,
    Max = 1000,
    Default = 10,
    Rounding = 0,
    Callback = function(value)
        AuraConfig.orbitSpeed = value
    end
})


aaaa = 20
RunService = game:GetService("RunService")
local antiFi = nil
nigaall = false
local niga2 = nil
local niga3 = "OvenDarkGray"
localPlayer = game:GetService("Players").LocalPlayer
Players = game:GetService("Players")
Debris = game:GetService("Debris")

function findSpawnedObject(objectName)
    local folder = workspace:FindFirstChild(localPlayer.Name .. "SpawnedInToys")
    if folder then
        for _, item in pairs(folder:GetChildren()) do
            if item.Name == objectName then
                return item
            end
        end
    end
    return nil
end

function sp(objectName)
    task.spawn(function()
        local rotation = Vector3.new(0, 0, 0)
        local playerCharacter = localPlayer.Character
        if not playerCharacter or not playerCharacter:FindFirstChild("Head") then
            return
        end
        pcall(function()
            game:GetService("ReplicatedStorage").MenuToys.SpawnToyRemoteFunction:InvokeServer(objectName,
                playerCharacter.Head.CFrame, rotation)
        end)
    end)
end

function niga4(item)
    if item and item:IsA("Model") then
        for _, part in pairs(item:GetDescendants()) do
        end

        local PaintPlayerPart = item:FindFirstChild("PaintPlayerPart")
        if PaintPlayerPart then
            PaintPlayerPart.Size = Vector3.new(1500, 1500, 1500)
        end
    end
end

function ch()
    if nigaall and localPlayer.Character and localPlayer.Character.Parent == workspace then
        if not niga2 then
            sp(niga3)
            task.wait(0.001)
            niga2 = findSpawnedObject(niga3)
        end
        if niga2 then
            niga4(niga2)
        else
            sp(niga3)
        end
    end
end

RunService.Heartbeat:Connect(ch)

arasuTab:AddToggle({
    Name = "にがーおーる",
    Default = false,
    Save = true,
    Callback = function(enabled)
        nigaall = enabled
        if not enabled then
            niga2 = nil
        end

        if enabled then
            FCoroutine = coroutine.create(function()
                while true do
                    local success, err = pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local root = character.HumanoidRootPart

                            for _, player in pairs(Players:GetPlayers()) do
                                local toysFolder = workspace:FindFirstChild(player.Name .. "SpawnedInToys")
                                if toysFolder then
                                    for _, obj in pairs(toysFolder:GetChildren()) do
                                        coroutine.wrap(function()
                                            if obj:IsA("Model") and obj.Name == "OvenDarkGray" then
                                                local soundPart = obj:FindFirstChild("SoundPart")
                                                if soundPart and soundPart:IsA("BasePart") then
                                                    local dist = (soundPart.Position - root.Position).Magnitude
                                                    if dist <= aaaa then
                                                        SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
                                                        task.wait(1)
                                                        local velocity =
                                                            soundPart:FindFirstChild("l") or
                                                                Instance.new("BodyVelocity", soundPart)
                                                        velocity.Name = "l"
                                                        velocity.Velocity = Vector3.new(0, 0, 0)
                                                        velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                                        Debris:AddItem(velocity, 0)
                                                    end
                                                end
                                                local ButtonOven = obj:FindFirstChild("ButtonOven")
                                                if ButtonOven and ButtonOven:IsA("BasePart") then
                                                    local dist = (ButtonOven.Position - root.Position).Magnitude
                                                    if dist <= aaaa then
                                                        SetNetworkOwner:FireServer(ButtonOven, ButtonOven.CFrame)
                                                        task.wait(1)
                                                        local velocity =
                                                            ButtonOven:FindFirstChild("l") or
                                                                Instance.new("BodyVelocity", ButtonOven)
                                                        velocity.Name = "l"
                                                        velocity.Velocity = Vector3.new(0, 0, 0)
                                                        velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                                        Debris:AddItem(velocity, 0)
                                                    end
                                                end
                                            end
                                        end)()
                                    end
                                end
                            end
                        end
                    end)
                    wait(1)
                end
            end)
            coroutine.resume(FCoroutine)
        else
            if FCoroutine then
                coroutine.close(FCoroutine)
                FCoroutine = nil
            end
        end
    end
})

local mi2 = nil

function findSpawnedObject(objectName)
    local folder = workspace:FindFirstChild(localPlayer.Name .. "SpawnedInToys")
    if folder then
        for _, item in pairs(folder:GetChildren()) do
            if item.Name == objectName then
                return item
            end
        end
    end
    return nil
end

function sp(objectName)
    task.spawn(function()
        local rotation = Vector3.new(0, 0, 0)
        local playerCharacter = localPlayer.Character
        if not playerCharacter or not playerCharacter:FindFirstChild("Head") then
            return
        end
        pcall(function()
            game:GetService("ReplicatedStorage").MenuToys.SpawnToyRemoteFunction:InvokeServer(objectName,
                playerCharacter.Head.CFrame, rotation)
        end)
    end)
end

function mi4(item)
    if item and item:IsA("Model") then
        for _, part in pairs(item:GetDescendants()) do
        end

        local PaintPlayerPart = item:FindFirstChild("PaintPlayerPart")
        if PaintPlayerPart then
            PaintPlayerPart.Size = Vector3.new(1500, 1500, 1500)
        end
    end
end

function ch()
    if miall and localPlayer.Character and localPlayer.Character.Parent == workspace then
        if not mi2 then
            sp(mi3)
            task.wait(0.001)
            mi2 = findSpawnedObject("OvenMicrowaveWhite")
        end
        if mi2 then
            mi4(mi2)
        else
            sp(mi3)
        end
    end
end

RunService.Heartbeat:Connect(ch)

arasuTab:AddToggle({
    Name = "緑おーる",
    Default = false,
    Save = true,
    Callback = function(enabled)
        miall = enabled
        if not enabled then
            mi2 = nil
        end

        if enabled then
            FCoroutine = coroutine.create(function()
                while true do
                    local success, err = pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local root = character.HumanoidRootPart

                            for _, player in pairs(Players:GetPlayers()) do
                                local toysFolder = workspace:FindFirstChild(player.Name .. "SpawnedInToys")
                                if toysFolder then
                                    for _, obj in pairs(toysFolder:GetChildren()) do
                                        coroutine.wrap(function()
                                            if obj:IsA("Model") and obj.Name == "OvenMicrowaveWhite" then
                                                local soundPart = obj:FindFirstChild("SoundPart")
                                                if soundPart and soundPart:IsA("BasePart") then
                                                    local dist = (soundPart.Position - root.Position).Magnitude
                                                    if dist <= aaaa then
                                                        SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
                                                        task.wait(1)
                                                        local velocity =
                                                            soundPart:FindFirstChild("l") or
                                                                Instance.new("BodyVelocity", soundPart)
                                                        velocity.Name = "l"
                                                        velocity.Velocity = Vector3.new(0, 0, 0)
                                                        velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                                        Debris:AddItem(velocity, 0)
                                                    end
                                                end
                                                local Button = obj:FindFirstChild("Button")
                                                if Button and Button:IsA("BasePart") then
                                                    local dist = (Button.Position - root.Position).Magnitude
                                                    if dist <= aaaa then
                                                        SetNetworkOwner:FireServer(Button, Button.CFrame)
                                                        task.wait(1)
                                                        local velocity = Button:FindFirstChild("l") or
                                                                             Instance.new("BodyVelocity", Button)
                                                        velocity.Name = "l"
                                                        velocity.Velocity = Vector3.new(0, 0, 0)
                                                        velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                                        Debris:AddItem(velocity, 0)
                                                    end
                                                end
                                            end
                                        end)()
                                    end
                                end
                            end
                        end
                    end)
                    wait(1)
                end
            end)
            coroutine.resume(FCoroutine)
        else
            if FCoroutine then
                coroutine.close(FCoroutine)
                FCoroutine = nil
            end
        end
    end
})

OrionLib:Init()
