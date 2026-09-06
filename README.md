--// Roblox Adjustable Speed Script (1 - 70)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
--// ป้องกันการสร้าง UI ซ้ำซ้อน
if game.CoreGui:FindFirstChild("SpeedScriptUI") then
game.CoreGui.SpeedScriptUI:Destroy()
end
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SpeedScriptUI"
ScreenGui.Parent = game.CoreGui
ScreenGui.ResetOnSpawn = false
--// ปุ่มเปิด/ปิดเมนูรูปภาพ
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
--// หน้าต่างเมนูหลัก
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BorderColor3 = Color3.fromRGB(50, 50, 50)
MainFrame.Position = UDim2.new(0.5, -110, 0.5, -95)
MainFrame.Size = UDim2.new(0, 220, 0, 190)
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
TitleLabel.Text = "SPEED CONTROL (1-70)"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.TextSize = 12
local UICornerTitle = Instance.new("UICorner")
UICornerTitle.CornerRadius = UDim.new(0, 10)
UICornerTitle.Parent = TitleLabel
ToggleButton.MouseButton1Click:Connect(function()
MainFrame.Visible = not MainFrame.Visible
end)
--// ตัวแปรการทำงานความเร็ว
local currentSpeed = 16
local speedEnabled = false
-- ปุ่มเปิด/ปิด Speed Hack
local ToggleSpeedBtn = Instance.new("TextButton")
ToggleSpeedBtn.Parent = MainFrame
ToggleSpeedBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
ToggleSpeedBtn.Position = UDim2.new(0, 15, 0, 48)
ToggleSpeedBtn.Size = UDim2.new(0, 190, 0, 35)
ToggleSpeedBtn.Font = Enum.Font.GothamMedium
ToggleSpeedBtn.Text = "Speed Hack: OFF"
ToggleSpeedBtn.TextColor3 = Color3.fromRGB(255, 100, 100)
ToggleSpeedBtn.TextSize = 13
local cornerBtn = Instance.new("UICorner")
cornerBtn.CornerRadius = UDim.new(0, 6)
cornerBtn.Parent = ToggleSpeedBtn
ToggleSpeedBtn.MouseButton1Click:Connect(function()
speedEnabled = not speedEnabled
if speedEnabled then
ToggleSpeedBtn.Text = "Speed Hack: ON"
ToggleSpeedBtn.TextColor3 = Color3.fromRGB(100, 255, 100)
else
ToggleSpeedBtn.Text = "Speed Hack: OFF"
ToggleSpeedBtn.TextColor3 = Color3.fromRGB(255, 100, 100)
if LocalPlayer.Character then
local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
if humanoid then humanoid.WalkSpeed = 16 end
end
end
end)
-- ป้ายแสดงค่าความเร็วปัจจุบัน
local SpeedDisplayLabel = Instance.new("TextLabel")
SpeedDisplayLabel.Parent = MainFrame
SpeedDisplayLabel.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
SpeedDisplayLabel.Position = UDim2.new(0, 15, 0, 93)
SpeedDisplayLabel.Size = UDim2.new(0, 190, 0, 28)
SpeedDisplayLabel.Font = Enum.Font.Gotham
SpeedDisplayLabel.Text = "Speed Value: 16"
SpeedDisplayLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
SpeedDisplayLabel.TextSize = 12
local cornerDisplay = Instance.new("UICorner")
cornerDisplay.CornerRadius = UDim.new(0, 6)
cornerDisplay.Parent = SpeedDisplayLabel
-- ปุ่มเพิ่ม/ลดความเร็ว (-5 และ +5)
local decreaseBtn = Instance.new("TextButton")
decreaseBtn.Parent = MainFrame
decreaseBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
decreaseBtn.Position = UDim2.new(0, 15, 0, 133)
decreaseBtn.Size = UDim2.new(0, 90, 0, 35)
decreaseBtn.Font = Enum.Font.GothamBold
decreaseBtn.Text = "- (ลด 5)"
decreaseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
decreaseBtn.TextSize = 12
local cornerDec = Instance.new("UICorner")
cornerDec.CornerRadius = UDim.new(0, 6)
cornerDec.Parent = decreaseBtn
local increaseBtn = Instance.new("TextButton")
increaseBtn.Parent = MainFrame
increaseBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
increaseBtn.Position = UDim2.new(0, 115, 0, 133)
increaseBtn.Size = UDim2.new(0, 90, 0, 35)
increaseBtn.Font = Enum.Font.GothamBold
increaseBtn.Text = "+ (เพิ่ม 5)"
increaseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
increaseBtn.TextSize = 12
local cornerInc = Instance.new("UICorner")
cornerInc.CornerRadius = UDim.new(0, 6)
cornerInc.Parent = increaseBtn
decreaseBtn.MouseButton1Click:Connect(function()
currentSpeed = math.clamp(currentSpeed - 5, 1, 70)
SpeedDisplayLabel.Text = "Speed Value: " .. currentSpeed
end)
increaseBtn.MouseButton1Click:Connect(function()
currentSpeed = math.clamp(currentSpeed + 5, 1, 70)
SpeedDisplayLabel.Text = "Speed Value: " .. currentSpeed
end)
-- ลูปอัปเดตความเร็วตัวละครตลอดเวลา
RunService.RenderStepped:Connect(function()
if speedEnabled and LocalPlayer.Character then
local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
if humanoid then
humanoid.WalkSpeed = currentSpeed
end
end
end)
print("Adjustable Speed Script Loaded Successfully!")
