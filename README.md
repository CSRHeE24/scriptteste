-- Configurações do ESP
local ESP = {
    Enabled = true,
    TeamCheck = true,        -- Mostrar apenas inimigos
    Boxes = true,           -- Mostrar caixas
    Tracers = true,         -- Mostrar linhas traçadoras
    Names = true,           -- Mostrar nomes
    HealthBars = true,      -- Mostrar barras de vida
    Distance = true,        -- Mostrar distância
    MaxDistance = 1000,     -- Distância máxima
    BoxColor = Color3.fromRGB(255, 0, 0),  -- Cor da caixa
    TracerColor = Color3.fromRGB(255, 255, 255),  -- Cor do tracer
    TextColor = Color3.fromRGB(255, 255, 255),    -- Cor do texto
    TextSize = 14           -- Tamanho do texto
}

-- Serviços necessários
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Função para verificar se um jogador é inimigo
local function IsEnemy(player)
    if not ESP.TeamCheck then return true end
    if not LocalPlayer.Team then return true end  -- Se não houver times
    return player.Team ~= LocalPlayer.Team
end

-- Função para calcular a distância entre dois pontos
local function GetDistance(from, to)
    return (from - to).Magnitude
end

-- Função para criar um ESP para um jogador
local function CreateESP(player)
    if player == LocalPlayer then return end
    
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
    local humanoid = character:WaitForChild("Humanoid")
    
    -- Criar componentes do ESP
    local espFolder = Instance.new("Folder")
    espFolder.Name = player.Name .. "_ESP"
    espFolder.Parent = player
    
    -- Caixa
    local box = Instance.new("BoxHandleAdornment")
    box.Name = "Box"
    box.Adornee = humanoidRootPart
    box.AlwaysOnTop = true
    box.ZIndex = 5
    box.Size = Vector3.new(3, 5, 1)
    box.Transparency = 0.7
    box.Color3 = ESP.BoxColor
    box.Visible = ESP.Boxes and ESP.Enabled and IsEnemy(player)
    box.Parent = espFolder
    
    -- Tracer
    local tracer = Instance.new("LineHandleAdornment")
    tracer.Name = "Tracer"
    tracer.Adornee = humanoidRootPart
    tracer.AlwaysOnTop = true
    tracer.ZIndex = 3
    tracer.Transparency = 0.7
    tracer.Color3 = ESP.TracerColor
    tracer.Visible = ESP.Tracers and ESP.Enabled and IsEnemy(player)
    tracer.Parent = espFolder
    
    -- Nome
    local nameLabel = Instance.new("BillboardGui")
    nameLabel.Name = "Name"
    nameLabel.Adornee = humanoidRootPart
    nameLabel.AlwaysOnTop = true
    nameLabel.Size = UDim2.new(0, 200, 0, 50)
    nameLabel.StudsOffset = Vector3.new(0, 3.5, 0)
    
    local nameText = Instance.new("TextLabel")
    nameText.Size = UDim2.new(1, 0, 0.5, 0)
    nameText.BackgroundTransparency = 1
    nameText.Text = player.Name
    nameText.TextColor3 = ESP.TextColor
    nameText.TextSize = ESP.TextSize
    nameText.Font = Enum.Font.SourceSansBold
    nameText.Parent = nameLabel
    
    nameLabel.Enabled = ESP.Names and ESP.Enabled and IsEnemy(player)
    nameLabel.Parent = espFolder
    
    -- Barra de vida
    local healthBar = Instance.new("BillboardGui")
    healthBar.Name = "HealthBar"
    healthBar.Adornee = humanoidRootPart
    healthBar.AlwaysOnTop = true
    healthBar.Size = UDim2.new(2, 0, 0.2, 0)
    healthBar.StudsOffset = Vector3.new(0, 2.8, 0)
    
    local healthBackground = Instance.new("Frame")
    healthBackground.Size = UDim2.new(1, 0, 1, 0)
    healthBackground.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    healthBackground.BorderSizePixel = 0
    healthBackground.Parent = healthBar
    
    local healthFill = Instance.new("Frame")
    healthFill.Size = UDim2.new(1, 0, 1, 0)
    healthFill.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
    healthFill.BorderSizePixel = 0
    healthFill.Parent = healthBackground
    
    healthBar.Enabled = ESP.HealthBars and ESP.Enabled and IsEnemy(player)
    healthBar.Parent = espFolder
    
    -- Distância
    local distanceLabel = Instance.new("BillboardGui")
    distanceLabel.Name = "Distance"
    distanceLabel.Adornee = humanoidRootPart
    distanceLabel.AlwaysOnTop = true
    distanceLabel.Size = UDim2.new(0, 200, 0, 50)
    distanceLabel.StudsOffset = Vector3.new(0, 2, 0)
    
    local distanceText = Instance.new("TextLabel")
    distanceText.Size = UDim2.new(1, 0, 0.5, 0)
    distanceText.BackgroundTransparency = 1
    distanceText.TextColor3 = ESP.TextColor
    distanceText.TextSize = ESP.TextSize - 2
    distanceText.Font = Enum.Font.SourceSans
    distanceText.Parent = distanceLabel
    
    distanceLabel.Enabled = ESP.Distance and ESP.Enabled and IsEnemy(player)
    distanceLabel.Parent = espFolder
    
    -- Atualizar ESP em cada frame
    local connection
    connection = RunService.RenderStepped:Connect(function()
        if not character or not humanoidRootPart or not humanoid or not humanoid:IsDescendantOf(workspace) then
            connection:Disconnect()
            espFolder:Destroy()
            return
        end
        
        local distance = GetDistance(LocalPlayer.Character.HumanoidRootPart.Position, humanoidRootPart.Position)
        local isEnemy = IsEnemy(player)
        local inRange = distance <= ESP.MaxDistance
        
        -- Atualizar visibilidade
        box.Visible = ESP.Boxes and ESP.Enabled and isEnemy and inRange
        tracer.Visible = ESP.Tracers and ESP.Enabled and isEnemy and inRange
        nameLabel.Enabled = ESP.Names and ESP.Enabled and isEnemy and inRange
        healthBar.Enabled = ESP.HealthBars and ESP.Enabled and isEnemy and inRange
        distanceLabel.Enabled = ESP.Distance and ESP.Enabled and isEnemy and inRange
        
        -- Atualizar posição do tracer (do jogador para o alvo)
        if tracer.Visible then
            tracer.From = Camera.CFrame.Position
            tracer.To = humanoidRootPart.Position
        end
        
        -- Atualizar barra de vida
        if healthBar.Enabled then
            local healthPercent = humanoid.Health / humanoid.MaxHealth
            healthFill.Size = UDim2.new(healthPercent, 0, 1, 0)
            
            -- Mudar cor baseada na vida
            if healthPercent > 0.5 then
                healthFill.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
            elseif healthPercent > 0.25 then
                healthFill.BackgroundColor3 = Color3.fromRGB(255, 255, 0)
            else
                healthFill.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
            end
        end
        
        -- Atualizar texto de distância
        if distanceLabel.Enabled then
            distanceText.Text = string.format("%.1fm", distance)
        end
    end)
end

-- Criar ESP para todos os jogadores existentes
for _, player in ipairs(Players:GetPlayers()) do
    if player ~= LocalPlayer then
        coroutine.wrap(CreateESP)(player)
    end
end

-- Criar ESP para novos jogadores
Players.PlayerAdded:Connect(function(player)
    if player ~= LocalPlayer then
        coroutine.wrap(CreateESP)(player)
    end
end)

-- Remover ESP quando um jogador sair
Players.PlayerRemoving:Connect(function(player)
    local espFolder = player:FindFirstChild(player.Name .. "_ESP")
    if espFolder then
        espFolder:Destroy()
    end
end)

-- Atualizar ESP quando o personagem for recarregado
LocalPlayer.CharacterAdded:Connect(function(character)
    -- Recriar ESP para todos os jogadores
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local espFolder = player:FindFirstChild(player.Name .. "_ESP")
            if espFolder then
                espFolder:Destroy()
            end
            coroutine.wrap(CreateESP)(player)
        end
    end
end)

-- Comando para ativar/desativar o ESP
local function ToggleESP()
    ESP.Enabled = not ESP.Enabled
    print("ESP " .. (ESP.Enabled and "ativado" or "desativado"))
    
    -- Atualizar todos os ESPs
    for _, player in ipairs(Players:GetPlayers()) do
        local espFolder = player:FindFirstChild(player.Name .. "_ESP")
        if espFolder then
            for _, item in ipairs(espFolder:GetChildren()) do
                if item:IsA("BoxHandleAdornment") or item:IsA("LineHandleAdornment") then
                    item.Visible = ESP.Enabled and IsEnemy(player)
                elseif item:IsA("BillboardGui") then
                    item.Enabled = ESP.Enabled and IsEnemy(player)
                end
            end
        end
    end
end

-- Atribuir comando (opcional)
game:GetService("UserInputService").InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.F1 then
        ToggleESP()
    end
end)

print("ESP carregado. Pressione F1 para ativar/desativar.")
