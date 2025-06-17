--[[
🎮 Aura Battles Dominator.lua
📱 Compatível: Mobile (Arceus X, Hydrogen, Delta)
🛠 Funções: Fly, Kill Aura, Noclip, ESP, Super Speed, Anti Kick
🧭 Interface: Tabs com botões arrastáveis + Fechar Menu
]]

-- Proteção simples (Anti Kick)
local mt = getrawmetatable(game)
setreadonly(mt, false)
local old = mt.__namecall
mt.__namecall = newcclosure(function(self, ...)
    local args = {...}
    if getnamecallmethod() == "Kick" then
        return nil
    end
    return old(self, unpack(args))
end)

-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local cam = workspace.CurrentCamera
local lp = Players.LocalPlayer

repeat wait() until lp.Character and lp.Character:FindFirstChild("HumanoidRootPart")
local hrp = lp.Character:WaitForChild("HumanoidRootPart")

-- GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "AuraBattlesDominator"
gui.ResetOnSpawn = false

-- Janela principal
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 300, 0, 320)
frame.Position = UDim2.new(0.05, 0, 0.2, 0)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.Active = true
frame.Draggable = true
Instance.new("UICorner", frame)

-- Título
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.BackgroundTransparency = 1
title.Text = "Aura Battles Dominator"
title.Font = Enum.Font.GothamBold
title.TextColor3 = Color3.new(1, 1, 1)
title.TextScaled = true

-- Botão fechar
local closeBtn = Instance.new("TextButton", frame)
closeBtn.Size = UDim2.new(0, 25, 0, 25)
closeBtn.Position = UDim2.new(1, -30, 0, 5)
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.new(1, 0.4, 0.4)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextScaled = true
closeBtn.BackgroundTransparency = 1
closeBtn.MouseButton1Click:Connect(function()
    gui:Destroy()
end)

-- Funções
local fly = false
local aura = false
local noclip = false
local esp = false
local superSpeed = false

local bv = Instance.new("BodyVelocity")
bv.MaxForce = Vector3.new(9e9,9e9,9e9)
bv.P = 12500

-- Botão helper
local function makeButton(txt, pos, func)
	local btn = Instance.new("TextButton", frame)
	btn.Size = UDim2.new(0, 260, 0, 40)
	btn.Position = pos
	btn.Text = txt
	btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
	btn.TextColor3 = Color3.new(1,1,1)
	btn.TextScaled = true
	btn.Font = Enum.Font.GothamBold
	btn.MouseButton1Click:Connect(func)
	Instance.new("UICorner", btn)
	return btn
end

-- Fly + Kill Aura
makeButton("🛫 Fly + Kill Aura", UDim2.new(0, 20, 0, 60), function()
	fly = not fly
	aura = fly
end)

-- Kill Aura Only
makeButton("⚔ Kill Aura Only", UDim2.new(0, 20, 0, 110), function()
	aura = not aura
	fly = false
end)

-- Noclip
makeButton("🧱 Noclip", UDim2.new(0, 20, 0, 160), function()
	noclip = not noclip
end)

-- Super Speed
makeButton("🏃 Super Speed", UDim2.new(0, 20, 0, 210), function()
	superSpeed = not superSpeed
	if superSpeed and lp.Character:FindFirstChildOfClass("Humanoid") then
		lp.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = 90
	else
		lp.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = 16
	end
end)

-- ESP
makeButton("👁 Toggle ESP", UDim2.new(0, 20, 0, 260), function()
	esp = not esp
	for _, plr in ipairs(Players:GetPlayers()) do
		if plr ~= lp then
			local char = plr.Character
			if char then
				local head = char:FindFirstChild("Head")
				if head then
					if esp then
						local bill = Instance.new("BillboardGui", head)
						bill.Name = "ESP"
						bill.Size = UDim2.new(0,100,0,20)
						bill.AlwaysOnTop = true
						local txt = Instance.new("TextLabel", bill)
						txt.Size = UDim2.new(1,0,1,0)
						txt.BackgroundTransparency = 1
						txt.Text = plr.Name
						txt.TextColor3 = Color3.fromRGB(255, 0, 0)
						txt.TextScaled = true
					else
						if head:FindFirstChild("ESP") then
							head:FindFirstChild("ESP"):Destroy()
						end
					end
				end
			end
		end
	end
end)

-- Loop para Fly
RunService.RenderStepped:Connect(function()
	if fly then
		bv.Parent = hrp
		bv.Velocity = cam.CFrame.LookVector * 80
	else
		bv.Parent = nil
	end
end)

-- Kill Aura
spawn(function()
	while task.wait(0.25) do
		if aura then
			for _, p in pairs(Players:GetPlayers()) do
				if p ~= lp and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
					local t = p.Character.HumanoidRootPart
					if (t.Position - hrp.Position).Magnitude < 30 then
						firetouchinterest(hrp, t, 0)
						firetouchinterest(hrp, t, 1)
					end
				end
			end
		end
	end
end)

-- Noclip
RunService.Stepped:Connect(function()
	if noclip and lp.Character then
		for _, part in pairs(lp.Character:GetDescendants()) do
			if part:IsA("BasePart") then
				part.CanCollide = false
			end
		end
	end
end)
