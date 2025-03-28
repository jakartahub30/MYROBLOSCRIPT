local ScreenGui = Instance.new("ScreenGui") local MainFrame = Instance.new("Frame") local ScrollingFrame = Instance.new("ScrollingFrame") local UIListLayout = Instance.new("UIListLayout") local LogoButton = Instance.new("TextButton") local Bar = Instance.new("Frame") local Title = Instance.new("TextLabel")

ScreenGui.Name = "JakartaScript" ScreenGui.Parent = game.CoreGui

MainFrame.Name = "MainFrame" MainFrame.Parent = ScreenGui MainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0) MainFrame.Size = UDim2.new(0, 250, 0, 300) MainFrame.Position = UDim2.new(0.5, -125, 0.5, -150) MainFrame.Active = true MainFrame.Draggable = true

Bar.Parent = MainFrame Bar.BackgroundColor3 = Color3.fromRGB(30, 30, 30) Bar.Size = UDim2.new(1, 0, 0, 25)

Title.Parent = Bar Title.Text = "JAKARTA HUB | RIP_KOMPOS" Title.Size = UDim2.new(1, 0, 1, 0) Title.TextColor3 = Color3.fromRGB(255, 255, 255) Title.Font = Enum.Font.SourceSansBold Title.TextSize = 16 Title.BackgroundTransparency = 1

ScrollingFrame.Parent = MainFrame ScrollingFrame.Size = UDim2.new(1, 0, 1, -25) ScrollingFrame.Position = UDim2.new(0, 0, 0, 25) ScrollingFrame.CanvasSize = UDim2.new(0, 0, 0, 0) ScrollingFrame.ScrollBarThickness = 8 ScrollingFrame.BackgroundTransparency = 1 ScrollingFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y

UIListLayout.Parent = ScrollingFrame UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder UIListLayout.Padding = UDim.new(0, 5)

LogoButton.Name = "LogoButton" LogoButton.Parent = ScreenGui LogoButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255) LogoButton.Position = UDim2.new(0, 10, 0, 10) LogoButton.Size = UDim2.new(0, 50, 0, 50) LogoButton.Font = Enum.Font.SourceSansBold LogoButton.Text = "JKT" LogoButton.TextSize = 20 LogoButton.TextColor3 = Color3.fromRGB(0, 0, 0) LogoButton.Visible = false

local isVisible = true local toggles = { Speed = false, Invisible = false, JumpPower = false, InfiniteJump = false, NoClip = false, AimLock = false, GodMode = false, AntiAFK = false, KillAura = false, ESP = false, Gravity = false }

local function toggleGui() isVisible = not isVisible MainFrame.Visible = isVisible LogoButton.Visible = not isVisible end

local function createButton(name, callback) local Button = Instance.new("TextButton") Button.Parent = ScrollingFrame Button.BackgroundColor3 = Color3.fromRGB(50, 50, 50) Button.Size = UDim2.new(1, 0, 0, 30) Button.Font = Enum.Font.SourceSans Button.Text = name .. " [OFF]" Button.TextColor3 = Color3.fromRGB(255, 255, 255) Button.TextSize = 20 Button.MouseButton1Click:Connect(function() toggles[name] = not toggles[name] Button.Text = name .. (toggles[name] and " [ON]" or " [OFF]") callback(toggles[name]) end) end

local CloseButton = Instance.new("TextButton") CloseButton.Parent = MainFrame CloseButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0) CloseButton.Size = UDim2.new(0, 30, 0, 30) CloseButton.Position = UDim2.new(1, -35, 0, -35) CloseButton.Font = Enum.Font.SourceSansBold CloseButton.Text = "X" CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255) CloseButton.TextSize = 20 CloseButton.MouseButton1Click:Connect(toggleGui)

local function updateESP() for _, player in pairs(game.Players:GetPlayers()) do if player ~= game.Players.LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then if toggles.ESP then if not player.Character.HumanoidRootPart:FindFirstChild("ESP") then local billboard = Instance.new("BillboardGui") billboard.Name = "ESP" billboard.Parent = player.Character.HumanoidRootPart billboard.Size = UDim2.new(0, 100, 0, 50) billboard.AlwaysOnTop = true

local label = Instance.new("TextLabel")
                label.Parent = billboard
                label.Size = UDim2.new(1, 0, 1, 0)
                label.Text = player.Name
                label.TextColor3 = Color3.fromRGB(255, 0, 0)
                label.BackgroundTransparency = 1
                label.TextSize = 14
            end
        else
            if player.Character.HumanoidRootPart:FindFirstChild("ESP") then
                player.Character.HumanoidRootPart.ESP:Destroy()
            end
        end
    end
end

end

createButton("ESP", function(state) updateESP() end)

game.Players.PlayerAdded:Connect(updateESP) game.Players.PlayerRemoving:Connect(updateESP) LogoButton.MouseButton1Click:Connect(toggleGui)

