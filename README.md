local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local StarterGui = game:GetService("StarterGui")
local UserInputService = game:GetService("UserInputService")
local ScreenGui = Instance.new("ScreenGui")
local TextButton = Instance.new("TextButton")

-- Variável para controlar o estado do respawn instantâneo
local isRespawnEnabled = true

-- Criar interface do botão
ScreenGui.Parent = game.CoreGui
ScreenGui.ResetOnSpawn = false

TextButton.Parent = ScreenGui
TextButton.Size = UDim2.new(0, 200, 0, 50)
TextButton.Position = UDim2.new(0, 0, 0.5, -25)  -- Posicionamento no centro esquerdo
TextButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
TextButton.Text = "Desativar Respawn"

-- Função para o respawn instantâneo
local function InstantRespawn()
    local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildWhichIsA("Humanoid")
    if humanoid then
        humanoid.Died:Connect(function()
            if isRespawnEnabled then
                -- Disparar respawn imediatamente após a morte
                -- O respawn agora acontece sem delay
                ReplicatedStorage:WaitForChild("Guide"):FireServer()
                -- Aqui, podemos adicionar um pequeno delay para garantir que a ação de respawn não seja repetida de forma indesejada
                wait(0.1)
                -- Aqui, garantir que o personagem seja resetado de forma mais rápida, caso necessário
                -- Opcionalmente, você pode chamar outro método que reforce o respawn, dependendo do jogo
            end
        end)
    end
end

-- Função para alternar o estado de respawn instantâneo
local function ToggleRespawn()
    isRespawnEnabled = not isRespawnEnabled
    if isRespawnEnabled then
        TextButton.Text = "Desativar Respawn"
        StarterGui:SetCore("SendNotification", {
            Title = "Ghostly 👑",
            Text = "O respawn instantâneo foi ativado!",
            Duration = 3
        })
    else
        TextButton.Text = "Ativar Respawn"
        StarterGui:SetCore("SendNotification", {
            Title = "Ghostly 👑",
            Text = "O respawn instantâneo foi desativado.",
            Duration = 3
        })
    end
end

-- Conectar o botão à função de alternar
TextButton.MouseButton1Click:Connect(ToggleRespawn)

-- Garantir que a função de respawn seja chamada sempre que o personagem for adicionado
LocalPlayer.CharacterAdded:Connect(function()
    repeat
        local humanoid = LocalPlayer.Character:FindFirstChildWhichIsA("Humanoid")
        if humanoid then
            InstantRespawn()  -- Configurar respawn instantâneo assim que o humanoide estiver disponível
            break
        end
        wait()  -- Aguarda até o humanoide aparecer
    until false
end)

-- Se o personagem já existir no momento que o script rodar, configurar o respawn
if LocalPlayer.Character then
    InstantRespawn()
end
