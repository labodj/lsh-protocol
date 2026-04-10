# LSH Protocol

This document is auto-generated from `shared/lsh_protocol.json` by `tools/generate_lsh_protocol.py`.
Do not edit it manually.

- Spec revision: `2026041004`
- Wire protocol major: `3`
- Revision note: Code-only revision. Never transmitted on wire.
- Wire goal: compact payloads with single-character keys and numeric command IDs

## Trusted Environment

The protocol assumes a trusted environment and a cooperative broker. There is no built-in authentication, integrity or confidentiality layer on the LSH payloads.

## Handshake Contract

- `lsh-core` sends `BOOT` after configuration has been finalized.
- When `lsh-esp` receives `BOOT` from the controller during normal operation, it forwards that payload on the device `misc` topic so Node-RED can request a fresh `details + state` cycle.
- When MQTT becomes ready, `lsh-esp` sends `BOOT` back to the controller to force a fresh `details + state` re-sync.
- Topology is treated as static between two controller boots. Runtime hot topology changes are intentionally unsupported.

## Wire Constraints

- `i`, `c`, actuator IDs and button IDs are positive 8-bit values. `0` is reserved as a sentinel for missing or invalid fields and must not be used on the wire.

## JSON Keys

| Constant | Wire Key | Meaning |
| --- | --- | --- |
| `KEY_PAYLOAD` | `p` | Command discriminator. |
| `KEY_PROTOCOL_MAJOR` | `v` | Handshake-only protocol major. |
| `KEY_NAME` | `n` | Device name. |
| `KEY_ACTUATORS_ARRAY` | `a` | Actuator ID array. |
| `KEY_BUTTONS_ARRAY` | `b` | Button ID array. |
| `KEY_CORRELATION_ID` | `c` | Click correlation ID. |
| `KEY_ID` | `i` | Numeric actuator or button ID. |
| `KEY_STATE` | `s` | Actuator state or bitpacked state bytes. |
| `KEY_TYPE` | `t` | Click type discriminator. |

## Commands

| Value | C++ | TypeScript | Golden JSON Example | Description |
| --- | --- | --- | --- | --- |
| 1 | `DEVICE_DETAILS` | `DEVICE_DETAILS` | `{"p":1,"v":3,"n":"c1","a":[1,5],"b":[7]}` | Device details payload with handshake-only protocol major. |
| 2 | `ACTUATORS_STATE` | `ACTUATORS_STATE` | `{"p":2,"s":[90,3]}` | Bitpacked actuator state payload. |
| 3 | `NETWORK_CLICK_REQUEST` | `NETWORK_CLICK_REQUEST` | `{"p":3,"c":42,"i":7,"t":1}` | Network click request with correlation ID. |
| 4 | `BOOT` | `BOOT` | `{"p":4}` | Controller boot notification and re-sync trigger. |
| 5 | `PING_` | `PING` | `{"p":5}` | Ping or heartbeat payload. |
| 10 | `REQUEST_DETAILS` | `REQUEST_DETAILS` | `{"p":10}` | Request device details. |
| 11 | `REQUEST_STATE` | `REQUEST_STATE` | `{"p":11}` | Request current state. |
| 12 | `SET_STATE` | `SET_STATE` | `{"p":12,"s":[90,3]}` | Set all actuators. |
| 13 | `SET_SINGLE_ACTUATOR` | `SET_SINGLE_ACTUATOR` | `{"p":13,"i":5,"s":1}` | Set a single actuator. |
| 14 | `NETWORK_CLICK_ACK` | `NETWORK_CLICK_ACK` | `{"p":14,"c":42,"i":7,"t":1}` | Acknowledge a network click with correlation ID. |
| 15 | `FAILOVER` | `FAILOVER` | `{"p":15}` | General failover signal. |
| 16 | `FAILOVER_CLICK` | `FAILOVER_CLICK` | `{"p":16,"c":42,"i":7,"t":2}` | Failover for a specific click with correlation ID. |
| 17 | `NETWORK_CLICK_CONFIRM` | `NETWORK_CLICK_CONFIRM` | `{"p":17,"c":42,"i":7,"t":1}` | Confirm a network click after ACK using the same correlation ID. |
| 254 | `SYSTEM_REBOOT` | `SYSTEM_REBOOT` | `{"p":254}` | ESP system reboot command. |
| 255 | `SYSTEM_RESET` | `SYSTEM_RESET` | `{"p":255}` | ESP system reset command. |

## Click Types

| Value | C++ | TypeScript |
| --- | --- | --- |
| 1 | `LONG` | `Long` |
| 2 | `SUPER_LONG` | `SuperLong` |

## Pre-serialized Static Payloads

These payloads are generated as compile-time byte arrays for zero-allocation hot paths.

| Name | Command | C++ Enum | C++ Symbol | Targets | JSON Bytes | MsgPack Bytes |
| --- | --- | --- | --- | --- | --- | --- |
| `BOOT` | `BOOT` | `BOOT` | `BOOT` | `core`, `esp` | `'{', '"', 'p', '"', ':', '4', '}', '\n'` | `0x81, 0xA1, 0x70, 0x04` |
| `PING` | `PING` | `PING_` | `PING` | `core`, `esp` | `'{', '"', 'p', '"', ':', '5', '}', '\n'` | `0x81, 0xA1, 0x70, 0x05` |
| `ASK_DETAILS` | `REQUEST_DETAILS` | `ASK_DETAILS` | `ASK_DETAILS` | `esp` | `'{', '"', 'p', '"', ':', '1', '0', '}', '\n'` | `0x81, 0xA1, 0x70, 0x0A` |
| `ASK_STATE` | `REQUEST_STATE` | `ASK_STATE` | `ASK_STATE` | `esp` | `'{', '"', 'p', '"', ':', '1', '1', '}', '\n'` | `0x81, 0xA1, 0x70, 0x0B` |
| `GENERAL_FAILOVER` | `FAILOVER` | `GENERAL_FAILOVER` | `GENERAL_FAILOVER` | `esp` | `'{', '"', 'p', '"', ':', '1', '5', '}', '\n'` | `0x81, 0xA1, 0x70, 0x0F` |
