-- ============================================
--   AUTO BÔNUS + ANTI AFK + ANTI LAG + HIDE PETS + AUTO LIFT
--   Versão Xeno - FINAL DEFINITIVO
-- ============================================

local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local VirtualUser = game:GetService("VirtualUser")
local VirtualInputManager = game:GetService("VirtualInputManager")

pcall(function()
    local pg = LocalPlayer:FindFirstChild("PlayerGui")
    if pg then
        local gui = pg:FindFirstChild("AutoBonusGUI")
        if gui then gui:Destroy() end
    end
end)

if _G.BotaoRestore then
    pcall(function() _G.BotaoRestore:Destroy() end)
    _G.BotaoRestore = nil
end

if _G.HidePetsConexoes then
    for _, conn in ipairs(_G.HidePetsConexoes) do
        pcall(function() conn:Disconnect() end)
    end
    _G.HidePetsConexoes = {}
end

_G.hidePetsAtivo = false

local Config = {
    Rodando = false,
    AntiAFK = false,
    AutoLift = false,
    AntiLag = false,
    HidePets = false,
    EggSelecionado = "CommonEgg",
    EggSelecionadoDisplay = "Ovo Comum",
    Delay = 0.3,
    Minimizado = false,
}

_G.Config = Config

local NOMES_BONUS = { "x2strength", "x3strength" }

local LISTA_EGGS = {
    { botao = "CommonEgg",  display = "Ovo Comum",       cooldown = 10 },
    { botao = "Normal",     display = "Ovo Normal",      cooldown = 30 },
    { botao = "Gold",       display = "Ovo Dourado",     cooldown = 60 },
    { botao = "Lava",       display = "Ovo de Lava",     cooldown = 120 },
    { botao = "Diamond",    display = "Ovo de Diamante", cooldown = 180 },
    { botao = "Galaxy",     display = "Ovo Galáxia",     cooldown = 240 },
    { botao = "Rainbow",    display = "Ovo Arco-Íris",   cooldown = 300 },
}

local function getCooldown()
    for _, egg in ipairs(LISTA_EGGS) do
        if egg.botao == Config.EggSelecionado then
            return egg.cooldown
        end
    end
    return 10
end

local function ehBonus(nome)
    nome = (nome or ""):lower()
    for _, n in ipairs(NOMES_BONUS) do
        if string.find(nome, n, 1, true) then return true end
    end
    return false
end

-- GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "AutoBonusGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Main = Instance.new("Frame")
Main.Size = UDim2.new(0, 340, 0, 550)
Main.Position = UDim2.new(0.5, -170, 0.5, -275)
Main.BackgroundColor3 = Color3.fromRGB(15, 17, 21)
Main.BorderSizePixel = 0
Main.Active = true
Main.Draggable = true
Main.ZIndex = 1
Main.Parent = ScreenGui
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 14)

local Stroke = Instance.new("UIStroke", Main)
Stroke.Color = Color3.fromRGB(45, 50, 60)
Stroke.Thickness = 1.5

local Header = Instance.new("Frame")
Header.Size = UDim2.new(1, -20, 0, 65)
Header.Position = UDim2.new(0, 10, 0, 10)
Header.BackgroundColor3 = Color3.fromRGB(26, 29, 36)
Header.BorderSizePixel = 0
Header.ZIndex = 2
Header.Parent = Main
Instance.new("UICorner", Header).CornerRadius = UDim.new(0, 10)

local Titulo = Instance.new("TextLabel")
Titulo.Size = UDim2.new(1, -50, 0, 28)
Titulo.Position = UDim2.new(0, 0, 0, 8)
Titulo.BackgroundTransparency = 1
Titulo.Text = "⚡ Auto Bônus"
Titulo.TextColor3 = Color3.fromRGB(79, 195, 247)
Titulo.Font = Enum.Font.GothamBold
Titulo.TextSize = 19
Titulo.ZIndex = 3
Titulo.Parent = Header

local Subtitulo = Instance.new("TextLabel")
Subtitulo.Size = UDim2.new(1, -50, 0, 18)
Subtitulo.Position = UDim2.new(0, 0, 0, 38)
Subtitulo.BackgroundTransparency = 1
Subtitulo.Text = "Lift an Egg"
Subtitulo.TextColor3 = Color3.fromRGB(138, 143, 152)
Subtitulo.Font = Enum.Font.Gotham
Subtitulo.TextSize = 11
Subtitulo.ZIndex = 3
Subtitulo.Parent = Header

local BtnMinimizar = Instance.new("TextButton")
BtnMinimizar.Size = UDim2.new(0, 28, 0, 28)
BtnMinimizar.Position = UDim2.new(1, -34, 0, 8)
BtnMinimizar.BackgroundColor3 = Color3.fromRGB(40, 44, 52)
BtnMinimizar.Text = "─"
BtnMinimizar.TextColor3 = Color3.fromRGB(200, 200, 200)
BtnMinimizar.Font = Enum.Font.GothamBold
BtnMinimizar.TextSize = 18
BtnMinimizar.AutoButtonColor = false
BtnMinimizar.ZIndex = 5
BtnMinimizar.Parent = Header
Instance.new("UICorner", BtnMinimizar).CornerRadius = UDim.new(0, 6)

BtnMinimizar.MouseEnter:Connect(function()
    BtnMinimizar.BackgroundColor3 = Color3.fromRGB(60, 65, 75)
    BtnMinimizar.TextColor3 = Color3.fromRGB(79, 195, 247)
end)

BtnMinimizar.MouseLeave:Connect(function()
    BtnMinimizar.BackgroundColor3 = Color3.fromRGB(40, 44, 52)
    BtnMinimizar.TextColor3 = Color3.fromRGB(200, 200, 200)
end)

local LabelFunc = Instance.new("TextLabel")
LabelFunc.Size = UDim2.new(1, -20, 0, 16)
LabelFunc.Position = UDim2.new(0, 20, 0, 82)
LabelFunc.BackgroundTransparency = 1
LabelFunc.Text = "FUNÇÕES"
LabelFunc.TextColor3 = Color3.fromRGB(108, 114, 124)
LabelFunc.Font = Enum.Font.GothamBold
LabelFunc.TextSize = 10
LabelFunc.TextXAlignment = Enum.TextXAlignment.Left
LabelFunc.ZIndex = 2
LabelFunc.Parent = Main

local function criarCard(yPos, titulo, descricao)
    local Card = Instance.new("Frame")
    Card.Size = UDim2.new(1, -20, 0, 55)
    Card.Position = UDim2.new(0, 10, 0, yPos)
    Card.BackgroundColor3 = Color3.fromRGB(26, 29, 36)
    Card.BorderSizePixel = 0
    Card.ZIndex = 2
    Card.Parent = Main
    Instance.new("UICorner", Card).CornerRadius = UDim.new(0, 10)

    local CheckFrame = Instance.new("Frame")
    CheckFrame.Size = UDim2.new(0, 26, 0, 26)
    CheckFrame.Position = UDim2.new(0, 14, 0.5, -13)
    CheckFrame.BackgroundColor3 = Color3.fromRGB(15, 17, 21)
    CheckFrame.BorderSizePixel = 0
    CheckFrame.ZIndex = 3
    CheckFrame.Parent = Card
    Instance.new("UICorner", CheckFrame).CornerRadius = UDim.new(0, 7)

    local CheckStroke = Instance.new("UIStroke", CheckFrame)
    CheckStroke.Color = Color3.fromRGB(79, 195, 247)
    CheckStroke.Thickness = 2

    local CheckMark = Instance.new("TextLabel")
    CheckMark.Size = UDim2.new(1, 0, 1, 0)
    CheckMark.BackgroundTransparency = 1
    CheckMark.Text = "✓"
    CheckMark.TextColor3 = Color3.fromRGB(79, 195, 247)
    CheckMark.Font = Enum.Font.GothamBold
    CheckMark.TextSize = 18
    CheckMark.TextTransparency = 1
    CheckMark.ZIndex = 4
    CheckMark.Parent = CheckFrame

    local ClickBtn = Instance.new("TextButton")
    ClickBtn.Size = UDim2.new(1, 0, 1, 0)
    ClickBtn.BackgroundTransparency = 1
    ClickBtn.Text = ""
    ClickBtn.ZIndex = 5
    ClickBtn.Parent = CheckFrame

    local TituloLbl = Instance.new("TextLabel")
    TituloLbl.Size = UDim2.new(1, -150, 0, 18)
    TituloLbl.Position = UDim2.new(0, 52, 0, 8)
    TituloLbl.BackgroundTransparency = 1
    TituloLbl.Text = titulo
    TituloLbl.TextColor3 = Color3.fromRGB(230, 230, 230)
    TituloLbl.Font = Enum.Font.GothamBold
    TituloLbl.TextSize = 14
    TituloLbl.TextXAlignment = Enum.TextXAlignment.Left
    TituloLbl.ZIndex = 3
    TituloLbl.Parent = Card

    local DescLbl = Instance.new("TextLabel")
    DescLbl.Size = UDim2.new(1, -150, 0, 16)
    DescLbl.Position = UDim2.new(0, 52, 0, 28)
    DescLbl.BackgroundTransparency = 1
    DescLbl.Text = descricao
    DescLbl.TextColor3 = Color3.fromRGB(138, 143, 152)
    DescLbl.Font = Enum.Font.Gotham
    DescLbl.TextSize = 11
    DescLbl.TextXAlignment = Enum.TextXAlignment.Left
    DescLbl.ZIndex = 3
    DescLbl.Parent = Card

    local Indic = Instance.new("TextLabel")
    Indic.Size = UDim2.new(0, 70, 0, 20)
    Indic.Position = UDim2.new(1, -80, 0.5, -10)
    Indic.BackgroundTransparency = 1
    Indic.Text = "✓ ATIVO"
    Indic.TextColor3 = Color3.fromRGB(79, 195, 247)
    Indic.Font = Enum.Font.GothamBold
    Indic.TextSize = 11
    Indic.TextTransparency = 1
    Indic.ZIndex = 3
    Indic.Parent = Card

    return {
        Card = Card,
        CheckMark = CheckMark,
        Indic = Indic,
        ClickBtn = ClickBtn,
    }
end

local CardBonus = criarCard(102, "Auto Bônus", "Coleta os bônus da tela")
local CardAFK = criarCard(162, "Anti AFK", "Não te derruba por inatividade")
local CardLag = criarCard(222, "Anti Lag", "Otimiza o jogo pra mais FPS")
local CardPets = criarCard(282, "Hide Pets", "Esconde pets das bases")

local CardLift = Instance.new("Frame")
CardLift.Size = UDim2.new(1, -20, 0, 85)
CardLift.Position = UDim2.new(0, 10, 0, 342)
CardLift.BackgroundColor3 = Color3.fromRGB(26, 29, 36)
CardLift.BorderSizePixel = 0
CardLift.ZIndex = 2
CardLift.Parent = Main
Instance.new("UICorner", CardLift).CornerRadius = UDim.new(0, 10)

local TituloLift = Instance.new("TextLabel")
TituloLift.Size = UDim2.new(1, -20, 0, 16)
TituloLift.Position = UDim2.new(0, 10, 0, 6)
TituloLift.BackgroundTransparency = 1
TituloLift.Text = "Auto Lift Egg"
TituloLift.TextColor3 = Color3.fromRGB(230, 230, 230)
TituloLift.Font = Enum.Font.GothamBold
TituloLift.TextSize = 13
TituloLift.TextXAlignment = Enum.TextXAlignment.Left
TituloLift.ZIndex = 3
TituloLift.Parent = CardLift

local DropBtn = Instance.new("TextButton")
DropBtn.Size = UDim2.new(0, 165, 0, 32)
DropBtn.Position = UDim2.new(0, 10, 0, 26)
DropBtn.BackgroundColor3 = Color3.fromRGB(20, 23, 28)
DropBtn.Text = Config.EggSelecionadoDisplay
DropBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
DropBtn.Font = Enum.Font.Gotham
DropBtn.TextSize = 11
DropBtn.AutoButtonColor = false
DropBtn.ZIndex = 10
DropBtn.Parent = CardLift
Instance.new("UICorner", DropBtn).CornerRadius = UDim.new(0, 6)

local DropStroke = Instance.new("UIStroke", DropBtn)
DropStroke.Color = Color3.fromRGB(60, 65, 75)
DropStroke.Thickness = 1

local SetaUp = Instance.new("TextLabel")
SetaUp.Size = UDim2.new(0, 20, 0, 32)
SetaUp.Position = UDim2.new(1, -25, 0, 26)
SetaUp.BackgroundTransparency = 1
SetaUp.Text = "▲"
SetaUp.TextColor3 = Color3.fromRGB(79, 195, 247)
SetaUp.Font = Enum.Font.GothamBold
SetaUp.TextSize = 10
SetaUp.ZIndex = 11
SetaUp.Parent = CardLift

local DropList = Instance.new("Frame")
DropList.Size = UDim2.new(0, 165, 0, 0)
DropList.Position = UDim2.new(0, 10, 0, 60)
DropList.BackgroundColor3 = Color3.fromRGB(20, 23, 28)
DropList.BorderSizePixel = 0
DropList.Visible = false
DropList.ZIndex = 50
DropList.ClipsDescendants = false
DropList.Parent = Main
Instance.new("UICorner", DropList).CornerRadius = UDim.new(0, 6)

local DropListStroke = Instance.new("UIStroke", DropList)
DropListStroke.Color = Color3.fromRGB(60, 65, 75)
DropListStroke.Thickness = 1

local DropListLayout = Instance.new("UIListLayout")
DropListLayout.Padding = UDim.new(0, 2)
DropListLayout.SortOrder = Enum.SortOrder.LayoutOrder
DropListLayout.Parent = DropList

local DropPad = Instance.new("UIPadding")
DropPad.PaddingTop = UDim.new(0, 4)
DropPad.PaddingBottom = UDim.new(0, 4)
DropPad.PaddingLeft = UDim.new(0, 4)
DropPad.PaddingRight = UDim.new(0, 4)
DropPad.Parent = DropList

local listaAberta = false

for i, eggData in ipairs(LISTA_EGGS) do
    local Opcao = Instance.new("TextButton")
    Opcao.Size = UDim2.new(1, 0, 0, 28)
    Opcao.BackgroundColor3 = Color3.fromRGB(26, 29, 36)
    Opcao.Text = "  " .. eggData.display .. "  (" .. eggData.cooldown .. "s)"
    Opcao.TextColor3 = Color3.fromRGB(200, 200, 200)
    Opcao.Font = Enum.Font.Gotham
    Opcao.TextSize = 11
    Opcao.TextXAlignment = Enum.TextXAlignment.Left
    Opcao.AutoButtonColor = false
    Opcao.LayoutOrder = i
    Opcao.ZIndex = 51
    Opcao.Parent = DropList
    Instance.new("UICorner", Opcao).CornerRadius = UDim.new(0, 4)

    Opcao.MouseEnter:Connect(function()
        Opcao.BackgroundColor3 = Color3.fromRGB(35, 40, 50)
    end)
    Opcao.MouseLeave:Connect(function()
        Opcao.BackgroundColor3 = Color3.fromRGB(26, 29, 36)
    end)

    Opcao.MouseButton1Click:Connect(function()
        Config.EggSelecionado = eggData.botao
        Config.EggSelecionadoDisplay = eggData.display
        DropBtn.Text = eggData.display
        DropList.Visible = false
        DropList.Size = UDim2.new(0, 165, 0, 0)
        listaAberta = false
        SetaUp.Text = "▲"
    end)
end

DropBtn.MouseButton1Click:Connect(function()
    listaAberta = not listaAberta
    if listaAberta then
        DropList.Visible = true
        local altura = (#LISTA_EGGS * 30) + 8
        DropList.Size = UDim2.new(0, 165, 0, altura)
        SetaUp.Text = "▼"
    else
        DropList.Visible = false
        DropList.Size = UDim2.new(0, 165, 0, 0)
        SetaUp.Text = "▲"
    end
end)

local BtnLift = Instance.new("TextButton")
BtnLift.Size = UDim2.new(1, -20, 0, 32)
BtnLift.Position = UDim2.new(0, 10, 0, 62)
BtnLift.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
BtnLift.Text = "Auto Lift Egg"
BtnLift.TextColor3 = Color3.fromRGB(200, 200, 200)
BtnLift.Font = Enum.Font.GothamBold
BtnLift.TextSize = 12
BtnLift.AutoButtonColor = false
BtnLift.ZIndex = 10
BtnLift.Parent = CardLift
Instance.new("UICorner", BtnLift).CornerRadius = UDim.new(0, 6)

local BtnLiftStroke = Instance.new("UIStroke", BtnLift)
BtnLiftStroke.Color = Color3.fromRGB(79, 195, 247)
BtnLiftStroke.Thickness = 1

local StatusLbl = Instance.new("TextLabel")
StatusLbl.Size = UDim2.new(1, -20, 0, 22)
StatusLbl.Position = UDim2.new(0, 10, 0, 440)
StatusLbl.BackgroundTransparency = 1
StatusLbl.Text = "● Parado"
StatusLbl.TextColor3 = Color3.fromRGB(255, 82, 82)
StatusLbl.Font = Enum.Font.GothamBold
StatusLbl.TextSize = 13
StatusLbl.ZIndex = 3
StatusLbl.Parent = Main

local Contador = Instance.new("TextLabel")
Contador.Size = UDim2.new(1, -20, 0, 18)
Contador.Position = UDim2.new(0, 10, 0, 465)
Contador.BackgroundTransparency = 1
Contador.Text = "Coletados: 0 | Lifts: 0"
Contador.TextColor3 = Color3.fromRGB(138, 143, 152)
Contador.Font = Enum.Font.Gotham
Contador.TextSize = 11
Contador.ZIndex = 3
Contador.Parent = Main

-- MINIMIZAR
local guiMinimizada = false

BtnMinimizar.MouseButton1Click:Connect(function()
    guiMinimizada = not guiMinimizada
    
    if guiMinimizada then
        Main.Size = UDim2.new(0, 180, 0, 45)
        Main.Draggable = true
        
        Header.Size = UDim2.new(1, -10, 1, 0)
        Header.Position = UDim2.new(0, 5, 0, 0)
        Header.BackgroundColor3 = Color3.fromRGB(15, 17, 21)
        
        Titulo.Size = UDim2.new(1, -50, 1, 0)
        Titulo.Position = UDim2.new(0, 10, 0, 0)
        Titulo.TextSize = 13
        Titulo.TextXAlignment = Enum.TextXAlignment.Left
        Titulo.Text = "⚡ Auto Bônus"
        
        Subtitulo.Visible = false
        LabelFunc.Visible = false
        CardBonus.Card.Visible = false
        CardAFK.Card.Visible = false
        CardLag.Card.Visible = false
        CardPets.Card.Visible = false
        CardLift.Visible = false
        StatusLbl.Visible = false
        Contador.Visible = false
        
        BtnMinimizar.Text = "▲"
        BtnMinimizar.Position = UDim2.new(1, -34, 0.5, -14)
    else
        Main.Size = UDim2.new(0, 340, 0, 550)
        
        Header.Size = UDim2.new(1, -20, 0, 65)
        Header.Position = UDim2.new(0, 10, 0, 10)
        Header.BackgroundColor3 = Color3.fromRGB(26, 29, 36)
        
        Titulo.Size = UDim2.new(1, -50, 0, 28)
        Titulo.Position = UDim2.new(0, 0, 0, 8)
        Titulo.TextSize = 19
        Titulo.TextXAlignment = Enum.TextXAlignment.Center
        
        Subtitulo.Visible = true
        LabelFunc.Visible = true
        CardBonus.Card.Visible = true
        CardAFK.Card.Visible = true
        CardLag.Card.Visible = true
        CardPets.Card.Visible = true
        CardLift.Visible = true
        StatusLbl.Visible = true
        Contador.Visible = true
        
        BtnMinimizar.Text = "─"
        BtnMinimizar.Position = UDim2.new(1, -34, 0, 8)
    end
end)

-- CLICAR
local function clicarBotao(btn)
    if not btn then return end
    
    pcall(function() btn:Activate() end)
    pcall(function()
        if btn.MouseButton1Click then btn.MouseButton1Click:Fire() end
    end)
    pcall(function()
        if btn.MouseButton1Down then btn.MouseButton1Down:Fire() end
    end)
    pcall(function()
        if btn.MouseButton1Up then btn.MouseButton1Up:Fire() end
    end)
    
    pcall(function()
        local pos = btn.AbsolutePosition
        local size = btn.AbsoluteSize
        local cx = pos.X + size.X / 2
        local cy = pos.Y + size.Y / 2
        
        VirtualInputManager:SendMouseButtonEvent(cx, cy, 0, true, game, 1)
        task.wait(0.02)
        VirtualInputManager:SendMouseButtonEvent(cx, cy, 0, false, game, 1)
    end)
    
    pcall(function()
        local pos = btn.AbsolutePosition
        local size = btn.AbsoluteSize
        local cx = pos.X + size.X / 2
        local cy = pos.Y + size.Y / 2
        
        VirtualUser:Button1Down(Vector2.new(cx, cy))
        task.wait(0.02)
        VirtualUser:Button1Up(Vector2.new(cx, cy))
    end)
end

-- AUTO BÔNUS
local clicados = 0
local jaClicados = {}
local contadorLift = 0

local function tentarClicarBonus(obj)
    if not obj or not obj:IsA("GuiButton") then return end
    if not obj.Visible then return end
    if not ehBonus(obj.Name) then return end
    if jaClicados[obj] then return end

    jaClicados[obj] = true
    clicarBotao(obj)
    clicados = clicados + 1
    Contador.Text = "Coletados: " .. clicados .. " | Lifts: " .. contadorLift

    task.delay(0.5, function()
        jaClicados[obj] = nil
    end)
end

task.spawn(function()
    while task.wait(Config.Delay) do
        if not Config.Rodando then continue end

        local pg = LocalPlayer:FindFirstChild("PlayerGui")
        if not pg then continue end
        local mainGui = pg:FindFirstChild("MainGui")
        if not mainGui then continue end

        for _, obj in pairs(mainGui:GetDescendants()) do
            if obj:IsA("GuiButton") and obj.Visible then
                tentarClicarBonus(obj)
            end
        end
    end
end)

-- TOGGLES
CardBonus.ClickBtn.MouseButton1Click:Connect(function()
    Config.Rodando = not Config.Rodando
    if Config.Rodando then
        CardBonus.CheckMark.TextTransparency = 0
        CardBonus.Indic.TextTransparency = 0
        CardBonus.Card.BackgroundColor3 = Color3.fromRGB(21, 32, 48)
    else
        CardBonus.CheckMark.TextTransparency = 1
        CardBonus.Indic.TextTransparency = 1
        CardBonus.Card.BackgroundColor3 = Color3.fromRGB(26, 29, 36)
    end
end)

CardAFK.ClickBtn.MouseButton1Click:Connect(function()
    Config.AntiAFK = not Config.AntiAFK
    if Config.AntiAFK then
        CardAFK.CheckMark.TextTransparency = 0
        CardAFK.Indic.TextTransparency = 0
        CardAFK.Card.BackgroundColor3 = Color3.fromRGB(21, 32, 48)
    else
        CardAFK.CheckMark.TextTransparency = 1
        CardAFK.Indic.TextTransparency = 1
        CardAFK.Card.BackgroundColor3 = Color3.fromRGB(26, 29, 36)
    end
end)

CardLag.ClickBtn.MouseButton1Click:Connect(function()
    Config.AntiLag = not Config.AntiLag
    if Config.AntiLag then
        CardLag.CheckMark.TextTransparency = 0
        CardLag.Indic.TextTransparency = 0
        CardLag.Card.BackgroundColor3 = Color3.fromRGB(21, 32, 48)
        AtivarAntiLag()
    else
        CardLag.CheckMark.TextTransparency = 1
        CardLag.Indic.TextTransparency = 1
        CardLag.Card.BackgroundColor3 = Color3.fromRGB(26, 29, 36)
        DesativarAntiLag()
    end
end)

CardPets.ClickBtn.MouseButton1Click:Connect(function()
    Config.HidePets = not Config.HidePets
    _G.Config = Config
    
    if Config.HidePets then
        CardPets.CheckMark.TextTransparency = 0
        CardPets.Indic.TextTransparency = 0
        CardPets.Card.BackgroundColor3 = Color3.fromRGB(21, 32, 48)
        AtivarHidePets()
    else
        CardPets.CheckMark.TextTransparency = 1
        CardPets.Indic.TextTransparency = 1
        CardPets.Card.BackgroundColor3 = Color3.fromRGB(26, 29, 36)
        DesativarHidePets()
    end
end)

BtnLift.MouseButton1Click:Connect(function()
    Config.AutoLift = not Config.AutoLift
    if Config.AutoLift then
        BtnLift.BackgroundColor3 = Color3.fromRGB(21, 32, 48)
        BtnLift.TextColor3 = Color3.fromRGB(79,
