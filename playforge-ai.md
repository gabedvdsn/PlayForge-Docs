# PlayForge — Using PlayForge with an AI assistant

This guide is for an AI coding assistant working in a Unity project that uses PlayForge, and for the person who points
the assistant here. It is for PlayForge 1.0.0.

It gives an assistant what it needs before it writes PlayForge code: a map of the framework, what's authored in The
Forge and what's written in code, the main tasks as code that compiles, how to check the work, and the rules
assistants most often get wrong.

The guides it links, as [EFFECTS.md](EFFECTS.md), ship with PlayForge in its `Documentation` folder:
`Assets/Plugins/PlayForge/Documentation` from the Asset Store, or
`Library/PackageCache/com.faremerald.playforge/Documentation` from git. Read them there, or on the docs site. The API
reference is at https://gabedvdsn.github.io/PlayForge-Docs/api/index.html.

## Setting up your assistant

This guide is one Markdown file, which you download and keep in your project. Nothing loads it on its own: you point
your assistant at it.

Two files are there to download:

- **[playforge-ai.md](https://gabedvdsn.github.io/PlayForge-Docs/playforge-ai.md)**: this guide, the file an assistant
  learns from.
- **[llms.txt](https://gabedvdsn.github.io/PlayForge-Docs/llms.txt)**: a short index of links to the guides and the API
  reference, for an assistant that reads web pages. It points at what to read, and teaches nothing itself.

Save `playforge-ai.md` in your project's root folder, beside `CLAUDE.md` or `AGENTS.md`. Then point your assistant at
it.

**Claude Code** reads `CLAUDE.md`. Add an import to it, on a line of its own:

```text
@playforge-ai.md
```

- The import's path is relative to `CLAUDE.md`.
- An import loads the guide into every session. To have the guide read only when the work needs it, write a line
  instead: "Before you write code that uses PlayForge, read playforge-ai.md."

**Other assistants** read `AGENTS.md`, or a rules file of their own. Add a line to it:

```text
This project uses PlayForge 1.0.0, a gameplay framework for Unity. Before you write code that uses it, read
playforge-ai.md, then the guides it links.
```

**An assistant that reads web pages** needs no file: give it the address of `llms.txt`, or of `playforge-ai.md`.

## The map

PlayForge is a gameplay framework for Unity 6, modelled on Unreal's Gameplay Ability System:

- Each entity has one hub, which owns its attributes, effects, abilities, tags and items.
- ProcessControl steps every hub and process from Unity's player loop.
- Each ability runs as stages of async tasks, on UniTask.

The runtime's types are in the namespace `FarEmerald.PlayForge`, in the assembly of that name. Folders are under the
package's root.

| Concept | Main types | Folder | Guide |
|---|---|---|---|
| Entities | `GameplayAbilitySystem` (a component) and `RuntimeGameplayAbilitySystem` (a plain object), both `IGameplayAbilitySystem`; `EntityIdentity`, the asset one is made from | `Runtime/PlayForge/GAS` | [ENTITIES.md](ENTITIES.md) |
| Attributes | `Attribute`, an asset that's a name; `AttributeSet`, `AttributeSetElement`, `AttributeValue` | `Runtime/PlayForge/AttributeSystem` | [ATTRIBUTES.md](ATTRIBUTES.md), [ATTRIBUTE_SETS.md](ATTRIBUTE_SETS.md) |
| Effects | `GameplayEffect`, `GameplayEffectSpec`, `IEffectOrigin` | `Runtime/PlayForge/GameplayEffect` | [EFFECTS.md](EFFECTS.md), [EFFECT_PIPELINE.md](EFFECT_PIPELINE.md) |
| Abilities | `Ability`, `AbilityBehaviour`, `AbilitySystemComponent`, `ActivationResult`, `AbstractAbilityTask` | `Runtime/PlayForge/AbilitySystem` | [ABILITIES.md](ABILITIES.md) |
| Tags | `Tag`, `TagCache`, and `Tags`, PlayForge's own | `Runtime/PlayForge/Tag` | [TAGS.md](TAGS.md) |
| Items | `Item`, `ItemSystemComponent` | `Runtime/PlayForge/ItemSystem` | [ITEMS.md](ITEMS.md) |
| Workers | `AbstractAttributeWorker`, `AbstractImpactWorker`, `AbstractEffectWorker`, `AbstractTagWorker` | `Runtime/PlayForge/Workers` | [WORKERS.md](WORKERS.md) |
| Numbers | `ScalableFloat`, `ScalableInt`, `AbstractScaler`, `ValuePipeline` | `Runtime/PlayForge/Numbers` | [SCALERS.md](SCALERS.md), [VALUE_PIPELINES.md](VALUE_PIPELINES.md) |
| Processes | `ProcessControl`, `Bootstrapper`, `GameRoot`, `AbstractMonoProcess`, `AbstractRuntimeProcess` | `Runtime/PlayForge/Management` | [PROCESSES.md](PROCESSES.md) |
| Task sequences | `TaskSequence`, `TaskSequenceBuilder`, `SequenceTaskLibrary` | `Runtime/PlayForge/Core/TaskSequence` | [TASK_SEQUENCES.md](TASK_SEQUENCES.md) |
| Saving | `ForgeSnapshot` | `Runtime/PlayForge/Save` | [SAVING.md](SAVING.md) |
| Time, chance, input, logs | `ForgeTime`, `ForgeRandom`, `ForgeInput`, `ForgeLog` | `Runtime/PlayForge/Utils`, `Runtime/PlayForge/Core` | [SERVICES.md](SERVICES.md) |

The other assemblies:

| Assembly and namespace | What it holds | Folder | Guide |
|---|---|---|---|
| `FarEmerald.PlayForge.Editor` | The Forge, the inspectors, the Problems view and `ForgeValidator`, the Debugger | `Editor` | [EDITOR.md](EDITOR.md) |
| `FarEmerald.PlayForge.Lab`, `FarEmerald.PlayForge.Lab.Editor` | The Creator Lab | `Runtime/Lab`, `Editor/Lab` | [CREATOR_LAB.md](CREATOR_LAB.md) |
| `FarEmerald.PlayForge.Testing` | The test kit: `ForgeTest`, `ForgeTestWorld`, `TestAssets` | `TestKit` | [TESTING.md](TESTING.md) |

[GETTING_STARTED.md](GETTING_STARTED.md) tells how the pieces fit, and [EXTENDING.md](EXTENDING.md) covers every
kind a game can add.

## Authored or written

Most of a game made with PlayForge is assets:

- They're authored in The Forge, **Window → PlayForge → The Forge**.
- They're kept in a Forge Project, under `Assets/PlayForge/<project>`.
- Code plays them, and adds kinds of the game's own that their pickers then offer.

| Authored in The Forge | Written in code |
|---|---|
| Attributes and attribute sets | Scripts that play the assets: casting on input, applying an effect on a hit, reading attributes for UI |
| Entities (`EntityIdentity`) | A game's own components, workers, ability tasks, targeting tasks, scalers, value stages, cue handlers and validation rules ([EXTENDING.md](EXTENDING.md)) |
| Effects, among them every cost and cooldown | Processes and task sequences ([PROCESSES.md](PROCESSES.md), [TASK_SEQUENCES.md](TASK_SEQUENCES.md)) |
| Abilities: their targeting, stages, tasks and checks | A game's own data in a save ([SAVING.md](SAVING.md)) |
| Items, value pipelines, scalers, templates and requirement templates | Tests, with the test kit ([TESTING.md](TESTING.md)) |
| Tags, in the Tags view, and the workers on each asset | Tags a game names in code ([TAGS.md](TAGS.md)) |

So:

- **Asked to make an asset,** give the steps in The Forge, with each label as the editor shows it; the guides name
  them. Make it in code only when asked to, as [Making assets in code](#making-assets-in-code) shows.
- **Never write or edit an `.asset` file's YAML.** An asset's components, tasks and workers are `[SerializeReference]`
  lists of polymorphic kinds, which a hand edit easily breaks.
- **A kind of the game's own** needs a public constructor that takes nothing, and `[Serializable]`. Nothing registers
  it: the pickers find it by its base ([EXTENDING.md](EXTENDING.md#before-you-start)).

## A scene that runs PlayForge

A scene that runs PlayForge has these:

- **A Bootstrapper**, which brings up ProcessControl and the Game Root, and plays the active Forge Project. In the
  Hierarchy, right-click and choose **PlayForge → Bootstrapper**.
- **An entity on each object that is one:** a **Gameplay Ability System** component, whose **Entity Data** is the
  entity's `EntityIdentity`.

An entity registers as the scene starts. Until it has, reads such as `TryGetAttributeValue` return false.

This script casts an ability on a key, or at a target it's given:

```csharp
using FarEmerald.PlayForge;
using UnityEngine;

namespace MyGame
{
    public class CastOnKey : MonoBehaviour
    {
        public GameplayAbilitySystem caster;
        public Ability ability;
        public KeyCode key = KeyCode.Alpha1;

        private void Update()
        {
            // ForgeInput reads the Input System or the Input Manager, whichever the project has; the ability system is
            // null until the entity has registered
            if (!ForgeInput.Current.KeyPressed(key) || caster.AbilitySystem == null) return;
            var result = caster.AbilitySystem.TryActivateAbility(ability);
            if (!result) ForgeLog.Info($"{ability.GetName()} refused: {result.Refusal}", this);
        }

        // Casts at a target of the caller's: the activation's data holds it, as an ITarget, where targeting puts its pick
        public bool CastAt(GameplayAbilitySystem target)
        {
            if (caster.AbilitySystem == null) return false;
            var data = ProcessDataPacket.Default();
            data.SetPrimary(Tags.TARGET_REAL, (ITarget)target);
            return caster.AbilitySystem.TryActivateAbility(ability, data);
        }
    }
}
```

- `TryActivateAbility` returns an `ActivationResult`. It's true unless the ability was refused. `Started`,
  `Buffered` and `Refused` say which, and `Refusal` says why: its cooldown, its cost, a requirement, or a busy caster.
- The ability must be one the entity has: a starting ability of its `EntityIdentity`, or given with
  `AbilitySystem.GiveAbility(ability, level, out int slot)`.
- An ability whose targeting asks for a pick, as **Picked → Select Gas** does, waits for the player's click after it
  starts. A cast with a target in its data, as `CastAt` makes, takes that target instead, as an AI's cast does.

## Applying effects and reading attributes

An effect is applied from an origin: the entity, ability or item it comes from. This zone applies its effect to each
entity that enters it, from the zone's owner:

```csharp
using FarEmerald.PlayForge;
using UnityEngine;

namespace MyGame
{
    public class EffectZone : MonoBehaviour
    {
        public GameplayAbilitySystem owner;
        public GameplayEffect effect;

        private void OnTriggerEnter(Collider other)
        {
            if (!other.TryGetComponent(out GameplayAbilitySystem target)) return;
            bool landed = target.ApplyGameplayEffect(IEffectOrigin.GenerateSourceDerivation(owner), effect);
            if (!landed) ForgeLog.Info($"{effect.GetName()} didn't land on {target.name}", this);
        }
    }
}
```

An effect can refuse to land: the target is dead, on the wrong side or immune, a requirement fails, or its chance
misses. Its result says whether it landed.

An attribute's value is an `AttributeValue`, its `CurrentValue` and its `BaseValue`. This bar shows one, Current over
Base:

```csharp
using FarEmerald.PlayForge;
using UnityEngine;
using UnityEngine.UI;
using ForgeAttribute = FarEmerald.PlayForge.Attribute;

namespace MyGame
{
    public class AttributeBar : MonoBehaviour
    {
        public GameplayAbilitySystem entity;
        public ForgeAttribute attribute;
        public Image fill;

        private void Update()
        {
            // False until the entity has registered, and where its set hasn't the attribute
            if (!entity.TryGetAttributeValue(attribute, out AttributeValue value)) return;
            fill.fillAmount = value.ClampedRatio;
        }
    }
}
```

An entity's tags are in its `TagCache`. A tag's weight is how many sources give it, and −1 when none does:

```csharp
using FarEmerald.PlayForge;
using UnityEngine;

namespace MyGame
{
    public class StunnedAnimation : MonoBehaviour
    {
        public GameplayAbilitySystem entity;
        [ForgeTagContext(ForgeContext.Granted)] public Tag stunned;
        public Animator animator;

        private void Update()
        {
            // An absent tag's weight is -1, so test for more than 0; the cache is there once the entity has registered
            var tags = entity.GetTagCache();
            if (tags != null) animator.SetBool("Stunned", tags.GetWeight(stunned) > 0);
        }
    }
}
```

To hear a tag change rather than read it each frame, `GetTagCache().Listen(tag, change => ...)` returns a listener
to dispose ([ENTITIES.md](ENTITIES.md#hearing-tag-changes)). PlayForge's `AttributeReader` and `AbilityReader` show an
entity's state in UI, and [EXTENDING.md](EXTENDING.md#readers) tells how to write a reader of your own.

## Making assets in code

An asset made in code is a `ScriptableObject`, made with `ScriptableObject.CreateInstance<T>()`, its fields filled
as its inspector fills them. It's for tests, and for content made at run time, as the Battle sample's is.

- **Copy from `TestKit/TestAssets.cs`.** It fills every field each kind of asset reads. An asset with one of its lists
  left null throws as it's played, and an effect whose requirement groups are left null is removed as it lands.
- **In a test, the test kit builds them:** `Make.Attribute`, `Make.Set`, `Make.Effect`, `Make.Ability` and the rest,
  destroyed with the test's world.
- **A cost and a cooldown are effects.** The cost is an instant effect on the caster's resource. The cooldown lasts as
  long as the ability can't be cast: while it's on, the caster carries its asset tag, and the ability's cooldown check
  refuses. Both apply as the ability starts, after its targeting, unless a stage is marked **Apply Usage Effects**.
- **Three defaults differ from the inspector's:**
  - a scaler's `StretchOverLevels` is true, so its values stretch over its asset's levels: set it false for one value
    a level;
  - `new ScalableFloat()` adds its scaler to its magnitude, while a float assigned to one, as `= -30f`, is that number
    alone;
  - an `AttributeBackedScaler` reads the caster's attribute: set its `CaptureFrom` to `Target` for the target's.

This test makes an attribute set, two entities, an effect, a cost, a cooldown and an ability, then casts it:

```csharp
using System.Collections;
using System.Collections.Generic;
using FarEmerald.PlayForge;
using FarEmerald.PlayForge.Testing;
using NUnit.Framework;
using UnityEngine.TestTools;
using ForgeAttribute = FarEmerald.PlayForge.Attribute;

namespace MyGame.Tests
{
    public class FireballTests : ForgeTest
    {
        [UnityTest]
        public IEnumerator Fireball_hits_spends_mana_and_cools_down() => Live(async () =>
        {
            // The set: Health a pool between 0 and its base, Mana a stat
            ForgeAttribute health = Make.Attribute("Health"), mana = Make.Attribute("Mana");
            var set = Make.Set(Make.Pool(health, 400f, 400f), Make.Stat(mana, 100f));
            var mage = World.Entity(Hub.Mono, set, "Mage");
            var dummy = World.Entity(Hub.Mono, set, "Training Dummy");

            var damage = Make.Effect("Fireball Damage", new EffectConfig
            {
                Policy = EEffectDurationPolicy.Instant, Target = health, Magnitude = -30f,
            });
            // A wind-up of 0.3 seconds, then the hit
            var fireball = Make.Ability("Fireball",
                Make.Stage(new DelayTask { DelayMilliseconds = 300 }),
                Make.Stage(new ApplyEffectsAbilityTask { Effects = new List<GameplayEffect> { damage } }));
            fireball.Cost = Make.Effect("Fireball Cost", new EffectConfig
            {
                Policy = EEffectDurationPolicy.Instant, Target = mana, Magnitude = -20f,
            });
            fireball.Cooldown = Make.Effect("Fireball Cooldown", new EffectConfig
            {
                Policy = EEffectDurationPolicy.Durational, Duration = 3f,
            });

            Assert.IsTrue(World.Cast(mage, fireball, dummy), "Fireball starts");
            await Frames.Until(() => World.Value(dummy, health) <= 370f, 60, "its hit lands after the wind-up");
            Assert.AreEqual(80f, World.Value(mage, mana), 0.001f, "its cost spent 20 Mana");
            Assert.IsFalse(World.Use(mage, fireball, dummy), "on its cooldown, it's refused");
        });
    }
}
```

## Saving assets from an editor script

When the person asks for assets they won't make by hand, an editor script builds them, and a batch run calls it with
`-executeMethod`. Save each as The Forge's **Create** does: in its Forge Project's folder for its type, named, with
the asset tag its name gives.

- **The Forge Project.** Ask the person to make it in The Forge, which offers to make the first. Code can't name one:
  in a Unity project with none, the first asset `ForgePresets.Make` saves makes one named after the Unity project.
  `ForgeProject.Folders()` lists each project with its folder.
- **An asset with a preset**, as **Create → Gameplay Effect → Damage**: `ForgePresets.Make(preset, name)`, with the
  preset from `ForgePresets.For(type)`, as `ForgePresets.For(typeof(GameplayEffect))`.
- **Any other asset**, as below. Its asset tag is its kind, then its name's words capitalised and joined:
  `Tag.Generate("Effect.FireballDamage", "FireballDamage")`. The kinds are `Effect`, `Ability`, `Entity`, `Item` and
  `AttributeSet`.
- **A picker's label names its class:** the class's name spaced out, less its family's word
  ([EXTENDING.md](EXTENDING.md#how-a-kind-is-named-and-filed)). Return Damage Attribute is
  `ReturnDamageAttributeWorker`, Select Gas is `SelectGasTargetTask`, and Stack Bounds is `StackBoundsConfig`. A
  `[ForgeMenu]` attribute's `Name` gives some a label of their own, so search the source for the label too.
- **A template.** `SetTemplate` alone copies nothing. Copy the template into the new asset with
  `EditorUtility.CopySerialized`, give the asset its own name and tag, then call `SetTemplate(template)`. Change the
  asset's own fields, then call `ForgeTemplatePropagator.UpdateOverrides(asset)` and
  `ForgeTemplatePropagator.CaptureInitialListSnapshots(asset)`. They keep those fields as the asset's own when
  `ForgeTemplatePush.PushToChildren(template)` hands the template's changes down.
- **A scene.** `EditorApplication.ExecuteMenuItem("GameObject/PlayForge/Bootstrapper")` adds the Bootstrapper to the
  active scene, in a batch run too. A new scene unloads saved assets that nothing in the scene uses, so load them again
  by path after `EditorSceneManager.NewScene`.

This script saves an attribute where The Forge would, then checks it:

```csharp
using System.Collections.Generic;
using System.Linq;
using FarEmerald.PlayForge;
using FarEmerald.PlayForge.Editor;
using UnityEditor;
using UnityEngine;
using ForgeAttribute = FarEmerald.PlayForge.Attribute;

namespace MyGame.EditorTools
{
    public static class ArenaAssets
    {
        [MenuItem("Tools/Arena/Make Strength")]
        public static void MakeStrength()
        {
            // The Forge Project the person made, by its name
            var project = ForgeProject.Folders().Select(each => each.Project).First(each => each != null && each.name == "Arena");
            var strength = Save<ForgeAttribute>(project, "Strength");
            strength.Name = "Strength";
            strength.Textures = new List<TextureItem>();
            EditorUtility.SetDirty(strength);
            AssetDatabase.SaveAssets();
            foreach (var problem in ForgeValidator.Check(strength)) ForgeLog.Warning(problem.Message, strength);
        }

        // A new asset where The Forge's Create puts its type: the project's folder for it, the file its name without spaces
        private static T Save<T>(ForgeProject project, string name) where T : ScriptableObject
        {
            string within = PlayForgeSettings.FolderFor(typeof(T), project);
            string folder = within.Length == 0 ? project.Folder : $"{project.Folder}/{within}";
            if (!AssetDatabase.IsValidFolder(folder)) AssetDatabase.CreateFolder(project.Folder, within);
            var asset = ScriptableObject.CreateInstance<T>();
            AssetDatabase.CreateAsset(asset, $"{folder}/{name.Replace(" ", "")}.asset");
            return asset;
        }
    }
}
```

## Checking the work

Check each change in this order:

1. **It compiles.** Unity compiles as its editor takes focus. With the editor closed, a batch run compiles the project,
   and its log has an `error CS` line for each error:
   `Unity.exe -batchmode -quit -projectPath <project> -logFile <log>`. Never start a batch run on a project that's open
   in the editor.
2. **Its assets have no problems.** The Problems view, **Window → PlayForge → Problems**, or the count in The Forge's
   toolbar, checks every asset as it changes. Most problems have a quick fix.
   - In a batch run, `-executeMethod FarEmerald.PlayForge.Editor.ForgeValidator.LogAll` writes each problem to the log
     as `[PlayForge problems] <path>: <message>`, then how many there are.
   - In editor code, `ForgeValidator.Check(asset)` checks one asset, and `ForgeValidator.All` holds every problem, each
     with its `Message` and its quick fix, `Fix`.
   - `LogAll` checks the whole Unity project, samples included. To report on the assets you made, `Check` each one.
3. **It plays as it should.** A test of the test kit's plays it frame by frame ([Testing with the test kit](#testing-with-the-test-kit)).
4. **Look in Play mode.** The Debugger, **Window → PlayForge → Debugger**, shows each entity's attributes (Current,
   then Base), its effects, abilities and tags, and a timeline of each cast. You can't see it: ask the person to look,
   and say what to check.

PlayForge's own golden tests check the install: the Test Runner's EditMode tab, `FarEmerald.PlayForge.Tests.Golden`, or
a batch run's `-executeMethod FarEmerald.PlayForge.Tests.PlayForgeGoldenTests.RunAll`.

## Testing with the test kit

The test kit, `FarEmerald.PlayForge.Testing`, runs a live PlayForge for each test. It steps the player loop a frame at
a time, and checks afterwards what must always hold ([TESTING.md](TESTING.md)).

A project's tests need an assembly definition of their own:

```json
{
    "name": "MyGame.Tests",
    "references": [
        "FarEmerald.PlayForge",
        "FarEmerald.PlayForge.Testing",
        "UniTask",
        "UnityEngine.TestRunner",
        "UnityEditor.TestRunner"
    ],
    "overrideReferences": true,
    "precompiledReferences": [ "nunit.framework.dll" ],
    "autoReferenced": false,
    "defineConstraints": [ "UNITY_INCLUDE_TESTS" ]
}
```

- **Every platform, not the Editor alone.** The kit's tests are PlayMode tests, which step the real player loop.
- **Add the game's own assembly** to `references`, where the game's scripts have an assembly definition.
- **Run them** in the Test Runner's PlayMode tab, or with the editor closed:
  `Unity.exe -batchmode -projectPath <project> -runTests -testPlatform PlayMode -testFilter MyGame.Tests -testResults <results.xml> -logFile <log>`.
  A test run quits by itself, so leave out `-quit`.

A test derives from `ForgeTest`, and its body runs through `Live`, as [Making assets in code](#making-assets-in-code)
shows. `World` makes entities on either hub, `Hub.Mono` or `Hub.Runtime`, and plays them: `Hit`, `Cast`, `Use` and
`Hold`. It reads them with `Value`, `Base`, `Has`, `Stacks` and `HasTag`. `Frames` steps time: `Step(n)`,
`Seconds(s)` and `Until(condition, frames, what)`.

- **The game's saved assets** load through `AssetDatabase`, so a test that plays them is the editor's, under
  `#if UNITY_EDITOR`. `World.FromIdentity(hub, identity)` makes an entity from a saved `EntityIdentity`.
- **Play them under their project's settings**, as its roles, with `ForgeProject.Use(project)`: a test's world plays
  no project of its own. Give back the project it returns as the test ends.
- **An entity's starting abilities are its own already:** start one with `World.Use`. `World.Cast` gives the ability
  first, and fails for one the entity has.
- **A results file's `asserts="0"` is Unity's,** for every test. Its times are short too: each frame lasts a fixed
  game time, and the frames run as fast as the machine can.

## Rules assistants get wrong

- **`Attribute` is two types.** PlayForge's attribute asset, `FarEmerald.PlayForge.Attribute`, shares its name with
  `System.Attribute`. A file with both `using System;` and `using FarEmerald.PlayForge;` can't name it bare. Alias it:
  `using ForgeAttribute = FarEmerald.PlayForge.Attribute;`.
- **`Editor` is a namespace.** In code inside a namespace under `FarEmerald.PlayForge`, a bare `Editor` names
  `FarEmerald.PlayForge.Editor`. Write `UnityEditor.Editor` in full there.
- **An absent tag's weight is −1, not 0.** `TagCache.GetWeight` returns −1 for a tag the entity hasn't. Test
  `GetWeight(tag) > 0`, or use `TryGetWeight`.
- **Components are read through `Find<T>()`.** An asset's, a spec's or an entity's components are found by type:
  `ability.Find<T>()`, `effect.Find<T>()`, `identity.Find<T>()`. No property names a component.
- **Logs go through `ForgeLog`.** `ForgeLog.Info`, `Warning` and `Error` are gated by `ForgeLog.MinimumLevel`, and
  code hears them through `ForgeLog.Logged`. Don't log PlayForge content with `Debug.Log`.
- **Input goes through `ForgeInput`.** `ForgeInput.Current` reads the Input System or the Input Manager, whichever
  the project has, and a test puts in input of its own with `ForgeInput.Use`. Don't read `UnityEngine.Input` in
  content.
- **Time and chance go through `ForgeTime` and `ForgeRandom`**, so a test can step the clock and script the rolls.
- **Current comes before Base.** Wherever the two show together, in UI, logs and summaries, show Current first,
  then Base, as PlayForge's own tools do.
- **A process has one of four lifecycles:**
  - `SelfTerminating` runs and ends, as a projectile;
  - `RunThenWait` runs, then waits to run again, as an ability;
  - `RequiresControl` waits until it's told to run;
  - `Synchronous` is stepped at its timing for as long as it lasts, as a health bar.

  There are no others.
- **ProcessControl steps every process.** Register one with `ProcessControl.Register(process)...Run()`, and never
  call its update by hand ([PROCESSES.md](PROCESSES.md)).
- **An entity steps in LateUpdate.** A Gameplay Ability System's `ProcessTiming` is `UpdateAndLate`. Code that sets it
  keeps Late in it, or the entity's end of frame never runs.
- **Wait with PlayForge's clock.** In an ability task, await `SequenceTaskLibrary.Delay(seconds, token)` rather than
  `UniTask.Delay`, so a paused run stays paused, and pass the token, so an interrupt ends the task
  ([EXTENDING.md](EXTENDING.md#ability-tasks)).
- **Show a tag by `ToString()`**, its display name, not by its `Name`, which is its whole path.
