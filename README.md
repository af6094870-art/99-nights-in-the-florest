local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

local Window = Fluent:CreateWindow({
    Title = " Coelho Hub",
    SubTitle = "by mr",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = false, -- Desativa o efeito blur do Windows 11
    Theme = "Darker", -- CORRIGIDO: "Sakura" não existe na biblioteca original. Use "Darker", "Dark", "Aqua" ou "Light"
    MinimizeKey = Enum.KeyCode.RightControl
})

-- Força o Fluent original a tirar a transparência do fundo
Fluent:ToggleTransparency(false)

-- Notificação apenas para testar se carregou
Fluent:Notify({
    Title = "Coelho Hub",
    Content = "welcome! 99 nights in the florest",
    Duration = 3
})
local ScreenGui = Instance.new("ScreenGui")
local ToggleBtn = Instance.new("ImageButton")
local UICorner = Instance.new("UICorner")

ScreenGui.Parent = game.CoreGui
ScreenGui.ResetOnSpawn = false
ScreenGui.ResetOnSpawn = true

ToggleBtn.Parent = ScreenGui
ToggleBtn.Size = UDim2.new(0, 60, 0, 60)
ToggleBtn.Position = UDim2.new(0, 20, 0.5, 0)
ToggleBtn.BackgroundTransparency = 1
ToggleBtn.Image = "rbxassetid://94453919385793"

UICorner.CornerRadius = UDim.new(1, 0)
UICorner.Parent = ToggleBtn

-- Abre/fecha igual RightControl
ToggleBtn.MouseButton1Click:Connect(function()
    game:GetService("VirtualInputManager"):SendKeyEvent(true, Enum.KeyCode.RightControl, false, game)
    task.wait(0.1)
    game:GetService("VirtualInputManager"):SendKeyEvent(false, Enum.KeyCode.RightControl, false, game)
end)

-- Arrastar a bolinha
local dragging, dragStart, startPos

ToggleBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = ToggleBtn.Position
    end
end)

game:GetService("UserInputService").InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
        local delta = input.Position - dragStart
        ToggleBtn.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

game:GetService("UserInputService").InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

game:GetService("UserInputService").InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
        local delta = input.Position - dragStart
        ToggleBtn.Position = UDim2.new(
            startPos.X.Scale, 
            startPos.X.Offset + delta.X, 
            startPos.Y.Scale, 
            startPos.Y.Offset + delta.Y
        )
    end
end)

game:GetService("UserInputService").InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)
-- ========================================================
-- 1. PRIMEIRO: CRIA TODAS AS ABAS DO MENU
-- ========================================================
local Tabs = {
    Main = Window:AddTab({ Title = "main", Icon = "" }),
    Config = Window:AddTab({ Title = "config", Icon = "" }),
    Brings = Window:AddTab({ Title = "Bring And Fun", Icon = "" })
}

-- [[ GERENCIADORES INTERNOS ]]
SaveManager:SetLibrary(Fluent)
InterfaceManager:SetLibrary(Fluent)
SaveManager:SetIgnoreIndexes({})

-- Criando o Toggle dentro da aba Brings
Tabs.Brings:AddToggle("BringItemsToggle", {
    Title = "Fly whith chest",
    Default = false,
    Callback = function(Value)
        _G.BringItems = Value
        
        task.spawn(function()
            while _G.BringItems do
                pcall(function()
                    local player = game.Players.LocalPlayer
                    local character = player.Character
                    local hrp = character and character:FindFirstChild("HumanoidRootPart")
                    
                    if hrp then
                        -- Lista contendo exatamente os caminhos (paths) que vocÃª pediu
                        local targets = {
                            workspace.Items:GetChildren()[145],
                            workspace.Items.Coal,
                            workspace.Items:GetChildren()[107],
                            workspace.Items:GetChildren()[92],
                            workspace.Items:GetChildren()[106],
                            workspace.Items:GetChildren()[94],
                            workspace.Items:GetChildren()[164]
                        }
                        
                        -- Varre a lista e puxa o que estiver instanciado nesses paths
                        for _, item in ipairs(targets) do
                            if item and _G.BringItems then
                                if item:IsA("BasePart") then
                                    item.CFrame = hrp.CFrame
                                elseif item:IsA("Model") then
                                    if item.PrimaryPart then
                                        item:SetPrimaryPartCFrame(hrp.CFrame)
                                    else
                                        local part = item:FindFirstChildOfClass("BasePart")
                                        if part then
                                            part.CFrame = hrp.CFrame
                                        end
                                    end
                                end
                            end
                        end
                    end
                end)
                task.wait(0.3) -- Tempo de resposta do loop
            end
        end)
    end
})
