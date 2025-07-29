-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Variáveis
local SelectedPlayer = nil
local AimbotEnabled = false
local HitboxEnabled = false
local HitboxPart = "Head"
local SpeedEnabled = false
local NoClipEnabled = false
local WalkSpeed = 16

-- UI ScreenGui
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "CustomAimbotUI"
ScreenGui.ResetOnSpawn = false

-- Frame principal
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 400, 0, 300)
MainFrame.Position = UDim2.new(0.5, -200, 0.5, -150)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

-- Minimizar botão
local MinimizeButton = Instance.new("TextButton", MainFrame)
MinimizeButton.Size = UDim2.new(0, 25, 0, 25)
MinimizeButton.Position = UDim2.new(1, -30, 0, 5)
MinimizeButton.Text = "-"
MinimizeButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
MinimizeButton.TextColor3 = Color3.new(1, 1, 1)

-- Título
local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, -35, 0, 25)
Title.Position = UDim2.new(0, 5, 0, 5)
Title.Text = "Aimbot UI"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.BackgroundTransparency = 1
Title.TextXAlignment = Enum.TextXAlignment.Left

-- Abas
local Tabs = {"Home", "Aim Assist", "Extra"}
local Buttons = {}
local Pages = {}

for i, tabName in ipairs(Tabs) do
	local button = Instance.new("TextButton", MainFrame)
	button.Size = UDim2.new(0, 120, 0, 25)
	button.Position = UDim2.new(0, 10 + (i-1)*130, 0, 35)
	button.Text = tabName
	button.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
	button.TextColor3 = Color3.new(1,1,1)

	local page = Instance.new("Frame", MainFrame)
	page.Size = UDim2.new(1, -20, 1, -70)
	page.Position = UDim2.new(0, 10, 0, 65)
	page.BackgroundTransparency = 1
	page.Visible = (i == 1)

	button.MouseButton1Click:Connect(function()
		for j, pg in pairs(Pages) do pg.Visible = false end
		page.Visible = true
	end)

	Buttons[tabName] = button
	Pages[tabName] = page
end

-- Minimizar funcionalidade
local Minimized = false
MinimizeButton.MouseButton1Click:Connect(function()
	Minimized = not Minimized
	for _, v in pairs(MainFrame:GetChildren()) do
		if v ~= MinimizeButton and v ~= Title then
			v.Visible = not Minimized
		end
	end
	MainFrame.Size = Minimized and UDim2.new(0, 200, 0, 35) or UDim2.new(0, 400, 0, 300)
end)

-------------------
-- Página Home
-------------------
local HomeText = Instance.new("TextLabel", Pages["Home"])
HomeText.Size = UDim2.new(1, 0, 0, 50)
HomeText.Text = "Script made by @bloxfruitisbetter__78624\nAimbot, Hitbox, NoClip, Walkspeed"
HomeText.TextColor3 = Color3.new(1, 1, 1)
HomeText.BackgroundTransparency = 1
HomeText.TextWrapped = true
HomeText.TextScaled = true

-------------------
-- Página Aim Assist
-------------------
local AimPage = Pages["Aim Assist"]

-- Toggle Aimbot
local AimbotToggle = Instance.new("TextButton", AimPage)
AimbotToggle.Size = UDim2.new(0, 150, 0, 25)
AimbotToggle.Position = UDim2.new(0, 0, 0, 0)
AimbotToggle.Text = "Aimbot: OFF"
AimbotToggle.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
AimbotToggle.TextColor3 = Color3.new(1,1,1)
AimbotToggle.MouseButton1Click:Connect(function()
	AimbotEnabled = not AimbotEnabled
	AimbotToggle.Text = "Aimbot: " .. (AimbotEnabled and "ON" or "OFF")
end)

-- Toggle Hitbox
local HitboxToggle = Instance.new("TextButton", AimPage)
HitboxToggle.Size = UDim2.new(0, 150, 0, 25)
HitboxToggle.Position = UDim2.new(0, 0, 0, 35)
HitboxToggle.Text = "Hitbox: OFF"
HitboxToggle.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
HitboxToggle.TextColor3 = Color3.new(1,1,1)
HitboxToggle.MouseButton1Click:Connect(function()
	HitboxEnabled = not HitboxEnabled
	HitboxToggle.Text = "Hitbox: " .. (HitboxEnabled and "ON" or "OFF")
end)

-- Dropdown da Parte do Corpo
local partOptions = {"Head", "Neck", "UpperTorso"}
local selected = 1

local PartSelector = Instance.new("TextButton", AimPage)
PartSelector.Size = UDim2.new(0, 150, 0, 25)
PartSelector.Position = UDim2.new(0, 0, 0, 70)
PartSelector.Text = "Parte: " .. partOptions[selected]
PartSelector.BackgroundColor3 = Color3.fromRGB(60,60,60)
PartSelector.TextColor3 = Color3.new(1,1,1)

PartSelector.MouseButton1Click:Connect(function()
	selected += 1
	if selected > #partOptions then selected = 1 end
	HitboxPart = partOptions[selected]
	PartSelector.Text = "Parte: " .. HitboxPart
end)

-- Dropdown de Players (mais próximo)
local PlayerButton = Instance.new("TextButton", AimPage)
PlayerButton.Size = UDim2.new(0, 200, 0, 25)
PlayerButton.Position = UDim2.new(0, 0, 0, 105)
PlayerButton.Text = "Selecionar Jogador"
PlayerButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
PlayerButton.TextColor3 = Color3.new(1,1,1)

PlayerButton.MouseButton1Click:Connect(function()
	local closest, dist = nil, math.huge
	for _, p in pairs(Players:GetPlayers()) do
		if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
			local d = (p.Character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
			if d < dist then
				dist = d
				closest = p
			end
		end
	end
	if closest then
		SelectedPlayer = closest
		PlayerButton.Text = "Alvo: " .. closest.Name
	end
end)

-------------------
-- Página Extra
-------------------
local ExtraPage = Pages["Extra"]

-- Velocidade
local SpeedBox = Instance.new("TextBox", ExtraPage)
SpeedBox.Size = UDim2.new(0, 100, 0, 25)
SpeedBox.Position = UDim2.new(0, 0, 0, 0)
SpeedBox.Text = "16"
SpeedBox.TextColor3 = Color3.new(1, 1, 1)
SpeedBox.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
SpeedBox.FocusLost:Connect(function()
	local n = tonumber(SpeedBox.Text)
	if n then WalkSpeed = math.clamp(n, 0, 50) end
end)

-- No Clip Toggle
local NoClipToggle = Instance.new("TextButton", ExtraPage)
NoClipToggle.Size = UDim2.new(0, 150, 0, 25)
NoClipToggle.Position = UDim2.new(0, 0, 0, 35)
NoClipToggle.Text = "No Clip: OFF"
NoClipToggle.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
NoClipToggle.TextColor3 = Color3.new(1, 1, 1)
NoClipToggle.MouseButton1Click:Connect(function()
	NoClipEnabled = not NoClipEnabled
	NoClipToggle.Text = "No Clip: " .. (NoClipEnabled and "ON" or "OFF")
end)

-- LOOP
RunService.RenderStepped:Connect(function()
	if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
		LocalPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = WalkSpeed
	end

	if NoClipEnabled and LocalPlayer.Character then
		for _, part in ipairs(LocalPlayer.Character:GetDescendants()) do
			if part:IsA("BasePart") then
				part.CanCollide = false
			end
		end
	end

	if AimbotEnabled and SelectedPlayer and SelectedPlayer.Character and SelectedPlayer.Character:FindFirstChild(HitboxPart) then
		local targetPart = SelectedPlayer.Character[HitboxPart]
		Camera.CFrame = CFrame.new(Camera.CFrame.Position, targetPart.Position)
	end

	if HitboxEnabled and SelectedPlayer and SelectedPlayer.Character and SelectedPlayer.Character:FindFirstChild(HitboxPart) then
		local part = SelectedPlayer.Character[HitboxPart]
		part.Size = Vector3.new(3, 3, 3)
		part.Transparency = 0.5
		part.Material = Enum.Material.Neon
	end
end)-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Variáveis
local SelectedPlayer = nil
local AimbotEnabled = false
local HitboxEnabled = false
local HitboxPart = "Head"
local SpeedEnabled = false
local NoClipEnabled = false
local WalkSpeed = 16

-- UI ScreenGui
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "CustomAimbotUI"
ScreenGui.ResetOnSpawn = false

-- Frame principal
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 400, 0, 300)
MainFrame.Position = UDim2.new(0.5, -200, 0.5, -150)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

-- Minimizar botão
local MinimizeButton = Instance.new("TextButton", MainFrame)
MinimizeButton.Size = UDim2.new(0, 25, 0, 25)
MinimizeButton.Position = UDim2.new(1, -30, 0, 5)
MinimizeButton.Text = "-"
MinimizeButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
MinimizeButton.TextColor3 = Color3.new(1, 1, 1)

-- Título
local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, -35, 0, 25)
Title.Position = UDim2.new(0, 5, 0, 5)
Title.Text = "Aimbot UI"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.BackgroundTransparency = 1
Title.TextXAlignment = Enum.TextXAlignment.Left

-- Abas
local Tabs = {"Home", "Aim Assist", "Extra"}
local Buttons = {}
local Pages = {}

for i, tabName in ipairs(Tabs) do
	local button = Instance.new("TextButton", MainFrame)
	button.Size = UDim2.new(0, 120, 0, 25)
	button.Position = UDim2.new(0, 10 + (i-1)*130, 0, 35)
	button.Text = tabName
	button.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
	button.TextColor3 = Color3.new(1,1,1)

	local page = Instance.new("Frame", MainFrame)
	page.Size = UDim2.new(1, -20, 1, -70)
	page.Position = UDim2.new(0, 10, 0, 65)
	page.BackgroundTransparency = 1
	page.Visible = (i == 1)

	button.MouseButton1Click:Connect(function()
		for j, pg in pairs(Pages) do pg.Visible = false end
		page.Visible = true
	end)

	Buttons[tabName] = button
	Pages[tabName] = page
end

-- Minimizar funcionalidade
local Minimized = false
MinimizeButton.MouseButton1Click:Connect(function()
	Minimized = not Minimized
	for _, v in pairs(MainFrame:GetChildren()) do
		if v ~= MinimizeButton and v ~= Title then
			v.Visible = not Minimized
		end
	end
	MainFrame.Size = Minimized and UDim2.new(0, 200, 0, 35) or UDim2.new(0, 400, 0, 300)
end)

-------------------
-- Página Home
-------------------
local HomeText = Instance.new("TextLabel", Pages["Home"])
HomeText.Size = UDim2.new(1, 0, 0, 50)
HomeText.Text = "Script made by @bloxfruitisbetter__78624\nAimbot, Hitbox, NoClip, Walkspeed"
HomeText.TextColor3 = Color3.new(1, 1, 1)
HomeText.BackgroundTransparency = 1
HomeText.TextWrapped = true
HomeText.TextScaled = true

-------------------
-- Página Aim Assist
-------------------
local AimPage = Pages["Aim Assist"]

-- Toggle Aimbot
local AimbotToggle = Instance.new("TextButton", AimPage)
AimbotToggle.Size = UDim2.new(0, 150, 0, 25)
AimbotToggle.Position = UDim2.new(0, 0, 0, 0)
AimbotToggle.Text = "Aimbot: OFF"
AimbotToggle.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
AimbotToggle.TextColor3 = Color3.new(1,1,1)
AimbotToggle.MouseButton1Click:Connect(function()
	AimbotEnabled = not AimbotEnabled
	AimbotToggle.Text = "Aimbot: " .. (AimbotEnabled and "ON" or "OFF")
end)

-- Toggle Hitbox
local HitboxToggle = Instance.new("TextButton", AimPage)
HitboxToggle.Size = UDim2.new(0, 150, 0, 25)
HitboxToggle.Position = UDim2.new(0, 0, 0, 35)
HitboxToggle.Text = "Hitbox: OFF"
HitboxToggle.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
HitboxToggle.TextColor3 = Color3.new(1,1,1)
HitboxToggle.MouseButton1Click:Connect(function()
	HitboxEnabled = not HitboxEnabled
	HitboxToggle.Text = "Hitbox: " .. (HitboxEnabled and "ON" or "OFF")
end)

-- Dropdown da Parte do Corpo
local partOptions = {"Head", "Neck", "UpperTorso"}
local selected = 1

local PartSelector = Instance.new("TextButton", AimPage)
PartSelector.Size = UDim2.new(0, 150, 0, 25)
PartSelector.Position = UDim2.new(0, 0, 0, 70)
PartSelector.Text = "Parte: " .. partOptions[selected]
PartSelector.BackgroundColor3 = Color3.fromRGB(60,60,60)
PartSelector.TextColor3 = Color3.new(1,1,1)

PartSelector.MouseButton1Click:Connect(function()
	selected += 1
	if selected > #partOptions then selected = 1 end
	HitboxPart = partOptions[selected]
	PartSelector.Text = "Parte: " .. HitboxPart
end)

-- Dropdown de Players (mais próximo)
local PlayerButton = Instance.new("TextButton", AimPage)
PlayerButton.Size = UDim2.new(0, 200, 0, 25)
PlayerButton.Position = UDim2.new(0, 0, 0, 105)
PlayerButton.Text = "Selecionar Jogador"
PlayerButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
PlayerButton.TextColor3 = Color3.new(1,1,1)

PlayerButton.MouseButton1Click:Connect(function()
	local closest, dist = nil, math.huge
	for _, p in pairs(Players:GetPlayers()) do
		if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
			local d = (p.Character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
			if d < dist then
				dist = d
				closest = p
			end
		end
	end
	if closest then
		SelectedPlayer = closest
		PlayerButton.Text = "Alvo: " .. closest.Name
	end
end)

-------------------
-- Página Extra
-------------------
local ExtraPage = Pages["Extra"]

-- Velocidade
local SpeedBox = Instance.new("TextBox", ExtraPage)
SpeedBox.Size = UDim2.new(0, 100, 0, 25)
SpeedBox.Position = UDim2.new(0, 0, 0, 0)
SpeedBox.Text = "16"
SpeedBox.TextColor3 = Color3.new(1, 1, 1)
SpeedBox.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
SpeedBox.FocusLost:Connect(function()
	local n = tonumber(SpeedBox.Text)
	if n then WalkSpeed = math.clamp(n, 0, 50) end
end)

-- No Clip Toggle
local NoClipToggle = Instance.new("TextButton", ExtraPage)
NoClipToggle.Size = UDim2.new(0, 150, 0, 25)
NoClipToggle.Position = UDim2.new(0, 0, 0, 35)
NoClipToggle.Text = "No Clip: OFF"
NoClipToggle.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
NoClipToggle.TextColor3 = Color3.new(1, 1, 1)
NoClipToggle.MouseButton1Click:Connect(function()
	NoClipEnabled = not NoClipEnabled
	NoClipToggle.Text = "No Clip: " .. (NoClipEnabled and "ON" or "OFF")
end)

-- LOOP
RunService.RenderStepped:Connect(function()
	if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
		LocalPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = WalkSpeed
	end

	if NoClipEnabled and LocalPlayer.Character then
		for _, part in ipairs(LocalPlayer.Character:GetDescendants()) do
			if part:IsA("BasePart") then
				part.CanCollide = false
			end
		end
	end

	if AimbotEnabled and SelectedPlayer and SelectedPlayer.Character and SelectedPlayer.Character:FindFirstChild(HitboxPart) then
		local targetPart = SelectedPlayer.Character[HitboxPart]
		Camera.CFrame = CFrame.new(Camera.CFrame.Position, targetPart.Position)
	end

	if HitboxEnabled and SelectedPlayer and SelectedPlayer.Character and SelectedPlayer.Character:FindFirstChild(HitboxPart) then
		local part = SelectedPlayer.Character[HitboxPart]
		part.Size = Vector3.new(3, 3, 3)
		part.Transparency = 0.5
		part.Material = Enum.Material.Neon
	end
end)# Script-paraEb-e-afins
