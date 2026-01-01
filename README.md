--=====================================================
-- FPS OPTIMIZER
-- Criador: Frostzn
-- Versão: 1.2 Beta
--=====================================================

---------------- SERVIÇOS ----------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local TweenService = game:GetService("TweenService")
local UIS = game:GetService("UserInputService")

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

---------------- CONFIG ----------------
local ADMIN_KEY = "261120"

---------------- KEYS FIXAS ----------------
math.randomseed(261120)
local Keys = {}
for i = 1,1000 do
	local k = "FPS-"..i.."-"..math.random(100000,999999)
	Keys[k] = {uses = 0, max = 3}
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

---------------- LOADING ----------------
local LoadGui = Instance.new("ScreenGui", PlayerGui)
local bg = Instance.new("Frame", LoadGui)
bg.Size = UDim2.fromScale(1,1)
bg.BackgroundColor3 = Color3.fromRGB(10,10,10)

local barBg = Instance.new("Frame", bg)
barBg.Size = UDim2.new(0.4,0,0,10)
barBg.Position = UDim2.new(0.3,0,0.55,0)
barBg.BackgroundColor3 = Color3.fromRGB(40,40,40)

local bar = Instance.new("Frame", barBg)
bar.Size = UDim2.new(0,0,1,0)
bar.BackgroundColor3 = Color3.fromRGB(0,200,120)

TweenService:Create(bar, TweenInfo.new(2), {
	Size = UDim2.new(1,0,1,0)
}):Play()

task.wait(2.2)
LoadGui:Destroy()

---------------- KEY UI ----------------
local KeyGui = Instance.new("ScreenGui", PlayerGui)

local frame = Instance.new("Frame", KeyGui)
frame.Size = UDim2.fromOffset(360,220)
frame.Position = UDim2.new(0.5,-180,0.5,-110)
frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
Instance.new("UICorner", frame)

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1,0,0,40)
title.Text = "INSIRA A KEY"
title.TextColor3 = Color3.fromRGB(0,255,120)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBold

local box = Instance.new("TextBox", frame)
box.Size = UDim2.new(0.9,0,0,35)
box.Position = UDim2.new(0.05,0,0.45,0)
box.PlaceholderText = "Digite a key..."
box.TextColor3 = Color3.new(1,1,1)
box.BackgroundColor3 = Color3.fromRGB(40,40,40)

local confirm = Instance.new("TextButton", frame)
confirm.Size = UDim2.new(0.9,0,0,35)
confirm.Position = UDim2.new(0.05,0,0.7,0)
confirm.Text = "CONFIRMAR"
confirm.BackgroundColor3 = Color3.fromRGB(0,170,100)
confirm.TextColor3 = Color3.new(1,1,1)

---------------- MENU PRINCIPAL ----------------
local function createMainMenu()

	local Gui = Instance.new("ScreenGui", PlayerGui)

	-- MAIN FRAME
	local Main = Instance.new("Frame", Gui)
	Main.Size = UDim2.fromOffset(420,480)
	Main.Position = UDim2.new(0.5,-210,0.5,-240)
	Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", Main)

	-- DRAG COMPLETO
	local dragging, dragStart, startPos
	Main.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			dragStart = i.Position
			startPos = Main.Position
		end
	end)
	Main.InputEnded:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = false
		end
	end)
	UIS.InputChanged:Connect(function(i)
		if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then
			local delta = i.Position - dragStart
			Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y)
		end
	end)

	-- TOP BAR
	local top = Instance.new("Frame", Main)
	top.Size = UDim2.new(1,0,0,40)
	top.BackgroundColor3 = Color3.fromRGB(25,25,25)

	local title = Instance.new("TextLabel", top)
	title.Size = UDim2.new(1,-80,1,0)
	title.Text = "FPS OPTIMIZER v1.2 Beta"
	title.TextColor3 = Color3.fromRGB(0,255,120)
	title.BackgroundTransparency = 1
	title.Font = Enum.Font.GothamBold

	-- CLOSE
	local close = Instance.new("TextButton", top)
	close.Size = UDim2.fromOffset(30,30)
	close.Position = UDim2.new(1,-35,0,5)
	close.Text = "X"
	close.BackgroundColor3 = Color3.fromRGB(170,60,60)

	-- MINIMIZE
	local minimize = Instance.new("TextButton", top)
	minimize.Size = UDim2.fromOffset(30,30)
	minimize.Position = UDim2.new(1,-70,0,5)
	minimize.Text = "-"
	minimize.BackgroundColor3 = Color3.fromRGB(90,90,90)

	-- INFO
	local info = Instance.new("TextLabel", Main)
	info.Size = UDim2.new(1,0,0,30)
	info.Position = UDim2.new(0,0,1,-30)
	info.Text = "Criado por Frostzn | Mais funções em breve..."
	info.TextSize = 12
	info.TextColor3 = Color3.fromRGB(180,180,180)
	info.BackgroundTransparency = 1

	-- FPS COUNTER
	local fpsLabel = Instance.new("TextLabel", Gui)
	fpsLabel.Position = UDim2.new(1,-120,0,10)
	fpsLabel.Size = UDim2.fromOffset(110,30)
	fpsLabel.BackgroundColor3 = Color3.fromRGB(0,0,0)
	fpsLabel.TextColor3 = Color3.fromRGB(0,255,120)
	fpsLabel.Visible = false

	local fpsOn = false
	local frames, last = 0, tick()
	RunService.RenderStepped:Connect(function()
		if fpsOn then
			frames += 1
			if tick() - last >= 1 then
				fpsLabel.Text = "FPS: "..frames
				frames = 0
				last = tick()
			end
		end
	end)

	-- TOGGLE FPS COUNTER
	local fpsBtn = Instance.new("TextButton", Main)
	fpsBtn.Size = UDim2.new(0.9,0,0,35)
	fpsBtn.Position = UDim2.new(0.05,0,0.15,0)
	fpsBtn.Text = "Contador de FPS: OFF"
	fpsBtn.BackgroundColor3 = Color3.fromRGB(40,40,40)
	fpsBtn.TextColor3 = Color3.new(1,1,1)

	fpsBtn.MouseButton1Click:Connect(function()
		fpsOn = not fpsOn
		fpsLabel.Visible = fpsOn
		fpsBtn.Text = "Contador de FPS: "..(fpsOn and "ON" or "OFF")
	end)

	-- CLOSE CONFIRM
	close.MouseButton1Click:Connect(function()
		if confirm.Text ~= "CONFIRMAR" then return end
		confirm.Text = "Fechar? Não poderá abrir novamente"
	end)

	minimize.MouseButton1Click:Connect(function()
		Main.Visible = false
	end)
end

---------------- CONFIRM KEY ----------------
confirm.MouseButton1Click:Connect(function()
	local ok = validateKey(box.Text)
	if ok then
		KeyGui:Destroy()
		createMainMenu()
	else
		confirm.Text = "KEY INVÁLIDA"
		confirm.BackgroundColor3 = Color3.fromRGB(170,60,60)
	end
end)
