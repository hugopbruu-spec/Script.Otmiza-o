--====================================================
-- FPS OPTIMIZER SYSTEM (BASE ESTÁVEL)
-- Criador: Frostzn
--====================================================

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Terrain = workspace.Terrain
local UIS = game:GetService("UserInputService")

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

---------------- DRAG ----------------
local function drag(frame)
	local dragging, startPos, startInput
	frame.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			startPos = frame.Position
			startInput = i.Position
		end
	end)
	UIS.InputChanged:Connect(function(i)
		if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then
			local delta = i.Position - startInput
			frame.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)
	UIS.InputEnded:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = false
		end
	end)
end

---------------- ORIGINAL SETTINGS ----------------
local Original = {
	Quality = settings().Rendering.QualityLevel,
	Shadows = Lighting.GlobalShadows,
	Water = {
		WaveSize = Terrain.WaterWaveSize,
		WaveSpeed = Terrain.WaterWaveSpeed,
		Transparency = Terrain.WaterTransparency
	}
}

---------------- KEYS ----------------
local ADMIN_KEY = "261120"
local MAX_USES = 3
local Keys = {}

math.randomseed(123)
for i = 1,1000 do
	local key = "FPS-" .. tostring(100000 + i)
	Keys[key] = MAX_USES
end

--------------------------------------------------
-- FPS OPTIMIZER MENU
--------------------------------------------------
local function OpenFPSOptimizer()
	local gui = Instance.new("ScreenGui", PlayerGui)

	local main = Instance.new("Frame", gui)
	main.Size = UDim2.fromOffset(350,480)
	main.Position = UDim2.new(0.5,-175,0.5,-240)
	main.BackgroundColor3 = Color3.fromRGB(20,20,20)
	main.BorderSizePixel = 0
	drag(main)

	local title = Instance.new("TextLabel", main)
	title.Size = UDim2.new(1,0,0,40)
	title.Text = "FPS OPTIMIZER"
	title.Font = Enum.Font.GothamBold
	title.TextSize = 16
	title.TextColor3 = Color3.fromRGB(0,255,120)
	title.BackgroundTransparency = 1

	local function toggle(text, on, off)
		local state = false
		local b = Instance.new("TextButton", main)
		b.Size = UDim2.new(0.9,0,0,34)
		b.Position = UDim2.new(0.05,0,0,60 + (#main:GetChildren()*36))
		b.Text = text .. " [OFF]"
		b.Font = Enum.Font.Gotham
		b.TextSize = 14
		b.BackgroundColor3 = Color3.fromRGB(40,40,40)
		b.TextColor3 = Color3.new(1,1,1)

		b.MouseButton1Click:Connect(function()
			state = not state
			if state then
				on()
				b.Text = text .. " [ON]"
				b.BackgroundColor3 = Color3.fromRGB(0,170,90)
			else
				if off then off() end
				b.Text = text .. " [OFF]"
				b.BackgroundColor3 = Color3.fromRGB(40,40,40)
			end
		end)
	end

	toggle("FPS BOOST", function()
		settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
	end, function()
		settings().Rendering.QualityLevel = Original.Quality
	end)

	toggle("DESATIVAR SOMBRAS", function()
		Lighting.GlobalShadows = false
	end, function()
		Lighting.GlobalShadows = Original.Shadows
	end)

	toggle("OTIMIZAR ÁGUA", function()
		Terrain.WaterWaveSize = 0
		Terrain.WaterWaveSpeed = 0
		Terrain.WaterTransparency = 1
	end, function()
		Terrain.WaterWaveSize = Original.Water.WaveSize
		Terrain.WaterWaveSpeed = Original.Water.WaveSpeed
		Terrain.WaterTransparency = Original.Water.Transparency
	end)

	local credit = Instance.new("TextLabel", main)
	credit.Size = UDim2.new(1,0,0,20)
	credit.Position = UDim2.new(0,0,1,-22)
	credit.Text = "Criador: Frostzn"
	credit.Font = Enum.Font.Gotham
	credit.TextSize = 12
	credit.TextColor3 = Color3.fromRGB(150,150,150)
	credit.BackgroundTransparency = 1
end

--------------------------------------------------
-- ADMIN PANEL
--------------------------------------------------
local function OpenAdminPanel()
	local gui = Instance.new("ScreenGui", PlayerGui)

	local main = Instance.new("Frame", gui)
	main.Size = UDim2.fromOffset(420,520)
	main.Position = UDim2.new(0.5,-210,0.5,-260)
	main.BackgroundColor3 = Color3.fromRGB(20,20,20)
	drag(main)

	local title = Instance.new("TextLabel", main)
	title.Size = UDim2.new(1,0,0,40)
	title.Text = "PAINEL ADMIN - KEYS"
	title.Font = Enum.Font.GothamBold
	title.TextSize = 16
	title.TextColor3 = Color3.fromRGB(255,170,0)
	title.BackgroundTransparency = 1

	local scroll = Instance.new("ScrollingFrame", main)
	scroll.Position = UDim2.new(0,10,0,50)
	scroll.Size = UDim2.new(1,-20,1,-60)
	scroll.CanvasSize = UDim2.new(0,0,0,1000)
	scroll.ScrollBarImageTransparency = 0.3
	scroll.BackgroundTransparency = 1

	local y = 0
	for key,uses in pairs(Keys) do
		local l = Instance.new("TextLabel", scroll)
		l.Size = UDim2.new(1,0,0,20)
		l.Position = UDim2.new(0,0,0,y)
		l.Text = key .. " | Usos: " .. uses
		l.Font = Enum.Font.Code
		l.TextSize = 13
		l.TextXAlignment = Left
		l.TextColor3 = Color3.new(1,1,1)
		l.BackgroundTransparency = 1
		y += 22
	end
end

--------------------------------------------------
-- KEY MENU
--------------------------------------------------
local gui = Instance.new("ScreenGui", PlayerGui)

local box = Instance.new("Frame", gui)
box.Size = UDim2.fromOffset(280,160)
box.Position = UDim2.new(0.5,-140,0.5,-80)
box.BackgroundColor3 = Color3.fromRGB(20,20,20)
drag(box)

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
		gui:Destroy()
		OpenAdminPanel()
		return
	end

	if Keys[key] and Keys[key] > 0 then
		Keys[key] -= 1
		status.Text = "Key válida!"
		status.TextColor3 = Color3.fromRGB(0,255,120)
		task.wait(0.5)
		gui:Destroy()
		OpenFPSOptimizer()
	else
		status.Text = "Key inválida ou sem usos"
		status.TextColor3 = Color3.fromRGB(255,80,80)
		input.Text = ""
	end
end)
