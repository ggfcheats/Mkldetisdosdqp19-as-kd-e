_Hawk = "ohhahtuhthttoouttpwuttuaunbotwo"

local Hawk = loadstring(game:HttpGet("https://raw.githubusercontent.com/TheHanki/HawkHUB/main/LibSources/HawkLib.lua", true))()

local Janela = Hawk:Janela({
    ScriptName = "visualmente sonolento",
    DestroyIfExists = true,
    Tema = "Escuro"
})

Janela:Fechar({
    visibilidade = true,
    Retorno de chamada = function()
        Janela:Destruir()
    end,
})

Janela:Minimizar({
    visibilidade = true,
    OpenButton = true,
    Retorno de chamada = function()
    end,
})

-- Guia ESP
local tab1 = Janela:Tab("Guia ESP")

local boxEspEnabled = false
local espBoxes = {}

tab1:Toggle("Box Esp", false, function(valor)
    boxEspEnabled = valor
    if not boxEspEnabled then
        for _, caixa in pairs (espBoxes) do
            caixa:Remover()
        end
        espBoxes = {}
    end
end)

local function createBox()
    local caixa = Desenho.new("Quadrado")
    caixa.Espessura = 1
    caixa.Transparência = 1
    caixa.Cor = Color3.fromRGB(255, 255, 255)
    caixa.Preenchida = false
    caixa.Visível = false
    return caixa
end

local function updateBox(jogador)
    if not boxEspEnabled then return end
    local Jogadores = game:GetService("Players")
    local Camera = workspace.CurrentCamera
    local LocalPlayer = Jogadores.LocalPlayer

    if jogador == LocalPlayer then return end
    local personagem = jogador.Character
    if not personagem then return end
    local humanoidRootPart = personagem:FindFirstChild("HumanoidRootPart")
    if not humanoidRootPart then return end
    local caixa = espBoxes[jogador] or createBox()
    espBoxes[jogador] = caixa
    local rootPos, onScreen = Camera:WorldToViewportPoint(humanoidRootPart.Position)
    if onScreen then
        local distancia = (Camera.CFrame.Position - humanoidRootPart.Position).Magnitude
        local boxSize = Vector2.new(2000 / distancia, 3000 / distancia)
        local boxPosition = Vector2.new(rootPos.X - boxSize.X / 2, rootPos.Y - boxSize.Y / 2)
        caixa.Size = boxSize
        caixa.Position = boxPosition
        caixa.Visible = true
    else
        caixa.Visible = false
    end
end

local skeletonEspEnabled = false
local esqueletos = {}

tab1:Toggle("Esqueleto Esp", false, function(valor)
    skeletonEspEnabled = valor
    if not skeletonEspEnabled then
        for _, playerSkeleton in pairs(esqueletos) do
            for _, linha in pairs(playerSkeleton) do
                linha:Remover()
            end
        end
        esqueletos = {}
    end
end)

local bodyConnections = {
    R15 = {
        {"Head", "UpperTorso"},
        {"UpperTorso", "LowerTorso"},
        {"LowerTorso", "LeftUpperLeg"},
        {"LowerTorso", "RightUpperLeg"},
        {"LeftUpperLeg", "LeftLowerLeg"},
        {"LeftLowerLeg", "LeftFoot"},
        {"RightUpperLeg", "RightLowerLeg"},
        {"RightLowerLeg", "RightFoot"},
        {"UpperTorso", "LeftUpperArm"},
        {"UpperTorso", "RightUpperArm"},
        {"LeftUpperArm", "LeftLowerArm"},
        {"LeftLowerArm", "LeftHand"},
        {"RightUpperArm", "RightLowerArm"},
        {"RightLowerArm", "RightHand"}
    },
    R6 = {
        {"Head", "Torso"},
        {"Torso", "Left Arm"},
        {"Torso", "Right Arm"},
        {"Torso", "Left Leg"},
        {"Torso", "Right Leg"}
    }
}

local function createLine()
    local linha = Desenho.new("Linha")
    linha.Cor = Color3.fromRGB(255, 255, 255)
    linha.Espessura = 1
    linha.Transparência = 1
    return linha
end

local function updateSkeleton(jogador)
    if not skeletonEspEnabled then return end
    local Jogadores = game:GetService("Players")
    local Camera = workspace.CurrentCamera
    local LocalPlayer = Jogadores.LocalPlayer

    if jogador == LocalPlayer then return end
    local personagem = jogador.Character
    if not personagem then return end
    local humanoid = personagem:FindFirstChild("Humanoid")
    if not humanoid then return end
    local rigType = humanoid.RigType.Name
    local connections = bodyConnections[rigType]
    if not connections then return end
    if not esqueletos[jogador] then
        esqueletos[jogador] = {}
    end
    for _, connection in pairs(connections) do
        local partA = personagem:FindFirstChild(connection[1])
        local partB = personagem:FindFirstChild(connection[2])
        if partA and partB then
            local linha = esqueletos[jogador][connection[1] .. "-" .. connection[2]] or createLine()
            local posA, onScreenA = Camera:WorldToViewportPoint(partA.Position)
            local posB, onScreenB = Camera:WorldToViewportPoint(partB.Position)
            if onScreenA and onScreenB then
                linha.From = Vector2.new(posA.X, posA.Y)
                linha.To = Vector2.new(posB.X, posB.Y)
                linha.Visível = true
            else
                linha.Visível = false
            end
            esqueletos[jogador][connection[1] .. "-" .. connection[2]] = linha
        end
    end
end

local tracersEnabled = false
local tracers = {}

tab1:Toggle("Rastreadores", false, function(valor)
    tracersEnabled = valor
    if not tracersEnabled then
        for _, tracer in pairs (tracers) do
            tracer:Remover()
        end
        tracers = {}
    end
end)

local function createTracer()
    local tracer = Desenho.new("Linha")
    tracer.Espessura = 1
    tracer.Transparência = 1
    tracer.Color = Color3.fromRGB(255, 255, 255)
    tracer.Visible = false
    return tracer
end

local function updateTracer(player)
    if not tracersEnabled then return end
    local Jogadores = game:GetService("Players")
    local Camera = workspace.CurrentCamera
    local LocalPlayer = Jogadores.LocalPlayer

    if jogador == LocalPlayer then return end
    local personagem = jogador.Character
    if not personagem then return end
    local humanoidRootPart = personagem:FindFirstChild("HumanoidRootPart")
    if not humanoidRootPart then return end
    local tracer = tracers[jogador] or createTracer()
    tracers[jogador] = tracer
    local rootPos, onScreen = Camera:WorldToViewportPoint(humanoidRootPart.Position)
    if onScreen then
        tracer.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
        tracer.To = Vector2.new(rootPos.X, rootPos.Y)
        tracer.Visible = true
    else
        tracer.Visible = false
    end
end

local usernameEspEnabled = false
local usernameTags = {}

tab1:Toggle("Nome de usuário Esp", false, function(valor)
    usernameEspEnabled = valor
    if not usernameEspEnabled then
        for _, tag in pairs(usernameTags) do
            tag:Remover()
        end
        usernameTags = {}
    end
end)

local function createUsernameTag()
    local usernameTag = Desenho.new("Texto")
    usernameTag.Size = 18
    usernameTag.Center = true
    usernameTag.Outline = true
    usernameTag.Color = Color3.fromRGB(255, 255, 255)
    usernameTag.OutlineColor = Color3.fromRGB(0, 0, 0)
    usernameTag.Visible = false
    return usernameTag
end

local function updateUsernameTag(jogador)
    if not usernameEspEnabled then return end
    local Jogadores = game:GetService("Players")
    local Camera = workspace.CurrentCamera
    local LocalPlayer = Jogadores.LocalPlayer

    if jogador == LocalPlayer then return end
    local personagem = jogador.Character
    if not personagem then return end
    local humanoidRootPart = personagem:FindFirstChild("HumanoidRootPart")
    if not humanoidRootPart then return end
    local usernameTag = usernameTags[jogador] or createUsernameTag()
    usernameTags[jogador] = usernameTag
    local rootPos, onScreen = Camera:WorldToViewportPoint(humanoidRootPart.Position)
    if onScreen then
        usernameTag.Text = jogador.Name
        usernameTag.Position = Vector2.new(rootPos.X, rootPos.Y - 25)
        usernameTag.Visible = true
    else
        usernameTag.Visible = false
    end
end

local displayNameEspEnabled = false
local nameTags = {}

tab1:Toggle("Nome de Exibição Esp", false, function(valor)
    displayNameEspEnabled = valor
    if not displayNameEspEnabled then
        for _, tag in pairs(nameTags) do
            tag:Remover()
        end
        nameTags = {}
    end
end)

local function createNameTag()
    local nameTag = Desenho.new("Texto")
    nameTag.Size = 18
    nameTag.Center = true
    nameTag.Outline = true
    nameTag.Color = Color3.fromRGB(255, 255, 255)
    nameTag.OutlineColor = Color3.fromRGB(0, 0, 0)
    nameTag.Visible = false
    return nameTag
end

local function updateNameTag(jogador)
    if not displayNameEspEnabled then return end
    local Jogadores = game:GetService("Players")
    local Camera = workspace.CurrentCamera
    local LocalPlayer = Jogadores.LocalPlayer

    if jogador == LocalPlayer then return end
    local personagem = jogador.Character
    if not personagem then return end
    local humanoidRootPart = personagem:FindFirstChild("HumanoidRootPart")
    if not humanoidRootPart then return end
    local nameTag = nameTags[jogador] or createNameTag()
    nameTags[jogador] = nameTag
    local rootPos, onScreen = Camera:WorldToViewportPoint(humanoidRootPart.Position)
    if onScreen then
        nameTag.Text = jogador.DisplayName
        nameTag.Position = Vector2.new(rootPos.X, rootPos.Y - 40)
        nameTag.Visible = true
    else
        nameTag.Visible = false
    end
end

-- Aba Auto Farm
local tab2 = Janela:Tab("Auto Farm")

local autoFarmEnabled = false
local farmTarget = nil -- Variável para armazenar o alvo de farm

tab2:Toggle("Ativar Auto Farm", false, function(valor)
    autoFarmEnabled = valor
    if autoFarmEnabled then
        print("Auto Farm ativado")
        -- Adicione aqui a lógica para identificar o alvo de farm
        -- farmTarget = ...
    else
        print("Auto Farm desativado")
    end
end)

-- Adicione aqui controles para selecionar o alvo de farm, se necessário
-- Exemplo:
-- tab2:Dropdown("Selecionar Alvo", {"Opção 1", "Opção 2"}, function(opcao)
--     farmTarget = opcao -- Adapte isso para identificar o alvo no jogo
-- end)

-- Função para realizar a ação de farm (você precisará adaptar isso ao seu jogo)
local function doFarmAction()
    if autoFarmEnabled and farmTarget then
        local player = game:GetService("Players").LocalPlayer
        local character = player.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            -- Exemplo genérico: mover para o alvo e tentar interagir
            local success, errorMessage = pcall(function()
                character.Humanoid:MoveTo(farmTarget.Position)
                wait(2) -- Esperar um pouco para chegar
                -- Adicione aqui a lógica específica para "farmar" o alvo
                -- (ex: acionar um evento remoto, manipular um objeto)
                -- game:GetService("ReplicatedStorage").FarmEvent:FireServer(farmTarget)
            end)
            if not success then
                warn("Erro ao farmar:", errorMessage)
            end
        end
    end
end

-- Loop para o Auto Farm
game:GetService("RunService").Heartbeat:Connect(doFarmAction)

-- Aba Teleporte
local tab3 = Janela:Tab("Teleporte")

local teleportPositionX = Janela:TextBox({
    Text = "X",
    PlaceholderText = "Coordenada X"
})

local teleportPositionY = Janela:TextBox({
    Text = "Y",
    PlaceholderText = "Coordenada Y"
})

local teleportPositionZ = Janela:TextBox({
    Text = "Z",
    PlaceholderText = "Coordenada Z"
})

tab3:Button("Teleportar para Coordenadas", function()
    local x = tonumber(teleportPositionX.Text)
    local y = tonumber(teleportPositionY.Text)
    local z = tonumber(teleportPositionZ.Text)

    if x and y and z then
        local player = game:GetService("Players").LocalPlayer
        local character = player.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            local newPosition = Vector3.new(x, y, z)
            local success, errorMessage = pcall(function()
                character.HumanoidRootPart.CFrame = CFrame.new(newPosition)
            end)
            if not success then
                warn("Erro ao teleportar:", errorMessage)
            end
        else
            warn("Personagem não encontrado.")
        end
    else
        warn("Por favor, insira coordenadas numéricas válidas.")
    end
end)

-- Opcional: Dropdown de locais de teleporte
-- local teleportLocations = {
--     ["Local 1"] = Vector3.new(10, 5, 20),
--     ["Local 2"] = Vector3.new(-30, 12, 50)
-- }
--
-- tab3:Dropdown("Locais", {"Local 1", "Local 2"}, function(localSelecionado)
--     local player = game:GetService("Players").LocalPlayer
--     local character = player.Character
--     if character and character:FindFirstChild("HumanoidRootPart") then
--         local newPosition = teleportLocations[localSelecionado]
--         local success, errorMessage = pcall(function()
--             character.HumanoidRootPart.CFrame = CFrame.new(newPosition)
--         end)
--         if not success then
--             warn("Erro ao teleportar:", errorMessage)
--         end
--     else
--         warn("Personagem não encontrado.")
--     end
-- end)

-- Aba Informações (Posição)
local tab4 = Janela:Tab("Informações")

local positionText = Janela:Texto({
    Text = "Posição: X=0, Y=0, Z=0"
})

game:GetService("RunService").RenderStepped:Connect(function()
    if Janela.Visível then -- Atualiza a posição apenas se a janela estiver visível (opcional)
        local player = game:GetService("Players").LocalPlayer
        local character = player.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            local position = character.HumanoidRootPart.Position
            positionText.Text = string.format("Posição: X=%.2f, Y=%.2f, Z=%.2f", position.X, position.Y, position.Z)
        end
    end
end)

-- Atualizações contínuas para ESP
game:GetService("RunService").RenderStepped:Connect(function()
    local Jogadores = game:GetService("Players")
    for _, jogador in pairs(Jogadores:GetPlayers()) do
        updateBox(jogador)
        updateSkeleton(jogador)
        updateTracer(jogador)
        updateUsernameTag(jogador)
        updateNameTag(jogador)
    end
end)
