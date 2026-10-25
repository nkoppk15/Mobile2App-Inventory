# Mobile2App Inventory

An Android inventory manager built in Java with Room, covering a full development cycle. Built for CS-360 as a complete mobile app: user accounts, a grid of inventory items, full CRUD, and SMS alerts when stock runs out.

## What it solves

The target user is anyone tracking stock in a spreadsheet who wants a more convenient option, such as a small business owner or a collector. The core needs prioritized simplicity. 
- See everything you have on one screen
- Update counts fast
- Get notified the moment an item's stock is depleted
- Maintain privacy, since inventory and account data never leave the device.

## The UI

Five screens cover the flows:
- Login screen that handles existing accounts and new-user creation
- A scrollable three-column grid of the full inventory
- A details screen for editing/removing an item
- An add-item form
- An SMS opt-in screen.

Decisions that made this user-centered rather than just functional: 
Quantity lives in both plus/minus buttons and a direct text field, so a quick correction and a big shipment count are both one step. Edits are held in a working copy until Confirm, so Cancel is free and nothing is written by accident. The grid persists as the home view so nothing is buried. The SMS feature is not mandatory. The user can deny the permission and everything else still works.

The designs worked because each screen answers one question and getting anywhere is one tap away.

## How it was coded

The app leans on a strict separation of concerns. 
- Activities only handle input and navigation
- A repository class owns every database operation and runs each one on a background thread pool
- Room DAOs and entities stay dumb and reusable.

That structure is why the delete feature and the SMS flow could be added late without touching login or grid code. This is the pattern I'd reach for first on any future app.

## Testing

Every feature was built against the emulator in small increments. A few interesting bugs were caught early due to this frequent testing:

- A first-run race where the login query could beat the seeded test user into the database.
- An SMS alert that failed because an intent extra was written as a long where a string was expected.

This matters because a crash in the field is a bad review you didn't get a chance to prevent.

## Where I Innovated

The threading model ended up being the most interesting part. Since every database call runs through one shared executor, a pattern emerged that I learned from researching best practices. Instead of each screen managing its own refresh, the grid re-queries the database in `onResume`, so returning from any other screen is itself the refresh. Add an item or edit a quantity and the grid is correct the moment you back out, with zero added refresh code in those screens.

The first-login slowness on a cold emulator also got an interesting fix. I called the database early so it opens in the background while the login screen sits idle. This ensured the first login tap is fast and snappy, which users will expect.


## Running the app

1. Open the project in Android Studio 
2. Let Gradle sync
3. Run on any emulator or device with API 24 or newer.
4. Log in with `admin` / `password123` (seeded on first run) or create your own account.

Requires no API keys and no backend. Everything runs on-device.
