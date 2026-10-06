# Scripts-andrres
Una pagina web que muestra scripts para delta creados por mi 
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

local MASTER_KEY = "2012410"

--==================================================
-- CONFIGURACIÓN DEL AIMBOT
--==================================================

local Config = {
	Enabled = false,
	TargetPart = "Head",
	TargetEnemiesOnly = false,
	CircleSize = 110,
	MaxDistance = 500,
	AimSpeed = 1,
	OnlyInsideCircle = true,
	KeepTarget = true
}

local CurrentTarget = nil

--==================================================
-- GUI
--==================================================

local OldGui = PlayerGui:FindFirstChild("AdaptacionGUI")
if OldGui then
	OldGui:Destroy()
end

local Gui = Instance.new("ScreenGui")
Gui.Name = "AdaptacionGUI"
Gui.ResetOnSpawn = false
Gui.IgnoreGuiInset = true
Gui.Parent = PlayerGui

local function Corner(obj, radius)
	local c = Instance.new("UICorner")
	c.CornerRadius = UDim.new(0, radius)
	c.Parent = obj
end

local function Stroke(obj, color, thickness)
	local s = Instance.new("UIStroke")
	s.Color = color
	s.Thickness = thickness
	s.Parent = obj
end

local function Label(parent, text, size, pos, textSize)
	local x = Instance.new("TextLabel")
	x.BackgroundTransparency = 1
	x.Size = size
	x.Position = pos
	x.Text = text
	x.TextColor3 = Color3.fromRGB(235, 240, 255)
	x.TextSize = textSize
	x.Font = Enum.Font.GothamBold
	x.Parent = parent
	return x
end

--==================================================
-- KEY
--==================================================

local KeyFrame = Instance.new("Frame")
KeyFrame.Size = UDim2.fromOffset(370, 235)
KeyFrame.Position = UDim2.fromScale(0.5, 0.5)
KeyFrame.AnchorPoint = Vector2.new(0.5, 0.5)
KeyFrame.BackgroundColor3 = Color3.fromRGB(11, 15, 27)
KeyFrame.Parent = Gui
Corner(KeyFrame, 16)
Stroke(KeyFrame, Color3.fromRGB(45, 110, 255), 2)

local KeyTitle = Label(
	KeyFrame,
	"♛  ADAPTACIÓN",
	UDim2.new(1, -20, 0, 42),
	UDim2.fromOffset(10, 12),
	25
)

KeyTitle.TextColor3 = Color3.fromRGB(70, 140, 255)

local KeyInfo = Label(
	KeyFrame,
	"Introduce tu key para continuar",
	UDim2.new(1, -30, 0, 25),
	UDim2.fromOffset(15, 55),
	14
)

KeyInfo.TextColor3 = Color3.fromRGB(155, 165, 185)

local KeyBox = Instance.new("TextBox")
KeyBox.Size = UDim2.new(1, -40, 0, 45)
KeyBox.Position = UDim2.fromOffset(20, 90)
KeyBox.BackgroundColor3 = Color3.fromRGB(21, 27, 42)
KeyBox.PlaceholderText = "KEY"
KeyBox.PlaceholderColor3 = Color3.fromRGB(95, 105, 125)
KeyBox.TextColor3 = Color3.new(1, 1, 1)
KeyBox.TextSize = 17
KeyBox.Font = Enum.Font.GothamBold
KeyBox.ClearTextOnFocus = false
KeyBox.Parent = KeyFrame
Corner(KeyBox, 10)
Stroke(KeyBox, Color3.fromRGB(45, 65, 100), 1)

local Activate = Instance.new("TextButton")
Activate.Size = UDim2.new(1, -40, 0, 42)
Activate.Position = UDim2.fromOffset(20, 145)
Activate.BackgroundColor3 = Color3.fromRGB(35, 105, 255)
Activate.Text = "ACTIVAR"
Activate.TextColor3 = Color3.new(1, 1, 1)
Activate.TextSize = 15
Activate.Font = Enum.Font.GothamBold
Activate.Parent = KeyFrame
Corner(Activate, 10)

local KeyStatus = Label(
	KeyFrame,
	"",
	UDim2.new(1, -30, 0, 25),
	UDim2.fromOffset(15, 198),
	12
)

--==================================================
-- MENÚ
--==================================================

local Main = Instance.new("Frame")
Main.Size = UDim2.fromOffset(430, 455)
Main.Position = UDim2.fromScale(0.5, 0.5)
Main.AnchorPoint = Vector2.new(0.5, 0.5)
Main.BackgroundColor3 = Color3.fromRGB(10, 14, 25)
Main.Visible = false
Main.Parent = Gui
Corner(Main, 18)
Stroke(Main, Color3.fromRGB(45, 110, 255), 2)

local Header = Instance.new("Frame")
Header.Size = UDim2.new(1, 0, 0, 65)
Header.BackgroundColor3 = Color3.fromRGB(17, 25, 44)
Header.Parent = Main
Corner(Header, 18)

local HeaderFix = Instance.new("Frame")
HeaderFix.Size = UDim2.new(1, 0, 0, 20)
HeaderFix.Position = UDim2.new(0, 0, 1, -20)
HeaderFix.BackgroundColor3 = Color3.fromRGB(17, 25, 44)
Header.BorderSizePixel = 0
HeaderFix.Parent = Header

local Crown = Label(
	Header,
	"♛",
	UDim2.fromOffset(52, 55),
	UDim2.fromOffset(8, 5),
	37
)

Crown.TextColor3 = Color3.fromRGB(65, 135, 255)

local Title = Label(
	Header,
	"ADAPTACIÓN",
	UDim2.new(1, -125, 0, 28),
	UDim2.fromOffset(65, 8),
	21
)

Title.TextXAlignment = Enum.TextXAlignment.Left

local Subtitle = Label(
	Header,
	"AIM CONFIGURATION",
	UDim2.new(1, -125, 0, 20),
	UDim2.fromOffset(66, 36),
	10
)

Subtitle.TextColor3 = Color3.fromRGB(110, 150, 230)
Subtitle.TextXAlignment = Enum.TextXAlignment.Left

local Close = Instance.new("TextButton")
Close.Size = UDim2.fromOffset(40, 40)
Close.Position = UDim2.new(1, -52, 0, 12)
Close.BackgroundColor3 = Color3.fromRGB(35, 43, 60)
Close.Text = "×"
Close.TextColor3 = Color3.new(1, 1, 1)
Close.TextSize = 27
Close.Font = Enum.Font.GothamBold
Close.Parent = Header
Corner(Close, 10)

--==================================================
-- DRAG
--==================================================

local dragging = false
local dragStart
local startPosition

Header.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		dragging = true
		dragStart = input.Position
		startPosition = Main.Position
	end
end)

Header.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then
		dragging = false
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if not dragging then return end

	if input.UserInputType == Enum.UserInputType.MouseMovement
		or input.UserInputType == Enum.UserInputType.Touch then

		local delta = input.Position - dragStart

		Main.Position = UDim2.new(
			startPosition.X.Scale,
			startPosition.X.Offset + delta.X,
			startPosition.Y.Scale,
			startPosition.Y.Offset + delta.Y
		)
	end
end)

--==================================================
-- AIM TOGGLE
--==================================================

local AimBox = Instance.new("Frame")
AimBox.Size = UDim2.new(1, -40, 0, 70)
AimBox.Position = UDim2.fromOffset(20, 82)
AimBox.BackgroundColor3 = Color3.fromRGB(18, 25, 40)
AimBox.Parent = Main
Corner(AimBox, 12)

local AimName = Label(
	AimBox,
	"ADAPTACIÓN",
	UDim2.new(1, -100, 0, 27),
	UDim2.fromOffset(15, 9),
	16
)

AimName.TextXAlignment = Enum.TextXAlignment.Left

local AimDesc = Label(
	AimBox,
	"Activar asistencia de puntería",
	UDim2.new(1, -100, 0, 20),
	UDim2.fromOffset(15, 37),
	11
)

AimDesc.TextColor3 = Color3.fromRGB(125, 140, 165)
AimDesc.TextXAlignment = Enum.TextXAlignment.Left

local AimToggle = Instance.new("TextButton")
AimToggle.Size = UDim2.fromOffset(54, 30)
AimToggle.Position = UDim2.new(1, -70, 0.5, 0)
AimToggle.AnchorPoint = Vector2.new(0, 0.5)
AimToggle.BackgroundColor3 = Color3.fromRGB(55, 62, 78)
AimToggle.Text = ""
AimToggle.Parent = AimBox
Corner(AimToggle, 20)

local ToggleKnob = Instance.new("Frame")
ToggleKnob.Size = UDim2.fromOffset(24, 24)
ToggleKnob.Position = UDim2.fromOffset(3, 3)
ToggleKnob.BackgroundColor3 = Color3.fromRGB(235, 240, 250)
ToggleKnob.Parent = AimToggle
Corner(ToggleKnob, 20)

local function UpdateToggle()
	if Config.Enabled then
		AimToggle.BackgroundColor3 = Color3.fromRGB(35, 110, 255)
		ToggleKnob.Position = UDim2.fromOffset(27, 3)
	else
		AimToggle.BackgroundColor3 = Color3.fromRGB(55, 62, 78)
		ToggleKnob.Position = UDim2.fromOffset(3, 3)
	end
end

AimToggle.Activated:Connect(function()
	Config.Enabled = not Config.Enabled

	if not Config.Enabled then
		CurrentTarget = nil
	end

	UpdateToggle()
end)

--==================================================
-- CÍRCULO
--==================================================

local Circle = Instance.new("Frame")
Circle.Size = UDim2.fromOffset(Config.CircleSize, Config.CircleSize)
Circle.AnchorPoint = Vector2.new(0.5, 0.5)
Circle.Position = UDim2.fromScale(0.5, 0.5)
Circle.BackgroundTransparency = 1
Circle.Visible = false
Circle.Parent = Gui
Corner(Circle, 100)

local CircleStroke = Instance.new("UIStroke")
CircleStroke.Color = Color3.fromRGB(60, 140, 255)
CircleStroke.Thickness = 2
CircleStroke.Parent = Circle

--==================================================
-- CONTROLES
--==================================================

local function CreateOption(text, y)
	local button = Instance.new("TextButton")
	button.Size = UDim2.new(1, -40, 0, 45)
	button.Position = UDim2.fromOffset(20, y)
	button.BackgroundColor3 = Color3.fromRGB(17, 23, 37)
	button.Text = ""
	button.Parent = Main
	Corner(button, 10)

	local name = Label(
		button,
		text,
		UDim2.new(0.55, 0, 1, 0),
		UDim2.fromOffset(14, 0),
		13
	)

	name.TextXAlignment = Enum.TextXAlignment.Left

	local value = Label(
		button,
		"",
		UDim2.new(0.4, -10, 1, 0),
		UDim2.new(0.6, 0, 0, 0),
		13
	)

	value.TextColor3 = Color3.fromRGB(75, 145, 255)
	value.TextXAlignment = Enum.TextXAlignment.Right

	return button, value
end

local TargetButton, TargetValue = CreateOption("Parte objetivo", 165)
local EnemyButton, EnemyValue = CreateOption("Objetivo", 215)
local CircleButton, CircleValue = CreateOption("Tamaño del círculo", 265)
local SpeedButton, SpeedValue = CreateOption("Velocidad", 315)
local DistanceButton, DistanceValue = CreateOption("Distancia máxima", 365)

TargetValue.Text = "CABEZA"
EnemyValue.Text = "TODOS"
CircleValue.Text = "110"
SpeedValue.Text = "INSTANTÁNEO"
DistanceValue.Text = "500"

TargetButton.Activated:Connect(function()
	if Config.TargetPart == "Head" then
		Config.TargetPart = "HumanoidRootPart"
		TargetValue.Text = "TORSO"
	else
		Config.TargetPart = "Head"
		TargetValue.Text = "CABEZA"
	end
end)

EnemyButton.Activated:Connect(function()
	Config.TargetEnemiesOnly = not Config.TargetEnemiesOnly

	if Config.TargetEnemiesOnly then
		EnemyValue.Text = "ENEMIGOS"
	else
		EnemyValue.Text = "TODOS"
	end

	CurrentTarget = nil
end)

CircleButton.Activated:Connect(function()
	local sizes = {70, 90, 110, 140, 180}
	local current = Config.CircleSize
	local nextSize = sizes[1]

	for i, size in ipairs(sizes) do
		if size == current then
			nextSize = sizes[i + 1] or sizes[1]
			break
		end
	end

	Config.CircleSize = nextSize
	Circle.Size = UDim2.fromOffset(nextSize, nextSize)
	CircleValue.Text = tostring(nextSize)
end)

SpeedButton.Activated:Connect(function()
	if Config.AimSpeed == 1 then
		Config.AimSpeed = 0.35
		SpeedValue.Text = "SUAVE"
	elseif Config.AimSpeed == 0.35 then
		Config.AimSpeed = 0.65
		SpeedValue.Text = "MEDIO"
	else
		Config.AimSpeed = 1
		SpeedValue.Text = "INSTANTÁNEO"
	end
end)

DistanceButton.Activated:Connect(function()
	local distances = {100, 250, 500, 750, 1000}
	local current = Config.MaxDistance
	local nextDistance = distances[1]

	for i, distance in ipairs(distances) do
		if distance == current then
			nextDistance = distances[i + 1] or distances[1]
			break
		end
	end

	Config.MaxDistance = nextDistance
	DistanceValue.Text = tostring(nextDistance)
	CurrentTarget = nil
end)

--==================================================
-- CÍRCULO / TARGET
--==================================================

local function IsValidCharacter(character)
	if not character then
		return false
	end

	local humanoid = character:FindFirstChildOfClass("Humanoid")
	if not humanoid or humanoid.Health <= 0 then
		return false
	end

	return true
end

local function GetTargetPart(character)
	local part = character:FindFirstChild(Config.TargetPart)

	if part then
		return part
	end

	return character:FindFirstChild("Head")
		or character:FindFirstChild("HumanoidRootPart")
end

local function FindTarget()
	local Camera = workspace.CurrentCamera
	if not Camera then
		return nil
	end

	local center = Camera.ViewportSize / 2
	local closest = nil
	local closestDistance = math.huge

	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer then

			if Config.TargetEnemiesOnly then
				if LocalPlayer.Team ~= nil
					and player.Team ~= nil
					and LocalPlayer.Team == player.Team then
					continue
				end
			end

			local character = player.Character

			if IsValidCharacter(character) then
				local targetPart = GetTargetPart(character)

				if targetPart then
					local worldDistance =
						(Camera.CFrame.Position - targetPart.Position).Magnitude

					if worldDistance <= Config.MaxDistance then
						local screenPosition, visible =
							Camera:WorldToViewportPoint(targetPart.Position)

						if visible and screenPosition.Z > 0 then
							local screenDistance =
								(Vector2.new(screenPosition.X, screenPosition.Y) - center).Magnitude

							if not Config.OnlyInsideCircle
								or screenDistance <= Config.CircleSize / 2 then

								if screenDistance < closestDistance then
									closestDistance = screenDistance
									closest = targetPart
								end
							end
						end
					end
				end
			end
		end
	end

	return closest
end

--==================================================
-- BOTÓN DE CORONA
--==================================================

local CrownButton = Instance.new("TextButton")
CrownButton.Size = UDim2.fromOffset(60, 60)
CrownButton.Position = UDim2.new(0, 20, 0.5, 0)
CrownButton.AnchorPoint = Vector2.new(0, 0.5)
CrownButton.BackgroundColor3 = Color3.fromRGB(25, 85, 190)
CrownButton.Text = "♛"
CrownButton.TextColor3 = Color3.new(1, 1, 1)
CrownButton.TextSize = 36
CrownButton.Font = Enum.Font.GothamBold
CrownButton.Visible = false
CrownButton.Parent = Gui
Corner(CrownButton, 18)
Stroke(CrownButton, Color3.fromRGB(75, 150, 255), 2)

Close.Activated:Connect(function()
	Main.Visible = false
	CrownButton.Visible = true
	Circle.Visible = false
end)

CrownButton.Activated:Connect(function()
	Main.Visible = true
	CrownButton.Visible = false
	Circle.Visible = Config.Enabled
end)

--==================================================
-- KEY
--==================================================

local function CheckKey()
	if tostring(KeyBox.Text) == MASTER_KEY then
		KeyStatus.Text = "✓ KEY VÁLIDA"
		KeyStatus.TextColor3 = Color3.fromRGB(70, 225, 125)

		task.wait(0.25)

		KeyFrame:Destroy()
		Main.Visible = true
	else
		KeyStatus.Text = "✕ KEY INCORRECTA"
		KeyStatus.TextColor3 = Color3.fromRGB(255, 70, 80)
		KeyBox.Text = ""
	end
end

Activate.Activated:Connect(CheckKey)

KeyBox.FocusLost:Connect(function(enterPressed)
	if enterPressed then
		CheckKey()
	end
end)

--==================================================
-- AIM LOOP
--==================================================

RunService.RenderStepped:Connect(function()
	local Camera = workspace.CurrentCamera
	if not Camera then
		return
	end

	local center = Camera.ViewportSize / 2

	Circle.Position = UDim2.fromOffset(center.X, center.Y)
	Circle.Size = UDim2.fromOffset(Config.CircleSize, Config.CircleSize)

	if not Config.Enabled then
		Circle.Visible = false
		CurrentTarget = nil
		return
	end

	Circle.Visible = true

	if Config.KeepTarget and CurrentTarget then
		if not CurrentTarget.Parent then
			CurrentTarget = nil
		else
			local character = CurrentTarget.Parent
			local humanoid = character:FindFirstChildOfClass("Humanoid")

			if not humanoid or humanoid.Health <= 0 then
				CurrentTarget = nil
			end
		end
	end

	if not CurrentTarget then
		CurrentTarget = FindTarget()
	end

	if not CurrentTarget then
		return
	end

	local character = CurrentTarget.Parent
	local humanoid = character and character:FindFirstChildOfClass("Humanoid")

	if not humanoid or humanoid.Health <= 0 then
		CurrentTarget = nil
		return
	end

	local direction = CurrentTarget.Position - Camera.CFrame.Position

	if Config.AimSpeed >= 1 then
		Camera.CFrame = CFrame.lookAt(
			Camera.CFrame.Position,
			CurrentTarget.Position
		)
	else
		local targetCFrame = CFrame.lookAt(
			Camera.CFrame.Position,
			CurrentTarget.Position
		)

		Camera.CFrame = Camera.CFrame:Lerp(
			targetCFrame,
			Config.AimSpeed
		)
	end
end)
