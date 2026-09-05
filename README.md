-- STREAMING_CHUNK:Initializing Services and Variables...
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local AimbotEnabled = false
local ESPEnabled = false
local SpeedEnabled = false
local CurrentSpeed = 16
-- ฟังก์ชันค้นหาพาร์ทหัว (Head) ที่แม่นยำที่สุด รองรับหลายรูปแบบตัวละคร
local function getTargetHead(character)
if not character then return nil end
local head = character:FindFirstChild("Head")
if head then return head end
-- ค้นหาเผื่อกรณีโมเดลเปลี่ยนชื่อหรือโครงสร้าง R15/R6
for _, child in ipairs(character:GetChildren()) do
if child:IsA("BasePart") and (child.Name:lower():find("head") or child.Name:lower():find("upper")) then
return child
end
end
return character:FindFirstChild("HumanoidRootPart")
end
-- สร้างหน้าต่าง GUI (ScreenGui)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ModMenuGui"
ScreenGui.ResetOnSpawn = false
if syn and syn.protect_gui then
syn.protect_gui(ScreenGui)
ScreenGui.Parent = game:GetService("CoreGui")
else
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
end
-- STREAMING_CHUNK:Creating Floating Toggle Button...
local ToggleButton = Instance.new("ImageButton")
ToggleButton.Name = "ToggleButton"
ToggleButton.Size = UDim2.new(0, 60, 0, 60)
ToggleButton.Position = UDim2.new(0, 20, 0, 20)
ToggleButton.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
ToggleButton.BorderColor3 = Color3.fromRGB(0, 255, 204)
ToggleButton.BorderSizePixel = 2
ToggleButton.Image = "rbxassetid://0" -- สามารถแทนที่ด้วย Asset ID หรือลิงก์รูปภาพ
ToggleButton.Parent = ScreenGui
local UICornerBtn = Instance.new("UICorner")
UICornerBtn.CornerRadius = UDim.new(1, 0)
UICornerBtn.Parent = ToggleButton
-- STREAMING_CHUNK:Creating Main Script Window...
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 280, 0, 320)
MainFrame.Position = UDim2.new(0.5, -140, 0.5, -160)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BackgroundTransparency = 0.1
MainFrame.BorderColor3 = Color3.fromRGB(51, 51, 51)
MainFrame.Visible = false
MainFrame.Parent = ScreenGui
local UICornerMain = Instance.new("UICorner")
UICornerMain.CornerRadius = UDim.new(0, 10)
UICornerMain.Parent = MainFrame
local Header = Instance.new("TextLabel")
Header.Size = UDim2.new(1, 0, 0, 40)
Header.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Header.Text = "  Control Panel (Fixed V2)"
Header.TextColor3 = Color3.fromRGB(0, 255, 204)
Header.TextSize = 16
Header.Font = Enum.Font.SourceSansBold
Header.TextXAlignment = Enum.TextXAlignment.Left
Header.Parent = MainFrame
local UICornerHeader = Instance.new("UICorner")
UICornerHeader.CornerRadius = UDim.new(0, 10)
UICornerHeader.Parent = Header
ToggleButton.MouseButton1Click:Connect(function()
MainFrame.Visible = not MainFrame.Visible
end)
-- STREAMING_CHUNK:Adding Toggles and Aimbot Logic...
local function createToggle(name, yPos, callback)
local Label = Instance.new("TextLabel")
Label.Size = UDim2.new(0, 160, 0, 30)
Label.Position = UDim2.new(0, 15, 0, yPos)
Label.BackgroundTransparency = 1
Label.Text = name
Label.TextColor3 = Color3.fromRGB(255, 255, 255)
Label.TextSize = 14
Label.Font = Enum.Font.SourceSansSemibold
Label.TextXAlignment = Enum.TextXAlignment.Left
Label.Parent = MainFrame
local Button = Instance.new("TextButton")
Button.Size = UDim2.new(0, 50, 0, 24)
Button.Position = UDim2.new(1, -65, 0, yPos + 3)
Button.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
Button.Text = "OFF"
Button.TextColor3 = Color3.fromRGB(255, 255, 255)
Button.TextSize = 12
Button.Font = Enum.Font.SourceSansBold
Button.Parent = MainFrame
local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 4)
UICorner.Parent = Button
local state = false
Button.MouseButton1Click:Connect(function()
state = not state
if state then
Button.BackgroundColor3 = Color3.fromRGB(0, 255, 204)
Button.TextColor3 = Color3.fromRGB(0, 0, 0)
Button.Text = "ON"
else
Button.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
Button.TextColor3 = Color3.fromRGB(255, 255, 255)
Button.Text = "OFF"
end
callback(state)
end)
end
-- 1. ฟังก์ชันล็อกหัวปรับปรุงใหม่ให้แม่นยำ
createToggle("1. ล็อกหัว (Aimbot)", 60, function(state)
AimbotEnabled = state
end)
RunService.RenderStepped:Connect(function()
if AimbotEnabled then
local closestPlayer = nil
local shortestDistance = math.huge
local mousePos = UserInputService:GetMouseLocation()
for _, player in ipairs(Players:GetPlayers()) do
if player ~= LocalPlayer and player.Character then
local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
local headPart = getTargetHead(player.Character)
-- ตรวจสอบว่าผู้เล่นยังมีชีวิตและอยู่ฝ่ายตรงข้าม (ตรวจสอบ Team ตามโครงสร้างเกมทั่วไปได้)
if humanoid and humanoid.Health > 0 and headPart then
local screenPos, onScreen = Camera:WorldToScreenPoint(headPart.Position)
if onScreen then
local distance = (Vector2.new(screenPos.X, screenPos.Y) - mousePos).Magnitude
if distance < shortestDistance then
shortestDistance = distance
closestPlayer = player
end
end
end
end
end
if closestPlayer then
local headPart = getTargetHead(closestPlayer.Character)
if headPart then
-- เล็งไปที่หัวโดยปรับตำแหน่งความสูงเล็กน้อยเพื่อความแม่นยำสูงสุด
local targetCFrame = CFrame.new(Camera.CFrame.Position, headPart.Position + Vector3.new(0, 0.2, 0))
Camera.CFrame = Camera.CFrame:Lerp(targetCFrame, 0.5) -- สมูทกล้องไม่ให้กระชากเกินไป
end
end
end
end)
-- STREAMING_CHUNK:Implementing Persistent ESP Logic...
local function applyESP(character)
if not character then return end
task.wait(0.3)
if not character:FindFirstChild("ESP_Highlight") then
local hl = Instance.new("Highlight")
hl.Name = "ESP_Highlight"
hl.FillColor = Color3.fromRGB(255, 0, 0)
hl.OutlineColor = Color3.fromRGB(255, 255, 255)
hl.Parent = character
end
end
createToggle("2. มองผู้เล่น (ESP)", 110, function(state)
ESPEnabled = state
for _, player in ipairs(Players:GetPlayers()) do
if player ~= LocalPlayer then
if ESPEnabled then
applyESP(player.Character)
else
local hl = player.Character and player.Character:FindFirstChild("ESP_Highlight")
if hl then hl:Destroy() end
end
end
end
end)
-- ระบบจัดการรอบเกมและตัวละครเกิดใหม่ (ESP ติดต่อเนื่องอัตโนมัติ)
local function setupPlayer(player)
player.CharacterAdded:Connect(function(char)
if ESPEnabled then
applyESP(char)
end
end)
if player.Character and ESPEnabled then
applyESP(player.Character)
end
end
Players.PlayerAdded:Connect(setupPlayer)
for _, player in ipairs(Players:GetPlayers()) do
if player ~= LocalPlayer then
setupPlayer(player)
end
end
-- STREAMING_CHUNK:Implementing Speed Hack and Drag System...
createToggle("3. วิ่งเร็ว (Speed)", 160, function(state)
SpeedEnabled = state
end)
local SpeedLabel = Instance.new("TextLabel")
SpeedLabel.Size = UDim2.new(0, 250, 0, 20)
SpeedLabel.Position = UDim2.new(0, 15, 0, 210)
SpeedLabel.BackgroundTransparency = 1
SpeedLabel.Text = "ความเร็ว: 16"
SpeedLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
SpeedLabel.TextSize, SpeedLabel.Font = 13, Enum.Font.SourceSans
SpeedLabel.TextXAlignment = Enum.TextXAlignment.Left
SpeedLabel.Parent = MainFrame
local SpeedBox = Instance.new("TextBox")
SpeedBox.Size = UDim2.new(0, 250, 0, 30)
SpeedBox.Position = UDim2.new(0, 15, 0, 235)
SpeedBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
SpeedBox.Text = "16"
SpeedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedBox.TextSize = 14
SpeedBox.Font = Enum.Font.SourceSans
SpeedBox.Parent = MainFrame
local UICornerBox = Instance.new("UICorner")
UICornerBox.CornerRadius = UDim.new(0, 4)
UICornerBox.Parent = SpeedBox
SpeedBox.FocusLost:Connect(function(enterPressed)
local val = tonumber(SpeedBox.Text)
if val then
CurrentSpeed = math.clamp(val, 16, 200)
SpeedBox.Text = tostring(CurrentSpeed)
SpeedLabel.Text = "ความเร็ว: " .. CurrentSpeed
else
SpeedBox.Text = tostring(CurrentSpeed)
end
end)
RunService.Heartbeat:Connect(function()
if SpeedEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
LocalPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = CurrentSpeed
end
end)
-- ระบบลากหน้าต่างเมนู (Draggable Window)
local dragging, dragInput, dragStart, startPos
Header.InputBegan:Connect(function(input)
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
Header.InputChanged:Connect(function(input)
if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
dragInput = input
end
end)
UserInputService.InputChanged:Connect(function(input)
if input == dragInput and dragging then
local delta = input.Position - dragStart
MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end
end)
