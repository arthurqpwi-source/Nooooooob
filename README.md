--[[
    Painel Rayfield: Noob Hack
    Abas: Principal (Pegar Rápido, InfJump, Anti‑Ragdoll, Alerta Brainrot) | Desync | Configurações
--]]

local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "Noob Hack",
    Icon = 0,
    LoadingTitle = "Noob Hack Interface",
    LoadingSubtitle = "pela Sirius",
    Theme = "Default",

    ConfigurationSaving = {
        Enabled = true,
        FolderName = nil,
        FileName = "NoobHack_Config"
    },

    KeySystem = false,
    KeySettings = {
        Title = "Sistema de Chave",
        Subtitle = "Verificação de Acesso",
        Note = "Obtenha a chave no Discord",
        FileName = "NoobHack_Key",
        SaveKey = true,
        GrabKeyFromSite = false,
        Key = {"Noob123"}
    }
})

local TabPrincipal = Window:CreateTab("Principal", 4483362458)  -- Nome alterado aqui
local TabDesync = Window:CreateTab("Desync", 4483362458)
local TabConfig = Window:CreateTab("Configurações", 4483362458)

-- ====================== ABA PRINCIPAL ======================
TabPrincipal:CreateSection("Coleta Otimizada")

-- Pegar Rápido (Estilo Noob Prime)
local pegarRapidoEnabled = false

local function enablePegarRapido()
    pegarRapidoEnabled = true
    task.spawn(function()
        while pegarRapidoEnabled do
            for _, obj in ipairs(workspace:GetDescendants()) do
                if obj:IsA("ProximityPrompt") then
                    obj.MaxActivationDistance = 30
                    obj.HoldDuration = 0
                elseif obj:IsA("ClickDetector") then
                    obj.MaxActivationDistance = 30
                end
            end
            task.wait(2)
        end
    end)
end

local function disablePegarRapido()
    pegarRapidoEnabled = false
end

TabPrincipal:CreateToggle({
    Name = "Pegar Rápido (Alcance + Instantâneo)",
    CurrentValue = false,
    Flag = "PegarRapidoToggle",
    Callback = function(Value)
        if Value then
            enablePegarRapido()
        else
            disablePegarRapido()
        end
    end,
})

TabPrincipal:CreateLabel("Aumenta o alcance de coleta e remove a barra de progresso.\nAtualiza a cada 2s (lag free).", Color3.fromRGB(200, 200, 200))

TabPrincipal:CreateSection("Movimento")

-- InfJump (Bloco Instantâneo)
local infJumpEnabled = false
local infJumpConn = nil

local function onJump()
    local player = game.Players.LocalPlayer
    local char = player.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local root = char.HumanoidRootPart

    local block = Instance.new("Part")
    block.Name = "InfJumpTempBlock"
    block.Anchored = true
    block.CanCollide = true
    block.Size = Vector3.new(5, 1, 5)
    block.Transparency = 1
    block.CFrame = CFrame.new(root.Position - Vector3.new(0, 3.5, 0))
    block.Parent = workspace

    task.delay(0.15, function()
        if block and block.Parent then
            block:Destroy()
        end
    end)
end

local function enableInfJump()
    infJumpEnabled = true
    infJumpConn = game:GetService("UserInputService").JumpRequest:Connect(function()
        if infJumpEnabled then
            onJump()
        end
    end)
end

local function disableInfJump()
    infJumpEnabled = false
    if infJumpConn then
        infJumpConn:Disconnect()
        infJumpConn = nil
    end
end

TabPrincipal:CreateToggle({
    Name = "InfJump (Bloco Instantâneo)",
    CurrentValue = false,
    Flag = "InfJumpToggle",
    Callback = function(Value)
        if Value then
            enableInfJump()
        else
            disableInfJump()
        end
    end,
})

TabPrincipal:CreateLabel("Ao pular, um bloco invisível aparece por um instante,\nte impulsionando para cima.", Color3.fromRGB(200, 200, 200))

-- Anti-Ragdoll (ANDAR SEM CAIR)
local antiRagdollEnabled = false
local antiRagdollConn = nil

local function enableAntiRagdoll()
    antiRagdollEnabled = true
    local player = game.Players.LocalPlayer
    antiRagdollConn = game:GetService("RunService").Heartbeat:Connect(function()
        if not antiRagdollEnabled then return end
        local char = player.Character
        if not char or not char:FindFirstChild("Humanoid") then return end
        local hum = char.Humanoid
        local state = hum:GetState()
        if state == Enum.HumanoidStateType.Physics or state == Enum.HumanoidStateType.FallingDown then
            hum:ChangeState(Enum.HumanoidStateType.RunningNoPhysics)
        end
    end)
end

local function disableAntiRagdoll()
    antiRagdollEnabled = false
    if antiRagdollConn then
        antiRagdollConn:Disconnect()
        antiRagdollConn = nil
    end
end

TabPrincipal:CreateToggle({
    Name = "Anti‑Ragdoll (Andar sem cair)",
    CurrentValue = false,
    Flag = "AntiRagdollToggle",
    Callback = function(Value)
        if Value then
            enableAntiRagdoll()
        else
            disableAntiRagdoll()
        end
    end,
})

TabPrincipal:CreateLabel("Impede que você seja derrubado ou entre em ragdoll.\nMantém o personagem sempre de pé.", Color3.fromRGB(200, 200, 200))

-- Alerta Brainrot (Detector de Pets Raros)
TabPrincipal:CreateSection("Alertas de Itens Raros")

local brainrotAlertEnabled = false
local brainrotConnections = {}
local brainrotCharConnections = {}

local function showBrainrotAlert(playerName, itemName, moneyAmount)
    local player = game.Players.LocalPlayer
    local gui = Instance.new("ScreenGui")
    gui.Parent = player.PlayerGui
    gui.Name = "BrainrotAlert"

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 350, 0, 120)
    frame.Position = UDim2.new(0.5, -175, 0, 10)
    frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    frame.BorderSizePixel = 0
    frame.BackgroundTransparency = 0.3
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 12)
    frame.Parent = gui

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, 0, 0, 30)
    title.Position = UDim2.new(0, 0, 0, 5)
    title.Text = "🔥 BRAINROT ENCONTRADO! 🔥"
    title.TextColor3 = Color3.fromRGB(255, 215, 0)
    title.Font = Enum.Font.GothamBold
    title.TextSize = 18
    title.BackgroundTransparency = 1
    title.Parent = frame

    local ownerLabel = Instance.new("TextLabel")
    ownerLabel.Size = UDim2.new(1, 0, 0, 25)
    ownerLabel.Position = UDim2.new(0, 0, 0, 35)
    ownerLabel.Text = "Jogador: " .. playerName
    ownerLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    ownerLabel.Font = Enum.Font.Gotham
    ownerLabel.TextSize = 14
    ownerLabel.BackgroundTransparency = 1
    ownerLabel.Parent = frame

    local nameLabel = Instance.new("TextLabel")
    nameLabel.Size = UDim2.new(1, 0, 0, 25)
    nameLabel.Position = UDim2.new(0, 0, 0, 60)
    nameLabel.Text = "Nome: " .. itemName
    nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    nameLabel.Font = Enum.Font.Gotham
    nameLabel.TextSize = 14
    nameLabel.BackgroundTransparency = 1
    nameLabel.Parent = frame

    local moneyLabel = Instance.new("TextLabel")
    moneyLabel.Size = UDim2.new(1, 0, 0, 25)
    moneyLabel.Position = UDim2.new(0, 0, 0, 85)
    moneyLabel.Text = "💰 Dinheiro: " .. tostring(moneyAmount)
    moneyLabel.TextColor3 = Color3.fromRGB(0, 255, 100)
    moneyLabel.Font = Enum.Font.Gotham
    moneyLabel.TextSize = 14
    moneyLabel.BackgroundTransparency = 1
    moneyLabel.Parent = frame

    task.delay(6, function()
        gui:Destroy()
    end)
end

local function scanCharacterForBrainrot(char, playerName)
    if not char then return end
    for _, obj in ipairs(char:GetDescendants()) do
        if obj:IsA("Model") or obj:IsA("Part") then
            local name = obj.Name
            if string.find(string.lower(name), "brainrot") then
                local money = 0
                local valueObj = obj:FindFirstChild("Value") or obj:FindFirstChild("Cash") or obj:FindFirstChild("Money")
                if valueObj and valueObj:IsA("NumberValue") then
                    money = valueObj.Value
                elseif valueObj and valueObj:IsA("StringValue") then
                    money = valueObj.Value
                end
                if money == 0 then
                    pcall(function()
                        money = obj:GetAttribute("Value") or obj:GetAttribute("Cash") or obj:GetAttribute("Money") or 0
                    end)
                end
                showBrainrotAlert(playerName, name, money)
                break
            end
        end
    end
end

local function startBrainrotMonitoring()
    local Players = game.Players
    local LocalPlayer = Players.LocalPlayer

    local playerAddedConn = Players.PlayerAdded:Connect(function(player)
        if not brainrotAlertEnabled or player == LocalPlayer then return end

        local charAddedConn
        charAddedConn = player.CharacterAdded:Connect(function(char)
            if not brainrotAlertEnabled then
                charAddedConn:Disconnect()
                return
            end
            task.wait(0.5)
            scanCharacterForBrainrot(char, player.Name)
        end)
        brainrotCharConnections[player] = charAddedConn

        if player.Character then
            task.wait(0.5)
            scanCharacterForBrainrot(player.Character, player.Name)
        end
    end)

    table.insert(brainrotConnections, playerAddedConn)

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            task.spawn(function()
                task.wait(0.5)
                scanCharacterForBrainrot(player.Character, player.Name)
            end)
        end
    end
end

local function enableBrainrotAlert()
    brainrotAlertEnabled = true
    startBrainrotMonitoring()
end

local function disableBrainrotAlert()
    brainrotAlertEnabled = false
    for _, conn in ipairs(brainrotConnections) do
        conn:Disconnect()
    end
    brainrotConnections = {}
    for player, conn in pairs(brainrotCharConnections) do
        conn:Disconnect()
    end
    brainrotCharConnections = {}
end

TabPrincipal:CreateToggle({
    Name = "Aviso de Brainrot",
    CurrentValue = false,
    Flag = "BrainrotAlertToggle",
    Callback = function(Value)
        if Value then
            enableBrainrotAlert()
        else
            disableBrainrotAlert()
        end
    end,
})

TabPrincipal:CreateLabel("Detecta quando um jogador possui um pet com 'Brainrot' no nome\ne mostra o nome, dono e valor em dinheiro.", Color3.fromRGB(200, 200, 200))

-- ====================== ABA DESYNC ======================
TabDesync:CreateSection("Sincronização de Rede")

local desyncEnabled = false
local desyncPosition = nil
local espDesyncEnabled = false
local espMarker = nil

local function atualizarESP()
    if espMarker then
        espMarker:Destroy()
        espMarker = nil
    end

    if desyncEnabled and espDesyncEnabled and desyncPosition then
        espMarker = Instance.new("Part")
        espMarker.Name = "DesyncGhost"
        espMarker.Anchored = true
        espMarker.CanCollide = false
        espMarker.Size = Vector3.new(1, 1, 1)
        espMarker.Shape = Enum.PartType.Ball
        espMarker.Material = Enum.Material.Neon
        espMarker.Color = Color3.fromRGB(255, 0, 255)
        espMarker.Transparency = 0.3
        espMarker.Position = desyncPosition
        espMarker.Parent = workspace

        local highlight = Instance.new("Highlight")
        highlight.FillColor = Color3.fromRGB(255, 0, 255)
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        highlight.FillTransparency = 0.5
        highlight.OutlineTransparency = 0.2
        highlight.Parent = espMarker
    end
end

local function enableDesync()
    local player = game.Players.LocalPlayer
    local char = player.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local root = char.HumanoidRootPart

    desyncPosition = root.Position

    local success, err = pcall(function()
        if raknet and raknet.desync then
            raknet.desync(true)
        end
    end)

    if not success then
        Rayfield:Notify({
            Title = "Desync",
            Content = "Seu executor não suporta raknet.desync. Nenhum risco à sua segurança.",
            Duration = 5
        })
    else
        desyncEnabled = true
        atualizarESP()
        Rayfield:Notify({
            Title = "Desync",
            Content = "Modo Desync ATIVADO. Você está invisível!",
            Duration = 5
        })
    end
end

local function disableDesync()
    pcall(function()
        if raknet and raknet.desync then
            raknet.desync(false)
        end
    end)
    desyncEnabled = false
    desyncPosition = nil
    atualizarESP()
    Rayfield:Notify({
        Title = "Desync",
        Content = "Modo Desync DESATIVADO.",
        Duration = 5
    })
end

TabDesync:CreateToggle({
    Name = "Ativar Desync",
    CurrentValue = false,
    Flag = "DesyncToggle",
    Callback = function(Value)
        if Value then
            enableDesync()
        else
            disableDesync()
        end
    end,
})

TabDesync:CreateToggle({
    Name = "ESP Desync (Ver Fantasma)",
    CurrentValue = false,
    Flag = "EspDesyncToggle",
    Callback = function(Value)
        espDesyncEnabled = Value
        atualizarESP()
    end,
})

TabDesync:CreateLabel("Usa apenas o comando nativo (seguro). O marcador rosa\nmostra onde seu personagem está congelado para os outros.", Color3.fromRGB(200, 200, 200))

-- ====================== ABA CONFIGURAÇÕES ======================
TabConfig:CreateSection("Sistema")

local autoExecuteEnabled = false
local autoExecuteConnection = nil

local function enableAutoExecute()
    if autoExecuteConnection then
        autoExecuteConnection:Disconnect()
        autoExecuteConnection = nil
    end

    autoExecuteConnection = game:GetService("TeleportService").LocalPlayerArrivedFromTeleport:Connect(function()
        if autoExecuteEnabled then
            loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
            loadstring(game:HttpGet('SCRIPT_URL_AQUI'))()
        end
    end)

    autoExecuteEnabled = true
    Rayfield:Notify({
        Title = "Auto Executar",
        Content = "O script será executado automaticamente ao trocar de servidor!",
        Duration = 5
    })
end

local function disableAutoExecute()
    autoExecuteEnabled = false
    if autoExecuteConnection then
        autoExecuteConnection:Disconnect()
        autoExecuteConnection = nil
    end
    Rayfield:Notify({
        Title = "Auto Executar",
        Content = "Auto execução DESATIVADA.",
        Duration = 3
    })
end

TabConfig:CreateToggle({
    Name = "Auto Executar",
    CurrentValue = false,
    Flag = "AutoExecuteToggle",
    Callback = function(Value)
        if Value then
            enableAutoExecute()
        else
            disableAutoExecute()
        end
    end,
})

TabConfig:CreateLabel("Ao ativar, o script reexecuta ao trocar de servidor.\n(Substitua 'SCRIPT_URL_AQUI' pelo link do script)", Color3.fromRGB(200, 200, 200))

TabConfig:CreateButton({
    Name = "Fechar Painel",
    Callback = function()
        if pegarRapidoEnabled then disablePegarRapido() end
        if infJumpEnabled then disableInfJump() end
        if antiRagdollEnabled then disableAntiRagdoll() end
        if brainrotAlertEnabled then disableBrainrotAlert() end
        if desyncEnabled then disableDesync() end
        if autoExecuteEnabled then disableAutoExecute() end
        Rayfield:Destroy()
    end,
})

-- Carregar configurações salvas
Rayfield:LoadConfiguration()
task.spawn(function()
    task.wait(1)
    if Rayfield.Flags["PegarRapidoToggle"] then
        enablePegarRapido()
    end
    if Rayfield.Flags["InfJumpToggle"] then
        enableInfJump()
    end
    if Rayfield.Flags["AntiRagdollToggle"] then
        enableAntiRagdoll()
    end
    if Rayfield.Flags["BrainrotAlertToggle"] then
        enableBrainrotAlert()
    end
    if Rayfield.Flags["DesyncToggle"] then
        enableDesync()
    end
    if Rayfield.Flags["EspDesyncToggle"] then
        espDesyncEnabled = true
        atualizarESP()
    end
    if Rayfield.Flags["AutoExecuteToggle"] then
        enableAutoExecute()
    end
end)
