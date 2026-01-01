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

---------------- KEY SYSTEM ----------------
local ADMIN_KEY = "261120"

local Keys = {}
for i = 1,150 do
	Keys["FPSKEY-"..i.."-"..math.random(1000,9999)] = 0
end

local function isValidKey(key)
	if key == ADMIN_KEY then
		return true, "ADMIN"
	end
	if Keys[key] then
		if Keys[key] < 3 then
			Keys[key] += 1
			return true, "USER"
		end
	end
	return false
end

---------------- LOADING SCREEN ----------------
local LoadGui = Instance.new("ScreenGui", guiParent)
LoadGui.IgnoreGuiInset = true

local LoadFrame = Instance.new("Frame", LoadGui)
LoadFrame.Size = UDim2.fromScale(1,1)
LoadFrame.BackgroundColor3 = Color3.fromRGB(10,10,10)

local LoadText = Instance.new("TextLabel", LoadFrame)
LoadText.Size = UDim2.new(1,0,0,40)
LoadText.Position = UDim2.new(0,0,0.45,0)
LoadText.Text = "Carregando FPS Optimizer..."
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

TweenService:Create(Bar, TweenInfo.new(2), {
	Size = UDim2.new(1,0,1,0)
}):Play()

task.wait(2.2)
LoadGui:Destroy()

---------------- KEY GUI ----------------
local KeyGui = Instance.new("ScreenGui", guiParent)

local KeyFrame = Instance.new("Frame", KeyGui)
KeyFrame.Size = UDim2.fromOffset(320,200)
KeyFrame.Position = UDim2.new(0.5,-160,0.5,-100)
KeyFrame.BackgroundColor3 = Color3.fromRGB(20,20,20)
Instance.new("UICorner", KeyFrame)

local Title = Instance.new("TextLabel", KeyFrame)
Title.Size = UDim2.new(1,0,0,40)
Title.Text = "🔐 INSIRA A KEY"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 16
Title.TextColor3 = Color3.fromRGB(0,255,120)
Title.BackgroundTransparency = 1

local Box = Instance.new("TextBox", KeyFrame)
Box.Size = UDim2.new(0.9,0,0,36)
Box.Position = UDim2.new(0.05,0,0.4,0)
Box.PlaceholderText = "Digite a key..."
Box.Text = ""
Box.Font = Enum.Font.Gotham
Box.TextSize = 14
Box.BackgroundColor3 = Color3.fromRGB(40,40,40)
Box.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", Box)

local Btn = Instance.new("TextButton", KeyFrame)
Btn.Size = UDim2.new(0.9,0,0,36)
Btn.Position = UDim2.new(0.05,0,0.65,0)
Btn.Text = "VALIDAR"
Btn.Font = Enum.Font.GothamBold
Btn.TextSize = 14
Btn.BackgroundColor3 = Color3.fromRGB(0,160,90)
Btn.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", Btn)

---------------- ADMIN PANEL ----------------
local function openAdmin()
	KeyFrame:Destroy()

	local AdminGui = Instance.new("ScreenGui", guiParent)
	local Frame = Instance.new("Frame", AdminGui)
	Frame.Size = UDim2.fromOffset(400,400)
	Frame.Position = UDim2.new(0.5,-200,0.5,-200)
	Frame.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Instance.new("UICorner", Frame)

	local Scroll = Instance.new("ScrollingFrame", Frame)
	Scroll.Size = UDim2.new(1,-10,1,-10)
	Scroll.Position = UDim2.new(0,5,0,5)
	Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
	Scroll.CanvasSize = UDim2.new(0,0,0,0)
	Scroll.BackgroundTransparency = 1

	local Layout = Instance.new("UIListLayout", Scroll)

	for k,_ in pairs(Keys) do
		local lbl = Instance.new("TextLabel", Scroll)
		lbl.Size = UDim2.new(1,0,0,24)
		lbl.Text = k
		lbl.Font = Enum.Font.Gotham
		lbl.TextSize = 12
		lbl.TextColor3 = Color3.new(1,1,1)
		lbl.BackgroundTransparency = 1
	end
end

---------------- VALIDATE ----------------
Btn.MouseButton1Click:Connect(function()
	local key = Box.Text
	local ok, mode = isValidKey(key)

	if ok then
		KeyGui:Destroy()
		if mode == "ADMIN" then
			openAdmin()
		end
		loadstring(game:HttpGet("")) -- placeholder
	else
		Btn.Text = "KEY INVÁLIDA"
		Btn.BackgroundColor3 = Color3.fromRGB(170,60,60)
	end
end)
