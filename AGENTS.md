# Aura Project

## Project Identity

- **Engine**: Unreal Engine 5.5 (not 4.26 despite CLAUDE.md claims)
- **Project file**: `Aura.uproject`
- **Module**: Single `Aura` module (Runtime), defined in `Source/Aura/`
- **Solution**: `Aura.sln` (gitignored; regenerate via `.uproject` right-click → Generate)

## Build

- C++ only (no Lua/UnLua in this repo despite CLAUDE.md mentioning it)
- Build targets: `Aura` (Game), `AuraEditor` (Editor)
- Build settings: `BuildSettingsVersion.V4`, explicit/shared PCH
- Key module dependencies: `GameplayAbilities`, `EnhancedInput`, `AIModule`, `Niagara`, `NavigationSystem`
- Enabled plugins: `GameplayAbilities`, `MotionWarping`, `ModelingToolsEditorMode`
- Renderer: DX12, SM6, Virtual Shadow Maps, Lumen GI/reflection

## Source Layout

```
Source/Aura/
  Aura.Build.cs          — Module build config
  Public/                 — Header files (mirrors Private/ structure)
  Private/                — Implementation files
    AbilitySystem/        — GAS: ASC, AttributeSet, abilities, MMC, ExecCalc, async tasks, data
    Actor/                — Projectile, EffectActor
    AI/                   — AIController, BTTask, BTService
    Character/            — AuraCharacterBase → AuraCharacter, AuraEnemy
    Game/                 — AuraGameModeBase
    Input/                — AuraInputConfig, AuraInputComponent (Enhanced Input)
    Interaction/          — Interfaces: CombatInterface, PlayerInterface, EnemyInterface
    Player/               — AuraPlayerController, AuraPlayerState
    UI/                   — HUD, WidgetControllers, Widgets
```

## Architecture Patterns

- **Gameplay Ability System (GAS)** is the core framework; all abilities, attributes, effects use it
- **Native Gameplay Tags**: Defined in C++ via `FAuraGameplayTags` singleton (`AuraGameplayTags.h/.cpp`), not in `DefaultGameplayTags.ini` (that file only holds a few extras)
- **Widget Controller pattern**: `UAuraWidgetController` base → `UOverlayWidgetController`, `UAttributeMenuWidgetController` for UI-MVC binding
- **Character hierarchy**: `AAuraCharacterBase` (abstract, owns ASC) → `AAuraCharacter` (player) / `AAuraEnemy` (AI)
- **Interfaces**: `ICombatInterface`, `IPlayerInterface`, `IEnemyInterface` — blueprint-callable implementations
- **Custom input**: `UAuraInputComponent` overrides default input component (`DefaultInput.ini` sets `DefaultInputComponentClass`)
- **Curve Tables**: Attribute scaling data lives in `Data/` as JSON/CSV, imported into `Content/Blueprints/AbilitySystem/.../CurveTables/`
- **Asset Manager**: Custom `UAuraAssetManager` registered in `DefaultEngine.ini`
- **Custom collision channel**: `Projectile` (ECC_GameTraceChannel1) with default Ignore response

## Git

- `Saved/`, `Intermediate/`, `Binaries/`, `Build/`, `.vs/`, `.idea/`, `Aura.sln` are gitignored
- Only `Source/`, `Content/`, `Config/`, `Data/` are tracked
- Binary `.uasset` files in `Content/` are tracked (UE blueprints/assets)
