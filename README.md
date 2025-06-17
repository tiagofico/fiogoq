--[[
	🎮 Script: Aura Battles Dominator
	📱 Compatível: Mobile (Arceus X, Hydrogen, Delta)
	🔧 Funções: Fly + Kill Aura automático + Noclip
	✍️ Feito por ChatGPT (OpenAI)
]]

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local cam = workspace.CurrentCamera
local lp = Players.LocalPlayer

repeat wait() until lp.Character and lp.Character:FindFirstChild("HumanoidRootPart")
local hrp = lp.Character:WaitForChild("HumanoidRootPart")

-- GUI Setup
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "AuraBattlesDominatorUI"
gui.ResetOnSpawn = false

local function createButton(name, position, callback, color)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 120, 0, 45)
    btn.Position = position
    btn.BackgroundColor3 = color or Color3.fromRGB(40, 40, 40)
    btn.Text = name
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.TextScaled = true
    btn.Font = Enum.Font.GothamBold
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = true
    btn.Parent = gui
    btn.UICorner = Instance.new("UICorner", btn)
    btn.MouseButton1Click:Connect(callback)
    return btn
end

-- === VARIÁVEIS ===
local flyAuraEnabled = false
local flySpeed = 80
local auraRange = 25
local noclipEnabled = false

-- === BOTÕES ===
local flyBtn = createButton("🛫 Fly+AURA", UDim2.new(0,20,0,100), function()
    flyAuraEnabled = not flyAuraEnabled
    if not flyAuraEnabled then
        bv.Parent = nil
    end
end, Color3.fromRGB(0,170,255))

local auraEnabled = false
local auraBtn = createButton("⚔️ Kill Aura", UDim2.new(0,20,0,160), function()
    flyAuraEnabled = false
    auraEnabled = not auraEnabled
    if auraEnabled then bv.Parent = nil end
end, Color3.fromRGB(255,60,60))

local noclipBtn = createButton("🧱 Noclip", UDim2.new(0,20,0,220), function()
    noclipEnabled = not noclipEnabled
end, Color3.fromRGB(120,255,120))

-- === BODYVELOCITY PARA VOAR ===
local bv = Instance.new("BodyVelocity")
bv.MaxForce = Vector3.new(9e9, 9e9, 9e9)
bv.P = 12500

RunService.RenderStepped:Connect(function()
    if flyAuraEnabled then
        bv.Parent = hrp
        bv.Velocity = cam.CFrame.LookVector * flySpeed
    end
end)

-- === KILL AURA ===
spawn(function()
    while wait(0.2) do
        if flyAuraEnabled or auraEnabled then
            for _, plr in ipairs(Players:GetPlayers()) do
                if plr ~= lp and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                    local targetHRP = plr.Character.HumanoidRootPart
                    local dist = (targetHRP.Position - hrp.Position).Magnitude
                    if dist < auraRange then
                        firetouchinterest(hrp, targetHRP, 0)
                        firetouchinterest(hrp, targetHRP, 1)
                    end
                end
            end
        end
    end
end)

-- === NOCLIP ===
RunService.Stepped:Connect(function()
    if noclipEnabled and lp.Character then
        for _, part in pairs(lp.Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end
end)
