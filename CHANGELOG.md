# Changelog

## 1.2.0 — 加载器更新后可以自助补齐界面文案

**新增「更新汉化（加载器更新后）…」**

Mod Loader 更新后，它新增或改动的界面文案不会自动翻译。现在汉化助手可以自动找出来：

1. 从 `DressmakerMods.dll` **直接提取全部界面文案**（解析 .NET 的 #US 字符串堆），
   与上次扫描的快照对比，**只列出新增/改动的**；
2. 导入插件记录的 `untranslated.txt`（游戏里出现过但没有译文的界面文本）；
3. 列表支持**多选/全选**，可用**内置词典（离线）**或 **API** 翻译，也可手工填；
4. 保存后写入 `ui-zh.tsv`（插件优先读取），**重启游戏即生效** —— 不需要等本模组出新版本。

实测：从 DressmakerMods.dll 提取出 2831 条用户字符串，其中 1669 条被识别为界面文案。

**其它**：新增 `--dlltest` 诊断命令，可单独检查 DLL 文案提取结果。

## 1.1.2

* API 出错时显示服务器返回的原始 JSON，并区分 401/403/402/429/400 的原因；Key 格式提示只对 deepseek/openai 生效且可关闭。
* **补词窗口支持多选/全选**，每 40 个一批并显示进度。
* 新增 `--apitest` 诊断；修正"窗口未显示时弹窗导致 Key 被误清空"的问题。
* API Key 改为 Windows DPAPI 本机加密存储；**每次更新后首次启动需重新确认 Key**；设置里新增「清除已保存的 Key」。

## 1.1.1

**API 报错现在会说清楚原因**

* HTTP 失败时读取并显示服务器返回的原始 JSON，例如 DeepSeek 的
  `Authentication Fails, Your api key: ****c10 is invalid`；
* 401/403/402/429/400 各自附带原因提示（Key 无效 / 余额不足 / 频率过高 / 模型名或 Base URL 不对）；
* 「设置…」里点『测试连接』时，如果 Key 不像 API Key（DeepSeek / OpenAI 的 Key 以 `sk-` 开头）会先弹格式提醒；
* 新增命令行诊断：`汉化助手.exe --apitest`（结果写入 `apitest_result.txt`）。

**词典扩充约 130 条**，补齐 Mermaid Fantasy / Forest Nymph 等新模组用到的词
（Luminous 夜光、Kiss 亲吻、Song 之歌、Venomous 剧毒、Crowned 加冕、Moonlight 月光、Nursery 童趣、
Familiar 使魔、Nymph 仙子、Toad 蟾蜍、Hummingbird 蜂鸟、Potion 药水……）以及动物/植物/天气/宝石/蕾丝工艺等常用词。

**未命中词主动提示**：一键汉化结束后若有词典不认识的词，会弹窗列出并询问是否立即补词；
补词结果写入 `dict\learned.tsv`，永久离线生效。

**修复**：`zh-cn.txt` 被反复回写导致文件膨胀（136 KB → 1.8 MB）；界面词典条目改为统一写入 `ui-zh.tsv`。

## 1.1.0 — 根治"汉化总是不全"

**根因修复（这是之前反复漏翻的真正原因）**

* `JavaScriptSerializer` 把 JSON 数组反序列化成 `ArrayList`，而编辑器代码写的是 `arr as object[]`
  → 永远返回 `null` → **编辑器从来没有真正读取过 `mod.json` 里的物品名**。
  于是只有插件"待译清单"里出现过的名字才有原文，其余条目一律拿**本地化键**去翻译，
  自然翻不出来、留下半英文。
* 修复后立即读到 **520 条 键→英文名 映射（516 个条目）**，此前为 **0**。

**新增的四道防线（不再靠"补几个词"救火）**

1. **补词机制**：翻译过程中自动收集所有未命中词，主界面「补词（未命中词）…」列出它们，
   可手工填写或用 API 一键翻译，结果写入 `dict\learned.tsv`，**永久离线生效**。
2. **原文永不丢失**：`content-zh.tsv` 改为三列（键 / 原文 / 中文），插件同时兼容两列与三列；
   以后重复扫描永远能拿到原文，不会再出现"用键名翻译"。
3. **残次译文自动重翻**：已保存但还夹着英文、且与标准译文不一致的条目会自动标记为「待定」
   并在下次一键汉化时重翻；用户手动改成纯中文的条目不会被覆盖。
4. **API 失败不再静默**：失败时弹窗显示真实错误并把原始响应写入 `api_error.txt`；
   返回内容无法按行解析时也会明确报错（此前会静默把全部条目留成"待定"）。

**其它修复**

* 名称中混入制表符、末尾带冒号/句号导致分词失败的问题（`Woodlands:` 现在能正常查词）。
* `A in/on B` 语序规则统一为 B+A（`Capitoné in Ruby` → 红宝石绗缝），专名短语由词典整条保护。
* 模组 ID 含点号（如 `colourful.stitches`）时分组重复的问题。
* 词典新增约 150 条术语（明线、缝线、星点、孔雀石、热带、向日葵、祖母方格、绗缝、斜纹……）。

**游戏目录自动检测（正版 / 盗版都适用）**

* 先从工具自身位置向上查找（把包解压进游戏目录的情况）；
* 正版：读注册表 `Software\Valve\Steam\SteamPath` → 解析 `steamapps\libraryfolders.vdf` 的所有库
  → 在 `steamapps\common\*` 中查找含 `Dressmaker.exe` 的目录；
* 其它情况：对所有固定磁盘做"盘根 → 一级 → 二级"浅扫描（跳过系统目录），并检查 Steam 库的深层结构；
* 找到后自动记住，找不到才提示手动选择。

## 1.0.2

Fixed a column-parsing bug in the editor that made every newly exported name look half-translated.

* The plugin exports pending names as three columns (`key`, `english`, `type`); the editor read the line
  with a single tab split, so the English name silently swallowed the trailing `type` column
  (`Bats in Midnight` + `Fabric`). The translator then treated `MidnightFabric` as one unknown word,
  marked the entry as *pending* and kept English — in both dictionary and API mode.
* The editor now splits all columns, strips stray tabs from every name, and the same fix applies to the
  exported English column when reading.
* Mod group names in the tree are now unified (`模组:<pack display name>` instead of one node per mod id
  plus one per pack name), so a mod no longer appears twice.
* Dictionary: added ~30 terms for fabric techniques and patterns (Capitoné/Tufting 绗缝,
  Granny Squares 祖母方格, Diagonal 斜纹, Thorn Collection 荆棘系列, Bats 蝙蝠 …) and protected
  proper-noun phrases such as `Alice in Wonderland` from the `A in B` word-order rule.
* Game-folder auto-detection also probes common install paths (`D:\Dressmaker`, Steam libraries) and the
  window now shows a prominent "扫描源 / scan source" line with the mod and entry counts.

## 1.0.1

* Stronger game-folder detection; the editor is now shipped pre-configured inside `<Game>\汉化助手\`.
* New `--scan <game folder>` diagnostic mode (`scan_report.txt` with per-mod entry counts).

## 1.0.0

First public release.

**In-game plugin (`BepInEx\plugins\DressmakerModsCN`)**

* Mod Loader UI localization: 2,338 UI strings (settings tabs, setting titles & descriptions,
  mod manager, install/update dialogs, warnings and errors).
* Content localization through the game's own localization pipeline:
  * postfix patch on `LocalizedContent.Get(table, key, englishDefault)`,
  * plus injection of the translated entries into the current locale's `Content` string table,
  * covers fabrics, accessories, garment components/parts, item tags, characters and quests.
* 441 content keys translated (all mod content plus game content that has no official Chinese).
* Generated names (`Split Skirt SkirtRight 17`, `Ruffle Bodice BodiceBack, BodiceLeft 7`) are
  translated with a 218-entry word table.
* Automatic Chinese font fallback (uses the game's own `SourceHanSansSC`) so no missing-glyph boxes.
* Self-checks in `BepInEx\LogOutput.log`; `untranslated.txt` lists anything still English.

**Editor (`汉化助手\汉化助手.exe`)**

* Single-file WinForms app — no runtime installation required.
* Auto-detects the game folder; scans `Mods\*\mod.json` and the plugin's exported pending list.
* Tree view grouped by mod; filterable, sortable table; editable translations.
* One-click translation with the built-in offline dictionary
  (3,166 official terms + 2,438 built-in terms, 99% hit rate on 2,600 real mod names).
* Optional API translation: DeepSeek, any OpenAI-compatible endpoint, DeepL, Baidu, Youdao.
* Find & replace / bulk edit with regular expression support.
* Ignore list, untranslated export, automatic timestamped backups and restore.

**Notes**

* No game or mod files are modified. Uninstall by deleting the two folders.
* Dictionary terms are seeded from the game's official zh-Hans localization.
