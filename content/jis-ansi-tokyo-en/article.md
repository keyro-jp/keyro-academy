# Can't Type @ on Your Tokyo Office PC? JIS vs US Layouts Explained

You sit down at your new desk in Tokyo and start your first email. Your fingers reach for `Shift + 2`, expecting `@`. A double quote appears instead. Parentheses feel misplaced, and even a simple line of code takes a second attempt.

If you learned to type on a US keyboard and have just received a Japanese office laptop, this can be a familiar surprise. You have not forgotten how to type. **The physical keyboard, the Windows layout setting, and the input method may be using different rules.**

This guide explains how to separate those causes and try the JIS/ANSI switch in KeyroIME. It includes a product introduction from the KeyroIME team.

[日本語](https://github.com/keyro-jp/keyro-academy/blob/main/content/jis-ansi-tokyo-ja/article.md) · [中文](https://github.com/keyro-jp/keyro-academy/blob/main/content/jis-ansi-tokyo-zh/article.md)

## Three layers behind a keyboard layout

1. **Physical layout:** the number, position, and shape of the keys. Japanese keyboards include keys such as 変換 (conversion) and 無変換 (non-conversion).
2. **Logical layout:** the characters produced by those keys. Windows settings such as Japanese 106/109 and English 101/102 affect this layer.
3. **Input method state:** Japanese or alphanumeric input, full-width or half-width characters, and key handling inside the IME. Application shortcuts can also interfere.

ANSI describes a physical keyboard arrangement; US describes a character layout. People often use the terms together, but they are not identical. ISO keyboards and other national character layouts also exist. A computer outside Japan is not automatically a US-layout computer.

## The punctuation that interrupts emails and code

This table compares typical US English and Japanese JIS keyboards using their matching layout settings. Test in a mode that accepts **half-width alphanumeric characters**. An IME or remapping utility can change the result.

| Character | US English layout | Japanese JIS layout |
| --- | --- | --- |
| `@` | `Shift + 2` | Dedicated `@` key to the right of P |
| `"` | `Shift + '` | `Shift + 2` |
| `(` / `)` | `Shift + 9` / `Shift + 0` | `Shift + 8` / `Shift + 9` |
| `=` | `=` key | `Shift + -` |
| `+` | `Shift + =` | `Shift + ;` |

The conversion keys on a Japanese keyboard serve Japanese input operations. Its spacebar and Enter key may also have different shapes. Changing character mappings does not change the physical keys or make the two keyboards feel identical.

## Diagnose the mismatch in a plain text editor

Before testing in an outgoing email or production code, open Notepad and type:

```text
dev@example.com
"Tokyo" (2026)
total = (a + b)
```

- **`Shift + 2` produces `"`:** Japanese-layout processing may be active. Check the key labels and the layout you intend to use.
- **The symbols are correct but full-width:** check the IME's character-width setting as well as the layout.
- **Notepad works, but another application does not:** check that application's shortcuts and, if applicable, the remote computer's settings.

An input language and a keyboard layout are not the same thing. You can type Japanese with a US keyboard; you do not need Japanese physical keys simply because you need Japanese text. Follow your company's rules when changing settings or installing software on a managed PC.

## Switch JIS and ANSI in KeyroIME

![KeyroIME banner illustrating JIS and ANSI switching with Alt and the semicolon key](https://raw.githubusercontent.com/keyro-jp/keyro-academy/main/media/keyro6-1.png)

KeyroIME Pro for Windows provides a **JIS/ANSI layout switch**. The supplied Japanese banner calls this feature “KANA-SWITCHER.”

1. Install a supported KeyroIME Pro release and select KeyroIME as your input method.
2. Use the layout setting in the tray menu, or press **`Alt + ;`** to switch JIS/ANSI. The shortcut uses the key at the semicolon position.
3. Return to the text field. In a mode that accepts half-width symbols, test `Shift + 2` and the sample above. Check whether the ANSI setting produces `@` and the JIS setting produces the mapping you expect.
4. Repeat the check in the email client and editor you actually use.

This switches KeyroIME's layout setting. It does not reshape your keyboard, relabel its keys, change every other IME, or override every application's shortcuts. If the switch does not respond, first check that KeyroIME is selected and that the application is not intercepting the keys.

**Switching layouts is separate from switching between Japanese and alphanumeric input.** If punctuation works but Japanese input does not, check the input mode before changing the layout again.

## Choose a setting by testing your actual work

Try the same short email or code sample with each setup. Record the time to finish, punctuation corrections, and input-mode changes. Those observations are more useful than a universal typing-speed claim: the right setup depends on your habits and applications.

See the [official releases](https://github.com/keyro-jp/KeyroIME-Releases/releases) for supported environments, license terms, and the current version. The [edition guide](https://github.com/keyro-jp/keyro-academy/blob/main/content/keyro-editions/article.md) explains the product editions in Japanese.

If this helped, Star or Watch [Keyro Academy](https://github.com/keyro-jp/keyro-academy), or follow [KeyroIME on X](https://x.com/KeyroIME) for future guides.

## References

- [Microsoft Japanese IME: keyboards and input controls](https://support.microsoft.com/en-us/windows/hardware/input-devices/microsoft-japanese-ime)
- [Apple: identifying ANSI, ISO, and Japanese keyboards](https://support.apple.com/en-gb/102743)

Checked on September 20, 2026. The KeyroIME instructions are based on the implementation and product documentation for Pro v1.0.6.17 for Windows; they do not guarantee compatibility with every application or a measured speed improvement.
