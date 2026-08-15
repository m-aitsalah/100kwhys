# 100K Whys remote content

This folder is a ready-to-upload content package for the Phase 2.5 remote-content system.

## Files

- `manifest.json` declares the supported schema, overall content version, and the CDN URLs for the category and question JSON files.
- `categories.json` contains the 12 application categories.
- `questions.json` contains the current 60 Why questions in one list, which is the format expected by the app parser.

## Before publishing

Replace these two placeholders in `manifest.json` with the final absolute HTTPS URLs served by GitHub/jsDelivr or another CDN:

- `__CATEGORIES_URL__`
- `__QUESTIONS_URL__`

The Flutter app manifest endpoint itself is configured separately in `lib/data/content_config.dart` through `--dart-define=WHY_CONTENT_MANIFEST_URL=...`.

## Versioning

Increase `contentVersion` whenever either file changes; the app uses it to decide whether a remote package is newer than its active content. Also increase the relevant entry version: `categories.version` for category changes and `questions.version` for question changes. Keep `schemaVersion` at `1` unless the Flutter parser is deliberately upgraded.

## Adding questions safely

Add a new question object to `questions.json` without changing existing IDs. Give it a unique stable `id`, a valid existing `categoryId`, exactly four quiz answers, a `correctIndex` from `0` to `3`, at least two steps, at least one fun fact, and `relatedIds` that refer only to question IDs present in this file. Run:

```bash
dart run tool/validate_remote_content.dart remote_content
```

For a future package whose question count intentionally changes, use:

```bash
dart run tool/validate_remote_content.dart remote_content --allow-question-count-change
```

## Safety and fallback

The app validates remote JSON before caching or activating it. If the network is unavailable, the manifest is malformed, or validation fails, the last known-good cache remains active; if no cache exists, the bundled JSON in the APK remains the fallback. Existing progress is keyed by stable question IDs.
