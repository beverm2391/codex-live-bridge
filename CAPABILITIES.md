# Agent Capability In Ableton Live

This bridge is a composable control surface, not a set of hardcoded workflows.
An agent can combine inspection, mutation, observation, and readback against
Ableton Live's object hierarchy. The useful question is not whether a named
workflow exists. It is whether the agent has every capability that workflow
needs.

This document maps that boundary. **Current** means the bridge has an explicit
command or wrapper with validated replies and documented limits. **Partial**
means the generic LiveAPI route can reach part of the behavior, but the bridge
does not yet make the whole operation dependable. **Wanted** means a real
workflow is blocked.

The [protocol](PROTOCOL.md) owns exact command contracts and safety classes.
The [command cheat sheet](bridge/commands.md) owns examples. Ableton's
[Live Object Model](https://docs.cycling74.com/apiref/lom/) defines the larger
platform surface; it is not a promise that this bridge has qualified every
property and function.

## 1. Live Set

### 1.1 Inspection: current

An agent can inspect set status, tempo, meter, scale, tuning, Session context,
tracks, return tracks, Main, devices, mixer state, and bounded Arrangement
structure. Generic object description, child traversal, property reads, and
observers cover smaller questions without a new named command.

### 1.2 Tempo and meter: current

An agent can read, set, observe, and verify tempo and time signature.

### 1.3 Transport: partial

The generic RPC can reach documented `Song` properties and functions such as
play state, song time, loop state, start, stop, and cue movement. These are not
yet tested and documented as one dependable transport capability with explicit
preconditions and readback. Reliable playback, locating, looping, recording,
and cueing remain wanted. See Ableton's
[`Song` model](https://docs.cycling74.com/apiref/lom/song/).

## 2. Tracks And Mixer

### 2.1 Track structure: current

An agent can inspect tracks, create MIDI or audio tracks, ensure a MIDI track
count, rename tracks, and delete bounded batches. It can inspect mixer state and
set reachable properties through generic RPC.

### 2.2 Routing and richer mixer control: partial

LiveAPI exposes routing, arm, mute, solo, send, and other mixer controls. The
bridge's named wrappers do not yet make all of these controls dependable. A
workflow that depends on them still needs targeted runtime testing and
readback.

## 3. Clips

### 3.1 Session MIDI clips: current

An agent can create or replace a Session MIDI clip from notes, append notes,
and inspect the clip with correlated, packet-bounded replies. Replacement is a
destructive operation. The agent must inspect first and verify afterward.

### 3.2 Arrangement clips: partial

An agent can inspect Arrangement projects, tracks, clips, devices, and optional
MIDI notes. The generic RPC can reach additional clip functions, but there is
no tested set of insert, copy, move, and edit operations for arranging clips
at exact times. That blocks dependable Arrangement editing by an agent.

### 3.3 Audio clip insertion: wanted

The bridge has no explicit command to create a Session or Arrangement audio
clip from a file. Ableton documents audio clip creation in the
[`Track` model](https://docs.cycling74.com/apiref/lom/track/), but a generic
call alone does not provide file resolution, placement validation, batching,
or readback. A multi-file drop workflow needs one reliable insertion capability
that accepts ordered files and placements and returns the created clips.

### 3.4 Warp control: partial

Generic RPC can reach audio clip properties such as `warping` and `warp_mode`,
and the Live Object Model documents warp-marker reads and writes. The bridge
does not yet validate clip type, operation order, marker constraints, deferred
warp changes, or final marker state in one operation. Reliable warp enablement,
mode selection, and marker editing remain wanted. See Ableton's
[`Clip` model](https://docs.cycling74.com/apiref/lom/clip/) and
[warping guide](https://www.ableton.com/en/live-manual/12/audio-clips-tempo-and-warping/).

## 4. Devices

### 4.1 Inspection and parameters: current

An agent can list devices, inspect parameters, set a validated parameter value,
insert rack chains, and set a Drum Rack chain note.

### 4.2 Device loading: partial

On Live 12.3 or later, an agent can insert native Live devices by exact device
name. That insertion API does not load plug-ins or Max for Live devices. The
bridge also does not resolve a natural-language request to a library item.
Workflows such as "find my compressor and load it after the instrument" need
search plus a stable item reference, followed by a verified load operation.

## 5. Browser And Library

### 5.1 Search, filtering, and tags: wanted

The bridge has no browser search, preview, filter, or tag surface. These are
separate from the Live Set hierarchy that the bridge currently controls.
Ableton's [browser guide](https://www.ableton.com/en/live-manual/12/working-with-the-browser/)
describes the user-facing search and tag model.

A useful agent capability would search devices, presets, samples, and clips. It
would return stable results with relevant tags and a reliable file identifier.
It would pass the selected result to a compatible loading or clip-insertion
capability. Tag editing is wanted, but no specific blocked organization
workflow defines its shape yet.

## 6. Composition Boundary

The bridge should keep exposing small tools with clear inputs, effects, and
verification. Codex combines those tools with instructions and the current Live
Set. The bridge should not hardcode workflow logic as fixed command sequences.

The next capability should be chosen by a blocked real workflow. Today the
largest named blockers are audio clip insertion, multi-file placement, browser
search and item identity, plug-in or Max for Live loading, dependable warp
control, and dependable transport control.
