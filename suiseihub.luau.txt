
--[[
	==== SUISEI HUB ====
		Made by Luna!!
		(https://luna0322.onrender.com)
]]

local wind
local service=setmetatable({},{
	__index=function(self,k)
		local s=game:GetService(k)
		rawset(self,k,s)
		return s
	end,
})
local ols,olr=pcall(function()
	wind=loadstring(game:HttpGet('https://github.com/Footagesus/WindUI/releases/latest/download/main.lua'))()
end)
if(not ols or not wind)then
	local s,r=pcall(function()
		service.StarterGui:SetCore("MakeNotification", {
			Title="Suisei Hub",
			Text="Failed to Load WindUI"
		})
	end)
	if(not s)then
		warn(r)
	end
	return warn("[Suisei Hub] Critical Error\nError Message: "..olr)
end
local loop=Instance.new("BindableEvent")
service.RunService.Heartbeat:Connect(function(dt)
	loop:Fire(dt)
end)

do
	--// USELESS UTILS
	function hn(func,...)
		if(service.RunService:IsStudio())then print'hn call'end
		if(coroutine.status(task.spawn(hn,func,...))=="dead")then return end
		return pcall(func,...)
	end
	function sn(depth,func,...)
		if(depth>=80)then return pcall(func,...)end
		task.defer(sn,depth+1,func,...)
	end

	--// FUNCTIONS/VARIABLES/UTILS
	local get=game.FindFirstChild
	local cget=game.FindFirstChildOfClass
	local waitc=game.WaitForChild
	local function getLocalPlayer()
		return service.Players.LocalPlayer
	end
	local function getMouse()
		return getLocalPlayer():GetMouse()
	end
	local function getLookTarget()
		return getMouse().Target
	end
	local function getLocalChar()
		return getLocalPlayer().Character
	end
	local function getLocalRoot()
		if(not getLocalChar())then return end
		return get(getLocalChar(),"HumanoidRootPart")or get(getLocalChar(),"Torso")
	end
	local function getLocalHum()
		if(not getLocalChar())then return end
		return cget(getLocalChar(),"Humanoid")
	end
	local function Velocity(part,value)
		local b=Instance.new("BodyVelocity")
		b.MaxForce=Vector3.one*math.huge
		b.Velocity=value
		b.Parent=part
		task.spawn(task.delay,1,game.Destroy,b)
	end
	local function SetNetworkOwner(part)
		service.ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(part,getLocalRoot().CFrame)
	end
	local function GetNearParts(origin,radius)
		return workspace:GetPartBoundsInRadius(origin,radius)
	end
	local function IsInRadius(part,origin,radius)
		if((part.Position-origin).Magnitude<=radius)then
			return true
		end
		return false
	end
	local function MoveTo(part,x)
		for _,v in ipairs(part.Parent:GetDescendants())do
			if(v:IsA("BasePart"))then
				v.CanCollide=false
			end
		end
		local pos=typeof(x)=="CFrame"and x.Position or x
		local b=Instance.new("BodyPosition")
		b.MaxForce=Vector3.one*math.huge
		b.Position=pos
		b.P=2e4
		b.D=5e3
		b.Parent=part
		task.spawn(function()
			b.ReachedTarget:Wait()
			pcall(game.Destroy,b)
			for _,v in ipairs(part.Parent:GetDescendants())do
				if(v:IsA("BasePart"))then
					v.CanCollide=true
				end
			end
		end)
	end
	local function anchor(part)
		local pos=getLocalRoot().CFrame
		local tpos=part.CFrame
		for _=1,2 do
			getLocalRoot().CFrame=part.CFrame
			SetNetworkOwner(part)
			task.spawn(function()
				task.wait(.5)
				for _=1,2 do
					task.wait(.5)
					SetNetworkOwner(part)
					local p=Instance.new("BodyPosition")
					p.Position=part.CFrame.Position
					p.MaxForce=Vector3.one*math.huge
					p.Parent=part
					local r=Instance.new("BodyGyro")
					r.CFrame=tpos
					r.MaxTorque=Vector3.one*math.huge
					r.Parent=part
				end
			end)
			task.wait()
		end
		getLocalRoot().CFrame=pos
	end
	local function lag(value)
		for _=1,value do
			service.ReplicatedStorage.GrabEvents.CreateGrabLine:FireServer()
		end
	end
	local function ping(value)
		for _=1,value do
			service.ReplicatedStorage.GrabEvents.ExtendGrabLine:FireServer(string.rep("Balls Balls Balls Balls",value))
		end
	end
	local function createLine(part)
		service.ReplicatedStorage.GrabEvents.CreateGrabLine:FireServer(part,CFrame.identity)
	end
	local function ungrab(part)
		service.ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(part)
	end
	local function kickGrab(player)
		local char=player.Character
		if(not char)then return end
		local root=get(char,"HumanoidRootPart")
		local fpp=get(root,"FirePlayerPart")
		fpp.Size=Vector3.new(4.5,5.5,4.5)
		fpp.CollisionGroup="1"
		fpp.CanQuery=true
	end
	local function getInv()
		return get(workspace,getLocalPlayer().Name.."SpawnedInToys")
	end
	local function spawntoy(name,cframe,vector3)
		local toy=service.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(table.unpack({
			[1]=name,
			[2]=cframe,
			[3]=vector3 or Vector3.zero
		}))
		local r=get(getInv(),name)
		return r
	end
	local function destroyToy(model)
		service.ReplicatedStorage.MenuToys.DestroyToy:FireServer(model)
	end
	curAntiDetectPart=nil
	local function AntiDetect()
		repeat task.wait()until getLocalChar()and getLocalRoot()
		local exists=false
		if(getInv())then
			for _,v in pairs(getInv():GetChildren())do
				if(v.Name=="NinjaShuriken")then
					exists=true
					if(get(v.StickyPart,"PartOwner"))then destroyToy(v)end
					if(get(v.SoundPart,"PartOwner"))then destroyToy(v)end
					if(v.StickyPart.StickyWeld.Part1 and v.StickyPart.StickyWeld.Part1:IsDescendantOf(getLocalChar()))then return end
					destroyToy(v)
					break
				end
			end
		end
		if(not exists)then
			curAntiDetectPart=spawntoy("NinjaShuriken",getLocalRoot().CFrame)
			repeat task.wait()until(get(getInv(),"NinjaShuriken"))
			SetNetworkOwner(curAntiDetectPart.SoundPart)
			curAntiDetectPart.SoundPart.CFrame=getLocalRoot().CFrame+Vector3.new(0,.5,0)
		end
		repeat task.wait()until(get(getInv(),"NinjaShuriken"))
		curAntiDetectPart.SoundPart.CFrame=getLocalRoot().CFrame+Vector3.new(0,.5,0)
		local w=Instance.new("WeldConstraint")
		w.Part0=curAntiDetectPart.SoundPart
		w.Part1=getLocalRoot()
		w.Parent=getLocalRoot()
	end
	local IsSafespot=false
	local function Safespot()
		if(not IsSafespot)then
			local p=Instance.new("Part",workspace)
			p.Material=Enum.Material.Grass
			p.Transparency=.5
			p.Anchored=true
			p.CFrame=CFrame.new(1e4,1e4,1e4)
			p.Size=Vector3.new(128,4,128)
			IsSafespot=true
		end
		getLocalRoot().CFrame=CFrame.new(1e4,1e4+10,1e4)
	end
	
	local function ragdoll()
		local args={
			[1]=getLocalRoot(),
			[2]=0
		}
		service.ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(unpack(args))
	end

	local function BlackBoxpos()
		return CFrame.new(32e32,32e32,32e32)
	end

	--// BLOBMAN
	local function getBlobman()
		local v=get(getInv(),"CreatureBlobman",true)
		if(not v)then
			for _,p in ipairs(workspace.PlotItems:GetChildren())do
				if(p)then
					local m=get(p,"CreatureBlobman")
					if(not m)or(m and m.PlayerValue.Value~=getLocalPlayer().Name)then
						wind:Notify({
							Title="Suisei Hub",
							Content="Blobman not found!",
							Duration=5
						})
						return
					end
					v=m
				end
			end
		end
		if(v.ClassName~="Model")then return false end
		if(not get(v,"VehicleSeat"))then return false end
		--[[if(not get(v.VehicleSeat,"SeatWeld"))then return false end
		if(v.VehicleSeat.SeatWeld.Part1~=getLocalRoot())then return false end]]
		return v
	end
	local function spawnBlobman()
		local blobman=spawntoy("CreatureBlobman",getLocalRoot().CFrame)
		return blobman
	end
	local function blobGrab(blob,target,side)
		local args={
			[1]=get(blob,side.."Detector"),
			[2]=target,
			[3]=get(get(blob,side.."Detector"),side.."Weld")
		}
		blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(unpack(args))
	end
	local function blobDrop(blob,target,side)
		local args={
			[1]=get(blob,side.."Detector"),
			[2]=target
		}
		blob.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(unpack(args))
	end
	local function sirentBlobGrab(blob,target,side)
		local args={
			[1]=get(blob,side.."Detector"),
			[2]=target,
			[3]=get(blob,side.."Detector").AttachPlayer
		}
		blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(unpack(args))
	end
	local function blobBring(blob,target,side)
		local pos=getLocalRoot().CFrame
		getLocalRoot().CFrame=target.CFrame
		task.wait(.25)
		blobGrab(blob,target,side)
		task.wait(.25)
		getLocalRoot().CFrame=pos
	end
	local function blobKick(blob,target,side)
		blobGrab(blob,getLocalRoot(),side)
		task.wait(.1)
		SetNetworkOwner(target)
		task.wait()
		target.CFrame+=Vector3.new(0,16,0)
		--MoveTo(target,target.CFrame*Vector3.new(0,24,0))
		task.wait(.1)
		ungrab(target)
		blobGrab(blob,target,side)
	end

	local function IsFriend(p)
		if(not p or not p.UserId or not getLocalPlayer())then return end
		return getLocalPlayer():IsFriendsWith(p.UserId)
	end
	local function IsInPlot(p)
		return p.InPlot.Value
	end
	local function IsInOwnedPlot(p)
		return p.InOwnedPlot.Value
	end
	
	local function getPlayerFromName(name)
		local tplayer=nil
		local sname=name:lower()
		for _,player in pairs(service.Players:GetPlayers())do
			if(player.DisplayName:lower():sub(1,#sname)==sname)then
				tplayer=player
				break
			elseif(player.Name:lower():sub(1,#sname)==sname)then
				if(not tplayer )then
					tplayer=player
				end
			end
		end
		return tplayer
	end

	local function playSound(id)
		task.spawn(function()
			local s=Instance.new("Sound",service.JointsService)
			s.SoundId=id
			s:Play()
			return s
		end)
	end

	local function Snipefunc(root,func,...)
		local pos=getLocalRoot().CFrame
		task.spawn(function(...)
			local parts={"Head","Torso","HumanoidRootPart"}
			for _,p in pairs(parts)do get(getLocalChar(),p).CanCollide=false end
			getLocalRoot().CFrame=CFrame.new(root.Position-root.CFrame.LookVector*15)
			task.wait(0.1)
			workspace.CurrentCamera.CFrame=CFrame.lookAt(workspace.CurrentCamera.CFrame.Position,root.Position)
			for _=1,4 do SetNetworkOwner(root)task.wait(0.05)end
			local look=workspace.CurrentCamera.CFrame
			task.wait(0.1)
			func(...)
			workspace.CurrentCamera.CFrame=look
			task.wait(0.1)
			for _,p in pairs(parts)do get(getLocalChar(),p).CanCollide=true end
			getLocalRoot().CFrame=pos
			Velocity(getLocalRoot(),Vector3.zero)
		end,...)
	end

	--//DEBUGGING
	if(not game:IsLoaded())then
		wind:Notify({
			Title="Suisei Hub",
			Content="Game not loaded! Waiting...",
			Duration=5
		})
		game.Loaded:Wait()
		wind:Notify({
			Title="Suisei Hub",
			Content="Game loaded!",
			Duration=5
		})
	end

	--// MAIN GUI
	local IconURL="https://luna0322.onrender.com/assets/icon.jpg"
	local ToggleKeybind=Enum.KeyCode.C
	wind:AddTheme({
		Name="DefaultTheme",
		Accent=wind:Gradient({
			["0"]={Color=Color3.fromRGB(23,23,76),Transparency=0},
			["100"]={Color=Color3.fromRGB(32,32,64),Transparency=0}
		},{
			Rotation=45
		})
	})
	wind:SetTheme("DefaultTheme")
	
	local window=wind:CreateWindow({
		Title="Suisei Hub",
		Author="Made by Luna",
		Icon=IconURL,
		Folder="SuiseiHub.MadeByLuna.FTAP",
		Transparent=true,
		HideSearchBar=false,
		ScrollBarEnabled=true
	})
	
	local __configmanager=window.ConfigManager
	local __config=__configmanager:CreateConfig("Suisei")
	
	window:SetIconSize(24)
	window:Tag({
		Title="Fling Thing And People",
		Color=Color3.new(.3,0,1)
	})
	window:Tag({
		Title="Beta",
		Color=Color3.new(0.2,.7,0)
	})
	window:EditOpenButton({
		Title="Suisei Hub",
		Icon=IconURL,
		CornerRadius=UDim.new(0,16),
		StrokeThickness=2,
		Color=ColorSequence.new(
			Color3.new(.1,0,1),
			Color3.new(.5,0,1)
		),
		OnlyMobile=false,
		Enabled=true,
		Draggable=true
	})
	window:OnDestroy(function()
		pcall(game.Destroy,loop)
	end)
	window:SetToggleKey(ToggleKeybind)
	
	wind:Notify({
		Title="Suisei Hub",
		Content="Hello, "..getLocalPlayer().DisplayName,
		Duration=5
	})
	wind:Notify({
		Title="Suisei Hub",
		Content=ToggleKeybind.Name.." to toggle the GUI",
		Duration=5
	})

	local __movements=window:Tab({
		Title="Movements",
		Icon="lucide:footprints"
	})
	local __players=window:Tab({
		Title="Players",
		Icon="lucide:user"
	})
	local __visuals=window:Tab({
		Title="Visuals",
		Icon="lucide:eye"
	})
	local __combats=window:Tab({
		Title="Combats",
		Icon="lucide:hand-fist"
	})
	local __auras=window:Tab({
		Title="Auras",
		Icon="lucide:sparkles"
	})
	local __grabs=window:Tab({
		Title="Grabs",
		Icon="lucide:hand"
	})
	local __miscs=window:Tab({
		Title="Miscs",
		Icon="lucide:box"
	})
	local __blobmans=window:Tab({
		Title="Blobmans",
		Icon="lucide:ghost"
	})
	local __snipes=window:Tab({
		Title="Snipes",
		Icon="lucide:crosshair"
	})
	local __trolls=window:Tab({
		Title="Trolls",
		Icon="lucide:skull"
	})
	window:Divider()
	local __settings=window:Tab({
		Title="Settings",
		Icon="lucide:settings"
	})
	local __infos=window:Tab({
		Title="Infos",
		Icon="lucide:info"
	})
	--// CONFIG TABLE
	local config={
		Movements={
			CrouchSpeedHack={
				Value=getLocalHum().WalkSpeed,
				Loop=false
			},
			SpeedHack={
				Value=getLocalHum().WalkSpeed
			},
			JumppowerHack={
				Value=getLocalHum().JumpPower
			},
			Freeze={
				Value=false,
				CFrame=CFrame.new(0,0,0)
			},
			Infjump={
				Value=false
			},
			Fly={
				Value=false
			},
			Noclip={
				Value=false
			},
			Teleports={
				Target={
					Value=false
				},
				Barn={
					CFrame=CFrame.new(-234,85,-311)
				},
				BlueBouse={
					CFrame=CFrame.new(525,98,-375)
				},
				Factory={
					CFrame=CFrame.new(138,365,346)
				},
				GlassHouse={
					CFrame=CFrame.new(-325,109,337)
				},
				JapaneseHouse={
					CFrame=CFrame.new(584,141,-100)
				},
				PinkRoofHouse={
					CFrame=CFrame.new(-525,22,-165)
				},
				SpookyHouse={
					CFrame=CFrame.new(303,14,483)
				},
				TudorHouse={
					CFrame=CFrame.new(-572,20,89)
				},
				TrainCave={
					CFrame=CFrame.new(571,48,-153)
				},
				SmallSecretCave={
					CFrame=CFrame.new(-50,-7,-298)
				},
				BigSecretCave={
					CFrame=CFrame.new(-130,-7,575)
				}
			}
		},
		Players={
			AntiDetect={
				Value=false
			},
			AntiRagdoll={
				Value=false
			},
			AntiTouch={
				Value=false
			},
			AntiBanana={
				Value=false
			},
			AutoSlot={
				Value=false,
				Time=0
			},
			Ragdoll={
				Value=false
			},
			AntiGucci={
				Value=false
			}
		},
		Visuals={
			ESP={
				Value=false,
				FillColor=Color3.new(0.25,0,1),
				OutlineColor=Color3.new(1,1,1)
			},
			FOV={
				Value=70
			},
			TPS={
				Value=false
			},
			Spectate={
				Value=false
			}
		},
		Combats={
			AntiGrab={
				Value=false
			},
			AntiVoid={
				Value=false
			},
			AntiFar={
				Value=false
			},
			AntiExplode={
				Value=false
			},
			StrAntiGrab={
				Value=false
			},
			Extinguisher={
				Value=false
			},
			InvisLine={
				Value=false
			},
			SuperStrength={
				Value=false,
				Power={
					Value=250
				}
			},
			InfLine={
				Value=false,
				Dist={
					Value=0
				}
			},
			Revenge={
				Void={
					Value=false
				},
				Kill={
					Value=false
				},
				Poison={
					Value=false
				},
				Ragdoll={
					Value=false
				},
				Death={
					Value=false
				}
			},
			AimBot={
				Value=false,
				Radius={
					Value=30
				},
				Part={
					Value="Torso"
				}
			}
		},
		Auras={
			VoidAura={
				Value=false
			},
			KillAura={
				Value=false
			},
			PoisonAura={
				Value=false
			},
			RagdollAura={
				Value=false
			},
			DeathAura={
				Value=false
			},
			FireAura={
				Value=false
			},
			AnchorAura={
				Value=false
			},
			NoclipAura={
				Value=false
			}
		},
		Grabs={
			VoidGrab={
				Value=false
			},
			KillGrab={
				Value=false
			},
			PoisonGrab={
				Value=false
			},
			RagdollGrab={
				Value=false
			},
			DeathGrab={
				Value=false
			},
			AnchorGrab={
				Value=false
			},
			KickGrab={
				Value=false
			},
			NoclipGrab={
				Value=false
			}
		},
		Miscs={
			NWOAura={
				Value=false
			},
			Control={
				Target={
					Value=false
				},
				Value=false
			},
			NoTyping={
				Value=false
			},
			AntiKickDisabler={
				Value=false
			}
		},
		Blobman={
			Target={
				Value=nil
			},
			ArmSide={
				Value="Left"
			},
			Noclip={
				Value=false
			},
			GrabAura={
				Value=false
			},
			KickAura={
				Value=false
			},
			LoopKick={
				Value=false
			},
			LoopKickAll={
				Value=false
			}
		},
		Snipes={
			Target={
				Value=nil
			},
			LoopVoid={
				Value=false
			},
			LoopKill={
				Value=false
			},
			LoopPoison={
				Value=false
			},
			LoopRagdoll={
				Value=false
			},
			LoopDeath={
				Value=false
			}
		},
		Trolls={
			LoudAll={
				Value=false,
				SoundPart={
					Value=nil
				}
			},
			Lag={
				Value=false
			},
			Ping={
				Value=false
			},
			ServerDestroyer={
				Value=false,
				CFrame=CFrame.new(0,0,0)
			},
			ChaosLine={
				Value=false
			}
		},
		Settings={
			OnlyPlayer={
				Value=false
			},
			IgnoreFriend={
				Value=false
			},
			IgnoreIsInPlot={
				Value=false
			},
			AuraRadius={
				Value=32
			},
			AimBotSpeed={
				Value=5
			},
			Lag={
				Value=32
			},
			Ping={
				Value=32
			},
			KickMethod={
				"Void"
			},
			SpeedHackMethod={
				"CFrame"
			},
			AutoSpeedHackMethod={
				Value=false
			},
			FlyMethod={
				"Velocity"
			},
			DebugMode={
				Value=false
			}
		},
	}

	local __esp={}
	local function updateESP()
		for i=#__esp,1,-1 do
			local v=__esp[i]
			if(not config.Visuals.ESP.Value or not v or not v.Parent)then
				pcall(game.Destroy,v)
				table.remove(__esp,i)
			else
				v.FillColor=config.Visuals.ESP.FillColor
				v.OutlineColor=config.Visuals.ESP.OutlineColor
			end
		end
	end
	local function addESP(character)
		if(get(character,"__esp.suiseihub"))then return end
		local h=Instance.new("Highlight",character)
		h.FillColor=config.Visuals.ESP.FillColor
		h.OutlineColor=config.Visuals.ESP.OutlineColor
		h.Name="__esp.suiseihub"
		table.insert(__esp,h)
	end

	do
		--// MOVEMENTS
		__movements:Slider({
			Title="Speed (Crouching)",
			Step=1,
			Value={
				Min=0,
				Max=150,
				Default=getLocalHum().WalkSpeed
			},
			Flag="CrouchingSpeed",
			Callback=function(value)
				getLocalHum().WalkSpeed=value
				config.Movements.CrouchSpeedHack.Value=value
			end,
		})
		__movements:Toggle({
			Title="Loop Speed (Crouching)",
			Type="Checkbox",
			Value=false,
			Flag="CrouchingLoopSpeed",
			Callback=function(value)
				config.Movements.CrouchSpeedHack.Loop=value
			end,
		})
		__movements:Slider({
			Title="Jumppower",
			Step=1,
			Value={
				Min=0,
				Max=150,
				Default=getLocalHum().WalkSpeed
			},
			Flag="Jumppower",
			Callback=function(value)
				getLocalHum().JumpPower=value
				config.Movements.JumppowerHack.Value=value
			end,
		})
		__movements:Toggle({
			Title="Loop Jumppower",
			Type="Checkbox",
			Value=false,
			Flag="LoopJumppower",
			Callback=function(value)
				config.Movements.JumppowerHack.Loop=value
			end,
		})
		__movements:Slider({
			Title="Speed",
			Step=1,
			Value={
				Min=0,
				Max=150,
				Default=getLocalHum().WalkSpeed
			},
			Flag="Speed",
			Callback=function(value)
				config.Movements.SpeedHack.Value=value
			end,
		})
		__movements:Toggle({
			Title="Inf jump",
			Type="Checkbox",
			Value=false,
			Flag="Infjump",
			Callback=function(value)
				config.Movements.Infjump.Value=value
			end,
		})
		__movements:Toggle({
			Title="Fly (unstable)",
			Type="Checkbox",
			Value=false,
			Flag="Fly",
			Callback=function(value)
				config.Movements.Fly.Value=value
			end,
		})
		__movements:Toggle({
			Title="Noclip",
			Type="Checkbox",
			Value=false,
			Flag="Noclip",
			Callback=function(value)
				config.Movements.Noclip.Value=value
			end,
		})
		__movements:Toggle({
			Title="Freeze",
			Type="Checkbox",
			Value=false,
			Flag="Freeze",
			Callback=function(value)
				config.Movements.Freeze.CFrame=getLocalRoot().CFrame
				config.Movements.Freeze.Value=value
			end,
		})

		__movements:Divider()

		__movements:Input({
			Title="Target",
			Value=getLocalPlayer().Name,
			Type="Input",
			Placeholder="Target name",
			Flag="TPTarget",
			Callback=function(value)
				config.Movements.Movements.Target.Value=value
			end,
		})
		__movements:Button({
			Title="Teleport",
			Callback=function()
				local character=config.Movements.Teleports.Target.Value.Character
				if(not character)then return end
				local root=get(character,"HumanoidRootPart")
				if(not root)then return end
				getLocalRoot().CFrame=root.CFrame
			end,
		})
		__movements:Button({
			Title="Spawn",
			Callback=function()
				if(not getLocalRoot())then return end
				getLocalRoot().CFrame=CFrame.new(1,-10,-2)
			end,
		})
		__movements:Button({
			Title="Barn",
			Callback=function()
				if(not getLocalRoot())then return end
				getLocalRoot().CFrame=config.Movements.Teleports.Barn.CFrame
			end,
		})
		__movements:Button({
			Title="Blue House",
			Callback=function()
				if(not getLocalRoot())then return end
				getLocalRoot().CFrame=config.Movements.Teleports.BlueBouse.CFrame
			end,
		})
		__movements:Button({
			Title="Factory",
			Callback=function()
				if(not getLocalRoot())then return end
				getLocalRoot().CFrame=config.Movements.Teleports.Factory.CFrame
			end,
		})
		__movements:Button({
			Title="Glass House",
			Callback=function()
				if(not getLocalRoot())then return end
				getLocalRoot().CFrame=config.Movements.Teleports.GlassHouse.CFrame
			end,
		})
		__movements:Button({
			Title="Japanese House",
			Callback=function()
				if(not getLocalRoot())then return end
				getLocalRoot().CFrame=config.Movements.Teleports.JapaneseHouse.CFrame
			end,
		})
		__movements:Button({
			Title="PinkRoof House",
			Callback=function()
				if(not getLocalRoot())then return end
				getLocalRoot().CFrame=config.Movements.Teleports.PinkRoofHouse.CFrame
			end,
		})
		__movements:Button({
			Title="Spooky House",
			Callback=function()
				if(not getLocalRoot())then return end
				getLocalRoot().CFrame=config.Movements.Teleports.SpookyHouse.CFrame
			end,
		})
		__movements:Button({
			Title="Train",
			Callback=function()
				if(not getLocalRoot())then return end
				getLocalRoot().CFrame=workspace.Waypoints.Train.CFrame
			end,
		})
		__movements:Button({
			Title="Tudor House",
			Callback=function()
				if(not getLocalRoot())then return end
				getLocalRoot().CFrame=config.Movements.Teleports.TudorHouse.CFrame
			end,
		})
		__movements:Button({
			Title="Train Cave",
			Callback=function()
				if(not getLocalRoot())then return end
				getLocalRoot().CFrame=config.Movements.Teleports.TrainCave.CFrame
			end,
		})
		__movements:Button({
			Title="Small Secret Cave",
			Callback=function()
				if(not getLocalRoot())then return end
				getLocalRoot().CFrame=config.Movements.Teleports.SmallSecretCave.CFrame
			end,
		})
		__movements:Button({
			Title="Big Secret Cave",
			Callback=function()
				if(not getLocalRoot())then return end
				getLocalRoot().CFrame=config.Movements.Teleports.BigSecretCave.CFrame
			end,
		})
	end

	do
		--// PLAYERS
		__players:Button({
			Title="Barrier Destroyer",
			Callback=function()
				local pos=getLocalRoot().CFrame
				local toy=spawntoy("InstrumentWoodwindOcarina",getLocalRoot().CFrame)
				local args={
					[1]=toy,
					[2]=getLocalChar()
				}
				toy.HoldPart.HoldItemRemoteFunction:InvokeServer(unpack(args))
				task.wait(.05)
				getLocalRoot().CFrame=CFrame.new(304.06,25.77,488.54)
				task.wait(.05)
				destroyToy(toy)
				task.wait(.05)
				getLocalRoot().CFrame=pos
				for _,v in ipairs(workspace.Plots:GetChildren())do
					local b=v.Barrier
					for _,p in ipairs(b:GetChildren())do
						p.CanCollide=false
					end
				end
				task.wait(1)
				--// CHECK
				wind:Notify({
					Title="Suisei Hub",
					Content="Checking Barrier...",
					Duration=5
				})
				local p=spawntoy("FireExtinguisher",getLocalRoot().CFrame)
				SetNetworkOwner(p.SoundPart)
				task.wait(.5)
				p.SoundPart.CFrame=CFrame.new(304,25,488)
				task.wait(.75)
				if(p.Parent)then
					wind:Notify({
						Title="Suisei Hub",
						Content="Barrier has been destroyed!",
						Duration=5
					})
					destroyToy(p)
				else
					wind:Notify({
						Title="Suisei Hub",
						Content="Failed to destroy the barrier!",
						Duration=5
					})
					destroyToy(p)
				end
			end,
		})
		__players:Toggle({
			Title="Anti-Detect",
			Type="Checkbox",
			Value=false,
			Flag="ACBypass",
			Callback=function(value)
				if(value)then
					AntiDetect()
				end
				config.Players.AntiDetect.Value=value
			end,
		})
		__players:Toggle({
			Title="Anti-Ragdoll",
			Type="Checkbox",
			Value=false,
			Flag="AntiRagdoll",
			Callback=function(value)
				config.Players.AntiRagdoll.Value=value
			end,
		})
		__players:Toggle({
			Title="Anti-Touch",
			Type="Checkbox",
			Value=false,
			Flag="AntiTouch",
			Callback=function(value)
				config.Players.AntiTouch.Value=value
			end,
		})
		__players:Toggle({
			Title="Anti-Banana",
			Type="Checkbox",
			Value=false,
			Flag="AntiBanana",
			Callback=function(value)
				config.Players.AntiTouch.Value=value
			end,
		})
		__players:Toggle({
			Title="Auto-Slot",
			Type="Checkbox",
			Value=false,
			Flag="AutoSlot",
			Callback=function(value)
				config.Players.AutoSlot.Value=value
			end,
		})
		__players:Toggle({
			Title="Ragdoll",
			Type="Checkbox",
			Value=false,
			Flag="Ragdoll",
			Callback=function(value)
				config.Players.Ragdoll.Value=value
			end,
		})
		__players:Toggle({
			Title="Anti-Gucci",
			Type="Checkbox",
			Value=false,
			Flag="AntiGucci",
			Callback=function(value)
				config.Players.AntiGucci.Value=value
				if(value)then
					local pos=getLocalRoot().CFrame
					local blob=spawntoy("CreatureBlobman",getLocalRoot().CFrame)
					local head=get(blob,"Head")
					if(head)then
						head.CFrame=CFrame.new(1e5,1e5,1e5)
						head.Anchored=true
					end
					task.wait(.25)
					local pos=getLocalRoot().CFrame
					if(blob.VehicleSeat)then
						getLocalRoot().CFrame=blob.VehicleSeat.CFrame+Vector3.new(0,2,0)
						blob.VehicleSeat:Sit(getLocalHum())
					end
					task.wait(.25)
					getLocalHum():ChangeState(Enum.HumanoidStateType.Jumping)
					task.wait(.25)
					getLocalRoot().CFrame=pos
				else
					destroyToy(config.Players.AntiGucci.Blob.Value)
				end
			end,
		})
	end

	do
		--// VISUALS
		__visuals:Toggle({
			Title="ESP",
			Type="Checkbox",
			Value=false,
			Flag="ESP",
			Callback=function(value)
				config.Visuals.ESP.Value=value
			end,
		})
		__visuals:Slider({
			Title="FOV",
			Step=1,
			Value={
				Min=1,
				Max=120,
				Default=70
			},
			Flag="PingAmount",
			Callback=function(value)
				config.Visuals.FOV.Value=value
				workspace.CurrentCamera.FieldOfView=value
			end,
		})
		__visuals:Toggle({
			Title="3rd person",
			Type="Checkbox",
			Value=false,
			Flag="TPS",
			Callback=function(value)
				config.Visuals.TPS.Value=value
			end,
		})
		__visuals:Input({
			Title="Spectate",
			Value=getLocalPlayer().Name,
			Type="Input",
			Placeholder="Target name",
			Flag="SpectateTarget",
			Callback=function(value)
				local tplayer=getPlayerFromName(value)
				workspace.CurrentCamera.CameraSubject=cget(tplayer.Character,"Humanoid")
			end,
		})
	end

	do
		--// COMBATS
		__combats:Toggle({
			Title="Anti-Grab (may increase ping)",
			Type="Checkbox",
			Value=false,
			Flag="AntiGrab",
			Callback=function(value)
				config.Combats.AntiGrab.Value=value
			end,
		})
		__combats:Toggle({
			Title="Anti-Void",
			Type="Checkbox",
			Value=false,
			Flag="AntiVoid",
			Callback=function(value)
				config.Combats.AntiVoid.Value=value
			end,
		})
		__combats:Toggle({
			Title="Anti-Far",
			Type="Checkbox",
			Value=false,
			Flag="AntiFar",
			Callback=function(value)
				config.Combats.AntiFar.Value=value
			end,
		})
		__combats:Toggle({
			Title="Anti-Explode",
			Type="Checkbox",
			Value=false,
			Flag="AntiExplode",
			Callback=function(value)
				config.Combats.AntiExplode.Value=value
			end,
		})
		__combats:Toggle({
			Title="Anti-Explode V2",
			Type="Checkbox",
			Value=false,
			Flag="AntiExplodeV2",
			Callback=function(value)
				local p=getLocalPlayer().PlayerScripts.ClientExoplosionHandler
				p.Enabled=not value
			end,
		})
		__combats:Toggle({
			Title="Strict Anti-Grab",
			Type="Checkbox",
			Value=false,
			Flag="StrAntiGrab",
			Callback=function(value)
				config.Combats.StrAntiGrab.Value=value
			end,
		})
		__combats:Toggle({
			Title="Extinguisher",
			Type="Checkbox",
			Value=false,
			Flag="Extinguisher",
			Callback=function(value)
				config.Combats.Extinguisher.Value=value
			end,
		})
		__combats:Toggle({
			Title="Invisible Line",
			Type="Checkbox",
			Value=false,
			Flag="InvisLine",
			Callback=function(value)
				config.Combats.InvisLine.Value=value
			end,
		})
		__combats:Toggle({
			Title="Super Strength",
			Type="Checkbox",
			Value=false,
			Flag="SuperStrength",
			Callback=function(value)
				config.Combats.SuperStrength.Value=value
			end,
		})
		__combats:Slider({
			Title="Super Strength",
			Step=1,
			Value={
				Min=0,
				Max=5000,
				Default=256
			},
			Flag="SuperStrengthPower",
			Callback=function(value)
				config.Combats.SuperStrength.Power.Value=value
			end,
		})
		__combats:Toggle({
			Title="Inf Line",
			Type="Checkbox",
			Value=false,
			Flag="InfLine",
			Callback=function(value)
				config.Combats.InfLine.Value=value
			end,
		})
		__combats:Toggle({
			Title="Aim Bot",
			Type="Checkbox",
			Value=false,
			Flag="AimBot",
			Callback=function(value)
				config.Combats.AimBot.Value=value
			end,
		})

		__combats:Divider()

		__combats:Toggle({
			Title="Revenge Void",
			Type="Checkbox",
			Value=false,
			Flag="RevengeVoid",
			Callback=function(value)
				config.Combats.Revenge.Void.Value=value
			end,
		})
		__combats:Toggle({
			Title="Revenge Kill",
			Type="Checkbox",
			Value=false,
			Flag="RevengeKill",
			Callback=function(value)
				config.Combats.Revenge.Void.Value=value
			end,
		})
		__combats:Toggle({
			Title="Revenge Poison",
			Type="Checkbox",
			Value=false,
			Flag="RevengeVoid",
			Callback=function(value)
				config.Combats.Revenge.Poison.Value=value
			end,
		})
		__combats:Toggle({
			Title="Revenge Ragdoll",
			Type="Checkbox",
			Value=false,
			Flag="RevengeKill",
			Callback=function(value)
				config.Combats.Revenge.Ragdoll.Value=value
			end,
		})
		__combats:Toggle({
			Title="Revenge Death",
			Type="Checkbox",
			Value=false,
			Flag="RevengeDeath",
			Callback=function(value)
				config.Combats.Revenge.Death.Value=value
			end,
		})
	end

	do
		--// AURAS
		__auras:Toggle({
			Title="Void Aura",
			Type="Checkbox",
			Value=false,
			Flag="VoidAura",
			Callback=function(value)
				config.Auras.VoidAura.Value=value
			end,
		})
		__auras:Toggle({
			Title="Kill Aura",
			Type="Checkbox",
			Value=false,
			Flag="KillAura",
			Callback=function(value)
				config.Auras.KillAura.Value=value
			end,
		})
		__auras:Toggle({
			Title="Poison Aura",
			Type="Checkbox",
			Value=false,
			Flag="PoisonAura",
			Callback=function(value)
				config.Auras.PoisonAura.Value=value
			end,
		})
		__auras:Toggle({
			Title="Ragdoll Aura",
			Type="Checkbox",
			Value=false,
			Flag="RagdollAura",
			Callback=function(value)
				config.Auras.RagdollAura.Value=value
			end,
		})
		__auras:Toggle({
			Title="Death Aura",
			Type="Checkbox",
			Value=false,
			Flag="DeathAura",
			Callback=function(value)
				config.Auras.DeathAura.Value=value
			end,
		})
		__auras:Toggle({
			Title="Fire Aura",
			Type="Checkbox",
			Value=false,
			Flag="FireAura",
			Callback=function(value)
				config.Auras.FireAura.Value=value
			end,
		})
		__auras:Toggle({
			Title="Anchor Aura (may glitch)",
			Type="Checkbox",
			Value=false,
			Flag="AnchorAura",
			Callback=function(value)
				config.Auras.AnchorAura.Value=value
			end,
		})
		__auras:Toggle({
			Title="Noclip Aura",
			Type="Checkbox",
			Value=false,
			Flag="NoclipAura",
			Callback=function(value)
				config.Auras.NoclipAura.Value=value
			end,
		})
	end

	do
		--// GRABS
		__grabs:Toggle({
			Title="Void Grab",
			Type="Checkbox",
			Value=false,
			Flag="VoidGrab",
			Callback=function(value)
				config.Grabs.VoidGrab.Value=value
			end,
		})
		__grabs:Toggle({
			Title="Kill Grab",
			Type="Checkbox",
			Value=false,
			Flag="KillGrab",
			Callback=function(value)
				config.Grabs.KillGrab.Value=value
			end,
		})
		__grabs:Toggle({
			Title="Poison Grab",
			Type="Checkbox",
			Value=false,
			Flag="PoisonGrab",
			Callback=function(value)
				config.Grabs.PoisonGrab.Value=value
			end,
		})
		__grabs:Toggle({
			Title="Ragdoll Grab",
			Type="Checkbox",
			Value=false,
			Flag="RagdollGrab",
			Callback=function(value)
				config.Grabs.RagdollGrab.Value=value
			end,
		})
		__grabs:Toggle({
			Title="Death Grab",
			Type="Checkbox",
			Value=false,
			Flag="DeathGrab",
			Callback=function(value)
				config.Grabs.DeathGrab.Value=value
			end,
		})
		__grabs:Toggle({
			Title="Anchor Grab (may glitch)",
			Type="Checkbox",
			Value=false,
			Flag="AnchorGrab",
			Callback=function(value)
				config.Grabs.AnchorGrab.Value=value
			end,
		})
		__grabs:Toggle({
			Title="Kick Grab",
			Type="Checkbox",
			Value=false,
			Flag="KickGrab",
			Callback=function(value)
				config.Grabs.KickGrab.Value=value
			end,
		})
		__grabs:Toggle({
			Title="Noclip Grab",
			Type="Checkbox",
			Value=false,
			Flag="NoclipGrab",
			Callback=function(value)
				config.Grabs.NoclipGrab.Value=value
			end,
		})
	end

	do
		--// MISCS
		__miscs:Toggle({
			Title="Control",
			Type="Checkbox",
			Value=false,
			Flag="Control",
			Callback=function(value)
				config.Miscs.Control.Value=value
				if(value)then
					wind:Notify({
						Title="Suisei Hub",
						Content="Grab to select!",
						Duration=5
					})
				else
					workspace.CurrentCamera.CameraSubject=getLocalHum()
					config.Miscs.Control.Target.Value=nil
				end
			end,
		})
		__miscs:Toggle({
			Title="Ocean Walk",
			Type="Checkbox",
			Value=false,
			Flag="OceanWalk",
			Callback=function(value)
				local p=workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel
				for _,v in ipairs(p:GetChildren())do
					if(v:IsA("BasePart"))then
						v.CanCollide=value
					end
				end
			end,
		})
		__miscs:Button({
			Title="Safespot",
			Callback=function()
				Safespot()
			end,
		})
		__miscs:Toggle({
			Title="Anti-Lag",
			Type="Checkbox",
			Value=false,
			Flag="AntiLag",
			Callback=function(value)
				local p=getLocalPlayer().PlayerScripts.CharacterAndBeamMove
				p.Enabled=not value
			end,
		})
		__miscs:Toggle({
			Title="Anti-Sticky (NetworkOwner)",
			Type="Checkbox",
			Value=false,
			Flag="AntiSticky",
			Callback=function(value)
				local p=getLocalPlayer().PlayerScripts.StickyPartsTouchDetection
				p.Enabled=not value
			end,
		})
		__miscs:Toggle({
			Title="NetworkOwner-Aura",
			Type="Checkbox",
			Value=false,
			Flag="NetworkOwnerAura",
			Callback=function(value)
				config.Miscs.NWOAura.Value=value
			end,
		})
		__miscs:Toggle({
			Title="No Typing",
			Type="Checkbox",
			Value=false,
			Flag="NoTyping",
			Callback=function(value)
				config.Miscs.NoTyping.Value=value
			end,
		})
		__miscs:Toggle({
			Title="Anti-Kick Disabler",
			Type="Checkbox",
			Value=false,
			Flag="AntiKickDisabler",
			Callback=function(value)
				config.Miscs.AntiKickDisabler.Value=value
			end,
		})
	end

	do
		--// BLOBMANS
		__blobmans:Input({
			Title="Target",
			Value=getLocalPlayer().Name,
			Type="Input",
			Placeholder="Target name",
			Flag="BlobTarget",
			Callback=function(value)
				config.Blobman.Target.Value=value
			end,
		})
		__blobmans:Dropdown({
			Title="Arm Side",
			Values={
				"Left",
				"Right"
			},
			Value="Left",
			Flag="ArmSide",
			Callback=function(value)
				config.Blobman.ArmSide.Value=value
			end,
		})
		__blobmans:Button({
			Title="Spawn Blobman",
			Callback=function()
				spawnBlobman()
			end,
		})
		__blobmans:Button({
			Title="OP-Blobman",
			Callback=function()
				local blob=getBlobman()
				if(not blob)then
					blob=spawnBlobman()
				end
				if(not getLocalHum().Sit)then
					blob.VehicleSeat:Sit(getLocalHum())
				end
				local pos=getLocalRoot().CFrame
				task.wait()
				if(blob and getLocalHum())then
					--// RIGHT

					--// IsInPlotItemsFolder
					if(blob:IsDescendantOf(workspace.PlotItems))then
						getLocalRoot().CFrame=CFrame.new(0,0,0)
						task.wait(.5)
					end
					local Toy=spawntoy("YouDecoy",getLocalRoot().CFrame)
					SetNetworkOwner(Toy.HumanoidRootPart)
					Toy.HumanoidRootPart.CFrame=blob.RightDetector.CFrame
					task.wait()
					blobGrab(blob,Toy.HumanoidRootPart,"Right")
					task.wait(1.25)
					destroyToy(Toy)
					task.wait(.1)

					--// LEFT
					local Toy=spawntoy("YouDecoy",getLocalRoot().CFrame)
					SetNetworkOwner(Toy.HumanoidRootPart)
					Toy.HumanoidRootPart.CFrame=blob.LeftDetector.CFrame
					task.wait()
					blobGrab(blob,Toy.HumanoidRootPart,"Left")
					task.wait(1.25)
					destroyToy(Toy)
					task.wait(.1)
				end
				getLocalRoot().CFrame=pos
			end,
		})
		__blobmans:Toggle({
			Title="Noclip",
			Type="Checkbox",
			Value=false,
			Flag="Noclip",
			Callback=function(value)
				config.Blobman.Noclip.Value=value
			end,
		})
		__blobmans:Button({
			Title="Bring",
			Callback=function()
				local t=getPlayerFromName(config.Blobman.Target.Value)
				if(t)then
					task.spawn(function()
						local root=get(t.Character,"HumanoidRootPart")
						local b=getBlobman()
						if(not root or not b)then return end
						local pos=getLocalRoot().CFrame
						getLocalRoot().CFrame=root.CFrame
						blobBring(b,root,config.Blobman.ArmSide.Value)
						task.wait()
						getLocalRoot().CFrame=pos
						task.spawn(function()
							for _=1,256 do
								task.wait()
								if(IsInRadius(root,getLocalRoot().Position,12))then
									task.wait(1)
									getLocalHum().Sit=false
									break
								end
							end
						end)
					end)
				end
			end,
		})
		__blobmans:Button({
			Title="Lock (OP Blobman)",
			Callback=function()
				task.spawn(function()
					local t=getPlayerFromName(config.Blobman.Target.Value)
					if(t)then
						local root=get(t.Character,"HumanoidRootPart")
						local b=getBlobman()
						local pos=getLocalRoot().CFrame
						blobBring(b,root,config.Blobman.ArmSide.Value)
						task.wait()
						getLocalRoot().CFrame=pos
					end
				end)
			end,
		})
		__blobmans:Button({
			Title="Slide",
			Callback=function()
				local t=getPlayerFromName(config.Blobman.Target.Value)
				if(t)then
					task.spawn(function()
						local root=get(t.Character,"HumanoidRootPart")
						local b=getBlobman()
						local pos=getLocalRoot().CFrame
						blobGrab(b,getLocalRoot(),config.Blobman.ArmSide.Value)
						task.wait()
						blobBring(b,root,config.Blobman.ArmSide.Value)
						task.wait()
						getLocalRoot().CFrame=pos
						task.wait(.5)
						destroyToy(b)
					end)
				end
			end,
		})
		__blobmans:Button({
			Title="Void",
			Callback=function()
				local t=getPlayerFromName(config.Blobman.Target.Value)
				if(t)then
					task.spawn(function()
						local root=get(t.Character,"HumanoidRootPart")
						local b=getBlobman()
						local pos=getLocalRoot().CFrame
						blobGrab(b,getLocalRoot(),config.Blobman.ArmSide.Value)
						task.wait()
						blobBring(b,root,config.Blobman.ArmSide.Value)
						task.wait()
						getLocalRoot().CFrame=CFrame.new(1e32,-16,1e32)
						task.wait(1)
						getLocalHum().Sit=false
						task.wait(.1)
						getLocalRoot().CFrame=pos
						task.wait()
						destroyToy(b)
					end)
				end
			end,
		})
		__blobmans:Button({
			Title="Kick",
			Callback=function()
				local t=getPlayerFromName(config.Blobman.Target.Value)
				if(t)then
					task.spawn(function()
						local root=get(t.Character,"HumanoidRootPart")
						local b=getBlobman()
						local pos=getLocalRoot().CFrame
						task.wait(.5)
						getLocalRoot().CFrame=root.CFrame
						task.wait()
						blobKick(b,root,config.Blobman.ArmSide.Value)
						task.wait(.5)
						getLocalRoot().CFrame=pos
					end)
				end
			end,
		})
		__blobmans:Button({
			Title="Slide All",
			Callback=function()
				local blob=getBlobman()
				if(not blob)then
					blob=spawnBlobman()
				end
				if(not getLocalHum().Sit)then
					blob.VehicleSeat:Sit(getLocalHum())
				end
				task.wait()
				local pos=getLocalRoot().CFrame
				if(blob and getLocalHum().Sit)then
					blobGrab(blob,getLocalRoot(),config.Blobman.ArmSide.Value)
					for _,v in ipairs(service.Players:GetPlayers())do
						if(v==getLocalPlayer())then continue end
						if(not config.Settings.IgnoreIsInPlot.Value and IsInPlot(v))then continue end
						if(config.Settings.IgnoreFriend.Value and IsFriend(v))then continue end
						local character=v.Character
						if(not character)then continue end
						local root=get(character,"HumanoidRootPart")
						if(not root)then continue end
						getLocalRoot().CFrame=root.CFrame
						task.wait(.2)
						blobGrab(blob,root,config.Blobman.ArmSide.Value)
					end
					task.wait(.1)
					getLocalRoot().CFrame=pos
					destroyToy(blob)
				end
			end,
		})
		__blobmans:Button({
			Title="Kick All",
			Callback=function()
				local blob=getBlobman()
				if(not blob)then
					blob=spawnBlobman()
				end
				if(not getLocalHum().Sit)then
					blob.VehicleSeat:Sit(getLocalHum())
				end
				task.wait()
				local pos=getLocalRoot().CFrame
				if(blob and getLocalHum().Sit)then
					blobGrab(blob,getLocalRoot(),config.Blobman.ArmSide.Value)
					for _,v in ipairs(service.Players:GetPlayers())do
						if(v==getLocalPlayer())then continue end
						if(not config.Settings.IgnoreIsInPlot.Value and IsInPlot(v))then continue end
						if(config.Settings.IgnoreFriend.Value and IsFriend(v))then continue end
						local character=v.Character
						if(not character)then continue end
						local root=get(character,"HumanoidRootPart")
						if(not root)then continue end
						getLocalRoot().CFrame=root.CFrame
						task.wait(.25)
						blobKick(blob,root,config.Blobman.ArmSide.Value)
					end
					task.wait(.1)
					getLocalRoot().CFrame=pos
					destroyToy(blob)
				end
			end,
		})
		
		__blobmans:Divider()
		
		__blobmans:Toggle({
			Title="Grab Aura",
			Type="Checkbox",
			Value=false,
			Flag="BlobGrabAura",
			Callback=function(value)
				config.Blobman.GrabAura.Value=value
			end,
		})
		__blobmans:Toggle({
			Title="Kick Aura",
			Type="Checkbox",
			Value=false,
			Flag="BlobKickAura",
			Callback=function(value)
				config.Blobman.KickAura.Value=value
			end,
		})
		__blobmans:Toggle({
			Title="Loop kick",
			Type="Checkbox",
			Value=false,
			Flag="BlobLoopKick",
			Callback=function(value)
				config.Blobman.LoopKick.Value=value
			end,
		})
		__blobmans:Toggle({
			Title="Loop Kick All",
			Type="Checkbox",
			Value=false,
			Flag="LoopKickAll",
			Callback=function(value)
				config.Blobman.LoopKickAll.Value=value
			end,
		})
	end

	do
		--// SNIPES
		__snipes:Input({
			Title="Target",
			Value=getLocalPlayer().Name,
			Type="Input",
			Placeholder="Target name",
			Flag="Target",
			Callback=function(value)
				config.Snipes.Target.Value=value
			end,
		})
		__snipes:Button({
			Title="Bring",
			Callback=function()
				local pos=getLocalRoot().CFrame
				local t=getPlayerFromName(config.Snipes.Target.Value)
				if(not t)then return end
				local root=get(t.Character,"HumanoidRootPart")
				if(not root)then return end
				task.spawn(function()
					Snipefunc(root,function()
						task.wait(.01)
						root.CFrame=pos
						task.wait(.5)
						ungrab(root)
						getLocalRoot().CFrame=pos
					end)
				end)
			end,
		})
		__snipes:Button({
			Title="Void",
			Callback=function()
				task.spawn(function()
					local pos=getLocalRoot().CFrame
					local t=getPlayerFromName(config.Snipes.Target.Value)
					if(not t)then return end
					local root=get(t.Character,"HumanoidRootPart")
					if(not root)then return end
					Snipefunc(root,function()
						Velocity(root,Vector3.new(0,1e4,0))
						getLocalRoot().CFrame=pos
					end)
				end)
			end,
		})
		__snipes:Button({
			Title="Kill",
			Callback=function()
				task.spawn(function()
					local pos=getLocalRoot().CFrame
					local t=getPlayerFromName(config.Snipes.Target.Value)
					if(not t)then return end
					local root=get(t.Character,"HumanoidRootPart")
					if(not root)then return end
					Snipefunc(root,function()
						MoveTo(root,CFrame.new(4096,-75,4096))
						Velocity(root,Vector3.new(0,-1e3,0))
						getLocalRoot().CFrame=pos
					end)
				end)
			end,
		})
		__snipes:Button({
			Title="Poison",
			Callback=function()
				task.spawn(function()
					local pos=getLocalRoot().CFrame
					local t=getPlayerFromName(config.Snipes.Target.Value)
					if(not t)then return end
					local root=get(t.Character,"HumanoidRootPart")
					if(not root)then return end
					Snipefunc(root,function()
						MoveTo(root,CFrame.new(58,-70,271))
						getLocalRoot().CFrame=pos
					end)
				end)
			end,
		})
		__snipes:Button({
			Title="Ragdoll",
			Callback=function()
				task.spawn(function()
					local pos=getLocalRoot().CFrame
					local t=getPlayerFromName(config.Snipes.Target.Value)
					if(not t)then return end
					local root=get(t.Character,"HumanoidRootPart")
					if(not root)then return end
					Snipefunc(root,function()
						local rpos=root.CFrame
						Velocity(root,Vector3.new(0,-64,0))
						task.wait(.1)
						getLocalRoot().CFrame=rpos
						Velocity(root,Vector3.zero)
						getLocalRoot().CFrame=pos
					end)
				end)
			end,
		})
		__snipes:Button({
			Title="Death",
			Callback=function()
				task.spawn(function()
					local pos=getLocalRoot().CFrame
					local t=getPlayerFromName(config.Snipes.Target.Value)
					if(not t)then return end
					local root=get(t.Character,"HumanoidRootPart")
					if(not root)then return end
					Snipefunc(root,function()
						cget(root.Parent,"Humanoid"):ChangeState(Enum.HumanoidStateType.Dead)
						task.wait(.5)
						ungrab(root)
						getLocalRoot().CFrame=pos
					end)
				end)
			end,
		})
		__snipes:Button({
			Title="Fling",
			Callback=function()
				local pos=getLocalRoot().CFrame
				local t=getPlayerFromName(config.Snipes.Target.Value)
				if(not t)then return end
				local root=get(t.Character,"HumanoidRootPart")
				if(not root)then return end
				local toy=spawntoy("YouDecoy",getLocalRoot().CFrame)
				task.wait(.3)
				getLocalRoot().CFrame=toy.PrimaryPart.CFrame
				task.wait(.1)
				SetNetworkOwner(toy.PrimaryPart)
				for _=1,256 do
					SetNetworkOwner(toy.PrimaryPart)
					task.wait()
					local rx=math.rad(math.random(0,360*32768))
					local ry=math.rad(math.random(0,360*32768))
					local rz=math.rad(math.random(0,360*32768))
					local rr=1.5
					toy.PrimaryPart.CFrame=CFrame.new(root.Position+Vector3.one*math.random(-rr,rr))*CFrame.Angles(rx,ry,rz)
					Velocity(toy.PrimaryPart,Vector3.one*1e16)
				end
				task.wait(.5)
				getLocalRoot().CFrame=pos
				task.wait(.5)
				destroyToy(toy)
			end,
		})

		__snipes:Divider()

		__snipes:Toggle({
			Title="Loop Void",
			Type="Checkbox",
			Value=false,
			Flag="LoopVoid",
			Callback=function(value)
				config.Snipes.LoopVoid.Value=value
			end,
		})
		__snipes:Toggle({
			Title="Loop Kill",
			Type="Checkbox",
			Value=false,
			Flag="LoopKill",
			Callback=function(value)
				config.Snipes.LoopKill.Value=value
			end,
		})
		__snipes:Toggle({
			Title="Loop Poison",
			Type="Checkbox",
			Value=false,
			Flag="LoopPoison",
			Callback=function(value)
				config.Snipes.LoopPoison.Value=value
			end,
		})
		__snipes:Toggle({
			Title="Loop Ragdoll",
			Type="Checkbox",
			Value=false,
			Flag="LoopRagdoll",
			Callback=function(value)
				config.Snipes.LoopRagdoll.Value=value
			end,
		})
		__snipes:Toggle({
			Title="Loop Death",
			Type="Checkbox",
			Value=false,
			Flag="LoopDeath",
			Callback=function(value)
				config.Snipes.LoopDeath.Value=value
			end,
		})
	end

	do
		--// TROLLS
		__trolls:Button({
			Title="Burn All",
			Callback=function()
				local Oven=spawntoy("OvenDarkGray",getLocalRoot().CFrame)
				local Button=Oven.ButtonOven
				SetNetworkOwner(Button)
				ungrab(Button)
				task.wait(.1)
				SetNetworkOwner(Oven.SoundPart)
				for _,v in ipairs(service.Players:GetPlayers())do
					if(v==getLocalPlayer())then continue end
					if(not config.Settings.IgnoreIsInPlot.Value and IsInPlot(v))then continue end
					if(config.Settings.IgnoreFriend.Value and IsFriend(v))then continue end
					local character=v.Character
					if(not character)then continue end
					local root=get(character,"HumanoidRootPart")
					if(not root)then continue end
					task.wait(.25)
					Oven.SoundPart.CFrame=root.CFrame
				end
				task.wait(1)
				destroyToy(Oven)
			end,
		})
		__trolls:Button({
			Title="Fire All",
			Callback=function()
				local toy=spawntoy("Campfire",getLocalRoot().CFrame)
				SetNetworkOwner(toy.SoundPart)
				for _,v in ipairs(service.Players:GetPlayers())do
					if(v==getLocalPlayer())then continue end
					if(not config.Settings.IgnoreIsInPlot.Value and IsInPlot(v))then continue end
					if(config.Settings.IgnoreFriend.Value and IsFriend(v))then continue end
					local character=v.Character
					if(not character)then continue end
					local root=get(character,"HumanoidRootPart")
					if(not root)then continue end
					task.wait(.25)
					toy.SoundPart.CFrame=root.CFrame
				end
				task.wait(1)
				destroyToy(toy)
			end,
		})
		__trolls:Toggle({
			Title="Loud All",
			Type="Checkbox",
			Value=false,
			Flag="LoudAll",
			Callback=function(value)
				config.Trolls.LoudAll.Value=value
				if(value)then
					local SoundMain=spawntoy("BellBig",getLocalRoot().CFrame)
					task.wait(.5)
					config.Trolls.LoudAll.SoundPart.Value=SoundMain
					SetNetworkOwner(SoundMain.SoundPart)
				else
					destroyToy(config.Trolls.LoudAll.SoundPart.Value)
				end
			end,
		})
		__trolls:Toggle({
			Title="Lag",
			Type="Checkbox",
			Value=false,
			Flag="Lag",
			Callback=function(value)
				config.Trolls.Lag.Value=value
			end,
		})
		__trolls:Toggle({
			Title="Ping",
			Type="Checkbox",
			Value=false,
			Flag="Ping",
			Callback=function(value)
				config.Trolls.Ping.Value=value
			end,
		})
		__trolls:Toggle({
			Title="Server Destroyer",
			Type="Checkbox",
			Value=false,
			Flag="ServerDestroyer",
			Callback=function(value)
				config.Trolls.ServerDestroyer.Value=value
				if(value)then
					config.Trolls.ServerDestroyer.CFrame=getLocalRoot().CFrame
				end
			end,
		})
		__trolls:Toggle({
			Title="Chaos Line",
			Type="Checkbox",
			Value=false,
			Flag="ChaosLine",
			Callback=function(value)
				config.Trolls.ChaosLine.Value=value
			end,
		})
		__trolls:Button({
			Title="THE WORLD",
			Callback=function()
				local s=playSound("rbxassetid://7514417921")
				task.wait(1)
				local e=Instance.new("Part",workspace)
				e.Size=Vector3.one
				e.Shape=Enum.PartType.Ball
				e.Material=Enum.Material.ForceField
				e.Color=Color3.new(.5,.7,0.2)
				e.CanCollide=false
				e.CanQuery=false
				e.CanTouch=false
				e.Locked=true
				task.spawn(function()
					while(true)do
						e.Size+=Vector3.one/2
						e.CFrame=getLocalRoot().CFrame
						e.Transparency+=.1
						lag(4096)
						task.wait()
					end
				end)
			end,
		})
	end

	do
		--// SETTINGS
		__settings:Button({
			Title="Save Config",
			Callback=function()
				window:Dialog({
					Title="Suisei Hub",
					Content="Are you sure you want to save the config?",
					Icon="lucide:info",
					Buttons={
						{
							Title="Confirm",
							Variant="Primary",
							Icon="lucide:arrow-right",
							Callback=function()
								local s,r=pcall(function()
									__config:Save()
								end)
								if(s)then
									wind:Notify({
										Title="Suisei Hub",
										Content="Config saved!",
										Duration=5
									})
								else
									wind:Notify({
										Title="Suisei Hub",
										Content="Failed to save config!\n"..r,
										Duration=5
									})
								end
							end,
						},
						{
							Title="Cancel",
							Variant="Tertiary",
							Callback=function()
								wind:Notify({
									Title="Suisei Hub",
									Content="Cancelled",
									Duration=5
								})
							end,
						}
					}
				})
			end,
		})
		__settings:Button({
			Title="Load Config",
			Callback=function()
				window:Dialog({
					Title="Suisei Hub",
					Content="Are you sure you want to load the config?",
					Icon="lucide:info",
					Buttons={
						{
							Title="Confirm",
							Variant="Primary",
							Icon="lucide:arrow-right",
							Callback=function()
								local s,r=pcall(function()
									__config:Load()
								end)
								if(s)then
									wind:Notify({
										Title="Suisei Hub",
										Content="Config loaded!",
										Duration=5
									})
								else
									wind:Notify({
										Title="Suisei Hub",
										Content="Failed to load config!\n"..r,
										Duration=5
									})
								end
							end,
						},
						{
							Title="Cancel",
							Variant="Tertiary",
							Callback=function()
								wind:Notify({
									Title="Suisei Hub",
									Content="Cancelled",
									Duration=5
								})
							end,
						}
					}
				})
			end,
		})
		__settings:Button({
			Title="Delete Config",
			Callback=function()
				window:Dialog({
					Title="Suisei Hub",
					Content="Are you sure you want to delete the config?",
					Icon="lucide:info",
					Buttons={
						{
							Title="Confirm",
							Variant="Primary",
							Icon="lucide:arrow-right",
							Callback=function()
								local s,r=pcall(function()
									__config:Delete()
								end)
								if(s)then
									wind:Notify({
										Title="Suisei Hub",
										Content="Config deleted!",
										Duration=5
									})
								else
									wind:Notify({
										Title="Suisei Hub",
										Content="Failed to delete config!\n"..r,
										Duration=5
									})
								end
							end,
						},
						{
							Title="Cancel",
							Variant="Tertiary",
							Callback=function()
								wind:Notify({
									Title="Suisei Hub",
									Content="Cancelled",
									Duration=5
								})
							end,
						}
					}
				})
			end,
		})
		local AuraRadius=__settings:Slider({
			Title="Aura Radius",
			Step=1,
			Value={
				Min=0,
				Max=128,
				Default=28
			},
			Flag="AuraRadius",
			Callback=function(value)
				config.Settings.AuraRadius.Value=value
			end,
		})
		__settings:Button({
			Title="Infinite Aura Radius (NetworkOwner)",
			Callback=function()
				config.Settings.AuraRadius.Value=10000
				AuraRadius:Set(10000)
			end,
		})
		__settings:Slider({
			Title="AimBot Speed",
			Step=1,
			Value={
				Min=1,
				Max=10,
				Default=5
			},
			Flag="AimBotSpeed",
			Callback=function(value)
				config.Settings.AimBotSpeed.Value=value
			end,
		})
		__settings:Toggle({
			Title="Ignore Friend",
			Type="Checkbox",
			Value=false,
			Flag="IgnoreFriend",
			Callback=function(value)
				config.Settings.IgnoreFriend.Value=value
			end,
		})
		__settings:Toggle({
			Title="Ignore IsInPlot",
			Type="Checkbox",
			Value=false,
			Flag="IgnoreIsInPlot",
			Callback=function(value)
				config.Settings.IgnoreIsInPlot.Value=value
			end,
		})
		__settings:Toggle({
			Title="Only Player",
			Type="Checkbox",
			Value=false,
			Flag="OnlyPlayer",
			Callback=function(value)
				config.Settings.OnlyPlayer.Value=value
			end,
		})
		__settings:Slider({
			Title="Lag Amount",
			Step=1,
			Value={
				Min=0,
				Max=4096,
				Default=1024
			},
			Flag="LagAmount",
			Callback=function(value)
				config.Settings.Lag.Value=value
			end,
		})
		__settings:Slider({
			Title="Ping Amount",
			Step=1,
			Value={
				Min=0,
				Max=4096,
				Default=1024
			},
			Flag="PingAmount",
			Callback=function(value)
				config.Settings.Ping.Value=value
			end,
		})
		__settings:Keybind({
			Title="Toggle Keybind",
			Value="C",
			Flag="ToggleKeybind",
			Callback=function(value)
				window:SetToggleKey(Enum.KeyCode[value])
				ToggleKeybind=Enum.KeyCode[value]
			end,
		})
		__settings:Dropdown({
			Title="Kick Method",
			Values={
				"Void",
				"Float"
			},
			Value="Void",
			Flag="KickMethod",
			Callback=function(value)
				config.Settings.KickMethod.Value=value
			end,
		})
		__settings:Dropdown({
			Title="SpeedHack Method",
			Values={
				"CFrame",
				"Velocity",
				"Disable"
			},
			Value="CFrame",
			Flag="SpeedHackMethod",
			Callback=function(value)
				config.Settings.SpeedHackMethod.Value=value
			end,
		})
		__settings:Toggle({
			Title="Auto SpeedHack Method",
			Type="Checkbox",
			Value=true,
			Flag="AutoSpeedHackMethod",
			Callback=function(value)
				config.Settings.AutoSpeedHackMethod.Value=value
			end,
		})
		__settings:Dropdown({
			Title="Fly Method",
			Values={
				"CFrame",
				"Velocity",
				"Disable"
			},
			Value="Velocity",
			Flag="FlyMethod",
			Callback=function(value)
				config.Settings.FlyMethod.Value=value
			end,
		})
		__settings:Toggle({
			Title="Debug Mode",
			Type="Checkbox",
			Value=false,
			Flag="DebugMode",
			Callback=function(value)
				config.Settings.DebugMode.Value=value
			end,
		})
		__settings:Button({
			Title="Rejoin",
			Callback=function()
				local s,r=pcall(function()
					wind:Notify({
						Title="Suisei Hub",
						Content="Rejoining...",
						Duration=5
					})
					service.TeleportService:Teleport(
						game.PlaceId,
						getLocalPlayer(),
						game.JobId
					)
				end)
				if(not s)then
					wind:Notify({
						Title="Suisei Hub",
						Content="Failed to rejoin!\n"..r,
						Duration=5
					})
				end
			end,
		})
	end

	do
		--// INFOS
		local DiscordLink=__infos:Code({
			Title="Discord Link",
			Code=[[https://discord.gg/ENnZ4W9eMN]]
		})
		__infos:Button({
			Title="Notes",
			Desc=[[Made by Luna!]],
			Icon=""
		})
	end

	--// SYSTEM

	task.wait(.1)

	--// INPUTS
	service.UserInputService.JumpRequest:Connect(function()
		if(config.Movements.Infjump.Value and getLocalHum())then
			getLocalHum():ChangeState(Enum.HumanoidStateType.Jumping)
		end
	end)

	local keys={W=false,A=false,S=false,D=false,Space=false,Shift=false}
	service.UserInputService.InputBegan:Connect(function(i,g)
		if(g)then return end
		local k=i.KeyCode
		if(k==Enum.KeyCode.W)then keys.W=true end
		if(k==Enum.KeyCode.A)then keys.A=true end
		if(k==Enum.KeyCode.S)then keys.S=true end
		if(k==Enum.KeyCode.D)then keys.D=true end
		if(k==Enum.KeyCode.Space)then keys.Space=true end
		if(k==Enum.KeyCode.LeftShift or k==Enum.KeyCode.RightShift)then keys.Shift=true end
	end)
	service.UserInputService.InputEnded:Connect(function(i,g)
		if(g)then return end
		local k=i.KeyCode
		if(k==Enum.KeyCode.W)then keys.W=false end
		if(k==Enum.KeyCode.A)then keys.A=false end
		if(k==Enum.KeyCode.S)then keys.S=false end
		if(k==Enum.KeyCode.D)then keys.D=false end
		if(k==Enum.KeyCode.Space)then keys.Space=false end
		if(k==Enum.KeyCode.LeftShift or k==Enum.KeyCode.RightShift)then keys.Shift=false end
	end)

	service.UserInputService.InputChanged:Connect(function(i)
		if(i.UserInputType==Enum.UserInputType.MouseWheel)then
			if(config.Combats.InfLine.Dist.Value<11)then
				config.Combats.InfLine.Dist.Value=11
			end
			if(i.Position.Z>0)then
				config.Combats.InfLine.Dist.Value+=7
			elseif(i.Position.Z<0)then
				config.Combats.InfLine.Dist.Value-=7
			end
		end
	end)

	--// SLOTS
	service.ReplicatedStorage.SlotEvents.SlotTime.OnClientEvent:Connect(function(i)
		task.spawn(function()
			config.Players.AutoSlot.Time=i
			if(config.Players.AutoSlot.Time==0 and config.Players.AutoSlot.Value)then
				local p=getLocalRoot().CFrame
				while(config.Players.AutoSlot.Time==0 and config.Players.AutoSlot.Value)do
					for _,v in ipairs(workspace.Slots:GetChildren())do
						task.wait(.1)
						local h=v.SlotHandle.Handle
						getLocalRoot().CFrame=h.CFrame
						task.wait(.2)
						SetNetworkOwner(h)
					end
				end
				task.wait(1.5)
				getLocalRoot().CFrame=p
			end
		end)
	end)

	--// GRABPARTS AND BLACKHOLE
	workspace.ChildAdded:Connect(function(v)
		--// ANTIEXPLODE
		if(config.Combats.AntiExplode.Value)then
			if(v.Name=="Part"and getLocalChar())then
				local pos=(v.Position-getLocalRoot().Position).Magnitude
				if(pos>=4)then
					getLocalRoot().Anchored=true
					task.wait(.05)
					while(get(getLocalChar(),"Ragdolled").Value)do
						service.ReplicatedStorage.CharacterEvents.Struggle:FireServer(getLocalPlayer())
						service.ReplicatedStorage.GameCorrectionEvents.StopAllVelocity:FireServer()
						task.wait()
					end
					getLocalRoot().Anchored=false
				end
			end
		end

		--// GRABPARTS
		if(v.Name=="GrabParts"and v:IsA("Model"))then
			local Owner=get(v,"Owner")
			local BeamPart=get(v,"BeamPart")
			local DragPart=get(v,"DragPart")
			local GrabPart=get(v,"GrabPart")
			local GrabAttach=get(GrabPart,"GrabAttach")
			local Grabbing=get(GrabPart,"WeldConstraint").Part1

			if(config.Settings.DebugMode.Value)then
				wind:Notify({
					Title="Suisei Hub",
					Content="Found GrabParts",
					Duration=5
				})
			end

			if(Grabbing.Anchored)then
				if(config.Settings.DebugMode.Value)then
					wind:Notify({
						Title="Suisei Hub",
						Content="Target is Anchored",
						Duration=5
					})
				end
				return
			end

			local function Grabfunc(func,...)
				task.spawn(function(...)
					if(config.Settings.DebugMode.Value)then
						wind:Notify({
							Title="Suisei Hub",
							Content="Running Grabfunc",
							Duration=5
						})
					end
					func(...)
				end,...)
			end

			--// MAINS

			Grabfunc(function()
				if(config.Combats.InfLine.Value)then
					local DragPartC=DragPart:Clone()
					DragPartC.AlignPosition.Attachment1=DragPartC.DragAttach
					DragPartC.Parent=v
					config.Combats.InfLine.Dist.Value=(DragPartC.Position-workspace.CurrentCamera.CFrame.Position).Magnitude
					DragPartC.AlignOrientation.Enabled=false
					DragPart.AlignPosition.Enabled=false
					task.spawn(function()
						while(v.Parent)do
							DragPartC.Position=workspace.CurrentCamera.CFrame.Position+workspace.CurrentCamera.CFrame.LookVector*config.Combats.InfLine.Dist.Value
							task.wait()
						end
						config.Combats.InfLine.Dist.Value=0
					end)
				end
			end)

			Grabfunc(function()
				v:GetPropertyChangedSignal("Parent"):Connect(function()
					if(not v.Parent and config.Combats.SuperStrength.Value)then
						service.UserInputService.InputBegan:Once(function(i)
							if(i.UserInputType==Enum.UserInputType.MouseButton2)then
								local vel=Instance.new("BodyVelocity",Grabbing)
								vel.MaxForce=Vector3.one*math.huge
								vel.Velocity=workspace.CurrentCamera.CFrame.LookVector*config.Combats.SuperStrength.Power.Value
								service.Debris:AddItem(vel,.1)
							end
						end)
					end
				end)
			end)
			if(config.Grabs.VoidGrab.Value)then
				Grabfunc(function()
					for _=1,3 do
						task.wait()
						SetNetworkOwner(Grabbing)
					end
					task.wait()
					Velocity(Grabbing,Vector3.new(0,1e4,0))
				end)
			end
			if(config.Grabs.KillGrab.Value)then
				Grabfunc(function()
					for _=1,3 do
						task.wait()
						SetNetworkOwner(Grabbing)
					end
					task.wait()
					MoveTo(Grabbing,CFrame.new(4096,-75,4096))
					Velocity(Grabbing,Vector3.new(0,-1e3,0))
				end)
			end
			if(config.Grabs.PoisonGrab.Value)then
				Grabfunc(function()
					for _=1,3 do
						task.wait()
						SetNetworkOwner(Grabbing)
					end
					task.wait()
					local pos=Grabbing.CFrame
					MoveTo(Grabbing,CFrame.new(58,-70,271))
				end)
			end
			if(config.Grabs.RagdollGrab.Value)then
				Grabfunc(function()
					local pos=Grabbing.CFrame
					for _=1,3 do
						task.wait()
						SetNetworkOwner(Grabbing)
					end
					task.wait()
					Velocity(Grabbing,Vector3.new(0,-256,0))
					task.wait()
					MoveTo(Grabbing,pos)
					Velocity(Grabbing,Vector3.zero)
				end)
			end
			if(config.Grabs.AnchorGrab.Value)then
				Grabfunc(function()
					anchor(Grabbing)
				end)
			end
			if(config.Grabs.DeathGrab.Value)then
				Grabfunc(function()
					for _=1,3 do
						task.wait()
						SetNetworkOwner(Grabbing)
					end
					task.wait(.1)
					cget(Grabbing.Parent,"Humanoid"):ChangeState(Enum.HumanoidStateType.Dead)
					task.wait(.5)
					ungrab(Grabbing)
				end)
			end
			if(config.Grabs.NoclipGrab.Value)then
				Grabfunc(function()
					while(Grabbing.Parent and v)do
						for _,v in ipairs(Grabbing.Parent:GetDescendants())do
							if(v:IsA("BasePart"))then
								v.CanCollide=false
							end
						end
						task.wait()
					end
				end)
			end
			if(config.Grabs.KickGrab.Value)then
				Grabfunc(function()
					local p=service.Players:GetPlayerFromCharacter(Grabbing.Parent)
					if(p)then
						local character=p.Character
						if(not character)then return end
						local root=get(character,"HumanoidRootPart")
						if(not root)then return end
						local hum=cget(character,"Humanoid")
						if(config.Settings.KickMethod.Value=="Void")then
							task.wait(.5)
							ungrab(Grabbing)
							task.spawn(function()
								while(p and root and root.Parent)do
									if(hum and hum.FloorMaterial~=Enum.Material.Air)then
										local pos=getLocalRoot().CFrame
										task.wait()
										getLocalRoot().CFrame=root.CFrame
										task.wait(.1)
										SetNetworkOwner(root)
										task.wait(.1)
										MoveTo(Grabbing,CFrame.new(25e25,25e25,25e25))
										Velocity(root,root.AssemblyLinearVelocity*25e25)
										task.wait()
										ungrab(root)
										getLocalRoot().CFrame=pos
									end
									task.wait()
								end
							end)
						elseif(config.Settings.KickMethod.Value=="Float")then
							for _=1,32 do
								SetNetworkOwner(root)
								hum:ChangeState(Enum.HumanoidStateType.Ragdoll)
								hum.Health=0
								ungrab(root)
							end
						end
					end
				end)
			end
			if(config.Miscs.Control.Value)then
				local p=service.Players:GetPlayerFromCharacter(Grabbing)
				if(not p and cget(Grabbing.Parent,"Humanoid"))then
					SetNetworkOwner(Grabbing.Parent.PrimaryPart)
					config.Miscs.Control.Target.Value=Grabbing.Parent
				end
			end
		end
	end)

	--// LOOPS
	local AntiDetectTimer=0
	local AntiBananaTimer=0
	local AuraTimer=0
	local ServerDestroyerTimer=0
	local espTimer=0
	local ExtinguisherTimer=0
	local SnipeLoopTimer=0
	local LoudAllTimer=0
	local LoopKickTimer=0
	local AntiKickDisablerTimer=0
	loop.Event:Connect(function(dt)
		--//DELTATIME/TIMER
		local altdt=1/60
		AntiDetectTimer+=dt
		AntiBananaTimer+=dt
		AuraTimer+=dt
		ServerDestroyerTimer+=dt
		espTimer+=dt
		ExtinguisherTimer+=dt
		SnipeLoopTimer+=dt
		LoudAllTimer+=dt
		LoopKickTimer+=dt
		AntiKickDisablerTimer+=dt

		--//MOVEMENTS
		if(config.Settings.AutoSpeedHackMethod.Value)then
			if(getLocalHum()and getLocalHum().Sit)then
				config.Settings.SpeedHackMethod.Value="Velocity"
			else
				config.Settings.SpeedHackMethod.Value="CFrame"
			end
		end
		if(config.Movements.CrouchSpeedHack.Loop)then
			getLocalHum().WalkSpeed=config.Movements.CrouchSpeedHack.Value
		end
		if(config.Movements.JumppowerHack.Loop)then
			getLocalHum().JumpPower=config.Movements.JumppowerHack.Value
		end
		if(config.Movements.Freeze.Value)then
			getLocalRoot().CFrame=config.Movements.Freeze.CFrame
		end
		if(config.Movements.Noclip.Value)then
			for _,v in ipairs(getLocalChar():GetDescendants())do
				if(v:IsA("BasePart"))then
					v.CanCollide=false
				end
			end
		end
		if(config.Movements.Fly.Value)then
			if(config.Settings.FlyMethod.Value=="Velocity")then
				local dir=Vector3.zero
				local look=workspace.CurrentCamera.CFrame.LookVector
				local right=workspace.CurrentCamera.CFrame.RightVector
				if(keys.W)then dir+=look end;if(keys.S)then dir-=look end
				if(keys.A)then dir-=right end;if(keys.D)then dir+=right end
				if(keys.Space)then dir+=Vector3.new(0,1,0)end;if(keys.Shift)then dir-=Vector3.new(0,1,0) end
				if(dir.Magnitude>0)then
					dir=dir.Unit*config.Movements.SpeedHack.Value
					getLocalRoot().AssemblyLinearVelocity=dir
				else
					getLocalRoot().AssemblyLinearVelocity=Vector3.zero
					getLocalRoot().CFrame=CFrame.new(getLocalRoot().Position,getLocalRoot().Position+workspace.CurrentCamera.CFrame.LookVector)
				end
			elseif(config.Settings.FlyMethod.Value=="CFrame")then
				local dir=Vector3.zero
				local look=workspace.CurrentCamera.CFrame.LookVector
				local right=workspace.CurrentCamera.CFrame.RightVector
				if(keys.W)then dir+=look end;if(keys.S)then dir-=look end
				if(keys.A)then dir-=right end;if(keys.D)then dir+=right end
				if(keys.Space)then dir+=Vector3.new(0,1,0)end;if(keys.Shift)then dir-=Vector3.new(0,1,0) end
				if(dir.Magnitude>0)then
					dir=dir.Unit*config.Movements.SpeedHack.Value*altdt
					local targetPos=getLocalRoot().Position+dir
					getLocalRoot().CFrame=getLocalRoot().CFrame:Lerp(CFrame.new(targetPos,targetPos+workspace.CurrentCamera.CFrame.LookVector),.5)
				else
					getLocalRoot().Velocity=Vector3.zero
					getLocalRoot().CFrame=CFrame.new(getLocalRoot().Position,getLocalRoot().Position+workspace.CurrentCamera.CFrame.LookVector)
				end
			end
		end

		--//PLAYERS
		if(AntiDetectTimer>=.5)then
			if(config.Players.AntiDetect.Value)then
				AntiDetect()
			end
			AntiDetectTimer=0
		end
		if(config.Players.AntiRagdoll.Value)then
			if(getLocalHum())and((getLocalHum():GetState()==Enum.HumanoidStateType.Ragdoll)or(getLocalHum():GetState()==Enum.HumanoidStateType.FallingDown))then
				ragdoll()
				getLocalHum():ChangeState()
			end
		end
		if(config.Players.AntiTouch.Value)then
			for _,v in ipairs(getLocalChar():GetDescendants())do
				if(v:IsA("BasePart"))then
					v.Locked=true
					v.CanTouch=false
					v.CanQuery=false
				end
			end
		end
		if(AntiBananaTimer>=1)then
			for _,v in ipairs(workspace:GetChildren())do
				if(v:IsA("Folder")and v.Name:find("SpawnedInToys")and not v:IsDescendantOf(getInv()))then
					local banana=get(v,"FoodBanana")
					if(banana)then
						for _,v in ipairs(banana:GetDescendants())do
							if(v:IsA("BasePart"))then
								v.CanQuery=config.Players.AntiBanana.Value
								v.CanTouch=config.Players.AntiBanana.Value
								if(config.Players.AntiBanana.Value)then
									if(IsInRadius(v,getLocalRoot().Position,8))then
										SetNetworkOwner(v)
									end
								end
							end
						end
					end
				end
			end
			AntiBananaTimer=0
		end
		if(config.Players.Ragdoll.Value)then
			ragdoll()
			getLocalHum().PlatformStand=true
		end
		if(config.Players.AntiGucci.Value)then
			ragdoll()
		end

		--//VISUALS
		if(espTimer>=1)then
			updateESP()
			if(config.Visuals.ESP.Value)then
				for _,p in ipairs(service.Players:GetPlayers())do
					local c=p.Character
					if(c)then
						addESP(c)
					end
				end
			end
		end
		if(workspace.CurrentCamera)then
			workspace.CurrentCamera.FieldOfView=config.Visuals.FOV.Value
		end
		if(config.Visuals.TPS.Value)then
			getLocalPlayer().CameraMaxZoomDistance=100000
			getLocalPlayer().CameraMode=Enum.CameraMode.Classic
			if((workspace.CurrentCamera.CFrame.Position-workspace.CurrentCamera.Focus.Position).Magnitude>getLocalPlayer().CameraMinZoomDistance)then
				service.UserInputService.MouseIconEnabled=true
			else
				service.UserInputService.MouseIconEnabled=false
			end
		else
			getLocalPlayer().CameraMode=Enum.CameraMode.LockFirstPerson
		end

		--//COMBATS
		do
			--// ANTIGRAB-REVENGE
			if(getLocalChar())then
				local head=get(getLocalChar(),"Head")
				if(head)then
					local owner=get(head,"PartOwner")
					local target
					if(owner and owner.Value)then
						target=get(service.Players,owner.Value)
					end
					task.spawn(function()
						while(owner and config.Combats.AntiGrab.Value)or(getLocalPlayer().IsHeld.Value and config.Combats.AntiGrab.Value)do
							if(getLocalRoot())then
								getLocalRoot().Anchored=true
							end
							service.ReplicatedStorage.CharacterEvents.Struggle:FireServer(getLocalPlayer())
							service.ReplicatedStorage.GameCorrectionEvents.StopAllVelocity:FireServer()
							task.wait()
						end
						if(getLocalRoot())then
							getLocalRoot().Anchored=false
						end
					end)

					do
						if(target)then
							local character=target.Character
							if(character)then
								local root=get(character,"HumanoidRootPart")
								if(root)then
									if(config.Combats.Revenge.Void.Value)then
										for _=1,3 do
											SetNetworkOwner(root)
											task.wait(.1)
											Velocity(root,Vector3.new(0,1e4,0))
											task.wait()
										end
									end
									if(config.Combats.Revenge.Kill.Value)then
										for _=1,3 do
											SetNetworkOwner(root)
											task.wait(.1)
											MoveTo(root,CFrame.new(4096,-75,4096))
											Velocity(root,Vector3.new(0,-1e3,0))
											task.wait()
										end
									end
									if(config.Combats.Revenge.Poison.Value)then
										for _=1,3 do
											SetNetworkOwner(root)
											task.wait(.1)
											MoveTo(root,CFrame.new(58,-70,271))
											task.wait()
										end
									end
									if(config.Combats.Revenge.Ragdoll.Value)then
										for _=1,3 do
											local pos=root.CFrame
											SetNetworkOwner(root)
											task.wait(.1)
											Velocity(root,Vector3.new(0,-64,0))
											task.wait()
											MoveTo(root,pos)
											Velocity(root,Vector3.zero)
											task.wait()
										end
									end
									if(config.Combats.Revenge.Death.Value)then
										for _=1,3 do
											SetNetworkOwner(root)
											task.wait(.1)
											cget(root.Parent,"Humanoid"):ChangeState(Enum.HumanoidStateType.Dead)
											task.wait(.5)
											ungrab(root)
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

		if(config.Combats.StrAntiGrab.Value and getLocalHum()and getLocalHum().Sit)then
			service.ReplicatedStorage.CharacterEvents.Struggle:FireServer(getLocalPlayer())
			service.ReplicatedStorage.GameCorrectionEvents.StopAllVelocity:FireServer()
			getLocalHum().Sit=false
			SetNetworkOwner(getLocalRoot())
		end

		if(ExtinguisherTimer>=1)then
			if(config.Combats.Extinguisher.Value)then
				if(getLocalChar()and get(getLocalHum(),"FireDebounce").Value)then
					local FireExtinguisher=spawntoy("FireExtinguisher",getLocalRoot().CFrame)
					for _,v in ipairs(FireExtinguisher:GetDescendants())do
						if(v:IsA("BasePart"))then
							v.CanCollide=false
						end
					end
					local pos=getLocalRoot().Position-Vector3.new(0,3,0)
					local look=getLocalRoot().CFrame.LookVector
					FireExtinguisher.PrimaryPart.CFrame=CFrame.new(pos,pos+look)
					task.delay(1,function()
						destroyToy(FireExtinguisher)
					end)
				end
			end
			ExtinguisherTimer=0
		end

		if(AuraTimer>=.5)then
			if(not getLocalRoot())then return end
			for _,v in ipairs(GetNearParts(getLocalRoot().Position,config.Settings.AuraRadius.Value)) do
				if(not v.Anchored and not v:IsDescendantOf(getLocalChar())and(v.Name=="HumanoidRootPart"or v.Name=="Torso"or v.Name=="Head"))then
					local p=service.Players:GetPlayerFromCharacter(v.Parent)
					if(IsFriend(p)and config.Settings.IgnoreFriend.Value)then continue end
					if(not p and config.Settings.OnlyPlayer.Value)then continue end
					if(config.Auras.VoidAura.Value)then
						task.spawn(function()
							SetNetworkOwner(v)
							v.CanCollide=false
							Velocity(v,Vector3.new(0,1e4,0))
						end)
					end
					if(config.Auras.KillAura.Value)then
						task.spawn(function()
							SetNetworkOwner(v)
							v.CanCollide=false
							MoveTo(v,CFrame.new(4096,-75,4096))
							Velocity(v,Vector3.new(0,-1e3,0))
						end)
					end
					if(config.Auras.PoisonAura.Value)then
						task.spawn(function()
							SetNetworkOwner(v)
							local pos=v.CFrame
							MoveTo(v,CFrame.new(58,-70,271))
						end)
					end
					if(config.Auras.RagdollAura.Value)then
						task.spawn(function()
							local pos=v.CFrame
							SetNetworkOwner(v)
							Velocity(v,Vector3.new(0,-256,0))
							task.wait()
							MoveTo(v,pos)
							Velocity(v,Vector3.zero)
						end)
					end
					if(config.Auras.DeathAura.Value)then
						task.spawn(function()
							SetNetworkOwner(v)
							task.wait(.1)
							cget(v.Parent,"Humanoid"):ChangeState(Enum.HumanoidStateType.Dead)
							task.wait(.5)
							ungrab(v)
						end)
					end
					if(config.Auras.AnchorAura.Value)then
						task.spawn(function()
							anchor(v)
						end)
					end
					if(config.Auras.FireAura.Value)then
						task.spawn(function()
							local e=spawntoy("Campfire",getLocalRoot().CFrame).SoundPart
							SetNetworkOwner(e)
							task.wait(.1)
							e.CFrame=v.CFrame
							task.delay(.5,destroyToy,e.Parent)
						end)
					end
					if(config.Auras.NoclipAura.Value)then
						task.spawn(function()
							SetNetworkOwner(v)
							task.wait(.1)
							v.CanCollide=false
						end)
					end
				end
			end
			AuraTimer=0
		end

		if(config.Combats.AntiVoid.Value)then
			workspace.FallenPartsDestroyHeight=0/0
			local rad=math.huge
			if(getLocalRoot())then
				local height=-87.5
				local Y=getLocalRoot().CFrame.Y
				if(height>=Y)then
					service.ReplicatedStorage.CharacterEvents.Struggle:FireServer()
					service.ReplicatedStorage.GameCorrectionEvents.StopAllVelocity:FireServer()
					local p=workspace.SpawnLocation
					local pos=(p.Position+Vector3.new(math.random(-p.Size.X*0.5,p.Size.X*0.5),math.random(0,p.Size.Y),math.random(-p.Size.Z*0.5,p.Size.Z*0.5)))
					getLocalRoot().CFrame=(CFrame.new(pos,getLocalRoot().CFrame.LookVector))
					getLocalRoot().AssemblyLinearVelocity=Vector3.zero
					getLocalRoot().AssemblyAngularVelocity=Vector3.zero
					wind:Notify({
						Title="Suisei Hub",
						Content="Suisei saved you from the void!",
						Duration=5
					})
				end
			end
		end

		if(config.Combats.AntiFar.Value)then
			if(not IsInRadius(getLocalRoot(),Vector3.zero,4096))then
				service.ReplicatedStorage.CharacterEvents.Struggle:FireServer()
				service.ReplicatedStorage.GameCorrectionEvents.StopAllVelocity:FireServer()
				local p=workspace.SpawnLocation
				local pos=(p.Position+Vector3.new(math.random(-p.Size.X*0.5,p.Size.X*0.5),math.random(0,p.Size.Y),math.random(-p.Size.Z*0.5,p.Size.Z*0.5)))
				getLocalRoot().CFrame=(CFrame.new(pos,getLocalRoot().CFrame.LookVector))
				getLocalRoot().AssemblyLinearVelocity=Vector3.zero
				getLocalRoot().AssemblyAngularVelocity=Vector3.zero
				wind:Notify({
					Title="Suisei Hub",
					Content="Suisei saved you from being too far!",
					Duration=5
				})
			end
		end

		if(config.Combats.InvisLine.Value)then
			service.ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer()
		end
		
		if(config.Combats.AimBot.Value)then
			for _,v in ipairs(GetNearParts(getLocalRoot().CFrame.Position,config.Combats.AimBot.Radius.Value))do
				if(not v.Anchored and not v:IsDescendantOf(getLocalChar()))then
					local p=service.Players:GetPlayerFromCharacter(v.Parent)
					if(IsFriend(p)and config.Settings.IgnoreFriend.Value)then continue end
					if(not p and config.Settings.OnlyPlayer.Value)then continue end
					if(v.Name~=config.Combats.AimBot.Part.Value)then continue end
					workspace.CurrentCamera.CFrame=
						workspace.CurrentCamera.CFrame:Lerp(
							CFrame.lookAt(
								workspace.CurrentCamera.CFrame.Position,
								v.Position
							),
							config.Settings.AimBotSpeed.Value/10
						)
				end
			end
		end

		--// MISCS
		if(config.Miscs.Control.Value)then
			if(not config.Miscs.Control.Target.Value)then return end
			local root=config.Miscs.Control.Target.Value.PrimaryPart
			getLocalRoot().CFrame=root.CFrame+Vector3.new(0,-10,0)
			workspace.CurrentCamera.CameraSubject=cget(root.Parent,"Humanoid")
			for _,v in ipairs(getLocalChar():GetDescendants())do
				if(v:IsA("BasePart"))then
					v.CanCollide=false
				end
			end
		end
		if(getLocalChar())then
			local ChatTyping=get(getLocalChar(),"ChatTyping")
			if(ChatTyping)then
				ChatTyping.Enabled=not config.Miscs.NoTyping.Value
			end
		end
		if(AntiKickDisablerTimer>=.5)then
			if(config.Miscs.AntiKickDisabler.Value)then
				for _,v in ipairs(GetNearParts(getLocalRoot().CFrame.Position,30))do
					if(v:IsA("BasePart")and v.Name=="StickyPart")then
						if(not v:IsDescendantOf(getInv()))then
							SetNetworkOwner(get(v.Parent,"SoundPart"))
						end
					end
				end
			end
			AntiKickDisablerTimer=0
		end

		--// AURAS
		if(getLocalChar()and getLocalRoot())then
			for _,v in ipairs(GetNearParts(getLocalRoot().Position,config.Settings.AuraRadius.Value))do
				if(not v.Anchored and not v:IsDescendantOf(getLocalChar()))then
					local p=service.Players:GetPlayerFromCharacter(v.Parent)
					if(IsFriend(p)and config.Settings.IgnoreFriend.Value)then continue end
					if(not p and config.Settings.OnlyPlayer.Value)then continue end
					if(config.Miscs.NWOAura.Value)then
						if(not p)then
							SetNetworkOwner(v)
						end
					end
					if(config.Blobman.GrabAura.Value)then
						if(v.Name~="HumanoidRootPart"or not getBlobman())then continue end
						if(p and config.Settings.IgnoreFriend.Value and IsFriend(p))then continue end
						if(p and p==getLocalPlayer())then continue end
						local blob=getBlobman()
						local side=(math.random()>=.5)and"Left"or"Right"
						blobGrab(blob,v,side)
					end
					if(config.Blobman.KickAura.Value)then
						if(not p)then continue end
						if(getLocalHum()and getLocalHum().Sit)then
							if(v.Name~="HumanoidRootPart"or not getBlobman())then continue end
							local blob=getBlobman()
							local side=(math.random()>=.5)and"Left"or"Right"
							task.spawn(function()
								blobKick(blob,v,side)
							end)
						end
					end
				end
			end
		end

		--// BLOBS
		if(config.Blobman.Noclip.Value)then
			local blob=getBlobman()
			for _,v in ipairs(blob:GetDescendants())do
				if(v:IsA("BasePart"))then
					v.CanCollide=false
				end
			end
		end
		if(LoopKickTimer>=1.5)then
			if(config.Blobman.LoopKick.Value)then
				local target=getPlayerFromName(config.Blobman.Target.Value)
				local blob=getBlobman()
				if(not blob)then
					blob=spawnBlobman()
				end
				local character=target.Character
				if(character)then
					local root=get(character,"HumanoidRootPart")
					if(root)then
						local side=(math.random()>=.5)and"Left"or"Right"
						local b=getBlobman()
						local pos=getLocalRoot().CFrame
						task.wait(.5)
						getLocalRoot().CFrame=root.CFrame
						task.wait()
						blobKick(b,root,side)
						task.wait(.5)
						getLocalRoot().CFrame=pos
					end
				end
			end
			if(config.Blobman.LoopKickAll.Value)then
				local blob=getBlobman()
				if(not blob)then
					blob=spawnBlobman()
				end
				for _,p in ipairs(service.Players:GetPlayers())do
					if(p~=getLocalPlayer()and not config.Settings.IgnoreIsInPlot.Value and not IsInPlot(p)and getLocalChar())then
						local character=p.Character
						if(character)then
							local root=get(character,"HumanoidRootPart")
							if(root)then
								local side=(math.random()>=.5)and"Left"or"Right"
								local b=getBlobman()
								local pos=getLocalRoot().CFrame
								task.wait(.25)
								getLocalRoot().CFrame=root.CFrame
								task.wait()
								blobKick(b,root,side)
								task.wait(.25)
								getLocalRoot().CFrame=pos
							end
						end
					end
				end
			end
			LoopKickTimer=0
		end

		--// SNIPES
		if(SnipeLoopTimer>=1)then
			SnipeLoopTimer=0
			if(config.Snipes.LoopVoid.Value)then
				task.spawn(function()
					local character=getPlayerFromName(config.Snipes.Target.Value).Character
					if(character)then
						local root=get(character,"HumanoidRootPart")
						if(root)then
							Snipefunc(root,function()
								Velocity(root,Vector3.new(0,1e4,0))
							end)
						end
					end
				end)
			end
			if(config.Snipes.LoopKill.Value)then
				task.spawn(function()
					local character=getPlayerFromName(config.Snipes.Target.Value).Character
					if(character)then
						local root=get(character,"HumanoidRootPart")
						if(root)then
							Snipefunc(root,function()
								MoveTo(root,CFrame.new(512,-85,512))
								Velocity(root,Vector3.new(0,-1e3,0))
							end)
						end
					end
				end)
			end
			if(config.Snipes.LoopPoison.Value)then
				task.spawn(function()
					local character=getPlayerFromName(config.Snipes.Target.Value).Character
					if(character)then
						local root=get(character,"HumanoidRootPart")
						if(root)then
							Snipefunc(root,function()
								MoveTo(root,CFrame.new(58,-70,271))
							end)
						end
					end
				end)
			end
			if(config.Snipes.LoopRagdoll.Value)then
				task.spawn(function()
					local character=getPlayerFromName(config.Snipes.Target.Value).Character
					if(character)then
						local root=get(character,"HumanoidRootPart")
						if(root)then
							Snipefunc(root,function()
								local rpos=root.CFrame
								Velocity(root,Vector3.new(0,-64,0))
								task.wait(.1)
								MoveTo(root,rpos)
								Velocity(root,Vector3.zero)
							end)
						end
					end
				end)
			end
			if(config.Snipes.LoopDeath.Value)then
				task.spawn(function()
					local character=getPlayerFromName(config.Snipes.Target.Value).Character
					if(character)then
						local root=get(character,"HumanoidRootPart")
						if(root)then
							Snipefunc(root,function()
								cget(root.Parent,"Humanoid"):ChangeState(Enum.HumanoidStateType.Dead)
								task.wait(.5)
								ungrab(root)
							end)
						end
					end
				end)
			end
		end

		--// TROLLS
		if(LoudAllTimer>=.5)then
			if(config.Trolls.LoudAll.Value)then
				local pos=getLocalRoot().CFrame
				local SoundMain=config.Trolls.LoudAll.SoundPart.Value
				if(SoundMain)then
					for _,p in ipairs(service.Players:GetPlayers())do
						if(p~=getLocalPlayer()and not config.Settings.IgnoreIsInPlot.Value and not IsInPlot(p)and getLocalChar())then
							local SoundPart=SoundMain.SoundPart
							getLocalRoot().CFrame=SoundPart.CFrame
							task.wait()
							SetNetworkOwner(SoundPart)
							task.wait()
							local root=get(p.Character,"HumanoidRootPart")
							if(not root)then continue end
							MoveTo(SoundPart,CFrame.new(58,-70,271))
						end
					end
					task.delay(.1,function()
						getLocalRoot().CFrame=pos
					end)
				end
			end
			LoudAllTimer=0
		end
		if(config.Trolls.Lag.Value)then
			lag(config.Settings.Lag.Value)
		end
		if(config.Trolls.Ping.Value)then
			ping(config.Settings.Ping.Value)
		end

		local side="Left"
		if(ServerDestroyerTimer>=.05)then
			if(config.Trolls.ServerDestroyer.Value)then
				task.spawn(function()
					for _=1,4 do
						local pos=config.Trolls.ServerDestroyer.CFrame
						local blob=getBlobman()
						if(not blob)then
							blob=spawnBlobman()
						end
						if(not getLocalHum().Sit)then
							blob.VehicleSeat:Sit(getLocalHum())
						end
						if(blob and getLocalHum().Sit)then
							side=(side=="Left"and"Right")or"Left"
							local players=service.Players:GetPlayers()
							for i=#players,2,-1 do
								local j=math.random(i)
								players[i],players[j]=players[j],players[i]
							end
							for _,v in ipairs(players)do
								if(v~=getLocalPlayer())then
									if(IsFriend(v)and config.Settings.IgnoreFriend.Value)or(not config.Settings.IgnoreIsInPlot.Value and IsInPlot(v))then continue end
									local char=v.Character
									if(char)then
										local root=get(char,"HumanoidRootPart")
										if(root and IsInRadius(root,Vector3.zero,4096)and root.CFrame.Y>=-95)then
											getLocalRoot().CFrame=root.CFrame
											task.wait()
											blobGrab(blob,root,side)
											task.wait()
											getLocalRoot().CFrame=pos
											Velocity(getLocalRoot(),Vector3.zero)
										end
									end
								end
							end
							destroyToy(blob)
							task.wait(.1)
							blob=spawnBlobman()
						end
					end
				end)
			end
			ServerDestroyerTimer=0
		end

		if(config.Trolls.ChaosLine.Value)then
			for _=1,2 do
				local c=0
				local p=nil
				for _,v in ipairs(service.Players:GetPlayers())do
					if(v==getLocalPlayer())then continue end
					c+=1
					if(math.random(c)==1)then
						p=v
					end
				end
				if(p)then
					local t=p.Character
					if(not t)then continue end
					local r=get(t,"HumanoidRootPart")or get(t,"Torso")
					createLine(r)
				end
			end
		end

		--//SETTINGS
		local function getMoveDir()
			local look=Vector3.new(workspace.CurrentCamera.CFrame.LookVector.X,0,workspace.CurrentCamera.CFrame.LookVector.Z)
			local right=Vector3.new(workspace.CurrentCamera.CFrame.RightVector.X,0,workspace.CurrentCamera.CFrame.RightVector.Z)
			local dir=Vector3.zero
			if(keys.W)then dir+=look end
			if(keys.S)then dir-=look end
			if(keys.A)then dir-=right end
			if(keys.D)then dir+=right end
			if(dir.Magnitude>0)then dir=dir.Unit*config.Movements.SpeedHack.Value end
			return dir
		end

		--// WALKSPEED
		if(config.Settings.SpeedHackMethod.Value=="Velocity")then
			local dir=getMoveDir()
			local root=getLocalRoot()
			if(dir.Magnitude>0)then
				root.AssemblyLinearVelocity=Vector3.new(dir.X,root.AssemblyLinearVelocity.Y,dir.Z)
			else
				root.AssemblyLinearVelocity=Vector3.new(0,root.AssemblyLinearVelocity.Y,0)
			end
		elseif(config.Settings.SpeedHackMethod.Value=="CFrame")then
			local dir=getMoveDir()*altdt
			if(dir.Magnitude>0)then
				local root=getLocalRoot()
				local targetPos=root.Position+Vector3.new(dir.X,0,dir.Z)
				local look=Vector3.new(root.CFrame.LookVector.X,0,root.CFrame.LookVector.Z)
				root.CFrame=root.CFrame:Lerp(CFrame.new(targetPos,targetPos+look),altdt*10)
			end
		end

		--// CONTROL
		if(config.Miscs.Control.Value)then
			if(config.Miscs.Control.Target.Value)then
				local root=config.Miscs.Control.Target.Value.PrimaryPart
				local dir=getMoveDir()
				if(dir.Magnitude>0)then
					root.AssemblyLinearVelocity=Vector3.new(dir.X,root.AssemblyLinearVelocity.Y,dir.Z)
				else
					root.AssemblyLinearVelocity=Vector3.new(0,root.AssemblyLinearVelocity.Y,0)
				end
			end
		end
	end)
end
