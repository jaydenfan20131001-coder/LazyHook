# My Script

```lua
-- your full script here
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local HRP = Character:WaitForChild("HumanoidRootPart")

-- ==================== CONFIG ====================
local config = {
    voidActive = false,
    fakeDesyncActive = false,
    meowDesyncActive = false,
    projRageActive = false,
    meleeRageActive = false,
    slingActive = false,

    entropyFactor = 80,
    bypassStrength = 75,
    iterationDelay = 120,
    voidStabilizer = true,
}

local connections = {}
local lastReset = 0
local originalCFrame = nil

-- ==================== LOAD RAYFIELD ====================
local Rayfield = loadstring(game:HttpGet("https://sirius.menu/rayfield"))()

local Window = Rayfield:CreateWindow({
    Name = "Deathness UE V4.5 🔥 Advanced",
    LoadingTitle = "Deathness UE",
    LoadingSubtitle = "by L4·deathness",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "DeathnessUE",
        FileName = "V45_Config"
    },
})

-- Force open after load
task.delay(1.5, function()
    pcall(function() Window:Open() end)
end)

-- ==================== TABS ====================
local VoidTab = Window:CreateTab("Void", 4483362458)
local AdvTab = Window:CreateTab("Advanced", 6031097228)
local CombatTab = Window:CreateTab("Combat", 6026568198)

-- ==================== VOID TAB ====================
VoidTab:CreateSection("Desync Systems")

VoidTab:CreateToggle({
    Name = "Void Protocol",
    CurrentValue = config.voidActive,
    Flag = "VoidToggle",
    Callback = function(Value)
        config.voidActive = Value
        UpdateSystems()
    end,
})

VoidTab:CreateToggle({
    Name = "Fake Desync",
    CurrentValue = config.fakeDesyncActive,
    Flag = "FakeToggle",
    Callback = function(Value)
        config.fakeDesyncActive = Value
        UpdateSystems()
    end,
})

VoidTab:CreateToggle({
    Name = "Meow Desync",
    CurrentValue = config.meowDesyncActive,
    Flag = "MeowToggle",
    Callback = function(Value)
        config.meowDesyncActive = Value
        UpdateSystems()
    end,
})

-- ==================== ADVANCED TAB ====================
AdvTab:CreateSection("Settings")

AdvTab:CreateSlider({
    Name = "Entropy Factor",
    Range = {0, 100},
    Increment = 1,
    CurrentValue = config.entropyFactor,
    Callback = function(Value)
        config.entropyFactor = Value
    end,
})

AdvTab:CreateSlider({
    Name = "Bypass Strength",
    Range = {0, 100},
    Increment = 1,
    CurrentValue = config.bypassStrength,
    Callback = function(Value)
        config.bypassStrength = Value
    end,
})

AdvTab:CreateSlider({
    Name = "Iteration Delay (ms)",
    Range = {50, 500},
    Increment = 10,
    CurrentValue = config.iterationDelay,
    Callback = function(Value)
        config.iterationDelay = Value
    end,
})

AdvTab:CreateToggle({
    Name = "Void Stabilizer",
    CurrentValue = config.voidStabilizer,
    Callback = function(Value)
        config.voidStabilizer = Value
    end,
})

-- ==================== COMBAT TAB ====================
CombatTab:CreateSection("Combat Features")

CombatTab:CreateToggle({
    Name = "Projectile Rage",
    CurrentValue = config.projRageActive,
    Callback = function(Value)
        config.projRageActive = Value
    end,
})

CombatTab:CreateToggle({
    Name = "Melee Rage",
    CurrentValue = config.meleeRageActive,
    Callback = function(Value)
        config.meleeRageActive = Value
    end,
})

-- ==================== CORE VOID LOGIC ====================
local function SaveOriginal()
    if HRP then originalCFrame = HRP.CFrame end
end

local function ApplyVoid()
    if not (config.voidActive or config.fakeDesyncActive or config.meowDesyncActive) then return end

    local now = tick()
    if now - lastReset < (config.iterationDelay / 1000) then return end
    lastReset = now

    SaveOriginal()

    local strength = config.bypassStrength / 100
    local radius = 48000000 * strength

    local offset = Vector3.new(
        math.random(-radius, radius) * 0.65,
        18000000 + math.random(5000000, 40000000),
        math.random(-radius, radius) * 0.65
    )

    if HRP then
        HRP.CFrame = HRP.CFrame + offset

        if config.voidStabilizer then
            task.delay(0.07, function()
                if HRP and originalCFrame then
                    HRP.CFrame = originalCFrame
                end
            end)
        end
    end
end

function UpdateSystems()
    -- Disconnect old
    for _, v in pairs(connections) do v:Disconnect() end
    connections = {}

    if config.voidActive or config.fakeDesyncActive or config.meowDesyncActive then
        connections.main = RunService.Heartbeat:Connect(ApplyVoid)
    end

    Rayfield:Notify({
        Title = "Deathness UE",
        Content = "Settings Applied",
        Duration = 2.5
    })
end

-- Respawn Support
LocalPlayer.CharacterAdded:Connect(function(new)
    Character = new
    HRP = new:WaitForChild("HumanoidRootPart")
end)

-- ==================== START ====================
print("✅ Deathness UE V4.5 Advanced Loaded!")
Rayfield:Notify({
    Title = "Success",
    Content = "Press Right Shift to open the menu",
    Duration = 8
})
