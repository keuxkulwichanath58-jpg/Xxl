-- STREAMING_CHUNK:Initializing GUI and Services...
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local StarterGui = game:GetService("StarterGui")
local CoreGui = game:GetService("CoreGui")
local LocalPlayer = Players.LocalPlayer
-- ลบ UI เก่าทิ้งถ้ามีการรันซ้ำ
if CoreGui:FindFirstChild("iOSSpeedUI") then
CoreGui:FindFirstChild("iOSSpeedUI"):Destroy()
end
-- STREAMING_CHUNK:Creating the Main Frame...
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "iOSSpeedUI"
ScreenGui.Parent = CoreGui
ScreenGui.ResetOnSpawn = false
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.Position = UDim2.new(0.5, -125, 0.5, -100)
MainFrame.Size = UDim2.new(0, 250, 0, 220)
MainFrame.Active = true
MainFrame.Draggable = true -- ทำให้สามารถใช้นิ้วลากบนหน้าจอ iPad ได้
local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 10)
UICorner.Parent = MainFrame
-- STREAMING_CHUNK:Adding Title and UI Elements...
local Title = Instance.new("TextLabel")
Title.Parent = MainFrame
Title.BackgroundTransparency = 1
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Font = Enum.Font.GothamBold
Title.Text = "iOS Speed & Anti-Report"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 16
local SpeedInput = Instance.new("TextBox")
SpeedInput.Parent = MainFrame
SpeedInput.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
SpeedInput.Position = UDim2.new(0.1, 0, 0.25, 0)
SpeedInput.Size = UDim2.new(0.8, 0, 0, 35)
SpeedInput.Font = Enum.Font.Gotham
SpeedInput.PlaceholderText = "Enter Speed (1 - 350)"
SpeedInput.Text = ""
SpeedInput.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedInput.TextSize = 14
local UICornerInput = Instance.new("UICorner")
UICornerInput.CornerRadius = UDim.new(0, 6)
UICornerInput.Parent = SpeedInput
local SpeedButton = Instance.new("TextButton")
SpeedButton.Parent = MainFrame
SpeedButton.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
SpeedButton.Position = UDim2.new(0.1, 0, 0.45, 0)
SpeedButton.Size = UDim2.new(0.8, 0, 0, 40)
SpeedButton.Font = Enum.Font.GothamBold
SpeedButton.Text = "Apply Speed"
SpeedButton.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedButton.TextSize = 14
local UICornerBtn = Instance.new("UICorner")
UICornerBtn.CornerRadius = UDim.new(0, 6)
UICornerBtn.Parent = SpeedButton
local AntiReportButton = Instance.new("TextButton")
AntiReportButton.Parent = MainFrame
AntiReportButton.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
AntiReportButton.Position = UDim2.new(0.1, 0, 0.7, 0)
AntiReportButton.Size = UDim2.new(0.8, 0, 0, 40)
AntiReportButton.Font = Enum.Font.GothamBold
AntiReportButton.Text = "Enable Anti-Report"
AntiReportButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AntiReportButton.TextSize = 14
local UICornerAntiBtn = Instance.new("UICorner")
UICornerAntiBtn.CornerRadius = UDim.new(0, 6)
UICornerAntiBtn.Parent = AntiReportButton
-- STREAMING_CHUNK:Implementing Speed Bypass Logic...
local speedEnabled = false
local targetSpeed = 16
local speedConnection
-- ฟังก์ชันสำหรับล็อกความเร็ว ป้องกันเกมรีเซ็ตค่า
local function applySpeedBypass()
if speedConnection then speedConnection:Disconnect() end
speedConnection = RunService.RenderStepped:Connect(function()
local character = LocalPlayer.Character
if character and character:FindFirstChild("Humanoid") then
-- บังคับตั้งค่าความเร็วในทุกๆ เฟรม
character.Humanoid.WalkSpeed = targetSpeed
end
end)
end
-- ฟังก์ชันยกเลิกล็อกความเร็ว
local function removeSpeedBypass()
if speedConnection then
speedConnection:Disconnect()
speedConnection = nil
end
local character = LocalPlayer.Character
if character and character:FindFirstChild("Humanoid") then
character.Humanoid.WalkSpeed = 16 -- คืนค่าความเร็วปกติ
end
end
-- STREAMING_CHUNK:Handling Speed Button Click Event...
SpeedButton.MouseButton1Click:Connect(function()
if not speedEnabled then
local num = tonumber(SpeedInput.Text)
if num and num >= 1 and num <= 350 then
speedEnabled = true
targetSpeed = num
SpeedButton.Text = "Stop Speed"
SpeedButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
applySpeedBypass() -- เรียกใช้ฟังก์ชันล็อกความเร็ว
else
SpeedButton.Text = "Invalid Number!"
SpeedButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
task.wait(1.5)
if not speedEnabled then
SpeedButton.Text = "Apply Speed"
SpeedButton.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
end
end
else
speedEnabled = false
SpeedButton.Text = "Apply Speed"
SpeedButton.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
removeSpeedBypass() -- ปิดการล็อกความเร็ว
end
end)
-- STREAMING_CHUNK:Handling Anti-Report with Cooldown...
local isAntiReportOnCooldown = false
AntiReportButton.MouseButton1Click:Connect(function()
if isAntiReportOnCooldown then return end
isAntiReportOnCooldown = true
AntiReportButton.Text = "Processing..."
AntiReportButton.BackgroundColor3 = Color3.fromRGB(150, 150, 150)
-- ซ่อน PlayerList ป้องกันการส่องชื่อ
pcall(function()
StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.PlayerList, false)
end)
AntiReportButton.Text = "Anti-Report: ACTIVE"
AntiReportButton.BackgroundColor3 = Color3.fromRGB(50, 180, 50)
-- แจ้งเตือนแค่ครั้งเดียว
pcall(function()
StarterGui:SetCore("SendNotification", {
Title = "Protection",
Text = "Anti-Report functions activated locally.",
Duration = 2
})
end)
-- หน่วงเวลา 3 วินาทีก่อนให้กดปุ่มได้อีกครั้ง
task.wait(3)
AntiReportButton.Text = "Enable Anti-Report"
AntiReportButton.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
isAntiReportOnCooldown = false
end)
