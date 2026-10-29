# Blue-AC Console

Blue-AC Console is an iPhone and iPad app for monitoring a Bluetti AC180 or AC70 portable power station through Bluetooth Low Energy. It also runs in Apple CarPlay, on the Lock Screen and Dynamic Island, on the Home Screen and on Apple Watch.

The app connects locally to the selected unit, reads live telemetry registers and presents the most relevant electrical values. It only reads: it never sends commands to switch outputs or change settings.

> This project is independent and is not affiliated with, endorsed by, or sponsored by Bluetti.

The app is called **Blue-AC Console** in the App Store and in every screen. The Xcode project, targets, bundle identifiers and the repository keep the earlier name **Blue-AC180**.

## App Store

App Store link coming soon. Release texts (promotional text, what's new, description, keywords) are kept in [`AppStore.md`](AppStore.md).

## Features

### iPhone

- Local Bluetooth Low Energy connection to Bluetti AC180 and AC70 units, with automatic reconnection.
- Dark navy and gold dashboard: state of charge ring, net power, voltage, runtime, connection status and signal strength.
- Power tiles for AC In, DC In, AC Out, DC Out and Temperature, each with a mini chart of the last hour.
- History charts for the same five values over 1 hour, 6 hours, 24 hours or 7 days, with minimum, average, maximum and the energy in Wh or kWh. One averaged point is saved per minute while connected, also in the background, and the last 30 days are kept on the iPhone only.
- Alarms: a notification when the grid (AC input) is lost and when it is restored, and when the charge drops below a threshold you choose (5 % to 90 %).
- Details: grid, charging mode, AC and DC output state.
- Demo Mode: the whole app with simulated data, including a week of history, no power station required.
- Runs on iPad as a compact floating panel, as in Tru-Control: a phone-sized, rounded panel in the middle of a full-screen window, and a phone-sized window (420 × 640 up to 680 × 1100) when the app is windowed.
- Built-in Help and About screens, and a What's New screen shown once after an update (and reopenable from the menu).
- Device screen filtered to advertised names beginning with `AC180` or `AC70`, with a stored binding to the selected unit.
- Diagnostics screen and a persistent protocol log that can be sent to support.

### CarPlay

- A tabbed Driving Task interface: Dashboard (large cards for Battery, Input, Output, Power and Status), Details (one row per reading with an icon) and Connection.
- Voice readout when a card is opened, in English, Italiano, Français, Deutsch or Español, or turned off, chosen from the iPhone menu under CarPlay voice. Other audio is lowered while it speaks.

### Apple Watch

- A Watch app with three pages: Battery, Power and Details, and a label that says how old the reading is.
- A complication for the watch face in circular, rectangular and inline sizes.
- The Watch does not connect to the power station. The iPhone relays the readings through WatchConnectivity.

### Lock Screen, Dynamic Island and Home Screen

- A Live Activity in the look of the medium widget: the charge ring with the runtime, net power, source and the four AC/DC powers, on the Lock Screen and the CarPlay Widgets page, and as a ring in the Dynamic Island.
- A Home Screen widget in small, medium and large sizes: charge ring, runtime, AC and DC power in and out, and in the large one voltage, temperature, outputs and a chart of the last hour.

## Supported Devices

Device | Notes
--- | ---
Bluetti AC180 | BLE advertised name beginning with `AC180`
Bluetti AC70 | BLE advertised name beginning with `AC70`. Same register map as the AC180, except that the remaining runtime (register `104`) uses a different scale, which the app handles

Other Bluetti models may expose similar data, but they should not be considered supported unless validated with real BLE logs and parser updates.

## Decoded Values

Field | Meaning
--- | ---
State of charge | Battery percentage
Runtime | Remaining runtime, shown as decimal hours
Battery voltage | Pack voltage
Battery current | Inverter battery current estimate
AC input / DC input | Power entering from the grid, or from solar and car charging
AC output / DC output | Load power
Grid connected | Whether the AC input is present (status flag or measured power)
Charging source | AC, Solar or none
Charging mode | Standard, Silent or Turbo
AC / DC output enabled | State of the two outputs
Temperature | Internal temperature

The register map used by the app is in [`AC180_REGISTER_MAP.md`](AC180_REGISTER_MAP.md).

## Important Notes About Values

- When the unit is not connected every reading is reset to zero, so old values are never shown as live.
- AC and DC power values may update at a different cadence from the physical display of the unit.
- iOS does not expose the real Bluetooth MAC address. The app stores the CoreBluetooth peripheral identifier and the advertised name as the device binding.
- Bluetooth can be affected by distance, interference, the iOS Bluetooth state and other apps that are already connected to the unit.

## How It Works

The app scans nearby Bluetooth Low Energy peripherals and lists only devices whose advertised name starts with `AC180` or `AC70`. After you select your unit, it opens the encrypted session for the units that require one, polls the known telemetry register ranges every few seconds and decodes them into readable values.

The same readings feed every surface:

- the iPhone dashboard and the history store (SwiftData);
- the alarm engine, which raises local notifications;
- CarPlay, the widget and the Live Activity, through a shared snapshot in an App Group;
- the Watch app and its complication, through WatchConnectivity.

Because the app declares the `bluetooth-central` background mode, it keeps receiving readings while the Bluetooth connection is alive, so history and alarms keep working in the background. They stop if you force-quit the app or the unit goes out of range.

## Project Layout

Target | Purpose
--- | ---
`Blue-AC180` | The iPhone app, including the CarPlay scene
`BlueAC180Widget` | Home Screen widget and Live Activity
`Blue-AC Console Watch App` | The Apple Watch app
`BlueAC180WatchComplication` | The watch face complication

Main files in `Blue-AC180/`:

- `BluettiAC180BLEClient.swift`, `BluettiEncryption.swift`, `ModbusFrame.swift`, `AC180Parser.swift`: Bluetooth session and decoding.
- `TelemetryStore.swift`, `Telemetry.swift`, `ConnectionStatus.swift`: live state shared by the views.
- `DashboardView.swift`, `HistoryViews.swift`, `AlarmsView.swift`, `HelpView.swift`, `AboutView.swift`, `PurchaseView.swift`, `AppTheme.swift`: the iPhone interface.
- `HistoryStore.swift`, `HistoryMath.swift`: persistent history and its calculations.
- `AlarmEngine.swift`, `AlarmManager.swift`: when an alarm fires, and how it is delivered.
- `CarPlaySceneDelegate.swift`, `CarPlayTiles.swift`, `CarPlaySpeech.swift`: CarPlay interface, rendered cards and the voice readout.
- `WatchConnectivityManager.swift`, `LiveActivityManager.swift`: the relays to Watch and Live Activity.
- `PurchaseManager.swift`: StoreKit purchase, free sessions and entitlement.
- `DemoDataGenerator.swift`: simulated data for Demo Mode.

## Requirements

- iPhone or iPad with Bluetooth, iOS 18.0 or later.
- A Bluetti AC180 or AC70 with Bluetooth enabled.
- Apple CarPlay compatible vehicle, receiver or simulator for the CarPlay interface.
- Apple Watch for the Watch app and complication (the Watch targets are built for watchOS 26.5).
- Xcode with the iOS 18 and watchOS 26.5 SDKs to build from source.

## Using the iPhone App

1. Launch Blue-AC Console on the iPhone and allow Bluetooth.
2. Open the menu (top right) and choose Device settings, then Scan.
3. Select your unit from the filtered list.
4. Return to the dashboard and keep the unit nearby while the app reads live values.
5. Choose Help in the menu for a short guide to every screen.

To change power station, forget the current one in Device settings and select another. To try the app without a unit, choose Start Demo Mode in the menu.

## Using CarPlay

1. Install and run Blue-AC Console on the iPhone and select your unit at least once.
2. Connect the iPhone to CarPlay and open Blue-AC Console from the launcher.
3. Use the Dashboard, Details and Connection tabs. Opening a Dashboard card shows its values and reads them aloud, unless the voice is off.

The interface is intentionally compact and low-distraction: large colored cards, mnemonic icons and only the most relevant values. CarPlay availability depends on vehicle compatibility, region and the App Store entitlement approval.

## Blue-AC Console Pro

The app is free to try. One CarPlay session, one Live Activity session, one voice readout and one Apple Watch session are included. Blue-AC Console Pro, a one-time purchase with no subscription, unlocks unlimited use of all four, plus the history charts, the alarms and the Home Screen widget. Everything is available in Demo Mode.

Purchase and restore are available only in the iPhone app, and are handled by Apple through the App Store.

StoreKit product identifier: `com.mflab.blueac180.pro`

## Privacy

Blue-AC Console reads the unit's data locally over Bluetooth on the iPhone.

- No personal data collection by MF-Lab servers.
- No cloud synchronization, no account and no analytics.
- No external telemetry server.
- The selected unit's identifier and name, the settings and the chart history are stored locally on the iPhone and never leave it.
- Purchase status is handled through Apple StoreKit.

For the full policy, see the Privacy Policy linked in the About screen.

## Known Limitations

- Only Bluetti AC180 and AC70 units are supported.
- iOS may limit Bluetooth activity depending on system state, permissions and nearby radio conditions.
- The app monitors telemetry only. It does not send control commands to enable, disable or configure outputs.
- Alarms and history recording in the background depend on the Bluetooth connection staying alive. They cannot work after the app is force-quit or while the unit is out of range, and a Focus mode can silence notifications.
- The Watch and the complication show the last reading relayed by the iPhone, so they need the iPhone nearby.
- The Home Screen widget refreshes when iOS decides to.

## Support

For support, feedback or privacy questions, contact MF-Lab by email:

support@mf-lab.com

When requesting support, include:

- iPhone model and iOS version.
- Blue-AC Console version and build.
- The advertised name of the unit, if visible.
- Whether the issue happens on iPhone, CarPlay, Apple Watch or several.
- The diagnostics log: menu, Diagnostics, Send Log.

## License

All rights reserved unless a separate license file is provided in this repository.
