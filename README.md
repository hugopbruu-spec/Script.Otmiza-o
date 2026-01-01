--[[
  ROBLOX PERFORMANCE OPTIMIZER
  Criador: Frostzn
]]

-------------------------------
-- SERVICES
-------------------------------
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local Terrain = workspace:WaitForChild("Terrain")

-------------------------------
-- PLAYER READY
-------------------------------
local player = Players.LocalPlayer or Players.PlayerAdded:Wait()
local PlayerGui = player:WaitForChild("PlayerGui")

-------------------------------
-- GUI ROOT
-------------------------------
local ScreenGui = Instance.new("ScreenGui", PlayerGui)
ScreenGui.Name = "FPSOptimizer"
ScreenGui.IgnoreGuiInset = true
ScreenGui.ResetOnSpawn = false

-------------------------------
-- MAIN FRAME
-------------------------------
local Main = Instance.new("Frame", ScreenGui)
Main.Size = UDim2.new(0, 360, 0, 470)
Main.Position = UDim2.new(0.5, -180, 0.5, -235)
Main.BackgroundColor3 = Color3.fromRGB(20,20,20)
Main.BorderSizePixel = 0
Main.Active = true
Instance.new("UICorner", Main).CornerRadius = UDim.new(0,16)

-------------------------------
-- MINI BUTTON (FPS)
-------------------------------
local Mini = Instance.new("TextButton", ScreenGui)
Mini.Size = UDim2.new(0, 50, 0, 50)
Mini.Position = UDim2.new(0, 20, 0.5, -25)
Mini.Text = "FPS"
Mini.Visible = false
Mini.Font = Enum.Font.GothamBold
Mini.TextSize = 16
Mini.TextColor3 = Color3.new(1,1,1)
Mini.BackgroundColor3 = Color3.fromRGB(0,170,90)
Mini.BorderSizePixel = 0
Instance.new("UICorner", Mini).CornerRadius = UDim.new(0,12)

-------------------------------
-- DRAG SYSTEM
-------------------------------
local dragging, dragStart, startPos
Main.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragStart = input.Position
		startPos = Main.Position
		dragging = true
	end
end)

Main.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = false
	end
end)

UIS.InputChanged:Connect(function(input)
	if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
		local delta = input.Position - dragStart
		Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

-------------------------------
-- ANIMAÇÃO ABERTURA
-------------------------------
Main.BackgroundTransparency = 1
TweenService:Create(Main, TweenInfo.new(0.6, Enum.EasingStyle.Back), {BackgroundTransparency = 0}):Play()

-------------------------------
-- HEADER
-------------------------------
local Title = Instance.new("TextLabel", Main)
Title.Size = UDim2.new(1,0,0,40)
Title.Text = "🚀 FPS OPTIMIZER"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20
Title.TextColor3 = Color3.fromRGB(0,255,120)
Title.BackgroundTransparency = 1

-------------------------------
-- CLOSE & MINIMIZE
-------------------------------
local Close = Instance.new("TextButton", Main)
Close.Size = UDim2.new(0,30,0,30)
Close.Position = UDim2.new(1,-35,0,5)
Close.Text = "X"
Close.Font = Enum.Font.GothamBold
Close.TextColor3 = Color3.fromRGB(255,90,90)
Close.BackgroundTransparency = 1

local Minimize = Instance.new("TextButton", Main)
Minimize.Size = UDim2.new(0,30,0,30)
Minimize.Position = UDim2.new(1,-70,0,5)
Minimize.Text = "-"
Minimize.Font = Enum.Font.GothamBold
Minimize.TextColor3 = Color3.fromRGB(200,200,200)
Minimize.BackgroundTransparency = 1

-------------------------------
-- MINIMIZE / RESTORE
-------------------------------
Minimize.MouseButton1Click:Connect(function()
	Main.Visible = false
	Mini.Visible = true
end)

Mini.MouseButton1Click:Connect(function()
	Main.Visible = true
	Mini.Visible = false
end)

Close.MouseButton1Click:Connect(function()
	ScreenGui:Destroy()
end)

-------------------------------
-- TOGGLE BUTTON
-------------------------------
local function CreateToggle(text, y, onFunc, offFunc)
	local enabled = false
	local btn = Instance.new("TextButton", Main)
	btn.Size = UDim2.new(0.9,0,0,34)
	btn.Position = UDim2.new(0.05,0,0,y)
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	btn.BorderSizePixel = 0
	Instance.new("UICorner", btn).CornerRadius = UDim.new(0,10)

	local function Update()
		btn.Text = text .. (enabled and " [ON]" or " [OFF]")
		btn.BackgroundColor3 = enabled and Color3.fromRGB(0,170,90) or Color3.fromRGB(40,40,40)
	end

	btn.MouseButton1Click:Connect(function()
		enabled = not enabled
		if enabled then onFunc() elseif offFunc then offFunc() end
		Update()
	end)

	Update()
end

-------------------------------
-- OPTIMIZATIONS
-------------------------------
local function FPSBoost()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end

local function OptimizeTextures()
	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			v.Material = Enum.Material.Plastic
			v.CastShadow = false
		end
	end
end

local function DisableParticles()
	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("ParticleEmitter") or v:IsA("Trail") then
			v.Enabled = false
		end
	end
end

local function OptimizeLighting()
	Lighting.GlobalShadows = false
	Lighting.Brightness = 1
	Lighting.FogEnd = 1e9
	Lighting.EnvironmentSpecularScale = 0
end

local function RemoveEffects()
	for _,v in pairs(Lighting:GetChildren()) do
		if v:IsA("PostEffect") then v.Enabled = false end
	end
end

-- ✅ WATER FIX REAL
local function OptimizeWater()
	Terrain.WaterWaveSize = 0
	Terrain.WaterWaveSpeed = 0
	Terrain.WaterReflectance = 0
	Terrain.WaterTransparency = 1
end

local function ReduceRender()
	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then v.CastShadow = false end
	end
end

-------------------------------
-- AUTO OPTIMIZER
-------------------------------
local autoConn
local function AutoOn()
	autoConn = RunService.RenderStepped:Connect(function()
		if workspace:GetRealPhysicsFPS() < 50 then
			FPSBoost()
			OptimizeTextures()
			DisableParticles()
		end
	end)
end

local function AutoOff()
	if autoConn then autoConn:Disconnect() end
end

-------------------------------
-- TOGGLES
-------------------------------
CreateToggle("⚡ FPS BOOST", 60, FPSBoost)
CreateToggle("🧱 TEXTURAS LEVES", 100, OptimizeTextures)
CreateToggle("✨ REMOVER PARTÍCULAS", 140, DisableParticles)
CreateToggle("💡 OTIMIZAR LUZ", 180, OptimizeLighting)
CreateToggle("🎨 REMOVER EFEITOS", 220, RemoveEffects)
CreateToggle("🌊 OTIMIZAR ÁGUA", 260, OptimizeWater)
CreateToggle("📉 REDUZIR RENDER", 300, ReduceRender)
CreateToggle("🤖 AUTO OPTIMIZER", 340, AutoOn, AutoOff)

-------------------------------
-- CREDIT
-------------------------------
local Credit = Instance.new("TextLabel", Main)
Credit.Size = UDim2.new(1,0,0,20)
Credit.Position = UDim2.new(0,0,1,-25)
Credit.Text = "Criador: Frostzn"
Credit.Font = Enum.Font.Gotham
Credit.TextSize = 12
Credit.TextColor3 = Color3.fromRGB(150,150,150)
Credit.BackgroundTransparency = 1

print("FPS Optimizer carregado | Criador: Frostzn")
