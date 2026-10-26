# Xcode Localization Patterns

Use this reference when the target repository already has localization infrastructure and you need to match it instead of inventing a new convention.

## WristScore Signals

- The project stores user-visible copy in `WristScore/Localizable.xcstrings`.
- Existing keys use the `sb.` namespace.
- The catalog's `sourceLanguage` is `zh-Hans`; existing target locales include `en`, `de`, `es`, `fr`, `it`, `ja`, and `ko`. Re-check the catalog before editing because supported locales may change.
- SwiftUI commonly uses key-based APIs such as `Text("sb.feature.title")`, `Label("sb.feature.action", systemImage: ...)`, and `.navigationTitle("sb.feature.nav_title")`.
- Resolved strings commonly use `String(localized: "sb.feature.message")`.
- Formatted strings commonly use `String(format: String(localized: "sb.feature.count_format"), locale: .current, value)`.

## Default Decision Rule

If a string will be rendered to a person in the app, on the watch, in a notification, or in any user-visible export, localize it. If the string is purely internal, keep it hardcoded only when localization adds no value.

## Locale Coverage Rule

For every added or modified `sb.` key, fill every locale already present in `WristScore/Localizable.xcstrings`. Do not populate `de`, `es`, `fr`, `it`, `ja`, `ko`, or other non-English locales by copying the English value. If a value is identical to English, it should be because the text is a brand name, acronym, unit, or other intentionally untranslated proper noun.

When editing format strings, keep placeholders equivalent in every locale:

```text
%d, %@, %.1f, {name}, and similar placeholders must appear with compatible meaning in each translation.
```

## Common Safe Patterns

```swift
Text("sb.rule_center.nav_title")

Label("sb.common.delete", systemImage: "trash")

.navigationTitle("sb.history.title")

let title = String(localized: "sb.premium.purchase_success")

let body = String(
    format: String(localized: "sb.notification.daily_share_reminder_body_format"),
    locale: .current,
    todaysRecords.count
)
```

## Risky Patterns To Replace

```swift
Text("我的信息")

Button("删除") { ... }

let message = "还没有人物"

.navigationTitle("人物")

content.body = "今天记录了 \(count) 场比赛"
```

Replace those with keys in `Localizable.xcstrings` and then reference the keys from Swift.

## Scope Discipline

- Block new hardcoded user-visible strings.
- Do not widen scope solely to clean historical literals outside the change you are making.
- If you touch an existing hardcoded literal because the feature requires changing that text, localize it as part of the same edit.
- If you are unsure whether a string is user-visible, treat it as user-visible.

## Key Naming Guidance

- Reuse the nearby feature prefix, for example `sb.rule_center.*`, `sb.history.*`, or `sb.common.*`.
- Prefer descriptive suffixes such as `title`, `subtitle`, `message`, `empty_title`, `empty_body`, `button`, `confirm`, or `format`.
- Keep format strings in the catalog instead of concatenating localized fragments into a sentence.

## Final Review Checklist

1. Did the diff add any new quoted natural-language literals in Swift files?
2. If yes, are they truly internal and non-user-visible?
3. If not, is there a matching key in `Localizable.xcstrings`?
4. Does the Swift code use the correct API for that key?
5. For formatted strings, does the code use `locale: .current`?
6. Did every changed string catalog key get values for `zh-Hans`, `en`, `de`, `es`, `fr`, `it`, `ja`, and `ko` or the current locale set derived from the file?
7. Are any non-English values identical to English without a proper-noun or symbol reason?
