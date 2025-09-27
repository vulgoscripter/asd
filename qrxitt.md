]]

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

-- Variáveis para persistir estado das opções
local savedPosition = UDim2.new(0.5, -117, 0.1, 0)
local savedESP = true
local savedFOV = true
local savedFOVSize = 400

local PANEL_WIDTH = 234
local PANEL_HEIGHT = 257

-- Função para criar a interface
local function CreateUI()
    if LocalPlayer.PlayerGui:FindFirstChild("QrXIT") then
        LocalPlayer.PlayerGui.QrXIT:Destroy()
    end

    local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
    ScreenGui.Name = "QrXIT"
    local MainFrame = Instance.new("Frame", ScreenGui)
    MainFrame.Size = UDim2.new(0, PANEL_WIDTH, 0, PANEL_HEIGHT)
    MainFrame.Position = savedPosition
    MainFrame.BackgroundColor3 = Color3.fromRGB(44, 66, 101)
    MainFrame.BorderSizePixel = 0
    MainFrame.BackgroundTransparency = 0.15
    MainFrame.Active = true
    MainFrame.Draggable = false

    -- Removido UICorner para painel quadrado

    -- Título e flocos espaçados conforme imagem
    local TitleFrame = Instance.new("Frame", MainFrame)
    TitleFrame.Size = UDim2.new(1, 0, 0, 44)
    TitleFrame.Position = UDim2.new(0, 0, 0, 0)
    TitleFrame.BackgroundTransparency = 1

    -- Floco da esquerda
    local SnowLeft = Instance.new("TextLabel", TitleFrame)
    SnowLeft.Size = UDim2.new(0, 32, 1, 0)
    SnowLeft.Position = UDim2.new(0, 0, 0, 0)
    SnowLeft.BackgroundTransparency = 1
    SnowLeft.Text = "❄️"
    SnowLeft.Font = Enum.Font.GothamBlack
    SnowLeft.TextSize = 28
    SnowLeft.TextColor3 = Color3.fromRGB(220, 240, 255)
    SnowLeft.TextStrokeTransparency = 0.3
    SnowLeft.TextStrokeColor3 = Color3.fromRGB(50,80,120)

    -- Qr XIT centralizado
    local Title = Instance.new("TextLabel", TitleFrame)
    Title.Size = UDim2.new(0, 110, 1, 0)
    Title.Position = UDim2.new(0.5, -55, 0, 0)
    Title.BackgroundTransparency = 1
    Title.Text = "Qr XIT"
    Title.Font = Enum.Font.GothamBlack
    Title.TextSize = 28
    Title.TextColor3 = Color3.fromRGB(220, 240, 255)
    Title.TextStrokeTransparency = 0.3
    Title.TextStrokeColor3 = Color3.fromRGB(50,80,120)
    Title.Active = true

    -- Floco da direita
    local SnowRight = Instance.new("TextLabel", TitleFrame)
    SnowRight.Size = UDim2.new(0, 32, 1, 0)
    SnowRight.Position = UDim2.new(1, -32, 0, 0)
    SnowRight.BackgroundTransparency = 1
    SnowRight.Text = "❄️"
    SnowRight.Font = Enum.Font.GothamBlack
    SnowRight.TextSize = 28
    SnowRight.TextColor3 = Color3.fromRGB(220, 240, 255)
    SnowRight.TextStrokeTransparency = 0.3
    SnowRight.TextStrokeColor3 = Color3.fromRGB(50,80,120)

    -- Arrastar painel só pelo título
    local draggingPanel = false
    local dragInput, mouseStart, frameStart
    Title.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            draggingPanel = true
            mouseStart = input.Position
            frameStart = MainFrame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    draggingPanel = false
                    savedPosition = MainFrame.Position
                end
            end)
        end
    end)
    Title.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and draggingPanel then
            local delta = input.Position - mouseStart
            MainFrame.Position = UDim2.new(frameStart.X.Scale, frameStart.X.Offset + delta.X,
                                           frameStart.Y.Scale, frameStart.Y.Offset + delta.Y)
        end
    end)

    local function makeToggle(name, y, default, stateCallback)
        local btn = Instance.new("TextButton", MainFrame)
        btn.Size = UDim2.new(0.92, 0, 0, 38)
        btn.Position = UDim2.new(0.04, 0, 0, y)
        btn.BackgroundColor3 = Color3.fromRGB(70, 180, 240)
        btn.Text = name .. ": " .. (default and "ON" or "OFF")
        btn.Font = Enum.Font.GothamBold
        btn.TextSize = 22
        btn.TextColor3 = Color3.fromRGB(255,255,255)
        btn.AutoButtonColor = false
        btn.BorderSizePixel = 0
        local state = default
        btn.MouseButton1Click:Connect(function()
            state = not state
            btn.Text = name .. ": " .. (state and "ON" or "OFF")
            stateCallback(state)
        end)
        return function() return state end, btn, function(v)
            state = v
            btn.Text = name .. ": " .. (state and "ON" or "OFF")
            stateCallback(state)
        end
    end

    -- Botões espaçados como na imagem
    local getESPEnabled, ESPBtn, setESPBtn = makeToggle("ESP", 56, savedESP, function(v) savedESP = v end)
    local getFOVEnabled, FOVBtn, setFOVBtn = makeToggle("FOV", 104, savedFOV, function(v) savedFOV = v end)

    local FOVSize = savedFOVSize
    local SliderFrame = Instance.new("Frame", MainFrame)
    SliderFrame.Size = UDim2.new(0.93, 0, 0, 40)
    SliderFrame.Position = UDim2.new(0.04, 0, 0, 160)
    SliderFrame.BackgroundTransparency = 1

    local SliderLabel = Instance.new("TextLabel", SliderFrame)
    SliderLabel.Size = UDim2.new(1, 0, 0, 20)
    SliderLabel.Position = UDim2.new(0, 0, 0, 0)
    SliderLabel.BackgroundTransparency = 1
    SliderLabel.Text = "Tamanho FOV: " .. FOVSize
    SliderLabel.Font = Enum.Font.Gotham
    SliderLabel.TextSize = 17
    SliderLabel.TextColor3 = Color3.fromRGB(200,230,255)
    SliderLabel.TextXAlignment = Enum.TextXAlignment.Left

    local SliderBar = Instance.new("Frame", SliderFrame)
    SliderBar.Size = UDim2.new(1, 0, 0, 8)
    SliderBar.Position = UDim2.new(0, 0, 0, 26)
    SliderBar.BackgroundColor3 = Color3.fromRGB(90,140,200)
    SliderBar.BorderSizePixel = 0
    SliderBar.BackgroundTransparency = 0.2

    local SliderKnob = Instance.new("Frame", SliderBar)
    SliderKnob.Size = UDim2.new(0, 20, 0, 20)
    SliderKnob.Position = UDim2.new((FOVSize-50)/350, -10, 0.5, -10)
    SliderKnob.BackgroundColor3 = Color3.fromRGB(255,255,255)
    SliderKnob.BorderSizePixel = 0
    SliderKnob.BackgroundTransparency = 0.1
    SliderKnob.ZIndex = 2

    local draggingSlider = false
    SliderKnob.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            draggingSlider = true
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            draggingSlider = false
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if draggingSlider and input.UserInputType == Enum.UserInputType.MouseMovement then
            local mouseX = UserInputService:GetMouseLocation().X - SliderBar.AbsolutePosition.X
            local pct = math.clamp(mouseX/SliderBar.AbsoluteSize.X, 0, 1)
            FOVSize = math.floor(pct*350 + 50)
            SliderLabel.Text = "Tamanho FOV: " .. FOVSize
            SliderKnob.Position = UDim2.new(pct, -10, 0.5, -10)
            savedFOVSize = FOVSize
        end
    end)

    -- ESP Drawing (white thick outline)
    local function drawESP()
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                for _, part in ipairs(player.Character:GetChildren()) do
                    if part:IsA("BasePart") then
                        if not part:FindFirstChild("QrESP") then
                            local outline = Instance.new("SelectionBox", part)
                            outline.Name = "QrESP"
                            outline.LineThickness = 0.1
                            outline.SurfaceTransparency = 1
                            outline.Adornee = part
                            outline.Color3 = Color3.new(1,1,1)
                            outline.Transparency = 0
                        end
                    end
                end
            end
        end
    end
    local function clearESP()
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character then
                for _, part in ipairs(player.Character:GetChildren()) do
                    if part:IsA("BasePart") then
                        local outline = part:FindFirstChild("QrESP")
                        if outline then outline:Destroy() end
                    end
                end
            end
        end
    end

    -- FOV Circle Drawing (uses Drawing API for smoothness)
    local Drawing = Drawing or shared.Drawing
    local FOVCircle
    local function drawFOVCircle()
        if not FOVCircle then
            FOVCircle = Drawing.new("Circle")
            FOVCircle.Thickness = 2
            FOVCircle.Color = Color3.fromRGB(220,240,255)
            FOVCircle.Filled = false
        end
        local mouse = UserInputService:GetMouseLocation()
        FOVCircle.Position = Vector2.new(mouse.X, mouse.Y)
        FOVCircle.Radius = FOVSize
        FOVCircle.Visible = getFOVEnabled()
    end

    local function clearFOVCircle()
        if FOVCircle then
            FOVCircle.Visible = false
        end
    end

    -- Aimbot Logic: if enemy inside FOV, shots go to head
    local function getClosestEnemyInFOV()
        local mouse = UserInputService:GetMouseLocation()
        local closest = nil
        local closestDist = FOVSize
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
                local headPos = workspace.CurrentCamera:WorldToViewportPoint(player.Character.Head.Position)
                local dist = (Vector2.new(headPos.X, headPos.Y) - Vector2.new(mouse.X, mouse.Y)).Magnitude
                if dist <= FOVSize and dist < closestDist then
                    closestDist = dist
                    closest = player
                end
            end
        end
        return closest
    end

    local function aimAtTarget(target)
        if not target or not target.Character then return end
        local head = target.Character:FindFirstChild("Head")
        if not head then return end
        workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, head.Position)
    end

    UserInputService.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 and getFOVEnabled() then
            local target = getClosestEnemyInFOV()
            if target then
                aimAtTarget(target)
            end
        end
    end)

    RunService.RenderStepped:Connect(function()
        if getESPEnabled() then
            drawESP()
        else
            clearESP()
        end
        if getFOVEnabled() then
            drawFOVCircle()
        else
            clearFOVCircle()
        end
    end)

    ScreenGui.AncestryChanged:Connect(function()
        clearESP()
        clearFOVCircle()
    end)

    setESPBtn(savedESP)
    setFOVBtn(savedFOV)
    SliderLabel.Text = "Tamanho FOV: " .. savedFOVSize
    local pct = (savedFOVSize-50)/350
    SliderKnob.Position = UDim2.new(pct, -10, 0.5, -10)

    return ScreenGui
end

local currentGui = CreateUI()

LocalPlayer.CharacterAdded:Connect(function()
    repeat wait() until LocalPlayer:FindFirstChild("PlayerGui")
    currentGui = CreateUI()
end)

LocalPlayer.PlayerGui.AncestryChanged:Connect(function(_, parent)
    if not parent then
        wait(1)
        if LocalPlayer:FindFirstChild("PlayerGui") then
            currentGui = CreateUI()
        end
    end
end)
