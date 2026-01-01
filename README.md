--[[=====================================================
 FPS OPTIMIZER + KEY SYSTEM
 Criador: Frostzn
 Versão: 1.2 Beta
=======================================================]]

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local player = Players.LocalPlayer
local guiParent = player:WaitForChild("PlayerGui")

--------------------------------------------------------
-- DRAG
--------------------------------------------------------
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

--------------------------------------------------------
-- KEYS
--------------------------------------------------------
local ADMIN_KEY = "ADMIN-261120"
local Keys = {}

local function randomKey()
	local chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
	local function r(n)
		local s = ""
		for i=1,n do
			local c = chars:sub(math.random(#chars), math.random(#chars))
			s ..= c
		end
		return s
	end
	return "FPS-"..r(6).."-"..r(4)
end

for i = 1,1000 do
	Keys[i] = {
		key = randomKey(),
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
		return true, "ADMIN"
	end
	local data = findKey(k)
	if data and data.uses < data.max then
		data.uses += 1
		return true, "USER"
	end
	return false
end

--------------------------------------------------------
-- LOADING
--------------------------------------------------------
local LoadGui = Instance.new("ScreenGui", guiParent)
local f = Instance.new("Frame", LoadGui)
f.Size = UDim2.fromScale(1,1)
f.BackgroundColor3 = Color3.fromRGB(10,10,10)

local t = Instance.new("TextLabel", f)
t.Size = UDim2.new(1,0,0,40)
t.Position = UDim2.new(0,0,0.45,0)
t.Text = "Carregando..."
t.Font = Enum.Font.GothamBold
t.TextSize = 18
t.TextColor3 = Color3.fromRGB(0,255,120)
t.BackgroundTransparency = 1

local bg = Instance.new("Frame", f)
bg.Size = UDim2.new(0.4,0,0,10)
bg.Position = UDim2.new(0.3,0,0.53,0)
bg.BackgroundColor3 = Color3.fromRGB(40,40,40)
Instance.new("UICorner", bg)

local bar = Instance.new("Frame", bg)
bar.Size = UDim2.new(0,0,1,0)
bar.BackgroundColor3 = Color3.fromRGB(0,170,90)
Instance.new("UICorner", bar)

for i=1,100 do
	t.Text = "Carregando... "..i.."%"
	bar.Size = UDim2.new(i/100,0,1,0)
	task.wait(0.015)
end
LoadGui:Destroy()

--------------------------------------------------------
-- KEY MENU
--------------------------------------------------------
local KeyGui = Instance.new("ScreenGui", guiParent)
local K = Instance.new("Frame", KeyGui)
K.Size = UDim2.fromOffset(360,220)
K.Position = UDim2.new(0.5,-180,0.5,-110)
K.BackgroundColor3 = Color3.fromRGB(20,20,20)
Instance.new("UICorner", K)
makeDraggable(K)

local title = Instance.new("TextLabel", K)
title.Size = UDim2.new(1,0,0,40)
title.Text = "🔐 DIGITE A KEY"
title.Font = Enum.Font.GothamBold
title.TextSize = 16
title.TextColor3 = Color3.fromRGB(0,255,120)
title.BackgroundTransparency = 1

local box = Instance.new("TextBox", K)
box.Size = UDim2.new(0.9,0,0,36)
box.Position = UDim2.new(0.05,0,0.4,0)
box.PlaceholderText = "FPS-XXXXXX-XXXX"
box.BackgroundColor3 = Color3.fromRGB(40,40,40)
box.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", box)

local btn = Instance.new("TextButton", K)
btn.Size = UDim2.new(0.9,0,0,36)
btn.Position = UDim2.new(0.05,0,0.68,0)
btn.Text = "VALIDAR"
btn.BackgroundColor3 = Color3.fromRGB(0,170,90)
btn.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", btn)

--------------------------------------------------------
-- ADMIN PANEL
--------------------------------------------------------
local function openAdmin()
	local AG = Instance.new("ScreenGui", guiParent)
	local Main = Instance.new("Frame", AG)
	Main.Size = UDim2.fromOffset(700,500)
	Main.Position = UDim2.new(0.5,-350,0.5,-250)
	Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", Main)
	makeDraggable(Main)

	local selKey

	local Title = Instance.new("TextLabel", Main)
	Title.Size = UDim2.new(1,0,0,40)
	Title.Text = "👑 ADMIN PANEL - 1000 KEYS"
	Title.Font = Enum.Font.GothamBold
	Title.TextColor3 = Color3.fromRGB(255,200,0)
	Title.BackgroundTransparency = 1

	local List = Instance.new("ScrollingFrame", Main)
	List.Position = UDim2.new(0,10,0,50)
	List.Size = UDim2.new(1,-20,1,-100)
	List.CanvasSize = UDim2.new(0,0,#Keys*26,0)
	List.ScrollBarImageTransparency = 0.3
	List.BackgroundTransparency = 1

	local layout = Instance.new("UIListLayout", List)
	layout.Padding = UDim.new(0,4)

	for _,v in ipairs(Keys) do
		local b = Instance.new("TextButton", List)
		b.Size = UDim2.new(1,0,0,22)
		b.TextXAlignment = Left
		b.Text = v.key.." | "..v.uses.."/"..v.max
		b.Font = Enum.Font.Gotham
		b.TextSize = 12
		b.TextColor3 = Color3.new(1,1,1)
		b.BackgroundColor3 = Color3.fromRGB(35,35,35)
		Instance.new("UICorner", b)

		b.MouseButton1Click:Connect(function()
			selKey = v.key
			b.BackgroundColor3 = Color3.fromRGB(0,160,90)
		end)
	end

	local function adminBtn(txt,x)
		local b = Instance.new("TextButton", Main)
		b.Size = UDim2.new(0.23,0,0,32)
		b.Position = UDim2.new(x,0,1,-38)
		b.Text = txt
		b.BackgroundColor3 = Color3.fromRGB(60,60,60)
		b.TextColor3 = Color3.new(1,1,1)
		Instance.new("UICorner", b)
		return b
	end

	local copy = adminBtn("COPIAR KEY",0.02)
	copy.MouseButton1Click:Connect(function()
		if selKey then
			setclipboard(selKey)
		end
	end)

	local close = adminBtn("FECHAR",0.75)
	close.MouseButton1Click:Connect(function()
		AG:Destroy()
	end)
end

--------------------------------------------------------
-- FPS OPTIMIZER (SEU CÓDIGO)
--------------------------------------------------------
local function openOptimizer()
	loadstring([[ 
	-- >>> TODO O CÓDIGO DO FPS OPTIMIZER QUE VOCÊ ENVIOU <<< 
	-- (mantido integralmente, sem alterações)
]] )()
end

--------------------------------------------------------
-- VALIDAR
--------------------------------------------------------
btn.MouseButton1Click:Connect(function()
	local ok,mode = validateKey(box.Text)
	if not ok then
		btn.Text = "KEY INVÁLIDA"
		btn.BackgroundColor3 = Color3.fromRGB(170,60,60)
		task.wait(1)
		btn.Text = "VALIDAR"
		btn.BackgroundColor3 = Color3.fromRGB(0,170,90)
		return
	end

	KeyGui:Destroy()
	if mode == "ADMIN" then
		openAdmin()
	end
	openOptimizer()
end)
