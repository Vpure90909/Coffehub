-- Variáveis principais
local players = game:GetService("Players")
local player = players.LocalPlayer
local camera = workspace.CurrentCamera
local UserInputService = game:GetService("UserInputService")

-- Verifica se a GUI já existe para evitar criar múltiplas
if player:FindFirstChild("PlayerGui"):FindFirstChild("ESP_GUI") then
    player.PlayerGui.ESP_GUI:Destroy()
end

-- Controla se a GUI está visível
local guiVisible = false

-- Cria a GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ESP_GUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")
screenGui.Enabled = guiVisible

-- Botão para fechar
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 200, 0, 50)
closeButton.Position = UDim2.new(0.5, -100, 0.05, 0)
closeButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
closeButton.Text = "Fechar GUI"
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.TextScaled = true
closeButton.Parent = screenGui
closeButton.MouseButton1Click:Connect(function()
    guiVisible = false
    screenGui.Enabled = guiVisible
end)

-- Texto de ESP
local espLabel = Instance.new("TextLabel")
espLabel.Size = UDim2.new(0, 200, 0, 50)
espLabel.Position = UDim2.new(0.5, -100, 0.2, 0)
espLabel.BackgroundTransparency = 1
espLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
espLabel.TextScaled = true
espLabel.Text = ""
espLabel.Parent = screenGui

-- Loop do ESP (executa paralelamente)
spawn(function()
    while true do
        if guiVisible then
            for _, targetPlayer in pairs(players:GetPlayers()) do
                if targetPlayer ~= player and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    local rootPart = targetPlayer.Character.HumanoidRootPart
                    local screenPosition, onScreen = camera:WorldToScreenPoint(rootPart.Position)
                    if onScreen then
                        espLabel.Text = "Jogador: " .. targetPlayer.Name
                        espLabel.Position = UDim2.new(0, screenPosition.X, 0, screenPosition.Y)
                    else
                        espLabel.Text = ""
                    end
                end
            end
        else
            espLabel.Text = ""
        end
        wait(0.1)
    end
end)

-- Atalhos do teclado
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.H then
        guiVisible = true
        screenGui.Enabled = true
    elseif input.KeyCode == Enum.KeyCode.F then
        guiVisible = false
        screenGui.Enabled = false
    end
end)
