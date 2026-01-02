--[[=====================================================
 FPS OPTIMIZER ULTIMATE
 Criador: Frostzn
 Versão: FINAL 1.3
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
			local start = input.Position
			local startPos = frame.Position
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
-- ORIGINAL SETTINGS
--------------------------------------------------
local Original = {
	Quality = settings().Rendering.QualityLevel,
	GlobalShadows = Lighting.GlobalShadows,
	Specular = Lighting.EnvironmentSpecularScale,
	Blur = Lighting:FindFirstChildOfClass("BlurEffect"),
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

local function isValidKey(k)
	return Keys[k] and Keys[k] > 0
end

--------------------------------------------------
-- LOADING
--------------------------------------------------
do
	local LoadGui = Instance.new("ScreenGui", guiParent)
	local bg = Instance.new("Frame", LoadGui)
	bg.Size = UDim2.fromScale(1,1)
	bg.BackgroundColor3 = Color3.fromRGB(10,10,10)

	local txt = Instance.new("TextLabel", bg)
	txt.Size = UDim2.new(1,0,0,40)
	txt.Position = UDim2.new(0,0,0.45,0)
	txt.Font = Enum.Font.GothamBold
	txt.TextSize = 20
	txt.TextColor3 = Color3.fromRGB(0,255,120)
	txt.BackgroundTransparency = 1

	local barBG = Instance.new("Frame", bg)
	barBG.Size = UDim2.new(0.4,0,0,12)
	barBG.Position = UDim2.new(0.3,0,0.52,0)
	barBG.BackgroundColor3 = Color3.fromRGB(40,40,40)
	Instance.new("UICorner", barBG)

	local bar = Instance.new("Frame", barBG)
	bar.BackgroundColor3 = Color3.fromRGB(0,170,90)
	bar.Size = UDim2.new(0,0,1,0)
	Instance.new("UICorner", bar)

	for i=0,100 do
		txt.Text = "Carregando "..i.."%"
		bar.Size = UDim2.new(i/100,0,1,0)
		task.wait(0.015)
	end
	LoadGui:Destroy()
end

--------------------------------------------------
-- WINDOW BUTTONS
--------------------------------------------------
local function addWindowButtons(frame, closeFunc)
	local min = Instance.new("TextButton", frame)
	min.Size = UDim2.fromOffset(28,28)
	min.Position = UDim2.new(1,-64,0,8)
	min.Text = "–"
	min.Font = Enum.Font.GothamBold
	min.TextSize = 20
	min.BackgroundColor3 = Color3.fromRGB(60,60,60)
	Instance.new("UICorner", min)

	local close = Instance.new("TextButton", frame)
	close.Size = UDim2.fromOffset(28,28)
	close.Position = UDim2.new(1,-32,0,8)
	close.Text = "✕"
	close.Font = Enum.Font.GothamBold
	close.TextSize = 16
	close.BackgroundColor3 = Color3.fromRGB(170,60,60)
	Instance.new("UICorner", close)

	local minimized = false
	min.MouseButton1Click:Connect(function()
		minimized = not minimized
		frame.Size = minimized and UDim2.fromOffset(frame.Size.X.Offset,46) or frame:GetAttribute("FullSize")
	end)

	close.MouseButton1Click:Connect(closeFunc)
end

--------------------------------------------------
-- FPS OPTIMIZER
--------------------------------------------------
local function openFPSOptimizer()
	local gui = Instance.new("ScreenGui", guiParent)

	local main = Instance.new("Frame", gui)
	main.Size = UDim2.fromOffset(420,620)
	main:SetAttribute("FullSize", main.Size)
	main.Position = UDim2.new(0.5,-210,0.5,-310)
	main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", main)
	makeDraggable(main)

	addWindowButtons(main, function() gui:Destroy() end)

	local header = Instance.new("TextLabel", main)
	header.Size = UDim2.new(1,-80,0,40)
	header.Position = UDim2.new(0,12,0,0)
	header.Text = "🚀 FPS OPTIMIZER"
	header.Font = Enum.Font.GothamBold
	header.TextSize = 18
	header.TextColor3 = Color3.fromRGB(0,255,120)
	header.BackgroundTransparency = 1

	local scroll = Instance.new("ScrollingFrame", main)
	scroll.Position = UDim2.new(0,12,0,46)
	scroll.Size = UDim2.new(1,-24,1,-58)
	scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
	scroll.CanvasSize = UDim2.new(0,0,0,0)
	scroll.ScrollBarImageTransparency = 0.4
	scroll.BackgroundTransparency = 1

	local layout = Instance.new("UIListLayout", scroll)
	layout.Padding = UDim.new(0,8)

	local function toggle(text, on, off)
		local state = false
		local b = Instance.new("TextButton", scroll)
		b.Size = UDim2.new(1,0,0,38)
		b.Text = text.." [OFF]"
		b.Font = Enum.Font.Gotham
		b.TextSize = 14
		b.BackgroundColor3 = Color3.fromRGB(40,40,40)
		b.TextColor3 = Color3.new(1,1,1)
		Instance.new("UICorner", b)

		b.MouseButton1Click:Connect(function()
			state = not state
			if state then
				on()
				b.Text = text.." [ON]"
				b.BackgroundColor3 = Color3.fromRGB(0,160,90)
			else
				if off then off() end
				b.Text = text.." [OFF]"
				b.BackgroundColor3 = Color3.fromRGB(40,40,40)
			end
		end)
	end

	-- OPTIONS
	toggle("FPS Boost", function()
		settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
	end, function()
		settings().Rendering.QualityLevel = Original.Quality
	end)

	toggle("Remover Sombras", function()
		for _,v in ipairs(workspace:GetDescendants()) do
			if v:IsA("BasePart") then v.CastShadow = false end
		end
	end)

	toggle("Otimizar Iluminação", function()
		Lighting.GlobalShadows = false
		Lighting.EnvironmentSpecularScale = 0
	end)

	toggle("Remover Partículas", function()
		for _,v in ipairs(workspace:GetDescendants()) do
			if v:IsA("ParticleEmitter") then v.Enabled = false end
		end
	end)

	toggle("Remover Blur", function()
		if Original.Blur then Original.Blur.Enabled = false end
	end)

	toggle("Otimizar Água", function()
		Terrain.WaterWaveSize = 0
		Terrain.WaterWaveSpeed = 0
		Terrain.WaterTransparency = 1
	end)

	toggle("Restaurar Tudo", function()
		settings().Rendering.QualityLevel = Original.Quality
		Lighting.GlobalShadows = Original.GlobalShadows
		Lighting.EnvironmentSpecularScale = Original.Specular
		if Original.Blur then Original.Blur.Enabled = true end
	end)
end

--------------------------------------------------
-- ADMIN PANEL (mantido)
--------------------------------------------------
local function openAdmin()
	local gui = Instance.new("ScreenGui", guiParent)
	local f = Instance.new("Frame", gui)
	f.Size = UDim2.fromOffset(420,520)
	f:SetAttribute("FullSize", f.Size)
	f.Position = UDim2.new(0.5,-210,0.5,-260)
	f.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", f)
	makeDraggable(f)

	addWindowButtons(f, function() gui:Destroy() end)

	local list = Instance.new("ScrollingFrame", f)
	list.Position = UDim2.new(0,10,0,46)
	list.Size = UDim2.new(1,-20,1,-56)
	list.AutomaticCanvasSize = Enum.AutomaticSize.Y
	list.ScrollBarImageTransparency = 0.4
	list.BackgroundTransparency = 1

	local l = Instance.new("UIListLayout", list)
	l.Padding = UDim.new(0,4)

	for k,v in pairs(Keys) do
		local t = Instance.new("TextLabel", list)
		t.Size = UDim2.new(1,0,0,22)
		t.Text = k.." | usos: "..v
		t.Font = Enum.Font.Gotham
		t.TextSize = 12
		t.TextColor3 = Color3.new(1,1,1)
		t.BackgroundTransparency = 1
	end
end

--------------------------------------------------
-- KEY MENU (ANIMATED)
--------------------------------------------------
do
	local gui = Instance.new("ScreenGui", guiParent)
	local f = Instance.new("Frame", gui)
	f.Size = UDim2.fromOffset(360,240)
	f:SetAttribute("FullSize", f.Size)
	f.Position = UDim2.new(0.5,-180,0.6,0)
	f.BackgroundTransparency = 1
	f.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", f)
	makeDraggable(f)

	TweenService:Create(f,TweenInfo.new(0.4),{
		Position = UDim2.new(0.5,-180,0.5,-120),
		BackgroundTransparency = 0
	}):Play()

	addWindowButtons(f, function() gui:Destroy() end)

	local box = Instance.new("TextBox", f)
	box.Size = UDim2.new(0.9,0,0,36)
	box.Position = UDim2.new(0.05,0,0,80)
	box.PlaceholderText = "Digite sua KEY"
	box.Font = Enum.Font.Gotham
	box.TextSize = 14
	box.BackgroundColor3 = Color3.fromRGB(30,30,30)
	box.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", box)

	local info = Instance.new("TextLabel", f)
	info.Size = UDim2.new(1,0,0,20)
	info.Position = UDim2.new(0,0,0,125)
	info.Font = Enum.Font.Gotham
	info.TextSize = 14
	info.TextColor3 = Color3.fromRGB(255,80,80)
	info.BackgroundTransparency = 1

	local btn = Instance.new("TextButton", f)
	btn.Size = UDim2.new(0.5,0,0,36)
	btn.Position = UDim2.new(0.25,0,1,-50)
	btn.Text = "Confirmar"
	btn.Font = Enum.Font.GothamBold
	btn.TextSize = 14
	btn.BackgroundColor3 = Color3.fromRGB(0,170,90)
	btn.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", btn)

	btn.MouseButton1Click:Connect(function()
		local k = box.Text
		if k == ADMIN_KEY then
			gui:Destroy()
			openAdmin()
		elseif isValidKey(k) then
			Keys[k] -= 1
			gui:Destroy()
			openFPSOptimizer()
		else
			info.Text = "Key inválida"
			box.Text = ""
		end
	end)
end
