-- Configurações
local Settings = {
    AutoFarm = false,
    AutoBuso = false,
    AutoKen = false,
    AutoAttack = false,
    MaxDamage = false,
    SpeedHack = false,
    FlyHack = false,
    NoClip = false,
    GodMode = false,
    InfiniteEnergy = false,
    TpToIslands = false,
    AutoDefense = false,
    FarmFruits = false,
    WalkSpeed = 50,
    JumpPower = 100,
    FlySpeed = 100
}

-- Serviços
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- Funções Principais
local function AutoFarm()
    while Settings.AutoFarm and task.wait() do
        -- Lógica para farm automático
        pcall(function()
            local Enemies = Workspace:FindFirstChild("Enemies")
            if Enemies then
                for _, Enemy in pairs(Enemies:GetChildren()) do
                    if Enemy:FindFirstChild("HumanoidRootPart") then
                        LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame = Enemy.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5)
                        if Settings.AutoAttack then
                            game:GetService("VirtualInputManager"):SendKeyEvent(true, Enum.KeyCode.X, false, game)
                        end
                    end
                end
            end
        end)
    end
end

local function AutoBusoHaki()
    while Settings.AutoBuso and task.wait(1) do
        game:GetService("ReplicatedStorage").Remotes.To_Server.Handle_Initiate_S:InvokeServer({nil, nil, "Armament"})
    end
end

local function AutoKenHaki()
    while Settings.AutoKen and task.wait(1) do
        game:GetService("ReplicatedStorage").Remotes.To_Server.Handle_Initiate_S:InvokeServer({nil, nil, "Observation"})
    end
end

local function Fly()
    local BodyGyro = Instance.new("BodyGyro")
    local BodyVelocity = Instance.new("BodyVelocity")
    
    BodyGyro.Parent = LocalPlayer.Character.HumanoidRootPart
    BodyVelocity.Parent = LocalPlayer.Character.HumanoidRootPart
    
    while Settings.FlyHack and task.wait() do
        BodyGyro.CFrame = Camera.CFrame
        BodyVelocity.Velocity = Camera.CFrame.LookVector * Settings.FlySpeed
    end
    
    BodyGyro:Destroy()
    BodyVelocity:Destroy()
end

local function NoClip()
    while Settings.NoClip and task.wait() do
        pcall(function()
            for _, Part in pairs(LocalPlayer.Character:GetDescendants()) do
                if Part:IsA("BasePart") then
                    Part.CanCollide = false
                end
            end
        end)
    end
end

local function TeleportToIsland(IslandName)
    local Islands = {
        ["Shells Town"] = CFrame.new(1000, 50, 1000),
        ["Orange Town"] = CFrame.new(2000, 50, 2000),
        ["Marine Base"] = CFrame.new(3000, 50, 3000)
        -- Adicione mais ilhas conforme necessário
    }
    
    if Islands[IslandName] then
        LocalPlayer.Character.HumanoidRootPart.CFrame = Islands[IslandName]
    end
end

-- Interface do Menu
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "KingLegacyHack"
ScreenGui.Parent = game.CoreGui

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0.25, 0, 0.6, 0)
Frame.Position = UDim2.new(0.05, 0, 0.2, 0)
Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Frame.BorderSizePixel = 0
Frame.Visible = true
Frame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Text = "KING LEGACY HACK"
Title.Size = UDim2.new(1, 0, 0.1, 0)
Title.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBold
Title.Parent = Frame

-- Botões do Menu
local Options = {
    {Name = "Auto Farm", Option = "AutoFarm"},
    {Name = "Auto Buso Haki", Option = "AutoBuso"},
    {Name = "Auto Ken Haki", Option = "AutoKen"},
    {Name = "Auto Attack", Option = "AutoAttack"},
    {Name = "Max Damage", Option = "MaxDamage"},
    {Name = "Speed Hack", Option = "SpeedHack"},
    {Name = "Fly Hack", Option = "FlyHack"},
    {Name = "NoClip", Option = "NoClip"},
    {Name = "God Mode", Option = "GodMode"},
    {Name = "Infinite Energy", Option = "InfiniteEnergy"},
    {Name = "Auto Defense", Option = "AutoDefense"},
    {Name = "Farm Fruits", Option = "FarmFruits"}
}

local function ToggleOption(Option)
    Settings[Option] = not Settings[Option]
    
    if Option == "AutoFarm" then
        coroutine.wrap(AutoFarm)()
    elseif Option == "AutoBuso" then
        coroutine.wrap(AutoBusoHaki)()
    elseif Option == "AutoKen" then
        coroutine.wrap(AutoKenHaki)()
    elseif Option == "FlyHack" then
        coroutine.wrap(Fly)()
    elseif Option == "NoClip" then
        coroutine.wrap(NoClip)()
    end
end

for i, Option in pairs(Options) do
    local Button = Instance.new("TextButton")
    Button.Text = Option.Name
    Button.Size = UDim2.new(0.9, 0, 0.07, 0)
    Button.Position = UDim2.new(0.05, 0, 0.1 + (0.08 * i), 0)
    Button.BackgroundColor3 = Settings[Option.Option] and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(60, 60, 60)
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.Font = Enum.Font.Gotham
    Button.Parent = Frame
    
    Button.MouseButton1Click:Connect(function()
        ToggleOption(Option.Option)
        Button.BackgroundColor3 = Settings[Option.Option] and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(60, 60, 60)
    end)
end

-- Atualização de Stats
RunService.RenderStepped:Connect(function()
    if LocalPlayer.Character then
        local Humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if Humanoid then
            if Settings.SpeedHack then
                Humanoid.WalkSpeed = Settings.WalkSpeed
            end
            if Settings.JumpPower then
                Humanoid.JumpPower = Settings.JumpPower
            end
        end
    end
end)

print("✅ King Legacy Hack Carregado! ✅")
