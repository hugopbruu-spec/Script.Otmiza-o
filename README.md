--[[=====================================================
 FPS OPTIMIZER
 Criador: Frostzn
 Versão: 1.2 Beta
=======================================================]]

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

---------------- KEY SYSTEM ----------------
local ADMIN_KEY = "261120"
math.randomseed(261120)

local Keys = {}
for i = 1,1000 do
	Keys["FPS-"..i.."-"..math.random(100000,999999)] = {uses = 0, max = 3}
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

---------------- LOADING SCREEN ----------------
local LoadGui = Instance.new("ScreenGui", PlayerGui)
local lf = Instance.new("Frame", LoadGui)
lf.Size = UDim2.fromScale(1,1)
lf.BackgroundColor3 = Color3.fromRGB(10,10,10)

local barBG = Instance.new("Frame", lf)
barBG.Size = UDim2.new(0.4,0,0,12)
barBG.Position = UDim2.new(0.3,0,0.55,0)
barBG.BackgroundColor3 = Color3.fromRGB(40,40,40)

local bar = Instance.new("Frame", barBG)
bar.Size = UDim2.new(0,0,1,0)
bar.BackgroundColor3 = Color3.fromRGB(0,200,120)

TweenService:Create(bar, TweenInfo.new(2), {
	Size = UDim2.new(1,0,1,0)
}):Play()

task.wait(2.2)
LoadGui:Destroy()

---------------- KEY UI ----------------
local KeyGui = Instance.new("ScreenGui", PlayerGui)
local kf = Instance.new("Frame", KeyGui)
kf.Size = UDim2.fromOffset(350,220)
kf.Position = UDim2.new(0.5,-175,0.5,-110)
kf.BackgroundColor3 = Color3.fromRGB(20,20,20)
Instance.new("UICorner", kf)

local input = Instance.new("TextBox", kf)
input.Size = UDim2.new(0.9,0,0,35)
input.Position = UDim2.new(0.05,0,0.4,0)
input.PlaceholderText = "Digite a key"
input.TextColor3 = Color3.new(1,1,1)
input.BackgroundColor3 = Color3.fromRGB(40,40,40)

local btn = Instance.new("TextButton", kf)
btn.Size = UDim2.new(0.9,0,0,35)
btn.Position = UDim2.new(0.05,0,0.65,0)
btn.Text = "CONFIRMAR"
btn.BackgroundColor3 = Color3.fromRGB(0,170,100)
btn.TextColor3 = Color3.new(1,1,1)

---------------- MAIN MENU (FPS OPTIMIZER) ----------------
local function openMenu()
	local gui = Instance.new("ScreenGui", PlayerGui)

	local main = Instance.new("Frame", gui)
	main.Size = UDim2.fromOffset(420,460)
	main.Position = UDim2.new(0.5,-210,0.5,-230)
	main.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", main)

	-- DRAG
	local dragging, dragInput, dragStart, startPos
	main.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			dragStart = i.Position
			startPos = main.Position
		end
	end)
	main.InputEnded:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = false
		end
	end)
	UIS.InputChanged:Connect(function(i)
		if dragging and i == dragInput then
			local delta = i.Position - dragStart
			main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
				startPos.Y.Scale, startPos.Y.Offset + delta.Y)
		end
	end)
	main.InputChanged:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.MouseMovement then
			dragInput = i
		end
	end)

	-- TITLE
	local title = Instance.new("TextLabel", main)
	title.Size = UDim2.new(1,0,0,40)
	title.Text = "FPS OPTIMIZER v1.2 Beta"
	title.TextColor3 = Color3.fromRGB(0,255,120)
	title.BackgroundTransparency = 1

	-- INFO
	local info = Instance.new("TextLabel", main)
	info.Position = UDim2.new(0,0,1,-30)
	info.Size = UDim2.new(1,0,0,30)
	info.Text = "Criado por Frostzn | Mais funções em breve..."
	info.TextSize = 12
	info.TextColor3 = Color3.fromRGB(180,180,180)
	info.BackgroundTransparency = 1

	-- FPS COUNTER
	local fpsLabel = Instance.new("TextLabel", gui)
	fpsLabel.Position = UDim2.new(1,-110,0,10)
	fpsLabel.Size = UDim2.fromOffset(100,30)
	fpsLabel.BackgroundColor3 = Color3.fromRGB(0,0,0)
	fpsLabel.TextColor3 = Color3.fromRGB(0,255,120)
	fpsLabel.Visible = false

	local fpsOn = false
	local frames = 0
	local last = tick()
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
	local fpsBtn = Instance.new("TextButton", main)
	fpsBtn.Position = UDim2.new(0.05,0,0.15,0)
	fpsBtn.Size = UDim2.new(0.9,0,0,35)
	fpsBtn.Text = "Contador de FPS: OFF"
	fpsBtn.BackgroundColor3 = Color3.fromRGB(40,40,40)
	fpsBtn.TextColor3 = Color3.new(1,1,1)

	fpsBtn.MouseButton1Click:Connect(function()
		fpsOn = not fpsOn
		fpsLabel.Visible = fpsOn
		fpsBtn.Text = "Contador de FPS: "..(fpsOn and "ON" or "OFF")
	end)
end

---------------- KEY CONFIRM ----------------
btn.MouseButton1Click:Connect(function()
	local ok, mode = validateKey(input.Text)
	if ok then
		KeyGui:Destroy()
		openMenu()
	else
		btn.Text = "KEY INVÁLIDA"
		btn.BackgroundColor3 = Color3.fromRGB(170,60,60)
	end
end)
