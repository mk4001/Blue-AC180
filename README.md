# Blue-AC180

Blue-AC180 is an iPhone and Apple CarPlay app for monitoring a Bluetti AC180 portable power station through Bluetooth Low Energy.

The app connects locally to the selected AC180, reads live telemetry registers, and presents the most relevant electrical values on the iPhone. On Apple CarPlay, Blue-AC180 provides a low-distraction dashboard with three large sections: Overview, Power and Battery.

> This project is independent and is not affiliated with, endorsed by, or sponsored by Bluetti.

## App Store

App Store link coming soon.

## Features

- Local Bluetooth Low Energy connection to Bluetti AC180 units.
- Dedicated device selection screen filtered to devices whose name starts with `AC180`.
- Stored device binding using the iOS CoreBluetooth peripheral identifier.
- Battery state of charge.
- Remaining runtime shown as decimal hours.
- Battery voltage and current estimate.
- AC input power.
- AC output power.
- DC output power.
- Grid connection status.
- Charging status.
- Charging mode: Standard, Silent or Turbo.
- AC output enabled state.
- DC output enabled state.
- Internal temperature.
- Apple CarPlay interface with large, simple sections.
- No cloud service, no account and no external telemetry server required.
- About screen and App Store in-app purchase support.

## Supported Devices

The current implementation is built and tested for the Bluetti AC180 family.

Device family | Exact device verified so far | Decoded values
--- | --- | ---
Bluetti AC180 | AC180 with BLE advertised name beginning with `AC180` | SOC, runtime, battery voltage, current estimate, AC input, AC output, DC output, grid state, charging state, charging mode, AC/DC output state, internal temperature

Other Bluetti models may expose similar data, but Blue-AC180 intentionally filters discovery to AC180 devices and does not claim support for other models.

## Confirmed AC180 Values

Blue-AC180 reads confirmed AC180 BLE telemetry registers and converts them into display values:

Field | Meaning
--- | ---
SOC | Battery state of charge in percent
Runtime | Remaining runtime converted to decimal hours
Battery voltage | Pack voltage
Current estimate | Inverter battery current estimate
AC input | Power entering from the grid or AC source
AC output | AC load power
DC output | DC load power
Grid connected | Whether AC input is connected
Charging | Whether the unit is charging from AC input
Charging mode | Standard, Silent or Turbo
AC enabled | AC output/inverter enabled state
DC enabled | DC output enabled state
Temperature | Internal machine temperature

## Important Notes About Values

- Runtime is shown as decimal hours, matching the AC180 value after conversion from minutes.
- AC and DC power values are read from the AC180 telemetry registers and may update at a different cadence from the physical display.
- iOS does not expose the real Bluetooth MAC address. Blue-AC180 stores the selected CoreBluetooth peripheral UUID and the advertised AC180 name as the device binding.
- BLE communication can be affected by distance, interference, iOS Bluetooth state and other apps already connected to the unit.

## How It Works

Blue-AC180 scans nearby Bluetooth Low Energy peripherals and shows only devices whose advertised name starts with `AC180`.

After you select your power station, the app polls the AC180 over BLE using the known AC180 telemetry register ranges. The parser maps confirmed registers into readable values and updates the iPhone and CarPlay dashboards.

See `AC180_REGISTER_MAP.md` for the current register map used by the app.

## Requirements

- iPhone with Bluetooth enabled.
- Compatible Bluetti AC180 with Bluetooth enabled.
- iOS version supported by the current App Store build.
- Apple CarPlay compatible vehicle, receiver or simulator for the CarPlay interface.
- Bluetooth permission enabled for Blue-AC180.

## Using the iPhone App

1. Launch Blue-AC180 on the iPhone.
2. Open the device menu.
3. Select your AC180 from the filtered Bluetooth device list.
4. Return to the dashboard.
5. Keep the AC180 nearby while the app reads live values.
6. Use the About screen for app information, purchase status and restore actions.

If you change power station, use the device menu to forget the current binding and select another AC180.

## Using CarPlay

1. Install and run Blue-AC180 on the iPhone.
2. Select the AC180 from the iPhone app at least once.
3. Connect the iPhone to Apple CarPlay.
4. Open Blue-AC180 from the CarPlay launcher.
5. Choose one of the large dashboard sections:
   - Overview
   - Power
   - Battery
6. Open a section to view the related live values.

The CarPlay UI is intentionally compact and low-distraction: large colored buttons, mnemonic icons and only the most relevant values.

## Blue-AC180 Pro

Blue-AC180 includes an initial CarPlay preview. Continued CarPlay use requires the Blue-AC180 Pro in-app purchase.

Purchase and restore are available only in the iPhone app. Purchases are handled by Apple through the App Store.

Current StoreKit product identifier:

`com.mflab.blueac180.pro`

## Privacy

Blue-AC180 reads AC180 data locally over Bluetooth on the iPhone.

- No personal data collection by MF-Lab servers.
- No cloud synchronization.
- No account required.
- No external telemetry server.
- Selected AC180 identifier and name are stored locally on the iPhone.
- Purchase status is handled through Apple StoreKit.

For the full policy, see the privacy page included with the app support site.

## Known Limitations

- The app is designed for Bluetti AC180 units and filters the device list to advertised names beginning with `AC180`.
- iOS may limit Bluetooth activity depending on system state, permissions and nearby radio conditions.
- The app monitors telemetry only. It does not send control commands to enable, disable or configure AC180 outputs.
- CarPlay availability depends on Apple CarPlay support, vehicle compatibility, region and App Store entitlement approval.
- Additional Bluetti models should not be considered supported unless validated with real BLE logs and parser updates.

## Support

For support, feedback or privacy questions, contact MF-Lab by email:

support@mf-lab.com

When requesting support, include:

- iPhone model.
- iOS version.
- Blue-AC180 app version.
- AC180 advertised name, if visible.
- Whether the issue happens on iPhone, CarPlay or both.

## License

All rights reserved unless a separate license file is provided in this repository.
