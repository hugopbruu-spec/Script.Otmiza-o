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

-------------------------------
-- PLAYER READY
-------------------------------
local player = Players.LocalPlayer or Players.PlayerAdded:Wait()
local PlayerGui = player:WaitForChild("PlayerGui")

-------------------------------
-- GUI
-------------------------------
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "FPSOptimizer"
ScreenGui.IgnoreGuiInset = true
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui

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
-- DRAG SYSTEM (PC + MOBILE)
-------------------------------
local dragging, dragInput, dragStart, startPos

Main.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1
	or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = Main.Position
	end
end)

Main.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1
	or input.UserInputType == Enum.UserInputType.Touch then
		dragging = false
	end
end)

UIS.InputChanged:Connect(function(input)
	if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement
	or input.UserInputType == Enum.UserInputType.Touch) then
		local delta = input.Position - dragStart
		Main.Position = UDim2.new(
			startPos.X.Scale, startPos.X.Offset + delta.X,
			startPos.Y.Scale, startPos.Y.Offset + delta.Y
		)
	end
end)

-------------------------------
-- ANIMAÇÃO DE ENTRADA
-------------------------------
Main.BackgroundTransparency = 1
TweenService:Create(
	Main,
	TweenInfo.new(0.8, Enum.EasingStyle.Back),
	{BackgroundTransparency = 0}
):Play()

-------------------------------
-- TÍTULO
-------------------------------
local Title = Instance.new("TextLabel", Main)
Title.Size = UDim2.new(1,0,0,50)
Title.Text = "🚀 FPS OPTIMIZER"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 22
Title.TextColor3 = Color3.fromRGB(0,255,120)
Title.BackgroundTransparency = 1

-------------------------------
-- CRÉDITO
-------------------------------
local Credit = Instance.new("TextLabel", Main)
Credit.Size = UDim2.new(1,0,0,20)
Credit.Position = UDim2.new(0,0,1,-25)
Credit.Text = "Criador: Frostzn"
Credit.Font = Enum.Font.Gotham
Credit.TextSize = 12
Credit.TextColor3 = Color3.fromRGB(150,150,150)
Credit.BackgroundTransparency = 1

-------------------------------
-- BOTÃO TOGGLE
-------------------------------
local function CreateToggle(text, y, onFunc, offFunc)
	local enabled = false

	local btn = Instance.new("TextButton", Main)
	btn.Size = UDim2.new(0.9,0,0,36)
	btn.Position = UDim2.new(0.05,0,0,y)
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	btn.BackgroundColor3 = Color3.fromRGB(40,40,40)
	btn.TextColor3 = Color3.new(1,1,1)
	btn.BorderSizePixel = 0
	Instance.new("UICorner", btn).CornerRadius = UDim.new(0,10)

	local function Update()
		btn.Text = text .. (enabled and " [ON]" or " [OFF]")
		btn.BackgroundColor3 = enabled and Color3.fromRGB(0,170,90) or Color3.fromRGB(40,40,40)
	end

	btn.MouseButton1Click:Connect(function()
		enabled = not enabled
		if enabled then
			onFunc()
		elseif offFunc then
			offFunc()
		end
		Update()
	end)

	Update()
end

-------------------------------
-- FUNÇÕES DE OTIMIZAÇÃO
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
		if v:IsA("PostEffect") then
			v.Enabled = false
		end
	end
end

local function OptimizeWater()
	workspace.WaterWaveSize = 0
	workspace.WaterWaveSpeed = 0
	workspace.WaterTransparency = 1
end

local function ReduceRender()
	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			v.CastShadow = false
		end
	end
end

-------------------------------
-- AUTO OPTIMIZER
-------------------------------
local autoConnection
local function AutoOptimizerOn()
	autoConnection = RunService.RenderStepped:Connect(function()
		if workspace:GetRealPhysicsFPS() < 50 then
			FPSBoost()
			OptimizeTextures()
			DisableParticles()
		end
	end)
end

local function AutoOptimizerOff()
	if autoConnection then
		autoConnection:Disconnect()
	end
end

-------------------------------
-- TOGGLES
-------------------------------
CreateToggle("⚡ FPS BOOST", 60, FPSBoost)
CreateToggle("🧱 TEXTURAS LEVES", 105, OptimizeTextures)
CreateToggle("✨ REMOVER PARTÍCULAS", 150, DisableParticles)
CreateToggle("🌑 DESATIVAR SOMBRAS", 195, OptimizeLighting)
CreateToggle("🎨 REMOVER EFEITOS", 240, RemoveEffects)
CreateToggle("🌊 OTIMIZAR ÁGUA", 285, OptimizeWater)
CreateToggle("📉 REDUZIR RENDER", 330, ReduceRender)
CreateToggle("🤖 AUTO OPTIMIZER", 375, AutoOptimizerOn, AutoOptimizerOff)

print("FPS Optimizer carregado | Criador: Frostzn")
