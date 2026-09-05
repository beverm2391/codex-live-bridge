# Installation

The current source includes unreleased security and runtime fixes beyond the
latest release, 3.1.0. No newer release package is available. Build from the
current source or update an existing device to use these fixes.

The device package needs the Max for Live device and its two JavaScript
runtime files:

```text
LiveUdpBridge.amxd
live_udp_bridge.js
osc_loopback_receiver.js
```

The repository also includes `bridge/m4l/LiveUdpBridge.maxpat`, the editable
source for the device. Follow [Package From Source](#package-from-source) if
you need to build your own copy.

## Load the Current Device

1. Follow [Package From Source](#package-from-source) for a first installation,
   or [Update an Installed Device](#update-an-installed-device) for an existing
   installation.
2. Keep `LiveUdpBridge.amxd`, `live_udp_bridge.js`, and
   `osc_loopback_receiver.js` next to each other.
3. Open Ableton Live and drag `LiveUdpBridge.amxd` onto a MIDI track.
4. Verify the read-only connection from the repository root:

```bash
python3 bridge/ableton_udp_bridge.py --ack --status --no-tempo --no-signature
```

The bridge is ready for local reads and writes when the device responds.

## Package From Source

Ableton Live's bundled Max editor does not enable standalone export from an
opened `.maxpat` file. To create a loadable device from the tracked source:

1. Open Ableton Live and drag the blank **Max MIDI Effect** device onto a MIDI
   track.
2. Use the device's **Edit in Max** action to open its Live-hosted patch.
3. Update that patch from the reviewed
   `bridge/m4l/LiveUdpBridge.maxpat` source.
4. Use **File > Save As...** in the Live-hosted Max editor and save the device
   as `LiveUdpBridge.amxd`.
5. Place the packaged `LiveUdpBridge.amxd` next to
   `bridge/m4l/live_udp_bridge.js` and
   `bridge/m4l/osc_loopback_receiver.js`.
6. Reload `LiveUdpBridge.amxd` on a MIDI track in Ableton Live.
7. Run the read-only status command shown above.

## Update an Installed Device

After the bridge has been installed once, stage an updated copy without
changing the installation:

```bash
node scripts/ableton-device.js
```

On macOS, the default baseline is the existing device at:

```text
~/Music/Ableton/User Library/Presets/MIDI Effects/Max MIDI Effect/LiveUdpBridge.amxd
```

The command rebuilds the device from the tracked Max patch in a private
temporary directory. It preserves the installed device metadata. An existing baseline does not need to be opened in Max
or rediscovered through the Ableton interface.

To replace the installed files, keep Ableton Live open with the bridge loaded
and run:

```bash
node scripts/ableton-device.js --install --verify-live
```

No installed file changes without `--install`. The `--verify-live` option
requires `--install`. Before installation, the command saves the existing
device and JavaScript files under:

```text
~/Library/Application Support/codex-live-bridge/backups
```

It then checks the running bridge with a localhost status request.
If verification fails, all three installed files are restored from the backup.
Override paths with `--device PATH`, `--output-dir DIR`, `--backup-dir DIR`,
or `--python PATH`. Output and backup directories inside this repository are
rejected. Staging, backup, and installed-device locations must not contain
one another.

The JSON result reports `liveStatusVerified` for this status exchange and
`runtimeIdentityVerified: false`. The deprecated `verifiedLive` field is an
alias for `liveStatusVerified`. An older loaded device can answer the status
request, so this check does not establish which code is running. Reload the
device and verify the changed commands separately in a disposable set.

Keep staged devices and backups private. Do not commit or upload them.

## Local Control Boundary

The bridge has no application-level authentication. Its command receiver binds
only to `127.0.0.1`, and the Python client refuses non-loopback targets. Any
local process running as a user on this Mac can send reads or mutations while
the device is loaded. Use a dedicated bridge track, inspect before mutations,
make narrow changes, and verify state afterward.

## Source Editing

After editing `bridge/m4l/LiveUdpBridge.maxpat` or
either JavaScript runtime file, update an existing installation with:

```bash
node scripts/ableton-device.js --install --verify-live
```

Run the static checks from `README.md`. Reload the device when the current Live
set needs to pick up a changed Max patch.

For a first installation, follow [Package From Source](#package-from-source)
to create the initial device.
