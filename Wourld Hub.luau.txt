local q = (loadstring(game:HttpGet("https://raw.githubusercontent.com/deividcomsono/Obsidian/main/Library.lua")))()
function CreateObsidianCompat()
    local c = game:GetService("UserInputService")
    local r = game:GetService("SoundService")
    local j = { Options = {}, Toggles = {}, Scheme = (q and q.Scheme) or
    { FontColor = Color3.fromRGB(236, 236, 236), MainColor = Color3.fromRGB(16, 16, 16), AccentColor = Color3.fromRGB(
    220, 220, 220), BackgroundColor = Color3.fromRGB(10, 10, 10), OutlineColor = Color3.fromRGB(48, 48, 48) }, ShowCustomCursor = true, _UnloadCallbacks = {}, _Window = nil, _NotifySide =
    "Right", _DPIScale = 100, _NotifySoundEnabled = true, _NotifySoundVolume = .5, _NotifySound = nil }
    j.FontColor = j.Scheme.FontColor
    j.MainColor = j.Scheme.MainColor
    j.AccentColor = j.Scheme.AccentColor
    j.BackgroundColor = j.Scheme.BackgroundColor
    j.OutlineColor = j.Scheme.OutlineColor
    local function u(q)
        if typeof(q) == "EnumItem" then return q end
        local c = tostring(q or "Unknown")
        c = c:gsub("Enum%.KeyCode%.", "")
        local r = Enum.KeyCode[c]
        if r then return r end
        return Enum.KeyCode.Unknown
    end
    local function M(q) return (u(q)).Name end
    local function G(q, c)
        if not q then return false end
        local r = { "Set", "SetValue", "Select" }
        for r, j in ipairs(r) do
            local u = q[j]
            if type(u) == "function" then
                local r = pcall(function() u(q, c) end)
                if not r then r = pcall(function() u(c) end) end
                if r then return true end
            end
        end
        return false
    end
    local d = { Visible = true }
    j.KeybindFrame = d
    local z = {}
    c.InputBegan:Connect(function(q, c)
        if c then return end
        if q.UserInputType ~= Enum.UserInputType.Keyboard then return end
        for c, r in ipairs(z) do if r and (r.Option and (r.Option.Value == q.KeyCode and type(r.Callback) == "function")) then
                pcall(r.Callback, q.KeyCode) end end
    end)
    function j.PlayNotifySound(q)
        if not q._NotifySoundEnabled then return end
        if (not q._NotifySound) or (not q._NotifySound.Parent) then
            local c = Instance.new("Sound")
            c.Name = "WourldNotifySound"
            c.SoundId = "rbxasset://sounds/electronicpingshort.wav"
            c.Volume = q._NotifySoundVolume
            c.PlayOnRemove = false
            c.Parent = r
            q._NotifySound = c
        end
        q._NotifySound.Volume = q._NotifySoundVolume
        pcall(function() q._NotifySound:Play() end)
    end

    function j.Notify(r, c)
        local j = c or {}
        local u = j.Title or "Wourld Hub"
        local M = j.Description or j.Content or ""
        local G = j.Duration or 4
        local d = tick()
        local z = tostring(u) .. ("|" .. tostring(M))
        if r._LastNotifyHash == z and (d - ((r._LastNotifyAt or 0))) < .75 then return end
        r._LastNotifyHash = z
        r._LastNotifyAt = d
        r:PlayNotifySound()
        pcall(function() q:Notify({ Title = u, Description = M, Time = G }) end)
    end

    function j.SetNotifySide(r, c)
        r._NotifySide = c or "Right"
        if q and type(q.SetNotifySide) == "function" then pcall(function() q:SetNotifySide(r._NotifySide) end) end
    end

    function j.SetDPIScale(r, c)
        local j = tonumber(c) or 100
        r._DPIScale = j
        if q and type(q.SetDPIScale) == "function" then pcall(function() q:SetDPIScale(j) end) end
    end

    function j.UpdateColorsUsingRegistry(c) if q and type(q.UpdateColorsUsingRegistry) == "function" then pcall(function()
                q:UpdateColorsUsingRegistry() end) end end

    function j.GetDarkerColor(r, c)
        if q and type(q.GetDarkerColor) == "function" then
            local r, j = pcall(function() return q:GetDarkerColor((typeof(c) == "Color3") and c or
                Color3.fromRGB(220, 220, 220)) end)
            if r and typeof(j) == "Color3" then return j end
        end
        local j = (typeof(c) == "Color3") and c or Color3.fromRGB(220, 220, 220)
        return j:Lerp(Color3.new(0, 0, 0), .25)
    end

    function j.OnUnload(c, q) if type(q) == "function" then table.insert(c._UnloadCallbacks, q) end end

    function j.Unload(c)
        for q, c in ipairs(c._UnloadCallbacks) do pcall(c) end
        if c._NotifySound then
            pcall(function() c._NotifySound:Destroy() end)
            c._NotifySound = nil
        end
        if q and type(q.Unload) == "function" then pcall(function() q:Unload() end) end
    end

    local function Y(q)
        local c = (tostring(q or "")):lower()
        if c == "" then return nil end
        local r = { shield = "shield", target = "crosshair", eye = "eye", server = "server", palette = "palette", smile =
        "smile", users = "users", settings = "settings", misc = "sliders-horizontal", keybinds = "keyboard", keyboard =
        "keyboard", owner = "crown", credits = "info", ui = "settings", cloud = "cloud" }
        return r[c] or c
    end
    local function P(c, r)
        local d = {}
        function d.AddDivider(q) pcall(function() c:AddDivider() end) end

        function d.AddLabel(r, q)
            local j = tostring(q or "")
            local u
            pcall(function() u = c:AddLabel(j, true) end)
            local M = {}
            function M.AddKeyPicker(r, q, c) return d:AddKeyPicker(q, c, u) end

            function M.AddColorPicker(r, q, c) return d:AddColorPicker(q, c, u) end

            return M
        end

        function d.AddButton(r, q)
            local j = q or {}
            local u = j.Text or "Button"
            local M = j.Func
            local G
            pcall(function() G = c:AddButton({ Text = u, DoubleClick = j.DoubleClick == true, Func = function() if M then
                        pcall(M) end end }) end)
            return G
        end

        function d.AddToggle(u, q, r)
            local M = r or {}
            local z = M.Callback
            local Y = { Value = M.Default and true or false }
            local P
            pcall(function() P = c:AddToggle(q,
                    { Text = M.Text or q, Default = Y.Value, Callback = function(q)
                        Y.Value = q and true or false
                        if z then pcall(z, Y.Value) end
                    end }) end)
            function Y.SetValue(c, q)
                local r = q and true or false
                c.Value = r
                local j = G(P, r)
                if z and not j then pcall(z, r) end
            end

            j.Toggles[q] = Y
            local O = {}
            function O.AddColorPicker(r, q, c) return d:AddColorPicker(q, c, P) end

            return O
        end

        function d.AddDropdown(u, q, r)
            local M = r or {}
            local function d(q)
                local c = {}
                local r = {}
                if type(q) == "table" then for q, j in ipairs(q) do if j ~= nil then
                            local q = tostring(j)
                            if q ~= "" then
                                local j = string.lower(q)
                                if not r[j] then
                                    r[j] = true
                                    c[#c + 1] = q
                                end
                            end
                        end end end
                return c
            end
            local z = d(M.Values or {})
            local Y = M.Callback
            local P = M.Default
            if type(P) == "number" then P = z[P] end
            if P ~= nil then P = tostring(P) end
            if P == nil then P = z[1] end
            if P ~= nil and (#z > 0 and table.find(z, P) == nil) then P = z[1] end
            local O = { Value = P, Values = z }
            local a
            pcall(function() a = c:AddDropdown(q,
                    { Text = M.Text or q, Values = z, Default = P, Multi = M.Multi and true or false, AllowNull = M
                    .AllowNone == true, Callback = function(q)
                        local c = q ~= nil and tostring(q) or nil
                        O.Value = c
                        if Y then pcall(Y, c) end
                    end }) end)
            function O.SetValue(c, q)
                local r = q ~= nil and tostring(q) or nil
                if r ~= nil and (c.Values and (#c.Values > 0 and table.find(c.Values, r) == nil)) then r = c.Values[1] end
                c.Value = r
                local j = G(a, r)
                if a then pcall(function() a.Value = r end) end
                if Y and not j then pcall(Y, r) end
            end

            function O.SetValues(c, q)
                c.Values = d(q or {})
                if a then
                    if type(a.SetValues) == "function" then pcall(function() a:SetValues(c.Values) end) end
                    if type(a.SetOptions) == "function" then pcall(function() a:SetOptions(c.Values) end) end
                    if type(a.SetItems) == "function" then pcall(function() a:SetItems(c.Values) end) end
                    if type(a.UpdateValues) == "function" then pcall(function() a:UpdateValues(c.Values) end) end
                    if type(a.Refresh) == "function" then pcall(function() a:Refresh() end) end
                    pcall(function() a.Values = c.Values end)
                    pcall(function() a.Options = c.Values end)
                end
                if c.Values and (#c.Values > 0 and table.find(c.Values, c.Value) == nil) then c:SetValue(c.Values[1]) elseif (not c.Values) or #c.Values == 0 then
                    c:SetValue(nil) end
            end

            j.Options[q] = O
            return O
        end

        function d.AddSlider(u, q, r)
            local M = r or {}
            local d = tonumber(M.Min) or 0
            local z = tonumber(M.Max) or 100
            local Y = tonumber(M.Default) or d
            local P = tonumber(M.Rounding) or 0
            local O = M.Callback
            local a = { Value = Y }
            local o
            pcall(function() o = c:AddSlider(q,
                    { Text = M.Text or q, Min = d, Max = z, Default = Y, Rounding = P, Callback = function(q)
                        a.Value = q
                        if O then pcall(O, q) end
                    end }) end)
            function a.SetValue(c, q)
                local r = tonumber(q) or d
                r = math.clamp(r, d, z)
                c.Value = r
                local j = G(o, r)
                if O and not j then pcall(O, r) end
            end

            j.Options[q] = a
            return a
        end

        function d.AddInput(u, q, r)
            local M = r or {}
            local d = M.Callback
            local z = tostring(M.Default or "")
            local Y = { Value = z }
            local P
            pcall(function() P = c:AddInput(q,
                    { Text = M.Text or q, Default = z, Placeholder = M.Placeholder or "", Numeric = M.Numeric and true or
                    false, Finished = M.Finished and true or false, Callback = function(q)
                        Y.Value = tostring(q or "")
                        if d then pcall(d, Y.Value) end
                    end }) end)
            function Y.SetValue(c, q)
                local r = tostring(q or "")
                c.Value = r
                local j = G(P, r)
                if d and not j then pcall(d, r) end
            end

            j.Options[q] = Y
            return Y
        end

        function d.AddColorPicker(d, r, u, M)
            local z = u or {}
            local Y = z.Callback
            local P = z.Default or Color3.fromRGB(255, 255, 255)
            local O = { Value = P }
            local a
            local o = M
            if not o or type(o.AddColorPicker) ~= "function" then pcall(function() o = c:AddLabel(
                    tostring(z.Title or z.Text or r), false) end) end
            pcall(function() if o and type(o.AddColorPicker) == "function" then
                    o:AddColorPicker(r, { Default = P, Callback = function(q)
                        O.Value = q
                        if Y then pcall(Y, q) end
                    end })
                    a = q.Options[r]
                end end)
            function O.SetValueRGB(c, q)
                c.Value = q
                local r = false
                if a and type(a.SetValueRGB) == "function" then r = pcall(function() a:SetValueRGB(q) end) else r = G(a,
                        q) end
                if Y and not r then pcall(Y, q) end
            end

            j.Options[r] = O
            return O
        end

        function d.AddKeyPicker(Y, r, G, d)
            local P = G or {}
            local O = P.Callback
            local a = P.ChangedCallback
            local o = u(P.Default or "Unknown")
            local v = { Value = o }
            local b
            local X = d
            local x = { Option = v, Callback = O }
            table.insert(z, x)
            if not X or type(X.AddKeyPicker) ~= "function" then pcall(function() X = c:AddLabel(tostring(P.Text or r),
                        false) end) end
            pcall(function() if X and type(X.AddKeyPicker) == "function" then
                    X:AddKeyPicker(r,
                        { Text = P.Text or r, Default = M(o), Mode = P.Mode or "Toggle", Callback = function() end, ChangedCallback = function(
                            c)
                            local j = u(c)
                            if v.Value ~= j then
                                v.Value = j
                                if a then pcall(a, j) end
                            end
                            if r == "MenuKeybind" then q.ToggleKeybind = j end
                        end })
                    b = q.Options[r]
                end end)
            function v.SetValue(j, c)
                local G = u(c)
                local d = M(G)
                j.Value = G
                local z = false
                if b and type(b.SetValue) == "function" then
                    z = pcall(function() b:SetValue({ d, b.Mode or P.Mode or "Toggle", b.Modifiers or {} }) end)
                    if not z then z = pcall(function() b:SetValue({ d, P.Mode or "Toggle", {} }) end) end
                end
                if a and not z then pcall(a, G) end
                x.Option = v
                if r == "MenuKeybind" then q.ToggleKeybind = G end
            end

            j.Options[r] = v
            return v
        end

        return d
    end
    function j.CreateWindow(j, r)
        local u = r or {}
        local M = c.TouchEnabled and (not c.MouseEnabled)
        local G = typeof(u.Size) == "UDim2" and u.Size or (M and UDim2.fromOffset(900, 650) or UDim2.fromOffset(980, 690))
        local d = q:CreateWindow({ Title = tostring(u.Title or "Wourld Hub"), Footer = tostring(u.Footer or "Wourld UI"), Icon =
        u.Icon or "cloud", NotifySide = u.NotifySide or j._NotifySide or "Right", ShowCustomCursor = u.ShowCustomCursor ~=
        false, Size = G, CornerRadius = tonumber(u.CornerRadius or u.Radius or 14) or 14, Center = u.Center ~= false, EnableCompacting =
        u.EnableCompacting and true or false, SidebarCompacted = u.SidebarCompacted and true or false, DisableSearch = u
        .HideSearchBar and true or false, ToggleKeybind = u.ToggleKeybind or Enum.KeyCode.RightShift, ShowMobileButtons =
        u.ShowMobileButtons ~= false, MobileButtonsSide = u.MobileButtonsSide or (M and "Right" or "Left"), UnlockMouseWhileOpen =
        u.UnlockMouseWhileOpen ~= false })
        j._Window = d
        if q and q.KeybindFrame then j.KeybindFrame = q.KeybindFrame end
        j:SetNotifySide(u.NotifySide or j._NotifySide or "Right")
        j:SetDPIScale(j._DPIScale)
        local z = {}
        function z.AddTab(r, q, c)
            local j = d:AddTab({ Name = tostring(q), Icon = Y(c) })
            local u = {}
            function u.AddLeftGroupbox(c, q) return P(j:AddLeftGroupbox(tostring(q)), d) end

            function u.AddRightGroupbox(c, q) return P(j:AddRightGroupbox(tostring(q)), d) end

            return u
        end

        return z
    end

    local O = game:GetService("HttpService")
    local a = { Library = nil, Folder = "Wourld_Hub", CurrentTheme = "Dark", Themes = { "Dark", "Light", "Rose", "Plant", "Red", "Indigo", "Sky", "Violet", "Amber", "Emerald", "Midnight", "Crimson", "MonokaiPro", "CottonCandy", "Mellowsi", "Rainbow" } }
    local o = { Dark = { FontColor = Color3.fromRGB(236, 236, 236), MainColor = Color3.fromRGB(16, 16, 16), AccentColor = Color3.fromRGB(220, 220, 220), BackgroundColor = Color3.fromRGB(10, 10, 10), OutlineColor = Color3.fromRGB(48, 48, 48) }, Light = { FontColor = Color3.fromRGB(22, 22, 22), MainColor = Color3.fromRGB(250, 250, 250), AccentColor = Color3.fromRGB(40, 120, 255), BackgroundColor = Color3.fromRGB(236, 236, 236), OutlineColor = Color3.fromRGB(185, 185, 185) }, Rose = { FontColor = Color3.fromRGB(255, 235, 245), MainColor = Color3.fromRGB(36, 19, 30), AccentColor = Color3.fromRGB(255, 92, 153), BackgroundColor = Color3.fromRGB(26, 14, 22), OutlineColor = Color3.fromRGB(92, 42, 70) }, Plant = { FontColor = Color3.fromRGB(226, 245, 228), MainColor = Color3.fromRGB(20, 32, 22), AccentColor = Color3.fromRGB(98, 214, 132), BackgroundColor = Color3.fromRGB(13, 21, 15), OutlineColor = Color3.fromRGB(52, 86, 58) }, Red = { FontColor = Color3.fromRGB(255, 235, 235), MainColor = Color3.fromRGB(33, 14, 14), AccentColor = Color3.fromRGB(255, 85, 85), BackgroundColor = Color3.fromRGB(21, 10, 10), OutlineColor = Color3.fromRGB(94, 40, 40) }, Indigo = { FontColor = Color3.fromRGB(232, 233, 255), MainColor = Color3.fromRGB(20, 22, 39), AccentColor = Color3.fromRGB(114, 126, 255), BackgroundColor = Color3.fromRGB(12, 14, 24), OutlineColor = Color3.fromRGB(52, 58, 105) }, Sky = { FontColor = Color3.fromRGB(225, 244, 255), MainColor = Color3.fromRGB(16, 29, 38), AccentColor = Color3.fromRGB(88, 189, 255), BackgroundColor = Color3.fromRGB(10, 18, 24), OutlineColor = Color3.fromRGB(44, 84, 110) }, Violet = { FontColor = Color3.fromRGB(236, 226, 255), MainColor = Color3.fromRGB(25, 17, 40), AccentColor = Color3.fromRGB(168, 120, 255), BackgroundColor = Color3.fromRGB(16, 11, 26), OutlineColor = Color3.fromRGB(62, 47, 104) }, Amber = { FontColor = Color3.fromRGB(255, 243, 220), MainColor = Color3.fromRGB(38, 27, 12), AccentColor = Color3.fromRGB(255, 176, 66), BackgroundColor = Color3.fromRGB(24, 17, 8), OutlineColor = Color3.fromRGB(108, 78, 38) }, Emerald = { FontColor = Color3.fromRGB(224, 255, 242), MainColor = Color3.fromRGB(10, 34, 23), AccentColor = Color3.fromRGB(69, 226, 156), BackgroundColor = Color3.fromRGB(8, 21, 15), OutlineColor = Color3.fromRGB(35, 92, 67) }, Midnight = { FontColor = Color3.fromRGB(232, 238, 255), MainColor = Color3.fromRGB(12, 14, 24), AccentColor = Color3.fromRGB(88, 132, 255), BackgroundColor = Color3.fromRGB(7, 9, 16), OutlineColor = Color3.fromRGB(38, 45, 74) }, Crimson = { FontColor = Color3.fromRGB(255, 236, 242), MainColor = Color3.fromRGB(36, 12, 21), AccentColor = Color3.fromRGB(255, 70, 130), BackgroundColor = Color3.fromRGB(24, 8, 14), OutlineColor = Color3.fromRGB(96, 34, 58) }, MonokaiPro = { FontColor = Color3.fromRGB(248, 248, 242), MainColor = Color3.fromRGB(42, 42, 35), AccentColor = Color3.fromRGB(169, 220, 118), BackgroundColor = Color3.fromRGB(30, 30, 25), OutlineColor = Color3.fromRGB(79, 79, 66) }, CottonCandy = { FontColor = Color3.fromRGB(255, 240, 252), MainColor = Color3.fromRGB(40, 22, 38), AccentColor = Color3.fromRGB(255, 123, 204), BackgroundColor = Color3.fromRGB(27, 15, 26), OutlineColor = Color3.fromRGB(100, 60, 96) }, Mellowsi = { FontColor = Color3.fromRGB(240, 244, 255), MainColor = Color3.fromRGB(22, 30, 44), AccentColor = Color3.fromRGB(120, 191, 255), BackgroundColor = Color3.fromRGB(15, 21, 32), OutlineColor = Color3.fromRGB(58, 76, 112) }, Rainbow = { FontColor = Color3.fromRGB(240, 240, 240), MainColor = Color3.fromRGB(20, 20, 20), AccentColor = Color3.fromRGB(128, 190, 255), BackgroundColor = Color3.fromRGB(12, 12, 12), OutlineColor = Color3.fromRGB(64, 64, 64) } }
    function a.SetLibrary(c, q) c.Library = q end

    function a.SetFolder(c, q) c.Folder = tostring(q or "Wourld_Hub") end

    function a.ApplyTheme(c, q)
        local r = tostring(q or "Dark")
        if table.find(c.Themes, r) == nil then r = "Dark" end
        c.CurrentTheme = r
        local j = o[r] or o.Dark
        if c.Library and c.Library.Scheme then
            c.Library.Scheme.FontColor = j.FontColor
            c.Library.Scheme.MainColor = j.MainColor
            c.Library.Scheme.AccentColor = j.AccentColor
            c.Library.Scheme.BackgroundColor = j.BackgroundColor
            c.Library.Scheme.OutlineColor = j.OutlineColor
            c.Library.FontColor = j.FontColor
            c.Library.MainColor = j.MainColor
            c.Library.AccentColor = j.AccentColor
            c.Library.BackgroundColor = j.BackgroundColor
            c.Library.OutlineColor = j.OutlineColor
            if type(c.Library.GetDarkerColor) == "function" then c.Library.AccentColorDark = c.Library:GetDarkerColor(c
                .Library.AccentColor) end
            pcall(function() c.Library:UpdateColorsUsingRegistry() end)
        end
    end

    function a.ApplyToTab(c, q)
        local r = q:AddLeftGroupbox("Theme")
        r:AddDropdown("ThemeManager_ThemeList",
            { Text = "Theme", Values = c.Themes, Default = c.CurrentTheme, Callback = function(q) a:ApplyTheme(q) end })
        r:AddButton({ Text = "Apply Theme", Func = function() a:ApplyTheme(a.CurrentTheme) end, DoubleClick = false })
    end

    local v = { Library = nil, Folder = "Wourld_Hub", SubFolder = "game-config", IgnoreTheme = false, IgnoreIndexes = {}, SelectedConfig = nil, InputConfigName =
    "", _LoadBusy = false, _ApplyingConfig = false, _ConfigPathMap = {} }
    function v.SetLibrary(c, q) c.Library = q end

    function v.IgnoreThemeSettings(q) q.IgnoreTheme = true end

    function v.SetIgnoreIndexes(c, q)
        c.IgnoreIndexes = {}
        if type(q) == "table" then for q, r in ipairs(q) do c.IgnoreIndexes[tostring(r)] = true end end
    end

    function v.SetFolder(c, q)
        c.Folder = tostring(q or "Wourld_Hub")
        c._CandidateRootsCache = nil
        c._CandidateRootsCacheAt = 0
        c._AllConfigsCache = nil
        c._AllConfigsCacheAt = 0
    end

    function v.SetSubFolder(c, q)
        c.SubFolder = tostring(q or "game-config")
        c._CandidateRootsCache = nil
        c._CandidateRootsCacheAt = 0
        c._AllConfigsCache = nil
        c._AllConfigsCacheAt = 0
    end

    function v.Notify(c, q) if c.Library and type(c.Library.Notify) == "function" then c.Library:Notify({ Title =
            "Wourld Hub", Description = tostring(q or ""), Duration = 4 }) end end

    function v.SanitizeName(c, q)
        local r = tostring(q or "")
        r = r:gsub("[%c<>:\"/\\|%?%*]", "")
        r = r:gsub("^%s+", "")
        r = r:gsub("%s+$", "")
        return r
    end

    function v.GetRootPath(q) return tostring(q.Folder or "Wourld_Hub") .. ("/" .. tostring(q.SubFolder or "game-config")) end

    function v.GetCandidateRootPaths(q)
        local c = tick()
        if q._CandidateRootsCache and c < ((tonumber(q._CandidateRootsCacheAt) or 0)) then
            local c = {}
            for q, r in ipairs(q._CandidateRootsCache) do c[#c + 1] = r end
            return c
        end
        local r = {}
        local j = {}
        local function u(q)
            local c = tostring(q or "")
            if c == "" then return false end
            if isfolder then
                local q, r = pcall(function() return isfolder(c) end)
                if q and r then return true end
            end
            if listfiles then
                local q = pcall(function() return listfiles(c) end)
                if q then return true end
            end
            return false
        end
        local function M(q)
            local c = tostring(q or "")
            local M = (c:gsub("\\", "/")):gsub("/+$", "")
            M = M:gsub("^workspace/workspace/", "workspace/")
            if (M:lower()):find("^workspace/", 1, true) == 1 then
                local q = M:gsub("^workspace/", "")
                if q ~= M and u(q) then M = q end
            end
            local G = string.lower(M)
            if M ~= "" and not j[G] then
                j[G] = true
                table.insert(r, M)
            end
        end
        local function G(q)
            local c = string.lower((tostring(q or "")):gsub("\\", "/"))
            if c:find("game%-config") then return true end
            if c:find("/configs", 1, true) or c:sub(-7) == "configs" then return true end
            if c:find("/config", 1, true) or c:sub(-6) == "config" then return true end
            return false
        end
        local d = tostring(q.Folder or "Wourld_Hub")
        local z = tostring(q.SubFolder or "game-config")
        M(d)
        M(d .. ("/" .. z))
        M("Wourld_Hub")
        M("Wourld_Hub/game-config")
        M("Wourld_Hub/FlingThings")
        M("Wourld_Hub/FlingThings/game-config")
        M("wourld_hub")
        M("wourld_hub/game-config")
        M("wourld_hub/FlingThings")
        M("wourld_hub/FlingThings/game-config")
        M("wourld_hub/flingthings/game-config")
        M("workspace/Wourld_Hub/FlingThings/game-config")
        M("workspace/wourld_hub/FlingThings/game-config")
        M("workspace/wourld_hub/flingthings/game-config")
        M("workspace\\Wourld_Hub\\FlingThings\\game-config")
        M("workspace\\wourld_hub\\FlingThings\\game-config")
        M("workspace\\wourld_hub\\flingthings\\game-config")
        M("workspace/wourld_hub")
        M("workspace/Wourld_Hub")
        M("workspace/wourld_hub/FlingThings")
        M("workspace/Wourld_Hub/FlingThings")
        M(d .. "/game-config")
        M(d .. "/configs")
        M(d .. ("/" .. (z .. "/configs")))
        M(d .. "/config")
        M("Wourld_Hub/configs")
        M("Wourld_Hub/config")
        M("Wourld_Hub/FlingThings/configs")
        M("NNhub")
        M("NNhub/configs")
        M("NNhub/game-config")
        M("NNHub")
        M("NNHub/configs")
        if listfiles then
            local q = {}
            local c = {}
            local function r(c, r)
                local j = ((tostring(c or "")):gsub("\\", "/")):gsub("/+$", "")
                if j ~= "" then table.insert(q, { path = j, depth = r or 0 }) end
            end
            r("workspace", 0)
            r("Workspace", 0)
            r("Wourld_Hub", 0)
            r("wourld_hub", 0)
            r(d, 0)
            local j = 1
            local u = 3
            local z = 260
            local Y = 0
            while j <= #q and Y < z do
                local d = q[j]
                j = j + 1
                local P = string.lower((tostring(d.path or "")):gsub("\\", "/"))
                if not c[P] then
                    c[P] = true
                    local q, j = pcall(function() return listfiles(d.path) end)
                    if q and type(j) == "table" then
                        if G(d.path) then M(d.path) end
                        for q, c in ipairs(j) do
                            Y = Y + 1
                            local j = ((tostring(c or "")):gsub("\\", "/")):gsub("/+$", "")
                            if j ~= "" then
                                local q = string.lower(j)
                                if G(j) then M(j) end
                                if d.depth < u and ((q:find("wourld_hub", 1, true) or q:find("wourld", 1, true) or q:find("flingthings", 1, true) or q:find("fling", 1, true) or q:find("game-config", 1, true) or q:find("/config", 1, true))) then
                                    local q = pcall(function() return listfiles(j) end)
                                    if q then r(j, d.depth + 1) end
                                end
                            end
                            if Y >= z then break end
                        end
                    end
                end
            end
        end
        q._CandidateRootsCache = {}
        for c, r in ipairs(r) do q._CandidateRootsCache[#q._CandidateRootsCache + 1] = r end
        q._CandidateRootsCacheAt = c + 2.8
        return r
    end

    function v.GetConfigFileExtensions(q) return { ".json", ".cfg", ".txt", ".lua", ".luau" } end

    function v.PathVariants(c, q)
        local r = tostring(q or "")
        local j = {}
        local u = {}
        local function M(q)
            local c = tostring(q or "")
            local r = string.lower(c:gsub("\\", "/"))
            if c ~= "" and not u[r] then
                u[r] = true
                table.insert(j, c)
            end
        end
        M(r)
        M(r:gsub("\\", "/"))
        M(r:gsub("/", "\\"))
        return j
    end

    function v.ListFilesAny(c, q)
        if not listfiles then return false, {} end
        for q, c in ipairs(c:PathVariants(q)) do
            local r, j = pcall(function() return listfiles(c) end)
            if r and type(j) == "table" then return true, j, c end
        end
        return false, {}
    end

    function v.FileExists(c, q)
        for q, c in ipairs(c:PathVariants(q)) do
            if isfile then
                local q, r = pcall(function() return isfile(c) end)
                if q and r then return true, c end
            end
            if readfile then
                local q = pcall(function() readfile(c) end)
                if q then return true, c end
            end
        end
        return false, tostring(q or "")
    end

    function v.ReadFileAny(c, q)
        if not readfile then return false, nil, tostring(q or "") end
        for q, c in ipairs(c:PathVariants(q)) do
            local r, j = pcall(function() return readfile(c) end)
            if r and type(j) == "string" then return true, j, c end
        end
        return false, nil, tostring(q or "")
    end

    function v.StripConfigExtension(c, q)
        local r = tostring(q or "")
        local j = string.lower(r)
        for q, c in ipairs(c:GetConfigFileExtensions()) do if #r > #c and j:sub(-(#c)) == c then return r:sub(1, #r - #c),
                    c end end
        return nil, nil
    end

    function v.IterateConfigFiles(c, q)
        if type(q) ~= "function" then return end
        if not listfiles then return end
        local r = {}
        local j = {}
        for q, c in ipairs(c:GetCandidateRootPaths()) do
            local j = tostring(c or "")
            if j ~= "" then table.insert(r, { path = j, depth = 0 }) end
        end
        local u = 1
        local M = 5
        local G = 760
        local d = 0
        while u <= #r and d < G do
            local z = r[u]
            u = u + 1
            local Y = string.lower((tostring(z.path)):gsub("\\", "/"))
            if not j[Y] then
                j[Y] = true
                local u, P = c:ListFilesAny(z.path)
                if u and type(P) == "table" then for j, u in ipairs(P) do
                        d = d + 1
                        local Y = tostring(u or "")
                        local P = string.lower(Y:gsub("\\", "/"))
                        local O = Y:find("^%a:[/\\]") ~= nil or Y:find("^/") ~= nil or P:find("^workspace/", 1, true) ==
                        1
                        if (not O) and ((Y:find("/", 1, true) == nil) and (Y:find("\\", 1, true) == nil)) then Y =
                            tostring(z.path) .. ("/" .. Y) end
                        local a = Y:match("([^/\\]+)$") or ""
                        local o = false
                        if isfile or readfile then
                            o = c:FileExists(Y)
                            if (not o) and a:find("%.") then o = true end
                        elseif a:find("%.") then o = true end
                        if a ~= "" and o then
                            local r = c:StripConfigExtension(a)
                            if r then if q(Y, r, a) == false then return end elseif not a:find("%.") then if q(Y, a, a) == false then return end end
                        elseif z.depth < M and Y ~= "" then
                            local q = string.lower(Y:gsub("\\", "/"))
                            if (q:find("wourld_hub", 1, true) or q:find("wourld", 1, true) or q:find("flingthings", 1, true) or q:find("fling", 1, true) or q:find("game%-config") or q:find("/config", 1, true)) then
                                local q = c:ListFilesAny(Y)
                                if q then table.insert(r, { path = Y, depth = z.depth + 1 }) end
                            end
                        end
                        if d >= G then break end
                    end end
            end
        end
    end

    function v.ResolveConfigPath(c, q)
        local r = c:SanitizeName(q)
        if r == "" then return nil end
        local j = r:gsub("\\", "/")
        if j:find("%.") then
            local q = c:GetRootPath() .. ("/" .. j)
            local r, u = c:FileExists(q)
            if r then return u end
        end
        local u = c:GetConfigPath(r)
        local M, G = c:FileExists(u)
        if M then return G end
        local d = { r, r .. ".json", r .. ".cfg", r .. ".txt", r .. ".lua", r .. ".luau" }
        for q, r in ipairs(d) do
            local j, u = c:FileExists(r)
            if j then return u end
        end
        for q, j in ipairs(c:GetConfigFileExtensions()) do
            local u = c:GetRootPath() .. ("/" .. (tostring(r) .. j))
            local M, G = c:FileExists(u)
            if M then return G end
        end
        local z = string.lower(r)
        local Y = c._ConfigPathMap and c._ConfigPathMap[z]
        if Y then
            local q, r = c:FileExists(Y)
            if q then return r end
        end
        for q, j in ipairs(c:GetCandidateRootPaths()) do
            local u = tostring(j or "")
            local M = u .. ("/" .. tostring(r))
            local G, d = c:FileExists(M)
            if G then return d end
            for q, j in ipairs(c:GetConfigFileExtensions()) do
                local M = u .. ("/" .. (tostring(r) .. j))
                local G, d = c:FileExists(M)
                if G then return d end
            end
        end
        c:AllConfigs()
        Y = c._ConfigPathMap and c._ConfigPathMap[z]
        if Y then
            local q, r = c:FileExists(Y)
            if q then return r end
        end
        local P = string.lower(r)
        local O = nil
        c:IterateConfigFiles(function(q, c)
            if string.lower(tostring(c or "")) == P then
                O = q
                return false
            end
            return true
        end)
        if O then return O end
        return nil
    end

    function v.GetConfigPath(c, q) return c:GetRootPath() .. ("/" .. (tostring(q) .. ".json")) end

    function v.GetAutoloadPath(q) return tostring(q.Folder or "Wourld_Hub") .. "/autoload.txt" end

    function v.GetConfigIndexPaths(q)
        local c = q:GetRootPath()
        local r = tostring(q.Folder or "Wourld_Hub")
        return { c .. "/_index.json", c .. "/config-index.json", r .. "/_index.json", r .. "/config-index.json",
            "Wourld_Hub/_index.json", "wourld_hub/_index.json" }
    end

    function v.ReadConfigIndex(q)
        if not readfile then return {} end
        local c = {}
        local r = {}
        for j, u in ipairs(q:GetConfigIndexPaths()) do
            local M, G = pcall(function() return readfile(u) end)
            if M and (type(G) == "string" and G ~= "") then
                local j, u = pcall(function() return O:JSONDecode(G) end)
                if j and type(u) == "table" then for j, u in ipairs(u) do
                        local M = q:SanitizeName(u)
                        local G = string.lower(M)
                        if M ~= "" and not r[G] then
                            r[G] = true
                            c[#c + 1] = M
                        end
                    end end
            end
        end
        table.sort(c, function(q, c) return string.lower(q) < string.lower(c) end)
        return c
    end

    function v.WriteConfigIndex(c, q)
        if not writefile then return false end
        local r = {}
        local j = {}
        for q, u in ipairs(type(q) == "table" and q or {}) do
            local M = c:SanitizeName(u)
            local G = string.lower(M)
            if M ~= "" and not j[G] then
                j[G] = true
                r[#r + 1] = M
            end
        end
        table.sort(r, function(q, c) return string.lower(q) < string.lower(c) end)
        local u, M = pcall(function() return O:JSONEncode(r) end)
        if not u then return false end
        for q, r in ipairs(c:GetConfigIndexPaths()) do
            local j = ((tostring(r)):gsub("\\", "/")):match("^(.*)/[^/]+$")
            if j and j ~= "" then c:EnsurePath(j) end
            local u = pcall(function() writefile(r, M) end)
            if u then return true end
        end
        return false
    end

    function v.SetConfigIndexEntry(r, q, c)
        local j = r:SanitizeName(q)
        if j == "" then return end
        local u = r:ReadConfigIndex()
        local M = {}
        local G = {}
        local d = string.lower(j)
        for q, c in ipairs(u) do
            local r = string.lower(tostring(c))
            if r ~= d and not G[r] then
                G[r] = true
                M[#M + 1] = c
            end
        end
        if c then M[#M + 1] = j end
        r:WriteConfigIndex(M)
    end

    function v.EnsurePath(c, q)
        local r = (tostring(q or "")):gsub("\\", "/")
        if not ((isfolder and makefolder)) then return r ~= "" end
        local j = ""
        for q in r:gmatch("[^/]+") do
            if j == "" then j = q else j = j .. ("/" .. q) end
            if not isfolder(j) then pcall(function() makefolder(j) end) end
        end
        return isfolder(r)
    end

    function v.EnsureStorage(q)
        local c = tostring(q.Folder or "Wourld_Hub")
        local r = q:GetRootPath()
        q:EnsurePath(c)
        q:EnsurePath(r)
        if isfolder then
            local q, c = pcall(function() return isfolder(r) end)
            if q then return c and true or false end
        end
        return true
    end

    function v.EncodeValue(c, q)
        local r = typeof(q)
        if r == "Color3" then return { __type = "Color3", r = q.R, g = q.G, b = q.B } end
        if r == "EnumItem" then return { __type = "EnumItem", enum = tostring(q.EnumType), name = q.Name } end
        if r == "Vector2" then return { __type = "Vector2", x = q.X, y = q.Y } end
        if r == "Vector3" then return { __type = "Vector3", x = q.X, y = q.Y, z = q.Z } end
        if type(q) == "table" then
            local r = {}
            for q, j in pairs(q) do r[tostring(q)] = c:EncodeValue(j) end
            return r
        end
        if type(q) == "number" or type(q) == "string" or type(q) == "boolean" or q == nil then return q end
        return tostring(q)
    end

    function v.DecodeValue(c, q)
        if type(q) ~= "table" then return q end
        if q.__type == "Color3" then return Color3.new(tonumber(q.r) or 1, tonumber(q.g) or 1, tonumber(q.b) or 1) end
        if q.__type == "EnumItem" then
            local c = (tostring(q.enum or "")):gsub("Enum%.", "")
            local r = Enum[c]
            local j = tostring(q.name or "")
            if r and r[j] then return r[j] end
            if Enum.KeyCode[j] then return Enum.KeyCode[j] end
            return Enum.KeyCode.Unknown
        end
        if q.__type == "Vector2" then return Vector2.new(tonumber(q.x) or 0, tonumber(q.y) or 0) end
        if q.__type == "Vector3" then return Vector3.new(tonumber(q.x) or 0, tonumber(q.y) or 0, tonumber(q.z) or 0) end
        local r = {}
        for q, j in pairs(q) do r[q] = c:DecodeValue(j) end
        return r
    end

    function v.CollectConfig(q)
        local c = { options = {}, toggles = {} }
        if not q.Library then return c end
        for r, j in pairs(q.Library.Options or {}) do
            local u = tostring(r)
            if not q.IgnoreIndexes[u] and (u ~= "ConfigListDropdown" and u ~= "ConfigNameInput") then if not ((q.IgnoreTheme and u:sub(1, 13) == "ThemeManager_")) then c.options[u] =
                    q:EncodeValue(j.Value) end end
        end
        for r, j in pairs(q.Library.Toggles or {}) do
            local u = tostring(r)
            if not q.IgnoreIndexes[u] then c.toggles[u] = j.Value and true or false end
        end
        return c
    end

    function v.ApplyConfig(c, q)
        if type(q) ~= "table" or not c.Library then return end
        c._ApplyingConfig = true
        pcall(function()
            local r = 0
            local j = tick()
            local function u()
                r = r + 1
                local q = tick()
                if (r % 2 == 0) or (q - j >= .03) then
                    j = q
                    task.wait()
                end
            end
            local M = {}
            for q in pairs(q.toggles or {}) do table.insert(M, q) end
            table.sort(M, function(q, c) return tostring(q) < tostring(c) end)
            for r, j in ipairs(M) do
                local M = q.toggles[j]
                local G = c.Library.Toggles[j]
                if G and type(G.SetValue) == "function" then
                    local q = M and true or false
                    if G.Value ~= q then
                        pcall(function() G:SetValue(q) end)
                        u()
                    end
                end
            end
            local G = {}
            for q in pairs(q.options or {}) do table.insert(G, q) end
            table.sort(G, function(q, c) return tostring(q) < tostring(c) end)
            for r, j in ipairs(G) do
                local M = q.options[j]
                local G = c.Library.Options[j]
                if G then
                    local q = c:DecodeValue(M)
                    if typeof(q) == "Color3" and type(G.SetValueRGB) == "function" then if G.Value ~= q then
                            pcall(function() G:SetValueRGB(q) end)
                            u()
                        end elseif type(G.SetValue) == "function" then if G.Value ~= q then
                            pcall(function() G:SetValue(q) end)
                            u()
                        end else G.Value = q end
                end
            end
        end)
        c._ApplyingConfig = false
    end

    function v.AllConfigs(q)
        local c = tick()
        if q._AllConfigsCache and c < ((tonumber(q._AllConfigsCacheAt) or 0)) then
            local c = {}
            for q, r in ipairs(q._AllConfigsCache) do c[#c + 1] = r end
            return c
        end
        q:EnsureStorage()
        local r = {}
        local j = {}
        local u = q._ConfigPathMap or {}
        q._ConfigPathMap = {}
        local function M(c, u, M)
            local G = q:SanitizeName(u)
            local d = string.lower(G)
            if G == "" or d == "autoload" then return end
            local z = string.lower((tostring(c or "")):gsub("\\", "/"))
            local Y = string.lower(tostring(M or ""))
            if Y:sub(-4) == ".txt" then if not ((z:find("config", 1, true) or z:find("wourld_hub", 1, true) or z:find("nnhub", 1, true))) then return end end
            if not j[d] then
                j[d] = true
                table.insert(r, G)
            end
            if not q._ConfigPathMap[d] then q._ConfigPathMap[d] = tostring(c or "") end
        end
        for q, c in pairs(u) do M(c, q, q .. ".json") end
        if listfiles then for c, r in ipairs(q:GetCandidateRootPaths()) do
                local j, u = q:ListFilesAny(r)
                if j and type(u) == "table" then for c, j in ipairs(u) do
                        local u = tostring(j or "")
                        local G = string.lower(u:gsub("\\", "/"))
                        local d = u:find("^%a:[/\\]") ~= nil or u:find("^/") ~= nil or G:find("^workspace/", 1, true) ==
                        1
                        if (not d) and ((u:find("/", 1, true) == nil) and (u:find("\\", 1, true) == nil)) then u =
                            tostring(r) .. ("/" .. u) end
                        local z = u:match("([^/\\]+)$") or ""
                        if z ~= "" then
                            local c = q:FileExists(u)
                            if (not c) then
                                local r = string.lower(z)
                                for q, j in ipairs(q:GetConfigFileExtensions()) do if r:sub(-(#j)) == j then
                                        c = true
                                        break
                                    end end
                            end
                            if c then
                                local c = q:StripConfigExtension(z)
                                if c then M(u, c, z) end
                            end
                        end
                    end end
            end end
        local G = q:ReadConfigIndex()
        for c, r in ipairs(G) do M(q:GetConfigPath(r), r, r .. ".json") end
        if #r == 0 and listfiles then q:IterateConfigFiles(function(q, c, r)
                M(q, c, r)
                return true
            end) end
        table.sort(r, function(q, c) return string.lower(q) < string.lower(c) end)
        q._AllConfigsCache = {}
        for c, r in ipairs(r) do q._AllConfigsCache[#q._AllConfigsCache + 1] = r end
        q._AllConfigsCacheAt = c + 1.8
        return r
    end

    function v.RefreshConfigDropdown(q)
        q._AllConfigsCache = nil
        q._AllConfigsCacheAt = 0
        local c = q:AllConfigs()
        local r = q.Library and (q.Library.Options and q.Library.Options.ConfigListDropdown)
        if r and type(r.SetValues) == "function" then pcall(function() r:SetValues(c) end) end
        if #c == 0 then
            q.SelectedConfig = nil
            return c
        end
        local j = false
        if q.SelectedConfig then
            local r = string.lower(tostring(q.SelectedConfig))
            for c, u in ipairs(c) do if string.lower(tostring(u)) == r then
                    q.SelectedConfig = u
                    j = true
                    break
                end end
        end
        if not j then q.SelectedConfig = c[1] end
        if r and type(r.SetValue) == "function" then pcall(function() r:SetValue(q.SelectedConfig) end) end
        return c
    end

    function v.SaveConfig(r, q, c)
        if not ((r:EnsureStorage() and writefile)) then return false, "File API is not available" end
        local j = r:SanitizeName(q)
        if j == "" then return false, "Invalid config name" end
        local u = r:GetConfigPath(j)
        local M = r:ResolveConfigPath(j)
        if c == false and M then return false, "Config already exists" end
        local G = r:CollectConfig()
        local d, z = pcall(function() return O:JSONEncode(G) end)
        if not d then return false, "Failed to encode config" end
        local Y = {}
        local P = {}
        local function a(q)
            local c = (tostring(q or "")):gsub("\\", "/")
            local r = string.lower(c)
            if c ~= "" and not P[r] then
                P[r] = true
                Y[#Y + 1] = c
            end
        end
        a(u)
        local o, v = pcall(function() return r:GetCandidateRootPaths() end)
        if o and type(v) == "table" then for q, c in ipairs(v) do
                local r = ((tostring(c or "")):gsub("\\", "/")):gsub("/+$", "")
                if r ~= "" then a(r .. ("/" .. (j .. ".json"))) end
            end end
        a(tostring(r.Folder or "Wourld_Hub") .. ("/" .. (j .. ".json")))
        a("Wourld_Hub/" .. (j .. ".json"))
        local b = nil
        local X = nil
        for q, c in ipairs(Y) do
            local j = ((tostring(c)):gsub("\\", "/")):match("^(.*)/[^/]+$")
            if j and j ~= "" then r:EnsurePath(j) end
            local u, M = pcall(function() writefile(c, z) end)
            if u then
                b = c
                break
            end
            X = M
        end
        if not b then
            local q = M or u
            local c, r = pcall(function() writefile(q, z) end)
            if c then b = q else X = r end
        end
        if not b then return false,
                "Failed to write config into " .. (tostring(r:GetRootPath()) .. (": " .. tostring(X or "unknown error"))) end
        local x = r:SetConfigIndexEntry(j, true)
        r._ConfigPathMap[string.lower(j)] = tostring(b)
        r._AllConfigsCache = nil
        r._AllConfigsCacheAt = 0
        r.SelectedConfig = j
        if not x then r:Notify("Config saved, but index update failed. Path: " .. tostring(b)) end
        return true
    end

    function v.LoadConfig(c, q)
        if not readfile then return false, "File API is not available" end
        local r = c:SanitizeName(q)
        if r == "" then return false, "Invalid config name" end
        local j = c:ResolveConfigPath(r)
        local u, M = c:FileExists(j)
        if not u then return false, "Config not found" end
        local G, d, z = c:ReadFileAny(M or j)
        if not G then return false, "Failed to read config" end
        local Y, P = pcall(function() return O:JSONDecode(d) end)
        if not Y then
            local q = false
            local r = (tostring(d or "")):gsub("^%s+", "")
            local j = type(loadstring) == "function" and
            (#r > 1 and (#r <= 260000 and ((r:sub(1, 1) == "{" or r:sub(1, 6) == "return"))))
            if j then
                local r, j = pcall(function()
                    local q = loadstring(d)
                    if not q and d:sub(1, 6) ~= "return" then q = loadstring("return " .. d) end
                    return q and q() or nil
                end)
                if r and type(j) == "table" then
                    if type(j.options) == "table" or type(j.toggles) == "table" then P = j else
                        P = { options = {}, toggles = {} }
                        for q, r in pairs(j) do
                            local j = tostring(q)
                            if c.Library and (c.Library.Toggles and (c.Library.Toggles[j] and type(r) == "boolean")) then P.toggles[j] =
                                r else P.options[j] = r end
                        end
                    end
                    q = true
                end
            end
            if not q then return false, "Invalid config data" end
        end
        c:ApplyConfig(P)
        local a = ((tostring(z or M or j)):gsub("\\", "/")):match("([^/]+)$") or r
        local o = c:StripConfigExtension(a)
        c.SelectedConfig = o or r
        return true
    end

    function v.DeleteConfig(c, q)
        local r = delfile or deletefile
        if not ((r and ((isfile or readfile)))) then return false, "Delete API is not available" end
        local j = c:SanitizeName(q)
        if j == "" then return false, "Invalid config name" end
        local u = c:ResolveConfigPath(j)
        local M, G = c:FileExists(u)
        if not M then return false, "Config not found" end
        local d, z = pcall(function() r(G or u) end)
        if not d then return false, tostring(z) end
        c:SetConfigIndexEntry(j, false)
        c._AllConfigsCache = nil
        c._AllConfigsCacheAt = 0
        if c.SelectedConfig == j then c.SelectedConfig = nil end
        return true
    end

    function v.SetAutoload(c, q)
        if not ((c:EnsureStorage() and writefile)) then return false, "File API is not available" end
        local r = c:SanitizeName(q)
        if r == "" then return false, "Invalid config name" end
        local j, u = pcall(function() writefile(c:GetAutoloadPath(), r) end)
        if not j then return false, tostring(u) end
        return true
    end

    function v.ClearAutoload(q)
        local c = delfile or deletefile
        local r = q:GetAutoloadPath()
        if c and (isfile and isfile(r)) then
            pcall(function() c(r) end)
            return true
        end
        if writefile then
            pcall(function() writefile(r, "") end)
            return true
        end
        return false
    end

    function v.BuildConfigSection(c, q)
        local r = q:AddRightGroupbox("Config Manager")
        r:AddInput("ConfigNameInput",
            { Text = "Config Name", Default = c.InputConfigName, Placeholder = "my_config", Callback = function(q) v.InputConfigName =
                tostring(q or "") end })
        r:AddDropdown("ConfigListDropdown",
            { Text = "Saved Configs", Values = c:AllConfigs(), AllowNone = true, Callback = function(q)
                v.SelectedConfig = q
                local c = v:SanitizeName(q)
                if c ~= "" then
                    v.InputConfigName = c
                    local q = v.Library and (v.Library.Options and v.Library.Options.ConfigNameInput)
                    if q and type(q.SetValue) == "function" then pcall(function() q:SetValue(c) end) end
                end
            end })
        r:AddButton({ Text = "Refresh Configs", Func = function()
            local q = v:RefreshConfigDropdown()
            v:Notify("Config list: " .. (tostring(#q) .. (" (" .. (tostring(v:GetRootPath()) .. ")"))))
        end, DoubleClick = false })
        r:AddButton({ Text = "New Config", Func = function()
            local q = v:SanitizeName(v.InputConfigName)
            local c, r = v:SaveConfig(q, false)
            if c then
                v:RefreshConfigDropdown()
                local c = v:ResolveConfigPath(q)
                if c and c ~= "" then v:Notify("New config created: " .. (q .. (" [" .. (tostring(c) .. "]")))) else v
                        :Notify("New config created: " .. q) end
            else v:Notify("New config failed: " .. tostring(r)) end
        end, DoubleClick = false })
        r:AddButton({ Text = "Save Config", Func = function()
            local q = v:SanitizeName(v.InputConfigName)
            local c = v:SanitizeName(v.SelectedConfig)
            local r = q ~= "" and q or c
            local j, u = v:SaveConfig(r, true)
            if j then
                v:RefreshConfigDropdown()
                local q = v:ResolveConfigPath(r)
                if q and q ~= "" then v:Notify("Config saved: " .. (r .. (" [" .. (tostring(q) .. "]")))) else v:Notify(
                    "Config saved: " .. r) end
            else v:Notify("Save failed: " .. tostring(u)) end
        end, DoubleClick = false })
        r:AddButton({ Text = "Load Config", Func = function()
            if v._LoadBusy then
                v:Notify("Load in progress...")
                return
            end
            v._LoadBusy = true
            task.spawn(function()
                local q = v:SanitizeName(v.SelectedConfig or v.InputConfigName)
                if q == "" then
                    local c = v:RefreshConfigDropdown()
                    if #c > 0 then q = v:SanitizeName(c[1]) end
                end
                if q == "" then
                    v:Notify("No configs found in " .. tostring(v:GetRootPath()))
                    v._LoadBusy = false
                    return
                end
                local c, r = v:LoadConfig(q)
                if c then
                    v:RefreshConfigDropdown()
                    v:Notify("Config loaded: " .. q)
                else v:Notify("Load failed: " .. tostring(r)) end
                task.delay(.4, function() v._LoadBusy = false end)
            end)
        end, DoubleClick = false })
        r:AddButton({ Text = "Delete Config", Func = function()
            local q = v:SanitizeName(v.SelectedConfig or v.InputConfigName)
            local c, r = v:DeleteConfig(q)
            if c then
                v:RefreshConfigDropdown()
                v:Notify("Config deleted: " .. q)
            else v:Notify("Delete failed: " .. tostring(r)) end
        end, DoubleClick = false })
        r:AddButton({ Text = "Set Autoload", Func = function()
            local q = v:SanitizeName(v.SelectedConfig or v.InputConfigName)
            local c, r = v:SetAutoload(q)
            if c then v:Notify("Autoload set: " .. q) else v:Notify("Autoload failed: " .. tostring(r)) end
        end, DoubleClick = false })
        r:AddButton({ Text = "Clear Autoload", Func = function()
            v:ClearAutoload()
            v:Notify("Autoload cleared")
        end, DoubleClick = false })
        c:RefreshConfigDropdown()
    end

    function v.LoadAutoloadConfig(q)
        if not readfile then return end
        local c = { q:GetAutoloadPath(), "Wourld_Hub/autoload.txt", "Wourld_Hub/FlingThings/autoload.txt" }
        for c, r in ipairs(c) do
            local j = true
            if isfile then
                local q, c = pcall(function() return isfile(r) end)
                if q then j = c and true or false end
            end
            if j then
                local c, j = pcall(function() return readfile(r) end)
                if c then
                    local c = q:SanitizeName(j)
                    if c ~= "" then
                        q:LoadConfig(c)
                        return
                    end
                end
            end
        end
    end

    return j, a, v
end

function CreateLinoriaCompat()
    local q = "https://raw.githubusercontent.com/violin-suzutsuki/LinoriaLib/main/"
    local c = (loadstring(game:HttpGet(q .. "Library.lua")))()
    local r = (loadstring(game:HttpGet(q .. "addons/ThemeManager.lua")))()
    local j = (loadstring(game:HttpGet(q .. "addons/SaveManager.lua")))()
    local u = c.Notify
    local M = c.CreateWindow
    c.Scheme = c.Scheme or {}
    c.Scheme.FontColor = Color3.fromRGB(236, 236, 236)
    c.Scheme.MainColor = Color3.fromRGB(16, 16, 16)
    c.Scheme.AccentColor = Color3.fromRGB(220, 220, 220)
    c.Scheme.BackgroundColor = Color3.fromRGB(10, 10, 10)
    c.Scheme.OutlineColor = Color3.fromRGB(48, 48, 48)
    c.Font = Enum.Font.GothamSemibold
    c.MainColor = c.Scheme.MainColor
    c.AccentColor = c.Scheme.AccentColor
    c.BackgroundColor = c.Scheme.BackgroundColor
    c.OutlineColor = c.Scheme.OutlineColor
    c.FontColor = c.Scheme.FontColor
    c.AccentColorDark = c:GetDarkerColor(c.AccentColor)
    c.ShowCustomCursor = true
    c._NotifySide = "Right"
    c._DPIScale = 100
    if not c.KeybindFrame then c.KeybindFrame = { Visible = true } end
    function c.Notify(r, q, c)
        local j = tick()
        if type(q) == "table" then
            local c = tostring(q.Description or q.Content or q.Title or "Wourld Hub")
            local M = tonumber(q.Duration) or 4
            local G = tostring(q.Title or "Wourld Hub") .. ("|" .. c)
            if r._LastNotifyHash == G and (j - ((r._LastNotifyAt or 0))) < .75 then return end
            r._LastNotifyHash = G
            r._LastNotifyAt = j
            return u(r, c, M)
        end
        if type(q) == "string" then
            local M = "Wourld Hub|" .. q
            if r._LastNotifyHash == M and (j - ((r._LastNotifyAt or 0))) < .75 then return end
            r._LastNotifyHash = M
            r._LastNotifyAt = j
            return u(r, q, c)
        end
        local M = tostring(q or "")
        local G = "Wourld Hub|" .. M
        if r._LastNotifyHash == G and (j - ((r._LastNotifyAt or 0))) < .75 then return end
        r._LastNotifyHash = G
        r._LastNotifyAt = j
        return u(r, tostring(q or ""), c)
    end

    function c.SetNotifySide(c, q) c._NotifySide = tostring(q or "Right") end

    function c.SetDPIScale(c, q)
        local r = tonumber(q) or 100
        c._DPIScale = r
        local j = math.clamp(r / 100, .5, 2)
        if type(c.SetUIScale) == "function" then
            pcall(function() c:SetUIScale(j) end)
            return
        end
        if type(c.SetScale) == "function" then pcall(function() c:SetScale(j) end) end
    end

    function c.UpdateColorsUsingRegistry(q) end

    function c.CreateWindow(c, q)
        local r = q or {}
        local j = r.Size
        if typeof(j) ~= "UDim2" then j = UDim2.fromOffset(960, 700) end
        local u = M(c,
            { Title = tostring(r.Title or "Wourld Hub"), Center = r.Center ~= false, AutoShow = true, Size = j, Position =
            r.Position, TabPadding = 10, MenuFadeTime = .1 })
        c:SetDPIScale(c._DPIScale)
        return u
    end

    return c, r, j
end

local c, r, j = CreateObsidianCompat()
local u = c.Options
local M = c.Toggles
local G = game:GetService("Players")
local d = game:GetService("ReplicatedStorage")
local z = game:GetService("RunService")
local Y = game:GetService("Workspace")
local P = game:GetService("Lighting")
local O = game:GetService("UserInputService")
local a = game:GetService("SoundService")
local o = game:GetService("TextChatService")
local v = game:GetService("Stats")
local b = G.LocalPlayer
local X = {}
local x = "https://raw.githubusercontent.com/banhotopro-gif/icon/main/icon.png"
local U = "Wourld_Hub/assets/wourld_hub_icon.png"
local T = "https://raw.githubusercontent.com/github/explore/main/topics/github/github.png"
local e = "Wourld_Hub/assets/wourld_status_icon.png"
local h = 7733715400
local V = "WourldHub_1_0_1"
do
    local q = (type(getgenv) == "function" and getgenv()) or _G
    local c = q and rawget(q, "__WourldHubRuntimeGuard")
    if type(c) == "table" and (c.Active and tostring(c.BuildId) == V) then
        if c.Ready == true then return end
        local q = tick() - ((tonumber(c.StartedAt) or 0))
        if q < 12 then return end
        c.Active = false
        c.Ready = false
    end
    if q then q.__WourldHubRuntimeGuard = { Active = true, Ready = false, BuildId = V, StartedAt = tick() } end
end
local function t(q)
    if not ((makefolder and isfolder)) then return end
    local c = (tostring(q or "")):gsub("\\", "/")
    if c == "" then return end
    local r = ""
    for q in string.gmatch(c, "[^/]+") do
        r = (r == "") and q or (r .. ("/" .. q))
        local c, j = pcall(function() return isfolder(r) end)
        if (not c) or (not j) then pcall(function() makefolder(r) end) end
    end
end
local function B(q)
    if type(q) == "number" then return "rbxassetid://" .. tostring(q) end
    local c = tostring(q or "")
    if c:match("^%d+$") then return "rbxassetid://" .. c end
    return c
end
local function F()
    local q = h
    if writefile and (getcustomasset and (game and game.HttpGet)) then pcall(function()
            t("Wourld_Hub")
            t("Wourld_Hub/assets")
            local c = true
            if c then
                local q = game:HttpGet(x)
                writefile(U, q)
            end
            local r = getcustomasset(U)
            if type(r) == "string" and r ~= "" then q = r end
        end) end
    return q
end
h = F()
local function l()
    local q = B(h)
    if writefile and (getcustomasset and (game and game.HttpGet)) then pcall(function()
            t("Wourld_Hub")
            t("Wourld_Hub/assets")
            local c = game:HttpGet(T)
            writefile(e, c)
            local r = getcustomasset(e)
            if type(r) == "string" and r ~= "" then q = r end
        end) end
    return B(q)
end
local D = l()
if O.TouchEnabled then c._DPIScale = 130 end
local J = c:CreateWindow({ Title = "Wourld Hub", Footer = "Wourld Hub", Icon = h, NotifySide = "Right", ShowCustomCursor = true, EnableCompacting = false, SidebarCompacted = false, CornerRadius = 18, ShowMobileButtons = true, MobileButtonsSide =
O.TouchEnabled and "Right" or "Left", ToggleKeybind = Enum.KeyCode.RightShift })
local w = { Defence = J:AddTab("Defense", "shield"), Target = J:AddTab("Target", "crosshair"), Visuals = J:AddTab(
"Visuals", "eye"), Server = J:AddTab("Server", "server"), Miscs = J:AddTab("Misc", "sliders-horizontal"), Keybinds = J
:AddTab("Keybinds", "keyboard"), Owner = J:AddTab("Owner", "crown"), Credits = J:AddTab("Credits", "info"), UISettings =
J:AddTab("UI Settings", "settings") }
_G.ShurikenAntiKick = false
_G.AutoSitBlobZ = false
_G.AutoSitBloom = false
_G.AntiInputLag = false
_G.AntiGrab = false
_G.AntiExplosion = false
_G.AntiBurn = false
_G.AntiVoid = false
_G.AntiKick = false
_G.SuperSpeed = false
_G.InfiniteJump = false
_G.InfiniteJumpPower = 80
_G.KickAura = false
_G.KickAuraDebounce = false
_G.KickAuraType = "Silent"
_G.NoclipToggle = false
_G.NoclipGrab = false
_G.WourldCharacterBeamSafeMode = false
_G.WourldBeamSafeChildConnection = nil; (getgenv()).Multiplier = (getgenv()).Multiplier or .15
kickSelfGuardUntil = 0
local g = {}
local A = {}
local f = false
local H = false
local E = {}
local m = false
local i = 0
local L = false
local p = nil
local I = nil
local Q = {}
local y = nil
local W = nil
local R = nil
local k = nil
local K = nil
local Z = nil
local N = nil
local S = 20
local s = .3
local C = false
local n = 0
local qN = false
local cN = nil
local rN = false
local jN = nil
local uN = false
local MN = nil
local GN = 0
_G.WourldAntiInputLagItemFilter = _G.WourldAntiInputLagItemFilter or "All"
_G.WourldAntiInputLagItemFilterSecondary = _G.WourldAntiInputLagItemFilterSecondary or "FoodBread"
_G.WourldRemoveAntiKickAuraItem = _G.WourldRemoveAntiKickAuraItem or "All"
local dN = nil
local zN = nil
local YN = nil
local PN = nil
local ON = nil
local aN = nil
local oN = nil
local vN = nil
local bN = nil
local XN = nil
local xN = nil
local UN = nil
local TN = nil
local eN = nil
local hN = {}
local VN = { antiVoidMessageCooldown = 0, heldName = "", ragdollState = false, bodyVelocity = nil, connections = { Held = nil, Burn = nil, Ragdoll = nil, Humanoid = nil, Char = nil, Jump = nil, Speed = nil } }
local tN = false
local BN = {}
local FN = Color3.fromRGB(255, 255, 255)
local lN = { "partesp", "playercharacterlocationdetector" }
local DN = nil
local JN = nil
local wN = { usernameEnabled = false, usernameColor = Color3.fromRGB(95, 195, 255), usernameTask = nil, usernameBillboards = {}, showDisplayName = true, showDistance = true, showHealth = true, hitboxEnabled = false, hitboxScale = 1.7, hitboxTransparency = .55, hitboxColor =
Color3.fromRGB(255, 64, 64), hitboxTask = nil, hitboxData = {}, antiKickOwnerEnabled = false, antiKickOwnerColor = Color3
.fromRGB(255, 220, 84), antiKickOwnerTask = nil, antiKickOwnerBillboards = {}, antiKickOwnerNotify = false, antiKickOwnerKnown = {}, selfAntiKickVisualEnabled = true, selfAntiKickTask = nil, selfAntiKickAdornment = nil, selfAntiKickBillboard = nil, blizHighlightEnabled = false, blizHighlightTask = nil, blizHighlights = {}, blizHighlightFillColor =
Color3.fromRGB(82, 140, 255), blizHighlightOutlineColor = Color3.fromRGB(255, 255, 255), blizHighlightFillTransparency = .55, blizHighlightOutlineTransparency = .05, blizHighlightMode =
Enum.HighlightDepthMode.AlwaysOnTop, blizIconEnabled = false, blizIconTask = nil, blizIcons = {}, blizIconYOffset = 10, blizIconScale = 1, angelWingColor =
Color3.fromRGB(214, 214, 214), angelWingAccentColor = Color3.fromRGB(255, 255, 255), angelWingOffsetX = 1.4, angelWingOffsetY = .5, angelWingOffsetZ = .7, kickLineColorStart =
Color3.fromRGB(170, 170, 170), kickLineColorEnd = Color3.fromRGB(232, 232, 232), kickPulseColor = Color3.fromRGB(178, 178,
    178), kickRingColor = Color3.fromRGB(196, 196, 196), kickLineWidth = .2, kickRingRadius = 5.5 }
local gN = { friendJoinNotify = true, targetTrackName = nil, targetTrackPendingReturn = false, targetTrackCons = {}, cursorUnlock = false, kickAttempts = {}, kickAttemptMethods = {}, kickedPlayers = {}, ownKickNotifyAt = {}, kickSeenObjects = {}, kickSeenTrimAt = 0, forceNotifyLock = false, kickFxLastRingAt = 0, kickLineTimeoutSeconds = 8.5, loopBugAutoLeave = false, gucciKeyActive = false, mouse1DownAt = 0, antiInputLagManualUntil = 0, antiInputLagPulseRevision = 0, auraShield = false, auraShieldTask = nil, auraShieldRadius = 28, auraShieldGrabAlert = true, auraShieldKickAlert = true, auraShieldKillAlert = true, auraShieldNotifyAt = {}, targetHistory = {}, targetHistoryIndex = 0, smartDefence = false, smartDefenceTask = nil, smartAntiGrab = true, safeZoneReturn = true, trapEscape = true, velocityControl = true, velocityLimit = 140, counterAttack = true, counterAttackMode =
"Both", blobCounter = false, safeCFrame = nil, lastHealth = 100, lastCounter = 0, lastTrap = 0, lastDefenceNotify = 0, smartTargetAuto = false, smartTargetTask = nil, autoRetarget = true, distanceLock = false, distanceRange = 16, distanceTolerance = 4, triggerBot = false, triggerBotTask = nil, triggerRate = 6, triggerDistance = 24, triggerUseKick = false, dynamicEsp = false, threatHighlight = false, offscreenIndicator = false, focusMode =
"All", visualTask = nil, threatHighlights = {}, offscreenGui = nil, offscreenLabels = {}, orbit = false, orbitTask = nil, orbitRadius = 12, orbitSpeed = 1.8, autoEdgeSave = false, autoEdgeTask = nil, returnAfterAction = false, returnDelay = 45, actionCFrame = nil, smartTeleport = true, autoRejoin = false, autoRejoinConn = nil, autoRejoinTeleportFailConn = nil, autoRejoinRetryBusy = false, autoRejoinRetryAt = 0, musicEnabled = false, musicAutoNext = true, musicVolume = .55, musicPitch = 1, musicPlaylist = {}, musicIndex = 1, musicDropdownValues = {}, musicMap = {}, musicSound = nil, musicSoundConn = nil, musicUi = nil, musicUiVisible = true, musicUiConnection = nil, musicUiBeat = 0, musicUiAngle = 0, musicSelectedName =
"", musicFailedMap = {}, musicInputName = "", musicInputId = "", loopExplosionActive = false, loopExplosionTask = nil, loopExplosionItemA =
"BallSnowball", loopExplosionItemB = "CrystalSnowman", loopExplosionMode = "Alternate", loopExplosionInterval = .22, loopExplosionBurst = 2, loopExplosionVelocity = 540, loopExplosionPulse = 0, homeGuardActive = false, homeGuardTask = nil, homeGuardRadius = 120, homeGuardMode =
"UnderMap", buildPresetName = "base", buildPresetSelected = "base", buildPresetPathMap = {}, buildPresetValues = { "base" }, autoPerfMode = false, autoPerfTask = nil, perfThreshold = 35, perfApplied = false, switchServerMaxPlayers = 8, stats = { defenceSaves = 0, counterAttacks = 0, actions = 0, edgeSaves = 0 } }
packetLagNotifyEnabled = false
lastLagSource = false
packetLagDetectorStarted = false
packetLagConnection = nil
packetLagActive = false
kickActionBusy = false
loopKillActive = false
ownershipKickActive = false
ownershipRagdollActive = false
snowballRagdollActive = false
loopKickBlobActive = false
lagLineActive = false
antiOwnershipActive = false
loopTPActive = false
antiExplosionActive = false
antiBurnActive = false
antiVoidActive = false
tpEnabled = false
antiAntiKickActive = false
antiAntiLagEnabled = false
removeAntiKickAuraActive = false
removeAntiKickRadius = 15
useWhitelistRemoveAntiKick = true
gucciKey = Enum.KeyCode.J
gucciRunId = 0
tpKey = Enum.KeyCode.C
destroyGucciActive = false
gucciProtectActive = false
gucciProtectTask = nil
gucciProtectLastSeen = 0
gucciProtectLastRespawn = 0
gucciProtectSeenAny = false
antiGucciActive = false
antiGucciRadius = 70
antiGucciTask = nil
traceEnabled = false
traceColor = Color3.fromRGB(255, 0, 0)
antiBananaSitActive = false
killDodgeActive = false
flyingResetActive = false
antiRagBlobActive = false
antiBlobDenyActive = false
antiBlobDenyTask = nil
antiBlobDenyRadius = 220
antiBlobDenyMode = "UnderMap"
telekinesisShieldActive = false
telekinesisShieldTask = nil
breakPcldActive = false
breakPcldTask = nil
bloomFXEnabled = false
bloomFXIntensity = 2.2
bloomFXSize = 48
bloomFXThreshold = .9
neonAuraEnabled = false
neonAuraConnection = nil
accentPulseEnabled = false
accentPulseTask = nil
baseAccentColor = nil
fullBrightActive = false
noFogActive = false
timeLockActive = false
timeLockValue = 14
saturationFXActive = false
contrastFXActive = false
sunRaysFXActive = false
atmosphereOffActive = false
hidePlayersActive = false
hidePlayersTransparency = .85
hideAccessoriesActive = false
rainbowBodyActive = false
local AN = false
local fN = 30
local HN = nil
local EN = nil
local mN = false
local iN = 80
local LN = nil
local pN = false
local IN = nil
local QN = false
local yN = nil
local WN = false
local RN = nil
_G.WourldMovementResetBusy = false
local kN = false
local KN = 8
local ZN = nil
local NN = nil
local SN = false
local sN = 16
local CN = .8
local nN = 0
local q6 = false
local c6 = nil
local r6 = "AntiKick"
local j6 = 1
local u6 = {}
local M6 = nil
local G6 = 0
local d6 = nil
grabPoisonActive = false
grabRadioactiveActive = false
grabBurnActive = false
grabKillActive = false
grabFlingActive = false
grabNoclipModActive = false
grabCrazyActive = false
grabSpinActive = false
grabUltraActive = false
ultraClickGrabActive = false
lineInvisibleActive = false
lineExtendActive = false
lineCrazyPlayersActive = false
lineCrazyAllPartsActive = false
lineCrazyAllToysActive = false
grabLineModsTask = nil
grabLineHazardRefreshAt = 0
grabLineHazards = { poison = nil, radioactive = nil, burn = nil }
grabLineNextPulseAt = 0
grabModNextByUserId = {}
auraGrabActionActive = false
auraKillActionActive = false
auraBringActionActive = false
auraTpSpawnActionActive = false
auraFlingActionActive = false
auraActionTask = nil
auraActionRadius = 24
auraActionCooldown = .9
auraActionNextByUserId = {}
toyFreezeAuraActive = false
toyTpAuraActive = false
toyAuraTask = nil
toyAuraRadius = 24
toyAuraTpPos = CFrame.new(0, 1000, 0)
toyLoopSpawnActive = false
toyLoopSpawnTask = nil
toyLoopSpawnItem = "BombMissile"
toyLoopSpawnInterval = .25
toyExplodeDelay = .05
local z6 = false
local Y6 = "FoodHamburger"
local P6 = 800
local O6 = Enum.KeyCode.V
local a6 = "Plot1"
local o6 = 220
local v6 = false
local b6 = nil
local X6 = nil
local x6 = 0
local U6 = {}
local T6 = {}
local e6 = nil
local h6 = 1
local V6 = 0
local t6 = false
local B6 = false
local F6 = false
local l6 = false
local D6 = Y.Gravity
local J6 = Y.Gravity
local w6 = false
local g6 = nil
local A6 = nil
local f6 = 0
local H6 = 0
local E6 = false
local m6 = false
local i6 = false
local L6 = 0
local p6 = nil
local I6 = {}
local Q6 = 0
local y6 = false
local W6 = nil
local R6 = nil
local k6 = 0
local K6 = 0
local Z6 = 0
local N6 = 0
local S6 = 0
local s6 = false
local C6 = nil
local n6 = nil
githubHeadlessVisualActive = false
githubKorbloxVisualActive = false
githubKatanaHeadActive = false
githubHatHeadActive = false
githubVisualSpinActive = false
githubVisualSpinSpeed = 7
githubVisualAngle = 0
githubVisualCharacterConnection = nil
githubVisualLoopConnection = nil
githubKorbloxMeshId = "136273049724604"
githubKorbloxTextureId = "128312481343540"
uiClickSound = nil
localKickLineRun = 0
kickFxEnabled = true
angelAuraEnabled = false
angelAuraTask = nil
angelAuraModel = nil
angelLeftWeld = nil
angelRightWeld = nil
notifySoundEnabled = true
notifySoundVolume = .5
chatAnnouncementEnabled = true
uiFeedbackConnection = nil
uiVisualConnection = nil
uiVisualGradient = nil
menuOpenIconButton = nil
menuOpenIconConnections = {}
menuOpenTextButton = nil
menuOpenTextConnections = {}
phoneMenuButton = nil
phoneMenuConnections = {}
statusHudRoot = nil
statusHudLabel = nil
statusHudIcon = nil
statusHudConnections = {}
statusHudFpsAccumulator = 0
statusHudFpsFrames = 0
statusHudLastText = ""
menuWidgetSmoothness = 55
menuBackgroundEnabled = false
menuBackgroundSource = ""
menuBackgroundTransparency = .22
menuBackgroundFailNotifyAt = 0
menuCursorConnection = nil
menuCursorWatchConnection = nil
local qa = true
phoneModeEnabled = false
phoneModeScale = nil
phoneModeCache = {}
easterSequence = { Enum.KeyCode.Up, Enum.KeyCode.Up, Enum.KeyCode.Down, Enum.KeyCode.Down, Enum.KeyCode.Left, Enum
    .KeyCode.Right, Enum.KeyCode.Left, Enum.KeyCode.Right, Enum.KeyCode.B, Enum.KeyCode.A }
easterIndex = 1
blizIconTemplate = nil
eventSoundTemplate = nil
local ca = false
local ra = nil
local ja = false
local ua = {}
local Ma = {}
local Ga = false
local da = .2
local za = Color3.fromRGB(20, 20, 20)
local Ya = Enum.Material.SmoothPlastic
local Pa = nil
local Oa = {}
local aa = false
local oa = nil
local va = false
local ba = nil
local Xa = {}
local xa = 18
local Ua = 1.1
clickTpEnabled = false
clickTpKey = Enum.KeyCode.C
removeLegsKey = Enum.KeyCode.Y
removeArmsKey = Enum.KeyCode.U
removeLimbsKey = Enum.KeyCode.I
backtrackKey = Enum.KeyCode.B
freezeGrabKey = Enum.KeyCode.G
local Ta = false
local ea = nil
local ha = false
local Va = false
local ta = nil
task.defer(function()
    local q = b:FindFirstChild("PlayerScripts")
    local c = q and q:FindFirstChild("CharacterAndBeamMove")
    if c then c.Disabled = t6 and true or false end
    if _G.WourldBeamSafeChildConnection then
        _G.WourldBeamSafeChildConnection:Disconnect()
        _G.WourldBeamSafeChildConnection = nil
    end
    if q then _G.WourldBeamSafeChildConnection = q.ChildAdded:Connect(function(q) if q and (q.Name == "CharacterAndBeamMove" and q:IsA("LocalScript")) then q.Disabled =
                t6 and true or false end end) end
    autoloadInfiniteYield()
end)
function getMenuWidgetTweenTime()
    local q = math.clamp(tonumber(menuWidgetSmoothness) or 55, 0, 100)
    return q / 100
end

function setMenuWidgetPosition(q, c, r)
    if not q then return end
    local j = getMenuWidgetTweenTime()
    if r or j <= 0 then
        q.Position = c
        return
    end
    local u = math.clamp(j * .45, 0, 1)
    q.Position = q.Position:Lerp(c, u)
end

function destroyMenuOpenTextButton()
    if menuOpenTextConnections then
        for q, c in ipairs(menuOpenTextConnections) do if c and c.Disconnect then c:Disconnect() end end
        table.clear(menuOpenTextConnections)
    end
    if menuOpenTextButton then
        pcall(function() menuOpenTextButton:Destroy() end)
        menuOpenTextButton = nil
    end
end

function destroyMenuOpenIconButton()
    if menuOpenIconConnections then
        for q, c in ipairs(menuOpenIconConnections) do if c and c.Disconnect then c:Disconnect() end end
        table.clear(menuOpenIconConnections)
    end
    if menuOpenIconButton then
        pcall(function() menuOpenIconButton:Destroy() end)
        menuOpenIconButton = nil
    end
end

function destroyPhoneMenuButton()
    if phoneMenuConnections then
        for q, c in ipairs(phoneMenuConnections) do if c and c.Disconnect then c:Disconnect() end end
        table.clear(phoneMenuConnections)
    end
    if phoneMenuButton then
        pcall(function() phoneMenuButton:Destroy() end)
        phoneMenuButton = nil
    end
end

function destroyStatusHud()
    if statusHudConnections then
        for q, c in ipairs(statusHudConnections) do if c and c.Disconnect then c:Disconnect() end end
        table.clear(statusHudConnections)
    end
    if statusHudRoot then
        pcall(function() statusHudRoot:Destroy() end)
        statusHudRoot = nil
    end
    statusHudLabel = nil
    statusHudIcon = nil
    statusHudFpsAccumulator = 0
    statusHudFpsFrames = 0
    statusHudLastText = ""
end

function getStatusPingText()
    local q = nil
    pcall(function()
        local c = v.Network.ServerStatsItem["Data Ping"]:GetValue()
        if type(c) == "number" then q = math.floor(c + .5) end
    end)
    if not q then pcall(function()
            local c = tostring(v.Network.ServerStatsItem["Data Ping"]:GetValueString() or "")
            local r = tonumber(c:match("%d+%.?%d*"))
            if r then q = math.floor(r + .5) end
        end) end
    return tostring(q or "?") .. " ms"
end

function updateStatusHud(q)
    statusHudFpsAccumulator = ((tonumber(statusHudFpsAccumulator) or 0)) + math.max(tonumber(q) or 0, 0)
    statusHudFpsFrames = ((tonumber(statusHudFpsFrames) or 0)) + 1
    if statusHudFpsAccumulator < .32 then return end
    local r = math.floor((statusHudFpsFrames / math.max(statusHudFpsAccumulator, .0041666666666667)) + .5)
    statusHudFpsAccumulator = 0
    statusHudFpsFrames = 0
    local j = "Wourld Hub | " .. (tostring(math.max(r, 1)) .. (" fps | " .. getStatusPingText()))
    if statusHudLabel and statusHudLabel.Parent then statusHudLabel.Text = j end
    if j ~= statusHudLastText then
        statusHudLastText = j
        pcall(function() c:SetWatermark(j) end)
    end
end

function createStatusHud()
    destroyStatusHud()
    local q = c.ScreenGui
    if not q or not q.Parent then return end
    local r = O.TouchEnabled and (not O.MouseEnabled)
    local j = Instance.new("Frame")
    j.Name = "WourldStatusHud"
    j.AnchorPoint = Vector2.new(0, 0)
    j.Position = r and UDim2.new(0, 10, 0, 10) or UDim2.new(0, 12, 0, 10)
    j.Size = r and UDim2.fromOffset(310, 36) or UDim2.fromOffset(285, 32)
    j.BackgroundColor3 = Color3.fromRGB(14, 14, 18)
    j.BackgroundTransparency = .04
    j.BorderSizePixel = 0
    j.ZIndex = 2002
    j.Parent = q
    local u = Instance.new("UICorner")
    u.CornerRadius = UDim.new(0, 18)
    u.Parent = j
    local M = Instance.new("UIStroke")
    M.Thickness = r and 1.7 or 1.4
    M.Color = Color3.fromRGB(170, 170, 170)
    M.Transparency = .2
    M.Parent = j
    local G = Instance.new("ImageLabel")
    G.Name = "StatusIcon"
    G.BackgroundTransparency = 1
    G.AnchorPoint = Vector2.new(0, .5)
    G.Position = UDim2.new(0, 10, .5, 0)
    G.Size = r and UDim2.fromOffset(18, 18) or UDim2.fromOffset(16, 16)
    G.Image = B(D)
    G.ImageColor3 = Color3.fromRGB(238, 238, 238)
    G.ZIndex = j.ZIndex + 1
    G.Parent = j
    local d = Instance.new("TextLabel")
    d.Name = "StatusText"
    d.BackgroundTransparency = 1
    d.AnchorPoint = Vector2.new(0, .5)
    d.Position = UDim2.new(0, 34, .5, 0)
    d.Size = UDim2.new(1, -38, 1, 0)
    d.TextXAlignment = Enum.TextXAlignment.Left
    d.TextYAlignment = Enum.TextYAlignment.Center
    d.Font = Enum.Font.GothamSemibold
    d.TextSize = r and 15 or 13
    d.TextColor3 = Color3.fromRGB(235, 235, 235)
    d.Text = "Wourld Hub | ... fps | ... ms"
    d.ZIndex = j.ZIndex + 1
    d.Parent = j
    local z = false
    local Y = nil
    local P = nil
    local a = nil
    table.insert(statusHudConnections,
        j.InputBegan:Connect(function(q) if q.UserInputType == Enum.UserInputType.MouseButton1 or q.UserInputType == Enum.UserInputType.Touch then
                z = true
                P = q.Position
                a = j.Position
            end end))
    table.insert(statusHudConnections,
        j.InputEnded:Connect(function(q) if q.UserInputType == Enum.UserInputType.MouseButton1 or q.UserInputType == Enum.UserInputType.Touch then z = false end end))
    table.insert(statusHudConnections,
        j.InputChanged:Connect(function(q) if q.UserInputType == Enum.UserInputType.MouseMovement or q.UserInputType == Enum.UserInputType.Touch then Y =
                q end end))
    table.insert(statusHudConnections,
        O.InputChanged:Connect(function(q) if z and (Y and (q == Y and (P and a))) then
                local c = q.Position - P
                setMenuWidgetPosition(j, UDim2.new(a.X.Scale, a.X.Offset + c.X, a.Y.Scale, a.Y.Offset + c.Y), false)
            end end))
    statusHudRoot = j
    statusHudLabel = d
    statusHudIcon = G
    updateStatusHud(.34)
end

function createPhoneMenuButton()
    destroyPhoneMenuButton()
    local r = c.ScreenGui
    if not r or not r.Parent then return end
    local j = O.TouchEnabled and (not O.MouseEnabled)
    if not ((j or phoneModeEnabled)) then return end
    local u = Instance.new("TextButton")
    u.Name = "WourldPhoneMenuButton"
    u.AnchorPoint = Vector2.new(0, 0)
    u.Position = UDim2.new(0, 10, 0, 54)
    u.Size = UDim2.fromOffset(84, 30)
    u.BackgroundColor3 = Color3.fromRGB(14, 14, 18)
    u.BackgroundTransparency = .06
    u.BorderSizePixel = 0
    u.AutoButtonColor = false
    u.Text = "Menu"
    u.TextColor3 = Color3.fromRGB(238, 238, 238)
    u.TextSize = 14
    u.Font = Enum.Font.GothamSemibold
    u.ZIndex = 2002
    u.Parent = r
    local M = Instance.new("UICorner")
    M.CornerRadius = UDim.new(0, 14)
    M.Parent = u
    local G = Instance.new("UIStroke")
    G.Thickness = 1.4
    G.Color = Color3.fromRGB(170, 170, 170)
    G.Transparency = .2
    G.Parent = u
    table.insert(phoneMenuConnections, u.MouseButton1Click:Connect(function() pcall(function() q:Toggle() end) end))
    local d = false
    local z = nil
    local Y = nil
    local P = nil
    table.insert(phoneMenuConnections,
        u.InputBegan:Connect(function(q) if q.UserInputType == Enum.UserInputType.MouseButton1 or q.UserInputType == Enum.UserInputType.Touch then
                d = true
                Y = q.Position
                P = u.Position
            end end))
    table.insert(phoneMenuConnections,
        u.InputEnded:Connect(function(q) if q.UserInputType == Enum.UserInputType.MouseButton1 or q.UserInputType == Enum.UserInputType.Touch then d = false end end))
    table.insert(phoneMenuConnections,
        u.InputChanged:Connect(function(q) if q.UserInputType == Enum.UserInputType.MouseMovement or q.UserInputType == Enum.UserInputType.Touch then z =
                q end end))
    table.insert(phoneMenuConnections,
        O.InputChanged:Connect(function(q) if d and (z and (q == z and (Y and P))) then
                local c = q.Position - Y
                setMenuWidgetPosition(u, UDim2.new(P.X.Scale, P.X.Offset + c.X, P.Y.Scale, P.Y.Offset + c.Y), false)
            end end))
    phoneMenuButton = u
end

function createMenuOpenTextButton()
    destroyMenuOpenTextButton()
    local r = c.ScreenGui
    if not r or not r.Parent then return end
    local j = O.TouchEnabled and (not O.MouseEnabled)
    local u = Instance.new("TextButton")
    u.Name = "WourldMenuOpenText"
    u.AnchorPoint = Vector2.new(0, 0)
    u.Position = j and UDim2.new(0, 12, 0, 92) or UDim2.new(0, 10, 0, 68)
    u.Size = j and UDim2.fromOffset(250, 36) or UDim2.fromOffset(216, 32)
    u.BackgroundColor3 = Color3.fromRGB(14, 14, 18)
    u.BackgroundTransparency = .06
    u.Text = ""
    u.BorderSizePixel = 0
    u.AutoButtonColor = false
    u.ZIndex = 1998
    u.Parent = r
    local M = Instance.new("UICorner")
    M.CornerRadius = UDim.new(0, 16)
    M.Parent = u
    local G = Instance.new("UIStroke")
    G.Thickness = j and 1.6 or 1.3
    G.Color = Color3.fromRGB(170, 170, 170)
    G.Transparency = .2
    G.Parent = u
    local d = Instance.new("ImageLabel")
    d.Name = "Icon"
    d.BackgroundTransparency = 1
    d.AnchorPoint = Vector2.new(0, .5)
    d.Position = UDim2.new(0, 12, .5, 0)
    d.Size = j and UDim2.fromOffset(20, 20) or UDim2.fromOffset(18, 18)
    d.Image = B(h)
    d.ImageColor3 = Color3.fromRGB(232, 232, 232)
    d.ZIndex = u.ZIndex + 1
    d.Parent = u
    local z = Instance.new("TextLabel")
    z.Name = "Title"
    z.BackgroundTransparency = 1
    z.AnchorPoint = Vector2.new(0, .5)
    z.Position = UDim2.new(0, 38, .5, 0)
    z.Size = UDim2.new(1, -44, 1, 0)
    z.TextXAlignment = Enum.TextXAlignment.Left
    z.Font = Enum.Font.GothamSemibold
    z.Text = "Wourld Hub"
    z.TextColor3 = Color3.fromRGB(232, 232, 232)
    z.TextSize = j and 20 or 18
    z.ZIndex = u.ZIndex + 1
    z.Parent = u
    table.insert(menuOpenTextConnections, u.MouseButton1Click:Connect(function() pcall(function() q:Toggle() end) end))
    local Y = false
    local P = nil
    local a = nil
    local o = nil
    table.insert(menuOpenTextConnections,
        u.InputBegan:Connect(function(q) if q.UserInputType == Enum.UserInputType.MouseButton1 or q.UserInputType == Enum.UserInputType.Touch then
                Y = true
                a = q.Position
                o = u.Position
            end end))
    table.insert(menuOpenTextConnections,
        u.InputEnded:Connect(function(q) if q.UserInputType == Enum.UserInputType.MouseButton1 or q.UserInputType == Enum.UserInputType.Touch then Y = false end end))
    table.insert(menuOpenTextConnections,
        u.InputChanged:Connect(function(q) if q.UserInputType == Enum.UserInputType.MouseMovement or q.UserInputType == Enum.UserInputType.Touch then P =
                q end end))
    table.insert(menuOpenTextConnections,
        O.InputChanged:Connect(function(q) if Y and (P and (q == P and (a and o))) then
                local c = q.Position - a
                setMenuWidgetPosition(u, UDim2.new(o.X.Scale, o.X.Offset + c.X, o.Y.Scale, o.Y.Offset + c.Y), false)
            end end))
    menuOpenTextButton = u
end

function createMenuOpenIconButton()
    destroyMenuOpenIconButton()
    local r = c.ScreenGui
    if not r or not r.Parent then return end
    local j = O.TouchEnabled and (not O.MouseEnabled)
    local u = Instance.new("ImageButton")
    u.Name = "WourldMenuOpenIcon"
    u.AnchorPoint = Vector2.new(1, 0)
    u.Position = j and UDim2.new(1, -16, 0, 86) or UDim2.new(1, -18, 0, 60)
    u.Size = j and UDim2.fromOffset(58, 58) or UDim2.fromOffset(46, 46)
    u.BackgroundColor3 = Color3.fromRGB(14, 14, 16)
    u.BackgroundTransparency = .08
    u.BorderSizePixel = 0
    u.AutoButtonColor = false
    u.ScaleType = Enum.ScaleType.Fit
    u.Image = B(h)
    u.ZIndex = 2000
    u.Parent = r
    local M = Instance.new("UICorner")
    M.CornerRadius = UDim.new(0, j and 16 or 13)
    M.Parent = u
    local G = Instance.new("UIStroke")
    G.Thickness = j and 1.7 or 1.4
    G.Color = Color3.fromRGB(170, 170, 170)
    G.Transparency = .2
    G.Parent = u
    local d = Instance.new("UIAspectRatioConstraint")
    d.AspectRatio = 1
    d.Parent = u
    table.insert(menuOpenIconConnections, u.MouseButton1Click:Connect(function() pcall(function() q:Toggle() end) end))
    local z = false
    local Y = nil
    local P = nil
    local a = nil
    table.insert(menuOpenIconConnections,
        u.InputBegan:Connect(function(q) if q.UserInputType == Enum.UserInputType.MouseButton1 or q.UserInputType == Enum.UserInputType.Touch then
                z = true
                P = q.Position
                a = u.Position
            end end))
    table.insert(menuOpenIconConnections,
        u.InputEnded:Connect(function(q) if q.UserInputType == Enum.UserInputType.MouseButton1 or q.UserInputType == Enum.UserInputType.Touch then z = false end end))
    table.insert(menuOpenIconConnections,
        u.InputChanged:Connect(function(q) if q.UserInputType == Enum.UserInputType.MouseMovement or q.UserInputType == Enum.UserInputType.Touch then Y =
                q end end))
    table.insert(menuOpenIconConnections,
        O.InputChanged:Connect(function(q) if z and (Y and (q == Y and (P and a))) then
                local c = q.Position - P
                setMenuWidgetPosition(u, UDim2.new(a.X.Scale, a.X.Offset + c.X, a.Y.Scale, a.Y.Offset + c.Y), false)
            end end))
    menuOpenIconButton = u
    createMenuOpenTextButton()
    createPhoneMenuButton()
end

function setupPremiumInterface()
    if uiFeedbackConnection then
        uiFeedbackConnection:Disconnect()
        uiFeedbackConnection = nil
    end
    if uiVisualConnection then
        uiVisualConnection:Disconnect()
        uiVisualConnection = nil
    end
    uiVisualGradient = nil
    pcall(function() c:SetWatermark("Wourld Hub") end)
    local q = c.ScreenGui
    if not q then return end
    createMenuOpenIconButton()
    createStatusHud()
    task.delay(.05, function() applyMenuBackgroundState() end)
    if uiClickSound then
        uiClickSound:Destroy()
        uiClickSound = nil
    end
    uiClickSound = Instance.new("Sound")
    uiClickSound.Name = "WourldUIClick"
    uiClickSound.SoundId = "rbxassetid://9118828568"
    uiClickSound.Volume = .2
    uiClickSound.PlaybackSpeed = 1.05
    uiClickSound.RollOffMaxDistance = 40
    uiClickSound.Parent = q
    uiFeedbackConnection = O.InputBegan:Connect(function(c, r)
        if r then return end
        if c.UserInputType ~= Enum.UserInputType.MouseButton1 then return end
        local j = false
        for q, c in ipairs(q:GetChildren()) do if c:IsA("Frame") and c.Visible then
                j = true
                break
            end end
        if j and uiClickSound then pcall(function() uiClickSound:Play() end) end
    end)
    task.delay(.3,
        function()
            if not q.Parent then return end
            local c = nil
            local r = 0
            for q, j in ipairs(q:GetChildren()) do if j:IsA("Frame") then
                    local q = j.AbsoluteSize.X * j.AbsoluteSize.Y
                    if q > r then
                        r = q
                        c = j
                    end
                end end
            if not c then return end
            local j = c:FindFirstChild("WourldMainCorner")
            if not j then
                j = Instance.new("UICorner")
                j.Name = "WourldMainCorner"
                j.CornerRadius = UDim.new(0, 14)
                j.Parent = c
            end
            local u = c:FindFirstChild("WourldMainStroke")
            if not u then
                u = Instance.new("UIStroke")
                u.Name = "WourldMainStroke"
                u.Thickness = 1.6
                u.Color = Color3.fromRGB(176, 176, 176)
                u.Transparency = .2
                u.Parent = c
            end
            local M = c:FindFirstChild("WourldMainGradient")
            if not M then
                M = Instance.new("UIGradient")
                M.Name = "WourldMainGradient"
                M.Color = ColorSequence.new({ ColorSequenceKeypoint.new(0, Color3.fromRGB(10, 10, 10)),
                    ColorSequenceKeypoint.new(.45, Color3.fromRGB(22, 22, 22)), ColorSequenceKeypoint.new(1,
                    Color3.fromRGB(14, 14, 14)) })
                M.Rotation = 140
                M.Parent = c
            end
            uiVisualGradient = M
            for q, c in ipairs(c:GetDescendants()) do if c:IsA("Frame") then
                    c.BorderSizePixel = 0
                    local q = c.AbsoluteSize
                    if q.X >= 70 and (q.Y >= 20 and not c:FindFirstChild("WourldCorner")) then
                        local q = Instance.new("UICorner")
                        q.Name = "WourldCorner"
                        q.CornerRadius = UDim.new(0, 8)
                        q.Parent = c
                    end
                end end
        end)
    uiVisualConnection = z.Heartbeat:Connect(function(q)
        if uiVisualGradient then uiVisualGradient.Rotation = ((uiVisualGradient.Rotation + q * 9)) % 360 end
        updateStatusHud(q)
    end)
end

function syncNotifySoundSettings()
    notifySoundEnabled = notifySoundEnabled and true or false
    c._NotifySoundEnabled = notifySoundEnabled
    c._NotifySoundVolume = math.clamp(tonumber(notifySoundVolume) or .5, 0, 1)
end

function ensureEventSoundTemplate()
    if eventSoundTemplate and eventSoundTemplate.Parent then return eventSoundTemplate end
    local q = Instance.new("Sound")
    q.Name = "WourldEventSound"
    q.Volume = math.clamp(((tonumber(notifySoundVolume) or .5)) * .95, 0, 1)
    q.PlayOnRemove = false
    q.Parent = a
    eventSoundTemplate = q
    return q
end

function playEventSound(q)
    if not notifySoundEnabled then return end
    local c = { friend = "rbxasset://sounds/electronicpingshort.wav", packet =
    "rbxasset://sounds/electronicpingshort.wav", kick = "rbxasset://sounds/electronicpingshort.wav", reset =
    "rbxasset://sounds/electronicpingshort.wav" }
    local r = { friend = 1.08, packet = .96, kick = .82, reset = 1 }
    local j = ensureEventSoundTemplate()
    local u = j:Clone()
    u.SoundId = c[q] or c.packet
    u.PlaybackSpeed = r[q] or 1
    u.Volume = math.clamp(((tonumber(notifySoundVolume) or .5)) * .9, 0, 1)
    u.Parent = a
    local M = false
    pcall(function()
        a:PlayLocalSound(u)
        M = true
    end)
    if not M then pcall(function() u:Play() end) end
    task.delay(3, function() if u and u.Parent then u:Destroy() end end)
end

function normalizeMusicTrackId(q)
    local c = ((tostring(q or "")):gsub("^%s+", "")):gsub("%s+$", "")
    if c == "" then return "" end
    local r = string.lower(c)
    if r:find("rbxassetid://", 1, true) == 1 or r:find("rbxasset://", 1, true) == 1 then return c end
    if r:find("http://", 1, true) == 1 or r:find("https://", 1, true) == 1 then return c end
    local j = c:match("(%d+)")
    if j and j ~= "" then return "rbxassetid://" .. tostring(j) end
    return ""
end

function getBuiltInMusicSeeds() return { { Name = "my destiny", Id = "rbxassetid://1843529609" }, { Name = "utro", Id = "rbxassetid://1843529618" }, { Name = "bas da da da phonk", Id = "rbxassetid://1843529635" }, { Name = "Sick & Tired 1", Id = "rbxassetid://1843529629" }, { Name = "Sick & Tired 2", Id = "rbxassetid://1843529644" }, { Name = "Sick & Tired 3", Id = "rbxassetid://1843529654" } } end

function getMusicFolderCandidates()
    local q = {}
    local c = {}
    local function r(r)
        local j = ((tostring(r or "")):gsub("\\", "/")):gsub("/+$", "")
        local u = string.lower(j)
        if j ~= "" and not c[u] then
            c[u] = true
            q[#q + 1] = j
        end
    end
    r("Wourld_Hub/FlingThings/music")
    r("wourld_hub/FlingThings/music")
    r("workspace/Wourld_Hub/FlingThings/music")
    r("workspace/wourld_hub/FlingThings/music")
    r("Wourld_Hub/FlingThings/game-config/music")
    r("wourld_hub/FlingThings/game-config/music")
    r("workspace/Wourld_Hub/FlingThings/game-config/music")
    r("workspace/wourld_hub/FlingThings/game-config/music")
    r("Wourld_Hub/FlingThings/game-config")
    r("wourld_hub/FlingThings/game-config")
    r("workspace/Wourld_Hub/FlingThings/game-config")
    r("workspace/wourld_hub/FlingThings/game-config")
    if j and j.GetCandidateRootPaths then
        local q, c = pcall(function() return j:GetCandidateRootPaths() end)
        if q and type(c) == "table" then for q, c in ipairs(c) do
                local j = (tostring(c or "")):gsub("\\", "/")
                if j ~= "" then
                    r(j)
                    r(j .. "/music")
                end
            end end
    end
    if listfiles then
        local q = {}
        local c = {}
        local function u(c, r)
            local j = ((tostring(c or "")):gsub("\\", "/")):gsub("/+$", "")
            if j ~= "" then q[#q + 1] = { path = j, depth = r or 0 } end
        end
        local function M(q)
            local c = string.lower((tostring(q or "")):gsub("\\", "/"))
            return c:find("/music", 1, true) ~= nil or c:find("game%-config") ~= nil
        end
        u("workspace", 0)
        u("Workspace", 0)
        u("Wourld_Hub", 0)
        u("wourld_hub", 0)
        if j and j.GetRootPath then
            local q, c = pcall(function() return j:GetRootPath() end)
            if q and (type(c) == "string" and c ~= "") then u(c, 0) end
        end
        local G = 1
        local d = 4
        local z = 480
        local Y = 0
        while G <= #q and Y < z do
            local j = q[G]
            G = G + 1
            local P = string.lower((tostring(j.path or "")):gsub("\\", "/"))
            if not c[P] then
                c[P] = true
                local q, G = pcall(function() return listfiles(j.path) end)
                if q and type(G) == "table" then
                    if M(j.path) then
                        r(j.path)
                        r(j.path .. "/music")
                    end
                    for q, c in ipairs(G) do
                        Y = Y + 1
                        local G = ((tostring(c or "")):gsub("\\", "/")):gsub("/+$", "")
                        if G ~= "" then
                            local q = string.lower(G)
                            if M(G) then
                                r(G)
                                r(G .. "/music")
                            end
                            if j.depth < d and ((q:find("workspace", 1, true) or q:find("wourld", 1, true) or q:find("fling", 1, true) or q:find("music", 1, true) or q:find("config", 1, true))) then
                                local q = pcall(function() return listfiles(G) end)
                                if q then u(G, j.depth + 1) end
                            end
                        end
                        if Y >= z then break end
                    end
                end
            end
        end
    end
    return q
end

function ensureMusicFolder()
    if isfolder then for q, c in ipairs(getMusicFolderCandidates()) do
            local r, j = pcall(function() return isfolder(c) end)
            if r and (j and not (string.lower(c)):match("/game%-config$")) then return c end
        end end
    if not ((isfolder and makefolder)) then return nil end
    local q = "Wourld_Hub/FlingThings/game-config/music"
    if j and j.GetRootPath then
        local c, r = pcall(function() return j:GetRootPath() end)
        if c and (type(r) == "string" and r ~= "") then q = (tostring(r)):gsub("\\", "/") .. "/music" end
    end
    local c = ""
    for q in q:gmatch("[^/]+") do
        if c == "" then c = q else c = c .. ("/" .. q) end
        local r, j = pcall(function() return isfolder(c) end)
        if (not r) or (not j) then pcall(function() makefolder(c) end) end
    end
    local r, u = pcall(function() return isfolder(q) end)
    if r and u then return q end
    return nil
end

function isMusicAudioFilePath(q)
    local c = string.lower((tostring(q or "")):match("%.([%w]+)$") or "")
    return c == "mp3" or c == "ogg" or c == "wav"
end

function isMusicTextFilePath(q)
    local c = string.lower((tostring(q or "")):match("%.([%w]+)$") or "")
    return c == "txt" or c == "cfg" or c == "json" or c == "lua" or c == "luau" or c == ""
end

function resolveMusicLocalAsset(q)
    local c = getcustomasset or getsynasset
    if not c then return "" end
    local r, j = pcall(function() return c(q) end)
    if r and (type(j) == "string" and j ~= "") then return normalizeMusicTrackId(j) end
    return ""
end

function collectMusicSourceFiles()
    local q = {}
    local c = {}
    local function r(r)
        local j = ((tostring(r or "")):gsub("\\", "/")):gsub("/+$", "")
        local u = string.lower(j)
        if j ~= "" and not c[u] then
            c[u] = true
            q[#q + 1] = j
        end
    end
    local j = { "music_tracks.txt", "custom_tracks.txt", "tracks.json" }
    local u = {}
    local M = {}
    local function G(q, c)
        local r = ((tostring(q or "")):gsub("\\", "/")):gsub("/+$", "")
        if r ~= "" then u[#u + 1] = { path = r, depth = c or 0 } end
    end
    for q, c in ipairs(getMusicFolderCandidates()) do G(c, 0) end
    local d = 1
    local z = 4
    local Y = 600
    local P = 0
    while d <= #u and P < Y do
        local q = u[d]
        d = d + 1
        local c = string.lower((tostring(q.path or "")):gsub("\\", "/"))
        if not M[c] then
            M[c] = true
            if isfile then for c, j in ipairs(j) do
                    local u = tostring(q.path) .. ("/" .. j)
                    local M, G = pcall(function() return isfile(u) end)
                    if M and G then r(u) end
                end end
            if listfiles then
                local c, j = pcall(function() return listfiles(q.path) end)
                if c and type(j) == "table" then for c, j in ipairs(j) do
                        P = P + 1
                        local u = ((tostring(j or "")):gsub("\\", "/")):gsub("/+$", "")
                        if u ~= "" then
                            local c = string.lower(u)
                            local j = false
                            if isfile then
                                local q, c = pcall(function() return isfile(u) end)
                                j = q and c
                            else j = u:find("%.%w+$") ~= nil end
                            if j then if isMusicAudioFilePath(u) or isMusicTextFilePath(u) then r(u) end elseif q.depth < z and ((c:find("music", 1, true) or c:find("config", 1, true) or c:find("workspace", 1, true) or c:find("wourld", 1, true) or c:find("fling", 1, true))) then
                                local c = pcall(function() return listfiles(u) end)
                                if c then G(u, q.depth + 1) end
                            end
                        end
                        if P >= Y then break end
                    end end
            end
        end
    end
    return q
end

function refreshMusicDropdownValues()
    local q = {}
    local c = {}
    for r, j in ipairs(gN.musicPlaylist or {}) do if type(j) == "table" then
            local r = tostring(j.Name or "")
            if r ~= "" then
                local j = string.lower(r)
                if not c[j] then
                    c[j] = true
                    q[#q + 1] = r
                end
            end
        end end
    if #q == 0 then q[1] = "No tracks" end
    gN.musicDropdownValues = q
    local r = tostring(gN.musicSelectedName or "")
    if r == "" or not table.find(q, r) then
        r = q[1]
        gN.musicSelectedName = r
    end
    local j = u and u.MusicTrackDropdown
    if j and j.SetValues then pcall(function() j:SetValues(q) end) end
    if j and j.SetValue then
        gN.musicDropdownSync = true
        pcall(function() j:SetValue(r) end)
        gN.musicDropdownSync = false
    end
end

function rebuildMusicPlaylist()
    gN.musicPlaylist = {}
    gN.musicMap = {}
    for q, c in ipairs(getBuiltInMusicSeeds()) do upsertMusicTrack(c.Name, c.Id, "builtin") end
    local q = collectMusicSourceFiles()
    for q, c in ipairs(q) do
        local r = (tostring(c or "")):gsub("\\", "/")
        if r ~= "" then
            local q = r:match("([^/]+)$") or "custom"
            local c = q:gsub("%.%w+$", "")
            if isMusicAudioFilePath(r) then
                local q = resolveMusicLocalAsset(r)
                if q ~= "" then upsertMusicTrack(c, q, "local-audio") end
            elseif readfile and isMusicTextFilePath(r) then
                local j, u = pcall(function() return readfile(r) end)
                if j and (type(u) == "string" and #u > 0) then
                    local r = u:gsub("^\239\187\191", "")
                    local j = string.lower(r:gsub("^%s+", ""))
                    if j:sub(1, 1) == "{" or j:sub(1, 1) == "[" then
                        local c, j = pcall(function() return (game:GetService("HttpService")):JSONDecode(r) end)
                        if c and type(j) == "table" then if j.tracks and type(j.tracks) == "table" then for c, r in ipairs(j.tracks) do if type(r) == "table" then
                                        upsertMusicTrack(r.name or r.Name, r.id or r.Id, q) end end else for c, r in ipairs(j) do if type(r) == "table" then
                                        upsertMusicTrack(r.name or r.Name, r.id or r.Id, q) end end end end
                    end
                    for r in r:gmatch("[^\r\n]+") do parseMusicLine(r, c, q) end
                end
            end
        end
    end
    if gN.musicIndex < 1 or gN.musicIndex > #gN.musicPlaylist then gN.musicIndex = 1 end
    if #gN.musicPlaylist > 0 then
        local q = gN.musicPlaylist[gN.musicIndex]
        gN.musicSelectedName = tostring((q and q.Name) or "")
    else gN.musicSelectedName = "No tracks" end
    refreshMusicDropdownValues()
    updateMusicWidgetText()
end

function ensureMusicSound()
    if gN.musicSound and gN.musicSound.Parent then
        gN.musicSound.Volume = math.clamp(tonumber(gN.musicVolume) or .55, 0, 1)
        gN.musicSound.PlaybackSpeed = math.clamp(tonumber(gN.musicPitch) or 1, .5, 2)
        return gN.musicSound
    end
    if gN.musicSoundConn then
        pcall(function() gN.musicSoundConn:Disconnect() end)
        gN.musicSoundConn = nil
    end
    local q = Instance.new("Sound")
    q.Name = "WourldMusicPlayer"
    q.Volume = math.clamp(tonumber(gN.musicVolume) or .55, 0, 1)
    q.PlaybackSpeed = math.clamp(tonumber(gN.musicPitch) or 1, .5, 2)
    q.Looped = false
    q.RollOffMode = Enum.RollOffMode.Linear
    q.RollOffMaxDistance = 40
    q.Parent = a
    gN.musicSound = q
    gN.musicSoundConn = q.Ended:Connect(function() if gN.musicEnabled and gN.musicAutoNext then playNextMusicTrack() else
            updateMusicWidgetText() end end)
    return q
end

function getCurrentMusicTrack()
    local q = gN.musicPlaylist or {}
    if #q == 0 then return nil end
    local c = math.clamp(tonumber(gN.musicIndex) or 1, 1, #q)
    gN.musicIndex = c
    return q[c], c
end

function getMusicTrackByName(q)
    local c = string.lower(tostring(q or ""))
    local r = gN.musicMap and gN.musicMap[c]
    if r and gN.musicPlaylist[r] then return gN.musicPlaylist[r], r end
    return nil, nil
end

function getMusicTrackFailKey(q)
    if type(q) ~= "table" then return "" end
    return string.lower(tostring(q.Name or "") .. ("|" .. normalizeMusicTrackId(q.Id)))
end

function markMusicTrackFailed(q, c)
    local r = getMusicTrackFailKey(q)
    if r == "" then return end
    gN.musicFailedMap = gN.musicFailedMap or {}
    if c then gN.musicFailedMap[r] = true else gN.musicFailedMap[r] = nil end
end

function formatMusicTime(q)
    local c = tonumber(q) or 0
    if c < 0 then c = 0 end
    local r = math.floor(c / 60)
    local j = math.floor(c % 60)
    return string.format("%02d:%02d", r, j)
end

function getWindThemeColor(q, r)
    local j = c and c[q]
    if typeof(j) == "Color3" then return j end
    local u = c and c.Scheme
    local M = u and u[q]
    if typeof(M) == "Color3" then return M end
    return r
end

function colorMix(q, c, r) return q:Lerp(c, math.clamp(tonumber(r) or 0, 0, 1)) end

function setMusicButtonIconColor(q, c)
    if not q then return end
    for q, r in ipairs(q:GetDescendants()) do if r:IsA("ImageLabel") then r.ImageColor3 = c elseif r:IsA("Frame") and (r.Name ~= "WourldIconRoot" and r.Name ~= "WourldIconCut") then r.BackgroundColor3 =
            c end end
end

function applyMusicWidgetTheme(q)
    if not q or not q.Root then return end
    local c = getWindThemeColor("MainColor", Color3.fromRGB(16, 16, 16))
    local r = getWindThemeColor("BackgroundColor", Color3.fromRGB(10, 10, 10))
    local j = getWindThemeColor("AccentColor", Color3.fromRGB(176, 176, 176))
    local u = getWindThemeColor("OutlineColor", Color3.fromRGB(48, 48, 48))
    local M = getWindThemeColor("FontColor", Color3.fromRGB(236, 236, 236))
    q.Root.BackgroundColor3 = colorMix(r, c, .46)
    if q.Stroke then
        q.Stroke.Color = colorMix(j, Color3.fromRGB(255, 255, 255), .1)
        q.Stroke.Transparency = .16
    end
    if q.Gradient then q.Gradient.Color = ColorSequence.new({ ColorSequenceKeypoint.new(0, colorMix(r, c, .35)),
            ColorSequenceKeypoint.new(1, colorMix(r, c, .7)) }) end
    if q.Disc then q.Disc.BackgroundColor3 = colorMix(c, j, .18) end
    if q.DiscGradient then q.DiscGradient.Color = ColorSequence.new({ ColorSequenceKeypoint.new(0,
            colorMix(j, Color3.fromRGB(255, 255, 255), .12)), ColorSequenceKeypoint.new(1, colorMix(j, r, .26)) }) end
    if q.TitleLabel then q.TitleLabel.TextColor3 = M end
    if q.SubLabel then q.SubLabel.TextColor3 = colorMix(M, j, .24) end
    if q.TimeLabel then q.TimeLabel.TextColor3 = colorMix(M, u, .38) end
    if q.ProgressTrack then q.ProgressTrack.BackgroundColor3 = colorMix(u, r, .35) end
    if q.ProgressFill then q.ProgressFill.BackgroundColor3 = colorMix(j, Color3.fromRGB(255, 255, 255), .1) end
    if q.ProgressFillGradient then q.ProgressFillGradient.Color = ColorSequence.new({ ColorSequenceKeypoint.new(0,
            colorMix(j, r, .15)), ColorSequenceKeypoint.new(1, colorMix(j, Color3.fromRGB(255, 255, 255), .18)) }) end
    if q.Bars then for q, c in ipairs(q.Bars) do if c and c.Parent then c.BackgroundColor3 = colorMix(j,
                    Color3.fromRGB(255, 255, 255), .08) end end end
    if q.Buttons then for q, j in pairs(q.Buttons) do if j and j.Parent then
                j.BackgroundColor3 = colorMix(c, r, .28)
                setMusicButtonIconColor(j, M)
                local q = j:FindFirstChild("WourldIconRoot") and j.WourldIconRoot:FindFirstChild("WourldIconCut")
                if q and q:IsA("Frame") then q.BackgroundColor3 = j.BackgroundColor3 end
            end end end
    if q.PowerButton then
        setMusicButtonIconColor(q.PowerButton, M)
        local c = q.PowerButton:FindFirstChild("WourldIconRoot") and
        q.PowerButton.WourldIconRoot:FindFirstChild("WourldIconCut")
        if c and c:IsA("Frame") then c.BackgroundColor3 = q.PowerButton.BackgroundColor3 end
    end
    if q.LoopButton then setMusicButtonIconColor(q.LoopButton, M) end
end

function updateMusicWidgetText()
    local q = gN.musicUi
    if not q then return end
    applyMusicWidgetTheme(q)
    local c = getCurrentMusicTrack()
    local r = "No Track"
    local j = "builtin"
    if c then
        r = tostring(c.Name or "No Track")
        j = tostring(c.Source or "builtin")
    end
    local u = gN.musicSound
    local M = u and u.Playing
    local G = "Off"
    if gN.musicEnabled then G = M and "Playing" or "Paused" end
    q.TitleLabel.Text = r
    q.SubLabel.Text = G .. ("  [" .. (j .. "]"))
    local d = u and formatMusicTime(u.TimePosition) or "00:00"
    local z = u and formatMusicTime(u.TimeLength) or "00:00"
    q.TimeLabel.Text = d .. (" / " .. z)
    local Y = getWindThemeColor("AccentColor", Color3.fromRGB(176, 176, 176))
    local P = getWindThemeColor("FontColor", Color3.fromRGB(236, 236, 236))
    local O = getWindThemeColor("MainColor", Color3.fromRGB(18, 18, 18))
    local a = getWindThemeColor("BackgroundColor", Color3.fromRGB(10, 10, 10))
    if q.PowerButton then
        q.PowerButton.BackgroundColor3 = gN.musicEnabled and colorMix(Y, Color3.fromRGB(88, 88, 88), .52) or
        colorMix(O, Color3.fromRGB(58, 58, 58), .36)
        local c = gN.musicEnabled and colorMix(Y, Color3.fromRGB(246, 246, 246), .3) or
        colorMix(P, Color3.fromRGB(204, 204, 204), .45)
        setMusicButtonIconColor(q.PowerButton, c)
        local r = q.PowerButton:FindFirstChild("WourldIconRoot") and
        q.PowerButton.WourldIconRoot:FindFirstChild("WourldIconCut")
        if r and r:IsA("Frame") then r.BackgroundColor3 = q.PowerButton.BackgroundColor3 end
    end
    if q.LoopButton then
        q.LoopButton.BackgroundColor3 = gN.musicAutoNext and colorMix(Y, Color3.fromRGB(72, 72, 72), .42) or
        colorMix(O, a, .28)
        local c = gN.musicAutoNext and colorMix(Y, Color3.fromRGB(235, 235, 235), .4) or colorMix(P, a, .24)
        setMusicButtonIconColor(q.LoopButton, c)
    end
    if q.Buttons and (q.Buttons.Play and q.Buttons.Pause) then
        local c = gN.musicEnabled and not M
        local r = gN.musicEnabled and M
        q.Buttons.Play.BackgroundColor3 = c and colorMix(Y, Color3.fromRGB(86, 86, 86), .46) or colorMix(O, a, .26)
        q.Buttons.Pause.BackgroundColor3 = r and colorMix(Y, Color3.fromRGB(98, 98, 98), .52) or colorMix(O, a, .26)
        setMusicButtonIconColor(q.Buttons.Play, c and Color3.fromRGB(246, 246, 246) or P)
        setMusicButtonIconColor(q.Buttons.Pause, r and Color3.fromRGB(246, 246, 246) or P)
    end
    if q.Buttons then
        if q.Buttons.Stop then
            q.Buttons.Stop.BackgroundColor3 = colorMix(O, a, .26)
            setMusicButtonIconColor(q.Buttons.Stop, P)
        end
        if q.Buttons.Next then
            q.Buttons.Next.BackgroundColor3 = colorMix(O, a, .26)
            setMusicButtonIconColor(q.Buttons.Next, P)
        end
        if q.Buttons.Prev then
            q.Buttons.Prev.BackgroundColor3 = colorMix(O, a, .26)
            setMusicButtonIconColor(q.Buttons.Prev, P)
        end
    end
end

function setMusicWidgetLoopState(q)
    if gN.musicUiConnection then
        pcall(function() gN.musicUiConnection:Disconnect() end)
        gN.musicUiConnection = nil
    end
    if not q then return end
    local c = 0
    gN.musicUiConnection = z.RenderStepped:Connect(function(q)
        local r = gN.musicUi
        if not r or not r.Root or not r.Root.Parent then return end
        local j = gN.musicSound
        local u = j and j.Playing
        if u then
            gN.musicUiAngle = ((tonumber(gN.musicUiAngle) or 0)) + ((tonumber(q) or .016)) * 86
            gN.musicUiBeat = math.min(1, ((tonumber(gN.musicUiBeat) or 0)) + ((tonumber(q) or .016)) * 4)
        else
            gN.musicUiAngle = ((tonumber(gN.musicUiAngle) or 0)) + ((tonumber(q) or .016)) * 12
            gN.musicUiBeat = math.max(0, ((tonumber(gN.musicUiBeat) or 0)) - ((tonumber(q) or .016)) * 2.2)
        end
        r.Disc.Rotation = gN.musicUiAngle % 360
        local M = 0
        if j and (j.TimeLength and j.TimeLength > 0) then M = math.clamp(j.TimePosition / j.TimeLength, 0, 1) end
        r.ProgressFill.Size = UDim2.new(M, 0, 1, 0)
        local G = 0
        if j then G = math.clamp(((tonumber(j.PlaybackLoudness) or 0)) / 220, 0, 1) end
        for q, c in ipairs(r.Bars) do
            local r = ((math.sin((tick() * 8) + (q * .6)) + 1)) * .5
            local j = .16 + r * ((u and (.52 + G * .28) or .08))
            j = j + ((gN.musicUiBeat or 0)) * .1
            local M = math.floor(6 + (j * 22))
            c.Size = UDim2.new(0, 5, 0, M)
            c.Position = UDim2.new(0, 4 + (((q - 1)) * 6), 1, -M - 2)
            local d = getWindThemeColor("AccentColor", Color3.fromRGB(120, 180, 255))
            c.BackgroundColor3 = colorMix(d, Color3.fromRGB(255, 255, 255), .12 + r * .2)
        end
        if r.Stroke then
            local q = getWindThemeColor("AccentColor", Color3.fromRGB(120, 180, 255))
            r.Stroke.Color = colorMix(q, Color3.fromRGB(255, 255, 255), .08)
        end
        c = c - ((tonumber(q) or .016))
        if c <= 0 then
            c = .08
            updateMusicWidgetText()
        end
    end)
end

function ensureMusicWidget()
    local q = gN.musicUi
    if q and (q.Root and q.Root.Parent) then return q end
    local c = nil
    local r, j = pcall(function() return gethui and gethui() or nil end)
    if r and typeof(j) == "Instance" then c = j end
    if not c then
        local q, r = pcall(function() return game:GetService("CoreGui") end)
        if q and typeof(r) == "Instance" then c = r end
    end
    if not c then return nil end
    if c:IsA("ScreenGui") and c.Parent then c = c.Parent end
    local u = c:FindFirstChild("WourldMusicWidgetGui")
    if u and not u:IsA("ScreenGui") then
        pcall(function() u:Destroy() end)
        u = nil
    end
    if not u then
        u = Instance.new("ScreenGui")
        u.Name = "WourldMusicWidgetGui"
        u.ResetOnSpawn = false
        u.IgnoreGuiInset = true
        u.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
        u.DisplayOrder = 998
        u.Parent = c
    end
    local G = Instance.new("Frame")
    G.Name = "WourldMusicWidget"
    G.AnchorPoint = Vector2.new(1, 0)
    G.Size = UDim2.fromOffset(368, 168)
    G.Position = UDim2.new(1, -28, 0, 92)
    G.BackgroundColor3 = Color3.fromRGB(11, 15, 24)
    G.BackgroundTransparency = .05
    G.Active = true
    G.Draggable = true
    G.Visible = gN.musicUiVisible and true or false
    G.ZIndex = 10
    G.Parent = u
    local d = Instance.new("UICorner")
    d.CornerRadius = UDim.new(0, 16)
    d.Parent = G
    local z = Instance.new("UIStroke")
    z.Thickness = 1.3
    z.Transparency = .08
    z.Color = Color3.fromRGB(62, 142, 255)
    z.Parent = G
    local Y = Instance.new("UIGradient")
    Y.Color = ColorSequence.new({ ColorSequenceKeypoint.new(0, Color3.fromRGB(10, 16, 27)), ColorSequenceKeypoint.new(1,
        Color3.fromRGB(8, 10, 17)) })
    Y.Rotation = -18
    Y.Parent = G
    local P = Instance.new("Frame")
    P.BackgroundTransparency = 1
    P.Position = UDim2.fromOffset(292, 10)
    P.Size = UDim2.fromOffset(66, 24)
    P.Parent = G
    local function O(q, c, r, j)
        local u = Instance.new("TextButton")
        u.Name = q
        u.AutoButtonColor = false
        u.Text = ""
        u.Font = Enum.Font.GothamBold
        u.TextSize = 15
        u.TextColor3 = Color3.fromRGB(211, 228, 255)
        u.BackgroundColor3 = Color3.fromRGB(22, 34, 57)
        u.Size = UDim2.fromOffset(30, 24)
        u.Position = UDim2.fromOffset(r, 0)
        u.Parent = P
        local M = Instance.new("UICorner")
        M.CornerRadius = UDim.new(0, 8)
        M.Parent = u
        local G = Instance.new("UIStroke")
        G.Thickness = 1
        G.Transparency = .24
        G.Color = Color3.fromRGB(99, 150, 233)
        G.Parent = u
        u.MouseEnter:Connect(function() ((game:GetService("TweenService")):Create(u, TweenInfo.new(.12), { BackgroundColor3 = Color3.fromRGB(34, 51, 82) }))
                :Play() end)
        u.MouseLeave:Connect(function() updateMusicWidgetText() end)
        u.MouseButton1Click:Connect(function()
            pcall(j)
            updateMusicWidgetText()
        end)
        return u
    end
    local function a(q)
        if not q then return end
        for q, c in ipairs(q:GetChildren()) do if c.Name == "WourldIconRoot" then c:Destroy() end end
    end
    local function o(q, c)
        a(q)
        local r = Instance.new("Frame")
        r.Name = "WourldIconRoot"
        r.BackgroundTransparency = 1
        r.Size = UDim2.fromOffset(18, 18)
        r.AnchorPoint = Vector2.new(.5, .5)
        r.Position = UDim2.fromScale(.5, .5)
        r.Parent = q
        local j = Color3.fromRGB(226, 236, 255)
        local function u(q, c, u, M)
            local G = Instance.new("Frame")
            G.BorderSizePixel = 0
            G.BackgroundColor3 = j
            G.Position = UDim2.fromOffset(q, c)
            G.Size = UDim2.fromOffset(u, M)
            G.Parent = r
            local d = Instance.new("UICorner")
            d.CornerRadius = UDim.new(0, 2)
            d.Parent = G
            return G
        end
        local function M(q, c)
            local r = c and { 14, 10, 6 } or { 6, 10, 14 }
            for c = 1, 3, 1 do u(q + (((c - 1)) * 4), math.floor(((18 - r[c])) / 2), 3, r[c]) end
        end
        if c == "Play" then M(4, false) elseif c == "Pause" then
            u(4, 3, 4, 12)
            u(10, 3, 4, 12)
        elseif c == "Stop" then u(4, 4, 10, 10) elseif c == "Prev" then
            u(2, 3, 2, 12)
            M(5, true)
        elseif c == "Next" then
            M(2, false)
            u(14, 3, 2, 12)
        elseif c == "Power" then
            u(8, 2, 2, 6)
            local c = Instance.new("Frame")
            c.BackgroundColor3 = j
            c.BorderSizePixel = 0
            c.Size = UDim2.fromOffset(10, 10)
            c.Position = UDim2.fromOffset(4, 6)
            c.Parent = r
            local M = Instance.new("UICorner")
            M.CornerRadius = UDim.new(1, 0)
            M.Parent = c
            local G = Instance.new("Frame")
            G.Name = "WourldIconCut"
            G.BackgroundColor3 = q.BackgroundColor3
            G.BorderSizePixel = 0
            G.Size = UDim2.fromOffset(4, 4)
            G.Position = UDim2.fromOffset(7, 5)
            G.Parent = r
        elseif c == "Loop" then
            u(3, 4, 10, 2)
            u(9, 2, 4, 2)
            u(11, 4, 2, 2)
            u(5, 12, 10, 2)
            u(5, 10, 4, 2)
            u(5, 12, 2, 2)
        end
    end
    local v = O("Power", "", 0,
        function()
            setMusicEnabled(not gN.musicEnabled)
            if M and (M.MusicPlayerEnableToggle and (M.MusicPlayerEnableToggle.SetValue and M.MusicPlayerEnableToggle.Value ~= gN.musicEnabled)) then
                M.MusicPlayerEnableToggle:SetValue(gN.musicEnabled) end
        end)
    o(v, "Power")
    local b = O("Loop", "", 34,
        function()
            gN.musicAutoNext = not gN.musicAutoNext
            if M and (M.MusicAutoNextToggle and (M.MusicAutoNextToggle.SetValue and M.MusicAutoNextToggle.Value ~= gN.musicAutoNext)) then
                M.MusicAutoNextToggle:SetValue(gN.musicAutoNext) end
        end)
    o(b, "Loop")
    local X = Instance.new("Frame")
    X.Name = "Disc"
    X.Size = UDim2.fromOffset(78, 78)
    X.Position = UDim2.fromOffset(14, 12)
    X.BackgroundColor3 = Color3.fromRGB(28, 40, 66)
    X.Parent = G
    local x = Instance.new("UICorner")
    x.CornerRadius = UDim.new(1, 0)
    x.Parent = X
    local U = Instance.new("UIGradient")
    U.Color = ColorSequence.new({ ColorSequenceKeypoint.new(0, Color3.fromRGB(58, 112, 255)), ColorSequenceKeypoint.new(
    1, Color3.fromRGB(119, 220, 255)) })
    U.Rotation = 35
    U.Parent = X
    local T = Instance.new("Frame")
    T.Size = UDim2.fromOffset(20, 20)
    T.AnchorPoint = Vector2.new(.5, .5)
    T.Position = UDim2.fromScale(.5, .5)
    T.BackgroundColor3 = Color3.fromRGB(18, 20, 30)
    T.Parent = X
    local e = Instance.new("UICorner")
    e.CornerRadius = UDim.new(1, 0)
    e.Parent = T
    local h = Instance.new("TextLabel")
    h.BackgroundTransparency = 1
    h.Position = UDim2.fromOffset(104, 14)
    h.Size = UDim2.fromOffset(182, 24)
    h.Font = Enum.Font.GothamBold
    h.TextXAlignment = Enum.TextXAlignment.Left
    h.TextColor3 = Color3.fromRGB(241, 248, 255)
    h.TextSize = 18
    h.TextTruncate = Enum.TextTruncate.AtEnd
    h.TextWrapped = false
    h.ClipsDescendants = true
    h.Text = "No Track"
    h.Parent = G
    local V = Instance.new("TextLabel")
    V.BackgroundTransparency = 1
    V.Position = UDim2.fromOffset(104, 38)
    V.Size = UDim2.fromOffset(250, 18)
    V.Font = Enum.Font.GothamSemibold
    V.TextXAlignment = Enum.TextXAlignment.Left
    V.TextColor3 = Color3.fromRGB(156, 192, 255)
    V.TextSize = 13
    V.TextTruncate = Enum.TextTruncate.AtEnd
    V.TextWrapped = false
    V.ClipsDescendants = true
    V.Text = "Paused  [builtin]"
    V.Parent = G
    local t = Instance.new("TextLabel")
    t.BackgroundTransparency = 1
    t.Position = UDim2.fromOffset(104, 57)
    t.Size = UDim2.fromOffset(250, 16)
    t.Font = Enum.Font.Code
    t.TextXAlignment = Enum.TextXAlignment.Left
    t.TextColor3 = Color3.fromRGB(185, 205, 247)
    t.TextSize = 12
    t.TextTruncate = Enum.TextTruncate.AtEnd
    t.TextWrapped = false
    t.ClipsDescendants = true
    t.Text = "00:00 / 00:00"
    t.Parent = G
    local B = Instance.new("Frame")
    B.BackgroundColor3 = Color3.fromRGB(29, 43, 72)
    B.Position = UDim2.fromOffset(104, 78)
    B.Size = UDim2.fromOffset(244, 8)
    B.Parent = G
    local F = Instance.new("UICorner")
    F.CornerRadius = UDim.new(1, 0)
    F.Parent = B
    local l = Instance.new("Frame")
    l.BackgroundColor3 = Color3.fromRGB(88, 180, 255)
    l.Size = UDim2.fromScale(0, 1)
    l.Parent = B
    local D = Instance.new("UICorner")
    D.CornerRadius = UDim.new(1, 0)
    D.Parent = l
    local J = Instance.new("UIGradient")
    J.Color = ColorSequence.new({ ColorSequenceKeypoint.new(0, Color3.fromRGB(73, 128, 255)), ColorSequenceKeypoint.new(
    1, Color3.fromRGB(114, 241, 255)) })
    J.Parent = l
    local w = Instance.new("Frame")
    w.BackgroundTransparency = 1
    w.Position = UDim2.fromOffset(14, 102)
    w.Size = UDim2.fromOffset(130, 50)
    w.Parent = G
    local g = {}
    for q = 1, 20, 1 do
        local c = Instance.new("Frame")
        c.Name = "Bar" .. tostring(q)
        c.Size = UDim2.fromOffset(5, 8)
        c.Position = UDim2.new(0, 4 + (((q - 1)) * 6), 1, -10)
        c.BorderSizePixel = 0
        c.BackgroundColor3 = Color3.fromRGB(72, 170, 255)
        c.Parent = w
        local r = Instance.new("UICorner")
        r.CornerRadius = UDim.new(1, 0)
        r.Parent = c
        g[q] = c
    end
    local A = {}
    local function f(q, c, r, j, u)
        local M = Instance.new("TextButton")
        M.Name = q
        M.AutoButtonColor = false
        M.Text = ""
        M.Font = Enum.Font.GothamBold
        M.TextSize = 14
        M.TextColor3 = Color3.fromRGB(230, 241, 255)
        M.BackgroundColor3 = Color3.fromRGB(22, 34, 57)
        M.Size = UDim2.fromOffset(j, 28)
        M.Position = UDim2.fromOffset(r, 122)
        M.Parent = G
        local d = Instance.new("UICorner")
        d.CornerRadius = UDim.new(0, 8)
        d.Parent = M
        local z = Instance.new("UIStroke")
        z.Thickness = 1
        z.Transparency = .2
        z.Color = Color3.fromRGB(88, 131, 206)
        z.Parent = M
        M.MouseEnter:Connect(function() ((game:GetService("TweenService")):Create(M, TweenInfo.new(.15), { BackgroundColor3 = Color3.fromRGB(34, 51, 82) }))
                :Play() end)
        M.MouseLeave:Connect(function() ((game:GetService("TweenService")):Create(M, TweenInfo.new(.18), { BackgroundColor3 = Color3.fromRGB(22, 34, 57) }))
                :Play() end)
        M.MouseButton1Click:Connect(function() pcall(u) end)
        o(M, c)
        A[q] = M
    end
    f("Prev", "Prev", 146, 40, function() playPreviousMusicTrack() end)
    f("Play", "Play", 190, 40, function() playSelectedMusicTrack() end)
    f("Pause", "Pause", 234, 40, function() togglePauseMusicTrack() end)
    f("Stop", "Stop", 278, 40, function() stopMusicTrack() end)
    f("Next", "Next", 322, 40, function() playNextMusicTrack() end)
    gN.musicUi = { Root = G, Stroke = z, Gradient = Y, Disc = X, DiscGradient = U, TitleLabel = h, SubLabel = V, TimeLabel =
    t, ProgressTrack = B, ProgressFill = l, ProgressFillGradient = J, Bars = g, Buttons = A, PowerButton = v, LoopButton =
    b }
    setMusicWidgetLoopState(true)
    updateMusicWidgetText()
    return gN.musicUi
end

function setMusicWidgetVisible(q)
    gN.musicUiVisible = q and true or false
    local c = ensureMusicWidget()
    if c and c.Root then c.Root.Visible = gN.musicUiVisible end
end

function setMusicSelectedTrack(q)
    local c, r = getMusicTrackByName(q)
    if c and r then
        gN.musicIndex = r
        gN.musicSelectedName = c.Name
    end
    updateMusicWidgetText()
end

function playMusicTrackAt(q)
    if #gN.musicPlaylist == 0 then rebuildMusicPlaylist() end
    local r = #gN.musicPlaylist
    if r == 0 then
        c:Notify({ Title = "Wourld Hub", Description = "net rabochiy muzyki", Duration = 3 })
        return false
    end
    local j = math.clamp(tonumber(q) or 1, 1, r)
    local M = ensureMusicSound()
    M.Volume = math.clamp(tonumber(gN.musicVolume) or .55, 0, 1)
    M.PlaybackSpeed = math.clamp(tonumber(gN.musicPitch) or 1, .5, 2)
    local function G(q, c)
        if tostring(q or "") == "" then return false end
        M.SoundId = q
        pcall(function()
            M.TimePosition = 0
            M:Stop()
        end)
        local r = false
        pcall(function()
            M:Play()
            r = true
        end)
        if not r then pcall(function()
                a:PlayLocalSound(M)
                r = true
            end) end
        local j = tick() + math.clamp(tonumber(c) or .8, .2, 2)
        while tick() <= j do
            if M.Playing or M.IsLoaded or ((tonumber(M.TimeLength) or 0)) > 0 then return true end
            task.wait(.05)
        end
        return M.Playing
    end
    local d = {}
    for q = 0, r - 1, 1 do d[#d + 1] = ((((j - 1) + q)) % r) + 1 end
    local z = false
    local Y = nil
    local P = nil
    for q, c in ipairs(d) do
        local r = gN.musicPlaylist[c]
        if r then
            local q = normalizeMusicTrackId(r.Id)
            local j = q ~= "" and G(q, 1.15)
            if j then
                z = true
                Y = r
                P = c
                markMusicTrackFailed(r, false)
                break
            end
            markMusicTrackFailed(r, true)
        end
    end
    if z and (Y and P) then
        gN.musicIndex = P
        gN.musicSelectedName = tostring(Y.Name)
        refreshMusicDropdownValues()
        if u and (u.MusicTrackDropdown and u.MusicTrackDropdown.SetValue) then pcall(function() u.MusicTrackDropdown
                    :SetValue(gN.musicSelectedName) end) end
        updateMusicWidgetText()
        return true
    end
    c:Notify({ Title = "Wourld Hub", Description = "net rabochiy muzyki", Duration = 2.4 })
    refreshMusicDropdownValues()
    updateMusicWidgetText()
    return false
end

function playSelectedMusicTrack()
    local q, c = getMusicTrackByName(gN.musicSelectedName)
    if q and c then return playMusicTrackAt(c) end
    return playMusicTrackAt(gN.musicIndex or 1)
end

function togglePauseMusicTrack()
    local q = ensureMusicSound()
    if q.Playing then pcall(function() q:Pause() end) else if q.SoundId and (q.SoundId ~= "" and q.TimePosition > 0) then
            local c = false
            pcall(function()
                q:Resume()
                c = true
            end)
            if not c then pcall(function() q:Play() end) end
        else playSelectedMusicTrack() end end
    updateMusicWidgetText()
end

function stopMusicTrack()
    local q = gN.musicSound
    if q then pcall(function() q:Stop() end) end
    updateMusicWidgetText()
end

function playNextMusicTrack()
    if #gN.musicPlaylist == 0 then rebuildMusicPlaylist() end
    local q = #gN.musicPlaylist
    if q == 0 then return false end
    local c = (math.clamp(tonumber(gN.musicIndex) or 1, 1, q) % q) + 1
    return playMusicTrackAt(c)
end

function playPreviousMusicTrack()
    if #gN.musicPlaylist == 0 then rebuildMusicPlaylist() end
    local q = #gN.musicPlaylist
    if q == 0 then return false end
    local c = math.clamp(tonumber(gN.musicIndex) or 1, 1, q) - 1
    if c < 1 then c = q end
    return playMusicTrackAt(c)
end

function setMusicEnabled(q)
    gN.musicEnabled = q and true or false
    if gN.musicEnabled then
        ensureMusicSound()
        ensureMusicWidget()
        setMusicWidgetVisible(gN.musicUiVisible)
    else stopMusicTrack() end
    updateMusicWidgetText()
end

function shutdownMusicPlayer()
    if gN.musicUiConnection then
        pcall(function() gN.musicUiConnection:Disconnect() end)
        gN.musicUiConnection = nil
    end
    if gN.musicSoundConn then
        pcall(function() gN.musicSoundConn:Disconnect() end)
        gN.musicSoundConn = nil
    end
    if gN.musicSound then
        pcall(function() gN.musicSound:Stop() end)
        if gN.musicSound.Parent then pcall(function() gN.musicSound:Destroy() end) end
        gN.musicSound = nil
    end
    local q = nil
    if gN.musicUi and (gN.musicUi.Root and gN.musicUi.Root.Parent) then
        q = gN.musicUi.Root.Parent
        pcall(function() gN.musicUi.Root:Destroy() end)
    end
    if q and (q:IsA("ScreenGui") and q.Name == "WourldMusicWidgetGui") then pcall(function() q:Destroy() end) end
    local c, r = pcall(function() return game:GetService("CoreGui") end)
    if c and r then
        local q = r:FindFirstChild("WourldMusicWidgetGui")
        if q and q:IsA("ScreenGui") then pcall(function() q:Destroy() end) end
    end
    gN.musicUi = nil
end

function isLocalOwnedInstance(q)
    if not q or not q.Parent then return false end
    local c = b.Character
    if c and q:IsDescendantOf(c) then return true end
    local r = b:FindFirstChildOfClass("Backpack")
    if r and q:IsDescendantOf(r) then return true end
    local j = Y:FindFirstChild(b.Name .. "SpawnedInToys")
    if j and q:IsDescendantOf(j) then return true end
    return false
end

function isKickObjectName(q)
    local c = (tostring(q or "")):lower()
    if c == "" then return false end
    local r = c:gsub("[%s_%-%(%)%[%]%.]+", "")
    local j = { blackholekick = true, blackholekicktweensold = true, blackholekicktweens = true, jhole = true, blackhole = true, voidhole = true, singularity = true }
    if j[r] then return true end
    local u = { "blackhole", "voidhole", "singularity", "jhole", "kick", "antikick" }
    for q, c in ipairs(u) do if r:find(c, 1, true) then return true end end
    return false
end

function getInstanceWorldPosition(q)
    if not q then return nil end
    if q:IsA("BasePart") then return q.Position end
    if q:IsA("Model") then
        local c = q.PrimaryPart or q:FindFirstChild("HumanoidRootPart") or q:FindFirstChildWhichIsA("BasePart", true)
        return c and c.Position or nil
    end
    local c = q:FindFirstChildWhichIsA("BasePart", true)
    return c and c.Position or nil
end

function sendFreeChatAnnouncement()
    if not chatAnnouncementEnabled then return end
    local q = getgenv and getgenv() or _G
    if q.WourldFreeChatSent then return end
    q.WourldFreeChatSent = true
    task.spawn(function() pcall(function()
            local q = o and o:FindFirstChild("TextChannels")
            local c = q and q:FindFirstChild("RBXGeneral")
            if c and c.SendAsync then
                c:SendAsync("Wourld script [FREE]")
                return
            end
            local r = d:FindFirstChild("DefaultChatSystemChatEvents")
            local j = r and r:FindFirstChild("SayMessageRequest")
            if j then j:FireServer("Wourld script [FREE]", "All") end
        end) end)
end

function autoloadInfiniteYield()
    local q = getgenv and getgenv() or _G
    if q.WourldIYLoaded then return end
    q.WourldIYLoaded = true
    task.spawn(function() pcall(function() (loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source")))() end) end)
end

function getLocalToyFolder() return Y:FindFirstChild(b.Name .. "SpawnedInToys") end

function getLocalOwnedPlotFolder()
    local q = Y:FindFirstChild("Plots")
    local c = Y:FindFirstChild("PlotItems")
    if not q or not c then return nil end
    for q, r in ipairs(q:GetChildren()) do
        local j = r:FindFirstChild("PlotSign")
        local u = j and j:FindFirstChild("ThisPlotsOwners")
        if u then for q, j in ipairs(u:GetChildren()) do if tostring(j.Value or "") == b.Name then
                    local q = c:FindFirstChild(r.Name)
                    if q then return q end
                end end end
    end
    return nil
end

function getLocalToyContainers()
    local q = {}
    local c = {}
    local function r(r) if r and (r.Parent and not c[r]) then
            c[r] = true
            q[#q + 1] = r
        end end
    r(getLocalToyFolder())
    r(getLocalOwnedPlotFolder())
    return q
end

function findLocalToyByName(q)
    local c = tostring(q or "")
    if c == "" then return nil, nil end
    for q, r in ipairs(getLocalToyContainers()) do
        local j = r:FindFirstChild(c)
        if j then return j, r end
    end
    return nil, nil
end

function getAntiInputLagItemGroups() return { ["Food (All)"] = { "FoodMayonnaise", "FoodHamburger", "FoodBread", "FoodPizzaCheese", "FoodPizzaPepperoni", "FoodDonut", "FoodFrenchFries", "FoodHotdog", "FoodCheeseburger", "FoodCakePink", "FoodCoconut", "FoodDippyEgg", "FoodMayonnaise", "FoodMeatStick", "FoodMushroomPoison" },
        ["Instruments (All)"] = { "InstrumentBrassTrumpet", "InstrumentBrassVuvuzelaQwizik", "InstrumentDrumBongos", "InstrumentDrumSnare", "InstrumentGuitarBanjo", "InstrumentGuitarUkulele", "InstrumentGuitarViolin", "InstrumentPianoMelodica", "InstrumentVoiceMicrophone", "InstrumentWoodwindOcarina", "InstrumentWoodwindSaxophone" },
        ["Cups (All)"] = { "CupMugBrown", "CupMugWhite" }, ["Poop (All)"] = { "PoopPile", "PoopPileSparkle" } } end

antiInputLagRuntimeNames = antiInputLagRuntimeNames or {}
antiInputLagRuntimeRefreshAt = antiInputLagRuntimeRefreshAt or 0
function getRuntimeAntiInputLagToyNames(q)
    local c = tick()
    if (not q) and (c < ((tonumber(antiInputLagRuntimeRefreshAt) or 0)) and #antiInputLagRuntimeNames > 0) then return
        antiInputLagRuntimeNames end
    local r = {}
    local j = {}
    local function u(q)
        local c = tostring(q or "")
        local u = string.lower(c)
        if c ~= "" and not j[u] then
            j[u] = true
            r[#r + 1] = c
        end
    end
    local M = 0
    local G = 1400
    local function z(q)
        if M >= G or not q or not q:IsA("Model") then return end
        M = M + 1
        local c = q:FindFirstChild("HoldPart")
        if not c then return end
        local r = c:FindFirstChild("HoldItemRemoteFunction")
        local j = c:FindFirstChild("DropItemRemoteFunction")
        if r and j then u(q.Name) end
    end
    for q, c in ipairs(getLocalToyContainers()) do for q, c in ipairs(c:GetChildren()) do z(c) end end
    local P = Y:FindFirstChild("PlotItems")
    if P then for q, c in ipairs(P:GetChildren()) do
            for q, c in ipairs(c:GetChildren()) do
                z(c)
                if M >= G then break end
            end
            if M >= G then break end
        end end
    local O = d:FindFirstChild("MenuToys")
    if O then for q, c in ipairs(O:GetDescendants()) do
            if M >= G then break end
            if c:IsA("StringValue") and c.Value ~= "" then u(c.Value) elseif c:IsA("Folder") and c.Name:find("Toy", 1, true) then
                u(c.Name) end
        end end
    table.sort(r, function(q, c) return string.lower(q) < string.lower(c) end)
    antiInputLagRuntimeNames = r
    antiInputLagRuntimeRefreshAt = c + 3.5
    return r
end

function getAntiInputLagFilterOptions(q)
    local c = {}
    local r = {}
    local function j(q)
        local j = tostring(q or "")
        local u = string.lower(j)
        if j ~= "" and not r[u] then
            r[u] = true
            c[#c + 1] = j
        end
    end
    j("All")
    j("Food (All)")
    j("Instruments (All)")
    j("Cups (All)")
    j("Poop (All)")
    for q, c in ipairs(getRuntimeAntiInputLagToyNames(q)) do j(c) end
    j("FoodHamburger")
    return c
end

function getAntiInputLagItemCandidatesByFilter(q)
    local c = tostring(q or "All")
    local r = {}
    local j = {}
    local function u(q)
        local c = tostring(q or "")
        local u = string.lower(c)
        if c ~= "" and not j[u] then
            j[u] = true
            r[#r + 1] = c
        end
    end
    local M = getAntiInputLagItemGroups()
    if c == "" or c == "All" then
        u("FoodMayonnaise")
        u("FoodHamburger")
        u("FoodBread")
        for q, c in pairs(M) do for q, c in ipairs(c) do u(c) end end
        for q, c in ipairs(getRuntimeAntiInputLagToyNames(false)) do u(c) end
    elseif M[c] then for q, c in ipairs(M[c]) do u(c) end else u(c) end
    u("FoodMayonnaise")
    u("FoodHamburger")
    u("FoodBread")
    return r
end

function getRemoveAntiKickAuraToyNames()
    local q = tostring(_G.WourldRemoveAntiKickAuraItem or "All")
    local c = { All = { "NinjaKunai", "NinjaShuriken", "AntiKick" }, NinjaShuriken = { "NinjaShuriken" }, NinjaKunai = { "NinjaKunai" }, AntiKick = { "AntiKick" },
        ["Kunai+Shuriken"] = { "NinjaKunai", "NinjaShuriken" }, ["Shuriken+AntiKick"] = { "NinjaShuriken", "AntiKick" } }
    return c[q] or c.All
end

function getAntiKickAnyItemOptions() return { "AntiKick", "NinjaShuriken", "NinjaKunai", "FoodBread", "FoodHamburger",
        "SwordKatana", "NinjaKatana", "PencilClassic" } end

function resolveStickyWeld(q)
    if not q then return nil end
    return q:FindFirstChild("StickyWeld") or q:FindFirstChildWhichIsA("WeldConstraint") or
    q:FindFirstChildWhichIsA("Weld")
end

function hasLocalPartOwnership(q)
    if not q or not q.Parent then return false end
    local c = q:FindFirstChild("PartOwner") or q:FindFirstChild("PartOwner", true)
    if c and c:IsA("StringValue") then return c.Value == b.Name end
    local r, j = pcall(function() return q:GetNetworkOwner() == b end)
    return r and j or false
end

function claimPartOwnershipFast(q, c, r)
    if not q or not q:IsA("BasePart") or not c then return false end
    local j = tick() + math.clamp(tonumber(r) or .24, .05, 1.2)
    repeat
        pcall(function() c:FireServer(q, q.CFrame) end)
        if hasLocalPartOwnership(q) then return true end
        task.wait(.03)
    until tick() >= j or not q.Parent
    return hasLocalPartOwnership(q)
end

function isStickyAttachedToFirePart(q, c, r)
    if not q or not q.Parent then return false end
    local j = resolveStickyWeld(q)
    if not j then return false end
    local u = j.Part1
    if not u then return false end
    local M = b and b.Character
    if M and u:IsDescendantOf(M) then return true end
    if c and u == c then return true end
    if c then return ((u.Position - c.Position)).Magnitude <= math.max(3, tonumber(r) or 9) end
    return true
end

function getKunaiUsageState(q)
    if not ((q and (q:IsA("Model") and q.Parent))) then return "Useless", nil, nil end
    local c = q:FindFirstChild("StickyPart", true)
    if not c then return "Useless", nil, nil end
    local r = resolveStickyWeld(c)
    if not r then return "Useless", c, nil end
    local j = true
    pcall(function() if r.Enabled == false then j = false end end)
    if not j then return "Useless", c, r end
    local u = r.Part1
    if not u then return "No use!", c, r end
    local M = b and b.Character
    if M and u:IsDescendantOf(M) then return "Using", c, r end
    return "Used", c, r
end

function getLocalCharacterAntiKickAttachTarget(q, c)
    if not q then return nil, nil, nil end
    local r = q:FindFirstChild("HumanoidRootPart")
    local j = r and ((r:FindFirstChild("FirePlayerPart") or r)) or nil
    if c and (j and j:IsA("BasePart")) then return j, CFrame.new(0, 0, 0) * CFrame.Angles(0, math.rad(90), math.rad(90)),
            j end
    local u = q:FindFirstChild("Left Leg") or q:FindFirstChild("LeftLowerLeg") or q:FindFirstChild("LeftFoot")
    if u and u:IsA("BasePart") then return u, CFrame.new(0, -0.5, 0) * CFrame.Angles(0, 0, math.rad(90)), j end
    if j and j:IsA("BasePart") then return j, CFrame.new(0, 0, 0) * CFrame.Angles(0, math.rad(90), math.rad(90)), j end
    return nil, nil, j
end

function clearAntiKickAnyToys()
    local q, c = getMenuToyRemotes()
    u6 = {}
    for q, r in ipairs(getLocalToyContainers()) do for q, r in ipairs(r:GetChildren()) do if string.find(r.Name, "AntiKickAny_", 1, true) == 1 then
                if c then pcall(function() c:FireServer(r) end) end
                if r.Parent then pcall(function() r:Destroy() end) end
            end end end
end

function spawnAntiKickAnyToy(q, c)
    local r = select(1, getMenuToyRemotes())
    if not r then return nil end
    local j = b.Character
    local u = j and j:FindFirstChild("HumanoidRootPart")
    local M = {}
    for q, c in ipairs(getLocalToyContainers()) do for q, c in ipairs(c:GetChildren()) do M[c] = true end end
    local G = u and (u.CFrame * CFrame.new(0, 12, 18)) or CFrame.new(0, 40, 0)
    pcall(function() r:InvokeServer(q, G, Vector3.zero) end)
    local d = tick() + 2.6
    repeat
        for r, j in ipairs(getLocalToyContainers()) do
            local u = j:FindFirstChild(c)
            if u then return u end
            for r, j in ipairs(j:GetChildren()) do if not M[j] and j.Name == q then
                    pcall(function() j.Name = c end)
                    return j
                end end
        end
        task.wait(.05)
    until tick() >= d
    return nil
end

function hardenAntiKickAnyToy(q)
    if not q then return end
    for q, c in ipairs(q:GetDescendants()) do if c:IsA("BasePart") then
            c.CanCollide = false
            c.CanTouch = false
            c.CanQuery = false
            if c.Name == "Main" or c.Name == "Pyramid" then c.Transparency = 0 else c.Transparency = 1 end
        end end
end

function maintainAntiKickAnySlot(q, c, r, j)
    local u = "AntiKickAny_" .. tostring(q)
    local M = findLocalToyByName(u)
    if not M then M = spawnAntiKickAnyToy(c, u) end
    if not M then return end
    local G = b.Character
    local d = G and G:FindFirstChild("HumanoidRootPart")
    local z = d and ((d:FindFirstChild("FirePlayerPart") or d))
    local Y = M:FindFirstChild("SoundPart") or M:FindFirstChildWhichIsA("BasePart", true)
    if Y then claimPartOwnershipFast(Y, r, .25) end
    local P = M:FindFirstChild("StickyPart", true)
    local O = isStickyAttachedToFirePart(P, z, 11)
    local a = P and (z and (((P.Position - z.Position)).Magnitude > 26))
    if P and (z and (((not O) or a))) then
        pcall(function() j:FireServer(P, z, CFrame.new(0, 0, 0) * CFrame.Angles(0, math.rad(90), math.rad(90))) end)
        task.wait(.03)
        if Y then claimPartOwnershipFast(Y, r, .15) end
    elseif Y and (z and (not P)) then pcall(function()
            Y.CFrame = z.CFrame
            Y.AssemblyLinearVelocity = Vector3.zero
            Y.AssemblyAngularVelocity = Vector3.zero
        end) end
    hardenAntiKickAnyToy(M)
    u6[q] = M
end

function StartAnyItemAntiKickLoop()
    local q = (d:WaitForChild("GrabEvents")):WaitForChild("SetNetworkOwner")
    local c = (d:WaitForChild("PlayerEvents")):WaitForChild("StickyPartEvent")
    local r = false
    while q6 do
        if shouldPauseLocalAntiKickInHouse and shouldPauseLocalAntiKickInHouse(b) then
            if not r then
                clearAntiKickAnyToys()
                r = true
            end
            task.wait(.2)
            continue
        end
        r = false
        local j = b.Character
        local u = j and j:FindFirstChildOfClass("Humanoid")
        local M = j and j:FindFirstChild("HumanoidRootPart")
        if j and (u and (M and u.Health > 0)) then
            local r = math.clamp(tonumber(j6) or 1, 1, 3)
            local j = tostring(r6 or "AntiKick")
            for r = 1, r, 1 do maintainAntiKickAnySlot(r, j, q, c) end
            for q = r + 1, 3, 1 do
                local c = findLocalToyByName("AntiKickAny_" .. tostring(q))
                if c then
                    local q, r = getMenuToyRemotes()
                    if r then pcall(function() r:FireServer(c) end) end
                    pcall(function() c:Destroy() end)
                end
            end
        end
        task.wait(.12)
    end
    clearAntiKickAnyToys()
end

function setAnyItemAntiKickState(q)
    q6 = q and true or false
    if c6 then
        task.cancel(c6)
        c6 = nil
    end
    if q6 then c6 = task.spawn(StartAnyItemAntiKickLoop) else clearAntiKickAnyToys() end
    _G.AntiKick = ((_G.ShurikenAntiKick or q6)) and true or false
end

function ensureShurikenAntiKickWatchdog()
    if d6 and coroutine.status(d6) ~= "dead" then return end
    d6 = task.spawn(function()
        local q = 0
        while true do
            task.wait(.7)
            if not _G.ShurikenAntiKick then q = 0 elseif not M6 then
                local c = tick()
                if c - q >= 1.4 then
                    q = c
                    local r = M and M.ShurikenAntiKickToggle
                    if r and (r.Value and r.SetValue) then pcall(function() r:SetValue(true) end) end
                end
            end
        end
    end)
end

function setRagalicShurikenAntiKickState(q)
    _G.ShurikenAntiKick = q and true or false
    _G.AntiKick = ((_G.ShurikenAntiKick or q6)) and true or false
    setSelfAntiKickVisualState(wN.selfAntiKickVisualEnabled)
    G6 = G6 + 1
    local c = G6
    local function r()
        local q, c = getMenuToyRemotes()
        for q, r in ipairs(getLocalToyContainers()) do for q, r in ipairs(r:GetChildren()) do
                local j = string.lower(tostring(r.Name or ""))
                if j == "antikick" or j == "ninjashuriken" then
                    if c then pcall(function() c:FireServer(r) end) end
                    pcall(function() r:Destroy() end)
                end
            end end
    end
    if M6 then
        task.cancel(M6)
        M6 = nil
    end
    if not _G.ShurikenAntiKick then
        r()
        ensureAntiKickSelfVisual()
        return
    end
    M6 = task.spawn(function()
        local q = b
        local j = game:GetService("ReplicatedStorage")
        local u = (j:WaitForChild("GrabEvents")):WaitForChild("SetNetworkOwner")
        local M = (j:WaitForChild("PlayerEvents")):WaitForChild("StickyPartEvent")
        local G = q:WaitForChild("CanSpawnToy")
        local function d()
            local c = q.Character or q.CharacterAdded:Wait()
            return c and c:FindFirstChild("HumanoidRootPart")
        end
        local function z(c)
            if not c then return end
            local r = c:FindFirstChild("StickyPart", true)
            local j = d()
            if not ((r and j)) then return end
            local G = c:FindFirstChild("SoundPart", true)
            if G and G:IsA("BasePart") then
                local c = G:FindFirstChild("PartOwner")
                if (not c) or c.Value ~= q.Name then pcall(function() u:FireServer(G, G.CFrame) end) end
            end
            local z = j:FindFirstChild("FirePlayerPart") or j
            pcall(function() M:FireServer(r, z, CFrame.new(0, 0, 0) * CFrame.Angles(0, math.rad(90), math.rad(90))) end)
            for q, c in ipairs(c:GetDescendants()) do if c:IsA("BasePart") then
                    c.CanTouch = false
                    c.CanCollide = false
                    c.CanQuery = false
                    if c.Name == "Pyramid" or c.Name == "Main" then c.Transparency = 0 else c.Transparency = 1 end
                end end
        end
        local function Y()
            local q = tick()
            while G and (not G.Value) do
                if c ~= G6 or (not _G.ShurikenAntiKick) or tick() - q > 5 then return nil end
                task.wait(.1)
            end
            local r = spawnOwnedToyByName("NinjaShuriken", CFrame.new(0, 12, 20))
            if r then pcall(function() r.Name = "AntiKick" end) end
            return r
        end
        while c == G6 and _G.ShurikenAntiKick do
            task.wait(.01)
            local c = q.Character
            local j = c and c:FindFirstChildOfClass("Humanoid")
            local u = c and c:FindFirstChild("HumanoidRootPart")
            if not c or not j or j.Health <= 0 or not u then continue end
            local M = nil
            for q, c in ipairs(getLocalToyContainers()) do
                M = c:FindFirstChild("NinjaShuriken") or c:FindFirstChild("AntiKick")
                if M then break end
            end
            if not M then
                M = Y()
                if not M then continue end
            end
            pcall(function() M.Name = "AntiKick" end)
            local G = M:FindFirstChild("StickyPart", true)
            if G and G:IsA("BasePart") then
                if G.CanTouch == true then z(M) end
                local q = ((u.Position - G.Position)).Magnitude
                if q >= 20 then z(M) end
                if q >= 30 then r() end
            else z(M) end
        end
        r()
        M6 = nil
        ensureAntiKickSelfVisual()
    end)
end

function setToggleCursorState(q)
    ca = q and true or false
    if ra then
        task.cancel(ra)
        ra = nil
    end
    if ca then ra = task.spawn(function() while task.wait() do
                if not ca then break end
                O.MouseIconEnabled = true
            end end) else applyMenuCursorState() end
end

function getDynamicWaterModel()
    local q = Y:FindFirstChild("Map")
    local c = q and q:FindFirstChild("AlwaysHereTweenedObjects")
    local r = c and c:FindFirstChild("Ocean")
    local j = r and r:FindFirstChild("Object")
    local u = j and j:FindFirstChild("ObjectModel")
    if u and u:IsA("Model") then return u end
    return nil
end

function setDynamicWaterState(q)
    ja = q and true or false
    local c = Y.Terrain
    if ja then
        ua = {}
        Ma = {}
        local q = getDynamicWaterModel()
        if not q then return false end
        for q, r in ipairs(q:GetChildren()) do if r:IsA("Part") then
                local q = r.Size
                local j = r.CFrame
                local u = (Region3.new((j.Position - q / 2), (j.Position + q / 2))):ExpandToGrid(4)
                c:FillRegion(u, 4, Enum.Material.Water)
                Ma[#Ma + 1] = u
                ua[r] = { Transparency = r.Transparency, CanCollide = r.CanCollide, CanTouch = r.CanTouch, CanQuery = r
                .CanQuery }
                r.Transparency = 1
                r.CanCollide = false
                r.CanTouch = false
                r.CanQuery = false
            end end
        return true
    end
    for q, r in ipairs(Ma) do pcall(function() c:FillRegion(r, 4, Enum.Material.Air) end) end
    for q, c in pairs(ua) do if q and (q.Parent and c) then
            q.Transparency = c.Transparency
            q.CanCollide = c.CanCollide
            q.CanTouch = c.CanTouch
            q.CanQuery = c.CanQuery
        end end
    ua = {}
    Ma = {}
    return true
end

function applyPalletVisualModel(q)
    if not q then return end
    for q, c in ipairs(q:GetDescendants()) do if c:IsA("BasePart") then if Ga then
                if not Oa[c] then Oa[c] = { Transparency = c.Transparency, Color = c.Color, Material = c.Material } end
                c.Transparency = da
                c.Color = za
                c.Material = Ya
            elseif Oa[c] then
                local q = Oa[c]
                c.Transparency = q.Transparency
                c.Color = q.Color
                c.Material = q.Material
                Oa[c] = nil
            end end end
end

function refreshPalletVisuals()
    local q = getLocalToyFolder()
    if not q then return end
    for q, c in ipairs(q:GetChildren()) do if string.find(string.lower(c.Name), "pallet", 1, true) then
            applyPalletVisualModel(c) end end
end

function setPalletVisualState(q)
    Ga = q and true or false
    if Pa then
        Pa:Disconnect()
        Pa = nil
    end
    refreshPalletVisuals()
    local c = getLocalToyFolder()
    if c then Pa = c.ChildAdded:Connect(function(q) if string.find(string.lower(q.Name), "pallet", 1, true) then
                task.wait()
                applyPalletVisualModel(q)
            end end) end
end

function getSelectedTargetName()
    if u and (u.TargetPlayer and u.TargetPlayer.Value) then return tostring(u.TargetPlayer.Value) end
    return ""
end

function selectTargetByPartialNick(q)
    local c = tostring(q or "")
    if c == "" then return false end
    local r = string.lower(c)
    local j = nil
    local M = math.huge
    for q, c in ipairs(G:GetPlayers()) do if c ~= b then
            local q = string.lower(c.Name)
            local u = string.lower(c.DisplayName)
            local G = string.find(q, r, 1, true)
            local d = string.find(u, r, 1, true)
            if G or d then
                local q = G or d or 99
                local r = (q * 10) + #c.Name
                if r < M then
                    M = r
                    j = c
                end
            end
        end end
    if j and (u and (u.TargetPlayer and u.TargetPlayer.SetValue)) then
        u.TargetPlayer:SetValue(j.Name)
        return true
    end
    return false
end

function getGrabbedCharacter()
    local q = Y:FindFirstChild("GrabParts")
    local c = q and q:FindFirstChild("GrabPart")
    local r = c and c:FindFirstChildOfClass("WeldConstraint")
    local j = r and r.Part1
    local u = j and j:FindFirstAncestorOfClass("Model")
    local M = u and u:FindFirstChildOfClass("Humanoid")
    if u and M then return u end
    return nil
end

function getGrabEventsBundle()
    local q = d:FindFirstChild("GrabEvents")
    if not q then return nil, nil, nil, nil end
    local c = q:FindFirstChild("SetNetworkOwner")
    local r = q:FindFirstChild("CreateGrabLine")
    local j = q:FindFirstChild("DestroyGrabLine")
    local u = q:FindFirstChild("ExtendGrabLine")
    return c, r, j, u
end

function getCurrentGrabbedPart()
    local q = Y:FindFirstChild("GrabParts")
    local c = q and q:FindFirstChild("GrabPart")
    local r = c and ((c:FindFirstChild("WeldConstraint") or c:FindFirstChildOfClass("WeldConstraint")))
    local j = r and r.Part1
    if j and j.Parent then return j end
    return nil
end

function getOwningPlayerFromPart(q)
    if not q or not q.Parent then return nil end
    local c = q:FindFirstAncestorOfClass("Model")
    local r = c and c:FindFirstChildOfClass("Humanoid")
    if c and r then
        local q = G:GetPlayerFromCharacter(c)
        if q then return q end
    end
    local j = q:FindFirstChild("PartOwner") or q:FindFirstChild("PartOwner", true)
    if j and (j:IsA("StringValue") and j.Value ~= "") then return G:FindFirstChild(j.Value) end
    return nil
end

function refreshGrabLineHazards(q)
    if (not q) and tick() < ((tonumber(grabLineHazardRefreshAt) or 0)) then return end
    grabLineHazardRefreshAt = tick() + 6
    grabLineHazards.poison = nil
    grabLineHazards.radioactive = nil
    grabLineHazards.burn = nil
    for q, c in ipairs(Y:GetDescendants()) do if c:IsA("BasePart") then
            local q = string.lower(c.Name or "")
            if (not grabLineHazards.poison) and ((q:find("poison", 1, true) or q:find("toxic", 1, true))) then grabLineHazards.poison =
                c end
            if (not grabLineHazards.radioactive) and ((q:find("radio", 1, true) or q:find("nuclear", 1, true))) then grabLineHazards.radioactive =
                c end
            if (not grabLineHazards.burn) and ((q:find("fire", 1, true) or q:find("lava", 1, true) or q:find("burn", 1, true))) then grabLineHazards.burn =
                c end
            if grabLineHazards.poison and (grabLineHazards.radioactive and grabLineHazards.burn) then break end
        end end
    if not grabLineHazards.poison then grabLineHazards.poison = Y:FindFirstChild("PoisonHurtPart", true) end
    if not grabLineHazards.burn then grabLineHazards.burn = Y:FindFirstChild("apagarfogo", true) end
end

function hasAnyGrabLineModEnabled() return grabPoisonActive or grabRadioactiveActive or grabBurnActive or grabKillActive or
    grabFlingActive or grabNoclipModActive or grabCrazyActive or grabSpinActive or grabUltraActive or
    ultraClickGrabActive or lineInvisibleActive or lineExtendActive or lineCrazyPlayersActive or lineCrazyAllPartsActive or
    lineCrazyAllToysActive end

function StartGrabLineModsLoop()
    local q = 0
    local c = 0
    local r = 0
    while hasAnyGrabLineModEnabled() do
        local j = tick()
        local u, M, d, z = getGrabEventsBundle()
        if lineInvisibleActive and (M and j - q >= .09) then
            q = j
            pcall(function() M:FireServer() end)
        end
        if lineExtendActive and (z and j - q >= .09) then
            q = j
            pcall(function() z:FireServer(1000000) end)
        end
        if ((lineCrazyPlayersActive or lineCrazyAllPartsActive or lineCrazyAllToysActive)) and (M and j - c >= .32) then
            c = j
            if lineCrazyPlayersActive then for q, c in ipairs(G:GetPlayers()) do if c ~= b and c.Character then
                        local q = c.Character:FindFirstChild("Head") or c.Character:FindFirstChild("HumanoidRootPart")
                        if q and q:IsA("BasePart") then pcall(function() M:FireServer(q, Vector3.zero, q.Position, false) end) end
                    end end end
            if lineCrazyAllPartsActive then
                local q = 0
                for c, r in ipairs(Y:GetDescendants()) do if r:IsA("BasePart") and (r.Parent and ((not b.Character or not r:IsDescendantOf(b.Character)))) then
                        pcall(function() M:FireServer(r, Vector3.zero, r.Position, false) end)
                        q = q + 1
                        if q >= 55 then break end
                    end end
            end
            if lineCrazyAllToysActive then
                local q = 0
                for c, r in ipairs(Y:GetChildren()) do if r:IsA("Folder") and (r.Name:sub(-13) == "SpawnedInToys" and r.Name ~= (b.Name .. "SpawnedInToys")) then
                        for c, r in ipairs(r:GetChildren()) do
                            local j = r:FindFirstChild("HumanoidRootPart") or r.PrimaryPart or
                            r:FindFirstChildWhichIsA("BasePart", true)
                            if j and j:IsA("BasePart") then
                                pcall(function() M:FireServer(j, Vector3.zero, j.Position, false) end)
                                q = q + 1
                                if q >= 70 then break end
                            end
                        end
                        if q >= 70 then break end
                    end end
            end
        end
        if ultraClickGrabActive and (u and j - r >= .06) then
            r = j
            local q = b and b:GetMouse()
            local c = q and q.Target
            if c and c:IsA("BasePart") then pcall(function() u:FireServer(c, c.CFrame) end) end
        end
        local P = getCurrentGrabbedPart()
        local O = b.Character
        local a = O and O:FindFirstChild("HumanoidRootPart")
        if P and P.Parent then
            if u then pcall(function() u:FireServer(P, P.CFrame) end) end
            if grabNoclipModActive then
                local q = P:FindFirstAncestorOfClass("Model")
                for q, c in ipairs((q and q:GetDescendants()) or { P }) do if c:IsA("BasePart") then
                        c.CanCollide = false
                        c.CanTouch = false
                        c.CanQuery = false
                    end end
            end
            if grabSpinActive then pcall(function() P.AssemblyAngularVelocity = Vector3.new(0, 120, 0) end) end
            if grabCrazyActive then pcall(function()
                    P.AssemblyAngularVelocity = Vector3.new(math.random(-240, 240), math.random(-240, 240),
                        math.random(-240, 240))
                    P.AssemblyLinearVelocity = P.AssemblyLinearVelocity +
                    Vector3.new(math.random(-95, 95), math.random(20, 110), math.random(-95, 95))
                end) end
            if grabUltraActive and a then pcall(function()
                    P.CFrame = a.CFrame * CFrame.new(0, 2.5, -7)
                    P.AssemblyLinearVelocity = a.CFrame.LookVector * 120 + Vector3.new(0, 36, 0)
                end) end
            if j >= ((tonumber(grabLineNextPulseAt) or 0)) then
                grabLineNextPulseAt = j + .22
                refreshGrabLineHazards(false)
                local q = getOwningPlayerFromPart(P)
                local c = q and q.UserId
                local r = q and (q.Character and q.Character:FindFirstChild("HumanoidRootPart"))
                if grabFlingActive and (q and (q ~= b and r)) then pcall(function()
                        local q = r.Position - (((a and a.Position) or (r.Position - Vector3.new(0, 0, 1))))
                        local c = q.Magnitude > .01 and q.Unit or Vector3.new(0, 0, 1)
                        r.AssemblyLinearVelocity = c * 210 + Vector3.new(0, 120, 0)
                        r.AssemblyAngularVelocity = Vector3.new(math.random(-180, 180), math.random(-300, 300),
                            math.random(-180, 180))
                    end) end
                if grabKillActive and (q and (q ~= b and (r and c))) then
                    local q = "kill_" .. tostring(c)
                    local u = tonumber(grabModNextByUserId[q]) or 0
                    if j >= u then
                        grabModNextByUserId[q] = j + 1.2
                        pcall(function()
                            r.CFrame = CFrame.new(r.Position.X, -920, r.Position.Z)
                            r.AssemblyLinearVelocity = Vector3.new(0, -240, 0)
                        end)
                    end
                elseif grabKillActive and (not q) then pcall(function()
                        P.CFrame = CFrame.new(P.Position.X, -920, P.Position.Z)
                        P.AssemblyLinearVelocity = Vector3.new(0, -200, 0)
                    end) end
                if grabPoisonActive and grabLineHazards.poison then pcall(function() P.CFrame = grabLineHazards.poison
                        .CFrame * CFrame.new(0, 2.5, 0) end) end
                if grabRadioactiveActive and grabLineHazards.radioactive then pcall(function() P.CFrame = grabLineHazards
                        .radioactive.CFrame * CFrame.new(0, 2.5, 0) end) end
                if grabBurnActive and grabLineHazards.burn then pcall(function() P.CFrame = grabLineHazards.burn.CFrame *
                        CFrame.new(0, 2.5, 0) end) end
            end
        end
        task.wait(.05)
    end
    grabLineModsTask = nil
end

function refreshGrabLineModsLoop() if hasAnyGrabLineModEnabled() then if not grabLineModsTask then grabLineModsTask =
            task.spawn(StartGrabLineModsLoop) end else if grabLineModsTask then
            task.cancel(grabLineModsTask)
            grabLineModsTask = nil
        end end end

function removeGrabbedLimbs(q, c)
    local r = getGrabbedCharacter()
    if not r then return false end
    local j = r:FindFirstChild("Torso") or r:FindFirstChild("UpperTorso") or r:FindFirstChild("HumanoidRootPart")
    if not j then return false end
    local u = Y.FallenPartsDestroyHeight
    Y.FallenPartsDestroyHeight = -50000
    local function M(q)
        local c = r:FindFirstChild(q)
        if c and c:IsA("BasePart") then c.CFrame = CFrame.new(0, -60000, 0) end
    end
    if q then
        M("Left Leg")
        M("Right Leg")
        M("LeftLowerLeg")
        M("RightLowerLeg")
    end
    if c then
        M("Left Arm")
        M("Right Arm")
        M("LeftLowerArm")
        M("RightLowerArm")
    end
    task.wait(.1)
    if j and j.Parent then j.CFrame = CFrame.new(0, -55970, 0) end
    task.wait(.1)
    Y.FallenPartsDestroyHeight = u
    return true
end

function tryRemoveGrabbedLimbs(q, r, j)
    local u = removeGrabbedLimbs(q, r)
    if u then return true end
    local M = tick()
    if M - ((tonumber(Z6) or 0)) >= 1.1 then
        Z6 = M
        c:Notify({ Title = "Wourld Hub", Description = "No grabbed target for " .. tostring(j or "limb key"), Duration = 2 })
    end
    return false
end

function freezeGrabbedObject()
    local q = getGrabbedCharacter()
    if not q then return false end
    for q, c in ipairs(q:GetDescendants()) do if c:IsA("BasePart") then
            c.Anchored = true
            c.AssemblyLinearVelocity = Vector3.zero
            c.AssemblyAngularVelocity = Vector3.zero
        end end
    return true
end

function getLocalBlobContext()
    local q = b.Character
    local c = q and q:FindFirstChildOfClass("Humanoid")
    local r = q and q:FindFirstChild("HumanoidRootPart")
    local j = c and c.SeatPart
    local u = j and j.Parent
    if not u or u.Name ~= "CreatureBlobman" then return nil end
    local M = u:FindFirstChild("BlobmanSeatAndOwnerScript")
    if not M then return nil end
    return { Character = q, Humanoid = c, Root = r, Blob = u, Script = M }
end

function ownerBringHand(q, c, r)
    local j = getLocalBlobContext()
    if not j or not q or q == "" then return false end
    if isSelfTargetName and isSelfTargetName(q) then return false end
    local u = G:FindFirstChild(q)
    local M = u and (u.Character and u.Character:FindFirstChild("HumanoidRootPart"))
    if not u or not M then return false end
    local d = j.Blob:FindFirstChild(c .. "Detector")
    local z = d and d:FindFirstChild(c .. "Weld")
    if not d or not z then return false end
    local Y = j.Script:FindFirstChild("CreatureGrab")
    local P = j.Script:FindFirstChild("CreatureDrop")
    local O = j.Script:FindFirstChild("CreatureRelease")
    if r == "grab" and Y then
        Y:FireServer(d, M, z)
        return true
    end
    if r == "drop" and P then
        P:FireServer(z, M)
        return true
    end
    if r == "release" and (O and j.Root) then
        O:FireServer(z, j.Root)
        return true
    end
    return false
end

function ownerBringOnce(q)
    local c = getLocalBlobContext()
    local r = G:FindFirstChild(tostring(q or ""))
    local j = r and (r.Character and r.Character:FindFirstChild("HumanoidRootPart"))
    if not c or not j then return false end
    local u = c.Root and c.Root.CFrame
    local M = false
    for r = 1, 14, 1 do
        if not Ta then break end
        if c.Root and j.Parent then
            c.Root.CFrame = j.CFrame * CFrame.new(0, 9, 0)
            ownerBringHand(q, "Right", "grab")
            task.wait(.04)
            ownerBringHand(q, "Right", "release")
            M = true
        end
    end
    if u and c.Root then c.Root.CFrame = u end
    return M
end

function stopOwnerWalkLoop()
    ha = false
    Va = false
    if ta then
        task.cancel(ta)
        ta = nil
    end
end

function startOwnerWalkLoop(q)
    stopOwnerWalkLoop()
    if q == "me" then ha = true elseif q == "mouse" then Va = true else return end
    ta = task.spawn(function() while ha or Va do
            local q = getSelectedTargetName()
            local c = G:FindFirstChild(q)
            local r = c and (c.Character and c.Character:FindFirstChildOfClass("Humanoid"))
            local j = b.Character and b.Character:FindFirstChild("HumanoidRootPart")
            if r and j then if ha then r:MoveTo(j.Position) elseif Va then
                    local q = b:GetMouse()
                    r:MoveTo(q.Hit.Position)
                end end
            task.wait(.08)
        end end)
end

function applyBacktrackNow()
    local q = getSelectedTargetName()
    if q == "" then return false end
    local c = Xa[q]
    if not c or #c == 0 then return false end
    local r = c[1]
    local j = tick()
    for q = #c, 1, -1 do
        local u = c[q]
        if j - u.t >= .2 then
            r = u
            break
        end
    end
    local u = b.Character and b.Character:FindFirstChild("HumanoidRootPart")
    if not u or not r or not r.cf then return false end
    u.CFrame = r.cf * CFrame.new(0, 2.5, 2.8)
    return true
end

function StartBacktrackLoop() while va do
        local q = getSelectedTargetName()
        local c = G:FindFirstChild(q)
        local r = c and (c.Character and c.Character:FindFirstChild("HumanoidRootPart"))
        if r then
            local c = Xa[q]
            if not c then
                c = {}
                Xa[q] = c
            end
            c[#c + 1] = { t = tick(), cf = r.CFrame }
            while #c > xa do table.remove(c, 1) end
            local j = tick() - math.clamp(Ua, .25, 3)
            while #c > 0 and c[1].t < j do table.remove(c, 1) end
        end
        task.wait(.055)
    end end

function setBacktrackState(q)
    va = q and true or false
    if ba then
        task.cancel(ba)
        ba = nil
    end
    if va then ba = task.spawn(StartBacktrackLoop) end
end

function StartUnstickAuraLoop() while aa do
        local q = b.Character
        local c = q and q:FindFirstChildOfClass("Humanoid")
        local r = q and q:FindFirstChild("HumanoidRootPart")
        local j = b:FindFirstChild("IsHeld")
        local u = c and c:FindFirstChild("Ragdolled")
        if q and (c and (r and c.Health > 0)) then if (j and j.Value) or (u and u.Value) then
                pcall(function()
                    local q = d:FindFirstChild("CharacterEvents") and d.CharacterEvents:FindFirstChild("Struggle")
                    if q then q:FireServer("Unbind") end
                end)
                pcall(function()
                    local q = d:FindFirstChild("CharacterEvents") and d.CharacterEvents:FindFirstChild("RagdollRemote")
                    if q then q:FireServer(r, 0) end
                end)
                pcall(function()
                    r.AssemblyLinearVelocity = Vector3.zero
                    r.AssemblyAngularVelocity = Vector3.zero
                end)
            end end
        task.wait(.12)
    end end

function setUnstickAuraState(q)
    aa = q and true or false
    if oa then
        task.cancel(oa)
        oa = nil
    end
    if aa then oa = task.spawn(StartUnstickAuraLoop) end
end

function restartTargetBoundActions()
    local q = getSelectedTargetName()
    if q == "" then return end
    if M and (M.RemoveAntiKickToggle and M.RemoveAntiKickToggle.Value) then
        if oN then
            task.cancel(oN)
            oN = nil
        end
        oN = task.spawn(function() RemoveAntiKickFunction(q) end)
    end
    if M and (M.OwnershipKickToggle and M.OwnershipKickToggle.Value) then
        if W then
            task.cancel(W)
            W = nil
        end
        W = task.spawn(function() StartOwnershipKick(q) end)
    end
    if M and (M.OwnershipRagdollToggle and M.OwnershipRagdollToggle.Value) then
        if R then
            task.cancel(R)
            R = nil
        end
        R = task.spawn(function() PalletRagdollFunction(q) end)
    end
    if M and (M.LoopKillToggle and M.LoopKillToggle.Value) then
        if y then
            task.cancel(y)
            y = nil
        end
        y = task.spawn(function() LoopKillFunction(q) end)
    end
    if M and (M.SnowballRagdollToggle and M.SnowballRagdollToggle.Value) then
        if k then
            task.cancel(k)
            k = nil
        end
        k = task.spawn(function() SnowballRagdollFunction(q) end)
    end
    if M and (M.LoopAppleMethod and M.LoopAppleMethod.Value) then task.defer(function() if M and (M.LoopAppleMethod and M.LoopAppleMethod.Value) then
                M.LoopAppleMethod:SetValue(false)
                task.wait()
                M.LoopAppleMethod:SetValue(true)
            end end) end
end

function requestAutoRespawn(q, r, j)
    if E6 then if j and (tick() - f6 >= 1.2) then E6 = false else return end end
    if E6 then return end
    local u = tick()
    if (not j) and (u - f6 < 2.6) then return end
    f6 = u
    E6 = true
    if p6 then
        task.cancel(p6)
        p6 = nil
    end
    local function M(q)
        local c = game:GetService("TeleportService")
        local function r()
            local q = false
            pcall(function()
                c:TeleportToPlaceInstance(game.PlaceId, game.JobId, b)
                q = true
            end)
            if q then return true end
            pcall(function()
                c:Teleport(game.PlaceId, b)
                q = true
            end)
            return q
        end
        for q = 1, 4, 1 do
            if r() then return true end
            task.wait(.35 + q * .18)
        end
        local j = queueAutoRejoin(q or "reset timeout", .6)
        if j then return false end
        pcall(function() b:Kick("Auto leave: " .. tostring(q or "reset failed")) end)
        return false
    end
    local G = b.Character
    task.delay(r or 0,
        function()
            local r = false
            local j = b.Character
            local u = j and j:FindFirstChildOfClass("Humanoid")
            pcall(function() forceReleaseHeldItems(2200) end)
            local d = false
            pcall(function()
                b:LoadCharacter()
                d = true
                r = true
            end)
            if (not d) and (u and u.Health > 0) then pcall(function()
                    u.Health = 0
                    r = true
                end) end
            if tick() - H6 > 3 then
                H6 = tick()
                c:Notify({ Title = "Wourld Hub", Description = "Auto reset: " .. tostring(q or "protection"), Duration = 2.5 })
                playEventSound("reset")
            end
            task.spawn(function()
                local q = tick()
                while E6 and tick() - q <= 5.4 do
                    local q = b.Character
                    local c = q and q:FindFirstChildOfClass("Humanoid")
                    if q and (q ~= G and (c and c.Health > 0)) then
                        E6 = false
                        return
                    end
                    if q and (c and (c.Health > 0 and r)) then
                        local r = (q ~= G) or (c ~= u)
                        if r then
                            E6 = false
                            return
                        end
                    end
                    task.wait(.12)
                end
            end)
            if m6 then p6 = task.spawn(function()
                    task.wait(5.8)
                    if not E6 then return end
                    E6 = false
                    M(q or "reset timeout")
                end) end
            task.delay(6.2, function() if E6 then E6 = false end end)
        end)
end

function shouldBlockHeldAutoReset()
    if ownershipKickActive or kickActionBusy or loopKickBlobActive then return true end
    if _G and _G.ShurikenAntiKick then return true end
    if (_G and ((_G.AutoSitBlobZ or _G.AutoSitBloom))) then return true end
    local q = b and b.Character
    local c = q and q:FindFirstChildOfClass("Humanoid")
    local r = c and c.SeatPart
    if r and (r.Parent and r.Parent.Name == "CreatureBlobman") then return true end
    return tick() < ((kickSelfGuardUntil or 0))
end

function setKickThreatMonitorState(q)
    if W6 then
        W6:Disconnect()
        W6 = nil
    end
    if R6 then
        R6:Disconnect()
        R6 = nil
    end
    if gN.kickThreatHeldAddedConnection then
        gN.kickThreatHeldAddedConnection:Disconnect()
        gN.kickThreatHeldAddedConnection = nil
    end
    if gN.kickThreatHeldRemovedConnection then
        gN.kickThreatHeldRemovedConnection:Disconnect()
        gN.kickThreatHeldRemovedConnection = nil
    end
    if not q then return end
    local function r()
        if R6 then
            R6:Disconnect()
            R6 = nil
        end
        local q = b:FindFirstChild("IsHeld")
        if q and q:IsA("BoolValue") then
            R6 = q.Changed:Connect(function()
                local c = i6 and (((y6 or w6)) and (not shouldBlockHeldAutoReset()))
                if not c then
                    L6 = 0
                    return
                end
                if q.Value then
                    L6 = tick()
                    task.delay(.95,
                        function()
                            if not ((i6 and (q and (q.Parent and q.Value)))) then return end
                            if not ((y6 or w6)) then return end
                            if shouldBlockHeldAutoReset() then return end
                            requestAutoRespawn("held threat", .02, true)
                        end)
                else L6 = 0 end
            end)
            if i6 and (((y6 or w6)) and (q.Value and (not shouldBlockHeldAutoReset()))) then
                L6 = tick()
                task.delay(.95,
                    function() if q and (q.Parent and (q.Value and (i6 and (((y6 or w6)) and (not shouldBlockHeldAutoReset()))))) then
                            requestAutoRespawn("held threat", .02, true) end end)
            end
        end
    end
    r()
    gN.kickThreatHeldAddedConnection = b.ChildAdded:Connect(function(q) if q and q.Name == "IsHeld" then task.defer(r) end end)
    gN.kickThreatHeldRemovedConnection = b.ChildRemoved:Connect(function(q) if q and (q.Name == "IsHeld" and R6) then
            R6:Disconnect()
            R6 = nil
            L6 = 0
        end end)
    W6 = Y.ChildAdded:Connect(function(q)
        if not y6 or not isKickObjectName(q and q.Name) then return end
        if kickActionBusy or shouldBlockHeldAutoReset() or tick() < ((kickSelfGuardUntil or 0)) then return end
        task.delay(.04,
            function()
                local r = b.Character
                local j = r and r:FindFirstChild("HumanoidRootPart")
                if not j then return end
                local u = getInstanceWorldPosition(q)
                if not u then return end
                local M = ((j.Position - u)).Magnitude
                local G = b:FindFirstChild("IsHeld")
                local d = G and G.Value
                if M <= 40 or (M <= 75 and d) then
                    local q = tick()
                    K6 = q
                    local r = getClosestPlayer(u)
                    if r and r ~= b then
                        gN.kickThreatNotifyAt = gN.kickThreatNotifyAt or {}
                        local j = tostring(r.UserId or r.Name)
                        local u = gN.kickThreatNotifyAt[j] or 0
                        if q - u >= 2.6 then
                            gN.kickThreatNotifyAt[j] = q
                            c:Notify({ Title = "Wourld Hub", Description = "Kick threat detected from " ..
                            tostring(r.Name), Duration = 2.8 })
                            playEventSound("kick")
                        end
                    end
                    if q - k6 > 3.8 then
                        k6 = q
                        pcall(function() forceReleaseHeldItems(2400) end)
                        requestAutoRespawn("kick threat", .05, true)
                        c:Notify({ Title = "Wourld Hub", Description = "Kick defence auto recover triggered", Duration = 2.4 })
                        playEventSound("kick")
                    end
                end
            end)
    end)
end

function runRuntimeRepairCheck(q)
    local r = 0
    local j = {}
    if y6 and ((not W6 or not W6.Connected)) then
        setKickThreatMonitorState(true)
        r = r + 1
        j[#j + 1] = "kick monitor"
    end
    if w6 and not A6 then
        setAutoRespawnState(true)
        r = r + 1
        j[#j + 1] = "auto respawn"
    end
    if q6 and not c6 then
        c6 = task.spawn(StartAnyItemAntiKickLoop)
        r = r + 1
        j[#j + 1] = "any item anti-kick"
    end
    if removeAntiKickAuraActive and not gN.removeAntiKickAuraTask then
        gN.removeAntiKickAuraTask = task.spawn(RemoveAntiKickAuraFunction)
        r = r + 1
        j[#j + 1] = "anti-kick aura"
    end
    if _G.ShurikenAntiKick and not M6 then
        local q = M and M.ShurikenAntiKickToggle
        if q and q.SetValue then
            pcall(function() q:SetValue(true) end)
            r = r + 1
            j[#j + 1] = "anti-kick loop"
        end
    end
    if wN.antiKickOwnerEnabled and not wN.antiKickOwnerTask then
        setAntiKickOwnerEspState(true)
        r = r + 1
        j[#j + 1] = "anti-kick owner esp"
    end
    local u = tostring(q or "manual")
    if r > 0 then c:Notify({ Title = "Wourld Hub", Description = "Runtime repair (" ..
        (u .. ("): fixed " .. (tostring(r) .. (" [" .. (table.concat(j, ", ") .. "]"))))), Duration = 3.8 }) else c
            :Notify({ Title = "Wourld Hub", Description = "Runtime check (" .. (u .. "): all core features healthy"), Duration = 2.6 }) end
end

function notifyAuraThreat(q, r, j)
    local u = string.lower(tostring(q or "aura") .. (":" .. tostring(r or "unknown")))
    local M = tick()
    gN.auraShieldNotifyAt = gN.auraShieldNotifyAt or {}
    local G = gN.auraShieldNotifyAt[u] or 0
    if M - G < 2.2 then return end
    gN.auraShieldNotifyAt[u] = M
    c:Notify({ Title = "Wourld Hub", Description = tostring(q or "Aura") .. (" aura threat: " .. tostring(r or "Unknown")), Duration =
    tonumber(j) or 2.6 })
    playEventSound("kick")
end

function evaluateAuraThreat(q, c)
    if not q or not c or c == b then return nil end
    local r = c.Character
    local j = r and r:FindFirstChildOfClass("Humanoid")
    local u = r and r:FindFirstChild("HumanoidRootPart")
    if not j or not u or j.Health <= 0 then return nil end
    local M = math.clamp(tonumber(gN.auraShieldRadius) or 28, 8, 90)
    local G = ((u.Position - q.Position)).Magnitude
    if G > M then return nil end
    local d = r:FindFirstChildOfClass("Tool") or c.Backpack:FindFirstChildOfClass("Tool")
    local z = d ~= nil
    local Y = string.lower(tostring(d and d.Name or ""))
    local P = u.AssemblyLinearVelocity.Magnitude
    local O = j.SeatPart and (j.SeatPart.Parent and j.SeatPart.Parent.Name == "CreatureBlobman")
    local a = O or (z and G <= M * .72)
    local o = isKickObjectName(Y) or (P > 70 and G <= M * .9)
    local v = (z and G <= math.max(9, M * .35)) or P > 95
    return G, a, o, v
end

function SetAuraShieldState(q)
    gN.auraShield = q and true or false
    if gN.auraShieldTask then
        task.cancel(gN.auraShieldTask)
        gN.auraShieldTask = nil
    end
    if not gN.auraShield then return end
    gN.auraShieldTask = task.spawn(function() while gN.auraShield do
            local q = b.Character
            local c = q and q:FindFirstChildOfClass("Humanoid")
            local r = q and q:FindFirstChild("HumanoidRootPart")
            if q and (c and (r and c.Health > 0)) then
                local q = b:FindFirstChild("IsHeld")
                local j = math.clamp(tonumber(gN.auraShieldRadius) or 28, 8, 90)
                local u = math.max(9, j * .45)
                for j, M in ipairs(G:GetPlayers()) do if M ~= b then
                        local j, G, d, z = evaluateAuraThreat(r, M)
                        if j then
                            if gN.auraShieldGrabAlert and G then notifyAuraThreat("Grab", M.Name, 2.4) end
                            if gN.auraShieldKickAlert and d then notifyAuraThreat("Kick", M.Name, 2.4) end
                            if gN.auraShieldKillAlert and z then notifyAuraThreat("Kill", M.Name, 2.4) end
                            if ((G or d or z)) and j <= u then if q and q.Value then requestAutoRespawn("aura threat",
                                        .05) else
                                    c.PlatformStand = false
                                    c.Jump = true
                                    pcall(function() c:ChangeState(Enum.HumanoidStateType.Jumping) end)
                                    r.AssemblyLinearVelocity = Vector3.new(r.AssemblyLinearVelocity.X * .2,
                                        math.max(r.AssemblyLinearVelocity.Y, 56), r.AssemblyLinearVelocity.Z * .2)
                                end end
                        end
                    end end
            end
            task.wait(.16)
        end end)
end

function isLikelyMainMenuFrame(q)
    if not q or not q:IsA("Frame") then return false end
    local c = q.AbsoluteSize
    if c.X < 260 or c.Y < 180 then return false end
    local r = q:FindFirstChild("PanelBackground", true) ~= nil
    if r then return true end
    if q.BackgroundTransparency >= .98 and #q:GetChildren() < 4 then return false end
    return true
end

function getMenuOpenStateFromUI()
    local q = c.ScreenGui
    if not q or not q.Parent then return nil end
    local r = false
    for q, c in ipairs(q:GetChildren()) do if isLikelyMainMenuFrame(c) then
            r = true
            if c.Visible then return true end
        end end
    if r then return false end
    return nil
end

function getMainWourldFrame()
    local q = c.ScreenGui
    if not q or not q.Parent then return nil end
    local r = nil
    local j = 0
    for q, c in ipairs(q:GetChildren()) do if isLikelyMainMenuFrame(c) then
            local q = c.AbsoluteSize.X * c.AbsoluteSize.Y
            if q > j then
                j = q
                r = c
            end
        end end
    if r then return r end
    for q, c in ipairs(q:GetChildren()) do if c:IsA("Frame") then
            local q = c.AbsoluteSize.X * c.AbsoluteSize.Y
            if q > j then
                j = q
                r = c
            end
        end end
    return r
end

function resolveMenuBackgroundImageSource(q)
    local c = ((tostring(q or "")):gsub("^%s+", "")):gsub("%s+$", "")
    local r = c:match("%b[]%((https?://[^%)]+)%)")
    if r then c = r end
    c = (c:gsub("^<", "")):gsub(">$", "")
    c = (c:gsub("^[\'\"]", "")):gsub("[\'\"]$", "")
    local j = c:match("(https?://%S+)")
    if j then c = j end
    if c == "" then return nil end
    local u = string.lower(c)
    if u:find("^rbxassetid://") or u:find("^rbxasset://") then return c end
    if c:match("^%d+$") then return "rbxassetid://" .. c end
    if u:find("^https://media.discordapp.net/attachments/", 1, true) == 1 then
        c = "https://cdn.discordapp.com/attachments/" .. c:sub(42)
        u = string.lower(c)
    elseif u:find("^http://media.discordapp.net/attachments/", 1, true) == 1 then
        c = "https://cdn.discordapp.com/attachments/" .. c:sub(41)
        u = string.lower(c)
    end
    local M = getcustomasset or getsynasset
    if ((u:find("^http://") or u:find("^https://"))) and (writefile and M) then
        local q = string.lower((c:match("^[^%?#]+")) or c)
        local r = q:match("%.([a-z0-9]+)$") or ""
        if r == "jpeg" then r = "jpg" end
        local j = nil
        local G = nil
        local d = (syn and syn.request) or http_request or request or (http and http.request) or
        (fluxus and fluxus.request)
        if d then pcall(function()
                local q = d({ Url = c, Method = "GET", Headers = { ["User-Agent"] = "Mozilla/5.0", Accept = "image/*,*/*;q=0.8", Referer = "https://discord.com/" } })
                local r = tonumber(q and ((q.StatusCode or q.status_code or q.Status)))
                if r and (r >= 200 and r < 300) then
                    j = q.Body or q.body
                    local c = q.Headers or q.headers
                    if type(c) == "table" then for q, c in pairs(c) do if string.lower(tostring(q)) == "content-type" then
                                G = tostring(c)
                                break
                            end end end
                end
            end) end
        if ((not j or #tostring(j) < 8)) and (game and game.HttpGet) then pcall(function() j = game:HttpGet(c) end) end
        if ((not j or #tostring(j) < 8)) and ((u:find("cdn.discordapp.com/attachments/", 1, true) or u:find("media.discordapp.net/attachments/", 1, true))) then
            local q = c:gsub("^https://media%.discordapp%.net/", "https://cdn.discordapp.com/")
            if q ~= c and (game and game.HttpGet) then pcall(function()
                    j = game:HttpGet(q)
                    c = q
                end) end
        end
        if (not j) or (#tostring(j) < 8) then return nil end
        if r ~= "png" and (r ~= "jpg" and r ~= "webp") then r = "" end
        if r == "" then
            local q = string.lower(tostring(G or ""))
            if q:find("image/png", 1, true) then r = "png" elseif q:find("image/jpeg", 1, true) or q:find("image/jpg", 1, true) then r =
                "jpg" elseif q:find("image/webp", 1, true) then r = "webp" else r = "png" end
        end
        local z = "Wourld_Hub/assets/menu_background." .. r
        t("Wourld_Hub/assets")
        local Y = pcall(function() writefile(z, j) end)
        if Y then
            local q, c = pcall(function() return M(z) end)
            if q and (type(c) == "string" and c ~= "") then return c end
        end
        return nil
    end
    if isfile and M then
        local q, r = pcall(function() return isfile(c) end)
        if q and r then
            local q, r = pcall(function() return M(c) end)
            if q and (type(r) == "string" and r ~= "") then return r end
        end
    end
    if M then
        local q, r = pcall(function() return M(c) end)
        if q and (type(r) == "string" and r ~= "") then return r end
    end
    return nil
end

function applyMenuBackgroundState()
    local q = getMainWourldFrame()
    if not q then return end
    local r = q:FindFirstChild("WourldCustomMenuBackground")
    if not menuBackgroundEnabled then
        if r then r:Destroy() end
        return
    end
    local j = resolveMenuBackgroundImageSource(menuBackgroundSource)
    if not j then
        if r then r:Destroy() end
        local q = ((tostring(menuBackgroundSource or "")):gsub("^%s+", "")):gsub("%s+$", "") ~= ""
        if q and (tick() - ((tonumber(menuBackgroundFailNotifyAt) or 0))) > 1.2 then
            menuBackgroundFailNotifyAt = tick()
            c:Notify({ Title = "Wourld Hub", Description =
            "Background load failed. Use direct image URL (cdn.discordapp.com / png / jpg) or local file path.", Duration = 3.2 })
        end
        return
    end
    local u = r
    if not u then
        u = Instance.new("ImageLabel")
        u.Name = "WourldCustomMenuBackground"
        u.BackgroundTransparency = 1
        u.BorderSizePixel = 0
        u.AnchorPoint = Vector2.new(0, 0)
        u.Position = UDim2.fromScale(0, 0)
        u.Size = UDim2.fromScale(1, 1)
        u.ZIndex = 0
        u.ScaleType = Enum.ScaleType.Crop
        u.Parent = q
    end
    u.Image = B(j)
    u.ImageTransparency = math.clamp(tonumber(menuBackgroundTransparency) or .22, 0, 1)
end

function setPhoneModeState(q)
    phoneModeEnabled = q and true or false
    local r = c.ScreenGui
    if r and r.Parent then
        if (not phoneModeScale) or (not phoneModeScale.Parent) then
            phoneModeScale = r:FindFirstChild("WourldPhoneModeScale")
            if not phoneModeScale then
                phoneModeScale = Instance.new("UIScale")
                phoneModeScale.Name = "WourldPhoneModeScale"
                phoneModeScale.Parent = r
            end
        end
        phoneModeScale.Scale = phoneModeEnabled and 1.16 or 1
        local q = getMainWourldFrame()
        if q then if phoneModeEnabled then
                if not phoneModeCache[q] then phoneModeCache[q] = { AnchorPoint = q.AnchorPoint, Position = q.Position, Size =
                    q.Size } end
                q.AnchorPoint = Vector2.new(.5, .5)
                q.Position = UDim2.fromScale(.5, .5)
                q.Size = UDim2.fromScale(.98, .96)
            else
                local c = phoneModeCache[q]
                if c then
                    q.AnchorPoint = c.AnchorPoint
                    q.Position = c.Position
                    q.Size = c.Size
                end
            end end
    end
    c:SetDPIScale(phoneModeEnabled and 145 or 100)
    if phoneModeEnabled then
        O.MouseBehavior = Enum.MouseBehavior.Default
        O.MouseIconEnabled = true
    end
    createMenuOpenIconButton()
    createPhoneMenuButton()
    createStatusHud()
    task.delay(.05, function() applyMenuBackgroundState() end)
end

function getMenuKeycode()
    local q = u and u.MenuKeybind
    local c = q and q.Value
    if typeof(c) == "EnumItem" then return c end
    local r = (tostring(c or "RightShift")):gsub("Enum%.KeyCode%.", "")
    return Enum.KeyCode[r] or Enum.KeyCode.RightShift
end

function normalizeRuntimeKeyCode(q, c)
    if typeof(q) == "EnumItem" then return q end
    local r = (tostring(q or "")):gsub("Enum%.KeyCode%.", "")
    return Enum.KeyCode[r] or c or Enum.KeyCode.Unknown
end

function isTextInputFocused()
    local q = nil
    pcall(function() q = O:GetFocusedTextBox() end)
    return q ~= nil
end

function isGameplayKeyboardInput(q)
    if not q or q.UserInputType ~= Enum.UserInputType.Keyboard then return false end
    if isTextInputFocused() then return false end
    return true
end

function getOptionKeyCode(q, c, r)
    local j = normalizeRuntimeKeyCode(c, r)
    local M = u and u[q]
    if M and M.Value ~= nil then j = normalizeRuntimeKeyCode(M.Value, j) end
    return j
end

function isPrimaryMouseHeld()
    if C then return true end
    local q = false
    pcall(function() q = O:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) end)
    return q and true or false
end

function isToyAttachedToCharacter(q, c)
    if not q or not c then return false end
    local r = q:FindFirstChild("HoldPart")
    if not r then return false end
    local function j(q) return q and (q:IsA("BasePart") and q:IsDescendantOf(c)) end
    for q, c in ipairs(r:GetDescendants()) do if c:IsA("WeldConstraint") then if j(c.Part0) or j(c.Part1) then return true end elseif c:IsA("Weld") or c:IsA("Motor6D") then if j(c.Part0) or j(c.Part1) then return true end end end
    return false
end

function getCharacterGripAttachment(q)
    if not q then return nil end
    local c = q:FindFirstChild("Left Arm")
    if c then
        local q = c:FindFirstChild("LeftGripAttachment")
        if q then return q end
    end
    local r = q:FindFirstChild("LeftHand")
    if r then
        local q = r:FindFirstChild("LeftGripAttachment")
        if q then return q end
    end
    return nil
end

function isLocalHoldingToyNow(q)
    local c = tonumber(gN.antiInputHoldScanAt) or 0
    if q < c then return gN.antiInputHoldState and true or false end
    local r = b and b.Character
    local j = false
    if r then for q, c in ipairs(getLocalToyContainers()) do
            for q, c in ipairs(c:GetChildren()) do if isToyAttachedToCharacter(c, r) then
                    j = true
                    break
                end end
            if j then break end
        end end
    gN.antiInputHoldState = j and true or false
    gN.antiInputHoldScanAt = q + .09
    return gN.antiInputHoldState
end

function isLocalGrabBusy()
    local q = tick()
    if q < ((tonumber(gN.antiInputLagManualUntil) or 0)) then return true end
    if kickActionBusy or ownershipKickActive or loopKickBlobActive then return true end
    local c = b and b.Character
    local r = c and c:FindFirstChildOfClass("Humanoid")
    local j = b and b:FindFirstChild("IsHeld")
    if j and j.Value then
        gN.antiInputLagManualUntil = math.max(tonumber(gN.antiInputLagManualUntil) or 0, q + .55)
        return true
    end
    local u = getGrabbedCharacter and getGrabbedCharacter()
    if u and (c and u == c) then
        gN.antiInputLagManualUntil = math.max(tonumber(gN.antiInputLagManualUntil) or 0, q + .7)
        return true
    end
    local M = q < ((tonumber(gN.antiInputInternalHoldUntil) or 0))
    if (not M) and isLocalHoldingToyNow(q) then
        gN.antiInputLagManualUntil = math.max(tonumber(gN.antiInputLagManualUntil) or 0, q + .45)
        return true
    end
    if r and r.SeatPart then return true end
    if isPrimaryMouseHeld() then
        gN.antiInputLagManualUntil = math.max(tonumber(gN.antiInputLagManualUntil) or 0, q + .6)
        return true
    end
    return false
end

function getTeleportAimPosition()
    local q = Y.CurrentCamera
    if not q then return nil end
    local c = q.ViewportSize
    local r = math.floor(c.X * .5)
    local j = math.floor(c.Y * .5)
    local u = q:ViewportPointToRay(r, j, 0)
    local M = RaycastParams.new()
    M.FilterType = Enum.RaycastFilterType.Blacklist
    M.IgnoreWater = false
    if b and b.Character then M.FilterDescendantsInstances = { b.Character } end
    local G = Y:Raycast(u.Origin, u.Direction * 1600, M)
    if G and G.Position then return G.Position end
    local d = b and b:GetMouse()
    local z = d and d.Hit
    if z and z.Position then return z.Position end
    return u.Origin + u.Direction * 140
end

function applyMenuCursorState()
    if phoneModeEnabled then
        O.MouseBehavior = Enum.MouseBehavior.Default
        O.MouseIconEnabled = true
        return
    end
    if gN.cursorUnlock then
        O.MouseBehavior = Enum.MouseBehavior.Default
        O.MouseIconEnabled = true
        return
    end
    if qa then
        O.MouseBehavior = Enum.MouseBehavior.Default
        O.MouseIconEnabled = true
    else
        O.MouseBehavior = Enum.MouseBehavior.LockCenter
        O.MouseIconEnabled = false
    end
end

function processEasterSequence(q) if q == easterSequence[easterIndex] then
        easterIndex = easterIndex + 1
        if easterIndex > #easterSequence then
            easterIndex = 1
            pcall(function() b:Kick("you found easter egg now take a break") end)
        end
    elseif q == easterSequence[1] then easterIndex = 2 else easterIndex = 1 end end

function startMenuCursorSync()
    if menuCursorConnection then
        menuCursorConnection:Disconnect()
        menuCursorConnection = nil
    end
    if menuCursorWatchConnection then
        menuCursorWatchConnection:Disconnect()
        menuCursorWatchConnection = nil
    end
    menuCursorConnection = O.InputBegan:Connect(function(q, c)
        if q.UserInputType ~= Enum.UserInputType.Keyboard then return end
        local r = getMenuKeycode()
        if not c then processEasterSequence(q.KeyCode) end
        if q.KeyCode ~= r then return end
        task.delay(.04, function()
            qa = not qa
            applyMenuCursorState()
        end)
    end)
    menuCursorWatchConnection = z.RenderStepped:Connect(function()
        if C then
            local q = false
            pcall(function() q = O:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) end)
            if not q and (tick() - ((gN.mouse1DownAt or 0))) > .12 then
                C = false
                gN.mouse1DownAt = 0
            end
        end
        if phoneModeEnabled or gN.cursorUnlock or qa then
            if O.MouseBehavior ~= Enum.MouseBehavior.Default then O.MouseBehavior = Enum.MouseBehavior.Default end
            if not O.MouseIconEnabled then O.MouseIconEnabled = true end
        end
    end)
end

function updatePlayerList()
    local q = {}
    local c = {}
    for r, j in ipairs(G:GetPlayers()) do if j ~= b then
            local r = tostring(j.Name or "")
            local u = string.lower(r)
            if r ~= "" and not c[u] then
                c[u] = true
                table.insert(q, r)
            end
        end end
    table.sort(q, function(q, c) return string.lower(q) < string.lower(c) end)
    return q
end

function refreshTargetPlayerDropdown(q)
    local c = updatePlayerList()
    if u and (u.TargetPlayer and u.TargetPlayer.SetValues) then
        pcall(function() u.TargetPlayer:SetValues(c) end)
        if #c > 0 and u.TargetPlayer.SetValue then
            local r = tostring(u.TargetPlayer.Value or "")
            if q or r == "" or not table.find(c, r) then pcall(function() u.TargetPlayer:SetValue(c[1]) end) end
        end
    end
    return c
end

function isPlayerFriendFast(q)
    if not q or not q.UserId or q.UserId <= 0 then return false end
    local c, r = pcall(function() return b:IsFriendsWith(q.UserId) end)
    if c and r then return true end
    c, r = pcall(function() return q:IsFriendsWith(b.UserId) end)
    return c and r or false
end

function rememberKickAttempt(q, c)
    if not q or q == "" then return end
    local r = tick()
    gN.kickAttempts[q] = r
    gN.kickAttempts[string.lower(q)] = r
    if c and c ~= "" then gN.kickAttemptMethods[string.lower(q)] = tostring(c) end
end

function rememberKickedPlayer(q)
    if not q then return end
    local c = tick()
    if typeof(q) == "Instance" and q:IsA("Player") then
        gN.kickedPlayers[string.lower(q.Name)] = c
        gN.kickedPlayers["uid:" .. tostring(q.UserId)] = c
        gN.kickAttempts[q.Name] = c
        return
    end
    local r = tostring(q)
    if r ~= "" then
        gN.kickedPlayers[string.lower(r)] = c
        gN.kickAttempts[r] = c
    end
end

function consumeKickedReturn(q)
    if not q then return false end
    local c = tick()
    local r = string.lower(tostring(q.Name or ""))
    local j = "uid:" .. tostring(q.UserId or 0)
    local u = gN.kickedPlayers[j] or gN.kickedPlayers[r]
    if u and (c - u) <= 1800 then
        gN.kickedPlayers[j] = nil
        gN.kickedPlayers[r] = nil
        clearKickAttempt(q.Name)
        return true
    end
    return false
end

function wasRecentKickAttempt(q, c)
    local r = tonumber(c) or 10
    local j = tick()
    local u = tostring(q or "")
    if u == "" then return false end
    local M = string.lower(u)
    local G = gN.kickAttempts[u] or gN.kickAttempts[M]
    if not G then return false end
    return (j - G) <= r
end

function getKickAttemptAge(q)
    local c = tostring(q or "")
    if c == "" then return math.huge end
    local r = string.lower(c)
    local j = gN.kickAttempts[c] or gN.kickAttempts[r]
    if not j then return math.huge end
    return tick() - j
end

function clearKickAttempt(q)
    local c = tostring(q or "")
    if c == "" then return end
    local r = string.lower(c)
    gN.kickAttempts[c] = nil
    gN.kickAttempts[r] = nil
    gN.kickAttemptMethods[r] = nil
end

function isSelfTargetName(q)
    local c = string.lower(tostring(q or ""))
    local r = string.lower(tostring(b and b.Name or ""))
    return c ~= "" and (r ~= "" and c == r)
end

function notifyOwnKick(q, r)
    if not q or q == "" then return end
    if isSelfTargetName(q) then return end
    local j = string.lower(q)
    local u = tick()
    local M = gN.ownKickNotifyAt[j] or 0
    if u - M < 1.1 then return end
    gN.ownKickNotifyAt[j] = u
    rememberKickAttempt(q, r)
    if gN.targetTrackName and gN.targetTrackName == q then gN.targetTrackPendingReturn = true end
    local d = G:FindFirstChild(q)
    local z = d and ((d.DisplayName or d.Name)) or q
    local Y = d and d.Name or q
    local P = tostring(r or "Kick")
    drawKickLineToTarget(q, .7)
    c:Notify({ Title = "Wourld Hub", Description = z .. (" (" .. (Y .. (") Kick sent [" .. (P .. "]")))), Duration = 3 })
    playEventSound("kick")
end

function notifyLoopState(q, r, j)
    local u = tostring(q or "loop")
    local M = tick()
    gN.loopNotifyAt = gN.loopNotifyAt or {}
    local G = gN.loopNotifyAt[u] or 0
    if M - G < .7 then return end
    gN.loopNotifyAt[u] = M
    c:Notify({ Title = "Wourld Hub", Description = tostring(r or "Loop status"), Duration = tonumber(j) or 2.5 })
end

function markKickLinePulse(q)
    local c = tick()
    gN.kickLinePulseGlobalAt = c
    gN.kickLinePulseAt = gN.kickLinePulseAt or {}
    local r = string.lower(tostring(q or ""))
    if r ~= "" then gN.kickLinePulseAt[r] = c end
end

function getKickLinePulseAge(q)
    local c = tick()
    local r = string.lower(tostring(q or ""))
    local j = gN.kickLinePulseAt and gN.kickLinePulseAt[r]
    local u = gN.kickLinePulseGlobalAt
    local M = j or u
    if not M then return 1000000000.0 end
    return c - M
end

function handleLoopKickBug(q, c)
    local r = tostring(q or "loop")
    local j = tick()
    gN.loopBugState = gN.loopBugState or {}
    local u = gN.loopBugState[r]
    if not u or (j - ((u.windowStart or 0))) > 24 then
        u = { windowStart = j, count = 0, lastResetAt = 0, lastLeaveAt = 0 }
        gN.loopBugState[r] = u
    end
    u.count = ((u.count or 0)) + 1
    if u.count >= 3 and (j - ((u.lastResetAt or 0))) > 3.2 then
        u.lastResetAt = j
        requestAutoRespawn("loop kick bug", .05)
    end
    if u.count >= 4 and (E6 and (gN.loopBugAutoLeave and (m6 and (j - ((u.lastLeaveAt or 0))) > 10))) then
        u.lastLeaveAt = j
        notifyLoopState("loopbugleave:" .. r, "Loop bug escalation: auto leaving server", 3)
        pcall(function()
            local q = game:GetService("TeleportService")
            q:TeleportToPlaceInstance(game.PlaceId, game.JobId, b)
        end)
        task.delay(.8,
            function() if E6 then pcall(function()
                        local q = game:GetService("TeleportService")
                        q:Teleport(game.PlaceId, b)
                    end) end end)
        task.delay(1.8, function() if E6 then pcall(function() b:Kick("Auto leave: loop kick bug") end) end end)
    else notifyLoopState("loopbugcount:" .. r,
            "Loop bug detected for " .. (tostring(c or "target") .. (" (" .. (tostring(u.count) .. ")"))), 2.6) end
end

function releaseGucciGrabState(q, r)
    gucciRunId = gucciRunId + 1
    gN.gucciKeyActive = false
    C = false
    gN.mouse1DownAt = 0
    n = tick() + .22
    local j = b.Character
    local u = j and j:FindFirstChildOfClass("Humanoid")
    local M = j and j:FindFirstChild("HumanoidRootPart")
    if u then
        u.Sit = false
        u.PlatformStand = false
        u.AutoRotate = true
        pcall(function() u:ChangeState(Enum.HumanoidStateType.Running) end)
    end
    if M then
        M.AssemblyLinearVelocity = Vector3.new(M.AssemblyLinearVelocity.X * .2, math.max(M.AssemblyLinearVelocity.Y, 0),
            M.AssemblyLinearVelocity.Z * .2)
        M.AssemblyAngularVelocity = Vector3.zero
    end
    local G = d:FindFirstChild("CharacterEvents")
    local z = G and G:FindFirstChild("Struggle")
    if z then pcall(function() z:FireServer(b) end) end
    if not r then c:Notify({ Title = "Wourld Hub", Description = tostring(q or "Gucci mode released"), Duration = 2.5 }) end
end

function IsPlayerInHouse(q)
    if not q then return false end
    local c = q:FindFirstChild("InPlot")
    if c and (c:IsA("BoolValue") and c.Value) then return true end
    local r = Y:FindFirstChild("PlotItems")
    local j = r and r:FindFirstChild("PlayersInPlots")
    if j and j:FindFirstChild(q.Name) then return true end
    return false
end

function shouldPauseLocalAntiKickInHouse(q)
    if not q then return false end
    local c = q:FindFirstChild("InPlot")
    local r = q:FindFirstChild("InOwnedPlot")
    if c and (c:IsA("BoolValue") and (r and r:IsA("BoolValue"))) then return c.Value and (not r.Value) end
    return false
end

function GetHouseBlockedMessage() return "Player is inside house" end

function getPlotNameList()
    local q = {}
    local c = Y:FindFirstChild("Plots")
    if c then for c, r in ipairs(c:GetChildren()) do q[#q + 1] = r.Name end end
    if #q == 0 then q = { "Plot1", "Plot2", "Plot3", "Plot4", "Plot5" } end
    table.sort(q)
    return q
end

function isHouseBarrierBroken(q)
    local c = Y:FindFirstChild("Plots")
    local r = c and c:FindFirstChild(tostring(q or ""))
    local j = r and r:FindFirstChild("Barrier")
    if not j then return true end
    local u = false
    for q, c in ipairs(j:GetDescendants()) do if c:IsA("BasePart") and c.Name == "PlotBarrier" then
            u = true
            if c.CanCollide and c.Transparency < .98 then return false end
        end end
    return not u or true
end

function runHouseRavage(q)
    local r = tostring(q or "")
    if r == "" then return false end
    if not isHouseBarrierBroken(r) then
        c:Notify({ Title = "Wourld Hub", Description = "House barrier is not broken", Duration = 2.6 })
        return false
    end
    local j = Y:FindFirstChild("PlotItems")
    local u = j and j:FindFirstChild(r)
    if not u then
        c:Notify({ Title = "Wourld Hub", Description = "House folder not found: " .. r, Duration = 2.6 })
        return false
    end
    local M = d:FindFirstChild("GrabEvents") and d.GrabEvents:FindFirstChild("SetNetworkOwner")
    local G = spawnOwnedToyByName("NinjaShuriken", CFrame.new(0, 8, 14))
    if G then pcall(function() G.Name = "HouseRavageClone" end) end
    local z = math.clamp(tonumber(o6) or 220, 80, 900)
    local P = 0
    for q, c in ipairs(u:GetDescendants()) do if c:IsA("BasePart") and not c.Anchored then
            if M then pcall(function() M:FireServer(c, c.CFrame) end) end
            pcall(function()
                c.AssemblyLinearVelocity = Vector3.new(math.random(-z, z), math.floor(z * .9), math.random(-z, z))
                c.AssemblyAngularVelocity = Vector3.new(math.random(-45, 45), math.random(-80, 80), math.random(-45, 45))
            end)
            P = P + 1
        end end
    if G and G.Parent then task.delay(.5,
            function()
                local q, c = getMenuToyRemotes()
                if c then pcall(function() c:FireServer(G) end) end
                pcall(function() G:Destroy() end)
            end) end
    c:Notify({ Title = "Wourld Hub", Description = "House ravage done: " .. (tostring(r) .. (" | parts: " .. tostring(P))), Duration = 3 })
    return P > 0
end

function IsLocalAdminUser() return false end

function deleteAllPaintParts() for q, c in ipairs(Y:GetDescendants()) do if c:IsA("BasePart") and c.Name == "PaintPlayerPart" then
            local q = c:Clone()
            q.Archivable = true
            g[c:GetDebugId()] = { clone = q, parent = c.Parent }
            c:Destroy()
        end end end

function restorePaintParts()
    for q, c in pairs(g) do if c.clone and c.parent then c.clone.Parent = c.parent end end
    g = {}
end

function watchNewPaintParts() table.insert(A,
        Y.DescendantAdded:Connect(function(q) if q:IsA("BasePart") and q.Name == "PaintPlayerPart" then task.defer(function() if q and q.Parent then
                        local c = q:Clone()
                        c.Archivable = true
                        g[q:GetDebugId()] = { clone = c, parent = q.Parent }
                        q:Destroy()
                    end end) end end)) end

function disconnectWatchers()
    for q, c in ipairs(A) do if c.Connected then c:Disconnect() end end
    A = {}
end

function setTouchQuery(q)
    local c = Y:FindFirstChild(b.Name)
    if not c then return end
    for c, r in ipairs(c:GetChildren()) do if r:IsA("Part") or r:IsA("BasePart") then
            r.CanTouch = q
            r.CanQuery = q
        end end
end

function FWC(q, c) return q:WaitForChild(c) end

function Disc(q) if E[q] then
        E[q]:Disconnect()
        E[q] = nil
    end end

function toy_spawn(q, c, r)
    local j = { q, c, r }
    d.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(j))
    local u = Y:WaitForChild(b.Name .. "SpawnedInToys", 5)
    if u then return u:WaitForChild(q, 5) end
    return nil
end

function grab(q) if q and (q.Parent and q.CFrame) then d.CharacterEvents.Grab:FireServer(q) end end

function getClosestPlayer(q)
    if not q then return nil end
    local c = nil
    local r = math.huge
    for j, u in pairs(game.Players:GetPlayers()) do if u ~= b and u.Character then
            local j = u.Character:FindFirstChild("HumanoidRootPart")
            if j then
                local M = ((j.Position - q)).Magnitude
                if M < r then
                    r = M
                    c = u
                end
            end
        end end
    return c
end

function GetSizeMB(q) return q / (1048576) end

function shouldIgnorePacketLagNotification(q, c)
    if type(c) == "string" then if c:find("WourldLagPayload", 1, true) or c:find(b.Name, 1, true) then return true end end
    if q == b then return true end
    if typeof(q) == "string" and q:lower() == b.Name:lower() then return true end
    if typeof(q) == "Instance" then
        if q == b then return true end
        if q:IsA("Player") and q.UserId == b.UserId then return true end
        if isLocalOwnedInstance(q) then return true end
        local c = q:FindFirstChild("PartOwner", true)
        if c and (c:IsA("StringValue") and c.Value == b.Name) then return true end
    end
    return false
end

function StartPacketLagDetector()
    if packetLagDetectorStarted and (packetLagConnection and packetLagConnection.Connected) then return end
    packetLagDetectorStarted = true
    local q = game:GetService("ReplicatedStorage")
    if packetLagConnection then
        packetLagConnection:Disconnect()
        packetLagConnection = nil
    end
    packetLagConnection = q.GrabEvents.ExtendGrabLine.OnClientEvent:Connect(function(q, r) if typeof(r) == "string" and (not lastLagSource and packetLagNotifyEnabled) then
            if shouldIgnorePacketLagNotification(q, r) then return end
            lastLagSource = true
            local j = string.len(r)
            if j > 420 then
                local r = math.round(GetSizeMB(j) * 1000) / 1000
                playEventSound("packet")
                c:Notify({ Title = "Wourld Hub", Description = "PACKET LAG DETECTED\nSource: " ..
                ((tostring(q)):sub(1, 20) .. ("\nSize: " .. (tostring(r) .. " MB"))), Duration = 5 })
            end
            task.delay(5, function() lastLagSource = false end)
        end end)
end

function setWaterWalk(q)
    local c = workspace:FindFirstChild("Map")
    if c then
        local r = c:FindFirstChild("AlwaysHereTweenedObjects")
        if r then
            local c = r:FindFirstChild("Ocean")
            if c then
                local r = c:FindFirstChild("Object")
                if r then
                    local c = r:FindFirstChild("ObjectModel")
                    if c then for c, r in pairs(c:GetChildren()) do if r:IsA("BasePart") then r.CanCollide = q end end end
                end
            end
        end
    end
end

function IsTarget(q)
    if not q or not q:IsA("BasePart") then return false end
    local function c(q)
        local c = string.lower(tostring(q or ""))
        if c == "" then return false end
        for q, r in ipairs(lN) do
            local j = string.lower(tostring(r or ""))
            if j ~= "" and ((c == j or c:find(j, 1, true))) then return true end
        end
        if c:find("playercharacterlocationdetector", 1, true) then return true end
        if c:find("characterlocationdetector", 1, true) then return true end
        if c:find("partesp", 1, true) then return true end
        if c:find("pcld", 1, true) then return true end
        return false
    end
    if c(q.Name) then return true end
    local r = q.Parent
    local j = 0
    while r and j < 3 do
        if c(r.Name) then return true end
        r = r.Parent
        j = j + 1
    end
    return false
end

function AddBoxESP(q)
    if BN[q] then
        BN[q].Color3 = FN
        BN[q].Adornee = q
        if q:IsA("BasePart") then BN[q].Size = q.Size end
        return
    end
    local c = Instance.new("BoxHandleAdornment")
    c.Adornee = q
    c.AlwaysOnTop = true
    c.ZIndex = 5
    c.Color3 = FN
    c.Transparency = .5
    c.Size = q.Size
    c.Parent = game.CoreGui
    BN[q] = c
    q.AncestryChanged:Connect(function(c, r) if not r and BN[q] then
            BN[q]:Destroy()
            BN[q] = nil
        end end)
end

function RemoveAllBoxes()
    for q, c in pairs(BN) do if c then c:Destroy() end end
    BN = {}
end

function CleanupPCLDBoxes() for q, c in pairs(BN) do if (not tN) or (not q) or (not q.Parent) or (not IsTarget(q)) then
            if c then c:Destroy() end
            BN[q] = nil
        else
            c.Color3 = FN
            c.Adornee = q
            if q:IsA("BasePart") then c.Size = q.Size end
        end end end

function ScanPCLD()
    for q, c in ipairs(workspace:GetDescendants()) do if tN and IsTarget(c) then AddBoxESP(c) end end
    CleanupPCLDBoxes()
end

function ResetPCLDScanState()
    gN.pcldScanSnapshot = nil
    gN.pcldScanIndex = 1
    gN.pcldScanRefreshAt = 0
    gN.pcldScanDone = false
end

function ScanPCLDChunk()
    if not tN then return end
    if gN.pcldScanDone then return end
    local q = tick()
    if (not gN.pcldScanSnapshot) or q >= ((gN.pcldScanRefreshAt or 0)) then
        gN.pcldScanSnapshot = workspace:GetDescendants()
        gN.pcldScanIndex = 1
        gN.pcldScanRefreshAt = math.huge
    end
    local c = gN.pcldScanSnapshot
    local r = tonumber(gN.pcldScanIndex) or 1
    local j = 0
    local u = 140
    while c and (r <= #c and j < u) do
        local q = c[r]
        if q and IsTarget(q) then AddBoxESP(q) end
        r = r + 1
        j = j + 1
    end
    gN.pcldScanIndex = r
    if c and r > #c then
        gN.pcldScanSnapshot = nil
        gN.pcldScanIndex = 1
        gN.pcldScanDone = true
    end
end

function StartPCLDRefreshLoop()
    local q = 0
    while tN do
        pcall(function() ScanPCLDChunk() end)
        if tick() - q >= .7 then
            q = tick()
            pcall(function() CleanupPCLDBoxes() end)
        end
        task.wait(.09)
    end
    CleanupPCLDBoxes()
    ResetPCLDScanState()
end

function findBillboardTargetPart(q)
    if not q then return nil end
    if q:IsA("BasePart") then return q end
    if q:IsA("Model") then
        if q.PrimaryPart then return q.PrimaryPart end
        local c = q:FindFirstChild("HumanoidRootPart")
        if c and c:IsA("BasePart") then return c end
    end
    return q:FindFirstChildWhichIsA("BasePart", true)
end

function destroyGuiObject(q) if q and q.Destroy then pcall(function() q:Destroy() end) end end

function getPlayerDisplayLabel(q)
    if wN.showDisplayName and (q.DisplayName and q.DisplayName ~= q.Name) then return tostring(q.DisplayName) ..
        (" @" .. tostring(q.Name)) end
    return tostring(q.Name)
end

function ensureUsernameBillboard(q)
    local c = wN.usernameBillboards[q]
    local r = nil
    local j = q.Character
    if j then r = j:FindFirstChild("Head") or j:FindFirstChild("HumanoidRootPart") end
    if not r or not r:IsA("BasePart") then
        if c then
            destroyGuiObject(c.Gui)
            wN.usernameBillboards[q] = nil
        end
        return nil
    end
    if not c or not c.Gui or not c.Gui.Parent then
        local r = Instance.new("BillboardGui")
        r.Name = "WourldUsernameESP"
        r.AlwaysOnTop = true
        r.MaxDistance = 1500
        r.Size = UDim2.new(0, 220, 0, 54)
        r.StudsOffset = Vector3.new(0, 2.9, 0)
        r.Parent = game.CoreGui
        local j = Instance.new("TextLabel")
        j.Name = "NameLabel"
        j.BackgroundTransparency = 1
        j.BorderSizePixel = 0
        j.Size = UDim2.fromScale(1, 1)
        j.Font = Enum.Font.GothamBold
        j.TextScaled = true
        j.TextStrokeTransparency = .35
        j.TextXAlignment = Enum.TextXAlignment.Center
        j.TextYAlignment = Enum.TextYAlignment.Center
        j.Parent = r
        c = { Gui = r, Label = j }
        wN.usernameBillboards[q] = c
    end
    c.Gui.Adornee = r
    c.Gui.Enabled = true
    return c
end

function updateUsernameEsp()
    if not wN.usernameEnabled then return end
    local q = b.Character and b.Character:FindFirstChild("HumanoidRootPart")
    local c = {}
    for r, j in ipairs(G:GetPlayers()) do if j ~= b then
            c[j] = true
            local r = ensureUsernameBillboard(j)
            if r and r.Label then
                local c = j.Character and j.Character:FindFirstChild("HumanoidRootPart")
                local u = j.Character and j.Character:FindFirstChildOfClass("Humanoid")
                local M = getPlayerDisplayLabel(j)
                if wN.showDistance and (q and c) then M = M ..
                    (" | " .. (tostring(math.floor(((c.Position - q.Position)).Magnitude)) .. "m")) end
                if wN.showHealth and u then M = M .. (" | HP " .. tostring(math.max(0, math.floor(u.Health)))) end
                r.Label.Text = M
                r.Label.TextColor3 = wN.usernameColor
            end
        end end
    for q, r in pairs(wN.usernameBillboards) do if not c[q] then
            destroyGuiObject(r.Gui)
            wN.usernameBillboards[q] = nil
        end end
end

function clearUsernameEsp() for q, c in pairs(wN.usernameBillboards) do
        destroyGuiObject(c.Gui)
        wN.usernameBillboards[q] = nil
    end end

function setUsernameEspState(q)
    wN.usernameEnabled = q and true or false
    if wN.usernameEnabled then if not wN.usernameTask then wN.usernameTask = task.spawn(function()
                while wN.usernameEnabled do
                    pcall(updateUsernameEsp)
                    task.wait(.12)
                end
                clearUsernameEsp()
            end) end else
        if wN.usernameTask then
            task.cancel(wN.usernameTask)
            wN.usernameTask = nil
        end
        clearUsernameEsp()
    end
end

function applyHitboxToPlayer(q)
    local c = q.Character
    local r = c and c:FindFirstChild("HumanoidRootPart")
    if not r or not r:IsA("BasePart") then
        local c = wN.hitboxData[q]
        if c then wN.hitboxData[q] = nil end
        return
    end
    local j = wN.hitboxData[q]
    if not j or j.Part ~= r then
        if j and (j.Part and j.Part.Parent) then pcall(function()
                j.Part.Size = j.OriginalSize
                j.Part.Transparency = j.OriginalTransparency
                j.Part.LocalTransparencyModifier = j.OriginalLocalTransparency
                j.Part.Color = j.OriginalColor
                j.Part.Material = j.OriginalMaterial
                j.Part.CanCollide = j.OriginalCanCollide
                j.Part.Massless = j.OriginalMassless
            end) end
        j = { Part = r, OriginalSize = r.Size, OriginalTransparency = r.Transparency, OriginalLocalTransparency = r
        .LocalTransparencyModifier, OriginalColor = r.Color, OriginalMaterial = r.Material, OriginalCanCollide = r
        .CanCollide, OriginalMassless = r.Massless }
        wN.hitboxData[q] = j
    end
    local u = math.max(.5, wN.hitboxScale)
    local M = Vector3.new(2.2, 3.2, 1.6) * u
    pcall(function()
        r.Size = M
        r.Transparency = math.clamp(wN.hitboxTransparency, 0, .95)
        r.LocalTransparencyModifier = math.clamp(wN.hitboxTransparency, 0, .95)
        r.Color = wN.hitboxColor
        r.Material = Enum.Material.Neon
        r.CanCollide = false
        r.Massless = true
    end)
end

function restoreHitboxForPlayer(q)
    local c = wN.hitboxData[q]
    if not c then return end
    wN.hitboxData[q] = nil
    if c.Part and c.Part.Parent then pcall(function()
            c.Part.Size = c.OriginalSize
            c.Part.Transparency = c.OriginalTransparency
            c.Part.LocalTransparencyModifier = c.OriginalLocalTransparency
            c.Part.Color = c.OriginalColor
            c.Part.Material = c.OriginalMaterial
            c.Part.CanCollide = c.OriginalCanCollide
            c.Part.Massless = c.OriginalMassless
        end) end
end

function clearHitboxEsp() for q in pairs(wN.hitboxData) do restoreHitboxForPlayer(q) end end

function updateHitboxEsp()
    if not wN.hitboxEnabled then return end
    local q = {}
    for c, r in ipairs(G:GetPlayers()) do if r ~= b then
            q[r] = true
            applyHitboxToPlayer(r)
        end end
    for c in pairs(wN.hitboxData) do if not q[c] then restoreHitboxForPlayer(c) end end
end

function setHitboxEspState(q)
    wN.hitboxEnabled = q and true or false
    if wN.hitboxEnabled then if not wN.hitboxTask then wN.hitboxTask = task.spawn(function()
                while wN.hitboxEnabled do
                    pcall(updateHitboxEsp)
                    task.wait(.16)
                end
                clearHitboxEsp()
            end) end else
        if wN.hitboxTask then
            task.cancel(wN.hitboxTask)
            wN.hitboxTask = nil
        end
        clearHitboxEsp()
    end
end

function clearBlizHighlights() for q, c in pairs(wN.blizHighlights) do
        destroyGuiObject(c)
        wN.blizHighlights[q] = nil
    end end

function updateBlizHighlights()
    if not wN.blizHighlightEnabled then return end
    local q = {}
    for c, r in ipairs(G:GetPlayers()) do if r ~= b then
            local c = r.Character
            local j = c and c:FindFirstChild("Head")
            if j then
                q[r] = true
                local j = wN.blizHighlights[r]
                if not j or not j.Parent then
                    j = Instance.new("Highlight")
                    j.Name = "WourldBlizHighlight"
                    j.Parent = c
                    wN.blizHighlights[r] = j
                end
                if j.Parent ~= c then j.Parent = c end
                j.Enabled = true
                j.FillColor = wN.blizHighlightFillColor
                j.OutlineColor = wN.blizHighlightOutlineColor
                j.FillTransparency = wN.blizHighlightFillTransparency
                j.OutlineTransparency = wN.blizHighlightOutlineTransparency
                j.DepthMode = wN.blizHighlightMode
            end
        end end
    for c, r in pairs(wN.blizHighlights) do if not q[c] then
            destroyGuiObject(r)
            wN.blizHighlights[c] = nil
        end end
end

function setBlizHighlightState(q)
    wN.blizHighlightEnabled = q and true or false
    if wN.blizHighlightEnabled then if not wN.blizHighlightTask then wN.blizHighlightTask = task.spawn(function()
                while wN.blizHighlightEnabled do
                    pcall(updateBlizHighlights)
                    task.wait(.16)
                end
                clearBlizHighlights()
            end) end else
        if wN.blizHighlightTask then
            task.cancel(wN.blizHighlightTask)
            wN.blizHighlightTask = nil
        end
        clearBlizHighlights()
    end
end

function createBlizIconTemplate()
    local q = Instance.new("BillboardGui")
    local c = Instance.new("ImageButton")
    local r = Instance.new("UICorner")
    local j = Instance.new("TextLabel")
    local u = Instance.new("UITextSizeConstraint")
    local M = Instance.new("UIAspectRatioConstraint")
    q.Name = "WourldBlizIconESP"
    q.Parent = nil
    q.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    q.Active = true
    q.Adornee = nil
    q.AlwaysOnTop = true
    q.ExtentsOffset = Vector3.new(0, 10, 0)
    q.Size = UDim2.new(3, 50, 3, 45)
    c.Name = "UserImage"
    c.Parent = q
    c.AnchorPoint = Vector2.new(.5, .5)
    c.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    c.BackgroundTransparency = 1
    c.BorderSizePixel = 0
    c.Position = UDim2.new(.5, 0, .300000012, 0)
    c.Size = UDim2.new(.5, 5, .5, 5)
    c.Image = ""
    r.CornerRadius = UDim.new(2, 0)
    r.Parent = c
    j.Name = "Username"
    j.Parent = q
    j.AnchorPoint = Vector2.new(.5, .5)
    j.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    j.BackgroundTransparency = 1
    j.BorderSizePixel = 0
    j.Position = UDim2.new(.5, 0, .75999999, 0)
    j.Size = UDim2.new(1, 5, .340000004, 5)
    j.Font = Enum.Font.SourceSans
    j.Text = ""
    j.TextColor3 = Color3.fromRGB(255, 255, 255)
    j.TextScaled = true
    j.TextSize = 35
    j.TextStrokeTransparency = 0
    j.TextWrapped = true
    u.Parent = j
    u.MaxTextSize = 35
    u.MinTextSize = 15
    M.Parent = q
    M.AspectRatio = 1.043
    return q
end

function clearBlizIcons() for q, c in pairs(wN.blizIcons) do
        destroyGuiObject(c.Gui)
        wN.blizIcons[q] = nil
    end end

function ensureBlizIcon(q)
    local c = wN.blizIcons[q]
    local r = q.Character
    local j = r and r:FindFirstChild("Head")
    if not j then
        if c then
            destroyGuiObject(c.Gui)
            wN.blizIcons[q] = nil
        end
        return nil
    end
    if not c or not c.Gui or not c.Gui.Parent then
        if not blizIconTemplate then blizIconTemplate = createBlizIconTemplate() end
        c = { Gui = blizIconTemplate:Clone() }
        c.Gui.Parent = game.CoreGui
        wN.blizIcons[q] = c
    end
    c.Gui.Adornee = j
    c.Gui.Username.Text = q.Name
    c.Gui.UserImage.Image = "https://www.roblox.com/headshot-thumbnail/image?userId=" ..
    (tostring(q.UserId) .. "&width=420&height=420&format=png")
    local u = math.clamp(tonumber(wN.blizIconScale) or 1, .6, 2.2)
    c.Gui.ExtentsOffset = Vector3.new(0, tonumber(wN.blizIconYOffset) or 10, 0)
    c.Gui.Size = UDim2.new(3 * u, 50 * u, 3 * u, 45 * u)
    return c
end

function updateBlizIcons()
    if not wN.blizIconEnabled then return end
    local q = {}
    for c, r in ipairs(G:GetPlayers()) do if r ~= b then
            local c = ensureBlizIcon(r)
            if c then q[r] = true end
        end end
    for c, r in pairs(wN.blizIcons) do if not q[c] then
            destroyGuiObject(r.Gui)
            wN.blizIcons[c] = nil
        end end
end

function setBlizIconState(q)
    wN.blizIconEnabled = q and true or false
    if wN.blizIconEnabled then if not wN.blizIconTask then wN.blizIconTask = task.spawn(function()
                while wN.blizIconEnabled do
                    pcall(updateBlizIcons)
                    task.wait(.2)
                end
                clearBlizIcons()
            end) end else
        if wN.blizIconTask then
            task.cancel(wN.blizIconTask)
            wN.blizIconTask = nil
        end
        clearBlizIcons()
    end
end

function findAntiKickToyInFolder(q)
    if not q then return nil end
    local c = q:FindFirstChild("AntiKick")
    if c then return c end
    local r = q:FindFirstChild("NinjaShuriken")
    if r and r:FindFirstChild("StickyPart") then
        local q = r.StickyPart
        if q.CanTouch == false or q:FindFirstChild("StickyWeld") then return r end
    end
    return nil
end

function ensureAntiKickSelfVisual()
    local q = game.CoreGui:FindFirstChild("WourldSelfAntiKickTag")
    if q then destroyGuiObject(q) end
    if not wN.selfAntiKickVisualEnabled or not _G.ShurikenAntiKick then
        if wN.selfAntiKickAdornment then
            destroyGuiObject(wN.selfAntiKickAdornment)
            wN.selfAntiKickAdornment = nil
        end
        if wN.selfAntiKickBillboard then
            destroyGuiObject(wN.selfAntiKickBillboard)
            wN.selfAntiKickBillboard = nil
        end
        return
    end
    local c = Y:FindFirstChild(b.Name .. "SpawnedInToys")
    local r = findAntiKickToyInFolder(c)
    local j = r and ((r:FindFirstChild("StickyPart") or findBillboardTargetPart(r)))
    if not j or not j:IsA("BasePart") then
        if wN.selfAntiKickAdornment then
            destroyGuiObject(wN.selfAntiKickAdornment)
            wN.selfAntiKickAdornment = nil
        end
        if wN.selfAntiKickBillboard then
            destroyGuiObject(wN.selfAntiKickBillboard)
            wN.selfAntiKickBillboard = nil
        end
        return
    end
    if not wN.selfAntiKickAdornment or not wN.selfAntiKickAdornment.Parent then
        local q = Instance.new("Highlight")
        q.Name = "WourldSelfAntiKickBox"
        q.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        q.FillColor = Color3.fromRGB(55, 190, 255)
        q.FillTransparency = .62
        q.OutlineColor = Color3.fromRGB(255, 255, 255)
        q.OutlineTransparency = .08
        q.Parent = game.CoreGui
        wN.selfAntiKickAdornment = q
    end
    wN.selfAntiKickAdornment.Adornee = j
    if wN.selfAntiKickBillboard then
        destroyGuiObject(wN.selfAntiKickBillboard)
        wN.selfAntiKickBillboard = nil
    end
end

function setSelfAntiKickVisualState(q)
    wN.selfAntiKickVisualEnabled = q and true or false
    if wN.selfAntiKickVisualEnabled and _G.ShurikenAntiKick then if not wN.selfAntiKickTask then wN.selfAntiKickTask =
            task.spawn(function()
                while _G.ShurikenAntiKick and wN.selfAntiKickVisualEnabled do
                    pcall(ensureAntiKickSelfVisual)
                    task.wait(.12)
                end
                ensureAntiKickSelfVisual()
            end) end else
        if wN.selfAntiKickTask then
            task.cancel(wN.selfAntiKickTask)
            wN.selfAntiKickTask = nil
        end
        ensureAntiKickSelfVisual()
    end
end

function ensureAntiKickOwnerBillboard(q, c)
    local r = wN.antiKickOwnerBillboards[q]
    local j = c and ((c:FindFirstChild("StickyPart") or findBillboardTargetPart(c)))
    if not j or not j:IsA("BasePart") then
        if r then
            destroyGuiObject(r.Gui)
            wN.antiKickOwnerBillboards[q] = nil
        end
        return
    end
    if not r or not r.Gui or not r.Gui.Parent then
        local c = Instance.new("BillboardGui")
        c.Name = "WourldAntiKickOwnerESP"
        c.AlwaysOnTop = true
        c.MaxDistance = 1500
        c.Size = UDim2.new(0, 250, 0, 46)
        c.StudsOffset = Vector3.new(0, 2.4, 0)
        c.Parent = game.CoreGui
        local j = Instance.new("TextLabel")
        j.BackgroundTransparency = 1
        j.Size = UDim2.fromScale(1, 1)
        j.Font = Enum.Font.GothamBold
        j.TextScaled = true
        j.TextStrokeTransparency = .25
        j.Parent = c
        r = { Gui = c, Label = j }
        wN.antiKickOwnerBillboards[q] = r
    end
    r.Gui.Adornee = j
    r.Label.TextColor3 = wN.antiKickOwnerColor
    r.Label.Text = "ANTI KICK | " .. getPlayerDisplayLabel(q)
end

function clearAntiKickOwnerEsp()
    for q, c in pairs(wN.antiKickOwnerBillboards) do
        destroyGuiObject(c.Gui)
        wN.antiKickOwnerBillboards[q] = nil
    end
    wN.antiKickOwnerKnown = {}
end

function updateAntiKickOwnerEsp()
    if not wN.antiKickOwnerEnabled then return end
    local q = {}
    for r, j in ipairs(G:GetPlayers()) do if j ~= b then
            local r = Y:FindFirstChild(j.Name .. "SpawnedInToys")
            local u = findAntiKickToyInFolder(r)
            if u then
                q[j] = true
                ensureAntiKickOwnerBillboard(j, u)
                if wN.antiKickOwnerNotify and not wN.antiKickOwnerKnown[j.UserId] then
                    wN.antiKickOwnerKnown[j.UserId] = true
                    c:Notify({ Title = "Wourld Hub", Description = tostring(j.Name) .. " enabled anti kick", Duration = 3 })
                end
            else wN.antiKickOwnerKnown[j.UserId] = nil end
        else wN.antiKickOwnerKnown[j.UserId] = nil end end
    for c, r in pairs(wN.antiKickOwnerBillboards) do if not q[c] then
            destroyGuiObject(r.Gui)
            wN.antiKickOwnerBillboards[c] = nil
        end end
end

function setAntiKickOwnerEspState(q)
    wN.antiKickOwnerEnabled = q and true or false
    if wN.antiKickOwnerTask then
        task.cancel(wN.antiKickOwnerTask)
        wN.antiKickOwnerTask = nil
    end
    clearAntiKickOwnerEsp()
    if wN.antiKickOwnerEnabled then wN.antiKickOwnerTask = task.spawn(function()
            while wN.antiKickOwnerEnabled do
                pcall(updateAntiKickOwnerEsp)
                task.wait(.35)
            end
            clearAntiKickOwnerEsp()
            wN.antiKickOwnerTask = nil
        end) end
end

function SmartDisconnectBucket(q)
    for q, c in pairs(q) do if c and c.Disconnect then pcall(function() c:Disconnect() end) end end
    table.clear(q)
end

function GetLocalCharacterData()
    local q = b.Character
    if not q then return nil, nil, nil end
    local c = q:FindFirstChildOfClass("Humanoid")
    local r = q:FindFirstChild("HumanoidRootPart")
    return q, c, r
end

function RememberActionPoint()
    local q, c, r = GetLocalCharacterData()
    if c and (r and c.Health > 0) then gN.actionCFrame = r.CFrame end
end

function ReturnAfterActionPoint()
    if not gN.returnAfterAction then return end
    local q = gN.actionCFrame
    if not q then return end
    local c = math.max(.05, ((gN.returnDelay or 45)) / 100)
    task.delay(c,
        function()
            local c, r, j = GetLocalCharacterData()
            if r and (j and r.Health > 0) then pcall(function()
                    j.CFrame = q
                    j.AssemblyLinearVelocity = Vector3.zero
                    j.AssemblyAngularVelocity = Vector3.zero
                end) end
        end)
end

function TrackTargetHistory(q)
    if not q or q == "" then return end
    local c = gN.targetHistory
    if c[#c] ~= q then
        table.insert(c, q)
        if #c > 35 then table.remove(c, 1) end
    end
    gN.targetHistoryIndex = #c
end

function DisconnectTargetTrack() SmartDisconnectBucket(gN.targetTrackCons) end

function BindTargetHumanoidDied(q, r)
    if not q or not r then return end
    local j = r:FindFirstChildOfClass("Humanoid")
    if not j then return end
    local u = j.Died:Connect(function() if gN.targetTrackName == q.Name then c:Notify({ Title = "Wourld Hub", Description =
            q.Name .. " downed", Duration = 2 }) end end)
    table.insert(gN.targetTrackCons, u)
end

function SetTrackedTarget(q)
    local r = gN.targetTrackPendingReturn and gN.targetTrackName == q
    gN.targetTrackName = q
    gN.targetTrackPendingReturn = r
    DisconnectTargetTrack()
    if not q or q == "" then return end
    TrackTargetHistory(q)
    restartTargetBoundActions()
    local j = G:FindFirstChild(q)
    if not j then return end
    local u = j.CharacterAdded:Connect(function(q)
        task.wait(.2)
        if gN.targetTrackName == j.Name then
            if gN.targetTrackPendingReturn then c:Notify({ Title = "Wourld Hub", Description = j.Name .. " returned", Duration = 3 }) end
            gN.targetTrackPendingReturn = false
            BindTargetHumanoidDied(j, q)
        end
    end)
    table.insert(gN.targetTrackCons, u)
    if j.Character then BindTargetHumanoidDied(j, j.Character) end
end

function IsPlayerThreat(q)
    if not q or q == b then return false end
    local c = q.Character
    if not c then return false end
    local r = c:FindFirstChildOfClass("Tool") or q.Backpack:FindFirstChildOfClass("Tool")
    if r then return true end
    local j = c:FindFirstChild("HumanoidRootPart")
    if j and j.AssemblyLinearVelocity.Magnitude > 55 then return true end
    return false
end

function ShouldShowByFocusMode(q, c)
    local r = gN.focusMode or "All"
    if r == "Target Only" then return q and q.Name == ((u.TargetPlayer and u.TargetPlayer.Value)) end
    if r == "Threat Only" then return c end
    return true
end

function ScoreTargetPlayer(q)
    local c, r, j = GetLocalCharacterData()
    if not c or not j or not q or q == b then return -math.huge end
    local u = q.Character
    local M = u and u:FindFirstChildOfClass("Humanoid")
    local G = u and u:FindFirstChild("HumanoidRootPart")
    if not M or M.Health <= 0 or not G then return -math.huge end
    local d = ((G.Position - j.Position)).Magnitude
    local z = 0
    z = z + math.max(0, 200 - d)
    local Y = ((j.Position - G.Position)).Unit
    local P = G.CFrame.LookVector:Dot(Y)
    z = z + math.max(0, P) * 80
    if IsPlayerThreat(q) then z = z + 70 end
    return z
end

function SelectSmartTarget()
    local q = nil
    local c = -math.huge
    for r, j in ipairs(G:GetPlayers()) do
        local u = ScoreTargetPlayer(j)
        if u > c then
            c = u
            q = j
        end
    end
    if q and u.TargetPlayer then
        u.TargetPlayer:SetValue(q.Name)
        SetTrackedTarget(q.Name)
        return q.Name
    end
    return nil
end

function TargetInvalid(q)
    if not q or q == "" then return true end
    local c = G:FindFirstChild(q)
    local r, j, u = GetLocalCharacterData()
    if not c or not c.Character then return true end
    local M = c.Character:FindFirstChildOfClass("Humanoid")
    local d = c.Character:FindFirstChild("HumanoidRootPart")
    if not M or M.Health <= 0 or not d then return true end
    if u and ((d.Position - u.Position)).Magnitude > 260 then return true end
    return false
end

function KeepDistanceToTarget(q)
    if not gN.distanceLock then return end
    local c = G:FindFirstChild(q or "")
    local r, j, u = GetLocalCharacterData()
    if not c or not u or not j or j.Health <= 0 then return end
    local M = c.Character and c.Character:FindFirstChild("HumanoidRootPart")
    if not M then return end
    local d = math.max(6, gN.distanceRange or 16)
    local z = math.max(1, gN.distanceTolerance or 4)
    local Y = ((u.Position - M.Position)).Magnitude
    if Y > d + z or Y < d - z then
        local q = (u.Position - M.Position)
        if q.Magnitude < .1 then q = Vector3.new(0, 0, 1) end
        q = q.Unit
        local c = (M.Position + q * d) + Vector3.new(0, 2.5, 0)
        pcall(function() u.CFrame = CFrame.new(c, M.Position) end)
    end
end

function StartSmartTargetLoop() while gN.smartTargetAuto do
        local q = u.TargetPlayer and u.TargetPlayer.Value
        if gN.autoRetarget and TargetInvalid(q) then
            SelectSmartTarget()
            q = u.TargetPlayer and u.TargetPlayer.Value
        end
        if q and q ~= "" then KeepDistanceToTarget(q) end
        task.wait(.12)
    end end

function SetSmartTargetLoopState(q)
    gN.smartTargetAuto = q and true or false
    if gN.smartTargetAuto then if not gN.smartTargetTask then gN.smartTargetTask = task.spawn(StartSmartTargetLoop) end else if gN.smartTargetTask then
            task.cancel(gN.smartTargetTask)
            gN.smartTargetTask = nil
        end end
end

function ClearThreatHighlights() for q, c in pairs(gN.threatHighlights) do
        if c then pcall(function() c:Destroy() end) end
        gN.threatHighlights[q] = nil
    end end

function EnsureOffscreenGui()
    if gN.offscreenGui and gN.offscreenGui.Parent then return gN.offscreenGui end
    local q = Instance.new("ScreenGui")
    q.Name = "WourldOffscreen"
    q.ResetOnSpawn = false
    q.IgnoreGuiInset = true
    q.Parent = game.CoreGui
    gN.offscreenGui = q
    return q
end

function ClearOffscreenIndicators()
    for q, c in pairs(gN.offscreenLabels) do
        if c then pcall(function() c:Destroy() end) end
        gN.offscreenLabels[q] = nil
    end
    if gN.offscreenGui then
        pcall(function() gN.offscreenGui:Destroy() end)
        gN.offscreenGui = nil
    end
end

function UpdateOffscreenIndicators()
    if not gN.offscreenIndicator then
        ClearOffscreenIndicators()
        return
    end
    local q = Y.CurrentCamera
    if not q then return end
    local c = EnsureOffscreenGui()
    local r = q.ViewportSize
    local j = {}
    for u, M in ipairs(G:GetPlayers()) do if M ~= b then
            local u = M.Character and M.Character:FindFirstChild("HumanoidRootPart")
            if u then
                local G = IsPlayerThreat(M)
                if ShouldShowByFocusMode(M, G) then
                    local d, z = q:WorldToViewportPoint(u.Position)
                    if not z then
                        j[M] = true
                        local q = gN.offscreenLabels[M]
                        if not q or not q.Parent then
                            q = Instance.new("TextLabel")
                            q.Name = "Off_" .. M.Name
                            q.Size = UDim2.new(0, 170, 0, 22)
                            q.BackgroundTransparency = 1
                            q.Font = Enum.Font.GothamBold
                            q.TextScaled = true
                            q.TextStrokeTransparency = .2
                            q.Parent = c
                            gN.offscreenLabels[M] = q
                        end
                        local u = math.clamp(d.X, 80, r.X - 80)
                        local z = math.clamp(d.Y, 20, r.Y - 24)
                        q.Position = UDim2.fromOffset(u - 85, z)
                        q.TextColor3 = G and Color3.fromRGB(255, 90, 90) or Color3.fromRGB(255, 255, 255)
                        q.Text = "< " .. (M.Name .. " >")
                    end
                end
            end
        end end
    for q, c in pairs(gN.offscreenLabels) do if not j[q] then
            pcall(function() c:Destroy() end)
            gN.offscreenLabels[q] = nil
        end end
end

function ApplyDynamicVisuals()
    local q, c, r = GetLocalCharacterData()
    if not q or not r then return end
    for q, c in ipairs(G:GetPlayers()) do if c ~= b then
            local q = IsPlayerThreat(c)
            if gN.threatHighlight then if q and ShouldShowByFocusMode(c, true) then
                    local q = gN.threatHighlights[c]
                    if not q or not q.Parent then
                        q = Instance.new("Highlight")
                        q.Name = "WourldThreat"
                        q.FillTransparency = .7
                        q.OutlineTransparency = 0
                        q.FillColor = Color3.fromRGB(255, 88, 88)
                        q.OutlineColor = Color3.fromRGB(255, 32, 32)
                        q.Parent = game.CoreGui
                        gN.threatHighlights[c] = q
                    end
                    q.Adornee = c.Character
                else
                    local q = gN.threatHighlights[c]
                    if q then
                        pcall(function() q:Destroy() end)
                        gN.threatHighlights[c] = nil
                    end
                end end
            if gN.dynamicEsp then
                local j = wN.usernameBillboards[c]
                local u = c.Character and c.Character:FindFirstChild("HumanoidRootPart")
                if j and (j.Label and u) then
                    local M = ((u.Position - r.Position)).Magnitude
                    local G = math.clamp(M / 250, .1, .9)
                    if not ShouldShowByFocusMode(c, q) then j.Gui.Enabled = false else
                        j.Gui.Enabled = true
                        j.Label.TextTransparency = G
                        j.Label.TextColor3 = q and Color3.fromRGB(255, 85, 85) or Color3.fromRGB(105, 205, 255)
                    end
                end
            end
        end end
    if not gN.threatHighlight then ClearThreatHighlights() end
    UpdateOffscreenIndicators()
end

function StartSmartVisualLoop()
    while gN.dynamicEsp or gN.threatHighlight or gN.offscreenIndicator do
        pcall(ApplyDynamicVisuals)
        task.wait(.12)
    end
    ClearThreatHighlights()
    ClearOffscreenIndicators()
end

function RefreshSmartVisualLoop()
    local q = gN.dynamicEsp or gN.threatHighlight or gN.offscreenIndicator
    if q then
        if gN.dynamicEsp and (M and (M.UsernameEspToggle and not M.UsernameEspToggle.Value)) then M.UsernameEspToggle
                :SetValue(true) end
        if not gN.visualTask then gN.visualTask = task.spawn(function()
                StartSmartVisualLoop()
                gN.visualTask = nil
            end) end
    else
        if gN.visualTask then
            task.cancel(gN.visualTask)
            gN.visualTask = nil
        end
        ClearThreatHighlights()
        ClearOffscreenIndicators()
    end
end

function GetGrabberPlayerName()
    local q, c, r = GetLocalCharacterData()
    if not q or not c or not r then return nil end
    local j = q:FindFirstChild("Head")
    local u = j and j:FindFirstChild("PartOwner")
    if u and (u:IsA("StringValue") and (u.Value ~= "" and u.Value ~= b.Name)) then return u.Value end
    local M = c.SeatPart
    if M and (M.Parent and M.Parent.Name == "CreatureBlobman") then
        local q = M.Parent:FindFirstChild("Head")
        local c = q and q:FindFirstChild("PartOwner")
        if c and (c:IsA("StringValue") and (c.Value ~= "" and c.Value ~= b.Name)) then return c.Value end
    end
    for q, c in ipairs(G:GetPlayers()) do if c ~= b then
            local q = c.Character and c.Character:FindFirstChild("HumanoidRootPart")
            if q and ((q.Position - r.Position)).Magnitude < 8 then return c.Name end
        end end
    return nil
end

function QuickCounterAttack(q)
    if not q or q == "" or q == b.Name then return end
    local r = G:FindFirstChild(q)
    local j = r and r.Character
    local u = j and j:FindFirstChild("HumanoidRootPart")
    if not r or not u then return end
    local M, d, z = GetLocalCharacterData()
    if not d or not z or d.Health <= 0 then return end
    local P = tostring(gN.counterAttackMode or "Both")
    pcall(function()
        d.PlatformStand = false
        d.Sit = false
        d.Jump = true
        d:ChangeState(Enum.HumanoidStateType.Jumping)
        if P == "Jump" or P == "Both" or gN.blobCounter then
            local q = Y.CurrentCamera and Y.CurrentCamera.CFrame.LookVector or z.CFrame.LookVector
            z.AssemblyLinearVelocity = Vector3.new(-q.X * 30, 72, -q.Z * 30)
        end
    end)
    if P == "Return" or P == "Both" then SmartReturnToSafe("Counter Recover") end
    drawKickLineToTarget(q, .42)
    gN.stats.counterAttacks = ((gN.stats.counterAttacks or 0)) + 1
    c:Notify({ Title = "Wourld Hub", Description = "Counter Recover [" .. (P .. ("] vs " .. q)), Duration = 2 })
end

function SmartReturnToSafe(q)
    local r, j, u = GetLocalCharacterData()
    if not j or not u or j.Health <= 0 then return end
    if gN.safeCFrame then pcall(function()
            u.CFrame = gN.safeCFrame + Vector3.new(0, 2.5, 0)
            u.AssemblyLinearVelocity = Vector3.zero
            u.AssemblyAngularVelocity = Vector3.zero
        end) else pcall(function()
            u.AssemblyLinearVelocity = Vector3.zero
            u.AssemblyAngularVelocity = Vector3.zero
        end) end
    gN.stats.defenceSaves = ((gN.stats.defenceSaves or 0)) + 1
    if tick() - ((gN.lastDefenceNotify or 0)) > .8 then
        gN.lastDefenceNotify = tick()
        if q and q ~= "" then c:Notify({ Title = "Wourld Hub", Description = "Defence: " .. q, Duration = 2 }) end
    end
end

function ScanTrapNear(q, c)
    if not q then return false end
    local r = { "trap", "cage", "box", "prison", "barrier" }
    local j = OverlapParams.new()
    j.FilterType = Enum.RaycastFilterType.Blacklist
    j.FilterDescendantsInstances = { c }
    local u = {}
    pcall(function() u = Y:GetPartBoundsInRadius(q.Position, 8, j) end)
    for q, j in ipairs(u) do if j and (j.Parent and not j:IsDescendantOf(c)) then
            local q = string.lower(j.Name or "")
            for c, r in ipairs(r) do if string.find(q, r, 1, true) then return true end end
        end end
    return false
end

function StartSmartDefenceLoop() while gN.smartDefence do
        local q, c, r = GetLocalCharacterData()
        local j = b:FindFirstChild("IsHeld")
        if q and (c and (r and c.Health > 0)) then
            local u = r.AssemblyLinearVelocity.Magnitude
            if not j or not j.Value then if not c.SeatPart and u < 28 then gN.safeCFrame = r.CFrame end end
            if gN.safeZoneReturn then
                if r.Position.Y < Y.FallenPartsDestroyHeight + 15 then SmartReturnToSafe("Safe Zone Return") end
                if c.Health < ((gN.lastHealth or c.Health)) - 22 then SmartReturnToSafe("Damage Return") end
            end
            if gN.velocityControl and u > ((gN.velocityLimit or 140)) then
                pcall(function()
                    r.AssemblyLinearVelocity = Vector3.zero
                    r.AssemblyAngularVelocity = Vector3.zero
                end)
                SmartReturnToSafe("Velocity Control")
            end
            if gN.trapEscape and (tick() - ((gN.lastTrap or 0)) > .3 and ScanTrapNear(r, q)) then
                gN.lastTrap = tick()
                pcall(function() r.CFrame = r.CFrame + Vector3.new(0, 9, 0) end)
                SmartReturnToSafe("Trap Escape")
            end
            if gN.smartAntiGrab then
                local c = (j and j.Value) or false
                local u = q:FindFirstChild("Head")
                local M = u and u:FindFirstChild("PartOwner")
                if M and (M:IsA("StringValue") and (M.Value ~= "" and M.Value ~= b.Name)) then c = true end
                if c then
                    pcall(function() r.AssemblyLinearVelocity = Vector3.new(0, 90, 0) end)
                    for q, c in ipairs(q:GetDescendants()) do if c:IsA("BasePart") then c.CanCollide = false end end
                    SmartReturnToSafe("Smart Anti Grab")
                    if gN.counterAttack and tick() - ((gN.lastCounter or 0)) > 1.2 then
                        gN.lastCounter = tick()
                        local q = GetGrabberPlayerName()
                        if q then QuickCounterAttack(q) end
                    end
                end
            end
            gN.lastHealth = c.Health
        end
        task.wait(.07)
    end end

function SetSmartDefenceState(q)
    gN.smartDefence = q and true or false
    if gN.smartDefence then
        local q, c, r = GetLocalCharacterData()
        gN.safeCFrame = r and r.CFrame or gN.safeCFrame
        gN.lastHealth = c and c.Health or gN.lastHealth
        if not gN.smartDefenceTask then gN.smartDefenceTask = task.spawn(StartSmartDefenceLoop) end
    else if gN.smartDefenceTask then
            task.cancel(gN.smartDefenceTask)
            gN.smartDefenceTask = nil
        end end
end

function StartTriggerBotLoop()
    local q = 0
    while gN.triggerBot do
        local c = u.TargetPlayer and u.TargetPlayer.Value
        local r = G:FindFirstChild(c or "")
        local j, M, z = GetLocalCharacterData()
        local Y = r and (r.Character and r.Character:FindFirstChild("HumanoidRootPart"))
        local P = r and (r.Character and r.Character:FindFirstChildOfClass("Humanoid"))
        if M and (z and (Y and (P and P.Health > 0))) then
            local c = ((Y.Position - z.Position)).Magnitude
            if c <= ((gN.triggerDistance or 24)) then
                local c = 1 / math.max(1, gN.triggerRate or 6)
                if tick() - q >= c then
                    q = tick()
                    local c = (d:WaitForChild("GrabEvents")):WaitForChild("SetNetworkOwner")
                    pcall(function() c:FireServer(Y, Y.CFrame) end)
                    pcall(function() c:FireServer(Y, Y.CFrame) end)
                    if gN.triggerUseKick and M.SeatPart then pcall(function() Kick(r.Name) end) end
                    gN.stats.actions = ((gN.stats.actions or 0)) + 1
                end
            end
        end
        task.wait(.03)
    end
end

function getKickAuraTargets(q)
    local c, r, j = GetLocalCharacterData()
    if not j then return {} end
    local u = math.max(4, tonumber(q) or 16)
    local M = {}
    for q, c in ipairs(G:GetPlayers()) do if c ~= b and c.Character then
            local q = c.Character:FindFirstChildOfClass("Humanoid")
            local r = c.Character:FindFirstChild("HumanoidRootPart")
            if q and (r and q.Health > 0) then
                local q = ((r.Position - j.Position)).Magnitude
                if q <= u then table.insert(M, { player = c, distance = q, root = r }) end
            end
        end end
    table.sort(M, function(q, c) return ((q.distance or math.huge)) < ((c.distance or math.huge)) end)
    return M
end

function StartKickAuraLoop() while SN do
        if tick() - ((nN or 0)) >= math.max(.2, tonumber(CN) or .8) then
            local q = getKickAuraTargets(sN)
            if #q > 0 then
                nN = tick()
                kickSelfGuardUntil = math.max(kickSelfGuardUntil or 0, tick() + 2.1)
                local c = math.min(18, #q)
                for c = 1, c, 1 do
                    local r = q[c]
                    if r and r.player then
                        local q = c
                        local j = r.player.Name
                        rememberKickAttempt(j)
                        drawKickLineToTarget(j, .55)
                        if r.root and r.root.Parent then applyKickAuraTypeEffect(r.root) end
                        task.spawn(function()
                            if q > 1 then task.wait(((q - 1)) * .02) end
                            pcall(function() TryKickTargetNoBlob(j, 1) end)
                        end)
                    end
                end
            end
        end
        task.wait(.08)
    end end

function setKickAuraState(q)
    SN = q and true or false
    _G.KickAura = SN
    if NN then
        task.cancel(NN)
        NN = nil
    end
    if SN then
        nN = 0
        NN = task.spawn(StartKickAuraLoop)
    end
end

function SetTriggerBotState(q)
    gN.triggerBot = q and true or false
    if gN.triggerBot then if not gN.triggerBotTask then gN.triggerBotTask = task.spawn(StartTriggerBotLoop) end else if gN.triggerBotTask then
            task.cancel(gN.triggerBotTask)
            gN.triggerBotTask = nil
        end end
end

function StartOrbitLoop()
    local q = 0
    local c = Vector3.zero
    local r = nil
    while gN.orbit do
        local j = z.Heartbeat:Wait()
        j = math.clamp(tonumber(j) or .016, .008, .06)
        local M = u.TargetPlayer and u.TargetPlayer.Value
        local d = G:FindFirstChild(M or "")
        local Y, P, O = GetLocalCharacterData()
        local a = d and (d.Character and d.Character:FindFirstChild("HumanoidRootPart"))
        if P and (O and (a and P.Health > 0)) then
            local u = math.max(4, gN.orbitRadius or 12)
            local M = math.max(.2, gN.orbitSpeed or 1.8)
            r = r or a.Position
            r = r:Lerp(a.Position, math.clamp(j * 6.8, .08, .38))
            q = q + (M * j) * 2.35
            local G = Vector3.new(math.cos(q) * u, 1.85, math.sin(q) * u)
            local d = r + G
            pcall(function()
                local q = d - O.Position
                local G = q * math.clamp(j * 9.2, .08, .46)
                c = ((c + G)) * math.clamp(1 - j * 2.8, .72, .98)
                local z = math.max(18, u * ((M * 7)))
                if c.Magnitude > z then c = c.Unit * z end
                local Y = O.Position + c
                local P = r + Vector3.new(0, 1.4, 0)
                local a = CFrame.new(Y, P)
                local o = math.clamp(.2 + j * 3.4, .18, .44)
                O.CFrame = O.CFrame:Lerp(a, o)
            end)
        else
            r = nil
            c = c * .6
        end
    end
end

function SetOrbitState(q)
    gN.orbit = q and true or false
    if gN.orbit then if not gN.orbitTask then gN.orbitTask = task.spawn(StartOrbitLoop) end else if gN.orbitTask then
            task.cancel(gN.orbitTask)
            gN.orbitTask = nil
        end end
end

function StartAutoEdgeSaveLoop() while gN.autoEdgeSave do
        local q, c, r = GetLocalCharacterData()
        if q and (c and (r and c.Health > 0)) then
            local c = RaycastParams.new()
            c.FilterType = Enum.RaycastFilterType.Blacklist
            c.FilterDescendantsInstances = { q }
            local j = Y:Raycast(r.Position, Vector3.new(0, -8.5, 0), c)
            if not j and r.AssemblyLinearVelocity.Y < -2 then
                SmartReturnToSafe("Auto Edge Save")
                gN.stats.edgeSaves = ((gN.stats.edgeSaves or 0)) + 1
            end
        end
        task.wait(.07)
    end end

function SetAutoEdgeSaveState(q)
    gN.autoEdgeSave = q and true or false
    if gN.autoEdgeSave then if not gN.autoEdgeTask then gN.autoEdgeTask = task.spawn(StartAutoEdgeSaveLoop) end else if gN.autoEdgeTask then
            task.cancel(gN.autoEdgeTask)
            gN.autoEdgeTask = nil
        end end
end

function SmartTeleportToPosition(q)
    local c, r, j = GetLocalCharacterData()
    if not c or not r or not j or r.Health <= 0 then return end
    local u = q + Vector3.new(0, 4, 0)
    if gN.smartTeleport then
        local r = RaycastParams.new()
        r.FilterType = Enum.RaycastFilterType.Blacklist
        r.FilterDescendantsInstances = { c }
        local j = q + Vector3.new(0, 60, 0)
        local M = Y:Raycast(j, Vector3.new(0, -160, 0), r)
        if M then u = M.Position + Vector3.new(0, 4, 0) end
        if u.Y < Y.FallenPartsDestroyHeight + 20 and gN.safeCFrame then u = gN.safeCFrame.Position + Vector3.new(0, 3, 0) end
    end
    pcall(function()
        local q = j.Position
        local c = j.CFrame.LookVector
        local r = ((q - u)).Magnitude
        if r <= 240 then
            local M = math.clamp(math.floor(r / 8) + 7, 7, 28)
            local G = math.clamp(.22 + r * .001, .22, .45)
            for d = 1, M, 1 do
                local Y = d / M
                local P = (Y * Y) * ((3 - (2 * Y)))
                local O = math.sin(math.pi * Y) * math.min(2.6, r * .014)
                local a = q:Lerp(u, P) + Vector3.new(0, O, 0)
                local o = ((u - a)).Magnitude > .01 and ((u - a)).Unit or c
                local v = CFrame.new(a, a + o)
                j.CFrame = j.CFrame:Lerp(v, G)
                z.Heartbeat:Wait()
            end
        else j.CFrame = CFrame.new(u, u + c) end
    end)
end

function queueAutoRejoin(q, r)
    if not ((gN and gN.autoRejoin)) then return false end
    local j = tick()
    local u = tonumber(gN.autoRejoinRetryAt) or 0
    if j < u then return false end
    gN.autoRejoinRetryAt = j + 1.1
    if gN.autoRejoinRetryBusy then return false end
    gN.autoRejoinRetryBusy = true
    task.spawn(function()
        local q = game:GetService("TeleportService")
        local c = math.clamp(tonumber(r) or .9, .05, 5)
        task.wait(c)
        for c = 1, 5, 1 do
            if not ((gN and gN.autoRejoin)) then break end
            local r = false
            pcall(function()
                q:TeleportToPlaceInstance(game.PlaceId, game.JobId, b)
                r = true
            end)
            if r then break end
            pcall(function()
                q:Teleport(game.PlaceId, b)
                r = true
            end)
            if r then break end
            task.wait(.45 + c * .2)
        end
        gN.autoRejoinRetryBusy = false
    end)
    if q and q ~= "" then c:Notify({ Title = "Wourld Hub", Description = "Auto rejoin: " .. tostring(q), Duration = 2.2 }) end
    return true
end

function SetAutoRejoinState(q)
    gN.autoRejoin = q and true or false
    if gN.autoRejoinConn then
        pcall(function() gN.autoRejoinConn:Disconnect() end)
        gN.autoRejoinConn = nil
    end
    if gN.autoRejoinTeleportFailConn then
        pcall(function() gN.autoRejoinTeleportFailConn:Disconnect() end)
        gN.autoRejoinTeleportFailConn = nil
    end
    gN.autoRejoinRetryBusy = false
    gN.autoRejoinRetryAt = 0
    if gN.autoRejoin then
        local q = game:GetService("GuiService")
        local c = game:GetService("TeleportService")
        gN.autoRejoinConn = q.ErrorMessageChanged:Connect(function(q) if gN.autoRejoin and (q and q ~= "") then
                queueAutoRejoin("gui error", 1.4) end end)
        if c and c.TeleportInitFailed then gN.autoRejoinTeleportFailConn = c.TeleportInitFailed:Connect(function(q, c, r)
                if not gN.autoRejoin then return end
                if q and q ~= b then return end
                local j = tostring(c or r or "teleport init failed")
                queueAutoRejoin(j, .8)
            end) end
    end
end

function QuickServerSwitch(q)
    local r = game:GetService("TeleportService")
    local j = game:GetService("HttpService")
    local u = game.PlaceId
    local M = nil
    local G = ""
    local d = tonumber(q) or 8
    for q = 1, 4, 1 do
        local c = "https://games.roblox.com/v1/games/" .. (tostring(u) .. "/servers/Public?sortOrder=Asc&limit=100")
        if G ~= "" then c = c .. ("&cursor=" .. G) end
        local r = nil
        pcall(function() r = game:HttpGet(c) end)
        if not r then break end
        local z = nil
        pcall(function() z = j:JSONDecode(r) end)
        if not z or not z.data then break end
        for q, c in ipairs(z.data) do if c and (c.id and (c.playing and (c.maxPlayers and c.id ~= game.JobId))) then if c.playing < c.maxPlayers and c.playing <= d then
                    M = c.id
                    break
                end end end
        if M then break end
        G = z.nextPageCursor or ""
        if G == "" then break end
    end
    if M then r:TeleportToPlaceInstance(u, M, b) else c:Notify({ Title = "Wourld Hub", Description =
        "No low-pop server found", Duration = 3 }) end
end

function ShowSessionStats()
    local q = gN.stats
    c:Notify({ Title = "Wourld Hub", Description = "Saves: " ..
    (tostring(q.defenceSaves) .. (" | Counter: " .. (tostring(q.counterAttacks) .. (" | Actions: " .. (tostring(q.actions) .. (" | Edge: " .. tostring(q.edgeSaves))))))), Duration = 6 })
end

function StartAutoPerformanceLoop() while gN.autoPerfMode do
        local q = z.Heartbeat:Wait()
        local c = math.floor(1 / math.max(q, .0041666666666667))
        local r = tonumber(gN.perfThreshold) or 35
        if c < r and not gN.perfApplied then
            gN.perfApplied = true
            s6 = true
            applyLowGraphicsState(true)
            if M.DynamicEspToggle and M.DynamicEspToggle.Value then M.DynamicEspToggle:SetValue(false) end
            if M.OffscreenIndicatorToggle and M.OffscreenIndicatorToggle.Value then M.OffscreenIndicatorToggle:SetValue(false) end
        elseif c > r + 8 and gN.perfApplied then
            gN.perfApplied = false
            s6 = false
            applyLowGraphicsState(false)
        end
        task.wait(.2)
    end end

function SetAutoPerformanceState(q)
    gN.autoPerfMode = q and true or false
    if gN.autoPerfMode then if not gN.autoPerfTask then gN.autoPerfTask = task.spawn(StartAutoPerformanceLoop) end else
        if gN.autoPerfTask then
            task.cancel(gN.autoPerfTask)
            gN.autoPerfTask = nil
        end
        if gN.perfApplied then
            gN.perfApplied = false
            s6 = false
            applyLowGraphicsState(false)
        end
    end
end

function getAutoSitConflictReason()
    if antiRagBlobActive then return "Anti Blobman" end
    if antiBlobDenyActive then return "Anti Enemy Blob" end
    if gN and gN.gucciKeyActive then return "Gucci mode" end
    if antiGucciActive then return "Anti Gucci" end
    if gucciProtectActive then return "Gucci Protect" end
    if destroyGucciActive then return "Destroy Gucci" end
    return nil
end

function disableAutoSitBloomWithNotify(q)
    local r = ((_G.AutoSitBlobZ or _G.AutoSitBloom)) and true or false
    _G.AutoSitBlobZ = false
    _G.AutoSitBloom = false
    if M and (M.AutoSitBlobmanToggle and M.AutoSitBlobmanToggle.Value) then pcall(function() M.AutoSitBlobmanToggle
                :SetValue(false) end) end
    if r and (tick() - ((N6 or 0))) > 1.2 then
        N6 = tick()
        c:Notify({ Title = "Wourld Hub", Description = "Auto Sit Bloom disabled: conflict with " ..
        tostring(q or "another feature"), Duration = 3 })
    end
end

function AutoSitLoop()
    S6 = S6 + 1
    local q = S6
    while ((_G.AutoSitBlobZ or _G.AutoSitBloom)) and q == S6 do
        local c = getAutoSitConflictReason()
        if c then
            disableAutoSitBloomWithNotify(c)
            break
        end
        local r = b
        local j = r.Character
        local u = j and j:FindFirstChild("HumanoidRootPart")
        local M = j and j:FindFirstChildOfClass("Humanoid")
        if not u or not M or M.Health <= 0 then
            task.wait(.4)
            continue
        end
        local G = M.SeatPart
        if G and (G.Parent and G.Parent.Name == "CreatureBlobman") then
            local q = G
            if q.Position.Y < -120 then
                pcall(function() M.Sit = false end)
                pcall(function()
                    q.CFrame = CFrame.new(u.Position.X, math.max(u.Position.Y, 8) + 5, u.Position.Z)
                    q.AssemblyLinearVelocity = Vector3.new(q.AssemblyLinearVelocity.X * .2, 0,
                        q.AssemblyLinearVelocity.Z * .2)
                    q.AssemblyAngularVelocity = Vector3.zero
                end)
            elseif q.AssemblyLinearVelocity.Y < -85 then pcall(function() q.AssemblyLinearVelocity = Vector3.new(
                    q.AssemblyLinearVelocity.X * .5, -3, q.AssemblyLinearVelocity.Z * .5) end) end
            task.wait(.14)
            continue
        elseif G then
            task.wait(.2)
            continue
        end
        local P = r.Name .. "SpawnedInToys"
        local O = Y:FindFirstChild(P)
        local a = O and O:FindFirstChild("CreatureBlobman")
        if not a then
            task.spawn(function() pcall(function() d.MenuToys.SpawnToyRemoteFunction:InvokeServer("CreatureBlobman",
                        u.CFrame * CFrame.new(0, 0, -4), Vector3.zero) end) end)
            if not O then O = Y:WaitForChild(P, 3) end
            if O then a = O:FindFirstChild("CreatureBlobman") or O:WaitForChild("CreatureBlobman", 3) end
        end
        if a then
            local c = a:FindFirstChild("VehicleSeat") or a:WaitForChild("VehicleSeat", 3)
            if c then
                local r = tick()
                repeat
                    if getAutoSitConflictReason() then
                        disableAutoSitBloomWithNotify(getAutoSitConflictReason())
                        return
                    end
                    if M.Health <= 0 then break end
                    if not M.SeatPart then pcall(function()
                            u.CFrame = c.CFrame + Vector3.new(0, 1.25, 0)
                            u.AssemblyLinearVelocity = Vector3.zero
                            M.Jump = false
                            c:Sit(M)
                        end) end
                    z.Heartbeat:Wait()
                until M.SeatPart == c or tick() - r > 1.7 or not ((_G.AutoSitBlobZ or _G.AutoSitBloom)) or q ~= S6
            end
        end
        task.wait(.2)
    end
end

function getOrCreateBloomEffect()
    local q = P:FindFirstChild("WourldBloom")
    if q and not q:IsA("BloomEffect") then
        q:Destroy()
        q = nil
    end
    if not q then
        q = Instance.new("BloomEffect")
        q.Name = "WourldBloom"
        q.Parent = P
    end
    return q
end

function applyBloomEffectState()
    local q = getOrCreateBloomEffect()
    q.Intensity = bloomFXIntensity
    q.Size = bloomFXSize
    q.Threshold = bloomFXThreshold
    q.Enabled = bloomFXEnabled
end

function stopNeonAura()
    if neonAuraConnection then
        neonAuraConnection:Disconnect()
        neonAuraConnection = nil
    end
    local q = b.Character
    if q then
        local c = q:FindFirstChild("HumanoidRootPart")
        if c then
            local q = c:FindFirstChild("WourldNeonAura")
            if q then q:Destroy() end
        end
    end
end

function startNeonAura()
    stopNeonAura()
    neonAuraConnection = z.Heartbeat:Connect(function()
        if not neonAuraEnabled then return end
        local q = b.Character
        local c = q and q:FindFirstChild("HumanoidRootPart")
        if not c then return end
        local r = c:FindFirstChild("WourldNeonAura")
        if not r then
            r = Instance.new("PointLight")
            r.Name = "WourldNeonAura"
            r.Brightness = 2.5
            r.Range = 28
            r.Shadows = true
            r.Parent = c
        end
        r.Color = Color3.fromHSV(((tick() * .14)) % 1, .9, 1)
    end)
end

function stopAccentPulse()
    accentPulseEnabled = false
    if accentPulseTask then
        task.cancel(accentPulseTask)
        accentPulseTask = nil
    end
    if baseAccentColor then
        c.Scheme.AccentColor = baseAccentColor
        if c.UpdateColorsUsingRegistry then c:UpdateColorsUsingRegistry() end
    end
end

function startAccentPulse()
    if accentPulseTask then return end
    baseAccentColor = c.Scheme.AccentColor
    accentPulseEnabled = true
    accentPulseTask = task.spawn(function() while accentPulseEnabled do
            c.Scheme.AccentColor = Color3.fromHSV(((tick() * .08)) % 1, .85, 1)
            if c.UpdateColorsUsingRegistry then c:UpdateColorsUsingRegistry() end
            task.wait(.05)
        end end)
end

function ensureLightingDefaults()
    if n6 then return end
    n6 = { Brightness = P.Brightness, ClockTime = P.ClockTime, FogStart = P.FogStart, FogEnd = P.FogEnd, GlobalShadows =
    P.GlobalShadows, Ambient = P.Ambient, OutdoorAmbient = P.OutdoorAmbient }
end

function getOrCreateColorCorrection()
    local q = P:FindFirstChild("WourldColorCorrection")
    if q and not q:IsA("ColorCorrectionEffect") then
        q:Destroy()
        q = nil
    end
    if not q then
        q = Instance.new("ColorCorrectionEffect")
        q.Name = "WourldColorCorrection"
        q.Parent = P
    end
    return q
end

function getOrCreateSunRays()
    local q = P:FindFirstChild("WourldSunRays")
    if q and not q:IsA("SunRaysEffect") then
        q:Destroy()
        q = nil
    end
    if not q then
        q = Instance.new("SunRaysEffect")
        q.Name = "WourldSunRays"
        q.Parent = P
    end
    return q
end

function applyAtmosphereState() for q, c in ipairs(P:GetDescendants()) do if c:IsA("Atmosphere") then if atmosphereOffActive then
                if c:GetAttribute("WourldDensity") == nil then
                    c:SetAttribute("WourldDensity", c.Density)
                    c:SetAttribute("WourldHaze", c.Haze)
                    c:SetAttribute("WourldGlare", c.Glare)
                end
                c.Density = 0
                c.Haze = 0
                c.Glare = 0
            else
                local q = c:GetAttribute("WourldDensity")
                if q ~= nil then
                    c.Density = q
                    c.Haze = c:GetAttribute("WourldHaze") or c.Haze
                    c.Glare = c:GetAttribute("WourldGlare") or c.Glare
                    c:SetAttribute("WourldDensity", nil)
                    c:SetAttribute("WourldHaze", nil)
                    c:SetAttribute("WourldGlare", nil)
                end
            end end end end

function applyLowGraphicsState(q) for c, r in ipairs(Y:GetDescendants()) do if r:IsA("BasePart") then if q then
                if r:GetAttribute("WourldOldMaterial") == nil then
                    r:SetAttribute("WourldOldMaterial", r.Material.Name)
                    r:SetAttribute("WourldOldReflectance", r.Reflectance)
                end
                r.Material = Enum.Material.SmoothPlastic
                r.Reflectance = 0
            else
                local q = r:GetAttribute("WourldOldMaterial")
                if q then
                    local c = Enum.Material[q]
                    if c then r.Material = c end
                    r.Reflectance = r:GetAttribute("WourldOldReflectance") or 0
                    r:SetAttribute("WourldOldMaterial", nil)
                    r:SetAttribute("WourldOldReflectance", nil)
                end
            end elseif r:IsA("Decal") or r:IsA("Texture") then if q then
                if r:GetAttribute("WourldOldTransparency") == nil then r:SetAttribute("WourldOldTransparency",
                        r.Transparency) end
                r.Transparency = 1
            else
                local q = r:GetAttribute("WourldOldTransparency")
                if q ~= nil then
                    r.Transparency = q
                    r:SetAttribute("WourldOldTransparency", nil)
                end
            end elseif r:IsA("ParticleEmitter") or r:IsA("Trail") or r:IsA("Beam") or r:IsA("Smoke") or r:IsA("Fire") or r:IsA("Sparkles") then if q then
                if r:GetAttribute("WourldOldEnabled") == nil then r:SetAttribute("WourldOldEnabled", r.Enabled) end
                r.Enabled = false
            else
                local q = r:GetAttribute("WourldOldEnabled")
                if q ~= nil then
                    r.Enabled = q
                    r:SetAttribute("WourldOldEnabled", nil)
                end
            end end end end

function applyHidePlayersState() for q, c in ipairs(G:GetPlayers()) do if c ~= b then
            local q = c.Character
            if q then for q, c in ipairs(q:GetDescendants()) do if c:IsA("BasePart") then
                        local q = c.Parent and c.Parent:IsA("Accessory")
                        local r = hidePlayersActive or (hideAccessoriesActive and q)
                        c.LocalTransparencyModifier = r and hidePlayersTransparency or 0
                    end end end
        end end end

function applyRainbowCharacter()
    if not rainbowBodyActive then return end
    local q = b.Character
    if not q then return end
    local c = Color3.fromHSV(((tick() * .18)) % 1, .9, 1)
    for q, r in ipairs(q:GetDescendants()) do if r:IsA("BasePart") and r.Name ~= "HumanoidRootPart" then r.Color = c end end
end

function applyGravityState() if l6 then Y.Gravity = D6 else Y.Gravity = J6 end end

function applyVisualEnhancements()
    ensureLightingDefaults()
    if fullBrightActive then
        P.Brightness = 3
        P.GlobalShadows = false
        P.Ambient = Color3.fromRGB(255, 255, 255)
        P.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
    else
        P.Brightness = n6.Brightness
        P.GlobalShadows = n6.GlobalShadows
        P.Ambient = n6.Ambient
        P.OutdoorAmbient = n6.OutdoorAmbient
    end
    if noFogActive then
        P.FogStart = 0
        P.FogEnd = 1000000
    else
        P.FogStart = n6.FogStart
        P.FogEnd = n6.FogEnd
    end
    if timeLockActive then P.ClockTime = timeLockValue else P.ClockTime = n6.ClockTime end
    local q = getOrCreateColorCorrection()
    if saturationFXActive or contrastFXActive then
        q.Enabled = true
        q.Saturation = saturationFXActive and .55 or 0
        q.Contrast = contrastFXActive and .24 or 0
    else
        q.Enabled = false
        q.Saturation = 0
        q.Contrast = 0
    end
    local c = getOrCreateSunRays()
    c.Enabled = sunRaysFXActive
    if sunRaysFXActive then
        c.Intensity = .08
        c.Spread = .78
    end
    applyAtmosphereState()
    applyHidePlayersState()
    applyRainbowCharacter()
    applyGravityState()
end

function visualEnhancementNeeded() return fullBrightActive or noFogActive or timeLockActive or saturationFXActive or
    contrastFXActive or sunRaysFXActive or atmosphereOffActive or hidePlayersActive or hideAccessoriesActive or
    rainbowBodyActive or l6 end

function refreshVisualEnhanceLoop() if visualEnhancementNeeded() then if not C6 then C6 = task.spawn(function()
                while visualEnhancementNeeded() do
                    applyVisualEnhancements()
                    task.wait(.15)
                end
                applyVisualEnhancements()
                C6 = nil
            end) end else
        if C6 then
            task.cancel(C6)
            C6 = nil
        end
        applyVisualEnhancements()
    end end

function startSpeedLockLoop()
    if HN then
        task.cancel(HN)
        HN = nil
    end
    local q = b.Character
    local c = q and q:FindFirstChildOfClass("Humanoid")
    if c and not EN then EN = c.WalkSpeed end
    _G.SuperSpeed = true
    HN = task.spawn(function() while AN do
            local q = b.Character
            local c = q and q:FindFirstChildOfClass("Humanoid")
            if c and c.Health > 0 then c.WalkSpeed = math.clamp(tonumber(fN) or 30, 16, 220) end; (getgenv()).Multiplier =
            math.clamp(((tonumber(fN) or 30)) / 180, .05, 5)
            task.wait(.03)
        end end)
end

function stopSpeedLockLoop()
    _G.SuperSpeed = false
    if HN then
        task.cancel(HN)
        HN = nil
    end; (getgenv()).Multiplier = .15
    local q = b.Character
    local c = q and q:FindFirstChildOfClass("Humanoid")
    if c then c.WalkSpeed = tonumber(EN) or 16 end
    EN = nil
end

function startJumpLockLoop()
    if LN then
        task.cancel(LN)
        LN = nil
    end
    LN = task.spawn(function() while mN do
            local q = b.Character
            local c = q and q:FindFirstChildOfClass("Humanoid")
            if c then
                c.UseJumpPower = true
                c.JumpPower = iN
            end
            task.wait(.1)
        end end)
end

function stopJumpLockLoop()
    if LN then
        task.cancel(LN)
        LN = nil
    end
    local q = b.Character
    local c = q and q:FindFirstChildOfClass("Humanoid")
    if c then
        c.UseJumpPower = true
        c.JumpPower = 50
    end
end

function startNoclip()
    if IN then
        IN:Disconnect()
        IN = nil
    end
    IN = z.Stepped:Connect(function()
        if not pN then return end
        local q = b.Character
        if q then for q, c in ipairs(q:GetDescendants()) do if c:IsA("BasePart") then c.CanCollide = false end end end
    end)
end

function stopNoclip()
    if IN then
        IN:Disconnect()
        IN = nil
    end
    local q = b.Character
    if q then for q, c in ipairs(q:GetDescendants()) do if c:IsA("BasePart") then c.CanCollide = true end end end
end

function startInfiniteJump()
    if yN then
        yN:Disconnect()
        yN = nil
    end
    yN = O.JumpRequest:Connect(function()
        if not QN then return end
        local q = b.Character
        local c = q and q:FindFirstChildOfClass("Humanoid")
        if c then c:ChangeState(Enum.HumanoidStateType.Jumping) end
    end)
end

function stopInfiniteJump() if yN then
        yN:Disconnect()
        yN = nil
    end end

function startAntiAfk()
    if RN then
        RN:Disconnect()
        RN = nil
    end
    RN = b.Idled:Connect(function()
        if not WN then return end
        pcall(function()
            local q = game:GetService("VirtualUser")
            q:CaptureController()
            q:ClickButton2(Vector2.new(0, 0))
        end)
    end)
end

function stopAntiAfk() if RN then
        RN:Disconnect()
        RN = nil
    end end

function startSpinbot()
    if ZN then
        task.cancel(ZN)
        ZN = nil
    end
    ZN = task.spawn(function() while kN do
            local q = b.Character
            local c = q and q:FindFirstChild("HumanoidRootPart")
            if c then c.CFrame = c.CFrame * CFrame.Angles(0, math.rad(KN), 0) end
            z.Heartbeat:Wait()
        end end)
end

function stopSpinbot() if ZN then
        task.cancel(ZN)
        ZN = nil
    end end

local Ba = nil
function bindAutoRespawnToCharacter(q)
    E6 = false
    if p6 then
        task.cancel(p6)
        p6 = nil
    end
    if Ba then
        Ba:Disconnect()
        Ba = nil
    end
    local c = q and q:FindFirstChildOfClass("Humanoid")
    if not c then return end
    Ba = c.Died:Connect(function()
        if not w6 then return end
        requestAutoRespawn("death", 1.25)
    end)
end

function StartAutoRespawnMonitor() while w6 do
        local q = b.Character
        local c = q and q:FindFirstChildOfClass("Humanoid")
        local r = q and q:FindFirstChild("HumanoidRootPart")
        local j = b:FindFirstChild("IsHeld")
        if not q or not c then
            requestAutoRespawn("character missing", .15)
            task.wait(.45)
            continue
        end
        if c.Health <= 0 then
            requestAutoRespawn("health 0", .12)
            task.wait(.45)
            continue
        end
        if r and r.Position.Y < -650 then
            requestAutoRespawn("void stuck", .05)
            task.wait(.42)
            continue
        end
        if j and j.Value then if i6 then
                if shouldBlockHeldAutoReset() then
                    L6 = 0
                    task.wait(.22)
                    continue
                end
                if L6 <= 0 then L6 = tick() end
                local q = tick() - L6
                local c = (tick() - ((tonumber(K6) or 0))) <= 1.2
                if c or q >= 1.05 then
                    requestAutoRespawn("kick hold detected", .02, true)
                    task.wait(.4)
                    continue
                end
            end else L6 = 0 end
        task.wait(.28)
    end end

function setAutoRespawnState(q)
    w6 = q
    L6 = 0
    if not q then E6 = false end
    if g6 then
        g6:Disconnect()
        g6 = nil
    end
    if A6 then
        task.cancel(A6)
        A6 = nil
    end
    if Ba then
        Ba:Disconnect()
        Ba = nil
    end
    if p6 then
        task.cancel(p6)
        p6 = nil
    end
    if q then
        g6 = b.CharacterAdded:Connect(function(q) bindAutoRespawnToCharacter(q) end)
        bindAutoRespawnToCharacter(b.Character)
        A6 = task.spawn(StartAutoRespawnMonitor)
    end
end

function runExternalLoadstring(q, r)
    local j, u = pcall(function() return game:HttpGet(q) end)
    if not j or type(u) ~= "string" or #u < 12 then
        c:Notify({ Title = "Wourld Hub", Description = tostring(r or "Feature") .. " load failed", Duration = 3 })
        return false
    end
    local M, G = pcall(function() return loadstring(u) end)
    if not M or type(G) ~= "function" then
        c:Notify({ Title = "Wourld Hub", Description = tostring(r or "Feature") .. " compile failed", Duration = 3 })
        return false
    end
    local d = pcall(G)
    if d then
        c:Notify({ Title = "Wourld Hub", Description = tostring(r or "Feature") .. " loaded", Duration = 3 })
        return true
    end
    c:Notify({ Title = "Wourld Hub", Description = tostring(r or "Feature") .. " runtime error", Duration = 3 })
    return false
end

function clearAngelAura()
    if angelAuraTask then
        task.cancel(angelAuraTask)
        angelAuraTask = nil
    end
    angelLeftWeld = nil
    angelRightWeld = nil
    if angelAuraModel and angelAuraModel.Parent then angelAuraModel:Destroy() end
    angelAuraModel = nil
end

function createAngelWingPart(q, c, r, j)
    local u = Instance.new("Part")
    u.Name = c
    u.CanCollide = false
    u.CanTouch = false
    u.CanQuery = false
    u.Massless = true
    u.Transparency = 1
    u.Size = Vector3.new(.25, .25, .25)
    u.Parent = q
    local M = j and 1 or -1
    local G = r or Color3.fromRGB(190, 235, 255)
    local d = wN.angelWingAccentColor or Color3.fromRGB(255, 255, 255)
    local z = Instance.new("Part")
    z.Name = c .. "Spine"
    z.CanCollide = false
    z.CanTouch = false
    z.CanQuery = false
    z.Massless = true
    z.Material = Enum.Material.Neon
    z.Color = G:Lerp(d, .35)
    z.Transparency = .22
    z.Size = Vector3.new(.22, .6, 1.55)
    z.Parent = q
    local Y = CFrame.new(M * .48, .11, .74) * CFrame.Angles(math.rad(4), math.rad(M * 12), 0)
    z.CFrame = u.CFrame * Y
    local P = Instance.new("Weld")
    P.Part0 = u
    P.Part1 = z
    P.C0 = Y
    P.Parent = z
    for r = 1, 10, 1 do for j = 1, 2, 1 do
            local z = Instance.new("Part")
            z.Name = c .. ("Feather" .. (tostring(r) .. ("_" .. tostring(j))))
            z.CanCollide = false
            z.CanTouch = false
            z.CanQuery = false
            z.Massless = true
            z.Material = Enum.Material.Neon
            local Y = math.clamp((.08 + ((r / 10)) * .55) + ((j == 2 and .08 or 0)), 0, .95)
            z.Color = G:Lerp(d, Y)
            z.Transparency = math.clamp((.06 + r * .018) + ((j == 2 and .08 or 0)), .05, .55)
            z.Size = Vector3.new(.24 + r * .022, .16 + r * .028, .95 + r * .24)
            z.Parent = q
            z:SetAttribute("WingIndex", r)
            z:SetAttribute("WingLayer", j)
            local P = Instance.new("SpecialMesh")
            P.MeshType = Enum.MeshType.Wedge
            P.Scale = Vector3.new(1, 1 + r * .03, 1.2 + r * .08)
            P.Parent = z
            local O = j == 1 and .12 or -0.12
            local a = j == 1 and .03 or -0.03
            local o = j == 1 and -2 or 4
            local v = CFrame.new(M * ((.22 + r * .135)), (.28 - r * .03) + a, (.02 + r * .14) + O) *
            CFrame.Angles(math.rad(4 + r * .9), math.rad(M * ((11 + r * 5.5))), math.rad(M * (((9 + r * 3.5) + o))))
            z.CFrame = u.CFrame * v
            local b = Instance.new("Weld")
            b.Part0 = u
            b.Part1 = z
            b.C0 = v
            b.Parent = z
        end end
    return u
end

function refreshAngelWingPalette()
    if not angelAuraModel or not angelAuraModel.Parent then return end
    local q = wN.angelWingColor or Color3.fromRGB(190, 235, 255)
    local c = wN.angelWingAccentColor or Color3.fromRGB(255, 255, 255)
    for r, j in ipairs(angelAuraModel:GetDescendants()) do if j:IsA("BasePart") then
            local r = string.lower(j.Name)
            if string.find(r, "feather", 1, true) then
                local r = tonumber(j:GetAttribute("WingIndex")) or 1
                local u = tonumber(j:GetAttribute("WingLayer")) or 1
                local M = math.clamp((.08 + ((r / 10)) * .55) + ((u == 2 and .08 or 0)), 0, .95)
                j.Color = q:Lerp(c, M)
            elseif string.find(r, "spine", 1, true) then j.Color = q:Lerp(c, .35) end
        elseif j:IsA("ParticleEmitter") and j.Name == "WourldAngelDust" then j.Color = ColorSequence.new(q:Lerp(c, .3), c) end end
end

function ensureAngelAuraModel()
    local q = b.Character
    local c = q and ((q:FindFirstChild("UpperTorso") or q:FindFirstChild("Torso") or q:FindFirstChild("HumanoidRootPart")))
    if not q or not c then return nil end
    if angelAuraModel and (angelAuraModel.Parent == q and (angelLeftWeld and angelRightWeld)) then return c end
    if angelAuraModel and angelAuraModel.Parent then angelAuraModel:Destroy() end
    angelAuraModel = Instance.new("Model")
    angelAuraModel.Name = "WourldAngelAura"
    angelAuraModel.Parent = q
    local r = wN.angelWingColor or Color3.fromRGB(190, 235, 255)
    local j = wN.angelWingAccentColor or Color3.fromRGB(255, 255, 255)
    local u = math.max(.4, tonumber(wN.angelWingOffsetX) or 1.4)
    local M = tonumber(wN.angelWingOffsetY) or .5
    local G = tonumber(wN.angelWingOffsetZ) or .7
    local d = createAngelWingPart(angelAuraModel, "LeftWing", r, false)
    local z = createAngelWingPart(angelAuraModel, "RightWing", r, true)
    angelLeftWeld = Instance.new("Weld")
    angelLeftWeld.Part0 = c
    angelLeftWeld.Part1 = d
    angelLeftWeld.C0 = CFrame.new(-u, M, G) * CFrame.Angles(0, math.rad(-28), math.rad(-18))
    angelLeftWeld.Parent = d
    angelRightWeld = Instance.new("Weld")
    angelRightWeld.Part0 = c
    angelRightWeld.Part1 = z
    angelRightWeld.C0 = CFrame.new(u, M, G) * CFrame.Angles(0, math.rad(28), math.rad(18))
    angelRightWeld.Parent = z
    local Y = Instance.new("ParticleEmitter")
    Y.Name = "WourldAngelDust"
    Y.Texture = "rbxassetid://243098098"
    Y.Lifetime = NumberRange.new(.45, .9)
    Y.Speed = NumberRange.new(1, 4)
    Y.Rate = 22
    Y.Size = NumberSequence.new({ NumberSequenceKeypoint.new(0, .25), NumberSequenceKeypoint.new(1, 0) })
    Y.Transparency = NumberSequence.new({ NumberSequenceKeypoint.new(0, .2), NumberSequenceKeypoint.new(1, 1) })
    Y.Color = ColorSequence.new(r:Lerp(j, .3), j)
    Y.Parent = c
    refreshAngelWingPalette()
    return c
end

function startAngelAuraLoop()
    clearAngelAura()
    angelAuraEnabled = true
    angelAuraTask = task.spawn(function()
        local q = 0
        while angelAuraEnabled do
            local c = ensureAngelAuraModel()
            if c and (angelLeftWeld and angelRightWeld) then
                q = q + .2
                local c = math.sin(q) * 24
                local r = math.max(.4, tonumber(wN.angelWingOffsetX) or 1.4)
                local j = tonumber(wN.angelWingOffsetY) or .5
                local u = tonumber(wN.angelWingOffsetZ) or .7
                local M = math.rad(2 + math.cos(q * .5) * 2)
                angelLeftWeld.C0 = CFrame.new(-r, j, u) * CFrame.Angles(M, math.rad(-28 - c), math.rad(-18))
                angelRightWeld.C0 = CFrame.new(r, j, u) * CFrame.Angles(M, math.rad(28 + c), math.rad(18))
            end
            task.wait(.03)
        end
        clearAngelAura()
    end)
end

function fetchGitHubVisualDefaults()
    local q = "https://raw.githubusercontent.com/Kayy9961/Roblox-Universal-Korblox-Headless-System/main/Script.lua"
    local c, r = pcall(function() return game:HttpGet(q) end)
    if not c or type(r) ~= "string" then return end
    local j = r:match("global_mesh%s*=%s*\"(%d+)\"")
    local u = r:match("global_tex%s*=%s*\"(%d+)\"")
    if j and #j > 0 then githubKorbloxMeshId = j end
    if u and #u > 0 then githubKorbloxTextureId = u end
end

function resolveCustomAsset(q)
    local c = getcustomasset or getsynasset
    if not c then return nil end
    local r, j = pcall(function() return c(q) end)
    if r then return j end
    return nil
end

function ensureGitHubImageAsset(q, c)
    if not ((writefile and (isfile and (makefolder and isfolder)))) then return nil end
    local r = "WourldAssets"
    if not isfolder(r) then pcall(function() makefolder(r) end) end
    local j = r .. ("/" .. c)
    if not isfile(j) then
        local c, r = pcall(function() return game:HttpGet(q) end)
        if c and (type(r) == "string" and #r > 0) then pcall(function() writefile(j, r) end) end
    end
    if isfile(j) then return resolveCustomAsset(j) end
    return nil
end

function clearGitHubCosmetics()
    local q = b.Character
    if not q then return end
    for q, c in ipairs(q:GetChildren()) do if c.Name == "WourldGitHubKatana" or c.Name == "WourldGitHubHat" then c
                :Destroy() end end
    local c = q:FindFirstChild("RightUpperLeg")
    if c then
        local q = c:FindFirstChild("WourldGitHubKorbloxLeg")
        if q then q:Destroy() end
    end
end

function applyGitHubHeadlessVisual(q)
    local c = q and q:FindFirstChild("Head")
    if not c then return end
    c.Transparency = githubHeadlessVisualActive and 1 or 0
    local r = c:FindFirstChildOfClass("Decal")
    if r then r.Transparency = githubHeadlessVisualActive and 1 or 0 end
end

function applyGitHubKorbloxVisual(q)
    local c = q and q:FindFirstChild("RightUpperLeg")
    if not c then return end
    local r = { "RightUpperLeg", "RightLowerLeg", "RightFoot" }
    for c, r in ipairs(r) do
        local j = q:FindFirstChild(r)
        if j then j.Transparency = githubKorbloxVisualActive and 1 or 0 end
    end
    local j = c:FindFirstChild("WourldGitHubKorbloxLeg")
    if not githubKorbloxVisualActive then
        if j then j:Destroy() end
        return
    end
    if not j then
        j = Instance.new("Part")
        j.Name = "WourldGitHubKorbloxLeg"
        j.Size = Vector3.new(1, 1, 1)
        j.CanCollide = false
        j.Massless = true
        j.Parent = c
        local q = Instance.new("SpecialMesh")
        q.Parent = j
        local r = Instance.new("Weld")
        r.Parent = j
    end
    local u = j:FindFirstChildOfClass("SpecialMesh")
    local M = j:FindFirstChildOfClass("Weld")
    if u and M then
        u.MeshId = "rbxassetid://" .. tostring(githubKorbloxMeshId)
        u.TextureId = "rbxassetid://" .. tostring(githubKorbloxTextureId)
        local q = c.Size.Y / 1.217
        u.Scale = Vector3.new(q, q, q)
        M.Part0 = j
        M.Part1 = c
        M.C0 = CFrame.new(0, -((c.Size.Y / 2)) + .2, 0)
    end
end

function applyGitHubKatanaVisual(q)
    local c = q and q:FindFirstChild("Head")
    if not c then return end
    local r = q:FindFirstChild("WourldGitHubKatana")
    if not githubKatanaHeadActive then
        if r then r:Destroy() end
        return
    end
    if not r then
        r = Instance.new("Model")
        r.Name = "WourldGitHubKatana"
        r.Parent = q
        local j = Instance.new("Part")
        j.Name = "Blade"
        j.Size = Vector3.new(.16, 2.8, .16)
        j.Color = Color3.fromRGB(230, 236, 255)
        j.Material = Enum.Material.Neon
        j.CanCollide = false
        j.Massless = true
        j.Parent = r
        local u = Instance.new("Part")
        u.Name = "Guard"
        u.Size = Vector3.new(.5, .1, .5)
        u.Color = Color3.fromRGB(30, 30, 30)
        u.Material = Enum.Material.Metal
        u.CanCollide = false
        u.Massless = true
        u.Parent = r
        local M = Instance.new("Part")
        M.Name = "Handle"
        M.Size = Vector3.new(.2, .75, .2)
        M.Color = Color3.fromRGB(45, 25, 15)
        M.Material = Enum.Material.Wood
        M.CanCollide = false
        M.Massless = true
        M.Parent = r
        local G = Instance.new("WeldConstraint")
        G.Part0 = j
        G.Part1 = u
        G.Parent = j
        local d = Instance.new("WeldConstraint")
        d.Part0 = M
        d.Part1 = u
        d.Parent = M
        local z = Instance.new("Part")
        z.Name = "Root"
        z.Size = Vector3.new(.2, .2, .2)
        z.Transparency = 1
        z.CanCollide = false
        z.Massless = true
        z.Parent = r
        local Y = Instance.new("Weld")
        Y.Name = "RootWeld"
        Y.Part0 = z
        Y.Part1 = c
        Y.C0 = CFrame.new(.9, .12, -0.15) * CFrame.Angles(0, 0, math.rad(34))
        Y.Parent = z
        local P = Instance.new("WeldConstraint")
        P.Part0 = u
        P.Part1 = z
        P.Parent = u
        j.CFrame = z.CFrame * CFrame.new(0, 1.45, 0)
        u.CFrame = z.CFrame
        M.CFrame = z.CFrame * CFrame.new(0, -0.42, 0)
        local O = ensureGitHubImageAsset("https://raw.githubusercontent.com/DollarAlchemy/KatanaModel/main/katana.jpg",
            "github_katana.jpg")
        if O then
            local q = Instance.new("Decal")
            q.Face = Enum.NormalId.Front
            q.Texture = O
            q.Parent = j
            local c = Instance.new("Decal")
            c.Face = Enum.NormalId.Back
            c.Texture = O
            c.Parent = j
        end
    end
end

function applyGitHubHatVisual(q)
    local c = q and q:FindFirstChild("Head")
    if not c then return end
    local r = q:FindFirstChild("WourldGitHubHat")
    if not githubHatHeadActive then
        if r then r:Destroy() end
        return
    end
    if not r then
        r = Instance.new("Model")
        r.Name = "WourldGitHubHat"
        r.Parent = q
        local j = Instance.new("Part")
        j.Name = "Brim"
        j.Shape = Enum.PartType.Cylinder
        j.Size = Vector3.new(1.8, .12, 1.8)
        j.Color = Color3.fromRGB(21, 21, 26)
        j.Material = Enum.Material.SmoothPlastic
        j.CanCollide = false
        j.Massless = true
        j.Parent = r
        local u = Instance.new("Part")
        u.Name = "Crown"
        u.Size = Vector3.new(1.05, .5, 1.05)
        u.Color = Color3.fromRGB(28, 28, 34)
        u.Material = Enum.Material.SmoothPlastic
        u.CanCollide = false
        u.Massless = true
        u.Parent = r
        local M = Instance.new("Part")
        M.Name = "Root"
        M.Size = Vector3.new(.2, .2, .2)
        M.Transparency = 1
        M.CanCollide = false
        M.Massless = true
        M.Parent = r
        local G = Instance.new("Weld")
        G.Name = "RootWeld"
        G.Part0 = M
        G.Part1 = c
        G.C0 = CFrame.new(0, .62, 0)
        G.Parent = M
        local d = Instance.new("WeldConstraint")
        d.Part0 = j
        d.Part1 = M
        d.Parent = j
        local z = Instance.new("WeldConstraint")
        z.Part0 = u
        z.Part1 = M
        z.Parent = u
        j.CFrame = M.CFrame * CFrame.Angles(0, 0, math.rad(90))
        u.CFrame = M.CFrame * CFrame.new(0, .25, 0)
        local Y = ensureGitHubImageAsset(
        "https://raw.githubusercontent.com/hfg-gmuend/openmoji/master/color/618x618/1F3A9.png", "github_hat.png")
        if Y then
            local q = Instance.new("Texture")
            q.Face = Enum.NormalId.Top
            q.Texture = Y
            q.StudsPerTileU = 1.3
            q.StudsPerTileV = 1.3
            q.Parent = j
        end
    end
end

function applyGitHubVisualPack()
    local q = b.Character
    if not q then return end
    applyGitHubHeadlessVisual(q)
    applyGitHubKorbloxVisual(q)
    applyGitHubKatanaVisual(q)
    applyGitHubHatVisual(q)
end

function refreshGitHubVisualHooks()
    if githubVisualCharacterConnection then
        githubVisualCharacterConnection:Disconnect()
        githubVisualCharacterConnection = nil
    end
    if githubVisualLoopConnection then
        githubVisualLoopConnection:Disconnect()
        githubVisualLoopConnection = nil
    end
    if githubHeadlessVisualActive or githubKorbloxVisualActive or githubKatanaHeadActive or githubHatHeadActive then
        githubVisualCharacterConnection = b.CharacterAdded:Connect(function(q)
            task.wait(.8)
            if q and q.Parent then applyGitHubVisualPack() end
        end)
        applyGitHubVisualPack()
    else
        clearGitHubCosmetics()
        applyGitHubHeadlessVisual(b.Character)
    end
    if githubVisualSpinActive and githubKatanaHeadActive then githubVisualLoopConnection = z.Heartbeat:Connect(function(
            q)
            local c = b.Character
            local r = c and c:FindFirstChild("WourldGitHubKatana")
            local j = r and r:FindFirstChild("Root")
            local u = j and j:FindFirstChild("RootWeld")
            if u then
                githubVisualAngle = ((githubVisualAngle + githubVisualSpinSpeed * q)) % ((math.pi * 2))
                u.C0 = CFrame.new(.9, .12, -0.15) * CFrame.Angles(0, githubVisualAngle, math.rad(34))
            end
        end) end
end

function AntiOwnershipFunction()
    local q = d.CharacterEvents.Struggle
    while antiOwnershipActive do
        local c = b.Character
        if c and c:FindFirstChild("Head") then
            local r = c.Head
            if r:FindFirstChild("PartOwner") then
                q:FireServer(b)
                for q, c in pairs(c:GetChildren()) do if c:IsA("BasePart") then c.Anchored = true end end
                local r = b:FindFirstChild("IsHeld")
                while r and (r.Value and antiOwnershipActive) do task.wait() end
                for q, c in pairs(c:GetChildren()) do if c:IsA("BasePart") then c.Anchored = false end end
            end
        end
        task.wait(.1)
    end
end

function LoopTPFunction()
    local q = game.Players.LocalPlayer
    while loopTPActive do
        local c = q.Character
        if c then
            local q = c:FindFirstChild("HumanoidRootPart")
            local r = c:FindFirstChildOfClass("Humanoid")
            if q and r then
                r.PlatformStand = true
                local c = math.random(-500, 500)
                local j = math.random(30, 480)
                local u = math.random(-500, 500)
                q.CFrame = CFrame.new(c, j, u)
            end
        end
        task.wait(.03)
    end
    local c = q.Character
    if c then
        local q = c:FindFirstChildOfClass("Humanoid")
        if q then q.PlatformStand = false end
    end
end

function StartAntiExplosion()
    local q = b.Character
    if not q then return end
    local c = q:WaitForChild("HumanoidRootPart")
    YN = workspace.ChildAdded:Connect(function(r) if r.Name == "Part" and antiExplosionActive then
            local j = ((r.Position - c.Position)).Magnitude
            if j <= 20 then
                c.Anchored = true
                task.wait(.01)
                local r = q:FindFirstChild("Right Arm")
                if r then
                    local q = r:FindFirstChild("RagdollLimbPart")
                    if q then while q.CanCollide and antiExplosionActive do task.wait(.001) end end
                end
                if antiExplosionActive then c.Anchored = false end
            end
        end end)
end

function StartAntiBurn()
    local q = b.Character
    if not q then return end
    local c = q:WaitForChild("Humanoid")
    local r = q:WaitForChild("HumanoidRootPart")
    q.PrimaryPart = r
    PN = c.FireDebounce.Changed:Connect(function(j) if j and antiBurnActive then
            local j = q
            local u = r.CFrame
            local M = workspace:FindFirstChild("Plots")
            if M and M:FindFirstChild("Plot2") then
                local q = M.Plot2
                local r = q:FindFirstChild("Barrier")
                local G = r and r:FindFirstChild("PlotBarrier")
                if G and G:IsA("BasePart") then
                    local q = G.CFrame * CFrame.new(0, 6, 0)
                    j:SetPrimaryPartCFrame(q)
                    task.wait(.3)
                    local r = j:FindFirstChild("FirePlayerPart", true)
                    if r then
                        for q, c in ipairs(r:GetChildren()) do
                            if c:IsA("Sound") then c:Stop() end
                            if c:IsA("Light") or c:IsA("ParticleEmitter") then c.Enabled = false end
                        end
                        if r:FindFirstChild("CanBurn") then r.CanBurn.Value = false end
                        if c:FindFirstChild("FireDebounce") then c.FireDebounce.Value = false end
                    end
                    task.wait(.6)
                    if j and (j.PrimaryPart and antiBurnActive) then j:SetPrimaryPartCFrame(u) end
                end
            end
        end end)
end

function StartAntiVoid()
    Y.FallenPartsDestroyHeight = -1000
    ON = z.Heartbeat:Connect(function()
        if not antiVoidActive and not _G.AntiVoid then return end
        local q = b.Character
        local c = q and ((q:FindFirstChild("HumanoidRootPart") or q.PrimaryPart))
        if c then if c.Position.Y < -800 then
                q:SetPrimaryPartCFrame(CFrame.new(0, 0, 0))
                c.AssemblyLinearVelocity = Vector3.zero
                antivoidmesssage()
            end end
    end)
end

function FWC(q, c, r) return q:FindFirstChild(c) or q:WaitForChild(c, r or 3) end

function grab(q)
    if not q or not q.CFrame then return end
    d.GrabEvents.SetNetworkOwner:FireServer(q, q.CFrame)
end

function toy_spawn_gucci(q, c, r)
    local j = d.MenuToys.SpawnToyRemoteFunction
    local u = b:FindFirstChild("InPlot") or b:WaitForChild("InPlot", 3)
    local M = b:FindFirstChild("InOwnedPlot") or b:WaitForChild("InOwnedPlot", 3)
    local G = b:FindFirstChild("CanSpawnToy") or b:WaitForChild("CanSpawnToy", 3)
    if not j or not u or not M or not G then return false end
    local z = tick()
    while tick() - z <= 3.5 do
        local q = G.Value and true or false
        local c = u.Value and (not M.Value)
        if q and not c then break end
        task.wait(.05)
    end
    if not G.Value then return false end
    task.spawn(function() j:InvokeServer(q, c, r or Vector3.new()) end)
    local P = Y:FindFirstChild(b.Name .. "SpawnedInToys")
    if not P then
        local q = tick()
        repeat
            P = Y:FindFirstChild(b.Name .. "SpawnedInToys")
            if P then break end
            task.wait(.05)
        until tick() - q > 2
    end
    if not P then return false end
    local O
    P.ChildAdded:Once(function(c) if c.Name == q and c:IsA("Model") then O = c end end)
    local a = tick()
    while not O do if tick() - a < 2 then task.wait(.01) else return false end end
    return O
end

function GucciAntiGrab()
    if _G.AutoSitBlobZ or _G.AutoSitBloom then disableAutoSitBloomWithNotify("Gucci mode") end
    gN.gucciKeyActive = true
    gucciRunId = gucciRunId + 1
    local q = gucciRunId
    local c = b.Character or b.CharacterAdded:Wait()
    local r = FWC(c, "Humanoid")
    if not c or not r then
        releaseGucciGrabState("Gucci init failed", true)
        return
    end
    r.Sit = true
    task.wait(.02)
    r.Sit = false
    task.wait(.02)
    task.spawn(function()
        local q = tick()
        while tick() - q < .8 do
            for q, c in pairs(c:GetChildren()) do if c:IsA("BasePart") then c.Velocity = Vector3.new() end end
            task.wait(.01)
        end
    end)
    local j, u, M, G = true, false, nil, nil
    task.spawn(function()
        while not M and q == gucciRunId do task.wait(.01) end
        if q ~= gucciRunId then return end
        G = FWC(M, "Head")
        local c = FWC(M, "GrabbableHitbox")
        while q == gucciRunId and (G and ((not G:FindFirstChild("PartOwner") or G.PartOwner.Value ~= b.Name))) do
            grab(c)
            task.wait(.01)
        end
    end)
    local z = FWC(c, "HumanoidRootPart")
    M = toy_spawn_gucci("CreatureBlobman", z.CFrame * CFrame.new(0, 0, -5), Vector3.new(0, -15.716, 0))
    if not M then
        releaseGucciGrabState("Gucci spawn failed, state recovered", false)
        return
    end
    local P = FWC(M, "VehicleSeat")
    task.defer(function()
        if (not c) or (not r) then return end
        local G = tick()
        while j and (q == gucciRunId and tick() - G < .3) do
            if M and M.Parent then if P and (P.Parent and P.Occupant ~= r) then P:Sit(r) end end
            task.wait(.03)
            if c and (r and r.Parent) then r:ChangeState(Enum.HumanoidStateType.Jumping) end
            task.wait(.03)
        end
        j = false
        u = false
    end)
    u = true
    task.defer(function() while u and q == gucciRunId do
            if c and (z and z.Parent) then d.CharacterEvents.RagdollRemote:FireServer(z, .095) end
            task.wait(.01)
        end end)
    local O
    task.wait(.4)
    if q ~= gucciRunId then return end
    r.Sit = false
    M.Name = "Gucci"
    local a = Y:FindFirstChild(b.Name .. "SpawnedInToys")
    if a then for q, c in pairs(a:GetChildren()) do if c.Name == "Gucci" then
                O = q
                break
            end end end
    for q, c in pairs(M:GetChildren()) do if c:IsA("BasePart") then
            c.CanCollide = false
            if c.Name == "GrabbableHitbox" or c.Name == "VehicleSeat" or c.Name == "Head" then
                c.CanTouch = true
                c.CanQuery = true
            end
        end end
    task.defer(function() while q == gucciRunId and (M and G) do
            G.CFrame = CFrame.new(G.Position.X, 100000.0, G.Position.Z)
            task.wait(.01)
        end end)
    local o, v = pcall(function() return b.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents end)
    if o and (v and O) then for q, c in ipairs(v:GetChildren()) do if c.Name == "CreatureBlobman" and q == O then
                local q = c.ViewItemButton
                q.Text = "GUCCI"
                q.TextScaled = true
                q.LowResImage.Image = ""
            end end end
    task.delay(4.6,
        function() if gN.gucciKeyActive and q == gucciRunId then releaseGucciGrabState("Gucci timeout, grab restored",
                    true) end end)
end

function IsLocalOwnedGucci(q)
    if not q or not q:IsA("Model") then return false end
    if q.Name ~= "CreatureBlobman" and q.Name ~= "Gucci" then return false end
    local c = q:FindFirstChild("Head")
    local r = c and c:FindFirstChild("PartOwner")
    return r and (r:IsA("StringValue") and r.Value == b.Name) or false
end

function ProtectSingleGucciModel(q)
    if not q or not q:IsA("Model") then return false end
    local c = q:FindFirstChild("Head")
    local r = q:FindFirstChild("GrabbableHitbox")
    local j = q:FindFirstChild("VehicleSeat") or q:FindFirstChildWhichIsA("VehicleSeat", true)
    local u = c and c:FindFirstChild("PartOwner")
    if r and r:IsA("BasePart") then
        pcall(function() grab(r) end)
        r.CanTouch = true
        r.CanQuery = true
    end
    if j and j:IsA("BasePart") then
        j.CanTouch = true
        j.CanQuery = true
    end
    if u and (u:IsA("StringValue") and u.Value ~= b.Name) then if r and r:IsA("BasePart") then pcall(function() grab(r) end) elseif c and c:IsA("BasePart") then
            pcall(function() grab(c) end) end end
    for q, c in ipairs(q:GetDescendants()) do if c:IsA("BasePart") then
            c.CanCollide = false
            c.CanTouch = true
            c.CanQuery = true
        end end
    local M = b.Character
    local G = M and M:FindFirstChild("HumanoidRootPart")
    local d = c or j or q.PrimaryPart or q:FindFirstChildWhichIsA("BasePart", true)
    if G and (d and d:IsA("BasePart")) then
        local c = ((d.Position - G.Position)).Magnitude
        if c > 140 then pcall(function() q:PivotTo(G.CFrame * CFrame.new(0, 2, -6)) end) end
    end
    return true
end

function StartGucciProtectLoop()
    gucciProtectSeenAny = false
    gucciProtectLastSeen = tick()
    while gucciProtectActive do
        local q = false
        local c = Y:FindFirstChild(b.Name .. "SpawnedInToys")
        if c then for c, r in ipairs(c:GetChildren()) do if IsLocalOwnedGucci(r) then q = ProtectSingleGucciModel(r) or q end end end
        if q then
            gucciProtectSeenAny = true
            gucciProtectLastSeen = tick()
        else
            local q = b.Character
            local c = q and q:FindFirstChildOfClass("Humanoid")
            local r = b:FindFirstChild("IsHeld")
            local j = false
            if r and r.Value then j = true end
            if c and c.SeatPart then j = true end
            if isPrimaryMouseHeld() then j = true end
            if gucciProtectSeenAny and (c and (c.Health > 0 and (not j and ((not c.SeatPart) and (tick() - gucciProtectLastSeen > 2.6 and tick() - gucciProtectLastRespawn > 8))))) then
                gucciProtectLastRespawn = tick()
                pcall(function() GucciAntiGrab() end)
            end
        end
        task.wait(.12)
    end
end

function destroyPlayerGucci(q)
    if not q or q == b then return false end
    local c = q.Name .. "SpawnedInToys"
    local r = Y:FindFirstChild(c)
    if not r then return false end
    for q, c in ipairs(r:GetChildren()) do if c.Name == "CreatureBlobman" then
            local q = c:FindFirstChild("VehicleSeat") or c:FindFirstChildWhichIsA("VehicleSeat", true)
            if q then
                local r = b.Character
                if not r then return false end
                local j = r:FindFirstChild("Humanoid")
                local u = r:FindFirstChild("HumanoidRootPart")
                if not j or not u then return false end
                local M = u.CFrame
                u.CFrame = q.CFrame
                u.Velocity = Vector3.zero
                q:Sit(j)
                task.wait(.3)
                if j.SeatPart == q then
                    j.Sit = false
                    task.wait(.1)
                    u.CFrame = M
                    task.wait(.5)
                    c:Destroy()
                    return true
                else u.CFrame = M end
            end
        end end
    return false
end

function StartDestroyGucciLoop() while destroyGucciActive do
        for q, c in ipairs(G:GetPlayers()) do if c ~= b and c.Character then destroyPlayerGucci(c) end end
        task.wait(2)
    end end

function ClearNearbyGucci(q)
    local c = b.Character
    local r = c and c:FindFirstChild("HumanoidRootPart")
    if not c or not r then return end
    if isLocalGrabBusy() then return end
    local j = b:FindFirstChild("IsHeld")
    local u = j and j.Value
    local M = tonumber(q) or 70
    local G = u or M <= 35
    local z = d:FindFirstChild("GrabEvents")
    local P = z and z:FindFirstChild("SetNetworkOwner")
    for q, c in ipairs(Y:GetChildren()) do if c:IsA("Folder") and c.Name:sub(-13) == "SpawnedInToys" then
            local q = c.Name:sub(1, #c.Name - 13)
            if q ~= b.Name then for q, c in ipairs(c:GetChildren()) do if c:IsA("Model") and ((c.Name == "CreatureBlobman" or c.Name == "Gucci")) then
                        local q = c:FindFirstChild("Head") or c:FindFirstChild("VehicleSeat") or
                        c:FindFirstChildWhichIsA("BasePart", true)
                        local j = false
                        if q and q:IsA("BasePart") then j = ((q.Position - r.Position)).Magnitude <= M else j = true end
                        if j and not IsLocalOwnedGucci(c) then
                            local j = c:FindFirstChild("Head")
                            local M = c:FindFirstChild("GrabbableHitbox")
                            local d = j and j:FindFirstChild("PartOwner")
                            if P then
                                local q = {}
                                for c, r in ipairs(c:GetDescendants()) do if r:IsA("BasePart") then q[#q + 1] = r end end
                                if #q == 0 then if M and M:IsA("BasePart") then q[1] = M elseif j and j:IsA("BasePart") then q[1] =
                                        j end end
                                for c = 1, math.min(10, #q), 1 do
                                    local r = q[c]
                                    pcall(function() P:FireServer(r, r.CFrame) end)
                                end
                            end
                            local z = d and (d:IsA("StringValue") and d.Value == b.Name)
                            if q and q:IsA("BasePart") then pcall(function()
                                    q.AssemblyAngularVelocity = Vector3.zero
                                    q.AssemblyLinearVelocity = Vector3.new(0,
                                        math.max(q.AssemblyLinearVelocity.Y, G and 240 or 140), 0)
                                    q.CFrame = r.CFrame * CFrame.new(0, G and 1000 or 450, -6)
                                end) end
                            if u then pcall(function()
                                    if q and q:IsA("BasePart") then q.CFrame = r.CFrame * CFrame.new(0, 1200, 0) end
                                    c:Destroy()
                                end) elseif z then pcall(function()
                                    if q and q:IsA("BasePart") then q.CFrame = r.CFrame * CFrame.new(0, 900, 0) end
                                    c:Destroy()
                                end) else
                                local q = c:FindFirstChild("VehicleSeat") or c:FindFirstChildWhichIsA("VehicleSeat", true)
                                if M and M:IsA("BasePart") then
                                    M.CanTouch = false
                                    M.CanQuery = false
                                    M.CanCollide = false
                                end
                                if q and q:IsA("BasePart") then
                                    q.CanTouch = false
                                    q.CanQuery = false
                                end
                                if G then pcall(function() c:Destroy() end) end
                            end
                        end
                    end end end
        end end
end

function StartAntiGucciLoop() while antiGucciActive do if isLocalGrabBusy() then task.wait(.28) else
            ClearNearbyGucci(antiGucciRadius)
            task.wait(.24)
        end end end

function RemoveAntiKickFunction(q)
    local c = d.GrabEvents.SetNetworkOwner
    local r = game.Players.LocalPlayer
    local function j(q, r) c:FireServer(q, r) end
    local function u(q)
        local c = q:FindFirstChild("SoundPart")
        if c then
            j(c, c.CFrame)
            if c:FindFirstChild("PartOwner") and c.PartOwner.Value == r.Name then c.CFrame = CFrame.new(0, 1000, 0) end
        end
    end
    while antiAntiKickActive do
        local c = game.Players:FindFirstChild(q)
        if c and c ~= r then
            local q = workspace:FindFirstChild(c.Name .. "SpawnedInToys")
            if q then
                if q:FindFirstChild("NinjaKunai") then u(q.NinjaKunai) end
                if q:FindFirstChild("NinjaShuriken") then u(q.NinjaShuriken) end
                if q:FindFirstChild("AntiKick") then u(q.AntiKick) end
            end
        end
        task.wait(.1)
    end
end

function getMenuToyRemotes()
    local q = d:FindFirstChild("MenuToys")
    if not q then return nil, nil end
    local c = q:FindFirstChild("SpawnToyRemoteFunction")
    local r = q:FindFirstChild("DestroyToy")
    return c, r
end

function destroyOwnedToyByName(q)
    local c, r = getMenuToyRemotes()
    for c, j in ipairs(getLocalToyContainers()) do for c, j in ipairs(j:GetChildren()) do if j.Name == q then
                if r then pcall(function() r:FireServer(j) end) end
                if j.Parent then pcall(function() j:Destroy() end) end
            end end end
end

function spawnOwnedToyByName(q, c)
    local r = select(1, getMenuToyRemotes())
    if not r then return nil end
    local j = {}
    for q, c in ipairs(getLocalToyContainers()) do for q, c in ipairs(c:GetChildren()) do j[c] = true end end
    local u = b.Character
    local M = u and u:FindFirstChild("HumanoidRootPart")
    local G = M and (M.CFrame * ((c or CFrame.new(0, 12, 16)))) or CFrame.new(0, 40, 0)
    pcall(function() r:InvokeServer(q, G, Vector3.zero) end)
    local d = tick() + 2.6
    repeat
        local c = nil
        for r, u in ipairs(getLocalToyContainers()) do
            local M = u:FindFirstChild(q)
            if M and not j[M] then return M end
            if M and not c then c = M end
            for c, r in ipairs(u:GetChildren()) do if (not j[r]) and r.Name == q then return r end end
        end
        if c then return c end
        task.wait(.05)
    until tick() >= d
    return findLocalToyByName(q)
end

function setModelCollisionState(q, c)
    if not q then return end
    for q, r in ipairs(q:GetDescendants()) do if r:IsA("BasePart") then r.CanCollide = c and true or false end end
end

function stopModelVelocity(q)
    if not q then return end
    for q, c in ipairs(q:GetDescendants()) do if c:IsA("BasePart") then
            c.AssemblyLinearVelocity = Vector3.zero
            c.AssemblyAngularVelocity = Vector3.zero
        end end
end

function getToyControlPart(q)
    if not q then return nil end
    return q:FindFirstChild("SoundPart") or q:FindFirstChild("HoldPart") or q.PrimaryPart or
    q:FindFirstChildWhichIsA("BasePart", true)
end

function claimToyOwnership(q)
    local c = getToyControlPart(q)
    local r = d:FindFirstChild("GrabEvents")
    local j = r and r:FindFirstChild("SetNetworkOwner")
    if not ((c and j)) then return false end
    for q = 1, 2, 1 do pcall(function() j:FireServer(c, c.CFrame) end) end
    return true
end

function tryConsumeHeldToy(q, c)
    if not ((q and c)) then return false end
    local r = false
    for j, u in ipairs(q:GetDescendants()) do
        local M = string.lower(tostring(u.Name or ""))
        local G = M:find("eat", 1, true) or M:find("consume", 1, true) or M:find("use", 1, true) or
        M:find("activate", 1, true)
        if G and u:IsA("RemoteFunction") then
            pcall(function() u:InvokeServer(q, c) end)
            r = true
        elseif G and u:IsA("RemoteEvent") then
            pcall(function() u:FireServer(q, c) end)
            r = true
        end
    end
    local j = c:FindFirstChildOfClass("Tool")
    if not j then
        local q = b:FindFirstChildOfClass("Backpack")
        j = q and q:FindFirstChildOfClass("Tool")
    end
    if j and ((j.Name == q.Name or string.sub(tostring(q.Name), 1, 4) == "Food")) then
        pcall(function() j:Activate() end)
        r = true
    end
    return r
end

function getAntiInputLagPrimaryItem()
    local q = getAntiInputLagItemCandidates()
    return q[1] or "FoodHamburger"
end

function getAntiInputLagItemCandidates() return getAntiInputLagItemCandidatesByFilter(_G.WourldAntiInputLagItemFilter or
    "All") end

function getAntiInputLagInterval()
    local q = math.clamp(tonumber(s) or .3, .1, 3)
    local c = u and u.AntiInputLagSpeed
    if c and c.Value ~= nil then q = math.clamp(tonumber(c.Value) or q, .1, 3) end
    s = q
    return q
end

function bumpAntiInputLagRevision()
    local q = tonumber(gN.antiInputLagPulseRevision) or 0
    q = q + 1
    if q > 1000000 then q = 1 end
    gN.antiInputLagPulseRevision = q
end

function waitAntiInputLagInterval(q)
    local c = tonumber(gN.antiInputLagPulseRevision) or 0
    local r = math.max(.01, tonumber(q) or .3)
    while r > 0 do
        if ((tonumber(gN.antiInputLagPulseRevision) or 0)) ~= c then return end
        local q = math.min(.08, r)
        task.wait(q)
        r = r - q
    end
end

function isAntiInputLagPaused()
    if tick() < ((n or 0)) then return true end
    if tick() < ((tonumber(gN.antiInputLagManualUntil) or 0)) then return true end
    if isLocalGrabBusy and isLocalGrabBusy() then return true end
    if gN and gN.gucciKeyActive then return true end
    if antiGucciActive or gucciProtectActive or destroyGucciActive then return true end
    if (_G and _G.ShurikenAntiKick) or q6 then return true end
    return false
end

function forceReleaseHeldItems(q)
    local c = b.Character
    local r = c and c:FindFirstChild("HumanoidRootPart")
    local j = getLocalToyFolder()
    if not ((c and (r and j))) then return end
    local u = tonumber(q) or 1600
    local M = CFrame.new(r.Position + Vector3.new(0, -math.abs(u), 0))
    for q, r in ipairs(j:GetChildren()) do
        local j = r and r:FindFirstChild("HoldPart")
        local u = j and j:FindFirstChild("HoldItemRemoteFunction")
        local G = j and j:FindFirstChild("DropItemRemoteFunction")
        if u and G then
            pcall(function() u:InvokeServer(r, c) end)
            pcall(function() G:InvokeServer(r, M, Vector3.zero) end)
        end
    end
end

function StartAntiInputLagBurgerLoop()
    local q = game:GetService("Players")
    local c = game:GetService("ReplicatedStorage")
    local r = game:GetService("Workspace")
    local j = game:GetService("RunService")
    local u = q.LocalPlayer
    local M = (c:WaitForChild("MenuToys")):WaitForChild("SpawnToyRemoteFunction")
    local function G()
        local q = {}
        local c = {}
        local function r(r)
            local j = tostring(r or "")
            if j == "" then return end
            local u = string.lower(j)
            if c[u] then return end
            c[u] = true
            q[#q + 1] = j
        end
        for q, c in ipairs(getAntiInputLagItemCandidatesByFilter(_G.WourldAntiInputLagItemFilter or "All")) do r(c) end
        if uN then for q, c in ipairs(getAntiInputLagItemCandidatesByFilter(_G.WourldAntiInputLagItemFilterSecondary or "FoodBread")) do
                r(c) end end
        if #q == 0 then r("FoodHamburger") end
        return q
    end
    while qN do
        local q = getAntiInputLagInterval()
        if isAntiInputLagPaused() then
            waitAntiInputLagInterval(math.clamp(q * .4, .03, .8))
            continue
        end
        local c = u.Character or u.CharacterAdded:Wait()
        local r = c and c:FindFirstChildOfClass("Humanoid")
        local d = c and c:FindFirstChild("HumanoidRootPart")
        if not c or not r or r.Health <= 0 or not d then
            waitAntiInputLagInterval(math.clamp(q * .5, .05, .9))
            continue
        end
        local z = G()
        for r, G in ipairs(z) do
            if not qN or isAntiInputLagPaused() then break end
            local z = select(1, findLocalToyByName(G))
            if not z then
                pcall(function() M:InvokeServer(G, d.CFrame * CFrame.new(0, 5, 0), Vector3.zero) end)
                local q = tick()
                repeat
                    j.Heartbeat:Wait()
                    z = select(1, findLocalToyByName(G))
                until z or tick() - q > 1 or (not qN)
            end
            if z and z.Parent then
                local r = z:FindFirstChild("HoldPart")
                local j = r and r:FindFirstChild("HoldItemRemoteFunction")
                local M = r and r:FindFirstChild("DropItemRemoteFunction")
                if r and (j and M) then
                    local G = r:FindFirstChild("HoldingPlayer")
                    G = G and G.Value
                    if G and G ~= u then
                        pcall(function() M:InvokeServer(z, d.CFrame * CFrame.new(0, 2000, 0), Vector3.zero) end)
                        pcall(function() z:Destroy() end)
                    else
                        pcall(function() j:InvokeServer(z, c) end)
                        task.wait(math.clamp(q * .18, .01, .06))
                        pcall(function() M:InvokeServer(z, CFrame.new(d.Position + Vector3.new(0, -2000, 0)),
                                Vector3.zero) end)
                        task.wait(math.clamp(q * .06, .01, .03))
                    end
                end
            end
        end
        waitAntiInputLagInterval(math.clamp(q * .5, .03, .4))
    end
end

function StartAntiInputLagTestLoop()
    local q = d:FindFirstChild("CharacterEvents") and d.CharacterEvents:FindFirstChild("RagdollRemote")
    while rN do
        local c = getAntiInputLagInterval()
        if isAntiInputLagPaused() then
            waitAntiInputLagInterval(math.clamp(c * .4, .03, .8))
            continue
        end
        local r = b.Character
        local j = r and r:FindFirstChild("HumanoidRootPart")
        local u = b:FindFirstChild("IsHeld")
        local M = u and u.Value
        local G = true
        if j then
            pcall(function() G = j:GetNetworkOwner() == b end)
            if (not G) and ((not M) and q) then pcall(function() q:FireServer(j, 0) end) end
        end
        waitAntiInputLagInterval(c)
    end
end

function RunBreadAntiKick()
    GN = GN + 1
    local q = GN
    if MN then
        task.cancel(MN)
        MN = nil
    end
    local c = b.Character
    local r = c and c:FindFirstChild("HumanoidRootPart")
    if not r then return false end
    local j = r:FindFirstChild("FirePlayerPart") or r
    local u = spawnOwnedToyByName("FoodBread", CFrame.new(0, 10, 16))
    local M = u and ((u:FindFirstChild("SoundPart") or u:FindFirstChildWhichIsA("BasePart")))
    local G = d:FindFirstChild("GrabEvents")
    local z = G and G:FindFirstChild("SetNetworkOwner")
    if not u or not M or not z then return false end
    local Y = tick() + 2
    repeat
        pcall(function() z:FireServer(M, M.CFrame) end)
        task.wait(.03)
    until tick() >= Y or (M:FindFirstChild("PartOwner") and M.PartOwner.Value == b.Name)
    setModelCollisionState(u, false)
    MN = task.spawn(function() while q == GN and (u and (u.Parent and (M and M.Parent))) do
            if not j.Parent then
                local q = b.Character
                local c = q and q:FindFirstChild("HumanoidRootPart")
                j = (c and ((c:FindFirstChild("FirePlayerPart") or c))) or j
            end
            pcall(function() M.CFrame = j.CFrame * CFrame.Angles(math.rad(90), 0, 0) end)
            stopModelVelocity(u)
            task.wait()
        end end)
    return true
end

function setRemoveAllAntiInputState(q)
    antiAntiLagEnabled = q and true or false
    bumpAntiInputLagRevision()
    if antiAntiLagEnabled then
        if vN then
            task.cancel(vN)
            vN = nil
        end
        vN = task.spawn(RemoveAllAntiInputFunction)
    else if vN then
            task.cancel(vN)
            vN = nil
        end end
end

function setAntiInputLagBurgerState(q)
    qN = q and true or false
    bumpAntiInputLagRevision()
    if cN then
        task.cancel(cN)
        cN = nil
    end
    if qN then cN = task.spawn(StartAntiInputLagBurgerLoop) else
        local q = { tostring(_G.WourldAntiInputLagItemFilter or "All") }
        if uN then q[#q + 1] = tostring(_G.WourldAntiInputLagItemFilterSecondary or "FoodBread") end
        for q, c in ipairs(q) do for q, c in ipairs(getAntiInputLagItemCandidatesByFilter(c)) do destroyOwnedToyByName(c) end end
    end
end

function setAntiInputLagTestState(q)
    rN = q and true or false
    bumpAntiInputLagRevision()
    if jN then
        task.cancel(jN)
        jN = nil
    end
    if rN then jN = task.spawn(StartAntiInputLagTestLoop) end
end

function setAntiInputLagItemFilter(q)
    local c = tostring(q or "All")
    if c == "" then c = "All" end
    _G.WourldAntiInputLagItemFilter = c
    bumpAntiInputLagRevision()
end

function antiInputLagItemMatches(q, c)
    local r = tostring(q or "")
    local j = tostring(_G.WourldAntiInputLagItemFilter or "All")
    if j == "" or j == "All" then return c and true or false end
    if j == "Food (All)" then return string.sub(r, 1, 4) == "Food" end
    if j == "Instruments (All)" then return string.sub(r, 1, 10) == "Instrument" end
    if j == "Cups (All)" then return string.sub(r, 1, 3) == "Cup" end
    if j == "Poop (All)" then return string.sub(r, 1, 4) == "Poop" end
    return r == j
end

function RemoveAllAntiInputFunction()
    local function q(q)
        if not q or not q:IsA("Model") then return false end
        local c = q:FindFirstChild("HoldPart")
        if not c then return false end
        local r = c:FindFirstChild("HoldItemRemoteFunction")
        local j = c:FindFirstChild("DropItemRemoteFunction")
        local u = (r ~= nil and j ~= nil)
        if not u then return false end
        return antiInputLagItemMatches(q.Name, u)
    end
    local c = {}
    local r = {}
    local function j(j)
        if not j or r[j] then return end
        if q(j) then
            r[j] = true
            table.insert(c, j)
        end
    end
    local u = workspace.DescendantAdded:Connect(function(q) j(q) end)
    for q, c in ipairs(workspace:GetDescendants()) do j(c) end
    while antiAntiLagEnabled do
        local q = getAntiInputLagInterval()
        if isAntiInputLagPaused() then
            waitAntiInputLagInterval(math.min(.14, math.max(.05, q * .32)))
            continue
        end
        local j = b.Character
        local u = j and j:FindFirstChild("HumanoidRootPart")
        if not j or not u then
            waitAntiInputLagInterval(math.clamp(q * .45, .05, .9))
            continue
        end
        for M = #c, 1, -1 do
            if isAntiInputLagPaused() then break end
            local G = c[M]
            if not G or not G.Parent or not G:FindFirstChild("HoldPart") then
                r[G] = nil
                table.remove(c, M)
            else
                local d = G:FindFirstChild("HoldPart")
                local z = d and d:FindFirstChild("HoldItemRemoteFunction")
                local Y = d and d:FindFirstChild("DropItemRemoteFunction")
                local P = (z ~= nil and Y ~= nil)
                if not P or not antiInputLagItemMatches(G.Name, P) then
                    r[G] = nil
                    table.remove(c, M)
                else
                    if isAntiInputLagPaused() then break end
                    pcall(function() z:InvokeServer(G, j) end)
                    waitAntiInputLagInterval(math.clamp(q * .22, .02, .9))
                    if isAntiInputLagPaused() then break end
                    pcall(function() Y:InvokeServer(G, CFrame.new(u.Position + Vector3.new(0, -2000, 0)),
                            Vector3.new(0, 0, 0)) end)
                end
            end
        end
        waitAntiInputLagInterval(q)
    end
    u:Disconnect()
end

function RemoveAntiKickAuraFunction()
    local q = game:GetService("ReplicatedStorage")
    local c = game:GetService("Players")
    local r = c.LocalPlayer
    local j = q:WaitForChild("GrabEvents")
    local u = j:WaitForChild("SetNetworkOwner")
    while removeAntiKickAuraActive do
        local q = r.Character
        local j = q and q:FindFirstChild("HumanoidRootPart")
        if not j then
            task.wait(.1)
            continue
        end
        for q, c in ipairs(c:GetPlayers()) do if c ~= r then
                if useWhitelistRemoveAntiKick and r:IsFriendsWith(c.UserId) then continue end
                local q = c.Character
                local M = q and q:FindFirstChild("HumanoidRootPart")
                if not M then continue end
                if ((M.Position - j.Position)).Magnitude <= removeAntiKickRadius then
                    local q = workspace:FindFirstChild(c.Name .. "SpawnedInToys")
                    if q then for c, j in ipairs(getRemoveAntiKickAuraToyNames()) do
                            local M = q:FindFirstChild(j)
                            if M then
                                local q = M:FindFirstChild("SoundPart")
                                if q then
                                    pcall(function() u:FireServer(q, q.CFrame) end)
                                    if q:FindFirstChild("PartOwner") and q.PartOwner.Value == r.Name then q.CFrame =
                                        CFrame.new(0, 1000, 0) end
                                end
                            end
                        end end
                end
            end end
        task.wait(.1)
    end
    gN.removeAntiKickAuraTask = nil
end

function OwnershipKickFunction(q)
    local r = G:FindFirstChild(q)
    if not r then return end
    if IsPlayerInHouse(r) then
        ownershipKickActive = false
        c:Notify({ Title = "Wourld Hub", Description = GetHouseBlockedMessage(), Duration = 3 })
        playEventSound("kick")
        task.defer(function() if M and (M.OwnershipKickToggle and M.OwnershipKickToggle.Value) then M
                    .OwnershipKickToggle:SetValue(false) end end)
        return
    end
    local j = d:WaitForChild("GrabEvents")
    local u = b.Character
    local Y = u and u:FindFirstChild("HumanoidRootPart")
    local P = u and u:FindFirstChildOfClass("Humanoid")
    if not Y or not P or P.Health <= 0 then return end
    local O = Y.CFrame
    kickActionBusy = true
    kickSelfGuardUntil = tick() + 6.2
    local a = 0
    local o = tick()
    local v = 0
    notifyOwnKick(q, "Ownership Kick")
    drawKickLineToTarget(q, .9)
    local X = { dragging = false, grabStart = 0, checkStart = 0, fps = 60, bodyPos = nil, bodyGyro = nil }
    local function x()
        if X.bodyPos then
            pcall(function() X.bodyPos:Destroy() end)
            X.bodyPos = nil
        end
        if X.bodyGyro then
            pcall(function() X.bodyGyro:Destroy() end)
            X.bodyGyro = nil
        end
    end
    local function U()
        X.dragging = false
        X.grabStart = 0
        X.checkStart = 0
        x()
    end
    local function T(q, c)
        x()
        for q, c in ipairs(q:GetChildren()) do if c:IsA("BodyPosition") or c:IsA("BodyGyro") then c:Destroy() end end
        local r = Instance.new("BodyPosition")
        r.MaxForce = Vector3.new(9000000000.0, 9000000000.0, 9000000000.0)
        r.P = 420000
        r.D = 1800
        r.Position = c.Position
        r.Parent = q
        local j = Instance.new("BodyGyro")
        j.MaxTorque = Vector3.new(9000000000.0, 9000000000.0, 9000000000.0)
        j.P = 520000
        j.D = 1600
        j.CFrame = c
        j.Parent = q
        X.bodyPos = r
        X.bodyGyro = j
    end
    local function e(q, c)
        local r = 6
        if X.fps > 200 then r = 4 elseif X.fps >= 155 then r = 5 end
        for r = 1, r, 1 do
            j.SetNetworkOwner:FireServer(q, c)
            if r % 2 == 0 then j.DestroyGrabLine:FireServer(q) end
        end
        j.DestroyGrabLine:FireServer(q)
    end
    local h = z.RenderStepped:Connect(function(q) if q > 0 then X.fps = 1 / q end end)
    local V, t = pcall(function() while ownershipKickActive do
            kickSelfGuardUntil = math.max(kickSelfGuardUntil or 0, tick() + 3.2)
            local r = G:FindFirstChild(q)
            if not r or not r.Parent then break end
            if IsPlayerInHouse(r) then
                ownershipKickActive = false
                c:Notify({ Title = "Wourld Hub", Description = GetHouseBlockedMessage(), Duration = 3 })
                playEventSound("kick")
                task.defer(function() if M and (M.OwnershipKickToggle and M.OwnershipKickToggle.Value) then M
                            .OwnershipKickToggle:SetValue(false) end end)
                break
            end
            local j = b:FindFirstChild("IsHeld")
            local d = j and j.Value
            if d then if v == 0 then v = tick() end else v = 0 end
            if v > 0 and (tick() - v) > .16 then
                pcall(function() forceReleaseHeldItems(2600) end)
                if (tick() - v) > .78 then
                    kickSelfGuardUntil = math.max(kickSelfGuardUntil or 0, tick() + 1.1)
                    ownershipKickActive = false
                    c:Notify({ Title = "Wourld Hub", Description = "Ownership paused: self held detected", Duration = 2.2 })
                    task.defer(function() if M and (M.OwnershipKickToggle and M.OwnershipKickToggle.Value) then M
                                .OwnershipKickToggle:SetValue(false) end end)
                    break
                end
                z.Heartbeat:Wait()
                continue
            end
            u = b.Character
            Y = u and u:FindFirstChild("HumanoidRootPart")
            P = u and u:FindFirstChildOfClass("Humanoid")
            local h = r.Character
            local V = h and h:FindFirstChild("HumanoidRootPart")
            local t = h and h:FindFirstChild("Humanoid")
            if not Y or not P or P.Health <= 0 or not V or not t or t.Health <= 0 then
                U()
                z.Heartbeat:Wait()
                continue
            end
            if tick() - a > .18 then
                a = tick()
                drawKickLineToTarget(q, .38)
            end
            if not X.dragging then
                local q = V.CFrame * CFrame.new(0, 0, 5.5)
                if ((Y.Position - q.Position)).Magnitude > 1.5 then Y.CFrame = q end
                X.checkStart = 0
                x()
                pcall(function()
                    t.PlatformStand = true
                    t.Sit = true
                    e(V, V.CFrame)
                end)
                Y.AssemblyLinearVelocity = Vector3.zero
                Y.AssemblyAngularVelocity = Vector3.zero
                if X.grabStart == 0 then X.grabStart = tick() end
                if tick() - X.grabStart > .22 then
                    X.dragging = true
                    X.grabStart = 0
                    X.checkStart = tick()
                    local q = O * CFrame.new(0, 22, 0)
                    T(V, q)
                end
            else
                Y.CFrame = O
                Y.AssemblyLinearVelocity = Vector3.zero
                Y.AssemblyAngularVelocity = Vector3.zero
                local q = O * CFrame.new(0, 22, 0)
                if X.bodyPos and X.bodyPos.Parent then
                    X.bodyPos.Position = q.Position
                    if X.bodyGyro then X.bodyGyro.CFrame = q end
                else T(V, q) end
                t.PlatformStand = true
                t.Sit = true
                pcall(function() e(V, q) end)
                if X.checkStart > 0 and tick() - X.checkStart > .24 then if ((V.Position - q.Position)).Magnitude > 8 then
                        U()
                        Y.CFrame = V.CFrame * CFrame.new(0, 0, 5.5)
                    else X.checkStart = tick() end end
            end
            if tick() - o > 11 and not X.dragging then
                o = tick()
                c:Notify({ Title = "Wourld Hub", Description = "Ownership lock retrying...", Duration = 1.6 })
            end
            z.Heartbeat:Wait()
        end end)
    if h then h:Disconnect() end
    U()
    if Y and Y.Parent then pcall(function()
            Y.CFrame = O
            Y.AssemblyLinearVelocity = Vector3.zero
            Y.AssemblyAngularVelocity = Vector3.zero
        end) end
    kickActionBusy = false
    kickSelfGuardUntil = math.max(kickSelfGuardUntil or 0, tick() + .75)
    if not V then warn("OwnershipKickFunction error:", t) end
end

function PalletRagdollFunction(q)
    local c = d
    local r = c:WaitForChild("GrabEvents")
    local j = CFrame.new(0, 800000, 0)
    c.MenuToys.SpawnToyRemoteFunction:InvokeServer("PalletLightBrown", j, Vector3.zero)
    local u
    repeat
        u = workspace:FindFirstChild(b.Name .. "SpawnedInToys") and
        workspace[b.Name .. "SpawnedInToys"]:FindFirstChild("PalletLightBrown")
        z.Heartbeat:Wait()
    until u or not ownershipRagdollActive
    if not u then return end
    local M = u:FindFirstChild("SoundPart")
    if not M then return end
    M.CanCollide = false
    M.Anchored = false
    local function P(q) for c = 1, 3, 1 do
            r.SetNetworkOwner:FireServer(q, q.CFrame)
            r.CreateGrabLine:FireServer(q, Vector3.zero, q.Position, false)
            r.DestroyGrabLine:FireServer(q)
        end end
    P(M)
    while ownershipRagdollActive do
        local q = getSelectedTargetName()
        local c = G:FindFirstChild(q)
        local r = c and (c.Character and c.Character:FindFirstChild("Head"))
        if not r or not M.Parent then
            task.wait(.08)
            continue
        end
        local u = r.Position
        M.CFrame = CFrame.new(u.X, u.Y + .2, u.Z)
        M.AssemblyLinearVelocity = Vector3.zero
        M.AssemblyAngularVelocity = Vector3.new(1000, 1000, 1000)
        P(M)
        M.CanCollide = true
        for q = 1, 3, 1 do z.Heartbeat:Wait() end
        M.CanCollide = false
        M.CFrame = j
        M.AssemblyAngularVelocity = Vector3.zero
        task.wait(.09)
    end
    local O = Y:FindFirstChild(b.Name .. "SpawnedInToys")
    if O then for q, r in ipairs(O:GetChildren()) do if r and ((r.Name == "PalletLightBrown" or r.Name == "RagdollPalete")) then
                pcall(function() c.MenuToys.DestroyToy:FireServer(r) end)
                if r.Parent then r:Destroy() end
            end end end
end

function StartTrace(q)
    local c = G:FindFirstChild(q)
    if not c then return end
    local r = Instance.new("Part")
    r.Anchored = true
    r.CanCollide = false
    r.Transparency = 1
    r.Size = Vector3.new(.1, .1, .1)
    r.Parent = Y
    local j = Instance.new("Part")
    j.Anchored = true
    j.CanCollide = false
    j.Transparency = 1
    j.Size = Vector3.new(.1, .1, .1)
    j.Parent = Y
    local u = Instance.new("Attachment", r)
    local M = Instance.new("Attachment", j)
    XN = Instance.new("Beam")
    XN.Attachment0 = u
    XN.Attachment1 = M
    XN.Color = ColorSequence.new(traceColor)
    XN.Width0 = .6
    XN.Width1 = .6
    XN.LightEmission = 0
    XN.LightInfluence = 1
    XN.FaceCamera = true
    XN.Parent = Y
    local d = b.Character and b.Character:FindFirstChild("HumanoidRootPart")
    local P = c.Character and c.Character:FindFirstChild("HumanoidRootPart")
    xN = z.RenderStepped:Connect(function()
        if not traceEnabled then
            if XN then XN:Destroy() end
            if r then r:Destroy() end
            if j then j:Destroy() end
            return
        end
        if not G:FindFirstChild(q) then
            if XN then XN:Destroy() end
            if r then r:Destroy() end
            if j then j:Destroy() end
            return
        end
        if b.Character and c.Character then
            d = b.Character:FindFirstChild("HumanoidRootPart")
            P = c.Character:FindFirstChild("HumanoidRootPart")
            if d and (P and (r and j)) then
                r.Position = d.Position
                j.Position = P.Position
                XN.Color = ColorSequence.new(traceColor)
            end
        end
    end)
    b.CharacterAdded:Connect(function(q) d = q:WaitForChild("HumanoidRootPart") end)
    c.CharacterAdded:Connect(function(q) P = q:WaitForChild("HumanoidRootPart") end)
end

function getKickGroundPosition(q, c)
    if not q or not q:IsA("BasePart") then return nil end
    local r = RaycastParams.new()
    r.FilterType = Enum.RaycastFilterType.Blacklist
    local j = {}
    if c then table.insert(j, c) end
    if b and b.Character then table.insert(j, b.Character) end
    r.FilterDescendantsInstances = j
    local u = q.Position + Vector3.new(0, 2.2, 0)
    local M = Y:Raycast(u, Vector3.new(0, -24, 0), r)
    if M and M.Position then return M.Position + Vector3.new(0, .06, 0) end
    return (q.Position - Vector3.new(0, q.Size.Y * .5, 0)) + Vector3.new(0, .06, 0)
end

function spawnKickImpactVisual(q)
    if not kickFxEnabled or not q or not q:IsA("BasePart") then return end
    local c = q.Parent
    local r = getKickGroundPosition(q, c)
    if not r then return end
    local j = wN.kickPulseColor or Color3.fromRGB(178, 178, 178)
    local u = Instance.new("Part")
    u.Name = "WourldKickPulse"
    u.Anchored = true
    u.CanCollide = false
    u.CanTouch = false
    u.CanQuery = false
    u.Material = Enum.Material.Neon
    u.Shape = Enum.PartType.Ball
    u.Color = j
    u.Transparency = .25
    u.Size = Vector3.new(1.5, 1.5, 1.5)
    u.CFrame = CFrame.new(r + Vector3.new(0, .16, 0))
    u.Parent = Y
    local M = Instance.new("Part")
    M.Name = "WourldKickHalo"
    M.Anchored = true
    M.CanCollide = false
    M.CanTouch = false
    M.CanQuery = false
    M.Material = Enum.Material.Neon
    M.Shape = Enum.PartType.Cylinder
    M.Color = j:Lerp(Color3.new(1, 1, 1), .2)
    M.Transparency = .38
    M.Size = Vector3.new(.18, 2.2, 2.2)
    M.CFrame = CFrame.new(r + Vector3.new(0, .03, 0)) * CFrame.Angles(0, 0, math.rad(90))
    M.Parent = Y
    task.spawn(function()
        local r = tick()
        while u.Parent and tick() - r <= .45 do
            local j = math.clamp(((tick() - r)) / .45, 0, 1)
            if q.Parent then
                local r = getKickGroundPosition(q, c)
                if r then
                    u.CFrame = CFrame.new(r + Vector3.new(0, .16, 0))
                    M.CFrame = CFrame.new(r + Vector3.new(0, .03, 0)) * CFrame.Angles(0, 0, math.rad(90))
                end
            end
            u.Size = Vector3.new(1.5, 1.5, 1.5) + Vector3.new(8, 8, 8) * j
            u.Transparency = .25 + (.75 * j)
            M.Size = Vector3.new(.18, 2.2, 2.2) + Vector3.new(0, 7.5, 7.5) * j
            M.Transparency = .38 + (.58 * j)
            task.wait(.025)
        end
        if u and u.Parent then u:Destroy() end
        if M and M.Parent then M:Destroy() end
    end)
end

function spawnKickRingVisual(q)
    if not kickFxEnabled or not q or not q:IsA("BasePart") then return end
    local c = q.Parent
    local r = getKickGroundPosition(q, c)
    if not r then return end
    local j = wN.kickRingColor or Color3.fromRGB(196, 196, 196)
    local u = math.max(2, tonumber(wN.kickRingRadius) or 5.5)
    local M = Instance.new("Part")
    M.Name = "WourldKickRing"
    M.Anchored = true
    M.CanCollide = false
    M.CanTouch = false
    M.CanQuery = false
    M.Material = Enum.Material.Neon
    M.Shape = Enum.PartType.Cylinder
    M.Color = j
    M.Transparency = .2
    M.Size = Vector3.new(.15, u, u)
    M.CFrame = CFrame.new(r + Vector3.new(0, .03, 0)) * CFrame.Angles(0, 0, math.rad(90))
    M.Parent = Y
    local G = M:Clone()
    G.Name = "WourldKickRingInner"
    G.Color = j:Lerp(Color3.new(1, 1, 1), .32)
    G.Transparency = .34
    G.Size = Vector3.new(.12, math.max(1.5, u * .72), math.max(1.5, u * .72))
    G.Parent = Y
    task.spawn(function()
        local r = tick()
        while M.Parent and tick() - r <= .55 do
            if not q.Parent then break end
            local j = math.clamp(((tick() - r)) / .55, 0, 1)
            local d = getKickGroundPosition(q, c)
            if not d then break end
            local z = u + ((u * 1.8) * j)
            M.Size = Vector3.new(.15, z, z)
            M.Transparency = .2 + (.8 * j)
            M.CFrame = CFrame.new(d + Vector3.new(0, .03, 0)) * CFrame.Angles(0, 0, math.rad(90))
            local Y = math.max(1.5, u * .72) + ((u * 1.12) * j)
            G.Size = Vector3.new(.12, Y, Y)
            G.Transparency = .34 + (.62 * j)
            G.CFrame = M.CFrame
            task.wait(.025)
        end
        if M and M.Parent then M:Destroy() end
        if G and G.Parent then G:Destroy() end
    end)
end

function applyKickAuraTypeEffect(q)
    if not q or not q:IsA("BasePart") then return end
    local c = tostring(_G.KickAuraType or "Silent")
    local r = game:GetService("Debris")
    if c == "Float" then
        local c = Instance.new("BodyVelocity")
        c.Name = "WourldKickAuraFloat"
        c.MaxForce = Vector3.new(4000, 4000, 4000)
        c.Velocity = Vector3.new(0, 95, 0)
        c.Parent = q
        r:AddItem(c, .35)
    elseif c == "Sky Anchor" then
        local c = Instance.new("BodyPosition")
        c.Name = "WourldKickAuraSky"
        c.MaxForce = Vector3.new(9000, 9000, 9000)
        c.Position = q.Position + Vector3.new(math.random(25, 80), math.random(90, 220), math.random(25, 80))
        c.D = 150
        c.P = 4500
        c.Parent = q
        r:AddItem(c, .55)
    else
        local c = Instance.new("BodyPosition")
        c.Name = "WourldKickAuraSilent"
        c.MaxForce = Vector3.new(0, 12500, 0)
        c.D = 140
        c.P = 4200
        c.Position = q.Position + Vector3.new(0, 6, 0)
        c.Parent = q
        r:AddItem(c, .3)
    end
end

function spawnKickLegVisual(q)
    if not kickFxEnabled or not q then return end
    local c = {}
    for r, j in ipairs({ "Left Leg", "Right Leg", "LeftLowerLeg", "RightLowerLeg" }) do
        local u = q:FindFirstChild(j)
        if u and u:IsA("BasePart") then c[#c + 1] = u end
    end
    if #c == 0 then return end
    local r = ((wN.kickRingColor or Color3.fromRGB(196, 196, 196))):Lerp(Color3.new(1, 1, 1), .08)
    for c, j in ipairs(c) do
        if not getKickGroundPosition(j, q) then continue end
        local u = Instance.new("Part")
        u.Name = "WourldKickLegRing"
        u.Anchored = true
        u.CanCollide = false
        u.CanTouch = false
        u.CanQuery = false
        u.Material = Enum.Material.Neon
        u.Shape = Enum.PartType.Cylinder
        u.Color = r
        u.Transparency = .28
        u.Size = Vector3.new(.12, 1.15, 1.15)
        u.Parent = Y
        task.spawn(function()
            local c = tick()
            local r = (math.random() * math.pi) * 2
            while u.Parent and tick() - c <= .72 do
                if not j.Parent then break end
                local M = math.clamp(((tick() - c)) / .72, 0, 1)
                r = r + .22
                local G = getKickGroundPosition(j, q)
                if not G then break end
                u.Size = Vector3.new(.12, 1.15 + M * 1.85, 1.15 + M * 1.85)
                u.Transparency = .28 + M * .68
                u.CFrame = CFrame.new(G + Vector3.new(0, .04 + M * .08, 0)) * CFrame.Angles(0, r * 1.6, math.rad(90))
                task.wait(.02)
            end
            if u and u.Parent then u:Destroy() end
        end)
    end
end

function spawnKickTargetSquare(q, c)
    if not kickFxEnabled or not q or not q:IsA("BasePart") then return end
    local r = Instance.new("Part")
    r.Name = "WourldKickTargetSquare"
    r.Anchored = true
    r.CanCollide = false
    r.CanTouch = false
    r.CanQuery = false
    r.Material = Enum.Material.Neon
    r.Color = wN.kickLineColorStart or Color3.fromRGB(170, 170, 170)
    r.Transparency = .18
    r.Size = Vector3.new(5.2, .16, 5.2)
    r.Parent = Y
    local j = math.max(.5, tonumber(c) or .9)
    task.spawn(function()
        local c = tick()
        local u = 0
        while r.Parent and tick() - c <= j do
            if not q.Parent then break end
            local M = math.clamp(((tick() - c)) / j, 0, 1)
            u = u + 1.6
            r.Transparency = .18 + M * .7
            r.Size = Vector3.new(5.2 + M * 2.8, .16, 5.2 + M * 2.8)
            r.CFrame = CFrame.new(q.Position + Vector3.new(0, 2.4 + M * 1.4, 0)) * CFrame.Angles(0, math.rad(u), 0)
            task.wait(.03)
        end
        if r and r.Parent then r:Destroy() end
    end)
end

function drawKickLineToTarget(q, c)
    local r = G:FindFirstChild(tostring(q or ""))
    local j = b.Character and b.Character:FindFirstChild("HumanoidRootPart")
    local u = r and (r.Character and r.Character:FindFirstChild("HumanoidRootPart"))
    if not j or not u then return end
    markKickLinePulse(q)
    localKickLineRun = localKickLineRun + 1
    local M = localKickLineRun
    local d = Instance.new("Part")
    d.Anchored = true
    d.Transparency = 1
    d.CanCollide = false
    d.CanTouch = false
    d.CanQuery = false
    d.Size = Vector3.new(.1, .1, .1)
    d.Parent = Y
    local z = d:Clone()
    z.Parent = Y
    local P = Instance.new("Attachment")
    P.Parent = d
    local O = Instance.new("Attachment")
    O.Parent = z
    local a = Instance.new("Beam")
    a.Name = "WourldKickLine"
    a.FaceCamera = true
    a.LightEmission = .85
    a.LightInfluence = 0
    local o = math.clamp(tonumber(wN.kickLineWidth) or .2, .06, 1.8)
    local v = wN.kickLineColorStart or Color3.fromRGB(170, 170, 170)
    local X = wN.kickLineColorEnd or Color3.fromRGB(232, 232, 232)
    a.Width0 = o
    a.Width1 = o
    a.CurveSize0 = 1.4
    a.CurveSize1 = -1.4
    a.Transparency = NumberSequence.new({ NumberSequenceKeypoint.new(0, .05), NumberSequenceKeypoint.new(.5, .15),
        NumberSequenceKeypoint.new(1, .05) })
    a.Color = ColorSequence.new(v, X)
    a.Attachment0 = P
    a.Attachment1 = O
    a.Segments = 12
    a.Parent = Y
    local x = Instance.new("Beam")
    x.Name = "WourldKickLineGlow"
    x.FaceCamera = true
    x.LightEmission = 1
    x.LightInfluence = 0
    x.Width0 = o * 2.2
    x.Width1 = o * 2.2
    x.CurveSize0 = 1.4
    x.CurveSize1 = -1.4
    x.Transparency = NumberSequence.new(.72)
    x.Color = ColorSequence.new(v:Lerp(Color3.new(1, 1, 1), .2), X:Lerp(Color3.new(1, 1, 1), .2))
    x.Attachment0 = P
    x.Attachment1 = O
    x.Segments = 10
    x.Parent = Y
    local U = tick()
    if U - ((gN.kickFxLastRingAt or 0)) >= .55 then
        gN.kickFxLastRingAt = U
        spawnKickImpactVisual(u)
        spawnKickRingVisual(u)
        spawnKickLegVisual(r and r.Character)
        spawnKickLegVisual(b.Character)
        spawnKickTargetSquare(u, math.max(.7, tonumber(c) or .9))
    end
    local T = math.max(.2, tonumber(c) or .6)
    task.spawn(function()
        local c = tick()
        while M == localKickLineRun and (a.Parent and tick() - c <= T) do
            j = b.Character and b.Character:FindFirstChild("HumanoidRootPart")
            u = r and (r.Character and r.Character:FindFirstChild("HumanoidRootPart"))
            if not j or not u then break end
            local M = math.clamp(((tick() - c)) / T, 0, 1)
            local G = ((j.Position - u.Position)).Magnitude
            local Y = math.clamp(G * .045, 1.2, 8)
            a.CurveSize0 = Y
            a.CurveSize1 = -Y
            x.CurveSize0 = Y
            x.CurveSize1 = -Y
            a.Width0 = o * ((1 - M * .18))
            a.Width1 = o * ((.92 - M * .22))
            x.Width0 = (o * 2.2) * ((1 - M * .08))
            x.Width1 = (o * 2.2) * ((.94 - M * .1))
            markKickLinePulse(q)
            d.Position = j.Position + Vector3.new(0, 1.1, 0)
            z.Position = u.Position + Vector3.new(0, 1, 0)
            task.wait(.025)
        end
        if a and a.Parent then a:Destroy() end
        if x and x.Parent then x:Destroy() end
        if d and d.Parent then d:Destroy() end
        if z and z.Parent then z:Destroy() end
    end)
end

function AntiBananaSitFunction() while antiBananaSitActive do
        local q = b.Character
        if q then
            local c = q:FindFirstChild("Humanoid")
            local r = q:FindFirstChild("HumanoidRootPart")
            if c and (r and c.Health > 0) then
                c.Sit = true
                c.PlatformStand = false
                c.AutoRotate = true
                c:ChangeState(Enum.HumanoidStateType.Running)
                c.Jump = true
                if c.HipHeight < 1.8 then c.HipHeight = 1.8 end
                if c.WalkSpeed < 10 then c.WalkSpeed = 16 end
                task.defer(function() if antiBananaSitActive and (c and c.Parent) then c.Sit = false end end)
                local q = workspace.CurrentCamera
                if q then
                    local j = q.CFrame.LookVector
                    local u = Vector3.new(j.X, 0, j.Z)
                    if u.Magnitude > .05 then
                        r.CFrame = CFrame.new(r.Position, r.Position + u)
                        c:Move(u.Unit, true)
                    end
                    local M = r.AssemblyLinearVelocity
                    r.AssemblyLinearVelocity = Vector3.new(M.X * .35, math.max(M.Y, 18), M.Z * .35)
                end
            end
        end
        task.wait(.035)
    end end

function KillDodgeFunction()
    local q = d:FindFirstChild("Struggle")
    local c = Vector3.new(252, -7, 464)
    local function r()
        for q, c in pairs(workspace.Plots:GetChildren()) do if c:FindFirstChild(b.Name) then return c end end
        return nil
    end
    local j = r()
    if j then if j.Name == "Plot1" then c = Vector3.new(-533, -7, 90) elseif j.Name == "Plot2" then c = Vector3.new(-483,
                -7, -164) elseif j.Name == "Plot3" then c = Vector3.new(252, -7, 464) elseif j.Name == "Plot4" then c =
            Vector3.new(509, 83, -339) else c = Vector3.new(553, 123, -74) end end
    b.CharacterAdded:Connect(function(r)
        local j = r:WaitForChild("HumanoidRootPart")
        local u = r:WaitForChild("Humanoid")
        task.spawn(function() while killDodgeActive do
                task.wait()
                if not ((b:FindFirstChild("InPlot") and b.InPlot.Value)) and u.Health > 0 then
                    j.CFrame = CFrame.new(c)
                    j.Anchored = false
                end
            end end)
        u.Died:Connect(function()
            task.wait(2.8)
            while killDodgeActive and (not ((b:FindFirstChild("InPlot") and b.InPlot.Value)) and u.Health > 0) do
                for c = 1, 3, 1 do task.spawn(function() if q then q:FireServer(b) end end) end
                task.wait()
            end
        end)
    end)
end

function AntiRagBlobFunction()
    local q = d:FindFirstChild("RagdollRemote")
    local c = false
    local function r(q) if hN[q] then
            hN[q]:Disconnect()
            hN[q] = nil
        end end
    local function j(j)
        local u = j and j:FindFirstChild("Humanoid")
        local M = j and j:FindFirstChild("HumanoidRootPart")
        if u and (M and q) then
            r("ARSeat")
            hN.ARSeat = (u:GetPropertyChangedSignal("SeatPart")):Connect(function() if u.SeatPart and (u.SeatPart.Parent and (u.SeatPart.Parent.Name == "CreatureBlobman" and not c)) then
                    c = true
                    local r = u.SeatPart
                    while not u.Sit do task.wait() end
                    q:FireServer(M, 3)
                    while not ((u:FindFirstChild("Ragdolled") and u.Ragdolled.Value)) and not u.Sit do task.wait() end
                    task.wait(.4)
                    u.Sit = false
                    if r and r:IsA("Part") then r:Sit(u) end
                    task.delay(.25,
                        function()
                            while u and u.SeatPart do
                                if b.Character and b.Character:FindFirstChild("HumanoidRootPart") then q:FireServer(
                                    b.Character.HumanoidRootPart, 1) end
                                task.wait(.05)
                            end
                            c = false
                        end)
                end end)
        end
    end
    if antiRagBlobActive then
        j(b.Character or b.CharacterAdded:Wait())
        r("ARChar")
        hN.ARChar = b.CharacterAdded:Connect(function(q)
            task.wait(.5)
            j(q)
        end)
    else
        for q, c in pairs(hN) do if c then c:Disconnect() end end
        hN = {}
    end
end

function denyEnemyBlobModel(q, c, r)
    if not q or not q.Parent then return false end
    local j = q:FindFirstChild("Head")
    local u = j and j:FindFirstChild("PartOwner")
    if u and tostring(u.Value or "") == b.Name then return false end
    local M = q:FindFirstChild("HumanoidRootPart") or q.PrimaryPart or q:FindFirstChildWhichIsA("BasePart", true)
    if not M then return false end
    pcall(function() c:FireServer(M, M.CFrame) end)
    local G = tostring(r or "UnderMap")
    if G == "UnderMap" or G == "Both" then pcall(function()
            M.CFrame = CFrame.new(M.Position.X, -2200, M.Position.Z)
            M.AssemblyLinearVelocity = Vector3.zero
            M.AssemblyAngularVelocity = Vector3.zero
        end) end
    if G == "Fling" or G == "Both" then pcall(function()
            M.AssemblyLinearVelocity = Vector3.new(math.random(-150, 150), 240, math.random(-150, 150))
            M.AssemblyAngularVelocity = Vector3.new(math.random(-40, 40), math.random(-70, 70), math.random(-40, 40))
        end) end
    return true
end

function StartAntiBlobDenyLoop()
    local q = (d:WaitForChild("GrabEvents")):WaitForChild("SetNetworkOwner")
    while antiBlobDenyActive do
        local c = b.Character
        local r = c and c:FindFirstChild("HumanoidRootPart")
        if r then
            local c = math.clamp(tonumber(antiBlobDenyRadius) or 220, 40, 600)
            for j, u in ipairs(G:GetPlayers()) do if u ~= b then
                    local j = Y:FindFirstChild(u.Name .. "SpawnedInToys")
                    local M = j and j:FindFirstChild("CreatureBlobman")
                    local G = M and
                    ((M:FindFirstChild("HumanoidRootPart") or M.PrimaryPart or M:FindFirstChildWhichIsA("BasePart", true)))
                    if M and (G and ((G.Position - r.Position)).Magnitude <= c) then denyEnemyBlobModel(M, q,
                            antiBlobDenyMode) end
                end end
        end
        task.wait(.12)
    end
end

function setAntiBlobDenyState(q)
    antiBlobDenyActive = q and true or false
    if antiBlobDenyActive then disableAutoSitBloomWithNotify("Anti Enemy Blob") end
    if antiBlobDenyTask then
        task.cancel(antiBlobDenyTask)
        antiBlobDenyTask = nil
    end
    if antiBlobDenyActive then antiBlobDenyTask = task.spawn(StartAntiBlobDenyLoop) end
end

function fireSelectedItemProjectile()
    local q = tostring(Y6 or "")
    if q == "" then return false end
    local c = b.Character
    local r = c and c:FindFirstChildOfClass("Humanoid")
    local j = c and c:FindFirstChild("HumanoidRootPart")
    if not c or not r or not j or r.Health <= 0 then return false end
    local u = spawnOwnedToyByName(q, CFrame.new(0, 8, 15))
    if not u then return false end
    local M = u:FindFirstChild("HoldPart")
    local G = M and M:FindFirstChild("HoldItemRemoteFunction")
    local z = M and M:FindFirstChild("DropItemRemoteFunction")
    local P = Y.CurrentCamera
    local O = P and P.CFrame.LookVector or j.CFrame.LookVector
    local a = math.clamp(tonumber(P6) or 800, 50, 3000)
    n = tick() + .45
    if M and (G and z) then
        pcall(function() G:InvokeServer(u, c) end)
        task.wait(.025)
        pcall(function() z:InvokeServer(u, j.CFrame * CFrame.new(0, 2, -2), O.Unit * a) end)
    else
        local q = u.PrimaryPart or u:FindFirstChild("SoundPart") or u:FindFirstChildWhichIsA("BasePart", true)
        if not q then return false end
        local c = d:FindFirstChild("GrabEvents") and d.GrabEvents:FindFirstChild("SetNetworkOwner")
        if c then pcall(function() c:FireServer(q, q.CFrame) end) end
        pcall(function()
            q.CFrame = j.CFrame * CFrame.new(0, 2, -2)
            q.AssemblyLinearVelocity = O.Unit * a
            q.AssemblyAngularVelocity = Vector3.new(math.random(-20, 20), math.random(-20, 20), math.random(-20, 20))
        end)
    end
    return true
end

function fireLoopExplosionItemAtTarget(q, c, r)
    local j = ((tostring(q or "")):gsub("^%s+", "")):gsub("%s+$", "")
    if j == "" or not c then return false end
    local u = getMenuToyRemotes()
    if not u then return false end
    local M = c.CFrame * CFrame.new(math.random(-8, 8) / 10, math.random(16, 34) / 10, math.random(-8, 8) / 10)
    local G = math.clamp(tonumber(r) or 540, 50, 3200)
    local d = c.Position + Vector3.new(math.random(-2, 2), math.random(-1, 2), math.random(-2, 2))
    local z = (d - M.Position)
    if z.Magnitude <= .01 then z = c.CFrame.LookVector end
    pcall(function() u:InvokeServer(j, M, z.Unit * G) end)
    return true
end

function StartLoopExplosionMixLoop() while gN.loopExplosionActive do
        local q = getSelectedTargetName()
        local c = G:FindFirstChild(q)
        local r = c and (c.Character and c.Character:FindFirstChild("HumanoidRootPart"))
        if r then
            local q = tostring(gN.loopExplosionMode or "Alternate")
            local c = tostring(gN.loopExplosionItemA or "BallSnowball")
            local j = tostring(gN.loopExplosionItemB or c)
            local u = {}
            if q == "Single" then u[1] = c elseif q == "Together" then
                u[1] = c
                u[2] = j
            else
                gN.loopExplosionPulse = ((tonumber(gN.loopExplosionPulse) or 0)) + 1
                u[1] = ((gN.loopExplosionPulse % 2) == 1) and c or j
            end
            local M = math.clamp(tonumber(gN.loopExplosionBurst) or 2, 1, 8)
            local G = math.clamp(tonumber(gN.loopExplosionVelocity) or 540, 50, 3200)
            for q = 1, M, 1 do for q, c in ipairs(u) do if c and c ~= "" then fireLoopExplosionItemAtTarget(c, r, G) end end end
        end
        task.wait(math.clamp(tonumber(gN.loopExplosionInterval) or .22, .08, 2.5))
    end end

function setLoopExplosionState(q)
    gN.loopExplosionActive = q and true or false
    if gN.loopExplosionTask then
        task.cancel(gN.loopExplosionTask)
        gN.loopExplosionTask = nil
    end
    if gN.loopExplosionActive then gN.loopExplosionTask = task.spawn(StartLoopExplosionMixLoop) end
end

function getBestSpawnCFrame()
    local q = Y:FindFirstChild("SpawnLocation", true)
    if q and q:IsA("BasePart") then return q.CFrame end
    for q, c in ipairs(Y:GetDescendants()) do if c:IsA("BasePart") then
            local q = string.lower(c.Name or "")
            if q:find("spawn", 1, true) and c.Anchored then return c.CFrame end
        end end
    return CFrame.new(0, 20, 0)
end

function getAuraActionCandidates(q)
    local c = {}
    local r = b.Character and b.Character:FindFirstChild("HumanoidRootPart")
    if not r then return c end
    local j = math.clamp(tonumber(q) or 24, 6, 120)
    for q, u in ipairs(G:GetPlayers()) do if u ~= b then
            local q = u.Character
            local M = q and q:FindFirstChildOfClass("Humanoid")
            local G = q and q:FindFirstChild("HumanoidRootPart")
            if M and (G and (M.Health > 0 and ((G.Position - r.Position)).Magnitude <= j)) then c[#c + 1] = u end
        end end
    return c
end

function hasAnyAuraActionEnabled() return auraGrabActionActive or auraKillActionActive or auraBringActionActive or
    auraTpSpawnActionActive or auraFlingActionActive end

function StartAuraActionsLoop()
    local q = getBestSpawnCFrame()
    while hasAnyAuraActionEnabled() do
        local c = tick()
        local r, j = getGrabEventsBundle()
        local u = getAuraActionCandidates(auraActionRadius)
        for u, M in ipairs(u) do
            local G = tostring(M.UserId)
            local d = tonumber(auraActionNextByUserId[G]) or 0
            if c >= d then
                auraActionNextByUserId[G] = c + math.max(.1, tonumber(auraActionCooldown) or .9)
                local u = M.Character and M.Character:FindFirstChild("HumanoidRootPart")
                if u and r then pcall(function() r:FireServer(u, u.CFrame) end) end
                if auraGrabActionActive and (u and j) then pcall(function() j:FireServer(u, Vector3.zero, u.Position,
                            false) end) end
                if auraTpSpawnActionActive and u then pcall(function()
                        u.CFrame = q * CFrame.new(0, 5, 0)
                        u.AssemblyLinearVelocity = Vector3.zero
                        u.AssemblyAngularVelocity = Vector3.zero
                    end) end
                if auraFlingActionActive and u then pcall(function()
                        local q = b.Character and b.Character:FindFirstChild("HumanoidRootPart")
                        local c = (q and q.Position) or (u.Position - Vector3.new(0, 0, 1))
                        local r = u.Position - c
                        local j = r.Magnitude > .01 and r.Unit or Vector3.new(0, 0, 1)
                        u.AssemblyLinearVelocity = j * 245 + Vector3.new(0, 130, 0)
                        u.AssemblyAngularVelocity = Vector3.new(math.random(-240, 240), math.random(-320, 320),
                            math.random(-240, 240))
                    end) end
                if auraBringActionActive then pcall(function() RunSelectedMethodOnTarget(M.Name, "Bring") end) end
                if auraKillActionActive then pcall(function() RunSelectedMethodOnTarget(M.Name, "Kick") end) end
            end
        end
        task.wait(.12)
    end
    auraActionTask = nil
end

function refreshAuraActionsLoop() if hasAnyAuraActionEnabled() then if not auraActionTask then auraActionTask = task
            .spawn(StartAuraActionsLoop) end else if auraActionTask then
            task.cancel(auraActionTask)
            auraActionTask = nil
        end end end

function getForeignToyRootParts(q)
    local c = {}
    local r = b.Character and b.Character:FindFirstChild("HumanoidRootPart")
    local j = math.max(15, tonumber(q) or 240)
    local u = math.clamp(tonumber(toyAuraRadius) or 24, 8, 220)
    if not r then return c end
    for q, M in ipairs(Y:GetChildren()) do if M:IsA("Folder") and (M.Name:sub(-13) == "SpawnedInToys" and M.Name ~= (b.Name .. "SpawnedInToys")) then for q, M in ipairs(M:GetChildren()) do
                local G = M:FindFirstChild("HumanoidRootPart") or M.PrimaryPart or
                M:FindFirstChildWhichIsA("BasePart", true)
                if G and (G:IsA("BasePart") and ((G.Position - r.Position)).Magnitude <= u) then
                    c[#c + 1] = G
                    if #c >= j then return c end
                end
            end end end
    return c
end

function setToyFreezeOnRoot(q, c)
    if not q or not q.Parent then return end
    local r = q:FindFirstChild("WH_ToyFreezeBP")
    local j = q:FindFirstChild("WH_ToyFreezeBG")
    if c then
        if not r then
            r = Instance.new("BodyPosition")
            r.Name = "WH_ToyFreezeBP"
            r.D = 120
            r.P = 450000
            r.MaxForce = Vector3.new(9000000000.0, 9000000000.0, 9000000000.0)
            r.Parent = q
        end
        if not j then
            j = Instance.new("BodyGyro")
            j.Name = "WH_ToyFreezeBG"
            j.D = 120
            j.P = 450000
            j.MaxTorque = Vector3.new(9000000000.0, 9000000000.0, 9000000000.0)
            j.Parent = q
        end
        r.Position = q.Position
        j.CFrame = q.CFrame
    else
        if r then pcall(function() r:Destroy() end) end
        if j then pcall(function() j:Destroy() end) end
    end
end

function cleanupToyAuraFreeze() for q, c in ipairs(Y:GetDescendants()) do if c.Name == "WH_ToyFreezeBP" or c.Name == "WH_ToyFreezeBG" then
            pcall(function() c:Destroy() end) end end end

function hasAnyToyAuraEnabled() return toyFreezeAuraActive or toyTpAuraActive end

function StartToyAuraLoop()
    while hasAnyToyAuraEnabled() do
        local q = select(1, getGrabEventsBundle())
        local c = getForeignToyRootParts(220)
        for c, r in ipairs(c) do
            if q then pcall(function() q:FireServer(r, r.CFrame) end) end
            if toyFreezeAuraActive then setToyFreezeOnRoot(r, true) else setToyFreezeOnRoot(r, false) end
            if toyTpAuraActive then pcall(function()
                    r.CFrame = toyAuraTpPos
                    r.AssemblyLinearVelocity = Vector3.zero
                    r.AssemblyAngularVelocity = Vector3.zero
                end) end
        end
        task.wait(.11)
    end
    cleanupToyAuraFreeze()
    toyAuraTask = nil
end

function refreshToyAuraLoop() if hasAnyToyAuraEnabled() then if not toyAuraTask then toyAuraTask = task.spawn(
            StartToyAuraLoop) end else
        if toyAuraTask then
            task.cancel(toyAuraTask)
            toyAuraTask = nil
        end
        cleanupToyAuraFreeze()
    end end

function isBombToyName(q)
    local c = string.lower(tostring(q or ""))
    return c == "bombmissile" or c == "fireworkmissile" or c == "bombballoon" or c == "bombdarkmatter" or
    c == "ballsnowball"
end

function getBombTriggerPart(q)
    if not q then return nil end
    local c = { "PartHitDetector", "Balloon", "HitboxBodyTop", "PyramidOctagon", "Spinner", "SoundPart" }
    for c, r in ipairs(c) do
        local j = q:FindFirstChild(r, true)
        if j and j:IsA("BasePart") then return j end
    end
    return q:FindFirstChildWhichIsA("BasePart", true)
end

function detonateBombModelAt(q, c, r)
    if not q or not q.Parent then return false end
    local j = getBombTriggerPart(q)
    if not j then return false end
    local u = select(1, getGrabEventsBundle())
    if u then pcall(function() u:FireServer(j, j.CFrame) end) end
    pcall(function() if firetouchinterest and (c and c:IsA("BasePart")) then
            firetouchinterest(j, c, 0)
            firetouchinterest(j, c, 1)
        elseif r then j.CFrame = CFrame.new(r) end end)
    return true
end

function getAllSpawnedToyFolders()
    local q = {}
    local c = {}
    local function r(r) if r and (r.Parent and not c[r]) then
            c[r] = true
            q[#q + 1] = r
        end end
    for q, c in ipairs(Y:GetChildren()) do if c:IsA("Folder") and c.Name:sub(-13) == "SpawnedInToys" then r(c) end end
    for q, c in ipairs(getLocalToyContainers()) do r(c) end
    return q
end

function explodeAllBombsAtMouse(q)
    local c = b and b:GetMouse()
    local r = c and c.Target
    local j = c and (c.Hit and c.Hit.Position)
    if not j and not r then return 0 end
    local u = math.clamp(tonumber(q) or 140, 1, 400)
    local M = 0
    for q, c in ipairs(getAllSpawnedToyFolders()) do for q, c in ipairs(c:GetChildren()) do if isBombToyName(c.Name) then if detonateBombModelAt(c, r, j or (r and r.Position) or Vector3.new()) then
                    M = M + 1
                    if M >= u then return M end
                    if toyExplodeDelay > 0 then task.wait(math.clamp(tonumber(toyExplodeDelay) or 0, 0, 1)) end
                end end end end
    return M
end

function explodeMyBombsAtMouse(q)
    local c = b and b:GetMouse()
    local r = c and c.Target
    local j = c and (c.Hit and c.Hit.Position)
    if not j and not r then return 0 end
    local u = math.clamp(tonumber(q) or 90, 1, 200)
    local M = 0
    for q, c in ipairs(getLocalToyContainers()) do for q, c in ipairs(c:GetChildren()) do if isBombToyName(c.Name) then if detonateBombModelAt(c, r, j or (r and r.Position) or Vector3.new()) then
                    M = M + 1
                    if M >= u then return M end
                    if toyExplodeDelay > 0 then task.wait(math.clamp(tonumber(toyExplodeDelay) or 0, 0, 1)) end
                end end end end
    return M
end

function StartToyLoopSpawn()
    while toyLoopSpawnActive do
        local q = ((tostring(toyLoopSpawnItem or "")):gsub("^%s+", "")):gsub("%s+$", "")
        if q ~= "" then spawnOwnedToyByName(q, CFrame.new(0, 10, 14)) end
        task.wait(math.clamp(tonumber(toyLoopSpawnInterval) or .25, .05, 3))
    end
    toyLoopSpawnTask = nil
end

function refreshToyLoopSpawn() if toyLoopSpawnActive then if not toyLoopSpawnTask then toyLoopSpawnTask = task.spawn(
            StartToyLoopSpawn) end else if toyLoopSpawnTask then
            task.cancel(toyLoopSpawnTask)
            toyLoopSpawnTask = nil
        end end end

function getLocalHousePlotAndFolder()
    local q = Y:FindFirstChild("Plots")
    local c = Y:FindFirstChild("PlotItems")
    local r = c and c:FindFirstChild("PlayersInPlots")
    if not ((q and (c and (r and r:FindFirstChild(b.Name))))) then return nil, nil end
    for q, r in ipairs(q:GetChildren()) do
        local j = r:FindFirstChild("PlotSign")
        local u = j and j:FindFirstChild("ThisPlotsOwners")
        if u then for q, j in ipairs(u:GetChildren()) do if tostring(j.Value or "") == b.Name then return r,
                        c:FindFirstChild(r.Name) end end end
    end
    return nil, nil
end

function applyHomeGuardToToy(q, c)
    if not q or not q.Parent then return false end
    local r = q:FindFirstChild("HumanoidRootPart") or q.PrimaryPart or q:FindFirstChildWhichIsA("BasePart", true)
    if not r then return false end
    pcall(function() c:FireServer(r, r.CFrame) end)
    local j = tostring(gN.homeGuardMode or "UnderMap")
    if j == "UnderMap" or j == "Both" then pcall(function()
            r.CFrame = CFrame.new(r.Position.X, -2200, r.Position.Z)
            r.AssemblyLinearVelocity = Vector3.zero
            r.AssemblyAngularVelocity = Vector3.zero
        end) end
    if j == "Fling" or j == "Both" then pcall(function()
            r.AssemblyLinearVelocity = Vector3.new(math.random(-170, 170), 220, math.random(-170, 170))
            r.AssemblyAngularVelocity = Vector3.new(math.random(-50, 50), math.random(-80, 80), math.random(-50, 50))
        end) end
    return true
end

function StartHomeGuardLoop()
    local q = (d:WaitForChild("GrabEvents")):WaitForChild("SetNetworkOwner")
    while gN.homeGuardActive do
        local c, r = getLocalHousePlotAndFolder()
        local j = c and ((c:FindFirstChild("PlotSign") or c:FindFirstChildWhichIsA("BasePart", true)))
        local u = j and j.Position
        if u then
            local c = math.clamp(tonumber(gN.homeGuardRadius) or 120, 45, 420)
            for r, j in ipairs(G:GetPlayers()) do if j ~= b then
                    local r = Y:FindFirstChild(j.Name .. "SpawnedInToys")
                    if r then for r, j in ipairs(r:GetChildren()) do
                            local M = string.lower(j.Name or "")
                            if M:find("blob", 1, true) or M:find("tractor", 1, true) or M:find("gucci", 1, true) then
                                local r = j:FindFirstChild("HumanoidRootPart") or j.PrimaryPart or
                                j:FindFirstChildWhichIsA("BasePart", true)
                                if r and ((r.Position - u)).Magnitude <= c then if M:find("blob", 1, true) then
                                        denyEnemyBlobModel(j, q, gN.homeGuardMode) else applyHomeGuardToToy(j, q) end end
                            end
                        end end
                end end
        end
        task.wait(.12)
    end
end

function setHomeGuardState(q)
    gN.homeGuardActive = q and true or false
    if gN.homeGuardTask then
        task.cancel(gN.homeGuardTask)
        gN.homeGuardTask = nil
    end
    if gN.homeGuardActive then gN.homeGuardTask = task.spawn(StartHomeGuardLoop) end
end

function getBuildPresetFolderPath()
    local q = "Wourld_Hub/FlingThings/game-config"
    if j and j.GetRootPath then
        local c, r = pcall(function() return j:GetRootPath() end)
        if c and (type(r) == "string" and r ~= "") then q = tostring(r) end
    end
    return ((tostring(q)):gsub("\\", "/")):gsub("/+$", "") .. "/build-presets"
end

function ensureBuildPresetFolderPath()
    if not ((isfolder and makefolder)) then return nil end
    local q = getBuildPresetFolderPath()
    local c = ""
    for q in (tostring(q)):gmatch("[^/]+") do
        c = (c == "") and q or (c .. ("/" .. q))
        local r, j = pcall(function() return isfolder(c) end)
        if (not r) or (not j) then pcall(function() makefolder(c) end) end
    end
    local r, j = pcall(function() return isfolder(q) end)
    if r and j then return q end
    return nil
end

function refreshBuildPresetDropdown()
    local q = {}
    local c = {}
    local r = {}
    local function j(j, u)
        local M = ((tostring(j or "")):gsub("^%s+", "")):gsub("%s+$", "")
        local G = string.lower(M)
        if M ~= "" and not r[G] then
            r[G] = true
            q[#q + 1] = M
            c[G] = tostring(u or "")
        end
    end
    local M = getBuildPresetFolderPath()
    local G = { M, M:gsub("/", "\\"), "workspace/" .. M, "workspace\\" .. M:gsub("/", "\\") }
    if listfiles then for q, c in ipairs(G) do
            local r, u = pcall(function() return listfiles(c) end)
            if r and type(u) == "table" then for q, c in ipairs(u) do
                    local r = (tostring(c or "")):gsub("\\", "/")
                    local u = r:match("([^/\\]+)$") or ""
                    local M = string.lower(u)
                    if M:sub(-5) == ".json" and #u > 5 then j(u:sub(1, #u - 5), r) end
                end end
        end end
    table.sort(q, function(q, c) return string.lower(q) < string.lower(c) end)
    if #q == 0 then
        q = { "base" }
        c.base = ""
    end
    gN.buildPresetValues = q
    gN.buildPresetPathMap = c
    local d = tostring(gN.buildPresetSelected or "")
    if d == "" or not c[string.lower(d)] then gN.buildPresetSelected = q[1] end
    if u and (u.BuildPresetDropdown and u.BuildPresetDropdown.SetValues) then pcall(function() u.BuildPresetDropdown
                :SetValues(q) end) end
    if u and (u.BuildPresetDropdown and u.BuildPresetDropdown.SetValue) then pcall(function() u.BuildPresetDropdown
                :SetValue(gN.buildPresetSelected) end) end
    return q
end

function saveBuildPreset(q)
    if not writefile then return false, "writefile unavailable" end
    local c = ensureBuildPresetFolderPath()
    if not c then return false, "build preset folder unavailable" end
    local r = ((tostring(q or "")):gsub("^%s+", "")):gsub("%s+$", "")
    if j and j.SanitizeName then r = j:SanitizeName(r) else r = r:gsub("[%c<>:\"/\\|%?%*]", "") end
    if r == "" then return false, "invalid preset name" end
    local u = getLocalToyFolder()
    if not u then return false, "local toys folder not found" end
    local M = {}
    for q, c in ipairs(u:GetChildren()) do
        local r = c:IsA("BasePart") and c or c.PrimaryPart or c:FindFirstChildWhichIsA("BasePart", true)
        if r then M[#M + 1] = { name = c.Name, c = { r.CFrame:GetComponents() } } end
    end
    if #M == 0 then return false, "nothing to save" end
    local G = (game:GetService("HttpService")):JSONEncode({ name = r, count = #M, savedAt = os.time(), items = M })
    local d = c .. ("/" .. (r .. ".json"))
    local z, Y = pcall(function() writefile(d, G) end)
    if not z then return false, tostring(Y) end
    gN.buildPresetSelected = r
    refreshBuildPresetDropdown()
    return true, #M
end

function loadBuildPreset(q)
    if not readfile then return false, "readfile unavailable" end
    local c = ((tostring(q or gN.buildPresetSelected or "")):gsub("^%s+", "")):gsub("%s+$", "")
    if c == "" then return false, "preset name empty" end
    local r = string.lower(c)
    local j = gN.buildPresetPathMap or {}
    local u = j[r]
    if not u or u == "" then
        local q = getBuildPresetFolderPath()
        u = q .. ("/" .. (c .. ".json"))
    end
    local M, G = pcall(function() return readfile(u) end)
    if not M or type(G) ~= "string" or G == "" then return false, "preset file not found" end
    local d, z = pcall(function() return (game:GetService("HttpService")):JSONDecode(G) end)
    if not d or type(z) ~= "table" then return false, "invalid preset data" end
    local Y = z.items
    if type(Y) ~= "table" or #Y == 0 then return false, "preset has no items" end
    local P = getMenuToyRemotes()
    if not P then return false, "spawn remote unavailable" end
    local O = table.unpack or unpack
    local a = 0
    for q, c in ipairs(Y) do
        local r = tostring((type(c) == "table" and ((c.name or c.Name))) or "")
        local j = type(c) == "table" and ((c.c or c.cf or c.CFrame)) or nil
        if r ~= "" then
            local c = nil
            if type(j) == "table" and #j >= 12 then
                local q, r = pcall(function() return CFrame.new(O(j, 1, 12)) end)
                if q then c = r end
            end
            if not c then
                local q = b.Character
                local r = q and q:FindFirstChild("HumanoidRootPart")
                c = r and (r.CFrame * CFrame.new(math.random(-7, 7), 5, math.random(-7, 7))) or CFrame.new(0, 35, 0)
            end
            pcall(function() P:InvokeServer(r, c, Vector3.zero) end)
            a = a + 1
            task.wait((q % 9 == 0) and .08 or .03)
        end
    end
    gN.buildPresetSelected = c
    return a > 0, a
end

function TelekinesisShieldFunction()
    local q = game:GetService("Players")
    local c = q.LocalPlayer
    local r = (game:GetService("ReplicatedStorage")).GrabEvents.SetNetworkOwner
    while telekinesisShieldActive do
        local q = c.Character
        local j = q and q:FindFirstChild("HumanoidRootPart")
        if j then for c, u in pairs(workspace:GetDescendants()) do
                if not telekinesisShieldActive then break end
                if u:IsA("BasePart") and (not u.Anchored and not u:IsDescendantOf(q)) then
                    local q = ((u.Position - j.Position)).Magnitude
                    if q <= 60 then
                        local q = ((u.Position - j.Position)).Unit
                        local c = ((q + Vector3.new(0, .2, 0))).Unit * 100
                        pcall(function()
                            r:FireServer(u, u.CFrame)
                            u.AssemblyLinearVelocity = c
                            u.AssemblyAngularVelocity = Vector3.new(math.random(-10, 10), math.random(-10, 10),
                                math.random(-10, 10))
                        end)
                    end
                end
            end end
        task.wait(.1)
    end
end

function LoopKillFunction(q)
    local c = game.Players:FindFirstChild(q)
    if not c then return end
    local r = game:GetService("ReplicatedStorage")
    local j = game:GetService("RunService")
    local u = r:WaitForChild("GrabEvents")
    while loopKillActive and (c and c.Parent) do
        if not c.Character then
            task.wait(.5)
            continue
        end
        local q = game.Players.LocalPlayer.Character
        local r = q and q:FindFirstChild("HumanoidRootPart")
        local M = c.Character
        local G = M and M:FindFirstChild("HumanoidRootPart")
        local d = M and M:FindFirstChild("Humanoid")
        if G and (d and (d.Health > 0 and r)) then
            local q = r.CFrame
            local c = tick()
            while tick() - c < .35 and loopKillActive do
                if not G.Parent then break end
                r.CFrame = G.CFrame * CFrame.new(0, 0, 2)
                r.Velocity = Vector3.zero
                pcall(function()
                    u.SetNetworkOwner:FireServer(G, r.CFrame)
                    d:ChangeState(Enum.HumanoidStateType.Dead)
                    d.Health = 0
                    u.CreateGrabLine:FireServer(G, Vector3.zero, G.Position, false)
                    u.DestroyGrabLine:FireServer(G)
                end)
                j.Heartbeat:Wait()
            end
            if r then
                r.CFrame = q
                r.Velocity = Vector3.zero
            end
            task.wait(1.2)
        else task.wait(.5) end
    end
    local M = game.Players.LocalPlayer.Character
    local G = M and M:FindFirstChild("HumanoidRootPart")
    if G then G.Velocity = Vector3.zero end
end

function SnowballRagdollFunction(q)
    local c = G.LocalPlayer
    local r = (d:WaitForChild("MenuToys")):WaitForChild("SpawnToyRemoteFunction")
    local j = d:FindFirstChild("GrabEvents")
    local u = j and j:FindFirstChild("SetNetworkOwner")
    while snowballRagdollActive do
        local q = getSelectedTargetName()
        local j = G:FindFirstChild(q)
        if not j then
            task.wait(.12)
            continue
        end
        if not j or not j.Parent then break end
        local M = j.Character
        local d = M and ((M:FindFirstChild("UpperTorso") or M:FindFirstChild("Torso")))
        if not d then
            task.wait(.08)
            continue
        end
        pcall(function()
            local q = Vector3.new(math.random(-30, 30) / 100, math.random(-30, 30) / 100, math.random(-30, 30) / 100)
            local c = d.CFrame * CFrame.new(q)
            r:InvokeServer("BallSnowball", c, Vector3.zero)
        end)
        local z = Y:FindFirstChild(c.Name .. "SpawnedInToys")
        if z then for q, c in pairs(z:GetChildren()) do if c.Name == "BallSnowball" and c.Parent then
                    local q = c.PrimaryPart or c:FindFirstChildWhichIsA("BasePart")
                    if q then
                        if u then pcall(function() u:FireServer(q, q.CFrame) end) end
                        local c = Vector3.new(math.random(-30, 30) / 100, math.random(-30, 30) / 100,
                            math.random(-30, 30) / 100)
                        q.CFrame = d.CFrame * CFrame.new(c)
                        q.AssemblyLinearVelocity = Vector3.zero
                        q.AssemblyAngularVelocity = Vector3.zero
                    end
                end end end
        task.wait(.08)
    end
end

function LoopKickBlobFunction(q)
    if isSelfTargetName(q) then return end
    notifyLoopState("blobloop:" .. tostring(q), "Blob Loop started: " .. tostring(q), 2.8)
    local c = true
    local r = game:GetService("Players")
    local j = game:GetService("ReplicatedStorage")
    local u = game:GetService("RunService")
    local M = r.LocalPlayer
    local G = j:WaitForChild("GrabEvents")
    local d = .002
    local z = 0
    local function Y()
        local j = r:FindFirstChild(q)
        if not j then
            notifyLoopState("blobloop:" .. tostring(q), "Blob Loop stopped: target not found", 2.8)
            return warn("Target not found")
        end
        notifyOwnKick(q, "Blob Kick")
        drawKickLineToTarget(q, .9)
        local Y = M.Character or M.CharacterAdded:Wait()
        local P = Y:WaitForChild("Humanoid")
        local O = P.SeatPart
        if not O or O.Parent.Name ~= "CreatureBlobman" then
            notifyLoopState("blobloop:" .. tostring(q), "Blob Loop stopped: sit on Blobman first", 2.8)
            return warn("Sit on Blobman first")
        end
        local a = O.Parent
        local o = a:FindFirstChild("HumanoidRootPart") or a.PrimaryPart
        local v = a:WaitForChild("BlobmanSeatAndOwnerScript")
        local b = v:WaitForChild("CreatureGrab")
        local X = v:WaitForChild("CreatureDrop")
        local x = a:WaitForChild("RightDetector")
        local U = o.CFrame
        local T = false
        local e = 0
        local h = 0
        local V = math.clamp(tonumber(gN.kickLineTimeoutSeconds) or 8.5, 6.5, 13)
        local t = 2.4
        local B = 0
        while c and loopKickBlobActive do
            local c = r:FindFirstChild(q)
            if not c then break end
            Y = M.Character
            P = Y and Y:FindFirstChild("Humanoid")
            O = P and P.SeatPart
            if not O or O.Parent.Name ~= "CreatureBlobman" then
                notifyLoopState("blobloop:" .. tostring(q), "Blob Loop stopped: left Blobman", 2.8)
                warn("Stopped: left Blobman")
                break
            end
            local j = tick()
            V = math.clamp(tonumber(gN.kickLineTimeoutSeconds) or V, 6.5, 13)
            local v = getKickLinePulseAge(q)
            if v >= V and (j - B) >= t then
                B = j
                notifyLoopState("blobloopbug:" .. tostring(q), "Loop Kick bug detected: line timeout, auto recover", 3)
                handleLoopKickBug("blobloop:" .. tostring(q), q)
                T = false
                e = 0
                n = tick() + .35
                markKickLinePulse(q)
                drawKickLineToTarget(q, .85)
            end
            a = O.Parent
            o = a:FindFirstChild("HumanoidRootPart") or a.PrimaryPart
            local F = c.Character
            local l = F and F:FindFirstChild("HumanoidRootPart")
            local D = F and F:FindFirstChild("Humanoid")
            if l and (D and (D.Health > 0 and o)) then
                if tick() - h > .2 then
                    h = tick()
                    drawKickLineToTarget(q, .38)
                    markKickLinePulse(q)
                end
                l.Velocity = Vector3.zero
                if not T then
                    o.CFrame = l.CFrame
                    o.Velocity = Vector3.zero
                    if tick() - z >= d then
                        z = tick()
                        pcall(function()
                            D.PlatformStand = true
                            D.Sit = true
                            G.SetNetworkOwner:FireServer(l, o.CFrame)
                            G.DestroyGrabLine:FireServer(l)
                        end)
                    end
                    if e == 0 then e = tick() end
                    if tick() - e > .35 then
                        T = true
                        e = 0
                        o.CFrame = U
                        o.Velocity = Vector3.zero
                    end
                else
                    o.CFrame = U
                    o.Velocity = Vector3.zero
                    local q = U * CFrame.new(0, 23, 0)
                    l.CFrame = q
                    D.PlatformStand = true
                    D.Sit = true
                    if tick() - z >= d then
                        z = tick()
                        pcall(function()
                            G.SetNetworkOwner:FireServer(l, q)
                            G.DestroyGrabLine:FireServer(l)
                            local c = x:FindFirstChild("RightWeld") or x:FindFirstChildWhichIsA("Weld")
                            if c then
                                X:FireServer(c)
                                b:FireServer(x, l, c)
                            end
                        end)
                    end
                end
            else
                T = false
                e = 0
            end
            u.Heartbeat:Wait()
        end
        if o then
            o.CFrame = U
            o.Velocity = Vector3.zero
        end
        notifyLoopState("blobloop:" .. tostring(q), "Blob Loop finished: " .. tostring(q), 2.8)
    end
    task.spawn(Y)
end

function ServerLagLineFunction(q)
    local c = game:GetService("Players")
    local r = game:GetService("ReplicatedStorage")
    local j = (r:WaitForChild("GrabEvents")):WaitForChild("CreateGrabLine")
    while lagLineActive do
        for q = 1, q, 1 do for q, c in pairs(c:GetPlayers()) do if c.Character then
                    local q = c.Character:FindFirstChild("Torso") or c.Character:FindFirstChild("UpperTorso")
                    if q then j:FireServer(q, q.CFrame) end
                end end end
        task.wait(1)
    end
end

function setAntiGrabLimbState(q, c)
    if not q then return end
    for q, r in pairs(q:GetChildren()) do if r:IsA("BasePart") and (r.Name ~= "Head" and r:FindFirstChild("BallSocketConstraint")) then
            r.BallSocketConstraint.Enabled = false
            local q = r:FindFirstChild("RagdollLimbPart")
            local j = q and q:FindFirstChild("WeldConstraint")
            if j then j.Enabled = not c end
        end end
end

function releaseCharacterAnchors(q)
    if not q then return end
    for q, c in pairs(q:GetChildren()) do if c:IsA("BasePart") and c.Anchored then c.Anchored = false end end
end

function isHeadOwnedByOther(q)
    local c = q and q:FindFirstChild("PartOwner")
    if not c then return false end
    local r = tostring(c.Value or "")
    return r ~= "" and r ~= b.Name
end

function runAntiGrabRecover(q)
    if f then return end
    f = true
    i = i + 1
    local c = i
    task.spawn(function()
        local r = d:FindFirstChild("CharacterEvents") and d.CharacterEvents:FindFirstChild("Struggle")
        local j = d:FindFirstChild("CharacterEvents") and d.CharacterEvents:FindFirstChild("RagdollRemote")
        local u = b:FindFirstChild("IsHeld")
        local M = tick()
        while m and c == i do
            local c = q and (q.Parent and q) or b.Character
            local G = c and c:FindFirstChildOfClass("Humanoid")
            local d = c and c:FindFirstChild("HumanoidRootPart")
            local z = c and c:FindFirstChild("Head")
            if not c or not G or not d or G.Health <= 0 then break end
            local Y = u and u.Value
            local P = isHeadOwnedByOther(z)
            if (not Y) and ((not P) and (tick() - M) > .22) then break end
            setAntiGrabLimbState(c, true)
            G.PlatformStand = false
            G.Sit = false
            G.AutoRotate = true
            G.Jump = true
            if r then pcall(function() r:FireServer(b) end) end
            if j then pcall(function() j:FireServer(d, 0) end) end
            local O = d.AssemblyLinearVelocity
            d.AssemblyLinearVelocity = Vector3.new(0, math.max(O.Y, 16), 0)
            if G.MoveDirection.Magnitude > .05 then d.CFrame = d.CFrame + G.MoveDirection.Unit * .32 end
            if tick() - M > 2.8 then break end
            task.wait(.03)
        end
        f = false
        H = false
    end)
end

function bindAntiGrabCharacter(q)
    Disc("AGHead")
    Disc("AGRagdoll")
    Disc("AGWeld")
    Disc("AGHeld")
    if not q then return end
    local c = FWC(q, "Head", 2)
    local r = FWC(q, "Humanoid", 2)
    local j = FWC(q, "HumanoidRootPart", 2)
    if not c or not r or not j then return end
    setAntiGrabLimbState(q, true)
    E.AGHead = c.ChildAdded:Connect(function(c) if m and (c and c.Name == "PartOwner") then runAntiGrabRecover(q) end end)
    local u = FWC(r, "Ragdolled", 2)
    if u then E.AGRagdoll = u.Changed:Connect(function() if m then runAntiGrabRecover(q) end end) end
    local M = FWC(j, "WeldHRP", 2)
    if M then E.AGWeld = M.Changed:Connect(function() if m and M.Enabled then runAntiGrabRecover(q) end end) end
    local G = b:FindFirstChild("IsHeld")
    if G then E.AGHeld = G.Changed:Connect(function(c) if m and c then runAntiGrabRecover(q) end end) end
    if isHeadOwnedByOther(c) or (G and G.Value) then runAntiGrabRecover(q) end
end

function setAntiGrabState(q)
    m = q and true or false
    _G.AntiGrab = m
    i = i + 1
    f = false
    H = false
    Disc("AGHead")
    Disc("AGRagdoll")
    Disc("AGWeld")
    Disc("AGHeld")
    Disc("AGChar")
    if m then
        bindAntiGrabCharacter(b.Character)
        E.AGChar = b.CharacterAdded:Connect(function(q)
            task.wait(.15)
            bindAntiGrabCharacter(q)
        end)
    else
        local q = b.Character
        if q then
            setAntiGrabLimbState(q, false)
            releaseCharacterAnchors(q)
        end
    end
end

function disconnectBlizCompat(q)
    local c = VN.connections[q]
    if not c then return end
    if typeof(c) == "RBXScriptConnection" then c:Disconnect() else pcall(function() task.cancel(c) end) end
    VN.connections[q] = nil
end

function checkIfPlayerInRagdollAntiExplosion()
    local q = VN.ragdollState
    if q then q = _G.AntiExplosion end
    return q
end

function antivoidmesssage()
    local q = tick()
    if q - VN.antiVoidMessageCooldown < 2.5 then return end
    VN.antiVoidMessageCooldown = q
    c:Notify({ Title = "Wourld Hub", Description = "I saved you from falling on the void, my son!", Duration = 2.5 })
end

function applyBlizFireCleanup(q, c, r)
    local j = c and c:FindFirstChild("FirePlayerPart")
    if j then
        for q, c in ipairs(j:GetDescendants()) do if c:IsA("Sound") then c:Stop() elseif c:IsA("Light") or c:IsA("ParticleEmitter") then c.Enabled = false end end
        local q = j:FindFirstChild("CanBurn")
        if q and q:IsA("BoolValue") then q.Value = false end
    end
    if r and r:FindFirstChild("FireDebounce") then r.FireDebounce.Value = false end
end

function bindBlizCharacterRuntime(q)
    disconnectBlizCompat("Burn")
    disconnectBlizCompat("Ragdoll")
    disconnectBlizCompat("Humanoid")
    if not q then return end
    local c = q:FindFirstChildOfClass("Humanoid")
    local r = q:FindFirstChild("HumanoidRootPart")
    local j = q:FindFirstChild("Torso") or q:FindFirstChild("UpperTorso")
    if not c or not r then return end
    c.JumpPower = tonumber(_G.InfiniteJumpPower) or c.JumpPower
    if j then
        if VN.bodyVelocity and VN.bodyVelocity.Parent ~= j then
            pcall(function() VN.bodyVelocity:Destroy() end)
            VN.bodyVelocity = nil
        end
        if not VN.bodyVelocity then
            VN.bodyVelocity = Instance.new("BodyVelocity")
            VN.bodyVelocity.MaxForce = Vector3.new(0, 0, 0)
            VN.bodyVelocity.Velocity = Vector3.new(0, 0, 0)
            VN.bodyVelocity.Parent = j
        end
        _G.AntiExplosionVelocity = VN.bodyVelocity
    end
    local u = c:FindFirstChild("FireDebounce")
    if u then VN.connections.Burn = u.Changed:Connect(function(j) if j and _G.AntiBurn then while u.Value and (_G.AntiBurn and q.Parent) do
                    local j = r:FindFirstChild("FirePlayerPart")
                    local u = Y:FindFirstChild("apagarfogo", true) or Y:FindFirstChild("PoisonHurtPart", true)
                    if firetouchinterest and (j and (u and u:IsA("BasePart"))) then
                        firetouchinterest(j, u, 0)
                        task.wait()
                        firetouchinterest(j, u, 1)
                    else applyBlizFireCleanup(q, r, c) end
                    task.wait()
                end end end) end
    local M = c:FindFirstChild("Ragdolled")
    if M then VN.connections.Ragdoll = M.Changed:Connect(function(c)
            VN.ragdollState = c and true or false
            if c and _G.AntiExplosion then
                if _G.AntiExplosionVelocity then _G.AntiExplosionVelocity.MaxForce = Vector3.new(math.huge, -6200,
                        math.huge) end
                while M.Value and (_G.AntiExplosion and q.Parent) do
                    for c, j in ipairs({ "Head", "Right Arm", "Right Leg", "Left Arm", "Left Leg", "Torso" }) do
                        local u = q:FindFirstChild(j)
                        if u and u:IsA("BasePart") then
                            u.CanCollide = false
                            u.Massless = true
                            u.CFrame = r.CFrame
                        end
                        local M = u and u:FindFirstChild("RagdollLimbPart")
                        if M and M:IsA("BasePart") then M.CanCollide = false end
                    end
                    task.wait()
                end
                for c, r in ipairs({ "Head", "Right Arm", "Right Leg", "Left Arm", "Left Leg" }) do
                    local j = q:FindFirstChild(r)
                    if j and j:IsA("BasePart") then j.Massless = false end
                end
            elseif _G.AntiExplosionVelocity then _G.AntiExplosionVelocity.MaxForce = Vector3.new(0, 0, 0) end
        end) end
    VN.connections.Humanoid = c.Changed:Connect(function(q)
        if q == "Sit" and c.Sit == true then if c.SeatPart == nil and _G.AntiGrab then
                c:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
                c.Sit = false
            end end
        if q == "MoveDirection" and VN.bodyVelocity then VN.bodyVelocity.Velocity = c.MoveDirection * 20 end
    end)
end

function bindBlizHeldRuntime()
    disconnectBlizCompat("Held")
    local q = b:FindFirstChild("IsHeld")
    if not q then return end
    local c = d:FindFirstChild("CharacterEvents") and d.CharacterEvents:FindFirstChild("Struggle")
    local r = d:FindFirstChild("CharacterEvents") and d.CharacterEvents:FindFirstChild("RagdollRemote")
    VN.connections.Held = q.Changed:Connect(function(j)
        local u = b.Character
        local M = u and u:FindFirstChild("Head")
        local d = M and M:FindFirstChild("PartOwner")
        VN.heldName = d and tostring(d.Value or "") or ""
        local Y = G:FindFirstChild(VN.heldName)
        if j == true and (_G.AntiGrab and (((not Y) or Y ~= b))) then
            local j = ((b.Character or b.CharacterAdded:Wait())):FindFirstChild("HumanoidRootPart")
            if j and q.Value then
                local u = nil
                u = z.Heartbeat:Connect(function() if q.Value and _G.AntiGrab then
                        j.Velocity = Vector3.new()
                        j.Anchored = true
                        if _G.NoclipGrab then
                            local q = b.Character
                            if q then for q, c in ipairs(q:GetDescendants()) do if c:IsA("BasePart") then c.CanCollide = false end end end
                        end
                        if c then c:FireServer(b) end
                        if r then r:FireServer(j, 0) end
                    else
                        j.Velocity = Vector3.new()
                        j.Anchored = false
                        u:Disconnect()
                    end end)
            end
        end
    end)
end

function initBlizCompatRuntime()
    disconnectBlizCompat("Char")
    bindBlizHeldRuntime()
    bindBlizCharacterRuntime(b.Character)
    VN.connections.Char = b.CharacterAdded:Connect(function(q)
        task.wait(.2)
        bindBlizHeldRuntime()
        bindBlizCharacterRuntime(q)
    end)
    if not VN.connections.Jump then VN.connections.Jump = O.JumpRequest:Connect(function() if _G.InfiniteJump then
                local q = b.Character
                local c = q and q:FindFirstChildOfClass("Humanoid")
                if c then c:ChangeState(Enum.HumanoidStateType.Jumping) end
            end end) end
    if not VN.connections.Speed then VN.connections.Speed = z.Heartbeat:Connect(function() if _G.SuperSpeed then
                local q = b.Character
                local c = q and q:FindFirstChildOfClass("Humanoid")
                local r = q and q:FindFirstChild("HumanoidRootPart")
                if c and r then r.CFrame = r.CFrame + c.MoveDirection * ((tonumber((getgenv()).Multiplier) or .15)) end
            end end) end
end

initBlizCompatRuntime()
LeftGroupBox = w.Defence:AddLeftGroupbox("Anti Features")
RightGroupBox = w.Defence:AddRightGroupbox("Defense Tools")
LeftGroupBox:AddToggle("AntiGrabToggle",
    { Text = "Anti Grab", Default = false, Callback = function(q)
        _G.AntiGrab = q and true or false
        setAntiGrabState(q)
    end }); (RightGroupBox:AddLabel("Gucci Keybind")):AddKeyPicker("GucciKeyPicker",
    { Default = "J", Mode = "Press", Text = "Gucci Key", NoUI = false, Callback = function() GucciAntiGrab() end, ChangedCallback = function(
        q) gucciKey = normalizeRuntimeKeyCode(q, Enum.KeyCode.J) end })
LeftGroupBox:AddToggle("PlotBarriersToggle",
    { Text = "Anti Barrier", Default = false, Callback = function(q)
        local c = workspace:FindFirstChild("Plots")
        if not c then return end
        for c, r in ipairs(c:GetChildren()) do
            local j = r:FindFirstChild("Barrier")
            if j then for c, r in ipairs(j:GetChildren()) do if r:IsA("BasePart") and r.Name == "PlotBarrier" then r.CanCollide = not
                        q end end end
        end
    end })
LeftGroupBox:AddToggle("ShurikenAntiKickToggle",
    { Text = "Anti Kick", Default = false, Callback = function(q) setRagalicShurikenAntiKickState(q) end })
LeftGroupBox:AddToggle("AutoAntiKickResetToggle",
    { Text = "Auto Anti-Kick", Default = y6, Callback = function(q)
        y6 = q and true or false
        setKickThreatMonitorState(true)
    end })
LeftGroupBox:AddToggle("DefenceAutoRespawnToggle",
    { Text = "Auto Respawn", Default = w6, Callback = function(q) setAutoRespawnState(q) end })
LeftGroupBox:AddToggle("DefenceHeldResetToggle",
    { Text = "Reset On Grab Hold", Default = i6, Callback = function(q)
        i6 = q and true or false
        if not i6 then L6 = 0 end
    end })
LeftGroupBox:AddToggle("DefenceLeaveResetFailToggle",
    { Text = "Leave If Reset Fails", Default = m6, Callback = function(q) m6 = q and true or false end })
LeftGroupBox:AddToggle("DefenceLoopBugAutoLeaveToggle",
    { Text = "Auto Leave Loop Bug", Default = gN.loopBugAutoLeave, Callback = function(q) gN.loopBugAutoLeave = q and
        true or false end })
LeftGroupBox:AddToggle("AnyItemAntiKickToggle",
    { Text = "Anti Kick [Any Item]", Default = false, Callback = function(q) setAnyItemAntiKickState(q) end })
LeftGroupBox:AddDropdown("AnyItemAntiKickItem",
    { Text = "Anti Kick Item", Values = getAntiKickAnyItemOptions(), Default = r6, Callback = function(q)
        r6 = tostring(q or "AntiKick")
        if q6 then clearAntiKickAnyToys() end
    end })
LeftGroupBox:AddInput("AnyItemAntiKickCustomInput",
    { Default = r6, Numeric = false, Finished = true, Text = "Custom Anti Kick Item", Placeholder = "Any toy name", Callback = function(
        q)
        local c = ((tostring(q or "")):gsub("^%s+", "")):gsub("%s+$", "")
        if c ~= "" then
            r6 = c
            if q6 then clearAntiKickAnyToys() end
        end
    end })
LeftGroupBox:AddSlider("AnyItemAntiKickCount",
    { Text = "Anti Kick Count", Default = j6, Min = 1, Max = 3, Rounding = 0, Callback = function(q) j6 = math.clamp(
        tonumber(q) or 1, 1, 3) end })
LeftGroupBox:AddToggle("AntiLagToggle", { Text = "Anti Lag", Default = false, Callback = function(q) setAntiLagState(q) end })
RightGroupBox:AddButton({ Text = "Runtime Check & Repair", Func = function() runRuntimeRepairCheck("defense button") end, DoubleClick = false })
RightGroupBox:AddToggle("BreakPCLDToggle",
    { Text = "Break PCLD", Default = false, Callback = function(q)
        breakPcldActive = q and true or false
        if breakPcldTask then
            task.cancel(breakPcldTask)
            breakPcldTask = nil
        end
        if breakPcldActive then breakPcldTask = task.spawn(function() while breakPcldActive do
                    requestAutoRespawn("break pcld", 0)
                    task.wait(1.4)
                end end) end
    end })
RightGroupBox:AddToggle("AntiInputLagBurgerToggle",
    { Text = "Anti-INPUTLAG", Default = false, Callback = function(q) setAntiInputLagBurgerState(q) end })
RightGroupBox:AddToggle("AntiInputLagTestToggle",
    { Text = "Anti-INPUTLAG [TEST]", Default = false, Callback = function(q) setAntiInputLagTestState(q) end })
RightGroupBox:AddToggle("AntiInputLagAllItemsToggle",
    { Text = "Anti-INPUTLAG [ALL ITEMS]", Default = false, Callback = function(q)
        setRemoveAllAntiInputState(q)
        if M.RemoveAllAntiInputToggle and M.RemoveAllAntiInputToggle.Value ~= q then M.RemoveAllAntiInputToggle:SetValue(
            q) end
    end })
RightGroupBox:AddDropdown("AntiInputLagItemFilterDropdown",
    { Text = "Anti-INPUTLAG ITEM", Values = getAntiInputLagFilterOptions(true), Default = _G
    .WourldAntiInputLagItemFilter or "All", Callback = function(q)
        setAntiInputLagItemFilter(q)
        if antiAntiLagEnabled then
            setRemoveAllAntiInputState(false)
            task.defer(function() setRemoveAllAntiInputState(true) end)
        end
    end })
RightGroupBox:AddToggle("AntiInputLagDualItemToggle",
    { Text = "Anti-INPUTLAG [DUAL ITEM]", Default = false, Callback = function(q)
        uN = q and true or false
        bumpAntiInputLagRevision()
    end })
RightGroupBox:AddDropdown("AntiInputLagSecondItemFilterDropdown",
    { Text = "Anti-INPUTLAG ITEM #2", Values = getAntiInputLagFilterOptions(true), Default = _G
    .WourldAntiInputLagItemFilterSecondary or "FoodBread", Callback = function(q)
        _G.WourldAntiInputLagItemFilterSecondary = tostring(q or "FoodBread")
        bumpAntiInputLagRevision()
    end })
RightGroupBox:AddSlider("AntiInputLagSpeed",
    { Text = "Anti-INPUTLAG Speed (sec)", Default = math.clamp(tonumber(s) or .3, .1, 3), Min = .1, Max = 3, Rounding = 2, Callback = function(
        q)
        s = math.clamp(tonumber(q) or .3, .1, 3)
        bumpAntiInputLagRevision()
    end })
RightGroupBox:AddButton({ Text = "Refresh Anti-INPUT Items", Func = function()
    local q = getAntiInputLagFilterOptions(true)
    if u and (u.AntiInputLagItemFilterDropdown and u.AntiInputLagItemFilterDropdown.SetValues) then u
            .AntiInputLagItemFilterDropdown:SetValues(q) end
    if u and (u.AntiInputLagSecondItemFilterDropdown and u.AntiInputLagSecondItemFilterDropdown.SetValues) then u
            .AntiInputLagSecondItemFilterDropdown:SetValues(q) end
    c:Notify({ Title = "Wourld Hub", Description = "Anti-INPUT item list refreshed", Duration = 2.2 })
end, DoubleClick = false })
RightGroupBox:AddButton({ Text = "Bread ANTIKICK", Func = function() RunBreadAntiKick() end, DoubleClick = false })
function setCharacterBeamScriptDisabled(q)
    local c = G.LocalPlayer
    local r = c:FindFirstChild("PlayerScripts")
    local j = r and r:FindFirstChild("CharacterAndBeamMove")
    local u = q and true or false
    if j then if j.Disabled ~= u then j.Disabled = u end end
    t6 = u and true or false
end

function setAntiLagState(q)
    B6 = q and true or false
    if B6 then
        setCharacterBeamScriptDisabled(true)
        task.spawn(function() clearPacketVisuals(120, 1600) end)
    else if not v6 then setCharacterBeamScriptDisabled(false) end end
end

function isPacketVisualCandidate(q)
    if not q then return false end
    if isLocalOwnedInstance(q) then return false end
    local c = string.lower(q.Name or "")
    if q:IsA("Beam") or q:IsA("Trail") then return true end
    if c == "grabline" or c == "line" or c:find("packet", 1, true) or c:find("grabline", 1, true) then return true end
    return false
end

function destroyPacketVisual(q) pcall(function()
        if q:IsA("Beam") or q:IsA("Trail") then q.Enabled = false end
        q:Destroy()
    end) end

function clearPacketVisuals(q, c)
    local r = 0
    local j = 0
    local u = tonumber(q) or math.huge
    local M = tonumber(c) or 900
    local G = tick()
    if not e6 or G >= V6 then
        e6 = workspace:GetDescendants()
        h6 = 1
        V6 = G + 2.2
    end
    while e6 and h6 <= #e6 do
        local q = e6[h6]
        h6 = h6 + 1
        j = j + 1
        if isPacketVisualCandidate(q) then
            r = r + 1
            destroyPacketVisual(q)
        end
        if r >= u or j >= M then break end
    end
    if e6 and h6 > #e6 then
        e6 = nil
        h6 = 1
    end
    return r
end

function enqueueAntiPacketCandidate(q)
    if not v6 then return end
    if not q or T6[q] then return end
    T6[q] = true
    U6[#U6 + 1] = q
end

function processAntiPacketQueue(q)
    local c = 0
    local r = tonumber(q) or 20
    while c < r and #U6 > 0 do
        local q = #U6
        local r = U6[q]
        U6[q] = nil
        T6[r] = nil
        if r and (r.Parent and isPacketVisualCandidate(r)) then
            destroyPacketVisual(r)
            c = c + 1
        else c = c + 1 end
    end
    return c
end

function resetAntiPacketRuntime()
    U6 = {}
    T6 = {}
    e6 = nil
    h6 = 1
    V6 = 0
end

function setAntiPacketFilterState(q)
    if X6 then
        X6:Disconnect()
        X6 = nil
    end
    if q then X6 = workspace.DescendantAdded:Connect(function(q) enqueueAntiPacketCandidate(q) end) end
end

function StartAntiPacketsLoop()
    setAntiPacketFilterState(true)
    resetAntiPacketRuntime()
    x6 = 0
    clearPacketVisuals(120, 1800)
    while v6 do
        setCharacterBeamScriptDisabled(true)
        processAntiPacketQueue(28)
        local q = tick()
        if q - x6 >= 7 then
            x6 = q
            clearPacketVisuals(45, 650)
        end
        task.wait(.14)
    end
    setAntiPacketFilterState(false)
    resetAntiPacketRuntime()
    if not B6 then setCharacterBeamScriptDisabled(false) end
end

RightGroupBox:AddToggle("AntiExplosionToggle",
    { Text = "Anti Explode", Default = false, Callback = function(q)
        antiExplosionActive = q
        _G.AntiExplosion = q and true or false
        if q then StartAntiExplosion() else
            if YN then
                YN:Disconnect()
                YN = nil
            end
            local q = b.Character
            if q then
                local c = q:FindFirstChild("HumanoidRootPart")
                if c then c.Anchored = false end
            end
        end
    end })
RightGroupBox:AddToggle("AntiBurnToggle",
    { Text = "Anti Fire", Default = false, Callback = function(q)
        antiBurnActive = q
        _G.AntiBurn = q and true or false
        if q then StartAntiBurn() else if PN then
                PN:Disconnect()
                PN = nil
            end end
    end })
RightGroupBox:AddToggle("AntiGucciToggle",
    { Text = "Anti Gucci", Default = false, Callback = function(q)
        antiGucciActive = q
        n = tick() + 1
        if q then
            disableAutoSitBloomWithNotify("Anti Gucci")
            if not antiGucciTask then antiGucciTask = task.spawn(StartAntiGucciLoop) end
        else
            if antiGucciTask then
                task.cancel(antiGucciTask)
                antiGucciTask = nil
            end
            releaseGucciGrabState("Anti Gucci disabled, grab restored", true)
        end
    end })
RightGroupBox:AddToggle("GucciProtectToggle",
    { Text = "Gucci Protect", Default = false, Callback = function(q)
        gucciProtectActive = q
        if q then
            disableAutoSitBloomWithNotify("Gucci Protect")
            gucciProtectSeenAny = false
            gucciProtectLastSeen = tick()
            if not gucciProtectTask then gucciProtectTask = task.spawn(StartGucciProtectLoop) end
        else
            gucciProtectSeenAny = false
            if gucciProtectTask then
                task.cancel(gucciProtectTask)
                gucciProtectTask = nil
            end
            releaseGucciGrabState("Gucci Protect disabled, grab restored", true)
        end
    end })
RightGroupBox:AddSlider("AntiGucciRadius",
    { Text = "Anti Gucci Radius", Default = antiGucciRadius, Min = 20, Max = 160, Rounding = 0, Callback = function(q) antiGucciRadius =
        q end })
RightGroupBox:AddToggle("AntiRagBlobToggle",
    { Text = "Anti Blobman", Default = false, Callback = function(q)
        antiRagBlobActive = q
        if q then disableAutoSitBloomWithNotify("Anti Blobman") end
        AntiRagBlobFunction()
    end })
RightGroupBox:AddToggle("AntiEnemyBlobDenyToggle",
    { Text = "Anti Enemy Blob", Default = false, Callback = function(q)
        setAntiBlobDenyState(q)
        if q then disableAutoSitBloomWithNotify("Anti Enemy Blob") end
    end })
RightGroupBox:AddDropdown("AntiEnemyBlobModeDropdown",
    { Text = "Enemy Blob Mode", Values = { "UnderMap", "Fling", "Both" }, Default = antiBlobDenyMode, Callback = function(
        q) antiBlobDenyMode = tostring(q or "UnderMap") end })
RightGroupBox:AddSlider("AntiEnemyBlobRadius",
    { Text = "Enemy Blob Radius", Default = antiBlobDenyRadius, Min = 40, Max = 600, Rounding = 0, Callback = function(q) antiBlobDenyRadius =
        math.clamp(tonumber(q) or 220, 40, 600) end })
TargetLeft = w.Target:AddLeftGroupbox("Target Selection")
TargetRight = w.Target:AddRightGroupbox("Blobman Features")
KickSection = w.Target:AddRightGroupbox("Kick Methods")
XZSection = w.Target:AddRightGroupbox("Combat")
TargetExtra = w.Target:AddLeftGroupbox("Extra Tools")
TraceSection = w.Target:AddLeftGroupbox("Trace Visual")
GrabLineModsBox = w.Target:AddRightGroupbox("Grab/Line Mods")
KickSection:AddSlider("KickLineTimeoutSlider",
    { Text = "Kick Line Timeout (s)", Default = math.floor(((tonumber(gN.kickLineTimeoutSeconds) or 8.5)) * 10), Min = 65, Max = 130, Rounding = 0, Callback = function(
        q) gN.kickLineTimeoutSeconds = math.clamp(((tonumber(q) or 85)) / 10, 6.5, 13) end })
local Fa = refreshTargetPlayerDropdown(false)
TargetLeft:AddDropdown("TargetPlayer",
    { Text = "Target Player", Values = Fa, Default = Fa[1] or nil, Callback = function(q) SetTrackedTarget(q) end })
TargetLeft:AddInput("FindByNickPartialInput",
    { Default = "", Numeric = false, Finished = true, Text = "Find By Nick [PARTIAL]", Placeholder = "Type nick/display", Callback = function(
        q) if q and q ~= "" then
            local r = selectTargetByPartialNick(q)
            if not r then c:Notify({ Title = "Wourld Hub", Description = "Player not found by partial nick", Duration = 2.4 }) end
        end end })
TargetLeft:AddButton({ Text = "Refresh List", Func = function() refreshTargetPlayerDropdown(true) end, DoubleClick = false })
TargetLeft:AddButton({ Text = "Smart Pick Target", Func = function() SelectSmartTarget() end, DoubleClick = false })
TargetExtra:AddToggle("AuraShieldToggle", { Text = "Aura Shield", Default = false, Callback = function(q)
    SetAuraShieldState(q) end })
TargetExtra:AddSlider("AuraShieldRadiusSlider",
    { Text = "Aura Radius", Default = math.floor(tonumber(gN.auraShieldRadius) or 28), Min = 8, Max = 90, Rounding = 0, Callback = function(
        q) gN.auraShieldRadius = math.clamp(tonumber(q) or 28, 8, 90) end })
TargetExtra:AddToggle("AuraShieldGrabAlertToggle",
    { Text = "Grab Aura Alert", Default = true, Callback = function(q) gN.auraShieldGrabAlert = q and true or false end })
TargetExtra:AddToggle("AuraShieldKickAlertToggle",
    { Text = "Kick Aura Alert", Default = true, Callback = function(q) gN.auraShieldKickAlert = q and true or false end })
TargetExtra:AddToggle("AuraShieldKillAlertToggle",
    { Text = "Kill Aura Alert", Default = true, Callback = function(q) gN.auraShieldKillAlert = q and true or false end })
GrabLineModsBox:AddToggle("GrabPoisonModToggle",
    { Text = "Poison Grab", Default = false, Callback = function(q)
        grabPoisonActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("GrabRadioactiveModToggle",
    { Text = "Radioactive Grab", Default = false, Callback = function(q)
        grabRadioactiveActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("GrabBurnModToggle",
    { Text = "Burn Grab", Default = false, Callback = function(q)
        grabBurnActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("GrabKillModToggle",
    { Text = "Kill Grab", Default = false, Callback = function(q)
        grabKillActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("GrabFlingModToggle",
    { Text = "Fling Grab", Default = false, Callback = function(q)
        grabFlingActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("GrabNoclipModToggle",
    { Text = "Noclip Grab Mod", Default = false, Callback = function(q)
        grabNoclipModActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("GrabCrazyModToggle",
    { Text = "Crazy Grab", Default = false, Callback = function(q)
        grabCrazyActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("GrabSpinModToggle",
    { Text = "Spin Grab", Default = false, Callback = function(q)
        grabSpinActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("GrabUltraModToggle",
    { Text = "Ultra Grab", Default = false, Callback = function(q)
        grabUltraActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("UltraClickGrabModToggle",
    { Text = "Ultra Click Grab", Default = false, Callback = function(q)
        ultraClickGrabActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("LineInvisibleModToggle",
    { Text = "Invisible Line", Default = false, Callback = function(q)
        lineInvisibleActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("LineExtendModToggle",
    { Text = "Extend Line", Default = false, Callback = function(q)
        lineExtendActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("LineCrazyPlayersModToggle",
    { Text = "Crazy Line (Players)", Default = false, Callback = function(q)
        lineCrazyPlayersActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("LineCrazyPartsModToggle",
    { Text = "Crazy Line (All Parts)", Default = false, Callback = function(q)
        lineCrazyAllPartsActive = q and true or false
        refreshGrabLineModsLoop()
    end })
GrabLineModsBox:AddToggle("LineCrazyToysModToggle",
    { Text = "Crazy Line (All Toys)", Default = false, Callback = function(q)
        lineCrazyAllToysActive = q and true or false
        refreshGrabLineModsLoop()
    end })
TargetLeft:AddButton({ Text = "Target Back", Func = function()
    local q = gN.targetHistory
    if #q == 0 then return end
    gN.targetHistoryIndex = math.max(1, ((gN.targetHistoryIndex or #q)) - 1)
    local c = q[gN.targetHistoryIndex]
    if c and u.TargetPlayer then
        u.TargetPlayer:SetValue(c)
        SetTrackedTarget(c)
    end
end, DoubleClick = false })
TargetLeft:AddButton({ Text = "Target Forward", Func = function()
    local q = gN.targetHistory
    if #q == 0 then return end
    gN.targetHistoryIndex = math.min(#q, ((gN.targetHistoryIndex or #q)) + 1)
    local c = q[gN.targetHistoryIndex]
    if c and u.TargetPlayer then
        u.TargetPlayer:SetValue(c)
        SetTrackedTarget(c)
    end
end, DoubleClick = false })
TargetLeft:AddToggle("SmartTargetAutoToggle",
    { Text = "Auto Retarget", Default = false, Callback = function(q)
        gN.autoRetarget = q
        SetSmartTargetLoopState(q or gN.distanceLock)
    end })
TargetLeft:AddToggle("DistanceLockToggle",
    { Text = "Distance Lock", Default = false, Callback = function(q)
        gN.distanceLock = q
        SetSmartTargetLoopState(q or gN.autoRetarget)
    end })
TargetLeft:AddSlider("DistanceLockRange",
    { Text = "Distance Range", Default = 16, Min = 6, Max = 40, Rounding = 0, Callback = function(q) gN.distanceRange = q end })
TargetLeft:AddToggle("ReturnAfterActionToggle",
    { Text = "Return After Action", Default = false, Callback = function(q) gN.returnAfterAction = q end })
TargetLeft:AddSlider("ReturnAfterActionDelay",
    { Text = "Return Delay", Default = 45, Min = 5, Max = 200, Rounding = 0, Callback = function(q) gN.returnDelay = q end })
if #Fa > 0 then task.defer(function() SetTrackedTarget(Fa[1]) end) end
TargetExtra:AddToggle("DestroyGucciToggle",
    { Text = "Destroy Gucci", Default = false, Callback = function(q)
        destroyGucciActive = q
        if q then
            disableAutoSitBloomWithNotify("Destroy Gucci")
            bN = task.spawn(StartDestroyGucciLoop)
        else if bN then
                task.cancel(bN)
                bN = nil
            end end
    end })
TargetExtra:AddToggle("KickAuraToggle", { Text = "Kick Aura", Default = false, Callback = function(q) setKickAuraState(q) end })
TargetExtra:AddSlider("KickAuraRadius",
    { Text = "Kick Aura Radius", Default = sN, Min = 6, Max = 35, Rounding = 0, Callback = function(q) sN = q end })
TargetExtra:AddSlider("KickAuraCooldown",
    { Text = "Kick Aura Cooldown", Default = math.floor(CN * 10), Min = 1, Max = 30, Rounding = 0, Callback = function(q) CN =
        math.max(.1, q / 10) end })
TargetExtra:AddDropdown("KickAuraTypeMode",
    { Text = "Kick Aura Type", Values = { "Silent", "Float", "Sky Anchor" }, Default = "Silent", Callback = function(q) _G.KickAuraType =
        tostring(q or "Silent") end })
TargetExtra:AddToggle("LoopExplosionToggle",
    { Text = "Loop Explosion Mix", Default = gN.loopExplosionActive, Callback = function(q) setLoopExplosionState(q) end })
TargetExtra:AddDropdown("LoopExplosionItemA",
    { Text = "Explosion Item A", Values = { "BallSnowball", "CrystalSnowman", "ToyBomb", "BalloonBlue", "NinjaShuriken", "FoodCoconut", "FoodBurger", "PalletLightBrown" }, Default =
    gN.loopExplosionItemA, Callback = function(q) gN.loopExplosionItemA = tostring(q or gN.loopExplosionItemA) end })
TargetExtra:AddDropdown("LoopExplosionItemB",
    { Text = "Explosion Item B", Values = { "CrystalSnowman", "BallSnowball", "ToyBomb", "BalloonBlue", "NinjaShuriken", "FoodCoconut", "FoodBurger", "PalletLightBrown" }, Default =
    gN.loopExplosionItemB, Callback = function(q) gN.loopExplosionItemB = tostring(q or gN.loopExplosionItemB) end })
TargetExtra:AddDropdown("LoopExplosionMode",
    { Text = "Explosion Mode", Values = { "Alternate", "Together", "Single" }, Default = gN.loopExplosionMode, Callback = function(
        q) gN.loopExplosionMode = tostring(q or "Alternate") end })
TargetExtra:AddSlider("LoopExplosionBurst",
    { Text = "Explosion Burst", Default = gN.loopExplosionBurst, Min = 1, Max = 8, Rounding = 0, Callback = function(q) gN.loopExplosionBurst =
        math.clamp(tonumber(q) or 2, 1, 8) end })
TargetExtra:AddSlider("LoopExplosionVelocity",
    { Text = "Explosion Velocity", Default = gN.loopExplosionVelocity, Min = 50, Max = 3200, Rounding = 0, Callback = function(
        q) gN.loopExplosionVelocity = math.clamp(tonumber(q) or 540, 50, 3200) end })
TargetExtra:AddSlider("LoopExplosionInterval",
    { Text = "Explosion Interval", Default = math.floor(((tonumber(gN.loopExplosionInterval) or .22)) * 100), Min = 8, Max = 250, Rounding = 0, Callback = function(
        q) gN.loopExplosionInterval = math.clamp(((tonumber(q) or 22)) / 100, .08, 2.5) end })
TargetExtra:AddButton({ Text = "Explosion Pulse Once", Func = function()
    local q = getSelectedTargetName()
    local c = G:FindFirstChild(q)
    local r = c and (c.Character and c.Character:FindFirstChild("HumanoidRootPart"))
    if r then
        fireLoopExplosionItemAtTarget(gN.loopExplosionItemA, r, gN.loopExplosionVelocity)
        if tostring(gN.loopExplosionMode or "") == "Together" then fireLoopExplosionItemAtTarget(gN.loopExplosionItemB, r,
                gN.loopExplosionVelocity) end
    end
end, DoubleClick = false })
TargetExtra:AddToggle("RemoveAllAntiInputToggle",
    { Text = "Remove Anti Input", Default = false, Callback = function(q)
        setRemoveAllAntiInputState(q)
        if M.AntiInputLagAllItemsToggle and M.AntiInputLagAllItemsToggle.Value ~= q then M.AntiInputLagAllItemsToggle
                :SetValue(q) end
    end })
TargetExtra:AddToggle("TargetNotifyToggle",
    { Text = "Leave/Join Target Notify", Default = false, Callback = function(q) if q then
            local q = u.TargetPlayer.Value
            if not q or q == "" then
                M.TargetNotifyToggle:SetValue(false)
                return
            end
            local r = G:FindFirstChild(q)
            if r then c:Notify({ Title = "Wourld Hub", Description = r.DisplayName ..
                (" (" .. (r.Name .. ") is currently in game")), Duration = 3 }) end
            X.Added = G.PlayerAdded:Connect(function(q) if q.Name == u.TargetPlayer.Value then c:Notify({ Title =
                    "Wourld Hub", Description = q.DisplayName .. (" (" .. (q.Name .. ") Joined")), Duration = 3 }) end end)
            X.Removing = G.PlayerRemoving:Connect(function(q) if q.Name == u.TargetPlayer.Value then c:Notify({ Title =
                    "Wourld Hub", Description = q.DisplayName .. (" (" .. (q.Name .. ") Left")), Duration = 3 }) end end)
        else
            for q, c in pairs(X) do if c then c:Disconnect() end end
            X = {}
        end end })
TargetRight:AddToggle("RemoveAntiKickToggle",
    { Text = "Remove Anti Kick", Default = false, Callback = function(q)
        antiAntiKickActive = q
        if q then
            local q = u.TargetPlayer.Value
            if q and q ~= "" then oN = task.spawn(function() RemoveAntiKickFunction(q) end) else antiAntiKickActive = false end
        else if oN then
                task.cancel(oN)
                oN = nil
            end end
    end })
TargetRight:AddToggle("AutoSitBlobmanToggle",
    { Text = "Auto Sit Bloom", Default = false, Callback = function(q)
        if q then
            local q = getAutoSitConflictReason()
            if q then
                disableAutoSitBloomWithNotify(q)
                return
            end
        end
        _G.AutoSitBlobZ = q and true or false
        _G.AutoSitBloom = q and true or false
        if q then task.spawn(AutoSitLoop) else S6 = S6 + 1 end
    end })
TargetRight:AddDropdown("MethodSelect",
    { Text = "Selected Method", Values = { "Bring", "Loop Kick", "Bypass", "Kick", "Kick Blob", "Loop Kick (Grab+Blob)", "Blob Kill", "Lock" }, Default =
    "Bring", Callback = function(q) end })
function BlobKill(q)
    local c = game:GetService("Players")
    local r = c.LocalPlayer
    local j = game:GetService("ReplicatedStorage")
    local u = function(q, c, r) return q:FindFirstChild(c) or q:WaitForChild(c, r or 3) end
    local M = function(q) j.GrabEvents.SetNetworkOwner:FireServer(q, q.CFrame) end
    local G = function(q, c, r, j)
        local u = q:FindFirstChild(r .. "Detector")
        if not u then return end
        local M = q.BlobmanSeatAndOwnerScript
        if j == "Default" then M.CreatureGrab:FireServer(u, c, u[r .. "Weld"]) elseif j == "DDrop" then M.CreatureDrop
                :FireServer(u[r .. "Weld"]) elseif j == "Release" then M.CreatureRelease:FireServer(u[r .. "Weld"], c) end
    end
    local d = true
    local z
    while true do
        local q = r.Character or r.CharacterAdded:Wait()
        local c = u(q, "Humanoid")
        if c.SeatPart then
            z = c.SeatPart.Parent
            break
        end
        task.wait()
    end
    while d and task.wait() do
        local j = r.Character or r.CharacterAdded:Wait()
        local Y = u(j, "HumanoidRootPart")
        local P = u(j, "Humanoid")
        if not P.SeatPart then
            d = false
            print("Stopped: left Blob")
            break
        end
        if P.SeatPart.Parent ~= z then
            d = false
            print("Stopped: changed Blob")
            break
        end
        local O = c:FindFirstChild(q)
        if not O then continue end
        local a = O.Character
        if not a then continue end
        local o = u(a, "Humanoid", 2)
        local v = u(a, "HumanoidRootPart", 2)
        if not ((o and v)) then continue end
        if o.Health == 0 then
            a = O.CharacterAdded:Wait()
            o = u(a, "Humanoid", 2)
            v = u(a, "HumanoidRootPart", 2)
            task.wait(.15)
            if not ((o and v)) then continue end
        end
        if not ((d and (z and z.Parent))) then continue end
        local b = z:FindFirstChild("LeftDetector")
        local X = b and b:FindFirstChild("LeftWeld")
        if b and X then while X.Attachment0 ~= v.RootAttachment and d do
                local q = Y.CFrame
                while o.SeatPart do
                    task.spawn(M, v)
                    task.wait()
                end
                for q = 1, 4, 1 do
                    if not P.SeatPart then
                        d = false
                        break
                    end
                    Y.CFrame = v.CFrame - Vector3.new(0, 10, 0)
                    G(z, v, "Left", "Default")
                    task.wait(.05)
                    G(z, v, "Left", "Release")
                    o.Health = 0
                    task.wait()
                end
                if not d then break end
                Y.CFrame = q
            end end
    end
end

function BlobHeal(q)
    local c = game:GetService("Players")
    local r = c.LocalPlayer
    local j = game:GetService("ReplicatedStorage")
    local u = function(q, c, r) return q:FindFirstChild(c) or q:WaitForChild(c, r or 3) end
    local M = function(q) j.GrabEvents.SetNetworkOwner:FireServer(q, q.CFrame) end
    local G = function(q, c, r, j)
        local u = q:FindFirstChild(r .. "Detector")
        if not u then return end
        local M = q.BlobmanSeatAndOwnerScript
        if j == "Default" then M.CreatureGrab:FireServer(u, c, u[r .. "Weld"]) elseif j == "DDrop" then M.CreatureDrop
                :FireServer(u[r .. "Weld"]) elseif j == "Release" then M.CreatureRelease:FireServer(u[r .. "Weld"], c) end
    end
    local d
    while true do
        local q = r.Character or r.CharacterAdded:Wait()
        local c = u(q, "Humanoid")
        if c.SeatPart then
            d = c.SeatPart.Parent
            break
        end
        task.wait()
    end
    local function z()
        local j = r.Character or r.CharacterAdded:Wait()
        local z = u(j, "HumanoidRootPart")
        local Y = u(j, "Humanoid")
        if not Y.SeatPart then
            print("Not sitting on Blob!")
            return false
        end
        if Y.SeatPart.Parent ~= d then
            print("Changed Blob!")
            return false
        end
        local P = c:FindFirstChild(q)
        if not P then return false end
        local O = P.Character
        if not O then return false end
        local a = O:FindFirstChild("Humanoid")
        local o = O:FindFirstChild("HumanoidRootPart")
        if not ((a and o)) then return false end
        if not ((d and d.Parent)) then return false end
        local v = d:FindFirstChild("LeftDetector")
        local b = v and v:FindFirstChild("LeftWeld")
        if not ((v and b)) then return false end
        local X = z.CFrame
        task.spawn(M, o)
        task.wait(.1)
        for q = 1, 3, 1 do
            if not Y.SeatPart then return false end
            z.CFrame = o.CFrame * CFrame.new(0, 0, -2.5)
            G(d, o, "Left", "Default")
            task.wait(.08)
            G(d, o, "Left", "Release")
            a.Health = a.MaxHealth
            task.wait(.08)
        end
        z.CFrame = X
        print("Heal completed for " .. q)
        return true
    end
    local Y = z()
    if not Y then
        task.wait(.5)
        z()
    end
end

function Bring(q)
    local c = (d:WaitForChild("GrabEvents")):WaitForChild("SetNetworkOwner")
    local r = G:FindFirstChild(q)
    if not r then return end
    local j = b.Character or b.CharacterAdded:Wait()
    local u = j:WaitForChild("Humanoid")
    local M = j:WaitForChild("HumanoidRootPart")
    local z = u.SeatPart
    if not z or not r or r == b then return end
    local Y = z.Parent
    local P = r.Character or r.CharacterAdded:Wait()
    local O = P:WaitForChild("HumanoidRootPart")
    local a = Y:WaitForChild("LeftDetector")
    local o = a:WaitForChild("LeftWeld")
    local v = Y.BlobmanSeatAndOwnerScript:WaitForChild("CreatureGrab")
    local X = M.CFrame
    local x = {}
    for q, c in ipairs(j:GetDescendants()) do if c:IsA("BasePart") then
            x[c] = c.Transparency
            c.Transparency = 1
        end end
    local U = workspace.CurrentCamera
    local T = U.CFrame
    U.CameraType = Enum.CameraType.Scriptable
    U.CFrame = T
    M.CFrame = O.CFrame * CFrame.new(0, 0, 2.5)
    task.wait()
    v:FireServer(a, O, o)
    task.delay(.1, function() v:FireServer(a, O, o) end)
    task.delay(.2,
        function()
            M.CFrame = X
            for q, c in pairs(x) do if q and q.Parent then q.Transparency = c end end
            U.CameraType = Enum.CameraType.Custom
            U.CameraSubject = u
        end)
end

function restoreLocalAfterKick(q)
    local c = b.Character
    if not c then return end
    local r = c:FindFirstChildOfClass("Humanoid")
    local j = c:FindFirstChild("HumanoidRootPart")
    if r then
        r.PlatformStand = false
        r.AutoRotate = true
        if r.Health > 0 then pcall(function() r:ChangeState(Enum.HumanoidStateType.Running) end) end
    end
    if j then
        j.Anchored = false
        j.AssemblyLinearVelocity = Vector3.zero
        j.AssemblyAngularVelocity = Vector3.zero
        if q and ((j.Position - q.Position)).Magnitude > 120 then j.CFrame = q end
    end
end

function Kick(q)
    if isSelfTargetName(q) then return false end
    local c = G:FindFirstChild(q)
    if not c or c == b then return false end
    local r = b.Character or b.CharacterAdded:Wait()
    local j = r:FindFirstChildOfClass("Humanoid")
    local u = r:FindFirstChild("HumanoidRootPart")
    if not j or not u then return false end
    local M = j.SeatPart
    if not M then return false end
    local d = M.Parent
    local z = d and d:FindFirstChild("BlobmanSeatAndOwnerScript")
    local Y = d and d:FindFirstChild("LeftDetector")
    local P = Y and Y:FindFirstChild("LeftWeld")
    local O = z and z:FindFirstChild("CreatureGrab")
    local a = z and z:FindFirstChild("CreatureDrop")
    if not ((d and (z and (Y and (P and (O and a)))))) then return false end
    local o = c.Character or c.CharacterAdded:Wait()
    local v = o and o:FindFirstChild("HumanoidRootPart")
    if not v or o == r or v == u then return false end
    kickActionBusy = true
    kickSelfGuardUntil = tick() + 2.6
    n = tick() + 1
    task.delay(4, function() kickActionBusy = false end)
    local X = u.CFrame
    local x = nil
    drawKickLineToTarget(q, .6)
    pcall(function()
        u.CFrame = v.CFrame * CFrame.new(0, 0, 3)
        task.wait(.09)
        for q = 1, 2, 1 do
            O:FireServer(Y, v, P)
            task.wait(.045)
            a:FireServer(P, v)
            task.wait(.04)
        end
        x = Instance.new("BodyVelocity")
        x.Name = "WourldKickVelocity"
        x.MaxForce = Vector3.new(100000000.0, 100000000.0, 100000000.0)
        x.Velocity = Vector3.new(0, 280, 0)
        x.Parent = v
        applyKickAuraTypeEffect(v)
        task.wait(.07)
        O:FireServer(Y, v, P)
        task.wait(.035)
        a:FireServer(P, v)
    end)
    if x then pcall(function() x:Destroy() end) end
    pcall(function() a:FireServer(P, v) end)
    restoreLocalAfterKick(X)
    pcall(function() forceReleaseHeldItems(2000) end)
    kickActionBusy = false
    notifyOwnKick(q, "Kick")
    return true
end

function LoopKick(q)
    if isSelfTargetName(q) then return false end
    local c = G:FindFirstChild(q)
    if not c or c == b then return false end
    local r = b.Character or b.CharacterAdded:Wait()
    local j = r:FindFirstChildOfClass("Humanoid")
    local u = r:FindFirstChild("HumanoidRootPart")
    if not j or not u then return false end
    local M = j.SeatPart
    if not M then return false end
    local d = M.Parent
    local z = d and d:FindFirstChild("BlobmanSeatAndOwnerScript")
    local Y = d and d:FindFirstChild("LeftDetector")
    local P = Y and Y:FindFirstChild("LeftWeld")
    local O = d and d:FindFirstChild("RightDetector")
    local a = O and O:FindFirstChild("RightWeld")
    local o = z and z:FindFirstChild("CreatureGrab")
    local v = z and z:FindFirstChild("CreatureDrop")
    if not ((Y and (P and (O and (a and (o and v)))))) then return false end
    local X = c.Character or c.CharacterAdded:Wait()
    local x = X and X:FindFirstChildOfClass("Humanoid")
    local U = X and X:FindFirstChild("HumanoidRootPart")
    if not ((x and U)) or X == r or U == u then return false end
    notifyLoopState("loopkick:" .. tostring(q), "Loop Kick started: " .. tostring(q), 2.4)
    kickActionBusy = true
    kickSelfGuardUntil = tick() + 3.2
    n = tick() + 1
    task.delay(6, function() kickActionBusy = false end)
    local T = u.CFrame
    local e = nil
    local h = 0
    local V = math.clamp(tonumber(gN.kickLineTimeoutSeconds) or 8.5, 6.5, 13)
    local t = 2.4
    local B = 0
    drawKickLineToTarget(q, .8)
    pcall(function()
        u.CFrame = U.CFrame * CFrame.new(0, -5, 0)
        task.wait(.05)
        e = Instance.new("BodyPosition")
        e.Name = "WourldLoopKickBody"
        e.MaxForce = Vector3.new(7000000.0, 7000000.0, 7000000.0)
        e.Position = Vector3.new(0, 100000.0, 0)
        e.Parent = U
        applyKickAuraTypeEffect(U)
        local r = 0
        while c.Parent and (x.Health > 0 and r < 90) do
            local c = tick()
            V = math.clamp(tonumber(gN.kickLineTimeoutSeconds) or V, 6.5, 13)
            if c - h >= .35 then
                h = c
                drawKickLineToTarget(q, .45)
                markKickLinePulse(q)
            end
            local j = getKickLinePulseAge(q)
            if j >= V and (c - B) >= t then
                B = c
                notifyLoopState("loopkickbug:" .. tostring(q), "Loop Kick bug detected: line timeout, auto recover", 3)
                handleLoopKickBug("loopkick:" .. tostring(q), q)
                pcall(function()
                    v:FireServer(P, U)
                    v:FireServer(a, U)
                end)
                n = tick() + .4
                drawKickLineToTarget(q, .95)
                markKickLinePulse(q)
            end
            r = r + 1
            o:FireServer(Y, U, P)
            task.wait(.015)
            v:FireServer(P, U)
            task.wait(.015)
            o:FireServer(O, U, a)
            task.wait(.015)
            v:FireServer(a, U)
            task.wait(.015)
        end
    end)
    if e then pcall(function() e:Destroy() end) end
    pcall(function() v:FireServer(P, U) end)
    pcall(function() v:FireServer(a, U) end)
    if U and U.Parent then pcall(function()
            U.AssemblyLinearVelocity = Vector3.new(0, 300, 0)
            U.AssemblyAngularVelocity = Vector3.zero
        end) end
    restoreLocalAfterKick(T)
    pcall(function() forceReleaseHeldItems(2000) end)
    if x and (x.Parent and x.Health > 0) then
        task.wait(.05)
        Kick(q)
    end
    kickActionBusy = false
    notifyOwnKick(q, "Loop Kick")
    notifyLoopState("loopkick:" .. tostring(q), "Loop Kick finished: " .. tostring(q), 2.4)
    return true
end

function QuickNoBlobKick(q)
    if not q or q == "" then return false end
    if isSelfTargetName(q) then return false end
    local c = G:FindFirstChild(q)
    if not c or c == b then return false end
    local r = b.Character
    local j = r and r:FindFirstChildOfClass("Humanoid")
    local u = r and r:FindFirstChild("HumanoidRootPart")
    if not r or not j or not u or j.Health <= 0 then return false end
    local M = c.Character
    local z = M and M:FindFirstChildOfClass("Humanoid")
    local Y = M and M:FindFirstChild("HumanoidRootPart")
    if not M or not z or not Y or z.Health <= 0 then return false end
    local P = d:FindFirstChild("GrabEvents")
    local O = P and P:FindFirstChild("SetNetworkOwner")
    if not O then return false end
    local a = P:FindFirstChild("CreateGrabLine")
    local o = P:FindFirstChild("DestroyGrabLine")
    local v = u.CFrame
    local X = nil
    local x = false
    local U = false
    kickActionBusy = true
    kickSelfGuardUntil = tick() + 2.2
    n = tick() + .9
    drawKickLineToTarget(q, .55)
    pcall(function()
        u.CFrame = Y.CFrame * CFrame.new(0, 0, 2.6)
        u.AssemblyLinearVelocity = Vector3.zero
        u.AssemblyAngularVelocity = Vector3.zero
        task.wait(.03)
        for q = 1, 7, 1 do
            if claimPartOwnershipFast(Y, O, .08) then U = true else pcall(function() O:FireServer(Y, Y.CFrame) end) end
            if a then a:FireServer(Y, Vector3.zero, Y.Position, false) end
            if o then o:FireServer(Y) end
        end
        if not U then return end
        X = Instance.new("BodyVelocity")
        X.Name = "WourldQuickKickVelocity"
        X.MaxForce = Vector3.new(200000000.0, 200000000.0, 200000000.0)
        X.Velocity = Vector3.new(0, 320, 0)
        X.Parent = Y
        pcall(function()
            Y.AssemblyLinearVelocity = Vector3.new(0, 320, 0)
            Y.AssemblyAngularVelocity = Vector3.new(0, 25, 0)
        end)
        applyKickAuraTypeEffect(Y)
        x = U and true or false
    end)
    if X then task.delay(.16, function() pcall(function() X:Destroy() end) end) end
    restoreLocalAfterKick(v)
    kickActionBusy = false
    if x then notifyOwnKick(q, "Quick Kick") end
    return x
end

function Bypass(q)
    local c = G:FindFirstChild(q)
    if not c or c == b then return end
    local r = b.Character or b.CharacterAdded:Wait()
    local j = r:WaitForChild("Humanoid")
    local u = r:WaitForChild("HumanoidRootPart")
    local d = j.SeatPart
    if not d then return end
    local z = d.Parent
    local Y = c.Character or c.CharacterAdded:Wait()
    local P = Y:WaitForChild("Humanoid")
    local O = Y:WaitForChild("HumanoidRootPart")
    local a = z:WaitForChild("LeftDetector")
    local o = a:WaitForChild("LeftWeld")
    local v = z:WaitForChild("RightDetector")
    local X = v:WaitForChild("RightWeld")
    local x = z.BlobmanSeatAndOwnerScript:WaitForChild("CreatureGrab")
    local U = z.BlobmanSeatAndOwnerScript:WaitForChild("CreatureDrop")
    local function T(q, c, r) x:FireServer(q, r, c) end
    local function e(q, c) U:FireServer(q, c) end
    task.wait(.05)
    while c and (c.Parent and (P.Health > 0 and M.LoopAppleMethod.Value)) do
        for q = 1, 20, 1 do T(a, o, O) end
        e(o, O)
        e(o, O)
        task.wait(.01)
        if not M.LoopAppleMethod.Value then break end
    end
end

BlobLock = { MyBlob = nil, Running = false, Time = 0, StartPos = nil, LastTP = 0 }
function isnetworkowner(q) return q and (q:IsDescendantOf(workspace) and q:GetNetworkOwner() == b) end

function FindTargetByName(q)
    q = string.lower(q)
    for c, r in pairs(G:GetPlayers()) do if r ~= b then if string.find(string.lower(r.Name), q) or string.find(string.lower(r.DisplayName), q) then return
                r.Name end end end
    return nil
end

function BlobLock.TPToTargetAndBack(c, q)
    local r = b.Character
    if not r then return end
    local j = FWC(r, "HumanoidRootPart", 2)
    if not j then return end
    c.StartPos = j.CFrame
    j.CFrame = q.CFrame + Vector3.new(0, 5, 0)
    task.wait(.05)
    for c = 1, 3, 1 do
        d.GrabEvents.SetNetworkOwner:FireServer(q, q.CFrame)
        task.wait()
    end
    task.wait(.1)
    j.CFrame = c.StartPos
    c.LastTP = tick()
end

function BlobLock.Start(c, q)
    if c.Running then return end
    c.Running = true
    if q == "" or q == nil then
        c.Running = false
        return
    end
    if not G:FindFirstChild(q) then
        local r = FindTargetByName(q)
        if r then q = r else
            c.Running = false
            return
        end
    end
    task.spawn(function()
        local r = G:FindFirstChild(q)
        if not r then
            c:Stop()
            return
        end
        local j = r.Character
        if not j then
            r.CharacterAdded:Wait()
            task.wait(.5)
            j = r.Character
        end
        local u = j and FWC(j, "HumanoidRootPart", 2)
        if u then c:TPToTargetAndBack(u) end
        while c.Running do
            task.wait()
            local u = b.Character
            if not u then continue end
            local M = FWC(u, "HumanoidRootPart", 2)
            local z = FWC(u, "Humanoid", 2)
            if not M or not z then continue end
            if not z.SeatPart then
                c:Stop()
                break
            end
            if z.SeatPart then c.MyBlob = z.SeatPart.Parent end
            r = G:FindFirstChild(q)
            if not r then
                c:Stop()
                break
            end
            j = r.Character
            if not j then continue end
            local Y = FWC(j, "Humanoid", 2)
            local P = FWC(j, "HumanoidRootPart", 2)
            if not Y or not P then continue end
            if Y.Health == 0 then continue end
            local O = ((M.Position - P.Position)).Magnitude
            if O > 15 and tick() - c.LastTP > .5 then c:TPToTargetAndBack(P) end
            if c.MyBlob and c.MyBlob.Parent then
                task.defer(function() if isnetworkowner(P) then
                        if tick() - c.Time > .5 then
                            Y.Sit = true
                            task.wait(.16)
                            Y.Sit = false
                            c.Time = tick()
                        end
                        local q = c.MyBlob:FindFirstChild("LeftDetector")
                        if q then P.CFrame = q.CFrame end
                        for q, c in pairs(j:GetChildren()) do if c:IsA("BasePart") then c.Velocity = Vector3.new() end end
                        if O < 40 and Y.SeatPart then d.GrabEvents.SetNetworkOwner:FireServer(P, P.CFrame) end
                    end end)
                local q = c.MyBlob
                local r = q:FindFirstChild("LeftDetector")
                if r then
                    local c = q.BlobmanSeatAndOwnerScript.CreatureGrab
                    local j = q.BlobmanSeatAndOwnerScript.CreatureRelease
                    c:FireServer(r, P, r.LeftWeld)
                    task.wait(.005)
                    j:FireServer(r.LeftWeld, P)
                end
            end
        end
    end)
end

function BlobLock.Stop(q)
    q.Running = false
    q.MyBlob = nil
end

function RunSelectedMethodOnTarget(q, r)
    if not q or q == "" then return false end
    if isSelfTargetName(q) then
        c:Notify({ Title = "Wourld Hub", Description = "Self target blocked", Duration = 2 })
        return false
    end
    if r ~= "Bring" then rememberKickAttempt(q) end
    local j = false
    if r == "Bring" then
        Bring(q)
        j = true
    elseif r == "Kick" then j = select(1, TryKickTargetNoBlob(q, 2)) elseif r == "Loop Kick" then
        j = LoopKick(q)
        if not j then j = select(1, TryKickTargetNoBlob(q, 2)) end
    elseif r == "Bypass" then
        Bypass(q)
        notifyOwnKick(q, "Bypass")
        j = true
    elseif r == "Kick Blob" then
        loopKickBlobActive = true
        task.spawn(function() LoopKickBlobFunction(q) end)
        task.wait(.65)
        loopKickBlobActive = false
        j = true
    elseif r == "Loop Kick (Grab+Blob)" then
        loopKickBlobActive = true
        LoopKickBlobFunction(q)
        j = true
    elseif r == "Blob Kill" then
        BlobKill(q)
        notifyOwnKick(q, "Blob Kill")
        j = true
    elseif r == "Lock" then
        BlobLock:Start(q)
        notifyOwnKick(q, "Lock")
        j = true
    else return false end
    return j
end

function NormalizeKickAllMethod(q)
    local c = { ["Loop Kick"] = true, ["Kick Blob"] = true, ["Loop Kick (Grab+Blob)"] = true, ["Blob Kill"] = true, Lock = true }
    if c[q] then return "Kick" end
    return q
end

function KickAllPlayersByMethod(q)
    local r = NormalizeKickAllMethod(q or "Kick")
    local j = 0
    for q, c in ipairs(G:GetPlayers()) do if c ~= b then
            j = j + 1
            task.spawn(function() pcall(function() RunSelectedMethodOnTarget(c.Name, r) end) end)
            task.wait(.18)
        end end
    c:Notify({ Title = "Wourld Hub", Description = "Kick all started: " .. (tostring(j) .. (" | Method: " .. tostring(r))), Duration = 4 })
end

function KickAllPlayersNoBlob()
    if true then
        KickAllPlayersNoBlobFast()
        return
    end
    local q = 0
    for c, r in ipairs(G:GetPlayers()) do if r ~= b then
            q = q + 1
            task.spawn(function() pcall(function()
                    rememberKickAttempt(r.Name)
                    Kick(r.Name)
                end) end)
            task.wait(.18)
        end end
    c:Notify({ Title = "Wourld Hub", Description = "Kick all started: " .. (tostring(q) .. " | Mode: No Blob"), Duration = 4 })
end

function KickAllPlayersBlob()
    if true then
        KickAllPlayersBlobSafe()
        return
    end
    local q = b.Character
    local r = q and q:FindFirstChildOfClass("Humanoid")
    local j = r and r.SeatPart
    if not j or j.Parent.Name ~= "CreatureBlobman" then
        c:Notify({ Title = "Wourld Hub", Description = "Sit on Blobman to use Kick All Blob", Duration = 3 })
        return
    end
    local u = 0
    for q, c in ipairs(G:GetPlayers()) do if c ~= b then
            u = u + 1
            rememberKickAttempt(c.Name)
            loopKickBlobActive = true
            task.spawn(function() pcall(function() LoopKickBlobFunction(c.Name) end) end)
            task.wait(1.35)
            loopKickBlobActive = false
            task.wait(.2)
        end end
    loopKickBlobActive = false
    c:Notify({ Title = "Wourld Hub", Description = "Kick all started: " .. (tostring(u) .. " | Mode: Blob"), Duration = 4 })
end

function IsSeatedOnBlobman()
    local q = b.Character
    local c = q and q:FindFirstChildOfClass("Humanoid")
    local r = c and c.SeatPart
    return r and (r.Parent and r.Parent.Name == "CreatureBlobman")
end

function TryKickTargetNoBlob(q, c)
    if not q or q == "" then return false, "none" end
    if isSelfTargetName(q) then return false, "self" end
    local r = tonumber(c) or 3
    local j = "none"
    for c = 1, r, 1 do
        rememberKickAttempt(q)
        if QuickNoBlobKick(q) then return true, "Quick Kick" end
        j = "Quick Kick"
        task.wait(.06)
        if Kick(q) then return true, "Kick" end
        j = "Kick"
        task.wait(.08)
        if LoopKick(q) then return true, "Loop Kick" end
        j = "Loop Kick"
        task.wait(.12)
    end
    return false, j
end

function KickAllPlayersNoBlobFast()
    local q = {}
    for c, r in ipairs(G:GetPlayers()) do if r ~= b then table.insert(q, r.Name) end end
    local r = #q
    if r == 0 then
        c:Notify({ Title = "Wourld Hub", Description = "No targets for Kick All", Duration = 2 })
        return
    end
    for q, c in ipairs(q) do
        local r = c
        task.spawn(function()
            local q = false
            for c = 1, 3, 1 do
                q = select(1, TryKickTargetNoBlob(r, 1))
                if q then break end
                task.wait(.25)
            end
            if q then rememberKickedPlayer(r) end
        end)
    end
    c:Notify({ Title = "Wourld Hub", Description = "Kick all started: " .. (tostring(r) .. " | Mode: No Blob + Recovery"), Duration = 4 })
end

function KickAllPlayersBlobSafe()
    local q = {}
    for c, r in ipairs(G:GetPlayers()) do if r ~= b then table.insert(q, r.Name) end end
    if #q == 0 then
        c:Notify({ Title = "Wourld Hub", Description = "No targets for Kick All", Duration = 2 })
        return
    end
    if K then
        task.cancel(K)
        K = nil
        loopKickBlobActive = false
    end
    c:Notify({ Title = "Wourld Hub", Description = "Kick all started: " ..
    (tostring(#q) .. " | Mode: Blob Queue + Recovery"), Duration = 4 })
    if not IsSeatedOnBlobman() then c:Notify({ Title = "Wourld Hub", Description =
        "Sit on Blobman for best Blob queue hit rate", Duration = 3 }) end
    K = task.spawn(function()
        for q, c in ipairs(q) do
            local r = false
            if IsSeatedOnBlobman() then for q = 1, 2, 1 do
                    rememberKickAttempt(c)
                    loopKickBlobActive = true
                    pcall(function() LoopKickBlobFunction(c) end)
                    task.wait(.95)
                    loopKickBlobActive = false
                    r = Kick(c) and true or false
                    if r then
                        rememberKickedPlayer(c)
                        break
                    end
                    task.wait(.18)
                end end
            if not r then
                local q = select(1, TryKickTargetNoBlob(c, 2))
                if q then rememberKickedPlayer(c) end
            end
            task.wait(.08)
        end
        loopKickBlobActive = false
        K = nil
        c:Notify({ Title = "Wourld Hub", Description = "Kick all blob queue finished", Duration = 3 })
    end)
end

function ForceAllNotifySettings()
    gN.friendJoinNotify = true
    notifySoundEnabled = true
    syncNotifySoundSettings()
    local q = { "KickNotifyToggle", "PacketLagNotifyToggle", "FriendJoinNotifyToggle", "NotificationSoundToggle" }
    for q, c in ipairs(q) do
        local r = M and M[c]
        if r and (not r.Value) then r:SetValue(true) end
    end
end

function AdminEmergencyStop()
    loopKickBlobActive = false
    if K then
        task.cancel(K)
        K = nil
    end
    ownershipKickActive = false
    if W then
        task.cancel(W)
        W = nil
    end
    kickActionBusy = false
    loopKillActive = false
    if y then
        task.cancel(y)
        y = nil
    end
    if M and (M.LoopAppleMethod and M.LoopAppleMethod.Value) then M.LoopAppleMethod:SetValue(false) end
    if M and (M.OwnershipKickToggle and M.OwnershipKickToggle.Value) then M.OwnershipKickToggle:SetValue(false) end
end

function StartOwnershipKick(q)
    if isSelfTargetName(q) then
        ownershipKickActive = false
        c:Notify({ Title = "Wourld Hub", Description = "Self target blocked", Duration = 2 })
        return
    end
    local r = G:FindFirstChild(q)
    if r and IsPlayerInHouse(r) then
        ownershipKickActive = false
        c:Notify({ Title = "Wourld Hub", Description = GetHouseBlockedMessage(), Duration = 3 })
        playEventSound("kick")
        task.defer(function() if M and (M.OwnershipKickToggle and M.OwnershipKickToggle.Value) then M
                    .OwnershipKickToggle:SetValue(false) end end)
        return
    end
    OwnershipKickFunction(q)
end

TargetRight:AddButton({ Text = "Apply Method Once", Func = function()
    RememberActionPoint()
    local q = u.TargetPlayer.Value
    local r = u.MethodSelect.Value
    if q and q ~= "" then
        local j = RunSelectedMethodOnTarget(q, r)
        if j then gN.stats.actions = ((gN.stats.actions or 0)) + 1 else c:Notify({ Title = "Wourld Hub", Description =
            "Kick failed on " .. (tostring(q) .. (" [" .. (tostring(r or "Method") .. "]"))), Duration = 2.8 }) end
        ReturnAfterActionPoint()
    end
end, DoubleClick = false })
TargetRight:AddButton({ Text = "Destroy Visual (Try 2 Times)", Func = function()
    local q = u.TargetPlayer.Value
    if q and q ~= "" then BlobHeal(q) end
end, DoubleClick = false })
TargetRight:AddToggle("LoopAppleMethod",
    { Text = "Loop Apple Method", Default = false, Callback = function(q) if q then
            local q = u.TargetPlayer.Value
            local c = u.MethodSelect.Value
            if q and q ~= "" then task.spawn(function() if c == "Loop Kick (Grab+Blob)" then
                        loopKickBlobActive = true
                        LoopKickBlobFunction(q)
                        while M.LoopAppleMethod.Value do task.wait(.1) end
                        loopKickBlobActive = false
                    elseif c == "Lock" then
                        BlobLock:Start(q)
                        while M.LoopAppleMethod.Value and BlobLock.Running do task.wait(.1) end
                        BlobLock:Stop()
                    else while M.LoopAppleMethod.Value do
                            RunSelectedMethodOnTarget(q, c)
                            task.wait(1)
                        end end end) else M.LoopAppleMethod:SetValue(false) end
        else
            loopKickBlobActive = false
            BlobLock:Stop()
        end end })
KickSection:AddToggle("OwnershipKickToggle",
    { Text = "Ownership Kick", Default = false, Callback = function(q)
        ownershipKickActive = q
        local c = u.TargetPlayer.Value
        if q then if c and c ~= "" then W = task.spawn(function() StartOwnershipKick(c) end) else ownershipKickActive = false end else
            if W then
                task.cancel(W)
                W = nil
            end
            kickActionBusy = false
            kickSelfGuardUntil = math.max(kickSelfGuardUntil or 0, tick() + .6)
            local q = G:FindFirstChild(u.TargetPlayer.Value or "")
            if q and q.Character then
                local c = q.Character:FindFirstChild("HumanoidRootPart")
                if c then
                    for q, c in pairs(c:GetChildren()) do if c:IsA("BodyPosition") or c:IsA("BodyGyro") then pcall(function()
                                c:Destroy() end) end end
                    pcall(function()
                        c.AssemblyLinearVelocity = Vector3.zero
                        c.AssemblyAngularVelocity = Vector3.zero
                    end)
                end
            end
        end
    end })
KickSection:AddToggle("OwnershipRagdollToggle",
    { Text = "Pallet Ragdoll", Default = false, Callback = function(q)
        ownershipRagdollActive = q
        local c = u.TargetPlayer.Value
        if q then if c and c ~= "" then R = task.spawn(function() PalletRagdollFunction(c) end) else
                ownershipRagdollActive = false
                M.OwnershipRagdollToggle:SetValue(false)
            end else
            if R then
                task.cancel(R)
                R = nil
            end
            task.spawn(function()
                local q = workspace:FindFirstChild(b.Name .. "SpawnedInToys")
                if q then
                    local c = q:FindFirstChild("PalletLightBrown")
                    if c then
                        pcall(function() d.MenuToys.DestroyToy:FireServer(c) end)
                        if c.Parent then c:Destroy() end
                    end
                end
            end)
        end
    end })
KickSection:AddButton({ Text = "Kick All (No Blob)", Func = function() KickAllPlayersNoBlobFast() end, DoubleClick = false })
KickSection:AddButton({ Text = "Kick All (Blob)", Func = function() KickAllPlayersBlobSafe() end, DoubleClick = false })
XZSection:AddToggle("LoopKillToggle",
    { Text = "Loop Kill", Default = false, Callback = function(q)
        loopKillActive = q
        local c = u.TargetPlayer.Value
        if q then if c and c ~= "" then y = task.spawn(function() LoopKillFunction(c) end) else loopKillActive = false end else if y then
                task.cancel(y)
                y = nil
            end end
    end })
XZSection:AddToggle("SnowballRagdollToggle",
    { Text = "Snowball Ragdoll", Default = false, Callback = function(q)
        snowballRagdollActive = q
        local c = u.TargetPlayer.Value
        if q then if c and c ~= "" then k = task.spawn(function() SnowballRagdollFunction(c) end) else
                snowballRagdollActive = false
                M.SnowballRagdollToggle:SetValue(false)
            end else if k then
                task.cancel(k)
                k = nil
            end end
    end })
XZSection:AddToggle("TriggerBotToggle", { Text = "Trigger Bot", Default = false, Callback = function(q)
    SetTriggerBotState(q) end })
XZSection:AddSlider("TriggerBotRate",
    { Text = "Trigger Grab Rate", Default = 6, Min = 1, Max = 25, Rounding = 0, Callback = function(q) gN.triggerRate = q end })
XZSection:AddSlider("TriggerBotDistance",
    { Text = "Trigger Distance", Default = 24, Min = 6, Max = 80, Rounding = 0, Callback = function(q) gN.triggerDistance =
        q end })
XZSection:AddToggle("TriggerUseKickToggle",
    { Text = "Trigger Use Kick", Default = false, Callback = function(q) gN.triggerUseKick = q end })
TraceSection:AddToggle("TraceToggle",
    { Text = "Trace to Target", Default = false, Callback = function(q)
        traceEnabled = q
        if q then
            local q = u.TargetPlayer.Value
            if q and q ~= "" then StartTrace(q) else
                traceEnabled = false
                M.TraceToggle:SetValue(false)
            end
        else
            if xN then
                xN:Disconnect()
                xN = nil
            end
            if XN then
                XN:Destroy()
                XN = nil
            end
        end
    end }); (TraceSection:AddLabel("Trace Color")):AddColorPicker("TraceColorPicker",
    { Default = Color3.fromRGB(255, 0, 0), Title = "Trace Color", Callback = function(q) traceColor = q end })
allunVisuals = { DefaultLighting = { Brightness = P.Brightness, ClockTime = P.ClockTime, GlobalShadows = P.GlobalShadows, OutdoorAmbient = P.OutdoorAmbient, Ambient = P.Ambient, FogStart = P.FogStart, FogEnd = P.FogEnd, FogColor = P.FogColor, ExposureCompensation = P.ExposureCompensation }, DefaultSkySettings = {}, HatEnabled = false, HatTransparency = .3, HatRainbow = false, HatColor =
Color3.fromRGB(0, 255, 255), HatParts = {}, TrailEnabled = false, TrailGradient = false, TrailLifetime = .5, TrailTransparencyStart = 0, TrailRainbow = false, TrailColorStatic =
Color3.fromRGB(0, 255, 255), TrailGradient1 = Color3.fromRGB(0, 86, 255), TrailGradient2 = Color3.fromRGB(255, 0, 0), TrailParts = {}, SkinTrailEnabled = false, SkinTrailColor =
Color3.fromRGB(255, 0, 0), SkinTrailLife = .5, ForceFieldEnabled = false, ForceFieldColor = Color3.fromRGB(128, 128, 128), ForceFieldRainbow = false, OriginalColors = {}, AuraEnabled = false, AuraType =
"Godly", CustomAuraID = "", CurrentAuraModel = nil, AuraEffects = {}, WorldTimeEnabled = false, WorldTimeValue = 12, FullBrightEnabled = false, NebulaEnabled = false, NebulaThemeColor =
Color3.fromRGB(173, 216, 230), CurrentSkybox = "HD", CustomSkyEnabled = false, ScreenEnabled = false, ScreenIntensity = 0, ScreenConnection = nil, AnimeImageEnabled = false, AnimeImageGui = nil, RuntimeConnection = nil, CharacterAddedConnection = nil }
do
    local q = P:FindFirstChildOfClass("Sky")
    if q then allunVisuals.DefaultSkySettings = { SkyboxBk = q.SkyboxBk, SkyboxDn = q.SkyboxDn, SkyboxFt = q.SkyboxFt, SkyboxLf =
        q.SkyboxLf, SkyboxRt = q.SkyboxRt, SkyboxUp = q.SkyboxUp } end
end
allunVisuals.AuraModels = { Godly = "rbxassetid://16699750981", ["Super Sayien"] = "rbxassetid://116109508364297",
    ["North Star"] = "rbxassetid://83945069652732", ["Blue Lord"] = "rbxassetid://10974316799", ["Pink Aura"] =
"rbxassetid://115980859615239", ["Angel Wing"] = "rbxassetid://90022969696073", ["Sweet Heart"] =
"rbxassetid://91724768175470", ["Ethereal Aura"] = "rbxassetid://97041568674250" }
allunVisuals.SkyboxAssets = { ["Black Storm"] = { Bk = "rbxassetid://15502511288", Dn = "rbxassetid://15502508460", Ft = "rbxassetid://15502510289", Lf = "rbxassetid://15502507918", Rt = "rbxassetid://15502509398", Up = "rbxassetid://15502511911" }, HD = { Bk = "http://www.roblox.com/asset/?id=16553658937", Dn = "http://www.roblox.com/asset/?id=16553660713", Ft = "http://www.roblox.com/asset/?id=16553662144", Lf = "http://www.roblox.com/asset/?id=16553664042", Rt = "http://www.roblox.com/asset/?id=16553665766", Up = "http://www.roblox.com/asset/?id=16553667750" }, Snow = { Bk = "http://www.roblox.com/asset/?id=155657655", Dn = "http://www.roblox.com/asset/?id=155674246", Ft = "http://www.roblox.com/asset/?id=155657609", Lf = "http://www.roblox.com/asset/?id=155657671", Rt = "http://www.roblox.com/asset/?id=155657619", Up = "http://www.roblox.com/asset/?id=155674931" },
    ["Blue Space"] = { Bk = "rbxassetid://15536110634", Dn = "rbxassetid://15536112543", Ft = "rbxassetid://15536116141", Lf = "rbxassetid://15536114370", Rt = "rbxassetid://15536118762", Up = "rbxassetid://15536117282" }, Realistic = { Bk = "rbxassetid://653719502", Dn = "rbxassetid://653718790", Ft = "rbxassetid://653719067", Lf = "rbxassetid://653719190", Rt = "rbxassetid://653718931", Up = "rbxassetid://653719321" }, Stormy = { Bk = "http://www.roblox.com/asset/?id=18703245834", Dn = "http://www.roblox.com/asset/?id=18703243349", Ft = "http://www.roblox.com/asset/?id=18703240532", Lf = "http://www.roblox.com/asset/?id=18703237556", Rt = "http://www.roblox.com/asset/?id=18703235430", Up = "http://www.roblox.com/asset/?id=18703232671" }, Pink = { Bk = "rbxassetid://12216109205", Dn = "rbxassetid://12216109875", Ft = "rbxassetid://12216109489", Lf = "rbxassetid://12216110170", Rt = "rbxassetid://12216110471", Up = "rbxassetid://12216108877" }, Sunset = { Bk = "rbxassetid://600830446", Dn = "rbxassetid://600831635", Ft = "rbxassetid://600832720", Lf = "rbxassetid://600886090", Rt = "rbxassetid://600833862", Up = "rbxassetid://600835177" }, Arctic = { Bk = "http://www.roblox.com/asset/?id=225469390", Dn = "http://www.roblox.com/asset/?id=225469395", Ft = "http://www.roblox.com/asset/?id=225469403", Lf = "http://www.roblox.com/asset/?id=225469450", Rt = "http://www.roblox.com/asset/?id=225469471", Up = "http://www.roblox.com/asset/?id=225469481" }, Space = { Bk = "http://www.roblox.com/asset/?id=166509999", Dn = "http://www.roblox.com/asset/?id=166510057", Ft = "http://www.roblox.com/asset/?id=166510116", Lf = "http://www.roblox.com/asset/?id=166510092", Rt = "http://www.roblox.com/asset/?id=166510131", Up = "http://www.roblox.com/asset/?id=166510114" },
    ["Roblox Default"] = { Bk = "rbxasset://textures/sky/sky512_bk.tex", Dn = "rbxasset://textures/sky/sky512_dn.tex", Ft = "rbxasset://textures/sky/sky512_ft.tex", Lf = "rbxasset://textures/sky/sky512_lf.tex", Rt = "rbxasset://textures/sky/sky512_rt.tex", Up = "rbxasset://textures/sky/sky512_up.tex" },
    ["Red Night"] = { Bk = "http://www.roblox.com/asset/?id=401664839", Dn = "http://www.roblox.com/asset/?id=401664862", Ft = "http://www.roblox.com/asset/?id=401664960", Lf = "http://www.roblox.com/asset/?id=401664881", Rt = "http://www.roblox.com/asset/?id=401664901", Up = "http://www.roblox.com/asset/?id=401664936" },
    ["Deep Space 1"] = { Bk = "http://www.roblox.com/asset/?id=149397692", Dn = "http://www.roblox.com/asset/?id=149397686", Ft = "http://www.roblox.com/asset/?id=149397697", Lf = "http://www.roblox.com/asset/?id=149397684", Rt = "http://www.roblox.com/asset/?id=149397688", Up = "http://www.roblox.com/asset/?id=149397702" },
    ["Pink Skies"] = { Bk = "http://www.roblox.com/asset/?id=151165214", Dn = "http://www.roblox.com/asset/?id=151165197", Ft = "http://www.roblox.com/asset/?id=151165224", Lf = "http://www.roblox.com/asset/?id=151165191", Rt = "http://www.roblox.com/asset/?id=151165206", Up = "http://www.roblox.com/asset/?id=151165227" },
    ["Purple Sunset"] = { Bk = "rbxassetid://264908339", Dn = "rbxassetid://264907909", Ft = "rbxassetid://264909420", Lf = "rbxassetid://264909758", Rt = "rbxassetid://264908886", Up = "rbxassetid://264907379" },
    ["Blue Night"] = { Bk = "http://www.roblox.com/asset/?id=12064107", Dn = "http://www.roblox.com/asset/?id=12064152", Ft = "http://www.roblox.com/asset/?id=12064121", Lf = "http://www.roblox.com/asset/?id=12063984", Rt = "http://www.roblox.com/asset/?id=12064115", Up = "http://www.roblox.com/asset/?id=12064131" },
    ["Blossom Daylight"] = { Bk = "http://www.roblox.com/asset/?id=271042516", Dn = "http://www.roblox.com/asset/?id=271077243", Ft = "http://www.roblox.com/asset/?id=271042556", Lf = "http://www.roblox.com/asset/?id=271042310", Rt = "http://www.roblox.com/asset/?id=271042467", Up = "http://www.roblox.com/asset/?id=271077958" },
    ["Blue Nebula"] = { Bk = "http://www.roblox.com/asset?id=135207744", Dn = "http://www.roblox.com/asset?id=135207662", Ft = "http://www.roblox.com/asset?id=135207770", Lf = "http://www.roblox.com/asset?id=135207615", Rt = "http://www.roblox.com/asset?id=135207695", Up = "http://www.roblox.com/asset?id=135207794" },
    ["Blue Planet"] = { Bk = "rbxassetid://218955819", Dn = "rbxassetid://218953419", Ft = "rbxassetid://218954524", Lf = "rbxassetid://218958493", Rt = "rbxassetid://218957134", Up = "rbxassetid://218950090" },
    ["Deep Space 2"] = { Bk = "http://www.roblox.com/asset/?id=159248188", Dn = "http://www.roblox.com/asset/?id=159248183", Ft = "http://www.roblox.com/asset/?id=159248187", Lf = "http://www.roblox.com/asset/?id=159248173", Rt = "http://www.roblox.com/asset/?id=159248192", Up = "http://www.roblox.com/asset/?id=159248176" }, Summer = { Bk = "rbxassetid://16648590964", Dn = "rbxassetid://16648617436", Ft = "rbxassetid://16648595424", Lf = "rbxassetid://16648566370", Rt = "rbxassetid://16648577071", Up = "rbxassetid://16648598180" }, Galaxy = { Bk = "rbxassetid://15983968922", Dn = "rbxassetid://15983966825", Ft = "rbxassetid://15983965025", Lf = "rbxassetid://15983967420", Rt = "rbxassetid://15983966246", Up = "rbxassetid://15983964246" }, Stylized = { Bk = "rbxassetid://18351376859", Dn = "rbxassetid://18351374919", Ft = "rbxassetid://18351376800", Lf = "rbxassetid://18351376469", Rt = "rbxassetid://18351376457", Up = "rbxassetid://18351377189" }, Minecraft = { Bk = "rbxassetid://8735166756", Dn = "http://www.roblox.com/asset/?id=8735166707", Ft = "http://www.roblox.com/asset/?id=8735231668", Lf = "http://www.roblox.com/asset/?id=8735166755", Rt = "http://www.roblox.com/asset/?id=8735166751", Up = "http://www.roblox.com/asset/?id=8735166729" },
    ["Sunset 2"] = { Bk = "http://www.roblox.com/asset/?id=151165214", Dn = "http://www.roblox.com/asset/?id=151165197", Ft = "http://www.roblox.com/asset/?id=151165224", Lf = "http://www.roblox.com/asset/?id=151165191", Rt = "http://www.roblox.com/asset/?id=151165206", Up = "http://www.roblox.com/asset/?id=151165227" },
    ["Cloudy Rain"] = { Bk = "http://www.roblox.com/asset/?id=4498828382", Dn = "http://www.roblox.com/asset/?id=4498828812", Ft = "http://www.roblox.com/asset/?id=4498829917", Lf = "http://www.roblox.com/asset/?id=4498830911", Rt = "http://www.roblox.com/asset/?id=4498830417", Up = "http://www.roblox.com/asset/?id=4498831746" },
    ["Black Cloudy Rain"] = { Bk = "http://www.roblox.com/asset/?id=149679669", Dn = "http://www.roblox.com/asset/?id=149681979", Ft = "http://www.roblox.com/asset/?id=149679690", Lf = "http://www.roblox.com/asset/?id=149679709", Rt = "http://www.roblox.com/asset/?id=149679722", Up = "http://www.roblox.com/asset/?id=149680199" } }
function allunVisuals.removeHat(q)
    local c = allunVisuals.HatParts[q]
    if c then
        c:Destroy()
        allunVisuals.HatParts[q] = nil
    end
end

function allunVisuals.addHat(q)
    task.wait(.1)
    local c = q and q:FindFirstChild("Head")
    if not c then return end
    allunVisuals.removeHat(q)
    local r = Instance.new("Part")
    r.Name = "Hat"
    r.Transparency = allunVisuals.HatTransparency
    r.Color = allunVisuals.HatColor
    r.Material = Enum.Material.Neon
    r.CanCollide = false
    r.CanTouch = false
    r.CanQuery = false
    r.Massless = true
    local j = Instance.new("SpecialMesh")
    j.MeshId = "rbxassetid://1033714"
    j.Scale = Vector3.new(2.4, 1.6, 2.4)
    j.Parent = r
    local u = Instance.new("WeldConstraint")
    u.Part0 = c
    u.Part1 = r
    u.Parent = r
    r.CFrame = c.CFrame * CFrame.new(0, 1.1, 0)
    r.Parent = q
    allunVisuals.HatParts[q] = r
end

function allunVisuals.updateHats()
    local q = b.Character
    for c, r in pairs(allunVisuals.HatParts) do if r and (r.Parent and c == q) then
            r.Transparency = allunVisuals.HatTransparency
            r.Color = allunVisuals.HatRainbow and Color3.fromHSV(((tick() % 5)) / 5, 1, 1) or allunVisuals.HatColor
        end end
end

function allunVisuals.removeTrail(q)
    if allunVisuals.TrailParts[q] then
        allunVisuals.TrailParts[q]:Destroy()
        allunVisuals.TrailParts[q] = nil
    end
    local c = q and q:FindFirstChild("HumanoidRootPart")
    if c then
        local q = c:FindFirstChild("TrailAttach0")
        local r = c:FindFirstChild("TrailAttach1")
        if q then q:Destroy() end
        if r then r:Destroy() end
    end
end

function allunVisuals.addTrail(q)
    local c = q and q:FindFirstChild("HumanoidRootPart")
    if not c then return end
    allunVisuals.removeTrail(q)
    local r = Instance.new("Attachment")
    r.Name = "TrailAttach0"
    r.Position = Vector3.new(0, 2, 0)
    r.Parent = c
    local j = Instance.new("Attachment")
    j.Name = "TrailAttach1"
    j.Position = Vector3.new(0, -2, 0)
    j.Parent = c
    local u = Instance.new("Trail")
    u.Attachment0 = r
    u.Attachment1 = j
    u.Lifetime = allunVisuals.TrailLifetime
    u.LightEmission = .2
    u.Enabled = true
    u.Transparency = NumberSequence.new({ NumberSequenceKeypoint.new(0, allunVisuals.TrailTransparencyStart),
        NumberSequenceKeypoint.new(1, 1) })
    if allunVisuals.TrailGradient then u.Color = ColorSequence.new(allunVisuals.TrailGradient1,
            allunVisuals.TrailGradient2) else u.Color = ColorSequence.new(allunVisuals.TrailColorStatic) end
    u.Parent = q
    allunVisuals.TrailParts[q] = u
end

function allunVisuals.updateTrails()
    local q = b.Character
    for c, r in pairs(allunVisuals.TrailParts) do if r and (r.Parent and c == q) then
            r.Lifetime = allunVisuals.TrailLifetime
            r.Transparency = NumberSequence.new({ NumberSequenceKeypoint.new(0, allunVisuals.TrailTransparencyStart),
                NumberSequenceKeypoint.new(1, 1) })
            if allunVisuals.TrailGradient then r.Color = ColorSequence.new(allunVisuals.TrailGradient1,
                    allunVisuals.TrailGradient2) else
                local q = allunVisuals.TrailRainbow and Color3.fromHSV(((tick() % 5)) / 5, 1, 1) or
                allunVisuals.TrailColorStatic
                r.Color = ColorSequence.new(q)
            end
        end end
end

function allunVisuals.saveOriginalColors(q)
    allunVisuals.OriginalColors[q] = {}
    for c, r in ipairs(q:GetDescendants()) do if r:IsA("BasePart") and r.Name ~= "Hat" then allunVisuals.OriginalColors[q][r] = { Color =
            r.Color, Material = r.Material } end end
end

function allunVisuals.applyForceField(q)
    allunVisuals.saveOriginalColors(q)
    for q, c in ipairs(q:GetDescendants()) do if c:IsA("BasePart") and c.Name ~= "Hat" then
            c.Color = allunVisuals.ForceFieldColor
            c.Material = Enum.Material.ForceField
        end end
end

function allunVisuals.removeForceField(q)
    local c = allunVisuals.OriginalColors[q]
    if not c then return end
    for q, c in pairs(c) do if q and (q.Parent and q:IsA("BasePart")) then
            q.Color = c.Color
            q.Material = c.Material
        end end
    allunVisuals.OriginalColors[q] = nil
end

function allunVisuals.updateForceField()
    if not ((b.Character and allunVisuals.ForceFieldEnabled)) then return end
    for q, c in ipairs(b.Character:GetDescendants()) do if c:IsA("BasePart") and (c.Name ~= "Hat" and c.Material == Enum.Material.ForceField) then c.Color =
            allunVisuals.ForceFieldRainbow and Color3.fromHSV(((tick() % 5)) / 5, 1, 1) or allunVisuals.ForceFieldColor end end
end

function allunVisuals.toggleSkinTrail(q)
    local c = b.Character
    if not c then return end
    local r = c:FindFirstChild("HumanoidRootPart")
    if not r then return end
    for c, j in ipairs(c:GetChildren()) do if j:IsA("BasePart") and j ~= r then if q then if not j:FindFirstChild("SkinTrail") then
                    local q = Instance.new("Trail")
                    q.Name = "SkinTrail"
                    q.Texture = "rbxassetid://1390780157"
                    q.Color = ColorSequence.new(allunVisuals.SkinTrailColor)
                    q.Lifetime = allunVisuals.SkinTrailLife
                    q.Parent = j
                    local c = Instance.new("Attachment")
                    c.Name = "SkinPointer1"
                    c.Parent = j
                    local u = Instance.new("Attachment")
                    u.Name = "SkinPointer2"
                    u.Parent = r
                    q.Attachment0 = c
                    q.Attachment1 = u
                end else
                local q = j:FindFirstChild("SkinTrail")
                local c = j:FindFirstChild("SkinPointer1")
                if q then q:Destroy() end
                if c then c:Destroy() end
            end end end
    if not q then
        local q = r:FindFirstChild("SkinPointer2")
        if q then q:Destroy() end
    end
end

function allunVisuals.updateSkinTrail()
    local q = b.Character
    if not q then return end
    for q, c in ipairs(q:GetDescendants()) do if c:IsA("Trail") and c.Name == "SkinTrail" then
            c.Color = ColorSequence.new(allunVisuals.SkinTrailColor)
            c.Lifetime = allunVisuals.SkinTrailLife
        end end
end

function allunVisuals.loadAuraModel(q)
    local c, r = pcall(function() return (game:GetObjects(q))[1] end)
    if c then return r end
    return nil
end

function allunVisuals.disableAura()
    for q, c in ipairs(allunVisuals.AuraEffects) do if c and c.Parent then c:Destroy() end end
    table.clear(allunVisuals.AuraEffects)
end

function allunVisuals.enableAura(q)
    allunVisuals.disableAura()
    if not allunVisuals.CurrentAuraModel then return end
    local c = allunVisuals.CurrentAuraModel:Clone()
    for c, r in ipairs(c:GetDescendants()) do if not r:IsA("BasePart") then
            local c = r:Clone()
            local j = r.Parent and r.Parent.Name
            local u = j and q:FindFirstChild(j)
            if not u then u = q:FindFirstChildWhichIsA("BasePart") end
            if u and not u:FindFirstChild(c.Name) then
                c.Parent = u
                table.insert(allunVisuals.AuraEffects, c)
            end
        end end
    c:Destroy()
end

function allunVisuals.updateAuraLogic()
    local q = allunVisuals.CustomAuraID ~= "" and ("rbxassetid://" .. allunVisuals.CustomAuraID:gsub("%D", "")) or
    allunVisuals.AuraModels[allunVisuals.AuraType]
    if not q then return end
    local c = allunVisuals.loadAuraModel(q)
    if c then
        allunVisuals.CurrentAuraModel = c
        if allunVisuals.AuraEnabled and b.Character then allunVisuals.enableAura(b.Character) end
    end
end

function allunVisuals.applySkybox(q)
    local c = allunVisuals.SkyboxAssets[q]
    if not c then return end
    local r = P:FindFirstChildOfClass("Sky")
    if not r then
        r = Instance.new("Sky")
        r.Name = "Sky"
        r.Parent = P
    end
    r.SkyboxBk = c.Bk
    r.SkyboxDn = c.Dn
    r.SkyboxFt = c.Ft
    r.SkyboxLf = c.Lf
    r.SkyboxRt = c.Rt
    r.SkyboxUp = c.Up
end

function allunVisuals.restoreDefaultSky()
    local q = P:FindFirstChildOfClass("Sky")
    if q and allunVisuals.DefaultSkySettings.SkyboxBk then
        q.SkyboxBk = allunVisuals.DefaultSkySettings.SkyboxBk
        q.SkyboxDn = allunVisuals.DefaultSkySettings.SkyboxDn
        q.SkyboxFt = allunVisuals.DefaultSkySettings.SkyboxFt
        q.SkyboxLf = allunVisuals.DefaultSkySettings.SkyboxLf
        q.SkyboxRt = allunVisuals.DefaultSkySettings.SkyboxRt
        q.SkyboxUp = allunVisuals.DefaultSkySettings.SkyboxUp
    elseif q then q:Destroy() end
end

function allunVisuals.setNebulaEnabled(q)
    allunVisuals.NebulaEnabled = q and true or false
    if q then
        local q = P:FindFirstChild("NebulaBloom") or Instance.new("BloomEffect")
        q.Name = "NebulaBloom"
        q.Intensity = .7
        q.Size = 24
        q.Threshold = 1
        q.Parent = P
        local c = P:FindFirstChild("NebulaColorCorrection") or Instance.new("ColorCorrectionEffect")
        c.Name = "NebulaColorCorrection"
        c.Saturation = .5
        c.Contrast = .2
        c.TintColor = allunVisuals.NebulaThemeColor
        c.Parent = P
        local r = P:FindFirstChild("NebulaAtmosphere") or Instance.new("Atmosphere")
        r.Name = "NebulaAtmosphere"
        r.Density = .4
        r.Offset = .25
        r.Glare = 1
        r.Haze = 2
        r.Color = allunVisuals.NebulaThemeColor
        r.Decay = Color3.fromRGB(173, 216, 230)
        r.Parent = P
        P.Ambient = allunVisuals.NebulaThemeColor
        P.OutdoorAmbient = allunVisuals.NebulaThemeColor
        P.FogStart = 100
        P.FogEnd = 500
        P.FogColor = allunVisuals.NebulaThemeColor
    else
        for q, c in ipairs({ "NebulaBloom", "NebulaColorCorrection", "NebulaAtmosphere" }) do
            local r = P:FindFirstChild(c)
            if r then r:Destroy() end
        end
        P.Ambient = allunVisuals.DefaultLighting.Ambient
        P.OutdoorAmbient = allunVisuals.DefaultLighting.OutdoorAmbient
        P.FogStart = allunVisuals.DefaultLighting.FogStart
        P.FogEnd = allunVisuals.DefaultLighting.FogEnd
        P.FogColor = allunVisuals.DefaultLighting.FogColor
    end
end

function allunVisuals.setFullBrightEnabled(q)
    allunVisuals.FullBrightEnabled = q and true or false
    if not q then
        P.Brightness = allunVisuals.DefaultLighting.Brightness
        P.GlobalShadows = allunVisuals.DefaultLighting.GlobalShadows
        P.OutdoorAmbient = allunVisuals.DefaultLighting.OutdoorAmbient
        P.ExposureCompensation = allunVisuals.DefaultLighting.ExposureCompensation
    end
end

function allunVisuals.setScreenEnabled(q)
    allunVisuals.ScreenEnabled = q and true or false
    if q then
        if allunVisuals.ScreenConnection then allunVisuals.ScreenConnection:Disconnect() end
        allunVisuals.ScreenConnection = z.RenderStepped:Connect(function()
            local q = Y.CurrentCamera
            if q then q.CFrame = q.CFrame * CFrame.new(0, 0, 0, 1, 0, 0, 0, .65 + allunVisuals.ScreenIntensity, 0, 0, 0,
                    1) end
        end)
    elseif allunVisuals.ScreenConnection then
        allunVisuals.ScreenConnection:Disconnect()
        allunVisuals.ScreenConnection = nil
    end
end

function allunVisuals.toggleAnimeImage(q)
    allunVisuals.AnimeImageEnabled = q and true or false
    if q then
        if allunVisuals.AnimeImageGui then allunVisuals.AnimeImageGui:Destroy() end
        local q = Instance.new("ScreenGui")
        q.Name = "AnimeImageGui"
        q.ResetOnSpawn = false
        q.Parent = b:WaitForChild("PlayerGui")
        local c = Instance.new("ImageLabel")
        c.Name = "AnimeImage"
        c.Image = "http://www.roblox.com/asset/?id=117783035423570"
        c.Size = UDim2.new(0, 350, 0, 400)
        c.Position = UDim2.new(1, -25, 0, 10)
        c.AnchorPoint = Vector2.new(1, 0)
        c.BackgroundTransparency = 1
        c.Parent = q
        allunVisuals.AnimeImageGui = q
    elseif allunVisuals.AnimeImageGui then
        allunVisuals.AnimeImageGui:Destroy()
        allunVisuals.AnimeImageGui = nil
    end
end

function allunVisuals.reapplyVisuals(q)
    task.wait(1)
    if allunVisuals.HatEnabled then allunVisuals.addHat(q) end
    if allunVisuals.TrailEnabled then allunVisuals.addTrail(q) end
    if allunVisuals.ForceFieldEnabled then allunVisuals.applyForceField(q) end
    if allunVisuals.AuraEnabled then allunVisuals.enableAura(q) end
    if allunVisuals.SkinTrailEnabled then allunVisuals.toggleSkinTrail(true) end
    if allunVisuals.AnimeImageEnabled then allunVisuals.toggleAnimeImage(true) end
end

function allunVisuals.refreshLoop()
    if allunVisuals.HatEnabled then allunVisuals.updateHats() end
    if allunVisuals.TrailEnabled then allunVisuals.updateTrails() end
    if allunVisuals.ForceFieldEnabled then allunVisuals.updateForceField() end
    if allunVisuals.WorldTimeEnabled then P.ClockTime = allunVisuals.WorldTimeValue end
    if allunVisuals.FullBrightEnabled then
        P.Brightness = 3
        P.GlobalShadows = false
        P.OutdoorAmbient = Color3.new(1, 1, 1)
        P.ExposureCompensation = .3
    end
end

function allunVisuals.bind()
    if allunVisuals.RuntimeConnection then
        allunVisuals.RuntimeConnection:Disconnect()
        allunVisuals.RuntimeConnection = nil
    end
    allunVisuals.RuntimeConnection = z.Heartbeat:Connect(allunVisuals.refreshLoop)
    if allunVisuals.CharacterAddedConnection then
        allunVisuals.CharacterAddedConnection:Disconnect()
        allunVisuals.CharacterAddedConnection = nil
    end
    allunVisuals.CharacterAddedConnection = b.CharacterAdded:Connect(allunVisuals.reapplyVisuals)
    if b.Character then task.defer(function() allunVisuals.reapplyVisuals(b.Character) end) end
end

function allunVisuals.disableAll()
    allunVisuals.HatEnabled = false
    allunVisuals.TrailEnabled = false
    allunVisuals.ForceFieldEnabled = false
    allunVisuals.SkinTrailEnabled = false
    allunVisuals.AuraEnabled = false
    allunVisuals.WorldTimeEnabled = false
    allunVisuals.setFullBrightEnabled(false)
    allunVisuals.setNebulaEnabled(false)
    allunVisuals.setScreenEnabled(false)
    allunVisuals.toggleAnimeImage(false)
    allunVisuals.restoreDefaultSky()
    local q = b.Character
    if q then
        allunVisuals.removeHat(q)
        allunVisuals.removeTrail(q)
        allunVisuals.removeForceField(q)
        allunVisuals.toggleSkinTrail(false)
    end
    allunVisuals.disableAura()
end

allunVisuals.bind()
local la, Da = pcall(function()
    VisualsLeft = w.Visuals:AddLeftGroupbox("Visuals")
    local function q()
        local q = Y.CurrentCamera
        if q and type(q.FieldOfView) == "number" then return q.FieldOfView end
        return 70
    end
    VisualsLeft:AddSlider("FOVSlider",
        { Text = "Field of View", Default = q(), Min = 1, Max = 120, Rounding = 1, Callback = function(q)
            local c = Y.CurrentCamera
            if c then c.FieldOfView = q end
        end })
    VisualsLeft:AddToggle("Thirdperson",
        { Text = "Third person", Default = false, Callback = function(q)
            local c = game.Players.LocalPlayer
            if q then
                c.CameraMode = Enum.CameraMode.Classic
                c.CameraMaxZoomDistance = 1000
                c.CameraMinZoomDistance = .5
            else
                c.CameraMode = Enum.CameraMode.LockFirstPerson
                c.CameraMaxZoomDistance = .5
                c.CameraMinZoomDistance = .5
            end
        end }); (VisualsLeft:AddToggle("PCLDToggle", { Text = "PCLD ESP", Default = false, Callback = function(q)
        tN = q
        if q then
            if not DN or not DN.Connected then DN = workspace.DescendantAdded:Connect(function(q) if tN and IsTarget(q) then
                        AddBoxESP(q) end end) end
            ResetPCLDScanState()
            if not JN then JN = task.spawn(StartPCLDRefreshLoop) end
            ScanPCLDChunk()
            CleanupPCLDBoxes()
        else
            if DN then
                DN:Disconnect()
                DN = nil
            end
            if JN then
                task.cancel(JN)
                JN = nil
            end
            ResetPCLDScanState()
            RemoveAllBoxes()
        end
    end })):AddColorPicker("PCLDColor",
        { Default = Color3.fromRGB(255, 255, 255), Title = "ESP Color", Callback = function(q)
            FN = q
            for q, c in pairs(BN) do if c then c.Color3 = FN end end
        end }); (VisualsLeft:AddToggle("UsernameEspToggle", { Text = "Username ESP", Default = false, Callback = function(
        q) setUsernameEspState(q) end })):AddColorPicker("UsernameEspColor",
        { Default = Color3.fromRGB(95, 195, 255), Title = "Username Color", Callback = function(q)
            wN.usernameColor = q
            if wN.usernameEnabled then updateUsernameEsp() end
        end })
    VisualsLeft:AddToggle("UsernameShowDistanceToggle",
        { Text = "Username Distance", Default = true, Callback = function(q)
            wN.showDistance = q
            if wN.usernameEnabled then updateUsernameEsp() end
        end })
    VisualsLeft:AddToggle("UsernameShowHealthToggle",
        { Text = "Username Health", Default = true, Callback = function(q)
            wN.showHealth = q
            if wN.usernameEnabled then updateUsernameEsp() end
        end })
    VisualsLeft:AddToggle("UsernameDisplayNameToggle",
        { Text = "Use Display Name", Default = true, Callback = function(q)
            wN.showDisplayName = q
            if wN.usernameEnabled then updateUsernameEsp() end
        end }); (VisualsLeft:AddToggle("HitboxEspToggle", { Text = "Hitbox ESP", Default = false, Callback = function(q)
        setHitboxEspState(q) end })):AddColorPicker("HitboxEspColor",
        { Default = Color3.fromRGB(255, 64, 64), Title = "Hitbox Color", Callback = function(q) wN.hitboxColor = q end })
    VisualsLeft:AddSlider("HitboxScaleSlider",
        { Text = "Hitbox Size", Default = 17, Min = 10, Max = 40, Rounding = 0, Callback = function(q) wN.hitboxScale = q /
            10 end })
    VisualsLeft:AddSlider("HitboxTransparencySlider",
        { Text = "Hitbox Transparency", Default = 55, Min = 0, Max = 95, Rounding = 0, Callback = function(q) wN.hitboxTransparency =
            q / 100 end })
    VisualsLeft:AddToggle("SelfAntiKickEspToggle",
        { Text = "My Anti Kick ESP", Default = true, Callback = function(q) setSelfAntiKickVisualState(q) end }); (VisualsLeft:AddToggle("AntiKickOwnerEspToggle", { Text = "Anti Kick Owner ESP", Default = false, Callback = function(
        q) setAntiKickOwnerEspState(q) end })):AddColorPicker("AntiKickOwnerEspColor",
        { Default = wN.antiKickOwnerColor, Title = "Owner ESP Color", Callback = function(q)
            wN.antiKickOwnerColor = q
            if wN.antiKickOwnerEnabled then pcall(updateAntiKickOwnerEsp) end
        end })
    VisualsLeft:AddToggle("AntiKickOwnerNotifyToggle",
        { Text = "Anti Kick Owner Notify", Default = false, Callback = function(q) wN.antiKickOwnerNotify = q and true or
            false end })
    VisualsLeft:AddToggle("KickNotifyToggle",
        { Text = "Kick Notify", Default = true, Callback = function(q) if q then
                if p then
                    p:Disconnect()
                    p = nil
                end
                p = Y.DescendantAdded:Connect(function(q)
                    if not isKickObjectName(q and q.Name) then return end
                    local r = tick()
                    local j = nil
                    pcall(function() j = q:GetDebugId() end)
                    if j then
                        local q = gN.kickSeenObjects[j]
                        if q and (r - q) < 1.1 then return end
                        gN.kickSeenObjects[j] = r
                        if r - ((gN.kickSeenTrimAt or 0)) > 14 then
                            gN.kickSeenTrimAt = r
                            for q, c in pairs(gN.kickSeenObjects) do if r - c > 8 then gN.kickSeenObjects[q] = nil end end
                        end
                    end
                    if r - ((Q6 or 0)) < .35 then return end
                    Q6 = r
                    local u = getInstanceWorldPosition(q)
                    if not u then return end
                    local M = getClosestPlayer(u)
                    if M then
                        local q = M.Character
                        local r = q and q:FindFirstChildOfClass("Humanoid")
                        local j = q and q:FindFirstChild("HumanoidRootPart")
                        local G = wasRecentKickAttempt(M.Name, 12)
                        local d = getKickAttemptAge(M.Name)
                        if r and (r.Health <= 0 and not G) then return end
                        if G and d < .55 then return end
                        local z = G and 85 or 45
                        if j and ((j.Position - u)).Magnitude > z then return end
                        local Y = M.DisplayName or "Unknown"
                        local P = M.Name or "Unknown"
                        local O = gN.kickAttemptMethods[string.lower(P)]
                        local a = G
                        if not a then
                            local q = M:FindFirstChild("IsHeld")
                            a = (q and (q:IsA("BoolValue") and q.Value)) or (r and ((r.PlatformStand or r.Sit))) or false
                        end
                        if not a then return end
                        rememberKickedPlayer(M)
                        if gN.targetTrackName and M.Name == gN.targetTrackName then gN.targetTrackPendingReturn = true end
                        local o
                        if G then o = Y .. (" (" .. (P .. (") Got Kicked!" .. ((O and (" [" .. (tostring(O) .. "]")) or ""))))) else o =
                            "Kick hit detected near " .. (Y .. (" (" .. (P .. ")"))) end
                        c:Notify({ Title = "Wourld Hub", Description = o, Duration = 3 })
                        playEventSound("kick")
                        clearKickAttempt(P)
                    end
                end)
            else if p then
                    p:Disconnect()
                    p = nil
                end end end })
    VisualsLeft:AddToggle("PacketLagNotifyToggle",
        { Text = "Packet Lag Notify", Default = true, Callback = function(q)
            packetLagNotifyEnabled = q and true or false
            if q and not lastLagSource then StartPacketLagDetector() end
        end })
    VisualsLeft:AddToggle("FriendJoinNotifyToggle",
        { Text = "Friend Join Notify", Default = true, Callback = function(q) gN.friendJoinNotify = q and true or false end })
    VisualsCombat = w.Visuals:AddLeftGroupbox("Combat Visuals")
    VisualsCombat:AddToggle("DynamicEspToggle",
        { Text = "Dynamic ESP", Default = false, Callback = function(q)
            gN.dynamicEsp = q
            RefreshSmartVisualLoop()
        end })
    VisualsCombat:AddToggle("ThreatHighlightToggle",
        { Text = "Threat Highlight", Default = false, Callback = function(q)
            gN.threatHighlight = q
            RefreshSmartVisualLoop()
        end })
    VisualsCombat:AddToggle("OffscreenIndicatorToggle",
        { Text = "Offscreen Indicator", Default = false, Callback = function(q)
            gN.offscreenIndicator = q
            RefreshSmartVisualLoop()
        end })
    VisualsCombat:AddDropdown("FocusModeDropdown",
        { Text = "Focus Mode", Values = { "All", "Target Only", "Threat Only" }, Default = "All", Callback = function(q) gN.focusMode =
            q end })
    VisualsBliz = w.Visuals:AddRightGroupbox("Tactical ESP")
    do
        local q, c = pcall(function() return w.Visuals:AddRightGroupbox("Kick FX") end)
        VisualsFx = (q and c) or VisualsBliz
        local r, j = pcall(function() return w.Visuals:AddRightGroupbox("Main Visuals") end)
        VisualsMain = (r and j) or VisualsBliz
        local u, M = pcall(function() return w.Visuals:AddLeftGroupbox("World Visuals") end)
        VisualsWorld = (u and M) or VisualsLeft
        local G, d = pcall(function() return w.Visuals:AddLeftGroupbox("Extra Visuals") end)
        VisualsOther = (G and d) or VisualsLeft
        local z, Y = pcall(function() return w.Visuals:AddLeftGroupbox("Player Visuals") end)
        VisualsHat = (z and Y) or VisualsLeft
    end
    VisualsBliz:AddToggle("BlizHighlightToggle",
        { Text = "Highlight ESP", Default = false, Callback = function(q) setBlizHighlightState(q) end })
    VisualsBliz:AddColorPicker("BlizHighlightFillColor",
        { Title = "Highlight Fill", Default = Color3.fromRGB(255, 84, 84), Callback = function(q)
            wN.blizHighlightFillColor = q
            if wN.blizHighlightEnabled then updateBlizHighlights() end
        end })
    VisualsBliz:AddColorPicker("BlizHighlightOutlineColor",
        { Title = "Highlight Outline", Default = Color3.fromRGB(255, 255, 255), Callback = function(q)
            wN.blizHighlightOutlineColor = q
            if wN.blizHighlightEnabled then updateBlizHighlights() end
        end })
    VisualsBliz:AddSlider("BlizHighlightFillTransparency",
        { Text = "Fill Transparency", Default = 55, Min = 0, Max = 100, Rounding = 0, Callback = function(q)
            wN.blizHighlightFillTransparency = q / 100
            if wN.blizHighlightEnabled then updateBlizHighlights() end
        end })
    VisualsBliz:AddSlider("BlizHighlightOutlineTransparency",
        { Text = "Outline Transparency", Default = 5, Min = 0, Max = 100, Rounding = 0, Callback = function(q)
            wN.blizHighlightOutlineTransparency = q / 100
            if wN.blizHighlightEnabled then updateBlizHighlights() end
        end })
    VisualsBliz:AddDropdown("BlizHighlightMode",
        { Text = "Highlight Mode", Values = { "AlwaysOnTop", "Occluded" }, Default = "AlwaysOnTop", Callback = function(
            q)
            if q == "Occluded" then wN.blizHighlightMode = Enum.HighlightDepthMode.Occluded else wN.blizHighlightMode =
                Enum.HighlightDepthMode.AlwaysOnTop end
            if wN.blizHighlightEnabled then updateBlizHighlights() end
        end })
    VisualsBliz:AddToggle("BlizIconToggle",
        { Text = "Avatar Icon ESP", Default = false, Callback = function(q) setBlizIconState(q) end })
    VisualsBliz:AddSlider("BlizIconYOffset",
        { Text = "Icon Height", Default = 10, Min = 4, Max = 20, Rounding = 0, Callback = function(q)
            wN.blizIconYOffset = q
            if wN.blizIconEnabled then updateBlizIcons() end
        end })
    VisualsBliz:AddSlider("BlizIconScale",
        { Text = "Icon Scale", Default = 100, Min = 60, Max = 220, Rounding = 0, Callback = function(q)
            wN.blizIconScale = q / 100
            if wN.blizIconEnabled then updateBlizIcons() end
        end }); (VisualsFx:AddToggle("KickFXToggle", { Text = "Kick FX", Default = true, Callback = function(q) kickFxEnabled =
        q and true or false end })):AddColorPicker("KickLineStartColorPicker",
        { Default = wN.kickLineColorStart, Title = "Kick Line Start", Callback = function(q) wN.kickLineColorStart = q end }); (VisualsFx:AddLabel("Kick Line End"))
        :AddColorPicker("KickLineEndColorPicker",
            { Default = wN.kickLineColorEnd, Title = "Kick Line End", Callback = function(q) wN.kickLineColorEnd = q end }); (VisualsFx:AddLabel("Kick Ring Color"))
        :AddColorPicker("KickRingColorPicker",
            { Default = wN.kickRingColor, Title = "Kick Ring Color", Callback = function(q) wN.kickRingColor = q end }); (VisualsFx:AddLabel("Kick Pulse Color"))
        :AddColorPicker("KickPulseColorPicker",
            { Default = wN.kickPulseColor, Title = "Kick Pulse Color", Callback = function(q) wN.kickPulseColor = q end })
    VisualsFx:AddSlider("KickLineWidthSlider",
        { Text = "Kick Line Width", Default = math.floor(((wN.kickLineWidth or .2)) * 100), Min = 8, Max = 120, Rounding = 0, Callback = function(
            q) wN.kickLineWidth = q / 100 end })
    VisualsFx:AddSlider("KickRingSizeSlider",
        { Text = "Kick Ring Size", Default = math.floor(((wN.kickRingRadius or 5.5)) * 10), Min = 20, Max = 180, Rounding = 0, Callback = function(
            q) wN.kickRingRadius = q / 10 end })
    VisualsFx:AddToggle("AngelAuraToggle",
        { Text = "Angel Aura", Default = false, Callback = function(q)
            angelAuraEnabled = q and true or false
            if angelAuraEnabled then startAngelAuraLoop() else clearAngelAura() end
        end }); (VisualsFx:AddLabel("Wing Base Color")):AddColorPicker("AngelWingBaseColorPicker",
        { Default = wN.angelWingColor, Title = "Wing Base Color", Callback = function(q)
            wN.angelWingColor = q
            if angelAuraEnabled then refreshAngelWingPalette() end
        end }); (VisualsFx:AddLabel("Wing Accent Color")):AddColorPicker("AngelWingAccentColorPicker",
        { Default = wN.angelWingAccentColor, Title = "Wing Accent Color", Callback = function(q)
            wN.angelWingAccentColor = q
            if angelAuraEnabled then refreshAngelWingPalette() end
        end })
    VisualsFx:AddSlider("AngelWingOffsetXSlider",
        { Text = "Wing Offset X", Default = math.floor(((wN.angelWingOffsetX or 1.4)) * 10), Min = 5, Max = 40, Rounding = 0, Callback = function(
            q) wN.angelWingOffsetX = q / 10 end })
    VisualsFx:AddSlider("AngelWingOffsetYSlider",
        { Text = "Wing Offset Y", Default = math.floor(((wN.angelWingOffsetY or .5)) * 10), Min = -20, Max = 30, Rounding = 0, Callback = function(
            q) wN.angelWingOffsetY = q / 10 end })
    VisualsFx:AddSlider("AngelWingOffsetZSlider",
        { Text = "Wing Offset Z", Default = math.floor(((wN.angelWingOffsetZ or .7)) * 10), Min = -20, Max = 30, Rounding = 0, Callback = function(
            q) wN.angelWingOffsetZ = q / 10 end })
    pcall(function() rebuildMusicPlaylist() end)
    do
        local q, c = pcall(function() return w.Visuals:AddRightGroupbox("Music Player") end)
        gN.musicBox = (q and c) or VisualsBliz
    end
    gN.musicBox:AddToggle("MusicPlayerEnableToggle",
        { Text = "Enable Music Player", Default = false, Callback = function(q) setMusicEnabled(q) end })
    gN.musicBox:AddToggle("MusicWidgetToggle",
        { Text = "Show Music Widget", Default = true, Callback = function(q) setMusicWidgetVisible(q) end })
    gN.musicBox:AddToggle("MusicAutoNextToggle",
        { Text = "Auto Next", Default = true, Callback = function(q) gN.musicAutoNext = q and true or false end })
    if type(gN.musicDropdownValues) ~= "table" or #gN.musicDropdownValues == 0 then gN.musicDropdownValues = {
            "No tracks" } end
    if not table.find(gN.musicDropdownValues, tostring(gN.musicSelectedName or "")) then gN.musicSelectedName = gN
        .musicDropdownValues[1] end
    gN.musicBox:AddDropdown("MusicTrackDropdown",
        { Text = "Track", Values = gN.musicDropdownValues, Default = gN.musicSelectedName ~= "" and gN.musicSelectedName or
        (gN.musicDropdownValues[1] or "No tracks"), Callback = function(q)
            if gN.musicDropdownSync then
                gN.musicSelectedName = tostring(q or "")
                return
            end
            gN.musicSelectedName = tostring(q or "")
            setMusicSelectedTrack(gN.musicSelectedName)
        end })
    gN.musicBox:AddSlider("MusicVolumeSlider",
        { Text = "Volume", Default = math.floor(((tonumber(gN.musicVolume) or .55)) * 100), Min = 0, Max = 100, Rounding = 0, Callback = function(
            q)
            gN.musicVolume = math.clamp(((tonumber(q) or 55)) / 100, 0, 1)
            if gN.musicSound then gN.musicSound.Volume = gN.musicVolume end
        end })
    gN.musicBox:AddButton({ Text = "Play Selected", Func = function() playSelectedMusicTrack() end, DoubleClick = false })
    gN.musicBox:AddButton({ Text = "Pause / Resume", Func = function() togglePauseMusicTrack() end, DoubleClick = false })
    gN.musicBox:AddButton({ Text = "Stop", Func = function() stopMusicTrack() end, DoubleClick = false })
    gN.musicBox:AddButton({ Text = "Next Track", Func = function() playNextMusicTrack() end, DoubleClick = false })
    gN.musicBox:AddButton({ Text = "Previous Track", Func = function() playPreviousMusicTrack() end, DoubleClick = false })
    gN.musicBox:AddButton({ Text = "Refresh Music Folder", Func = function()
        rebuildMusicPlaylist()
        c:Notify({ Title = "Wourld Hub", Description = "Music list refreshed", Duration = 2.5 })
    end, DoubleClick = false })
    gN.musicBox:AddButton({ Text = "Init Music Folder", Func = function()
        local q = initMusicFolderTemplate()
        if q then rebuildMusicPlaylist() end
    end, DoubleClick = false })
    gN.musicBox:AddInput("MusicTrackNameInput",
        { Text = "Track Name", Default = gN.musicInputName or "", Placeholder = "custom name", Callback = function(q) gN.musicInputName =
            tostring(q or "") end })
    gN.musicBox:AddInput("MusicTrackIdInput",
        { Text = "Track ID", Default = gN.musicInputId or "", Placeholder = "rbxassetid://123456", Callback = function(q) gN.musicInputId =
            tostring(q or "") end })
    gN.musicBox:AddButton({ Text = "Save Track To Folder", Func = function()
        local q, r = appendMusicTrackToFolder(gN.musicInputName, gN.musicInputId)
        if q then
            rebuildMusicPlaylist()
            c:Notify({ Title = "Wourld Hub", Description = "Track saved", Duration = 2.5 })
        else c:Notify({ Title = "Wourld Hub", Description = "Save failed: " .. tostring(r), Duration = 3 }) end
    end, DoubleClick = false })
    AllunVisualsHatBox = VisualsHat
    AllunVisualsSkinBox = VisualsMain
    AllunVisualsWorldBox = VisualsWorld
    AllunVisualsOtherBox = VisualsOther; (AllunVisualsHatBox:AddToggle("AllunVisualHatToggle", { Text = "Enable Chinese Hat", Default = false, Callback = function(
        q)
        allunVisuals.HatEnabled = q and true or false
        local c = b.Character
        if c then if q then allunVisuals.addHat(c) else allunVisuals.removeHat(c) end end
    end })):AddColorPicker("AllunVisualHatColor",
        { Default = allunVisuals.HatColor, Title = "Hat Color", Callback = function(q) allunVisuals.HatColor = q end })
    AllunVisualsHatBox:AddToggle("AllunVisualHatRainbowToggle",
        { Text = "Rainbow Hat", Default = false, Callback = function(q) allunVisuals.HatRainbow = q and true or false end })
    AllunVisualsHatBox:AddSlider("AllunVisualHatTransparency",
        { Text = "Hat Transparency", Default = math.floor(((allunVisuals.HatTransparency or .3)) * 100), Min = 0, Max = 100, Rounding = 0, Callback = function(
            q) allunVisuals.HatTransparency = math.clamp(((tonumber(q) or 30)) / 100, 0, 1) end }); (AllunVisualsHatBox:AddToggle("AllunVisualTrailToggle", { Text = "Enable Trail", Default = false, Callback = function(
        q)
        allunVisuals.TrailEnabled = q and true or false
        local c = b.Character
        if c then if q then allunVisuals.addTrail(c) else allunVisuals.removeTrail(c) end end
    end })):AddColorPicker("AllunVisualTrailColor",
        { Default = allunVisuals.TrailColorStatic, Title = "Trail Color", Callback = function(q) allunVisuals.TrailColorStatic =
            q end })
    AllunVisualsHatBox:AddToggle("AllunVisualTrailGradientToggle",
        { Text = "Use Gradient Mode", Default = false, Callback = function(q)
            allunVisuals.TrailGradient = q and true or false
            if allunVisuals.TrailEnabled and b.Character then allunVisuals.addTrail(b.Character) end
        end }); (AllunVisualsHatBox:AddLabel("Trail Gradient 1")):AddColorPicker("AllunVisualTrailGradient1",
        { Default = allunVisuals.TrailGradient1, Title = "Gradient Color 1", Callback = function(q)
            allunVisuals.TrailGradient1 = q
            if allunVisuals.TrailEnabled and (allunVisuals.TrailGradient and b.Character) then allunVisuals.addTrail(b
                .Character) end
        end }); (AllunVisualsHatBox:AddLabel("Trail Gradient 2")):AddColorPicker("AllunVisualTrailGradient2",
        { Default = allunVisuals.TrailGradient2, Title = "Gradient Color 2", Callback = function(q)
            allunVisuals.TrailGradient2 = q
            if allunVisuals.TrailEnabled and (allunVisuals.TrailGradient and b.Character) then allunVisuals.addTrail(b
                .Character) end
        end })
    AllunVisualsHatBox:AddToggle("AllunVisualTrailRainbowToggle",
        { Text = "Trail Rainbow", Default = false, Callback = function(q) allunVisuals.TrailRainbow = q and true or false end })
    AllunVisualsHatBox:AddSlider("AllunVisualTrailLifetime",
        { Text = "Trail Lifetime", Default = math.floor(((allunVisuals.TrailLifetime or .5)) * 10), Min = 1, Max = 30, Rounding = 0, Callback = function(
            q) allunVisuals.TrailLifetime = math.max(.1, ((tonumber(q) or 5)) / 10) end })
    AllunVisualsHatBox:AddSlider("AllunVisualTrailTransparency",
        { Text = "Trail Transparency", Default = math.floor(((allunVisuals.TrailTransparencyStart or 0)) * 100), Min = 0, Max = 100, Rounding = 0, Callback = function(
            q) allunVisuals.TrailTransparencyStart = math.clamp(((tonumber(q) or 0)) / 100, 0, 1) end }); (AllunVisualsSkinBox:AddToggle("AllunVisualForceFieldToggle", { Text = "Enable ForceField", Default = false, Callback = function(
        q)
        allunVisuals.ForceFieldEnabled = q and true or false
        local c = b.Character
        if c then if q then allunVisuals.applyForceField(c) else allunVisuals.removeForceField(c) end end
    end })):AddColorPicker("AllunVisualForceFieldColor",
        { Default = allunVisuals.ForceFieldColor, Title = "ForceField Color", Callback = function(q)
            allunVisuals.ForceFieldColor = q
            if allunVisuals.ForceFieldEnabled and (b.Character and not allunVisuals.ForceFieldRainbow) then allunVisuals
                    .applyForceField(b.Character) end
        end })
    AllunVisualsSkinBox:AddToggle("AllunVisualForceFieldRainbowToggle",
        { Text = "Rainbow ForceField", Default = false, Callback = function(q) allunVisuals.ForceFieldRainbow = q and
            true or false end }); (AllunVisualsSkinBox:AddToggle("AllunVisualSkinTrailToggle", { Text = "Enable Skin Trail", Default = false, Callback = function(
        q)
        allunVisuals.SkinTrailEnabled = q and true or false
        allunVisuals.toggleSkinTrail(q)
    end })):AddColorPicker("AllunVisualSkinTrailColor",
        { Default = allunVisuals.SkinTrailColor, Title = "Skin Trail Color", Callback = function(q)
            allunVisuals.SkinTrailColor = q
            if allunVisuals.SkinTrailEnabled then allunVisuals.updateSkinTrail() end
        end })
    AllunVisualsSkinBox:AddSlider("AllunVisualSkinTrailLife",
        { Text = "Skin Trail Life", Default = math.floor(((allunVisuals.SkinTrailLife or .5)) * 10), Min = 1, Max = 30, Rounding = 0, Callback = function(
            q)
            allunVisuals.SkinTrailLife = math.max(.1, ((tonumber(q) or 5)) / 10)
            if allunVisuals.SkinTrailEnabled then allunVisuals.updateSkinTrail() end
        end })
    AllunVisualsSkinBox:AddToggle("AllunVisualAuraToggle",
        { Text = "Enable Local Aura", Default = false, Callback = function(q)
            allunVisuals.AuraEnabled = q and true or false
            if q then
                if not allunVisuals.CurrentAuraModel then allunVisuals.updateAuraLogic() end
                if b.Character then allunVisuals.enableAura(b.Character) end
            else allunVisuals.disableAura() end
        end })
    gN.allunAuraList = {}
    gN.allunAuraIterKey = nil
    while true do
        gN.allunAuraIterKey = next(allunVisuals.AuraModels, gN.allunAuraIterKey)
        if not gN.allunAuraIterKey then break end
        table.insert(gN.allunAuraList, gN.allunAuraIterKey)
    end
    table.sort(gN.allunAuraList)
    gN.allunAuraIterKey = nil
    if #gN.allunAuraList == 0 then table.insert(gN.allunAuraList, "Godly") end
    if not table.find(gN.allunAuraList, allunVisuals.AuraType) then allunVisuals.AuraType = gN.allunAuraList[1] end
    AllunVisualsSkinBox:AddDropdown("AllunVisualAuraTypeDropdown",
        { Text = "Aura Type", Values = gN.allunAuraList, Default = allunVisuals.AuraType, Multi = false, Callback = function(
            q)
            allunVisuals.AuraType = q
            allunVisuals.CustomAuraID = ""
            if allunVisuals.AuraEnabled then allunVisuals.updateAuraLogic() end
        end })
    AllunVisualsSkinBox:AddInput("AllunVisualCustomAuraInput",
        { Text = "Custom Aura ID", Default = "", Placeholder = "Asset ID", Callback = function(q)
            allunVisuals.CustomAuraID = (tostring(q or "")):match("^%s*(.-)%s*$")
            if allunVisuals.AuraEnabled and allunVisuals.CustomAuraID ~= "" then allunVisuals.updateAuraLogic() end
        end })
    allunSkyList = {}
    for q in pairs(allunVisuals.SkyboxAssets) do table.insert(allunSkyList, q) end
    table.sort(allunSkyList)
    if #allunSkyList == 0 then table.insert(allunSkyList, "HD") end
    if not table.find(allunSkyList, allunVisuals.CurrentSkybox) then allunVisuals.CurrentSkybox = allunSkyList[1] end
    AllunVisualsWorldBox:AddDropdown("AllunVisualSkyboxDropdown",
        { Text = "Select Skybox", Values = allunSkyList, Default = allunVisuals.CurrentSkybox, Multi = false, Callback = function(
            q)
            allunVisuals.CurrentSkybox = q
            allunVisuals.CustomSkyEnabled = true
            allunVisuals.applySkybox(q)
        end })
    AllunVisualsWorldBox:AddToggle("AllunVisualSkyboxToggle",
        { Text = "Enable Custom Skybox", Default = false, Callback = function(q)
            allunVisuals.CustomSkyEnabled = q and true or false
            if q then allunVisuals.applySkybox(allunVisuals.CurrentSkybox) else allunVisuals.restoreDefaultSky() end
        end }); (AllunVisualsWorldBox:AddToggle("AllunVisualNebulaToggle", { Text = "Nebula Theme", Default = false, Callback = function(
        q) allunVisuals.setNebulaEnabled(q) end })):AddColorPicker("AllunVisualNebulaColor",
        { Default = allunVisuals.NebulaThemeColor, Title = "Nebula Color", Callback = function(q)
            allunVisuals.NebulaThemeColor = q
            if allunVisuals.NebulaEnabled then
                allunVisuals.setNebulaEnabled(false)
                allunVisuals.setNebulaEnabled(true)
            end
        end })
    AllunVisualsWorldBox:AddToggle("AllunVisualTimeToggle",
        { Text = "Enable Time Changer", Default = false, Callback = function(q) allunVisuals.WorldTimeEnabled = q and
            true or false end })
    AllunVisualsWorldBox:AddSlider("AllunVisualTimeValue",
        { Text = "Time (0-24)", Default = allunVisuals.WorldTimeValue, Min = 0, Max = 24, Rounding = 1, Callback = function(
            q) allunVisuals.WorldTimeValue = q end })
    AllunVisualsWorldBox:AddToggle("AllunVisualFullBrightToggle",
        { Text = "Full Bright", Default = false, Callback = function(q) allunVisuals.setFullBrightEnabled(q) end })
    AllunVisualsWorldBox:AddSlider("AllunVisualFovSlider",
        { Text = "Allun FOV", Default = q(), Min = 40, Max = 120, Rounding = 0, Callback = function(q)
            local c = Y.CurrentCamera
            if c then c.FieldOfView = q end
        end })
    AllunVisualsOtherBox:AddToggle("AllunVisualScreenToggle",
        { Text = "Enable Screen Effect", Default = false, Callback = function(q) allunVisuals.setScreenEnabled(q) end })
    AllunVisualsOtherBox:AddSlider("AllunVisualScreenIntensity",
        { Text = "Screen Stretch", Default = allunVisuals.ScreenIntensity, Min = 0, Max = .2, Rounding = 3, Callback = function(
            q) allunVisuals.ScreenIntensity = q end })
    AllunVisualsOtherBox:AddToggle("AllunVisualAnimeImageToggle",
        { Text = "Anime Image", Default = false, Callback = function(q) allunVisuals.toggleAnimeImage(q) end }); (AllunVisualsOtherBox:AddToggle("AllunEspOutlineToggle", { Text = "ESP Outline", Default = false, Callback = function(
        q) setBlizHighlightState(q) end })):AddColorPicker("AllunEspOutlineColor",
        { Default = wN.blizHighlightOutlineColor, Title = "Outline Color", Callback = function(q)
            wN.blizHighlightOutlineColor = q
            if wN.blizHighlightEnabled then updateBlizHighlights() end
        end })
    AllunVisualsOtherBox:AddSlider("AllunEspOutlineTransparency",
        { Text = "Outline Transparency", Default = math.floor(((wN.blizHighlightOutlineTransparency or .05)) * 100), Min = 0, Max = 100, Rounding = 0, Callback = function(
            q)
            wN.blizHighlightOutlineTransparency = math.clamp(((tonumber(q) or 5)) / 100, 0, 1)
            if wN.blizHighlightEnabled then updateBlizHighlights() end
        end }); (AllunVisualsOtherBox:AddToggle("AllunEspNameToggle", { Text = "ESP Names", Default = false, Callback = function(
        q) setUsernameEspState(q) end })):AddColorPicker("AllunEspNameColor",
        { Default = wN.usernameColor, Title = "Name Color", Callback = function(q)
            wN.usernameColor = q
            if wN.usernameEnabled then updateUsernameEsp() end
        end })
    AllunVisualsOtherBox:AddToggle("AllunEspAvatarToggle",
        { Text = "ESP Avatar", Default = false, Callback = function(q) setBlizIconState(q) end })
    AllunVisualsOtherBox:AddSlider("AllunEspAvatarSize",
        { Text = "Avatar Size", Default = math.floor(((wN.blizIconScale or 1)) * 100), Min = 40, Max = 220, Rounding = 0, Callback = function(
            q)
            wN.blizIconScale = math.clamp(((tonumber(q) or 100)) / 100, .4, 2.2)
            if wN.blizIconEnabled then updateBlizIcons() end
        end })
    AllunVisualsOtherBox:AddButton({ Text = "Activate FPS/Ping Counter", Func = function() runExternalLoadstring(
        "https://raw.githubusercontent.com/GLAMOHGA/fling/refs/heads/main/%D1%85%D0%B7%20%D0%BA%D0%B0%D0%BA%20%D0%BD%D0%B0%D0%B7%D0%B2%D0%B0%D1%82%D1%8C%20%D1%82%D0%B8%D0%BF%D0%BE%20%D1%84%D0%BF%D1%81%20%D0%B8%20%D0%BF%D0%B8%D0%BD%D0%B3.md",
            "FPS/Ping Counter") end, DoubleClick = false })
    AllunVisualsOtherBox:AddButton({ Text = "Activate FPS/Ping Counter 2", Func = function() runExternalLoadstring(
        "https://raw.githubusercontent.com/VetrexTheBest/Fps-ping/refs/heads/main/fps%2Bping.txt", "FPS/Ping Counter 2") end, DoubleClick = false })
end)
if not la then c:Notify({ Title = "Wourld Hub", Description = "Visuals build error fixed-safe: " .. tostring(Da), Duration = 3 }) end
ServerLeft = w.Server:AddLeftGroupbox("Server Lag")
ServerLeft:AddToggle("ServerLagLineToggle",
    { Text = "Server Lag Line", Default = false, Callback = function(q)
        lagLineActive = q
        local c = u and u.LagLineIntensity
        local r = tonumber(c and c.Value) or 150
        if q then Z = task.spawn(function() ServerLagLineFunction(r) end) else if Z then
                task.cancel(Z)
                Z = nil
            end end
    end })
ServerLeft:AddSlider("LagLineIntensity",
    { Text = "Lag Line Intensity", Default = 150, Min = 1, Max = 1000, Rounding = 0, Callback = function(q) if lagLineActive then
            lagLineActive = false
            task.wait(.1)
            lagLineActive = true
            Z = task.spawn(function() ServerLagLineFunction(q) end)
        end end })
function getPacketLagPayload(q)
    local c = math.clamp(math.floor(tonumber(q) or 0), 0, 6250)
    if c <= 0 then return nil end
    local r = math.floor(c / 125) * 125
    local j = Q[r]
    if j then return j end
    local u = math.clamp(math.floor(r * 1.6), 64, 9500)
    local M = "WourldLagPayload|" .. (tostring(b.UserId) .. ("|" .. string.rep("WL", u)))
    Q[r] = M
    return M
end

ServerLeft:AddToggle("PacketLagToggle",
    { Text = "Packet Lag", Default = false, Callback = function(q)
        packetLagActive = q and true or false
        if I then
            task.cancel(I)
            I = nil
        end
        if packetLagActive then I = task.spawn(function()
                local q = d:FindFirstChild("GrabEvents")
                local c = q and q:FindFirstChild("ExtendGrabLine")
                if not c then
                    packetLagActive = false
                    F6 = true
                    if M and (M.PacketLagToggle and M.PacketLagToggle.Value) then M.PacketLagToggle:SetValue(false) end
                    if M and (M.AllunMiscLagToggle and M.AllunMiscLagToggle.Value) then M.AllunMiscLagToggle:SetValue(false) end
                    F6 = false
                    return
                end
                while packetLagActive do
                    local q = getPacketLagPayload(u.PacketLagStrength and u.PacketLagStrength.Value or 0)
                    if q then pcall(function() c:FireServer(q) end) end
                    task.wait(.9)
                end
            end) end
        F6 = true
        if M and (M.AllunMiscLagToggle and M.AllunMiscLagToggle.Value ~= packetLagActive) then M.AllunMiscLagToggle
                :SetValue(packetLagActive) end
        F6 = false
    end })
ServerLeft:AddSlider("PacketLagStrength",
    { Text = "Packet Lag Strength", Default = 1600, Min = 0, Max = 6250, Rounding = 1, Callback = function(q)
        getPacketLagPayload(q) end })
UtilityLeft = w.Server:AddRightGroupbox("Utility")
UtilityLeft:AddButton({ Text = "Show Session Stats", Func = function() ShowSessionStats() end, DoubleClick = false })
UtilityLeft:AddButton({ Text = "Rejoin Server", Func = function()
    local q = game:GetService("TeleportService")
    q:Teleport(game.PlaceId, b)
end, DoubleClick = false })
UtilityLeft:AddSlider("SwitchServerMaxPlayers",
    { Text = "Switch Max Players", Default = 8, Min = 1, Max = 30, Rounding = 0, Callback = function(q) gN.switchServerMaxPlayers =
        math.clamp(tonumber(q) or 8, 1, 30) end })
UtilityLeft:AddButton({ Text = "Server Switch", Func = function()
    local q = tonumber(gN.switchServerMaxPlayers) or (u.SwitchServerMaxPlayers and u.SwitchServerMaxPlayers.Value) or 8
    QuickServerSwitch(q)
end, DoubleClick = false })
UtilityLeft:AddToggle("AutoRejoinToggle", { Text = "Auto Rejoin", Default = false, Callback = function(q)
    SetAutoRejoinState(q) end })
UtilityLeft:AddToggle("LeaveOnResetFailToggle",
    { Text = "Leave If Reset Fails", Default = m6, Callback = function(q) m6 = q and true or false end })
UtilityLeft:AddToggle("LoopBugAutoLeaveToggle",
    { Text = "Auto Leave On Loop Bug", Default = gN.loopBugAutoLeave, Callback = function(q) gN.loopBugAutoLeave = q and
        true or false end })
UtilityLeft:AddToggle("PerformanceModeToggle",
    { Text = "Performance Mode", Default = false, Callback = function(q)
        s6 = q
        applyLowGraphicsState(q)
    end })
UtilityLeft:AddToggle("AutoPerformanceToggle",
    { Text = "Auto Performance", Default = false, Callback = function(q) SetAutoPerformanceState(q) end })
UtilityLeft:AddSlider("AutoPerformanceFPS",
    { Text = "Performance FPS", Default = 35, Min = 15, Max = 90, Rounding = 0, Callback = function(q) gN.perfThreshold =
        q end })
KeybindLeft = w.Keybinds:AddLeftGroupbox("Keybinds")
KeybindRight = w.Keybinds:AddRightGroupbox("Actions")
KeybindLeft:AddToggle("ClickTpToggle",
    { Text = "Click TP", Default = false, Callback = function(q) clickTpEnabled = q and true or false end }); (KeybindLeft:AddLabel("Click TP Key"))
    :AddKeyPicker("ClickTpKeyPicker",
        { Default = "C", Mode = "Press", Text = "Click TP Key", NoUI = false, ChangedCallback = function(q) clickTpKey =
            normalizeRuntimeKeyCode(q, Enum.KeyCode.C) end }); (KeybindLeft:AddLabel("Remove Legs Key")):AddKeyPicker(
"RemoveLegsKeyPicker",
    { Default = "Y", Mode = "Press", Text = "Remove Legs Key", NoUI = false, ChangedCallback = function(q) removeLegsKey =
        normalizeRuntimeKeyCode(q, Enum.KeyCode.Y) end }); (KeybindLeft:AddLabel("Remove Arms Key")):AddKeyPicker(
"RemoveArmsKeyPicker",
    { Default = "U", Mode = "Press", Text = "Remove Arms Key", NoUI = false, ChangedCallback = function(q) removeArmsKey =
        normalizeRuntimeKeyCode(q, Enum.KeyCode.U) end }); (KeybindLeft:AddLabel("Remove All Limbs")):AddKeyPicker(
"RemoveLimbsKeyPicker",
    { Default = "I", Mode = "Press", Text = "Remove Limbs Key", NoUI = false, ChangedCallback = function(q) removeLimbsKey =
        normalizeRuntimeKeyCode(q, Enum.KeyCode.I) end }); (KeybindLeft:AddLabel("Backtrack Key")):AddKeyPicker(
"BacktrackKeyPicker",
    { Default = "B", Mode = "Press", Text = "Backtrack Key", NoUI = false, ChangedCallback = function(q) backtrackKey =
        normalizeRuntimeKeyCode(q, Enum.KeyCode.B) end }); (KeybindLeft:AddLabel("Freeze Grab Key")):AddKeyPicker(
"FreezeGrabKeyPicker",
    { Default = "G", Mode = "Press", Text = "Freeze Grab Key", NoUI = false, ChangedCallback = function(q) freezeGrabKey =
        normalizeRuntimeKeyCode(q, Enum.KeyCode.G) end })
KeybindLeft:AddToggle("ItemShooterToggle",
    { Text = "Item Shooter", Default = false, Callback = function(q) z6 = q and true or false end }); (KeybindLeft:AddLabel("Item Shooter Key"))
    :AddKeyPicker("ItemShooterKeyPicker",
        { Default = "V", Mode = "Press", Text = "Shooter Key", NoUI = false, ChangedCallback = function(q) O6 =
            normalizeRuntimeKeyCode(q, Enum.KeyCode.V) end })
KeybindRight:AddToggle("BacktrackRecordToggle",
    { Text = "Backtrack Record", Default = false, Callback = function(q) setBacktrackState(q) end })
KeybindRight:AddSlider("BacktrackSecondsSlider",
    { Text = "Backtrack Seconds", Default = 11, Min = 3, Max = 25, Rounding = 0, Callback = function(q) Ua = math.clamp(
        ((tonumber(q) or 11)) / 10, .3, 2.5) end })
KeybindRight:AddButton({ Text = "Backtrack Now", Func = function() applyBacktrackNow() end, DoubleClick = false })
KeybindRight:AddButton({ Text = "Freeze Grabbed Object", Func = function() freezeGrabbedObject() end, DoubleClick = false })
KeybindRight:AddButton({ Text = "Remove Legs", Func = function() tryRemoveGrabbedLimbs(true, false, "Remove Legs") end, DoubleClick = false })
KeybindRight:AddButton({ Text = "Remove Arms", Func = function() tryRemoveGrabbedLimbs(false, true, "Remove Arms") end, DoubleClick = false })
KeybindRight:AddButton({ Text = "Remove Legs + Arms", Func = function() tryRemoveGrabbedLimbs(true, true, "Remove Limbs") end, DoubleClick = false })
KeybindRight:AddInput("ItemShooterNameInput",
    { Default = Y6, Numeric = false, Finished = true, Text = "Shooter Item Name", Placeholder =
    "FoodHamburger / Anvil / CreatureBlobman", Callback = function(q)
        local c = ((tostring(q or "")):gsub("^%s+", "")):gsub("%s+$", "")
        if c ~= "" then Y6 = c end
    end })
KeybindRight:AddSlider("ItemShooterVelocitySlider",
    { Text = "Shooter Velocity", Default = P6, Min = 50, Max = 3000, Rounding = 0, Callback = function(q) P6 = math
        .clamp(tonumber(q) or 800, 50, 3000) end })
KeybindRight:AddButton({ Text = "Shoot Item Now", Func = function() fireSelectedItemProjectile() end, DoubleClick = false })
OwnerLeft = w.Owner:AddLeftGroupbox("Owner")
OwnerRight = w.Owner:AddRightGroupbox("Owner Loops")
OwnerToys = w.Owner:AddRightGroupbox("Toys Menu+")
OwnerLeft:AddDropdown("HouseRavagePlotDropdown",
    { Text = "House Plot", Values = getPlotNameList(), Default = a6, Callback = function(q) a6 = tostring(q or a6) end })
OwnerLeft:AddSlider("HouseRavagePowerSlider",
    { Text = "House Ravage Power", Default = o6, Min = 80, Max = 900, Rounding = 0, Callback = function(q) o6 = math
        .clamp(tonumber(q) or 220, 80, 900) end })
OwnerLeft:AddButton({ Text = "House Ravage", Func = function() runHouseRavage(a6) end, DoubleClick = false })
OwnerLeft:AddToggle("HomeGuardToggle",
    { Text = "Home Guard (Blob/Tractor)", Default = gN.homeGuardActive, Callback = function(q) setHomeGuardState(q) end })
OwnerLeft:AddDropdown("HomeGuardModeDropdown",
    { Text = "Home Guard Mode", Values = { "UnderMap", "Fling", "Both" }, Default = gN.homeGuardMode, Callback = function(
        q) gN.homeGuardMode = tostring(q or "UnderMap") end })
OwnerLeft:AddSlider("HomeGuardRadiusSlider",
    { Text = "Home Guard Radius", Default = gN.homeGuardRadius, Min = 45, Max = 420, Rounding = 0, Callback = function(q) gN.homeGuardRadius =
        math.clamp(tonumber(q) or 120, 45, 420) end })
OwnerLeft:AddInput("BuildPresetNameInput",
    { Text = "Build Preset Name", Default = gN.buildPresetName, Numeric = false, Finished = true, Placeholder = "base", Callback = function(
        q)
        local c = ((tostring(q or "")):gsub("^%s+", "")):gsub("%s+$", "")
        if c ~= "" then gN.buildPresetName = c end
    end })
OwnerLeft:AddDropdown("BuildPresetDropdown",
    { Text = "Build Presets", Values = gN.buildPresetValues, Default = gN.buildPresetSelected, AllowNone = true, Callback = function(
        q) gN.buildPresetSelected = tostring(q or gN.buildPresetSelected) end })
OwnerLeft:AddButton({ Text = "Refresh Build Presets", Func = function()
    refreshBuildPresetDropdown()
    c:Notify({ Title = "Wourld Hub", Description = "Build presets refreshed", Duration = 2 })
end, DoubleClick = false })
OwnerLeft:AddButton({ Text = "Save Build Preset", Func = function()
    local q, r = saveBuildPreset(gN.buildPresetName)
    if q then c:Notify({ Title = "Wourld Hub", Description = "Build preset saved: " ..
        (tostring(gN.buildPresetName) .. (" | items: " .. tostring(r or 0))), Duration = 2.8 }) else c:Notify({ Title =
        "Wourld Hub", Description = "Save preset failed: " .. tostring(r or "error"), Duration = 2.8 }) end
end, DoubleClick = false })
OwnerLeft:AddButton({ Text = "Load Build Preset", Func = function()
    local q = tostring(gN.buildPresetSelected or gN.buildPresetName or "")
    local r, j = loadBuildPreset(q)
    if r then c:Notify({ Title = "Wourld Hub", Description = "Build preset loaded: " ..
        (tostring(q) .. (" | placed: " .. tostring(j or 0))), Duration = 2.8 }) else c:Notify({ Title = "Wourld Hub", Description =
        "Load preset failed: " .. tostring(j or "error"), Duration = 2.8 }) end
end, DoubleClick = false })
OwnerLeft:AddButton({ Text = "Bring Right", Func = function() ownerBringHand(getSelectedTargetName(), "Right", "grab") end, DoubleClick = false })
OwnerLeft:AddButton({ Text = "Drop Right", Func = function() ownerBringHand(getSelectedTargetName(), "Right", "drop") end, DoubleClick = false })
OwnerLeft:AddButton({ Text = "Release Right", Func = function() ownerBringHand(getSelectedTargetName(), "Right",
        "release") end, DoubleClick = false })
OwnerLeft:AddButton({ Text = "Bring Combo", Func = function()
    Ta = true
    ownerBringOnce(getSelectedTargetName())
    Ta = false
end, DoubleClick = false })
OwnerLeft:AddButton({ Text = "Jump Target", Func = function()
    local q = G:FindFirstChild(getSelectedTargetName())
    local c = q and (q.Character and q.Character:FindFirstChildOfClass("Humanoid"))
    if c then c.Jump = true end
end, DoubleClick = false })
OwnerLeft:AddButton({ Text = "Sit Target", Func = function()
    local q = G:FindFirstChild(getSelectedTargetName())
    local c = q and (q.Character and q.Character:FindFirstChildOfClass("Humanoid"))
    if c then c.Sit = not c.Sit end
end, DoubleClick = false })
OwnerRight:AddToggle("OwnerBringLoopToggle",
    { Text = "Loop Bring", Default = false, Callback = function(q)
        Ta = q and true or false
        if Ta then
            if ea then
                task.cancel(ea)
                ea = nil
            end
            ea = task.spawn(function() while Ta do
                    ownerBringOnce(getSelectedTargetName())
                    task.wait(.25)
                end end)
        else if ea then
                task.cancel(ea)
                ea = nil
            end end
    end })
OwnerRight:AddToggle("OwnerWalkMeToggle",
    { Text = "Loop Walk To Me", Default = false, Callback = function(q) if q then
            startOwnerWalkLoop("me")
            if M.OwnerWalkMouseToggle and M.OwnerWalkMouseToggle.Value then M.OwnerWalkMouseToggle:SetValue(false) end
        else
            ha = false
            if not Va then stopOwnerWalkLoop() end
        end end })
OwnerRight:AddToggle("OwnerWalkMouseToggle",
    { Text = "Loop Walk To Mouse", Default = false, Callback = function(q) if q then
            startOwnerWalkLoop("mouse")
            if M.OwnerWalkMeToggle and M.OwnerWalkMeToggle.Value then M.OwnerWalkMeToggle:SetValue(false) end
        else
            Va = false
            if not ha then stopOwnerWalkLoop() end
        end end })
OwnerToys:AddDropdown("ToyLoopSpawnItemDropdown",
    { Text = "Loop Spawn Item", Values = { "BombMissile", "FireworkMissile", "BombBalloon", "BombDarkMatter", "BallSnowball", "NinjaShuriken", "PalletLightBrown" }, Default =
    toyLoopSpawnItem, Callback = function(q) toyLoopSpawnItem = tostring(q or toyLoopSpawnItem) end })
OwnerToys:AddToggle("ToyLoopSpawnToggle",
    { Text = "Loop Spawn", Default = false, Callback = function(q)
        toyLoopSpawnActive = q and true or false
        refreshToyLoopSpawn()
    end })
OwnerToys:AddSlider("ToyLoopSpawnIntervalSlider",
    { Text = "Spawn Interval", Default = math.floor(((tonumber(toyLoopSpawnInterval) or .25)) * 100), Min = 5, Max = 300, Rounding = 0, Callback = function(
        q) toyLoopSpawnInterval = math.clamp(((tonumber(q) or 25)) / 100, .05, 3) end })
OwnerToys:AddSlider("ToyExplodeDelaySlider",
    { Text = "Explode Delay", Default = math.floor(((tonumber(toyExplodeDelay) or .05)) * 100), Min = 0, Max = 100, Rounding = 0, Callback = function(
        q) toyExplodeDelay = math.clamp(((tonumber(q) or 5)) / 100, 0, 1) end })
OwnerToys:AddButton({ Text = "Explode All Bombs", Func = function()
    local q = explodeAllBombsAtMouse(220)
    c:Notify({ Title = "Wourld Hub", Description = "All bombs triggered: " .. tostring(q), Duration = 2.6 })
end, DoubleClick = false })
OwnerToys:AddButton({ Text = "Explode My Bombs", Func = function()
    local q = explodeMyBombsAtMouse(140)
    c:Notify({ Title = "Wourld Hub", Description = "Bombs triggered: " .. tostring(q), Duration = 2.6 })
end, DoubleClick = false })
OwnerToys:AddButton({ Text = "Set Toy TP Position", Func = function()
    local q = b.Character
    local r = q and q:FindFirstChild("HumanoidRootPart")
    if r then
        toyAuraTpPos = r.CFrame
        c:Notify({ Title = "Wourld Hub", Description = "Toy TP position updated", Duration = 2 })
    end
end, DoubleClick = false })
OwnerToys:AddToggle("ToyFreezeAuraToggle",
    { Text = "Freeze Toy Aura", Default = false, Callback = function(q)
        toyFreezeAuraActive = q and true or false
        refreshToyAuraLoop()
    end })
OwnerToys:AddToggle("ToyTpAuraToggle",
    { Text = "TP Toy Aura", Default = false, Callback = function(q)
        toyTpAuraActive = q and true or false
        refreshToyAuraLoop()
    end })
OwnerToys:AddSlider("ToyAuraRadiusSlider",
    { Text = "Toy Aura Radius", Default = toyAuraRadius, Min = 8, Max = 220, Rounding = 0, Callback = function(q) toyAuraRadius =
        math.clamp(tonumber(q) or 24, 8, 220) end })
OwnerToys:AddButton({ Text = "Clear Toy Freeze", Func = function() cleanupToyAuraFreeze() end, DoubleClick = false })
refreshBuildPresetDropdown()
MiscLeft = w.Miscs:AddLeftGroupbox("Miscellaneous")
MiscAura = w.Miscs:AddRightGroupbox("Aura")
MiscAuraActions = w.Miscs:AddRightGroupbox("Aura Actions")
tpKey = Enum.KeyCode.C; (MiscLeft:AddLabel("Teleport")):AddKeyPicker("TeleportKey",
    { Default = "C", Mode = "Press", Text = "Teleport Key", NoUI = false, Callback = function() end, ChangedCallback = function(
        q) tpKey = normalizeRuntimeKeyCode(q, Enum.KeyCode.C) end })
MiscLeft:AddToggle("TeleportToggle", { Text = "Teleport Binder", Default = true, Callback = function(q) tpEnabled = q end })
MiscLeft:AddToggle("SmartTeleportToggle", { Text = "Smart Teleport", Default = true, Callback = function(q) gN.smartTeleport =
    q end })
MiscLeft:AddButton({ Text = "Open Inventory Plus", Func = function() runExternalLoadstring(
    "https://raw.githubusercontent.com/m1kp0/ftap/refs/heads/main/InventoryPlusV2.lua", "Inventory Plus") end, DoubleClick = false })
MiscLeft:AddButton({ Text = "Apply HDR Shader", Func = function() runExternalLoadstring(
    "https://raw.githubusercontent.com/SyntaxScript/HDR-Graphics/main/main.lua", "HDR Shader") end, DoubleClick = false })
MiscLeft:AddToggle("WaterWalkToggle", { Text = "Water Walk", Default = false, Callback = function(q) setWaterWalk(q) end })
MiscLeft:AddToggle("ToggleCursorMisc",
    { Text = "Toggle Cursor", Default = false, Callback = function(q) setToggleCursorState(q) end })
MiscLeft:AddToggle("DynamicWaterToggle",
    { Text = "Dynamic Water", Default = false, Callback = function(q) setDynamicWaterState(q) end }); (MiscLeft:AddToggle("PalletVisualToggle", { Text = "Pallet Visual", Default = false, Callback = function(
    q) setPalletVisualState(q) end })):AddColorPicker("PalletVisualColor",
    { Default = Color3.fromRGB(20, 20, 20), Title = "Pallet Color", Callback = function(q)
        za = q
        if Ga then refreshPalletVisuals() end
    end })
MiscLeft:AddSlider("PalletVisualTransparency",
    { Text = "Pallet Transparency", Default = 20, Min = 0, Max = 85, Rounding = 0, Callback = function(q)
        da = math.clamp(((tonumber(q) or 20)) / 100, 0, .85)
        if Ga then refreshPalletVisuals() end
    end })
MiscMovement = w.Miscs:AddLeftGroupbox("Movement Plus")
MiscMovement:AddToggle("SpeedLockToggle",
    { Text = "Speed Lock", Default = false, Callback = function(q)
        AN = q
        _G.SuperSpeed = q and true or false
        if q then startSpeedLockLoop() else stopSpeedLockLoop() end
    end })
MiscMovement:AddSlider("SpeedLockValue",
    { Text = "Speed Value", Default = 30, Min = 16, Max = 120, Rounding = 0, Callback = function(q)
        fN = q; (getgenv()).Multiplier = math.clamp(((tonumber(q) or 30)) / 180, .05, 5)
    end })
MiscMovement:AddToggle("JumpLockToggle",
    { Text = "Jump Lock", Default = false, Callback = function(q)
        mN = q
        if q then startJumpLockLoop() else stopJumpLockLoop() end
    end })
MiscMovement:AddSlider("JumpLockValue",
    { Text = "Jump Value", Default = 80, Min = 50, Max = 250, Rounding = 0, Callback = function(q)
        iN = q
        _G.InfiniteJumpPower = q
        local c = b.Character
        local r = c and c:FindFirstChildOfClass("Humanoid")
        if r then r.JumpPower = q end
    end })
MiscMovement:AddToggle("NoclipToggle",
    { Text = "Noclip", Default = false, Callback = function(q)
        pN = q
        _G.NoclipToggle = q and true or false
        if q then startNoclip() else stopNoclip() end
    end })
MiscMovement:AddToggle("NoclipGrabToggle",
    { Text = "Noclip Grab (Bliz)", Default = false, Callback = function(q) _G.NoclipGrab = q and true or false end })
MiscMovement:AddToggle("InfiniteJumpToggle",
    { Text = "Infinite Jump", Default = false, Callback = function(q)
        QN = q
        _G.InfiniteJump = q and true or false
        if q then
            local q = b.Character
            local c = q and q:FindFirstChildOfClass("Humanoid")
            if c then c.JumpPower = tonumber(_G.InfiniteJumpPower) or c.JumpPower end
            startInfiniteJump()
        else stopInfiniteJump() end
    end })
MiscMovement:AddToggle("AntiAfkToggle",
    { Text = "Anti AFK", Default = false, Callback = function(q)
        WN = q
        if q then startAntiAfk() else stopAntiAfk() end
    end })
MiscMovement:AddToggle("SpinbotToggle",
    { Text = "Spinbot", Default = false, Callback = function(q)
        kN = q
        if q then startSpinbot() else stopSpinbot() end
    end })
MiscMovement:AddSlider("SpinbotSpeedValue",
    { Text = "Spin Speed", Default = 8, Min = 1, Max = 50, Rounding = 0, Callback = function(q) KN = q end })
MiscMovement:AddToggle("GravityLockToggle",
    { Text = "Gravity Lock", Default = false, Callback = function(q)
        l6 = q
        refreshVisualEnhanceLoop()
    end })
MiscMovement:AddSlider("GravityValueSlider",
    { Text = "Gravity Value", Default = math.floor(J6), Min = 0, Max = 300, Rounding = 0, Callback = function(q)
        D6 = q
        if l6 then refreshVisualEnhanceLoop() end
    end })
MiscMovement:AddToggle("AutoRespawnToggle",
    { Text = "Auto Respawn", Default = w6, Callback = function(q) setAutoRespawnState(q) end })
MiscMovement:AddToggle("OrbitTargetToggle", { Text = "Orbit Player", Default = false, Callback = function(q)
    SetOrbitState(q) end })
MiscMovement:AddSlider("OrbitRadiusSlider",
    { Text = "Orbit Radius", Default = 12, Min = 4, Max = 45, Rounding = 0, Callback = function(q) gN.orbitRadius = q end })
MiscMovement:AddSlider("OrbitSpeedSlider",
    { Text = "Orbit Speed", Default = 18, Min = 2, Max = 80, Rounding = 0, Callback = function(q) gN.orbitSpeed = q / 10 end })
MiscMovement:AddToggle("AutoEdgeSaveToggle",
    { Text = "Auto Edge Save", Default = false, Callback = function(q) SetAutoEdgeSaveState(q) end })
MiscMovement:AddButton({ Text = "Reset Movement Defaults", Func = function()
    if _G.WourldMovementResetBusy then return end
    _G.WourldMovementResetBusy = true
    AN = false
    mN = false
    pN = false
    QN = false
    _G.NoclipGrab = false
    kN = false
    l6 = false
    stopSpeedLockLoop()
    stopJumpLockLoop()
    stopNoclip()
    stopInfiniteJump()
    stopSpinbot()
    applyGravityState()
    task.spawn(function()
        pcall(function()
            local q = { "SpeedLockToggle", "JumpLockToggle", "NoclipToggle", "NoclipGrabToggle", "InfiniteJumpToggle",
                "SpinbotToggle", "GravityLockToggle" }
            for q, c in ipairs(q) do
                local r = M and M[c]
                if r and (r.SetValue and r.Value ~= false) then pcall(function() r:SetValue(false) end) end
                if q % 2 == 0 then task.wait() end
            end
        end)
        _G.WourldMovementResetBusy = false
    end)
end, DoubleClick = false })
MiscAura:AddSlider("AuraRadius",
    { Text = "Aura Radius", Default = 15, Min = 1, Max = 20, Rounding = 0, Callback = function(q) removeAntiKickRadius =
        q end })
MiscAura:AddDropdown("RemoveAntiKickAuraItemDropdown",
    { Text = "Aura Item Filter", Values = { "All", "NinjaShuriken", "NinjaKunai", "AntiKick", "Kunai+Shuriken", "Shuriken+AntiKick" }, Default =
    _G.WourldRemoveAntiKickAuraItem or "All", Callback = function(q) _G.WourldRemoveAntiKickAuraItem = tostring(q or
        "All") end })
MiscAura:AddToggle("RemoveAntiKickAuraToggle",
    { Text = "Remove Anti Kick Aura", Default = false, Callback = function(q)
        removeAntiKickAuraActive = q and true or false
        if gN.removeAntiKickAuraTask then
            task.cancel(gN.removeAntiKickAuraTask)
            gN.removeAntiKickAuraTask = nil
        end
        if q then gN.removeAntiKickAuraTask = task.spawn(RemoveAntiKickAuraFunction) end
    end })
MiscAura:AddToggle("AuraWhitelistToggle",
    { Text = "Whitelist Friends", Default = true, Callback = function(q) useWhitelistRemoveAntiKick = q end })
MiscAura:AddToggle("UnstickAuraToggle", { Text = "Unstick Aura", Default = false, Callback = function(q)
    setUnstickAuraState(q) end })
MiscAuraActions:AddSlider("AuraActionRadiusSlider",
    { Text = "Action Radius", Default = auraActionRadius, Min = 6, Max = 120, Rounding = 0, Callback = function(q) auraActionRadius =
        math.clamp(tonumber(q) or 24, 6, 120) end })
MiscAuraActions:AddSlider("AuraActionCooldownSlider",
    { Text = "Action Cooldown", Default = math.floor(((tonumber(auraActionCooldown) or .9)) * 100), Min = 10, Max = 300, Rounding = 0, Callback = function(
        q) auraActionCooldown = math.clamp(((tonumber(q) or 90)) / 100, .1, 3) end })
MiscAuraActions:AddToggle("AuraGrabActionToggle",
    { Text = "Grab Aura", Default = false, Callback = function(q)
        auraGrabActionActive = q and true or false
        refreshAuraActionsLoop()
    end })
MiscAuraActions:AddToggle("AuraKillActionToggle",
    { Text = "Kill Aura", Default = false, Callback = function(q)
        auraKillActionActive = q and true or false
        refreshAuraActionsLoop()
    end })
MiscAuraActions:AddToggle("AuraBringActionToggle",
    { Text = "Bring Aura", Default = false, Callback = function(q)
        auraBringActionActive = q and true or false
        refreshAuraActionsLoop()
    end })
MiscAuraActions:AddToggle("AuraTpSpawnActionToggle",
    { Text = "Tp To Spawn Aura", Default = false, Callback = function(q)
        auraTpSpawnActionActive = q and true or false
        refreshAuraActionsLoop()
    end })
MiscAuraActions:AddToggle("AuraFlingActionToggle",
    { Text = "Fling Aura", Default = false, Callback = function(q)
        auraFlingActionActive = q and true or false
        refreshAuraActionsLoop()
    end })
MiscAllun = MiscLeft
MiscAllun:AddToggle("AllunSecondPersonToggle",
    { Text = "Second Person Camera", Default = false, Callback = function(q)
        local c = b
        if q then
            c.CameraMode = Enum.CameraMode.Classic
            c.CameraMinZoomDistance = .5
            c.CameraMaxZoomDistance = .5
        else
            c.CameraMode = Enum.CameraMode.Classic
            c.CameraMinZoomDistance = .5
            c.CameraMaxZoomDistance = 1000
        end
    end })
MiscAllun:AddToggle("AllunMiscLagToggle",
    { Text = "Lag", Default = false, Callback = function(q)
        if F6 then return end
        if M.PacketLagToggle and (M.PacketLagToggle.SetValue and M.PacketLagToggle.Value ~= q) then M.PacketLagToggle
                :SetValue(q) elseif not q then
            packetLagActive = false
            if I then
                task.cancel(I)
                I = nil
            end
        end
    end })
MiscAllun:AddSlider("AllunMiscLagIntensity",
    { Text = "Lag Intensity", Default = 1600, Min = 1, Max = 6250, Rounding = 0, Callback = function(q) if u.PacketLagStrength and u.PacketLagStrength.SetValue then
            u.PacketLagStrength:SetValue(q) else getPacketLagPayload(q) end end })
MiscAllun:AddToggle("AllunMiscNoclipGrab",
    { Text = "Noclip Grab", Default = false, Callback = function(q)
        _G.NoclipGrab = q and true or false
        if M.NoclipGrabToggle and (M.NoclipGrabToggle.SetValue and M.NoclipGrabToggle.Value ~= q) then M
                .NoclipGrabToggle:SetValue(q) end
    end })
CreditsLeft = w.Credits:AddLeftGroupbox("Credits")
CreditsLeft:AddLabel("Wourld Hub", true)
CreditsLeft:AddLabel("Creator: Wourld Team", true)
CreditsLeft:AddLabel("Visual Core: Wourld Build", true)
CreditsLeft:AddLabel("Telegram: @Wourld_soft", true)
CreditsLeft:AddLabel("")
CreditsLeft:AddLabel("UI Pack: Obsidian", true)
CreditsLeft:AddLabel("Edition: Cinematic FX", true)
MenuGroup = w.UISettings:AddLeftGroupbox("Menu")
MenuGroup:AddToggle("KeybindMenuOpen",
    { Default = c.KeybindFrame.Visible, Text = "Open Keybind Menu", Callback = function(q) c.KeybindFrame.Visible = q end })
MenuGroup:AddToggle("ShowCustomCursor",
    { Text = "Custom Cursor", Default = true, Callback = function(q)
        c.ShowCustomCursor = q
        applyMenuCursorState()
    end })
MenuGroup:AddToggle("CursorUnlockToggle",
    { Text = "Unlock Cursor", Default = gN.cursorUnlock, Callback = function(q)
        gN.cursorUnlock = q and true or false
        applyMenuCursorState()
    end })
MenuGroup:AddToggle("NotificationSoundToggle",
    { Text = "Notification Sound", Default = notifySoundEnabled, Callback = function(q)
        notifySoundEnabled = q and true or false
        syncNotifySoundSettings()
    end })
MenuGroup:AddToggle("FreeChatAnnouncementToggle",
    { Text = "Free Chat Message", Default = chatAnnouncementEnabled, Callback = function(q) chatAnnouncementEnabled = q and
        true or false end })
MenuGroup:AddSlider("NotificationVolume",
    { Text = "Notification Volume", Default = 50, Min = 0, Max = 100, Rounding = 0, Callback = function(q)
        notifySoundVolume = math.clamp(((tonumber(q) or 50)) / 100, 0, 1)
        syncNotifySoundSettings()
    end })
MenuGroup:AddSlider("WidgetSmoothness",
    { Text = "Widget Smoothness", Default = 55, Min = 0, Max = 100, Rounding = 0, Callback = function(q) menuWidgetSmoothness =
        math.clamp(tonumber(q) or 55, 0, 100) end })
MenuGroup:AddToggle("MenuBackgroundToggle",
    { Text = "Menu Background", Default = false, Callback = function(q)
        menuBackgroundEnabled = q and true or false
        applyMenuBackgroundState()
    end })
MenuGroup:AddInput("MenuBackgroundSourceInput",
    { Text = "Background Source", Default = "", Placeholder = "url / path / rbxassetid", Callback = function(q)
        menuBackgroundSource = tostring(q or "")
        applyMenuBackgroundState()
    end })
MenuGroup:AddSlider("MenuBackgroundTransparency",
    { Text = "Background Transparency", Default = math.floor(((tonumber(menuBackgroundTransparency) or .22)) * 100), Min = 0, Max = 100, Rounding = 0, Callback = function(
        q)
        menuBackgroundTransparency = math.clamp(((tonumber(q) or 22)) / 100, 0, 1)
        applyMenuBackgroundState()
    end })
MenuGroup:AddToggle("PhoneModeToggle", { Text = "Phone Mode UI", Default = false, Callback = function(q)
    setPhoneModeState(q) end })
MenuGroup:AddDropdown("NotificationSide",
    { Values = { "Left", "Right" }, Default = "Right", Text = "Notification Side", Callback = function(q) c
            :SetNotifySide(q) end })
MenuGroup:AddDropdown("DPIDropdown",
    { Values = { "50%", "75%", "100%", "125%", "150%", "175%", "200%" }, Default = "100%", Text = "DPI Scale", Callback = function(
        q)
        q = q:gsub("%%", "")
        local r = tonumber(q)
        c:SetDPIScale(r)
    end })
MenuGroup:AddDivider(); (MenuGroup:AddLabel("Menu Keybind")):AddKeyPicker("MenuKeybind",
    { Default = "RightShift", NoUI = true, Text = "Menu Keybind" })
MenuGroup:AddButton({ Text = "Unload", Func = function() c:Unload() end, DoubleClick = false })
if false and IsLocalAdminUser() then
    AdminGroup = w.UISettings:AddRightGroupbox("Admin Panel")
    AdminGroup:AddLabel("Access: " .. tostring(b.Name), true)
    AdminGroup:AddLabel("Wourld Internal Controls", true)
    AdminGroup:AddButton({ Text = "Force Notifications ON", Func = function()
        ForceAllNotifySettings()
        c:Notify({ Title = "Wourld Hub", Description = "All notifications forced ON", Duration = 3 })
    end, DoubleClick = false })
    AdminGroup:AddButton({ Text = "Kick All Fast (No Blob)", Func = function() KickAllPlayersNoBlobFast() end, DoubleClick = false })
    AdminGroup:AddButton({ Text = "Kick All Queue (Blob)", Func = function() KickAllPlayersBlobSafe() end, DoubleClick = false })
    AdminGroup:AddButton({ Text = "Retry Selected x3", Func = function()
        local q = u.TargetPlayer and u.TargetPlayer.Value
        if not q or q == "" then return end
        if isSelfTargetName(q) then
            c:Notify({ Title = "Wourld Hub", Description = "Self target blocked", Duration = 2 })
            return
        end
        task.spawn(function() for c = 1, 3, 1 do
                local r = select(1, TryKickTargetNoBlob(q, 1))
                if r then break end
                task.wait(.2)
            end end)
    end, DoubleClick = false })
    AdminGroup:AddButton({ Text = "Force Safe Respawn", Func = function() requestAutoRespawn("admin recover", 0) end, DoubleClick = false })
    AdminGroup:AddButton({ Text = "Refresh Target List", Func = function()
        local q = updatePlayerList()
        if u.TargetPlayer and u.TargetPlayer.SetValues then u.TargetPlayer:SetValues(q) end
    end, DoubleClick = false })
    AdminGroup:AddButton({ Text = "Rejoin Current Server", Func = function() pcall(function() (game:GetService("TeleportService"))
                :TeleportToPlaceInstance(game.PlaceId, game.JobId, b) end) end, DoubleClick = false })
    AdminGroup:AddButton({ Text = "Emergency Stop", Func = function()
        AdminEmergencyStop()
        c:Notify({ Title = "Wourld Hub", Description = "Emergency stop done", Duration = 2 })
    end, DoubleClick = false })
end
c.ToggleKeybind = u.MenuKeybind
r:SetLibrary(c)
j:SetLibrary(c)
j:IgnoreThemeSettings()
j:SetIgnoreIndexes({ "MenuKeybind" })
r:SetFolder("Wourld_Hub")
local Ja = "Wourld_Hub/FlingThings"
do
    local function q(q)
        local c = tostring(q or "")
        if c == "" then return false end
        if isfolder then
            local q, r = pcall(function() return isfolder(c) end)
            if q and r then return true end
        end
        if listfiles then
            local q = pcall(function() return listfiles(c) end)
            if q then return true end
        end
        return false
    end
    local function c(c)
        local r = ((tostring(c or "")):gsub("\\", "/")):gsub("/+$", "")
        if (r:lower()):match("/game%-config$") then r = r:gsub("/game%-config$", "") end
        if r == "" then return "" end
        local j = {}
        local u = {}
        local function M(q)
            local c = ((tostring(q or "")):gsub("\\", "/")):gsub("/+$", "")
            local r = string.lower(c)
            if c ~= "" and not u[r] then
                u[r] = true
                j[#j + 1] = c
            end
        end
        M(r)
        M(r:gsub("^workspace/workspace/", "workspace/"))
        M(r:gsub("^workspace/workspace/", ""))
        M(r:gsub("^workspace/", ""))
        for c, r in ipairs(j) do if q(r) then return r end end
        return r
    end
    local r = { "wourld_hub/FlingThings", "Wourld_Hub/FlingThings", "wourld_hub/FlingThings/game-config",
        "Wourld_Hub/FlingThings/game-config", "workspace/wourld_hub/FlingThings", "workspace/Wourld_Hub/FlingThings",
        "workspace\\wourld_hub\\FlingThings", "workspace\\Wourld_Hub\\FlingThings",
        "workspace/wourld_hub/FlingThings/game-config", "workspace/Wourld_Hub/FlingThings/game-config",
        "workspace\\wourld_hub\\FlingThings\\game-config", "workspace\\Wourld_Hub\\FlingThings\\game-config" }
    for r, j in ipairs(r) do if q(j) then
            local q = c(j)
            if q ~= "" then
                Ja = q
                break
            end
        end end
end
j:SetFolder(Ja)
j:SetSubFolder("game-config")
r:ApplyToTab(w.UISettings)
j:BuildConfigSection(w.UISettings)
pcall(function() r:ApplyTheme("Dark") end)
c.MainColor = Color3.fromRGB(16, 16, 16)
c.BackgroundColor = Color3.fromRGB(10, 10, 10)
c.AccentColor = Color3.fromRGB(220, 220, 220)
c.OutlineColor = Color3.fromRGB(48, 48, 48)
c.FontColor = Color3.fromRGB(242, 242, 242)
c.AccentColorDark = (c.GetDarkerColor and c:GetDarkerColor(c.AccentColor)) or c.AccentColor:Lerp(Color3.new(0, 0, 0), .25)
c.Scheme.MainColor = c.MainColor
c.Scheme.BackgroundColor = c.BackgroundColor
c.Scheme.AccentColor = c.AccentColor
c.Scheme.OutlineColor = c.OutlineColor
c.Scheme.FontColor = c.FontColor
pcall(function() c:UpdateColorsUsingRegistry() end)
setupPremiumInterface()
applyBloomEffectState()
syncNotifySoundSettings()
startMenuCursorSync()
applyMenuCursorState()
setKickThreatMonitorState(true)
isRealMobileInput = O.TouchEnabled and (not O.MouseEnabled)
if isRealMobileInput then
    setPhoneModeState(true)
    if M.PhoneModeToggle and not M.PhoneModeToggle.Value then M.PhoneModeToggle:SetValue(true) end
end
if c.OnUnload then c:OnUnload(function()
        local q = (type(getgenv) == "function" and getgenv()) or _G
        local c = q and rawget(q, "__WourldHubRuntimeGuard")
        if type(c) == "table" and tostring(c.BuildId) == V then
            c.Active = false
            c.Ready = false
        end
        if uiFeedbackConnection then
            uiFeedbackConnection:Disconnect()
            uiFeedbackConnection = nil
        end
        if uiVisualConnection then
            uiVisualConnection:Disconnect()
            uiVisualConnection = nil
        end
        if uiClickSound then
            uiClickSound:Destroy()
            uiClickSound = nil
        end
        destroyMenuOpenIconButton()
        destroyMenuOpenTextButton()
        destroyPhoneMenuButton()
        destroyStatusHud()
        if menuCursorConnection then
            menuCursorConnection:Disconnect()
            menuCursorConnection = nil
        end
        if menuCursorWatchConnection then
            menuCursorWatchConnection:Disconnect()
            menuCursorWatchConnection = nil
        end
        if W6 then
            W6:Disconnect()
            W6 = nil
        end
        if R6 then
            R6:Disconnect()
            R6 = nil
        end
        if gN.kickThreatHeldAddedConnection then
            gN.kickThreatHeldAddedConnection:Disconnect()
            gN.kickThreatHeldAddedConnection = nil
        end
        if gN.kickThreatHeldRemovedConnection then
            gN.kickThreatHeldRemovedConnection:Disconnect()
            gN.kickThreatHeldRemovedConnection = nil
        end
        if X6 then
            X6:Disconnect()
            X6 = nil
        end
        if packetLagConnection then
            packetLagConnection:Disconnect()
            packetLagConnection = nil
        end
        if _G.WourldBeamSafeChildConnection then
            _G.WourldBeamSafeChildConnection:Disconnect()
            _G.WourldBeamSafeChildConnection = nil
        end
        if eventSoundTemplate and eventSoundTemplate.Parent then
            eventSoundTemplate:Destroy()
            eventSoundTemplate = nil
        end
        shutdownMusicPlayer()
        gN.musicEnabled = false
        O.MouseBehavior = Enum.MouseBehavior.Default
        O.MouseIconEnabled = true
        if phoneModeScale and phoneModeScale.Parent then
            phoneModeScale:Destroy()
            phoneModeScale = nil
        end
        uiVisualGradient = nil
        if allunVisuals then
            allunVisuals.disableAll()
            if allunVisuals.RuntimeConnection then
                allunVisuals.RuntimeConnection:Disconnect()
                allunVisuals.RuntimeConnection = nil
            end
            if allunVisuals.CharacterAddedConnection then
                allunVisuals.CharacterAddedConnection:Disconnect()
                allunVisuals.CharacterAddedConnection = nil
            end
        end
        bloomFXEnabled = false
        applyBloomEffectState()
        stopNeonAura()
        stopAccentPulse()
        fullBrightActive = false
        noFogActive = false
        timeLockActive = false
        saturationFXActive = false
        contrastFXActive = false
        sunRaysFXActive = false
        atmosphereOffActive = false
        hidePlayersActive = false
        hideAccessoriesActive = false
        rainbowBodyActive = false
        tN = false
        if DN then
            DN:Disconnect()
            DN = nil
        end
        if JN then
            task.cancel(JN)
            JN = nil
        end
        ResetPCLDScanState()
        RemoveAllBoxes()
        setUsernameEspState(false)
        setHitboxEspState(false)
        setBlizHighlightState(false)
        setBlizIconState(false)
        setAntiKickOwnerEspState(false)
        setSelfAntiKickVisualState(false)
        gN.dynamicEsp = false
        gN.threatHighlight = false
        gN.offscreenIndicator = false
        RefreshSmartVisualLoop()
        SetAuraShieldState(false)
        setAntiGrabState(false)
        SetSmartDefenceState(false)
        SetSmartTargetLoopState(false)
        SetTriggerBotState(false)
        SetOrbitState(false)
        SetAutoEdgeSaveState(false)
        SetAutoRejoinState(false)
        SetAutoPerformanceState(false)
        DisconnectTargetTrack()
        l6 = false
        if C6 then
            task.cancel(C6)
            C6 = nil
        end
        applyVisualEnhancements()
        AN = false
        mN = false
        pN = false
        QN = false
        WN = false
        kN = false
        stopSpeedLockLoop()
        stopJumpLockLoop()
        stopNoclip()
        stopInfiniteJump()
        stopAntiAfk()
        stopSpinbot()
        for q, c in ipairs({ "Held", "Burn", "Ragdoll", "Humanoid", "Char", "Jump", "Speed" }) do disconnectBlizCompat(c) end
        if VN.bodyVelocity then
            pcall(function() VN.bodyVelocity:Destroy() end)
            VN.bodyVelocity = nil
        end
        _G.AntiGrab = false
        _G.AntiExplosion = false
        _G.AntiBurn = false
        _G.AntiVoid = false
        _G.ShurikenAntiKick = false
        _G.AntiKick = false
        _G.SuperSpeed = false
        _G.InfiniteJump = false
        _G.KickAura = false
        _G.KickAuraType = "Silent"
        _G.NoclipToggle = false
        _G.NoclipGrab = false; (getgenv()).Multiplier = .15
        Y.FallenPartsDestroyHeight = -100
        v6 = false
        if b6 then
            task.cancel(b6)
            b6 = nil
        end
        resetAntiPacketRuntime()
        setCharacterBeamScriptDisabled(false)
        antiGucciActive = false
        if antiGucciTask then
            task.cancel(antiGucciTask)
            antiGucciTask = nil
        end
        gucciProtectActive = false
        gucciProtectSeenAny = false
        gucciProtectLastSeen = 0
        gucciProtectLastRespawn = 0
        if gucciProtectTask then
            task.cancel(gucciProtectTask)
            gucciProtectTask = nil
        end
        releaseGucciGrabState(nil, true)
        SN = false
        if NN then
            task.cancel(NN)
            NN = nil
        end
        angelAuraEnabled = false
        clearAngelAura()
        setAutoRespawnState(false)
        applyLowGraphicsState(false)
        applyGravityState()
        githubHeadlessVisualActive = false
        githubKorbloxVisualActive = false
        githubKatanaHeadActive = false
        githubHatHeadActive = false
        githubVisualSpinActive = false
        if githubVisualCharacterConnection then
            githubVisualCharacterConnection:Disconnect()
            githubVisualCharacterConnection = nil
        end
        if githubVisualLoopConnection then
            githubVisualLoopConnection:Disconnect()
            githubVisualLoopConnection = nil
        end
        clearGitHubCosmetics()
        applyGitHubHeadlessVisual(b.Character)
        setToggleCursorState(false)
        setDynamicWaterState(false)
        setPalletVisualState(false)
        setUnstickAuraState(false)
        setBacktrackState(false)
        Xa = {}
        removeAntiKickAuraActive = false
        if gN.removeAntiKickAuraTask then
            task.cancel(gN.removeAntiKickAuraTask)
            gN.removeAntiKickAuraTask = nil
        end
        setAnyItemAntiKickState(false)
        G6 = G6 + 1
        if M6 then
            task.cancel(M6)
            M6 = nil
        end
        grabPoisonActive = false
        grabRadioactiveActive = false
        grabBurnActive = false
        grabKillActive = false
        grabFlingActive = false
        grabNoclipModActive = false
        grabCrazyActive = false
        grabSpinActive = false
        grabUltraActive = false
        ultraClickGrabActive = false
        lineInvisibleActive = false
        lineExtendActive = false
        lineCrazyPlayersActive = false
        lineCrazyAllPartsActive = false
        lineCrazyAllToysActive = false
        if grabLineModsTask then
            task.cancel(grabLineModsTask)
            grabLineModsTask = nil
        end
        auraGrabActionActive = false
        auraKillActionActive = false
        auraBringActionActive = false
        auraTpSpawnActionActive = false
        auraFlingActionActive = false
        if auraActionTask then
            task.cancel(auraActionTask)
            auraActionTask = nil
        end
        toyFreezeAuraActive = false
        toyTpAuraActive = false
        if toyAuraTask then
            task.cancel(toyAuraTask)
            toyAuraTask = nil
        end
        cleanupToyAuraFreeze()
        toyLoopSpawnActive = false
        if toyLoopSpawnTask then
            task.cancel(toyLoopSpawnTask)
            toyLoopSpawnTask = nil
        end
        setAntiBlobDenyState(false)
        setLoopExplosionState(false)
        setHomeGuardState(false)
        z6 = false
        setAntiInputLagBurgerState(false)
        setAntiInputLagTestState(false)
        setRemoveAllAntiInputState(false)
        clickTpEnabled = false
        stopOwnerWalkLoop()
        Ta = false
        if ea then
            task.cancel(ea)
            ea = nil
        end
        breakPcldActive = false
        if breakPcldTask then
            task.cancel(breakPcldTask)
            breakPcldTask = nil
        end
        GN = GN + 1
        if MN then
            task.cancel(MN)
            MN = nil
        end
    end) end
O.InputBegan:Connect(function(q, c)
    if q.UserInputType == Enum.UserInputType.MouseButton1 then
        C = true
        gN.mouse1DownAt = tick()
        n = tick() + .18
        gN.antiInputLagManualUntil = math.max(tonumber(gN.antiInputLagManualUntil) or 0, tick() + .35)
        bumpAntiInputLagRevision()
    end
    if not isGameplayKeyboardInput(q) then return end
    local r = getOptionKeyCode("GucciKeyPicker", gucciKey, Enum.KeyCode.J)
    local j = getOptionKeyCode("ClickTpKeyPicker", clickTpKey, Enum.KeyCode.C)
    local u = getOptionKeyCode("TeleportKey", tpKey, j)
    local M = getOptionKeyCode("RemoveLegsKeyPicker", removeLegsKey, Enum.KeyCode.Y)
    local G = getOptionKeyCode("RemoveArmsKeyPicker", removeArmsKey, Enum.KeyCode.U)
    local d = getOptionKeyCode("RemoveLimbsKeyPicker", removeLimbsKey, Enum.KeyCode.I)
    local z = getOptionKeyCode("BacktrackKeyPicker", backtrackKey, Enum.KeyCode.B)
    local Y = getOptionKeyCode("FreezeGrabKeyPicker", freezeGrabKey, Enum.KeyCode.G)
    local P = getOptionKeyCode("ItemShooterKeyPicker", O6, Enum.KeyCode.V)
    local O = false
    if tpEnabled and q.KeyCode == u then
        local q = b.Character
        local c = q and q:FindFirstChild("HumanoidRootPart")
        if c then
            n = tick() + 1
            local q = getTeleportAimPosition()
            if q then
                SmartTeleportToPosition(q)
                O = true
            end
        end
    end
    if clickTpEnabled and (q.KeyCode == j and not O) then
        local q = getTeleportAimPosition()
        if q then
            n = tick() + .8
            SmartTeleportToPosition(q)
        end
    end
    if q.KeyCode == M then tryRemoveGrabbedLimbs(true, false, "Remove Legs") end
    if q.KeyCode == G then tryRemoveGrabbedLimbs(false, true, "Remove Arms") end
    if q.KeyCode == d then tryRemoveGrabbedLimbs(true, true, "Remove Limbs") end
    if q.KeyCode == z then applyBacktrackNow() end
    if q.KeyCode == Y then freezeGrabbedObject() end
    if z6 and q.KeyCode == P then fireSelectedItemProjectile() end
    if q.KeyCode == r then
        n = tick() + 1
        if gN.gucciKeyActive then releaseGucciGrabState("Gucci key disabled", false) else GucciAntiGrab() end
    end
end)
O.InputEnded:Connect(function(q) if q.UserInputType == Enum.UserInputType.MouseButton1 then
        C = false
        gN.mouse1DownAt = 0
        gN.antiInputLagManualUntil = math.max(tonumber(gN.antiInputLagManualUntil) or 0, tick() + .12)
        bumpAntiInputLagRevision()
    end end)
function reapplyRuntimeTogglesAfterRespawn()
    setKickThreatMonitorState(true)
    restartTargetBoundActions()
    gN.gucciKeyActive = false
    gucciProtectSeenAny = false
    gucciProtectLastSeen = tick()
    if w6 then setAutoRespawnState(true) end
    if gN.autoRejoin then SetAutoRejoinState(true) end
    if qN then setAntiInputLagBurgerState(true) end
    if rN then setAntiInputLagTestState(true) end
    if antiAntiLagEnabled then setRemoveAllAntiInputState(true) end
    if SN then setKickAuraState(true) end
    if gN.orbit then SetOrbitState(true) end
    if antiGucciTask then
        task.cancel(antiGucciTask)
        antiGucciTask = nil
    end
    if antiGucciActive then antiGucciTask = task.spawn(StartAntiGucciLoop) end
    if gucciProtectTask then
        task.cancel(gucciProtectTask)
        gucciProtectTask = nil
    end
    if gucciProtectActive then gucciProtectTask = task.spawn(StartGucciProtectLoop) end
    if bN then
        task.cancel(bN)
        bN = nil
    end
    if destroyGucciActive then bN = task.spawn(StartDestroyGucciLoop) end
    if breakPcldActive and not breakPcldTask then breakPcldTask = task.spawn(function() while breakPcldActive do
                requestAutoRespawn("break pcld", 0)
                task.wait(1.4)
            end end) end
    if removeAntiKickAuraActive and not gN.removeAntiKickAuraTask then gN.removeAntiKickAuraTask = task.spawn(
        RemoveAntiKickAuraFunction) end
    if antiBlobDenyActive and not antiBlobDenyTask then antiBlobDenyTask = task.spawn(StartAntiBlobDenyLoop) end
    if gN.loopExplosionActive and not gN.loopExplosionTask then gN.loopExplosionTask = task.spawn(
        StartLoopExplosionMixLoop) end
    if gN.homeGuardActive and not gN.homeGuardTask then gN.homeGuardTask = task.spawn(StartHomeGuardLoop) end
    if q6 and not c6 then c6 = task.spawn(StartAnyItemAntiKickLoop) end
    if wN.antiKickOwnerEnabled and not wN.antiKickOwnerTask then setAntiKickOwnerEspState(true) end
    local q = M and M.ShurikenAntiKickToggle
    if _G.ShurikenAntiKick and (q and (q.SetValue and q.Value)) then task.spawn(function() pcall(function() q:SetValue(true) end) end) end
end

b.CharacterAdded:Connect(function(q)
    task.delay(.2,
        function()
            if b.Character ~= q then return end
            releaseGucciGrabState(nil, true)
            if m or (M.AntiGrabToggle and M.AntiGrabToggle.Value) then setAntiGrabState(true) end
            if AN then startSpeedLockLoop() end
            if Ga then refreshPalletVisuals() end
            if aa then setUnstickAuraState(true) end
            if va then setBacktrackState(true) end
            reapplyRuntimeTogglesAfterRespawn()
        end)
    task.delay(1.1,
        function()
            if b.Character ~= q then return end
            if antiGucciActive and not antiGucciTask then antiGucciTask = task.spawn(StartAntiGucciLoop) end
            if gucciProtectActive and not gucciProtectTask then gucciProtectTask = task.spawn(StartGucciProtectLoop) end
        end)
end)
G.PlayerAdded:Connect(function(q)
    task.wait(1)
    refreshTargetPlayerDropdown(true)
    if q and gN.friendJoinNotify then if isPlayerFriendFast(q) then
            c:Notify({ Title = "Wourld Hub", Description = "Friend in server: " .. q.Name, Duration = 4 })
            playEventSound("friend")
        end end
    if q and consumeKickedReturn(q) then
        c:Notify({ Title = "Wourld Hub", Description = q.Name .. " rejoined server", Duration = 4 })
        playEventSound("kick")
    end
    if q and (gN.targetTrackName and q.Name == gN.targetTrackName) then
        if gN.targetTrackPendingReturn then
            c:Notify({ Title = "Wourld Hub", Description = q.Name .. " returned", Duration = 3 })
            playEventSound("kick")
        end
        gN.targetTrackPendingReturn = false
        SetTrackedTarget(q.Name)
    end
end)
G.PlayerRemoving:Connect(function(q)
    task.wait(.5)
    refreshTargetPlayerDropdown(false)
    if q then
        local r = tick()
        local j = gN.kickAttempts[q.Name] or gN.kickAttempts[string.lower(q.Name)]
        if j and (r - j) <= 24 then
            local j = string.lower(q.Name)
            local u = gN.kickedPlayers[j] or 0
            local M = I6[j] or 0
            if (r - u) > 1.2 and (r - M) > 2.4 then
                I6[j] = r
                rememberKickedPlayer(q)
                local u = gN.kickAttemptMethods[j]
                c:Notify({ Title = "Wourld Hub", Description = ((q.DisplayName or q.Name)) ..
                (" (" .. (q.Name .. (") left after kick attempt" .. ((u and (" [" .. (tostring(u) .. "]")) or ""))))), Duration = 3 })
                playEventSound("kick")
            end
        end
    end
    if q and (gN.targetTrackName and q.Name == gN.targetTrackName) then gN.targetTrackPendingReturn = true end
end)
c:Notify({ Title = "Wourld Hub", Description = "Script loaded successfully", Duration = 5 })
j:LoadAutoloadConfig()
task.defer(function()
    pcall(function() refreshTargetPlayerDropdown(true) end)
    pcall(function() j:RefreshConfigDropdown() end)
    pcall(function() rebuildMusicPlaylist() end)
    pcall(function() sendFreeChatAnnouncement() end)
    task.delay(1.1,
        function()
            pcall(function() j:RefreshConfigDropdown() end)
            pcall(function() refreshTargetPlayerDropdown(false) end)
        end)
    task.delay(2.4, function() pcall(function() j:RefreshConfigDropdown() end) end)
    task.wait(.7)
    if u.TargetPlayer and u.TargetPlayer.Value then SetTrackedTarget(u.TargetPlayer.Value) end
    task.wait(.2)
    applyMenuCursorState()
    syncNotifySoundSettings()
    task.delay(.35,
        function()
            pcall(function()
                local q = M and M.KickNotifyToggle
                if q and (q.Value and ((not p) and q.SetValue)) then
                    q:SetValue(false)
                    task.wait(.05)
                    q:SetValue(true)
                end
            end)
            pcall(function()
                local q = M and M.PCLDToggle
                if q and (q.Value and q.SetValue) then
                    q:SetValue(false)
                    task.wait(.05)
                    q:SetValue(true)
                end
            end)
        end)
end)
do
    local q = (type(getgenv) == "function" and getgenv()) or _G
    local c = q and rawget(q, "__WourldHubRuntimeGuard")
    if type(c) == "table" and tostring(c.BuildId) == V then
        c.Ready = true
        c.Active = true
    end
end
print("Wourld Hub")
