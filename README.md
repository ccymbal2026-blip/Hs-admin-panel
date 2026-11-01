lua
--========================================
--          HS ADMIN PANEL
--          ONE SCRIPT VERSION
--========================================

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- CREATE REMOTE
local remote = Instance.new("RemoteEvent")
remote.Name = "HSAdminRemote"
remote.Parent = ReplicatedStorage

-- SERVER COMMANDS
remote.OnServerEvent:Connect(function(player, command, value)

    local character = player.Character
    if not character then return end

    local humanoid = character:FindFirstChildOfClass("Humanoid")
    if not humanoid then return end

    command = string.lower(tostring(command))
    value = tonumber(value)

    if command == "speed" then
        humanoid.WalkSpeed = math.clamp(value or 16, 0, 100)

    elseif command == "jump" then
        humanoid.UseJumpPower = true
        humanoid.JumpPower = math.clamp(value or 50, 0, 200)

    elseif command == "heal" then
        humanoid.Health = humanoid.MaxHealth

    elseif command == "reset" then
        humanoid.Health = 0

    elseif command == "respawn" then
        player:LoadCharacter()

    elseif command == "sit" then
        humanoid.Sit = true

    elseif command == "freeze" then
        local root = character:FindFirstChild("HumanoidRootPart")
        if root then root.Anchored = true end

    elseif command == "unfreeze" then
        local root = character:FindFirstChild("HumanoidRootPart")
        if root then root.Anchored = false end
    end
end)

-- CREATE CLIENT SCRIPT
local client = Instance.new("LocalScript")
client.Name = "HSAdminClient"

client.Source = [==[

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RS = game:GetService("ReplicatedStorage")

local player = Players.LocalPlayer
local remote = RS:WaitForChild("HSAdminRemote")

-- GUI
local gui = Instance.new("ScreenGui")
gui.Name = "HSAdminPanel"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 400, 0, 330)
frame.Position = UDim2.new(0.5, -200, 0.5, -165)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
frame.Visible = false
frame.Parent = gui

Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 15)

-- TITLE
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 50)
title.BackgroundColor3 = Color3.fromRGB(105, 55, 200)
title.Text = "HS ADMIN PANEL"
title.TextColor3 = Color3.new(1,1,1)
title.TextSize = 23
title.Font = Enum.Font.GothamBold
title.Parent = frame

Instance.new("UICorner", title).CornerRadius = UDim.new(0, 15)

-- COMMAND BARS
local function makeBar(y, placeholder)

    local bar = Instance.new("TextBox")
    bar.Size = UDim2.new(0.85, 0, 0, 42)
    bar.Position = UDim2.new(0.075, 0, 0, y)
    bar.BackgroundColor3 = Color3.fromRGB(45,45,60)
    bar.TextColor3 = Color3.new(1,1,1)
    bar.PlaceholderText = placeholder
    bar.Text = ""
    bar.TextSize = 16
    bar.Font = Enum.Font.Gotham
    bar.ClearTextOnFocus = false
    bar.Parent = frame

    Instance.new("UICorner", bar).CornerRadius = UDim.new(0,8)

    return bar
end

local bar1 = makeBar(75, "Command Bar 1...")
local bar2 = makeBar(135, "Command Bar 2...")

-- EXECUTE COMMAND
local function execute(text)

    local args = string.split(text, " ")

    if args[1] and args[1] ~= "" then
        remote:FireServer(args[1], args[2])
    end

end

-- BUTTONS
local function makeButton(text, y, callback)

    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0.85, 0, 0, 40)
    button.Position = UDim2.new(0.075, 0, 0, y)
    button.BackgroundColor3 = Color3.fromRGB(105,55,200)
    button.Text = text
    button.TextColor3 = Color3.new(1,1,1)
    button.Font = Enum.Font.GothamBold
    button.TextSize = 15
    button.Parent = frame

    Instance.new("UICorner", button).CornerRadius = UDim.new(0,8)

    button.MouseButton1Click:Connect(callback)

end

makeButton("EXECUTE BAR 1", 195, function()
    execute(bar1.Text)
end)

makeButton("EXECUTE BAR 2", 245, function()
    execute(bar2.Text)
end)

-- TOGGLE WITH !
local function toggle()
    frame.Visible = not frame.Visible
end

UIS.InputBegan:Connect(function(input, processed)

    if processed then return end

    if input.KeyCode == Enum.KeyCode.One
    and (UIS:IsKeyDown(Enum.KeyCode.LeftShift)
    or UIS:IsKeyDown(Enum.KeyCode.RightShift)) then

        toggle()

    end

end)

-- DRAG PANEL
local dragging = false
local dragStart
local startPos

title.InputBegan:Connect(function(input)

    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = frame.Position
    end

end)

UIS.InputChanged:Connect(function(input)

    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then

        local delta = input.Position - dragStart

        frame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )

    end

end)

UIS.InputEnded:Connect(function(input)

    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end

end)

]==]

client.Parent = game:GetService("StarterPlayer"):WaitForChild("StarterPlayerScripts")

print("HS ADMIN PANEL LOADED!")
