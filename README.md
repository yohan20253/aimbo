--// Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

--// Configurações
local ParteParaMirar = "Head" -- ou "HumanoidRootPart"
local ChecarTime = false -- true se quiser evitar mirar em aliados
local FOV = 200 -- Campo de visão em pixels
local AimbotAtivo = false

--// Interface
local gui = Instance.new("ScreenGui")
gui.Name = "AimbotGUI"
gui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local botao = Instance.new("TextButton")
botao.Size = UDim2.new(0, 120, 0, 50)
botao.Position = UDim2.new(1, -130, 1, -60)
botao.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
botao.TextColor3 = Color3.new(1, 1, 1)
botao.Text = "Aimbot: OFF"
botao.Parent = gui

--// Função: encontrar inimigo mais próximo
local function PegarAlvoMaisProximo()
	local alvoMaisProximo = nil
	local menorDistancia = FOV

	for _, jogador in pairs(Players:GetPlayers()) do
		if jogador ~= LocalPlayer and jogador.Character and jogador.Character:FindFirstChild(ParteParaMirar) then
			if not ChecarTime or jogador.Team ~= LocalPlayer.Team then
				local parte = jogador.Character[ParteParaMirar]
				local posicaoNaTela, visivel = Camera:WorldToViewportPoint(parte.Position)
				if visivel then
					local centro = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
					local distancia = (Vector2.new(posicaoNaTela.X, posicaoNaTela.Y) - centro).Magnitude
					if distancia < menorDistancia then
						menorDistancia = distancia
						alvoMaisProximo = parte
					end
				end
			end
		end
	end

	return alvoMaisProximo
end

--// Atualiza a câmera para mirar no alvo
RunService.RenderStepped:Connect(function()
	if AimbotAtivo then
		local alvo = PegarAlvoMaisProximo()
		if alvo then
			local direcao = (alvo.Position - Camera.CFrame.Position).Unit
			Camera.CFrame = CFrame.new(Camera.CFrame.Position, Camera.CFrame.Position + direcao)
		end
	end
end)

--// Quando botão é tocado
botao.MouseButton1Click:Connect(function()
	AimbotAtivo = not AimbotAtivo
	botao.Text = AimbotAtivo and "Aimbot: ON" or "Aimbot: OFF"
	botao.BackgroundColor3 = AimbotAtivo and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(30, 30, 30)
end)
