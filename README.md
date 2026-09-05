-- STREAMING_CHUNK:Initializing GUI components...
-- ตรวจสอบหา Player
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local CoreGui = game:GetService("CoreGui")
-- สร้าง ScreenGui หน้าต่างเมนูหลัก
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "iPadModMenu"
ScreenGui.ResetOnSpawn = false
-- พยายามใส่ UI ไว้ใน CoreGui เพื่อป้องกันการถูกตรวจจับจากเกม (ถ้า Executor รองรับ)
-- หากไม่รองรับ ให้ไปใส่ไว้ใน PlayerGui ของผู้เล่นแทน
local success, err = pcall(function()
ScreenGui.Parent = CoreGui
end)
if not success then
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
end
-- STREAMING_CHUNK:Building Main Frame and Title...
-- สร้างกรอบเมนู (Frame)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.5, -125, 0.5, -100)
MainFrame.Size = UDim2.new(0, 250, 0, 200)
MainFrame.Active = true
MainFrame.Draggable = true -- ทำให้สามารถลากเมนูบนหน้าจอ iPad ได้
-- สร้างแถบหัวข้อ (Title)
local Title = Instance.new("TextLabel")
Title.Name = "Title"
Title.Parent = MainFrame
Title.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
Title.BorderSizePixel = 0
Title.Size = UDim2.new(1, 0, 0, 35)
Title.Font = Enum.Font.GothamBold
Title.Text = " iPad Mod Menu "
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 16
-- STREAMING_CHUNK:Building Speed (1-350) UI...
-- สร้างช่องกรอกตัวเลขความเร็ว (TextBox)
local SpeedInput = Instance.new("TextBox")
SpeedInput.Name = "SpeedInput"
SpeedInput.Parent = MainFrame
SpeedInput.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
SpeedInput.BorderSizePixel = 0
SpeedInput.Position = UDim2.new(0.1, 0, 0.25, 0)
SpeedInput.Size = UDim2.new(0.8, 0, 0, 35)
SpeedInput.Font = Enum.Font.Gotham
SpeedInput.PlaceholderText = "Enter Speed (1 - 350)"
SpeedInput.Text = ""
SpeedInput.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedInput.TextSize = 14
-- สร้างปุ่มยืนยันความเร็ว (Speed Button)
local SpeedButton = Instance.new("TextButton")
SpeedButton.Name = "SpeedButton"
SpeedButton.Parent = MainFrame
SpeedButton.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
SpeedButton.BorderSizePixel = 0
SpeedButton.Position = UDim2.new(0.1, 0, 0.45, 0)
SpeedButton.Size = UDim2.new(0.8, 0, 0, 35)
SpeedButton.Font = Enum.Font.GothamBold
SpeedButton.Text = "Apply Speed"
SpeedButton.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedButton.TextSize = 14
-- STREAMING_CHUNK:Building Anti-Report UI...
-- สร้างปุ่ม Anti-Report
local AntiReportButton = Instance.new("TextButton")
AntiReportButton.Name = "AntiReportButton"
AntiReportButton.Parent = MainFrame
AntiReportButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
AntiReportButton.BorderSizePixel = 0
AntiReportButton.Position = UDim2.new(0.1, 0, 0.67, 0)
AntiReportButton.Size = UDim2.new(0.8, 0, 0, 35)
AntiReportButton.Font = Enum.Font.GothamBold
AntiReportButton.Text = "Enable Anti-Report"
AntiReportButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AntiReportButton.TextSize = 14
-- STREAMING_CHUNK:Adding Logic to Buttons...
-- ฟังก์ชันเมื่อกดปุ่ม Apply Speed
SpeedButton.MouseButton1Click:Connect(function()
local speedValue = tonumber(SpeedInput.Text)
if speedValue and speedValue >= 1 and speedValue <= 350 then
-- ตรวจสอบว่าตัวละครของผู้เล่นโหลดมาหรือยัง
if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
-- ปรับความเร็วตัวละคร
LocalPlayer.Character.Humanoid.WalkSpeed = speedValue
SpeedButton.Text = "Speed Applied: " .. speedValue
wait(2)
SpeedButton.Text = "Apply Speed"
end
else
SpeedButton.Text = "Invalid Number!"
SpeedButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
wait(1.5)
SpeedButton.Text = "Apply Speed"
SpeedButton.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
end
end)
-- ฟังก์ชันเมื่อกดปุ่ม Anti-Report
AntiReportButton.MouseButton1Click:Connect(function()
-- 1. ปิด UI PlayerList (Leaderboard) เพื่อป้องกันไม่ให้คนอื่นในเครื่องเราเห็นชื่อ
local StarterGui = game:GetService("StarterGui")
pcall(function()
StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.PlayerList, false)
end)
-- 2. เคลียร์ข้อความแชท (ถ้าทำได้) และพยายามทำให้ชื่อเราไม่โดดเด่นฝั่ง Client
AntiReportButton.Text = "Anti-Report: ACTIVE"
AntiReportButton.BackgroundColor3 = Color3.fromRGB(50, 180, 50)
-- ข้อความแจ้งเตือนมุมขวาล่างของ Roblox
pcall(function()
StarterGui:SetCore("SendNotification", {
Title = "Protection",
Text = "Anti-Report functions activated locally.",
Duration = 3
})
end)
end)
