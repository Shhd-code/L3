-- ╔══════════════════════════════════════╗
-- ║          L3 TEAM SCRIPT HUB         ║
-- ║    Clean Rewrite - Fixed Remotes    ║
-- ╚══════════════════════════════════════╝

-- ══════════════ SERVICES ══════════════
local TweenService  = game:GetService("TweenService")
local Players       = game:GetService("Players")
local RunService    = game:GetService("RunService")

local LocalPlayer   = Players.LocalPlayer
local PlayerGui     = LocalPlayer.PlayerGui

-- ══════════════ REMOTES ══════════════
local RS            = game:GetService("ReplicatedStorage")
local ChatRemote    = RS:WaitForChild("RemoteEvents"):WaitForChild("ChatEvent")
local AdminRemote   = RS:WaitForChild("HDAdminHDClient"):WaitForChild("Signals"):WaitForChild("RequestCommandModification")

-- ══════════════ SEND COMMAND HELPER ══════════════
local function sendCommand(cmd)
    local args = { [1] = cmd }
    pcall(function() ChatRemote:FireServer(unpack(args)) end)
    pcall(function() AdminRemote:InvokeServer(unpack(args)) end)
end

-- ══════════════ CLEANUP ══════════════
for _, g in ipairs({ "L3_TEAM_GUI", "L3_FINAL_V4" }) do
    local old = PlayerGui:FindFirstChild(g)
    if old then old:Destroy() end
end

-- ══════════════ HELPER: CLOSE BUTTON ══════════════
local function addCloseButton(parent, onClose)
    local btn = Instance.new("TextButton")
    btn.Size     = UDim2.new(0, 30, 0, 30)
    btn.Position = UDim2.new(1, -35, 0, 5)
    btn.Text     = "✕"
    btn.TextSize = 16
    btn.Font     = Enum.Font.GothamBold
    btn.BackgroundColor3 = Color3.fromRGB(200, 30, 30)
    btn.TextColor3       = Color3.new(1, 1, 1)
    btn.BorderSizePixel  = 0
    btn.ZIndex           = 10
    btn.Parent           = parent
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
    btn.MouseButton1Click:Connect(function()
        if onClose then onClose() else parent:Destroy() end
    end)
    return btn
end

-- ══════════════ GUI 1: MAIN PANEL ══════════════
local MainGui = Instance.new("ScreenGui")
MainGui.Name         = "L3_TEAM_GUI"
MainGui.ResetOnSpawn = false
MainGui.Parent       = PlayerGui

-- Blur effect
local Blur = Instance.new("BlurEffect", game.Lighting)
Blur.Size = 0

-- Toggle button
local ToggleBtn = Instance.new("TextButton", MainGui)
ToggleBtn.Size             = UDim2.new(0, 65, 0, 65)
ToggleBtn.Position         = UDim2.new(0, 10, 0, 10)
ToggleBtn.Text             = "L3"
ToggleBtn.Font             = Enum.Font.GothamBold
ToggleBtn.TextSize         = 25
ToggleBtn.TextColor3       = Color3.fromRGB(0, 200, 255)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(0, 50, 150)
ToggleBtn.Active           = true
ToggleBtn.Draggable        = true
ToggleBtn.BorderSizePixel  = 0
Instance.new("UICorner", ToggleBtn).CornerRadius = UDim.new(0, 12)
local ToggleStroke = Instance.new("UIStroke", ToggleBtn)
ToggleStroke.Thickness = 2
ToggleStroke.Color     = Color3.fromRGB(0, 150, 255)

-- Main Frame
local MainFrame = Instance.new("Frame", MainGui)
MainFrame.Size             = UDim2.new(0, 380, 0, 300)
MainFrame.Position         = UDim2.new(0.5, -190, 0.5, -150)
MainFrame.BackgroundColor3 = Color3.fromRGB(0, 30, 100)
MainFrame.Visible          = false
MainFrame.Active           = true
MainFrame.Draggable        = true
MainFrame.BorderSizePixel  = 0
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 10)
local MainStroke = Instance.new("UIStroke", MainFrame)
MainStroke.Color     = Color3.fromRGB(0, 150, 255)
MainStroke.Thickness = 2

-- Background decorative images
local BgTop = Instance.new("ImageLabel", MainFrame)
BgTop.Size                = UDim2.new(1, 0, 1, 0)
BgTop.Image               = "rbxassetid://11942020306"
BgTop.BackgroundTransparency = 1
BgTop.ImageTransparency   = 0.3

local BgAnim = Instance.new("ImageLabel", MainFrame)
BgAnim.Size               = UDim2.new(1, 0, 1, 0)
BgAnim.Image              = "rbxassetid://8992230677"
BgAnim.BackgroundTransparency = 1
BgAnim.ImageTransparency  = 0.5

-- Title label
local TitleLabel = Instance.new("TextLabel", MainFrame)
TitleLabel.Size               = UDim2.new(1, 0, 0, 30)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text               = "made by L3 TEAM"
TitleLabel.TextColor3         = Color3.fromRGB(0, 200, 255)
TitleLabel.TextScaled         = true
TitleLabel.Font               = Enum.Font.GothamBold

-- Player name search box
local NameBox = Instance.new("TextBox", MainFrame)
NameBox.Size              = UDim2.new(0, 300, 0, 40)
NameBox.Position          = UDim2.new(0, 40, 0, 60)
NameBox.PlaceholderText   = "اكتب اول حروف الاسم"
NameBox.BackgroundColor3  = Color3.fromRGB(0, 40, 120)
NameBox.TextColor3        = Color3.fromRGB(255, 255, 255)
NameBox.BorderSizePixel   = 0
Instance.new("UICorner", NameBox).CornerRadius = UDim.new(0, 8)

-- Helper: create a main panel button
local function makeButton(text, posY)
    local btn = Instance.new("TextButton", MainFrame)
    btn.Size             = UDim2.new(0, 300, 0, 40)
    btn.Position         = UDim2.new(0, 40, 0, posY)
    btn.Text             = text
    btn.Font             = Enum.Font.GothamBold
    btn.TextSize         = 16
    btn.TextColor3       = Color3.fromRGB(0, 255, 255)
    btn.BackgroundColor3 = Color3.fromRGB(0, 60, 150)
    btn.BorderSizePixel  = 0
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    local s = Instance.new("UIStroke", btn)
    s.Color = Color3.fromRGB(0, 200, 255)
    btn.MouseEnter:Connect(function()  btn.BackgroundColor3 = Color3.fromRGB(0, 80, 200) end)
    btn.MouseLeave:Connect(function()  btn.BackgroundColor3 = Color3.fromRGB(0, 60, 150) end)
    return btn
end

local SpamBtn    = makeButton("تشغيل سبام",   120)
local CustomBtn  = makeButton("اوامر مخصصة", 170)
local SettingsBtn = makeButton("⚙️ إعدادات",  220)

-- ══════════════ ANIMATE PANEL IN ══════════════
local function showPanel(frame)
    frame.Visible  = true
    frame.Position = UDim2.new(0.5, -190, 1, 0)
    TweenService:Create(frame, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
        Position = UDim2.new(0.5, -190, 0.5, -150)
    }):Play()
end

-- ══════════════ TOGGLE MAIN PANEL ══════════════
ToggleBtn.MouseButton1Click:Connect(function()
    local visible = not MainFrame.Visible
    MainFrame.Visible = visible
    Blur.Size = visible and 15 or 0
    if visible then showPanel(MainFrame) end
end)

-- ══════════════ SPAM LOGIC ══════════════
local spamActive    = false
local cmd1          = "+RE"
local cmd2          = "+LOGS"
local targetName    = nil

local function searchPlayers()
    local results = {}
    local query   = NameBox.Text:lower()
    for _, p in ipairs(Players:GetPlayers()) do
        if p.Name:lower():sub(1, #query) == query then
            table.insert(results, p)
        end
    end
    return results
end

-- Player list panel (when multiple players match)
local function showPlayerList(playerList)
    local listFrame = Instance.new("Frame", MainGui)
    listFrame.Size             = UDim2.new(0, 300, 0, 250)
    listFrame.Position         = UDim2.new(0.5, -150, 1, 0)
    listFrame.BackgroundColor3 = Color3.fromRGB(0, 30, 100)
    listFrame.Active           = true
    listFrame.Draggable        = true
    listFrame.BorderSizePixel  = 0
    Instance.new("UICorner", listFrame).CornerRadius = UDim.new(0, 10)

    addCloseButton(listFrame)

    showPanel(listFrame)

    local yOffset = 40
    for _, player in ipairs(playerList) do
        local playerBtn = Instance.new("TextButton", listFrame)
        playerBtn.Size             = UDim2.new(0, 240, 0, 50)
        playerBtn.Position         = UDim2.new(0, 20, 0, yOffset)
        playerBtn.BackgroundColor3 = Color3.fromRGB(0, 50, 150)
        playerBtn.BorderSizePixel  = 0
        Instance.new("UICorner", playerBtn).CornerRadius = UDim.new(0, 8)

        playerBtn.Text      = player.DisplayName .. " (" .. player.Name .. ")"
        playerBtn.TextColor3 = Color3.fromRGB(0, 255, 255)
        Instance.new("UICorner", playerBtn)

        local avatar = Instance.new("ImageLabel", playerBtn)
        avatar.Size     = UDim2.new(0, 40, 0, 40)
        avatar.Position = UDim2.new(0, 5, 0, 5)
        avatar.BackgroundTransparency = 1
        avatar.Image    = "rbxthumb://type=AvatarHeadShot&id=" .. player.UserId .. "&w=150&h=150"
        Instance.new("UICorner", avatar).CornerRadius = UDim.new(1, 0)

        playerBtn.MouseButton1Click:Connect(function()
            targetName = player.Name:upper()
            spamActive = true
            SpamBtn.Text = "ايقاف سبام"
            listFrame:Destroy()
            task.spawn(function()
                while spamActive do
                    local command = (cmd1 .. " " .. targetName .. " " .. cmd2 .. " " .. targetName .. " "):rep(30)
                    sendCommand(command)
                    task.wait(0.2)
                end
            end)
        end)

        yOffset = yOffset + 60
    end
end

-- Spam button logic
SpamBtn.MouseButton1Click:Connect(function()
    if spamActive then
        spamActive = false
        SpamBtn.Text = "تشغيل سبام"
        return
    end

    local found = searchPlayers()
    if #found == 0 then return end

    if #found > 1 then
        showPlayerList(found)
    else
        targetName = found[1].Name:upper()
        spamActive = true
        SpamBtn.Text = "ايقاف سبام"
        task.spawn(function()
            while spamActive do
                local command = (cmd1 .. " " .. targetName .. " " .. cmd2 .. " " .. targetName .. " "):rep(30)
                sendCommand(command)
                task.wait(0.2)
            end
        end)
    end
end)

-- ══════════════ CUSTOM COMMANDS PANEL ══════════════
CustomBtn.MouseButton1Click:Connect(function()
    -- Remove existing if open
    local existing = MainGui:FindFirstChild("CustomCmdsPanel")
    if existing then existing:Destroy() return end

    local panel = Instance.new("Frame", MainGui)
    panel.Name             = "CustomCmdsPanel"
    panel.Size             = UDim2.new(0, 260, 0, 190)
    panel.Position         = UDim2.new(0.5, -130, 0.5, -95)
    panel.BackgroundColor3 = Color3.fromRGB(0, 30, 100)
    panel.Active           = true
    panel.Draggable        = true
    panel.BorderSizePixel  = 0
    Instance.new("UICorner", panel).CornerRadius = UDim.new(0, 10)
    local ps = Instance.new("UIStroke", panel)
    ps.Color = Color3.fromRGB(0, 150, 255)
    ps.Thickness = 2

    -- Close button
    addCloseButton(panel)

    -- Header
    local header = Instance.new("TextLabel", panel)
    header.Size               = UDim2.new(1, 0, 0, 30)
    header.Position           = UDim2.new(0, 0, 0, 5)
    header.BackgroundTransparency = 1
    header.Text               = "اوامر مخصصة"
    header.TextColor3         = Color3.fromRGB(0, 200, 255)
    header.Font               = Enum.Font.GothamBold
    header.TextSize           = 15

    -- Command 1 input
    local lbl1 = Instance.new("TextLabel", panel)
    lbl1.Size               = UDim2.new(0, 80, 0, 25)
    lbl1.Position           = UDim2.new(0, 10, 0, 45)
    lbl1.BackgroundTransparency = 1
    lbl1.Text               = "أمر 1:"
    lbl1.TextColor3         = Color3.fromRGB(200, 200, 255)
    lbl1.Font               = Enum.Font.Gotham
    lbl1.TextSize           = 14

    local box1 = Instance.new("TextBox", panel)
    box1.Size            = UDim2.new(0, 150, 0, 30)
    box1.Position        = UDim2.new(0, 20, 0, 70)
    box1.PlaceholderText = "Command 1  (حالياً: " .. cmd1 .. ")"
    box1.BackgroundColor3 = Color3.fromRGB(0, 40, 120)
    box1.TextColor3      = Color3.fromRGB(0, 200, 255)
    box1.BorderSizePixel = 0
    box1.ClearTextOnFocus = false
    Instance.new("UICorner", box1).CornerRadius = UDim.new(0, 6)

    -- Command 2 input
    local lbl2 = Instance.new("TextLabel", panel)
    lbl2.Size               = UDim2.new(0, 80, 0, 25)
    lbl2.Position           = UDim2.new(0, 10, 0, 110)
    lbl2.BackgroundTransparency = 1
    lbl2.Text               = "أمر 2:"
    lbl2.TextColor3         = Color3.fromRGB(200, 200, 255)
    lbl2.Font               = Enum.Font.Gotham
    lbl2.TextSize           = 14

    local box2 = Instance.new("TextBox", panel)
    box2.Size            = UDim2.new(0, 150, 0, 30)
    box2.Position        = UDim2.new(0, 20, 0, 135)
    box2.PlaceholderText = "Command 2  (حالياً: " .. cmd2 .. ")"
    box2.BackgroundColor3 = Color3.fromRGB(0, 40, 120)
    box2.TextColor3      = Color3.fromRGB(0, 200, 255)
    box2.BorderSizePixel = 0
    box2.ClearTextOnFocus = false
    Instance.new("UICorner", box2).CornerRadius = UDim.new(0, 6)

    -- Save button
    local saveBtn = Instance.new("TextButton", panel)
    saveBtn.Size             = UDim2.new(0, 60, 0, 25)
    saveBtn.Position         = UDim2.new(1, -75, 1, -35)
    saveBtn.Text             = "حفظ +"
    saveBtn.Font             = Enum.Font.GothamBold
    saveBtn.TextSize         = 13
    saveBtn.TextColor3       = Color3.fromRGB(255, 255, 255)
    saveBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
    saveBtn.BorderSizePixel  = 0
    Instance.new("UICorner", saveBtn).CornerRadius = UDim.new(0, 6)

    saveBtn.MouseButton1Click:Connect(function()
        if box1.Text ~= "" then cmd1 = box1.Text end
        if box2.Text ~= "" then cmd2 = box2.Text end
        panel:Destroy()
    end)

    showPanel(panel)
end)

-- ══════════════ SETTINGS PANEL ══════════════
local settings = { CopyProtection = false, Sky = false, SpinButton = false }
local hdAdminBackup = nil

local function makeToggle(parent, label, yPos, callback)
    local state = false
    local btn = Instance.new("TextButton", parent)
    btn.Size             = UDim2.new(0, 220, 0, 35)
    btn.Position         = UDim2.new(0, 20, 0, yPos)
    btn.Text             = label .. "  OFF"
    btn.Font             = Enum.Font.GothamBold
    btn.TextSize         = 14
    btn.TextColor3       = Color3.fromRGB(0, 255, 255)
    btn.BackgroundColor3 = Color3.fromRGB(0, 60, 150)
    btn.BorderSizePixel  = 0
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)

    btn.MouseButton1Click:Connect(function()
        state = not state
        btn.Text = label .. "  " .. (state and "ON" or "OFF")
        callback(state)
    end)
    return btn
end

SettingsBtn.MouseButton1Click:Connect(function()
    local existing = MainGui:FindFirstChild("SettingsPanel")
    if existing then existing:Destroy() return end

    local panel = Instance.new("Frame", MainGui)
    panel.Name             = "SettingsPanel"
    panel.Size             = UDim2.new(0, 260, 0, 200)
    panel.Position         = UDim2.new(0.5, -130, 0.5, -100)
    panel.BackgroundColor3 = Color3.fromRGB(0, 30, 100)
    panel.Active           = true
    panel.Draggable        = true
    panel.BorderSizePixel  = 0
    Instance.new("UICorner", panel).CornerRadius = UDim.new(0, 10)
    local ps = Instance.new("UIStroke", panel)
    ps.Color = Color3.fromRGB(0, 150, 255)
    ps.Thickness = 2

    -- Close button
    addCloseButton(panel)

    -- Header
    local header = Instance.new("TextLabel", panel)
    header.Size               = UDim2.new(1, 0, 0, 30)
    header.Position           = UDim2.new(0, 0, 0, 5)
    header.BackgroundTransparency = 1
    header.Text               = "⚙️ إعدادات"
    header.TextColor3         = Color3.fromRGB(0, 200, 255)
    header.Font               = Enum.Font.GothamBold
    header.TextSize           = 15

    makeToggle(panel, "حماية النسخ", 45, function(on)
        settings.CopyProtection = on
        if on then
            local hdGui = PlayerGui:FindFirstChild("HDAdminInterface")
            if hdGui then
                hdAdminBackup = hdGui:Clone()
                hdGui:Destroy()
            end
        else
            if hdAdminBackup then
                hdAdminBackup.Parent = PlayerGui
            end
        end
    end)

    makeToggle(panel, "Sky خارق", 90, function(on)
        settings.Sky = on
        if on then
            Instance.new("Sky", game.Lighting)
        else
            local sky = game.Lighting:FindFirstChildOfClass("Sky")
            if sky then sky:Destroy() end
        end
    end)

    makeToggle(panel, "Spin Button", 135, function(on)
        settings.SpinButton = on
    end)

    showPanel(panel)
end)

-- ══════════════ ANIMATED BACKGROUND ══════════════
task.spawn(function()
    while true do
        MainStroke.Color = Color3.fromRGB(0, 150, 255)
        BgAnim.Position = UDim2.new(
            0, math.sin(tick() / 5) * 10,
            0, math.cos(tick() / 5) * 10
        )
        task.wait()
    end
end)

-- ══════════════ GUI 2: SUCTION (TURBO) PANEL ══════════════
local SecondGui = Instance.new("ScreenGui", PlayerGui)
SecondGui.Name         = "L3_FINAL_V4"
SecondGui.ResetOnSpawn = false

-- Notification function
local function notify(msg)
    local frame = Instance.new("Frame", SecondGui)
    frame.Size             = UDim2.new(0, 260, 0, 45)
    frame.Position         = UDim2.new(0.5, -130, -0.1, 0)
    frame.BackgroundColor3 = Color3.new(0, 0, 0)
    frame.BackgroundTransparency = 0.2
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 8)
    local stroke = Instance.new("UIStroke", frame)
    stroke.Color = Color3.new(1, 0, 0)
    local label = Instance.new("TextLabel", frame)
    label.Size                = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text                = "L3 TEAM: " .. msg
    label.TextColor3          = Color3.new(1, 1, 1)
    label.TextScaled          = true
    label.Font                = Enum.Font.GothamBold
    frame:TweenPosition(UDim2.new(0.5, -130, 0.05, 0), "Out", "Back", 0.5)
    task.delay(2, function()
        frame:TweenPosition(UDim2.new(0.5, -130, -0.1, 0), "In", "Quad", 0.5)
        task.wait(0.6)
        frame:Destroy()
    end)
end

-- Minimized restore button
local RestoreBtn = Instance.new("TextButton", SecondGui)
RestoreBtn.Size             = UDim2.new(0, 50, 0, 50)
RestoreBtn.Position         = UDim2.new(0, 10, 0.5, 0)
RestoreBtn.Text             = "open"
RestoreBtn.Visible          = false
RestoreBtn.BackgroundColor3 = Color3.new(0, 0, 0)
RestoreBtn.BackgroundTransparency = 0.4
RestoreBtn.TextColor3       = Color3.new(1, 1, 1)
RestoreBtn.Draggable        = true
Instance.new("UICorner", RestoreBtn).CornerRadius = UDim.new(0, 8)
Instance.new("UIStroke", RestoreBtn).Color = Color3.fromRGB(0, 200, 255)

-- Suction Panel
local SuctionPanel = Instance.new("Frame", SecondGui)
SuctionPanel.Size             = UDim2.new(0, 280, 0, 200)
SuctionPanel.Position         = UDim2.new(0.5, -140, 0.5, -100)
SuctionPanel.BackgroundColor3 = Color3.new(0, 0, 0)
SuctionPanel.BackgroundTransparency = 0.2
SuctionPanel.Draggable        = true
SuctionPanel.Active           = true
SuctionPanel.BorderSizePixel  = 0
Instance.new("UICorner", SuctionPanel).CornerRadius = UDim.new(0, 10)
local spStroke = Instance.new("UIStroke", SuctionPanel)
spStroke.Color = Color3.fromRGB(0, 200, 255)

-- Close (minimize) button on suction panel
local CloseSpBtn = Instance.new("TextButton", SuctionPanel)
CloseSpBtn.Size             = UDim2.new(0, 30, 0, 30)
CloseSpBtn.Position         = UDim2.new(1, -35, 0, 5)
CloseSpBtn.Text             = "X"
CloseSpBtn.Font             = Enum.Font.GothamBold
CloseSpBtn.TextSize         = 14
CloseSpBtn.BackgroundColor3 = Color3.new(0.8, 0, 0)
CloseSpBtn.TextColor3       = Color3.new(1, 1, 1)
CloseSpBtn.ZIndex           = 5
CloseSpBtn.BorderSizePixel  = 0
Instance.new("UICorner", CloseSpBtn).CornerRadius = UDim.new(0, 6)

-- Minimize button on suction panel
local MinSpBtn = Instance.new("TextButton", SuctionPanel)
MinSpBtn.Size             = UDim2.new(0, 30, 0, 30)
MinSpBtn.Position         = UDim2.new(1, -70, 0, 5)
MinSpBtn.Text             = "-"
MinSpBtn.Font             = Enum.Font.GothamBold
MinSpBtn.TextSize         = 18
MinSpBtn.BackgroundColor3 = Color3.new(1, 1, 0)
MinSpBtn.TextColor3       = Color3.new(0, 0, 0)
MinSpBtn.ZIndex           = 5
MinSpBtn.BorderSizePixel  = 0
Instance.new("UICorner", MinSpBtn).CornerRadius = UDim.new(0, 6)

-- Content frame (toggleable by minimize)
local ContentFrame = Instance.new("Frame", SuctionPanel)
ContentFrame.Size                = UDim2.new(1, 0, 1, 0)
ContentFrame.BackgroundTransparency = 1

-- Name input
local VictimBox = Instance.new("TextBox", ContentFrame)
VictimBox.Size            = UDim2.new(0, 160, 0, 40)
VictimBox.Position        = UDim2.new(0, 10, 0.2, 0)
VictimBox.PlaceholderText = "اسم الضحية..."
VictimBox.BackgroundColor3 = Color3.new(0.1, 0.1, 1)
VictimBox.TextColor3      = Color3.new(1, 1, 1)
VictimBox.BorderSizePixel = 0
Instance.new("UICorner", VictimBox).CornerRadius = UDim.new(0, 8)

-- Avatar preview
local AvatarImg = Instance.new("ImageLabel", ContentFrame)
AvatarImg.Size             = UDim2.new(0, 55, 0, 55)
AvatarImg.Position         = UDim2.new(0.75, 0, 0.15, 0)
AvatarImg.BackgroundColor3 = Color3.new(0, 0, 0)
AvatarImg.BackgroundTransparency = 0.5
Instance.new("UICorner", AvatarImg).CornerRadius = UDim.new(1, 0)

-- Turbo button
local TurboBtn = Instance.new("TextButton", ContentFrame)
TurboBtn.Size             = UDim2.new(0, 240, 0, 40)
TurboBtn.Position         = UDim2.new(0.5, -120, 0.65, 0)
TurboBtn.Text             = "بدء مص (Turbo)"
TurboBtn.TextSize         = 20
TurboBtn.Font             = Enum.Font.GothamBold
TurboBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 220)
TurboBtn.TextColor3       = Color3.new(1, 1, 1)
TurboBtn.BorderSizePixel  = 0
Instance.new("UICorner", TurboBtn).CornerRadius = UDim.new(0, 8)

-- Suction (Turbo) logic
local turboActive   = false
local turboTarget   = nil
local turboConn     = nil

VictimBox:GetPropertyChangedSignal("Text"):Connect(function()
    local query = VictimBox.Text:lower()
    if #query >= 2 then
        for _, p in ipairs(Players:GetPlayers()) do
            if p.Name:lower():sub(1, #query) == query and p ~= LocalPlayer then
                turboTarget = p
                AvatarImg.Image = "rbxthumb://type=AvatarHeadShot&id=" .. p.UserId .. "&w=150&h=150"
                break
            end
        end
    end
end)

TurboBtn.MouseButton1Click:Connect(function()
    if not turboTarget then
        notify("اكتب اسم الضحية")
        return
    end

    turboActive = not turboActive
    TurboBtn.Text = turboActive and "إيقاف" or "بدء مص (Turbo)"

    if turboActive then
        notify("تم التفعيل (أعلى)")
        turboConn = RunService.Heartbeat:Connect(function()
            if turboActive and turboTarget.Character
                and turboTarget.Character:FindFirstChild("Head")
                and LocalPlayer.Character then
                LocalPlayer.Character.Humanoid.Sit = true
                local offset = math.sin(tick() * 22) * 1.1
                LocalPlayer.Character:SetPrimaryPartCFrame(
                    turboTarget.Character.Head.CFrame
                    * CFrame.new(0, -0.6 + offset, -0.6)
                    * CFrame.Angles(0, math.rad(180), 0)
                )
            end
        end)
    else
        if turboConn then turboConn:Disconnect() turboConn = nil end
        if LocalPlayer.Character then
            LocalPlayer.Character.Humanoid.Sit = false
        end
        notify("إيقاف")
    end
end)

-- Close suction panel
CloseSpBtn.MouseButton1Click:Connect(function()
    SuctionPanel.Visible = false
    RestoreBtn.Visible   = true
end)

RestoreBtn.MouseButton1Click:Connect(function()
    SuctionPanel.Visible = true
    RestoreBtn.Visible   = false
end)

-- Minimize content
local contentVisible = true
MinSpBtn.MouseButton1Click:Connect(function()
    contentVisible = not contentVisible
    ContentFrame.Visible = contentVisible
    SuctionPanel:TweenSize(
        contentVisible and UDim2.new(0, 280, 0, 200) or UDim2.new(0, 280, 0, 35),
        "Out", "Quad", 0.3
    )
end)

notify("L3 TEAM - Position Adjusted")
