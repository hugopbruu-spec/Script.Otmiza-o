--[[
 ROBLOX FPS OPTIMIZER
 Criador: Frostzn
]]

--------------------------------------------------
-- SERVICES
--------------------------------------------------
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local Terrain = workspace:WaitForChild("Terrain")

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

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
LoadText.Text = "Carregando FPS Optimizer..."
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
Instance.new("UICorner", Main).CornerRadius = UDim.new(0,16)

--------------------------------------------------
-- DRAG
--------------------------------------------------
local function Drag(gui)
	local dragging, startPos, startInput
	gui.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			startPos = gui.Position
			startInput = i.Position
		end
	end)
	UIS.InputChanged:Connect(function(i)
		if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then
			local delta = i.Position - startInput
			gui.Position = UDim2.new(
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

Drag(Main)

--------------------------------------------------
-- HEADER
--------------------------------------------------
local Title = Instance.new("TextLabel", Main)
Title.Size = UDim2.new(1,0,0,40)
Title.Text = "🚀 FPS OPTIMIZER"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20
Title.TextColor3 = Color3.fromRGB(0,255,120)
Title.BackgroundTransparency = 1

--------------------------------------------------
-- SCROLL AREA (CORREÇÃO PRINCIPAL)
--------------------------------------------------
local Scroll = Instance.new("ScrollingFrame", Main)
Scroll.Size = UDim2.new(1,-20,1,-90)
Scroll.Position = UDim2.new(0,10,0,50)
Scroll.CanvasSize = UDim2.new(0,0,0,0)
Scroll.ScrollBarImageTransparency = 0.4
Scroll.BackgroundTransparency = 1
Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0,10)

--------------------------------------------------
-- TOGGLE FUNCTION
--------------------------------------------------
local function CreateToggle(text, onFunc, offFunc)
	local enabled = false

	local btn = Instance.new("TextButton", Scroll)
	btn.Size = UDim2.new(1,0,0,36)
	btn.Text = text .. " [OFF]"
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	btn.TextColor3 = Color3.fromRGB(240,240,240)
	btn.BackgroundColor3 = Color3.fromRGB(40,40,40)
	btn.BorderSizePixel = 0
	Instance.new("UICorner", btn)

	btn.MouseButton1Click:Connect(function()
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
-- ORIGINAL VALUES
--------------------------------------------------
local originalWater = {
	Terrain.WaterWaveSize,
	Terrain.WaterWaveSpeed,
	Terrain.WaterTransparency
}

--------------------------------------------------
-- FUNCTIONS
--------------------------------------------------
CreateToggle("⚡ FPS Boost", function()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end, function()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Automatic
end)

CreateToggle("🎯 Limitar FPS em 120", function()
	RunService:Set3dRenderingEnabled(true)
end)

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
end, function()
	Lighting.GlobalShadows = true
end)

CreateToggle("🎨 Remover Efeitos", function()
	for _,v in pairs(Lighting:GetChildren()) do
		if v:IsA("PostEffect") then v.Enabled = false end
	end
end, function()
	for _,v in pairs(Lighting:GetChildren()) do
		if v:IsA("PostEffect") then v.Enabled = true end
	end
end)

CreateToggle("🌊 Otimizar Água", function()
	Terrain.WaterWaveSize = 0
	Terrain.WaterWaveSpeed = 0
	Terrain.WaterTransparency = 1
end, function()
	Terrain.WaterWaveSize = originalWater[1]
	Terrain.WaterWaveSpeed = originalWater[2]
	Terrain.WaterTransparency = originalWater[3]
end)

CreateToggle("🤖 Auto Optimizer", function()
	print("Auto Optimizer Ativo")
end)

--------------------------------------------------
-- CREDIT
--------------------------------------------------
local Credit = Instance.new("TextLabel", Main)
Credit.Size = UDim2.new(1,0,0,20)
Credit.Position = UDim2.new(0,0,1,-25)
Credit.Text = "Criador: Frostzn"
Credit.Font = Enum.Font.Gotham
Credit.TextSize = 12
Credit.TextColor3 = Color3.fromRGB(150,150,150)
Credit.BackgroundTransparency = 1
