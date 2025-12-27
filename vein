-- AUTO BLOCKER NO GUI VERSION

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local VirtualUser = game:GetService("VirtualUser")

local LOCAL_PLAYER = Players.LocalPlayer

-- CONFIG
local ENABLED = true
local RADIUS = 11
local BLOCK_DELAY = 0 -- ms
local TOGGLE_KEY = Enum.KeyCode.B

-- SAFE ANIMATIONS
local SAFE_ANIMATIONS = {
    ["180426354"] = true,
    ["180435792"] = true,
    ["684969250"] = true,
    ["7950784441"] = true,
    ["12245963642"] = true,
    ["16398226659"] = true,
    ["6835294179"] = true,
    ["6835294624"] = true,
    ["6835295587"] = true,
    ["6835295340"] = true,
    ["6835295768"] = true,
    ["6843685401"] = true,
    ["6843686183"] = true,
    ["6843688241"] = true,
    ["6844129816"] = true,
    ["6844132951"] = true,
    ["6887306042"] = true,
    ["6891877544"] = true,
    ["9819726292"] = true,
    ["9819735529"] = true,
    ["9819745648"] = true,
    ["9819790668"] = true,
    ["9819806597"] = true,
    ["9819811478"] = true,
    ["9819823356"] = true,
    ["9819830644"] = true
}

-- AFK DO JOGO
local function isPlayerAFK()
    local char = LOCAL_PLAYER.Character
    if not char then return false end

    local info = char:FindFirstChild("Info")
    if not info then return false end

    local afk = info:FindFirstChild("AFK")
    if afk and afk:IsA("BoolValue") then
        return afk.Value
    end

    return false
end

local function normalizeAnimationId(id)
    return id:match("id=(%d+)") or id:match("%d+")
end

local function getDistance(player)
    local char = player.Character
    local localChar = LOCAL_PLAYER.Character
    if not (char and localChar) then return math.huge end

    local root = char:FindFirstChild("HumanoidRootPart")
    local localRoot = localChar:FindFirstChild("HumanoidRootPart")
    if not (root and localRoot) then return math.huge end

    return (root.Position - localRoot.Position).Magnitude
end

local function block(animTrack)
    if BLOCK_DELAY > 0 then
        task.wait(BLOCK_DELAY / 1000)
    end

    VirtualUser:Button2Down(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
    animTrack.Stopped:Wait()
    VirtualUser:Button2Up(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
end

local function handleAnimation(player, animTrack)
    if not ENABLED then return end
    if isPlayerAFK() then return end
    if player == LOCAL_PLAYER then return end
    if UserInputService:GetFocusedTextBox() then return end

    local animId = normalizeAnimationId(animTrack.Animation.AnimationId)
    if SAFE_ANIMATIONS[animId] then return end

    if getDistance(player) > RADIUS then return end

    task.spawn(function()
        block(animTrack)
    end)
end

local function trackPlayer(player)
    local function onCharacter(character)
        local humanoid = character:WaitForChild("Humanoid")
        local animator = humanoid:WaitForChild("Animator")

        animator.AnimationPlayed:Connect(function(track)
            handleAnimation(player, track)
        end)
    end

    if player.Character then
        onCharacter(player.Character)
    end

    player.CharacterAdded:Connect(onCharacter)
end

-- TOGGLE KEY
UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == TOGGLE_KEY then
        ENABLED = not ENABLED
        warn("AutoBlock:", ENABLED and "ON" or "OFF")
    end
end)

-- INIT
for _, plr in ipairs(Players:GetPlayers()) do
    trackPlayer(plr)
end

Players.PlayerAdded:Connect(trackPlayer)
