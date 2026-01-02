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

---------------- DRAG ----------------
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

---------------- KEYS ----------------
local ADMIN_KEY = "261120"
local Keys = {}
local MAX_USES = 3

math.randomseed(123456)
for i=1,1000 do
	local key = "FPS-"..math.random(100000,999999).."-"..i
	Keys[key] = MAX_USES
end

local function isValidKey(k)
	return Keys[k] and Keys[k] > 0
end

---------------- LOADING ----------------
local LoadGui = Instance.new("ScreenGui", guiParent)
LoadGui.IgnoreGuiInset = true

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
	task.wait(0.02)
end

LoadGui:Destroy()

---------------- KEY MENU ----------------
local KeyGui = Instance.new("ScreenGui", guiParent)

local KeyFrame = Instance.new("Frame", KeyGui)
KeyFrame.Size = UDim2.fromOffset(360,220)
KeyFrame.Position = UDim2.new(0.5,-180,0.5,-110)
KeyFrame.BackgroundColor3 = Color3.fromRGB(18,18,18)
Instance.new("UICorner", KeyFrame)
makeDraggable(KeyFrame)

local title = Instance.new("TextLabel", KeyFrame)
title.Size = UDim2.new(1,0,0,40)
title.Text = "Digite sua KEY"
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.TextColor3 = Color3.fromRGB(0,255,120)
title.BackgroundTransparency = 1

local box = Instance.new("TextBox", KeyFrame)
box.Size = UDim2.new(0.9,0,0,36)
box.Position = UDim2.new(0.05,0,0,60)
box.PlaceholderText = "FPS-XXXXXX"
box.Font = Enum.Font.Gotham
box.TextSize = 14
box.TextColor3 = Color3.new(1,1,1)
box.BackgroundColor3 = Color3.fromRGB(30,30,30)
Instance.new("UICorner", box)

local info = Instance.new("TextLabel", KeyFrame)
info.Size = UDim2.new(1,0,0,20)
info.Position = UDim2.new(0,0,0,105)
info.Font = Enum.Font.Gotham
info.TextSize = 14
info.TextColor3 = Color3.fromRGB(255,80,80)
info.BackgroundTransparency = 1

local confirm = Instance.new("TextButton", KeyFrame)
confirm.Size = UDim2.new(0.5,0,0,36)
confirm.Position = UDim2.new(0.25,0,1,-50)
confirm.Text = "Confirmar"
confirm.Font = Enum.Font.GothamBold
confirm.TextSize = 14
confirm.BackgroundColor3 = Color3.fromRGB(0,170,90)
confirm.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", confirm)

------------------------------------------------
-- ADMIN PANEL
------------------------------------------------
local function openAdmin()
	local AdminGui = Instance.new("ScreenGui", guiParent)
	local f = Instance.new("Frame", AdminGui)
	f.Size = UDim2.fromOffset(420,500)
	f.Position = UDim2.new(0.5,-210,0.5,-250)
	f.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", f)
	makeDraggable(f)

	local title = Instance.new("TextLabel", f)
	title.Size = UDim2.new(1,0,0,40)
	title.Text = "Painel Admin - Keys"
	title.Font = Enum.Font.GothamBold
	title.TextSize = 18
	title.TextColor3 = Color3.fromRGB(0,255,120)
	title.BackgroundTransparency = 1

	local list = Instance.new("ScrollingFrame", f)
	list.Position = UDim2.new(0,10,0,50)
	list.Size = UDim2.new(1,-20,1,-110)
	list.CanvasSize = UDim2.new(0,0,0,0)
	list.AutomaticCanvasSize = Enum.AutomaticSize.Y
	list.ScrollBarImageTransparency = 0.4
	list.BackgroundTransparency = 1

	local layout = Instance.new("UIListLayout", list)
	layout.Padding = UDim.new(0,4)

	for k,v in pairs(Keys) do
		local lbl = Instance.new("TextLabel", list)
		lbl.Size = UDim2.new(1,0,0,24)
		lbl.Text = k.." | usos: "..v
		lbl.Font = Enum.Font.Gotham
		lbl.TextSize = 12
		lbl.TextColor3 = Color3.new(1,1,1)
		lbl.BackgroundTransparency = 1
	end

	local close = Instance.new("TextButton", f)
	close.Size = UDim2.new(0.4,0,0,36)
	close.Position = UDim2.new(0.3,0,1,-45)
	close.Text = "Fechar Admin"
	close.Font = Enum.Font.GothamBold
	close.TextSize = 14
	close.BackgroundColor3 = Color3.fromRGB(170,60,60)
	close.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", close)

	close.MouseButton1Click:Connect(function()
		AdminGui:Destroy()
	end)
end

------------------------------------------------
-- CONFIRM KEY
------------------------------------------------
confirm.MouseButton1Click:Connect(function()
	local k = box.Text
	if k == ADMIN_KEY then
		KeyGui:Destroy()
		openAdmin()
	elseif isValidKey(k) then
		Keys[k] -= 1
		KeyGui:Destroy()
		loadstring([[print("FPS OPTIMIZER ABERTO")]])()
	else
		info.Text = "Key inválida"
		box.Text = ""
	end
end)
