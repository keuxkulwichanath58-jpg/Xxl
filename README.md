--// Roblox Script UI (Auto Speed 30 & Safe Shooting)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")

local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

--// ลบ UI เก่าทิ้งถ้ามีอยู่แล้ว
if game.CoreGui:FindFirstChild("CustomScriptUI") then
    game.CoreGui.CustomScriptUI:Destroy()
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "CustomScriptUI"
ScreenGui.Parent = game.CoreGui
ScreenGui.ResetOnSpawn = false

--// 1. ปุ่มเปิด/ปิดเมนูหลัก (ใช้รูปภาพ image.png)
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

--// 2. หน้าต่างเมนูหลัก
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BorderColor3 = Color3.fromRGB(50, 50, 50)
MainFrame.Position = UDim2.new(0.5, -125, 0.5, -125)
MainFrame.Size = UDim2.new(0, 250, 0, 260)
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
TitleLabel.Text = "CONTROL MENU"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.TextSize = 14

local UICornerTitle = Instance.new("UICorner")
UICornerTitle.CornerRadius = UDim.new(0, 10)
UICornerTitle.Parent = TitleLabel

ToggleButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

local function createMenuToggle(name, yPos, callback)
    local btn = Instance.new("TextButton")
    btn.Parent = MainFrame
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    btn.Position = UDim2.new(0, 15, 0, yPos)
    btn.Size = UDim2.new(0, 220, 0, 35)
    btn.Font = Enum.Font.GothamMedium
    btn.Text = name .. ": OFF"
    btn.TextColor3 = Color3.fromRGB(255, 100, 100)
    btn.TextSize = 13
    
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

--// ฟังก์ชันที่ 1: ESP (มองผู้เล่น)
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

--// ฟังก์ชันที่ 2: NoClip (เดินทะลุกำแพง)
local noclipEnabled = false
createMenuToggle("NoClip", 90, function(state)
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

--// ฟังก์ชันที่ 3: Infinite Jump (กระโดดไม่จำกัด)
local infJumpEnabled = false
createMenuToggle("Infinite Jump", 135, function(state)
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

--// ฟังก์ชันที่ 4: วิ่งเร็วอัตโนมัติ (เปิดตลอดเวลา 30)
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
SpeedLabel.Position = UDim2.new(0, 15, 0, 180)
SpeedLabel.Size = UDim2.new(0, 220, 0, 30)
SpeedLabel.Font = Enum.Font.Gotham
SpeedLabel.Text = "Auto Speed: " .. autoSpeedVal .. " (Active)"
SpeedLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
SpeedLabel.TextSize = 12

local UICornerSpeed = Instance.new("UICorner")
UICornerSpeed.CornerRadius = UDim.new(0, 6)
UICornerSpeed.Parent = SpeedLabel

print("Script Loaded Successfully!")
