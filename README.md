--[[
    ULTRA MULTI-GIFT FARM (Red, Blue, Green & Yellow)
    Created By: CyberNoDry
    Features: Auto-Detect R/B/G/Y Gifts, Draggable, High Speed, Infinite Loop
--]]

local Player = game:GetService("Players").LocalPlayer
local UserInputService = game:GetService("UserInputService")

local function Create(cls, props)
    local inst = Instance.new(cls)
    for i, v in pairs(props) do
        inst[i] = v
    end
    return inst
end

-- GUI Principal
local ScreenGui = Create("ScreenGui", {
    Name = "UltimateFarm_CyberNoDry",
    Parent = Player:WaitForChild("PlayerGui"),
    ResetOnSpawn = false
})

local MainFrame = Create("Frame", {
    Name = "MainFrame",
    Parent = ScreenGui,
    BackgroundColor3 = Color3.fromRGB(15, 15, 15),
    Position = UDim2.new(0.5, -110, 0.2, 0),
    Size = UDim2.new(0, 220, 0, 165),
    BorderSizePixel = 0,
    Active = true
})
Create("UICorner", { Parent = MainFrame, CornerRadius = UDim.new(0, 10) })
Create("UIStroke", { Parent = MainFrame, Color = Color3.fromRGB(255, 200, 0), Thickness = 2 }) -- Cor amarela neon no contorno

local TitleBar = Create("Frame", {
    Parent = MainFrame,
    Size = UDim2.new(1, 0, 0, 35),
    BackgroundColor3 = Color3.fromRGB(30, 30, 30),
    BorderSizePixel = 0
})
Create("UICorner", { Parent = TitleBar, CornerRadius = UDim.new(0, 10) })

local Title = Create("TextLabel", {
    Parent = TitleBar,
    Text = "VIP FARM | BY: CyberNoDry",
    Size = UDim2.new(1, -40, 1, 0),
    Position = UDim2.new(0, 10, 0, 0),
    BackgroundTransparency = 1,
    TextColor3 = Color3.fromRGB(255, 255, 255),
    Font = Enum.Font.GothamBold,
    TextSize = 12,
    TextXAlignment = Enum.TextXAlignment.Left
})

local CloseBtn = Create("TextButton", {
    Parent = TitleBar,
    Text = "×",
    Size = UDim2.new(0, 26, 0, 26),
    Position = UDim2.new(1, -30, 0, 4),
    BackgroundColor3 = Color3.fromRGB(200, 50, 50),
    TextColor3 = Color3.new(1, 1, 1),
    Font = Enum.Font.GothamBold,
    TextSize = 18
})
Create("UICorner", { Parent = CloseBtn, CornerRadius = UDim.new(1, 0) })

local StartBtn = Create("TextButton", {
    Parent = MainFrame,
    Position = UDim2.new(0, 10, 0, 45),
    Size = UDim2.new(1, -20, 0, 45),
    BackgroundColor3 = Color3.fromRGB(255, 170, 0),
    Text = "INICIAR AUTO FARM\n(R + B + G + Y)",
    TextColor3 = Color3.fromRGB(0, 0, 0),
    Font = Enum.Font.GothamBold,
    TextSize = 13
})
Create("UICorner", { Parent = StartBtn })

local StopBtn = Create("TextButton", {
    Parent = MainFrame,
    Position = UDim2.new(0, 10, 0, 100),
    Size = UDim2.new(1, -20, 0, 45),
    BackgroundColor3 = Color3.fromRGB(40, 40, 40),
    Text = "PARAR TUDO",
    TextColor3 = Color3.new(1, 1, 1),
    Font = Enum.Font.GothamBold,
    TextSize = 13
})
Create("UICorner", { Parent = StopBtn })

-- LÓGICA DE FARM
local isRunning = false

local function RunFarm()
    while isRunning do
        local TempFolder = workspace:FindFirstChild("Temp")
        if not TempFolder then 
            StartBtn.Text = "ERRO: PASTA TEMP"
            task.wait(1)
            break 
        end

        local items = TempFolder:GetChildren()
        local encontrouGifts = false

        for _, obj in pairs(items) do
            if not isRunning then break end
            
            -- Verifica os 4 nomes: Red, Blue, Green e Yellow
            if (obj.Name == "RedGift" or obj.Name == "BlueGift" or obj.Name == "GreenGift" or obj.Name == "YellowGift") and obj:IsA("Model") then
                -- Acha qualquer BasePart dentro do modelo
                local targetPart = obj:FindFirstChildWhichIsA("BasePart", true)
                
                if targetPart then
                    encontrouGifts = true
                    local char = Player.Character
                    local root = char and char:FindFirstChild("HumanoidRootPart")
                    
                    if root then
                        StartBtn.Text = "COLETANDO: " .. obj.Name
                        -- Teleporte exato
                        root.CFrame = targetPart.CFrame
                        
                        -- Tempo otimizado para coletar e já pular pro próximo
                        task.wait(0.35) 
                    end
                end
            end
        end

        if not encontrouGifts then
            StartBtn.Text = "AGUARDANDO GIFTS..."
            task.wait(1)
        end
        task.wait(0.1)
    end
    StartBtn.Text = "INICIAR AUTO FARM\n(R + B + G + Y)"
end

-- EVENTOS
StartBtn.MouseButton1Click:Connect(function()
    if isRunning then return end
    isRunning = true
    StartBtn.BackgroundColor3 = Color3.fromRGB(180, 120, 0)
    RunFarm()
end)

StopBtn.MouseButton1Click:Connect(function()
    isRunning = false
    StartBtn.Text = "PARADO"
end)

CloseBtn.MouseButton1Click:Connect(function()
    isRunning = false
    ScreenGui:Destroy()
end)

-- SISTEMA DE ARRASTAR (DRAG)
local dragging, dragInput, dragStart, startPos
MainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)
UserInputService.InputEnded:Connect(function(input)
    dragging = false
end)

warn("Ultimate Gift Farm Carregado! (R, B, G, Y) - By: BYCyberNoDry")
