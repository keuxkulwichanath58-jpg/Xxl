-- STREAMING_CHUNK:Initializing Services and Variables...
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local AimbotEnabled = false
local AimTargetPart = "Head" -- ค่าเริ่มต้นคือล็อกหัว ("Head" หรือ "HumanoidRootPart")
local ESPEnabled = false
local SpeedEnabled = false
local CurrentSpeed = 16
-- ฟังก์ชันตรวจสอบการมองเห็น (Raycast WallCheck) เพื่อไม่ให้ล็อกทะลุกำแพง
local function isVisible(targetPart)
if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("Head") then
return true -- ถ้าหาตัวผู้เล่นไม่เจอ ให้ผ่านไปก่อน
end
local origin = LocalPlayer.Character.Head.Position
local direction = targetPart.Position - origin
local raycastParams = RaycastParams.new()
raycastParams.FilterType = Enum.RaycastFilterType.Exclude
raycastParams.FilterDescendantsInstances = {LocalPlayer.Character}
raycastParams.IgnoreWater = true
local result = workspace:Raycast(origin, direction, raycastParams)
if result then
-- ถ้าชนอะไรบางอย่างก่อนถึงเป้าหมาย และสิ่งนั้นไม่ได้เป็นส่วนหนึ่งของตัวละครเป้าหมาย แสดงว่ามีกำแพงกั้น
local hitInstance = result.Instance
local targetCharacter = targetPart.Parent
if hitInstance:IsDescendantOf(targetCharacter) then
return true -- มองเห็นได้ปกติ (ไม่มีกำแพงขวางระหว่างทาง)
else
return false -- มีกำแพงกั้น
end
end
return true
end
-- ฟังก์ชันค้นหาพาร์ทเป้าหมายที่แม่นยำ (หัว หรือ ตัว)
local function getTargetPart(character)
if not character then return nil end
if AimTargetPart == "Head" then
local head = character:FindFirstChild("Head")
if head then return head end
for _, child in ipairs(character:GetChildren()) do
if child:IsA("BasePart") and (child.Name:lower():find("head") or child.Name:lower():find("upper")) then
return child
end
end
else
-- ล็อกตัว (HumanoidRootPart หรือ Torso)
local rootPart = character:FindFirstChild("HumanoidRootPart") or character:FindFirstChild("Torso")
if rootPart then return rootPart end
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
-- STREAMING_CHUNK:Creating Floating Toggle Button with Custom Image...
local ToggleButton = Instance.new("ImageButton")
ToggleButton.Name = "ToggleButton"
ToggleButton.Size = UDim2.new(0, 60, 0, 60)
ToggleButton.Position = UDim2.new(0, 20, 0, 20)
ToggleButton.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
ToggleButton.BorderColor3 = Color3.fromRGB(0, 255, 204)
ToggleButton.BorderSizePixel = 2
-- ใช้รูปภาพตามที่คุณกำหนด (หากรันในเกมที่รองรับ asset id สามารถเปลี่ยนเป็น rbxassetid://... ได้)
ToggleButton.Image = "rbxassetid://0"
ToggleButton.Parent = ScreenGui
local UICornerBtn = Instance.new("UICorner")
UICornerBtn.CornerRadius = UDim.new(1, 0)
UICornerBtn.Parent = ToggleButton
-- STREAMING_CHUNK:Creating Main Script Window...
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 290, 0, 360)
MainFrame.Position = UDim2.new(0.5, -145, 0.5, -180)
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
Header.Text = "  Control Panel (Fixed V4)"
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
-- STREAMING_CHUNK:Adding Toggles and Advanced Aimbot Logic...
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
Button.Size = UDim2.new(0, 60, 0, 24)
Button.Position = UDim2.new(1, -75, 0, yPos + 3)
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
-- 1. ฟังก์ชันเปิด/ปิดระบบล็อก (Aimbot)
createToggle("1. ล็อกเป้าหมาย", 55, function(state)
AimbotEnabled = state
end)
-- ปุ่มเลือกโหมดล็อก (หัว / ตัว)
local ModeButton = Instance.new("TextButton")
ModeButton.Size = UDim2.new(0, 260, 0, 25)
ModeButton.Position = UDim2.new(0, 15, 0, 95)
ModeButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
ModeButton.Text = "โหมดล็อก: เล็งไปที่ [ หัว ]"
ModeButton.TextColor3 = Color3.fromRGB(0, 255, 204)
ModeButton.TextSize = 13
ModeButton.Font = Enum.Font.SourceSansBold
ModeButton.Parent = MainFrame
local UICornerMode = Instance.new("UICorner")
UICornerMode.CornerRadius = UDim.new(0, 4)
UICornerMode.Parent = ModeButton
ModeButton.MouseButton1Click:Connect(function()
if AimTargetPart == "Head" then
AimTargetPart = "HumanoidRootPart"
ModeButton.Text = "โหมดล็อก: เล็งไปที่ [ ตัว ]"
else
AimTargetPart = "Head"
ModeButton.Text = "โหมดล็อก: เล็งไปที่ [ หัว ]"
end
end)
-- ลูปการทำงาน Aimbot พร้อมระบบเช็คกำแพง (WallCheck)
RunService.RenderStepped:Connect(function()
if AimbotEnabled then
local closestPlayer = nil
local shortestDistance = math.huge
local mousePos = UserInputService:GetMouseLocation()
for _, player in ipairs(Players:GetPlayers()) do
if player ~= LocalPlayer and player.Character then
local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
local targetPart = getTargetPart(player.Character)
if humanoid and humanoid.Health > 0 and targetPart then
-- เช็คว่ามองเห็นจริงหรือไม่ (ป้องกันการล็อกทะลุกำแพง)
if isVisible(targetPart) then
local screenPos, onScreen = Camera:WorldToScreenPoint(targetPart.Position)
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
end
if closestPlayer then
local targetPart = getTargetPart(closestPlayer.Character)
if targetPart then
local offset = (AimTargetPart == "Head") and Vector3.new(0, 0.1, 0) or Vector3.new(0, 0, 0)
local targetCFrame = CFrame.new(Camera.CFrame.Position, targetPart.Position + offset)
Camera.CFrame = Camera.CFrame:Lerp(targetCFrame, 0.4) -- สมูทกล้องแบบนุ่มนวล
end
end
end
end)
-- STREAMING_CHUNK:Implementing Persistent ESP Logic...
local function applyESP(character)
if not character then return end
task.wait(0.2)
if not character:FindFirstChild("ESP_Highlight") then
local hl = Instance.new("Highlight")
hl.Name = "ESP_Highlight"
hl.FillColor = Color3.fromRGB(255, 0, 0)
hl.OutlineColor = Color3.fromRGB(255, 255, 255)
hl.Parent = character
end
end
createToggle("2. มองผู้เล่น (ESP)", 135, function(state)
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
-- ระบบจัดการรอบเกมและตัวละครเกิดใหม่ (ESP ติดต่อเนื่องอัตโนมัติทุกเกม)
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
createToggle("3. วิ่งเร็ว (Speed)", 185, function(state)
SpeedEnabled = state
end)
local SpeedLabel = Instance.new("TextLabel")
SpeedLabel.Size = UDim2.new(0, 260, 0, 20)
SpeedLabel.Position = UDim2.new(0, 15, 0, 230)
SpeedLabel.BackgroundTransparency = 1
SpeedLabel.Text = "ความเร็ว: 16"
SpeedLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
SpeedLabel.TextSize, SpeedLabel.Font = 13, Enum.Font.SourceSans
SpeedLabel.TextXAlignment = Enum.TextXAlignment.Left
SpeedLabel.Parent = MainFrame
local SpeedBox = Instance.new("TextBox")
SpeedBox.Size = UDim2.new(0, 260, 0, 30)
SpeedBox.Position = UDim2.new(0, 15, 0, 255)
SpeedBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
SpeedBox.Text = "16"
SpeedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedBox.TextSize = 14
SpeedBox.Font = Enum.Font.SourceSans
SpeedBox.Parent = MainFrame
local UICornerBox = Instance.new("UICorner")
UICornerBox.CornerRadius = UDim.new(0, 4)
UICornerBox.Parent = SpeedBox
SpeedBox.FocusLost:Connect(function()
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
