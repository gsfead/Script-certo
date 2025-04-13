--[[
    Script Completo - GS_Rails
    Funcionalidades: Fly, Kill Aura, TP, Interface Mobile
]]

-- Serviços
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Tela principal
local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
ScreenGui.Name = "GS_Rails"
ScreenGui.ResetOnSpawn = false

-- Função para criar janelas móveis
local function createDraggableFrame(name, color, position)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 230, 0, 280)
    frame.Position = position
    frame.BackgroundColor3 = color
    frame.BorderSizePixel = 0
    frame.Name = name
    frame.Active = true
    frame.Draggable = true
    frame.Parent = ScreenGui

    local title = Instance.new("TextLabel", frame)
    title.Size = UDim2.new(1, 0, 0, 30)
    title.BackgroundTransparency = 1
    title.Text = name
    title.TextColor3 = Color3.new(1, 1, 1)
    title.Font = Enum.Font.SourceSansBold
    title.TextSize = 22

    return frame
end

-- Fly
local flyFrame = createDraggableFrame("Fly", Color3.fromRGB(95, 60, 160), UDim2.new(0, 10, 0, 10))
local flying = false
local speed = 50

local toggleFlyBtn = Instance.new("TextButton", flyFrame)
toggleFlyBtn.Position = UDim2.new(0, 10, 0, 40)
toggleFlyBtn.Size = UDim2.new(0, 100, 0, 30)
toggleFlyBtn.Text = "Ativar Fly"
toggleFlyBtn.BackgroundColor3 = Color3.fromRGB(120, 90, 200)
toggleFlyBtn.TextColor3 = Color3.new(1, 1, 1)
toggleFlyBtn.MouseButton1Click:Connect(function()
    flying = not flying
    toggleFlyBtn.Text = flying and "Desativar Fly" or "Ativar Fly"
end)

local speedBox = Instance.new("TextBox", flyFrame)
speedBox.Position = UDim2.new(0, 10, 0, 80)
speedBox.Size = UDim2.new(0, 100, 0, 30)
speedBox.Text = tostring(speed)
speedBox.PlaceholderText = "Velocidade"

speedBox.FocusLost:Connect(function()
    local value = tonumber(speedBox.Text)
    if value then speed = value end
end)

-- Fly Loop
RunService.RenderStepped:Connect(function()
    if flying and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local move = Vector3.new()
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then move = move + workspace.CurrentCamera.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then move = move - workspace.CurrentCamera.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then move = move - workspace.CurrentCamera.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then move = move + workspace.CurrentCamera.CFrame.RightVector end
        LocalPlayer.Character:TranslateBy(move.Unit * speed * RunService.RenderStepped:Wait())
    end
end)

-- Kill Aura
local killAuraFrame = createDraggableFrame("Kill Aura", Color3.fromRGB(120, 40, 120), UDim2.new(0, 250, 0, 10))
local killAuraEnabled = false
local killRadius = 10

local toggleKillBtn = Instance.new("TextButton", killAuraFrame)
toggleKillBtn.Position = UDim2.new(0, 10, 0, 40)
toggleKillBtn.Size = UDim2.new(0, 100, 0, 30)
toggleKillBtn.Text = "Ativar Aura"
toggleKillBtn.BackgroundColor3 = Color3.fromRGB(150, 80, 180)
toggleKillBtn.TextColor3 = Color3.new(1, 1, 1)

toggleKillBtn.MouseButton1Click:Connect(function()
    killAuraEnabled = not killAuraEnabled
    toggleKillBtn.Text = killAuraEnabled and "Desativar Aura" or "Ativar Aura"
end)

local radiusBox = Instance.new("TextBox", killAuraFrame)
radiusBox.Position = UDim2.new(0, 10, 0, 80)
radiusBox.Size = UDim2.new(0, 100, 0, 30)
radiusBox.Text = tostring(killRadius)
radiusBox.PlaceholderText = "Raio"

radiusBox.FocusLost:Connect(function()
    local value = tonumber(radiusBox.Text)
    if value then killRadius = value end
end)

-- Kill Aura Visual
local auraVisual = Instance.new("Part")
auraVisual.Anchored = true
auraVisual.CanCollide = false
auraVisual.Shape = Enum.PartType.Ball
auraVisual.Color = Color3.fromRGB(255, 0, 255)
auraVisual.Transparency = 0.6
auraVisual.Material = Enum.Material.Neon
auraVisual.Size = Vector3.new(killRadius * 2, killRadius * 2, killRadius * 2)
auraVisual.Parent = workspace

-- Kill Aura Loop
RunService.RenderStepped:Connect(function()
    if killAuraEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        auraVisual.Position = LocalPlayer.Character.HumanoidRootPart.Position
        auraVisual.Size = Vector3.new(killRadius * 2, killRadius * 2, killRadius * 2)
        for _, v in pairs(Players:GetPlayers()) do
            if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
                local dist = (v.Character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
                if dist <= killRadius then
                    v.Character:BreakJoints()
                end
            end
        end
    else
        auraVisual.Position = Vector3.new(0, -100, 0)
    end
end)

-- TP
local tpFrame = createDraggableFrame("Teleport", Color3.fromRGB(60, 20, 90), UDim2.new(0, 10, 0, 300))
local tpBox = Instance.new("TextBox", tpFrame)
tpBox.Position = UDim2.new(0, 10, 0, 40)
tpBox.Size = UDim2.new(0, 200, 0, 30)
tpBox.PlaceholderText = "Nome do jogador ou NPC"

local tpBtn = Instance.new("TextButton", tpFrame)
tpBtn.Position = UDim2.new(0, 10, 0, 80)
tpBtn.Size = UDim2.new(0, 100, 0, 30)
tpBtn.Text = "Teleportar"
tpBtn.BackgroundColor3 = Color3.fromRGB(100, 60, 130)
tpBtn.TextColor3 = Color3.new(1, 1, 1)

tpBtn.MouseButton1Click:Connect(function()
    local name = tpBox.Text:lower()
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("Model") and v:FindFirstChild("HumanoidRootPart") and v.Name:lower():find(name) then
            LocalPlayer.Character:MoveTo(v.HumanoidRootPart.Position + Vector3.new(0, 5, 0))
            break
        end
    end
end)

-- Fim do Script
