-- GS_Rails - Menu Unificado

getgenv().GS_Rails_Config = getgenv().GS_Rails_Config or {}

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

local flyToggle = getgenv().GS_Rails_Config.flyToggle or false
local flySpeed = getgenv().GS_Rails_Config.flySpeed or 50
local auraToggle = getgenv().GS_Rails_Config.auraToggle or false
local auraRadius = getgenv().GS_Rails_Config.auraRadius or 10

local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "GS_Rails"
gui.ResetOnSpawn = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 300, 0, 500)
frame.Position = UDim2.new(0, 20, 0, 20)
frame.BackgroundColor3 = Color3.fromRGB(90, 40, 140)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true

local minimize = false
local miniBtn = Instance.new("TextButton", frame)
miniBtn.Size = UDim2.new(0, 30, 0, 30)
miniBtn.Position = UDim2.new(1, -35, 0, 5)
miniBtn.Text = "-"
miniBtn.BackgroundColor3 = Color3.fromRGB(120, 80, 160)
miniBtn.TextColor3 = Color3.new(1, 1, 1)
miniBtn.MouseButton1Click:Connect(function()
	minimize = not minimize
	for _, child in pairs(frame:GetChildren()) do
		if child:IsA("GuiObject") and child ~= miniBtn then
			child.Visible = not minimize
		end
	end
	miniBtn.Text = minimize and "+" or "-"
end)

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 35)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundTransparency = 1
title.Text = "GS_Rails - Menu"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 24

local layout = Instance.new("UIListLayout", frame)
layout.Padding = UDim.new(0, 8)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.VerticalAlignment = Enum.VerticalAlignment.Top

local function Spacer(height)
	local s = Instance.new("Frame")
	s.Size = UDim2.new(1, 0, 0, height)
	s.BackgroundTransparency = 1
	return s
end

-- Fly
local flyTitle = Instance.new("TextLabel")
flyTitle.Text = "Fly"
flyTitle.Size = UDim2.new(1, -20, 0, 25)
flyTitle.BackgroundTransparency = 1
flyTitle.TextColor3 = Color3.new(1, 1, 1)
flyTitle.Font = Enum.Font.SourceSansBold
flyTitle.TextSize = 20
flyTitle.TextXAlignment = Enum.TextXAlignment.Left

local flyBtn = Instance.new("TextButton")
flyBtn.Size = UDim2.new(1, -20, 0, 30)
flyBtn.Text = flyToggle and "Desativar Fly" or "Ativar Fly"
flyBtn.BackgroundColor3 = Color3.fromRGB(130, 90, 200)
flyBtn.TextColor3 = Color3.new(1, 1, 1)
flyBtn.MouseButton1Click:Connect(function()
	flyToggle = not flyToggle
	flyBtn.Text = flyToggle and "Desativar Fly" or "Ativar Fly"
end)

local speedBox = Instance.new("TextBox")
speedBox.Size = UDim2.new(1, -20, 0, 30)
speedBox.PlaceholderText = "Velocidade do Fly"
speedBox.Text = tostring(flySpeed)
speedBox.BackgroundColor3 = Color3.fromRGB(150, 110, 220)
speedBox.TextColor3 = Color3.new(1, 1, 1)
speedBox.FocusLost:Connect(function()
	local val = tonumber(speedBox.Text)
	if val then flySpeed = val end
end)

RunService.RenderStepped:Connect(function()
	if flyToggle and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
		local move = Vector3.zero
		if UserInputService:IsKeyDown(Enum.KeyCode.W) then move += workspace.CurrentCamera.CFrame.LookVector end
		if UserInputService:IsKeyDown(Enum.KeyCode.S) then move -= workspace.CurrentCamera.CFrame.LookVector end
		if UserInputService:IsKeyDown(Enum.KeyCode.A) then move -= workspace.CurrentCamera.CFrame.RightVector end
		if UserInputService:IsKeyDown(Enum.KeyCode.D) then move += workspace.CurrentCamera.CFrame.RightVector end
		LocalPlayer.Character:TranslateBy(move.Unit * flySpeed * RunService.RenderStepped:Wait())
	end
end)

-- Kill Aura
local auraPart = Instance.new("Part")
auraPart.Anchored = true
auraPart.CanCollide = false
auraPart.Shape = Enum.PartType.Ball
auraPart.Material = Enum.Material.Neon
auraPart.Color = Color3.fromRGB(255, 0, 255)
auraPart.Transparency = 0.5
auraPart.Size = Vector3.new(auraRadius * 2, auraRadius * 2, auraRadius * 2)
auraPart.Parent = workspace

local auraTitle = Instance.new("TextLabel")
auraTitle.Text = "Kill Aura"
auraTitle.Size = UDim2.new(1, -20, 0, 25)
auraTitle.BackgroundTransparency = 1
auraTitle.TextColor3 = Color3.new(1, 1, 1)
auraTitle.Font = Enum.Font.SourceSansBold
auraTitle.TextSize = 20
auraTitle.TextXAlignment = Enum.TextXAlignment.Left

local auraBtn = Instance.new("TextButton")
auraBtn.Size = UDim2.new(1, -20, 0, 30)
auraBtn.Text = auraToggle and "Desativar Kill Aura" or "Ativar Kill Aura"
auraBtn.BackgroundColor3 = Color3.fromRGB(150, 50, 200)
auraBtn.TextColor3 = Color3.new(1, 1, 1)
auraBtn.MouseButton1Click:Connect(function()
	auraToggle = not auraToggle
	auraBtn.Text = auraToggle and "Desativar Kill Aura" or "Ativar Kill Aura"
end)

local radiusBox = Instance.new("TextBox")
radiusBox.Size = UDim2.new(1, -20, 0, 30)
radiusBox.PlaceholderText = "Raio da Aura"
radiusBox.Text = tostring(auraRadius)
radiusBox.BackgroundColor3 = Color3.fromRGB(170, 80, 220)
radiusBox.TextColor3 = Color3.new(1, 1, 1)
radiusBox.FocusLost:Connect(function()
	local val = tonumber(radiusBox.Text)
	if val then auraRadius = val end
end)

RunService.RenderStepped:Connect(function()
	if auraToggle and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
		auraPart.Position = LocalPlayer.Character.HumanoidRootPart.Position
		auraPart.Size = Vector3.new(auraRadius * 2, auraRadius * 2, auraRadius * 2)
		for _, player in pairs(Players:GetPlayers()) do
			if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
				local dist = (player.Character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
				if dist <= auraRadius then
					player.Character:BreakJoints()
				end
			end
		end
	else
		auraPart.Position = Vector3.new(0, -100, 0)
	end
end)

-- Teleport
local tpTitle = Instance.new("TextLabel")
tpTitle.Text = "Teleport"
tpTitle.Size = UDim2.new(1, -20, 0, 25)
tpTitle.BackgroundTransparency = 1
tpTitle.TextColor3 = Color3.new(1, 1, 1)
tpTitle.Font = Enum.Font.SourceSansBold
tpTitle.TextSize = 20
tpTitle.TextXAlignment = Enum.TextXAlignment.Left

local tpBox = Instance.new("TextBox")
tpBox.Size = UDim2.new(1, -20, 0, 30)
tpBox.PlaceholderText = "Nome do NPC ou jogador"
tpBox.BackgroundColor3 = Color3.fromRGB(130, 70, 200)
tpBox.TextColor3 = Color3.new(1, 1, 1)

local tpBtn = Instance.new("TextButton")
tpBtn.Size = UDim2.new(1, -20, 0, 30)
tpBtn.Text = "Teleportar"
tpBtn.BackgroundColor3 = Color3.fromRGB(150, 60, 220)
tpBtn.TextColor3 = Color3.new(1, 1, 1)
tpBtn.MouseButton1Click:Connect(function()
	local name = tpBox.Text:lower()
	for _, obj in pairs(workspace:GetDescendants()) do
		if obj:IsA("Model") and obj:FindFirstChild("HumanoidRootPart") and obj.Name:lower():find(name) then
			LocalPlayer.Character:MoveTo(obj.HumanoidRootPart.Position + Vector3.new(0, 5, 0))
			break
		end
	end
end)

-- Botão de salvar configurações
local saveBtn = Instance.new("TextButton")
saveBtn.Size = UDim2.new(1, -20, 0, 35)
saveBtn.Text = "Salvar Configurações"
saveBtn.BackgroundColor3 = Color3.fromRGB(110, 40, 190)
saveBtn.TextColor3 = Color3.new(1, 1, 1)
saveBtn.MouseButton1Click:Connect(function()
	getgenv().GS_Rails_Config = {
		flyToggle = flyToggle,
		flySpeed = flySpeed,
		auraToggle = auraToggle,
		auraRadius = auraRadius
	}
end)

-- Botão Lomu Hub
local lomuBtn = Instance.new("TextButton")
lomuBtn.Size = UDim2.new(1, -20, 0, 35)
lomuBtn.Text = "Abrir Lomu Hub"
lomuBtn.BackgroundColor3 = Color3.fromRGB(100, 30, 180)
lomuBtn.TextColor3 = Color3.new(1, 1, 1)
lomuBtn.Font = Enum.Font.SourceSansBold
lomuBtn.TextSize = 18
lomuBtn.MouseButton1Click:Connect(function()
	loadstring(game:HttpGet('https://raw.githubusercontent.com/notyourfavorite1/lomuhubmain/refs/heads/main/main.txt', true))()
end)

-- Adicionar elementos ao menu
local elements = {
	Spacer(5),
	flyTitle, flyBtn, speedBox,
	Spacer(10),
	auraTitle, auraBtn, radiusBox,
	Spacer(10),
	tpTitle, tpBox, tpBtn,
	Spacer(10),
	saveBtn,
	lomuBtn
}
for _, e in pairs(elements) do
	e.Parent = frame
end
