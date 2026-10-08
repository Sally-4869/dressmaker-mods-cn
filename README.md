# Dressmaker Mods — Chinese Localization / 汉化模组

Chinese (Simplified) localization for **Yuuma's Dressmaker Mod Loader** and for the content added by mods
(fabrics, accessories, garment parts, tags).

针对《针影裁梦》(Dressmaker) 的 **Mod Loader 界面 + mod 内容名** 简体中文汉化。

---

## What it does / 功能

| | |
|---|---|
| Mod Loader UI | ~2,300 UI strings translated (settings, mod manager, dialogs, messages) |
| Mod content names | All fabric / accessory / garment / tag names are translated **through the game's own localization tables** — they show up in the shop, design book, filters and tooltips |
| Built-in offline dictionary | ~3,600 official game terms + ~2,400 community terms — works with **no internet and no API key** |
| Optional API mode | DeepSeek / any OpenAI-compatible endpoint / DeepL / Baidu / Youdao — you supply your own key |
| Editor | Tree view by mod, editable table, find & replace, ignore list, automatic backups |
| Safety | No game or mod file is ever modified. Uninstall = delete two folders |

## Requirements / 前置

* Dressmaker (the mod loader itself: *Yuuma's Dressmaker Mod Loader*, Nexus mods/1)
* Windows 10/11, .NET Framework 4.x (already part of Windows — no runtime install needed)

## Install / 安装

1. Extract **everything in this archive into your game folder**, so you get:

   ```
   <Game>\BepInEx\plugins\DressmakerModsCN\   (required — the in-game part)
   <Game>\汉化助手\汉化助手.exe                (optional but recommended — the editor)
   ```

2. Launch the game **once** and quit. The plugin writes `content-pending.tsv`
   (a list of the content names that are not translated yet).
3. Run `汉化助手.exe`:

   * it auto-detects the game folder (or use **浏览…** to pick it),
   * press **▶ 开始一键汉化** — with the built-in dictionary this needs no network,
   * review / edit anything you like in the table,
   * press **保存译文**.
4. Start the game again — everything is Chinese.
5. Later, after installing new mods, just repeat steps 2–4.

> The tool writes only into `<Game>\BepInEx\plugins\DressmakerModsCN\` and keeps a timestamped backup
> of the previous files in `汉化助手\backup\`.

## When the Mod Loader itself gets updated / Mod Loader 更新后怎么办

New or changed UI strings from a loader update are not translated automatically — but the assistant can find them:

1. Launch the game once and quit (the plugin records UI text that has no translation).
2. Open the assistant and press **「更新汉化（加载器更新后）…」**.
3. Keep both sources checked and press **重新扫描** — the list shows only the new strings.
4. Press **全选未填**, then **用词典翻译选中** (free, offline) or **用 API 翻译选中**.
5. Press **保存并重新扫描**, restart the game — done.

Source ① extracts all UI strings **directly from `DressmakerMods.dll`** and diffs them against the previous scan
(so only new strings are listed); source ② imports `untranslated.txt`, the plugin's on-screen record of
untranslated text. You do not have to wait for a new release of this mod.

## Known issue — please read / 已知问题（请务必阅读）

**Do not use the “打开游戏 / Launch game” button inside the assistant.**
It can occasionally start the game in its vanilla (no-mods) state, which may make your in-game saves
appear to be gone. Always launch through **Steam** or by double-clicking `Dressmaker.exe` in the game folder.
If it happens anyway: start the game normally (with mods) once, then use the **Mod Loader's save-backup /
restore-save** feature to restore the matching backup — the Mod Loader keeps automatic save backups.
The assistant itself never touches saves; it only reads and writes translation files.

## API mode (optional) / 可选联网翻译

Open **设置…**, choose *API 模式*, pick a provider and paste your key:

| Provider | What to fill in |
| --- | --- |
| DeepSeek | Base URL `https://api.deepseek.com/v1`, model `deepseek-chat`, API Key |
| OpenAI or compatible | your gateway's Base URL + model name + key |
| DeepL | API Key (free tier works) |
| Baidu / Youdao | appid/appkey pair |

**测试连接** verifies the setup before translating. Your key is stored locally in
`汉化助手\config.json` and is never sent anywhere except the provider you chose.

## Files / 文件说明

```
BepInEx\plugins\DressmakerModsCN\
    DressmakerModsCN.dll    the in-game plugin
    zh-cn.txt               UI strings (English<TAB>Chinese)
    content-zh.tsv          content names keyed by the game's localization keys
    ui-zh.tsv               content/pack names as display text (maintained by the editor)
    tokens.tsv              word table used for generated names ("Split Skirt SkirtRight 17")
    说明.md                 detailed notes (Chinese)
汉化助手\
    汉化助手.exe            the editor (single file, no installation)
    dict\official.tsv       official game terms (highest priority)
    dict\builtin.tsv        built-in offline dictionary
    backup\                 automatic backups of your translation files
```

## Notes / 说明

* The dictionary is seeded from the game's own official zh-Hans localization, so material and colour
  terms stay consistent with the base game (Cotton 棉布, Silk 真丝, Wool 羊毛呢, Lace 蕾丝 …).
* Translation quality of the offline dictionary is good for item names (the vast majority of mod
  content) and basic for long sentences — use API mode for free-form text.
* Nothing here redistributes mod content or game assets; it is only code and translations.
* Uninstall: delete `BepInEx\plugins\DressmakerModsCN` and the `汉化助手` folder.

## Credits / 致谢

* Yuuma — Dressmaker Mod Loader
* Translation terms derived from the game's official Simplified-Chinese localization
* Community terminology contributions are welcome — export your `dict\*.tsv` and share it.

## Changelog

See `CHANGELOG.md`.
