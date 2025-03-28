local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MainGui"
screenGui.Parent = playerGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 200, 0, 180)
frame.Position = UDim2.new(0.5, -100, 0.5, -90)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0
frame.Parent = screenGui

local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0, 10)
uiCorner.Parent = frame

local flyActive = false
local flySpeed = 50
local connection

local userInput = game:GetService("UserInputService")
local runService = game:GetService("RunService")

local function startFlying()
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
    
    local bodyGyro = Instance.new("BodyGyro")
    bodyGyro.CFrame = humanoidRootPart.CFrame
    bodyGyro.Parent = humanoidRootPart
    
    local bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    bodyVelocity.Parent = humanoidRootPart
    
    connection = runService.RenderStepped:Connect(function()
        local camera = workspace.CurrentCamera
        local moveDirection = Vector3.zero
        
        if userInput:IsKeyDown(Enum.KeyCode.W) then
            moveDirection = moveDirection + camera.CFrame.LookVector
        end
        if userInput:IsKeyDown(Enum.KeyCode.S) then
            moveDirection = moveDirection - camera.CFrame.LookVector
        end
        if userInput:IsKeyDown(Enum.KeyCode.A) then
            moveDirection = moveDirection - camera.CFrame.RightVector
        end
        if userInput:IsKeyDown(Enum.KeyCode.D) then
            moveDirection = moveDirection + camera.CFrame.RightVector
        end
        if userInput:IsKeyDown(Enum.KeyCode.Space) then
            moveDirection = moveDirection + Vector3.new(0, 1, 0)
        end
        if userInput:IsKeyDown(Enum.KeyCode.LeftShift) then
            moveDirection = moveDirection - Vector3.new(0, 1, 0)
        end

        if moveDirection.Magnitude > 0 then
            bodyVelocity.Velocity = moveDirection.Unit * flySpeed
        else
            bodyVelocity.Velocity = Vector3.zero
        end

        bodyGyro.CFrame = camera.CFrame
    end)
end

local function stopFlying()
    if connection then connection:Disconnect() end
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

    for _, v in pairs(humanoidRootPart:GetChildren()) do
        if v:IsA("BodyGyro") or v:IsA("BodyVelocity") then
            v:Destroy()
        end
    end
end

local function createToggleButton(name, position, onToggle)
    local toggle = Instance.new("TextButton")
    toggle.Size = UDim2.new(0, 120, 0, 40)
    toggle.Position = UDim2.new(0, position.X, 0, position.Y)
    toggle.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    toggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    toggle.Font = Enum.Font.SourceSansBold
    toggle.TextSize = 20
    toggle.Text = name .. " [OFF]"
    toggle.Parent = frame
    
    local isActive = false
    
    toggle.MouseButton1Click:Connect(function()
        isActive = not isActive
        toggle.BackgroundColor3 = isActive and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(100, 100, 100)
        toggle.Text = name .. (isActive and " [ON]" or " [OFF]")
        onToggle(isActive)
    end)
end

createToggleButton("Fly", Vector2.new(40, 10), function(isActive)
    flyActive = isActive
    if flyActive then
        startFlying()
    else
        stopFlying()
    end
end)

createToggleButton("Speed", Vector2.new(40, 60), function(isActive)
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    humanoid.WalkSpeed = isActive and 50 or 16
end)

createToggleButton("Invisible", Vector2.new(40, 110), function(isActive)
    local character = player.Character or player.CharacterAdded:Wait()
    for _, part in pairs(character:GetChildren()) do
        if part:IsA("BasePart") then
            part.Transparency = isActive and 1 or 0
            part.CanCollide = not isActive
        end
    end
end)