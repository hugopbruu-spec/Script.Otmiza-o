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

--------------------------------------------------------
-- KEY SYSTEM
--------------------------------------------------------
local ADMIN_KEY = "261120"
math.randomseed(261120)

local Keys = {}
for i = 1,1000 do
	local key = "FPS-"..i.."-"..math.random(100000,999999)
	Keys[key] = {uses = 0, max = 3}
end

local function validateKey(key)
	if key == ADMIN_KEY then
		return true, "ADMIN"
	end
	if Keys[key] and Keys[key].uses < Keys[key].max then
		Keys[key].uses += 1
		return true, "USER"
	end
	return false
end

--------------------------------------------------------
-- LOADING SCREEN (0% → 100%)
--------------------------------------------------------
local LoadGui = Instance.new("ScreenGui", guiParent)
LoadGui.IgnoreGuiInset = true

local LoadFrame = Instance.new("Frame", LoadGui)
LoadFrame.Size = UDim2.fromScale(1,1)
LoadFrame.BackgroundColor3 = Color3.fromRGB(10,10,10)

local LoadText = Instance.new("TextLabel", LoadFrame)
LoadText.Size = UDim2.new(1,0,0,40)
LoadText.Position = UDim2.new(0,0,0.45,0)
LoadText.Text = "Carregando... 0%"
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

for i = 1,100 do
	Bar.Size = UDim2.new(i/100,0,1,0)
	LoadText.Text = "Carregando... "..i.."%"
	task.wait(0.02)
end

LoadGui:Destroy()

--------------------------------------------------------
-- KEY UI
--------------------------------------------------------
local KeyGui = Instance.new("ScreenGui", guiParent)

local KF = Instance.new("Frame", KeyGui)
KF.Size = UDim2.fromOffset(360,230)
KF.Position = UDim2.new(0.5,-180,0.5,-115)
KF.BackgroundColor3 = Color3.fromRGB(20,20,20)
Instance.new("UICorner", KF)

local KTitle = Instance.new("TextLabel", KF)
KTitle.Size = UDim2.new(1,0,0,40)
KTitle.Text = "🔐 INSIRA A KEY"
KTitle.Font = Enum.Font.GothamBold
KTitle.TextSize = 16
KTitle.TextColor3 = Color3.fromRGB(0,255,120)
KTitle.BackgroundTransparency = 1

local KBox = Instance.new("TextBox", KF)
KBox.Size = UDim2.new(0.9,0,0,36)
KBox.Position = UDim2.new(0.05,0,0.42,0)
KBox.PlaceholderText = "Digite a key..."
KBox.Text = ""
KBox.TextColor3 = Color3.new(1,1,1)
KBox.BackgroundColor3 = Color3.fromRGB(40,40,40)
Instance.new("UICorner", KBox)

local KBtn = Instance.new("TextButton", KF)
KBtn.Size = UDim2.new(0.9,0,0,36)
KBtn.Position = UDim2.new(0.05,0,0.68,0)
KBtn.Text = "VALIDAR"
KBtn.Font = Enum.Font.GothamBold
KBtn.TextSize = 14
KBtn.TextColor3 = Color3.new(1,1,1)
KBtn.BackgroundColor3 = Color3.fromRGB(0,170,90)
Instance.new("UICorner", KBtn)

--------------------------------------------------------
-- ADMIN PANEL
--------------------------------------------------------
local function openAdmin(openOptimizer)
	local AdminGui = Instance.new("ScreenGui", guiParent)

	local Main = Instance.new("Frame", AdminGui)
	Main.Size = UDim2.fromOffset(520,420)
	Main.Position = UDim2.new(0.5,-260,0.5,-210)
	Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", Main)

	local Title = Instance.new("TextLabel", Main)
	Title.Size = UDim2.new(1,0,0,40)
	Title.Text = "👑 ADMIN PANEL"
	Title.Font = Enum.Font.GothamBold
	Title.TextSize = 16
	Title.TextColor3 = Color3.fromRGB(255,200,0)
	Title.BackgroundTransparency = 1

	local Scroll = Instance.new("ScrollingFrame", Main)
	Scroll.Position = UDim2.new(0,10,0,50)
	Scroll.Size = UDim2.new(1,-20,1,-100)
	Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
	Scroll.CanvasSize = UDim2.new(0,0,0,0)
	Scroll.BackgroundTransparency = 1

	local layout = Instance.new("UIListLayout", Scroll)
	layout.Padding = UDim.new(0,6)

	for k,_ in pairs(Keys) do
		local lbl = Instance.new("TextLabel", Scroll)
		lbl.Size = UDim2.new(1,0,0,22)
		lbl.Text = k
		lbl.Font = Enum.Font.Gotham
		lbl.TextSize = 12
		lbl.TextXAlignment = Left
		lbl.TextColor3 = Color3.new(1,1,1)
		lbl.BackgroundTransparency = 1
	end

	local OpenBtn = Instance.new("TextButton", Main)
	OpenBtn.Size = UDim2.new(0.45,0,0,36)
	OpenBtn.Position = UDim2.new(0.05,0,1,-45)
	OpenBtn.Text = "Abrir Optimizer"
	OpenBtn.BackgroundColor3 = Color3.fromRGB(0,170,90)
	OpenBtn.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", OpenBtn)

	local ExitBtn = Instance.new("TextButton", Main)
	ExitBtn.Size = UDim2.new(0.45,0,0,36)
	ExitBtn.Position = UDim2.new(0.5,0,1,-45)
	ExitBtn.Text = "Sair"
	ExitBtn.BackgroundColor3 = Color3.fromRGB(170,60,60)
	ExitBtn.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", ExitBtn)

	OpenBtn.MouseButton1Click:Connect(function()
		AdminGui:Destroy()
		openOptimizer()
	end)

	ExitBtn.MouseButton1Click:Connect(function()
		AdminGui:Destroy()
	end)
end

--------------------------------------------------------
-- FPS OPTIMIZER (SEU SCRIPT ORIGINAL)
--------------------------------------------------------
local function openOptimizer()
	-- AQUI entra exatamente o script que você enviou
	-- sem alterações de lógica
	-- ele já está correto e funcional
end

--------------------------------------------------------
-- KEY VALIDATION
--------------------------------------------------------
KBtn.MouseButton1Click:Connect(function()
	local ok, mode = validateKey(KBox.Text)
	if ok then
		KeyGui:Destroy()
		if mode == "ADMIN" then
			openAdmin(openOptimizer)
		else
			openOptimizer()
		end
	else
		KBtn.Text = "KEY INVÁLIDA"
		KBtn.BackgroundColor3 = Color3.fromRGB(170,60,60)
		task.wait(1.2)
		KBtn.Text = "VALIDAR"
		KBtn.BackgroundColor3 = Color3.fromRGB(0,170,90)
		KBox.Text = ""
	end
end)
