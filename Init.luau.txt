print[[

██╗███╗░░██╗██╗████████╗
██║████╗░██║██║╚══██╔══╝
██║██╔██╗██║██║░░░██║░░░
██║██║╚████║██║░░░██║░░░
██║██║░╚███║██║░░░██║░░░
╚═╝╚═╝░░╚══╝╚═╝░░░╚═╝░░░

]]
task.spawn(pcall, function()
    if SPY_LOADED == true then return end
    pcall(function() getgenv().SPY_LOADED = true end)
    -- // Initialise
    --if (getgenv().ChatSpy) then return getgenv().ChatSpy; end;
    repeat wait() until game:GetService("ContentProvider").RequestQueueSize == 0
    repeat wait() until game:IsLoaded()

    -- // Vars
    local Players = game:GetService("Players")
    local StarterGui = game:GetService("StarterGui")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local LocalPlayer = Players.LocalPlayer
    local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
    local TextChatService, General = game:GetService("TextChatService")
    for _,v in pairs(TextChatService:GetChildren()) do
        if v.Name == "TextChannels" and v:FindFirstChild("RBXGeneral") then
            General = v.RBXGeneral
        end
    end
    getgenv().ChatSpy = {
        Enabled = true,
        EnabledTF = "",
        SpyOnSelf = false,
        Public = false,
        Count = 0,
        OnMsg = false,
        Chat = {
            Color  = Color3.fromRGB(0, 255, 255),
            Font = Enum.Font.SourceSansBold,
            TextSize = 18,
            Text = "",
        },
        IgnoreList = {
            {Message = ":part/1/1/1", ExactMatch = true},
            {Message = ":part/10/10/10", ExactMatch = true},
            {Message = "A?????????", ExactMatch = false},
            {Message = ":colorshifttop 10000 0 0", ExactMatch = true},
            {Message = ":colorshiftbottom 10000 0 0", ExactMatch = true},
            {Message = ":colorshifttop 0 10000 0", ExactMatch = true},
            {Message = ":colorshiftbottom 0 10000 0", ExactMatch = true},
            {Message = ":colorshifttop 0 0 10000", ExactMatch = true},
            {Message = ":colorshiftbottom 0 0 10000", ExactMatch = true},
        },
    };

    -- // Function
    function ChatSpy.checkIgnored(message)
        for i = 1, #ChatSpy.IgnoreList do
            local v = ChatSpy.IgnoreList[i]
            if (v.ExactMatch and message == v.Message) or (not v.ExactMatch and string.match(v.Message, message)) then 
                return true
            end
        end
        return false
    end

    function ChatSpy.onChatted(targetPlayer, message)
        if (targetPlayer == LocalPlayer and string.lower(message) == "/spy") then
        ChatSpy.Count = ChatSpy.Count + 1
        if ChatSpy.Count < 2 then
            ChatSpy.Enabled = not(ChatSpy.Enabled)
            ChatSpy.EnabledTF = ChatSpy.Enabled and "Enabled." or "Disabled."
            ChatSpy.Chat.Text = `<font color='#10e3df'>{"{SPY} - "..ChatSpy.EnabledTF}</font>`
            task.wait(0.55)
            General:DisplaySystemMessage(ChatSpy.Chat.Text)
        else
            ChatSpy.Count = 0
        end
        elseif (ChatSpy.Enabled and (ChatSpy.SpyOnSelf or targetPlayer ~= LocalPlayer)) then
            local message = message:gsub("[\n\r]",''):gsub("\t",' '):gsub("[ ]+",' ')

            local Hidden = true
            local Connect = TextChatService.MessageReceived:Connect(function()
                Hidden = false
            end)

            task.wait(0.75)
            Connect:Disconnect()
            Connect = nil

            if (Hidden and ChatSpy.Enabled and not ChatSpy.checkIgnored(message)) then
                if (#message > 1200) then
                    message = message:sub(1200) .. "..."
                end
                if message:sub(1,2) == "/w" then
                    for _,plr in pairs(Players:GetPlayers()) do
                        local msg, count = message:gsub(plr.Name,plr.DisplayName)
                        if count ~= 0 then
                            ChatSpy.Chat.Text = `<font color='#10e3df'>{"{SPY} ["..targetPlayer.DisplayName.."]: "..msg}</font>`
                            break
                        end
                    end
                else
                    ChatSpy.Chat.Text = `<font color='#10e3df'>{"{SPY} ["..targetPlayer.DisplayName.."]: "..message}</font>`
                end
                General:DisplaySystemMessage(ChatSpy.Chat.Text)
            end
        end
    end

    -- // Handling Chats
    local AllPlayers = Players:GetPlayers()
    for i = 1, #AllPlayers do
        local player = AllPlayers[i]
        player.Chatted:Connect(function(message)
            ChatSpy.onChatted(player, message)
        end)
    end

    Players.PlayerAdded:Connect(function(player)
        player.Chatted:Connect(function(message)
            ChatSpy.onChatted(player, message)
        end)
    end)

    -- // Initialise Text
    ChatSpy.EnabledTF = ChatSpy.Enabled and "Enabled." or "Disabled."
    ChatSpy.Chat.Text = `<font color='#10e3df'>{"{SPY} - "..ChatSpy.EnabledTF}</font>`
    General:DisplaySystemMessage(ChatSpy.Chat.Text)
end)
local Rayfield = (function()
    if debugX then
        warn('Initialising Rayfield')
    end

    local function getService(name)
        local service = game:GetService(name)
        return if cloneref then cloneref(service) else service
    end

    local function loadWithTimeout(url: string, timeout: number?): ...any
        assert(type(url) == "string", "Expected string, got " .. type(url))
        timeout = timeout or 5
        local requestCompleted = false
        local success, result = false, nil

        local requestThread = task.spawn(function()
            local fetchSuccess, fetchResult = pcall(game.HttpGet, game, url)

            if not fetchSuccess or #fetchResult == 0 then
                if #fetchResult == 0 then
                    fetchResult = "Empty response" 
                end
                success, result = false, fetchResult
                requestCompleted = true
                return
            end
            local content = fetchResult -- Fetched content
            local execSuccess, execResult = pcall(function()
                return loadstring(content)()
            end)
            success, result = execSuccess, execResult
            requestCompleted = true
        end)

        local timeoutThread = task.delay(timeout, function()
            if not requestCompleted then
                warn(`Request for {url} timed out after {timeout} seconds`)
                task.cancel(requestThread)
                result = "Request timed out"
                requestCompleted = true
            end
        end)

        -- Wait for completion or timeout
        while not requestCompleted do
            task.wait()
        end
        -- Cancel timeout thread if still running when request completes
        if coroutine.status(timeoutThread) ~= "dead" then
            task.cancel(timeoutThread)
        end
        if not success then
            warn(`Failed to process {url}: {result}`)
        end
        return if success then result else nil
    end

    local requestsDisabled = true --getgenv and getgenv().DISABLE_RAYFIELD_REQUESTS
    local InterfaceBuild = '3K3W'
    local Release = "Build 1.68"
    local RayfieldFolder = "Rayfield"
    local ConfigurationFolder = RayfieldFolder.."/Configurations"
    local ConfigurationExtension = ".rfld"
    local settingsTable = {
        General = {
            rayfieldOpen = {Type = 'bind', Value = 'N', Name = 'Rayfield Keybind'},

        },
        System = {
            usageAnalytics = {Type = 'toggle', Value = true, Name = 'Anonymised Analytics'},
        }
    }

    local overriddenSettings: { [string]: any } = {} -- For example, overriddenSettings["System.rayfieldOpen"] = "J"
    local function overrideSetting(category: string, name: string, value: any)
        overriddenSettings[`{category}.{name}`] = value
    end

    local function getSetting(category: string, name: string): any
        if overriddenSettings[`{category}.{name}`] ~= nil then
            return overriddenSettings[`{category}.{name}`]
        elseif settingsTable[category][name] ~= nil then
            return settingsTable[category][name].Value
        end
    end

    if requestsDisabled then
        overrideSetting("System", "usageAnalytics", false)
    end

    local HttpService = getService('HttpService')
    local RunService = getService('RunService')

    -- Environment Check
    local useStudio = RunService:IsStudio() or false

    local settingsCreated = false
    local settingsInitialized = false -- Whether the UI elements in the settings page have been set to the proper values
    local cachedSettings
    local prompt = useStudio and require(script.Parent.prompt) or loadWithTimeout('https://raw.githubusercontent.com/SiriusSoftwareLtd/Sirius/refs/heads/request/prompt.lua')
    local requestFunc = (syn and syn.request) or (fluxus and fluxus.request) or (http and http.request) or http_request or request

    if not prompt and not useStudio then
        warn("Failed to load prompt library, using fallback")
        prompt = {
            create = function() end -- No-op fallback
        }
    end



    local function loadSettings()
        local file = nil

        local success, result =	pcall(function()
            task.spawn(function()
                if isfolder and isfolder(RayfieldFolder) then
                    if isfile and isfile(RayfieldFolder..'/settings'..ConfigurationExtension) then
                        file = readfile(RayfieldFolder..'/settings'..ConfigurationExtension)
                    end
                end

                if useStudio then
                    file = [[
            {"General":{"rayfieldOpen":{"Value":"K","Type":"bind","Name":"Rayfield Keybind","Element":{"HoldToInteract":false,"Ext":true,"Name":"Rayfield Keybind","Set":null,"CallOnChange":true,"Callback":null,"CurrentKeybind":"K"}}},"System":{"usageAnalytics":{"Value":false,"Type":"toggle","Name":"Anonymised Analytics","Element":{"Ext":true,"Name":"Anonymised Analytics","Set":null,"CurrentValue":false,"Callback":null}}}}
        ]]
                end


                if file then
                    local success, decodedFile = pcall(function() return HttpService:JSONDecode(file) end)
                    if success then
                        file = decodedFile
                    else
                        file = {}
                    end
                else
                    file = {}
                end


                if not settingsCreated then 
                    cachedSettings = file
                    return
                end

                if file ~= {} then
                    for categoryName, settingCategory in pairs(settingsTable) do
                        if file[categoryName] then
                            for settingName, setting in pairs(settingCategory) do
                                if file[categoryName][settingName] then
                                    setting.Value = file[categoryName][settingName].Value
                                    setting.Element:Set(getSetting(categoryName, settingName))
                                end
                            end
                        end
                    end
                end
                settingsInitialized = true
            end)
        end)

        if not success then 
            if writefile then
                warn('Rayfield had an issue accessing configuration saving capability.')
            end
        end
    end

    if debugX then
        warn('Now Loading Settings Configuration')
    end

    loadSettings()

    if debugX then
        warn('Settings Loaded')
    end

    local analyticsLib
    local sendReport = function(ev_n, sc_n) warn("Failed to load report function") end
    if not requestsDisabled then
        if debugX then
            warn('Querying Settings for Reporter Information')
        end	
        analyticsLib = loadWithTimeout("https://analytics.sirius.menu/script")
        if not analyticsLib then
            warn("Failed to load analytics reporter")
            analyticsLib = nil
        elseif analyticsLib and type(analyticsLib.load) == "function" then
            analyticsLib:load()
        else
            warn("Analytics library loaded but missing load function")
            analyticsLib = nil
        end
        sendReport = function(ev_n, sc_n)
            if not (type(analyticsLib) == "table" and type(analyticsLib.isLoaded) == "function" and analyticsLib:isLoaded()) then
                warn("Analytics library not loaded")
                return
            end
            if useStudio then
                print('Sending Analytics')
            else
                if debugX then warn('Reporting Analytics') end
                analyticsLib:report(
                    {
                        ["name"] = ev_n,
                        ["script"] = {["name"] = sc_n, ["version"] = Release}
                    },
                    {
                        ["version"] = InterfaceBuild
                    }
                )
                if debugX then warn('Finished Report') end
            end
        end
        if cachedSettings and (#cachedSettings == 0 or (cachedSettings.System and cachedSettings.System.usageAnalytics and cachedSettings.System.usageAnalytics.Value)) then
            sendReport("execution", "Rayfield")
        elseif not cachedSettings then
            sendReport("execution", "Rayfield")
        end
    end

    local promptUser = 2

    if promptUser == 1 and prompt and type(prompt.create) == "function" then
        prompt.create(
            'Be cautious when running scripts',
            [[Please be careful when running scripts from unknown developers. This script has already been ran.

    <font transparency='0.3'>Some scripts may steal your items or in-game goods.</font>]],
            'Okay',
            '',
            function()

            end
        )
    end

    if debugX then
        warn('Moving on to continue initialisation')
    end

    local RayfieldLibrary = {
        Flags = {},
        Theme = {
            Default = {
                TextColor = Color3.fromRGB(240, 240, 240),

                Background = Color3.fromRGB(25, 25, 25),
                Topbar = Color3.fromRGB(34, 34, 34),
                Shadow = Color3.fromRGB(20, 20, 20),

                NotificationBackground = Color3.fromRGB(20, 20, 20),
                NotificationActionsBackground = Color3.fromRGB(230, 230, 230),

                TabBackground = Color3.fromRGB(80, 80, 80),
                TabStroke = Color3.fromRGB(85, 85, 85),
                TabBackgroundSelected = Color3.fromRGB(210, 210, 210),
                TabTextColor = Color3.fromRGB(240, 240, 240),
                SelectedTabTextColor = Color3.fromRGB(50, 50, 50),

                ElementBackground = Color3.fromRGB(35, 35, 35),
                ElementBackgroundHover = Color3.fromRGB(40, 40, 40),
                SecondaryElementBackground = Color3.fromRGB(25, 25, 25),
                ElementStroke = Color3.fromRGB(50, 50, 50),
                SecondaryElementStroke = Color3.fromRGB(40, 40, 40),

                SliderBackground = Color3.fromRGB(50, 138, 220),
                SliderProgress = Color3.fromRGB(50, 138, 220),
                SliderStroke = Color3.fromRGB(58, 163, 255),

                ToggleBackground = Color3.fromRGB(30, 30, 30),
                ToggleEnabled = Color3.fromRGB(0, 146, 214),
                ToggleDisabled = Color3.fromRGB(100, 100, 100),
                ToggleEnabledStroke = Color3.fromRGB(0, 170, 255),
                ToggleDisabledStroke = Color3.fromRGB(125, 125, 125),
                ToggleEnabledOuterStroke = Color3.fromRGB(100, 100, 100),
                ToggleDisabledOuterStroke = Color3.fromRGB(65, 65, 65),

                DropdownSelected = Color3.fromRGB(40, 40, 40),
                DropdownUnselected = Color3.fromRGB(30, 30, 30),

                InputBackground = Color3.fromRGB(30, 30, 30),
                InputStroke = Color3.fromRGB(65, 65, 65),
                PlaceholderColor = Color3.fromRGB(178, 178, 178)
            },

            Ocean = {
                TextColor = Color3.fromRGB(230, 240, 240),

                Background = Color3.fromRGB(20, 30, 30),
                Topbar = Color3.fromRGB(25, 40, 40),
                Shadow = Color3.fromRGB(15, 20, 20),

                NotificationBackground = Color3.fromRGB(25, 35, 35),
                NotificationActionsBackground = Color3.fromRGB(230, 240, 240),

                TabBackground = Color3.fromRGB(40, 60, 60),
                TabStroke = Color3.fromRGB(50, 70, 70),
                TabBackgroundSelected = Color3.fromRGB(100, 180, 180),
                TabTextColor = Color3.fromRGB(210, 230, 230),
                SelectedTabTextColor = Color3.fromRGB(20, 50, 50),

                ElementBackground = Color3.fromRGB(30, 50, 50),
                ElementBackgroundHover = Color3.fromRGB(40, 60, 60),
                SecondaryElementBackground = Color3.fromRGB(30, 45, 45),
                ElementStroke = Color3.fromRGB(45, 70, 70),
                SecondaryElementStroke = Color3.fromRGB(40, 65, 65),

                SliderBackground = Color3.fromRGB(0, 110, 110),
                SliderProgress = Color3.fromRGB(0, 140, 140),
                SliderStroke = Color3.fromRGB(0, 160, 160),

                ToggleBackground = Color3.fromRGB(30, 50, 50),
                ToggleEnabled = Color3.fromRGB(0, 130, 130),
                ToggleDisabled = Color3.fromRGB(70, 90, 90),
                ToggleEnabledStroke = Color3.fromRGB(0, 160, 160),
                ToggleDisabledStroke = Color3.fromRGB(85, 105, 105),
                ToggleEnabledOuterStroke = Color3.fromRGB(50, 100, 100),
                ToggleDisabledOuterStroke = Color3.fromRGB(45, 65, 65),

                DropdownSelected = Color3.fromRGB(30, 60, 60),
                DropdownUnselected = Color3.fromRGB(25, 40, 40),

                InputBackground = Color3.fromRGB(30, 50, 50),
                InputStroke = Color3.fromRGB(50, 70, 70),
                PlaceholderColor = Color3.fromRGB(140, 160, 160)
            },

            AmberGlow = {
                TextColor = Color3.fromRGB(255, 245, 230),

                Background = Color3.fromRGB(45, 30, 20),
                Topbar = Color3.fromRGB(55, 40, 25),
                Shadow = Color3.fromRGB(35, 25, 15),

                NotificationBackground = Color3.fromRGB(50, 35, 25),
                NotificationActionsBackground = Color3.fromRGB(245, 230, 215),

                TabBackground = Color3.fromRGB(75, 50, 35),
                TabStroke = Color3.fromRGB(90, 60, 45),
                TabBackgroundSelected = Color3.fromRGB(230, 180, 100),
                TabTextColor = Color3.fromRGB(250, 220, 200),
                SelectedTabTextColor = Color3.fromRGB(50, 30, 10),

                ElementBackground = Color3.fromRGB(60, 45, 35),
                ElementBackgroundHover = Color3.fromRGB(70, 50, 40),
                SecondaryElementBackground = Color3.fromRGB(55, 40, 30),
                ElementStroke = Color3.fromRGB(85, 60, 45),
                SecondaryElementStroke = Color3.fromRGB(75, 50, 35),

                SliderBackground = Color3.fromRGB(220, 130, 60),
                SliderProgress = Color3.fromRGB(250, 150, 75),
                SliderStroke = Color3.fromRGB(255, 170, 85),

                ToggleBackground = Color3.fromRGB(55, 40, 30),
                ToggleEnabled = Color3.fromRGB(240, 130, 30),
                ToggleDisabled = Color3.fromRGB(90, 70, 60),
                ToggleEnabledStroke = Color3.fromRGB(255, 160, 50),
                ToggleDisabledStroke = Color3.fromRGB(110, 85, 75),
                ToggleEnabledOuterStroke = Color3.fromRGB(200, 100, 50),
                ToggleDisabledOuterStroke = Color3.fromRGB(75, 60, 55),

                DropdownSelected = Color3.fromRGB(70, 50, 40),
                DropdownUnselected = Color3.fromRGB(55, 40, 30),

                InputBackground = Color3.fromRGB(60, 45, 35),
                InputStroke = Color3.fromRGB(90, 65, 50),
                PlaceholderColor = Color3.fromRGB(190, 150, 130)
            },

            Light = {
                TextColor = Color3.fromRGB(40, 40, 40),

                Background = Color3.fromRGB(245, 245, 245),
                Topbar = Color3.fromRGB(230, 230, 230),
                Shadow = Color3.fromRGB(200, 200, 200),

                NotificationBackground = Color3.fromRGB(250, 250, 250),
                NotificationActionsBackground = Color3.fromRGB(240, 240, 240),

                TabBackground = Color3.fromRGB(235, 235, 235),
                TabStroke = Color3.fromRGB(215, 215, 215),
                TabBackgroundSelected = Color3.fromRGB(255, 255, 255),
                TabTextColor = Color3.fromRGB(80, 80, 80),
                SelectedTabTextColor = Color3.fromRGB(0, 0, 0),

                ElementBackground = Color3.fromRGB(240, 240, 240),
                ElementBackgroundHover = Color3.fromRGB(225, 225, 225),
                SecondaryElementBackground = Color3.fromRGB(235, 235, 235),
                ElementStroke = Color3.fromRGB(210, 210, 210),
                SecondaryElementStroke = Color3.fromRGB(210, 210, 210),

                SliderBackground = Color3.fromRGB(150, 180, 220),
                SliderProgress = Color3.fromRGB(100, 150, 200), 
                SliderStroke = Color3.fromRGB(120, 170, 220),

                ToggleBackground = Color3.fromRGB(220, 220, 220),
                ToggleEnabled = Color3.fromRGB(0, 146, 214),
                ToggleDisabled = Color3.fromRGB(150, 150, 150),
                ToggleEnabledStroke = Color3.fromRGB(0, 170, 255),
                ToggleDisabledStroke = Color3.fromRGB(170, 170, 170),
                ToggleEnabledOuterStroke = Color3.fromRGB(100, 100, 100),
                ToggleDisabledOuterStroke = Color3.fromRGB(180, 180, 180),

                DropdownSelected = Color3.fromRGB(230, 230, 230),
                DropdownUnselected = Color3.fromRGB(220, 220, 220),

                InputBackground = Color3.fromRGB(240, 240, 240),
                InputStroke = Color3.fromRGB(180, 180, 180),
                PlaceholderColor = Color3.fromRGB(140, 140, 140)
            },

            Amethyst = {
                TextColor = Color3.fromRGB(240, 240, 240),

                Background = Color3.fromRGB(30, 20, 40),
                Topbar = Color3.fromRGB(40, 25, 50),
                Shadow = Color3.fromRGB(20, 15, 30),

                NotificationBackground = Color3.fromRGB(35, 20, 40),
                NotificationActionsBackground = Color3.fromRGB(240, 240, 250),

                TabBackground = Color3.fromRGB(60, 40, 80),
                TabStroke = Color3.fromRGB(70, 45, 90),
                TabBackgroundSelected = Color3.fromRGB(180, 140, 200),
                TabTextColor = Color3.fromRGB(230, 230, 240),
                SelectedTabTextColor = Color3.fromRGB(50, 20, 50),

                ElementBackground = Color3.fromRGB(45, 30, 60),
                ElementBackgroundHover = Color3.fromRGB(50, 35, 70),
                SecondaryElementBackground = Color3.fromRGB(40, 30, 55),
                ElementStroke = Color3.fromRGB(70, 50, 85),
                SecondaryElementStroke = Color3.fromRGB(65, 45, 80),

                SliderBackground = Color3.fromRGB(100, 60, 150),
                SliderProgress = Color3.fromRGB(130, 80, 180),
                SliderStroke = Color3.fromRGB(150, 100, 200),

                ToggleBackground = Color3.fromRGB(45, 30, 55),
                ToggleEnabled = Color3.fromRGB(120, 60, 150),
                ToggleDisabled = Color3.fromRGB(94, 47, 117),
                ToggleEnabledStroke = Color3.fromRGB(140, 80, 170),
                ToggleDisabledStroke = Color3.fromRGB(124, 71, 150),
                ToggleEnabledOuterStroke = Color3.fromRGB(90, 40, 120),
                ToggleDisabledOuterStroke = Color3.fromRGB(80, 50, 110),

                DropdownSelected = Color3.fromRGB(50, 35, 70),
                DropdownUnselected = Color3.fromRGB(35, 25, 50),

                InputBackground = Color3.fromRGB(45, 30, 60),
                InputStroke = Color3.fromRGB(80, 50, 110),
                PlaceholderColor = Color3.fromRGB(178, 150, 200)
            },

            Green = {
                TextColor = Color3.fromRGB(30, 60, 30),

                Background = Color3.fromRGB(235, 245, 235),
                Topbar = Color3.fromRGB(210, 230, 210),
                Shadow = Color3.fromRGB(200, 220, 200),

                NotificationBackground = Color3.fromRGB(240, 250, 240),
                NotificationActionsBackground = Color3.fromRGB(220, 235, 220),

                TabBackground = Color3.fromRGB(215, 235, 215),
                TabStroke = Color3.fromRGB(190, 210, 190),
                TabBackgroundSelected = Color3.fromRGB(245, 255, 245),
                TabTextColor = Color3.fromRGB(50, 80, 50),
                SelectedTabTextColor = Color3.fromRGB(20, 60, 20),

                ElementBackground = Color3.fromRGB(225, 240, 225),
                ElementBackgroundHover = Color3.fromRGB(210, 225, 210),
                SecondaryElementBackground = Color3.fromRGB(235, 245, 235), 
                ElementStroke = Color3.fromRGB(180, 200, 180),
                SecondaryElementStroke = Color3.fromRGB(180, 200, 180),

                SliderBackground = Color3.fromRGB(90, 160, 90),
                SliderProgress = Color3.fromRGB(70, 130, 70),
                SliderStroke = Color3.fromRGB(100, 180, 100),

                ToggleBackground = Color3.fromRGB(215, 235, 215),
                ToggleEnabled = Color3.fromRGB(60, 130, 60),
                ToggleDisabled = Color3.fromRGB(150, 175, 150),
                ToggleEnabledStroke = Color3.fromRGB(80, 150, 80),
                ToggleDisabledStroke = Color3.fromRGB(130, 150, 130),
                ToggleEnabledOuterStroke = Color3.fromRGB(100, 160, 100),
                ToggleDisabledOuterStroke = Color3.fromRGB(160, 180, 160),

                DropdownSelected = Color3.fromRGB(225, 240, 225),
                DropdownUnselected = Color3.fromRGB(210, 225, 210),

                InputBackground = Color3.fromRGB(235, 245, 235),
                InputStroke = Color3.fromRGB(180, 200, 180),
                PlaceholderColor = Color3.fromRGB(120, 140, 120)
            },

            Bloom = {
                TextColor = Color3.fromRGB(60, 40, 50),

                Background = Color3.fromRGB(255, 240, 245),
                Topbar = Color3.fromRGB(250, 220, 225),
                Shadow = Color3.fromRGB(230, 190, 195),

                NotificationBackground = Color3.fromRGB(255, 235, 240),
                NotificationActionsBackground = Color3.fromRGB(245, 215, 225),

                TabBackground = Color3.fromRGB(240, 210, 220),
                TabStroke = Color3.fromRGB(230, 200, 210),
                TabBackgroundSelected = Color3.fromRGB(255, 225, 235),
                TabTextColor = Color3.fromRGB(80, 40, 60),
                SelectedTabTextColor = Color3.fromRGB(50, 30, 50),

                ElementBackground = Color3.fromRGB(255, 235, 240),
                ElementBackgroundHover = Color3.fromRGB(245, 220, 230),
                SecondaryElementBackground = Color3.fromRGB(255, 235, 240), 
                ElementStroke = Color3.fromRGB(230, 200, 210),
                SecondaryElementStroke = Color3.fromRGB(230, 200, 210),

                SliderBackground = Color3.fromRGB(240, 130, 160),
                SliderProgress = Color3.fromRGB(250, 160, 180),
                SliderStroke = Color3.fromRGB(255, 180, 200),

                ToggleBackground = Color3.fromRGB(240, 210, 220),
                ToggleEnabled = Color3.fromRGB(255, 140, 170),
                ToggleDisabled = Color3.fromRGB(200, 180, 185),
                ToggleEnabledStroke = Color3.fromRGB(250, 160, 190),
                ToggleDisabledStroke = Color3.fromRGB(210, 180, 190),
                ToggleEnabledOuterStroke = Color3.fromRGB(220, 160, 180),
                ToggleDisabledOuterStroke = Color3.fromRGB(190, 170, 180),

                DropdownSelected = Color3.fromRGB(250, 220, 225),
                DropdownUnselected = Color3.fromRGB(240, 210, 220),

                InputBackground = Color3.fromRGB(255, 235, 240),
                InputStroke = Color3.fromRGB(220, 190, 200),
                PlaceholderColor = Color3.fromRGB(170, 130, 140)
            },

            DarkBlue = {
                TextColor = Color3.fromRGB(230, 230, 230),

                Background = Color3.fromRGB(20, 25, 30),
                Topbar = Color3.fromRGB(30, 35, 40),
                Shadow = Color3.fromRGB(15, 20, 25),

                NotificationBackground = Color3.fromRGB(25, 30, 35),
                NotificationActionsBackground = Color3.fromRGB(45, 50, 55),

                TabBackground = Color3.fromRGB(35, 40, 45),
                TabStroke = Color3.fromRGB(45, 50, 60),
                TabBackgroundSelected = Color3.fromRGB(40, 70, 100),
                TabTextColor = Color3.fromRGB(200, 200, 200),
                SelectedTabTextColor = Color3.fromRGB(255, 255, 255),

                ElementBackground = Color3.fromRGB(30, 35, 40),
                ElementBackgroundHover = Color3.fromRGB(40, 45, 50),
                SecondaryElementBackground = Color3.fromRGB(35, 40, 45), 
                ElementStroke = Color3.fromRGB(45, 50, 60),
                SecondaryElementStroke = Color3.fromRGB(40, 45, 55),

                SliderBackground = Color3.fromRGB(0, 90, 180),
                SliderProgress = Color3.fromRGB(0, 120, 210),
                SliderStroke = Color3.fromRGB(0, 150, 240),

                ToggleBackground = Color3.fromRGB(35, 40, 45),
                ToggleEnabled = Color3.fromRGB(0, 120, 210),
                ToggleDisabled = Color3.fromRGB(70, 70, 80),
                ToggleEnabledStroke = Color3.fromRGB(0, 150, 240),
                ToggleDisabledStroke = Color3.fromRGB(75, 75, 85),
                ToggleEnabledOuterStroke = Color3.fromRGB(20, 100, 180), 
                ToggleDisabledOuterStroke = Color3.fromRGB(55, 55, 65),

                DropdownSelected = Color3.fromRGB(30, 70, 90),
                DropdownUnselected = Color3.fromRGB(25, 30, 35),

                InputBackground = Color3.fromRGB(25, 30, 35),
                InputStroke = Color3.fromRGB(45, 50, 60), 
                PlaceholderColor = Color3.fromRGB(150, 150, 160)
            },

            Serenity = {
                TextColor = Color3.fromRGB(50, 55, 60),
                Background = Color3.fromRGB(240, 245, 250),
                Topbar = Color3.fromRGB(215, 225, 235),
                Shadow = Color3.fromRGB(200, 210, 220),

                NotificationBackground = Color3.fromRGB(210, 220, 230),
                NotificationActionsBackground = Color3.fromRGB(225, 230, 240),

                TabBackground = Color3.fromRGB(200, 210, 220),
                TabStroke = Color3.fromRGB(180, 190, 200),
                TabBackgroundSelected = Color3.fromRGB(175, 185, 200),
                TabTextColor = Color3.fromRGB(50, 55, 60),
                SelectedTabTextColor = Color3.fromRGB(30, 35, 40),

                ElementBackground = Color3.fromRGB(210, 220, 230),
                ElementBackgroundHover = Color3.fromRGB(220, 230, 240),
                SecondaryElementBackground = Color3.fromRGB(200, 210, 220),
                ElementStroke = Color3.fromRGB(190, 200, 210),
                SecondaryElementStroke = Color3.fromRGB(180, 190, 200),

                SliderBackground = Color3.fromRGB(200, 220, 235),  -- Lighter shade
                SliderProgress = Color3.fromRGB(70, 130, 180),
                SliderStroke = Color3.fromRGB(150, 180, 220),

                ToggleBackground = Color3.fromRGB(210, 220, 230),
                ToggleEnabled = Color3.fromRGB(70, 160, 210),
                ToggleDisabled = Color3.fromRGB(180, 180, 180),
                ToggleEnabledStroke = Color3.fromRGB(60, 150, 200),
                ToggleDisabledStroke = Color3.fromRGB(140, 140, 140),
                ToggleEnabledOuterStroke = Color3.fromRGB(100, 120, 140),
                ToggleDisabledOuterStroke = Color3.fromRGB(120, 120, 130),

                DropdownSelected = Color3.fromRGB(220, 230, 240),
                DropdownUnselected = Color3.fromRGB(200, 210, 220),

                InputBackground = Color3.fromRGB(220, 230, 240),
                InputStroke = Color3.fromRGB(180, 190, 200),
                PlaceholderColor = Color3.fromRGB(150, 150, 150)
            },
        }
    }


    -- Services
    local UserInputService = getService("UserInputService")
    local TweenService = getService("TweenService")
    local Players = getService("Players")
    local CoreGui = getService("CoreGui")

    -- Interface Management

    local Rayfield = useStudio and script.Parent:FindFirstChild('Rayfield') or game:GetObjects("rbxassetid://10804731440")[1]
    local buildAttempts = 0
    local correctBuild = false
    local warned
    local globalLoaded
    local rayfieldDestroyed = false -- True when RayfieldLibrary:Destroy() is called

    repeat
        if Rayfield:FindFirstChild('Build') and Rayfield.Build.Value == InterfaceBuild then
            correctBuild = true
            break
        end

        correctBuild = false

        if not warned then
            warn('Rayfield | Build Mismatch')
            print('Rayfield may encounter issues as you are running an incompatible interface version ('.. ((Rayfield:FindFirstChild('Build') and Rayfield.Build.Value) or 'No Build') ..').\n\nThis version of Rayfield is intended for interface build '..InterfaceBuild..'.')
            warned = true
        end

        toDestroy, Rayfield = Rayfield, useStudio and script.Parent:FindFirstChild('Rayfield') or game:GetObjects("rbxassetid://10804731440")[1]
        if toDestroy and not useStudio then toDestroy:Destroy() end

        buildAttempts = buildAttempts + 1
    until buildAttempts >= 2

    Rayfield.Enabled = false

    if gethui then
        Rayfield.Parent = gethui()
    elseif syn and syn.protect_gui then 
        syn.protect_gui(Rayfield)
        Rayfield.Parent = CoreGui
    elseif not useStudio and CoreGui:FindFirstChild("RobloxGui") then
        Rayfield.Parent = CoreGui:FindFirstChild("RobloxGui")
    elseif not useStudio then
        Rayfield.Parent = CoreGui
    end

    if gethui then
        for _, Interface in ipairs(gethui():GetChildren()) do
            if Interface.Name == Rayfield.Name and Interface ~= Rayfield then
                Interface.Enabled = false
                Interface.Name = "Rayfield-Old"
            end
        end
    elseif not useStudio then
        for _, Interface in ipairs(CoreGui:GetChildren()) do
            if Interface.Name == Rayfield.Name and Interface ~= Rayfield then
                Interface.Enabled = false
                Interface.Name = "Rayfield-Old"
            end
        end
    end


    local minSize = Vector2.new(1024, 768)
    local useMobileSizing

    if Rayfield.AbsoluteSize.X < minSize.X and Rayfield.AbsoluteSize.Y < minSize.Y then
        useMobileSizing = true
    end

    if UserInputService.TouchEnabled then
        useMobilePrompt = true
    end


    -- Object Variables

    local Main = Rayfield.Main
    local MPrompt = Rayfield:FindFirstChild('Prompt')
    local Topbar = Main.Topbar
    local Elements = Main.Elements
    local LoadingFrame = Main.LoadingFrame
    local TabList = Main.TabList
    local dragBar = Rayfield:FindFirstChild('Drag')
    local dragInteract = dragBar and dragBar.Interact or nil
    local dragBarCosmetic = dragBar and dragBar.Drag or nil

    local dragOffset = 255
    local dragOffsetMobile = 150

    Rayfield.DisplayOrder = 100
    LoadingFrame.Version.Text = Release

    -- Thanks to Latte Softworks for the Lucide integration for Roblox
    local Icons = useStudio and require(script.Parent.icons) or loadWithTimeout('https://raw.githubusercontent.com/SiriusSoftwareLtd/Rayfield/refs/heads/main/icons.lua')
    -- Variables

    local CFileName = nil
    local CEnabled = false
    local Minimised = false
    local Hidden = false
    local Debounce = false
    local searchOpen = false
    local Notifications = Rayfield.Notifications

    local SelectedTheme = RayfieldLibrary.Theme.Default

    local function ChangeTheme(Theme)
        if typeof(Theme) == 'string' then
            SelectedTheme = RayfieldLibrary.Theme[Theme]
        elseif typeof(Theme) == 'table' then
            SelectedTheme = Theme
        end

        Rayfield.Main.BackgroundColor3 = SelectedTheme.Background
        Rayfield.Main.Topbar.BackgroundColor3 = SelectedTheme.Topbar
        Rayfield.Main.Topbar.CornerRepair.BackgroundColor3 = SelectedTheme.Topbar
        Rayfield.Main.Shadow.Image.ImageColor3 = SelectedTheme.Shadow

        Rayfield.Main.Topbar.ChangeSize.ImageColor3 = SelectedTheme.TextColor
        Rayfield.Main.Topbar.Hide.ImageColor3 = SelectedTheme.TextColor
        Rayfield.Main.Topbar.Search.ImageColor3 = SelectedTheme.TextColor
        if Topbar:FindFirstChild('Settings') then
            Rayfield.Main.Topbar.Settings.ImageColor3 = SelectedTheme.TextColor
            Rayfield.Main.Topbar.Divider.BackgroundColor3 = SelectedTheme.ElementStroke
        end

        Main.Search.BackgroundColor3 = SelectedTheme.TextColor
        Main.Search.Shadow.ImageColor3 = SelectedTheme.TextColor
        Main.Search.Search.ImageColor3 = SelectedTheme.TextColor
        Main.Search.Input.PlaceholderColor3 = SelectedTheme.TextColor
        Main.Search.UIStroke.Color = SelectedTheme.SecondaryElementStroke

        if Main:FindFirstChild('Notice') then
            Main.Notice.BackgroundColor3 = SelectedTheme.Background
        end

        for _, text in ipairs(Rayfield:GetDescendants()) do
            if text.Parent.Parent ~= Notifications then
                if text:IsA('TextLabel') or text:IsA('TextBox') then text.TextColor3 = SelectedTheme.TextColor end
            end
        end

        for _, TabPage in ipairs(Elements:GetChildren()) do
            for _, Element in ipairs(TabPage:GetChildren()) do
                if Element.ClassName == "Frame" and Element.Name ~= "Placeholder" and Element.Name ~= "SectionSpacing" and Element.Name ~= "Divider" and Element.Name ~= "SectionTitle" and Element.Name ~= "SearchTitle-fsefsefesfsefesfesfThanks" then
                    Element.BackgroundColor3 = SelectedTheme.ElementBackground
                    Element.UIStroke.Color = SelectedTheme.ElementStroke
                end
            end
        end
    end

    local function getIcon(name : string): {id: number, imageRectSize: Vector2, imageRectOffset: Vector2}
        if not Icons then
            warn("Lucide Icons: Cannot use icons as icons library is not loaded")
            return
        end
        name = string.match(string.lower(name), "^%s*(.*)%s*$") :: string
        local sizedicons = Icons['48px']
        local r = sizedicons[name]
        if not r then
            error(`Lucide Icons: Failed to find icon by the name of "{name}"`, 2)
        end

        local rirs = r[2]
        local riro = r[3]

        if typeof(r[1]) ~= "number" or typeof(rirs) ~= "table" or typeof(riro) ~= "table" then
            error("Lucide Icons: Internal error: Invalid auto-generated asset entry")
        end

        local irs = Vector2.new(rirs[1], rirs[2])
        local iro = Vector2.new(riro[1], riro[2])

        local asset = {
            id = r[1],
            imageRectSize = irs,
            imageRectOffset = iro,
        }

        return asset
    end
    -- Converts ID to asset URI. Returns rbxassetid://0 if ID is not a number
    local function getAssetUri(id: any): string
        local assetUri = "rbxassetid://0" -- Default to empty image
        if type(id) == "number" then
            assetUri = "rbxassetid://" .. id
        elseif type(id) == "string" and not Icons then
            warn("Rayfield | Cannot use Lucide icons as icons library is not loaded")
        else
            warn("Rayfield | The icon argument must either be an icon ID (number) or a Lucide icon name (string)")
        end
        return assetUri
    end

    local function makeDraggable(object, dragObject, enableTaptic, tapticOffset)
        local dragging = false
        local relative = nil

        local offset = Vector2.zero
        local screenGui = object:FindFirstAncestorWhichIsA("ScreenGui")
        if screenGui and screenGui.IgnoreGuiInset then
            offset += getService('GuiService'):GetGuiInset()
        end

        local function connectFunctions()
            if dragBar and enableTaptic then
                dragBar.MouseEnter:Connect(function()
                    if not dragging and not Hidden then
                        TweenService:Create(dragBarCosmetic, TweenInfo.new(0.25, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {BackgroundTransparency = 0.5, Size = UDim2.new(0, 120, 0, 4)}):Play()
                    end
                end)

                dragBar.MouseLeave:Connect(function()
                    if not dragging and not Hidden then
                        TweenService:Create(dragBarCosmetic, TweenInfo.new(0.25, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {BackgroundTransparency = 0.7, Size = UDim2.new(0, 100, 0, 4)}):Play()
                    end
                end)
            end
        end

        connectFunctions()

        dragObject.InputBegan:Connect(function(input, processed)
            if processed then return end

            local inputType = input.UserInputType.Name
            if inputType == "MouseButton1" or inputType == "Touch" then
                dragging = true

                relative = object.AbsolutePosition + object.AbsoluteSize * object.AnchorPoint - UserInputService:GetMouseLocation()
                if enableTaptic and not Hidden then
                    TweenService:Create(dragBarCosmetic, TweenInfo.new(0.35, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Size = UDim2.new(0, 110, 0, 4), BackgroundTransparency = 0}):Play()
                end
            end
        end)

        local inputEnded = UserInputService.InputEnded:Connect(function(input)
            if not dragging then return end

            local inputType = input.UserInputType.Name
            if inputType == "MouseButton1" or inputType == "Touch" then
                dragging = false

                connectFunctions()

                if enableTaptic and not Hidden then
                    TweenService:Create(dragBarCosmetic, TweenInfo.new(0.35, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Size = UDim2.new(0, 100, 0, 4), BackgroundTransparency = 0.7}):Play()
                end
            end
        end)

        local renderStepped = RunService.RenderStepped:Connect(function()
            if dragging and not Hidden then
                local position = UserInputService:GetMouseLocation() + relative + offset
                if enableTaptic and tapticOffset then
                    TweenService:Create(object, TweenInfo.new(0.4, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Position = UDim2.fromOffset(position.X, position.Y)}):Play()
                    TweenService:Create(dragObject.Parent, TweenInfo.new(0.05, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Position = UDim2.fromOffset(position.X, position.Y + ((useMobileSizing and tapticOffset[2]) or tapticOffset[1]))}):Play()
                else
                    if dragBar and tapticOffset then
                        dragBar.Position = UDim2.fromOffset(position.X, position.Y + ((useMobileSizing and tapticOffset[2]) or tapticOffset[1]))
                    end
                    object.Position = UDim2.fromOffset(position.X, position.Y)
                end
            end
        end)

        object.Destroying:Connect(function()
            if inputEnded then inputEnded:Disconnect() end
            if renderStepped then renderStepped:Disconnect() end
        end)
    end


    local function PackColor(Color)
        return {R = Color.R * 255, G = Color.G * 255, B = Color.B * 255}
    end    

    local function UnpackColor(Color)
        return Color3.fromRGB(Color.R, Color.G, Color.B)
    end

    local function LoadConfiguration(Configuration)
        local success, Data = pcall(function() return HttpService:JSONDecode(Configuration) end)
        local changed

        if not success then warn('Rayfield had an issue decoding the configuration file, please try delete the file and reopen Rayfield.') return end

        -- Iterate through current UI elements' flags
        for FlagName, Flag in pairs(RayfieldLibrary.Flags) do
            local FlagValue = Data[FlagName]

            if (typeof(FlagValue) == 'boolean' and FlagValue == false) or FlagValue then
                task.spawn(function()
                    if Flag.Type == "ColorPicker" then
                        changed = true
                        Flag:Set(UnpackColor(FlagValue))
                    else
                        if (Flag.CurrentValue or Flag.CurrentKeybind or Flag.CurrentOption or Flag.Color) ~= FlagValue then 
                            changed = true
                            Flag:Set(FlagValue) 	
                        end
                    end
                end)
            else
                warn("Rayfield | Unable to find '"..FlagName.. "' in the save file.")
                print("The error above may not be an issue if new elements have been added or not been set values.")
                --RayfieldLibrary:Notify({Title = "Rayfield Flags", Content = "Rayfield was unable to find '"..FlagName.. "' in the save file. Check sirius.menu/discord for help.", Image = 3944688398})
            end
        end

        return changed
    end

    local function SaveConfiguration()
        if not CEnabled or not globalLoaded then return end

        if debugX then
            print('Saving')
        end

        local Data = {}
        for i, v in pairs(RayfieldLibrary.Flags) do
            if v.Type == "ColorPicker" then
                Data[i] = PackColor(v.Color)
            else
                if typeof(v.CurrentValue) == 'boolean' then
                    if v.CurrentValue == false then
                        Data[i] = false
                    else
                        Data[i] = v.CurrentValue or v.CurrentKeybind or v.CurrentOption or v.Color
                    end
                else
                    Data[i] = v.CurrentValue or v.CurrentKeybind or v.CurrentOption or v.Color
                end
            end
        end

        if useStudio then
            if script.Parent:FindFirstChild('configuration') then script.Parent.configuration:Destroy() end

            local ScreenGui = Instance.new("ScreenGui")
            ScreenGui.Parent = script.Parent
            ScreenGui.Name = 'configuration'

            local TextBox = Instance.new("TextBox")
            TextBox.Parent = ScreenGui
            TextBox.Size = UDim2.new(0, 800, 0, 50)
            TextBox.AnchorPoint = Vector2.new(0.5, 0)
            TextBox.Position = UDim2.new(0.5, 0, 0, 30)
            TextBox.Text = HttpService:JSONEncode(Data)
            TextBox.ClearTextOnFocus = false
        end

        if debugX then
            warn(HttpService:JSONEncode(Data))
        end

        if writefile then
            writefile(ConfigurationFolder .. "/" .. CFileName .. ConfigurationExtension, tostring(HttpService:JSONEncode(Data)))
        end
    end

    function RayfieldLibrary:Notify(data) -- action e.g open messages
        task.spawn(function()

            -- Notification Object Creation
            local newNotification = Notifications.Template:Clone()
            newNotification.Name = data.Title or 'No Title Provided'
            newNotification.Parent = Notifications
            newNotification.LayoutOrder = #Notifications:GetChildren()
            newNotification.Visible = false

            -- Set Data
            newNotification.Title.Text = data.Title or "Unknown Title"
            newNotification.Description.Text = data.Content or "Unknown Content"

            if data.Image then
                if typeof(data.Image) == 'string' and Icons then
                    local asset = getIcon(data.Image)

                    newNotification.Icon.Image = 'rbxassetid://'..asset.id
                    newNotification.Icon.ImageRectOffset = asset.imageRectOffset
                    newNotification.Icon.ImageRectSize = asset.imageRectSize
                else
                    newNotification.Icon.Image = getAssetUri(data.Image)
                end
            else
                newNotification.Icon.Image = "rbxassetid://" .. 0
            end

            -- Set initial transparency values

            newNotification.Title.TextColor3 = SelectedTheme.TextColor
            newNotification.Description.TextColor3 = SelectedTheme.TextColor
            newNotification.BackgroundColor3 = SelectedTheme.Background
            newNotification.UIStroke.Color = SelectedTheme.TextColor
            newNotification.Icon.ImageColor3 = SelectedTheme.TextColor

            newNotification.BackgroundTransparency = 1
            newNotification.Title.TextTransparency = 1
            newNotification.Description.TextTransparency = 1
            newNotification.UIStroke.Transparency = 1
            newNotification.Shadow.ImageTransparency = 1
            newNotification.Size = UDim2.new(1, 0, 0, 800)
            newNotification.Icon.ImageTransparency = 1
            newNotification.Icon.BackgroundTransparency = 1

            task.wait()

            newNotification.Visible = true

            if data.Actions then
                warn('Rayfield | Not seeing your actions in notifications?')
                print("Notification Actions are being sunset for now, keep up to date on when they're back in the discord. (sirius.menu/discord)")
            end

            -- Calculate textbounds and set initial values
            local bounds = {newNotification.Title.TextBounds.Y, newNotification.Description.TextBounds.Y}
            newNotification.Size = UDim2.new(1, -60, 0, -Notifications:FindFirstChild("UIListLayout").Padding.Offset)

            newNotification.Icon.Size = UDim2.new(0, 32, 0, 32)
            newNotification.Icon.Position = UDim2.new(0, 20, 0.5, 0)

            TweenService:Create(newNotification, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Size = UDim2.new(1, 0, 0, math.max(bounds[1] + bounds[2] + 31, 60))}):Play()

            task.wait(0.15)
            TweenService:Create(newNotification, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0.45}):Play()
            TweenService:Create(newNotification.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()

            task.wait(0.05)

            TweenService:Create(newNotification.Icon, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 0}):Play()

            task.wait(0.05)
            TweenService:Create(newNotification.Description, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0.35}):Play()
            TweenService:Create(newNotification.UIStroke, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {Transparency = 0.95}):Play()
            TweenService:Create(newNotification.Shadow, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 0.82}):Play()

            local waitDuration = math.min(math.max((#newNotification.Description.Text * 0.1) + 2.5, 3), 10)
            task.wait(data.Duration or waitDuration)

            newNotification.Icon.Visible = false
            TweenService:Create(newNotification, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
            TweenService:Create(newNotification.UIStroke, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
            TweenService:Create(newNotification.Shadow, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
            TweenService:Create(newNotification.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
            TweenService:Create(newNotification.Description, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()

            TweenService:Create(newNotification, TweenInfo.new(1, Enum.EasingStyle.Exponential), {Size = UDim2.new(1, -90, 0, 0)}):Play()

            task.wait(1)

            TweenService:Create(newNotification, TweenInfo.new(1, Enum.EasingStyle.Exponential), {Size = UDim2.new(1, -90, 0, -Notifications:FindFirstChild("UIListLayout").Padding.Offset)}):Play()

            newNotification.Visible = false
            newNotification:Destroy()
        end)
    end

    local function openSearch()
        searchOpen = true

        Main.Search.BackgroundTransparency = 1
        Main.Search.Shadow.ImageTransparency = 1
        Main.Search.Input.TextTransparency = 1
        Main.Search.Search.ImageTransparency = 1
        Main.Search.UIStroke.Transparency = 1
        Main.Search.Size = UDim2.new(1, 0, 0, 80)
        Main.Search.Position = UDim2.new(0.5, 0, 0, 70)

        Main.Search.Input.Interactable = true

        Main.Search.Visible = true

        for _, tabbtn in ipairs(TabList:GetChildren()) do
            if tabbtn.ClassName == "Frame" and tabbtn.Name ~= "Placeholder" then
                tabbtn.Interact.Visible = false
                TweenService:Create(tabbtn, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                TweenService:Create(tabbtn.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                TweenService:Create(tabbtn.Image, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
                TweenService:Create(tabbtn.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
            end
        end

        Main.Search.Input:CaptureFocus()
        TweenService:Create(Main.Search.Shadow, TweenInfo.new(0.05, Enum.EasingStyle.Quint), {ImageTransparency = 0.95}):Play()
        TweenService:Create(Main.Search, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Position = UDim2.new(0.5, 0, 0, 57), BackgroundTransparency = 0.9}):Play()
        TweenService:Create(Main.Search.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 0.8}):Play()
        TweenService:Create(Main.Search.Input, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0.2}):Play()
        TweenService:Create(Main.Search.Search, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 0.5}):Play()
        TweenService:Create(Main.Search, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = UDim2.new(1, -35, 0, 35)}):Play()
    end

    local function closeSearch()
        searchOpen = false

        TweenService:Create(Main.Search, TweenInfo.new(0.35, Enum.EasingStyle.Quint), {BackgroundTransparency = 1, Size = UDim2.new(1, -55, 0, 30)}):Play()
        TweenService:Create(Main.Search.Search, TweenInfo.new(0.15, Enum.EasingStyle.Quint), {ImageTransparency = 1}):Play()
        TweenService:Create(Main.Search.Shadow, TweenInfo.new(0.15, Enum.EasingStyle.Quint), {ImageTransparency = 1}):Play()
        TweenService:Create(Main.Search.UIStroke, TweenInfo.new(0.15, Enum.EasingStyle.Quint), {Transparency = 1}):Play()
        TweenService:Create(Main.Search.Input, TweenInfo.new(0.15, Enum.EasingStyle.Quint), {TextTransparency = 1}):Play()

        for _, tabbtn in ipairs(TabList:GetChildren()) do
            if tabbtn.ClassName == "Frame" and tabbtn.Name ~= "Placeholder" then
                tabbtn.Interact.Visible = true
                if tostring(Elements.UIPageLayout.CurrentPage) == tabbtn.Title.Text then
                    TweenService:Create(tabbtn, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                    TweenService:Create(tabbtn.Image, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 0}):Play()
                    TweenService:Create(tabbtn.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
                    TweenService:Create(tabbtn.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                else
                    TweenService:Create(tabbtn, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0.7}):Play()
                    TweenService:Create(tabbtn.Image, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 0.2}):Play()
                    TweenService:Create(tabbtn.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0.2}):Play()
                    TweenService:Create(tabbtn.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 0.5}):Play()
                end
            end
        end

        Main.Search.Input.Text = ''
        Main.Search.Input.Interactable = false
    end

    local function Hide(notify: boolean?)
        if MPrompt then
            MPrompt.Title.TextColor3 = Color3.fromRGB(255, 255, 255)
            MPrompt.Position = UDim2.new(0.5, 0, 0, -50)
            MPrompt.Size = UDim2.new(0, 40, 0, 10)
            MPrompt.BackgroundTransparency = 1
            MPrompt.Title.TextTransparency = 1
            MPrompt.Visible = true
        end

        task.spawn(closeSearch)

        Debounce = true
        UserInputService.MouseIconEnabled = false
        if notify then
            if useMobilePrompt then 
                RayfieldLibrary:Notify({Title = "Interface Hidden", Content = "The interface has been hidden, you can unhide the interface by tapping 'Show'.", Duration = 7, Image = 4400697855})
            else
                RayfieldLibrary:Notify({Title = "Interface Hidden", Content = `The interface has been hidden, you can unhide the interface by tapping {getSetting("General", "rayfieldOpen")}.`, Duration = 7, Image = 4400697855})
            end
        end

        TweenService:Create(Main, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 470, 0, 0)}):Play()
        TweenService:Create(Main.Topbar, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 470, 0, 45)}):Play()
        TweenService:Create(Main, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
        TweenService:Create(Main.Topbar, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
        TweenService:Create(Main.Topbar.Divider, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
        TweenService:Create(Main.Topbar.CornerRepair, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
        TweenService:Create(Main.Topbar.Title, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
        TweenService:Create(Main.Shadow.Image, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
        TweenService:Create(Topbar.UIStroke, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
        TweenService:Create(dragBarCosmetic, TweenInfo.new(0.25, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {BackgroundTransparency = 1}):Play()

        if useMobilePrompt and MPrompt then
            TweenService:Create(MPrompt, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 120, 0, 30), Position = UDim2.new(0.5, 0, 0, 20), BackgroundTransparency = 0.3}):Play()
            TweenService:Create(MPrompt.Title, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 0.3}):Play()
        end

        for _, TopbarButton in ipairs(Topbar:GetChildren()) do
            if TopbarButton.ClassName == "ImageButton" then
                TweenService:Create(TopbarButton, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
            end
        end

        for _, tabbtn in ipairs(TabList:GetChildren()) do
            if tabbtn.ClassName == "Frame" and tabbtn.Name ~= "Placeholder" then
                TweenService:Create(tabbtn, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                TweenService:Create(tabbtn.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                TweenService:Create(tabbtn.Image, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
                TweenService:Create(tabbtn.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
            end
        end

        dragInteract.Visible = false

        for _, tab in ipairs(Elements:GetChildren()) do
            if tab.Name ~= "Template" and tab.ClassName == "ScrollingFrame" and tab.Name ~= "Placeholder" then
                for _, element in ipairs(tab:GetChildren()) do
                    if element.ClassName == "Frame" then
                        if element.Name ~= "SectionSpacing" and element.Name ~= "Placeholder" then
                            if element.Name == "SectionTitle" or element.Name == 'SearchTitle-fsefsefesfsefesfesfThanks' then
                                TweenService:Create(element.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                            elseif element.Name == 'Divider' then
                                TweenService:Create(element.Divider, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                            else
                                TweenService:Create(element, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                                TweenService:Create(element.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                                TweenService:Create(element.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                            end
                            for _, child in ipairs(element:GetChildren()) do
                                if child.ClassName == "Frame" or child.ClassName == "TextLabel" or child.ClassName == "TextBox" or child.ClassName == "ImageButton" or child.ClassName == "ImageLabel" then
                                    child.Visible = false
                                end
                            end
                        end
                    end
                end
            end
        end

        task.wait(0.5)
        Main.Visible = false
        Debounce = false
    end

    local function Maximise()
        Debounce = true
        Topbar.ChangeSize.Image = "rbxassetid://"..10137941941

        TweenService:Create(Topbar.UIStroke, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
        TweenService:Create(Main.Shadow.Image, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {ImageTransparency = 0.6}):Play()
        TweenService:Create(Topbar.CornerRepair, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
        TweenService:Create(Topbar.Divider, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
        TweenService:Create(dragBarCosmetic, TweenInfo.new(0.25, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {BackgroundTransparency = 0.7}):Play()
        TweenService:Create(Main, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = useMobileSizing and UDim2.new(0, 500, 0, 275) or UDim2.new(0, 500, 0, 475)}):Play()
        TweenService:Create(Topbar, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 500, 0, 45)}):Play()
        TabList.Visible = true
        task.wait(0.2)

        Elements.Visible = true

        for _, tab in ipairs(Elements:GetChildren()) do
            if tab.Name ~= "Template" and tab.ClassName == "ScrollingFrame" and tab.Name ~= "Placeholder" then
                for _, element in ipairs(tab:GetChildren()) do
                    if element.ClassName == "Frame" then
                        if element.Name ~= "SectionSpacing" and element.Name ~= "Placeholder" then
                            if element.Name == "SectionTitle" or element.Name == 'SearchTitle-fsefsefesfsefesfesfThanks' then
                                TweenService:Create(element.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0.4}):Play()
                            elseif element.Name == 'Divider' then
                                TweenService:Create(element.Divider, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0.85}):Play()
                            else
                                TweenService:Create(element, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                                TweenService:Create(element.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                                TweenService:Create(element.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
                            end
                            for _, child in ipairs(element:GetChildren()) do
                                if child.ClassName == "Frame" or child.ClassName == "TextLabel" or child.ClassName == "TextBox" or child.ClassName == "ImageButton" or child.ClassName == "ImageLabel" then
                                    child.Visible = true
                                end
                            end
                        end
                    end
                end
            end
        end

        task.wait(0.1)

        for _, tabbtn in ipairs(TabList:GetChildren()) do
            if tabbtn.ClassName == "Frame" and tabbtn.Name ~= "Placeholder" then
                if tostring(Elements.UIPageLayout.CurrentPage) == tabbtn.Title.Text then
                    TweenService:Create(tabbtn, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                    TweenService:Create(tabbtn.Image, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 0}):Play()
                    TweenService:Create(tabbtn.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
                    TweenService:Create(tabbtn.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                else
                    TweenService:Create(tabbtn, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0.7}):Play()
                    TweenService:Create(tabbtn.Image, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 0.2}):Play()
                    TweenService:Create(tabbtn.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0.2}):Play()
                    TweenService:Create(tabbtn.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 0.5}):Play()
                end

            end
        end

        task.wait(0.5)
        Debounce = false
    end


    local function Unhide()
        Debounce = true
        Main.Position = UDim2.new(0.5, 0, 0.5, 0)
        UserInputService.MouseIconEnabled = true
        Main.Visible = true
        TweenService:Create(Main, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = useMobileSizing and UDim2.new(0, 500, 0, 275) or UDim2.new(0, 500, 0, 475)}):Play()
        TweenService:Create(Main.Topbar, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 500, 0, 45)}):Play()
        TweenService:Create(Main.Shadow.Image, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageTransparency = 0.6}):Play()
        TweenService:Create(Main, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
        TweenService:Create(Main.Topbar, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
        TweenService:Create(Main.Topbar.Divider, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
        TweenService:Create(Main.Topbar.CornerRepair, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
        TweenService:Create(Main.Topbar.Title, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()

        if MPrompt then
            TweenService:Create(MPrompt, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 40, 0, 10), Position = UDim2.new(0.5, 0, 0, -50), BackgroundTransparency = 1}):Play()
            TweenService:Create(MPrompt.Title, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()

            task.spawn(function()
                task.wait(0.5)
                MPrompt.Visible = false
            end)
        end

        if Minimised then
            task.spawn(Maximise)
        end

        dragBar.Position = useMobileSizing and UDim2.new(0.5, 0, 0.5, dragOffsetMobile) or UDim2.new(0.5, 0, 0.5, dragOffset)

        dragInteract.Visible = true

        for _, TopbarButton in ipairs(Topbar:GetChildren()) do
            if TopbarButton.ClassName == "ImageButton" then
                if TopbarButton.Name == 'Icon' then
                    TweenService:Create(TopbarButton, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageTransparency = 0}):Play()
                else
                    TweenService:Create(TopbarButton, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageTransparency = 0.8}):Play()
                end

            end
        end

        for _, tabbtn in ipairs(TabList:GetChildren()) do
            if tabbtn.ClassName == "Frame" and tabbtn.Name ~= "Placeholder" then
                if tostring(Elements.UIPageLayout.CurrentPage) == tabbtn.Title.Text then
                    TweenService:Create(tabbtn, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                    TweenService:Create(tabbtn.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
                    TweenService:Create(tabbtn.Image, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 0}):Play()
                    TweenService:Create(tabbtn.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                else
                    TweenService:Create(tabbtn, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0.7}):Play()
                    TweenService:Create(tabbtn.Image, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 0.2}):Play()
                    TweenService:Create(tabbtn.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0.2}):Play()
                    TweenService:Create(tabbtn.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 0.5}):Play()
                end
            end
        end

        for _, tab in ipairs(Elements:GetChildren()) do
            if tab.Name ~= "Template" and tab.ClassName == "ScrollingFrame" and tab.Name ~= "Placeholder" then
                for _, element in ipairs(tab:GetChildren()) do
                    if element.ClassName == "Frame" then
                        if element.Name ~= "SectionSpacing" and element.Name ~= "Placeholder" then
                            if element.Name == "SectionTitle" or element.Name == 'SearchTitle-fsefsefesfsefesfesfThanks' then
                                TweenService:Create(element.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0.4}):Play()
                            elseif element.Name == 'Divider' then
                                TweenService:Create(element.Divider, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0.85}):Play()
                            else
                                TweenService:Create(element, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                                TweenService:Create(element.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                                TweenService:Create(element.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
                            end
                            for _, child in ipairs(element:GetChildren()) do
                                if child.ClassName == "Frame" or child.ClassName == "TextLabel" or child.ClassName == "TextBox" or child.ClassName == "ImageButton" or child.ClassName == "ImageLabel" then
                                    child.Visible = true
                                end
                            end
                        end
                    end
                end
            end
        end

        TweenService:Create(dragBarCosmetic, TweenInfo.new(0.25, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {BackgroundTransparency = 0.5}):Play()

        task.wait(0.5)
        Minimised = false
        Debounce = false
    end

    local function Minimise()
        Debounce = true
        Topbar.ChangeSize.Image = "rbxassetid://"..11036884234

        Topbar.UIStroke.Color = SelectedTheme.ElementStroke

        task.spawn(closeSearch)

        for _, tabbtn in ipairs(TabList:GetChildren()) do
            if tabbtn.ClassName == "Frame" and tabbtn.Name ~= "Placeholder" then
                TweenService:Create(tabbtn, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                TweenService:Create(tabbtn.Image, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
                TweenService:Create(tabbtn.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                TweenService:Create(tabbtn.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
            end
        end

        for _, tab in ipairs(Elements:GetChildren()) do
            if tab.Name ~= "Template" and tab.ClassName == "ScrollingFrame" and tab.Name ~= "Placeholder" then
                for _, element in ipairs(tab:GetChildren()) do
                    if element.ClassName == "Frame" then
                        if element.Name ~= "SectionSpacing" and element.Name ~= "Placeholder" then
                            if element.Name == "SectionTitle" or element.Name == 'SearchTitle-fsefsefesfsefesfesfThanks' then
                                TweenService:Create(element.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                            elseif element.Name == 'Divider' then
                                TweenService:Create(element.Divider, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                            else
                                TweenService:Create(element, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                                TweenService:Create(element.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                                TweenService:Create(element.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                            end
                            for _, child in ipairs(element:GetChildren()) do
                                if child.ClassName == "Frame" or child.ClassName == "TextLabel" or child.ClassName == "TextBox" or child.ClassName == "ImageButton" or child.ClassName == "ImageLabel" then
                                    child.Visible = false
                                end
                            end
                        end
                    end
                end
            end
        end

        TweenService:Create(dragBarCosmetic, TweenInfo.new(0.25, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {BackgroundTransparency = 1}):Play()
        TweenService:Create(Topbar.UIStroke, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
        TweenService:Create(Main.Shadow.Image, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
        TweenService:Create(Topbar.CornerRepair, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
        TweenService:Create(Topbar.Divider, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
        TweenService:Create(Main, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 495, 0, 45)}):Play()
        TweenService:Create(Topbar, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 495, 0, 45)}):Play()

        task.wait(0.3)

        Elements.Visible = false
        TabList.Visible = false

        task.wait(0.2)
        Debounce = false
    end

    local function saveSettings() -- Save settings to config file
        local encoded
        local success, err = pcall(function()
            encoded = HttpService:JSONEncode(settingsTable)
        end)

        if success then
            if useStudio then
                if script.Parent['get.val'] then
                    script.Parent['get.val'].Value = encoded
                end
            end
            if writefile then
                writefile(RayfieldFolder..'/settings'..ConfigurationExtension, encoded)
            end
        end
    end

    local function updateSetting(category: string, setting: string, value: any)
        if not settingsInitialized then
            return
        end
        settingsTable[category][setting].Value = value
        overriddenSettings[`{category}.{setting}`] = nil -- If user changes an overriden setting, remove the override
        saveSettings()
    end

    local function createSettings(window)
        if not (writefile and isfile and readfile and isfolder and makefolder) and not useStudio then
            if Topbar['Settings'] then Topbar.Settings.Visible = false end
            Topbar['Search'].Position = UDim2.new(1, -75, 0.5, 0)
            warn('Can\'t create settings as no file-saving functionality is available.')
            return
        end

        local newTab = window:CreateTab('Rayfield Settings', 0, true)

        if TabList['Rayfield Settings'] then
            TabList['Rayfield Settings'].LayoutOrder = 1000
        end

        if Elements['Rayfield Settings'] then
            Elements['Rayfield Settings'].LayoutOrder = 1000
        end

        -- Create sections and elements
        for categoryName, settingCategory in pairs(settingsTable) do
            newTab:CreateSection(categoryName)

            for settingName, setting in pairs(settingCategory) do
                if setting.Type == 'input' then
                    setting.Element = newTab:CreateInput({
                        Name = setting.Name,
                        CurrentValue = setting.Value,
                        PlaceholderText = setting.Placeholder,
                        Ext = true,
                        RemoveTextAfterFocusLost = setting.ClearOnFocus,
                        Callback = function(Value)
                            updateSetting(categoryName, settingName, Value)
                        end,
                    })
                elseif setting.Type == 'toggle' then
                    setting.Element = newTab:CreateToggle({
                        Name = setting.Name,
                        CurrentValue = setting.Value,
                        Ext = true,
                        Callback = function(Value)
                            updateSetting(categoryName, settingName, Value)
                        end,
                    })
                elseif setting.Type == 'bind' then
                    setting.Element = newTab:CreateKeybind({
                        Name = setting.Name,
                        CurrentKeybind = setting.Value,
                        HoldToInteract = false,
                        Ext = true,
                        CallOnChange = true,
                        Callback = function(Value)
                            updateSetting(categoryName, settingName, Value)
                        end,
                    })
                end
            end
        end

        settingsCreated = true
        loadSettings()
        saveSettings()
    end



    function RayfieldLibrary:CreateWindow(Settings)
        --[[
        if Rayfield:FindFirstChild('Loading') then
            if getgenv and not getgenv().rayfieldCached then
                Rayfield.Enabled = true
                Rayfield.Loading.Visible = true

                task.wait(1.4)
                Rayfield.Loading.Visible = false
            end
        end

        if getgenv then getgenv().rayfieldCached = true end

        if not correctBuild and not Settings.DisableBuildWarnings then
            task.delay(3, 
                function() 
                    RayfieldLibrary:Notify({Title = 'Build Mismatch', Content = 'Rayfield may encounter issues as you are running an incompatible interface version ('.. ((Rayfield:FindFirstChild('Build') and Rayfield.Build.Value) or 'No Build') ..').\n\nThis version of Rayfield is intended for interface build '..InterfaceBuild..'.\n\nTry rejoining and then run the script twice.', Image = 4335487866, Duration = 15})		
                end)
        end
        ]]--

        if Settings.ToggleUIKeybind then -- Can either be a string or an Enum.KeyCode
            local keybind = Settings.ToggleUIKeybind
            if type(keybind) == "string" then
                keybind = string.upper(keybind)
                assert(pcall(function()
                    return Enum.KeyCode[keybind]
                end), "ToggleUIKeybind must be a valid KeyCode")
                overrideSetting("General", "rayfieldOpen", keybind)
            elseif typeof(keybind) == "EnumItem" then
                assert(keybind.EnumType == Enum.KeyCode, "ToggleUIKeybind must be a KeyCode enum")
                overrideSetting("General", "rayfieldOpen", keybind.Name)
            else
                error("ToggleUIKeybind must be a string or KeyCode enum")
            end
        end

        if isfolder and not isfolder(RayfieldFolder) then
            makefolder(RayfieldFolder)
        end

        -- Attempt to report an event to analytics
        if not requestsDisabled then
            sendReport("window_created", Settings.Name or "Unknown")
        end
        local Passthrough = false
        
        Topbar.Title.Text = "<b>" .. Settings.Name .. "</b>"

        Main.Size = UDim2.new(0, 420, 0, 100)
        Main.Visible = true
        Main.BackgroundTransparency = 1
        if Main:FindFirstChild('Notice') then Main.Notice.Visible = false end
        Main.Shadow.Image.ImageTransparency = 1

        LoadingFrame.Title.TextTransparency = 1
        LoadingFrame.Subtitle.TextTransparency = 1

        if Settings.ShowText then
            MPrompt.Title.Text = 'Show '..Settings.ShowText
        end

        LoadingFrame.Version.TextTransparency = 1
        LoadingFrame.Title.Text = Settings.LoadingTitle or "Rayfield"
        LoadingFrame.Subtitle.Text = Settings.LoadingSubtitle or "Interface Suite"

        if Settings.LoadingTitle ~= "Rayfield Interface Suite" then
            LoadingFrame.Version.Text = "Rayfield UI"
        end

        if Settings.Icon and Settings.Icon ~= 0 and Topbar:FindFirstChild('Icon') then
            Topbar.Icon.Visible = true
            Topbar.Title.Position = UDim2.new(0, 47, 0.5, 0)

            if Settings.Icon then
                if typeof(Settings.Icon) == 'string' and Icons then
                    local asset = getIcon(Settings.Icon)

                    Topbar.Icon.Image = 'rbxassetid://'..asset.id
                    Topbar.Icon.ImageRectOffset = asset.imageRectOffset
                    Topbar.Icon.ImageRectSize = asset.imageRectSize
                else
                    Topbar.Icon.Image = getAssetUri(Settings.Icon)
                end
            else
                Topbar.Icon.Image = "rbxassetid://" .. 0
            end
        end

        if dragBar then
            dragBar.Visible = false
            dragBarCosmetic.BackgroundTransparency = 1
            dragBar.Visible = true
        end

        if Settings.Theme then
            local success, result = pcall(ChangeTheme, Settings.Theme)
            if not success then
                local success, result2 = pcall(ChangeTheme, 'Default')
                if not success then
                    warn('CRITICAL ERROR - NO DEFAULT THEME')
                    print(result2)
                end
                warn('issue rendering theme. no theme on file')
                print(result)
            end
        end

        Topbar.Visible = false
        Elements.Visible = false
        LoadingFrame.Visible = true

        pcall(function()
            if not Settings.ConfigurationSaving.FileName then
                Settings.ConfigurationSaving.FileName = tostring(game.PlaceId)
            end

            if Settings.ConfigurationSaving.Enabled == nil then
                Settings.ConfigurationSaving.Enabled = false
            end

            CFileName = Settings.ConfigurationSaving.FileName
            ConfigurationFolder = Settings.ConfigurationSaving.FolderName or ConfigurationFolder
            CEnabled = Settings.ConfigurationSaving.Enabled

            if Settings.ConfigurationSaving.Enabled then
                if not isfolder(ConfigurationFolder) then
                    makefolder(ConfigurationFolder)
                end	
            end
        end)


        makeDraggable(Main, Topbar, false, {dragOffset, dragOffsetMobile})
        if dragBar then dragBar.Position = useMobileSizing and UDim2.new(0.5, 0, 0.5, dragOffsetMobile) or UDim2.new(0.5, 0, 0.5, dragOffset) makeDraggable(Main, dragInteract, true, {dragOffset, dragOffsetMobile}) end

        for _, TabButton in ipairs(TabList:GetChildren()) do
            if TabButton.ClassName == "Frame" and TabButton.Name ~= "Placeholder" then
                TabButton.BackgroundTransparency = 1
                TabButton.Title.TextTransparency = 1
                TabButton.Image.ImageTransparency = 1
                TabButton.UIStroke.Transparency = 1
            end
        end

        if Settings.Discord and Settings.Discord.Enabled and not useStudio then
            if isfolder and not isfolder(RayfieldFolder.."/Discord Invites") then
                makefolder(RayfieldFolder.."/Discord Invites")
            end

            if isfile and not isfile(RayfieldFolder.."/Discord Invites".."/"..Settings.Discord.Invite..ConfigurationExtension) then
                if requestFunc then
                    pcall(function()
                        requestFunc({
                            Url = 'http://127.0.0.1:6463/rpc?v=1',
                            Method = 'POST',
                            Headers = {
                                ['Content-Type'] = 'application/json',
                                Origin = 'https://discord.com'
                            },
                            Body = HttpService:JSONEncode({
                                cmd = 'INVITE_BROWSER',
                                nonce = HttpService:GenerateGUID(false),
                                args = {code = Settings.Discord.Invite}
                            })
                        })
                    end)
                end

                if Settings.Discord.RememberJoins then -- We do logic this way so if the developer changes this setting, the user still won't be prompted, only new users
                    writefile(RayfieldFolder.."/Discord Invites".."/"..Settings.Discord.Invite..ConfigurationExtension,"Rayfield RememberJoins is true for this invite, this invite will not ask you to join again")
                end
            end
        end

        if (Settings.KeySystem) then
            if not Settings.KeySettings then
                Passthrough = true
                return
            end

            if isfolder and not isfolder(RayfieldFolder.."/Key System") then
                makefolder(RayfieldFolder.."/Key System")
            end

            if typeof(Settings.KeySettings.Key) == "string" then Settings.KeySettings.Key = {Settings.KeySettings.Key} end

            if Settings.KeySettings.GrabKeyFromSite then
                for i, Key in ipairs(Settings.KeySettings.Key) do
                    local Success, Response = pcall(function()
                        Settings.KeySettings.Key[i] = tostring(game:HttpGet(Key):gsub("[\n\r]", " "))
                        Settings.KeySettings.Key[i] = string.gsub(Settings.KeySettings.Key[i], " ", "")
                    end)
                    if not Success then
                        print("Rayfield | "..Key.." Error " ..tostring(Response))
                        warn('Check docs.sirius.menu for help with Rayfield specific development.')
                    end
                end
            end

            if not Settings.KeySettings.FileName then
                Settings.KeySettings.FileName = "No file name specified"
            end

            if isfile and isfile(RayfieldFolder.."/Key System".."/"..Settings.KeySettings.FileName..ConfigurationExtension) then
                for _, MKey in ipairs(Settings.KeySettings.Key) do
                    if string.find(readfile(RayfieldFolder.."/Key System".."/"..Settings.KeySettings.FileName..ConfigurationExtension), MKey) then
                        Passthrough = true
                    end
                end
            end

            if not Passthrough then
                local AttemptsRemaining = math.random(2, 5)
                Rayfield.Enabled = false
                local KeyUI = useStudio and script.Parent:FindFirstChild('Key') or game:GetObjects("rbxassetid://11380036235")[1]

                KeyUI.Enabled = true

                if gethui then
                    KeyUI.Parent = gethui()
                elseif syn and syn.protect_gui then 
                    syn.protect_gui(KeyUI)
                    KeyUI.Parent = CoreGui
                elseif not useStudio and CoreGui:FindFirstChild("RobloxGui") then
                    KeyUI.Parent = CoreGui:FindFirstChild("RobloxGui")
                elseif not useStudio then
                    KeyUI.Parent = CoreGui
                end

                if gethui then
                    for _, Interface in ipairs(gethui():GetChildren()) do
                        if Interface.Name == KeyUI.Name and Interface ~= KeyUI then
                            Interface.Enabled = false
                            Interface.Name = "KeyUI-Old"
                        end
                    end
                elseif not useStudio then
                    for _, Interface in ipairs(CoreGui:GetChildren()) do
                        if Interface.Name == KeyUI.Name and Interface ~= KeyUI then
                            Interface.Enabled = false
                            Interface.Name = "KeyUI-Old"
                        end
                    end
                end

                local KeyMain = KeyUI.Main
                KeyMain.Title.Text = Settings.KeySettings.Title or Settings.Name
                KeyMain.Subtitle.Text = Settings.KeySettings.Subtitle or "Key System"
                KeyMain.NoteMessage.Text = Settings.KeySettings.Note or "No instructions"

                KeyMain.Size = UDim2.new(0, 467, 0, 175)
                KeyMain.BackgroundTransparency = 1
                KeyMain.Shadow.Image.ImageTransparency = 1
                KeyMain.Title.TextTransparency = 1
                KeyMain.Subtitle.TextTransparency = 1
                KeyMain.KeyNote.TextTransparency = 1
                KeyMain.Input.BackgroundTransparency = 1
                KeyMain.Input.UIStroke.Transparency = 1
                KeyMain.Input.InputBox.TextTransparency = 1
                KeyMain.NoteTitle.TextTransparency = 1
                KeyMain.NoteMessage.TextTransparency = 1
                KeyMain.Hide.ImageTransparency = 1

                TweenService:Create(KeyMain, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                TweenService:Create(KeyMain, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 500, 0, 187)}):Play()
                TweenService:Create(KeyMain.Shadow.Image, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {ImageTransparency = 0.5}):Play()
                task.wait(0.05)
                TweenService:Create(KeyMain.Title, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
                TweenService:Create(KeyMain.Subtitle, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
                task.wait(0.05)
                TweenService:Create(KeyMain.KeyNote, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
                TweenService:Create(KeyMain.Input, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                TweenService:Create(KeyMain.Input.UIStroke, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                TweenService:Create(KeyMain.Input.InputBox, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
                task.wait(0.05)
                TweenService:Create(KeyMain.NoteTitle, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
                TweenService:Create(KeyMain.NoteMessage, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
                task.wait(0.15)
                TweenService:Create(KeyMain.Hide, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {ImageTransparency = 0.3}):Play()


                KeyUI.Main.Input.InputBox.FocusLost:Connect(function()
                    if #KeyUI.Main.Input.InputBox.Text == 0 then return end
                    local KeyFound = false
                    local FoundKey = ''
                    for _, MKey in ipairs(Settings.KeySettings.Key) do
                        --if string.find(KeyMain.Input.InputBox.Text, MKey) then
                        --	KeyFound = true
                        --	FoundKey = MKey
                        --end


                        -- stricter key check
                        if KeyMain.Input.InputBox.Text == MKey then
                            KeyFound = true
                            FoundKey = MKey
                        end
                    end
                    if KeyFound then 
                        TweenService:Create(KeyMain, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                        TweenService:Create(KeyMain, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 467, 0, 175)}):Play()
                        TweenService:Create(KeyMain.Shadow.Image, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
                        TweenService:Create(KeyMain.Title, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                        TweenService:Create(KeyMain.Subtitle, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                        TweenService:Create(KeyMain.KeyNote, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                        TweenService:Create(KeyMain.Input, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                        TweenService:Create(KeyMain.Input.UIStroke, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        TweenService:Create(KeyMain.Input.InputBox, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                        TweenService:Create(KeyMain.NoteTitle, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                        TweenService:Create(KeyMain.NoteMessage, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                        TweenService:Create(KeyMain.Hide, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
                        task.wait(0.51)
                        Passthrough = true
                        KeyMain.Visible = false
                        if Settings.KeySettings.SaveKey then
                            if writefile then
                                writefile(RayfieldFolder.."/Key System".."/"..Settings.KeySettings.FileName..ConfigurationExtension, FoundKey)
                            end
                            RayfieldLibrary:Notify({Title = "Key System", Content = "The key for this script has been saved successfully.", Image = 3605522284})
                        end
                    else
                        if AttemptsRemaining == 0 then
                            TweenService:Create(KeyMain, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                            TweenService:Create(KeyMain, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 467, 0, 175)}):Play()
                            TweenService:Create(KeyMain.Shadow.Image, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
                            TweenService:Create(KeyMain.Title, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                            TweenService:Create(KeyMain.Subtitle, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                            TweenService:Create(KeyMain.KeyNote, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                            TweenService:Create(KeyMain.Input, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                            TweenService:Create(KeyMain.Input.UIStroke, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                            TweenService:Create(KeyMain.Input.InputBox, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                            TweenService:Create(KeyMain.NoteTitle, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                            TweenService:Create(KeyMain.NoteMessage, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                            TweenService:Create(KeyMain.Hide, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
                            task.wait(0.45)
                            Players.LocalPlayer:Kick("No Attempts Remaining")
                            game:Shutdown()
                        end
                        KeyMain.Input.InputBox.Text = ""
                        AttemptsRemaining = AttemptsRemaining - 1
                        TweenService:Create(KeyMain, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 467, 0, 175)}):Play()
                        TweenService:Create(KeyMain, TweenInfo.new(0.4, Enum.EasingStyle.Elastic), {Position = UDim2.new(0.495,0,0.5,0)}):Play()
                        task.wait(0.1)
                        TweenService:Create(KeyMain, TweenInfo.new(0.4, Enum.EasingStyle.Elastic), {Position = UDim2.new(0.505,0,0.5,0)}):Play()
                        task.wait(0.1)
                        TweenService:Create(KeyMain, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {Position = UDim2.new(0.5,0,0.5,0)}):Play()
                        TweenService:Create(KeyMain, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 500, 0, 187)}):Play()
                    end
                end)

                KeyMain.Hide.MouseButton1Click:Connect(function()
                    TweenService:Create(KeyMain, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                    TweenService:Create(KeyMain, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 467, 0, 175)}):Play()
                    TweenService:Create(KeyMain.Shadow.Image, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
                    TweenService:Create(KeyMain.Title, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                    TweenService:Create(KeyMain.Subtitle, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                    TweenService:Create(KeyMain.KeyNote, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                    TweenService:Create(KeyMain.Input, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                    TweenService:Create(KeyMain.Input.UIStroke, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                    TweenService:Create(KeyMain.Input.InputBox, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                    TweenService:Create(KeyMain.NoteTitle, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                    TweenService:Create(KeyMain.NoteMessage, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                    TweenService:Create(KeyMain.Hide, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
                    task.wait(0.51)
                    RayfieldLibrary:Destroy()
                    KeyUI:Destroy()
                end)
            else
                Passthrough = true
            end
        end
        if Settings.KeySystem then
            repeat task.wait() until Passthrough
        end

        Notifications.Template.Visible = false
        Notifications.Visible = true
        Rayfield.Enabled = true

        task.wait(0.5)
        TweenService:Create(Main, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
        TweenService:Create(Main.Shadow.Image, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageTransparency = 0.6}):Play()
        task.wait(0.1)
        TweenService:Create(LoadingFrame.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
        task.wait(0.05)
        TweenService:Create(LoadingFrame.Subtitle, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
        task.wait(0.05)
        TweenService:Create(LoadingFrame.Version, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()


        Elements.Template.LayoutOrder = 100000
        Elements.Template.Visible = false

        Elements.UIPageLayout.FillDirection = Enum.FillDirection.Horizontal
        TabList.Template.Visible = false

        -- Tab
        local FirstTab = false
        local Window = {}
        function Window:CreateTab(Name, Image, Ext)
            local SDone = false
            local TabButton = TabList.Template:Clone()
            TabButton.Name = Name
            TabButton.Title.Text = Name
            TabButton.Parent = TabList
            TabButton.Title.TextWrapped = false
            TabButton.Size = UDim2.new(0, TabButton.Title.TextBounds.X + 30, 0, 30)

            if Image and Image ~= 0 then
                if typeof(Image) == 'string' and Icons then
                    local asset = getIcon(Image)

                    TabButton.Image.Image = 'rbxassetid://'..asset.id
                    TabButton.Image.ImageRectOffset = asset.imageRectOffset
                    TabButton.Image.ImageRectSize = asset.imageRectSize
                else
                    TabButton.Image.Image = getAssetUri(Image)
                end

                TabButton.Title.AnchorPoint = Vector2.new(0, 0.5)
                TabButton.Title.Position = UDim2.new(0, 37, 0.5, 0)
                TabButton.Image.Visible = true
                TabButton.Title.TextXAlignment = Enum.TextXAlignment.Left
                TabButton.Size = UDim2.new(0, TabButton.Title.TextBounds.X + 52, 0, 30)
            end



            TabButton.BackgroundTransparency = 1
            TabButton.Title.TextTransparency = 1
            TabButton.Image.ImageTransparency = 1
            TabButton.UIStroke.Transparency = 1

            TabButton.Visible = not Ext or false

            -- Create Elements Page
            local TabPage = Elements.Template:Clone()
            TabPage.Name = Name
            TabPage.Visible = true

            TabPage.LayoutOrder = #Elements:GetChildren() or Ext and 10000

            for _, TemplateElement in ipairs(TabPage:GetChildren()) do
                if TemplateElement.ClassName == "Frame" and TemplateElement.Name ~= "Placeholder" then
                    TemplateElement:Destroy()
                end
            end

            TabPage.Parent = Elements
            if not FirstTab and not Ext then
                Elements.UIPageLayout.Animated = false
                Elements.UIPageLayout:JumpTo(TabPage)
                Elements.UIPageLayout.Animated = true
            end

            TabButton.UIStroke.Color = SelectedTheme.TabStroke

            if Elements.UIPageLayout.CurrentPage == TabPage then
                TabButton.BackgroundColor3 = SelectedTheme.TabBackgroundSelected
                TabButton.Image.ImageColor3 = SelectedTheme.SelectedTabTextColor
                TabButton.Title.TextColor3 = SelectedTheme.SelectedTabTextColor
            else
                TabButton.BackgroundColor3 = SelectedTheme.TabBackground
                TabButton.Image.ImageColor3 = SelectedTheme.TabTextColor
                TabButton.Title.TextColor3 = SelectedTheme.TabTextColor
            end


            -- Animate
            task.wait(0.1)
            if FirstTab or Ext then
                TabButton.BackgroundColor3 = SelectedTheme.TabBackground
                TabButton.Image.ImageColor3 = SelectedTheme.TabTextColor
                TabButton.Title.TextColor3 = SelectedTheme.TabTextColor
                TweenService:Create(TabButton, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0.7}):Play()
                TweenService:Create(TabButton.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0.2}):Play()
                TweenService:Create(TabButton.Image, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageTransparency = 0.2}):Play()
                TweenService:Create(TabButton.UIStroke, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Transparency = 0.5}):Play()
            elseif not Ext then
                FirstTab = Name
                TabButton.BackgroundColor3 = SelectedTheme.TabBackgroundSelected
                TabButton.Image.ImageColor3 = SelectedTheme.SelectedTabTextColor
                TabButton.Title.TextColor3 = SelectedTheme.SelectedTabTextColor
                TweenService:Create(TabButton.Image, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageTransparency = 0}):Play()
                TweenService:Create(TabButton, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                TweenService:Create(TabButton.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
            end


            TabButton.Interact.MouseButton1Click:Connect(function()
                if Minimised then return end
                TweenService:Create(TabButton, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                TweenService:Create(TabButton.UIStroke, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                TweenService:Create(TabButton.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
                TweenService:Create(TabButton.Image, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageTransparency = 0}):Play()
                TweenService:Create(TabButton, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.TabBackgroundSelected}):Play()
                TweenService:Create(TabButton.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextColor3 = SelectedTheme.SelectedTabTextColor}):Play()
                TweenService:Create(TabButton.Image, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageColor3 = SelectedTheme.SelectedTabTextColor}):Play()

                for _, OtherTabButton in ipairs(TabList:GetChildren()) do
                    if OtherTabButton.Name ~= "Template" and OtherTabButton.ClassName == "Frame" and OtherTabButton ~= TabButton and OtherTabButton.Name ~= "Placeholder" then
                        TweenService:Create(OtherTabButton, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.TabBackground}):Play()
                        TweenService:Create(OtherTabButton.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextColor3 = SelectedTheme.TabTextColor}):Play()
                        TweenService:Create(OtherTabButton.Image, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageColor3 = SelectedTheme.TabTextColor}):Play()
                        TweenService:Create(OtherTabButton, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0.7}):Play()
                        TweenService:Create(OtherTabButton.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0.2}):Play()
                        TweenService:Create(OtherTabButton.Image, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageTransparency = 0.2}):Play()
                        TweenService:Create(OtherTabButton.UIStroke, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Transparency = 0.5}):Play()
                    end
                end

                if Elements.UIPageLayout.CurrentPage ~= TabPage then
                    Elements.UIPageLayout:JumpTo(TabPage)
                end
            end)

            local Tab = {}

            -- Button
            function Tab:CreateButton(ButtonSettings)
                local ButtonValue = {}

                local Button = Elements.Template.Button:Clone()
                Button.Name = ButtonSettings.Name
                Button.Title.Text = ButtonSettings.Name
                Button.Visible = true
                Button.Parent = TabPage

                Button.BackgroundTransparency = 1
                Button.UIStroke.Transparency = 1
                Button.Title.TextTransparency = 1

                TweenService:Create(Button, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                TweenService:Create(Button.UIStroke, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                TweenService:Create(Button.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()	


                Button.Interact.MouseButton1Click:Connect(function()
                    local Success, Response = pcall(ButtonSettings.Callback)
                    -- Prevents animation from trying to play if the button's callback called RayfieldLibrary:Destroy()
                    if rayfieldDestroyed then
                        return
                    end
                    if not Success then
                        TweenService:Create(Button, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = Color3.fromRGB(85, 0, 0)}):Play()
                        TweenService:Create(Button.ElementIndicator, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                        TweenService:Create(Button.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        Button.Title.Text = "Callback Error"
                        print("Rayfield | "..ButtonSettings.Name.." Callback Error " ..tostring(Response))
                        warn('Check docs.sirius.menu for help with Rayfield specific development.')
                        task.wait(0.5)
                        Button.Title.Text = ButtonSettings.Name
                        TweenService:Create(Button, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                        TweenService:Create(Button.ElementIndicator, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {TextTransparency = 0.9}):Play()
                        TweenService:Create(Button.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                    else
                        if not ButtonSettings.Ext then
                            SaveConfiguration(ButtonSettings.Name..'\n')
                        end
                        TweenService:Create(Button, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                        TweenService:Create(Button.ElementIndicator, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                        TweenService:Create(Button.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        task.wait(0.2)
                        TweenService:Create(Button, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                        TweenService:Create(Button.ElementIndicator, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {TextTransparency = 0.9}):Play()
                        TweenService:Create(Button.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                    end
                end)

                Button.MouseEnter:Connect(function()
                    TweenService:Create(Button, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                    TweenService:Create(Button.ElementIndicator, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {TextTransparency = 0.7}):Play()
                end)

                Button.MouseLeave:Connect(function()
                    TweenService:Create(Button, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                    TweenService:Create(Button.ElementIndicator, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {TextTransparency = 0.9}):Play()
                end)

                function ButtonValue:Set(NewButton)
                    Button.Title.Text = NewButton
                    Button.Name = NewButton
                end

                return ButtonValue
            end

            -- ColorPicker
            function Tab:CreateColorPicker(ColorPickerSettings) -- by Throit
                ColorPickerSettings.Type = "ColorPicker"
                local ColorPicker = Elements.Template.ColorPicker:Clone()
                local Background = ColorPicker.CPBackground
                local Display = Background.Display
                local Main = Background.MainCP
                local Slider = ColorPicker.ColorSlider
                ColorPicker.ClipsDescendants = true
                ColorPicker.Name = ColorPickerSettings.Name
                ColorPicker.Title.Text = ColorPickerSettings.Name
                ColorPicker.Visible = true
                ColorPicker.Parent = TabPage
                ColorPicker.Size = UDim2.new(1, -10, 0, 45)
                Background.Size = UDim2.new(0, 39, 0, 22)
                Display.BackgroundTransparency = 0
                Main.MainPoint.ImageTransparency = 1
                ColorPicker.Interact.Size = UDim2.new(1, 0, 1, 0)
                ColorPicker.Interact.Position = UDim2.new(0.5, 0, 0.5, 0)
                ColorPicker.RGB.Position = UDim2.new(0, 17, 0, 70)
                ColorPicker.HexInput.Position = UDim2.new(0, 17, 0, 90)
                Main.ImageTransparency = 1
                Background.BackgroundTransparency = 1

                for _, rgbinput in ipairs(ColorPicker.RGB:GetChildren()) do
                    if rgbinput:IsA("Frame") then
                        rgbinput.BackgroundColor3 = SelectedTheme.InputBackground
                        rgbinput.UIStroke.Color = SelectedTheme.InputStroke
                    end
                end

                ColorPicker.HexInput.BackgroundColor3 = SelectedTheme.InputBackground
                ColorPicker.HexInput.UIStroke.Color = SelectedTheme.InputStroke

                local opened = false 
                local mouse = Players.LocalPlayer:GetMouse()
                Main.Image = "http://www.roblox.com/asset/?id=11415645739"
                local mainDragging = false 
                local sliderDragging = false 
                ColorPicker.Interact.MouseButton1Down:Connect(function()
                    task.spawn(function()
                        TweenService:Create(ColorPicker, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                        TweenService:Create(ColorPicker.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        task.wait(0.2)
                        TweenService:Create(ColorPicker, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                        TweenService:Create(ColorPicker.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                    end)

                    if not opened then
                        opened = true 
                        TweenService:Create(Background, TweenInfo.new(0.45, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 18, 0, 15)}):Play()
                        task.wait(0.1)
                        TweenService:Create(ColorPicker, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Size = UDim2.new(1, -10, 0, 120)}):Play()
                        TweenService:Create(Background, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 173, 0, 86)}):Play()
                        TweenService:Create(Display, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                        TweenService:Create(ColorPicker.Interact, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Position = UDim2.new(0.289, 0, 0.5, 0)}):Play()
                        TweenService:Create(ColorPicker.RGB, TweenInfo.new(0.8, Enum.EasingStyle.Exponential), {Position = UDim2.new(0, 17, 0, 40)}):Play()
                        TweenService:Create(ColorPicker.HexInput, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Position = UDim2.new(0, 17, 0, 73)}):Play()
                        TweenService:Create(ColorPicker.Interact, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Size = UDim2.new(0.574, 0, 1, 0)}):Play()
                        TweenService:Create(Main.MainPoint, TweenInfo.new(0.2, Enum.EasingStyle.Exponential), {ImageTransparency = 0}):Play()
                        TweenService:Create(Main, TweenInfo.new(0.2, Enum.EasingStyle.Exponential), {ImageTransparency = SelectedTheme ~= RayfieldLibrary.Theme.Default and 0.25 or 0.1}):Play()
                        TweenService:Create(Background, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                    else
                        opened = false
                        TweenService:Create(ColorPicker, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Size = UDim2.new(1, -10, 0, 45)}):Play()
                        TweenService:Create(Background, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 39, 0, 22)}):Play()
                        TweenService:Create(ColorPicker.Interact, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Size = UDim2.new(1, 0, 1, 0)}):Play()
                        TweenService:Create(ColorPicker.Interact, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Position = UDim2.new(0.5, 0, 0.5, 0)}):Play()
                        TweenService:Create(ColorPicker.RGB, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Position = UDim2.new(0, 17, 0, 70)}):Play()
                        TweenService:Create(ColorPicker.HexInput, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Position = UDim2.new(0, 17, 0, 90)}):Play()
                        TweenService:Create(Display, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                        TweenService:Create(Main.MainPoint, TweenInfo.new(0.2, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
                        TweenService:Create(Main, TweenInfo.new(0.2, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
                        TweenService:Create(Background, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                    end

                end)

                UserInputService.InputEnded:Connect(function(input, gameProcessed) if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then 
                        mainDragging = false
                        sliderDragging = false
                    end end)
                Main.MouseButton1Down:Connect(function()
                    if opened then
                        mainDragging = true 
                    end
                end)
                Main.MainPoint.MouseButton1Down:Connect(function()
                    if opened then
                        mainDragging = true 
                    end
                end)
                Slider.MouseButton1Down:Connect(function()
                    sliderDragging = true 
                end)
                Slider.SliderPoint.MouseButton1Down:Connect(function()
                    sliderDragging = true 
                end)
                local h,s,v = ColorPickerSettings.Color:ToHSV()
                local color = Color3.fromHSV(h,s,v) 
                local hex = string.format("#%02X%02X%02X",color.R*0xFF,color.G*0xFF,color.B*0xFF)
                ColorPicker.HexInput.InputBox.Text = hex
                local function setDisplay()
                    --Main
                    Main.MainPoint.Position = UDim2.new(s,-Main.MainPoint.AbsoluteSize.X/2,1-v,-Main.MainPoint.AbsoluteSize.Y/2)
                    Main.MainPoint.ImageColor3 = Color3.fromHSV(h,s,v)
                    Background.BackgroundColor3 = Color3.fromHSV(h,1,1)
                    Display.BackgroundColor3 = Color3.fromHSV(h,s,v)
                    --Slider 
                    local x = h * Slider.AbsoluteSize.X
                    Slider.SliderPoint.Position = UDim2.new(0,x-Slider.SliderPoint.AbsoluteSize.X/2,0.5,0)
                    Slider.SliderPoint.ImageColor3 = Color3.fromHSV(h,1,1)
                    local color = Color3.fromHSV(h,s,v) 
                    local r,g,b = math.floor((color.R*255)+0.5),math.floor((color.G*255)+0.5),math.floor((color.B*255)+0.5)
                    ColorPicker.RGB.RInput.InputBox.Text = tostring(r)
                    ColorPicker.RGB.GInput.InputBox.Text = tostring(g)
                    ColorPicker.RGB.BInput.InputBox.Text = tostring(b)
                    hex = string.format("#%02X%02X%02X",color.R*0xFF,color.G*0xFF,color.B*0xFF)
                    ColorPicker.HexInput.InputBox.Text = hex
                end
                setDisplay()
                ColorPicker.HexInput.InputBox.FocusLost:Connect(function()
                    if not pcall(function()
                            local r, g, b = string.match(ColorPicker.HexInput.InputBox.Text, "^#?(%w%w)(%w%w)(%w%w)$")
                            local rgbColor = Color3.fromRGB(tonumber(r, 16),tonumber(g, 16), tonumber(b, 16))
                            h,s,v = rgbColor:ToHSV()
                            hex = ColorPicker.HexInput.InputBox.Text
                            setDisplay()
                            ColorPickerSettings.Color = rgbColor
                        end) 
                    then 
                        ColorPicker.HexInput.InputBox.Text = hex 
                    end
                    pcall(function()ColorPickerSettings.Callback(Color3.fromHSV(h,s,v))end)
                    local r,g,b = math.floor((h*255)+0.5),math.floor((s*255)+0.5),math.floor((v*255)+0.5)
                    ColorPickerSettings.Color = Color3.fromRGB(r,g,b)
                    if not ColorPickerSettings.Ext then
                        SaveConfiguration()
                    end
                end)
                --RGB
                local function rgbBoxes(box,toChange)
                    local value = tonumber(box.Text) 
                    local color = Color3.fromHSV(h,s,v) 
                    local oldR,oldG,oldB = math.floor((color.R*255)+0.5),math.floor((color.G*255)+0.5),math.floor((color.B*255)+0.5)
                    local save 
                    if toChange == "R" then save = oldR;oldR = value elseif toChange == "G" then save = oldG;oldG = value else save = oldB;oldB = value end
                    if value then 
                        value = math.clamp(value,0,255)
                        h,s,v = Color3.fromRGB(oldR,oldG,oldB):ToHSV()

                        setDisplay()
                    else 
                        box.Text = tostring(save)
                    end
                    local r,g,b = math.floor((h*255)+0.5),math.floor((s*255)+0.5),math.floor((v*255)+0.5)
                    ColorPickerSettings.Color = Color3.fromRGB(r,g,b)
                    if not ColorPickerSettings.Ext then
                        SaveConfiguration(ColorPickerSettings.Flag..'\n'..tostring(ColorPickerSettings.Color))
                    end
                end
                ColorPicker.RGB.RInput.InputBox.FocusLost:connect(function()
                    rgbBoxes(ColorPicker.RGB.RInput.InputBox,"R")
                    pcall(function()ColorPickerSettings.Callback(Color3.fromHSV(h,s,v))end)
                end)
                ColorPicker.RGB.GInput.InputBox.FocusLost:connect(function()
                    rgbBoxes(ColorPicker.RGB.GInput.InputBox,"G")
                    pcall(function()ColorPickerSettings.Callback(Color3.fromHSV(h,s,v))end)
                end)
                ColorPicker.RGB.BInput.InputBox.FocusLost:connect(function()
                    rgbBoxes(ColorPicker.RGB.BInput.InputBox,"B")
                    pcall(function()ColorPickerSettings.Callback(Color3.fromHSV(h,s,v))end)
                end)

                RunService.RenderStepped:connect(function()
                    if mainDragging then 
                        local localX = math.clamp(mouse.X-Main.AbsolutePosition.X,0,Main.AbsoluteSize.X)
                        local localY = math.clamp(mouse.Y-Main.AbsolutePosition.Y,0,Main.AbsoluteSize.Y)
                        Main.MainPoint.Position = UDim2.new(0,localX-Main.MainPoint.AbsoluteSize.X/2,0,localY-Main.MainPoint.AbsoluteSize.Y/2)
                        s = localX / Main.AbsoluteSize.X
                        v = 1 - (localY / Main.AbsoluteSize.Y)
                        Display.BackgroundColor3 = Color3.fromHSV(h,s,v)
                        Main.MainPoint.ImageColor3 = Color3.fromHSV(h,s,v)
                        Background.BackgroundColor3 = Color3.fromHSV(h,1,1)
                        local color = Color3.fromHSV(h,s,v) 
                        local r,g,b = math.floor((color.R*255)+0.5),math.floor((color.G*255)+0.5),math.floor((color.B*255)+0.5)
                        ColorPicker.RGB.RInput.InputBox.Text = tostring(r)
                        ColorPicker.RGB.GInput.InputBox.Text = tostring(g)
                        ColorPicker.RGB.BInput.InputBox.Text = tostring(b)
                        ColorPicker.HexInput.InputBox.Text = string.format("#%02X%02X%02X",color.R*0xFF,color.G*0xFF,color.B*0xFF)
                        pcall(function()ColorPickerSettings.Callback(Color3.fromHSV(h,s,v))end)
                        ColorPickerSettings.Color = Color3.fromRGB(r,g,b)
                        if not ColorPickerSettings.Ext then
                            SaveConfiguration()
                        end
                    end
                    if sliderDragging then 
                        local localX = math.clamp(mouse.X-Slider.AbsolutePosition.X,0,Slider.AbsoluteSize.X)
                        h = localX / Slider.AbsoluteSize.X
                        Display.BackgroundColor3 = Color3.fromHSV(h,s,v)
                        Slider.SliderPoint.Position = UDim2.new(0,localX-Slider.SliderPoint.AbsoluteSize.X/2,0.5,0)
                        Slider.SliderPoint.ImageColor3 = Color3.fromHSV(h,1,1)
                        Background.BackgroundColor3 = Color3.fromHSV(h,1,1)
                        Main.MainPoint.ImageColor3 = Color3.fromHSV(h,s,v)
                        local color = Color3.fromHSV(h,s,v) 
                        local r,g,b = math.floor((color.R*255)+0.5),math.floor((color.G*255)+0.5),math.floor((color.B*255)+0.5)
                        ColorPicker.RGB.RInput.InputBox.Text = tostring(r)
                        ColorPicker.RGB.GInput.InputBox.Text = tostring(g)
                        ColorPicker.RGB.BInput.InputBox.Text = tostring(b)
                        ColorPicker.HexInput.InputBox.Text = string.format("#%02X%02X%02X",color.R*0xFF,color.G*0xFF,color.B*0xFF)
                        pcall(function()ColorPickerSettings.Callback(Color3.fromHSV(h,s,v))end)
                        ColorPickerSettings.Color = Color3.fromRGB(r,g,b)
                        if not ColorPickerSettings.Ext then
                            SaveConfiguration()
                        end
                    end
                end)

                if Settings.ConfigurationSaving then
                    if Settings.ConfigurationSaving.Enabled and ColorPickerSettings.Flag then
                        RayfieldLibrary.Flags[ColorPickerSettings.Flag] = ColorPickerSettings
                    end
                end

                function ColorPickerSettings:Set(RGBColor)
                    ColorPickerSettings.Color = RGBColor
                    h,s,v = ColorPickerSettings.Color:ToHSV()
                    color = Color3.fromHSV(h,s,v)
                    setDisplay()
                end

                ColorPicker.MouseEnter:Connect(function()
                    TweenService:Create(ColorPicker, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                end)

                ColorPicker.MouseLeave:Connect(function()
                    TweenService:Create(ColorPicker, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                end)

                Rayfield.Main:GetPropertyChangedSignal('BackgroundColor3'):Connect(function()
                    for _, rgbinput in ipairs(ColorPicker.RGB:GetChildren()) do
                        if rgbinput:IsA("Frame") then
                            rgbinput.BackgroundColor3 = SelectedTheme.InputBackground
                            rgbinput.UIStroke.Color = SelectedTheme.InputStroke
                        end
                    end

                    ColorPicker.HexInput.BackgroundColor3 = SelectedTheme.InputBackground
                    ColorPicker.HexInput.UIStroke.Color = SelectedTheme.InputStroke
                end)

                return ColorPickerSettings
            end

            -- Section
            function Tab:CreateSection(SectionName)

                local SectionValue = {}

                if SDone then
                    local SectionSpace = Elements.Template.SectionSpacing:Clone()
                    SectionSpace.Visible = true
                    SectionSpace.Parent = TabPage
                end

                local Section = Elements.Template.SectionTitle:Clone()
                Section.Title.Text = SectionName
                Section.Visible = true
                Section.Parent = TabPage

                Section.Title.TextTransparency = 1
                TweenService:Create(Section.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0.4}):Play()

                function SectionValue:Set(NewSection)
                    Section.Title.Text = NewSection
                end

                SDone = true

                return SectionValue
            end

            -- Divider
            function Tab:CreateDivider()
                local DividerValue = {}

                local Divider = Elements.Template.Divider:Clone()
                Divider.Visible = true
                Divider.Parent = TabPage

                Divider.Divider.BackgroundTransparency = 1
                TweenService:Create(Divider.Divider, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0.85}):Play()

                function DividerValue:Set(Value)
                    Divider.Visible = Value
                end

                return DividerValue
            end

            -- Label
            function Tab:CreateLabel(LabelText : string, Icon: number, Color : Color3, IgnoreTheme : boolean)
                local LabelValue = {}

                local Label = Elements.Template.Label:Clone()
                Label.Title.Text = LabelText
                Label.Visible = true
                Label.Parent = TabPage

                Label.BackgroundColor3 = Color or SelectedTheme.SecondaryElementBackground
                Label.UIStroke.Color = Color or SelectedTheme.SecondaryElementStroke

                if Icon then
                    if typeof(Icon) == 'string' and Icons then
                        local asset = getIcon(Icon)

                        Label.Icon.Image = 'rbxassetid://'..asset.id
                        Label.Icon.ImageRectOffset = asset.imageRectOffset
                        Label.Icon.ImageRectSize = asset.imageRectSize
                    else
                        Label.Icon.Image = getAssetUri(Icon)
                    end
                else
                    Label.Icon.Image = "rbxassetid://" .. 0
                end

                if Icon and Label:FindFirstChild('Icon') then
                    Label.Title.Position = UDim2.new(0, 45, 0.5, 0)
                    Label.Title.Size = UDim2.new(1, -100, 0, 14)

                    if Icon then
                        if typeof(Icon) == 'string' and Icons then
                            local asset = getIcon(Icon)

                            Label.Icon.Image = 'rbxassetid://'..asset.id
                            Label.Icon.ImageRectOffset = asset.imageRectOffset
                            Label.Icon.ImageRectSize = asset.imageRectSize
                        else
                            Label.Icon.Image = getAssetUri(Icon)
                        end
                    else
                        Label.Icon.Image = "rbxassetid://" .. 0
                    end

                    Label.Icon.Visible = true
                end

                Label.Icon.ImageTransparency = 1
                Label.BackgroundTransparency = 1
                Label.UIStroke.Transparency = 1
                Label.Title.TextTransparency = 1

                TweenService:Create(Label, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = Color and 0.8 or 0}):Play()
                TweenService:Create(Label.UIStroke, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Transparency = Color and 0.7 or 0}):Play()
                TweenService:Create(Label.Icon, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageTransparency = 0.2}):Play()
                TweenService:Create(Label.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = Color and 0.2 or 0}):Play()	

                function LabelValue:Set(NewLabel, Icon, Color)
                    Label.Title.Text = NewLabel

                    if Color then
                        Label.BackgroundColor3 = Color or SelectedTheme.SecondaryElementBackground
                        Label.UIStroke.Color = Color or SelectedTheme.SecondaryElementStroke
                    end

                    if Icon and Label:FindFirstChild('Icon') then
                        Label.Title.Position = UDim2.new(0, 45, 0.5, 0)
                        Label.Title.Size = UDim2.new(1, -100, 0, 14)

                        if Icon then
                            if typeof(Icon) == 'string' and Icons then
                                local asset = getIcon(Icon)

                                Label.Icon.Image = 'rbxassetid://'..asset.id
                                Label.Icon.ImageRectOffset = asset.imageRectOffset
                                Label.Icon.ImageRectSize = asset.imageRectSize
                            else
                                Label.Icon.Image = getAssetUri(Icon)
                            end
                        else
                            Label.Icon.Image = "rbxassetid://" .. 0
                        end

                        Label.Icon.Visible = true
                    end
                end

                Rayfield.Main:GetPropertyChangedSignal('BackgroundColor3'):Connect(function()
                    Label.BackgroundColor3 = IgnoreTheme and (Color or Label.BackgroundColor3) or SelectedTheme.SecondaryElementBackground
                    Label.UIStroke.Color = IgnoreTheme and (Color or Label.BackgroundColor3) or SelectedTheme.SecondaryElementStroke
                end)

                return LabelValue
            end

            -- Paragraph
            function Tab:CreateParagraph(ParagraphSettings)
                local ParagraphValue = {}

                local Paragraph = Elements.Template.Paragraph:Clone()
                Paragraph.Title.Text = ParagraphSettings.Title
                Paragraph.Content.Text = ParagraphSettings.Content
                Paragraph.Visible = true
                Paragraph.Parent = TabPage

                Paragraph.BackgroundTransparency = 1
                Paragraph.UIStroke.Transparency = 1
                Paragraph.Title.TextTransparency = 1
                Paragraph.Content.TextTransparency = 1

                Paragraph.BackgroundColor3 = SelectedTheme.SecondaryElementBackground
                Paragraph.UIStroke.Color = SelectedTheme.SecondaryElementStroke

                TweenService:Create(Paragraph, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                TweenService:Create(Paragraph.UIStroke, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                TweenService:Create(Paragraph.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()	
                TweenService:Create(Paragraph.Content, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()	

                function ParagraphValue:Set(NewParagraphSettings)
                    Paragraph.Title.Text = NewParagraphSettings.Title
                    Paragraph.Content.Text = NewParagraphSettings.Content
                end

                Rayfield.Main:GetPropertyChangedSignal('BackgroundColor3'):Connect(function()
                    Paragraph.BackgroundColor3 = SelectedTheme.SecondaryElementBackground
                    Paragraph.UIStroke.Color = SelectedTheme.SecondaryElementStroke
                end)

                return ParagraphValue
            end

            -- Input
            function Tab:CreateInput(InputSettings)
                local Input = Elements.Template.Input:Clone()
                Input.Name = InputSettings.Name
                Input.Title.Text = InputSettings.Name
                Input.Visible = true
                Input.Parent = TabPage

                Input.BackgroundTransparency = 1
                Input.UIStroke.Transparency = 1
                Input.Title.TextTransparency = 1

                Input.InputFrame.InputBox.Text = InputSettings.CurrentValue or ''

                Input.InputFrame.BackgroundColor3 = SelectedTheme.InputBackground
                Input.InputFrame.UIStroke.Color = SelectedTheme.InputStroke

                TweenService:Create(Input, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                TweenService:Create(Input.UIStroke, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                TweenService:Create(Input.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()	

                Input.InputFrame.InputBox.PlaceholderText = InputSettings.PlaceholderText
                Input.InputFrame.Size = UDim2.new(0, Input.InputFrame.InputBox.TextBounds.X + 24, 0, 30)

                Input.InputFrame.InputBox.FocusLost:Connect(function()
                    local Success, Response = pcall(function()
                        InputSettings.Callback(Input.InputFrame.InputBox.Text)
                        InputSettings.CurrentValue = Input.InputFrame.InputBox.Text
                    end)

                    if not Success then
                        TweenService:Create(Input, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = Color3.fromRGB(85, 0, 0)}):Play()
                        TweenService:Create(Input.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        Input.Title.Text = "Callback Error"
                        print("Rayfield | "..InputSettings.Name.." Callback Error " ..tostring(Response))
                        warn('Check docs.sirius.menu for help with Rayfield specific development.')
                        task.wait(0.5)
                        Input.Title.Text = InputSettings.Name
                        TweenService:Create(Input, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                        TweenService:Create(Input.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                    end

                    if InputSettings.RemoveTextAfterFocusLost then
                        Input.InputFrame.InputBox.Text = ""
                    end

                    if not InputSettings.Ext then
                        SaveConfiguration()
                    end
                end)

                Input.MouseEnter:Connect(function()
                    TweenService:Create(Input, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                end)

                Input.MouseLeave:Connect(function()
                    TweenService:Create(Input, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                end)

                Input.InputFrame.InputBox:GetPropertyChangedSignal("Text"):Connect(function()
                    TweenService:Create(Input.InputFrame, TweenInfo.new(0.55, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Size = UDim2.new(0, Input.InputFrame.InputBox.TextBounds.X + 24, 0, 30)}):Play()
                end)

                function InputSettings:Set(text)
                    Input.InputFrame.InputBox.Text = text
                    InputSettings.CurrentValue = text

                    local Success, Response = pcall(function()
                        InputSettings.Callback(text)
                    end)

                    if not InputSettings.Ext then
                        SaveConfiguration()
                    end
                end

                if Settings.ConfigurationSaving then
                    if Settings.ConfigurationSaving.Enabled and InputSettings.Flag then
                        RayfieldLibrary.Flags[InputSettings.Flag] = InputSettings
                    end
                end

                Rayfield.Main:GetPropertyChangedSignal('BackgroundColor3'):Connect(function()
                    Input.InputFrame.BackgroundColor3 = SelectedTheme.InputBackground
                    Input.InputFrame.UIStroke.Color = SelectedTheme.InputStroke
                end)

                return InputSettings
            end

            -- Dropdown
            function Tab:CreateDropdown(DropdownSettings)
                local Dropdown = Elements.Template.Dropdown:Clone()
                if string.find(DropdownSettings.Name,"closed") then
                    Dropdown.Name = "Dropdown"
                else
                    Dropdown.Name = DropdownSettings.Name
                end
                Dropdown.Title.Text = DropdownSettings.Name
                Dropdown.Visible = true
                Dropdown.Parent = TabPage

                Dropdown.List.Visible = false
                if DropdownSettings.CurrentOption then
                    if type(DropdownSettings.CurrentOption) == "string" then
                        DropdownSettings.CurrentOption = {DropdownSettings.CurrentOption}
                    end
                    if not DropdownSettings.MultipleOptions and type(DropdownSettings.CurrentOption) == "table" then
                        DropdownSettings.CurrentOption = {DropdownSettings.CurrentOption[1]}
                    end
                else
                    DropdownSettings.CurrentOption = {}
                end

                if DropdownSettings.MultipleOptions then
                    if DropdownSettings.CurrentOption and type(DropdownSettings.CurrentOption) == "table" then
                        if #DropdownSettings.CurrentOption == 1 then
                            Dropdown.Selected.Text = DropdownSettings.CurrentOption[1]
                        elseif #DropdownSettings.CurrentOption == 0 then
                            Dropdown.Selected.Text = "None"
                        else
                            Dropdown.Selected.Text = "Various"
                        end
                    else
                        DropdownSettings.CurrentOption = {}
                        Dropdown.Selected.Text = "None"
                    end
                else
                    Dropdown.Selected.Text = DropdownSettings.CurrentOption[1] or "None"
                end

                Dropdown.Toggle.ImageColor3 = SelectedTheme.TextColor
                TweenService:Create(Dropdown, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()

                Dropdown.BackgroundTransparency = 1
                Dropdown.UIStroke.Transparency = 1
                Dropdown.Title.TextTransparency = 1

                Dropdown.Size = UDim2.new(1, -10, 0, 45)

                TweenService:Create(Dropdown, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                TweenService:Create(Dropdown.UIStroke, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                TweenService:Create(Dropdown.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()	

                for _, ununusedoption in ipairs(Dropdown.List:GetChildren()) do
                    if ununusedoption.ClassName == "Frame" and ununusedoption.Name ~= "Placeholder" then
                        ununusedoption:Destroy()
                    end
                end

                Dropdown.Toggle.Rotation = 180

                Dropdown.Interact.MouseButton1Click:Connect(function()
                    TweenService:Create(Dropdown, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                    TweenService:Create(Dropdown.UIStroke, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                    task.wait(0.1)
                    TweenService:Create(Dropdown, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                    TweenService:Create(Dropdown.UIStroke, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                    if Debounce then return end
                    if Dropdown.List.Visible then
                        Debounce = true
                        TweenService:Create(Dropdown, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = UDim2.new(1, -10, 0, 45)}):Play()
                        for _, DropdownOpt in ipairs(Dropdown.List:GetChildren()) do
                            if DropdownOpt.ClassName == "Frame" and DropdownOpt.Name ~= "Placeholder" then
                                TweenService:Create(DropdownOpt, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                                TweenService:Create(DropdownOpt.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                                TweenService:Create(DropdownOpt.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                            end
                        end
                        TweenService:Create(Dropdown.List, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ScrollBarImageTransparency = 1}):Play()
                        TweenService:Create(Dropdown.Toggle, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Rotation = 180}):Play()	
                        task.wait(0.35)
                        Dropdown.List.Visible = false
                        Debounce = false
                    else
                        TweenService:Create(Dropdown, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = UDim2.new(1, -10, 0, 180)}):Play()
                        Dropdown.List.Visible = true
                        TweenService:Create(Dropdown.List, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ScrollBarImageTransparency = 0.7}):Play()
                        TweenService:Create(Dropdown.Toggle, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Rotation = 0}):Play()	
                        for _, DropdownOpt in ipairs(Dropdown.List:GetChildren()) do
                            if DropdownOpt.ClassName == "Frame" and DropdownOpt.Name ~= "Placeholder" then
                                if DropdownOpt.Name ~= Dropdown.Selected.Text then
                                    TweenService:Create(DropdownOpt.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                                end
                                TweenService:Create(DropdownOpt, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                                TweenService:Create(DropdownOpt.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
                            end
                        end
                    end
                end)

                Dropdown.MouseEnter:Connect(function()
                    if not Dropdown.List.Visible then
                        TweenService:Create(Dropdown, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                    end
                end)

                Dropdown.MouseLeave:Connect(function()
                    TweenService:Create(Dropdown, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                end)

                local function SetDropdownOptions()
                    for _, Option in ipairs(DropdownSettings.Options) do
                        local DropdownOption = Elements.Template.Dropdown.List.Template:Clone()
                        DropdownOption.Name = Option
                        DropdownOption.Title.Text = Option
                        DropdownOption.Parent = Dropdown.List
                        DropdownOption.Visible = true

                        DropdownOption.BackgroundTransparency = 1
                        DropdownOption.UIStroke.Transparency = 1
                        DropdownOption.Title.TextTransparency = 1

                        DropdownOption.Interact.ZIndex = 50
                        DropdownOption.Interact.MouseButton1Click:Connect(function()
                            if not DropdownSettings.MultipleOptions and table.find(DropdownSettings.CurrentOption, Option) then 
                                return
                            end

                            if table.find(DropdownSettings.CurrentOption, Option) then
                                table.remove(DropdownSettings.CurrentOption, table.find(DropdownSettings.CurrentOption, Option))
                                if DropdownSettings.MultipleOptions then
                                    if #DropdownSettings.CurrentOption == 1 then
                                        Dropdown.Selected.Text = DropdownSettings.CurrentOption[1]
                                    elseif #DropdownSettings.CurrentOption == 0 then
                                        Dropdown.Selected.Text = "None"
                                    else
                                        Dropdown.Selected.Text = "Various"
                                    end
                                else
                                    Dropdown.Selected.Text = DropdownSettings.CurrentOption[1]
                                end
                            else
                                if not DropdownSettings.MultipleOptions then
                                    table.clear(DropdownSettings.CurrentOption)
                                end
                                table.insert(DropdownSettings.CurrentOption, Option)
                                if DropdownSettings.MultipleOptions then
                                    if #DropdownSettings.CurrentOption == 1 then
                                        Dropdown.Selected.Text = DropdownSettings.CurrentOption[1]
                                    elseif #DropdownSettings.CurrentOption == 0 then
                                        Dropdown.Selected.Text = "None"
                                    else
                                        Dropdown.Selected.Text = "Various"
                                    end
                                else
                                    Dropdown.Selected.Text = DropdownSettings.CurrentOption[1]
                                end
                                TweenService:Create(DropdownOption.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                                TweenService:Create(DropdownOption, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.DropdownSelected}):Play()
                                Debounce = true
                            end


                            local Success, Response = pcall(function()
                                DropdownSettings.Callback(DropdownSettings.CurrentOption)
                            end)

                            if not Success then
                                TweenService:Create(Dropdown, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = Color3.fromRGB(85, 0, 0)}):Play()
                                TweenService:Create(Dropdown.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                                Dropdown.Title.Text = "Callback Error"
                                print("Rayfield | "..DropdownSettings.Name.." Callback Error " ..tostring(Response))
                                warn('Check docs.sirius.menu for help with Rayfield specific development.')
                                task.wait(0.5)
                                Dropdown.Title.Text = DropdownSettings.Name
                                TweenService:Create(Dropdown, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                                TweenService:Create(Dropdown.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                            end

                            for _, droption in ipairs(Dropdown.List:GetChildren()) do
                                if droption.ClassName == "Frame" and droption.Name ~= "Placeholder" and not table.find(DropdownSettings.CurrentOption, droption.Name) then
                                    TweenService:Create(droption, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.DropdownUnselected}):Play()
                                end
                            end
                            if not DropdownSettings.MultipleOptions then
                                task.wait(0.1)
                                TweenService:Create(Dropdown, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = UDim2.new(1, -10, 0, 45)}):Play()
                                for _, DropdownOpt in ipairs(Dropdown.List:GetChildren()) do
                                    if DropdownOpt.ClassName == "Frame" and DropdownOpt.Name ~= "Placeholder" then
                                        TweenService:Create(DropdownOpt, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                                        TweenService:Create(DropdownOpt.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                                        TweenService:Create(DropdownOpt.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                                    end
                                end
                                TweenService:Create(Dropdown.List, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ScrollBarImageTransparency = 1}):Play()
                                TweenService:Create(Dropdown.Toggle, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Rotation = 180}):Play()	
                                task.wait(0.35)
                                Dropdown.List.Visible = false
                            end
                            Debounce = false
                            if not DropdownSettings.Ext then
                                SaveConfiguration()
                            end
                        end)

                        Rayfield.Main:GetPropertyChangedSignal('BackgroundColor3'):Connect(function()
                            DropdownOption.UIStroke.Color = SelectedTheme.ElementStroke
                        end)
                    end
                end
                SetDropdownOptions()

                for _, droption in ipairs(Dropdown.List:GetChildren()) do
                    if droption.ClassName == "Frame" and droption.Name ~= "Placeholder" then
                        if not table.find(DropdownSettings.CurrentOption, droption.Name) then
                            droption.BackgroundColor3 = SelectedTheme.DropdownUnselected
                        else
                            droption.BackgroundColor3 = SelectedTheme.DropdownSelected
                        end

                        Rayfield.Main:GetPropertyChangedSignal('BackgroundColor3'):Connect(function()
                            if not table.find(DropdownSettings.CurrentOption, droption.Name) then
                                droption.BackgroundColor3 = SelectedTheme.DropdownUnselected
                            else
                                droption.BackgroundColor3 = SelectedTheme.DropdownSelected
                            end
                        end)
                    end
                end

                function DropdownSettings:Set(NewOption)
                    DropdownSettings.CurrentOption = NewOption

                    if typeof(DropdownSettings.CurrentOption) == "string" then
                        DropdownSettings.CurrentOption = {DropdownSettings.CurrentOption}
                    end

                    if not DropdownSettings.MultipleOptions then
                        DropdownSettings.CurrentOption = {DropdownSettings.CurrentOption[1]}
                    end

                    if DropdownSettings.MultipleOptions then
                        if #DropdownSettings.CurrentOption == 1 then
                            Dropdown.Selected.Text = DropdownSettings.CurrentOption[1]
                        elseif #DropdownSettings.CurrentOption == 0 then
                            Dropdown.Selected.Text = "None"
                        else
                            Dropdown.Selected.Text = "Various"
                        end
                    else
                        Dropdown.Selected.Text = DropdownSettings.CurrentOption[1]
                    end


                    local Success, Response = pcall(function()
                        DropdownSettings.Callback(NewOption)
                    end)
                    if not Success then
                        TweenService:Create(Dropdown, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = Color3.fromRGB(85, 0, 0)}):Play()
                        TweenService:Create(Dropdown.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        Dropdown.Title.Text = "Callback Error"
                        print("Rayfield | "..DropdownSettings.Name.." Callback Error " ..tostring(Response))
                        warn('Check docs.sirius.menu for help with Rayfield specific development.')
                        task.wait(0.5)
                        Dropdown.Title.Text = DropdownSettings.Name
                        TweenService:Create(Dropdown, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                        TweenService:Create(Dropdown.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                    end

                    for _, droption in ipairs(Dropdown.List:GetChildren()) do
                        if droption.ClassName == "Frame" and droption.Name ~= "Placeholder" then
                            if not table.find(DropdownSettings.CurrentOption, droption.Name) then
                                droption.BackgroundColor3 = SelectedTheme.DropdownUnselected
                            else
                                droption.BackgroundColor3 = SelectedTheme.DropdownSelected
                            end
                        end
                    end
                    --SaveConfiguration()
                end

                function DropdownSettings:Refresh(optionsTable: table) -- updates a dropdown with new options from optionsTable
                    DropdownSettings.Options = optionsTable
                    for _, option in Dropdown.List:GetChildren() do
                        if option.ClassName == "Frame" and option.Name ~= "Placeholder" then
                            option:Destroy()
                        end
                    end
                    SetDropdownOptions()
                end

                if Settings.ConfigurationSaving then
                    if Settings.ConfigurationSaving.Enabled and DropdownSettings.Flag then
                        RayfieldLibrary.Flags[DropdownSettings.Flag] = DropdownSettings
                    end
                end

                Rayfield.Main:GetPropertyChangedSignal('BackgroundColor3'):Connect(function()
                    Dropdown.Toggle.ImageColor3 = SelectedTheme.TextColor
                    TweenService:Create(Dropdown, TweenInfo.new(0.4, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                end)

                return DropdownSettings
            end

            -- Keybind
            function Tab:CreateKeybind(KeybindSettings)
                local CheckingForKey = false
                local Keybind = Elements.Template.Keybind:Clone()
                Keybind.Name = KeybindSettings.Name
                Keybind.Title.Text = KeybindSettings.Name
                Keybind.Visible = true
                Keybind.Parent = TabPage

                Keybind.BackgroundTransparency = 1
                Keybind.UIStroke.Transparency = 1
                Keybind.Title.TextTransparency = 1

                Keybind.KeybindFrame.BackgroundColor3 = SelectedTheme.InputBackground
                Keybind.KeybindFrame.UIStroke.Color = SelectedTheme.InputStroke

                TweenService:Create(Keybind, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                TweenService:Create(Keybind.UIStroke, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                TweenService:Create(Keybind.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()	

                Keybind.KeybindFrame.KeybindBox.Text = KeybindSettings.CurrentKeybind
                Keybind.KeybindFrame.Size = UDim2.new(0, Keybind.KeybindFrame.KeybindBox.TextBounds.X + 24, 0, 30)

                Keybind.KeybindFrame.KeybindBox.Focused:Connect(function()
                    CheckingForKey = true
                    Keybind.KeybindFrame.KeybindBox.Text = ""
                end)
                Keybind.KeybindFrame.KeybindBox.FocusLost:Connect(function()
                    CheckingForKey = false
                    if Keybind.KeybindFrame.KeybindBox.Text == nil or Keybind.KeybindFrame.KeybindBox.Text == "" then
                        Keybind.KeybindFrame.KeybindBox.Text = KeybindSettings.CurrentKeybind
                        if not KeybindSettings.Ext then
                            SaveConfiguration()
                        end
                    end
                end)

                Keybind.MouseEnter:Connect(function()
                    TweenService:Create(Keybind, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                end)

                Keybind.MouseLeave:Connect(function()
                    TweenService:Create(Keybind, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                end)

                UserInputService.InputBegan:Connect(function(input, processed)
                    if CheckingForKey then
                        if input.KeyCode ~= Enum.KeyCode.Unknown then
                            local SplitMessage = string.split(tostring(input.KeyCode), ".")
                            local NewKeyNoEnum = SplitMessage[3]
                            Keybind.KeybindFrame.KeybindBox.Text = tostring(NewKeyNoEnum)
                            KeybindSettings.CurrentKeybind = tostring(NewKeyNoEnum)
                            Keybind.KeybindFrame.KeybindBox:ReleaseFocus()
                            if not KeybindSettings.Ext then
                                SaveConfiguration()
                            end

                            if KeybindSettings.CallOnChange then
                                KeybindSettings.Callback(tostring(NewKeyNoEnum))
                            end
                        end
                    elseif not KeybindSettings.CallOnChange and KeybindSettings.CurrentKeybind ~= nil and (input.KeyCode == Enum.KeyCode[KeybindSettings.CurrentKeybind] and not processed) then -- Test
                        local Held = true
                        local Connection
                        Connection = input.Changed:Connect(function(prop)
                            if prop == "UserInputState" then
                                Connection:Disconnect()
                                Held = false
                            end
                        end)

                        if not KeybindSettings.HoldToInteract then
                            local Success, Response = pcall(KeybindSettings.Callback)
                            if not Success then
                                TweenService:Create(Keybind, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = Color3.fromRGB(85, 0, 0)}):Play()
                                TweenService:Create(Keybind.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                                Keybind.Title.Text = "Callback Error"
                                print("Rayfield | "..KeybindSettings.Name.." Callback Error " ..tostring(Response))
                                warn('Check docs.sirius.menu for help with Rayfield specific development.')
                                task.wait(0.5)
                                Keybind.Title.Text = KeybindSettings.Name
                                TweenService:Create(Keybind, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                                TweenService:Create(Keybind.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                            end
                        else
                            task.wait(0.25)
                            if Held then
                                local Loop; Loop = RunService.Stepped:Connect(function()
                                    if not Held then
                                        KeybindSettings.Callback(false) -- maybe pcall this
                                        Loop:Disconnect()
                                    else
                                        KeybindSettings.Callback(true) -- maybe pcall this
                                    end
                                end)
                            end
                        end
                    end
                end)

                Keybind.KeybindFrame.KeybindBox:GetPropertyChangedSignal("Text"):Connect(function()
                    TweenService:Create(Keybind.KeybindFrame, TweenInfo.new(0.55, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Size = UDim2.new(0, Keybind.KeybindFrame.KeybindBox.TextBounds.X + 24, 0, 30)}):Play()
                end)

                function KeybindSettings:Set(NewKeybind)
                    Keybind.KeybindFrame.KeybindBox.Text = tostring(NewKeybind)
                    KeybindSettings.CurrentKeybind = tostring(NewKeybind)
                    Keybind.KeybindFrame.KeybindBox:ReleaseFocus()
                    if not KeybindSettings.Ext then
                        SaveConfiguration()
                    end

                    if KeybindSettings.CallOnChange then
                        KeybindSettings.Callback(tostring(NewKeybind))
                    end
                end

                if Settings.ConfigurationSaving then
                    if Settings.ConfigurationSaving.Enabled and KeybindSettings.Flag then
                        RayfieldLibrary.Flags[KeybindSettings.Flag] = KeybindSettings
                    end
                end

                Rayfield.Main:GetPropertyChangedSignal('BackgroundColor3'):Connect(function()
                    Keybind.KeybindFrame.BackgroundColor3 = SelectedTheme.InputBackground
                    Keybind.KeybindFrame.UIStroke.Color = SelectedTheme.InputStroke
                end)

                return KeybindSettings
            end

            -- Toggle
            function Tab:CreateToggle(ToggleSettings)
                local ToggleValue = {}

                local Toggle = Elements.Template.Toggle:Clone()
                Toggle.Name = ToggleSettings.Name
                Toggle.Title.Text = ToggleSettings.Name
                Toggle.Visible = true
                Toggle.Parent = TabPage

                Toggle.BackgroundTransparency = 1
                Toggle.UIStroke.Transparency = 1
                Toggle.Title.TextTransparency = 1
                Toggle.Switch.BackgroundColor3 = SelectedTheme.ToggleBackground

                if SelectedTheme ~= RayfieldLibrary.Theme.Default then
                    Toggle.Switch.Shadow.Visible = false
                end

                TweenService:Create(Toggle, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                TweenService:Create(Toggle.UIStroke, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                TweenService:Create(Toggle.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()	

                if ToggleSettings.CurrentValue == true then
                    Toggle.Switch.Indicator.Position = UDim2.new(1, -20, 0.5, 0)
                    Toggle.Switch.Indicator.UIStroke.Color = SelectedTheme.ToggleEnabledStroke
                    Toggle.Switch.Indicator.BackgroundColor3 = SelectedTheme.ToggleEnabled
                    Toggle.Switch.UIStroke.Color = SelectedTheme.ToggleEnabledOuterStroke
                else
                    Toggle.Switch.Indicator.Position = UDim2.new(1, -40, 0.5, 0)
                    Toggle.Switch.Indicator.UIStroke.Color = SelectedTheme.ToggleDisabledStroke
                    Toggle.Switch.Indicator.BackgroundColor3 = SelectedTheme.ToggleDisabled
                    Toggle.Switch.UIStroke.Color = SelectedTheme.ToggleDisabledOuterStroke
                end

                Toggle.MouseEnter:Connect(function()
                    TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                end)

                Toggle.MouseLeave:Connect(function()
                    TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                end)

                Toggle.Interact.MouseButton1Click:Connect(function()
                    if ToggleSettings.CurrentValue == true then
                        ToggleSettings.CurrentValue = false
                        TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                        TweenService:Create(Toggle.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        TweenService:Create(Toggle.Switch.Indicator, TweenInfo.new(0.45, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Position = UDim2.new(1, -40, 0.5, 0)}):Play()
                        TweenService:Create(Toggle.Switch.Indicator.UIStroke, TweenInfo.new(0.55, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Color = SelectedTheme.ToggleDisabledStroke}):Play()
                        TweenService:Create(Toggle.Switch.Indicator, TweenInfo.new(0.8, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {BackgroundColor3 = SelectedTheme.ToggleDisabled}):Play()
                        TweenService:Create(Toggle.Switch.UIStroke, TweenInfo.new(0.55, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Color = SelectedTheme.ToggleDisabledOuterStroke}):Play()
                        TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                        TweenService:Create(Toggle.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()	
                    else
                        ToggleSettings.CurrentValue = true
                        TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                        TweenService:Create(Toggle.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        TweenService:Create(Toggle.Switch.Indicator, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Position = UDim2.new(1, -20, 0.5, 0)}):Play()
                        TweenService:Create(Toggle.Switch.Indicator.UIStroke, TweenInfo.new(0.55, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Color = SelectedTheme.ToggleEnabledStroke}):Play()
                        TweenService:Create(Toggle.Switch.Indicator, TweenInfo.new(0.8, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {BackgroundColor3 = SelectedTheme.ToggleEnabled}):Play()
                        TweenService:Create(Toggle.Switch.UIStroke, TweenInfo.new(0.55, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Color = SelectedTheme.ToggleEnabledOuterStroke}):Play()
                        TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                        TweenService:Create(Toggle.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()		
                    end

                    local Success, Response = pcall(function()
                        if debugX then warn('Running toggle \''..ToggleSettings.Name..'\' (Interact)') end

                        ToggleSettings.Callback(ToggleSettings.CurrentValue)
                    end)

                    if not Success then
                        TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = Color3.fromRGB(85, 0, 0)}):Play()
                        TweenService:Create(Toggle.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        Toggle.Title.Text = "Callback Error"
                        print("Rayfield | "..ToggleSettings.Name.." Callback Error " ..tostring(Response))
                        warn('Check docs.sirius.menu for help with Rayfield specific development.')
                        task.wait(0.5)
                        Toggle.Title.Text = ToggleSettings.Name
                        TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                        TweenService:Create(Toggle.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                    end

                    if not ToggleSettings.Ext then
                        SaveConfiguration()
                    end
                end)

                function ToggleSettings:Set(NewToggleValue)
                    if NewToggleValue == true then
                        ToggleSettings.CurrentValue = true
                        TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                        TweenService:Create(Toggle.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        TweenService:Create(Toggle.Switch.Indicator, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Position = UDim2.new(1, -20, 0.5, 0)}):Play()
                        TweenService:Create(Toggle.Switch.Indicator, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Size = UDim2.new(0,12,0,12)}):Play()
                        TweenService:Create(Toggle.Switch.Indicator.UIStroke, TweenInfo.new(0.55, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Color = SelectedTheme.ToggleEnabledStroke}):Play()
                        TweenService:Create(Toggle.Switch.Indicator, TweenInfo.new(0.8, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {BackgroundColor3 = SelectedTheme.ToggleEnabled}):Play()
                        TweenService:Create(Toggle.Switch.UIStroke, TweenInfo.new(0.55, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Color = SelectedTheme.ToggleEnabledOuterStroke}):Play()
                        TweenService:Create(Toggle.Switch.Indicator, TweenInfo.new(0.45, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Size = UDim2.new(0,17,0,17)}):Play()	
                        TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                        TweenService:Create(Toggle.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()	
                    else
                        ToggleSettings.CurrentValue = false
                        TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                        TweenService:Create(Toggle.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        TweenService:Create(Toggle.Switch.Indicator, TweenInfo.new(0.45, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Position = UDim2.new(1, -40, 0.5, 0)}):Play()
                        TweenService:Create(Toggle.Switch.Indicator, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Size = UDim2.new(0,12,0,12)}):Play()
                        TweenService:Create(Toggle.Switch.Indicator.UIStroke, TweenInfo.new(0.55, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Color = SelectedTheme.ToggleDisabledStroke}):Play()
                        TweenService:Create(Toggle.Switch.Indicator, TweenInfo.new(0.8, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {BackgroundColor3 = SelectedTheme.ToggleDisabled}):Play()
                        TweenService:Create(Toggle.Switch.UIStroke, TweenInfo.new(0.55, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Color = SelectedTheme.ToggleDisabledOuterStroke}):Play()
                        TweenService:Create(Toggle.Switch.Indicator, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Size = UDim2.new(0,17,0,17)}):Play()
                        TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                        TweenService:Create(Toggle.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()	
                    end

                    local Success, Response = pcall(function()
                        if debugX then warn('Running toggle \''..ToggleSettings.Name..'\' (:Set)') end

                        ToggleSettings.Callback(ToggleSettings.CurrentValue)
                    end)

                    if not Success then
                        TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = Color3.fromRGB(85, 0, 0)}):Play()
                        TweenService:Create(Toggle.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        Toggle.Title.Text = "Callback Error"
                        print("Rayfield | "..ToggleSettings.Name.." Callback Error " ..tostring(Response))
                        warn('Check docs.sirius.menu for help with Rayfield specific development.')
                        task.wait(0.5)
                        Toggle.Title.Text = ToggleSettings.Name
                        TweenService:Create(Toggle, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                        TweenService:Create(Toggle.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                    end

                    if not ToggleSettings.Ext then
                        SaveConfiguration()
                    end
                end

                if not ToggleSettings.Ext then
                    if Settings.ConfigurationSaving then
                        if Settings.ConfigurationSaving.Enabled and ToggleSettings.Flag then
                            RayfieldLibrary.Flags[ToggleSettings.Flag] = ToggleSettings
                        end
                    end
                end


                Rayfield.Main:GetPropertyChangedSignal('BackgroundColor3'):Connect(function()
                    Toggle.Switch.BackgroundColor3 = SelectedTheme.ToggleBackground

                    if SelectedTheme ~= RayfieldLibrary.Theme.Default then
                        Toggle.Switch.Shadow.Visible = false
                    end

                    task.wait()

                    if not ToggleSettings.CurrentValue then
                        Toggle.Switch.Indicator.UIStroke.Color = SelectedTheme.ToggleDisabledStroke
                        Toggle.Switch.Indicator.BackgroundColor3 = SelectedTheme.ToggleDisabled
                        Toggle.Switch.UIStroke.Color = SelectedTheme.ToggleDisabledOuterStroke
                    else
                        Toggle.Switch.Indicator.UIStroke.Color = SelectedTheme.ToggleEnabledStroke
                        Toggle.Switch.Indicator.BackgroundColor3 = SelectedTheme.ToggleEnabled
                        Toggle.Switch.UIStroke.Color = SelectedTheme.ToggleEnabledOuterStroke
                    end
                end)

                return ToggleSettings
            end

            -- Slider
            function Tab:CreateSlider(SliderSettings)
                local SLDragging = false
                local Slider = Elements.Template.Slider:Clone()
                Slider.Name = SliderSettings.Name
                Slider.Title.Text = SliderSettings.Name
                Slider.Visible = true
                Slider.Parent = TabPage

                Slider.BackgroundTransparency = 1
                Slider.UIStroke.Transparency = 1
                Slider.Title.TextTransparency = 1

                if SelectedTheme ~= RayfieldLibrary.Theme.Default then
                    Slider.Main.Shadow.Visible = false
                end

                Slider.Main.BackgroundColor3 = SelectedTheme.SliderBackground
                Slider.Main.UIStroke.Color = SelectedTheme.SliderStroke
                Slider.Main.Progress.UIStroke.Color = SelectedTheme.SliderStroke
                Slider.Main.Progress.BackgroundColor3 = SelectedTheme.SliderProgress

                TweenService:Create(Slider, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
                TweenService:Create(Slider.UIStroke, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                TweenService:Create(Slider.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()	

                Slider.Main.Progress.Size =	UDim2.new(0, Slider.Main.AbsoluteSize.X * ((SliderSettings.CurrentValue + SliderSettings.Range[1]) / (SliderSettings.Range[2] - SliderSettings.Range[1])) > 5 and Slider.Main.AbsoluteSize.X * (SliderSettings.CurrentValue / (SliderSettings.Range[2] - SliderSettings.Range[1])) or 5, 1, 0)

                if not SliderSettings.Suffix then
                    Slider.Main.Information.Text = tostring(SliderSettings.CurrentValue)
                else
                    Slider.Main.Information.Text = tostring(SliderSettings.CurrentValue) .. " " .. SliderSettings.Suffix
                end

                Slider.MouseEnter:Connect(function()
                    TweenService:Create(Slider, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackgroundHover}):Play()
                end)

                Slider.MouseLeave:Connect(function()
                    TweenService:Create(Slider, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                end)

                Slider.Main.Interact.InputBegan:Connect(function(Input)
                    if Input.UserInputType == Enum.UserInputType.MouseButton1 or Input.UserInputType == Enum.UserInputType.Touch then 
                        TweenService:Create(Slider.Main.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        TweenService:Create(Slider.Main.Progress.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        SLDragging = true 
                    end 
                end)

                Slider.Main.Interact.InputEnded:Connect(function(Input) 
                    if Input.UserInputType == Enum.UserInputType.MouseButton1 or Input.UserInputType == Enum.UserInputType.Touch then 
                        TweenService:Create(Slider.Main.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0.4}):Play()
                        TweenService:Create(Slider.Main.Progress.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0.3}):Play()
                        SLDragging = false 
                    end 
                end)

                Slider.Main.Interact.MouseButton1Down:Connect(function(X)
                    local Current = Slider.Main.Progress.AbsolutePosition.X + Slider.Main.Progress.AbsoluteSize.X
                    local Start = Current
                    local Location = X
                    local Loop; Loop = RunService.Stepped:Connect(function()
                        if SLDragging then
                            Location = UserInputService:GetMouseLocation().X
                            Current = Current + 0.025 * (Location - Start)

                            if Location < Slider.Main.AbsolutePosition.X then
                                Location = Slider.Main.AbsolutePosition.X
                            elseif Location > Slider.Main.AbsolutePosition.X + Slider.Main.AbsoluteSize.X then
                                Location = Slider.Main.AbsolutePosition.X + Slider.Main.AbsoluteSize.X
                            end

                            if Current < Slider.Main.AbsolutePosition.X + 5 then
                                Current = Slider.Main.AbsolutePosition.X + 5
                            elseif Current > Slider.Main.AbsolutePosition.X + Slider.Main.AbsoluteSize.X then
                                Current = Slider.Main.AbsolutePosition.X + Slider.Main.AbsoluteSize.X
                            end

                            if Current <= Location and (Location - Start) < 0 then
                                Start = Location
                            elseif Current >= Location and (Location - Start) > 0 then
                                Start = Location
                            end
                            TweenService:Create(Slider.Main.Progress, TweenInfo.new(0.45, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Size = UDim2.new(0, Current - Slider.Main.AbsolutePosition.X, 1, 0)}):Play()
                            local NewValue = SliderSettings.Range[1] + (Location - Slider.Main.AbsolutePosition.X) / Slider.Main.AbsoluteSize.X * (SliderSettings.Range[2] - SliderSettings.Range[1])

                            NewValue = math.floor(NewValue / SliderSettings.Increment + 0.5) * (SliderSettings.Increment * 10000000) / 10000000
                            NewValue = math.clamp(NewValue, SliderSettings.Range[1], SliderSettings.Range[2])

                            if not SliderSettings.Suffix then
                                Slider.Main.Information.Text = tostring(NewValue)
                            else
                                Slider.Main.Information.Text = tostring(NewValue) .. " " .. SliderSettings.Suffix
                            end

                            if SliderSettings.CurrentValue ~= NewValue then
                                local Success, Response = pcall(function()
                                    SliderSettings.Callback(NewValue)
                                end)
                                if not Success then
                                    TweenService:Create(Slider, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = Color3.fromRGB(85, 0, 0)}):Play()
                                    TweenService:Create(Slider.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                                    Slider.Title.Text = "Callback Error"
                                    print("Rayfield | "..SliderSettings.Name.." Callback Error " ..tostring(Response))
                                    warn('Check docs.sirius.menu for help with Rayfield specific development.')
                                    task.wait(0.5)
                                    Slider.Title.Text = SliderSettings.Name
                                    TweenService:Create(Slider, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                                    TweenService:Create(Slider.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                                end

                                SliderSettings.CurrentValue = NewValue
                                if not SliderSettings.Ext then
                                    SaveConfiguration()
                                end
                            end
                        else
                            TweenService:Create(Slider.Main.Progress, TweenInfo.new(0.3, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Size = UDim2.new(0, Location - Slider.Main.AbsolutePosition.X > 5 and Location - Slider.Main.AbsolutePosition.X or 5, 1, 0)}):Play()
                            Loop:Disconnect()
                        end
                    end)
                end)

                function SliderSettings:Set(NewVal)
                    local NewVal = math.clamp(NewVal, SliderSettings.Range[1], SliderSettings.Range[2])

                    TweenService:Create(Slider.Main.Progress, TweenInfo.new(0.45, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Size = UDim2.new(0, Slider.Main.AbsoluteSize.X * ((NewVal + SliderSettings.Range[1]) / (SliderSettings.Range[2] - SliderSettings.Range[1])) > 5 and Slider.Main.AbsoluteSize.X * (NewVal / (SliderSettings.Range[2] - SliderSettings.Range[1])) or 5, 1, 0)}):Play()
                    Slider.Main.Information.Text = tostring(NewVal) .. " " .. (SliderSettings.Suffix or "")

                    local Success, Response = pcall(function()
                        SliderSettings.Callback(NewVal)
                    end)

                    if not Success then
                        TweenService:Create(Slider, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = Color3.fromRGB(85, 0, 0)}):Play()
                        TweenService:Create(Slider.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                        Slider.Title.Text = "Callback Error"
                        print("Rayfield | "..SliderSettings.Name.." Callback Error " ..tostring(Response))
                        warn('Check docs.sirius.menu for help with Rayfield specific development.')
                        task.wait(0.5)
                        Slider.Title.Text = SliderSettings.Name
                        TweenService:Create(Slider, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.ElementBackground}):Play()
                        TweenService:Create(Slider.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {Transparency = 0}):Play()
                    end

                    SliderSettings.CurrentValue = NewVal
                    if not SliderSettings.Ext then
                        SaveConfiguration()
                    end
                end

                if Settings.ConfigurationSaving then
                    if Settings.ConfigurationSaving.Enabled and SliderSettings.Flag then
                        RayfieldLibrary.Flags[SliderSettings.Flag] = SliderSettings
                    end
                end

                Rayfield.Main:GetPropertyChangedSignal('BackgroundColor3'):Connect(function()
                    if SelectedTheme ~= RayfieldLibrary.Theme.Default then
                        Slider.Main.Shadow.Visible = false
                    end

                    Slider.Main.BackgroundColor3 = SelectedTheme.SliderBackground
                    Slider.Main.UIStroke.Color = SelectedTheme.SliderStroke
                    Slider.Main.Progress.UIStroke.Color = SelectedTheme.SliderStroke
                    Slider.Main.Progress.BackgroundColor3 = SelectedTheme.SliderProgress
                end)

                return SliderSettings
            end

            Rayfield.Main:GetPropertyChangedSignal('BackgroundColor3'):Connect(function()
                TabButton.UIStroke.Color = SelectedTheme.TabStroke

                if Elements.UIPageLayout.CurrentPage == TabPage then
                    TabButton.BackgroundColor3 = SelectedTheme.TabBackgroundSelected
                    TabButton.Image.ImageColor3 = SelectedTheme.SelectedTabTextColor
                    TabButton.Title.TextColor3 = SelectedTheme.SelectedTabTextColor
                else
                    TabButton.BackgroundColor3 = SelectedTheme.TabBackground
                    TabButton.Image.ImageColor3 = SelectedTheme.TabTextColor
                    TabButton.Title.TextColor3 = SelectedTheme.TabTextColor
                end
            end)

            return Tab
        end

        Elements.Visible = true


        task.wait(0.1)
        TweenService:Create(Main, TweenInfo.new(0.7, Enum.EasingStyle.Exponential, Enum.EasingDirection.InOut), {Size = UDim2.new(0, 390, 0, 90)}):Play()
        task.wait(0.3)
        TweenService:Create(LoadingFrame.Title, TweenInfo.new(0.2, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
        TweenService:Create(LoadingFrame.Subtitle, TweenInfo.new(0.2, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
        TweenService:Create(LoadingFrame.Version, TweenInfo.new(0.2, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
        task.wait(0.1)
        TweenService:Create(Main, TweenInfo.new(0.6, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Size = useMobileSizing and UDim2.new(0, 500, 0, 275) or UDim2.new(0, 500, 0, 475)}):Play()
        TweenService:Create(Main.Shadow.Image, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {ImageTransparency = 0.6}):Play()

        Topbar.BackgroundTransparency = 1
        Topbar.Divider.Size = UDim2.new(0, 0, 0, 1)
        Topbar.Divider.BackgroundColor3 = SelectedTheme.ElementStroke
        Topbar.CornerRepair.BackgroundTransparency = 1
        Topbar.Title.TextTransparency = 1
        Topbar.Title.RichText = true
        Topbar.Title.TextXAlignment = Enum.TextXAlignment.Center
        Topbar.Title.Size = UDim2.new(0, 466, 0, 30)
        Topbar.Title.TextColor3 = Color3.fromRGB(0, 120, 210)
        Topbar.Search.ImageTransparency = 1
        if Topbar:FindFirstChild('Settings') then
            Topbar.Settings.ImageTransparency = 1
        end
        Topbar.ChangeSize.ImageTransparency = 1
        Topbar.Hide.ImageTransparency = 1


        task.wait(0.5)
        Topbar.Visible = true
        TweenService:Create(Topbar, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
        TweenService:Create(Topbar.CornerRepair, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0}):Play()
        task.wait(0.1)
        TweenService:Create(Topbar.Divider, TweenInfo.new(1, Enum.EasingStyle.Exponential), {Size = UDim2.new(1, 0, 0, 1)}):Play()
        TweenService:Create(Topbar.Title, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {TextTransparency = 0}):Play()
        task.wait(0.05)
        TweenService:Create(Topbar.Search, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {ImageTransparency = 0.8}):Play()
        task.wait(0.05)
        if Topbar:FindFirstChild('Settings') then
            TweenService:Create(Topbar.Settings, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {ImageTransparency = 0.8}):Play()
            task.wait(0.05)
        end
        TweenService:Create(Topbar.ChangeSize, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {ImageTransparency = 0.8}):Play()
        task.wait(0.05)
        TweenService:Create(Topbar.Hide, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {ImageTransparency = 0.8}):Play()

        if dragBar then
            TweenService:Create(dragBarCosmetic, TweenInfo.new(0.6, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0.7}):Play()
        end

        function Window.ModifyTheme(NewTheme)
            local success = pcall(ChangeTheme, NewTheme)
            if not success then
                RayfieldLibrary:Notify({Title = 'Unable to Change Theme', Content = 'We are unable find a theme on file.', Image = 4400704299})
            else
                RayfieldLibrary:Notify({Title = 'Theme Changed', Content = 'Successfully changed theme to '..(typeof(NewTheme) == 'string' and NewTheme or 'Custom Theme')..'.', Image = 4483362748})
            end
        end

        local success, result = pcall(function()
            createSettings(Window)
        end)

        if not success then warn('Rayfield had an issue creating settings.') end

        return Window
    end

    local function setVisibility(visibility: boolean, notify: boolean?)
        if Debounce then return end
        if visibility then
            Hidden = false
            Unhide()
        else
            Hidden = true
            Hide(notify)
        end
    end

    function RayfieldLibrary:SetVisibility(visibility: boolean)
        setVisibility(visibility, false)
    end

    function RayfieldLibrary:IsVisible(): boolean
        return not Hidden
    end

    local hideHotkeyConnection -- Has to be initialized here since the connection is made later in the script
    function RayfieldLibrary:Destroy()
        rayfieldDestroyed = true
        hideHotkeyConnection:Disconnect()
        Rayfield:Destroy()
    end

    Topbar.ChangeSize.MouseButton1Click:Connect(function()
        if Debounce then return end
        if Minimised then
            Minimised = false
            Maximise()
        else
            Minimised = true
            Minimise()
        end
    end)

    Main.Search.Input:GetPropertyChangedSignal('Text'):Connect(function()
        if #Main.Search.Input.Text > 0 then
            if not Elements.UIPageLayout.CurrentPage:FindFirstChild('SearchTitle-fsefsefesfsefesfesfThanks') then 
                local searchTitle = Elements.Template.SectionTitle:Clone()
                searchTitle.Parent = Elements.UIPageLayout.CurrentPage
                searchTitle.Name = 'SearchTitle-fsefsefesfsefesfesfThanks'
                searchTitle.LayoutOrder = -100
                searchTitle.Title.Text = "Results from '"..Elements.UIPageLayout.CurrentPage.Name.."'"
                searchTitle.Visible = true
            end
        else
            local searchTitle = Elements.UIPageLayout.CurrentPage:FindFirstChild('SearchTitle-fsefsefesfsefesfesfThanks')

            if searchTitle then
                searchTitle:Destroy()
            end
        end

        for _, element in ipairs(Elements.UIPageLayout.CurrentPage:GetChildren()) do
            if element.ClassName ~= 'UIListLayout' and element.Name ~= 'Placeholder' and element.Name ~= 'SearchTitle-fsefsefesfsefesfesfThanks' then
                if element.Name == 'SectionTitle' then
                    if #Main.Search.Input.Text == 0 then
                        element.Visible = true
                    else
                        element.Visible = false
                    end
                else
                    if string.lower(element.Name):find(string.lower(Main.Search.Input.Text), 1, true) then
                        element.Visible = true
                    else
                        element.Visible = false
                    end
                end
            end
        end
    end)

    Main.Search.Input.FocusLost:Connect(function(enterPressed)
        if #Main.Search.Input.Text == 0 and searchOpen then
            task.wait(0.12)
            closeSearch()
        end
    end)

    Topbar.Search.MouseButton1Click:Connect(function()
        task.spawn(function()
            if searchOpen then
                closeSearch()
            else
                openSearch()
            end
        end)
    end)

    if Topbar:FindFirstChild('Settings') then
        Topbar.Settings.MouseButton1Click:Connect(function()
            task.spawn(function()
                for _, OtherTabButton in ipairs(TabList:GetChildren()) do
                    if OtherTabButton.Name ~= "Template" and OtherTabButton.ClassName == "Frame" and OtherTabButton ~= TabButton and OtherTabButton.Name ~= "Placeholder" then
                        TweenService:Create(OtherTabButton, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundColor3 = SelectedTheme.TabBackground}):Play()
                        TweenService:Create(OtherTabButton.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextColor3 = SelectedTheme.TabTextColor}):Play()
                        TweenService:Create(OtherTabButton.Image, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageColor3 = SelectedTheme.TabTextColor}):Play()
                        TweenService:Create(OtherTabButton, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0.7}):Play()
                        TweenService:Create(OtherTabButton.Title, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {TextTransparency = 0.2}):Play()
                        TweenService:Create(OtherTabButton.Image, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageTransparency = 0.2}):Play()
                        TweenService:Create(OtherTabButton.UIStroke, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {Transparency = 0.5}):Play()
                    end
                end

                Elements.UIPageLayout:JumpTo(Elements['Rayfield Settings'])
            end)
        end)

    end


    Topbar.Hide.MouseButton1Click:Connect(function()
        setVisibility(Hidden, not useMobileSizing)
    end)

    hideHotkeyConnection = UserInputService.InputBegan:Connect(function(input, processed)
        if (input.KeyCode == Enum.KeyCode[getSetting("General", "rayfieldOpen")]) and not processed then
            if Debounce then return end
            local GUI = game.Players.LocalPlayer.PlayerGui:FindFirstChild("INIT GUI")
            if (not GUI or (GUI and not GUI.GucciFrame.GucciNotify.Visible)) then
                if Hidden then
                    Hidden = false
                    Unhide()
                else
                    Hidden = true
                    Hide()
                end
            end
        end
    end)

    if MPrompt then
        MPrompt.Interact.MouseButton1Click:Connect(function()
            if Debounce then return end
            if Hidden then
                Hidden = false
                Unhide()
            end
        end)
    end

    for _, TopbarButton in ipairs(Topbar:GetChildren()) do
        if TopbarButton.ClassName == "ImageButton" and TopbarButton.Name ~= 'Icon' then
            TopbarButton.MouseEnter:Connect(function()
                TweenService:Create(TopbarButton, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageTransparency = 0}):Play()
            end)

            TopbarButton.MouseLeave:Connect(function()
                TweenService:Create(TopbarButton, TweenInfo.new(0.7, Enum.EasingStyle.Exponential), {ImageTransparency = 0.8}):Play()
            end)
        end
    end


    function RayfieldLibrary:LoadConfiguration()
        local config

        if debugX then
            warn('Loading Configuration')
        end

        if useStudio then
            config = [[{"Toggle1adwawd":true,"ColorPicker1awd":{"B":255,"G":255,"R":255},"Slider1dawd":100,"ColorPicfsefker1":{"B":255,"G":255,"R":255},"Slidefefsr1":80,"dawdawd":"","Input1":"hh","Keybind1":"B","Dropdown1":["Ocean"]}]]
        end

        if CEnabled then
            local notified
            local loaded

            local success, result = pcall(function()
                if useStudio and config then
                    loaded = LoadConfiguration(config)
                    return
                end

                if isfile then 
                    if isfile(ConfigurationFolder .. "/" .. CFileName .. ConfigurationExtension) then
                        loaded = LoadConfiguration(readfile(ConfigurationFolder .. "/" .. CFileName .. ConfigurationExtension))
                    end
                else
                    notified = true
                    RayfieldLibrary:Notify({Title = "Rayfield Configurations", Content = "We couldn't enable Configuration Saving as you are not using software with filesystem support.", Image = 4384402990})
                end
            end)

            if success and loaded and not notified then
                RayfieldLibrary:Notify({Title = "Rayfield Configurations", Content = "The configuration file for this script has been loaded from a previous session.", Image = 4384403532})
            elseif not success and not notified then
                warn('Rayfield Configurations Error | '..tostring(result))
                RayfieldLibrary:Notify({Title = "Rayfield Configurations", Content = "We've encountered an issue loading your configuration correctly.\n\nCheck the Developer Console for more information.", Image = 4384402990})
            end
        end

        globalLoaded = true
    end

    if CEnabled and Main:FindFirstChild('Notice') then
        Main.Notice.BackgroundTransparency = 1
        Main.Notice.Title.TextTransparency = 1
        Main.Notice.Size = UDim2.new(0, 0, 0, 0)
        Main.Notice.Position = UDim2.new(0.5, 0, 0, -100)
        Main.Notice.Visible = true


        TweenService:Create(Main.Notice, TweenInfo.new(0.5, Enum.EasingStyle.Exponential, Enum.EasingDirection.InOut), {Size = UDim2.new(0, 280, 0, 35), Position = UDim2.new(0.5, 0, 0, -50), BackgroundTransparency = 0.5}):Play()
        TweenService:Create(Main.Notice.Title, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 0.1}):Play()
    end

    -- task.delay(4, function()
        RayfieldLibrary.LoadConfiguration()
        if Main:FindFirstChild('Notice') and Main.Notice.Visible then
            TweenService:Create(Main.Notice, TweenInfo.new(0.5, Enum.EasingStyle.Exponential, Enum.EasingDirection.InOut), {Size = UDim2.new(0, 100, 0, 25), Position = UDim2.new(0.5, 0, 0, -100), BackgroundTransparency = 1}):Play()
            TweenService:Create(Main.Notice.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()

            task.wait(0.5)
            Main.Notice.Visible = false
        end
    -- end)

    if CoreGui:FindFirstChild("Rayfield") then
        CoreGui.Rayfield.Drag.Interact.Modal = true
    else
        if CoreGui:FindFirstChild("HUI") then
            CoreGui.HUI.Rayfield.Drag.Interact.Modal = true
        end
    end

    return RayfieldLibrary
end)()
local w = game:GetService("Workspace")
local rs = game:GetService("ReplicatedStorage")
local rrs = game:GetService("RobloxReplicatedStorage")
local UIS = game:GetService("UserInputService")
local Players = game:GetService("Players")
local me = Players.LocalPlayer
local Mouse = me:GetMouse()
local BackPack = w[me.Name .. 'SpawnedInToys']
local TargetPlayer = ""
local TargetPlayerView = "TargetPlayer : " .. TargetPlayer
local Limbs = {"Left Arm", "Left Leg", "Right Arm", "Right Leg"}
local BananaList = {}
local FireList = {}
local BlobList = {}
local Cons = {}
local ABFriends = {}
local GrabFlag = "Default"
local DGType = "BackPack"
local ClickerType = "Default"
local Strentype = "All"
local PCLDColor, PCLDTrans
local MassLessGrab = false
local NoLegSit = false
local sitJumpT = false
local invis = false
local antibanana = false
local Waiting = false
local RWaiting = false
local TryProcess = false
local TMass = 0
local timer = 0
local msgCount = 0
w.FallenPartsDestroyHeight = "nan"

local function FWC(Parent,Name,Time) return Parent:FindFirstChild(Name) or Parent:WaitForChild(Name,Time) end

-- local my = {
--     Cam = w.CurrentCamera,
--     CamPart = FWC(me.Character, "CamPart"),
--     Head = FWC(me.Character, "Head"),
--     HRP = FWC(me.Character, "HumanoidRootPart"),
--     Torso = FWC(me.Character, "Torso"),
--     Hum = FWC(me.Character, "Humanoid"),
--     LLeg = FWC(me.Character, "Left Leg",2),
--     RLeg = FWC(me.Character, "Right Leg",2)
-- }

-- setmetatable(my, {
--     __index = function(_, key)
--         return me[key]
--     end
-- })

local function Disc(Name)
	for i,v in Cons do
		if i:find(Name) then
			v:Disconnect()
			v = nil
		end
	end
end

local IUFO = w.Map.AlwaysHereTweenedObjects.InnerUFO.Object
local OUFO = w.Map.AlwaysHereTweenedObjects.OuterUFO.Object
if IUFO:FindFirstChild("ObjectModel") and IUFO:FindFirstChild("ObjectModel").Laser.Attachment:FindFirstChild("ElectricBuzz") then
	w.Map.AlwaysHereTweenedObjects.InnerUFO.Object.ObjectModel.Laser.Attachment.ElectricBuzz:Destroy()
end
if OUFO:FindFirstChild("ObjectModel") and OUFO:FindFirstChild("ObjectModel").Laser.Attachment:FindFirstChild("ElectricBuzz") then
	w.Map.AlwaysHereTweenedObjects.OuterUFO.Object.ObjectModel.Laser.Attachment.ElectricBuzz:Destroy()
end
for _,part in pairs(w.Map.FactoryIsland:GetDescendants()) do
	if part:IsA("BasePart") and part.Name == "PoisonHurtPart" then
		part.Name = "HP"
		part.Parent = w
		part.Size = Vector3.new(1, 1, 1)
		part.CFrame = CFrame.new(Vector3.new(10000,10000,10000))
	elseif part:IsA("BasePart") and part.Name == "PaintPlayerPart" then
		part.Parent = w
		part.Name = "AP"
		part.Size = Vector3.new(1, 1, 1)
		part.CFrame = CFrame.new(Vector3.new(10000,10000,10000))
	elseif part:IsA("BasePart") and part.Name == "ExtinguishPart" then
		part.Name = "AF"
		part.Parent = w
		part.Size = Vector3.new(1, 1, 1)
		part.Transparency = 1
		part.CFrame = CFrame.new(Vector3.new(10000,10000,10000))
		if part:FindFirstChild("Tex") then part.Tex:Destroy() end
	end
end
for _,part in pairs(w.Map.Hole:GetDescendants()) do
	if part:IsA("BasePart") and part.Name == "ExtinguishPart" then
		part.Name = "AF"
		part.Parent = w
		part.Size = Vector3.new(1, 1, 1)
		part.Transparency = 1
		part.CFrame = CFrame.new(Vector3.new(10000,10000,10000))
		if part:FindFirstChild("Tex") then part.Tex:Destroy() end
	end
end
for _,part in pairs(w.Map.AlwaysHereTweenedObjects:GetChildren()) do for _,prt in pairs(part.PathParts:GetChildren()) do prt:Destroy() end end

task.defer(function()
	local char = me.Character or me.CharacterAdded:wait()
	local hrp = char and FWC(char,"HumanoidRootPart",3)
	local Root = hrp and FWC(hrp,"RootAttachment",3)
	local CamPart = char and FWC(char,"CamPart",3)
	if Root and CamPart then Root.Parent = CamPart end
end)

FWC(me.Character, "Torso"):GetPropertyChangedSignal("Transparency"):Connect(function()
	if FWC(me.Character, "Torso").Transparency > 0 then
		task.wait(0.25)
		for _,v in pairs(me.Character:GetChildren()) do
			if v:IsA("BasePart") and v.Name ~= "CamPart" and v.Name ~= "HumanoidRootPart" then v.Transparency = 0 end
			if v:IsA("Accessory") and v.Name ~= "TypingKeyboardMyWorld" then for _,b in pairs(v:GetChildren()) do b.Transparency = 0 end end
		end
	end
end)

FWC(me.Character, "HumanoidRootPart"):GetPropertyChangedSignal("Massless"):Connect(function()
	local char = me.Character or me.CharacterAdded:wait()
	for _,v in pairs(char:GetChildren()) do
		if v:IsA("BasePart") then v.Massless = false end
	end
	local HRP = char and FWC(char, "HumanoidRootPart")
	if HRP.Massless then
		local hum = char and FWC(char, "Humanoid")
		local CamPart = char and FWC(char, "CamPart",2)
		local Head = char and FWC(char, "Head")
		while char and hum and HRP.Massless and Head.Massless do
			for _,v in pairs(char:GetChildren()) do
				if v:IsA("BasePart") then v.Massless = false; task.wait() end
			end
			if HRP:FindFirstChild("RootAttachment") and CamPart then HRP.RootAttachment.Parent = CamPart end
			if hum.Sit and not hum.SeatPart then hum.Sit = false end
			task.wait(0.1)
		end
	end
end)

local screenGui = Instance.new("ScreenGui")
local textLabel = Instance.new("TextLabel")
local CoolDown = Instance.new("TextLabel")
local invis = Instance.new("ColorCorrectionEffect", w.CurrentCamera)
local GucciFrame = Instance.new("Frame")
GucciFrame.Parent = screenGui
local GucciNotify = Instance.new("TextLabel")
GucciNotify.Parent = GucciFrame
local GucciCor = Instance.new("UICorner")
GucciCor.Parent = GucciNotify

local TextBox = Instance.new("TextLabel")
TextBox.Parent = GucciNotify
local TextStroke = Instance.new("UIStroke")
TextStroke.Parent = TextBox

local YesBox = Instance.new("TextLabel")
YesBox.Parent = GucciNotify
local YesCor = Instance.new("UICorner")
YesCor.Parent = YesBox
local YesStroke = Instance.new("UIStroke")
YesStroke.Parent = YesBox

local NoBox = Instance.new("TextLabel")
NoBox.Parent = GucciNotify
local NoCor = Instance.new("UICorner")
NoCor.Parent = NoBox
local NoStroke = Instance.new("UIStroke")
NoStroke.Parent = NoBox

GucciFrame.Name = "Gucci"
GucciFrame.Position = UDim2.new(0.409, 0, 0.54, 0)
GucciFrame.Size = UDim2.new(0.175, 0, 0.175, 0)
GucciFrame.AnchorPoint = Vector2.new(0.409, 0.5)
GucciFrame.BackgroundTransparency = 1
GucciFrame.BorderSizePixel = 0

GucciNotify.Name = "GucciNotify"
GucciNotify.Text = ""
GucciNotify.Visible = false
GucciNotify.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
GucciNotify.BackgroundTransparency = 0.2
GucciNotify.Size = UDim2.new(0, 250, 0, 125)
GucciNotify.Position = UDim2.new(0.408, 0, 0.5, 0)

GucciCor.CornerRadius = UDim.new(0, 10)

TextBox.Text = "<b>You want spawn new blobman?</b>"
TextBox.RichText = true
TextBox.TextScaled = true
TextBox.BackgroundTransparency = 1
TextBox.TextColor3 = Color3.fromRGB(0, 0, 0)
TextBox.Size = UDim2.new(0, 221, 0, 50)
TextBox.Position = UDim2.new(0.068, 0, 0.112, 0)

TextStroke.Color = Color3.fromRGB(50, 50, 50)
TextStroke.Thickness = 0.5

YesBox.Name = "YesBox"
YesBox.Text = "<b>[ Y ]</b>"
YesBox.RichText = true
YesBox.TextSize = 14
YesBox.TextColor3 = Color3.fromRGB(0, 0, 0)
YesBox.BackgroundColor3 = Color3.fromRGB(72, 200, 72)
YesBox.Size = UDim2.new(0, 95, 0, 30)
YesBox.Position = UDim2.new(0.068, 0, 0.656, 0)

YesCor.CornerRadius = UDim.new(0, 10)
YesStroke.Color = Color3.fromRGB(50, 50, 50)
YesStroke.Thickness = 0.5

NoBox.Name = "NoBox"
NoBox.Text = "<b>[ N ]</b>"
NoBox.RichText = true
NoBox.TextSize = 14
NoBox.TextColor3 = Color3.fromRGB(0, 0, 0)
NoBox.BackgroundColor3 = Color3.fromRGB(200, 72, 72)
NoBox.Size = UDim2.new(0, 95, 0, 30)
NoBox.Position = UDim2.new(0.572, 0, 0.656, 0)

NoCor.CornerRadius = UDim.new(0, 10)
NoStroke.Color = Color3.fromRGB(50, 50, 50)
YesStroke.Thickness = 0.5

screenGui.Parent = Players.LocalPlayer:WaitForChild("PlayerGui")
screenGui.ResetOnSpawn = false
textLabel.Parent = screenGui
CoolDown.Parent = screenGui

invis.Enabled = false
invis.Contrast = 0.5
invis.TintColor = Color3.fromRGB(150, 150, 255)

local anim = Instance.new("Animation")
anim.AnimationId = "rbxassetid://168268306"

textLabel.Size = UDim2.new(0.1, 0, 0.015, 0)
textLabel.Position = UDim2.new(0.458, 0, 0.477, 0)
textLabel.Text = 'Jerk'
textLabel.TextStrokeColor3 = Color3.new(0, 0, 0)
textLabel.BackgroundTransparency = 1
textLabel.TextScaled = true
textLabel.TextColor3 = Color3.new(255, 255, 255)
textLabel.TextStrokeTransparency = 0
textLabel.Visible = false

CoolDown.Size = UDim2.new(0.1, 0, 0.015, 0)
CoolDown.Position = UDim2.new(0.458, 0, 0.460, 0)
CoolDown.Text = ''
CoolDown.TextStrokeColor3 = Color3.new(0, 0, 255)
CoolDown.BackgroundTransparency = 1
CoolDown.TextScaled = true
CoolDown.TextColor3 = Color3.new(255, 255, 255)
CoolDown.TextStrokeTransparency = 0
CoolDown.Visible = false

local DestroyToyEvent = rs.MenuToys.DestroyToy
local Struggle = rs.CharacterEvents.Struggle
local DestroyLine = rs.GrabEvents.DestroyGrabLine
local GrabEvent = rs.GrabEvents.SetNetworkOwner
local ToySpawn = rs.MenuToys.SpawnToyRemoteFunction
local BombEvent = rs.BombEvents.BombExplode
local RagdollRemote = rs.CharacterEvents.RagdollRemote
local Look = rs.CharacterEvents.Look
local CreateGrabEvent = rs.GrabEvents.CreateGrabLine
local StickyPartEvent = rs.PlayerEvents.StickyPartEvent
local UseEvent = rs.HoldEvents.Use
local GameNotify = rs.GameCorrectionEvents.GameCorrectionsNotify

local function FindPlot()
    local myName, plots, plotItems = me.Name, w.Plots, w.PlotItems
    for i = 1,5 do
        local Plot = "Plot"..i
		local childs = plots[Plot].PlotSign.ThisPlotsOwners:GetChildren()
		local childsnum = #childs
        for j = 1, childsnum do
            if childs[j].Value == myName then
                return plotItems[Plot]
            end
        end
    end
	return false
end

local function toy_delete(toy) if toy then DestroyToyEvent:FireServer(toy) end end
local function Destroy_Line(Part) for _,v in pairs(Part.Parent:GetDescendants()) do if v.Name == "PartOwner" then DestroyLine:FireServer(v.Parent) end end end
local function toy_spawn(name, cframe, vector)
	local InPlot, InOwnerPlot, CanSpawn = me.InPlot, me.InOwnedPlot, me.CanSpawnToy

	while InPlot.Value and not InOwnerPlot.Value and not CanSpawn.Value do task.wait() end

	if typeof(vector) == "string" then
		if string.lower(vector) == "headup" then
			task.spawn(function()
				ToySpawn:InvokeServer(name,CFrame.new(cframe.Position+Vector3.new(0, 1, 0), cframe.Position)*CFrame.Angles(math.pi, 0, 0),Vector3.new(0,0,0))
			end)
		end
	else
		if vector then
			task.spawn(function()
				ToySpawn:InvokeServer(name, cframe, vector)
			end)
		else
			task.spawn(function()
				ToySpawn:InvokeServer(name, cframe, Vector3.new(0, 0, 0))
			end)
		end
	end
	local SpawnedToy, InHouse
	if InOwnerPlot.Value then
		local Plot = FindPlot()
		if Plot then
			Plot.ChildAdded:Once(function(toy)
				if toy.Name == name and toy:IsA("Model") then
					SpawnedToy = toy
				end
			end)
			InHouse = true
		end
	else
		BackPack.ChildAdded:Once(function(toy)
			if toy.Name == name and toy:IsA("Model") then
				SpawnedToy = toy
			end
		end)
		InHouse = false
	end
	local time = tick()
	while not SpawnedToy do
		if tick()-time < 3 then 
			task.wait()
		else
			return false
		end
	end
	return SpawnedToy, InHouse
end
local function grab(prt) GrabEvent:FireServer(prt, prt.CFrame) end

local WorldToScreen = function(Object)
    local ObjectVector = w.CurrentCamera:WorldToScreenPoint(Object.Position)
    return Vector2.new(ObjectVector.X, ObjectVector.Y)
end

local GetClosestPlayerFromCursor = function()
	if Mouse.Target then 
		local target = Mouse.Target.Parent
		for _,v in pairs(Players:GetPlayers()) do if v.Name == target.Name then return v.Name end end
	end
    local found = nil
    local ClosestDistance = math.huge
    for i, v in pairs(Players:GetPlayers()) do
        if v ~= me and v.Character and v.Character:FindFirstChildOfClass("Humanoid") then
            for k, x in pairs(v.Character:GetChildren()) do
                if string.find(x.Name, "HumanoidRootPart") then
                    local Distance = (WorldToScreen(x) - Vector2.new(Mouse.X, Mouse.Y)).Magnitude
                    if Distance < ClosestDistance then
                        ClosestDistance = Distance
                        found = v
                    end
                end
            end
        end
    end
    return found
end

local function checkPointInArea(point, area, size): boolean
	local distance = Vector3.new(point.X - area.X, point.Y - area.Y,point.Z - area.Z)
	local X = math.abs(distance.X)
	local Y = math.abs(distance.Y)
	local Z = math.abs(distance.Z)
	return (X < (size.X)/2) and (Y < (size.Y)/2) and (Z < (size.Z)/2)
end

local function BringPlayer(name)
    if not Players:FindFirstChild(name) then
        return Rayfield:Notify({
        Title = "Bring error",
        Content = 'Player left',
        Duration = 4,
        Image = 7743878056,
        })
	elseif Players:FindFirstChild(name).Character.Humanoid.Health == 0 or Players:FindFirstChild(name).Character.Humanoid:GetState() == Enum.HumanoidStateType.Dead then
		return Rayfield:Notify({
        Title = "Bring Error",
        Content = 'Player died',
        Duration = 4,
        Image = 7743878857,
        })
	elseif Players:FindFirstChild(name).InPlot.Value then
		return Rayfield:Notify({
        Title = "Bring Error",
        Content = 'Player in House',
        Duration = 4,
        Image = 7743878857,
        })
    end
	local char, mychar = Players[name].Character or Players[name].CharacterAdded:wait(), me.Character or me.CharacterAdded:wait()
	local myhum, hum = mychar and FWC(mychar,"Humanoid",3), char and FWC(char,"Humanoid",3)
	local HRP, myHRP = char and FWC(char,"HumanoidRootPart",3), mychar and FWC(mychar,"HumanoidRootPart",3)
	local Head = char and FWC(char,"Head",3)
	local last_pos = HRP.Position
	local BackUp = myHRP.CFrame
	local function Stop(Back)
		myHRP.CFrame = Back
		for _,prt in pairs(mychar:GetChildren()) do if prt:IsA("BasePart") then prt.Velocity = Vector3.new() end end
		timer = 0
	end
	task.spawn(function()
		while (not(Head:FindFirstChild("PartOwner")) or Head:FindFirstChild("PartOwner").Value ~= me.Name) and task.wait(0.1) do
			if last_pos ~= HRP.Position then last_pos = HRP.Position end
		end
	end)
	while not(Head:FindFirstChild("PartOwner")) or (Head:FindFirstChild("PartOwner") and Head:FindFirstChild("PartOwner").Value ~= me.Name) do
		if HRP and HRP.Parent and myhum.Health ~= 0 and hum.Health ~= 0 then
			if timer < 74 and not(Players[name].InPlot.Value) then
				timer = timer + 1
				if not(me.IsHeld.Value) then
					myHRP.CFrame = HRP.CFrame + ((HRP.Position - last_pos)*me:GetNetworkPing()*25) + Vector3.new(0, -3.5, 0)
					task.spawn(grab, HRP)
					task.wait()
				else
					local myHead = mychar and FWC(mychar,"Head")
					while myHead:FindFirstChild("PartOwner") do
						Struggle:FireServer(me)
						task.wait()
					end
				end
			else
				Stop(BackUp)
				return
			end
		else
			Stop(BackUp)
			return
		end
	end
	myHRP.CFrame = BackUp
	task.defer(function()
		for _,prt in pairs(mychar:GetChildren()) do if prt:IsA("Part") then prt.Velocity = Vector3.new() end end
		while Head and Head.Parent and (Head:FindFirstChild("PartOwner") and Head:FindFirstChild("PartOwner").Value == me.Name) do
			if not Head.BallSocketConstraint.Enabled then
				HRP.CFrame = myHRP.CFrame + myHRP.CFrame.LookVector*math.pi, Vector3.new(10,0,0)
				for _,prt in pairs(char:GetChildren()) do if prt:IsA("Part") then prt.Velocity = Vector3.new(); prt.CanTouch = false end end
			else
				for _,prt in pairs(char:GetChildren()) do
					if prt:IsA("Part") then
						prt.CFrame = myHRP.CFrame + myHRP.CFrame.LookVector*math.pi, Vector3.new(10,0,0)
						prt.Velocity = Vector3.new()
						prt.CanTouch = false
					end
				end
			end
			task.wait()
		end
	end)
	task.wait(0.15)
	if GrabFlag == "Default" then Destroy_Line(HRP) end
	timer = 0
end

local function SwitchPlayer()
	if timer ~= 0 then return end
    if not Players:FindFirstChild(TargetPlayer) then
        return Rayfield:Notify({
        Title = "Switch error",
        Content = 'Player left',
        Duration = 4,
        Image = 7743878056,
        })
    end
    if Players:FindFirstChild(TargetPlayer).Character.Humanoid.Health == 0 or Players:FindFirstChild(TargetPlayer).InPlot.Value then
        return Rayfield:Notify({
            Title = "Switch Error",
            Content = 'Player died or in safe-zone',
            Duration = 4,
            Image = 7743878857,
            })
    end
	if Players:FindFirstChild(TargetPlayer) then HRP = Players:FindFirstChild(TargetPlayer).Character.HumanoidRootPart end
    local BackUp = FWC(me.Character, "HumanoidRootPart").CFrame
    while task.wait() and HRP and FWC(me.Character, "Humanoid").Health ~= 0 and not(HRP.Parent.Head:FindFirstChild("PartOwner")) or (HRP.Parent.Head:FindFirstChild("PartOwner") and HRP.Parent.Head:FindFirstChild("PartOwner").Value ~= me.Name) do
		if timer > 75 then FWC(me.Character, "HumanoidRootPart").CFrame = BackUp; timer = 0; return else timer = timer + 1 end
		if w.PlotItems.PlayersInPlots:FindFirstChild(TargetPlayer) or me.IsHeld.Value == true or WaterBring then FWC(me.Character, "HumanoidRootPart").CFrame = BackUp; return end
		FWC(me.Character, "HumanoidRootPart").CFrame = HRP.CFrame
		task.defer(grab, HRP)
    end
    for _,prt in pairs(HRP.Parent:GetChildren()) do if prt:IsA("Part") then prt.Velocity = Vector3.new() end end
    HRP.CFrame = BackUp
	if GrabFlag == "Default" then Destroy_Line(HRP) end
	for _,prt in pairs(FWC(me.Character, "HumanoidRootPart").Parent:GetChildren()) do if prt:IsA("Part") then prt.Velocity = Vector3.new() end end
	timer = 0
end
local function WaterTeleport(Part)
	if (Part.Name == "Ocean" or Part.Name == "CloneOcean") then
		local torso = FWC(me.Character,"Torso")
		Struggle:FireServer(me)
		for i,Prt in pairs(w.Map.BaseGround:GetChildren()) do
			if i == 4 then TP = Prt
			elseif Prt:IsA("BasePart") and (Prt.Position - torso.Position).Magnitude < (TP.Position - torso.Position).Magnitude then
				TP = Prt
			end
		end
		for _,Prt in pairs(w.Map.CaveMountain["Mountain2rocks.1"]:GetChildren()) do
			if (Prt.Position - torso.Position).Magnitude < (TP.Position - torso.Position).Magnitude then
				for _,check in pairs(w.Map.CaveMountain["Mountain2rocks.1"]:GetChildren()) do
					if checkPointInArea(Vector3.new(Prt.Position.X, (Prt.Size.Y/2 + 5) + Prt.Position.Y, Prt.Position.Z), check.Position, check.Size) then break
					else TP = Prt end
				end
			end
		end
		for _,Prt in pairs(w.Map.TrainTunnel.Mountain1:GetChildren()) do
			if (Prt.Position - torso.Position).Magnitude < (TP.Position - torso.Position).Magnitude and Prt.Position ~= Vector3.new(576.7764282226562, 48.647216796875, 218.220947265625) and Prt.Position ~= Vector3.new(653.5665283203125, -80.8524169921875, 114.07162475585938) then
				local breaker = false
				for _,check in pairs(w.Map.CaveMountain["Mountain2rocks.1"]:GetChildren()) do
					if checkPointInArea(Vector3.new(Prt.Position.X, (Prt.Size.Y/2 + 5) + Prt.Position.Y, Prt.Position.Z), check.Position, check.Size) then breaker = true; break
					else TP = Prt end
				end
				for _,check in pairs(w.Map.TrainTunnel.TrainTunnelFloor:GetChildren()) do
					if breaker then break end
					if checkPointInArea(Vector3.new(Prt.Position.X, (Prt.Size.Y/2 + 5) + Prt.Position.Y, Prt.Position.Z), check.Position, check.Size) then break
					else TP = Prt end
				end
				for _,model in pairs(w.Map.TrainTunnel.TrainTunnelFrame:GetChildren()) do
					if breaker then break end
					for _,check in pairs(model:GetChildren()) do
						if checkPointInArea(Vector3.new(Prt.Position.X, (Prt.Size.Y/2 + 5) + Prt.Position.Y, Prt.Position.Z), check.Position, check.Size) then break
						else TP = Prt end
					end
				end
			end
		end
		for _,prt in pairs(me.Character:GetChildren()) do if prt:IsA("Part") then prt.Velocity = Vector3.new() end end
		if TP.Position == Vector3.new(551.9424438476562, -79.8524169921875, 255.3026580810547) then torso.CFrame = CFrame.new(TP.Position.X, ((TP.Size.Y/2 + 5) + TP.Position.Y), TP.Position.Z + 10); return end
		if TP.Position == Vector3.new(693.1507568359375, 27.1475830078125, -121.8285903930664) then torso.CFrame = CFrame.new(TP.Position.X+10, ((TP.Size.Y/2 + 5) + TP.Position.Y), TP.Position.Z); return end
		if TP.Size.Y > 180 then torso.CFrame = CFrame.new(TP.Position.X, ((TP.Size.Y/2 + 5) + TP.Position.Y), TP.Position.Z)
		else torso.CFrame = CFrame.new(TP.Position.X, ((TP.Size.X/2 + 5) + TP.Position.Y), TP.Position.Z) end
	elseif (Part.Name == "Ocean" or Part.Name == "CloneOcean") and timer > 0 then WaterBring = true end	
end
local function blob_kick(blob,hrp,rl,v)
	local detec = FWC(blob,rl.."Detector",2)
	if detec then
		local grab = blob.BlobmanSeatAndOwnerScript.CreatureGrab
		local drop = blob.BlobmanSeatAndOwnerScript.CreatureDrop
		local rel = blob.BlobmanSeatAndOwnerScript.CreatureRelease
		if v == "Default" and detec then
			grab:FireServer(detec,hrp,detec[rl.."Weld"])
		elseif v == "DDrop" then
			drop:FireServer(detec[rl.."Weld"])
		elseif v == "Release" then
			rel:FireServer(detec[rl.."Weld"],hrp)
		end
	end
end

local function AntiKick(toy)
	task.defer(function()
		local LDetec = FWC(toy,"LeftDetector",3)
		local Left = LDetec and FWC(LDetec,"LeftWeld",3)
		local LeftAlig = LDetec and FWC(LDetec,"LeftAlignOrientation",3)
		local Rigid = LDetec and FWC(LDetec,"RigidConstraint",3)
		if LeftAlig then 
			LeftAlig.Changed:Connect(function(Attach)
				local hrp = FWC(me.Character,"HumanoidRootPart",2)
				local Cam = FWC(me.Character,"CamPart",2)
				if Attach == "Attachment0" and LeftAlig.Attachment0 == Cam:FindFirstChild("RootAttachment") and not Waiting then
					LeftAlig.Enabled = false; Left.Enabled = false; Waiting = true
					task.spawn(function() Struggle:FireServer(me) end)
					task.wait(0.4)
					while LeftAlig.Attachment0 == Cam:FindFirstChild("RootAttachment") do task.wait() end
					LeftAlig.Enabled = true; Left.Enabled = true; Waiting = false
				end
			end)
		end
	end)
	local RDetec = FWC(toy,"RightDetector",3)
	local Right = RDetec and FWC(RDetec,"RightWeld",3)
	local RightAlig = RDetec and FWC(RDetec,"RightAlignOrientation",3)
	local Rigid = RDetec and FWC(RDetec,"RigidConstraint",3)
	if RightAlig then
		RightAlig.Changed:Connect(function(Attach)
			local hrp = FWC(me.Character,"HumanoidRootPart")
			local Cam = FWC(me.Character,"CamPart")
			if Attach == "Attachment0" and RightAlig.Attachment0 == Cam:FindFirstChild("RootAttachment") and not Waiting then
				RightAlig.Enabled = false; Right.Enabled = false; Waiting = true
				task.spawn(function() Struggle:FireServer(me) end)
				task.wait(0.4)
				while RightAlig.Attachment0 == Cam:FindFirstChild("RootAttachment") do task.wait() end
				RightAlig.Enabled = true; Right.Enabled = true; Waiting = false
			end
		end)
	end
end

local Window = Rayfield:CreateWindow({
    Name = "INIT",
    Icon = 0,
    Theme = "Default",

    DisableRayfieldPrompts = false,
    DisableBuildWarnings = false,

    ConfigurationSaving = {
      Enabled = true,
      FolderName = "DarknessScript",
      FileName = "DarknessScript"
    },

    Discord = {
      Enabled = false,
      Invite = "noinvitelink",
      RememberJoins = true
    },

    KeySystem = false,
    KeySettings = {
      Title = "Untitled",
      Subtitle = "Key System",
      Note = "No method of obtaining the key is provided",
      FileName = "Key",
      SaveKey = true,
      GrabKeyFromSite = false,
      Key = {"Hello"}
    }
})
local Tab = Window:CreateTab("Others", 4483362458)
local Section = Tab:CreateSection("Target Player")
local Label = Tab:CreateLabel(TargetPlayerView, 4483362458, Color3.fromRGB(34, 34, 34), false)

	local Input = Tab:CreateInput({
		Name = "Input Player",
		CurrentValue = "",
		PlaceholderText = "Input NickName",
		RemoveTextAfterFocusLost = false,
		Flag = "Input1",
		Callback = function(Text)
			for _, name in pairs(Players:GetChildren()) do
				if string.find(string.lower(name.DisplayName), string.lower(Text)) ~= nil then
					TargetPlayer = name.Name
					if Players:FindFirstChild(TargetPlayer) then HRP = Players:FindFirstChild(TargetPlayer).Character:FindFirstChild("HumanoidRootPart") end
					TargetPlayerView = "TargetPlayer : " .. Players[TargetPlayer].DisplayName .. ' (' .. TargetPlayer .. ')'
					Label:Set(TargetPlayerView, 4483362458, Color3.fromRGB(34, 34, 34), false)
					return
				elseif string.find(string.lower(name.Name), string.lower(Text)) ~= nil then
					TargetPlayer = name.Name
					if Players:FindFirstChild(TargetPlayer) then HRP = Players:FindFirstChild(TargetPlayer).Character:FindFirstChild("HumanoidRootPart") end
					TargetPlayerView = "TargetPlayer : " .. Players[TargetPlayer].DisplayName .. ' (' .. TargetPlayer .. ')'
					Label:Set(TargetPlayerView, 4483362458, Color3.fromRGB(34, 34, 34), false)
					return
				end
			end
		end,
	})

	local Keybind = Tab:CreateKeybind({
		Name = "MouseTarget",
		CurrentKeybind = "Y",
		HoldToInteract = false,
		Flag = "MouseTarget",
		Callback = function(Keybind)
			if GucciNotify.Visible then return end
			TargetPlayer = tostring(GetClosestPlayerFromCursor())
			TargetPlayerView = "TargetPlayer : " .. Players[TargetPlayer].DisplayName .. ' (' .. TargetPlayer .. ')'
			Label:Set(TargetPlayerView, 4483362458, Color3.fromRGB(34, 34, 34), false)
			print(Players[TargetPlayer].DisplayName .. ' (' .. TargetPlayer .. ')')
			if Players:FindFirstChild(TargetPlayer) then HRP = Players:FindFirstChild(TargetPlayer).Character.HumanoidRootPart end
			Rayfield:Notify({
				Title = "Target",
				Content = Players[TargetPlayer].DisplayName .. ' (' .. TargetPlayer .. ')',
				Duration = 5,
				Image = 7743876054,
			})
		end,
	})

local Section = Tab:CreateSection("Combat")

	local Toggle = Tab:CreateToggle({
		Name = "Ragdoll Grab",
		CurrentValue = false,
		Flag = "RagdollGrab",
		Callback = function(Value)
			RagdollGrab = Value
			if RagdollGrab then
				if not BackPack:FindFirstChild("Ragdoll") then
					local Pallet = toy_spawn("PalletLightBrown",FWC(me.Character,"Head"),"headup")
					local Sound = Pallet:WaitForChild("SoundPart")
					while Sound and not Sound:FindFirstChild("PartOwner") or (Sound:FindFirstChild("PartOwner") and Sound:FindFirstChild("PartOwner").Value ~= me.Name) do
						task.spawn(grab,Sound)
						task.wait()
					end
					local BV = Instance.new("BodyVelocity")
					BV.Parent = Sound
					BV.MaxForce = Vector3.new(0,"inf", 0)
					BV.Velocity = Vector3.new(0,1000,0)
					for _,v in pairs(Pallet:GetChildren()) do if v:IsA("BasePart") then v.Transparency = 1; v.Size = Vector3.new(0.5,0.5,0.5) end end
					if Sound:FindFirstChild("Wood") then Sound.Wood:Destroy() end
					Pallet.Name = "Ragdoll"
					local index = nil
					for i,v in pairs(BackPack:GetChildren()) do if v.Name == "Ragdoll" then index = i end end
					local contents = me.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents
					for i,v in ipairs(contents:GetChildren()) do
						if v.Name == "PalletLightBrown" and i == index then
							local view = v.ViewItemButton
							view.Text = "RAGDOLL"
							view.TextScaled = true
							view.LowResImage.Image = ""
						end
					end
				end
				Cons["RagdollGrab"] = w.ChildAdded:Connect(function(part)
					if RagdollGrab then
						if part.Name == "GrabParts" then
							local GP = part.GrabPart.WeldConstraint.Part1
							local hrp = GP.Parent:FindFirstChild("HumanoidRootPart")
							if GP.Parent:FindFirstChild("HumanoidRootPart") then
								firetouchinterest(BackPack:FindFirstChild("Ragdoll").SoundPart,GP.Parent.Head,0)
							end
						end
					end
				end)
			else
				Disc("RagdollGrab")
			end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "MassLess Grab",
		CurrentValue = false,
		Flag = "MassLess",
		Callback = function(Value)
			MassLessGrab = Value
			if MassLessGrab then
				Cons["MLChild"] = w.ChildAdded:Connect(function(part)
					if part.Name == "GrabParts" then
						local GP = part.GrabPart.WeldConstraint.Part1
						local DP = FWC(part,"DragPart")
						DP.AlignOrientation.MaxTorque = "inf"
						DP.AlignOrientation.Responsiveness = 200
						DP.AlignPosition.MaxForce = "inf"
						DP.AlignPosition.Responsiveness = 200
					end
				end)
			else
				Disc("MLChild")
			end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Strength [LMB]",
		CurrentValue = false,
		Flag = "LBMStrength",
		Callback = function(Value)
			LMBStren = Value
			if LMBStren then
				Cons["StrengChild"] = w.ChildAdded:Connect(function(part)
					if LMBStren then
						if part.Name == "GrabParts" then
							local GP = part.GrabPart.WeldConstraint.Part1
							for _,plr in pairs(Players:GetPlayers()) do if GP.Parent.Name == plr.Name then type = "Players"; break end; type = "Objects" end
							for _,prt in pairs(GP.Parent:GetChildren()) do if prt:IsA("BasePart") then TMass = TMass + prt.Mass end end
						end
					end
				end)
				Cons["StrengRem"] = w.ChildRemoved:Connect(function(part)
					if part.Name == "GrabParts" then
						local GP = part.GrabPart.WeldConstraint.Part1
						if Strentype == type or Strentype == "All" then
							if GP.Velocity.X+TMass/MassCheck > 90 or GP.Velocity.Y+TMass/MassCheck > 90 or GP.Velocity.Z+TMass/MassCheck > 90 then
								GP.Velocity = GP.Velocity * LMBValue
							end
						end
					end
				end)
			else
				Disc("StrengChild")
				Disc("StrengRem")
			end
		end,
	})

	local Slider = Tab:CreateSlider({
		Name = "Strength Power",
		Range = {1.1, 10},
		Increment = 0.1,
		Suffix = "x",
		CurrentValue = 2,
		Flag = "StrengthPower",
		Callback = function(Value)
			LMBValue = Value
		end,
	})

	local Slider = Tab:CreateSlider({
		Name = "Strength Mass check",
		Range = {1, 2},
		Increment = 0.1,
		Suffix = "/",
		CurrentValue = 1,
		Flag = "StrengthMass",
		Callback = function(Value)
			MassCheck = Value
		end,
	})

	local Dropdown = Tab:CreateDropdown({
		Name = "Strength Type",
		Options = {"Objects","Players","All"},
		CurrentOption = {"All"},
		MultipleOptions = false,
		Flag = "Strengthtype",
		Callback = function(Options)
			Strentype = Options[1]
		end,
	})

local Section = Tab:CreateSection("Ragdoll")

	local Keybind = Tab:CreateKeybind({
		Name = "Fire SnowBall",
		CurrentKeybind = "V",
		HoldToInteract = false,
		Flag = "SnowBall",
		Callback = function(Keybind)
			if not Players:FindFirstChild(TargetPlayer) or FWC(me.Character, "Humanoid").Health == 0 then return end
			while Players[TargetPlayer].Character:FindFirstChild("Humanoid").Health == 0 and task.wait(0.15) do end
			local SnowModel = toy_spawn("BallSnowball",FWC(me.Character,"Head"),"headup")
			local SnowBall = SnowModel:WaitForChild("SoundPart")
			local TargetPlr = Players:FindFirstChild(TargetPlayer)
			local TargetChar = TargetPlr.Character
			local TargetHRP = TargetChar:WaitForChild("HumanoidRootPart")
			while task.wait() and SnowBall and FWC(me.Character, "Humanoid").Health ~= 0 and not(SnowBall:FindFirstChild("PartOwner")) or (SnowBall:FindFirstChild("PartOwner") and SnowBall:FindFirstChild("PartOwner").Value ~= me.Name) do
				if (SnowBall.Position - FWC(me.Character, "HumanoidRootPart").Position).Magnitude > 45 then return end
				task.spawn(grab, SnowBall)
			end
			local BV = Instance.new("BodyVelocity")
			BV.Parent = SnowBall
			BV.Velocity = Vector3.new(0,150,0)
			firetouchinterest(SnowBall, TargetHRP, 0)
			task.spawn(function() while task.wait(0.05) and SnowBall and SnowBall.Parent do firetouchinterest(SnowBall, TargetHRP, 0) end end)
			while SnowBall and SnowBall.Parent and task.wait() do SnowBall.CFrame = FWC(me.Character, "HumanoidRootPart").CFrame + Vector3.new(0, 12.5, 0) end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Loop SnowBall",
		CurrentValue = false,
		Flag = "Toggle1",
		Callback = function(Value)
			LoopSnow = Value
			while LoopSnow and task.wait() do
				if not Players:FindFirstChild(TargetPlayer) then return
				elseif FWC(me.Character, "Humanoid").Health == 0 then me.CharacterAdded:Wait() end
				local SnowModel = toy_spawn("BallSnowball",FWC(me.Character,"Head"),"headup")
				local SnowBall = SnowModel:WaitForChild("SoundPart")
				local TargetPlr = Players:FindFirstChild(TargetPlayer)
				local TargetChar = TargetPlr.Character
				local TargetHRP = FWC(TargetChar,"HumanoidRootPart")
				local TargetHum = FWC(TargetChar,"Humanoid")
				local Again = false
				while not(SnowBall:FindFirstChild("PartOwner")) or (SnowBall:FindFirstChild("PartOwner") and SnowBall:FindFirstChild("PartOwner").Value ~= me.Name) do
					if SnowBall and SnowBall.Parent then
						if FWC(me.Character, "Humanoid").Health ~= 0 then
							if (SnowBall.Position - FWC(me.Character, "HumanoidRootPart").Position).Magnitude < 45 then
								task.spawn(grab, SnowBall)
								task.wait()
							else
								if SnowModel and SnowModel.Parent then toy_delete(SnowModel) end
								Again = true
								break
							end
						else
							if SnowModel and SnowModel.Parent then toy_delete(SnowModel) end
							Again = true
							break
						end
					else
						Again = true
						break
					end
				end
				if Again then continue end
				local BV = Instance.new("BodyVelocity")
				BV.Parent = SnowBall
				BV.Velocity = Vector3.new(0,150,0)
				local DiedWait
				if TargetHum.Health == 0 then
					DiedWait = true
					task.defer(function()
						while DiedWait and SnowBall and SnowBall.Parent do SnowBall.CFrame = FWC(me.Character, "HumanoidRootPart").CFrame + Vector3.new(0, 12.5, 0); task.wait() end
					end)
					TargetPlr.CharacterAdded:Once(function(char) TargetChar = char end)
					TargetHRP = FWC(TargetChar,"HumanoidRootPart")
				end
				DiedWait = false
				firetouchinterest(SnowBall, TargetHRP, 0)
				task.spawn(function()
					while task.wait(0.05) and SnowBall and SnowBall.Parent do
						firetouchinterest(SnowBall, TargetHRP, 0)
					end
				end)
				while SnowBall and SnowBall.Parent and task.wait() do SnowBall.CFrame = FWC(me.Character, "HumanoidRootPart").CFrame + Vector3.new(0, 12.5, 0) end
			end
		end,
	})

local Section = Tab:CreateSection("Player Teleport")

	local Keybind = Tab:CreateKeybind({
		Name = "Bring Player",
		CurrentKeybind = "B",
		HoldToInteract = false,
		Flag = "Bring",
		Callback = function(Keybind)
			BringPlayer(TargetPlayer)
		end,
	})

	local Keybind = Tab:CreateKeybind({
		Name = "Switch Player",
		CurrentKeybind = "G",
		HoldToInteract = false,
		Flag = "Switch",
		Callback = function(Keybind)
			SwitchPlayer()
		end,
	})

	local Dropdown = Tab:CreateDropdown({
	Name = "GrabEvent",
	Options = {"Default","Always"},
	CurrentOption = {"Default"},
	MultipleOptions = false,
	Flag = "GrabEvent",
	Callback = function(Options)
		GrabFlag = Options[1]
	end,
	})

local Section = Tab:CreateSection("House")

	local Toggle = Tab:CreateToggle({
		Name = "Off Barrier",
		CurrentValue = false,
		Flag = "Barrier",
		Callback = function(Value)
			for _, v in pairs(w.Plots:GetChildren()) do
				v.PlotArea.CanQuery = not(Value)
				for _, b in pairs(v.Barrier:GetChildren()) do
					b.CanCollide = not(Value)
					b.CanQuery = not(Value)
					b.CanTouch = not(Value)
				end
			end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Invisible Toys",
		CurrentValue = false,
		Flag = "InvisibleToys",
		Callback = function(Value)
			if Value then
				local plot = FindPlot()
				if plot then
					for _,v in pairs(w.PlotItems:GetChildren()) do
						if v.Name ~= "PlayersInPlots" and v.Name ~= plot.Name then v.Parent = rs.SlotEvents end
					end
				else
					for _,v in pairs(w.PlotItems:GetChildren()) do
						if v.Name ~= "PlayersInPlots" then v.Parent = rs.SlotEvents end
					end
				end
			else
				for _,v in pairs(rs.SlotEvents:GetChildren()) do
					if v.ClassName == "Folder" then v.Parent = w.PlotItems end
				end
			end
		end,
	})

local Section = Tab:CreateSection("Movement")

	local Keybind = Tab:CreateKeybind({
		Name = "Click TP",
		CurrentKeybind = "Z",
		HoldToInteract = false,
		Flag = "ClickTP",
		Callback = function(Keybind)
			if Mouse.Target then
				local char = me.Character
				if not char.Head.BallSocketConstraint.Enabled then
					char.HumanoidRootPart.CFrame = Mouse.Hit + Vector3.new(0, 3, 0)
					for _,v in pairs(char:GetChildren()) do if v:IsA("BasePart") then v.Velocity = Vector3.new() end end
				else
					for _,v in pairs(char:GetChildren()) do if v:IsA("BasePart") then v.CFrame = Mouse.Hit + Vector3.new(0, 2.15, 0) end end
					for _,v in pairs(char:GetChildren()) do if v:IsA("BasePart") then v.Velocity = Vector3.new() end end
				end
			end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Shift Speed",
		CurrentValue = false,
		Flag = "ShiftSpeed",
		Callback = function(Value)
			ShiftSpeed = Value
		end,
	})

	local Slider = Tab:CreateSlider({
		Name = "Speed",
		Range = {30, 300},
		Increment = 2,
		Suffix = "",
		CurrentValue = 50,
		Flag = "ShiftSpeedValue",
		Callback = function(Value)
			ShiftValue = Value
		end,
	})

local Section = Tab:CreateSection("Other")

	-- local Keybind = Tab:CreateKeybind({
	-- 	Name = "Auto SoundBar",
	-- 	CurrentKeybind = "J",
	-- 	HoldToInteract = false,
	-- 	Flag = "AutoSoundBar",
	-- 	Callback = function(Keybind)
	-- 		local Model = Mouse.Target.Parent
	-- 		for _,prt in pairs(Model:GetChildren()) do
	-- 			if prt:IsA("BasePart") and string.len(prt.Name) == 2 then
	-- 				if table.find(CFGList, prt.Name) and prt.Transparency == 1 then grab(prt) end
	-- 				if not(table.find(CFGList, prt.Name)) and prt.Transparency == 0 then grab(prt) end
	-- 			end
	-- 		end
	-- 		if Model.MuteButton.Transparency == 1 then grab(Model.MuteButton) end
	-- 	end,
	-- })

	-- local Dropdown = Tab:CreateDropdown({
	-- 	Name = "SoundBar Config",
	-- 	Options = {"Zig","Heart","Penis"},
	-- 	CurrentOption = {"Zig"},
	-- 	MultipleOptions = false,
	-- 	Flag = "SoundBarCFG",
	-- 	Callback = function(Options)
	-- 		if Options[1] == "Zig" then CFGList = {"A3","B3","C3","C6","C7","C8","F1","F2","F3","F6","G6","H6"}
	-- 		elseif Options[1] == "Heart" then CFGList = {"A1","A2","A3","A4","A5","A6","A7","A8","B1","B4","B5","B8","F1","F8","G1","G2","G7","G8","H1","H2","H3","H6","H7","H8"}
	-- 		elseif Options[1] == "Penis" then CFGList = {"A1","A2","A3","A6","A7","A8","B1","B2","B7","B8","C1","C2","C3","C6","C7","C8","D1","D2","D3","D6","D7","D8","E1","E8","H1","H4","H5","H8"}
	-- 		end
	-- 	end,
	-- })

	local Keybind = Tab:CreateKeybind({
		Name = "Clicker",
		CurrentKeybind = "U",
		HoldToInteract = false,
		Flag = "Clicker",
		Callback = function(Keybind)
			Disc("Clicker")
			local sound = BackPack:FindFirstChild("Ragdoll")
			if ClickerType == "Ragdoll" and not sound then
				local Pallet = toy_spawn("PalletLightBrown",FWC(me.Character,"Head"),"headup")
				local Sound = Pallet:WaitForChild("SoundPart")
				while Sound and not Sound:FindFirstChild("PartOwner") or (Sound:FindFirstChild("PartOwner") and Sound:FindFirstChild("PartOwner").Value ~= me.Name) do
					task.spawn(grab,Sound)
					task.wait()
				end
				local BV = Instance.new("BodyVelocity")
				BV.Parent = Sound
				BV.MaxForce = Vector3.new(0,"inf", 0)
				BV.Velocity = Vector3.new(0,1000,0)
				for _,v in pairs(Pallet:GetChildren()) do if v:IsA("BasePart") then v.Transparency = 1; v.Size = Vector3.new(0.5,0.5,0.5) end end
				if Sound:FindFirstChild("Wood") then Sound.Wood:Destroy() end
				Pallet.Name = "Ragdoll"
				local index
				for i,v in pairs(BackPack:GetChildren()) do if v.Name == "Ragdoll" then index = i end end
				local contents = me.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents
				for i,v in ipairs(contents:GetChildren()) do
					if v.Name == "PalletLightBrown" and i == index then
						local view = v.ViewItemButton
						view.Text = "RAGDOLL"
						view.TextScaled = true
						view.LowResImage.Image = ""
					end
				end
			end
			if sound and sound.Name == "Ragdoll" then sound = sound.SoundPart end
			if not(ClickerPart) then
				ClickerPart = Mouse.Target
				SB = Instance.new("SelectionBox", ClickerPart)
				SB.Adornee = ClickerPart
				SB.Color3 = Color3.fromRGB(255, 0, 0)
				SB.LineThickness = 0.025
				SB.SurfaceColor3 = Color3.fromRGB(255, 0, 0)
				SB.SurfaceTransparency = 0.5
			elseif ClickerPart and ClickerPart ~= Mouse.Target then
				SB:Destroy()
				ClickerPart = Mouse.Target
				SB = Instance.new("SelectionBox", ClickerPart)
				SB.Adornee = ClickerPart
				SB.Color3 = Color3.fromRGB(255, 0, 0)
				SB.LineThickness = 0.025
				SB.SurfaceColor3 = Color3.fromRGB(255, 0, 0)
				SB.SurfaceTransparency = 0.5
			elseif ClickerPart and ClickerPart == Mouse.Target then
				SB:Destroy()
				ClickerPart = nil
			end
			for _,plr in pairs(Players:GetPlayers()) do
				if ClickerPart and ClickerPart.Parent and ClickerPart.Parent.Name == plr.Name then
					Cons["Clicker"] = plr.CharacterAdded:Connect(function(char)
						if ClickerPart and ClickerPart.Parent and ClickerPart.Parent.Name == char.Name then
							if ClickerPart:FindFirstChild("SelectionBox") then SB:Destroy() end
							ClickerPart = char:WaitForChild("HumanoidRootPart")
							SB = Instance.new("SelectionBox", ClickerPart)
							SB.Adornee = ClickerPart
							SB.Color3 = Color3.fromRGB(255, 0, 0)
							SB.LineThickness = 0.025
							SB.SurfaceColor3 = Color3.fromRGB(255, 0, 0)
							SB.SurfaceTransparency = 0.5
						end
					end)
				end
			end
			local time1 = tick()
			task.defer(function() 
				while ClickerType == "Ragdoll" and ClickerPart and ClickerPart.Parent and ClickerPart.Parent:FindFirstChild("HumanoidRootPart") do
					if tick()-time1 > 0.25 then
						firetouchinterest(sound,ClickerPart.Parent.Head,0)
					end
					task.wait()
				end
			end)
			while task.wait() and ClickerPart and ClickerPart.Parent do
				if (me.Character.HumanoidRootPart.Position - ClickerPart.Position).Magnitude < 30 then
					task.spawn(function() for i = 1,3 do task.spawn(grab, ClickerPart) end end)
				end
			end
		end,
	})

	local ClickerType = Tab:CreateDropdown({
		Name = "Clicker Type",
		Options = {"Default","Ragdoll"},
		CurrentOption = {"Default"},
		MultipleOptions = false,
		Flag = "ClickerType",
		Callback = function(Options)
			ClickerType = Options[1]
			if ClickerType == "Ragdoll" and not(BackPack:FindFirstChild("Ragdoll")) then
				local Pallet = toy_spawn("PalletLightBrown",FWC(me.Character,"Head"),"headup")
				local Sound = FWC(Pallet,"SoundPart")
				while Sound and not Sound:FindFirstChild("PartOwner") or (Sound:FindFirstChild("PartOwner") and Sound:FindFirstChild("PartOwner").Value ~= me.Name) do
					task.spawn(grab,Sound)
					task.wait()
				end
				local BV = Instance.new("BodyVelocity")
				BV.Parent = Sound
				BV.MaxForce = Vector3.new(0,"inf", 0)
				BV.Velocity = Vector3.new(0,1000,0)
				for _,v in pairs(Pallet:GetChildren()) do if v:IsA("BasePart") then v.Transparency = 1; v.Size = Vector3.new(0.5,0.5,0.5) end end
				if Sound:FindFirstChild("Wood") then Sound.Wood:Destroy() end
				Pallet.Name = "Ragdoll"
				local index
				for i,v in pairs(BackPack:GetChildren()) do if v.Name == "Ragdoll" then index = i end end
				local contents = me.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents
				for i,v in ipairs(contents:GetChildren()) do
					if v.Name == "PalletLightBrown" and i == index then
						local view = v.ViewItemButton
						view.Text = "RAGDOLL"
						view.TextScaled = true
						view.LowResImage.Image = ""
					end
				end
			end
		end,
	})

	local Keybind = Tab:CreateKeybind({
		Name = "JerkOff",
		CurrentKeybind = "Q",
		HoldToInteract = false,
		Flag = "JerkOff",
		Callback = function(Keybind)
			if JerkFlag == nil then
				textLabel.Visible = true
				Rayfield:Notify({
					Title = "JerkOff",
					Content = 'JerkOff Enabled',
					Duration = 2,
					Image = 7734042071,
				})
				animator = FWC(me.Character, "Humanoid"):WaitForChild("Animator")
				jerkoff = animator:LoadAnimation(anim)
				JerkFlag = true
				jerkoff:Play()
				while task.wait(0.1) and JerkFlag do jerkoff.TimePosition = 0.3 end
			elseif JerkFlag then
				textLabel.Visible = false
				jerkoff:Stop()
				Rayfield:Notify({
					Title = "JerkOff",
					Content = 'JerkOff Disabled',
					Duration = 2,
					Image = 7734000129,
				})
				JerkFlag = nil
			end
		end,
	})

	local Keybind = Tab:CreateKeybind({
		Name = "Delete object",
		CurrentKeybind = "H",
		HoldToInteract = false,
		Flag = "DeleteObj",
		Callback = function(Keybind)
			local obj = Mouse.Target
			if obj and not obj:FindFirstAncestor("Map") and not obj:FindFirstAncestor("Slots") and not obj:FindFirstAncestor("Plots") and (obj.Position - FWC(me.Character, "HumanoidRootPart").Position).Magnitude < 30 then
				if not obj.Parent:FindFirstChildOfClass("Humanoid") then
					while obj and (not obj:FindFirstChild("PartOwner") or (obj:FindFirstChild("PartOwner") and obj:FindFirstChild("PartOwner").Value ~= me.Name)) do
						task.spawn(grab,obj)
						task.wait()
					end
				else
					local Head = FWC(obj.Parent,"Head")
					while obj and Head and (not Head:FindFirstChild("PartOwner") or (Head:FindFirstChild("PartOwner") and Head:FindFirstChild("PartOwner").Value ~= me.Name)) do
						task.spawn(grab,obj)
						task.wait()
					end
				end
				obj.CFrame = CFrame.new(300,-97,3000)
			end
		end,
	})

	local WS = Tab:CreateDropdown({
		Name = "Water Safe Type",
		Options = {"Walk", "Teleport", "Nothing"},
		CurrentOption = {"Nothing"},
		MultipleOptions = false,
		Flag = "WaterSafeType",
		Callback = function(Options)
			WaterSafe = Options[1]
			local Model = w.Map.AlwaysHereTweenedObjects.Ocean.Object:FindFirstChild("ObjectModel")
			if WaterSafe == "Walk" and Model then
				OceanFlag = false
				Disc("WTChar")
				Disc("WTTouch")
				for _,Ocean in pairs(Model:GetChildren()) do
					if Ocean.Name == 'Ocean' then
						Ocean.CanCollide = true
					end
				end
			elseif WaterSafe == "Teleport" then
				OceanFlag = true
				if Model then
					for _,Ocean in pairs(Model:GetChildren()) do
						if Ocean.Name == 'Ocean' then
							Ocean.CanCollide = false
						end
					end
				end
				Cons["WTChar"] = me.CharacterAdded:Connect(function(char)
					Cons["WTTouch"] = FWC(char,"Torso").Touched:Connect(function(Part)
						WaterTeleport(Part)
					end)
				end)
				Cons["WTTouch"] = FWC(me.Character,"Torso").Touched:Connect(function(Part)
					WaterTeleport(Part)
				end)
			elseif WaterSafe == "Nothing" then
				OceanFlag = false
				Disc("WTChar")
				Disc("WTTouch")
				if Model then
					for _,Ocean in pairs(Model:GetChildren()) do
						if Ocean.Name == 'Ocean' then
							Ocean.CanCollide = false
						end
					end
				end
			end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Player CoolDown",
		CurrentValue = false,
		Flag = "CoolDown",
		Callback = function(Value)
			CoolDownFlag = Value
			if CoolDownFlag then
				Cons["CoolDown"] = w.ChildAdded:Connect(function(part)
					if part.Name == "GrabParts" then
						local GP = part.GrabPart.WeldConstraint.Part1
						for _,v in pairs(Players:GetPlayers()) do
							if v.Name == GP.Parent.Name then
								CoolDown.Visible = true
								while part.Parent and task.wait() do if v and v.Parent then CoolDown.Text = tostring(v.HeldTimer.Value) end end
								CoolDown.Visible = false
								break
							end
						end
					end
				end)
			else
				Disc("CoolDown")
			end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Blob Notify",
		CurrentValue = false,
		Flag = "BlobNotify",
		Callback = function(Value)
			BlobNotify = Value
			while task.wait() and BlobNotify do
				for _,plr in pairs(Players:GetPlayers()) do
					if w:FindFirstChild(plr.Name.."SpawnedInToys") then
						for _,toy in pairs(w:FindFirstChild(plr.Name.."SpawnedInToys"):GetChildren()) do
							if toy.Name == "CreatureBlobman" and not(toy:GetAttribute("SPCHECK")) and plr ~= me then
								toy:SetAttribute("SPCHECK", true)
								task.wait(0.1)
								BlobHRP = toy:FindFirstChild("HumanoidRootPart")
								Rayfield:Notify({
								Title = "Blob",
								Content = plr.Name.." ("..plr.DisplayName..") Spawned Blob!",
								Duration = 3,
								Image = 4483362458,
								})
							end
						end
					end
				end
				for i = 1,5 do
					if w.PlotItems:FindFirstChild("Plot" .. i) then
						for _,Htoy in pairs(w.PlotItems["Plot" .. i]:GetChildren()) do
							if Htoy.Name == "CreatureBlobman" and not(Htoy:GetAttribute("SPCHECK")) then
								Htoy:SetAttribute("SPCHECK", true)
								task.wait(0.15)
								local Name = tostring(Htoy:WaitForChild("PlayerValue").Value)
								if Name ~= me.Name then
									BlobHRP = Htoy:FindFirstChild("HumanoidRootPart")
									Rayfield:Notify({
									Title = "House Blob",
									Content = Name.." ("..tostring(Players[Name].DisplayName)..") Spawned Blob in house!",
									Duration = 3,
									Image = 4483362458,
									})
								end
							end
						end
					end
				end
			end
		end,
	})

	local Keybind = Tab:CreateKeybind({
		Name = "Teleport to Blob",
		CurrentKeybind = "T",
		HoldToInteract = false,
		Flag = "TeleportBlob",
		Callback = function(Keybind)
			if BlobHRP then
				FWC(me.Character, "HumanoidRootPart").CFrame = BlobHRP.CFrame + Vector3.new(0, 7.5, 0)
			end
		end,
	})

local Tab = Window:CreateTab("Defence", 4483362458)
local Section = Tab:CreateSection("Anti-Lag")

	local LagView = Tab:CreateLabel("Lagger : ...", 4483362458, Color3.fromRGB(34, 34, 34), false)

	local Toggle = Tab:CreateToggle({
		Name = "Anti Lag",
		CurrentValue = false,
		Flag = "AntiLag",
		Callback = function(Value)
		AntiLag = Value
			if AntiLag then
				if ALType == "Default" or ALType == nil then
					me.PlayerScripts.CharacterAndBeamMove.Disabled = true
					for _,plr in pairs(Players:GetPlayers()) do
						if plr.Character and plr.Character:FindFirstChild("GrabParts") then plr.Character:FindFirstChild("GrabParts"):Destroy() end
					end
				elseif ALType == "Automatic" then
					local LinesCount = 0
					local LinesPlayers = {}
					Cons["ALEvent"] = CreateGrabEvent.OnClientEvent:Connect(function(plr)
						LinesCount = LinesCount + 1
						if not LinesPlayers[plr] then
							LinesPlayers[plr] = 1
						else
							LinesPlayers[plr] = LinesPlayers[plr] + 1
						end
					end)
					task.defer(function()
						while ALType == "Automatic" and task.wait(0.5) do
							if LinesCount > 55 then
								me.PlayerScripts.CharacterAndBeamMove.Disabled = true
								for i,v in LinesPlayers do
									if v > 55 then
										for _,plr in pairs(Players:GetPlayers()) do
											if plr.Name == tostring(i) then
												LagView:Set("Lagger : "..plr.DisplayName.." ("..plr.Name..")", 4483362458, Color3.fromRGB(34, 34, 34), false)
												break
											end
										end
										break
									end
								end
								for _,plr in pairs(game.Players:GetPlayers()) do
									if plr.Character and plr.Character:FindFirstChild("GrabParts") then plr.Character:FindFirstChild("GrabParts"):Destroy() end
								end

								local time2 = tick()
								while tick()-time2 < 15 and task.wait(0.2) do
									if LinesCount > 55 then
										time2 = tick()
									end
								end
							else
								me.PlayerScripts.CharacterAndBeamMove.Disabled = false
							end
						end
					end)

					while ALType == "Automatic" do
						time1 = tick()
						while tick()-time1 < 0.5 do task.wait() end
						LinesCount = 0
						for i,v in LinesPlayers do
							LinesPlayers[i] = 0
						end
						task.wait()
					end
				end
			else
				me.PlayerScripts.CharacterAndBeamMove.Disabled = false
				Disc("ALEvent")
			end
		end,
	})

	local LagList = Tab:CreateDropdown({
		Name = "Anti Lag Type",
		Options = {"Default","Automatic"},
		CurrentOption = {"Default"},
		MultipleOptions = false,
		Flag = "AntiLagType",
		Callback = function(Options)
			ALType = Options[1]
			if AntiLag and ALType == "Default" or not(ALType) then
				me.PlayerScripts.CharacterAndBeamMove.Disabled = true
				for _,plr in pairs(Players:GetPlayers()) do
					if plr.Character and plr.Character:FindFirstChild("GrabParts") then plr.Character:FindFirstChild("GrabParts"):Destroy() end
				end
			elseif AntiLag and ALType == "Automatic" then
				local LinesCount = 0
				local LinesPlayers = {}
				Cons["ALEvent"] = CreateGrabEvent.OnClientEvent:Connect(function(plr)
					LinesCount = LinesCount + 1
					if not LinesPlayers[plr] then
						LinesPlayers[plr] = 1
					else
						LinesPlayers[plr] = LinesPlayers[plr] + 1
					end
				end)
				task.defer(function()
					while ALType == "Automatic" and task.wait(0.5) do
						if LinesCount > 55 then
							me.PlayerScripts.CharacterAndBeamMove.Disabled = true
							for i,v in LinesPlayers do
								if v > 55 then
									for _,plr in pairs(Players:GetPlayers()) do
										if plr.Name == tostring(i) then
											LagView:Set("Lagger : "..plr.DisplayName.." ("..plr.Name..")", 4483362458, Color3.fromRGB(34, 34, 34), false)
											break
										end
									end
									break
								end
							end
							for _,plr in pairs(game.Players:GetPlayers()) do
								if plr.Character and plr.Character:FindFirstChild("GrabParts") then plr.Character:FindFirstChild("GrabParts"):Destroy() end
							end

							local time2 = tick()
							while tick()-time2 < 15 and task.wait(0.2) do
								if LinesCount > 55 then
									time2 = tick()
								end
							end
						else
							me.PlayerScripts.CharacterAndBeamMove.Disabled = false
						end
					end
				end)

				while ALType == "Automatic" do
					time1 = tick()
					while tick()-time1 < 0.5 do task.wait() end
					LinesCount = 0
					for i,v in LinesPlayers do
						LinesPlayers[i] = 0
					end
					task.wait()
				end
			end
		end,
	})

local Section = Tab:CreateSection("Defence")

	local Toggle = Tab:CreateToggle({
		Name = "Anti Grab [BETA]",
		CurrentValue = false,
		Flag = "AntiGrab",
		Callback = function(Value)
			AntiGrab = Value
			if AntiGrab then
				local char, AGWalk = me.Character or me.CharacterAdded:wait(), false
				local hrp, hum, head = FWC(char,"HumanoidRootPart"), FWC(char,"Humanoid"), FWC(char,"Head")
				Cons["AGHead"] = head.ChildAdded:Connect(function(PartOwner)
					if PartOwner.Name == "PartOwner" then
						if not(AntiGrabProc) then
							AntiGrabProc = true
							local save = hrp.CFrame; hum.Sit = false; Struggle:FireServer(me)
							task.spawn(function() 
								while (head and head:FindFirstChild("PartOwner")) or me.IsHeld.Value do
									Struggle:FireServer(me)
									RagdollRemote:FireServer(hrp, 0)
									task.wait()
								end
							end)
							hrp.Anchored = true
							if not(AGWalk) then
								AGWalk = true
								while me.IsHeld.Value and task.wait() do hrp.CFrame = hrp.CFrame + hum.MoveDirection*0.43 end
							end
							hrp.Anchored = false; AntiGrabProc = false; AGWalk = false
						end
					end
				end)
				Cons["AGRagdoll"] = FWC(hum,"Ragdolled").Changed:Connect(function()
					if hum.Ragdolled.Value then
						for _,v in pairs(char:GetChildren()) do
							if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
								v.BallSocketConstraint.Enabled = false
								if v:FindFirstChild("RagdollLimbPart") then
									v.RagdollLimbPart.WeldConstraint.Enabled = false
								end
							end
						end
					end
				end)
				Cons["AGWeld"] = FWC(hrp, "WeldHRP").Changed:Connect(function()
					if hrp.WeldHRP.Enabled then
						while not(hum.Sit) do task.wait() end; hum.Sit = false;
						hum.AutoRotate = true; hum.HipHeight = 1
						while hrp.WeldHRP.Enabled and task.wait() do head.CFrame = hrp.CFrame + Vector3.new(0, 1.35, 0) end
						hum.HipHeight = 0
					end
				end)
				for _,v in pairs(char:GetChildren()) do
					if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
						v.BallSocketConstraint.Enabled = false
						if v:FindFirstChild("RagdollLimbPart") then
							v.RagdollLimbPart.WeldConstraint.Enabled = false
						end
					end
				end
				Cons["AGChar"] = me.CharacterAdded:Connect(function(char)
					local hrp, hum, head = FWC(char,"HumanoidRootPart"), FWC(char,"Humanoid"), FWC(char,"Head")
					Cons["AGHead"] = head.ChildAdded:Connect(function(PartOwner)
						if PartOwner.Name == "PartOwner" then
							if not(AntiGrabProc) then
								AntiGrabProc = true
								local save = hrp.CFrame; hum.Sit = false; Struggle:FireServer(me)
								task.spawn(function() 
									while (head and head:FindFirstChild("PartOwner")) or me.IsHeld.Value do
										Struggle:FireServer(me)
										RagdollRemote:FireServer(hrp, 0)
										task.wait()
									end
								end)
								hrp.Anchored = true
								if not(AGWalk) then
									AGWalk = true
									while me.IsHeld.Value and task.wait() do hrp.CFrame = hrp.CFrame + hum.MoveDirection*0.43 end
								end
								hrp.Anchored = false; AntiGrabProc = false; AGWalk = false
							end
						end
					end)
					Cons["AGRagdoll"] = FWC(hum,"Ragdolled").Changed:Connect(function()
						if hum.Ragdolled.Value then
							for _,v in pairs(char:GetChildren()) do
								if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
									v.BallSocketConstraint.Enabled = false
									if v:FindFirstChild("RagdollLimbPart") then
										v.RagdollLimbPart.WeldConstraint.Enabled = false
									end
								end
							end
						end
					end)
					Cons["AGWeld"] = FWC(hrp, "WeldHRP").Changed:Connect(function()
						if hrp.WeldHRP.Enabled then
							while not(hum.Sit) do task.wait() end; hum.Sit = false;
							hum.AutoRotate = true; hum.HipHeight = 1
							while hrp.WeldHRP.Enabled and task.wait() do head.CFrame = hrp.CFrame + Vector3.new(0, 1.35, 0) end
							hum.HipHeight = 0
						end
					end)
					for _,v in pairs(char:GetChildren()) do
						if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
							v.BallSocketConstraint.Enabled = false
							if v:FindFirstChild("RagdollLimbPart") then
								v.RagdollLimbPart.WeldConstraint.Enabled = false
							end
						end
					end
				end)
			else
				local char = me.Character or me.CharacterAdded:wait()
				for _,v in pairs(char:GetChildren()) do
					if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
						v.BallSocketConstraint.Enabled = false
						if v:FindFirstChild("RagdollLimbPart") then
							v.RagdollLimbPart.WeldConstraint.Enabled = true
						end
					end
				end
				Disc("AGHead")
				Disc("AGRagdoll")
				Disc("AGWeld")
				Disc("AGChar")
			end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Kill Dodge",
		CurrentValue = false,
		Flag = "KillDodge",
		Callback = function(Value)
			KillDodge = Value
			if KillDodge then
				local Plot = FindPlot()
				if Plot then
					if Plot.Name == "Plot1" then Tppos = Vector3.new(-533, -7, 90)
					elseif Plot.Name == "Plot2" then Tppos = Vector3.new(-483, -7, -164)
					elseif Plot.Name == "Plot3" then Tppos = Vector3.new(252, -7, 464)
					elseif Plot.Name == "Plot4" then Tppos = Vector3.new(509, 83, -339)
					else Tppos = Vector3.new(553, 123, -74) end
				else
					Tppos = Vector3.new(252, -7, 464)
				end
				Cons["KDChar"] = me.CharacterAdded:Connect(function(char)
					if KillDodge then
						local hrp = FWC(char,"HumanoidRootPart")
						local hum = FWC(char,"Humanoid")
						task.spawn(function()
							while KillDodge and not(me.InPlot.Value) and hum.Health ~= 0 and task.wait() do
								hrp.CFrame = CFrame.new(Tppos)
								hrp.Anchored = false
							end
						end)
						Cons["KDDied"] = hum.Died:Connect(function()
							if KillDodge then
								task.wait(2.8)
								while KillDodge and not(me.InPlot.Value) and hum.Health ~= 0 do
									for i = 1,3 do task.spawn(function() Struggle:FireServer(me) end) end
									task.wait()
								end
							end
						end)
					end
				end)
			else
				Disc("KDDied")
				Disc("KDChar")
			end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Anti Banana [SIT]",
		CurrentValue = false,
		Flag = "AntiBananaSit",
		Callback = function(Value)
			antibananaSit = Value
			while antibananaSit and task.wait() do
				local char = me.Character or me.CharacterAdded:Wait()
				local hum = char and FWC(char,"Humanoid")
				local hrp = char and FWC(char,"HumanoidRootPart")
				if hum and hrp then
					if hum.Health ~= 0 then 
						hum.Sit = true
						hum:ChangeState(Enum.HumanoidStateType.Running)
						local Vec = w.CurrentCamera.CFrame.LookVector
						hrp.CFrame = CFrame.new(hrp.Position, hrp.Position + Vector3.new(Vec.X, 0, Vec.Z))
					end
				end; task.wait(); hum:ChangeState(Enum.HumanoidStateType.Running)
			end
		end,
	})
	
	local Toggle = Tab:CreateToggle({
		Name = "Anti Ragdoll (On Blob)",
		CurrentValue = false,
		Flag = "AntiRagdoll",
		Callback = function(Value)
			AntiRagBlob = Value
			RagdolledSit = false
			if AntiRagBlob then
				local char = me.Character or me.CharacterAdded:wait()
				local hum = char and FWC(char,"Humanoid")
				local HRP = char and FWC(char,"HumanoidRootPart")
				Cons["ARSeat"] = hum:GetPropertyChangedSignal("SeatPart"):Connect(function()
					if hum.SeatPart and hum.SeatPart.Parent.Name == "CreatureBlobman" and not RagdolledSit then
						RagdolledSit = true
						local Seat = hum.SeatPart
						while not hum.Sit do task.wait() end
						RagdollRemote:FireServer(HRP,3)
						while not hum.Ragdolled.Value and not hum.Sit do task.wait() end
						task.wait(0.4)
						hum.Sit = false
						Seat:Sit(hum)
						task.delay(0.25,function()
							while hum and hum.SeatPart do
								RagdollRemote:FireServer(me.Character.HumanoidRootPart,1)
								task.wait(0.05)
							end
							RagdolledSit = false
						end)
					end
				end)
				Cons["ARChar"] = me.CharacterAdded:Connect(function(char)
					Cons["ARSeat"] = hum:GetPropertyChangedSignal("SeatPart"):Connect(function()
						if hum.SeatPart and hum.SeatPart.Parent.Name == "CreatureBlobman" and not RagdolledSit then
							RagdolledSit = true
							local Seat = hum.SeatPart
							while not hum.Sit do task.wait() end
							RagdollRemote:FireServer(HRP,3)
							while not hum.Ragdolled.Value and not hum.Sit do task.wait() end
							task.wait(0.4)
							hum.Sit = false
							Seat:Sit(hum)
							task.delay(0.25,function()
								while hum and hum.SeatPart do
									RagdollRemote:FireServer(me.Character.HumanoidRootPart,1)
									task.wait(0.05)
								end
								RagdolledSit = false
							end)
						end
					end)
				end)
			else
				Disc("ARSeat")
				Disc("ARChar")
			end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Anti Explode",
		CurrentValue = false,
		Flag = "AntiExplode",
		Callback = function(Value)
			AntiExplode = Value
			if AntiExplode then
				Cons["AntiExplode"] = BombEvent.OnClientEvent:Connect(function(table,pos)
					if AntiExplode then
						local model = table["Model"]
						local char = me.Character
						local hrp = FWC(char,"HumanoidRootPart")
						local hum = FWC(char,"Humanoid")
						task.spawn(function()
							if table["Radius"] + 10 > (pos - hrp.Position).Magnitude then
								hrp.Anchored = true
								task.wait()
								hum:ChangeState(Enum.HumanoidStateType.Running)
								hrp.Anchored = false
								for i = 1,4 do
									local limb = char:FindFirstChild(Limbs[i])
									if limb then limb.RagdollLimbPart.CanCollide = false end
								end
							end
						end)
					end
				end)
			else
				Disc("AntiExplode")
			end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Anti Fire",
		CurrentValue = false,
		Flag = "AntiFire",
		Callback = function(Value)
			AntiFire = Value
			if AntiFire then
				Cons["AFChar"] = me.CharacterAdded:Connect(function(char)
					if AntiFire then
						local HRP = FWC(char,"HumanoidRootPart")
						local Hum = FWC(char,"Humanoid")
						local FireDeb = FWC(Hum,"FireDebounce")
						Cons["AFDeb"] = FireDeb:GetPropertyChangedSignal("Value"):Connect(function()
							if AntiFire then
								local back = w.AF.CFrame
								while FireDeb.Value do
									w.AF.CFrame = HRP.CFrame + Vector3.new(math.random(0.1,1),math.random(0.1,1),math.random(0.1,1))
									task.wait()
								end
								w.AF.CFrame = back
							end
						end)
					end
				end)
				local char = me.Character or me.CharacterAdded:wait()
				local HRP = FWC(char,"HumanoidRootPart")
				local Hum = FWC(char,"Humanoid")
				local FireDeb = FWC(Hum,"FireDebounce")
				Cons["AFDeb"] = FireDeb:GetPropertyChangedSignal("Value"):Connect(function()
					if AntiFire then
						local back = w.AF.CFrame
						while FireDeb.Value do
							w.AF.CFrame = HRP.CFrame + Vector3.new(math.random(0.1,1),math.random(0.1,1),math.random(0.1,1))
							task.wait()
						end
						w.AF.CFrame = back
					end
				end)
			else
				Disc("AFDeb")
				Disc("AFChar")
			end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Anti Paint",
		CurrentValue = false,
		Flag = "AntiPaint",
		Callback = function(Value)
			AntiPaint = Value
			for _,plr in pairs(Players:GetPlayers()) do
				for _,toy in pairs(w[plr.Name.."SpawnedInToys"]:GetChildren()) do
					if toy.Name == "BucketPaint" or toy.Name == "FoodHotSauce" or toy.Name == "ToiletGold" or toy.Name == "ToiletWhite" then
						task.spawn(function()
							for _,prt in pairs(toy:GetChildren()) do
								if prt.Name == "PaintPlayerPart" or prt.Name == "FirePlayerPart" then prt:Destroy() end
							end
						end)
					end
				end
			end
			for i = 1,5 do
				for _,toy in pairs(w.PlotItems["Plot"..i]:GetChildren()) do
					if toy.Name == "BucketPaint" or toy.Name == "FoodHotSauce" or toy.Name == "ToiletGold" or toy.Name == "ToiletWhite" then
						task.spawn(function()
							for _,prt in pairs(toy:GetChildren()) do
								if prt.Name == "PaintPlayerPart" or prt.Name == "FirePlayerPart" then prt:Destroy() end
							end
						end)
					end
				end
			end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Flying Reset",
		CurrentValue = false,
		Flag = "FlyingReset",
		Callback = function(Value)
			FlyingReset = Value
			if FlyingReset then
				Cons["GameNotify"] = GameNotify.OnClientEvent:Connect(function(Type)
					if Type == "Flying" then
						Struggle:FireServer(me)
						FWC(me.Character,"Humanoid").Health = 0
					end
				end)
			else
				Disc("GameNotify")
			end
		end,
	})

	local Button = Tab:CreateButton({
		Name = "Shuriken Anti-Kick",
		Callback = function()
			local ShurModel = toy_spawn("NinjaShuriken",FWC(me.Character,"Head"),"headup")
			local Part = FWC(FWC(me.Character,"HumanoidRootPart",3),"FirePlayerPart",3)
			local Sticky = FWC(ShurModel,"StickyPart")
			while Sticky and Sticky.Parent and not Sticky:FindFirstChild("PartOwner") do
				task.spawn(grab,Sticky)
				task.wait()
			end
			print("ez4")
			StickyPartEvent:FireServer(Sticky,Part,CFrame.Angles(0, math.rad(90), math.rad(90)))
			for _,v in pairs(ShurModel:GetChildren()) do
				if v:IsA("BasePart") then 
					v.CanQuery = false
					if v.Transparency == 0 then v.Transparency = 0.7 end
				end
			end
		end,
	})

	local Button = Tab:CreateButton({
		Name = "Input-Lag",
		Callback = function()
			task.spawn(grab,FWC(me.Character,"HumanoidRootPart"))
		end,
	})

local Tab = Window:CreateTab("Blobman", 4483362458)
local Section = Tab:CreateSection("Kick")

	local Toggle = Tab:CreateToggle({
		Name = "Kick Notify",
		CurrentValue = false,
		Flag = "KickNotify",
		Callback = function(Value)
			FlagKickOn = Value
			if FlagKickOn then
				Cons["KickNotify"] = w.ChildAdded:Connect(function(part)
					if part.Name == "BlackHoleKick" then
						kicklist = {}
						kicklistDis = {}
						part.Name = 'BlackHoleDetected'
						for _, player in pairs(Players:GetPlayers()) do 
							table.insert(kicklist, player.Name)
							table.insert(kicklistDis, player.DisplayName)
						end
						task.wait(3.25)
						if #kicklist - #Players:GetPlayers() > 1 then
							Rayfield:Notify({
							Title = "Error Kicked " .. #kicklist - #Players:GetPlayers(),
							Content = "Script dont understand who get kicked",
							Duration = 5,
							Image = 4483362458,
							})
							return
						end
						for i, player in Players:GetPlayers() do
							if player.Name ~= kicklist[i] then
								Rayfield:Notify({
								Title = "Kicked",
								Content = kicklistDis[i] .. ' (' .. kicklist[i] .. ') get kicked!',
								Duration = 5,
								Image = 4483362458,
								})
								return
							end
							if #kicklist - #Players:GetPlayers() == 1 and i+1 == #kicklist then
								Rayfield:Notify({
								Title = "Kicked",
								Content = kicklistDis[i+1] .. ' (' .. kicklist[i+1] .. ') get kicked!',
								Duration = 5,
								Image = 4483362458,
								})
								return
							end
						end
					end
				end)
			else
				Disc("KickNotify")
			end
		end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Loop Kick",
		CurrentValue = false,
		Flag = "LoopKick",
		Callback = function(Value)
			kfmb = Value
			local MyFriends, TargetFriends, MyBlob = {}, {}
			for _,plr in pairs(Players:GetPlayers()) do
				if plr:IsFriendsWith(me.UserId) then table.insert(MyFriends,plr) end
				if Players:FindFirstChild(TargetPlayer) and plr:IsFriendsWith(Players:FindFirstChild(TargetPlayer).UserId) then table.insert(TargetFriends,plr) end
			end
			local Time = tick()
			while kfmb and task.wait() do
				local mychar = me.Character or me.CharacterAdded:Wait()
				local myHRP = FWC(mychar,"HumanoidRootPart")
				local myhum = FWC(mychar,"Humanoid")
				if myhum.SeatPart then MyBlob = myhum.SeatPart.Parent end
				for i,plr in pairs(Players:GetPlayers()) do
					if plr and (TargetType == "Target" and plr.Name == TargetPlayer) or (TargetType == "All" and not(table.find(MyFriends,plr)) and plr ~= me) or (TargetType == "Target Friends" and plr ~= me and not(table.find(MyFriends,plr)) and (plr.Name == TargetPlayer or (Players:FindFirstChild(TargetPlayer) and table.find(TargetFriends,plr)))) then
						local char = plr.Character
						local hum = char and FWC(char, "Humanoid",2)
						local HRP = char and FWC(char, "HumanoidRootPart",2)
						local Head = char and FWC(char, "Head",2)
						if hum and hum.Health == 0 then
							if TargetType == "Target" then
								task.defer(function()
									while GrabType == "Kill" and hum.Health == 0 and kfmb do
										if myhum.SeatPart then
											myHRP.CFrame = CFrame.new(0,200,0)
										end
										task.wait()
									end
								end)
								char = plr.CharacterAdded:Wait()
								hum = FWC(char, "Humanoid",2)
								HRP = FWC(char, "HumanoidRootPart",2)
								Head = char and FWC(char, "Head",2)
								task.wait(0.15)
							end
						end
						if HRP and hum then
							if kfmb and MyBlob and MyBlob.Parent then
								if GrabType == "Default" and (myHRP.Position - HRP.Position).Magnitude < 40 then
									blob_kick(MyBlob,HRP,"Left","Default")
									task.wait()
									blob_kick(MyBlob,HRP,"Right","Default")
								elseif GrabType == "Default New" and (myHRP.Position - HRP.Position).Magnitude < 40 then
									blob_kick(MyBlob,HRP,"Left","Default")
									blob_kick(MyBlob,HRP,"Right","Default")
									task.wait()
									blob_kick(MyBlob,HRP,"Left","DDrop")
									blob_kick(MyBlob,HRP,"Right","DDrop")
								elseif GrabType == "Lock" then
									task.defer(function()
										if isnetworkowner(HRP) then
											if tick()-Time > 0.5 then
												hum.Sit = true
												task.wait(0.16)
												hum.Sit = false
												Time = tick()
											end  
											local LD = MyBlob:FindFirstChild("LeftDetector")
											if LD then HRP.CFrame = LD.CFrame end
											for _,v in pairs(char:GetChildren()) do
												if v:IsA("BasePart") then v.Velocity = Vector3.new() end
											end
											if (myHRP.Position - HRP.Position).Magnitude < 40 and hum.SeatPart then
												grab(HRP)
											end
										end
									end)
									blob_kick(MyBlob,HRP,"Left","Default")
									task.wait(0.05)
									blob_kick(MyBlob,HRP,"Left","Release")
								elseif GrabType == "Kill" then
									local LD = MyBlob:FindFirstChild("LeftDetector")
									local LW = LD:FindFirstChild("LeftWeld")
									if LD and LW then
										while LW.Attachment0 ~= HRP.RootAttachment and not(isnetworkowner(HRP)) do
											while hum.SeatPart do task.spawn(grab,HRP); task.wait() end
											for i = 1,4 do
												if myhum.SeatPart then myHRP.CFrame = HRP.CFrame - Vector3.new(0, 10, 0) end
												blob_kick(MyBlob,HRP,"Left","Default")
												task.wait(0.05)
												blob_kick(MyBlob,HRP,"Left","Release")
												hum.Health = 0
												task.wait()
											end
										end
									end
								end
							end
						end
					end
				end
			end
		end,
	})

	local Dropdown = Tab:CreateDropdown({
	Name = "Grab type",
	Options = {"Default", "Default New", "Lock", "Kill"},
	CurrentOption = {"Default"},
	MultipleOptions = false,
	Flag = "GrabType",
	Callback = function(Options)
		GrabType = Options[1]
	end,
	})

	local Dropdown = Tab:CreateDropdown({
	Name = "Target type",
	Options = {"Target", "Target Friends", "All"},
	CurrentOption = {"Target"},
	MultipleOptions = false,
	Flag = "TargetType",
	Callback = function(Options)
		TargetType = Options[1]
	end,
	})

	local Toggle = Tab:CreateToggle({
		Name = "Anti-Sticky",
		CurrentValue = false,
		Flag = "AntiSticky",
		Callback = function(Value)
			AntiStick = Value
			while AntiStick and task.wait() do
				local Plot, SRP, WD = FindPlot()
				local WD = BackPack:FindFirstChild("WD")
				if not WD and Plot then WD = Plot:FindFirstChild("WD") end

				if not WD then
					local WDToy, House = toy_spawn("SprayCanWD",FWC(me.Character,"Head"),"headup")
					WD = WDToy
					local Hitbox = FWC(WD,"Hitbox")
					while Hitbox and not Hitbox:FindFirstChild("PartOwner") or (Hitbox:FindFirstChild("PartOwner") and Hitbox:FindFirstChild("PartOwner").Value ~= me.Name) do
						task.spawn(grab,Hitbox)
						task.wait()
					end
					FWC(WD,"StickyRemoverPart")
					for _,v in pairs(WD:GetChildren()) do
						if v.Name == "StickyRemoverPart" and v.Size == Vector3.new(2, 2, 2) then
							SRP = v
							v.Name = "SRP"
							WD.Name = "WD"
						end
					end
					if not House then
						Hitbox.CFrame = CFrame.new(30, 15, -226)
					end
				else
					SRP = FWC(WD,"SRP")
				end
				local time = tick()
				while task.wait() and AntiStick do
					local plr = Players:FindFirstChild(TargetPlayer)
					local char = plr and plr.Character
					local Torso = char and FWC(char,"HumanoidRootPart",2)
					if Torso and SRP and WD and tick()-time > 0.5 then
						if not WD.Parent then break end
						firetouchinterest(SRP,Torso,0)
						time = tick()
					end
				end
			end
		end,
	})

	local Button = Tab:CreateButton({
		Name = "Try to Kick Target",
		Callback = function()
			if not(TryProcess) then
				local mychar = me.Character or me.CharacterAdded:wait()
				local char = Players[TargetPlayer].Character or Players[TargetPlayer].CharacterAdded:wait()
				local myHRP, HRP = FWC(mychar, "HumanoidRootPart"), FWC(char, "HumanoidRootPart")
				local Head, myHum, Hum = FWC(char, "Head"), FWC(mychar, "Humanoid"), FWC(char, "Humanoid")
				local MyBlob = myHum.SeatPart.Parent
				local Weld = MyBlob.LeftDetector.LeftWeld
				for _,v in pairs(HRP:GetChildren()) do if v.Name == "BodyPosition" then v:Destroy() end end
				local BackUp = myHRP.CFrame
				local BP = Instance.new("BodyPosition")
				BP.Parent = HRP
				BP.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
				BP.D = 105
				BP.P = 8000
				BP.Position = BackUp.Position + Vector3.new(0, 12.5, 0)
				local TryProcess = true
				while Weld.Attachment0 ~= HRP.RootAttachment do
					if myHum.SeatPart then
						myHRP.CFrame = HRP.CFrame - Vector3.new(0, 7.5, 0)
						blob_kick(MyBlob,HRP,"Left","Default")
						task.wait()
						blob_kick(MyBlob,HRP,"Left","Release")
					else
						TryProcess = false
						return
					end
				end
				myHRP.CFrame = BackUp

				task.wait(0.15)

				while not(Head:FindFirstChild("PartOwner") and isnetworkowner(HRP)) and myHum.SeatPart do
					if myHum.SeatPart then
						for i = 1,3 do task.spawn(grab,HRP) end
					else
						TryProcess = false
						return
					end
					task.wait()
				end
				task.wait()
				Destroy_Line(HRP)

				task.delay(0.5,function() BP:Destroy() end)
				while HRP.Parent and HRP and Hum.Health ~= 0 and myHum.SeatPart do 
					blob_kick(MyBlob,HRP,"Left","Default")
					blob_kick(MyBlob,HRP,"Right","Default")
					task.wait()
					blob_kick(MyBlob,HRP,"Left","DDrop")
					blob_kick(MyBlob,HRP,"Right","DDrop")
				end
				TryProcess = false
			end
		end,
	})

local Section = Tab:CreateSection("Gucci")

	local Keybind = Tab:CreateKeybind({
		Name = "Gucci Anti-Grab",
		CurrentKeybind = "J",
		HoldToInteract = false,
		Flag = "Gucci",
		Callback = function(Keybind)
			for _,toy in pairs(BackPack:GetChildren()) do if toy.Name == "Gucci" then GucciNotify.Visible = true end end
			for _,plot in pairs(w.PlotItems:GetChildren()) do
				if plot.Name ~= "PlayersInPlots" then 
					for _,toy in pairs(plot:GetChildren()) do
						if toy.Name == "Gucci" then GucciNotify.Visible = true end
					end
				end
			end
			local char = me.Character or me.CharacterAdded:wait()
			local hum = FWC(char,"Humanoid")
			while GucciNotify.Visible and task.wait() do 
				if NoBox.Text == "Success" then return
				elseif YesBox.Text == "Success" then
					hum.Sit = true
					task.wait()
					hum.Sit = false
					task.wait()
				end
			end
			task.spawn(function()
				local time = tick(); while tick()-time < 1 do 
					for _,v in pairs(me.Character:GetChildren()) do if v:IsA('BasePart') then v.Velocity = Vector3.new() end end
					task.wait()
				end
			end)
			local autoGucciT, sitJumpT, Blob, BHead = true, false
			task.spawn(function()
				while not Blob do task.wait() end
				BHead = FWC(Blob,"Head")
				local HitBox = FWC(Blob,"GrabbableHitbox")
				while not BHead:FindFirstChild("PartOwner") or (BHead:FindFirstChild("PartOwner") and BHead:FindFirstChild("PartOwner").Value ~= me.Name) do
					task.spawn(grab, HitBox)
					task.wait()
				end
			end)
			local hrp = FWC(char,"HumanoidRootPart")
			Blob = toy_spawn("CreatureBlobman", hrp.CFrame*CFrame.new(0,0,-5),Vector3.new(0, -15.716, 0))
			local Seat = FWC(Blob,"VehicleSeat")
			task.defer(function()
				if not(char or hum) then return end
				local startTime = tick()
				while autoGucciT and tick()-startTime<0.175 do
					if Blob then
						if Seat and Seat.Occupant ~= hum then Seat:Sit(hum) end
					end
					task.wait(0.35)
					if char and hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
					task.wait(0.35)
				end
				autoGucciT = false
				sitJumpT = false
			end)
			sitJumpT = true
			task.defer(function()
				if ragdollLoopD then return end
				ragdollLoopD = true
				while sitJumpT do
					if char and hrp then
						RagdollRemote:FireServer(hrp,0.095)
					end
					task.wait()
				end
				ragdollLoopD = false
			end)
			local index
			task.wait(1)
			hum.Sit = false
			Blob.Name = "Gucci"
			for i,v in pairs(BackPack:GetChildren()) do if v.Name == "Gucci" then index = i; break end end
			for _,v in pairs(Blob:GetChildren()) do if v:IsA("BasePart") then v.CanCollide = false; v.CanTouch = false; v.CanQuery = false end end
			task.defer(function() while task.wait() and Blob and BHead do BHead.CFrame = CFrame.new(-1e+22,-1e+22,math.random(0,100000)) end end)
			local contents = me.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents
			for i,v in ipairs(contents:GetChildren()) do
				print(v.Name, i, index)
				if v.Name == "CreatureBlobman" and i == index then
					local view = v.ViewItemButton
					view.Text = "GUCCI"
					view.TextScaled = true
					view.LowResImage.Image = ""
				end
			end
		end,
	})

	local Button = Tab:CreateButton({
		Name = "Delete Target Gucci",
		Callback = function()
			local Kunais = {}
			local BlobH
			local KunaiDeleter = 0
			if DGType == "BackPack" then
				BlobH = w[TargetPlayer.."SpawnedInToys"]:FindFirstChild("CreatureBlobman"):WaitForChild("Weight")
			else
				for i = 1, 5 do
					local PlotOwners = w.Plots["Plot" .. i].PlotSign.ThisPlotsOwners
					if PlotOwners:FindFirstChild("Value") then
						for _,value in pairs(PlotOwners:GetChildren()) do
							if value.Value == TargetPlayer then
								BlobH = w.PlotItems["Plot"..i]:FindFirstChild("CreatureBlobman"):WaitForChild("Weight")
							end
						end
					end
				end
			end
			while KunaiDeleter < 10 and task.wait() do
				local KunaiModel, InHouse = toy_spawn("NinjaKunai",FWC(me.Character,"Head"),"headup")
				local sound = FWC(KunaiModel,"SoundPart")
				local sticky = FWC(KunaiModel,"StickyPart")
				while sound and sound.Parent and not(sound:FindFirstChild("PartOwner")) or (sound:FindFirstChild("PartOwner") and sound:FindFirstChild("PartOwner").Value ~= me.Name) do
					task.spawn(grab,sound)
					task.wait()
				end
				for _,prt in pairs(sound.Parent:GetChildren()) do if prt:IsA("BasePart") then prt.CanCollide = false end end
				local BP = Instance.new("BodyPosition")
				BP.Parent = sound
				BP.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
				BP.D = 105
				BP.P = 8000
				BP.Position = Vector3.new(5000, 10000+KunaiDeleter*5, 5000)
				KunaiModel.Name = InHouse and "House" or "BackPack"
				Kunais[#Kunais+1] = KunaiModel
				KunaiDeleter = KunaiDeleter + 1
				if KunaiDeleter == 10 then
					for _,Model in Kunais do
						local sticky = FWC(Model,"StickyPart")
						local sound = FWC(Model,"SoundPart")
						if Model.Name == "House" and DGType == "House" then
							StickyPartEvent:FireServer(sticky,BlobH,CFrame.new(0,0,0,0,0,0,0,0,0,0,0,0))
						elseif Model.Name == "House" and DGType == "BackPack" then
							task.defer(function() while sound and sound.Parent and task.wait() do sound.CFrame = BlobH.CFrame end end)
						elseif Model.Name == "BackPack" and DGType == "BackPack" then
							StickyPartEvent:FireServer(sticky,BlobH,CFrame.new(0,0,0,0,0,0,0,0,0,0,0,0))
						else task.defer(function() while sound and sound.Parent and task.wait() do sound.CFrame = BlobH.CFrame end end) end
					end
					break
				end
			end
		end,
	})

	local List = Tab:CreateDropdown({
		Name = "Delete Gucci Type",
		Options = {"BackPack","House"},
		CurrentOption = {"BackPack"},
		MultipleOptions = false,
		Flag = "DeleteGucciType",
		Callback = function(Options)
			DGType = Options[1]
		end,
	})

local Section = Tab:CreateSection("Others")

local Toggle = Tab:CreateToggle({
		Name = "Auto Sit",
		CurrentValue = false,
		Flag = "AutoSit",
		Callback = function(Value)
			AutoSit = Value
			local blob
			while AutoSit do
				if not blob then 
					blob = BackPack:FindFirstChild("CreatureBlobman")
				elseif not blob.Parent then
					blob = BackPack:FindFirstChild("CreatureBlobman")
				end
				local char = me.Character or me.CharacterAdded:wait()
				local hum = char and char:FindFirstChild("Humanoid")
				if not blob then
					Plot = FindPlot()
					if Plot then blob = Plot:FindFirstChild("CreatureBlobman") end
					task.wait(0.05)
				elseif not blob.Parent then
					Plot = FindPlot()
					if Plot then blob = Plot:FindFirstChild("CreatureBlobman") end
					task.wait(0.05)
				end
				if hum and hum.Health ~= 0 and blob then
					local Seat = blob:FindFirstChild("VehicleSeat")
					if Seat and not Seat.Occupant then
						Seat:Sit(hum)
					end
				end
				task.wait()
			end
		end,
	})

local Tab = Window:CreateTab("Lists", 4483362458)
local Section = Tab:CreateSection("Lists")

	local FL = Tab:CreateDropdown({
		Name = "Fire/Nigger List",
		Options = {},
		CurrentOption = {""},
		MultipleOptions = false,
		Flag = "",
		Callback = function(Options)
		end,
	})

	local CheckUpd = Tab:CreateLabel("", 4483362458, Color3.fromRGB(34, 34, 34), false)

	local UpdL = Tab:CreateButton({
		Name = "Update Lists",
		Callback = function()
			BananaList = {}
			FireList = {}
			for _,plr in ipairs(Players:GetPlayers()) do
				if w:FindFirstChild(plr.Name.."SpawnedInToys") then
					for _,toys in pairs(w[plr.Name.."SpawnedInToys"]:GetChildren()) do
						if (toys.Name == "Campfire" or toys.Name == "OvenDarkGray" or toys.Name == "OvenRusty") and toys:FindFirstChild("SoundPart") and toys:FindFirstChild("SoundPart").Position.Y > 480 and plr ~= me then
							table.insert(FireList, plr.Name.." ("..plr.DisplayName..")")
						end
					end -- Y - 420
				end
			end
			BL:Refresh(BananaList)
			FL:Refresh(FireList)
			CheckUpd:Set("Lists Refreshed!", 4483362458, Color3.fromRGB(34, 70, 34), false)
			task.wait(1)
			CheckUpd:Set("", 4483362458, Color3.fromRGB(34, 34, 34), false)
		end,
	})
	
local Tab = Window:CreateTab("ESP", 4483362458)
local Section = Tab:CreateSection("PCLD ESP")

	local Toggle = Tab:CreateToggle({
		Name = "PCLD ESP",
		CurrentValue = false,
		Flag = "PCLDESP",
		Callback = function(Value)
			PCLDFlag = Value
			if PCLDFlag then
				Cons["PCLDChild"] = w.ChildAdded:Connect(function(part)
					if part.Name == "PlayerCharacterLocationDetector" and PCLDFlag then
						part.Transparency = PCLDTrans
						part.Color = PCLDColor
					end
				end)
				for _,prt in pairs(w:GetChildren()) do
					if prt.Name == "PlayerCharacterLocationDetector" then
						if PCLDFlag then prt.Transparency = PCLDTrans; prt.Color = PCLDColor else prt.Transparency = 1 end
					end
				end
				while PCLDFlag and not(w:FindFirstChild("PCLDme")) and task.wait() do
					local char = me.Character or me.CharacterAdded:Wait()
					local torso = FWC(char,"Torso",3)
					if torso and char then
						for _,prt in pairs(w:GetChildren()) do
							if prt.Name == "PlayerCharacterLocationDetector" and (prt.Position - torso.Position + Vector3.new(0,0.51,0)).Magnitude < 0.5 then prt.Name = "PCLDme"; prt.Transparency = 1 end
						end
					end
				end
			else
				Disc("PCLDChild")
			end
		end,
	})

	local ColorPicker = Tab:CreateColorPicker({
		Name = "PCLD Color Picker",
		Color = Color3.fromRGB(255,0,0),
		Flag = "PCLDColor",
		Callback = function(Value)
			PCLDColor = Value
			if PCLDFlag then
				for _,prt in pairs(w:GetChildren()) do
					if prt.Name == "PlayerCharacterLocationDetector" then
						prt.Color = PCLDColor
					end
				end
			end
		end,
	})

	local Slider = Tab:CreateSlider({
		Name = "PCLD Transparency",
		Range = {0.1, 1},
		Increment = 0.1,
		Suffix = "",
		CurrentValue = 0.5,
		Flag = "PCLDTrans",
		Callback = function(Value)
			PCLDTrans = Value
			if PCLDFlag then
				for _,prt in pairs(w:GetChildren()) do
					if prt.Name == "PlayerCharacterLocationDetector" then
						prt.Transparency = PCLDTrans
					end
				end
			end
		end,
	})

local Tab = Window:CreateTab("Lag", 4483362458)
local Section = Tab:CreateSection("Lag")

	local Toggle = Tab:CreateToggle({
		Name = "Lag Server [Lines]",
		CurrentValue = false,
		Flag = "LagServer",
		Callback = function(Value)
			LagServer = Value
			while LagServer do
				for i = 1,LinesCount do
					CreateGrabEvent:FireServer(w.SpawnLocation,w.SpawnLocation.CFrame)
				end
				task.wait(1)
			end
		end,
	})

	local Slider = Tab:CreateSlider({
		Name = "Lines Count",
		Range = {50,3000},
		Increment = 1,
		Suffix = "Per second",
		CurrentValue = 250,
		Flag = "LagCount",
		Callback = function(Value)
			LinesCount = Value
		end,
	})

UIS.InputBegan:Connect(function(input, chatting)
    if chatting then return end

	if input.KeyCode == Enum.KeyCode.Space then
		local char = me.Character
		local hum = char.Humanoid
		if hum.SeatPart and hum.Ragdolled.Value and (game.CoreGui:FindFirstChild("HUI") and not(game.CoreGui.HUI.Rayfield.Drag.Interact.Visible) or not(game.CoreGui.Rayfield.Drag.Interact.Visible)) then
			hum.Sit = false
		end
	elseif input.KeyCode == Enum.KeyCode.LeftShift and ShiftSpeed then
		local char = me.Character
		local hum = char.Humanoid
        if not char.BobbingAndCrouch.Disabled then char.BobbingAndCrouch.Disabled = true end
        if hum.WalkSpeed ~= ShiftValue then
            hum.WalkSpeed = ShiftValue
        else
            hum.WalkSpeed = 16
        end
        char.BobbingAndCrouch.Disabled = false
	elseif input.KeyCode == Enum.KeyCode.F1 then
		local char = me.Character
		local hum = char.Humanoid
		if hum.SeatPart then
			local toy = hum.SeatPart.Parent
			if toy.Name == "CreatureBlobman" then
				toy.HumanoidRootPart.Anchored = not(toy.HumanoidRootPart.Anchored)
			end
		end
	elseif input.KeyCode == Enum.KeyCode.LeftControl then
		local char = me.Character
		local hum = char.Humanoid
		if not hum.SeatPart and not(me.Character:FindFirstChild("Left Leg") and me.Character:FindFirstChild("Right Leg")) then
			NoLegSit = not(NoLegSit)
			if NoLegSit and (game.CoreGui:FindFirstChild("HUI") and not(game.CoreGui.HUI.Rayfield.Drag.Interact.Visible) or not(game.CoreGui.Rayfield.Drag.Interact.Visible)) then
				me.Character.Humanoid.HipHeight = 0
				me.Character.Humanoid.WalkSpeed = 5
			else
				me.Character.Humanoid.WalkSpeed = 16
			end
		end
	end
end)

Mouse.KeyDown:Connect(function(key)
	if key == "y" or key == "n" and GucciNotify.Visible then
		local Box = ""
		if key == "y" then Box = YesBox else Box = NoBox end
		Box.Text = "Success"; task.wait(0.4)
		GucciNotify.Visible = false
		Box.Text = "[ "..string.upper(key).." ]"
	end
end)

Players.PlayerRemoving:Connect(function(plr)
	if plr.Name == TargetPlayer then
		TargetPlayerView = TargetPlayerView.." [LEFT]"
		Label:Set(TargetPlayerView, 4483362458, Color3.fromRGB(34, 34, 34), false)
		Rayfield:Notify({
			Title = "Target Left",
			Content = plr.DisplayName .. ' (' .. plr.Name .. ') Left',
			Duration = 4,
			Image = 7743876054,
		})
	end
end)

Players.PlayerAdded:Connect(function(plr)
	task.defer(function()
		w:WaitForChild(plr.Name.."SpawnedInToys").ChildAdded:Connect(function(toy)
			if toy.Name == "CreatureBlobman" then
				AntiKick(toy)
			end
		end)
	end)
	if plr.Name == TargetPlayer then
		TargetPlayerView = "TargetPlayer : " .. plr.DisplayName .. ' (' .. plr.Name .. ')'
		Label:Set(TargetPlayerView, 4483362458, Color3.fromRGB(34, 34, 34), false)
		Rayfield:Notify({
			Title = "Target Joined",
			Content = plr.DisplayName .. ' (' .. plr.Name .. ') Joined',
			Duration = 4,
			Image = 7743876054,
		})
	end
end)

me.CharacterAdded:Connect(function(char)
	char:WaitForChild("Torso"):GetPropertyChangedSignal("Transparency"):Connect(function()
		if char.Torso.Transparency > 0 then
			task.wait(0.25)
			for _,v in pairs(char:GetChildren()) do
				if v:IsA("BasePart") and v.Name ~= "CamPart" and v.Name ~= "HumanoidRootPart" then v.Transparency = 0 end
				if v:IsA("Accessory") and v.Name ~= "TypingKeyboardMyWorld" then for _,b in pairs(v:GetChildren()) do b.Transparency = 0 end end
			end
		end
	end)
end)

w.DescendantAdded:Connect(function(Part)
	if AntiPaint then
		if Part.Name == "BucketPaint" or Part.Name == "FoodHotSauce" or Part.Name == "ToiletGold" or Part.Name == "ToiletWhite" then
			task.spawn(function()
				task.wait(0.25)
				for _,prt in pairs(Part:GetChildren()) do
					if prt.Name == "PaintPlayerPart" or prt.Name == "FirePlayerPart" then prt:Destroy() end
				end
			end)
		end
	end
	if Part.Name == "CreatureBlobman" then
		task.defer(AntiKick, Part)
	elseif Part.Name == "Coin5" or Part.Name == "Coin10" or Part.Name == "Coin25" then
		task.wait()
		Part:Destroy()
	end	
end)

for i = 1,5 do
	for _,toy in pairs(w.PlotItems["Plot"..i]:GetChildren()) do
		if toy.Name == "CreatureBlobman" then
			AntiKick(toy)
		end
	end
end

for _,plr in pairs(Players:GetPlayers()) do
	if plr ~= me then
		for _,toy in pairs(w[plr.Name.."SpawnedInToys"]:GetChildren()) do
			if toy.Name == "CreatureBlobman" then
				AntiKick(toy)
			end
		end
	end
end

me.CharacterAdded:Connect(function(char)
	task.defer(function()
		task.spawn(function()
			FWC(char, "HumanoidRootPart"):GetPropertyChangedSignal("Massless"):Connect(function()
				for _,v in pairs(char:GetChildren()) do
					if v:IsA("BasePart") then v.Massless = false end
				end
				local HRP = char and FWC(char, "HumanoidRootPart")
				if HRP.Massless then
					local hum = char and FWC(char, "Humanoid")
					local CamPart = char and FWC(char, "CamPart",2)
					local Head = char and FWC(char, "Head")
					while char and hum and HRP.Massless and Head.Massless do
						for _,v in pairs(char:GetChildren()) do
							if v:IsA("BasePart") then v.Massless = false; task.wait() end
						end
						if HRP:FindFirstChild("RootAttachment") and CamPart then HRP.RootAttachment.Parent = CamPart end
						if hum.Sit and not hum.SeatPart then hum.Sit = false end
						task.wait(0.1)
					end
				end
			end)
			while FWC(char, "HumanoidRootPart").Massless do
				for _,v in pairs(char:GetChildren()) do 
					if v:IsA("BasePart") then 
						v.Massless = false
						v.Velocity = Vector3.new()
					end
					task.wait()
				end
			end
		end)
        local time1 = tick()
        task.spawn(function()
            while tick()-time1 < 1.25 do
                for _,v in pairs(char:GetChildren()) do if v:IsA("BasePart") then v.Velocity = Vector3.new() end end
				task.wait()
            end
        end)
		task.wait(1)
		local HRP = FWC(char,"HumanoidRootPart")
		local root = FWC(HRP,"RootAttachment",2)
		if root then 
			local Cam = FWC(char,"CamPart")
			root.Parent = Cam
		end
	end)
end)

if w.Map.AlwaysHereTweenedObjects.Ocean.Object:FindFirstChild("ObjectModel") then
	for _,ocean in pairs(w.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel:GetChildren()) do
		ocean.CanTouch = true
		local Clone = Instance.new("Part")
		Clone.CFrame = ocean.CFrame
		Clone.Parent = w.Map.AlwaysHereTweenedObjects.Ocean.Object
		Clone.Anchored = true
		Clone.Transparency = 1
		Clone.Size = Vector3.new(ocean.Size.X,1,ocean.Size.Z)
		Clone.Name = "CloneOcean"
		Clone.CanTouch = true
		Clone.CanCollide = false
	end
end

if not PCLDColor or not PCLDTrans then PCLDColor = Color3.fromRGB(255, 0, 0); PCLDTrans = 0.5 end
if not ShiftValue then ShiftValue = 50 end

me.Chatted:Connect(function(msg)
	if msg:sub(1,6):lower() == "/bring" or msg:sub(1,2):lower() == "/b" then
		msgCount = msgCount + 1
		if msgCount < 2 then
			local Text = string.lower(msg:split(" ")[2])
			for _,plr in pairs(Players:GetChildren()) do
				if string.find(plr.DisplayName:lower(),Text) then
					BringPlayer(plr.Name)
					return
				elseif string.find(plr.Name:lower(),Text) then
					BringPlayer(plr.Name)
					return
				end
			end
		else
			msgCount = 0
		end
	end
end)

UIS.MouseIconEnabled = true
Rayfield:LoadConfiguration()
