-- Serviços
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")

-- Criar ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "BolinhaGui"
screenGui.Parent = CoreGui
screenGui.ResetOnSpawn = false

-- Criar bolinha arredondada na cor do painel
local bolinha = Instance.new("ImageButton")
bolinha.Name = "Bolinha"
bolinha.Size = UDim2.new(0, 50, 0, 50)
bolinha.Position = UDim2.new(0, 20, 0, 20)
bolinha.BackgroundColor3 = Color3.fromRGB(35, 35, 35) -- cor do painel
bolinha.BorderSizePixel = 0
bolinha.Image = ""

local uicorner = Instance.new("UICorner", bolinha)
uicorner.CornerRadius = UDim.new(1, 0)
bolinha.Parent = screenGui

-- Criar painel principal
local painel = Instance.new("Frame")
painel.Name = "Painel"
painel.Size = UDim2.new(0, 320, 0, 380)
painel.Position = UDim2.new(0.75, -160, 0.5, -190)
painel.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
painel.BorderSizePixel = 0
painel.Visible = false
painel.Parent = screenGui

local cornerPainel = Instance.new("UICorner", painel)
cornerPainel.CornerRadius = UDim.new(0, 15)

-- Título
local titulo = Instance.new("TextLabel", painel)
titulo.Size = UDim2.new(1, 0, 0, 45)
titulo.BackgroundTransparency = 1
titulo.Text = "SECRET SCRIPT"
titulo.Font = Enum.Font.GothamBlack
titulo.TextColor3 = Color3.new(1, 1, 1)
titulo.TextScaled = true
titulo.Position = UDim2.new(0, 0, 0, 0)

-- Status label
local status = Instance.new("TextLabel", painel)
status.Size = UDim2.new(1, 0, 0, 25)
status.Position = UDim2.new(0, 0, 0, 50)
status.BackgroundTransparency = 1
status.Text = "🔴 Todos desativados"
status.Font = Enum.Font.Gotham
status.TextColor3 = Color3.new(1, 1, 1)
status.TextScaled = true

-- Botão fechar X menor no canto superior direito
local btnClose = Instance.new("TextButton", painel)
btnClose.Size = UDim2.new(0, 30, 0, 30)
btnClose.Position = UDim2.new(1, -35, 0, 7)
btnClose.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
btnClose.Text = "X"
btnClose.Font = Enum.Font.GothamBold
btnClose.TextColor3 = Color3.new(1, 1, 1)
btnClose.TextScaled = true
btnClose.BorderSizePixel = 0
btnClose.AutoButtonColor = false

btnClose.MouseEnter:Connect(function()
    btnClose.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
end)
btnClose.MouseLeave:Connect(function()
    btnClose.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
end)

btnClose.MouseButton1Click:Connect(function()
    painel.Visible = false
end)

-- Função para atualizar o texto de status
local function updateStatus()
    local ativos = {}
    if ESPEnabled then table.insert(ativos, "ESP") end
    if TPBrainrotEnabled then table.insert(ativos, "TP Brainrot") end
    if FlyEnabled then table.insert(ativos, "Fly") end
    if SpeedEnabled then table.insert(ativos, "Speed") end
    status.Text = (#ativos > 0 and "🟢 Ativos: " .. table.concat(ativos, " | ") or "🔴 Todos desativados")
end

-- Função para criar botões
local function makeBtn(nome, ypos, callback)
    local btn = Instance.new("TextButton", painel)
    btn.Size = UDim2.new(0, 280, 0, 40)
    btn.Position = UDim2.new(0, 20, 0, ypos)
    btn.BackgroundColor3 = Color3.fromRGB(146, 100, 245)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 20
    btn.Text = nome .. " [OFF]"
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.AutoButtonColor = false
    btn.BorderSizePixel = 0
    btn.TextScaled = true

    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0, 10)

    btn.MouseEnter:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(124, 78, 229)
    end)
    btn.MouseLeave:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(146, 100, 245)
    end)

    btn.MouseButton1Click:Connect(callback)
    return btn
end

-- Variáveis de estado
local ESPEnabled, TPBrainrotEnabled, FlyEnabled, SpeedEnabled = false, false, false, false

-- Armazenar conexões para limpar depois
local ESPDrawings = {}
local RenderConn, TPConn, FlyConn, SpeedConn

-- Funções do script (copiadas e adaptadas)

-- Limpa ESP
local function ClearESP()
    for _, v in ipairs(ESPDrawings) do
        if v.box then
            pcall(function() v.box:Remove() end)
        end
    end
    ESPDrawings = {}
end

-- Atualiza lista ESP
local function UpdateESP()
    ClearESP()
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            local box = Drawing.new("Square")
            box.Color = Color3.fromRGB(255, 255, 255)
            box.Thickness = 2
            box.Filled = false
            box.Transparency = 1
            table.insert(ESPDrawings, {player = plr, box = box})
        end
    end
end

-- Renderiza ESP
local function RenderESP()
    local cam = Workspace.CurrentCamera
    for _, bundle in ipairs(ESPDrawings) do
        local char = bundle.player.Character
        if char and char:FindFirstChild("HumanoidRootPart") then
            local root = char.HumanoidRootPart
            local pos, onscreen = cam:WorldToViewportPoint(root.Position)
            if onscreen then
                local size = Vector2.new(50, 100)
                bundle.box.Size = size
                bundle.box.Position = Vector2.new(pos.X - size.X / 2, pos.Y - size.Y / 2)
                bundle.box.Visible = true
            else
                bundle.box.Visible = false
            end
        end
    end
end

-- Ativa ESP
local function EnableESP()
    UpdateESP()
    if RenderConn then RenderConn:Disconnect() end
    RenderConn = RunService.RenderStepped:Connect(function()
        if ESPEnabled then
            RenderESP()
        else
            ClearESP()
        end
    end)
end

-- Encontra base (Brainrot)
local BrainrotName = "Brainrot"
local BaseName = "Base"

local function FindMyBase()
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("BasePart") and obj.Name == BaseName and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            if (obj.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude < 1000 then
                return obj
            end
        end
    end
    return nil
end

-- TP Brainrot
local TPSpeed = 70
local function EnableTPBrainrot()
    if TPConn then TPConn:Disconnect() end
    TPConn = RunService.Heartbeat:Connect(function()
        if not TPBrainrotEnabled then return end
        local char = LocalPlayer.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then return end
        local hrp = char.HumanoidRootPart

        if not LocalPlayer.Backpack:FindFirstChild(BrainrotName) and not char:FindFirstChild(BrainrotName) then return end

        local base = FindMyBase()
        if base then
            local targetPos = base.Position + Vector3.new(0, 5, 0)
            local dir = (targetPos - hrp.Position)
            if dir.Magnitude > 1 then
                hrp.CFrame = hrp.CFrame + dir.Unit * math.min(dir.Magnitude, TPSpeed * RunService.Heartbeat:Wait())
            else
                TPBrainrotEnabled = false
            end
        end
    end)
end

-- Speed Indetectável
local SpeedValue = 45
local function EnableSpeed()
    if SpeedConn then SpeedConn:Disconnect() end
    SpeedConn = RunService.Heartbeat:Connect(function()
        if not SpeedEnabled then return end
        local char = LocalPlayer.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") or UIS:GetFocusedTextBox() then return end

        local hrp = char.HumanoidRootPart
        local moveDirection = Vector3.new()

        local camCF = Workspace.CurrentCamera.CFrame
        local forward = Vector3.new(camCF.LookVector.X, 0, camCF.LookVector.Z).Unit
        local right = Vector3.new(camCF.RightVector.X, 0, camCF.RightVector.Z).Unit

        if UIS:IsKeyDown(Enum.KeyCode.W) then moveDirection += forward end
        if UIS:IsKeyDown(Enum.KeyCode.S) then moveDirection -= forward end
        if UIS:IsKeyDown(Enum.KeyCode.D) then moveDirection += right end
        if UIS:IsKeyDown(Enum.KeyCode.A) then moveDirection -= right end

        if moveDirection.Magnitude > 0 then
            hrp.AssemblyLinearVelocity = Vector3.new(moveDirection.Unit.X * SpeedValue, hrp.AssemblyLinearVelocity.Y, moveDirection.Unit.Z * SpeedValue)
        else
            hrp.AssemblyLinearVelocity = Vector3.new(0, hrp.AssemblyLinearVelocity.Y, 0)
        end
    end)
end

-- Fly
local FlySpeed = 100
local FlyForce

local function EnableFly()
    local char = LocalPlayer.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    if FlyForce then
        FlyForce:Destroy()
        FlyForce = nil
    end

    FlyForce = Instance.new("BodyVelocity")
    FlyForce.Velocity = Vector3.new(0,0,0)
    FlyForce.MaxForce = Vector3.new(1e5,1e5,1e5)
    FlyForce.Parent = hrp

    if FlyConn then FlyConn:Disconnect() end
    FlyConn = RunService.Heartbeat:Connect(function()
        if not FlyEnabled then
            if FlyForce then
                FlyForce:Destroy()
                FlyForce = nil
            end
            return
        end
        if UIS:GetFocusedTextBox() then return end

        local move = Vector3.new()
        local camCF = Workspace.CurrentCamera.CFrame

        local forward = Vector3.new(camCF.LookVector.X, 0, camCF.LookVector.Z).Unit
        local right = Vector3.new(camCF.RightVector.X, 0, camCF.RightVector.Z).Unit

        if UIS:IsKeyDown(Enum.KeyCode.W) then move += forward end
        if UIS:IsKeyDown(Enum.KeyCode.S) then move -= forward end
        if UIS:IsKeyDown(Enum.KeyCode.D) then move += right end
        if UIS:IsKeyDown(Enum.KeyCode.A) then move -= right end
        if UIS:IsKeyDown(Enum.KeyCode.Space) then move += Vector3.new(0,1,0) end
        if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then move -= Vector3.new(0,1,0) end

        if move.Magnitude > 0 then
            FlyForce.Velocity = move.Unit * FlySpeed
        else
            FlyForce.Velocity = Vector3.new(0,0,0)
        end
    end)
end

-- Criar botões

local btnESP = makeBtn("ESP", 90, function()
    ESPEnabled = not ESPEnabled
    if ESPEnabled then
        btnESP.Text = "ESP [ON]"
        EnableESP()
    else
        btnESP.Text = "ESP [OFF]"
        if RenderConn then RenderConn:Disconnect() RenderConn = nil end
        ClearESP()
    end
    updateStatus()
end)

local btnTPBrainrot = makeBtn("TP Brainrot", 140, function()
    TPBrainrotEnabled = not TPBrainrotEnabled
    if TPBrainrotEnabled then
        btnTPBrainrot.Text = "TP Brainrot [ON]"
        EnableTPBrainrot()
    else
        btnTPBrainrot.Text = "TP Brainrot [OFF]"
        if TPConn then TPConn:Disconnect() TPConn = nil end
    end
    updateStatus()
end)

local btnFly = makeBtn("Fly", 190, function()
    FlyEnabled = not FlyEnabled
    if FlyEnabled then
        btnFly.Text = "Fly [ON]"
        EnableFly()
    else
        btnFly.Text = "Fly [OFF]"
        if FlyConn then FlyConn:Disconnect() FlyConn = nil end
        -- Para evitar voar depois de desligar
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            LocalPlayer.Character.HumanoidRootPart.Velocity = Vector3.new(0, 0, 0)
        end
    end
    updateStatus()
end)

local btnSpeed = makeBtn("Speed", 240, function()
    SpeedEnabled = not SpeedEnabled
    if SpeedEnabled then
        btnSpeed.Text = "Speed [ON]"
        EnableSpeed()
    else
        btnSpeed.Text = "Speed [OFF]"
        if SpeedConn then SpeedConn:Disconnect() SpeedConn = nil end
        -- Para evitar continuar a velocidade depois de desligar
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
        end
    end
    updateStatus()
end)

-- Mostrar/esconder painel ao clicar na bolinha
bolinha.MouseButton1Click:Connect(function()
    painel.Visible = not painel.Visible
end)

-- Atualizar status inicial
updateStatus()
