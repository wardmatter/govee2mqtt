# Govee to MQTT bridge for Home Assistant

This repo provides a `govee` executable whose primary purpose is to act
as a bridge between [Govee](https://govee.com) devices and Home Assistant,
via the [Home Assistant MQTT Integration](https://www.home-assistant.io/integrations/mqtt/).

## Tap-to-Run support in this fork

This branch combines two upstream Govee2MQTT patches for running scenes saved
under Tap-to-Run in Govee Home.

- [PR #652](https://github.com/wez/govee2mqtt/pull/652) updates account login and
  requests an email verification code when Govee returns status `454`.
- [PR #739](https://github.com/wez/govee2mqtt/pull/739) uses the account token to
  load Tap-to-Run scenes after Govee stopped accepting community tokens for
  scene discovery.

### Install the Home Assistant test app

1. Start Mosquitto and confirm that the Home Assistant MQTT integration is connected.
2. In **Settings → Apps → App store → Repositories**, add
   `https://github.com/wardmatter/govee2mqtt#codex/govee-tap-to-run`.
3. Install **Govee to MQTT Bridge (Tap-to-Run test)**. Supervisor builds the app
   from the pinned patched source. The first build can take several minutes.
4. Enter your Govee Home email and password in the app configuration and start
   it. If the log says a verification code was requested, enter the emailed
   code in `govee_2fa_code` and start the app again.
5. Open **Settings → Devices & services → MQTT** and find the scenes under
   **Govee to MQTT**. Activate one with `scene.turn_on` before assigning it to
   a button or automation.

The test app uses an isolated container network so its UDP 4002 listener can
coexist with a Govee integration already using that port on the host. This
setup targets cloud-based Tap-to-Run. LAN discovery across the container
boundary may be limited. Tap-to-Run discovery was verified without a Govee
developer API key. Per-device API features may still require one.

On the Home Assistant test installation, the app discovered **Cinema Scene**
and **Luminance Scene**, and the bridge logged a `scene.turn_on` request for
each. Both scenes returned after clearing the one-time code and restarting the
bridge. The test verified command delivery to the bridge. Physical button
presses and the lights' response still need checking in the room.

## Features

* Robust LAN-first design. Not all of Govee's devices support LAN control,
  but for those that do, you'll have the lowest latency and ability to
  control them even when your primary internet connection is offline.
* Support for per-device modes and scenes.
* Support for the undocumented AWS IoT interface to your devices, provides
  low latency status updates.
* Support for the new [Platform
  API](https://developer.govee.com/reference/get-you-devices) in case the AWS
  IoT or LAN control is unavailable.

|Feature|Requires|Notes|
|-------|--------|-------------|
|DIY Scenes|API Key|Find in the list of Effects for the light in Home Assistant|
|Music Modes|API Key|Find in the list of Effects for the light in Home Assistant|
|Tap-to-Run / One Click Scene|IoT|Find in the overall list of Scenes in Home Assistant, as well as under the `Govee to MQTT` device|
|Live Device Status Updates|LAN and/or IoT|Devices typically report most changes within a couple of seconds.|
|Segment Color|API Key|Find the `Segment 00X` light entities associated with your main light device in Home Assistant|

* `API Key` means that you have [applied for a key from Govee](https://developer.govee.com/reference/apply-you-govee-api-key)
  and have configured it for use in govee2mqtt
* `IoT` means that you have configured your Govee account email and password for
  use in govee2mqtt, which will then attempt to use the
  *undocumented and likely unsupported* AWS MQTT-based IoT service
* `LAN` means that you have enabled the [Govee LAN API](https://app-h5.govee.com/user-manual/wlan-guide)
  on supported devices and that the LAN API protocol is functional on your network

## Usage

* [Installing the HASS Add-On](docs/ADDON.md) - for HAOS and Supervised HASS users
* [Running it in Docker](docs/DOCKER.md)
* [Configuration](docs/CONFIG.md)

## Have a question?

* [Is my device supported?](docs/SKUS.md)
* [Check out the FAQ](docs/FAQ.md)

## Want to show your support or gratitude?

It takes significant effort to build, maintain and support users of software
like this. If you can spare something to say thanks, it is appreciated!

* [Sponsor me on Github](https://github.com/sponsors/wez)
* [Sponsor me on Patreon](https://patreon.com/WezFurlong)
* [Sponsor me on Ko-Fi](https://ko-fi.com/wezfurlong)
* [Sponsor me via liberapay](https://liberapay.com/wez)

## Credits

This work is based on my earlier work with [Govee LAN
Control](https://github.com/wez/govee-lan-hass/).

The AWS IoT support was made possible by the work of @bwp91 in
[homebridge-govee](https://github.com/bwp91/homebridge-govee/).

