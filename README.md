-- 🧠 Tác giả: Hào sữa🇻🇳 | Bay tự do theo hướng camera (PC & Mobile + UI Icon)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local player = Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")

-- Biến
local flying = false
local speed = 50
local bodyGyro, bodyVel
local isMobile = UIS.TouchEnabled and not UIS.KeyboardEnabled

-- GUI
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "FreeFlyMenu"
gui.ResetOnSpawn = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 260, 0, 210)
frame.Position = UDim2.new(0, 20, 0.35, 0)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.Active = true
frame.Draggable = true
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)

local header = Instance.new("TextLabel", frame)
header.Size = UDim2.new(1, 0, 0, 30)
header.Text = "✈️ Free-Fly Mod - Hào sữa🇻🇳"
header.BackgroundTransparency = 1
header.Font = Enum.Font.GothamBold
header.TextColor3 = Color3.fromRGB(0, 255, 255)
header.TextScaled = true

local speedLabel = Instance.new("TextLabel", frame)
speedLabel.Size = UDim2.new(1, -20, 0, 30)
speedLabel.Position = UDim2.new(0, 10, 0, 35)
speedLabel.Text = "🚀 Tốc độ: " .. speed
speedLabel.BackgroundTransparency = 1
speedLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
speedLabel.Font = Enum.Font.Gotham
speedLabel.TextScaled = true

local function createButton(txt, yPos, callback)
	local btn = Instance.new("TextButton", frame)
	btn.Size = UDim2.new(1, -20, 0, 40)
	btn.Position = UDim2.new(0, 10, 0, yPos)
	btn.Text = txt
	btn.Font = Enum.Font.SourceSansBold
	btn.TextScaled = true
	btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
	btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	btn.MouseButton1Click:Connect(callback)
	Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
	return btn
end

-- Chế độ bay tự do
local function startFlying()
	bodyGyro = Instance.new("BodyGyro", hrp)
	bodyGyro.P = 9e4
	bodyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
	bodyGyro.CFrame = hrp.CFrame

	bodyVel = Instance.new("BodyVelocity", hrp)
	bodyVel.MaxForce = Vector3.new(9e9, 9e9, 9e9)

	local conn
	conn = RunService.RenderStepped:Connect(function()
		if not flying then conn:Disconnect() return end

		local cam = workspace.CurrentCamera
		local lookVector = cam.CFrame.LookVector

		bodyGyro.CFrame = cam.CFrame
		bodyVel.Velocity = lookVector * speed
	end)
end

local function stopFlying()
	if bodyGyro then bodyGyro:Destroy() end
	if bodyVel then bodyVel:Destroy() end
end

-- Các nút chức năng
createButton("🛫 Bật/Tắt Bay", 80, function()
	flying = not flying
	if flying then startFlying() else stopFlying() end
end)

createButton("➕ Tăng Tốc", 130, function()
	speed += 5
	speedLabel.Text = "🚀 Tốc độ: " .. speed
end)

createButton("➖ Giảm Tốc", 180, function()
	speed = math.max(5, speed - 5)
	speedLabel.Text = "🚀 Tốc độ: " .. speed
end)

-- 🎯 Icon toggle menu (kéo được + ghi nhớ vị trí)
local toggleButton = Instance.new("ImageButton", gui)
toggleButton.Size = UDim2.new(0, 50, 0, 50)
toggleButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
toggleButton.BackgroundTransparency = 0.2
toggleButton.Image = "rbxassetid://6035047377"
toggleButton.ZIndex = 10
toggleButton.Active = true
toggleButton.Draggable = true
Instance.new("UICorner", toggleButton).CornerRadius = UDim.new(1, 0)

-- Ghi nhớ vị trí toggleButton
local savedPos = player:WaitForChild("PlayerGui"):FindFirstChild("TogglePosValue")
if not savedPos then
	savedPos = Instance.new("Vector2Value")
	savedPos.Name = "TogglePosValue"
	savedPos.Parent = player:WaitForChild("PlayerGui")
	savedPos.Value = Vector2.new(0.9, 0.9) -- mặc định
end
toggleButton.Position = UDim2.new(0, savedPos.Value.X, 0, savedPos.Value.Y)

toggleButton:GetPropertyChangedSignal("Position"):Connect(function()
	local pos = toggleButton.Position
	savedPos.Value = Vector2.new(pos.X.Offset, pos.Y.Offset)
end)

-- Toggle hiển thị menu
local menuVisible = true
toggleButton.MouseButton1Click:Connect(function()
	menuVisible = not menuVisible
	frame.Visible = menuVisible
end)

