-- Time Bomb 👑 Script (Save Position on Toggle & Free Movement After Giving Bomb)
-- Developer: عبدالله العتيبي

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local RunService = game:GetService("RunService")

-- إزالة أي واجهة قديمة لنفس السكربت لتجنب التكرار
if PlayerGui:FindFirstChild("TimeBombGUI") then
    PlayerGui.TimeBombGUI:Destroy()
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "TimeBombGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui

-- النافذة الرئيسية باللون الأسود
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 260, 0, 180)
MainFrame.Position = UDim2.new(0.5, -130, 0.5, -90)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 12)
UICorner.Parent = MainFrame

-- اسم السكربت (فوق على اليسار)
local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(0, 140, 0, 35)
TitleLabel.Position = UDim2.new(0, 15, 0, 10)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "Time Bomb 👑"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.TextSize = 18
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = MainFrame

-- اسم المطور (تحت اسم السكربت)
local DevLabel = Instance.new("TextLabel")
DevLabel.Size = UDim2.new(0, 180, 0, 25)
DevLabel.Position = UDim2.new(0, 15, 0, 42)
DevLabel.BackgroundTransparency = 1
DevLabel.Text = "المطور : عبدالله العتيبي"
DevLabel.TextColor3 = Color3.fromRGB(220, 40, 40)
DevLabel.TextSize = 13
DevLabel.Font = Enum.Font.GothamBold
DevLabel.TextXAlignment = Enum.TextXAlignment.Left
DevLabel.Parent = MainFrame

-- أزرار التحكم (فوق على اليمين: قفل '🔒' ، سلة مهملات '🗑️')
local isLocked = false

-- زر القمامة (حذف السكربت)
local TrashBtn = Instance.new("TextButton")
TrashBtn.Size = UDim2.new(0, 28, 0, 28)
TrashBtn.Position = UDim2.new(1, -35, 0, 12)
TrashBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TrashBtn.Text = "🗑️"
TrashBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
TrashBtn.TextSize = 13
TrashBtn.Parent = MainFrame
Instance.new("UICorner", TrashBtn).CornerRadius = UDim.new(0, 6)

TrashBtn.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)

-- قفل تثبيت الواجهة الرئيسية
local LockBtn = Instance.new("TextButton")
LockBtn.Size = UDim2.new(0, 28, 0, 28)
LockBtn.Position = UDim2.new(1, -68, 0, 12)
LockBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
LockBtn.Text = "🔓"
LockBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
LockBtn.TextSize = 12
LockBtn.Parent = MainFrame
Instance.new("UICorner", LockBtn).CornerRadius = UDim.new(0, 6)

LockBtn.MouseButton1Click:Connect(function()
    isLocked = not isLocked
    MainFrame.Draggable = not isLocked
    LockBtn.Text = isLocked and "🔒" or "🔓"
    LockBtn.BackgroundColor3 = isLocked and Color3.fromRGB(180, 40, 40) or Color3.fromRGB(40, 40, 40)
end)

-- متغير لتخزين المكان المحفوظ عند تشغيل الزر
local savedPosition = nil

-- زر Auto 💣 الأساسي
local autoBombActive = false
local AutoBombBtn = Instance.new("TextButton")
AutoBombBtn.Size = UDim2.new(1, -30, 0, 48)
AutoBombBtn.Position = UDim2.new(0, 15, 0, 85)
AutoBombBtn.BackgroundColor3 = Color3.fromRGB(180, 30, 30)
AutoBombBtn.Text = "Auto Bomb : Off"
AutoBombBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoBombBtn.TextSize = 15
AutoBombBtn.Font = Enum.Font.GothamBold
AutoBombBtn.Parent = MainFrame
Instance.new("UICorner", AutoBombBtn).CornerRadius = UDim.new(0, 8)

AutoBombBtn.MouseButton1Click:Connect(function()
    autoBombActive = not autoBombActive
    if autoBombActive then
        AutoBombBtn.BackgroundColor3 = Color3.fromRGB(40, 180, 40)
        AutoBombBtn.Text = "Auto Bomb : On"
        
        -- حفظ مكان اللاعب الحالي فور تشغيل الزر
        local character = LocalPlayer.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            savedPosition = character.HumanoidRootPart.CFrame
        end
    else
        AutoBombBtn.BackgroundColor3 = Color3.fromRGB(180, 30, 30)
        AutoBombBtn.Text = "Auto Bomb : Off"
        savedPosition = nil
    end
end)

-- نظام الأوتو بومب المحدث: الالتصاق بالخصم، إعطاء القنبلة، الرجوع للمكان المحفوظ، ثم حرية الحركة
RunService.RenderStepped:Connect(function()
    if not autoBombActive then return end
    
    local character = LocalPlayer.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then return end
    
    local backpack = LocalPlayer:FindFirstChild("Backpack")
    local hasBomb = (character:FindFirstChild("TimeBomb") or character:FindFirstChild("Bomb") or 
                     (backpack and (backpack:FindFirstChild("TimeBomb") or backpack:FindFirstChild("Bomb"))))
    
    if not hasBomb then return end
    
    -- إذا لم يتم حفظ مكان لسبب ما، نأخذ المكان الحالي كاحتياط
    if not savedPosition then
        savedPosition = character.HumanoidRootPart.CFrame
    end
    
    local closestEnemy = nil
    local shortestDistance = math.huge
    local myPos = character.HumanoidRootPart.Position
    
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local sameTeam = false
            if LocalPlayer.Team and player.Team and LocalPlayer.Team == player.Team then
                sameTeam = true
            end
            
            if not sameTeam then
                local hrp = player.Character.HumanoidRootPart
                if math.abs(hrp.Position.X) < 1500 and math.abs(hrp.Position.Z) < 1500 then
                    local distance = (hrp.Position - myPos).Magnitude
                    if distance < shortestDistance then
                        shortestDistance = distance
                        closestEnemy = hrp
                    end
                end
            end
        end
    end
    
    if closestEnemy then
        -- 1. الالتصاق بالخصم لإعطائه القنبلة أولاً
        while autoBombActive and closestEnemy and closestEnemy.Parent do
            local currentBackpack = LocalPlayer:FindFirstChild("Backpack")
            local stillHasBomb = (character:FindFirstChild("TimeBomb") or character:FindFirstChild("Bomb") or 
                                  (currentBackpack and (currentBackpack:FindFirstChild("TimeBomb") or currentBackpack:FindFirstChild("Bomb"))))
            
            if not stillHasBomb then break end
            
            character.HumanoidRootPart.CFrame = closestEnemy.CFrame
            RunService.RenderStepped:Wait()
        end
        
        -- 2. بمجرد تسليمها: الرجوع الفوري للمكان الذي تم حفظه عند تفعيل الزر لمرة واحدة
        if savedPosition then
            character.HumanoidRootPart.CFrame = savedPosition
        end
        
        -- 3. إيقاف الأوتو بومب تلقائياً لتعود لك حرية الحركة والتنقل طبيعي حتى تبدأ الجولة القادمة
        autoBombActive = false
        AutoBombBtn.BackgroundColor3 = Color3.fromRGB(180, 30, 30)
        AutoBombBtn.Text = "Auto Bomb : Off"
        savedPosition = nil
    end
end)