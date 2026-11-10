# Last Safe Zone - Game Project Documentation

> **Estado auditado (2026-11-09):** esta documentación histórica no refleja por completo la configuración actual de ZAREK ENDZONE PRO v0.1.1. Consulta [`AUDITORIA_ZAREK.md`](AUDITORIA_ZAREK.md) y [`VALIDACION_ZAREK.md`](VALIDACION_ZAREK.md) antes de modificar jugabilidad o activos.

## 📋 Table of Contents
- [Project Overview](#project-overview)
- [Project Structure](#project-structure)
- [Architecture Diagram](#architecture-diagram)
- [Module Documentation](#module-documentation)
  - [Global Managers](#global-managers)
  - [Global Data](#global-data)
  - [Player System](#player-system)
  - [Team System](#team-system)
  - [UI System](#ui-system)
  - [Shop System](#shop-system)
  - [Tutorial System](#tutorial-system)
- [Game Flow](#game-flow)
- [Configuration](#configuration)

---

## 🎮 Project Overview

**Last Safe Zone** is PUGC game mode, simulating the final safe zones.

- **Multi-team combat** (up to 3 teams)
- **Dynamic safe zones** that shrink and move over multiple phases
- **Round-based gameplay** with preparation and combat phases
- **Two scoring modes**: Booyah Count and Esport
- **Multiple maps** with 11 different stages
- **Shop system** for purchasing weapons and equipment
- **Comprehensive UI** including scoreboards, timers, and notifications

---

## 📁 Project Structure

```
Assets/
├── Scripts/
│   ├── Client/                    # Client-side scripts
│   │   └── ClientHUD.fcg
│   ├── Global/                    # Global game management
│   │   ├── GlobalConfig.fcg       # Game configuration
│   │   ├── GlobalData/            # Global data storage
│   │   │   ├── CombatRoundCounter.fcg
│   │   │   ├── GlobalSafeZones.fcg
│   │   │   ├── PlayerData.fcg
│   │   │   ├── PlayersShopIcon.fcg
│   │   │   ├── PlayersTimerUI.fcg
│   │   │   ├── PlayersTutorial.fcg
│   │   │   ├── PlayersWallets.fcg
│   │   │   ├── SpawnPoints.fcg
│   │   │   ├── TargetAndCombatTimer.fcg
│   │   │   ├── TeamData.fcg
│   │   │   ├── TeamPoints.fcg
│   │   │   └── TeamsAlive.fcg
│   │   └── GlobalManager/         # Global managers
│   │       ├── GlobalAnimationManager.fcg
│   │       ├── GlobalCombatManager.fcg
│   │       ├── GlobalCurrencyManager.fcg
│   │       ├── GlobalGameManager.fcg
│   │       ├── GlobalLeaderboard.fcg
│   │       ├── GlobalPreparationPhaseManager.fcg
│   │       ├── GlobalSoundManager.fcg
│   │       └── GlobalUIManager.fcg
│   ├── Player/                    # Player-related scripts
│   │   ├── PlayerController.fcg
│   │   └── PlayerUtils.fcg
│   ├── Team/                      # Team-related scripts
│   │   ├── Team.fcg
│   │   └── TeamCurrentRoundData.fcg
│   ├── Shop/                      # Shop system
│   │   └── ShopIconHUD.fcg
│   ├── ScoreBoard/                # Scoreboard UI
│   │   ├── CloseScoreBoardButton.fcg
│   │   └── MiniScoreBoard.fcg
│   └── Tutorial/                  # Tutorial system
│       ├── GameInfoTutorialButton.fcg
│       ├── SkipButton.fcg
│       └── TutorialUIButton.fcg
├── UI/                            # UI definitions
│   ├── GameInfo.ui
│   ├── MiniScoreBoardUI.ui
│   ├── FullScoreBoard.ui
│   └── EsportFullScoreBoard.ui
├── Localization/                  # Localization files
│   └── key.csv
├── Resources/                     # Game resources
└── Scene/                         # Scene files
```

---

## 🏗️ Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                      GAME ARCHITECTURE                          │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                    GLOBAL GAME MANAGER                          │
│  - Game initialization and lifecycle                            │
│  - Player join/quit handling                                    │
│  - Phase management (Preparation → Combat → End)                │
│  - Win condition checking                                       │
└──────────────────┬──────────────────────────────────────────────┘
                   │
        ┌──────────┴──────────┬───────────────┬────────────────┐
        │                     │               │                │
        ▼                     ▼               ▼                ▼
┌──────────────┐    ┌──────────────┐  ┌─────────────┐  ┌─────────────┐
│ PREPARATION  │    │   COMBAT     │  │     UI      │  │    DATA     │
│   MANAGER    │    │   MANAGER    │  │   MANAGER   │  │  MANAGERS   │
└──────────────┘    └──────────────┘  └─────────────┘  └─────────────┘
│                   │                 │                 │
│ - Random stage    │ - Team status   │ - Scoreboards  │ - Team Data
│   selection       │   tracking      │ - Timers       │ - Player Data
│ - Player spawn    │ - Combat events │ - Notifications│ - Safe Zones
│ - Weapon init     │ - Round end     │ - Animations   │ - Spawn Points
│ - Safe zone setup│   detection     │ - End game HUD │ - Round Counter
└─────┬─────────────┴────┬────────────┴────┬───────────┴────┬────────
      │                  │                 │                │
      ▼                  ▼                 ▼                ▼
┌──────────────────────────────────────────────────────────────┐
│                        PLAYER LAYER                          │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐            │
│  │  Player 1  │  │  Player 2  │  │  Player N  │            │
│  │ Controller │  │ Controller │  │ Controller │            │
│  └────────────┘  └────────────┘  └────────────┘            │
└──────────────────────────────────────────────────────────────┘
      │                  │                 │
      └──────────────────┴─────────────────┘
                         │
                         ▼
┌──────────────────────────────────────────────────────────────┐
│                         TEAM LAYER                           │
│  ┌─────────┐        ┌─────────┐        ┌─────────┐         │
│  │ Team 1  │        │ Team 2  │        │ Team 3  │         │
│  │ - Score │        │ - Score │        │ - Score │         │
│  │ - ACE   │        │ - ACE   │        │ - ACE   │         │
│  └─────────┘        └─────────┘        └─────────┘         │
└──────────────────────────────────────────────────────────────┘

GAME FLOW:
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  Start   │───▶│Preparation│───▶│  Combat  │───▶│   End    │
│  Phase   │    │  Phase    │    │  Phase   │    │  Phase   │
└──────────┘    └──────────┘    └──────────┘    └──────────┘
                     │                │                │
                     │                ▼                │
                     │      ┌────────────────┐         │
                     │      │ Safe Zone      │         │
                     │      │ Management:    │         │
                     │      │ 1. Shrinking   │         │
                     │      │ 2. Moving      │         │
                     │      │ 3. Final Zone  │         │
                     │      │ 4. Sudden Death│         │
                     │      └────────────────┘         │
                     │                                  │
                     └──────────────┬───────────────────┘
                                    │
                                    ▼
                         (Repeat until win condition)
```

---

## 📚 Module Documentation

### Global Managers

#### 🎯 GlobalGameManager
**File:** `Assets/Scripts/Global/GlobalManager/GlobalGameManager.fcg`

**Purpose:** Core game controller that manages the entire game lifecycle, player events, and phase transitions.

**Key Variables:**
- `winPoints` (int): Number of points required to win (default: 3) - Used in Booyah Count mode
- `maxRoundLock` (int): Number of rounds before changing map (default: 2)
- `availableStages` (int): Total number of available maps/stages (11)
- `prepPhaseDuration` (int): Preparation phase duration in seconds (20s first round, 10s subsequent)
- `isMatchEnded` (bool): Flag indicating if match has ended
- `isRoundEnded` (bool): Flag indicating if current round has ended

**Key Functions:**
- `OnGameStart()`: Initializes game when it starts
- `OnPlayerJoin(player)`: Handles player joining - registers UIs, sets up minimap, applies invincibility
- `OnPlayerQuit(player)`: Handles player quitting - unregisters UIs, eliminates player, checks end condition
- `OnPhaseStart(phase)`: Manages phase transitions (Preparation/End/EndCombat)
- `OnPhaseEnd(phase)`: Cleanup when phases end
- `PreparationPhaseUIHandler(duration)`: Manages preparation phase UI countdown
- `MatchEndHandler(winningTeam)`: Handles match end - shows results, updates leaderboard
- `CombatEnd(winningTeam)`: Event triggered when round ends - awards points, checks win condition
- `NoTeamWinException()`: Handles edge case where no team wins
- `CheckWinningCondition(team)`: Verifies if team meets win criteria
- `GetActiveTeams()`: Returns list of teams with active players
- `GetAllWinningTeams()`: Returns all teams with highest score (handles ties)
- `TransportToGameStartZone(player, position)`: Teleports player to start zone

**Event Flow:**
1. Game starts → Initialize global data
2. Player joins → Register UI components, setup player state
3. Phase changes → Update game state, trigger appropriate managers
4. Round ends → Calculate scores, check win condition
5. Match ends → Show results, update leaderboard

---

#### ⚔️ GlobalCombatManager
**File:** `Assets/Scripts/Global/GlobalManager/GlobalCombatManager.fcg`

**Purpose:** Manages all combat-related interactions and round logic.

**Key Variables:**
- `isChecked` (bool): Flag to prevent duplicate end-combat checks
- `teamsAlive` (List<Team>): List of teams still alive in combat phase
- `teamRanking` (List<entity<Team>>): Ranking of teams by elimination order (for esport mode)

**Key Functions:**
- `OnPhaseStart(phase)`: Starts combat timer for Combat/MovingSafeZone/LastSafeZone phases
- `OnRoundStart(roundIndex)`: Resets combat data at round start
- `OnPlayerBeEliminated(player)`: Updates mini scoreboard when player dies
- `OnPlayerEliminate(player, eliminatedPlayer, damageType)`: Tracks kills/deaths, excludes environmental deaths
- `OnPlayerDealDamage(attacker, target, damageType, value, bodyPart)`: Tracks damage dealt
- `CheckEndCombatCondition()`: Checks if only one team remains, triggers round end
- `AddAcedTeam(team)`: Adds team to ranking when all members eliminated
- `GetTeamRanking()`: Returns team elimination order for esport scoring

**Combat Flow:**
1. Combat phase starts → Timer begins
2. Players engage → Damage/kills tracked
3. Team eliminated → Added to ranking
4. One team remains → Round ends
5. Scores calculated based on ranking

---

#### 🛠️ GlobalPreparationPhaseManager
**File:** `Assets/Scripts/Global/GlobalManager/GlobalPreparationPhaseManager.fcg`

**Purpose:** Handles setup before each combat round - spawning, equipment, safe zone initialization.

**Key Variables:**
- `oldStageIndex` (int): Previous stage index to avoid repetition
- `oldStagesList` (List<int>): History of used stages
- `currentLockRound` (int): Counter for map lock rounds

**Key Functions:**
- `OnPhaseStart(phase)`: Main preparation logic - random stage selection, player setup
- `OnPhaseEnd(phase)`: Hides shop icons when preparation ends
- `InitDataBeforeCombat(duration, stageIndex, phase)`: 
  - Activates safe zone
  - Increments round counter
  - Opens shop UI
  - Sets gravity delay
  - Resets ammo and skills
  - Sets invincibility
  - Spawns teams at random positions
- `SetGravityDelay(player, delay)`: Temporary zero gravity for terrain loading
- `SetInvincibilityStatus(player, invincible)`: Sets player invincibility
- `SetMovementStatus(canMove)`: Enables/disables player movement
- `InitPlayerWeapons()`: 
  - Adds Level 3 vest and helmet (undamageable)
  - Adds weapons (M590 shotgun, Woodpecker rifle)
  - Adds consumables (MedKit, SuperMed, Inhaler, Grenades)
  - Adds utility items (Gloo Walls, Flash Freeze, Gloo Melter)
  - Adds attachments (Muzzle, Mag, Foregrip, Scope, Stock)
- `RandomStage(oldValue)`: Selects random stage, avoiding recent stages
- `ResetSkill(player)`: Resets player skills (workaround for API bug)
- `ResetAmmo(player)`: Sets all weapons to 100 ammo

**Preparation Flow:**
1. Select random stage (avoid recent ones)
2. Activate safe zone
3. Setup players (gravity, invincibility, equipment)
4. Spawn teams at random locations
5. Start countdown timer
6. Switch to combat phase

---

#### 🖼️ GlobalUIManager
**File:** `Assets/Scripts/Global/GlobalManager/GlobalUIManager.fcg`

**Purpose:** Central hub for all UI management - scoreboards, notifications, HUDs, animations.

**Key Variables:**
- `fullScoreBoardUIDict` (Map<entity<Player>, entity<CustomUI>>): Full scoreboards per player
- `miniScoreBoardUIdict` (Map<entity<Player>, entity<CustomUI>>): Mini scoreboards per player
- `borderNotiDict` (Map<entity<Player>, entity<BuiltInUI>>): Border notification UIs
- `roundInfoUIDitct` (Map<entity<Player>, entity<UISimpleRoundStartBanner>>): Round info banners

**Key Functions:**

**Scoreboard Management:**
- `RegisterMiniScoreBoard(player)`: Creates mini scoreboard with team color (#FFB400)
- `UpdateMiniScoreboard()`: Updates team scores and alive counts
- `RegisterFullScoreBoard(player)`: Creates full scoreboard (Booyah/Esport mode)
- `SetVisibilityFullScoreBoard(player, visible)`: Shows/hides full scoreboard
- `UpdateFullScoreBoardWinTeam(winningTeam)`: Animates score updates with color transitions

**HUD Creation:**
- `CreateGlobalOpeningHUD(player)`: Shows game title/description for 8 seconds
- `CreateRoundInfoHUD(player)`: Displays round number and stage name
- `CreateEndRoundHUD(winningTeam)`: Shows winning team for 2 seconds
- `CreateLastRoundHUD(winningTeams, player)`: Displays win/lose result
- `CreateEndGameHUD(player, team, resultStatus, teamSorted)`: Final match results

**Notifications:**
- `RegisterBorderNotification(player)`: Creates border notification UI
- `ActivateBorderMovingNotification()`: "Border is moving" message
- `ActivateBorderShrinkingNotification()`: "Border is shrinking" message
- `CreateLastRoundNotification(player)`: "Last round" warning with sound

**Utility:**
- `CreatePlusAnimation(winningTeam)`: Animates +1 point on scoreboard
- `UnregisterAllUIs(player)`: Removes all UIs when player leaves
- `Enable5sCountdownHUD(player)`: 5-second countdown with sound
- `CreateDelay5sCountDownHUD(player, delay)`: Delayed countdown

**UI Update Flow:**
1. Player joins → Register all UI components
2. Score changes → Update mini/full scoreboards with animations
3. Round events → Show appropriate notifications
4. Player leaves → Clean up all UIs

---

#### 🎵 GlobalSoundManager
**File:** `Assets/Scripts/Global/GlobalManager/GlobalSoundManager.fcg`

**Purpose:** Manages all game audio including music, sound effects, and voice notifications.

**Key Functions:**
- `PlayEndMatchSound(player)`: Victory/defeat music
- `PlayLastRoundNotificationSound(player)`: Last round alert sound
- `PlayTooltipSfx(player)`: UI interaction sounds

---

#### 🎬 GlobalAnimationManager
**File:** `Assets/Scripts/Global/GlobalManager/GlobalAnimationManager.fcg`

**Purpose:** Handles UI animations and tweens for smooth visual transitions.

**Key Functions:**
- `DOIncreaseTween(widget)`: Animates number increase
- `DOChangeColor(widget, fromColor, toColor, duration)`: Color transition animation
- `DOChangeIncreaseAlpha(widget, increase, duration, targetWidget, baseValue)`: Fade-in with value increase
- `DOIncrease(widget, targetValue, duration, delay)`: Delayed number animation

---

#### 💰 GlobalCurrencyManager
**File:** `Assets/Scripts/Global/GlobalManager/GlobalCurrencyManager.fcg`

**Purpose:** Manages in-game currency and player wallets.

**Key Functions:**
- `AddCurrency(player, amount)`: Adds currency to player
- `RemoveCurrency(player, amount)`: Deducts currency from player
- `GetCurrency(player)`: Returns player's current currency

---

#### 🏆 GlobalLeaderboard
**File:** `Assets/Scripts/Global/GlobalManager/GlobalLeaderboard.fcg`

**Purpose:** Manages persistent leaderboard data with automatic daily reset functionality using AccumulatedDataStore.

**Key Features:**
- **Automatic Daily Reset**: Clears leaderboard data after 12 minutes (720 seconds) of inactivity
- **Race Condition Prevention**: Uses deletion flag to prevent multiple servers from clearing simultaneously
- **Persistent Storage**: Stores player points and matches played across game sessions
- **Server Timestamp Tracking**: Monitors last update time for reset logic

**Key Variables:**
- Server-side persistent data stored in AccumulatedDataStore:
  - `lastServerTimeStamp`: Last update timestamp for reset tracking
  - `isDeleting`: Flag to prevent concurrent deletion operations (0 = not deleting, 1 = deleting in progress, 2 = deletion complete)

**Key Functions:**

**Initialization & Reset:**
- `OnAwake()`: Main initialization logic
  - Reads current server timestamp
  - Checks if leaderboard data is outdated (>720 seconds)
  - Manages deletion flag to prevent race conditions
  - Triggers automatic leaderboard clear if needed
  - Updates timestamp after successful clear
- `ClearLeaderboardData()`: Async function that clears all leaderboard entries
  - Reads entries ranked 1-150
  - Resets each entry's points to [0, 0]
  - Uses 2-second delays between operations to prevent rate limiting

**Player Data Management:**
- `SetLeaderboardDataForPlayer(player, points, matchesPlayed)`: Async function to update player stats
  - Retrieves existing player data
  - If player is new: Creates entry with initial points and matches
  - If player exists: Adds new points to existing total, increments match count
  - Automatically updates leaderboard rankings
- `GetLeaderboardDataForPlayer(player, out returnList)`: Async function to retrieve player stats
  - Returns `[totalPoints, matchesPlayed]` as List<int>
  - Returns `nil` if player has no leaderboard data

**Event Handlers:**
- `OnPlayerJoin(player)`: (Currently commented out) Placeholder for future player join logic
- `OnPhaseStart(phase)`: (Currently commented out) Placeholder for timestamp debugging

**Leaderboard Reset Logic:**
```
┌──────────────────────────────────────────────────┐
│ OnAwake() - Server Start                        │
└─────────────────┬────────────────────────────────┘
                  │
                  ▼
      ┌───────────────────────┐
      │ Read Server Timestamp │
      │ Read Last Timestamp   │
      └───────────┬───────────┘
                  │
                  ▼
      ┌───────────────────────┐
      │ Calculate Time Diff   │
      └───────────┬───────────┘
                  │
                  ▼
      ┌───────────────────────┐
      │ Diff > 720 seconds?   │
      └────┬──────────┬───────┘
           │ NO       │ YES
           │          │
           ▼          ▼
    ┌────────┐   ┌──────────────────────┐
    │ Skip   │   │ Check Deletion Flag  │
    └────────┘   └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ isDeleting == 0?     │
                 └────┬──────────┬──────┘
                      │ NO       │ YES
                      │          │
                      ▼          ▼
               ┌─────────┐  ┌────────────────────┐
               │ Return  │  │ Set isDeleting = 1 │
               └─────────┘  └─────────┬──────────┘
                                      │
                                      ▼
                           ┌──────────────────────┐
                           │ ClearLeaderboardData │
                           │ (Loop 1-150 ranks)   │
                           └─────────┬────────────┘
                                     │
                                     ▼
                           ┌──────────────────────┐
                           │ Update Timestamp     │
                           └─────────┬────────────┘
                                     │
                                     ▼
                           ┌──────────────────────┐
                           │ Set isDeleting = 2   │
                           │ (Deletion complete)  │
                           └──────────────────────┘
```

**Usage Example:**
```fc
// At match end, update player's leaderboard
async func UpdatePlayerLeaderboard(player entity<Player>) {
    var playerScore = globalEntity<PlayerData>.GetPlayerData(player)[0] // Get kills
    var placementPoints = globalEntity<TeamData>.GetTeamPlacementPoints(player<Player>.HostTeam)
    var totalPoints = playerScore + placementPoints
    
    wait globalEntity<GlobalLeaderboard>.SetLeaderboardDataForPlayer(player, totalPoints, 1)
}
```

**Technical Notes:**
- Uses `AccumulatedDataStore` for persistent cross-session storage
- 2-second delays between operations prevent database rate limiting
- Deletion flag prevents multiple servers from clearing simultaneously
- Timestamp stored as float, converted to int using `Math.Round()`
- Leaderboard ranks are 1-indexed (1-150)
- Each entry stores: `[UUID, totalPoints, matchesPlayed]`

**Reset Behavior:**
- **12-minute inactivity window**: If no games run for 720+ seconds, leaderboard clears
- **First server wins**: Uses `isDeleting` flag to ensure only one server performs clear
- **Graceful failure**: If deletion flag is set, subsequent servers skip clearing
- **Automatic recovery**: Deletion flag reset to 0 after completion

---

### Global Data

#### 📊 TeamData
**File:** `Assets/Scripts/Global/GlobalData/TeamData.fcg`

**Purpose:** Central repository for all team-related statistics and scoring.

**Key Variables:**
- `teamPoints` (Map<entity<Team>, int>): Total points per team
- `teamPlacementPoints` (Map<entity<Team>, int>): Esport mode placement points
- `teamKillsPoints` (Map<entity<Team>, int>): Esport mode kill points
- `teamBooyahCount` (Map<entity<Team>, int>): Esport mode win count

**Key Functions:**

**Initialization:**
- `OnAwake()`: Initializes maps based on scoring type
- `InitializeTeamPoints()`: Sets all teams to zero points
- `RegisterTeamPoint(team)`: Adds new team to tracking

**Booyah Count Mode:**
- `AddPointsToTeam(team, points)`: Adds points to team (simple +1 per win)
- `GetTeamPoint(team)`: Returns team's total points

**Esport Mode:**
- `AddEsportPointsToTeam(team, kills, placementPoints)`: Adds kills + placement points
- `AddBooyahToTeam(team)`: Increments win count
- `GetTeamBooyahCount(team)`: Returns number of wins
- `GetTeamPlacementPoints(team)`: Returns placement points
- `GetTeamKillsPoints(team)`: Returns kill points
- `GetTeamEsportPoints(team)`: Returns total esport points

**Sorting & Ranking:**
- `GetSortedTeamList()`: Returns teams sorted by:
  - **Booyah Count Mode**: Points → Team kills → Team damage
  - **Esport Mode**: Total points → Booyah count → Kill points → Placement points
- `GetSortedPlayersGroup()`: Returns players grouped by team ranking
- `GetSortedPlayersInTeam(team)`: Returns players in team sorted by kills → damage

---

#### 👥 PlayerData
**File:** `Assets/Scripts/Global/GlobalData/PlayerData.fcg`

**Purpose:** Tracks individual player statistics (kills, deaths, damage).

**Key Variables:**
- `playerKills` (Map<entity<Player>, int>): Kill count per player
- `playerDeaths` (Map<entity<Player>, int>): Death count per player
- `playerDamage` (Map<entity<Player>, int>): Damage dealt per player

**Key Functions:**
- `RegisterPlayer(player)`: Adds player to tracking (initializes to 0)
- `UnregisterPlayer(player)`: Removes player from tracking
- `IncreaseKill(player)`: Increments kill count
- `IncreaseDeath(player)`: Increments death count
- `IncreaseDamage(player, value)`: Adds to damage total
- `GetPlayerData(player)`: Returns `[kills, deaths, damage]`
- `GetSortedPlayerList()`: Returns all players sorted by kills → damage

---

#### 🔵 GlobalSafeZones
**File:** `Assets/Scripts/Global/GlobalData/GlobalSafeZones.fcg`

**Purpose:** Manages dynamic safe zone system with multiple phases.

**Key Variables:**
- `safeZonePhase1Dmg` (int): 5 damage per tick (initial shrink)
- `safeZonePhase2Dmg` (int): 20 damage per tick (moving zone)
- `safeZonePhase3Dmg` (int): 40 damage per tick (final zone)
- `safeZoneFinalDmg` (int): 80 damage per tick (sudden death)
- `safeZoneStartPoints` (Map<int, Vector3>): Starting positions for 11 stages
- `safeZoneEndPoints` (Map<int, List<entity<Entity>>>): Possible end positions per stage
- `safeZoneInitRadius` (Map<int, float>): Initial radius per stage (15-50m)
- `safeZonesEndRadius` (Map<int, float>): Final radius per stage (12-25m)
- `safeZonesShrinkDuration` (Map<int, int>): Duration for shrinking (60s)
- `lastSafeZoneDuration` (int): Final zone duration (30s)
- `reduceRadiusWhenMoving` (float): Radius reduction during move (5m)

**Key Functions:**
- `OnGameStart()`: Initializes all stage configurations
- `SetStartShrinkTime(prepDuration)`: Adjusts shrink timing based on prep phase
- `ActivateSafeZone(index, phase)`: Main safe zone controller
  - **__Before6Combat__**: Creates initial large zone
  - **Combat**: Shrinks to smaller radius
  - **MovingSafeZone**: Moves to random end point while shrinking
  - **LastSafeZone**: Final shrink to point
  - **SuddenDeath**: Zero radius, maximum damage

**Safe Zone Phases:**
```
Phase 1: Initial Shrink
├─ Radius: 15-50m → 12-25m
├─ Duration: 33s
└─ Damage: 5/tick

Phase 2: Moving + Shrinking
├─ Radius: 12-25m → 7-20m (reduced by 5m)
├─ Duration: 60s
├─ Moves to random end point
└─ Damage: 20/tick

Phase 3: Final Zone
├─ Radius: 7-20m → 0m
├─ Duration: 30s
└─ Damage: 40/tick

Phase 4: Sudden Death
├─ Radius: 0m
├─ Duration: Infinite
└─ Damage: 80/tick (instant elimination)
```

---

#### 📍 SpawnPoints
**File:** `Assets/Scripts/Global/GlobalData/SpawnPoints.fcg`

**Purpose:** Manages spawn point groups for each stage.

**Key Functions:**
- `GetSpawnPositionForRound(stageIndex)`: Returns list of spawn positions for stage
- `GetRandomSpawnPattern()`: Returns randomized team assignment pattern
- `SetSpawnPointsActiveStatusForRound(stageIndex, active)`: Shows/hides spawn markers
- `GetNameForStage(stageIndex)`: Returns localized stage name

---

#### ⏱️ TargetAndCombatTimer
**File:** `Assets/Scripts/Global/GlobalData/TargetAndCombatTimer.fcg`

**Purpose:** Manages in-game timers and displays them to players.

**Key Functions:**
- `RegisterGameInfo(player)`: Creates timer UI for player
- `UnregisterGameInfo(player)`: Removes timer UI
- `StartTimer(duration)`: Starts countdown timer
- `ResetTimer()`: Stops and resets timer
- `UpdateRoundCountInfo(player, roundCount)`: Updates round number display
- `UpdateRoundStageInfo(player, roundCount, stageName)`: Updates stage name display

---

#### 🔢 CombatRoundCounter
**File:** `Assets/Scripts/Global/GlobalData/CombatRoundCounter.fcg`

**Purpose:** Tracks current round number.

**Key Variables:**
- `count` (int): Current round number (starts at 0)

**Key Functions:**
- `IncreaseCount()`: Increments round by 1
- `GetCount()`: Returns current round number

---

#### ✅ TeamsAlive
**File:** `Assets/Scripts/Global/GlobalData/TeamsAlive.fcg`

**Purpose:** Tracks which teams are still active.

**Key Functions:**
- `RegisterTeam(team)`: Adds team to alive list
- `GetAliveTeams()`: Returns list of non-aced teams

---

#### 🛒 PlayersShopIcon
**File:** `Assets/Scripts/Global/GlobalData/PlayersShopIcon.fcg`

**Purpose:** Manages shop button UI for each player.

**Key Functions:**
- `RegisterShopIcon(player)`: Creates shop button UI
- `RegisterClientShopIconDimension(player, position, size)`: Sets button position/size from client
- `UnregisterShopIcon(player)`: Removes shop button
- `ToggleIconForPlayer(player, visible)`: Shows/hides shop button

---

#### 💵 PlayersWallets
**File:** `Assets/Scripts/Global/GlobalData/PlayersWallets.fcg`

**Purpose:** Stores currency amounts for players.

---

#### 🎓 PlayersTutorial
**File:** `Assets/Scripts/Global/GlobalData/PlayersTutorial.fcg`

**Purpose:** Manages tutorial UI for new players.

**Key Functions:**
- `RegisterPlayer(player)`: Shows tutorial UI
- `UnRegisterPlayer(player)`: Removes tutorial UI

---

#### ⏲️ PlayersTimerUI
**File:** `Assets/Scripts/Global/GlobalData/PlayersTimerUI.fcg`

**Purpose:** Individual timer displays for players.

**Key Functions:**
- `SetTimerVisibility(visible)`: Shows/hides all player timers

---

### Player System

#### 🎮 PlayerController
**File:** `Assets/Scripts/Player/PlayerController.fcg`

**Purpose:** Attached to each player entity, handles player-specific logic and events.

**Key Variables:**
- `playerShopHUD` (entity<BuiltInUI>): Reference to player's shop UI

**Key Functions:**
- `OnAwake()`: Disables item dropping on death
- `OnUpdateShopButton(position, size)`: Registers shop button with client dimensions
- `OnPlayerBeEliminated(player)`: Removes dropped items when eliminated
- `OnEquipWeapon(weapon)`: Sets new weapon ammo to 100
- `OnDropWeapon(weapon)`: Destroys dropped weapon (prevents duplication)
- `OnDropConsumable(consumableID, item)`: Destroys dropped consumable
- `SetPlayerShopHUD(shop)`: Stores shop UI reference
- `SetZeroGravityStatus(player, status)`: Toggles gravity (for terrain loading)
- `AddItemToPlayer(player, item, amount)`: Adds items to inventory, handles equipment replacement

**Event Handlers:**
- `OnPlayerBeEliminated`: Cleanup when player dies
- `OnEquipWeapon`: Initialize new weapon state
- `OnDropWeapon/OnDropConsumable`: Prevent item accumulation

---

#### 🛠️ PlayerUtils
**File:** `Assets/Scripts/Player/PlayerUtils.fcg`

**Purpose:** Utility functions for player operations.

**Key Functions:**
- `RemoveAllDropItems(player)`: Removes all dropped items from player
- `RotatePlayerToDirection(fromPos, toPos)`: Calculates rotation to face direction
- `CreateOpponentPositionEffect(position, duration)`: Visual effect at enemy spawn

---

### Team System

#### 👥 Team
**File:** `Assets/Scripts/Team/Team.fcg`

**Purpose:** Attached to each team entity, handles team-level events.

**Key Variables:**
- `lastRoundPoint` (int): Team's score from previous round (for esport mode)

**Key Functions:**
- `OnACE()`: Triggered when all team members eliminated
  - Adds team to ranking
  - Checks if combat should end
- `GetLastRoundPoint()`: Returns previous round score
- `SetLastRoundPoint(point)`: Stores previous round score

**Team Lifecycle:**
1. Team formed → Registered in TeamData
2. Round starts → Players spawn together
3. Combat → Members eliminated one by one
4. All eliminated → OnACE triggered → Added to ranking
5. Round ends → Score calculated based on ranking

---

#### 📈 TeamCurrentRoundData
**File:** `Assets/Scripts/Team/TeamCurrentRoundData.fcg`

**Purpose:** Tracks temporary per-round statistics for esport mode.

**Key Functions:**
- `IncreaseKill()`: Increments team kills this round
- `IncreaseBooyah()`: Marks team as winner this round
- `GetKillThisRound()`: Returns kills in current round
- `GetBooyahThisRound()`: Returns if team won this round
- `ResetRoundData()`: Clears data for next round

---

### Shop System

#### 🛒 ShopIconHUD
**File:** `Assets/Scripts/Shop/ShopIconHUD.fcg`

**Purpose:** Handles shop button interactions.

**Key Functions:**
- `OnTapped(player)`: Opens shop UI when button clicked
- `OpenShopExternal(player)`: Opens shop programmatically
- `CloseShop(player)`: Closes shop UI

---

### UI System

#### 📊 MiniScoreBoard
**File:** `Assets/Scripts/ScoreBoard/MiniScoreBoard.fcg`

**Purpose:** Small scoreboard visible during combat.

**Features:**
- Team scores
- Alive count per team
- Team color indicators
- Compact design

---

#### 🔘 CloseScoreBoardButton
**File:** `Assets/Scripts/ScoreBoard/CloseScoreBoardButton.fcg`

**Purpose:** Button to close full scoreboard.

---

### Tutorial System

#### 📚 GameInfoTutorialButton
**File:** `Assets/Scripts/Tutorial/GameInfoTutorialButton.fcg`

**Purpose:** Shows game information tutorial.

---

#### ⏭️ SkipButton
**File:** `Assets/Scripts/Tutorial/SkipButton.fcg`

**Purpose:** Allows skipping tutorial.

---

#### 🎯 TutorialUIButton
**File:** `Assets/Scripts/Tutorial/TutorialUIButton.fcg`

**Purpose:** Generic tutorial button handler.

---

## 🎯 Game Flow

### Complete Match Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                        MATCH START                              │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ 1. INITIALIZATION                                               │
│    - Teams formed (3 teams)                                     │
│    - Players join teams                                         │
│    - TeamData registers all teams                               │
│    - PlayerData registers all players                           │
│    - Opening HUD displayed                                      │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
                    ┌──────────────────┐
                    │  ROUND LOOP      │◀─────────────┐
                    │  (until win)     │               │
                    └─────────┬────────┘               │
                              │                        │
                              ▼                        │
┌─────────────────────────────────────────────────────────────────┐
│ 2. PREPARATION PHASE (__Before6Combat__)                        │
│    Duration: 20s (first round) / 10s (subsequent)               │
│                                                                 │
│    GlobalPreparationPhaseManager:                               │
│    ├─ Select random stage (avoid recent 2)                     │
│    ├─ Activate safe zone (initial large radius)                │
│    ├─ Increment round counter                                  │
│    ├─ For each player:                                         │
│    │  ├─ Open shop UI (first round only)                       │
│    │  ├─ Set gravity delay (500ms)                             │
│    │  ├─ Reset ammo (100 per weapon)                           │
│    │  ├─ Reset skills                                          │
│    │  ├─ Set invincibility (until countdown ends)              │
│    │  ├─ Show shop icon                                        │
│    │  └─ Create round info HUD                                 │
│    ├─ Update mini scoreboard                                   │
│    ├─ Initialize weapons & equipment:                          │
│    │  ├─ Level 3 Vest (undamageable)                           │
│    │  ├─ Level 3 Helmet (undamageable)                         │
│    │  ├─ M590 Shotgun (100 ammo)                               │
│    │  ├─ Woodpecker Rifle (100 ammo)                           │
│    │  ├─ Attachments (Muzzle, Mag, Foregrip, Scope, Stock)    │
│    │  ├─ Consumables (4 MedKit, 1 SuperMed, 10 Inhaler)       │
│    │  ├─ Throwables (1 Grenade, 1 Flash Freeze)               │
│    │  └─ Utility (8 Gloo Wall, 2 Gloo Melter, Heal Pistol)    │
│    ├─ Get spawn pattern (randomized team positions)            │
│    ├─ Spawn teams at random locations                          │
│    └─ Start 5-second countdown                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ 3. COMBAT PHASE - PHASE 1: Initial Shrink                      │
│    Duration: 33s                                                │
│                                                                 │
│    Safe Zone:                                                   │
│    ├─ Type: Shrinking (static center)                          │
│    ├─ Radius: 15-50m → 12-25m (stage dependent)                │
│    ├─ Damage: 5 per tick                                       │
│    └─ Notification: "Border is shrinking"                      │
│                                                                 │
│    GlobalCombatManager:                                         │
│    ├─ Start combat timer (33s)                                 │
│    ├─ Remove invincibility from all players                    │
│    ├─ Hide shop icons                                          │
│    ├─ Track eliminations:                                      │
│    │  ├─ OnPlayerEliminate → Update kills/deaths               │
│    │  ├─ OnPlayerDealDamage → Track damage                     │
│    │  └─ Update mini scoreboard                                │
│    └─ Monitor team status continuously                         │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ 4. COMBAT PHASE - PHASE 2: Moving Zone                         │
│    Duration: 60s                                                │
│                                                                 │
│    Safe Zone:                                                   │
│    ├─ Type: Moving + Shrinking                                 │
│    ├─ Start: Previous zone center                              │
│    ├─ End: Random end point (chosen from stage endpoints)      │
│    ├─ Radius: 12-25m → 7-20m (reduced by 5m)                   │
│    ├─ Damage: 20 per tick                                      │
│    └─ Notification: "Border is moving"                         │
│                                                                 │
│    Combat Events:                                               │
│    ├─ Team Elimination (OnACE):                                │
│    │  ├─ Add to teamRanking (for esport scoring)               │
│    │  └─ CheckEndCombatCondition()                             │
│    └─ Continuous damage tracking                               │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ 5. COMBAT PHASE - PHASE 3: Final Zone                          │
│    Duration: 30s                                                │
│                                                                 │
│    Safe Zone:                                                   │
│    ├─ Type: Shrinking to point                                 │
│    ├─ Center: Previous end point                               │
│    ├─ Radius: 7-20m → 0m                                        │
│    ├─ Damage: 40 per tick                                      │
│    └─ Notification: "Border is shrinking"                      │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ 6. COMBAT PHASE - PHASE 4: Sudden Death (if needed)            │
│    Duration: Until one team remains                            │
│                                                                 │
│    Safe Zone:                                                   │
│    ├─ Type: Zero radius (entire map is damage zone)            │
│    ├─ Damage: 80 per tick (instant elimination)                │
│    └─ Forces rapid conclusion                                  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ 7. ROUND END                                                    │
│                                                                 │
│    Triggered when:                                              │
│    ├─ Only 1 team remains alive                                │
│    └─ OR all teams eliminated (sudden death)                   │
│                                                                 │
│    GlobalGameManager.CombatEnd(winningTeam):                    │
│    ├─ Stop combat timer                                        │
│    ├─ Set invincibility on winners (60s)                       │
│    ├─ Disable safe zone                                        │
│    ├─ Award points based on scoring mode:                      │
│    │                                                            │
│    │  ┌─ BOOYAH COUNT MODE:                                    │
│    │  │  └─ AddPointsToTeam(winningTeam, 1)                    │
│    │  │                                                         │
│    │  └─ ESPORT MODE:                                          │
│    │     ├─ Winner gets:                                       │
│    │     │  ├─ Placement points (based on rank = 1st)          │
│    │     │  ├─ Kill points (team total kills)                  │
│    │     │  └─ +1 Booyah count                                 │
│    │     │                                                      │
│    │     └─ Eliminated teams get:                              │
│    │        ├─ Placement points (based on elimination order)   │
│    │        └─ Kill points (team total kills)                  │
│    │                                                            │
│    ├─ Show end round HUD (2s)                                  │
│    ├─ Update full scoreboard (with animations):                │
│    │  ├─ Highlight player's team (#D68E00 gold)                │
│    │  ├─ Animate point increases                               │
│    │  └─ Show kill/placement/total breakdown (esport)          │
│    ├─ Clear residual level objects                             │
│    └─ Check win condition                                      │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
                    ┌─────────────────┐
                    │ Win Condition?  │
                    └────┬────────┬───┘
                         │        │
                    NO   │        │ YES
                         │        │
                         │        ▼
                         │   ┌──────────────────────────────────┐
                         │   │ 8. MATCH END                     │
                         │   │                                  │
                         │   │ Condition Met:                   │
                         │   │ ├─ Booyah: Team reaches 3 points│
                         │   │ └─ Esport: 5 rounds completed   │
                         │   │                                  │
                         │   │ GlobalGameManager.MatchEndHandler│
                         │   │ ├─ Calculate final rankings:    │
                         │   │ │  - Esport: Total points →     │
                         │   │ │    Booyah count → Kills →     │
                         │   │ │    Placement                  │
                         │   │ │  - Booyah: Points → Kills →   │
                         │   │ │    Damage                     │
                         │   │ ├─ Update leaderboard           │
                         │   │ ├─ Show victory/defeat HUD      │
                         │   │ ├─ Unregister all UIs           │
                         │   │ ├─ Play end match sound         │
                         │   │ └─ Display final results (5s)   │
                         │   └──────────────────────────────────┘
                         │                    │
                         │                    ▼
                         │            ┌──────────────┐
                         │            │  END PHASE   │
                         │            └──────────────┘
                         │
                         └─────────────────────┐
                                              │
                    ┌─────────────────────────┘
                    │
                    ▼
        ┌────────────────────────┐
        │ 9. ENDCOMBAT PHASE     │
        │    Duration: 7s        │
        │                        │
        │    ├─ Show full        │
        │    │  scoreboard       │
        │    ├─ Players review   │
        │    │  stats            │
        │    └─ Prepare for next │
        │       round            │
        └────────────────────────┘
                    │
                    └──────────────────┐
                                       │
                  (Return to ROUND LOOP)
```

### Edge Cases Handled

1. **No Team Wins (All Eliminated)**:
   - `NoTeamWinException()` triggered
   - Scores awarded based on ranking
   - Round ends normally

2. **Player Disconnection**:
   - `OnPlayerQuit()` eliminates player
   - Checks if combat should end
   - Unregisters all UIs

3. **Ties in Esport Mode**:
   - Primary: Total points
   - Tiebreaker 1: Booyah count
   - Tiebreaker 2: Kill points
   - Tiebreaker 3: Placement points
   - Multiple winners possible

4. **Last Round Detection**:
   - Esport mode: When `roundCount == esportMaxRound`
   - Shows special notification
   - Match ends after this round

---

## ⚙️ Configuration

### GlobalConfig.fcg

```fc
scoringType: SCORING_TYPE.ESPORT  // or SCORING_TYPE.BOOYAH_COUNT
esportMaxRound: 5                 // Number of rounds in esport mode
```

### Scoring Types

#### Booyah Count Mode
- **Win Condition**: First team to 3 points
- **Points**: +1 per round win
- **Tiebreaker**: Team kills → Team damage

#### Esport Mode
- **Win Condition**: 5 rounds completed
- **Points Calculation**:
  - **Placement Points**: Based on elimination order
    - 1st place: Most points
    - 2nd place: Medium points
    - 3rd place: Least points
  - **Kill Points**: 1 point per kill
  - **Booyah Count**: +1 for round winner
- **Final Ranking**: Total points → Booyah count → Kill points → Placement points

### Game Settings (GlobalGameManager)

```fc
winPoints: 3               // Points to win (Booyah mode)
maxRoundLock: 2           // Rounds before changing map
availableStages: 11       // Number of stages/maps
prepPhaseDuration: 20s    // First round preparation
                 : 10s    // Subsequent rounds
```

### Safe Zone Settings (GlobalSafeZones)

```fc
// Damage per tick
safeZonePhase1Dmg: 5      // Initial shrink
safeZonePhase2Dmg: 20     // Moving zone
safeZonePhase3Dmg: 40     // Final zone
safeZoneFinalDmg: 80      // Sudden death

// Timing
lastSafeZoneDuration: 30s
reduceRadiusWhenMoving: 5m
```

---

## 🎨 Key Features

### Dynamic Safe Zones
- 11 different stage layouts
- 4-phase shrinking system
- Moving zones for strategy variation
- Configurable damage and timing

### Comprehensive UI
- Real-time mini scoreboard
- Detailed full scoreboard with animations
- Round information displays
- Border notifications
- End game results screen

### Flexible Scoring
- Two distinct modes (Booyah/Esport)
- Complex tiebreaker system
- Individual player stats tracking
- Team-based ranking

### Player Management
- Automatic weapon initialization
- Shop system for purchases
- Invincibility during preparation
- Drop prevention to avoid clutter

### Performance Optimizations
- UI caching per player
- Efficient scoreboard updates
- Cleanup on player quit
- Optimized safe zone calculations

---

## 🔧 Technical Details

### File Types
- **`.fcg`**: Script logic files (graphs, functions, events)
- **`.fcc`**: Library definition files (components, types, events)
- **`.ui`**: UI layout definitions
- **`.csv`**: Localization strings

### Architecture Patterns
- **Singleton Globals**: `globalEntity<GraphName>` for managers
- **Component-Based**: `thisEntity<ComponentType>` for entity access
- **Event-Driven**: `DispatchEvent()` for cross-script communication
- **Async/Await**: Asynchronous functions for timing and delays

### Platform Targeting
- `[platform_server]`: Server-authoritative logic (default)
- `[platform_client]`: Client-side UI and effects

---

## 📝 Notes

- This project uses FC language version compatible with Craftland Studio 1.14.1+
- Some functions have workarounds for API limitations (e.g., `ResetSkill()`)
- Extensive logging for debugging (can be disabled in production)
- Modular design allows easy feature addition/modification

---

## 🤝 Contributing

When modifying this project:

1. **Follow naming conventions**:
   - Variables: `camelCase`
   - Functions: `PascalCase`
   - Constants: `UPPER_CASE`

2. **Document all functions**:
   - Purpose description
   - Parameter explanations
   - Return value details

3. **Test both scoring modes**:
   - Booyah Count
   - Esport

4. **Verify safe zone behavior** across all 11 stages

5. **Check UI updates** for all players

---

## 📄 License

[Specify your license here]

---

**Last Updated**: October 2025
**Version**: 1.0
**Engine**: Free Fire Craftland Studio
**Language**: FC (Free Fire Code Language)

