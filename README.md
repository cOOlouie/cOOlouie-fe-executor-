# cOOlouie-fe-executor-
cOOlouie executor is free executor and a beta test risk it for your self!
local Players = game:GetService("Players")
local HttpService = game:GetService("HttpService")
local StarterGui = game:GetService("StarterGui")
local LocalPlayer = Players.LocalPlayer

local function sendNotification(title, text, icon)
    pcall(function()
        StarterGui:SetCore("SendNotification", {
            Title = title,
            Text = text,
            Icon = icon,
            Duration = 5
        })
    end)
end

sendNotification("Thank You!", "Thank you for using cOOLouie FE Executor. Enjoy!", "")

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game:GetService("CoreGui")

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 400, 0, 300)
Frame.Position = UDim2.new(0.5, -200, 0.5, -150)
Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Frame.BorderSizePixel = 2
Frame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0.12, 0)
Title.Position = UDim2.new(0, 0, 0, 0)
Title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
Title.BorderSizePixel = 0
Title.Text = "cOOLouie FE Executor"
Title.TextSize = 22
Title.Font = Enum.Font.SourceSansBold
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextStrokeTransparency = 0.5
Title.TextScaled = true
Title.Parent = Frame

spawn(function()
    while true do
        for i = 0, 255, 5 do
            Title.TextColor3 = Color3.fromHSV(i / 255, 1, 1)
            wait(0.05)
        end
    end
end)

local ExecutorTab = Instance.new("TextButton")
ExecutorTab.Size = UDim2.new(0.5, 0, 0.1, 0)
ExecutorTab.Position = UDim2.new(0, 0, 0.85, 0)
ExecutorTab.Text = "Executor"
ExecutorTab.Parent = Frame

local PlayerInfoTab = Instance.new("TextButton")
PlayerInfoTab.Size = UDim2.new(0.5, 0, 0.1, 0)
PlayerInfoTab.Position = UDim2.new(0.5, 0, 0.85, 0)
PlayerInfoTab.Text = "Player Info"
PlayerInfoTab.Parent = Frame

local ExecutorBox = Instance.new("TextBox")
ExecutorBox.Size = UDim2.new(1, -10, 0.55, 0)
ExecutorBox.Position = UDim2.new(0, 5, 0.15, 0)
ExecutorBox.ClearTextOnFocus = false
ExecutorBox.Text = "-- Paste script here"
ExecutorBox.Parent = Frame

local ExecuteButton = Instance.new("TextButton")
ExecuteButton.Size = UDim2.new(1, -10, 0.1, 0)
ExecuteButton.Position = UDim2.new(0, 5, 0.72, 0)
ExecuteButton.Text = "Execute"
ExecuteButton.Parent = Frame

ExecuteButton.MouseButton1Click:Connect(function()
    local scriptToRun = ExecutorBox.Text
    if scriptToRun and scriptToRun ~= "" then
        local success, err = pcall(function()
            loadstring(game:HttpGet(scriptToRun, true))()
        end)
        if not success then
            warn("Execution Error: " .. tostring(err))
        end
    end
end)

local PlayerInfo = Instance.new("TextLabel")
PlayerInfo.Size = UDim2.new(1, -10, 0.55, 0)
PlayerInfo.Position = UDim2.new(0, 5, 0.15, 0)
PlayerInfo.Text = "Fetching Player Info..."
PlayerInfo.Visible = false
PlayerInfo.Parent = Frame

ExecutorTab.MouseButton1Click:Connect(function()
    ExecutorBox.Visible = true
    ExecuteButton.Visible = true
    PlayerInfo.Visible = false
end)

PlayerInfoTab.MouseButton1Click:Connect(function()
    ExecutorBox.Visible = false
    ExecuteButton.Visible = false
    PlayerInfo.Visible = true
end)

local HideButton = Instance.new("TextButton")
HideButton.Size = UDim2.new(0.2, 0, 0.08, 0)
HideButton.Position = UDim2.new(0.8, 0, 0.92, 0)
HideButton.Text = "Hide"
HideButton.Parent = Frame

HideButton.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
end)

sendNotification("FE Bypass", "Injecting FE Bypass...", "")

spawn(function()
    local success = pcall(function()
        local replicator = game:GetService("ReplicatedStorage"):FindFirstChild("Replicator")
        if replicator then
            local response = replicator:InvokeServer("ReplicationRequest", "auth_ticket", "{}")
            return response and response:lower():find("success")
        end
        return false
    end)

    if success then
        sendNotification("FE Bypass Loaded", "FE Bypass successfully loaded! Enjoy!", "")
        print("[cOOLouie]: FE Bypassed.")
    else
        print("[cOOLouie]: FE Bypass failed.")
    end
end)

spawn(function()
    local success, response = pcall(function()
        return HttpService:GetAsync("https://users.roblox.com/v1/users/" .. LocalPlayer.UserId)
    end)

    if success then
        local data = HttpService:JSONDecode(response)
        PlayerInfo.Text = "Username: " .. LocalPlayer.Name ..
                          "\nUser ID: " .. LocalPlayer.UserId ..
                          "\nDisplay Name: " .. LocalPlayer.DisplayName ..
                          "\nCountry: " .. (data.location and data.location.country or "Unknown")
    else
        PlayerInfo.Text = "Failed to fetch player info."
    end
end)
