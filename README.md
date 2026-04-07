local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer


-- CARGA DE LIBRERÃA
local library = loadstring(game:HttpGet("https://raw.githubusercontent.com/IJNTJNNPTJ/farm/refs/heads/main/.PH", true))()

-- VENTANA PRINCIPAL
local window = library:AddWindow("âš ï¸ Toxic PAID V2 Deobfuscated By 228 âš ï¸", {
    main_color = Color3.fromRGB(34, 56, 34), 
    min_size = Vector2.new(600, 820),
    can_resize = false,
})

local AutoFarm = window:AddTab("Farm OP")
AutoFarm:Show()


-- 1ï¸âƒ£ Switch: Fuerza OP
AutoFarm:AddSwitch("Fuerza OP", function(state)
    getgenv()._AutoRepFarmEnabled = state
    warn("[Auto Rep Farm] Estado cambiado a:", state and "ON" or "OFF")
end)

-- âœ… ConfiguraciÃ³n optimizada
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")
local Stats = game:GetService("Stats")
local LocalPlayer = Players.LocalPlayer

local PET_NAME = "Swift Samurai"
local ROCK_NAME = "Rock5M"
local PROTEIN_EGG_NAME = "ProteinEgg"
local PROTEIN_EGG_INTERVAL = 30 * 60 -- 30 min
local REPS_PER_CYCLE = 180
local REP_DELAY = 0.01
local ROCK_INTERVAL = 1
local MAX_PING = 1100

local HumanoidRootPart
local lastProteinEggTime = 0
local lastRockTime = 0
local RockRef = workspace:FindFirstChild(ROCK_NAME)

local function getPing()
    local success, ping = pcall(function()
        return Stats.Network.ServerStatsItem["Data Ping"]:GetValue()
    end)
    return success and ping or 999
end

local function updateCharacterRefs()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    HumanoidRootPart = character:WaitForChild("HumanoidRootPart", 5)
end

local function equipPet()
    local petsFolder = LocalPlayer:FindFirstChild("petsFolder")
    if petsFolder and petsFolder:FindFirstChild("Unique") then
        for _, pet in pairs(petsFolder.Unique:GetChildren()) do
            if pet.Name == PET_NAME then
                ReplicatedStorage.rEvents.equipPetEvent:FireServer("equipPet", pet)
                break
            end
        end
    end
end

local function eatProteinEgg()
    if LocalPlayer:FindFirstChild("Backpack") then
        for _, item in pairs(LocalPlayer.Backpack:GetChildren()) do
            if item.Name == PROTEIN_EGG_NAME then
                ReplicatedStorage.rEvents.eatEvent:FireServer("eat", item)
                break
            end
        end
    end
end

local function hitRock()
    if not RockRef or not RockRef.Parent then
        RockRef = workspace:FindFirstChild(ROCK_NAME)
    end
    if RockRef and HumanoidRootPart then
        HumanoidRootPart.CFrame = RockRef.CFrame * CFrame.new(0, 0, -5)
        ReplicatedStorage.rEvents.hitEvent:FireServer("hit", RockRef)
    end
end

-- âœ… Loop principal
if not getgenv()._AutoRepFarmLoop then
    getgenv()._AutoRepFarmLoop = true

    task.spawn(function()
        updateCharacterRefs()
        equipPet()
        lastProteinEggTime = tick()
        lastRockTime = tick()

        while true do
            if getgenv()._AutoRepFarmEnabled then
                local ping = getPing()
                if ping > MAX_PING then
                    warn("[Auto Rep Farm] Ping alto ("..math.floor(ping).."ms), pausando 5s...")
                    task.wait(5)
                else
                    if LocalPlayer:FindFirstChild("muscleEvent") then
                        for i = 1, REPS_PER_CYCLE do
                            LocalPlayer.muscleEvent:FireServer("rep")
                        end
                    end

                    if tick() - lastProteinEggTime >= PROTEIN_EGG_INTERVAL then
                        eatProteinEgg()
                        lastProteinEggTime = tick()
                    end

                    if tick() - lastRockTime >= ROCK_INTERVAL then
                        hitRock()
                        lastRockTime = tick()
                    end

                    task.wait(REP_DELAY)
                end
            else
                task.wait(1)
            end
        end
    end)
end



-- 2ï¸âƒ£ Switch: Eat Egg (30 Min)
local autoEatEnabled = false
local function eatProteinEggNew()
    local player = game.Players.LocalPlayer
    local backpack = player:WaitForChild("Backpack")
    local character = player.Character or player.CharacterAdded:Wait()

    local egg = backpack:FindFirstChild("Protein Egg")
    if egg then
        egg.Parent = character
        pcall(function()
            egg:Activate()
        end)
        print("[AutoEgg] Protein Egg consumido.")
    else
        warn("[AutoEgg] No se encontrÃ³ Protein Egg en Backpack.")
    end
end

task.spawn(function()
    while true do
        if autoEatEnabled then
            eatProteinEggNew()
            task.wait(1800)
        else
            task.wait(1)
        end
    end
end)

AutoFarm:AddSwitch("Eat Egg (30 Min)", function(state)
    autoEatEnabled = state
    print(state and "[AutoEgg] Activado." or "[AutoEgg] Desactivado.")
end)


-- 4ï¸âƒ£ Switch: Anti Lag
AutoFarm:AddSwitch("Anti Lag", function(State)
    local lighting = game:GetService("Lighting")
    if State then
        for _, gui in pairs(LocalPlayer.PlayerGui:GetChildren()) do
            if gui:IsA("ScreenGui") then gui:Destroy() end
        end
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj:IsA("ParticleEmitter") or obj:IsA("PointLight") or obj:IsA("SpotLight") or obj:IsA("SurfaceLight") then
                obj:Destroy()
            end
        end
        for _, v in pairs(lighting:GetChildren()) do
            if v:IsA("Sky") then v:Destroy() end
        end
        local darkSky = Instance.new("Sky")
        darkSky.Name = "DarkSky"
        darkSky.SkyboxBk = "rbxassetid://0"
        darkSky.SkyboxDn = "rbxassetid://0"
        darkSky.SkyboxFt = "rbxassetid://0"
        darkSky.SkyboxLf = "rbxassetid://0"
        darkSky.SkyboxRt = "rbxassetid://0"
        darkSky.SkyboxUp = "rbxassetid://0"
        darkSky.Parent = lighting
        lighting.Brightness = 0
        lighting.ClockTime = 0
        lighting.TimeOfDay = "00:00:00"
        lighting.OutdoorAmbient = Color3.new(0,0,0)
        lighting.Ambient = Color3.new(0,0,0)
        lighting.FogColor = Color3.new(0,0,0)
        lighting.FogEnd = 100
        task.spawn(function()
            while State do
                task.wait(5)
                if not lighting:FindFirstChild("DarkSky") then
                    darkSky:Clone().Parent = lighting
                end
            end
        end)
    end
end)


-- ðŸ”¹ SWITCH PARA ACTIVAR/DESACTIVAR EL ANTI AFK
AutoFarm:AddSwitch("Anti AFK", function(state)
    if state then
        -- âœ… ACTIVAR ANTI AFK
        if getgenv().AntiAfkExecuted and thisoneissocoldww then 
            getgenv().AntiAfkExecuted = false
            getgenv().zamanbaslaticisi = false
            game.CoreGui.thisoneissocoldww:Destroy()
        end
        getgenv().AntiAfkExecuted = true

        local thisoneissocoldww = Instance.new("ScreenGui")
        local madebybloodofbatus = Instance.new("Frame")
        local UICornerw = Instance.new("UICorner")
        local DestroyButton = Instance.new("TextButton")
        local uselesslabelone = Instance.new("TextLabel")
        local timerlabel = Instance.new("TextLabel")
        local uselesslabeltwo = Instance.new("TextLabel")
        local fpslabel = Instance.new("TextLabel")
        local uselesslabelthree = Instance.new("TextLabel")
        local pinglabel = Instance.new("TextLabel")
        local uselessframeone = Instance.new("Frame")
        local UICornerww = Instance.new("UICorner")
        local uselesslabelfour = Instance.new("TextLabel")

        thisoneissocoldww.Name = "thisoneissocoldww"
        thisoneissocoldww.Parent = game.CoreGui
        thisoneissocoldww.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

        madebybloodofbatus.Name = "madebybloodofbatus"
        madebybloodofbatus.Parent = thisoneissocoldww
        madebybloodofbatus.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        madebybloodofbatus.Position = UDim2.new(0.085,0,0.13,0)
        madebybloodofbatus.Size = UDim2.new(0,225,0,96)
        UICornerw.Parent = madebybloodofbatus

        DestroyButton.Name = "DestroyButton"
        DestroyButton.Parent = madebybloodofbatus
        DestroyButton.BackgroundTransparency = 1
        DestroyButton.Position = UDim2.new(0.87,0,0.02,0)
        DestroyButton.Size = UDim2.new(0,27,0,15)
        DestroyButton.Font = Enum.Font.SourceSans
        DestroyButton.Text = "X"
        DestroyButton.TextColor3 = Color3.fromRGB(255,255,255)
        DestroyButton.TextSize = 14
        DestroyButton.MouseButton1Click:Connect(function()
            getgenv().AntiAfkExecuted = false
            wait(0.1)
            thisoneissocoldww:Destroy()
        end)

        uselesslabelone.Parent = madebybloodofbatus
        uselesslabelone.BackgroundTransparency = 1
        uselesslabelone.Position = UDim2.new(0.3,0,0,0)
        uselesslabelone.Size = UDim2.new(0,95,0,24)
        uselesslabelone.Font = Enum.Font.SourceSans
        uselesslabelone.Text = "Anti Afk sacado de chat gpt de rafaelðŸ˜‚"
        uselesslabelone.TextColor3 = Color3.fromRGB(255,255,255)
        uselesslabelone.TextSize = 14

        timerlabel.Parent = madebybloodofbatus
        timerlabel.BackgroundTransparency = 1
        timerlabel.Position = UDim2.new(0.65,0,0.68,0)
        timerlabel.Size = UDim2.new(0,60,0,24)
        timerlabel.Font = Enum.Font.SourceSans
        timerlabel.Text = "0:0:0"
        timerlabel.TextColor3 = Color3.fromRGB(255,255,255)
        timerlabel.TextSize = 14

        uselesslabeltwo.Parent = madebybloodofbatus
        uselesslabeltwo.BackgroundTransparency = 1
        uselesslabeltwo.Position = UDim2.new(0.03,0,0.37,0)
        uselesslabeltwo.Size = UDim2.new(0,29,0,24)
        uselesslabeltwo.Font = Enum.Font.SourceSans
        uselesslabeltwo.Text = "Ping: "
        uselesslabeltwo.TextColor3 = Color3.fromRGB(255,255,255)
        uselesslabeltwo.TextSize = 14

        fpslabel.Parent = madebybloodofbatus
        fpslabel.BackgroundTransparency = 1
        fpslabel.Position = UDim2.new(0.72,0,0.35,0)
        fpslabel.Size = UDim2.new(0,55,0,24)
        fpslabel.Font = Enum.Font.SourceSans
        fpslabel.Text = "0"
        fpslabel.TextColor3 = Color3.fromRGB(255,255,255)
        fpslabel.TextSize = 14

        uselesslabelthree.Parent = madebybloodofbatus
        uselesslabelthree.BackgroundTransparency = 1
        uselesslabelthree.Position = UDim2.new(0.5,0,0.35,0)
        uselesslabelthree.Size = UDim2.new(0,26,0,24)
        uselesslabelthree.Font = Enum.Font.SourceSans
        uselesslabelthree.Text = "Fps: "
        uselesslabelthree.TextColor3 = Color3.fromRGB(255,255,255)
        uselesslabelthree.TextSize = 14

        pinglabel.Parent = madebybloodofbatus
        pinglabel.BackgroundTransparency = 1
        pinglabel.Position = UDim2.new(0.2,0,0.37,0)
        pinglabel.Size = UDim2.new(0,55,0,24)
        pinglabel.Font = Enum.Font.SourceSans
        pinglabel.Text = "0"
        pinglabel.TextColor3 = Color3.fromRGB(255,255,255)
        pinglabel.TextSize = 14
        pinglabel.TextWrapped = true

        uselessframeone.Parent = madebybloodofbatus
        uselessframeone.BackgroundColor3 = Color3.fromRGB(255,255,255)
        uselessframeone.Position = UDim2.new(0.004,0,0.24,0)
        uselessframeone.Size = UDim2.new(0,224,0,5)
        UICornerww.CornerRadius = UDim.new(0,50)
        UICornerww.Parent = uselessframeone

        uselesslabelfour.Parent = madebybloodofbatus
        uselesslabelfour.BackgroundTransparency = 1
        uselesslabelfour.Position = UDim2.new(0.05,0,0.81,0)
        uselesslabelfour.Size = UDim2.new(0,95,0,12)
        uselesslabelfour.Font = Enum.Font.SourceSans
        uselesslabelfour.Text = "Anti-Afk Auto Enabled"
        uselesslabelfour.TextColor3 = Color3.fromRGB(255,255,255)
        uselesslabelfour.TextSize = 14

        -- ðŸ§² Draggable UI
        local Drag = madebybloodofbatus
        local gsTween = game:GetService("TweenService")
        local UserInputService = game:GetService("UserInputService")
        local dragging, dragInput, dragStart, startPos
        local function update(input)
            local delta = input.Position - dragStart
            local dragTime = 0.04
            local SmoothDrag = {}
            SmoothDrag.Position = UDim2.new(startPos.X.Scale,startPos.X.Offset+delta.X,startPos.Y.Scale,startPos.Y.Offset+delta.Y)
            gsTween:Create(Drag,TweenInfo.new(dragTime,Enum.EasingStyle.Sine,Enum.EasingDirection.InOut),SmoothDrag):Play()
        end
        Drag.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = true
                dragStart = input.Position
                startPos = Drag.Position
                input.Changed:Connect(function()
                    if input.UserInputState == Enum.UserInputState.End then dragging = false end
                end)
            end
        end)
        Drag.InputChanged:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
                dragInput = input
            end
        end)
        UserInputService.InputChanged:Connect(function(input)
            if input == dragInput and dragging then update(input) end
        end)

        -- ðŸ’¤ Anti-AFK Core
        local vu = game:GetService('VirtualUser')
        game.Players.LocalPlayer.Idled:Connect(function()
            vu:CaptureController()
            vu:ClickButton2(Vector2.new())
        end)

        -- âš¡ FPS & Ping
        local RunService = game:GetService("RunService")
        local sec = tick()
        local FPS = {}
        RunService.RenderStepped:Connect(function()
            local fr = tick()
            for i=#FPS,1,-1 do FPS[i+1] = (FPS[i]>=fr-1) and FPS[i] or nil end
            FPS[1] = fr
            local fps = math.floor((tick()-sec>=1 and #FPS) or (#FPS/(tick()-sec)))
            fpslabel.Text = fps
        end)

        spawn(function()
            while getgenv().AntiAfkExecuted do
                wait(1)
                local ping = math.floor(tonumber(game:GetService("Stats"):FindFirstChild("PerformanceStats").Ping:GetValue()))
                pinglabel.Text = ping
            end
        end)

        -- â±ï¸ Timer
        local saniye, dakika, saat = 0,0,0
        getgenv().zamanbaslaticisi = true
        spawn(function()
            while getgenv().zamanbaslaticisi do
                saniye = saniye + 1
                wait(1)
                if saniye>=60 then saniye=0;dakika=dakika+1 end
                if dakika>=60 then dakika=0;saat=saat+1 end
                timerlabel.Text = saat..":"..dakika..":"..saniye
            end
        end)
    else
        -- âŒ DESACTIVAR ANTI AFK
        getgenv().AntiAfkExecuted = false
        getgenv().zamanbaslaticisi = false
        if game.CoreGui:FindFirstChild("thisoneissocoldww") then
            game.CoreGui.thisoneissocoldww:Destroy()
        end
    end
end)


-- ðŸŽ° BotÃ³n: Girar Ruleta
AutoFarm:AddButton("Auto Ruleta", function(state)
    _G.AutoSpinWheel = state
    
    if state then
        spawn(function()
            while _G.AutoSpinWheel and task.wait(0.1) do
                game:GetService("ReplicatedStorage").rEvents.openFortuneWheelRemote:InvokeServer(
                    "openFortuneWheel",
                    game:GetService("ReplicatedStorage").fortuneWheelChances["Fortune Wheel"]
                )
            end
        end)
    end
end)


AutoFarm:AddButton("Equip Swift Samurai", function()
    print("BotÃ³n presionado: equipando 8 Swift Samurai")

    local LocalPlayer = game:GetService("Players").LocalPlayer
    local ReplicatedStorage = game:GetService("ReplicatedStorage")

    -- Primero desequipamos todo
    local petsFolder = LocalPlayer:FindFirstChild("petsFolder")
    if not petsFolder then return end

    for _, folder in pairs(petsFolder:GetChildren()) do
        if folder:IsA("Folder") then
            for _, pet in pairs(folder:GetChildren()) do
                ReplicatedStorage.rEvents.equipPetEvent:FireServer("unequipPet", pet)
            end
        end
    end
    task.wait(0.1)

    -- Ahora equipamos mÃ¡ximo 8 "Swift Samurai"
    local equipped = 0
    local maxEquip = 8
    for _, folder in pairs(petsFolder:GetChildren()) do
        if folder:IsA("Folder") then
            for _, pet in pairs(folder:GetChildren()) do
                if pet.Name == "Swift Samurai" then
                    ReplicatedStorage.rEvents.equipPetEvent:FireServer("equipPet", pet)
                    equipped += 1
                    print("Equipado Swift Samurai #" .. equipped)

                    if equipped >= maxEquip then
                        return -- salir cuando ya haya 8 equipados
                    end
                end
            end
        end
    end

    print("Se equiparon " .. equipped .. " Swift Samurai")
end)



-- ðŸŽ® BotÃ³n: Jungle Squat
AutoFarm:AddButton("Jungle Squat", function()
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char:SetPrimaryPartCFrame(CFrame.new(-8374.25586, 34.5933418, 2932.44995))
        
        local machine = workspace:FindFirstChild("machinesFolder")
        if machine and machine:FindFirstChild("Jungle Squat") then
            local seat = machine["Jungle Squat"]:FindFirstChild("interactSeat")
            if seat then
                game:GetService("ReplicatedStorage").rEvents.machineInteractRemote:InvokeServer("useMachine", seat)
            end
        end
        print("[Jungle Squat] AcciÃ³n ejecutada.")
    else
        warn("[Jungle Squat] Personaje no encontrado o no tiene HumanoidRootPart.")
    end
end)


-- ðŸ“Œ FOLDER REBIRTH (Ordenado con Anti AFK antes de Anti Lag)
local RebirthFolder = AutoFarm:AddFolder("Rebirth")

-- ðŸ” Fast Rebirths
RebirthFolder:AddSwitch("Fast Rebirths", function(state)
    getgenv().AutoFarming = state
    if state then
        task.spawn(function()
            local a = ReplicatedStorage
            local c = LocalPlayer
            local function equipPetByName(name)
                local folderPets = c:FindFirstChild("petsFolder")
                if not folderPets then return end
                for _, folder in pairs(folderPets:GetChildren()) do
                    if folder:IsA("Folder") then
                        for _, pet in pairs(folder:GetChildren()) do
                            if pet.Name == name then
                                a.rEvents.equipPetEvent:FireServer("equipPet", pet)
                            end
                        end
                    end
                end
            end
            local function unequipAllPets()
                local f = c:FindFirstChild("petsFolder")
                if not f then return end
                for _, folder in pairs(f:GetChildren()) do
                    if folder:IsA("Folder") then
                        for _, pet in pairs(folder:GetChildren()) do
                            a.rEvents.equipPetEvent:FireServer("unequipPet", pet)
                        end
                    end
                end
                task.wait(0.1)
            end
            local function getGoldenRebirthCount()
                local g = c:FindFirstChild("ultimatesFolder")
                if g and g:FindFirstChild("Golden Rebirth") then
                    return g["Golden Rebirth"].Value
                end
                return 0
            end
            local function getStrengthRequiredForRebirth()
                local rebirths = c.leaderstats.Rebirths.Value
                local baseStrength = 10000 + (5000 * rebirths)
                local golden = getGoldenRebirthCount()
                if golden >= 1 and golindex.html# About Projects

Projects is an adaptable, flexible tool for planning and tracking work on GitHub.

## About Projects

A project is an adaptable table, board, and roadmap that integrates with your issues and pull requests on GitHub to help you plan and track your work effectively at the user or organization level. You can create and customize multiple views by filtering, sorting, slicing, and grouping your issues and pull requests to manage your team backlogs and roadmaps, visualize work with configurable charts, add custom fields to track metadata specific to your team, create templates, share status updates, and automate your projects. Rather than enforcing a specific methodology, a project provides flexible features you can customize to your team’s needs and processes.

To get started and create a project, see [Creating a project](/en/issues/planning-and-tracking-with-projects/creating-projects/creating-a-project). To learn more about the different layouts, see [Changing the layout of a view](/en/issues/planning-and-tracking-with-projects/customizing-views-in-your-project/changing-the-layout-of-a-view).

### Staying up-to-date

Your projects are built from the issues and pull requests you add, creating direct references between your project and your work. Information is synced automatically to your project as you make changes, updating your views and charts. This integration works both ways, so that when you change information about a pull request or issue in your project, the pull request or issue reflects that information. For example, change an assignee in your project and that change is shown in your issue. You can take this integration even further, group your project by assignee, and make changes to issue assignment by dragging issues into the different groups.

To learn more about managing items in your project, see [Adding items to your project](/en/issues/planning-and-tracking-with-projects/managing-items-in-your-project/adding-items-to-your-project) and [Editing items in your project](/en/issues/planning-and-tracking-with-projects/managing-items-in-your-project/editing-items-in-your-project).

### Viewing your project from different perspectives

Quickly answer your most pressing questions by tailoring your project view to give you the information you need. You can save these views, allowing you to quickly return to them as needed and make them available to your team. Views not only let you scope down the items listed but also offer three different layout options.

You can view your project as a high-density table layout, as a kanban board, or a timeline-style roadmap. These customized views help you manage your team backlog, perform iteration planning, plan your roadmap, plan for a feature release, or triage bugs, right next to the code. For more information about the different layout options, see [Changing the layout of a view](/en/issues/planning-and-tracking-with-projects/customizing-views-in-your-project/changing-the-layout-of-a-view).

### Adding metadata to your items

You can use custom fields to add metadata to your issues, pull requests, and draft issues and build a richer view of item attributes. You’re not limited to the built-in metadata (assignee, milestone, labels, etc.) that currently exists for issues and pull requests. For example, you can add the following metadata as custom fields:

* A date field to track target ship dates.
* A number field to track the complexity of a task.
* A single select field to track whether a task is Low, Medium, or High priority.
* A text field to add a quick note.
* An iteration field to plan work week-by-week, including support for breaks.

You can use up to 50 fields in a project, including built-in metadata and custom fields. To learn more about the different fields you can add to a project, see [Understanding fields](/en/issues/planning-and-tracking-with-projects/understanding-fields) and [Managing items in your project](/en/issues/planning-and-tracking-with-projects/managing-items-in-your-project).

### Automating your projects

There are a number of ways you can add automation to your project. Built-in workflows allow you to automatically set fields when items are added or changed, and you can also configure your project to automatically archive items when they meet certain criteria and automatically add items from a repository when they match set criteria. For more information, see [Using the built-in automations](/en/issues/planning-and-tracking-with-projects/automating-your-project/using-the-built-in-automations).

You can also use the GraphQL API and GitHub Actions to take even greater control of your project. For more information, see [Using the API to manage Projects](/en/issues/planning-and-tracking-with-projects/automating-your-project/using-the-api-to-manage-projects) and [Automating Projects using Actions](/en/issues/planning-and-tracking-with-projects/automating-your-project/automating-projects-using-actions).

### Viewing charts and insights

You can use insights for Projects to view, create, and customize charts that use the items added to your project as their source data. You can apply filters to the default chart and also create your own charts. When you create a chart, you set the filters, chart type, the information displayed, and the chart is available to anyone that can view the project.

For more information, see [About insights for Projects](/en/issues/planning-and-tracking-with-projects/viewing-insights-from-your-project/about-insights-for-projects).

### Creating project templates

You can create project templates for your organization, or set a project as a template, to share a pre-configured project with other people in your organization which they can then use as the base for their projects. Project templates include the views, custom fields, draft issues and associated fields, configured workflows (except any auto-add workflows), and insights.

For more information, see [Managing project templates in your organization](/en/issues/planning-and-tracking-with-projects/managing-your-project/managing-project-templates-in-your-organization).

### Sharing status updates

You can keep your team up to date and share high-level overviews, which people can use to determine the status of your project. You can set a status, such as "On track" or "At risk", to allow people to quickly determine the current state of the project. You can also set start dates and target dates. Your status update can also contain a message that supports formatting with Markdown. Status updates are found on your project's side panel, below the description and README, and in the project's header and in lists when you're browsing projects.

For more information, see [Sharing project updates](/en/issues/planning-and-tracking-with-projects/learning-about-projects/sharing-project-updates).

## Next steps

Here are some helpful resources for taking your next steps with Projects:

* To learn about getting started using projects, see [Quickstart for Projects](/en/issues/planning-and-tracking-with-projects/learning-about-projects/quickstart-for-projects).
* To learn tips for managing your projects, see [Best practices for Projects](/en/issues/planning-and-tracking-with-projects/learning-about-projects/best-practices-for-projects).
