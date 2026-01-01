-- ROBLOX ULTIMATE FPS OPTIMIZER (FIX UI)

-------------------------------
-- SERVICES
-------------------------------
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local RunService = game:GetService("RunService")

-------------------------------
-- PLAYER READY
-------------------------------
local player = Players.LocalPlayer or Players.PlayerAdded:Wait()
local PlayerGui = player:WaitForChild("PlayerGui")

-------------------------------
-- GUI
-------------------------------
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "OptimizerGUI"
ScreenGui.IgnoreGuiInset = true
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui

-------------------------------
-- MAIN FRAME
-------------------------------
local Main = Instance.new("Frame")
Main.Parent = ScreenGui
Main.Size = UDim2.new(0, 350, 0, 420)
Main.Position = UDim2.new(0.5, -175, 0.5, -210)
Main.BackgroundColor3 = Color3.fromRGB(20,20,20)
Main.BorderSizePixel = 0

Instance.new("UICorner", Main).CornerRadius = UDim.new(0,16)

-------------------------------
-- ANIMAÇÃO DE ENTRADA
-------------------------------
Main.BackgroundTransparency = 1
Main.Size = UDim2.new(0, 300, 0, 380)

TweenService:Create(
    Main,
    TweenInfo.new(0.8, Enum.EasingStyle.Back),
    {
        BackgroundTransparency = 0,
        Size = UDim2.new(0, 350, 0, 420)
    }
):Play()

-------------------------------
-- TÍTULO
-------------------------------
local Title = Instance.new("TextLabel", Main)
Title.Size = UDim2.new(1,0,0,50)
Title.Text = "🚀 FPS OPTIMIZER"
Title.TextColor3 = Color3.fromRGB(0,255,120)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 22
Title.BackgroundTransparency = 1

-------------------------------
-- FUNÇÃO BOTÃO
-------------------------------
local function CreateButton(text, y, callback)
    local btn = Instance.new("TextButton")
    btn.Parent = Main
    btn.Size = UDim2.new(0.9,0,0,36)
    btn.Position = UDim2.new(0.05,0,0,y)
    btn.Text = text
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 14
    btn.TextColor3 = Color3.new(1,1,1)
    btn.BackgroundColor3 = Color3.fromRGB(35,35,35)
    btn.BorderSizePixel = 0

    Instance.new("UICorner", btn).CornerRadius = UDim.new(0,10)

    btn.MouseButton1Click:Connect(callback)
end

-------------------------------
-- FUNÇÕES DE OTIMIZAÇÃO
-------------------------------

local function FPSBoost()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end

local function OptimizeTextures()
    for _,v in pairs(workspace:GetDescendants()) do
        if v:IsA("BasePart") then
            v.Material = Enum.Material.Plastic
            v.Reflectance = 0
            v.CastShadow = false
        end
    end
end

local function DisableParticles()
    for _,v in pairs(workspace:GetDescendants()) do
        if v:IsA("ParticleEmitter") or v:IsA("Trail") then
            v.Enabled = false
        end
    end
end

local function OptimizeLighting()
    Lighting.GlobalShadows = false
    Lighting.Brightness = 1
    Lighting.FogEnd = 1e9
    Lighting.EnvironmentSpecularScale = 0
end

local function RemoveEffects()
    for _,v in pairs(Lighting:GetChildren()) do
        if v:IsA("PostEffect") then
            v.Enabled = false
        end
    end
end

local function OptimizeWater()
    workspace.WaterWaveSize = 0
    workspace.WaterWaveSpeed = 0
    workspace.WaterTransparency = 1
end

local function AutoOptimizer()
    RunService.RenderStepped:Connect(function()
        if workspace:GetRealPhysicsFPS() < 50 then
            FPSBoost()
            OptimizeTextures()
            DisableParticles()
        end
    end)
end

-------------------------------
-- BOTÕES
-------------------------------
CreateButton("⚡ FPS BOOST", 70, FPSBoost)
CreateButton("🧱 TEXTURAS LEVES", 115, OptimizeTextures)
CreateButton("✨ REMOVER PARTÍCULAS", 160, DisableParticles)
CreateButton("💡 OTIMIZAR LUZ", 205, OptimizeLighting)
CreateButton("🎨 REMOVER EFEITOS", 250, RemoveEffects)
CreateButton("🌊 OTIMIZAR ÁGUA", 295, OptimizeWater)
CreateButton("🤖 AUTO OPTIMIZER", 340, AutoOptimizer)

print("✅ FPS OPTIMIZER INICIADO COM SUCESSO")
