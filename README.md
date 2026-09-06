-- STREAMING_CHUNK:Initializing Services and Variables...
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local AimbotEnabled = false
local AimTargetPart = "Head" -- "Head" หรือ "HumanoidRootPart"
local ESPEnabled = false
local SpeedEnabled = false
local CurrentSpeed = 16
local NoClipEnabled = false
local OneShotEnabled = false
local InfJumpEnabled = false
-- ฟังก์ชันตรวจสอบทีม (Team Check) เพื่อไม่ให้ล็อกเพื่อนร่วมทีม
local function isEnemy(player)
if player == LocalPlayer then return false end
if LocalPlayer.Team and player.Team then
return LocalPlayer.Team ~= player.Team
end
return true
end
-- STREAMING_CHUNK:Initializing WallCheck and Target Finder...
local function isVisible(targetPart)
if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("Head") then
return true
end
local origin = LocalPlayer.Character.Head.Position
local direction = targetPart.Position - origin
local raycastParams = RaycastParams.new()
raycastParams.FilterType = Enum.RaycastFilterType.Exclude
raycastParams.FilterDescendantsInstances = {LocalPlayer.Character}
raycastParams.IgnoreWater = true
local result = workspace:Raycast(origin, direction, raycastParams)
if result then
local hitInstance = result.Instance
local targetCharacter = targetPart.Parent
if hitInstance:IsDescendantOf(targetCharacter) then
return true
else
return false
end
end
return true
end
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
local rootPart = character:FindFirstChild("HumanoidRootPart") or character:FindFirstChild("Torso")
if rootPart then return rootPart end
end
return character:FindFirstChild("HumanoidRootPart")
end
-- STREAMING_CHUNK:Creating GUI and Custom Toggle Button (image.png)...
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ModMenuGui"
ScreenGui.ResetOnSpawn = false
if syn and syn.protect_gui then
syn.protect_gui(ScreenGui)
ScreenGui.Parent = game:GetService("CoreGui")
else
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
end
local ToggleButton = Instance.new("ImageButton")
ToggleButton.Name = "ToggleButton"
ToggleButton.Size = UDim2.new(0, 60, 0, 60)
ToggleButton.Position = UDim2.new(0, 20, 0, 20)
ToggleButton.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
ToggleButton.BorderColor3 = Color3.fromRGB(0, 255, 204)
ToggleButton.BorderSizePixel = 2
ToggleButton.Image = "rbxassetid://image.png"
ToggleButton.Parent = ScreenGui
local UICornerBtn = Instance.new("UICorner")
UICornerBtn.CornerRadius = UDim.new(1, 0)
UICornerBtn.Parent = ToggleButton
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 290, 0, 480)
MainFrame.Position = UDim2.new(0.5, -145, 0.5, -240)
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
Header.Text = "  Control Panel"
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
-- STREAMING_CHUNK:Adding Toggles and GUI Elements...
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
-- 1. ล็อกเป้าหมาย (Global Aimbot)
createToggle("1. ล็อกเป้าหมาย (ทั่วจอ)", 55, function(state)
AimbotEnabled = state
end)
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
-- STREAMING_CHUNK:Implementing Main Feature Loops (Snappy Aimbot & Magic Bullet)...
local currentLockedTarget = nil
RunService.RenderStepped:Connect(function()
currentLockedTarget = nil
if AimbotEnabled then
local closestPlayer = nil
local shortestDistance = math.huge
for _, player in ipairs(Players:GetPlayers()) do
if isEnemy(player) and player.Character then
local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
local targetPart = getTargetPart(player.Character)
if humanoid and humanoid.Health > 0 and targetPart then
if isVisible(targetPart) then
local screenPos, onScreen = Camera:WorldToScreenPoint(targetPart.Position)
if onScreen then
local distance = (Vector2.new(screenPos.X, screenPos.Y) - UserInputService:GetMouseLocation()).Magnitude
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
currentLockedTarget = targetPart
local offset = (AimTargetPart == "Head") and Vector3.new(0, 0.1, 0) or Vector3.new(0, 0, 0)
local targetCFrame = CFrame.new(Camera.CFrame.Position, targetPart.Position + offset)
Camera.CFrame = Camera.CFrame:Lerp(targetCFrame, 1.0)
end
end
end
end)
-- ระบบกระสุนพุ่งเข้าเป้าหมาย 100% (Silent Aim / Magic Bullet)
local oldNamecall
oldNamecall = hookmetamethod(game, "__namecall", function(self, ...)
local method = getnamecallmethod()
local args = {...}
if AimbotEnabled and currentLockedTarget and (method == "FireServer" or method == "InvokeServer") then
for i, v in ipairs(args) do
if typeof(v) == "Vector3" then
args[i] = currentLockedTarget.Position
elseif typeof(v) == "Instance" and v:IsA("BasePart") then
args[i] = currentLockedTarget
end
end
end
return oldNamecall(self, unpack(args))
end)
-- 2. ESP
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
createToggle("2. มองผู้เล่น (ESP)", 130, function(state)
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
local function setupPlayer(player)
player.CharacterAdded:Connect(function(char)
if ESPEnabled and player ~= LocalPlayer then
applyESP(char)
end
end)
if player.Character and ESPEnabled and player ~= LocalPlayer then
applyESP(player.Character)
end
end
Players.PlayerAdded:Connect(setupPlayer)
for _, player in ipairs(Players:GetPlayers()) do
if player ~= LocalPlayer then
setupPlayer(player)
end
end
-- 3. Speed
createToggle("3. วิ่งเร็ว (Speed)", 175, function(state)
SpeedEnabled = state
end)
local SpeedLabel = Instance.new("TextLabel")
SpeedLabel.Size = UDim2.new(0, 260, 0, 18)
SpeedLabel.Position = UDim2.new(0, 15, 0, 215)
SpeedLabel.BackgroundTransparency = 1
SpeedLabel.Text = "ความเร็ว: 16"
SpeedLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
SpeedLabel.TextSize, SpeedLabel.Font = 12, Enum.Font.SourceSans
SpeedLabel.TextXAlignment = Enum.TextXAlignment.Left
SpeedLabel.Parent = MainFrame
local SpeedBox = Instance.new("TextBox")
SpeedBox.Size = UDim2.new(0, 260, 0, 26)
SpeedBox.Position = UDim2.new(0, 15, 0, 235)
SpeedBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
SpeedBox.Text = "16"
SpeedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedBox.TextSize = 13
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
-- 4. NoClip
createToggle("4. เดินทะลุกำแพง (NoClip)", 275, function(state)
NoClipEnabled = state
end)
RunService.Stepped:Connect(function()
if NoClipEnabled and LocalPlayer.Character then
for _, part in ipairs(LocalPlayer.Character:GetDescendants()) do
if part:IsA("BasePart") then
part.CanCollide = false
end
end
end
end)
-- 5. One-Shot Ammo Dump
createToggle("5. ยิงทีเดียวหมดแม็ก (One-Shot)", 320, function(state)
OneShotEnabled = state
end)
RunService.Heartbeat:Connect(function()
if OneShotEnabled and LocalPlayer.Character then
local tool = LocalPlayer.Character:FindFirstChildOfClass("Tool")
if tool then
pcall(function()
for i = 1, 10 do
tool:Activate()
end
end)
end
end
end)
-- 6. Infinite Jump (กระโดดไม่จำกัด)
createToggle("6. กระโดดไม่จำกัด (Inf Jump)", 365, function(state)
InfJumpEnabled = state
end)
UserInputService.JumpRequest:Connect(function()
if InfJumpEnabled and LocalPlayer.Character then
local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
if humanoid then
humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
end
end
end)
-- Draggable Window
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
