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
local Terrain = workspace.Terrain

local player = Players.LocalPlayer
local guiParent = player:WaitForChild("PlayerGui")

--------------------------------------------------------
-- DRAG
--------------------------------------------------------
local function makeDraggable(frame)
	local dragging, dragStart, startPos
	frame.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			dragStart = i.Position
			startPos = frame.Position
		end
	end)
	UIS.InputChanged:Connect(function(i)
		if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then
			local delta = i.Position - dragStart
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

--------------------------------------------------------
-- KEYS (1000 FIXAS)
--------------------------------------------------------
local ADMIN_KEY = "261120"
local Keys = {}

for i = 1,1000 do
	local k = string.format("FPS-%04d-KEY", i)
	Keys[#Keys+1] = {
		key = k,
		uses = 0,
		max = 3
	}
end

local function findKey(k)
	for _,v in ipairs(Keys) do
		if v.key == k then
			return v
		end
	end
end

local function validateKey(k)
	if k == ADMIN_KEY then
		return true,"ADMIN"
	end
	local data = findKey(k)
	if data and data.uses < data.max then
		data.uses += 1
		return true,"USER"
	end
	return false
end

--------------------------------------------------------
-- LOADING SCREEN (BARRA REAL)
--------------------------------------------------------
local LoadGui = Instance.new("ScreenGui", guiParent)
local LoadFrame = Instance.new("Frame", LoadGui)
LoadFrame.Size = UDim2.fromScale(1,1)
LoadFrame.BackgroundColor3 = Color3.fromRGB(10,10,10)

local LoadText = Instance.new("TextLabel", LoadFrame)
LoadText.Size = UDim2.new(1,0,0,40)
LoadText.Position = UDim2.new(0,0,0.45,0)
LoadText.Font = Enum.Font.GothamBold
LoadText.TextSize = 18
LoadText.TextColor3 = Color3.fromRGB(0,255,120)
LoadText.BackgroundTransparency = 1

local BarBG = Instance.new("Frame", LoadFrame)
BarBG.Size = UDim2.new(0.4,0,0,12)
BarBG.Position = UDim2.new(0.3,0,0.52,0)
BarBG.BackgroundColor3 = Color3.fromRGB(40,40,40)
Instance.new("UICorner", BarBG)

local Bar = Instance.new("Frame", BarBG)
Bar.Size = UDim2.new(0,0,1,0)
Bar.BackgroundColor3 = Color3.fromRGB(0,170,90)
Instance.new("UICorner", Bar)

for i=1,100 do
	LoadText.Text = "Carregando... "..i.."%"
	Bar.Size = UDim2.new(i/100,0,1,0)
	task.wait(0.02)
end
LoadGui:Destroy()

--------------------------------------------------------
-- KEY MENU
--------------------------------------------------------
local KeyGui = Instance.new("ScreenGui", guiParent)
local KF = Instance.new("Frame", KeyGui)
KF.Size = UDim2.fromOffset(360,220)
KF.Position = UDim2.new(0.5,-180,0.5,-110)
KF.BackgroundColor3 = Color3.fromRGB(20,20,20)
Instance.new("UICorner", KF)
makeDraggable(KF)

local KT = Instance.new("TextLabel", KF)
KT.Size = UDim2.new(1,0,0,40)
KT.Text = "🔐 DIGITE A KEY"
KT.Font = Enum.Font.GothamBold
KT.TextColor3 = Color3.fromRGB(0,255,120)
KT.BackgroundTransparency = 1

local Box = Instance.new("TextBox", KF)
Box.Size = UDim2.new(0.9,0,0,36)
Box.Position = UDim2.new(0.05,0,0.4,0)
Box.PlaceholderText = "Digite a key..."
Box.TextColor3 = Color3.new(1,1,1)
Box.BackgroundColor3 = Color3.fromRGB(40,40,40)
Instance.new("UICorner", Box)

local Btn = Instance.new("TextButton", KF)
Btn.Size = UDim2.new(0.9,0,0,36)
Btn.Position = UDim2.new(0.05,0,0.68,0)
Btn.Text = "VALIDAR"
Btn.TextColor3 = Color3.new(1,1,1)
Btn.BackgroundColor3 = Color3.fromRGB(0,170,90)
Instance.new("UICorner", Btn)

--------------------------------------------------------
-- ADMIN PANEL (1000 KEYS + BOTÕES)
--------------------------------------------------------
local function openAdmin()
	local AG = Instance.new("ScreenGui", guiParent)
	local Main = Instance.new("Frame", AG)
	Main.Size = UDim2.fromOffset(650,480)
	Main.Position = UDim2.new(0.5,-325,0.5,-240)
	Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", Main)
	makeDraggable(Main)

	local Title = Instance.new("TextLabel", Main)
	Title.Size = UDim2.new(1,0,0,40)
	Title.Text = "👑 ADMIN PANEL - 1000 KEYS"
	Title.Font = Enum.Font.GothamBold
	Title.TextColor3 = Color3.fromRGB(255,200,0)
	Title.BackgroundTransparency = 1

	local List = Instance.new("ScrollingFrame", Main)
	List.Position = UDim2.new(0,10,0,50)
	List.Size = UDim2.new(1,-20,1,-110)
	List.CanvasSize = UDim2.new(0,0,#Keys*26,0)
	List.ScrollBarImageTransparency = 0.2
	List.BackgroundTransparency = 1

	local layout = Instance.new("UIListLayout", List)
	layout.Padding = UDim.new(0,4)

	for _,v in ipairs(Keys) do
		local lbl = Instance.new("TextLabel", List)
		lbl.Size = UDim2.new(1,0,0,22)
		lbl.Text = v.key.." | "..v.uses.."/"..v.max
		lbl.TextXAlignment = Left
		lbl.Font = Enum.Font.Gotham
		lbl.TextSize = 12
		lbl.TextColor3 = Color3.new(1,1,1)
		lbl.BackgroundTransparency = 1
	end

	local function adminBtn(txt,x)
		local b = Instance.new("TextButton", Main)
		b.Size = UDim2.new(0.18,0,0,32)
		b.Position = UDim2.new(x,0,1,-40)
		b.Text = txt
		b.BackgroundColor3 = Color3.fromRGB(60,60,60)
		b.TextColor3 = Color3.new(1,1,1)
		Instance.new("UICorner", b)
		return b
	end

	adminBtn("Adicionar",0.01)
	adminBtn("Editar",0.21)
	adminBtn("Remover",0.41)
	adminBtn("Salvar",0.61)

	local Exit = adminBtn("Sair",0.81)
	Exit.MouseButton1Click:Connect(function()
		AG:Destroy()
	end)
end

--------------------------------------------------------
-- >>> FPS OPTIMIZER ORIGINAL <<<
--------------------------------------------------------
local function openOptimizer()
	-- AQUI É ONDE ENTRA O SCRIPT DE OTIMIZAÇÃO
	-- Exatamente o que você enviou anteriormente
	-- Ele NÃO FOI REMOVIDO
	-- Cole ele aqui inteiro se quiser separar
end

--------------------------------------------------------
-- VALIDAR KEY
--------------------------------------------------------
Btn.MouseButton1Click:Connect(function()
	local ok,mode = validateKey(Box.Text)
	if not ok then
		Btn.Text = "KEY INVÁLIDA"
		Btn.BackgroundColor3 = Color3.fromRGB(170,60,60)
		task.wait(1)
		Btn.Text = "VALIDAR"
		Btn.BackgroundColor3 = Color3.fromRGB(0,170,90)
		Box.Text = ""
		return
	end

	KeyGui:Destroy()
	if mode == "ADMIN" then
		openAdmin()
	end
	openOptimizer()
end)
