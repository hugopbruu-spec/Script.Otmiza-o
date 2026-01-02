--==================================================
-- FPS OPTIMIZER SYSTEM
-- Criador: Frostzn
--==================================================

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local Terrain = workspace.Terrain

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

---------------- DRAG ----------------
local function dragify(frame)
	local dragToggle, dragInput, dragStart, startPos
	frame.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragToggle = true
			dragStart = input.Position
			startPos = frame.Position
		end
	end)
	frame.InputChanged:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseMovement then
			dragInput = input
		end
	end)
	UserInputService.InputChanged:Connect(function(input)
		if input == dragInput and dragToggle then
			local delta = input.Position - dragStart
			frame.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)
	UserInputService.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragToggle = false
		end
	end)
end

---------------- SETTINGS BACKUP ----------------
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

---------------- KEYS ----------------
local ADMIN_KEY = "261120"
local MAX_USES = 3
local Keys = {}

math.randomseed(999)
for i = 1,1000 do
	local key = "FPS-"..math.random(100000,999999).."-"..i
	Keys[key] = MAX_USES
end

--------------------------------------------------
-- FPS OPTIMIZER MENU
--------------------------------------------------
local function OpenFPSOptimizer()

	local Gui = Instance.new("ScreenGui", PlayerGui)

	local Main = Instance.new("Frame", Gui)
	Main.Size = UDim2.fromOffset(360,520)
	Main.Position = UDim2.new(0.5,-180,0.5,-260)
	Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", Main)
	dragify(Main)

	-- HEADER
	local Header = Instance.new("Frame", Main)
	Header.Size = UDim2.new(1,0,0,42)
	Header.BackgroundColor3 = Color3.fromRGB(22,22,22)

	local Title = Instance.new("TextLabel", Header)
	Title.Size = UDim2.new(1,-90,1,0)
	Title.Position = UDim2.new(0,10,0,0)
	Title.Text = "FPS OPTIMIZER"
	Title.Font = Enum.Font.GothamBold
	Title.TextSize = 16
	Title.TextColor3 = Color3.fromRGB(0,255,120)
	Title.BackgroundTransparency = 1

	local Min = Instance.new("TextButton", Header)
	Min.Size = UDim2.fromOffset(28,28)
	Min.Position = UDim2.new(1,-64,0,7)
	Min.Text = "-"
	Min.Font = Enum.Font.GothamBold
	Min.TextSize = 22
	Min.BackgroundTransparency = 1
	Min.TextColor3 = Color3.new(1,1,1)

	local Close = Instance.new("TextButton", Header)
	Close.Size = UDim2.fromOffset(28,28)
	Close.Position = UDim2.new(1,-34,0,7)
	Close.Text = "X"
	Close.Font = Enum.Font.GothamBold
	Close.TextSize = 16
	Close.BackgroundTransparency = 1
	Close.TextColor3 = Color3.fromRGB(255,80,80)

	-- SCROLL
	local Scroll = Instance.new("ScrollingFrame", Main)
	Scroll.Position = UDim2.new(0,10,0,52)
	Scroll.Size = UDim2.new(1,-20,1,-104)
	Scroll.CanvasSize = UDim2.new(0,0,0,0)
	Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
	Scroll.ScrollBarImageTransparency = 0.3
	Scroll.BackgroundTransparency = 1

	local Layout = Instance.new("UIListLayout", Scroll)
	Layout.Padding = UDim.new(0,8)

	-- TOGGLE FUNCTION
	local function Toggle(text,on,off)
		local state = false
		local b = Instance.new("TextButton", Scroll)
		b.Size = UDim2.new(1,0,0,36)
		b.Text = text.." [OFF]"
		b.Font = Enum.Font.Gotham
		b.TextSize = 14
		b.TextColor3 = Color3.new(1,1,1)
		b.BackgroundColor3 = Color3.fromRGB(40,40,40)
		Instance.new("UICorner", b)

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

	-- FUNCTIONS (NADA REMOVIDO)
	Toggle("FPS BOOST", function()
		settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
	end, function()
		settings().Rendering.QualityLevel = Original.Quality
	end)

	Toggle("DESATIVAR SOMBRAS", function()
		Lighting.GlobalShadows = false
	end, function()
		Lighting.GlobalShadows = Original.Shadows
	end)

	Toggle("TEXTURAS LEVES", function()
		for _,v in pairs(workspace:GetDescendants()) do
			if v:IsA("BasePart") then
				v.CastShadow = false
				v.Material = Enum.Material.Plastic
			end
		end
	end)

	Toggle("OTIMIZAR AGUA", function()
		Terrain.WaterWaveSize = 0
		Terrain.WaterWaveSpeed = 0
		Terrain.WaterTransparency = 1
	end, function()
		Terrain.WaterWaveSize = Original.Water.WaveSize
		Terrain.WaterWaveSpeed = Original.Water.WaveSpeed
		Terrain.WaterTransparency = Original.Water.Transparency
	end)

	Toggle("REMOVER EFEITOS", function()
		for _,v in pairs(Lighting:GetChildren()) do
			if v:IsA("PostEffect") then
				v.Enabled = false
			end
		end
	end)

	-- CREDIT
	local Credit = Instance.new("TextLabel", Main)
	Credit.Size = UDim2.new(1,0,0,18)
	Credit.Position = UDim2.new(0,0,1,-20)
	Credit.Text = "Criador: Frostzn"
	Credit.Font = Enum.Font.Gotham
	Credit.TextSize = 12
	Credit.TextColor3 = Color3.fromRGB(160,160,160)
	Credit.BackgroundTransparency = 1

	-- MINI BUTTON
	local Mini = Instance.new("TextButton", Gui)
	Mini.Size = UDim2.fromOffset(50,50)
	Mini.Position = UDim2.new(0,20,0.5,-25)
	Mini.Text = "FPS"
	Mini.Visible = false
	Mini.Font = Enum.Font.GothamBold
	Mini.TextSize = 16
	Mini.BackgroundColor3 = Color3.fromRGB(0,170,90)
	Mini.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", Mini)
	dragify(Mini)

	Min.MouseButton1Click:Connect(function()
		Main.Visible = false
		Mini.Visible = true
	end)

	Mini.MouseButton1Click:Connect(function()
		Main.Visible = true
		Mini.Visible = false
	end)

	Close.MouseButton1Click:Connect(function()
		Gui:Destroy()
	end)
end

--------------------------------------------------
-- ADMIN PANEL
--------------------------------------------------
local function OpenAdminPanel()
	local Gui = Instance.new("ScreenGui", PlayerGui)

	local Main = Instance.new("Frame", Gui)
	Main.Size = UDim2.fromOffset(420,520)
	Main.Position = UDim2.new(0.5,-210,0.5,-260)
	Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", Main)
	dragify(Main)

	local Title = Instance.new("TextLabel", Main)
	Title.Size = UDim2.new(1,0,0,40)
	Title.Text = "PAINEL ADMIN - KEYS"
	Title.Font = Enum.Font.GothamBold
	Title.TextSize = 16
	Title.TextColor3 = Color3.fromRGB(255,170,0)
	Title.BackgroundTransparency = 1

	local Close = Instance.new("TextButton", Main)
	Close.Size = UDim2.fromOffset(30,30)
	Close.Position = UDim2.new(1,-36,0,6)
	Close.Text = "X"
	Close.Font = Enum.Font.GothamBold
	Close.TextSize = 16
	Close.TextColor3 = Color3.fromRGB(255,80,80)
	Close.BackgroundTransparency = 1

	local Scroll = Instance.new("ScrollingFrame", Main)
	Scroll.Position = UDim2.new(0,10,0,46)
	Scroll.Size = UDim2.new(1,-20,1,-56)
	Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
	Scroll.BackgroundTransparency = 1

	local Layout = Instance.new("UIListLayout", Scroll)
	Layout.Padding = UDim.new(0,4)

	for key,uses in pairs(Keys) do
		local l = Instance.new("TextLabel", Scroll)
		l.Size = UDim2.new(1,0,0,22)
		l.Text = key.." | Usos restantes: "..uses
		l.Font = Enum.Font.Code
		l.TextSize = 13
		l.TextXAlignment = Left
		l.TextColor3 = Color3.new(1,1,1)
		l.BackgroundTransparency = 1
	end

	Close.MouseButton1Click:Connect(function()
		Gui:Destroy()
	end)
end

--------------------------------------------------
-- KEY MENU
--------------------------------------------------
local KeyGui = Instance.new("ScreenGui", PlayerGui)

local Box = Instance.new("Frame", KeyGui)
Box.Size = UDim2.fromOffset(300,180)
Box.Position = UDim2.new(0.5,-150,0.5,-90)
Box.BackgroundColor3 = Color3.fromRGB(18,18,18)
Instance.new("UICorner", Box)
dragify(Box)

local Title = Instance.new("TextLabel", Box)
Title.Size = UDim2.new(1,0,0,36)
Title.Text = "INSIRA SUA KEY"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 15
Title.TextColor3 = Color3.fromRGB(0,255,120)
Title.BackgroundTransparency = 1

local Input = Instance.new("TextBox", Box)
Input.Size = UDim2.new(0.9,0,0,34)
Input.Position = UDim2.new(0.05,0,0,50)
Input.PlaceholderText = "FPS-XXXXXX"
Input.Text = ""
Input.Font = Enum.Font.Gotham
Input.TextSize = 14
Input.TextColor3 = Color3.new(1,1,1)
Input.BackgroundColor3 = Color3.fromRGB(30,30,30)
Instance.new("UICorner", Input)

local Status = Instance.new("TextLabel", Box)
Status.Size = UDim2.new(1,0,0,18)
Status.Position = UDim2.new(0,0,0,90)
Status.Text = ""
Status.Font = Enum.Font.Gotham
Status.TextSize = 12
Status.BackgroundTransparency = 1

local Confirm = Instance.new("TextButton", Box)
Confirm.Size = UDim2.new(0.5,0,0,30)
Confirm.Position = UDim2.new(0.25,0,1,-40)
Confirm.Text = "Confirmar"
Confirm.Font = Enum.Font.GothamBold
Confirm.TextSize = 14
Confirm.TextColor3 = Color3.new(1,1,1)
Confirm.BackgroundColor3 = Color3.fromRGB(0,170,90)
Instance.new("UICorner", Confirm)

Confirm.MouseButton1Click:Connect(function()
	local key = Input.Text

	if key == ADMIN_KEY then
		KeyGui:Destroy()
		OpenAdminPanel()
		return
	end

	if Keys[key] and Keys[key] > 0 then
		Keys[key] -= 1
		Status.Text = "Key válida! Abrindo menu..."
		Status.TextColor3 = Color3.fromRGB(0,255,120)
		task.wait(0.6)
		KeyGui:Destroy()
		OpenFPSOptimizer()
	else
		Status.Text = "Key inválida ou sem usos restantes"
		Status.TextColor3 = Color3.fromRGB(255,80,80)
		Input.Text = ""
	end
end)
 
