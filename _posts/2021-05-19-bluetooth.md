---
layout: post
title: Bluetooth
categories:
    - Android
description: A comprehensive guide to Bluetooth LE and Classic on Android.
---

Developing Bluetooth apps can be tricky, to put it mildly. This guide is based on my experience working on Bluetooth (LE and Classic) companion apps for devices like headsets, scooters, cars and smartwatches.

<div class="message" markdown="1">

**WARNING:** The Android Bluetooth LE API has lots of hidden traps and fragmentation issues. To save yourself some headache consider using a thoroughly tested [library](#libraries).
</div>


Links:
- [Official Documentation](https://developer.android.com/guide/topics/connectivity/bluetooth)
- [The Ultimate Guide to Android Bluetooth Low Energy](https://punchthrough.com/android-ble-guide/)
- [Bluetooth LE for modern Android Development](https://www.hellsoft.se/bluetooth-le-for-modern-android-development-part-1/)
- [Bluetooth LE Standard Services and Characteristics](https://github.com/oesmith/gatt-xml)


# Standards
There are two Bluetooth standards:
- **Bluetooth Classic**: Used for high data throughput like file and audio transfer. Uses a two-way [socket](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothSocket) for communication. Supports [standardized profiles](https://developer.android.com/guide/topics/connectivity/bluetooth/profiles), like headset (volume control), A2DP (audio transfer) or health devices (step sensors). Sometimes called BR/EDR (Bluetooth Basic Rate/Enhanced Data Rate) or RFCOMM.
- **Bluetooth Low Energy** (BLE): Used for low throughput interaction with IoT devices. Endpoints are called characteristics (e.g. "firmware revision") and are grouped into services (e.g. "device information"). There are lots of [standardized services and characteristics](https://www.bluetooth.com/specifications/specs/), like "battery service" or "device information" service. Sometimes called GATT (Generic Attribute Profile).

The Android Bluetooth API is a partial mix of both standards. E.g. Bluetooth Classic and BLE devices are both represented by the [BluetoothDevice](https://developer.android.com/reference/android/bluetooth/BluetoothDevice) class (with `DEVICE_TYPE_CLASSIC`, `DEVICE_TYPE_LE` or `DEVICE_TYPE_DUAL`). Scanning and communication however use entirely different classes and methods.


# Preconditions
The following preconditions are required for **scanning** Bluetooth Classic or BLE devices:
- Permission:
    - API < 29 → `BLUETOOTH`, `BLUETOOTH_ADMIN`, `ACCESS_COARSE_LOCATION`
    - API ≥ 29 → `BLUETOOTH`, `BLUETOOTH_ADMIN`, `ACCESS_FINE_LOCATION` (runtime permission),`ACCESS_BACKGROUND_LOCATION` (only for scanning in background service)
    - API ≥ 31 → `BLUETOOTH_SCAN` (runtime permission) + `neverForLocation` Flag
- [Bluetooth is on](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothAdapter#getstate)
- API < 31 → [location service](https://developer.android.com/reference/kotlin/android/location/LocationManager#islocationenabled) ("GPS") is on

The following preconditions are required for **connecting** to Bluetooth Classic or BLE devices:
- Permission:
    - API < 31 → `BLUETOOTH`
    - API ≥ 31 → `BLUETOOTH_CONNECT` (runtime permission)
- [Bluetooth is on](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothAdapter#getstate)
- Reference to a [BluetoothDevice](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice) instance.

These preconditions **can be lost at any time**:
- Bluetooth and location service can be turned off via quick settings even while the app is in foreground.
- Location permission can be removed in system settings or if app is running in background.
- Devices can be removed in system settings.


# Scanning
If the device is already bonded (i.e. it is in the list of paired device in system settings), it can be access via [BluetoothAdapter.getBondedDevices](https://developer.android.com/reference/android/bluetooth/BluetoothAdapter#getBondedDevices()) and **no scanning is needed**.

## Bluetooth Classic
You can scan for nearby Bluetooth Classic devices by calling [BluetoothAdapter.startDiscovery](https://developer.android.com/reference/android/bluetooth/BluetoothAdapter#startDiscovery()). The `BluetoothAdapter` is a system service, that is shared by all apps, so results are passed via broadcast receivers:
- `ACTION_DISCOVERY_STARTED`/`ACTION_DISCOVERY_FINISHED`: Discovery started / stopped 
- `ACTION_FOUND`: Device found

Discovery will stop itself after some time and needs to be restarted manually if you want continuous discovery.

## Bluetooth LE
Some libraries support scanning. If not, you can scan for nearby Bluetooth LE devices by calling [BluetoothLeScanner.startScan](https://developer.android.com/reference/kotlin/android/bluetooth/le/BluetoothLeScanner#startscan). Be prepared for undocumented internal scan limits and irrecoverable states in the bluetooth adapter, that may require a reboot on old devices.

BLE scans happen in two steps:
1. The smartphone passively listens for advertising packages that BLE devices periodically broadcast.
2. Once a device is found, the smartphone actively requests additional "scan response" data from it.

Every BLE service can have some arbitrary advertising data. This data can be accessed via [ScanRecord.getServiceData](https://developer.android.com/reference/kotlin/android/bluetooth/le/ScanRecord#getservicedata).

<div class="message" markdown="1">

Parsing of the `ScanRecord` data may be broken in some cases in Android < 8. In that case we still have access to the raw data via [ScanRecord.getBytes](https://developer.android.com/reference/kotlin/android/bluetooth/le/ScanRecord#getbytes), so we can [parse them manually](https://github.com/dariuszseweryn/RxAndroidBle/blob/master/rxandroidble/src/main/java/com/polidea/rxandroidble2/internal/util/ScanRecordParser.java).
</div>


# Connecting
Once you have a [BluetoothDevice](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice) (e.g. from [ScanResult.getDevice](https://developer.android.com/reference/kotlin/android/bluetooth/le/ScanResult#getdevice), [BluetoothAdapter.getBondedDevices](https://developer.android.com/reference/android/bluetooth/BluetoothAdapter#getBondedDevices()), etc.), you can connect to it. 

## Bluetooth Classic
Calling [BluetoothDevice.createRfcommSocketToServiceRecord](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#createRfcommSocketToServiceRecord(java.util.UUID)) will create a [BluetoothSocket](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothSocket). The service record UUID parameter depends on the device, but it is often the well known SPP-UUID (`00001101-0000-1000-8000-00805F9B34FB`). The device's supported service record UUIDs can be accessed with [BluetoothDevice.getUuids](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#getuuids).

Calling [BluetoothSocket.connect](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothSocket#connect) will block until the device is connected. This is usually just takes a few milliseconds, if the device is in range. You can then use the socket's threadsafe [input](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothSocket#getinputstream) and [output](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothSocket#getoutputstream) streams, which can be used to send and retrieve arbitrary bytes.

## Bluetooth LE
Most libraries support connecting. If not, you can connect to a Bluetooth LE device by calling [BluetoothDevice.connectGatt](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#connectgatt_3). The behavior of the `autoConnect` flag is poorly documented and depends on the smartphone manufacturer and Android version. In my experience it's easier to just not use it and build your own retry mechanism instead.


# Bonding
Pairing and bonding are actually two related concepts, however Android makes no distinction:
- Pairing: Exchange of cryptographic keys for encrypted communication, i.e. to prevent MITM attacks.
- Bonding: Long term storage of encrypted keys, instead of discarding them after disconnecting.

A bonded device will appear in the list of bluetooth devices in the system settings and it can be access via [BluetoothAdapter.getBondedDevices](https://developer.android.com/reference/android/bluetooth/BluetoothAdapter#getBondedDevices()).

Bluetooth Classic always requires pairing for communication. BLE requires pairing for communicating with **protected characteristics** (encryption required). For IoT devices without protected BLE characteristics, no pairing is required, so you can just scan for devices and read their values.

There are multiple ways to trigger bonding:
- A user can bond Bluetooth devices in system settings, though some smartphones do not show Bluetooth-LE device there, depending on Android versions or manufacturer.
- Calling [BluetoothDevice.createBond](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#createBond()), which does pretty much the same as clicking on a device in system settings.
- BLE only:
    - Reading a protected characteristic, while the device is not bonded yet, will fail with an error (`GATT_INSUFFICIENT_AUTHENTICATION`) and trigger bonding.
    - The device can trigger bonding after connecting, though this will show a pairing dialog every time, even if the device is already bonded.

Pairing takes a few seconds and sometimes randomly fails, so there has to be a retry mechanism. Results are passed via broadcast receivers:
- `ACTION_BOND_STATE_CHANGED`: Device started pairing, device was bonded, bonding failed ([BOND_NONE](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#bond_none)), or device lost bonding ("forget device" in system settings).
- `ACTION_PAIRING_REQUEST`: Dialog (see below) is shown, so user can confirm pairing, e.g. to [enter a PIN](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#setpin) or authorized access to contacts.

![Pairing request](https://i.stack.imgur.com/p9BNq.png)


# Communication
## Bluetooth Classic
Bluetooth Classic uses a simple duplex [BluetoothSocket](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothSocket) for communication. It has an input and output stream, so you can write and receive arbitrary bytes to and from the device.

This socket is not used for audio transfer ([A2DP](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothA2dp) profile), but for custom communication or control protocols.

There is no global standard for how these bytes are formatted, so a custom protocol has to be specified. This is usually a packet frame structure with header and payload.

## Bluetooth LE
<div class="message" markdown="1">

**WARNING:** This is a tricky part. Use a [library](#libraries) to make your life easier.
</div>

When connecting with [BluetoothDevice.connectGatt](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#connectgatt), you have to provide a [BluetoothGattCallback](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothGattCallback), and will get a [BluetoothGatt](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothGatt) object. 

[BluetoothGattCallback](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothGattCallback) is a "god callback", because every unrelated action (characteristic reads, writes, MTU changes, etc.) is piped through it and has to be manually associated with the triggering request.

[BluetoothGatt](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothGatt) is used for every interaction with the device (send requests, start discovery, start MTU negotiation) and also handles (re-)connection, disconnection and resource cleanup. For reconnection it is usually better to just completely `close` the `BluetoothGatt`, remove any `BluetoothGatt` references and call [BluetoothDevice.connectGatt](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#connectgatt) again after a short delay.

The Android Bluetooth LE API does not make it obvious, that you need to write your own scheduler and job queue for scheduling requests:
- For every request on the `BluetoothGatt` object, you have to wait for a response on the `BluetoothGattCallback`. Calling `BluetoothGatt.readCharacteristic` before the `BluetoothGattCallback` is called from the previous request, will lead to irrecoverable states. 
- Some comments on StackOverflow will recommend adding `Thead.sleep` to solve this problem. Do not do that. Blocking the callback thread may crash the Bluetooth stack on some smartphones, which requires a full reboot, unless you provided a `handler` to `BluetoothDevice.connectGatt` on API ≥ 26.

There will be undocumented, obscure and platform dependent error codes.


# Architecture
There is no best solution for an Android app's Bluetooth architecture, as it often depends on your use case, but it's usually a good idea to split it into separate business logic components:
- `PreconditionResolver`: A component for solving preconditions and providing the preconditions state.
- `Scanner`: A component for scanning for nearby devices.
- `BondingService`: A component for bonding to a device and providing the list of bonded devices.
- `DeviceProvider`: A component that provides a device for connecting. It does this by remembering a unique identifier (e.g. [MAC-Address](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#getaddress)) of the last selected device. E.g. if the user can own multiple similar devices, that are all bonded with the smartphone, then the app reconnects to last selected device instead of a random one.
- `AutoConnector`: A component for automatically (re-)connecting to a device.
    - This component likely has a `start` and `stop` method, which control its lifecycle. Lifecycle can be controlled by the application layer, e.g. "try to connect as long as the dashboard is visible".
    - This component usually contains a state machine, that is run every time preconditions change, and aggressively tries to establish and maintain a connection (lots of retry logic) once `start` is called. E.g. once `start` is called, it should automatically connect to a paired device, the moment all preconditions are resolved.
    - Once connected, it provides an abstract `Connection`, that can be used to communicate with a device. It should not contain domain specific logic like specific characteristics or command, just a way to communicate with the device.
- Multiple domain specific components for communication, that use the `AutoConnector`'s `Connection` to send messages to the device. E.g. an extension function on the `Connection` for sending a read request to the "battery level" characteristic.

The view layer should be [separated](https://en.wikipedia.org/wiki/Fundamental_theorem_of_software_engineering) as far as possible from the Bluetooth stack. Bluetooth implementation details should not leak through the architecture stack. E.g. a `ViewModel` should not even know, that it is sending a command via Bluetooth LE or how requests are encoded on the byte level.


# Other Topics
## Bluetooth LE MTU
The Maximum Transmission Unit (MTU) is the size of a Bluetooth LE packet. The minimum size is 23, which leaves us with **20 bytes of usable payload**. Trying to send or receive larger packets than the MTU will fail.

However smartphones and Bluetooth 4.2 devices can negotiate a larger MTU (up to 512), which allows larger packets and [slightly](https://punchthrough.com/maximizing-ble-throughput-part-2-use-larger-att-mtu-2/) higher data throughput. The maximum supported MTU depends on the smartphone's and Bluetooth device's chipset, though some device chipsets may not support larger MTUs at all.

Some libraries support changing MTU. Otherwise you can request a larger MTU size with [BluetoothGatt.requestMtu](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothGatt#requestMtu(kotlin.Int)) and the result will be passed to [BluetoothGattCallback.onMtuChanged](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothGattCallback#onMtuChanged(android.bluetooth.BluetoothGatt,%20kotlin.Int,%20kotlin.Int)).

Using a larger MTU may lead to disconnects on some Android devices (e.g. [Android 10 Samsung](https://forum.developer.samsung.com/t/samsung-android-10-ble-connectivity-regression/509)), so only use this if necessary.

## Bluetooth LE PHY
On API ≥ 26 the PHY (physical layer) [Bluetooth 5.0 extensions](https://www.bluetooth.com/blog/what-bluetooth-developers-should-know-about-android-o/) can be used to control the connection's [error correction redundancy and phase modulations](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy#2M_PHY) which affects bandwidth and range:
- [PHY_LE_1M](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#phy_le_1m): 1 Mb/s and 100% range (default)
- [PHY_LE_2M](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#phy_le_2m): 2 Mb/s and ~80% range
- [PHY_LE_CODED](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#phy_le_coded):
    - [PHY_OPTION_S2](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#phy_option_s2): 500 kb/s, 200% range
    - [PHY_OPTION_S8](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#phy_option_s8): 125 kb/s, 400% range

Setting the PHY ([BluetoothGatt.setPreferredPhy](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothGatt#setPreferredPhy(kotlin.Int,%20kotlin.Int,%20kotlin.Int))) does not guarantee that it will be used as not all smartphones or Bluetooth devices support all PHY modes.

## Bluetooth LE Subscriptions
Instead of repeatedly reading a characteristic to get the current value (polling) you can use Bluetooth LE subscriptions to automatically be informed once the characteristics's value changes (pushing). There are two types of subscription, which characteristics can support: 
- Indications: Device requires acknowledgement from smartphone for pushed values (like TCP).
- Notifications: Device does not require acknowledgement (like UDP).

The maximum number of open subscriptions [depend on the Android version](https://stackoverflow.com/questions/42771904/android-bluetooth-low-energy-characteristic-notification-count-limit-does-this) (more subscriptions are silently ignored):
- API ≥ 18 → 3
- API ≥ 19 → 7
- API ≥ 21 → 15

## Google Fast Pair
Some Bluetooth LE devices support [Google Fast Pair](https://developers.google.com/nearby/fast-pair/help), which is a [special BLE service](https://developers.google.com/nearby/fast-pair/specifications/introduction#gatt_service). When a BLE device enters pairing mode, smartphones in the vicinity will automatically show a notification:

![Fast Pair Notification](https://developers.google.com/nearby/fast-pair/images/initial-pairing.png)

After pairing, Fast Pair may suggest installing the companion app, if the app id is correctly configured in the device's [Google Nearby](https://developers.google.com/nearby) console.

<div class="message" markdown="1">

I've met an issue in the past, where it was not possible to use protected BLE characteristics, when the [BluetoothDevice](https://developer.android.com/reference/android/bluetooth/BluetoothDevice) (provided by [BluetoothAdapter.getBondedDevices](https://developer.android.com/reference/android/bluetooth/BluetoothAdapter#getBondedDevices())) was bonded via Fast Pair. This was probably just an issue with the device's firmware, but still worth to check when dealing with Fast Pair.
</div>

## Dual Mode devices
Some devices support both BLE and Bluetooth Classic and appear as two devices (with different names) in the list of bonded Bluetooth devices (two separate [BluetoothDevice](https://developer.android.com/reference/android/bluetooth/BluetoothDevice), not `DEVICE_TYPE_DUAL`). For example headsets, which use Bluetooth Classic for audio transfer and BLE for control (noice cancellation, equalizer, firmware update, etc.). When bonding one type, the other may be bonded automatically as well.

## Companion device pairing
[Companion device pairing](https://developer.android.com/guide/topics/connectivity/companion-device-pairing) is an alternative for scanning, that provides a system dialog for selecting a device. It does not require any location permissions and works with location service disabled, however bluetooth still needs to be enabled. It can be configured with a scan filter. Bonding has to be done manually.

However it is only available on API ≥ 26 and is very limited, as its [use case](https://www.youtube.com/watch?v=F1mvm_bfNgU) seems to be very specific to smart watches and background connectivity. The dialog is only shown as soon as at least one matching device is found, so if no device is in range, no dialog is shown. It does not support continuous scanning, which makes it unusable for onboarding flows where a device is not always discoverable, i.e. if the user has to put in pairing mode first. To retry a scan, the dialog has to be closed and opened again by the user.

## Unstable service record UUIDs
A Bluetooth Classic device's supported service record UUIDs can be accessed with [BluetoothDevice.getUuids](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#getuuids). This is a cache which is created during pairing.

In very rare cases some devices have unstable service record UUIDs (e.g. UUID depends on firmware version). In that case, the cache can can be refreshed by calling [BluetoothDevice.fetchUuidsWithSdp](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#fetchUuidsWithSdp()) and waiting for the result in a `ACTION_UUID` broadcast receiver. Fetching UUIDs takes up to 5 seconds, so don't do this for every connection attempt, just as a fallback for devices with unstable service records.

## Randomized MAC addresses
Most Bluetooth 4.2 devices use a periodically changing randomized MAC address during scanning. This is a [privacy feature](https://www.bluetooth.com/blog/bluetooth-technology-protecting-your-privacy/) to prevent tracking. So a MAC address ([BluetoothDevice.getAddress](https://developer.android.com/reference/kotlin/android/bluetooth/BluetoothDevice#getaddress)) from a scanned device should not be used to identify a device.

However once paired, the MAC address is stable and can be persisted, e.g. to remember the last connected device if an app allows connecting to multiple devices of the same kind.

## Bluetooth on iOS
The iOS Bluetooth API is functionally very different to Android, which leads to different UX in onboarding and connection flows:
- Both have different preconditions and permission models for scanning and connecting.
- iOS can't access the list of bonded devices or know if a device is not paired anymore.
- iOS can't pair manually. Pairing is always done by reading from a protected characteristic.
- Bluetooth Classic requires a special [MFI](https://mfi.apple.com/) chip on the Bluetooth device and MFI certification of both app and device.


# Libraries
- [RxAndroidBle](https://github.com/dariuszseweryn/RxAndroidBle): Very stable BLE Library with support for permission handling, scanning, communication, long writes, setting MTU, etc. Uses reactive programming with [RxJava](https://github.com/ReactiveX/RxJava). **Recommendation**: Combine this with [kotlinx-coroutines-rx2](https://github.com/Kotlin/kotlinx.coroutines/tree/master/reactive/kotlinx-coroutines-rx2) adapters, so you can use coroutines and flows.
- [Kable](https://github.com/JuulLabs/kable): Kotlin-Multi-Platform BLE library (Android, iOS, Web). Supports scanning and communication. Uses Kotlin coroutines and flows.
- [RxBluetooth](https://github.com/IvBaranov/RxBluetooth): Bluetooth Classic library with some nice abstractions. Uses reactive programming with [RxJava](https://github.com/ReactiveX/RxJava).
- [Android-BLE-library](https://github.com/NordicSemiconductor/Android-BLE-Library): I have not used this yet, but it is made by Nordic Semiconductor who brought us the [nRF Connect](https://play.google.com/store/apps/details?id=no.nordicsemi.android.mcp&hl=de&gl=US) app, which is really great for debugging.