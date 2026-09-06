-- STREAMING_CHUNK:Initializing Roblox Services and Variables...
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local Workspace = game:GetService("Workspace")
local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera
--// ลบ UI เก่าทิ้งถ้ามีอยู่แล้วเพื่อป้องกันการซ้ำซ้อน
if game.CoreGui:FindFirstChild("CustomScriptUI") then
game.CoreGui.CustomScriptUI:Destroy()
end
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "CustomScriptUI"
ScreenGui.Parent = game.CoreGui
ScreenGui.ResetOnSpawn = false
-- STREAMING_CHUNK:Creating Main Toggle Button and Window UI...
local ToggleButton = Instance.new("ImageButton")
ToggleButton.Name = "ToggleMenuBtn"
ToggleButton.Parent = ScreenGui
ToggleButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
ToggleButton.Position = UDim2.new(0, 20, 0, 80)
ToggleButton.Size = UDim2.new(0, 55, 0, 55)
ToggleButton.Image = "rbxassetid://image.png"
local UICornerBtn = Instance.new("UICorner")
UICornerBtn.CornerRadius = UDim.new(1, 0)
UICornerBtn.Parent = ToggleButton
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BorderColor3 = Color3.fromRGB(50, 50, 50)
MainFrame.Position = UDim2.new(0.5, -125, 0.5, -160)
MainFrame.Size = UDim2.new(0, 250, 0, 345)
MainFrame.Visible = false
MainFrame.Active = true
MainFrame.Draggable = true
local UICornerFrame = Instance.new("UICorner")
UICornerFrame.CornerRadius = UDim.new(0, 10)
UICornerFrame.Parent = MainFrame
local TitleLabel = Instance.new("TextLabel")
TitleLabel.Parent = MainFrame
TitleLabel.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
TitleLabel.Size = UDim2.new(1, 0, 0, 35)
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.Text = "AIMBOT & CONTROL MENU"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.TextSize = 13
local UICornerTitle = Instance.new("UICorner")
UICornerTitle.CornerRadius = UDim.new(0, 10)
UICornerTitle.Parent = TitleLabel
ToggleButton.MouseButton1Click:Connect(function()
MainFrame.Visible = not MainFrame.Visible
end)
-- STREAMING_CHUNK:Building Menu Toggle Helper Function...
local function createMenuToggle(name, yPos, callback)
local btn = Instance.new("TextButton")
btn.Parent = MainFrame
btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
btn.Position = UDim2.new(0, 15, 0, yPos)
btn.Size = UDim2.new(0, 220, 0, 30)
btn.Font = Enum.Font.GothamMedium
btn.Text = name .. ": OFF"
btn.TextColor3 = Color3.fromRGB(255, 100, 100)
btn.TextSize = 12
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 6)
corner.Parent = btn
local state = false
btn.MouseButton1Click:Connect(function()
state = not state
if state then
btn.Text = name .. ": ON"
btn.TextColor3 = Color3.fromRGB(100, 255, 100)
else
btn.Text = name .. ": OFF"
btn.TextColor3 = Color3.fromRGB(255, 100, 100)
end
callback(state)
end)
end
-- STREAMING_CHUNK:Implementing ESP and NoClip Systems...
local espEnabled = false
createMenuToggle("ESP (Players)", 45, function(state)
espEnabled = state
end)
RunService.RenderStepped:Connect(function()
if not espEnabled then
for _, p in pairs(Players:GetPlayers()) do
if p.Character and p.Character:FindFirstChild("Highlight_ESP") then
p.Character.Highlight_ESP:Destroy()
end
end
return
end
for _, player in pairs(Players:GetPlayers()) do
if player ~= LocalPlayer and player.Character then
local char = player.Character
local hl = char:FindFirstChild("Highlight_ESP")
if not hl then
hl = Instance.new("Highlight")
hl.Name = "Highlight_ESP"
hl.Adornee = char
hl.FillColor = Color3.fromRGB(255, 0, 0)
hl.OutlineColor = Color3.fromRGB(255, 255, 255)
hl.Parent = char
end
end
end
end)
local noclipEnabled = false
createMenuToggle("NoClip", 80, function(state)
noclipEnabled = state
end)
RunService.Stepped:Connect(function()
if noclipEnabled and LocalPlayer.Character then
for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
if part:IsA("BasePart") then
part.CanCollide = false
end
end
end
end)
-- STREAMING_CHUNK:Implementing High-Precision Aimbot (Head/Body Lock)...
local aimbotEnabled = false
local aimPartName = "Head" -- ค่าเริ่มต้นล็อกหัว สามารถเปลี่ยนเป็น "HumanoidRootPart" สำหรับล็อกตัว
createMenuToggle("Aimbot (Lock Head/Body)", 115, function(state)
aimbotEnabled = state
end)
-- ฟังก์ชันสลับส่วนที่ต้องการล็อก (หัว หรือ ตัว)
local aimModeBtn = Instance.new("TextButton")
aimModeBtn.Parent = MainFrame
aimModeBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
aimModeBtn.Position = UDim2.new(0, 15, 0, 150)
aimModeBtn.Size = UDim2.new(0, 220, 0, 26)
aimModeBtn.Font = Enum.Font.Gotham
aimModeBtn.Text = "Target Part: Head (ล็อกหัว)"
aimModeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
aimModeBtn.TextSize = 11
local cornerAimMode = Instance.new("UICorner")
cornerAimMode.CornerRadius = UDim.new(0, 6)
cornerAimMode.Parent = aimModeBtn
aimModeBtn.MouseButton1Click:Connect(function()
if aimPartName == "Head" then
aimPartName = "HumanoidRootPart"
aimModeBtn.Text = "Target Part: Body (ล็อกตัว)"
aimModeBtn.TextColor3 = Color3.fromRGB(255, 200, 100)
else
aimPartName = "Head"
aimModeBtn.Text = "Target Part: Head (ล็อกหัว)"
aimModeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
end
end)
-- ค้นหาผู้เล่นที่อยู่ใกล้เป้าหมายกลางจอที่สุด
local function getClosestEnemy()
local closestPlayer = nil
local shortestDistance = math.huge
local viewportCenter = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
for _, player in pairs(Players:GetPlayers()) do
if player ~= LocalPlayer and player.Character then
local character = player.Character
local humanoid = character:FindFirstChildOfClass("Humanoid")
local targetPart = character:FindFirstChild(aimPartName)
if humanoid and humanoid.Health > 0 and targetPart then
-- ตรวจสอบทีม (กรณีเกมมีระบบทีม)
if not LocalPlayer.Team or player.Team ~= LocalPlayer.Team then
local screenPos, onScreen = Camera:WorldToViewportPoint(targetPart.Position)
if onScreen then
local distance = (Vector2.new(screenPos.X, screenPos.Y) - viewportCenter).Magnitude
if distance < shortestDistance then
shortestDistance = distance
closestPlayer = player
end
end
end
end
end
end
return closestPlayer
end
-- ล็อกกล้องไปที่เป้าหมายด้วยความแม่นยำสูง
RunService.RenderStepped:Connect(function()
if not aimbotEnabled then return end
local target = getClosestEnemy()
if target and target.Character and target.Character:FindFirstChild(aimPartName) then
local part = target.Character[aimPartName]
-- บังคับมุมมองกล้องหันไปที่เป้าหมายทันทีด้วยความลื่นไหลและแม่นยำ
Camera.CFrame = CFrame.new(Camera.CFrame.Position, part.Position)
end
end)
-- STREAMING_CHUNK:Implementing Auto-Shoot and Speed Systems...
local autoShootEnabled = false
createMenuToggle("Auto-Shoot", 181, function(state)
autoShootEnabled = state
end)
RunService.RenderStepped:Connect(function()
if not autoShootEnabled then return end
local target = getClosestEnemy()
if target and target.Character then
local viewportSize = Camera.ViewportSize
pcall(function()
VirtualInputManager:SendMouseButtonEvent(viewportSize.X / 2, viewportSize.Y / 2, 0, true, game, 1)
task.wait(0.04)
VirtualInputManager:SendMouseButtonEvent(viewportSize.X / 2, viewportSize.Y / 2, 0, false, game, 1)
end)
end
end)
local autoSpeedVal = 30
RunService.RenderStepped:Connect(function()
if LocalPlayer.Character then
local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
if humanoid then
humanoid.WalkSpeed = autoSpeedVal
end
end
end)
local SpeedLabel = Instance.new("TextLabel")
SpeedLabel.Parent = MainFrame
SpeedLabel.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
SpeedLabel.Position = UDim2.new(0, 15, 0, 221)
SpeedLabel.Size = UDim2.new(0, 220, 0, 30)
SpeedLabel.Font = Enum.Font.Gotham
SpeedLabel.Text = "Auto Speed: " .. autoSpeedVal .. " (Active)"
SpeedLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
SpeedLabel.TextSize = 12
local UICornerSpeed = Instance.new("UICorner")
UICornerSpeed.CornerRadius = UDim.new(0, 6)
UICornerSpeed.Parent = SpeedLabel
-- STREAMING_CHUNK:Adding Infinite Jump Feature...
local infJumpEnabled = false
createMenuToggle("Infinite Jump", 260, function(state)
infJumpEnabled = state
end)
UserInputService.JumpRequest:Connect(function()
if infJumpEnabled and LocalPlayer.Character then
local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
if humanoid then
humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
end
end
end)
print("Aimbot & Script Loaded Successfully with Maximum Precision!")
