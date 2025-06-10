-- Mod Menu para Brookhaven RP
-- Ativação: Pressione M para abrir/fechar o menu

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local UIS = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")

-- Configurações
local Settings = {
    MenuKey = Enum.KeyCode.M,
    MenuOpen = false,
    Options = {
        SpeedHack = {Enabled = false, Speed = 200},
        JumpPower = {Enabled = false, Power = 100},
        NoClip = {Enabled = false},
        Fly = {Enabled = false, Speed = 50},
        GodMode = {Enabled = false},
        InfiniteMoney = {Enabled = false},
        CarHack = {Enabled = false, Speed = 100}
    }
}

-- Criação da interface
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BrookhavenModMenu"
ScreenGui.Parent = CoreGui

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0.25, 0, 0.5, 0)
Frame.Position = UDim2.new(0.05, 0, 0.25, 0)
Frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Frame.BorderSizePixel = 0
Frame.Visible = false
Frame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Text = "BROOKHAVEN MOD MENU"
Title.Size = UDim2.new(1, 0, 0.1, 0)
Title.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBold
Title.Parent = Frame

-- Funções de hack
local function ApplySpeedHack()
    if Settings.Options.SpeedHack.Enabled and LocalPlayer.Character then
        local Humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if Humanoid then
            Humanoid.WalkSpeed = Settings.Options.SpeedHack.Speed
        end
    end
end

local function ApplyJumpPower()
    if Settings.Options.JumpPower.Enabled and LocalPlayer.Character then
        local Humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if Humanoid then
            Humanoid.JumpPower = Settings.Options.JumpPower.Power
        end
    end
end

local function ApplyNoClip()
    if Settings.Options.NoClip.Enabled and LocalPlayer.Character then
        for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end
end

local function ApplyFly()
    if Settings.Options.Fly.Enabled then
        -- Implementação do voo aqui
    end
end

-- Criação dos botões
local options = {
    {Name = "Speed Hack", Option = "SpeedHack"},
    {Name = "Super Jump", Option = "JumpPower"},
    {Name = "NoClip", Option = "NoClip"},
    {Name = "Fly", Option = "Fly"},
    {Name = "God Mode", Option = "GodMode"},
    {Name = "Infinite Money", Option = "InfiniteMoney"},
    {Name = "Car Hack", Option = "CarHack"}
}

local buttonHeight = 0.08
local padding = 0.01

for i, option in ipairs(options) do
    local button = Instance.new("TextButton")
    button.Name = option.Option
    button.Text = option.Name
    button.Size = UDim2.new(0.9, 0, buttonHeight, 0)
    button.Position = UDim2.new(0.05, 0, 0.1 + (buttonHeight + padding) * (i-1), 0)
    button.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.Font = Enum.Font.Gotham
    button.Parent = Frame
    
    button.MouseButton1Click:Connect(function()
        Settings.Options[option.Option].Enabled = not Settings.Options[option.Option].Enabled
        button.BackgroundColor3 = Settings.Options[option.Option].Enabled and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(60, 60, 60)
    end)
end

-- Controles
UIS.InputBegan:Connect(function(input, gameProcessed)
    if input.KeyCode == Settings.MenuKey and not gameProcessed then
        Settings.MenuOpen = not Settings.MenuOpen
        Frame.Visible = Settings.MenuOpen
    end
end)

-- Loop principal
game:GetService("RunService").RenderStepped:Connect(function()
    ApplySpeedHack()
    ApplyJumpPower()
    ApplyNoClip()
    ApplyFly()
    
    -- Atualiza as cores dos botões
    for _, option in ipairs(options) do
        local button = Frame:FindFirstChild(option.Option)
        if button then
            button.BackgroundColor3 = Settings.Options[option.Option].Enabled and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(60, 60, 60)
        end
    end
end)

print("Mod Menu para Brookhaven carregado! Pressione M para abrir o menu.")
