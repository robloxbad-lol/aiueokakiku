-- 重複防止
local oldGui = game.CoreGui:FindFirstChild("植松聖HUB")
if oldGui then oldGui:Destroy() end

local player = game.Players.LocalPlayer
local UIS = game:GetService("UserInputService")

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = ("植松聖HUB")
ScreenGui.Parent = game.CoreGui
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

---------------------------------
-- 設定保存 (セッション内)
---------------------------------
_G.NebulaSettings = _G.NebulaSettings or {
    WalkSpeed = 16,
    InfiniteJump = false,
    SavedPos = nil,
    AutoAim = false,
    FovAim = false
}
local settings = _G.NebulaSettings
local globalSliderDragging = false

---------------------------------
-- サウンド & 通知
---------------------------------
local function PlaySound(id)
    local sound = Instance.new("Sound")
    sound.SoundId = "rbxassetid://" .. id
    sound.Volume = 2
    sound.Parent = game:GetService("SoundService")
    sound.PlayOnRemove = true
    sound:Destroy()
end

local function Notify(text)
    local Frame = Instance.new("Frame", ScreenGui)
    Frame.Size = UDim2.new(0, 220, 0, 50)
    Frame.Position = UDim2.new(1.1, 0, 0.9, 0)
    Frame.BackgroundColor3 = Color3.fromRGB(30, 0, 0)
    Instance.new("UICorner", Frame)
    Instance.new("UIStroke", Frame).Color = Color3.fromRGB(255, 0, 0)
    local T = Instance.new("TextLabel", Frame)
    T.Size = UDim2.new(1, 0, 1, 0); T.Text = text; T.TextColor3 = Color3.fromRGB(255, 255, 255)
    T.Font = Enum.Font.SourceSansBold; T.TextSize = 14; T.BackgroundTransparency = 1
    PlaySound("6518811702")
    Frame:TweenPosition(UDim2.new(1, -230, 0.9, 0), "Out", "Quart", 0.3)
    task.delay(2, function()
        if Frame and Frame.Parent then
            Frame:TweenPosition(UDim2.new(1.1, 0, 0.9, 0), "In", "Quart", 0.3)
            task.wait(0.4); Frame:Destroy()
        end
    end)
end

---------------------------------
-- 機能ロジック
---------------------------------
local jumpConn = UIS.JumpRequest:Connect(function()
    if settings.InfiniteJump then
        local char = player.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
    end
end)

task.spawn(function()
    while task.wait(1) do
        local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
        if hum and hum.WalkSpeed ~= settings.WalkSpeed then hum.WalkSpeed = settings.WalkSpeed end
    end
end)

---------------------------------
-- メインGUI
---------------------------------
local function makeDraggable(frame)
    local dragging, dragStart, startPos
    frame.InputBegan:Connect(function(input)
        if not globalSliderDragging and input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true; dragStart = input.Position; startPos = frame.Position
        end
    end)
    UIS.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    UIS.InputEnded:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end end)
end

local Main = Instance.new("Frame", ScreenGui)
Main.Size = UDim2.new(0, 550, 0, 350)
Main.Position = UDim2.new(0.5, -275, 0.5, -175)
Main.BackgroundColor3 = Color3.fromRGB(10, 0, 0)
makeDraggable(Main)
Instance.new("UICorner", Main)
Instance.new("UIStroke", Main).Color = Color3.fromRGB(255, 50, 0)

local GuiTitle = Instance.new("TextLabel", Main)
GuiTitle.Size = UDim2.new(0, 200, 0, 40)
GuiTitle.Position = UDim2.new(0, 140, 0, 0)
GuiTitle.Text = "植松聖HUB"
GuiTitle.TextColor3 = Color3.fromRGB(255, 50, 0)
GuiTitle.Font = Enum.Font.SourceSansItalic
GuiTitle.TextSize = 22
GuiTitle.TextXAlignment = Enum.TextXAlignment.Left
GuiTitle.BackgroundTransparency = 1

local CloseBtn = Instance.new("TextButton", Main)
CloseBtn.Size = UDim2.new(0, 30, 0, 30); CloseBtn.Position = UDim2.new(1, -35, 0, 5)
CloseBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0); CloseBtn.Text = "×"; CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
Instance.new("UICorner", CloseBtn)

local ConfirmFrame = Instance.new("Frame", ScreenGui)
ConfirmFrame.Size = UDim2.new(0, 250, 0, 120); ConfirmFrame.Position = UDim2.new(0.5, -125, 0.5, -60)
ConfirmFrame.BackgroundColor3 = Color3.fromRGB(20, 0, 0); ConfirmFrame.Visible = false; ConfirmFrame.ZIndex = 2000
Instance.new("UICorner", ConfirmFrame); Instance.new("UIStroke", ConfirmFrame).Color = Color3.fromRGB(255, 0, 0)
local ConfirmTitle = Instance.new("TextLabel", ConfirmFrame)
ConfirmTitle.Size = UDim2.new(1, 0, 0, 50); ConfirmTitle.Text = "完全に終了しますか？"; ConfirmTitle.TextColor3 = Color3.fromRGB(255, 255, 255); ConfirmTitle.BackgroundTransparency = 1
local YesBtn = Instance.new("TextButton", ConfirmFrame)
YesBtn.Size = UDim2.new(0, 100, 0, 40); YesBtn.Position = UDim2.new(0, 20, 0, 60); YesBtn.BackgroundColor3 = Color3.fromRGB(100, 0, 0); YesBtn.Text = "はい"; YesBtn.TextColor3 = Color3.fromRGB(255, 255, 255); Instance.new("UICorner", YesBtn)
local NoBtn = Instance.new("TextButton", ConfirmFrame)
NoBtn.Size = UDim2.new(0, 100, 0, 40); NoBtn.Position = UDim2.new(1, -120, 0, 60); NoBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50); NoBtn.Text = "いいえ"; NoBtn.TextColor3 = Color3.fromRGB(255, 255, 255); Instance.new("UICorner", NoBtn)

YesBtn.MouseButton1Click:Connect(function()
    settings.InfiniteJump = false; jumpConn:Disconnect()
    local h = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if h then h.WalkSpeed = 16 end
    ScreenGui:Destroy()
end)
NoBtn.MouseButton1Click:Connect(function() ConfirmFrame.Visible = false end)
CloseBtn.MouseButton1Click:Connect(function() ConfirmFrame.Visible = true end)

local ToggleBtn = Instance.new("ImageButton", ScreenGui)
ToggleBtn.Size = UDim2.new(0, 60, 0, 60); ToggleBtn.Position = UDim2.new(0, 20, 0.5, -30)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(20, 0, 0); ToggleBtn.Image = "rbxassetid://94535192599903"
makeDraggable(ToggleBtn); Instance.new("UICorner", ToggleBtn); Instance.new("UIStroke", ToggleBtn).Color = Color3.fromRGB(255, 0, 0)

UIS.InputBegan:Connect(function(input, gpe)
    if not gpe and input.KeyCode == Enum.KeyCode.K then
        PlaySound("6895079853"); Main.Visible = not Main.Visible
    end
end)
ToggleBtn.MouseButton1Click:Connect(function() PlaySound("6895079853"); Main.Visible = not Main.Visible end)

---------------------------------
-- タブ・パーツ構成
---------------------------------
-- ★ここを修正：サイドバー本体をScrollingFrameに変更
local SideBar = Instance.new("ScrollingFrame", Main)
SideBar.Size = UDim2.new(0, 130, 1, -10)
SideBar.Position = UDim2.new(0, 0, 0, 5)
SideBar.BackgroundColor3 = Color3.fromRGB(15, 0, 0)
SideBar.BorderSizePixel = 0
SideBar.ScrollBarThickness = 2
SideBar.AutomaticCanvasSize = Enum.AutomaticSize.Y
SideBar.CanvasSize = UDim2.new(0, 0, 0, 0)

local Pages = Instance.new("Frame", Main)
Pages.Size = UDim2.new(1, -140, 1, -50); Pages.Position = UDim2.new(0, 140, 0, 50); Pages.BackgroundTransparency = 1

local Avatar = Instance.new("ImageLabel", SideBar)
Avatar.Size = UDim2.new(0, 50, 0, 50); Avatar.Position = UDim2.new(0.5, -25, 0, 15)
Avatar.Image = game.Players:GetUserThumbnailAsync(player.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size100x100)
Instance.new("UICorner", Avatar).CornerRadius = UDim.new(1, 0)

local NameTag = Instance.new("TextLabel", SideBar)
NameTag.Size = UDim2.new(1, 0, 0, 20); NameTag.Position = UDim2.new(0, 0, 0, 70)
NameTag.Text = player.DisplayName; NameTag.TextColor3 = Color3.fromRGB(255, 255, 255); NameTag.Font = Enum.Font.SourceSansBold; NameTag.TextSize = 14; NameTag.BackgroundTransparency = 1

local TabList = Instance.new("UIListLayout", SideBar)
TabList.Padding = UDim.new(0, 8); TabList.HorizontalAlignment = Enum.HorizontalAlignment.Center

-- ダミー要素を置いてレイアウトがアバターの上に来ないように調整
local Spacer = Instance.new("Frame", SideBar)
Spacer.Size = UDim2.new(0, 0, 0, 95); Spacer.BackgroundTransparency = 1

local function CreateTab(name)
    local Page = Instance.new("ScrollingFrame", Pages)
    Page.Size = UDim2.new(1, 0, 1, 0)
    Page.BackgroundTransparency = 1
    Page.Visible = false
    Page.ScrollBarThickness = 4
    Page.BorderSizePixel = 0
    Page.ClipsDescendants = true

    local Layout = Instance.new("UIListLayout", Page)
    Layout.Padding = UDim.new(0, 12)
    Layout.SortOrder = Enum.SortOrder.LayoutOrder

    Layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        Page.CanvasSize = UDim2.new(0, 0, 0, Layout.AbsoluteContentSize.Y + 50)
    end)

    local TabBtn = Instance.new("TextButton", SideBar)
    TabBtn.Size = UDim2.new(0, 115, 0, 40)
    TabBtn.BackgroundColor3 = Color3.fromRGB(35, 0, 0)
    TabBtn.Text = name:upper()
    TabBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    TabBtn.Font = Enum.Font.SourceSansBold
    TabBtn.TextSize = 18
    Instance.new("UICorner", TabBtn)

    TabBtn.MouseButton1Click:Connect(function()
        PlaySound("6895079853")
        for _, v in pairs(Pages:GetChildren()) do
            if v:IsA("ScrollingFrame") then v.Visible = false end
        end
        Page.Visible = true
        Page.CanvasSize = UDim2.new(0, 0, 0, Layout.AbsoluteContentSize.Y + 50)
    end)

    return Page
end

local function CreateToggle(parent, name, currentVal, callback)
    local ToggleFrame = Instance.new("TextButton", parent)
    ToggleFrame.Size = UDim2.new(1, -10, 0, 45); ToggleFrame.BackgroundColor3 = Color3.fromRGB(25, 0, 0); ToggleFrame.Text = ""
    Instance.new("UICorner", ToggleFrame)
    local Title = Instance.new("TextLabel", ToggleFrame)
    Title.Size = UDim2.new(1, -60, 1, 0); Title.Position = UDim2.new(0, 10, 0, 0); Title.Text = name; Title.TextColor3 = Color3.fromRGB(255, 255, 255); Title.Font = Enum.Font.SourceSansBold; Title.TextSize = 16; Title.BackgroundTransparency = 1; Title.TextXAlignment = Enum.TextXAlignment.Left
    local Box = Instance.new("Frame", ToggleFrame)
    Box.Size = UDim2.new(0, 40, 0, 20); Box.Position = UDim2.new(1, -50, 0.5, -10); Box.BackgroundColor3 = currentVal and Color3.fromRGB(255, 0, 0) or Color3.fromRGB(50, 0, 0)
    Instance.new("UICorner", Box).CornerRadius = UDim.new(1, 0)
    local Dot = Instance.new("Frame", Box)
    Dot.Size = UDim2.new(0, 16, 0, 16); Dot.Position = currentVal and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8); Dot.BackgroundColor3 = Color3.fromRGB(255, 255, 255); Instance.new("UICorner", Dot).CornerRadius = UDim.new(1, 0)
    local enabled = currentVal
    ToggleFrame.MouseButton1Click:Connect(function()
        enabled = not enabled; PlaySound("6895079853")
        Box.BackgroundColor3 = enabled and Color3.fromRGB(255, 0, 0) or Color3.fromRGB(50, 0, 0)
        Dot:TweenPosition(enabled and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8), "Out", "Quart", 0.2)
        callback(enabled); Notify(name .. (enabled and ": ON" or ": OFF"))
    end)
end

local function CreateSlider(parent, name, min, max, currentVal, callback)
    local SliderFrame = Instance.new("Frame", parent)
    SliderFrame.Size = UDim2.new(1, -10, 0, 55); SliderFrame.BackgroundColor3 = Color3.fromRGB(25, 0, 0); Instance.new("UICorner", SliderFrame)
    local Title = Instance.new("TextLabel", SliderFrame)
    Title.Size = UDim2.new(1, -10, 0, 20); Title.Position = UDim2.new(0, 10, 0, 5); Title.Text = name .. " : " .. currentVal; Title.TextColor3 = Color3.fromRGB(255, 255, 255); Title.Font = Enum.Font.SourceSansBold; Title.TextSize = 15; Title.BackgroundTransparency = 1; Title.TextXAlignment = Enum.TextXAlignment.Left
    local SliderBar = Instance.new("Frame", SliderFrame)
    SliderBar.Size = UDim2.new(1, -20, 0, 8); SliderBar.Position = UDim2.new(0, 10, 0, 35); SliderBar.BackgroundColor3 = Color3.fromRGB(50, 0, 0); Instance.new("UICorner", SliderBar)
    local Fill = Instance.new("Frame", SliderBar)
    Fill.Size = UDim2.new((currentVal - min) / (max - min), 0, 1, 0); Fill.BackgroundColor3 = Color3.fromRGB(255, 0, 0); Instance.new("UICorner", Fill)
    local selfDragging = false
    local function update()
        local relativePos = math.clamp((UIS:GetMouseLocation().X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X, 0, 1)
        Fill.Size = UDim2.new(relativePos, 0, 1, 0); local value = math.floor(min + (max - min) * relativePos); Title.Text = name .. " : " .. value; callback(value)
    end
    SliderBar.InputBegan:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 then selfDragging = true; globalSliderDragging = true end end)
    UIS.InputEnded:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 and selfDragging then selfDragging = false; globalSliderDragging = false; Notify(name .. "を保存") end end)
    UIS.InputChanged:Connect(function(input) if selfDragging and input.UserInputType == Enum.UserInputType.MouseMovement then update() end end)
end

local function CreateButton(parent, name, callback)
    local Btn = Instance.new("TextButton", parent)
    Btn.Size = UDim2.new(1, -10, 0, 45); Btn.BackgroundColor3 = Color3.fromRGB(35, 0, 0); Btn.Text = name; Btn.TextColor3 = Color3.fromRGB(255, 255, 255); Btn.Font = Enum.Font.SourceSansBold; Btn.TextSize = 16; Instance.new("UICorner", Btn)
    Btn.MouseButton1Click:Connect(function() PlaySound("6895079853"); callback() end)
end
---------------------------------
-- 設定適用 & タブ配置
---------------------------------
local MainTab = CreateTab("Main")
local PlayerTab = CreateTab("Player")
local BloxFruitsTab = CreateTab("BloxFruits")
MainTab.Visible = true

CreateToggle(MainTab, "無限ジャンプ", settings.InfiniteJump, function(val) settings.InfiniteJump = val end)

CreateButton(MainTab, "現在の場所を保存", function() 
    local root = player.Character and player.Character:FindFirstChild("HumanoidRootPart") 
    if root then settings.SavedPos = root.Position Notify("位置を保存しました") end 
end)
CreateButton(MainTab, "保存地点へテレポート", function() 
    local root = player.Character and player.Character:FindFirstChild("HumanoidRootPart") 
    if settings.SavedPos and root then root.CFrame = CFrame.new(settings.SavedPos) Notify("移動しました") end 
end)

CreateSlider(PlayerTab, "移動速度", 16, 250, settings.WalkSpeed, function(s) 
    settings.WalkSpeed = s 
    local h = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if h then h.WalkSpeed = s end
end)

---------------------------------
-- オートエイム（強力版）
---------------------------------
local aiming = false
local camera = workspace.CurrentCamera

CreateToggle(MainTab, "オートエイム（カメラロック）", settings.AutoAim, function(state)
    settings.AutoAim = state
end)

UIS.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton2 then aiming = true end
end)
UIS.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton2 then aiming = false end
end)

game:GetService("RunService").RenderStepped:Connect(function()
    if settings.AutoAim and aiming then
        local closestPlayer = nil
        local shortestDistance = math.huge
        for _, target in pairs(game.Players:GetPlayers()) do
            if target ~= player and target.Character and target.Character:FindFirstChild("Head") then
                local hum = target.Character:FindFirstChildOfClass("Humanoid")
                if hum and hum.Health > 0 then
                    local head = target.Character.Head
                    local distance = (head.Position - camera.CFrame.Position).Magnitude
                    if distance < shortestDistance then
                        shortestDistance = distance
                        closestPlayer = target
                    end
                end
            end
        end
        if closestPlayer and closestPlayer.Character and closestPlayer.Character:FindFirstChild("Head") then
            camera.CFrame = CFrame.new(camera.CFrame.Position, closestPlayer.Character.Head.Position)
        end
    end
end)

---------------------------------
-- Fly（飛行）
---------------------------------
CreateButton(MainTab, "Fly", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/XNEOFF/FlyGuiV3/main/FlyGuiV3.txt"))()
    Notify("Flyスクリプトを実行しました")
end)

---------------------------------
-- オートエイム（カーソルFOV）
---------------------------------
local fovAiming = false
local FOV_RADIUS = 200
local mousePos = Vector2.new()

local fovCircle = Drawing.new("Circle")
fovCircle.Radius = FOV_RADIUS
fovCircle.Color = Color3.fromRGB(255, 255, 255)
fovCircle.Thickness = 1
fovCircle.Transparency = 0.5
fovCircle.Visible = false
fovCircle.Filled = false

CreateToggle(MainTab, "オートエイム（FOVカメラ）", settings.FovAim, function(state)
    settings.FovAim = state
    fovCircle.Visible = state
end)

UIS.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton2 then
        fovAiming = true
    elseif input.UserInputType == Enum.UserInputType.MouseMovement then
        mousePos = Vector2.new(input.Position.X, input.Position.Y)
    end
end)
UIS.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        mousePos = Vector2.new(input.Position.X, input.Position.Y)
    end
end)
UIS.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton2 then fovAiming = false end
end)

game:GetService("RunService").RenderStepped:Connect(function()
    fovCircle.Position = mousePos
    if settings.FovAim and fovAiming then
        local closestPlayer = nil
        local shortestDistance = math.huge
        for _, target in pairs(game.Players:GetPlayers()) do
            if target ~= player and target.Character and target.Character:FindFirstChild("Head") then
                local head = target.Character.Head
                local hum = target.Character:FindFirstChildOfClass("Humanoid")
                if hum and hum.Health > 0 then
                    local screenPos, onScreen = camera:WorldToViewportPoint(head.Position)
                    if onScreen then
                        local distFromCursor = (Vector2.new(screenPos.X, screenPos.Y) - mousePos).Magnitude
                        if distFromCursor <= FOV_RADIUS then
                            local worldDist = (head.Position - camera.CFrame.Position).Magnitude
                            if worldDist < shortestDistance then
                                shortestDistance = worldDist
                                closestPlayer = target
                            end
                        end
                    end
                end
            end
        end
        if closestPlayer and closestPlayer.Character:FindFirstChild("Head") then
            camera.CFrame = CFrame.new(camera.CFrame.Position, closestPlayer.Character.Head.Position)
        end
    end
end)
do ---------------------------------
-- Mainタブ：Silent Aim (高精度スキャン版)
---------------------------------
local Players = game:GetService("Players")
local Mouse = player:GetMouse()

-- 設定に追加
settings.SilentAim = false
local aimDist = 1000
local targetPartName = "HumanoidRootPart"
local scanRate = 0.01

local targetPlayerName = nil
local targetPosition = nil

-- 【高精度スキャン】最も近い敵をロック
task.spawn(function()
    while true do
        if settings.SilentAim then
            local closest = nil
            local shortestDist = aimDist
            local myChar = player.Character
            local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
            
            if myRoot then
                for _, p in pairs(Players:GetPlayers()) do
                    if p ~= player and p.Character then
                        local tPart = p.Character:FindFirstChild(targetPartName)
                        local hum = p.Character:FindFirstChild("Humanoid")
                        
                        -- 生存確認と距離チェック
                        if tPart and hum and hum.Health > 0 then
                            local mag = (tPart.Position - myRoot.Position).Magnitude
                            if mag < shortestDist then
                                shortestDist = mag
                                closest = p
                            end
                        end
                    end
                end
            end
            
            -- ターゲット情報の更新
            if closest then
                targetPlayerName = closest.Name
                targetPosition = closest.Character[targetPartName].Position
            else
                targetPlayerName = nil
                targetPosition = nil
            end
        end
        task.wait(scanRate)
    end
end)

-- 【メタテーブル・フック】通信データのすり替え
local mt = getrawmetatable(game)
local oldIndex = mt.__index
local oldNamecall = mt.__namecall
setreadonly(mt, false)

-- マウス位置の偽装 (indexフック)
mt.__index = newcclosure(function(self, index)
    if settings.SilentAim and not checkcaller() and targetPlayerName and targetPosition then
        if self == Mouse and (index == "Hit" or index == "Target") then
            if index == "Hit" then
                return CFrame.new(targetPosition)
            else
                return Players[targetPlayerName].Character[targetPartName]
            end
        end
    end
    return oldIndex(self, index)
end)

-- サーバー通信の偽装 (namecallフック)
mt.__namecall = newcclosure(function(self, ...)
    local method = getnamecallmethod()
    local args = {...}
    
    if settings.SilentAim and not checkcaller() and (method == "FireServer" or method == "InvokeServer") and targetPosition then
        local name = self.Name:lower()
        -- バグ防止のためUI系は除外
        if not name:find("gui") and not name:find("weather") and not name:find("hud") then
            for i, v in ipairs(args) do
                if typeof(v) == "Vector3" then
                    args[i] = targetPosition
                elseif typeof(v) == "CFrame" then
                    local myChar = player.Character
                    if myChar and myChar:FindFirstChild("HumanoidRootPart") then
                        args[i] = CFrame.new(myChar.HumanoidRootPart.Position, targetPosition)
                    end
                end
            end
        end
    end
    return oldNamecall(self, unpack(args))
end)

setreadonly(mt, true)

-- Mainタブにトグルを追加
if MainTab then
    CreateToggle(MainTab, "Silent Aim (サイレントエイム)", settings.SilentAim, function(v)
        settings.SilentAim = v
        if v then
            Notify("Silent Aim 有効 (全攻撃必中)")
        else
            Notify("Silent Aim 無効")
        end
    end)
end
do ---------------------------------
-- Mainタブ：Silent Aim (チームチェック・高精度版)
---------------------------------
local Players = game:GetService("Players")
local Mouse = player:GetMouse()

-- 設定に追加
settings.SilentAim = false
local aimDist = 1000
local targetPartName = "HumanoidRootPart"
local scanRate = 0.01

local targetPlayerName = nil
local targetPosition = nil

-- 【高精度スキャン】味方を除外して敵だけをロック
task.spawn(function()
    while true do
        if settings.SilentAim then
            local closest = nil
            local shortestDist = aimDist
            local myChar = player.Character
            local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
            
            if myRoot then
                for _, p in pairs(Players:GetPlayers()) do
                    -- 自分以外、かつチームが異なるプレイヤーのみをターゲットにする
                    if p ~= player and p.Character and p.Team ~= player.Team then
                        local tPart = p.Character:FindFirstChild(targetPartName)
                        local hum = p.Character:FindFirstChild("Humanoid")
                        
                        -- 生存確認
                        if tPart and hum and hum.Health > 0 then
                            local mag = (tPart.Position - myRoot.Position).Magnitude
                            if mag < shortestDist then
                                shortestDist = mag
                                closest = p
                            end
                        end
                    end
                end
            end
            
            -- ターゲット情報の更新
            if closest then
                targetPlayerName = closest.Name
                targetPosition = closest.Character[targetPartName].Position
            else
                targetPlayerName = nil
                targetPosition = nil
            end
        end
        task.wait(scanRate)
    end
end)

-- 【メタテーブル・フック】通信データのすり替え（サイレント）
local mt = getrawmetatable(game)
local oldIndex = mt.__index
local oldNamecall = mt.__namecall
setreadonly(mt, false)

-- マウス位置の偽装
mt.__index = newcclosure(function(self, index)
    if settings.SilentAim and not checkcaller() and targetPlayerName and targetPosition then
        if self == Mouse and (index == "Hit" or index == "Target") then
            if index == "Hit" then
                return CFrame.new(targetPosition)
            else
                return Players[targetPlayerName].Character[targetPartName]
            end
        end
    end
    return oldIndex(self, index)
end)

-- サーバー通信の偽装（スキル・銃・必殺技）
mt.__namecall = newcclosure(function(self, ...)
    local method = getnamecallmethod()
    local args = {...}
    
    if settings.SilentAim and not checkcaller() and (method == "FireServer" or method == "InvokeServer") and targetPosition then
        local name = self.Name:lower()
        -- GUIや演出系の通信を除外してバグを防ぐ
        if not name:find("gui") and not name:find("weather") and not name:find("hud") then
            for i, v in ipairs(args) do
                if typeof(v) == "Vector3" then
                    args[i] = targetPosition
                elseif typeof(v) == "CFrame" then
                    -- 自分の位置から敵を向いたCFrameを作成
                    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                        args[i] = CFrame.new(player.Character.HumanoidRootPart.Position, targetPosition)
                    end
                end
            end
        end
    end
    return oldNamecall(self, unpack(args))
end)

setreadonly(mt, true)

-- Mainタブにトグルを追加（既存のものがあれば上書き）
if MainTab then
    CreateToggle(MainTab, "Silent Aim (チームチェック)", settings.SilentAim, function(v)
        settings.SilentAim = v
        if v then
            Notify("Silent Aim 有効 (敵のみロックオン)")
        else
            Notify("Silent Aim 無効")
        end
    end)
end
do ---------------------------------
-- BloxFruitsタブ：NPC専用 Silent Aim (超軽量ファーム版)
---------------------------------
local Workspace = game:GetService("Workspace")
local Mouse = player:GetMouse()

-- 設定に追加
settings.NPCSilentAim = false
local npcDist = 1000
local npcTargetPart = "HumanoidRootPart"
local npcScanRate = 0.01

-- ターゲットフォルダ定義
local EnemyFolder = Workspace:FindFirstChild("Enemies")

local TargetNPC = nil
local TargetNPCPosition = nil

-- 【超軽量スキャン】Enemiesフォルダの中身だけを高速チェック
task.spawn(function()
    while true do
        if settings.NPCSilentAim then
            local closest = nil
            local shortestDist = npcDist
            local myChar = player.Character
            local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
            
            if myRoot and EnemyFolder then
                for _, obj in pairs(EnemyFolder:GetChildren()) do
                    local hum = obj:FindFirstChildOfClass("Humanoid")
                    local tPart = obj:FindFirstChild(npcTargetPart)
                    
                    if hum and tPart and hum.Health > 0 then
                        local mag = (tPart.Position - myRoot.Position).Magnitude
                        if mag < shortestDist then
                            shortestDist = mag
                            closest = obj
                        end
                    end
                end
            end
            
            -- ターゲット確定
            if closest then
                TargetNPC = closest
                TargetNPCPosition = closest[npcTargetPart].Position
            else
                TargetNPC = nil
                TargetNPCPosition = nil
            end
        end
        task.wait(npcScanRate)
    end
end)

-- 【メタテーブル・フック】NPC用エイムの適用
-- (※MainタブのSilent Aimと干渉しないように論理和で判定)
local mt = getrawmetatable(game)
local oldIndex = mt.__index
local oldNamecall = mt.__namecall
setreadonly(mt, false)

mt.__index = newcclosure(function(self, index)
    if not checkcaller() then
        -- NPCエイムがONかつターゲットがいる場合
        if settings.NPCSilentAim and TargetNPCPosition then
            if self == Mouse and (index == "Hit" or index == "Target") then
                if index == "Hit" then return CFrame.new(TargetNPCPosition) end
                if index == "Target" then return TargetNPC[npcTargetPart] end
            end
        end
    end
    return oldIndex(self, index)
end)

mt.__namecall = newcclosure(function(self, ...)
    local method = getnamecallmethod()
    local args = {...}
    
    if not checkcaller() and (method == "FireServer" or method == "InvokeServer") then
        -- NPCエイム適用時
        if settings.NPCSilentAim and TargetNPCPosition then
            local name = self.Name:lower()
            if not name:find("gui") and not name:find("weather") and not name:find("hud") then
                for i, v in ipairs(args) do
                    if typeof(v) == "Vector3" then
                        args[i] = TargetNPCPosition
                    elseif typeof(v) == "CFrame" then
                        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                            args[i] = CFrame.new(player.Character.HumanoidRootPart.Position, TargetNPCPosition)
                        end
                    end
                end
            end
        end
    end
    return oldNamecall(self, unpack(args))
end)

setreadonly(mt, true)

-- BloxFruitsタブにトグルを追加
if BloxFruitsTab then
    CreateToggle(BloxFruitsTab, "NPC Silent Aim (Farm用)", settings.NPCSilentAim, function(v)
        settings.NPCSilentAim = v
        if v then
            Notify("NPC Silent Aim 有効 (ファーム特化)")
        else
            Notify("NPC Silent Aim 無効")
        end
    end)
end
---------------------------------
-- 🚀 Mainタブ：ダメージ無効化
---------------------------------
task.spawn(function()
    -- 1. 設定用テーブルの安全確保
    if not _G.Settings then _G.Settings = {} end
    _G.Settings.DisableHit = true -- 初期値ON

    -- 2. MainTabを確実に探す（変数がなくてもWindowから検索）
    local TargetTab = MainTab
    if not TargetTab and Window then
        for _, v in pairs(Window) do
            if type(v) == "table" and (v.Name == "Main" or v.Title == "Main") then
                TargetTab = v
                break
            end
        end
    end

    -- 3. ダメージ判定（CanTouch）を切り替える関数
    local function ToggleAllTouch(state)
        local count = 0
        local playerChar = game.Players.LocalPlayer.Character
        
        for _, obj in ipairs(workspace:GetDescendants()) do
            if obj:IsA("BasePart") then
                pcall(function()
                    -- 自分のキャラ以外を無効化
                    if not playerChar or not obj:IsDescendantOf(playerChar) then
                        obj.CanTouch = not state
                    end
                end)
            end
            
            -- フリーズ防止のウェイト
            count = count + 1
            if count % 1000 == 0 then task.wait() end
        end
    end

    -- 4. 起動時に即実行
    task.spawn(function()
        ToggleAllTouch(_G.Settings.DisableHit)
    end)

    -- 5. 新しいオブジェクト（スキル等）も自動で無効化
    workspace.DescendantAdded:Connect(function(obj)
        if _G.Settings.DisableHit and obj:IsA("BasePart") then
            pcall(function()
                local playerChar = game.Players.LocalPlayer.Character
                if not playerChar or not obj:IsDescendantOf(playerChar) then
                    obj.CanTouch = false
                end
            end)
        end
    end)

    -- 6. Mainタブにトグルを作成
    if TargetTab then
        pcall(function()
            CreateToggle(TargetTab, "オブジェクトダメージ無効化", _G.Settings.DisableHit, function(state)
                _G.Settings.DisableHit = state
                task.spawn(function()
                    ToggleAllTouch(state)
                end)
                Notify("ダメージ無効化: " .. (state and "ON" or "OFF"))
            end)
        end)
    else
        warn("MainTabが見つかりませんでした。")
    end
end)
---------------------------------
-- 🍈 BloxFruitsタブ：自動更新プレイヤーリスト観戦
---------------------------------
local spectateEnabled = false
local spectateTarget = nil
local cam = workspace.CurrentCamera

-- 1. リストを表示するコンテナ
local SpecFrame = Instance.new("Frame", BloxFruitsTab)
SpecFrame.Size = UDim2.new(1, -10, 0, 180) 
SpecFrame.BackgroundColor3 = Color3.fromRGB(20, 0, 0)
Instance.new("UICorner", SpecFrame)

local SpecTitle = Instance.new("TextLabel", SpecFrame)
SpecTitle.Size = UDim2.new(1, 0, 0, 30); SpecTitle.Text = " 👥 観戦プレイヤーを選択 (自動更新)"; SpecTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
SpecTitle.Font = Enum.Font.SourceSansBold; SpecTitle.TextSize = 14; SpecTitle.BackgroundTransparency = 1; SpecTitle.TextXAlignment = Enum.TextXAlignment.Left

-- 2. スクロールリスト
local ScrollList = Instance.new("ScrollingFrame", SpecFrame)
ScrollList.Size = UDim2.new(1, -10, 1, -40); ScrollList.Position = UDim2.new(0, 5, 0, 35)
ScrollList.BackgroundTransparency = 1; ScrollList.CanvasSize = UDim2.new(0, 0, 0, 0); ScrollList.ScrollBarThickness = 4
local ListLayout = Instance.new("UIListLayout", ScrollList); ListLayout.Padding = UDim.new(0, 5)

-- 3. リスト更新関数
local function UpdatePlayerList()
    for _, child in pairs(ScrollList:GetChildren()) do
        if child:IsA("TextButton") then child:Destroy() end
    end

    for _, p in pairs(game.Players:GetPlayers()) do
        if p ~= player then
            local pBtn = Instance.new("TextButton", ScrollList)
            pBtn.Size = UDim2.new(1, -10, 0, 35); pBtn.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
            pBtn.Text = p.DisplayName .. " (@" .. p.Name .. ")"; pBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
            pBtn.Font = Enum.Font.SourceSans; pBtn.TextSize = 14; Instance.new("UICorner", pBtn)

            pBtn.MouseButton1Click:Connect(function()
                spectateTarget = p.Name
                Notify("対象: " .. p.DisplayName)
                SpecTitle.Text = " 👁️ 観戦中: " .. p.DisplayName
            end)
        end
    end
    ScrollList.CanvasSize = UDim2.new(0, 0, 0, ListLayout.AbsoluteContentSize.Y)
end

-- 4. ★自動更新のイベント接続
game.Players.PlayerAdded:Connect(UpdatePlayerList)   -- 誰かが入ったら更新
game.Players.PlayerRemoving:Connect(UpdatePlayerList) -- 誰かが抜けたら更新
UpdatePlayerList() -- 初回起動

-- 5. ON/OFFトグル
CreateToggle(BloxFruitsTab, "スペクテイト有効化", false, function(state)
    spectateEnabled = state
    if not state then
        cam.CameraSubject = player.Character:FindFirstChildOfClass("Humanoid")
    end
end)

-- 6. 追従ロジック
game:GetService("RunService").RenderStepped:Connect(function()
    if spectateEnabled and spectateTarget then
        local targetPlayer = game.Players:FindFirstChild(spectateTarget)
        if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChildOfClass("Humanoid") then
            cam.CameraSubject = targetPlayer.Character:FindFirstChildOfClass("Humanoid")
        else
            -- ターゲットが抜けたら視点を自分に戻す
            cam.CameraSubject = player.Character:FindFirstChildOfClass("Humanoid")
            if spectateTarget then
                spectateTarget = nil
                SpecTitle.Text = " 👥 観戦対象を選択"
                Notify("対象がいなくなったため解除しました")
            end
        end
    end
end)
---------------------------------
-- 🍈 BloxFruitsタブ：観戦連動型 "疑似Tween" (高速微細TP版)
---------------------------------
-- 1. 変数管理
local tweenFollowing = false
local tweenSpeed = 200 -- 初期スピード (1秒間に進むスタッド数)
local RunService = game:GetService("RunService")

-- 2. 追従ループ関数
local function StartTweenFollow()
    if tweenFollowing then return end
    if not spectateTarget then 
        Notify("まず観戦リストからターゲットを選んでください")
        return 
    end
    
    tweenFollowing = true
    Notify(spectateTarget .. " への追従を開始 (Custom TP)")
    
    -- Noclip（壁抜け）用：体の衝突判定を消す
    local function activateNoclip()
        if player.Character then
            for _, v in pairs(player.Character:GetDescendants()) do
                if v:IsA("BasePart") and v.CanCollide == true then
                    v.CanCollide = false
                end
            end
        end
    end

    -- 非同期スレッドでループ開始
    task.spawn(function()
        -- RenderStepped: 画面描画の前のフレーム毎に実行（最も滑らかに見える）
        local connection
        connection = RunService.RenderStepped:Connect(function(deltaTime)
            if not tweenFollowing then 
                connection:Disconnect() -- ループ停止
                return 
            end

            local targetPlayer = game.Players:FindFirstChild(spectateTarget)
            local myChar = player.Character
            local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
            
            if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") and myRoot then
                local tarRoot = targetPlayer.Character.HumanoidRootPart
                
                -- Noclip維持
                activateNoclip()

                -- 目標地点：相手の背後3スタッド、少し上
                local targetCFrame = tarRoot.CFrame * CFrame.new(0, 5, 3)
                local targetPos = targetCFrame.Position
                local currentPos = myRoot.Position
                
                -- 現在地から目標までのベクトルと距離
                local directionVec = (targetPos - currentPos)
                local distance = directionVec.Magnitude
                
                -- 「今回進む距離」を計算 (スピード × 経過時間)
                local stepDist = tweenSpeed * deltaTime
                
                if distance > 0.5 then
                    if distance < stepDist then
                        -- 目標まであとちょっとなら、直接そこに吸い付く
                        myRoot.CFrame = targetCFrame
                    else
                        -- 目標に向かって「stepDist」分だけ座標をずらす（微細テレポート）
                        -- CFrame.lookAtを使って、相手の方を向きながら移動
                        local newPos = currentPos + (directionVec.Unit * stepDist)
                        myRoot.CFrame = CFrame.lookAt(newPos, targetPos)
                    end
                    
                    -- 物理演算による落下や慣性を完全に殺す（空中でピタッと止まるため）
                    myRoot.Velocity = Vector3.zero
                    myRoot.AssemblyLinearVelocity = Vector3.zero
                end
            else
                -- ターゲットがいなくなったら終了
                tweenFollowing = false
                connection:Disconnect()
                Notify("ターゲットロスト / 停止")
            end
        end)
    end)
end

-- 3. GUIコンテナ
local TweenBtnFrame = Instance.new("Frame", BloxFruitsTab)
TweenBtnFrame.Size = UDim2.new(1, -10, 0, 100) -- スライダー用に縦幅確保
TweenBtnFrame.BackgroundTransparency = 1

local StartBtn = Instance.new("TextButton", TweenBtnFrame)
StartBtn.Size = UDim2.new(0.48, 0, 0, 40)
StartBtn.BackgroundColor3 = Color3.fromRGB(0, 80, 0); StartBtn.Text = "疑似Tween開始"; StartBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
Instance.new("UICorner", StartBtn)

local StopBtn = Instance.new("TextButton", TweenBtnFrame)
StopBtn.Size = UDim2.new(0.48, 0, 0, 40); StopBtn.Position = UDim2.new(0.52, 0, 0, 0)
StopBtn.BackgroundColor3 = Color3.fromRGB(80, 0, 0); StopBtn.Text = "停止"; StopBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
Instance.new("UICorner", StopBtn)

-- 4. スピード調整スライダー
local SliderContainer = Instance.new("Frame", TweenBtnFrame)
SliderContainer.Size = UDim2.new(1, 0, 0, 50)
SliderContainer.Position = UDim2.new(0, 0, 0, 45)
SliderContainer.BackgroundTransparency = 1

-- ユーザー要望: 100 ～ 350 の範囲で設定
CreateSlider(SliderContainer, "追従スピード", 100, 350, tweenSpeed, function(val)
    tweenSpeed = val
end)

-- 5. ボタンイベント
StartBtn.MouseButton1Click:Connect(function()
    StartTweenFollow()
end)

StopBtn.MouseButton1Click:Connect(function()
    tweenFollowing = false
    Notify("追従を停止しました")
end)
---------------------------------
-- 🍈 BloxFruitsタブ：Speed Multiplier (常時適用版)
---------------------------------
-- 1. 設定に追加
settings.SpeedMultiplier = 1

-- 2. 数値を強制適用するループ (Heartbeatで高速上書き)
game:GetService("RunService").Heartbeat:Connect(function()
    local char = player.Character
    if char then
        -- 属性 "SpeedMultiplier" を設定値で上書きし続ける
        char:SetAttribute("SpeedMultiplier", settings.SpeedMultiplier)
    end
end)

-- 3. BloxFruitsタブにスライダーを追加
-- ※既存のCreateSlider関数 (親, 名前, 最小, 最大, 初期値, 関数) を使用
-- 増分(Increment)は自動的に内部で処理されます
CreateSlider(BloxFruitsTab, "スピード", 1, 1000, settings.SpeedMultiplier, function(val)
    settings.SpeedMultiplier = val
    -- スライダーを動かした瞬間にも即座に適用
    local char = player.Character
    if char then
        char:SetAttribute("SpeedMultiplier", val)
    end
end)
---------------------------------
-- 🍈 BloxFruitsタブ：Dash Length (常時適用版)
---------------------------------
-- 1. 設定に追加
settings.DashLength = 10

-- 2. 数値を強制適用するループ (毎フレーム実行)
game:GetService("RunService").Heartbeat:Connect(function()
    local char = player.Character
    if char then
        -- ゲーム側の設定を上書きし続ける
        char:SetAttribute("DashLength", settings.DashLength)
    end
end)

-- 3. BloxFruitsタブにスライダーを追加
-- ※既存のCreateSlider関数 (親, 名前, 最小, 最大, 初期値, 関数) を使用
CreateSlider(BloxFruitsTab, "ダッシュ", 10, 1000, settings.DashLength, function(val)
    settings.DashLength = val
    -- スライダーを動かした瞬間にも一応適用
    local char = player.Character
    if char then
        char:SetAttribute("DashLength", val)
    end
end)
---------------------------------
-- 🍈 BloxFruitsタブ：Jump Power (常時適用版)
---------------------------------
-- 1. 設定に追加 (デフォルト値 50)
_G.JumpHeightValue = 50
_G.AutoJumpEnabled = true

-- 2. 数値を強制適用するループ (毎フレーム実行)
-- これで死んだ後やゲーム側のリセットを上書きします
game:GetService("RunService").Heartbeat:Connect(function()
    if _G.AutoJumpEnabled then
        pcall(function()
            local char = game.Players.LocalPlayer.Character
            local hum = char and char:FindFirstChildOfClass("Humanoid")
            if hum then
                hum.UseJumpPower = true
                hum.JumpPower = _G.JumpHeightValue
            end
        end)
    end
end)

-- 3. BloxFruitsタブにスライダーを追加
-- あなたが提示した「CreateSlider(親, 名前, 最小, 最大, 初期値, 関数)」の形式です
CreateSlider(BloxFruitsTab, "ジャンプ力", 50, 500, _G.JumpHeightValue, function(val)
    _G.JumpHeightValue = val
    _G.AutoJumpEnabled = true
    
    -- 動かした瞬間に適用
    pcall(function()
        local hum = game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if hum then
            hum.UseJumpPower = true
            hum.JumpPower = val
        end
    end)
end)
---------------------------------
-- 💢 強化版 BANG & TP システム (キー設定・範囲緩和)
---------------------------------
-- 1. 設定項目
settings.BangKey = "G"
settings.TpKey = "U"
settings.BangEnabled = false
settings.TpEnabled = false

local bangActive = false
local lastClickTime = tick()
local bangTarget = nil
local cam = workspace.CurrentCamera
local mouse = player:GetMouse()

-- 2. キー入力変換用関数
local function GetKeyCode(keyString)
    local success, code = pcall(function() return Enum.KeyCode[keyString:upper()] end)
    return success and code or nil
end

-- 3. UIの作成 (Mainタブ)
-- キー設定用の入力欄
local function CreateKeyInput(parent, name, default, callback)
    local Frame = Instance.new("Frame", parent)
    Frame.Size = UDim2.new(1, -10, 0, 45); Frame.BackgroundColor3 = Color3.fromRGB(25, 0, 0); Instance.new("UICorner", Frame)
    local Label = Instance.new("TextLabel", Frame)
    Label.Size = UDim2.new(0, 150, 1, 0); Label.Position = UDim2.new(0, 10, 0, 0); Label.Text = name; Label.TextColor3 = Color3.fromRGB(255, 255, 255); Label.Font = Enum.Font.SourceSansBold; Label.TextSize = 16; Label.BackgroundTransparency = 1; Label.TextXAlignment = Enum.TextXAlignment.Left
    local Box = Instance.new("TextBox", Frame)
    Box.Size = UDim2.new(0, 50, 0, 30); Box.Position = UDim2.new(1, -60, 0.5, -15); Box.BackgroundColor3 = Color3.fromRGB(40, 0, 0); Box.Text = default; Box.TextColor3 = Color3.fromRGB(255, 255, 255); Box.Font = Enum.Font.SourceSansBold; Box.TextSize = 16; Instance.new("UICorner", Box)
    Box.FocusLost:Connect(function() callback(Box.Text) Notify(name .. "を [" .. Box.Text:upper() .. "] に設定") end)
end

CreateKeyInput(MainTab, "BANG起動キー設定", settings.BangKey, function(val) settings.BangKey = val:upper() end)
CreateKeyInput(MainTab, "クリックTPキー設定", settings.TpKey, function(val) settings.TpKey = val:upper() end)

CreateToggle(MainTab, "BANG機能 有効化", false, function(state) 
    settings.BangEnabled = state 
    if not state then bangActive = false end
end)

-- 4. ターゲット選択 (判定を大幅に緩和)
UIS.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.UserInputType == Enum.UserInputType.MouseButton1 and UIS:IsKeyDown(Enum.KeyCode.LeftControl) then
        local mousePos = UIS:GetMouseLocation()
        local closest, bestDist = nil, 250 -- ★判定距離を250（かなり広い）に設定

        for _, p in pairs(game.Players:GetPlayers()) do
            if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local pos, onScreen = cam:WorldToViewportPoint(p.Character.HumanoidRootPart.Position)
                if onScreen then
                    local d = (Vector2.new(pos.X, pos.Y) - mousePos).Magnitude
                    if d < bestDist then
                        bestDist = d
                        closest = p
                    end
                end
            end
        end
        
        if closest then
            bangTarget = closest
            Notify("ターゲット確定: " .. closest.DisplayName)
        else
            Notify("近くにプレイヤーがいません")
        end
    end

    -- 設定されたキーで動作
    local currentBangKey = GetKeyCode(settings.BangKey)
    local currentTpKey = GetKeyCode(settings.TpKey)

    if currentBangKey and input.KeyCode == currentBangKey then
        settings.BangEnabled = not settings.BangEnabled
        Notify("BANG: " .. (settings.BangEnabled and "ON" or "OFF"))
    elseif currentTpKey and input.KeyCode == currentTpKey then
        settings.TpEnabled = not settings.TpEnabled
        Notify("クリックTP: " .. (settings.TpEnabled and "ON" or "OFF"))
    end
end)

-- 5. 実行ロジック (Heartbeat/RenderStepped)
game:GetService("RunService").Heartbeat:Connect(function()
    if settings.BangEnabled and bangTarget and bangTarget.Character then
        local myHRP = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
        local tarHRP = bangTarget.Character:FindFirstChild("HumanoidRootPart")
        
        if myHRP and tarHRP and (tick() - lastClickTime >= 0.2) then
            myHRP.CFrame = tarHRP.CFrame * CFrame.new(0, 0, 3)
        end
    end
end)

mouse.Button1Down:Connect(function()
    if settings.BangEnabled and settings.TpEnabled and bangTarget then
        local tarHRP = bangTarget.Character and bangTarget.Character:FindFirstChild("HumanoidRootPart")
        if tarHRP then
            local angle = math.random() * 2 * math.pi
            local offset = Vector3.new(math.cos(angle) * 300, 5, math.sin(angle) * 300)
            player.Character.HumanoidRootPart.CFrame = CFrame.new(tarHRP.Position + offset)
            lastClickTime = tick()
        end
    end
end)
---------------------------------
-- 🍈 BloxFruitsタブ：Target Slots (ボートハメ込みTP)
---------------------------------
-- 1. 変数管理
local targetSlotsSelected = {}
local boatsFolder = workspace:FindFirstChild("Boats")

-- 2. 自分のボートと空き席を検索する関数
local function getAvailableSeats()
    local char = player.Character
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if not hum or not hum.SeatPart or not boatsFolder then return nil, {} end

    local myBoat = nil
    for _, b in ipairs(boatsFolder:GetChildren()) do
        if hum.SeatPart:IsDescendantOf(b) then
            myBoat = b
            break
        end
    end

    local seats = {}
    if myBoat then
        for _, obj in ipairs(myBoat:GetDescendants()) do
            if (obj:IsA("Seat") or obj:IsA("VehicleSeat")) and obj ~= hum.SeatPart and obj.Occupant == nil then
                table.insert(seats, obj)
            end
        end
    end
    return myBoat, seats
end

-- 3. GUIコンテナ作成
local SlotFrame = Instance.new("Frame", BloxFruitsTab)
SlotFrame.Size = UDim2.new(1, -10, 0, 200)
SlotFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
Instance.new("UICorner", SlotFrame)

local SlotTitle = Instance.new("TextLabel", SlotFrame)
SlotTitle.Size = UDim2.new(1, 0, 0, 30); SlotTitle.Text = " 🚢 Target Slots (ボート空き枠)"; SlotTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
SlotTitle.Font = Enum.Font.SourceSansBold; SlotTitle.TextSize = 14; SlotTitle.BackgroundTransparency = 1; SlotTitle.TextXAlignment = Enum.TextXAlignment.Left

local SlotScroll = Instance.new("ScrollingFrame", SlotFrame)
SlotScroll.Size = UDim2.new(1, -10, 1, -75); SlotScroll.Position = UDim2.new(0, 5, 0, 35)
SlotScroll.BackgroundTransparency = 1; SlotScroll.ScrollBarThickness = 4
local SlotLayout = Instance.new("UIListLayout", SlotScroll); SlotLayout.Padding = UDim.new(0, 5)

-- 4. リスト更新関数
local function UpdateSlotList()
    local _, seats = getAvailableSeats()
    local maxSlots = #seats
    SlotTitle.Text = " 🚢 Target Slots: " .. #targetSlotsSelected .. " / " .. maxSlots

    for _, v in ipairs(SlotScroll:GetChildren()) do if v:IsA("TextButton") then v:Destroy() end end

    for _, p in ipairs(game.Players:GetPlayers()) do
        if p ~= player then
            local isSelected = table.find(targetSlotsSelected, p)
            local b = Instance.new("TextButton", SlotScroll)
            b.Size = UDim2.new(1, -10, 0, 30)
            b.Text = p.DisplayName; b.TextColor3 = Color3.fromRGB(255, 255, 255)
            b.BackgroundColor3 = isSelected and Color3.fromRGB(150, 0, 0) or Color3.fromRGB(40, 40, 40)
            Instance.new("UICorner", b)

            b.MouseButton1Click:Connect(function()
                local idx = table.find(targetSlotsSelected, p)
                if idx then
                    table.remove(targetSlotsSelected, idx)
                elseif #targetSlotsSelected < maxSlots then
                    table.insert(targetSlotsSelected, p)
                else
                    Notify("空き席が足りません！")
                end
                UpdateSlotList()
            end)
        end
    end
    SlotScroll.CanvasSize = UDim2.new(0, 0, 0, SlotLayout.AbsoluteContentSize.Y)
end

-- 5. 実行ボタン
local ExecSlotBtn = Instance.new("TextButton", SlotFrame)
ExecSlotBtn.Size = UDim2.new(0.9, 0, 0, 35); ExecSlotBtn.Position = UDim2.new(0.05, 0, 1, -40)
ExecSlotBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 0); ExecSlotBtn.Text = "ボートハメ込みTP開始"; ExecSlotBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
Instance.new("UICorner", ExecSlotBtn)

ExecSlotBtn.MouseButton1Click:Connect(function()
    local myBoat, seats = getAvailableSeats()
    if not myBoat or #targetSlotsSelected == 0 then 
        Notify("ボートに乗っていないか、対象を選んでいません")
        return 
    end

    Notify("テレポートを開始します...")
    for i, target in ipairs(targetSlotsSelected) do
        local seat = seats[i]
        local enemyRoot = target.Character and target.Character:FindFirstChild("HumanoidRootPart")
        if seat and enemyRoot then
            local offset = myBoat:GetPivot():Inverse() * seat.CFrame
            local targetCFrame = enemyRoot.CFrame * offset:Inverse()
            myBoat:PivotTo(targetCFrame)
            task.wait(0.3)
        end
    end
    targetSlotsSelected = {}
    UpdateSlotList()
    Notify("全ターゲットの処理が完了しました")
end)

-- 自動更新
game.Players.PlayerAdded:Connect(UpdateSlotList)
game.Players.PlayerRemoving:Connect(UpdateSlotList)
UpdateSlotList()
---------------------------------
-- 🚀 Mainタブ：個別軽量化パーツ（アニメ無効 / FPS Boost）
---------------------------------

-- 1. アニメーション無効化の処理
local function ApplyDisableAnims(char)
    if not char then return end
    local animate = char:WaitForChild("Animate", 5)
    if animate then animate:Destroy() end

    local hum = char:WaitForChild("Humanoid", 5)
    if hum then
        -- 現在再生中のアニメをすべて停止
        for _, track in pairs(hum:GetPlayingAnimationTracks()) do
            track:Stop(0)
        end
        -- 以降に再生されるアニメを強制停止
        hum.AnimationPlayed:Connect(function(track)
            track:Stop(0)
        end)
    end
end
-- 3. Mainタブにボタンを個別に配置
CreateButton(MainTab, "アニメーション無効化", function()
    if player.Character then ApplyDisableAnims(player.Character) end
    -- 死んでも自動適用されるように接続
    player.CharacterAdded:Connect(ApplyDisableAnims)
    Notify("アニメーションを完全に削除しました")
end)
---------------------------------
-- 🚀 Mainタブ：FPS Boost (極限透明：限界設定)
---------------------------------
local function ApplyUltimateGhostFPSBoost()
    local lighting = game:GetService("Lighting")
    local workspace = game:GetService("Workspace")

    local function Clean(v)
        -- 音バグ徹底排除
        if v:IsA("Sound") or v:IsA("SoundGroup") then return end

        pcall(function()
            -- 1. エフェクト系：透明度 0.99 (ほぼ空気)
            if v:IsA("ParticleEmitter") or v:IsA("Smoke") or v:IsA("Fire") or v:IsA("Sparkles") then
                -- 1.0(完全消滅)の一歩手前。ごく僅かに形が残る
                v.Transparency = NumberSequence.new(0.99) 
                if v:IsA("ParticleEmitter") then
                    -- 粒の数は1%（元の1/100）まで削減。描画コストをほぼゼロへ
                    v.Rate = v.Rate * 0.01
                end
            elseif v:IsA("Trail") or v:IsA("Beam") then
                -- 軌跡や光線も0.99透明
                v.Transparency = 0.99
            
            -- 2. テクスチャ・デカール：透明度 0.98
            elseif v:IsA("Decal") or v:IsA("Texture") then
                if v.Name ~= "face" then -- 顔だけは消さない（透明人間防止）
                    v.Transparency = 0.98
                end

            -- 3. 画面全体エフェクト：計算そのものを停止
            elseif v:IsA("PostEffect") or v:IsA("ColorCorrectionEffect") or 
                   v:IsA("BloomEffect") or v:IsA("BlurEffect") or 
                   v:IsA("SunRaysEffect") then
                v.Enabled = false 

            -- 4. パーツ：全マテリアルを強制プラスチック化（最も軽い計算）
            elseif v:IsA("BasePart") or v:IsA("MeshPart") then
                v.CastShadow = false
                v.Reflectance = 0
                v.Material = Enum.Material.SmoothPlastic
            end
        end)
    end

    -- 既存の全データをスキャン
    for _, v in pairs(game:GetDescendants()) do
        Clean(v)
    end

    -- 【重要】新しく生まれるエフェクトも即座に「極限透明」にする
    workspace.DescendantAdded:Connect(Clean)

    -- ライティング負荷の完全排除
    lighting.GlobalShadows = false
    lighting.FogEnd = 9e9 -- 霧の計算を飛ばす
    
    pcall(function()
        settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
    end)

    Notify("FPS Boost有効")
end

-- ボタン登録
CreateButton(MainTab, "FPS Boost", function()
    ApplyUltimateGhostFPSBoost()
end)
---------------------------------
-- 👑 Mainタブ：True Cinematic (色彩完全自由)
---------------------------------
local function ApplyZenithRTX_V2()
    local lighting = game:GetService("Lighting")
    local workspace = game:GetService("Workspace")
    local terrain = workspace.Terrain
    local runService = game:GetService("RunService")

    -- 1. 【最強のライティング設定】影と反射のキレを維持
    lighting.GlobalShadows = true
    lighting.ShadowSoftness = 0 -- 影をクッキリ
    lighting.Brightness = 1.8
    lighting.EnvironmentDiffuseScale = 1
    lighting.EnvironmentSpecularScale = 1
    lighting.ExposureCompensation = 0.1

    -- 2. 【空の黄金バランス & 雲の移動】
    local clouds = terrain:FindFirstChildOfClass("Clouds") or Instance.new("Clouds", terrain)
    clouds.Enabled = true
    clouds.Cover = 0.62
    clouds.Density = 0.75
    
    -- 雲を流す（色には影響しません）
    pcall(function() runService:UnbindFromRenderStep("ZenithCloudDrift") end)
    local cloudTime = 0
    runService:BindToRenderStep("ZenithCloudDrift", Enum.RenderPriority.Last.Value, function(delta)
        cloudTime = cloudTime + delta
        clouds.Cover = 0.62 + (math.sin(cloudTime * 0.15) * 0.015)
    end)

    -- 3. 【テクスチャ・反射の移植】
    -- パーツ自体が演出の色を反射するようにし、一体感を出します
    for _, v in pairs(workspace:GetDescendants()) do
        pcall(function()
            if v:IsA("BasePart") or v:IsA("MeshPart") then
                v.CastShadow = true
                if v.Reflectance < 0.15 then
                    v.Reflectance = 0.22 
                end
                if v.Material == Enum.Material.SmoothPlastic or v.Material == Enum.Material.Glass then
                    v.Reflectance = 0.35
                end
            end
        end)
    end

    -- 4. 【色固定バグを消すエフェクト処理】
    -- 新しく作らず、今あるエフェクトの「質」だけをブースト。
    -- 無い場合だけ透明なエフェクトを追加します。
    local function Boost(v)
        if v:IsA("BloomEffect") then
            v.Intensity = 0.35
            v.Size = 24
            v.Threshold = 0.9
        elseif v:IsA("SunRaysEffect") then
            v.Intensity = 0.06
        elseif v:IsA("Atmosphere") then
            -- [重要] ColorとDecayを上書きせず、密度(Density)とハッキリ感だけ調整
            v.Density = 0.28
            v.Glare = 0.4
            v.Haze = 0.8
        end
    end

    for _, v in pairs(lighting:GetChildren()) do Boost(v) end
    lighting.ChildAdded:Connect(Boost)

    -- もしゲームにAtmosphereがない場合のみ、色のない大気を追加
    if not lighting:FindFirstChildOfClass("Atmosphere") then
        local a = Instance.new("Atmosphere", lighting)
        a.Density = 0.28
        a.Glare = 0.4
        a.Haze = 0.8
        -- Colorを指定しないことで、ゲームのデフォルト(空の色)に従わせます
    end

    -- 5. 【映画級モーションブラー】
    local blur = lighting:FindFirstChild("ZenithBlurV2") or Instance.new("BlurEffect", lighting)
    blur.Name = "ZenithBlurV2"
    local lastCF = workspace.CurrentCamera.CFrame
    
    pcall(function() runService:UnbindFromRenderStep("ZenithDynamicBlur") end)
    runService:BindToRenderStep("ZenithDynamicBlur", Enum.RenderPriority.Camera.Value + 1, function()
        local cam = workspace.CurrentCamera
        local speed = (cam.CFrame.Position - lastCF.Position).Magnitude
        local rot = (cam.CFrame.LookVector - lastCF.LookVector).Magnitude
        local target = math.min((speed * 2) + (rot * 40), 10)
        blur.Size = blur.Size + (target - blur.Size) * 0.2
        lastCF = cam.CFrame
    end)

    Notify("RTX適用完了")
end

-- ボタン登録
CreateButton(MainTab, "RTX", function()
    ApplyZenithRTX_V2()
end)
---------------------------------
-- 👁️ Mainタブ：詳細プレイヤーESP (戦闘中判定・最優先版)
---------------------------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- 1. 重複防止リセット
if _G.ESPConnection then _G.ESPConnection:Disconnect() end
if _G.ESPObjects then
    for _, user in pairs(_G.ESPObjects) do
        for _, obj in pairs(user) do pcall(function() obj:Remove() end) end
    end
end
_G.ESPObjects = {}
_G.ESPEnabled = false

-- --- 補助関数 ---
local function isInsideSafeZone(plr)
    local char = plr.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return false end
    local folder = workspace:FindFirstChild("_WorldOrigin") and workspace._WorldOrigin:FindFirstChild("SafeZones")
    if not folder then return false end
    for _, obj in pairs(folder:GetChildren()) do
        if obj:IsA("BasePart") then
            local center, size = obj.Position, obj.Size
            local sm = obj:FindFirstChildOfClass("SpecialMesh")
            if sm then size = size * sm.Scale end
            local p = hrp.Position
            if (p.X >= center.X - size.X/2 and p.X <= center.X + size.X/2) and
               (p.Z >= center.Z - size.Z/2 and p.Z <= center.Z + size.Z/2) then return true end
        end
    end
    return false
end

local function checkKenActive(plr)
    return plr:GetAttribute("KenActive") == true or (plr:FindFirstChild("KenActive", true) and plr:FindFirstChild("KenActive", true).Value == true)
end

local function formatNumber(n)
    if not n or type(n) ~= "number" then return "0" end
    if n >= 1e6 then return string.format("%.1fM", n/1e6)
    elseif n >= 1e3 then return string.format("%.1fk", n/1e3)
    else return tostring(n) end
end

-- --- ESP作成 ---
local function CreateESP(plr)
    if plr == LocalPlayer then return end
    local function NewText(size, color)
        local t = Drawing.new("Text")
        t.Size = size; t.Color = color; t.Center = true; t.Outline = true; t.Visible = false
        return t
    end
    _G.ESPObjects[plr] = {
        Line = Drawing.new("Line"),
        NameText = NewText(16, Color3.new(1, 1, 1)),      -- 名前：白
        PvpText = NewText(14, Color3.new(1, 1, 1)),       -- PvP/Combat：可変
        KenText = NewText(14, Color3.new(1, 0, 1)),       -- 見聞：紫
        StatText = NewText(13, Color3.new(1, 0.8, 0)),    -- 賞金：黄
        HPText = NewText(13, Color3.new(0, 1, 0))         -- HP：緑
    }
end

for _, p in ipairs(Players:GetPlayers()) do CreateESP(p) end
Players.PlayerAdded:Connect(CreateESP)

-- --- メインループ ---
_G.ESPConnection = RunService.RenderStepped:Connect(function()
    for plr, objs in pairs(_G.ESPObjects) do
        if not _G.ESPEnabled then
            for _, o in pairs(objs) do o.Visible = false end
            continue
        end

        local char = plr.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        local hum = char and char:FindFirstChildOfClass("Humanoid")

        if hrp and hum then
            local pos, onScreen = Camera:WorldToViewportPoint(hrp.Position)
            if onScreen then
                local dist = math.floor((hrp.Position - Camera.CFrame.Position).Magnitude)
                local inSafe = isInsideSafeZone(plr)
                local pvpOff = plr:GetAttribute("PvpDisabled")
                local hasKen = checkKenActive(plr)
                local stats = plr:FindFirstChild("leaderstats")
                local bounty = stats and stats:FindFirstChild("Bounty/Honor")

                -- --- InCombat判定ロジック ---
                local combatVal = char:GetAttribute("InCombat") or (char:FindFirstChild("InCombat") and char:FindFirstChild("InCombat").Value)
                local isInCombat = type(combatVal) == "number" and combatVal > 0
                -- ---------------------------

                -- 描画：線
                objs.Line.From = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y)
                objs.Line.To = Vector2.new(pos.X, pos.Y)
                objs.Line.Visible = true

                -- 1. 名前と距離
                objs.NameText.Text = string.format("%s [%dm]", plr.DisplayName, dist)
                objs.NameText.Position = Vector2.new(pos.X, pos.Y - 80)
                objs.NameText.Visible = true

                -- 2. PvP & 状態 (戦闘中を最優先)
                local info = ""
                if isInCombat then
                    -- 戦闘中の場合：セーフゾーン無視、赤文字
                    info = "PvP: ON (InCombat)"
                    objs.PvpText.Color = Color3.new(1, 0, 0) -- 赤
                elseif inSafe then
                    -- セーフゾーン：水色
                    info = (pvpOff and "PvP: OFF" or "PvP: ON") .. " (SAFE)"
                    objs.PvpText.Color = Color3.new(0, 1, 1) -- 水色
                else
                    -- 通常時
                    info = pvpOff and "PvP: OFF" or "PvP: ON"
                    objs.PvpText.Color = pvpOff and Color3.new(0.7, 0.7, 0.7) or Color3.new(1, 0, 0)
                end
                objs.PvpText.Text = info
                objs.PvpText.Position = Vector2.new(pos.X, pos.Y - 65)
                objs.PvpText.Visible = true

                -- 3. 見聞色
                objs.KenText.Text = hasKen and "● 見聞あり ●" or "○ 見聞なし ○"
                objs.KenText.Position = Vector2.new(pos.X, pos.Y - 50)
                objs.KenText.Visible = true

                -- 4. 賞金
                objs.StatText.Text = bounty and "Bounty: " .. formatNumber(bounty.Value) or ""
                objs.StatText.Position = Vector2.new(pos.X, pos.Y - 35)
                objs.StatText.Visible = true

                -- 5. HP
                objs.HPText.Text = "HP: " .. math.floor(hum.Health)
                objs.HPText.Position = Vector2.new(pos.X, pos.Y - 20)
                objs.HPText.Visible = true
            else
                for _, o in pairs(objs) do o.Visible = false end
            end
        else
            for _, o in pairs(objs) do o.Visible = false end
        end
    end
end)

-- --- UI統合 (トグル設定) ---
-- お使いのUIライブラリに合わせて "CreateToggle(MainTab..." の部分は適宜調整してください
CreateToggle(MainTab, "ESP (Detailed)", false, function(state)
    _G.ESPEnabled = state
end)
---------------------------------
-- ⚓ BloxFruits：テレポート ＆ キーバインド
---------------------------------
task.spawn(function()
    -- 1. 座標と設定の初期化
    local ghostShipPos = Vector3.new(970.4629, 252.0259, 32872.1875)
    local safeTPpos = Vector3.new(-285.2947082519531, 305.8865966796875, 590.4352416992188)

    -- 設定がなければデフォルト値をセット
    if not _G.NebulaSettings then _G.NebulaSettings = {} end
    _G.NebulaSettings.GhostShipKey = _G.NebulaSettings.GhostShipKey or "H"
    _G.NebulaSettings.MansionKey = _G.NebulaSettings.MansionKey or "V"

    local isChangingGhostKey = false
    local isChangingMansionKey = false

    -- 2. TP関数
    local function FastTP(pos, name)
        local char = game.Players.LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if root then
            root.CFrame = CFrame.new(pos)
            Notify(name .. " へテレポートしました")
        else
            Notify("キャラクターが見つかりません")
        end
    end

    -- 3. キー入力監視
    game:GetService("UserInputService").InputBegan:Connect(function(input, processed)
        if processed then return end
        
        -- キー設定変更中の処理
        if isChangingGhostKey then
            _G.NebulaSettings.GhostShipKey = input.KeyCode.Name
            isChangingGhostKey = false
            Notify("幽霊船キーを " .. input.KeyCode.Name .. " に設定しました")
            return
        end
        if isChangingMansionKey then
            _G.NebulaSettings.MansionKey = input.KeyCode.Name
            isChangingMansionKey = false
            Notify("マンションキーを " .. input.KeyCode.Name .. " に設定しました")
            return
        end

        -- 通常のキー判定
        if input.KeyCode.Name == _G.NebulaSettings.GhostShipKey then
            FastTP(ghostShipPos, "幽霊船")
        elseif input.KeyCode.Name == _G.NebulaSettings.MansionKey then
            FastTP(safeTPpos, "マンション")
        end
    end)

    -- 4. GUIボタン追加 (BloxFruitsTabに追加)
    if BloxFruitsTab then
        -- --- 幽霊船セクション ---
        local GhostFrame = Instance.new("Frame", BloxFruitsTab)
        GhostFrame.Size = UDim2.new(1, -10, 0, 45)
        GhostFrame.BackgroundTransparency = 1

        -- TPボタン
        local GhostBtn = Instance.new("TextButton", GhostFrame)
        GhostBtn.Size = UDim2.new(0.65, 0, 0, 40)
        GhostBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 80)
        GhostBtn.Text = "幽霊船へTP"
        GhostBtn.TextColor3 = Color3.new(1, 1, 1)
        Instance.new("UICorner", GhostBtn)
        GhostBtn.MouseButton1Click:Connect(function() FastTP(ghostShipPos, "幽霊船") end)

        -- キー設定ボタン
        local GhostKeyBtn = Instance.new("TextButton", GhostFrame)
        GhostKeyBtn.Size = UDim2.new(0.3, 0, 0, 40)
        GhostKeyBtn.Position = UDim2.new(0.7, 0, 0, 0)
        GhostKeyBtn.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
        GhostKeyBtn.Text = "[" .. _G.NebulaSettings.GhostShipKey .. "]"
        GhostKeyBtn.TextColor3 = Color3.new(1, 0.8, 0)
        Instance.new("UICorner", GhostKeyBtn)
        
        GhostKeyBtn.MouseButton1Click:Connect(function()
            GhostKeyBtn.Text = "入力待機..."
            isChangingGhostKey = true
            task.spawn(function()
                while isChangingGhostKey do task.wait() end
                GhostKeyBtn.Text = "[" .. _G.NebulaSettings.GhostShipKey .. "]"
            end)
        end)

        -- --- マンションセクション ---
        local MansionFrame = Instance.new("Frame", BloxFruitsTab)
        MansionFrame.Size = UDim2.new(1, -10, 0, 45)
        MansionFrame.BackgroundTransparency = 1

        -- TPボタン
        local MansionBtn = Instance.new("TextButton", MansionFrame)
        MansionBtn.Size = UDim2.new(0.65, 0, 0, 40)
        MansionBtn.BackgroundColor3 = Color3.fromRGB(80, 40, 40)
        MansionBtn.Text = "マンションへTP"
        MansionBtn.TextColor3 = Color3.new(1, 1, 1)
        Instance.new("UICorner", MansionBtn)
        MansionBtn.MouseButton1Click:Connect(function() FastTP(safeTPpos, "マンション") end)

        -- キー設定ボタン
        local MansionKeyBtn = Instance.new("TextButton", MansionFrame)
        MansionKeyBtn.Size = UDim2.new(0.3, 0, 0, 40)
        MansionKeyBtn.Position = UDim2.new(0.7, 0, 0, 0)
        MansionKeyBtn.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
        MansionKeyBtn.Text = "[" .. _G.NebulaSettings.MansionKey .. "]"
        MansionKeyBtn.TextColor3 = Color3.new(1, 0.8, 0)
        Instance.new("UICorner", MansionKeyBtn)

        MansionKeyBtn.MouseButton1Click:Connect(function()
            MansionKeyBtn.Text = "入力待機..."
            isChangingMansionKey = true
            task.spawn(function()
                while isChangingMansionKey do task.wait() end
                MansionKeyBtn.Text = "[" .. _G.NebulaSettings.MansionKey .. "]"
            end)
        end)
    end
end)
--------------------------------------------------
-- 1. 設定の初期値
--------------------------------------------------
if not _G.NebulaSettings then _G.NebulaSettings = {} end
_G.NebulaSettings.InvFlyEnabled = false
_G.NebulaSettings.InvFlyKey = _G.NebulaSettings.InvFlyKey or "B" -- デフォルトBキー
_G.NebulaSettings.InvFlySpeed = 300 -- 要望の初期スピード300

local HIDE_Y = -199996.48 -- 本体を隠す座標
local FakeCharacter, BodyVelocity, BodyGyro, MoveConnection
local isChangingKey = false
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

--------------------------------------------------
-- 2. Invisible & Fly (Y-BANG方式) 核心処理
--------------------------------------------------
local function ToggleInvisible(state)
    _G.NebulaSettings.InvFlyEnabled = state
    local char = game.Players.LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChild("Humanoid")

    if state then
        -- 【オン】クローン作成と本体の奈落移動
        if FakeCharacter then FakeCharacter:Destroy() end
        char.Archivable = true
        FakeCharacter = char:Clone()
        FakeCharacter.Parent = workspace
        
        -- クローンの見た目を半透明にする
        for _, v in ipairs(FakeCharacter:GetDescendants()) do
            if v:IsA("BasePart") then
                v.CanCollide = false
                v.Transparency = 0.5
            end
        end

        -- カメラをクローンに追従させる
        workspace.CurrentCamera.CameraSubject = FakeCharacter.Humanoid
        
        -- 移動用の物理パーツ設定
        BodyVelocity = Instance.new("BodyVelocity", FakeCharacter.HumanoidRootPart)
        BodyVelocity.MaxForce = Vector3.new(1e6, 1e6, 1e6)
        BodyVelocity.Velocity = Vector3.zero

        BodyGyro = Instance.new("BodyGyro", FakeCharacter.HumanoidRootPart)
        BodyGyro.MaxTorque = Vector3.new(1e6, 1e6, 1e6)
        BodyGyro.CFrame = FakeCharacter.HumanoidRootPart.CFrame

        -- 本体の固定（奈落にBANG）
        task.spawn(function()
            while _G.NebulaSettings.InvFlyEnabled and hrp do
                hrp.CFrame = CFrame.new(hrp.Position.X, HIDE_Y, hrp.Position.Z)
                hrp.AssemblyLinearVelocity = Vector3.zero
                task.wait()
            end
        end)

        -- フライ操作（RenderSteppedで滑らかに移動）
        MoveConnection = RunService.RenderStepped:Connect(function()
            if not FakeCharacter then return end
            local cam = workspace.CurrentCamera
            local moveDir = hum.MoveDirection
            local velocity = Vector3.zero

            -- 水平移動 (W,A,S,D)
            if moveDir.Magnitude > 0 then
                velocity = (cam.CFrame.LookVector * -moveDir.Z) + (cam.CFrame.RightVector * moveDir.X)
            end
            -- 上下移動 (Space / LeftShift)
            if UIS:IsKeyDown(Enum.KeyCode.Space) then
                velocity += Vector3.new(0, 1, 0)
            elseif UIS:IsKeyDown(Enum.KeyCode.LeftShift) then
                velocity += Vector3.new(0, -1, 0)
            end

            BodyVelocity.Velocity = velocity.Unit * _G.NebulaSettings.InvFlySpeed
            if velocity.Magnitude == 0 then BodyVelocity.Velocity = Vector3.zero end
            BodyGyro.CFrame = cam.CFrame
        end)
        
        Notify("Inv-Fly: 有効 (Speed:".._G.NebulaSettings.InvFlySpeed..")")
    else
        -- 【オフ】本体をクローンの位置に戻して解除
        if MoveConnection then MoveConnection:Disconnect() end
        if FakeCharacter and hrp then
            hrp.CFrame = FakeCharacter.HumanoidRootPart.CFrame
            FakeCharacter:Destroy()
            FakeCharacter = nil
        end
        workspace.CurrentCamera.CameraSubject = hum
        Notify("Inv-Fly: 解除しました")
    end
end

--------------------------------------------------
-- 3. キーバインド入力
--------------------------------------------------
UIS.InputBegan:Connect(function(input, processed)
    if processed then return end
    
    -- キー設定変更中の場合
    if isChangingKey then
        _G.NebulaSettings.InvFlyKey = input.KeyCode.Name
        isChangingKey = false
        Notify("Flyキーを " .. input.KeyCode.Name .. " に変更")
        return
    end

    -- 指定キーでオンオフ
    if input.KeyCode.Name == _G.NebulaSettings.InvFlyKey then
        _G.NebulaSettings.InvFlyEnabled = not _G.NebulaSettings.InvFlyEnabled
        ToggleInvisible(_G.NebulaSettings.InvFlyEnabled)
    end
end)

--------------------------------------------------
-- 4. MainタブへのUI追加
--------------------------------------------------
if MainTab then
    -- メイントグル
    CreateToggle(MainTab, "Invisible & Fly", false, function(state)
        ToggleInvisible(state)
    end)

    -- スピード調整 (要望通り初期300)
    CreateSlider(MainTab, "Flyスピード", 10, 800, 300, function(val)
        _G.NebulaSettings.InvFlySpeed = val
    end)

    -- キー設定ボタン
    local KeyBtn = Instance.new("TextButton", MainTab)
    KeyBtn.Size = UDim2.new(1, -10, 0, 40)
    KeyBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    KeyBtn.Text = "オンオフキー: [" .. _G.NebulaSettings.InvFlyKey .. "]"
    KeyBtn.TextColor3 = Color3.new(1, 1, 1)
    KeyBtn.Font = Enum.Font.SourceSansBold
    KeyBtn.TextSize = 16
    Instance.new("UICorner", KeyBtn)

    KeyBtn.MouseButton1Click:Connect(function()
        KeyBtn.Text = "キー入力待機..."
        isChangingKey = true
        task.spawn(function()
            while isChangingKey do task.wait() end
            KeyBtn.Text = "オンオフキー: [" .. _G.NebulaSettings.InvFlyKey .. "]"
        end)
    end)
end
---------------------------------
-- BloxFruitsタブ：Extreme Speed M1 (超速3連射版)
---------------------------------
local LP = game:GetService("Players").LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- 設定初期化
settings.ExtremeM1 = false

-- リモート取得
local Net = ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net")
local RegAttack = Net["RE/RegisterAttack"]
local RegHit = Net["RE/RegisterHit"]

-- ターゲット取得 (700スタッド以内の一番近い敵)
local function GetExtremeTarget()
    local char = LP.Character
    if not char then return nil end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return nil end
    
    for _, v in pairs(workspace.Enemies:GetChildren()) do
        local hum = v:FindFirstChild("Humanoid")
        local eroot = v:FindFirstChild("HumanoidRootPart")
        if hum and eroot and hum.Health > 0 then
            local dist = (root.Position - eroot.Position).Magnitude
            if dist < 700 then return {v, eroot} end
        end
    end
    return nil
end

-- 実を検索
local function GetExtremeFruit()
    for _, t in pairs(LP.Backpack:GetChildren()) do
        if t:IsA("Tool") and t.ToolTip == "Blox Fruit" then return t end
    end
    local held = LP.Character:FindFirstChildOfClass("Tool")
    if held and held.ToolTip == "Blox Fruit" then return held end
    return nil
end

-- 超速攻撃メインロジック
local function ExecuteExtreme()
    local char = LP.Character
    local targetData = GetExtremeTarget()
    if not char or not targetData then return end
    
    local fruit = GetExtremeFruit()
    if not fruit then return end

    -- サーバーへ攻撃信号をコルーチンで高速送信
    coroutine.wrap(function()
        -- 攻撃開始パケット
        RegAttack:FireServer(0)
        
        -- ヒット判定を3連射 (一気にHPを削る)
        for i = 1, 3 do 
            RegHit:FireServer(targetData[2], {{targetData[1], targetData[2]}})
        end

        -- 実のエフェクト発生
        local remote = fruit:FindFirstChild("LeftClickRemote")
        if remote then
            remote:FireServer(char.HumanoidRootPart.CFrame.LookVector, 1)
        end
    end)()
end

-- 実行管理ループ (実用上の最速 0.01秒間隔)
task.spawn(function()
    while true do
        if settings.ExtremeM1 then
            pcall(ExecuteExtreme)
        end
        task.wait(0.01) 
    end
end)

-- 既存の「BloxFruitsTab」にトグルを追加
if BloxFruitsTab then
    CreateToggle(BloxFruitsTab, "Extreme Speed M1", settings.ExtremeM1, function(v)
        settings.ExtremeM1 = v
        if v then
            Notify("Extreme M1 有効 )")
        else
            Notify("Extreme M1 無効")
        end
    end)
end
---------------------------------
-- BloxFruitsタブ：Skull Guitar Auto TAP (Player + NPC 対応版)
---------------------------------
local LP = game:GetService("Players").LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- 設定初期化
settings.SoulGuitarAuto = false

-- リモート取得
local GunValidator = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("Validator2")

-- GCからValidator用関数を特定 (検知回避)
local old_shoot
for _, v in pairs(getgc(true)) do
    if type(v) == "table" and rawget(v, "Attack") and type(v.Attack) == "function" then
        local s, val = pcall(getupvalue, v.Attack, 9)
        if s then old_shoot = val; break end
    end
end

-- ターゲット取得 (プレイヤー優先、次にNPC)
local function GetSkullTargetAll()
    local char = LP.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return nil end
    local myRoot = char.HumanoidRootPart
    local nearest = nil
    local minDist = 1000 -- 射程距離

    -- Characters (プレイヤー) と Enemies (NPC) の両方をスキャン
    local folders = {workspace:FindFirstChild("Characters"), workspace:FindFirstChild("Enemies")}

    for _, folder in pairs(folders) do
        if folder then
            for _, obj in pairs(folder:GetChildren()) do
                if obj ~= char then
                    local hum = obj:FindFirstChild("Humanoid")
                    local root = obj:FindFirstChild("HumanoidRootPart")
                    if hum and root and hum.Health > 0 then
                        local d = (myRoot.Position - root.Position).Magnitude
                        if d < minDist then
                            minDist = d
                            nearest = root
                        end
                    end
                end
            end
        end
    end
    -- ターゲットがいればその座標、いなければ正面30スタッドを返す
    return nearest and nearest.Position or (myRoot.CFrame * CFrame.new(0, 0, -30)).Position
end

-- ツール取得
local function GetSkullTool()
    local h = LP.Character:FindFirstChild("Skull Guitar")
    if h then return h end
    for _, t in pairs(LP.Backpack:GetChildren()) do
        if t.Name == "Skull Guitar" then return t end
    end
    return nil
end

-- メイン実行ロジック
local function ExecuteSkullAll()
    if not settings.SoulGuitarAuto then return end
    
    local skull = GetSkullTool()
    if not skull or not skull:FindFirstChild("RemoteEvent") then return end
    
    local pos = GetSkullTargetAll()
    if not pos then return end

    pcall(function()
        -- Validator計算と送信 (検知回避)
        if old_shoot then
            local v1, v2, v3, v4, v5, v6, v7 = getupvalue(old_shoot, 15), getupvalue(old_shoot, 13), getupvalue(old_shoot, 16), getupvalue(old_shoot, 17), getupvalue(old_shoot, 14), getupvalue(old_shoot, 12), getupvalue(old_shoot, 18)
            local v9 = (v5 * v2 + v6 * v1) % v3
            v9 = (v9 * v3 + (v6 * v2)) % v4
            v5, v6, v7 = math.floor(v9/v3), v9-(math.floor(v9/v3)*v3), v7+1
            setupvalue(old_shoot, 15, v1); setupvalue(old_shoot, 13, v2); setupvalue(old_shoot, 16, v3); setupvalue(old_shoot, 17, v4); setupvalue(old_shoot, 14, v5); setupvalue(old_shoot, 12, v6); setupvalue(old_shoot, 18, v7)
            GunValidator:FireServer(math.floor(v9/v4 * 16777215), v7)
        end
        -- 攻撃送信
        skull.RemoteEvent:FireServer("TAP", pos)
    end)
end

-- 超速ループ (0.01秒)
task.spawn(function()
    while true do
        if settings.SoulGuitarAuto then
            ExecuteSkullAll()
        end
        task.wait(0.01)
    end
end)

-- 既存の「BloxFruitsTab」にトグルを追加
if BloxFruitsTab then
    CreateToggle(BloxFruitsTab, "Skull Guitar (All-Target)", settings.SoulGuitarAuto, function(v)
        settings.SoulGuitarAuto = v
        if v then
            Notify("Skull Guitar: 全対象・常時連射 ON")
        else
            Notify("Skull Guitar: OFF")
        end
    end)
end
-- ==================================================
-- Blox Fruits: Buso & Energy (Z-Delayと同じ形式)
-- ==================================================
task.spawn(function()
    -- タブの特定 (お前のコードを完全継承)
    local BF = (typeof(BloxFruitsTab) == "Instance" or typeof(BloxFruitsTab) == "userdata") and BloxFruitsTab or MainTab 
    if not BF then return end

    local LP = game:GetService("Players").LocalPlayer
    local RS = game:GetService("RunService")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local CollectionService = game:GetService("CollectionService")

    -- リモート取得
    local CommF = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("CommF_")

    --------------------------------------------------
    -- 1. Auto Buso Haki (自動武装)
    --------------------------------------------------
    _G.AutoBuso = false
    -- お前がさっき見せた「CreateToggle(BF, ...)」の形式だ
    CreateToggle(BF, "Auto Buso Haki", false, function(state)
        _G.AutoBuso = state
    end)

    task.spawn(function()
        while true do
            task.wait(0.5)
            if _G.AutoBuso then
                pcall(function()
                    local char = LP.Character
                    if char and CollectionService:HasTag(char, "Buso") and not char:FindFirstChild("HasBuso") then
                        CommF:InvokeServer("Buso")
                    end
                end)
            end
        end
    end)
-- ==================================================
-- Blox Fruits 機能まとめ (FastAttack常時動作型)
-- ==================================================
task.spawn(function()
    local BF = BloxFruitsTab -- タブ変数名
    local LP = game:GetService("Players").LocalPlayer
    local ReplicatedStorage = game:GetService("ReplicatedStorage")

    -- FastAttack用の通信定義
    local FA_Net = ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net")
    local FA_RE_RegisterAttack = FA_Net["RE/RegisterAttack"]
    local FA_RE_RegisterHit = FA_Net["RE/RegisterHit"]
    local FA_Enabled = false
    local FA_ClickDelay = 0.01
    local FA_Distance = 1000

    --------------------------------------------------
    -- ターゲット取得関数 (FastAttack用)
    --------------------------------------------------
    local function GetFATargets()
        local targets = {}
        local nearestPart = nil
        local char = LP.Character
        local myRoot = char and char:FindFirstChild("HumanoidRootPart")
        if not myRoot then return nil, {} end

        local function scan(folder)
            if not folder then return end
            for _, obj in pairs(folder:GetChildren()) do
                if obj ~= char then
                    local hum = obj:FindFirstChild("Humanoid")
                    local root = obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChild("Head")
                    if hum and root and hum.Health > 0 then
                        local dist = (myRoot.Position - root.Position).Magnitude
                        if dist <= FA_Distance then
                            table.insert(targets, {obj, root})
                            nearestPart = root
                        end
                    end
                end
            end
        end
        scan(workspace:FindFirstChild("Enemies"))
        scan(workspace:FindFirstChild("Characters"))
        return nearestPart, targets
    end

    --------------------------------------------------
    -- 1. Fast Attack (高速攻撃) - トグル統合
    --------------------------------------------------
    CreateToggle(BF, "Fast Attack", false, function(v)
        FA_Enabled = v
    end)

    local function ExecuteFA()
        local char = LP.Character
        local tool = char and char:FindFirstChildOfClass("Tool")
        -- 武器チェック
        if not tool or not (tool.ToolTip == "Melee" or tool.ToolTip == "Sword" or tool.ToolTip == "Blox Fruit") then return end

        local targetPart, enemies = GetFATargets()

        -- 【修正点】敵がいなくてもRegisterAttackは常に送る
        FA_RE_RegisterAttack:FireServer(FA_ClickDelay)

        -- 敵がいる時だけヒット判定を送信
        if targetPart and #enemies > 0 then
            FA_RE_RegisterHit:FireServer(targetPart, enemies)
        end
        
        -- 【修正点】LeftClickRemoteも常に送る (敵がいなければ正面を向いて空振り)
        if tool:FindFirstChild("LeftClickRemote") then
            local direction
            if targetPart then
                direction = (targetPart.Position - char:GetPivot().Position).Unit
            else
                direction = char.HumanoidRootPart.CFrame.LookVector
            end
            tool.LeftClickRemote:FireServer(direction, 1)
        end
    end

    -- メインループ
    task.spawn(function()
        while true do
            if FA_Enabled then 
                pcall(ExecuteFA) 
            end
            task.wait(FA_ClickDelay)
        end
    end)
end)
    --------------------------------------------------
    -- 2. Infinite Energy (無限エネルギー)
    --------------------------------------------------
    _G.InfiniteEnergy = false
    CreateToggle(BF, "Infinite Energy", false, function(state)
        _G.InfiniteEnergy = state
    end)

    RS.Heartbeat:Connect(function()
        if _G.InfiniteEnergy then
            local char = LP.Character
            if char and char:FindFirstChild("Energy") then
                char.Energy.Value = char.Energy.MaxValue
            end
        end
    end)
end)
-- ==================================================
-- Blox Fruits 機能まとめ (FastAttack統合 & エラー修正版)
-- ==================================================
task.spawn(function()
    local BF = BloxFruitsTab -- タブ変数名
    local LP = game:GetService("Players").LocalPlayer
    local ReplicatedStorage = game:GetService("ReplicatedStorage")



    --------------------------------------------------
    -- 2. Fruit Sniper (実の自動回収)
    --------------------------------------------------
    CreateToggle(BF, "Fruit Sniper", false, function(v)
        _G.FruitSniper = v
    end)

    task.spawn(function()
        while task.wait(1) do
            if _G.FruitSniper then
                pcall(function()
                    for _, v in pairs(workspace:GetChildren()) do
                        if v:IsA("Tool") and v:FindFirstChild("Handle") then
                            local root = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
                            if root then
                                root.CFrame = v.Handle.CFrame
                                task.wait(0.1)
                                firetouchinterest(root, v.Handle, 0)
                                firetouchinterest(root, v.Handle, 1)
                            end
                        end
                    end
                end)
            end
        end
    end)
--------------------------------------------------
-- 3. Mob 操作 (Bring & Freeze) - 決定版
--------------------------------------------------
CreateToggle(BF, "Bring Mob (敵寄せ)", false, function(v) _G.BringMob = v end)
CreateToggle(BF, "Freeze Mob (敵固定)", false, function(v) _G.FreezeMob = v end)

task.spawn(function()
    -- RenderSteppedに近い速度で回すためにwaitを最小限に
    while true do
        task.wait() 
        pcall(function()
            if _G.BringMob or _G.FreezeMob then
                local lpRoot = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
                if not lpRoot then return end
                
                -- 自分の正面5スタッドの位置を計算
                local targetPos = lpRoot.CFrame * CFrame.new(0, 0, -5)

                for _, v in pairs(workspace.Enemies:GetChildren()) do
                    local mRoot = v:FindFirstChild("HumanoidRootPart")
                    local mHum = v:FindFirstChild("Humanoid")
                    
                    if mRoot and mHum and mHum.Health > 0 then
                        -- 1. Bring機能 (他人がいても座標を奪い取る)
                        if _G.BringMob then
                            -- 物理演算を完全に黙らせる
                            mRoot.Velocity = Vector3.zero
                            mRoot.RotVelocity = Vector3.zero
                            mRoot.AssemblyLinearVelocity = Vector3.zero
                            
                            -- 座標を強制固定
                            mRoot.CFrame = targetPos
                            mRoot.CanCollide = false
                        end

                        -- 2. Freeze機能
                        -- Bring中はAnchoredをオフにしないと同期がバグるため、排他制御
                        if _G.FreezeMob and not _G.BringMob then
                            mRoot.Anchored = true
                            mHum.WalkSpeed = 0
                        elseif not _G.FreezeMob then
                            mRoot.Anchored = false
                        end
                    end
                end
            end
        end)
    end
end)

    --------------------------------------------------
    -- 4. Kill Aura (範囲内即死)
    --------------------------------------------------
    _G.KillRange = 50 
    CreateToggle(BF, "Kill Aura", false, function(v) _G.KillAura = v end)

    task.spawn(function()
        while task.wait() do
            if _G.KillAura then
                pcall(function()
                    local lpRoot = LP.Character.HumanoidRootPart
                    for _, v in pairs(workspace.Enemies:GetChildren()) do
                        local root = v:FindFirstChild("HumanoidRootPart")
                        local hum = v:FindFirstChild("Humanoid")
                        if root and hum and (root.Position - lpRoot.Position).Magnitude < _G.KillRange then
                            hum.Health = 0
                        end
                    end
                end)
            end
        end
    end)
end) -- 全体のtask.spawnを閉じる
-- ==================================================
-- Water Walking 単体セクション (修正版)
-- ==================================================
task.spawn(function()
    -- タブの変数名を指定 (環境に合わせて調整してください)
    local BF = (typeof(BloxFruitsTab) == "Instance" or typeof(BloxFruitsTab) == "userdata") and BloxFruitsTab or MainTab 
    
    if not BF then return end

    local LP = game:GetService("Players").LocalPlayer

    --------------------------------------------------
    -- Water Walking (水上歩行)
    --------------------------------------------------
    CreateToggle(BF, "Water Walking", false, function(state)
        _G.WaterWalkingEnabled = state
        
        if state then
            -- 【オン】の処理：ループ開始
            task.spawn(function()
                while _G.WaterWalkingEnabled do
                    local name = LP.Name
                    local char = game:GetService("Workspace").Characters:FindFirstChild(name)
                    
                    if char then
                        -- 全パターンの属性をオンにする
                        pcall(function() char.WaterWalking = true end)
                        pcall(function() char:SetAttribute("WaterWalking", true) end)
                        local ww = char:FindFirstChild("WaterWalking")
                        if ww then pcall(function() ww.Value = true end) end
                    end
                    task.wait(0.5)
                end
            end)
        else
            -- 【オフ】の処理：すべての属性を即座にfalseに戻す
            pcall(function()
                local name = LP.Name
                local char = game:GetService("Workspace").Characters:FindFirstChild(name)
                if char then
                    char.WaterWalking = false
                    char:SetAttribute("WaterWalking", false)
                    local ww = char:FindFirstChild("WaterWalking")
                    if ww then ww.Value = false end
                end
            end)
        end
    end)

end) -- EOFエラーを防ぐための閉じカッコ
-- ==================================================
-- NPC BANG (BloxFruitsタブ統合版)
-- ==================================================
task.spawn(function()
    local BF = (typeof(BloxFruitsTab) == "Instance" or typeof(BloxFruitsTab) == "userdata") and BloxFruitsTab or MainTab 
    if not BF then return end

    local LP = game:GetService("Players").LocalPlayer
    local RunService = game:GetService("RunService")

    -- 設定値
    _G.NPCBangEnabled = false
    _G.NPCBangRange = 100 -- 初期範囲

    --------------------------------------------------
    -- UI追加
    --------------------------------------------------
    -- オンオフ切り替え
    CreateToggle(BF, "NPC BANG", false, function(state)
        _G.NPCBangEnabled = state
    end)

    -- 範囲調整スライダー (10 ~ 1000)
    -- ※あなたのライブラリのCreateSliderの引数に合わせて調整してください
    CreateSlider(BF, "BANG 探索範囲", 10, 1000, 100, function(val)
        _G.NPCBangRange = val
    end)

    --------------------------------------------------
    -- メインループ
    --------------------------------------------------
    RunService.Heartbeat:Connect(function()
        if not _G.NPCBangEnabled then return end
        
        local char = LP.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not root then return end

        local target = nil
        local closestDist = _G.NPCBangRange -- 設定した範囲内のみ

        -- Blox Fruitsの敵とNPCを探索
        local folders = {workspace:FindFirstChild("Enemies"), workspace:FindFirstChild("NPCs"), workspace}
        
        for _, folder in pairs(folders) do
            if folder then
                for _, obj in pairs(folder:GetChildren()) do
                    -- モデルであり、生きているか、ターゲット用のRootPartがあるか
                    if obj:IsA("Model") and obj:FindFirstChild("Humanoid") and obj.Humanoid.Health > 0 and obj:FindFirstChild("HumanoidRootPart") then
                        
                        -- Shadow（影）を除外する判定
                        local nameLower = string.lower(obj.Name)
                        local displayNameLower = string.lower(obj.Humanoid.DisplayName)
                        
                        if not string.find(nameLower, "shadow") and not string.find(displayNameLower, "shadow") then
                            if obj.Name ~= LP.Name then
                                local dist = (root.Position - obj.HumanoidRootPart.Position).Magnitude
                                -- 範囲内で最も近い敵を探す
                                if dist < closestDist then
                                    closestDist = dist
                                    target = obj
                                end
                            end
                        end
                    end
                end
            end
            if target then break end
        end

        if target then
            -- ターゲットの頭上12スタッドにテレポート固定
            root.CFrame = target.HumanoidRootPart.CFrame * CFrame.new(0, 12, 0)
            -- 速度をゼロにして震えを抑える
            root.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
        end
    end)
end)
-- ==================================================
-- Blox Fruits: Race V4 & V3 分離セクション
-- ==================================================
task.spawn(function()
    local BF = (typeof(BloxFruitsTab) == "Instance" or typeof(BloxFruitsTab) == "userdata") and BloxFruitsTab or MainTab 
    if not BF then return end

    local LP = game:GetService("Players").LocalPlayer
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local RS = game:GetService("RunService")

    -- [[ 設定 ]]
    _G.AutoRaceV4_Force = false
    _G.AutoRaceV3_Ability = false
    
    local AbilityCooldown = 0
    local RaceAbilities = {
        "Last Resort", "Agility", "Water Body", "Heavenly Blood",
        "Heightened Senses", "Energy Core", "Primordial Reign"
    }

    local function HasAbility()
        local char = LP.Character
        if not char then return false end
        for _, v in ipairs(RaceAbilities) do
            if LP.Backpack:FindFirstChild(v) or char:FindFirstChild(v) then return true end
        end
        return false
    end

    --------------------------------------------------
    -- 1. UI追加 (2つのボタンに分離)
    --------------------------------------------------
    CreateToggle(BF, "Auto Race V4 (強制変身)", false, function(v)
        _G.AutoRaceV4_Force = v
    end)

    CreateToggle(BF, "Auto Race Ability (V3スキル連打)", false, function(v)
        _G.AutoRaceV3_Ability = v
    end)

    --------------------------------------------------
    -- 2. 独立ロジック
    --------------------------------------------------
    RS.Heartbeat:Connect(function(dt)
        local char = LP.Character
        if not char then return end

        pcall(function()
            -- --- [ ボタン1: V4 強制変身 ] ---
            if _G.AutoRaceV4_Force then
                local energy = char:FindFirstChild("RaceEnergy")
                if energy and energy.Value >= 1 then
                    -- フラグ偽装
                    local rt = char:FindFirstChild("RaceTransformed")
                    if rt then rt.Value = false end
                    
                    -- 強制送信
                    ReplicatedStorage.Remotes.CommE:FireServer("ActivateAbility")
                    
                    local t = LP.Backpack:FindFirstChild("Awakening") or char:FindFirstChild("Awakening")
                    if t and t:FindFirstChild("RemoteFunction") then 
                        t.RemoteFunction:InvokeServer(true) 
                    end
                end
            end

            -- --- [ ボタン2: V3 スキル連打 ] ---
            if _G.AutoRaceV3_Ability then
                local hum = char:FindFirstChildOfClass("Humanoid")
                if hum and hum.Health > 0 and HasAbility() then
                    AbilityCooldown = AbilityCooldown - dt
                    if AbilityCooldown <= 0 then
                        -- スキル発動
                        ReplicatedStorage.Remotes.CommE:FireServer("ActivateAbility")
                        AbilityCooldown = 0.2
                    end
                end
            end
        end)
    end)
end)---------------------------------
-- ⛵ BloxFruitsタブ：Boat Speed (100 - 500)
---------------------------------
-- 1. 設定に追加
_G.BoatMaxSpeed = 100
_G.AutoBoatSpeedEnabled = true

-- 2. ボートの速度を適用・維持するループ
task.spawn(function()
    while task.wait(1) do -- 1秒ごとに新しく出たボートをチェック
        if _G.AutoBoatSpeedEnabled then
            pcall(function()
                local boatsFolder = workspace:FindFirstChild("Boats")
                if boatsFolder then
                    for _, obj in pairs(boatsFolder:GetDescendants()) do
                        -- 車両のシート（運転席）の最高速度を書き換える
                        if obj:IsA("VehicleSeat") then
                            if obj.MaxSpeed ~= _G.BoatMaxSpeed then
                                obj.MaxSpeed = _G.BoatMaxSpeed
                            end
                        end
                    end
                end
            end)
        end
    end
end)

-- 3. BloxFruitsタブにスライダーを追加
-- 形式: CreateSlider(親, 名前, 最小, 最大, 初期値, 関数)
CreateSlider(BloxFruitsTab, "ボートの速度", 100, 500, _G.BoatMaxSpeed, function(val)
    _G.BoatMaxSpeed = val
    _G.AutoBoatSpeedEnabled = true
    
    -- スライダーを動かした瞬間に現在のボートにも適用
    pcall(function()
        local boatsFolder = workspace:FindFirstChild("Boats")
        if boatsFolder then
            for _, obj in pairs(boatsFolder:GetDescendants()) do
                if obj:IsA("VehicleSeat") then
                    obj.MaxSpeed = val
                end
            end
        end
    end)
end)
-- ==================================================
-- Blox Fruits: Raid Hub (一覧ボタン方式・エラー回避版)
-- ==================================================
task.spawn(function()
    local BF = (typeof(BloxFruitsTab) == "Instance" or typeof(BloxFruitsTab) == "userdata") and BloxFruitsTab or MainTab 
    if not BF then return end

    local LP = game:GetService("Players").LocalPlayer
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local TweenService = game:GetService("TweenService")
    local CommF = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("CommF_")

    local Config = {
        RaidAutoMove = false,
        AutoStartRaid = false,
        SelectRaid = "Flame",
        TweenSpeed = 250
    }

    local RaidList = {"Flame", "Ice", "Quake", "Light", "Dark", "Spider", "Rumble", "Magma", "Buddha", "Sand"}
    local LowFruits = {"Rocket-Rocket", "Spin-Spin", "Chop-Chop", "Spring-Spring", "Bomb-Bomb", "Smoke-Smoke"}

    local function TweenTP(targetCFrame)
        local root = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
        if not root then return end
        local dist = (targetCFrame.Position - root.Position).Magnitude
        local tween = TweenService:Create(root, TweenInfo.new(dist/Config.TweenSpeed, Enum.EasingStyle.Linear), {CFrame = targetCFrame})
        tween:Play()
    end

    --------------------------------------------------
    -- 1. レイド選択 (確実に動くボタン一覧方式)
    --------------------------------------------------
    -- 現在の選択状態を表示するだけのラベル（またはボタン）
    CreateButton(BF, "--- Click Below to Select Raid ---", function() end)

    -- 各レイドのボタンを生成
    for _, raidName in pairs(RaidList) do
        CreateButton(BF, "Select: " .. raidName, function()
            Config.SelectRaid = raidName
            -- 画面通知
            pcall(function()
                game:GetService("StarterGui"):SetCore("SendNotification", {
                    Title = "Raid Selected",
                    Text = "Target: " .. raidName,
                    Duration = 2
                })
            end)
        end)
    end

    CreateButton(BF, "----------------------------", function() end)

    -- 安い実を出す
    CreateButton(BF, "Get Low Fruit (実を出す)", function()
        for _, fName in pairs(LowFruits) do
            if CommF:InvokeServer("LoadFruit", fName) then break end
        end
    end)

    --------------------------------------------------
    -- 2. オート機能
    --------------------------------------------------
    CreateToggle(BF, "Raid Auto Move (250)", false, function(v) Config.RaidAutoMove = v end)
    CreateToggle(BF, "Auto Start Raid", false, function(v) Config.AutoStartRaid = v end)

    --------------------------------------------------
    -- 3. 自動実行ループ
    --------------------------------------------------
    task.spawn(function()
        while task.wait(1.5) do
            if Config.AutoStartRaid then
                pcall(function()
                    local TimerUI = LP.PlayerGui.Main:FindFirstChild("Timer")
                    if not (TimerUI and TimerUI.Visible) then
                        local hasChip = LP.Backpack:FindFirstChild("Special Microchip") or LP.Character:FindFirstChild("Special Microchip")
                        if hasChip then
                            local Map = workspace.Map:FindFirstChild("CircleIsland") or workspace.Map:FindFirstChild("Boat Castle")
                            local btn = Map and Map:FindFirstChild("RaidSummon2") and Map.RaidSummon2.Button.Main
                            if btn then TweenTP(btn.CFrame) end
                        else
                            CommF:InvokeServer("RaidsNpc", "Select", Config.SelectRaid)
                            CommF:InvokeServer("RaidsNpc", "Buy")
                        end
                    end
                end)
            end
        end
    end)

    workspace:WaitForChild("Map"):WaitForChild("RaidMap").ChildAdded:Connect(function(child)
        if Config.RaidAutoMove and child.Name:match("RaidIsland") then
            if child.Name:match("RaidIsland1") and not child.Name:match("RaidIsland1[0-9]") then return end
            task.wait(1)
            local target = child:FindFirstChildWhichIsA("BasePart") or (child:IsA("Model") and child.PrimaryPart)
            if target then TweenTP(target.CFrame + Vector3.new(0, 50, 0)) end
        end
    end)
end)
---------------------------------
-- 🕊️ Fly（飛行）実行ボタン追加
---------------------------------
CreateButton(MainTab, "Fly", function()
    -- 外部のフライスクリプトを読み込んで実行
    loadstring(game:HttpGet("https://raw.githubusercontent.com/XNEOFF/FlyGuiV3/main/FlyGuiV3.txt"))()
    Notify("Flyスクリプトを実行しました")
end)
---------------------------------
-- 🟢 Noclip（壁抜け）追加用コード
---------------------------------
-- 1. 設定に項目を追加
settings.Noclip = false

-- 2. Mainタブにトグルを追加
CreateToggle(MainTab, "Noclip（壁抜け）", settings.Noclip, function(state)
    settings.Noclip = state
end)

-- 3. 壁抜けの実行ロジック
game:GetService("RunService").Stepped:Connect(function()
    if settings.Noclip then
        local char = player.Character
        if char then
            for _, part in pairs(char:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = false
                end
            end
        end
    end
end)
---------------------------------
-- 🆕 Player Information (提供コードをそのまま実行)
---------------------------------
local PlayerInfoTab = CreateTab("Player Information")

-- お前のコードをこのタブの中（親）として動かす設定
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

-- 除外リスト
local ignoreList = {
    ["Summon Sea Beast"] = true, ["Awakening"] = true, ["Tool"] = true,
    ["Last Resort"] = true, ["Agility"] = true, ["Water Body"] = true,
    ["Heavenly Blood"] = true, ["Heightened Senses"] = true,
    ["Energy Core"] = true, ["Primordial Reign"] = true
}
local v3Skills = {"Last Resort", "Agility", "Water Body", "Heavenly Blood", "Heightened Senses", "Energy Core", "Primordial Reign"}

-- [[ お前のコードをタブ内に配置 ]]
-- 本来 ScreenGui だったものを、HUBのタブ（ScrollingFrameなど）の中に収まるように調整
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(1, -10, 0, 550) -- 横幅をタブに合わせる
mainFrame.Position = UDim2.new(0, 5, 0, 5)
mainFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
mainFrame.Parent = PlayerInfoTab -- ここでタブに関連付け
Instance.new("UICorner", mainFrame)

-- ドラッグバー (タブ内なので一応残すが、固定も可能)
local dragBar = Instance.new("Frame")
dragBar.Size = UDim2.new(1, 0, 0, 50); dragBar.BackgroundColor3 = Color3.fromRGB(25, 25, 25); dragBar.Parent = mainFrame
local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(1, 0, 1, 0); titleLabel.BackgroundTransparency = 1; titleLabel.Text = "  PLAYER INTELLIGENCE (v3.5)"
titleLabel.TextColor3 = Color3.new(1,1,1); titleLabel.Font = Enum.Font.SourceSansBold; titleLabel.TextSize = 18; titleLabel.TextXAlignment = Enum.TextXAlignment.Left; titleLabel.Parent = dragBar

local scrollFrame = Instance.new("ScrollingFrame")
scrollFrame.Size = UDim2.new(1, -15, 1, -65); scrollFrame.Position = UDim2.new(0, 7, 0, 55); scrollFrame.BackgroundTransparency = 1
scrollFrame.CanvasSize = UDim2.new(0, 0, 0, 0); scrollFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y; scrollFrame.ScrollBarThickness = 5; scrollFrame.Parent = mainFrame
Instance.new("UIListLayout", scrollFrame).Padding = UDim.new(0, 8)

-- 以降、お前のロジックを100%継承
local function findSmartData(player, key)
    local attr = player:GetAttribute(key)
    if attr ~= nil then return tostring(attr) end
    local success, prop = pcall(function() return player[key] end)
    if success and prop ~= nil then return tostring(prop) end
    local folders = {player:FindFirstChild("Data"), player:FindFirstChild("leaderstats"), player}
    for _, f in pairs(folders) do
        if f then
            local obj = f:FindFirstChild(key)
            if obj and obj:IsA("ValueBase") then return tostring(obj.Value) end
        end
    end
    return "Not Found"
end

local function getDeepStat(player, statName)
    local d = player:FindFirstChild("Data")
    if d and d:FindFirstChild("Stats") then
        local cat = d.Stats:FindFirstChild(statName)
        if cat then
            local l = cat:FindFirstChild("Level")
            return l and tostring(l.Value) or (cat:IsA("ValueBase") and tostring(cat.Value) or "0")
        end
    end
    return "0"
end

local function getRaceVersion(player)
    local bp = player:FindFirstChild("Backpack")
    local ch = player.Character
    local function h(n) return (bp and bp:FindFirstChild(n)) or (ch and ch:FindFirstChild(n)) end
    if h("Awakening") then return "v4" end
    for _, s in ipairs(v3Skills) do if h(s) then return "v3" end end
    return "v1/v2"
end

local function createPlayerEntry(player)
    if scrollFrame:FindFirstChild(player.Name) then return end
    
    local container = Instance.new("Frame")
    container.Name = player.Name; container.Size = UDim2.new(1, -5, 0, 65); container.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    container.ClipsDescendants = true; container.Parent = scrollFrame; Instance.new("UICorner", container)

    local mainBtn = Instance.new("TextButton")
    mainBtn.Size = UDim2.new(1, 0, 0, 65); mainBtn.BackgroundTransparency = 1; mainBtn.Text = ""; mainBtn.Parent = container

    local img = Instance.new("ImageLabel")
    img.Size = UDim2.new(0, 55, 0, 55); img.Position = UDim2.new(0, 5, 0, 5); img.BackgroundTransparency = 1; img.Parent = mainBtn
    task.spawn(function() img.Image = Players:GetUserThumbnailAsync(player.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size100x100) end)

    local nameLabel = Instance.new("TextLabel")
    nameLabel.Size = UDim2.new(1, -70, 0, 25); nameLabel.Position = UDim2.new(0, 70, 0, 5); nameLabel.BackgroundTransparency = 1
    nameLabel.Text = player.DisplayName; nameLabel.Font = Enum.Font.SourceSansBold; nameLabel.TextSize = 16; nameLabel.TextXAlignment = Enum.TextXAlignment.Left; nameLabel.Parent = mainBtn

    local statsLabel = Instance.new("TextLabel")
    statsLabel.Size = UDim2.new(1, -70, 0, 20); statsLabel.Position = UDim2.new(0, 70, 0, 30); statsLabel.BackgroundTransparency = 1
    statsLabel.TextColor3 = Color3.fromRGB(0, 200, 255); statsLabel.TextSize = 13; statsLabel.TextXAlignment = Enum.TextXAlignment.Left; statsLabel.Font = Enum.Font.SourceSansBold; statsLabel.Parent = mainBtn

    local fragLabelShort = Instance.new("TextLabel")
    fragLabelShort.Size = UDim2.new(1, -70, 0, 15); fragLabelShort.Position = UDim2.new(0, 70, 0, 45); fragLabelShort.BackgroundTransparency = 1
    fragLabelShort.TextColor3 = Color3.fromRGB(255, 85, 255); fragLabelShort.TextSize = 12; fragLabelShort.TextXAlignment = Enum.TextXAlignment.Left; fragLabelShort.Font = Enum.Font.SourceSansBold; fragLabelShort.Parent = mainBtn

    local detailArea = Instance.new("ScrollingFrame")
    detailArea.Size = UDim2.new(1, -10, 0, 320); detailArea.Position = UDim2.new(0, 5, 0, 75); detailArea.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    detailArea.CanvasSize = UDim2.new(0,0,0,0); detailArea.AutomaticCanvasSize = Enum.AutomaticSize.Y; detailArea.ScrollBarThickness = 3; detailArea.Parent = container
    Instance.new("UICorner", detailArea)
    Instance.new("UIListLayout", detailArea).Padding = UDim.new(0, 5)

    local hpLabel = Instance.new("TextLabel")
    hpLabel.Size = UDim2.new(1, -20, 0, 25); hpLabel.BackgroundTransparency = 1; hpLabel.TextColor3 = Color3.fromRGB(0, 255, 150)
    hpLabel.Font = Enum.Font.SourceSansBold; hpLabel.TextSize = 16; hpLabel.TextXAlignment = Enum.TextXAlignment.Left; hpLabel.Parent = detailArea

    local infoText = Instance.new("TextLabel")
    infoText.Size = UDim2.new(1, -20, 0, 0); infoText.AutomaticSize = Enum.AutomaticSize.Y; infoText.BackgroundTransparency = 1
    infoText.TextColor3 = Color3.fromRGB(255, 255, 100); infoText.Font = Enum.Font.SourceSansItalic; infoText.TextSize = 14; infoText.TextXAlignment = Enum.TextXAlignment.Left; infoText.Parent = detailArea

    local statInfo = Instance.new("TextLabel")
    statInfo.Size = UDim2.new(1, -20, 0, 0); statInfo.AutomaticSize = Enum.AutomaticSize.Y; statInfo.BackgroundTransparency = 1
    statInfo.TextColor3 = Color3.fromRGB(255, 165, 0); statInfo.Font = Enum.Font.Code; statInfo.TextSize = 14; statInfo.TextXAlignment = Enum.TextXAlignment.Left; statInfo.Parent = detailArea

    local invText = Instance.new("TextLabel")
    invText.Size = UDim2.new(1, -20, 0, 0); invText.AutomaticSize = Enum.AutomaticSize.Y; invText.BackgroundTransparency = 1
    invText.TextColor3 = Color3.new(1, 1, 1); invText.TextSize = 14; invText.TextXAlignment = Enum.TextXAlignment.Left
    invText.TextWrapped = true; invText.Font = Enum.Font.SourceSans; invText.Parent = detailArea

    local isOpen = false
    mainBtn.MouseButton1Click:Connect(function()
        isOpen = not isOpen
        container:TweenSize(UDim2.new(1, -5, 0, isOpen and 400 or 65), "Out", "Quad", 0.2, true)
    end)

    task.spawn(function()
        while container.Parent do
            local teamName = player.Team and player.Team.Name or "None"
            if string.find(string.lower(teamName), "pirate") then nameLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
            elseif string.find(string.lower(teamName), "marine") then nameLabel.TextColor3 = Color3.fromRGB(50, 150, 255)
            else nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255) end

            local lv = findSmartData(player, "Level")
            local rc = findSmartData(player, "Race")
            local rv = getRaceVersion(player)
            local fr = findSmartData(player, "Fragments")
            
            statsLabel.Text = "Lv: "..lv.." | "..rc.." ("..rv..")"
            fragLabelShort.Text = "Fragments: "..fr

            if isOpen then
                local ch = player.Character
                hpLabel.Text = "  [ HEALTH ] : " .. ((ch and ch:FindFirstChild("Humanoid")) and math.floor(ch.Humanoid.Health) or "DEAD")
                
                local tag = findSmartData(player, "ChatTagText")
                local loc = findSmartData(player, "ExactLocation")
                infoText.Text = string.format("  タイトル: %s\n  現在の場所: %s", tag, loc)

                local def = getDeepStat(player, "Defense")
                local fru = getDeepStat(player, "Demon Fruit")
                local gun = getDeepStat(player, "Gun")
                local mel = getDeepStat(player, "Melee")
                local swd = getDeepStat(player, "Sword")
                statInfo.Text = string.format("  [ STATISTICS ]\n  Team: %s\n  Defense: %s\n  Fruit: %s\n  Gun: %s\n  Melee: %s\n  Sword: %s\n  Fragments: %s", teamName, def, fru, gun, mel, swd, fr)

                local itemList = {}
                local function collect(folder)
                    for _, i in pairs(folder:GetChildren()) do if i:IsA("Tool") and not ignoreList[i.Name] then table.insert(itemList, i.Name) end end
                end
                if ch then collect(ch) end
                collect(player.Backpack)
                table.sort(itemList)

                local displayLines = {"  [ INVENTORY ]"}
                for _, name in ipairs(itemList) do table.insert(displayLines, "  - " .. name) end
                invText.Text = #itemList > 0 and table.concat(displayLines, "\n") or "  [ INVENTORY ]\n  (Empty)"
            end
            task.wait(0.6)
        end
    end)
end

for _, p in pairs(Players:GetPlayers()) do createPlayerEntry(p) end
Players.PlayerAdded:Connect(createPlayerEntry)
Players.PlayerRemoving:Connect(function(p) if scrollFrame:FindFirstChild(p.Name) then scrollFrame[p.Name]:Destroy() end end)
---------------------------------
-- 🎨 VFXカラー タブ (リスポーン完全対応 & 0.2s爆速レインボー)
---------------------------------
local VFXTab = CreateTab("VFXカラー")

-- 内部変数
local selectedVFXName = nil -- オブジェクトではなく「名前」で覚える
local currentVFXObj = nil
local rainbowLoop = nil
local lastSelectedColor = Color3.new(1, 1, 1)
local isRainbowEnabled = false

-- --- 【コアロジック】仕組みはそのまま、より確実に ---
local function applyColor(targetColor)
    -- 現在有効なVFXオブジェクトがない場合は何もしない
    if not currentVFXObj then return end
    
    local shifted = currentVFXObj:FindFirstChild("Shifted")
    if shifted then
        -- 属性（Attributes）を更新
        for attrName, _ in pairs(shifted:GetAttributes()) do
            if string.find(attrName:lower(), "shifted_color") then 
                shifted:SetAttribute(attrName, targetColor) 
            end
        end
        -- 子要素（Color3Value）を更新
        for _, child in pairs(shifted:GetChildren()) do
            if string.find(child.Name:lower(), "shifted_color") and child:IsA("Color3Value") then 
                child.Value = targetColor 
            end
        end
    end
end

-- --- 【自動更新】VFXリスト作成 ---
local vfxListFrame = Instance.new("Frame", VFXTab)
vfxListFrame.Size = UDim2.new(1, 0, 0, 0)
vfxListFrame.AutomaticSize = Enum.AutomaticSize.Y
vfxListFrame.BackgroundTransparency = 1
vfxListFrame.LayoutOrder = -10

local listLayout = Instance.new("UIListLayout", vfxListFrame)
listLayout.Padding = UDim.new(0, 5)

local function refreshVFXList()
    for _, child in pairs(vfxListFrame:GetChildren()) do
        if not child:IsA("UIListLayout") then child:Destroy() end
    end
    
    for _, vfx in pairs(game.Players.LocalPlayer:GetChildren()) do
        if vfx.Name:find("VFXColor") then
            local vfxName = vfx.Name:gsub("VFXColor", "")
            CreateButton(vfxListFrame, "👉 選択: " .. vfxName, function()
                currentVFXObj = vfx
                selectedVFXName = vfx.Name -- 名前を保存しておく（重要！）
                Notify("Active: " .. vfxName)
                if not isRainbowEnabled then applyColor(lastSelectedColor) end
            end)
        end
    end
end

-- --- 【リスポーン対策】生き返ったら自動で再適用 ---
game.Players.LocalPlayer.CharacterAdded:Connect(function()
    task.wait(1.5) -- VFXが生成されるまで待機
    refreshVFXList()
    
    -- 死ぬ前に選んでいたVFXがあれば、新しい方を自動でターゲットにする
    if selectedVFXName then
        local newVfx = game.Players.LocalPlayer:FindFirstChild(selectedVFXName)
        if newVfx then
            currentVFXObj = newVfx
            if isRainbowEnabled then
                -- レインボー継続（ループは切れていないので自動で適用され続ける）
            else
                applyColor(lastSelectedColor)
            end
        end
    end
end)

-- アイテム増減監視
game.Players.LocalPlayer.ChildAdded:Connect(function(c) if c.Name:find("VFXColor") then task.wait(0.1); refreshVFXList() end end)
game.Players.LocalPlayer.ChildRemoved:Connect(function(c) if c.Name:find("VFXColor") then task.wait(0.1); refreshVFXList() end end)

-- --- 🌈 超高速レインボー (0.2秒周期) ---
CreateButton(VFXTab, "🔥 GAMING RAINBOW (0.2s)", function()
    if not currentVFXObj then Notify("先にVFXを選択してください"); return end
    
    isRainbowEnabled = not isRainbowEnabled
    if rainbowLoop then rainbowLoop:Disconnect(); rainbowLoop = nil end
    
    if isRainbowEnabled then
        rainbowLoop = game:GetService("RunService").Heartbeat:Connect(function()
            local speed = 0.2 -- ここを0.2に固定
            local h = (tick() % speed) / speed
            applyColor(Color3.fromHSV(h, 1, 1))
        end)
        Notify("Rainbow ON")
    else
        applyColor(lastSelectedColor)
        Notify("Rainbow OFF")
    end
end)

-- --- 🎨 被りなし！厳選カラーパレット ---
local palette = {
    {"Red", Color3.new(1,0,0)}, {"Orange", Color3.new(1,0.5,0)}, {"Yellow", Color3.new(1,1,0)},
    {"Lime", Color3.new(0.5,1,0)}, {"Green", Color3.new(0,1,0)}, {"Mint", Color3.new(0.4,1,0.6)},
    {"Aqua", Color3.new(0,1,0.8)}, {"Cyan", Color3.new(0,1,1)}, {"Sky", Color3.new(0.2,0.6,1)},
    {"Blue", Color3.new(0,0,1)}, {"Purple", Color3.new(0.6,0,1)}, {"Magenta", Color3.new(1,0,1)},
    {"Pink", Color3.new(1,0.4,0.7)}, {"White", Color3.new(1,1,1)}, {"Gold", Color3.new(1,0.8,0)},
    {"Crimson", Color3.new(0.6,0,0)}, {"Forest", Color3.new(0,0.4,0)}, {"Navy", Color3.new(0,0,0.5)},
    {"Silver", Color3.new(0.7,0.7,0.7)}, {"Ghost", Color3.new(0.9,0.9,1)}
}

for _, d in pairs(palette) do
    CreateButton(VFXTab, "Color: " .. d[1], function()
        isRainbowEnabled = false
        if rainbowLoop then rainbowLoop:Disconnect(); rainbowLoop = nil end
        lastSelectedColor = d[2]
        applyColor(d[2])
    end)
end

refreshVFXList()
---------------------------------
-- 🌊 Sea Beast / Sea Event (障害物すり抜け・全機能搭載版)
---------------------------------
local SeaTab = CreateTab("Sea Beast / Sea Event")

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local VirtualInputManager = game:GetService("VirtualInputManager")
local player = Players.LocalPlayer
local remote = ReplicatedStorage.Remotes.CommF_

-- 設定
local SETTINGS = {
    DefaultY = 200,
    SeaBeastY = 50, 
    ShipY = 20,
    MoveSpeed = 3.0,
    HuntTweenSpeed = 350,
    ReturnTweenSpeed = 250,
    BoatsPath = Workspace:WaitForChild("Boats"),
    SeaBeastsPath = Workspace:WaitForChild("SeaBeasts"),
    EnemiesPath = Workspace:WaitForChild("Enemies")
}

local State = {
    Hunting = false, Terror = false, Shark = false, 
    Piranha = false, Brigade = false, Fishman = false,
    Moving = false, Processing = false, LastSeat = nil,
    LastBoatName = "Sloop", 
    LastBoatType = false,
    FixedShopPos = nil,
    LastIsMarineModel = true
}

-- --- ターゲット有効判定 ---
local function isValid(m)
    if not (m and m.Parent) then return false end
    local hum = m:FindFirstChildOfClass("Humanoid")
    if hum then return hum.Health > 0 end
    local h = m:FindFirstChild("Health")
    if h and h:IsA("ValueBase") then return h.Value > 0 end
    return m:FindFirstChild("HumanoidRootPart") or m:FindFirstChildWhichIsA("BasePart", true)
end

-- --- 移動関数 ---
local function stableTween(targetPos, speed)
    local root = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    
    local connection
    connection = RunService.RenderStepped:Connect(function(dt)
        if not (root and root.Parent) then connection:Disconnect() return end
        local dist = (targetPos - root.Position).Magnitude
        if dist < 4 then 
            root.CFrame = CFrame.new(targetPos)
            connection:Disconnect() return 
        end
        root.CFrame = CFrame.new(root.Position + ((targetPos - root.Position).Unit * (speed * dt)))
        root.Velocity = Vector3.zero
    end)
    while connection.Connected do task.wait() end
end

-- --- 船購入 & 乗船 ---
local function buyAndRideBoat(boatName, isLuxury, isMarineModel)
    local dealerName = isLuxury and "Luxury Boat Dealer" or "Boat Dealer"
    local dealerModel = nil
    
    if State.FixedShopPos then
        stableTween(State.FixedShopPos + Vector3.new(0, 5, 0), 400)
        task.wait(0.5)
        for _, v in pairs(Workspace:GetDescendants()) do
            if v.Name == dealerName and v:IsA("Model") then
                if (v:GetModelCFrame().p - State.FixedShopPos).Magnitude < 30 then
                    dealerModel = v
                    break
                end
            end
        end
    end
    
    if not dealerModel then
        dealerModel = Workspace:FindFirstChild(dealerName, true)
    end
    
    if dealerModel then
        State.FixedShopPos = dealerModel:GetModelCFrame().p
        State.LastBoatName = boatName
        State.LastBoatType = isLuxury
        State.LastIsMarineModel = isMarineModel

        local root = player.Character:FindFirstChild("HumanoidRootPart")
        stableTween(State.FixedShopPos + Vector3.new(0, 5, 0), 350)
        task.wait(0.5)
        
        local team = (player.Team.Name == "Marines") and "Marine" or "Pirate"
        remote:InvokeServer("BuyBoat", "Speak")
        
        local names = {boatName}
        if isMarineModel then
            table.insert(names, team..boatName)
            table.insert(names, team.." "..boatName)
        end
        
        for _, n in ipairs(names) do
            if remote:InvokeServer("BuyBoat", n, dealerName) ~= 0 then
                task.wait(1.2)
                local targetBoat = nil
                local dist = 999
                for _, b in pairs(SETTINGS.BoatsPath:GetChildren()) do
                    local m = b:FindFirstChildWhichIsA("BasePart", true)
                    if m and (m.Position - root.Position).Magnitude < dist then
                        dist = (m.Position - root.Position).Magnitude; targetBoat = b
                    end
                end
                if targetBoat then 
                    local seat = targetBoat:FindFirstChildWhichIsA("VehicleSeat", true)
                    if seat then 
                        State.LastSeat = seat
                        stableTween(seat.Position + Vector3.new(0, 3, 0), 250)
                        task.wait(0.3)
                        pcall(function() seat:Sit(player.Character.Humanoid) end)
                    end
                end
                break
            end
        end
    end
end

-- --- 2キー装備 ---
local function forceEquipWeapon()
    local char = player.Character
    local hum = char and char:FindFirstChild("Humanoid")
    if hum then
        hum.Sit = false
        task.wait(0.3)
        for i = 1, 8 do
            if char:FindFirstChildOfClass("Tool") then break end
            VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.Two, false, game)
            task.wait(0.05)
            VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.Two, false, game)
            task.wait(0.1)
        end
    end
end

-- --- スキル連打 ---
local function spamSkills()
    for _, key in ipairs({Enum.KeyCode.Z, Enum.KeyCode.X, Enum.KeyCode.C, Enum.KeyCode.F}) do
        VirtualInputManager:SendKeyEvent(true, key, false, game)
        task.wait(0.01)
        VirtualInputManager:SendKeyEvent(false, key, false, game)
    end
end

-- --- ターゲット検索 ---
local function findNextTarget()
    local enm = SETTINGS.EnemiesPath:GetChildren()
    if State.Terror then for _,v in pairs(enm) do if v.Name == "Terrorshark" and isValid(v) then return v, true, SETTINGS.DefaultY end end end
    if State.Brigade then for _,v in pairs(enm) do if (v.Name:find("Brigade") or v.Name:find("Ship") or v.Name:find("Boat")) and isValid(v) then return v, false, SETTINGS.ShipY end end end
    if State.Fishman then for _,v in pairs(enm) do if (v.Name:find("Fish") or v.Name:find("Crew")) and isValid(v) then local isD = not v.Name:find("Boat") return v, isD, (isD and SETTINGS.DefaultY or SETTINGS.ShipY) end end end
    if State.Piranha then for _,v in pairs(enm) do if v.Name == "Piranha" and isValid(v) then return v, true, SETTINGS.DefaultY end end end
    if State.Shark then for _,v in pairs(enm) do if (v.Name:find("Shark") or v.Name == "Sharks") and v.Name ~= "Terrorshark" and isValid(v) then return v, true, SETTINGS.DefaultY end end end
    if State.Hunting then for _,v in pairs(SETTINGS.SeaBeastsPath:GetChildren()) do if isValid(v) then return v, false, SETTINGS.SeaBeastY end end end
    return nil
end

-- --- 狩りプロセス ---
local function startHunt()
    if State.Processing then return end
    local target, isDirect, currentY = findNextTarget()
    if not target then return end

    State.Processing = true
    forceEquipWeapon()

    while true do
        target, isDirect, currentY = findNextTarget()
        if not target then break end

        local root = player.Character:FindFirstChild("HumanoidRootPart")
        local trp = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChildWhichIsA("BasePart", true)

        if trp then
            stableTween(trp.Position + (isDirect and Vector3.new(0, 15, 0) or Vector3.new(0, currentY - trp.Position.Y, 0)), SETTINGS.HuntTweenSpeed)
            while isValid(target) and (State.Hunting or State.Terror or State.Shark or State.Piranha or State.Brigade or State.Fishman) do
                root.CFrame = isDirect and (trp.CFrame * CFrame.new(0, 20, 0)) or CFrame.new(trp.Position.X, currentY, trp.Position.Z)
                root.Velocity = Vector3.zero
                spamSkills()
                RunService.Heartbeat:Wait()
                if not (State.Hunting or State.Terror or State.Shark or State.Piranha or State.Brigade or State.Fishman) then break end
            end
        end
        task.wait(0.2)
    end
    
    if State.LastSeat and State.LastSeat.Parent then 
        stableTween(State.LastSeat.Position + Vector3.new(0, 3, 0), SETTINGS.ReturnTweenSpeed)
        task.wait(0.3)
        pcall(function() State.LastSeat:Sit(player.Character.Humanoid) end)
    end
    State.Processing = false
end

-- --- 死んだ時の自動復帰 ---
player.CharacterAdded:Connect(function(char)
    task.wait(4)
    if State.Moving and State.FixedShopPos then
        buyAndRideBoat(State.LastBoatName, State.LastBoatType, State.LastIsMarineModel)
    end
end)

-- --- UI ---
CreateSlider(SeaTab, "Hunt Speed", 100, 500, 350, function(v) SETTINGS.HuntTweenSpeed = v end)
CreateToggle(SeaTab, "AUTO MOVE (LOOP)", false, function(v) State.Moving = v end)
CreateToggle(SeaTab, "SEA BEAST", false, function(v) State.Hunting = v end)
CreateToggle(SeaTab, "TERROR SHARK", false, function(v) State.Terror = v end)
CreateToggle(SeaTab, "SHARKS", false, function(v) State.Shark = v end)
CreateToggle(SeaTab, "PIRANHA", false, function(v) State.Piranha = v end)
CreateToggle(SeaTab, "PIRATE SHIPS", false, function(v) State.Brigade = v end)
CreateToggle(SeaTab, "FISHMAN / BOAT", false, function(v) State.Fishman = v end)

CreateButton(SeaTab, "RETURN TO BOAT", function()
    if State.Processing then return end
    if State.LastSeat and State.LastSeat.Parent then
        task.spawn(function()
            State.Processing = true
            stableTween(State.LastSeat.Position + Vector3.new(0, 3, 0), SETTINGS.ReturnTweenSpeed)
            task.wait(0.3)
            pcall(function() State.LastSeat:Sit(player.Character.Humanoid) end)
            State.Processing = false
        end)
    end
end)

-- --- BOAT SHOP ---
do
    local boats = {
        {"Dinghy", false}, {"Sloop", true}, {"Brigade", true}, {"Grand Brigade", true},
        {"Miracle", false, true}, {"The Sentinel", false, true}, {"Guardian", false, true}, {"Lantern", false, true}, {"Sleigh", false, true}, {"Beast Hunter", false, true}
    }
    for _, info in ipairs(boats) do
        CreateButton(SeaTab, "Buy & Start: "..info[1], function()
            buyAndRideBoat(info[1], info[3] or false, info[2])
        end)
    end
end

-- --- 常駐ループ (Noclip機能追加) ---
RunService.Stepped:Connect(function()
    -- AUTO MOVE 中かつ船に乗っているなら当たり判定を消す
    if State.Moving and State.LastSeat and State.LastSeat.Parent then
        local boat = State.LastSeat.Parent
        -- プレイヤーの当たり判定オフ
        if player.Character then
            for _, part in pairs(player.Character:GetDescendants()) do
                if part:IsA("BasePart") then part.CanCollide = false end
            end
        end
        -- 船の当たり判定オフ
        for _, part in pairs(boat:GetDescendants()) do
            if part:IsA("BasePart") then part.CanCollide = false end
        end
    end
end)

RunService.Heartbeat:Connect(function()
    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChild("Humanoid")
    if not (root and hum) then return end

    if hum.SeatPart and hum.SeatPart:IsA("VehicleSeat") then
        State.LastSeat = hum.SeatPart
    end

    -- AUTO MOVE 移動
    if State.Moving and not State.Processing and State.LastSeat and hum.Sit then
        local fwd = Vector3.new(root.CFrame.LookVector.X, 0, root.CFrame.LookVector.Z).Unit
        root.CFrame = CFrame.new(root.Position + (fwd * SETTINGS.MoveSpeed), root.Position + (fwd * SETTINGS.MoveSpeed) + root.CFrame.LookVector)
        root.CFrame = CFrame.new(root.Position.X, SETTINGS.DefaultY, root.Position.Z) * (root.CFrame - root.CFrame.Position)
    end

    if not State.Processing and findNextTarget() then
        task.spawn(startHunt)
    end
end)
---------------------------------
-- 📍 Teleport Locations (Blox Fruits)
---------------------------------
-- テレポート関数
local function TP(pos)
    local char = game.Players.LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = CFrame.new(pos)
        Notify("Teleported to Location")
    end
end

-- お前のHUBの「CreateButton」命令を使ってタブに追加
CreateButton(BloxFruitsTab, "Mansion (マンション)", function()
    TP(Vector3.new(-12455.86, 376.34, -7565.61))
end)

CreateButton(BloxFruitsTab, "Hydra Island (ヒドラ)", function()
    TP(Vector3.new(5649.43, 1015.29, -341.05))
end)

CreateButton(BloxFruitsTab, "Sea Castle (海の城)", function()
    TP(Vector3.new(-5029.62, 315.97, -3195.88))
end)
---------------------------------
-- STATSタブ (リアルタイム更新版)
---------------------------------
local StatsTab = CreateTab("stats")

-- ラベル作成用関数
local function CreateStatLabel(text)
    local Label = Instance.new("TextLabel", StatsTab)
    Label.Size = UDim2.new(1, -10, 0, 35)
    Label.BackgroundColor3 = Color3.fromRGB(20, 0, 0)
    Label.TextColor3 = Color3.fromRGB(255, 255, 255)
    Label.Font = Enum.Font.SourceSansBold
    Label.TextSize = 15
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Instance.new("UICorner", Label)
    local Padding = Instance.new("UIPadding", Label)
    Padding.PaddingLeft = UDim.new(0, 10)
    return Label
end

local JobIdLabel = CreateStatLabel("Server ID: Loading...")
local PlayTimeLabel = CreateStatLabel("滞在時間: 00:00:00")
local FPSLabel = CreateStatLabel("FPS: 0")
local PingLabel = CreateStatLabel("Ping: 0 ms")

-- サーバーIDをコピーするボタン
CreateButton(StatsTab, "サーバーIDをコピー", function()
    setclipboard(game.JobId)
    Notify("サーバーIDをクリップボードにコピーしました")
end)

-- リアルタイム更新ロジック
local RunService = game:GetService("RunService")
local Stats = game:GetService("Stats")
local startTime = os.time()

task.spawn(function()
    JobIdLabel.Text = "Server ID: " .. (game.JobId ~= "" and game.JobId or "Studio/Private")
    
    while task.wait(0.5) do
        -- 滞在時間の計算
        local diff = os.time() - startTime
        local hours = math.floor(diff / 3600)
        local mins = math.floor((diff % 3600) / 60)
        local secs = diff % 60
        PlayTimeLabel.Text = string.format("滞在時間: %02d:%02d:%02d", hours, mins, secs)
        
        -- FPSの計算
        local fps = math.floor(1 / RunService.RenderStepped:Wait())
        FPSLabel.Text = "FPS: " .. fps
        
        -- Pingの取得
        local ping = math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValue())
        PingLabel.Text = "Ping: " .. ping .. " ms"
    end
end)
---------------------------------
-- ボス追跡機能 (究極低負荷・プロ版)
---------------------------------
local BossLabelTitle = CreateStatLabel("--- 出現中のボス ---")
BossLabelTitle.TextColor3 = Color3.fromRGB(255, 50, 0)

local BossScroll = Instance.new("ScrollingFrame", StatsTab)
BossScroll.Size = UDim2.new(1, -10, 0, 150)
BossScroll.BackgroundTransparency = 1; BossScroll.BorderSizePixel = 0
BossScroll.ScrollBarThickness = 3
BossScroll.CanvasSize = UDim2.new(0, 0, 0, 0)
BossScroll.AutomaticCanvasSize = Enum.AutomaticSize.Y

local BossListLabel = Instance.new("TextLabel", BossScroll)
BossListLabel.Size = UDim2.new(1, 0, 0, 0); BossListLabel.AutomaticSize = Enum.AutomaticSize.Y
BossListLabel.BackgroundTransparency = 1; BossListLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
BossListLabel.Font = Enum.Font.SourceSansBold; BossListLabel.TextSize = 15
BossListLabel.TextXAlignment = Enum.TextXAlignment.Left; BossListLabel.TextYAlignment = Enum.TextYAlignment.Top
BossListLabel.TextWrapped = true; BossListLabel.Text = "待機中..."

-- 内部データ
local targetBossNames = {} -- ボスの名前だけを記録
local currentBossStates = {} -- [名前] = "📍" または "⚔️"
local updatePending = false

-- 名前を一度だけ綺麗にする
local function Clean(name)
    return name:gsub("%[Lv%. %d+%]%s*", ""):gsub("%[Boss%]", ""):match("^%s*(.-)%s*$")
end

-- 表示更新（デバウンス付き：短時間に何度も走らせない）
local function SafeUpdateDisplay()
    if updatePending then return end
    updatePending = true
    
    task.delay(0.5, function() -- 0.5秒間隔でまとめて更新
        local displayList = {}
        for name, icon in pairs(currentBossStates) do
            table.insert(displayList, icon .. " " .. name)
        end
        
        local final = #displayList > 0 and table.concat(displayList, "\n") or "なし"
        if BossListLabel.Text ~= final then
            BossListLabel.Text = final
        end
        updatePending = false
    end)
end

local function SetupUltraWatch()
    local Spawns = game:GetService("Workspace"):FindFirstChild("_WorldOrigin")
    if Spawns then Spawns = Spawns:FindFirstChild("EnemySpawns") end
    local Enemies = game:GetService("Workspace"):FindFirstChild("Enemies")

    -- 1. スポーンフォルダの監視
    if Spawns then
        local function onAdd(child)
            if child.Name:find("%[Boss%]") then
                local name = Clean(child.Name)
                targetBossNames[name] = true
                currentBossStates[name] = "📍"
                SafeUpdateDisplay()
            end
        end
        Spawns.ChildAdded:Connect(onAdd)
        Spawns.ChildRemoved:Connect(function(child)
            local name = Clean(child.Name)
            if currentBossStates[name] == "📍" then
                currentBossStates[name] = nil
                SafeUpdateDisplay()
            end
        end)
        for _, v in pairs(Spawns:GetChildren()) do onAdd(v) end
    end

    -- 2. 戦闘フォルダの監視 (ここが一番重いので最適化)
    if Enemies then
        local function onAdd(child)
            -- 既にtargetBossNamesにある名前か、[Boss]タグがある場合のみ
            local name = Clean(child.Name)
            if targetBossNames[name] or child.Name:find("%[Boss%]") then
                targetBossNames[name] = true
                currentBossStates[name] = "⚔️"
                SafeUpdateDisplay()
            end
        end
        Enemies.ChildAdded:Connect(onAdd)
        Enemies.ChildRemoved:Connect(function(child)
            local name = Clean(child.Name)
            if currentBossStates[name] == "⚔️" then
                currentBossStates[name] = nil
                SafeUpdateDisplay()
            end
        end)
        for _, v in pairs(Enemies:GetChildren()) do onAdd(v) end
    end
end

task.spawn(SetupUltraWatch)
---------------------------------
-- 特殊島 出現チェック (⭕/❌)
---------------------------------
local IslandTitle = CreateStatLabel("--- 特殊島 出現状況 ---")
IslandTitle.TextColor3 = Color3.fromRGB(255, 50, 0)

-- 島ごとの表示ラベルを作成
local function CreateIslandLabel(name)
    local Label = CreateStatLabel(name .. " : ❌")
    Label.TextSize = 14
    return Label
end

local MirageLabel = CreateIslandLabel("Mirage Island")
local KitsuneLabel = CreateIslandLabel("Kitsune Island")
local FrozenLabel = CreateIslandLabel("Frozen Dimension")
local PrehistoricLabel = CreateIslandLabel("Prehistoric Island")

local targetIslands = {
    ["Mirage Island"] = MirageLabel,
    ["Kitsune Island"] = KitsuneLabel,
    ["Frozen Dimension"] = FrozenLabel,
    ["Prehistoric Island"] = PrehistoricLabel
}

task.spawn(function()
    local MapFolder = game:GetService("Workspace"):FindFirstChild("Map")
    
    while task.wait(3) do -- 島は頻繁に消えないから3秒おきで十分（超軽量）
        if not MapFolder then continue end
        
        -- 全ての表示を一旦 ❌ にリセット
        for name, label in pairs(targetIslands) do
            label.Text = name .. " : ❌"
            label.TextColor3 = Color3.fromRGB(150, 150, 150)
        end
        
        -- Mapの中をスキャンして見つけたら ⭕ に変更
        for _, obj in pairs(MapFolder:GetChildren()) do
            if targetIslands[obj.Name] then
                targetIslands[obj.Name].Text = obj.Name .. " : ⭕"
                targetIslands[obj.Name].TextColor3 = Color3.fromRGB(0, 255, 0) -- 出現時は緑に光らせる
            end
        end
    end
end)
---------------------------------
-- BloxFruitsタブへのFinal-Z統合
---------------------------------
-- 既存の変数設定
local HIDE_Y = -199996.48
local IsIsolating = false
local FakeCharacter = nil
local lastHealths = {}
local sZAttackActive = false
settings.FinalZ = settings.FinalZ or false

-- [[ 復帰・リセット関数 ]]
local function FinalZ_Reset()
    IsIsolating = false
    workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
    if player.Character and player.Character:FindFirstChild("Humanoid") then
        workspace.CurrentCamera.CameraSubject = player.Character.Humanoid
    end
    if FakeCharacter then
        local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
        if hrp and player.Character.Humanoid.Health > 0 then 
            hrp.CFrame = FakeCharacter.HumanoidRootPart.CFrame 
        end
        FakeCharacter:Destroy()
        FakeCharacter = nil
    end
end

-- [[ クローン生成 & 本体隠蔽ロジック ]]
local function StartCloneTP(targetPos)
    if IsIsolating or not settings.FinalZ then return end
    IsIsolating = true
    local char = player.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    char.Archivable = true
    FakeCharacter = char:Clone()
    FakeCharacter.Parent = workspace
    for _, v in ipairs(FakeCharacter:GetDescendants()) do 
        if v:IsA("BasePart") then v.CanCollide = false end 
    end
    FakeCharacter.HumanoidRootPart.CFrame = CFrame.new(targetPos + Vector3.new(0, 3, 0))
    workspace.CurrentCamera.CameraSubject = FakeCharacter.Humanoid

    task.spawn(function()
        local startTime = tick()
        while IsIsolating and settings.FinalZ do
            if (tick() - startTime > 1.5) or (char.Humanoid.Health <= 0) then break end
            hrp.CFrame = CFrame.new(hrp.Position.X, HIDE_Y, hrp.Position.Z)
            hrp.AssemblyLinearVelocity = Vector3.zero
            game:GetService("RunService").Heartbeat:Wait()
        end
        FinalZ_Reset()
    end)
end

-- [[ 命中検知ロジック ]]
local function SetupFinalZ(char)
    local hum = char:WaitForChild("Humanoid")
    char.ChildAdded:Connect(function(child)
        if child.Name == "TridentGrabZ" and settings.FinalZ then
            task.wait()
            local val = child.Value
            local pos = (typeof(val) == "Vector3") and val or (val:IsA("Model") and val:FindFirstChild("HumanoidRootPart") and val.HumanoidRootPart.Position)
            if pos then StartCloneTP(pos) end
        end
    end)
    hum.AnimationPlayed:Connect(function(animTrack)
        if animTrack.Name == "Saddi_Z_Attack" then
            sZAttackActive = true
            task.delay(1.0, function() sZAttackActive = false end)
        end
    end)
    game:GetService("RunService").Heartbeat:Connect(function()
        if not sZAttackActive or not settings.FinalZ then return end
        local myRoot = char:FindFirstChild("HumanoidRootPart")
        if not myRoot then return end
        for _, p in ipairs(game.Players:GetPlayers()) do
            if p ~= player and p.Character then
                local eHum = p.Character:FindFirstChildOfClass("Humanoid")
                local eRoot = p.Character:FindFirstChild("HumanoidRootPart")
                if eHum and eRoot and eHum.Health > 0 then
                    if lastHealths[p] and eHum.Health < lastHealths[p] then
                        if (eRoot.Position - myRoot.Position).Magnitude < 120 then
                            sZAttackActive = false 
                            StartCloneTP(eRoot.Position)
                        end
                    end
                    lastHealths[p] = eHum.Health
                end
            end
        end
    end)
end

-- 既存のキャラクタースポーン時にも適用
if player.Character then SetupFinalZ(player.Character) end
player.CharacterAdded:Connect(SetupFinalZ)

-- 既存の「BloxFruitsTab」にトグルを追加 
if BloxFruitsTab then
    CreateToggle(BloxFruitsTab, "Final-Z (Trident/Saddi)", settings.FinalZ, function(v)
        settings.FinalZ = v
        if not v then FinalZ_Reset() end
    end)
end
---------------------------------
-- BloxFruitsタブ：Void Protection (奈落死防止)
---------------------------------

-- 設定初期化
settings.VoidProtection = false

-- 奈落死防止の実行関数
local function SetVoidProtection(state)
    if state then
        -- ON: FallenPartsDestroyHeightを無効化（0/0 = nan）
        workspace.FallenPartsDestroyHeight = 0/0
    else
        -- OFF: デフォルトの値（だいたい-500）に戻す
        workspace.FallenPartsDestroyHeight = -500
    end
end

-- サーバー移動やリセット時にも設定を維持するための監視
task.spawn(function()
    while true do
        task.wait(2)
        -- settingsの状態と実際のゲーム設定がズレていたら強制上書き
        if settings.VoidProtection then
            if workspace.FallenPartsDestroyHeight == workspace.FallenPartsDestroyHeight then -- NaN判定
                workspace.FallenPartsDestroyHeight = 0/0
            end
        end
    end
end)

-- 既存の「BloxFruitsTab」にトグルを追加
if BloxFruitsTab then
    CreateToggle(BloxFruitsTab, "奈落無効", settings.VoidProtection, function(v)
        settings.VoidProtection = v
        SetVoidProtection(v)
        if v then
            Notify("Void Protection: ON")
        else
            Notify("Void Protection: OFF")
        end
    end)
end
---------------------------------
-- BloxFruitsタブ：Team Changer統合
---------------------------------

-- チーム変更実行関数
local function ChangeTeam(teamName)
    local Remotes = game:GetService("ReplicatedStorage"):WaitForChild("Remotes")
    local targets = {"CommF", "CommF_", "CommE"}
    for _, name in pairs(targets) do
        local remote = Remotes:FindFirstChild(name)
        if remote then
            pcall(function()
                if remote:IsA("RemoteFunction") then
                    remote:InvokeServer("SetTeam", teamName)
                else
                    remote:FireServer("SetTeam", teamName)
                end
            end)
        end
    end
    Notify("Team changed to: " .. teamName)
end

-- 既存の「BloxFruitsTab」にボタンを追加
if BloxFruitsTab then
    -- タイトル的なラベル（区切り用）
    local TeamLabel = Instance.new("TextLabel", BloxFruitsTab)
    TeamLabel.Size = UDim2.new(1, -10, 0, 30)
    TeamLabel.BackgroundTransparency = 1
    TeamLabel.Text = "--- Team Changer ---"
    TeamLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    TeamLabel.Font = Enum.Font.SourceSansBold
    TeamLabel.TextSize = 16

    -- Pirateボタン
    CreateButton(BloxFruitsTab, "Pirate", function()
        ChangeTeam("Pirates")
    end)

    -- Marineボタン
    CreateButton(BloxFruitsTab, "Marine", function()
        ChangeTeam("Marines")
    end)
end
---------------------------------
-- BloxFruitsタブ：gun m1 (モーション強制ループ版)
---------------------------------
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")

-- リモート
local Net = ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net")
local RE_Attack = Net["RE/RegisterAttack"]
local RE_Hit = Net["RE/RegisterHit"]
local RE_Gun = Net["RE/ShootGunEvent"]

-- お前のSettingsをHUBのsettingsに統合
settings.GunM1 = false
local attackDist = 160
local multiHit = 2

-- [[ ターゲットサーチ ]]
local function GetTarget()
    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if not root then return nil end
    local nearest, minDist = nil, attackDist
    for _, folder in pairs({workspace:FindFirstChild("Enemies"), workspace:FindFirstChild("Characters")}) do
        if folder then
            for _, v in pairs(folder:GetChildren()) do
                if v ~= char and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
                    local p = v:FindFirstChild("HumanoidRootPart")
                    if p and (root.Position - p.Position).Magnitude < minDist then
                        minDist = (root.Position - p.Position).Magnitude
                        nearest = {v, p}
                    end
                end
            end
        end
    end
    return nearest
end

-- [[ 自動モーション・攻撃メイン ]]
RunService.Stepped:Connect(function()
    if not settings.GunM1 then return end
    
    local char = player.Character
    local tool = char and char:FindFirstChildOfClass("Tool")
    if not tool then return end

    local target = GetTarget()
    if not target then return end
    local targetChar, targetPart = target[1], target[2]

    pcall(function()
        -- 1. 共通ダメージ判定（実のファスト攻撃）
        for i = 1, multiHit do
            RE_Attack:FireServer(0)
            RE_Hit:FireServer(targetPart, {{targetChar, targetPart}})
        end

        -- 2. 銃(Gun)の処理
        if tool.ToolTip == "Gun" then
            -- 射撃リモート
            RE_Gun:FireServer(targetPart.Position, {targetPart})
            
            -- 【重要】実際にツールを「使った」ことにする (これでモーションが出る)
            tool:Activate() 
            
            -- 【強力】ツールの内部スクリプトを無視して、クリック信号を直接リモートに叩き込む
            local remote = tool:FindFirstChild("RemoteEvent") or tool:FindFirstChildOfClass("RemoteEvent")
            if remote then
                remote:FireServer("Down", targetPart.Position)
                -- Upを送らずDownを連打することで、撃ちっぱなしの状態を作る
            end

            -- 【モーション強制】アニメーションが止まらないように再生し続ける
            local hum = char:FindFirstChildOfClass("Humanoid")
            if hum then
                for _, track in pairs(hum:GetPlayingAnimationTracks()) do
                    -- 銃関連のアニメーションを高速再生させる
                    if track.Name:lower():find("gun") or track.Name:lower():find("shoot") then
                        track:AdjustSpeed(2) -- 2倍速で出し続ける
                    end
                end
            end
        end

        -- 3. 実(Fruit)のクリック
        if tool:FindFirstChild("LeftClickRemote") then
            tool.LeftClickRemote:FireServer((targetPart.Position - char:GetPivot().Position).Unit, 1)
        end
    end)
end)

-- 既存の「BloxFruitsTab」にトグルを追加
if BloxFruitsTab then
    CreateToggle(BloxFruitsTab, "gun m1", settings.GunM1, function(v)
        settings.GunM1 = v
        if v then
            Notify("gun m1 有効")
        else
            Notify("gun m1 無効")
        end
    end)
end
---------------------------------
-- BloxFruitsタブ：大仏の範囲でかくなる (Hitbox)
---------------------------------
local runService = game:GetService("RunService")
local superScale = 700
local armParts = {"LeftUpperArm", "LeftLowerArm", "LeftHand", "RightUpperArm", "RightLowerArm", "RightHand"}

-- 設定初期化
settings.BigBuddha = false

-- --- 腕の巨大化処理 ---
local function updateHitbox()
    if not settings.BigBuddha then return end
    
    local char = player.Character
    if not char then return end
    
    for _, name in pairs(armParts) do
        local part = char:FindFirstChild(name)
        if part and part:IsA("BasePart") then
            part.CanQuery = false 
            -- メッシュで見た目だけ巨大化させる
            local m = part:FindFirstChildOfClass("SpecialMesh") or Instance.new("SpecialMesh", part)
            m.MeshType = Enum.MeshType.Brick
            m.Scale = Vector3.new(superScale * 0.1, superScale, superScale * 0.1)
            
            part.Color = Color3.new(1, 1, 1)
            part.Material = Enum.Material.Neon
            part.Transparency = 0.5 -- 少し透明にして見やすくしたぜ
            
            -- 関節の位置調整（これが範囲拡大の肝だな）
            local motor = part:FindFirstChildOfClass("Motor6D")
            if motor then
                motor.C0 = CFrame.new(motor.C0.Position.X, 0, motor.C0.Position.Z) * CFrame.new(0, superScale / 2, 0)
            end
        end
    end
    -- 視界を広くする
    player.CameraMaxZoomDistance = 140
end

-- 毎フレーム実行
runService.RenderStepped:Connect(function()
    if settings.BigBuddha then
        pcall(updateHitbox)
    end
end)

-- 既存の「BloxFruitsTab」にトグルを追加
if BloxFruitsTab then
    CreateToggle(BloxFruitsTab, "大仏の範囲でかくなる", settings.BigBuddha, function(v)
        settings.BigBuddha = v
        if not v then
            -- 無効時に戻す処理（リセットは再スポーン推奨だが、一応カメラだけ戻す）
            player.CameraMaxZoomDistance = 128
            Notify("大仏範囲: OFF (戻すには再スポーン推奨)")
        else
            Notify("大仏範囲: ON ")
        end
    end)
end
