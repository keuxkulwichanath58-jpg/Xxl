-- รอให้โหลดเกมเสร็จสมบูรณ์
if not game:IsLoaded() then game.Loaded:Wait() end
local CoreGui = game:GetService("CoreGui")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local StarterGui = game:GetService("StarterGui")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer
local guiName = "iPadMenu_Speed_AntiReport"
-- ลบ UI เก่าทิ้งถ้ามีการรันซ้ำ
local targetParent = pcall(function() return CoreGui end) and CoreGui or player:WaitForChild("PlayerGui")
if targetParent:FindFirstChild(guiName) then
targetParent[guiName]:Destroy()
end
-- สร้าง ScreenGui หลัก
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = guiName
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = targetParent
-- ==========================================
-- ปุ่ม เปิด/ปิด เมนู (Toggle Button)
-- ==========================================
local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(0, 45, 0, 45)
ToggleButton.Position = UDim2.new(0, 15, 0, 15) -- มุมซ้ายบน
ToggleButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
ToggleButton.BorderSizePixel = 0
ToggleButton.Text = "⚙️"
ToggleButton.TextSize = 25
ToggleButton.Parent = ScreenGui
local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(0.5, 0) -- ทำให้เป็นวงกลม
ToggleCorner.Parent = ToggleButton
local ToggleStroke = Instance.new("UIStroke")
ToggleStroke.Color = Color3.fromRGB(80, 80, 80)
ToggleStroke.Thickness = 2
ToggleStroke.Parent = ToggleButton
-- ==========================================
-- หน้าต่างเมนูหลัก (Main Frame)
-- ==========================================
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 250, 0, 220)
MainFrame.Position = UDim2.new(0.5, -125, 0.5, -110) -- ตรงกลางจอ
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Parent = ScreenGui
local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = MainFrame
local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(60, 60, 60)
MainStroke.Thickness = 2
MainStroke.Parent = MainFrame
-- หัวข้อ (Title)
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundTransparency = 1
Title.Text = "🚀 Speed & Protect"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 20
Title.Font = Enum.Font.GothamBold
Title.Parent = MainFrame
-- ช่องกรอกตัวเลขความเร็ว (TextBox)
local SpeedInput = Instance.new("TextBox")
SpeedInput.Size = UDim2.new(0.8, 0, 0, 40)
SpeedInput.Position = UDim2.new(0.1, 0, 0, 50)
SpeedInput.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
SpeedInput.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedInput.PlaceholderText = "Speed (1 - 350)"
SpeedInput.Text = "50"
SpeedInput.TextSize = 18
SpeedInput.Font = Enum.Font.Gotham
SpeedInput.Parent = MainFrame
local InputCorner = Instance.new("UICorner")
InputCorner.CornerRadius = UDim.new(0, 6)
InputCorner.Parent = SpeedInput
-- ปุ่มเปิด/ปิด ความเร็ว (Speed Button)
local SpeedButton = Instance.new("TextButton")
SpeedButton.Size = UDim2.new(0.8, 0, 0, 40)
SpeedButton.Position = UDim2.new(0.1, 0, 0, 100)
SpeedButton.BackgroundColor3 = Color3.fromRGB(0, 170, 100)
SpeedButton.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedButton.Text = "Apply Speed"
SpeedButton.TextSize = 18
SpeedButton.Font = Enum.Font.GothamBold
SpeedButton.Parent = MainFrame
local BtnCorner1 = Instance.new("UICorner")
BtnCorner1.CornerRadius = UDim.new(0, 6)
BtnCorner1.Parent = SpeedButton
-- ปุ่มป้องกันรีพอร์ต (Anti-Report Button)
local AntiReportBtn = Instance.new("TextButton")
AntiReportBtn.Size = UDim2.new(0.8, 0, 0, 40)
AntiReportBtn.Position = UDim2.new(0.1, 0, 0, 150)
AntiReportBtn.BackgroundColor3 = Color3.fromRGB(170, 80, 0)
AntiReportBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
AntiReportBtn.Text = "🛡️ Anti-Report"
AntiReportBtn.TextSize = 18
AntiReportBtn.Font = Enum.Font.GothamBold
AntiReportBtn.Parent = MainFrame
local BtnCorner2 = Instance.new("UICorner")
BtnCorner2.CornerRadius = UDim.new(0, 6)
BtnCorner2.Parent = AntiReportBtn
-- ==========================================
-- ระบบ เปิด/ปิด หน้าต่าง (Toggle Logic)
-- ==========================================
ToggleButton.MouseButton1Click:Connect(function()
MainFrame.Visible = not MainFrame.Visible
end)
-- ==========================================
-- ระบบลากจูง (Drag UI) รองรับระบบสัมผัส (Touch)
-- ==========================================
local dragging, dragInput, dragStart, startPos
local function update(input)
local delta = input.Position - dragStart
MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end
MainFrame.InputBegan:Connect(function(input)
if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
dragging = true
dragStart = input.Position
startPos = MainFrame.Position
input.Changed:Connect(function()
if input.UserInputState == Enum.UserInputState.End then
dragging = false
end
end)
end
end)
MainFrame.InputChanged:Connect(function(input)
if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
dragInput = input
end
end)
UserInputService.InputChanged:Connect(function(input)
if input == dragInput and dragging then
update(input)
end
end)
-- ==========================================
-- ระบบ Speed (Bypass เกมที่ชอบรีเซ็ตความเร็ว)
-- ==========================================
local speedEnabled = false
local speedConnection = nil
SpeedButton.MouseButton1Click:Connect(function()
speedEnabled = not speedEnabled
if speedEnabled then
local targetSpeed = tonumber(SpeedInput.Text)
if targetSpeed and targetSpeed >= 1 and targetSpeed <= 350 then
SpeedButton.Text = "Stop Speed"
SpeedButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50) -- เปลี่ยนเป็นสีแดง
-- ล็อกความเร็วทุกๆ เฟรม
speedConnection = RunService.RenderStepped:Connect(function()
local char = player.Character
if char and char:FindFirstChild("Humanoid") then
char.Humanoid.WalkSpeed = targetSpeed
end
end)
else
-- ถ้ากรอกเลขผิด
speedEnabled = false
SpeedInput.Text = "50"
StarterGui:SetCore("SendNotification", {
Title = "Error",
Text = "กรุณาใส่ตัวเลข 1 - 350",
Duration = 3
})
end
else
SpeedButton.Text = "Apply Speed"
SpeedButton.BackgroundColor3 = Color3.fromRGB(0, 170, 100) -- เปลี่ยนกลับเป็นสีเขียว
-- ปิดการล็อกความเร็วและคืนค่าปกติ (16 คือความเร็วพื้นฐาน Roblox)
if speedConnection then
speedConnection:Disconnect()
speedConnection = nil
end
local char = player.Character
if char and char:FindFirstChild("Humanoid") then
char.Humanoid.WalkSpeed = 16
end
end
end)
-- ==========================================
-- ระบบ Anti-Report พร้อม Cooldown กันข้อความเด้งซ้อน
-- ==========================================
local isCooldown = false
AntiReportBtn.MouseButton1Click:Connect(function()
if isCooldown then return end -- ถ้าติดคูลดาวน์อยู่ กดไม่ติด
isCooldown = true
-- ทำการแจ้งเตือน
StarterGui:SetCore("SendNotification", {
Title = "Protection",
Text = "Anti-Report functions activated locally.",
Duration = 3 -- โชว์ 3 วินาที
})
-- โค้ดป้องกันการรีพอร์ตเบื้องต้น (Clear Name/ID locally)
-- เป็นการจำลองการซ่อนฝั่งผู้เล่น
pcall(function()
if player.Character then
-- ลบชื่อตัวเองที่ลอยอยู่บนหัว (ถ้าเกมนั้นมี)
for _, v in pairs(player.Character:GetDescendants()) do
if v:IsA("BillboardGui") or v:IsA("SurfaceGui") or v:IsA("TextLabel") then
if string.match(string.lower(v.Text), string.lower(player.Name)) or
string.match(v.Text, player.DisplayName) then
v:Destroy()
end
end
end
end
end)
AntiReportBtn.Text = "⏳ Wait..."
AntiReportBtn.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
-- หน่วงเวลา 3.5 วินาที ก่อนจะกดได้ใหม่
task.wait(3.5)
isCooldown = false
AntiReportBtn.Text = "🛡️ Anti-Report"
AntiReportBtn.BackgroundColor3 = Color3.fromRGB(170, 80, 0)
end)
print("Loaded Script Successfully!")
