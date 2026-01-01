--[[
 ROBLOX FPS OPTIMIZER
 Criador: Frostzn
 Versão: 1.2 Beta
]]

--------------------------------------------------
-- SERVICES
--------------------------------------------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local TweenService = game:GetService("TweenService")
local UIS = game:GetService("UserInputService")
local Terrain = workspace:WaitForChild("Terrain")

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

--------------------------------------------------
-- SAVE ORIGINAL VALUES
--------------------------------------------------
local Original = {
	Quality = settings().Rendering.QualityLevel,
	Lighting = {
		GlobalShadows = Lighting.GlobalShadows,
		Specular = Lighting.EnvironmentSpecularScale
	},
	Water = {
		WaveSize = Terrain.WaterWaveSize,
		WaveSpeed = Terrain.WaterWaveSpeed,
		Transparency = Terrain.WaterTransparency
	}
}

--------------------------------------------------
-- LOADING SCREEN
--------------------------------------------------
local LoadGui = Instance.new("ScreenGui", PlayerGui)
LoadGui.IgnoreGuiInset = true
LoadGui.ResetOnSpawn = false

local LoadFrame = Instance.new("Frame", LoadGui)
LoadFrame.Size = UDim2.new(1,0,1,0)
LoadFrame.BackgroundColor3 = Color3.fromRGB(12,12,12)

local LoadText = Instance.new("TextLabel", LoadFrame)
LoadText.Size = UDim2.new(1,0,0,40)
LoadText.Position = UDim2.new(0,0,0.45,0)
LoadText.Text = "Carregando FPS Optimizer v1.2 Beta..."
LoadText.Font = Enum.Font.GothamBold
LoadText.TextSize = 18
LoadText.TextColor3 = Color3.fromRGB(0,255,120)
LoadText.BackgroundTransparency = 1

local BarBack = Instance.new("Frame", LoadFrame)
BarBack.Size = UDim2.new(0.4,0,0,10)
BarBack.Position = UDim2.new(0.3,0,0.53,0)
BarBack.BackgroundColor3 = Color3.fromRGB(40,40,40)
Instance.new("UICorner", BarBack)

local Bar = Instance.new("Frame", BarBack)
Bar.Size = UDim2.new(0,0,1,0)
Bar.BackgroundColor3 = Color3.fromRGB(0,170,90)
Instance.new("UICorner", Bar)

TweenService:Create(Bar, TweenInfo.new(2), {
	Size = UDim2.new(1,0,1,0)
}):Play()

task.wait(2.2)
LoadGui:Destroy()

--------------------------------------------------
-- MAIN GUI
--------------------------------------------------
local ScreenGui = Instance.new("ScreenGui", PlayerGui)
ScreenGui.ResetOnSpawn = false

local Main = Instance.new("Frame", ScreenGui)
Main.Size = UDim2.new(0,360,0,560)
Main.Position = UDim2.new(0.5,-180,0.5,-280)
Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
Main.Active = true
Main.BorderSizePixel = 0
Instance.new("UICorner", Main)

--------------------------------------------------
-- DRAG (APENAS MAIN)
--------------------------------------------------
do
	local dragging, startPos, startInput
	Main.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			startPos = Main.Position
			startInput = i.Position
		end
	end)

	UIS.InputChanged:Connect(function(i)
		if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then
			local delta = i.Position - startInput
			Main.Position = UDim2.new(
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

--------------------------------------------------
-- MINI FPS BUTTON
--------------------------------------------------
local Mini = Instance.new("TextButton", ScreenGui)
Mini.Size = UDim2.new(0,50,0,50)
Mini.Position = UDim2.new(0,20,0.5,-25)
Mini.Text = "FPS"
Mini.Font = Enum.Font.GothamBold
Mini.TextSize = 16
Mini.TextColor3 = Color3.new(1,1,1)
Mini.BackgroundColor3 = Color3.fromRGB(0,170,90)
Mini.Visible = false
Mini.Active = true
Mini.ZIndex = 20
Instance.new("UICorner", Mini)

--------------------------------------------------
-- HEADER
--------------------------------------------------
local Header = Instance.new("Frame", Main)
Header.Size = UDim2.new(1,0,0,44)
Header.BackgroundColor3 = Color3.fromRGB(22,22,22)
Header.BorderSizePixel = 0
Header.ZIndex = 10
Instance.new("UICorner", Header)

local Title = Instance.new("TextLabel", Header)
Title.Size = UDim2.new(1,-100,1,0)
Title.Position = UDim2.new(0,12,0,0)
Title.Text = "🚀 FPS OPTIMIZER  |  v1.2 Beta"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.TextColor3 = Color3.fromRGB(0,255,120)
Title.BackgroundTransparency = 1
Title.ZIndex = 11

local Minimize = Instance.new("TextButton", Header)
Minimize.Size = UDim2.new(0,30,0,30)
Minimize.Position = UDim2.new(1,-70,0,7)
Minimize.Text = "-"
Minimize.Font = Enum.Font.GothamBold
Minimize.TextSize = 20
Minimize.TextColor3 = Color3.fromRGB(220,220,220)
Minimize.BackgroundTransparency = 1
Minimize.ZIndex = 11

local Close = Instance.new("TextButton", Header)
Close.Size = UDim2.new(0,30,0,30)
Close.Position = UDim2.new(1,-35,0,7)
Close.Text = "X"
Close.Font = Enum.Font.GothamBold
Close.TextSize = 16
Close.TextColor3 = Color3.fromRGB(255,80,80)
Close.BackgroundTransparency = 1
Close.ZIndex = 11

--------------------------------------------------
-- SCROLL
--------------------------------------------------
local Scroll = Instance.new("ScrollingFrame", Main)
Scroll.Size = UDim2.new(1,-20,1,-110)
Scroll.Position = UDim2.new(0,10,0,54)
Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
Scroll.CanvasSize = UDim2.new(0,0,0,0)
Scroll.BackgroundTransparency = 1
Scroll.ScrollBarImageTransparency = 0.4

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0,8)

--------------------------------------------------
-- TOGGLE CREATOR
--------------------------------------------------
local function CreateToggle(text, onFunc, offFunc, locked)
	local enabled = false
	local btn = Instance.new("TextButton", Scroll)
	btn.Size = UDim2.new(1,0,0,36)
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	btn.TextColor3 = Color3.fromRGB(240,240,240)
	btn.BackgroundColor3 = locked and Color3.fromRGB(60,60,60) or Color3.fromRGB(40,40,40)
	btn.BorderSizePixel = 0
	Instance.new("UICorner", btn)

	btn.Text = locked and text or text .. " [OFF]"

	btn.MouseButton1Click:Connect(function()
		if locked then return end
		enabled = not enabled
		if enabled then
			onFunc()
			btn.Text = text .. " [ON]"
			btn.BackgroundColor3 = Color3.fromRGB(0,160,90)
		else
			if offFunc then offFunc() end
			btn.Text = text .. " [OFF]"
			btn.BackgroundColor3 = Color3.fromRGB(40,40,40)
		end
	end)
end

--------------------------------------------------
-- FPS LIMIT 120
--------------------------------------------------
local fpsConn
local function FPS120On()
	fpsConn = RunService.RenderStepped:Connect(function(dt)
		local target = 1/120
		if dt < target then
			task.wait(target - dt)
		end
	end)
end

local function FPS120Off()
	if fpsConn then fpsConn:Disconnect() end
end

--------------------------------------------------
-- TOGGLES
--------------------------------------------------
CreateToggle("⚡ FPS Boost", function()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end, function()
	settings().Rendering.QualityLevel = Original.Quality
end)

CreateToggle("🎯 Limite de FPS: 120", FPS120On, FPS120Off)

CreateToggle("🧱 Texturas Leves", function()
	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then v.CastShadow = false end
	end
end, function()
	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then v.CastShadow = true end
	end
end)

CreateToggle("💡 Otimizar Iluminação", function()
	Lighting.GlobalShadows = false
	Lighting.EnvironmentSpecularScale = 0
end, function()
	Lighting.GlobalShadows = Original.Lighting.GlobalShadows
	Lighting.EnvironmentSpecularScale = Original.Lighting.Specular
end)

CreateToggle("🌊 Otimizar Água", function()
	Terrain.WaterWaveSize = 0
	Terrain.WaterWaveSpeed = 0
	Terrain.WaterTransparency = 1
end, function()
	Terrain.WaterWaveSize = Original.Water.WaveSize
	Terrain.WaterWaveSpeed = Original.Water.WaveSpeed
	Terrain.WaterTransparency = Original.Water.Transparency
end)

CreateToggle("🔒 Mais funções em breve...", function() end, nil, true)

--------------------------------------------------
-- CONFIRM CLOSE
--------------------------------------------------
Close.MouseButton1Click:Connect(function()
	local Confirm = Instance.new("TextLabel", Main)
	Confirm.Size = UDim2.new(0.9,0,0,80)
	Confirm.Position = UDim2.new(0.05,0,0.4,0)
	Confirm.BackgroundColor3 = Color3.fromRGB(30,30,30)
	Confirm.TextWrapped = true
	Confirm.Text = "Se fechar o menu, ele não poderá ser aberto novamente.\n\nClique fora para cancelar."
	Confirm.Font = Enum.Font.GothamBold
	Confirm.TextSize = 14
	Confirm.TextColor3 = Color3.new(1,1,1)
	Confirm.ZIndex = 50
	Instance.new("UICorner", Confirm)

	Confirm.InputBegan:Wait()
	ScreenGui:Destroy()
end)

Minimize.MouseButton1Click:Connect(function()
	Main.Visible = false
	Mini.Visible = true
end)

Mini.MouseButton1Click:Connect(function()
	Main.Visible = true
	Mini.Visible = false
end)

--------------------------------------------------
-- CREDIT
--------------------------------------------------
local Credit = Instance.new("TextLabel", Main)
Credit.Size = UDim2.new(1,0,0,20)
Credit.Position = UDim2.new(0,0,1,-22)
Credit.Text = "Criador: Frostzn"
Credit.Font = Enum.Font.Gotham
Credit.TextSize = 12
Credit.TextColor3 = Color3.fromRGB(150,150,150)
Credit.BackgroundTransparency = 1
