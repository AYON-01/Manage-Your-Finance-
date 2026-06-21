# Manage Your Finance 💸

A cross-platform personal finance app built with **Flutter**, designed specifically
for students to track income/expenses, set category budgets, and visualize spending —
**100% offline**, using a local **Hive** database. Dark, modern fintech UI.

---

## Features

- **Local profile setup** (name + optional 4-digit PIN) — no backend, no internet required.
  This personalizes the app and gives a soft privacy lock, but is *not* secure authentication.
- **Dashboard** — total balance, this month's income & expenses, budget watch, recent activity.
- **Transactions** — add/delete income or expense entries with category, date, and notes.
  Searchable & filterable transaction history grouped by day.
- **Budgets** — set a monthly spending limit per category; live progress bars show how
  close you are to (or over) the limit.
- **Reports** — pie chart of spending by category (tap a slice to highlight it) and a
  6-month bar chart trend, plus a dedicated budget-performance view.
- **Offline data persistence** — everything is stored locally via **Hive**, so the app
  works fully without an internet connection and data survives app restarts.

---

## Project Structure

```
manage_your_finance/
├── pubspec.yaml                  # Dependencies & project config
├── analysis_options.yaml         # Lint rules
├── lib/
│   ├── main.dart                 # App entry point + routing logic
│   ├── models/                   # Hive data models
│   │   ├── transaction_model.dart   (+ .g.dart adapter)
│   │   ├── budget_model.dart        (+ .g.dart adapter)
│   │   └── user_profile_model.dart  (+ .g.dart adapter)
│   ├── services/
│   │   └── hive_service.dart     # All local DB read/write logic
│   ├── providers/                # State management (Provider package)
│   │   ├── finance_provider.dart # Transactions + derived totals/charts data
│   │   ├── budget_provider.dart  # Monthly category budgets
│   │   └── user_provider.dart    # Local profile / PIN
│   ├── screens/
│   │   ├── welcome_screen.dart        # Name + avatar entry
│   │   ├── pin_setup_screen.dart      # Optional PIN creation
│   │   ├── pin_lock_screen.dart       # PIN entry for returning users
│   │   ├── dashboard_screen.dart      # Home tab
│   │   ├── add_transaction_screen.dart
│   │   ├── transactions_screen.dart   # Full searchable/filterable list
│   │   ├── budgets_screen.dart        # Set/edit budgets per category
│   │   ├── reports_screen.dart        # Pie chart + bar chart
│   │   └── settings_screen.dart       # PIN management, reset app, about
│   ├── widgets/
│   │   ├── main_navigation.dart  # Bottom nav bar shell
│   │   ├── balance_card.dart
│   │   ├── transaction_tile.dart
│   │   └── budget_progress_tile.dart
│   └── utils/
│       ├── app_theme.dart        # Dark fintech color palette & ThemeData
│       ├── categories.dart       # Category list + icon/color mapping
│       └── currency_formatter.dart
└── assets/
    └── icons/
```

---

## Setup Instructions

### 1. Prerequisites
Install the [Flutter SDK](https://docs.flutter.dev/get-started/install) (stable channel,
Flutter 3.x+ / Dart 3.x+). Verify with:
```bash
flutter doctor
```

### 2. Create the project shell
Since this code was generated outside of a Flutter environment, you need to let Flutter
generate the native `android/` and `ios/` folders (build files, Gradle config, Xcode
project, etc.) — these are large, machine-generated, and shouldn't be hand-written.

```bash
flutter create --org com.yourname --project-name manage_your_finance manage_your_finance_app
```

Then copy this project's `lib/`, `pubspec.yaml`, `analysis_options.yaml`, and `assets/`
folders into the newly created `manage_your_finance_app/` directory, overwriting the
defaults Flutter generated for those specific paths.

### 3. Install dependencies
```bash
cd manage_your_finance_app
flutter pub get
```

### 4. Generate Hive adapters (optional — already hand-written)
The `.g.dart` adapter files are already included and hand-written to match what
`build_runner` would generate. If you ever **add or change fields** on any model in
`lib/models/`, regenerate them with:
```bash
flutter pub run build_runner build --delete-conflicting-outputs
```

### 5. Run the app
```bash
flutter run
```
Pick a connected device/emulator when prompted, or run on a specific platform:
```bash
flutter run -d chrome     # web
flutter run -d macos      # macOS desktop
flutter run -d android    # Android device/emulator
```

### 6. Build a release
```bash
flutter build apk          # Android
flutter build ios          # iOS (requires Xcode + a Mac)
```

---

## Customization Notes

- **Currency**: change the `symbol` constant in `lib/utils/currency_formatter.dart`
  (defaults to `$`).
- **Categories**: add/edit categories in `lib/utils/categories.dart` — update
  `expenseCategories`/`incomeCategories` lists and the `iconFor`/`colorFor` switch
  statements together.
- **Color theme**: all colors live in `lib/utils/app_theme.dart` under `AppColors`.
- **PIN lock**: this is a **local convenience lock only** (stored in Hive, the local
  on-device database) — there is no encryption, password recovery, or server-side
  account system. It's meant to stop a sibling or roommate from casually opening the
  app, not to protect against a sophisticated attacker.

---

## Why Hive instead of SQLite?

Hive was chosen because it's a pure-Dart, NoSQL key-value store that's fast, has zero
native dependencies, and pairs naturally with Dart model classes via generated
adapters — ideal for a personal finance tracker's relatively simple data shape Hive
also works on all platforms Flutter supports (including web) without extra plugins.

If you'd prefer relational queries (e.g. complex multi-table joins for very large
datasets), swapping in `sqflite` would mean rewriting `hive_service.dart` only — the
rest of the app (providers, screens, widgets) talks to that service layer, not to
Hive directly, so the migration surface is contained.
