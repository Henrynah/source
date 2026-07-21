-- ts file was generated with delusion v2 | bat i love u 
local Env = getfenv();
local solarisui = loadstring(game:HttpGet("https://raw.githubusercontent.com/katnaa-debug/solarisobf/refs/heads/main/solarisui.lua"))();
loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source"))();
local LocalPlayer = r25.Players.LocalPlayer;
local list = {
    "Left Leg",
    "Right Leg",
    "Left Arm",
    "Right Arm"
};
local r28 = {
    ["Selected"] = {
        ["kickPlayer"] = nil,
        ["players"] = {},
        ["multiSelect"] = false,
        ["currentPlayer"] = nil,
        ["currentButton"] = nil
    },
    ["Teleport"] = {
        ["loop"] = {
            ["enabled"] = false,
            ["target"] = CFrame.new(3, -7, -2),
            ["loopThread"] = nil
        },
        ["houses"] = {
            {
                "Pink House",
                CFrame.new(-491, -7, -166)
            },
            {
                "Green House",
                CFrame.new(-535, -7, 93)
            },
            {
                "Purple House",
                CFrame.new(250, -6, 463)
            },
            {
                "China House",
                CFrame.new(554, 123, -72)
            },
            {
                "Blue House",
                CFrame.new(510, 83, -339)
            },
            {
                "Spawn",
                CFrame.new(3, -7, -2)
            }
        }
    },
    ["Speed"] = {
        ["value"] = 16,
        ["min"] = 16,
        ["max"] = 10000,
        ["enabled"] = false,
        ["originalWalkSpeed"] = nil,
        ["connection"] = nil
    },
    ["ThirdPerson"] = {
        ["enabled"] = false
    },
    ["PCLD"] = {
        ["Enabled"] = false,
        ["Boxes"] = {},
        ["NameTags"] = {},
        ["Connections"] = {}
    },
    ["PacketLag"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["packetSize"] = 100,
        ["minSize"] = 100,
        ["maxSize"] = 1000000
    },
    ["KickNotify"] = {
        ["enabled"] = false,
        ["connection"] = nil,
        ["notifications"] = {},
        ["notificationQueue"] = {},
        ["processedKicks"] = {}
    },
    ["Gucci"] = {
        ["active"] = false,
        ["blobman"] = nil,
        ["connection"] = nil,
        ["remotes"] = {}
    },
    ["GucciTractor"] = {
        ["enabled"] = false,
        ["busy"] = false,
        ["currentTractor"] = nil,
        ["seatMonitorConn"] = nil,
        ["loopThread"] = nil
    },
    ["AntiLag"] = {
        ["enabled"] = false
    },
    ["RemoveAntiKick"] = {
        ["enabled"] = false,
        ["targetPlayer"] = nil,
        ["loopThread"] = nil
    },
    ["AntiGrab"] = {
        ["enabled"] = false,
        ["antiGrabEnabled"] = false
    },
    ["SafePos"] = {
        ["enabled"] = false,
        ["target"] = nil,
        ["loopThread"] = nil
    },
    ["AutoSitBlob"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["currentBlob"] = nil
    },
    ["AntiKickReset"] = {
        ["enabled"] = false,
        ["connection"] = nil
    },
    ["AntiStick"] = {
        ["enabled"] = false,
        ["loopThread"] = nil
    },
    ["AntiKickStick"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["selectedItem"] = "NinjaShuriken",
        ["items"] = {
            "NinjaShuriken",
            "NinjaKunai",
            "ToolPencil",
            "ToolPickaxe",
            "ToolDiggingForkRusty",
            "ToolCleaver",
            "NinjaKatana"
        }
    },
    ["LoopKick"] = {
        ["enabled"] = false,
        ["loopThread"] = nil
    },
    ["PermanentPallet"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["currentPallet"] = nil,
        ["isReady"] = false
    },
    ["RagdollAura"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["ragdolledPlayers"] = {},
        ["isTeleportingPallet"] = false
    },
    ["RagdollLoopKick"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["savedPos"] = nil,
        ["dragging"] = false,
        ["grabStart"] = 0
    },
    ["SelfGrab"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["MyBlob"] = nil,
        ["OriginalMassless"] = nil,
        ["OriginalPosition"] = nil,
        ["WasGrabbedByEnemy"] = false,
        ["ProcessingGrab"] = false,
        ["ProcessingRelease"] = false,
        ["GrabCooldown"] = 0,
        ["RegrabActive"] = false,
        ["SpawningBlob"] = false
    },
    ["BlobKill"] = {
        ["enabled"] = false,
        ["selectedPlayers"] = {},
        ["loopThread"] = nil,
        ["barrierThread"] = nil,
        ["currentBlob"] = nil
    },
    ["BlobKickDual"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["bp"] = nil,
        ["lastTargetChar"] = nil
    },
    ["Fly"] = {
        ["Enabled"] = false,
        ["Speed"] = 100,
        ["BV"] = nil,
        ["BG"] = nil,
        ["Connections"] = {}
    },
    ["ServerLag"] = {
        ["enabled"] = false,
        ["loopThread"] = nil
    },
    ["AntiInputLag"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["currentItem"] = nil,
        ["selectedItem"] = "FoodHamburger",
        ["items"] = {
            "FoodHamburger",
            "InstrumentDrumBongos",
            "InstrumentBrassTrumpet",
            "InstrumentBrassBugle",
            "InstrumentVoiceMicrophone",
            "InstrumentWoodwindOcarina",
            "InstrumentGuitarUkulele",
            "InstrumentGuitarLyre",
            "InstrumentGuitarBanjo",
            "InstrumentGuitarAcoustic",
            "CupMugBrown",
            "CupMugWhite",
            "FoodBanana",
            "FoodBread",
            "FoodBroccoli",
            "FoodCakePink",
            "FoodCoconut",
            "FoodDippyEgg",
            "FoodDonut",
            "FoodFrenchFries",
            "FoodHotdog",
            "FoodMayonnaise",
            "FoodMeatStick",
            "FoodMushroomPoison",
            "FoodPizzaCheese",
            "FoodPizzaPepperoni",
            "FoodSodaCan",
            "PoopPile",
            "PoopPileSparkle"
        }
    },
    ["AntiPacketLag"] = idk,
    ["AntiRagdoll"] = {
        ["enabled"] = false,
        ["connections"] = {},
        ["ragdolledSit"] = false
    },
    ["PacketLag"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["packetSize"] = 50000,
        ["minSize"] = 100,
        ["maxSize"] = 1000000
    },
    ["ServerLag"] = {
        ["enabled"] = false,
        ["loopThread"] = nil
    },
    ["KillAura"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["currentBlob"] = nil,
        ["spawningBlob"] = false,
        ["killCooldown"] = {},
        ["targetPlayers"] = {},
        ["lastKillTime"] = 0
    },
    ["AntiPacketLag"] = {
        ["enabled"] = false,
        ["connections"] = {},
        ["blockedPackets"] = 0
    },
    ["AntiBlob"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["processedPlayers"] = {}
    },
    ["WaterWalk"] = {
        ["enabled"] = false,
        ["waterParts"] = {},
        ["originalStates"] = {}
    },
    ["AntiBurn"] = {
        ["enabled"] = false,
        ["connection1"] = nil,
        ["connection2"] = nil,
        ["currentBarrier"] = nil
    },
    ["AntiVoid"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["originalDestroyHeight"] = -100
    },
    ["AntiPaint"] = {
        ["enabled"] = false,
        ["connection"] = nil
    },
    ["AdvancedDuallock"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["currentBlob"] = nil,
        ["targetPlayer"] = nil
    },
    ["LoopSnowball"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["currentSnowball"] = nil,
        ["soundPart"] = nil,
        ["selectedPlayer"] = nil
    },
    ["DestroyKickAura"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["currentBlob"] = nil,
        ["processedPlayers"] = {},
        ["spawnInterval"] = 0,
        ["isSpawning"] = false,
        ["lastSpawnTime"] = 0
    },
    ["DeleteLegs"] = {
        ["enabled"] = false,
        ["monitoringThread"] = nil,
        ["hipHeightAdjusted"] = false
    },
    ["AntiExplode"] = {
        ["enabled"] = false,
        ["connection"] = nil,
        ["bombEvent"] = nil
    },
    ["AutoGucci"] = {
        ["enabled"] = false,
        ["monitoringThread"] = nil,
        ["connection"] = nil,
        ["safePosition"] = nil,
        ["restoreFrames"] = 0
    },
    ["AntiBananaEat"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["scanConnection"] = nil,
        ["processedBananas"] = {},
        ["processedFolders"] = {}
    },
    ["AntiAntiInput"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["scanConnection"] = nil,
        ["processedItems"] = {},
        ["processedFolders"] = {}
    },
    ["AntiBarrier"] = {
        ["enabled"] = false,
        ["originalStates"] = {}
    },
    ["AntiInvisible"] = {
        ["enabled"] = false,
        ["loopThread"] = nil
    },
    ["SantaSleighGucci"] = {
        ["enabled"] = false
    },
    ["Noclip"] = {
        ["enabled"] = false,
        ["connection"] = nil,
        ["parts"] = {}
    },
    ["JumpKickAura"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["processedPlayers"] = {}
    },
    ["KickAuraFixed"] = {
        ["enabled"] = false,
        ["loopThread"] = nil,
        ["fixedPlayers"] = {}
    }
};
local Selected = r28.Selected;
local Teleport = r28.Teleport;
local v16 = r28.Speed;
local character = r28.PacketLag;
local v18 = r28.KickNotify;
local v19 = r28.AntiGrab;
local v20 = r28.SafePos;
local AutoSitBlob = r28.AutoSitBlob;
local AntiKickReset = r28.AntiKickReset;
local AntiKickStick = r28.AntiKickStick;
local LoopKick = r28.LoopKick;
local PermanentPallet = r28.PermanentPallet;
local RagdollAura = r28.RagdollAura;
local RagdollLoopKick = r28.RagdollLoopKick;
local SelfGrab2 = r28.SelfGrab;
local BlobKill = r28.BlobKill;
local AntiRagdoll = r28.AntiRagdoll;
local KillAura = r28.KillAura;
local AntiBlob = r28.AntiBlob;
local AntiBurn = r28.AntiBurn;
local AntiVoid = r28.AntiVoid;
local AntiPaint = r28.AntiPaint;
local AdvancedDuallock = r28.AdvancedDuallock;
local DestroyKickAura = r28.DestroyKickAura;
local DeleteLegs = r28.DeleteLegs;
local RemoveAntiKick = r28.RemoveAntiKick;
local JumpKickAura = r28.JumpKickAura;
local KickAuraFixed = r28.KickAuraFixed;
local PacketLag = r28.PacketLag;
local ServerLag = r28.ServerLag;
local function r44(...)
    pcall(function(...)
        local sound = Instance.new("Sound");
        sound.SoundId = "rbxassetid://8745692251";
        sound.Volume = 0.5;
        local SoundService = game:GetService("SoundService");
        sound.Parent = SoundService;
        sound:Play();
        task.delay(3, function(...)
            if r45 and r45.IsPlaying then
                r45:Stop();
                r45:Destroy();
            end;
            return; 
        end);
        return; 
    end); 
end;
local function v43(...)
    pcall(function(...)
        local sound = Instance.new("Sound");
        sound.SoundId = "rbxassetid://5463227301";
        sound.Volume = 0.5;
        local SoundService = game:GetService("SoundService");
        sound.Parent = SoundService;
        sound:Play();
        task.delay(4.7, function(...)
            if r46 and r46.IsPlaying then
                r46:Stop();
                r46:Destroy();
            end;
            return; 
        end);
        return; 
    end); 
end;
local Window = solarisui:CreateWindow({
    ["Title"] = "Amethyst premium",
    ["Theme"] = "Void",
    ["ToggleKey"] = Enum.KeyCode.RightShift,
    ["Transparency"] = .15,
    ["ShowWatermark"] = true
});
task.spawn(function(...)
    print("[AMETHYST] Ожидание загрузки игры...");
    local O = not game:IsLoaded();
    if O then
        game.Loaded:Wait();
    end;
    if not game.Players.LocalPlayer then
        local LocalPlayer = game.Players.LocalPlayer;
        task.wait(.1);
        if game.Players.LocalPlayer then
            local LocalPlayer = game.Players.LocalPlayer;
            ref1 = not LocalPlayer.Character;
            if ref1 then
                ref1 = LocalPlayer.CharacterAdded;
                ref1:Wait();
            end;
            task.wait(3);
            print("[AMETHYST] ===== АКТИВАЦИЯ ВСЕХ ЗАЩИТНЫХ ФУНКЦИЙ =====");
            pcall(function(...)
                print("[AMETHYST] Запуск Kick Notify...");
                local screenGui = Instance.new("ScreenGui");
                screenGui.Name = "KickNotifyUI";
                screenGui.Parent = game:GetService("CoreGui");
                local r49 = {};
                local r50 = {};
                local function r52(arg1_4, ...)
                    if not r50[arg1_4] then
                        return true;
                    end;
                    if tick() - r50[arg1_4] > 30 then
                        r50[arg1_4] = nil;
                        return true;
                    end;
                    return false; 
                end;
                local function r57(...)
                    local k = {};
                    local ref2 = 124[2];
                    for SoundService, ref2 in ipairs(ref2) do
                        if not ref2 or not v2.Parent then
                            table.insert({}, SoundService);
                        end; 
                    end;
                    for T = #k, 1, -1 do
                        table.remove(r49, k[SoundService]); 
                    end;
                    return; 
                end;
                workspace.ChildAdded:Connect(function(arg1_6, ...)
                    if k.Name == "BlackHoleKick" or k.Name == "BlackHole" then
                        k.Name = "BlackHole_Detected_" .. tick();
                        local v = v[1];
                        for o, game in pairs(v:GetPlayers()) do
                            local ref3 = o;
                            if ref3 ~= ref3.Players.LocalPlayer then
                                ({})[v4.Name] = v4.DisplayName;
                            end; 
                        end;
                        task.wait(3.25);
                        local SoundService = {};
                        Players = game.Players;
                        for Players, character2 in pairs(N(character2)) do
                            Players = Players;
                            SoundService[character2.Name] = true; 
                        end;
                        o = {};
                        Players = character2[2];
                        for Players, Players in pairs({}) do
                            if not SoundService[Players] then
                                table.insert({}, {
                                    ["name"] = Players,
                                    ["display"] = Players
                                });
                            end; 
                        end;
                        if #o > 0 then
                            Players = character2[3];
                            for Players, Players in Players:GetPlayers(), ipairs(o) do
                                r57();
                                r53(v5.name, v5.display);
                                task.wait(.1); 
                            end;
                        end;
                    end;
                    return; 
                end);
                print("[AMETHYST] Packet Lag Notify активен");
                local function r60(...)
                    local k = game:GetService("ReplicatedStorage");
                    grabEvents = k:WaitForChild("GrabEvents");
                    grabEvents = (grabEvents:WaitForChild("ExtendGrabLine") and nil).OnClientEvent;
                    return; 
                end;
                r60();
                RS.ChildAdded:Connect(function(arg1_8, ...)
                    if arg1_8.Name == "GrabEvents" then
                        task.wait(1);
                        print("[PACKET LAG] Переподключаю GrabEvents...");
                        r60();
                    end;
                    return; 
                end);
                print("[AMETHYST] ✅ Kick Notify + Packet Lag активирован");
                return; 
            end);
            task.wait();
            pcall(function(...)
                local LocalPlayer = game.Players.LocalPlayer;
                LocalPlayer.CameraMaxZoomDistance = 1250;
                LocalPlayer.CameraMode = Enum.CameraMode.Classic;
                print("[AMETHYST] ✅ Third Person разблокирована");
                return; 
            end);
            task.wait();
            pcall(function(...)
                O.enabled = true;
                if LocalPlayer and LocalPlayer.PlayerScripts then
                    local characterAndBeamMove = LocalPlayer.PlayerScripts:FindFirstChild("CharacterAndBeamMove");
                    if characterAndBeamMove then
                        characterAndBeamMove.Enabled = false;
                    end;
                end;
                print("[AMETHYST] ✅ Anti Lag активен");
                return; 
            end);
            task.wait();
            pcall(function(...)
                r28.AntiKickReset.enabled = true;
                local ReplicatedStorage = r25.ReplicatedStorage;
                if ReplicatedStorage:FindFirstChild("GameCorrectionEvents") then
                    ReplicatedStorage = k:FindFirstChild("GameCorrectionsNotify");
                    if ReplicatedStorage then
                        local ref4 = ReplicatedStorage:IsA("RemoteEvent");
                    end;
                    if ReplicatedStorage then
                        local OnClientEvent = ReplicatedStorage.OnClientEvent;
                        r28.AntiKickReset.connection = OnClientEvent:Connect(function(arg1_9, ...)
                            if arg1_9 == "Flying" then
                                local Character = LocalPlayer.Character;
                                if Character then
                                    local humanoid = Character:FindFirstChild("Humanoid");
                                    if humanoid then
                                        SoundService.Health = 0;
                                    end;
                                end;
                            end;
                            return; 
                        end);
                    end;
                end;
                print("[AMETHYST] ✅ Anti Kick Reset активен");
                return; 
            end);
            task.wait();
            pcall(function(...)
                r28.AntiBarrier.enabled = true;
                r28.AntiBarrier.originalStates = {};
                task.spawn(function(...)
                    local O = true;
                    while true --[[O]] do
                        if r25.Workspace:FindFirstChild("Plots") then
                            for SoundService, game in ipairs(N(game)) do
                                if game:FindFirstChild("Barrier") then
                                    for enabled2, G in G[1], ipairs(G(game)) do
                                        if G:IsA("BasePart") and G.Name == "PlotBarrier" then
                                            r28.AntiBarrier.originalStates[G] = G.CanCollide;
                                            G.CanCollide = false;
                                        end; 
                                    end;
                                end; 
                            end;
                            task.wait(2);
                        end; 
                    end;
                    return; 
                end);
                print("[AMETHYST] ✅ Anti Barrier активен");
                return; 
            end);
            task.wait(0.5);
            pcall(function(...)
                r28.AntiBurn.enabled = true;
                local function r64(...)
                    local Character = LocalPlayer.Character;
                    if not Character then
                        return;
                    end;
                    local humanoid = Character:FindFirstChild("Humanoid");
                    local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                    if not humanoid or not humanoidRootPart then
                        return;
                    end;
                    local Plots = r25.Workspace.Plots;
                    local plot1 = Plots:FindFirstChild("Plot1");
                    if plot1 then
                        local barrier = plot1:FindFirstChild("Barrier");
                    end;
                    local r67 = r67 and {};
                    if not r67 then
                        return;
                    end;
                    r28.AntiBurn.currentBarrier = r67;
                    Plots = r28.AntiBurn.connection1;
                    if Plots then
                        Plots = r28.AntiBurn.connection1;
                        Plots:Disconnect();
                    end;
                    local Changed = humanoid.FireDebounce.Changed;
                    r28.AntiBurn.connection1 = Changed:Connect(function(...)
                        if r65.FireDebounce.Value == true then
                            task.spawn(function(...)
                                local k = not r65.FireDebounce.Value;
                                local ref5 = k;
                                if k then
                                    task.wait();
                                    r67.CFrame = r66.CFrame;
                                else
                                    ref5 = not r28.AntiBurn.enabled;
                                end; 
                            end);
                            task.wait(1);
                            if r28.AntiBurn["enabled"] then
                                r65.FireDebounce.Value = false;
                                task.wait();
                                r67.CFrame = r67.CFrame;
                            end;
                        end;
                        return; 
                    end);
                    return; 
                end;
                if LocalPlayer.Character then
                    r64();
                end;
                LocalPlayer.CharacterAdded:Connect(function(...)
                    task.wait(0.5);
                    if r28.AntiBurn.enabled then
                        r64();
                    end;
                    return; 
                end);
                print("[AMETHYST] ✅ Anti Burn активен");
                return; 
            end);
            task.wait(0.5);
            pcall(function(...)
                r28.AntiVoid.enabled = true;
                r28.AntiVoid.originalDestroyHeight = r25.Workspace.FallenPartsDestroyHeight;
                r25.Workspace.FallenPartsDestroyHeight = -50000;
                task.spawn(function(...)
                    while r28.AntiVoid.enabled do
                        if LocalPlayer.Character then
                            local humanoidRootPart = k:FindFirstChild("HumanoidRootPart");
                            local ref6 = "task";
                            if humanoidRootPart then
                                ref6 = humanoidRootPart.Position.Y < -400;
                            end;
                            if ref6 then
                                O:SetPrimaryPartCFrame(CFrame.new(0, 0, 0));
                            end;
                            task.wait(.1);
                        end; 
                    end;
                    return; 
                end);
                print("[AMETHYST] ✅ Anti Void активен");
                return; 
            end);
            task.wait(0.5);
            pcall(function(...)
                r28.AntiPaint.enabled = true;
                local SoundService = SoundService[1];
                for F, v in SoundService, pairs(SoundService:GetPlayers()) do
                    local Workspace = r25.Workspace;
                    if Workspace:FindFirstChild(v.Name .. "SpawnedInToys") then
                        local ref7 = v2.GetChildren[3];
                        for ref7, v16 in v16[1], Workspace(v16(ref7)) do
                            if S[Workspace].Name == "BucketPaint" or (S[Workspace].Name == "FoodHotSauce" or (S[Workspace].Name == "ToiletGold" or S[Workspace].Name == "ToiletWhite")) then
                                task.spawn(function(...)
                                    local SoundService = SoundService[1];
                                    for F, v in pairs(SoundService:GetChildren()) do
                                        if v.Name == "PaintPlayerPart" or v.Name == "FirePlayerPart" then
                                            v:Destroy();
                                        end; 
                                    end;
                                    return; 
                                end);
                            end; 
                        end;
                    end; 
                end;
                DescendantAdded = r25.Workspace.DescendantAdded;
                r28.AntiPaint.connection = DescendantAdded:Connect(function(arg1_10, ...)
                    if k.Name == "PaintPlayerPart" or k.Name == "FirePlayerPart" then
                        local Parent = k.Parent;
                        if Parent then
                            local Name = Parent.Name;
                            if Name == "BucketPaint" or (Name == "FoodHotSauce" or (Name == "ToiletGold" or Name == "ToiletWhite")) then
                                task.wait(.1);
                                if arg1_10 then
                                    local Parent = arg1_10.Parent;
                                end;
                                if arg1_10 then
                                    arg1_10:Destroy();
                                end;
                            end;
                        end;
                    end;
                    return; 
                end);
                print("[AMETHYST] ✅ Anti Paint активен");
                return; 
            end);
            task.wait(0.5);
            pcall(function(...)
                r28.AntiExplode.enabled = true;
                r28.AntiExplode.bombEvent = (function(...)
                    local ReplicatedStorage = r25.ReplicatedStorage;
                    ReplicatedStorage = r25.ReplicatedStorage;
                    ReplicatedStorage = r25.ReplicatedStorage;
                    ReplicatedStorage = r25.ReplicatedStorage.GameEvents;
                    ReplicatedStorage = r25.ReplicatedStorage.CharacterEvents;
                    ReplicatedStorage = r25.ReplicatedStorage.PlayerEvents;
                    for SoundService, ReplicatedStorage in ipairs({
                        ReplicatedStorage:FindFirstChild("BombEvent"),
                        ReplicatedStorage:FindFirstChild("ExplosionEvent"),
                        ReplicatedStorage:FindFirstChild("BombExplosion"),
                        "ipairs",
                        ReplicatedStorage:FindFirstChild("BombEvent"),
                        ReplicatedStorage:FindFirstChild("BombEvent")
                    }) do
                        if ReplicatedStorage then
                            ReplicatedStorage = ReplicatedStorage:IsA("RemoteEvent");
                        end;
                        if ReplicatedStorage then
                            return ReplicatedStorage;
                        else
                            
                        end; 
                    end;
                    return r69(r25.ReplicatedStorage); 
                end)();
                if r28.AntiExplode.bombEvent then
                    local OnClientEvent = r28.AntiExplode.bombEvent.OnClientEvent;
                    r28.AntiExplode.connection = OnClientEvent:Connect(function(arg1_12, arg2_12, ...)
                        if not r28.AntiExplode.enabled then
                            return;
                        end;
                        local Character = LocalPlayer.Character;
                        if not Character then
                            return;
                        end;
                        local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                        local humanoid = Character:FindFirstChild("Humanoid");
                        if not humanoidRootPart or not humanoid then
                            return;
                        end;
                        task.spawn(function(...)
                            if (r70.Radius or 20) + 10 > (arg2_12 - r73.Position).Magnitude then
                                r73.Anchored = true;
                                task.wait(.05);
                                local ref8 = r74;
                                ref8:ChangeState(Enum.HumanoidStateType.Running);
                                r73.Anchored = false;
                                ref8 = 1;
                                for T = ref8, 4 do
                                    ref8 = r72;
                                    ref8 = ref8:FindFirstChild(list[SoundService]);
                                    if ref8 then
                                        local ragdollLimbPart = ref8:FindFirstChild("RagdollLimbPart");
                                        if ragdollLimbPart then
                                            ragdollLimbPart.CanCollide = false;
                                        end;
                                    end; 
                                end;
                                r73.AssemblyLinearVelocity = Vector3.zero;
                                r73.AssemblyAngularVelocity = Vector3.zero;
                                r73.Velocity = Vector3.zero;
                                r73.RotVelocity = Vector3.zero;
                                task.delay(1.5, function(...)
                                    if r72 and r72.Parent then
                                        for k = 1, 4 do
                                            local ref9 = r72:FindFirstChild(list[k]);
                                            if ref9 then
                                                local ragdollLimbPart = ref9:FindFirstChild("RagdollLimbPart");
                                                if ragdollLimbPart then
                                                    ragdollLimbPart.CanCollide = true;
                                                end;
                                            end; 
                                        end;
                                    end;
                                    return; 
                                end);
                            end;
                            return; 
                        end);
                        return; 
                    end);
                    print("[AMETHYST] ✅ Anti Explode активен");
                end;
                return; 
            end);
            task.wait(0.5);
            pcall(function(...)
                r28.AntiInvisible.enabled = true;
                task.spawn(function(...)
                    while r28.AntiInvisible.enabled do
                        local SoundService = SoundService[1];
                        for F, v in ipairs(SoundService:GetPlayers()) do
                            local Character = v.Character;
                            if Character then
                                for enabled2, G in ipairs(G(Character)) do
                                    if G:IsA("BasePart") and G.Massless == true then
                                        G.Massless = false;
                                    end; 
                                end;
                            end;
                            Workspace = r25.Workspace;
                            Character = Workspace:FindFirstChild(v.Name .. "SpawnedInToys");
                            if Character then
                                for enabled2, G in Workspace(G(Character)) do
                                    if G:IsA("Model") then
                                        for character, i in Workspace(i(G)) do
                                            if i:IsA("BasePart") and i.Massless == true then
                                                i.Massless = false;
                                            end; 
                                        end;
                                    end; 
                                end;
                            end; 
                        end;
                        task.wait(3); 
                    end;
                    return; 
                end);
                print("[AMETHYST] ✅ Anti-Invisible активен");
                return; 
            end);
            task.wait(0.5);
            pcall(function(...)
                if r28.AntiBananaEat then
                    r28.AntiBananaEat.enabled = true;
                    r28.AntiBananaEat.processedBananas = r28.AntiBananaEat.processedBananas or {};
                    O.processedFolders = r28.AntiBananaEat.processedFolders or {};
                    print("[AMETHYST] ✅ Anti Banana Eat активен");
                end;
                return; 
            end);
            task.wait();
            pcall(function(...)
                if r28.AntiAntiInput then
                    r28.AntiAntiInput.enabled = true;
                    r28.AntiAntiInput.processedItems = r28.AntiAntiInput.processedItems or {};
                    r28.AntiAntiInput.processedFolders = r28.AntiAntiInput.processedFolders or {};
                    print("[AMETHYST] ✅ Anti Anti Input активен");
                end;
                return; 
            end);
            task.wait(0);
            return;
        end;
    end; 
end);
local function findFirstChildOfClass()
    local Character = LocalPlayer and LocalPlayer.Character;
    if not Character then
        return nil;
    end;
    return Character:FindFirstChildOfClass("Humanoid"); 
end;
local function v47(arg1_13)
    if not r28.Speed.enabled then
        return;
    end;
    local Character = LocalPlayer.Character;
    if not Character then
        return;
    end;
    local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
    local humanoid = Character:FindFirstChildOfClass("Humanoid");
    if not humanoidRootPart or not humanoid then
        return;
    end;
    humanoid.WalkSpeed = r28.Speed.value;
    local MoveDirection = humanoid.MoveDirection;
    if MoveDirection.Magnitude > 0 then
        local Unit = MoveDirection.Unit * r28.Speed.value * arg1_13 * .1;
        humanoidRootPart.CFrame = humanoidRootPart.CFrame + Vector3.new(v2.X, 0, v2.Z);
    end; 
end;
Window:CreateTab("Player"):CreateSection("Player");
for v54, v55 in ipairs(v49.houses), idk do
    table.insert(items, v55[1]); 
end;
r75:CreateToggle({
    ["Name"] = "Loop Teleport",
    ["Default"] = false,
    ["Callback"] = function(arg1_14, ...)
        if arg1_14 then
            Teleport.loop.enabled = true;
            Teleport.loop.loopThread = task.spawn(function()
                while Teleport.loop.enabled do
                    if LocalPlayer.Character then
                        local humanoidRootPart = k:FindFirstChild("HumanoidRootPart");
                        if "task" then
                            humanoidRootPart.CFrame = Teleport.loop.target;
                        end;
                        task.wait();
                    end; 
                end; 
            end);
            r47("Loop TP", "Enabled - Looping to selected location");
            r44();
        else
            Teleport.loop.enabled = false;
            if Teleport.loop.loopThread then
                task.cancel(Teleport.loop.loopThread);
                Teleport.loop.loopThread = nil;
            end;
            r47("Loop TP", "Disabled");
            return;
        end; 
    end
});
r75:CreateDropdown({
    ["Name"] = "Teleport Location",
    ["Items"] = items,
    ["Default"] = "Spawn",
    ["Callback"] = function(arg1_15)
        for SoundService, game in ipairs("ipairs") do
            if game[1] == arg1_15 then
                Teleport.loop.target = game[2];
                break;
            else
                
            end; 
        end; 
    end
});
r75:CreateButton({
    ["Name"] = "Teleport Once",
    ["Callback"] = function()
        local Character = LocalPlayer.Character;
        if Character then
            local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
            if humanoidRootPart then
                humanoidRootPart.CFrame = Teleport.loop.target;
                r47("Teleport", "Teleported to selected location");
                r44();
            end;
        end; 
    end
});
r75:CreateToggle({
    ["Name"] = "Teleport to Mouse (Z key)",
    ["Default"] = false,
    ["Callback"] = function(arg1_16, ...)
        if arg1_16 then
            local TPToolConnection = Selected.TPToolConnection;
            if TPToolConnection then
                Selected.TPToolConnection:Disconnect();
            end;
            local InputBegan = r25.UserInputService.InputBegan;
            Selected.TPToolConnection = InputBegan:Connect(function(arg1_17, arg2_17)
                if arg2_17 then
                    return;
                end;
                if arg1_17.KeyCode == Enum.KeyCode.Z then
                    local Character = LocalPlayer.Character;
                    if Character then
                        local o = LocalPlayer:GetMouse();
                        local TargetSurface = o.TargetSurface;
                        if o.Target then
                            Character.HumanoidRootPart.CFrame = CFrame.new(o.Hit.Position + Vector3.new(0, 3, 0));
                        else
                            solarisui:Notify({
                                ["Title"] = "Teleport Error",
                                ["Content"] = "No target object to teleport to",
                                ["Duration"] = 2
                            });
                        end;
                    end;
                end; 
            end);
            solarisui:Notify({
                ["Title"] = "Teleport Tool",
                ["Content"] = "Enabled - Press Z to teleport to mouse target",
                ["Duration"] = 3
            });
            r44();
        else
            if Selected.TPToolConnection then
                Selected.TPToolConnection:Disconnect();
                Selected.TPToolConnection = nil;
            end;
            solarisui:Notify({
                ["Title"] = "Teleport Tool",
                ["Content"] = "Disabled",
                ["Duration"] = 2
            });
            return;
        end; 
    end
});
r75:CreateSection("Speed player");
local function findFirstChildOfClass2()
    local Character = LocalPlayer and LocalPlayer.Character;
    if not Character then
        return nil;
    end;
    return Character:FindFirstChildOfClass("Humanoid"); 
end;
r75:CreateSlider({
    ["Name"] = "Speed Control",
    ["Min"] = 16,
    ["Max"] = 500,
    ["Default"] = 16,
    ["Callback"] = function(arg1_18)
        r28.Speed.value = arg1_18;
        if r28.Speed.enabled then
            arg1_18 = findFirstChildOfClass2();
            if arg1_18 then
                F.WalkSpeed = arg1_18;
            end;
        end; 
    end
});
local function findFirstChildOfClass3()
    local Character = LocalPlayer and LocalPlayer.Character;
    if not Character then
        return nil;
    end;
    return Character:FindFirstChildOfClass("Humanoid"); 
end;
local function r78(arg1_19)
    if not r28.Speed.enabled then
        return;
    end;
    local Character = LocalPlayer.Character;
    if not Character then
        return;
    end;
    local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
    local humanoid = Character:FindFirstChildOfClass("Humanoid");
    if not humanoidRootPart or not humanoid then
        return;
    end;
    humanoid.WalkSpeed = r28.Speed.value;
    local MoveDirection = humanoid.MoveDirection;
    if MoveDirection.Magnitude > 0 then
        local Unit = MoveDirection.Unit * r28.Speed.value * arg1_19;
        humanoidRootPart.CFrame = humanoidRootPart.CFrame + Vector3.new(v2.X, 0, v2.Z);
    end; 
end;
local function r79(...)
    if r28.Speed.enabled then
        return;
    end;
    local k = findFirstChildOfClass3();
    if k then
        r28.Speed.originalWalkSpeed = k.WalkSpeed;
    end;
    r28.Speed.enabled = true;
    if r28.Speed.connection then
        local connection = r28.Speed.connection;
        connection:Disconnect();
        r28.Speed.connection = nil;
    end;
    local Stepped = r25.RunService.Stepped;
    r28.Speed.connection = Stepped:Connect(function(arg1_20, arg2_20)
        r78(arg2_20); 
    end);
    local charConnection = r28.Speed.charConnection;
    if charConnection then
        charConnection = r28.Speed.charConnection;
        charConnection:Disconnect();
    end;
    r28.Speed.charConnection = LocalPlayer.CharacterAdded:Connect(function(Character)
        task.wait(0.5);
        if r28.Speed.enabled then
            r79();
        end; 
    end); 
end;
local function r80()
    r28.Speed.enabled = false;
    if r28.Speed.connection then
        local connection = r28.Speed.connection;
        connection:Disconnect();
        r28.Speed.connection = nil;
    end;
    if r28.Speed.charConnection then
        local charConnection = r28.Speed.charConnection;
        charConnection:Disconnect();
        r28.Speed.charConnection = nil;
    end;
    local k = findFirstChildOfClass3();
    if k then
        k.WalkSpeed = r28.Speed.originalWalkSpeed;
    end; 
end;
r75:CreateToggle({
    ["Name"] = "Enable Speed",
    ["Default"] = false,
    ["Callback"] = function(arg1_22)
        if arg1_22 then
            r79();
            r47("Speed", "Enabled - WalkSpeed: " .. r28.Speed.value);
            r44();
        else
            r80();
            r47("Speed", "Disabled");
        end; 
    end
});
r75:CreateSection("Visible player location");
r75:CreateToggle({
    ["Name"] = "PCLD ESP",
    ["Default"] = false,
    ["Callback"] = function()
        task.wait();
        local ref10 = solarisui;
        if r44.enabled then
            ref10 = r15.Players;
            for character2, v16 in ref10, ipairs(ref10:GetPlayers()) do
                if not not r44.enabled then
                    
                end; 
            end;
        end; 
    end
});
r75:CreateSection("Other");
r75:CreateToggle({
    ["Name"] = "Hold Network Yourself",
    ["Default"] = false,
    ["Callback"] = function(arg1_23, ...)
        if arg1_23 then
            local Config = {
                ["active"] = true,
                ["heartbeatConnection"] = nil,
                ["characterConnection"] = nil
            };
            local Heartbeat = game:GetService("RunService").Heartbeat;
            Config.heartbeatConnection = Heartbeat:Connect(function()
                local Character = game.Players.LocalPlayer.Character;
                if Character then
                    local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                    if humanoidRootPart then
                        humanoidRootPart = game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner;
                        humanoidRootPart:FireServer(humanoidRootPart, humanoidRootPart.CFrame);
                    end;
                end; 
            end);
            Heartbeat = game.Players.LocalPlayer.CharacterAdded;
            Config.characterConnection = Heartbeat:Connect(function(...)
                local heartbeatConnection = Config.heartbeatConnection;
                if heartbeatConnection then
                    Config.heartbeatConnection:Disconnect();
                end;
                task.wait();
                local k = "active";
                if Config[k] then
                    local Heartbeat = game:GetService("RunService").Heartbeat;
                    Config.heartbeatConnection = Heartbeat:Connect(function()
                        local Character = game.Players.LocalPlayer.Character;
                        if Character then
                            local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                            if humanoidRootPart then
                                humanoidRootPart = game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner;
                                humanoidRootPart:FireServer(humanoidRootPart, humanoidRootPart.CFrame);
                            end;
                        end; 
                    end);
                end; 
            end);
            r75.NetworkData = Config;
            r47("Hold Network Yourself", "Enabled", 2);
        else
            if r75.NetworkData then
                r75.NetworkData.active = false;
                if r75.NetworkData.heartbeatConnection then
                    local heartbeatConnection = r75.NetworkData.heartbeatConnection;
                    heartbeatConnection:Disconnect();
                    r75.NetworkData.heartbeatConnection = nil;
                end;
                if r75.NetworkData.characterConnection then
                    local characterConnection = r75.NetworkData.characterConnection;
                    characterConnection:Disconnect();
                    r75.NetworkData.characterConnection = nil;
                end;
                r75.NetworkData = nil;
            end;
            r47("Hold Network Yourself", "Disabled", 2);
            return;
        end; 
    end
});
Window:CreateTab("Defense"):CreateSection("Break PCLD");
r28.AutoFaultLocation = {
    ["enabled"] = false,
    ["isFaulted"] = false
};
local AutoFaultLocation = r28.AutoFaultLocation;
local function r83()
    local SoundService = "Character";
    local F = LocalPlayer[SoundService];
    if F then
        local F = k.CharacterAdded:Wait();
        local humanoid = F:WaitForChild("Humanoid");
        humanoid:ChangeState(Enum.HumanoidStateType.Dead);
        F = k.CharacterAdded:Wait();
        humanoid = F:WaitForChild("Humanoid");
        humanoid:ChangeState(Enum.HumanoidStateType.Dead);
        AutoFaultLocation.isFaulted = true;
        r47("Fault Location", "PCLD faulted successfully", 3);
        r44();
        return true;
    else
        
    end; 
end;
v68:CreateToggle({
    ["Name"] = "Auto Fault Location",
    ["Default"] = false,
    ["Callback"] = function(arg1_24, ...)
        if arg1_24 then
            AutoFaultLocation.enabled = true;
            if not AutoFaultLocation.isFaulted then
                task.spawn(function(...)
                    r83();
                    return; 
                end);
            end;
            local Character = LocalPlayer.Character;
            if Character then
                getRootPart(Character);
            end;
            LocalPlayer.CharacterAdded:Connect(function(Character, ...)
                if AutoFaultLocation.enabled then
                    getRootPart(Character);
                end;
                return; 
            end);
            r47("Auto Fault Location", "Enabled - PCLD will auto-rebreak on death", 3);
            r44();
        else
            AutoFaultLocation.enabled = false;
            AutoFaultLocation.isFaulted = false;
            r47("Auto Fault Location", "Disabled", 2);
        end; 
    end
});
v68:CreateButton({
    ["Name"] = "Fault My Location",
    ["Callback"] = function()
        local Character = k.Character;
        if Character then
            local humanoid = Character:WaitForChild("Humanoid");
            humanoid:ChangeState(Enum.HumanoidStateType.Dead);
            local Character = humanoid.CharacterAdded:Wait();
            humanoid = Character:WaitForChild("Humanoid");
            humanoid:ChangeState(Enum.HumanoidStateType.Dead);
            r47("Fault Location", "Location faulted successfully", 3);
            r44();
            return;
        else
            
        end; 
    end
});
v68:CreateSection("Gucci");
v68:CreateToggle({
    ["Name"] = "Auto Gucci Tractor",
    ["Default"] = false,
    ["Callback"] = function(arg1_27, ...)
        if arg1_27 then
            r28.AutoGucciTractor = r28.AutoGucciTractor or {};
            r28.AutoGucciTractor.enabled = true;
            local function r85(...)
                local r86 = {
                    [1] = "TractorGreen",
                    [2] = CFrame.new(0, 500000, 0),
                    [3] = Vector3.new(0, 60, 0)
                };
                pcall(function(...)
                    local SpawnToyRemoteFunction = r25.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction;
                    SpawnToyRemoteFunction:InvokeServer(unpack(r86));
                    return; 
                end);
                local SoundService = 5;
                local F = r25.Workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys", SoundService);
                if F then
                    local TractorGreen = F.TractorGreen;
                    if TractorGreen:FindFirstChild("VehicleSeat") then
                        local VehicleSeat = TractorGreen.VehicleSeat;
                        VehicleSeat.CFrame = CFrame.new(0, 50000, 0);
                        VehicleSeat.Anchored = true;
                    end;
                    return TractorGreen;
                else
                    return nil;
                end; 
            end;
            local function r87(...)
                local Character = LocalPlayer.Character;
                local O = not Character;
                local humanoid = Character:WaitForChild("Humanoid");
                local humanoidRootPart = Character:WaitForChild("HumanoidRootPart");
                r28.AutoGucciTractor.safePosition = humanoidRootPart.Position;
                local v = r25.Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys");
                local ref11 = v and {};
                if v then
                    ref11 = v:FindFirstChild("VehicleSeat");
                end;
                if not ref11 or not ref11:IsA("VehicleSeat") then
                    return false;
                end;
                humanoidRootPart.CFrame = v2.CFrame + Vector3.new(0, 2, 0);
                ref11:Sit(humanoid);
                ref11 = humanoid:GetPropertyChangedSignal("Jump");
                ref11:Connect(function(...)
                    if r88.Jump and r88.Sit then
                        r28.AutoGucciTractor.restoreFrames = 15;
                        r28.AutoGucciTractor.safePosition = r89.Position;
                    end;
                    return; 
                end);
                connection = r28.AutoGucciTractor.connection;
                if connection then
                    connection = r28.AutoGucciTractor.connection;
                    connection:Disconnect();
                end;
                local Heartbeat = r25.RunService.Heartbeat;
                r28.AutoGucciTractor.connection = Heartbeat:Connect(function()
                    if not r89 or not r88 then
                        return;
                    end;
                    local RagdollRemote = r25.ReplicatedStorage.CharacterEvents.RagdollRemote;
                    RagdollRemote:FireServer(r89, 0);
                    if r28.AutoGucciTractor.restoreFrames > 0 then
                        r89.CFrame = CFrame.new(r28.AutoGucciTractor.safePosition);
                        r28.AutoGucciTractor.restoreFrames = r28.AutoGucciTractor.restoreFrames - 1;
                    end; 
                end);
                task.spawn(function(...)
                    while r88.Sit do
                        task.wait(); 
                    end;
                    task.wait(0.5);
                    if r89 then
                        r89.CFrame = CFrame.new(r28.AutoGucciTractor.safePosition);
                    end;
                    return; 
                end);
                return true; 
            end;
            local function r90()
                local F = r25.Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys") and {};
                if F then
                    task.wait(.1);
                    local vehicleSeat = F:FindFirstChild("VehicleSeat");
                    if vehicleSeat then
                        if not r28.AutoGucciTractor.enabled then
                            return;
                        end;
                        local o = r87();
                        if o then
                            print("Auto Gucci Tractor: Tractor found, Gucci activated instantly");
                        end;
                    end;
                end; 
            end;
            r28.AutoGucciTractor.monitoringThread = task.spawn(function()
                while r28.AutoGucciTractor.enabled do
                    local Workspace = r25.Workspace;
                    local idk = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys") and {};
                    if not idk then
                        if r28.AutoGucciTractor.connection then
                            Workspace = r28.AutoGucciTractor.connection;
                            Workspace:Disconnect();
                            r28.AutoGucciTractor.connection = nil;
                        end;
                        r85();
                        if idk then
                            task.wait();
                            Workspace = SoundService + 1;
                            local Name = LocalPlayer.Name;
                            local k = r25.Workspace:FindFirstChild(Name .. "SpawnedInToys");
                            while not k do
                                Name = Workspace;
                                if k then
                                    if k then
                                        Name = r28.AutoGucciTractor.enabled;
                                        if Name then
                                            Name = k;
                                        end;
                                        Name = Name;
                                        if Name then
                                            r90();
                                        end;
                                        task.wait(.2);
                                    end;
                                else
                                    
                                end; 
                            end;
                            k:FindFirstChild("TractorGreen");
                        else
                            
                        end;
                    end; 
                end; 
            end);
            characterAddedConnection = r28.AutoGucciTractor.characterAddedConnection;
            if characterAddedConnection then
                characterAddedConnection = r28.AutoGucciTractor.characterAddedConnection;
                characterAddedConnection:Disconnect();
            end;
            local CharacterAdded = LocalPlayer.CharacterAdded;
            r28.AutoGucciTractor.characterAddedConnection = CharacterAdded:Connect(v);
            if LocalPlayer.Character then
                v(LocalPlayer.Character);
            end;
            r47("Auto Gucci Tractor", "Enabled - Tractor protection active");
            r44();
        else
            if r28.AutoGucciTractor then
                r28.AutoGucciTractor.enabled = false;
                if r28.AutoGucciTractor.connection then
                    local connection = r28.AutoGucciTractor.connection;
                    connection:Disconnect();
                    r28.AutoGucciTractor.connection = nil;
                end;
                if r28.AutoGucciTractor.characterAddedConnection then
                    local characterAddedConnection = r28.AutoGucciTractor.characterAddedConnection;
                    characterAddedConnection:Disconnect();
                    r28.AutoGucciTractor.characterAddedConnection = nil;
                end;
                if r28.AutoGucciTractor.monitoringThread then
                    task.cancel(r28.AutoGucciTractor.monitoringThread);
                    r28.AutoGucciTractor.monitoringThread = nil;
                end;
                local F = r25.Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys");
                if F then
                    F.TractorGreen:Destroy();
                end;
                r28.AutoGucciTractor.safePosition = nil;
                r28.AutoGucciTractor.restoreFrames = 0;
            end;
            r47("Auto Gucci Tractor", "Disabled");
            return;
        end; 
    end
});
v68:CreateButton({
    ["Name"] = "Gucci",
    ["Callback"] = function(...)
        pcall(function(...)
            local Character = k.Character;
            if not Character then
                return false;
            end;
            local SoundService = r25.Workspace:FindFirstChild(k.Name .. "SpawnedInToys");
            if not SoundService then
                return false;
            end;
            local RagdollRemote = r25.ReplicatedStorage.CharacterEvents.RagdollRemote;
            local SpawnToyRemoteFunction = r25.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction;
            local O = not k.CanSpawnToy.Value;
            if O then
                local Changed = k.CanSpawnToy.Changed;
                Changed:Wait();
            end;
            task.spawn(function(...)
                r93:InvokeServer("CreatureBlobman", r91.CFrame * CFrame.new(5, 5, 20), Vector3.new(0, 0, 0));
                return; 
            end);
            task.wait(.3);
            local GetChildren = SoundService.GetChildren;
            local ref12 = {
                GetChildren(SoundService)
            };
            ref12 = GetChildren[2];
            for character2, v16 in pairs(N("pairs")) do
                if v16.Name == "CreatureBlobman" and (v16.PrimaryPart and (v16.PrimaryPart.Position - r91.Position).Magnitude < 30) then
                    ref12 = v16;
                end; 
            end;
            if not nil then
                for H = 1, 5 do
                    ref12 = r25.Workspace.Plots;
                    if ref12:FindFirstChild("Plot" .. ref12) then
                        ref12 = ref12:FindFirstChild(character).PlotSign.ThisPlotsOwners;
                        value = ref12:FindFirstChild("Value");
                        ref12 = 10;
                        if value then
                            ref12 = value.Value:find(LocalPlayer.Name);
                        end;
                        if ref12 then
                            ref12:FindFirstChild(value);
                        else
                            
                        end;
                        if nil then
                            GetChildren = r25.Workspace.PlotItems[nil.Name];
                            GetChildren:WaitForChild("CreatureBlobman", 0.5);
                            ref12 = pairs;
                            GetChildren = r25.Workspace.PlotItems[nil.Name];
                            ref12 = GetChildren:FindFirstChild("CreatureBlobman") or {};
                        end;
                        if not nil then
                            return false;
                        end;
                        ref12 = nil.WaitForChild(nil, "VehicleSeat", 3);
                        if not ref12 then
                            return false;
                        end;
                        ref12:Sit(k.Character.Humanoid);
                        task.spawn(function(...)
                            while tick() < tick() + 3 do
                                local ref13 = r92;
                                ref13:FireServer(F, 0);
                                task.wait(); 
                            end;
                            return; 
                        end);
                        task.wait();
                        local Occupant = v5.Occupant ~= k.Character.Humanoid;
                        while not Occupant do
                            local ref14 = pairs;
                            if Occupant then
                                task.wait();
                            end;
                            if v5.Occupant ~= k.Character.Humanoid then
                                return false;
                            end;
                            ref14 = k.Character.Humanoid;
                            ref14:ChangeState(Enum.HumanoidStateType.Jumping);
                            task.wait(.2);
                            v5.CFrame = CFrame.new(0, 1000000000, 0);
                            return true; 
                        end;
                    end; 
                end;
                if nil then
                    ref12 = pairs;
                    GetChildren = r25.Workspace.PlotItems[nil.Name];
                    if 1 then
                        ref12 = GetChildren:FindFirstChild("CreatureBlobman") or {};
                        if not nil then
                            return false;
                        end;
                        ref12 = nil.WaitForChild(nil, "VehicleSeat", 3);
                        if not ref12 then
                            return false;
                        end;
                        ref12:Sit(k.Character.Humanoid);
                        task.spawn(function(...)
                            while tick() < tick() + 3 do
                                local ref13 = r92;
                                ref13:FireServer(F, 0);
                                task.wait(); 
                            end;
                            return; 
                        end);
                        task.wait();
                        local Occupant = v5.Occupant ~= k.Character.Humanoid;
                        while not Occupant do
                            local ref14 = pairs;
                            if Occupant then
                                task.wait();
                            end;
                            if v5.Occupant ~= k.Character.Humanoid then
                                return false;
                            end;
                            ref14 = k.Character.Humanoid;
                            ref14:ChangeState(Enum.HumanoidStateType.Jumping);
                            task.wait(.2);
                            v5.CFrame = CFrame.new(0, 1000000000, 0);
                            return true; 
                        end;
                    else
                        GetChildren = r25.Workspace.PlotItems[nil.Name];
                        GetChildren:WaitForChild("CreatureBlobman", 0.5);
                    end;
                end;
            end; 
        end);
        r47("Gucci", "Activated Gucci protection");
        r44(); 
    end
});
v68:CreateButton({
    ["Name"] = "Gucci Train",
    ["Callback"] = function(...)
        pcall(function(...)
            local ref15 = "Character";
            local k = LocalPlayer[idk[ref15]];
            if k then
                local humanoidRootPart = k:WaitForChild("HumanoidRootPart");
                local AlwaysHereTweenedObjects = r25.Workspace.Map.AlwaysHereTweenedObjects;
                if not AlwaysHereTweenedObjects then
                    return false;
                end;
                ref15 = AlwaysHereTweenedObjects:FindFirstChild("Train");
                if not ref15 then
                    return false;
                end;
                local GetDescendants = v2.GetDescendants;
                for ref15, G in ipairs(N("ipairs")) do
                    local O = G:IsA("Seat");
                    if O then
                        ref15 = G;
                    else
                        
                    end;
                    if not nil then
                        return false;
                    end;
                    humanoidRootPart.CFrame = nil.CFrame + Vector3.new(0, 2, 0);
                    nil.Sit(nil, k:WaitForChild("Humanoid"));
                    task.spawn(function(...)
                        while tick() < tick() + 1 do
                            local RagdollRemote = r25.ReplicatedStorage.CharacterEvents.RagdollRemote;
                            RagdollRemote:FireServer(r94, 0);
                            task.wait(); 
                        end;
                        return; 
                    end);
                    task.wait(0.5);
                    if k then
                        G:IsA("Seat");
                    end;
                    if k then
                        if nil.Occupant == k:WaitForChild("Humanoid") then
                            k:WaitForChild("Humanoid").Sit = false;
                            break;
                        end;
                        humanoidRootPart.CFrame = S[SoundService].CFrame;
                        return true;
                    end;
                    return false; 
                end;
            else
                
            end; 
        end);
        r47("Gucci Train", "Activated train protection");
        r44(); 
    end
});
v68:CreateButton({
    ["Name"] = "Gucci Tractor",
    ["Callback"] = function(...)
        task.spawn(function(...)
            local Character = r95.Character;
            if not Character then
                task.wait(0.5);
                local Character = r95.Character;
                if Character then
                    if not r95.Character then
                        return;
                    end;
                    local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                    if not humanoidRootPart or not Character:FindFirstChild("Humanoid") then
                        return;
                    end;
                    local r97 = r25.Workspace:FindFirstChild(r95.Name .. "SpawnedInToys");
                    if not r97 then
                        return;
                    end;
                    local function r98(...)
                        for k = 1, 5 do
                            local Plots = r25.Workspace.Plots;
                            local ref16 = "Plot";
                            ref16 = Plots:FindFirstChild(ref16 .. k);
                            if ref16 then
                                Plots = v2.PlotSign.ThisPlotsOwners;
                                ref16 = Plots:FindFirstChild("Value");
                                if ref16 then
                                    return list3;
                                else
                                    
                                end;
                            end; 
                        end;
                        return nil; 
                    end;
                    local tractorGreen = r97:FindFirstChild("TractorGreen");
                    Character = not tractorGreen;
                    if Character then
                        tractorGreen = (function(tractorGreen2, arg2_29, ...)
                            local r100 = arg2_29;
                            local O = not r95.CanSpawnToy.Value;
                            if O then
                                local Changed = r95.CanSpawnToy.Changed;
                                Changed:Wait();
                            end;
                            local SpawnToyRemoteFunction = r25.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction;
                            task.spawn(function(...)
                                r101:InvokeServer(tractorGreen2, r100, Vector3.new(0, 0, 0));
                                return; 
                            end);
                            task.wait(.3);
                            local ref17 = r97;
                            local character2 = {
                                ref17:GetChildren()
                            };
                            ref17 = ref17[3];
                            ref17 = "pairs";
                            for ref17, ref17 in pairs(N(character2)) do
                                 
                            end;
                            if nil then
                                return nil;
                            end;
                            ref17 = r98();
                            if ref17 then
                                local character2 = r25.Workspace.PlotItems[v2.Name];
                                character2:WaitForChild(tractorGreen2, 0.5);
                                character2 = r25.Workspace.PlotItems[v2.Name];
                                return character2:FindFirstChild(tractorGreen2) or {};
                            end;
                            return; 
                        end)("TractorGreen", humanoidRootPart.CFrame * CFrame.new(5, 10, 20));
                    end;
                    if not tractorGreen then
                        return;
                    end;
                    if not true then
                        tractorGreen = not tractorGreen;
                        local y = not r97:FindFirstChild("TractorGreen");
                        local v16 = tick() - tick() < 5;
                        while not y do
                            if y then
                                task.wait(.1);
                            end;
                            local vehicleSeat = tractorGreen:WaitForChild("VehicleSeat", 3);
                            if not vehicleSeat then
                                return;
                            end;
                            vehicleSeat:Sit(o);
                            local RagdollRemote = r25.ReplicatedStorage.CharacterEvents.RagdollRemote;
                            task.spawn(function(...)
                                while tick() < tick() + 3 do
                                    r102:FireServer(r96, 0);
                                    task.wait(); 
                                end;
                                return; 
                            end);
                            task.wait(.1);
                            while vehicleSeat.Occupant ~= o do
                                task.wait(.05); 
                            end;
                            o:ChangeState(Enum.HumanoidStateType.Jumping);
                            task.wait(.1);
                            while vehicleSeat.Occupant == o do
                                task.wait(.05); 
                            end;
                            vehicleSeat.CFrame = CFrame.new(0, 1000000000, 0);
                            task.wait(1);
                            return; 
                        end;
                    end;
                end;
            end; 
        end);
        r47("Gucci Tractor", "Activated tractor protection");
        r44(); 
    end
});
v68:CreateButton({
    ["Name"] = "Santa Sleigh Gucci",
    ["Callback"] = function(...)
        task.spawn(function(...)
            local r103 = LocalPlayer;
            local Character = r103.Character;
            if not Character then
                local Character = r103.Character;
                task.wait(0.5);
                if r103.Character then
                    local ref18 = 22663328655673;
                    if not r103.Character then
                        return;
                    end;
                    local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                    Character = Character:FindFirstChild("Humanoid");
                    local r15 = not humanoidRootPart;
                    if r15 then
                        if r15 then
                            return;
                        end;
                        local r105 = r25.Workspace:FindFirstChild(r103.Name .. "SpawnedInToys");
                        if not r105 then
                            return;
                        end;
                        22663328655673 --[[ref18]] = r105:FindFirstChild("SantaSleigh");
                        local function r106(...)
                            for k = 1, 5 do
                                local Plots = r25.Workspace.Plots;
                                local ref19 = "Plot";
                                ref19 = Plots:FindFirstChild(ref19 .. k);
                                if ref19 then
                                    Plots = v2.PlotSign.ThisPlotsOwners;
                                    ref19 = Plots:FindFirstChild("Value");
                                    if ref19 then
                                        return r15;
                                    else
                                        
                                    end;
                                end; 
                            end;
                            return nil; 
                        end;
                        Character = not 22663328655673 --[[ref18]];
                        if Character then
                            22663328655673 --[[ref18]] = (function(santaSleigh, arg2_30, ...)
                                local r107 = santaSleigh;
                                local r108 = arg2_30;
                                local O = not r103.CanSpawnToy.Value;
                                if O then
                                    local Changed = r103.CanSpawnToy.Changed;
                                    Changed:Wait();
                                end;
                                local SpawnToyRemoteFunction = r25.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction;
                                task.spawn(function(...)
                                    r109:InvokeServer(r107, r108, Vector3.new(0, 0, 0));
                                    return; 
                                end);
                                task.wait(.3);
                                local ref20 = r105;
                                local character2 = {
                                    ref20:GetChildren()
                                };
                                ref20 = ref20[3];
                                ref20 = ref20[1];
                                for ref20, ref20 in ref20, pairs(N(character2)) do
                                     
                                end;
                                if nil then
                                    return nil;
                                end;
                                ref20 = r106();
                                if ref20 then
                                    local character2 = r25.Workspace.PlotItems[v2.Name];
                                    character2:WaitForChild(r107, 0.5);
                                    character2 = r25.Workspace.PlotItems[v2.Name];
                                    return character2:FindFirstChild(r107) or {};
                                end;
                                return; 
                            end)("SantaSleigh", humanoidRootPart.CFrame * CFrame.new(5, 10, 20));
                        end;
                        if not 22663328655673 --[[ref18]] then
                            return;
                        end;
                        if not true then
                            22663328655673 --[[ref18]] = not 22663328655673 --[[ref18]];
                            local y = not r105:FindFirstChild("SantaSleigh");
                            local v16 = tick() - tick() < 5;
                            while not y do
                                if y then
                                    task.wait(.1);
                                end;
                                local vehicleSeat = 22663328655673 --[[ref18]]:WaitForChild("VehicleSeat", 3);
                                if not vehicleSeat then
                                    return;
                                end;
                                vehicleSeat:Sit(Character);
                                local RagdollRemote = r25.ReplicatedStorage.CharacterEvents.RagdollRemote;
                                task.spawn(function(...)
                                    while tick() < tick() + 3 do
                                        r110:FireServer(r104, 0);
                                        task.wait(); 
                                    end;
                                    return; 
                                end);
                                task.wait(.1);
                                while vehicleSeat.Occupant ~= Character do
                                    task.wait(.05); 
                                end;
                                Character:ChangeState(Enum.HumanoidStateType.Jumping);
                                task.wait(.1);
                                while vehicleSeat.Occupant == Character do
                                    task.wait(.05); 
                                end;
                                vehicleSeat.CFrame = CFrame.new(0, 1000000000, 0);
                                task.wait(1);
                                return; 
                            end;
                        end;
                    else
                        
                    end;
                end;
            end; 
        end);
        r47("Santa Sleigh", "Activated sleigh protection");
        r44(); 
    end
});
v68:CreateSection("Anti-Function");
v68:CreateToggle({
    ["Name"] = "Anti Grab",
    ["Default"] = false,
    ["Callback"] = function(arg1_31, ...)
        if arg1_31 then
            r28.AntiGrab.enabled = true;
            r28.AntiGrab.antiGrabEnabled = true;
            local r111 = {
                ["remotes"] = {},
                ["parts"] = {},
                ["conns"] = {}
            };
            local function r113()
                if not r111.parts.IsHeld or not r111.parts.IsHeld.Value then
                    return;
                end;
                if not r111.parts.Root or not r111.parts.Humanoid then
                    return;
                end;
                r111.parts.Root.Anchored = true;
                local SetNetworkOwner = r25.ReplicatedStorage.GrabEvents.SetNetworkOwner;
                SetNetworkOwner:FireServer(r111.parts.Root, r111.parts.Root.CFrame);
                r111.parts.Root.Massless = true;
                r111.parts.Root.CanCollide = false;
                r111.parts.Root.CanQuery = false;
                r111.parts.Root.AssemblyLinearVelocity = Vector3.zero;
                r111.parts.Root.AssemblyAngularVelocity = Vector3.zero;
                r111.parts.Root.Velocity = Vector3.zero;
                r111.parts.Root.RotVelocity = Vector3.zero;
                local struggle = r112("Struggle");
                local ragdollRemote = r112("RagdollRemote");
                if struggle then
                    struggle:FireServer();
                end;
                if ragdollRemote then
                    ragdollRemote:FireServer(r111.parts.Root, 0);
                end;
                if r111.parts.originalGrabPosition then
                    r111.parts.Root.CFrame = r111.parts.originalGrabPosition;
                end;
                SetNetworkOwner = r111.parts.Humanoid;
                SetNetworkOwner:ChangeState(Enum.HumanoidStateType.Physics);
                r111.parts.Humanoid.Sit = false;
                r111.parts.Humanoid.PlatformStand = false;
                if r111.parts.Humanoid.MoveDirection.Magnitude > 0 then
                    r111.parts.Root.CFrame = r111.parts.Root.CFrame + r111.parts.Humanoid.MoveDirection * 2;
                end;
                if not r111.parts.grabStartTime then
                    r111.parts.grabStartTime = os.clock();
                end;
                local grabStartTime = os.clock() - r111.parts.grabStartTime;
                if grabStartTime > 0.5 then
                    if r111.parts.Humanoid.MoveDirection.Magnitude > 0 then
                        r111.parts.Root.CFrame = r111.parts.Root.CFrame + r111.parts.Humanoid.MoveDirection * math.min(grabStartTime * 0.5, 3);
                    end;
                end; 
            end;
            local function v()
                if not r111.parts.IsHeld then
                    return;
                end;
                if r111.parts.IsHeld.Value then
                    r111.parts.grabStartTime = os.clock();
                    if r111.parts.Root then
                        r111.parts.originalGrabPosition = r111.parts.Root.CFrame;
                        r111.parts.Root.Anchored = true;
                    end;
                    local conns = r111.conns;
                    if not conns.heartbeat then
                        conns = r25.RunService.Heartbeat;
                        r111.conns.heartbeat = conns:Connect(r113);
                    end;
                else
                    if r111.conns.heartbeat then
                        local heartbeat = r111.conns.heartbeat;
                        heartbeat:Disconnect();
                        r111.conns.heartbeat = nil;
                    end;
                    if r111.parts.Root then
                        r111.parts.Root.Anchored = false;
                        r111.parts.Root.Massless = false;
                        r111.parts.Root.CanCollide = true;
                        r111.parts.Root.CanQuery = true;
                        r111.parts.Root.AssemblyLinearVelocity = Vector3.zero;
                        r111.parts.Root.AssemblyAngularVelocity = Vector3.zero;
                        r111.parts.Root.Velocity = Vector3.zero;
                        r111.parts.Root.RotVelocity = Vector3.zero;
                    end;
                    local Humanoid = r111.parts.Humanoid;
                    if Humanoid then
                        local Humanoid = r111.parts.Humanoid;
                        Humanoid:ChangeState(Enum.HumanoidStateType.Running);
                    end;
                    r111.parts.grabStartTime = nil;
                    r111.parts.originalGrabPosition = nil;
                    return;
                end; 
            end;
            local ref21 = LocalPlayer;
            r111.parts.IsHeld = ref21:WaitForChild("IsHeld");
            if LocalPlayer.Character then
                ref21(LocalPlayer.Character);
            end;
            ref21 = LocalPlayer.CharacterAdded;
            r111.conns.charAdded = ref21:Connect(ref21);
            ref21 = r111.parts.IsHeld;
            local character2 = ref21:GetPropertyChangedSignal("Value");
            r111.conns.heldChanged = character2:Connect(v);
            v();
            r47("Anti Grab", "Enabled - Grab protection active");
            r44();
        else
            r28.AntiGrab.enabled = false;
            r28.AntiGrab.antiGrabEnabled = false;
            r47("Anti Grab", "Disabled");
        end; 
    end
});
r28.AntiKickStick = {
    ["enabled"] = false,
    ["loopThread"] = nil,
    ["selectedItem"] = "NinjaShuriken",
    ["items"] = {
        "NinjaShuriken",
        "NinjaKunai",
        "ToolCleaver"
    }
};
local AntiKickStick2 = r28.AntiKickStick;
local function r116()
    local F = r25.Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys");
    local ref22 = r15;
    local menuToys = r25.ReplicatedStorage:FindFirstChild("MenuToys");
    local SoundService = menuToys and {};
    if F then
        ref22 = F.GetChildren;
        ref22 = {
            ref22(F)
        };
        for v, character2 in pairs(N(menuToys)) do
            ref22 = v;
            if character2.Name == "AntiKick" or (character2.Name == "NinjaShuriken" or (character2.Name == "NinjaKunai" or character2.Name == "ToolCleaver")) then
                SoundService:FireServer(character2);
            end; 
        end;
    end; 
end;
v68:CreateDropdown({
    ["Name"] = "Anti Kick (Stick) Item",
    ["Items"] = AntiKickStick2.items,
    ["Default"] = "NinjaShuriken",
    ["Callback"] = function(arg1_36, ...)
        AntiKickStick2.selectedItem = arg1_36;
        r47("Anti Kick", "Selected: " .. arg1_36, 2);
        if AntiKickStick2.enabled then
            if AntiKickStick2.loopThread then
                task.cancel(AntiKickStick2.loopThread);
                AntiKickStick2.loopThread = nil;
            end;
            r116();
            task.wait(.1);
            AntiKickStick2.loopThread = task.spawn(function(...)
                local r122 = LocalPlayer;
                local grabEvents = r25.ReplicatedStorage:WaitForChild("GrabEvents");
                grabEvents:WaitForChild("SetNetworkOwner");
                grabEvents = r25.ReplicatedStorage:WaitForChild("PlayerEvents");
                grabEvents:WaitForChild("StickyPartEvent");
                local SpawnToyRemoteFunction = r25.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction;
                grabEvents = r25.ReplicatedStorage:WaitForChild("MenuToys");
                grabEvents:WaitForChild("DestroyToy");
                local canSpawnToy = r122:WaitForChild("CanSpawnToy");
                local function r125()
                    arg1_36 = r122.Character;
                    if arg1_36 then
                        arg1_36 = r122.Character;
                    end;
                    if arg1_36 then
                        return r122.Character.HumanoidRootPart;
                    end;
                    CharacterAdded = r122.CharacterAdded;
                    arg1_36 = CharacterAdded:Wait();
                    return arg1_36:WaitForChild("HumanoidRootPart"); 
                end;
                local function r126()
                    local PlayersInPlots = r25.Workspace.PlotItems.PlayersInPlots;
                    if not PlayersInPlots:FindFirstChild(r122.Name) then
                        return false;
                    end;
                    local Plots = r25.Workspace.Plots;
                    arg1_36 = Plots[3];
                    Plots = Plots[1];
                    for arg1_36, v in Plots, pairs(Plots:GetChildren()) do
                        local plotSign = v:FindFirstChild("PlotSign");
                        if plotSign then
                            plotSign = plotSign:FindFirstChild("ThisPlotsOwners");
                        end;
                        if plotSign then
                            for enabled2, G in G[1], pairs(G(plotSign)) do
                                plotSign = G.Value;
                                local Name = plotSign == r122.Name;
                                if Name then
                                    Name = r25.Workspace.PlotItems;
                                    plotSign = Name:FindFirstChild(v.Name);
                                    if plotSign then
                                        return true, plotSign;
                                    else
                                        
                                    end;
                                end; 
                            end;
                        end; 
                    end;
                    return false; 
                end;
                local function game(...)
                    local selectedItem = AntiKickStick2.selectedItem;
                    while not r124.Value do
                        if not AntiKickStick2.enabled or tick() - tick() > 5 then
                            return nil;
                        else
                            task.wait();
                        end; 
                    end;
                    local SoundService = r125();
                    if SoundService then
                        local CFrame_ = SoundService.CFrame * CFrame.new(0, 8, 17);
                        task.spawn(function(...)
                            local r122 = r123;
                            r122:InvokeServer(r127, r128, Vector3.new(0, 0, 0));
                            return; 
                        end);
                    end;
                    local v = {
                        r126()
                    };
                    local o = v[2];
                    v = r126();
                    Workspace = r25.Workspace;
                    local ref23 = Workspace:FindFirstChild(Workspace.Name .. "SpawnedInToys");
                    if v then
                        return o:WaitForChild(selectedItem, 2);
                    end;
                    local PlayersInPlots = r25.Workspace.PlotItems.PlayersInPlots;
                    ref23 = not PlayersInPlots:FindFirstChild(Workspace.Name);
                    if ref23 then
                        return ref23:WaitForChild(selectedItem, 2);
                    end;
                    return nil; 
                end;
                local character = 2490501039239;
                enabled2 = "enabled";
                local O = AntiKickStick2[enabled2];
                while O do
                    task.wait();
                    local ref24 = "Character";
                    local enabled2 = r25.Workspace:FindFirstChild(r122.Name .. "SpawnedInToys");
                    local PlayersInPlots = r25.Workspace.PlotItems.PlayersInPlots;
                    if PlayersInPlots:FindFirstChild(r122.Name) then
                        local y = {
                            r126()
                        };
                        ref24 = y[2];
                        y = r126();
                        if y then
                            if ref24 then
                                
                            end;
                            local r126 = S[character2];
                        end;
                        if y then
                            local r126 = r25.Workspace.Plots[v7.Name];
                            local plotSign = r126:FindFirstChild("PlotSign");
                            if plotSign then
                                local G = (function(...)
                                    local selectedItem = AntiKickStick2.selectedItem;
                                    while not r124.Value do
                                        if not AntiKickStick2.enabled or tick() - tick() > 5 then
                                            return nil;
                                        else
                                            task.wait();
                                        end; 
                                    end;
                                    local SoundService = r125();
                                    if SoundService then
                                        local CFrame_ = SoundService.CFrame * CFrame.new(0, 8, 17);
                                        task.spawn(function(...)
                                            local r126 = r123;
                                            r126:InvokeServer(r127, r128, Vector3.new(0, 0, 0));
                                            return; 
                                        end);
                                    end;
                                    local v = {
                                        r126()
                                    };
                                    local o = v[2];
                                    v = r126();
                                    Workspace = r25.Workspace;
                                    local ref25 = Workspace:FindFirstChild(r122.Name .. "SpawnedInToys");
                                    if v then
                                        return o:WaitForChild(selectedItem, 2);
                                    end;
                                    local PlayersInPlots = r25.Workspace.PlotItems.PlayersInPlots;
                                    ref25 = not PlayersInPlots:FindFirstChild(r122.Name);
                                    if ref25 then
                                        return ref25:WaitForChild(selectedItem, 2);
                                    end;
                                    return nil; 
                                end)();
                                if not (G == nil) then
                                    r116(G, r122);
                                end;
                            end;
                        end;
                    end;
                    grabEvents = not enabled2;
                    if grabEvents then
                        grabEvents = r25.Workspace.PlotItems.PlayersInPlots;
                        if not grabEvents:FindFirstChild(r122.Name) then
                            local G = (function(...)
                                local selectedItem = AntiKickStick2.selectedItem;
                                while not r124.Value do
                                    if not AntiKickStick2.enabled or tick() - tick() > 5 then
                                        return nil;
                                    else
                                        task.wait();
                                    end; 
                                end;
                                local SoundService = r125();
                                if SoundService then
                                    local CFrame_ = SoundService.CFrame * CFrame.new(0, 8, 17);
                                    task.spawn(function(...)
                                        r123:InvokeServer(r127, r128, Vector3.new(0, 0, 0));
                                        return; 
                                    end);
                                end;
                                local v = {
                                    r126()
                                };
                                local o = v[2];
                                v = r126();
                                local ref26 = r25.Workspace:FindFirstChild(r122.Name .. "SpawnedInToys");
                                if v then
                                    return o:WaitForChild(selectedItem, 2);
                                end;
                                local PlayersInPlots = r25.Workspace.PlotItems.PlayersInPlots;
                                ref26 = not PlayersInPlots:FindFirstChild(r122.Name);
                                if ref26 then
                                    return ref26:WaitForChild(selectedItem, 2);
                                end;
                                return nil; 
                            end)();
                            if not (G == nil) then
                                ref24 = r122.Character;
                                if ref24 then
                                    grabEvents:SetPrimaryPartCFrame(v7.HumanoidRootPart.CFrame);
                                end;
                                r117(G, r122);
                            end;
                        end;
                    end;
                    local character = not enabled2;
                    ref24 = character;
                    if character then
                        ref24 = enabled2;
                        while not enabled2 do
                            if G then
                                if not enabled2:FindFirstChild("Amethyst_FullHighlight") then
                                    S[x[9]](enabled2);
                                end;
                            end;
                            task.wait(.3);
                            local character = not enabled2;
                            if character then
                                if character then
                                    if not enabled2 or (not enabled2:FindFirstChild("StickyPart") or (not r122.Character or (not v20:FindFirstChild("HumanoidRootPart") or (r122.Character.HumanoidRootPart.Position - enabled2.StickyPart.Position).Magnitude >= 20))) then
                                        AntiKickStick2();
                                    end;
                                    ref24 = not AntiKickStick2.enabled or (not r122.Character or (not r15:FindFirstChild("Humanoid") or (not enabled2 or (not enabled2:FindFirstChild("StickyPart") or (not SelfGrab2:FindFirstChild("StickyWeld") or not enabled2.StickyPart.StickyWeld.Part1)))));
                                    task.wait(.05);
                                    if not AntiKickStick2.enabled or (not r122.Character or (not r15:FindFirstChild("Humanoid") or (not enabled2 or (not enabled2:FindFirstChild("StickyPart") or (not SelfGrab2:FindFirstChild("StickyWeld") or not enabled2.StickyPart.StickyWeld.Part1))))) then
                                        if not enabled2 or not enabled2:FindFirstChild("StickyPart") then
                                            AntiKickStick2();
                                        end;
                                    end;
                                end;
                            else
                                ref24 = not AntiKickStick2.enabled or (not enabled2:FindFirstChild("StickyPart") or (enabled2.StickyPart.CanTouch == false or (not r122.Character or (not SelfGrab2:FindFirstChild("HumanoidRootPart") or (not enabled2:FindFirstChild("StickyPart") or (r122.Character.HumanoidRootPart.Position - enabled2.StickyPart.Position).Magnitude >= 20)))));
                            end; 
                        end;
                        ref24 = enabled2:FindFirstChild("StickyPart") and enabled2.StickyPart.CanTouch == true;
                    else
                        ref24 = not AntiKickStick2.enabled or (not enabled2:FindFirstChild("StickyPart") or (enabled2.StickyPart.CanTouch == false or (not r122.Character or (not SelfGrab2:FindFirstChild("HumanoidRootPart") or (not enabled2:FindFirstChild("StickyPart") or (r122.Character.HumanoidRootPart.Position - enabled2.StickyPart.Position).Magnitude >= 20)))));
                    end; 
                end; 
            end);
        end; 
    end
});
v68:CreateToggle({
    ["Name"] = "Anti Kick (Stick)",
    ["Default"] = false,
    ["Callback"] = function(arg1_37, ...)
        if arg1_37 then
            AntiKickStick2.enabled = true;
            r47("Anti Kick", "Enabled with " .. AntiKickStick2.selectedItem);
            r44();
            AntiKickStick2.loopThread = task.spawn(function(...)
                local r129 = LocalPlayer;
                local grabEvents = r25.ReplicatedStorage:WaitForChild("GrabEvents");
                local setNetworkOwner = grabEvents:WaitForChild("SetNetworkOwner");
                grabEvents = r25.ReplicatedStorage:WaitForChild("PlayerEvents");
                local stickyPartEvent = grabEvents:WaitForChild("StickyPartEvent");
                local SpawnToyRemoteFunction = r25.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction;
                grabEvents = r25.ReplicatedStorage:WaitForChild("MenuToys");
                local destroyToy = grabEvents:WaitForChild("DestroyToy");
                local canSpawnToy = r129:WaitForChild("CanSpawnToy");
                local function r135()
                    local Character = r129.Character;
                    if Character then
                        return r129.Character.HumanoidRootPart;
                    end;
                    CharacterAdded = r129.CharacterAdded;
                    Character = CharacterAdded:Wait();
                    return Character:WaitForChild("HumanoidRootPart"); 
                end;
                local function r136()
                    local PlayersInPlots = r25.Workspace.PlotItems.PlayersInPlots;
                    if not PlayersInPlots:FindFirstChild(r129.Name) then
                        return false;
                    end;
                    for _, v in pairs(("pairs"):GetChildren()) do
                        local plotSign = v:FindFirstChild("PlotSign");
                        if plotSign then
                            plotSign = plotSign:FindFirstChild("ThisPlotsOwners");
                        end;
                        if plotSign then
                            for enabled2, G in G[1], pairs(G(plotSign)) do
                                plotSign = G.Value;
                                local Name = plotSign == r129.Name;
                                if Name then
                                    Name = r25.Workspace.PlotItems;
                                    plotSign = Name:FindFirstChild(v.Name);
                                    if plotSign then
                                        return true, plotSign;
                                    else
                                        
                                    end;
                                end; 
                            end;
                        end; 
                    end;
                    return false; 
                end;
                local function enabled2(...)
                    local selectedItem = AntiKickStick2.selectedItem;
                    while not r134.Value do
                        if not AntiKickStick2.enabled or tick() - tick() > 5 then
                            return nil;
                        else
                            task.wait();
                        end; 
                    end;
                    local r138 = r135();
                    if r138 then
                        task.spawn(function(...)
                            local r129 = r132;
                            r129:InvokeServer(r137, r138.CFrame * CFrame.new(0, 12, 20), Vector3.new(0, 0, 0));
                            return; 
                        end);
                    end;
                    local v = {
                        r136()
                    };
                    local o = v[2];
                    v = r136();
                    Workspace = r25.Workspace;
                    local ref27 = Workspace:FindFirstChild(Workspace.Name .. "SpawnedInToys");
                    if v then
                        return o:WaitForChild(selectedItem, 2);
                    end;
                    local PlayersInPlots = r25.Workspace.PlotItems.PlayersInPlots;
                    ref27 = not PlayersInPlots:FindFirstChild(Workspace.Name);
                    if ref27 then
                        return ref27:WaitForChild(selectedItem, 2);
                    end;
                    return nil; 
                end;
                local function v16()
                    local Workspace = r25.Workspace;
                    local k = Workspace:FindFirstChild(Workspace.Name .. "SpawnedInToys");
                    if k then
                        for SoundService, game in pairs(N(list3)) do
                            Workspace = v2.Name == AntiKickStick2.selectedItem;
                            if Workspace then
                                Workspace = r133;
                                Workspace:FireServer(game);
                            end; 
                        end;
                    end; 
                end;
                while AntiKickStick2.enabled do
                    task.wait();
                    local character = "Character";
                    local Workspace = r25.Workspace;
                    local selectedItem = AntiKickStick2.selectedItem;
                    grabEvents = Workspace:FindFirstChild(Workspace.Name .. "SpawnedInToys");
                    local r15 = grabEvents and {};
                    Workspace = r25.Workspace.PlotItems.PlayersInPlots;
                    if Workspace:FindFirstChild(Workspace.Name) then
                        Workspace = r136;
                        local list2 = {
                            Workspace()
                        };
                        local character = list2[2];
                        list2 = Workspace();
                        if list2 then
                            Workspace = S[character2];
                        end;
                        if list2 then
                            Workspace = r25.Workspace.Plots[character.Name];
                            local plotSign = Workspace:FindFirstChild("PlotSign");
                            if plotSign then
                                local r15 = (function(...)
                                    local selectedItem = AntiKickStick2.selectedItem;
                                    while not r134.Value do
                                        if not AntiKickStick2.enabled or tick() - tick() > 5 then
                                            return nil;
                                        else
                                            task.wait();
                                        end; 
                                    end;
                                    local r138 = r135();
                                    if r138 then
                                        task.spawn(function(...)
                                            local r129 = r132;
                                            r129:InvokeServer(r137, r138.CFrame * CFrame.new(0, 12, 20), Vector3.new(0, 0, 0));
                                            return; 
                                        end);
                                    end;
                                    local v = {
                                        r136()
                                    };
                                    local o = v[2];
                                    v = r136();
                                    Workspace = r25.Workspace;
                                    local ref28 = Workspace:FindFirstChild(Workspace.Name .. "SpawnedInToys");
                                    if v then
                                        return o:WaitForChild(selectedItem, 2);
                                    end;
                                    local PlayersInPlots = r25.Workspace.PlotItems.PlayersInPlots;
                                    ref28 = not PlayersInPlots:FindFirstChild(Workspace.Name);
                                    if ref28 then
                                        return ref28:WaitForChild(selectedItem, 2);
                                    end;
                                    return nil; 
                                end)();
                                if not (r15 == nil) then
                                    (function(object, ...)
                                        if not object or not object:FindFirstChild("StickyPart") then
                                            return;
                                        end;
                                        if not r135() then
                                            return;
                                        end;
                                        if object:FindFirstChild("SoundPart") then
                                            if not k.SoundPart:FindFirstChild("PartOwner") or k.SoundPart.PartOwner.Value ~= r129.Name then
                                                local r129 = r130;
                                                r129:FireServer(k.SoundPart, k.SoundPart.CFrame);
                                            end;
                                        end; 
                                    end)(r15);
                                end;
                            end;
                        end;
                    end;
                    grabEvents = not r15;
                    if grabEvents then
                        grabEvents = r25.Workspace.PlotItems.PlayersInPlots;
                        if not grabEvents:FindFirstChild(Workspace.Name) then
                            local r15 = (function(...)
                                local selectedItem = AntiKickStick2.selectedItem;
                                while not r134.Value do
                                    if not AntiKickStick2.enabled or tick() - tick() > 5 then
                                        return nil;
                                    else
                                        task.wait();
                                    end; 
                                end;
                                local r138 = r135();
                                if r138 then
                                    task.spawn(function(...)
                                        local r129 = r132;
                                        r129:InvokeServer(r137, r138.CFrame * CFrame.new(0, 12, 20), Vector3.new(0, 0, 0));
                                        return; 
                                    end);
                                end;
                                local v = {
                                    r136()
                                };
                                local o = v[2];
                                v = r136();
                                Workspace = r25.Workspace;
                                local ref29 = Workspace:FindFirstChild(Workspace.Name .. "SpawnedInToys");
                                if v then
                                    return o:WaitForChild(selectedItem, 2);
                                end;
                                local PlayersInPlots = r25.Workspace.PlotItems.PlayersInPlots;
                                ref29 = not PlayersInPlots:FindFirstChild(Workspace.Name);
                                if ref29 then
                                    return ref29:WaitForChild(selectedItem, 2);
                                end;
                                return nil; 
                            end)();
                            if not (r15 == nil) then
                                (function(object2, ...)
                                    if not object2 or not object2:FindFirstChild("StickyPart") then
                                        return;
                                    end;
                                    if not r135() then
                                        return;
                                    end;
                                    if object2:FindFirstChild("SoundPart") then
                                        if not k.SoundPart:FindFirstChild("PartOwner") or k.SoundPart.PartOwner.Value ~= r129.Name then
                                            local r129 = r130;
                                            r129:FireServer(k.SoundPart, k.SoundPart.CFrame);
                                        end;
                                    end; 
                                end)(r15);
                            end;
                        end;
                    end;
                    while not r15 do
                        Workspace = Workspace;
                        if r15 then
                            (function(object3, ...)
                                if not object3 or not object3:FindFirstChild("StickyPart") then
                                    return;
                                end;
                                if not r135() then
                                    return;
                                end;
                                if object3:FindFirstChild("SoundPart") then
                                    if not k.SoundPart:FindFirstChild("PartOwner") or k.SoundPart.PartOwner.Value ~= r129.Name then
                                        local r129 = r130;
                                        r129:FireServer(k.SoundPart, k.SoundPart.CFrame);
                                    end;
                                end; 
                            end)(grabEvents and {});
                        end;
                        task.wait(.3);
                        Workspace = Workspace;
                        if not r15 or (not AntiKickStick2.enabled or (not r15:FindFirstChild("StickyPart") or (y.StickyPart.CanTouch == false or (not Workspace.Character or (not p:FindFirstChild("HumanoidRootPart") or (not r15:FindFirstChild("StickyPart") or (Workspace.Character.HumanoidRootPart.Position - y.StickyPart.Position).Magnitude >= 20)))))) then
                            Workspace = Workspace;
                            if not r15 or (not r15:FindFirstChild("StickyPart") or (not Workspace.Character or (not AntiKickReset:FindFirstChild("HumanoidRootPart") or (Workspace.Character.HumanoidRootPart.Position - y.StickyPart.Position).Magnitude >= 20))) then
                                do
                                    local Workspace = r25.Workspace;
                                    local k = Workspace:FindFirstChild(Workspace.Name .. "SpawnedInToys");
                                    if k then
                                        for SoundService, game in pairs(N(list3)) do
                                            Workspace = v2.Name == AntiKickStick2.selectedItem;
                                            if Workspace then
                                                Workspace = r133;
                                                Workspace:FireServer(game);
                                            end; 
                                        end;
                                    end; 
                                end;
                            end;
                            r15 = not AntiKickStick2.enabled or (not Workspace.Character or not r15:FindFirstChild("Humanoid"));
                            Workspace = Workspace;
                            task.wait(.05);
                            Workspace = Workspace;
                            if not AntiKickStick2.enabled or (not Workspace.Character or (not r15:FindFirstChild("Humanoid") or (not r15 or (not r15:FindFirstChild("StickyPart") or (not p:FindFirstChild("StickyWeld") or not y.StickyPart.StickyWeld.Part1))))) then
                                Workspace = Workspace;
                                if not r15 or not r15:FindFirstChild("StickyPart") then
                                    do
                                        local Workspace = r25.Workspace;
                                        local k = Workspace:FindFirstChild(Workspace.Name .. "SpawnedInToys");
                                        if k then
                                            for SoundService, game in pairs(N(list3)) do
                                                Workspace = v2.Name == AntiKickStick2.selectedItem;
                                                if Workspace then
                                                    Workspace = r133;
                                                    Workspace:FireServer(game);
                                                end; 
                                            end;
                                        end; 
                                    end;
                                end;
                            end;
                        end; 
                    end;
                    Workspace = list2; 
                end; 
            end);
        else
            AntiKickStick2.enabled = false;
            if AntiKickStick2.loopThread then
                task.cancel(AntiKickStick2.loopThread);
                AntiKickStick2.loopThread = nil;
            end;
            local menuToys = r25.ReplicatedStorage:WaitForChild("MenuToys");
            local destroyToy = menuToys:WaitForChild("DestroyToy");
            local ref30 = idk;
            local o = r25.Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys");
            if o then
                local GetChildren = o.GetChildren;
                ref30 = {
                    GetChildren(o)
                };
                ref30 = GetChildren[1];
                for ref30, ref30 in pairs(N(ref30)) do
                    if v5.Name == AntiKickStick2.selectedItem then
                        destroyToy:FireServer(ref30);
                    end; 
                end;
            end;
            r47("Anti Kick", "Disabled");
            return;
        end; 
    end
});
v68:CreateToggle({
    ["Name"] = "Anti (Anti-Input)",
    ["Default"] = false,
    ["Callback"] = function(arg1_42, ...)
        local AntiAntiInput = r28.AntiAntiInput;
        if arg1_42 then
            AntiAntiInput.enabled = true;
            local Workspace = r25.Workspace;
            local r140 = LocalPlayer;
            local r141 = {};
            local ref31 = ipairs;
            local character = "items";
            ref31 = ref31[3];
            ref31 = ref31[2];
            for ref31, enabled2 in ref31(list3) do
                ref31 = ref31;
                r141[enabled2] = true; 
            end;
            for ref31, v16 in ipairs(v16(Workspace)) do
                local character = "enabled";
                if AntiAntiInput[character] and (character and r141[v16.Name]) then
                    task.spawn(r142, v16);
                end; 
            end;
            AntiAntiInput.connection = Workspace.DescendantAdded:Connect(function(arg1_44)
                local o = "enabled";
                if r139[o] and (o and r141[k.Name]) then
                    task.spawn(r142, arg1_44);
                end; 
            end);
            r47("Anti Anti-Input", "Active");
            r44();
        else
            AntiAntiInput.enabled = false;
            if AntiAntiInput.connection then
                AntiAntiInput.connection:Disconnect();
                AntiAntiInput.connection = nil;
            end;
            r47("Anti Anti-Input", "Disabled");
            return;
        end; 
    end
});
v68:CreateToggle({
    ["Name"] = "Anti Ragdoll (for blob)",
    ["Default"] = false,
    ["Callback"] = function(arg1_45, ...)
        if arg1_45 then
            r28.AntiRagdoll.enabled = true;
            r28.AntiRagdoll.ragdolledSit = false;
            local characterEvents = r25.ReplicatedStorage:WaitForChild("CharacterEvents");
            local ragdollRemote = characterEvents:WaitForChild("RagdollRemote");
            local function r146(...)
                local Character = LocalPlayer.Character;
                if not Character then
                    return;
                end;
                local humanoid = Character:FindFirstChild("Humanoid");
                local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                if not humanoid or not humanoidRootPart then
                    return;
                end;
                local v = humanoid:GetPropertyChangedSignal("SeatPart");
                r28.AntiRagdoll.connections.ARSeat = v:Connect(function(...)
                    if r148.SeatPart and (r148.SeatPart.Parent and (r148.SeatPart.Parent.Name == "CreatureBlobman" and not r28.AntiRagdoll.ragdolledSit)) then
                        r28.AntiRagdoll.ragdolledSit = true;
                        local SeatPart = r148.SeatPart;
                        while not r148 do
                            if r148 then
                                task.wait();
                            end;
                            SeatPart = r145;
                            SeatPart:FireServer(r149, 3);
                            SeatPart = r148;
                            ragdolled = SeatPart:FindFirstChild("Ragdolled");
                            while not ragdolled do
                                if ragdolled then
                                    task.wait();
                                end;
                                task.wait(.4);
                                if ragdolled then
                                    ragdolled.Sit = false;
                                    if SeatPart then
                                        SeatPart:Sit(ragdolled);
                                    end;
                                    task.delay(0.25, function(...)
                                        local r147 = r148;
                                        enabled = enabled.SeatPart and r28.AntiRagdoll.enabled;
                                        while not r147 do
                                            if r147 then
                                                local r147 = r147;
                                                enabled = r147;
                                                if r147 then
                                                    local r147 = r147;
                                                    enabled = r147:FindFirstChild("HumanoidRootPart");
                                                end;
                                                if enabled then
                                                    local r147 = r145;
                                                    r147:FireServer(r147.HumanoidRootPart, 1);
                                                end;
                                                task.wait(.05);
                                            end;
                                            r28.AntiRagdoll.ragdolledSit = false;
                                            return; 
                                        end;
                                        enabled = enabled.SeatPart and r28.AntiRagdoll.enabled;
                                        r147 = enabled; 
                                    end);
                                end;
                                break; 
                            end;
                            local ref32 = not ragdolled;
                            if ref32 then
                                SeatPart = SeatPart;
                                ragdolled = ref32 and not ragdolled.Sit;
                                SeatPart = SeatPart;
                            else
                                
                            end; 
                        end;
                    end; 
                end); 
            end;
            if LocalPlayer.Character then
                r146();
            end;
            r28.AntiRagdoll.connections.ARChar = LocalPlayer.CharacterAdded:Connect(function(Character)
                task.wait(1);
                if r28.AntiRagdoll.enabled then
                    r146();
                end; 
            end);
            r47("Anti Ragdoll", "Enabled - Blob protection active");
            r44();
        else
            r28.AntiRagdoll.enabled = false;
            local o = r28.AntiRagdoll[1];
            for SoundService, game in o, pairs(o) do
                if game then
                    game:Disconnect();
                end; 
            end;
            r28.AntiRagdoll.connections = {};
            r28.AntiRagdoll.ragdolledSit = false;
            r47("Anti Ragdoll", "Disabled");
            return;
        end; 
    end
});
v68:CreateDropdown({
    ["Name"] = "Anti-Input Lag Item",
    ["Items"] = r28.AntiInputLag.items,
    ["Default"] = "FoodHamburger",
    ["Callback"] = function(arg1_47)
        r28.AntiInputLag.selectedItem = arg1_47;
        r47("Anti-Input Lag", "Selected: " .. arg1_47, 2); 
    end
});
v68:CreateToggle({
    ["Name"] = "Anti-Input Lag",
    ["Default"] = false,
    ["Callback"] = function(arg1_48, ...)
        if arg1_48 then
            r28.AntiInputLag.enabled = true;
            r28.AntiInputLag.loopThread = task.spawn(function(...)
                while r28.AntiInputLag[idk["enabled"]] do
                    if not k.Character or (not idk:FindFirstChild("Humanoid") or k.Character.Humanoid.Health <= 0) then
                        task.wait(0.5);
                    else
                        local Character = k.Character;
                        local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                        local character = "Humanoid";
                        if not Character or (not humanoidRootPart or not Character:FindFirstChild(idk[character])) then
                            task.wait(0.5);
                        else
                            local Character = r25.Workspace;
                            local idk = Character:FindFirstChild(k.Name .. "SpawnedInToys");
                            if not idk then
                                task.wait(0.5);
                            end;
                            local SpawnToyRemoteFunction = r25.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction;
                            local canSpawnToy = LocalPlayer:FindFirstChild("CanSpawnToy");
                            local character = "currentItem";
                            Character = r28.AntiInputLag[idk[character]];
                            local r153 = Character;
                            if not r153 or (not r153.Parent or not character:FindFirstChild("HoldPart")) then
                                local Character = idk:FindFirstChild(r28.AntiInputLag.selectedItem);
                                local r153 = Character;
                                if not r153 then
                                    tick();
                                    canSpawnToy = Character;
                                    while not canSpawnToy do
                                        local Character = Character;
                                        if canSpawnToy then
                                            local Character = Character;
                                            if not r28.AntiInputLag.enabled or tick() - tick() > 5 then
                                                task.wait();
                                            else
                                                task.wait();
                                            end;
                                        end;
                                        if humanoidRootPart then
                                            task.spawn(function(...)
                                                local r150 = r152;
                                                r150:InvokeServer(r28.AntiInputLag.selectedItem, r151.CFrame * CFrame.new(0, 12, 20), Vector3.new(0, 0, 0));
                                                return; 
                                            end);
                                        end;
                                        for D = 1, 20 do
                                            local r153 = idk:FindFirstChild(r28.AntiInputLag.selectedItem);
                                            if not r153 then
                                                task.wait(.007);
                                            end;
                                            if not r153 then
                                                task.wait(.007);
                                            else
                                                r28.AntiInputLag.currentItem = r153;
                                                canSpawnToy = r153:FindFirstChild("HoldPart");
                                                canSpawnToy = Character;
                                                if canSpawnToy then
                                                    canSpawnToy:FindFirstChild("HoldItemRemoteFunction");
                                                end;
                                                local Character = canSpawnToy;
                                                local r154 = canSpawnToy;
                                                if canSpawnToy then
                                                    canSpawnToy:FindFirstChild("DropItemRemoteFunction");
                                                end;
                                                local r155 = canSpawnToy;
                                                Character = Character;
                                                local Character = Character;
                                                if not canSpawnToy or (not r154 or not r155) then
                                                    r28.AntiInputLag.currentItem = nil;
                                                    task.wait();
                                                else
                                                    task.spawn(function(...)
                                                        pcall(function(...)
                                                            local ref33 = LocalPlayer;
                                                            ref33:InvokeServer(r28, idk);
                                                            return; 
                                                        end);
                                                        return; 
                                                    end);
                                                    task.wait(.05);
                                                    task.spawn(function(...)
                                                        pcall(function(...)
                                                            local ref34 = r155;
                                                            ref34:InvokeServer(r153, r151.CFrame * CFrame.new(0, 199, 0), Vector3.zero);
                                                            return; 
                                                        end);
                                                        return; 
                                                    end);
                                                    task.wait(.035);
                                                end;
                                            end; 
                                        end;
                                        if not r153 then
                                            task.wait(.007);
                                        else
                                            r28.AntiInputLag.currentItem = r153;
                                            canSpawnToy = r153:FindFirstChild("HoldPart");
                                            canSpawnToy = Character;
                                            if canSpawnToy then
                                                canSpawnToy:FindFirstChild("HoldItemRemoteFunction");
                                            end;
                                            local Character = canSpawnToy;
                                            local r154 = canSpawnToy;
                                            if canSpawnToy then
                                                canSpawnToy:FindFirstChild("DropItemRemoteFunction");
                                            end;
                                            local r155 = canSpawnToy;
                                            Character = Character;
                                            if 20 then
                                                local Character = Character;
                                                if not canSpawnToy or (not r154 or not r155) then
                                                    r28.AntiInputLag.currentItem = nil;
                                                    task.wait();
                                                else
                                                    task.spawn(function(...)
                                                        pcall(function(...)
                                                            local ref33 = LocalPlayer;
                                                            ref33:InvokeServer(r28, idk);
                                                            return; 
                                                        end);
                                                        return; 
                                                    end);
                                                    task.wait(.05);
                                                    task.spawn(function(...)
                                                        pcall(function(...)
                                                            local ref34 = r155;
                                                            ref34:InvokeServer(r153, r151.CFrame * CFrame.new(0, 199, 0), Vector3.zero);
                                                            return; 
                                                        end);
                                                        return; 
                                                    end);
                                                    task.wait(.035);
                                                end;
                                            else
                                                local Character = canSpawnToy;
                                            end;
                                        end; 
                                    end;
                                end;
                            end;
                        end;
                    end; 
                end;
                r28.AntiInputLag.currentItem = nil; 
            end);
            r47("Anti-Input Lag", "Enabled with " .. r28.AntiInputLag.selectedItem);
            r44();
        else
            r28.AntiInputLag.enabled = false;
            if r28.AntiInputLag.loopThread then
                task.cancel(r28.AntiInputLag.loopThread);
                r28.AntiInputLag.loopThread = nil;
            end;
            r28.AntiInputLag.currentItem = nil;
            r47("Anti-Input Lag", "Disabled");
            return;
        end; 
    end
});
v68:CreateSection("Other");
v68:CreateToggle({
    ["Name"] = "Safe Pos LP",
    ["Default"] = false,
    ["Callback"] = function(arg1_50, ...)
        if arg1_50 then
            r28.SafePos.enabled = true;
            local Character = LocalPlayer.Character;
            if Character then
                local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                if humanoidRootPart then
                    r28.SafePos.target = humanoidRootPart.CFrame;
                end;
            end;
            if not r28.SafePos.target then
                r47("Safe Pos", "Error: Could not save position!");
                return;
            end;
            r28.SafePos.loopThread = task.spawn(function()
                while r28.SafePos.enabled do
                    local Character = LocalPlayer.Character;
                    if Character then
                        local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                        if humanoidRootPart then
                            humanoidRootPart.CFrame = r28.SafePos.target;
                        end;
                    end;
                    task.wait(); 
                end; 
            end);
            r47("Safe Pos", "Enabled - Position saved");
            r44();
        else
            r28.SafePos.enabled = false;
            if r28.SafePos.loopThread then
                task.cancel(r28.SafePos.loopThread);
                r28.SafePos.loopThread = nil;
            end;
            r47("Safe Pos", "Disabled");
            return;
        end; 
    end
});
v68:CreateToggle({
    ["Name"] = "Auto Sit Blob",
    ["Default"] = false,
    ["Callback"] = function(arg1_51, ...)
        if arg1_51 then
            r28.AutoSitBlob.enabled = true;
            r28.AutoSitBlob.loopThread = task.spawn(function(...)
                local r156 = LocalPlayer;
                local menuToys = r25.ReplicatedStorage:WaitForChild("MenuToys");
                local spawnToyRemoteFunction = menuToys:WaitForChild("SpawnToyRemoteFunction");
                local ReplicatedStorage = r25.ReplicatedStorage;
                menuToys = ReplicatedStorage:WaitForChild("MenuToys");
                menuToys:WaitForChild("DestroyToy");
                local function r158()
                    local v = "Workspace";
                    local O = r25[idk[v]];
                    local F = O:FindFirstChild(r156.Name .. "SpawnedInToys");
                    if F then
                        local GetChildren = F.GetChildren;
                        for o, GetChildren in v, pairs(N(v)) do
                            GetChildren = o;
                            if v4.Name == "CreatureBlobman" then
                                return GetChildren;
                            else
                                
                            end; 
                        end;
                    end;
                    return nil; 
                end;
                local function v()
                    local canSpawnToy = r156:FindFirstChild("CanSpawnToy");
                    if canSpawnToy then
                        local F = tick();
                        local SoundService = not canSpawnToy.Value;
                        local list3 = tick() - F < 3;
                        while not SoundService do
                            if list3 then
                                task.wait();
                            end;
                            if not r156:FindFirstChild(F[o]).Value then
                                return nil;
                            end;
                            local Character = r156.Character;
                            if not Character then
                                return nil;
                            end;
                            local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                            if not humanoidRootPart then
                                return nil;
                            end;
                            r156 = r157;
                            r156:InvokeServer("CreatureBlobman", humanoidRootPart.CFrame * CFrame.new(0, 0, 5), Vector3.new(0, 0, 0));
                            local enabled2 = 14067835394454;
                            task["+UQ="]();
                            for v = 1, 10 do
                                local ref35 = v;
                                local enabled2 = r158();
                                if enabled2 then
                                    return enabled2;
                                else
                                    task.wait();
                                end; 
                            end;
                            return nil; 
                        end;
                    end; 
                end;
                local function game(arg1_52)
                    if not arg1_52 then
                        return false;
                    end;
                    local Character = r156.Character;
                    if not Character then
                        return false;
                    end;
                    local humanoid = Character:FindFirstChild("Humanoid");
                    if not humanoid then
                        return false;
                    end;
                    local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                    if not humanoidRootPart then
                        return false;
                    end;
                    local vehicleSeat = arg1_52:FindFirstChild("VehicleSeat");
                    if not vehicleSeat then
                        return false;
                    end;
                    if humanoid.SeatPart == vehicleSeat then
                        return true;
                    end;
                    humanoidRootPart.CFrame = vehicleSeat.CFrame * CFrame.new(0, 2, 0);
                    task.wait();
                    vehicleSeat:Sit(humanoid);
                    task.wait();
                    return humanoid.SeatPart == vehicleSeat; 
                end;
                while r28.AutoSitBlob.enabled do
                    if not r156.Character then
                        task.wait();
                    end; 
                end;
                r28.AutoSitBlob.currentBlob = nil; 
            end);
            r47("Auto Sit Blob", "Enabled - Auto sitting on blob");
            r44();
        else
            r28.AutoSitBlob.enabled = false;
            if r28.AutoSitBlob.loopThread then
                task.cancel(r28.AutoSitBlob.loopThread);
                r28.AutoSitBlob.loopThread = nil;
            end;
            r28.AutoSitBlob.currentBlob = nil;
            r47("Auto Sit Blob", "Disabled");
            return;
        end; 
    end
});
v68:CreateToggle({
    ["Name"] = "Self Grab (Blob)",
    ["Default"] = false,
    ["Callback"] = function(arg1_53, ...)
        if arg1_53 then
            r28.SelfGrab.enabled = true;
            r28.SelfGrab.loopThread = task.spawn(function(...)
                local r159 = LocalPlayer;
                r159:WaitForChild("IsHeld");
                local ReplicatedStorage = r25.ReplicatedStorage;
                local menuToys = ReplicatedStorage:FindFirstChild("MenuToys");
                if menuToys then
                    
                end;
                local r160 = menuToys;
                menuToys = r25.ReplicatedStorage;
                local menuToys = menuToys:FindFirstChild("MenuToys");
                local list3 = 211;
                if menuToys then
                    
                end;
                local r164 = false;
                local function r165()
                    if r28.SelfGrab.SpawningBlob then
                        return nil;
                    end;
                    if r161 and r161.Parent then
                        return r161;
                    end;
                    local r159 = r159;
                    local canSpawnToy = r159:FindFirstChild("CanSpawnToy");
                    if canSpawnToy then
                        local SoundService = not canSpawnToy.Value;
                        local list3 = tick() - tick() < 1;
                        while not SoundService do
                            if list3 then
                                task.wait();
                            end;
                            if not r159:FindFirstChild(F[list2]).Value then
                                return nil;
                            end;
                            r28.SelfGrab.SpawningBlob = true;
                            r159 = r160;
                            if r159 then
                                local r159 = r160;
                                r159:InvokeServer("CreatureBlobman", CFrame.new(0, 0, 0), Vector3.new(0, 0, 0));
                            end;
                            r159 = tick() - tick() < 1;
                            while r159 do
                                local Workspace = r25.Workspace;
                                local list2 = Workspace:FindFirstChild(Workspace.Name .. "SpawnedInToys");
                                if list2 then
                                    local creatureBlobman = list2:FindFirstChild("CreatureBlobman");
                                    if creatureBlobman then
                                        r28.SelfGrab.SpawningBlob = false;
                                        local r161 = creatureBlobman;
                                        return creatureBlobman;
                                    else
                                        
                                    end;
                                end;
                                task.wait(); 
                            end;
                            r28.SelfGrab.SpawningBlob = false;
                            return nil; 
                        end;
                    end; 
                end;
                local function r166()
                    if r161 and r161.Parent then
                        return true;
                    end;
                    local Workspace = r25.Workspace;
                    local k = Workspace:FindFirstChild(Workspace.Name .. "SpawnedInToys");
                    if k then
                        local idk = k.GetChildren[1];
                        for SoundService, game in idk, pairs(N(idk)) do
                            if v2.Name == "CreatureBlobman" then
                                local r161 = game;
                                return true;
                            else
                                
                            end; 
                        end;
                    end;
                    return r165() ~= nil; 
                end;
                local function r168(arg1_55)
                    if not arg1_55 then
                        return;
                    end;
                    if not r163 then
                        return;
                    end;
                    if r162 ~= nil then
                        k.Massless = r162;
                    end;
                    k.CFrame = r163;
                    k.Velocity = Vector3.zero; 
                end;
                local function game(arg1_56, arg2_56)
                    if r28.SelfGrab.ProcessingGrab then
                        return false;
                    end;
                    if tick() - r28.SelfGrab.GrabCooldown < 0 then
                        return false;
                    end;
                    r28.SelfGrab.ProcessingGrab = true;
                    r28.SelfGrab.GrabCooldown = tick();
                    if not r166() then
                        r28.SelfGrab.ProcessingGrab = false;
                        return false;
                    end;
                    if not r161 then
                        r28.SelfGrab.ProcessingGrab = false;
                        return false;
                    end;
                    if r164 then
                        r167(arg1_56);
                    end;
                    local r159 = r161;
                    local blobmanSeatAndOwnerScript = r159:FindFirstChild("BlobmanSeatAndOwnerScript");
                    r159 = r161;
                    local rightDetector = r159:FindFirstChild("RightDetector");
                    local rightDetector = r159:FindFirstChild("RightDetector");
                    local v = rightDetector and {};
                    local ref36 = r161;
                    if blobmanSeatAndOwnerScript then
                        if rightDetector then
                            local r159 = r161;
                        end;
                        r159 = r161;
                    end;
                    r159 = ref36;
                    if not blobmanSeatAndOwnerScript then
                        r28.SelfGrab.ProcessingGrab = false;
                        return false;
                    end;
                    blobmanSeatAndOwnerScript:FireServer(nil, arg1_56, v);
                    r159 = r161;
                    ref36 = r159:FindFirstChild("VehicleSeat");
                    if ref36 then
                        task.wait();
                        F.Sit = false;
                        ref36:Sit(arg2_56);
                    end;
                    r28.SelfGrab.ProcessingGrab = false;
                    return true; 
                end;
                local function y(arg1_57, arg2_57)
                    if r28.SelfGrab.ProcessingRelease then
                        return;
                    end;
                    if tick() - r28.SelfGrab.GrabCooldown < 0 then
                        return;
                    end;
                    r28.SelfGrab.ProcessingRelease = true;
                    r28.SelfGrab.GrabCooldown = tick();
                    if not r161 then
                        r28.SelfGrab.ProcessingRelease = false;
                        return;
                    end;
                    local r159 = r161;
                    r159 = r161;
                    local rightDetector = r159:FindFirstChild("RightDetector");
                    local v = rightDetector and {};
                    rightDetector = r159:FindFirstChild("BlobmanSeatAndOwnerScript");
                    local ref37 = rightDetector and {};
                    if ref37 then
                        ref37:FireServer(v);
                        task.wait();
                    end;
                    if arg2_57 then
                        F.Sit = false;
                        task.wait();
                    end;
                    if r164 then
                        task.wait();
                        r168(arg1_57);
                        local r164 = false;
                    end;
                    r28.SelfGrab.ProcessingRelease = false; 
                end;
                r165();
                local enabled = r28.SelfGrab.enabled;
                while enabled do
                    local character3 = "Character";
                    local character = r159[idk[character3]];
                    local list2 = character and {};
                    if list2 then
                        r166();
                        local Value = enabled:WaitForChild("IsHeld").Value;
                        if Value then
                            local O = true;
                        end;
                        if Value then
                            if not r28.SelfGrab.RegrabActive then
                                r28.SelfGrab.RegrabActive = true;
                            end;
                            if false --[[r164]] then
                                (function(arg1_58, arg2_58, ...)
                                    if r28.SelfGrab.ProcessingGrab then
                                        return false;
                                    end;
                                    if tick() - r28.SelfGrab.GrabCooldown < 0 then
                                        return false;
                                    end;
                                    r28.SelfGrab.ProcessingGrab = true;
                                    r28.SelfGrab.GrabCooldown = tick();
                                    if not r166() then
                                        r28.SelfGrab.ProcessingGrab = false;
                                        return false;
                                    end;
                                    if not r161 then
                                        r28.SelfGrab.ProcessingGrab = false;
                                        return false;
                                    end;
                                    if r164 then
                                        r167(arg1_58);
                                    end;
                                    local blobmanSeatAndOwnerScript = r161:FindFirstChild("BlobmanSeatAndOwnerScript");
                                    local rightDetector = r161:FindFirstChild("RightDetector");
                                    local rightDetector = r161:FindFirstChild("RightDetector");
                                    local v = rightDetector and {};
                                    local ref38 = r161;
                                    if blobmanSeatAndOwnerScript then
                                        if rightDetector then
                                            local r161 = r161;
                                        end;
                                        r161 = r161;
                                    end;
                                    if not blobmanSeatAndOwnerScript then
                                        r28.SelfGrab.ProcessingGrab = false;
                                        return false;
                                    end;
                                    blobmanSeatAndOwnerScript:FireServer(nil, arg1_58, v);
                                    ref38 = r161:FindFirstChild("VehicleSeat");
                                    if ref38 then
                                        task.wait();
                                        F.Sit = false;
                                        ref38:Sit(arg2_58);
                                    end;
                                    r28.SelfGrab.ProcessingGrab = false;
                                    return true; 
                                end)(character and {}, character and {});
                            end;
                        else
                            if r28.SelfGrab.RegrabActive then
                                r28.SelfGrab.RegrabActive = false;
                                (function(arg1_59, arg2_59, ...)
                                    if r28.SelfGrab.ProcessingRelease then
                                        return;
                                    end;
                                    if tick() - r28.SelfGrab.GrabCooldown < 0 then
                                        return;
                                    end;
                                    r28.SelfGrab.ProcessingRelease = true;
                                    r28.SelfGrab.GrabCooldown = tick();
                                    if not r161 then
                                        r28.SelfGrab.ProcessingRelease = false;
                                        return;
                                    end;
                                    local rightDetector = r161:FindFirstChild("RightDetector");
                                    local v = rightDetector and {};
                                    rightDetector = r161:FindFirstChild("BlobmanSeatAndOwnerScript");
                                    local ref39 = rightDetector and {};
                                    if ref39 then
                                        ref39:FireServer(v);
                                        task.wait();
                                    end;
                                    if arg2_59 then
                                        F.Sit = false;
                                        task.wait();
                                    end;
                                    if r164 then
                                        task.wait();
                                        r168(arg1_59);
                                        local r164 = false;
                                    end;
                                    r28.SelfGrab.ProcessingRelease = false;
                                    return; 
                                end)(character and {}, character and {});
                            end;
                        end;
                    end;
                    task.wait(); 
                end;
                if "SelfGrab" then
                    r161:FireServer(r161);
                end;
                r28.SelfGrab.MyBlob = r161; 
            end);
            r47("Self Grab", "Enabled - Self grab with blob");
            r44();
        else
            r28.SelfGrab.enabled = false;
            if r28.SelfGrab.loopThread then
                task.cancel(r28.SelfGrab.loopThread);
                r28.SelfGrab.loopThread = nil;
            end;
            local SelfGrab = r28.SelfGrab;
            if SelfGrab.MyBlob then
                SelfGrab = r25.ReplicatedStorage:FindFirstChild("MenuToys");
                local F = SelfGrab and {};
                if F then
                    F:FireServer(r28.SelfGrab.MyBlob);
                end;
            end;
            r28.SelfGrab.MyBlob = nil;
            r47("Self Grab", "Disabled");
            return;
        end; 
    end
});
v68:CreateToggle({
    ["Name"] = "Noclip Obj players",
    ["Default"] = false,
    ["Callback"] = function(arg1_60, ...)
        if arg1_60 then
            r28.Noclip.enabled = true;
            r28.Noclip.connections = {};
            r28.Noclip.parts = {};
            local Players = r25.Players;
            for v, character2 in Players, ipairs(Players:GetPlayers()) do
                local r171 = character2;
                Players = v;
                local O = r171 ~= LocalPlayer;
                if O then
                    r28.Noclip.parts[r171] = {};
                    if r171.Character then
                        r172(r171.Character);
                    end;
                    r171.CharacterAdded:Connect(function(Character, ...)
                        if r28.Noclip.enabled then
                            r172(Character);
                        end;
                        return; 
                    end);
                end; 
            end;
            Players = r25.Players;
            for Players, character2 in ipairs(N(character2)) do
                local O = character2 ~= LocalPlayer;
                Players = Players;
                if O then
                    Players = r25.Workspace:FindFirstChild(character2.Name .. "SpawnedInToys");
                    if Players then
                        for G, y in ipairs(y(Players)) do
                            r170(y);
                            Players = G; 
                        end;
                    end;
                end; 
            end;
            DescendantAdded = r25.Workspace.DescendantAdded;
            r28.Noclip.connections.descendantAdded = DescendantAdded:Connect(function(arg1_65)
                if not r28.Noclip.enabled then
                    return;
                end;
                if arg1_65:IsA("Model") and not r169(k.Name) then
                    local SoundService = false;
                    while k.Parent do
                        if not (arg1_65[list3].Name == LocalPlayer.Name .. "SpawnedInToys") then
                            
                        end;
                        if not SoundService then
                            task.wait(.1);
                            r170(arg1_65);
                        end;
                        break; 
                    end;
                end; 
            end);
            DescendantAdded = r25.Players.PlayerAdded;
            r28.Noclip.connections.playerAdded = DescendantAdded:Connect(function(arg1_66, ...)
                if not r28.Noclip.enabled or arg1_66 == LocalPlayer then
                    return;
                end;
                r28.Noclip.parts[arg1_66] = {};
                k.CharacterAdded:Connect(function(Character, ...)
                    local v = "enabled";
                    if r28.Noclip[idk[v]] then
                        local r15 = k.GetChildren[1];
                        for SoundService, game in r15, ipairs(N(r15)) do
                            if game:IsA("BasePart") then
                                v2.CanCollide = false;
                            end; 
                        end;
                    end;
                    return; 
                end); 
            end);
            r28.Noclip.connections.periodicCheck = task.spawn(function()
                while r28.Noclip.enabled do
                    local ref40 = r15;
                    for _, v in ipairs(("ipairs"):GetPlayers()) do
                        ref40 = v ~= LocalPlayer;
                        if ref40 then
                            ref40 = v.Character;
                        end;
                        if ref40 then
                            ref40 = v.Character;
                            for character2, enabled2 in ipairs(N(enabled2)) do
                                ref40 = character2;
                                if enabled2:IsA("BasePart") and enabled2.CanCollide then
                                    enabled2.CanCollide = false;
                                end; 
                            end;
                        end; 
                    end;
                    for SoundService, v in r25.Players[1], ipairs(N(v)) do
                        local O = v ~= LocalPlayer;
                        if O then
                            ref40 = r25.Workspace:FindFirstChild(v.Name .. "SpawnedInToys");
                            if ref40 then
                                for ref40, v16 in ipairs(v16(ref40)) do
                                    if v16:IsA("Model") and not r169(v16.Name) then
                                        for _, list2 in ipairs(list2(v16)) do
                                            if list2:IsA("BasePart") and list2.CanCollide then
                                                list2.CanCollide = false;
                                            end; 
                                        end;
                                    end; 
                                end;
                            end;
                        end; 
                    end;
                    task.wait(2); 
                end; 
            end);
            r47("Noclip", "Enabled - Noclip through players/items");
            r44();
        else
            r28.Noclip.enabled = false;
            local Noclip = r28.Noclip;
            for _, Noclip in pairs(v2.connections) do
                if Noclip then
                    if type(Noclip) == "RBXScriptConnection" then
                        Noclip:Disconnect();
                    else
                        task.cancel(Noclip);
                    end;
                end; 
            end;
            r28.Noclip.connections = {};
            r28.Noclip.parts = {};
            r47("Noclip", "Disabled");
            return;
        end; 
    end
});
v68:CreateButton({
    ["Name"] = "Delete Legs",
    ["Callback"] = function(...)
        local r173 = LocalPlayer;
        local Character = r173.Character;
        if Character then
            local Character = Character;
        end;
        local RagdollRemote = r25.ReplicatedStorage.CharacterEvents.RagdollRemote;
        if Character then
            if Character then
                local ref41 = Character;
                local Character = ref41:FindFirstChild("Left Leg") and {};
            end;
        end;
        if Character then
            local Torso = Character.Torso;
            r25.Workspace.FallenPartsDestroyHeight = -100;
            r47("Delete Legs", "Deleting legs...", 3);
            r44();
            RagdollRemote:FireServer(Character, 2);
            task.wait(0.5);
            Character["Right Leg"].CFrame = CFrame.new(0, -10000, 0);
            Character["Left Leg"].CFrame = CFrame.new(0, -10000, 0);
            task.wait(.3);
            v4.CFrame = CFrame.new(0, -9970, 0);
            task.wait(0.5);
            local CFrame_ = v4.CFrame;
            v4.CFrame = CFrame_;
            task.wait(0.5);
            local FallenPartsDestroyHeight = r25.Workspace.FallenPartsDestroyHeight;
            r25.Workspace.FallenPartsDestroyHeight = FallenPartsDestroyHeight;
            if not Character:FindFirstChild("Left Leg") and not Character:FindFirstChild("Right Leg") then
                r47("Delete Legs", "Legs successfully deleted!", 5);
                r44();
                task.spawn(function(...)
                    while not r174 do
                        if r174.Parent then
                            if not r174:FindFirstChild("Left Leg") and not r174:FindFirstChild("Right Leg") then
                                local humanoid = r174:FindFirstChild("Humanoid");
                                if humanoid then
                                    humanoid = r173.PlayerGui;
                                    humanoid = humanoid:FindFirstChild("ControlsGui");
                                    if humanoid then
                                        humanoid = humanoid:FindFirstChild("PCFrame");
                                        if humanoid then
                                            local stand = humanoid:FindFirstChild("Stand");
                                            if stand then
                                                k.HipHeight = 2;
                                            else
                                                k.HipHeight = 0;
                                            end;
                                        end;
                                    end;
                                end;
                                task.wait();
                            else
                            end;
                        end;
                        return; 
                    end; 
                end);
            else
                r47("Delete Legs", "Warning: Legs not fully deleted", 3);
            end;
        else
            r47("Delete Legs", "Error: Missing body parts", 3);
        end; 
    end
});
local Target = Window:CreateTab("Target");
local r183 = workspace:WaitForChild("PlotItems"):WaitForChild("PlayersInPlots");
local partOwner2 = {
    ["loopKick"] = {},
    ["ragdollLoopKick"] = {},
    ["loopBring"] = {}
};
local function r186(...)
    local SoundService = 195[1];
    for F, v in pairs(SoundService) do
        if v then
            local ref42 = type(v) == "table";
        end;
        if v then
            local ref43 = game[1];
            ref43 = ref43[2];
            for character2, enabled2 in pairs(v) do
                local r187 = enabled2;
                if S[O] and S[G].PacketLag then
                    pcall(function(...)
                        local O = S[O];
                        O:Destroy();
                        return; 
                    end);
                end; 
            end;
            S[N2][F] = {};
        end; 
    end; 
end;
task.wait(0.5);
Target:CreateSection("Select Player");
local function r192()
    local v = v[1];
    for o, game in v, ipairs(v:GetPlayers()) do
        local ref44 = o;
        if ref44 ~= LocalPlayer then
            local character2 = r191(ref44);
            table.insert({}, character2);
            ({})[character2] = ref44;
        end; 
    end;
    return {}, {}; 
end;
local r193 = r192();
local r195 = Target:CreateDropdown({
    ["Name"] = "Select Target Player",
    ["Items"] = r193,
    ["Default"] = "",
    ["Callback"] = function(arg1_73)
        if arg1_73 == "" then
            Selected.kickPlayer = nil;
            Selected.currentPlayer = nil;
            return;
        end;
        local F = r194[arg1_73];
        if F then
            Selected.kickPlayer = O;
            Selected.currentPlayer = F;
            r47("Target Selected", "Target: " .. r191(F), 2);
            r44();
        end; 
    end
});
local function r196()
    local r193 = r192();
    local r194 = F[2];
    if r195 then
        r195:Refresh(r193, true);
        local Parent = Selected.currentPlayer and Selected.currentPlayer.Parent;
        if Parent then
            r195:Set(r191(Selected.currentPlayer));
        end;
    end; 
end;
task.spawn(function()
    while true do
        task.wait(0.5);
        local o = o[1];
        for SoundService, game in ipairs(o:GetPlayers()) do
            if game ~= LocalPlayer then
                local ref45 = r184(game);
                if (ref45:GetAttribute("WasInPlot") or false) ~= ref45 then
                    ref45:SetAttribute("WasInPlot", ref45);
                end;
            end; 
        end;
        if false then
            r196();
        end; 
    end; 
end);
task.spawn(function()
    while true do
        task.wait(0.5);
        r196(); 
    end; 
end);
local v74 = S[F];
if v73 then
    r195:Set(r191(Selected.currentPlayer));
end;
r25.Players.PlayerAdded:Connect(function(arg1_74)
    task.wait(0.5);
    r196(); 
end);
r25.Players.PlayerRemoving:Connect(function(arg1_75)
    r196();
    if Selected.currentPlayer == arg1_75 then
        Selected.kickPlayer = nil;
        Selected.currentPlayer = nil;
        r195:Set("");
        r47("Target", "Selected player left the game", 2);
    end; 
end);
task.spawn(function(...)
    for _, v in ipairs(("ipairs"):GetPlayers()) do
        if v ~= LocalPlayer then
            v:SetAttribute("WasInPlot", r184(v));
        end; 
    end;
    local function k()
        r196(); 
    end;
    r183.ChildAdded:Connect(k);
    r183.ChildRemoved:Connect(k); 
end);
Target:CreateSection("Grabs func");
Target:CreateToggle({
    ["Name"] = "Fast kick",
    ["Default"] = false,
    ["Callback"] = function(arg1_78, ...)
        if arg1_78 then
            if not Selected.kickPlayer then
                r47("Error", "Select a target player first!", 3);
                return;
            end;
            if not PermanentPallet.enabled or not PermanentPallet.currentPallet then
                r47("Error", "Enable Permanent Pallet first!", 3);
                return;
            end;
            r186();
            RagdollLoopKick.enabled = true;
            RagdollLoopKick.loopThread = task.spawn(function()
                local currentPallet = PermanentPallet.currentPallet;
                if not (currentPallet and {}) then
                    RagdollLoopKick.enabled = false;
                    r47("Error", "SoundPart not found!", 3);
                    return;
                end;
                local ReplicatedStorage = r25.ReplicatedStorage;
                local grabEvents = ReplicatedStorage:WaitForChild("GrabEvents");
                grabEvents:WaitForChild("SetNetworkOwner");
                grabEvents:WaitForChild("CreateGrabLine");
                grabEvents:WaitForChild("DestroyGrabLine");
                local ref46 = false;
                local enabled = RagdollLoopKick.enabled;
                while not enabled do
                    if enabled then
                        currentPallet = LocalPlayer.Character;
                        local v18 = currentPallet and {};
                        if not v18 then
                            task.wait();
                        end;
                        ReplicatedStorage = not nil;
                        local Character = Selected.kickPlayer.Character;
                        local e = Character and {};
                        local v20 = Character and {};
                        if not v20 or (not e or not r198(e)) then
                            r188("ragdollLoopKick");
                            false --[[ref46]] = false;
                            task.wait();
                        else
                            if r197(Selected.kickPlayer) then
                                if false then
                                    r188("ragdollLoopKick");
                                    false --[[ref46]] = false;
                                end;
                                task.wait(.1);
                            else
                                if not false then
                                    if not nil then
                                        false --[[ref46]] = tick();
                                    end;
                                    if tick() - nil > 0 then
                                        if not false then
                                            v18.CFrame = (Character and {}).CFrame * CFrame.new(0, -6, -10);
                                            v18.Velocity = Vector3.zero;
                                            false --[[ref46]] = tick();
                                            task.wait();
                                        else
                                            false --[[ref46]] = tick() - 0;
                                        end;
                                    end;
                                    v18.CFrame = v20.CFrame * CFrame.new(0, -6, -10);
                                    v18.Velocity = Vector3.zero;
                                    if not v20:FindFirstChild("PartOwner") then
                                        v:FireServer(v20, v20.CFrame);
                                    end;
                                    task.wait();
                                    false --[[ref46]]:FireServer(v20);
                                    false --[[ref46]]:FireServer(v20, Vector3.zero, v20.Position, false);
                                    e.PlatformStand = true;
                                    e.Sit = true;
                                    if tick() - nil > .4 then
                                        if e.PlatformStand then
                                            false --[[ref46]] = true;
                                            v18.CFrame = U_;
                                            v18.Velocity = Vector3.zero;
                                            U_ = r190("ragdollLoopKick", Instance.new("Attachment", v18));
                                            U_.Name = "RagdollLoopKickAttHold";
                                            local ragdollLoopKick = r190("ragdollLoopKick", Instance.new("Attachment", v20));
                                            ragdollLoopKick.Name = "RagdollLoopKickAttTarget";
                                            local ragdollLoopKick = r190("ragdollLoopKick", Instance.new("AlignPosition"));
                                            ragdollLoopKick.Attachment0 = ragdollLoopKick;
                                            ragdollLoopKick.Attachment1 = U_;
                                            ragdollLoopKick.MaxForce = math.huge;
                                            ragdollLoopKick.Responsiveness = math.huge;
                                            ragdollLoopKick.Name = "RagdollLoopKickAlignPos";
                                            ragdollLoopKick.Parent = v20;
                                            local ragdollLoopKick = r190("ragdollLoopKick", Instance.new("AlignOrientation"));
                                            ragdollLoopKick.Attachment0 = ragdollLoopKick;
                                            ragdollLoopKick.Attachment1 = U_;
                                            ragdollLoopKick.MaxTorque = math.huge;
                                            ragdollLoopKick.Responsiveness = math.huge;
                                            ragdollLoopKick.Name = "RagdollLoopKickAlignOri";
                                            ragdollLoopKick.Parent = v20;
                                            e:ChangeState(Enum.HumanoidStateType.Physics);
                                        else
                                            
                                        end;
                                    end;
                                    task.wait();
                                else
                                    if nil then
                                        if ((currentPallet and {}).Position - (Character and {}).Position).Magnitude > 30 then
                                            r188("ragdollLoopKick");
                                            false --[[ref46]] = false;
                                            v18.CFrame = (Character and {}).CFrame * CFrame.new(0, -6, -10);
                                            v18.Velocity = Vector3.zero;
                                            task.wait();
                                        else
                                            
                                        end;
                                    end;
                                    0 = (0 + ReplicatedStorage * .016) % 360;
                                    l_ = partOwner2;
                                    t_ = l_.ragdollLoopKick;
                                    for undefined, t_ in pairs(t_) do
                                        l_ = idk;
                                        if t_ then
                                            l_ = t_.Name == "RagdollLoopKickAttHold";
                                        end;
                                        if not l_ then
                                            
                                        end;
                                        if nil then
                                            nil.Position = Vector3.new(0, 14.5, 0);
                                            nil.CFrame = CFrame.new(0, 14.5, 0) * CFrame.Angles(math.rad(AntiBurn), 0, 0);
                                        end;
                                        if not v20:FindFirstChild("PartOwner") then
                                            v:FireServer(v20, v20.CFrame);
                                        end;
                                        false --[[ref46]]:FireServer(v20);
                                        false --[[ref46]]:FireServer(v20, Vector3.zero, v20.Position, false);
                                        if tick() - 0 >= .15 then
                                            0 = tick();
                                            SoundService.CFrame = (Character and {}).CFrame * CFrame.new(0, 2, 0);
                                            if not SoundService:FindFirstChild("PartOwner") then
                                                v:FireServer(SoundService, SoundService.CFrame);
                                            end;
                                            task.wait();
                                            SoundService.CFrame = CFrame.new(0, 1000000000, 0);
                                        end; 
                                    end;
                                end;
                            end;
                        end;
                    end;
                    r188("ragdollLoopKick");
                    false --[[ref46]] = false;
                    return; 
                end; 
            end);
            r47("Ragdoll Loop Kick", "Enabled - Target: " .. Selected.kickPlayer.Name, 3);
            r44();
        else
            RagdollLoopKick.enabled = false;
            if RagdollLoopKick.loopThread then
                task.cancel(RagdollLoopKick.loopThread);
                RagdollLoopKick.loopThread = nil;
            end;
            r188("ragdollLoopKick");
            r47("Ragdoll Loop Kick", "Disabled", 2);
            return;
        end; 
    end
});
Target:CreateToggle({
    ["Name"] = "control kick",
    ["Default"] = false,
    ["Callback"] = function(arg1_79, ...)
        if arg1_79 then
            if not Selected.kickPlayer then
                r47("Error", "Select a target player first!", 3);
                return;
            end;
            if not PermanentPallet.enabled or not PermanentPallet.currentPallet then
                r47("Error", "Enable Permanent Pallet first!", 3);
                return;
            end;
            local kickPlayer = Selected.kickPlayer;
            local currentPallet = PermanentPallet.currentPallet;
            r186();
            RagdollLoopKick.enabled = true;
            RagdollLoopKick.loopThread = task.spawn(function()
                if not (r200 and {}) then
                    RagdollLoopKick.enabled = false;
                    r47("Error", "SoundPart not found!", 3);
                    return;
                end;
                local grabEvents = r25.ReplicatedStorage:WaitForChild("GrabEvents");
                grabEvents:WaitForChild("SetNetworkOwner");
                grabEvents:WaitForChild("CreateGrabLine");
                grabEvents:WaitForChild("DestroyGrabLine");
                local zero = Vector3.zero;
                CFrame.Angles(0, 0, 0);
                tick();
                local r201 = {};
                local enabled = RagdollLoopKick.enabled;
                while not enabled do
                    if enabled then
                        local Character = LocalPlayer.Character;
                        local a = Character and {};
                        if not a then
                            task.wait();
                        end;
                        local v74 = not nil;
                        if v74 then
                            zero = a.CFrame;
                        end;
                        local Character = r199.Character;
                        if Character then
                            local Character = Character:FindFirstChildOfClass("Humanoid");
                        end;
                        n_ = not (Character and {});
                        Character = n_;
                    end;
                    r188("ragdollLoopKick");
                    if nil then
                        nil.Destroy(nil);
                        break;
                    end;
                    local r201 = {};
                    return; 
                end; 
            end);
            r47("Ragdoll Loop Kick", "Enabled - Target: " .. kickPlayer.Name, 3);
            r44();
        else
            RagdollLoopKick.enabled = false;
            if RagdollLoopKick.loopThread then
                task.cancel(RagdollLoopKick.loopThread);
                RagdollLoopKick.loopThread = nil;
            end;
            r188("ragdollLoopKick");
            r47("Ragdoll Loop Kick", "Disabled", 2);
            return;
        end; 
    end
});
Target:CreateToggle({
    ["Name"] = "loop bring",
    ["Default"] = false,
    ["Callback"] = function(arg1_81, ...)
        if arg1_81 then
            local v74 = not Selected.kickPlayer;
            if v74 then
                solarisui:Notify({
                    ["Title"] = "Error",
                    ["Content"] = "Select a target player first!",
                    ["Duration"] = 3
                });
                return;
            end;
            if not PermanentPallet.enabled or not PermanentPallet.currentPallet then
                solarisui:Notify({
                    ["Title"] = "Error",
                    ["Content"] = "Enable Permanent Pallet first!",
                    ["Duration"] = 3
                });
                return;
            end;
            r186();
            LoopKick.enabled = true;
            LoopKick.loopThread = task.spawn(function()
                local ReplicatedStorage = r25.ReplicatedStorage;
                local grabEvents = ReplicatedStorage:WaitForChild("GrabEvents");
                grabEvents:WaitForChild("SetNetworkOwner");
                local currentPallet = PermanentPallet.currentPallet;
                local v = currentPallet and {};
                local ref47 = 0;
                ref47 = false;
                local enabled = LoopKick.enabled;
                while not enabled do
                    if enabled then
                        currentPallet = LocalPlayer.Character;
                        local i = currentPallet and {};
                        if not i then
                            task.wait();
                        end;
                        ReplicatedStorage = not nil;
                        if ReplicatedStorage then
                            ref47 = i.CFrame;
                        end;
                        local Character = Selected.kickPlayer.Character;
                        if not Character or (not Character or not r198(Character)) then
                            r188("loopBring");
                            ref47 = false;
                            task.wait();
                        else
                            if r197(Selected.kickPlayer) then
                                if false then
                                    r188("loopBring");
                                end;
                                task.wait(.4);
                            else
                                if not false then
                                    if tick() - nil > 0 then
                                        if not false then
                                            i.CFrame = Character.CFrame * CFrame.new(0, -6, -10);
                                            i.Velocity = Vector3.zero;
                                            ref47 = true;
                                            task.wait();
                                        else
                                            ref47 = false;
                                        end;
                                    end;
                                    i.CFrame = Character.CFrame * CFrame.new(0, -6, -10);
                                    i.Velocity = Vector3.zero;
                                    if not Character:FindFirstChild("PartOwner") then
                                        SoundService:FireServer(Character, Character.CFrame);
                                    end;
                                    Character.Velocity = Vector3.zero;
                                    Character.AssemblyLinearVelocity = Vector3.zero;
                                    Character.AssemblyAngularVelocity = Vector3.zero;
                                    Character.RotVelocity = Vector3.zero;
                                    if not nil then
                                        ref47 = tick();
                                    end;
                                    if tick() - nil > .35 then
                                        if Character.Velocity.Magnitude < 5 then
                                            ref47 = false;
                                            i.CFrame = AntiRagdoll;
                                            i.Velocity = Vector3.zero;
                                            loopBring = r190("loopBring", Instance.new("Attachment", i));
                                            loopBring.Name = "LoopBringAttHold";
                                            local loopBring = r190("loopBring", Instance.new("Attachment", Character));
                                            loopBring.Name = "LoopBringAttTarget";
                                            U_ = r190("loopBring", Instance.new("AlignPosition"));
                                            U_.Attachment0 = loopBring;
                                            U_.Attachment1 = loopBring;
                                            U_.MaxForce = math.huge;
                                            U_.Responsiveness = 1000;
                                            U_.Name = "LoopBringAlignPos";
                                            U_.Parent = v19;
                                            local loopBring = r190("loopBring", Instance.new("AlignOrientation"));
                                            loopBring.Attachment0 = loopBring;
                                            loopBring.Attachment1 = loopBring;
                                            loopBring.MaxTorque = math.huge;
                                            loopBring.Responsiveness = 1000;
                                            loopBring.Name = "LoopBringAlignOri";
                                            loopBring.Parent = v19;
                                            if v then
                                                v.CFrame = Character.CFrame * CFrame.new(0, 2, 0);
                                                if not v:FindFirstChild("PartOwner") then
                                                    SoundService:FireServer(v, v.CFrame);
                                                end;
                                                task.wait();
                                                v.CFrame = CFrame.new(0, 1000000000, 0);
                                            end;
                                            local GetChildren = Character.GetChildren;
                                            T_ = {
                                                GetChildren(Character)
                                            };
                                            for v35, T_ in GetChildren[1], pairs(N(T_)) do
                                                if T_:IsA("BasePart") then
                                                    T_.CustomPhysicalProperties = PhysicalProperties.new(.2, 0, 10);
                                                end; 
                                            end;
                                        else
                                            ref47 = tick();
                                        end;
                                    end;
                                    task.wait();
                                else
                                    if enabled2 then
                                        if ((currentPallet and {}).Position - Character.Position).Magnitude > 30 then
                                            r188("loopBring");
                                            ref47 = tick();
                                            ref47 = false;
                                            i.CFrame = Character.CFrame * CFrame.new(0, -6, -10);
                                            i.Velocity = Vector3.zero;
                                            task.wait();
                                        else
                                            
                                        end;
                                    end;
                                    if v then
                                        ref47 = tick();
                                        v.CFrame = Character.CFrame * CFrame.new(0, 2, 0);
                                        if not v:FindFirstChild("PartOwner") then
                                            SoundService:FireServer(v, v.CFrame);
                                        end;
                                        task.wait();
                                        v.CFrame = CFrame.new(0, 1000000000, 0);
                                    end;
                                    0 = (0 + ReplicatedStorage * .016) % 360;
                                    for v40, list4 in pairs(list4) do
                                        local partOwner2 = "PartOwner";
                                        if list4 then
                                            local Name = list4.Name == "LoopBringAttHold";
                                        end;
                                        if partOwner2 then
                                            T_ = list4;
                                        else
                                            
                                        end;
                                        if nil then
                                            nil.CFrame = CFrame.new(0, 7, 0) * CFrame.Angles(math.rad(-90), 0, 0) * CFrame.Angles(0, math.rad(AntiBlob), 0);
                                        end;
                                        if not Character:FindFirstChild("PartOwner") then
                                            SoundService:FireServer(Character, Character.CFrame);
                                        end; 
                                    end;
                                end;
                            end;
                        end;
                    end;
                    r188("loopBring");
                    ref47 = false;
                    if nil then
                        local Character = LocalPlayer.Character;
                        if Character then
                            Character.CFrame = DeleteLegs;
                            Character.Velocity = Vector3.zero;
                        end;
                        break;
                    end;
                    return; 
                end; 
            end);
            solarisui:Notify({
                ["Title"] = "Loop Kick (Hold + Pallet)",
                ["Content"] = "Holding: " .. Selected.kickPlayer.Name,
                ["Duration"] = 3
            });
            r44();
        else
            LoopKick.enabled = false;
            if LoopKick.loopThread then
                task.cancel(LoopKick.loopThread);
                LoopKick.loopThread = nil;
            end;
            r188("loopBring");
            solarisui:Notify({
                ["Title"] = "Loop Kick (Hold + Pallet)",
                ["Content"] = "Disabled",
                ["Duration"] = 2
            });
            return;
        end; 
    end
});
Target:CreateSection("removed");
Target:CreateToggle({
    ["Name"] = "Remove Anti Kick",
    ["Default"] = false,
    ["Callback"] = function(arg1_82, ...)
        if arg1_82 then
            if not Selected.kickPlayer then
                r47("Error", "Select a target player first!", 3);
                return;
            end;
            RemoveAntiKick.enabled = true;
            RemoveAntiKick.targetPlayer = Selected.kickPlayer;
            r47("Remove Anti Kick", "Enabled - Target: " .. Selected.kickPlayer.Name, 3);
            r44();
            RemoveAntiKick.loopThread = task.spawn(function(...)
                local ReplicatedStorage = r25.ReplicatedStorage;
                local grabEvents = ReplicatedStorage:WaitForChild("GrabEvents");
                local setNetworkOwner = grabEvents:WaitForChild("SetNetworkOwner");
                local targetPlayer = RemoveAntiKick.targetPlayer;
                local v74 = {};
                local ref48 = v74;
                while not RemoveAntiKick.enabled do
                    if targetPlayer then
                        local Workspace = r25.Workspace;
                        if Workspace:FindFirstChild(RemoveAntiKick.targetPlayer.Name .. "SpawnedInToys") then
                            ref48 = list3.GetChildren[2];
                            for enabled2, ref48 in ipairs(N(targetPlayer)) do
                                for _, v18 in ipairs({
                                    "NinjaKunai",
                                    "NinjaShuriken",
                                    "AntiKick"
                                }) do
                                    if v7.Name == v18 then
                                        if not Workspace[v7.Name .. "_" .. tostring(ref48:GetDebugId())] then
                                            if (function(ref482, ...)
                                                k = ref482;
                                                if not k then
                                                    return false;
                                                end;
                                                F = k:FindFirstChild("SoundPart");
                                                if not F then
                                                    return false;
                                                end;
                                                r203(F, F.CFrame);
                                                task.wait();
                                                SoundService = F:FindFirstChild("PartOwner");
                                                if SoundService then
                                                    o = SoundService.Value == LocalPlayer.Name;
                                                end;
                                                if SoundService then
                                                    F.CFrame = CFrame.new(0, 1000, 0);
                                                    return true;
                                                end;
                                                return false; 
                                            end)(ref48) then
                                                Workspace[v7.Name .. v20 .. AntiKickReset] = true;
                                            end;
                                        end;
                                    else
                                        
                                    end; 
                                end; 
                            end;
                            local G = {
                                pairs(Workspace)
                            };
                            local v16 = pairs(Workspace)(G[2], G[3]);
                            while v16 do
                                if string.match(v16, "_(%d+)$") then
                                    local y = targetPlayer:FindFirstChild(string.match(v16, "^(.-)_"));
                                    if not y or not y:IsDescendantOf(targetPlayer) then
                                        Workspace[v16] = nil;
                                    end;
                                end; 
                            end;
                            task.wait();
                        end;
                    end;
                    RemoveAntiKick.enabled = false;
                    RemoveAntiKick.targetPlayer = nil;
                    ref48 = {};
                    return; 
                end; 
            end);
        else
            RemoveAntiKick.enabled = false;
            RemoveAntiKick.targetPlayer = nil;
            if RemoveAntiKick.loopThread then
                task.cancel(RemoveAntiKick.loopThread);
                RemoveAntiKick.loopThread = nil;
            end;
            r47("Remove Anti Kick", "Disabled", 2);
            return;
        end; 
    end
});
Target:CreateSection("Blob Functions");
Target:CreateToggle({
    ["Name"] = "Auto Sit Blob",
    ["Default"] = false,
    ["Callback"] = function(arg1_86, ...)
        if arg1_86 then
            r28.AutoSitBlob.enabled = true;
            r28.AutoSitBlob.loopThread = task.spawn(function(...)
                local r206 = LocalPlayer;
                local menuToys = r25.ReplicatedStorage:WaitForChild("MenuToys");
                local spawnToyRemoteFunction = menuToys:WaitForChild("SpawnToyRemoteFunction");
                local ReplicatedStorage = r25.ReplicatedStorage;
                menuToys = ReplicatedStorage:WaitForChild("MenuToys");
                menuToys:WaitForChild("DestroyToy");
                local function r208()
                    local ref49 = "O|b\xd4\xd0\xf7\x91x";
                    local v = "\xcd\xc4\x9e\xba\x16\x10<\x9d\x90";
                    local v74 = r25[idk[v]];
                    local F = v74:FindFirstChild(r206.Name .. "SpawnedInToys");
                    if F then
                        ref49 = F.GetChildren;
                        for o, ref49 in pairs(N(v)) do
                            ref49 = o;
                            if v4.Name == "CreatureBlobman" then
                                return ref49;
                            else
                                
                            end; 
                        end;
                    end;
                    return nil; 
                end;
                local function v()
                    local canSpawnToy = r206:FindFirstChild("CanSpawnToy");
                    if canSpawnToy then
                        local F = tick();
                        local SoundService = not canSpawnToy.Value;
                        local list3 = tick() - F < 3;
                        while not SoundService do
                            if list3 then
                                task.wait();
                            end;
                            if not r206:FindFirstChild(F[o]).Value then
                                return nil;
                            end;
                            local Character = r206.Character;
                            if not Character then
                                return nil;
                            end;
                            local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                            if not humanoidRootPart then
                                return nil;
                            end;
                            r206 = r207;
                            local enabled2 = 5094411635012;
                            r206:InvokeServer("CreatureBlobman", humanoidRootPart.CFrame * CFrame.new(0, 0, 5), Vector3.new(0, 0, 0));
                            task["+UQ="]();
                            for v = 1, 10 do
                                local ref50 = v;
                                local enabled2 = r208();
                                if enabled2 then
                                    return enabled2;
                                else
                                    task.wait();
                                end; 
                            end;
                            return nil; 
                        end;
                    end; 
                end;
                local function game(arg1_87)
                    if not arg1_87 then
                        return false;
                    end;
                    local Character = r206.Character;
                    if not Character then
                        return false;
                    end;
                    local humanoid = Character:FindFirstChild("Humanoid");
                    if not humanoid then
                        return false;
                    end;
                    local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                    if not humanoidRootPart then
                        return false;
                    end;
                    local vehicleSeat = arg1_87:FindFirstChild("VehicleSeat");
                    if not vehicleSeat then
                        return false;
                    end;
                    if humanoid.SeatPart == vehicleSeat then
                        return true;
                    end;
                    humanoidRootPart.CFrame = vehicleSeat.CFrame * CFrame.new(0, 2, 0);
                    task.wait();
                    vehicleSeat:Sit(humanoid);
                    task.wait();
                    return humanoid.SeatPart == vehicleSeat; 
                end;
                while r28.AutoSitBlob.enabled do
                    if not r206.Character then
                        task.wait();
                    end; 
                end;
                r28.AutoSitBlob.currentBlob = nil; 
            end);
            r47("Auto Sit Blob", "Enabled - Auto sitting on blob");
            r44();
        else
            r28.AutoSitBlob.enabled = false;
            if r28.AutoSitBlob.loopThread then
                task.cancel(r28.AutoSitBlob.loopThread);
                r28.AutoSitBlob.loopThread = nil;
            end;
            r28.AutoSitBlob.currentBlob = nil;
            r47("Auto Sit Blob", "Disabled");
            return;
        end; 
    end
});
Target:CreateToggle({
    ["Name"] = "Blob Kill",
    ["Default"] = false,
    ["Callback"] = function(arg1_88, ...)
        if arg1_88 then
            if not Selected.kickPlayer then
                r47("Error", "Select a target player first!", 3);
                return;
            end;
            BlobKill.enabled = true;
            BlobKill.selectedPlayers = {
                Selected.kickPlayer
            };
            BlobKill.currentBlob = nil;
            BlobKill.lastTeleportTime = 0;
            BlobKill.savedPosition = nil;
            local function F()
                local ref51 = "\xdd\x100d~Xe\xde\xd3";
                local v = "?\xa2b|.\xa0^r\n";
                local v74 = r25[idk[v]];
                local F = v74:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys");
                if F then
                    ref51 = F.GetChildren;
                    for o, ref51 in pairs(N(v)) do
                        ref51 = o;
                        if v4.Name == "CreatureBlobman" then
                            return ref51;
                        else
                            
                        end; 
                    end;
                end;
                return nil; 
            end;
            BlobKill.loopThread = task.spawn(function()
                local k = "enabled";
                while BlobKill[k] do
                    local Character = LocalPlayer.Character;
                    if not Character then
                        task.wait(0.5);
                    end;
                    local humanoid = Character:FindFirstChild("Humanoid");
                    if not humanoid or F.Health <= 0 then
                        task.wait(0.5);
                    else
                        if not F.SeatPart then
                            task.wait(0.5);
                        end;
                    end; 
                end;
                BlobKill.currentBlob = nil;
                BlobKill.savedPosition = nil;
                print("[Blob Kill] Цикл остановлен"); 
            end);
            r47("Blob Kill", "Enabled - Target: " .. Selected.kickPlayer.Name, 3);
            r44();
        else
            BlobKill.enabled = false;
            if BlobKill.loopThread then
                task.cancel(BlobKill.loopThread);
                BlobKill.loopThread = nil;
            end;
            BlobKill.currentBlob = nil;
            BlobKill.selectedPlayers = {};
            BlobKill.lastTeleportTime = 0;
            BlobKill.savedPosition = nil;
            r47("Blob Kill", "Disabled", 2);
            return;
        end; 
    end
});
Target:CreateToggle({
    ["Name"] = "Blob - Simple Teleport Spam",
    ["Default"] = false,
    ["Callback"] = function(arg1_90, ...)
        if arg1_90 then
            if not Selected.kickPlayer then
                r47("Error", "Select target!", 2);
                return;
            end;
            local kickPlayer = Selected.kickPlayer;
            local r211 = true;
            Target.SimpleSpamData = {
                ["thread"] = task.spawn(function()
                    local Parent = r210 and r210.Parent;
                    while not r211 do
                        if Parent then
                            if not LocalPlayer.Character then
                                task.wait(.1);
                            end;
                        end;
                        return; 
                    end; 
                end),
                ["active"] = true --[[r211]]
            };
            r47("Simple Teleport Spam", "Enabled", 2);
        else
            local SimpleSpamData = Target.SimpleSpamData;
            if SimpleSpamData then
                SimpleSpamData.active = false;
                if SimpleSpamData.thread then
                    task.cancel(SimpleSpamData.thread);
                end;
                Target.SimpleSpamData = nil;
            end;
            r47("Simple Teleport Spam", "Disabled", 2);
            return;
        end; 
    end
});
Window:CreateTab("Auras"):CreateSection("Ragdoll auras");
v86:CreateToggle({
    ["Name"] = "Ragdoll Aura",
    ["Default"] = false,
    ["Callback"] = function(arg1_91, ...)
        if arg1_91 then
            if not PermanentPallet.enabled then
                r47("Error", "Enable Permanent Pallet first!", 3);
                return;
            end;
            local currentPallet = PermanentPallet.currentPallet;
            if not currentPallet or not currentPallet.Parent then
                r47("Error", "Permanent Pallet not found!", 3);
                return;
            end;
            local soundPart = currentPallet:FindFirstChild("SoundPart");
            if not soundPart then
                r47("Error", "Pallet has no SoundPart!", 3);
                return;
            end;
            local partOwner = soundPart:FindFirstChild("PartOwner");
            if not partOwner or partOwner.Value ~= LocalPlayer.Name then
                r47("Error", "No ownership on pallet!", 3);
                return;
            end;
            RagdollAura.enabled = true;
            RagdollAura.ragdolledPlayers = {};
            RagdollAura.isTeleportingPallet = false;
            RagdollAura.loopThread = task.spawn(function(...)
                local r213 = LocalPlayer;
                local Character = r213.Character;
                if not Character then
                    return;
                end;
                local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                if not humanoidRootPart then
                    return;
                end;
                local SetNetworkOwner = r25.ReplicatedStorage.GrabEvents.SetNetworkOwner;
                local RagdollRemote = r25.ReplicatedStorage.CharacterEvents.RagdollRemote;
                local function game()
                    if not r212 then
                        return false;
                    end;
                    if not r212.Parent then
                        return false;
                    end;
                    r212 = r212;
                    local partOwner = r212:FindFirstChild("PartOwner");
                    if not partOwner or partOwner.Value ~= r213.Name then
                        r216(r212);
                    end;
                    return true; 
                end;
                while RagdollAura.enabled do
                    local Character = r213.Character;
                    if not not Character then
                        humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                        if not not humanoidRootPart then
                            local r212 = S[k][enabled2];
                            if not PermanentPallet.enabled or not PermanentPallet.currentPallet then
                                print("[Ragdoll Aura] Permanent Pallet отключилась");
                            else
                                if not (function(...)
                                    if not r212 then
                                        return false;
                                    end;
                                    if not r212.Parent then
                                        return false;
                                    end;
                                    r212 = r212;
                                    k = r212:FindFirstChild("PartOwner");
                                    if not k or k.Value ~= r213.Name then
                                        r216(r212);
                                    end;
                                    return true; 
                                end)() then
                                    print("[Ragdoll Aura] Не удалось установить ownership на паллет");
                                else
                                    for character, list2 in pairs(list2) do
                                        if os.clock() - list2 > 1 then
                                            RagdollAura.ragdolledPlayers[character] = nil;
                                        end; 
                                    end;
                                    for character, list2 in pairs(list2:GetPlayers()) do
                                        if not (list2 == r213) then
                                            if not not list2.Character then
                                                local humanoidRootPart = list2.Character:FindFirstChild("HumanoidRootPart");
                                                if not not humanoidRootPart then
                                                    if (i.Position - humanoidRootPart.Position).Magnitude <= 30 then
                                                        table.insert({}, list2);
                                                    end;
                                                end;
                                            end;
                                        end; 
                                    end;
                                    local ref52 = list2[1];
                                    for character, list2 in ipairs({}) do
                                        if not not RagdollAura.enabled then
                                            (function(list22, ...)
                                                if not list22 or list22 == r213 then
                                                    return false;
                                                end;
                                                local Character = k.Character;
                                                if not Character then
                                                    return false;
                                                end;
                                                local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                                                local v = not humanoidRootPart;
                                                if v then
                                                    if v then
                                                        return false;
                                                    end;
                                                    if (humanoidRootPart.Position - r214.Position).Magnitude > 30 then
                                                        RagdollAura.ragdolledPlayers[list22] = nil;
                                                        return false;
                                                    end;
                                                    if r218(humanoidRootPart) then
                                                        RagdollAura.ragdolledPlayers[list22] = os.clock();
                                                        return true;
                                                    end;
                                                    return false;
                                                else
                                                    Character:FindFirstChild(idk[game]);
                                                end; 
                                            end)(list2);
                                            if character % 3 == 0 then
                                                task.wait();
                                            end;
                                        end;
                                        task.wait(); 
                                    end;
                                end;
                                RagdollAura.ragdolledPlayers = {};
                                if RagdollAura.enabled then
                                    RagdollAura.enabled = false;
                                    break;
                                end;
                                print("[Ragdoll Aura] Цикл остановлен");
                                return;
                            end;
                        end;
                    end; 
                end; 
            end);
            task.spawn(function(...)
                while RagdollAura.enabled do
                    if RagdollAura.enabled and not PermanentPallet.enabled then
                        print("[Ragdoll Aura] Permanent Pallet отключилась, отключаем...");
                        if RagdollAura.loopThread then
                            task.cancel(RagdollAura.loopThread);
                            RagdollAura.loopThread = nil;
                        end;
                        RagdollAura.enabled = false;
                        RagdollAura.ragdolledPlayers = {};
                        RagdollAura.isTeleportingPallet = false;
                        break;
                    else
                        task.wait(1);
                    end;
                    return; 
                end; 
            end);
            r47("Ragdoll Aura", "Enabled", 3);
            r44();
        else
            RagdollAura.enabled = false;
            if RagdollAura.loopThread then
                task.cancel(RagdollAura.loopThread);
                RagdollAura.loopThread = nil;
            end;
            RagdollAura.ragdolledPlayers = {};
            RagdollAura.isTeleportingPallet = false;
            r47("Ragdoll Aura", "Disabled", 2);
            return;
        end; 
    end
});
v86:CreateSection("Blob Auras");
v86:CreateToggle({
    ["Name"] = "Auto Sit Blob",
    ["Default"] = false,
    ["Callback"] = function(arg1_96, ...)
        if arg1_96 then
            r28.AutoSitBlob.enabled = true;
            r28.AutoSitBlob.loopThread = task.spawn(function(...)
                local r219 = LocalPlayer;
                local menuToys = r25.ReplicatedStorage:WaitForChild("MenuToys");
                local spawnToyRemoteFunction = menuToys:WaitForChild("SpawnToyRemoteFunction");
                local ReplicatedStorage = r25.ReplicatedStorage;
                menuToys = ReplicatedStorage:WaitForChild("MenuToys");
                menuToys:WaitForChild("DestroyToy");
                local function r221()
                    local ref53 = "*\xd8;\xe6\x11g~\x88";
                    local v = "\xf2\x8dB\xda\xf9\\s\xe7\xca";
                    local v74 = r25[idk[v]];
                    local F = v74:FindFirstChild(r219.Name .. "SpawnedInToys");
                    if F then
                        ref53 = F.GetChildren;
                        for o, ref53 in pairs(N(v)) do
                            ref53 = o;
                            if v4.Name == "CreatureBlobman" then
                                return ref53;
                            else
                                
                            end; 
                        end;
                    end;
                    return nil; 
                end;
                local function v()
                    local canSpawnToy = r219:FindFirstChild("CanSpawnToy");
                    if canSpawnToy then
                        local F = tick();
                        local SoundService = not canSpawnToy.Value;
                        local list3 = tick() - F < 3;
                        while not SoundService do
                            if list3 then
                                task.wait();
                            end;
                            if not r219:FindFirstChild(F[o]).Value then
                                return nil;
                            end;
                            local Character = r219.Character;
                            if not Character then
                                return nil;
                            end;
                            local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                            if not humanoidRootPart then
                                return nil;
                            end;
                            r219 = r220;
                            r219:InvokeServer("CreatureBlobman", humanoidRootPart.CFrame * CFrame.new(0, 0, 5), Vector3.new(0, 0, 0));
                            local enabled2 = 5900298947259;
                            task["+UQ="]();
                            for v = 1, 10 do
                                local ref54 = v;
                                local enabled2 = r221();
                                if enabled2 then
                                    return enabled2;
                                else
                                    task.wait();
                                end; 
                            end;
                            return nil; 
                        end;
                    end; 
                end;
                local function game(arg1_97)
                    if not arg1_97 then
                        return false;
                    end;
                    local Character = r219.Character;
                    if not Character then
                        return false;
                    end;
                    local humanoid = Character:FindFirstChild("Humanoid");
                    if not humanoid then
                        return false;
                    end;
                    local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                    if not humanoidRootPart then
                        return false;
                    end;
                    local vehicleSeat = arg1_97:FindFirstChild("VehicleSeat");
                    if not vehicleSeat then
                        return false;
                    end;
                    if humanoid.SeatPart == vehicleSeat then
                        return true;
                    end;
                    humanoidRootPart.CFrame = vehicleSeat.CFrame * CFrame.new(0, 2, 0);
                    task.wait();
                    vehicleSeat:Sit(humanoid);
                    task.wait();
                    return humanoid.SeatPart == vehicleSeat; 
                end;
                while r28.AutoSitBlob.enabled do
                    if not r219.Character then
                        task.wait();
                    end; 
                end;
                r28.AutoSitBlob.currentBlob = nil; 
            end);
            r47("Auto Sit Blob", "Enabled - Auto sitting on blob");
            r44();
        else
            r28.AutoSitBlob.enabled = false;
            if r28.AutoSitBlob.loopThread then
                task.cancel(r28.AutoSitBlob.loopThread);
                r28.AutoSitBlob.loopThread = nil;
            end;
            r28.AutoSitBlob.currentBlob = nil;
            r47("Auto Sit Blob", "Disabled");
            return;
        end; 
    end
});
v86:CreateToggle({
    ["Name"] = "Kill Aura (Blob)",
    ["Default"] = false,
    ["Callback"] = function(arg1_98, ...)
        if arg1_98 then
            KillAura.enabled = true;
            KillAura.killCooldown = {};
            KillAura.targetPlayers = {};
            KillAura.lastKillTime = 0;
            KillAura.loopThread = task.spawn(function(...)
                local r222 = LocalPlayer;
                local ReplicatedStorage = r25.ReplicatedStorage;
                local Workspace = r25.Workspace;
                local function o()
                    local ref55 = 808535736696;
                    local F = r223:FindFirstChild(r222.Name .. "1E\xad\x17y\x81\xef{\x98\x18r\xd4\xe6");
                    if F then
                        808535736696 --[[ref55]] = F.GetChildren;
                        for o, undefined in pairs(N(list3)) do
                            808535736696 --[[ref55]] = o;
                            if v4.Name == "CreatureBlobman" then
                                return 808535736696 --[[ref55]];
                            else
                                
                            end; 
                        end;
                    end;
                    return nil; 
                end;
                local function game(arg1_100, arg2_100, arg3_100, ...)
                    local r224 = arg2_100;
                    if not arg1_100 or arg1_100 == r222 then
                        return false;
                    end;
                    local Character = k.Character;
                    if not Character then
                        return false;
                    end;
                    local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                    local humanoid = Character:FindFirstChild("Humanoid");
                    if not humanoidRootPart or (not humanoid or humanoid.Health <= 0) then
                        return false;
                    end;
                    local Character = r222.Character and {};
                    if not Character then
                        return false;
                    end;
                    if (Character.Position - humanoidRootPart.Position).Magnitude > 30 then
                        return false;
                    end;
                    local enabled2 = tick();
                    if KillAura.killCooldown[arg1_100] and enabled2 - KillAura.killCooldown[arg1_100] < 1 then
                        return false;
                    end;
                    local rightDet = arg3_100 or r224.rightDet;
                    local rightWeld = arg3_100 or r224.rightWeld;
                    if not rightDet or not rightWeld then
                        return false;
                    end;
                    pcall(function(...)
                        r224.grab:FireServer(r227, r225, r228);
                        task.wait(.0001);
                        r224.release:FireServer(r228);
                        return; 
                    end);
                    task.wait(.0002);
                    pcall(function(...)
                        if humanoid2 and humanoid2.Health > 0 then
                            humanoid2:ChangeState(Enum.HumanoidStateType.Dead);
                        end;
                        return; 
                    end);
                    KillAura.killCooldown[arg1_100] = enabled2;
                    KillAura.lastKillTime = enabled2;
                    return true; 
                end;
                ref56 = "enabled";
                while KillAura[ref56] do
                    ref56 = r15;
                    ref56 = r222.Character;
                    if not ref56 then
                        task.wait();
                    end;
                    local humanoid = ref56:FindFirstChild("Humanoid");
                    if not humanoid or character2.Health <= 0 then
                        task.wait(0);
                    else
                        ref56 = character2.SeatPart;
                        if not ref56 or not ref56:IsDescendantOf(Workspace) then
                            task.wait(0.5);
                        else
                            local enabled2 = ref56:FindFirstAncestor("CreatureBlobman");
                            if not enabled2 or not enabled2.Parent then
                                task.wait(0.5);
                            else
                                KillAura.currentBlob = enabled2;
                                if not (function(enabled22, ...)
                                    k = enabled22;
                                    if not k then
                                        return nil;
                                    end;
                                    F = k:FindFirstChild("BlobmanSeatAndOwnerScript");
                                    if not F then
                                        return nil;
                                    end;
                                    F:FindFirstChild("CreatureDrop");
                                    SoundService = F:FindFirstChild("CreatureGrab");
                                    o = F:FindFirstChild("CreatureRelease") or {};
                                    v = k:FindFirstChild("LeftDetector");
                                    if v then
                                        list3 = v:FindFirstChild("LeftWeld");
                                    end;
                                    game = k:FindFirstChild("RightDetector");
                                    list3 = k:FindFirstChild("RightDetector");
                                    if SoundService then
                                        if o then
                                            if v then
                                                if v then
                                                    if v74 then
                                                        character = list3 and {};
                                                    end;
                                                    k:FindFirstChild(idk[game]);
                                                    game = v74;
                                                end;
                                                v16 = game;
                                                k:FindFirstChild(idk[game]);
                                            end;
                                            game = v;
                                            k:FindFirstChild(idk[game]);
                                        end;
                                        list3 = o;
                                        k:FindFirstChild(idk[game]);
                                    end;
                                    if SoundService then
                                        return {
                                            ["grab"] = SoundService,
                                            ["release"] = o,
                                            ["leftDet"] = v,
                                            ["leftWeld"] = v,
                                            ["rightDet"] = game,
                                            ["rightWeld"] = list3 and {}
                                        };
                                    end;
                                    return nil; 
                                end)(enabled2) then
                                    task.wait();
                                end;
                                local v19 = {
                                    r25.Players:GetPlayers()
                                };
                                for character3, e in ipairs(N(v19)) do
                                    if e ~= r222 and e.Character then
                                        humanoid = e.Character;
                                        local humanoidRootPart = humanoid:FindFirstChild("HumanoidRootPart");
                                        if humanoidRootPart then
                                            humanoid:FindFirstChild(humanoid[A_]);
                                        end;
                                        if humanoidRootPart then
                                            local humanoidRootPart = ref56:FindFirstChild("HumanoidRootPart");
                                            if humanoidRootPart then
                                                if (humanoidRootPart.Position - humanoid:FindFirstChild("HumanoidRootPart").Position).Magnitude <= 30 then
                                                    table.insert({}, e);
                                                end;
                                            end;
                                        end;
                                    end; 
                                end;
                                ref56 = true;
                                for v18, e in ipairs({}) do
                                    if not not KillAura.enabled then
                                        humanoid = (function(e2, arg2_102, isEnabled, ...)
                                            local r224 = arg2_102;
                                            if not e2 or e2 == r222 then
                                                return false;
                                            end;
                                            local Character = k.Character;
                                            if not Character then
                                                return false;
                                            end;
                                            local humanoidRootPart = Character:FindFirstChild("HumanoidRootPart");
                                            local humanoid = Character:FindFirstChild("Humanoid");
                                            if not humanoidRootPart or (not humanoid or humanoid.Health <= 0) then
                                                return false;
                                            end;
                                            local Character = r222.Character and {};
                                            if not Character then
                                                return false;
                                            end;
                                            if (character2.Position - humanoidRootPart.Position).Magnitude > 30 then
                                                return false;
                                            end;
                                            local enabled2 = tick();
                                            if KillAura.killCooldown[e2] and enabled2 - KillAura.killCooldown[e2] < 1 then
                                                return false;
                                            end;
                                            local rightDet = isEnabled or r224.rightDet;
                                            local rightWeld = isEnabled or r224.rightWeld;
                                            if not rightDet or not rightWeld then
                                                return false;
                                            end;
                                            pcall(function(...)
                                                r224.grab:FireServer(r227, r225, r228);
                                                task.wait(.0001);
                                                r224.release:FireServer(r228);
                                                return; 
                                            end);
                                            task.wait(.0002);
                                            pcall(function(...)
                                                if humanoid2 and humanoid2.Health > 0 then
                                                    humanoid2:ChangeState(Enum.HumanoidStateType.Dead);
                                                end;
                                                return; 
                                            end);
                                            KillAura.killCooldown[e2] = enabled2;
                                            KillAura.lastKillTime = enabled2;
                                            return true; 
                                        end)(e, v(enabled2), true);
                                        if humanoid then
                                            ref56 = not true;
                                            if (0 + 1) % 3 == 0 then
                                                task.wait();
                                            end;
                                        end;
                                    end;
                                    if #{} > 0 then
                                        KillAura.targetPlayers = {};
                                    end;
                                    for v19, e in pairs(e) do
                                        if tick() - e > 1e-05 then
                                            table.insert({}, v19);
                                        end; 
                                    end;
                                    for v19, e in ipairs({}) do
                                        KillAura.killCooldown[e] = nil; 
                                    end;
                                    if 0 == 0 then
                                        task.wait(.1);
                                    else
                                        task.wait(.05);
                                    end; 
                                end;
                            end;
                        end;
                    end; 
                end;
                KillAura.currentBlob = nil;
                KillAura.spawningBlob = false;
                KillAura.killCooldown = {};
                KillAura.targetPlayers = {};
                KillAura.lastKillTime = 0;
                print("[Kill Aura] Цикл остановлен"); 
            end);
            r47("Kill Aura", "Enabled", 3);
            r44();
        else
            KillAura.enabled = false;
            if KillAura.loopThread then
                task.cancel(KillAura.loopThread);
                KillAura.loopThread = nil;
            end;
            KillAura.currentBlob = nil;
            KillAura.spawningBlob = false;
            KillAura.killCooldown = {};
            KillAura.targetPlayers = {};
            KillAura.lastKillTime = 0;
            r47("Kill Aura", "Disabled", 2);
            return;
        end; 
    end
});
v86:CreateToggle({
    ["Name"] = "Dual Lock Aura",
    ["Default"] = false,
    ["Callback"] = function(arg1_103, ...)
        if arg1_103 then
            AdvancedDuallock.enabled = true;
            AdvancedDuallock.processedPlayers = {};
            AdvancedDuallock.originalBarrierState = {};
            local function r229()
                local o = "Plots";
                local k = r25.Workspace:FindFirstChild(idk[o]);
                if k then
                    local o = k.GetChildren[1];
                    for SoundService, game in o, ipairs(N(o)) do
                        if game:FindFirstChild("Barrier") then
                            for enabled2, G in ipairs(G(game)) do
                                if G:IsA("BasePart") and G.Name == "PlotBarrier" then
                                    AdvancedDuallock.originalBarrierState[G] = G.CanCollide;
                                    G.CanCollide = false;
                                end; 
                            end;
                        end; 
                    end;
                end; 
            end;
            AdvancedDuallock.loopThread = task.spawn(function()
                while AdvancedDuallock.enabled do
                    r229();
                    task.wait(); 
                end; 
            end);
            AdvancedDuallock.loopThread = task.spawn(function()
                local ref57 = idk;
                ref57 = "enabled";
                while AdvancedDuallock[ref57] do
                    local character2 = "Character";
                    ref57 = LocalPlayer[idk[character2]];
                    if not ref57 then
                        task.wait();
                    end;
                    ref57 = ref57:FindFirstChild("Humanoid");
                    if not ref57 or v4.Health <= 0 then
                        task.wait();
                    else
                        local SeatPart = v4.SeatPart;
                        if not SeatPart or not SeatPart:IsDescendantOf(r25.Workspace) then
                            task.wait(0.5);
                        else
                            ref57 = SeatPart:FindFirstAncestor("CreatureBlobman");
                            if not ref57 or not v5.Parent then
                                task.wait(0.5);
                            else
                                AdvancedDuallock.currentBlob = v74;
                                if not ref57:FindFirstChild("BlobmanSeatAndOwnerScript") then
                                    task.wait(0.5);
                                end;
                            end;
                        end;
                    end; 
                end;
                AdvancedDuallock.currentBlob = nil;
                AdvancedDuallock.processedPlayers = {};
                print("[Dual Lock Aura] Цикл остановлен"); 
            end);
            r47("Dual Lock Aura", "Enabled", 3);
            r44();
        else
            AdvancedDuallock.enabled = false;
            if AdvancedDuallock.loopThread then
                task.cancel(AdvancedDuallock.loopThread);
                AdvancedDuallock.loopThread = nil;
            end;
            for SoundService, game in pairs(list3) do
                if SoundService then
                    local Parent = SoundService.Parent;
                end;
                if SoundService then
                    SoundService.CanCollide = game;
                end; 
            end;
            AdvancedDuallock.originalBarrierState = {};
            AdvancedDuallock.currentBlob = nil;
            AdvancedDuallock.processedPlayers = {};
            r47("Dual Lock Aura", "Disabled", 2);
            return;
        end; 
    end
});
v86:CreateToggle({
    ["Name"] = "Destroy Kick Aura",
    ["Default"] = false,
    ["Callback"] = function(arg1_104, ...)
        if arg1_104 then
            DestroyKickAura.enabled = true;
            DestroyKickAura.processedPlayers = {};
            DestroyKickAura.isSpawning = false;
            DestroyKickAura.lastSpawnTime = 0;
            DestroyKickAura.spawnInterval = 0;
            local function r232()
                local SoundService = DestroyKickAura[1];
                for F, v in SoundService, pairs(SoundService) do
                    if not F:IsDescendantOf(r25.Players) or (not F.Character or not r231(F)) then
                        DestroyKickAura.processedPlayers[F] = nil;
                    end; 
                end; 
            end;
            DestroyKickAura.loopThread = task.spawn(function()
                local k = "enabled";
                while DestroyKickAura[k] do
                    local Character = LocalPlayer.Character;
                    if not Character then
                        task.wait(.1);
                    end;
                    local humanoid = Character:FindFirstChild("Humanoid");
                    if not humanoid or F.Health <= 0 then
                        task.wait(.1);
                    else
                        local SeatPart = F.SeatPart;
                        if not SeatPart or not SeatPart:IsDescendantOf(r25.Workspace) then
                            task.wait(0.5);
                        else
                            local o = SeatPart:FindFirstAncestor("CreatureBlobman");
                            if not o or not o.Parent then
                                task.wait(0.5);
                            else
                                local ref58 = v74;
                                DestroyKickAura[humanoid["currentBlob"]] = ref58;
                                for ref58, enabled2 in ipairs(r233(30)) do
                                    ref58 = 0;
                                    if not not DestroyKickAura.enabled then
                                        r234(enabled2);
                                        if #r233(30) > 1 then
                                            task.wait(.1);
                                        end;
                                    end;
                                    r232();
                                    if #r233(30) == 0 then
                                        task.wait(.1);
                                    else
                                        task.wait();
                                    end; 
                                end;
                            end;
                        end;
                    end; 
                end;
                DestroyKickAura.currentBlob = nil;
                DestroyKickAura.processedPlayers = {};
                DestroyKickAura.isSpawning = false;
                print("[Destroy Kick Aura] Цикл остановлен"); 
            end);
            r47("Destroy Kick Aura", "Enabled", 3);
            r44();
        else
            DestroyKickAura.enabled = false;
            if DestroyKickAura.loopThread then
                task.cancel(DestroyKickAura.loopThread);
                DestroyKickAura.loopThread = nil;
            end;
            DestroyKickAura.currentBlob = nil;
            DestroyKickAura.processedPlayers = {};
            DestroyKickAura.isSpawning = false;
            r47("Destroy Kick Aura", "Disabled", 2);
            return;
        end; 
    end
});
v86:CreateSection("Kick Auras");
v86:CreateToggle({
    ["Name"] = "Kick Aura (Fixed)",
    ["Default"] = false,
    ["Callback"] = function(arg1_109, ...)
        if arg1_109 then
            KickAuraFixed.enabled = true;
            KickAuraFixed.fixedPlayers = {};
            KickAuraFixed.loopThread = task.spawn(function()
                local ReplicatedStorage = r25.ReplicatedStorage;
                local RagdollRemote = ReplicatedStorage.CharacterEvents.RagdollRemote;
                RagdollRemote = {
                    Vector3.new(-4, 15, -4),
                    Vector3.new(4, 15, -4),
                    Vector3.new(-4, 15, 4),
                    Vector3.new(4, 15, 4)
                };
                while KickAuraFixed.enabled do
                    if not LocalPlayer.Character then
                        break;
                    end;
                    return; 
                end; 
            end);
            r47("Kick Aura (Fixed)", "Enabled", 3);
            r44();
        else
            KickAuraFixed.enabled = false;
            if KickAuraFixed.loopThread then
                task.cancel(KickAuraFixed.loopThread);
                KickAuraFixed.loopThread = nil;
            end;
            local o = KickAuraFixed[1];
            for SoundService, game in pairs(o) do
                if v2.alignPos and v2.alignPos.Parent then
                    v2.alignPos:Destroy();
                end;
                if v2.alignOri and v2.alignOri.Parent then
                    v2.alignOri:Destroy();
                end; 
            end;
            KickAuraFixed.fixedPlayers = {};
            r47("Kick Aura (Fixed)", "Disabled", 2);
            return;
        end; 
    end
});
v86:CreateToggle({
    ["Name"] = "Jump Kick Aura",
    ["Default"] = false,
    ["Callback"] = function(arg1_110, ...)
        if arg1_110 then
            JumpKickAura.enabled = true;
            JumpKickAura.processedPlayers = {};
            JumpKickAura.loopThread = task.spawn(function()
                 
            end);
            r47("Jump Kick Aura", "Enabled", 3);
            r44();
        else
            JumpKickAura.enabled = false;
            if JumpKickAura.loopThread then
                task.cancel(JumpKickAura.loopThread);
                JumpKickAura.loopThread = nil;
            end;
            JumpKickAura.processedPlayers = {};
            r47("Jump Kick Aura", "Disabled", 2);
            return;
        end; 
    end
});
Window:CreateTab("Server"):CreateSection("Server Lag");
local function r237()
    if r236 then
        r236.Text = "Size: " .. PacketLag.packetSize;
        if PacketLag.packetSize > 600000 then
            r236.TextColor3 = Color3.fromRGB(255, 50, 50);
            if r235 then
                r235.Text = "⚠️ HIGH LAG WARNING! ⚠️";
                r235.TextColor3 = Color3.fromRGB(255, 50, 50);
            end;
        else
            if PacketLag.packetSize > 200000 then
                r236.TextColor3 = Color3.fromRGB(255, 165, 0);
                if r235 then
                    r235.Text = "⚠️ Medium lag risk ⚠️";
                    r235.TextColor3 = Color3.fromRGB(255, 165, 0);
                end;
            else
                r236.TextColor3 = Color3.fromRGB(200, 200, 200);
                if r235 then
                    r235.Text = "Low lag risk";
                    r235.TextColor3 = Color3.fromRGB(200, 200, 200);
                end;
            end;
        end;
    end; 
end;
local function r238(...)
    if PacketLag.loopThread then
        task.cancel(PacketLag.loopThread);
        PacketLag.loopThread = nil;
    end;
    PacketLag.enabled = false;
    if r235 then
        r235.Text = "Stopped";
        r235.TextColor3 = Color3.fromRGB(50, 255, 50);
    end;
    solarisui:Notify({
        ["Title"] = "Packet Lag",
        ["Content"] = "Disabled",
        ["Duration"] = 2
    });
    task.delay(2, function(...)
        if r235 then
            r237();
        end;
        return; 
    end); 
end;
local function r239(...)
    PacketLag.enabled = true;
    if r235 then
        r235.Text = "STARTING...";
        r235.TextColor3 = Color3.fromRGB(50, 255, 50);
    end;
    PacketLag.loopThread = task.spawn(function()
        local grabEvents = r25.ReplicatedStorage:WaitForChild("GrabEvents");
        local extendGrabLine = grabEvents:WaitForChild("ExtendGrabLine");
        if not extendGrabLine then
            warn("[Packet Lag] ExtendGrabLine RemoteEvent not found!");
            r238();
            return;
        end;
        if r235 then
            r235.Text = "LAGGING SERVER...";
            r235.TextColor3 = Color3.fromRGB(50, 255, 50);
        end;
        solarisui:Notify({
            ["Title"] = "Packet Lag",
            ["Content"] = "Enabled - Packet Size: " .. PacketLag.packetSize,
            ["Duration"] = 3
        });
        r44();
        while PacketLag.enabled do
            extendGrabLine:FireServer(string.rep("❤️❤️❤️❤️❤️😘😘😘😘😍😍😍😍❤️❤️❤️❤️❤️😘😘😘😘😍😍😍😍❤️❤️❤️❤️❤️😘😘😘😘😍😍😍😍❤️❤❤", math.floor(PacketLag.packetSize / 10)));
            print("[Packet Lag] Sent packet size: " .. PacketLag.packetSize);
            task.wait(3e-05); 
        end; 
    end); 
end;
local r240 = v87:CreateSlider({
    ["Name"] = "Packet Size",
    ["Min"] = 100,
    ["Max"] = 1000000,
    ["Default"] = 50000,
    ["Callback"] = function(arg1_111)
        PacketLag.packetSize = arg1_111;
        r237(); 
    end
});
task.spawn(function()
    task.wait(0.5);
    local k = r240:GetUI();
    if k then
        local o = k.GetChildren[1];
        for SoundService, game in pairs(N(o)) do
            local r240 = game:IsA("TextLabel");
            if r240 then
                local Text = v2.Text;
                if Text:find("Size:") then
                    local r236 = game;
                else
                    Text = v2.Text;
                    if v2.Name == "ValueLabel" or v2.Name == "Value" then
                        local r236 = game;
                    end;
                end;
            end; 
        end;
        if not r236 then
            local textLabel = Instance.new("TextLabel");
            textLabel.Size = UDim2.new(1, -20, 0, 20);
            textLabel.Position = UDim2.new(0, 10, 0, 70);
            textLabel.BackgroundTransparency = 1;
            textLabel.Text = "Size: " .. PacketLag.packetSize;
            textLabel.Font = Enum.Font.Gotham;
            textLabel.TextSize = 12;
            textLabel.TextXAlignment = Enum.TextXAlignment.Center;
            textLabel.TextColor3 = Color3.fromRGB(200, 200, 200);
            textLabel.Parent = list3;
        end;
        local textLabel = Instance.new("TextLabel");
        textLabel.Size = UDim2.new(1, -20, 0, 20);
        textLabel.Position = UDim2.new(0, 10, 0, 90);
        textLabel.BackgroundTransparency = 1;
        textLabel.Text = "Low lag risk";
        textLabel.Font = Enum.Font.GothamBold;
        textLabel.TextSize = 11;
        textLabel.TextXAlignment = Enum.TextXAlignment.Center;
        textLabel.TextColor3 = Color3.fromRGB(200, 200, 200);
        textLabel.Parent = k;
        r237();
    end; 
end);
v87:CreateToggle({
    ["Name"] = "Packet Lag",
    ["Default"] = false,
    ["Callback"] = function(arg1_112)
        if arg1_112 then
            r239();
        else
            r238();
        end; 
    end
});
v87:CreateSection("Line Lag");
v87:CreateToggle({
    ["Name"] = "Line Lag (Server Lag)",
    ["Default"] = false,
    ["Callback"] = function(arg1_113, ...)
        if arg1_113 then
            ServerLag.enabled = true;
            do
                if ServerLag.enabled then
                    local grabEvents = r25.ReplicatedStorage:WaitForChild("GrabEvents");
                    local createGrabLine = grabEvents:WaitForChild("CreateGrabLine");
                    local v74 = not createGrabLine;
                    if v74 then
                        warn("[Server Lag] CreateGrabLine RemoteEvent not found!");
                        ServerLag.enabled = false;
                        solarisui:Notify({
                            ["Title"] = "Server Lag",
                            ["Content"] = "Error: CreateGrabLine not found!",
                            ["Duration"] = 3
                        });
                        return;
                    end;
                    print("[Server Lag] Started!");
                    ServerLag.loopThread = task.spawn(function()
                        while ServerLag["enabled"] do
                             
                        end; 
                    end);
                    solarisui:Notify({
                        ["Title"] = "Server Lag",
                        ["Content"] = "Enabled - Lagging server with lines",
                        ["Duration"] = 3
                    });
                    r44();
                end; 
            end;
        else
            ServerLag.enabled = false;
            if ServerLag.loopThread then
                task.cancel(ServerLag.loopThread);
                ServerLag.loopThread = nil;
            end;
            solarisui:Notify({
                ["Title"] = "Server Lag",
                ["Content"] = "Disabled",
                ["Duration"] = 2
            });
            print("[Server Lag] Stopped");
            return;
        end; 
    end
});
return;
