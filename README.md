-- [[ Rscripts Risk Notice ]]
-- This script is not verified by rscripts.net. Deal with caution.
-- [[ End Rscripts Risk Notice ]]

-- 🌸 AXR SCRIPT / FISHING - SAKURA EDITION 🌸

if not game:IsLoaded() then game.Loaded:Wait() end

local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local CoreGui = game:GetService("CoreGui")
local VirtualUser = game:GetService("VirtualUser")
local VirtualInputManager = game:GetService("VirtualInputManager")
local UserInputService = game:GetService("UserInputService")
local StarterGui = game:GetService("StarterGui")

local LocalPlayer = Players.LocalPlayer
local Remotes = ReplicatedStorage:WaitForChild("Remotes", 10)

-- 기존 GUI 정리
pcall(function()
    for _, gui in pairs(CoreGui:GetChildren()) do
        if gui.Name == "AXR_System" then gui:Destroy() end
    end
end)

-- 시스템 저장용 폴더 및 파일 경로
local SaveFolder = "AXRScript_System"
local DataFile = SaveFolder .. "/KeyTimerData.json"

if makefolder and not isfolder(SaveFolder) then
    pcall(function() makefolder(SaveFolder) end)
end

-- 로컬 데이터 로드/저장
local function loadKeyData()
    if isfile and readfile and isfile(DataFile) then
        local success, result = pcall(function()
            return HttpService:JSONDecode(readfile(DataFile))
        end)
        if success and type(result) == "table" then return result end
    end
    return {}
end

local function saveKeyData(data)
    if writefile then
        pcall(function()
            writefile(DataFile, HttpService:JSONEncode(data))
        end)
    end
end

local keyData = loadKeyData()

-- 키 및 유저 정의
local DEV_KEY = "5.01po"
local DEV_USER = "zxxdaswo"

local TIMER_KEYS = {
    ["5min.key"] = 300,   -- 5분 키
    ["dop.8915"] = 900    -- 15분 키
}

local isDevMode = false
local currentActiveKey = nil
local activeDuration = 0

-- 메인 ScreenGui 생성
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "AXR_System"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = CoreGui

-- ==========================================
-- 1. 키 검증 GUI
-- ==========================================
local KeyFrame = Instance.new("Frame")
KeyFrame.Name = "KeyFrame"
KeyFrame.Size = UDim2.new(0, 380, 0, 220)
KeyFrame.Position = UDim2.new(0.5, -190, 0.5, -110)
KeyFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
KeyFrame.BorderSizePixel = 0
KeyFrame.Parent = ScreenGui

local KeyCorner = Instance.new("UICorner")
KeyCorner.CornerRadius = UDim.new(0, 10)
KeyCorner.Parent = KeyFrame

local KeyStroke = Instance.new("UIStroke")
KeyStroke.Color = Color3.fromRGB(155, 89, 182)
KeyStroke.Thickness = 2
KeyStroke.Parent = KeyFrame

local KeyTitle = Instance.new("TextLabel")
KeyTitle.Size = UDim2.new(1, 0, 0, 40)
KeyTitle.Position = UDim2.new(0, 0, 0, 15)
KeyTitle.BackgroundTransparency = 1
KeyTitle.Text = "🌸 AXR 스크립트 인증"
KeyTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyTitle.TextSize = 20
KeyTitle.Font = Enum.Font.GothamBold
KeyTitle.Parent = KeyFrame

local KeyInput = Instance.new("TextBox")
KeyInput.Size = UDim2.new(1, -40, 0, 40)
KeyInput.Position = UDim2.new(0, 20, 0, 75)
KeyInput.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
KeyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyInput.PlaceholderText = "키를 입력하세요..."
KeyInput.PlaceholderColor3 = Color3.fromRGB(120, 120, 120)
KeyInput.TextSize = 14
KeyInput.Font = Enum.Font.Gotham
KeyInput.ClearTextOnFocus = false
KeyInput.Parent = KeyFrame

local InputCorner = Instance.new("UICorner")
InputCorner.CornerRadius = UDim.new(0, 6)
InputCorner.Parent = KeyInput

local KeyBtn = Instance.new("TextButton")
KeyBtn.Size = UDim2.new(1, -40, 0, 42)
KeyBtn.Position = UDim2.new(0, 20, 0, 135)
KeyBtn.BackgroundColor3 = Color3.fromRGB(155, 89, 182)
KeyBtn.Text = "인증하기"
KeyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyBtn.TextSize = 15
KeyBtn.Font = Enum.Font.GothamBold
KeyBtn.Parent = KeyFrame

local BtnCorner = Instance.new("UICorner")
BtnCorner.CornerRadius = UDim.new(0, 6)
BtnCorner.Parent = KeyBtn

-- ==========================================
-- 2. 메인 스크립트 GUI
-- ==========================================
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 500, 0, 340)
MainFrame.Position = UDim2.new(0.5, -250, 0.5, -170)
MainFrame.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
MainFrame.BorderSizePixel = 0
MainFrame.Visible = false
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(155, 89, 182)
MainStroke.Thickness = 2
MainStroke.Parent = MainFrame

-- 상단 바
local TopBar = Instance.new("Frame")
TopBar.Size = UDim2.new(1, 0, 0, 42)
TopBar.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
TopBar.BorderSizePixel = 0
TopBar.Parent = MainFrame

local TopBarCorner = Instance.new("UICorner")
TopBarCorner.CornerRadius = UDim.new(0, 10)
TopBarCorner.Parent = TopBar

local MainTitle = Instance.new("TextLabel")
MainTitle.Size = UDim2.new(0, 200, 1, 0)
MainTitle.Position = UDim2.new(0, 15, 0, 0)
MainTitle.BackgroundTransparency = 1
MainTitle.Text = "🌸 AXR 스크립트"
MainTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
MainTitle.TextSize = 16
MainTitle.Font = Enum.Font.GothamBold
MainTitle.TextXAlignment = Enum.TextXAlignment.Left
MainTitle.Parent = TopBar

-- 우측 상단 타이머 레이블
local TimerDisplay = Instance.new("TextLabel")
TimerDisplay.Size = UDim2.new(0, 220, 1, 0)
TimerDisplay.Position = UDim2.new(1, -255, 0, 0)
TimerDisplay.BackgroundTransparency = 1
TimerDisplay.Text = "⏳ 계산 중..."
TimerDisplay.TextColor3 = Color3.fromRGB(255, 215, 0)
TimerDisplay.TextSize = 13
TimerDisplay.Font = Enum.Font.GothamBold
TimerDisplay.TextXAlignment = Enum.TextXAlignment.Right
TimerDisplay.Parent = TopBar

local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 30, 0, 30)
CloseBtn.Position = UDim2.new(1, -35, 0, 6)
CloseBtn.BackgroundTransparency = 1
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Color3.fromRGB(255, 100, 100)
CloseBtn.TextSize = 18
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.Parent = TopBar

CloseBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

-- 드래그 기능
local dragging, dragInput, dragStart, startPos
TopBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then dragging = false end
        end)
    end
end)
TopBar.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then dragInput = input end
end)
UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- 스크롤 및 토글 목록
local Scroll = Instance.new("ScrollingFrame")
Scroll.Size = UDim2.new(1, -20, 1, -55)
Scroll.Position = UDim2.new(0, 10, 0, 48)
Scroll.BackgroundTransparency = 1
Scroll.BorderSizePixel = 0
Scroll.CanvasSize = UDim2.new(0, 0, 0, 420)
Scroll.ScrollBarThickness = 4
Scroll.Parent = MainFrame

local UIList = Instance.new("UIListLayout")
UIList.Padding = UDim.new(0, 8)
UIList.SortOrder = Enum.SortOrder.LayoutOrder
UIList.Parent = Scroll

local function createToggle(name, default, callback)
    local state = default
    local Btn = Instance.new("TextButton")
    Btn.Size = UDim2.new(1, -10, 0, 38)
    Btn.BackgroundColor3 = state and Color3.fromRGB(46, 204, 113) or Color3.fromRGB(40, 40, 40)
    Btn.Text = name .. " : " .. (state and "켜짐 [ON]" or "꺼짐 [OFF]")
    Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    Btn.TextSize = 14
    Btn.Font = Enum.Font.GothamMedium
    Btn.Parent = Scroll

    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 6)
    Corner.Parent = Btn

    Btn.MouseButton1Click:Connect(function()
        state = not state
        Btn.BackgroundColor3 = state and Color3.fromRGB(46, 204, 113) or Color3.fromRGB(40, 40, 40)
        Btn.Text = name .. " : " .. (state and "켜짐 [ON]" or "꺼짐 [OFF]")
        callback(state)
    end)
end

getgenv().autoFish = false
getgenv().autoRebirth = false
getgenv().autoQuests = false
getgenv().autoGifts = false
getgenv().autoCases = false
getgenv().autoPotion = false
getgenv().antiAfk = false
getgenv().autoCodes = false

createToggle("🎣 자동 낚시", false, function(v) getgenv().autoFish = v end)
createToggle("🔄 자동 리버스", false, function(v) getgenv().autoRebirth = v end)
createToggle("📜 자동 퀘스트 완료", false, function(v) getgenv().autoQuests = v end)
createToggle("🎁 자동 선물 수령", false, function(v) getgenv().autoGifts = v end)
createToggle("📦 자동 상자 까기", false, function(v) getgenv().autoCases = v end)
createToggle("🧪 자동 포션 사용", false, function(v) getgenv().autoPotion = v end)
createToggle("🛡️ 안티 AFK (자리비움 방지)", false, function(v) getgenv().antiAfk = v end)
createToggle("🎟️ 자동 프로모션 코드 입력", false, function(v) getgenv().autoCodes = v end)

-- 실시간 타이머 루프
local function startTimerLoop()
    task.spawn(function()
        while task.wait(1) do
            if isDevMode then
                TimerDisplay.Text = "👑 개발자 권한 (무제한)"
                TimerDisplay.TextColor3 = Color3.fromRGB(155, 89, 182)
            elseif currentActiveKey and keyData[currentActiveKey] then
                local kInfo = keyData[currentActiveKey]
                local elapsed = os.time() - kInfo.FirstUsedTime
                local remaining = activeDuration - elapsed

                if remaining <= 0 then
                    kInfo.Blacklisted = true
                    saveKeyData(keyData)
                    TimerDisplay.Text = "⛔ 만료됨 (블랙리스트)"
                    TimerDisplay.TextColor3 = Color3.fromRGB(255, 70, 70)
                    
                    StarterGui:SetCore("SendNotification", {
                        Title = "AXR 스크립트",
                        Text = "타이머 제한 시간이 만료되었습니다.",
                        Duration = 5
                    })
                    task.wait(2)
                    ScreenGui:Destroy()
                    break
                else
                    local mins = math.floor(remaining / 60)
                    local secs = remaining % 60
                    TimerDisplay.Text = string.format("⏳ 남은 시간: %02d분 %02d초", mins, secs)
                end
            end
        end
    end)
end

-- ==========================================
-- 3. 키 인증 처리 로직
-- ==========================================
KeyBtn.MouseButton1Click:Connect(function()
    local inputKey = KeyInput.Text

    -- 1. 개발자 키 검증 (5.01po + zxxdaswo 닉네임 검증)
    if inputKey == DEV_KEY then
        if LocalPlayer.Name == DEV_USER or LocalPlayer.DisplayName == DEV_USER then
            isDevMode = true
            KeyFrame:Destroy()
            MainFrame.Visible = true
            startTimerLoop()

            StarterGui:SetCore("SendNotification", {
                Title = "👑 AXR 개발자 인증 성공",
                Text = "개발자 권한으로 로그인했습니다 (무제한)",
                Duration = 4
            })
            return
        else
            KeyInput.Text = ""
            KeyInput.PlaceholderText = "개발자 전용 유저만 사용할 수 있습니다!"
            KeyInput.PlaceholderColor3 = Color3.fromRGB(255, 100, 100)
            return
        end
    end

    -- 2. 타이머 키 검증 (5min.key / dop.8915)
    if TIMER_KEYS[inputKey] then
        activeDuration = TIMER_KEYS[inputKey]
        currentActiveKey = inputKey

        if not keyData[inputKey] then
            keyData[inputKey] = { Blacklisted = false, FirstUsedTime = nil }
        end

        local kInfo = keyData[inputKey]

        if kInfo.Blacklisted then
            KeyInput.Text = ""
            KeyInput.PlaceholderText = "만료된 키입니다! (블랙리스트 등록됨)"
            KeyInput.PlaceholderColor3 = Color3.fromRGB(255, 80, 80)
            return
        end

        -- 첫 입력 시간 기록
        if not kInfo.FirstUsedTime then
            kInfo.FirstUsedTime = os.time()
            saveKeyData(keyData)
        else
            local elapsed = os.time() - kInfo.FirstUsedTime
            if elapsed >= activeDuration then
                kInfo.Blacklisted = true
                saveKeyData(keyData)
                KeyInput.Text = ""
                KeyInput.PlaceholderText = "만료된 키입니다! (블랙리스트 등록됨)"
                KeyInput.PlaceholderColor3 = Color3.fromRGB(255, 80, 80)
                return
            end
        end

        KeyFrame:Destroy()
        MainFrame.Visible = true
        startTimerLoop()

        StarterGui:SetCore("SendNotification", {
            Title = "🌸 AXR 스크립트",
            Text = string.format("키 인증 성공! (%d분 제한)", math.floor(activeDuration / 60)),
            Duration = 3
        })
        return
    end

    KeyInput.Text = ""
    KeyInput.PlaceholderText = "유효하지 않은 키입니다!"
    KeyInput.PlaceholderColor3 = Color3.fromRGB(255, 100, 100)
end)

-- ==========================================
-- 4. 백그라운드 자동화 루프
-- ==========================================

-- 안티 AFK
task.spawn(function()
    while task.wait(15) do
        if getgenv().antiAfk then
            pcall(function()
                VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.Space, false, game)
                task.wait(0.1)
                VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.Space, false, game)
            end)
        end
    end
end)

-- 자동 낚시
task.spawn(function()
    while task.wait(0.1) do
        if getgenv().autoFish and Remotes then
            local click = Remotes:FindFirstChild("FishingClick")
            if click then pcall(function() click:FireServer() end) end
            
            local char = LocalPlayer.Character
            if char and char.PrimaryPart and (not getgenv().lastCast or tick() - getgenv().lastCast > 2) then
                getgenv().lastCast = tick()
                local scripted = Workspace:FindFirstChild("Scripted")
                local pondAreas = scripted and scripted:FindFirstChild("PondAreas")
                local pond = pondAreas and (pondAreas:FindFirstChild("PONDAREA1") or pondAreas:GetChildren()[1])
                if pond then
                    local reqStart = Remotes:FindFirstChild("FishingRequestStart")
                    if reqStart then pcall(function() reqStart:FireServer(pond, char.PrimaryPart.Position) end) end
                end
            end
        end
    end
end)

-- 자동 리버스
task.spawn(function()
    while task.wait(2) do
        if getgenv().autoRebirth and Remotes then
            local rebirth = Remotes:FindFirstChild("RebirthPurchase")
            if rebirth then pcall(function() rebirth:InvokeServer("Cash") end) end
        end
    end
end)

-- 자동 퀘스트 & 선물
task.spawn(function()
    while task.wait(5) do
        if Remotes then
            if getgenv().autoQuests then
                local q = Remotes:FindFirstChild("QuestClaimAll")
                if q then pcall(function() q:InvokeServer() end) end
            end
            if getgenv().autoGifts then
                local gift = Remotes:FindFirstChild("ClaimPlaytimeReward")
                if gift then
                    for i = 1, 16 do pcall(function() gift:InvokeServer(i) end) end
                end
            end
        end
    end
end)

-- 자동 상자 까기
local cases = {{"EpicCase", 40}, {"LegendaryCase", 40}, {"MythicalCase", 40}}
task.spawn(function()
    while task.wait(3) do
        if getgenv().autoCases and Remotes then
            local caseOpen = Remotes:FindFirstChild("CaseOpenRequest")
            if caseOpen then
                for _, c in pairs(cases) do
                    pcall(function() caseOpen:InvokeServer(c[1], c[2]) end)
                    task.wait(0.5)
                end
            end
        end
    end
end)

-- 자동 코드 입력
local codesList = {"Forgotten", "10KMEMBERS", "20KLIKES", "RELEASE", "UPDATE1"}
local claimed = false
task.spawn(function()
    while task.wait(5) do
        if getgenv().autoCodes and not claimed and Remotes then
            local redeem = Remotes:FindFirstChild("RedeemCode")
            if redeem then
                for _, c in pairs(codesList) do
                    pcall(function() redeem:InvokeServer(c) end)
                    task.wait(0.8)
                end
                claimed = true
            end
        end
    end
end)
