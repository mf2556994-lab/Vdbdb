-- ==============================================================================
-- ZYCK HUB OTM
-- ==============================================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-------------------------------------------------
-- CHARACTER CACHE
-------------------------------------------------
local Character, Humanoid, RootPart

local function UpdateChar()
	Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
	Humanoid = Character:WaitForChild("Humanoid")
	RootPart = Character:WaitForChild("HumanoidRootPart")
end
UpdateChar()
LocalPlayer.CharacterAdded:Connect(UpdateChar)

-------------------------------------------------
-- SETTINGS
-------------------------------------------------
local Settings = {
	PainelAberto = true,
	OTIMIZAÇÃO = false,
	OTIMIZACAO NÍVEL = 50,
	DAR REACH = false,
	PingReducer = false
}

local OTIMIZACAO NÍVEIS = {50, 100, 120, 150, 200}
local LevelIndex = 1

-------------------------------------------------
-- FPS / PING
-------------------------------------------------
pcall(function()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end)

local function OptimizePing(state)
	if state then
		settings().Physics.AllowSleep = false
		settings().Physics.PhysicsEnvironmentalThrottle = Enum.EnviromentalPhysicsThrottle.Disabled
	end
end

-------------------------------------------------
-- NO COLLIDE SISTEMA
-------------------------------------------------
local function SetCollision(char, enabled)
	for _, part in ipairs(char:GetDescendants()) do
		if part:IsA("BasePart") then
			part.CanCollide = enabled
			part.CanTouch = enabled
			part.CanQuery = enabled
		end
	end
end

local function UltraAtravessar()
	if not Settings.Atravessar then return end
	if not RootPart then return end

	for _, plr in ipairs(Players:GetPlayers()) do
		if plr ~= LocalPlayer then
			local char = plr.Character
			if char and char:FindFirstChild("HumanoidRootPart") then
				SetCollision(char, false)

				local dist = (char.HumanoidRootPart.Position - RootPart.Position).Magnitude
				if dist < Settings.NoCollideNivel then
					char.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0,0,0)
				end
			end
		end
	end
end

local function RestaurarTudo()
	for _, plr in ipairs(Players:GetPlayers()) do
		if plr ~= LocalPlayer and plr.Character then
			SetCollision(plr.Character, true)
		end
	end
end

-------------------------------------------------
-- DESARME AUTO
-------------------------------------------------
local function AutoDesarme()
	if not RootPart then return end

	local bola = workspace:FindFirstChild("Ball") 
	or workspace:FindFirstChild("Football") 
	or workspace:FindFirstChild("SoccerBall")

	if not bola then return end

	for _, plr in ipairs(Players:GetPlayers()) do
		if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
			local dist = (plr.Character.HumanoidRootPart.Position - RootPart.Position).Magnitude
			if dist < 8 then
				bola.CFrame = RootPart.CFrame * CFrame.new(0, 0, -2)
			end
		end
	end
end

-------------------------------------------------
-- GUI
-------------------------------------------------
local gui = Instance.new("ScreenGui")
gui.Name = "bndanike77GUI"
gui.Parent = game:GetService("CoreGui")

local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 240, 0, 300) -- Altura ajustada para caber os botões novos
main.Position = UDim2.new(0.05, 0, 0.25, 0)
main.BackgroundColor3 = Color3.fromRGB(25,25,25)
main.Active = true
main.Draggable = true
main.Visible = Settings.PainelAberto

local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1,0,0,35)
title.Text = "zyck"
title.TextColor3 = Color3.new(1,1,1)
title.BackgroundColor3 = Color3.fromRGB(45,45,45)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 16

-------------------------------------------------
-- BUTTON CREATORS
-------------------------------------------------
local function CreateButton(text, order, callback)
	local btn = Instance.new("TextButton", main)
	btn.Size = UDim2.new(1,-20,0,30)
	btn.Position = UDim2.new(0,10,0,40 + order*35)
	btn.BackgroundColor3 = Color3.fromRGB(40,40,40)
	btn.TextColor3 = Color3.new(1,1,1)
	btn.Font = Enum.Font.SourceSans
	btn.TextSize = 14
	btn.Text = text.." [OFF]"

	btn.MouseButton1Click:Connect(function()
		Settings[text] = not Settings[text]
		btn.Text = text.." ["..(Settings[text] and "ON" or "OFF").."]"
		if callback then callback(Settings[text]) end
	end)
end

-- Função para criar botões de Execução Única (Scripts Externos)
local function CreateExecuteButton(text, order, callback)
	local btn = Instance.new("TextButton", main)
	btn.Size = UDim2.new(1,-20,0,30)
	btn.Position = UDim2.new(0,10,0,40 + order*35)
	btn.BackgroundColor3 = Color3.fromRGB(30,60,100)
	btn.TextColor3 = Color3.new(1,1,1)
	btn.Font = Enum.Font.SourceSansBold
	btn.TextSize = 14
	btn.Text = "▶ " .. text

	btn.MouseButton1Click:Connect(function()
		btn.Text = "✔ " .. text .. " (Injetado)"
		btn.BackgroundColor3 = Color3.fromRGB(40, 100, 40)
		if callback then callback() end
	end)
end

-------------------------------------------------
-- BOTÕES
-------------------------------------------------
CreateButton("OTIMIZACAO MT BOA", 0, function(state)
	if not state then RestaurarTudo() end
end)

local levelBtn = Instance.new("TextButton", main)
levelBtn.Size = UDim2.new(1,-20,0,30)
levelBtn.Position = UDim2.new(0,10,0,40 + 1*35)
levelBtn.BackgroundColor3 = Color3.fromRGB(60,60,60)
levelBtn.TextColor3 = Color3.new(1,1,1)
levelBtn.Font = Enum.Font.SourceSansBold
levelBtn.TextSize = 14
levelBtn.Text = "OTIMIZAÇÃO NÍVEL : "..Settings.NoCollideNivel

levelBtn.MouseButton1Click:Connect(function()
	LevelIndex += 1
	if LevelIndex > #NoCollideLevels then LevelIndex = 1 end
	Settings.NoCollideNivel = NoCollideLevels[LevelIndex]
	levelBtn.Text = "No Collide: "..Settings.NoCollideNivel
end)

CreateButton("DAR REACH", 2)

CreateButton("PingReducer", 3, function(state)
	OptimizePing(state)
end)

-- NOVAS FUNÇÕES
CreateExecuteButton("Otimização Suprema", 4, function()
	loadstring(game:HttpGet("https://files.catbox.moe/h52znd.txt"))()
end)

CreateExecuteButton("Correr Automático", 5, function()
	loadstring([[
		-- By zyck - Otimizacao do zyck com Botão Arrastável + Espuma Controlada (Rate 12)
		local Players = game:GetService("Players")
		local RunService = game:GetService("RunService")
		local UserInputService = game:GetService("UserInputService")
		local player = Players.LocalPlayer
		local TARGET_SPEED = 24
		local FOAM_RATE = 12
		local locked = false
		local forceConnection = nil
		local foamLoop = nil
		local character = player.Character or player.CharacterAdded:Wait()
		local humanoid = character:WaitForChild("Humanoid")

		local ScreenGui = Instance.new("ScreenGui")
		ScreenGui.Name = "otm24.4Gui"
		ScreenGui.ResetOnSpawn = false
		pcall(function() ScreenGui.Parent = game:GetService("CoreGui") end)
		if not ScreenGui.Parent then ScreenGui.Parent = player:WaitForChild("PlayerGui") end

		local Button = Instance.new("TextButton")
		Button.Size = UDim2.new(0, 50, 0, 50)
		Button.Position = UDim2.new(1, -80, 1, -80)
		Button.BackgroundColor3 = Color3.fromRGB(231, 76, 60)
		Button.Text = "\nOFF"
		Button.TextColor3 = Color3.new(1, 1, 1)
		Button.TextScaled = true
		Button.Font = Enum.Font.SourceSansBold
		Button.BorderSizePixel = 0
		Button.AutoButtonColor = false
		Button.Parent = ScreenGui
		Instance.new("UICorner", Button).CornerRadius = UDim.new(1, 0)

		local dragging, dragInput, dragStart, startPos = false, nil, nil, nil
		Button.InputBegan:Connect(function(input)
			if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
				dragging = true dragStart = input.Position startPos = Button.Position
				input.Changed:Connect(function() if input.UserInputState == Enum.UserInputState.End then dragging = false end end)
			end
		end)
		Button.InputChanged:Connect(function(input)
			if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then dragInput = input end
		end)
		UserInputService.InputChanged:Connect(function(input)
			if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
				local delta = input.Position - dragStart
				Button.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
			end
		end)

		local function lockSpeed()
			if forceConnection then forceConnection:Disconnect() end
			humanoid.WalkSpeed = TARGET_SPEED
			forceConnection = RunService.Heartbeat:Connect(function()
				if humanoid and humanoid.WalkSpeed ~= TARGET_SPEED then humanoid.WalkSpeed = TARGET_SPEED end
			end)
			locked = true Button.Text = "\nON" Button.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
		end
		local function unlockSpeed()
			if forceConnection then forceConnection:Disconnect() forceConnection = nil end
			humanoid.WalkSpeed = 16 locked = false Button.Text = "otm\nOFF" Button.BackgroundColor3 = Color3.fromRGB(231, 76, 60)
		end
		local function startFoam()
			if foamLoop then foamLoop:Disconnect() end
			foamLoop = RunService.Heartbeat:Connect(function()
				if not locked then return end
				for _, obj in pairs(character:GetDescendants()) do
					if obj:IsA("ParticleEmitter") then
						local n = obj.Name:lower()
						if n:find("foam") or n:find("foot") or n:find("sprint") or n:find("run") or n:find("dust") then obj.Enabled = true obj.Rate = FOAM_RATE end
					end
				end
			end)
		end
		local function stopFoam() if foamLoop then foamLoop:Disconnect() foamLoop = nil end end

		Button.MouseButton1Click:Connect(function()
			if locked then unlockSpeed() stopFoam() else lockSpeed() startFoam() end
		end)
		player.CharacterAdded:Connect(function(newChar)
			character = newChar humanoid = newChar:WaitForChild("Humanoid")
			task.wait(0.8) if locked then lockSpeed() startFoam() end
		end)
	]])()
end)

CreateExecuteButton("HUB MT BOM", 6, function()
	loadstring([[
		local cgGui = Instance.new("ScreenGui")
		cgGui.Name = "CagaNaRoupaCustom"
		pcall(function() cgGui.Parent = game:GetService("CoreGui") end)
		if not cgGui.Parent then cgGui.Parent = game:GetService("Players").LocalPlayer:WaitForChild("PlayerGui") end

		local cgMain = Instance.new("Frame", cgGui)
		cgMain.Size = UDim2.new(0, 300, 0, 350)
		cgMain.Position = UDim2.new(0.5, 100, 0.5, -175)
		cgMain.BackgroundColor3 = Color3.fromRGB(15, 20, 25)
		cgMain.Active = true cgMain.Draggable = true
		Instance.new("UICorner", cgMain).CornerRadius = UDim.new(0, 8)
		Instance.new("UIStroke", cgMain).Color = Color3.fromRGB(80, 150, 255)

		local topbar = Instance.new("Frame", cgMain)
		topbar.Size = UDim2.new(1, 0, 0, 40)
		topbar.BackgroundColor3 = Color3.fromRGB(10, 15, 20)
		Instance.new("UICorner", topbar).CornerRadius = UDim.new(0, 8)
		
		local title = Instance.new("TextLabel", topbar)
		title.Size = UDim2.new(1, -40, 1, 0)
		title.BackgroundTransparency = 1
		title.Text = " 🔥hub quente do Zyck macaco"
		title.TextColor3 = Color3.fromRGB(255, 255, 255)
		title.Font = Enum.Font.GothamBold
		title.TextSize = 14
		title.TextXAlignment = Enum.TextXAlignment.Left

		local closeBtn = Instance.new("TextButton", topbar)
		closeBtn.Size = UDim2.new(0, 40, 1, 0)
		closeBtn.Position = UDim2.new(1, -40, 0, 0)
		closeBtn.BackgroundTransparency = 1
		closeBtn.Text = "X"
		closeBtn.TextColor3 = Color3.fromRGB(255, 60, 60)
		closeBtn.Font = Enum.Font.GothamBlack
		closeBtn.TextSize = 18
		closeBtn.MouseButton1Click:Connect(function() cgGui:Destroy() end)

		local scroll = Instance.new("ScrollingFrame", cgMain)
		scroll.Size = UDim2.new(1, -10, 1, -50)
		scroll.Position = UDim2.new(0, 5, 0, 45)
		scroll.BackgroundTransparency = 1
		scroll.ScrollBarThickness = 3
		local layout = Instance.new("UIListLayout", scroll)
		layout.Padding = UDim.new(0, 6)

		local function createTgl(name, callback)
			local btn = Instance.new("TextButton", scroll)
			btn.Size = UDim2.new(1, -10, 0, 35)
			btn.BackgroundColor3 = Color3.fromRGB(35, 40, 50)
			btn.Text = name .. " [OFF]"
			btn.TextColor3 = Color3.fromRGB(200, 200, 200)
			btn.Font = Enum.Font.GothamBold
			btn.TextSize = 13
			Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
			
			local s = false
			btn.MouseButton1Click:Connect(function()
				s = not s
				btn.Text = name .. (s and " [ON]" or " [OFF]")
				btn.BackgroundColor3 = s and Color3.fromRGB(40, 180, 80) or Color3.fromRGB(35, 40, 50)
				btn.TextColor3 = Color3.fromRGB(255, 255, 255)
				callback(s)
			end)
		end

		local function createBtn(name, callback)
			local btn = Instance.new("TextButton", scroll)
			btn.Size = UDim2.new(1, -10, 0, 35)
			btn.BackgroundColor3 = Color3.fromRGB(80, 100, 150)
			btn.Text = name
			btn.TextColor3 = Color3.fromRGB(255, 255, 255)
			btn.Font = Enum.Font.GothamBold
			btn.TextSize = 13
			Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
			btn.MouseButton1Click:Connect(callback)
		end

		-- ================= MIGRANDO FUNÇÕES =================
		createTgl("CORRER AUTOMÁTICO", function(state)
			if state then game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 24
			else game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 16 end
		end)

		createTgl("Otimizar Ping", function(state) print("Otimizar Ping: " .. tostring(state)) end)

		_G.Imant = false
		createTgl("REACH 1", function(state)
			_G.Imant = state
			task.spawn(function()
				while _G.Imant and game.Players.LocalPlayer.Character do
					local ball = workspace:FindFirstChild("Football") or workspace:FindFirstChild("Ball") or workspace:FindFirstChild("SoccerBall")
					if ball and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
						ball.Position = game.Players.LocalPlayer.Character.HumanoidRootPart.Position + Vector3.new(0, 2, 0)
					end
					task.wait(0.05)
				end
			end)
		end)

		_G.AutoGol = false
		createTgl("ASSISTÊNCIA PARA AJUDAR A FZR GOLS", function(state)
			_G.AutoGol = state
			task.spawn(function()
				while _G.AutoGol do
					pcall(function() game:GetService("ReplicatedStorage").Remotes.Kick:FireServer() end)
					task.wait(0.8)
				end
			end)
		end)

		createTgl("Aumento de FPS", function(state)
			if state then pcall(function() setfpscap(999) end)
			else pcall(function() setfpscap(60) end) end
		end)

		createTgl("botei a função sem querer", function(state) print("Passinho do Jamal: " .. tostring(state)) end)
		
		createBtn("REACH 2 (F4)", function() print("Desarme Auto ativado!") end)
	]])()
end)

-------------------------------------------------
-- BOTÃO FLUTUANTE
-------------------------------------------------
local toggle = Instance.new("TextButton", gui)
toggle.Size = UDim2.new(0,140,0,32)
toggle.Position = UDim2.new(0.05,0,0.15,0)
toggle.Text = "zyck otm "
toggle.BackgroundColor3 = Color3.fromRGB(90,90,90)
toggle.TextColor3 = Color3.new(1,1,1)
toggle.Font = Enum.Font.SourceSansBold
toggle.TextSize = 14
toggle.Active = true
toggle.Draggable = true

toggle.MouseButton1Click:Connect(function()
	main.Visible = not main.Visible
end)

-------------------------------------------------
-- LOOP PRINCIPAL
-------------------------------------------------
RunService.RenderStepped:Connect(function()
	if Settings.Atravessar then UltraAtravessar() end
	if Settings.DesarmeAuto then AutoDesarme() end
end)
