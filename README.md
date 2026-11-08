do
	local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))();
	local Players = game:GetService("Players");
	local RunService = game:GetService("RunService");
	local ReplicatedStorage = game:GetService("ReplicatedStorage");
	local Workspace = game:GetService("Workspace");
	local CurrentCamera = Workspace.CurrentCamera;
	local LocalPlayer = Players.LocalPlayer;
	local CoreGui = game:GetService("CoreGui");
	local UserInputService = game:GetService("UserInputService");
	local HttpService = game:GetService("HttpService");
	local TeleportService = game:GetService("TeleportService");

-- ==========================================
-- RASTREAMENTO DE CONEXÕES PARA O SISTEMA DE TOGGLE
-- ==========================================
local ActiveConnections = {
	RenderStepped = {},
	Stepped = {},
	Heartbeat = {},
	PlayerRemoving = {},
	ChildAdded = {},
	OnTeleport = {}
}
local ScriptEnabled = true
local GUIVisible = true
local CtrlPressed = false

-- Variáveis da Mira Silenciosa
local SilentAimbot = {
	Enabled = false,
	TargetPart = "Head",
	FOV = 100,
	WallCheck = true,
	TeamCheck = false,
	Prediction = 0.165,
	CircleVisible = true,
	CircleColor = Color3.fromRGB(255, 255, 255),
	CircleRadius = 100,
	SelectedTarget = nil,
	Connection = nil
}

function gradient(text, startColor, endColor)
	local result = "";
	local length = #text;
	for i = 1, length do
		local t = (i - 1) / math.max(length - 1, 1);
		local r = math.floor((startColor.R + ((endColor.R - startColor.R) * t)) * 255);
		local g = math.floor((startColor.G + ((endColor.G - startColor.G) * t)) * 255);
		local b = math.floor((startColor.B + ((endColor.B - startColor.B) * t)) * 255);
		local char = text:sub(i, i);
		result = result .. '<font color="rgb(' .. r .. ", " .. g .. ", " .. b .. ')">' .. char .. "</font>";
	end
	return result;
end

local Confirmed = false;
WindUI:Popup({
	Title = gradient("Eclipse", Color3.fromHex("#eb1010"), Color3.fromHex("#1023eb")),
	Icon = "rbxassetid://72462144048455",
	Content = gradient("Este script foi criado por", Color3.fromHex("#10eb3c"), Color3.fromHex("#67c97a")) .. gradient(" Wellyson José", Color3.fromHex("#001e80"), Color3.fromHex("#16f2d9")),
	Buttons = {
		{
			Title = gradient("Cancelar", Color3.fromHex("#e80909"), Color3.fromHex("#630404")),
			Callback = function()
			end,
			Variant = "Tertiary"
		},
		{
			Title = gradient("Carregar", Color3.fromHex("#90f09e"), Color3.fromHex("#13ed34")),
			Callback = function()
				Confirmed = true;
			end,
			Variant = "Secondary"
		}
	}
});
repeat
	task.wait();
until Confirmed

WindUI:Notify({
	Title = gradient("Eclipse", Color3.fromHex("#eb1010"), Color3.fromHex("#1023eb")),
	Content = "Script carregado com sucesso! Pressione Ctrl+M para alternar",
	Icon = "check-circle",
	Duration = 5
});

local Window = WindUI:CreateWindow({
	Title = gradient("Eclipse [ATUALIZAÇÃO DE VERÃO]", Color3.fromHex("#001e80"), Color3.fromHex("#ffea00")),
	Icon = "rbxassetid://72462144048455",
	Author = gradient("Wellyson José", Color3.fromHex("#1bf2b2"), Color3.fromHex("#1bcbf2")),
	Folder = "EclipseWindUI",
	Size = UDim2.fromOffset(350, 400),
	Transparent = true,
	Theme = "Dark",
	SideBarWidth = 200,
	UserEnabled = true,
	HasOutline = true
});

Window:EditOpenButton({
	Title = "Abrir UI do Eclipse",
	Icon = "rbxassetid://72462144048455",
	CornerRadius = UDim.new(2, 6),
	StrokeThickness = 2,
	Color = ColorSequence.new(Color3.fromHex("1E213D"), Color3.fromHex("1F75FE")),
	Draggable = true
});

local Tabs = {
	MainTab = Window:Tab({
		Title = gradient("PRINCIPAL", Color3.fromHex("#ffffff"), Color3.fromHex("#636363")),
		Icon = "terminal",
		Desc = "Acesso rápido e informações"
	}),
	CharacterTab = Window:Tab({
		Title = gradient("PERSONAGEM", Color3.fromHex("#ffffff"), Color3.fromHex("#636363")),
		Icon = "file-cog"
	}),
	TeleportTab = Window:Tab({
		Title = gradient("TELEPORTE", Color3.fromHex("#ffffff"), Color3.fromHex("#636363")),
		Icon = "user"
	}),
	EspTab = Window:Tab({
		Title = gradient("ESP", Color3.fromHex("#ffffff"), Color3.fromHex("#636363")),
		Icon = "eye"
	}),
	AimbotTab = Window:Tab({
		Title = gradient("MIRA AUTOMÁTICA", Color3.fromHex("#ffffff"), Color3.fromHex("#636363")),
		Icon = "arrow-right"
	}),
	AutoFarm = Window:Tab({
		Title = gradient("FARM AUTOMÁTICO", Color3.fromHex("#ffffff"), Color3.fromHex("#636363")),
		Icon = "user"
	}),
	bs = Window:Divider(),
	InnocentTab = Window:Tab({
		Title = gradient("INOCENTE", Color3.fromHex("#0ff707"), Color3.fromHex("#1e690c")),
		Icon = "circle"
	}),
	MurderTab = Window:Tab({
		Title = gradient("ASSASSINO", Color3.fromHex("#e80909"), Color3.fromHex("#630404")),
		Icon = "circle"
	}),
	SheriffTab = Window:Tab({
		Title = gradient("XERIFE", Color3.fromHex("#001e80"), Color3.fromHex("#16f2d9")),
		Icon = "circle"
	}),
	gh = Window:Divider(),
	ServerTab = Window:Tab({
		Title = gradient("SERVIDOR", Color3.fromHex("#ffffff"), Color3.fromHex("#636363")),
		Icon = "atom",
		Desc = "Ferramentas de gerenciamento de servidor"
	}),
	SettingsTab = Window:Tab({
		Title = gradient("CONFIGURAÇÕES", Color3.fromHex("#ffffff"), Color3.fromHex("#636363")),
		Icon = "code"
	}),
	ChangelogsTab = Window:Tab({
		Title = gradient("NOVIDADES", Color3.fromHex("#ffffff"), Color3.fromHex("#636363")),
		Icon = "info",
		Desc = "Histórico de atualizações e planos futuros"
	}),
	SocialsTab = Window:Tab({
		Title = gradient("REDES SOCIAIS", Color3.fromHex("#ffffff"), Color3.fromHex("#636363")),
		Icon = "star",
		Desc = "Conecte-se com o desenvolvedor"
	}),
	b = Window:Divider(),
	WindowTab = Window:Tab({
		Title = gradient("CONFIGURAÇÃO", Color3.fromHex("#ffffff"), Color3.fromHex("#636363")),
		Icon = "settings",
		Desc = "Gerencie as configurações da janela e arquivos de configuração."
	}),
	CreateThemeTab = Window:Tab({
		Title = gradient("TEMAS", Color3.fromHex("#ffffff"), Color3.fromHex("#636363")),
		Icon = "palette",
		Desc = "Crie e aplique temas personalizados."
	})
};

-- ==========================================
-- CONTEÚDO DA ABA PRINCIPAL
-- ==========================================
Tabs.MainTab:Section({
	Title = gradient("Bem-vindo", Color3.fromHex("#FFD700"), Color3.fromHex("#FFA500"))
});

Tabs.MainTab:Paragraph({
	Title = "Eclipse Script v2.0 CORRIGIDO",
	Desc = "Bem-vindo ao Eclipse de Wellyson José!\n\nPressione Ctrl+M para ativar/desativar o script.\nTodos os recursos estão organizados por abas de cargo.",
	Image = "rbxassetid://72462144048455",
	ImageSize = 48
});

Tabs.MainTab:Section({
	Title = gradient("Ações Rápidas", Color3.fromHex("#00ff40"), Color3.fromHex("#008f11"))
});

Tabs.MainTab:Button({
	Title = "Teleportar para o Lobby",
	Callback = function()
		local lobby = workspace:FindFirstChild("Lobby");
		if lobby then
			local spawnPoint = lobby:FindFirstChild("SpawnPoint") or lobby:FindFirstChildOfClass("SpawnLocation");
			if not spawnPoint then
				spawnPoint = lobby:FindFirstChildWhichIsA("BasePart") or lobby;
			end
			if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
				LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(spawnPoint.Position + Vector3.new(0, 3, 0));
				WindUI:Notify({
					Title = "Teleporte",
					Content = "Teleportado para o Lobby!",
					Icon = "check-circle",
					Duration = 2
				});
			end
		else
			WindUI:Notify({
				Title = "Erro",
				Content = "Lobby não encontrado!",
				Icon = "x-circle",
				Duration = 2
			});
		end
	end
});

Tabs.MainTab:Button({
	Title = "Pegar Arma (Se Disponível)",
	Callback = function()
		WindUI:Notify({
			Title = "Info",
			Content = "Use a aba Inocente para recursos de arma!",
			Icon = "info",
			Duration = 3
		});
	end
});

Tabs.MainTab:Section({
	Title = gradient("Controles", Color3.fromHex("#00eaff"), Color3.fromHex("#002a2e"))
});

Tabs.MainTab:Code({
	Title = "Atalhos:",
	Code = [[• Ctrl + M - Alternar Script Ligado/Desligado
• Tiro automático - Ativa a Mira Silenciosa
• Abas da UI - Acesse todos os recursos

Dicas:
• Use o ESP para encontrar o Assassino/Xerife
• A Mira Silenciosa funciona ao segurar o botão direito
• O Botão de Tiro aparece para o Xerife]]
});

Tabs.MainTab:Section({
	Title = gradient("Status", Color3.fromHex("#b914fa"), Color3.fromHex("#7023c2"))
});

local statusParagraph = Tabs.MainTab:Paragraph({
	Title = "Status Atual",
	Desc = "Cargo: Verificando...\nVivo: Sim\nScript: Ativo",
	Image = "activity",
	ImageSize = 32
});

task.spawn(function()
	while task.wait(2) do
		local success, roles = pcall(function()
			return ReplicatedStorage:FindFirstChild("GetPlayerData", true):InvokeServer();
		end);
		local roleText = "Desconhecido";
		local isAlive = "Sim";
		if success and roles and roles[LocalPlayer.Name] then
			roleText = roles[LocalPlayer.Name].Role or "Desconhecido";
			if roles[LocalPlayer.Name].Dead or roles[LocalPlayer.Name].Killed then
				isAlive = "Não";
			end
		end
		statusParagraph:SetDesc("Cargo: " .. roleText .. "\nVivo: " .. isAlive .. "\nScript: Ativo | Alternar: Ctrl+M");
	end
end);

-- ==========================================
-- ABA PERSONAGEM
-- ==========================================
local CharacterSettings = {
	WalkSpeed = {
		Value = 16,
		Default = 16,
		Locked = false
	},
	JumpPower = {
		Value = 50,
		Default = 50,
		Locked = false
	}
};

local function updateCharacter()
	local character = LocalPlayer.Character;
	if not character then return end
	local humanoid = character:FindFirstChildOfClass("Humanoid");
	if humanoid then
		if not CharacterSettings.WalkSpeed.Locked then
			humanoid.WalkSpeed = CharacterSettings.WalkSpeed.Value;
		end
		if not CharacterSettings.JumpPower.Locked then
			humanoid.JumpPower = CharacterSettings.JumpPower.Value;
		end
	end
end

Tabs.CharacterTab:Section({
	Title = gradient("Velocidade de Caminhada", Color3.fromHex("#ff0000"), Color3.fromHex("#300000"))
});

Tabs.CharacterTab:Slider({
	Title = "Velocidade",
	Value = {
		Min = 0,
		Max = 200,
		Default = 16
	},
	Callback = function(value)
		CharacterSettings.WalkSpeed.Value = value;
		updateCharacter();
	end
});

Tabs.CharacterTab:Button({
	Title = "Redefinir velocidade",
	Callback = function()
		CharacterSettings.WalkSpeed.Value = CharacterSettings.WalkSpeed.Default;
		updateCharacter();
	end
});

Tabs.CharacterTab:Toggle({
	Title = "Bloquear velocidade",
	Default = false,
	Callback = function(state)
		CharacterSettings.WalkSpeed.Locked = state;
		updateCharacter();
	end
});

Tabs.CharacterTab:Section({
	Title = gradient("Força do Pulo", Color3.fromHex("#001aff"), Color3.fromHex("#020524"))
});

Tabs.CharacterTab:Slider({
	Title = "Força do Pulo",
	Value = {
		Min = 0,
		Max = 200,
		Default = 50
	},
	Callback = function(value)
		CharacterSettings.JumpPower.Value = value;
		updateCharacter();
	end
});

Tabs.CharacterTab:Button({
	Title = "Redefinir força do pulo",
	Callback = function()
		CharacterSettings.JumpPower.Value = CharacterSettings.JumpPower.Default;
		updateCharacter();
	end
});

Tabs.CharacterTab:Toggle({
	Title = "Bloquear força do pulo",
	Default = false,
	Callback = function(state)
		CharacterSettings.JumpPower.Locked = state;
		updateCharacter();
	end
});

-- ==========================================
-- ABA ESP (COM ESP DE DESENHO ADICIONADO)
-- ==========================================
local LP = Players.LocalPlayer;
local ESPConfig = {
	HighlightMurderer = false,
	HighlightInnocent = false,
	HighlightSheriff = false,
	NameESP = false,
	BoxESP = false,
	TracerESP = false,
	Alerts = true
};
local Murder, Sheriff, Hero;
local roles = {};
local KnownMurderer = nil;

local ESPDrawings = {}

local function createDrawings(player)
	local drawings = {}
	drawings.Box = Drawing.new("Square")
	drawings.Box.Visible = false
	drawings.Box.Color = Color3.new(1, 1, 1)
	drawings.Box.Thickness = 1
	drawings.Box.Filled = false

	drawings.Name = Drawing.new("Text")
	drawings.Name.Visible = false
	drawings.Name.Color = Color3.new(1, 1, 1)
	drawings.Name.Size = 16
	drawings.Name.Center = true
	drawings.Name.Outline = true

	drawings.Tracer = Drawing.new("Line")
	drawings.Tracer.Visible = false
	drawings.Tracer.Color = Color3.new(1, 1, 1)
	drawings.Tracer.Thickness = 1

	ESPDrawings[player] = drawings
end

for _, player in pairs(Players:GetPlayers()) do
	if player ~= LP then createDrawings(player) end
end

table.insert(ActiveConnections.ChildAdded, Players.PlayerAdded:Connect(function(player)
	createDrawings(player)
end))

function CreateHighlight(player)
	if ((player ~= LP) and player.Character and not player.Character:FindFirstChild("Highlight")) then
		local highlight = Instance.new("Highlight");
		highlight.Parent = player.Character;
		highlight.Adornee = player.Character;
		highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop;
		return highlight;
	end
	return player.Character and player.Character:FindFirstChild("Highlight");
end

function RemoveAllHighlights()
	for _, player in pairs(Players:GetPlayers()) do
		if (player.Character and player.Character:FindFirstChild("Highlight")) then
			player.Character.Highlight:Destroy();
		end
		if ESPDrawings[player] then
			ESPDrawings[player].Box.Visible = false
			ESPDrawings[player].Name.Visible = false
			ESPDrawings[player].Tracer.Visible = false
		end
	end
end

function UpdateHighlights()
	for _, player in pairs(Players:GetPlayers()) do
		if ((player ~= LP) and player.Character) then
			local highlight = player.Character:FindFirstChild("Highlight");
			local drawings = ESPDrawings[player]

			local shouldHighlight = false;
			local color = Color3.new(0, 1, 0);

			if ((player.Name == Murder) and IsAlive(player) and (ESPConfig.HighlightMurderer or ESPConfig.NameESP or ESPConfig.BoxESP or ESPConfig.TracerESP)) then
				color = Color3.fromRGB(255, 0, 0);
				shouldHighlight = true;
			elseif ((player.Name == Sheriff) and IsAlive(player) and (ESPConfig.HighlightSheriff or ESPConfig.NameESP or ESPConfig.BoxESP or ESPConfig.TracerESP)) then
				color = Color3.fromRGB(0, 0, 255);
				shouldHighlight = true;
			elseif (ESPConfig.HighlightInnocent and IsAlive(player) and (player.Name ~= Murder) and (player.Name ~= Sheriff) and (player.Name ~= Hero)) then
				color = Color3.fromRGB(0, 255, 0);
				shouldHighlight = true;
			elseif ((player.Name == Hero) and IsAlive(player) and not IsAlive(game.Players[Sheriff]) and (ESPConfig.HighlightSheriff or ESPConfig.NameESP or ESPConfig.BoxESP or ESPConfig.TracerESP)) then
				color = Color3.fromRGB(255, 250, 0);
				shouldHighlight = true;
			end

			if shouldHighlight then
				if highlight and (ESPConfig.HighlightMurderer or ESPConfig.HighlightSheriff or ESPConfig.HighlightInnocent) then
					highlight.FillColor = color;
					highlight.OutlineColor = color;
					highlight.Enabled = true;
				elseif not highlight and (ESPConfig.HighlightMurderer or ESPConfig.HighlightSheriff or ESPConfig.HighlightInnocent) then
					highlight = CreateHighlight(player);
					if highlight then
						highlight.FillColor = color;
						highlight.OutlineColor = color;
						highlight.Enabled = true;
					end
				elseif highlight and not (ESPConfig.HighlightMurderer or ESPConfig.HighlightSheriff or ESPConfig.HighlightInnocent) then
					highlight.Enabled = false
				end

				if drawings then
					local root = player.Character:FindFirstChild("HumanoidRootPart")
					local head = player.Character:FindFirstChild("Head")
					if root and head then
						local pos, onScreen = CurrentCamera:WorldToViewportPoint(root.Position)
						local headPos, headOnScreen = CurrentCamera:WorldToViewportPoint(head.Position + Vector3.new(0, 0.5, 0))
						local legPos, legOnScreen = CurrentCamera:WorldToViewportPoint(root.Position - Vector3.new(0, 3, 0))

						if onScreen then
							if ESPConfig.BoxESP then
								drawings.Box.Size = Vector2.new(2000 / pos.Z, headPos.Y - legPos.Y)
								drawings.Box.Position = Vector2.new(pos.X - drawings.Box.Size.X / 2, legPos.Y)
								drawings.Box.Color = color
								drawings.Box.Visible = true
							else
								drawings.Box.Visible = false
							end

							if ESPConfig.NameESP then
								drawings.Name.Text = player.Name
								drawings.Name.Position = Vector2.new(headPos.X, headPos.Y - 20)
								drawings.Name.Color = color
								drawings.Name.Visible = true
							else
								drawings.Name.Visible = false
							end

							if ESPConfig.TracerESP then
								drawings.Tracer.From = Vector2.new(CurrentCamera.ViewportSize.X / 2, CurrentCamera.ViewportSize.Y)
								drawings.Tracer.To = Vector2.new(pos.X, pos.Y)
								drawings.Tracer.Color = color
								drawings.Tracer.Visible = true
							else
								drawings.Tracer.Visible = false
							end
						else
							drawings.Box.Visible = false
							drawings.Name.Visible = false
							drawings.Tracer.Visible = false
						end
					else
						drawings.Box.Visible = false
						drawings.Name.Visible = false
						drawings.Tracer.Visible = false
					end
				end
			else
				if highlight then highlight.Enabled = false end
				if drawings then
					drawings.Box.Visible = false
					drawings.Name.Visible = false
					drawings.Tracer.Visible = false
				end
			end
		end
	end
end

function IsAlive(player)
	for name, data in pairs(roles) do
		if (player.Name == name) then
			return not data.Killed and not data.Dead;
		end
	end
	return false;
end

local function UpdateRoles()
	local success, result = pcall(function()
		return ReplicatedStorage:FindFirstChild("GetPlayerData", true):InvokeServer();
	end);
	if success then
		roles = result or {};
		for name, data in pairs(roles) do
			if (data.Role == "Murderer") then
				Murder = name;
				if ESPConfig.Alerts and KnownMurderer ~= Murder then
					KnownMurderer = Murder
					WindUI:Notify({
						Title = "⚠️ Assassino Detectado ⚠️",
						Content = Murder .. " é o Assassino!",
						Icon = "alert-triangle",
						Duration = 6
					})
				end
			elseif (data.Role == "Sheriff") then
				Sheriff = name;
			elseif (data.Role == "Hero") then
				Hero = name;
			end
		end
	end
end

Tabs.EspTab:Section({
	Title = gradient("Destaque de Jogadores", Color3.fromHex("#b914fa"), Color3.fromHex("#7023c2"))
});

Tabs.EspTab:Toggle({
	Title = gradient("Destacar Assassino", Color3.fromHex("#e80909"), Color3.fromHex("#630404")),
	Default = false,
	Callback = function(state)
		ESPConfig.HighlightMurderer = state;
		if not state then UpdateHighlights(); end
	end
});

Tabs.EspTab:Toggle({
	Title = gradient("Destacar Inocente", Color3.fromHex("#0ff707"), Color3.fromHex("#1e690c")),
	Default = false,
	Callback = function(state)
		ESPConfig.HighlightInnocent = state;
		if not state then UpdateHighlights(); end
	end
});

Tabs.EspTab:Toggle({
	Title = gradient("Destacar Xerife", Color3.fromHex("#001e80"), Color3.fromHex("#16f2d9")),
	Default = false,
	Callback = function(state)
		ESPConfig.HighlightSheriff = state;
		if not state then UpdateHighlights(); end
	end
});

Tabs.EspTab:Section({
	Title = gradient("ESP de Desenho", Color3.fromHex("#ff8800"), Color3.fromHex("#ffaa00"))
});

Tabs.EspTab:Toggle({
	Title = "ESP de Nome",
	Default = false,
	Callback = function(state)
		ESPConfig.NameESP = state;
		if not state then UpdateHighlights(); end
	end
});

Tabs.EspTab:Toggle({
	Title = "ESP de Caixa",
	Default = false,
	Callback = function(state)
		ESPConfig.BoxESP = state;
		if not state then UpdateHighlights(); end
	end
});

Tabs.EspTab:Toggle({
	Title = "ESP de Rastreador",
	Default = false,
	Callback = function(state)
		ESPConfig.TracerESP = state;
		if not state then UpdateHighlights(); end
	end
});

Tabs.EspTab:Toggle({
	Title = "Alertas de Detecção do Assassino",
	Default = true,
	Callback = function(state)
		ESPConfig.Alerts = state;
	end
});

local gunDropESPEnabled = false;

local function createGunDropHighlight(gunDrop)
	if (gunDropESPEnabled and gunDrop and not gunDrop:FindFirstChild("GunDropHighlight")) then
		local highlight = Instance.new("Highlight");
		highlight.Name = "GunDropHighlight";
		highlight.FillColor = Color3.fromRGB(255, 215, 0);
		highlight.OutlineColor = Color3.fromRGB(255, 165, 0);
		highlight.Adornee = gunDrop;
		highlight.Parent = gunDrop;
	end
end

local function updateGunDropESP()
	for _, child in pairs(workspace:GetDescendants()) do
		if child.Name == "GunDrop" then
			if gunDropESPEnabled then
				createGunDropHighlight(child)
			else
				if child:FindFirstChild("GunDropHighlight") then
					child.GunDropHighlight:Destroy()
				end
			end
		end
	end
end

Tabs.EspTab:Toggle({
	Title = gradient("Destaque de Arma Caída", Color3.fromHex("#ffff00"), Color3.fromHex("#4f4f00")),
	Default = false,
	Callback = function(state)
		gunDropESPEnabled = state;
		updateGunDropESP();
	end
});

local workspaceChildConn = workspace.DescendantAdded:Connect(function(child)
	if child.Name == "GunDrop" then
		task.wait(1);
		updateGunDropESP();
	end
end);
table.insert(ActiveConnections.ChildAdded, workspaceChildConn);

local roleUpdateAccumulator = 0
local espRenderConn = RunService.RenderStepped:Connect(function(dt)
	if not ScriptEnabled then return end
	roleUpdateAccumulator += dt
	if roleUpdateAccumulator >= 0.5 then
		roleUpdateAccumulator = 0
		UpdateRoles()
	end
	UpdateHighlights()
end);
table.insert(ActiveConnections.RenderStepped, espRenderConn);

local playerRemoveConn = Players.PlayerRemoving:Connect(function(player)
	if (player == LP) then
		RemoveAllHighlights();
	end
	if ESPDrawings[player] then
		ESPDrawings[player].Box:Remove()
		ESPDrawings[player].Name:Remove()
		ESPDrawings[player].Tracer:Remove()
		ESPDrawings[player] = nil
	end
end);
table.insert(ActiveConnections.PlayerRemoving, playerRemoveConn);

-- ==========================================
-- ABA TELEPORTE
-- ==========================================
Tabs.TeleportTab:Section({
	Title = gradient("TP Padrão", Color3.fromHex("#00448c"), Color3.fromHex("#0affd6"))
});

local teleportTarget = nil;
local teleportDropdown = nil;

local function updateTeleportPlayers()
	local playersList = {
		"Selecionar Jogador"
	};
	for _, player in pairs(Players:GetPlayers()) do
		if (player ~= LocalPlayer) then
			table.insert(playersList, player.Name);
		end
	end
	return playersList;
end

local function initializeTeleportDropdown()
	teleportDropdown = Tabs.TeleportTab:Dropdown({
		Title = "Jogadores",
		Values = updateTeleportPlayers(),
		Value = "Selecionar Jogador",
		Callback = function(selected)
			if (selected ~= "Selecionar Jogador") then
				teleportTarget = Players:FindFirstChild(selected);
			else
				teleportTarget = nil;
			end
		end
	});
end
initializeTeleportDropdown();

local playerAddedConn = Players.PlayerAdded:Connect(function(player)
	task.wait(1);
	if teleportDropdown then
		teleportDropdown:Refresh(updateTeleportPlayers());
	end
end);
table.insert(ActiveConnections.ChildAdded, playerAddedConn);

local playerRemoveConn2 = Players.PlayerRemoving:Connect(function(player)
	if teleportDropdown then
		teleportDropdown:Refresh(updateTeleportPlayers());
	end
end);
table.insert(ActiveConnections.ChildAdded, playerRemoveConn2);

local function teleportToPlayer()
	if (teleportTarget and teleportTarget.Character) then
		local targetRoot = teleportTarget.Character:FindFirstChild("HumanoidRootPart");
		local localRoot = LocalPlayer.Character:FindFirstChild("HumanoidRootPart");
		if (targetRoot and localRoot) then
			localRoot.CFrame = targetRoot.CFrame;
			WindUI:Notify({
				Title = "Teleporte",
				Content = "Teleportado com sucesso para " .. teleportTarget.Name,
				Icon = "check-circle",
				Duration = 3
			});
		end
	else
		WindUI:Notify({
			Title = "Erro",
			Content = "Alvo não encontrado ou indisponível",
			Icon = "x-circle",
			Duration = 3
		});
	end
end

Tabs.TeleportTab:Button({
	Title = "Teleportar para jogador",
	Callback = teleportToPlayer
});

Tabs.TeleportTab:Button({
	Title = "Atualizar lista de jogadores",
	Callback = function()
		teleportDropdown:Refresh(updateTeleportPlayers());
	end
});

Tabs.TeleportTab:Section({
	Title = gradient("TP Especial", Color3.fromHex("#b914fa"), Color3.fromHex("#7023c2"))
});

Tabs.TeleportTab:Button({
	Title = "Teleportar para o Lobby",
	Callback = function()
		local lobby = workspace:FindFirstChild("Lobby");
		if not lobby then
			WindUI:Notify({
				Title = "Teleporte",
				Content = "Lobby não encontrado!",
				Icon = "x-circle",
				Duration = 2
			});
			return;
		end
		local spawnPoint = lobby:FindFirstChild("SpawnPoint") or lobby:FindFirstChildOfClass("SpawnLocation");
		if not spawnPoint then
			spawnPoint = lobby:FindFirstChildWhichIsA("BasePart") or lobby;
		end
		if (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")) then
			LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(spawnPoint.Position + Vector3.new(0, 3, 0));
			WindUI:Notify({
				Title = "Teleporte",
				Content = "Teleportado para o Lobby!",
				Icon = "check-circle",
				Duration = 2
			});
		end
	end
});

Tabs.TeleportTab:Button({
	Title = "Teleportar para o Xerife",
	Callback = function()
		UpdateRoles();
		if Sheriff then
			local sheriffPlayer = Players:FindFirstChild(Sheriff);
			if (sheriffPlayer and sheriffPlayer.Character) then
				local targetRoot = sheriffPlayer.Character:FindFirstChild("HumanoidRootPart");
				local localRoot = LocalPlayer.Character:FindFirstChild("HumanoidRootPart");
				if (targetRoot and localRoot) then
					localRoot.CFrame = targetRoot.CFrame;
					WindUI:Notify({
						Title = "Teleporte",
						Content = "Teleportado com sucesso para o Xerife " .. Sheriff,
						Icon = "check-circle",
						Duration = 3
					});
				end
			else
				WindUI:Notify({
					Title = "Erro",
					Content = "Xerife não encontrado ou indisponível",
					Icon = "x-circle",
					Duration = 3
				});
			end
		else
			WindUI:Notify({
				Title = "Erro",
				Content = "Xerife não definido na partida atual",
				Icon = "x-circle",
				Duration = 3
			});
		end
	end
});

Tabs.TeleportTab:Button({
	Title = "Teleportar para o Assassino",
	Callback = function()
		UpdateRoles();
		if Murder then
			local murderPlayer = Players:FindFirstChild(Murder);
			if (murderPlayer and murderPlayer.Character) then
				local targetRoot = murderPlayer.Character:FindFirstChild("HumanoidRootPart");
				local localRoot = LocalPlayer.Character:FindFirstChild("HumanoidRootPart");
				if (targetRoot and localRoot) then
					localRoot.CFrame = targetRoot.CFrame;
					WindUI:Notify({
						Title = "Teleporte",
						Content = "Teleportado com sucesso para o Assassino " .. Murder,
						Icon = "check-circle",
						Duration = 3
					});
				end
			else
				WindUI:Notify({
					Title = "Erro",
					Content = "Assassino não encontrado ou indisponível",
					Icon = "x-circle",
					Duration = 3
				});
			end
		else
			WindUI:Notify({
				Title = "Erro",
				Content = "Assassino não definido na partida atual",
				Icon = "x-circle",
				Duration = 3
			});
		end
	end
});

-- ==========================================
-- ABA MIRA AUTOMÁTICA
-- ==========================================
Tabs.AimbotTab:Section({
	Title = gradient("Mira Automática de Câmera", Color3.fromHex("#00448c"), Color3.fromHex("#0affd6"))
});

local AimbotConfig = {
	SmoothAim = false,
	Smoothness = 0.5
}

local isCameraLocked = false;
local isSpectating = false;
local lockedRole = nil;
local cameraConnection = nil;
local originalCameraType = Enum.CameraType.Custom;
local originalCameraSubject = nil;

Tabs.AimbotTab:Dropdown({
	Title = "Cargo Alvo",
	Values = {
		"Nenhum",
		"Xerife",
		"Assassino"
	},
	Value = "Nenhum",
	Callback = function(selected)
		lockedRole = ((selected ~= "Nenhum") and selected) or nil;
	end
});

Tabs.AimbotTab:Toggle({
	Title = "Modo Espectador",
	Default = false,
	Callback = function(state)
		isSpectating = state;
		if state then
			originalCameraType = CurrentCamera.CameraType;
			originalCameraSubject = CurrentCamera.CameraSubject;
			CurrentCamera.CameraType = Enum.CameraType.Scriptable;
		else
			CurrentCamera.CameraType = originalCameraType;
			CurrentCamera.CameraSubject = originalCameraSubject;
		end
	end
});

Tabs.AimbotTab:Toggle({
	Title = "Travar Câmera",
	Default = false,
	Callback = function(state)
		isCameraLocked = state;
		if (not state and not isSpectating) then
			CurrentCamera.CameraType = originalCameraType;
			CurrentCamera.CameraSubject = originalCameraSubject;
		end
	end
});

Tabs.AimbotTab:Toggle({
	Title = "Mira Suave",
	Default = false,
	Callback = function(state)
		AimbotConfig.SmoothAim = state
	end
});

Tabs.AimbotTab:Slider({
	Title = "Suavidade (Menor = Mais Lento)",
	Step = 0.05,
	Value = {
		Min = 0.05,
		Max = 1,
		Default = 0.5
	},
	Callback = function(value)
		AimbotConfig.Smoothness = value
	end
});

local function GetTargetPosition()
	if not lockedRole then
		return nil;
	end
	local targetName = ((lockedRole == "Xerife") and Sheriff) or Murder;
	if not targetName then
		return nil;
	end
	local player = Players:FindFirstChild(targetName);
	if (not player or not IsAlive(player)) then
		return nil;
	end
	local character = player.Character;
	if not character then
		return nil;
	end
	local head = character:FindFirstChild("Head");
	return (head and head.Position) or nil;
end

local function UpdateSpectate()
	if (not isSpectating or not lockedRole) then
		return;
	end
	local targetPos = GetTargetPosition();
	if not targetPos then
		return;
	end
	local offset = CFrame.new(0, 2, 8);
	local targetChar = Players:FindFirstChild(((lockedRole == "Xerife") and Sheriff) or Murder).Character;
	if targetChar then
		local root = targetChar:FindFirstChild("HumanoidRootPart");
		if root then
			CurrentCamera.CFrame = root.CFrame * offset;
		end
	end
end

local function UpdateLockCamera()
	if (not isCameraLocked or not lockedRole) then
		return;
	end
	local targetPos = GetTargetPosition();
	if not targetPos then
		return;
	end
	local currentPos = CurrentCamera.CFrame.Position;
	if AimbotConfig.SmoothAim then
		CurrentCamera.CFrame = CurrentCamera.CFrame:Lerp(CFrame.new(currentPos, targetPos), AimbotConfig.Smoothness)
	else
		CurrentCamera.CFrame = CFrame.new(currentPos, targetPos);
	end
end

local function Update()
	if isSpectating then
		UpdateSpectate();
	elseif isCameraLocked then
		UpdateLockCamera();
	end
end

cameraConnection = RunService.RenderStepped:Connect(Update);
table.insert(ActiveConnections.RenderStepped, cameraConnection);

-- ==========================================
-- MIRA SILENCIOSA
-- ==========================================
Tabs.AimbotTab:Section({
	Title = gradient("Mira Silenciosa", Color3.fromHex("#ff0000"), Color3.fromHex("#ff6600"))
});

-- Círculo FOV de Desenho
local FOVCircle = Drawing.new("Circle")
FOVCircle.Visible = false
FOVCircle.Thickness = 1.5
FOVCircle.NumSides = 64
FOVCircle.Radius = 100
FOVCircle.Color = Color3.fromRGB(255, 255, 255)
FOVCircle.Filled = false

-- Obter Assassino mais Próximo do Mouse (Apenas mira no Assassino)
local function GetClosestTargetToMouse()
	local closestPlayer = nil
	local shortestDistance = SilentAimbot.FOV

	for _, player in pairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Name == Murder then
			local targetPart = player.Character:FindFirstChild(SilentAimbot.TargetPart)
			if targetPart then
				local pos, onScreen = CurrentCamera:WorldToViewportPoint(targetPart.Position)
				if onScreen then
					local distance = (Vector2.new(pos.X, pos.Y) - UserInputService:GetMouseLocation()).Magnitude
					if distance <= SilentAimbot.FOV then
						if IsAlive(player) then
							if SilentAimbot.WallCheck then
								local ray = Ray.new(CurrentCamera.CFrame.Position, (targetPart.Position - CurrentCamera.CFrame.Position).Unit * 1000)
								local hit = workspace:FindPartOnRayWithIgnoreList(ray, {LocalPlayer.Character, player.Character})
								if not hit or hit:IsDescendantOf(player.Character) then
									closestPlayer = player
									shortestDistance = distance
								end
							else
								closestPlayer = player
								shortestDistance = distance
							end
						end
					end
				end
			end
		end
	end

	return closestPlayer
end

local lastAutoShoot = 0
local autoShootCooldown = 1.2 -- Evita spam de eventos remotos/colapso

Tabs.AimbotTab:Toggle({
	Title = "Ativar Tiro Automático Silencioso",
	Default = false,
	Callback = function(state)
		SilentAimbot.Enabled = state
		FOVCircle.Visible = state and SilentAimbot.CircleVisible

		if state then
			WindUI:Notify({
				Title = "Mira Silenciosa",
				Content = "Ativada! Assassinos no FOV são atingidos automaticamente.",
				Icon = "check-circle",
				Duration = 3
			})

			SilentAimbot.Connection = RunService.RenderStepped:Connect(function()
				FOVCircle.Position = UserInputService:GetMouseLocation()
				FOVCircle.Radius = SilentAimbot.FOV
				FOVCircle.Color = SilentAimbot.CircleColor
				FOVCircle.Visible = SilentAimbot.CircleVisible and SilentAimbot.Enabled

				if SilentAimbot.Enabled then
					local target = GetClosestTargetToMouse()
					if target and target.Character then
						local targetPart = target.Character:FindFirstChild(SilentAimbot.TargetPart)
						if targetPart then
							local targetVelocity = target.Character:FindFirstChild("HumanoidRootPart") and target.Character.HumanoidRootPart.Velocity or Vector3.new(0, 0, 0)
							local predictedPosition = targetPart.Position + (targetVelocity * SilentAimbot.Prediction)

							local gun = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Gun")
							if gun and gun:FindFirstChild("KnifeLocal") then
								if tick() - lastAutoShoot >= autoShootCooldown then
									pcall(function()
										gun.KnifeLocal.CreateBeam.RemoteFunction:InvokeServer(1, predictedPosition, "AH2")
										lastAutoShoot = tick()
									end)
								end
							end
						end
					end
				end
			end)
			table.insert(ActiveConnections.RenderStepped, SilentAimbot.Connection)
		else
			if SilentAimbot.Connection then
				SilentAimbot.Connection:Disconnect()
				SilentAimbot.Connection = nil
			end
			FOVCircle.Visible = false
		end
	end
});

Tabs.AimbotTab:Dropdown({
	Title = "Parte Alvo",
	Values = {"Head", "HumanoidRootPart", "Torso"},
	Value = "Head",
	Callback = function(selected)
		SilentAimbot.TargetPart = selected
	end
});

Tabs.AimbotTab:Slider({
	Title = "Raio do FOV",
	Value = {
		Min = 50,
		Max = 500,
		Default = 100
	},
	Callback = function(value)
		SilentAimbot.FOV = value
	end
});

Tabs.AimbotTab:Slider({
	Title = "Previsão",
	Step = 0.01,
	Value = {
		Min = 0,
		Max = 0.5,
		Default = 0.165
	},
	Callback = function(value)
		SilentAimbot.Prediction = value
	end
});

Tabs.AimbotTab:Toggle({
	Title = "Mostrar Círculo FOV",
	Default = true,
	Callback = function(state)
		SilentAimbot.CircleVisible = state
		if SilentAimbot.Enabled then
			FOVCircle.Visible = state
		end
	end
});

Tabs.AimbotTab:Colorpicker({
	Title = "Cor do Círculo FOV",
	Default = Color3.fromRGB(255, 255, 255),
	Callback = function(color)
		SilentAimbot.CircleColor = color
	end
});

Tabs.AimbotTab:Toggle({
	Title = "Verificação de Parede",
	Default = true,
	Callback = function(state)
		SilentAimbot.WallCheck = state
	end
});

Tabs.AimbotTab:Code({
	Title = "Como usar:",
	Code = [[Instruções da Mira Silenciosa:
1. Ative o Tiro Automático Silencioso.
2. Segure sua Arma.
3. Mantenha o círculo FOV próximo a portas ou passagens.
4. Se o Assassino entrar no seu círculo, o script atira automaticamente!

Dicas:
• A câmera não irá travar (Mira Silenciosa verdadeira).
• Ajuste o FOV para tornar o círculo de detecção maior ou menor.
• A previsão se ajusta automaticamente ao alvo.
• Parte Alvo: Head = Melhor chance de sucesso contra hit-reg.]]
});

-- ==========================================
-- ABA FARM AUTOMÁTICO
-- ==========================================
local AutoFarm = {
	Enabled = false,
	EggEnabled = false,
	Mode = "Teleporte",
	TeleportDelay = 0,
	MoveSpeed = 50,
	WalkSpeed = 32,
	Connection = nil,
	EggConnection = nil,
	CoinCheckInterval = 0.5,
	CoinContainers = {
		"Factory",
		"Hospital3",
		"MilBase",
		"House2",
		"Workplace",
		"Mansion2",
		"BioLab",
		"Hotel",
		"Bank2",
		"PoliceStation",
		"ResearchFacility",
		"Lobby"
	}
};

local function findNearestCoin()
	local closestCoin = nil;
	local shortestDistance = math.huge;
	local character = LocalPlayer.Character;
	local humanoidRootPart = character and character:FindFirstChild("HumanoidRootPart");
	if not humanoidRootPart then
		return nil;
	end

	for _, containerName in ipairs(AutoFarm.CoinContainers) do
		local container = workspace:FindFirstChild(containerName);
		if container then
			local coinContainer = ((containerName == "Lobby") and container) or container:FindFirstChild("CoinContainer");
			if coinContainer then
				for _, coin in ipairs(coinContainer:GetChildren()) do
					local cv = coin:FindFirstChild("CoinVisual")
					if cv and not cv:GetAttribute("Collected") and coin:IsA("BasePart") then
						local distance = (humanoidRootPart.Position - coin.Position).Magnitude;
						if distance < shortestDistance then
							shortestDistance = distance;
							closestCoin = coin;
						end
					end
				end
			end
		end
	end
	return closestCoin;
end

local function findNearestEgg()
	local closestEgg = nil;
	local shortestDistance = math.huge;
	local character = LocalPlayer.Character;
	local humanoidRootPart = character and character:FindFirstChild("HumanoidRootPart");
	if not humanoidRootPart then
		return nil;
	end

	for _, item in pairs(workspace:GetDescendants()) do
		if item:IsA("BasePart") and (string.find(string.lower(item.Name), "egg") or string.find(string.lower(item.Name), "rareegg")) then
			local distance = (humanoidRootPart.Position - item.Position).Magnitude;
			if distance < shortestDistance then
				shortestDistance = distance;
				closestEgg = item;
			end
		end
	end
	return closestEgg;
end

local function teleportToItem(item)
	if (not item or not LocalPlayer.Character) then
		return;
	end
	local humanoidRootPart = LocalPlayer.Character:FindFirstChild("HumanoidRootPart");
	if not humanoidRootPart then
		return;
	end
	humanoidRootPart.CFrame = CFrame.new(item.Position + Vector3.new(0, 3, 0));
	task.wait(AutoFarm.TeleportDelay);
end

local function smoothMoveToItem(item)
	if (not item or not LocalPlayer.Character) then
		return;
	end
	local humanoidRootPart = LocalPlayer.Character:FindFirstChild("HumanoidRootPart");
	if not humanoidRootPart then
		return;
	end

	local startTime = tick();
	local startPos = humanoidRootPart.Position;
	local endPos = item.Position + Vector3.new(0, 3, 0);
	local distance = (startPos - endPos).Magnitude;
	local duration = distance / AutoFarm.MoveSpeed;

	while (tick() - startTime) < duration and (AutoFarm.Enabled or AutoFarm.EggEnabled) do
		if (not item or not item.Parent) then
			break;
		end
		local progress = math.min((tick() - startTime) / duration, 1);
		humanoidRootPart.CFrame = CFrame.new(startPos:Lerp(endPos, progress));
		task.wait();
	end
end

local function walkToItem(item)
	if (not item or not LocalPlayer.Character) then
		return;
	end
	local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid");
	if not humanoid then
		return;
	end

	humanoid.WalkSpeed = AutoFarm.WalkSpeed;
	humanoid:MoveTo(item.Position + Vector3.new(0, 0, 3));
	local startTime = tick();
	while (AutoFarm.Enabled or AutoFarm.EggEnabled) and (humanoid.MoveDirection.Magnitude > 0) and (tick() - startTime) < 10 do
		task.wait(0.5);
	end
end

local function collectItem(item)
	if (not item or not LocalPlayer.Character) then
		return;
	end
	local humanoidRootPart = LocalPlayer.Character:FindFirstChild("HumanoidRootPart");
	if not humanoidRootPart then
		return;
	end

	firetouchinterest(humanoidRootPart, item, 0);
	firetouchinterest(humanoidRootPart, item, 1);
end

local function farmLoop()
	while AutoFarm.Enabled do
		local coin = findNearestCoin();
		if coin then
			if AutoFarm.Mode == "Teleporte" then
				teleportToItem(coin);
			elseif AutoFarm.Mode == "Suave" then
				smoothMoveToItem(coin);
			else
				walkToItem(coin);
			end
			collectItem(coin);
		else
			task.wait(2);
		end
		task.wait(AutoFarm.CoinCheckInterval);
	end
end

local function eggFarmLoop()
	while AutoFarm.EggEnabled do
		local egg = findNearestEgg();
		if egg then
			if AutoFarm.Mode == "Teleporte" then
				teleportToItem(egg);
			elseif AutoFarm.Mode == "Suave" then
				smoothMoveToItem(egg);
			else
				walkToItem(egg);
			end
			collectItem(egg);
		else
			task.wait(2);
		end
		task.wait(AutoFarm.CoinCheckInterval);
	end
end

Tabs.AutoFarm:Section({
	Title = gradient("Farm de Eventos e Moedas", Color3.fromHex("#FFD700"), Color3.fromHex("#ADD8E6"))
});

Tabs.AutoFarm:Dropdown({
	Title = "Modo de Movimento",
	Values = {
		"Teleporte",
		"Suave",
		"Andar"
	},
	Value = "Teleporte",
	Callback = function(mode)
		AutoFarm.Mode = mode;
	end
});

Tabs.AutoFarm:Slider({
	Title = "Atraso do Teletransporte (seg)",
	Value = {
		Min = 0,
		Max = 1,
		Default = 0,
		Step = 0.1
	},
	Callback = function(value)
		AutoFarm.TeleportDelay = value;
	end
});

Tabs.AutoFarm:Slider({
	Title = "Velocidade de Movimento Suave",
	Value = {
		Min = 20,
		Max = 200,
		Default = 50
	},
	Callback = function(value)
		AutoFarm.MoveSpeed = value;
	end
});

Tabs.AutoFarm:Slider({
	Title = "Velocidade de Caminhada",
	Value = {
		Min = 16,
		Max = 100,
		Default = 32
	},
	Callback = function(value)
		AutoFarm.WalkSpeed = value;
	end
});

Tabs.AutoFarm:Slider({
	Title = "Intervalo de Verificação (seg)",
	Step = 0.1,
	Value = {
		Min = 0.1,
		Max = 2,
		Default = 0.5
	},
	Callback = function(value)
		AutoFarm.CoinCheckInterval = value;
	end
});

Tabs.AutoFarm:Toggle({
	Title = "Ativar Farm Automático (Moedas)",
	Default = false,
	Callback = function(state)
		AutoFarm.Enabled = state;
		if state then
			AutoFarm.Connection = task.spawn(farmLoop);
			WindUI:Notify({
				Title = "Farm Automático",
				Content = "Iniciado!",
				Icon = "check-circle",
				Duration = 2
			});
		else
			if AutoFarm.Connection then
				task.cancel(AutoFarm.Connection);
				AutoFarm.Connection = nil;
			end
			WindUI:Notify({
				Title = "Farm Automático",
				Content = "Parado!",
				Icon = "x-circle",
				Duration = 2
			});
		end
	end
});

Tabs.AutoFarm:Toggle({
	Title = "Ativar Farm Automático de Ovos Raros",
	Default = false,
	Callback = function(state)
		AutoFarm.EggEnabled = state;
		if state then
			AutoFarm.EggConnection = task.spawn(eggFarmLoop);
			WindUI:Notify({
				Title = "Farm de Ovos",
				Content = "Começando a coletar ovos!",
				Icon = "check-circle",
				Duration = 2
			});
		else
			if AutoFarm.EggConnection then
				task.cancel(AutoFarm.EggConnection);
				AutoFarm.EggConnection = nil;
			end
			WindUI:Notify({
				Title = "Farm de Ovos",
				Content = "Parado!",
				Icon = "x-circle",
				Duration = 2
			});
		end
	end
});

Tabs.AutoFarm:Toggle({
	Title = "Ativar Farm Automático de Bolas de Praia",
	Default = false,
	Callback = function(state)
		if state then
			task.spawn(function()
				loadstring(game:HttpGet("https://raw.githubusercontent.com/NoovaScripts/roblox/refs/heads/main/beachballfarm"))()
			end);
			WindUI:Notify({
				Title = "Farm Automático",
				Content = "Farm de Bolas de Praia iniciado!",
				Icon = "check-circle",
				Duration = 2
			});
		end
	end
});

-- ==========================================
-- ABA INOCENTE
-- ==========================================
local GunSystem = {
	AutoGrabEnabled = false,
	NotifyGunDrop = true,
	GunDropCheckInterval = 1,
	ActiveGunDrops = {},
};

local function ScanForGunDrops()
	GunSystem.ActiveGunDrops = {};
	for _, child in pairs(workspace:GetDescendants()) do
		if child.Name == "GunDrop" then
			table.insert(GunSystem.ActiveGunDrops, child);
		end
	end
end

local function EquipGun()
	if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Gun") then
		return true;
	end
	local gun = LocalPlayer.Backpack:FindFirstChild("Gun");
	if gun then
		gun.Parent = LocalPlayer.Character;
		task.wait(0.1);
		return LocalPlayer.Character:FindFirstChild("Gun") ~= nil;
	end
	return false;
end

local function GrabGun(gunDrop)
	if not gunDrop then
		ScanForGunDrops();
		if #GunSystem.ActiveGunDrops == 0 then
			WindUI:Notify({
				Title = "Sistema de Arma",
				Content = "Nenhuma arma disponível!",
				Icon = "x-circle",
				Duration = 3
			});
			return false;
		end
		local nearestGun = nil;
		local minDistance = math.huge;
		local humanoidRootPart = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart");
		if humanoidRootPart then
			for _, drop in ipairs(GunSystem.ActiveGunDrops) do
				local distance = (humanoidRootPart.Position - drop.Position).Magnitude;
				if distance < minDistance then
					minDistance = distance;
					nearestGun = drop;
				end
			end
		end
		gunDrop = nearestGun;
	end

	if gunDrop and LocalPlayer.Character then
		local humanoidRootPart = LocalPlayer.Character:FindFirstChild("HumanoidRootPart");
		if humanoidRootPart then
			humanoidRootPart.CFrame = gunDrop.CFrame;
			task.wait(0.3);
			local prompt = gunDrop:FindFirstChildOfClass("ProximityPrompt");
			if prompt then
				fireproximityprompt(prompt);
				WindUI:Notify({
					Title = "Sistema de Arma",
					Content = "Arma pega!",
					Icon = "check-circle",
					Duration = 3
				});
				return true;
			end
		end
	end
	return false;
end

local function AutoGrabGun()
	while GunSystem.AutoGrabEnabled do
		ScanForGunDrops();
		if #GunSystem.ActiveGunDrops > 0 and LocalPlayer.Character then
			local humanoidRootPart = LocalPlayer.Character:FindFirstChild("HumanoidRootPart");
			if humanoidRootPart then
				local nearestGun = nil;
				local minDistance = math.huge;
				for _, gunDrop in ipairs(GunSystem.ActiveGunDrops) do
					local distance = (humanoidRootPart.Position - gunDrop.Position).Magnitude;
					if distance < minDistance then
						nearestGun = gunDrop;
						minDistance = distance;
					end
				end
				if nearestGun then
					humanoidRootPart.CFrame = nearestGun.CFrame;
					task.wait(0.3);
					local prompt = nearestGun:FindFirstChildOfClass("ProximityPrompt");
					if prompt then
						fireproximityprompt(prompt);
						task.wait(1);
					end
				end
			end
		end
		task.wait(GunSystem.GunDropCheckInterval);
	end
end

Tabs.InnocentTab:Section({
	Title = gradient("Funções de Arma", Color3.fromHex("#001e80"), Color3.fromHex("#16f2d9"))
});

Tabs.InnocentTab:Toggle({
	Title = "Notificar Arma Caída",
	Default = true,
	Callback = function(state)
		gunDropESPEnabled = state;
	end
});

Tabs.InnocentTab:Button({
	Title = "Pegar Arma",
	Callback = function()
		GrabGun();
	end
});

Tabs.InnocentTab:Toggle({
	Title = "Pegar Arma Automaticamente",
	Default = false,
	Callback = function(state)
		GunSystem.AutoGrabEnabled = state;
		if state then
			coroutine.wrap(AutoGrabGun)();
		end
	end
});

Tabs.InnocentTab:Button({
	Title = "Pegar Arma e Atirar no Assassino",
	Callback = function()
		if not (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Gun")) then
			if not GrabGun() then
				return;
			end
			task.wait(0.1);
		end
		if not EquipGun() then
			return;
		end

		local roles = ReplicatedStorage:FindFirstChild("GetPlayerData", true):InvokeServer();
		local murderer = nil;
		for name, data in pairs(roles) do
			if data.Role == "Murderer" then
				murderer = Players:FindFirstChild(name);
				break;
			end
		end

		if not murderer or not murderer.Character then
			WindUI:Notify({
				Title = "Sistema de Arma",
				Content = "Assassino não encontrado!",
				Icon = "x-circle",
				Duration = 3
			});
			return;
		end

		local targetRoot = murderer.Character:FindFirstChild("HumanoidRootPart");
		local localRoot = LocalPlayer.Character:FindFirstChild("HumanoidRootPart");
		if targetRoot and localRoot then
			localRoot.CFrame = targetRoot.CFrame * CFrame.new(0, 0, -4);
			task.wait(0.1);
		end

		local gun = LocalPlayer.Character:FindFirstChild("Gun");
		if gun and gun:FindFirstChild("KnifeLocal") then
			local args = {[1] = 1, [2] = targetRoot.Position, [3] = "AH2"};
			gun.KnifeLocal.CreateBeam.RemoteFunction:InvokeServer(unpack(args));
			WindUI:Notify({
				Title = "Sistema de Arma",
				Content = "Assassino atingido!",
				Icon = "check-circle",
				Duration = 3
			});
		end
	end
});

-- ==========================================
-- ABA ASSASSINO
-- ==========================================
local killActive = false;
local attackDelay = 0.5;

local function equipKnife()
	local character = LocalPlayer.Character;
	if not character then
		return false;
	end
	if character:FindFirstChild("Knife") then
		return true;
	end
	local knife = LocalPlayer.Backpack:FindFirstChild("Knife");
	if knife then
		knife.Parent = character;
		return true;
	end
	return false;
end

local function getNearestTarget()
	local targets = {};
	local roles = ReplicatedStorage:FindFirstChild("GetPlayerData", true):InvokeServer();
	local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart");
	if not localRoot then
		return nil;
	end

	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character then
			local role = nil;
			if roles and roles[player.Name] then
				role = roles[player.Name].Role;
			end
			local humanoid = player.Character:FindFirstChild("Humanoid");
			local targetRoot = player.Character:FindFirstChild("HumanoidRootPart");
			if role and humanoid and humanoid.Health > 0 and targetRoot then
				if role ~= "Murderer" then
					table.insert(targets, {
						Player = player,
						Distance = (localRoot.Position - targetRoot.Position).Magnitude
					});
				end
			end
		end
	end
	table.sort(targets, function(a, b)
		return a.Distance < b.Distance;
	end);
	return targets[1] and targets[1].Player or nil;
end

local function attackTarget(target)
	if not target or not target.Character then
		return false;
	end
	local humanoid = target.Character:FindFirstChild("Humanoid");
	if not humanoid or humanoid.Health <= 0 then
		return false;
	end
	if not equipKnife() then
		return false;
	end

	local targetRoot = target.Character:FindFirstChild("HumanoidRootPart");
	local localRoot = LocalPlayer.Character:FindFirstChild("HumanoidRootPart");
	if targetRoot and localRoot then
		localRoot.CFrame = CFrame.new(targetRoot.Position + ((localRoot.Position - targetRoot.Position).Unit * 2), targetRoot.Position);
	end

	local knife = LocalPlayer.Character:FindFirstChild("Knife");
	if knife and knife:FindFirstChild("Stab") then
		for i = 1, 3 do
			knife.Stab:FireServer("Down");
		end
		return true;
	end
	return false;
end

local function killTargets()
	if killActive then
		return;
	end
	killActive = true;
	WindUI:Notify({
		Title = "Matar Alvos",
		Content = "Iniciando...",
		Icon = "alert-circle",
		Duration = 2
	});

	task.spawn(function()
		while killActive do
			local target = getNearestTarget();
			if not target then
				killActive = false;
				WindUI:Notify({
					Title = "Matar Alvos",
					Content = "Nenhum alvo!",
					Icon = "x-circle",
					Duration = 3
				});
				break;
			end
			attackTarget(target);
			task.wait(attackDelay);
		end
	end);
end

local function stopKilling()
	killActive = false;
	WindUI:Notify({
		Title = "Matar Alvos",
		Content = "Parado",
		Icon = "x-circle",
		Duration = 2
	});
end

Tabs.MurderTab:Section({
	Title = gradient("Funções de Matar", Color3.fromHex("#e80909"), Color3.fromHex("#630404"))
});

Tabs.MurderTab:Toggle({
	Title = "Matar Todos",
	Default = false,
	Callback = function(state)
		if state then
			killTargets();
		else
			stopKilling();
		end
	end
});

Tabs.MurderTab:Slider({
	Title = "Atraso de Ataque",
	Step = 0.1,
	Value = {
		Min = 0.1,
		Max = 2,
		Default = 0.5
	},
	Callback = function(value)
		attackDelay = value;
	end
});

Tabs.MurderTab:Button({
	Title = "Equipar Faca",
	Callback = function()
		if equipKnife() then
			WindUI:Notify({
				Title = "Faca",
				Content = "Equipada!",
				Icon = "check-circle",
				Duration = 2
			});
		else
			WindUI:Notify({
				Title = "Faca",
				Content = "Não encontrada!",
				Icon = "x-circle",
				Duration = 2
			});
		end
	end
});

-- ==========================================
-- ABA XERIFE
-- ==========================================
local shotButton = nil;
local shotButtonFrame = nil;
local shotButtonActive = false;
local shotType = "Padrão";
local buttonSize = 50;

local function RemoveShotButton()
	if shotButton then
		shotButton:Destroy();
		shotButton = nil;
	end
	if shotButtonFrame then
		shotButtonFrame:Destroy();
		shotButtonFrame = nil;
	end
	local screenGui = CoreGui:FindFirstChild("WindUI_SheriffGui");
	if screenGui then
		screenGui:Destroy();
	end
	shotButtonActive = false;
end

local function CreateShotButton()
	if shotButton then
		return;
	end
	local screenGui = Instance.new("ScreenGui");
	screenGui.Name = "WindUI_SheriffGui";
	screenGui.Parent = CoreGui;
	screenGui.ResetOnSpawn = false;
	screenGui.DisplayOrder = 999;

	shotButtonFrame = Instance.new("Frame");
	shotButtonFrame.Size = UDim2.new(0, buttonSize, 0, buttonSize);
	shotButtonFrame.Position = UDim2.new(1, -buttonSize - 20, 0.5, -buttonSize / 2);
	shotButtonFrame.AnchorPoint = Vector2.new(1, 0.5);
	shotButtonFrame.BackgroundTransparency = 1;
	shotButtonFrame.ZIndex = 100;

	shotButton = Instance.new("TextButton");
	shotButton.Size = UDim2.new(1, 0, 1, 0);
	shotButton.BackgroundColor3 = Color3.fromRGB(0, 100, 255);
	shotButton.Text = "ATIRAR";
	shotButton.TextSize = 14;
	shotButton.Font = Enum.Font.GothamBold;
	shotButton.TextColor3 = Color3.fromRGB(255, 255, 255);
	shotButton.ZIndex = 101;

	local corner = Instance.new("UICorner");
	corner.CornerRadius = UDim.new(0.3, 0);
	corner.Parent = shotButton;

	shotButton.MouseButton1Click:Connect(function()
		if not LocalPlayer.Character then
			return;
		end
		local roles = ReplicatedStorage:FindFirstChild("GetPlayerData", true):InvokeServer();
		local murderer = nil;
		for name, data in pairs(roles) do
			if data.Role == "Murderer" then
				murderer = Players:FindFirstChild(name);
				break;
			end
		end
		if not murderer or not murderer.Character then
			return;
		end

		local gun = LocalPlayer.Character:FindFirstChild("Gun") or LocalPlayer.Backpack:FindFirstChild("Gun");
		if gun and not LocalPlayer.Character:FindFirstChild("Gun") then
			gun.Parent = LocalPlayer.Character;
		end

		if shotType == "Teleporte" then
			local targetRoot = murderer.Character:FindFirstChild("HumanoidRootPart");
			local localRoot = LocalPlayer.Character:FindFirstChild("HumanoidRootPart");
			if targetRoot and localRoot then
				localRoot.CFrame = targetRoot.CFrame * CFrame.new(0, 0, -4);
			end
		end

		gun = LocalPlayer.Character:FindFirstChild("Gun");
		if gun and gun:FindFirstChild("KnifeLocal") then
			local targetPart = murderer.Character:FindFirstChild("HumanoidRootPart");
			if targetPart then
				local args = {[1] = 10, [2] = targetPart.Position, [3] = "AH2"};
				gun.KnifeLocal.CreateBeam.RemoteFunction:InvokeServer(unpack(args));
			end
		end
	end);

	shotButton.Parent = shotButtonFrame;
	shotButtonFrame.Parent = screenGui;
	shotButtonActive = true;
end

Tabs.SheriffTab:Section({
	Title = gradient("Funções de Tiro", Color3.fromHex("#001e80"), Color3.fromHex("#16f2d9"))
});

Tabs.SheriffTab:Dropdown({
	Title = "Tipo de Tiro",
	Values = {"Padrão", "Teleporte"},
	Value = "Padrão",
	Callback = function(selected)
		shotType = selected;
	end
});

Tabs.SheriffTab:Section({
	Title = gradient("Botão de Tiro", Color3.fromHex("#001e80"), Color3.fromHex("#16f2d9"))
});

Tabs.SheriffTab:Button({
	Title = "Alternar Botão de Tiro",
	Callback = function()
		if shotButtonActive then
			RemoveShotButton();
		else
			CreateShotButton();
		end
	end
});

Tabs.SheriffTab:Slider({
	Title = "Tamanho do Botão",
	Step = 1,
	Value = {
		Min = 10,
		Max = 100,
		Default = 50
	},
	Callback = function(size)
		buttonSize = size;
		if shotButtonActive then
			RemoveShotButton();
			CreateShotButton();
		end
	end
});

-- ==========================================
-- ABA SERVIDOR
-- ==========================================
Tabs.ServerTab:Section({
	Title = gradient("Gerenciamento de Servidor", Color3.fromHex("#ff6600"), Color3.fromHex("#ff9900"))
});

Tabs.ServerTab:Button({
	Title = "Reentrar no Servidor",
	Desc = "Reentrar no servidor atual",
	Callback = function()
		TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId, LocalPlayer);
	end
});

Tabs.ServerTab:Button({
	Title = "Ir para Outro Servidor",
	Desc = "Entrar em um servidor diferente",
	Callback = function()
		local success, result = pcall(function()
			return HttpService:JSONDecode(game:HttpGet("https://games.roproxy.com/v1/games/" .. tostring(game.PlaceId) .. "/servers/Public?sortOrder=Asc&limit=100"));
		end);
		if success and result and result.data then
			local servers = {};
			for _, server in ipairs(result.data) do
				if server.id ~= game.JobId then
					table.insert(servers, server);
				end
			end
			if #servers > 0 then
				TeleportService:TeleportToPlaceInstance(game.PlaceId, servers[math.random(#servers)].id);
			else
				TeleportService:Teleport(game.PlaceId);
			end
		else
			TeleportService:Teleport(game.PlaceId);
		end
	end
});

Tabs.ServerTab:Button({
	Title = "Entrar em Servidor com Poucos Jogadores",
	Desc = "Entrar em servidor com menos jogadores",
	Callback = function()
		local success, result = pcall(function()
			return HttpService:JSONDecode(game:HttpGet("https://games.roproxy.com/v1/games/" .. tostring(game.PlaceId) .. "/servers/Public?sortOrder=Asc&limit=100"));
		end);
		if success and result and result.data then
			local servers = {};
			for _, server in ipairs(result.data) do
				if server.id ~= game.JobId and server.playing < server.maxPlayers then
					table.insert(servers, server);
				end
			end
			table.sort(servers, function(a, b)
				return a.playing < b.playing;
			end);
			if #servers > 0 then
				TeleportService:TeleportToPlaceInstance(game.PlaceId, servers[1].id);
			else
				TeleportService:Teleport(game.PlaceId);
			end
		else
			TeleportService:Teleport(game.PlaceId);
		end
	end
});

Tabs.ServerTab:Section({
	Title = gradient("Informações do Servidor", Color3.fromHex("#00eaff"), Color3.fromHex("#002a2e"))
});

local serverInfo = Tabs.ServerTab:Paragraph({
	Title = "Detalhes do Servidor",
	Desc = "Carregando...",
	Image = "server",
	ImageSize = 32
});

task.spawn(function()
	while task.wait(5) do
		local success, result = pcall(function()
			return HttpService:JSONDecode(game:HttpGet("https://games.roproxy.com/v1/games/" .. tostring(game.PlaceId) .. "/servers/Public?sortOrder=Asc&limit=100"));
		end);
		if success and result and result.data then
			for _, server in ipairs(result.data) do
				if server.id == game.JobId then
					local ping = "N/A";
					pcall(function()
						ping = game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValueString();
					end);
					serverInfo:SetDesc("Servidor: " .. string.sub(game.JobId, 1, 8) .. "...\nJogadores: " .. server.playing .. "/" .. server.maxPlayers .. "\nPing: " .. ping);
					break;
				end
			end
		end
	end
end);

Tabs.ServerTab:Section({
	Title = gradient("Ferramentas de Segurança", Color3.fromHex("#e80909"), Color3.fromHex("#630404"))
});

local autoReportActive = false

Tabs.ServerTab:Toggle({
	Title = "Reportar Assassino Automaticamente (Trapaça)",
	Default = false,
	Callback = function(state)
		autoReportActive = state;
		if state then
			task.spawn(function()
				while autoReportActive do
					if KnownMurderer and Players:FindFirstChild(KnownMurderer) then
						pcall(function()
							Players:ReportAbuse(Players[KnownMurderer], "Cheating/Exploiting")
						end)
						task.wait(60) -- Evita ratelimiting
					else
						task.wait(2)
					end
				end
			end)
		end
	end
});

-- ==========================================
-- ABA CONFIGURAÇÕES
-- ==========================================
local Settings = {
	Hitbox = {
		Enabled = false,
		Size = 5,
		Color = Color3.new(1, 0, 0),
		Adornments = {}
	},
	Noclip = {
		Enabled = false,
		Connection = nil
	},
	AntiAFK = {
		Enabled = false,
		Connection = nil
	}
};

local function ToggleNoclip(state)
	if state then
		Settings.Noclip.Connection = RunService.Stepped:Connect(function()
			if LocalPlayer.Character then
				for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
					if part:IsA("BasePart") then
						part.CanCollide = false;
					end
				end
			end
		end);
	elseif Settings.Noclip.Connection then
		Settings.Noclip.Connection:Disconnect();
	end
end

local function UpdateHitboxes()
	for _, plr in pairs(Players:GetPlayers()) do
		if plr ~= LocalPlayer then
			local chr = plr.Character;
			local box = Settings.Hitbox.Adornments[plr];
			if chr and Settings.Hitbox.Enabled then
				local root = chr:FindFirstChild("HumanoidRootPart");
				if root then
					if not box then
						box = Instance.new("BoxHandleAdornment");
						box.Adornee = root;
						box.Size = Vector3.new(Settings.Hitbox.Size, Settings.Hitbox.Size, Settings.Hitbox.Size);
						box.Color3 = Settings.Hitbox.Color;
						box.Transparency = 0.4;
						box.ZIndex = 10;
						box.Parent = root;
						Settings.Hitbox.Adornments[plr] = box;
					end
				end
			elseif box then
				box:Destroy();
				Settings.Hitbox.Adornments[plr] = nil;
			end
		end
	end
end

local function ToggleAntiAFK(state)
	if state then
		Settings.AntiAFK.Connection = RunService.Heartbeat:Connect(function()
			pcall(function()
				game:GetService("VirtualUser"):CaptureController();
				game:GetService("VirtualUser"):ClickButton2(Vector2.new());
			end);
		end);
	elseif Settings.AntiAFK.Connection then
		Settings.AntiAFK.Connection:Disconnect();
	end
end

Tabs.SettingsTab:Section({
	Title = gradient("Hitboxes", Color3.fromHex("#ff0000"), Color3.fromHex("#ff8800"))
});

Tabs.SettingsTab:Toggle({
	Title = "Hitboxes",
	Callback = function(state)
		Settings.Hitbox.Enabled = state;
		if state then
			local conn = RunService.Heartbeat:Connect(UpdateHitboxes);
			table.insert(ActiveConnections.RenderStepped, conn);
		else
			for _, box in pairs(Settings.Hitbox.Adornments) do
				if box then
					box:Destroy();
				end
			end
			Settings.Hitbox.Adornments = {};
		end
	end
});

Tabs.SettingsTab:Slider({
	Title = "Tamanho da Hitbox",
	Value = {
		Min = 1,
		Max = 20,
		Default = 5
	},
	Callback = function(val)
		Settings.Hitbox.Size = val;
	end
});

Tabs.SettingsTab:Colorpicker({
	Title = "Cor da Hitbox",
	Default = Color3.new(1, 0, 0),
	Callback = function(col)
		Settings.Hitbox.Color = col;
	end
});

Tabs.SettingsTab:Section({
	Title = gradient("Personagem", Color3.fromHex("#00eaff"), Color3.fromHex("#002a2e"))
});

Tabs.SettingsTab:Toggle({
	Title = "Anti-AFK",
	Callback = function(state)
		Settings.AntiAFK.Enabled = state;
		ToggleAntiAFK(state);
	end
});

Tabs.SettingsTab:Toggle({
	Title = "NoClip (Atravessar Paredes)",
	Callback = function(state)
		Settings.Noclip.Enabled = state;
		ToggleNoclip(state);
	end
});

-- ==========================================
-- ABA NOVIDADES
-- ==========================================
Tabs.ChangelogsTab:Section({
	Title = gradient("Versão Atual", Color3.fromHex("#00ff40"), Color3.fromHex("#008f11"))
});

Tabs.ChangelogsTab:Code({
	Title = "v2.0 - Atualização Corrigida",
	Code = [[
Recursos:
• Rastreamento e limpeza de conexões corrigidos
• Estado do toggle mestre Ctrl+M corrigido
• Adicionado Sistema de Evasão do Assassino
• Adicionada Esquiva Preditiva de Arma
• Adicionadas ferramentas avançadas de proteção local
• Melhorada a segurança contra nulos e throttle de cargo
• ESP Completo (Assassino, Xerife, Inocente, Arma)
• ESP de Desenho (Nome, Caixa, Rastreadores)
• Mira Silenciosa (Segure Botão Direito)
• Mira Automática de Câmera (Espectador e Trava)
• Farm Automático (Moedas, Bolas de Praia, Ovos)
• Sistema de Teleporte
• Matar Todos (Assassino)
• Pegar Arma e Atirar Automático
• Botão de Tiro para Mobile
• Alertas de Detecção do Assassino
• Sistema de Reporte Automático
• Troca de Servidor e Gerenciamento Fast API
• Personalização de Tema
• Sistema de Toggle Ctrl+M
]]
});

-- ==========================================
-- ABA REDES SOCIAIS
-- ==========================================
Tabs.SocialsTab:Section({
	Title = gradient("Desenvolvedor", Color3.fromHex("#FFD700"), Color3.fromHex("#FFA500"))
});

Tabs.SocialsTab:Paragraph({
	Title = "Eclipse feito por Wellyson José",
	Desc = "Por favor, siga-me no Roblox e inscreva-se no meu canal do YouTube! Seu apoio me ajuda a criar mais scripts.",
	Image = "rbxassetid://72462144048455",
	ImageSize = 64
});

Tabs.SocialsTab:Section({
	Title = gradient("Links", Color3.fromHex("#00eaff"), Color3.fromHex("#002a2e"))
});

Tabs.SocialsTab:Button({
	Title = "Copiar YouTube",
	Callback = function()
		if pcall(setclipboard, "https://www.youtube.com/@bxrutodev") then
			WindUI:Notify({
				Title = "Copiado!",
				Content = "Link do YouTube copiado! Por favor se inscreva!",
				Icon = "check-circle",
				Duration = 3
			});
		end
	end
});

Tabs.SocialsTab:Button({
	Title = "Copiar Roblox",
	Callback = function()
		if pcall(setclipboard, "https://www.roblox.com/users/3814718003/profile") then
			WindUI:Notify({
				Title = "Copiado!",
				Content = "Perfil do Roblox copiado! Por favor me siga!",
				Icon = "check-circle",
				Duration = 3
			});
		end
	end
});

-- ==========================================
-- ABA CONFIGURAÇÃO
-- ==========================================
Tabs.WindowTab:Section({
	Title = "Configurações da Janela"
});

local themeValues = {};
for name, _ in pairs(WindUI:GetThemes()) do
	table.insert(themeValues, name);
end

Tabs.WindowTab:Dropdown({
	Title = "Selecionar Tema",
	Values = themeValues,
	Callback = function(theme)
		WindUI:SetTheme(theme);
	end
});

Tabs.WindowTab:Toggle({
	Title = "Alternar Transparência",
	Callback = function(e)
		Window:ToggleTransparency(e);
	end,
	Value = WindUI:GetTransparency()
});

Tabs.WindowTab:Section({
	Title = "Salvar/Carregar"
});

local fileNameInput = "";
Tabs.WindowTab:Input({
	Title = "Nome do Arquivo",
	PlaceholderText = "Digite o nome do arquivo",
	Callback = function(text)
		fileNameInput = text;
	end
});

Tabs.WindowTab:Button({
	Title = "Salvar Configuração",
	Callback = function()
		if fileNameInput ~= "" then
			makefolder("EclipseScript");
			writefile("EclipseScript/" .. fileNameInput .. ".json", HttpService:JSONEncode({
				Theme = WindUI:GetCurrentTheme(),
				Transparent = WindUI:GetTransparency()
			}));
			WindUI:Notify({
				Title = "Salvo!",
				Content = "Configuração salva!",
				Icon = "check-circle",
				Duration = 2
			});
		end
	end
});

Tabs.WindowTab:Button({
	Title = "Carregar Configuração",
	Callback = function()
		if fileNameInput ~= "" and isfile("EclipseScript/" .. fileNameInput .. ".json") then
			local data = HttpService:JSONDecode(readfile("EclipseScript/" .. fileNameInput .. ".json"));
			if data.Theme then
				WindUI:SetTheme(data.Theme);
			end
			if data.Transparent then
				Window:ToggleTransparency(data.Transparent);
			end
			WindUI:Notify({
				Title = "Carregado!",
				Content = "Configuração carregada!",
				Icon = "check-circle",
				Duration = 2
			});
		end
	end
});

-- ==========================================
-- ABA TEMAS
-- ==========================================
Tabs.CreateThemeTab:Section({
	Title = "Tema Personalizado"
});

local newThemeName = "";
Tabs.CreateThemeTab:Input({
	Title = "Nome do Tema",
	PlaceholderText = "Digite o nome do tema",
	Callback = function(text)
		newThemeName = text;
	end
});

local customColors = {
	Accent = Color3.fromHex("#18181b"),
	Outline = Color3.fromHex("#FFFFFF"),
	Text = Color3.fromHex("#FFFFFF"),
	Background = Color3.fromHex("#0e0e10")
};

Tabs.CreateThemeTab:Colorpicker({
	Title = "Cor de Destaque",
	Default = customColors.Accent,
	Callback = function(color)
		customColors.Accent = color;
	end
});

Tabs.CreateThemeTab:Colorpicker({
	Title = "Cor do Contorno",
	Default = customColors.Outline,
	Callback = function(color)
		customColors.Outline = color;
	end
});

Tabs.CreateThemeTab:Colorpicker({
	Title = "Cor do Texto",
	Default = customColors.Text,
	Callback = function(color)
		customColors.Text = color;
	end
});

Tabs.CreateThemeTab:Colorpicker({
	Title = "Cor de Fundo",
	Default = customColors.Background,
	Callback = function(color)
		customColors.Background = color;
	end
});

Tabs.CreateThemeTab:Button({
	Title = "Criar Tema",
	Callback = function()
		if newThemeName ~= "" then
			WindUI:AddTheme({
				Name = newThemeName,
				Accent = tostring(customColors.Accent),
				Outline = tostring(customColors.Outline),
				Text = tostring(customColors.Text),
				Background = tostring(customColors.Background)
			});
			WindUI:SetTheme(newThemeName);
			WindUI:Notify({
				Title = "Tema Criado!",
				Content = "Tema " .. newThemeName .. " aplicado!",
				Icon = "check-circle",
				Duration = 3
			});
		end
	end
});

-- ==========================================
-- TOGGLE MESTRE / LIMPEZA
-- ==========================================
local function SetSystemsEnabled(enabled)
	ScriptEnabled = enabled
	if not enabled then
		if SilentAimbot.Connection then
			SilentAimbot.Connection:Disconnect()
			SilentAimbot.Connection = nil
		end
		FOVCircle.Visible = false
		killActive = false
		AutoFarm.Enabled = false
		AutoFarm.EggEnabled = false
		if AutoFarm.Connection then pcall(task.cancel, AutoFarm.Connection); AutoFarm.Connection = nil end
		if AutoFarm.EggConnection then pcall(task.cancel, AutoFarm.EggConnection); AutoFarm.EggConnection = nil end
		RemoveShotButton()
		RemoveAllHighlights()
	else
		WindUI:Notify({Title="Script Alternado", Content="Sistemas ativos.", Icon="check-circle", Duration=3, Color="Green"})
	end
end

getgenv().ToggleMM2Script = function()
	SetSystemsEnabled(not ScriptEnabled)
	if not ScriptEnabled then
		WindUI:Notify({Title="Script Alternado", Content="Sistemas pausados. Pressione Ctrl+M para restaurar.", Icon="power-off", Duration=3, Color="Red"})
	end
end

-- ==========================================
-- SISTEMAS EXTRAS DA v2
-- ==========================================
local EvasionSystem = {Enabled=false, TriggerDistance=35, Connection=nil}
local MurdSystem = {PredictiveDodge=false, DodgeConnection=nil}

Tabs.InnocentTab:Section({
	Title = gradient("Evasão do Assassino", Color3.fromHex("#00ff40"), Color3.fromHex("#008f11"))
});

Tabs.InnocentTab:Toggle({
	Title = "Sistema de Evasão do Assassino",
	Default = false,
	Callback = function(state)
		EvasionSystem.Enabled = state
		if EvasionSystem.Connection then EvasionSystem.Connection:Disconnect(); EvasionSystem.Connection=nil end
		if state then
			EvasionSystem.Connection = RunService.Heartbeat:Connect(function()
				if not ScriptEnabled or not EvasionSystem.Enabled then return end
				local char = LocalPlayer.Character
				local root = char and char:FindFirstChild("HumanoidRootPart")
				local hum = char and char:FindFirstChildOfClass("Humanoid")
				local murderer = Murder and Players:FindFirstChild(Murder)
				local mroot = murderer and murderer.Character and murderer.Character:FindFirstChild("HumanoidRootPart")
				if root and hum and mroot and (root.Position-mroot.Position).Magnitude <= EvasionSystem.TriggerDistance then
					local dir = root.Position-mroot.Position
					if dir.Magnitude > 0 then
						hum:MoveTo(root.Position + dir.Unit*40)
					end
				end
			end)
			table.insert(ActiveConnections.Heartbeat, EvasionSystem.Connection)
		end
	end
});

Tabs.InnocentTab:Slider({
	Title = "Raio de Evasão",
	Value = {Min=15, Max=100, Default=35},
	Callback = function(v) EvasionSystem.TriggerDistance=v end
});

Tabs.MurderTab:Section({
	Title = gradient("Esquiva Preditiva", Color3.fromHex("#ff0000"), Color3.fromHex("#800000"))
});

Tabs.MurderTab:Toggle({
	Title = "Esquiva Preditiva de Arma",
	Default = false,
	Callback = function(state)
		MurdSystem.PredictiveDodge = state
		if MurdSystem.DodgeConnection then MurdSystem.DodgeConnection:Disconnect(); MurdSystem.DodgeConnection=nil end
		if state then
			MurdSystem.DodgeConnection = RunService.Heartbeat:Connect(function()
				if not ScriptEnabled or not MurdSystem.PredictiveDodge or LocalPlayer.Name ~= Murder then return end
				local char = LocalPlayer.Character
				local root = char and char:FindFirstChild("HumanoidRootPart")
				local sheriff = Sheriff and Players:FindFirstChild(Sheriff)
				local shead = sheriff and sheriff.Character and sheriff.Character:FindFirstChild("Head")
				local sgun = sheriff and sheriff.Character and sheriff.Character:FindFirstChild("Gun")
				if root and shead and sgun then
					local delta = root.Position-shead.Position
					if delta.Magnitude > 0 and shead.CFrame.LookVector:Dot(delta.Unit) > 0.85 then
						local hum = char:FindFirstChildOfClass("Humanoid")
						if hum then
							hum:MoveTo(root.Position + shead.CFrame.RightVector*(math.random(0,1)==0 and -18 or 18))
							hum.Jump = true
						end
					end
				end
			end)
			table.insert(ActiveConnections.Heartbeat, MurdSystem.DodgeConnection)
		end
	end
});
-- Garantir que todos os callbacks de alta frequência respeitem o switch mestre.
local previousUpdateRoles = UpdateRoles
UpdateRoles = function(...)
	if not ScriptEnabled then return end
	return previousUpdateRoles(...)
end

-- Detecção de Ctrl + M
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then
		return
	end

	if input.KeyCode == Enum.KeyCode.LeftControl or input.KeyCode == Enum.KeyCode.RightControl then
		CtrlPressed = true;
	end

	if CtrlPressed and input.KeyCode == Enum.KeyCode.M then
		getgenv().ToggleMM2Script();
	end
end);

UserInputService.InputEnded:Connect(function(input)
	if input.KeyCode == Enum.KeyCode.LeftControl or input.KeyCode == Enum.KeyCode.RightControl then
		CtrlPressed = false;
	end
end);

-- Adicionar botão de toggle nas Configurações
Tabs.SettingsTab:Section({
	Title = gradient("Alternar Script", Color3.fromHex("#ff0000"), Color3.fromHex("#ff6600"))
});

Tabs.SettingsTab:Button({
	Title = "Alternar Script (Ctrl + M)",
	Desc = "Pressione Ctrl+M para ativar/desativar",
	Callback = function()
		getgenv().ToggleMM2Script();
	end
});

print("Eclipse Script Carregado - Pressione Ctrl+M para alternar o script");
