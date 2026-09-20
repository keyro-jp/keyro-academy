# 刚到东京上班，连 @ 都打不出来？看懂 JIS 与 US 键盘布局

坐到东京办公室的新工位，准备写第一封英文邮件。你习惯性按下 `Shift + 2`，期待出现 `@`，屏幕上却是双引号 `"`。接着，括号的位置不对，写一行代码也要反复修改。

如果你熟悉美式键盘，刚换上公司配发的日式笔记本，这种困惑并不奇怪。不是你突然不会打字了，而是**物理键盘、Windows 的布局设置和输入法状态，可能采用了不同的规则**。

这篇文章从在日职场场景出发，介绍常见键位差异、排查方法，以及 KeyroIME 的 JIS／ANSI 切换功能。本文包含 KeyroIME 开发方的产品介绍。

[日本語](https://github.com/keyro-jp/keyro-academy/blob/main/content/jis-ansi-tokyo-ja/article.md) · [English](https://github.com/keyro-jp/keyro-academy/blob/main/content/jis-ansi-tokyo-en/article.md)

## 先分清三件事：键盘形状、字符映射、输入法状态

1. **物理布局**：按键数量、位置和形状。日式键盘上常见「変換」「無変換」等日语输入功能键。
2. **逻辑布局**：按下某个键，系统把它解释成什么字符。例如 Windows 中的日语 106/109 与英语 101/102 键盘设置。
3. **输入法状态**：日语或英数字、全角或半角，以及输入法自身的按键处理。应用快捷键也可能影响结果。

ANSI 主要描述物理按键排列，US 描述美式字符布局，二者并不是同一个概念。日常交流中常把它们一起称为“美式布局”，但海外还存在 ISO 键盘及不同国家的字符布局，并非所有电脑都使用 US 布局。

## 邮件和代码里最容易按错的符号

下表比较常见 US 英语键盘与日语 JIS 键盘，各自使用匹配的布局设置时的按键。请在**可以输入半角英数字的状态**下测试；输入法或改键工具可能改变输出。

| 目标字符 | US 英语布局 | 日语 JIS 布局 |
| --- | --- | --- |
| `@` | `Shift + 2` | P 右侧独立的 `@` 键 |
| `"` | `Shift + '` | `Shift + 2` |
| `(` / `)` | `Shift + 9` / `Shift + 0` | `Shift + 8` / `Shift + 9` |
| `=` | `=` 键 | `Shift + -` |
| `+` | `Shift + =` | `Shift + ;` |

日式键盘的转换键服务于日语输入操作。空格键和 Enter 的形状也可能不同，因此改变字符映射，并不会改变实体按键形状，也不会让两种键盘的手感完全相同。

## 先在记事本里定位问题

不要直接在待发送邮件或生产代码里试错。打开记事本，输入以下示例：

```text
dev@example.com
"Tokyo" (2026)
total = (a + b)
```

- **`Shift + 2` 输出 `"`**：可能正在按日语布局处理。检查键帽标记，以及你希望使用的布局。
- **符号正确，但变成全角**：同时检查输入法的全角／半角状态。
- **记事本正常，其他应用异常**：检查应用快捷键；使用远程桌面时，还要检查远端设置。

输入语言不等于键盘布局。需要输入日语，并不意味着必须使用日式实体键盘。公司管理的电脑，修改配置和安装软件时应遵循内部规则。

## 使用 KeyroIME 切换 JIS／ANSI

![KeyroIME 的 JIS 与 ANSI 切换宣传图，展示 Alt 加分号快捷键](https://raw.githubusercontent.com/keyro-jp/keyro-academy/main/media/keyro6-1.png)

Windows 版 KeyroIME Pro 提供 **JIS／ANSI 布局切换**。上方原图用“KANA-SWITCHER”介绍这一功能。

1. 安装支持该功能的 KeyroIME Pro，并选中 KeyroIME 输入法。
2. 通过托盘菜单的布局设置，或按 **`Alt + ;`** 切换 JIS／ANSI。快捷键使用分号所在位置的按键。
3. 回到输入框，在可以输入半角符号的状态下，测试 `Shift + 2` 和上面的例文。确认 ANSI 设置下是否输出 `@`，JIS 设置下是否符合预期映射。
4. 在常用邮件客户端和编辑器中再测试一次。

这项操作改变的是 KeyroIME 的布局设置，不会改变实体键盘的形状和印字，也不意味着其他输入法或所有应用的快捷键都会一起改变。如果切换没有响应，先确认选中的输入法是 KeyroIME，以及应用是否拦截了快捷键。

**布局切换与日语／英数字切换是两种不同操作。** 如果符号正常但无法输入日语，先检查输入模式，不必反复修改布局。

## 用自己的工作内容，判断哪种设置更顺手

选择一段常写的邮件或短代码，用相同内容比较不同设置，记录完成时间、符号修正次数和输入模式切换次数。这样更容易判断是否适合自己，而不是依赖没有实测依据的“提速百分比”。

KeyroIME 的支持环境、使用许可和最新版本，请查看[官方发布页](https://github.com/keyro-jp/KeyroIME-Releases/releases)。各版本区别见[日文功能指南](https://github.com/keyro-jp/keyro-academy/blob/main/content/keyro-editions/article.md)。

如果本文对你有帮助，欢迎给 [Keyroアカデミー](https://github.com/keyro-jp/keyro-academy) 点 Star、通过 Watch 关注更新，或关注[官方 X](https://x.com/KeyroIME)。

## 参考资料

- [Microsoft 日语输入法：键盘与输入操作](https://support.microsoft.com/en-us/windows/hardware/input-devices/microsoft-japanese-ime)
- [Apple：识别 ANSI、ISO 和日语键盘](https://support.apple.com/en-gb/102743)

核对日期：2026-09-20。KeyroIME 部分以 Windows 版 Pro v1.0.6.17 的实现及产品资料为依据，不代表对所有应用兼容性或实际提速幅度的保证。
