local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local ScrollingFrame = Instance.new("ScrollingFrame")
local UIListLayout = Instance.new("UIListLayout")
local LogoButton = Instance.new("TextButton")

ScreenGui.Name = "JakartaScript"
ScreenGui.Parent = game.CoreGui

MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
MainFrame.Size = UDim2.new(0, 250, 0, 300)
MainFrame.Position = UDim2.new(0.5, -125, 0.5, -150)
MainFrame.Active = true
MainFrame.Draggable = true

ScrollingFrame.Parent = MainFrame
ScrollingFrame.Size = UDim2.new(1, 0, 1, 0)
ScrollingFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
ScrollingFrame.ScrollBarThickness = 8
ScrollingFrame.BackgroundTransparency = 1
ScrollingFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y

UIListLayout.Parent = ScrollingFrame
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 5)

LogoButton.Name = "LogoButton"
LogoButton.Parent = ScreenGui
LogoButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
LogoButton.Position = UDim2.new(0, 10, 0, 10)
LogoButton.Size = UDim2.new(0, 50, 0, 50)
LogoButton.Font = Enum.Font.SourceSansBold
LogoButton.Text = "JKT"
LogoButton.TextSize = 20
LogoButton.TextColor3 = Color3.fromRGB(0, 0, 0)
LogoButton.Visible = false
LogoButton.ZIndex = 10

local isVisible = true
local toggles = {
    Speed = false,
    Invisible = false,
    JumpPower = false,
    InfiniteJump = false,
    NoClip = false,
    GodMode = false,
    AntiAFK = false,
    KillAura = false,
    ESP = false
}

local ESPObjects = {}
local connections = {}

local function toggleGui()
    isVisible = not isVisible
    MainFrame.Visible = isVisible
    LogoButton.Visible = not isVisible
    LogoButton.Active = not isVisible
end

local function createButton(name, callback)
    local Button = Instance.new("TextButton")
    Button.Parent = ScrollingFrame
    Button.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    Button.Size = UDim2.new(1, 0, 0, 30)
    Button.Font = Enum.Font.SourceSans
    Button.Text = name .. " [OFF]"
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.TextSize = 20
    Button.MouseButton1Click:Connect(function()
        toggles[name] = not toggles[name]
        Button.Text = name .. (toggles[name] and " [ON]" or " [OFF]")
        callback(toggles[name])
    end)
end

local CloseButton = Instance.new("TextButton")
CloseButton.Parent = MainFrame
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -35, 0, -35)
CloseButton.Font = Enum.Font.SourceSansBold
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.TextSize = 20
CloseButton.MouseButton1Click:Connect(toggleGui)

createButton("Super Speed", function(state)
    local player = game.Players.LocalPlayer
    player.Character.Humanoid.WalkSpeed = state and 500 or 16
end)

createButton("Invisible", function(state)
    local player = game.Players.LocalPlayer
    for _, part in pairs(player.Character:GetChildren()) do
        if part:IsA("BasePart") then
            part.Transparency = state and 1 or 0
            if part:FindFirstChild("face") then
                part.face.Transparency = state and 1 or 0
            end
        end
    end
end)

createButton("Jump Power", function(state)
    local player = game.Players.LocalPlayer
    player.Character.Humanoid.JumpPower = state and 150 or 50
end)

local infiniteJumpConnection
createButton("Infinite Jump", function(state)
    if state then
        infiniteJumpConnection = game:GetService("UserInputService").JumpRequest:Connect(function()
            game.Players.LocalPlayer.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
        end)
    elseif infiniteJumpConnection then
        infiniteJumpConnection:Disconnect()
    end
end)

local noClipConnection
createButton("NoClip", function(state)
    if state then
        noClipConnection = game:GetService("RunService").Stepped:Connect(function()
            for _, part in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
                if part:IsA("BasePart") then
                    part.CanCollide = false
                end
            end
        end)
    elseif noClipConnection then
        noClipConnection:Disconnect()
    end
end)

createButton("God Mode", function(state)
    game.Players.LocalPlayer.Character.Humanoid.MaxHealth = state and math.huge or 100
end)

local antiAFKConnection
createButton("Anti AFK", function(state)
    if state then
        antiAFKConnection = game:GetService("Players").LocalPlayer.Idled:Connect(function()
            game.VirtualUser:CaptureController()
            game.VirtualUser:ClickButton2(Vector2.new())
        end)
    elseif antiAFKConnection then
        antiAFKConnection:Disconnect()
    end
end)

local killAuraConnection
createButton("Kill Aura", function(state)
    if state then
        killAuraConnection = game:GetService("RunService").Stepped:Connect(function()
            for _, player in pairs(game.Players:GetPlayers()) do
                if player ~= game.Players.LocalPlayer and player.Character and player.Character:FindFirstChild("Humanoid") then
                    player.Character.Humanoid.Health = 0
                end
            end
        end)
    elseif killAuraConnection then
        killAuraConnection:Disconnect()
    end
end)

local function createESP(player)
    if toggles.ESP and player ~= game.Players.LocalPlayer then
        if player.Character and player.Character:FindFirstChild("Head") then
            local esp = Instance.new("BillboardGui")
            esp.Adornee = player.Character.Head
            esp.Size = UDim2.new(0, 50, 0, 20)
            esp.StudsOffset = Vector3.new(0, 1.5, 0)
            esp.AlwaysOnTop = true
            esp.Parent = player.Character.Head

            local label = Instance.new("TextLabel")
            label.Parent = esp
            label.Size = UDim2.new(1, 0, 1, 0)
            label.BackgroundTransparency = 1
            label.Text = player.Name
            label.TextColor3 = Color3.fromRGB(255, 0, 0)
            label.TextSize = 14
            label.TextStrokeTransparency = 0.5
            label.TextScaled = true
            
            table.insert(ESPObjects, esp)
        end
    end
end

createButton("ESP", function(state)
    for _, esp in pairs(ESPObjects) do
        esp:Destroy()
    end
    ESPObjects = {}
    
    if state then
        for _, player in pairs(game.Players:GetPlayers()) do
            createESP(player)
        end
    end
end)

for _, player in pairs(game.Players:GetPlayers()) do
    createESP(player)
end

game.Players.PlayerAdded:Connect(createESP)

LogoButton.MouseButton1Click:Connect(toggleGui)