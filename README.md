--====================================================
-- FPS OPTIMIZER SYSTEM
-- Criador: Frostzn
--====================================================

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Terrain = workspace.Terrain
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

----------------------------------------------------
-- DRAG FUNCTION
----------------------------------------------------
local function MakeDraggable(frame)
	local dragging, dragStart, startPos
	frame.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			dragStart = input.Position
			startPos = frame.Position
		end
	end)
	UIS.InputChanged:Connect(function(input)
		if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
			local delta = input.Position - dragStart
			frame.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)
	UIS.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = false
		end
	end)
end

----------------------------------------------------
-- ORIGINAL SETTINGS
----------------------------------------------------
local Original = {
	Quality = settings().Rendering.QualityLevel,
	Shadows = Lighting.GlobalShadows,
	Specular = Lighting.EnvironmentSpecularScale,
	Water = {
		WaveSize = Terrain.WaterWaveSize,
		WaveSpeed = Terrain.WaterWaveSpeed,
		Transparency = Terrain.WaterTransparency
	}
}

----------------------------------------------------
-- KEYS SYSTEM
----------------------------------------------------
local ADMIN_KEY = "261120"
local MAX_USES = 3
local Keys = {}

for i = 1,1000 do
	Keys["FPS-"..(100000+i)] = MAX_USES
end

----------------------------------------------------
-- LOADING SCREEN
----------------------------------------------------
local LoadingGui = Instance.new("ScreenGui", PlayerGui)
LoadingGui.IgnoreGuiInset = true

local bg = Instance.new("Frame", LoadingGui)
bg.Size = UDim2.fromScale(1,1)
bg.BackgroundColor3 = Color3.fromRGB(10,10,10)

local text = Instance.new("TextLabel", bg)
text.Size = UDim2.new(1,0,0,40)
text.Position = UDim2.new(0,0,0.45,0)
text.Text = "Carregando FPS Optimizer..."
text.Font = Enum.Font.GothamBold
text.TextSize = 18
text.TextColor3 = Color3.fromRGB(0,255,120)
text.BackgroundTransparency = 1

local barBg = Instance.new("Frame", bg)
barBg.Size = UDim2.new(0.4,0,0,10)
barBg.Position = UDim2.new(0.3,0,0.53,0)
barBg.BackgroundColor3 = Color3.fromRGB(40,40,40)

local bar = Instance.new("Frame", barBg)
bar.Size = UDim2.new(0,0,1,0)
bar.BackgroundColor3 = Color3.fromRGB(0,170,90)

for i = 1,100 do
	bar.Size = UDim2.new(i/100,0,1,0)
	task.wait(0.02)
end

LoadingGui:Destroy()

----------------------------------------------------
-- FPS OPTIMIZER MENU
----------------------------------------------------
local function OpenOptimizer()
	local gui = Instance.new("ScreenGui", PlayerGui)

	local main = Instance.new("Frame", gui)
	main.Size = UDim2.fromOffset(360,520)
	main.Position = UDim2.new(0.5,-180,0.5,-260)
	main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	MakeDraggable(main)

	local header = Instance.new("TextLabel", main)
	header.Size = UDim2.new(1,0,0,40)
	header.Text = "FPS OPTIMIZER"
	header.Font = Enum.Font.GothamBold
	header.TextSize = 16
	header.TextColor3 = Color3.fromRGB(0,255,120)
	header.BackgroundTransparency = 1

	local function Toggle(text, on, off, y)
		local state = false
		local b = Instance.new("TextButton", main)
		b.Size = UDim2.new(0.9,0,0,36)
		b.Position = UDim2.new(0.05,0,0,y)
		b.Text = text.." [OFF]"
		b.Font = Enum.Font.Gotham
		b.TextSize = 14
		b.BackgroundColor3 = Color3.fromRGB(40,40,40)
		b.TextColor3 = Color3.new(1,1,1)

		b.MouseButton1Click:Connect(function()
			state = not state
			if state then
				on()
				b.Text = text.." [ON]"
				b.BackgroundColor3 = Color3.fromRGB(0,170,90)
			else
				if off then off() end
				b.Text = text.." [OFF]"
				b.BackgroundColor3 = Color3.fromRGB(40,40,40)
			end
		end)
	end

	Toggle("FPS BOOST", function()
		settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
	end, function()
		settings().Rendering.QualityLevel = Original.Quality
	end, 60)

	Toggle("REMOVER SOMBRAS", function()
		Lighting.GlobalShadows = false
	end, function()
		Lighting.GlobalShadows = Original.Shadows
	end, 104)

	Toggle("OTIMIZAR ÁGUA", function()
		Terrain.WaterWaveSize = 0
		Terrain.WaterWaveSpeed = 0
		Terrain.WaterTransparency = 1
	end, function()
		Terrain.WaterWaveSize = Original.Water.WaveSize
		Terrain.WaterWaveSpeed = Original.Water.WaveSpeed
		Terrain.WaterTransparency = Original.Water.Transparency
	end, 148)

	local credit = Instance.new("TextLabel", main)
	credit.Size = UDim2.new(1,0,0,20)
	credit.Position = UDim2.new(0,0,1,-22)
	credit.Text = "Criador: Frostzn"
	credit.Font = Enum.Font.Gotham
	credit.TextSize = 12
	credit.TextColor3 = Color3.fromRGB(150,150,150)
	credit.BackgroundTransparency = 1
end

----------------------------------------------------
-- ADMIN PANEL
----------------------------------------------------
local function OpenAdmin()
	local gui = Instance.new("ScreenGui", PlayerGui)

	local main = Instance.new("Frame", gui)
	main.Size = UDim2.fromOffset(420,500)
	main.Position = UDim2.new(0.5,-210,0.5,-250)
	main.BackgroundColor3 = Color3.fromRGB(20,20,20)
	MakeDraggable(main)

	local title = Instance.new("TextLabel", main)
	title.Size = UDim2.new(1,0,0,40)
	title.Text = "PAINEL ADMIN"
	title.Font = Enum.Font.GothamBold
	title.TextSize = 16
	title.TextColor3 = Color3.fromRGB(255,170,0)
	title.BackgroundTransparency = 1

	local scroll = Instance.new("ScrollingFrame", main)
	scroll.Position = UDim2.new(0,10,0,50)
	scroll.Size = UDim2.new(1,-20,1,-60)
	scroll.CanvasSize = UDim2.new(0,0,0,1000)
	scroll.BackgroundTransparency = 1

	local y = 0
	for k,v in pairs(Keys) do
		local l = Instance.new("TextLabel", scroll)
		l.Size = UDim2.new(1,0,0,20)
		l.Position = UDim2.new(0,0,0,y)
		l.Text = k.." | Usos: "..v
		l.Font = Enum.Font.Code
		l.TextSize = 13
		l.TextXAlignment = Left
		l.TextColor3 = Color3.new(1,1,1)
		l.BackgroundTransparency = 1
		y += 22
	end
end

----------------------------------------------------
-- KEY MENU
----------------------------------------------------
local KeyGui = Instance.new("ScreenGui", PlayerGui)

local box = Instance.new("Frame", KeyGui)
box.Size = UDim2.fromOffset(300,170)
box.Position = UDim2.new(0.5,-150,0.5,-85)
box.BackgroundColor3 = Color3.fromRGB(20,20,20)
MakeDraggable(box)

local title = Instance.new("TextLabel", box)
title.Size = UDim2.new(1,0,0,36)
title.Text = "INSIRA SUA KEY"
title.Font = Enum.Font.GothamBold
title.TextSize = 15
title.TextColor3 = Color3.fromRGB(0,255,120)
title.BackgroundTransparency = 1

local input = Instance.new("TextBox", box)
input.Size = UDim2.new(0.9,0,0,30)
input.Position = UDim2.new(0.05,0,0,50)
input.PlaceholderText = "FPS-100001"
input.Text = ""
input.Font = Enum.Font.Gotham
input.TextSize = 14
input.BackgroundColor3 = Color3.fromRGB(35,35,35)
input.TextColor3 = Color3.new(1,1,1)

local status = Instance.new("TextLabel", box)
status.Size = UDim2.new(1,0,0,18)
status.Position = UDim2.new(0,0,0,85)
status.Text = ""
status.Font = Enum.Font.Gotham
status.TextSize = 12
status.BackgroundTransparency = 1

local btn = Instance.new("TextButton", box)
btn.Size = UDim2.new(0.5,0,0,30)
btn.Position = UDim2.new(0.25,0,1,-40)
btn.Text = "Confirmar"
btn.Font = Enum.Font.GothamBold
btn.TextSize = 14
btn.BackgroundColor3 = Color3.fromRGB(0,170,90)
btn.TextColor3 = Color3.new(1,1,1)

btn.MouseButton1Click:Connect(function()
	local key = input.Text

	if key == ADMIN_KEY then
		KeyGui:Destroy()
		OpenAdmin()
		return
	end

	if Keys[key] and Keys[key] > 0 then
		Keys[key] -= 1
		KeyGui:Destroy()
		OpenOptimizer()
	else
		status.Text = "Key inválida ou sem usos"
		status.TextColor3 = Color3.fromRGB(255,80,80)
		input.Text = ""
	end
end)
 
