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
-- PLAYER
-------------------------------
local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

-------------------------------
-- GUI ROOT
-------------------------------
local ScreenGui = Instance.new("ScreenGui", PlayerGui)
ScreenGui.IgnoreGuiInset = true
ScreenGui.ResetOnSpawn = false

-------------------------------
-- MAIN FRAME
-------------------------------
local Main = Instance.new("Frame", ScreenGui)
Main.Size = UDim2.new(0, 360, 0, 480)
Main.Position = UDim2.new(0.5, -180, 0.5, -240)
Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
Main.BorderSizePixel = 0
Main.Active = true
Instance.new("UICorner", Main).CornerRadius = UDim.new(0,16)

-------------------------------
-- MINI FPS BUTTON
-------------------------------
local Mini = Instance.new("TextButton", ScreenGui)
Mini.Size = UDim2.new(0, 50, 0, 50)
Mini.Position = UDim2.new(0, 20, 0.5, -25)
Mini.Text = "FPS"
Mini.Font = Enum.Font.GothamBold
Mini.TextSize = 16
Mini.TextColor3 = Color3.new(1,1,1)
Mini.BackgroundColor3 = Color3.fromRGB(0,170,90)
Mini.BorderSizePixel = 0
Mini.Visible = false
Mini.Active = true
Instance.new("UICorner", Mini).CornerRadius = UDim.new(0,12)

-------------------------------
-- DRAG (GENERIC)
-------------------------------
local function MakeDraggable(frame)
	local dragging, dragStart, startPos

	frame.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			dragStart = input.Position
			startPos = frame.Position
		end
	end)

	frame.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = false
		end
	end)

	UIS.InputChanged:Connect(function(input)
		if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
			local delta = input.Position - dragStart
			frame.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)
end

MakeDraggable(Main)
MakeDraggable(Mini)

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
-- CLOSE / MINIMIZE
-------------------------------
local Close = Instance.new("TextButton", Main)
Close.Size = UDim2.new(0,30,0,30)
Close.Position = UDim2.new(1,-35,0,5)
Close.Text = "X"
Close.Font = Enum.Font.GothamBold
Close.TextSize = 16
Close.TextColor3 = Color3.fromRGB(255,90,90)
Close.BackgroundTransparency = 1

local Minimize = Instance.new("TextButton", Main)
Minimize.Size = UDim2.new(0,30,0,30)
Minimize.Position = UDim2.new(1,-70,0,5)
Minimize.Text = "-"
Minimize.Font = Enum.Font.GothamBold
Minimize.TextSize = 20
Minimize.TextColor3 = Color3.fromRGB(220,220,220)
Minimize.BackgroundTransparency = 1

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
-- TOGGLE SYSTEM (REAL ON/OFF)
-------------------------------
local function CreateToggle(text, y, onFunc, offFunc)
	local enabled = false

	local btn = Instance.new("TextButton", Main)
	btn.Size = UDim2.new(0.9,0,0,34)
	btn.Position = UDim2.new(0.05,0,0,y)
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	btn.TextColor3 = Color3.fromRGB(240,240,240)
	btn.BackgroundColor3 = Color3.fromRGB(40,40,40)
	btn.BorderSizePixel = 0
	Instance.new("UICorner", btn).CornerRadius = UDim.new(0,10)

	local function Refresh()
		btn.Text = text .. (enabled and "  [ON]" or "  [OFF]")
		btn.BackgroundColor3 = enabled and Color3.fromRGB(0,160,90) or Color3.fromRGB(40,40,40)
	end

	btn.MouseButton1Click:Connect(function()
		enabled = not enabled
		if enabled then onFunc() else if offFunc then offFunc() end end
		Refresh()
	end)

	Refresh()
end

-------------------------------
-- SAVE ORIGINAL VALUES
-------------------------------
local original = {
	Lighting = {
		GlobalShadows = Lighting.GlobalShadows,
		Brightness = Lighting.Brightness,
		FogEnd = Lighting.FogEnd,
		Specular = Lighting.EnvironmentSpecularScale
	},
	Water = {
		WaveSize = Terrain.WaterWaveSize,
		WaveSpeed = Terrain.WaterWaveSpeed,
		Reflectance = Terrain.WaterReflectance,
		Transparency = Terrain.WaterTransparency
	}
}

-------------------------------
-- OPTIMIZATIONS
-------------------------------
local function FPSOn()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end

local function FPSOff()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Automatic
end

-- TEXTURE (LEVE)
local function TextureOn()
	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			v.CastShadow = false
		end
	end
end

local function TextureOff()
	for _,v in pairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			v.CastShadow = true
		end
	end
end

local function LightOn()
	Lighting.GlobalShadows = false
	Lighting.Brightness = 1
	Lighting.FogEnd = 1e9
	Lighting.EnvironmentSpecularScale = 0
end

local function LightOff()
	Lighting.GlobalShadows = original.Lighting.GlobalShadows
	Lighting.Brightness = original.Lighting.Brightness
	Lighting.FogEnd = original.Lighting.FogEnd
	Lighting.EnvironmentSpecularScale = original.Lighting.Specular
end

local function WaterOn()
	Terrain.WaterWaveSize = 0
	Terrain.WaterWaveSpeed = 0
	Terrain.WaterReflectance = 0
	Terrain.WaterTransparency = 1
end

local function WaterOff()
	Terrain.WaterWaveSize = original.Water.WaveSize
	Terrain.WaterWaveSpeed = original.Water.WaveSpeed
	Terrain.WaterReflectance = original.Water.Reflectance
	Terrain.WaterTransparency = original.Water.Transparency
end

-------------------------------
-- AUTO OPTIMIZER
-------------------------------
local autoConn
local function AutoOn()
	autoConn = RunService.RenderStepped:Connect(function()
		if workspace:GetRealPhysicsFPS() < 50 then
			FPSOn()
			TextureOn()
		end
	end)
end

local function AutoOff()
	if autoConn then autoConn:Disconnect() end
end

-------------------------------
-- TOGGLES
-------------------------------
CreateToggle("⚡ FPS BOOST", 60, FPSOn, FPSOff)
CreateToggle("🧱 TEXTURAS LEVES", 100, TextureOn, TextureOff)
CreateToggle("💡 OTIMIZAR LUZ", 140, LightOn, LightOff)
CreateToggle("🌊 OTIMIZAR ÁGUA", 180, WaterOn, WaterOff)
CreateToggle("🤖 AUTO OPTIMIZER", 220, AutoOn, AutoOff)

-------------------------------
-- CREDIT
-------------------------------
local Credit = Instance.new("TextLabel", Main)
Credit.Size = UDim2.new(1,0,0,18)
Credit.Position = UDim2.new(0,0,1,-22)
Credit.Text = "Criador: Frostzn"
Credit.Font = Enum.Font.Gotham
Credit.TextSize = 12
Credit.TextColor3 = Color3.fromRGB(150,150,150)
Credit.BackgroundTransparency = 1

print("FPS Optimizer carregado | Criador: Frostzn")
