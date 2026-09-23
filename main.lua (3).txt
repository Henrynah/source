--[[
     _      ___         ____  ______
    | | /| / (_)__  ___/ / / / /  _/
    | |/ |/ / / _ \/ _  / /_/ // /  
    |__/|__/_/_//_/\_,_/\____/___/
    
    v1.6.62  |  2025-11-25  |  Roblox UI Library for scripts
    
    To view the source code, see the `src/` folder on the official GitHub repository.
    
    Author: Footagesus (Footages, .ftgs, oftgs)
    Github: https://github.com/kiciahook/WindUI
    Discord: https://discord.gg/ftgs-development-hub-1300692552005189632
    License: MIT
]]

local __DARKLUA_BUNDLE_MODULES

__DARKLUA_BUNDLE_MODULES = {
    cache = {},
    load = function(m)
        if not __DARKLUA_BUNDLE_MODULES.cache[m] then
            __DARKLUA_BUNDLE_MODULES.cache[m] = {
                c = __DARKLUA_BUNDLE_MODULES[m](),
            }
        end

        return __DARKLUA_BUNDLE_MODULES.cache[m].c
    end,
}

do
    function __DARKLUA_BUNDLE_MODULES.a()
        local IconModule = {
            IconsType = 'lucide',
            New = nil,
            IconThemeTag = nil,
            Icons = {},
        }
        local ICON_URLS = {
            ['lucide'] = 
[[https://raw.githubusercontent.com/Footagesus/Icons/refs/heads/main/lucide/dist/Icons.lua]],
            ['craft'] = 
[[https://raw.githubusercontent.com/Footagesus/Icons/refs/heads/main/craft/dist/Icons.lua]],
            ['geist'] = 
[[https://raw.githubusercontent.com/Footagesus/Icons/refs/heads/main/geist/dist/Icons.lua]],
            ['sfsymbols'] = 
[[https://raw.githubusercontent.com/Footagesus/Icons/refs/heads/main/sfsymbols/dist/Icons.lua]],
        }
        local parseIconString = function(iconString)
            if type(iconString) == 'string' then
                local splitIndex = iconString:find(':')

                if splitIndex then
                    local iconType = iconString:sub(1, splitIndex - 1)
                    local iconName = iconString:sub(splitIndex + 1)

                    return iconType, iconName
                end
            end

            return nil, iconString
        end

        function IconModule.AddIcons(packName, iconsData)
            if type(packName) ~= 'string' or type(iconsData) ~= 'table' then
                error('AddIcons: packName must be string, iconsData must be table')

                return
            end
            if not IconModule.Icons[packName] then
                if ICON_URLS[packName] == nil then
                    IconModule.Icons[packName] = {
                        Icons = {},
                        Spritesheets = {},
                    }
                end
            end

            for iconName, iconValue in pairs(iconsData)do
                if type(iconValue) == 'number' or (type(iconValue) == 'string' and iconValue:match('^rbxassetid://')) then
                    local imageId = iconValue

                    if type(iconValue) == 'number' then
                        imageId = 'rbxassetid://' .. tostring(iconValue)
                    end

                    IconModule.Icons[packName].Icons[iconName] = {
                        Image = imageId,
                        ImageRectSize = Vector2.new(0, 0),
                        ImageRectPosition = Vector2.new(0, 0),
                        Parts = nil,
                    }
                    IconModule.Icons[packName].Spritesheets[imageId] = imageId
                elseif type(iconValue) == 'table' then
                    if iconValue.Image and iconValue.ImageRectSize and iconValue.ImageRectPosition then
                        local imageId = iconValue.Image

                        if type(imageId) == 'number' then
                            imageId = 'rbxassetid://' .. tostring(imageId)
                        end

                        IconModule.Icons[packName].Icons[iconName] = {
                            Image = imageId,
                            ImageRectSize = iconValue.ImageRectSize,
                            ImageRectPosition = iconValue.ImageRectPosition,
                            Parts = iconValue.Parts,
                        }

                        if not IconModule.Icons[packName].Spritesheets[imageId] then
                            IconModule.Icons[packName].Spritesheets[imageId] = imageId
                        end
                    else
                        warn("AddIcons: Invalid spritesheet data format for icon '" .. iconName .. "'")
                    end
                else
                    warn("AddIcons: Unsupported data type for icon '" .. iconName .. "': " .. type(iconValue))
                end
            end
        end
        function IconModule.SetIconsType(iconType)
            IconModule.IconsType = iconType
        end
        function IconModule.Init(New, IconThemeTag)
            IconModule.New = New
            IconModule.IconThemeTag = IconThemeTag

            return IconModule
        end
        function IconModule.Icon(Icon, Type, DefaultFormat)
            local iconType, iconName = parseIconString(Icon)
            local targetType = iconType or Type or IconModule.IconsType
            local targetName = iconName
            local iconSet = IconModule.Icons[targetType]

            if iconSet == nil and ICON_URLS[targetType] ~= nil then
                iconSet = loadstring(game:HttpGetAsync(ICON_URLS[targetType]))()
                IconModule.Icons[targetType] = iconSet
            end
            if iconSet and iconSet.Icons and iconSet.Icons[targetName] then
                return {
                    iconSet.Spritesheets[tostring(iconSet.Icons[targetName].Image)],
                    iconSet.Icons[targetName],
                }
            elseif iconSet and iconSet[targetName] and string.find(iconSet[targetName], 'rbxassetid://') then
                return {
                    iconSet[targetName],
                    {
                        ImageRectSize = Vector2.new(0, 0),
                        ImageRectPosition = Vector2.new(0, 0),
                    },
                }
            end

            return nil
        end
        function IconModule.Image(IconConfig)
            local Icon = {
                Icon = IconConfig.Icon or nil,
                Type = IconConfig.Type,
                Colors = IconConfig.Colors or {
                    (IconModule.IconThemeTag or Color3.new(1, 1, 1)),
                    Color3.new(1, 1, 1),
                },
                Size = IconConfig.Size or UDim2.new(0, 24, 0, 24),
                IconFrame = nil,
            }
            local Colors = {}

            for _, color in next, Icon.Colors do
                Colors[_] = {
                    ThemeTag = typeof(color) == 'string' and color,
                    Color = typeof(color) == 'Color3' and color,
                }
            end

            local IconLabel = IconModule.Icon(Icon.Icon, Icon.Type)
            local isrbxassetid = typeof(IconLabel) == 'string' and string.find(IconLabel, 'rbxassetid://')

            if IconModule.New then
                local New = IconModule.New
                local IconFrame = New('ImageLabel', {
                    Size = Icon.Size,
                    BackgroundTransparency = 1,
                    ImageColor3 = Colors[1].Color or nil,
                    ThemeTag = Colors[1].ThemeTag and {
                        ImageColor3 = Colors[1].ThemeTag,
                    },
                    Image = isrbxassetid and IconLabel or IconLabel[1],
                    ImageRectSize = isrbxassetid and nil or IconLabel[2].ImageRectSize,
                    ImageRectOffset = isrbxassetid and nil or IconLabel[2].ImageRectPosition,
                })

                if not isrbxassetid and IconLabel[2].Parts then
                    for _, part in next, IconLabel[2].Parts do
                        local IconPartLabel = IconModule.Icon(part, Icon.Type)

                        New('ImageLabel', {
                            Size = UDim2.new(1, 0, 1, 0),
                            BackgroundTransparency = 1,
                            ImageColor3 = Colors[1 + _].Color or nil,
                            ThemeTag = Colors[1 + _].ThemeTag and {
                                ImageColor3 = Colors[1 + _].ThemeTag,
                            },
                            Image = IconPartLabel[1],
                            ImageRectSize = IconPartLabel[2].ImageRectSize,
                            ImageRectOffset = IconPartLabel[2].ImageRectPosition,
                            Parent = IconFrame,
                        })
                    end
                end

                Icon.IconFrame = IconFrame
            else
                local IconFrame = Instance.new('ImageLabel')

                IconFrame.Size = Icon.Size
                IconFrame.BackgroundTransparency = 1
                IconFrame.ImageColor3 = Colors[1].Color
                IconFrame.Image = isrbxassetid and IconLabel or IconLabel[1]
                IconFrame.ImageRectSize = isrbxassetid and nil or IconLabel[2].ImageRectSize
                IconFrame.ImageRectOffset = isrbxassetid and nil or IconLabel[2].ImageRectPosition

                if not isrbxassetid and IconLabel[2].Parts then
                    for _, part in next, IconLabel[2].Parts do
                        local IconPartLabel = IconModule.Icon(part, Icon.Type)
                        local IconPart = Instance.New('ImageLabel')

                        IconPart.Size = UDim2.new(1, 0, 1, 0)
                        IconPart.BackgroundTransparency = 1
                        IconPart.ImageColor3 = Colors[1 + _].Color
                        IconPart.Image = IconPartLabel[1]
                        IconPart.ImageRectSize = IconPartLabel[2].ImageRectSize
                        IconPart.ImageRectOffset = IconPartLabel[2].ImageRectPosition
                        IconPart.Parent = IconFrame
                    end
                end

                Icon.IconFrame = IconFrame
            end

            return Icon
        end

        return IconModule
    end
    function __DARKLUA_BUNDLE_MODULES.b()
        local module = __DARKLUA_BUNDLE_MODULES.load('a')

        return module
    end
    function __DARKLUA_BUNDLE_MODULES.c()
        return {
            White = Color3.new(1, 1, 1),
            Black = Color3.new(0, 0, 0),
            Dialog = 'Accent',
            Background = 'Accent',
            BackgroundTransparency = 0,
            Hover = 'Text',
            WindowBackground = 'Background',
            WindowShadow = 'Black',
            WindowTopbarTitle = 'Text',
            WindowTopbarAuthor = 'Text',
            WindowTopbarIcon = 'Icon',
            WindowTopbarButtonIcon = 'Icon',
            TabBackground = 'Hover',
            TabTitle = 'Text',
            TabIcon = 'Icon',
            ElementBackground = 'Text',
            ElementTitle = 'Text',
            ElementDesc = 'Text',
            ElementIcon = 'Icon',
            PopupBackground = 'Background',
            PopupBackgroundTransparency = 'BackgroundTransparency',
            PopupTitle = 'Text',
            PopupContent = 'Text',
            PopupIcon = 'Icon',
            DialogBackground = 'Background',
            DialogBackgroundTransparency = 'BackgroundTransparency',
            DialogTitle = 'Text',
            DialogContent = 'Text',
            DialogIcon = 'Icon',
            Toggle = 'Button',
            ToggleBar = 'White',
            Checkbox = 'Button',
            CheckboxIcon = 'White',
        }
    end
    function __DARKLUA_BUNDLE_MODULES.d()
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)
        local RunService = cloneref(game:GetService('RunService'))
        local UserInputService = cloneref(game:GetService('UserInputService'))
        local TweenService = cloneref(game:GetService('TweenService'))
        local LocalizationService = cloneref(game:GetService('LocalizationService'))

        cloneref(game:GetService('HttpService'))

        local _ = RunService.Heartbeat
        local Icons = __DARKLUA_BUNDLE_MODULES.load('b')

        Icons.SetIconsType('lucide')

        local WindUI
        local Creator = {
            Font = 'rbxassetid://12187365364',
            Localization = nil,
            CanDraggable = true,
            Theme = nil,
            Themes = nil,
            Icons = Icons,
            Signals = {},
            Objects = {},
            LocalizationObjects = {},
            FontObjects = {},
            Language = string.match(LocalizationService.SystemLocaleId, '^[a-z]+'),
            Request = http_request or (syn and syn.request) or request,
            DefaultProperties = {
                ScreenGui = {
                    ResetOnSpawn = false,
                    ZIndexBehavior = 'Sibling',
                },
                CanvasGroup = {
                    BorderSizePixel = 0,
                    BackgroundColor3 = Color3.new(1, 1, 1),
                },
                Frame = {
                    BorderSizePixel = 0,
                    BackgroundColor3 = Color3.new(1, 1, 1),
                },
                TextLabel = {
                    BackgroundColor3 = Color3.new(1, 1, 1),
                    BorderSizePixel = 0,
                    Text = '',
                    RichText = true,
                    TextColor3 = Color3.new(1, 1, 1),
                    TextSize = 14,
                },
                TextButton = {
                    BackgroundColor3 = Color3.new(1, 1, 1),
                    BorderSizePixel = 0,
                    Text = '',
                    AutoButtonColor = false,
                    TextColor3 = Color3.new(1, 1, 1),
                    TextSize = 14,
                },
                TextBox = {
                    BackgroundColor3 = Color3.new(1, 1, 1),
                    BorderColor3 = Color3.new(0, 0, 0),
                    ClearTextOnFocus = false,
                    Text = '',
                    TextColor3 = Color3.new(0, 0, 0),
                    TextSize = 14,
                },
                ImageLabel = {
                    BackgroundTransparency = 1,
                    BackgroundColor3 = Color3.new(1, 1, 1),
                    BorderSizePixel = 0,
                },
                ImageButton = {
                    BackgroundColor3 = Color3.new(1, 1, 1),
                    BorderSizePixel = 0,
                    AutoButtonColor = false,
                },
                UIListLayout = {
                    SortOrder = 'LayoutOrder',
                },
                ScrollingFrame = {
                    ScrollBarImageTransparency = 1,
                    BorderSizePixel = 0,
                },
                VideoFrame = {BorderSizePixel = 0},
            },
            Colors = {
                Red = '#e53935',
                Orange = '#f57c00',
                Green = '#43a047',
                Blue = '#039be5',
                White = '#ffffff',
                Grey = '#484848',
            },
            ThemeFallbacks = __DARKLUA_BUNDLE_MODULES.load('c'),
            Shapes = {
                Square = 'rbxassetid://82909646051652',
                ['Square-Outline'] = 'rbxassetid://72946211851948',
                Squircle = 'rbxassetid://80999662900595',
                SquircleOutline = 'rbxassetid://117788349049947',
                ['Squircle-Outline'] = 'rbxassetid://117817408534198',
                SquircleOutline2 = 'rbxassetid://117817408534198',
                ['Shadow-sm'] = 'rbxassetid://84825982946844',
                ['Squircle-TL-TR'] = 'rbxassetid://73569156276236',
                ['Squircle-BL-BR'] = 'rbxassetid://93853842912264',
                ['Squircle-TL-TR-Outline'] = 'rbxassetid://136702870075563',
                ['Squircle-BL-BR-Outline'] = 'rbxassetid://75035847706564',
            },
        }

        function Creator.Init(WindUITable)
            WindUI = WindUITable
        end
        function Creator.AddSignal(Signal, Function)
            local conn = Signal:Connect(Function)

            table.insert(Creator.Signals, conn)

            return conn
        end
        function Creator.DisconnectAll()
            for idx, signal in next, Creator.Signals do
                local Connection = table.remove(Creator.Signals, idx)

                Connection:Disconnect()
            end
        end
        function Creator.SafeCallback(Function, ...)
            if not Function then
                return
            end

            local Success, Event = pcall(Function, ...)

            if not Success then
                if WindUI and WindUI.Window and WindUI.Window.Debug then
                    local _, i = Event:find(':%d+: ')

                    warn('[ WindUI: DEBUG Mode ] ' .. Event)

                    return WindUI:Notify({
                        Title = 'DEBUG Mode: Error',
                        Content = not i and Event or Event:sub(i + 1),
                        Duration = 8,
                    })
                end
            end
        end
        function Creator.Gradient(stops, props)
            if WindUI and WindUI.Gradient then
                return WindUI:Gradient(stops, props)
            end

            local colorSequence = {}
            local transparencySequence = {}

            for posStr, stop in next, stops do
                local position = tonumber(posStr)

                if position then
                    position = math.clamp(position / 100, 0, 1)

                    table.insert(colorSequence, ColorSequenceKeypoint.new(position, stop.Color))
                    table.insert(transparencySequence, NumberSequenceKeypoint.new(position, stop.Transparency or 0))
                end
            end

            table.sort(colorSequence, function(a, b)
                return a.Time < b.Time
            end)
            table.sort(transparencySequence, function(a, b)
                return a.Time < b.Time
            end)

            if #colorSequence < 2 then
                error('ColorSequence requires at least 2 keypoints')
            end

            local gradientData = {
                Color = ColorSequence.new(colorSequence),
                Transparency = NumberSequence.new(transparencySequence),
            }

            if props then
                for k, v in pairs(props)do
                    gradientData[k] = v
                end
            end

            return gradientData
        end
        function Creator.SetTheme(Theme)
            Creator.Theme = Theme

            Creator.UpdateTheme(nil, false)
        end
        function Creator.AddFontObject(Object)
            table.insert(Creator.FontObjects, Object)
            Creator.UpdateFont(Creator.Font)
        end
        function Creator.UpdateFont(FontId)
            Creator.Font = FontId

            for _, Obj in next, Creator.FontObjects do
                Obj.FontFace = Font.new(FontId, Obj.FontFace.Weight, Obj.FontFace.Style)
            end
        end
        function Creator.GetThemeProperty(Property, Theme)
            local getValue = function(prop, themeTable)
                local value = themeTable[prop]

                if value == nil then
                    return nil
                end
                if typeof(value) == 'string' and string.sub(value, 1, 1) == '#' then
                    return Color3.fromHex(value)
                end
                if typeof(value) == 'Color3' then
                    return value
                end
                if typeof(value) == 'number' then
                    return value
                end
                if typeof(value) == 'table' and value.Color and value.Transparency then
                    return value
                end
                if typeof(value) == 'function' then
                    return value()
                end

                return value
            end
            local value = getValue(Property, Theme)

            if value ~= nil then
                if typeof(value) == 'string' and string.sub(value, 1, 1) ~= '#' then
                    local referencedValue = Creator.GetThemeProperty(value, Theme)

                    if referencedValue ~= nil then
                        return referencedValue
                    end
                else
                    return value
                end
            end

            local fallbackProperty = Creator.ThemeFallbacks[Property]

            if fallbackProperty ~= nil then
                if typeof(fallbackProperty) == 'string' and string.sub(fallbackProperty, 1, 1) ~= '#' then
                    return Creator.GetThemeProperty(fallbackProperty, Theme)
                else
                    return getValue(Property, {[Property] = fallbackProperty})
                end
            end

            value = getValue(Property, Creator.Themes['Dark'])

            if value ~= nil then
                if typeof(value) == 'string' and string.sub(value, 1, 1) ~= '#' then
                    local referencedValue = Creator.GetThemeProperty(value, Creator.Themes['Dark'])

                    if referencedValue ~= nil then
                        return referencedValue
                    end
                else
                    return value
                end
            end
            if fallbackProperty ~= nil then
                if typeof(fallbackProperty) == 'string' and string.sub(fallbackProperty, 1, 1) ~= '#' then
                    return Creator.GetThemeProperty(fallbackProperty, Creator.Themes['Dark'])
                else
                    return getValue(Property, {[Property] = fallbackProperty})
                end
            end

            return nil
        end
        function Creator.AddThemeObject(Object, Properties)
            Creator.Objects[Object] = {
                Object = Object,
                Properties = Properties,
            }

            Creator.UpdateTheme(Object, false)

            return Object
        end
        function Creator.AddLangObject(idx)
            local currentObj = Creator.LocalizationObjects[idx]
            local Object = currentObj.Object
            local TranslationId = currentObjTranslationId

            Creator.UpdateLang(Object, TranslationId)

            return Object
        end
        function Creator.UpdateTheme(TargetObject, isTween)
            local ApplyTheme = function(objData)
                for Property, ColorKey in pairs(objData.Properties or {})do
                    local value = Creator.GetThemeProperty(ColorKey, Creator.Theme)

                    if value ~= nil then
                        if typeof(value) == 'Color3' then
                            local gradient = objData.Object:FindFirstChild('WindUIGradient')

                            if gradient then
                                gradient:Destroy()
                            end
                            if not isTween then
                                objData.Object[Property] = value
                            else
                                Creator.Tween(objData.Object, 0.08, {[Property] = value}):Play()
                            end
                        elseif typeof(value) == 'table' and value.Color and value.Transparency then
                            objData.Object[Property] = Color3.new(1, 1, 1)

                            local gradient = objData.Object:FindFirstChild('WindUIGradient')

                            if not gradient then
                                gradient = Instance.new('UIGradient')
                                gradient.Name = 'WindUIGradient'
                                gradient.Parent = objData.Object
                            end

                            gradient.Color = value.Color
                            gradient.Transparency = value.Transparency

                            for prop, propValue in pairs(value)do
                                if prop ~= 'Color' and prop ~= 'Transparency' and gradient[prop] ~= nil then
                                    gradient[prop] = propValue
                                end
                            end
                        elseif typeof(value) == 'number' then
                            if not isTween then
                                objData.Object[Property] = value
                            else
                                Creator.Tween(objData.Object, 0.08, {[Property] = value}):Play()
                            end
                        end
                    else
                        local gradient = objData.Object:FindFirstChild('WindUIGradient')

                        if gradient then
                            gradient:Destroy()
                        end
                    end
                end
            end

            if TargetObject then
                local objData = Creator.Objects[TargetObject]

                if objData then
                    ApplyTheme(objData)
                end
            else
                for _, objData in pairs(Creator.Objects)do
                    ApplyTheme(objData)
                end
            end
        end
        function Creator.SetLangForObject(index)
            if Creator.Localization and Creator.Localization.Enabled then
                local data = Creator.LocalizationObjects[index]

                if not data then
                    return
                end

                local obj = data.Object
                local translationId = data.TranslationId
                local translations = Creator.Localization.Translations[Creator.Language]

                if translations and translations[translationId] then
                    obj.Text = translations[translationId]
                else
                    local enTranslations = Creator.Localization and Creator.Localization.Translations and Creator.Localization.Translations.en or nil

                    if enTranslations and enTranslations[translationId] then
                        obj.Text = enTranslations[translationId]
                    else
                        obj.Text = '[' .. translationId .. ']'
                    end
                end
            end
        end
        function Creator:ChangeTranslationKey(object, newKey)
            if Creator.Localization and Creator.Localization.Enabled then
                local ParsedKey = string.match(newKey, '^' .. Creator.Localization.Prefix .. '(.+)')

                if ParsedKey then
                    for i, data in ipairs(Creator.LocalizationObjects)do
                        if data.Object == object then
                            data.TranslationId = ParsedKey

                            Creator.SetLangForObject(i)

                            return
                        end
                    end

                    table.insert(Creator.LocalizationObjects, {
                        TranslationId = ParsedKey,
                        Object = object,
                    })
                    Creator.SetLangForObject(#Creator.LocalizationObjects)
                end
            end
        end
        function Creator.UpdateLang(newLang)
            if newLang then
                Creator.Language = newLang
            end

            for i = 1, #Creator.LocalizationObjects do
                local data = Creator.LocalizationObjects[i]

                if data.Object and data.Object.Parent ~= nil then
                    Creator.SetLangForObject(i)
                else
                    Creator.LocalizationObjects[i] = nil
                end
            end
        end
        function Creator.SetLanguage(lang)
            Creator.Language = lang

            Creator.UpdateLang()
        end
        function Creator.Icon(Icon, formatdefault)
            return Icons.Icon(Icon, nil, formatdefault ~= false)
        end
        function Creator.AddIcons(packName, iconsData)
            return Icons.AddIcons(packName, iconsData)
        end
        function Creator.New(Name, Properties, Children)
            local Object = Instance.new(Name)

            for Name, Value in next, Creator.DefaultProperties[Name] or {}do
                Object[Name] = Value
            end
            for Name, Value in next, Properties or {}do
                if Name ~= 'ThemeTag' then
                    Object[Name] = Value
                end
                if Creator.Localization and Creator.Localization.Enabled and Name == 'Text' then
                    local TranslationId = string.match(Value, '^' .. Creator.Localization.Prefix .. '(.+)')

                    if TranslationId then
                        local currentId = #Creator.LocalizationObjects + 1

                        Creator.LocalizationObjects[currentId] = {
                            TranslationId = TranslationId,
                            Object = Object,
                        }

                        Creator.SetLangForObject(currentId)
                    end
                end
            end
            for _, Child in next, Children or {}do
                Child.Parent = Object
            end

            if Properties and Properties.ThemeTag then
                Creator.AddThemeObject(Object, Properties.ThemeTag)
            end
            if Properties and Properties.FontFace then
                Creator.AddFontObject(Object)
            end

            return Object
        end
        function Creator.Tween(Object, Time, Properties, ...)
            return TweenService:Create(Object, TweenInfo.new(Time, ...), Properties)
        end
        function Creator.NewRoundFrame(
            Radius,
            Type,
            Properties,
            Children,
            isButton,
            ReturnTable
        )
            local getImageForType = function(shapeType)
                return Creator.Shapes[shapeType]
            end
            local getSliceCenterForType = function(shapeType)
                return shapeType ~= 'Shadow-sm' and Rect.new(256, 256, 256, 256) or Rect.new(512, 512, 512, 512)
            end
            local Image = Creator.New(isButton and 'ImageButton' or 'ImageLabel', {
                Image = getImageForType(Type),
                ScaleType = 'Slice',
                SliceCenter = getSliceCenterForType(Type),
                SliceScale = 1,
                BackgroundTransparency = 1,
                ThemeTag = Properties.ThemeTag and Properties.ThemeTag,
            }, Children)

            for k, v in pairs(Properties or {})do
                if k ~= 'ThemeTag' then
                    Image[k] = v
                end
            end

            local UpdateSliceScale = function(newRadius)
                local sliceScale = Type ~= 'Shadow-sm' and (newRadius / (256)) or (newRadius / 512)

                Image.SliceScale = math.max(sliceScale, 0.0001)
            end
            local Wrapper = {}

            function Wrapper:SetRadius(newRadius)
                UpdateSliceScale(newRadius)
            end
            function Wrapper:SetType(newType)
                Type = newType
                Image.Image = getImageForType(newType)
                Image.SliceCenter = getSliceCenterForType(newType)

                UpdateSliceScale(Radius)
            end
            function Wrapper:UpdateShape(newRadius, newType)
                if newType then
                    Type = newType
                    Image.Image = getImageForType(newType)
                    Image.SliceCenter = getSliceCenterForType(newType)
                end
                if newRadius then
                    Radius = newRadius
                end

                UpdateSliceScale(Radius)
            end
            function Wrapper:GetRadius()
                return Radius
            end
            function Wrapper:GetType()
                return Type
            end

            UpdateSliceScale(Radius)

            return Image, ReturnTable and Wrapper or nil
        end

        local New = Creator.New
        local _ = Creator.Tween

        function Creator.SetDraggable(can)
            Creator.CanDraggable = can
        end
        function Creator.Drag(mainFrame, dragFrames, ondrag)
            local currentDragFrame = nil
            local dragging, dragStart, startPos
            local DragModule = {CanDraggable = true}

            if not dragFrames or typeof(dragFrames) ~= 'table' then
                dragFrames = {mainFrame}
            end

            local update = function(input)
                if not dragging or not DragModule.CanDraggable then
                    return
                end

                local delta = input.Position - dragStart

                Creator.Tween(mainFrame, 0.02, {
                    Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y),
                }):Play()
            end

            for _, dragFrame in pairs(dragFrames)do
                dragFrame.InputBegan:Connect(function(input)
                    if (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) and DragModule.CanDraggable then
                        if currentDragFrame == nil then
                            currentDragFrame = dragFrame
                            dragging = true
                            dragStart = input.Position
                            startPos = mainFrame.Position

                            if ondrag and typeof(ondrag) == 'function' then
                                ondrag(true, currentDragFrame)
                            end

                            input.Changed:Connect(function()
                                if input.UserInputState == Enum.UserInputState.End then
                                    dragging = false
                                    currentDragFrame = nil

                                    if ondrag and typeof(ondrag) == 'function' then
                                        ondrag(false, nil)
                                    end
                                end
                            end)
                        end
                    end
                end)
                dragFrame.InputChanged:Connect(function(input)
                    if dragging and currentDragFrame == dragFrame then
                        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
                            update(input)
                        end
                    end
                end)
            end

            UserInputService.InputChanged:Connect(function(input)
                if dragging and currentDragFrame ~= nil then
                    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
                        update(input)
                    end
                end
            end)

            function DragModule:Set(v)
                DragModule.CanDraggable = v
            end

            return DragModule
        end

        Icons.Init(New, 'Icon')

        function Creator.SanitizeFilename(url)
            local filename = url:match('([^/]+)$') or url

            filename = filename:gsub('%.[^%.]+$', '')
            filename = filename:gsub('[^%w%-_]', '_')

            if #filename > 50 then
                filename = filename:sub(1, 50)
            end

            return filename
        end
        function Creator.Image(
            Img,
            Name,
            Corner,
            Folder,
            Type,
            IsThemeTag,
            Themed,
            ThemeTagName
        )
            Folder = Folder or 'Temp'
            Name = Creator.SanitizeFilename(Name)

            local ImageFrame = New('Frame', {
                Size = UDim2.new(0, 0, 0, 0),
                BackgroundTransparency = 1,
            }, {
                New('ImageLabel', {
                    Size = UDim2.new(1, 0, 1, 0),
                    BackgroundTransparency = 1,
                    ScaleType = 'Crop',
                    ThemeTag = (Creator.Icon(Img) or Themed) and {
                        ImageColor3 = IsThemeTag and (ThemeTagName or 'Icon') or nil,
                    } or nil,
                }, {
                    New('UICorner', {
                        CornerRadius = UDim.new(0, Corner),
                    }),
                }),
            })

            if Creator.Icon(Img) then
                ImageFrame.ImageLabel:Destroy()

                local IconLabel = Icons.Image({
                    Icon = Img,
                    Size = UDim2.new(1, 0, 1, 0),
                    Colors = {
                        (IsThemeTag and (ThemeTagName or 'Icon') or false),
                        'Button',
                    },
                }).IconFrame

                IconLabel.Parent = ImageFrame
            elseif string.find(Img, 'http') then
                local FileName = 'WindUI/' .. Folder .. '/assets/.' .. Type .. '-' .. Name .. '.png'
                local success, response = pcall(function()
                    task.spawn(function()
                        local response = Creator.Request({
                            Url = Img,
                            Method = 'GET',
                        }).Body

                        writefile(FileName, response)

                        local assetSuccess, asset = pcall(getcustomasset, FileName)

                        if assetSuccess then
                            ImageFrame.ImageLabel.Image = asset
                        else
                            warn(string.format("[ WindUI.Creator ] Failed to load custom asset '%s': %s", FileName, tostring(asset)))
                            ImageFrame:Destroy()

                            return
                        end
                    end)
                end)

                if not success then
                    warn("[ WindUI.Creator ]  '" .. identifyexecutor() .. "' doesnt support the URL Images. Error: " .. response)
                    ImageFrame:Destroy()
                end
            elseif Img == '' then
                ImageFrame.Visible = false
            else
                ImageFrame.ImageLabel.Image = Img
            end

            return ImageFrame
        end

        return Creator
    end
    function __DARKLUA_BUNDLE_MODULES.e()
        local Localization = {}

        function Localization:New(LocalizationConfig, Creator)
            local LocalizationModule = {
                Enabled = LocalizationConfig.Enabled or false,
                Translations = LocalizationConfig.Translations or {},
                Prefix = LocalizationConfig.Prefix or 'loc:',
                DefaultLanguage = LocalizationConfig.DefaultLanguage or 'en',
            }

            Creator.Localization = LocalizationModule

            return LocalizationModule
        end

        return Localization
    end
    function __DARKLUA_BUNDLE_MODULES.f()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween
        local NotificationModule = {
            Size = UDim2.new(0, 300, 1, -156),
            SizeLower = UDim2.new(0, 300, 1, -56),
            UICorner = 13,
            UIPadding = 14,
            Holder = nil,
            NotificationIndex = 0,
            Notifications = {},
        }

        function NotificationModule.Init(Parent)
            local NotModule = {Lower = false}

            function NotModule.SetLower(val)
                NotModule.Lower = val
                NotModule.Frame.Size = val and NotificationModule.SizeLower or NotificationModule.Size
            end

            NotModule.Frame = New('Frame', {
                Position = UDim2.new(1, -29, 0, 56),
                AnchorPoint = Vector2.new(1, 0),
                Size = NotificationModule.Size,
                Parent = Parent,
                BackgroundTransparency = 1,
            }, {
                New('UIListLayout', {
                    HorizontalAlignment = 'Center',
                    SortOrder = 'LayoutOrder',
                    VerticalAlignment = 'Bottom',
                    Padding = UDim.new(0, 8),
                }),
                New('UIPadding', {
                    PaddingBottom = UDim.new(0, 29),
                }),
            })

            return NotModule
        end
        function NotificationModule.New(Config)
            local Notification = {
                Title = Config.Title or 'Notification',
                Content = Config.Content or nil,
                Icon = Config.Icon or nil,
                IconThemed = Config.IconThemed,
                Background = Config.Background,
                BackgroundImageTransparency = Config.BackgroundImageTransparency,
                Duration = Config.Duration or 5,
                Buttons = Config.Buttons or {},
                CanClose = true,
                UIElements = {},
                Closed = false,
            }

            if Notification.CanClose == nil then
                Notification.CanClose = true
            end

            NotificationModule.NotificationIndex = NotificationModule.NotificationIndex + 1
            NotificationModule.Notifications[NotificationModule.NotificationIndex] = Notification

            local Icon

            if Notification.Icon then
                Icon = Creator.Image(Notification.Icon, Notification.Title .. ':' .. Notification.Icon, 0, Config.Window, 'Notification', Notification.IconThemed)
                Icon.Size = UDim2.new(0, 26, 0, 26)
                Icon.Position = UDim2.new(0, NotificationModule.UIPadding, 0, NotificationModule.UIPadding)
            end

            local CloseButton

            if Notification.CanClose then
                CloseButton = New('ImageButton', {
                    Image = Creator.Icon('x')[1],
                    ImageRectSize = Creator.Icon('x')[2].ImageRectSize,
                    ImageRectOffset = Creator.Icon('x')[2].ImageRectPosition,
                    BackgroundTransparency = 1,
                    Size = UDim2.new(0, 16, 0, 16),
                    Position = UDim2.new(1, -NotificationModule.UIPadding, 0, NotificationModule.UIPadding),
                    AnchorPoint = Vector2.new(1, 0),
                    ThemeTag = {
                        ImageColor3 = 'Text',
                    },
                    ImageTransparency = 0.4,
                }, {
                    New('TextButton', {
                        Size = UDim2.new(1, 8, 1, 8),
                        BackgroundTransparency = 1,
                        AnchorPoint = Vector2.new(0.5, 0.5),
                        Position = UDim2.new(0.5, 0, 0.5, 0),
                        Text = '',
                    }),
                })
            end

            local Duration = New('Frame', {
                Size = UDim2.new(0, 0, 1, 0),
                BackgroundTransparency = 0.95,
                ThemeTag = {
                    BackgroundColor3 = 'Text',
                },
            })
            local TextContainer = New('Frame', {
                Size = UDim2.new(1, Notification.Icon and -28 - NotificationModule.UIPadding or 0, 1, 0),
                Position = UDim2.new(1, 0, 0, 0),
                AnchorPoint = Vector2.new(1, 0),
                BackgroundTransparency = 1,
                AutomaticSize = 'Y',
            }, {
                New('UIPadding', {
                    PaddingTop = UDim.new(0, NotificationModule.UIPadding),
                    PaddingLeft = UDim.new(0, NotificationModule.UIPadding),
                    PaddingRight = UDim.new(0, NotificationModule.UIPadding),
                    PaddingBottom = UDim.new(0, NotificationModule.UIPadding),
                }),
                New('TextLabel', {
                    AutomaticSize = 'Y',
                    Size = UDim2.new(1, -30 - NotificationModule.UIPadding, 0, 0),
                    TextWrapped = true,
                    TextXAlignment = 'Left',
                    RichText = true,
                    BackgroundTransparency = 1,
                    TextSize = 16,
                    ThemeTag = {
                        TextColor3 = 'Text',
                    },
                    Text = Notification.Title,
                    FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                }),
                New('UIListLayout', {
                    Padding = UDim.new(0, NotificationModule.UIPadding / 3),
                }),
            })

            if Notification.Content then
                New('TextLabel', {
                    AutomaticSize = 'Y',
                    Size = UDim2.new(1, 0, 0, 0),
                    TextWrapped = true,
                    TextXAlignment = 'Left',
                    RichText = true,
                    BackgroundTransparency = 1,
                    TextTransparency = 0.4,
                    TextSize = 15,
                    ThemeTag = {
                        TextColor3 = 'Text',
                    },
                    Text = Notification.Content,
                    FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                    Parent = TextContainer,
                })
            end

            local Main = Creator.NewRoundFrame(NotificationModule.UICorner, 'Squircle', {
                Size = UDim2.new(1, 0, 0, 0),
                Position = UDim2.new(2, 0, 1, 0),
                AnchorPoint = Vector2.new(0, 1),
                AutomaticSize = 'Y',
                ImageTransparency = 0.05,
                ThemeTag = {
                    ImageColor3 = 'Background',
                },
            }, {
                New('CanvasGroup', {
                    Size = UDim2.new(1, 0, 1, 0),
                    BackgroundTransparency = 1,
                }, {
                    Duration,
                    New('UICorner', {
                        CornerRadius = UDim.new(0, NotificationModule.UICorner),
                    }),
                }),
                New('ImageLabel', {
                    Name = 'Background',
                    Image = Notification.Background,
                    BackgroundTransparency = 1,
                    Size = UDim2.new(1, 0, 1, 0),
                    ScaleType = 'Crop',
                    ImageTransparency = Notification.BackgroundImageTransparency,
                }, {
                    New('UICorner', {
                        CornerRadius = UDim.new(0, NotificationModule.UICorner),
                    }),
                }),
                TextContainer,
                Icon,
                CloseButton,
            })
            local MainContainer = New('Frame', {
                BackgroundTransparency = 1,
                Size = UDim2.new(1, 0, 0, 0),
                Parent = Config.Holder,
            }, {Main})

            function Notification:Close()
                if not Notification.Closed then
                    Notification.Closed = true

                    Tween(MainContainer, 0.45, {
                        Size = UDim2.new(1, 0, 0, -8),
                    }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                    Tween(Main, 0.55, {
                        Position = UDim2.new(2, 0, 1, 0),
                    }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                    task.wait(0.45)
                    MainContainer:Destroy()
                end
            end

            task.spawn(function()
                task.wait()
                Tween(MainContainer, 0.45, {
                    Size = UDim2.new(1, 0, 0, Main.AbsoluteSize.Y),
                }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                Tween(Main, 0.45, {
                    Position = UDim2.new(0, 0, 1, 0),
                }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()

                if Notification.Duration then
                    Tween(Duration, Notification.Duration, {
                        Size = UDim2.new(1, 0, 1, 0),
                    }, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut):Play()
                    task.wait(Notification.Duration)
                    Notification:Close()
                end
            end)

            if CloseButton then
                Creator.AddSignal(CloseButton.TextButton.MouseButton1Click, function(
                )
                    Notification:Close()
                end)
            end

            return Notification
        end

        return NotificationModule
    end
    function __DARKLUA_BUNDLE_MODULES.g()
        local a = 4294967296
        local b = a - 1
        local c = function(d, e)
            local f, g = 0, 1

            while d ~= 0 or e ~= 0 do
                local h, i = d % 2, e % 2
                local j = (h + i) % 2

                f = f + j * g
                d = math.floor(d / 2)
                e = math.floor(e / 2)
                g = g * 2
            end

            return f % a
        end

        local function k(d, e, l, ...)
            local m

            if e then
                d = d % a
                e = e % a
                m = c(d, e)

                if l then
                    m = k(m, l, ...)
                end

                return m
            elseif d then
                return d % a
            else
                return 0
            end
        end
        local function n(d, e, l, ...)
            local m

            if e then
                d = d % a
                e = e % a
                m = (d + e - c(d, e)) / 2

                if l then
                    m = n(m, l, ...)
                end

                return m
            elseif d then
                return d % a
            else
                return b
            end
        end

        local o = function(p)
            return b - p
        end
        local q = function(d, r)
            if r < 0 then
                return lshift(d, -r)
            end

            return math.floor(d % 4294967296 / 2 ^ r)
        end
        local s = function(p, r)
            if r > 31 or r < -31 then
                return 0
            end

            return q(p % a, r)
        end
        local lshift = function(d, r)
            if r < 0 then
                return s(d, -r)
            end

            return d * 2 ^ r % 4294967296
        end
        local t = function(p, r)
            p = p % a
            r = r % 32

            local u = n(p, 2 ^ r - 1)

            return s(p, r) + lshift(u, 32 - r)
        end
        local v = {
            0x428a2f98,
            0x71374491,
            0xb5c0fbcf,
            0xe9b5dba5,
            0x3956c25b,
            0x59f111f1,
            0x923f82a4,
            0xab1c5ed5,
            0xd807aa98,
            0x12835b01,
            0x243185be,
            0x550c7dc3,
            0x72be5d74,
            0x80deb1fe,
            0x9bdc06a7,
            0xc19bf174,
            0xe49b69c1,
            0xefbe4786,
            0xfc19dc6,
            0x240ca1cc,
            0x2de92c6f,
            0x4a7484aa,
            0x5cb0a9dc,
            0x76f988da,
            0x983e5152,
            0xa831c66d,
            0xb00327c8,
            0xbf597fc7,
            0xc6e00bf3,
            0xd5a79147,
            0x6ca6351,
            0x14292967,
            0x27b70a85,
            0x2e1b2138,
            0x4d2c6dfc,
            0x53380d13,
            0x650a7354,
            0x766a0abb,
            0x81c2c92e,
            0x92722c85,
            0xa2bfe8a1,
            0xa81a664b,
            0xc24b8b70,
            0xc76c51a3,
            0xd192e819,
            0xd6990624,
            0xf40e3585,
            0x106aa070,
            0x19a4c116,
            0x1e376c08,
            0x2748774c,
            0x34b0bcb5,
            0x391c0cb3,
            0x4ed8aa4a,
            0x5b9cca4f,
            0x682e6ff3,
            0x748f82ee,
            0x78a5636f,
            0x84c87814,
            0x8cc70208,
            0x90befffa,
            0xa4506ceb,
            0xbef9a3f7,
            0xc67178f2,
        }
        local w = function(x)
            return string.gsub(x, '.', function(l)
                return string.format('%02x', string.byte(l))
            end)
        end
        local y = function(z, A)
            local x = ''

            for B = 1, A do
                local C = z % 256

                x = string.char(C) .. x
                z = (z - C) / 256
            end

            return x
        end
        local D = function(x, B)
            local A = 0

            for B = B, B + 3 do
                A = A * 256 + string.byte(x, B)
            end

            return A
        end
        local E = function(F, G)
            local H = 64 - (G + 9) % 64

            G = y(8 * G, 8)
            F = F .. '\128' .. string.rep('\0', H) .. G

            assert(#F % 64 == 0)

            return F
        end
        local I = function(J)
            J[1] = 0x6a09e667
            J[2] = 0xbb67ae85
            J[3] = 0x3c6ef372
            J[4] = 0xa54ff53a
            J[5] = 0x510e527f
            J[6] = 0x9b05688c
            J[7] = 0x1f83d9ab
            J[8] = 0x5be0cd19

            return J
        end
        local K = function(F, B, J)
            local L = {}

            for M = 1, 16 do
                L[M] = D(F, B + (M - 1) * 4)
            end
            for M = 17, 64 do
                local N = L[M - 15]
                local O = k(t(N, 7), t(N, 18), s(N, 3))

                N = L[M - 2]
                L[M] = (L[M - 16] + O + L[M - 7] + k(t(N, 17), t(N, 19), s(N, 10))) % a
            end

            local d, e, l, P, Q, R, S, T = J[1], J[2], J[3], J[4], J[5], J[6], J[7], J[8]

            for B = 1, 64 do
                local O = k(t(d, 2), t(d, 13), t(d, 22))
                local U = k(n(d, e), n(d, l), n(e, l))
                local V = (O + U) % a
                local W = k(t(Q, 6), t(Q, 11), t(Q, 25))
                local X = k(n(Q, R), n(o(Q), S))
                local Y = (T + W + X + v[B] + L[B]) % a

                T = S
                S = R
                R = Q
                Q = (P + Y) % a
                P = l
                l = e
                e = d
                d = (Y + V) % a
            end

            J[1] = (J[1] + d) % a
            J[2] = (J[2] + e) % a
            J[3] = (J[3] + l) % a
            J[4] = (J[4] + P) % a
            J[5] = (J[5] + Q) % a
            J[6] = (J[6] + R) % a
            J[7] = (J[7] + S) % a
            J[8] = (J[8] + T) % a
        end
        local Z = function(F)
            F = E(F, #F)

            local J = I({})

            for B = 1, #F, 64 do
                K(F, B, J)
            end

            return w(y(J[1], 4) .. y(J[2], 4) .. y(J[3], 4) .. y(J[4], 4) .. y(J[5], 4) .. y(J[6], 4) .. y(J[7], 4) .. y(J[8], 4))
        end
        local e
        local l = {
            ['\\'] = '\\',
            ['"'] = '"',
            ['\b'] = 'b',
            ['\f'] = 'f',
            ['\n'] = 'n',
            ['\r'] = 'r',
            ['\t'] = 't',
        }
        local P = {
            ['/'] = '/',
        }

        for Q, R in pairs(l)do
            P[R] = Q
        end

        local S = function(T)
            return '\\' .. (l[T] or string.format('u%04x', T:byte()))
        end
        local B = function(M)
            return 'null'
        end
        local v = function(M, z)
            local _ = {}

            z = z or {}

            if z[M] then
                error('circular reference')
            end

            z[M] = true

            if rawget(M, 1) ~= nil or next(M) == nil then
                local A = 0

                for Q in pairs(M)do
                    if type(Q) ~= 'number' then
                        error('invalid table: mixed or invalid key types')
                    end

                    A = A + 1
                end

                if A ~= #M then
                    error('invalid table: sparse array')
                end

                for a0, R in ipairs(M)do
                    table.insert(_, e(R, z))
                end

                z[M] = nil

                return '[' .. table.concat(_, ',') .. ']'
            else
                for Q, R in pairs(M)do
                    if type(Q) ~= 'string' then
                        error('invalid table: mixed or invalid key types')
                    end

                    table.insert(_, e(Q, z) .. ':' .. e(R, z))
                end

                z[M] = nil

                return '{' .. table.concat(_, ',') .. '}'
            end
        end
        local g = function(M)
            return '"' .. M:gsub('[%z\1-\31\\"]', S) .. '"'
        end
        local a1 = function(M)
            if M ~= M or M <= -math.huge or M >= math.huge then
                error("unexpected number value '" .. tostring(M) .. "'")
            end

            return string.format('%.14g', M)
        end
        local j = {
            ['nil'] = B,
            ['table'] = v,
            ['string'] = g,
            ['number'] = a1,
            ['boolean'] = tostring,
        }

        e = function(M, z)
            local x = type(M)
            local a2 = j[x]

            if a2 then
                return a2(M, z)
            end

            error("unexpected type '" .. x .. "'")
        end

        local a3 = function(M)
            return e(M)
        end
        local a4
        local N = function(...)
            local _ = {}

            for a0 = 1, select('#', ...)do
                _[select(a0, ...)] = true
            end

            return _
        end
        local L = N(' ', '\t', '\r', '\n')
        local p = N(' ', '\t', '\r', '\n', ']', '}', ',')
        local a5 = N('\\', '/', '"', 'b', 'f', 'n', 'r', 't', 'u')
        local m = N('true', 'false', 'null')
        local a6 = {
            ['true'] = true,
            ['false'] = false,
            ['null'] = nil,
        }
        local a7 = function(a8, a9, aa, ab)
            for a0 = a9, #a8 do
                if aa[a8:sub(a0, a0)] ~= ab then
                    return a0
                end
            end

            return #a8 + 1
        end
        local ac = function(a8, a9, J)
            local ad = 1
            local ae = 1

            for a0 = 1, a9 - 1 do
                ae = ae + 1

                if a8:sub(a0, a0) == '\n' then
                    ad = ad + 1
                    ae = 1
                end
            end

            error(string.format('%s at line %d col %d', J, ad, ae))
        end
        local af = function(A)
            local a2 = math.floor

            if A <= 0x7f then
                return string.char(A)
            elseif A <= 0x7ff then
                return string.char(a2(A / 64) + 192, A % 64 + 128)
            elseif A <= 0xffff then
                return string.char(a2(A / 4096) + 224, a2(A % 4096 / 64) + 128, A % 64 + 128)
            elseif A <= 0x10ffff then
                return string.char(a2(A / 262144) + 240, a2(A % 262144 / 4096) + 128, a2(A % 4096 / 64) + 128, A % 64 + 128)
            end

            error(string.format("invalid unicode codepoint '%x'", A))
        end
        local ag = function(ah)
            local ai = tonumber(ah:sub(1, 4), 16)
            local aj = tonumber(ah:sub(7, 10), 16)

            if aj then
                return af((ai - 0xd800) * 0x400 + aj - 0xdc00 + 0x10000)
            else
                return af(ai)
            end
        end
        local ak = function(a8, a0)
            local _ = ''
            local al = a0 + 1
            local Q = al

            while al <= #a8 do
                local am = a8:byte(al)

                if am < 32 then
                    ac(a8, al, 'control character in string')
                elseif am == 92 then
                    _ = _ .. a8:sub(Q, al - 1)
                    al = al + 1

                    local T = a8:sub(al, al)

                    if T == 'u' then
                        local an = a8:match('^[dD][89aAbB]%x%x\\u%x%x%x%x', al + 1) or a8:match('^%x%x%x%x', al + 1) or ac(a8, al - 1, 'invalid unicode escape in string')

                        _ = _ .. ag(an)
                        al = al + #an
                    else
                        if not a5[T] then
                            ac(a8, al - 1, "invalid escape char '" .. T .. "' in string")
                        end

                        _ = _ .. P[T]
                    end

                    Q = al + 1
                elseif am == 34 then
                    _ = _ .. a8:sub(Q, al - 1)

                    return _, al + 1
                end

                al = al + 1
            end

            ac(a8, a0, 'expected closing quote for string')
        end
        local ao = function(a8, a0)
            local am = a7(a8, a0, p)
            local ah = a8:sub(a0, am - 1)
            local A = tonumber(ah)

            if not A then
                ac(a8, a0, "invalid number '" .. ah .. "'")
            end

            return A, am
        end
        local ap = function(a8, a0)
            local am = a7(a8, a0, p)
            local aq = a8:sub(a0, am - 1)

            if not m[aq] then
                ac(a8, a0, "invalid literal '" .. aq .. "'")
            end

            return a6[aq], am
        end
        local ar = function(a8, a0)
            local _ = {}
            local A = 1

            a0 = a0 + 1

            while 1 do
                local am

                a0 = a7(a8, a0, L, true)

                if a8:sub(a0, a0) == ']' then
                    a0 = a0 + 1

                    break
                end

                am, a0 = a4(a8, a0)
                _[A] = am
                A = A + 1
                a0 = a7(a8, a0, L, true)

                local as = a8:sub(a0, a0)

                a0 = a0 + 1

                if as == ']' then
                    break
                end
                if as ~= ',' then
                    ac(a8, a0, "expected ']' or ','")
                end
            end

            return _, a0
        end
        local at = function(a8, a0)
            local _ = {}

            a0 = a0 + 1

            while 1 do
                local au, M

                a0 = a7(a8, a0, L, true)

                if a8:sub(a0, a0) == '}' then
                    a0 = a0 + 1

                    break
                end
                if a8:sub(a0, a0) ~= '"' then
                    ac(a8, a0, 'expected string for key')
                end

                au, a0 = a4(a8, a0)
                a0 = a7(a8, a0, L, true)

                if a8:sub(a0, a0) ~= ':' then
                    ac(a8, a0, "expected ':' after key")
                end

                a0 = a7(a8, a0 + 1, L, true)
                M, a0 = a4(a8, a0)
                _[au] = M
                a0 = a7(a8, a0, L, true)

                local as = a8:sub(a0, a0)

                a0 = a0 + 1

                if as == '}' then
                    break
                end
                if as ~= ',' then
                    ac(a8, a0, "expected '}' or ','")
                end
            end

            return _, a0
        end
        local av = {
            ['"'] = ak,
            ['0'] = ao,
            ['1'] = ao,
            ['2'] = ao,
            ['3'] = ao,
            ['4'] = ao,
            ['5'] = ao,
            ['6'] = ao,
            ['7'] = ao,
            ['8'] = ao,
            ['9'] = ao,
            ['-'] = ao,
            ['t'] = ap,
            ['f'] = ap,
            ['n'] = ap,
            ['['] = ar,
            ['{'] = at,
        }

        a4 = function(a8, a9)
            local as = a8:sub(a9, a9)
            local a2 = av[as]

            if a2 then
                return a2(a8, a9)
            end

            ac(a8, a9, "unexpected character '" .. as .. "'")
        end

        local aw = function(a8)
            if type(a8) ~= 'string' then
                error('expected argument of type string, got ' .. type(a8))
            end

            local _, a9 = a4(a8, a7(a8, 1, L, true))

            a9 = a7(a8, a9, L, true)

            if a9 <= #a8 then
                ac(a8, a9, 'trailing garbage')
            end

            return _
        end
        local lEncode, lDecode, lDigest = a3, aw, Z
        local Platoboost = {}
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)

        function Platoboost.New(Service, Secret)
            local service = Service
            local secret = Secret
            local useNonce = true
            local onMessage = function(message) end

            repeat
                task.wait(1)
            until game:IsLoaded()

            local requestSending = false
            local fSetClipboard, fRequest, fStringChar, fToString, fStringSub, fOsTime, fMathRandom, fMathFloor, fGetHwid = setclipboard or toclipboard, request or http_request or syn_request, string.char, tostring, string.sub, os.time, math.random, math.floor, gethwid or function(
            )
                return cloneref(game:GetService('Players')).LocalPlayer.UserId
            end
            local cachedLink, cachedTime = '', 0
            local host = 'https://api.platoboost.app'
            local hostResponse = fRequest({
                Url = host .. '/public/connectivity',
                Method = 'GET',
            })

            if hostResponse.StatusCode ~= 200 and hostResponse.StatusCode ~= 429 then
                host = 'https://api.platoboost.net'
            end

            function cacheLink()
                if cachedTime + (600) < fOsTime() then
                    local response = fRequest({
                        Url = host .. '/public/start',
                        Method = 'POST',
                        Body = lEncode({
                            service = service,
                            identifier = lDigest(fGetHwid()),
                        }),
                        Headers = {
                            ['Content-Type'] = 'application/json',
                            ['User-Agent'] = 'Roblox/Exploit',
                        },
                    })

                    if response.StatusCode == 200 then
                        local decoded = lDecode(response.Body)

                        if decoded.success == true then
                            cachedLink = decoded.data.url
                            cachedTime = fOsTime()

                            return true, cachedLink
                        else
                            onMessage(decoded.message)

                            return false, decoded.message
                        end
                    elseif response.StatusCode == 429 then
                        local msg = 
[[you are being rate limited, please wait 20 seconds and try again.]]

                        onMessage(msg)

                        return false, msg
                    end

                    local msg = 'Failed to cache link.'

                    onMessage(msg)

                    return false, msg
                else
                    return true, cachedLink
                end
            end

            cacheLink()

            local generateNonce = function()
                local str = ''

                for _ = 1, 16 do
                    str = str .. fStringChar(fMathFloor(fMathRandom() * (26)) + 97)
                end

                return str
            end

            for _ = 1, 5 do
                local oNonce = generateNonce()

                task.wait(0.2)

                if generateNonce() == oNonce then
                    local msg = 'platoboost nonce error.'

                    onMessage(msg)
                    error(msg)
                end
            end

            local copyLink = function()
                local success, link = cacheLink()

                if success then
                    fSetClipboard(link)
                end
            end
            local redeemKey = function(key)
                local nonce = generateNonce()
                local endpoint = host .. '/public/redeem/' .. fToString(service)
                local body = {
                    identifier = lDigest(fGetHwid()),
                    key = key,
                }

                if useNonce then
                    body.nonce = nonce
                end

                local response = fRequest({
                    Url = endpoint,
                    Method = 'POST',
                    Body = lEncode(body),
                    Headers = {
                        ['Content-Type'] = 'application/json',
                    },
                })

                if response.StatusCode == 200 then
                    local decoded = lDecode(response.Body)

                    if decoded.success == true then
                        if decoded.data.valid == true then
                            if useNonce then
                                if decoded.data.hash == lDigest('true' .. '-' .. nonce .. '-' .. secret) then
                                    return true
                                else
                                    onMessage('failed to verify integrity.')

                                    return false
                                end
                            else
                                return true
                            end
                        else
                            onMessage('key is invalid.')

                            return false
                        end
                    else
                        if fStringSub(decoded.message, 1, 27) == 'unique constraint violation' then
                            onMessage(
[[you already have an active key, please wait for it to expire before redeeming it.]])

                            return false
                        else
                            onMessage(decoded.message)

                            return false
                        end
                    end
                elseif response.StatusCode == 429 then
                    onMessage(
[[you are being rate limited, please wait 20 seconds and try again.]])

                    return false
                else
                    onMessage(
[[server returned an invalid status code, please try again later.]])

                    return false
                end
            end
            local verifyKey = function(key)
                if requestSending == true then
                    return false, ('A request is already being sent, please slow down.')
                else
                    requestSending = true
                end

                local nonce = generateNonce()
                local endpoint = host .. '/public/whitelist/' .. fToString(service) .. '?identifier=' .. lDigest(fGetHwid()) .. '&key=' .. key

                if useNonce then
                    endpoint = endpoint .. '&nonce=' .. nonce
                end

                local response = fRequest({
                    Url = endpoint,
                    Method = 'GET',
                })

                requestSending = false

                if response.StatusCode == 200 then
                    local decoded = lDecode(response.Body)

                    if decoded.success == true then
                        if decoded.data.valid == true then
                            if useNonce then
                                if decoded.data.hash == lDigest('true' .. '-' .. nonce .. '-' .. secret) then
                                    return true, ''
                                else
                                    return false, ('failed to verify integrity.')
                                end
                            else
                                return true
                            end
                        else
                            if fStringSub(key, 1, 4) == 'KEY_' then
                                return true, redeemKey(key)
                            else
                                return false, ('Key is invalid.')
                            end
                        end
                    else
                        return false, (decoded.message)
                    end
                elseif response.StatusCode == 429 then
                    return false, (
[[You are being rate limited, please wait 20 seconds and try again.]])
                else
                    return false, (
[[Server returned an invalid status code, please try again later.]])
                end
            end
            local getFlag = function(name)
                local nonce = generateNonce()
                local endpoint = host .. '/public/flag/' .. fToString(service) .. '?name=' .. name

                if useNonce then
                    endpoint = endpoint .. '&nonce=' .. nonce
                end

                local response = fRequest({
                    Url = endpoint,
                    Method = 'GET',
                })

                if response.StatusCode == 200 then
                    local decoded = lDecode(response.Body)

                    if decoded.success == true then
                        if useNonce then
                            if decoded.data.hash == lDigest(fToString(decoded.data.value) .. '-' .. nonce .. '-' .. secret) then
                                return decoded.data.value
                            else
                                onMessage('failed to verify integrity.')

                                return nil
                            end
                        else
                            return decoded.data.value
                        end
                    else
                        onMessage(decoded.message)

                        return nil
                    end
                else
                    return nil
                end
            end

            return {
                Verify = verifyKey,
                GetFlag = getFlag,
                Copy = copyLink,
            }
        end

        return Platoboost
    end
    function __DARKLUA_BUNDLE_MODULES.h()
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)
        local HttpService = cloneref(game:GetService('HttpService'))
        local PandaDevelopment = {}

        function PandaDevelopment.New(serviceId)
            local hwid = gethwid or function()
                return cloneref(game:GetService('Players')).LocalPlayer.UserId
            end
            local frequest, fsetclipboard = request or http_request or syn_request, setclipboard or toclipboard

            function ValidateKey(key)
                local validationUrl = 'https://pandadevelopment.net/v2_validation?key=' .. tostring(key) .. '&service=' .. tostring(serviceId) .. '&hwid=' .. tostring(hwid())
                local success, response = pcall(function()
                    return frequest({
                        Url = validationUrl,
                        Method = 'GET',
                        Headers = {
                            ['User-Agent'] = 'Roblox/Exploit',
                        },
                    })
                end)

                if success and response then
                    if response.Success then
                        local decodeSuccess, jsonData = pcall(function()
                            return HttpService:JSONDecode(response.Body)
                        end)

                        if decodeSuccess and jsonData then
                            if jsonData.V2_Authentication and jsonData.V2_Authentication == 'success' then
                                return true, 'Authenticated'
                            else
                                local reason = jsonData.Key_Information.Notes or 'Unknown reason'

                                return false, 'Authentication failed: ' .. reason
                            end
                        else
                            return false, 'JSON decode error'
                        end
                    else
                        warn('[Pelinda Ov2.5] HTTP request was not successful. Code: ' .. tostring(response.StatusCode) .. ' Message: ' .. response.StatusMessage)

                        return false, 'HTTP request failed: ' .. response.StatusMessage
                    end
                else
                    return false, 'Request pcall error'
                end
            end
            function GetKeyLink()
                return 'https://pandadevelopment.net/getkey?service=' .. tostring(serviceId) .. '&hwid=' .. tostring(hwid())
            end
            function CopyLink()
                return fsetclipboard(GetKeyLink())
            end

            return {
                Verify = ValidateKey,
                Copy = CopyLink,
            }
        end

        return PandaDevelopment
    end
    function __DARKLUA_BUNDLE_MODULES.i()
        local Luarmor = {}

        function Luarmor.New(scriptId, discord)
            local APIURL = 'https://sdkapi-public.luarmor.net/library.lua'
            local API = loadstring(game.HttpGetAsync and game:HttpGetAsync(APIURL) or HttpService:GetAsync(APIURL))()
            local fsetclipboard = setclipboard or toclipboard

            API.script_id = scriptId

            function ValidateKey(key)
                local status = API.check_key(key)

                if (status.code == 'KEY_VALID') then
                    return true, 'Whitelisted!'
                elseif (status.code == 'KEY_HWID_LOCKED') then
                    return false, 
[[Key linked to a different HWID. Please reset it using our bot]]
                elseif (status.code == 'KEY_INCORRECT') then
                    return false, 'Key is wrong or deleted!'
                else
                    return false, 'Key check failed:' .. status.message .. ' Code: ' .. status.code
                end
            end
            function CopyLink()
                fsetclipboard(tostring(discord))
            end

            return {
                Verify = ValidateKey,
                Copy = CopyLink,
            }
        end

        return Luarmor
    end
    function __DARKLUA_BUNDLE_MODULES.j()
        return {
            platoboost = {
                Name = 'Platoboost',
                Icon = 'rbxassetid://75920162824531',
                Args = {
                    'ServiceId',
                    'Secret',
                },
                New = __DARKLUA_BUNDLE_MODULES.load('g').New,
            },
            pandadevelopment = {
                Name = 'Panda Development',
                Icon = 'panda',
                Args = {
                    'ServiceId',
                },
                New = __DARKLUA_BUNDLE_MODULES.load('h').New,
            },
            luarmor = {
                Name = 'Luarmor',
                Icon = 'rbxassetid://130918283130165',
                Args = {
                    'ScriptId',
                    'Discord',
                },
                New = __DARKLUA_BUNDLE_MODULES.load('i').New,
            },
        }
    end
    function __DARKLUA_BUNDLE_MODULES.k()
        return 
[[{
    "name": "windui",
    "version": "1.6.62",
    "main": "./dist/main.lua",
    "repository": "https://github.com/kiciahook/WindUI",
    "discord": "https://discord.gg/ftgs-development-hub-1300692552005189632",
    "author": "Footagesus",
    "description": "Roblox UI Library for scripts",
    "license": "MIT",
    "scripts": {
        "dev": "bash build/build.sh dev $INPUT_FILE",
        "build": "bash build/build.sh build $INPUT_FILE",
        "live": "python -m http.server 8642",
        "watch": "chokidar . -i 'node_modules' -i 'dist' -i 'build' -c 'npm run dev --'",
        "live-build": "concurrently \"npm run live\" \"npm run watch --\"",
        "example-live-build": "INPUT_FILE=main_example.lua npm run live-build"
    },
    "keywords": [
        "ui-library",
        "ui-design",
        "script",
        "script-hub",
        "exploiting"
    ],
    "devDependencies": {
        "chokidar-cli": "^3.0.0",
        "concurrently": "^9.2.0"
    }
}]]
    end
    function __DARKLUA_BUNDLE_MODULES.l()
        local Button = {}
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween

        function Button.New(
            Title,
            Icon,
            Callback,
            Variant,
            Parent,
            Dialog,
            FullRounded,
            Radius
        )
            Variant = Variant or 'Primary'

            local Radius = Radius or (not FullRounded and 10 or 99)
            local IconButtonFrame

            if Icon and Icon ~= '' then
                IconButtonFrame = New('ImageLabel', {
                    Image = Creator.Icon(Icon)[1],
                    ImageRectSize = Creator.Icon(Icon)[2].ImageRectSize,
                    ImageRectOffset = Creator.Icon(Icon)[2].ImageRectPosition,
                    Size = UDim2.new(0, 21, 0, 21),
                    BackgroundTransparency = 1,
                    ImageColor3 = Variant == 'White' and Color3.new(0, 0, 0) or nil,
                    ImageTransparency = Variant == 'White' and 0.4 or 0,
                    ThemeTag = {
                        ImageColor3 = Variant ~= 'White' and 'Icon' or nil,
                    },
                })
            end

            local ButtonFrame = New('TextButton', {
                Size = UDim2.new(0, 0, 1, 0),
                AutomaticSize = 'X',
                Parent = Parent,
                BackgroundTransparency = 1,
            }, {
                Creator.NewRoundFrame(Radius, 'Squircle', {
                    ThemeTag = {
                        ImageColor3 = Variant ~= 'White' and 'Button' or nil,
                    },
                    ImageColor3 = Variant == 'White' and Color3.new(1, 1, 1) or nil,
                    Size = UDim2.new(1, 0, 1, 0),
                    Name = 'Squircle',
                    ImageTransparency = Variant == 'Primary' and 0 or Variant == 'White' and 0 or 1,
                }),
                Creator.NewRoundFrame(Radius, 'Squircle', {
                    ImageColor3 = Color3.new(1, 1, 1),
                    Size = UDim2.new(1, 0, 1, 0),
                    Name = 'Special',
                    ImageTransparency = Variant == 'Secondary' and 0.95 or 1,
                }),
                Creator.NewRoundFrame(Radius, 'Shadow-sm', {
                    ImageColor3 = Color3.new(0, 0, 0),
                    Size = UDim2.new(1, 3, 1, 3),
                    AnchorPoint = Vector2.new(0.5, 0.5),
                    Position = UDim2.new(0.5, 0, 0.5, 0),
                    Name = 'Shadow',
                    ImageTransparency = 1,
                    Visible = not FullRounded,
                }),
                Creator.NewRoundFrame(Radius, not FullRounded and 'SquircleOutline' or 'SquircleOutline2', {
                    ThemeTag = {
                        ImageColor3 = Variant ~= 'White' and 'Outline' or nil,
                    },
                    Size = UDim2.new(1, 0, 1, 0),
                    ImageColor3 = Variant == 'White' and Color3.new(0, 0, 0) or nil,
                    ImageTransparency = Variant == 'Primary' and 0.95 or 0.85,
                    Name = 'SquircleOutline',
                }, {
                    New('UIGradient', {
                        Rotation = 70,
                        Color = ColorSequence.new({
                            ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
                            ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 255, 255)),
                            ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255)),
                        }),
                        Transparency = NumberSequence.new({
                            NumberSequenceKeypoint.new(0, 0.1),
                            NumberSequenceKeypoint.new(0.5, 1),
                            NumberSequenceKeypoint.new(1, 0.1),
                        }),
                    }),
                }),
                Creator.NewRoundFrame(Radius, 'Squircle', {
                    Size = UDim2.new(1, 0, 1, 0),
                    Name = 'Frame',
                    ThemeTag = {
                        ImageColor3 = Variant ~= 'White' and 'Text' or nil,
                    },
                    ImageColor3 = Variant == 'White' and Color3.new(0, 0, 0) or nil,
                    ImageTransparency = 1,
                }, {
                    New('UIPadding', {
                        PaddingLeft = UDim.new(0, 16),
                        PaddingRight = UDim.new(0, 16),
                    }),
                    New('UIListLayout', {
                        FillDirection = 'Horizontal',
                        Padding = UDim.new(0, 8),
                        VerticalAlignment = 'Center',
                        HorizontalAlignment = 'Center',
                    }),
                    IconButtonFrame,
                    New('TextLabel', {
                        BackgroundTransparency = 1,
                        FontFace = Font.new(Creator.Font, Enum.FontWeight.SemiBold),
                        Text = Title or 'Button',
                        ThemeTag = {
                            TextColor3 = (Variant ~= 'Primary' and Variant ~= 'White') and 'Text',
                        },
                        TextColor3 = Variant == 'Primary' and Color3.new(1, 1, 1) or Variant == 'White' and Color3.new(0, 0, 0) or nil,
                        AutomaticSize = 'XY',
                        TextSize = 18,
                    }),
                }),
            })

            Creator.AddSignal(ButtonFrame.MouseEnter, function()
                Tween(ButtonFrame.Frame, 0.047, {ImageTransparency = 0.95}):Play()
            end)
            Creator.AddSignal(ButtonFrame.MouseLeave, function()
                Tween(ButtonFrame.Frame, 0.047, {ImageTransparency = 1}):Play()
            end)
            Creator.AddSignal(ButtonFrame.MouseButton1Up, function()
                if Dialog then
                    Dialog:Close()()
                end
                if Callback then
                    Creator.SafeCallback(Callback)
                end
            end)

            return ButtonFrame
        end

        return Button
    end
    function __DARKLUA_BUNDLE_MODULES.m()
        local Input = {}
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local _ = Creator.Tween

        function Input.New(
            Placeholder,
            Icon,
            Parent,
            Type,
            Callback,
            OnChange,
            Radius,
            ClearTextOnFocus
        )
            Type = Type or 'Input'

            local Radius = Radius or 10
            local IconInputFrame

            if Icon and Icon ~= '' then
                IconInputFrame = New('ImageLabel', {
                    Image = Creator.Icon(Icon)[1],
                    ImageRectSize = Creator.Icon(Icon)[2].ImageRectSize,
                    ImageRectOffset = Creator.Icon(Icon)[2].ImageRectPosition,
                    Size = UDim2.new(0, 21, 0, 21),
                    BackgroundTransparency = 1,
                    ThemeTag = {
                        ImageColor3 = 'Icon',
                    },
                })
            end

            local isMulti = Type ~= 'Input'
            local TextBox = New('TextBox', {
                BackgroundTransparency = 1,
                TextSize = 17,
                FontFace = Font.new(Creator.Font, Enum.FontWeight.Regular),
                Size = UDim2.new(1, IconInputFrame and -29 or 0, 1, 0),
                PlaceholderText = Placeholder,
                ClearTextOnFocus = ClearTextOnFocus or false,
                ClipsDescendants = true,
                TextWrapped = isMulti,
                MultiLine = isMulti,
                TextXAlignment = 'Left',
                TextYAlignment = Type == 'Input' and 'Center' or 'Top',
                ThemeTag = {
                    PlaceholderColor3 = 'PlaceholderText',
                    TextColor3 = 'Text',
                },
            })
            local InputFrame = New('Frame', {
                Size = UDim2.new(1, 0, 0, 42),
                Parent = Parent,
                BackgroundTransparency = 1,
            }, {
                New('Frame', {
                    Size = UDim2.new(1, 0, 1, 0),
                    BackgroundTransparency = 1,
                }, {
                    Creator.NewRoundFrame(Radius, 'Squircle', {
                        ThemeTag = {
                            ImageColor3 = 'Accent',
                        },
                        Size = UDim2.new(1, 0, 1, 0),
                        ImageTransparency = 0.97,
                    }),
                    Creator.NewRoundFrame(Radius, 'SquircleOutline', {
                        ThemeTag = {
                            ImageColor3 = 'Outline',
                        },
                        Size = UDim2.new(1, 0, 1, 0),
                        ImageTransparency = 0.95,
                    }, {}),
                    Creator.NewRoundFrame(Radius, 'Squircle', {
                        Size = UDim2.new(1, 0, 1, 0),
                        Name = 'Frame',
                        ImageColor3 = Color3.new(1, 1, 1),
                        ImageTransparency = 0.95,
                    }, {
                        New('UIPadding', {
                            PaddingTop = UDim.new(0, Type == 'Input' and 0 or 12),
                            PaddingLeft = UDim.new(0, 12),
                            PaddingRight = UDim.new(0, 12),
                            PaddingBottom = UDim.new(0, Type == 'Input' and 0 or 12),
                        }),
                        New('UIListLayout', {
                            FillDirection = 'Horizontal',
                            Padding = UDim.new(0, 8),
                            VerticalAlignment = Type == 'Input' and 'Center' or 'Top',
                            HorizontalAlignment = 'Left',
                        }),
                        IconInputFrame,
                        TextBox,
                    }),
                }),
            })

            if OnChange then
                Creator.AddSignal(TextBox:GetPropertyChangedSignal('Text'), function(
                )
                    if Callback then
                        Creator.SafeCallback(Callback, TextBox.Text)
                    end
                end)
            else
                Creator.AddSignal(TextBox.FocusLost, function()
                    if Callback then
                        Creator.SafeCallback(Callback, TextBox.Text)
                    end
                end)
            end

            return InputFrame
        end

        return Input
    end
    function __DARKLUA_BUNDLE_MODULES.n()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween
        local DialogModule = {
            Holder = nil,
            Parent = nil,
        }

        function DialogModule.Init(WindowTable, Parent)
            Window = WindowTable
            DialogModule.Parent = Parent

            return DialogModule
        end
        function DialogModule.Create(Key, Type)
            local Dialog = {
                UICorner = 24,
                UIPadding = 15,
                UIElements = {},
            }

            if Key then
                Dialog.UIPadding = 0
            end
            if Key then
                Dialog.UICorner = 26
            end

            Type = Type or 'Dialog'

            if not Key then
                Dialog.UIElements.FullScreen = New('Frame', {
                    ZIndex = 999,
                    BackgroundTransparency = 1,
                    BackgroundColor3 = Color3.fromHex('#000000'),
                    Size = UDim2.new(1, 0, 1, 0),
                    Active = false,
                    Visible = false,
                    Parent = DialogModule.Parent or (Window and Window.UIElements and Window.UIElements.Main and Window.UIElements.Main.Main),
                }, {
                    New('UICorner', {
                        CornerRadius = UDim.new(0, Window.UICorner),
                    }),
                })
            end

            Dialog.UIElements.Main = New('Frame', {
                Size = UDim2.new(0, 280, 0, 0),
                ThemeTag = {
                    BackgroundColor3 = Type .. 'Background',
                },
                AutomaticSize = 'Y',
                BackgroundTransparency = 1,
                Visible = false,
                ZIndex = 99999,
            }, {
                New('UIPadding', {
                    PaddingTop = UDim.new(0, Dialog.UIPadding),
                    PaddingLeft = UDim.new(0, Dialog.UIPadding),
                    PaddingRight = UDim.new(0, Dialog.UIPadding),
                    PaddingBottom = UDim.new(0, Dialog.UIPadding),
                }),
            })
            Dialog.UIElements.MainContainer = Creator.NewRoundFrame(Dialog.UICorner, 'Squircle', {
                Visible = false,
                ImageTransparency = Key and 0.15 or 0,
                Parent = Key and DialogModule.Parent or Dialog.UIElements.FullScreen,
                Position = UDim2.new(0.5, 0, 0.5, 0),
                AnchorPoint = Vector2.new(0.5, 0.5),
                AutomaticSize = 'XY',
                ThemeTag = {
                    ImageColor3 = Type .. 'Background',
                    ImageTransparency = Type .. 'BackgroundTransparency',
                },
                ZIndex = 9999,
            }, {
                Dialog.UIElements.Main,
            })

            function Dialog:Open()
                if not Key then
                    Dialog.UIElements.FullScreen.Visible = true
                    Dialog.UIElements.FullScreen.Active = true
                end

                task.spawn(function()
                    Dialog.UIElements.MainContainer.Visible = true

                    if not Key then
                        Tween(Dialog.UIElements.FullScreen, 0.1, {BackgroundTransparency = 0.3}):Play()
                    end

                    Tween(Dialog.UIElements.MainContainer, 0.1, {ImageTransparency = 0}):Play()
                    task.spawn(function()
                        task.wait(0.05)

                        Dialog.UIElements.Main.Visible = true
                    end)
                end)
            end
            function Dialog:Close()
                if not Key then
                    Tween(Dialog.UIElements.FullScreen, 0.1, {BackgroundTransparency = 1}):Play()

                    Dialog.UIElements.FullScreen.Active = false

                    task.spawn(function()
                        task.wait(0.1)

                        Dialog.UIElements.FullScreen.Visible = false
                    end)
                end

                Dialog.UIElements.Main.Visible = false

                Tween(Dialog.UIElements.MainContainer, 0.1, {ImageTransparency = 1}):Play()
                task.spawn(function()
                    task.wait(0.1)

                    if not Key then
                        Dialog.UIElements.FullScreen:Destroy()
                    else
                        Dialog.UIElements.MainContainer:Destroy()
                    end
                end)

                return function() end
            end

            return Dialog
        end

        return DialogModule
    end
    function __DARKLUA_BUNDLE_MODULES.o()
        local KeySystem = {}
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween
        local CreateButton = __DARKLUA_BUNDLE_MODULES.load('l').New
        local CreateInput = __DARKLUA_BUNDLE_MODULES.load('m').New

        function KeySystem.new(Config, Filename, func, keyValidator)
            local KeyDialogInit = __DARKLUA_BUNDLE_MODULES.load('n').Init(nil, Config.WindUI.ScreenGui.KeySystem)
            local KeyDialog = KeyDialogInit.Create(true)
            local Services = {}
            local EnteredKey
            local ThumbnailSize = (Config.KeySystem.Thumbnail and Config.KeySystem.Thumbnail.Width) or 200
            local UISize = 430

            if Config.KeySystem.Thumbnail and Config.KeySystem.Thumbnail.Image then
                UISize = 430 + (ThumbnailSize / 2)
            end

            KeyDialog.UIElements.Main.AutomaticSize = 'Y'
            KeyDialog.UIElements.Main.Size = UDim2.new(0, UISize, 0, 0)

            local IconFrame

            if Config.Icon then
                IconFrame = Creator.Image(Config.Icon, Config.Title .. ':' .. Config.Icon, 0, 'Temp', 'KeySystem', Config.IconThemed)
                IconFrame.Size = UDim2.new(0, 24, 0, 24)
                IconFrame.LayoutOrder = -1
            end

            local Title = New('TextLabel', {
                AutomaticSize = 'XY',
                BackgroundTransparency = 1,
                Text = Config.KeySystem.Title or Config.Title,
                FontFace = Font.new(Creator.Font, Enum.FontWeight.SemiBold),
                ThemeTag = {
                    TextColor3 = 'Text',
                },
                TextSize = 20,
            })
            local KeySystemTitle = New('TextLabel', {
                AutomaticSize = 'XY',
                BackgroundTransparency = 1,
                Text = 'Key System',
                AnchorPoint = Vector2.new(1, 0.5),
                Position = UDim2.new(1, 0, 0.5, 0),
                TextTransparency = 1,
                FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                ThemeTag = {
                    TextColor3 = 'Text',
                },
                TextSize = 16,
            })
            local IconAndTitleContainer = New('Frame', {
                BackgroundTransparency = 1,
                AutomaticSize = 'XY',
            }, {
                New('UIListLayout', {
                    Padding = UDim.new(0, 14),
                    FillDirection = 'Horizontal',
                    VerticalAlignment = 'Center',
                }),
                IconFrame,
                Title,
            })
            local TitleContainer = New('Frame', {
                AutomaticSize = 'Y',
                Size = UDim2.new(1, 0, 0, 0),
                BackgroundTransparency = 1,
            }, {IconAndTitleContainer, KeySystemTitle})
            local InputFrame = CreateInput('Enter Key', 'key', nil, 'Input', function(
                k
            )
                EnteredKey = k
            end)
            local NoteText

            if Config.KeySystem.Note and Config.KeySystem.Note ~= '' then
                NoteText = New('TextLabel', {
                    Size = UDim2.new(1, 0, 0, 0),
                    AutomaticSize = 'Y',
                    FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                    TextXAlignment = 'Left',
                    Text = Config.KeySystem.Note,
                    TextSize = 18,
                    TextTransparency = 0.4,
                    ThemeTag = {
                        TextColor3 = 'Text',
                    },
                    BackgroundTransparency = 1,
                    RichText = true,
                    TextWrapped = true,
                })
            end

            local ButtonsContainer = New('Frame', {
                Size = UDim2.new(1, 0, 0, 42),
                BackgroundTransparency = 1,
            }, {
                New('Frame', {
                    BackgroundTransparency = 1,
                    AutomaticSize = 'X',
                    Size = UDim2.new(0, 0, 1, 0),
                }, {
                    New('UIListLayout', {
                        Padding = UDim.new(0, 9),
                        FillDirection = 'Horizontal',
                    }),
                }),
            })
            local ThumbnailFrame

            if Config.KeySystem.Thumbnail and Config.KeySystem.Thumbnail.Image then
                local ThumbnailTitle

                if Config.KeySystem.Thumbnail.Title then
                    ThumbnailTitle = New('TextLabel', {
                        Text = Config.KeySystem.Thumbnail.Title,
                        ThemeTag = {
                            TextColor3 = 'Text',
                        },
                        TextSize = 18,
                        FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                        BackgroundTransparency = 1,
                        AutomaticSize = 'XY',
                        AnchorPoint = Vector2.new(0.5, 0.5),
                        Position = UDim2.new(0.5, 0, 0.5, 0),
                    })
                end

                ThumbnailFrame = New('ImageLabel', {
                    Image = Config.KeySystem.Thumbnail.Image,
                    BackgroundTransparency = 1,
                    Size = UDim2.new(0, ThumbnailSize, 1, -12),
                    Position = UDim2.new(0, 6, 0, 6),
                    Parent = KeyDialog.UIElements.Main,
                    ScaleType = 'Crop',
                }, {
                    ThumbnailTitle,
                    New('UICorner', {
                        CornerRadius = UDim.new(0, 20),
                    }),
                })
            end

            New('Frame', {
                Size = UDim2.new(1, ThumbnailFrame and -ThumbnailSize or 0, 1, 0),
                Position = UDim2.new(0, ThumbnailFrame and ThumbnailSize or 0, 0, 0),
                BackgroundTransparency = 1,
                Parent = KeyDialog.UIElements.Main,
            }, {
                New('Frame', {
                    Size = UDim2.new(1, 0, 1, 0),
                    BackgroundTransparency = 1,
                }, {
                    New('UIListLayout', {
                        Padding = UDim.new(0, 18),
                        FillDirection = 'Vertical',
                    }),
                    TitleContainer,
                    NoteText,
                    InputFrame,
                    ButtonsContainer,
                    New('UIPadding', {
                        PaddingTop = UDim.new(0, 16),
                        PaddingLeft = UDim.new(0, 16),
                        PaddingRight = UDim.new(0, 16),
                        PaddingBottom = UDim.new(0, 16),
                    }),
                }),
            })

            local ExitButton = CreateButton('Exit', 'log-out', function()
                KeyDialog:Close()()
            end, 'Tertiary', ButtonsContainer.Frame)

            if ThumbnailFrame then
                ExitButton.Parent = ThumbnailFrame
                ExitButton.Size = UDim2.new(0, 0, 0, 42)
                ExitButton.Position = UDim2.new(0, 10, 1, -10)
                ExitButton.AnchorPoint = Vector2.new(0, 1)
            end
            if Config.KeySystem.URL then
                CreateButton('Get key', 'key', function()
                    setclipboard(Config.KeySystem.URL)
                end, 'Secondary', ButtonsContainer.Frame)
            end
            if Config.KeySystem.API then
                local Width = 240
                local Opened = false
                local ButtonFrame = CreateButton('Get key', 'key', nil, 'Secondary', ButtonsContainer.Frame)
                local Divider = Creator.NewRoundFrame(99, 'Squircle', {
                    Size = UDim2.new(0, 1, 1, 0),
                    ThemeTag = {
                        ImageColor3 = 'Text',
                    },
                    ImageTransparency = 0.9,
                })

                New('Frame', {
                    BackgroundTransparency = 1,
                    Size = UDim2.new(0, 0, 1, 0),
                    AutomaticSize = 'X',
                    Parent = ButtonFrame.Frame,
                }, {
                    Divider,
                    New('UIPadding', {
                        PaddingLeft = UDim.new(0, 5),
                        PaddingRight = UDim.new(0, 5),
                    }),
                })

                local ChevronDown = Creator.Image('chevron-down', 'chevron-down', 0, 'Temp', 'KeySystem', true)

                ChevronDown.Size = UDim2.new(1, 0, 1, 0)

                New('Frame', {
                    Size = UDim2.new(0, 21, 0, 21),
                    Parent = ButtonFrame.Frame,
                    BackgroundTransparency = 1,
                }, {ChevronDown})

                local DropdownFrame = Creator.NewRoundFrame(15, 'Squircle', {
                    Size = UDim2.new(1, 0, 0, 0),
                    AutomaticSize = 'Y',
                    ThemeTag = {
                        ImageColor3 = 'Background',
                    },
                }, {
                    New('UIPadding', {
                        PaddingTop = UDim.new(0, 5),
                        PaddingLeft = UDim.new(0, 5),
                        PaddingRight = UDim.new(0, 5),
                        PaddingBottom = UDim.new(0, 5),
                    }),
                    New('UIListLayout', {
                        FillDirection = 'Vertical',
                        Padding = UDim.new(0, 5),
                    }),
                })
                local DropdownContainer = New('Frame', {
                    BackgroundTransparency = 1,
                    Size = UDim2.new(0, Width, 0, 0),
                    ClipsDescendants = true,
                    AnchorPoint = Vector2.new(1, 0),
                    Parent = ButtonFrame,
                    Position = UDim2.new(1, 0, 1, 15),
                }, {DropdownFrame})

                New('TextLabel', {
                    Text = 'Select Service',
                    BackgroundTransparency = 1,
                    FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                    ThemeTag = {
                        TextColor3 = 'Text',
                    },
                    TextTransparency = 0.2,
                    TextSize = 16,
                    Size = UDim2.new(1, 0, 0, 0),
                    AutomaticSize = 'Y',
                    TextWrapped = true,
                    TextXAlignment = 'Left',
                    Parent = DropdownFrame,
                }, {
                    New('UIPadding', {
                        PaddingTop = UDim.new(0, 10),
                        PaddingLeft = UDim.new(0, 10),
                        PaddingRight = UDim.new(0, 10),
                        PaddingBottom = UDim.new(0, 10),
                    }),
                })

                for _, i in next, Config.KeySystem.API do
                    local serviceDef = Config.WindUI.Services[i.Type]

                    if serviceDef then
                        local args = {}

                        for _, argName in next, serviceDef.Args do
                            table.insert(args, i[argName])
                        end

                        local serviceInstance = serviceDef.New(table.unpack(args))

                        serviceInstance.Type = i.Type

                        table.insert(Services, serviceInstance)

                        local IconFrame = Creator.Image(i.Icon or serviceDef.Icon or Icons[i.Type] or 'user', i.Icon or serviceDef.Icon or Icons[i.Type] or 'user', 0, 'Temp', 'KeySystem', true)

                        IconFrame.Size = UDim2.new(0, 24, 0, 24)

                        local APIFrame = Creator.NewRoundFrame(10, 'Squircle', {
                            Size = UDim2.new(1, 0, 0, 0),
                            ThemeTag = {
                                ImageColor3 = 'Text',
                            },
                            ImageTransparency = 1,
                            Parent = DropdownFrame,
                            AutomaticSize = 'Y',
                        }, {
                            New('UIListLayout', {
                                FillDirection = 'Horizontal',
                                Padding = UDim.new(0, 10),
                                VerticalAlignment = 'Center',
                            }),
                            IconFrame,
                            New('UIPadding', {
                                PaddingTop = UDim.new(0, 10),
                                PaddingLeft = UDim.new(0, 10),
                                PaddingRight = UDim.new(0, 10),
                                PaddingBottom = UDim.new(0, 10),
                            }),
                            New('Frame', {
                                BackgroundTransparency = 1,
                                Size = UDim2.new(1, -34, 0, 0),
                                AutomaticSize = 'Y',
                            }, {
                                New('UIListLayout', {
                                    FillDirection = 'Vertical',
                                    Padding = UDim.new(0, 5),
                                    HorizontalAlignment = 'Center',
                                }),
                                New('TextLabel', {
                                    Text = i.Title or serviceDef.Name,
                                    BackgroundTransparency = 1,
                                    FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                                    ThemeTag = {
                                        TextColor3 = 'Text',
                                    },
                                    TextTransparency = 0.05,
                                    TextSize = 18,
                                    Size = UDim2.new(1, 0, 0, 0),
                                    AutomaticSize = 'Y',
                                    TextWrapped = true,
                                    TextXAlignment = 'Left',
                                }),
                                New('TextLabel', {
                                    Text = i.Desc or '',
                                    BackgroundTransparency = 1,
                                    FontFace = Font.new(Creator.Font, Enum.FontWeight.Regular),
                                    ThemeTag = {
                                        TextColor3 = 'Text',
                                    },
                                    TextTransparency = 0.2,
                                    TextSize = 16,
                                    Size = UDim2.new(1, 0, 0, 0),
                                    AutomaticSize = 'Y',
                                    TextWrapped = true,
                                    Visible = i.Desc and true or false,
                                    TextXAlignment = 'Left',
                                }),
                            }),
                        }, true)

                        Creator.AddSignal(APIFrame.MouseEnter, function()
                            Tween(APIFrame, 0.08, {ImageTransparency = 0.95}):Play()
                        end)
                        Creator.AddSignal(APIFrame.InputEnded, function()
                            Tween(APIFrame, 0.08, {ImageTransparency = 1}):Play()
                        end)
                        Creator.AddSignal(APIFrame.MouseButton1Click, function()
                            serviceInstance.Copy()
                            Config.WindUI:Notify({
                                Title = 'Key System',
                                Content = 'Key link copied to clipboard.',
                                Image = 'key',
                            })
                        end)
                    end
                end

                Creator.AddSignal(ButtonFrame.MouseButton1Click, function()
                    if not Opened then
                        Tween(DropdownContainer, 0.3, {
                            Size = UDim2.new(0, Width, 0, DropdownFrame.AbsoluteSize.Y + 1),
                        }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                        Tween(ChevronDown, 0.3, {Rotation = 180}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                    else
                        Tween(DropdownContainer, 0.25, {
                            Size = UDim2.new(0, Width, 0, 0),
                        }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                        Tween(ChevronDown, 0.25, {Rotation = 0}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                    end

                    Opened = not Opened
                end)
            end

            local handleSuccess = function(key)
                KeyDialog:Close()()
                writefile((Config.Folder or 'Temp') .. '/' .. Filename .. '.key', tostring(key))
                task.wait(0.4)
                func(true)
            end
            local SubmitButton = CreateButton('Submit', 'arrow-right', function()
                local key = tostring(EnteredKey or 'empty')
                local _ = Config.Folder or Config.Title

                if Config.KeySystem.KeyValidator then
                    local isValid = Config.KeySystem.KeyValidator(key)

                    if isValid then
                        if Config.KeySystem.SaveKey then
                            handleSuccess(key)
                        else
                            KeyDialog:Close()()
                            task.wait(0.4)
                            func(true)
                        end
                    else
                        Config.WindUI:Notify({
                            Title = 'Key System. Error',
                            Content = 'Invalid key.',
                            Icon = 'triangle-alert',
                        })
                    end
                elseif not Config.KeySystem.API then
                    local isKey = type(Config.KeySystem.Key) == 'table' and table.find(Config.KeySystem.Key, key) or Config.KeySystem.Key == key

                    if isKey then
                        if Config.KeySystem.SaveKey then
                            handleSuccess(key)
                        else
                            KeyDialog:Close()()
                            task.wait(0.4)
                            func(true)
                        end
                    end
                else
                    local isSuccess, result

                    for _, service in next, Services do
                        local success, res = service.Verify(key)

                        if success then
                            isSuccess, result = true, res

                            break
                        end

                        result = res
                    end

                    if isSuccess then
                        handleSuccess(key)
                    else
                        Config.WindUI:Notify({
                            Title = 'Key System. Error',
                            Content = result,
                            Icon = 'triangle-alert',
                        })
                    end
                end
            end, 'Primary', ButtonsContainer)

            SubmitButton.AnchorPoint = Vector2.new(1, 0.5)
            SubmitButton.Position = UDim2.new(1, 0, 0.5, 0)

            KeyDialog:Open()
        end

        return KeySystem
    end
    function __DARKLUA_BUNDLE_MODULES.p()
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)
        local map = function(value, inMin, inMax, outMin, outMax)
            return (value - inMin) * (outMax - outMin) / (inMax - inMin) + outMin
        end
        local viewportPointToWorld = function(location, distance)
            local unitRay = cloneref(game:GetService('Workspace')).CurrentCamera:ScreenPointToRay(location.X, location.Y)

            return unitRay.Origin + unitRay.Direction * distance
        end
        local getOffset = function()
            local viewportSizeY = cloneref(game:GetService('Workspace')).CurrentCamera.ViewportSize.Y

            return map(viewportSizeY, 0, 2560, 8, 56)
        end

        return {viewportPointToWorld, getOffset}
    end
    function __DARKLUA_BUNDLE_MODULES.q()
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local viewportPointToWorld, getOffset = unpack(__DARKLUA_BUNDLE_MODULES.load('p'))
        local BlurFolder = Instance.new('Folder', cloneref(game:GetService('Workspace')).CurrentCamera)
        local createAcrylic = function()
            local Part = New('Part', {
                Name = 'Body',
                Color = Color3.new(0, 0, 0),
                Material = Enum.Material.Glass,
                Size = Vector3.new(1, 1, 0),
                Anchored = true,
                CanCollide = false,
                Locked = true,
                CastShadow = false,
                Transparency = 0.98,
            }, {
                New('SpecialMesh', {
                    MeshType = Enum.MeshType.Brick,
                    Offset = Vector3.new(0, 0, -1E-6),
                }),
            })

            return Part
        end
        local createAcrylicBlur = function(distance)
            local cleanups = {}

            distance = distance or 0.001

            local positions = {
                topLeft = Vector2.new(),
                topRight = Vector2.new(),
                bottomRight = Vector2.new(),
            }
            local model = createAcrylic()

            model.Parent = BlurFolder

            local updatePositions = function(size, position)
                positions.topLeft = position
                positions.topRight = position + Vector2.new(size.X, 0)
                positions.bottomRight = position + size
            end
            local render = function()
                local res = cloneref(game:GetService('Workspace')).CurrentCamera

                if res then
                    res = res.CFrame
                end

                local cond = res

                if not cond then
                    cond = CFrame.new()
                end

                local camera = cond
                local topLeft = positions.topLeft
                local topRight = positions.topRight
                local bottomRight = positions.bottomRight
                local topLeft3D = viewportPointToWorld(topLeft, distance)
                local topRight3D = viewportPointToWorld(topRight, distance)
                local bottomRight3D = viewportPointToWorld(bottomRight, distance)
                local width = (topRight3D - topLeft3D).Magnitude
                local height = (topRight3D - bottomRight3D).Magnitude

                model.CFrame = CFrame.fromMatrix((topLeft3D + bottomRight3D) / 2, camera.XVector, camera.YVector, camera.ZVector)
                model.Mesh.Scale = Vector3.new(width, height, 0)
            end
            local onChange = function(rbx)
                local offset = getOffset()
                local size = rbx.AbsoluteSize - Vector2.new(offset, offset)
                local position = rbx.AbsolutePosition + Vector2.new(offset / 2, offset / 2)

                updatePositions(size, position)
                task.spawn(render)
            end
            local renderOnChange = function()
                local camera = cloneref(game:GetService('Workspace')).CurrentCamera

                if not camera then
                    return
                end

                table.insert(cleanups, camera:GetPropertyChangedSignal('CFrame'):Connect(render))
                table.insert(cleanups, camera:GetPropertyChangedSignal('ViewportSize'):Connect(render))
                table.insert(cleanups, camera:GetPropertyChangedSignal('FieldOfView'):Connect(render))
                task.spawn(render)
            end

            model.Destroying:Connect(function()
                for _, item in cleanups do
                    pcall(function()
                        item:Disconnect()
                    end)
                end
            end)
            renderOnChange()

            return onChange, model
        end

        return function(distance)
            local Blur = {}
            local onChange, model = createAcrylicBlur(distance)
            local comp = New('Frame', {
                BackgroundTransparency = 1,
                Size = UDim2.fromScale(1, 1),
            })

            Creator.AddSignal(comp:GetPropertyChangedSignal('AbsolutePosition'), function(
            )
                onChange(comp)
            end)
            Creator.AddSignal(comp:GetPropertyChangedSignal('AbsoluteSize'), function(
            )
                onChange(comp)
            end)

            Blur.AddParent = function(Parent)
                Creator.AddSignal(Parent:GetPropertyChangedSignal('Visible'), function(
                )
                    Blur.SetVisibility(Parent.Visible)
                end)
            end
            Blur.SetVisibility = function(Value)
                model.Transparency = Value and 0.98 or 1
            end
            Blur.Frame = comp
            Blur.Model = model

            return Blur
        end
    end
    function __DARKLUA_BUNDLE_MODULES.r()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local AcrylicBlur = __DARKLUA_BUNDLE_MODULES.load('q')
        local New = Creator.New

        return function(props)
            local AcrylicPaint = {}

            AcrylicPaint.Frame = New('Frame', {
                Size = UDim2.fromScale(1, 1),
                BackgroundTransparency = 1,
                BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                BorderSizePixel = 0,
            }, {
                New('UICorner', {
                    CornerRadius = UDim.new(0, 8),
                }),
                New('Frame', {
                    BackgroundTransparency = 1,
                    Size = UDim2.fromScale(1, 1),
                    Name = 'Background',
                    ThemeTag = {
                        BackgroundColor3 = 'AcrylicMain',
                    },
                }, {
                    New('UICorner', {
                        CornerRadius = UDim.new(0, 8),
                    }),
                }),
                New('Frame', {
                    BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                    BackgroundTransparency = 1,
                    Size = UDim2.fromScale(1, 1),
                }, {}),
                New('ImageLabel', {
                    Image = 'rbxassetid://9968344105',
                    ImageTransparency = 0.98,
                    ScaleType = Enum.ScaleType.Tile,
                    TileSize = UDim2.new(0, 128, 0, 128),
                    Size = UDim2.fromScale(1, 1),
                    BackgroundTransparency = 1,
                }, {
                    New('UICorner', {
                        CornerRadius = UDim.new(0, 8),
                    }),
                }),
                New('ImageLabel', {
                    Image = 'rbxassetid://9968344227',
                    ImageTransparency = 0.9,
                    ScaleType = Enum.ScaleType.Tile,
                    TileSize = UDim2.new(0, 128, 0, 128),
                    Size = UDim2.fromScale(1, 1),
                    BackgroundTransparency = 1,
                    ThemeTag = {
                        ImageTransparency = 'AcrylicNoise',
                    },
                }, {
                    New('UICorner', {
                        CornerRadius = UDim.new(0, 8),
                    }),
                }),
                New('Frame', {
                    BackgroundTransparency = 1,
                    Size = UDim2.fromScale(1, 1),
                    ZIndex = 2,
                }, {}),
            })

            local Blur

            task.wait()

            if props.UseAcrylic then
                Blur = AcrylicBlur()
                Blur.Frame.Parent = AcrylicPaint.Frame
                AcrylicPaint.Model = Blur.Model
                AcrylicPaint.AddParent = Blur.AddParent
                AcrylicPaint.SetVisibility = Blur.SetVisibility
            end

            return AcrylicPaint, Blur
        end
    end
    function __DARKLUA_BUNDLE_MODULES.s()
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)
        local Acrylic = {
            AcrylicBlur = __DARKLUA_BUNDLE_MODULES.load('q'),
            AcrylicPaint = __DARKLUA_BUNDLE_MODULES.load('r'),
        }

        function Acrylic.init()
            local baseEffect = Instance.new('DepthOfFieldEffect')

            baseEffect.FarIntensity = 0
            baseEffect.InFocusRadius = 0.1
            baseEffect.NearIntensity = 1

            local depthOfFieldDefaults = {}

            function Acrylic.Enable()
                for _, effect in pairs(depthOfFieldDefaults)do
                    effect.Enabled = false
                end

                baseEffect.Parent = cloneref(game:GetService('Lighting'))
            end
            function Acrylic.Disable()
                for _, effect in pairs(depthOfFieldDefaults)do
                    effect.Enabled = effect.enabled
                end

                baseEffect.Parent = nil
            end

            local registerDefaults = function()
                local register = function(object)
                    if object:IsA('DepthOfFieldEffect') then
                        depthOfFieldDefaults[object] = {
                            enabled = object.Enabled,
                        }
                    end
                end

                for _, child in pairs(cloneref(game:GetService('Lighting')):GetChildren())do
                    register(child)
                end

                if cloneref(game:GetService('Workspace')).CurrentCamera then
                    for _, child in pairs(cloneref(game:GetService('Workspace')).CurrentCamera:GetChildren())do
                        register(child)
                    end
                end
            end

            registerDefaults()
            Acrylic.Enable()
        end

        return Acrylic
    end
    function __DARKLUA_BUNDLE_MODULES.t()
        local PopupModule = {}
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local _ = Creator.Tween

        function PopupModule.new(PopupConfig)
            local Popup = {
                Title = PopupConfig.Title or 'Dialog',
                Content = PopupConfig.Content,
                Icon = PopupConfig.Icon,
                IconThemed = PopupConfig.IconThemed,
                Thumbnail = PopupConfig.Thumbnail,
                Buttons = PopupConfig.Buttons,
                IconSize = 22,
            }
            local DialogInit = __DARKLUA_BUNDLE_MODULES.load('n').Init(nil, PopupConfig.WindUI.ScreenGui.Popups)
            local Dialog = DialogInit.Create(true, 'Popup')
            local ThumbnailSize = 200
            local UISize = 430

            if Popup.Thumbnail and Popup.Thumbnail.Image then
                UISize = 430 + (ThumbnailSize / 2)
            end

            Dialog.UIElements.Main.AutomaticSize = 'Y'
            Dialog.UIElements.Main.Size = UDim2.new(0, UISize, 0, 0)

            local IconFrame

            if Popup.Icon then
                IconFrame = Creator.Image(Popup.Icon, Popup.Title .. ':' .. Popup.Icon, 0, PopupConfig.WindUI.Window, 'Popup', true, PopupConfig.IconThemed, 'PopupIcon')
                IconFrame.Size = UDim2.new(0, Popup.IconSize, 0, Popup.IconSize)
                IconFrame.LayoutOrder = -1
            end

            local Title = New('TextLabel', {
                AutomaticSize = 'Y',
                BackgroundTransparency = 1,
                Text = Popup.Title,
                TextXAlignment = 'Left',
                FontFace = Font.new(Creator.Font, Enum.FontWeight.SemiBold),
                ThemeTag = {
                    TextColor3 = 'PopupTitle',
                },
                TextSize = 20,
                TextWrapped = true,
                Size = UDim2.new(1, IconFrame and -Popup.IconSize - 14 or 0, 0, 0),
            })
            local IconAndTitleContainer = New('Frame', {
                BackgroundTransparency = 1,
                AutomaticSize = 'XY',
            }, {
                New('UIListLayout', {
                    Padding = UDim.new(0, 14),
                    FillDirection = 'Horizontal',
                    VerticalAlignment = 'Center',
                }),
                IconFrame,
                Title,
            })
            local TitleContainer = New('Frame', {
                AutomaticSize = 'Y',
                Size = UDim2.new(1, 0, 0, 0),
                BackgroundTransparency = 1,
            }, {IconAndTitleContainer})
            local NoteText

            if Popup.Content and Popup.Content ~= '' then
                NoteText = New('TextLabel', {
                    Size = UDim2.new(1, 0, 0, 0),
                    AutomaticSize = 'Y',
                    FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                    TextXAlignment = 'Left',
                    Text = Popup.Content,
                    TextSize = 18,
                    TextTransparency = 0.2,
                    ThemeTag = {
                        TextColor3 = 'PopupContent',
                    },
                    BackgroundTransparency = 1,
                    RichText = true,
                    TextWrapped = true,
                })
            end

            local ButtonsContainer = New('Frame', {
                Size = UDim2.new(1, 0, 0, 42),
                BackgroundTransparency = 1,
            }, {
                New('UIListLayout', {
                    Padding = UDim.new(0, 9),
                    FillDirection = 'Horizontal',
                    HorizontalAlignment = 'Right',
                }),
            })
            local ThumbnailFrame

            if Popup.Thumbnail and Popup.Thumbnail.Image then
                local ThumbnailTitle

                if Popup.Thumbnail.Title then
                    ThumbnailTitle = New('TextLabel', {
                        Text = Popup.Thumbnail.Title,
                        ThemeTag = {
                            TextColor3 = 'Text',
                        },
                        TextSize = 18,
                        FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                        BackgroundTransparency = 1,
                        AutomaticSize = 'XY',
                        AnchorPoint = Vector2.new(0.5, 0.5),
                        Position = UDim2.new(0.5, 0, 0.5, 0),
                    })
                end

                ThumbnailFrame = New('ImageLabel', {
                    Image = Popup.Thumbnail.Image,
                    BackgroundTransparency = 1,
                    Size = UDim2.new(0, ThumbnailSize, 1, 0),
                    Parent = Dialog.UIElements.Main,
                    ScaleType = 'Crop',
                }, {
                    ThumbnailTitle,
                    New('UICorner', {
                        CornerRadius = UDim.new(0, 0),
                    }),
                })
            end

            New('Frame', {
                Size = UDim2.new(1, ThumbnailFrame and -ThumbnailSize or 0, 1, 0),
                Position = UDim2.new(0, ThumbnailFrame and ThumbnailSize or 0, 0, 0),
                BackgroundTransparency = 1,
                Parent = Dialog.UIElements.Main,
            }, {
                New('Frame', {
                    Size = UDim2.new(1, 0, 1, 0),
                    BackgroundTransparency = 1,
                }, {
                    New('UIListLayout', {
                        Padding = UDim.new(0, 18),
                        FillDirection = 'Vertical',
                    }),
                    TitleContainer,
                    NoteText,
                    ButtonsContainer,
                    New('UIPadding', {
                        PaddingTop = UDim.new(0, 16),
                        PaddingLeft = UDim.new(0, 16),
                        PaddingRight = UDim.new(0, 16),
                        PaddingBottom = UDim.new(0, 16),
                    }),
                }),
            })

            local CreateButton = __DARKLUA_BUNDLE_MODULES.load('l').New

            for _, values in next, Popup.Buttons do
                CreateButton(values.Title, values.Icon, values.Callback, values.Variant, ButtonsContainer, Dialog)
            end

            Dialog:Open()

            return Popup
        end

        return PopupModule
    end
    function __DARKLUA_BUNDLE_MODULES.u()
        return function(WindUI)
            return {
                Dark = {
                    Name = 'Dark',
                    Accent = Color3.fromHex('#18181b'),
                    Dialog = Color3.fromHex('#161616'),
                    Outline = Color3.fromHex('#FFFFFF'),
                    Text = Color3.fromHex('#FFFFFF'),
                    Placeholder = Color3.fromHex('#7a7a7a'),
                    Background = Color3.fromHex('#101010'),
                    Button = Color3.fromHex('#52525b'),
                    Icon = Color3.fromHex('#a1a1aa'),
                    Toggle = Color3.fromHex('#33C759'),
                    Checkbox = Color3.fromHex('#0091ff'),
                },
                Light = {
                    Name = 'Light',
                    Accent = Color3.fromHex('#FFFFFF'),
                    Dialog = Color3.fromHex('#f4f4f5'),
                    Outline = Color3.fromHex('#09090b'),
                    Text = Color3.fromHex('#000000'),
                    Placeholder = Color3.fromHex('#555555'),
                    Background = Color3.fromHex('#e4e4e7'),
                    Button = Color3.fromHex('#18181b'),
                    Icon = Color3.fromHex('#52525b'),
                },
                Rose = {
                    Name = 'Rose',
                    Accent = Color3.fromHex('#be185d'),
                    Dialog = Color3.fromHex('#4c0519'),
                    Outline = Color3.fromHex('#fecdd3'),
                    Text = Color3.fromHex('#fdf2f8'),
                    Placeholder = Color3.fromHex('#d67aa6'),
                    Background = Color3.fromHex('#1f0308'),
                    Button = Color3.fromHex('#e11d48'),
                    Icon = Color3.fromHex('#fb7185'),
                },
                Plant = {
                    Name = 'Plant',
                    Accent = Color3.fromHex('#166534'),
                    Dialog = Color3.fromHex('#052e16'),
                    Outline = Color3.fromHex('#bbf7d0'),
                    Text = Color3.fromHex('#f0fdf4'),
                    Placeholder = Color3.fromHex('#4fbf7a'),
                    Background = Color3.fromHex('#0a1b0f'),
                    Button = Color3.fromHex('#16a34a'),
                    Icon = Color3.fromHex('#4ade80'),
                },
                Red = {
                    Name = 'Red',
                    Accent = Color3.fromHex('#991b1b'),
                    Dialog = Color3.fromHex('#450a0a'),
                    Outline = Color3.fromHex('#fecaca'),
                    Text = Color3.fromHex('#fef2f2'),
                    Placeholder = Color3.fromHex('#d95353'),
                    Background = Color3.fromHex('#1c0606'),
                    Button = Color3.fromHex('#dc2626'),
                    Icon = Color3.fromHex('#ef4444'),
                },
                Indigo = {
                    Name = 'Indigo',
                    Accent = Color3.fromHex('#3730a3'),
                    Dialog = Color3.fromHex('#1e1b4b'),
                    Outline = Color3.fromHex('#c7d2fe'),
                    Text = Color3.fromHex('#f1f5f9'),
                    Placeholder = Color3.fromHex('#7078d9'),
                    Background = Color3.fromHex('#0f0a2e'),
                    Button = Color3.fromHex('#4f46e5'),
                    Icon = Color3.fromHex('#6366f1'),
                },
                Sky = {
                    Name = 'Sky',
                    Accent = Color3.fromHex('#0369a1'),
                    Dialog = Color3.fromHex('#0c4a6e'),
                    Outline = Color3.fromHex('#bae6fd'),
                    Text = Color3.fromHex('#f0f9ff'),
                    Placeholder = Color3.fromHex('#4fb6d9'),
                    Background = Color3.fromHex('#041f2e'),
                    Button = Color3.fromHex('#0284c7'),
                    Icon = Color3.fromHex('#0ea5e9'),
                },
                Violet = {
                    Name = 'Violet',
                    Accent = Color3.fromHex('#6d28d9'),
                    Dialog = Color3.fromHex('#3c1361'),
                    Outline = Color3.fromHex('#ddd6fe'),
                    Text = Color3.fromHex('#faf5ff'),
                    Placeholder = Color3.fromHex('#8f7ee0'),
                    Background = Color3.fromHex('#1e0a3e'),
                    Button = Color3.fromHex('#7c3aed'),
                    Icon = Color3.fromHex('#8b5cf6'),
                },
                Amber = {
                    Name = 'Amber',
                    Accent = Color3.fromHex('#b45309'),
                    Dialog = Color3.fromHex('#451a03'),
                    Outline = Color3.fromHex('#fde68a'),
                    Text = Color3.fromHex('#fffbeb'),
                    Placeholder = Color3.fromHex('#d1a326'),
                    Background = Color3.fromHex('#1c1003'),
                    Button = Color3.fromHex('#d97706'),
                    Icon = Color3.fromHex('#f59e0b'),
                },
                Emerald = {
                    Name = 'Emerald',
                    Accent = Color3.fromHex('#047857'),
                    Dialog = Color3.fromHex('#022c22'),
                    Outline = Color3.fromHex('#a7f3d0'),
                    Text = Color3.fromHex('#ecfdf5'),
                    Placeholder = Color3.fromHex('#3fbf8f'),
                    Background = Color3.fromHex('#011411'),
                    Button = Color3.fromHex('#059669'),
                    Icon = Color3.fromHex('#10b981'),
                },
                Midnight = {
                    Name = 'Midnight',
                    Accent = Color3.fromHex('#1e3a8a'),
                    Dialog = Color3.fromHex('#0c1e42'),
                    Outline = Color3.fromHex('#bfdbfe'),
                    Text = Color3.fromHex('#dbeafe'),
                    Placeholder = Color3.fromHex('#2f74d1'),
                    Background = Color3.fromHex('#0a0f1e'),
                    Button = Color3.fromHex('#2563eb'),
                    Icon = Color3.fromHex('#3b82f6'),
                },
                Crimson = {
                    Name = 'Crimson',
                    Accent = Color3.fromHex('#b91c1c'),
                    Dialog = Color3.fromHex('#450a0a'),
                    Outline = Color3.fromHex('#fca5a5'),
                    Text = Color3.fromHex('#fef2f2'),
                    Placeholder = Color3.fromHex('#6f757b'),
                    Background = Color3.fromHex('#0c0404'),
                    Button = Color3.fromHex('#991b1b'),
                    Icon = Color3.fromHex('#dc2626'),
                },
                MonokaiPro = {
                    Name = 'Monokai Pro',
                    Accent = Color3.fromHex('#fc9867'),
                    Dialog = Color3.fromHex('#1e1e1e'),
                    Outline = Color3.fromHex('#78dce8'),
                    Text = Color3.fromHex('#fcfcfa'),
                    Placeholder = Color3.fromHex('#6f6f6f'),
                    Background = Color3.fromHex('#191622'),
                    Button = Color3.fromHex('#ab9df2'),
                    Icon = Color3.fromHex('#a9dc76'),
                },
                CottonCandy = {
                    Name = 'Cotton Candy',
                    Accent = Color3.fromHex('#ec4899'),
                    Dialog = Color3.fromHex('#2d1b3d'),
                    Outline = Color3.fromHex('#f9a8d4'),
                    Text = Color3.fromHex('#fdf2f8'),
                    Placeholder = Color3.fromHex('#8a5fd3'),
                    Background = Color3.fromHex('#1a0b2e'),
                    Button = Color3.fromHex('#d946ef'),
                    Icon = Color3.fromHex('#06b6d4'),
                },
                Rainbow = {
                    Name = 'Rainbow',
                    Accent = WindUI:Gradient({
                        ['0'] = {
                            Color = Color3.fromHex('#00ff41'),
                            Transparency = 0,
                        },
                        ['33'] = {
                            Color = Color3.fromHex('#00ffff'),
                            Transparency = 0,
                        },
                        ['66'] = {
                            Color = Color3.fromHex('#0080ff'),
                            Transparency = 0,
                        },
                        ['100'] = {
                            Color = Color3.fromHex('#8000ff'),
                            Transparency = 0,
                        },
                    }, {Rotation = 45}),
                    Dialog = WindUI:Gradient({
                        ['0'] = {
                            Color = Color3.fromHex('#ff0080'),
                            Transparency = 0,
                        },
                        ['25'] = {
                            Color = Color3.fromHex('#8000ff'),
                            Transparency = 0,
                        },
                        ['50'] = {
                            Color = Color3.fromHex('#0080ff'),
                            Transparency = 0,
                        },
                        ['75'] = {
                            Color = Color3.fromHex('#00ff80'),
                            Transparency = 0,
                        },
                        ['100'] = {
                            Color = Color3.fromHex('#ff8000'),
                            Transparency = 0,
                        },
                    }, {Rotation = 135}),
                    Outline = Color3.fromHex('#ffffff'),
                    Text = Color3.fromHex('#ffffff'),
                    Placeholder = Color3.fromHex('#00ff80'),
                    Background = WindUI:Gradient({
                        ['0'] = {
                            Color = Color3.fromHex('#ff0040'),
                            Transparency = 0,
                        },
                        ['20'] = {
                            Color = Color3.fromHex('#ff4000'),
                            Transparency = 0,
                        },
                        ['40'] = {
                            Color = Color3.fromHex('#ffff00'),
                            Transparency = 0,
                        },
                        ['60'] = {
                            Color = Color3.fromHex('#00ff40'),
                            Transparency = 0,
                        },
                        ['80'] = {
                            Color = Color3.fromHex('#0040ff'),
                            Transparency = 0,
                        },
                        ['100'] = {
                            Color = Color3.fromHex('#4000ff'),
                            Transparency = 0,
                        },
                    }, {Rotation = 90}),
                    Button = WindUI:Gradient({
                        ['0'] = {
                            Color = Color3.fromHex('#ff0080'),
                            Transparency = 0,
                        },
                        ['25'] = {
                            Color = Color3.fromHex('#ff8000'),
                            Transparency = 0,
                        },
                        ['50'] = {
                            Color = Color3.fromHex('#ffff00'),
                            Transparency = 0,
                        },
                        ['75'] = {
                            Color = Color3.fromHex('#80ff00'),
                            Transparency = 0,
                        },
                        ['100'] = {
                            Color = Color3.fromHex('#00ffff'),
                            Transparency = 0,
                        },
                    }, {Rotation = 60}),
                    Icon = Color3.fromHex('#ffffff'),
                },
            }
        end
    end
    function __DARKLUA_BUNDLE_MODULES.v()
        local Label = {}
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local _ = Creator.Tween

        function Label.New(Text, Icon, Parent, IsPlaceholder, Radius)
            local Radius = Radius or 10
            local IconLabelFrame

            if Icon and Icon ~= '' then
                IconLabelFrame = New('ImageLabel', {
                    Image = Creator.Icon(Icon)[1],
                    ImageRectSize = Creator.Icon(Icon)[2].ImageRectSize,
                    ImageRectOffset = Creator.Icon(Icon)[2].ImageRectPosition,
                    Size = UDim2.new(0, 21, 0, 21),
                    BackgroundTransparency = 1,
                    ThemeTag = {
                        ImageColor3 = 'Icon',
                    },
                })
            end

            local TextLabel = New('TextLabel', {
                BackgroundTransparency = 1,
                TextSize = 17,
                FontFace = Font.new(Creator.Font, Enum.FontWeight.Regular),
                Size = UDim2.new(1, IconLabelFrame and -29 or 0, 1, 0),
                TextXAlignment = 'Left',
                ThemeTag = {
                    TextColor3 = IsPlaceholder and 'Placeholder' or 'Text',
                },
                Text = Text,
            })
            local LabelFrame = New('TextButton', {
                Size = UDim2.new(1, 0, 0, 42),
                Parent = Parent,
                BackgroundTransparency = 1,
                Text = '',
            }, {
                New('Frame', {
                    Size = UDim2.new(1, 0, 1, 0),
                    BackgroundTransparency = 1,
                }, {
                    Creator.NewRoundFrame(Radius, 'Squircle', {
                        ThemeTag = {
                            ImageColor3 = 'Accent',
                        },
                        Size = UDim2.new(1, 0, 1, 0),
                        ImageTransparency = 0.97,
                    }),
                    Creator.NewRoundFrame(Radius, 'SquircleOutline', {
                        ThemeTag = {
                            ImageColor3 = 'Outline',
                        },
                        Size = UDim2.new(1, 0, 1, 0),
                        ImageTransparency = 0.95,
                    }, {
                        New('UIGradient', {
                            Rotation = 70,
                            Color = ColorSequence.new({
                                ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
                                ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 255, 255)),
                                ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255)),
                            }),
                            Transparency = NumberSequence.new({
                                NumberSequenceKeypoint.new(0, 0.1),
                                NumberSequenceKeypoint.new(0.5, 1),
                                NumberSequenceKeypoint.new(1, 0.1),
                            }),
                        }),
                    }),
                    Creator.NewRoundFrame(Radius, 'Squircle', {
                        Size = UDim2.new(1, 0, 1, 0),
                        Name = 'Frame',
                        ImageColor3 = Color3.new(1, 1, 1),
                        ImageTransparency = 0.95,
                    }, {
                        New('UIPadding', {
                            PaddingLeft = UDim.new(0, 12),
                            PaddingRight = UDim.new(0, 12),
                        }),
                        New('UIListLayout', {
                            FillDirection = 'Horizontal',
                            Padding = UDim.new(0, 8),
                            VerticalAlignment = 'Center',
                            HorizontalAlignment = 'Left',
                        }),
                        IconLabelFrame,
                        TextLabel,
                    }),
                }),
            })

            return LabelFrame
        end

        return Label
    end
    function __DARKLUA_BUNDLE_MODULES.w()
        local ScrollSlider = {}
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)
        local UserInputService = cloneref(game:GetService('UserInputService'))
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local _ = Creator.Tween

        function ScrollSlider.New(ScrollingFrame, Parent, Window, Thickness)
            local Slider = New('Frame', {
                Size = UDim2.new(0, Thickness, 1, 0),
                BackgroundTransparency = 1,
                Position = UDim2.new(1, 0, 0, 0),
                AnchorPoint = Vector2.new(1, 0),
                Parent = Parent,
                ZIndex = 999,
                Active = true,
            })
            local Thumb = Creator.NewRoundFrame(Thickness / 2, 'Squircle', {
                Size = UDim2.new(1, 0, 0, 0),
                ImageTransparency = 0.85,
                ThemeTag = {
                    ImageColor3 = 'Text',
                },
                Parent = Slider,
            })
            local Hitbox = New('Frame', {
                Size = UDim2.new(1, 12, 1, 12),
                Position = UDim2.new(0.5, 0, 0.5, 0),
                AnchorPoint = Vector2.new(0.5, 0.5),
                BackgroundTransparency = 1,
                Active = true,
                ZIndex = 999,
                Parent = Thumb,
            })
            local isDragging = false
            local dragOffset = 0
            local updateSliderSize = function()
                local container = ScrollingFrame
                local canvasSize = container.AbsoluteCanvasSize.Y
                local windowSize = container.AbsoluteWindowSize.Y

                if canvasSize <= windowSize then
                    Thumb.Visible = false

                    return
                end

                local visibleRatio = math.clamp(windowSize / canvasSize, 0.1, 1)

                Thumb.Size = UDim2.new(1, 0, visibleRatio, 0)
                Thumb.Visible = true
            end
            local updateScrollingFramePosition = function()
                local thumbPositionY = Thumb.Position.Y.Scale
                local canvasSize = ScrollingFrame.AbsoluteCanvasSize.Y
                local windowSize = ScrollingFrame.AbsoluteWindowSize.Y
                local maxScroll = math.max(canvasSize - windowSize, 0)

                if maxScroll <= 0 then
                    return
                end

                local maxThumbPos = math.max(1 - Thumb.Size.Y.Scale, 0)

                if maxThumbPos <= 0 then
                    return
                end

                local scrollRatio = thumbPositionY / maxThumbPos

                ScrollingFrame.CanvasPosition = Vector2.new(ScrollingFrame.CanvasPosition.X, scrollRatio * maxScroll)
            end
            local updateThumbPosition = function()
                if isDragging then
                    return
                end

                local canvasPosition = ScrollingFrame.CanvasPosition.Y
                local canvasSize = ScrollingFrame.AbsoluteCanvasSize.Y
                local windowSize = ScrollingFrame.AbsoluteWindowSize.Y
                local maxScroll = math.max(canvasSize - windowSize, 0)

                if maxScroll <= 0 then
                    Thumb.Position = UDim2.new(0, 0, 0, 0)

                    return
                end

                local scrollRatio = canvasPosition / maxScroll
                local maxThumbPos = math.max(1 - Thumb.Size.Y.Scale, 0)
                local newThumbPosition = math.clamp(scrollRatio * maxThumbPos, 0, maxThumbPos)

                Thumb.Position = UDim2.new(0, 0, newThumbPosition, 0)
            end

            Creator.AddSignal(Slider.InputBegan, function(input)
                if (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) then
                    local thumbTop = Thumb.AbsolutePosition.Y
                    local thumbBottom = thumbTop + Thumb.AbsoluteSize.Y

                    if not (input.Position.Y >= thumbTop and input.Position.Y <= thumbBottom) then
                        local sliderTop = Slider.AbsolutePosition.Y
                        local sliderHeight = Slider.AbsoluteSize.Y
                        local thumbHeight = Thumb.AbsoluteSize.Y
                        local targetY = input.Position.Y - sliderTop - thumbHeight / 2
                        local maxThumbPos = sliderHeight - thumbHeight
                        local newThumbPosScale = math.clamp(targetY / maxThumbPos, 0, 1 - Thumb.Size.Y.Scale)

                        Thumb.Position = UDim2.new(0, 0, newThumbPosScale, 0)

                        updateScrollingFramePosition()
                    end
                end
            end)
            Creator.AddSignal(Hitbox.InputBegan, function(input)
                if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                    isDragging = true
                    dragOffset = input.Position.Y - Thumb.AbsolutePosition.Y

                    local moveConnection
                    local releaseConnection

                    moveConnection = UserInputService.InputChanged:Connect(function(
                        changedInput
                    )
                        if changedInput.UserInputType == Enum.UserInputType.MouseMovement or changedInput.UserInputType == Enum.UserInputType.Touch then
                            local sliderTop = Slider.AbsolutePosition.Y
                            local sliderHeight = Slider.AbsoluteSize.Y
                            local thumbHeight = Thumb.AbsoluteSize.Y
                            local newY = changedInput.Position.Y - sliderTop - dragOffset
                            local maxThumbPos = sliderHeight - thumbHeight
                            local newThumbPosScale = math.clamp(newY / maxThumbPos, 0, 1 - Thumb.Size.Y.Scale)

                            Thumb.Position = UDim2.new(0, 0, newThumbPosScale, 0)

                            updateScrollingFramePosition()
                        end
                    end)
                    releaseConnection = UserInputService.InputEnded:Connect(function(
                        endInput
                    )
                        if endInput.UserInputType == Enum.UserInputType.MouseButton1 or endInput.UserInputType == Enum.UserInputType.Touch then
                            isDragging = false

                            if moveConnection then
                                moveConnection:Disconnect()
                            end
                            if releaseConnection then
                                releaseConnection:Disconnect()
                            end
                        end
                    end)
                end
            end)
            Creator.AddSignal(ScrollingFrame:GetPropertyChangedSignal('AbsoluteWindowSize'), function(
            )
                updateSliderSize()
                updateThumbPosition()
            end)
            Creator.AddSignal(ScrollingFrame:GetPropertyChangedSignal('AbsoluteCanvasSize'), function(
            )
                updateSliderSize()
                updateThumbPosition()
            end)
            Creator.AddSignal(ScrollingFrame:GetPropertyChangedSignal('CanvasPosition'), function(
            )
                if not isDragging then
                    updateThumbPosition()
                end
            end)
            updateSliderSize()
            updateThumbPosition()

            return Slider
        end

        return ScrollSlider
    end
    function __DARKLUA_BUNDLE_MODULES.x()
        local Tag = {}
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween
        local Color3ToHSB = function(color)
            local r, g, b = color.R, color.G, color.B
            local max = math.max(r, g, b)
            local min = math.min(r, g, b)
            local delta = max - min
            local h = 0

            if delta ~= 0 then
                if max == r then
                    h = (g - b) / delta % 6
                elseif max == g then
                    h = (b - r) / delta + 2
                else
                    h = (r - g) / delta + 4
                end

                h = h * 60
            else
                h = 0
            end

            local s = (max == 0) and 0 or (delta / max)
            local v = max

            return {
                h = math.floor(h + 0.5),
                s = s,
                b = v,
            }
        end
        local GetPerceivedBrightness = function(color)
            local r = color.R
            local g = color.G
            local b = color.B

            return 0.299 * r + 0.587 * g + 0.114 * b
        end
        local GetTextColorForHSB = function(color)
            local hsb = Color3ToHSB(color)
            local h, s, b = hsb.h, hsb.s, hsb.b

            if GetPerceivedBrightness(color) > 0.5 then
                return Color3.fromHSV(h / 360, 0, 0.05)
            else
                return Color3.fromHSV(h / 360, 0, 0.98)
            end
        end
        local GetAverageColor = function(gradient)
            local r, g, b = 0, 0, 0
            local keypoints = gradient.Color.Keypoints

            for _, k in ipairs(keypoints)do
                r = r + k.Value.R
                g = g + k.Value.G
                b = b + k.Value.B
            end

            local n = #keypoints

            return Color3.new(r / n, g / n, b / n)
        end

        function Tag:New(TagConfig, Parent)
            local TagModule = {
                Title = TagConfig.Title or 'Tag',
                Icon = TagConfig.Icon,
                Color = TagConfig.Color or Color3.fromHex('#315dff'),
                Radius = TagConfig.Radius or 999,
                TagFrame = nil,
                Height = 26,
                Padding = 10,
                TextSize = 14,
                IconSize = 16,
            }
            local TagIcon

            if TagModule.Icon then
                TagIcon = Creator.Image(TagModule.Icon, TagModule.Icon, 0, TagConfig.Window, 'Tag', false)
                TagIcon.Size = UDim2.new(0, TagModule.IconSize, 0, TagModule.IconSize)
                TagIcon.ImageLabel.ImageColor3 = typeof(TagModule.Color) == 'Color3' and GetTextColorForHSB(TagModule.Color) or nil
            end

            local TagTitle = New('TextLabel', {
                BackgroundTransparency = 1,
                AutomaticSize = 'XY',
                TextSize = TagModule.TextSize,
                FontFace = Font.new(Creator.Font, Enum.FontWeight.SemiBold),
                Text = TagModule.Title,
                TextColor3 = typeof(TagModule.Color) == 'Color3' and GetTextColorForHSB(TagModule.Color) or nil,
            })
            local BackgroundGradient

            if typeof(TagModule.Color) == 'table' then
                BackgroundGradient = New('UIGradient')

                for key, value in next, TagModule.Color do
                    BackgroundGradient[key] = value
                end

                TagTitle.TextColor3 = GetTextColorForHSB(GetAverageColor(BackgroundGradient))

                if TagIcon then
                    TagIcon.ImageLabel.ImageColor3 = GetTextColorForHSB(GetAverageColor(BackgroundGradient))
                end
            end

            local TagFrame = Creator.NewRoundFrame(TagModule.Radius, 'Squircle', {
                AutomaticSize = 'X',
                Size = UDim2.new(0, 0, 0, TagModule.Height),
                Parent = Parent,
                ImageColor3 = typeof(TagModule.Color) == 'Color3' and TagModule.Color or Color3.new(1, 1, 1),
            }, {
                BackgroundGradient,
                New('UIPadding', {
                    PaddingLeft = UDim.new(0, TagModule.Padding),
                    PaddingRight = UDim.new(0, TagModule.Padding),
                }),
                TagIcon,
                TagTitle,
                New('UIListLayout', {
                    FillDirection = 'Horizontal',
                    VerticalAlignment = 'Center',
                    Padding = UDim.new(0, TagModule.Padding / 1.5),
                }),
            })

            function TagModule:SetTitle(text)
                TagModule.Title = text
                TagTitle.Text = text
            end
            function TagModule:SetColor(color)
                TagModule.Color = color

                if typeof(color) == 'table' then
                    local avgColor = GetAverageColor(color)

                    Tween(TagTitle, 0.06, {
                        TextColor3 = GetTextColorForHSB(avgColor),
                    }):Play()

                    local gradient = TagFrame:FindFirstChildOfClass('UIGradient') or New('UIGradient', {Parent = TagFrame})

                    for k, v in next, color do
                        gradient[k] = v
                    end

                    Tween(TagFrame, 0.06, {
                        ImageColor3 = Color3.new(1, 1, 1),
                    }):Play()
                else
                    if BackgroundGradient then
                        BackgroundGradient:Destroy()
                    end

                    Tween(TagTitle, 0.06, {
                        TextColor3 = GetTextColorForHSB(color),
                    }):Play()

                    if TagIcon then
                        Tween(TagIcon.ImageLabel, 0.06, {
                            ImageColor3 = GetTextColorForHSB(color),
                        }):Play()
                    end

                    Tween(TagFrame, 0.06, {ImageColor3 = color}):Play()
                end
            end

            return TagModule
        end

        return Tag
    end
    function __DARKLUA_BUNDLE_MODULES.y()
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)
        local HttpService = cloneref(game:GetService('HttpService'))
        local Window
        local ConfigManager

        ConfigManager = {
            Folder = nil,
            Path = nil,
            Configs = {},
            Parser = {
                Colorpicker = {
                    Save = function(obj)
                        return {
                            __type = obj.__type,
                            value = obj.Default:ToHex(),
                            transparency = obj.Transparency or nil,
                        }
                    end,
                    Load = function(element, data)
                        if element and element.Update then
                            element:Update(Color3.fromHex(data.value), data.transparency or nil)
                        end
                    end,
                },
                Dropdown = {
                    Save = function(obj)
                        return {
                            __type = obj.__type,
                            value = obj.Value,
                        }
                    end,
                    Load = function(element, data)
                        if element and element.Select then
                            element:Select(data.value)
                        end
                    end,
                },
                Input = {
                    Save = function(obj)
                        return {
                            __type = obj.__type,
                            value = obj.Value,
                        }
                    end,
                    Load = function(element, data)
                        if element and element.Set then
                            element:Set(data.value)
                        end
                    end,
                },
                Keybind = {
                    Save = function(obj)
                        return {
                            __type = obj.__type,
                            value = obj.Value,
                        }
                    end,
                    Load = function(element, data)
                        if element and element.Set then
                            element:Set(data.value)
                        end
                    end,
                },
                Slider = {
                    Save = function(obj)
                        return {
                            __type = obj.__type,
                            value = obj.Value.Default,
                        }
                    end,
                    Load = function(element, data)
                        if element and element.Set then
                            element:Set(tonumber(data.value))
                        end
                    end,
                },
                Toggle = {
                    Save = function(obj)
                        return {
                            __type = obj.__type,
                            value = obj.Value,
                        }
                    end,
                    Load = function(element, data)
                        if element and element.Set then
                            element:Set(data.value)
                        end
                    end,
                },
            },
        }

        function ConfigManager:Init(WindowTable)
            if not WindowTable.Folder then
                warn('[ WindUI.ConfigManager ] Window.Folder is not specified.')

                return false
            end

            Window = WindowTable
            ConfigManager.Folder = Window.Folder
            ConfigManager.Path = 'WindUI/' .. tostring(ConfigManager.Folder) .. '/config/'

            if not isfolder('WindUI/' .. ConfigManager.Folder) then
                makefolder('WindUI/' .. ConfigManager.Folder)

                if not isfolder('WindUI/' .. ConfigManager.Folder .. '/config/') then
                    makefolder('WindUI/' .. ConfigManager.Folder .. '/config/')
                end
            end

            local files = ConfigManager:AllConfigs()

            for _, f in next, files do
                if isfile and readfile and isfile(f .. '.json') then
                    ConfigManager.Configs[f] = readfile(f .. '.json')
                end
            end

            return ConfigManager
        end
        function ConfigManager:CreateConfig(configFilename, autoload)
            local ConfigModule = {
                Path = ConfigManager.Path .. configFilename .. '.json',
                Elements = {},
                CustomData = {},
                AutoLoad = autoload or false,
                Version = 1.2,
            }

            if not configFilename then
                return false, 'No config file is selected'
            end

            function ConfigModule:SetAsCurrent()
                Window:SetCurrentConfig(ConfigModule)
            end
            function ConfigModule:Register(Name, Element)
                ConfigModule.Elements[Name] = Element
            end
            function ConfigModule:Set(key, value)
                ConfigModule.CustomData[key] = value
            end
            function ConfigModule:Get(key)
                return ConfigModule.CustomData[key]
            end
            function ConfigModule:SetAutoLoad(Value)
                ConfigModule.AutoLoad = Value
            end
            function ConfigModule:Save()
                if Window.PendingFlags then
                    for flag, element in next, Window.PendingFlags do
                        ConfigModule:Register(flag, element)
                    end
                end

                local saveData = {
                    __version = ConfigModule.Version,
                    __elements = {},
                    __autoload = ConfigModule.AutoLoad,
                    __custom = ConfigModule.CustomData,
                }

                for name, element in next, ConfigModule.Elements do
                    if ConfigManager.Parser[element.__type] then
                        saveData.__elements[tostring(name)] = ConfigManager.Parser[element.__type].Save(element)
                    end
                end

                local jsonData = HttpService:JSONEncode(saveData)

                if writefile then
                    writefile(ConfigModule.Path, jsonData)
                end

                return saveData
            end
            function ConfigModule:Load()
                if isfile and not isfile(ConfigModule.Path) then
                    return false, 'Config file does not exist'
                end

                local success, loadData = pcall(function()
                    local readfile = readfile or function()
                        warn(
[[[ WindUI.ConfigManager ] The config system doesn't work in the studio.]])

                        return nil
                    end

                    return HttpService:JSONDecode(readfile(ConfigModule.Path))
                end)

                if not success then
                    return false, 'Failed to parse config file'
                end
                if not loadData.__version then
                    local migratedData = {
                        __version = ConfigModule.Version,
                        __elements = loadData,
                        __custom = {},
                    }

                    loadData = migratedData
                end
                if Window.PendingFlags then
                    for flag, element in next, Window.PendingFlags do
                        ConfigModule:Register(flag, element)
                    end
                end

                for name, data in next, (loadData.__elements or {})do
                    if ConfigModule.Elements[name] and ConfigManager.Parser[data.__type] then
                        task.spawn(function()
                            ConfigManager.Parser[data.__type].Load(ConfigModule.Elements[name], data)
                        end)
                    end
                end

                ConfigModule.CustomData = loadData.__custom or {}

                return ConfigModule.CustomData
            end
            function ConfigModule:Delete()
                if not delfile then
                    return false, 'delfile function is not available'
                end
                if not isfile(ConfigModule.Path) then
                    return false, 'Config file does not exist'
                end

                local success, err = pcall(function()
                    delfile(ConfigModule.Path)
                end)

                if not success then
                    return false, 'Failed to delete config file: ' .. tostring(err)
                end

                ConfigManager.Configs[configFilename] = nil

                if Window.CurrentConfig == ConfigModule then
                    Window.CurrentConfig = nil
                end

                return true, 'Config deleted successfully'
            end
            function ConfigModule:GetData()
                return {
                    elements = ConfigModule.Elements,
                    custom = ConfigModule.CustomData,
                    autoload = ConfigModule.AutoLoad,
                }
            end

            if isfile(ConfigModule.Path) then
                local success, configData = pcall(function()
                    return HttpService:JSONDecode(readfile(ConfigModule.Path))
                end)

                if success and configData and configData.__autoload then
                    ConfigModule.AutoLoad = true

                    task.spawn(function()
                        task.wait(0.5)

                        local success, result = pcall(function()
                            return ConfigModule:Load()
                        end)

                        if success then
                            if Window.Debug then
                                print('[ WindUI.ConfigManager ] AutoLoaded config: ' .. configFilename)
                            end
                        else
                            warn('[ WindUI.ConfigManager ] Failed to AutoLoad config: ' .. configFilename .. ' - ' .. tostring(result))
                        end
                    end)
                end
            end

            ConfigModule:SetAsCurrent()

            ConfigManager.Configs[configFilename] = ConfigModule

            return ConfigModule
        end
        function ConfigManager:Config(configFilename, autoload)
            return ConfigManager:CreateConfig(configFilename, autoload)
        end
        function ConfigManager:GetAutoLoadConfigs()
            local autoloadConfigs = {}

            for configName, configModule in pairs(ConfigManager.Configs)do
                if configModule.AutoLoad then
                    table.insert(autoloadConfigs, configName)
                end
            end

            return autoloadConfigs
        end
        function ConfigManager:DeleteConfig(configName)
            if not delfile then
                return false, 'delfile function is not available'
            end

            local configPath = ConfigManager.Path .. configName .. '.json'

            if not isfile(configPath) then
                return false, 'Config file does not exist'
            end

            local success, err = pcall(function()
                delfile(configPath)
            end)

            if not success then
                return false, 'Failed to delete config file: ' .. tostring(err)
            end

            ConfigManager.Configs[configName] = nil

            if Window.CurrentConfig and Window.CurrentConfig.Path == configPath then
                Window.CurrentConfig = nil
            end

            return true, 'Config deleted successfully'
        end
        function ConfigManager:AllConfigs()
            if not listfiles then
                return {}
            end

            local files = {}

            if not isfolder(ConfigManager.Path) then
                makefolder(ConfigManager.Path)

                return files
            end

            for _, file in next, listfiles(ConfigManager.Path)do
                local name = file:match('([^\\/]+)%.json$')

                if name then
                    table.insert(files, name)
                end
            end

            return files
        end
        function ConfigManager:GetConfig(configName)
            return ConfigManager.Configs[configName]
        end

        return ConfigManager
    end
    function __DARKLUA_BUNDLE_MODULES.z()
        local OpenButton = {}
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)

        cloneref(game:GetService('UserInputService'))

        function OpenButton.New(Window)
            local OpenButtonMain = {Button = nil}
            local Icon
            local Title = New('TextLabel', {
                Text = Window.Title,
                TextSize = 17,
                FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                BackgroundTransparency = 1,
                AutomaticSize = 'XY',
            })
            local Drag = New('Frame', {
                Size = UDim2.new(0, 36, 0, 36),
                BackgroundTransparency = 1,
                Name = 'Drag',
            }, {
                New('ImageLabel', {
                    Image = Creator.Icon('move')[1],
                    ImageRectOffset = Creator.Icon('move')[2].ImageRectPosition,
                    ImageRectSize = Creator.Icon('move')[2].ImageRectSize,
                    Size = UDim2.new(0, 18, 0, 18),
                    BackgroundTransparency = 1,
                    Position = UDim2.new(0.5, 0, 0.5, 0),
                    AnchorPoint = Vector2.new(0.5, 0.5),
                    ThemeTag = {
                        ImageColor3 = 'Icon',
                    },
                    ImageTransparency = 0.3,
                }),
            })
            local Divider = New('Frame', {
                Size = UDim2.new(0, 1, 1, 0),
                Position = UDim2.new(0, 36, 0.5, 0),
                AnchorPoint = Vector2.new(0, 0.5),
                BackgroundColor3 = Color3.new(1, 1, 1),
                BackgroundTransparency = 0.9,
            })
            local Container = New('Frame', {
                Size = UDim2.new(0, 0, 0, 0),
                Position = UDim2.new(0.5, 0, 0, 28),
                AnchorPoint = Vector2.new(0.5, 0.5),
                Parent = Window.Parent,
                BackgroundTransparency = 1,
                Active = true,
                Visible = false,
            })
            local Button = New('TextButton', {
                Size = UDim2.new(0, 0, 0, 44),
                AutomaticSize = 'X',
                Parent = Container,
                Active = false,
                BackgroundTransparency = 0.25,
                ZIndex = 99,
                BackgroundColor3 = Color3.new(0, 0, 0),
            }, {
                New('UIScale', {Scale = 1}),
                New('UICorner', {
                    CornerRadius = UDim.new(1, 0),
                }),
                New('UIStroke', {
                    Thickness = 1,
                    ApplyStrokeMode = 'Border',
                    Color = Color3.new(1, 1, 1),
                    Transparency = 0,
                }, {
                    New('UIGradient', {
                        Color = ColorSequence.new(Color3.fromHex('40c9ff'), Color3.fromHex('e81cff')),
                    }),
                }),
                Drag,
                Divider,
                New('UIListLayout', {
                    Padding = UDim.new(0, 4),
                    FillDirection = 'Horizontal',
                    VerticalAlignment = 'Center',
                }),
                New('TextButton', {
                    AutomaticSize = 'XY',
                    Active = true,
                    BackgroundTransparency = 1,
                    Size = UDim2.new(0, 0, 0, 36),
                    BackgroundColor3 = Color3.new(1, 1, 1),
                }, {
                    New('UICorner', {
                        CornerRadius = UDim.new(1, -4),
                    }),
                    Icon,
                    New('UIListLayout', {
                        Padding = UDim.new(0, Window.UIPadding),
                        FillDirection = 'Horizontal',
                        VerticalAlignment = 'Center',
                    }),
                    Title,
                    New('UIPadding', {
                        PaddingLeft = UDim.new(0, 11),
                        PaddingRight = UDim.new(0, 11),
                    }),
                }),
                New('UIPadding', {
                    PaddingLeft = UDim.new(0, 4),
                    PaddingRight = UDim.new(0, 4),
                }),
            })

            OpenButtonMain.Button = Button

            function OpenButtonMain:SetIcon(newIcon)
                if Icon then
                    Icon:Destroy()
                end
                if newIcon then
                    Icon = Creator.Image(newIcon, Window.Title, 0, Window.Folder, 'OpenButton', true, Window.IconThemed)
                    Icon.Size = UDim2.new(0, 22, 0, 22)
                    Icon.LayoutOrder = -1
                    Icon.Parent = OpenButtonMain.Button.TextButton
                end
            end

            if Window.Icon then
                OpenButtonMain:SetIcon(Window.Icon)
            end

            Creator.AddSignal(Button:GetPropertyChangedSignal('AbsoluteSize'), function(
            )
                Container.Size = UDim2.new(0, Button.AbsoluteSize.X, 0, Button.AbsoluteSize.Y)
            end)
            Creator.AddSignal(Button.TextButton.MouseEnter, function()
                Tween(Button.TextButton, 0.1, {BackgroundTransparency = 0.93}):Play()
            end)
            Creator.AddSignal(Button.TextButton.MouseLeave, function()
                Tween(Button.TextButton, 0.1, {BackgroundTransparency = 1}):Play()
            end)

            local DragModule = Creator.Drag(Container)

            function OpenButtonMain:Visible(v)
                Container.Visible = v
            end
            function OpenButtonMain:Edit(OpenButtonConfig)
                local OpenButtonModule = {
                    Title = OpenButtonConfig.Title,
                    Icon = OpenButtonConfig.Icon,
                    Enabled = OpenButtonConfig.Enabled,
                    Position = OpenButtonConfig.Position,
                    OnlyIcon = OpenButtonConfig.OnlyIcon or false,
                    Draggable = OpenButtonConfig.Draggable or nil,
                    OnlyMobile = OpenButtonConfig.OnlyMobile,
                    CornerRadius = OpenButtonConfig.CornerRadius or UDim.new(1, 0),
                    StrokeThickness = OpenButtonConfig.StrokeThickness or 2,
                    Color = OpenButtonConfig.Color or ColorSequence.new(Color3.fromHex('40c9ff'), Color3.fromHex('e81cff')),
                }

                if OpenButtonModule.Enabled == false then
                    Window.IsOpenButtonEnabled = false
                end
                if OpenButtonModule.OnlyMobile ~= false then
                    OpenButtonModule.OnlyMobile = true
                else
                    Window.IsPC = false
                end
                if OpenButtonModule.Draggable == false and Drag and Divider then
                    Drag.Visible = OpenButtonModule.Draggable
                    Divider.Visible = OpenButtonModule.Draggable

                    if DragModule then
                        DragModule:Set(OpenButtonModule.Draggable)
                    end
                end
                if OpenButtonModule.Position and Container then
                    Container.Position = OpenButtonModule.Position
                end
                if OpenButtonModule.OnlyIcon == true and Title then
                    Title.Visible = false
                    Button.TextButton.UIPadding.PaddingLeft = UDim.new(0, 7)
                    Button.TextButton.UIPadding.PaddingRight = UDim.new(0, 7)
                elseif OpenButtonModule.OnlyIcon == false then
                    Title.Visible = true
                    Button.TextButton.UIPadding.PaddingLeft = UDim.new(0, 11)
                    Button.TextButton.UIPadding.PaddingRight = UDim.new(0, 11)
                end
                if Title then
                    if OpenButtonModule.Title then
                        Title.Text = OpenButtonModule.Title

                        Creator:ChangeTranslationKey(Title, OpenButtonModule.Title)
                    elseif OpenButtonModule.Title == nil then
                    end
                end
                if OpenButtonModule.Icon then
                    OpenButtonMain:SetIcon(OpenButtonModule.Icon)
                end

                Button.UIStroke.UIGradient.Color = OpenButtonModule.Color

                if Glow then
                    Glow.UIGradient.Color = OpenButtonModule.Color
                end

                Button.UICorner.CornerRadius = OpenButtonModule.CornerRadius
                Button.TextButton.UICorner.CornerRadius = UDim.new(OpenButtonModule.CornerRadius.Scale, OpenButtonModule.CornerRadius.Offset - 4)
                Button.UIStroke.Thickness = OpenButtonModule.StrokeThickness
            end

            return OpenButtonMain
        end

        return OpenButton
    end
    function __DARKLUA_BUNDLE_MODULES.A()
        local ToolTip = {}
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween

        function ToolTip.New(Title, Parent)
            local ToolTipModule = {
                Container = nil,
                ToolTipSize = 16,
            }
            local ToolTipTitle = New('TextLabel', {
                AutomaticSize = 'XY',
                TextWrapped = true,
                BackgroundTransparency = 1,
                FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                Text = Title,
                TextSize = 17,
                TextTransparency = 1,
                ThemeTag = {
                    TextColor3 = 'Text',
                },
            })
            local UIScale = New('UIScale', {Scale = 0.9})
            local Container = New('Frame', {
                AnchorPoint = Vector2.new(0.5, 0),
                AutomaticSize = 'XY',
                BackgroundTransparency = 1,
                Parent = Parent,
                Visible = false,
            }, {
                New('UISizeConstraint', {
                    MaxSize = Vector2.new(400, math.huge),
                }),
                New('Frame', {
                    AutomaticSize = 'XY',
                    BackgroundTransparency = 1,
                    LayoutOrder = 99,
                    Visible = false,
                }, {
                    New('ImageLabel', {
                        Size = UDim2.new(0, ToolTipModule.ToolTipSize, 0, ToolTipModule.ToolTipSize / 2),
                        BackgroundTransparency = 1,
                        Rotation = 180,
                        Image = 'rbxassetid://89524607682719',
                        ThemeTag = {
                            ImageColor3 = 'Accent',
                        },
                    }, {
                        New('ImageLabel', {
                            Size = UDim2.new(0, ToolTipModule.ToolTipSize, 0, ToolTipModule.ToolTipSize / 2),
                            BackgroundTransparency = 1,
                            LayoutOrder = 99,
                            ImageTransparency = 0.9,
                            Image = 'rbxassetid://89524607682719',
                            ThemeTag = {
                                ImageColor3 = 'Text',
                            },
                        }),
                    }),
                }),
                Creator.NewRoundFrame(14, 'Squircle', {
                    AutomaticSize = 'XY',
                    ThemeTag = {
                        ImageColor3 = 'Accent',
                    },
                    ImageTransparency = 1,
                    Name = 'Background',
                }, {
                    New('Frame', {
                        ThemeTag = {
                            BackgroundColor3 = 'Text',
                        },
                        AutomaticSize = 'XY',
                        BackgroundTransparency = 1,
                    }, {
                        New('UICorner', {
                            CornerRadius = UDim.new(0, 16),
                        }),
                        New('UIListLayout', {
                            Padding = UDim.new(0, 12),
                            FillDirection = 'Horizontal',
                            VerticalAlignment = 'Center',
                        }),
                        ToolTipTitle,
                        New('UIPadding', {
                            PaddingTop = UDim.new(0, 12),
                            PaddingLeft = UDim.new(0, 12),
                            PaddingRight = UDim.new(0, 12),
                            PaddingBottom = UDim.new(0, 12),
                        }),
                    }),
                }),
                UIScale,
                New('UIListLayout', {
                    Padding = UDim.new(0, 0),
                    FillDirection = 'Vertical',
                    VerticalAlignment = 'Center',
                    HorizontalAlignment = 'Center',
                }),
            })

            ToolTipModule.Container = Container

            function ToolTipModule:Open()
                Container.Visible = true

                Tween(Container.Background, 0.2, {ImageTransparency = 0}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                Tween(ToolTipTitle, 0.2, {TextTransparency = 0}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                Tween(UIScale, 0.18, {Scale = 1}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
            end
            function ToolTipModule:Close()
                Tween(Container.Background, 0.3, {ImageTransparency = 1}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                Tween(ToolTipTitle, 0.3, {TextTransparency = 1}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                Tween(UIScale, 0.35, {Scale = 0.9}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                task.wait(0.35)

                Container.Visible = false

                Container:Destroy()
            end

            return ToolTipModule
        end

        return ToolTip
    end
    function __DARKLUA_BUNDLE_MODULES.B()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local NewRoundFrame = Creator.NewRoundFrame
        local Tween = Creator.Tween
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)

        cloneref(game:GetService('UserInputService'))

        local Color3ToHSB = function(color)
            local r, g, b = color.R, color.G, color.B
            local max = math.max(r, g, b)
            local min = math.min(r, g, b)
            local delta = max - min
            local h = 0

            if delta ~= 0 then
                if max == r then
                    h = (g - b) / delta % 6
                elseif max == g then
                    h = (b - r) / delta + 2
                else
                    h = (r - g) / delta + 4
                end

                h = h * 60
            else
                h = 0
            end

            local s = (max == 0) and 0 or (delta / max)
            local v = max

            return {
                h = math.floor(h + 0.5),
                s = s,
                b = v,
            }
        end
        local GetPerceivedBrightness = function(color)
            local r = color.R
            local g = color.G
            local b = color.B

            return 0.299 * r + 0.587 * g + 0.114 * b
        end
        local GetTextColorForHSB = function(color)
            local hsb = Color3ToHSB(color)
            local h, s, b = hsb.h, hsb.s, hsb.b

            if GetPerceivedBrightness(color) > 0.5 then
                return Color3.fromHSV(h / 360, 0, 0.05)
            else
                return Color3.fromHSV(h / 360, 0, 0.98)
            end
        end
        local getElementPosition = function(elements, targetIndex)
            if type(targetIndex) ~= 'number' or targetIndex ~= math.floor(targetIndex) then
                return nil, 1
            end

            local maxIndex = #elements

            if maxIndex == 0 or targetIndex < 1 or targetIndex > maxIndex then
                return nil, 2
            end

            local isDelimiter = function(el)
                if el == nil then
                    return true
                end

                local t = el.__type

                return t == 'Divider' or t == 'Space' or t == 'Section' or t == 'Code'
            end

            if isDelimiter(elements[targetIndex]) then
                return nil, 3
            end

            local calculate = function(pos, size)
                if size == 1 then
                    return 'Squircle'
                end
                if pos == 1 then
                    return 'Squircle-TL-TR'
                end
                if pos == size then
                    return 'Squircle-BL-BR'
                end

                return 'Square'
            end
            local groupStart = 1
            local groupCount = 0

            for i = 1, maxIndex do
                local el = elements[i]

                if isDelimiter(el) then
                    if targetIndex >= groupStart and targetIndex <= i - 1 then
                        local pos = targetIndex - groupStart + 1

                        return calculate(pos, groupCount)
                    end

                    groupStart = i + 1
                    groupCount = 0
                else
                    groupCount = groupCount + 1
                end
            end

            if targetIndex >= groupStart and targetIndex <= maxIndex then
                local pos = targetIndex - groupStart + 1

                return calculate(pos, groupCount)
            end

            return nil, 4
        end

        return function(Config)
            local Element = {
                Title = Config.Title,
                Desc = Config.Desc or nil,
                Hover = Config.Hover,
                Thumbnail = Config.Thumbnail,
                ThumbnailSize = Config.ThumbnailSize or 80,
                Image = Config.Image,
                IconThemed = Config.IconThemed or false,
                ImageSize = Config.ImageSize or 30,
                Color = Config.Color,
                Scalable = Config.Scalable,
                Parent = Config.Parent,
                Justify = Config.Justify or 'Between',
                UIPadding = Config.Window.ElementConfig.UIPadding,
                UICorner = Config.Window.ElementConfig.UICorner,
                UIElements = {},
                Index = Config.Index,
            }
            local ImageSize = Element.ImageSize
            local ThumbnailSize = Element.ThumbnailSize
            local CanHover = true
            local IconOffset = 0
            local ThumbnailFrame
            local ImageFrame

            if Element.Thumbnail then
                ThumbnailFrame = Creator.Image(Element.Thumbnail, Element.Title, Config.Window.NewElements and Element.UICorner - 11 or (Element.UICorner - 4), Config.Window.Folder, 'Thumbnail', false, Element.IconThemed)
                ThumbnailFrame.Size = UDim2.new(1, 0, 0, ThumbnailSize)
            end
            if Element.Image then
                ImageFrame = Creator.Image(Element.Image, Element.Title, Config.Window.NewElements and Element.UICorner - 11 or (Element.UICorner - 4), Config.Window.Folder, 'Image', Element.IconThemed, not Element.Color and true or false, 'ElementIcon')

                if typeof(Element.Color) == 'string' then
                    ImageFrame.ImageLabel.ImageColor3 = GetTextColorForHSB(Color3.fromHex(Creator.Colors[Element.Color]))
                elseif typeof(Element.Color) == 'Color3' then
                    ImageFrame.ImageLabel.ImageColor3 = GetTextColorForHSB(Element.Color)
                end

                ImageFrame.Size = UDim2.new(0, ImageSize, 0, ImageSize)
                IconOffset = ImageSize
            end

            local CreateText = function(Title, Type)
                local TextColor = typeof(Element.Color) == 'string' and GetTextColorForHSB(Color3.fromHex(Creator.Colors[Element.Color])) or typeof(Element.Color) == 'Color3' and GetTextColorForHSB(Element.Color)

                return New('TextLabel', {
                    BackgroundTransparency = 1,
                    Text = Title or '',
                    TextSize = Type == 'Desc' and 15 or 17,
                    TextXAlignment = 'Left',
                    ThemeTag = {
                        TextColor3 = not Element.Color and ('Element' .. Type) or nil,
                    },
                    TextColor3 = Element.Color and TextColor or nil,
                    TextTransparency = Type == 'Desc' and 0.3 or 0,
                    TextWrapped = true,
                    Size = UDim2.new(Element.Justify == 'Between' and 1 or 0, 0, 0, 0),
                    AutomaticSize = Element.Justify == 'Between' and 'Y' or 'XY',
                    FontFace = Font.new(Creator.Font, Type == 'Desc' and Enum.FontWeight.Medium or Enum.FontWeight.SemiBold),
                })
            end
            local Title = CreateText(Element.Title, 'Title')
            local Desc = CreateText(Element.Desc, 'Desc')

            if not Element.Desc or Element.Desc == '' then
                Desc.Visible = false
            end

            Element.UIElements.Container = New('Frame', {
                Size = UDim2.new(1, 0, 1, 0),
                AutomaticSize = 'Y',
                BackgroundTransparency = 1,
            }, {
                New('UIListLayout', {
                    Padding = UDim.new(0, Element.UIPadding),
                    FillDirection = 'Vertical',
                    VerticalAlignment = 'Center',
                    HorizontalAlignment = Element.Justify == 'Between' and 'Left' or 'Center',
                }),
                ThumbnailFrame,
                New('Frame', {
                    Size = UDim2.new(Element.Justify == 'Between' and 1 or 0, Element.Justify == 'Between' and 
-Config.TextOffset or 0, 0, 0),
                    AutomaticSize = Element.Justify == 'Between' and 'Y' or 'XY',
                    BackgroundTransparency = 1,
                    Name = 'TitleFrame',
                }, {
                    New('UIListLayout', {
                        Padding = UDim.new(0, Element.UIPadding),
                        FillDirection = 'Horizontal',
                        VerticalAlignment = Config.Window.NewElements and (Element.Justify == 'Between' and 'Top' or 'Center') or 'Center',
                        HorizontalAlignment = Element.Justify ~= 'Between' and Element.Justify or 'Center',
                    }),
                    ImageFrame,
                    New('Frame', {
                        BackgroundTransparency = 1,
                        AutomaticSize = Element.Justify == 'Between' and 'Y' or 'XY',
                        Size = UDim2.new(Element.Justify == 'Between' and 1 or 0, Element.Justify == 'Between' and (ImageFrame and 
-IconOffset - Element.UIPadding or -IconOffset) or 0, 1, 0),
                        Name = 'TitleFrame',
                    }, {
                        New('UIPadding', {
                            PaddingTop = UDim.new(0, Config.Window.NewElements and Element.UIPadding / 2 or 0),
                            PaddingLeft = UDim.new(0, Config.Window.NewElements and Element.UIPadding / 2 or 0),
                            PaddingRight = UDim.new(0, Config.Window.NewElements and Element.UIPadding / 2 or 0),
                            PaddingBottom = UDim.new(0, Config.Window.NewElements and Element.UIPadding / 2 or 0),
                        }),
                        New('UIListLayout', {
                            Padding = UDim.new(0, 6),
                            FillDirection = 'Vertical',
                            VerticalAlignment = 'Center',
                            HorizontalAlignment = 'Left',
                        }),
                        Title,
                        Desc,
                    }),
                }),
            })

            local LockedIcon = Creator.Image('lock', 'lock', 0, Config.Window.Folder, 'Lock', false)

            LockedIcon.Size = UDim2.new(0, 20, 0, 20)
            LockedIcon.ImageLabel.ImageColor3 = Color3.new(1, 1, 1)
            LockedIcon.ImageLabel.ImageTransparency = 0.4

            local LockedTitle = New('TextLabel', {
                Text = 'Locked',
                TextSize = 18,
                FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                AutomaticSize = 'XY',
                BackgroundTransparency = 1,
                TextColor3 = Color3.new(1, 1, 1),
                TextTransparency = 0.05,
            })
            local ElementFullFrame = New('Frame', {
                Size = UDim2.new(1, Element.UIPadding * 2, 1, Element.UIPadding * 2),
                BackgroundTransparency = 1,
                AnchorPoint = Vector2.new(0.5, 0.5),
                Position = UDim2.new(0.5, 0, 0.5, 0),
                ZIndex = 9999999,
            })
            local Locked, LockedTable = NewRoundFrame(Element.UICorner, 'Squircle', {
                Size = UDim2.new(1, 0, 1, 0),
                ImageTransparency = 0.25,
                ImageColor3 = Color3.new(0, 0, 0),
                Visible = false,
                Active = false,
                Parent = ElementFullFrame,
            }, {
                New('UIListLayout', {
                    FillDirection = 'Horizontal',
                    VerticalAlignment = 'Center',
                    HorizontalAlignment = 'Center',
                    Padding = UDim.new(0, 8),
                }),
                LockedIcon,
                LockedTitle,
            }, nil, true)
            local HighlightOutline, HighlightOutlineTable = NewRoundFrame(Element.UICorner, 'Squircle-Outline', {
                Size = UDim2.new(1, 0, 1, 0),
                ImageTransparency = 1,
                Active = false,
                ThemeTag = {
                    ImageColor3 = 'Text',
                },
                Parent = ElementFullFrame,
            }, {
                New('UIListLayout', {
                    FillDirection = 'Horizontal',
                    VerticalAlignment = 'Center',
                    HorizontalAlignment = 'Center',
                    Padding = UDim.new(0, 8),
                }),
            }, nil, true)
            local Highlight, HighlightTable = NewRoundFrame(Element.UICorner, 'Squircle', {
                Size = UDim2.new(1, 0, 1, 0),
                ImageTransparency = 1,
                Active = false,
                ThemeTag = {
                    ImageColor3 = 'Text',
                },
                Parent = ElementFullFrame,
            }, {
                New('UIListLayout', {
                    FillDirection = 'Horizontal',
                    VerticalAlignment = 'Center',
                    HorizontalAlignment = 'Center',
                    Padding = UDim.new(0, 8),
                }),
            }, nil, true)
            local HoverOutline, HoverOutlineTable = NewRoundFrame(Element.UICorner, 'Squircle-Outline', {
                Size = UDim2.new(1, 0, 1, 0),
                ImageTransparency = 1,
                Active = false,
                ThemeTag = {
                    ImageColor3 = 'Text',
                },
                Parent = ElementFullFrame,
            }, {
                New('UIListLayout', {
                    FillDirection = 'Horizontal',
                    VerticalAlignment = 'Center',
                    HorizontalAlignment = 'Center',
                    Padding = UDim.new(0, 8),
                }),
                New('UIGradient', {
                    Name = 'HoverGradient',
                    Color = ColorSequence.new({
                        ColorSequenceKeypoint.new(0, Color3.new(1, 1, 1)),
                        ColorSequenceKeypoint.new(0.5, Color3.new(1, 1, 1)),
                        ColorSequenceKeypoint.new(1, Color3.new(1, 1, 1)),
                    }),
                    Transparency = NumberSequence.new({
                        NumberSequenceKeypoint.new(0, 1),
                        NumberSequenceKeypoint.new(0.25, 0.9),
                        NumberSequenceKeypoint.new(0.5, 0.3),
                        NumberSequenceKeypoint.new(0.75, 0.9),
                        NumberSequenceKeypoint.new(1, 1),
                    }),
                }),
            }, nil, true)
            local Hover, HoverTable = NewRoundFrame(Element.UICorner, 'Squircle', {
                Size = UDim2.new(1, 0, 1, 0),
                ImageTransparency = 1,
                Active = false,
                ThemeTag = {
                    ImageColor3 = 'Text',
                },
                Parent = ElementFullFrame,
            }, {
                New('UIGradient', {
                    Name = 'HoverGradient',
                    Color = ColorSequence.new({
                        ColorSequenceKeypoint.new(0, Color3.new(1, 1, 1)),
                        ColorSequenceKeypoint.new(0.5, Color3.new(1, 1, 1)),
                        ColorSequenceKeypoint.new(1, Color3.new(1, 1, 1)),
                    }),
                    Transparency = NumberSequence.new({
                        NumberSequenceKeypoint.new(0, 1),
                        NumberSequenceKeypoint.new(0.25, 0.9),
                        NumberSequenceKeypoint.new(0.5, 0.3),
                        NumberSequenceKeypoint.new(0.75, 0.9),
                        NumberSequenceKeypoint.new(1, 1),
                    }),
                }),
                New('UIListLayout', {
                    FillDirection = 'Horizontal',
                    VerticalAlignment = 'Center',
                    HorizontalAlignment = 'Center',
                    Padding = UDim.new(0, 8),
                }),
            }, nil, true)
            local Main, MainTable = NewRoundFrame(Element.UICorner, 'Squircle', {
                Size = UDim2.new(1, 0, 0, 0),
                AutomaticSize = 'Y',
                ImageTransparency = Element.Color and 0.05 or 0.93,
                Parent = Config.Parent,
                ThemeTag = {
                    ImageColor3 = not Element.Color and 'ElementBackground' or nil,
                },
                ImageColor3 = Element.Color and (typeof(Element.Color) == 'string' and Color3.fromHex(Creator.Colors[Element.Color]) or typeof(Element.Color) == 'Color3' and Element.Color) or nil,
            }, {
                Element.UIElements.Container,
                ElementFullFrame,
                New('UIPadding', {
                    PaddingTop = UDim.new(0, Element.UIPadding),
                    PaddingLeft = UDim.new(0, Element.UIPadding),
                    PaddingRight = UDim.new(0, Element.UIPadding),
                    PaddingBottom = UDim.new(0, Element.UIPadding),
                }),
            }, true, true)

            Element.UIElements.Main = Main
            Element.UIElements.Locked = Locked

            if Element.Hover then
                Creator.AddSignal(Main.MouseEnter, function()
                    if CanHover then
                        Tween(Main, 0.12, {
                            ImageTransparency = Element.Color and 0.15 or 0.9,
                        }):Play()
                        Tween(Hover, 0.12, {ImageTransparency = 0.9}):Play()
                        Tween(HoverOutline, 0.12, {ImageTransparency = 0.8}):Play()
                        Creator.AddSignal(Main.MouseMoved, function(x, y)
                            Hover.HoverGradient.Offset = Vector2.new(((x - Main.AbsolutePosition.X) / Main.AbsoluteSize.X) - 0.5, 0)
                            HoverOutline.HoverGradient.Offset = Vector2.new(((x - Main.AbsolutePosition.X) / Main.AbsoluteSize.X) - 0.5, 0)
                        end)
                    end
                end)
                Creator.AddSignal(Main.InputEnded, function()
                    if CanHover then
                        Tween(Main, 0.12, {
                            ImageTransparency = Element.Color and 0.05 or 0.93,
                        }):Play()
                        Tween(Hover, 0.12, {ImageTransparency = 1}):Play()
                        Tween(HoverOutline, 0.12, {ImageTransparency = 1}):Play()
                    end
                end)
            end

            function Element:SetTitle(text)
                Element.Title = text
                Title.Text = text
            end
            function Element:SetDesc(text)
                Element.Desc = text
                Desc.Text = text or ''

                if not text then
                    Desc.Visible = false
                elseif not Desc.Visible then
                    Desc.Visible = true
                end
            end
            function Element:Colorize(obj, prop)
                if Element.Color then
                    obj[prop] = typeof(Element.Color) == 'string' and GetTextColorForHSB(Color3.fromHex(Creator.Colors[Element.Color])) or typeof(Element.Color) == 'Color3' and GetTextColorForHSB(Element.Color) or nil
                end
            end

            if Config.ElementTable then
                Creator.AddSignal(Title:GetPropertyChangedSignal('Text'), function(
                )
                    if Element.Title ~= Title.Text then
                        Element:SetTitle(Title.Text)

                        Config.ElementTable.Title = Title.Text
                    end
                end)
                Creator.AddSignal(Desc:GetPropertyChangedSignal('Text'), function(
                )
                    if Element.Desc ~= Desc.Text then
                        Element:SetDesc(Desc.Text)

                        Config.ElementTable.Desc = Desc.Text
                    end
                end)
            end

            function Element:SetThumbnail(newThumbnail, newSize)
                Element.Thumbnail = newThumbnail

                if newSize then
                    Element.ThumbnailSize = newSize
                    ThumbnailSize = newSize
                end
                if ThumbnailFrame then
                    if newThumbnail then
                        ThumbnailFrame:Destroy()

                        ThumbnailFrame = Creator.Image(newThumbnail, Element.Title, Element.UICorner - 3, Config.Window.Folder, 'Thumbnail', false, Element.IconThemed)
                        ThumbnailFrame.Size = UDim2.new(1, 0, 0, ThumbnailSize)
                        ThumbnailFrame.Parent = Element.UIElements.Container

                        local layout = Element.UIElements.Container:FindFirstChild('UIListLayout')

                        if layout then
                            ThumbnailFrame.LayoutOrder = -1
                        end
                    else
                        ThumbnailFrame.Visible = false
                    end
                else
                    if newThumbnail then
                        ThumbnailFrame = Creator.Image(newThumbnail, Element.Title, Element.UICorner - 3, Config.Window.Folder, 'Thumbnail', false, Element.IconThemed)
                        ThumbnailFrame.Size = UDim2.new(1, 0, 0, ThumbnailSize)
                        ThumbnailFrame.Parent = Element.UIElements.Container

                        local layout = Element.UIElements.Container:FindFirstChild('UIListLayout')

                        if layout then
                            ThumbnailFrame.LayoutOrder = -1
                        end
                    end
                end
            end
            function Element:SetImage(newImage, newSize)
                Element.Image = newImage

                if newSize then
                    Element.ImageSize = newSize
                    ImageSize = newSize
                end
                if newImage then
                    ImageFrame = Creator.Image(newImage, Element.Title, Element.UICorner - 3, Config.Window.Folder, 'Image', not Element.Color and true or false)

                    if typeof(Element.Color) == 'string' then
                        ImageFrame.ImageLabel.ImageColor3 = GetTextColorForHSB(Color3.fromHex(Creator.Colors[Element.Color]))
                    elseif typeof(Element.Color) == 'Color3' then
                        ImageFrame.ImageLabel.ImageColor3 = GetTextColorForHSB(Element.Color)
                    end

                    ImageFrame.Visible = true
                    ImageFrame.Size = UDim2.new(0, ImageSize, 0, ImageSize)
                    IconOffset = ImageSize
                else
                    if ImageFrame then
                        ImageFrame.Visible = true
                    end

                    IconOffset = 0
                end

                Element.UIElements.Container.TitleFrame.TitleFrame.Size = UDim2.new(1, 
-IconOffset, 1, 0)
            end
            function Element:Destroy()
                Main:Destroy()
            end
            function Element:Lock()
                CanHover = false
                Locked.Active = true
                Locked.Visible = true
            end
            function Element:Unlock()
                CanHover = true
                Locked.Active = false
                Locked.Visible = false
            end
            function Element:Highlight()
                local OutlineGradient = New('UIGradient', {
                    Color = ColorSequence.new({
                        ColorSequenceKeypoint.new(0, Color3.new(1, 1, 1)),
                        ColorSequenceKeypoint.new(0.5, Color3.new(1, 1, 1)),
                        ColorSequenceKeypoint.new(1, Color3.new(1, 1, 1)),
                    }),
                    Transparency = NumberSequence.new({
                        NumberSequenceKeypoint.new(0, 1),
                        NumberSequenceKeypoint.new(0.1, 0.9),
                        NumberSequenceKeypoint.new(0.5, 0.3),
                        NumberSequenceKeypoint.new(0.9, 0.9),
                        NumberSequenceKeypoint.new(1, 1),
                    }),
                    Rotation = 0,
                    Offset = Vector2.new(-1, 0),
                    Parent = HighlightOutline,
                })
                local HighlightGradient = New('UIGradient', {
                    Color = ColorSequence.new({
                        ColorSequenceKeypoint.new(0, Color3.new(1, 1, 1)),
                        ColorSequenceKeypoint.new(0.5, Color3.new(1, 1, 1)),
                        ColorSequenceKeypoint.new(1, Color3.new(1, 1, 1)),
                    }),
                    Transparency = NumberSequence.new({
                        NumberSequenceKeypoint.new(0, 1),
                        NumberSequenceKeypoint.new(0.15, 0.8),
                        NumberSequenceKeypoint.new(0.5, 0.1),
                        NumberSequenceKeypoint.new(0.85, 0.8),
                        NumberSequenceKeypoint.new(1, 1),
                    }),
                    Rotation = 0,
                    Offset = Vector2.new(-1, 0),
                    Parent = Highlight,
                })

                HighlightOutline.ImageTransparency = 0.65
                Highlight.ImageTransparency = 0.88

                Tween(OutlineGradient, 0.75, {
                    Offset = Vector2.new(1, 0),
                }):Play()
                Tween(HighlightGradient, 0.75, {
                    Offset = Vector2.new(1, 0),
                }):Play()
                task.spawn(function()
                    task.wait(0.75)

                    HighlightOutline.ImageTransparency = 1
                    Highlight.ImageTransparency = 1

                    OutlineGradient:Destroy()
                    HighlightGradient:Destroy()
                end)
            end
            function Element.UpdateShape(Tab)
                if Config.Window.NewElements then
                    local newShape

                    if Config.ParentConfig.ParentType == 'Group' then
                        newShape = 'Squircle'
                    else
                        newShape = getElementPosition(Tab.Elements, Element.Index)
                    end
                    if newShape and Main then
                        MainTable:SetType(newShape)
                        LockedTable:SetType(newShape)
                        HighlightTable:SetType(newShape)
                        HighlightOutlineTable:SetType(newShape .. '-Outline')
                        HoverTable:SetType(newShape)
                        HoverOutlineTable:SetType(newShape .. '-Outline')
                    end
                end
            end

            return Element
        end
    end
    function __DARKLUA_BUNDLE_MODULES.C()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Element = {}
        local CreateButton = __DARKLUA_BUNDLE_MODULES.load('l').New

        function Element:New(ElementConfig)
            ElementConfig.Hover = false
            ElementConfig.TextOffset = 0
            ElementConfig.ParentConfig = ElementConfig
            ElementConfig.IsButtons = ElementConfig.Buttons and #ElementConfig.Buttons > 0 and true or false

            local ParagraphModule = {
                __type = 'Paragraph',
                Title = ElementConfig.Title or 'Paragraph',
                Desc = ElementConfig.Desc or nil,
                Locked = ElementConfig.Locked or false,
            }
            local Paragraph = __DARKLUA_BUNDLE_MODULES.load('B')(ElementConfig)

            ParagraphModule.ParagraphFrame = Paragraph

            if ElementConfig.Buttons and #ElementConfig.Buttons > 0 then
                local ButtonsContainer = New('Frame', {
                    Size = UDim2.new(1, 0, 0, 38),
                    BackgroundTransparency = 1,
                    AutomaticSize = 'Y',
                    Parent = Paragraph.UIElements.Container,
                }, {
                    New('UIListLayout', {
                        Padding = UDim.new(0, 10),
                        FillDirection = 'Vertical',
                    }),
                })

                for _, Button in next, ElementConfig.Buttons do
                    local ButtonFrame = CreateButton(Button.Title, Button.Icon, Button.Callback, 'White', ButtonsContainer, nil, nil, ElementConfig.Window.NewElements and 12 or 10)

                    ButtonFrame.Size = UDim2.new(1, 0, 0, 38)
                end
            end

            return ParagraphModule.__type, ParagraphModule
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.D()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local _ = Creator.New
        local Element = {}

        function Element:New(Config)
            local Button = {
                __type = 'Button',
                Title = Config.Title or 'Button',
                Desc = Config.Desc or nil,
                Icon = Config.Icon or 'mouse-pointer-click',
                IconThemed = Config.IconThemed or false,
                Color = Config.Color,
                Justify = Config.Justify or 'Between',
                IconAlign = Config.IconAlign or 'Right',
                Locked = Config.Locked or false,
                Callback = Config.Callback or function() end,
                UIElements = {},
            }
            local CanCallback = true

            Button.ButtonFrame = __DARKLUA_BUNDLE_MODULES.load('B')({
                Title = Button.Title,
                Desc = Button.Desc,
                Parent = Config.Parent,
                Window = Config.Window,
                Color = Button.Color,
                Justify = Button.Justify,
                TextOffset = 20,
                Hover = true,
                Scalable = true,
                Tab = Config.Tab,
                Index = Config.Index,
                ElementTable = Button,
                ParentConfig = Config,
            })
            Button.UIElements.ButtonIcon = Creator.Image(Button.Icon, Button.Icon, 0, Config.Window.Folder, 'Button', not Button.Color and true or nil, Button.IconThemed)
            Button.UIElements.ButtonIcon.Size = UDim2.new(0, 20, 0, 20)
            Button.UIElements.ButtonIcon.Parent = Button.Justify == 'Between' and Button.ButtonFrame.UIElements.Main or Button.ButtonFrame.UIElements.Container.TitleFrame
            Button.UIElements.ButtonIcon.LayoutOrder = Button.IconAlign == 'Left' and 
-99999 or 99999
            Button.UIElements.ButtonIcon.AnchorPoint = Vector2.new(1, 0.5)
            Button.UIElements.ButtonIcon.Position = UDim2.new(1, 0, 0.5, 0)

            Button.ButtonFrame:Colorize(Button.UIElements.ButtonIcon.ImageLabel, 'ImageColor3')

            function Button:Lock()
                Button.Locked = true
                CanCallback = false

                return Button.ButtonFrame:Lock()
            end
            function Button:Unlock()
                Button.Locked = false
                CanCallback = true

                return Button.ButtonFrame:Unlock()
            end

            if Button.Locked then
                Button:Lock()
            end

            Creator.AddSignal(Button.ButtonFrame.UIElements.Main.MouseButton1Click, function(
            )
                if CanCallback then
                    task.spawn(function()
                        Creator.SafeCallback(Button.Callback)
                    end)
                end
            end)

            return Button.__type, Button
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.E()
        local Toggle = {}
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween
        local UserInputService = game:GetService('UserInputService')

        function Toggle.New(
            Value,
            Icon,
            IconSize,
            Parent,
            Callback,
            NewElement,
            Config
        )
            local Toggle = {}
            local Radius = 12
            local IconToggleFrame

            if Icon and Icon ~= '' then
                IconToggleFrame = New('ImageLabel', {
                    Size = UDim2.new(1, -7, 1, -7),
                    BackgroundTransparency = 1,
                    AnchorPoint = Vector2.new(0.5, 0.5),
                    Position = UDim2.new(0.5, 0, 0.5, 0),
                    Image = Creator.Icon(Icon)[1],
                    ImageRectOffset = Creator.Icon(Icon)[2].ImageRectPosition,
                    ImageRectSize = Creator.Icon(Icon)[2].ImageRectSize,
                    ImageTransparency = 1,
                    ImageColor3 = Color3.new(0, 0, 0),
                })
            end

            local ToggleContainer = New('Frame', {
                Size = UDim2.new(0, 2, 0, 26),
                BackgroundTransparency = 1,
                Parent = Parent,
            })
            local ToggleFrame = Creator.NewRoundFrame(Radius, 'Squircle', {
                ImageTransparency = 0.85,
                ThemeTag = {
                    ImageColor3 = 'Text',
                },
                Parent = ToggleContainer,
                Size = UDim2.new(0, NewElement and (52) or (40.8), 0, 24),
                AnchorPoint = Vector2.new(1, 0.5),
                Position = UDim2.new(0, 0, 0.5, 0),
            }, {
                Creator.NewRoundFrame(Radius, 'Squircle', {
                    Size = UDim2.new(1, 0, 1, 0),
                    Name = 'Layer',
                    ThemeTag = {
                        ImageColor3 = 'Toggle',
                    },
                    ImageTransparency = 1,
                }),
                Creator.NewRoundFrame(Radius, 'SquircleOutline', {
                    Size = UDim2.new(1, 0, 1, 0),
                    Name = 'Stroke',
                    ImageColor3 = Color3.new(1, 1, 1),
                    ImageTransparency = 1,
                }, {
                    New('UIGradient', {
                        Rotation = 90,
                        Transparency = NumberSequence.new({
                            NumberSequenceKeypoint.new(0, 0),
                            NumberSequenceKeypoint.new(1, 1),
                        }),
                    }),
                }),
                Creator.NewRoundFrame(Radius, 'Squircle', {
                    Size = UDim2.new(0, NewElement and 30 or 20, 0, 20),
                    Position = UDim2.new(0, 2, 0.5, 0),
                    AnchorPoint = Vector2.new(0, 0.5),
                    ImageTransparency = 1,
                    Name = 'Frame',
                }, {
                    Creator.NewRoundFrame(Radius, 'Squircle', {
                        Size = UDim2.new(1, 0, 1, 0),
                        ImageTransparency = 0,
                        ThemeTag = {
                            ImageColor3 = 'ToggleBar',
                        },
                        AnchorPoint = Vector2.new(0.5, 0.5),
                        Position = UDim2.new(0.5, 0, 0.5, 0),
                        Name = 'Bar',
                    }, {
                        Creator.NewRoundFrame(Radius, 'SquircleOutline2', {
                            Size = UDim2.new(1, 0, 1, 0),
                            ImageColor3 = Color3.new(1, 1, 1),
                            Name = 'Highlight',
                            ImageTransparency = 0.45,
                        }, {
                            New('UIGradient', {
                                Rotation = 60,
                                Color = ColorSequence.new({
                                    ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
                                    ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 255, 255)),
                                    ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255)),
                                }),
                                Transparency = NumberSequence.new({
                                    NumberSequenceKeypoint.new(0, 0.1),
                                    NumberSequenceKeypoint.new(0.5, 1),
                                    NumberSequenceKeypoint.new(1, 0.1),
                                }),
                            }),
                        }),
                        IconToggleFrame,
                        New('UIScale', {Scale = 1}),
                    }),
                }),
            })
            local dragConnection
            local endConnection
            local FrameWidth = NewElement and 30 or 20
            local ToggleWidth = ToggleFrame.Size.X.Offset

            function Toggle:Set(Toggled, isCallback, isAnim)
                if not isAnim then
                    if Toggled then
                        Tween(ToggleFrame.Frame, 0.15, {
                            Position = UDim2.new(0, ToggleWidth - FrameWidth - 2, 0.5, 0),
                        }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                        Tween(ToggleFrame.Layer, 0.1, {ImageTransparency = 0}):Play()

                        if IconToggleFrame then
                            Tween(IconToggleFrame, 0.1, {ImageTransparency = 0}):Play()
                        end
                    else
                        Tween(ToggleFrame.Frame, 0.15, {
                            Position = UDim2.new(0, 2, 0.5, 0),
                        }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                        Tween(ToggleFrame.Layer, 0.1, {ImageTransparency = 1}):Play()

                        if IconToggleFrame then
                            Tween(IconToggleFrame, 0.1, {ImageTransparency = 1}):Play()
                        end
                    end
                end

                isCallback = isCallback ~= false

                task.spawn(function()
                    if Callback and isCallback then
                        Creator.SafeCallback(Callback, Toggled)
                    end
                end)
            end
            function Toggle:Animate(input, ToggleObj)
                if not Config.Window.IsToggleDragging then
                    Config.Window.IsToggleDragging = true

                    local startMouseX = input.Position.X
                    local startMouseY = input.Position.Y
                    local startFrameX = ToggleFrame.Frame.Position.X.Offset
                    local isScrolling = false

                    Tween(ToggleFrame.Frame.Bar.UIScale, 0.28, {Scale = 1.5}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                    Tween(ToggleFrame.Frame.Bar, 0.28, {ImageTransparency = 0.85}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()

                    if dragConnection then
                        dragConnection:Disconnect()
                    end

                    dragConnection = UserInputService.InputChanged:Connect(function(
                        inputChanged
                    )
                        if Config.Window.IsToggleDragging and (inputChanged.UserInputType == Enum.UserInputType.MouseMovement or inputChanged.UserInputType == Enum.UserInputType.Touch) then
                            if isScrolling then
                                return
                            end

                            local deltaX = math.abs(inputChanged.Position.X - startMouseX)
                            local deltaY = math.abs(inputChanged.Position.Y - startMouseY)

                            if deltaY > deltaX and deltaY > 10 then
                                isScrolling = true
                                Config.Window.IsToggleDragging = false

                                if dragConnection then
                                    dragConnection:Disconnect()

                                    dragConnection = nil
                                end
                                if endConnection then
                                    endConnection:Disconnect()

                                    endConnection = nil
                                end

                                Tween(ToggleFrame.Frame, 0.15, {
                                    Position = UDim2.new(0, startFrameX, 0.5, 0),
                                }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                                Tween(ToggleFrame.Frame.Bar.UIScale, 0.23, {Scale = 1}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                                Tween(ToggleFrame.Frame.Bar, 0.23, {ImageTransparency = 0}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()

                                return
                            end

                            local mouseDelta = inputChanged.Position.X - startMouseX
                            local newX = math.max(2, math.min(startFrameX + mouseDelta, ToggleWidth - FrameWidth - 2))

                            Tween(ToggleFrame.Frame, 0.05, {
                                Position = UDim2.new(0, newX, 0.5, 0),
                            }, Enum.EasingStyle.Linear, Enum.EasingDirection.Out):Play()
                        end
                    end)

                    if endConnection then
                        endConnection:Disconnect()
                    end

                    endConnection = UserInputService.InputEnded:Connect(function(
                        inputEnded
                    )
                        if Config.Window.IsToggleDragging and (inputEnded.UserInputType == Enum.UserInputType.MouseButton1 or inputEnded.UserInputType == Enum.UserInputType.Touch) then
                            Config.Window.IsToggleDragging = false

                            if dragConnection then
                                dragConnection:Disconnect()

                                dragConnection = nil
                            end
                            if endConnection then
                                endConnection:Disconnect()

                                endConnection = nil
                            end
                            if isScrolling then
                                return
                            end

                            local currentX = ToggleFrame.Frame.Position.X.Offset
                            local delta = math.abs(inputEnded.Position.X - startMouseX)

                            if delta < 10 then
                                ToggleObj:Set(not ToggleObj.Value, true, false)
                            else
                                local barCenter = currentX + FrameWidth / 2
                                local toggleCenter = ToggleWidth / 2
                                local newValue = barCenter > toggleCenter

                                ToggleObj:Set(newValue, true, false)
                            end

                            Tween(ToggleFrame.Frame.Bar.UIScale, 0.23, {Scale = 1}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                            Tween(ToggleFrame.Frame.Bar, 0.23, {ImageTransparency = 0}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                        end
                    end)
                end
            end

            return ToggleContainer, Toggle
        end

        return Toggle
    end
    function __DARKLUA_BUNDLE_MODULES.F()
        local Checkbox = {}
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween

        function Checkbox.New(Value, Icon, IconSize, Parent, Callback, Config)
            local Checkbox = {}

            Icon = Icon or 'sfsymbols:checkmark'

            local Radius = 9
            local IconCheckboxFrame = Creator.Image(Icon, Icon, 0, (Config and Config.Window.Folder or 'Temp'), 'Checkbox', true, false, 'CheckboxIcon')

            IconCheckboxFrame.Size = UDim2.new(1, -26 + IconSize, 1, -26 + IconSize)
            IconCheckboxFrame.AnchorPoint = Vector2.new(0.5, 0.5)
            IconCheckboxFrame.Position = UDim2.new(0.5, 0, 0.5, 0)

            local CheckboxFrame = Creator.NewRoundFrame(Radius, 'Squircle', {
                ImageTransparency = 0.85,
                ThemeTag = {
                    ImageColor3 = 'Text',
                },
                Parent = Parent,
                Size = UDim2.new(0, 26, 0, 26),
            }, {
                Creator.NewRoundFrame(Radius, 'Squircle', {
                    Size = UDim2.new(1, 0, 1, 0),
                    Name = 'Layer',
                    ThemeTag = {
                        ImageColor3 = 'Checkbox',
                    },
                    ImageTransparency = 1,
                }),
                Creator.NewRoundFrame(Radius, 'SquircleOutline', {
                    Size = UDim2.new(1, 0, 1, 0),
                    Name = 'Stroke',
                    ImageColor3 = Color3.new(1, 1, 1),
                    ImageTransparency = 1,
                }, {
                    New('UIGradient', {
                        Rotation = 90,
                        Transparency = NumberSequence.new({
                            NumberSequenceKeypoint.new(0, 0),
                            NumberSequenceKeypoint.new(1, 1),
                        }),
                    }),
                }),
                IconCheckboxFrame,
            })

            function Checkbox:Set(Toggled)
                if Toggled then
                    Tween(CheckboxFrame.Layer, 0.06, {ImageTransparency = 0}):Play()
                    Tween(IconCheckboxFrame.ImageLabel, 0.06, {ImageTransparency = 0}):Play()
                else
                    Tween(CheckboxFrame.Layer, 0.05, {ImageTransparency = 1}):Play()
                    Tween(IconCheckboxFrame.ImageLabel, 0.06, {ImageTransparency = 1}):Play()
                end

                task.spawn(function()
                    if Callback then
                        Creator.SafeCallback(Callback, Toggled)
                    end
                end)
            end

            return CheckboxFrame, Checkbox
        end

        return Checkbox
    end
    function __DARKLUA_BUNDLE_MODULES.G()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local _ = Creator.New
        local _ = Creator.Tween
        local CreateToggle = __DARKLUA_BUNDLE_MODULES.load('E').New
        local CreateCheckbox = __DARKLUA_BUNDLE_MODULES.load('F').New
        local Element = {}

        function Element:New(Config)
            local Toggle = {
                __type = 'Toggle',
                Title = Config.Title or 'Toggle',
                Desc = Config.Desc or nil,
                Locked = Config.Locked or false,
                Value = Config.Value,
                Icon = Config.Icon or nil,
                IconSize = Config.IconSize or 23,
                Type = Config.Type or 'Toggle',
                Callback = Config.Callback or function() end,
                UIElements = {},
            }

            Toggle.ToggleFrame = __DARKLUA_BUNDLE_MODULES.load('B')({
                Title = Toggle.Title,
                Desc = Toggle.Desc,
                Window = Config.Window,
                Parent = Config.Parent,
                TextOffset = (52),
                Hover = false,
                Tab = Config.Tab,
                Index = Config.Index,
                ElementTable = Toggle,
                ParentConfig = Config,
            })

            local CanCallback = true

            if Toggle.Value == nil then
                Toggle.Value = false
            end

            function Toggle:Lock()
                Toggle.Locked = true
                CanCallback = false

                return Toggle.ToggleFrame:Lock()
            end
            function Toggle:Unlock()
                Toggle.Locked = false
                CanCallback = true

                return Toggle.ToggleFrame:Unlock()
            end

            if Toggle.Locked then
                Toggle:Lock()
            end

            local Toggled = Toggle.Value
            local ToggleFrame, ToggleFunc

            if Toggle.Type == 'Toggle' then
                ToggleFrame, ToggleFunc = CreateToggle(Toggled, Toggle.Icon, Toggle.IconSize, Toggle.ToggleFrame.UIElements.Main, Toggle.Callback, Config.Window.NewElements, Config)
            elseif Toggle.Type == 'Checkbox' then
                ToggleFrame, ToggleFunc = CreateCheckbox(Toggled, Toggle.Icon, Toggle.IconSize, Toggle.ToggleFrame.UIElements.Main, Toggle.Callback, Config)
            else
                error('Unknown Toggle Type: ' .. tostring(Toggle.Type))
            end

            ToggleFrame.AnchorPoint = Vector2.new(1, Config.Window.NewElements and 0 or 0.5)
            ToggleFrame.Position = UDim2.new(1, 0, Config.Window.NewElements and 0 or 0.5, 0)

            function Toggle:Set(v, isCallback, isAnim)
                if CanCallback then
                    ToggleFunc:Set(v, isCallback, isAnim or false)

                    Toggled = v
                    Toggle.Value = v
                end
            end

            Toggle:Set(Toggled, false, Config.Window.NewElements)

            if Config.Window.NewElements and ToggleFunc.Animate then
                Creator.AddSignal(Toggle.ToggleFrame.UIElements.Main.InputBegan, function(
                    input
                )
                    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                        ToggleFunc:Animate(input, Toggle)
                    end
                end)
            else
                Creator.AddSignal(Toggle.ToggleFrame.UIElements.Main.MouseButton1Click, function(
                )
                    Toggle:Set(not Toggle.Value, nil, Config.Window.NewElements)
                end)
            end

            return Toggle.__type, Toggle
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.H()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)
        local Element = {}
        local HoldingSlider = false

        function Element:New(Config)
            local Slider = {
                __type = 'Slider',
                Title = Config.Title or 'Slider',
                Desc = Config.Desc or nil,
                Locked = Config.Locked or nil,
                Value = Config.Value or {},
                Step = Config.Step or 1,
                Callback = Config.Callback or function() end,
                UIElements = {},
                IsFocusing = false,
                Width = 130,
                TextBoxWidth = 30,
                ThumbSize = 13,
            }
            local isTouch
            local moveconnection
            local releaseconnection
            local Value = Slider.Value.Default or Slider.Value.Min or 0
            local LastValue = Value
            local delta = (Value - (Slider.Value.Min or 0)) / ((Slider.Value.Max or 100) - (Slider.Value.Min or 0))
            local CanCallback = true
            local IsFloat = Slider.Step % 1 ~= 0
            local FormatValue = function(val)
                if IsFloat then
                    return string.format('%.2f', val)
                else
                    return tostring(math.floor(val + 0.5))
                end
            end
            local CalculateValue = function(rawValue)
                if IsFloat then
                    return math.floor(rawValue / Slider.Step + 0.5) * Slider.Step
                else
                    return math.floor(rawValue / Slider.Step + 0.5) * Slider.Step
                end
            end

            Slider.SliderFrame = __DARKLUA_BUNDLE_MODULES.load('B')({
                Title = Slider.Title,
                Desc = Slider.Desc,
                Parent = Config.Parent,
                TextOffset = Slider.Width,
                Hover = false,
                Tab = Config.Tab,
                Index = Config.Index,
                Window = Config.Window,
                ElementTable = Slider,
                ParentConfig = Config,
            })
            Slider.UIElements.SliderIcon = Creator.NewRoundFrame(99, 'Squircle', {
                ImageTransparency = 0.95,
                Size = UDim2.new(1, -Slider.TextBoxWidth - 8, 0, 4),
                Name = 'Frame',
                ThemeTag = {
                    ImageColor3 = 'Text',
                },
            }, {
                Creator.NewRoundFrame(99, 'Squircle', {
                    Name = 'Frame',
                    Size = UDim2.new(delta, 0, 1, 0),
                    ImageTransparency = 0.1,
                    ThemeTag = {
                        ImageColor3 = 'Button',
                    },
                }, {
                    Creator.NewRoundFrame(99, 'Squircle', {
                        Size = UDim2.new(0, Config.Window.NewElements and (Slider.ThumbSize * 1.75) or (Slider.ThumbSize + 2), 0, Slider.ThumbSize + 2),
                        Position = UDim2.new(1, 0, 0.5, 0),
                        AnchorPoint = Vector2.new(0.5, 0.5),
                        ThemeTag = {
                            ImageColor3 = 'Text',
                        },
                        Name = 'Thumb',
                    }),
                }),
            })
            Slider.UIElements.SliderContainer = New('Frame', {
                Size = UDim2.new(0, Slider.Width, 0, 0),
                AutomaticSize = 'Y',
                Position = UDim2.new(1, Config.Window.NewElements and -20 or 0, 0.5, 0),
                AnchorPoint = Vector2.new(1, 0.5),
                BackgroundTransparency = 1,
                Parent = Slider.SliderFrame.UIElements.Main,
            }, {
                New('UIListLayout', {
                    Padding = UDim.new(0, 8),
                    FillDirection = 'Horizontal',
                    VerticalAlignment = 'Center',
                }),
                Slider.UIElements.SliderIcon,
                New('TextBox', {
                    Size = UDim2.new(0, Slider.TextBoxWidth, 0, 0),
                    TextXAlignment = 'Left',
                    Text = FormatValue(Value),
                    ThemeTag = {
                        TextColor3 = 'Text',
                    },
                    TextTransparency = 0.4,
                    AutomaticSize = 'Y',
                    TextSize = 15,
                    FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                    BackgroundTransparency = 1,
                    LayoutOrder = -1,
                }),
            })

            function Slider:Lock()
                Slider.Locked = true
                CanCallback = false

                return Slider.SliderFrame:Lock()
            end
            function Slider:Unlock()
                Slider.Locked = false
                CanCallback = true

                return Slider.SliderFrame:Unlock()
            end

            if Slider.Locked then
                Slider:Lock()
            end

            local ScrollingFrameParent = Config.Tab.UIElements.ContainerFrame

            function Slider:Set(Value, input)
                if CanCallback then
                    if not Slider.IsFocusing and not HoldingSlider and (not input or (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch)) then
                        Value = math.clamp(Value, Slider.Value.Min or 0, Slider.Value.Max or 100)

                        local delta = math.clamp((Value - (Slider.Value.Min or 0)) / ((Slider.Value.Max or 100) - (Slider.Value.Min or 0)), 0, 1)

                        Value = CalculateValue(Slider.Value.Min + delta * (Slider.Value.Max - Slider.Value.Min))

                        if Value ~= LastValue then
                            Tween(Slider.UIElements.SliderIcon.Frame, 0.05, {
                                Size = UDim2.new(delta, 0, 1, 0),
                            }):Play()

                            Slider.UIElements.SliderContainer.TextBox.Text = FormatValue(Value)
                            Slider.Value.Default = FormatValue(Value)
                            LastValue = Value

                            Creator.SafeCallback(Slider.Callback, FormatValue(Value))
                        end
                        if input then
                            isTouch = (input.UserInputType == Enum.UserInputType.Touch)
                            ScrollingFrameParent.ScrollingEnabled = false
                            HoldingSlider = true
                            moveconnection = cloneref(game:GetService('RunService')).RenderStepped:Connect(function(
                            )
                                local inputPosition = isTouch and input.Position.X or cloneref(game:GetService('UserInputService')):GetMouseLocation().X
                                local delta = math.clamp((inputPosition - Slider.UIElements.SliderIcon.AbsolutePosition.X) / Slider.UIElements.SliderIcon.AbsoluteSize.X, 0, 1)

                                Value = CalculateValue(Slider.Value.Min + delta * (Slider.Value.Max - Slider.Value.Min))

                                if Value ~= LastValue then
                                    Tween(Slider.UIElements.SliderIcon.Frame, 0.05, {
                                        Size = UDim2.new(delta, 0, 1, 0),
                                    }):Play()

                                    Slider.UIElements.SliderContainer.TextBox.Text = FormatValue(Value)
                                    Slider.Value.Default = FormatValue(Value)
                                    LastValue = Value

                                    Creator.SafeCallback(Slider.Callback, FormatValue(Value))
                                end
                            end)
                            releaseconnection = cloneref(game:GetService('UserInputService')).InputEnded:Connect(function(
                                endInput
                            )
                                if (endInput.UserInputType == Enum.UserInputType.MouseButton1 or endInput.UserInputType == Enum.UserInputType.Touch) and input == endInput then
                                    moveconnection:Disconnect()
                                    releaseconnection:Disconnect()

                                    HoldingSlider = false
                                    ScrollingFrameParent.ScrollingEnabled = true

                                    Tween(Slider.UIElements.SliderIcon.Frame.Thumb, 0.2, {
                                        Size = UDim2.new(0, Config.Window.NewElements and (Slider.ThumbSize * 1.75) or (Slider.ThumbSize + 2), 0, Slider.ThumbSize + 2),
                                    }, Enum.EasingStyle.Quint, Enum.EasingDirection.InOut):Play()
                                end
                            end)
                        end
                    end
                end
            end
            function Slider:SetMax(newMax)
                Slider.Value.Max = newMax

                local currentValue = tonumber(Slider.Value.Default) or LastValue

                if currentValue > newMax then
                    Slider:Set(newMax)
                else
                    local newDelta = math.clamp((currentValue - (Slider.Value.Min or 0)) / (newMax - (Slider.Value.Min or 0)), 0, 1)

                    Tween(Slider.UIElements.SliderIcon.Frame, 0.1, {
                        Size = UDim2.new(newDelta, 0, 1, 0),
                    }):Play()
                end
            end
            function Slider:SetMin(newMin)
                Slider.Value.Min = newMin

                local currentValue = tonumber(Slider.Value.Default) or LastValue

                if currentValue < newMin then
                    Slider:Set(newMin)
                else
                    local newDelta = math.clamp((currentValue - newMin) / ((Slider.Value.Max or 100) - newMin), 0, 1)

                    Tween(Slider.UIElements.SliderIcon.Frame, 0.1, {
                        Size = UDim2.new(newDelta, 0, 1, 0),
                    }):Play()
                end
            end

            Creator.AddSignal(Slider.UIElements.SliderContainer.TextBox.FocusLost, function(
                enterPressed
            )
                if enterPressed then
                    local newValue = tonumber(Slider.UIElements.SliderContainer.TextBox.Text)

                    if newValue then
                        Slider:Set(newValue)
                    else
                        Slider.UIElements.SliderContainer.TextBox.Text = FormatValue(LastValue)
                    end
                end
            end)
            Creator.AddSignal(Slider.UIElements.SliderContainer.InputBegan, function(
                input
            )
                Slider:Set(Value, input)

                if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                    Tween(Slider.UIElements.SliderIcon.Frame.Thumb, 0.24, {
                        Size = UDim2.new(0, (Config.Window.NewElements and (Slider.ThumbSize * 1.75) or (Slider.ThumbSize)) + 8, 0, Slider.ThumbSize + 8),
                    }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                end
            end)

            return Slider.__type, Slider
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.I()
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)
        local UserInputService = cloneref(game:GetService('UserInputService'))
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local _ = Creator.Tween
        local Element = {
            UICorner = 6,
            UIPadding = 8,
        }
        local CreateLabel = __DARKLUA_BUNDLE_MODULES.load('v').New

        function Element:New(Config)
            local Keybind = {
                __type = 'Keybind',
                Title = Config.Title or 'Keybind',
                Desc = Config.Desc or nil,
                Locked = Config.Locked or false,
                Value = Config.Value or 'F',
                Callback = Config.Callback or function() end,
                CanChange = Config.CanChange or true,
                Picking = false,
                UIElements = {},
            }
            local CanCallback = true

            Keybind.KeybindFrame = __DARKLUA_BUNDLE_MODULES.load('B')({
                Title = Keybind.Title,
                Desc = Keybind.Desc,
                Parent = Config.Parent,
                TextOffset = 85,
                Hover = Keybind.CanChange,
                Tab = Config.Tab,
                Index = Config.Index,
                Window = Config.Window,
                ElementTable = Keybind,
                ParentConfig = Config,
            })
            Keybind.UIElements.Keybind = CreateLabel(Keybind.Value, nil, Keybind.KeybindFrame.UIElements.Main)
            Keybind.UIElements.Keybind.Size = UDim2.new(0, 24 + Keybind.UIElements.Keybind.Frame.Frame.TextLabel.TextBounds.X, 0, 42)
            Keybind.UIElements.Keybind.AnchorPoint = Vector2.new(1, 0.5)
            Keybind.UIElements.Keybind.Position = UDim2.new(1, 0, 0.5, 0)

            New('UIScale', {
                Parent = Keybind.UIElements.Keybind,
                Scale = 0.85,
            })
            Creator.AddSignal(Keybind.UIElements.Keybind.Frame.Frame.TextLabel:GetPropertyChangedSignal('TextBounds'), function(
            )
                Keybind.UIElements.Keybind.Size = UDim2.new(0, 24 + Keybind.UIElements.Keybind.Frame.Frame.TextLabel.TextBounds.X, 0, 42)
            end)

            function Keybind:Lock()
                Keybind.Locked = true
                CanCallback = false

                return Keybind.KeybindFrame:Lock()
            end
            function Keybind:Unlock()
                Keybind.Locked = false
                CanCallback = true

                return Keybind.KeybindFrame:Unlock()
            end
            function Keybind:Set(v)
                Keybind.Value = v
                Keybind.UIElements.Keybind.Frame.Frame.TextLabel.Text = v
            end

            if Keybind.Locked then
                Keybind:Lock()
            end

            Creator.AddSignal(Keybind.KeybindFrame.UIElements.Main.MouseButton1Click, function(
            )
                if CanCallback then
                    if Keybind.CanChange then
                        Keybind.Picking = true
                        Keybind.UIElements.Keybind.Frame.Frame.TextLabel.Text = '...'

                        task.wait(0.2)

                        local Event

                        Event = UserInputService.InputBegan:Connect(function(
                            Input
                        )
                            local Key

                            if Input.UserInputType == Enum.UserInputType.Keyboard then
                                Key = Input.KeyCode.Name
                            elseif Input.UserInputType == Enum.UserInputType.MouseButton1 then
                                Key = 'MouseLeft'
                            elseif Input.UserInputType == Enum.UserInputType.MouseButton2 then
                                Key = 'MouseRight'
                            end

                            local EndedEvent

                            EndedEvent = UserInputService.InputEnded:Connect(function(
                                Input
                            )
                                if Input.KeyCode.Name == Key or Key == 'MouseLeft' and Input.UserInputType == Enum.UserInputType.MouseButton1 or Key == 'MouseRight' and Input.UserInputType == Enum.UserInputType.MouseButton2 then
                                    Keybind.Picking = false
                                    Keybind.UIElements.Keybind.Frame.Frame.TextLabel.Text = Key
                                    Keybind.Value = Key

                                    Event:Disconnect()
                                    EndedEvent:Disconnect()
                                end
                            end)
                        end)
                    end
                end
            end)
            Creator.AddSignal(UserInputService.InputBegan, function(input, gpe)
                if UserInputService:GetFocusedTextBox() then
                    return
                end
                if not CanCallback then
                    return
                end
                if input.UserInputType == Enum.UserInputType.Keyboard then
                    if input.KeyCode.Name == Keybind.Value then
                        Creator.SafeCallback(Keybind.Callback, input.KeyCode.Name)
                    end
                elseif input.UserInputType == Enum.UserInputType.MouseButton1 and Keybind.Value == 'MouseLeft' then
                    Creator.SafeCallback(Keybind.Callback, 'MouseLeft')
                elseif input.UserInputType == Enum.UserInputType.MouseButton2 and Keybind.Value == 'MouseRight' then
                    Creator.SafeCallback(Keybind.Callback, 'MouseRight')
                end
            end)

            return Keybind.__type, Keybind
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.J()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local _ = Creator.Tween
        local Element = {
            UICorner = 8,
            UIPadding = 8,
        }
        local _ = __DARKLUA_BUNDLE_MODULES.load('l').New
        local CreateInput = __DARKLUA_BUNDLE_MODULES.load('m').New

        function Element:New(Config)
            local Input = {
                __type = 'Input',
                Title = Config.Title or 'Input',
                Desc = Config.Desc or nil,
                Type = Config.Type or 'Input',
                Locked = Config.Locked or false,
                InputIcon = Config.InputIcon or false,
                Placeholder = Config.Placeholder or 'Enter Text...',
                Value = Config.Value or '',
                Callback = Config.Callback or function() end,
                ClearTextOnFocus = Config.ClearTextOnFocus or false,
                UIElements = {},
                Width = 150,
            }
            local CanCallback = true

            Input.InputFrame = __DARKLUA_BUNDLE_MODULES.load('B')({
                Title = Input.Title,
                Desc = Input.Desc,
                Parent = Config.Parent,
                TextOffset = Input.Width,
                Hover = false,
                Tab = Config.Tab,
                Index = Config.Index,
                Window = Config.Window,
                ElementTable = Input,
                ParentConfig = Config,
            })

            local InputComponent = CreateInput(Input.Placeholder, Input.InputIcon, Input.Type == 'Textarea' and Input.InputFrame.UIElements.Container or Input.InputFrame.UIElements.Main, Input.Type, function(
                v
            )
                Input:Set(v, true)
            end, nil, Config.Window.NewElements and 12 or 10, Input.ClearTextOnFocus)

            if Input.Type == 'Input' then
                InputComponent.Size = UDim2.new(0, Input.Width, 0, 36)
                InputComponent.Position = UDim2.new(1, 0, Config.Window.NewElements and 0 or 0.5, 0)
                InputComponent.AnchorPoint = Vector2.new(1, Config.Window.NewElements and 0 or 0.5)
            else
                InputComponent.Size = UDim2.new(1, 0, 0, 148)
            end

            New('UIScale', {
                Parent = InputComponent,
                Scale = 1,
            })

            function Input:Lock()
                Input.Locked = true
                CanCallback = false

                return Input.InputFrame:Lock()
            end
            function Input:Unlock()
                Input.Locked = false
                CanCallback = true

                return Input.InputFrame:Unlock()
            end
            function Input:Set(v, IsUserInput)
                if CanCallback then
                    Input.Value = v

                    Creator.SafeCallback(Input.Callback, v)

                    if not IsUserInput then
                        InputComponent.Frame.Frame.TextBox.Text = v
                    end
                end
            end
            function Input:SetPlaceholder(v)
                InputComponent.Frame.Frame.TextBox.PlaceholderText = v
                Input.Placeholder = v
            end

            Input:Set(Input.Value)

            if Input.Locked then
                Input:Lock()
            end

            return Input.__type, Input
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.K()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Element = {}

        function Element:New(Config)
            local Divider = New('Frame', {
                Size = Config.ParentType ~= 'Group' and UDim2.new(1, 0, 0, 1) or UDim2.new(0, 1, 1, 0),
                Position = UDim2.new(0.5, 0, 0.5, 0),
                AnchorPoint = Vector2.new(0.5, 0.5),
                BackgroundTransparency = 0.9,
                ThemeTag = {
                    BackgroundColor3 = 'Text',
                },
            })
            local MainDivider = New('Frame', {
                Parent = Config.Parent,
                Size = Config.ParentType ~= 'Group' and UDim2.new(1, -7, 0, 7) or UDim2.new(0, 7, 1, 
-7),
                BackgroundTransparency = 1,
            }, {Divider})

            return 'Divider', {
                __type = 'Divider',
                ElementFrame = MainDivider,
            }
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.L()
        local DropdownMenu = {}
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)
        local UserInputService = cloneref(game:GetService('UserInputService'))
        local Mouse = cloneref(game:GetService('Players')).LocalPlayer:GetMouse()
        local Camera = cloneref(game:GetService('Workspace')).CurrentCamera
        local CurrentCamera = workspace.CurrentCamera
        local CreateInput = __DARKLUA_BUNDLE_MODULES.load('m').New
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween

        function DropdownMenu.New(Config, Dropdown, Element, CanCallback, Type)
            local DropdownModule = {}

            if not Dropdown.Callback then
                Type = 'Menu'
            end

            Dropdown.UIElements.UIListLayout = New('UIListLayout', {
                Padding = UDim.new(0, Element.MenuPadding / 1.5),
                FillDirection = 'Vertical',
                HorizontalAlignment = 'Center',
            })
            Dropdown.UIElements.Menu = Creator.NewRoundFrame(Element.MenuCorner, 'Squircle', {
                ThemeTag = {
                    ImageColor3 = 'Background',
                },
                ImageTransparency = 1,
                Size = UDim2.new(1, 0, 1, 0),
                AnchorPoint = Vector2.new(1, 0),
                Position = UDim2.new(1, 0, 0, 0),
            }, {
                New('UIPadding', {
                    PaddingTop = UDim.new(0, Element.MenuPadding),
                    PaddingLeft = UDim.new(0, Element.MenuPadding),
                    PaddingRight = UDim.new(0, Element.MenuPadding),
                    PaddingBottom = UDim.new(0, Element.MenuPadding),
                }),
                New('UIListLayout', {
                    FillDirection = 'Vertical',
                    Padding = UDim.new(0, Element.MenuPadding),
                }),
                New('Frame', {
                    BackgroundTransparency = 1,
                    Size = UDim2.new(1, 0, 1, Dropdown.SearchBarEnabled and -Element.MenuPadding - Element.SearchBarHeight),
                    ClipsDescendants = true,
                    LayoutOrder = 999,
                }, {
                    New('UICorner', {
                        CornerRadius = UDim.new(0, Element.MenuCorner - Element.MenuPadding),
                    }),
                    New('ScrollingFrame', {
                        Size = UDim2.new(1, 0, 1, 0),
                        ScrollBarThickness = 0,
                        ScrollingDirection = 'Y',
                        AutomaticCanvasSize = 'Y',
                        CanvasSize = UDim2.new(0, 0, 0, 0),
                        BackgroundTransparency = 1,
                        ScrollBarImageTransparency = 1,
                    }, {
                        Dropdown.UIElements.UIListLayout,
                    }),
                }),
            })
            Dropdown.UIElements.MenuCanvas = New('Frame', {
                Size = UDim2.new(0, Dropdown.MenuWidth, 0, 300),
                BackgroundTransparency = 1,
                Position = UDim2.new(-10, 0, -10, 0),
                Visible = false,
                Active = false,
                Parent = Config.WindUI.DropdownGui,
                AnchorPoint = Vector2.new(1, 0),
            }, {
                Dropdown.UIElements.Menu,
                New('UISizeConstraint', {
                    MinSize = Vector2.new(170, 0),
                    MaxSize = Vector2.new(300, 400),
                }),
            })

            local RecalculateCanvasSize = function()
                Dropdown.UIElements.Menu.Frame.ScrollingFrame.CanvasSize = UDim2.fromOffset(0, Dropdown.UIElements.UIListLayout.AbsoluteContentSize.Y)
            end
            local RecalculateListSize = function()
                local MaxHeight = CurrentCamera.ViewportSize.Y * 0.6
                local ContentY = Dropdown.UIElements.UIListLayout.AbsoluteContentSize.Y
                local SearchBarOffset = Dropdown.SearchBarEnabled and (Element.SearchBarHeight + (Element.MenuPadding * 3)) or (Element.MenuPadding * 2)
                local TotalY = (ContentY) + SearchBarOffset

                if TotalY > MaxHeight then
                    Dropdown.UIElements.MenuCanvas.Size = UDim2.fromOffset(Dropdown.UIElements.MenuCanvas.AbsoluteSize.X, MaxHeight)
                else
                    Dropdown.UIElements.MenuCanvas.Size = UDim2.fromOffset(Dropdown.UIElements.MenuCanvas.AbsoluteSize.X, TotalY)
                end
            end

            function UpdatePosition()
                local button = Dropdown.UIElements.Dropdown or Dropdown.DropdownFrame.UIElements.Main
                local menu = Dropdown.UIElements.MenuCanvas
                local availableSpaceBelow = Camera.ViewportSize.Y - (button.AbsolutePosition.Y + button.AbsoluteSize.Y) - Element.MenuPadding - 54
                local requiredSpace = menu.AbsoluteSize.Y + Element.MenuPadding
                local offset = -54

                if availableSpaceBelow < requiredSpace then
                    offset = requiredSpace - availableSpaceBelow - 54
                end

                menu.Position = UDim2.new(0, button.AbsolutePosition.X + button.AbsoluteSize.X, 0, button.AbsolutePosition.Y + button.AbsoluteSize.Y - offset + (Element.MenuPadding * 2))
            end

            local SearchLabel

            function DropdownModule:Display()
                local Values = Dropdown.Values
                local Str = ''

                if Dropdown.Multi then
                    local selected = {}

                    if typeof(Dropdown.Value) == 'table' then
                        for _, item in ipairs(Dropdown.Value)do
                            local title = typeof(item) == 'table' and item.Title or item

                            selected[title] = true
                        end
                    end

                    for _, value in ipairs(Values)do
                        local title = typeof(value) == 'table' and value.Title or value

                        if selected[title] then
                            Str = Str .. title .. ', '
                        end
                    end

                    if #Str > 0 then
                        Str = Str:sub(1, #Str - 2)
                    end
                else
                    Str = typeof(Dropdown.Value) == 'table' and Dropdown.Value.Title or Dropdown.Value or ''
                end
                if Dropdown.UIElements.Dropdown then
                    Dropdown.UIElements.Dropdown.Frame.Frame.TextLabel.Text = (Str == '' and '--' or Str)
                end
            end

            local Callback = function(customCallback)
                DropdownModule:Display()

                if Dropdown.Callback then
                    task.spawn(function()
                        Creator.SafeCallback(Dropdown.Callback, Dropdown.Value)
                    end)
                else
                    task.spawn(function()
                        Creator.SafeCallback(customCallback)
                    end)
                end
            end

            function DropdownModule:LockValues(lockedItems)
                if not lockedItems then
                    return
                end

                for _, tab in next, Dropdown.Tabs do
                    if tab and tab.UIElements and tab.UIElements.TabItem then
                        local itemName = tab.Name
                        local isLocked = false

                        for _, lockedItem in next, lockedItems do
                            if itemName == lockedItem then
                                isLocked = true

                                break
                            end
                        end

                        if isLocked then
                            Tween(tab.UIElements.TabItem, 0.1, {ImageTransparency = 1}):Play()
                            Tween(tab.UIElements.TabItem.Highlight, 0.1, {ImageTransparency = 1}):Play()
                            Tween(tab.UIElements.TabItem.Frame.Title.TextLabel, 0.1, {TextTransparency = 0.6}):Play()

                            if tab.UIElements.TabIcon then
                                Tween(tab.UIElements.TabIcon.ImageLabel, 0.1, {ImageTransparency = 0.6}):Play()
                            end

                            tab.UIElements.TabItem.Active = false
                            tab.Locked = true
                        else
                            if tab.Selected then
                                Tween(tab.UIElements.TabItem, 0.1, {ImageTransparency = 0.95}):Play()
                                Tween(tab.UIElements.TabItem.Highlight, 0.1, {ImageTransparency = 0.75}):Play()
                                Tween(tab.UIElements.TabItem.Frame.Title.TextLabel, 0.1, {TextTransparency = 0}):Play()

                                if tab.UIElements.TabIcon then
                                    Tween(tab.UIElements.TabIcon.ImageLabel, 0.1, {ImageTransparency = 0}):Play()
                                end
                            else
                                Tween(tab.UIElements.TabItem, 0.1, {ImageTransparency = 1}):Play()
                                Tween(tab.UIElements.TabItem.Highlight, 0.1, {ImageTransparency = 1}):Play()
                                Tween(tab.UIElements.TabItem.Frame.Title.TextLabel, 0.1, {
                                    TextTransparency = Type == 'Dropdown' and 0.4 or 0.05,
                                }):Play()

                                if tab.UIElements.TabIcon then
                                    Tween(tab.UIElements.TabIcon.ImageLabel, 0.1, {
                                        ImageTransparency = Type == 'Dropdown' and 0.2 or 0,
                                    }):Play()
                                end
                            end

                            tab.UIElements.TabItem.Active = true
                            tab.Locked = false
                        end
                    end
                end
            end
            function DropdownModule:Refresh(Values)
                for _, Elementt in next, Dropdown.UIElements.Menu.Frame.ScrollingFrame:GetChildren()do
                    if not Elementt:IsA('UIListLayout') then
                        Elementt:Destroy()
                    end
                end

                Dropdown.Tabs = {}

                if Dropdown.SearchBarEnabled then
                    if not SearchLabel then
                        SearchLabel = CreateInput('Search...', 'search', Dropdown.UIElements.Menu, nil, function(
                            val
                        )
                            for _, tab in next, Dropdown.Tabs do
                                if string.find(string.lower(tab.Name), string.lower(val), 1, true) then
                                    tab.UIElements.TabItem.Visible = true
                                else
                                    tab.UIElements.TabItem.Visible = false
                                end

                                RecalculateListSize()
                                RecalculateCanvasSize()
                            end
                        end, true)
                        SearchLabel.Size = UDim2.new(1, 0, 0, Element.SearchBarHeight)
                        SearchLabel.Position = UDim2.new(0, 0, 0, 0)
                        SearchLabel.Name = 'SearchBar'
                    end
                end

                for Index, Tab in next, Values do
                    if (Tab.Type ~= 'Divider') then
                        local TabMain = {
                            Name = typeof(Tab) == 'table' and Tab.Title or Tab,
                            Desc = typeof(Tab) == 'table' and Tab.Desc or nil,
                            Icon = typeof(Tab) == 'table' and Tab.Icon or nil,
                            Original = Tab,
                            Selected = false,
                            Locked = typeof(Tab) == 'table' and Tab.Locked or false,
                            UIElements = {},
                        }
                        local TabIcon

                        if TabMain.Icon then
                            TabIcon = Creator.Image(TabMain.Icon, TabMain.Icon, 0, Config.Window.Folder, 'Dropdown', true)
                            TabIcon.Size = UDim2.new(0, Element.TabIcon, 0, Element.TabIcon)
                            TabIcon.ImageLabel.ImageTransparency = Type == 'Dropdown' and 0.2 or 0
                            TabMain.UIElements.TabIcon = TabIcon
                        end

                        TabMain.UIElements.TabItem = Creator.NewRoundFrame(Element.MenuCorner - Element.MenuPadding, 'Squircle', {
                            Size = UDim2.new(1, 0, 0, 36),
                            AutomaticSize = TabMain.Desc and 'Y',
                            ImageTransparency = 1,
                            Parent = Dropdown.UIElements.Menu.Frame.ScrollingFrame,
                            ImageColor3 = Color3.new(1, 1, 1),
                            Active = not TabMain.Locked,
                        }, {
                            Creator.NewRoundFrame(Element.MenuCorner - Element.MenuPadding, 'SquircleOutline', {
                                Size = UDim2.new(1, 0, 1, 0),
                                ImageColor3 = Color3.new(1, 1, 1),
                                ImageTransparency = 1,
                                Name = 'Highlight',
                            }, {
                                New('UIGradient', {
                                    Rotation = 80,
                                    Color = ColorSequence.new({
                                        ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
                                        ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 255, 255)),
                                        ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255)),
                                    }),
                                    Transparency = NumberSequence.new({
                                        NumberSequenceKeypoint.new(0, 0.1),
                                        NumberSequenceKeypoint.new(0.5, 1),
                                        NumberSequenceKeypoint.new(1, 0.1),
                                    }),
                                }),
                            }),
                            New('Frame', {
                                Size = UDim2.new(1, 0, 1, 0),
                                BackgroundTransparency = 1,
                            }, {
                                New('UIListLayout', {
                                    Padding = UDim.new(0, Element.TabPadding),
                                    FillDirection = 'Horizontal',
                                    VerticalAlignment = 'Center',
                                }),
                                New('UIPadding', {
                                    PaddingTop = UDim.new(0, Element.TabPadding),
                                    PaddingLeft = UDim.new(0, Element.TabPadding),
                                    PaddingRight = UDim.new(0, Element.TabPadding),
                                    PaddingBottom = UDim.new(0, Element.TabPadding),
                                }),
                                New('UICorner', {
                                    CornerRadius = UDim.new(0, Element.MenuCorner - Element.MenuPadding),
                                }),
                                TabIcon,
                                New('Frame', {
                                    Size = UDim2.new(1, TabIcon and -Element.TabPadding - Element.TabIcon or 0, 0, 0),
                                    BackgroundTransparency = 1,
                                    AutomaticSize = 'Y',
                                    Name = 'Title',
                                }, {
                                    New('TextLabel', {
                                        Text = TabMain.Name,
                                        TextXAlignment = 'Left',
                                        FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                                        ThemeTag = {
                                            TextColor3 = 'Text',
                                            BackgroundColor3 = 'Text',
                                        },
                                        TextSize = 15,
                                        BackgroundTransparency = 1,
                                        TextTransparency = Type == 'Dropdown' and 0.4 or 0.05,
                                        LayoutOrder = 999,
                                        AutomaticSize = 'Y',
                                        Size = UDim2.new(1, 0, 0, 0),
                                    }),
                                    New('TextLabel', {
                                        Text = TabMain.Desc or '',
                                        TextXAlignment = 'Left',
                                        FontFace = Font.new(Creator.Font, Enum.FontWeight.Regular),
                                        ThemeTag = {
                                            TextColor3 = 'Text',
                                            BackgroundColor3 = 'Text',
                                        },
                                        TextSize = 15,
                                        BackgroundTransparency = 1,
                                        TextTransparency = Type == 'Dropdown' and 0.6 or 0.35,
                                        LayoutOrder = 999,
                                        AutomaticSize = 'Y',
                                        TextWrapped = true,
                                        Size = UDim2.new(1, 0, 0, 0),
                                        Visible = TabMain.Desc and true or false,
                                        Name = 'Desc',
                                    }),
                                    New('UIListLayout', {
                                        Padding = UDim.new(0, Element.TabPadding / 3),
                                        FillDirection = 'Vertical',
                                    }),
                                }),
                            }),
                        }, true)

                        if TabMain.Locked then
                            TabMain.UIElements.TabItem.Frame.Title.TextLabel.TextTransparency = 0.6

                            if TabMain.UIElements.TabIcon then
                                TabMain.UIElements.TabIcon.ImageLabel.ImageTransparency = 0.6
                            end
                        end
                        if Dropdown.Multi and typeof(Dropdown.Value) == 'string' then
                            for _, i in next, Dropdown.Values do
                                if typeof(i) == 'table' then
                                    if i.Title == Dropdown.Value then
                                        Dropdown.Value = {i}
                                    end
                                else
                                    if i == Dropdown.Value then
                                        Dropdown.Value = {
                                            Dropdown.Value,
                                        }
                                    end
                                end
                            end
                        end
                        if Dropdown.Multi then
                            local found = false

                            if typeof(Dropdown.Value) == 'table' then
                                for _, item in ipairs(Dropdown.Value)do
                                    local itemName = typeof(item) == 'table' and item.Title or item

                                    if itemName == TabMain.Name then
                                        found = true

                                        break
                                    end
                                end
                            end

                            TabMain.Selected = found
                        else
                            local currentValue = typeof(Dropdown.Value) == 'table' and Dropdown.Value.Title or Dropdown.Value

                            TabMain.Selected = currentValue == TabMain.Name
                        end
                        if TabMain.Selected and not TabMain.Locked then
                            TabMain.UIElements.TabItem.ImageTransparency = 0.95
                            TabMain.UIElements.TabItem.Highlight.ImageTransparency = 0.75
                            TabMain.UIElements.TabItem.Frame.Title.TextLabel.TextTransparency = 0

                            if TabMain.UIElements.TabIcon then
                                TabMain.UIElements.TabIcon.ImageLabel.ImageTransparency = 0
                            end
                        end

                        Dropdown.Tabs[Index] = TabMain

                        DropdownModule:Display()

                        if Type == 'Dropdown' then
                            Creator.AddSignal(TabMain.UIElements.TabItem.MouseButton1Click, function(
                            )
                                if TabMain.Locked then
                                    return
                                end
                                if Dropdown.Multi then
                                    if not TabMain.Selected then
                                        TabMain.Selected = true

                                        Tween(TabMain.UIElements.TabItem, 0.1, {ImageTransparency = 0.95}):Play()
                                        Tween(TabMain.UIElements.TabItem.Highlight, 0.1, {ImageTransparency = 0.75}):Play()
                                        Tween(TabMain.UIElements.TabItem.Frame.Title.TextLabel, 0.1, {TextTransparency = 0}):Play()

                                        if TabMain.UIElements.TabIcon then
                                            Tween(TabMain.UIElements.TabIcon.ImageLabel, 0.1, {ImageTransparency = 0}):Play()
                                        end

                                        table.insert(Dropdown.Value, TabMain.Original)
                                    else
                                        if not Dropdown.AllowNone and #Dropdown.Value == 1 then
                                            return
                                        end

                                        TabMain.Selected = false

                                        Tween(TabMain.UIElements.TabItem, 0.1, {ImageTransparency = 1}):Play()
                                        Tween(TabMain.UIElements.TabItem.Highlight, 0.1, {ImageTransparency = 1}):Play()
                                        Tween(TabMain.UIElements.TabItem.Frame.Title.TextLabel, 0.1, {TextTransparency = 0.4}):Play()

                                        if TabMain.UIElements.TabIcon then
                                            Tween(TabMain.UIElements.TabIcon.ImageLabel, 0.1, {ImageTransparency = 0.2}):Play()
                                        end

                                        for i, v in next, Dropdown.Value do
                                            if typeof(v) == 'table' and (v.Title == TabMain.Name) or (v == TabMain.Name) then
                                                table.remove(Dropdown.Value, i)

                                                break
                                            end
                                        end
                                    end
                                else
                                    for Index, TabPisun in next, Dropdown.Tabs do
                                        Tween(TabPisun.UIElements.TabItem, 0.1, {ImageTransparency = 1}):Play()
                                        Tween(TabPisun.UIElements.TabItem.Highlight, 0.1, {ImageTransparency = 1}):Play()
                                        Tween(TabPisun.UIElements.TabItem.Frame.Title.TextLabel, 0.1, {TextTransparency = 0.4}):Play()

                                        if TabPisun.UIElements.TabIcon then
                                            Tween(TabPisun.UIElements.TabIcon.ImageLabel, 0.1, {ImageTransparency = 0.2}):Play()
                                        end

                                        TabPisun.Selected = false
                                    end

                                    TabMain.Selected = true

                                    Tween(TabMain.UIElements.TabItem, 0.1, {ImageTransparency = 0.95}):Play()
                                    Tween(TabMain.UIElements.TabItem.Highlight, 0.1, {ImageTransparency = 0.75}):Play()
                                    Tween(TabMain.UIElements.TabItem.Frame.Title.TextLabel, 0.1, {TextTransparency = 0}):Play()

                                    if TabMain.UIElements.TabIcon then
                                        Tween(TabMain.UIElements.TabIcon.ImageLabel, 0.1, {ImageTransparency = 0}):Play()
                                    end

                                    Dropdown.Value = TabMain.Original
                                end

                                Callback()
                            end)
                        elseif Type == 'Menu' then
                            if not TabMain.Locked then
                                Creator.AddSignal(TabMain.UIElements.TabItem.MouseEnter, function(
                                )
                                    Tween(TabMain.UIElements.TabItem, 0.08, {ImageTransparency = 0.95}):Play()
                                end)
                                Creator.AddSignal(TabMain.UIElements.TabItem.InputEnded, function(
                                )
                                    Tween(TabMain.UIElements.TabItem, 0.08, {ImageTransparency = 1}):Play()
                                end)
                            end

                            Creator.AddSignal(TabMain.UIElements.TabItem.MouseButton1Click, function(
                            )
                                if TabMain.Locked then
                                    return
                                end

                                Callback(Tab.Callback or function() end)
                            end)
                        end

                        RecalculateCanvasSize()
                        RecalculateListSize()
                    else
                        __DARKLUA_BUNDLE_MODULES.load('K'):New({
                            Parent = Dropdown.UIElements.Menu.Frame.ScrollingFrame,
                        })
                    end
                end

                local maxWidth = Dropdown.MenuWidth or 0

                if maxWidth == 0 then
                    for _, tabmain in next, Dropdown.Tabs do
                        if tabmain.UIElements.TabItem.Frame.UIListLayout then
                            maxWidth = math.max(maxWidth, tabmain.UIElements.TabItem.Frame.UIListLayout.AbsoluteContentSize.X)
                        end
                    end
                end

                Dropdown.UIElements.MenuCanvas.Size = UDim2.new(0, maxWidth + 6 + 6 + 5 + 5 + 18 + 6 + 6, Dropdown.UIElements.MenuCanvas.Size.Y.Scale, Dropdown.UIElements.MenuCanvas.Size.Y.Offset)

                Callback()

                Dropdown.Values = Values
            end

            DropdownModule:Refresh(Dropdown.Values)

            function DropdownModule:Select(Items)
                if Items then
                    Dropdown.Value = Items
                else
                    if Dropdown.Multi then
                        Dropdown.Value = {}
                    else
                        Dropdown.Value = nil
                    end
                end

                DropdownModule:Refresh(Dropdown.Values)
            end

            RecalculateListSize()
            RecalculateCanvasSize()

            function DropdownModule:Open()
                if CanCallback then
                    Dropdown.UIElements.Menu.Visible = true
                    Dropdown.UIElements.MenuCanvas.Visible = true
                    Dropdown.UIElements.MenuCanvas.Active = true
                    Dropdown.UIElements.Menu.Size = UDim2.new(1, 0, 0, 0)

                    Tween(Dropdown.UIElements.Menu, 0.1, {
                        Size = UDim2.new(1, 0, 1, 0),
                        ImageTransparency = 0.05,
                    }, Enum.EasingStyle.Quart, Enum.EasingDirection.Out):Play()
                    task.spawn(function()
                        task.wait(0.1)

                        Dropdown.Opened = true
                    end)
                    UpdatePosition()
                end
            end
            function DropdownModule:Close()
                Dropdown.Opened = false

                Tween(Dropdown.UIElements.Menu, 0.25, {
                    Size = UDim2.new(1, 0, 0, 0),
                    ImageTransparency = 1,
                }, Enum.EasingStyle.Quart, Enum.EasingDirection.Out):Play()
                task.spawn(function()
                    task.wait(0.1)

                    Dropdown.UIElements.Menu.Visible = false
                end)
                task.spawn(function()
                    task.wait(0.25)

                    Dropdown.UIElements.MenuCanvas.Visible = false
                    Dropdown.UIElements.MenuCanvas.Active = false
                end)
            end

            Creator.AddSignal((Dropdown.UIElements.Dropdown and Dropdown.UIElements.Dropdown.MouseButton1Click or Dropdown.DropdownFrame.UIElements.Main.MouseButton1Click), function(
            )
                DropdownModule:Open()
            end)
            Creator.AddSignal(UserInputService.InputBegan, function(Input)
                if Input.UserInputType == Enum.UserInputType.MouseButton1 or Input.UserInputType == Enum.UserInputType.Touch then
                    local menuCanvas = Dropdown.UIElements.MenuCanvas
                    local AbsPos, AbsSize = menuCanvas.AbsolutePosition, menuCanvas.AbsoluteSize
                    local DropdownButton = Dropdown.UIElements.Dropdown or Dropdown.DropdownFrame.UIElements.Main
                    local ButtonAbsPos = DropdownButton.AbsolutePosition
                    local ButtonAbsSize = DropdownButton.AbsoluteSize
                    local isClickOnDropdown = Mouse.X >= ButtonAbsPos.X and Mouse.X <= ButtonAbsPos.X + ButtonAbsSize.X and Mouse.Y >= ButtonAbsPos.Y and Mouse.Y <= ButtonAbsPos.Y + ButtonAbsSize.Y
                    local isClickOnMenu = Mouse.X >= AbsPos.X and Mouse.X <= AbsPos.X + AbsSize.X and Mouse.Y >= AbsPos.Y and Mouse.Y <= AbsPos.Y + AbsSize.Y

                    if Config.Window.CanDropdown and Dropdown.Opened and not isClickOnDropdown and not isClickOnMenu then
                        DropdownModule:Close()
                    end
                end
            end)
            Creator.AddSignal(Dropdown.UIElements.Dropdown and Dropdown.UIElements.Dropdown:GetPropertyChangedSignal('AbsolutePosition') or Dropdown.DropdownFrame.UIElements.Main:GetPropertyChangedSignal('AbsolutePosition'), UpdatePosition)

            return DropdownModule
        end

        return DropdownMenu
    end
    function __DARKLUA_BUNDLE_MODULES.M()
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)

        cloneref(game:GetService('UserInputService'))
        cloneref(game:GetService('Players')).LocalPlayer:GetMouse()

        local _ = cloneref(game:GetService('Workspace')).CurrentCamera
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local _ = Creator.Tween
        local CreateLabel = __DARKLUA_BUNDLE_MODULES.load('v').New
        local _ = __DARKLUA_BUNDLE_MODULES.load('m').New
        local CreateDropdown = __DARKLUA_BUNDLE_MODULES.load('L').New
        local _ = workspace.CurrentCamera
        local Element = {
            UICorner = 10,
            UIPadding = 12,
            MenuCorner = 15,
            MenuPadding = 5,
            TabPadding = 10,
            SearchBarHeight = 39,
            TabIcon = 18,
        }

        function Element:New(Config)
            local Dropdown = {
                __type = 'Dropdown',
                Title = Config.Title or 'Dropdown',
                Desc = Config.Desc or nil,
                Locked = Config.Locked or false,
                Values = Config.Values or {},
                MenuWidth = Config.MenuWidth,
                Value = Config.Value,
                AllowNone = Config.AllowNone,
                SearchBarEnabled = Config.SearchBarEnabled or false,
                Multi = Config.Multi,
                Callback = Config.Callback or nil,
                UIElements = {},
                Opened = false,
                Tabs = {},
                Width = 150,
            }

            if Dropdown.Multi and not Dropdown.Value then
                Dropdown.Value = {}
            end

            local CanCallback = true

            Dropdown.DropdownFrame = __DARKLUA_BUNDLE_MODULES.load('B')({
                Title = Dropdown.Title,
                Desc = Dropdown.Desc,
                Parent = Config.Parent,
                TextOffset = Dropdown.Callback and Dropdown.Width or 20,
                Hover = not Dropdown.Callback and true or false,
                Tab = Config.Tab,
                Index = Config.Index,
                Window = Config.Window,
                ElementTable = Dropdown,
                ParentConfig = Config,
            })

            if Dropdown.Callback then
                Dropdown.UIElements.Dropdown = CreateLabel('', nil, Dropdown.DropdownFrame.UIElements.Main, nil, Config.Window.NewElements and 12 or 10)
                Dropdown.UIElements.Dropdown.Frame.Frame.TextLabel.TextTruncate = 'AtEnd'
                Dropdown.UIElements.Dropdown.Frame.Frame.TextLabel.Size = UDim2.new(1, Dropdown.UIElements.Dropdown.Frame.Frame.TextLabel.Size.X.Offset - 18 - 12 - 12, 0, 0)
                Dropdown.UIElements.Dropdown.Size = UDim2.new(0, Dropdown.Width, 0, 36)
                Dropdown.UIElements.Dropdown.Position = UDim2.new(1, 0, Config.Window.NewElements and 0 or 0.5, 0)
                Dropdown.UIElements.Dropdown.AnchorPoint = Vector2.new(1, Config.Window.NewElements and 0 or 0.5)
            end

            Dropdown.DropdownMenu = CreateDropdown(Config, Dropdown, Element, CanCallback, 'Dropdown')
            Dropdown.Display = Dropdown.DropdownMenu.Display
            Dropdown.Refresh = Dropdown.DropdownMenu.Refresh
            Dropdown.Select = Dropdown.DropdownMenu.Select
            Dropdown.Open = Dropdown.DropdownMenu.Open
            Dropdown.Close = Dropdown.DropdownMenu.Close

            New('ImageLabel', {
                Image = Creator.Icon('chevrons-up-down')[1],
                ImageRectOffset = Creator.Icon('chevrons-up-down')[2].ImageRectPosition,
                ImageRectSize = Creator.Icon('chevrons-up-down')[2].ImageRectSize,
                Size = UDim2.new(0, 18, 0, 18),
                Position = UDim2.new(1, Dropdown.UIElements.Dropdown and -12 or 0, 0.5, 0),
                ThemeTag = {
                    ImageColor3 = 'Icon',
                },
                AnchorPoint = Vector2.new(1, 0.5),
                Parent = Dropdown.UIElements.Dropdown and Dropdown.UIElements.Dropdown.Frame or Dropdown.DropdownFrame.UIElements.Main,
            })

            function Dropdown:Lock()
                Dropdown.Locked = true
                CanCallback = false

                return Dropdown.DropdownFrame:Lock()
            end
            function Dropdown:Unlock()
                Dropdown.Locked = false
                CanCallback = true

                return Dropdown.DropdownFrame:Unlock()
            end

            if Dropdown.Locked then
                Dropdown:Lock()
            end

            return Dropdown.__type, Dropdown
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.N()
        local highlighter = {}
        local keywords = {
            lua = {
                'and',
                'break',
                'or',
                'else',
                'elseif',
                'if',
                'then',
                'until',
                'repeat',
                'while',
                'do',
                'for',
                'in',
                'end',
                'local',
                'return',
                'function',
                'export',
            },
            rbx = {
                'game',
                'workspace',
                'script',
                'math',
                'string',
                'table',
                'task',
                'wait',
                'select',
                'next',
                'Enum',
                'tick',
                'assert',
                'shared',
                'loadstring',
                'tonumber',
                'tostring',
                'type',
                'typeof',
                'unpack',
                'Instance',
                'CFrame',
                'Vector3',
                'Vector2',
                'Color3',
                'UDim',
                'UDim2',
                'Ray',
                'BrickColor',
                'OverlapParams',
                'RaycastParams',
                'Axes',
                'Random',
                'Region3',
                'Rect',
                'TweenInfo',
                'collectgarbage',
                'not',
                'utf8',
                'pcall',
                'xpcall',
                '_G',
                'setmetatable',
                'getmetatable',
                'os',
                'pairs',
                'ipairs',
            },
            operators = {
                '#',
                '+',
                '-',
                '*',
                '%',
                '/',
                '^',
                '=',
                '~',
                '=',
                '<',
                '>',
            },
        }
        local colors = {
            numbers = Color3.fromHex('#FAB387'),
            boolean = Color3.fromHex('#FAB387'),
            operator = Color3.fromHex('#94E2D5'),
            lua = Color3.fromHex('#CBA6F7'),
            rbx = Color3.fromHex('#F38BA8'),
            str = Color3.fromHex('#A6E3A1'),
            comment = Color3.fromHex('#9399B2'),
            null = Color3.fromHex('#F38BA8'),
            call = Color3.fromHex('#89B4FA'),
            self_call = Color3.fromHex('#89B4FA'),
            local_property = Color3.fromHex('#CBA6F7'),
        }
        local createKeywordSet = function(keywords)
            local keywordSet = {}

            for _, keyword in ipairs(keywords)do
                keywordSet[keyword] = true
            end

            return keywordSet
        end
        local luaSet = createKeywordSet(keywords.lua)
        local rbxSet = createKeywordSet(keywords.rbx)
        local operatorsSet = createKeywordSet(keywords.operators)
        local getHighlight = function(tokens, index)
            local token = tokens[index]

            if colors[token .. '_color'] then
                return colors[token .. '_color']
            end
            if tonumber(token) then
                return colors.numbers
            elseif token == 'nil' then
                return colors.null
            elseif token:sub(1, 2) == '--' then
                return colors.comment
            elseif operatorsSet[token] then
                return colors.operator
            elseif luaSet[token] then
                return colors.lua
            elseif rbxSet[token] then
                return colors.rbx
            elseif token:sub(1, 1) == '"' or token:sub(1, 1) == "'" then
                return colors.str
            elseif token == 'true' or token == 'false' then
                return colors.boolean
            end
            if tokens[index + 1] == '(' then
                if tokens[index - 1] == ':' then
                    return colors.self_call
                end

                return colors.call
            end
            if tokens[index - 1] == '.' then
                if tokens[index - 2] == 'Enum' then
                    return colors.rbx
                end

                return colors.local_property
            end
        end

        function highlighter.run(source)
            local tokens = {}
            local currentToken = ''
            local inString = false
            local inComment = false
            local commentPersist = false

            for i = 1, #source do
                local character = source:sub(i, i)

                if inComment then
                    if character == '\n' and not commentPersist then
                        table.insert(tokens, currentToken)
                        table.insert(tokens, character)

                        currentToken = ''
                        inComment = false
                    elseif source:sub(i - 1, i) == ']]' and commentPersist then
                        currentToken = currentToken .. ']'

                        table.insert(tokens, currentToken)

                        currentToken = ''
                        inComment = false
                        commentPersist = false
                    else
                        currentToken = currentToken .. character
                    end
                elseif inString then
                    if character == inString and source:sub(i - 1, i - 1) ~= '\\' or character == '\n' then
                        currentToken = currentToken .. character
                        inString = false
                    else
                        currentToken = currentToken .. character
                    end
                else
                    if source:sub(i, i + 1) == '--' then
                        table.insert(tokens, currentToken)

                        currentToken = '-'
                        inComment = true
                        commentPersist = source:sub(i + 2, i + 3) == '[['
                    elseif character == '"' or character == "'" then
                        table.insert(tokens, currentToken)

                        currentToken = character
                        inString = character
                    elseif operatorsSet[character] then
                        table.insert(tokens, currentToken)
                        table.insert(tokens, character)

                        currentToken = ''
                    elseif character:match('[%w_]') then
                        currentToken = currentToken .. character
                    else
                        table.insert(tokens, currentToken)
                        table.insert(tokens, character)

                        currentToken = ''
                    end
                end
            end

            table.insert(tokens, currentToken)

            local highlighted = {}

            for i, token in ipairs(tokens)do
                local highlight = getHighlight(tokens, i)

                if highlight then
                    local syntax = string.format('<font color = "#%s">%s</font>', highlight:ToHex(), token:gsub('<', '&lt;'):gsub('>', '&gt;'))

                    table.insert(highlighted, syntax)
                else
                    table.insert(highlighted, token)
                end
            end

            return table.concat(highlighted)
        end

        return highlighter
    end
    function __DARKLUA_BUNDLE_MODULES.O()
        local Code = {}
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween
        local Highlighter = __DARKLUA_BUNDLE_MODULES.load('N')

        function Code.New(Code, Title, Parent, Callback, UIScale)
            local CodeModule = {
                Radius = 12,
                Padding = 10,
            }
            local TextLabel = New('TextLabel', {
                Text = '',
                TextColor3 = Color3.fromHex('#CDD6F4'),
                TextTransparency = 0,
                TextSize = 14,
                TextWrapped = false,
                LineHeight = 1.15,
                RichText = true,
                TextXAlignment = 'Left',
                Size = UDim2.new(0, 0, 0, 0),
                BackgroundTransparency = 1,
                AutomaticSize = 'XY',
            }, {
                New('UIPadding', {
                    PaddingTop = UDim.new(0, CodeModule.Padding + 3),
                    PaddingLeft = UDim.new(0, CodeModule.Padding + 3),
                    PaddingRight = UDim.new(0, CodeModule.Padding + 3),
                    PaddingBottom = UDim.new(0, CodeModule.Padding + 3),
                }),
            })

            TextLabel.Font = 'Code'

            local ScrollingFrame = New('ScrollingFrame', {
                Size = UDim2.new(1, 0, 0, 0),
                BackgroundTransparency = 1,
                AutomaticCanvasSize = 'X',
                ScrollingDirection = 'X',
                ElasticBehavior = 'Never',
                CanvasSize = UDim2.new(0, 0, 0, 0),
                ScrollBarThickness = 0,
            }, {TextLabel})
            local CopyButton = New('TextButton', {
                BackgroundTransparency = 1,
                Size = UDim2.new(0, 30, 0, 30),
                Position = UDim2.new(1, -CodeModule.Padding / 2, 0, CodeModule.Padding / 2),
                AnchorPoint = Vector2.new(1, 0),
                Visible = Callback and true or false,
            }, {
                Creator.NewRoundFrame(CodeModule.Radius - 4, 'Squircle', {
                    ImageColor3 = Color3.fromHex('#ffffff'),
                    ImageTransparency = 1,
                    Size = UDim2.new(1, 0, 1, 0),
                    AnchorPoint = Vector2.new(0.5, 0.5),
                    Position = UDim2.new(0.5, 0, 0.5, 0),
                    Name = 'Button',
                }, {
                    New('UIScale', {Scale = 1}),
                    New('ImageLabel', {
                        Image = Creator.Icon('copy')[1],
                        ImageRectSize = Creator.Icon('copy')[2].ImageRectSize,
                        ImageRectOffset = Creator.Icon('copy')[2].ImageRectPosition,
                        BackgroundTransparency = 1,
                        AnchorPoint = Vector2.new(0.5, 0.5),
                        Position = UDim2.new(0.5, 0, 0.5, 0),
                        Size = UDim2.new(0, 12, 0, 12),
                        ImageColor3 = Color3.fromHex('#ffffff'),
                        ImageTransparency = 0.1,
                    }),
                }),
            })

            Creator.AddSignal(CopyButton.MouseEnter, function()
                Tween(CopyButton.Button, 0.05, {ImageTransparency = 0.95}):Play()
                Tween(CopyButton.Button.UIScale, 0.05, {Scale = 0.9}):Play()
            end)
            Creator.AddSignal(CopyButton.InputEnded, function()
                Tween(CopyButton.Button, 0.08, {ImageTransparency = 1}):Play()
                Tween(CopyButton.Button.UIScale, 0.08, {Scale = 1}):Play()
            end)

            local CodeFrame = Creator.NewRoundFrame(CodeModule.Radius, 'Squircle', {
                ImageColor3 = Color3.fromHex('#212121'),
                ImageTransparency = 0.035,
                Size = UDim2.new(1, 0, 0, 20 + (CodeModule.Padding * 2)),
                AutomaticSize = 'Y',
                Parent = Parent,
            }, {
                Creator.NewRoundFrame(CodeModule.Radius, 'SquircleOutline', {
                    Size = UDim2.new(1, 0, 1, 0),
                    ImageColor3 = Color3.fromHex('#ffffff'),
                    ImageTransparency = 0.955,
                }),
                New('Frame', {
                    BackgroundTransparency = 1,
                    Size = UDim2.new(1, 0, 0, 0),
                    AutomaticSize = 'Y',
                }, {
                    Creator.NewRoundFrame(CodeModule.Radius, 'Squircle-TL-TR', {
                        ImageColor3 = Color3.fromHex('#ffffff'),
                        ImageTransparency = 0.96,
                        Size = UDim2.new(1, 0, 0, 20 + (CodeModule.Padding * 2)),
                        Visible = Title and true or false,
                    }, {
                        New('ImageLabel', {
                            Size = UDim2.new(0, 18, 0, 18),
                            BackgroundTransparency = 1,
                            Image = 'rbxassetid://132464694294269',
                            ImageColor3 = Color3.fromHex('#ffffff'),
                            ImageTransparency = 0.2,
                        }),
                        New('TextLabel', {
                            Text = Title,
                            TextColor3 = Color3.fromHex('#ffffff'),
                            TextTransparency = 0.2,
                            TextSize = 16,
                            AutomaticSize = 'Y',
                            FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                            TextXAlignment = 'Left',
                            BackgroundTransparency = 1,
                            TextTruncate = 'AtEnd',
                            Size = UDim2.new(1, CopyButton and -20 - (CodeModule.Padding * 2), 0, 0),
                        }),
                        New('UIPadding', {
                            PaddingLeft = UDim.new(0, CodeModule.Padding + 3),
                            PaddingRight = UDim.new(0, CodeModule.Padding + 3),
                        }),
                        New('UIListLayout', {
                            Padding = UDim.new(0, CodeModule.Padding),
                            FillDirection = 'Horizontal',
                            VerticalAlignment = 'Center',
                        }),
                    }),
                    ScrollingFrame,
                    New('UIListLayout', {
                        Padding = UDim.new(0, 0),
                        FillDirection = 'Vertical',
                    }),
                }),
                CopyButton,
            })

            CodeModule.CodeFrame = CodeFrame

            Creator.AddSignal(TextLabel:GetPropertyChangedSignal('TextBounds'), function(
            )
                ScrollingFrame.Size = UDim2.new(1, 0, 0, (TextLabel.TextBounds.Y / (UIScale or 1)) + ((CodeModule.Padding + 3) * 2))
            end)

            function CodeModule.Set(code)
                TextLabel.Text = Highlighter.run(code)
            end
            function CodeModule.Destroy()
                CodeFrame:Destroy()

                CodeModule = nil
            end

            CodeModule.Set(Code)
            Creator.AddSignal(CopyButton.MouseButton1Click, function()
                if Callback then
                    Callback()

                    local CheckIcon = Creator.Icon('check')

                    CopyButton.Button.ImageLabel.Image = CheckIcon[1]
                    CopyButton.Button.ImageLabel.ImageRectSize = CheckIcon[2].ImageRectSize
                    CopyButton.Button.ImageLabel.ImageRectOffset = CheckIcon[2].ImageRectPosition

                    task.wait(1)

                    local CopyIcon = Creator.Icon('copy')

                    CopyButton.Button.ImageLabel.Image = CopyIcon[1]
                    CopyButton.Button.ImageLabel.ImageRectSize = CopyIcon[2].ImageRectSize
                    CopyButton.Button.ImageLabel.ImageRectOffset = CopyIcon[2].ImageRectPosition
                end
            end)

            return CodeModule
        end

        return Code
    end
    function __DARKLUA_BUNDLE_MODULES.P()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local _ = Creator.New
        local CodeComponent = __DARKLUA_BUNDLE_MODULES.load('O')
        local Element = {}

        function Element:New(Config)
            local Code = {
                __type = 'Code',
                Title = Config.Title,
                Code = Config.Code,
                OnCopy = Config.OnCopy,
            }
            local CanCallback = not Code.Locked
            local CodeElement = CodeComponent.New(Code.Code, Code.Title, Config.Parent, function(
            )
                if CanCallback then
                    local NewTitle = Code.Title or 'code'
                    local success, result = pcall(function()
                        toclipboard(Code.Code)

                        if Code.OnCopy then
                            Code.OnCopy()
                        end
                    end)

                    if not success then
                        Config.WindUI:Notify({
                            Title = 'Error',
                            Content = 'The ' .. NewTitle .. ' is not copied. Error: ' .. result,
                            Icon = 'x',
                            Duration = 5,
                        })
                    end
                end
            end, Config.WindUI.UIScale, Code)

            function Code:SetCode(code)
                CodeElement.Set(code)

                Code.Code = code
            end
            function Code:Destroy()
                CodeElement.Destroy()

                Code = nil
            end

            Code.ElementFrame = CodeElement.CodeFrame

            return Code.__type, Code
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.Q()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local _ = Creator.Tween
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)
        local UserInputService = cloneref(game:GetService('UserInputService'))

        cloneref(game:GetService('TouchInputService'))

        local RunService = cloneref(game:GetService('RunService'))
        local Players = cloneref(game:GetService('Players'))
        local RenderStepped = RunService.RenderStepped
        local LocalPlayer = Players.LocalPlayer
        local Mouse = LocalPlayer:GetMouse()
        local CreateButton = __DARKLUA_BUNDLE_MODULES.load('l').New
        local CreateInput = __DARKLUA_BUNDLE_MODULES.load('m').New
        local Element = {UICorner = 9}

        function Element:Colorpicker(Config, Window, OnApply)
            local Colorpicker = {
                __type = 'Colorpicker',
                Title = Config.Title,
                Desc = Config.Desc,
                Default = Config.Default,
                Callback = Config.Callback,
                Transparency = Config.Transparency,
                UIElements = Config.UIElements,
                TextPadding = 10,
            }

            function Colorpicker:SetHSVFromRGB(Color)
                local H, S, V = Color3.toHSV(Color)

                Colorpicker.Hue = H
                Colorpicker.Sat = S
                Colorpicker.Vib = V
            end

            Colorpicker:SetHSVFromRGB(Colorpicker.Default)

            local ColorpickerModule = __DARKLUA_BUNDLE_MODULES.load('n').Init(Window)
            local ColorpickerFrame = ColorpickerModule.Create()

            Colorpicker.ColorpickerFrame = ColorpickerFrame
            ColorpickerFrame.UIElements.Main.Size = UDim2.new(1, 0, 0, 0)

            local Hue, Sat, Vib = Colorpicker.Hue, Colorpicker.Sat, Colorpicker.Vib

            Colorpicker.UIElements.Title = New('TextLabel', {
                Text = Colorpicker.Title,
                TextSize = 20,
                FontFace = Font.new(Creator.Font, Enum.FontWeight.SemiBold),
                TextXAlignment = 'Left',
                Size = UDim2.new(1, 0, 0, 0),
                AutomaticSize = 'Y',
                ThemeTag = {
                    TextColor3 = 'Text',
                },
                BackgroundTransparency = 1,
                Parent = ColorpickerFrame.UIElements.Main,
            }, {
                New('UIPadding', {
                    PaddingTop = UDim.new(0, Colorpicker.TextPadding / 2),
                    PaddingLeft = UDim.new(0, Colorpicker.TextPadding / 2),
                    PaddingRight = UDim.new(0, Colorpicker.TextPadding / 2),
                    PaddingBottom = UDim.new(0, Colorpicker.TextPadding / 2),
                }),
            })

            local SatCursor = New('Frame', {
                Size = UDim2.new(0, 14, 0, 14),
                AnchorPoint = Vector2.new(0.5, 0.5),
                Position = UDim2.new(0.5, 0, 0, 0),
                Parent = HueDragHolder,
                BackgroundColor3 = Colorpicker.Default,
            }, {
                New('UIStroke', {
                    Thickness = 2,
                    Transparency = 0.1,
                    ThemeTag = {
                        Color = 'Text',
                    },
                }),
                New('UICorner', {
                    CornerRadius = UDim.new(1, 0),
                }),
            })

            Colorpicker.UIElements.SatVibMap = New('ImageLabel', {
                Size = UDim2.fromOffset(160, 158),
                Position = UDim2.fromOffset(0, 40 + Colorpicker.TextPadding),
                Image = 'rbxassetid://4155801252',
                BackgroundColor3 = Color3.fromHSV(Hue, 1, 1),
                BackgroundTransparency = 0,
                Parent = ColorpickerFrame.UIElements.Main,
            }, {
                New('UICorner', {
                    CornerRadius = UDim.new(0, 8),
                }),
                Creator.NewRoundFrame(8, 'SquircleOutline', {
                    ThemeTag = {
                        ImageColor3 = 'Outline',
                    },
                    Size = UDim2.new(1, 0, 1, 0),
                    ImageTransparency = 0.85,
                    ZIndex = 99999,
                }, {
                    New('UIGradient', {
                        Rotation = 45,
                        Color = ColorSequence.new({
                            ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
                            ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 255, 255)),
                            ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255)),
                        }),
                        Transparency = NumberSequence.new({
                            NumberSequenceKeypoint.new(0, 0.1),
                            NumberSequenceKeypoint.new(0.5, 1),
                            NumberSequenceKeypoint.new(1, 0.1),
                        }),
                    }),
                }),
                SatCursor,
            })
            Colorpicker.UIElements.Inputs = New('Frame', {
                AutomaticSize = 'XY',
                Size = UDim2.new(0, 0, 0, 0),
                Position = UDim2.fromOffset(Colorpicker.Transparency and 240 or 210, 40 + Colorpicker.TextPadding),
                BackgroundTransparency = 1,
                Parent = ColorpickerFrame.UIElements.Main,
            }, {
                New('UIListLayout', {
                    Padding = UDim.new(0, 4),
                    FillDirection = 'Vertical',
                }),
            })

            local OldColorFrame = New('Frame', {
                BackgroundColor3 = Colorpicker.Default,
                Size = UDim2.fromScale(1, 1),
                BackgroundTransparency = Colorpicker.Transparency,
            }, {
                New('UICorner', {
                    CornerRadius = UDim.new(0, 8),
                }),
            })

            New('ImageLabel', {
                Image = 'http://www.roblox.com/asset/?id=14204231522',
                ImageTransparency = 0.45,
                ScaleType = Enum.ScaleType.Tile,
                TileSize = UDim2.fromOffset(40, 40),
                BackgroundTransparency = 1,
                Position = UDim2.fromOffset(85, 208 + Colorpicker.TextPadding),
                Size = UDim2.fromOffset(75, 24),
                Parent = ColorpickerFrame.UIElements.Main,
            }, {
                New('UICorner', {
                    CornerRadius = UDim.new(0, 8),
                }),
                Creator.NewRoundFrame(8, 'SquircleOutline', {
                    ThemeTag = {
                        ImageColor3 = 'Outline',
                    },
                    Size = UDim2.new(1, 0, 1, 0),
                    ImageTransparency = 0.85,
                    ZIndex = 99999,
                }, {
                    New('UIGradient', {
                        Rotation = 60,
                        Color = ColorSequence.new({
                            ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
                            ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 255, 255)),
                            ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255)),
                        }),
                        Transparency = NumberSequence.new({
                            NumberSequenceKeypoint.new(0, 0.1),
                            NumberSequenceKeypoint.new(0.5, 1),
                            NumberSequenceKeypoint.new(1, 0.1),
                        }),
                    }),
                }),
                OldColorFrame,
            })

            local NewDisplayFrame = New('Frame', {
                BackgroundColor3 = Colorpicker.Default,
                Size = UDim2.fromScale(1, 1),
                BackgroundTransparency = 0,
                ZIndex = 9,
            }, {
                New('UICorner', {
                    CornerRadius = UDim.new(0, 8),
                }),
            })

            New('ImageLabel', {
                Image = 'http://www.roblox.com/asset/?id=14204231522',
                ImageTransparency = 0.45,
                ScaleType = Enum.ScaleType.Tile,
                TileSize = UDim2.fromOffset(40, 40),
                BackgroundTransparency = 1,
                Position = UDim2.fromOffset(0, 208 + Colorpicker.TextPadding),
                Size = UDim2.fromOffset(75, 24),
                Parent = ColorpickerFrame.UIElements.Main,
            }, {
                New('UICorner', {
                    CornerRadius = UDim.new(0, 8),
                }),
                Creator.NewRoundFrame(8, 'SquircleOutline', {
                    ThemeTag = {
                        ImageColor3 = 'Outline',
                    },
                    Size = UDim2.new(1, 0, 1, 0),
                    ImageTransparency = 0.85,
                    ZIndex = 99999,
                }, {
                    New('UIGradient', {
                        Rotation = 60,
                        Color = ColorSequence.new({
                            ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
                            ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 255, 255)),
                            ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255)),
                        }),
                        Transparency = NumberSequence.new({
                            NumberSequenceKeypoint.new(0, 0.1),
                            NumberSequenceKeypoint.new(0.5, 1),
                            NumberSequenceKeypoint.new(1, 0.1),
                        }),
                    }),
                }),
                NewDisplayFrame,
            })

            local SequenceTable = {}

            for Color = 0, 1, 0.1 do
                table.insert(SequenceTable, ColorSequenceKeypoint.new(Color, Color3.fromHSV(Color, 1, 1)))
            end

            local HueSliderGradient = New('UIGradient', {
                Color = ColorSequence.new(SequenceTable),
                Rotation = 90,
            })
            local HueDragHolder = New('Frame', {
                Size = UDim2.new(1, 0, 1, 0),
                Position = UDim2.new(0, 0, 0, 0),
                BackgroundTransparency = 1,
            })
            local HueDrag = New('Frame', {
                Size = UDim2.new(0, 14, 0, 14),
                AnchorPoint = Vector2.new(0.5, 0.5),
                Position = UDim2.new(0.5, 0, 0, 0),
                Parent = HueDragHolder,
                BackgroundColor3 = Colorpicker.Default,
            }, {
                New('UIStroke', {
                    Thickness = 2,
                    Transparency = 0.1,
                    ThemeTag = {
                        Color = 'Text',
                    },
                }),
                New('UICorner', {
                    CornerRadius = UDim.new(1, 0),
                }),
            })
            local HueSlider = New('Frame', {
                Size = UDim2.fromOffset(6, 192),
                Position = UDim2.fromOffset(180, 40 + Colorpicker.TextPadding),
                Parent = ColorpickerFrame.UIElements.Main,
            }, {
                New('UICorner', {
                    CornerRadius = UDim.new(1, 0),
                }),
                HueSliderGradient,
                HueDragHolder,
            })

            function CreateNewInput(Title, Value)
                local InputFrame = CreateInput(Title, nil, Colorpicker.UIElements.Inputs)

                New('TextLabel', {
                    BackgroundTransparency = 1,
                    TextTransparency = 0.4,
                    TextSize = 17,
                    FontFace = Font.new(Creator.Font, Enum.FontWeight.Regular),
                    AutomaticSize = 'XY',
                    ThemeTag = {
                        TextColor3 = 'Placeholder',
                    },
                    AnchorPoint = Vector2.new(1, 0.5),
                    Position = UDim2.new(1, -12, 0.5, 0),
                    Parent = InputFrame.Frame,
                    Text = Title,
                })
                New('UIScale', {
                    Parent = InputFrame,
                    Scale = 0.85,
                })

                InputFrame.Frame.Frame.TextBox.Text = Value
                InputFrame.Size = UDim2.new(0, 150, 0, 42)

                return InputFrame
            end

            local ToRGB = function(color)
                return {
                    R = math.floor(color.R * 255),
                    G = math.floor(color.G * 255),
                    B = math.floor(color.B * 255),
                }
            end
            local HexInput = CreateNewInput('Hex', '#' .. Colorpicker.Default:ToHex())
            local RedInput = CreateNewInput('Red', ToRGB(Colorpicker.Default)['R'])
            local GreenInput = CreateNewInput('Green', ToRGB(Colorpicker.Default)['G'])
            local BlueInput = CreateNewInput('Blue', ToRGB(Colorpicker.Default)['B'])
            local AlphaInput

            if Colorpicker.Transparency then
                AlphaInput = CreateNewInput('Alpha', ((1 - Colorpicker.Transparency) * 100) .. '%')
            end

            local ButtonsContent = New('Frame', {
                Size = UDim2.new(1, 0, 0, 40),
                AutomaticSize = 'Y',
                Position = UDim2.new(0, 0, 0, 254 + Colorpicker.TextPadding),
                BackgroundTransparency = 1,
                Parent = ColorpickerFrame.UIElements.Main,
                LayoutOrder = 4,
            }, {
                New('UIListLayout', {
                    Padding = UDim.new(0, 6),
                    FillDirection = 'Horizontal',
                    HorizontalAlignment = 'Right',
                }),
            })
            local Buttons = {
                {
                    Title = 'Cancel',
                    Variant = 'Secondary',
                    Callback = function() end,
                },
                {
                    Title = 'Apply',
                    Icon = 'chevron-right',
                    Variant = 'Primary',
                    Callback = function()
                        OnApply(Color3.fromHSV(Colorpicker.Hue, Colorpicker.Sat, Colorpicker.Vib), Colorpicker.Transparency)
                    end,
                },
            }

            for _, Button in next, Buttons do
                local ButtonFrame = CreateButton(Button.Title, Button.Icon, Button.Callback, Button.Variant, ButtonsContent, ColorpickerFrame, false)

                ButtonFrame.Size = UDim2.new(0.5, -3, 0, 40)
                ButtonFrame.AutomaticSize = 'None'
            end

            local TransparencySlider, TransparencyDrag, TransparencyColor

            if Colorpicker.Transparency then
                local TransparencyDragHolder = New('Frame', {
                    Size = UDim2.new(1, 0, 1, 0),
                    Position = UDim2.fromOffset(0, 0),
                    BackgroundTransparency = 1,
                })

                TransparencyDrag = New('ImageLabel', {
                    Size = UDim2.new(0, 14, 0, 14),
                    AnchorPoint = Vector2.new(0.5, 0.5),
                    Position = UDim2.new(0.5, 0, 0, 0),
                    ThemeTag = {
                        BackgroundColor3 = 'Text',
                    },
                    Parent = TransparencyDragHolder,
                }, {
                    New('UIStroke', {
                        Thickness = 2,
                        Transparency = 0.1,
                        ThemeTag = {
                            Color = 'Text',
                        },
                    }),
                    New('UICorner', {
                        CornerRadius = UDim.new(1, 0),
                    }),
                })
                TransparencyColor = New('Frame', {
                    Size = UDim2.fromScale(1, 1),
                }, {
                    New('UIGradient', {
                        Transparency = NumberSequence.new({
                            NumberSequenceKeypoint.new(0, 0),
                            NumberSequenceKeypoint.new(1, 1),
                        }),
                        Rotation = 270,
                    }),
                    New('UICorner', {
                        CornerRadius = UDim.new(0, 6),
                    }),
                })
                TransparencySlider = New('Frame', {
                    Size = UDim2.fromOffset(6, 192),
                    Position = UDim2.fromOffset(210, 40 + Colorpicker.TextPadding),
                    Parent = ColorpickerFrame.UIElements.Main,
                    BackgroundTransparency = 1,
                }, {
                    New('UICorner', {
                        CornerRadius = UDim.new(1, 0),
                    }),
                    New('ImageLabel', {
                        Image = 'rbxassetid://14204231522',
                        ImageTransparency = 0.45,
                        ScaleType = Enum.ScaleType.Tile,
                        TileSize = UDim2.fromOffset(40, 40),
                        BackgroundTransparency = 1,
                        Size = UDim2.fromScale(1, 1),
                    }, {
                        New('UICorner', {
                            CornerRadius = UDim.new(1, 0),
                        }),
                    }),
                    TransparencyColor,
                    TransparencyDragHolder,
                })
            end

            function Colorpicker:Round(Number, Factor)
                if Factor == 0 then
                    return math.floor(Number)
                end

                Number = tostring(Number)

                return Number:find('%.') and tonumber(Number:sub(1, Number:find('%.') + Factor)) or Number
            end
            function Colorpicker:Update(color, transparency)
                if color then
                    Hue, Sat, Vib = Color3.toHSV(color)
                else
                    Hue, Sat, Vib = Colorpicker.Hue, Colorpicker.Sat, Colorpicker.Vib
                end

                Colorpicker.UIElements.SatVibMap.BackgroundColor3 = Color3.fromHSV(Hue, 1, 1)
                SatCursor.Position = UDim2.new(Sat, 0, 1 - Vib, 0)
                SatCursor.BackgroundColor3 = Color3.fromHSV(Hue, Sat, Vib)
                NewDisplayFrame.BackgroundColor3 = Color3.fromHSV(Hue, Sat, Vib)
                HueDrag.BackgroundColor3 = Color3.fromHSV(Hue, 1, 1)
                HueDrag.Position = UDim2.new(0.5, 0, Hue, 0)
                HexInput.Frame.Frame.TextBox.Text = '#' .. Color3.fromHSV(Hue, Sat, Vib):ToHex()
                RedInput.Frame.Frame.TextBox.Text = ToRGB(Color3.fromHSV(Hue, Sat, Vib))['R']
                GreenInput.Frame.Frame.TextBox.Text = ToRGB(Color3.fromHSV(Hue, Sat, Vib))['G']
                BlueInput.Frame.Frame.TextBox.Text = ToRGB(Color3.fromHSV(Hue, Sat, Vib))['B']

                if transparency or Colorpicker.Transparency then
                    NewDisplayFrame.BackgroundTransparency = Colorpicker.Transparency or transparency
                    TransparencyColor.BackgroundColor3 = Color3.fromHSV(Hue, Sat, Vib)
                    TransparencyDrag.BackgroundColor3 = Color3.fromHSV(Hue, Sat, Vib)
                    TransparencyDrag.BackgroundTransparency = Colorpicker.Transparency or transparency
                    TransparencyDrag.Position = UDim2.new(0.5, 0, 1 - Colorpicker.Transparency or transparency, 0)
                    AlphaInput.Frame.Frame.TextBox.Text = Colorpicker:Round((1 - Colorpicker.Transparency or transparency) * 100, 0) .. '%'
                end
            end

            Colorpicker:Update(Colorpicker.Default, Colorpicker.Transparency)

            local GetRGB = function()
                local Value = Color3.fromHSV(Colorpicker.Hue, Colorpicker.Sat, Colorpicker.Vib)

                return {
                    R = math.floor(Value.r * 255),
                    G = math.floor(Value.g * 255),
                    B = math.floor(Value.b * 255),
                }
            end
            local clamp = function(val, min, max)
                return math.clamp(tonumber(val) or 0, min, max)
            end

            Creator.AddSignal(HexInput.Frame.Frame.TextBox.FocusLost, function(
                Enter
            )
                if Enter then
                    local hex = HexInput.Frame.Frame.TextBox.Text:gsub('#', '')
                    local Success, Result = pcall(Color3.fromHex, hex)

                    if Success and typeof(Result) == 'Color3' then
                        Colorpicker.Hue, Colorpicker.Sat, Colorpicker.Vib = Color3.toHSV(Result)

                        Colorpicker:Update()

                        Colorpicker.Default = Result
                    end
                end
            end)

            local updateColorFromInput = function(inputBox, component)
                Creator.AddSignal(inputBox.Frame.Frame.TextBox.FocusLost, function(
                    Enter
                )
                    if Enter then
                        local textBox = inputBox.Frame.Frame.TextBox
                        local current = GetRGB()
                        local clamped = clamp(textBox.Text, 0, 255)

                        textBox.Text = tostring(clamped)
                        current[component] = clamped

                        local Result = Color3.fromRGB(current.R, current.G, current.B)

                        Colorpicker.Hue, Colorpicker.Sat, Colorpicker.Vib = Color3.toHSV(Result)

                        Colorpicker:Update()
                    end
                end)
            end

            updateColorFromInput(RedInput, 'R')
            updateColorFromInput(GreenInput, 'G')
            updateColorFromInput(BlueInput, 'B')

            if Colorpicker.Transparency then
                Creator.AddSignal(AlphaInput.Frame.Frame.TextBox.FocusLost, function(
                    Enter
                )
                    if Enter then
                        local textBox = AlphaInput.Frame.Frame.TextBox
                        local clamped = clamp(textBox.Text, 0, 100)

                        textBox.Text = tostring(clamped)
                        Colorpicker.Transparency = 1 - clamped * 0.01

                        Colorpicker:Update(nil, Colorpicker.Transparency)
                    end
                end)
            end

            local SatVibMap = Colorpicker.UIElements.SatVibMap

            Creator.AddSignal(SatVibMap.InputBegan, function(Input)
                if Input.UserInputType == Enum.UserInputType.MouseButton1 or Input.UserInputType == Enum.UserInputType.Touch then
                    while UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) do
                        local MinX = SatVibMap.AbsolutePosition.X
                        local MaxX = MinX + SatVibMap.AbsoluteSize.X
                        local MouseX = math.clamp(Mouse.X, MinX, MaxX)
                        local MinY = SatVibMap.AbsolutePosition.Y
                        local MaxY = MinY + SatVibMap.AbsoluteSize.Y
                        local MouseY = math.clamp(Mouse.Y, MinY, MaxY)

                        Colorpicker.Sat = (MouseX - MinX) / (MaxX - MinX)
                        Colorpicker.Vib = 1 - ((MouseY - MinY) / (MaxY - MinY))

                        Colorpicker:Update()
                        RenderStepped:Wait()
                    end
                end
            end)
            Creator.AddSignal(HueSlider.InputBegan, function(Input)
                if Input.UserInputType == Enum.UserInputType.MouseButton1 or Input.UserInputType == Enum.UserInputType.Touch then
                    while UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) do
                        local MinY = HueSlider.AbsolutePosition.Y
                        local MaxY = MinY + HueSlider.AbsoluteSize.Y
                        local MouseY = math.clamp(Mouse.Y, MinY, MaxY)

                        Colorpicker.Hue = ((MouseY - MinY) / (MaxY - MinY))

                        Colorpicker:Update()
                        RenderStepped:Wait()
                    end
                end
            end)

            if Colorpicker.Transparency then
                Creator.AddSignal(TransparencySlider.InputBegan, function(
                    Input
                )
                    if Input.UserInputType == Enum.UserInputType.MouseButton1 or Input.UserInputType == Enum.UserInputType.Touch then
                        while UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) do
                            local MinY = TransparencySlider.AbsolutePosition.Y
                            local MaxY = MinY + TransparencySlider.AbsoluteSize.Y
                            local MouseY = math.clamp(Mouse.Y, MinY, MaxY)

                            Colorpicker.Transparency = 1 - ((MouseY - MinY) / (MaxY - MinY))

                            Colorpicker:Update()
                            RenderStepped:Wait()
                        end
                    end
                end)
            end

            return Colorpicker
        end
        function Element:New(Config)
            local Colorpicker = {
                __type = 'Colorpicker',
                Title = Config.Title or 'Colorpicker',
                Desc = Config.Desc or nil,
                Locked = Config.Locked or false,
                Default = Config.Default or Color3.new(1, 1, 1),
                Callback = Config.Callback or function() end,
                UIScale = Config.UIScale,
                Transparency = Config.Transparency,
                UIElements = {},
            }
            local CanCallback = true

            Colorpicker.ColorpickerFrame = __DARKLUA_BUNDLE_MODULES.load('B')({
                Title = Colorpicker.Title,
                Desc = Colorpicker.Desc,
                Parent = Config.Parent,
                TextOffset = 40,
                Hover = false,
                Tab = Config.Tab,
                Index = Config.Index,
                Window = Config.Window,
                ElementTable = Colorpicker,
                ParentConfig = Config,
            })
            Colorpicker.UIElements.Colorpicker = Creator.NewRoundFrame(Element.UICorner, 'Squircle', {
                ImageTransparency = 0,
                Active = true,
                ImageColor3 = Colorpicker.Default,
                Parent = Colorpicker.ColorpickerFrame.UIElements.Main,
                Size = UDim2.new(0, 26, 0, 26),
                AnchorPoint = Vector2.new(1, 0),
                Position = UDim2.new(1, 0, 0, 0),
                ZIndex = 2,
            }, nil, true)

            function Colorpicker:Lock()
                Colorpicker.Locked = true
                CanCallback = false

                return Colorpicker.ColorpickerFrame:Lock()
            end
            function Colorpicker:Unlock()
                Colorpicker.Locked = false
                CanCallback = true

                return Colorpicker.ColorpickerFrame:Unlock()
            end

            if Colorpicker.Locked then
                Colorpicker:Lock()
            end

            function Colorpicker:Update(Color, Transparency)
                Colorpicker.UIElements.Colorpicker.ImageTransparency = Transparency or 0
                Colorpicker.UIElements.Colorpicker.ImageColor3 = Color
                Colorpicker.Default = Color

                if Transparency then
                    Colorpicker.Transparency = Transparency
                end
            end
            function Colorpicker:Set(c, t)
                return Colorpicker:Update(c, t)
            end

            Creator.AddSignal(Colorpicker.UIElements.Colorpicker.MouseButton1Click, function(
            )
                if CanCallback then
                    Element:Colorpicker(Colorpicker, Config.Window, function(
                        color,
                        transparency
                    )
                        Colorpicker:Update(color, transparency)

                        Colorpicker.Default = color
                        Colorpicker.Transparency = transparency

                        Creator.SafeCallback(Colorpicker.Callback, color, transparency)
                    end).ColorpickerFrame:Open()
                end
            end)

            return Colorpicker.__type, Colorpicker
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.R()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween
        local Element = {}

        function Element:New(Config)
            local Section = {
                __type = 'Section',
                Title = Config.Title or 'Section',
                Icon = Config.Icon,
                TextXAlignment = Config.TextXAlignment or 'Left',
                TextSize = Config.TextSize or 19,
                Box = Config.Box or false,
                FontWeight = Config.FontWeight or Enum.FontWeight.SemiBold,
                TextTransparency = Config.TextTransparency or 0.05,
                Opened = Config.Opened or false,
                UIElements = {},
                HeaderSize = 42,
                IconSize = 20,
                Padding = 10,
                Elements = {},
                Expandable = false,
            }
            local Icon

            function Section:SetIcon(i)
                Section.Icon = i or nil

                if Icon then
                    Icon:Destroy()
                end
                if i then
                    Icon = Creator.Image(i, i .. ':' .. Section.Title, 0, Config.Window.Folder, Section.__type, true)
                    Icon.Size = UDim2.new(0, Section.IconSize, 0, Section.IconSize)
                end
            end

            local ChevronIconFrame = New('Frame', {
                Size = UDim2.new(0, Section.IconSize, 0, Section.IconSize),
                BackgroundTransparency = 1,
                Visible = false,
            }, {
                New('ImageLabel', {
                    Size = UDim2.new(1, 0, 1, 0),
                    BackgroundTransparency = 1,
                    Image = Creator.Icon('chevron-down')[1],
                    ImageRectSize = Creator.Icon('chevron-down')[2].ImageRectSize,
                    ImageRectOffset = Creator.Icon('chevron-down')[2].ImageRectPosition,
                    ThemeTag = {
                        ImageColor3 = 'Icon',
                    },
                    ImageTransparency = 0.7,
                }),
            })

            if Section.Icon then
                Section:SetIcon(Section.Icon)
            end

            local TitleFrame = New('TextLabel', {
                BackgroundTransparency = 1,
                TextXAlignment = Section.TextXAlignment,
                AutomaticSize = 'Y',
                TextSize = Section.TextSize,
                TextTransparency = Section.TextTransparency,
                ThemeTag = {
                    TextColor3 = 'Text',
                },
                FontFace = Font.new(Creator.Font, Section.FontWeight),
                Text = Section.Title,
                Size = UDim2.new(1, 0, 0, 0),
                TextWrapped = true,
            })
            local UpdateTitleSize = function()
                local offset = 0

                if Icon then
                    offset = offset - (Section.IconSize + 8)
                end
                if ChevronIconFrame.Visible then
                    offset = offset - (Section.IconSize + 8)
                end

                TitleFrame.Size = UDim2.new(1, offset, 0, 0)
            end
            local Main = Creator.NewRoundFrame(Config.Window.ElementConfig.UICorner, 'Squircle', {
                Size = UDim2.new(1, 0, 0, 0),
                BackgroundTransparency = 1,
                Parent = Config.Parent,
                ClipsDescendants = true,
                AutomaticSize = 'Y',
                ImageTransparency = Section.Box and 0.93 or 1,
                ThemeTag = {
                    ImageColor3 = 'Text',
                },
            }, {
                New('TextButton', {
                    Size = UDim2.new(1, 0, 0, Expandable and 0 or Section.HeaderSize),
                    BackgroundTransparency = 1,
                    AutomaticSize = Expandable and nil or 'Y',
                    Text = '',
                    Name = 'Top',
                }, {
                    Section.Box and New('UIPadding', {
                        PaddingLeft = UDim.new(0, Config.Window.ElementConfig.UIPadding),
                        PaddingRight = UDim.new(0, Config.Window.ElementConfig.UIPadding),
                    }) or nil,
                    Icon,
                    TitleFrame,
                    New('UIListLayout', {
                        Padding = UDim.new(0, 8),
                        FillDirection = 'Horizontal',
                        VerticalAlignment = 'Center',
                        HorizontalAlignment = 'Left',
                    }),
                    ChevronIconFrame,
                }),
                New('Frame', {
                    BackgroundTransparency = 1,
                    Size = UDim2.new(1, 0, 0, 0),
                    AutomaticSize = 'Y',
                    Name = 'Content',
                    Visible = false,
                    Position = UDim2.new(0, 0, 0, Section.HeaderSize),
                }, {
                    Section.Box and New('UIPadding', {
                        PaddingLeft = UDim.new(0, Config.Window.ElementConfig.UIPadding),
                        PaddingRight = UDim.new(0, Config.Window.ElementConfig.UIPadding),
                        PaddingBottom = UDim.new(0, Config.Window.ElementConfig.UIPadding),
                    }) or nil,
                    New('UIListLayout', {
                        FillDirection = 'Vertical',
                        Padding = UDim.new(0, Config.Tab.Gap),
                        VerticalAlignment = 'Top',
                    }),
                }),
            })
            local ElementsModule = Config.ElementsModule

            ElementsModule.Load(Section, Main.Content, ElementsModule.Elements, Config.Window, Config.WindUI, function(
            )
                if not Section.Expandable then
                    Section.Expandable = true
                    ChevronIconFrame.Visible = true

                    UpdateTitleSize()
                end
            end, ElementsModule, Config.UIScale, Config.Tab)
            UpdateTitleSize()

            function Section:SetTitle(Title)
                TitleFrame.Text = Title
            end
            function Section:Destroy()
                for _, element in next, Section.Elements do
                    element:Destroy()
                end

                Main:Destroy()
            end
            function Section:Open()
                if Section.Expandable then
                    Section.Opened = true

                    Tween(Main, 0.33, {
                        Size = UDim2.new(1, 0, 0, Section.HeaderSize + (Main.Content.AbsoluteSize.Y / Config.UIScale)),
                    }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                    Tween(ChevronIconFrame.ImageLabel, 0.1, {Rotation = 180}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                end
            end
            function Section:Close()
                if Section.Expandable then
                    Section.Opened = false

                    Tween(Main, 0.26, {
                        Size = UDim2.new(1, 0, 0, Section.HeaderSize),
                    }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                    Tween(ChevronIconFrame.ImageLabel, 0.1, {Rotation = 0}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                end
            end

            Creator.AddSignal(Main.Top.MouseButton1Click, function()
                if Section.Expandable then
                    if Section.Opened then
                        Section:Close()
                    else
                        Section:Open()
                    end
                end
            end)
            Creator.AddSignal(Main.Content.UIListLayout:GetPropertyChangedSignal('AbsoluteContentSize'), function(
            )
                if Section.Opened then
                    Section:Open()
                end
            end)
            task.spawn(function()
                task.wait(0.02)

                if Section.Expandable then
                    Main.Size = UDim2.new(1, 0, 0, Section.HeaderSize)
                    Main.AutomaticSize = 'None'
                    Main.Top.Size = UDim2.new(1, 0, 0, Section.HeaderSize)
                    Main.Top.AutomaticSize = 'None'
                    Main.Content.Visible = true
                end
                if Section.Opened then
                    Section:Open()
                end
            end)

            return Section.__type, Section
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.S()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Element = {}

        function Element:New(Config)
            local MainSpace = New('Frame', {
                Parent = Config.Parent,
                Size = Config.ParentType ~= 'Group' and UDim2.new(1, -7, 0, 7 * (Config.Columns or 1)) or UDim2.new(0, 7 * (Config.Columns or 1), 0, 0),
                BackgroundTransparency = 1,
            })

            return 'Space', {
                __type = 'Space',
                ElementFrame = MainSpace,
            }
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.T()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Element = {}
        local ParseAspectRatio = function(aspectRatio)
            if type(aspectRatio) == 'string' then
                local width, height = aspectRatio:match('(%d+):(%d+)')

                if width and height then
                    return tonumber(width) / tonumber(height)
                end
            elseif type(aspectRatio) == 'number' then
                return aspectRatio
            end

            return nil
        end

        function Element:New(Config)
            local ImageModule = {
                __type = 'Image',
                Image = Config.Image or '',
                AspectRatio = Config.AspectRatio or '16:9',
                Radius = Config.Radius or Config.Window.ElementConfig.UICorner,
            }
            local MainImage = Creator.Image(ImageModule.Image, ImageModule.Image, ImageModule.Radius, Config.Window.Folder, 'Image', false)

            MainImage.Parent = Config.Parent
            MainImage.Size = UDim2.new(1, 0, 0, 0)
            MainImage.BackgroundTransparency = 1

            local aspectRatio = ParseAspectRatio(ImageModule.AspectRatio)
            local aspectRatioConstraint = nil

            if aspectRatio then
                aspectRatioConstraint = New('UIAspectRatioConstraint', {
                    Parent = MainImage,
                    AspectRatio = aspectRatio,
                    AspectType = 'ScaleWithParentSize',
                    DominantAxis = 'Width',
                })
            end

            function ImageModule:Destroy()
                MainImage:Destroy()
            end

            return ImageModule.__type, ImageModule
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.U()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Element = {}

        function Element:New(Config)
            local GroupModule = {
                __type = 'Group',
                Elements = {},
            }
            local GroupFrame = New('Frame', {
                Size = UDim2.new(1, 0, 0, 0),
                BackgroundTransparency = 1,
                AutomaticSize = 'Y',
                Parent = Config.Parent,
            }, {
                New('UIListLayout', {
                    FillDirection = 'Horizontal',
                    HorizontalAlignment = 'Center',
                    VerticalAlignment = 'Center',
                    Padding = UDim.new(0, Config.Tab and Config.Tab.Gap or (Window.NewElements and 1 or 6)),
                }),
            })
            local ElementsModule = Config.ElementsModule

            ElementsModule.Load(GroupModule, GroupFrame, ElementsModule.Elements, Config.Window, Config.WindUI, function(
                CurrentElement,
                AllElements
            )
                local Gap = Config.Tab and Config.Tab.Gap or (Config.Window.NewElements and 1 or 6)
                local StretchableElements = {}
                local TotalFixedWidth = 0

                for _, Element in next, AllElements do
                    if Element.__type == 'Space' then
                        TotalFixedWidth = TotalFixedWidth + (Element.ElementFrame.Size.X.Offset or 6)
                    elseif Element.__type == 'Divider' then
                        TotalFixedWidth = TotalFixedWidth + (Element.ElementFrame.Size.X.Offset or 1)
                    else
                        table.insert(StretchableElements, Element)
                    end
                end

                local StretchCount = #StretchableElements

                if StretchCount == 0 then
                    return
                end

                local ElementWidthScale = 1 / StretchCount
                local TotalGapWidth = Gap * (StretchCount - 1)
                local TotalOffset = -(TotalGapWidth + TotalFixedWidth)
                local BaseOffset = math.floor(TotalOffset / StretchCount)
                local Remainder = TotalOffset - (BaseOffset * StretchCount)

                for i, Element in next, StretchableElements do
                    local Offset = BaseOffset

                    if i <= math.abs(Remainder) then
                        Offset = Offset - 1
                    end
                    if Element.ElementFrame then
                        Element.ElementFrame.Size = UDim2.new(ElementWidthScale, Offset, 1, 0)
                    end
                end
            end, ElementsModule, Config.UIScale, Config.Tab)

            return GroupModule.__type, GroupModule
        end

        return Element
    end
    function __DARKLUA_BUNDLE_MODULES.V()
        return {
            Elements = {
                Paragraph = __DARKLUA_BUNDLE_MODULES.load('C'),
                Button = __DARKLUA_BUNDLE_MODULES.load('D'),
                Toggle = __DARKLUA_BUNDLE_MODULES.load('G'),
                Slider = __DARKLUA_BUNDLE_MODULES.load('H'),
                Keybind = __DARKLUA_BUNDLE_MODULES.load('I'),
                Input = __DARKLUA_BUNDLE_MODULES.load('J'),
                Dropdown = __DARKLUA_BUNDLE_MODULES.load('M'),
                Code = __DARKLUA_BUNDLE_MODULES.load('P'),
                Colorpicker = __DARKLUA_BUNDLE_MODULES.load('Q'),
                Section = __DARKLUA_BUNDLE_MODULES.load('R'),
                Divider = __DARKLUA_BUNDLE_MODULES.load('K'),
                Space = __DARKLUA_BUNDLE_MODULES.load('S'),
                Image = __DARKLUA_BUNDLE_MODULES.load('T'),
                Group = __DARKLUA_BUNDLE_MODULES.load('U'),
            },
            Load = function(
                tbl,
                Container,
                Elements,
                Window,
                WindUI,
                OnElementCreateFunction,
                ElementsModule,
                UIScale,
                Tab
            )
                for name, module in next, Elements do
                    tbl[name] = function(self, config)
                        config = config or {}
                        config.Tab = Tab or tbl
                        config.ParentType = tbl.__type
                        config.ParentTable = tbl
                        config.Index = #tbl.Elements + 1
                        config.GlobalIndex = #Window.AllElements + 1
                        config.Parent = Container
                        config.Window = Window
                        config.WindUI = WindUI
                        config.UIScale = UIScale
                        config.ElementsModule = ElementsModule

                        local elementInstance, content = module:New(config)

                        if config.Flag and typeof(config.Flag) == 'string' then
                            if Window.CurrentConfig then
                                Window.CurrentConfig:Register(config.Flag, content)

                                if Window.PendingConfigData and Window.PendingConfigData[config.Flag] then
                                    local data = Window.PendingConfigData[config.Flag]
                                    local ConfigManager = Window.ConfigManager

                                    if ConfigManager.Parser[data.__type] then
                                        task.defer(function()
                                            local success, err = pcall(function()
                                                ConfigManager.Parser[data.__type].Load(content, data)
                                            end)

                                            if success then
                                                Window.PendingConfigData[config.Flag] = nil
                                            else
                                                warn("[ WindUI ] Failed to apply pending config for '" .. config.Flag .. "': " .. tostring(err))
                                            end
                                        end)
                                    end
                                end
                            else
                                Window.PendingFlags = Window.PendingFlags or {}
                                Window.PendingFlags[config.Flag] = content
                            end
                        end

                        local frame

                        for key, value in pairs(content)do
                            if typeof(value) == 'table' and key:match('Frame$') then
                                frame = value

                                break
                            end
                        end

                        if frame then
                            content.ElementFrame = frame.UIElements.Main

                            function content:SetTitle(title)
                                frame:SetTitle(title)
                            end
                            function content:SetDesc(desc)
                                frame:SetDesc(desc)
                            end
                            function content:Highlight()
                                frame:Highlight()
                            end
                            function content:Destroy()
                                frame:Destroy()
                                table.remove(Window.AllElements, config.GlobalIndex)
                                table.remove(tbl.Elements, config.Index)
                                table.remove(Tab.Elements, config.Index)
                                tbl:UpdateAllElementShapes(tbl)
                            end
                        end

                        Window.AllElements[config.Index] = content
                        tbl.Elements[config.Index] = content

                        if Tab then
                            Tab.Elements[config.Index] = content
                        end
                        if Window.NewElements then
                            tbl:UpdateAllElementShapes(tbl)
                        end
                        if OnElementCreateFunction then
                            OnElementCreateFunction(content, tbl.Elements)
                        end

                        return content
                    end
                end

                function tbl:UpdateAllElementShapes(bbb)
                    for i, element in next, bbb.Elements do
                        local frame

                        for key, value in pairs(element)do
                            if typeof(value) == 'table' and key:match('Frame$') then
                                frame = value

                                break
                            end
                        end

                        if frame then
                            frame.Index = i

                            if frame.UpdateShape then
                                frame.UpdateShape(bbb)
                            end
                        end
                    end
                end
            end,
        }
    end
    function __DARKLUA_BUNDLE_MODULES.W()
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)

        cloneref(game:GetService('UserInputService'))

        local Mouse = game.Players.LocalPlayer:GetMouse()
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween
        local CreateToolTip = __DARKLUA_BUNDLE_MODULES.load('A').New
        local CreateScrollSlider = __DARKLUA_BUNDLE_MODULES.load('w').New
        local TabModule = {
            Tabs = {},
            Containers = {},
            SelectedTab = nil,
            TabCount = 0,
            ToolTipParent = nil,
            TabHighlight = nil,
            OnChangeFunc = function(v) end,
        }

        function TabModule.Init(
            WindowTable,
            WindUITable,
            ToolTipParent,
            TabHighlight
        )
            Window = WindowTable
            WindUI = WindUITable
            TabModule.ToolTipParent = ToolTipParent
            TabModule.TabHighlight = TabHighlight

            return TabModule
        end
        function TabModule.New(Config, UIScale)
            local Tab = {
                __type = 'Tab',
                Title = Config.Title or 'Tab',
                Desc = Config.Desc,
                Icon = Config.Icon,
                IconThemed = Config.IconThemed,
                Locked = Config.Locked,
                ShowTabTitle = Config.ShowTabTitle,
                Selected = false,
                Index = nil,
                Parent = Config.Parent,
                UIElements = {},
                Elements = {},
                ContainerFrame = nil,
                UICorner = Window.UICorner - (Window.UIPadding / 2),
                Gap = Window.NewElements and 1 or 6,
            }

            TabModule.TabCount = TabModule.TabCount + 1

            local TabIndex = TabModule.TabCount

            Tab.Index = TabIndex
            Tab.UIElements.Main = Creator.NewRoundFrame(Tab.UICorner, 'Squircle', {
                BackgroundTransparency = 1,
                Size = UDim2.new(1, -7, 0, 0),
                AutomaticSize = 'Y',
                Parent = Config.Parent,
                ThemeTag = {
                    ImageColor3 = 'TabBackground',
                },
                ImageTransparency = 1,
            }, {
                Creator.NewRoundFrame(Tab.UICorner, 'SquircleOutline', {
                    Size = UDim2.new(1, 0, 1, 0),
                    ThemeTag = {
                        ImageColor3 = 'Text',
                    },
                    ImageTransparency = 1,
                    Name = 'Outline',
                }, {
                    New('UIGradient', {
                        Rotation = 80,
                        Color = ColorSequence.new({
                            ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
                            ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 255, 255)),
                            ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255)),
                        }),
                        Transparency = NumberSequence.new({
                            NumberSequenceKeypoint.new(0, 0.1),
                            NumberSequenceKeypoint.new(0.5, 1),
                            NumberSequenceKeypoint.new(1, 0.1),
                        }),
                    }),
                }),
                Creator.NewRoundFrame(Tab.UICorner, 'Squircle', {
                    Size = UDim2.new(1, 0, 0, 0),
                    AutomaticSize = 'Y',
                    ThemeTag = {
                        ImageColor3 = 'Text',
                    },
                    ImageTransparency = 1,
                    Name = 'Frame',
                }, {
                    New('UIListLayout', {
                        SortOrder = 'LayoutOrder',
                        Padding = UDim.new(0, 10),
                        FillDirection = 'Horizontal',
                        VerticalAlignment = 'Center',
                    }),
                    New('TextLabel', {
                        Text = Tab.Title,
                        ThemeTag = {
                            TextColor3 = 'TabTitle',
                        },
                        TextTransparency = not Tab.Locked and 0.4 or 0.7,
                        TextSize = 15,
                        Size = UDim2.new(1, 0, 0, 0),
                        FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                        TextWrapped = true,
                        RichText = true,
                        AutomaticSize = 'Y',
                        LayoutOrder = 2,
                        TextXAlignment = 'Left',
                        BackgroundTransparency = 1,
                    }),
                    New('UIPadding', {
                        PaddingTop = UDim.new(0, 2 + (Window.UIPadding / 2)),
                        PaddingLeft = UDim.new(0, 4 + (Window.UIPadding / 2)),
                        PaddingRight = UDim.new(0, 4 + (Window.UIPadding / 2)),
                        PaddingBottom = UDim.new(0, 2 + (Window.UIPadding / 2)),
                    }),
                }),
            }, true)

            local TextOffset = 0
            local Icon
            local Icon2

            if Tab.Icon then
                Icon = Creator.Image(Tab.Icon, Tab.Icon .. ':' .. Tab.Title, 0, Window.Folder, Tab.__type, true, Tab.IconThemed, 'TabIcon')
                Icon.Size = UDim2.new(0, 16, 0, 16)
                Icon.Parent = Tab.UIElements.Main.Frame
                Icon.ImageLabel.ImageTransparency = not Tab.Locked and 0 or 0.7
                Tab.UIElements.Main.Frame.TextLabel.Size = UDim2.new(1, -30, 0, 0)
                TextOffset = -30
                Tab.UIElements.Icon = Icon
                Icon2 = Creator.Image(Tab.Icon, Tab.Icon .. ':' .. Tab.Title, 0, Window.Folder, Tab.__type, true, Tab.IconThemed)
                Icon2.Size = UDim2.new(0, 16, 0, 16)
                Icon2.ImageLabel.ImageTransparency = not Tab.Locked and 0 or 0.7
                TextOffset = -30
            end

            Tab.UIElements.ContainerFrame = New('ScrollingFrame', {
                Size = UDim2.new(1, 0, 1, Tab.ShowTabTitle and -((Window.UIPadding * 2.4) + 12) or 0),
                BackgroundTransparency = 1,
                ScrollBarThickness = 0,
                ElasticBehavior = 'Never',
                CanvasSize = UDim2.new(0, 0, 0, 0),
                AnchorPoint = Vector2.new(0, 1),
                Position = UDim2.new(0, 0, 1, 0),
                AutomaticCanvasSize = 'Y',
                ScrollingDirection = 'Y',
            }, {
                New('UIPadding', {
                    PaddingTop = UDim.new(0, not Window.HidePanelBackground and 20 or 10),
                    PaddingLeft = UDim.new(0, not Window.HidePanelBackground and 20 or 10),
                    PaddingRight = UDim.new(0, not Window.HidePanelBackground and 20 or 10),
                    PaddingBottom = UDim.new(0, not Window.HidePanelBackground and 20 or 10),
                }),
                New('UIListLayout', {
                    SortOrder = 'LayoutOrder',
                    Padding = UDim.new(0, Tab.Gap),
                    HorizontalAlignment = 'Center',
                }),
            })
            Tab.UIElements.ContainerFrameCanvas = New('Frame', {
                Size = UDim2.new(1, 0, 1, 0),
                BackgroundTransparency = 1,
                Visible = false,
                Parent = Window.UIElements.MainBar,
                ZIndex = 5,
            }, {
                Tab.UIElements.ContainerFrame,
                New('Frame', {
                    Size = UDim2.new(1, 0, 0, ((Window.UIPadding * 2.4) + 12)),
                    BackgroundTransparency = 1,
                    Visible = Tab.ShowTabTitle or false,
                    Name = 'TabTitle',
                }, {
                    Icon2,
                    New('TextLabel', {
                        Text = Tab.Title,
                        ThemeTag = {
                            TextColor3 = 'Text',
                        },
                        TextSize = 20,
                        TextTransparency = 0.1,
                        Size = UDim2.new(1, -TextOffset, 1, 0),
                        FontFace = Font.new(Creator.Font, Enum.FontWeight.SemiBold),
                        TextTruncate = 'AtEnd',
                        RichText = true,
                        LayoutOrder = 2,
                        TextXAlignment = 'Left',
                        BackgroundTransparency = 1,
                    }),
                    New('UIPadding', {
                        PaddingTop = UDim.new(0, 20),
                        PaddingLeft = UDim.new(0, 20),
                        PaddingRight = UDim.new(0, 20),
                        PaddingBottom = UDim.new(0, 20),
                    }),
                    New('UIListLayout', {
                        SortOrder = 'LayoutOrder',
                        Padding = UDim.new(0, 10),
                        FillDirection = 'Horizontal',
                        VerticalAlignment = 'Center',
                    }),
                }),
                New('Frame', {
                    Size = UDim2.new(1, 0, 0, 1),
                    BackgroundTransparency = 0.9,
                    ThemeTag = {
                        BackgroundColor3 = 'Text',
                    },
                    Position = UDim2.new(0, 0, 0, ((Window.UIPadding * 2.4) + 12)),
                    Visible = Tab.ShowTabTitle or false,
                }),
            })
            TabModule.Containers[TabIndex] = Tab.UIElements.ContainerFrameCanvas
            TabModule.Tabs[TabIndex] = Tab
            Tab.ContainerFrame = ContainerFrameCanvas

            Creator.AddSignal(Tab.UIElements.Main.MouseButton1Click, function()
                if not Tab.Locked then
                    TabModule:SelectTab(TabIndex)
                end
            end)

            if Window.ScrollBarEnabled then
                CreateScrollSlider(Tab.UIElements.ContainerFrame, Tab.UIElements.ContainerFrameCanvas, Window, 3)
            end

            local ToolTip
            local hoverTimer
            local MouseConn
            local IsHovering = false

            if Tab.Desc then
                Creator.AddSignal(Tab.UIElements.Main.InputBegan, function()
                    IsHovering = true
                    hoverTimer = task.spawn(function()
                        task.wait(0.35)

                        if IsHovering and not ToolTip then
                            ToolTip = CreateToolTip(Tab.Desc, TabModule.ToolTipParent)

                            local updatePosition = function()
                                if ToolTip then
                                    ToolTip.Container.Position = UDim2.new(0, Mouse.X, 0, Mouse.Y - 20)
                                end
                            end

                            updatePosition()

                            MouseConn = Mouse.Move:Connect(updatePosition)

                            ToolTip:Open()
                        end
                    end)
                end)
            end

            Creator.AddSignal(Tab.UIElements.Main.MouseEnter, function()
                if not Tab.Locked then
                    Tween(Tab.UIElements.Main.Frame, 0.08, {ImageTransparency = 0.97}):Play()
                end
            end)
            Creator.AddSignal(Tab.UIElements.Main.InputEnded, function()
                if Tab.Desc then
                    IsHovering = false

                    if hoverTimer then
                        task.cancel(hoverTimer)

                        hoverTimer = nil
                    end
                    if MouseConn then
                        MouseConn:Disconnect()

                        MouseConn = nil
                    end
                    if ToolTip then
                        ToolTip:Close()

                        ToolTip = nil
                    end
                end
                if not Tab.Locked then
                    Tween(Tab.UIElements.Main.Frame, 0.08, {ImageTransparency = 1}):Play()
                end
            end)

            function Tab:ScrollToTheElement(elemindex)
                Tab.UIElements.ContainerFrame.ScrollingEnabled = false

                Tween(Tab.UIElements.ContainerFrame, 0.45, {
                    CanvasPosition = Vector2.new(0, Tab.Elements[elemindex].ElementFrame.AbsolutePosition.Y - Tab.UIElements.ContainerFrame.AbsolutePosition.Y - Tab.UIElements.ContainerFrame.UIPadding.PaddingTop.Offset),
                }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                task.spawn(function()
                    task.wait(0.48)

                    if Tab.Elements[elemindex].Highlight then
                        Tab.Elements[elemindex]:Highlight()

                        Tab.UIElements.ContainerFrame.ScrollingEnabled = true
                    end
                end)

                return Tab
            end

            local ElementsModule = __DARKLUA_BUNDLE_MODULES.load('V')

            ElementsModule.Load(Tab, Tab.UIElements.ContainerFrame, ElementsModule.Elements, Window, WindUI, nil, ElementsModule, UIScale)

            function Tab:LockAll()
                for _, element in next, Window.AllElements do
                    if element.Tab and element.Tab.Index and element.Tab.Index == Tab.Index and element.Lock then
                        element:Lock()
                    end
                end
            end
            function Tab:UnlockAll()
                for _, element in next, Window.AllElements do
                    if element.Tab and element.Tab.Index and element.Tab.Index == Tab.Index and element.Unlock then
                        element:Unlock()
                    end
                end
            end
            function Tab:GetLocked()
                local LockedElements = {}

                for _, element in next, Window.AllElements do
                    if element.Tab and element.Tab.Index and element.Tab.Index == Tab.Index and element.Locked == true then
                        table.insert(LockedElements, element)
                    end
                end

                return LockedElements
            end
            function Tab:GetUnlocked()
                local UnlockedElements = {}

                for _, element in next, Window.AllElements do
                    if element.Tab and element.Tab.Index and element.Tab.Index == Tab.Index and element.Locked == false then
                        table.insert(UnlockedElements, element)
                    end
                end

                return UnlockedElements
            end
            function Tab:Select()
                return TabModule:SelectTab(Tab.Index)
            end

            task.spawn(function()
                local Empty = New('Frame', {
                    BackgroundTransparency = 1,
                    Size = UDim2.new(1, 0, 1, -Window.UIElements.Main.Main.Topbar.AbsoluteSize.Y),
                    Parent = Tab.UIElements.ContainerFrame,
                }, {
                    New('UIListLayout', {
                        Padding = UDim.new(0, 8),
                        SortOrder = 'LayoutOrder',
                        VerticalAlignment = 'Center',
                        HorizontalAlignment = 'Center',
                        FillDirection = 'Vertical',
                    }),
                    New('ImageLabel', {
                        Size = UDim2.new(0, 48, 0, 48),
                        Image = Creator.Icon('frown')[1],
                        ImageRectOffset = Creator.Icon('frown')[2].ImageRectPosition,
                        ImageRectSize = Creator.Icon('frown')[2].ImageRectSize,
                        ThemeTag = {
                            ImageColor3 = 'Icon',
                        },
                        BackgroundTransparency = 1,
                        ImageTransparency = 0.6,
                    }),
                    New('TextLabel', {
                        AutomaticSize = 'XY',
                        Text = 'This tab is empty',
                        ThemeTag = {
                            TextColor3 = 'Text',
                        },
                        TextSize = 18,
                        TextTransparency = 0.5,
                        BackgroundTransparency = 1,
                        FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                    }),
                })
                local CreationConn

                CreationConn = Creator.AddSignal(Tab.UIElements.ContainerFrame.ChildAdded, function(
                )
                    Empty.Visible = false

                    CreationConn:Disconnect()
                end)
            end)

            return Tab
        end
        function TabModule:OnChange(func)
            TabModule.OnChangeFunc = func
        end
        function TabModule:SelectTab(TabIndex)
            if not TabModule.Tabs[TabIndex].Locked then
                TabModule.SelectedTab = TabIndex

                for _, TabObject in next, TabModule.Tabs do
                    if not TabObject.Locked then
                        Tween(TabObject.UIElements.Main, 0.15, {ImageTransparency = 1}):Play()
                        Tween(TabObject.UIElements.Main.Outline, 0.15, {ImageTransparency = 1}):Play()
                        Tween(TabObject.UIElements.Main.Frame.TextLabel, 0.15, {TextTransparency = 0.3}):Play()

                        if TabObject.UIElements.Icon then
                            Tween(TabObject.UIElements.Icon.ImageLabel, 0.15, {ImageTransparency = 0.4}):Play()
                        end

                        TabObject.Selected = false
                    end
                end

                Tween(TabModule.Tabs[TabIndex].UIElements.Main, 0.15, {ImageTransparency = 0.95}):Play()
                Tween(TabModule.Tabs[TabIndex].UIElements.Main.Outline, 0.15, {ImageTransparency = 0.85}):Play()
                Tween(TabModule.Tabs[TabIndex].UIElements.Main.Frame.TextLabel, 0.15, {TextTransparency = 0}):Play()

                if TabModule.Tabs[TabIndex].UIElements.Icon then
                    Tween(TabModule.Tabs[TabIndex].UIElements.Icon.ImageLabel, 0.15, {ImageTransparency = 0.1}):Play()
                end

                TabModule.Tabs[TabIndex].Selected = true

                task.spawn(function()
                    for _, ContainerObject in next, TabModule.Containers do
                        ContainerObject.AnchorPoint = Vector2.new(0, 0.05)
                        ContainerObject.Visible = false
                    end

                    TabModule.Containers[TabIndex].Visible = true

                    Tween(TabModule.Containers[TabIndex], 0.15, {
                        AnchorPoint = Vector2.new(0, 0),
                    }, Enum.EasingStyle.Quart, Enum.EasingDirection.Out):Play()
                end)
                TabModule.OnChangeFunc(TabIndex)
            end
        end

        return TabModule
    end
    function __DARKLUA_BUNDLE_MODULES.X()
        local Section = {}
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween
        local TabModule = __DARKLUA_BUNDLE_MODULES.load('W')

        function Section.New(SectionConfig, Parent, Folder, UIScale, Window)
            local SectionModule = {
                Title = SectionConfig.Title or 'Section',
                Icon = SectionConfig.Icon,
                IconThemed = SectionConfig.IconThemed,
                Opened = SectionConfig.Opened or false,
                HeaderSize = 42,
                IconSize = 18,
                Expandable = false,
            }
            local IconFrame

            if SectionModule.Icon then
                IconFrame = Creator.Image(SectionModule.Icon, SectionModule.Icon, 0, Folder, 'Section', true, SectionModule.IconThemed)
                IconFrame.Size = UDim2.new(0, SectionModule.IconSize, 0, SectionModule.IconSize)
                IconFrame.ImageLabel.ImageTransparency = 0.25
            end

            local ChevronIconFrame = New('Frame', {
                Size = UDim2.new(0, SectionModule.IconSize, 0, SectionModule.IconSize),
                BackgroundTransparency = 1,
                Visible = false,
            }, {
                New('ImageLabel', {
                    Size = UDim2.new(1, 0, 1, 0),
                    BackgroundTransparency = 1,
                    Image = Creator.Icon('chevron-down')[1],
                    ImageRectSize = Creator.Icon('chevron-down')[2].ImageRectSize,
                    ImageRectOffset = Creator.Icon('chevron-down')[2].ImageRectPosition,
                    ThemeTag = {
                        ImageColor3 = 'Icon',
                    },
                    ImageTransparency = 0.7,
                }),
            })
            local SectionFrame = New('Frame', {
                Size = UDim2.new(1, 0, 0, SectionModule.HeaderSize),
                BackgroundTransparency = 1,
                Parent = Parent,
                ClipsDescendants = true,
            }, {
                New('TextButton', {
                    Size = UDim2.new(1, 0, 0, SectionModule.HeaderSize),
                    BackgroundTransparency = 1,
                    Text = '',
                }, {
                    IconFrame,
                    New('TextLabel', {
                        Text = SectionModule.Title,
                        TextXAlignment = 'Left',
                        Size = UDim2.new(1, IconFrame and (-SectionModule.IconSize - 10) * 2 or (
-SectionModule.IconSize - 10), 1, 0),
                        ThemeTag = {
                            TextColor3 = 'Text',
                        },
                        FontFace = Font.new(Creator.Font, Enum.FontWeight.SemiBold),
                        TextSize = 14,
                        BackgroundTransparency = 1,
                        TextTransparency = 0.7,
                        TextWrapped = true,
                    }),
                    New('UIListLayout', {
                        FillDirection = 'Horizontal',
                        VerticalAlignment = 'Center',
                        Padding = UDim.new(0, 10),
                    }),
                    ChevronIconFrame,
                    New('UIPadding', {
                        PaddingLeft = UDim.new(0, 11),
                        PaddingRight = UDim.new(0, 11),
                    }),
                }),
                New('Frame', {
                    BackgroundTransparency = 1,
                    Size = UDim2.new(1, 0, 0, 0),
                    AutomaticSize = 'Y',
                    Name = 'Content',
                    Visible = true,
                    Position = UDim2.new(0, 0, 0, SectionModule.HeaderSize),
                }, {
                    New('UIListLayout', {
                        FillDirection = 'Vertical',
                        Padding = UDim.new(0, Window.Gap),
                        VerticalAlignment = 'Bottom',
                    }),
                }),
            })

            function SectionModule:Tab(TabConfig)
                if not SectionModule.Expandable then
                    SectionModule.Expandable = true
                    ChevronIconFrame.Visible = true
                end

                TabConfig.Parent = SectionFrame.Content

                return TabModule.New(TabConfig, UIScale)
            end
            function SectionModule:Open()
                if SectionModule.Expandable then
                    SectionModule.Opened = true

                    Tween(SectionFrame, 0.33, {
                        Size = UDim2.new(1, 0, 0, SectionModule.HeaderSize + (SectionFrame.Content.AbsoluteSize.Y / UIScale)),
                    }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                    Tween(ChevronIconFrame.ImageLabel, 0.1, {Rotation = 180}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                end
            end
            function SectionModule:Close()
                if SectionModule.Expandable then
                    SectionModule.Opened = false

                    Tween(SectionFrame, 0.26, {
                        Size = UDim2.new(1, 0, 0, SectionModule.HeaderSize),
                    }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                    Tween(ChevronIconFrame.ImageLabel, 0.1, {Rotation = 0}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                end
            end

            Creator.AddSignal(SectionFrame.TextButton.MouseButton1Click, function(
            )
                if SectionModule.Expandable then
                    if SectionModule.Opened then
                        SectionModule:Close()
                    else
                        SectionModule:Open()
                    end
                end
            end)
            Creator.AddSignal(SectionFrame.Content.UIListLayout:GetPropertyChangedSignal('AbsoluteContentSize'), function(
            )
                if SectionModule.Opened then
                    SectionModule:Open()
                end
            end)

            if SectionModule.Opened then
                task.spawn(function()
                    task.wait()
                    SectionModule:Open()
                end)
            end

            return SectionModule
        end

        return Section
    end
    function __DARKLUA_BUNDLE_MODULES.Y()
        return {
            Tab = 'table-of-contents',
            Paragraph = 'type',
            Button = 'square-mouse-pointer',
            Toggle = 'toggle-right',
            Slider = 'sliders-horizontal',
            Keybind = 'command',
            Input = 'text-cursor-input',
            Dropdown = 'chevrons-up-down',
            Code = 'terminal',
            Colorpicker = 'palette',
        }
    end
    function __DARKLUA_BUNDLE_MODULES.Z()
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)

        cloneref(game:GetService('UserInputService'))

        local SearchBar = {
            Margin = 8,
            Padding = 9,
        }
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween

        function SearchBar.new(TabModule, Parent, OnClose)
            local SearchBarModule = {
                IconSize = 18,
                Padding = 14,
                Radius = 22,
                Width = 400,
                MaxHeight = 380,
                Icons = __DARKLUA_BUNDLE_MODULES.load('Y'),
            }
            local TextBox = New('TextBox', {
                Text = '',
                PlaceholderText = 'Search...',
                ThemeTag = {
                    PlaceholderColor3 = 'Placeholder',
                    TextColor3 = 'Text',
                },
                Size = UDim2.new(1, -((SearchBarModule.IconSize * 2) + (SearchBarModule.Padding * 2)), 0, 0),
                AutomaticSize = 'Y',
                ClipsDescendants = true,
                ClearTextOnFocus = false,
                BackgroundTransparency = 1,
                TextXAlignment = 'Left',
                FontFace = Font.new(Creator.Font, Enum.FontWeight.Regular),
                TextSize = 18,
            })
            local CloseButton = New('ImageLabel', {
                Image = Creator.Icon('x')[1],
                ImageRectSize = Creator.Icon('x')[2].ImageRectSize,
                ImageRectOffset = Creator.Icon('x')[2].ImageRectPosition,
                BackgroundTransparency = 1,
                ThemeTag = {
                    ImageColor3 = 'Icon',
                },
                ImageTransparency = 0.1,
                Size = UDim2.new(0, SearchBarModule.IconSize, 0, SearchBarModule.IconSize),
            }, {
                New('TextButton', {
                    Size = UDim2.new(1, 8, 1, 8),
                    BackgroundTransparency = 1,
                    Active = true,
                    ZIndex = 999999999,
                    AnchorPoint = Vector2.new(0.5, 0.5),
                    Position = UDim2.new(0.5, 0, 0.5, 0),
                    Text = '',
                }),
            })
            local ScrollingFrame = New('ScrollingFrame', {
                Size = UDim2.new(1, 0, 0, 0),
                AutomaticCanvasSize = 'Y',
                ScrollingDirection = 'Y',
                ElasticBehavior = 'Never',
                ScrollBarThickness = 0,
                CanvasSize = UDim2.new(0, 0, 0, 0),
                BackgroundTransparency = 1,
                Visible = false,
            }, {
                New('UIListLayout', {
                    Padding = UDim.new(0, 0),
                    FillDirection = 'Vertical',
                }),
                New('UIPadding', {
                    PaddingTop = UDim.new(0, SearchBarModule.Padding),
                    PaddingLeft = UDim.new(0, SearchBarModule.Padding),
                    PaddingRight = UDim.new(0, SearchBarModule.Padding),
                    PaddingBottom = UDim.new(0, SearchBarModule.Padding),
                }),
            })
            local SearchFrame = Creator.NewRoundFrame(SearchBarModule.Radius, 'Squircle', {
                Size = UDim2.new(1, 0, 1, 0),
                ThemeTag = {
                    ImageColor3 = 'Background',
                },
                ImageTransparency = 0,
            }, {
                Creator.NewRoundFrame(SearchBarModule.Radius, 'Squircle', {
                    Size = UDim2.new(1, 0, 1, 0),
                    BackgroundTransparency = 1,
                    Visible = false,
                    ImageColor3 = Color3.new(1, 1, 1),
                    ImageTransparency = 0.98,
                    Name = 'Frame',
                }, {
                    New('Frame', {
                        Size = UDim2.new(1, 0, 0, 46),
                        BackgroundTransparency = 1,
                    }, {
                        New('Frame', {
                            Size = UDim2.new(1, 0, 1, 0),
                            BackgroundTransparency = 1,
                        }, {
                            New('ImageLabel', {
                                Image = Creator.Icon('search')[1],
                                ImageRectSize = Creator.Icon('search')[2].ImageRectSize,
                                ImageRectOffset = Creator.Icon('search')[2].ImageRectPosition,
                                BackgroundTransparency = 1,
                                ThemeTag = {
                                    ImageColor3 = 'Icon',
                                },
                                ImageTransparency = 0.1,
                                Size = UDim2.new(0, SearchBarModule.IconSize, 0, SearchBarModule.IconSize),
                            }),
                            TextBox,
                            CloseButton,
                            New('UIListLayout', {
                                Padding = UDim.new(0, SearchBarModule.Padding),
                                FillDirection = 'Horizontal',
                                VerticalAlignment = 'Center',
                            }),
                            New('UIPadding', {
                                PaddingLeft = UDim.new(0, SearchBarModule.Padding),
                                PaddingRight = UDim.new(0, SearchBarModule.Padding),
                            }),
                        }),
                    }),
                    New('Frame', {
                        BackgroundTransparency = 1,
                        AutomaticSize = 'Y',
                        Size = UDim2.new(1, 0, 0, 0),
                        Name = 'Results',
                    }, {
                        New('Frame', {
                            Size = UDim2.new(1, 0, 0, 1),
                            ThemeTag = {
                                BackgroundColor3 = 'Outline',
                            },
                            BackgroundTransparency = 0.9,
                            Visible = false,
                        }),
                        ScrollingFrame,
                        New('UISizeConstraint', {
                            MaxSize = Vector2.new(SearchBarModule.Width, SearchBarModule.MaxHeight),
                        }),
                    }),
                    New('UIListLayout', {
                        Padding = UDim.new(0, 0),
                        FillDirection = 'Vertical',
                    }),
                }),
            })
            local SearchFrameContainer = New('Frame', {
                Size = UDim2.new(0, SearchBarModule.Width, 0, 0),
                AutomaticSize = 'Y',
                Parent = Parent,
                BackgroundTransparency = 1,
                Position = UDim2.new(0.5, 0, 0.5, 0),
                AnchorPoint = Vector2.new(0.5, 0.5),
                Visible = false,
                ZIndex = 99999999,
            }, {
                New('UIScale', {Scale = 0.9}),
                SearchFrame,
                Creator.NewRoundFrame(SearchBarModule.Radius, 'SquircleOutline2', {
                    Size = UDim2.new(1, 0, 1, 0),
                    ThemeTag = {
                        ImageColor3 = 'Outline',
                    },
                    ImageTransparency = 1,
                }, {
                    New('UIGradient', {
                        Rotation = 45,
                        Transparency = NumberSequence.new({
                            NumberSequenceKeypoint.new(0, 0.55),
                            NumberSequenceKeypoint.new(0.5, 0.8),
                            NumberSequenceKeypoint.new(1, 0.6),
                        }),
                    }),
                }),
            })
            local CreateSearchTab = function(
                Title,
                Desc,
                Icon,
                Parent,
                IsParent,
                Callback
            )
                local Tab = New('TextButton', {
                    Size = UDim2.new(1, 0, 0, 0),
                    AutomaticSize = 'Y',
                    BackgroundTransparency = 1,
                    Parent = Parent or nil,
                }, {
                    Creator.NewRoundFrame(SearchBarModule.Radius - 11, 'Squircle', {
                        Size = UDim2.new(1, 0, 0, 0),
                        Position = UDim2.new(0.5, 0, 0.5, 0),
                        AnchorPoint = Vector2.new(0.5, 0.5),
                        ThemeTag = {
                            ImageColor3 = 'Text',
                        },
                        ImageTransparency = 1,
                        Name = 'Main',
                    }, {
                        Creator.NewRoundFrame(SearchBarModule.Radius - 11, 'SquircleOutline2', {
                            Size = UDim2.new(1, 0, 1, 0),
                            Position = UDim2.new(0.5, 0, 0.5, 0),
                            AnchorPoint = Vector2.new(0.5, 0.5),
                            ThemeTag = {
                                ImageColor3 = 'Outline',
                            },
                            ImageTransparency = 1,
                            Name = 'Outline',
                        }, {
                            New('UIGradient', {
                                Rotation = 65,
                                Transparency = NumberSequence.new({
                                    NumberSequenceKeypoint.new(0, 0.55),
                                    NumberSequenceKeypoint.new(0.5, 0.8),
                                    NumberSequenceKeypoint.new(1, 0.6),
                                }),
                            }),
                            New('UIPadding', {
                                PaddingTop = UDim.new(0, SearchBarModule.Padding - 2),
                                PaddingLeft = UDim.new(0, SearchBarModule.Padding),
                                PaddingRight = UDim.new(0, SearchBarModule.Padding),
                                PaddingBottom = UDim.new(0, SearchBarModule.Padding - 2),
                            }),
                            New('ImageLabel', {
                                Image = Creator.Icon(Icon)[1],
                                ImageRectSize = Creator.Icon(Icon)[2].ImageRectSize,
                                ImageRectOffset = Creator.Icon(Icon)[2].ImageRectPosition,
                                BackgroundTransparency = 1,
                                ThemeTag = {
                                    ImageColor3 = 'Icon',
                                },
                                ImageTransparency = 0.1,
                                Size = UDim2.new(0, SearchBarModule.IconSize, 0, SearchBarModule.IconSize),
                            }),
                            New('Frame', {
                                Size = UDim2.new(1, -SearchBarModule.IconSize - SearchBarModule.Padding, 0, 0),
                                BackgroundTransparency = 1,
                            }, {
                                New('TextLabel', {
                                    Text = Title,
                                    ThemeTag = {
                                        TextColor3 = 'Text',
                                    },
                                    TextSize = 17,
                                    BackgroundTransparency = 1,
                                    TextXAlignment = 'Left',
                                    FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                                    Size = UDim2.new(1, 0, 0, 0),
                                    TextTruncate = 'AtEnd',
                                    AutomaticSize = 'Y',
                                    Name = 'Title',
                                }),
                                New('TextLabel', {
                                    Text = Desc or '',
                                    Visible = Desc and true or false,
                                    ThemeTag = {
                                        TextColor3 = 'Text',
                                    },
                                    TextSize = 15,
                                    TextTransparency = 0.3,
                                    BackgroundTransparency = 1,
                                    TextXAlignment = 'Left',
                                    FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                                    Size = UDim2.new(1, 0, 0, 0),
                                    TextTruncate = 'AtEnd',
                                    AutomaticSize = 'Y',
                                    Name = 'Desc',
                                }) or nil,
                                New('UIListLayout', {
                                    Padding = UDim.new(0, 6),
                                    FillDirection = 'Vertical',
                                }),
                            }),
                            New('UIListLayout', {
                                Padding = UDim.new(0, SearchBarModule.Padding),
                                FillDirection = 'Horizontal',
                            }),
                        }),
                    }, true),
                    New('Frame', {
                        Name = 'ParentContainer',
                        Size = UDim2.new(1, -SearchBarModule.Padding, 0, 0),
                        AutomaticSize = 'Y',
                        BackgroundTransparency = 1,
                        Visible = IsParent,
                    }, {
                        Creator.NewRoundFrame(99, 'Squircle', {
                            Size = UDim2.new(0, 2, 1, 0),
                            BackgroundTransparency = 1,
                            ThemeTag = {
                                ImageColor3 = 'Text',
                            },
                            ImageTransparency = 0.9,
                        }),
                        New('Frame', {
                            Size = UDim2.new(1, -SearchBarModule.Padding - 2, 0, 0),
                            Position = UDim2.new(0, SearchBarModule.Padding + 2, 0, 0),
                            BackgroundTransparency = 1,
                        }, {
                            New('UIListLayout', {
                                Padding = UDim.new(0, 0),
                                FillDirection = 'Vertical',
                            }),
                        }),
                    }),
                    New('UIListLayout', {
                        Padding = UDim.new(0, 0),
                        FillDirection = 'Vertical',
                        HorizontalAlignment = 'Right',
                    }),
                })

                Tab.Main.Size = UDim2.new(1, 0, 0, Tab.Main.Outline.Frame.Desc.Visible and (((SearchBarModule.Padding - 2) * 2) + Tab.Main.Outline.Frame.Title.TextBounds.Y + 6 + Tab.Main.Outline.Frame.Desc.TextBounds.Y) or (((SearchBarModule.Padding - 2) * 2) + Tab.Main.Outline.Frame.Title.TextBounds.Y))

                Creator.AddSignal(Tab.Main.MouseEnter, function()
                    Tween(Tab.Main, 0.04, {ImageTransparency = 0.95}):Play()
                    Tween(Tab.Main.Outline, 0.04, {ImageTransparency = 0.7}):Play()
                end)
                Creator.AddSignal(Tab.Main.InputEnded, function()
                    Tween(Tab.Main, 0.08, {ImageTransparency = 1}):Play()
                    Tween(Tab.Main.Outline, 0.08, {ImageTransparency = 1}):Play()
                end)
                Creator.AddSignal(Tab.Main.MouseButton1Click, function()
                    if Callback then
                        Callback()
                    end
                end)

                return Tab
            end
            local ContainsText = function(str, query)
                if not query or query == '' then
                    return false
                end
                if not str or str == '' then
                    return false
                end

                local lowerStr = string.lower(str)
                local lowerQuery = string.lower(query)

                return string.find(lowerStr, lowerQuery, 1, true) ~= nil
            end
            local Search = function(query)
                if not query or query == '' then
                    return {}
                end

                local results = {}

                for tabindex, tab in next, TabModule.Tabs do
                    local tabMatches = ContainsText(tab.Title or '', query)
                    local elementResults = {}

                    for elemindex, elem in next, tab.Elements do
                        if elem.__type ~= 'Section' then
                            local titleMatches = ContainsText(elem.Title or '', query)
                            local descMatches = ContainsText(elem.Desc or '', query)

                            if titleMatches or descMatches then
                                elementResults[elemindex] = {
                                    Title = elem.Title,
                                    Desc = elem.Desc,
                                    Original = elem,
                                    __type = elem.__type,
                                    Index = elemindex,
                                }
                            end
                        end
                    end

                    if tabMatches or next(elementResults) ~= nil then
                        results[tabindex] = {
                            Tab = tab,
                            Title = tab.Title,
                            Icon = tab.Icon,
                            Elements = elementResults,
                        }
                    end
                end

                return results
            end

            function SearchBarModule:Search(query)
                query = query or ''

                local result = Search(query)

                ScrollingFrame.Visible = true
                SearchFrame.Frame.Results.Frame.Visible = true

                for _, item in next, ScrollingFrame:GetChildren()do
                    if item.ClassName ~= 'UIListLayout' and item.ClassName ~= 'UIPadding' then
                        item:Destroy()
                    end
                end

                if result and next(result) ~= nil then
                    for tabindex, i in next, result do
                        local TabIcon = SearchBarModule.Icons['Tab']
                        local TabMainElement = CreateSearchTab(i.Title, nil, TabIcon, ScrollingFrame, true, function(
                        )
                            SearchBarModule:Close()
                            TabModule:SelectTab(tabindex)
                        end)

                        if i.Elements and next(i.Elements) ~= nil then
                            for elemindex, e in next, i.Elements do
                                local ElementIcon = SearchBarModule.Icons[e.__type]

                                CreateSearchTab(e.Title, e.Desc, ElementIcon, TabMainElement:FindFirstChild('ParentContainer') and TabMainElement.ParentContainer.Frame or nil, false, function(
                                )
                                    SearchBarModule:Close()
                                    TabModule:SelectTab(tabindex)

                                    if i.Tab.ScrollToTheElement then
                                        i.Tab:ScrollToTheElement(e.Index)
                                    end
                                end)
                            end
                        end
                    end
                elseif query ~= '' then
                    New('TextLabel', {
                        Size = UDim2.new(1, 0, 0, 70),
                        BackgroundTransparency = 1,
                        Text = 'No results found',
                        TextSize = 16,
                        ThemeTag = {
                            TextColor3 = 'Text',
                        },
                        TextTransparency = 0.2,
                        BackgroundTransparency = 1,
                        FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                        Parent = ScrollingFrame,
                        Name = 'NotFound',
                    })
                else
                    ScrollingFrame.Visible = false
                    SearchFrame.Frame.Results.Frame.Visible = false
                end
            end

            Creator.AddSignal(TextBox:GetPropertyChangedSignal('Text'), function(
            )
                SearchBarModule:Search(TextBox.Text)
            end)
            Creator.AddSignal(ScrollingFrame.UIListLayout:GetPropertyChangedSignal('AbsoluteContentSize'), function(
            )
                Tween(ScrollingFrame, 0.06, {
                    Size = UDim2.new(1, 0, 0, math.clamp(ScrollingFrame.UIListLayout.AbsoluteContentSize.Y + (SearchBarModule.Padding * 2), 0, SearchBarModule.MaxHeight)),
                }, Enum.EasingStyle.Quint, Enum.EasingDirection.InOut):Play()
            end)

            function SearchBarModule:Open()
                task.spawn(function()
                    SearchFrame.Frame.Visible = true
                    SearchFrameContainer.Visible = true

                    Tween(SearchFrameContainer.UIScale, 0.12, {Scale = 1}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                end)
            end
            function SearchBarModule:Close()
                task.spawn(function()
                    OnClose()

                    SearchFrame.Frame.Visible = false

                    Tween(SearchFrameContainer.UIScale, 0.12, {Scale = 1}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                    task.wait(0.12)

                    SearchFrameContainer.Visible = false
                end)
            end

            Creator.AddSignal(CloseButton.TextButton.MouseButton1Click, function(
            )
                SearchBarModule:Close()
            end)
            SearchBarModule:Open()

            return SearchBarModule
        end

        return SearchBar
    end
    function __DARKLUA_BUNDLE_MODULES._()
        local cloneref = (cloneref or clonereference or function(instance)
            return instance
        end)
        local UserInputService = cloneref(game:GetService('UserInputService'))

        cloneref(game:GetService('RunService'))

        local CurrentCamera = workspace.CurrentCamera
        local Acrylic = __DARKLUA_BUNDLE_MODULES.load('s')
        local Creator = __DARKLUA_BUNDLE_MODULES.load('d')
        local New = Creator.New
        local Tween = Creator.Tween
        local CreateLabel = __DARKLUA_BUNDLE_MODULES.load('v').New
        local CreateButton = __DARKLUA_BUNDLE_MODULES.load('l').New
        local CreateScrollSlider = __DARKLUA_BUNDLE_MODULES.load('w').New
        local Tag = __DARKLUA_BUNDLE_MODULES.load('x')
        local ConfigManager = __DARKLUA_BUNDLE_MODULES.load('y')

        return function(Config)
            local Window = {
                Title = Config.Title or 'UI Library',
                Author = Config.Author,
                Icon = Config.Icon,
                IconSize = Config.IconSize or 22,
                IconThemed = Config.IconThemed,
                Folder = Config.Folder,
                Resizable = Config.Resizable ~= false,
                Background = Config.Background,
                BackgroundImageTransparency = Config.BackgroundImageTransparency or 0,
                ShadowTransparency = Config.ShadowTransparency or 0.7,
                User = Config.User or {},
                Size = Config.Size,
                MinSize = Config.MinSize or Vector2.new(560, 350),
                MaxSize = Config.MaxSize or Vector2.new(850, 560),
                TopBarButtonIconSize = Config.TopBarButtonIconSize or 16,
                ToggleKey = Config.ToggleKey,
                ElementsRadius = Config.ElementsRadius,
                Radius = Config.Radius or 16,
                Transparent = Config.Transparent or false,
                HideSearchBar = Config.HideSearchBar ~= false,
                ScrollBarEnabled = Config.ScrollBarEnabled or false,
                SideBarWidth = Config.SideBarWidth or 200,
                Acrylic = Config.Acrylic or false,
                NewElements = Config.NewElements or false,
                IgnoreAlerts = Config.IgnoreAlerts or false,
                HidePanelBackground = Config.HidePanelBackground or false,
                AutoScale = Config.AutoScale ~= false,
                OpenButton = Config.OpenButton,
                Position = UDim2.new(0.5, 0, 0.5, 0),
                UICorner = nil,
                UIPadding = 14,
                UIElements = {},
                CanDropdown = true,
                Closed = false,
                Parent = Config.Parent,
                Destroyed = false,
                IsFullscreen = false,
                CanResize = Config.Resizable ~= false,
                IsOpenButtonEnabled = true,
                CurrentConfig = nil,
                ConfigManager = nil,
                AcrylicPaint = nil,
                CurrentTab = nil,
                TabModule = nil,
                OnOpenCallback = nil,
                OnCloseCallback = nil,
                OnDestroyCallback = nil,
                IsPC = false,
                Gap = 5,
                TopBarButtons = {},
                AllElements = {},
                ElementConfig = {},
                PendingFlags = {},
                IsToggleDragging = false,
            }

            Window.UICorner = Window.Radius
            Window.ElementConfig = {
                UIPadding = (Window.NewElements and 10 or 13),
                UICorner = Window.ElementsRadius or (Window.NewElements and 23 or 12),
            }

            local WindowSize = Window.Size or UDim2.new(0, 580, 0, 460)

            Window.Size = UDim2.new(WindowSize.X.Scale, math.clamp(WindowSize.X.Offset, Window.MinSize.X, Window.MaxSize.X), WindowSize.Y.Scale, math.clamp(WindowSize.Y.Offset, Window.MinSize.Y, Window.MaxSize.Y))

            if Window.Folder then
                if not isfolder('WindUI/' .. Window.Folder) then
                    makefolder('WindUI/' .. Window.Folder)
                end
                if not isfolder('WindUI/' .. Window.Folder .. '/assets') then
                    makefolder('WindUI/' .. Window.Folder .. '/assets')
                end
                if not isfolder(Window.Folder) then
                    makefolder(Window.Folder)
                end
                if not isfolder(Window.Folder .. '/assets') then
                    makefolder(Window.Folder .. '/assets')
                end
            end

            local UICorner = New('UICorner', {
                CornerRadius = UDim.new(0, Window.UICorner),
            })

            if Window.Folder then
                Window.ConfigManager = ConfigManager:Init(Window)
            end
            if Window.Acrylic then
                local AcrylicPaint = Acrylic.AcrylicPaint({
                    UseAcrylic = Window.Acrylic,
                })

                Window.AcrylicPaint = AcrylicPaint
            end

            local ResizeHandle = New('Frame', {
                Size = UDim2.new(0, 32, 0, 32),
                Position = UDim2.new(1, 0, 1, 0),
                AnchorPoint = Vector2.new(0.5, 0.5),
                BackgroundTransparency = 1,
                ZIndex = 99,
                Active = true,
            }, {
                New('ImageLabel', {
                    Size = UDim2.new(0, 96, 0, 96),
                    BackgroundTransparency = 1,
                    Image = 'rbxassetid://120997033468887',
                    Position = UDim2.new(0.5, -16, 0.5, -16),
                    AnchorPoint = Vector2.new(0.5, 0.5),
                    ImageTransparency = 1,
                }),
            })
            local FullScreenIcon = Creator.NewRoundFrame(Window.UICorner, 'Squircle', {
                Size = UDim2.new(1, 0, 1, 0),
                ImageTransparency = 1,
                ImageColor3 = Color3.new(0, 0, 0),
                ZIndex = 98,
                Active = false,
            }, {
                New('ImageLabel', {
                    Size = UDim2.new(0, 70, 0, 70),
                    Image = Creator.Icon('expand')[1],
                    ImageRectOffset = Creator.Icon('expand')[2].ImageRectPosition,
                    ImageRectSize = Creator.Icon('expand')[2].ImageRectSize,
                    BackgroundTransparency = 1,
                    Position = UDim2.new(0.5, 0, 0.5, 0),
                    AnchorPoint = Vector2.new(0.5, 0.5),
                    ImageTransparency = 1,
                }),
            })
            local FullScreenBlur = Creator.NewRoundFrame(Window.UICorner, 'Squircle', {
                Size = UDim2.new(1, 0, 1, 0),
                ImageTransparency = 1,
                ImageColor3 = Color3.new(0, 0, 0),
                ZIndex = 999,
                Active = false,
            })

            Window.UIElements.SideBar = New('ScrollingFrame', {
                Size = UDim2.new(1, Window.ScrollBarEnabled and -3 - (Window.UIPadding / 2) or 0, 1, not Window.HideSearchBar and 
-45 or 0),
                Position = UDim2.new(0, 0, 1, 0),
                AnchorPoint = Vector2.new(0, 1),
                BackgroundTransparency = 1,
                ScrollBarThickness = 0,
                ElasticBehavior = 'Never',
                CanvasSize = UDim2.new(0, 0, 0, 0),
                AutomaticCanvasSize = 'Y',
                ScrollingDirection = 'Y',
                ClipsDescendants = true,
                VerticalScrollBarPosition = 'Left',
            }, {
                New('Frame', {
                    BackgroundTransparency = 1,
                    AutomaticSize = 'Y',
                    Size = UDim2.new(1, 0, 0, 0),
                    Name = 'Frame',
                }, {
                    New('UIPadding', {
                        PaddingTop = UDim.new(0, Window.UIPadding / 2),
                        PaddingBottom = UDim.new(0, Window.UIPadding / 2),
                    }),
                    New('UIListLayout', {
                        SortOrder = 'LayoutOrder',
                        Padding = UDim.new(0, Window.Gap),
                    }),
                }),
                New('UIPadding', {
                    PaddingLeft = UDim.new(0, Window.UIPadding / 2),
                    PaddingRight = UDim.new(0, Window.UIPadding / 2),
                }),
            })
            Window.UIElements.SideBarContainer = New('Frame', {
                Size = UDim2.new(0, Window.SideBarWidth, 1, Window.User.Enabled and 
-94 - (Window.UIPadding * 2) or -52),
                Position = UDim2.new(0, 0, 0, 52),
                BackgroundTransparency = 1,
                Visible = true,
            }, {
                New('Frame', {
                    Name = 'Content',
                    BackgroundTransparency = 1,
                    Size = UDim2.new(1, 0, 1, not Window.HideSearchBar and -45 - Window.UIPadding / 2 or 0),
                    Position = UDim2.new(0, 0, 1, 0),
                    AnchorPoint = Vector2.new(0, 1),
                }),
                Window.UIElements.SideBar,
            })

            if Window.ScrollBarEnabled then
                CreateScrollSlider(Window.UIElements.SideBar, Window.UIElements.SideBarContainer.Content, Window, 3)
            end

            Window.UIElements.MainBar = New('Frame', {
                Size = UDim2.new(1, -Window.UIElements.SideBarContainer.AbsoluteSize.X, 1, 
-52),
                Position = UDim2.new(1, 0, 1, 0),
                AnchorPoint = Vector2.new(1, 1),
                BackgroundTransparency = 1,
            }, {
                Creator.NewRoundFrame(Window.UICorner - (Window.UIPadding / 2), 'Squircle', {
                    Size = UDim2.new(1, 0, 1, 0),
                    ImageColor3 = Color3.new(1, 1, 1),
                    ZIndex = 3,
                    ImageTransparency = 0.95,
                    Name = 'Background',
                    Visible = not Window.HidePanelBackground,
                }),
                New('UIPadding', {
                    PaddingTop = UDim.new(0, Window.UIPadding / 2),
                    PaddingLeft = UDim.new(0, Window.UIPadding / 2),
                    PaddingRight = UDim.new(0, Window.UIPadding / 2),
                    PaddingBottom = UDim.new(0, Window.UIPadding / 2),
                }),
            })

            local Blur = New('ImageLabel', {
                Image = 'rbxassetid://8992230677',
                ThemeTag = {
                    ImageColor3 = 'WindowShadow',
                },
                ImageTransparency = 1,
                Size = UDim2.new(1, 120, 1, 116),
                Position = UDim2.new(0, -60, 0, -58),
                ScaleType = 'Slice',
                SliceCenter = Rect.new(99, 99, 99, 99),
                BackgroundTransparency = 1,
                ZIndex = -999999999999999,
                Name = 'Blur',
            })

            if UserInputService.TouchEnabled and not UserInputService.KeyboardEnabled then
                Window.IsPC = false
            elseif UserInputService.KeyboardEnabled then
                Window.IsPC = true
            else
                Window.IsPC = nil
            end

            local UserIcon

            if Window.User then
                local GetUserThumb = function()
                    local ImageId = cloneref(game:GetService('Players')):GetUserThumbnailAsync(Window.User.Anonymous and 1 or game.Players.LocalPlayer.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size420x420)

                    return ImageId
                end

                UserIcon = New('TextButton', {
                    Size = UDim2.new(0, (Window.UIElements.SideBarContainer.AbsoluteSize.X) - (Window.UIPadding / 2), 0, 42 + (Window.UIPadding)),
                    Position = UDim2.new(0, Window.UIPadding / 2, 1, -(Window.UIPadding / 2)),
                    AnchorPoint = Vector2.new(0, 1),
                    BackgroundTransparency = 1,
                    Visible = Window.User.Enabled or false,
                }, {
                    Creator.NewRoundFrame(Window.UICorner - (Window.UIPadding / 2), 'SquircleOutline', {
                        Size = UDim2.new(1, 0, 1, 0),
                        ThemeTag = {
                            ImageColor3 = 'Text',
                        },
                        ImageTransparency = 1,
                        Name = 'Outline',
                    }, {
                        New('UIGradient', {
                            Rotation = 78,
                            Color = ColorSequence.new({
                                ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
                                ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 255, 255)),
                                ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255)),
                            }),
                            Transparency = NumberSequence.new({
                                NumberSequenceKeypoint.new(0, 0.1),
                                NumberSequenceKeypoint.new(0.5, 1),
                                NumberSequenceKeypoint.new(1, 0.1),
                            }),
                        }),
                    }),
                    Creator.NewRoundFrame(Window.UICorner - (Window.UIPadding / 2), 'Squircle', {
                        Size = UDim2.new(1, 0, 1, 0),
                        ThemeTag = {
                            ImageColor3 = 'Text',
                        },
                        ImageTransparency = 1,
                        Name = 'UserIcon',
                    }, {
                        New('ImageLabel', {
                            Image = GetUserThumb(),
                            BackgroundTransparency = 1,
                            Size = UDim2.new(0, 42, 0, 42),
                            ThemeTag = {
                                BackgroundColor3 = 'Text',
                            },
                            BackgroundTransparency = 0.93,
                        }, {
                            New('UICorner', {
                                CornerRadius = UDim.new(1, 0),
                            }),
                        }),
                        New('Frame', {
                            AutomaticSize = 'XY',
                            BackgroundTransparency = 1,
                        }, {
                            New('TextLabel', {
                                Text = Window.User.Anonymous and 'Anonymous' or game.Players.LocalPlayer.DisplayName,
                                TextSize = 17,
                                ThemeTag = {
                                    TextColor3 = 'Text',
                                },
                                FontFace = Font.new(Creator.Font, Enum.FontWeight.SemiBold),
                                AutomaticSize = 'Y',
                                BackgroundTransparency = 1,
                                Size = UDim2.new(1, -27, 0, 0),
                                TextTruncate = 'AtEnd',
                                TextXAlignment = 'Left',
                                Name = 'DisplayName',
                            }),
                            New('TextLabel', {
                                Text = Window.User.Anonymous and 'anonymous' or game.Players.LocalPlayer.Name,
                                TextSize = 15,
                                TextTransparency = 0.6,
                                ThemeTag = {
                                    TextColor3 = 'Text',
                                },
                                FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                                AutomaticSize = 'Y',
                                BackgroundTransparency = 1,
                                Size = UDim2.new(1, -27, 0, 0),
                                TextTruncate = 'AtEnd',
                                TextXAlignment = 'Left',
                                Name = 'UserName',
                            }),
                            New('UIListLayout', {
                                Padding = UDim.new(0, 4),
                                HorizontalAlignment = 'Left',
                            }),
                        }),
                        New('UIListLayout', {
                            Padding = UDim.new(0, Window.UIPadding),
                            FillDirection = 'Horizontal',
                            VerticalAlignment = 'Center',
                        }),
                        New('UIPadding', {
                            PaddingLeft = UDim.new(0, Window.UIPadding / 2),
                            PaddingRight = UDim.new(0, Window.UIPadding / 2),
                        }),
                    }),
                })

                function Window.User:Enable()
                    Window.User.Enabled = true

                    Tween(Window.UIElements.SideBarContainer, 0.25, {
                        Size = UDim2.new(0, Window.SideBarWidth, 1, -94 - (Window.UIPadding * 2)),
                    }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()

                    UserIcon.Visible = true
                end
                function Window.User:Disable()
                    Window.User.Enabled = false

                    Tween(Window.UIElements.SideBarContainer, 0.25, {
                        Size = UDim2.new(0, Window.SideBarWidth, 1, -52),
                    }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()

                    UserIcon.Visible = false
                end
                function Window.User:SetAnonymous(v)
                    if v ~= false then
                        v = true
                    end

                    Window.User.Anonymous = v
                    UserIcon.UserIcon.ImageLabel.Image = GetUserThumb()
                    UserIcon.UserIcon.Frame.DisplayName.Text = v and 'Anonymous' or game.Players.LocalPlayer.DisplayName
                    UserIcon.UserIcon.Frame.UserName.Text = v and 'anonymous' or game.Players.LocalPlayer.Name
                end

                if Window.User.Enabled then
                    Window.User:Enable()
                else
                    Window.User:Disable()
                end
                if Window.User.Callback then
                    Creator.AddSignal(UserIcon.MouseButton1Click, function()
                        Window.User.Callback()
                    end)
                    Creator.AddSignal(UserIcon.MouseEnter, function()
                        Tween(UserIcon.UserIcon, 0.04, {ImageTransparency = 0.95}):Play()
                        Tween(UserIcon.Outline, 0.04, {ImageTransparency = 0.85}):Play()
                    end)
                    Creator.AddSignal(UserIcon.InputEnded, function()
                        Tween(UserIcon.UserIcon, 0.04, {ImageTransparency = 1}):Play()
                        Tween(UserIcon.Outline, 0.04, {ImageTransparency = 1}):Play()
                    end)
                end
            end

            local Outline1
            local Outline2
            local IsVideoBG = false
            local BGImage = nil
            local BGVideo = typeof(Window.Background) == 'string' and string.match(Window.Background, '^video:(.+)') or nil
            local BGImageUrl = typeof(Window.Background) == 'string' and not BGVideo and string.match(Window.Background, '^https?://.+') or nil
            local GetImageExtension = function(url)
                local ext = url:match('%.(%w+)$') or url:match('%.(%w+)%?')

                if ext then
                    ext = ext:lower()

                    if ext == 'jpg' or ext == 'jpeg' or ext == 'png' or ext == 'webp' then
                        return '.' .. ext
                    end
                end

                return '.png'
            end

            if typeof(Window.Background) == 'string' and BGVideo then
                IsVideoBG = true

                if string.find(BGVideo, 'http') then
                    local videoPath = Window.Folder .. '/assets/.' .. Creator.SanitizeFilename(BGVideo) .. '.webm'

                    if not isfile(videoPath) then
                        local success, result = pcall(function()
                            local response = Creator.Request({
                                Url = BGVideo,
                                Method = 'GET',
                                Headers = {
                                    ['User-Agent'] = 'Roblox/Exploit',
                                },
                            })

                            writefile(videoPath, response.Body)
                        end)

                        if not success then
                            warn('[ WindUI.Window.Background ] Failed to download video: ' .. tostring(result))

                            return
                        end
                    end

                    local success, customAsset = pcall(function()
                        return getcustomasset(videoPath)
                    end)

                    if not success then
                        warn('[ WindUI.Window.Background ] Failed to load custom asset: ' .. tostring(customAsset))

                        return
                    end

                    warn(
[[[ WindUI.Window.Background ] VideoFrame may not work with custom video]])

                    BGVideo = customAsset
                end

                BGImage = New('VideoFrame', {
                    BackgroundTransparency = 1,
                    Size = UDim2.new(1, 0, 1, 0),
                    Video = BGVideo,
                    Looped = true,
                    Volume = 0,
                }, {
                    New('UICorner', {
                        CornerRadius = UDim.new(0, Window.UICorner),
                    }),
                })

                BGImage:Play()
            elseif BGImageUrl then
                local imagePath = Window.Folder .. '/assets/.' .. Creator.SanitizeFilename(BGImageUrl) .. GetImageExtension(BGImageUrl)

                if not isfile(imagePath) then
                    local success, result = pcall(function()
                        local response = Creator.Request({
                            Url = BGImageUrl,
                            Method = 'GET',
                            Headers = {
                                ['User-Agent'] = 'Roblox/Exploit',
                            },
                        })

                        writefile(imagePath, response.Body)
                    end)

                    if not success then
                        warn('[ Window.Background ] Failed to download image: ' .. tostring(result))

                        return
                    end
                end

                local success, customAsset = pcall(function()
                    return getcustomasset(imagePath)
                end)

                if not success then
                    warn('[ Window.Background ] Failed to load custom asset: ' .. tostring(customAsset))

                    return
                end

                BGImage = New('ImageLabel', {
                    BackgroundTransparency = 1,
                    Size = UDim2.new(1, 0, 1, 0),
                    Image = customAsset,
                    ImageTransparency = 0,
                    ScaleType = 'Crop',
                }, {
                    New('UICorner', {
                        CornerRadius = UDim.new(0, Window.UICorner),
                    }),
                })
            elseif Window.Background then
                BGImage = New('ImageLabel', {
                    BackgroundTransparency = 1,
                    Size = UDim2.new(1, 0, 1, 0),
                    Image = typeof(Window.Background) == 'string' and Window.Background or '',
                    ImageTransparency = 1,
                    ScaleType = 'Crop',
                }, {
                    New('UICorner', {
                        CornerRadius = UDim.new(0, Window.UICorner),
                    }),
                })
            end

            local BottomDragFrame = Creator.NewRoundFrame(99, 'Squircle', {
                ImageTransparency = 0.8,
                ImageColor3 = Color3.new(1, 1, 1),
                Size = UDim2.new(0, 0, 0, 4),
                Position = UDim2.new(0.5, 0, 1, 4),
                AnchorPoint = Vector2.new(0.5, 0),
            }, {
                New('TextButton', {
                    Size = UDim2.new(1, 12, 1, 12),
                    BackgroundTransparency = 1,
                    Position = UDim2.new(0.5, 0, 0.5, 0),
                    AnchorPoint = Vector2.new(0.5, 0.5),
                    Active = true,
                    ZIndex = 99,
                    Name = 'Frame',
                }),
            })

            function createAuthor(text)
                return New('TextLabel', {
                    Text = text,
                    FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                    BackgroundTransparency = 1,
                    TextTransparency = 0.35,
                    AutomaticSize = 'XY',
                    Parent = Window.UIElements.Main and Window.UIElements.Main.Main.Topbar.Left.Title,
                    TextXAlignment = 'Left',
                    TextSize = 13,
                    LayoutOrder = 2,
                    ThemeTag = {
                        TextColor3 = 'WindowTopbarAuthor',
                    },
                    Name = 'Author',
                })
            end

            local WindowAuthor
            local WindowIcon

            if Window.Author then
                WindowAuthor = createAuthor(Window.Author)
            end

            local WindowTitle = New('TextLabel', {
                Text = Window.Title,
                FontFace = Font.new(Creator.Font, Enum.FontWeight.SemiBold),
                BackgroundTransparency = 1,
                AutomaticSize = 'XY',
                Name = 'Title',
                TextXAlignment = 'Left',
                TextSize = 16,
                ThemeTag = {
                    TextColor3 = 'WindowTopbarTitle',
                },
            })

            Window.UIElements.Main = New('Frame', {
                Size = Window.Size,
                Position = Window.Position,
                BackgroundTransparency = 1,
                Parent = Config.Parent,
                AnchorPoint = Vector2.new(0.5, 0.5),
                Active = true,
            }, {
                Config.WindUI.UIScaleObj,
                Window.AcrylicPaint and Window.AcrylicPaint.Frame or nil,
                Blur,
                Creator.NewRoundFrame(Window.UICorner, 'Squircle', {
                    ImageTransparency = 1,
                    Size = UDim2.new(1, 0, 1, -240),
                    AnchorPoint = Vector2.new(0.5, 0.5),
                    Position = UDim2.new(0.5, 0, 0.5, 0),
                    Name = 'Background',
                    ThemeTag = {
                        ImageColor3 = 'WindowBackground',
                    },
                }, {BGImage, BottomDragFrame, ResizeHandle}),
                UIStroke,
                UICorner,
                FullScreenIcon,
                FullScreenBlur,
                New('Frame', {
                    Size = UDim2.new(1, 0, 1, 0),
                    BackgroundTransparency = 1,
                    Name = 'Main',
                    Visible = false,
                    ZIndex = 97,
                }, {
                    New('UICorner', {
                        CornerRadius = UDim.new(0, Window.UICorner),
                    }),
                    Window.UIElements.SideBarContainer,
                    Window.UIElements.MainBar,
                    UserIcon,
                    Outline2,
                    New('Frame', {
                        Size = UDim2.new(1, 0, 0, 52),
                        BackgroundTransparency = 1,
                        BackgroundColor3 = Color3.fromRGB(50, 50, 50),
                        Name = 'Topbar',
                    }, {
                        Outline1,
                        New('Frame', {
                            AutomaticSize = 'X',
                            Size = UDim2.new(0, 0, 1, 0),
                            BackgroundTransparency = 1,
                            Name = 'Left',
                        }, {
                            New('UIListLayout', {
                                Padding = UDim.new(0, Window.UIPadding + 4),
                                SortOrder = 'LayoutOrder',
                                FillDirection = 'Horizontal',
                                VerticalAlignment = 'Center',
                            }),
                            New('Frame', {
                                AutomaticSize = 'XY',
                                BackgroundTransparency = 1,
                                Name = 'Title',
                                Size = UDim2.new(0, 0, 1, 0),
                                LayoutOrder = 2,
                            }, {
                                New('UIListLayout', {
                                    Padding = UDim.new(0, 0),
                                    SortOrder = 'LayoutOrder',
                                    FillDirection = 'Vertical',
                                    VerticalAlignment = 'Center',
                                }),
                                WindowTitle,
                                WindowAuthor,
                            }),
                            New('UIPadding', {
                                PaddingLeft = UDim.new(0, 4),
                            }),
                        }),
                        New('ScrollingFrame', {
                            Name = 'Center',
                            BackgroundTransparency = 1,
                            AutomaticSize = 'Y',
                            ScrollBarThickness = 0,
                            ScrollingDirection = 'X',
                            AutomaticCanvasSize = 'X',
                            CanvasSize = UDim2.new(0, 0, 0, 0),
                            Size = UDim2.new(0, 0, 1, 0),
                            AnchorPoint = Vector2.new(0, 0.5),
                            Position = UDim2.new(0, 0, 0.5, 0),
                            Visible = false,
                        }, {
                            New('UIListLayout', {
                                FillDirection = 'Horizontal',
                                VerticalAlignment = 'Center',
                                HorizontalAlignment = 'Left',
                                Padding = UDim.new(0, Window.UIPadding / 2),
                            }),
                        }),
                        New('Frame', {
                            AutomaticSize = 'XY',
                            BackgroundTransparency = 1,
                            Position = UDim2.new(1, 0, 0.5, 0),
                            AnchorPoint = Vector2.new(1, 0.5),
                            Name = 'Right',
                        }, {
                            New('UIListLayout', {
                                Padding = UDim.new(0, 9),
                                FillDirection = 'Horizontal',
                                SortOrder = 'LayoutOrder',
                            }),
                        }),
                        New('UIPadding', {
                            PaddingTop = UDim.new(0, Window.UIPadding),
                            PaddingLeft = UDim.new(0, Window.UIPadding),
                            PaddingRight = UDim.new(0, 8),
                            PaddingBottom = UDim.new(0, Window.UIPadding),
                        }),
                    }),
                }),
            })

            Creator.AddSignal(Window.UIElements.Main.Main.Topbar.Left:GetPropertyChangedSignal('AbsoluteSize'), function(
            )
                local LeftWidth = 0
                local RightWidth = Window.UIElements.Main.Main.Topbar.Right.UIListLayout.AbsoluteContentSize.X / Config.WindUI.UIScale

                if WindowTitle and WindowAuthor then
                    LeftWidth = math.max(WindowTitle.TextBounds.X / Config.WindUI.UIScale, WindowAuthor.TextBounds.X / Config.WindUI.UIScale)
                else
                    LeftWidth = WindowTitle.TextBounds.X / Config.WindUI.UIScale
                end
                if WindowIcon then
                    LeftWidth = LeftWidth + (Window.IconSize / Config.WindUI.UIScale) + (Window.UIPadding / Config.WindUI.UIScale) + (4 / Config.WindUI.UIScale)
                end

                Window.UIElements.Main.Main.Topbar.Center.Position = UDim2.new(0, LeftWidth + (Window.UIPadding / Config.WindUI.UIScale), 0.5, 0)
                Window.UIElements.Main.Main.Topbar.Center.Size = UDim2.new(1, -LeftWidth - RightWidth - ((Window.UIPadding * 2) / Config.WindUI.UIScale), 1, 0)
            end)

            function Window:CreateTopbarButton(
                Name,
                Icon,
                Callback,
                LayoutOrder,
                IconThemed
            )
                local IconFrame = Creator.Image(Icon, Icon, 0, Window.Folder, 'WindowTopbarIcon', true, IconThemed, 'WindowTopbarButtonIcon')

                IconFrame.Size = UDim2.new(0, Window.TopBarButtonIconSize, 0, Window.TopBarButtonIconSize)
                IconFrame.AnchorPoint = Vector2.new(0.5, 0.5)
                IconFrame.Position = UDim2.new(0.5, 0, 0.5, 0)

                local Button = Creator.NewRoundFrame(Window.UICorner - (Window.UIPadding / 2), 'Squircle', {
                    Size = UDim2.new(0, 36, 0, 36),
                    LayoutOrder = LayoutOrder or 999,
                    Parent = Window.UIElements.Main.Main.Topbar.Right,
                    ZIndex = 9999,
                    ThemeTag = {
                        ImageColor3 = 'Text',
                    },
                    ImageTransparency = 1,
                }, {
                    Creator.NewRoundFrame(Window.UICorner - (Window.UIPadding / 2), 'SquircleOutline', {
                        Size = UDim2.new(1, 0, 1, 0),
                        ThemeTag = {
                            ImageColor3 = 'Text',
                        },
                        ImageTransparency = 1,
                        Name = 'Outline',
                    }, {
                        New('UIGradient', {
                            Rotation = 45,
                            Color = ColorSequence.new({
                                ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
                                ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 255, 255)),
                                ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255)),
                            }),
                            Transparency = NumberSequence.new({
                                NumberSequenceKeypoint.new(0, 0.1),
                                NumberSequenceKeypoint.new(0.5, 1),
                                NumberSequenceKeypoint.new(1, 0.1),
                            }),
                        }),
                    }),
                    IconFrame,
                }, true)

                Window.TopBarButtons[100 - LayoutOrder] = {
                    Name = Name,
                    Object = Button,
                }

                Creator.AddSignal(Button.MouseButton1Click, function()
                    Callback()
                end)
                Creator.AddSignal(Button.MouseEnter, function()
                    Tween(Button, 0.15, {ImageTransparency = 0.93}):Play()
                    Tween(Button.Outline, 0.15, {ImageTransparency = 0.75}):Play()
                end)
                Creator.AddSignal(Button.MouseLeave, function()
                    Tween(Button, 0.1, {ImageTransparency = 1}):Play()
                    Tween(Button.Outline, 0.1, {ImageTransparency = 1}):Play()
                end)

                return Button
            end

            local WindowDragModule = Creator.Drag(Window.UIElements.Main, {
                Window.UIElements.Main.Main.Topbar,
                BottomDragFrame.Frame,
            }, function(dragging, frame)
                if not Window.Closed then
                    if dragging and frame == BottomDragFrame.Frame then
                        Tween(BottomDragFrame, 0.1, {ImageTransparency = 0.35}):Play()
                    else
                        Tween(BottomDragFrame, 0.2, {ImageTransparency = 0.8}):Play()
                    end

                    Window.Position = Window.UIElements.Main.Position
                    Window.Dragging = dragging
                end
            end)

            if not IsVideoBG and Window.Background and typeof(Window.Background) == 'table' then
                local BackgroundGradient = New('UIGradient')

                for key, value in next, Window.Background do
                    BackgroundGradient[key] = value
                end

                Window.UIElements.BackgroundGradient = Creator.NewRoundFrame(Window.UICorner, 'Squircle', {
                    Size = UDim2.new(1, 0, 1, 0),
                    Parent = Window.UIElements.Main.Background,
                    ImageTransparency = Window.Transparent and Config.WindUI.TransparencyValue or 0,
                }, {BackgroundGradient})
            end

            Window.OpenButtonMain = __DARKLUA_BUNDLE_MODULES.load('z').New(Window)

            task.spawn(function()
                if Window.Icon then
                    local WindowIconContainer = New('Frame', {
                        Size = UDim2.new(0, 22, 0, 22),
                        BackgroundTransparency = 1,
                        Parent = Window.UIElements.Main.Main.Topbar.Left,
                    })

                    WindowIcon = Creator.Image(Window.Icon, Window.Title, 0, Window.Folder, 'Window', true, Window.IconThemed, 'WindowTopbarIcon')
                    WindowIcon.Parent = WindowIconContainer
                    WindowIcon.Size = UDim2.new(0, Window.IconSize, 0, Window.IconSize)
                    WindowIcon.Position = UDim2.new(0.5, 0, 0.5, 0)
                    WindowIcon.AnchorPoint = Vector2.new(0.5, 0.5)

                    Window.OpenButtonMain:SetIcon(Window.Icon)
                else
                    Window.OpenButtonMain:SetIcon(Window.Icon)
                end
            end)

            function Window:SetToggleKey(keycode)
                Window.ToggleKey = keycode
            end
            function Window:SetTitle(text)
                Window.Title = text
                WindowTitle.Text = text
            end
            function Window:SetAuthor(text)
                Window.Author = text

                if not WindowAuthor then
                    WindowAuthor = createAuthor(Window.Author)
                end

                WindowAuthor.Text = text
            end
            function Window:SetBackgroundImage(id)
                Window.UIElements.Main.Background.ImageLabel.Image = id
            end
            function Window:SetBackgroundImageTransparency(v)
                if BGImage and BGImage:IsA('ImageLabel') then
                    BGImage.ImageTransparency = math.floor(v * 10 + 0.5) / 10
                end

                Window.BackgroundImageTransparency = math.floor(v * 10 + 0.5) / 10
            end
            function Window:SetBackgroundTransparency(v)
                local rounded = math.floor(tonumber(v) * 10 + 0.5) / 10

                Config.WindUI.TransparencyValue = rounded

                Window:ToggleTransparency(rounded > 0)
            end

            local CurrentPos
            local CurrentSize

            Creator.Icon('minimize')
            Creator.Icon('maximize')
            Window:CreateTopbarButton('Fullscreen', 'maximize', function()
                Window:ToggleFullscreen()
            end, 998)

            function Window:ToggleFullscreen()
                local isFullscreen = Window.IsFullscreen

                WindowDragModule:Set(isFullscreen)

                if not isFullscreen then
                    CurrentPos = Window.UIElements.Main.Position
                    CurrentSize = Window.UIElements.Main.Size
                    Window.CanResize = false
                else
                    if Window.Resizable then
                        Window.CanResize = true
                    end
                end

                Tween(Window.UIElements.Main, 0.45, {
                    Size = isFullscreen and CurrentSize or UDim2.new(1, -20, 1, 
-72),
                }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                Tween(Window.UIElements.Main, 0.45, {
                    Position = isFullscreen and CurrentPos or UDim2.new(0.5, 0, 0.5, 26),
                }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()

                Window.IsFullscreen = not isFullscreen
            end

            Window:CreateTopbarButton('Minimize', 'minus', function()
                Window:Close()
            end, 997)

            function Window:OnOpen(func)
                Window.OnOpenCallback = func
            end
            function Window:OnClose(func)
                Window.OnCloseCallback = func
            end
            function Window:OnDestroy(func)
                Window.OnDestroyCallback = func
            end

            if Config.WindUI.UseAcrylic then
                Window.AcrylicPaint.AddParent(Window.UIElements.Main)
            end

            function Window:SetIconSize(Size)
                local NewSize

                if typeof(Size) == 'number' then
                    NewSize = UDim2.new(0, Size, 0, Size)
                    Window.IconSize = Size
                elseif typeof(Size) == 'UDim2' then
                    NewSize = Size
                    Window.IconSize = Size.X.Offset
                end
                if WindowIcon then
                    WindowIcon.Size = NewSize
                end
            end
            function Window:Open()
                task.spawn(function()
                    if Window.OnOpenCallback then
                        task.spawn(function()
                            Creator.SafeCallback(Window.OnOpenCallback)
                        end)
                    end

                    task.wait(0.06)

                    Window.Closed = false

                    Tween(Window.UIElements.Main.Background, 0.2, {
                        ImageTransparency = Window.Transparent and Config.WindUI.TransparencyValue or 0,
                    }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()

                    if Window.UIElements.BackgroundGradient then
                        Tween(Window.UIElements.BackgroundGradient, 0.2, {ImageTransparency = 0}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                    end

                    Tween(Window.UIElements.Main.Background, 0.4, {
                        Size = UDim2.new(1, 0, 1, 0),
                    }, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out):Play()

                    if BGImage then
                        if BGImage:IsA('VideoFrame') then
                            BGImage.Visible = true
                        else
                            Tween(BGImage, 0.2, {
                                ImageTransparency = Window.BackgroundImageTransparency,
                            }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                        end
                    end
                    if Window.OpenButtonMain and Window.IsOpenButtonEnabled then
                        Window.OpenButtonMain:Visible(false)
                    end

                    Tween(Blur, 0.25, {
                        ImageTransparency = Window.ShadowTransparency,
                    }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()

                    if UIStroke then
                        Tween(UIStroke, 0.25, {Transparency = 0.8}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                    end

                    task.spawn(function()
                        task.wait(0.3)
                        Tween(BottomDragFrame, 0.45, {
                            Size = UDim2.new(0, 200, 0, 4),
                            ImageTransparency = 0.8,
                        }, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out):Play()
                        WindowDragModule:Set(true)
                        task.wait(0.45)

                        if Window.Resizable then
                            Tween(ResizeHandle.ImageLabel, 0.45, {ImageTransparency = 0.8}, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out):Play()

                            Window.CanResize = true
                        end
                    end)

                    Window.CanDropdown = true
                    Window.UIElements.Main.Visible = true

                    task.spawn(function()
                        task.wait(0.05)

                        Window.UIElements.Main:WaitForChild('Main').Visible = true

                        Config.WindUI:ToggleAcrylic(true)
                    end)
                end)
            end
            function Window:Close()
                local Close = {}

                if Window.OnCloseCallback then
                    task.spawn(function()
                        Creator.SafeCallback(Window.OnCloseCallback)
                    end)
                end

                Config.WindUI:ToggleAcrylic(false)

                Window.UIElements.Main:WaitForChild('Main').Visible = false
                Window.CanDropdown = false
                Window.Closed = true

                Tween(Window.UIElements.Main.Background, 0.32, {ImageTransparency = 1}, Enum.EasingStyle.Quint, Enum.EasingDirection.InOut):Play()

                if Window.UIElements.BackgroundGradient then
                    Tween(Window.UIElements.BackgroundGradient, 0.32, {ImageTransparency = 1}, Enum.EasingStyle.Quint, Enum.EasingDirection.InOut):Play()
                end

                Tween(Window.UIElements.Main.Background, 0.4, {
                    Size = UDim2.new(1, 0, 1, -240),
                }, Enum.EasingStyle.Exponential, Enum.EasingDirection.InOut):Play()

                if BGImage then
                    if BGImage:IsA('VideoFrame') then
                        BGImage.Visible = false
                    else
                        Tween(BGImage, 0.3, {ImageTransparency = 1}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                    end
                end

                Tween(Blur, 0.25, {ImageTransparency = 1}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()

                if UIStroke then
                    Tween(UIStroke, 0.25, {Transparency = 1}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()
                end

                Tween(BottomDragFrame, 0.3, {
                    Size = UDim2.new(0, 0, 0, 4),
                    ImageTransparency = 1,
                }, Enum.EasingStyle.Exponential, Enum.EasingDirection.InOut):Play()
                Tween(ResizeHandle.ImageLabel, 0.3, {ImageTransparency = 1}, Enum.EasingStyle.Exponential, Enum.EasingDirection.Out):Play()
                WindowDragModule:Set(false)

                Window.CanResize = false

                task.spawn(function()
                    task.wait(0.4)

                    Window.UIElements.Main.Visible = false

                    if Window.OpenButtonMain and not Window.Destroyed and not Window.IsPC and Window.IsOpenButtonEnabled then
                        Window.OpenButtonMain:Visible(true)
                    end
                end)

                function Close:Destroy()
                    task.spawn(function()
                        if Window.OnDestroyCallback then
                            task.spawn(function()
                                Creator.SafeCallback(Window.OnDestroyCallback)
                            end)
                        end
                        if Window.AcrylicPaint and Window.AcrylicPaint.Model then
                            Window.AcrylicPaint.Model:Destroy()
                        end

                        Window.Destroyed = true

                        task.wait(0.4)
                        Config.WindUI.ScreenGui:Destroy()
                        Config.WindUI.NotificationGui:Destroy()
                        Config.WindUI.DropdownGui:Destroy()
                        Creator.DisconnectAll()

                        return
                    end)
                end

                return Close
            end
            function Window:Destroy()
                return Window:Close():Destroy()
            end
            function Window:Toggle()
                if Window.Closed then
                    Window:Open()
                else
                    Window:Close()
                end
            end
            function Window:ToggleTransparency(Value)
                Window.Transparent = Value
                Config.WindUI.Transparent = Value
                Window.UIElements.Main.Background.ImageTransparency = Value and Config.WindUI.TransparencyValue or 0
                Window.UIElements.MainBar.Background.ImageTransparency = Value and 0.97 or 0.95
            end
            function Window:LockAll()
                for _, element in next, Window.AllElements do
                    if element.Lock then
                        element:Lock()
                    end
                end
            end
            function Window:UnlockAll()
                for _, element in next, Window.AllElements do
                    if element.Unlock then
                        element:Unlock()
                    end
                end
            end
            function Window:GetLocked()
                local LockedElements = {}

                for _, element in next, Window.AllElements do
                    if element.Locked then
                        table.insert(LockedElements, element)
                    end
                end

                return LockedElements
            end
            function Window:GetUnlocked()
                local UnlockedElements = {}

                for _, element in next, Window.AllElements do
                    if element.Locked == false then
                        table.insert(UnlockedElements, element)
                    end
                end

                return UnlockedElements
            end
            function Window:GetUIScale(v)
                return Config.WindUI.UIScale
            end
            function Window:SetUIScale(v)
                Config.WindUI.UIScale = v

                Tween(Config.WindUI.UIScaleObj, 0.2, {Scale = v}, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()

                return Window
            end
            function Window:SetToTheCenter()
                Tween(Window.UIElements.Main, 0.45, {
                    Position = UDim2.new(0.5, 0, 0.5, 0),
                }, Enum.EasingStyle.Quint, Enum.EasingDirection.Out):Play()

                return Window
            end
            function Window:SetCurrentConfig(ConfigModule)
                Window.CurrentConfig = ConfigModule
            end

            do
                local Margin = 40
                local ViewportSize = CurrentCamera.ViewportSize
                local WindowSize = Window.UIElements.Main.AbsoluteSize

                if not Window.IsFullscreen and Window.AutoScale then
                    local AvailableWidth = ViewportSize.X - (Margin * 2)
                    local AvailableHeight = ViewportSize.Y - (Margin * 2)
                    local ScaleX = AvailableWidth / WindowSize.X
                    local ScaleY = AvailableHeight / WindowSize.Y
                    local RequiredScale = math.min(ScaleX, ScaleY)
                    local MinScale = 0.3
                    local MaxScale = 1
                    local FinalScale = math.clamp(RequiredScale, MinScale, MaxScale)
                    local CurrentScale = Window:GetUIScale() or 1
                    local Tolerance = 0.05

                    if math.abs(FinalScale - CurrentScale) > Tolerance then
                        Window:SetUIScale(FinalScale)
                    end
                end
            end

            if Window.OpenButtonMain and Window.OpenButtonMain.Button then
                Creator.AddSignal(Window.OpenButtonMain.Button.TextButton.MouseButton1Click, function(
                )
                    Window:Open()
                end)
            end

            Creator.AddSignal(UserInputService.InputBegan, function(
                input,
                isProcessed
            )
                if isProcessed then
                    return
                end
                if Window.ToggleKey then
                    if input.KeyCode == Window.ToggleKey then
                        Window:Toggle()
                    end
                end
            end)
            task.spawn(function()
                Window:Open()
            end)

            function Window:EditOpenButton(OpenButtonConfig)
                return Window.OpenButtonMain:Edit(OpenButtonConfig)
            end

            if Window.OpenButton and typeof(Window.OpenButton) == 'table' then
                Window:EditOpenButton(Window.OpenButton)
            end

            local TabModuleMain = __DARKLUA_BUNDLE_MODULES.load('W')
            local SectionModule = __DARKLUA_BUNDLE_MODULES.load('X')
            local TabModule = TabModuleMain.Init(Window, Config.WindUI, Config.Parent.Parent.ToolTips)

            TabModule:OnChange(function(t)
                Window.CurrentTab = t
            end)

            Window.TabModule = TabModuleMain

            function Window:Tab(TabConfig)
                TabConfig.Parent = Window.UIElements.SideBar.Frame

                return TabModule.New(TabConfig, Config.WindUI.UIScale)
            end
            function Window:SelectTab(Tab)
                TabModule:SelectTab(Tab)
            end
            function Window:Section(SectionConfig)
                return SectionModule.New(SectionConfig, Window.UIElements.SideBar.Frame, Window.Folder, Config.WindUI.UIScale, Window)
            end
            function Window:IsResizable(v)
                Window.Resizable = v
                Window.CanResize = v
            end
            function Window:Divider()
                local Divider = New('Frame', {
                    Size = UDim2.new(1, 0, 0, 1),
                    Position = UDim2.new(0.5, 0, 0, 0),
                    AnchorPoint = Vector2.new(0.5, 0),
                    BackgroundTransparency = 0.9,
                    ThemeTag = {
                        BackgroundColor3 = 'Text',
                    },
                })
                local MainDivider = New('Frame', {
                    Parent = Window.UIElements.SideBar.Frame,
                    Size = UDim2.new(1, -7, 0, 5),
                    BackgroundTransparency = 1,
                }, {Divider})

                return MainDivider
            end

            local DialogModule = __DARKLUA_BUNDLE_MODULES.load('n').Init(Window, nil)

            function Window:Dialog(DialogConfig)
                local DialogTable = {
                    Title = DialogConfig.Title or 'Dialog',
                    Width = DialogConfig.Width or 320,
                    Content = DialogConfig.Content,
                    Buttons = DialogConfig.Buttons or {},
                    TextPadding = 10,
                }
                local Dialog = DialogModule.Create(false)

                Dialog.UIElements.Main.Size = UDim2.new(0, DialogTable.Width, 0, 0)

                local DialogTopFrame = New('Frame', {
                    Size = UDim2.new(1, 0, 0, 0),
                    AutomaticSize = 'Y',
                    BackgroundTransparency = 1,
                    Parent = Dialog.UIElements.Main,
                }, {
                    New('UIListLayout', {
                        FillDirection = 'Horizontal',
                        Padding = UDim.new(0, Dialog.UIPadding),
                        VerticalAlignment = 'Center',
                    }),
                    New('UIPadding', {
                        PaddingTop = UDim.new(0, DialogTable.TextPadding / 2),
                        PaddingLeft = UDim.new(0, DialogTable.TextPadding / 2),
                        PaddingRight = UDim.new(0, DialogTable.TextPadding / 2),
                    }),
                })
                local Icon

                if DialogConfig.Icon then
                    Icon = Creator.Image(DialogConfig.Icon, DialogTable.Title .. ':' .. DialogConfig.Icon, 0, Window, 'Dialog', true, DialogConfig.IconThemed)
                    Icon.Size = UDim2.new(0, 22, 0, 22)
                    Icon.Parent = DialogTopFrame
                end

                Dialog.UIElements.UIListLayout = New('UIListLayout', {
                    Padding = UDim.new(0, 12),
                    FillDirection = 'Vertical',
                    HorizontalAlignment = 'Left',
                    Parent = Dialog.UIElements.Main,
                })

                New('UISizeConstraint', {
                    MinSize = Vector2.new(180, 20),
                    MaxSize = Vector2.new(400, math.huge),
                    Parent = Dialog.UIElements.Main,
                })

                Dialog.UIElements.Title = New('TextLabel', {
                    Text = DialogTable.Title,
                    TextSize = 20,
                    FontFace = Font.new(Creator.Font, Enum.FontWeight.SemiBold),
                    TextXAlignment = 'Left',
                    TextWrapped = true,
                    RichText = true,
                    Size = UDim2.new(1, Icon and -26 - Dialog.UIPadding or 0, 0, 0),
                    AutomaticSize = 'Y',
                    ThemeTag = {
                        TextColor3 = 'Text',
                    },
                    BackgroundTransparency = 1,
                    Parent = DialogTopFrame,
                })

                if DialogTable.Content then
                    New('TextLabel', {
                        Text = DialogTable.Content,
                        TextSize = 18,
                        TextTransparency = 0.4,
                        TextWrapped = true,
                        RichText = true,
                        FontFace = Font.new(Creator.Font, Enum.FontWeight.Medium),
                        TextXAlignment = 'Left',
                        Size = UDim2.new(1, 0, 0, 0),
                        AutomaticSize = 'Y',
                        LayoutOrder = 2,
                        ThemeTag = {
                            TextColor3 = 'Text',
                        },
                        BackgroundTransparency = 1,
                        Parent = Dialog.UIElements.Main,
                    }, {
                        New('UIPadding', {
                            PaddingLeft = UDim.new(0, DialogTable.TextPadding / 2),
                            PaddingRight = UDim.new(0, DialogTable.TextPadding / 2),
                            PaddingBottom = UDim.new(0, DialogTable.TextPadding / 2),
                        }),
                    })
                end

                local ButtonsLayout = New('UIListLayout', {
                    Padding = UDim.new(0, 6),
                    FillDirection = 'Horizontal',
                    HorizontalAlignment = 'Right',
                })
                local ButtonsContent = New('Frame', {
                    Size = UDim2.new(1, 0, 0, 40),
                    AutomaticSize = 'None',
                    BackgroundTransparency = 1,
                    Parent = Dialog.UIElements.Main,
                    LayoutOrder = 4,
                }, {ButtonsLayout})
                local Buttons = {}

                for _, Button in next, DialogTable.Buttons do
                    local ButtonFrame = CreateButton(Button.Title, Button.Icon, Button.Callback, Button.Variant, ButtonsContent, Dialog, false)

                    table.insert(Buttons, ButtonFrame)
                end

                local CheckButtonsOverflow = function()
                    ButtonsLayout.FillDirection = Enum.FillDirection.Horizontal
                    ButtonsLayout.HorizontalAlignment = Enum.HorizontalAlignment.Right
                    ButtonsLayout.VerticalAlignment = Enum.VerticalAlignment.Center
                    ButtonsContent.AutomaticSize = Enum.AutomaticSize.None

                    for _, button in ipairs(Buttons)do
                        button.Size = UDim2.new(0, 0, 1, 0)
                        button.AutomaticSize = Enum.AutomaticSize.X
                    end

                    wait()

                    local totalWidth = ButtonsLayout.AbsoluteContentSize.X / Config.WindUI.UIScale
                    local parentWidth = ButtonsContent.AbsoluteSize.X / Config.WindUI.UIScale

                    if totalWidth > parentWidth then
                        ButtonsLayout.FillDirection = Enum.FillDirection.Vertical
                        ButtonsLayout.HorizontalAlignment = Enum.HorizontalAlignment.Right
                        ButtonsLayout.VerticalAlignment = Enum.VerticalAlignment.Bottom
                        ButtonsContent.AutomaticSize = Enum.AutomaticSize.Y

                        for _, button in ipairs(Buttons)do
                            button.Size = UDim2.new(1, 0, 0, 40)
                            button.AutomaticSize = Enum.AutomaticSize.None
                        end
                    else
                        local availableSpace = parentWidth - totalWidth

                        if availableSpace > 0 then
                            local smallestButton = nil
                            local smallestWidth = math.huge

                            for _, button in ipairs(Buttons)do
                                local buttonWidth = button.AbsoluteSize.X / Config.WindUI.UIScale

                                if buttonWidth < smallestWidth then
                                    smallestWidth = buttonWidth
                                    smallestButton = button
                                end
                            end

                            if smallestButton then
                                smallestButton.Size = UDim2.new(0, smallestWidth + availableSpace, 1, 0)
                                smallestButton.AutomaticSize = Enum.AutomaticSize.None
                            end
                        end
                    end
                end

                Creator.AddSignal(Dialog.UIElements.Main:GetPropertyChangedSignal('AbsoluteSize'), CheckButtonsOverflow)
                CheckButtonsOverflow()
                wait()
                Dialog:Open()

                return Dialog
            end

            Window:CreateTopbarButton('Close', 'x', function()
                if not Window.IgnoreAlerts then
                    Window:SetToTheCenter()
                    Window:Dialog({
                        Title = 'Close Window',
                        Content = 
[[Do you want to close this window? You will not be able to open it again.]],
                        Buttons = {
                            {
                                Title = 'Cancel',
                                Callback = function() end,
                                Variant = 'Secondary',
                            },
                            {
                                Title = 'Close Window',
                                Callback = function()
                                    Window:Destroy()
                                end,
                                Variant = 'Primary',
                            },
                        },
                    })
                else
                    Window:Destroy()
                end
            end, 999)

            function Window:Tag(TagConfig)
                if Window.UIElements.Main.Main.Topbar.Center.Visible == false then
                    Window.UIElements.Main.Main.Topbar.Center.Visible = true
                end

                return Tag:New(TagConfig, Window.UIElements.Main.Main.Topbar.Center)
            end

            local startResizing = function(input)
                if Window.CanResize then
                    isResizing = true
                    FullScreenIcon.Active = true
                    initialSize = Window.UIElements.Main.Size
                    initialInputPosition = input.Position

                    Tween(ResizeHandle.ImageLabel, 0.1, {ImageTransparency = 0.35}):Play()
                    Creator.AddSignal(input.Changed, function()
                        if input.UserInputState == Enum.UserInputState.End then
                            isResizing = false
                            FullScreenIcon.Active = false

                            Tween(ResizeHandle.ImageLabel, 0.17, {ImageTransparency = 0.8}):Play()
                        end
                    end)
                end
            end

            Creator.AddSignal(ResizeHandle.InputBegan, function(input)
                if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                    if Window.CanResize then
                        startResizing(input)
                    end
                end
            end)
            Creator.AddSignal(UserInputService.InputChanged, function(input)
                if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
                    if isResizing and Window.CanResize then
                        local delta = input.Position - initialInputPosition
                        local newSize = UDim2.new(0, initialSize.X.Offset + delta.X * 2, 0, initialSize.Y.Offset + delta.Y * 2)

                        newSize = UDim2.new(newSize.X.Scale, math.clamp(newSize.X.Offset, Window.MinSize.X, Window.MaxSize.X), newSize.Y.Scale, math.clamp(newSize.Y.Offset, Window.MinSize.Y, Window.MaxSize.Y))

                        Tween(Window.UIElements.Main, 0, {Size = newSize}):Play()

                        Window.Size = newSize
                    end
                end
            end)

            local LastUpTime = 0
            local DoubleClickWindow = 0.4
            local InitialPosition = nil
            local ClickCount = 0

            function onDoubleClick()
                Window:SetToTheCenter()
            end

            BottomDragFrame.Frame.MouseButton1Up:Connect(function()
                local currentTime = tick()
                local currentPosition = Window.Position

                ClickCount = ClickCount + 1

                if ClickCount == 1 then
                    LastUpTime = currentTime
                    InitialPosition = currentPosition

                    task.spawn(function()
                        task.wait(DoubleClickWindow)

                        if ClickCount == 1 then
                            ClickCount = 0
                            InitialPosition = nil
                        end
                    end)
                elseif ClickCount == 2 then
                    if currentTime - LastUpTime <= DoubleClickWindow and currentPosition == InitialPosition then
                        onDoubleClick()
                    end

                    ClickCount = 0
                    InitialPosition = nil
                    LastUpTime = 0
                else
                    ClickCount = 1
                    LastUpTime = currentTime
                    InitialPosition = currentPosition
                end
            end)

            if not Window.HideSearchBar then
                local SearchBar = __DARKLUA_BUNDLE_MODULES.load('Z')
                local IsOpen = false
                local SearchLabel = CreateLabel('Search', 'search', Window.UIElements.SideBarContainer, true)

                SearchLabel.Size = UDim2.new(1, -Window.UIPadding / 2, 0, 39)
                SearchLabel.Position = UDim2.new(0, Window.UIPadding / 2, 0, Window.UIPadding / 2)

                Creator.AddSignal(SearchLabel.MouseButton1Click, function()
                    if IsOpen then
                        return
                    end

                    SearchBar.new(Window.TabModule, Window.UIElements.Main, function(
                    )
                        IsOpen = false

                        if Window.Resizable then
                            Window.CanResize = true
                        end

                        Tween(FullScreenBlur, 0.1, {ImageTransparency = 1}):Play()

                        FullScreenBlur.Active = false
                    end)
                    Tween(FullScreenBlur, 0.1, {ImageTransparency = 0.65}):Play()

                    FullScreenBlur.Active = true
                    IsOpen = true
                    Window.CanResize = false
                end)
            end

            function Window:DisableTopbarButtons(btns)
                for _, b in next, btns do
                    for _, i in next, Window.TopBarButtons do
                        if i.Name == b then
                            i.Object.Visible = false
                        end
                    end
                end
            end

            return Window
        end
    end
end

local WindUI = {
    Window = nil,
    Theme = nil,
    Creator = __DARKLUA_BUNDLE_MODULES.load('d'),
    LocalizationModule = __DARKLUA_BUNDLE_MODULES.load('e'),
    NotificationModule = __DARKLUA_BUNDLE_MODULES.load('f'),
    Themes = nil,
    Transparent = false,
    TransparencyValue = 0.15,
    UIScale = 1,
    ConfigManager = nil,
    Version = '0.0.0',
    Services = __DARKLUA_BUNDLE_MODULES.load('j'),
    OnThemeChangeFunction = nil,
    cloneref = nil,
    UIScaleObj = nil,
}
local cloneref = (cloneref or clonereference or function(instance)
    return instance
end)

WindUI.cloneref = cloneref

local HttpService = cloneref(game:GetService('HttpService'))
local Players = cloneref(game:GetService('Players'))
local CoreGui = cloneref(game:GetService('CoreGui'))
local _ = Players.LocalPlayer or nil
local Package = HttpService:JSONDecode(__DARKLUA_BUNDLE_MODULES.load('k'))

if Package then
    WindUI.Version = Package.version
end

local KeySystem = __DARKLUA_BUNDLE_MODULES.load('o')
local _ = WindUI.Services
local Creator = WindUI.Creator
local New = Creator.New
local _ = Creator.Tween
local Acrylic = __DARKLUA_BUNDLE_MODULES.load('s')
local ProtectGui = protectgui or (syn and syn.protect_gui) or function() end
local GUIParent = gethui and gethui() or (CoreGui or game.Players.LocalPlayer:WaitForChild('PlayerGui'))
local UIScaleObj = New('UIScale', {
    Scale = WindUI.Scale,
})

WindUI.UIScaleObj = UIScaleObj
WindUI.ScreenGui = New('ScreenGui', {
    Name = 'WindUI',
    Parent = GUIParent,
    IgnoreGuiInset = true,
    ScreenInsets = 'None',
}, {
    New('Folder', {
        Name = 'Window',
    }),
    New('Folder', {
        Name = 'KeySystem',
    }),
    New('Folder', {
        Name = 'Popups',
    }),
    New('Folder', {
        Name = 'ToolTips',
    }),
})
WindUI.NotificationGui = New('ScreenGui', {
    Name = 'WindUI/Notifications',
    Parent = GUIParent,
    IgnoreGuiInset = true,
})
WindUI.DropdownGui = New('ScreenGui', {
    Name = 'WindUI/Dropdowns',
    Parent = GUIParent,
    IgnoreGuiInset = true,
})

ProtectGui(WindUI.ScreenGui)
ProtectGui(WindUI.NotificationGui)
ProtectGui(WindUI.DropdownGui)
Creator.Init(WindUI)

function WindUI:SetParent(parent)
    WindUI.ScreenGui.Parent = parent
    WindUI.NotificationGui.Parent = parent
    WindUI.DropdownGui.Parent = parent
end

math.clamp(WindUI.TransparencyValue, 0, 1)

local Holder = WindUI.NotificationModule.Init(WindUI.NotificationGui)

function WindUI:Notify(Config)
    Config.Holder = Holder.Frame
    Config.Window = WindUI.Window

    return WindUI.NotificationModule.New(Config)
end
function WindUI:SetNotificationLower(Val)
    Holder.SetLower(Val)
end
function WindUI:SetFont(FontId)
    Creator.UpdateFont(FontId)
end
function WindUI:OnThemeChange(func)
    WindUI.OnThemeChangeFunction = func
end
function WindUI:AddTheme(LTheme)
    WindUI.Themes[LTheme.Name] = LTheme

    return LTheme
end
function WindUI:SetTheme(Value)
    if WindUI.Themes[Value] then
        WindUI.Theme = WindUI.Themes[Value]

        Creator.SetTheme(WindUI.Themes[Value])

        if WindUI.OnThemeChangeFunction then
            WindUI.OnThemeChangeFunction(Value)
        end

        return WindUI.Themes[Value]
    end

    return nil
end
function WindUI:GetThemes()
    return WindUI.Themes
end
function WindUI:GetCurrentTheme()
    return WindUI.Theme.Name
end
function WindUI:GetTransparency()
    return WindUI.Transparent or false
end
function WindUI:GetWindowSize()
    return Window.UIElements.Main.Size
end
function WindUI:Localization(LocalizationConfig)
    return WindUI.LocalizationModule:New(LocalizationConfig, Creator)
end
function WindUI:SetLanguage(Value)
    if Creator.Localization then
        return Creator.SetLanguage(Value)
    end

    return false
end
function WindUI:ToggleAcrylic(Value)
    if WindUI.Window and WindUI.Window.AcrylicPaint and WindUI.Window.AcrylicPaint.Model then
        WindUI.Window.Acrylic = Value
        WindUI.Window.AcrylicPaint.Model.Transparency = Value and 0.98 or 1

        if Value then
            Acrylic.Enable()
        else
            Acrylic.Disable()
        end
    end
end
function WindUI:Gradient(stops, props)
    local colorSequence = {}
    local transparencySequence = {}

    for posStr, stop in next, stops do
        local position = tonumber(posStr)

        if position then
            position = math.clamp(position / 100, 0, 1)

            table.insert(colorSequence, ColorSequenceKeypoint.new(position, stop.Color))
            table.insert(transparencySequence, NumberSequenceKeypoint.new(position, stop.Transparency or 0))
        end
    end

    table.sort(colorSequence, function(a, b)
        return a.Time < b.Time
    end)
    table.sort(transparencySequence, function(a, b)
        return a.Time < b.Time
    end)

    if #colorSequence < 2 then
        error('ColorSequence requires at least 2 keypoints')
    end

    local gradientData = {
        Color = ColorSequence.new(colorSequence),
        Transparency = NumberSequence.new(transparencySequence),
    }

    if props then
        for k, v in pairs(props)do
            gradientData[k] = v
        end
    end

    return gradientData
end
function WindUI:Popup(PopupConfig)
    PopupConfig.WindUI = WindUI

    return __DARKLUA_BUNDLE_MODULES.load('t').new(PopupConfig)
end

WindUI.Themes = __DARKLUA_BUNDLE_MODULES.load('u')(WindUI)
Creator.Themes = WindUI.Themes

WindUI:SetTheme('Dark')
WindUI:SetLanguage(Creator.Language)

function WindUI:CreateWindow(Config)
    local CreateWindow = __DARKLUA_BUNDLE_MODULES.load('_')

    if not isfolder('WindUI') then
        makefolder('WindUI')
    end
    if Config.Folder then
        makefolder(Config.Folder)
    else
        makefolder(Config.Title)
    end

    Config.WindUI = WindUI
    Config.Parent = WindUI.ScreenGui.Window

    if WindUI.Window then
        warn('You cannot create more than one window')

        return
    end

    local CanLoadWindow = true
    local Theme = WindUI.Themes[Config.Theme or 'Dark']

    Creator.SetTheme(Theme)

    local hwid = gethwid or function()
        return Players.LocalPlayer.UserId
    end
    local Filename = hwid()

    if Config.KeySystem then
        CanLoadWindow = false

        local loadKeysystem = function()
            KeySystem.new(Config, Filename, function(c)
                CanLoadWindow = c
            end)
        end
        local keyPath = (Config.Folder or 'Temp') .. '/' .. Filename .. '.key'

        if Config.KeySystem.KeyValidator then
            if Config.KeySystem.SaveKey and isfile(keyPath) then
                local savedKey = readfile(keyPath)
                local isValid = Config.KeySystem.KeyValidator(savedKey)

                if isValid then
                    CanLoadWindow = true
                else
                    loadKeysystem()
                end
            else
                loadKeysystem()
            end
        elseif not Config.KeySystem.API then
            if Config.KeySystem.SaveKey and isfile(keyPath) then
                local savedKey = readfile(keyPath)
                local isKey = (type(Config.KeySystem.Key) == 'table') and table.find(Config.KeySystem.Key, savedKey) or tostring(Config.KeySystem.Key) == tostring(savedKey)

                if isKey then
                    CanLoadWindow = true
                else
                    loadKeysystem()
                end
            else
                loadKeysystem()
            end
        else
            if isfile(keyPath) then
                local fileKey = readfile(keyPath)
                local isSuccess = false

                for _, i in next, Config.KeySystem.API do
                    local serviceData = WindUI.Services[i.Type]

                    if serviceData then
                        local args = {}

                        for _, argName in next, serviceData.Args do
                            table.insert(args, i[argName])
                        end

                        local service = serviceData.New(table.unpack(args))
                        local success = service.Verify(fileKey)

                        if success then
                            isSuccess = true

                            break
                        end
                    end
                end

                CanLoadWindow = isSuccess

                if not isSuccess then
                    loadKeysystem()
                end
            else
                loadKeysystem()
            end
        end

        repeat
            task.wait()
        until CanLoadWindow
    end

    local Window = CreateWindow(Config)

    WindUI.Transparent = Config.Transparent
    WindUI.Window = Window

    if Config.Acrylic then
        Acrylic.init()
    end

    return Window
end

return WindUI
