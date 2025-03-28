local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local ScrollingFrame = Instance.new("ScrollingFrame")
local UIListLayout = Instance.new("UIListLayout")
local LogoButton = Instance.new("TextButton")

local HttpService = game:GetService("HttpService")
local TeleportService = game:GetService("TeleportService")
local PlaceId = game.PlaceId

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

local isVisible = true

local function toggleGui()
    isVisible = not isVisible
    MainFrame.Visible = isVisible
    LogoButton.Visible = not isVisible
end

local function createButton(name, callback)
    local Button = Instance.new("TextButton")
    Button.Parent = ScrollingFrame
    Button.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    Button.Size = UDim2.new(1, 0, 0, 30)
    Button.Font = Enum.Font.SourceSans
    Button.Text = name
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.TextSize = 20
    Button.MouseButton1Click:Connect(callback)
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

-- 🔥 Server Hub dengan daftar pemain
local function createServerHub()
    local servers = HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/" .. PlaceId .. "/servers/Public?sortOrder=Desc&limit=100")).data
    
    for _, server in pairs(servers) do
        if server.id ~= game.JobId and server.playing < server.maxPlayers then
            local serverInfo = "Join Server (" .. server.playing .. "/" .. server.maxPlayers .. ")"
            
            -- 🔎 Ambil daftar pemain di server
            local playerList = ""
            for _, player in pairs(server.playerIds or {}) do
                local success, name = pcall(function()
                    return game.Players:GetNameFromUserIdAsync(player)
                end)
                if success and name then
                    playerList = playerList .. name .. ", "
                end
            end
            
            if #playerList > 0 then
                playerList = playerList:sub(1, -3) -- Buang koma terakhir
                serverInfo = serverInfo .. "\n[" .. playerList .. "]"
            end
            
            createButton(serverInfo, function()
                TeleportService:TeleportToPlaceInstance(PlaceId, server.id, game.Players.LocalPlayer)
            end)
        end
    end
end

createButton("Server Hub", function()
    createServerHub()
end)

LogoButton.MouseButton1Click:Connect(toggleGui)