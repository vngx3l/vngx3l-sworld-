local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local mouse = LocalPlayer:GetMouse()

-- Clear out the previous layout engine to prevent memory stacking
if LocalPlayer.PlayerGui:FindFirstChild("vngx3l_EmojiConsole") then
    LocalPlayer.PlayerGui.vngx3l_EmojiConsole:Destroy()
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "vngx3l_EmojiConsole"
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

-- Wide row panel
local MainFrame = Instance.new("Frame")
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BorderColor3 = Color3.fromRGB(255, 0, 127)
MainFrame.BorderSizePixel = 2
MainFrame.Position = UDim2.new(0.05, 0, 0.35, 0)
MainFrame.Size = UDim2.new(0, 520, 0, 85)
MainFrame.Active = true
MainFrame.Draggable = true

local FrameCorner = Instance.new("UICorner")
FrameCorner.CornerRadius = UDim.new(0, 6)
FrameCorner.Parent = MainFrame

-- Close button
local CloseButton = Instance.new("TextButton")
CloseButton.Name = "CloseButton"
CloseButton.Parent = MainFrame
CloseButton.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
CloseButton.BorderColor3 = Color3.fromRGB(255, 0, 127)
CloseButton.Position = UDim2.new(0.94, 0, 0.1, 0)
CloseButton.Size = UDim2.new(0, 20, 0, 20)
CloseButton.Font = Enum.Font.SourceSansBold
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.TextSize = 11

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 4)
CloseCorner.Parent = CloseButton

CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)

-- Header label
local TitleLabel = Instance.new("TextLabel")
TitleLabel.Parent = MainFrame
TitleLabel.BackgroundTransparency = 1
TitleLabel.Position = UDim2.new(0.03, 0, 0.1, 0)
TitleLabel.Size = UDim2.new(0, 80, 0, 20)
TitleLabel.Font = Enum.Font.Creepster
TitleLabel.Text = "emojis"
TitleLabel.TextColor3 = Color3.fromRGB(255, 0, 127)
TitleLabel.TextSize = 13
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left

-- Send the selected emoji to chat
local function broadcastEmoji(emojiString)
    pcall(function()
        local chatEvents = game:GetService("ReplicatedStorage"):FindFirstChild("DefaultChatSystemChatEvents")
        if chatEvents and chatEvents:FindFirstChild("SayMessageRequest") then
            chatEvents.SayMessageRequest:FireServer(emojiString, "All")
        else
            local TextChatService = game:GetService("TextChatService")
            if TextChatService and TextChatService:FindFirstChild("TextChannels") and TextChatService.TextChannels:FindFirstChild("RBXGeneral") then
                TextChatService.TextChannels.RBXGeneral:SendAsync(emojiString)
            end
        end
    end)
end

-- Emoji buttons
local shortcuts = {"ð¤¡","ð¥","ð¤¦ââï¸ ", "ð¤¨", "ð¤£", "ð", "ð¬", "ð"}
local startX = 0.03
local spacingX = 0.11

for i, emojiText in ipairs(shortcuts) do
    local btn = Instance.new("TextButton")
    btn.Parent = MainFrame
    btn.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
    btn.BorderColor3 = Color3.fromRGB(255, 0, 127)
    btn.Position = UDim2.new(startX + ((i - 1) * spacingX), 0, 0.46, 0)
    btn.Size = UDim2.new(0, 36, 0, 32)
    btn.Font = Enum.Font.SourceSansBold
    btn.Text = emojiText
    btn.TextSize = 16
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)

    local BtnCorner = Instance.new("UICorner")
    BtnCorner.CornerRadius = UDim.new(0, 4)
    BtnCorner.Parent = btn

    btn.MouseButton1Click:Connect(function()
        broadcastEmoji(btn.Text)
    end)
end

-- Rainbow outline and hover transparency
RunService.Heartbeat:Connect(function()
    if not MainFrame or not MainFrame.Parent then return end

    local hue = (tick() % 4) / 4
    local rainbowColor = Color3.fromHSV(hue, 1, 1)
    MainFrame.BorderColor3 = rainbowColor
    TitleLabel.TextColor3 = rainbowColor
    CloseButton.BorderColor3 = rainbowColor

    local mX = mouse.X
    local mY = mouse.Y
    local absPos = MainFrame.AbsolutePosition
    local absSize = MainFrame.AbsoluteSize
    local padding = 45

    local inside = mX >= (absPos.X - padding) and mX <= (absPos.X + absSize.X + padding) and
                   mY >= (absPos.Y - padding) and mY <= (absPos.Y + absSize.Y + padding)

    if inside then
        MainFrame.BackgroundTransparency = 0.5
        TitleLabel.TextTransparency = 0
        CloseButton.BackgroundTransparency = 0.5
        CloseButton.TextTransparency = 0

        for _, child in pairs(MainFrame:GetChildren()) do
            if child:IsA("TextButton") and child.Name ~= "CloseButton" then
                child.BackgroundTransparency = 0.5
                child.TextTransparency = 0
                child.BorderColor3 = rainbowColor
            end
        end
    else
        MainFrame.BackgroundTransparency = 1
        TitleLabel.TextTransparency = 1
        CloseButton.BackgroundTransparency = 1
        CloseButton.TextTransparency = 1

        for _, child in pairs(MainFrame:GetChildren()) do
            if child:IsA("TextButton") and child.Name ~= "CloseButton" then
                child.BackgroundTransparency = 1
                child.TextTransparency = 1
                child.BorderColor3 = Color3.fromRGB(0, 0, 0)
            end
        end
    end
end)

print("vngx3l$ Extended 50% Transparent Emoji Engine initialized successfully.")
