--====================================================
-- FPS OPTIMIZER v1.2 Beta
-- Criador: Frostzn
--====================================================

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

------------------------------------------------------
-- DRAG FUNCTION (REUTILIZÁVEL)
------------------------------------------------------
local function makeDraggable(frame, dragArea)
	dragArea = dragArea or frame
	local dragging, dragStart, startPos

	dragArea.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			dragStart = input.Position
			startPos = frame.Position
			input.Changed:Connect(function()
				if input.UserInputState == Enum.UserInputState.End then
					dragging = false
				end
			end)
		end
	end)

	UIS.InputChanged:Connect(function(input)
		if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
			local delta = input.Position - dragStart
			frame.Position = UDim2.new(
				startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y
			)
		end
	end)
end

------------------------------------------------------
-- KEY SYSTEM
------------------------------------------------------
local ADMIN_KEY = "261120"

-- 1000 KEYS FIXAS
local Keys = {}
for i = 1,1000 do
	local key = string.format("FPS-%04d-261120", i)
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

------------------------------------------------------
-- LOADING SCREEN
------------------------------------------------------
local loadGui = Instance.new("ScreenGui", PlayerGui)
local lf = Instance.new("Frame", loadGui)
lf.Size = UDim2.fromScale(1,1)
lf.BackgroundColor3 = Color3.fromRGB(10,10,10)

local lt = Instance.new("TextLabel", lf)
lt.Size = UDim2.new(1,0,0,40)
lt.Position = UDim2.new(0,0,0.45,0)
lt.Text = "Carregando... 0%"
lt.TextColor3 = Color3.fromRGB(0,255,120)
lt.Font = Enum.Font.GothamBold
lt.TextSize = 18
lt.BackgroundTransparency = 1

for i=1,100 do
	lt.Text = "Carregando... "..i.."%"
	task.wait(0.02)
end
loadGui:Destroy()

------------------------------------------------------
-- KEY MENU
------------------------------------------------------
local keyGui = Instance.new("ScreenGui", PlayerGui)
local kf = Instance.new("Frame", keyGui)
kf.Size = UDim2.fromOffset(360,220)
kf.Position = UDim2.new(0.5,-180,0.5,-110)
kf.BackgroundColor3 = Color3.fromRGB(20,20,20)
Instance.new("UICorner", kf)

makeDraggable(kf)

local kt = Instance.new("TextLabel", kf)
kt.Size = UDim2.new(1,0,0,40)
kt.Text = "🔐 INSIRA A KEY"
kt.TextColor3 = Color3.fromRGB(0,255,120)
kt.Font = Enum.Font.GothamBold
kt.TextSize = 16
kt.BackgroundTransparency = 1

local box = Instance.new("TextBox", kf)
box.Size = UDim2.new(0.9,0,0,36)
box.Position = UDim2.new(0.05,0,0.4,0)
box.PlaceholderText = "Digite a key..."
box.BackgroundColor3 = Color3.fromRGB(40,40,40)
box.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", box)

local btn = Instance.new("TextButton", kf)
btn.Size = UDim2.new(0.9,0,0,36)
btn.Position = UDim2.new(0.05,0,0.68,0)
btn.Text = "VALIDAR"
btn.BackgroundColor3 = Color3.fromRGB(0,170,90)
btn.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", btn)

------------------------------------------------------
-- ADMIN PANEL
------------------------------------------------------
local function openAdmin(openOptimizer)
	local ag = Instance.new("ScreenGui", PlayerGui)
	local main = Instance.new("Frame", ag)
	main.Size = UDim2.fromOffset(560,440)
	main.Position = UDim2.new(0.5,-280,0.5,-220)
	main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", main)

	makeDraggable(main)

	local title = Instance.new("TextLabel", main)
	title.Size = UDim2.new(1,0,0,40)
	title.Text = "👑 ADMIN PANEL - KEYS"
	title.TextColor3 = Color3.fromRGB(255,200,0)
	title.Font = Enum.Font.GothamBold
	title.TextSize = 16
	title.BackgroundTransparency = 1

	local list = Instance.new("ScrollingFrame", main)
	list.Position = UDim2.new(0,10,0,50)
	list.Size = UDim2.new(1,-20,1,-140)
	list.CanvasSize = UDim2.new(0,0,0,0)
	list.AutomaticCanvasSize = Enum.AutomaticSize.Y
	list.BackgroundTransparency = 1

	local layout = Instance.new("UIListLayout", list)
	layout.Padding = UDim.new(0,4)

	local function refreshList()
		list:ClearAllChildren()
		layout.Parent = list
		for k,v in pairs(Keys) do
			local l = Instance.new("TextLabel", list)
			l.Size = UDim2.new(1,0,0,22)
			l.Text = k.." | usos: "..v.uses.."/"..v.max
			l.TextXAlignment = Left
			l.Font = Enum.Font.Gotham
			l.TextSize = 12
			l.TextColor3 = Color3.new(1,1,1)
			l.BackgroundTransparency = 1
		end
	end
	refreshList()

	local function adminButton(text,x)
		local b = Instance.new("TextButton", main)
		b.Size = UDim2.new(0.18,0,0,32)
		b.Position = UDim2.new(x,0,1,-40)
		b.Text = text
		b.TextSize = 12
		b.BackgroundColor3 = Color3.fromRGB(60,60,60)
		b.TextColor3 = Color3.new(1,1,1)
		Instance.new("UICorner", b)
		return b
	end

	local add = adminButton("Adicionar",0.01)
	local edit = adminButton("Editar",0.21)
	local remove = adminButton("Remover",0.41)
	local save = adminButton("Salvar",0.61)
	local exit = adminButton("Sair",0.81)

	add.MouseButton1Click:Connect(function()
		local newKey = "FPS-NEW-"..math.random(100000,999999)
		Keys[newKey] = {uses=0,max=3}
		refreshList()
	end)

	edit.MouseButton1Click:Connect(function()
		-- edição simples (exemplo)
		for k in pairs(Keys) do
			Keys[k].max = 5
			break
		end
		refreshList()
	end)

	remove.MouseButton1Click:Connect(function()
		for k in pairs(Keys) do
			Keys[k] = nil
			break
		end
		refreshList()
	end)

	save.MouseButton1Click:Connect(function()
		save.Text = "Salvo ✔"
		task.wait(1)
		save.Text = "Salvar"
	end)

	exit.MouseButton1Click:Connect(function()
		ag:Destroy()
	end)
end

------------------------------------------------------
-- OPTIMIZER (PLACEHOLDER DO SEU SCRIPT)
------------------------------------------------------
local function openOptimizer()
	warn("FPS OPTIMIZER ABERTO (aqui entra o script completo de otimização)")
end

------------------------------------------------------
-- KEY VALIDATION
------------------------------------------------------
btn.MouseButton1Click:Connect(function()
	local ok,mode = validateKey(box.Text)
	if ok then
		keyGui:Destroy()
		if mode == "ADMIN" then
			openAdmin(openOptimizer)
		else
			openOptimizer()
		end
	else
		btn.Text = "KEY INVÁLIDA"
		btn.BackgroundColor3 = Color3.fromRGB(170,60,60)
		task.wait(1)
		btn.Text = "VALIDAR"
		btn.BackgroundColor3 = Color3.fromRGB(0,170,90)
		box.Text = ""
	end
end)
