# Script.Otmiza-o

--[[ 
 ROBLOX PERFORMANCE OPTIMIZER
 Criado para melhorar FPS e reduzir lag
 Use como LocalScript
]]

local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer

--------------------------------------------------
-- CONFIGURAÇÕES (ative ou desative)
--------------------------------------------------

local Settings = {
    OptimizeFPS = true,
    OptimizeTextures = true,
    DisableParticles = true,
    DisableShadows = true,
    ReduceLighting = true,
    RemovePostProcessing = true,
    OptimizePhysics = true
}

--------------------------------------------------
-- FUNÇÕES
--------------------------------------------------

-- 🔹 Otimização geral de FPS
local function OptimizeFPS()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end

-- 🔹 Reduz texturas e materiais
local function OptimizeTextures()
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") then
            obj.Material = Enum.Material.Plastic
            obj.Reflectance = 0
        elseif obj:IsA("Decal") or obj:IsA("Texture") then
            obj.Transparency = 0.5
        end
    end
end

-- 🔹 Remove partículas e efeitos visuais pesados
local function DisableParticles()
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("ParticleEmitter")
        or obj:IsA("Trail")
        or obj:IsA("Smoke")
        or obj:IsA("Fire") then
            obj.Enabled = false
        end
    end
end

-- 🔹 Desativa sombras
local function DisableShadows()
    Lighting.GlobalShadows = false
end

-- 🔹 Simplifica iluminação
local function ReduceLighting()
    Lighting.Brightness = 1
    Lighting.FogEnd = 9e9
    Lighting.EnvironmentDiffuseScale = 0
    Lighting.EnvironmentSpecularScale = 0
end

-- 🔹 Remove efeitos de pós-processamento
local function RemovePostProcessing()
    for _, effect in pairs(Lighting:GetChildren()) do
        if effect:IsA("BloomEffect")
        or effect:IsA("BlurEffect")
        or effect:IsA("SunRaysEffect")
        or effect:IsA("ColorCorrectionEffect")
        or effect:IsA("DepthOfFieldEffect") then
            effect.Enabled = false
        end
    end
end

-- 🔹 Otimiza física (menos cálculos)
local function OptimizePhysics()
    sethiddenproperty(workspace, "InterpolationThrottling", Enum.InterpolationThrottlingMode.Enabled)
end

--------------------------------------------------
-- EXECUÇÃO
--------------------------------------------------

if Settings.OptimizeFPS then OptimizeFPS() end
if Settings.OptimizeTextures then OptimizeTextures() end
if Settings.DisableParticles then DisableParticles() end
if Settings.DisableShadows then DisableShadows() end
if Settings.ReduceLighting then ReduceLighting() end
if Settings.RemovePostProcessing then RemovePostProcessing() end
if Settings.OptimizePhysics then OptimizePhysics() end

print("✅ Otimização ativada com sucesso!")
