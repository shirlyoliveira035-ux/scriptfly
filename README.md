local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local PlayerGui = Players.LocalPlayer:WaitForChild("PlayerGui")
local Player = Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
local Humanoid = Character:WaitForChild("Humanoid")

-- Remove tela antiga se existir
local TelaAntiga = PlayerGui:FindFirstChild("PainelVoador")
if TelaAntiga then TelaAntiga:Destroy() end

-- CRIA A TELA
local Tela = Instance.new("ScreenGui")
Tela.Name = "PainelVoador"
Tela.Parent = PlayerGui
Tela.ResetOnSpawn = false

local Quadro = Instance.new("Frame")
Quadro.Size = UDim2.new(0, 300, 0, 100)
Quadro.Position = UDim2.new(0.02, 0, 0.85, 0)
Quadro.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Quadro.BorderSizePixel = 2
Quadro.BorderColor3 = Color3.fromRGB(255, 255, 255)
Quadro.Active = true
Quadro.Parent = Tela

-- Barra de arrastar
local BarraTitulo = Instance.new("TextLabel")
BarraTitulo.Size = UDim2.new(1, 0, 0, 25)
BarraTitulo.Position = UDim2.new(0, 0, 0, 0)
BarraTitulo.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
BarraTitulo.Text = "✈️ Painel Voador (arraste aqui)"
BarraTitulo.TextColor3 = Color3.fromRGB(255, 255, 255)
BarraTitulo.Font = Enum.Font.GothamBold
BarraTitulo.TextScaled = true
BarraTitulo.Parent = Quadro

-- BOTÃO +
local BotaoMais = Instance.new("TextButton")
BotaoMais.Size = UDim2.new(0, 60, 0, 50)
BotaoMais.Position = UDim2.new(0, 10, 0, 35)
BotaoMais.BackgroundColor3 = Color3.fromRGB(0, 180, 0)
BotaoMais.Text = "+"
BotaoMais.TextScaled = true
BotaoMais.Font = Enum.Font.GothamBold
BotaoMais.Parent = Quadro

-- NÚMERO
local Numero = Instance.new("TextLabel")
Numero.Size = UDim2.new(0, 60, 0, 50)
Numero.Position = UDim2.new(0, 80, 0, 35)
Numero.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
Numero.Text = "1"
Numero.TextScaled = true
Numero.Font = Enum.Font.GothamBold
Numero.TextColor3 = Color3.fromRGB(255, 255, 255)
Numero.Parent = Quadro

-- BOTÃO -
local BotaoMenos = Instance.new("TextButton")
BotaoMenos.Size = UDim2.new(0, 60, 0, 50)
BotaoMenos.Position = UDim2.new(0, 150, 0, 35)
BotaoMenos.BackgroundColor3 = Color3.fromRGB(220, 50, 50)
BotaoMenos.Text = "-"
BotaoMenos.TextScaled = true
BotaoMenos.Font = Enum.Font.GothamBold
BotaoMenos.Parent = Quadro

-- BOTÃO VOAR
local BotaoVoar = Instance.new("TextButton")
BotaoVoar.Size = UDim2.new(0, 70, 0, 50)
BotaoVoar.Position = UDim2.new(0, 230, 0, 35)
BotaoVoar.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
BotaoVoar.Text = "VOAR"
BotaoVoar.TextScaled = true
BotaoVoar.Font = Enum.Font.GothamBold
BotaoVoar.Parent = Quadro

-- SISTEMA DE VALORES
local valorAtual = 1
local voando = false

-- BOTÃO + → só aumenta 1
BotaoMais.MouseButton1Click:Connect(function()
    valorAtual += 1
    Numero.Text = tostring(valorAtual)
end)

-- BOTÃO - → só diminui 1, não fica menor que 1
BotaoMenos.MouseButton1Click:Connect(function()
    if valorAtual > 1 then
        valorAtual -= 1
        Numero.Text = tostring(valorAtual)
    end
end)

-- BOTÃO VOAR liga/desliga
BotaoVoar.MouseButton1Click:Connect(function()
    voando = not voando
    if voando then
        BotaoVoar.BackgroundColor3 = Color3.fromRGB(0, 180, 0)
        Humanoid.PlatformStand = true
    else
        BotaoVoar.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
        Humanoid.PlatformStand = false
    end
end)

-- SISTEMA DE ARRASTAR A TELA
local arrastando = false
local inicioPos, inicioQuadro

BarraTitulo.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 
    or input.UserInputType == Enum.UserInputType.Touch then
        arrastando = true
        inicioPos = UserInputService:GetMouseLocation()
        inicioQuadro = Quadro.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if arrastando and (input.UserInputType == Enum.UserInputType.MouseMovement
    or input.UserInputType == Enum.UserInputType.Touch) then
        local atualPos = UserInputService:GetMouseLocation()
        local delta = atualPos - inicioPos
        Quadro.Position = UDim2.new(
            inicioQuadro.X.Scale,
            inicioQuadro.X.Offset + delta.X,
            inicioQuadro.Y.Scale,
            inicioQuadro.Y.Offset + delta.Y
        )
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
    or input.UserInputType == Enum.UserInputType.Touch then
        arrastando = false
    end
end)

-- SISTEMA DE VOAR
game:GetService("RunService").RenderStepped:Connect(function()
    if voando and Humanoid.Health > 0 then
        local eixoX, eixoZ, eixoY = 0, 0, 0
        
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then eixoZ = -1 end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then eixoZ = 1 end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then eixoX = -1 end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then eixoX = 1 end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then eixoY = 1 end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then eixoY = -1 end
        
        local CameraCF = workspace.CurrentCamera.CFrame
        local velocidade = valorAtual * 16
        local movimento = (CameraCF.LookVector * eixoZ + CameraCF.RightVector * eixoX + Vector3.new(0, eixoY, 0))
        
        if movimento.Magnitude > 0 then
            HumanoidRootPart.Velocity = movimento.Unit * velocidade
        else
            HumanoidRootPart.Velocity = Vector3.new(0, 0, 0)
        end
    end
end)

print("✅ Painel carregado! Arraste a barra cinza para mover a janela")
