local Players = game:GetService("Players")
local LP = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

-- Limpeza
if LP.PlayerGui:FindFirstChild("KebarV2_Final") then LP.PlayerGui.KebarV2_Final:Destroy() end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "KebarV2_Final"
ScreenGui.Parent = LP:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

-- 1. BOLINHA VERMELHA LIMPA E ARRASTÁVEL
local Ball = Instance.new("Frame")
Ball.Size = UDim2.new(0, 50, 0, 50)
Ball.Position = UDim2.new(0.1, 0, 0.5, 0)
Ball.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
Ball.Active = true
Ball.Parent = ScreenGui

local BallCorner = Instance.new("UICorner")
BallCorner.CornerRadius = UDim.new(1, 0)
BallCorner.Parent = Ball

local BallBtn = Instance.new("TextButton")
BallBtn.Size = UDim2.new(1, 0, 1, 0)
BallBtn.BackgroundTransparency = 1
BallBtn.Text = ""
BallBtn.Parent = Ball

-- Lógica de Arrastar Otimizada
local dragging, dragInput, dragStart, startPos
Ball.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = Ball.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then dragging = false end
        end)
    end
end)
Ball.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then dragInput = input end
end)
RunService.RenderStepped:Connect(function()
    if dragging and dragInput then
        local delta = dragInput.Position - dragStart
        Ball.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- 2. MENU PRETO
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 500, 0, 350)
MainFrame.Position = UDim2.new(0.5, -250, 0.5, -175)
MainFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
MainFrame.Visible = false
MainFrame.Parent = ScreenGui

Instance.new("UICorner", MainFrame)

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundColor3 = Color3.fromRGB(20, 0, 0)
Title.Text = "KEBAR SCRIPT V2"
Title.TextColor3 = Color3.new(1, 0, 0)
Title.Font = Enum.Font.GothamBold
Title.Parent = MainFrame

BallBtn.MouseButton1Click:Connect(function() MainFrame.Visible = not MainFrame.Visible end)

-- SISTEMA DE ABAS
local SideBar = Instance.new("Frame")
SideBar.Size = UDim2.new(0, 130, 1, -40)
SideBar.Position = UDim2.new(0, 0, 0, 40)
SideBar.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
SideBar.Parent = MainFrame

local Container = Instance.new("Frame")
Container.Size = UDim2.new(1, -140, 1, -50)
Container.Position = UDim2.new(0, 135, 0, 45)
Container.BackgroundTransparency = 1
Container.Parent = MainFrame

local function NewTab(name, order)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, -10, 0, 30)
    b.Position = UDim2.new(0, 5, 0, (order-1)*35 + 5)
    b.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    b.Text = name
    b.TextColor3 = Color3.new(1,1,1)
    b.Parent = SideBar
    
    local p = Instance.new("ScrollingFrame")
    p.Size = UDim2.new(1, 0, 1, 0)
    p.Visible = (order == 1)
    p.BackgroundTransparency = 1
    p.Parent = Container
    
    b.MouseButton1Click:Connect(function()
        for _, v in pairs(Container:GetChildren()) do v.Visible = false end
        p.Visible = true
    end)
    return p
end

-- ABAS
local p1 = NewTab("Admin", 1)
local p2 = NewTab("Gramática", 2)
local p3 = NewTab("Funções", 3)
local p4 = NewTab("Título", 4)

-- ABA 1: IY
local iy = Instance.new("TextButton", p1)
iy.Size = UDim2.new(1, 0, 0, 40)
iy.Text = "Infinite Yield"
iy.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
iy.MouseButton1Click:Connect(function() loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))() end)

-- ABA 2: GRAMÁTICA
local textos = {"Importância do Exército Brasileiro...", "Importância da água...", "Por que quero subir de patente..."}
for i, t in ipairs(textos) do
    local btn = Instance.new("TextButton", p2)
    btn.Size = UDim2.new(1, 0, 0, 30)
    btn.Position = UDim2.new(0, 0, 0, (i-1)*35)
    btn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
    btn.Text = "Copiar Texto " .. i
    btn.MouseButton1Click:Connect(function() setclipboard(t) end)
end

-- ABA 3: FUNÇÕES (FIX INVIS)
local function CreateToggle(txt, pos, cb)
    local t = Instance.new("TextButton", p3)
    t.Size = UDim2.new(1, 0, 0, 30)
    t.Position = UDim2.new(0, 0, 0, pos)
    t.Text = txt .. ": OFF"
    local state = false
    t.MouseButton1Click:Connect(function()
        state = not state
        t.Text = txt .. (state and ": ON" or ": OFF")
        t.BackgroundColor3 = state and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(40,40,40)
        cb(state)
    end)
end

CreateToggle("Invisível", 0, function(v)
    local char = LP.Character
    if char then
        for _, p in pairs(char:GetDescendants()) do
            if p:IsA("BasePart") or p:IsA("Decal") then
                p.Transparency = v and 1 or 0
            end
            if p:IsA("BasePart") and p.Name == "HumanoidRootPart" then p.Transparency = 1 end
        end
    end
end)

CreateToggle("Inf Jump", 35, function(v)
    UserInputService.JumpRequest:Connect(function()
        if v then LP.Character.Humanoid:ChangeState("Jumping") end
    end)
end)

-- ABA 4: TÍTULO (FIX POSIÇÃO)
local Box = Instance.new("TextBox", p4)
Box.Size = UDim2.new(1, 0, 0, 40)
Box.PlaceholderText = "Texto do Título"

local Apply = Instance.new("TextButton", p4)
Apply.Size = UDim2.new(1, 0, 0, 40)
Apply.Position = UDim2.new(0, 0, 0, 50)
Apply.Text = "APLICAR"
Apply.BackgroundColor3 = Color3.fromRGB(200, 0, 0)

Apply.MouseButton1Click:Connect(function()
    local char = LP.Character
    if char and char:FindFirstChild("Head") then
        if char.Head:FindFirstChild("KebarTitle") then char.Head.KebarTitle:Destroy() end
        local bill = Instance.new("BillboardGui", char.Head)
        bill.Name = "KebarTitle"
        bill.Size = UDim2.new(0, 200, 0, 50)
        bill.Adornee = char.Head
        bill.AlwaysOnTop = true
        bill.ExtentsOffset = Vector3.new(0, 3, 0)
        local lbl = Instance.new("TextLabel", bill)
        lbl.Size = UDim2.new(1, 0, 1, 0)
        lbl.Text = Box.Text
        lbl.TextColor3 = Color3.new(1, 1, 1)
        lbl.BackgroundTransparency = 1
        lbl.Font = Enum.Font.GothamBold
        lbl.TextSize = 14
    end
end)

print("KEBAR V2 LOADED")
