# nxb-loader
NXB Auth System Loader
nxb-loader
NXB Auth System Loader
🏠 Diesel N Steel Script
📋 Description
Advanced script hub for Diesel N Steel game with auto-farm features, teleportation, and troll options.
Made by ∅ | NXB SCRIPTERS
✨ Features
🪙 Coin System
Auto Duplicate Coin - Automatically duplicate your coins with 20x burst rate
Works only when you're inside your jeep
⭐ EXP System
Auto Farm EXP - 100x multiplier for fast leveling
Automatic teleportation to optimal farming location
🚗 Main Controls
Free Cam - Activate free camera mode
Delete NPC Cars - Remove all AI vehicles from the map
Discord Support - Quick access to community discord
🎯 Teleportation
Drop Off Locations:
Guiguinto Drop Off
Bulakan Drop Off
Malolos Drop Off
Balagtas Drop Off
Terminal Points:
Balagtas Terminal
Guiguinto Terminal
Malolos Terminal
Bulakan Terminal
😈 Troll Features
Jeep Fling Troll - Fling your jeep into other players
Target Specific Player - Fling a specific player by username
🌐 Server Utilities
Server Hop - Join a different server
Rejoin Server - Reconnect to current server
Find Low Player Server - Auto-join servers with fewer players
🚀 Installation
Copy the script using the loadstring below
Open your executor (Solara, Wave, Arceus X, etc.)
Paste and execute!
💻 Loadstring
loadstring(game:HttpGet("https://raw.githubusercontent.com/YOUR-USERNAME/nxb-loader/main/script.lua"))()
📜 Full Script
-- Load UI Library
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/x2zu/OPEN-SOURCE-UI-ROBLOX/refs/heads/main/X2ZU%20UI%20ROBLOX%20OPEN%20SOURCE/DummyUi-leak-by-x2zu/fetching-main/Tools/Framework.luau"))()

-- Services
local Players = game:GetService("Players")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer

-- Create Window
local Window = Library:Window({
    Title = "NXB SCRIPTERS | DIESEL N STEEL",
    Desc = "MADE BY ∅",
    Icon = 85428929108639,
    Theme = "Dark",
    Config = {Keybind = Enum.KeyCode.LeftControl, Size = UDim2.new(0, 450, 0, 300)},
    CloseUIButton = {Enabled = true, Text = "NXB"}
})

-- ============================================
-- UTILITY FUNCTIONS
-- ============================================
local function getPlayerJeep()
    local jeeps = workspace:FindFirstChild("Jeepnies")
    if not jeeps then return nil end
    return jeeps:FindFirstChild(player.Name)
end

local function isPlayerInJeep()
    local char = player.Character
    if not char then return false end
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not hum then return false end
    return hum.Sit and hum.SeatPart and hum.SeatPart:IsA("VehicleSeat")
end

local function teleportTo(point)
    local char = player.Character or player.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart")
    local pos = point.Position + Vector3.new(0, 5, 0)
    local hum = char:FindFirstChildOfClass("Humanoid")
    
    -- If in jeep, teleport the jeep too
    if hum and hum.Sit and hum.SeatPart and hum.SeatPart:IsA("VehicleSeat") then
        local model = hum.SeatPart:FindFirstAncestorOfClass("Model")
        if model and model.PrimaryPart then
            model:SetPrimaryPartCFrame(CFrame.new(pos))
            return
        end
    end
    
    hrp.CFrame = CFrame.new(pos)
end

-- ============================================
-- MAIN TAB
-- ============================================
local MainTab = Window:Tab({Title = "Main", Icon = "home"})
MainTab:Section({Title = "Main Controls"})

MainTab:Button({
    Title = "Discord Link",
    Desc = "Copy Discord invite link",
    Callback = function()
        setclipboard("https://discord.gg/TDSAbQvyK")
        Window:Notify({Title = "Main", Desc = "Discord link copied!", Time = 3})
    end
})

MainTab:Button({
    Title = "Free Cam",
    Desc = "Activate free camera",
    Callback = function()
        loadstring(game:HttpGet("https://pastefy.app/xfVluu2u/raw"))()
        Window:Notify({Title = "Main", Desc = "Free Cam Activated!", Time = 3})
    end
})

MainTab:Button({
    Title = "DELETE NPC CARS",
    Desc = "Remove all NPC vehicles",
    Callback = function()
        local folder = workspace:FindFirstChild("AiVehicles")
        if not folder then
            Window:Notify({Title = "Main", Desc = "No AiVehicles folder found!", Time = 3})
            return
        end
        local count = #folder:GetChildren()
        folder:ClearAllChildren()
        Window:Notify({Title = "Main", Desc = "✅ Removed "..count.." AI vehicles!", Time = 3})
    end
})

-- ============================================
-- COIN TAB
-- ============================================
local CoinTab = Window:Tab({Title = "Coin", Icon = "coins"})
CoinTab:Section({Title = "Coin System"})

local autoFarmCoin = false
local coinBurstAmount = 20

CoinTab:Toggle({
    Title = "Auto Duplicate Coin",
    Desc = "Duplicate your coins automatically",
    Value = false,
    Callback = function(enabled)
        autoFarmCoin = enabled
        if enabled then
            Window:Notify({Title = "Coin", Desc = "Auto Duplicate started!", Time = 3})
            task.spawn(function()
                local remote = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("RecieveCoin")
                
                while autoFarmCoin do
                    local jeep = getPlayerJeep()
                    if jeep and isPlayerInJeep() then
                        local args = {
                            [1] = {
                                ["Value"] = 300,
                                ["Password"] = 351710667,
                                ["Main"] = true,
                                ["PassengerValues"] = jeep:FindFirstChild("PassengerValues")
                            }
                        }
                        -- Burst fire for speed
                        for i = 1, coinBurstAmount do
                            pcall(function()
                                remote:FireServer(unpack(args))
                            end)
                        end
                    end
                    RunService.Heartbeat:Wait()
                end
            end)
        else
            Window:Notify({Title = "Coin", Desc = "Auto Duplicate stopped", Time = 3})
        end
    end
})

-- ============================================
-- EXP TAB
-- ============================================
local ExpTab = Window:Tab({Title = "EXP", Icon = "bar-chart"})
local expEnabled = false
local hasTeleportedForExp = false

ExpTab:Toggle({
    Title = "Auto Farm EXP",
    Desc = "Automatically generate EXP (100x multiplier)",
    Default = false,
    Callback = function(state)
        expEnabled = state
        if expEnabled then
            Window:Notify({Title="EXP", Desc="Auto Farm EXP: ENABLED", Time=3})
            -- Teleport once to drop point
            if not hasTeleportedForExp then
                hasTeleportedForExp = true
                local dropPoint = workspace.Map.Misc.PassengerSpawnPoints["Guiguinto - Bulakan"].BulakanTerminalDropPoint
                teleportTo(dropPoint)
            end
            -- Install new metatable hook
            local settings = {repeatamount = 100, exceptions = {"SayMessageRequest"}}
            local mt = getrawmetatable(game)
            local old = mt.__namecall
            setreadonly(mt, false)
            mt.__namecall = function(uh, ...)
                local args = {...}
                local method = getnamecallmethod()
                for i,o in next, settings.exceptions do
                    if uh.Name == o then
                        return old(uh, ...)
                    end
                end
                if method == "FireServer" or method == "InvokeServer" then
                    for i = 50, settings.repeatamount do
                        old(uh, ...)
                    end
                end
                return old(uh, ...)
            end
            setreadonly(mt, true)
        else
            Window:Notify({Title="EXP", Desc="Auto Farm EXP: DISABLED", Time=3})
            hasTeleportedForExp = false
        end
    end
})

-- ============================================
-- TROLL TAB
-- ============================================
local TrollTab = Window:Tab({Title = "Troll", Icon = "alert-triangle"})
TrollTab:Section({Title = "Troll Features"})

local trollLoopActive = false
local targetPlayerName = ""

TrollTab:Toggle({
    Title = "Jeep Fling Troll",
    Desc = "Fling your jeep into other players",
    Value = false,
    Callback = function(enabled)
        trollLoopActive = enabled
        
        if enabled then
            Window:Notify({Title="Troll", Desc="Jeep Fling: ACTIVE", Time=3})
            
            task.spawn(function()
                while trollLoopActive do
                    local myJeep = getPlayerJeep()
                    if not myJeep or not myJeep.PrimaryPart then 
                        task.wait(1)
                        continue 
                    end
                    local jeepsFolder = workspace:FindFirstChild("Jeepnies")
                    if not jeepsFolder then 
                        task.wait(1)
                        continue 
                    end
                    -- Target all other jeeps
                    for _, plr in pairs(Players:GetPlayers()) do
                        if plr ~= player then
                            local targetJeep = jeepsFolder:FindFirstChild(plr.Name)
                            if targetJeep and targetJeep.PrimaryPart then
                                local targetCF = targetJeep.PrimaryPart.CFrame
                                
                                -- Position behind target
                                myJeep.PrimaryPart.CFrame = targetCF * CFrame.new(0, 0, -8)
                                
                                -- Apply force
                                local bv = Instance.new("BodyVelocity")
                                bv.MaxForce = Vector3.new(1e5, 1e5, 1e5)
                                bv.Velocity = (targetCF.Position - myJeep.PrimaryPart.Position).Unit * 300 + Vector3.new(0, 150, 0)
                                bv.Parent = targetJeep.PrimaryPart
                                game:GetService("Debris"):AddItem(bv, 0.5)
                            end
                        end
                    end
                    
                    task.wait(0.5)
                end
            end)
        else
            Window:Notify({Title="Troll", Desc="Jeep Fling: DISABLED", Time=3})
        end
    end
})

TrollTab:Textbox({
    Title = "Target Player Name",
    Desc = "Enter username of player to target",
    Placeholder = "PlayerName",
    Value = "",
    ClearTextOnFocus = false,
    Callback = function(text) 
        targetPlayerName = text 
    end
})

TrollTab:Button({
    Title = "Fling Specific Player",
    Desc = "Fling only the targeted player",
    Callback = function()
        local myJeep = getPlayerJeep()
        if not myJeep then 
            Window:Notify({Title="Troll", Desc="You don't have a jeep!", Time=3}) 
            return 
        end
        
        local targetPlayer = Players:FindFirstChild(targetPlayerName)
        if not targetPlayer then 
            Window:Notify({Title="Troll", Desc="Player not found!", Time=3}) 
            return 
        end
        
        local jeepsFolder = workspace:FindFirstChild("Jeepnies")
        local targetJeep = jeepsFolder and jeepsFolder:FindFirstChild(targetPlayer.Name)
        if not targetJeep or not targetJeep.PrimaryPart then 
            Window:Notify({Title="Troll", Desc="Target has no jeep!", Time=3}) 
            return 
        end
        
        local targetCF = targetJeep.PrimaryPart.CFrame
        myJeep.PrimaryPart.CFrame = targetCF * CFrame.new(0, 0, -8)
        
        local bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(1e5, 1e5, 1e5)
        bv.Velocity = (targetCF.Position - myJeep.PrimaryPart.Position).Unit * 300 + Vector3.new(0, 150, 0)
        bv.Parent = targetJeep.PrimaryPart
        game:GetService("Debris"):AddItem(bv, 0.5)
        
        Window:Notify({Title="Troll", Desc="Flung " .. targetPlayerName .. "!", Time=3})
    end
})

-- ============================================
-- TELEPORT TAB
-- ============================================
local TeleportTab = Window:Tab({Title = "Teleport", Icon = "map-pin"})
TeleportTab:Section({Title = "Drop Off Locations"})

local dropOffs = {
    ["Guiguinto Drop Off"] = workspace.Map.Misc.PassengerSpawnPoints["Guiguinto - Bulakan"].GuiguintoTerminalDropPoint,
    ["Bulakan Drop Off"] = workspace.Map.Misc.PassengerSpawnPoints["Malolos - Bulakan"].BulakanTerminalDropPoint,
    ["Malolos Drop Off"] = workspace.Map.Misc.PassengerSpawnPoints["Malolos - Bulakan"].MalolosTerminalDropPoint,
    ["Balagtas Drop Off"] = workspace.Map.Misc.PassengerSpawnPoints["Balagtas - Bulakan"].BalagtasTerminalDropPoint
}

local dropNames = {}
for name in pairs(dropOffs) do
    table.insert(dropNames, name)
end

TeleportTab:Dropdown({
    Title = "Select Drop Off",
    List = dropNames,
    Value = dropNames[1],
    Callback = function(choice)
        teleportTo(dropOffs[choice])
        Window:Notify({Title = "Teleport", Desc = "Teleported to "..choice, Time = 3})
    end
})

TeleportTab:Section({Title = "Terminal Load Points"})

local terminals = {
    ["Balagtas Terminal"] = workspace.Map.Misc.TerminalParts["Balagtas - Bulakan"].ToBalagtasTerminalLoadPoint,
    ["Guiguinto Terminal"] = workspace.Map.Misc.TerminalParts["Guiguinto - Bulakan"].ToGuiguintoTerminalLoadPoint,
    ["Malolos Terminal"] = workspace.Map.Misc.TerminalParts["Malolos - Bulakan"].ToMalolosTerminalLoadPoint,
    ["Bulakan Terminal"] = workspace.Map.Misc.TerminalParts["Malolos - Bulakan"].ToBulakanTerminalLoadPoint
}

local terminalNames = {}
for name in pairs(terminals) do
    table.insert(terminalNames, name)
end

TeleportTab:Dropdown({
    Title = "Select Terminal",
    List = terminalNames,
    Value = terminalNames[1],
    Callback = function(choice)
        teleportTo(terminals[choice])
        Window:Notify({Title = "Teleport", Desc = "Teleported to "..choice, Time = 3})
    end
})

-- ============================================
-- SERVER TAB
-- ============================================
local ServerTab = Window:Tab({Title = "Server", Icon = "server"})
ServerTab:Section({Title="Server Utilities"})

_G.LastServerJobId = _G.LastServerJobId or game.JobId

ServerTab:Button({
    Title="Server Hop", 
    Desc="Join a different server", 
    Callback=function()
        _G.LastServerJobId = game.JobId
        TeleportService:Teleport(game.PlaceId)
        Window:Notify({Title="Server", Desc="Hopping servers...", Time=3})
    end
})

ServerTab:Button({
    Title="Rejoin Server", 
    Desc="Rejoin current server", 
    Callback=function()
        TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId)
        Window:Notify({Title="Server", Desc="Rejoining...", Time=3})
    end
})

ServerTab:Button({
    Title="Find Low Player Server", 
    Desc="Join server with fewer players", 
    Callback=function()
        local success, servers = pcall(function()
            return HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?sortOrder=Asc&limit=100"))
        end)
        
        if success and servers and servers.data then
            local targetServer
            for _, server in ipairs(servers.data) do
                if server.id ~= game.JobId and server.playing < 10 then
                    targetServer = server.id
                    break
                end
            end
            
            if targetServer then
                TeleportService:TeleportToPlaceInstance(game.PlaceId, targetServer)
                Window:Notify({Title="Server", Desc="Joining low player server...", Time=3})
            else
                Window:Notify({Title="Server", Desc="No suitable server found", Time=3})
            end
        else
            Window:Notify({Title="Server", Desc="Failed to fetch servers", Time=3})
        end
    end
})
⚙️ Keybind
Default Toggle Key: Left Control
⚠️ Requirements
Must have a Jeep to use Coin and some Troll features
Executor with HttpGet and metatable support
🔗 Links
Discord: https://discord.gg/TDSAbQvyK
📝 Notes
Use responsibly
Some features require you to be inside your jeep
Troll features may get you kicked if abused
© 2024 NXB SCRIPTERS | All Rights Reserved
