--[[ 
 ULTIMATE ROBLOX PERFORMANCE OPTIMIZER
 FPS • LAG • TEXTURES • LIGHTING • MOBILE & PC
]]

-------------------------------
-- SERVICES
-------------------------------
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer

-------------------------------
-- GUI
-------------------------------
local ScreenGui = Instance.new("ScreenGui", player.PlayerGui)
ScreenGui.Name = "OptimizerGUI"
ScreenGui.ResetOnSpawn = false

local Main = Instance.new("Frame", ScreenGui)
Main.Size = UDim2.new(0, 350, 0, 420)
Main.Position = UDim2.new(0.5, -175, 0.5, -210)
Main.BackgroundColor3 = Color3.fromRGB(20,20,20)
Main.BackgroundTransparency = 1
Main.BorderSizePixel = 0

local Corner = Instance.new("UICorner", Main)
Corner.CornerRadius = UDim.new(0, 16)

-------------------------------
-- ANIMAÇÃO DE INÍCIO
-------------------------------
local fade = TweenService:Create(
    Main,
    TweenInfo.new(1, Enum.EasingStyle.Quad),
    {BackgroundTransparency = 0}
)
fade:Play()

-------------------------------
-- TÍTULO
-------------------------------
local Title = Instance.new("TextLabel", Main)
Title.Size = UDim2.new(1, 0, 0, 50)
Title.Text = "🚀 FPS OPTIMIZER"
Title.TextColor3 = Color3.fromRGB(0,255,120)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 22
Title.BackgroundTransparency = 1

-------------------------------
-- FUNÇÃO BOTÃO
-------------------------------
local function CreateButton(text, y, callback)
    local btn = Instance.new("TextButton", Main)
    btn.Size = UDim2.new(0.9, 0, 0, 35)
    btn.Position = UDim2.new(0.05, 0, 0, y)
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

-- 1 FPS Boost
local function FPSBoost()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end

-- 2 Texturas
local function OptimizeTextures()
    for _,v in pairs(workspace:GetDescendants()) do
        if v:IsA("BasePart") then
            v.Material = Enum.Material.Plastic
            v.Reflectance = 0
        end
    end
end

-- 3 Partículas
local function DisableParticles()
    for _,v in pairs(workspace:GetDescendants()) do
        if v:IsA("ParticleEmitter") or v:IsA("Trail") then
            v.Enabled = false
        end
    end
end

-- 4 Sombras
local function DisableShadows()
    Lighting.GlobalShadows = false
end

-- 5 Iluminação
local function OptimizeLighting()
    Lighting.Brightness = 1
    Lighting.FogEnd = 1e9
end

-- 6 Pós-processamento
local function RemoveEffects()
    for _,v in pairs(Lighting:GetChildren()) do
        if v:IsA("PostEffect") then
            v.Enabled = false
        end
    end
end

-- 7 Física
local function OptimizePhysics()
    sethiddenproperty(workspace, "InterpolationThrottling", Enum.InterpolationThrottlingMode.Enabled)
end

-- 8 Água
local function OptimizeWater()
    workspace.WaterWaveSize = 0
    workspace.WaterWaveSpeed = 0
    workspace.WaterTransparency = 1
end

-- 9 Reflexos
local function DisableReflections()
    Lighting.EnvironmentSpecularScale = 0
end

-- 10 Render Distance
local function ReduceRender()
    for _,v in pairs(workspace:GetDescendants()) do
        if v:IsA("BasePart") then
            v.CastShadow = false
        end
    end
end

-- 11 Animações
local function DisableAnimations()
    for _,v in pairs(workspace:GetDescendants()) do
        if v:IsA("Animator") then
            v:Destroy()
        end
    end
end

-- 12 AUTO OPTIMIZER
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
CreateButton("🧱 TEXTURAS LEVES", 110, OptimizeTextures)
CreateButton("✨ REMOVER PARTÍCULAS", 150, DisableParticles)
CreateButton("🌑 DESATIVAR SOMBRAS", 190, DisableShadows)
CreateButton("💡 OTIMIZAR LUZ", 230, OptimizeLighting)
CreateButton("🎨 REMOVER EFEITOS", 270, RemoveEffects)
CreateButton("🌊 OTIMIZAR ÁGUA", 310, OptimizeWater)
CreateButton("🤖 AUTO OPTIMIZER", 350, AutoOptimizer)

print("✅ FPS OPTIMIZER CARREGADO COM SUCESSO!")
