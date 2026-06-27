# HAMZ-Script
Script for Survive The Killer - Delta Executor
-- HAZ - Survive The Killer Full Script
-- Created by HAZRIL

local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hum = char:WaitForChild("Humanoid")
local root = char:WaitForChild("HumanoidRootPart")

-- GUI
local sg = Instance.new("ScreenGui")
sg.Name = "HAZ"
sg.Parent = player.PlayerGui

local f = Instance.new("Frame")
f.Size = UDim2.new(0, 300, 0, 400)
f.Position = UDim2.new(0.5, -150, 0.5, -200)
f.BackgroundColor3 = Color3.fromRGB(15, 15, 25)
f.BackgroundTransparency = 0.2
f.BorderSizePixel = 0
f.Draggable = true
f.Active = true
f.Parent = sg

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 35)
title.BackgroundColor3 = Color3.fromRGB(255, 40, 40)
title.BackgroundTransparency = 0.4
title.Text = "🔥 HAZ v3.0 🔥"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.Parent = f

local function makeBtn(text, pos, color, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9, 0, 0, 35)
    btn.Position = UDim2.new(0.05, 0, pos, 0)
    btn.BackgroundColor3 = color
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.TextScaled = true
    btn.Font = Enum.Font.GothamBold
    btn.Parent = f
    local active = false
    btn.MouseButton1Click:Connect(function()
        active = not active
        callback(active, btn)
    end)
    return btn
end

-- Fitur
local jumpActive = false
makeBtn("🦘 Infinite Jump", 0.12, Color3.fromRGB(30,150,200), function(act, btn)
    jumpActive = act
    btn.BackgroundColor3 = act and Color3.fromRGB(0,200,0) or Color3.fromRGB(30,150,200)
    btn.Text = act and "🦘 JUMP ON" or "🦘 Infinite Jump"
    if act then
        game:GetService("RunService").Stepped:Connect(function()
            if jumpActive and hum then
                hum.JumpPower = 999
                hum:ChangeState(Enum.HumanoidStateType.Jumping)
                task.wait(0.01)
                hum.JumpPower = 50
            end
        end)
    end
end)

local speedActive = false
makeBtn("💨 Speed Hack", 0.26, Color3.fromRGB(150,30,200), function(act, btn)
    speedActive = act
    btn.BackgroundColor3 = act and Color3.fromRGB(0,200,0) or Color3.fromRGB(150,30,200)
    btn.Text = act and "💨 SPEED ON" or "💨 Speed Hack"
    hum.WalkSpeed = act and 50 or 16
end)

local espActive = false
makeBtn("👁️ ESP Player", 0.40, Color3.fromRGB(200,150,30), function(act, btn)
    espActive = act
    btn.BackgroundColor3 = act and Color3.fromRGB(0,200,0) or Color3.fromRGB(200,150,30)
    btn.Text = act and "👁️ ESP ON" or "👁️ ESP Player"
    for _, v in pairs(game:GetService("Players"):GetPlayers()) do
        if v ~= player and v.Character then
            local h = v.Character:FindFirstChild("Highlight")
            if act and not h then
                h = Instance.new("Highlight")
                h.Adornee = v.Character
                h.FillColor = Color3.fromRGB(255,0,0)
                h.FillTransparency = 0.4
                h.OutlineColor = Color3.fromRGB(0,255,0)
                h.Parent = v.Character
            elseif not act and h then
                h:Destroy()
            end
        end
    end
end)

local murderActive = false
makeBtn("🔪 Auto Murderer", 0.54, Color3.fromRGB(200,30,30), function(act, btn)
    murderActive = act
    btn.BackgroundColor3 = act and Color3.fromRGB(0,200,0) or Color3.fromRGB(200,30,30)
    btn.Text = act and "🔪 MURDER ON" or "🔪 Auto Murderer"
    if act then
        for _, v in pairs(game:GetService("Players"):GetPlayers()) do
            if v ~= player and v.Character and v.Character:FindFirstChild("Humanoid") then
                game:GetService("ReplicatedStorage"):FindFirstChild("Remotes"):FindFirstChild("Attack"):FireServer(v, "kill")
            end
        end
    end
end)

local teleBtn = Instance.new("TextButton")
teleBtn.Size = UDim2.new(0.9, 0, 0, 35)
teleBtn.Position = UDim2.new(0.05, 0, 0.68, 0)
teleBtn.BackgroundColor3 = Color3.fromRGB(200,100,50)
teleBtn.Text = "🌀 Teleport Survivor"
teleBtn.TextColor3 = Color3.fromRGB(255,255,255)
teleBtn.TextScaled = true
teleBtn.Font = Enum.Font.GothamBold
teleBtn.Parent = f
teleBtn.MouseButton1Click:Connect(function()
    local near = nil
    local dist = math.huge
    for _, v in pairs(game:GetService("Players"):GetPlayers()) do
        if v ~= player and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
            local d = (root.Position - v.Character.HumanoidRootPart.Position).Magnitude
            if d < dist then dist = d; near = v end
        end
    end
    if near then root.CFrame = near.Character.HumanoidRootPart.CFrame + Vector3.new(0,2,3) end
end)

-- Bypass
local function antiBan()
    local r = game:GetService("ReplicatedStorage"):FindFirstChild("Remotes")
    if r then r.OnClientInvoke = function(...) return true end end
    local ac = game:GetService("Players").LocalPlayer.PlayerScripts:FindFirstChild("AntiCheat")
    if ac then ac:Destroy() end
    game:GetService("ReplicatedStorage").ChildAdded:Connect(function(c)
        if c.Name:lower():find("ban") or c.Name:lower():find("kick") then c:Destroy() end
    end)
end
antiBan()

player.CharacterAdded:Connect(function(c)
    char = c; hum = char:WaitForChild("Humanoid"); root = char:WaitForChild("HumanoidRootPart")
    if speedActive then hum.WalkSpeed = 50 end
    task.wait(0.5); antiBan()
end)

print("✅ HAZ LOADED | Created by HAZRIL")
