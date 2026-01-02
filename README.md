--[[=====================================================
 FPS OPTIMIZER
 Criador: Frostzn
 Versão: 1.2 Beta
=======================================================]]

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local TweenService = game:GetService("TweenService")
local UIS = game:GetService("UserInputService")
local Terrain = workspace:WaitForChild("Terrain")

local player = Players.LocalPlayer
local guiParent = player:WaitForChild("PlayerGui")

--------------------------------------------------
-- DRAG
--------------------------------------------------
local function makeDraggable(frame)
	frame.Active = true
	frame.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			local startPos = frame.Position
			local start = input.Position
			local conn
			conn = UIS.InputChanged:Connect(function(i)
				if i.UserInputType == Enum.UserInputType.MouseMovement then
					local delta = i.Position - start
					frame.Position = UDim2.new(
						startPos.X.Scale, startPos.X.Offset + delta.X,
						startPos.Y.Scale, startPos.Y.Offset + delta.Y
					)
				end
			end)
			UIS.InputEnded:Once(function()
				if conn then conn:Disconnect() end
			end)
		end
	end)
end

--------------------------------------------------
-- ORIGINAL VALUES
--------------------------------------------------
local Original = {
	Quality = settings().Rendering.QualityLevel,
	GlobalShadows = Lighting.GlobalShadows,
	Specular = Lighting.EnvironmentSpecularScale,
	Water = {
		WaveSize = Terrain.WaterWaveSize,
		WaveSpeed = Terrain.WaterWaveSpeed,
		Transparency = Terrain.WaterTransparency
	}
}

--------------------------------------------------
-- KEYS
--------------------------------------------------
local ADMIN_KEY = "261120"
local Keys = {}
local MAX_USES = 3

math.randomseed(1337)
for i = 1,1000 do
	local key = "FPS-"..math.random(100000,999999).."-"..i
	Keys[key] = MAX_USES
end

--------------------------------------------------
-- LOADING SCREEN
--------------------------------------------------
local LoadGui = Instance.new("ScreenGui", guiParent)
LoadGui.IgnoreGuiInset = true

local bg = Instance.new("Frame", LoadGui)
bg.Size = UDim2.fromScale(1,1)
bg.BackgroundColor3 = Color3.fromRGB(12,12,12)

local title = Instance.new("TextLabel", bg)
title.Size = UDim2.new(1,0,0,40)
title.Position = UDim2.new(0,0,0.42,0)
title.Text = "FPS OPTIMIZER"
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.TextColor3 = Color3.fromRGB(0,255,140)
title.BackgroundTransparency = 1

local percent = Instance.new("TextLabel", bg)
percent.Size = UDim2.new(1,0,0,30)
percent.Position = UDim2.new(0,0,0.47,0)
percent.Text = "0%"
percent.Font = Enum.Font.Gotham
percent.TextSize = 16
percent.TextColor3 = Color3.fromRGB(200,200,200)
percent.BackgroundTransparency = 1

local barBG = Instance.new("Frame", bg)
barBG.Size = UDim2.new(0.4,0,0,12)
barBG.Position = UDim2.new(0.3,0,0.52,0)
barBG.BackgroundColor3 = Color3.fromRGB(35,35,35)
Instance.new("UICorner", barBG)

local bar = Instance.new("Frame", barBG)
bar.Size = UDim2.new(0,0,1,0)
bar.BackgroundColor3 = Color3.fromRGB(0,170,100)
Instance.new("UICorner", bar)

for i = 0,100 do
	percent.Text = i.."%"
	bar.Size = UDim2.new(i/100,0,1,0)
	task.wait(0.02)
end

LoadGui:Destroy()

--------------------------------------------------
-- FPS OPTIMIZER
--------------------------------------------------
function openFPSOptimizer()
	local gui = Instance.new("ScreenGui", guiParent)

	local Main = Instance.new("Frame", gui)
	Main.Size = UDim2.fromOffset(380,600)
	Main.Position = UDim2.new(0.5,-190,0.5,-300)
	Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", Main)
	makeDraggable(Main)

	-- HEADER
	local Header = Instance.new("Frame", Main)
	Header.Size = UDim2.new(1,0,0,46)
	Header.BackgroundColor3 = Color3.fromRGB(22,22,22)

	local Title = Instance.new("TextLabel", Header)
	Title.Size = UDim2.new(1,-80,1,0)
	Title.Position = UDim2.new(0,10,0,0)
	Title.Text = "🚀 FPS OPTIMIZER | v1.2 Beta"
	Title.Font = Enum.Font.GothamBold
	Title.TextSize = 16
	Title.TextColor3 = Color3.fromRGB(0,255,140)
	Title.BackgroundTransparency = 1

	local Minimize = Instance.new("TextButton", Header)
	Minimize.Size = UDim2.fromOffset(32,32)
	Minimize.Position = UDim2.new(1,-70,0,7)
	Minimize.Text = "-"
	Minimize.Font = Enum.Font.GothamBold
	Minimize.TextSize = 22
	Minimize.BackgroundTransparency = 1
	Minimize.TextColor3 = Color3.new(1,1,1)

	local Close = Instance.new("TextButton", Header)
	Close.Size = UDim2.fromOffset(32,32)
	Close.Position = UDim2.new(1,-36,0,7)
	Close.Text = "X"
	Close.Font = Enum.Font.GothamBold
	Close.TextSize = 16
	Close.TextColor3 = Color3.fromRGB(255,90,90)
	Close.BackgroundTransparency = 1

	-- MINI BUTTON
	local Mini = Instance.new("TextButton", gui)
	Mini.Size = UDim2.fromOffset(50,50)
	Mini.Position = UDim2.new(0,20,0.5,-25)
	Mini.Text = "FPS"
	Mini.Font = Enum.Font.GothamBold
	Mini.TextSize = 16
	Mini.TextColor3 = Color3.new(1,1,1)
	Mini.BackgroundColor3 = Color3.fromRGB(0,170,100)
	Mini.Visible = false
	Instance.new("UICorner", Mini)
	makeDraggable(Mini)

	Minimize.MouseButton1Click:Connect(function()
		Main.Visible = false
		Mini.Visible = true
	end)

	Mini.MouseButton1Click:Connect(function()
		Main.Visible = true
		Mini.Visible = false
	end)

	Close.MouseButton1Click:Connect(function()
		gui:Destroy()
	end)

	-- SCROLL
	local Scroll = Instance.new("ScrollingFrame", Main)
	Scroll.Position = UDim2.new(0,10,0,56)
	Scroll.Size = UDim2.new(1,-20,1,-120)
	Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
	Scroll.CanvasSize = UDim2.new(0,0,0,0)
	Scroll.BackgroundTransparency = 1

	local Layout = Instance.new("UIListLayout", Scroll)
	Layout.Padding = UDim.new(0,8)

	local function toggle(text,on,off)
		local state=false
		local b=Instance.new("TextButton",Scroll)
		b.Size=UDim2.new(1,0,0,38)
		b.Text=text.." [OFF]"
		b.Font=Enum.Font.Gotham
		b.TextSize=14
		b.TextColor3=Color3.new(1,1,1)
		b.BackgroundColor3=Color3.fromRGB(40,40,40)
		Instance.new("UICorner",b)
		b.MouseButton1Click:Connect(function()
			state=not state
			if state then
				on()
				b.Text=text.." [ON]"
				b.BackgroundColor3=Color3.fromRGB(0,160,90)
			else
				if off then off() end
				b.Text=text.." [OFF]"
				b.BackgroundColor3=Color3.fromRGB(40,40,40)
			end
		end)
	end

	toggle("⚡ FPS Boost",function()
		settings().Rendering.QualityLevel=Enum.QualityLevel.Level01
	end,function()
		settings().Rendering.QualityLevel=Original.Quality
	end)

	toggle("🎯 Limite FPS 120",function()
		RunService:Set3dRenderingEnabled(true)
	end)

	toggle("💡 Otimizar Iluminação",function()
		Lighting.GlobalShadows=false
	end,function()
		Lighting.GlobalShadows=Original.GlobalShadows
	end)

	toggle("🌊 Otimizar Água",function()
		Terrain.WaterWaveSize=0
		Terrain.WaterWaveSpeed=0
		Terrain.WaterTransparency=1
	end,function()
		Terrain.WaterWaveSize=Original.Water.WaveSize
		Terrain.WaterWaveSpeed=Original.Water.WaveSpeed
		Terrain.WaterTransparency=Original.Water.Transparency
	end)

	local Credit = Instance.new("TextLabel", Main)
	Credit.Size = UDim2.new(1,0,0,20)
	Credit.Position = UDim2.new(0,0,1,-22)
	Credit.Text = "Criador: Frostzn"
	Credit.Font = Enum.Font.Gotham
	Credit.TextSize = 12
	Credit.TextColor3 = Color3.fromRGB(150,150,150)
	Credit.BackgroundTransparency = 1
end

--------------------------------------------------
-- ADMIN PANEL
--------------------------------------------------
function openAdminPanel()
	local gui=Instance.new("ScreenGui",guiParent)
	local f=Instance.new("Frame",gui)
	f.Size=UDim2.fromOffset(420,520)
	f.Position=UDim2.new(0.5,-210,0.5,-260)
	f.BackgroundColor3=Color3.fromRGB(18,18,18)
	Instance.new("UICorner",f)
	makeDraggable(f)

	local title=Instance.new("TextLabel",f)
	title.Size=UDim2.new(1,0,0,40)
	title.Text="🔐 ADMIN PANEL"
	title.Font=Enum.Font.GothamBold
	title.TextSize=18
	title.TextColor3=Color3.fromRGB(0,255,140)
	title.BackgroundTransparency=1

	local list=Instance.new("ScrollingFrame",f)
	list.Position=UDim2.new(0,10,0,46)
	list.Size=UDim2.new(1,-20,1,-56)
	list.AutomaticCanvasSize=Enum.AutomaticSize.Y
	list.BackgroundTransparency=1

	local lay=Instance.new("UIListLayout",list)
	lay.Padding=UDim.new(0,4)

	for k,v in pairs(Keys) do
		local t=Instance.new("TextLabel",list)
		t.Size=UDim2.new(1,0,0,22)
		t.Text=k.." | usos: "..v
		t.Font=Enum.Font.Gotham
		t.TextSize=12
		t.TextColor3=Color3.new(1,1,1)
		t.BackgroundTransparency=1
	end
end

--------------------------------------------------
-- KEY MENU
--------------------------------------------------
local KeyGui=Instance.new("ScreenGui",guiParent)
local f=Instance.new("Frame",KeyGui)
f.Size=UDim2.fromOffset(360,240)
f.Position=UDim2.new(0.5,-180,0.5,-120)
f.BackgroundColor3=Color3.fromRGB(18,18,18)
Instance.new("UICorner",f)
makeDraggable(f)

local t=Instance.new("TextLabel",f)
t.Size=UDim2.new(1,0,0,40)
t.Text="🔑 DIGITE SUA KEY"
t.Font=Enum.Font.GothamBold
t.TextSize=18
t.TextColor3=Color3.fromRGB(0,255,140)
t.BackgroundTransparency=1

local box=Instance.new("TextBox",f)
box.Size=UDim2.new(0.9,0,0,40)
box.Position=UDim2.new(0.05,0,0,70)
box.PlaceholderText="FPS-XXXXXX"
box.TextColor3=Color3.new(1,1,1)
box.PlaceholderColor3=Color3.fromRGB(150,150,150)
box.BackgroundColor3=Color3.fromRGB(30,30,30)
Instance.new("UICorner",box)

local info=Instance.new("TextLabel",f)
info.Size=UDim2.new(1,0,0,20)
info.Position=UDim2.new(0,0,0,120)
info.BackgroundTransparency=1
info.TextColor3=Color3.fromRGB(255,80,80)

local btn=Instance.new("TextButton",f)
btn.Size=UDim2.new(0.5,0,0,36)
btn.Position=UDim2.new(0.25,0,1,-50)
btn.Text="Confirmar"
btn.Font=Enum.Font.GothamBold
btn.TextSize=14
btn.TextColor3=Color3.new(1,1,1)
btn.BackgroundColor3=Color3.fromRGB(0,170,100)
Instance.new("UICorner",btn)

btn.MouseButton1Click:Connect(function()
	local k=box.Text
	if k==ADMIN_KEY then
		KeyGui:Destroy()
		openAdminPanel()
	elseif Keys[k] and Keys[k]>0 then
		Keys[k]-=1
		KeyGui:Destroy()
		openFPSOptimizer()
	else
		info.Text="❌ Key inválida"
		box.Text=""
	end
end)
