local Players = game:GetService("Players");
local Workspace = game:GetService("Workspace");
local ReplicatedStorage = game:GetService("ReplicatedStorage");
local ContextActionService = game:GetService("ContextActionService");
local RunService = game:GetService("RunService");
local TeleportService = game:GetService("TeleportService");
local LocalPlayer = Players.LocalPlayer;

local superStrengthConnection;
local struggleConnection;
local isHeldConnection;
local characterConnection;
local isGrabbing = false;
local grabValid = false;
local grabData;
local grabDistance = 3;
local charHead;
local camera;
local grabBeamAttachment;
local remoteEvents = {};

local function cleanupFirePart()
    local character = LocalPlayer.Character;
    if not character then
        return;
    end
    local humanoid = character:WaitForChild("Humanoid", 5);
    local rootPart = character:WaitForChild("HumanoidRootPart", 5);
    if humanoid then
        local firePart = rootPart:FindFirstChild("FirePlayerPart");
        if firePart then
            firePart:Destroy();
        end
    end
end

LocalPlayer.CharacterAdded:Connect(cleanupFirePart);

local WindUI = loadstring(game:HttpGet("https://raw.githubusercontent.com/Footagesus/WindUI/main/dist/main.lua"))();

local Window = WindUI:CreateWindow({
    ["Title"] = "Pluh Hub",
    ["Icon"] = "globe",
    ["Author"] = "Fling Things And People",
    ["Folder"] = "ftappluhhub",
    ["Theme"] = "Indigo"
});

Window:Tag({
    ["Title"] = "Beta Library"
});

Window:Tag({
    ["Title"] = "Alpha"
});

Window:ToggleTransparency(true);
WindUI.TransparencyValue = 0.2;

local mainSection = Window:Section({
    ["Title"] = "Main"
});

local combatTab = mainSection:Tab({
    ["Title"] = "Combat"
});

local protectiveTab = mainSection:Tab({
    ["Title"] = "Protective"
});

local customLineTab = mainSection:Tab({
    ["Title"] = "Custom Line"
});

local miscTab = mainSection:Tab({
    ["Title"] = "Misc"
});

combatTab:Slider({
    ["Title"] = "Fling Strength",
    ["Desc"] = "Adjust the power of the fling",
    ["Value"] = {
        ["Min"] = 0,
        ["Max"] = 10000,
        ["Default"] = 400
    },
    ["Callback"] = function(flingStrength)

end
});

combatTab:Slider({
    ["Title"] = "Fling Time",
    ["Desc"] = "Adjust the duration of the fling effect",
    ["Value"] = {
        ["Min"] = 0,
        ["Max"] = 500,
        ["Default"] = 1
    },
    ["Callback"] = function(flingDuration)
    end
});

combatTab:Toggle({
    ["Title"] = "Super Strength",
    ["Value"] = false,
    ["Callback"] = function(enabled)
        if enabled then
            Workspace = game:GetService("Workspace");
        else
            if superStrengthConnection then
                superStrengthConnection:Disconnect();
            end
            return;
        end
    end
});

combatTab:Divider();

combatTab:Paragraph({
    ["Title"] = "more soon"
});

protectiveTab:Toggle({
    ["Title"] = "Anti Grab",
    ["Value"] = false,
    ["Callback"] = function(enabled)
        if enabled then
            local result = {
                pcall(function()
                    return ReplicatedStorage:WaitForChild("CharacterEvents", 5);
                end)
            };
            local characterEvents = result[2];

            if not pcall(function()
                return ReplicatedStorage:WaitForChild("CharacterEvents", 5);
            end) or not characterEvents then
                return;
            end

            local struggleEvent = characterEvents:WaitForChild("Struggle", 5);
            local isHeldValue = LocalPlayer:WaitForChild("IsHeld", 5);

            if not (struggleEvent and isHeldValue) then
                return;
            end

            local character = LocalPlayer.Character;
            if character then
                local humanoid = character:WaitForChild("Humanoid", 5);
                local rootPart = character:WaitForChild("HumanoidRootPart", 5);
                if not (humanoid and rootPart) then
                    return;
                end
                cleanupFirePart();
                return;
            else
                LocalPlayer.CharacterAdded:Wait();
            end
        else
            if struggleConnection then
                struggleConnection:Disconnect();
            end
            if isHeldConnection then
                isHeldConnection:Disconnect();
            end
            if characterConnection then
                characterConnection:Disconnect();
            end
        end
    end
});

customLineTab:Button({
    ["Title"] = "Further Extend",
    ["Callback"] = function()
        local playerScripts = LocalPlayer:WaitForChild("PlayerScripts");
        local casModule = require(playerScripts:WaitForChild("CASButtonModule"));
        local playerGui = LocalPlayer:WaitForChild("PlayerGui");

        local crosshairs = playerGui:WaitForChild("Crosshairs");
        crosshairs.Enabled = true;

        local character = LocalPlayer.Character;
        if character then
            local charHumanoid = character:WaitForChild("Humanoid");
            charHead = character:WaitForChild("Head");
            camera = Workspace.CurrentCamera;
            camera.CameraType = Enum.CameraType.Custom;

            LocalPlayer.CharacterAdded:Connect(function(newCharacter)
                charHead = newCharacter:WaitForChild("Head");
                grabBeamAttachment.Parent = charHead;
            end);

            grabBeamAttachment = Instance.new("Attachment");
            grabBeamAttachment.Name = "GrabBeamAttachment";
            grabBeamAttachment.Position = Vector3.new(0, -2, -1);
            grabBeamAttachment.Parent = charHead;

            local function performRaycast()
                local rayParams = RaycastParams.new();
                rayParams.FilterDescendantsInstances = { character };
                rayParams.FilterType = Enum.RaycastFilterType.Exclude;

                local rayResult = Workspace:Raycast(
                    camera.CFrame.Position,
                    camera.CFrame.LookVector * 1000,
                    rayParams
                );

                if rayResult then
                    return rayResult, CFrame.new(rayResult.Position, rayResult.Position + rayResult.Normal);
                end
                return nil, nil;
            end

            local function setGrabDistance(newDistance)
                grabDistance = math.clamp(newDistance, 1, 100);
            end

            local function endGrab(reason)
                isGrabbing = false;
                grabValid = false;
                if grabData then
                    grabData = nil;
                end
            end

            local function updateGrabVisual()
                if isGrabbing and grabData then
                    if grabValid then
                        local targetPosition = camera.CFrame.Position + camera.CFrame.LookVector * grabDistance;
                        grabData.DragPart.Position = targetPosition;

                        local attachmentWorldPos = grabBeamAttachment.WorldPosition;
                        local grabAttachWorldPos = grabData.GrabPart.GrabAttach.WorldPosition;

                        grabData.BeamPart.CFrame = CFrame.new(attachmentWorldPos, grabAttachWorldPos);
                        local beamDistance = (attachmentWorldPos - grabAttachWorldPos).Magnitude;

                        grabData.BeamPart.GrabBeam.CurveSize0 = 0;
                        grabData.BeamPart.GrabBeam.CurveSize1 = beamDistance * 0.3;
                        grabData.GrabPart.BeamSound.PlaybackSpeed = math.clamp(beamDistance / 2 + 2, 0.5, 4);
                    else
                        endGrab("Throw");
                    end
                end
            end

            local function onZoomInput(actionName, inputState, inputObject)
                if not isGrabbing then
                    return;
                end
                if inputObject.UserInputType == Enum.UserInputType.MouseWheel then
                    if inputObject.Position.Z > 0 then
                        setGrabDistance(grabDistance + inputObject.Position.Z * 5);
                    elseif inputObject.Position.Z < 0 then
                        setGrabDistance(grabDistance + inputObject.Position.Z * 5);
                    end
                end
            end

            local function onGrabAction(actionName, inputState, inputObject)

            end

            ContextActionService:BindAction("Grab", onGrabAction, true, Enum.KeyCode.E);
            ContextActionService:BindAction("ZoomPC", onZoomInput, true, Enum.UserInputType.MouseWheel);

            RunService.Stepped:Connect(function()
                if not isGrabbing then
                    local rayResult = performRaycast();
                    if rayResult then
                        casModule.PlaceButton("Grab", onGrabAction);
                        local remote = remoteEvents[character];
                        if remote then
                            remote:Fire("GrabControls", true);
                        end
                    else
                        local remote = remoteEvents[character];
                        if remote then
                            remote:Fire("GrabControls", false);
                        end
                        casModule.GrayOutButton("Grab", true);
                        casModule.ChangeColor("Grab", Color3.new(0, 1, 1));
                    end
                end
            end);

            charHumanoid.Died:Connect(function()
                endGrab("Throw");
            end);
        end
    end
});

customLineTab:Button({
    ["Title"] = "Rejoin",
    ["Callback"] = function()
        coroutine.resume(coroutine.create(function()
            local success, errorMessage = pcall(function()
                TeleportService:Teleport(game.PlaceId, LocalPlayer);
            end);
            if errorMessage then
                warn(errorMessage);
            end
        end));
    end
});

customLineTab:Paragraph({
    ["Title"] = "You can't disable the further extend the only option is to rejoin"
});

customLineTab:Paragraph({
    ["Title"] = "Also! further extend is in beta. expect glitches please dont press the further extend button too much. or else you will have multiple grabs which is breaking the further extend"
});

customLineTab:Paragraph({
    ["Title"] = "We didnt skid the blitz hub further extend so yep. this method is made by us"
});

miscTab:Paragraph({
    ["Title"] = "to use zero-g object you must grab a object then press T to zerogravitify it (only mass less objects)"
});

miscTab:Toggle({
    ["Title"] = "Zero-Gravity Object",
    ["Value"] = false,
    ["Callback"] = function(enabled)
        if enabled then

        else
            if superStrengthConnection then
                superStrengthConnection:Disconnect();
            end
            return;
        end
    end
});

miscTab:Paragraph({
    ["Title"] = "to make ballsocket anchor goto the combat tab then you must make fling time to max then make the fling strength 0 and nice you can now ballsocket anchor any object (any mass objects)"
});

return;
