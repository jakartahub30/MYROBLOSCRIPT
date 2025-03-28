local ScreenGui = Instance.new("ScreenGui") local MainFrame = Instance.new("Frame") local ScrollingFrame = Instance.new("ScrollingFrame") local UIListLayout = Instance.new("UIListLayout") local LogoButton = Instance.new("TextButton")

ScreenGui.Name = "JakartaScript" ScreenGui.Parent = game.CoreGui

MainFrame.Name = "MainFrame" MainFrame.Parent = ScreenGui MainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0) MainFrame.Size = UDim2.new(0, 250, 0, 300) MainFrame.Position = UDim2.new(0.5, -125, 0.5, -150) MainFrame.Active = true MainFrame.Draggable = true

ScrollingFrame.Parent = MainFrame ScrollingFrame.Size = UDim2.new(1, 0, 1, 0) ScrollingFrame.CanvasSize = UDim2.new(0, 0, 0, 0) ScrollingFrame.ScrollBarThickness = 8 ScrollingFrame.BackgroundTransparency = 1 ScrollingFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y

UIListLayout.Parent = ScrollingFrame UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder UIListLayout.Padding = UDim.new(0, 5)

LogoButton.Name = "LogoButton" LogoButton.Parent = ScreenGui LogoButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255) LogoButton.Position = UDim2.new(0, 10, 0, 10) LogoButton.Size = UDim2.new(0, 50, 0, 50) LogoButton.Font = Enum.Font.SourceSansBold LogoButton.Text = "JKT" LogoButton.TextSize = 20 LogoButton.TextColor3 = Color3.fromRGB(0, 0, 0) LogoButton.Visible = false

local isVisible = true local toggles = {Speed = false, Invisible = false, JumpPower = false, InfiniteJump = false, NoClip = false, AimLock = false, GodMode = false, AntiAFK = false, KillAura = false, ESP = false}

local function toggleGui() isVisible = not isVisible MainFrame.Visible = isVisible LogoButton.Visible = not isVisible end

local function createButton(name, callback) local Button = Instance.new("TextButton") Button.Parent = ScrollingFrame Button.BackgroundColor3 = Color3.fromRGB(50, 50, 50) Button.Size = UDim2.new(1, 0, 0, 30) Button.Font = Enum.Font.SourceSans Button.Text = name .. " [OFF]" Button.TextColor3 = Color3.fromRGB(255, 255, 255) Button.TextSize = 20 Button.MouseButton1Click:Connect(function() toggles[name] = not toggles[name] Button.Text = name .. (toggles[name] and " [ON]" or " [OFF]") callback(toggles[name]) end) end

local CloseButton = Instance.new("TextButton") CloseButton.Parent = MainFrame CloseButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0) CloseButton.Size = UDim2.new(0, 30, 0, 30) CloseButton.Position = UDim2.new(1, -35, 0, -35) CloseButton.Font = Enum.Font.SourceSansBold CloseButton.Text = "X" CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255) CloseButton.TextSize = 20 CloseButton.MouseButton1Click:Connect(toggleGui)

createButton("Super Speed", function(state) local player = game.Players.LocalPlayer player.Character.Humanoid.WalkSpeed = state and 500 or 16 end)

createButton("Invisible", function(state) local player = game.Players.LocalPlayer for _, part in pairs(player.Character:GetChildren()) do if part:IsA("BasePart") then part.Transparency = state and 1 or 0 if part:FindFirstChild("face") then part.face.Transparency = state and 1 or 0 end end end end)

createButton("Jump Power", function(state) local player = game.Players.LocalPlayer player.Character.Humanoid.JumpPower = state and 150 or 50 end)

game:GetService("UserInputService").JumpRequest:Connect(function() if toggles.InfiniteJump then game.Players.LocalPlayer.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping) end end) createButton("Infinite Jump", function(state) end)

game:GetService("RunService").Stepped:Connect(function() if toggles.NoClip then for _, part in pairs(game.Players.LocalPlayer.Character:GetChildren()) do if part:IsA("BasePart") then part.CanCollide = false end end end end) createButton("NoClip", function(state) end)

createButton("ESP", function(state) for _, player in pairs(game.Players:GetPlayers()) do if player ~= game.Players.LocalPlayer then if state then local highlight = Instance.new("Highlight") highlight.Parent = player.Character highlight.FillColor = Color3.fromRGB(255, 0, 0) highlight.OutlineColor = Color3.fromRGB(255, 255, 255) highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop highlight.Adornee = player.Character else for _, highlight in pairs(player.Character:GetChildren()) do if highlight:IsA("Highlight") then highlight:Destroy() end end end end end end)

local function refreshTeleportButtons() for _, child in pairs(ScrollingFrame:GetChildren()) do if child:IsA("TextButton") and string.find(child.Text, "Teleport ke") then child:Destroy() end end for _, player in pairs(game.Players:GetPlayers()) do if player ~= game.Players.LocalPlayer then createButton("Teleport ke " .. player.Name, function() if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = player.Character.HumanoidRootPart.CFrame end end) end end end

game.Players.PlayerAdded:Connect(function() wait(0.5) refreshTeleportButtons() end)

game.Players.PlayerRemoving:Connect(function() wait(0.5) refreshTeleportButtons() end)

refreshTeleportButtons() LogoButton.MouseButton1Click:Connect(toggleGui)

