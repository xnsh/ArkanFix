# Arkan (Rust StringView compatibility fix)

This is a [fork](https://en.wikipedia.org/wiki/Fork_(software_development)) (i.e., a copy with changes) of the **Arkan** plugin for Rust by **Antidote**, originally published on uMod.

https://umod.org/plugins/arkan

Arkan is a player shot analysis tool for admins. It checks player shots for signs of No Recoil, Aimbot, and In Rock violations and reports them to admins.

All credit for the plugin goes to the original author. This fork only fixes a compile error so the plugin loads on current Rust builds.

## Changes in this fork

Based on Arkan **1.0.22**.

- **Fixed compile error caused by Rust's `ConsoleSystem.Arg.Args` change.** A recent Rust update changed `ConsoleSystem.Arg.Args` from `string[]` to `Facepunch.StringView[]`. Arkan assigned these values directly to strings, so it failed to compile with:

  ```
  Error while compiling Arkan: Cannot implicitly convert type 'Facepunch.StringView' to 'string'.
  An explicit conversion exists (are you missing a cast?)
  ```

  An explicit `(string)` cast was added to every `arg.Args[n]` usage in the console command handlers (14 places). This affects:
  - The `arkan.nr`, `arkan.aim`, and `arkan.ir` console commands (player lookup by name or SteamID, and the `0` reset argument)
  - The `arkan.save` and `arkan.load` console commands (file name argument)

Chat commands are not affected, since they still receive `string[] args`.

No other behavior or config changes were made.

## Installation

1. Download `Arkan.cs` from this repo. View it in **raw** format, then save it as `Arkan.cs`.
2. Place it in `oxide/plugins` (or `carbon/plugins`).
3. Grant yourself access:
   ```
   oxide.grant group admin arkan.allowed
   ```
4. In game, type `/arkan` to see the violation list.

Existing Arkan configs and data files work as they are.

Prefer to patch your own copy? Apply `Arkan.patch` to the original Arkan 1.0.22 file.

## Important: reports can be wrong

Arkan's detection logic has not been updated in a long time, and Rust's weapons and recoil have changed since then. Skilled players with good recoil control can be flagged.

**Do not ban based on Arkan reports alone.** Use them as a hint, then confirm by spectating the player, checking their combat log, and reviewing their Steam profile.

## Permissions

- `arkan.allowed` -- Use Arkan commands and view reports
- `arkan.nr.draw`, `arkan.aim.draw`, `arkan.ir.draw` -- Draw violation visuals in game
- `arkan.nr.reportchat`, `arkan.aim.reportchat`, `arkan.ir.reportchat` -- Get violation reports in chat
- `arkan.nr.reportconsole`, `arkan.aim.reportconsole`, `arkan.ir.reportconsole` -- Get violation reports in console
- `arkan.nrwhitelist`, `arkan.aimwhitelist`, `arkan.irwhitelist` -- Skip checks for this player
- `arkan.nrblacklist`, `arkan.aimblacklist`, `arkan.irblacklist` -- Only check players with this permission (when blacklist mode is on)

## Commands

**Chat:** `/arkan`, `/arkaninfo`, `/arkannr`, `/arkanaim`, `/arkanaimr`, `/arkanir`, `/arkanclear`, `/arkansave`, `/arkanload`

**Console:** `arkan`, `arkan.nr`, `arkan.aim`, `arkan.ir`, `arkan.clear`, `arkan.save`, `arkan.load`

## FAQ

#### Why do I get a compilation error?

If the error mentions `Facepunch.StringView`, you are using the original file without this fix. Use the `Arkan.cs` from this repo.

If you get a different error, a newer Rust update may have changed something else. Please open an issue with the full error text.

#### Will you add new detection features?

No. This fork only exists to keep Arkan compiling on newer Rust builds. Bug reports about the compatibility fix are welcome.

## Credits

- **Antidote** -- original author of Arkan

## License

Arkan is licensed under the **GNU General Public License v3.0** (or any later version). See [`LICENSE`](LICENSE).

This fork is a modified version, released under the same license. The modification (dated 2026-11-04) is described in [Changes in this fork](#changes-in-this-fork) and noted at the top of `Arkan.cs`.

This program comes with ABSOLUTELY NO WARRANTY.
