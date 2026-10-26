---
name: xcode-localization-guard
description: Use when editing Apple-platform code or string catalogs that add or change user-facing UI text, alerts, labels, navigation titles, buttons, empty states, validation messages, formatted copy, or Localizable.xcstrings entries; especially when preventing hardcoded strings or ensuring every existing locale has a real translation instead of an English copy.
---

# Xcode Localization Guard

## Overview

Prevent new hardcoded user-facing strings in Xcode code changes. For any added or modified copy, route text through localization resources, populate every existing target locale, and match the repository's existing localization pattern.

## Working Rule

Treat every newly introduced or modified user-facing string as localizable unless it is clearly non-user-visible. Do not expand scope just to clean unrelated legacy hardcoded strings.

## Workflow

1. Inspect nearby code and the repository's string catalog before choosing an API or key name.
2. If the text is shown to the user, add or update a key in the project's localization resources.
3. Derive the full locale set from the catalog's `sourceLanguage` and existing `localizations` keys; do not assume the project only supports Chinese and English.
4. In SwiftUI, prefer localized-key APIs such as `Text("key")`, `Label("key", systemImage: ...)`, `.navigationTitle("key")`, and similar modifiers that accept localization keys.
5. When code needs a resolved `String`, use `String(localized: "key")`.
6. For parameterized copy, add a format key and use `String(format: String(localized: "key"), locale: .current, ...)`.
7. Keep key namespaces consistent with nearby code instead of introducing unrelated prefixes.

## Complete Locale Coverage

A catalog key is incomplete if only the source language and English are meaningful while other supported locales are missing, stale, or filled with English placeholders.

- Do not copy English into non-English locales to satisfy `Localizable.xcstrings` structure.
- For each added or modified key, provide a language-specific value for every locale already present in that catalog.
- Preserve interpolation placeholders, printf specifiers, Markdown emphasis, punctuation intent, and product names across all locales.
- Exact matches to English in non-English locales require a concrete reason such as a brand name, acronym, unit symbol, or intentionally untranslated proper noun.
- If a trustworthy translation cannot be produced for a locale, leave the work explicitly incomplete in the final response instead of silently using English.

## Allowed Exceptions

Hardcoded strings are acceptable when they are not user-facing. Common examples: debug logs, assertions, analytics/event names, notification identifiers, `systemImage` names, internal enum raw values, persistence keys, protocol fields, URLs, and test fixtures. If there is ambiguity, localize it.

## Project Adaptation

When the repository already has a string catalog and established naming pattern, follow that pattern instead of inventing a new one. For WristScore-style projects, inspect `WristScore/Localizable.xcstrings`, reuse the existing `sb.` namespace, mirror the surrounding feature prefix, and populate every locale already present in the catalog. Read [references/project-patterns.md](references/project-patterns.md) when you need concrete Xcode-specific examples.

## Final Check

Before finishing, scan the changed lines for newly added quoted literals that look user-visible. If a new literal appears in UI code, alerts, sheets, menus, onboarding, empty states, button labels, or notification content, move it into localization resources before concluding. For changed `Localizable.xcstrings` entries, verify each existing locale has a non-placeholder value and that non-English locales are not merely copied from English.
