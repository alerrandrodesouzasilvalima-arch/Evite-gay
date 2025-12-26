-- // GUI FLUTUANTE AUTO JUMP

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer

-- Cria ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AutoJumpGui"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Frame flutuante
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 160, 0, 60)
frame.Position = UDim2.new(0.5, -80, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
frame.BackgroundTransparency = 0.2
frame.BorderSizePixel = 0
frame.Active = true -- importante pra arrastar
frame.Draggable = true -- arrastar igual o da imagem
frame.Parent = screenGui

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0.4, 0)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundTransparency = 1
title.Text = "AUTO JUMP"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.Parent = frame

-- Botão
local button = Instance.new("TextButton")
button.Size = UDim2.new(0.9, 0, 0.45, 0)
button.Position = UDim2.new(0.05, 0, 0.5, 0)
button.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
button.Text = "Enable"
button.TextColor3 = Color3.fromRGB(255, 255, 255)
button.Font = Enum.Font.GothamBold
button.TextScaled = true
button.AutoButtonColor = true
button.Parent = frame

-- Lógica do pulo infinito
local autoJumpOn = false
local connection

local function getHumanoid()
    local char = player.Character or player.CharacterAdded:Wait()
    return char:FindFirstChildOfClass("Humanoid")
end

local function startAutoJump()
    if connection then connection:Disconnect() end

    connection = RunService.RenderStepped:Connect(function()
        if not autoJumpOn then return end
        local hum = getHumanoid()
        if hum and hum.Health > 0 then
            hum:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end)
end

local function stopAutoJump()
    if connection then
        connection:Disconnect()
        connection = nil
    end
end

button.MouseButton1Click:Connect(function()
    autoJumpOn = not autoJumpOn

    if autoJumpOn then
        button.Text = "Disable"
        button.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
        startAutoJump()
    else
        button.Text = "Enable"
        button.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
        stopAutoJump()
    end
end)

-- Garante que para se o personagem morrer ou resetar
player.CharacterAdded:Connect(function()
    if autoJumpOn then
        startAutoJump()
    end
end)
