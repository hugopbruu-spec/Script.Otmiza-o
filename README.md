--[[=====================================================
 FPS OPTIMIZER
 Criador: Frostzn
 Versão: 1.2 Beta
=======================================================]]

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

--------------------------------------------------
-- DRAG
--------------------------------------------------
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

--------------------------------------------------
-- KEYS
--------------------------------------------------
local ADMIN_KEY = "261120"
local Keys = {}
for i = 1,1000 do
	local k = "FPS-"..string.format("%04d", i).."-KEY"
	Keys[k] = {uses = 0, max = 3}
end

local function validateKey(k)
	if k == ADMIN_KEY then return true, "ADMIN" end
	if Keys[k] and Keys[k].uses < Keys[k].max then
		Keys[k].uses += 1
		return true, "USER"
	end
	return false
end

--------------------------------------------------
-- LOADING
--------------------------------------------------
local loadGui = Instance.new("ScreenGui", PlayerGui)
local lf = Instance.new("Frame", loadGui)
lf.Size = UDim2.fromScale(1,1)
lf.BackgroundColor3 = Color3.fromRGB(10,10,10)

local txt = Instance.new("TextLabel", lf)
txt.Size = UDim2.new(1,0,0,40)
txt.Position = UDim2.new(0,0,0.45,0)
txt.Font = Enum.Font.GothamBold
txt.TextSize = 18
txt.TextColor3 = Color3.fromRGB(0,255,120)
txt.BackgroundTransparency = 1

for i=0,100 do
	txt.Text = "Carregando... "..i.."%"
	task.wait(0.02)
end
loadGui:Destroy()

--------------------------------------------------
-- KEY MENU
--------------------------------------------------
local keyGui = Instance.new("ScreenGui", PlayerGui)
local frame = Instance.new("Frame", keyGui)
frame.Size = UDim2.fromOffset(360,220)
frame.Position = UDim2.new(0.5,-180,0.5,-110)
frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
Instance.new("UICorner", frame)
makeDraggable(frame)

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1,0,0,40)
title.Text = "🔐 DIGITE A KEY"
title.Font = Enum.Font.GothamBold
title.TextColor3 = Color3.fromRGB(0,255,120)
title.BackgroundTransparency = 1

local box = Instance.new("TextBox", frame)
box.Size = UDim2.new(0.9,0,0,36)
box.Position = UDim2.new(0.05,0,0.4,0)
box.PlaceholderText = "Key..."
box.BackgroundColor3 = Color3.fromRGB(40,40,40)
box.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", box)

local btn = Instance.new("TextButton", frame)
btn.Size = UDim2.new(0.9,0,0,36)
btn.Position = UDim2.new(0.05,0,0.68,0)
btn.Text = "VALIDAR"
btn.BackgroundColor3 = Color3.fromRGB(0,170,90)
btn.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", btn)

--------------------------------------------------
-- ADMIN PANEL
--------------------------------------------------
local function openAdmin()
	local ag = Instance.new("ScreenGui", PlayerGui)
	local main = Instance.new("Frame", ag)
	main.Size = UDim2.fromOffset(600,450)
	main.Position = UDim2.new(0.5,-300,0.5,-225)
	main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", main)
	makeDraggable(main)

	local t = Instance.new("TextLabel", main)
	t.Size = UDim2.new(1,0,0,40)
	t.Text = "👑 ADMIN - KEYS FIXAS"
	t.Font = Enum.Font.GothamBold
	t.TextColor3 = Color3.fromRGB(255,200,0)
	t.BackgroundTransparency = 1

	local list = Instance.new("ScrollingFrame", main)
	list.Position = UDim2.new(0,10,0,50)
	list.Size = UDim2.new(1,-20,1,-100)
	list.AutomaticCanvasSize = Enum.AutomaticSize.Y
	list.CanvasSize = UDim2.new(0,0,0,0)
	list.BackgroundTransparency = 1

	local layout = Instance.new("UIListLayout", list)
	layout.Padding = UDim.new(0,4)

	for k,v in pairs(Keys) do
		local l = Instance.new("TextLabel", list)
		l.Size = UDim2.new(1,0,0,22)
		l.Text = k.." | usos "..v.uses.."/"..v.max
		l.TextXAlignment = Left
		l.TextColor3 = Color3.new(1,1,1)
		l.BackgroundTransparency = 1
	end
end

--------------------------------------------------
-- VALIDATE
--------------------------------------------------
btn.MouseButton1Click:Connect(function()
	local ok,mode = validateKey(box.Text)
	if not ok then
		btn.Text = "KEY INVÁLIDA"
		task.wait(1)
		btn.Text = "VALIDAR"
		box.Text = ""
		return
	end
	keyGui:Destroy()
	if mode == "ADMIN" then
		openAdmin()
	end
	_G.START_OPTIMIZER = true
end)
