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

---------------- ORIGINAL VALUES ----------------
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

---------------- LOADING ----------------
local LoadGui = Instance.new("ScreenGui", guiParent)
LoadGui.IgnoreGuiInset = true

local LoadFrame = Instance.new("Frame", LoadGui)
LoadFrame.Size = UDim2.fromScale(1,1)
LoadFrame.BackgroundColor3 = Color3.fromRGB(10,10,10)

local LoadText = Instance.new("TextLabel", LoadFrame)
LoadText.Size = UDim2.new(1,0,0,40)
LoadText.Position = UDim2.new(0,0,0.45,0)
LoadText.Text = "Carregando FPS Optimizer v1.2 Beta..."
LoadText.Font = Enum.Font.GothamBold
LoadText.TextSize = 18
LoadText.TextColor3 = Color3.fromRGB(0,255,120)
LoadText.BackgroundTransparency = 1

local BarBG = Instance.new("Frame", LoadFrame)
BarBG.Size = UDim2.new(0.4,0,0,10)
BarBG.Position = UDim2.new(0.3,0,0.53,0)
BarBG.BackgroundColor3 = Color3.fromRGB(40,40,40)
Instance.new("UICorner", BarBG)

local Bar = Instance.new("Frame", BarBG)
Bar.Size = UDim2.new(0,0,1,0)
Bar.BackgroundColor3 = Color3.fromRGB(0,170,90)
Instance.new("UICorner", Bar)

TweenService:Create(Bar, TweenInfo.new(2), {
	Size = UDim2.new(1,0,1,0)
}):Play()

task.wait(2.2)
LoadGui:Destroy()

---------------- MAIN GUI ----------------
local ScreenGui = Instance.new("ScreenGui", guiParent)
ScreenGui.ResetOnSpawn = false

local Main = Instance.new("Frame", ScreenGui)
Main.Size = UDim2.fromOffset(380,600)
Main.Position = UDim2.new(0.5,-190,0.5,-300)
Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
Main.BorderSizePixel = 0
Main.Active = true
Main.ZIndex = 1
Instance.new("UICorner", Main)

---------------- DRAG FUNCTION ----------------
local function makeDraggable(frame)
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

makeDraggable(Main)

---------------- HEADER ----------------
local Header = Instance.new("Frame", Main)
Header.Size = UDim2.new(1,0,0,46)
Header.BackgroundColor3 = Color3.fromRGB(22,22,22)
Header.ZIndex = 2
Instance.new("UICorner", Header)

local Title = Instance.new("TextLabel", Header)
Title.Size = UDim2.new(1,-90,1,0)
Title.Position = UDim2.new(0,10,0,0)
Title.Text = "🚀 FPS OPTIMIZER | v1.2 Beta"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.TextColor3 = Color3.fromRGB(0,255,120)
Title.BackgroundTransparency = 1
Title.ZIndex = 3

local Minimize = Instance.new("TextButton", Header)
Minimize.Size = UDim2.fromOffset(32,32)
Minimize.Position = UDim2.new(1,-70,0,7)
Minimize.Text = "-"
Minimize.Font = Enum.Font.GothamBold
Minimize.TextSize = 22
Minimize.TextColor3 = Color3.fromRGB(230,230,230)
Minimize.BackgroundTransparency = 1
Minimize.ZIndex = 3

local Close = Instance.new("TextButton", Header)
Close.Size = UDim2.fromOffset(32,32)
Close.Position = UDim2.new(1,-36,0,7)
Close.Text = "X"
Close.Font = Enum.Font.GothamBold
Close.TextSize = 16
Close.TextColor3 = Color3.fromRGB(255,80,80)
Close.BackgroundTransparency = 1
Close.ZIndex = 3

---------------- SCROLL ----------------
local Scroll = Instance.new("ScrollingFrame", Main)
Scroll.Position = UDim2.new(0,10,0,56)
Scroll.Size = UDim2.new(1,-20,1,-120)
Scroll.CanvasSize = UDim2.new(0,0,0,0)
Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
Scroll.ScrollBarImageTransparency = 0.4
Scroll.BackgroundTransparency = 1

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0,8)

---------------- TOGGLE CREATOR ----------------
local function createToggle(text, onFunc, offFunc, locked)
	local state = false
	local btn = Instance.new("TextButton", Scroll)
	btn.Size = UDim2.new(1,0,0,38)
	btn.Text = locked and text or text.." [OFF]"
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	btn.TextColor3 = Color3.fromRGB(245,245,245)
	btn.BackgroundColor3 = locked and Color3.fromRGB(70,70,70) or Color3.fromRGB(40,40,40)
	btn.BorderSizePixel = 0
	Instance.new("UICorner", btn)

	btn.MouseButton1Click:Connect(function()
		if locked then return end
		state = not state
		if state then
			onFunc()
			btn.Text = text.." [ON]"
			btn.BackgroundColor3 = Color3.fromRGB(0,160,90)
		else
			if offFunc then offFunc() end
			btn.Text = text.." [OFF]"
			btn.BackgroundColor3 = Color3.fromRGB(40,40,40)
		end
	end)
end

---------------- FPS LIMIT ----------------
local fpsConn
local function fps120On()
	fpsConn = RunService.RenderStepped:Connect(function(dt)
		local target = 1/120
		if dt < target then
			task.wait(target - dt)
		end
	end)
end
local function fps120Off()
	if fpsConn then fpsConn:Disconnect() end
end

---------------- FPS COUNTER ----------------
local FPSLabel = Instance.new("TextLabel", ScreenGui)
FPSLabel.Size = UDim2.fromOffset(120,30)
FPSLabel.Position = UDim2.new(1,-130,0,10)
FPSLabel.Text = "FPS: 0"
FPSLabel.Font = Enum.Font.GothamBold
FPSLabel.TextSize = 16
FPSLabel.TextColor3 = Color3.fromRGB(0,255,120)
FPSLabel.BackgroundTransparency = 1
FPSLabel.Visible = false

local fpsEnabled = false
local frames, last = 0, tick()
RunService.RenderStepped:Connect(function()
	if fpsEnabled then
		frames += 1
		if tick() - last >= 1 then
			FPSLabel.Text = "FPS: "..frames
			frames = 0
			last = tick()
		end
	end
end)

---------------- TOGGLES ----------------
createToggle("⚡ FPS Boost", function()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end, function()
	settings().Rendering.QualityLevel = Original.Quality
end)

createToggle("🎯 Limite de FPS: 120", fps120On, fps120Off)

createToggle("📊 Mostrar FPS em tempo real", function()
	fpsEnabled = true
	FPSLabel.Visible = true
end, function()
	fpsEnabled = false
	FPSLabel.Visible = false
end)

createToggle("🧱 Texturas Leves", function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then v.CastShadow = false end
	end
end, function()
	for _,v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then v.CastShadow = true end
	end
end)

createToggle("💡 Otimizar Iluminação", function()
	Lighting.GlobalShadows = false
	Lighting.EnvironmentSpecularScale = 0
end, function()
	Lighting.GlobalShadows = Original.GlobalShadows
	Lighting.EnvironmentSpecularScale = Original.Specular
end)

createToggle("🌊 Otimizar Água", function()
	Terrain.WaterWaveSize = 0
	Terrain.WaterWaveSpeed = 0
	Terrain.WaterTransparency = 1
end, function()
	Terrain.WaterWaveSize = Original.Water.WaveSize
	Terrain.WaterWaveSpeed = Original.Water.WaveSpeed
	Terrain.WaterTransparency = Original.Water.Transparency
end)

createToggle("🔒 Mais funções em breve...", function() end, nil, true)

---------------- MINI BUTTON ----------------
local Mini = Instance.new("TextButton", ScreenGui)
Mini.Size = UDim2.fromOffset(50,50)
Mini.Position = UDim2.new(0,20,0.5,-25)
Mini.Text = "FPS"
Mini.Font = Enum.Font.GothamBold
Mini.TextSize = 16
Mini.TextColor3 = Color3.new(1,1,1)
Mini.BackgroundColor3 = Color3.fromRGB(0,170,90)
Mini.Visible = false
Mini.ZIndex = 10
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

---------------- CLOSE CONFIRM ----------------
Close.MouseButton1Click:Connect(function()
	local box = Instance.new("Frame", Main)
	box.Size = UDim2.fromOffset(320,130)
	box.Position = UDim2.new(0.5,-160,0.5,-65)
	box.BackgroundColor3 = Color3.fromRGB(30,30,30)
	box.ZIndex = 20
	Instance.new("UICorner", box)

	local txt = Instance.new("TextLabel", box)
	txt.Size = UDim2.new(1,-10,0,60)
	txt.Position = UDim2.new(0,5,0,5)
	txt.TextWrapped = true
	txt.Text = "Se você fechar o menu, ele não poderá ser aberto novamente."
	txt.Font = Enum.Font.GothamBold
	txt.TextSize = 14
	txt.TextColor3 = Color3.new(1,1,1)
	txt.BackgroundTransparency = 1
	txt.ZIndex = 21

	local cancel = Instance.new("TextButton", box)
	cancel.Size = UDim2.new(0.45,0,0,32)
	cancel.Position = UDim2.new(0.05,0,1,-40)
	cancel.Text = "Cancelar"
	cancel.Font = Enum.Font.GothamBold
	cancel.TextSize = 14
	cancel.BackgroundColor3 = Color3.fromRGB(80,80,80)
	cancel.TextColor3 = Color3.new(1,1,1)
	cancel.ZIndex = 21
	Instance.new("UICorner", cancel)

	local confirm = Instance.new("TextButton", box)
	confirm.Size = UDim2.new(0.45,0,0,32)
	confirm.Position = UDim2.new(0.5,0,1,-40)
	confirm.Text = "Confirmar"
	confirm.Font = Enum.Font.GothamBold
	confirm.TextSize = 14
	confirm.BackgroundColor3 = Color3.fromRGB(170,60,60)
	confirm.TextColor3 = Color3.new(1,1,1)
	confirm.ZIndex = 21
	Instance.new("UICorner", confirm)

	cancel.MouseButton1Click:Connect(function()
		box:Destroy()
	end)

	confirm.MouseButton1Click:Connect(function()
		ScreenGui:Destroy()
	end)
end)

---------------- CREDIT ----------------
local Credit = Instance.new("TextLabel", Main)
Credit.Size = UDim2.new(1,0,0,20)
Credit.Position = UDim2.new(0,0,1,-22)
Credit.Text = "Criador: Frostzn"
Credit.Font = Enum.Font.Gotham
Credit.TextSize = 12
Credit.TextColor3 = Color3.fromRGB(150,150,150)
Credit.BackgroundTransparency = 1
