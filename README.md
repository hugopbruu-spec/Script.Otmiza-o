--[[=====================================================
 FPS OPTIMIZER ULTIMATE
 Criador: Frostzn
 Versão: 1.3 Beta (UI Premium)
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
-- DRAG SYSTEM
--------------------------------------------------
local function makeDraggable(frame)
	frame.Active = true
	frame.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			local start = i.Position
			local startPos = frame.Position
			local conn
			conn = UIS.InputChanged:Connect(function(i2)
				if i2.UserInputType == Enum.UserInputType.MouseMovement then
					local delta = i2.Position - start
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
-- PREMIUM LOADING SCREEN
--------------------------------------------------
do
	local gui = Instance.new("ScreenGui", guiParent)
	gui.IgnoreGuiInset = true

	local bg = Instance.new("Frame", gui)
	bg.Size = UDim2.fromScale(1,1)
	bg.BackgroundColor3 = Color3.fromRGB(10,10,10)

	local grad = Instance.new("UIGradient", bg)
	grad.Color = ColorSequence.new{
		ColorSequenceKeypoint.new(0, Color3.fromRGB(0,160,90)),
		ColorSequenceKeypoint.new(1, Color3.fromRGB(0,60,40))
	}
	TweenService:Create(grad, TweenInfo.new(3, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut, -1), {
		Rotation = 360
	}):Play()

	local title = Instance.new("TextLabel", bg)
	title.Size = UDim2.new(1,0,0,50)
	title.Position = UDim2.new(0,0,0.42,0)
	title.Text = ""
	title.Font = Enum.Font.GothamBold
	title.TextSize = 22
	title.TextColor3 = Color3.fromRGB(0,255,140)
	title.BackgroundTransparency = 1

	local barBG = Instance.new("Frame", bg)
	barBG.Size = UDim2.new(0.4,0,0,12)
	barBG.Position = UDim2.new(0.3,0,0.52,0)
	barBG.BackgroundColor3 = Color3.fromRGB(30,30,30)
	Instance.new("UICorner", barBG)

	local bar = Instance.new("Frame", barBG)
	bar.Size = UDim2.new(0,0,1,0)
	bar.BackgroundColor3 = Color3.fromRGB(0,200,120)
	Instance.new("UICorner", bar)

	local text = "FPS OPTIMIZER v1.3"
	for i = 1,#text do
		title.Text = text:sub(1,i)
		task.wait(0.04)
	end

	for i=0,100 do
		bar.Size = UDim2.new(i/100,0,1,0)
		task.wait(0.015)
	end

	TweenService:Create(bg, TweenInfo.new(0.4), {BackgroundTransparency = 1}):Play()
	task.wait(0.4)
	gui:Destroy()
end

--------------------------------------------------
-- FPS OPTIMIZER UI
--------------------------------------------------
local miniButton

local function openFPSOptimizer()
	if miniButton then miniButton:Destroy() miniButton = nil end

	local gui = Instance.new("ScreenGui", guiParent)
	gui.ResetOnSpawn = false

	local main = Instance.new("Frame", gui)
	main.Size = UDim2.fromOffset(420,620)
	main.Position = UDim2.new(0.5,-210,0.5,-310)
	main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	main.BackgroundTransparency = 1
	Instance.new("UICorner", main)
	makeDraggable(main)

	TweenService:Create(main, TweenInfo.new(0.4), {
		BackgroundTransparency = 0
	}):Play()

	-- HEADER
	local header = Instance.new("Frame", main)
	header.Size = UDim2.new(1,0,0,48)
	header.BackgroundColor3 = Color3.fromRGB(22,22,22)
	Instance.new("UICorner", header)

	local title = Instance.new("TextLabel", header)
	title.Size = UDim2.new(1,-120,1,0)
	title.Position = UDim2.new(0,14,0,0)
	title.Text = "🚀 FPS OPTIMIZER | v1.3 Beta"
	title.Font = Enum.Font.GothamBold
	title.TextSize = 16
	title.TextColor3 = Color3.fromRGB(0,255,140)
	title.BackgroundTransparency = 1

	-- MINIMIZE
	local min = Instance.new("TextButton", header)
	min.Size = UDim2.fromOffset(30,30)
	min.Position = UDim2.new(1,-70,0,9)
	min.Text = "–"
	min.Font = Enum.Font.GothamBold
	min.TextSize = 22
	min.BackgroundColor3 = Color3.fromRGB(45,45,45)
	min.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", min)

	-- CLOSE
	local close = Instance.new("TextButton", header)
	close.Size = UDim2.fromOffset(30,30)
	close.Position = UDim2.new(1,-34,0,9)
	close.Text = "✕"
	close.Font = Enum.Font.GothamBold
	close.TextSize = 16
	close.BackgroundColor3 = Color3.fromRGB(180,70,70)
	close.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", close)

	--------------------------------------------------
	-- MINI FPS BUTTON
	--------------------------------------------------
	local function createMini()
		miniButton = Instance.new("TextButton", guiParent)
		miniButton.Size = UDim2.fromOffset(52,52)
		miniButton.Position = UDim2.new(0.02,0,0.5,-26)
		miniButton.Text = "FPS"
		miniButton.Font = Enum.Font.GothamBold
		miniButton.TextSize = 14
		miniButton.BackgroundColor3 = Color3.fromRGB(0,180,110)
		miniButton.TextColor3 = Color3.new(1,1,1)
		Instance.new("UICorner", miniButton)
		makeDraggable(miniButton)

		miniButton.MouseButton1Click:Connect(function()
			miniButton:Destroy()
			openFPSOptimizer()
		end)
	end

	min.MouseButton1Click:Connect(function()
		gui:Destroy()
		createMini()
	end)

	close.MouseButton1Click:Connect(function()
		gui:Destroy()
	end)

	--------------------------------------------------
	-- SCROLL
	--------------------------------------------------
	local scroll = Instance.new("ScrollingFrame", main)
	scroll.Position = UDim2.new(0,12,0,56)
	scroll.Size = UDim2.new(1,-24,1,-68)
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
				b.BackgroundColor3 = Color3.fromRGB(0,170,110)
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
-- KEY MENU
--------------------------------------------------
do
	local gui = Instance.new("ScreenGui", guiParent)
	local frame = Instance.new("Frame", gui)
	frame.Size = UDim2.fromOffset(360,230)
	frame.Position = UDim2.new(0.5,-180,0.5,-115)
	frame.BackgroundColor3 = Color3.fromRGB(18,18,18)
	frame.BackgroundTransparency = 1
	Instance.new("UICorner", frame)
	makeDraggable(frame)

	TweenService:Create(frame, TweenInfo.new(0.4), {
		BackgroundTransparency = 0
	}):Play()

	local box = Instance.new("TextBox", frame)
	box.Size = UDim2.new(0.9,0,0,36)
	box.Position = UDim2.new(0.05,0,0,80)
	box.PlaceholderText = "Digite sua key"
	box.Font = Enum.Font.Gotham
	box.TextSize = 14
	box.BackgroundColor3 = Color3.fromRGB(30,30,30)
	box.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", box)

	local info = Instance.new("TextLabel", frame)
	info.Size = UDim2.new(1,0,0,20)
	info.Position = UDim2.new(0,0,0,120)
	info.Font = Enum.Font.Gotham
	info.TextSize = 14
	info.TextColor3 = Color3.fromRGB(255,80,80)
	info.BackgroundTransparency = 1

	local btn = Instance.new("TextButton", frame)
	btn.Size = UDim2.new(0.5,0,0,36)
	btn.Position = UDim2.new(0.25,0,1,-50)
	btn.Text = "Confirmar"
	btn.Font = Enum.Font.GothamBold
	btn.TextSize = 14
	btn.BackgroundColor3 = Color3.fromRGB(0,170,110)
	btn.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", btn)

	btn.MouseButton1Click:Connect(function()
		local k = box.Text
		if k == ADMIN_KEY then
			gui:Destroy()
			openFPSOptimizer()
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
