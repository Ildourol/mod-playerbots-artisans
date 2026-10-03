# mod-playerbots-artisans

Makes crafter bots feel like **players with a trade**. On a timer, bots with a
crafting profession advertise in Trade (and optionally General) chat, naming
**real recipes they actually know** — so the ads are level-appropriate for free:

```
[Trade] Enchanter (300) LFW - [Enchant Weapon - Crusader], [Enchant Cloak - Greater Resistance]. Whisper me if interested!
[Trade] Alchemist (285) LFW - [Greater Arcane Elixir], [Elixir of the Mongoose]. Whisper me for crafts!
```

For [AzerothCore](https://www.azerothcore.org/) +
[Playerbots](https://github.com/liyunfan1223/mod-playerbots).

## Why it's automatically level-appropriate

You don't need a "bots grind their profession" system — the Playerbots factory
already sets each bot's skill to `level x 5` (level-20 -> 100, level-60 -> 300) and
re-runs as bots level. This module reads each bot's **learned recipes** and current
skill straight from its spellbook, so a low-level bot only advertises the cheap
recipes it truly knows and a 300 bot advertises its best (ads are sorted by recipe
difficulty). Enchanters advertise enchant names; crafters advertise the item they
produce.

## Features

- Recipe-driven, level-appropriate Trade/General advertising.
- Clickable item and enchantment chat links that players can inspect and click.
- Reliable channel posting - posts straight to the faction-wide "Trade - City"
  channel bots join at login, **bypassing the Playerbots `SayToChannel` zone
  filter** that otherwise silently drops Trade messages.
- Conversational whisper replies: players whispering an advertiser receive a breakdown
  of the bot's trade and known recipes.
- Per-bot cooldown and a per-tick cap to prevent Trade channel spam.
- Coordinates via the shared `BotActivityRegistry.h`: bots busy in a dungeon run,
  raid sim, or world-PvP event (or in combat / an instance) do not advertise.

## Requirements

- AzerothCore + mod-playerbots.
- (Optional) the dungeon-sim / world-PvP mods if you want the shared reservation
  to actually gate anything — otherwise the registry is simply always "free."

## Installation

```bash
cd azerothcore/modules
git clone https://github.com/Ildourol/mod-playerbots-artisans.git
# re-run CMake configure, then rebuild
```

Keep `src/BotActivityRegistry.h` byte-identical to the copies shipped with the
other mods. Copy `conf/mod_playerbot_artisans.conf.dist` to your worldserver directory as `mod_playerbot_artisans.conf`.

> **Note on naming:** AzerothCore derives the loader function from the *folder*
> name, so this module's folder must be `mod-playerbots-artisans` (matching
> `Addmod_playerbots_artisansScripts`). The `.cpp` filename doesn't matter.

## Configuration

`conf/mod_playerbot_artisans.conf.dist`:

| Key | Default | Meaning |
|---|---|---|
| `PlayerbotArtisans.Enable` | 1 | Master switch |
| `PlayerbotArtisans.TickSeconds` | 45 | Seconds between ad batches |
| `PlayerbotArtisans.AdsPerTick` | 2 | Ads per batch |
| `PlayerbotArtisans.MinSkill` | 100 | Min skill to advertise |
| `PlayerbotArtisans.AdvertiseCount` | 2 | Recipes named per ad |
| `PlayerbotArtisans.PerBotCooldownSeconds` | 900 | Per-bot re-advertise delay |
| `PlayerbotArtisans.UseTradeChannel` | 1 | Post in Trade |
| `PlayerbotArtisans.UseGeneralChannel` | 0 | Post in zone General |
| `PlayerbotArtisans.UseItemLinks` | 1 | Use clickable item/enchant links |
| `PlayerbotArtisans.AnswerWhispers` | 1 | Reply to player whispers with recipes |
| `PlayerbotArtisans.WhisperRecipeCount` | 5 | Recipes listed in whisper reply |
| `PlayerbotArtisans.WhisperMemorySeconds` | 3600 | Duration bot remembers its ad |
| `PlayerbotArtisans.VaryAdWording` | 1 | Vary ad phrases across bots |

## Roadmap

This is the **advertising** layer. Fulfilment (a player takes a bot up on its ad)
is a natural next step: Playerbots already has craft + trade actions with a fee, so
whisper/trade -> craft with the player's mats + fee -> trade it back is the intended
path. The auction house is left to AHBot on purpose.

## Credits

Built on AzerothCore and mod-playerbots.