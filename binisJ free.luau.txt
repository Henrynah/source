if workspace:FindFirstChild("Boiyoubuiltlikeanigger") and workspace.Boiyoubuiltlikeanigger.Value then
	game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(255, 0, 255)\">BinisJ Already Started Nigga</font>")
else
	local bjjjj = Instance.new("BoolValue")
	bjjjj.Name = "Boiyoubuiltlikeanigger"
	bjjjj.Parent = workspace
	bjjjj.Value = true
	local bjlist = {}
	local MasterPlayer = game.Players.LocalPlayer
	game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(255, 0, 255)\">[BinisJ]</font><font color=\"rgb(255, 255, 255)\"> Initiating the EDITION</font>")

	local joinland = {}
	local currantplrlist = {}

	local dick3 = Instance.new("Folder")
	dick3.Parent = game.workspace
	dick3.Name = "BinisCreations"

	for _,plr in pairs(game.Players:GetChildren()) do
		if plr ~= game.Players.LocalPlayer then
			if not plr:FindFirstChild("BinisJ") then
				local b = Instance.new("Folder")
				b.Name = "BinisJ"
				b.Parent = plr

				local ba = Instance.new("BoolValue")
				ba.Name = "OwnershipTP"
				ba.Parent = b

				local bb = Instance.new("Vector3Value")
				bb.Name = "OwnershipLocation"
				bb.Parent = b

				local bc = Instance.new("BoolValue")
				bc.Name = "ESP"
				bc.Parent = b

				local bd = Instance.new("BoolValue")
				bd.Name = "Blobkick"
				bd.Parent = b

				local be = Instance.new("BoolValue")
				be.Name = "LoopKill"
				be.Parent = b

				local bf = Instance.new("BoolValue")
				bf.Name = "Penis"
				bf.Parent = b

				local bg = Instance.new("BoolValue")
				bg.Name = "AntiKick"
				bg.Parent = b

				local bh = Instance.new("BoolValue")
				bh.Name = "OwnershipKick"
				bh.Parent = b

				local bi = Instance.new("BoolValue")
				bi.Name = "BJValue"
				bi.Parent = b
				bi.Value = false
			end

			table.insert(joinland,plr.UserId)
			table.insert(joinland,plr.Name)
			table.insert(joinland,plr.DisplayName)
			table.insert(currantplrlist,plr.Name)
		end
	end

	local BS
	local buildnum = 1
	local frozenlist = {}

	function addanchitm(list,model)
		local pp1 = model:FindFirstChild("pp1")
		for _,prt in pairs(list) do
			if prt and prt.Parent then
				model:FindFirstChildWhichIsA("IntValue").Value += 1
				if prt:FindFirstChild("somebodyshotaloadintomyburger2") then
					prt.somebodyshotaloadintomyburger2:Destroy()
				end
				for _,prrt in pairs(prt:GetChildren()) do
					if prrt:FindFirstChild("somebodyshotaloadintomyburger") then
						prrt.somebodyshotaloadintomyburger:Destroy()
					end
				end
				local bool
				if prt:FindFirstChildWhichIsA("Humanoid") then
					bool = false
				else
					bool = true
				end
				prt.Archivable = true
				local dbcooper = prt:Clone()
				local gottheblicky = Instance.new("Highlight")
				gottheblicky.Name = "somebodyshotaloadintomyburger"
				gottheblicky.FillTransparency = 1
				gottheblicky.OutlineTransparency = 1
				gottheblicky.FillColor = Color3.fromRGB(125,125,125)
				local gottheblicky2 = Instance.new("Highlight")
				gottheblicky2.Name = "somebodyshotaloadintomyburger2"
				gottheblicky2.FillTransparency = 1
				gottheblicky2.OutlineTransparency = 1
				gottheblicky2.FillColor = Color3.fromRGB(125,125,125)
				dbcooper.Parent = model
				local wc = Instance.new("WeldConstraint")
				wc.Name = 'diddyconstraint'
				wc.Parent = dbcooper
				wc.Part0 = dbcooper.PrimaryPart
				wc.Part1 = pp1
				local a1 = Instance.new("Attachment")
				local a2 = Instance.new("Attachment")
				local pp = Instance.new("AlignPosition")
				local pp2 = Instance.new("AlignOrientation")
				pp.Parent = prt.PrimaryPart
				pp2.Parent = prt.PrimaryPart
				a1.Parent = prt.PrimaryPart
				a2.Parent = dbcooper.PrimaryPart
					if bool then
					pp.Attachment0 = a1
					pp2.Attachment0 = a1
					pp.Attachment1 = a2
					pp2.Attachment1 = a2
				else
					pp.Attachment0 = a2
					pp2.Attachment0 = a2
					pp.Attachment1 = a1
					pp2.Attachment1 = a1
				end
				pp.Responsiveness = 100
				pp.MaxForce = 10^39
				pp2.Responsiveness = 20
				pp2.MaxTorque = 10^39
				pp.MaxVelocity = 1000
				if dbcooper:FindFirstChildWhichIsA("VehicleSeat") then
					dbcooper.VehicleSeat:Destroy()
				end
				if bool then
					for _,ting in pairs(prt:GetDescendants()) do
						if ting:IsA("BasePart") then
							ting.CanCollide = false
							ting:SetAttribute("CanQuery",ting.CanQuery)
							ting.CanQuery = false
							local ph = ting.Transparency
							ting:SetAttribute("Transparency",ph)
							ting.Transparency = 1
						end
					end
				else
					for _,ting in pairs(dbcooper:GetDescendants()) do
						if ting:IsA("BasePart") then
							ting.CanCollide = false
							ting.CanQuery = false
							ting.Transparency = 1
						end
					end
				end
				if bool then
					task.spawn(function()
						while prt and dbcooper and dbcooper.PrimaryPart and prt.PrimaryPart do
							local biddy = (dbcooper.PrimaryPart.CFrame.Position - prt.PrimaryPart.CFrame.Position).Magnitude
							local trans = 0
							if biddy - 20 > 0 then
								trans = (biddy - 20) / 10
							end
							if trans > 1 then trans = 1 end
							if trans == 0 then
								gottheblicky.Parent = nil
								gottheblicky2.Parent = nil
							else
								gottheblicky.Parent = dbcooper
								gottheblicky2.Parent = prt
								gottheblicky.FillTransparency = 1 - (trans * 0.5)
								gottheblicky.OutlineTransparency = 1 - trans
								gottheblicky2.FillTransparency = 1 - (trans * 0.5)
								gottheblicky2.OutlineTransparency = 1 - trans
							end
							for _,prt in pairs(prt:GetChildren()) do
								if prt:IsA("BasePart") then
									prt.Transparency = 1 - ((1 - prt:GetAttribute("Transparency")) * trans)
									if trans ~= 0 then
										prt.CanQuery = prt:GetAttribute("CanQuery")
									else
										prt.CanQuery = false
									end
								end
							end
							task.wait()
						end
					end)
				end
				local p = false
				prt:GetPropertyChangedSignal("Parent"):Connect(function()
					if not prt or prt and not prt.Parent then
						dbcooper:Destroy()
						if not p then
							p = true
							model:FindFirstChildWhichIsA("IntValue").Value -= 1
						end
					end
				end)
				dbcooper:GetPropertyChangedSignal("Parent"):Connect(function()
					if not dbcooper or dbcooper and not dbcooper.Parent then
						if not p then
							p = true
							model:FindFirstChildWhichIsA("IntValue").Value -= 1
						end
						for _,prrt in pairs(prt:GetDescendants()) do
							if prrt:IsA("BasePart") then
								prrt.Transparency = prrt:GetAttribute("Transparency") or 0
								prrt.CanCollide = true
							end
						end
					end
				end)
				local avgpos = Vector3.new(0,0,0)
				local count = 0
				for _,prt in pairs(model:GetChildren()) do
					if prt.ClassName == "Model" and prt.PrimaryPart then
						avgpos += prt.PrimaryPart.CFrame.Position
						count += 1
					end
				end
				for _,lprt in pairs(model:GetChildren()) do
					if lprt.ClassName == "Model" then
						if lprt:FindFirstChild("diddyconstraint") then
							lprt.diddyconstraint.Part1 = nil
						end
					end
				end
				pp1.CFrame = CFrame.new(avgpos / Vector3.new(count,count,count))
				for _,lprt in pairs(model:GetChildren()) do
					if lprt.ClassName == "Model" then
						if lprt:FindFirstChild("diddyconstraint") then
							lprt.diddyconstraint.Part1 = pp1
						end
					end
				end
			end
		end
	end

	game.ReplicatedStorage.GrabEvents.ExtendGrabLine.OnClientEvent:Connect(function(plr,arg1)
		if type(arg1) == "table" and arg1[1] == "BinisJ" then
			if plr ~= game.Players.LocalPlayer then
				if arg1[2] == "Starting" then
					game.ReplicatedStorage.GrabEvents.ExtendGrabLine:FireServer({"BinisJ","NotStarting"})
					if not plr.BinisJ.BJValue.Value then
						game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(255, 0, 255)\">[BinisJ] "..plr.DisplayName.." (@"..plr.Name..") is a good boy</font>")
						plr.BinisJ.BJValue.Value = true
					end
				end
				if arg1[2] == "NotStarting" then
					if not plr.BinisJ.BJValue.Value then
						game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(255, 0, 255)\">[BinisJ] "..plr.DisplayName.." (@"..plr.Name..") is a good boy</font>")
						plr.BinisJ.BJValue.Value = true
					end
				end
			end
			if arg1[2] == "Print" then
				game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage(arg1[3])
			end
			if arg1[2] == "New Build" then
				BS:AddSection({
					Name = "Binisj Creation"
				})
				local dibbly = BS:AddLabel("Total Items In Build: 0")
				local buildtotal = 0
				local bn = buildnum
				local model = Instance.new("Model")
				model.Parent = dick3
				model.Name = "BinisJModel"..bn
				local pp1 = Instance.new("Part")
				pp1.Parent = model
				model.PrimaryPart = pp1
				pp1.Name = "pp1"
				pp1.CFrame = CFrame.new(0,0,0)
				pp1.Transparency = 1
				pp1.CanCollide = false
				pp1.CanQuery = false
				pp1.Massless = true
				local aura = Instance.new("IntValue")
				aura.Value = 0
				aura.Parent = model
				aura:GetPropertyChangedSignal("Value"):Connect(function()
					dibbly:Set("Total Items In Build: "..aura.Value)
				end)
				local aa1 = BS:AddButton({
					Name = "Add Anchored Items To Build",
					Callback = function()
						addanchitm(frozenlist,model)
						local boobyjr = {}
						for _,itm in pairs(frozenlist) do
							local itmprt = itm:FindFirstChild("HumanoidRootPart") or itm:FindFirstChild("SoundPart")
							table.insert(boobyjr,{itm,pp1.CFrame:Inverse() * itmprt.CFrame})
						end
						game.ReplicatedStorage.GrabEvents.ExtendGrabLine:FireServer({"BinisJ","Set "..bn,boobyjr})
						frozenlist = {}
					end
				})
				local sizer = 1
				local agentJEW = BS:AddSlider({
					Name = "Set Build Size",
					Min = 0.1,
					Max = 50,
					Default = 1,
					Color = Color3.fromRGB(255,255,255),
					Increment = 0.1,
					ValueName = "Sped Shed",
					Callback = function(Value)
						model:ScaleTo(Value)
					end
				})
				BS:AddButton({
					Name = "Set size to 1",
					Callback = function()
						agentJEW:Set(1)
					end
				})
				local aa2 = BS:AddToggle({
					Name = "Highlight Build",
					Default = false,
					Callback = function(Value)
						if Value then
							local randall = Instance.new("Highlight")
							randall.Parent = model
							randall.Name = "somebodyshotaloadintomyburger2"
							randall.FillTransparency = 0.4
							randall.FillColor = Color3.fromRGB(27,126,208)
							randall.OutlineColor = Color3.fromRGB(21,97,161)
							
							local bbg = Instance.new("BillboardGui")
							bbg.Name = "somebodyshotaloadintomyburger"
							bbg.Parent = pp1
							bbg.Size = UDim2.new(0, 100, 0, 100)
							bbg.SizeOffset = Vector2.new(0,0.25)
							bbg.StudsOffset = Vector3.new(0,3,0)
							bbg.Adornee = pp1

							local bbt = Instance.new("TextLabel")
							bbt.Parent = bbg
							bbt.Size = UDim2.new(0, 100, 0, 100)
							bbt.BackgroundTransparency = 1
							bbt.TextColor3 = Color3.new(1,1,1)
							bbt.TextSize = 20
							bbt.Text = "BinisJ Model "..bn
							bbt.TextStrokeColor3 = Color3.new(1,1,1)
							bbt.TextStrokeTransparency = 0.75
						else
							if model:FindFirstChild("somebodyshotaloadintomyburger2") then
								model.somebodyshotaloadintomyburger2:Destroy()
							end
							if pp1:FindFirstChild("somebodyshotaloadintomyburger") then
								pp1.somebodyshotaloadintomyburger:Destroy()
							end
						end
					end,
				})
				local aa3 = BS:AddButton({
					Name = "Dismantle Build",
					Callback = function()
						for _,prt in pairs(model:GetChildren()) do
							if prt.ClassName == "Model" then
								prt:Destroy()
							end
						end
					end,
				})
				game.ReplicatedStorage.GrabEvents.ExtendGrabLine.OnClientEvent:Connect(function(plr,arg1)
					if type(arg1) == "table" and arg1[1] == "BinisJ" and plr ~= game.Players.LocalPlayer then
						if arg1[2] == "Set "..bn then
							for line,itm in pairs(arg1[3]) do
								local idontflippingknowbro = itm[1]:FindFirstChild("HumanoidRootPart") or itm[1]:FindFirstChild("SoundPart")
								idontflippingknowbro.CFrame = pp1.CFrame * itm[2]
							end
							addanchitm(arg1[3],model) 
						elseif arg1[2] == "Resize "..bn then
							agentJEW:Set(arg1[3])
						elseif arg1[2] == "Dismantle "..bn then
							for _,prt in pairs(model:GetChildren()) do
								if prt.ClassName == "Model" then
									prt:Destroy()
								end
							end
						end
					end
				end)
				buildnum += 1
			end
		end
	end)

	game.ReplicatedStorage.GrabEvents.ExtendGrabLine:FireServer({"BinisJ","Starting"})

	getgenv().gethui = function() return game.CoreGui end
	local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/jensonhirst/Orion/main/source')))()

	lodaded = false

	local tpland = {
		Spawn = Vector3.new(0,-7.35,0),
		SpawnCave = Vector3.new(-90,14.6,-314.3),
		GreenHouse = Vector3.new(-538,-7,74),
		PinkHouse = Vector3.new(-478,-7,-147),
		Barn = Vector3.new(-228,82,-318),
		BlueHouse = Vector3.new(496,83,-350),
		ChineseHouse = Vector3.new(542,123,-93),
		PurpleHouse = Vector3.new(270,-7,448),
		Factory = Vector3.new(134,347,352),
		OtherGreenHouse = Vector3.new(-359,98,357),
		BigCave = Vector3.new(-245,80,485),
		GoodPrison = Vector3.new(569.6,-7,176.3),
		RuhubsDogAhhPrison = Vector3.new(564,82.5,210),
		ExtremelyGoodPrison = Vector3.new(525,76,56),
		TrainCave = Vector3.new(536.6,87.5,-169.5),
		IslanCave = Vector3.new(75.8,323,368.5),
		ChineseRoof = Vector3.new(592,153,-100),
		UfoCave = Vector3.new(29.6,10.5,-225.8),
		Prison = Vector3.new(195,-7,-561),
		BlueHouseSlot = Vector3.new(562.2,85.38,-212.56),
		SpawnSlot = Vector3.new(51.75,-5.3,-121.64),
		HauntedSlot = Vector3.new(164.57,-5.43,530.97),
		RandomSlot = Vector3.new(-211.65,85.7,426.72),
		BeachSlot = Vector3.new(-546.97,-5.3,-41.09)
	}

	local locc = tpland["Prison"]
	local loccc = "Prison"

	local SL = tpland["Spawn"]

	local mapp = game:GetService("Workspace").Map:Clone()
	mapp.AlwaysHereTweenedObjects:Destroy()
	mapp.MapNoises:Destroy()

	local plrinplot
	if not workspace:FindFirstChild("Players In Plots") then
		plrinplot = Instance.new("Folder", game:getService("Workspace"))
		plrinplot.Name = "Players In Plots"
	else
		plrinplot = workspace:FindFirstChild("Players In Plots")
	end

	local dick2 = Instance.new("Folder")
	dick2.Parent = game.workspace
	dick2.Name = "BinisHoles"

	game.Players.LocalPlayer.CameraMaxZoomDistance = 100000

	for _,prt in pairs(game.workspace:GetChildren()) do
		if prt.Name == "GrabParts" then
			prt.Name = "OfflineGrabParts"
		end
	end

	function Boner(itm)
		if not itm:FindFirstChild("BonerPart") then
			itm:WaitForChild("Center").Transparency = 1
			itm:WaitForChild("SoundPart"):WaitForChild("BillboardGui"):WaitForChild("ImageLabel")Visible = false
			itm.SoundPart:WaitForChild("Attachment"):WaitForChild("PointLight").Enabled = false
			itm.SoundPart.Attachment:WaitForChild("ParticleEmitter").Enabled = false
			itm.SoundPart.Size = Vector3.new(10,10,10)
			itm.SoundPart.Shape = "Block"
			local a = Instance.new("ManualWeld")
			local b = Instance.new("Part")
			b.Parent = itm
			b.CanQuery = false
			b.CanCollide = false
			b.CanTouch = false
			b.Size = Vector3.new(10,10,10)
			b.Name = "BonerPart"
			b.Anchored = false
			b.Massless = true

			a.Parent = b
			a.Name = "BonerGlue"
			a.Part0 = b
			a.Part1 = itm.Center

			local aa = Instance.new("Decal")
			aa.Texture = "rbxassetid://120974828567943"
			aa.Name = "DaveBoner"
			aa.Parent = b
				
			local ab = Instance.new("Decal")
			ab.Texture = "rbxassetid://120974828567943"
			ab.Name = "DaveBoner"
			ab.Face = "Back"
			ab.Parent = b

			local ac = Instance.new("Decal")
			ac.Texture = "rbxassetid://120974828567943"
			ac.Name = "DaveBoner"
			ac.Face = "Top"
			ac.Parent = b
				
			local ad = Instance.new("Decal")
			ad.Texture = "rbxassetid://120974828567943"
			ad.Name = "DaveBoner"
			ad.Face = "Bottom"
			ad.Parent = b

			local ae = Instance.new("Decal")
			ae.Texture = "rbxassetid://120974828567943"
			ae.Name = "DaveBoner"
			ae.Face = "Left"
			ae.Parent = b
				
			local af = Instance.new("Decal")
			af.Texture = "rbxassetid://120974828567943"
			af.Name = "DaveBoner"
			af.Face = "Right"
			af.Parent = b
		end
	end

	function UnBoner(itm)
		if itm:FindFirstChild("BonerPart") then
			itm.Center.Transparency = 0
			itm.SoundPart.BillboardGui.ImageLabel.Visible = true
			itm.SoundPart.Attachment.PointLight.Enabled = true
			itm.SoundPart.Attachment.ParticleEmitter.Enabled = true
			itm.SoundPart.Size = Vector3.new(3,3,3)
			itm.SoundPart.Shape = "Block"
			itm.BonerPart:Destroy()
		end
	end

	for _,prt in pairs(game:GetService("Workspace").Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel:GetChildren()) do 
		if prt:IsA("Part") and prt.Name == "Ocean" then 
			prt.Color = Color3.fromRGB(2,65,98)
			prt.CollisionGroup = "Items"
			prt.CanCollide = true
			prt.CanQuery = false
		end
	end

	for _,prt in pairs(game:GetService("Workspace").Plots:GetDescendants()) do
		if prt.Name == "PlotArea" or prt.Name == "PlotBarrier" then
			prt.CanQuery = false
			prt.CanCollide = false
		elseif prt.Name == "FalseBorder" then
			prt.CanQuery = true
			prt.CanCollide = true
		end 
	end

	local kickland = {}
	local dplist = {}
	local uslist = {}
	local usidlist = {}
	local poslist = {}
	local gettingkicked = {}

	local WS = 1
	local SS = 1

	local WSE = false

	local bonerenabled = false

	local ESPA = false

	local totalkicked = 0
	local TK

	local EspList = {}

	local flying = false

	function UNESPP(plr)
		task.spawn(function()
			if plr.Character then
				for _,ting in pairs(plr.Character:GetChildren()) do
					if ting.Name == "Highlight" or ting.Name == "BillboardGui"then
						ting:Destroy()
					end
				end
			end
		end)
	end

	function ESPP(plr)
		task.spawn(function()
			if plr.Character then
				local randall = Instance.new("Highlight")
				randall.Parent = plr.Character
				randall.FillTransparency = 0.4
				randall.FillColor = Color3.fromRGB(125,125,125)

				local hrp = plr.Character:WaitForChild("HumanoidRootPart",1)

				if hrp then
					local bbg = Instance.new("BillboardGui")
					bbg.Parent = plr.Character
					bbg.Size = UDim2.new(0, 100, 0, 100)
					bbg.SizeOffset = Vector2.new(0,0.25)
					bbg.StudsOffset = Vector3.new(0,3,0)
					bbg.AlwaysOnTop = true
					bbg.Adornee = plr.Character.HumanoidRootPart

					local bbt = Instance.new("TextLabel")
					bbt.Parent = bbg
					bbt.Size = UDim2.new(0, 100, 0, 100)
					bbt.BackgroundTransparency = 1
					bbt.TextColor3 = Color3.new(1,1,1)
					bbt.TextSize = 20
					bbt.Text = plr.DisplayName
					bbt.TextStrokeColor3 = Color3.new(1,1,1)
					bbt.TextStrokeTransparency = 0.75

					local bbt2 = Instance.new("TextLabel")
					bbt2.Parent = bbg
					bbt2.Size = UDim2.new(0, 100, 0, 100)
					bbt2.BackgroundTransparency = 1
					bbt2.TextColor3 = Color3.new(1,1,1)
					bbt2.TextSize = 12
					bbt2.Text = "(@"..plr.Name..")"
					bbt2.Position = UDim2.new(0,0,0,25)
					bbt2.TextStrokeColor3 = Color3.new(1,1,1)
					bbt2.TextStrokeTransparency = 0.75

					local bbl = Instance.new("ImageLabel")
					bbl.Parent = bbg
					bbl.BackgroundTransparency = 1
					bbl.Position = UDim2.new(0,0,-0.75,0)
					bbl.Size = UDim2.new(0,100,0,100)
					bbl.Image = game.Players:GetUserThumbnailAsync(plr.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size420x420)
				end
			end
		end)
	end

	local AXE = false

	local AGE = false

	game.workspace.ChildAdded:Connect(function(model)
		if model.Name == "BlackHoleKick" then
			totalkicked += 1
			TK:Set("Total Players Kicked: "..totalkicked)

			local hole = model:WaitForChild("Hole")

			local diddler
			local sttttrrrrrrringggggggg = "Some Unknown's BlackHole"

			task.spawn(function()
				task.wait(1)

				local starttime = tick()
				
				diddler = model:Clone()
				diddler.Parent = dick2
				diddler.Name = sttttrrrrrrringggggggg

				if not VOBBC then
					diddler.Hole.Transparency = 1
					diddler.Hole.BillboardGui.Enabled = false
				end

				while task.wait() do
					diddler.Hole.BillboardGui.Large.Rotation = (tick() - starttime) * 150
					diddler.Hole.BillboardGui.Small.Rotation = (tick() - starttime) * 150
				end
			end)

			local solved = false
			while not solved do
				if model then
					for _,plr in pairs(game.Players:GetChildren()) do
						if plr.Character then
							if plr.Character:FindFirstChild("HumanoidRootPart") then
								if plr.Character.HumanoidRootPart.Anchored == true then
									solved = true
									local alrflagged = false
									for _,plrr in pairs(gettingkicked) do
										if plrr == plr.UserId then
											alrflagged = true
										end
									end
									if not alrflagged then
										sttttrrrrrrringggggggg = plr.Name.."'s BlackHole"
										table.insert(usidlist,plr.UserId)
										table.insert(uslist,plr.Name)
										table.insert(dplist,plr.DisplayName)
										table.insert(poslist,math.floor(hole.CFrame.Position.X).."  "..math.floor(hole.CFrame.Position.Y).."  "..math.floor(hole.CFrame.Position.Z))
										local hpp = plr.Character.HumanoidRootPart.CFrame.Position
										local bhk = model.Hole.CFrame.Position
										local distance = Vector3.new((hpp.X-bhk.X)^2,(hpp.Y-bhk.Y)^2,(hpp.Z-bhk.Z)^2)
										local byplayer = false
										if math.sqrt(distance.X+distance.Y+distance.Z) > 2 then
											table.insert(kickland,true)
											byplayer = true
										else
											table.insert(kickland,false)
										end
										local H = plr.UserId
										table.insert(gettingkicked,H)
										if byplayer then
											game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(255,0,0)\">"..plr.DisplayName.." (@"..plr.Name..") has been kicked by a player at position ("..math.floor(model.Hole.CFrame.Position.X).."  "..math.floor(model.Hole.CFrame.Position.Y).."  "..math.floor(model.Hole.CFrame.Position.Z)..")</font>")
											OrionLib:MakeNotification({
												Name = "Kickland",
												Content = plr.DisplayName.." (@"..plr.Name..") has been kicked by a player at position ("..math.floor(model.Hole.CFrame.Position.X).."  "..math.floor(model.Hole.CFrame.Position.Y).."  "..math.floor(model.Hole.CFrame.Position.Z)..")",
												Image = "rbxassetid://4483345998",
												Time = 2
											})
										else
											game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(255,0,0)\">"..plr.DisplayName.." (@"..plr.Name..") has been kicked at position ("..math.floor(model.Hole.CFrame.Position.X).."  "..math.floor(model.Hole.CFrame.Position.Y).."  "..math.floor(model.Hole.CFrame.Position.Z)..")</font>")
											OrionLib:MakeNotification({
												Name = "Kickland",
												Content = plr.DisplayName.." (@"..plr.Name..") has been kicked at position ("..math.floor(model.Hole.CFrame.Position.X).."  "..math.floor(model.Hole.CFrame.Position.Y).."  "..math.floor(model.Hole.CFrame.Position.Z)..")",
												Image = "rbxassetid://4483345998",
												Time = 2
											})
										end
										task.wait(10)
										for line,var in pairs(gettingkicked) do
											if var == H then
												table.remove(gettingkicked,line)
											end
										end
									end
								end
							end
						end
					end
				else
					game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(255,0,0)\">Unable to check who was kicked</font>")
					solved = true
				end
				task.wait()
			end
		elseif model.Name == "GrabParts" and game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and not model:WaitForChild("GrabPart"):WaitForChild("WeldConstraint").Part1:FindFirstAncestor("Map") and not model.GrabPart.WeldConstraint.Part1:FindFirstAncestor("Plots") and not model.GrabPart.WeldConstraint.Part1:FindFirstAncestor("Slots") then
			for _,prt in pairs(model.GrabPart.WeldConstraint.Part1.Parent:GetDescendants()) do
				if prt:IsA("BasePart") then
					prt.AssemblyLinearVelocity = game.Players.LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity
					local cg = prt.CollisionGroup
					local j = prt:GetPropertyChangedSignal("CollisionGroup"):Connect(function()
						prt.CollisionGroup = game.Players.LocalPlayer.Character.HumanoidRootPart.CollisionGroup
					end)
					prt.CollisionGroup = game.Players.LocalPlayer.Character.HumanoidRootPart.CollisionGroup
					task.spawn(function()
						while game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and workspace:FindFirstChild("GrabParts") do task.wait() end
						j:Disconnect()
						prt.CollisionGroup = cg
					end)
				end
			end
		end
	end)

	local penis
	local penis2

	local DSL = true

	if game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
		penis = game.Players.LocalPlayer.Character.Humanoid:GetPropertyChangedSignal("Sit"):Connect(function()
			if not game.Players.LocalPlayer.Character.Humanoid.SeatPart and AXE then
				game.Players.LocalPlayer.Character.Humanoid.Sit = false
			end
		end)
		penis2 = game.Players.LocalPlayer.Character.Humanoid:GetPropertyChangedSignal("WalkSpeed"):Connect(function()
			local nigger = 1
			if flying then nigger = 10 end
			game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = WS * 16 * nigger
		end)
		game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = WS
	end

	game.Players.LocalPlayer.CharacterAdded:Connect(function()
		local b = game.Players.LocalPlayer.Character:WaitForChild("Humanoid")
		penis = b:GetPropertyChangedSignal("Sit"):Connect(function()
			if not game.Players.LocalPlayer.Character.Humanoid.SeatPart and AXE then
				game.Players.LocalPlayer.Character.Humanoid.Sit = false
			end
		end)
		penis2 = b:GetPropertyChangedSignal("WalkSpeed"):Connect(function()
			local nigger = 1
			if flying then nigger = 10 end
			game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = WS * 16 * nigger
		end)
		b.WalkSpeed = WS
		if DSL then
			game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart").CFrame = CFrame.new(SL)
		end
	end)

	task.spawn(function()
		while true do
			for _,plr in pairs(game.Players:GetChildren()) do
				if plr.Character and plr ~= game.Players.LocalPlayer then
					for _,prt in pairs(plr.Character:GetChildren()) do
						if prt:IsA("BasePart") then
							prt.CollisionGroup = "PlotPlayers"
							if prt:FindFirstChild("RagdollLimbPart") then
								prt.RagdollLimbPart.CollisionGroup = "PlotPlayers"
							end
						end
					end
				elseif plr.Character and plr == game.Players.LocalPlayer then
					for _,prt in pairs(plr.Character:GetChildren()) do
						if prt:IsA("BasePart") then
							prt.CollisionGroup = "Players"
							if prt:FindFirstChild("RagdollLimbPart") then
								prt.RagdollLimbPart.CollisionGroup = "Players"
							end
						end
					end
				end
			end
			task.wait()
		end
	end)

	local pOTP
	local pLK
	local pOL
	local coord
	local pBK
	local pESP
	local pPenis
	local pAK
	local pOK
	local Changing = false
	local sellplr

	local OTPList = {}
	local BlobkickList = {"BarnoDDino","ay3vx"}
	local LocList = {}
	local LoopKillList = {}
	local AKList = {}
	local OKList = {}
	local PenisList = {}

	local OKA = false
	local OWF = true
	local OKLA = false
	local OKLWF = true
	local BKA = false
	local BKAWL = true

	local plrlist
	local plrlistsel
	local WLL
	local WLlistsel

	game:GetService("Players").ChildAdded:Connect(function(plr)
		workspace:WaitForChild(plr.Name.."SpawnedInToys").ChildAdded:Connect(function(model)
			model:SetAttribute("Bumass", 1)
			if model.Name == "CreatureBlobman" then
				if WSE then
					model:WaitForChild("HumanoidCreature").WalkSpeed = WS * 20
				end
			elseif model.Name == "ToolPencil" then
				local bill = model:WaitForChild("SoundPart")
				local prt = Instance.new("Part")
				prt.Name = "PenisPart"
				prt.Parent = model
				prt.CFrame = bill.CFrame + (bill.CFrame.RightVector * 2.193)
				prt.Rotation += Vector3.new(0, 0, 270)
				prt.Transparency = 1
				prt.CanCollide = false
				prt.CanQuery = false
				prt.CanTouch = false
				prt.Size = Vector3.new(0.5,0.5,0.5)

				local wc = Instance.new("WeldConstraint")
				wc.Parent = prt
				wc.Part0 = bill
				wc.Part1 = prt

				local pe = Instance.new("ParticleEmitter")
				pe.Parent = prt
				pe.SpreadAngle = Vector2.new(10,10)
				pe.Texture = "rbxassetid://120974828567943"
				pe.Rate = 100
				pe.Speed = NumberRange.new(25,25)
				pe.Lifetime = NumberRange.new(0.25,0.5)
				local numberSequence = NumberSequence.new{
					NumberSequenceKeypoint.new(0, 0),
					NumberSequenceKeypoint.new(1, 1),
				}
				pe.Transparency = numberSequence
				numberSequence = NumberSequence.new{
					NumberSequenceKeypoint.new(0, 0.5),
					NumberSequenceKeypoint.new(1, 0.5),
				}
				pe.Size = numberSequence

				for _,prt in pairs(model:GetChildren()) do
					if prt:IsA("BasePart") then
						prt.CanQuery = false
						prt.CanTouch = false
					end
				end
			elseif model.Name == "NinjaKunai" then
				local bbg = Instance.new("BillboardGui")
				bbg.Parent = model
				bbg.Size = UDim2.new(1, 0, 1, 0)
				bbg.Adornee = model

				local bbl = Instance.new("ImageLabel")
				bbl.Parent = bbg
				bbl.BackgroundTransparency = 1
				bbl.Size = UDim2.new(1, 0, 1, 0)
				bbl.Position = UDim2.new(0, 0, 0, 0)
				bbl.AnchorPoint = Vector2.new(0.5, 0.5)
				bbl.Position = UDim2.new(0.5, 0, 0.5, 0)

				local constraint = Instance.new("UIAspectRatioConstraint")
				constraint.AspectRatio = 1
				constraint.Parent = bbl
				bbl.Image = 'rbxassetid://120974828567943'

				task.wait()

				for _,prt in pairs(model:GetChildren()) do
					if prt:IsA("BasePart") then
						prt.CanQuery = false
						prt.CanTouch = false
						prt.Transparency = 1
					end
				end
			elseif model.Name == "BallMagicLight" and bonerenabled then
				Boner(model)
			elseif model.Name == "BombMissile" or model.Name == "BallSnowball" or model.Name == "BombDarkMatter" or model.Name == "BombBalloon" then
				task.wait()
				for _,prt in pairs(model:GetChildren()) do
					if prt:IsA("BasePart") then
						prt.CollisionGroup = "PlotItems"
					end
				end
			end
		end)

		plr.CharacterAdded:Connect(function()
			if plr.BinisJ.ESP.Value or ESPA then
				ESPP(plr)
			end
		end)

		currantplrlist = {}
		for _,plr in pairs(game.Players:GetChildren()) do
			if plr ~= game.Players.LocalPlayer then
				table.insert(currantplrlist,plr.Name)
			end
		end

		local b = Instance.new("Folder")
		b.Name = "BinisJ"
		b.Parent = plr

		local ba = Instance.new("BoolValue")
		ba.Name = "OwnershipTP"
		ba.Parent = b

		for _,plrr in pairs(OTPList) do
			if plr.Name == plrr then
				ba.Value = true
			end
		end

		local bb = Instance.new("Vector3Value")
		bb.Name = "OwnershipLocation"
		bb.Parent = b

		local bc = Instance.new("BoolValue")
		bc.Name = "ESP"
		bc.Parent = b

		for _,plrr in pairs(EspList) do
			if plr.Name == plrr then
				bc.Value = true
			end
		end

		local bd = Instance.new("BoolValue")
		bd.Name = "Blobkick"
		bd.Parent = b

		for _,plrr in pairs(BlobkickList) do
			if plr.Name == plrr then
				bd.Value = true
			end
		end
		
		local be = Instance.new("BoolValue")
		be.Name = "LoopKill"
		be.Parent = b

		for _,plrr in pairs(LoopKillList) do
			if plr.Name == plrr then
				be.Value = true
			end
		end

		local bf = Instance.new("BoolValue")
		bf.Name = "Penis"
		bf.Parent = b

		for _,plrr in pairs(PenisList) do
			if plr.Name == plrr then
				bf.Value = true
			end
		end

		local bg = Instance.new("BoolValue")
		bg.Name = "AntiKick"
		bg.Parent = b

		for _,plrr in pairs(AKList) do
			if plr.Name == plrr then
				bg.Value = true
			end
		end

		local bh = Instance.new("BoolValue")
		bh.Name = "OwnershipKick"
		bh.Parent = b

		for _,plrr in pairs(OKList) do
			if plr.Name == plrr then
				bh.Value = true
			end
		end

		local bi = Instance.new("BoolValue")
		bi.Name = "BJValue"
		bi.Parent = b
		bi.Value = false

		if loaded then
			plrlist:Refresh(currantplrlist,true)
			plrlist:Set(plrlistsel)
			WLL:Refresh(currantplrlist,true)
			WLL:Set(WLlistsel)
		end

		task.wait()
		local beenbefore = false
		local kickedbefore = false
		for _,itm in pairs(kickland) do
			if itm == plr.UserId then
				kickedbefore = true
			end
		end
		for _,itm in pairs(joinland) do
			if itm == plr.UserId then
				beenbefore = true
			end
		end
		if plr:IsFriendsWith(game:GetService("Players").LocalPlayer.UserId) then
			if beenbefore then
				diduel = "Your friend "..plr.DisplayName.." (@"..plr.Name..") has rejoined",
				game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(150,150,150)\">"..plr.DisplayName.." (@"..plr.Name..") has rejoined</font>")
			else
				table.insert(joinland,plr.UserId)
				table.insert(joinland,plr.Name)
				table.insert(joinland,plr.DisplayName)
				diduel = plr.DisplayName.." (@"..plr.Name..") has joined the game",
				game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(150,150,150)\">Your friend "..plr.DisplayName.." (@"..plr.Name..") has joined the game</font>")
			end	
		elseif kickedbefore then
			diduel = plr.DisplayName.." (@"..plr.Name..") has rejoined",
			game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(255,0,0)\">"..plr.DisplayName.." (@"..plr.Name..") has rejoined</font>")
		else
			if beenbefore then
				diduel = plr.DisplayName.." (@"..plr.Name..") has rejoined",
				game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(150,150,150)\">"..plr.DisplayName.." (@"..plr.Name..") has rejoined</font>")
			else
				table.insert(joinland,plr.UserId)
				table.insert(joinland,plr.Name)
				table.insert(joinland,plr.DisplayName)
				diduel = plr.DisplayName.." (@"..plr.Name..") has joined the game",
				game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(150,150,150)\">"..plr.DisplayName.." (@"..plr.Name..") has joined the game</font>")
			end	
		end
		OrionLib:MakeNotification({
			Name = "Join Logger",
			Content = diduel,
			Image = "rbxassetid://4483345998",
			Time = 2
		})
	end)

	for _,plr in pairs(game.Players:GetPlayers()) do
		plr.CharacterAdded:Connect(function()
			if plr ~= game.Players.LocalPlayer then
				if plr.BinisJ.ESP.Value or ESPA then
					ESPP(plr)
				end
			end
		end)
		workspace:WaitForChild(plr.Name.."SpawnedInToys").ChildAdded:Connect(function(model)
			model:SetAttribute("Bumass", 1)
			if model.Name == "CreatureBlobman" then
				model:WaitForChild("HumanoidCreature").WalkSpeed = WS * 20
			elseif model.Name == "ToolPencil" then
				local bill = model:WaitForChild("SoundPart")
				local prt = Instance.new("Part")
				prt.Name = "PenisPart"
				prt.Parent = model
				prt.CFrame = bill.CFrame + (bill.CFrame.RightVector * 2.193)
				prt.Rotation += Vector3.new(0, 0, 270)
				prt.Transparency = 1
				prt.CanCollide = false
				prt.CanQuery = false
				prt.CanTouch = false
				prt.Size = Vector3.new(0.5,0.5,0.5)

				local wc = Instance.new("WeldConstraint")
				wc.Parent = prt
				wc.Part0 = bill
				wc.Part1 = prt

				local pe = Instance.new("ParticleEmitter")
				pe.Parent = prt
				pe.SpreadAngle = Vector2.new(10,10)
				pe.Texture = "rbxassetid://120974828567943"
				pe.Rate = 100
				pe.Speed = NumberRange.new(25,25)
				pe.Lifetime = NumberRange.new(0.25,0.5)
				local numberSequence = NumberSequence.new{
					NumberSequenceKeypoint.new(0, 0),
					NumberSequenceKeypoint.new(1, 1),
				}
				pe.Transparency = numberSequence
				numberSequence = NumberSequence.new{
					NumberSequenceKeypoint.new(0, 0.5),
					NumberSequenceKeypoint.new(1, 0.5),
				}
				pe.Size = numberSequence

				for _,prt in pairs(model:GetChildren()) do
					if prt:IsA("BasePart") then
						prt.CanQuery = false
						prt.CanTouch = false
					end
				end
			elseif model.Name == "NinjaKunai" then
				local bbg = Instance.new("BillboardGui")
				bbg.Parent = model
				bbg.Size = UDim2.new(1, 0, 1, 0)
				bbg.Adornee = model

				local bbl = Instance.new("ImageLabel")
				bbl.Parent = bbg
				bbl.BackgroundTransparency = 1
				bbl.Size = UDim2.new(1, 0, 1, 0)
				bbl.Position = UDim2.new(0, 0, 0, 0)
				bbl.AnchorPoint = Vector2.new(0.5, 0.5)
				bbl.Position = UDim2.new(0.5, 0, 0.5, 0)

				local constraint = Instance.new("UIAspectRatioConstraint")
				constraint.AspectRatio = 1
				constraint.Parent = bbl
				bbl.Image = 'rbxassetid://120974828567943'

				task.wait()

				for _,prt in pairs(model:GetChildren()) do
					if prt:IsA("BasePart") then
						prt.CanQuery = false
						prt.CanTouch = false
						prt.Transparency = 1
					end
				end
			elseif model.Name == "BallMagicLight" and bonerenabled then
				Boner(model)
			elseif model.Name == "BombMissile" or model.Name == "BallSnowball" or model.Name == "BombDarkMatter" or model.Name == "BombBalloon" then
				task.wait()
				for _,prt in pairs(model:GetChildren()) do
					if prt:IsA("BasePart") then
						prt.CollisionGroup = "PlotItems"
					end
				end
			end
		end)
		for _,model in pairs(workspace:FindFirstChild(plr.Name.."SpawnedInToys")) do
			if model:IsA("Model") then
				model:SetAttribute("Bumass", 1)
			end
		end
	end

	game:GetService("Players").ChildRemoved:Connect(function(plr)
		currantplrlist = {}
		local masterplr = game.Players.LocalPlayer
		for _,plr in pairs(game.Players:GetChildren()) do
			if plr ~= game.Players.LocalPlayer then
				table.insert(currantplrlist,plr.Name)
			end
			if table.find(bjlist,plr.Name) then
				local msplr = game.Players.LocalPlayer
				local msnum = 0
				for _,plr in pairs(game.Players:GetPlayers()) do
					if plr ~= game.Players.LocalPlayer and plr.BinisJ.BJValue.Value and plr.AccountAge > msnum then
						msplr = plr
						msnum = plr.AccountAge
					end
				end
				if MasterPlayer ~= msplr then
					game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(200,200,200)\">"..plr.DisplayName.." (@"..plr.Name..") is the muscle tustler</font>")
					MasterPlayer = msplr
				end
			end
		end
		if loaded then
			plrlist:Refresh(currantplrlist,true)
			plrlist:Set(plrlistsel)
		end
		local alrflagged = false
		for _,plrr in pairs(gettingkicked) do
			if plrr == plr.UserId then
				alrflagged = true
			end
		end
		if not alrflagged then
			game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(150,150,150)\">"..plr.DisplayName.." (@"..plr.Name..") has left the game</font>")
			OrionLib:MakeNotification({
				Name = "Join Logger",
				Content = plr.DisplayName.." (@"..plr.Name..") has left the game",
				Image = "rbxassetid://4483345998",
				Time = 2
			})
		end
	end)

	locations = {"Spawn","Prison","ChineseRoof","SpawnCave","GreenHouse","PinkHouse","Barn","BlueHouse","ChineseHouse","PurpleHouse","Factory","OtherGreenHouse","BigCave","GoodPrison","RuhubsDogAhhPrison","ExtremelyGoodPrison","TrainCave","IslandCave","UfoCave","BlueHouseSlot","SpawnSlot","HauntedSlot","RandomSlot","BeachSlot"}

	local Window = OrionLib:MakeWindow({Name = "BinisJ - Nigga Edition", IntroText = "BinisJ - Nigga Edition"})

	local GS = Window:MakeTab({
		Name = "Grab Stuff",
		Icon = "rbxassetid://4483362458",
	})

	GS:AddSection({
		Name = "My muscles are getting bigger!"
	})

	GS:AddToggle({
		Name = "Boi dont tussle with the muscle",
		Default = true,
		Callback = function(Value)
			BMA = Value
			if Value then
				game.ReplicatedFirst.GrabParts.DragPart.AlignPosition.MaxForce = math.huge
				game.ReplicatedFirst.GrabParts.DragPart.AlignOrientation.MaxTorque = math.huge
				if workspace:FindFirstChild("GrabParts") then
					if workspace.GrabParts:FindFirstChild("DragPart") then
						if workspace.GrabParts.DragPart:FindFirstChild("AlignOrientation") then
							workspace.GrabParts.DragPart.AlignOrientation.MaxTorque = math.huge
						end
						if workspace.GrabParts.DragPart:FindFirstChild("AlignPosition") then
							workspace.GrabParts.DragPart.AlignPosition.MaxForce = math.huge
						end
					end
				end
			else
				game.ReplicatedFirst.GrabParts.DragPart.AlignPosition.MaxForce = 60000
				game.ReplicatedFirst.GrabParts.DragPart.AlignOrientation.MaxTorque = 60000
				if workspace:FindFirstChild("GrabParts") then
					if workspace.GrabParts:FindFirstChild("DragPart") then
						if workspace.GrabParts.DragPart:FindFirstChild("AlignOrientation") then
							workspace.GrabParts.DragPart.AlignOrientation.MaxTorque = 60000
						end
						if workspace.GrabParts.DragPart:FindFirstChild("AlignPosition") then
							workspace.GrabParts.DragPart.AlignPosition.MaxForce = 60000
						end
					end
				end
			end
		end   
	})

	GS:AddSection({
		Name = "Teleport Items"
	})

	GS:AddDropdown({
		Name = "Dropdown",
		Default = "Prison",
		Options = locations,
		Callback = function(Value)
			locc = tpland[Value]
			loccc = Value
		end    
	})

	prisonactive = false

	GS:AddToggle({
		Name = "Activate Send To Location",
		Default = false,
		Callback = function(Value)
			prisonactive = Value
		end
	})

	GS:AddBind({
		Name = "Send To Location",
		Default = Enum.KeyCode.K,
		Hold = false,
		Callback = function()
			if prisonactive then
				if workspace:FindFirstChild("GrabParts") then
					p1 = game:GetService("Workspace"):FindFirstChild("GrabParts")
					if p1:FindFirstChild("GrabPart") then
						p2 = p1.GrabPart
						if p2:FindFirstChild("WeldConstraint") then
							p3 = p2.WeldConstraint
							if p3.Part1 then
								b = p3.Part1
								if not b:FindFirstAncestor("Plots") and not b:FindFirstAncestor("Slots") and not b:FindFirstAncestor("Map") then
									OrionLib:MakeNotification({
										Name = b.Parent.Name.." was sent to "..loccc,
										Content = "Teleportation",
										Image = "rbxassetid://4483345998",
										Time = 2
									})
									game.ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(b,b.CFrame)
									for _,prt in pairs(b.Parent:GetChildren()) do
										if prt:IsA("BasePart") then
											task.spawn(function()
												cc = prt.CanCollide
												prt.CanCollide = false
												local a = Instance.new("Attachment")
												a.Parent = prt
												local ap = Instance.new("AlignPosition")
												ap.Parent = a
												ap.Attachment0 = a
												ap.Mode = "OneAttachment"
												ap.MaxForce = math.huge
												ap.MaxVelocity = math.huge
												ap.Responsiveness = 200
												ap.Position = locc
												task.wait(2)
												prt.CanCollide = cc
												a:Destroy()
											end)
										end
									end
								end
							end
						end
					end
				end
			end
		end
	})

	GS:AddButton({
		Name = "Go to location",
		Callback = function()
			if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
				game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(locc)

				OrionLib:MakeNotification({
					Name = "Teleported to "..loccc,
					Content = "Teleportation",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			end
		end    
	})

	local PT = Window:MakeTab({
		Name = "Player Tab",
		Icon = "rbxassetid://4483362458",
	})

	PT:AddSection({
		Name = "Walk-Speed"
	})

	local gotitdownPAT = PT:AddSlider({
		Name = "Set Walk-Speed",
		Min = 0.1,
		Max = 100,
		Default = 1,
		Color = Color3.fromRGB(255,255,255),
		Increment = 0.1,
		ValueName = "Speed",
		Callback = function(Value)
			WS = Value
			if Value then
				if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
					game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = WS * 16
				end
				for _,plot in pairs(workspace.PlotItems:GetChildren()) do
					if plot.Name ~= "PlayersInPlots" then
						for _,itm in pairs(plot:GetChildren()) do
							if itm.Name == "CreatureBlobman" then
								if itm:FindFirstChild("HumanoidCreature") then
									itm.HumanoidCreature.WalkSpeed = WS * 20
								end
							end
						end
					end
				end
				for _,plr in pairs(game.Players:GetChildren()) do
					if workspace:FindFirstChild(plr.Name.."SpawnedInToys") then
						for _,itm in pairs(workspace:FindFirstChild(plr.Name.."SpawnedInToys"):GetChildren()) do
							if itm.Name == "CreatureBlobman" then
								if itm:FindFirstChild("HumanoidCreature") then
									itm.HumanoidCreature.WalkSpeed = WS * 20
								end
							end
						end
					end
				end
			end
		end
	})

	PT:AddButton({
		Name = "Set Walk-Speed To 1",
		Callback = function()
			gotitdownPAT:Set(1)
		end    
	})

	PT:AddSection({
		Name = "Third Person"
	})

	local tpon = false

	PT:AddToggle({
		Name = "Third Person",
		Default = true,
		Callback = function(Value)
			tpon = Value
			task.spawn(function()
				while tpon do
					game.UserInputService.MouseIconEnabled = true
					game.Players.LocalPlayer.CameraMode = "Classic"
					if game.Players.LocalPlayer.Character then
						for _,prt in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
							if prt:IsA("BasePart") and prt.Name ~= "HumanoidRootPart" then
								prt.Transparency = 0
							elseif prt.ClassName == "Accessory" and prt.Name ~= "TypingKeyboardMyWorld" and prt:FindFirstChild("Handle") then
								prt.Handle.Transparency = 0
							end
						end
					end
					task.wait()
				end
			end)
			game.Players.LocalPlayer.CameraMode = "LockFirstPerson"
		end,
	})

	local already = false
	local AKT
	local APT
	local AKE
	local APKE

	local already2 = false

	local holdingspace = false

	task.spawn(function()
		while true do
			if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
				game.Players.LocalPlayer.Character.Humanoid.JumpHeight = 0
				if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") and game.Players.LocalPlayer.Character.Humanoid.SeatPart and game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == "CreatureBlobman" then
					game.Players.LocalPlayer.Character.Humanoid.UseJumpPower = false
					if game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent:FindFirstChild("HumanoidCreature") then
						if not flying then
							game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.HumanoidCreature.Jump = holdingspace
						end
					end
				elseif flying then
					game.Players.LocalPlayer.Character.Humanoid.UseJumpPower = false
				else
					game.Players.LocalPlayer.Character.Humanoid.UseJumpPower = true
				end
			end
			task.wait()
		end
	end)

	cloneref(game.Players.LocalPlayer:GetMouse()).KeyUp:Connect(function(KEYY)
		if KEYY == ' ' then
			holdingspace = false
		end
	end)

	cloneref(game.Players.LocalPlayer:GetMouse()).KeyDown:Connect(function(KEYY)
		if KEYY == ' ' then
			holdingspace = true
			if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") and game.Players.LocalPlayer.Character.Humanoid.Sit then
				if already2 then
					if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") and game.Players.LocalPlayer.Character.Humanoid.Sit then
						game.Players.LocalPlayer.Character.Humanoid.Sit = false
					end
				else
					already2 = true
					local st = tick()
					while tick() - st < 0.4 and game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") and game.Players.LocalPlayer.Character.Humanoid.Sit do task.wait() end
					already2 = false
				end
			end
		end
	end)

	local uppies = false
	local downies = false
	local Up
	local Down

	cloneref(game.Players.LocalPlayer:GetMouse()).KeyDown:Connect(function(KEYY)
		if KEYY == 'y' then
			flying = not flying
			if flying then
				already = false
				if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
					local Float = Instance.new('Part')
					Float.Name = 'CreativeFlyPart'
					Float.Parent = game.Players.LocalPlayer.Character
					Float.Size = Vector3.new(2,0.2,1.5)
					Float.Anchored = true
					Float.Transparency = 1
					local FloatValue = 3.1
					local Value = FloatValue + game.Players.LocalPlayer.Character.Humanoid.HipHeight
					Float.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame - Vector3.new(0,Value,0)
					Up = cloneref(game.Players.LocalPlayer:GetMouse()).KeyUp:Connect(function(KEY)
						if KEY == ' ' then
							uppies = false
						elseif KEY == 'c' then
							downies = false
						end
					end)
					Down = cloneref(game.Players.LocalPlayer:GetMouse()).KeyDown:Connect(function(KEY)
						if KEY == ' ' then
							uppies = true
						elseif KEY == 'c' then
							downies = true
						end
					end)
					local function FloatPadLoop()
						if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild('CreativeFlyPart') and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") and flying then
							if not AKE and not APKE then
								APT:Set(true)
							end
							game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = WS * 16 * 10
							Float.Transparency = 1
							FloatValue = 3.1
							if uppies then
								if not downies then
									FloatValue -= 2 + (game.Players.LocalPlayer.Character.Humanoid.HipHeight * 0.75)
									if game.Players.LocalPlayer.Character.Humanoid.SeatPart and game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == "CreatureBlobman" then FloatValue -= 0.35 end
								end
							elseif downies then
								if not uppies then
									FloatValue += 4
								end
							end
							if game.Players.LocalPlayer.Character.Humanoid.Sit then
								if game.Players.LocalPlayer.Character.Humanoid.SeatPart and game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == "CreatureBlobman" and game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent:FindFirstChild("HumanoidRootPart") and game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent:FindFirstChild("HumanoidCreature") then
									Float.CFrame = game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.HumanoidRootPart.CFrame - Vector3.new(0,(FloatValue * 2) + game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.HumanoidCreature.HipHeight - 4.4125,0)
									game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.HumanoidCreature.WalkSpeed = 16 * WS * 7.5
									Float.CanCollide = true
								else
									Float.CanCollide = false
								end
							else
								Float.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame - Vector3.new(0,FloatValue + game.Players.LocalPlayer.Character.Humanoid.HipHeight,0)
								Float.CanCollide = true
							end
						else
							flying = false
							FloatingFunc:Disconnect()
							Float:Destroy()
							Up:Disconnect()
							Down:Disconnect()
						end
					end
					FloatingFunc = game:GetService("RunService").Heartbeat:Connect(FloatPadLoop)
				end
			end
		elseif KEYY == ' ' then
			if flying then
				if already then
					if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") and game.Players.LocalPlayer.Character.Humanoid.SeatPart and game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == "CreatureBlobman" then
						return
					else
						already = false
						flying = false
					end
				else
					already = true
					local st = tick()
					while tick() - st < 0.4 and flying do task.wait() end
					if flying then
						already = false
					end
				end
			else
				if already then
					already = false
					flying = true
					if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
						local Float = Instance.new('Part')
						Float.Name = 'CreativeFlyPart'
						Float.Parent = game.Players.LocalPlayer.Character
						Float.Size = Vector3.new(2,0.2,1.5)
						Float.Anchored = true
						Float.Transparency = 1
						local FloatValue = 3.1
						local Value = FloatValue + game.Players.LocalPlayer.Character.Humanoid.HipHeight
						Float.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame - Vector3.new(0,Value,0)
						local uppies = false
						local downies = false
						Up = cloneref(game.Players.LocalPlayer:GetMouse()).KeyUp:Connect(function(KEY)
							if KEY == ' ' then
								uppies = false
							elseif KEY == 'c' then
								downies = false
							end
						end)
						Down = cloneref(game.Players.LocalPlayer:GetMouse()).KeyDown:Connect(function(KEY)
							if KEY == ' ' then
								uppies = true
							elseif KEY == 'c' then
								downies = true
							end
						end)
						local function FloatPadLoop()
							if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild('CreativeFlyPart') and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") and flying then
								if not AKE and not APKE then
									APT:Set(true)
								end
								game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = WS * 16 * 10
								Float.Transparency = 1
								FloatValue = 3.1
								if uppies then
									if not downies then
										FloatValue -= 2 + (game.Players.LocalPlayer.Character.Humanoid.HipHeight * 0.75)
										if game.Players.LocalPlayer.Character.Humanoid.SeatPart and game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent and game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == "CreatureBlobman" then FloatValue -= 0.35 end
									end
								elseif downies then
									if not uppies then
										FloatValue += 4
									end
								end
								if game.Players.LocalPlayer.Character.Humanoid.Sit then
									if game.Players.LocalPlayer.Character.Humanoid.SeatPart and game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == "CreatureBlobman" and game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent:FindFirstChild("HumanoidRootPart") and game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent:FindFirstChild("HumanoidCreature") then
										Float.CFrame = game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.HumanoidRootPart.CFrame - Vector3.new(0,(FloatValue * 2) + game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.HumanoidCreature.HipHeight - 4.4125,0)
										game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.HumanoidCreature.WalkSpeed = 16 * WS * 7.5
										Float.CanCollide = true
									else
										Float.CanCollide = false
									end
								else
									Float.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame - Vector3.new(0,FloatValue + game.Players.LocalPlayer.Character.Humanoid.HipHeight,0)
									Float.CanCollide = true
								end
							else
								flying = false
								FloatingFunc:Disconnect()
								Float:Destroy()
								Up:Disconnect()
								Down:Disconnect()
							end
						end
						FloatingFunc = game:GetService("RunService").Heartbeat:Connect(FloatPadLoop)
					end
				else
					already = true
					local st = tick()
					while tick() - st < 0.4 and not flying do task.wait() end
					if not flying then
						already = false
					end
				end
			end
		end
	end)

	local AS = Window:MakeTab({
		Name = "Anti-Shit",
		Icon = "rbxassetid://4483345998",
	})

	local AGE = false

	AS:AddToggle({
		Name = "Anti-Grab",
		Default = false,
		Callback = function(Value)
			AGE = Value
			if Value then
				OrionLib:MakeNotification({
					Name = "Anti-Grab on nigga",
					Content = "Yobro!",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			else
				OrionLib:MakeNotification({
					Name = "Anti-Grab on a little bit less nigga",
					Content = "See you latere",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			end
			while AGE do
				while game:GetService("Players").LocalPlayer.IsHeld.Value do
					game:GetService("ReplicatedStorage").CharacterEvents.Struggle:FireServer(game.Players.LocalPlayer)
					game.ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(game.Players.LocalPlayer.Character.HumanoidRootPart,0.00000000001)
					if game.Players.LocalPlayer.Character.Humanoid.Sit then
						game.Players.LocalPlayer.Character.Humanoid.Sit = false
					end
					task.wait()
				end
				task.wait()
			end
		end,
	})

	local antifirepart

	if game.workspace.Plots.Plot5.Barrier:FindFirstChild("AntiFirePart") then	
		antifirepart = game.workspace.Plots.Plot5.Barrier["AntiFirePart"]
	else
		antifirepart = game.workspace.Plots.Plot5.Barrier:FindFirstChild("PlotBarrier")
	end
	antifirepart.CanCollide = true
	antifirepart.CanQuery = true
	antifirepart.Name = "AntiFirePart"
	local h = antifirepart:Clone()
	h.Name = "FalseBorder"
	h.Parent = antifirepart.Parent
	antifirepart.Size = Vector3.new(1,1,1)
	for _,prt in pairs(antifirepart:GetChildren()) do
		prt:Destroy()
	end
	antifirepart.CanQuery = false
	antifirepart.CanCollide = false
	AFE = false

	AS:AddToggle({
		Name = "Anti-Fire",
		Default = false,
		Callback = function(Value)
			AFE = Value
			while AFE do
				if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
					antifirepart.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame
				end
				antifirepart.CanCollide = not antifirepart.CanCollide
				antifirepart.CanCollide = not antifirepart.CanCollide
				task.wait()
			end
			antifirepart.CFrame = CFrame.new(0,-15,0)
		end,
	})

	AS:AddToggle({
		Name = "Anti-Explosion",
		Default = false,
		Callback = function(Value)
			AXE = Value
		end,
	})

	AKT = AS:AddToggle({
		Name = "Anti-Kick",
		Default = false,
		Callback = function(Value)
			AKE = Value
			if Value then
				table.insert(AKList,game.Players.LocalPlayer.Name)
				APT:Set(false)
			else
				if workspace[game.Players.LocalPlayer.Name.."SpawnedInToys"]:FindFirstChild(game.Players.LocalPlayer.Name) then
					game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(workspace[game.Players.LocalPlayer.Name.."SpawnedInToys"][game.Players.LocalPlayer.Name])
				end
				for line,var in pairs(AKList) do
					if var == game.Players.LocalPlayer.Name then
						table.remove(AKList,line)
					end
				end
			end
		end,
	})

	APT = AS:AddToggle({
		Name = "Penis Anti-Kick",
		Default = true,
		Callback = function(Value)
			APKE = Value
			if Value then
				table.insert(PenisList,game.Players.LocalPlayer.Name)
				AKT:Set(false)
			else
				if workspace[game.Players.LocalPlayer.Name.."SpawnedInToys"]:FindFirstChild(game.Players.LocalPlayer.Name) then
					game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(workspace[game.Players.LocalPlayer.Name.."SpawnedInToys"][game.Players.LocalPlayer.Name])
				end
				for line,var in pairs(PenisList) do
					if var == game.Players.LocalPlayer.Name then
						table.remove(PenisList,line)
					end
				end
			end
		end,
	})

	local antiok = true

	AS:AddToggle({
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

	local antiblob = false

	AS:AddToggle({
		Name = "Antiblob",
		Default = true,
		Callback = function(Value)
			antiblob = Value
			task.spawn(function()
				while antiblob do
					if game.Players.LocalPlayer.Character then
						if not game.Players.LocalPlayer.Character:FindFirstChild("TruePositionPart") then
							local tp = Instance.new("Part")
							tp.Parent = game.Players.LocalPlayer.Character
							tp.Name = "TruePositionPart"
							tp.Anchored = true
							tp.CFrame = CFrame.new(0,-100,0)
						end
						for _,prt in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
							if prt:IsA("BasePart") then
								if prt.Massless then
									prt.Massless = false
								end
							end
							if prt.Name == "HumanoidRootPart" then
								if game.Players.LocalPlayer.Character.HumanoidRootPart:FindFirstChild("RootAttachment") then
									task.wait()
									task.wait()
									task.wait()
									task.wait()
									task.wait()
									task.wait()
									task.wait()
									task.wait()
									task.wait()
									task.wait()
									if game.Players.LocalPlayer.Character then
										if game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.Players.LocalPlayer.Character.HumanoidRootPart:FindFirstChild("RootAttachment") and game.Players.LocalPlayer.Character:FindFirstChild("TruePositionPart") then
											game.Players.LocalPlayer.Character.HumanoidRootPart.RootAttachment.Parent = game.Players.LocalPlayer.Character.TruePositionPart
											OrionLib:MakeNotification({
												Name = "Antiblob has been enabled",
												Content = "Antiblob",
												Image = "rbxassetid://4483345998",
												Time = 2
											})
										end
									end
								end
							end
						end
					end
					task.wait()
				end
			end)
			if not Value and game.Players.LocalPlayer.Character then
				if game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.Players.LocalPlayer.Character:FindFirstChild("TruePositionPart") then
					if game.Players.LocalPlayer.Character.TruePositionPart:FindFirstChild("RootAttachment") then
						game.Players.LocalPlayer.Character.TruePositionPart.RootAttachment.Parent = game.Players.LocalPlayer.Character.HumanoidRootPart
						OrionLib:MakeNotification({
							Name = "Antiblob has been disabled",
							Content = "Antiblob",
							Image = "rbxassetid://4483345998",
							Time = 2
						})
					end
				end
			end
		end,
	})

	local antibanana

	AS:AddToggle({
		Name = "No bananas allowed in these parts buddy old pal",
		Default = true,
		Callback = function(Value)
			antibanana = Value
			task.spawn(function()
				while antibanana do
					for _,plot in pairs(game.workspace.PlotItems:GetChildren()) do
						if plot.Name ~= "PlayersInPlot" then
							for _,model in pairs(plot:GetChildren()) do
								if model.Name == "FoodBanana" then
									if model:FindFirstChild("HoldPart") then
										if model.HoldPart:FindFirstChild("HoldItemRemoteFunction") then
											model.HoldPart.HoldItemRemoteFunction:InvokeServer(model,game:GetService("Players").LocalPlayer.Character)
											if model.HoldPart:FindFirstChild("DropItemRemoteFunction") then
												model.HoldPart.DropItemRemoteFunction:InvokeServer(model,CFrame.new(0,-10^40,0),Vector3.new(0,-46.05,0))
											end
										end
									end
								end
							end
						end
					end
					for _,plr in pairs(game.Players:GetChildren()) do
						if game.workspace:FindFirstChild(plr.Name.."SpawnedInToys") and plr ~= game.Players.LocalPlayer then
							for _,model in pairs(game.workspace:FindFirstChild(plr.Name.."SpawnedInToys"):GetChildren()) do
								if model.Name == "FoodBanana" then
									if model:FindFirstChild("HoldPart") then
										if model.HoldPart:FindFirstChild("HoldItemRemoteFunction") then
											model.HoldPart.HoldItemRemoteFunction:InvokeServer(model,game:GetService("Players").LocalPlayer.Character)
											if model.HoldPart:FindFirstChild("DropItemRemoteFunction") then
												model.HoldPart.DropItemRemoteFunction:InvokeServer(model,CFrame.new(0,-10^40,0),Vector3.new(0,-46.05,0))
											end
										end
									end
								end
							end
						end
					end
					task.wait()
				end
			end)
		end,
	})

	local antiboid

	AV = AS:AddToggle({
		Name = "Disable Void",
		Default = true,
		Callback = function(Value)
			antiboid = Value
			if Value then 
				workspace.FallenPartsDestroyHeight = 0/0
				OrionLib:MakeNotification({
					Name = "Void has been disabled",
					Content = "Anti-Void",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			else
				workspace.FallenPartsDestroyHeight = -100
				OrionLib:MakeNotification({
					Name = "Void has been enabled",
					Content = "Anti-Void",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			end
		end,
	})

	AS:AddToggle({
		Name = "Anti-Lag",
		Default = false,
		Callback = function(Value)
			if Value then 
				game:GetService("Players").LocalPlayer.PlayerScripts.CharacterAndBeamMove.Disabled = true
				for _,plr in pairs(game:GetService("Players"):GetChildren()) do
					if plr.Character:FindFirstChild("GrabParts") then
						plr.Character.GrabParts:Destroy()
					end
				end
				OrionLib:MakeNotification({
					Name = "Anti-Lag has ben enabled",
					Content = "Anti-Lag",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			else
				game:GetService("Players").LocalPlayer.PlayerScripts.CharacterAndBeamMove.Enabled = true
				OrionLib:MakeNotification({
					Name = "Anti-Lag has been disabled",
					Content = "Anti-lag",
					Image = "rbxassetid://4483345998",
					Time = 2
				})	
			end
		end    
	})

	local VTPE = false

	AS:AddToggle({
		Name = "View True Position",
		Default = false,
		Callback = function(Value)
			VTPE = Value
			while VTPE do
				for _,plr in pairs(game.Players:GetPlayers()) do
					if plr.Character then
						if not plr.Character:FindFirstChild("TruePositionPart") then
							local tp = Instance.new("Part")
							tp.Parent = plr.Character
							tp.Name = "TruePositionPart"
							tp.Anchored = true
							tp.CFrame = CFrame.new(0,-100,0)
						end
						if plr.Character:FindFirstChild("HumanoidRootPart") then
							if plr.Character.HumanoidRootPart:FindFirstChild("RootAttachment") and plr.Character:FindFirstChild("TruePositionPart") then
								plr.Character.HumanoidRootPart.RootAttachment.Parent = plr.Character.TruePositionPart
							end
							for _,prt in pairs(plr.Character:GetChildren()) do
								if prt:IsA("BasePart") and prt.Name ~= "HumanoidRootPart" then
									prt.Massless = false
								end
							end
						end
					end
				end
				task.wait()
			end
			if not Value then
				for _,plr in pairs(game.Players:GetChildren()) do
					if plr.Character then
						if plr.Character:FindFirstChild("TruePositionPart") and plr.Character:FindFirstChild("HumanoidRootPart") then
							if plr.Character.TruePositionPart:FindFirstChild("RootAttachment") then
								plr.Character.TruePositionPart.RootAttachment.Parent = plr.Character.HumanoidRootPart
							end
						end
					end
				end
			end
		end,
	})

	local RDAAG = false

	AS:AddToggle({
		Name = "Ruhubs dog ahh antigrab",
		Default = false,
		Callback = function(Value)
			RDAAG = Value
			if Value then
				OrionLib:MakeNotification({
					Name = "Ruhub Anti-Grab has been enabled",
					Content = "Buns ahh 💀",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			else
				OrionLib:MakeNotification({
					Name = "Ruhub Anti-Grab has been disabled",
					Content = "Buns ahh 💀",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			end
			while RDAAG do
				while game:GetService("Players").LocalPlayer.IsHeld.Value do
					game:GetService("ReplicatedStorage").CharacterEvents.Struggle:FireServer(plr)
					game.Players.LocalPlayer.Character.HumanoidRootPart.Anchored = true
					task.wait()
				end
				game.Players.LocalPlayer.Character.HumanoidRootPart.Anchored = false
				task.wait()
			end
		end,
	})

	local PL = Window:MakeTab({
		Name = "Players",
		Icon = "rbxassetid://4483345998",
	})

	PL:AddSection({
		Name = "Select Players"
	})

	function BlobKick()
		while true do
			local smegma = true
			local listtype = 1
			local gotsomeone = false
			local listtt = {}
			if BKA then
				listtype = 2
				for _,plr in pairs(game.Players:GetPlayers()) do
					if plr ~= game.Players.LocalPlayer then
						if BKAWL and not game.Players.LocalPlayer:IsFriendsWith(plr.UserId) then
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
				if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
					j = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame
					h = game.Players.LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity
				end
				if smegma and game.Players:FindFirstChild(plrr) and game.Players:FindFirstChild(plrr) ~= game.Players.LocalPlayer then
					local continue = false
					local plr = game.Players:FindFirstChild(plrr)
					if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
						if plr.Character.HumanoidRootPart.Massless then
							if plr.Character.Humanoid.SeatPart then
								continue = true
							end
						else
							continue = true
						end
					end
					if BKA and BKAWL and game.Players.LocalPlayer:IsFriendsWith(game.Players:FindFirstChild(plrr).UserId) then
						continue = false
					end
					while smegma and game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and plr and plr.Character and plr.Character.Parent == workspace and plr.Character:FindFirstChild("HumanoidRootPart") and continue do
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
						if continue and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") and game.Players.LocalPlayer.Character.Humanoid.SeatPart and game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent and game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == "CreatureBlobman" then
							local blob = game.Players.LocalPlayer.Character.Humanoid.SeatPart.Parent
							local localtime = tick()
							if (plr.Character.HumanoidRootPart.CFrame.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame.Position + game.Players.LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity).magnitude > 30 and plr.Character.Parent == workspace then
								game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = plr.Character:FindFirstChild("HumanoidRootPart").CFrame + (plr.Character:FindFirstChild("HumanoidRootPart").AssemblyLinearVelocity / math.pi)
								game.Players.LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = plr.Character:FindFirstChild("HumanoidRootPart").AssemblyLinearVelocity
								task.wait(0.15)
								if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
									game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(plr.Character.HumanoidRootPart,plr.Character.HumanoidRootPart.CFrame)
								end
							end
							local timee = tick()
							while tick() - timee < 0.25 and plr and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") and continue do
								game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(plr.Character.HumanoidRootPart,plr.Character.HumanoidRootPart.CFrame)
								task.wait(0.05)
							end
							if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and blob:FindFirstChild("RightDetector") and blob.RightDetector:FindFirstChild("RightWeld") and blob:FindFirstChild("BlobmanSeatAndOwnerScript") and blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab") then
								plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0,10^10,0)
								game.ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(plr.Character.HumanoidRootPart)
								blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(blob.RightDetector,plr.Character.HumanoidRootPart,blob.RightDetector.RightWeld)
							end
						elseif continue then
							if game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
								if game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") and game.Players.LocalPlayer.Character.Humanoid.SeatPart then
									game.Players.LocalPlayer.Character.Humanoid.Sit = false
								end
							end
							if blob and blob:FindFirstChild("VehicleSeat") then
								if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") and game.Players.LocalPlayer.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
									blob.VehicleSeat:Sit(game.Players.LocalPlayer.Character.Humanoid)
								end
								task.wait(0.1)
							else
								foundblob = false
								blob = nil
								for _,itm in pairs(workspace[game.Players.LocalPlayer.Name.."SpawnedInToys"]) do
									if itm.Name == "CreatureBlobman" and itm:FindFirstChild("VehicleSeat") then
										foundblob = true
										blob = itm
									elseif itm.Name == "CreatureBlobman" then
										while itm do
											game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(itm)
											task.wait(0.1)
										end
									end
								end
								if blob and player.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") and game.Players.LocalPlayer.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
									blob.VehicleSeat:Sit(game.Players.LocalPlayer.Character.Humanoid)
								end
								if not foundblob then
									task.wait(1)
									if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
										if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.Players.LocalPlayer.Character.Parent ~= workspace then
											game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(0,-10,0)
											game.Players.LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
										end
										while smegma and not blob do
											if listtype == 2 then
												if not BKA then
													smegma = false
												end
											end
											if workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys"):FindFirstChild("CreatureBlobman") then
												blob = workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").CreatureBlobman
											elseif game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.Players.LocalPlayer.Character.Parent ~= workspace then
												game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(0,-10,0)
												game.Players.LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
											end
											if not blob then
												task.spawn(function()
													game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("CreatureBlobman",CFrame.new(game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame.Position)+Vector3.new(0,0,15),Vector3.new(0,0,0))
												end)
											end
											task.wait()
										end
									end
								end
							end
						end
						task.wait(0.1)
					end
				end
				if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
					game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = j
					game.Players.LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = h
				end
			end
			if not gotsomeone and blob then
				while blob.Parent do
					game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(blob)
					task.wait(0.1)
				end
				blob = nil
			end
			task.wait()
		end
	end

	task.spawn(BlobKick)

	task.spawn(function()
		while true do
			local listtt = {}
			local loclisttt = {}
			if OKA or OKLA then
				for _,plr in pairs(game.Players:GetPlayers()) do
					if plr ~= game.Players.LocalPlayer then
						if OWF and not game.Players.LocalPlayer:IsFriendsWith(plr.UserId) then
							table.insert(listtt,plr.Name)
							table.insert(loclisttt,Vector3.new(0,10^38,0))
						elseif not OWF then
							table.insert(listtt,plr.Name)
							table.insert(loclisttt,Vector3.new(0,10^38,0))
						end
					end
				end
			else
				listtt = OTPList
				loclisttt = LocList
			end
			for line,plrr in pairs(listtt) do
				if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.Players:FindFirstChild(plrr) and loclisttt[line] ~= 'dunno' then
					plr = game.Players[plrr]
					local j = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame
					local p = game.Players.LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity
					local bbld = false
					if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") and plr ~= game.Players.LocalPlayer and plr.Character.Parent ~= workspace.PlotItems.PlayersInPlots and plr.Character.Parent ~= plrinplot and plr.Character.HumanoidRootPart.CFrame.Position.Y < 1000 then
						bbld = true
						hrp = plr.Character.HumanoidRootPart
						game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = plr.Character.HumanoidRootPart.CFrame + Vector3.new(0,10,0) + (plr.Character.HumanoidRootPart.AssemblyLinearVelocity / 10)
						game.Players.LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = plr.Character.HumanoidRootPart.AssemblyLinearVelocity
						local Continue = true
						if OKLA then
							if OKLWF and game.Players.LocalPlayer:IsFriendsWith(plr.UserId) then
								continue = false
							end
						elseif OKA then
							if OWF and game.Players.LocalPlayer:IsFriendsWith(plr.UserId) then
								continue = false
							end
						end
						if hrp and Continue then
							task.wait(0.3)
							game.ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(hrp,hrp.CFrame)
							if loclisttt[line] and loclisttt[line] ~= 'dunno' then
								for _,prt in pairs(plr.Character:GetChildren()) do
									if prt.ClassName == "Part" then
										local bv = Instance.new("BodyPosition")
										bv.D = 50
										bv.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
										bv.P = 10000
										bv.Position = Vector3.new(592,153,-100)
										bv.Parent = prt
									end
								end
								if hrp then
									game.ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(hrp)
								end
							end
						end
					end
					if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and bbld then
						game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = j
						game.Players.LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = p
						task.wait()
					end
				end
			end
			task.wait()
		end
	end)

	function WaitForPenis(plr)
		while not game.Players.LocalPlayer.CanSpawnToy.Value do task.wait() end
		task.spawn(function()
			game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("ToolPencil",CFrame.new(game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame.Position)+Vector3.new(0,0,15),Vector3.new(0,0,0))
		end)

		local diddle = game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys"):WaitForChild("ToolPencil",1)
		
		if not diddle then return "Nigga" end

		local bill = diddle:WaitForChild("SoundPart",1)

		if not bill then return "Nigga" end

		if plr then
			diddle.Name = plr.Name
			return diddle
		else
			return "Nigga"
		end
	end

	function WaitForNinja(plr)
		while not game.Players.LocalPlayer.CanSpawnToy.Value do task.wait() end
		task.spawn(function()
			game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("NinjaKunai",CFrame.new(game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame.Position)+Vector3.new(0,0,15),Vector3.new(0,0,0))
		end)
		local diddle = game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys"):WaitForChild("NinjaKunai",1)
		
		if not diddle then return "Nigga" end

		if plr then
			diddle.Name = plr.Name
			return diddle
		else
			return "Nigga"
		end
	end

	function ticklepenis(diddle)
		while not diddle:FindFirstChild("SoundPart") do task.wait() end
		game.ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(diddle.SoundPart,diddle.SoundPart.CFrame)
	end

	function fondle(nimrod,nuggets)
		task.spawn(function()
			task.wait(0.5)
			if nimrod.Character then
				if nimrod.Character.Parent ~= workspace then 
					game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(nuggets)
					return
				end
				if nuggets and nuggets:FindFirstChild("StickyPart") and nuggets.StickyPart:FindFirstChild("StickyWeld") and nimrod.Character:FindFirstChild("Torso") and nuggets.StickyPart.StickyWeld.Part1 == nimrod.Character.Torso then return end
				if nuggets and nuggets:FindFirstChild("StickyPart") and nuggets.StickyPart:FindFirstChild("StickyWeld") and nimrod.Character:FindFirstChild("Left Leg") and nuggets.StickyPart.StickyWeld.Part1 == nimrod.Character["Left Leg"] then return end
				game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(nuggets)
			else
				game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(nuggets)
			end
		end)
	end

	function tickle(plr,diddle)
		if plr.Character and plr.Character:FindFirstChild("Torso") then
			ticklepenis(diddle)
			task.wait(0.75)
			if plr.Character and plr.Character:FindFirstChild("Torso") then
				if plr.Character.Parent == workspace and diddle:FindFirstChild("StickyPart") then 
					game.ReplicatedStorage.PlayerEvents.StickyPartEvent:FireServer(diddle.StickyPart,plr.Character.Torso,CFrame.new(0,-1,0) * CFrame.Angles(0,math.pi,0))
				else
					game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(diddle)
				end
			end
		end
	end

	function tickletipson(plr,diddle)
		if plr.Character and plr.Character:FindFirstChild("Left Leg") then
			ticklepenis(diddle)
			task.wait(0.75)
			if plr.Character and plr.Character:FindFirstChild("Left Leg") then
				if plr.Character.Parent == workspace and diddle:FindFirstChild("StickyPart") then
					game.ReplicatedStorage.PlayerEvents.StickyPartEvent:FireServer(diddle.StickyPart,plr.Character["Left Leg"],CFrame.new(0,-0.5,0) * CFrame.Angles(0,0,math.pi/2))
				else
					game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(diddle)
				end
			end
		end
	end

	function showmeyourpenis(plr,typee)
		if not game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys"):FindFirstChild(plr.Name) then
			if plr.Character then
				if plr.Character.Parent.Name == "Workspace" then
					if typee == 1 then
						local diddle = WaitForPenis(plr)
						if diddle ~= "Nigga" then
							tickle(plr,diddle)
						end
					else
						local diddle = WaitForNinja(plr)
						if diddle ~= "Nigga" then
							tickletipson(plr,diddle)
						end
					end
				end
			end
		else
			local nuggets = game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys"):FindFirstChild(plr.Name)
			fondle(plr,nuggets)
		end
	end

	task.spawn(function()
		while true do
			for _,plr in pairs(PenisList) do
				if game.Players:FindFirstChild(plr) then
					showmeyourpenis(game.Players:FindFirstChild(plr),1)
				end
			end
			for _,plr in pairs(AKList) do
				if game.Players:FindFirstChild(plr) then
					showmeyourpenis(game.Players:FindFirstChild(plr),2)
				end
			end
			task.wait()
		end
	end)

	plrlist = PL:AddDropdown({
		Name = "Select Player",
		Options = currantplrlist,
		Callback = function(Value)
			if loaded then
				Changing = true
				plrlistsel = Value
				if game.Players:FindFirstChild(Value) then
					sellplr = game.Players[Value]
					pOTP:Set(sellplr.BinisJ.OwnershipTP.Value)
					pLK:Set(sellplr.BinisJ.LoopKill.Value)
					pOL:Set(sellplr.BinisJ.OwnershipLocation.Value)
					pBK:Set(sellplr.BinisJ.Blobkick.Value)
					pESP:Set(sellplr.BinisJ.ESP.Value)
					pPenis:Set(sellplr.BinisJ.Penis.Value)
					pAK:Set(sellplr.BinisJ.AntiKick.Value)
					pOK:Set(sellplr.BinisJ.OwnershipKick.Value)
					pOK:Set(sellplr.BinisJ.OwnershipKick.Value)
				end
				Changing = false
			end
		end
	})

	PL:AddSection({
		Name = ""
	})

	PL:AddSection({
		Name = "Kick Player (Blobman)"
	})

	pBK = PL:AddToggle({
		Name = "Kick Player (Blobman)",
		Default = false,
		Callback = function(Value)
			if not Changing and sellplr then
				sellplr.BinisJ.Blobkick.Value = Value
				if Value then
					table.insert(BlobkickList,sellplr.Name)
				else
					for line,var in pairs(BlobkickList) do
						if var == sellplr.Name then
							table.remove(BlobkickList,line)
						end
					end
				end
			end
		end    
	})

	PL:AddSection({
		Name = "Loop Kill Player"
	})

	pLK = PL:AddToggle({
		Name = "Loop Kill Player",
		Default = false,
		Callback = function(Value)
			if not Changing and sellplr then
				sellplr.BinisJ.LoopKill.Value = Value
				if Value then
					table.insert(OTPList,sellplr.Name)
					table.insert(LoopKillList,sellplr.Name)
					table.insert(LocList,Vector3.new(0,math.huge,0))
				else
					for line,var in pairs(OTPList) do
						if var == sellplr.Name then
							table.remove(OTPList,line)
							table.remove(LocList,line)
						end
					end
					for line,var in pairs(LoopKillList) do
						if var == sellplr.Name then
							table.remove(LoopKillList,line)
						end
					end
				end
			end
		end
	})

	PL:AddSection({
		Name = "Ownership Kick"
	})

	pOK = PL:AddToggle({
		Name = "Ownership Kick Player",
		Default = false,
		Callback = function(Value)
			if not Changing and sellplr then
				sellplr.BinisJ.OwnershipKick.Value = Value
				if Value then
					table.insert(OTPList,sellplr.Name)
					table.insert(OKList,sellplr.Name)
					table.insert(LocList,Vector3.new(0,10^38,0))
				else
					for line,var in pairs(OTPList) do
						if var == sellplr.Name then
							table.remove(OTPList,line)
							table.remove(LocList,line)
						end
					end
					for line,var in pairs(LoopKillList) do
						if var == sellplr.Name then
							table.remove(LoopKillList,line)
						end
					end
				end
			end
			if not Changing and sellplr then
				sellplr.BinisJ.OwnershipKick.Value = Value
				if Value then
					table.insert(OKList,sellplr.Name)
					table.insert(LocList,Vector3.new(0,10^38,0))
				else
					for line,var in pairs(OKList) do
						if var == sellplr.Name then
							table.remove(OKList,line)
							table.remove(LocList,line)
						end
					end
				end
			end
		end
	})

	PL:AddSection({
		Name = "Loop TP Player"
	})

	local OTPE = false

	pOTP = PL:AddToggle({
		Name = "Loop TP Player",
		Default = false,
		Callback = function(Value)
			OTPE = Value
			if not Changing and sellplr then
				sellplr.BinisJ.OwnershipTP.Value = Value
				if Value then
					table.insert(OTPList,sellplr.Name)
					table.insert(LocList,'dunno')
				else
					for line,var in pairs(OTPList) do
						if var == sellplr.Name then
							table.remove(OTPList,line)
							table.remove(LocList,line)
						end
					end
				end
			end
		end
	})

	pOL = PL:AddDropdown({
		Name = "Select Location",
		Options = locations,
		Callback = function(Value)
			if OTPE then
				for line,var in pairs(OTPList) do
					if var == sellplr.Name then
						LocList[line] = tpland[Value]
					end
				end
			end
		end    
	})

	coord = PL:AddTextbox({
		Name = "Type Co-Ordinates",
		TextDisappear = true,
		Callback = function(Value)
			
		end	  
	})

	PL:AddSection({
		Name = "ESP Player"
	})

	pESP = PL:AddToggle({
		Name = "ESP",
		Default = false,
		Callback = function(Value)
			if not Changing and sellplr then
				sellplr.BinisJ.ESP.Value = Value
				if Value then
					table.insert(EspList,sellplr.Name)
					ESPP(sellplr)
				else
					UNESPP(sellplr)
					for line,var in pairs(EspList) do
						if var == sellplr.Name then
							table.remove(EspList,line)
						end
					end
				end
			end
		end    
	})

	PL:AddSection({
		Name = "Loop Penis Player"
	})

	pPenis = PL:AddToggle({
		Name = "Loop Penis Player",
		Default = false,
		Callback = function(Value)
			if not Changing and sellplr then
				sellplr.BinisJ.Penis.Value = Value
				if Value then
					table.insert(PenisList,sellplr.Name)
					pAK:Set(false)
				else
					if workspace[game.Players.LocalPlayer.Name.."SpawnedInToys"]:FindFirstChild(sellplr.Name) then
						game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(workspace[game.Players.LocalPlayer.Name.."SpawnedInToys"][sellplr.Name])
					end
					for line,var in pairs(PenisList) do
						if var == sellplr.Name then
							table.remove(PenisList,line)
						end
					end
				end
			end
		end    
	})

	PL:AddSection({
		Name = "Anti-Kick Player"
	})

	pAK = PL:AddToggle({
		Name = "Anti-Kick Player",
		Default = false,
		Callback = function(Value)
			if not Changing and sellplr then
				sellplr.BinisJ.AntiKick.Value = Value
				if Value then
					table.insert(AKList,sellplr.Name)
					pPenis:Set(false)
				else
					if workspace[game.Players.LocalPlayer.Name.."SpawnedInToys"]:FindFirstChild(sellplr.Name) then
						game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(workspace[game.Players.LocalPlayer.Name.."SpawnedInToys"][sellplr.Name])
					end
					for line,var in pairs(AKList) do
						if var == sellplr.Name then
							table.remove(AKList,line)
						end
					end
				end
			end
		end    
	})

	BS = Window:MakeTab({
		Name = "Builds",
		Icon = "rbxassetid://4483362458",
		PremiumOnly = false
	})

	BS:AddSection({
		Name = "Freeze Item"
	})

	local FA

	BS:AddToggle({
		Name = "Activate Freeze Item",
		Default = true,
		Callback = function(Value)
			FA = Value
		end
	})

	BS:AddBind({
		Name = "Freeze Item",
		Default = Enum.KeyCode.X,
		Hold = false,
		Callback = function()
			if FA and workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart") and workspace.GrabParts.GrabPart:FindFirstChild("WeldConstraint") and workspace.GrabParts.GrabPart.WeldConstraint.Part1 then
				local chillonme = workspace.GrabParts.GrabPart.WeldConstraint.Part1
				if not chillonme:FindFirstAncestor("Plots") and not chillonme:FindFirstAncestor("Slots") and not chillonme:FindFirstAncestor("Map") then
					if chillonme.Parent:FindFirstChild("somebodyshotaloadintomyburger2") then
						chillonme.Parent.somebodyshotaloadintomyburger2:Destroy()
						for _,prt in pairs(chillonme.Parent:GetChildren()) do
							if prt:FindFirstChild("somebodyshotaloadintomyburger") then
								prt.somebodyshotaloadintomyburger:Destroy()
							end
						end
						for line,thing in pairs(frozenlist) do
							if thing == chillonme.Parent then
								table.remove(frozenlist,line)
							end
						end
					else
						table.insert(frozenlist,chillonme.Parent)
						local diddysbiscuit = Instance.new("Attachment")
						diddysbiscuit.Name = "somebodyshotaloadintomyburger"
						diddysbiscuit.Parent = chillonme
						local diddlysquat = Instance.new("AlignPosition")
						diddlysquat.Parent = diddysbiscuit
						diddlysquat.Attachment0 = diddysbiscuit
						diddlysquat.Mode = "OneAttachment"
						diddlysquat.MaxForce = math.huge
						diddlysquat.MaxVelocity = math.huge
						diddlysquat.Responsiveness = 200
						diddlysquat.Position = chillonme.CFrame.Position
						local penismcandrew = Instance.new("AlignOrientation")
						penismcandrew.Parent = diddysbiscuit
						penismcandrew.Mode = "OneAttachment"
						penismcandrew.Attachment0 = diddysbiscuit
						penismcandrew.Responsiveness = 200
						penismcandrew.MaxTorque = math.huge
						penismcandrew.CFrame = chillonme.CFrame
						local gottheblicky = Instance.new("Highlight")
						gottheblicky.Parent = chillonme.Parent
						gottheblicky.Name = "somebodyshotaloadintomyburger2"
						gottheblicky.FillTransparency = 0.4
						gottheblicky.FillColor = Color3.fromRGB(27,126,208)
						gottheblicky.OutlineColor = Color3.fromRGB(21,97,161)
					end
				end
			end
		end
	})

	BS:AddSection({
		Name = "Create New Build"
	})

	BS:AddButton({
		Name = "Create New Build",
		Callback = function()
			game.ReplicatedStorage.GrabEvents.ExtendGrabLine:FireServer({"BinisJ","New Build"})
		end
	})

	MS = Window:MakeTab({
		Name = "Map",
		Icon = "rbxassetid://4483362458",
		PremiumOnly = false
	})

	MS:AddSection({
		Name = "Edit Map"
	})

	MS:AddToggle({
		Name = "Private Map",
		Default = false,
		Callback = function(Value)
			if Value and not game:GetService("Workspace"):FindFirstChild("PrivateMap") then
				nigga = mapp:Clone()
				game:GetService("Workspace").Plots:Clone().Parent = nigga
				game:GetService("Workspace").Slots:Clone().Parent = nigga
				nigga.Parent = game:GetService("Workspace")
				nigga.Name = "PrivateMap"
				local mapspp = Instance.new("Part")
				mapspp.Transparency = 1
				mapspp.Anchored = true
				mapspp.CFrame = CFrame.new(Vector3.zero)
				mapspp.Name = "Primary Part"
				mapspp.CanCollide = false
				mapspp.CanQuery = false
				mapspp.Parent = nigga
				nigga.PrimaryPart = mapspp
				nigga:SetPrimaryPartCFrame(CFrame.new(-5000,0,0))
				for _,prt in pairs(nigga.Plots:GetDescendants()) do
					if prt.Name == "PlotBarrier" or prt.Name == "PlotArea" or prt.Name == "FalseBorder" then
						prt.CanQuery = false
						prt.CanCollide = false
					end 
				end
				for _,jigga in pairs(nigga:GetChildren()) do
					if jigga.Name == "drip" then
						jigga:Destroy()
					end
				end
				OrionLib:MakeNotification({
					Name = "Private map created",
					Content = "Private Map",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			elseif not Value and game:GetService("Workspace"):FindFirstChild("PrivateMap") then
				game:GetService("Workspace").PrivateMap:Destroy()
				OrionLib:MakeNotification({
					Name = "Private map destroyed",
					Content = "Private Map",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			end
		end,
	})

	MS:AddSection({
		Name = "Ocean"
	})

	MS:AddToggle({
		Name = "Water Walk",
		Default = true,
		Callback = function(Value)
			local v1 = game:GetService("Workspace").Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel
			for _,prt in pairs(v1:GetChildren()) do 
				if prt:IsA("Part") then 
					prt.CanCollide = Value
				end
			end
			if Value then
				OrionLib:MakeNotification({
					Name = "Water Walk has been enabled",
					Content = "Water Walk",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			else
				OrionLib:MakeNotification({
					Name = "Water Walk has been enabled",
					Content = "Water Walk",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			end
		end,
	})

	MS:AddToggle({
		Name = "Infinite Ocean (adding later)",
		Default = false,
		Callback = function(Value)
			if Value then
				OrionLib:MakeNotification({
					Name = "Not done yet bro!",
					Content = "Infinite Ocean",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			end
		end,
	})

	MS:AddToggle({
		Name = "Freeze Ocean",
		Default = false,
		Callback = function(Value)
			if Value then
				for _,prt in pairs(workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel:GetChildren()) do
					prt.Anchored = true
					prt.CFrame = prt.CFrame + Vector3.new(0,-52 - prt.CFrame.Position.Y,0)
				end
			else
				for _,prt in pairs(workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel:GetChildren()) do
					prt.Anchored = false
				end
			end
		end,
	})

	GS:AddSection({
		Name = "Edit Plots"
	})

	MS:AddToggle({
		Name = "Plot Barrier Collision",
		Default = false,
		Callback = function(Value)
			for _,prt in pairs(game:GetService("Workspace").Plots:GetDescendants()) do
				if prt.Name == "PlotBarrier" or prt.Name == "FalseBorder" then
					prt.CanQuery = Value
					prt.CanCollide = Value
				end
			end
			if Value then
				OrionLib:MakeNotification({
					Name = "Plot Barrier Collision has been enabled",
					Content = "Plot Barrier Collision",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			else
				OrionLib:MakeNotification({
					Name = "Plot Barrier Collision has been disabled",
					Content = "Plot Barrier Collision",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			end
		end,
	})

	local GPI = false
	local GIP = false
	local canC = false

	for _,plot in pairs(game.workspace.PlotItems:GetChildren()) do
		if plot.Name ~= "PlayersInPlots" then
			plot.DescendantAdded:Connect(function(prt)
				if prt:IsA("BasePart") then
					if canC then
						prt.CollisionGroup = "Items"
					end
				end
			end)
		else
			plot.ChildAdded:Connect(function(itm)
				if GIP then
					if itm then
						task.wait()
						itm.Parent = plrinplot
					end
				end
			end)
		end
	end

	MS:AddToggle({
		Name = "Grab people in plots",
		Default = true,
		Callback = function(Value)
			GIP = Value
			if Value then
				for _,diddly in pairs(game:GetService("Workspace").PlotItems.PlayersInPlots:GetChildren()) do
					diddly.Parent = plrinplot
				end
				OrionLib:MakeNotification({
					Name = "You can grab people in plots",
					Content = "Grab people in plots",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			else
				for _,diddly in pairs(game:GetService("Workspace"):FindFirstChild("Players In Plots"):GetChildren()) do
					diddly.Parent = game:GetService("Workspace").PlotItems.PlayersInPlots
				end
				OrionLib:MakeNotification({
					Name = "You can't grab people in plots",
					Content = "Grab people in plots",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			end
		end,
	})

	MS:AddToggle({
		Name = "Plot Item Collision",
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


	MS:AddSection({
		Name = "Model Shit"
	})

	MS:AddToggle({
		Name = "Boner Objects",
		Default = false,
		Callback = function(Value)
			bonerenabled = Value
			if Value then
				for _,plr in pairs(game.Players:GetChildren()) do
					if game.workspace:FindFirstChild(plr.Name.."SpawnedInToys") then
						for _,itm in pairs(game.workspace:FindFirstChild(plr.Name.."SpawnedInToys"):GetChildren()) do
							if itm.Name == "BallMagicLight" then
								Boner(itm)
							end
						end
					end
				end
				for _,plot in pairs(game.workspace.PlotItems:GetChildren()) do
					for _,itm in pairs(plot:GetChildren()) do
						if itm.Name == "BallMagicLight" then
							Boner(itm)
						end
					end
				end
			else
				for _,plr in pairs(game.Players:GetChildren()) do
					if game.workspace:FindFirstChild(plr.Name.."SpawnedInToys") then
						for _,itm in pairs(game.workspace:FindFirstChild(plr.Name.."SpawnedInToys"):GetChildren()) do
							if itm.Name == "BallMagicLight" then
								UnBoner(itm)
							end
						end
					end
				end
				for _,plot in pairs(game.workspace.PlotItems:GetChildren()) do
					for _,itm in pairs(plot:GetChildren()) do
						if itm.Name == "BallMagicLight" then
							UnBoner(itm)
						end
					end
				end
			end
		end,
	})

	MS:AddSection({
		Name = "Foggy Map"
	})

	MS:AddToggle({
		Name = "Foggy Map",
		Default = false,
		Callback = function(Value)
			if Value then
				game:GetService("Workspace").Terrain.Clouds.Density = 0.8009999990463257
				game:GetService("Workspace").Terrain.Clouds.Cover = 0.8009999990463257
				game:GetService("Lighting").Brightness = 1.5
				game:GetService("Lighting").FogColor = Color3.fromRGB(255,255,255)
				game:GetService("Lighting").ClockTime = 16.5
				game:GetService("Lighting").FogEnd = 5000
				game:GetService("Lighting").FogStart = 200
				game:GetService("Lighting").Sky.SkyboxBk = "rbxassetid://10491933248"
				game:GetService("Lighting").Sky.SkyboxDn = "rbxassetid://10491933248"
				game:GetService("Lighting").Sky.SkyboxFt = "rbxassetid://10491933248"
				game:GetService("Lighting").Sky.SkyboxLf = "rbxassetid://10491933248"
				game:GetService("Lighting").Sky.SkyboxRt = "rbxassetid://10491933248"
				game:GetService("Lighting").Sky.SkyboxUp = "rbxassetid://10491933248"
				OrionLib:MakeNotification({
					Name = "Fog Enabled",
					Content = "Foggy Map",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			else
				game:GetService("Workspace").Terrain.Clouds.Density = 0.5709999799728394
				game:GetService("Workspace").Terrain.Clouds.Cover = 0.5709999799728394
				game:GetService("Lighting").Brightness = 2
				game:GetService("Lighting").FogColor = Color3.fromRGB(192,192,192)
				game:GetService("Lighting").ClockTime = 14
				game:GetService("Lighting").FogEnd = 100000
				game:GetService("Lighting").FogStart = 0
				game:GetService("Lighting").Sky.SkyboxBk = "rbxassetid://8995816670"
				game:GetService("Lighting").Sky.SkyboxDn = "rbxassetid://8995686153"
				game:GetService("Lighting").Sky.SkyboxFt = "rbxassetid://8995816670"
				game:GetService("Lighting").Sky.SkyboxLf = "rbxassetid://8995816670"
				game:GetService("Lighting").Sky.SkyboxRt = "rbxassetid://8995816670"
				game:GetService("Lighting").Sky.SkyboxUp = "rbxassetid://8995814929"
				OrionLib:MakeNotification({
					Name = "Fog Disabled",
					Content = "Foggy Map",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			end
		end,
	})

	MS:AddSection({
		Name = "Dave Boner Skybox"
	})

	MS:AddToggle({
		Name = "Dave Boner Skybox",
		Default = false,
		Callback = function(Value)
			if Value then
				game:GetService("Lighting").Sky.SkyboxBk = "rbxassetid://120974828567943"
				game:GetService("Lighting").Sky.SkyboxDn = "rbxassetid://120974828567943"
				game:GetService("Lighting").Sky.SkyboxFt = "rbxassetid://120974828567943"
				game:GetService("Lighting").Sky.SkyboxLf = "rbxassetid://120974828567943"
				game:GetService("Lighting").Sky.SkyboxRt = "rbxassetid://120974828567943"
				game:GetService("Lighting").Sky.SkyboxUp = "rbxassetid://120974828567943"
				OrionLib:MakeNotification({
					Name = "Dave Boner lol",
					Content = "- Dave Boner Himself",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			else
				game:GetService("Lighting").Sky.SkyboxBk = "rbxassetid://8995816670"
				game:GetService("Lighting").Sky.SkyboxDn = "rbxassetid://8995686153"
				game:GetService("Lighting").Sky.SkyboxFt = "rbxassetid://8995816670"
				game:GetService("Lighting").Sky.SkyboxLf = "rbxassetid://8995816670"
				game:GetService("Lighting").Sky.SkyboxRt = "rbxassetid://8995816670"
				game:GetService("Lighting").Sky.SkyboxUp = "rbxassetid://8995814929"
				OrionLib:MakeNotification({
					Name = "Bye Bye Boner",
					Content = "- Dave Boner Himself",
					Image = "rbxassetid://4483345998",
					Time = 2
				})
			end
		end,
	})

	local MiS = Window:MakeTab({
		Name = "Miscellaneous",
		Icon = "rbxassetid://4483362458",
	})

	TK = MiS:AddLabel("Total Players Kicked: 0")

	MiS:AddSection({
		Name = "Blobman Kick All"
	})

	MiS:AddToggle({
		Name = "Blobman Kick All",
		Default = false,
		Callback = function(Value)
			BKA = Value
		end    
	})

	MiS:AddToggle({
		Name = "Whitelist Friends",
		Default = true,
		Callback = function(Value)
			BKAWL = Value
		end    
	})

	MiS:AddSection({
		Name = "Ownership Kick All"
	})

	MiS:AddToggle({
		Name = "Ownership Kick All",
		Default = false,
		Callback = function(Value)
			OKA = Value
		end    
	})

	MiS:AddToggle({
		Name = "Whitelist Friends",
		Default = true,
		Callback = function(Value)
			OWF = Value
		end    
	})

	MiS:AddSection({
		Name = "Kill All"
	})

	MiS:AddToggle({
		Name = "Kill All",
		Default = false,
		Callback = function(Value)
			OKLA = Value
		end    
	})

	MiS:AddToggle({
		Name = "Whitelist Friends",
		Default = true,
		Callback = function(Value)
			OKLWF = Value
		end    
	})

	MiS:AddSection({
		Name = "ESP"
	})

	MiS:AddToggle({
		Name = "ESP All",
		Default = false,
		Callback = function(Value)
			ESPA = Value
			if Value then
				for _,plr in pairs(game.Players:GetPlayers()) do
					local continue = false
					if ESPA then
						continue = true
					else
						for _,plrr in EspList do
							if plr.Name == plrr.Name then
								continue = true
							end
						end
					end
					if plr == game.Players.LocalPlayer then
						continue = false
					end
					if continue then
						ESPP(plr)
					end
				end
			else
				for _,plr in pairs(game.Players:GetPlayers()) do
					local continue = true
					for _,plrr in EspList do
						if plr.Name == plrr.Name then
							continue = false
						end
					end
					if continue then
						UNESPP(plr)
					end
				end
			end
		end
	})

	local TS = Window:MakeTab({
		Name = "Teleportation",
		Icon = "rbxassetid://4483362458",
		PremiumOnly = false
	})

	TS:AddSection({
		Name = "Spawn Location"
	})

	local SLEE = false

	TS:AddToggle({
		Name = "Activate Spawn Location",
		Default = true,
		Callback = function(Value)
			SLEE = Value
			DSL = Value
		end    
	})

	TS:AddSection({
		Name = "Tp To Mouse"
	})

	local holdingthatschlong = false
	local lookforplrs = false
	local lockedplr = nil

	TS:AddBind({
		Name = "TP To Mouse",
		Default = Enum.KeyCode.Z,
		Hold = true,
		Callback = function(did)
			holdingthatschlong = did
			lookforplrs = did
			lockedplr = nil
		end
	})

	local mpoint = Vector3.new(0,0,0)
	local mobject = nil
	local diddyplr = nil

	task.spawn(function()
		while true do
			local hrp = nil
			if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
				hrp = game.Players.LocalPlayer.Character.HumanoidRootPart
			end
			if lookforplrs and not lockedplr then
				mpoint = game.Players.LocalPlayer:GetMouse().Hit.Position
				mobject = game.Players.LocalPlayer:GetMouse().Target
				if mobject then
					diddyplr = game.Players:GetPlayerFromCharacter(mobject:FindFirstAncestorWhichIsA("Model"))
				end
				if diddyplr then
					lockedplr = diddyplr
					lookforplrs = false
				end
			else
				mpoint = Vector3.new(0,0,0)
				mobject = nil
				diddyplr = nil
			end
			if hrp then
				if mobject then
					if holdingthatschlong then
						hrp.AssemblyLinearVelocity = mobject.AssemblyLinearVelocity
					end
				end
				if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
					if holdingthatschlong then
						local hrp = game.Players.LocalPlayer.Character.HumanoidRootPart
						hrp.CFrame = (hrp.CFrame - hrp.CFrame.Position) + game.Players.LocalPlayer:GetMouse().Hit.Position
						holdingthatschlong = false
					end
				end
			end
			if lockedplr and game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and lockedplr.Character and lockedplr.Character:FindFirstChild("HumanoidRootPart") then
				local hrp = game.Players.LocalPlayer.Character.HumanoidRootPart
				local hrpp = lockedplr.Character.HumanoidRootPart
				hrp.CFrame = (hrp.CFrame - hrp.CFrame.Position) + hrpp.CFrame.Position
				hrp.AssemblyLinearVelocity = hrpp.AssemblyLinearVelocity
			end
			task.wait()
		end
	end)

	local homeless = true

	task.spawn(function()
		while true do
			if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
				local dublo = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame
				local reidn = game.Players.LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity
				local plot = nil
				local diddybligga = true
				for _,home in pairs(workspace.Plots:GetChildren()) do
					for _,person in pairs(home.PlotSign.ThisPlotsOwners:GetChildren()) do
						if person.Value == game.Players.LocalPlayer.Name then
							if homeless then task.wait(5) end
							diddybligga = false
						end
					end
				end
				for _,house in pairs(workspace.Plots:GetChildren()) do
					for _,person in pairs(house.PlotSign:GetChildren()) do
						if person.Name == "Sign" then
							if person.Screen.SurfaceGui.Frame.Visible and person.Screen.SurfaceGui.Frame.PlayerDisplayName.Text == game.Players.LocalPlayer.DisplayName then
								if homeless then task.wait(5) end
								diddybligga = false
								plot = house.Name
								if plot == "Plot1" then
									SL = tpland["GreenHouse"]
								elseif plot == "Plot2" then
									SL = tpland["PinkHouse"]
								elseif plot == "Plot3" then
									SL = tpland["PurpleHouse"]
								elseif plot == "Plot4" then
									SL = tpland["BlueHouse"]
								elseif plot == "Plot5" then
									SL = tpland["ChineseHouse"]
								end
							end
						end
					end
				end
				homeless = diddybligga
				if homeless then
					for _,home in pairs(workspace.Plots:GetChildren()) do
						if not home.PlotSign.ThisPlotsOwners:FindFirstChild("Value") and not plot then
							plot = home.Name
							homeless = false
							local jellyfish = home.PlotSign.Sign.Plus.PlusGrabPart
							while not home.PlotSign.ThisPlotsOwners:FindFirstChild("Value") do
								if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
									game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = jellyfish.CFrame + (jellyfish.CFrame.LookVector * Vector3.new(10,10,10))
									game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(jellyfish,jellyfish.CFrame)
								end
								task.wait(0.1)
							end
						end
					end
					if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
						game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = dublo
						game.Players.LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = reidn
					end
					if plot then
						if plot == "Plot1" then
							SL = tpland["GreenHouse"]
						elseif plot == "Plot2" then
							SL = tpland["PinkHouse"]
						elseif plot == "Plot3" then
							SL = tpland["PurpleHouse"]
						elseif plot == "Plot4" then
							SL = tpland["BlueHouse"]
						elseif plot == "Plot5" then
							SL = tpland["ChineseHouse"]
						end
					end
				end
			end
			task.wait()
		end
	end)

	TS:AddSection({
		Name = "Teleport"
	})

	local ST = Window:MakeTab({
		Name = "Scripts",
		Icon = "rbxassetid://4483345998",
	})

	WLL = ST:AddDropdown({
		Name = "Player Whitelist",
		Options = currantplrlist,
		Callback = function(Value)
			WLlistsel = Value
		end
	})

	ST:AddSection({
		Name = "Infinite Yield"
	})

	ST:AddButton({
		Name = "Load Infinite Yield",
		Callback = function()
			OrionLib:MakeNotification({
				Name = "Loading Infinite Yield",
				Content = "Infinite Yield",
				Image = "rbxassetid://4483362458",
				Time = 5
			})
			loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source"))()
		end    
	})

	ST:AddSection({
		Name = "Dex"
	})

	ST:AddButton({
		Name = "Load Dex",
		Callback = function()
			OrionLib:MakeNotification({
				Name = "Loading Dex",
				Content = "Dex",
				Image = "rbxassetid://4483362458",
				Time = 5
			})
			loadstring(game:HttpGet("https://raw.githubusercontent.com/infyiff/backup/main/dex.lua"))()
		end    
	})

	ST:AddSection({
		Name = "Rejoin"
	})

	ST:AddButton({
		Name = "Rejoin Server",
		Callback = function()
			OrionLib:MakeNotification({
				Name = "Attempting to rejoin...",
				Content = "Rejoin",
				Image = "rbxassetid://4483362458",
				Time = 5
			})
			task.wait(0.5)
			game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, game.JobId, game:GetService("Players").LocalPlayer)
		end    
	})

	ST:AddSection({
		Name = "Join Land"
	})

	ST:AddButton({
		Name = "Print Join Land",
		Callback = function()
			game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(200,200,200)\">(Joinland) Check Console</font>")
			print()
			print("----------------------- Join Land -----------------------")
			for line,itm in pairs(joinland) do
				if line % 3 == 1 then
					local herenow = false
					for _,plr in pairs(game.Players:GetChildren()) do
						if plr.UserId == joinland[line] then
							herenow = true
						end
					end
					if not herenow then
						print("(Joinland: Left) "..joinland[line+2].." (@"..joinland[line+1]..") UserID: "..joinland[line])
					end
				end
			end
			for line,itm in pairs(joinland) do
				if line % 3 == 1 then
					local herenow = false
					for _,plr in pairs(game.Players:GetChildren()) do
						if plr.UserId == joinland[line] then
							herenow = true
						end
					end
					if herenow then
						print("(Joinland) "..joinland[line+2].." (@"..joinland[line+1]..") UserID: "..joinland[line])
					end
				end
			end
			print("------------------- End Of Join Land -------------------")
			print()
		end,
	})

	ST:AddSection({
		Name = "Kick Land"
	})

	ST:AddButton({
		Name = "Print Kick Land",
		Callback = function()
			game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(200,200,200)\">(Kickland) Check Console</font>")
			print()
			print("----------------------- Kick Land -----------------------")
			for line,itm in pairs(kickland) do
				if kickland[line] == true then
					print("(Kickland) "..dplist[line].." (@"..uslist[line]..") UserID: "..usidlist[line].." At Position ("..poslist[line]..") By A Player")
				else
					print("(Kickland) "..dplist[line].." (@"..uslist[line]..") UserID: "..usidlist[line].." At Position ("..poslist[line]..")")
				end
			end
			print("------------------- End Of Kick Land -------------------")
			print()
		end,
	})

	ST:AddSection({
		Name = "Print Deaths"
	})

	ST:AddToggle({
		Name = "View Offline BlackHoles",
		Default = false,
		Callback = function(Value)
			VOBBC = Value
			if VOBBC then
				for _,diddler in pairs(dick2:GetChildren()) do
					diddler.Hole.BillboardGui.Enabled = true
					for _,prt in pairs(diddler:GetDescendants()) do
						if prt:IsA("BasePart") then
							prt.Transparency = 0
							if prt.Name == "HumanoidRootPart" then
								prt.Transparency = 1
							end
							if prt.Name == "Hole" then
								prt.Transparency = 0.25
							end
						end
					end
				end
			else
				for _,diddler in pairs(dick2:GetChildren()) do
					diddler.Hole.BillboardGui.Enabled = false
					for _,prt in pairs(diddler:GetDescendants()) do
						if prt:IsA("BasePart") then
							prt.Transparency = 1
						end
					end
				end
			end
		end,
	})

	ST:AddSection({
		Name = "Destroy BinisJ"
	})

	ST:AddButton({
		Name = "Destroy BinisJ nooooooooo",
		Callback = function()
			OrionLib:Destroy()
		end,
	})

	loaded = true

	game.TextChatService.TextChannels.RBXSystem:DisplaySystemMessage("<font color=\"rgb(255, 0, 255)\">[BinisJ]</font><font color=\"rgb(255, 255, 255)\"> Niggalink ONLINE bris</font>")

	OrionLib:Init()
end
