-- ╔══════════════════════════════════════╗
-- ║          L3 TEAM SCRIPT HUB         ║
-- ╚══════════════════════════════════════╝

-- ══════════════ SERVICES ══════════════
local TweenService = game:GetService("TweenService")
local Players      = game:GetService("Players")
local RunService   = game:GetService("RunService")
local RS           = game:GetService("ReplicatedStorage")

local LocalPlayer  = Players.LocalPlayer
local PlayerGui    = LocalPlayer:WaitForChild("PlayerGui")

-- ══════════════ SEND COMMAND (lazy remotes) ══════════════
local function sendCommand(cmd)
    local args = { [1] = cmd }
    pcall(function()
        local remote = RS:FindFirstChild("RemoteEvents") and
                       RS.RemoteEvents:FindFirstChild("ChatEvent")
        if remote then remote:FireServer(unpack(args)) end
    end)
    pcall(function()
        local sig = RS:FindFirstChild("HDAdminHDClient") and
                    RS.HDAdminHDClient:FindFirstChild("Signals") and
                    RS.HDAdminHDClient.Signals:FindFirstChild("RequestCommandModification")
        if sig then sig:InvokeServer(unpack(args)) end
    end)
end

-- ══════════════ CLEANUP ══════════════
for _, name in ipairs({ "L3_TEAM_GUI", "L3_FINAL_V4" }) do
    local g = PlayerGui:FindFirstChild(name)
    if g then g:Destroy() end
end

-- ══════════════ GUI 1: MAIN PANEL ══════════════
local MainGui = Instance.new("ScreenGui")
MainGui.Name         = "L3_TEAM_GUI"
MainGui.ResetOnSpawn = false
MainGui.Parent       = PlayerGui

-- Blur
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
Instance.new("UICorner", ToggleBtn)
local ToggleStroke = Instance.new("UIStroke", ToggleBtn)
ToggleStroke.Thickness = 2
ToggleStroke.Color     = Color3.fromRGB(0, 150, 255)

-- Main Frame
local MainFrame = Instance.new("Frame", MainGui)
MainFrame.Size             = UDim2.new(0, 380, 0, 290)
MainFrame.Position         = UDim2.new(0.5, -190, 0.5, -145)
MainFrame.BackgroundColor3 = Color3.fromRGB(0, 30, 100)
MainFrame.Visible          = false
MainFrame.Active           = true
MainFrame.Draggable        = true
MainFrame.BorderSizePixel  = 0
Instance.new("UICorner", MainFrame)
local MainStroke = Instance.new("UIStroke", MainFrame)
MainStroke.Thickness = 2
MainStroke.Color     = Color3.fromRGB(0, 150, 255)

-- Background image
local BgImg = Instance.new("ImageLabel", MainFrame)
BgImg.Size                 = UDim2.new(1, 0, 1, 0)
BgImg.Image                = "rbxassetid://11942020306"
BgImg.BackgroundTransparency = 1
BgImg.ImageTransparency    = 0.7
BgImg.ZIndex               = 0

-- Animated overlay
local BgAnim = Instance.new("ImageLabel", MainFrame)
BgAnim.Size                = UDim2.new(1, 0, 1, 0)
BgAnim.Image               = "rbxassetid://8992230677"
BgAnim.BackgroundTransparency = 1
BgAnim.ImageTransparency   = 0.8
BgAnim.ZIndex              = 0

-- Title
local TitleLbl = Instance.new("TextLabel", MainFrame)
TitleLbl.Size               = UDim2.new(1, -10, 0, 28)
TitleLbl.Position           = UDim2.new(0, 5, 0, 5)
TitleLbl.BackgroundTransparency = 1
TitleLbl.Text               = "⚡ made by L3 TEAM"
TitleLbl.TextColor3         = Color3.fromRGB(0, 200, 255)
TitleLbl.TextScaled         = true
TitleLbl.Font               = Enum.Font.GothamBold
TitleLbl.ZIndex             = 2

-- Search box
local NameBox = Instance.new("TextBox", MainFrame)
NameBox.Size              = UDim2.new(0, 300, 0, 38)
NameBox.Position          = UDim2.new(0, 40, 0, 40)
NameBox.PlaceholderText   = "اكتب اول حروف الاسم"
NameBox.BackgroundColor3  = Color3.fromRGB(0, 40, 120)
NameBox.TextColor3        = Color3.fromRGB(255, 255, 255)
NameBox.Font              = Enum.Font.Gotham
NameBox.TextSize          = 15
NameBox.BorderSizePixel   = 0
NameBox.ZIndex            = 2
Instance.new("UICorner", NameBox)

-- Button factory
local function makeBtn(parent, text, posY, w, h)
    w = w or 300; h = h or 40
    local btn = Instance.new("TextButton", parent)
    btn.Size             = UDim2.new(0, w, 0, h)
    btn.Position         = UDim2.new(0, 40, 0, posY)
    btn.Text             = text
    btn.Font             = Enum.Font.GothamBold
    btn.TextSize         = 15
    btn.TextColor3       = Color3.fromRGB(0, 255, 255)
    btn.BackgroundColor3 = Color3.fromRGB(0, 60, 150)
    btn.BorderSizePixel  = 0
    btn.ZIndex           = 2
    Instance.new("UICorner", btn)
    Instance.new("UIStroke", btn).Color = Color3.fromRGB(0, 150, 255)
    btn.MouseEnter:Connect(function() btn.BackgroundColor3 = Color3.fromRGB(0, 80, 200) end)
    btn.MouseLeave:Connect(function() btn.BackgroundColor3 = Color3.fromRGB(0, 60, 150) end)
    return btn
end

local SpamBtn     = makeBtn(MainFrame, "تشغيل سبام",   90)
local CustomBtn   = makeBtn(MainFrame, "اوامر مخصصة", 140)
local SettingsBtn = makeBtn(MainFrame, "⚙️ إعدادات",  190)

-- ══════════════ CLOSE BTN HELPER ══════════════
local function addCloseBtn(parent, onClose)
    local btn = Instance.new("TextButton", parent)
    btn.Size             = UDim2.new(0, 28, 0, 28)
    btn.Position         = UDim2.new(1, -33, 0, 5)
    btn.Text             = "✕"
    btn.TextSize         = 15
    btn.Font             = Enum.Font.GothamBold
    btn.BackgroundColor3 = Color3.fromRGB(200, 30, 30)
    btn.TextColor3       = Color3.new(1, 1, 1)
    btn.BorderSizePixel  = 0
    btn.ZIndex           = 10
    btn.Parent           = parent
    Instance.new("UICorner", btn)
    btn.MouseButton1Click:Connect(function()
        if onClose then onClose() else parent:Destroy() end
    end)
    return btn
end

-- ══════════════ SLIDE-IN ANIMATION ══════════════
local function slideIn(frame)
    frame.Visible = true
    local cx = frame.Position.X.Scale
    local co = frame.Position.X.Offset
    frame.Position = UDim2.new(cx, co, 1, 0)
    TweenService:Create(frame,
        TweenInfo.new(0.35, Enum.EasingStyle.Back, Enum.EasingDirection.Out),
        { Position = UDim2.new(cx, co, 0.5, -frame.Size.Y.Offset / 2) }
    ):Play()
end

-- ══════════════ TOGGLE MAIN PANEL ══════════════
ToggleBtn.MouseButton1Click:Connect(function()
    local show = not MainFrame.Visible
    MainFrame.Visible = show
    Blur.Size = show and 12 or 0
    if show then slideIn(MainFrame) end
end)

-- ══════════════ SPAM ══════════════
local spamActive = false
local cmd1       = "+RE"
local cmd2       = "+LOGS"
local targetName = nil

local function searchPlayers()
    local res   = {}
    local query = NameBox.Text:lower()
    if query == "" then return res end
    for _, p in ipairs(Players:GetPlayers()) do
        if p.Name:lower():sub(1, #query) == query then
            table.insert(res, p)
        end
    end
    return res
end

local function startSpam(name)
    targetName   = name:upper()
    spamActive   = true
    SpamBtn.Text = "ايقاف سبام"
    task.spawn(function()
        while spamActive do
            local cmd = (cmd1 .. " " .. targetName .. " " .. cmd2 .. " " .. targetName .. " "):rep(30)
            sendCommand(cmd)
            task.wait(0.2)
        end
    end)
end

-- Player list popup (multiple matches)
local function showPlayerList(list)
    local pop = Instance.new("Frame", MainGui)
    pop.Name             = "PlayerListPopup"
    pop.Size             = UDim2.new(0, 300, 0, math.min(#list * 60 + 50, 320))
    pop.Position         = UDim2.new(0.5, -150, 0.5, -160)
    pop.BackgroundColor3 = Color3.fromRGB(0, 25, 90)
    pop.Active           = true
    pop.Draggable        = true
    pop.BorderSizePixel  = 0
    Instance.new("UICorner", pop)
    Instance.new("UIStroke", pop).Color = Color3.fromRGB(0, 150, 255)
    addCloseBtn(pop)
    slideIn(pop)

    local scroll = Instance.new("ScrollingFrame", pop)
    scroll.Size              = UDim2.new(1, 0, 1, -45)
    scroll.Position          = UDim2.new(0, 0, 0, 40)
    scroll.BackgroundTransparency = 1
    scroll.ScrollBarThickness = 4
    scroll.CanvasSize        = UDim2.new(0, 0, 0, #list * 62)
    scroll.BorderSizePixel   = 0

    for i, player in ipairs(list) do
        local row = Instance.new("TextButton", scroll)
        row.Size             = UDim2.new(1, -10, 0, 55)
        row.Position         = UDim2.new(0, 5, 0, (i - 1) * 60)
        row.BackgroundColor3 = Color3.fromRGB(0, 50, 140)
        row.BorderSizePixel  = 0
        row.Text             = "  " .. player.DisplayName .. "  (" .. player.Name .. ")"
        row.TextColor3       = Color3.fromRGB(0, 255, 255)
        row.Font             = Enum.Font.Gotham
        row.TextSize         = 14
        row.TextXAlignment   = Enum.TextXAlignment.Left
        Instance.new("UICorner", row)
        local av = Instance.new("ImageLabel", row)
        av.Size             = UDim2.new(0, 45, 0, 45)
        av.Position         = UDim2.new(1, -50, 0, 5)
        av.BackgroundTransparency = 1
        av.Image            = "rbxthumb://type=AvatarHeadShot&id=" .. player.UserId .. "&w=150&h=150"
        Instance.new("UICorner", av).CornerRadius = UDim.new(1, 0)
        row.MouseButton1Click:Connect(function()
            pop:Destroy()
            startSpam(player.Name)
        end)
    end
end

SpamBtn.MouseButton1Click:Connect(function()
    if spamActive then
        spamActive   = false
        SpamBtn.Text = "تشغيل سبام"
        return
    end
    local found = searchPlayers()
    if #found == 0 then return end
    if #found == 1 then
        startSpam(found[1].Name)
    else
        showPlayerList(found)
    end
end)

-- ══════════════ CUSTOM COMMANDS PANEL ══════════════
CustomBtn.MouseButton1Click:Connect(function()
    local ex = MainGui:FindFirstChild("CustomPanel")
    if ex then ex:Destroy(); return end

    local pan = Instance.new("Frame", MainGui)
    pan.Name             = "CustomPanel"
    pan.Size             = UDim2.new(0, 270, 0, 210)
    pan.Position         = UDim2.new(0.5, -135, 0.5, -105)
    pan.BackgroundColor3 = Color3.fromRGB(0, 25, 90)
    pan.Active           = true
    pan.Draggable        = true
    pan.BorderSizePixel  = 0
    Instance.new("UICorner", pan)
    Instance.new("UIStroke", pan).Color = Color3.fromRGB(0, 150, 255)
    addCloseBtn(pan)
    slideIn(pan)

    local hdr = Instance.new("TextLabel", pan)
    hdr.Size               = UDim2.new(1, -40, 0, 28)
    hdr.Position           = UDim2.new(0, 8, 0, 5)
    hdr.BackgroundTransparency = 1
    hdr.Text               = "اوامر مخصصة"
    hdr.TextColor3         = Color3.fromRGB(0, 200, 255)
    hdr.Font               = Enum.Font.GothamBold
    hdr.TextSize           = 16
    hdr.ZIndex             = 3

    local function inputRow(label, placeholder, yPos)
        local lbl = Instance.new("TextLabel", pan)
        lbl.Size               = UDim2.new(0, 80, 0, 22)
        lbl.Position           = UDim2.new(0, 10, 0, yPos)
        lbl.BackgroundTransparency = 1
        lbl.Text               = label
        lbl.TextColor3         = Color3.fromRGB(180, 220, 255)
        lbl.Font               = Enum.Font.Gotham
        lbl.TextSize           = 13
        local tb = Instance.new("TextBox", pan)
        tb.Size             = UDim2.new(0, 160, 0, 32)
        tb.Position         = UDim2.new(0, 15, 0, yPos + 22)
        tb.PlaceholderText  = placeholder
        tb.BackgroundColor3 = Color3.fromRGB(0, 40, 120)
        tb.TextColor3       = Color3.fromRGB(0, 220, 255)
        tb.Font             = Enum.Font.Gotham
        tb.TextSize         = 14
        tb.BorderSizePixel  = 0
        tb.ClearTextOnFocus = false
        Instance.new("UICorner", tb)
        return tb
    end

    local tb1 = inputRow("أمر 1:", "حالياً: " .. cmd1, 38)
    local tb2 = inputRow("أمر 2:", "حالياً: " .. cmd2, 110)

    local saveBtn = makeBtn(pan, "حفظ ✓", 168, 230, 32)
    saveBtn.Position = UDim2.new(0, 20, 0, 168)
    saveBtn.MouseButton1Click:Connect(function()
        if tb1.Text ~= "" then cmd1 = tb1.Text end
        if tb2.Text ~= "" then cmd2 = tb2.Text end
        pan:Destroy()
    end)
end)

-- ══════════════ SETTINGS PANEL ══════════════
local settings     = { CopyProtection = false, Sky = false, SpinButton = false }
local hdBackup     = nil

SettingsBtn.MouseButton1Click:Connect(function()
    local ex = MainGui:FindFirstChild("SettingsPanel")
    if ex then ex:Destroy(); return end

    local pan = Instance.new("Frame", MainGui)
    pan.Name             = "SettingsPanel"
    pan.Size             = UDim2.new(0, 270, 0, 210)
    pan.Position         = UDim2.new(0.5, -135, 0.5, -105)
    pan.BackgroundColor3 = Color3.fromRGB(0, 25, 90)
    pan.Active           = true
    pan.Draggable        = true
    pan.BorderSizePixel  = 0
    Instance.new("UICorner", pan)
    Instance.new("UIStroke", pan).Color = Color3.fromRGB(0, 150, 255)
    addCloseBtn(pan)
    slideIn(pan)

    local hdr = Instance.new("TextLabel", pan)
    hdr.Size               = UDim2.new(1, -40, 0, 28)
    hdr.Position           = UDim2.new(0, 8, 0, 5)
    hdr.BackgroundTransparency = 1
    hdr.Text               = "⚙️ إعدادات"
    hdr.TextColor3         = Color3.fromRGB(0, 200, 255)
    hdr.Font               = Enum.Font.GothamBold
    hdr.TextSize           = 16

    local function toggleRow(label, yPos, cb)
        local state = false
        local btn = Instance.new("TextButton", pan)
        btn.Size             = UDim2.new(0, 230, 0, 38)
        btn.Position         = UDim2.new(0, 20, 0, yPos)
        btn.Text             = label .. "  ◼ OFF"
        btn.Font             = Enum.Font.GothamBold
        btn.TextSize         = 14
        btn.TextColor3       = Color3.fromRGB(0, 255, 255)
        btn.BackgroundColor3 = Color3.fromRGB(0, 60, 150)
        btn.BorderSizePixel  = 0
        Instance.new("UICorner", btn)
        btn.MouseButton1Click:Connect(function()
            state = not state
            btn.Text = label .. (state and "  ◉ ON" or "  ◼ OFF")
            btn.TextColor3 = state and Color3.fromRGB(0, 255, 100) or Color3.fromRGB(0, 255, 255)
            cb(state)
        end)
    end

    toggleRow("حماية النسخ", 42, function(on)
        settings.CopyProtection = on
        if on then
            local hd = PlayerGui:FindFirstChild("HDAdminInterface")
            if hd then hdBackup = hd:Clone(); hd:Destroy() end
        elseif hdBackup then
            hdBackup.Parent = PlayerGui
        end
    end)

    toggleRow("Sky خارق", 90, function(on)
        settings.Sky = on
        if on then
            Instance.new("Sky", game.Lighting)
        else
            local s = game.Lighting:FindFirstChildOfClass("Sky")
            if s then s:Destroy() end
        end
    end)

    toggleRow("Spin Button", 138, function(on)
        settings.SpinButton = on
    end)
end)

-- ══════════════ ANIM LOOP ══════════════
task.spawn(function()
    while true do
        local t = tick()
        BgAnim.Position = UDim2.new(0, math.sin(t / 5) * 8, 0, math.cos(t / 5) * 8)
        task.wait(0.05)
    end
end)

-- ══════════════ GUI 2: SUCTION / TURBO ══════════════
local SecGui = Instance.new("ScreenGui", PlayerGui)
SecGui.Name         = "L3_FINAL_V4"
SecGui.ResetOnSpawn = false

-- Notification toast
local function notify(msg)
    local f = Instance.new("Frame", SecGui)
    f.Size             = UDim2.new(0, 260, 0, 44)
    f.Position         = UDim2.new(0.5, -130, -0.08, 0)
    f.BackgroundColor3 = Color3.new(0, 0, 0)
    f.BackgroundTransparency = 0.2
    f.BorderSizePixel  = 0
    Instance.new("UICorner", f)
    Instance.new("UIStroke", f).Color = Color3.new(1, 0, 0)
    local lbl = Instance.new("TextLabel", f)
    lbl.Size               = UDim2.new(1, 0, 1, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text               = "L3 TEAM: " .. msg
    lbl.TextColor3         = Color3.new(1, 1, 1)
    lbl.TextScaled         = true
    lbl.Font               = Enum.Font.GothamBold
    f:TweenPosition(UDim2.new(0.5, -130, 0.05, 0), "Out", "Back", 0.5)
    task.delay(2.5, function()
        f:TweenPosition(UDim2.new(0.5, -130, -0.08, 0), "In", "Quad", 0.4)
        task.wait(0.5)
        f:Destroy()
    end)
end

-- Restore button
local RestoreBtn = Instance.new("TextButton", SecGui)
RestoreBtn.Size             = UDim2.new(0, 55, 0, 55)
RestoreBtn.Position         = UDim2.new(0, 10, 0.5, 0)
RestoreBtn.Text             = "L3"
RestoreBtn.Font             = Enum.Font.GothamBold
RestoreBtn.TextSize         = 14
RestoreBtn.Visible          = false
RestoreBtn.BackgroundColor3 = Color3.fromRGB(0, 50, 150)
RestoreBtn.TextColor3       = Color3.fromRGB(0, 200, 255)
RestoreBtn.Draggable        = true
RestoreBtn.BorderSizePixel  = 0
Instance.new("UICorner", RestoreBtn)
Instance.new("UIStroke", RestoreBtn).Color = Color3.fromRGB(0, 200, 255)

-- Suction panel
local SP = Instance.new("Frame", SecGui)
SP.Name             = "SuctionPanel"
SP.Size             = UDim2.new(0, 290, 0, 205)
SP.Position         = UDim2.new(0.5, -145, 0.5, -102)
SP.BackgroundColor3 = Color3.new(0, 0, 0)
SP.BackgroundTransparency = 0.15
SP.Active           = true
SP.Draggable        = true
SP.BorderSizePixel  = 0
Instance.new("UICorner", SP)
Instance.new("UIStroke", SP).Color = Color3.fromRGB(0, 200, 255)

-- X button (hide panel)
local SPClose = Instance.new("TextButton", SP)
SPClose.Size             = UDim2.new(0, 28, 0, 28)
SPClose.Position         = UDim2.new(1, -33, 0, 5)
SPClose.Text             = "X"
SPClose.Font             = Enum.Font.GothamBold
SPClose.TextSize         = 14
SPClose.BackgroundColor3 = Color3.new(0.8, 0, 0)
SPClose.TextColor3       = Color3.new(1, 1, 1)
SPClose.ZIndex           = 5
SPClose.BorderSizePixel  = 0
Instance.new("UICorner", SPClose)

-- Minimize button
local SPMin = Instance.new("TextButton", SP)
SPMin.Size             = UDim2.new(0, 28, 0, 28)
SPMin.Position         = UDim2.new(1, -65, 0, 5)
SPMin.Text             = "–"
SPMin.Font             = Enum.Font.GothamBold
SPMin.TextSize         = 16
SPMin.BackgroundColor3 = Color3.fromRGB(200, 160, 0)
SPMin.TextColor3       = Color3.new(0, 0, 0)
SPMin.ZIndex           = 5
SPMin.BorderSizePixel  = 0
Instance.new("UICorner", SPMin)

-- Content
local SPContent = Instance.new("Frame", SP)
SPContent.Size               = UDim2.new(1, 0, 1, -38)
SPContent.Position           = UDim2.new(0, 0, 0, 38)
SPContent.BackgroundTransparency = 1

-- Name textbox
local VictimBox = Instance.new("TextBox", SPContent)
VictimBox.Size            = UDim2.new(0, 170, 0, 40)
VictimBox.Position        = UDim2.new(0, 10, 0, 5)
VictimBox.PlaceholderText = "اسم الضحية..."
VictimBox.BackgroundColor3 = Color3.new(0.1, 0.1, 0.9)
VictimBox.TextColor3      = Color3.new(1, 1, 1)
VictimBox.Font            = Enum.Font.Gotham
VictimBox.TextSize        = 15
VictimBox.BorderSizePixel = 0
Instance.new("UICorner", VictimBox)

-- Avatar
local AvatarImg = Instance.new("ImageLabel", SPContent)
AvatarImg.Size             = UDim2.new(0, 55, 0, 55)
AvatarImg.Position         = UDim2.new(1, -65, 0, 5)
AvatarImg.BackgroundColor3 = Color3.new(0, 0, 0)
AvatarImg.BackgroundTransparency = 0.4
Instance.new("UICorner", AvatarImg).CornerRadius = UDim.new(1, 0)

-- Turbo button
local TurboBtn = Instance.new("TextButton", SPContent)
TurboBtn.Size             = UDim2.new(0, 260, 0, 44)
TurboBtn.Position         = UDim2.new(0.5, -130, 0, 60)
TurboBtn.Text             = "بدء مص (Turbo)"
TurboBtn.TextSize         = 18
TurboBtn.Font             = Enum.Font.GothamBold
TurboBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 220)
TurboBtn.TextColor3       = Color3.new(1, 1, 1)
TurboBtn.BorderSizePixel  = 0
Instance.new("UICorner", TurboBtn)

-- Turbo logic
local turboActive = false
local turboTarget = nil
local turboConn   = nil

VictimBox:GetPropertyChangedSignal("Text"):Connect(function()
    local q = VictimBox.Text:lower()
    if #q < 2 then return end
    for _, p in ipairs(Players:GetPlayers()) do
        if p.Name:lower():sub(1, #q) == q and p ~= LocalPlayer then
            turboTarget = p
            AvatarImg.Image = "rbxthumb://type=AvatarHeadShot&id=" .. p.UserId .. "&w=150&h=150"
            return
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
    TurboBtn.BackgroundColor3 = turboActive
        and Color3.fromRGB(180, 30, 30)
        or  Color3.fromRGB(0, 100, 220)
    if turboActive then
        notify("تم التفعيل")
        turboConn = RunService.Heartbeat:Connect(function()
            if not turboActive then return end
            if not (turboTarget and turboTarget.Character) then return end
            local head = turboTarget.Character:FindFirstChild("Head")
            if not head then return end
            if not LocalPlayer.Character then return end
            pcall(function()
                LocalPlayer.Character.Humanoid.Sit = true
                local offset = math.sin(tick() * 22) * 1.1
                LocalPlayer.Character:SetPrimaryPartCFrame(
                    head.CFrame
                    * CFrame.new(0, -0.6 + offset, -0.6)
                    * CFrame.Angles(0, math.rad(180), 0)
                )
            end)
        end)
    else
        if turboConn then turboConn:Disconnect(); turboConn = nil end
        pcall(function()
            if LocalPlayer.Character then
                LocalPlayer.Character.Humanoid.Sit = false
            end
        end)
        notify("إيقاف")
    end
end)

SPClose.MouseButton1Click:Connect(function()
    SP.Visible          = false
    RestoreBtn.Visible  = true
end)
RestoreBtn.MouseButton1Click:Connect(function()
    SP.Visible          = true
    RestoreBtn.Visible  = false
end)

local spMinimized = false
SPMin.MouseButton1Click:Connect(function()
    spMinimized = not spMinimized
    SPContent.Visible = not spMinimized
    SP:TweenSize(
        spMinimized and UDim2.new(0, 290, 0, 38) or UDim2.new(0, 290, 0, 205),
        "Out", "Quad", 0.25
    )
end)

notify("L3 TEAM - Ready ✓")
