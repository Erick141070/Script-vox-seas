-- Script de Auto Attack para NPCs inimigos
-- Certifique-se de rodar como LocalScript no ambiente apropriado

local Players = game:GetService("Players")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

local attackRange = 100
local autoAttackEnabled = true

local NPCFolder = workspace:WaitForChild("NPCs")
local fireCannonEvent = game.ReplicatedStorage:WaitForChild("FireCannon")

local function getPlayerRoot()
    local char = player.Character or player.CharacterAdded:Wait()
    return char:FindFirstChild("HumanoidRootPart")
end

local function findEnemies()
    local enemies = {}
    local playerRoot = getPlayerRoot()
    if not playerRoot then return enemies end

    for _, npc in pairs(NPCFolder:GetChildren()) do
        local humanoid = npc:FindFirstChild("Humanoid")
        local rootPart = npc:FindFirstChild("HumanoidRootPart")
        if humanoid and humanoid.Health > 0 and rootPart then
            local dist = (rootPart.Position - playerRoot.Position).Magnitude
            if dist <= attackRange then
                table.insert(enemies, npc)
            end
        end
    end

    return enemies
end

local function attackEnemies()
    if not autoAttackEnabled then return end

    local enemies = findEnemies()
    for _, enemyNpc in ipairs(enemies) do
        fireCannonEvent:FireServer(enemyNpc.HumanoidRootPart.Position)
        task.wait(0.2) -- Ataque mais rápido (ajuste conforme necessário)
    end
end

-- Loop contínuo
while true do
    attackEnemies()
    task.wait(1)
end
