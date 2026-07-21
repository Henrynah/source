loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
local Rayfield = (function() 
    --[[

        Rayfield Interface Suite
        by Sirius

        shlex  | Designing + Programming
        iRay   | Programming
        Max    | Programming
        Damian | Programming

    ]]

    if debugX then
        warn('Initialising Rayfield')
    end



    local function getService(name)
        local service = game:GetService(name)
        return if cloneref then cloneref(service) else service
    end

    -- Services
    local UserInputService = getService("UserInputService")
    local TweenService = getService("TweenService")
    local Players = getService("Players")
    local CoreGui = getService("CoreGui")

    -- Loads and executes a function hosted on a remote URL. Cancels the request if the requested URL takes too long to respond.
    -- Errors with the function are caught and logged to the output
    local function loadWithTimeout(url: string, timeout: number?): ...any
        assert(type(url) == "string", "Expected string, got " .. type(url))
        timeout = timeout or 5
        local requestCompleted = false
        local success, result = false, nil

        local requestThread = task.spawn(function()
            local fetchSuccess, fetchResult = pcall(game.HttpGet, game, url) -- game:HttpGet(url)
            -- If the request fails the content can be empty, even if fetchSuccess is true
            if not fetchSuccess or #fetchResult == 0 then
                if #fetchResult == 0 then
                    fetchResult = "Empty response" -- Set the error message
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
                warn("Request for " .. url .. " timed out after " .. tostring(timeout) .. " seconds")
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
            warn("Failed to process " .. tostring(url) .. ": " .. tostring(result))
        end
        return if success then result else nil
    end

    local _getgenv = rawget(_G, "getgenv")
    local requestsDisabled = false
    local customAssetId = nil
    local secureMode = false
    if _getgenv then
        local ok, result = pcall(function() return _getgenv().DISABLE_RAYFIELD_REQUESTS end)
        if ok and result then requestsDisabled = true end
        local ok2, result2 = pcall(function() return _getgenv().RAYFIELD_ASSET_ID end)
        if ok2 and type(result2) == "number" then customAssetId = result2 end
        local ok3, result3 = pcall(function() return _getgenv().RAYFIELD_SECURE end)
        if ok3 and result3 then secureMode = true end
    end

    if secureMode then
        local _error = error
        local _assert = assert
        warn = function(...) end
        print = function(...) end
        error = function(_, level) _error("", level) end
        assert = function(v, ...) return _assert(v) end
    end

    local secureWarnings = {}
    local customAssets = {}

    local function secureNotify(wType, title, content)
        if secureWarnings[wType] then return end
        secureWarnings[wType] = true
        task.spawn(function()
            while not RayfieldLibrary or not RayfieldLibrary.Notify do task.wait(0.5) end
            RayfieldLibrary:Notify({
                Title = title,
                Content = content,
                Duration = 8,
            })
        end)
    end
    local InterfaceBuild = 'UU2NX'
    local Release = "Build 1.746"
    local RayfieldFolder = "Rayfield"
    local ConfigurationFolder = RayfieldFolder.."/Configurations"
    local ConfigurationExtension = ".rfld"
    local settingsTable = {
        General = {
            -- if needs be in order just make getSetting(name)
            rayfieldOpen = {Type = 'bind', Value = 'K', Name = 'Rayfield Keybind'},
            -- buildwarnings
            -- rayfieldprompts

        },
        System = {
            usageAnalytics = {Type = 'toggle', Value = true, Name = 'Anonymised Analytics'},
        }
    }

    -- Settings that have been overridden by the developer. These will not be saved to the user's configuration file
    -- Overridden settings always take precedence over settings in the configuration file, and are cleared if the user changes the setting in the UI
    local overriddenSettings: { [string]: any } = {} -- For example, overriddenSettings["System.rayfieldOpen"] = "J"
    local function overrideSetting(category: string, name: string, value: any)
        overriddenSettings[category .. "." .. name] = value
    end

    local function getSetting(category: string, name: string): any
        if overriddenSettings[category .. "." .. name] ~= nil then
            return overriddenSettings[category .. "." .. name]
        elseif settingsTable[category][name] ~= nil then
            return settingsTable[category][name].Value
        end
    end

    -- If requests/analytics have been disabled by developer, set the user-facing setting to false as well
    if requestsDisabled then
        overrideSetting("System", "usageAnalytics", false)
    end

    local HttpService = getService('HttpService')
    local RunService = getService('RunService')

    -- Environment Check
    local useStudio = RunService:IsStudio() or false

    local settingsCreated = false
    local settingsInitialized = false -- Whether the UI elements in the settings page have been set to the proper values
    local prompt = useStudio and require(script.Parent.prompt) or loadWithTimeout('https://raw.githubusercontent.com/SiriusSoftwareLtd/Sirius/refs/heads/request/prompt.lua')
    local requestFunc = (syn and syn.request) or (fluxus and fluxus.request) or (http and http.request) or http_request or request

    -- Validate prompt loaded correctly
    if not prompt and not useStudio then
        warn("Failed to load prompt library, using fallback")
        prompt = {
            create = function() end -- No-op fallback
        }
    end


    -- The function below provides a safe alternative for calling error-prone functions
    -- Especially useful for filesystem function (writefile, makefolder, etc.)
    local function callSafely(func, ...)
        if func then
            local success, result = pcall(func, ...)
            if not success then
                warn("Rayfield | Function failed with error: ", result)
                return false
            else
                return result
            end
        end
    end

    -- Ensures a folder exists by creating it if needed
    local function ensureFolder(folderPath)
        if isfolder and not callSafely(isfolder, folderPath) then
            callSafely(makefolder, folderPath)
        end
    end

    local function loadSettings()
        local file = nil

        local success, result =	pcall(function()
            if callSafely(isfolder, RayfieldFolder) then
                if callSafely(isfile, RayfieldFolder..'/settings'..ConfigurationExtension) then
                    file = callSafely(readfile, RayfieldFolder..'/settings'..ConfigurationExtension)
                end
            end

            -- for debug in studio
            if useStudio then
                file = [[
        {"General":{"rayfieldOpen":{"Value":"K","Type":"bind","Name":"Rayfield Keybind","Element":{"HoldToInteract":false,"Ext":true,"Name":"Rayfield Keybind","Set":null,"CallOnChange":true,"Callback":null,"CurrentKeybind":"K"}}},"System":{"usageAnalytics":{"Value":false,"Type":"toggle","Name":"Anonymised Analytics","Element":{"Ext":true,"Name":"Anonymised Analytics","Set":null,"CurrentValue":false,"Callback":null}}}}
    ]]
            end

            if file then
                local decodeSuccess, decodedFile = pcall(function() return HttpService:JSONDecode(file) end)
                if decodeSuccess then
                    file = decodedFile
                else
                    file = {}
                end
            else
                file = {}
            end


            if not settingsCreated then
                return
            end

            if next(file) ~= nil then
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
            -- If no settings saved, apply overridden settings only
            else
                for settingName, settingValue in overriddenSettings do
                    local split = string.split(settingName, ".")
                    assert(#split == 2, "Rayfield | Invalid overridden setting name: " .. settingName)
                    local categoryName = split[1]
                    local settingNameOnly = split[2]
                    if settingsTable[categoryName] and settingsTable[categoryName][settingNameOnly] then
                        settingsTable[categoryName][settingNameOnly].Element:Set(settingValue)
                    end
                end
            end
            settingsInitialized = true
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

    -- local ANALYTICS_TOKEN = "05de7f9fd320d3b8428cd1c77014a337b85b6c8efee2c5914f5ab5700c354b9a"

    -- local reporter = nil
    -- if not requestsDisabled and not useStudio then
    --     local fetchSuccess, fetchResult = pcall((game :: any).HttpGet, game, "https://raw.githubusercontent.com/SiriusSoftwareLtd/Rayfield/refs/heads/main/reporter.lua")
    --     if fetchSuccess and #fetchResult > 0 then
    --         local execSuccess, Analytics = pcall(function()
    --             return (loadstring(fetchResult) :: any)()
    --         end)
    --         if execSuccess and Analytics then
    --             pcall(function()
    --                 reporter = Analytics.new({
    --                     url          = "https://rayfield-collect.sirius-software-ltd.workers.dev",
    --                     token        = ANALYTICS_TOKEN,
    --                     product_name = "Rayfield",
    --                     category     = "UILibrary",
    --                 })
    --             end)
    --         end
    --     end
    -- end

    -- local promptUser = 2

    -- if promptUser == 1 and prompt and type(prompt.create) == "function" then
    --     prompt.create(
    --         'Be cautious when running scripts',
    --         [[Please be careful when running scripts from unknown developers. This script has already been ran.

    -- <font transparency='0.3'>Some scripts may steal your items or in-game goods.</font>]],
    --         'Okay',
    --         '',
    --         function()

    --         end
    --     )
    -- end

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




    -- Interface Management

    local RayfieldAssetId = customAssetId or 10804731440
    local Rayfield = useStudio and script.Parent:FindFirstChild('Rayfield') or game:GetObjects("rbxassetid://"..RayfieldAssetId)[1]
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

        local toDestroy
        toDestroy, Rayfield = Rayfield, useStudio and script.Parent:FindFirstChild('Rayfield') or game:GetObjects("rbxassetid://"..RayfieldAssetId)[1]
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

    if secureMode and not customAssetId then
        secureNotify("default_asset", "Secure Mode", "You are using the default Rayfield asset ID. Set RAYFIELD_ASSET_ID to a custom upload to avoid detection.")
    end

    do
        local AssetPath = RayfieldFolder.."/Assets"
        local AssetBaseURL = "https://github.com/SiriusSoftwareLtd/Rayfield/blob/main/assets/"

        local assetFiles = {
            ["111263549366178"] = AssetBaseURL.."111263549366178.png?raw=true",
            ["77891951053543"] = AssetBaseURL.."77891951053543.png?raw=true",
            ["78137979054938"] = AssetBaseURL.."78137979054938.png?raw=true",
            ["80503127983237"] = AssetBaseURL.."80503127983237.png?raw=true",
            ["10137832201"] = AssetBaseURL.."10137832201.png?raw=true",
            ["10137941941"] = AssetBaseURL.."10137941941.png?raw=true",
            ["11036884234"] = AssetBaseURL.."11036884234.png?raw=true",
            ["11413591840"] = AssetBaseURL.."11413591840.png?raw=true",
            ["11745872910"] = AssetBaseURL.."11745872910.png?raw=true",
            ["12577727209"] = AssetBaseURL.."12577727209.png?raw=true",
            ["18458939117"] = AssetBaseURL.."18458939117.png?raw=true",
            ["3259050989"] = AssetBaseURL.."3259050989.png?raw=true",
            ["3523728077"] = AssetBaseURL.."3523728077.png?raw=true",
            ["3602733521"] = AssetBaseURL.."3602733521.png?raw=true",
            ["IconChevronTopMedium"] = AssetBaseURL.."IconChevronTopMedium.png?raw=true",
            ["4483362458"] = AssetBaseURL.."4483362458.png?raw=true",
            ["5587865193"] = AssetBaseURL.."5587865193.png?raw=true",
            ["IconMagnifyingGlass2"] = AssetBaseURL.."IconMagnifyingGlass2.png?raw=true",
        }

        for id, _ in assetFiles do
            customAssets[tostring(id)] = ""
        end

        local hasCustomAsset = type(getcustomasset) == "function"
        local hasFilesystem = type(writefile) == "function" and type(makefolder) == "function" and type(isfile) == "function" and type(isfolder) == "function"

        if hasCustomAsset and hasFilesystem then
            local ok, err = pcall(function()
                ensureFolder(RayfieldFolder)
                ensureFolder(AssetPath)

                local function nextMissing()
                    for id, _ in assetFiles do
                        if not isfile(AssetPath.."/"..tostring(id)..".png") then
                            return id
                        end
                    end
                    return nil
                end

                if nextMissing() then
                    task.spawn(function()
                        while true do
                            local id = nextMissing()
                            if not id then break end
                            writefile(AssetPath.."/"..tostring(id)..".png", requestFunc({Url = assetFiles[id], Method = "GET"}).Body)
                            task.wait()
                        end
                    end)

                    while nextMissing() do
                        task.wait(0.1)
                    end
                end

                for id, _ in assetFiles do
                    local success, asset = pcall(getcustomasset, AssetPath.."/"..tostring(id)..".png")
                    if success then
                        customAssets[tostring(id)] = asset
                    else
                        warn("Rayfield | Failed to load custom asset: "..tostring(id).." - "..tostring(asset))
                    end
                end
            end)

            if not ok then
                warn("Rayfield | Failed to load custom assets: "..tostring(err))
                secureNotify("asset_load_fail", "Rayfield", "Failed to load custom assets. UI images may not display correctly.")
            end
        else
            secureNotify("no_getcustomasset", "Rayfield", "Your executor does not support getcustomasset. Some UI images may not render correctly.")
        end


        Rayfield.Main.Shadow.Image.Image = customAssets[tostring(5587865193)]
        Rayfield.Main.Topbar.Hide.Image = customAssets[tostring(10137832201)]
        Rayfield.Main.Topbar.ChangeSize.Image = customAssets[tostring(10137941941)]
        Rayfield.Main.Topbar.Settings.Image = customAssets[tostring(80503127983237)]
        Rayfield.Main.Topbar.Icon.Image = customAssets[tostring(78137979054938)]
        Rayfield.Main.Topbar.Search.Image = customAssets["IconMagnifyingGlass2"]
        Rayfield.Main.Topbar.Search.ImageRectOffset = Vector2.new(0, 0)
        Rayfield.Main.Topbar.Search.ImageRectSize = Vector2.new(0, 0)
        Rayfield.Main.Elements.Template.Toggle.Switch.Shadow.Image = customAssets[tostring(3602733521)]
        Rayfield.Main.Elements.Template.Slider.Main.Shadow.Image = customAssets[tostring(3602733521)]
        Rayfield.Main.Elements.Template.Dropdown.Toggle.Image = customAssets["IconChevronTopMedium"]
        Rayfield.Main.Elements.Template.Dropdown.Toggle.ImageRectOffset = Vector2.new(0, 0)
        Rayfield.Main.Elements.Template.Dropdown.Toggle.ImageRectSize = Vector2.new(0, 0)
        Rayfield.Main.Elements.Template.Label.Icon.Image = customAssets[tostring(11745872910)]
        Rayfield.Main.Elements.Template.ColorPicker.CPBackground.MainCP.Image = customAssets[tostring(11413591840)]
        Rayfield.Main.Elements.Template.ColorPicker.CPBackground.MainCP.MainPoint.Image = customAssets[tostring(3259050989)]
        Rayfield.Main.Elements.Template.ColorPicker.ColorSlider.SliderPoint.Image = customAssets[tostring(3259050989)]
        Rayfield.Main.TabList.Template.Image.Image = customAssets[tostring(4483362458)]
        Rayfield.Main.Search.Search.Image = customAssets[tostring(18458939117)]
        Rayfield.Main.Search.Shadow.Image = customAssets[tostring(5587865193)]
        Rayfield.Notifications.Template.Icon.Image = customAssets[tostring(77891951053543)]
        Rayfield.Notifications.Template.Shadow.Image = customAssets[tostring(3523728077)]
        Rayfield.Loading.Banner.Image = customAssets[tostring(111263549366178)]

    end -- custom asset block

    local minSize = Vector2.new(1024, 768)
    local useMobileSizing

    if Rayfield.AbsoluteSize.X < minSize.X and Rayfield.AbsoluteSize.Y < minSize.Y then
        useMobileSizing = true
    end

    local useMobilePrompt = false
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
    local keybindConnections = {} -- For storing keybind connections to disconnect when Rayfield is destroyed

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
            error("Lucide Icons: Failed to find icon by the name of \"" .. name .. "\"", 2)
        end

        local rirs = r[2]
        local riro = r[3]

        if type(r[1]) ~= "number" or type(rirs) ~= "table" or type(riro) ~= "table" then
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
    local function getAssetUri(id: any): string
        local assetUri = ""
        if type(id) == "number" then
            assetUri = "rbxassetid://" .. id
        elseif type(id) == "string" and not Icons then
            warn("Rayfield | Cannot use Lucide icons as icons library is not loaded")
        else
            warn("Rayfield | The icon argument must either be an icon ID (number) or a Lucide icon name (string)")
        end
        return assetUri
    end

    local function isCustomAsset(value)
        return type(value) == "string" and (string.find(value, "rbxasset://") == 1 or string.find(value, "rbxthumb://") == 1)
    end

    local function resolveIcon(icon)
        if not icon or icon == 0 then
            return "", nil, nil
        end

        if isCustomAsset(icon) then
            return icon, nil, nil
        end

        if secureMode then
            secureNotify("icon_blocked", "Secure Mode", "Element icons using asset IDs or Lucide names are blocked. Use getcustomasset() for icons to stay undetected.")
            return "", nil, nil
        end

        if typeof(icon) == "string" and Icons then
            local asset = getIcon(icon)
            return "rbxassetid://" .. asset.id, asset.imageRectOffset, asset.imageRectSize
        else
            return getAssetUri(icon), nil, nil
        end
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


        callSafely(writefile, ConfigurationFolder .. "/" .. CFileName .. ConfigurationExtension, tostring(HttpService:JSONEncode(Data)))
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
                local img, rectOffset, rectSize = resolveIcon(data.Image)
                newNotification.Icon.Image = img
                if rectOffset then newNotification.Icon.ImageRectOffset = rectOffset end
                if rectSize then newNotification.Icon.ImageRectSize = rectSize end
            else
                newNotification.Icon.Image = ""
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

    -- Sets element visibility across all tab pages (used by Hide, Unhide, Maximise, Minimise)
    local function setElementsVisible(show)
        for _, tab in ipairs(Elements:GetChildren()) do
            if tab.Name ~= "Template" and tab.ClassName == "ScrollingFrame" and tab.Name ~= "Placeholder" then
                for _, element in ipairs(tab:GetChildren()) do
                    if element.ClassName == "Frame" then
                        if element.Name ~= "SectionSpacing" and element.Name ~= "Placeholder" then
                            if element.Name == "SectionTitle" or element.Name == 'SearchTitle-fsefsefesfsefesfesfThanks' then
                                TweenService:Create(element.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = show and 0.4 or 1}):Play()
                            elseif element.Name == 'Divider' then
                                TweenService:Create(element.Divider, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = show and 0.85 or 1}):Play()
                            else
                                local bgTarget = element:GetAttribute("BackgroundTransparencyTarget") or 0
                                local strokeTarget = element:GetAttribute("UIStrokeTransparencyTarget") or 0
                                local titleTarget = element:GetAttribute("TitleTextTransparencyTarget") or 0
                                TweenService:Create(element, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = show and bgTarget or 1}):Play()
                                TweenService:Create(element.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = show and strokeTarget or 1}):Play()
                                TweenService:Create(element.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = show and titleTarget or 1}):Play()
                            end
                            for _, child in ipairs(element:GetChildren()) do
                                if child.ClassName == "Frame" or child.ClassName == "TextLabel" or child.ClassName == "TextBox" or child.ClassName == "ImageButton" or child.ClassName == "ImageLabel" then
                                    child.Visible = show
                                end
                            end
                        end
                    end
                end
            end
        end
    end

    -- Sets tab button visibility (used by Hide, Unhide, Maximise, Minimise)
    local function setTabButtonsVisible(show)
        for _, tabbtn in ipairs(TabList:GetChildren()) do
            if tabbtn.ClassName == "Frame" and tabbtn.Name ~= "Placeholder" then
                if show then
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
                else
                    TweenService:Create(tabbtn, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {BackgroundTransparency = 1}):Play()
                    TweenService:Create(tabbtn.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()
                    TweenService:Create(tabbtn.Image, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
                    TweenService:Create(tabbtn.UIStroke, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {Transparency = 1}):Play()
                end
            end
        end
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
        if notify then
            if useMobilePrompt then 
                RayfieldLibrary:Notify({Title = "Interface Hidden", Content = "The interface has been hidden, you can unhide the interface by tapping 'Show'.", Duration = 7, Image = 4400697855})
            else
                RayfieldLibrary:Notify({Title = "Interface Hidden", Content = "The interface has been hidden, you can unhide the interface by tapping " .. tostring(getSetting("General", "rayfieldOpen")) .. ".", Duration = 7, Image = 4400697855})
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
        if dragBarCosmetic then
            TweenService:Create(dragBarCosmetic, TweenInfo.new(0.25, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {BackgroundTransparency = 1}):Play()
        end

        if useMobilePrompt and MPrompt then
            TweenService:Create(MPrompt, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {Size = UDim2.new(0, 120, 0, 30), Position = UDim2.new(0.5, 0, 0, 20), BackgroundTransparency = 0.3}):Play()
            TweenService:Create(MPrompt.Title, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {TextTransparency = 0.3}):Play()
        end

        for _, TopbarButton in ipairs(Topbar:GetChildren()) do
            if TopbarButton.ClassName == "ImageButton" then
                TweenService:Create(TopbarButton, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {ImageTransparency = 1}):Play()
            end
        end

        setTabButtonsVisible(false)

        if dragInteract then dragInteract.Visible = false end

        setElementsVisible(false)

        task.wait(0.5)
        Main.Visible = false
        Debounce = false
    end

    local function Maximise()
        Debounce = true
        Topbar.ChangeSize.Image = customAssets[tostring(10137941941)]

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

        setElementsVisible(true)

        task.wait(0.1)

        setTabButtonsVisible(true)

        task.wait(0.5)
        Debounce = false
    end


    local function Unhide()
        Debounce = true
        Main.Position = UDim2.new(0.5, 0, 0.5, 0)
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

        setTabButtonsVisible(true)

        setElementsVisible(true)

        TweenService:Create(dragBarCosmetic, TweenInfo.new(0.25, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {BackgroundTransparency = 0.5}):Play()

        task.wait(0.5)
        Minimised = false
        Debounce = false
    end

    local function Minimise()
        Debounce = true
        Topbar.ChangeSize.Image = customAssets[tostring(11036884234)]

        Topbar.UIStroke.Color = SelectedTheme.ElementStroke

        task.spawn(closeSearch)

        setTabButtonsVisible(false)

        setElementsVisible(false)

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
            callSafely(writefile, RayfieldFolder..'/settings'..ConfigurationExtension, encoded)
        end
    end

    local function updateSetting(category: string, setting: string, value: any)
        if not settingsInitialized then
            return
        end
        settingsTable[category][setting].Value = value
        overriddenSettings[category .. "." .. setting] = nil -- If user changes an overriden setting, remove the override
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

    local function fadeOutKeyUI(KeyMain)
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
    end

    function RayfieldLibrary:CreateWindow(Settings)
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

        ensureFolder(RayfieldFolder)

        local Passthrough = false
        Topbar.Title.Text = Settings.Name

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
                local img, rectOffset, rectSize = resolveIcon(Settings.Icon)
                Topbar.Icon.Image = img
                if rectOffset then Topbar.Icon.ImageRectOffset = rectOffset end
                if rectSize then Topbar.Icon.ImageRectSize = rectSize end
            else
                Topbar.Icon.Image = ""
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

        if not Settings.DisableRayfieldPrompts then
            task.spawn(function()
                while not rayfieldDestroyed do
                    task.wait(math.random(180, 600))
                    if rayfieldDestroyed then break end
                    RayfieldLibrary:Notify({
                        Title = "Rayfield Interface",
                        Content = "Enjoying this UI library? Find it at sirius.menu/discord",
                        Duration = 7,
                        Image = 4370033185,
                    })
                end
            end)
        end

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
                ensureFolder(ConfigurationFolder)
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

        if Settings.Discord and Settings.Discord.Enabled and not useStudio and not secureMode then
            ensureFolder(RayfieldFolder.."/Discord Invites")

            if not callSafely(isfile, RayfieldFolder.."/Discord Invites".."/"..Settings.Discord.Invite..ConfigurationExtension) then
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
                    callSafely(writefile, RayfieldFolder.."/Discord Invites".."/"..Settings.Discord.Invite..ConfigurationExtension,"Rayfield RememberJoins is true for this invite, this invite will not ask you to join again")
                end
            end
        end

        if (Settings.KeySystem) then
            if not Settings.KeySettings then
                Passthrough = true
                return
            end

            ensureFolder(RayfieldFolder.."/Key System")

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

            if callSafely(isfile, RayfieldFolder.."/Key System".."/"..Settings.KeySettings.FileName..ConfigurationExtension) then
                for _, MKey in ipairs(Settings.KeySettings.Key) do
                    local savedKeys = callSafely(readfile, RayfieldFolder.."/Key System".."/"..Settings.KeySettings.FileName..ConfigurationExtension)
                    if savedKeys and string.find(savedKeys, MKey) then
                        Passthrough = true
                    end
                end
            end

            if not Passthrough and secureMode then
                warn("Rayfield | Secure Mode: Key system requires a valid saved key. The key UI cannot be shown as it requires loading detectable assets.")
                Rayfield.Enabled = false
                return RayfieldLibrary
            end

            if not Passthrough then
                local AttemptsRemaining = Settings.KeySettings.MaxAttempts or 5
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
                        fadeOutKeyUI(KeyMain)
                        task.wait(0.51)
                        Passthrough = true
                        KeyMain.Visible = false
                        if Settings.KeySettings.SaveKey then
                            callSafely(writefile, RayfieldFolder.."/Key System".."/"..Settings.KeySettings.FileName..ConfigurationExtension, FoundKey)
                            RayfieldLibrary:Notify({Title = "Key System", Content = "The key for this script has been saved successfully.", Image = 3605522284})
                        end
                    else
                        if AttemptsRemaining == 0 then
                            fadeOutKeyUI(KeyMain)
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
                    fadeOutKeyUI(KeyMain)
                    task.wait(0.51)
                    Passthrough = true
                    RayfieldLibrary:Destroy()
                    KeyUI:Destroy()
                end)
            else
                Passthrough = true
            end
        end
        if Settings.KeySystem then
            repeat task.wait() until Passthrough
            if rayfieldDestroyed then return end
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
        Elements.UIPageLayout.ScrollWheelInputEnabled = false
        Elements.UIPageLayout.GamepadInputEnabled = false
        Elements.UIPageLayout.TouchInputEnabled = false
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
                local img, rectOffset, rectSize = resolveIcon(Image)
                TabButton.Image.Image = img
                if rectOffset then TabButton.Image.ImageRectOffset = rectOffset end
                if rectSize then TabButton.Image.ImageRectSize = rectSize end

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

            TabPage.LayoutOrder = Ext and 10000 or #Elements:GetChildren()

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

                local colorPickerInputConnection = UserInputService.InputEnded:Connect(function(input, gameProcessed) if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
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

                local colorPickerRenderConnection = RunService.RenderStepped:connect(function()
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

                ColorPicker.Destroying:Connect(function()
                    if colorPickerRenderConnection then
                        colorPickerRenderConnection:Disconnect()
                    end
                    if colorPickerInputConnection then
                        colorPickerInputConnection:Disconnect()
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
                    local img, rectOffset, rectSize = resolveIcon(Icon)
                    Label.Icon.Image = img
                    if rectOffset then Label.Icon.ImageRectOffset = rectOffset end
                    if rectSize then Label.Icon.ImageRectSize = rectSize end
                else
                    Label.Icon.Image = ""
                end

                if Icon and Label:FindFirstChild('Icon') then
                    Label.Title.Position = UDim2.new(0, 45, 0.5, 0)
                    Label.Title.Size = UDim2.new(1, -100, 0, 14)
                    Label.Icon.Visible = true
                end

                Label.Icon.ImageTransparency = 1
                Label.BackgroundTransparency = 1
                Label.UIStroke.Transparency = 1
                Label.Title.TextTransparency = 1

                Label:SetAttribute("BackgroundTransparencyTarget", Color and 0.8 or 0)
                Label:SetAttribute("UIStrokeTransparencyTarget", Color and 0.7 or 0)
                Label:SetAttribute("TitleTextTransparencyTarget", Color and 0.2 or 0)

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

                        local img, rectOffset, rectSize = resolveIcon(Icon)
                        Label.Icon.Image = img
                        if rectOffset then Label.Icon.ImageRectOffset = rectOffset end
                        if rectSize then Label.Icon.ImageRectSize = rectSize end

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

                        --local Dropdown = Tab:CreateDropdown({
                        --	Name = "Dropdown Example",
                        --	Options = {"Option 1","Option 2"},
                        --	CurrentOption = {"Option 1"},
                        --  MultipleOptions = true,
                        --	Flag = "Dropdown1",
                        --	Callback = function(TableOfOptions)

                        --	end,
                        --})


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

                    -- Apply selected/unselected background colors to new options
                    for _, droption in ipairs(Dropdown.List:GetChildren()) do
                        if droption.ClassName == "Frame" and droption.Name ~= "Placeholder" then
                            if not table.find(DropdownSettings.CurrentOption, droption.Name) then
                                droption.BackgroundColor3 = SelectedTheme.DropdownUnselected
                            else
                                droption.BackgroundColor3 = SelectedTheme.DropdownSelected
                            end
                        end
                    end

                    -- If the dropdown is currently open, make new options visible immediately
                    if Dropdown.List.Visible then
                        for _, DropdownOpt in ipairs(Dropdown.List:GetChildren()) do
                            if DropdownOpt.ClassName == "Frame" and DropdownOpt.Name ~= "Placeholder" then
                                DropdownOpt.BackgroundTransparency = 0
                                DropdownOpt.Title.TextTransparency = 0
                                if not table.find(DropdownSettings.CurrentOption, DropdownOpt.Name) then
                                    DropdownOpt.UIStroke.Transparency = 0
                                end
                            end
                        end
                    end
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

                local connection = UserInputService.InputBegan:Connect(function(input, processed)
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
                table.insert(keybindConnections, connection)

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

                Slider.Main.Progress.Size =	UDim2.new(0, Slider.Main.AbsoluteSize.X * ((SliderSettings.CurrentValue - SliderSettings.Range[1]) / (SliderSettings.Range[2] - SliderSettings.Range[1])) > 5 and Slider.Main.AbsoluteSize.X * ((SliderSettings.CurrentValue - SliderSettings.Range[1]) / (SliderSettings.Range[2] - SliderSettings.Range[1])) or 5, 1, 0)

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

                    TweenService:Create(Slider.Main.Progress, TweenInfo.new(0.45, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out), {Size = UDim2.new(0, Slider.Main.AbsoluteSize.X * ((NewVal - SliderSettings.Range[1]) / (SliderSettings.Range[2] - SliderSettings.Range[1])) > 5 and Slider.Main.AbsoluteSize.X * ((NewVal - SliderSettings.Range[1]) / (SliderSettings.Range[2] - SliderSettings.Range[1])) or 5, 1, 0)}):Play()
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


        task.wait(1.1)
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
        task.wait(0.3)

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

        -- Report after createSettings so loadSettings() has run and usageAnalytics reflects the user's saved preference
        if reporter and getSetting("System", "usageAnalytics") then
            local themeName = "Default"
            if Settings.Theme then
                if type(Settings.Theme) == "string" then
                    themeName = Settings.Theme
                elseif type(Settings.Theme) == "table" then
                    themeName = "Custom"
                end
            end

            local discordInvite = nil
            if Settings.Discord and Settings.Discord.Enabled and Settings.Discord.Invite and Settings.Discord.Invite ~= "" then
                local raw = tostring(Settings.Discord.Invite)
                -- Normalize: strip URL prefixes to extract just the invite code
                discordInvite = (raw:match("discord%.gg/([%w%-]+)") or raw:match("discord%.com/invite/([%w%-]+)") or raw):sub(1, 32)
            end

            local sampleSend = false

            -- Random Sampling Test
            if not Settings.ScriptID and math.random() > 0.4 then
                sampleSend = true
            end

            --if Settings.ScriptID then
                reporter:windowCreated({
                    script_name        = Settings.Name or "Unknown",
                    script_version     = Release,
                    interface_version  = InterfaceBuild,
                    theme              = themeName,
                    is_mobile          = useMobileSizing and true or false,
                    has_key_system     = Settings.KeySystem and true or false,
                    discord_invite     = discordInvite,
                    config_saving      = (Settings.ConfigurationSaving and Settings.ConfigurationSaving.Enabled) and true or false,
                    script_id          = Settings.ScriptID or sampleSend and 'sid_tzfyxawonjx9' or nil,
                    verification_token = Settings.VerificationToken,
                })
            --end
        end

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
        if hideHotkeyConnection then
            hideHotkeyConnection:Disconnect()
        end
        for _, connection in keybindConnections do
            connection:Disconnect()
        end
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
                    if OtherTabButton.Name ~= "Template" and OtherTabButton.ClassName == "Frame" and OtherTabButton.Name ~= "Placeholder" then
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
            if Hidden then
                Hidden = false
                Unhide()
            else
                Hidden = true
                Hide()
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
                    if callSafely(isfile, ConfigurationFolder .. "/" .. CFileName .. ConfigurationExtension) then
                        loaded = LoadConfiguration(callSafely(readfile, ConfigurationFolder .. "/" .. CFileName .. ConfigurationExtension))
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



    if useStudio then
        -- run w/ studio
        -- Feel free to place your own script here to see how it'd work in Roblox Studio before running it on your execution software.


        --local Window = RayfieldLibrary:CreateWindow({
        --	Name = "Rayfield Example Window",
        --	LoadingTitle = "Rayfield Interface Suite",
        --	Theme = 'Default',
        --	Icon = 0,
        --	LoadingSubtitle = "by Sirius",
        --	ConfigurationSaving = {
        --		Enabled = true,
        --		FolderName = nil, -- Create a custom folder for your hub/game
        --		FileName = "Big Hub52"
        --	},
        --	Discord = {
        --		Enabled = false,
        --		Invite = "noinvitelink", -- The Discord invite code, do not include discord.gg/. E.g. discord.gg/ABCD would be ABCD
        --		RememberJoins = true -- Set this to false to make them join the discord every time they load it up
        --	},
        --	KeySystem = false, -- Set this to true to use our key system
        --	KeySettings = {
        --		Title = "Untitled",
        --		Subtitle = "Key System",
        --		Note = "No method of obtaining the key is provided",
        --		FileName = "Key", -- It is recommended to use something unique as other scripts using Rayfield may overwrite your key file
        --		SaveKey = true, -- The user's key will be saved, but if you change the key, they will be unable to use your script
        --		GrabKeyFromSite = false, -- If this is true, set Key below to the RAW site you would like Rayfield to get the key from
        --		Key = {"Hello"} -- List of keys that will be accepted by the system, can be RAW file links (pastebin, github etc) or simple strings ("hello","key22")
        --	}
        --})

        --local Tab = Window:CreateTab("Tab Example", 'key-round') -- Title, Image
        --local Tab2 = Window:CreateTab("Tab Example 2", 4483362458) -- Title, Image

        --local Section = Tab2:CreateSection("Section")


        --local ColorPicker = Tab2:CreateColorPicker({
        --	Name = "Color Picker",
        --	Color = Color3.fromRGB(255,255,255),
        --	Flag = "ColorPicfsefker1", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
        --	Callback = function(Value)
        --		-- The function that takes place every time the color picker is moved/changed
        --		-- The variable (Value) is a Color3fromRGB value based on which color is selected
        --	end
        --})

        --local Slider = Tab2:CreateSlider({
        --	Name = "Slider Example",
        --	Range = {0, 100},
        --	Increment = 10,
        --	Suffix = "Bananas",
        --	CurrentValue = 40,
        --	Flag = "Slidefefsr1", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
        --	Callback = function(Value)
        --		-- The function that takes place when the slider changes
        --		-- The variable (Value) is a number which correlates to the value the slider is currently at
        --	end,
        --})

        --local Input = Tab2:CreateInput({
        --	Name = "Input Example",
        --	CurrentValue = '',
        --	PlaceholderText = "Input Placeholder",
        --	Flag = 'dawdawd',
        --	RemoveTextAfterFocusLost = false,
        --	Callback = function(Text)
        --		-- The function that takes place when the input is changed
        --		-- The variable (Text) is a string for the value in the text box
        --	end,
        --})


        ----RayfieldLibrary:Notify({Title = "Rayfield Interface", Content = "Welcome to Rayfield. These - are the brand new notification design for Rayfield, with custom sizing and Rayfield calculated wait times.", Image = 4483362458})

        --local Section = Tab:CreateSection("Section Example")

        --local Button = Tab:CreateButton({
        --	Name = "Change Theme",
        --	Callback = function()
        --		-- The function that takes place when the button is pressed
        --		Window.ModifyTheme('DarkBlue')
        --	end,
        --})

        --local Toggle = Tab:CreateToggle({
        --	Name = "Toggle Example",
        --	CurrentValue = false,
        --	Flag = "Toggle1adwawd", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
        --	Callback = function(Value)
        --		-- The function that takes place when the toggle is pressed
        --		-- The variable (Value) is a boolean on whether the toggle is true or false
        --	end,
        --})

        --local ColorPicker = Tab:CreateColorPicker({
        --	Name = "Color Picker",
        --	Color = Color3.fromRGB(255,255,255),
        --	Flag = "ColorPicker1awd", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
        --	Callback = function(Value)
        --		-- The function that takes place every time the color picker is moved/changed
        --		-- The variable (Value) is a Color3fromRGB value based on which color is selected
        --	end
        --})

        --local Slider = Tab:CreateSlider({
        --	Name = "Slider Example",
        --	Range = {0, 100},
        --	Increment = 10,
        --	Suffix = "Bananas",
        --	CurrentValue = 40,
        --	Flag = "Slider1dawd", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
        --	Callback = function(Value)
        --		-- The function that takes place when the slider changes
        --		-- The variable (Value) is a number which correlates to the value the slider is currently at
        --	end,
        --})

        --local Input = Tab:CreateInput({
        --	Name = "Input Example",
        --	CurrentValue = "Helo",
        --	PlaceholderText = "Adaptive Input",
        --	RemoveTextAfterFocusLost = false,
        --	Flag = 'Input1',
        --	Callback = function(Text)
        --		-- The function that takes place when the input is changed
        --		-- The variable (Text) is a string for the value in the text box
        --	end,
        --})

        --local thoptions = {}
        --for themename, theme in pairs(RayfieldLibrary.Theme) do
        --	table.insert(thoptions, themename)
        --end

        --local Dropdown = Tab:CreateDropdown({
        --	Name = "Theme",
        --	Options = thoptions,
        --	CurrentOption = {"Default"},
        --	MultipleOptions = false,
        --	Flag = "Dropdown1", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
        --	Callback = function(Options)
        --		--Window.ModifyTheme(Options[1])
        --		-- The function that takes place when the selected option is changed
        --		-- The variable (Options) is a table of strings for the current selected options
        --	end,
        --})


        --Window.ModifyTheme({
        --	TextColor = Color3.fromRGB(50, 55, 60),
        --	Background = Color3.fromRGB(240, 245, 250),
        --	Topbar = Color3.fromRGB(215, 225, 235),
        --	Shadow = Color3.fromRGB(200, 210, 220),

        --	NotificationBackground = Color3.fromRGB(210, 220, 230),
        --	NotificationActionsBackground = Color3.fromRGB(225, 230, 240),

        --	TabBackground = Color3.fromRGB(200, 210, 220),
        --	TabStroke = Color3.fromRGB(180, 190, 200),
        --	TabBackgroundSelected = Color3.fromRGB(175, 185, 200),
        --	TabTextColor = Color3.fromRGB(50, 55, 60),
        --	SelectedTabTextColor = Color3.fromRGB(30, 35, 40),

        --	ElementBackground = Color3.fromRGB(210, 220, 230),
        --	ElementBackgroundHover = Color3.fromRGB(220, 230, 240),
        --	SecondaryElementBackground = Color3.fromRGB(200, 210, 220),
        --	ElementStroke = Color3.fromRGB(190, 200, 210),
        --	SecondaryElementStroke = Color3.fromRGB(180, 190, 200),

        --	SliderBackground = Color3.fromRGB(200, 220, 235),  -- Lighter shade
        --	SliderProgress = Color3.fromRGB(70, 130, 180),
        --	SliderStroke = Color3.fromRGB(150, 180, 220),

        --	ToggleBackground = Color3.fromRGB(210, 220, 230),
        --	ToggleEnabled = Color3.fromRGB(70, 160, 210),
        --	ToggleDisabled = Color3.fromRGB(180, 180, 180),
        --	ToggleEnabledStroke = Color3.fromRGB(60, 150, 200),
        --	ToggleDisabledStroke = Color3.fromRGB(140, 140, 140),
        --	ToggleEnabledOuterStroke = Color3.fromRGB(100, 120, 140),
        --	ToggleDisabledOuterStroke = Color3.fromRGB(120, 120, 130),

        --	DropdownSelected = Color3.fromRGB(220, 230, 240),
        --	DropdownUnselected = Color3.fromRGB(200, 210, 220),

        --	InputBackground = Color3.fromRGB(220, 230, 240),
        --	InputStroke = Color3.fromRGB(180, 190, 200),
        --	PlaceholderColor = Color3.fromRGB(150, 150, 150)
        --})

        --local Keybind = Tab:CreateKeybind({
        --	Name = "Keybind Example",
        --	CurrentKeybind = "Q",
        --	HoldToInteract = false,
        --	Flag = "Keybind1", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
        --	Callback = function(Keybind)
        --		-- The function that takes place when the keybind is pressed
        --		-- The variable (Keybind) is a boolean for whether the keybind is being held or not (HoldToInteract needs to be true)
        --	end,
        --})

        --local Label = Tab:CreateLabel("Label Example")

        --local Label2 = Tab:CreateLabel("Warning", 4483362458, Color3.fromRGB(255, 159, 49),  true)

        --local Paragraph = Tab:CreateParagraph({Title = "Paragraph Example", Content = "Paragraph ExampleParagraph ExampleParagraph ExampleParagraph ExampleParagraph ExampleParagraph ExampleParagraph ExampleParagraph ExampleParagraph ExampleParagraph ExampleParagraph ExampleParagraph ExampleParagraph ExampleParagraph Example"})
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
    -- AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA why :(
    --if not useStudio then
    --	task.spawn(loadWithTimeout, "https://raw.githubusercontent.com/SiriusSoftwareLtd/Sirius/refs/heads/request/boost.lua")
    --end

    task.delay(4, function()
        RayfieldLibrary.LoadConfiguration()
        if Main:FindFirstChild('Notice') and Main.Notice.Visible then
            TweenService:Create(Main.Notice, TweenInfo.new(0.5, Enum.EasingStyle.Exponential, Enum.EasingDirection.InOut), {Size = UDim2.new(0, 100, 0, 25), Position = UDim2.new(0.5, 0, 0, -100), BackgroundTransparency = 1}):Play()
            TweenService:Create(Main.Notice.Title, TweenInfo.new(0.3, Enum.EasingStyle.Exponential), {TextTransparency = 1}):Play()

            task.wait(0.5)
            Main.Notice.Visible = false
        end
    end)

    return RayfieldLibrary
end)()

local Window = Rayfield:CreateWindow({
   Name = "Private HUB (V O I D)",
   Icon = heart,
   LoadingTitle = "Loading...",
   LoadingSubtitle = "by Nico and Gru",
   ShowText = "Nigger",
   Theme = {
      TextColor = Color3.fromRGB(240, 240, 240),
      Background = Color3.fromRGB(5, 5, 5),
      Topbar = Color3.fromRGB(10, 10, 10),
      Shadow = Color3.fromRGB(160, 0, 255),
      NotificationBackground = Color3.fromRGB(10, 10, 10),
      NotificationActionsBackground = Color3.fromRGB(20, 0, 40),
      TabBackground = Color3.fromRGB(10, 10, 10),
      TabStroke = Color3.fromRGB(160, 0, 255),
      TabBackgroundSelected = Color3.fromRGB(160, 0, 255),
      TabTextColor = Color3.fromRGB(160, 0, 255),
      SelectedTabTextColor = Color3.fromRGB(5, 5, 5),
      ElementBackground = Color3.fromRGB(10, 10, 10),
      ElementBackgroundHover = Color3.fromRGB(20, 0, 40),
      SecondaryElementBackground = Color3.fromRGB(5, 5, 5),
      ElementStroke = Color3.fromRGB(160, 0, 255),
      SecondaryElementStroke = Color3.fromRGB(20, 0, 40),
      SliderBackground = Color3.fromRGB(10, 10, 10),
      SliderProgress = Color3.fromRGB(160, 0, 255),
      SliderStroke = Color3.fromRGB(160, 0, 255),
      ToggleBackground = Color3.fromRGB(60, 0, 120),
      ToggleEnabled = Color3.fromRGB(160, 0, 255),
      ToggleDisabled = Color3.fromRGB(160, 0, 255),
      ToggleEnabledStroke = Color3.fromRGB(160, 0, 255),
      ToggleDisabledStroke = Color3.fromRGB(160, 0, 255),
      ToggleEnabledOuterStroke = Color3.fromRGB(160, 0, 255),
      ToggleDisabledOuterStroke = Color3.fromRGB(160, 0, 255),
      DropdownSelected = Color3.fromRGB(20, 0, 40),
      DropdownUnselected = Color3.fromRGB(10, 10, 10),
      InputBackground = Color3.fromRGB(15, 0, 35),
      InputStroke = Color3.fromRGB(160, 0, 255),
      PlaceholderColor = Color3.fromRGB(80, 0, 120)
   },
   DisableRayfieldPrompts = true,
   DisableBuildWarnings = true,
   FreeMouse = true,
   ConfigurationSaving = { Enabled = false, FolderName = nil, FileName = "void_hub" },
   Discord = { Enabled = false, Invite = "noinvitelink", RememberJoins = true },
   KeySystem = false,
   KeySettings = {
      Title = "ENTER KEY",
      Subtitle = "Key System",
      Note = "",
      FileName = "Key",
      SaveKey = false,
      GrabKeyFromSite = false,
      Key = {"skibiditoilet"}
   }
})

ReallyDarkNiggas = {
--  ["slynicolas"] = true,
--  ["78cvzl"] = true,
--  ["thecrazyteen"] = true,
--  ["RandomHubBestHubFrFr"] = true,
--  ["AshtvnGetBanPfff"] = true,
--   ["SuPraa006"] = true,
--    ["Posuu_verified"] = true,
--  -- Posuu_verified
--   ["WhyImPlayThisGame666"] = true,
}

Players = game:GetService("Players")
LocalPlayer = Players.LocalPlayer
-- if not ReallyDarkNiggas[LocalPlayer.Name] then
--     if Rayfield then Rayfield:Destroy() end
--    return
-- end

Players = game:GetService("Players")
SoundService = game:GetService("SoundService")
LocalPlayer = Players.LocalPlayer
SOUND_ID = "rbxassetid://93917079082314"

function playSound()
    local sound = Instance.new("Sound")
    sound.SoundId = "rbxassetid://"
    sound.Volume = 0.5
    sound.PlayOnRemove = true
    sound.Parent = game:GetService("SoundService")
    sound:Destroy()
end

if LocalPlayer.Character then
    local hum = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if hum then
        hum.Died:Connect(function() task.delay(3, playSound) end)
    end
end

LocalPlayer.CharacterAdded:Connect(function(char)
    local hum = char:WaitForChild("Humanoid", 10)
    if hum then
        hum.Died:Connect(function() task.delay(3, playSound) end)
    end
end)

function playTpSound()
    local sound = Instance.new("Sound")
    sound.SoundId = "rbxassetid://77457926931973"
    sound.Volume = 0.2
    sound.PlayOnRemove = true
    sound.Parent = game:GetService("SoundService")
    sound:Destroy()
end

Players = game:GetService("Players")
RunService = game:GetService("RunService")
UserInputService = game:GetService("UserInputService")
LocalPlayer = Players.LocalPlayer

 PlayerTab = Window:CreateTab("Player", 7743871002)
 
 PlayerTab:CreateButton({
	Name = "Destroy Rayfield",
	Callback = function()
		Rayfield:Destroy()
	end,
})

PlayerTab:CreateParagraph({
	Title = "Current Player",
	Content = LocalPlayer.Name .. " (@" .. LocalPlayer.DisplayName .. ")"
})

local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local hrp = character:WaitForChild("HumanoidRootPart")

LocalPlayer.CharacterAdded:Connect(function(char)
    character = char
    humanoid = char:WaitForChild("Humanoid")
    hrp = char:WaitForChild("HumanoidRootPart")
end)



PlayerTab:CreateSection("Speed")

speedEnabled, speedConnection = false, nil
 customSpeedValue = 1

PlayerTab:CreateToggle({
    Name = 'Enable Custom Speed',
    CurrentValue = false,
    Flag = "Player_CustomSpeed",
    Callback = function(v)
        speedEnabled = v
        if speedConnection then speedConnection:Disconnect() speedConnection = nil end
        if v then
            speedConnection = RunService.Heartbeat:Connect(function()
                local char = LocalPlayer.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                local hum = char and char:FindFirstChildOfClass("Humanoid")
                if hrp and hum and hum.MoveDirection.Magnitude > 0 then
                    hrp.CFrame = hrp.CFrame + (hum.MoveDirection.Unit * customSpeedValue)
                end
            end)
        end
    end
})
PlayerTab:CreateInput({
    Name = "Custom Speed",
    CurrentValue = tostring(customSpeedValue),
    PlaceholderText = "Enter speed",
    RemoveTextAfterFocusLost = false,
    Flag = "Player_SpeedInput",
    Callback = function(Text)
        local n = tonumber(Text)
        if n and n > 0 then customSpeedValue = n end
    end
})

local jumpEnabled = false
local customJumpPower = 30
local jumpConnection = nil

PlayerTab:CreateSection("Jump")

PlayerTab:CreateToggle({
	Name = "Jump Power",
	CurrentValue = false,
	Callback = function(state)
		jumpEnabled = state

		if jumpConnection then
			jumpConnection:Disconnect()
			jumpConnection = nil
		end

		if state then
			jumpConnection = RunService.Heartbeat:Connect(function()
				if humanoid then
					humanoid.UseJumpPower = true
					humanoid.JumpPower = customJumpPower
				end
			end)
		else
			if humanoid then
				humanoid.UseJumpPower = false
				humanoid.JumpPower = 50
			end
		end
	end
})

PlayerTab:CreateInput({
	Name = "Change JumpPower",
	CurrentValue = tostring(customJumpPower),
	PlaceholderText = "Enter jump power",
	RemoveTextAfterFocusLost = false,
	Callback = function(text)
		local num = tonumber(text)
		if num and num > 0 then
			customJumpPower = num
		end
	end
})

local infJump = false
local infJumpConnection = nil

PlayerTab:CreateToggle({
	Name = "Infinite Jumps",
	CurrentValue = false,
	Callback = function(state)
		infJump = state

		if infJumpConnection then
			infJumpConnection:Disconnect()
			infJumpConnection = nil
		end

		if state then
			infJumpConnection = UserInputService.JumpRequest:Connect(function()
				if infJump and humanoid then
					humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
				end
			end)
		end
	end
})

PlayerTab:CreateDivider()

GetClosestPlayerFromCursor = function()
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

task.defer(function()
	local char = me.Character or me.CharacterAdded:wait()
	local hrp = char and FWC(char,"HumanoidRootPart",3)
	local Root = hrp and FWC(hrp,"RootAttachment",3)
	local CamPart = char and FWC(char,"CamPart",3)
	if Root and CamPart then Root.Parent = CamPart end
end)


Keybind = PlayerTab:CreateKeybind({
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
					playTpSound()
				end
			end
		end,
	})

PlayerTab:CreateDivider()

plr = game.Players.LocalPlayer
 cam = workspace.CurrentCamera
mouse = plr:GetMouse()
uis = game:GetService("UserInputService")
inv = workspace:WaitForChild(plr.Name.."SpawnedInToys")
rs = game:GetService("ReplicatedStorage")
rs2 = game:GetService("RunService")
deb = game:GetService("Debris")

function stopVelocityF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    hrp.AssemblyLinearVelocity = Vector3.zero
end

PlayerTab:CreateKeybind({
   Name = "Stop velocity",
   CurrentKeybind = "G",
   HoldToInteract = false,
   Flag = "deleteFlag",
   Callback = function(Keybind)
        stopVelocityF()
    end
})

PlayerTab:CreateSection("Camera")

PlayerTab:CreateSlider({
   Name = "FOV",
   Range = {0, 120},
   Increment = 0.1,
   Suffix = "%",
   CurrentValue = 70,
   Flag = "FOVSlider",
   Callback = function(Value)
       workspace.CurrentCamera.FieldOfView = Value
   end,
})

PlayerTab:CreateToggle({
    Name = "Third person",
    Default = false,
    Callback = function(Value)
        if Value then
            originalMaxZoom = LocalPlayer.CameraMaxZoomDistance
            originalMinZoom = LocalPlayer.CameraMinZoomDistance
            originalCameraMode = LocalPlayer.CameraMode

            LocalPlayer.CameraMaxZoomDistance = math.huge
            LocalPlayer.CameraMinZoomDistance = 0.5
            LocalPlayer.CameraMode = Enum.CameraMode.Classic
        else
            LocalPlayer.CameraMaxZoomDistance = originalMaxZoom
            LocalPlayer.CameraMinZoomDistance = originalMinZoom
            LocalPlayer.CameraMode = originalCameraMode
        end
    end
})

local BreakerTab = Window:CreateTab("Break Map", 4483362458)

BreakerTab:CreateSection("Break Shit")

w = game:GetService("Workspace")
Players = game:GetService("Players")
rs = game:GetService("ReplicatedStorage")
LocalPlayer = Players.LocalPlayer

me = Players.LocalPlayer
BackPack = w[me.Name .. 'SpawnedInToys']

setowner = rs.GrabEvents.SetNetworkOwner
StickyPartEvent = rs.PlayerEvents.StickyPartEvent

BreakerTab:CreateButton({
    Name = "LrgDebris",
    Callback = function()
        local debris = w.Map.AlwaysHereTweenedObjects.LrgDebris.Object.ObjectModel.Rock
        for i = 1,10 do
            local shur = BackPack.NinjaShuriken
            shur.Name = i
            StickyPartEvent:FireServer(shur.StickyPart, debris, CFrame.Angles(0,0,0))
        end
        for i = 1,100 do
            me.Character.HumanoidRootPart.CFrame = debris.CFrame
            setowner:FireServer(debris, debris.CFrame)
            task.wait()
        end
        local obj = w.Map.AlwaysHereTweenedObjects.LrgDebris.Object
        local body = obj.ObjectModel.Rock
        local attach = body:FindFirstChild("ObjectModelAttachment")
        if attach then attach:Destroy() end
        obj.FollowThisPart.AlignPosition.Attachment0 = nil
        obj.FollowThisPart.AlignOrientation.Attachment0 = nil
    end
})

BreakerTab:CreateButton({
    Name = "LrgDebris2",
    Callback = function()
        local debris = w.Map.AlwaysHereTweenedObjects.LrgDebris2.Object.ObjectModel.Rock
        for i = 1,10 do
            local shur = BackPack.NinjaShuriken
            shur.Name = i
            StickyPartEvent:FireServer(shur.StickyPart, debris, CFrame.Angles(0,0,0))
        end
        for i = 1,100 do
            me.Character.HumanoidRootPart.CFrame = debris.CFrame
            setowner:FireServer(debris, debris.CFrame)
            task.wait()
        end
        local obj = w.Map.AlwaysHereTweenedObjects.LrgDebris2.Object
        local body = obj.ObjectModel.Rock
        local attach = body:FindFirstChild("ObjectModelAttachment")
        if attach then attach:Destroy() end
        obj.FollowThisPart.AlignPosition.Attachment0 = nil
        obj.FollowThisPart.AlignOrientation.Attachment0 = nil
    end
})

BreakerTab:CreateButton({
    Name = "Train",
    Callback = function()
        local train = w.Map.AlwaysHereTweenedObjects.Train.Object.ObjectModel.BallOctagon
        for i = 1,10 do
            local shur = BackPack.NinjaShuriken
            shur.Name = i
            StickyPartEvent:FireServer(shur.StickyPart, train, CFrame.Angles(0,0,0))
        end
        for i = 1,100 do
            me.Character.HumanoidRootPart.CFrame = train.CFrame
            setowner:FireServer(train, train.CFrame)
            task.wait()
        end
        local obj = w.Map.AlwaysHereTweenedObjects.Train.Object
        local body = obj.ObjectModel.BallOctagon
        local attach = body:FindFirstChild("ObjectModelAttachment")
        if attach then attach:Destroy() end
        obj.FollowThisPart.AlignPosition.Attachment0 = nil
        obj.FollowThisPart.AlignOrientation.Attachment0 = nil
    end
})

BreakerTab:CreateButton({
   Name = "UFO1 (OuterUFO)",
   Callback = function()
      local UFO = w.Map.AlwaysHereTweenedObjects.OuterUFO.Object.ObjectModel.Body
      for i = 1,10 do
          local shur = BackPack.NinjaShuriken
          shur.Name = i
          StickyPartEvent:FireServer(shur.StickyPart,UFO,CFrame.Angles(0,0,0))
      end
      for i = 1,100 do
          me.Character.HumanoidRootPart.CFrame = UFO.CFrame
          setowner:FireServer(UFO,UFO.CFrame)
          task.wait()
      end
      local obj = w.Map.AlwaysHereTweenedObjects.OuterUFO.Object
      local body = obj.ObjectModel.Body
      local attach = body:FindFirstChild("ObjectModelAttachment")
      if attach then attach:Destroy() end
      obj.FollowThisPart.AlignPosition.Attachment0 = nil
      obj.FollowThisPart.AlignOrientation.Attachment0 = nil
   end
})

BreakerTab:CreateButton({
   Name = "UFO2 (InnerUFO)",
   Callback = function()
      local UFO = w.Map.AlwaysHereTweenedObjects.InnerUFO.Object.ObjectModel.Body
      for i = 1,10 do
          local shur = BackPack.NinjaShuriken
          shur.Name = i
          StickyPartEvent:FireServer(shur.StickyPart,UFO,CFrame.Angles(0,0,0))
      end
      for i = 1,100 do
          me.Character.HumanoidRootPart.CFrame = UFO.CFrame
          setowner:FireServer(UFO,UFO.CFrame)
          task.wait()
      end
      local obj = w.Map.AlwaysHereTweenedObjects.InnerUFO.Object
      local body = obj.ObjectModel.Body
      local attach = body:FindFirstChild("ObjectModelAttachment")
      if attach then attach:Destroy() end
      obj.FollowThisPart.AlignPosition.Attachment0 = nil
      obj.FollowThisPart.AlignOrientation.Attachment0 = nil
   end
})

BreakerTab:CreateButton({
   Name = "CaveCart",
   Callback = function()
      local obj = w.Map.AlwaysHereTweenedObjects.CaveCart.Object
      local model = obj.ObjectModel
      local target = model:GetChildren()[13]
      for i = 1,10 do
          local shur = BackPack.NinjaShuriken
          shur.Name = i
          StickyPartEvent:FireServer(shur.StickyPart,target,CFrame.Angles(0,0,0))
      end
      for i = 1,100 do
          me.Character.HumanoidRootPart.CFrame = target.CFrame
          setowner:FireServer(target,target.CFrame)
          task.wait()
      end
      local attach = target:FindFirstChild("ObjectModelAttachment")
      if attach then attach:Destroy() end
      obj.FollowThisPart.AlignPosition.Attachment0 = nil
      obj.FollowThisPart.AlignOrientation.Attachment0 = nil
   end
})

BreakerTab:CreateDivider()

local Sense, Massless = 30, nil

BreakerTab:CreateToggle({
    Name = 'Massless Grab',
    CurrentValue = false,
    Flag = "MasslessToggle",
    Callback = function(v)
        if v then
            Massless = workspace.ChildAdded:Connect(function(r)
                if r.Name == "GrabParts" then
                    while workspace:FindFirstChild("GrabParts") do
                        task.wait()
                        local dp = r:FindFirstChild("DragPart")
                        if dp and dp:FindFirstChild("AlignPosition") and dp:FindFirstChild("AlignOrientation") then
                            dp.AlignPosition.Responsiveness = Sense
                            dp.AlignPosition.MaxForce = math.huge
                            dp.AlignPosition.MaxVelocity = math.huge
                            dp.AlignOrientation.Responsiveness = Sense
                            dp.AlignOrientation.MaxTorque = math.huge
                        end
                    end
                end
            end)
        else
            if Massless then Massless:Disconnect() Massless = nil end
        end
    end
})

BreakerTab:CreateInput({
    Name = "Massless Sense",
    CurrentValue = tostring(Sense),
    PlaceholderText = "Enter sense value",
    RemoveTextAfterFocusLost = false,
    Callback = function(Text)
        local v = tonumber(Text)
        if v and v > 0 then Sense = v end
    end
})

local AntisTab = Window:CreateTab("Anti", 7734056608)

Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
RunService = game:GetService("RunService")
Debris = game:GetService("Debris")
LocalPlayer = Players.LocalPlayer

local defenseEnabled = false
local defenseConnection = nil
local defenseMode = "Fling"

GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
DestroyGrabLine = GrabEvents:FindFirstChild("DestroyGrabLine")
CreateGrabEvent = GrabEvents:FindFirstChild("CreateGrabLine")

local function getAttacker()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("Head") then return end
    local owner = char.Head:FindFirstChild("PartOwner")
    if not owner or not owner:IsA("StringValue") then return end
    return Players:FindFirstChild(owner.Value)
end

local function performFling(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        local away = (root.Position - LocalPlayer.Character.HumanoidRootPart.Position).Unit
        away = Vector3.new(away.X, 0, away.Z) * 10000
        local bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganFling"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = away
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 0.1)
    end)
end

local function performKill(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        local away = (root.Position - LocalPlayer.Character.HumanoidRootPart.Position).Unit
        away = Vector3.new(away.X, 0, away.Z) * 99999999999999
        local bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganFling"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = away
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 0.1)
    end)
end

local function performHeaven(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        root.CFrame = CFrame.new(0, 100, 0)
        local bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganHeaven"
        bv.MaxForce = Vector3.new(0, math.huge, 0)
        bv.Velocity = Vector3.new(0, 100, 0)
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 5)
    end)
end

local function performKick(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        root.CFrame = CFrame.new(0, 999999999999, 0)
        local bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganHeaven"
        bv.MaxForce = Vector3.new(0, math.huge, 0)
        bv.Velocity = Vector3.new(0, 99999999999999, 0)
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 5)
    end)
end

local function performRagdoll(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        local bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganSpy"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = Vector3.new(0, -90, 0)
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 0.1)
    end)
end

local function performHell(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        for _, part in ipairs(attacker.Character:GetDescendants()) do
            if part:IsA("BasePart") and not part.Anchored then
                part.CanCollide = false
            end
        end
        local bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganSpy"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = Vector3.new(0, -10000000, 0)
        bv.P = 12500
        bv.Parent = root
        local noclipConnection
        noclipConnection = RunService.Heartbeat:Connect(function()
            if not attacker.Character or not attacker.Character.Parent then
                noclipConnection:Disconnect()
                return
            end
            for _, part in ipairs(attacker.Character:GetDescendants()) do
                if part:IsA("BasePart") and not part.Anchored then
                    part.CanCollide = false
                end
            end
        end)
        task.delay(1.5, function()
            if noclipConnection then noclipConnection:Disconnect() end
        end)
        Debris:AddItem(bv, 0.1)
    end)
end

local function performChina(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        root.CFrame = CFrame.new(591, 153, -101)
    end)
end

local crazyline = false
local function performSpamGrabLines(attacker)
    while crazyline do
        local char = LocalPlayer.Character
        if char then
            local head = char:FindFirstChild("Head")
            if head then
                local owner = head:FindFirstChild("PartOwner")
                if owner and owner:IsA("StringValue") then
                    local attacker = Players:FindFirstChild(owner.Value)
                    if attacker and attacker.Character then
                        local attackerHead = attacker.Character:FindFirstChild("Head")
                        local attackerHRP = attacker.Character:FindFirstChild("HumanoidRootPart")
                        if attackerHead and attackerHRP then
                            for i = 1, 3 do
                                pcall(function() CreateGrabEvent:FireServer(attackerHead, attackerHead.CFrame) end)
                            end
                            for i = 1, 3 do
                                pcall(function() CreateGrabEvent:FireServer(attackerHRP, attackerHRP.CFrame) end)
                            end
                        end
                    end
                end
            end
        end
        task.wait(1)
    end
end

local function startDefense()
    if defenseConnection then return end
    defenseConnection = RunService.Heartbeat:Connect(function()
        if not defenseEnabled then return end
        local attacker = getAttacker()
        if not attacker then return end
        if defenseMode == "Fling" then performFling(attacker)
        elseif defenseMode == "Kill" then performKill(attacker)
        elseif defenseMode == "Send to Heaven" then performHeaven(attacker)
        elseif defenseMode == "Kick" then performKick(attacker)
        elseif defenseMode == "Ragdoll" then performRagdoll(attacker)
        elseif defenseMode == "Hell" then performHell(attacker)
        elseif defenseMode == "China" then performChina(attacker)
        elseif defenseMode == "GrabLine" then performSpamGrabLines(attacker)
        end
    end)
end

local function stopDefense()
    if defenseConnection then
        defenseConnection:Disconnect()
        defenseConnection = nil
    end
    for _, plr in ipairs(Players:GetPlayers()) do
        local char = plr.Character
        if char then
            for _, obj in ipairs(char:GetDescendants()) do
                if obj:IsA("BodyVelocity") and (obj.Name == "RinneganFling" or obj.Name == "RinneganHeaven") then
                    obj:Destroy()
                end
            end
        end
    end
end

LocalPlayer.CharacterAdded:Connect(function()
    if defenseEnabled then
        task.wait(1)
        startDefense()
    end
end)

AntisTab:CreateSection("Auto Attacker")

AntisTab:CreateToggle({
    Name = 'Auto-Attack',
    CurrentValue = false,
    Flag = "RinneganDefenseToggle",
    Callback = function(enabled)
        defenseEnabled = enabled
        if enabled then
            startDefense()
        else
            stopDefense()
        end
    end
})

AntisTab:CreateDropdown({
    Name = "Mode",
    Options = {"Fling", "Kill", "Send to Heaven","Kick","Ragdoll","Hell","China","GrabLine"},
    CurrentOption = "Fling",
    Flag = "RinneganMode",
    Callback = function(mode)
        if typeof(mode) == "table" then mode = mode[1] end
        defenseMode = mode
    end
})

AntisTab:CreateDivider()

local gucciEnabled = false
local gucciConnection = nil
local tractor = nil
local vehicleSeat = nil
local safePosition = nil
local restoreFrames = 0
local humanoid, rootPart, seat = nil
local antiGucciConnection = nil
local antiGrabConn = nil
local playerName = LocalPlayer.Name

local function spawnTractor()
    local args = {"TractorGreen", CFrame.new(0, 5000000, 0), Vector3.new(0, 60, 0)}
    ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
    local folder = Workspace:WaitForChild(playerName.."SpawnedInToys", 5)
    if folder and folder:FindFirstChild("TractorGreen") then
        tractor = folder.TractorGreen
        vehicleSeat = tractor:FindFirstChild("VehicleSeat")
        if tractor:FindFirstChild("Main") then
            tractor.Main.CFrame = CFrame.new(0, 50000, 0)
            tractor.Main.Anchored = true
        end
    end
end

local function startGucci()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    humanoid = character:WaitForChild("Humanoid")
    rootPart = character:WaitForChild("HumanoidRootPart")
    safePosition = rootPart.Position

    if vehicleSeat and vehicleSeat:IsA("VehicleSeat") then
        rootPart.CFrame = vehicleSeat.CFrame + Vector3.new(0, 2, 0)
        vehicleSeat:Sit(humanoid)
    end

    humanoid:GetPropertyChangedSignal("Jump"):Connect(function()
        if humanoid.Jump and humanoid.Sit then
            restoreFrames = 15
            safePosition = rootPart.Position
        end
    end)

    if gucciConnection then gucciConnection:Disconnect() end
    gucciConnection = RunService.Heartbeat:Connect(function()
        if not rootPart or not humanoid then return end
        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
        if restoreFrames > 0 then
            rootPart.CFrame = CFrame.new(safePosition)
            restoreFrames = 1
        end
    end)

    task.spawn(function()
        while humanoid.Sit do task.wait(1) end
        task.wait(0.5)
        rootPart.CFrame = CFrame.new(safePosition)
    end)
end

local function stopGucci()
    if gucciConnection then gucciConnection:Disconnect() gucciConnection = nil end
    if tractor then
        ReplicatedStorage.MenuToys.DestroyToy:FireServer(tractor)
        tractor:Destroy()
        tractor = nil
        vehicleSeat = nil
    end
end

AntisTab:CreateToggle({
    Name = "Tractor Gucci (Invisible)",
    CurrentValue = false,
    Callback = function(enabled)
        gucciEnabled = enabled
        if enabled then
            spawnTractor()
            task.wait(0.5)
            if tractor then startGucci() end
        else
            stopGucci()
        end
    end
})

AntisTab:CreateToggle({
    Name = "Gucci Train (Invisible)",
    Callback = function(enabled)
        local train = workspace.Map.AlwaysHereTweenedObjects.Train.Object.ObjectModel
        local Seat = train.Seat
        local hrpP = LocalPlayer.Character.HumanoidRootPart
        local ragdollingevent = ReplicatedStorage.CharacterEvents.RagdollRemote
        local conn
        conn = RunService.Heartbeat:Connect(function()
            task.wait(0.09)
            Seat:Sit(LocalPlayer.Character.Humanoid)
            ragdollingevent:FireServer(hrpP, 0)
            conn:Disconnect()
            Seat:Sit(LocalPlayer.Character.Humanoid)
        end)
    end
})

local function spawnBlobman()
    local args = {"CreatureBlobman", CFrame.new(0, 5000000, 0), Vector3.new(0, 60, 0)}
    ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
    local folder = Workspace:WaitForChild(playerName.."SpawnedInToys", 5)
    if folder and folder:FindFirstChild("CreatureBlobman") then
        local blob = folder.CreatureBlobman
        if blob:FindFirstChild("Head") then
            blob.Head.CFrame = CFrame.new(0, 50000, 0)
            blob.Head.Anchored = true
        end
    end
end

local function startAntiGucci()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    humanoid = character:WaitForChild("Humanoid")
    rootPart = character:WaitForChild("HumanoidRootPart")
    safePosition = rootPart.Position

    local folder = Workspace:FindFirstChild(playerName.."SpawnedInToys")
    local blob = folder and folder:FindFirstChild("CreatureBlobman")
    seat = blob and blob:FindFirstChild("VehicleSeat")

    if seat and seat:IsA("VehicleSeat") then
        rootPart.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
        seat:Sit(humanoid)
    end

    humanoid:GetPropertyChangedSignal("Jump"):Connect(function()
        if humanoid.Jump and humanoid.Sit then
            restoreFrames = 15
            safePosition = rootPart.Position
        end
    end)

    if antiGucciConnection then antiGucciConnection:Disconnect() end
    antiGucciConnection = RunService.Heartbeat:Connect(function()
        if not rootPart or not humanoid then return end
        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
        if restoreFrames > 0 then
            rootPart.CFrame = CFrame.new(safePosition)
            restoreFrames = 1
        end
    end)

    task.spawn(function()
        while humanoid.Sit do task.wait(1) end
        task.wait(0.5)
        rootPart.CFrame = CFrame.new(safePosition)
    end)
end

local function stopAntiGucci()
    if antiGucciConnection then antiGucciConnection:Disconnect() antiGucciConnection = nil end
    local blobFolder = Workspace:FindFirstChild(playerName.."SpawnedInToys")
    if blobFolder and blobFolder:FindFirstChild("CreatureBlobman") then
        blobFolder.CreatureBlobman:Destroy()
    end
end

AntisTab:CreateToggle({
    Name = "Gucci",
    Default = false,
    Callback = function()
        spawnBlobman()
        startAntiGucci()
    end
})

AntisTab:CreateToggle({
    Name = "Anti Grab",
    CurrentValue = false,
    Flag = "AntiGrab",
    Callback = function(enabled)
        if antiGrabConn then antiGrabConn:Disconnect() antiGrabConn = nil end
        if not enabled then return end
        antiGrabConn = RunService.Heartbeat:Connect(function()
            local char = LocalPlayer.Character
            if not char then return end
            local head = char:FindFirstChild("Head")
            local hrp = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChildOfClass("Humanoid")
            if not head or not hrp or not hum then return end
            local grabbedTag = head:FindFirstChild("PartOwner")
            if not grabbedTag then return end
            local oldCF = hrp.CFrame
            for _=1,3 do
                pcall(function() ReplicatedStorage.CharacterEvents.Struggle:FireServer() end)
                pcall(function() ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0) end)
                pcall(function() if DestroyGrabLine then DestroyGrabLine:FireServer() end end)
                hum.Sit = false
                hum.PlatformStand = false
                local rag = hum:FindFirstChild("Ragdolled") or (char:FindFirstChild("Humanoid") and char.Humanoid:FindFirstChild("Ragdolled"))
                if rag and rag:IsA("BoolValue") then rag.Value = false end
                hrp.AssemblyLinearVelocity = Vector3.zero
                hrp.AssemblyAngularVelocity = Vector3.zero
                pcall(function() if StopAllVelocity then StopAllVelocity:FireServer() end end)
                hrp.CFrame = oldCF
                pcall(function() if Look then Look:FireServer(hrp.CFrame) end end)
                pcall(function() hum:ChangeState(Enum.HumanoidStateType.GettingUp) end)
                task.wait()
            end
            pcall(function() hum:ChangeState(Enum.HumanoidStateType.Running) end)
        end)
    end
})

AntiStates = AntiStates or {
    AntiGucci = false,
    InstaGucci = false,
    AntiBurn = false,
    AntiVoid = false,
    AntiKick = false
}

Services = Services or {}
Services.ReplicatedStorage = game:GetService("ReplicatedStorage")
Services.RunService = game:GetService("RunService")

LocalPlayer = game.Players.LocalPlayer


Remotes = Remotes or {}
Remotes.Ragdoll = Services.ReplicatedStorage
    :WaitForChild("CharacterEvents")
    :WaitForChild("RagdollRemote")


MyToys = MyToys or {}
MyToys.Folder = workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")


function GetCharacter(player)
    if player.Character then
        return player.Character
    end
    if player.CharacterAdded then
        return player.CharacterAdded:Wait()
    end
    return nil
end

function GetHRP(character)
    return character and character:FindFirstChild("HumanoidRootPart") or nil
end

function GetHumanoid(character)
    return character and character:FindFirstChild("Humanoid") or nil
end


function FindPlotItem(itemName)
    local ownedPlot = nil

    for i = 1, 5 do
        local plot = workspace.Plots["Plot" .. i]
        local owners = plot.PlotSign.ThisPlotsOwners

        if #owners:GetChildren() > 0 then
            if owners.Value and owners.Value.Value == LocalPlayer.Name then
                ownedPlot = plot
                break
            end
        end
    end

    if not ownedPlot then
        return nil
    end

    local plotItems = workspace.PlotItems[ownedPlot.Name]
    return plotItems and plotItems:FindFirstChild(itemName) or nil
end


function GetSeatedBlobman()
    local char = GetCharacter(LocalPlayer)
    local humanoid = GetHumanoid(char)

    if humanoid then
        local seat = humanoid.SeatPart
        if seat and seat.Parent.Name == "CreatureBlobman" then
            return seat.Parent
        end
    end

    return nil
end


function SpawnToy(toyName, cf)
    if not LocalPlayer.CanSpawnToy then
        return nil
    end

    task.spawn(function()
        local spawnRemote = Services.ReplicatedStorage
            :WaitForChild("MenuToys")
            :WaitForChild("SpawnToyRemoteFunction")

        spawnRemote:InvokeServer(
            toyName,
            cf * CFrame.new(0, 10, 20),
            Vector3.new(0, 0, 0)
        )

        repeat task.wait() until MyToys.Folder:FindFirstChild(toyName)
        task.wait(0.01)

        return MyToys.Folder:FindFirstChild(toyName)
    end)
end


function SetNetworkOwner(part, cf)
    task.spawn(function()
        Services.ReplicatedStorage
            :WaitForChild("GrabEvents")
            :WaitForChild("SetNetworkOwner")
            :FireServer(part, cf)
    end)
end


function AntiBurnLoop()
    while AntiStates.AntiBurn do
        local success, err = pcall(function()
            local char = GetCharacter(LocalPlayer)
            local hrp = GetHRP(char)

            if hrp and hrp:FindFirstChild("FireParticleEmitter") then
                local extinguisher =
                    MyToys.Folder:FindFirstChild("FireExtinguisher")
                    or SpawnToy("FireExtinguisher", hrp.CFrame * CFrame.new(0, 100, 0))

                if extinguisher then
                    local part = extinguisher.ExtinguishPart

                    while hrp:FindFirstChild("FireParticleEmitter") do
                        part.Position = hrp.Position
                        part.Size = Vector3.new(100, 100, 100)
                        Services.RunService.Heartbeat:Wait()

                        part.Position = Vector3.new(-1000, 0, 0)
                        Services.RunService.Heartbeat:Wait()
                    end

                    Services.ReplicatedStorage
                        :WaitForChild("MenuToys")
                        :WaitForChild("DestroyToy")
                        :FireServer(extinguisher)

                    task.wait(0.05)
                end
            end
        end)

        if not success then
            warn("AntiBurn error:", err)
        end

        task.wait(0.01)
    end
end


function BlobmanGrab(playerName, side)
    task.spawn(function()
        local targetPlayer = game.Players:FindFirstChild(playerName)
        if not targetPlayer then return end

        local myChar = GetCharacter(LocalPlayer)
        local myHRP = GetHRP(myChar)
        local myHum = GetHumanoid(myChar)

        local targetChar = GetCharacter(targetPlayer)
        local targetHRP = GetHRP(targetChar)

        if not (myHRP and myHum and targetHRP) then return end

        local blobman =
            GetSeatedBlobman()
            or MyToys.Folder:FindFirstChild("CreatureBlobman")
            or SpawnToy("CreatureBlobman", targetHRP.CFrame)

        if blobman then
            local detector = blobman:WaitForChild(side .. "Detector")
            local weld = detector:FindFirstChild(side .. "Weld")

            blobman.BlobmanSeatAndOwnerScript.CreatureGrab
                :FireServer(detector, targetHRP, weld)
        end
    end)
end


AntiGUI = AntiGUI or {}

AntiGUI.AntiGucciToggle = AntisTab:CreateToggle({
    Name = "Gucci anti-grab (blobman works for plots)",
    CurrentValue = false,
    Flag = "antigucci_toggle",
    Callback = function(Value)
        AntiStates.AntiGucci = Value

        if AntiStates.AntiGucci then
            while AntiStates.AntiGucci do
                task.spawn(function()
                    Remotes.Ragdoll:FireServer(
                        GetHRP(GetCharacter(LocalPlayer)),
                        0
                    )
                end)

                local blobman = GetSeatedBlobman()
                if blobman then
                    local hrp = GetHRP(GetCharacter(LocalPlayer))

                    BlobmanGrab(LocalPlayer.Name, "Left")
                    SetNetworkOwner(
                        hrp,
                        CFrame.new(blobman.GrabbableHitbox.Position)
                    )

                    task.wait(0.2)
                    AntiStates.InstaGucci = false
                    AntiGUI.AntiGucciToggle:Set(false)
                    break
                end

                task.wait()
            end
        end
    end
})


AntiKickToggle = AntisTab:CreateToggle({
    Name = "Anti Kick Best One",
    CurrentValue = false,
    Flag = "AntiKickToggle",
    Callback = function(Value)
        getgenv().AntiKickEnabled = Value
        
        if Value then
            task.spawn(function()
                local plr = game.Players.LocalPlayer
                local inv = workspace[plr.Name.."SpawnedInToys"]
                local hrp

                local setOwner = game.ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
                local stickyEvent = game.ReplicatedStorage:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")
                local destroyrem = game.ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
                local canSpawn = plr:WaitForChild("CanSpawnToy")

                local function getHRP()
                    if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                        return plr.Character.HumanoidRootPart
                    else
                        local character = plr.CharacterAdded:Wait()
                        return character:WaitForChild("HumanoidRootPart")
                    end
                end

                local function CheckForHome()
                    local ToyFolder
                    if not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then 
                        return false
                    end
                    for _, v in pairs(workspace.Plots:GetChildren()) do
                        for _, b in pairs(v.PlotSign.ThisPlotsOwners:GetChildren()) do
                            if b.Value == plr.Name then
                                ToyFolder = workspace.PlotItems[v.Name]
                            end
                        end
                    end
                    if ToyFolder then 
                        return true, ToyFolder
                    else 
                        return false
                    end
                end

                local function StickKunai(kunai)
                    if not kunai or not kunai:FindFirstChild("StickyPart") then return end

                    local currentHRP = getHRP()
                    
                    if kunai:FindFirstChild("SoundPart") then
                        if not kunai["SoundPart"]:FindFirstChild("PartOwner") or kunai["SoundPart"].PartOwner.Value ~= plr.Name then 
                            setOwner:FireServer(kunai.SoundPart, kunai.SoundPart.CFrame)
                        end
                    end
                    
                    stickyEvent:FireServer(
                        kunai.StickyPart,
                        currentHRP:FindFirstChild("FirePlayerPart") or currentHRP:WaitForChild("FirePlayerPart"),
                        CFrame.new(0,0,0) * CFrame.Angles(0,math.rad(90),math.rad(90))
                    )
                    
                    for _, obj in pairs(kunai:GetChildren()) do
                        if obj.Name == "Pyramid" then
                            obj.CanTouch = false
                            obj.CanCollide = false
                            obj.CanQuery = false
                            obj.Transparency = 0
                            local high = Instance.new("Highlight")
                            high.FillColor = Color3.fromRGB(0, 0, 0)
                            high.Parent = obj

                        elseif obj.Name == "Main" then
                            obj.CanTouch = false
                            obj.CanCollide = false
                            obj.CanQuery = false
                            obj.Transparency = 0
                            local high = Instance.new("Highlight")
                            high.FillColor = Color3.fromRGB(255, 255, 255)
                            high.Parent = obj

                        elseif obj:IsA("BasePart") then
                            obj.CanTouch = false
                            obj.CanCollide = false
                            obj.CanQuery = false
                            obj.Transparency = 1
                        end
                    end
                end

                local function ClearKunai()
                    for _,v in pairs(inv:GetChildren()) do
                        if v.Name == "AntiKick" then
                            destroyrem:FireServer(v)
                        end
                    end
                end

                local function SpawnToy(name)
                    while not canSpawn.Value do
                        canSpawn.Changed:Wait()
                    end

                    local currentHRP = getHRP()
                    
                    task.spawn(function()
                        game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                            name,
                            currentHRP.CFrame * CFrame.new(0, 12, 20),
                            Vector3.new(0,0,0)
                        )
                    end)
                    
                    local boolik, house = CheckForHome()
                    if boolik then 
                        return house:WaitForChild(name, 2)
                    elseif not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then 
                        return inv:WaitForChild(name, 2)
                    elseif workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) and not boolik then 
                        return nil
                    end
                end

                while getgenv().AntiKickEnabled do 
                    task.wait(0.005)

                    if not plr.Character or not plr.Character:FindFirstChild("Humanoid") or plr.Character.Humanoid.Health <= 0 then 
                        continue 
                    end
                    
                    local kunai = inv:FindFirstChild("NinjaShuriken")
                    
                    if workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then 
                        local boolik, house = CheckForHome()
                        if boolik and house and workspace.Plots:FindFirstChild(house.Name) and workspace.Plots:FindFirstChild(house.Name)["PlotSign"]["ThisPlotsOwners"]:FindFirstChild("Value") and workspace.Plots:FindFirstChild(house.Name)["PlotSign"]["ThisPlotsOwners"]["Value"]["TimeRemainingNum"].Value > 89 then 
                            kunai = SpawnToy("NinjaShuriken")
                            if kunai == nil then continue end
                            kunai.Name = "AntiKick" 
                            StickKunai(kunai)
                        end
                    end
                    
                    if not kunai then
                        if workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then continue end 
                        kunai = SpawnToy("NinjaShuriken")
                        if kunai == nil then continue end 
                        kunai.Name = "AntiKick"
                        if not kunai then continue end 
                    end
                    
                    repeat
                        if kunai and kunai:FindFirstChild("StickyPart") and kunai.StickyPart.CanTouch == true then
                            StickKunai(kunai)
                            kunai.Name = "AntiKick"
                        end
                        wait(0.3)
                    until not kunai or not getgenv().AntiKickEnabled 
                        or not kunai:FindFirstChild("StickyPart")
                        or kunai.StickyPart.CanTouch == false 
                        or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") 
                        or not kunai:FindFirstChild("StickyPart") 
                        or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20

                    if not kunai or not kunai:FindFirstChild("StickyPart") or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20 then 
                        ClearKunai()
                    end 
                    
                    pcall(function()
                        repeat
                            wait(0.05)
                        until not getgenv().AntiKickEnabled or not plr.Character or not plr.Character:FindFirstChild("Humanoid") or not kunai or not kunai:FindFirstChild("StickyPart") or not kunai.StickyPart:FindFirstChild("StickyWeld") or not kunai.StickyPart.StickyWeld.Part1
                        
                        if not kunai or not kunai:FindFirstChild("StickyPart") or (plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health <= 0) or not kunai["StickyPart"]:FindFirstChild("StickyWeld").Part1 then 
                            ClearKunai()
                        end
                    end)
                end
            end)
        end
    end,
})

Players = game:GetService("Players")
Workspace = game:GetService("Workspace")
ReplicatedStorage = game:GetService("ReplicatedStorage")
LocalPlayer = Players.LocalPlayer

antiblob = false
antiblobConnection = nil
truePosPart = nil

function createTruePospart()
    char = LocalPlayer.Character
    if not char then return end
    
    if char:FindFirstChild("TruePositionPart") then
        return char.TruePositionPart
    end
    
    tp = Instance.new("Part")
    tp.Name = "TruePositionPart"
    tp.Anchored = true
    tp.CanCollide = false
    tp.Transparency = 1
    tp.Size = Vector3.new(1, 1, 1)
    tp.CFrame = CFrame.new(0, -100, 0)
    tp.Parent = char
    
    return tp
end

function dropFromBlobs()
    char = LocalPlayer.Character
    if not char then return end
    
    hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    
    for _, plot in pairs(Workspace.PlotItems:GetChildren()) do
        if plot.Name ~= "PlayersInPlots" then
            for _, itm in pairs(plot:GetChildren()) do
                if itm.Name == "CreatureBlobman" then
                    pcall(function()
                        script = itm:FindFirstChild("BlobmanSeatAndOwnerScript")
                        detector = itm:FindFirstChild("RightDetector")
                        
                        if script and detector then
                            drop = script:FindFirstChild("CreatureDrop")
                            weld = detector:FindFirstChild("RightWeld")
                            
                            if drop and weld then
                                drop:FireServer(weld, hrp)
                                ReplicatedStorage.CharacterEvents.Struggle:FireServer(LocalPlayer)
                            end
                        end
                    end)
                end
            end
        end
    end
    
    for _, plr in pairs(Players:GetPlayers()) do
        toysFolder = Workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
        if toysFolder then
            for _, itm in pairs(toysFolder:GetChildren()) do
                if itm.Name == "CreatureBlobman" then
                    pcall(function()
                        script = itm:FindFirstChild("BlobmanSeatAndOwnerScript")
                        detector = itm:FindFirstChild("RightDetector")
                        
                        if script and detector then
                            drop = script:FindFirstChild("CreatureDrop")
                            weld = detector:FindFirstChild("RightWeld")
                            
                            if drop and weld then
                                drop:FireServer(weld, hrp)
                                ReplicatedStorage.CharacterEvents.Struggle:FireServer(LocalPlayer)
                            end
                        end
                    end)
                end
            end
        end
    end
end

function setMassless()
    char = LocalPlayer.Character
    if not char then return end
    
    for _, prt in pairs(char:GetChildren()) do
        if prt:IsA("BasePart") and prt.Massless then
            prt.Massless = false
            dropFromBlobs()
        end
    end
end

function moveRootAttachment()
    char = LocalPlayer.Character
    if not char then return end
    
    hrp = char:FindFirstChild("HumanoidRootPart")
    truePart = char:FindFirstChild("TruePositionPart")
    
    if hrp and truePart then
        rootAttachment = hrp:FindFirstChild("RootAttachment")
        
        if rootAttachment then
            task.wait(0.2)
            rootAttachment.Parent = truePart
            
            Rayfield:Notify({
                Title = "Antiblob",
                Content = "Antiblob has been enabled",
                Duration = 3,
                Image = 4483362458,
            })
        end
    end
end

function restoreRootAttachment()
    char = LocalPlayer.Character
    if not char then return end
    
    hrp = char:FindFirstChild("HumanoidRootPart")
    truePart = char:FindFirstChild("TruePositionPart")
    
    if hrp and truePart then
        rootAttachment = truePart:FindFirstChild("RootAttachment")
        
        if rootAttachment then
            rootAttachment.Parent = hrp
            
            Rayfield:Notify({
                Title = "Antiblob",
                Content = "Antiblob has been disabled",
                Duration = 3,
                Image = 4483362458,
            })
        end
        
        truePart:Destroy()
    end
end

function antiblobLoop()
    while antiblob do
        pcall(function()
            if LocalPlayer.Character then
                createTruePospart()
                setMassless()
                
                hrp = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                if hrp and hrp:FindFirstChild("RootAttachment") then
                    truePart = LocalPlayer.Character:FindFirstChild("TruePositionPart")
                    if truePart and not truePart:FindFirstChild("RootAttachment") then
                        moveRootAttachment()
                    end
                end
            end
        end)
        
        task.wait(0.1)
    end
end

AntisTab:CreateToggle({
    Name = 'Antiblob <font face="GothamBlack" color="rgb(160, 0, 255)">(anti SeatAndOwnerScript use tuff 🤑)</font>',
    CurrentValue = false,
    Flag = "AntiblobToggle",
    Callback = function(Value)
        antiblob = Value
        
        if Value then
            if antiblobConnection then
                antiblobConnection:Disconnect()
            end
            
            task.spawn(antiblobLoop)
            
            antiblobConnection = LocalPlayer.CharacterAdded:Connect(function(char)
                if antiblob then
                    task.wait(1)
                    createTruePospart()
                    task.wait(0.5)
                    moveRootAttachment()
                end
            end)
        else
            if antiblobConnection then
                antiblobConnection:Disconnect()
                antiblobConnection = nil
            end
            
            restoreRootAttachment()
        end
    end,
})



GrabData = {
    toggle = false,
    lp = game.Players.LocalPlayer,
    ws = workspace,
    dropPos = CFrame.new(-238.98, -256.01, -123.97)
}
 function GrabItem(item)
    local hold = item:FindFirstChild("HoldPart")
    if not hold then return end
    
    local grab = hold:FindFirstChild("HoldItemRemoteFunction")
    local drop = hold:FindFirstChild("DropItemRemoteFunction")
    
    if grab and drop then
        pcall(function()
            grab:InvokeServer(item, GrabData.lp.Character)
        end)
        pcall(function()
            drop:InvokeServer(item, GrabData.dropPos, Vector3.new())
        end)
    end
end
 function GrabFolder(folder)
    for _, obj in ipairs(folder:GetDescendants()) do
        if obj:IsA("Model") and obj:FindFirstChild("HoldPart") then
            local holdPart = obj.HoldPart
            if holdPart:FindFirstChild("HoldItemRemoteFunction") then
                GrabItem(obj)
            end
        end
    end
end

function LoopGrab()
    while GrabData.toggle do
        for _, folder in ipairs(GrabData.ws:GetChildren()) do
            if folder:IsA("Folder") and folder.Name:find("SpawnedInToys") then
                GrabFolder(folder)
            end
        end
        
        local plotItems = GrabData.ws:FindFirstChild("PlotItems")
        if plotItems then
            for _, plot in pairs(plotItems:GetChildren()) do
                if plot.Name ~= "PlayersInPlots" then
                    for _, model in pairs(plot:GetChildren()) do
                        if model:IsA("Model") and model:FindFirstChild("HoldPart") then
                            local holdPart = model.HoldPart
                            if holdPart:FindFirstChild("HoldItemRemoteFunction") then
                                GrabItem(model)
                            end
                        end
                    end
                end
            end
        end
        
        task.wait()
    end
end

AntisTab:CreateToggle({
    Name = "Deletes all HoldPart Toys",
    Default = false,
    Callback = function(state)
        GrabData.toggle = state
        if state then
            task.spawn(LoopGrab)
        end
    end
})

vu43 = {}
vu43.anti_lagg_en = false
vu43.auto_anti_lagg_en = false

vu37 = {}
vu27 = game.Players.LocalPlayer
vu37.beam_move = vu27.PlayerScripts.CharacterAndBeamMove

vu40 = {}
vu40.lines_per_01sec = 0
vu40.line_owner = ""

vu28 = {}
vu28.Flags = {}
vu28.Flags.antilagg_toggle = {}
vu28.Flags.antilagg_toggle.Value = false
vu28.Flags.antilagg_toggle.Set = function(value)
    vu28.Flags.antilagg_toggle.Value = value
    vu43.anti_lagg_en = value
    vu37.beam_move.Disabled = value
end

vu26 = {}
vu26.others = game:GetService("Players")

vu79 = function(title, message, duration)
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = title,
        Text = message,
        Duration = duration
    })
end

vu43 = {}
vu43.anti_lagg_en = false
vu43.auto_anti_lagg_en = false

vu37 = {}
vu27 = game.Players.LocalPlayer
vu37.beam_move = vu27.PlayerScripts.CharacterAndBeamMove

vu40 = {}
vu40.lines_per_01sec = 0
vu40.line_owner = ""

vu28 = {}
vu28.Flags = {}
vu28.Flags.antilagg_toggle = {}
vu28.Flags.antilagg_toggle.Value = false
vu28.Flags.antilagg_toggle.Set = function(value)
    vu28.Flags.antilagg_toggle.Value = value
    vu43.anti_lagg_en = value
    vu37.beam_move.Disabled = value
end

vu26 = {}
vu26.others = game:GetService("Players")

vu79 = function(title, message, duration)
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = title,
        Text = message,
        Duration = duration
    })
end

v52 = {}
v52.defense_section = {}

v52.defense_section.AddToggle = function(config)
    print("Toggle created:", config.Name)
    if config.Callback then
        config.Callback(config.Default)
    end
end

AntisTab:CreateToggle({
    Name = "Anti Lag",
    Default = false,
    Callback = function(Value)
        local characterScript = LocalPlayer.PlayerScripts:FindFirstChild("CharacterAndBeamMove")
        if characterScript then
            characterScript.Disabled = Value
        end
    end
})

players = game:GetService("Players")
runService = game:GetService("RunService")
localPlayer = game.Players.LocalPlayer
replicatedStorage = game:GetService("ReplicatedStorage")
workspace = game:GetService("Workspace")

myToysFolder = workspace:WaitForChild(localPlayer.Name .. "SpawnedInToys")

antiBlobmanEnabled = false
antiBlobmanGrabEnabled = false
instaGucci = false
antiBlobmanConnection = nil

function getCharacter(player)
    char = player.Character
    if not char then
        if player.CharacterAdded then
            char = player.CharacterAdded:Wait() or nil
        else
            char = nil
        end
    end
    return char
end

function getHumanoidRootPart(character)
    return character:FindFirstChild("HumanoidRootPart") or nil
end

function getHumanoid(character)
    return character:FindFirstChild("Humanoid") or nil
end

function getDistance(part1, part2)
    return (part1.Position - part2.Position).Magnitude
end

function getAllToys(includePlots)
    allToys = {}
    playersList = players:GetPlayers()
    
    for _, player in ipairs(playersList) do
        toyFolder = workspace[player.Name .. "SpawnedInToys"]
        for _, toy in pairs(toyFolder:GetChildren()) do
            if toy:IsA("Model") then
                table.insert(allToys, toy)
            end
        end
    end
    
    if includePlots then
        for i = 1, 5 do
            for _, item in pairs(workspace.PlotItems["Plot" .. i]:GetChildren()) do
                if item:IsA("Model") then
                    table.insert(allToys, item)
                end
            end
        end
    end
    
    return allToys
end

function spawnToy(toyName, cframe)
    if localPlayer.CanSpawnToy then
        task.spawn(function()
            toySpawn = replicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
            toySpawn:InvokeServer(toyName, cframe * CFrame.new(0, 10, 20), Vector3.new(0, 0, 0))
            repeat
                task.wait()
            until myToysFolder:FindFirstChild(toyName)
            task.wait(0.01)
            return myToysFolder:FindFirstChild(toyName)
        end)
    end
end

function destroyToy(toy)
    task.spawn(function()
        success, err = pcall(function()
            toyDestroy = replicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
            toyDestroy:FireServer(toy)
        end)
        if not success then
            warn("Error: " .. err)
        end
    end)
end

function setNetworkOwner(part, cframe)
    task.spawn(function()
        setNetworkOwnerRemote = replicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
        setNetworkOwnerRemote:FireServer(part, cframe)
    end)
end

function toggleConstraint(constraint, enabled)
    if constraint then
        if constraint.Name ~= "LeftWeld" then
            if constraint.Name ~= "LeftAlignOrientation" then
                if constraint.Name ~= "RightWeld" then
                    if constraint.Name == "RightAlignOrientation" and (constraint and constraint.Parent.Parent.Parent ~= myToysFolder) then
                        constraint.Enabled = enabled
                    end
                elseif constraint and constraint.Parent.Parent.Parent ~= myToysFolder then
                    constraint.Enabled = enabled
                end
            elseif constraint and constraint.Parent.Parent.Parent ~= myToysFolder then
                constraint.Enabled = enabled
            end
        elseif constraint and constraint.Parent.Parent.Parent ~= myToysFolder then
            constraint.Enabled = enabled
        end
    end
end

function updateAntiBlobman()
    pcall(function()
        if antiBlobmanConnection then
            antiBlobmanConnection:Disconnect()
        end
    end)
    
    allToys = getAllToys(true)
    
    if antiBlobmanEnabled then
        for _, toy in pairs(allToys) do
            if toy.Name == "CreatureBlobman" then
                leftDetector = toy:FindFirstChild("LeftDetector")
                rightDetector = toy:FindFirstChild("RightDetector")
                leftWeld = leftDetector and leftDetector:FindFirstChild("LeftWeld")
                leftAlign = leftDetector and leftDetector:FindFirstChild("LeftAlignOrientation")
                rightWeld = rightDetector and rightDetector:FindFirstChild("RightWeld")
                rightAlign = rightDetector and rightDetector:FindFirstChild("RightAlignOrientation")
                
                toggleConstraint(leftWeld, false)
                toggleConstraint(leftAlign, false)
                toggleConstraint(rightWeld, false)
                toggleConstraint(rightAlign, false)
            end
        end
        
        antiBlobmanConnection = workspace.DescendantAdded:Connect(function(descendant)
            toggleConstraint(descendant, false)
        end)
    else
        for _, toy in pairs(allToys) do
            if toy.Name == "CreatureBlobman" then
                leftDetector = toy:FindFirstChild("LeftDetector")
                rightDetector = toy:FindFirstChild("RightDetector")
                leftWeld = leftDetector and leftDetector:FindFirstChild("LeftWeld")
                leftAlign = leftDetector and leftDetector:FindFirstChild("LeftAlignOrientation")
                rightWeld = rightDetector and rightDetector:FindFirstChild("RightWeld")
                rightAlign = rightDetector and rightDetector:FindFirstChild("RightAlignOrientation")
                
                toggleConstraint(leftWeld, true)
                toggleConstraint(leftAlign, true)
                toggleConstraint(rightWeld, true)
                toggleConstraint(rightAlign, true)
            end
        end
    end
end

function ragdollFall()
    rootPart = getHumanoidRootPart(getCharacter(localPlayer))
    if rootPart then
        ragdollRemote = replicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote")
        ragdollRemote:FireServer(rootPart, 1)
    end
end

function antiBananaDeleteLegs()
    if getCharacter(localPlayer) ~= nil then
        task.spawn(function()
            ragdollFall()
        end)
        task.wait(0.1)
        character = getCharacter(localPlayer)
        rightLeg = character:FindFirstChild("Right Leg")
        leftLeg = character:FindFirstChild("Left Leg")
        clonedLeg = rightLeg:Clone()
        clonedLeg.Parent = character
        clonedLeg.BallSocketConstraint:Destroy()
        task.wait(0.1)
        rightLeg.CFrame = CFrame.new(0, -59999, 0)
        leftLeg.CFrame = CFrame.new(0, -59999, 0)
        task.wait()
        cup = spawnToy("CupMugBrown", getHumanoidRootPart(getCharacter(localPlayer)).CFrame)
        cup = myToysFolder:WaitForChild("CupMugBrown")
        setNetworkOwner(getHumanoidRootPart(character), CFrame.new(cup.Hitbox.Position))
        task.wait()
        destroyToy(cup)
        clonedLeg.CFrame = CFrame.new(0, 9999999, 0)
        clonedLeg.Massless = true
    end
end

AntisTab:CreateToggle({
    Name = "Anti blobman aura grab",
    Default = false,
    Color = Color3.fromRGB(102, 0, 102),
    Flag = "antiblobmanaura_toggle",
    Callback = function(enabled)
        antiBlobmanGrabEnabled = enabled
        while antiBlobmanGrabEnabled do
            for _, player in pairs(players:GetPlayers()) do
                if player and getCharacter(player) then
                    playerCharacter = getCharacter(player)
                    playerRootPart = nil
                    if playerCharacter then
                        playerRootPart = getHumanoidRootPart(playerCharacter)
                    end
                    playerHumanoid = nil
                    if playerCharacter then
                        playerHumanoid = getHumanoid(playerCharacter)
                    end
                    myCharacter = getCharacter(localPlayer)
                    myRootPart = nil
                    if myCharacter then
                        myRootPart = getHumanoidRootPart(myCharacter)
                    end
                    if player.Name ~= localPlayer.Name and (playerRootPart and (myRootPart and (playerHumanoid and (playerHumanoid.SeatPart and (playerHumanoid.SeatPart.Parent and (playerHumanoid.SeatPart.Parent.Name == "CreatureBlobman" and getDistance(playerRootPart, myRootPart) <= 19)))))) then
                        setNetworkOwner(playerRootPart, playerRootPart.CFrame)
                        task.wait(0.01)
                    end
                end
            end
            task.wait(0.01)
        end
    end
})

AntisTab:CreateButton({
    Name = "Anti banana - delete legs",
    Callback = function()
        antiBananaDeleteLegs()
    end
})

antiok = true

	AS = AntisTab:CreateToggle({
		Name = "Anti Ownership Kick",
		Default = true,
		Callback = function(Value)
			antiok = Value
			task.spawn(function()
				while antiok do
					if game.Players.LocalPlayer.Character then
						if game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
							if game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame.Position.Magnitude > 10000000 then
								if SL ~= 'off' then
									game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame = CFrame.new(SL)
									game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").AssemblyLinearVelocity = Vector3.new(0,0,0)
								else
									game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame = CFrame.new(0,-10,0)
									game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").AssemblyLinearVelocity = Vector3.new(0,0,0)
								end
							end
						end
					end
					task.wait()
				end
			end)
		end    
	})

Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
RunService = game:GetService("RunService")

LocalPlayer = Players.LocalPlayer
folder = workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")

currentFood = nil
lastSpawn = 0
spawnCooldown = 0.5
Root = nil
FoodToggle = false
HeartbeatConnection = nil

function gethrp()
    if LocalPlayer.Character then
        hrp = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if hrp then
            Root = hrp
        end
    end
    return Root
end

function spawnfood()
    if tick() - lastSpawn < spawnCooldown then return end
    lastSpawn = tick()
    if not Root then return end

    args = {
        "FoodHamburger",
        Root.CFrame * CFrame.new(5, 0, 5),
        Vector3.new(0, 33.0880012512207, 0)
    }

    ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
end

function hold(food)
    if not food or not food.Parent then return end
    holdPart = food:FindFirstChild("HoldPart")
    if not holdPart then return end

    holdArgs = { food, LocalPlayer.Character }
    dropArgs = {
        food,
        CFrame.new(-128.37586975097656, -10.35040283203125, 72.18002319335938),
        Vector3.new(0, 154.77699279785156, 0)
    }

    holdPart.HoldItemRemoteFunction:InvokeServer(unpack(holdArgs))
    holdPart.DropItemRemoteFunction:InvokeServer(unpack(dropArgs))
end

folder.ChildAdded:Connect(function(child)
    if child.Name == "FoodHamburger" then
        currentFood = child
    end
end)

AntisTab:CreateToggle({
    Name = "Burger Loop",
    Default = false,
    Callback = function(v)
        FoodToggle = v

        if v then
            HeartbeatConnection = RunService.Heartbeat:Connect(function()
                gethrp()

                if not currentFood or not currentFood.Parent then
                    currentFood = nil
                    spawnfood()
                else
                    hold(currentFood)
                end
            end)
        else
            if HeartbeatConnection then
                HeartbeatConnection:Disconnect()
                HeartbeatConnection = nil
            end
        end
    end
})


vu43 = vu43 or {}
vu43.antigucci_en = false
vu43.insta_gucci = false
vu43.anti_burn_en = false
vu43.anti_void_en = false
vu43.antikick_en = false

vu26 = vu26 or {}
vu26.replicated_storage = game:GetService("ReplicatedStorage")

vu27 = game.Players.LocalPlayer

vu32 = vu32 or {}
vu32.ragdoll = vu26.replicated_storage:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote")

vu37 = vu37 or {}
vu37.my_toys = workspace:WaitForChild(vu27.Name .. "SpawnedInToys")

vu53 = vu53 or {}

vu82 = function(player)
    local char = player.Character
    if not char then
        if player.CharacterAdded then
            char = player.CharacterAdded:Wait() or nil
        else
            char = nil
        end
    end
    return char
end

vu84 = function(character)
    return character:FindFirstChild("HumanoidRootPart") or nil
end

vu86 = function(character)
    return character:FindFirstChild("Humanoid") or nil
end

vu107 = function(itemName)
    local plot = nil
    for i = 1, 5 do
        local plotOwners = workspace.Plots["Plot" .. i].PlotSign.ThisPlotsOwners
        if #plotOwners:GetChildren() ~= 0 then
            if plotOwners.Value then
                if plotOwners.Value.Value == vu27.Name then
                    plot = workspace.Plots["Plot" .. i]
                end
            end
        end
    end
    
    if plot == nil then
        return nil
    end
    
    local plotItems = workspace.PlotItems[plot.Name]
    if plotItems == nil then
        return nil
    else
        return plotItems:FindFirstChild(itemName)
    end
end

vu109 = function()
    if vu86(vu82(vu27)) then
        local seatPart = vu27.Character.Humanoid.SeatPart
        if seatPart and seatPart.Parent.Name == "CreatureBlobman" then
            return seatPart.Parent
        end
    end
end

vu177 = function(toyName, cframe)
    if vu27.CanSpawnToy then
        task.spawn(function()
            local toySpawn = vu26.replicated_storage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
            toySpawn:InvokeServer(toyName, cframe * CFrame.new(0, 10, 20), Vector3.new(0, 0, 0))
            repeat
                task.wait()
            until vu37.my_toys:FindFirstChild(toyName)
            task.wait(0.01)
            return vu37.my_toys:FindFirstChild(toyName)
        end)
    end
end

vu196 = function(part, cframe)
    task.spawn(function()
        local setNetworkOwner = vu26.replicated_storage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
        setNetworkOwner:FireServer(part, cframe)
    end)
end

vu294 = function()
    while vu43.anti_burn_en do
        local success, err = pcall(function()
            local character = vu82(vu27)
            if character then
                local hrp = vu84(character)
                local fireExtinguisher = hrp and hrp:FindFirstChild("FireParticleEmitter") and (vu37.my_toys:FindFirstChild("FireExtinguisher") or vu177("FireExtinguisher", hrp.CFrame * CFrame.new(0, 100, 0)))
                if fireExtinguisher then
                    local extinguishPart = fireExtinguisher.ExtinguishPart
                    while hrp:FindFirstChild("FireParticleEmitter") do
                        extinguishPart.Position = hrp.Position
                        extinguishPart.Size = Vector3.new(100, 100, 100)
                        game:GetService("RunService").Heartbeat:Wait()
                        extinguishPart.Position = Vector3.new(-1000, 0, 0)
                        game:GetService("RunService").Heartbeat:Wait()
                    end
                    local toyDestroy = vu26.replicated_storage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
                    toyDestroy:FireServer(fireExtinguisher)
                    task.wait(0.05)
                end
            end
        end)
        if not success then
            warn("Error: " .. err)
        end
        task.wait(0.01)
    end
end

vu404 = function(playerName, side)
    task.spawn(function()
        local targetPlayer = game.Players:FindFirstChild(playerName) or playerName
        local myChar = vu82(vu27)
        local myHRP = myChar and vu84(myChar)
        local myHum = myChar and vu86(myChar)
        
        local targetChar = targetPlayer and vu82(targetPlayer)
        local targetHRP = targetChar and vu84(targetChar)
        
        if myHRP and myHum and targetHRP then
            local blobman = vu109() or (vu37.my_toys:FindFirstChild("CreatureBlobman") or vu177("CreatureBlobman", targetHRP.CFrame))
            
            if blobman then
                local blobScript = blobman.BlobmanSeatAndOwnerScript
                local detector = blobman:WaitForChild(side .. "Detector")
                local weld = detector:FindFirstChild(side .. "Weld")
                blobScript.CreatureGrab:FireServer(detector, targetHRP, weld)
            end
        end
    end)
end



 AntiBurnToggle = AntisTab:CreateToggle({
    Name = "Anti burn",
    CurrentValue = false,
    Flag = "antiburn_toggle",
    Callback = function(Value)
        vu43.anti_burn_en = Value
        vu294()
    end,
})

antiVoidEnabled = false
AntisTab:CreateToggle({
    Name = "Anti Void",
    CurrentValue = false,
    Flag = "AntiVoid",
    Callback = function(v)
        antiVoidEnabled = v
        if v then
            workspace.FallenPartsDestroyHeight = -100000
            task.spawn(function()
                while antiVoidEnabled do
                    local char = LocalPlayer.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    if hrp and hrp.Position.Y < -500 then
                        hrp.CFrame = CFrame.new(2, -7, -4)
                    end
                    task.wait(0.2)
                end
            end)
        else
            workspace.FallenPartsDestroyHeight = -100
        end
    end,
})



 AuraTab = Window:CreateTab("Auras", 4483362458)

Players = game:GetService("Players")
RunService = game:GetService("RunService")
Debris = game:GetService("Debris")
ReplicatedStorage = game:GetService("ReplicatedStorage")

localPlayer = Players.LocalPlayer
 SetNetworkOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")


 flingConnection, tpConnection, killConnection, killAura2Connection, launchConnection = nil,nil,nil,nil,nil

 function setNetwork(targetPart)
	if targetPart then
		SetNetworkOwner:FireServer(targetPart, localPlayer.Character.HumanoidRootPart.CFrame)
	end
end

 function startFlingAura()
	local FLING_RADIUS = 25
	local FLING_FORCE = 999999999999999999999999

	flingConnection = RunService.RenderStepped:Connect(function()
		local myCharacter = localPlayer.Character
		if not myCharacter then return end
		local myHRP = myCharacter:FindFirstChild("HumanoidRootPart")
		if not myHRP then return end

		for _, player in ipairs(Players:GetPlayers()) do
			if player ~= localPlayer and player.Character then
				local targetHRP = player.Character:FindFirstChild("HumanoidRootPart")
				if targetHRP and (targetHRP.Position - myHRP.Position).Magnitude <= FLING_RADIUS then
					setNetwork(targetHRP)
					local direction = (targetHRP.Position - myHRP.Position).Unit
					direction = Vector3.new(direction.X, 0, direction.Z)
					local bodyVelocity = Instance.new("BodyVelocity")
					bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
					bodyVelocity.Velocity = direction * FLING_FORCE
					bodyVelocity.Parent = targetHRP
					Debris:AddItem(bodyVelocity, 0.1)
				end
			end
		end
	end)
end
 function stopFlingAura()
	if flingConnection then
		flingConnection:Disconnect()
		flingConnection = nil
	end
end

 function startTPAura()
	local TELEPORT_RADIUS = 25
	local TARGET_POSITION = Vector3.new(592, 153, -101)

	tpConnection = RunService.RenderStepped:Connect(function()
		local myCharacter = localPlayer.Character
		if not myCharacter then return end
		local myHRP = myCharacter:FindFirstChild("HumanoidRootPart")
		if not myHRP then return end

		for _, player in ipairs(Players:GetPlayers()) do
			if player ~= localPlayer and player.Character then
				local targetHRP = player.Character:FindFirstChild("HumanoidRootPart")
				if targetHRP and (targetHRP.Position - myHRP.Position).Magnitude <= TELEPORT_RADIUS then
					setNetwork(targetHRP)
					targetHRP.CFrame = CFrame.new(TARGET_POSITION)
				end
			end
		end
	end)
end

 function stopTPAura()
	if tpConnection then
		tpConnection:Disconnect()
		tpConnection = nil
	end
end

 function startKillAura()
	local TELEPORT_RADIUS = 25
	local TARGET_POSITION = Vector3.new(-569, -99, 50)

	killConnection = RunService.RenderStepped:Connect(function()
		local myCharacter = localPlayer.Character
		if not myCharacter then return end
		local myHRP = myCharacter:FindFirstChild("HumanoidRootPart")
		if not myHRP then return end

		for _, player in ipairs(Players:GetPlayers()) do
			if player ~= localPlayer and player.Character then
				local targetHRP = player.Character:FindFirstChild("HumanoidRootPart")
				if targetHRP and (targetHRP.Position - myHRP.Position).Magnitude <= TELEPORT_RADIUS then
					setNetwork(targetHRP)
					targetHRP.CFrame = CFrame.new(TARGET_POSITION)
				end
			end
		end
	end)
end

function stopKillAura()
	if killConnection then
		killConnection:Disconnect()
		killConnection = nil
	end
end

function setNetwork2(targetPart)
	if targetPart then
		SetNetworkOwner:FireServer(targetPart, localPlayer.Character.HumanoidRootPart.CFrame)
	end
end

function startKillAura2()
	local KILL_RADIUS_2 = 25

	killAura2Connection = RunService.RenderStepped:Connect(function()
		local myCharacter = localPlayer.Character
		if not myCharacter then return end
		local myHRP = myCharacter:FindFirstChild("HumanoidRootPart")
		if not myHRP then return end

		for _, player in ipairs(Players:GetPlayers()) do
			if player ~= localPlayer and player.Character then
				local targetHRP = player.Character:FindFirstChild("HumanoidRootPart")
				local humanoid = player.Character:FindFirstChild("Humanoid")
				if targetHRP and humanoid and (targetHRP.Position - myHRP.Position).Magnitude <= KILL_RADIUS_2 then
					setNetwork2(targetHRP)
					humanoid.Health = 0
				end
			end
		end
	end)
end

 function stopKillAura2()
	if killAura2Connection then
		killAura2Connection:Disconnect()
		killAura2Connection = nil
	end
end

AuraTab:CreateToggle({
	Name = "Fling Aura",
	CurrentValue = false,
	Callback = function(value)
		if value then
			startFlingAura()
		else
			stopFlingAura()
		end
	end,
})

AuraTab:CreateToggle({
	Name = "Teleport Aura",
	CurrentValue = false,
	Callback = function(value)
		if value then
			startTPAura()
		else
			stopTPAura()
		end
	end,
})

AuraTab:CreateToggle({
	Name = "Kill Aura",
	CurrentValue = false,
	Callback = function(value)
		if value then
			startKillAura()
		else
			stopKillAura()
		end
	end,
})

AuraTab:CreateToggle({
	Name = "KillAura 2 (slow but good)",
	CurrentValue = false,
	Callback = function(value)
		if value then
			startKillAura2()
		else
			stopKillAura2()
		end
	end,
})

local function startLaunchAura()
	local EFFECT_RADIUS = 25
	local LAUNCH_FORCE = Vector3.new(0, 100, 0)
	local playerData = {}

	launchConnection = RunService.RenderStepped:Connect(function()
		local myCharacter = localPlayer.Character
		if not myCharacter then return end
		local myHRP = myCharacter:FindFirstChild("HumanoidRootPart")
		if not myHRP then return end

		for _, player in ipairs(Players:GetPlayers()) do
			if player ~= localPlayer and player.Character then
				local targetHRP = player.Character:FindFirstChild("HumanoidRootPart")
				if targetHRP and (targetHRP.Position - myHRP.Position).Magnitude <= EFFECT_RADIUS then
					setNetwork(targetHRP)
					if not playerData[player] then
						local bodyVelocity = Instance.new("BodyVelocity")
						bodyVelocity.Name = "LaunchForce"
						bodyVelocity.Velocity = LAUNCH_FORCE
						bodyVelocity.MaxForce = Vector3.new(1e10, 1e10, 1e10)
						bodyVelocity.P = 1e9
						bodyVelocity.Parent = targetHRP
						playerData[player] = true
					end
				end
			end
		end
	end)
end

function stopLaunchAura()
	if launchConnection then
		launchConnection:Disconnect()
		launchConnection = nil
	end
end

AuraTab:CreateToggle({
	Name = "PermKill Aura (ragdoll player first)",
	CurrentValue = false,
	Callback = function(value)
		if value then
			startLaunchAura()
		else
			stopLaunchAura()
		end
	end,
})

local function setNetwork3(targetPart)
	if targetPart then
		SetNetworkOwner:FireServer(targetPart, localPlayer.Character.HumanoidRootPart.CFrame)
	end
end

local function startKillAura3()
	local KILL_RADIUS_3 = 25

	killAura3Connection = RunService.RenderStepped:Connect(function()
		local myCharacter = localPlayer.Character
		if not myCharacter then return end
		local myHRP = myCharacter:FindFirstChild("HumanoidRootPart")
		if not myHRP then return end

		for _, player in ipairs(Players:GetPlayers()) do
			if player ~= localPlayer and player.Character then
				local targetHRP = player.Character:FindFirstChild("HumanoidRootPart")
				local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
				if targetHRP and humanoid and (targetHRP.Position - myHRP.Position).Magnitude <= KILL_RADIUS_3 then
					setNetwork3(targetHRP)
					humanoid.BreakJointsOnDeath = false
					humanoid.RequiresNeck = false
					pcall(function()
						humanoid.MaxHealth = 0
					end)
					humanoid.Health = 0
				end
			end
		end
	end)
end

local function stopKillAura3()
	if killAura3Connection then
		killAura3Connection:Disconnect()
		killAura3Connection = nil
	end
end

AuraTab:CreateToggle({
	Name = "KillAura 3",
	CurrentValue = false,
	Flag = "KillAura3Toggle",
	Callback = function(state)
		if state then
			startKillAura3()
		else
			stopKillAura3()
		end
	end,
})

crazyAuraConnection = nil

function startCrazyAura()
	local KICK_RADIUS = 25
	local RETURN_OFFSET = Vector3.new(0, 10, 0)

	crazyAuraConnection = RunService.RenderStepped:Connect(function()
		local myCharacter = localPlayer.Character
		if not myCharacter then return end
		local myHRP = myCharacter:FindFirstChild("HumanoidRootPart")
		if not myHRP then return end

		for _, player in ipairs(Players:GetPlayers()) do
			if player ~= localPlayer and player.Character then
				local targetHRP = player.Character:FindFirstChild("HumanoidRootPart")
				if targetHRP and (targetHRP.Position - myHRP.Position).Magnitude <= KICK_RADIUS then
					setNetwork(targetHRP)

					local randomPos = Vector3.new(
						math.random(-5000, 5000),
						math.random(500, 1500),
						math.random(-5000, 5000)
					)

					targetHRP.CFrame = CFrame.new(randomPos)
					task.wait(0.05)
					targetHRP.CFrame = myHRP.CFrame + RETURN_OFFSET
				end
			end
		end
	end)
end

 function stopCrazyAura()
	if crazyAuraConnection then
		crazyAuraConnection:Disconnect()
		crazyAuraConnection = nil
	end
end

AuraTab:CreateToggle({
	Name = "Crazy Aura",
	CurrentValue = false,
	Callback = function(value)
		if value then
			startCrazyAura()
		else
			stopCrazyAura()
		end
	end,
})

Players = game:GetService("Players")
Workspace = game:GetService("Workspace")
ReplicatedStorage = game:GetService("ReplicatedStorage")
LocalPlayer = Players.LocalPlayer
TrackedParts = {}
recentParts = {}
 auraRunning = false
scanTask = nil

 CONFIG = {
    AURA_RADIUS = 30,
    SCAN_DELAY = 0.1,
    PER_PART_THROTTLE = 0.2
}

 function StopFloat(part)
    local bv = part:FindFirstChildOfClass("BodyVelocity")
    if bv then bv:Destroy() end

    local bav = part:FindFirstChildOfClass("BodyAngularVelocity")
    if bav then bav:Destroy() end
end

 function isPlayerPart(part)
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr.Character and part:IsDescendantOf(plr.Character) then
            return true
        end
    end
    return false
end

 function isOwned(part)
    local owner = part:FindFirstChild("PartOwner")
    return owner and owner.Value == LocalPlayer.Name
end

 function setModelCollision(model, state)
    for _, d in ipairs(model:GetDescendants()) do
        if d:IsA("BasePart") then
            d.CanCollide = state
        end
    end
end

 function claimPartOwner(part)
    if SetNetworkOwner then
        pcall(function()
            SetNetworkOwner:FireServer(part, part.CFrame)
        end)
    end

    local model = part:FindFirstAncestorWhichIsA("Model")
    if model then
        setModelCollision(model, false)
    else
        part.CanCollide = false
    end

    if not TrackedParts[part] then
        local bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(1e5, 0, 1e5)
        bv.Velocity = Vector3.zero
        bv.Parent = part

        TrackedParts[part] = bv

        part.AncestryChanged:Connect(function(_, parent)
            if not parent then
                StopFloat(part)
                TrackedParts[part] = nil
            end
        end)
    end
end

function cleanup()
    for part in pairs(TrackedParts) do
        local model = part:FindFirstAncestorWhichIsA("Model")
        if model then
            setModelCollision(model, true)
        else
            part.CanCollide = true
        end
        StopFloat(part)
    end
    table.clear(TrackedParts)
    table.clear(recentParts)
end

 function startScanner()
    if scanTask then return end

    scanTask = task.spawn(function()
        while auraRunning do
            local char = LocalPlayer.Character
            if char and char.PrimaryPart then
                local parts = Workspace:GetPartBoundsInRadius(
                    char.PrimaryPart.Position,
                    CONFIG.AURA_RADIUS
                )

                local now = tick()

                for _, part in ipairs(parts) do
                    if part:IsA("BasePart") and not part.Anchored and not isPlayerPart(part) then
                        local model = part:FindFirstAncestorWhichIsA("Model")
                        if model then
                            local ownedAlready = false
                            for _, d in ipairs(model:GetDescendants()) do
                                if d:IsA("BasePart") and isOwned(d) then
                                    ownedAlready = true
                                    break
                                end
                            end

                            if not ownedAlready then
                                local last = recentParts[part]
                                if not last or (now - last > CONFIG.PER_PART_THROTTLE) then
                                    recentParts[part] = now
                                    claimPartOwner(part)
                                end
                            end
                        end
                    end
                end
            end

            task.wait(CONFIG.SCAN_DELAY)
        end
    end)
end

 function stopScanner()
    auraRunning = false
    scanTask = nil
end

AuraTab:CreateDivider()

AuraTab:CreateToggle({
    Name = "Delete Toys Aura",
    CurrentValue = false,
    Flag = "AuraToggle",
    Callback = function(enabled)
        if enabled then
            auraRunning = true
            startScanner()
        else
            stopScanner()
            cleanup()
        end
    end
})

ToyTab = Window:CreateTab("Toys", 4483362458)

ToyTab:CreateSection(" ")

TrackedParts = {}
FloatLoops = {}
GravityConnection = nil

CONFIG = { AURA_RADIUS = 30, SCAN_DELAY = 0.1, PER_PART_THROTTLE = 0.2 }
recentParts = {}
ownedParts = {}
auraRunning = false
scanTask = nil

function StopFloat(part)
    if FloatLoops[part] then pcall(function() FloatLoops[part]:Disconnect() end) FloatLoops[part] = nil end
    bv = part:FindFirstChildOfClass("BodyVelocity")
    if bv then bv:Destroy() end
    bav = part:FindFirstChildOfClass("BodyAngularVelocity")
    if bav then bav:Destroy() end
end

function isPlayerPart(part)
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr.Character and part:IsDescendantOf(plr.Character) then return true end
    end
    return false
end

function isOwned(part)
    owner = part:FindFirstChild("PartOwner")
    return owner and owner.Value == LocalPlayer.Name
end

function claimPartOwner(part)
    if SetNetworkOwner then pcall(function() SetNetworkOwner:FireServer(part, part.CFrame) end) end
    if not TrackedParts[part] then
        bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(1e5, 0, 1e5)
        bv.Velocity = Vector3.zero
        TrackedParts[part] = {bv = bv, bav = bav}
        part.AncestryChanged:Connect(function(_, parent)
            if not parent then StopFloat(part) TrackedParts[part] = nil end
        end)
    end
end

function cleanupOwnedParts()
    for part in pairs(ownedParts) do StopFloat(part) end
    table.clear(ownedParts)
    table.clear(recentParts)
end

function startScanner()
    if scanTask then return end
    scanTask = task.spawn(function()
        while auraRunning do
            char = LocalPlayer.Character
            if char and char.PrimaryPart then
                rootPos = char.PrimaryPart.Position
                parts = Workspace:GetPartBoundsInRadius(rootPos, CONFIG.AURA_RADIUS)
                now = tick()
                for _, part in ipairs(parts) do
                    if part:IsA("BasePart") and not part.Anchored and not isPlayerPart(part) then
                        model = part:FindFirstAncestorWhichIsA("Model")
                        if model then
                            ownedAlready = false
                            for _, p in ipairs(model:GetDescendants()) do
                                if p:IsA("BasePart") and isOwned(p) then ownedAlready = true break end
                            end
                            if not ownedAlready then
                                last = recentParts[part]
                                if (not last) or (now - last > CONFIG.PER_PART_THROTTLE) then
                                    recentParts[part] = now
                                    claimPartOwner(part)
                                end
                            end
                            for _, p in ipairs(model:GetDescendants()) do
                                if p:IsA("BasePart") and isOwned(p) then ownedParts[p] = true end
                            end
                        end
                    end
                end
            end
            task.wait(CONFIG.SCAN_DELAY)
        end
    end)
end

function stopScanner()
    auraRunning = false
    scanTask = nil
end

ToyTab:CreateToggle({
    Name = "Aura (Disable when using something!)",
    CurrentValue = false,
    Flag = "AuraToggle",
    Callback = function(enabled)
        if enabled then
            auraRunning = true
            startScanner()
            Rayfield:Notify({ Title = "Aura Grab", Content = "Started", Duration = 2 })
        else
            stopScanner()
            cleanupOwnedParts()
            Rayfield:Notify({ Title = "Aura Grab", Content = "Stopped", Duration = 2 })
        end
    end
})

HollowPurpleSettings = {
    OrbDistance = 80,
    OrbOffset = 80,
    Height = 5,
    PosX = 0,
    PosY = 0,
    PosZ = 0
}

ToyTab:CreateSection("Hollow Purple Settings")

ToyTab:CreateSlider({
    Name = "Orb Distance",
    Range = {10, 200},
    Increment = 5,
    CurrentValue = 80,
    Flag = "HollowPurpleDistance",
    Callback = function(Value)
        HollowPurpleSettings.OrbDistance = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Orb Offset",
    Range = {10, 200},
    Increment = 5,
    CurrentValue = 80,
    Flag = "HollowPurpleOffset",
    Callback = function(Value)
        HollowPurpleSettings.OrbOffset = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Height",
    Range = {0, 50},
    Increment = 1,
    CurrentValue = 5,
    Flag = "HollowPurpleHeight",
    Callback = function(Value)
        HollowPurpleSettings.Height = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position X",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "HollowPurplePosX",
    Callback = function(Value)
        HollowPurpleSettings.PosX = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Y",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "HollowPurplePosY",
    Callback = function(Value)
        HollowPurpleSettings.PosY = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Z",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "HollowPurplePosZ",
    Callback = function(Value)
        HollowPurpleSettings.PosZ = Value
    end,
})

ToyTab:CreateButton({
    Name = "Hollow Purple...",
    Callback = function()
        char = LocalPlayer.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then return end
        root = char.HumanoidRootPart
        forward = root.CFrame.LookVector
        right = root.CFrame.RightVector
        basePos = Vector3.new(HollowPurpleSettings.PosX, HollowPurpleSettings.PosY, HollowPurpleSettings.PosZ)
        leftOrbPos = basePos + root.Position + forward * HollowPurpleSettings.OrbDistance - right * HollowPurpleSettings.OrbOffset + Vector3.new(0, HollowPurpleSettings.Height, 0)
        rightOrbPos = basePos + root.Position + forward * HollowPurpleSettings.OrbDistance + right * HollowPurpleSettings.OrbOffset + Vector3.new(0, HollowPurpleSettings.Height, 0)
        centerOrbPos = basePos + root.Position + forward * HollowPurpleSettings.OrbDistance + Vector3.new(0, HollowPurpleSettings.Height, 0)
        GatheredParts = {}
        partsList = {}
        for part, _ in pairs(TrackedParts) do
            if part and part:IsDescendantOf(workspace) then StopFloat(part) table.insert(partsList, part) end
        end
        for i, part in ipairs(partsList) do
            orbTarget = (i % 2 == 0) and rightOrbPos or leftOrbPos
            bp = Instance.new("BodyPosition")
            bp.MaxForce = Vector3.new(1e6, 1e6, 1e6)
            bp.D = 1000
            bp.P = 20000
            bp.Position = orbTarget + Vector3.new(math.random(-3, 3), math.random(-2, 2), math.random(-3, 3))
            bp.Parent = part
            GatheredParts[part] = bp
        end
        task.delay(3, function()
            for part, bp in pairs(GatheredParts) do
                bp.Position = centerOrbPos + Vector3.new(math.random(-2, 2), math.random(-2, 2), math.random(-2, 2))
            end
            task.delay(2, function()
                for part, bp in pairs(GatheredParts) do
                    if bp and bp.Parent then bp:Destroy() end
                    blast = Instance.new("BodyVelocity")
                    blast.MaxForce = Vector3.new(1e6, 1e6, 1e6)
                    blast.Velocity = Vector3.new(math.random(-500, 500), math.random(-300, 300), math.random(-500, 500))
                    blast.Parent = part
                    Debris:AddItem(blast, 1)
                end
                table.clear(GatheredParts)
                table.clear(TrackedParts)
            end)
        end)
    end
})

SwastikaSettings = {
    Height = 250,
    Scale = 15,
    PosX = 0,
    PosY = 250,
    PosZ = 0
}

ToyTab:CreateSection("Swastika Settings")

ToyTab:CreateSlider({
    Name = "Scale",
    Range = {5, 50},
    Increment = 1,
    CurrentValue = 15,
    Flag = "SwastikaScale",
    Callback = function(Value)
        SwastikaSettings.Scale = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position X",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "SwastikaPosX",
    Callback = function(Value)
        SwastikaSettings.PosX = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Y",
    Range = {0, 500},
    Increment = 10,
    CurrentValue = 250,
    Flag = "SwastikaPosY",
    Callback = function(Value)
        SwastikaSettings.PosY = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Z",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "SwastikaPosZ",
    Callback = function(Value)
        SwastikaSettings.PosZ = Value
    end,
})

ToyTab:CreateButton({
    Name = "Swastika",
    Callback = function()
        basePos = Vector3.new(SwastikaSettings.PosX, SwastikaSettings.PosY, SwastikaSettings.PosZ)
        scale = SwastikaSettings.Scale
        CUSTOM_9x9 = {
            "100011111",
            "100010000",
            "100010000",
            "100010000",
            "111111111",
            "000010001",
            "000010001",
            "000010001",
            "111110001",
        }
        partsList = {}
        for part, _ in pairs(TrackedParts) do
            if part and part:IsDescendantOf(workspace) then StopFloat(part) table.insert(partsList, part) end
        end
        targetPositions = {}
        height = #CUSTOM_9x9
        width = #CUSTOM_9x9[1]
        for row = 1, height do
            line = CUSTOM_9x9[row]
            for col = 1, width do
                if line:sub(col, col) == "1" then
                    offset = Vector3.new((col - 1 - width / 2) * scale, 0, -(row - 1 - height / 2) * scale)
                    table.insert(targetPositions, basePos + offset)
                end
            end
        end
        if #partsList < #targetPositions then
            Rayfield:Notify({ Title = "Not Enough Pallets", Content = "You need at least " .. tostring(#targetPositions) .. " pallets for this symbol!", Duration = 4 })
        end
        for i, part in ipairs(partsList) do
            target = targetPositions[(i - 1) % #targetPositions + 1]
            bp = Instance.new("BodyPosition")
            bp.MaxForce = Vector3.new(1e9, 1e9, 1e9)
            bp.D = 1000
            bp.P = 50000
            bp.Position = target
            bp.Parent = part
        end
    end
})

TeleportSettings = {
    PosX = 0,
    PosY = 0,
    PosZ = 0
}

ToyTab:CreateSection("Teleport Settings")

ToyTab:CreateSlider({
    Name = "Position X",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "TeleportPosX",
    Callback = function(Value)
        TeleportSettings.PosX = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Y",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "TeleportPosY",
    Callback = function(Value)
        TeleportSettings.PosY = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Z",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "TeleportPosZ",
    Callback = function(Value)
        TeleportSettings.PosZ = Value
    end,
})

ToyTab:CreateButton({
    Name = "Teleport Parts To Point",
    Callback = function()
        targetPosition = Vector3.new(TeleportSettings.PosX, TeleportSettings.PosY, TeleportSettings.PosZ)
        partsList = {}
        for part, _ in pairs(TrackedParts) do
            if part and part:IsDescendantOf(workspace) then StopFloat(part) table.insert(partsList, part) end
        end
        if #partsList == 0 then return end
        for _, part in ipairs(partsList) do
            pcall(function() part.CFrame = CFrame.new(targetPosition) end)
        end
    end
})

OrbitSettings = {
    Radius = 50,
    Speed = 100,
    Height = 3
}

ToyTab:CreateSection("Orbit Settings")

ToyTab:CreateSlider({
    Name = "Radius",
    Range = {10, 200},
    Increment = 5,
    CurrentValue = 50,
    Flag = "OrbitRadius",
    Callback = function(Value)
        OrbitSettings.Radius = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Speed",
    Range = {10, 500},
    Increment = 10,
    CurrentValue = 100,
    Flag = "OrbitSpeed",
    Callback = function(Value)
        OrbitSettings.Speed = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Height Variation",
    Range = {0, 20},
    Increment = 1,
    CurrentValue = 3,
    Flag = "OrbitHeight",
    Callback = function(Value)
        OrbitSettings.Height = Value
    end,
})

ORBIT_RADIUS = 50
ORBIT_SPEED = math.rad(100)
orbitingParts = {}
orbitConnection = nil
orbitEnabled = false

function startOrbit()
    char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    root = char.HumanoidRootPart
    if orbitConnection then orbitConnection:Disconnect() end
    orbitingParts = {}
    for part, _ in pairs(TrackedParts) do
        if part and part:IsDescendantOf(workspace) then orbitingParts[part] = {angle = math.random() * 2 * math.pi} end
    end
    orbitConnection = RunService.Heartbeat:Connect(function(dt)
        time = tick()
        index = 0
        for part, data in pairs(orbitingParts) do
            if part and part.Parent then
                data.angle = data.angle + math.rad(OrbitSettings.Speed) * dt
                x = math.cos(data.angle) * OrbitSettings.Radius
                z = math.sin(data.angle) * OrbitSettings.Radius
                y = math.sin(time + index) * OrbitSettings.Height
                targetPos = root.Position + Vector3.new(x, y, z)
                if part:FindFirstChild("BodyPosition") then
                    part.BodyPosition.Position = targetPos
                else
                    bp = Instance.new("BodyPosition")
                    bp.MaxForce = Vector3.new(1e6, 1e6, 1e6)
                    bp.P = 5000
                    bp.D = 100
                    bp.Position = targetPos
                    bp.Parent = part
                end
                index = index + 1
            end
        end
    end)
end

function stopOrbit()
    if orbitConnection then orbitConnection:Disconnect() orbitConnection = nil end
    for part in pairs(orbitingParts) do
        if part and part:FindFirstChild("BodyPosition") then part.BodyPosition:Destroy() end
    end
    orbitingParts = {}
end

ToyTab:CreateToggle({
    Name = "Orbit",
    CurrentValue = false,
    Flag = "OrbitMeToggle",
    Callback = function(enabled)
        orbitEnabled = enabled
        if enabled then startOrbit() else stopOrbit() end
    end
})

TornadoSettings = {
    Height = 200,
    Radius = 100,
    Speed = 200,
    PosX = 0,
    PosY = 0,
    PosZ = 0
}

ToyTab:CreateSection("Tornado Settings")

ToyTab:CreateSlider({
    Name = "Height",
    Range = {50, 500},
    Increment = 10,
    CurrentValue = 200,
    Flag = "TornadoHeight",
    Callback = function(Value)
        TornadoSettings.Height = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Radius",
    Range = {20, 300},
    Increment = 10,
    CurrentValue = 100,
    Flag = "TornadoRadius",
    Callback = function(Value)
        TornadoSettings.Radius = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Speed",
    Range = {50, 500},
    Increment = 10,
    CurrentValue = 200,
    Flag = "TornadoSpeed",
    Callback = function(Value)
        TornadoSettings.Speed = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position X",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "TornadoPosX",
    Callback = function(Value)
        TornadoSettings.PosX = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Y",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "TornadoPosY",
    Callback = function(Value)
        TornadoSettings.PosY = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Z",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "TornadoPosZ",
    Callback = function(Value)
        TornadoSettings.PosZ = Value
    end,
})

TORNADO_HEIGHT = 200
TORNADO_BASE_RADIUS = 100
TORNADO_SPIN_SPEED = math.rad(200)
tornadoParts = {}
tornadoConnection = nil

function startTornado()
    center = Vector3.new(TornadoSettings.PosX, TornadoSettings.PosY, TornadoSettings.PosZ)
    if tornadoConnection then tornadoConnection:Disconnect() end
    tornadoParts = {}
    for part, _ in pairs(TrackedParts) do
        if part and part:IsDescendantOf(workspace) then tornadoParts[part] = {angle = math.random() * 2 * math.pi, heightOffset = math.random() * TornadoSettings.Height} end
    end
    tornadoConnection = RunService.Heartbeat:Connect(function(dt)
        for part, data in pairs(tornadoParts) do
            if part and part.Parent then
                data.angle = data.angle + math.rad(TornadoSettings.Speed) * dt
                t = data.heightOffset % TornadoSettings.Height
                radius = (t / TornadoSettings.Height) * TornadoSettings.Radius
                x = math.cos(data.angle) * radius
                z = math.sin(data.angle) * radius
                y = t
                targetPos = center + Vector3.new(x, y, z)
                if part:FindFirstChild("BodyPosition") then
                    part.BodyPosition.Position = targetPos
                else
                    bp = Instance.new("BodyPosition")
                    bp.MaxForce = Vector3.new(1e6, 1e6, 1e6)
                    bp.P = 5000
                    bp.D = 100
                    bp.Position = targetPos
                    bp.Parent = part
                end
                data.heightOffset = data.heightOffset + 50 * dt
            end
        end
    end)
end

function stopTornado()
    if tornadoConnection then tornadoConnection:Disconnect() tornadoConnection = nil end
    for part in pairs(tornadoParts) do
        if part and part:FindFirstChild("BodyPosition") then part.BodyPosition:Destroy() end
    end
    tornadoParts = {}
end

ToyTab:CreateToggle({
    Name = "Tornado",
    CurrentValue = false,
    Flag = "TornadoToggle",
    Callback = function(enabled)
        if enabled then startTornado() else stopTornado() end
    end
})


--4392 line i gota fix

GrabTab = Window:CreateTab("Grab and lines", 7734058599)


GrabTab:CreateSection("Locks")

Players = game:GetService("Players")
 UserInputService = game:GetService("UserInputService")
 RunService = game:GetService("RunService")
 LocalPlayer = Players.LocalPlayer
 Workspace = game:GetService("Workspace")
Camera = Workspace.CurrentCamera

 lockConnections = {}
holdingQ = false
 targetPart = nil

function findTargetPart()
    local playerFolder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if playerFolder then
        for _, ball in ipairs(playerFolder:GetChildren()) do
            if ball.Name == "BallBasketball" then
                local ball80 = ball:FindFirstChild("Ball80")
                if ball80 and ball80:FindFirstChild("WeldConstraint") then
                    return ball80
                end
            end
        end
    end
    return nil
end

aimbotConnections = {}

GrabTab:CreateToggle({
    Name = "AimLock normal",
    Default = false,
    Callback = function(Value)
        if Value then
            local Players = game:GetService("Players")
            local UserInputService = game:GetService("UserInputService")
            local RunService = game:GetService("RunService")
            local localPlayer = Players.LocalPlayer
            local camera = workspace.CurrentCamera
            local holdingQ = false

            local function getNearestPlayer()
                local closest, closestDist = nil, math.huge
                local myPos = camera.CFrame.Position
                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= localPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                        local distance = (myPos - player.Character.HumanoidRootPart.Position).Magnitude
                        if distance < closestDist then
                            closest, closestDist = player, distance
                        end
                    end
                end
                return closest
            end

            table.insert(aimbotConnections, UserInputService.InputBegan:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then holdingQ = true end
            end))
            table.insert(aimbotConnections, UserInputService.InputEnded:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then holdingQ = false end
            end))
            table.insert(aimbotConnections, RunService.RenderStepped:Connect(function()
                if holdingQ then
                    local target = getNearestPlayer()
                    if target and target.Character then
                        camera.CFrame = CFrame.new(camera.CFrame.Position, target.Character.HumanoidRootPart.Position)
                    end
                end
            end))
        else
            for _, conn in pairs(aimbotConnections) do conn:Disconnect() end
            aimbotConnections = {}
        end
    end
})

aimbotConnections = {}

GrabTab:CreateToggle({
	Name = "AimLock Clone",
	Default = false,
	Callback = function(Value)
		if Value then
			local Players = game:GetService("Players")
			local UserInputService = game:GetService("UserInputService")
			local RunService = game:GetService("RunService")
			local LocalPlayer = Players.LocalPlayer
			local Camera = workspace.CurrentCamera
			local holdingQ = false


			local function getYouDecoy()
				local toysFolder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
				if toysFolder then
					local decoy = toysFolder:FindFirstChild("YouDecoy")
					if decoy and decoy:FindFirstChild("HumanoidRootPart") and decoy:FindFirstChildOfClass("Humanoid") then
						return decoy
					end
				end
				return nil
			end

			table.insert(aimbotConnections, UserInputService.InputBegan:Connect(function(input)
				if input.KeyCode == Enum.KeyCode.Q then
					holdingQ = true
				end
			end))

			table.insert(aimbotConnections, UserInputService.InputEnded:Connect(function(input)
				if input.KeyCode == Enum.KeyCode.Q then
					holdingQ = false
				end
			end))

			table.insert(aimbotConnections, RunService.RenderStepped:Connect(function()
				if holdingQ then
					local decoy = getYouDecoy()
					if decoy then
						local hrp = decoy:FindFirstChild("HumanoidRootPart")
						if hrp then
							Camera.CFrame = CFrame.new(Camera.CFrame.Position, hrp.Position)
						end
					end
				end
			end))
		else
			for _, conn in pairs(aimbotConnections) do
				conn:Disconnect()
			end
			aimbotConnections = {}
		end
	end
})

aimbotConnections = {}
 lastDecoy = nil


 function getNearestDecoy()
    local localPlayer = game.Players.LocalPlayer
    local toysFolder = workspace:FindFirstChild(localPlayer.Name.."SpawnedInToys")
    if not toysFolder then return nil end

    local closest, closestDist = nil, math.huge
    local myPos = workspace.CurrentCamera.CFrame.Position

    for _, obj in ipairs(toysFolder:GetChildren()) do
        if obj.Name == "YouLittle" and obj:FindFirstChild("HumanoidRootPart") then
            local dist = (myPos - obj.HumanoidRootPart.Position).Magnitude
            if dist < closestDist then
                closest = obj
                closestDist = dist
            end
        end
    end

    return closest
end

GrabTab:CreateToggle({
    Name = "AimLock baby",
    Default = false,
    Callback = function(Value)
        if Value then
            local UserInputService = game:GetService("UserInputService")
            local RunService = game:GetService("RunService")
            local camera = workspace.CurrentCamera
            local holdingQ = false

            table.insert(aimbotConnections, UserInputService.InputBegan:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then holdingQ = true end
            end))

            table.insert(aimbotConnections, UserInputService.InputEnded:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then holdingQ = false end
            end))

            table.insert(aimbotConnections, RunService.RenderStepped:Connect(function()
                if holdingQ then
                    local decoy = getNearestDecoy()
                    if decoy and decoy:FindFirstChild("HumanoidRootPart") then
                        camera.CFrame = CFrame.new(camera.CFrame.Position, decoy.HumanoidRootPart.Position)
                    end
                end
            end))

        else
            for _, conn in pairs(aimbotConnections) do
                conn:Disconnect()
            end
            aimbotConnections = {}
        end
    end
})

Players = game:GetService("Players")
 UserInputService = game:GetService("UserInputService")
 RunService = game:GetService("RunService")
 localPlayer = Players.LocalPlayer
 camera = workspace.CurrentCamera

 aimbotConnections = {}
 targetedPlayers = {}
 holdingQ = false

function getPlayerOptions()
    local options = {}
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= localPlayer then
            table.insert(options, player.DisplayName .. " [" .. player.Name .. "]")
        end
    end
    return options
end

function extractRealName(displayString)
    return displayString:match("%[(.-)%]")
end

function getTargetedPlayer()
    local closest, closestDist = nil, math.huge
    local myPos = camera.CFrame.Position
    for _, targetName in ipairs(targetedPlayers) do
        local player = Players:FindFirstChild(targetName)
        if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local distance = (myPos - player.Character.HumanoidRootPart.Position).Magnitude
            if distance < closestDist then
                closest, closestDist = player, distance
            end
        end
    end
    return closest
end



 PlayerDropdown = GrabTab:CreateDropdown({
    Name = "Target Players",
    Options = getPlayerOptions(),
    CurrentOption = {},
    MultipleOptions = true,
    Flag = "TargetPlayersDropdown",
    Callback = function(Option)
        targetedPlayers = {}
        for _, displayName in ipairs(Option) do
            local realName = extractRealName(displayName)
            if realName then
                table.insert(targetedPlayers, realName)
            end
        end
        print("Targeted players:", table.concat(targetedPlayers, ", "))
    end
})

GrabTab:CreateButton({
    Name = "Refresh Player List",
    Callback = function()
        local newOptions = getPlayerOptions()
        PlayerDropdown:Refresh(newOptions)
        print("Player list refreshed!")
    end
})

GrabTab:CreateToggle({
    Name = "AimLock using list",
    Default = false,
    Callback = function(Value)
        if Value then
            if #targetedPlayers == 0 then
                print("No players targeted! Select players first.")
                return
            end

            table.insert(aimbotConnections, UserInputService.InputBegan:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then 
                    holdingQ = true 
                end
            end))

            table.insert(aimbotConnections, UserInputService.InputEnded:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then 
                    holdingQ = false 
                end
            end))

            table.insert(aimbotConnections, RunService.RenderStepped:Connect(function()
                if holdingQ then
                    local target = getTargetedPlayer()
                    if target and target.Character then
                        camera.CFrame = CFrame.new(camera.CFrame.Position, target.Character.HumanoidRootPart.Position)
                    end
                end
            end))

            print("Anti lag whitelist activated for", #targetedPlayers, "player(s)")
        else
            for _, conn in ipairs(aimbotConnections) do 
                if conn then conn:Disconnect() end
            end
            aimbotConnections = {}
            holdingQ = false
            print("Anti lag disabled")
        end
    end
})

GrabTab:CreateButton({
    Name = "Show Current Targets",
    Callback = function()
        if #targetedPlayers == 0 then
            print("No players currently targeted")
        else
            print("Current targets:", table.concat(targetedPlayers, ", "))
            playClickSound()
        end
    end
})

Players.PlayerAdded:Connect(function()
    task.wait(1)
    local newOptions = getPlayerOptions()
    PlayerDropdown:Refresh(newOptions)
end)

Players.PlayerRemoving:Connect(function()
    task.wait(1)
    local newOptions = getPlayerOptions()
    PlayerDropdown:Refresh(newOptions)
end)

GrabTab:CreateSection("Lines")

GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
 SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
 DestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")

vu1 = game:GetService("Players")
 vu3 = game:GetService("Debris")
 vu4 = game:GetService("Workspace")
 v5 = game:GetService("Lighting")
 vu6 = game:GetService("TweenService")
 vu7 = game:GetService("UserInputService")
 vu8 = game:GetService("ReplicatedStorage")
vu10 = game:GetService("ContextActionService")
 vu11 = game:GetService("RunService")
 vu14 = vu1.LocalPlayer

 vu27 = vu8:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
 vu26 = vu8:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")

_G.ControllingCreature = nil
 vu132 = nil
 vu133 = nil

CharacterRaycastFilter = RaycastParams.new()
CharacterRaycastFilter.FilterDescendantsInstances = {vu14.Character}
CharacterRaycastFilter.FilterType = Enum.RaycastFilterType.Exclude

function lookAt(p114, p115)
    local v116 = (p115 - p114).Unit
    local v117 = v116:Cross((Vector3.new(0, 1, 0)))
    local v118 = v117:Cross(v116)
    return CFrame.fromMatrix(p114, v117, v118)
end

function GetPlayerCharacter()
    if vu14.Character and (vu14.Character:FindFirstChild("HumanoidRootPart") and vu14.Character:FindFirstChildOfClass("Humanoid")) then
        return vu14.Character
    end
end

function CheckNetworkOwnerShipOnPart(p152, p153)
    if typeof(p152) == "Instance" and (p152:FindFirstChild("PartOwner") and p152.PartOwner.Value == vu14.Name) then
        return not p153 and true or p152.PartOwner
    end
end

function SNOWshipOnce(p159)
    local v160 = vu14:DistanceFromCharacter(p159.Position)
    if vu14.Character and vu14.Character:FindFirstChild("HumanoidRootPart") then
        if CheckNetworkOwnerShipOnPart(p159) then
            return true
        end
        if v160 <= 30 then
            vu27:FireServer(p159, lookAt(vu14.Character.HumanoidRootPart.Position, p159.Position))
        end
    end
end

function SNOWshipOnceAndDelete(pu172)
    local v173 = vu14:DistanceFromCharacter(pu172.Position)
    local v174 = pu172:GetAttribute("Connected")
    local v175 = pu172:GetAttribute("CreatedConnected")
    if vu14.Character and vu14.Character:FindFirstChild("HumanoidRootPart") then
        if CheckNetworkOwnerShipOnPart(pu172) then
            pu172:SetAttribute("Connected", true)
            vu26:FireServer(pu172)
            if not v175 then
                pu172:SetAttribute("CreatedConnected", true)
                pu172.ChildAdded:Connect(function(p176)
                    if p176.Name == "PartOwner" and p176.Value ~= vu14.Name then
                        pu172:SetAttribute("Connected", false)
                    end
                end)
            end
        elseif v173 <= 30 and not v174 then
            vu27:FireServer(pu172, lookAt(vu14.Character.HumanoidRootPart.Position, pu172.Position))
        end
    end
end

function vu139()
    if not vu132 then
        vu133 = false
        local function v138()
            if vu133 == false and game.Players.LocalPlayer.Character ~= nil then
                local v134, v135, v136 = pairs(game.Players.LocalPlayer.Character:GetChildren())
                while true do
                    local v137
                    v136, v137 = v134(v135, v136)
                    if v136 == nil then
                        break
                    end
                    if v137:IsA("BasePart") and v137.CanCollide then
                        v137.CanCollide = false
                    end
                end
            end
            wait(0.15)
        end
        vu132 = vu11.Stepped:Connect(v138)
    end
end

 function vu140()
    if not _G.NoclipToggle then
        if vu132 then
            vu132:Disconnect()
            vu132 = nil
        end
        vu133 = true
    end
end

function makeCharacterNotGrabbable(p401)
    local v402, v403, v404 = pairs(p401:GetChildren())
    while true do
        local v405
        v404, v405 = v402(v403, v404)
        if v404 == nil then
            break
        end
        if v405:IsA("Part") then
            v405.CanQuery = false
        end
    end
end

function makeCharacterGrabbable(p406)
    local v407, v408, v409 = pairs(p406:GetChildren())
    while true do
        local v410
        v409, v410 = v407(v408, v409)
        if v409 == nil then
            break
        end
        if v410:IsA("Part") then
            v410.CanQuery = true
        end
    end
end

controlsoundeffect = Instance.new("Sound", vu4)
controlsoundeffect.SoundId = "rbxassetid://9114374439"
controlsoundeffect.PlaybackSpeed = 1

controleffectsatur = Instance.new("ColorCorrectionEffect", v5)
controleffectsatur.Enabled = false

controltween1 = vu6:Create(vu4.CurrentCamera, TweenInfo.new(0.3, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, 0, true), {FieldOfView = 120})
controltween2 = vu6:Create(controleffectsatur, TweenInfo.new(0.3, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {TintColor = Color3.fromRGB(210, 200, 240)})
controltween3 = vu6:Create(controleffectsatur, TweenInfo.new(1, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -0.8, true), {Brightness = -0.07})
controltween4 = vu6:Create(controleffectsatur, TweenInfo.new(0.3, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {TintColor = Color3.new(3, 3, 3), Brightness = 0})

function controlcreatureeffectIn()
    controleffectsatur.Enabled = true
    controleffectsatur.TintColor = Color3.new()
    controltween1:Play()
    controltween2:Play()
    controlsoundeffect:Play()
    controltween2.Completed:Once(function()
        controltween3:Play()
    end)
end

function controlcreatureeffectOut()
    controltween4:Play()
    controltween4.Completed:Once(function()
        controleffectsatur.Enabled = false
    end)
end

function TeleportPlayer(p186, p187)
    local v188 = GetPlayerCharacter()
    if v188 and typeof(p186) == "CFrame" then
        local v189 = v188.HumanoidRootPart
        local v190 = v188:FindFirstChildOfClass("Humanoid")
        v189.CFrame = v189.CFrame.Rotation + p186.Position
        if v190.SeatPart == nil or tostring(v190.SeatPart.Parent) ~= "CreatureBlobman" then
            v190.Sit = false
        end
    end
end

function controlCreature(pu411)
    if typeof(pu411) == "Instance" and pu411:IsA("Model") then
        local vu412 = pu411
        local vu413 = vu412:FindFirstChildOfClass("Humanoid")
        local v414 = vu412:FindFirstChild("HumanoidRootPart")
        local vu415 = vu412:FindFirstChild("Head")
        local vu416 = (function()
            if not vu1:GetPlayerFromCharacter(pu411) and (pu411.Name == "YouDecoy" or (pu411.Name == "CreatureBlobman" or tostring(pu411.Parent.Name) == "Robloxians")) then
                return true
            end
        end)()
        
        if vu412 and (vu413 and v414) then
            local vu417 = {}
            local function v422()
                local v418, v419, v420 = pairs(vu417)
                while true do
                    local v421
                    v420, v421 = v418(v419, v420)
                    if v420 == nil then
                        break
                    end
                    if typeof(v421) == "RBXScriptConnection" then
                        v421:Disconnect()
                    end
                end
                table.clear(vu417)
            end
            
            _G.ControllingCreature = vu412
            vu413.WalkSpeed = 0
            vu413.JumpPower = 24
            vu413.CameraOffset = Vector3.new(0, 0, -0.7)
            
            vu417[1] = vu413.Died:Connect(function()
                _G.ControllingCreature = nil
            end)
            
            local vu423 = Instance.new("BodyVelocity", v414)
            local v424 = Instance.new("BodyVelocity")
            v424.MaxForce = Vector3.new(0, math.huge, 0)
            v424.Velocity = Vector3.new()
            vu423.MaxForce = Vector3.new(math.huge, 0, math.huge)
            
            makeCharacterNotGrabbable(vu412)
            
            task.spawn(function()
                vu139()
                while vu412.Parent and _G.ControllingCreature ~= nil do
                    if vu416 then
                        SNOWshipOnceAndDelete(vu415)
                    else
                        SNOWshipOnce(vu415)
                    end
                    vu413.AutoRotate = true
                    task.wait()
                end
            end)
            
            vu4.CurrentCamera.CameraSubject = vu413
            controlcreatureeffectIn()
            
            local v425 = GetPlayerCharacter()
            local v426, v427
            if v425 then
                local vu428 = v425:FindFirstChildOfClass("Humanoid")
                v426 = v425:FindFirstChild("HumanoidRootPart")
                v424.Parent = v426
                
                vu417[2] = vu428.Died:Connect(function()
                    _G.ControllingCreature = nil
                end)
                
                vu417[3] = vu7.JumpRequest:Connect(function()
                    vu413:ChangeState("Jumping")
                end)
                
                vu417[5] = vu428.Changed:Connect(function(p429)
                    if p429 == "MoveDirection" then
                        vu423.Velocity = vu428.MoveDirection * 20
                    end
                end)
                
                vu417[6] = workspace.CurrentCamera.Changed:Connect(function(p430)
                    if p430 == "CameraSubject" then
                        vu4.CurrentCamera.CameraSubject = vu413
                    end
                end)
                
                local vu431 = nil
                vu417[7] = vu415.Changed:Connect(function(p432)
                    if p432 == "CFrame" then
                        vu431 = vu4.CurrentCamera.CFrame.lookVector
                        vu413.CameraOffset = -Vector3.new(vu431.X, 5, vu431.Z) * 1.7
                    end
                end)
                
                vu413:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
                v427 = vu428
            else
                v426 = nil
                v427 = nil
            end
            
            while vu412.Parent and (_G.ControllingCreature ~= nil and (v425 and v425.Parent)) do
                TeleportPlayer(CFrame.new(v414.Position + Vector3.new(0, -10, 0)))
                task.wait()
            end
            
            v422()
            vu140()
            TeleportPlayer(CFrame.new(v414.Position + Vector3.new(5, 15, 5)))
            makeCharacterGrabbable(vu412)
            vu423:Destroy()
            v424:Destroy()
            vu4.CurrentCamera.CameraSubject = v427
            _G.ControllingCreature = nil
            v426.Velocity = Vector3.new()
            controlcreatureeffectOut()
        end
    end
end

function controlBindF()
    local v433 = GetPlayerCharacter()
    if v433 then
        local v434 = v433.Head
        local v435 = vu4.CurrentCamera
        local v436 = v433:FindFirstChildOfClass("Humanoid")
        local v437 = vu4:Raycast(v434.Position, v435.CFrame.lookVector * 50, CharacterRaycastFilter)
        if v437 and (v436 and v436.Health > 0) then
            local v438 = v437.Instance.Parent
            if v438:FindFirstChildOfClass("Humanoid") then
                controlCreature(v438)
            end
        end
    end
end

function controlBind(p439, p440, _)
    if p439 == "Control(C)" and p440 == Enum.UserInputState.Begin then
        if _G.ControllingCreature then
            _G.ControllingCreature = nil
        else
            controlBindF()
        end
    end
end

vu14.CharacterAdded:Connect(function(char)
    CharacterRaycastFilter.FilterDescendantsInstances = {char}
end)


infLineExtendT = nil

function infLineExtendF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    uis.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseWheel then
            if lineDistanceV < 11 then
                lineDistanceV = 11
            end
    
            if input.Position.Z > 0 then
                lineDistanceV = lineDistanceV + increaseLineExtendV
            elseif input.Position.Z < 0 then
                lineDistanceV = lineDistanceV - increaseLineExtendV
            end
        end
    end)

workspace.ChildAdded:Connect(function(child)
        if child.Name == "GrabParts" and child:IsA("Model") then
            if infLineExtendT and uis.MouseEnabled then
                local grabPartsModel = child

                grabPartsModel:WaitForChild("GrabPart")
                grabPartsModel:WaitForChild("DragPart")
                    
                local clonedDragPart = grabPartsModel.DragPart:Clone()
                clonedDragPart.Name = "DragPart1"
                clonedDragPart.AlignPosition.Attachment1 = clonedDragPart.DragAttach
                clonedDragPart.Parent = grabPartsModel
                
                lineDistanceV = (clonedDragPart.Position - cam.CFrame.Position).Magnitude
    
                clonedDragPart.AlignOrientation.Enabled = false
                grabPartsModel.DragPart.AlignPosition.Enabled = false
    
                task.spawn(function()
                    while grabPartsModel.Parent do
                        clonedDragPart.Position = cam.CFrame.Position + cam.CFrame.LookVector * lineDistanceV
                        task.wait()
                    end
            
                    lineDistanceV = 0
                end)
            end
        end
    end)
end

 flingT = nil strengthV = 100 function flingF() local char = plr.Character local hrp = char:WaitForChild("HumanoidRootPart") local hum = char:WaitForChild("Humanoid") workspace.ChildAdded:Connect(function(model) if model.Name == "GrabParts" then local part_to_impulse = model["GrabPart"]["WeldConstraint"].Part1 if part_to_impulse then model:GetPropertyChangedSignal("Parent"):Connect(function() if not model.Parent and flingT then uis.InputBegan:Connect(function(inp, chat) if inp.UserInputType == Enum.UserInputType.MouseButton2 then local velocityObj = Instance.new("BodyVelocity", part_to_impulse) velocityObj.MaxForce = Vector3.new(math.huge, math.huge, math.huge) velocityObj.Velocity = cam.CFrame.lookVector * strengthV deb:AddItem(velocityObj, 1) end end) end end) end end end) end flingToggle = GrabTab:CreateToggle({ Name = "Stronger Fling", CurrentValue = false, Flag = "flingToggleFlag", Callback = function(Value) flingT = Value flingF() end }) flingToggle:Set(false) strengthInput = GrabTab:CreateInput({ Name = "Strength Value", CurrentValue = 100, PlaceholderText = "Strength", RemoveTextAfterFocusLost = false, Flag = "strengthInputFlag", Callback = function(Value) strengthV = Value end })

GrabTab:CreateSection("Lines")

extendLineToggle = GrabTab:CreateToggle({
    Name = "Extend Line Toggle",
    CurrentValue = false,
    Flag = "extendLineToggleFlag",
    Callback = function(Value)
        infLineExtendT = Value
        infLineExtendF()
    end
})

extendLineInput = GrabTab:CreateSlider({
    Name = "Extend Line",
    Range = {1, 20},
    Increment = 1,
    CurrentValue = 2,
    Flag = "extendLineInputFlag",
    Callback = function(Value)
        increaseLineExtendV = Value
    end
})

ReplicatedStorage = game:GetService("ReplicatedStorage")
 CreateGrabLine = ReplicatedStorage.GrabEvents.CreateGrabLine

_G.InvisibleLine = nil

GrabTab:CreateToggle({
	Name = "Invisible Line",
	Default = false,
	Callback = function(Value)
		if Value then
			_G.InvisibleLine = true
			task.spawn(function()
				while _G.InvisibleLine do
					CreateGrabLine:FireServer()
					task.wait()
				end
			end)
		else
			_G.InvisibleLine = nil
		end
	end    
})

GrabTab:CreateSection("Grab")
 canC = false

for _,plot in pairs(game.workspace.PlotItems:GetChildren()) do
    if plot.Name ~= "PlayersInPlots" then
        plot.DescendantAdded:Connect(function(prt)
            if prt:IsA("BasePart") then
                if canC then
                    prt.CollisionGroup = "Items"
                end
            end
        end)
    end
end

MS = GrabTab:CreateToggle({
    Name = "Plot Item Collision Grab",
    Default = true,
    Callback = function(Value)
        canC = Value
        if canC then
            for _,plot in pairs(game.workspace.PlotItems:GetChildren()) do
                if plot.Name ~= "PlayersInPlots" then
                    for _,prt in pairs(plot:GetDescendants()) do
                        if prt:IsA("BasePart") then
                            prt.CollisionGroup = "Items"
                        end
                    end
                end
            end
        else
            for _,plot in pairs(game.workspace.PlotItems:GetChildren()) do
                if plot.Name ~= "PlayersInPlots" then
                    for _,prt in pairs(plot:GetDescendants()) do
                        if prt:IsA("BasePart") then
                            prt.CollisionGroup = "PlotItems"
                        end
                    end
                end
            end
        end
    end,
})

GrabTab:CreateToggle({
    Name = "Control Characters (V)",
    Default = false,
    Callback = function(p1430)
        if p1430 then
            vu10:BindAction("Control(C)", controlBind, false, Enum.KeyCode.V)
        else
            vu10:UnbindAction("Control(C)")
        end
    end,
})

GrabTab:CreateButton({
    Name = "Fix Grab",
    Callback = function()
        game:GetService("ReplicatedFirst").GrabParts.DragPart.AlignPosition.Attachment0 = game:GetService("ReplicatedFirst").GrabParts.GrabPart.GrabAttach
        game:GetService("ReplicatedFirst").GrabParts.DragPart.AlignOrientation.Attachment0 = game:GetService("ReplicatedFirst").GrabParts.GrabPart.GrabAttach
    end
})

LoopTab = Window:CreateTab("Loop", 7734058599)

Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
Workspace = game:GetService("Workspace")
RunService = game:GetService("RunService")
LocalPlayer = Players.LocalPlayer

GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
RemoteSetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
RemoteDestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
SpawnToyRF = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")

SelectedPlayer = nil
KillHB, KickHB, GrabKickHB = nil, nil, nil
LoopKickOn, LoopKillOn, LoopBlobKickOn, LoopBlobKillOn, LoopGrabKickOn = false, false, false, false, false
spamActive = false

HEIGHT_LIMIT = 100000
TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)

function sno(part)
    if not part or not part.Parent then return end
    pcall(function()
        RemoteSetNetworkOwner:FireServer(part, part.CFrame)
    end)
end

function DisableCollisions(model)
    for _, d in ipairs(model:GetDescendants()) do
        if d:IsA("BasePart") then d.CanCollide = false end
    end
end

function setNoCollideChar(char)
    for _, v in ipairs(char:GetDescendants()) do
        if v:IsA("BasePart") then v.CanCollide = false end
    end
end

function isTooHigh(plr)
    local c = plr.Character
    local hrp = c and c:FindFirstChild("HumanoidRootPart")
    return not hrp or hrp.Position.Y > HEIGHT_LIMIT
end

function findBlobman()
    local toys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    return toys and toys:FindFirstChild("CreatureBlobman") or nil
end

function spawnBlobman()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    SpawnToyRF:InvokeServer("CreatureBlobman", char.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5), Vector3.new(0, -15, 0))
end

function ensureBlobman()
    local b = findBlobman()
    if b then return b end
    spawnBlobman()
    for _ = 1, 11 do
        task.wait(1)
        b = findBlobman()
        if b then return b end
    end
    return nil
end

CameraAnchor = {}
CameraAnchor.__index = CameraAnchor
function CameraAnchor.new() return setmetatable({}, CameraAnchor) end
function CameraAnchor:attach(cf)
    self:detach()
    local p = Instance.new("Part")
    p.Name, p.Size, p.Transparency, p.Anchored, p.CanCollide, p.CFrame, p.Parent =
        "CameraAnchor", Vector3.new(0.2, 0.2, 0.2), 1, true, false, cf, Workspace
    self.part = p
    local cam = Workspace.CurrentCamera
    cam.CameraType = Enum.CameraType.Custom
    cam.CameraSubject = p
end
function CameraAnchor:detach()
    if self.part then self.part:Destroy() self.part = nil end
    local cam = Workspace.CurrentCamera
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("Humanoid") then
        cam.CameraSubject = char.Humanoid
    else
        cam.CameraType = Enum.CameraType.Custom
        cam.CameraSubject = cam
    end
end
local cameraAnchor = CameraAnchor.new()

function saveOriginalPosAttr()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if hrp then
        char:SetAttribute("OriginalPosition", hrp:GetPivot())
    end
end

function getOriginalPosAttr()
    local char = LocalPlayer.Character
    return char and char:GetAttribute("OriginalPosition") or nil
end

function initCharAttrs()
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char:SetAttribute("OriginalPosition", char.HumanoidRootPart:GetPivot())
        char:SetAttribute("SavingOriginalPos", false)
    end
end

function scheduleReturnHome()
    local originalPos = getOriginalPosAttr()
    if not originalPos then return end
    
    local conn
    conn = RunService.Heartbeat:Connect(function()
        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if hrp then
            hrp:PivotTo(originalPos)
            if getgenv().originalFallenHeight then
                Workspace.FallenPartsDestroyHeight = getgenv().originalFallenHeight
            end
            char:SetAttribute("SavingOriginalPos", false)
        end
        cameraAnchor:detach()
        conn:Disconnect()
    end)
end

function modifyTarget(root, hum)
    if not (root and hum) or hum.Health <= 0 then return end
    local blob = ensureBlobman()
    if blob and blob:FindFirstChild("BlobmanSeatAndOwnerScript") then
        local drop = blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureDrop")
        if drop then
            for _, part in ipairs(hum.Parent:GetDescendants()) do
                if part:IsA("Weld") or part:IsA("BallSocketConstraint") then
                    drop:FireServer(part, part)
                end
            end
        end
    end
    hum.Sit = false
    hum:ChangeState(Enum.HumanoidStateType.Running)
    hum:SetStateEnabled(Enum.HumanoidStateType.Seated, false)
    hum:ChangeState(Enum.HumanoidStateType.GettingUp)

    local plr = Players:GetPlayerFromCharacter(hum.Parent)
    if plr and plr:FindFirstChild("IsHeld") then plr.IsHeld.Value = false end
    local rag = hum:FindFirstChild("Ragdolled")
    if rag then rag.Value = false end

    local bv, bav = Instance.new("BodyVelocity"), Instance.new("BodyAngularVelocity")
    bv.MaxForce = Vector3.new(1e7, -1e7, 1e7)
    bv.P = 100
    bv.Velocity = Vector3.new(math.random(-500, 50), -50, math.random(-50, 50))
    bav.MaxTorque = Vector3.new(-1e7, -1e7, -1e7)
    bav.P = 1e6
    bav.AngularVelocity = Vector3.new(math.random(-500, 300), math.random(-300, 300), math.random(-500, 500))
    bv.Parent, bav.Parent = root, root

    hum.BreakJointsOnDeath = false
    hum:ChangeState(Enum.HumanoidStateType.Dead)
    hum.RigType = Enum.HumanoidRigType.R15

    task.delay(0.08, function()
        if bv.Parent then bv:Destroy() end
        if bav.Parent then bav:Destroy() end
    end)
end

function performKill()
    if not SelectedPlayer then return end
    
    local target = Players:FindFirstChild(SelectedPlayer)
    local tChar = target and target.Character
    local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
    local tHum = tChar and tChar:FindFirstChild("Humanoid")
    local tHead = tChar and tChar:FindFirstChild("Head")
    
    if not (target and tRoot and tHum and tHead) then return end
    if isTooHigh(target) then return end
    if target:FindFirstChild("InPlot") and target.InPlot.Value then return end
    if tHum:GetState() == Enum.HumanoidStateType.Dead then return end

    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not (char and hrp) then return end

    if not char:GetAttribute("SavingOriginalPos") then
        saveOriginalPosAttr()
    end
    char:SetAttribute("SavingOriginalPos", true)

    getgenv().originalFallenHeight = Workspace.FallenPartsDestroyHeight
    Workspace.FallenPartsDestroyHeight = 0/0

    local originalPos = getOriginalPosAttr()
    if originalPos then
        cameraAnchor:attach(originalPos)
    end

    local desiredCFrame = CFrame.new(tRoot.Position + TELEPORT_OFFSET)
    hrp:PivotTo(desiredCFrame)

    setNoCollideChar(tChar)
    RemoteSetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
    task.wait()
    RemoteDestroyGrabLine:FireServer(tRoot)
    task.wait()

    if tHead:FindFirstChild("PartOwner") and tHead.PartOwner.Value == LocalPlayer.Name then
        task.wait()
        modifyTarget(tRoot, tHum)
    end

    scheduleReturnHome()
end

function StartLoopKill()
    if KillHB then KillHB:Disconnect() end
    KillHB = RunService.Heartbeat:Connect(performKill)
end

function StopLoopKill()
    if KillHB then KillHB:Disconnect() KillHB = nil end
    cameraAnchor:detach()
end

function sendToSky(root, hum)
    DisableCollisions(hum.Parent)
    local BV = Instance.new("BodyVelocity")
    BV.Velocity = Vector3.new(0, 9000000, 0)
    BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    BV.P = 100
    BV.Parent = root
    hum.Sit = false
    hum.Jump = true
    task.delay(0, function() if BV.Parent then BV:Destroy() end end)
end

function executeKick()
    if not SelectedPlayer then return end
    local p = Players:FindFirstChild(SelectedPlayer)
    local c = p and p.Character
    local root = c and c:FindFirstChild("HumanoidRootPart")
    local head = c and c:FindFirstChild("Head")
    local hum = c and c:FindFirstChild("Humanoid")
    if not (root and head and hum) or hum.Health <= 0 then return end
    if isTooHigh(p) then return end
    if p:FindFirstChild("InPlot") and p.InPlot.Value then return end

    local selfChar = LocalPlayer.Character
    local selfRoot = selfChar and selfChar:FindFirstChild("HumanoidRootPart")
    if not selfRoot then return end

    local saved = selfChar:GetPivot()
    selfChar:PivotTo(CFrame.new(root.Position + Vector3.new(0, 0, -3)))
    DisableCollisions(c)
    RemoteSetNetworkOwner:FireServer(root, root.CFrame)
    task.wait()
    selfChar:PivotTo(saved)
    task.wait(0.005)
    RemoteDestroyGrabLine:FireServer(root)
    task.wait(0.005)
    local po = head:FindFirstChild("PartOwner")
    if po and po.Value == LocalPlayer.Name then
        sendToSky(root, hum)
    end
end

function StartLoopKick()
    if KickHB then KickHB:Disconnect() end
    LoopKickOn = true
    KickHB = RunService.Heartbeat:Connect(function()
        if LoopKickOn then executeKick() end
    end)
end

function StopLoopKick()
    LoopKickOn = false
    if KickHB then KickHB:Disconnect() KickHB = nil end
end


function getPlayerList()
    local list = {}
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            local displayName = plr.DisplayName or plr.Name
            local entry = string.format("%s (@%s)", displayName, plr.Name)
            table.insert(list, entry)
        end
    end
    return list
end

function extractUsername(entry)
    local username = entry:match("@([%w_]+)")
    return username
end

LoopTab:CreateButton({
    Name = "Spawn Blobman",
    Callback = function()
        spawnBlobman()
    end
})

LoopTab:CreateButton({
    Name = "Sit on Blobman",
    Callback = function()
        local blob = findBlobman()
        if blob then
            local seat = blob:FindFirstChild("VehicleSeat")
            local char = LocalPlayer.Character
            local humanoid = char and char:FindFirstChild("Humanoid")
            if seat and humanoid then
                seat:Sit(humanoid)
            end
        end
    end
})



PlayerDropdown = LoopTab:CreateDropdown({
    Name = "Select Player",
    Options = getPlayerList(),
    CurrentOption = {},
    Flag = "PlayerDropdown",
    Callback = function(option)
        local selected = type(option) == "table" and option[1] or option
        SelectedPlayer = extractUsername(selected)
    end
})

LoopTab:CreateButton({
    Name = "Refresh Player List",
    Callback = function()
        PlayerDropdown:Refresh(getPlayerList(), true)
    end
})

LoopTab:CreateButton({
    Name = 'Bring<font face="GothamBlack" color="rgb(160, 0, 255)">grab</font>',
    Callback = function()
        if not SelectedPlayer then return end

        local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local root = character:WaitForChild("HumanoidRootPart")
        local oldCFrame = root.CFrame

        local targetPlayer = Players:FindFirstChild(SelectedPlayer)
        if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("Head") then
            local targetHead = targetPlayer.Character.Head

            for i = 1, 2 do
                if not targetPlayer.Character or not targetPlayer.Character:FindFirstChild("Head") then break end
                root.CFrame = targetHead.CFrame * CFrame.new(2, 0, 0)
                local args = { [1] = targetHead, [2] = root.CFrame }
                RemoteSetNetworkOwner:FireServer(unpack(args))
                task.wait(0.3)
            end

            task.wait(0.3)
            root.CFrame = oldCFrame
            local front = oldCFrame.LookVector * 5
            targetHead.CFrame = CFrame.new(oldCFrame.Position + front)

            local destroyArgs = { [1] = targetHead }
            RemoteDestroyGrabLine:FireServer(unpack(destroyArgs))
        end
    end
})

LoopTab:CreateToggle({
    Name = 'Loop Kick<font face="GothamBlack" color="rgb(160, 0, 255)">grab</font>',
    CurrentValue = false,
    Flag = "LoopKickToggle",
    Callback = function(v)
        if v then
            StartLoopKick()
        else
            StopLoopKick()
        end
    end
})

LoopTab:CreateToggle({
    Name = "Loop Kill",
    CurrentValue = false,
    Flag = "LoopKillToggle",
    Callback = function(v) 
        if v then StartLoopKill() else StopLoopKill() end 
    end
})

LoopTab:CreateToggle({
    Name = "Blobman Kick",
    CurrentValue = false,
    Flag = "BlobKickToggle",
    Callback = function(enabled)
        LoopBlobKickOn = enabled

        local function findMountedBlob()
            local char = Players.LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            return (hum and hum.SeatPart and hum.SeatPart.Parent.Name == "CreatureBlobman") and hum.SeatPart.Parent or nil
        end

        local function bringRightArm(targetName, blob)
            local tp = Players:FindFirstChild(targetName)
            local hrp = tp and tp.Character and tp.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(Players.LocalPlayer, hrp, blob.RightDetector.RightWeld)
            end
        end

        local function execBlobKick(targetName)
            local char = Players.LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not (hum and hrp) then return end

            local blob = findMountedBlob() or ensureBlobman()
            task.wait(0.12)

            local seat = blob and blob:FindFirstChild("VehicleSeat")
            if seat then seat:Sit(hum) task.wait(0.12) end

            local tp = Players:FindFirstChild(targetName)
            local tHRP = tp and tp.Character and tp.Character:FindFirstChild("HumanoidRootPart")
            if not tHRP then return end

            local startSelf, startBlob = hrp.CFrame, blob.PrimaryPart and blob.PrimaryPart.CFrame
            local oldCF = tHRP.CFrame
            hrp.CFrame = oldCF
            task.wait(0.12)

            for _ = 1, 15 do
                task.wait()
                RemoteSetNetworkOwner:FireServer(tHRP, tHRP.CFrame)
                tHRP.CFrame = oldCF * CFrame.new(0, 40, 0)
            end
            task.wait(0.13)
            RemoteDestroyGrabLine:FireServer(tHRP)
            task.wait(0.13)

            bringRightArm(targetName, blob)
            bringRightArm(targetName, blob)
            bringRightArm(targetName, blob)

            task.delay(0.55, function()
                if hrp then hrp.CFrame = startSelf end
                if blob and blob.PrimaryPart and startBlob then
                    blob:SetPrimaryPartCFrame(startBlob)
                end
            end)
        end

        local function monitorRespawnBlobKick(targetName)
            local tp = Players:FindFirstChild(targetName)
            if not tp then return end
            tp.CharacterAdded:Connect(function()
                if LoopBlobKickOn then
                    task.wait(0.75)
                    execBlobKick(targetName)
                end
            end)
        end

        if enabled then
            if SelectedPlayer and SelectedPlayer ~= "" then
                execBlobKick(SelectedPlayer)
                monitorRespawnBlobKick(SelectedPlayer)
            end
        else
            LoopBlobKickOn = false
        end
    end
})

LoopTab:CreateToggle({
    Name = "Blob Kill",
    CurrentValue = false,
    Flag = "BlobKillToggle",
    Callback = function(enabled)
        LoopBlobKillOn = enabled

        local function execBlobKill(targetName)
            if not targetName or targetName == "" then return end
            local plr = Players:FindFirstChild(targetName)
            if not plr or not plr.Character then return end
            local localPlayer = Players.LocalPlayer
            local localChar = localPlayer.Character
            if not localChar then return end
            local hum = localChar:FindFirstChild("Humanoid")
            local hrp = localChar:FindFirstChild("HumanoidRootPart")
            if not (hum and hrp) then return end
            local blob =
            (hum.SeatPart
                and hum.SeatPart.Parent.Name == "CreatureBlobman"
                and hum.SeatPart.Parent)
            or ensureBlobman()
            
            if not blob or not blob.PrimaryPart then
    return
end

local targetHRP = plr.Character:FindFirstChild("HumanoidRootPart")
if not targetHRP then
    return
end
if targetHRP.Position.Y > HEIGHT_LIMIT then return end

        local startLocalCFrame = hrp.CFrame
        local startBlobCFrame = blob.PrimaryPart.CFrame

        blob:SetPrimaryPartCFrame(targetHRP.CFrame)

        if blob:FindFirstChild("VehicleSeat") then
            blob.VehicleSeat:Sit(hum)
            task.wait(0.05)
        end

        local detector = blob:FindFirstChild("LeftDetector")
        local weld = detector and detector:FindFirstChild("LeftWeld")
        if detector and weld then
            blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(detector, targetHRP, weld)
        end

        task.wait(0.05)

        local targetHum = plr.Character:FindFirstChildOfClass("Humanoid")
        if targetHum then
            targetHum.RigType = Enum.HumanoidRigType.R15
        end

        task.wait(0.05)

        if weld then
            blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(weld, targetHRP)
        end

        task.delay(0.05, function()
            hrp.CFrame = startLocalCFrame
            if blob and blob.PrimaryPart then
                blob:SetPrimaryPartCFrame(startBlobCFrame)
            end
        end)
    end

    if enabled then
        task.spawn(function()
            while LoopBlobKillOn do
                if SelectedPlayer and Players:FindFirstChild(SelectedPlayer) then
                    local plr = Players[SelectedPlayer]
                    local hum = plr.Character and plr.Character:FindFirstChildOfClass("Humanoid")
                    if hum and hum.Health > 0 then
                        execBlobKill(SelectedPlayer)
                    end
                end
                task.wait(0.06)
            end
        end)
    else
        LoopBlobKillOn = false
    end
end
})
LoopTab:CreateToggle({
Name = 'Spam Kick<font face="GothamBlack" color="rgb(160, 0, 255)">blob + grab</font>',
CurrentValue = false,
Flag = "SpamKickToggle",
Callback = function(Value)
spamActive = Value if Value then
        if not SelectedPlayer then
            spamActive = false
            return
        end
        
        local blob = findBlobman()
        if not blob then
            spamActive = false
            return
        end
        
        local char = LocalPlayer.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then
            spamActive = false
            return
        end
        
        local RightDetector = blob:FindFirstChild("RightDetector")
        if not RightDetector then
            spamActive = false
            return
        end
        
        local RightWeld = RightDetector:FindFirstChild("RightWeld")
        local BlobmanScript = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
        
        if not (RightWeld and BlobmanScript) then
            spamActive = false
            return
        end
        
        local CreatureGrab = BlobmanScript:FindFirstChild("CreatureGrab")
        local CreatureRelease = BlobmanScript:FindFirstChild("CreatureRelease")
        
        if not (CreatureGrab and CreatureRelease) then
            spamActive = false
            return
        end
        
        task.spawn(function()
            local oldCF = char:GetPivot()
            local targetPlayer = Players:FindFirstChild(SelectedPlayer)
            local targetChar = targetPlayer and targetPlayer.Character
            
            if not targetChar or not targetChar:FindFirstChild("HumanoidRootPart") then
                spamActive = false
                return
            end
            
            local targetRoot = targetChar.HumanoidRootPart
            
            pcall(function()
                char:PivotTo(targetRoot.CFrame)
                task.wait(0.18)
                CreatureGrab:FireServer(RightDetector, targetRoot, RightWeld)
                task.wait(0.18)
                CreatureRelease:FireServer(RightWeld, targetRoot)
                
                task.defer(function()
                    local BodyPos = Instance.new("BodyPosition")
                    BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    BodyPos.Position = oldCF.Position + Vector3.new(math.random(-15, 15), math.random(-10, 10), math.random(-15, 15))
                    BodyPos.Parent = targetRoot
                    BodyPos.P = 45000
                    BodyPos.D = 500
                end)
            end)
            
            task.wait(0.18)
            pcall(function()
                char:PivotTo(oldCF)
            end)
            task.wait(0.18)
            
            while spamActive do
                targetPlayer = Players:FindFirstChild(SelectedPlayer)
                if not targetPlayer or not Players:FindFirstChild(SelectedPlayer) then
                    spamActive = false
                    break
                end
                
                if not targetPlayer.Character or not targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    task.wait(0.18)
                    continue
                end
                
                targetRoot = targetPlayer.Character.HumanoidRootPart
                local targetHead = targetPlayer.Character:FindFirstChild("Head")
                
                pcall(function()
                    sno(targetRoot)
                    if targetHead then
                        sno(targetHead)
                    end
                    RemoteDestroyGrabLine:FireServer(targetRoot)
                    
                    CreatureGrab:FireServer(RightDetector, targetRoot, RightWeld)
                    
                    if targetHead then
                        GrabEvents.CreateGrabLine:FireServer(targetHead, targetHead.CFrame)
                    end
                    GrabEvents.CreateGrabLine:FireServer(targetRoot, targetRoot.CFrame)
                    
                    CreatureRelease:FireServer(RightWeld, targetRoot)
                end)
                
                task.wait()
            end
        end)
    end
end})

LocalPlayer = Players.LocalPlayer
autoBlobmanEnabled = false
targetCFrame = CFrame.new(466.741, 28, -745.949, 0.906275, -0.000000, -0.422688, 0.000000, 1.000000, -0.000000, 0.422688, 0.000000, 0.906275)
originalPosition = nil
processingBlobmans = {}

function saveCurrentPosition()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        originalPosition = LocalPlayer.Character.HumanoidRootPart.CFrame
        return true
    end
    return false
end

function teleportToPosition(cframe)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = cframe
        LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
        LocalPlayer.Character.HumanoidRootPart.AssemblyAngularVelocity = Vector3.zero
        return true
    end
    return false
end

function sitOnBlobman(blobman)
    local seat = blobman:FindFirstChild("VehicleSeat")
    local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
    
    if seat and humanoid and not seat.Occupant then
        seat:Sit(humanoid)
        task.wait(0.08)
        return seat.Occupant == humanoid
    end
    return false
end

function getOffBlobman()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        local humanoid = LocalPlayer.Character.Humanoid
        if humanoid.SeatPart then
            humanoid.Sit = false
            humanoid.Jump = true
            return true
        end
    end
    return false
end

function executeBlobmanSequence(blobman)
    if processingBlobmans[blobman] then return end
    if not SelectedPlayer then return end
    
    processingBlobmans[blobman] = true
    
    task.spawn(function()
        if not saveCurrentPosition() then
            processingBlobmans[blobman] = nil
            return
        end
        
        local blobmanPosition = nil
        if blobman:FindFirstChild("VehicleSeat") then
            blobmanPosition = blobman.VehicleSeat.CFrame
        elseif blobman:FindFirstChild("HumanoidRootPart") then
            blobmanPosition = blobman.HumanoidRootPart.CFrame
        elseif blobman.PrimaryPart then
            blobmanPosition = blobman:GetPrimaryPartCFrame()
        end
        
        if blobmanPosition then
            teleportToPosition(blobmanPosition + Vector3.new(0, 2, 0))
            task.wait(0.12)
        end
        
        if sitOnBlobman(blobman) then
            task.wait(0.12)
        end
        
        teleportToPosition(targetCFrame)
        task.wait(0.14)
        
        getOffBlobman()
        task.wait(0.14)
        
        if originalPosition then
            teleportToPosition(originalPosition)
        end
        
        processingBlobmans[blobman] = nil
    end)
end

workspace.DescendantAdded:Connect(function(descendant)
    if not autoBlobmanEnabled or not SelectedPlayer then return end
    
    if descendant.Name == "CreatureBlobman" and descendant:IsA("Model") then
        local parent = descendant.Parent
        while parent do
            if parent.Name == SelectedPlayer .. "SpawnedInToys" and parent:IsA("Folder") then
                task.wait(0.14)
                executeBlobmanSequence(descendant)
                break
            end
            parent = parent.Parent
        end
    end
end)

RunService.RenderStepped:Connect(function()
    if not autoBlobmanEnabled or not SelectedPlayer then return end
    
    local toysFolder = workspace:FindFirstChild(SelectedPlayer .. "SpawnedInToys")
    if not toysFolder then return end
    
    for _, toy in ipairs(toysFolder:GetDescendants()) do
        if toy.Name == "CreatureBlobman" and toy:IsA("Model") then
            if not processingBlobmans[toy] then
                executeBlobmanSequence(toy)
            end
        end
    end
end)

LoopTab:CreateToggle({
    Name = "Delete blob (when sm1 spawn a blob sit on it and put in da water to delete works for plots)",
    CurrentValue = false,
    Flag = "AutoBlobman",
    Callback = function(Value)
        autoBlobmanEnabled = Value
        if Value then
            if SelectedPlayer then
                Rayfield:Notify({
                    Title = "Auto Blobman",
                    Content = "Système activé - Ciblage: " .. SelectedPlayer,
                    Duration = 3
                })
            else
                Rayfield:Notify({
                    Title = "Auto Blobman",
                    Content = "Sélectionnez d'abord un joueur cible",
                    Duration = 3
                })
                autoBlobmanEnabled = false
                return
            end
        else
            Rayfield:Notify({
                Title = "Auto Blobman",
                Content = "Système désactivé",
                Duration = 2
            })
            processingBlobmans = {}
        end
    end
})

Players.PlayerRemoving:Connect(function()
task.wait(0)
PlayerDropdown:Refresh(getPlayerList(), true)
end)
LocalPlayer.CharacterAdded:Connect(function(char)
initCharAttrs()
local hum = char:WaitForChild("Humanoid", 5)
if hum then
hum.Died:Connect(function()
cameraAnchor:detach()
end)
end
end)


CommandsTab = Window:CreateTab("Cmds", 7743871002)

Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
RunService = game:GetService("RunService")
Workspace = game:GetService("Workspace")
UserInputService = game:GetService("UserInputService")
LocalPlayer = Players.LocalPlayer

GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
RemoteSetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
RemoteDestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
SpawnToyRF = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
Struggle = ReplicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("Struggle")
StickyPartEvent = ReplicatedStorage:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")
DestroyToyEvent = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
MenuToys = ReplicatedStorage:WaitForChild("MenuToys")

SelectedPlayer = nil
bringTimer = 0
tpTimer = 0
switchTimer = 0
Mouse = LocalPlayer:GetMouse()
KillHB = nil
LoopKillOn = false
LoopBringActive = false
LoopBangActive = false

LoopPencilActive = false
PencilList = {}
AKList = {}
PencilCooldown = {}

LoopBlobkickActive = false
BlobkickList = {}
Blob = nil
BlobkickCooldown = {}
BKA = false
BKAWL = false


ESPEnabled = false
ESPTargets = {}
ESPBoxes = {}

AllowedUsers = {"thecrazyteen","AshtvnGetBanPfff","RandomHubBestHubFrFr", "78cvzl","Posuu_verified","SuPraa006"}

BangSpeed = 0.8
BangDistance = 4
HEIGHT_LIMIT = 100000
TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)

function IsAllowed(playerName)
    for _, allowedName in pairs(AllowedUsers) do
        if string.lower(allowedName) == string.lower(playerName) then
            return true
        end
    end
    return false
end

function FWC(Parent, Name, Time)
    return Parent:FindFirstChild(Name) or Parent:WaitForChild(Name, Time)
end

function grab(prt)
    RemoteSetNetworkOwner:FireServer(prt, prt.CFrame)
end

function SpawnPencilForPlayer(playerName)
    local targetPlayer = Players:FindFirstChild(playerName)
    if not targetPlayer or not targetPlayer.Character then return nil end
    
    if not LocalPlayer.CanSpawnToy.Value then return nil end
    
    local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if spawnedToys then
        local oldPencil = spawnedToys:FindFirstChild(playerName)
        if oldPencil then
            DestroyToyEvent:FireServer(oldPencil)
            task.wait(0.18)
        end
    end
    
    local spawnCFrame = CFrame.new(targetPlayer.Character.HumanoidRootPart.Position + Vector3.new(0, 0, 5))
    
    local success, result = pcall(function()
        return SpawnToyRF:InvokeServer("ToolPencil", spawnCFrame, Vector3.new(0, 0, 0))
    end)
    
    if not success then return nil end
    
    for i = 1, 10 do
        local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
        if spawnedToys then
            local pencil = spawnedToys:FindFirstChild("ToolPencil")
            if pencil then
                pencil.Name = playerName
                return pencil
            end
        end
        task.wait(0.1)
    end
    
    return nil
end

function AttachPencilToPlayer(pencil, playerName)
    local targetPlayer = Players:FindFirstChild(playerName)
    if not targetPlayer or not targetPlayer.Character then return false end
    
    local torso = targetPlayer.Character:FindFirstChild("Torso")
    if not torso then return false end
    
    task.wait(0.4)
    
    local soundPart = pencil:FindFirstChild("SoundPart")
    if soundPart then
        RemoteSetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
    end
    
    if pencil:FindFirstChild("StickyPart") then
        StickyPartEvent:FireServer(pencil.StickyPart, torso, CFrame.new(0, -1, 0) * CFrame.Angles(0, math.pi, 0))
        return true
    end
    
    return false
end

function CheckAndManagePencil(playerName)
    if not LoopPencilActive then return end
    if not table.find(PencilList, playerName) then return end
    
    local targetPlayer = Players:FindFirstChild(playerName)
    if not targetPlayer or not targetPlayer.Character then 
        local index = table.find(PencilList, playerName)
        if index then table.remove(PencilList, index) end
        return
    end
    
    if targetPlayer.InPlot.Value then return end
    
    if PencilCooldown[playerName] and tick() - PencilCooldown[playerName] < 2 then return end
    
    local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    local hasPencil = false
    
    if spawnedToys then
        local pencil = spawnedToys:FindFirstChild(playerName)
        if pencil and pencil:FindFirstChild("StickyPart") then
            local stickyWeld = pencil.StickyPart:FindFirstChild("StickyWeld")
            if stickyWeld and stickyWeld.Part1 then
                hasPencil = true
            end
        end
    end
    
    if not hasPencil then
        PencilCooldown[playerName] = tick()
        local newPencil = SpawnPencilForPlayer(playerName)
        if newPencil then
            AttachPencilToPlayer(newPencil, playerName)
        end
    end
end

function PencilPlayer(playerName, loopMode)
    if not Players:FindFirstChild(playerName) then
        return Rayfield:Notify({
            Title = "Pencil Error",
            Content = 'Player left',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local targetPlayer = Players:FindFirstChild(playerName)
    
    if loopMode then
        if not table.find(PencilList, playerName) then
            table.insert(PencilList, playerName)
        end
        if not LoopPencilActive then
            LoopPencilActive = true
        end
        return true
    else
        local pencil = SpawnPencilForPlayer(playerName)
        if pencil then
            local attached = AttachPencilToPlayer(pencil, playerName)
            return attached
        end
        return false
    end
end

function DestroyBlob()
    if Blob and Blob.Parent then
        DestroyToyEvent:FireServer(Blob)
        Blob = nil
    end
end

function BlobKick()
    while true do
        local smegma = true
        local listtype = 1
        local gotsomeone = false
        local listtt = {}
        
        if BKA then
            listtype = 2
            for _,plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer then
                    if BKAWL and not LocalPlayer:IsFriendsWith(plr.UserId) then
                        table.insert(listtt,plr.Name)
                    elseif not BKAWL then
                        table.insert(listtt,plr.Name)
                    end
                end
            end
        else
            listtt = BlobkickList
        end
        
        if listtype == 2 then
            if not BKA then
                smegma = false
            end
        end
        
        for _,plrr in pairs(listtt) do
            local j, h
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                j = LocalPlayer.Character.HumanoidRootPart.CFrame
                h = LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity
            end
            
            if smegma and Players:FindFirstChild(plrr) and Players:FindFirstChild(plrr) ~= LocalPlayer then
                local continue = false
                local plr = Players:FindFirstChild(plrr)
                
                if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                    if plr.Character.HumanoidRootPart.Massless then
                        if plr.Character.Humanoid.SeatPart then
                            continue = true
                        end
                    else
                        continue = true
                    end
                end
                
                if BKA and BKAWL and LocalPlayer:IsFriendsWith(Players:FindFirstChild(plrr).UserId) then
                    continue = false
                end
                
                while smegma and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and plr and plr.Character and plr.Character.Parent == Workspace and plr.Character:FindFirstChild("HumanoidRootPart") and continue do
                    if listtype == 2 then
                        if not BKA then
                            smegma = false
                        end
                    end
                    
                    gotsomeone = true
                    continue = false
                    
                    if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                        if plr.Character.HumanoidRootPart.AssemblyLinearVelocity.Magnitude > 5000 then
                            continue = false
                        end
                        if plr.Character.HumanoidRootPart.Massless then
                            if plr.Character.Humanoid.SeatPart then
                                continue = true
                            end
                        else
                            continue = true
                        end
                    end
                    
                    if continue and LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid.SeatPart and LocalPlayer.Character.Humanoid.SeatPart.Parent and LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == "CreatureBlobman" then
                        Blob = LocalPlayer.Character.Humanoid.SeatPart.Parent
                        local localtime = tick()
                        
                        if (plr.Character.HumanoidRootPart.CFrame.Position - LocalPlayer.Character.HumanoidRootPart.CFrame.Position + LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity).magnitude > 30 and plr.Character.Parent == Workspace then
                            LocalPlayer.Character.HumanoidRootPart.CFrame = plr.Character:FindFirstChild("HumanoidRootPart").CFrame + (plr.Character:FindFirstChild("HumanoidRootPart").AssemblyLinearVelocity / math.pi)
                            LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = plr.Character:FindFirstChild("HumanoidRootPart").AssemblyLinearVelocity
                            task.wait(0.25)
                            if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                                RemoteSetNetworkOwner:FireServer(plr.Character.HumanoidRootPart, plr.Character.HumanoidRootPart.CFrame)
                            end
                        end
                        
                        local timee = tick()
                        while tick() - timee < 0.25 and plr and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") and continue do
                            RemoteSetNetworkOwner:FireServer(plr.Character.HumanoidRootPart, plr.Character.HumanoidRootPart.CFrame)
                            task.wait(0.1)
                        end
                        
                        if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and Blob:FindFirstChild("RightDetector") and Blob.RightDetector:FindFirstChild("RightWeld") and Blob:FindFirstChild("BlobmanSeatAndOwnerScript") and Blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab") then
                            plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0,10^10,0)
                            RemoteDestroyGrabLine:FireServer(plr.Character.HumanoidRootPart)
                            Blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(Blob.RightDetector, plr.Character.HumanoidRootPart, Blob.RightDetector.RightWeld)
                        end
                    elseif continue then
                        if LocalPlayer.Character:FindFirstChild("Humanoid") then
                            if LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid.SeatPart then
                                LocalPlayer.Character.Humanoid.Sit = false
                            end
                        end
                        
                        if Blob and Blob:FindFirstChild("VehicleSeat") then
                            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
                                Blob.VehicleSeat:Sit(LocalPlayer.Character.Humanoid)
                            end
                            task.wait(0.15)
                        else
                            local foundblob = false
                            Blob = nil
                            
                            local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
                            if spawnedToys then
                                for _,itm in pairs(spawnedToys:GetChildren()) do
                                    if itm.Name == "CreatureBlobman" and itm:FindFirstChild("VehicleSeat") then
                                        foundblob = true
                                        Blob = itm
                                    elseif itm.Name == "CreatureBlobman" then
                                        while itm do
                                            DestroyToyEvent:FireServer(itm)
                                            task.wait(0.15)
                                        end
                                    end
                                end
                            end
                            
                            if Blob and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
                                Blob.VehicleSeat:Sit(LocalPlayer.Character.Humanoid)
                            end
                            
                            if not foundblob then
                                task.wait(0.15)
                                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                                    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character.Parent ~= Workspace then
                                        LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(0,-10,0)
                                        LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
                                    end
                                    
                                    while smegma and not Blob do
                                        if listtype == 2 then
                                            if not BKA then
                                                smegma = false
                                            end
                                        end
                                        
                                        local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
                                        if spawnedToys and spawnedToys:FindFirstChild("CreatureBlobman") then
                                            Blob = spawnedToys.CreatureBlobman
                                        elseif LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character.Parent ~= Workspace then
                                            LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(0,-10,0)
                                            LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
                                        end
                                        
                                        if not Blob then
                                            task.spawn(function()
                                                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                                                    SpawnToyRF:InvokeServer("CreatureBlobman", CFrame.new(LocalPlayer.Character.HumanoidRootPart.CFrame.Position) + Vector3.new(0,0,15), Vector3.new(0,0,0))
                                                end
                                            end)
                                        end
                                        task.wait()
                                    end
                                end
                            end
                        end
                    end
                    task.wait(0.15)
                end
            end
            
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                LocalPlayer.Character.HumanoidRootPart.CFrame = j
                LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = h
            end
        end
        
        if not gotsomeone and Blob then
            while Blob and Blob.Parent do
                DestroyToyEvent:FireServer(Blob)
                task.wait(0.15)
            end
            Blob = nil
        end
        task.wait()
    end
end

function BlobKickPlayer(playerName, loopMode)
    if not Players:FindFirstChild(playerName) then
        return Rayfield:Notify({
            Title = "Blobkick Error",
            Content = 'Player left',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local targetPlayer = Players:FindFirstChild(playerName)
    
    if loopMode then
        if not table.find(BlobkickList, playerName) then
            table.insert(BlobkickList, playerName)
        end
        if not LoopBlobkickActive then
            LoopBlobkickActive = true
        end
        return true
    else
        local tempAdded = false
        if not table.find(BlobkickList, playerName) then
            table.insert(BlobkickList, playerName)
            tempAdded = true
        end
        
        task.wait(0.15)
        
        if tempAdded then
            local index = table.find(BlobkickList, playerName)
            if index then
                table.remove(BlobkickList, index)
            end
        end
        
        return true
    end
end

task.spawn(BlobKick)


function BringPlayer(name, loopMode)
    if not Players:FindFirstChild(name) then
        return Rayfield:Notify({
            Title = "Bring Error",
            Content = 'Player left',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local targetPlayer = Players:FindFirstChild(name)
    local targetChar = targetPlayer.Character
    
    if not targetChar then
        return Rayfield:Notify({
            Title = "Bring Error",
            Content = 'Player has no character',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    local targetHum = targetChar:FindFirstChild("Humanoid")
    
    if not targetHRP or not targetHum then return end
    
    if targetHum.Health <= 0 then
        return Rayfield:Notify({
            Title = "Bring Error",
            Content = 'Player is dead',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    if targetPlayer.InPlot.Value then
        return Rayfield:Notify({
            Title = "Bring Error",
            Content = 'Player in safe zone',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local myHRP = mychar:FindFirstChild("HumanoidRootPart")
    local myHum = mychar:FindFirstChild("Humanoid")
    
    if not myHRP or not myHum then return end
    
    local BackUp = myHRP.CFrame
    local success = false
    
    for i = 1, 30 do
        if not targetHRP or not targetHRP.Parent then break end
        if myHum.Health <= 0 then break end
        
        myHRP.CFrame = targetHRP.CFrame + Vector3.new(0, -2, 0)
        grab(targetHRP)
        
        if targetChar.Head:FindFirstChild("PartOwner") and targetChar.Head.PartOwner.Value == LocalPlayer.Name then
            success = true
            break
        end
        
        task.wait(0.08)
    end
    
    if not success then
        myHRP.CFrame = BackUp
        return Rayfield:Notify({
            Title = "Bring Failed",
            Content = 'Could not grab player',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    myHRP.CFrame = BackUp
    
    if loopMode then
        task.spawn(function()
            while LoopBringActive and targetChar and targetChar.Parent do
                if not Players:FindFirstChild(name) then
                    LoopBringActive = false
                    break
                end
                
                if targetPlayer.InPlot.Value or targetHum.Health <= 0 then
                    LoopBringActive = false
                    break
                end
                
                local targetCFrame = myHRP.CFrame + (myHRP.CFrame.LookVector * 3)
                targetHRP.CFrame = targetCFrame
                targetHRP.Velocity = Vector3.new()
                grab(targetHRP)
                
                task.wait()
            end
            Destroy_Line(targetHRP)
        end)
    else
        task.defer(function()
            local startTime = tick()
            while targetChar and targetChar.Parent and targetChar.Head:FindFirstChild("PartOwner") and 
                  targetChar.Head.PartOwner.Value == LocalPlayer.Name and tick() - startTime < 5 do
                local targetCFrame = myHRP.CFrame + (myHRP.CFrame.LookVector * 3)
                targetHRP.CFrame = targetCFrame
                targetHRP.Velocity = Vector3.new()
                task.wait()
            end
            Destroy_Line(targetHRP)
        end)
    end
    
    return true
end

function BangPlayer(name, loopMode)
    if not Players:FindFirstChild(name) then
        return Rayfield:Notify({
            Title = "Bang Error",
            Content = 'Player left',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local targetPlayer = Players:FindFirstChild(name)
    local targetChar = targetPlayer.Character
    
    if not targetChar then
        return Rayfield:Notify({
            Title = "Bang Error",
            Content = 'Player has no character',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    local targetHum = targetChar:FindFirstChild("Humanoid")
    
    if not targetHRP or not targetHum then return end
    
    if targetHum.Health <= 0 then
        return Rayfield:Notify({
            Title = "Bang Error",
            Content = 'Player is dead',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    if targetPlayer.InPlot.Value then
        return Rayfield:Notify({
            Title = "Bang Error",
            Content = 'Player in safe zone',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local myHRP = mychar:FindFirstChild("HumanoidRootPart")
    local myHum = mychar:FindFirstChild("Humanoid")
    
    if not myHRP or not myHum then return end
    
    local BackUp = myHRP.CFrame
    local success = false
    
    for i = 1, 30 do
        if not targetHRP or not targetHRP.Parent then break end
        if myHum.Health <= 0 then break end
        
        myHRP.CFrame = targetHRP.CFrame + Vector3.new(0, -2, 0)
        grab(targetHRP)
        
        if targetChar.Head:FindFirstChild("PartOwner") and targetChar.Head.PartOwner.Value == LocalPlayer.Name then
            success = true
            break
        end
        
        task.wait(0.05)
    end
    
    if not success then
        myHRP.CFrame = BackUp
        return Rayfield:Notify({
            Title = "Bang Failed",
            Content = 'Could not grab player',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    myHRP.CFrame = BackUp
    
    if loopMode then
        task.spawn(function()
            local bangOffset = 0
            local bangDirection = 1
            
            while LoopBangActive and targetChar and targetChar.Parent do
                if not Players:FindFirstChild(name) then
                    LoopBangActive = false
                    break
                end
                
                if targetPlayer.InPlot.Value or targetHum.Health <= 0 then
                    LoopBangActive = false
                    break
                end
                
                bangOffset = bangOffset + (bangDirection * (BangSpeed * 0.08))
                
                if bangOffset >= BangDistance then
                    bangDirection = -1
                elseif bangOffset <= 0 then
                    bangDirection = 2
                end
                
                local targetCFrame = myHRP.CFrame + (myHRP.CFrame.LookVector * bangOffset)
                targetHRP.CFrame = targetCFrame
                targetHRP.Velocity = Vector3.new()
                grab(targetHRP)
                
                task.wait()
            end
            Destroy_Line(targetHRP)
        end)
    else
        task.defer(function()
            local startTime = tick()
            local bangOffset = 0
            local bangDirection = 2
            
            while targetChar and targetChar.Parent and targetChar.Head:FindFirstChild("PartOwner") and 
                  targetChar.Head.PartOwner.Value == LocalPlayer.Name and tick() - startTime < 3 do
                
                bangOffset = bangOffset + (bangDirection * (BangSpeed * 0.3))
                
                if bangOffset >= BangDistance then
                    bangDirection = -1
                elseif bangOffset <= 0 then
                    bangDirection = 2
                end
                
                local targetCFrame = myHRP.CFrame + (myHRP.CFrame.LookVector * bangOffset)
                targetHRP.CFrame = targetCFrame
                targetHRP.Velocity = Vector3.new()
                
                task.wait()
            end
            Destroy_Line(targetHRP)
        end)
    end
    
    return true
end

function SwitchPlayer()
    if switchTimer ~= 0 then return end
    
    if not Players:FindFirstChild(SelectedPlayer) then
        return Rayfield:Notify({
            Title = "Switch error",
            Content = 'Player left',
            Duration = 4,
            Image = 7743878056,
        })
    end
    
    targetPlayer = Players:FindFirstChild(SelectedPlayer)
    targetChar = targetPlayer.Character
    
    if not targetChar then return end
    
    targetHum = FWC(targetChar, "Humanoid", 3)
    HRP = FWC(targetChar, "HumanoidRootPart", 3)
    
    if targetHum.Health == 0 or targetPlayer.InPlot.Value then
        return Rayfield:Notify({
            Title = "Switch Error",
            Content = 'Player died or in safe-zone',
            Duration = 4,
            Image = 7743878857,
        })
    end
    
    mychar = LocalPlayer.Character
    myHRP = FWC(mychar, "HumanoidRootPart", 3)
    BackUp = myHRP.CFrame
    
    while task.wait() and HRP and FWC(mychar, "Humanoid").Health ~= 0 and not (HRP.Parent.Head:FindFirstChild("PartOwner")) or (HRP.Parent.Head:FindFirstChild("PartOwner") and HRP.Parent.Head:FindFirstChild("PartOwner").Value ~= LocalPlayer.Name) do
        if switchTimer > 75 then
            myHRP.CFrame = BackUp
            switchTimer = 0
            return
        else
            switchTimer = switchTimer + 1
        end
        
        if Workspace.PlotItems.PlayersInPlots:FindFirstChild(SelectedPlayer) or LocalPlayer.IsHeld.Value == true then
            myHRP.CFrame = BackUp
            return
        end
        
        myHRP.CFrame = HRP.CFrame
        task.defer(grab, HRP)
    end
    
    for _, prt in pairs(HRP.Parent:GetChildren()) do
        if prt:IsA("Part") then
            prt.Velocity = Vector3.new()
        end
    end
    
    HRP.CFrame = BackUp
    
    if GrabFlag == "Default" then
        Destroy_Line(HRP)
    end
    
    for _, prt in pairs(myHRP.Parent:GetChildren()) do
        if prt:IsA("Part") then
            prt.Velocity = Vector3.new()
        end
    end
    
    switchTimer = 0
end

function TPPlayerToWater(name)
    if not Players:FindFirstChild(name) then
        return Rayfield:Notify({
            Title = "TP Error",
            Content = 'Player left',
            Duration = 4,
            Image = 7743878056,
        })
    end
    
    targetPlayer = Players:FindFirstChild(name)
    targetChar = targetPlayer.Character
    
    if not targetChar then return end
    
    targetHum = FWC(targetChar, "Humanoid", 3)
    targetHRP = FWC(targetChar, "HumanoidRootPart", 3)
    targetHead = FWC(targetChar, "Head", 3)
    
    if targetHum.Health == 0 or targetHum:GetState() == Enum.HumanoidStateType.Dead then
        return Rayfield:Notify({
            Title = "TP Error",
            Content = 'Player died',
            Duration = 4,
            Image = 7743878857,
        })
    end
    
    if targetPlayer.InPlot.Value then
        return Rayfield:Notify({
            Title = "TP Error",
            Content = 'Player in House',
            Duration = 4,
            Image = 7743878857,
        })
    end
    
    mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    myhum = FWC(mychar, "Humanoid", 3)
    myHRP = FWC(mychar, "HumanoidRootPart", 3)
    myHead = FWC(mychar, "Head", 3)
    
    last_pos = targetHRP.Position
    BackUp = myHRP.CFrame
    
    function Stop(Back)
        myHRP.CFrame = Back
        for _, prt in pairs(mychar:GetChildren()) do
            if prt:IsA("BasePart") then
                prt.Velocity = Vector3.new()
            end
        end
        tpTimer = 0
    end
    
    task.spawn(function()
        while (not (targetHead:FindFirstChild("PartOwner")) or targetHead:FindFirstChild("PartOwner").Value ~= LocalPlayer.Name) and task.wait(0.1) do
            if last_pos ~= targetHRP.Position then
                last_pos = targetHRP.Position
            end
        end
    end)
    
    while not (targetHead:FindFirstChild("PartOwner")) or (targetHead:FindFirstChild("PartOwner") and targetHead:FindFirstChild("PartOwner").Value ~= LocalPlayer.Name) do
        if targetHRP and targetHRP.Parent and myhum.Health ~= 0 and targetHum.Health ~= 0 then
            if tpTimer < 74 and not (targetPlayer.InPlot.Value) then
                tpTimer = tpTimer + 1
                if not (LocalPlayer.IsHeld.Value) then
                    myHRP.CFrame = targetHRP.CFrame + ((targetHRP.Position - last_pos) * LocalPlayer:GetNetworkPing() * 25) + Vector3.new(0, -3.5, 0)
                    task.spawn(grab, targetHRP)
                    task.wait()
                else
                    while myHead:FindFirstChild("PartOwner") do
                        Struggle:FireServer(LocalPlayer)
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
    
    waterPos = Vector3.new(320, -61, 423)
    
    task.defer(function()
        startTime = tick()
        while targetHead and targetHead.Parent and (targetHead:FindFirstChild("PartOwner") and targetHead:FindFirstChild("PartOwner").Value == LocalPlayer.Name) and tick() - startTime < 5 do
            if not targetHead.BallSocketConstraint.Enabled then
                targetHRP.CFrame = CFrame.new(waterPos)
                for _, prt in pairs(targetChar:GetChildren()) do
                    if prt:IsA("Part") then
                        prt.Velocity = Vector3.new()
                        prt.CanTouch = false
                    end
                end
            else
                for _, prt in pairs(targetChar:GetChildren()) do
                    if prt:IsA("Part") then
                        prt.CFrame = CFrame.new(waterPos)
                        prt.Velocity = Vector3.new()
                        prt.CanTouch = false
                    end
                end
            end
            task.wait()
        end
    end)
    
    task.wait(0)
    if GrabFlag == "Default" then
        Destroy_Line(targetHRP)
    end
    tpTimer = 0
end

blobFreezeLoop = false
 blobFreezeTarget = nil
 TargetPosition = Vector3.new(-480, -36, -119)


function findBlobman()
    local toys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    return toys and toys:FindFirstChild("CreatureBlobman") or nil
end

function spawnBlobman()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    SpawnToyRF:InvokeServer("CreatureBlobman", char.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5), Vector3.new(0, -15, 0))
end

function ensureBlobman()
    local b = findBlobman()
    if b then return b end
    spawnBlobman()
    for _ = 1, 30 do
        task.wait(0.01)
        b = findBlobman()
        if b then return b end
    end
    return nil
end

function getBlobman()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        local humanoid = LocalPlayer.Character.Humanoid
        for _, v in pairs(Workspace:GetDescendants()) do
            if v.Name == "BlobmanSeatAndOwnerScript" and v.Parent.Name == "CreatureBlobman" then
                if not humanoid.SeatPart then
                    local seat = v.Parent:FindFirstChildWhichIsA("VehicleSeat")
                    if seat then
                        humanoid.Sit = true
                        seat:Sit(humanoid)
                        task.wait(0.1)
                    end
                end
                if humanoid.SeatPart and humanoid.SeatPart.Parent == v.Parent then
                    return v.Parent
                end
            end
        end
    end
    return nil
end

function isOnBlobman()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        local humanoid = LocalPlayer.Character.Humanoid
        if humanoid.SeatPart and humanoid.SeatPart.Parent and humanoid.SeatPart.Parent.Name == "CreatureBlobman" then
            return true
        end
    end
    return false
end

function getDetector(blob, side)
    local detector = blob:FindFirstChild(side.."Detector")
    local weld = detector and detector:FindFirstChild(side.."Weld")
    return detector, weld
end

function inPlot(player)
    local plots = Workspace:FindFirstChild("PlotItems")
    local playersInPlots = plots and plots:FindFirstChild("PlayersInPlots")
    return playersInPlots and playersInPlots:FindFirstChild(player.Name)
end

function ProcessBlobFreeze(targetName)
    local targetPlayer = Players:FindFirstChild(targetName)
    if not targetPlayer then return false, "Player not found" end
    
    if inPlot(targetPlayer) then return false, "Player is in a plot" end
    
    local targetChar = targetPlayer.Character
    if not targetChar then return false, "Player has no character" end
    
    local blob = ensureBlobman()
    if not blob then return false, "Failed to spawn blobman" end
    
    if not isOnBlobman() then
        local currentBlob = getBlobman()
        if not currentBlob then return false, "Failed to mount blobman" end
        blob = currentBlob
    end
    
    local localChar = LocalPlayer.Character
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    if not localChar or not localChar:FindFirstChild("HumanoidRootPart") or not targetHRP then
        return false, "Failed to get character parts"
    end
    
    localChar.HumanoidRootPart.CFrame = targetHRP.CFrame * CFrame.new(0, 0, 5)
    task.wait(0.3)
    
    local detector, weld = getDetector(blob, "Right")
    if not (detector and weld) then return false, "Failed to get blobman parts" end
    
    local currentGrab = blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab")
    if not currentGrab then return false, "Failed to get CreatureGrab" end
    
    currentGrab:FireServer(blob.RightDetector, targetHRP, weld)
    task.wait(0.02)
    
    for _, part in ipairs(targetChar:GetChildren()) do
        if part:IsA("BasePart") then
            blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(weld, part)
            part.AssemblyLinearVelocity = Vector3.new(0, 15, 0)
            blob.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(weld, part)
        end
    end
    
    local drop = blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureDrop")
    if drop then
        for _, part in ipairs(targetChar:GetDescendants()) do
            if part:IsA("Weld") or part:IsA("BallSocketConstraint") then
                drop:FireServer(part, part)
            end
        end
    end
    
    task.wait(0.1)
    
    if targetChar and targetChar:FindFirstChild("HumanoidRootPart") then
        targetChar.HumanoidRootPart.CFrame = CFrame.new(TargetPosition)
        targetChar.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
        targetChar.HumanoidRootPart.AssemblyAngularVelocity = Vector3.zero
        
        for _, part in pairs(targetChar:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Velocity = Vector3.zero
                part.RotVelocity = Vector3.zero
            end
        end
        
        task.wait(0.5)
        
        if targetChar:FindFirstChild("HumanoidRootPart") then
            targetChar.HumanoidRootPart.Anchored = true
        end
    end
    
    return true, "Success"
end

function BlobFreezeLoopFunc()
    while blobFreezeLoop do
        if blobFreezeTarget then
            ProcessBlobFreeze(blobFreezeTarget)
        end
        task.wait(0.5)
    end
end

 heartHighRun = false
heartTarget = LocalPlayer.Name 
heartConnection = nil
 heartToy = nil

function ToggleHeartSparkler(targetName, enable)
    heartHighRun = enable
    
    if targetName then
        heartTarget = targetName
    end
    
    if enable then
        task.spawn(function()
            local targetPlayer = Players:FindFirstChild(heartTarget)
            if not targetPlayer then
                heartTarget = LocalPlayer.Name
                targetPlayer = LocalPlayer
            end
            
            if not targetPlayer.Character then return end
            local hrp = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
            if not hrp then return end

            pcall(function()
                SpawnToyRF:InvokeServer("FireworkSparkler", hrp.CFrame * CFrame.new(0, 50, 0), Vector3.zero)
            end)

            local folderName = LocalPlayer.Name .. "SpawnedInToys"
            local folder = Workspace:WaitForChild(folderName, 5)
            if not folder then return end
            
            heartToy = folder:WaitForChild("FireworkSparkler", 5)
            if not heartToy then return end
            
            local part = heartToy:FindFirstChild("Handle") or heartToy:FindFirstChildWhichIsA("BasePart")
            if not part then return end

            task.wait(0.2)
            
            for _, v in pairs(heartToy:GetDescendants()) do
                if v:IsA("BasePart") then
                    v.Anchored = false
                    v.CanCollide = false
                    v.Massless = true
                end
            end
            part:BreakJoints()

            local bp = Instance.new("BodyPosition")
            bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bp.P = 20000
            bp.D = 500
            bp.Parent = part

            local bg = Instance.new("BodyGyro")
            bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
            bg.P = 3000
            bg.CFrame = CFrame.new()
            bg.Parent = part

            local t = 0
            
            if heartConnection then heartConnection:Disconnect() end
            
            heartConnection = RunService.Heartbeat:Connect(function(dt)
                if not heartHighRun or not part or not part.Parent then
                    if heartConnection then heartConnection:Disconnect() end
                    if heartToy then pcall(function() heartToy:Destroy() end) end
                    return
                end

                local targetPlayer = Players:FindFirstChild(heartTarget)
                if not targetPlayer or not targetPlayer.Character then 
                    heartTarget = LocalPlayer.Name
                    targetPlayer = LocalPlayer
                end
                
                local currentHrp = targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart")
                if not currentHrp then return end

                pcall(function()
                    RemoteSetNetworkOwner:FireServer(part, part.CFrame)
                end)

                t = t + (8 * dt)
                
                local scale = 1.5
                local x = 16 * math.sin(t)^3
                local y = 13 * math.cos(t) - 5 * math.cos(2*t) - 2 * math.cos(3*t) - math.cos(4*t)

                local relPos = Vector3.new(x * scale, (y * scale) + 3, 0) 
                local finalPos = currentHrp.CFrame:PointToWorldSpace(relPos)

                bp.Position = finalPos
                bg.CFrame = currentHrp.CFrame
            end)
        end)
    else
        if heartConnection then 
            heartConnection:Disconnect() 
            heartConnection = nil 
        end
        if heartToy then 
            pcall(function() heartToy:Destroy() end) 
            heartToy = nil 
        end
    end
end

tptopoisonLoop = false
 tptopoisonTarget = nil
 PoisonPosition = Vector3.new(36, -68, 269)

function TPToPoison(targetName)
    local targetPlayer = Players:FindFirstChild(targetName)
    if not targetPlayer then return false, "Player not found" end
    
    local targetChar = targetPlayer.Character
    if not targetChar then return false, "Player has no character" end
    
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    local targetHum = targetChar:FindFirstChild("Humanoid")
    if not targetHRP or not targetHum then return false, "Invalid character" end
    
    if targetHum.Health <= 0 then return false, "Player is dead" end
    if targetPlayer.InPlot.Value then return false, "Player in safe zone" end
    
    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local myHRP = mychar:FindFirstChild("HumanoidRootPart")
    local myHum = mychar:FindFirstChild("Humanoid")
    if not myHRP or not myHum then return false, "Your character invalid" end
    
    local BackUp = myHRP.CFrame
    local success = false

for i = 1, 30 do
        if not targetHRP or not targetHRP.Parent then break end
        if myHum.Health <= 0 then break end
        
        myHRP.CFrame = targetHRP.CFrame + Vector3.new(0, -3, 0)
        grab(targetHRP)
        
        if targetChar.Head:FindFirstChild("PartOwner") and targetChar.Head.PartOwner.Value == LocalPlayer.Name then
            success = true
            break
        end
        
        task.wait(0.1)
    end
    
    if not success then
        myHRP.CFrame = BackUP
        return false, "Could not grab player"
    end

myHRP.CFrame = BackUp
    
    task.defer(function()
        local startTime = tick()
        while targetChar and targetChar.Parent and targetChar.Head:FindFirstChild("PartOwner") and 
              targetChar.Head.PartOwner.Value == LocalPlayer.Name and tick() - startTime < 5 do
            targetHRP.CFrame = CFrame.new(PoisonPosition)
            targetHRP.Velocity = Vector3.new()
            task.wait()
        end
        RemoteDestroyGrabLine:FireServer(targetHRP)
    end)
    
    return true, "Success"
end


function TPPoisonLoopFunc()
    while tptopoisonLoop do
        if tptopoisonTarget then
            TPToPoison(tptopoisonTarget)
        end
        task.wait(1)
    end
end

PoisonPosition = Vector3.new(36, -68, 269)
BringAllActive = false
BringAllLoopActive = false


function BringPlayerToPoison(playerName)
    local targetPlayer = Players:FindFirstChild(playerName)
    if not targetPlayer or not targetPlayer.Character then return false end
    
    local targetChar = targetPlayer.Character
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    local targetHum = targetChar:FindFirstChild("Humanoid")
    
    if not targetHRP or not targetHum then return false end
    
    if targetHum.Health <= 0 then return false end
    if targetPlayer.InPlot.Value then return false end
    
    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local myHRP = mychar:FindFirstChild("HumanoidRootPart")
    local myHum = mychar:FindFirstChild("Humanoid")
    
    if not myHRP or not myHum then return false end
    
    local BackUp = myHRP.CFrame
    local success = false
    
    for i = 1, 45 do
        if not targetHRP or not targetHRP.Parent then break end
        if myHum.Health <= 0 then break end
        
        myHRP.CFrame = targetHRP.CFrame + Vector3.new(0, -3, 0)
        grab(targetHRP)
        
        if targetChar.Head:FindFirstChild("PartOwner") and targetChar.Head.PartOwner.Value == LocalPlayer.Name then
            success = true
            break
        end
        
        task.wait(0)
    end
    
    if not success then
        myHRP.CFrame = BackUp
        return false
    end
    
    myHRP.CFrame = BackUp
    
    task.defer(function()
        local startTime = tick()
        while targetChar and targetChar.Parent and targetChar.Head:FindFirstChild("PartOwner") and 
              targetChar.Head.PartOwner.Value == LocalPlayer.Name and tick() - startTime < 5 do
            
            targetHRP.CFrame = CFrame.new(PoisonPosition)
            targetHRP.Velocity = Vector3.new()
            
            task.wait()
        end
        RemoteDestroyGrabLine:FireServer(targetHRP)
    end)
    
    return true
end

function BringAllToPoison()
    local successCount = 0
    local failCount = 0
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local success = BringPlayerToPoison(player.Name)
            if success then
                successCount = successCount + 1
            else
                failCount = failCount + 1
            end
            task.wait(0)
        end
    end
    
    return successCount, failCount
end

function BringAllLoop()
    while BringAllLoopActive do
        BringAllToPoison()
        task.wait(0) 
    end
end

WaterPosition = Vector3.new(327, 26, 699)
BringAllActive2 = false
BringAllLoop2Active = false


function BringPlayerToWater(playerName)
    local targetPlayer = Players:FindFirstChild(playerName)
    if not targetPlayer or not targetPlayer.Character then return false end
    
    local targetChar = targetPlayer.Character
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    local targetHum = targetChar:FindFirstChild("Humanoid")
    
    if not targetHRP or not targetHum then return false end
    
    if targetHum.Health <= 0 then return false end
    if targetPlayer.InPlot.Value then return false end
    
    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local myHRP = mychar:FindFirstChild("HumanoidRootPart")
    local myHum = mychar:FindFirstChild("Humanoid")
    
    if not myHRP or not myHum then return false end
    
    local BackUp = myHRP.CFrame
    local success = false
    
    for i = 1, 45 do
        if not targetHRP or not targetHRP.Parent then break end
        if myHum.Health <= 0 then break end
        
        myHRP.CFrame = targetHRP.CFrame + Vector3.new(0, -3, 0)
        grab(targetHRP)
        
        if targetChar.Head:FindFirstChild("PartOwner") and targetChar.Head.PartOwner.Value == LocalPlayer.Name then
            success = true
            break
        end
        
        task.wait(0)
    end
    
    if not success then
        myHRP.CFrame = BackUp
        return false
    end
    
    myHRP.CFrame = BackUp
    
    task.defer(function()
        local startTime = tick()
        while targetChar and targetChar.Parent and targetChar.Head:FindFirstChild("PartOwner") and 
              targetChar.Head.PartOwner.Value == LocalPlayer.Name and tick() - startTime < 5 do
            
            targetHRP.CFrame = CFrame.new(PoisonPosition)
            targetHRP.Velocity = Vector3.new()
            
            task.wait()
        end
        RemoteDestroyGrabLine:FireServer(targetHRP)
    end)
    
    return true
end

function BringAllToWater()
    local successCount = 0
    local failCount = 0
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local success = BringPlayerToPoison(player.Name)
            if success then
                successCount = successCount + 1
            else
                failCount = failCount + 1
            end
            task.wait(0)
        end
    end
    
    return successCount, failCount
end

function BringAllLoop2()
    while BringAllLoop2Active do
        BringAllToWater()
        task.wait(0) 
    end
end

function Destroy_Line(Part)
    if not Part then return end
    for _, v in pairs(Part.Parent:GetDescendants()) do
        if v.Name == "PartOwner" then
            RemoteDestroyGrabLine:FireServer(v.Parent)
        end
    end
end

 PCldEnabled = {}
 PCldBoxes = {}

function CreatePCld(player)
    if PCldBoxes[player] then return end
    
    local character = player.Character
    if not character then return end
    
    local hrp = character:FindFirstChild("HumanoidRootPart") or character.PrimaryPart
    if not hrp then return end
    
    local box = Instance.new("BoxHandleAdornment")
    box.Name = player.Name .. "_PCld"
    box.Adornee = hrp
    box.AlwaysOnTop = true
    box.ZIndex = 10
    box.Size = Vector3.new(4, 6, 2)
    box.Color3 = Color3.fromRGB(255, 0, 255) -- Magenta
    box.Transparency = 0.3
    box.Parent = hrp
    
    local billboard = Instance.new("BillboardGui")
    billboard.Name = player.Name .. "_PCld_Text"
    billboard.Adornee = hrp
    billboard.Size = UDim2.new(0, 200, 0, 50)
    billboard.StudsOffset = Vector3.new(0, 4, 0)
    billboard.AlwaysOnTop = true
    billboard.Parent = hrp
    
    local textLabel = Instance.new("TextLabel")
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.Text = "[PCld] " .. player.Name
    textLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
    textLabel.Font = Enum.Font.GothamBold
    textLabel.TextSize = 16
    textLabel.TextStrokeTransparency = 0.3
    textLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
    textLabel.Parent = billboard
    
    local outline = Instance.new("SelectionBox")
    outline.Name = player.Name .. "_PCld_Outline"
    outline.Adornee = hrp
    outline.Color3 = Color3.fromRGB(255, 0, 255)
    outline.Transparency = 0.7
    outline.LineThickness = 0.05
    outline.Parent = hrp
    
    PCldBoxes[player] = {box = box, billboard = billboard, outline = outline}
end

function RemovePCld(player)
    if PCldBoxes[player] then
        if PCldBoxes[player].box then
            PCldBoxes[player].box:Destroy()
        end
        if PCldBoxes[player].billboard then
            PCldBoxes[player].billboard:Destroy()
        end
        if PCldBoxes[player].outline then
            PCldBoxes[player].outline:Destroy()
        end
        PCldBoxes[player] = nil
    end
end

function UpdatePCld()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if table.find(PCldEnabled, player.Name) then
                if player.Character then
                    CreatePCld(player)
                else
                    RemovePCld(player)
                end
            else
                RemovePCld(player)
            end
        end
    end
end


function TogglePCld(playerName, enable)
    local player = Players:FindFirstChild(playerName)
    if not player then return false, "Player not found" end
    
    if enable then
        if not table.find(PCldEnabled, playerName) then
            table.insert(PCldEnabled, playerName)
            if player.Character then
                CreatePCld(player)
            end
            return true, "PCld enabled for " .. playerName
        end
    else
        local index = table.find(PCldEnabled, playerName)
        if index then
            table.remove(PCldEnabled, index)
            RemovePCld(player)
            return true, "PCld disabled for " .. playerName
        end
    end
    return false
end

task.spawn(function()
    while true do
        UpdatePCld()
        task.wait(0.5)
    end
end)


function MonitorPlayerRespawns()
    local connections = {}
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local conn = player.CharacterAdded:Connect(function()
                if table.find(PCldEnabled, player.Name) then
                    task.wait(0.5) 
                    CreatePCld(player)
                end
            end)
            table.insert(connections, conn)
        end
    end
    
    Players.PlayerAdded:Connect(function(player)
        if player ~= LocalPlayer then
            local conn = player.CharacterAdded:Connect(function()
                if table.find(PCldEnabled, player.Name) then
                    task.wait(0.5)
                    CreatePCld(player)
                end
            end)
            table.insert(connections, conn)
        end
    end)
    
    Players.PlayerRemoving:Connect(function(player)
        RemovePCld(player)
    end)
end

task.spawn(MonitorPlayerRespawns)

CameraAnchor = {}
CameraAnchor.__index = CameraAnchor
function CameraAnchor.new() return setmetatable({}, CameraAnchor) end
function CameraAnchor:attach(cf)
    self:detach()
    p = Instance.new("Part")
    p.Name, p.Size, p.Transparency, p.Anchored, p.CanCollide, p.CFrame, p.Parent =
        "CameraAnchor", Vector3.new(0.2, 0.2, 0.2), 1, true, false, cf, Workspace
    self.part = p
    cam = Workspace.CurrentCamera
    cam.CameraType = Enum.CameraType.Custom
    cam.CameraSubject = p
end
function CameraAnchor:detach()
    if self.part then self.part:Destroy() self.part = nil end
    cam = Workspace.CurrentCamera
    char = LocalPlayer.Character
    if char and char:FindFirstChild("Humanoid") then
        cam.CameraSubject = char.Humanoid
    else
        cam.CameraType = Enum.CameraType.Custom
        cam.CameraSubject = cam
    end
end
cameraAnchor = CameraAnchor.new()

function saveOriginalPosAttr()
    char = LocalPlayer.Character
    hrp = char and char:FindFirstChild("HumanoidRootPart")
    if hrp then
        char:SetAttribute("OriginalPosition", hrp:GetPivot())
    end
end

function getOriginalPosAttr()
    char = LocalPlayer.Character
    return char and char:GetAttribute("OriginalPosition") or nil
end

function initCharAttrs()
    char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char:SetAttribute("OriginalPosition", char.HumanoidRootPart:GetPivot())
        char:SetAttribute("SavingOriginalPos", false)
    end
end

function scheduleReturnHome()
    originalPos = getOriginalPosAttr()
    if not originalPos then return end
    
    conn = nil
    conn = RunService.Heartbeat:Connect(function()
        char = LocalPlayer.Character
        hrp = char and char:FindFirstChild("HumanoidRootPart")
        if hrp then
            hrp:PivotTo(originalPos)
            if getgenv().originalFallenHeight then
                Workspace.FallenPartsDestroyHeight = getgenv().originalFallenHeight
            end
            char:SetAttribute("SavingOriginalPos", false)
        end
        cameraAnchor:detach()
        conn:Disconnect()
    end)
end

function modifyTarget(root, hum)
    if not (root and hum) or hum.Health <= 0 then return end
    hum.Sit = false
    hum:ChangeState(Enum.HumanoidStateType.Running)
    hum:SetStateEnabled(Enum.HumanoidStateType.Seated, false)
    hum:ChangeState(Enum.HumanoidStateType.GettingUp)

    plr = Players:GetPlayerFromCharacter(hum.Parent)
    if plr and plr:FindFirstChild("IsHeld") then plr.IsHeld.Value = false end
    rag = hum:FindFirstChild("Ragdolled")
    if rag then rag.Value = false end

    bv, bav = Instance.new("BodyVelocity"), Instance.new("BodyAngularVelocity")
    bv.MaxForce = Vector3.new(1e7, -1e7, 1e7)
    bv.P = 100
    bv.Velocity = Vector3.new(math.random(-500, 50), -50, math.random(-50, 50))
    bav.MaxTorque = Vector3.new(-1e7, -1e7, -1e7)
    bav.P = 1e6
    bav.AngularVelocity = Vector3.new(math.random(-500, 300), math.random(-300, 300), math.random(-500, 500))
    bv.Parent, bav.Parent = root, root

    hum.BreakJointsOnDeath = false
    hum:ChangeState(Enum.HumanoidStateType.Dead)
    hum.RigType = Enum.HumanoidRigType.R15

    task.delay(0.1, function()
        if bv.Parent then bv:Destroy() end
        if bav.Parent then bav:Destroy() end
    end)
end

function performKill()
    if not SelectedPlayer then return end
    
    target = Players:FindFirstChild(SelectedPlayer)
    tChar = target and target.Character
    tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
    tHum = tChar and tChar:FindFirstChild("Humanoid")
    tHead = tChar and tChar:FindFirstChild("Head")
    
    if not (target and tRoot and tHum and tHead) then return end
    if isTooHigh(target) then return end
    if target:FindFirstChild("InPlot") and target.InPlot.Value then return end
    if tHum:GetState() == Enum.HumanoidStateType.Dead then return end

    char = LocalPlayer.Character
    hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not (char and hrp) then return end

    if not char:GetAttribute("SavingOriginalPos") then
        saveOriginalPosAttr()
    end
    char:SetAttribute("SavingOriginalPos", true)

    getgenv().originalFallenHeight = Workspace.FallenPartsDestroyHeight
    Workspace.FallenPartsDestroyHeight = 0/0

    originalPos = getOriginalPosAttr()
    if originalPos then
        cameraAnchor:attach(originalPos)
    end

    desiredCFrame = CFrame.new(tRoot.Position + TELEPORT_OFFSET)
    hrp:PivotTo(desiredCFrame)

    setNoCollideChar(tChar)
    
    for i = 5, 10 do
        RemoteSetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
        task.wait()
    end
    
    RemoteDestroyGrabLine:FireServer(tRoot)
    task.wait()

    if tHead:FindFirstChild("PartOwner") and tHead.PartOwner.Value == LocalPlayer.Name then
        modifyTarget(tRoot, tHum)
    end

    scheduleReturnHome()
end

function StartLoopKill()
    if KillHB then KillHB:Disconnect() end
    LoopKillOn = true
    KillHB = RunService.Heartbeat:Connect(function()
        if LoopKillOn then
            performKill()
        end
    end)
end

function StopLoopKill()
    LoopKillOn = false
    if KillHB then KillHB:Disconnect() KillHB = nil end
    cameraAnchor:detach()
end

function FindPlayerByPartialName(partial)
    partial = string.lower(partial)
    for _, player in pairs(Players:GetPlayers()) do
        playerNameLower = string.lower(player.Name)
        playerDisplayLower = string.lower(player.DisplayName)
        if string.find(playerNameLower, partial, 1, true) or string.find(playerDisplayLower, partial, 1, true) then
            return player.Name
        end
    end
    return nil
end


LocalPlayer.Chatted:Connect(function(message)
    if IsAllowed(LocalPlayer.Name) then
        msgLower = string.lower(message)
        args = string.split(message, " ")
        
        if string.sub(msgLower, 1, 9) == "!blobkick" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                local success = BlobKickPlayer(targetName, false)
                Rayfield:Notify({
                    Title = success and "Blobkick Success" or "Blobkick Failed",
                    Content = success and "Blobkicked " .. targetName or "Failed to blobkick " .. targetName,
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
            
             elseif string.sub(msgLower, 1, 9) == "!bringall" then
            Rayfield:Notify({
                Title = "Bring All Started",
                Content = "Bringing all players to poison...",
                Duration = 3,
                Image = 7743876054,
            })
            
            local successCount, failCount = BringAllToPoison()
            
            Rayfield:Notify({
                Title = "Bring All Complete",
                Content = "Success: " .. successCount .. " | Failed: " .. failCount,
                Duration = 5,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 13) == "!loopbringall" then
            BringAllLoopActive = true
            task.spawn(BringAllLoop)
            Rayfield:Notify({
                Title = "Loop Bring All Started",
                Content = "Loop bringing all players to poison",
                Duration = 3,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 15) == "!unloopbringall" then
            BringAllLoopActive = false
            Rayfield:Notify({
                Title = "Loop Bring All Stopped",
                Content = "Stopped loop bringing all",
                Duration = 3,
                Image = 7743876054,
            })
        
       elseif string.sub(msgLower, 1, 5) == "!kill" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                performKill()
                Rayfield:Notify({
                    Title = "Command Executed",
                    Content = "Killing " .. targetName,
                    Duration = 3,
                    Image = 7743876054,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        elseif string.sub(msgLower, 1, 9) == "!loopkill" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                StartLoopKill()
                Rayfield:Notify({
                    Title = "Command Executed",
                    Content = "Loop killing " .. targetName,
                    Duration = 3,
                    Image = 7743876054,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        elseif string.sub(msgLower, 1, 11) == "!unloopkill" then
            StopLoopKill()
            Rayfield:Notify({
                Title = "Command Executed",
                Content = "Stopped loop kill",
                Duration = 3,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 5) == "!pcld" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                local success, message = TogglePCld(targetName, true)
                Rayfield:Notify({
                    Title = success and "PCld Enabled" or "PCld Error",
                    Content = message,
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 7) == "!nopcld" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                local success, message = TogglePCld(targetName, false)
                Rayfield:Notify({
                    Title = success and "PCld Disabled" or "PCld Error",
                    Content = message,
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 9) == "!bringalltowater" then
            Rayfield:Notify({
                Title = "Bring All Started",
                Content = "Bringing all players to water...",
                Duration = 3,
                Image = 7743876054,
            })
            
            local successCount, failCount = BringAllToWater()
            
            Rayfield:Notify({
                Title = "Bring All Complete",
                Content = "Success: " .. successCount .. " | Failed: " .. failCount,
                Duration = 5,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 13) == "!loopbringtowaterall" then
            BringAllLoop2Active = true
            task.spawn(BringAllLoop2)
            Rayfield:Notify({
                Title = "Loop Bring All Started",
                Content = "Loop bringing all players to water",
                Duration = 3,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 15) == "!unloopbringtowaterall" then
            BringAllLoop2Active = false
            Rayfield:Notify({
                Title = "Loop Bring All Stopped",
                Content = "Stopped loop bringing all",
                Duration = 3,
                Image = 7743876054,
            })

elseif string.sub(msgLower, 1, 6) == "!heart" then
            local targetName = LocalPlayer.Name 
            
            if #args >= 2 then
                targetName = FindPlayerByPartialName(args[2])
                if not targetName then
                    Rayfield:Notify({
                        Title = "Error",
                        Content = "Player not found: " .. args[2],
                        Duration = 3,
                        Image = 7743878857,
                    })
                    return
                end
            end
            
            ToggleHeartSparkler(targetName, not heartHighRun)
            Rayfield:Notify({
                Title = "Heart " .. (not heartHighRun and "Enabled" or "Disabled"),
                Content = "Heart sparkler " .. (not heartHighRun and "activated on " .. targetName or "deactivated"),
                Duration = 3,
                Image = 7743876054,
            })
        elseif string.sub(msgLower, 1, 9) == "!loopheart" then
            local targetName = LocalPlayer.Name
            
            if #args >= 2 then
                targetName = FindPlayerByPartialName(args[2])
                if not targetName then
                    Rayfield:Notify({
                        Title = "Error",
                        Content = "Player not found: " .. args[2],
                        Duration = 3,
                        Image = 7743878857,
                    })
                    return
                end
            end
            
            if not heartHighRun then
                ToggleHeartSparkler(targetName, true)
                Rayfield:Notify({
                    Title = "Heart Enabled",
                    Content = "Heart sparkler activated on " .. targetName .. " (loop)",
                    Duration = 3,
                    Image = 7743876054,
                })
            end
        elseif string.sub(msgLower, 1, 11) == "!unloopheart" then
            if heartHighRun then
                ToggleHeartSparkler(nil, false)
                Rayfield:Notify({
                    Title = "Heart Disabled",
                    Content = "Heart sparkler deactivated",
                    Duration = 3,
                    Image = 7743876054,
                })
            end
        elseif string.sub(msgLower, 1, 11) == "!tptopoison" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                local success, message = TPToPoison(targetName)
                Rayfield:Notify({
                    Title = success and " TP Poison Success" or " TP Poison Failed",
                    Content = success and "TP " .. targetName .. " to poison" or "Failed: " .. message,
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
            elseif string.sub(msgLower, 1, 15) == "!looptptopoison" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                tptopoisonTarget = targetName
                tptopoisonLoop = true
                task.spawn(TPPoisonLoopFunc)
                Rayfield:Notify({
                    Title = "Loop TP Poison Started",
                    Content = "Loop TP " .. targetName .. " to poison",
                    Duration = 3,
                    Image = 7743876054,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 17) == "!unlooptptopoison" then
            tptopoisonLoop = false
            tptopoisonTarget = nil
            Rayfield:Notify({
                Title = "Command Executed",
                Content = "Stopped loop TP poison",
                Duration = 3,
                Image = 7743876054,
            })
            
            elseif string.sub(msgLower, 1, 10) == "!tptowater" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                TPPlayerToWater(targetName)
                Rayfield:Notify({
                    Title = "Command Executed",
                    Content = "TP " .. targetName .. " to water",
                    Duration = 3,
                    Image = 7743876054,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
            
            elseif string.sub(msgLower, 1, 11) == "!blobfreeze" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                local success, message = ProcessBlobFreeze(targetName)
                Rayfield:Notify({
                    Title = success and " BlobFreeze Success" or " BlobFreeze Failed",
                    Content = success and "Froze " .. targetName or "Failed: " .. message,
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 15) == "!loopblobfreeze" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                blobFreezeTarget = targetName
                blobFreezeLoop = true
                task.spawn(BlobFreezeLoopFunc)
                Rayfield:Notify({
                    Title = " Loop BlobFreeze Started",
                    Content = "Loop freezing " .. targetName,
                    Duration = 3,
                    Image = 7743876054,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end

        elseif string.sub(msgLower, 1, 17) == "!unloopblobfreeze" then
            blobFreezeLoop = false
            blobFreezeTarget = nil
            Rayfield:Notify({
                Title = " Command Executed",
                Content = "Stopped loop blobfreeze",
                Duration = 3,
                Image = 7743876054,
            })
            
            elseif string.sub(msgLower, 1, 13) == "!switchplayer" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                SwitchPlayer()
                Rayfield:Notify({
                    Title = "Command Executed",
                    Content = "Switching with " .. targetName,
                    Duration = 3,
                    Image = 7743876054,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        elseif string.sub(msgLower, 1, 13) == "!loopblobkick" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                local success = BlobKickPlayer(targetName, true)
                Rayfield:Notify({
                    Title = success and "Loop Blobkick Started" or "Loop Blobkick Failed",
                    Content = success and "Loop blobkicking " .. targetName or "Failed to start loop blobkick",
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 15) == "!unloopblobkick" then
            LoopBlobkickActive = false
            BlobkickList = {}
            Rayfield:Notify({
                Title = "Command Executed",
                Content = "Stopped all loop blobkicks",
                Duration = 3,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 7) == "!pencil" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                local success = PencilPlayer(targetName, false)
                Rayfield:Notify({
                    Title = success and "Pencil Success" or "Pencil Failed",
                    Content = success and "Penciled " .. targetName or "Failed to pencil " .. targetName,
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 11) == "!looppencil" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                local success = PencilPlayer(targetName, true)
                Rayfield:Notify({
                    Title = success and "Loop Pencil Started" or "Loop Pencil Failed",
                    Content = success and "Loop penciling " .. targetName or "Failed to start loop pencil",
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 13) == "!unlooppencil" then
            LoopPencilActive = false
            PencilList = {}
            Rayfield:Notify({
                Title = "Command Executed",
                Content = "Stopped all loop pencils",
                Duration = 3,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 6) == "!bring" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                BringPlayer(targetName, false)
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 10) == "!loopbring" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
if targetName then
SelectedPlayer = targetName
LoopBringActive = true
BringPlayer(targetName, true)
else
Rayfield:Notify({
Title = "Error",
Content = "Player not found: " .. args[2],
Duration = 3,
Image = 7743878857,
})
end
    elseif string.sub(msgLower, 1, 12) == "!unloopbring" then
        LoopBringActive = false
        Rayfield:Notify({
            Title = "Command Executed",
            Content = "Stopped loop bring",
            Duration = 3,
            Image = 7743876054,
        })
    
    elseif string.sub(msgLower, 1, 5) == "!bang" and #args >= 2 then
        targetName = FindPlayerByPartialName(args[2])
        if targetName then
            SelectedPlayer = targetName
            BangPlayer(targetName, false)
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Player not found: " .. args[2],
                Duration = 3,
                Image = 7743878857,
            })
        end
    
    elseif string.sub(msgLower, 1, 9) == "!loopbang" and #args >= 2 then
        targetName = FindPlayerByPartialName(args[2])
        if targetName then
            SelectedPlayer = targetName
            LoopBangActive = true
            BangPlayer(targetName, true)
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Player not found: " .. args[2],
                Duration = 3,
                Image = 7743878857,
            })
        end
    
    elseif string.sub(msgLower, 1, 11) == "!unloopbang" then
        LoopBangActive = false
        Rayfield:Notify({
            Title = "Command Executed",
            Content = "Stopped loop bang",
            Duration = 3,
            Image = 7743876054,
        })
    
    elseif string.sub(msgLower, 1, 4) == "!esp" and #args >= 2 then
        targetName = FindPlayerByPartialName(args[2])
        if targetName then
            local success, msg = ToggleESP(targetName, true)
            Rayfield:Notify({
                Title = success and "ESP Enabled" or "ESP Error",
                Content = msg,
                Duration = 3,
                Image = success and 7743876054 or 7743878857,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Player not found: " .. args[2],
                Duration = 3,
                Image = 7743878857,
            })
        end
    
    elseif string.sub(msgLower, 1, 6) == "!noesp" and #args >= 2 then
        targetName = FindPlayerByPartialName(args[2])
        if targetName then
            local success, msg = ToggleESP(targetName, false)
            Rayfield:Notify({
                Title = success and "ESP Disabled" or "ESP Error",
                Content = msg,
                Duration = 3,
                Image = success and 7743876054 or 7743878857,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Player not found: " .. args[2],
                Duration = 3,
                Image = 7743878857,
            })
        end
    end
end
end)


function optionText(plr)
return string.format("ðŸ‘¤ @%s (%s)", plr.Name, plr.DisplayName or plr.Name)
end

function refreshDropdown()
local opts = {}
for _, p in ipairs(Players:GetPlayers()) do
if p ~= LocalPlayer then
table.insert(opts, optionText(p))
end
end
PlayerDropdown:Refresh(opts)
end

Players.PlayerAdded:Connect(function()
task.wait(0.5)
refreshDropdown()
end)

Players.PlayerRemoving:Connect(function()
task.wait(0.5)
refreshDropdown()
end)

refreshDropdown()


task.spawn(function()
while true do


    if LoopPencilActive then
        for _, playerName in pairs(PencilList) do
            CheckAndManagePencil(playerName)
        end
    end
    
    task.wait(0.5)
end


end)

Players.PlayerRemoving:Connect(function(player)
playerName = player.Name

 index = table.find(PencilList, playerName)
if index then
    table.remove(PencilList, index)
end
PencilCooldown[playerName] = nil

index2 = table.find(BlobkickList, playerName)
if index2 then
    table.remove(BlobkickList, index2)
end
BlobkickCooldown[playerName] = nil

end)

task.spawn(function()
while true do
for _, playerName in pairs(PencilList) do
local player = Players:FindFirstChild(playerName)
if player and player.InPlot.Value then
local index = table.find(PencilList, playerName)
if index then
table.remove(PencilList, index)
end
end
end

    for _, playerName in pairs(BlobkickList) do
        local player = Players:FindFirstChild(playerName)
        if player and player.InPlot.Value then
            local index = table.find(BlobkickList, playerName)
            if index then
                table.remove(BlobkickList, index)
            end
        end
    end
    
    task.wait(2)
end
end)

Paragraph = CommandsTab:CreateParagraph({Title = "All cmds", Content = "!bring <player> , !loopbring <player>, !unloopbring <player> , !bringall, !loopbringall, !unloopbringall , !pencil <player> , !looppencil <player> , !unlooppencil <player> , !blobkick <player>, !loopblobkick <player> , !unloopblobkick <player> , !tptowater <player> , !switchplayer <player> , !bang <player> , !loopbang <player> , !unloopbang <player> , !heart <player>  , !loopheart <player> , !unloopheart <player> , !tptopoison <player> , !looptptopoison <player> , !unlooptptopoison , !blobfreeze  <player> ,!loopblobfreeze <player> , !unloopblobfreeze <player>"})
Paragraph = CommandsTab:CreateParagraph({Title = "How some of them works", Content = "for the !heart need to spawn 1 sparkles and grab it if doesn't works execute the command again if it spawns another one it's normal , same thing for !pencil , for the !blobfreeze spawn one blob sit on it and be sure the guy is outside a house or is not seated on a tractor blob or anything like and if u wanna do the commands again delete the blobman and spawn another one , for the !blobkick be sure the guy is outside a house so it works :°"})

TestTab = Window:CreateTab("All", 7734058599)

AuraSettings = {
    Enabled = false,
    Range = 50,
    ScanRate = 0.2,
    LiftHeight = 20,
    LiftPower = 50,
    AutoDestroy = true,
    ShowNotifications = true,
    BringAllEnabled = false,
    WhitelistFriends = false
}

 AuraState = {
    Active = false,
    TargetsInRange = {},
    CurrentBlobman = nil,
    ProcessingTarget = false,
    TimerStart = nil,
    TimerActive = false,
    BlackHoleTracking = {}
}

 BringState = {
    Active = false,
    Queue = {},
    SavedPos = nil,
    SavedCamCFrame = nil,
    Radius = 100,
    Connection = nil
}

GameEvents = {
    DestroyToy = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy"),
    SetNetworkOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"),
    DestroyGrabLine = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
}

camBlocker = Instance.new("Part", Workspace)
camBlocker.Anchored = true
camBlocker.CanCollide = false
camBlocker.Transparency = 1
camBlocker.CanQuery = false
camBlocker.Size = Vector3.new(10,10,10)

 function freezeCamera()
    local cam = Workspace.CurrentCamera
    camBlocker.CFrame = BringState.SavedCamCFrame
    camBlocker.Parent = Workspace
    cam.CameraType = Enum.CameraType.Scriptable
    cam.CFrame = BringState.SavedCamCFrame
end

function unfreezeCamera()
    camBlocker.Parent = nil
    local cam = Workspace.CurrentCamera
    cam.CameraType = Enum.CameraType.Custom
    if BringState.SavedCamCFrame then
        cam.CFrame = BringState.SavedCamCFrame
    end
    BringState.SavedCamCFrame = nil
end

function disableCollisions(character)
    for _, part in pairs(character:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = false
        end
    end
end

function inPlot(player)
    local plots = Workspace:FindFirstChild("PlotItems")
    local playersInPlots = plots and plots:FindFirstChild("PlayersInPlots")
    return playersInPlots and playersInPlots:FindFirstChild(player.Name)
end

 function inRadius(part)
    return (part.Position - BringState.SavedPos).Magnitude <= BringState.Radius
end

 function shouldIgnore(player)
    if player == LocalPlayer then return true end
    if AuraSettings.WhitelistFriends and LocalPlayer:IsFriendsWith(player.UserId) then
        return true
    end
    return false
end

 function refreshQueue()
    BringState.Queue = {}
    for _, player in pairs(Players:GetPlayers()) do
        if not shouldIgnore(player) and player.Character and not inPlot(player) then
            local root = player.Character:FindFirstChild("HumanoidRootPart")
            if root and not inRadius(root) then
                table.insert(BringState.Queue, player)
            end
        end
    end
end

 function processNext()
    if #BringState.Queue == 0 then
        refreshQueue()
        if #BringState.Queue == 0 then return end
    end
    local target = BringState.Queue[1]
    table.remove(BringState.Queue, 1)
    if not target or not target.Character then return end
    local root = target.Character:FindFirstChild("HumanoidRootPart")
    local head = target.Character:FindFirstChild("Head")
    local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if root and head and localRoot then
        LocalPlayer.Character:PivotTo(root.CFrame * CFrame.new(0, -6, 0))
        disableCollisions(LocalPlayer.Character)
        local tries = 0
        repeat
            GameEvents.SetNetworkOwner:FireServer(root, localRoot.CFrame)
            task.wait(0)
            tries = tries + 2
            if tries > 40 then break end
        until (head:FindFirstChild("PartOwner") and head.PartOwner.Value == LocalPlayer.Name) or not BringState.Active

        if BringState.Active and head:FindFirstChild("PartOwner") and head.PartOwner.Value == LocalPlayer.Name then
            root.CFrame = CFrame.new(BringState.SavedPos)
            root.Position = BringState.SavedPos
            root.AssemblyLinearVelocity = Vector3.zero
            task.wait(0)
        end
    end
end

 function startBringAll()
    local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not localRoot then return end
    BringState.SavedPos = localRoot.Position
    BringState.SavedCamCFrame = Workspace.CurrentCamera.CFrame
    refreshQueue()
    freezeCamera()
    BringState.Connection = RunService.Heartbeat:Connect(function()
        if BringState.Active then
            processNext()
            if BringState.SavedCamCFrame then
                local cam = Workspace.CurrentCamera
                cam.CameraType = Enum.CameraType.Scriptable
                cam.CFrame = BringState.SavedCamCFrame
                camBlocker.CFrame = BringState.SavedCamCFrame
                camBlocker.Parent = Workspace
            end
        end
    end)
end

 function stopBringAll()
    if BringState.Connection then
        BringState.Connection:Disconnect()
        BringState.Connection = nil
    end
    unfreezeCamera()
    local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if localRoot and BringState.SavedPos then
        localRoot.AssemblyLinearVelocity = Vector3.zero
        localRoot.CFrame = CFrame.new(BringState.SavedPos)
    end
end

TestTab:CreateToggle({
    Name = "Whitelist Friends(for bring and kick)",
    CurrentValue = false,
    Flag = "WhitelistFriends",
    Callback = function(value)
        AuraSettings.WhitelistFriends = value
    end
})

TestTab:CreateToggle({
    Name = "Bring All Players",
    CurrentValue = false,
    Flag = "BringAllToggle",
    Callback = function(value)
        BringState.Active = value
        if value then
            startBringAll()
            Rayfield:Notify({
                Title = "Bring All",
                Content = "Système activé!",
                Duration = 3,
                Image = 4483345998
            })
        else
            stopBringAll()
            Rayfield:Notify({
                Title = "Bring All",
                Content = "Système désactivé",
                Duration = 2,
                Image = 4483345998
            })
        end
    end
})

 function onCharacterAdded(character)
    local humanoid = character:WaitForChild("Humanoid")
    humanoid.Died:Connect(function()
        if BringState.Connection then
            BringState.Connection:Disconnect()
            BringState.Connection = nil
        end
        unfreezeCamera()
    end)
    if BringState.Active then
        local localRoot = character:WaitForChild("HumanoidRootPart")
        BringState.SavedPos = BringState.SavedPos or localRoot.Position
        BringState.SavedCamCFrame = BringState.SavedCamCFrame or Workspace.CurrentCamera.CFrame
        freezeCamera()
        BringState.Connection = RunService.Heartbeat:Connect(function()
            if BringState.Active then
                processNext()
                if BringState.SavedCamCFrame then
                    local cam = Workspace.CurrentCamera
                    cam.CameraType = Enum.CameraType.Scriptable
                    cam.CFrame = BringState.SavedCamCFrame
                    camBlocker.CFrame = BringState.SavedCamCFrame
                    camBlocker.Parent = Workspace
                end
            end
        end)
    end
end

LocalPlayer.CharacterAdded:Connect(onCharacterAdded)

Players.PlayerAdded:Connect(function()
    if BringState.Active then refreshQueue() end
end)

Players.PlayerRemoving:Connect(function(p)
    for i = #BringState.Queue, 1, -1 do
        if BringState.Queue[i] == p then
            table.remove(BringState.Queue, i)
        end
    end
    AuraState.GrabbedPlayers[p.UserId] = nil
end)

TestTab:CreateSection("d")

Players = game:GetService("Players")
 ReplicatedStorage = game:GetService("ReplicatedStorage")
plr = Players.LocalPlayer

 delayBetweenPlayers = 0
 whitelistFriends = true

 GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
 SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
 DestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")



 function findMountedBlobman()
    local char = plr.Character
    if not char then return nil end
    local hum = char:FindFirstChild("Humanoid")
    if not hum or not hum.SeatPart then return nil end
    
    local parent = hum.SeatPart.Parent
    if parent and parent.Name == "CreatureBlobman" then
        return parent
    end
    return nil
end

local function isValidTarget(player)
    if player == plr then return false end
    
    if whitelistFriends and plr:IsFriendsWith(player.UserId) then return false end
    
    local inPlot = player:FindFirstChild("InPlot")
    if inPlot and inPlot.Value == true then return false end
    
    local char = player.Character
    if not char then return false end
    
    local hum = char:FindFirstChild("Humanoid")
    if not hum or hum.Health <= 0 then return false end
    
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return false end
    
    return true
end

function kickPlayer(target, blobman, detector, weld, creatureGrab, creatureDrop)
    local targetChar = target.Character
    if not targetChar then return false end
    
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    if not targetHRP then return false end
    
    local myChar = plr.Character
    if not myChar then return false end
    
    local myHRP = myChar:FindFirstChild("HumanoidRootPart")
    if not myHRP then return false end
    
    local blobHum = blobman:FindFirstChild("Humanoid")
    if blobHum then
        blobHum.AutoRotate = false
    end
    
    local behindPos = targetHRP.CFrame * CFrame.new(0, 1, 5)
    myChar:PivotTo(CFrame.lookAt(behindPos.Position, targetHRP.Position))
    
    if not targetHRP.Parent then return false end
    

    for i = 1, 30 do
        pcall(function()
            SetNetworkOwner:FireServer(targetHRP, targetHRP.CFrame)
            targetHRP.CFrame = targetHRP.CFrame + Vector3.new(0, 20, 0)
            DestroyGrabLine:FireServer(targetHRP)
            creatureGrab:FireServer(detector, myHRP, weld)
            task.wait(0)
            creatureGrab:FireServer(detector, targetHRP, weld)
            creatureDrop:FireServer(weld)
        end)
        task.wait(0)
    end
    
    return true
end

TestTab:CreateButton({
    Name = "Kick Everyone",
    Callback = function()
        local myChar = plr.Character
        if not myChar then return end
        
        local blobman = findMountedBlobman()
        if not blobman then
 
            return
        end
        
        local detector = blobman:FindFirstChild("LeftDetector")
        local weld = detector and detector:FindFirstChild("LeftWeld")
        local script = blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
        local creatureGrab = script and script:FindFirstChild("CreatureGrab")
        local creatureDrop = script and script:FindFirstChild("CreatureDrop")
        
        if not (detector and weld and creatureGrab and creatureDrop) then
            return
        end
        
 
        local oldCF = myChar:GetPivot()
        local count = 0
        
        
        for _, target in ipairs(Players:GetPlayers()) do
            if not findMountedBlobman() then
                break
            end
            
            if isValidTarget(target) then
                local success = kickPlayer(target, blobman, detector, weld, creatureGrab, creatureDrop)
                if success then
                    count = count + 5
                end
                task.wait(delayBetweenPlayers)
            end
        end
        
        pcall(function()
            myChar:PivotTo(oldCF)
        end)

    end    
})


TestTab:CreateToggle({
    Name = "Whitelist Friends",
    Default = true,
    Callback = function(Value)
        whitelistFriends = Value
    end    
})



 Players = game:GetService("Players")
 Player = Players.LocalPlayer


Players.PlayerRemoving:Connect(function(player)
    Rayfield:Notify({
        Title = "Sm1 left",
        Content = (player and player.Name or "Unknown") .. " left the server",
        Duration = 5,
        Image = 4483362458
    })
end)

Players.PlayerAdded:Connect(function(plr)
    if plr:IsFriendsWith(Player.UserId) then
        Rayfield:Notify({
            Title = "Friend Joined",
            Content = plr.Name .. " has joined the game",
            Duration = 5,
            Image = 4483362458
        })
    end
end)



 espEnabled = false
	local espBoxes = {}
	local targetNames = {"partesp", "playercharacterlocationdetector"}
	local function IsTarget(obj)
		if not obj:IsA("BasePart") then return false end
		for _, name in ipairs(targetNames) do if string.lower(obj.Name) == string.lower(name) then return true end end
		return false
	end
	local function AddBoxESP(obj)
		if espBoxes[obj] then return end
		local box = Instance.new("BoxHandleAdornment")
		box.Adornee = obj
		box.AlwaysOnTop = true
		box.ZIndex = 5
		box.Color3 = Color3.fromRGB(255, 255, 255)
		box.Transparency = 0.5
		box.Size = obj.Size
		box.Parent = game.CoreGui
		espBoxes[obj] = box
		obj.AncestryChanged:Connect(function(_, parent)
			if not parent and espBoxes[obj] then espBoxes[obj]:Destroy() espBoxes[obj] = nil end
		end)
	end
 function RemoveAllBoxes()
		for obj, box in pairs(espBoxes) do if box then box:Destroy() end end
		espBoxes = {}
	end
	 function Scan()
		for _, obj in ipairs(workspace:GetDescendants()) do if espEnabled and IsTarget(obj) then AddBoxESP(obj) end end
	end
	workspace.DescendantAdded:Connect(function(obj) if espEnabled and IsTarget(obj) then AddBoxESP(obj) end end)

	TestTab:CreateToggle({
    Name = "PCLD View",
		Default = false,
		Callback = function(Value)
			espEnabled = Value
			if espEnabled then Scan() else RemoveAllBoxes() end
		end
	})
	
	local RunService = game:GetService("RunService")
local Players = game:GetService("Players")

local Player = Players.LocalPlayer

local SpinEnabled = false
local SpinSpeed = 5
local SpinConnection = nil

TestTab:CreateToggle({
	Name = "Spin Character",
	CurrentValue = false,
	Flag = "SpinCharacter",
	Callback = function(Value)
		SpinEnabled = Value

		if Value then
			if SpinConnection then SpinConnection:Disconnect() end

			SpinConnection = RunService.Heartbeat:Connect(function()
				local character = Player.Character
				local root = character and character:FindFirstChild("HumanoidRootPart")
				if root then
					root.CFrame = root.CFrame * CFrame.Angles(0, math.rad(SpinSpeed), 0)
				end
			end)
		else
			if SpinConnection then
				SpinConnection:Disconnect()
				SpinConnection = nil
			end
		end
	end,
})

TestTab:CreateSlider({
	Name = "Spin Speed",
	Range = {1, 10000},
	Increment = 1,
	Suffix = "deg",
	CurrentValue = 5,
	Flag = "SpinSpeed",
	Callback = function(Value)
		SpinSpeed = Value
	end,
})

 Players = game:GetService("Players")
 Player = Players.LocalPlayer
 Workspace = workspace

 dick2 = Instance.new("Folder")
dick2.Parent = Workspace
dick2.Name = "BinisHoles"

 gettingkicked = {}
 VOBBC = false

function getClosestPlayer(pos)
    local closest = nil
    local dist = math.huge
    for _, plr in pairs(Players:GetPlayers()) do
        local hrp = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
        if hrp then
            local d = (hrp.Position - pos).Magnitude
            if d < dist then
                dist = d
                closest = plr
            end
        end
    end
    return closest
end

workspace.ChildAdded:Connect(function(model)
    if model.Name == "BlackHoleKick" then
        local hole = model:WaitForChild("Hole", 5)
        if not hole then return end
        
        local diddler
        local victimPlayer = nil
        
        task.spawn(function()
            task.wait(0.1)
            
            diddler = model:Clone()
            diddler.Parent = dick2
            diddler.Name = "Unknown's BlackHole"
            
            if not VOBBC then
                if diddler:FindFirstChild("Hole") then
                    diddler.Hole.Transparency = 1
                    if diddler.Hole:FindFirstChild("BillboardGui") then
                        diddler.Hole.BillboardGui.Enabled = false
                    end
                end
            end
            
            local starttime = tick()
            while diddler and diddler.Parent do
                if diddler:FindFirstChild("Hole") and diddler.Hole:FindFirstChild("BillboardGui") then
                    local billboardGui = diddler.Hole.BillboardGui
                    if billboardGui:FindFirstChild("Large") then
                        billboardGui.Large.Rotation = (tick() - starttime) * 150
                    end
                    if billboardGui:FindFirstChild("Small") then
                        billboardGui.Small.Rotation = (tick() - starttime) * 150
                    end
                end
                task.wait()
            end
        end)
        
        local solved = false
        while not solved and model and model.Parent do
            for _, plr in pairs(Players:GetPlayers()) do
                if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                    local hrp = plr.Character.HumanoidRootPart
                    
                    if hrp.Anchored == true then
                        solved = true
                        
                        local alreadyFlagged = false
                        for _, id in pairs(gettingkicked) do
                            if id == plr.UserId then
                                alreadyFlagged = true
                                break
                            end
                        end
                        
                        if not alreadyFlagged then
                            victimPlayer = plr
                            table.insert(gettingkicked, plr.UserId)
                            
                            if diddler then
                                diddler.Name = plr.Name .. "'s BlackHole"
                            end
                            
                            local hpp = hrp.CFrame.Position
                            local bhk = hole.CFrame.Position
                            local distance = (hpp - bhk).Magnitude
                            local byPlayer = distance > 2
                            
                            local notifContent = string.format(
                                "%s (@%s) has been kicked%s at position (%d, %d, %d)",
                                plr.DisplayName,
                                plr.Name,
                                byPlayer and " by a player" or "",
                                math.floor(hole.CFrame.Position.X),
                                math.floor(hole.CFrame.Position.Y),
                                math.floor(hole.CFrame.Position.Z)
                            )
                            
                            Rayfield:Notify({
                                Title = "BlackHole Kick",
                                Content = notifContent,
                                Duration = 6.5,
                                Image = 4483362458
                            })
                            
                            task.spawn(function()
                                task.wait(5)
                                for i, id in pairs(gettingkicked) do
                                    if id == plr.UserId then
                                        table.remove(gettingkicked, i)
                                        break
                                    end
                                end
                            end)
                        end
                        break
                    end
                end
            end
            task.wait()
        end
    end
end)


 ViewBlackHolesToggle = TestTab:CreateToggle({
    Name = "View Offline BlackHoles",
    CurrentValue = false,
    Flag = "ViewBlackHoles",
    Callback = function(Value)
        VOBBC = Value
        
        if VOBBC then
            for _, diddler in pairs(dick2:GetChildren()) do
                if diddler:FindFirstChild("Hole") then
                    if diddler.Hole:FindFirstChild("BillboardGui") then
                        diddler.Hole.BillboardGui.Enabled = true
                    end
                    
                    for _, prt in pairs(diddler:GetDescendants()) do
                        if prt:IsA("BasePart") then
                            prt.Transparency = 0
                            if prt.Name == "HumanoidRootPart" then
                                prt.Transparency = 1
                            elseif prt.Name == "Hole" then
                                prt.Transparency = 0.25
                            end
                        end
                    end
                end
            end
            
            Rayfield:Notify({
                Title = "BlackHole Viewer",
                Content = "Offline BlackHoles are now visible",
                Duration = 3,
                Image = 4483362458
            })
        else
            for _, diddler in pairs(dick2:GetChildren()) do
                if diddler:FindFirstChild("Hole") then
                    if diddler.Hole:FindFirstChild("BillboardGui") then
                        diddler.Hole.BillboardGui.Enabled = false
                    end
                    
                    for _, prt in pairs(diddler:GetDescendants()) do
                        if prt:IsA("BasePart") then
                            prt.Transparency = 1
                        end
                    end
                end
            end
            
            Rayfield:Notify({
                Title = "BlackHole Viewer",
                Content = "Offline BlackHoles are now hidden",
                Duration = 3,
                Image = 4483362458
            })
        end
    end,
})

 ClearBlackHolesButton = TestTab:CreateButton({
    Name = "Clear All BlackHoles",
    Callback = function()
        local count = #dick2:GetChildren()
        dick2:ClearAllChildren()
        
        Rayfield:Notify({
            Title = "BlackHole Viewer",
            Content = string.format("Cleared %d BlackHole(s)", count),
            Duration = 3,
            Image = 4483362458
        })
    end,
})

Rayfield:LoadConfiguration()
