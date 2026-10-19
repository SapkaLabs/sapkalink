# Microsoft connected-camera evidence and validation gates

Research date: 2026-10-19. Public-source planning pass for [Identify evidence and validation gates for Microsoft connected-camera transport](https://github.com/SapkaLabs/sapkalink/issues/4).

## Decision supported by this pass

Use **Windows Settings > Bluetooth & devices > Mobile devices > Use as connected camera** as the reference feature. Its Android side is the existing Microsoft **Link to Windows** app. Microsoft's original announcement identifies **Cross Device Experience Host** as a required Windows component. These facts identify the feature to investigate; they do not identify its current process topology, control protocol, media transport, or an independently usable Linux interface. [Current camera support][camera] [Historical announcement][announcement]

**Independent Linux reception remains unproven.** The reviewed public material supplies product behavior and protocol leads, rather than a validated connected-camera contract. Keep the investigation open. A Linux virtual webcam can be evaluated after actual Microsoft-origin frames and a legitimate independent-client authorization path are established; selecting an output API cannot resolve the input-side unknowns.

Scope: original, sanitized planning notes only. No application implementation, Android replacement companion, scrcpy/ADB camera input, installation, target execution, runtime capture, credential extraction, authentication bypass, or proprietary artifact publication occurred. No binary was selected or analyzed, and there are no REA Evidence IDs for this pass. Public documentation observations below must not be described as runtime observations.

## What the official sources establish

| Evidence | Established statement | Limit |
| --- | --- | --- |
| Current Microsoft camera support | The reference requirements are Windows 11 or later, Android 10 or later, and Link to Windows 1.24022.0 or later. Enable the linked device in Mobile devices with Use as connected camera. The phone then appears as a camera choice to Windows applications. | Requirements for Microsoft's Windows experience, not a Linux compatibility guarantee. [Source][camera] |
| Current Microsoft camera support | Selecting the phone camera can require permission on the phone; troubleshooting names Link to Windows camera permission and a PC camera window with refresh guidance. Rotation behavior is described. | Does not specify a consent message format, per-session permission lifetime, or remote authorization API. [Source][camera] |
| Current Microsoft Mobile devices support | The linking UI uses Add device, a QR code, and phone prompts. The page asks for nearby powered devices, both connected to a Wi-Fi network, and lists connected camera among managed features. | This does not prove same-subnet, LAN-only, Wi-Fi Direct, cloud-relay, or Bluetooth media transport. Its general Link to Windows floor, 1.23112.189, is not the camera-specific floor. [Source][manage] |
| Windows Insider announcement, 2024-02-29, updated 2024-03-19 | The rollout was wireless, offered front/back switching, stream pause and device effects, and required a Cross Device Experience Host Microsoft Store update through Mobile devices / Manage devices. | Historical architecture clue. Its Android 9+ and beta Link to Windows 1.24012* requirements must not replace today's camera support requirements. [Source][announcement] |

**Planning inference:** prioritize the Mobile devices / Cross Device Experience Host feature boundary. Do not substitute successful Phone Link UI activity, clipboard relay, screen mirroring, or a webcam API example for camera-path evidence. The official pages above do not establish whether the Phone Link desktop executable must be open, which process owns a current session, or which IPC/media subsystem it uses.

## Public protocol leads and their evidence limits

### MS-CDP is a possible substrate, not a camera specification

Microsoft's [MS-CDP overview][cdp-overview] describes discovery, user/device authentication, authorized channels, and app-service connections. The [published-version index][cdp-root] lists protocol revision 9.0 dated 2023-10-09. These statements concern MS-CDP, not evidence that this connected-camera feature uses it.

The [Common Header section][cdp-header] explicitly assigns the inner protocol and message types to each channel, with the decrypted payload passed to its owner. Consequently, knowing its session/channel envelope would still leave a feature-specific camera contract to establish. The [App Control Messages section][cdp-app] lists URI launch, app-service, and resource operations; those generic operations do not identify a connected-camera service or schema.

The [Transport section][cdp-transport] lists several supported transports and says it does not define a particular transport for these messages. Its BLE/Bluetooth/LAN/Wi-Fi Direct list cannot select the camera's media path. The [Versioning and Capability Negotiation section][cdp-version] concerns V3 and its header version, not an enumerated camera capability contract.

**Open hypothesis:** MS-CDP might participate in discovery, control, or another layer. The reviewed sources do not prove which, or that it participates at all. In particular, CDP, DCG and YPP must not be treated as interchangeable protocol names merely because they concern connected devices.

### Unofficial YPP/DCG notes are useful, version-sensitive leads

Examined source revision: `243a16673475aec66e93a494e8c66c89b61c4b23` of ZonD80/msft-phone-link-docs. The [README][lead-readme] describes authentication, enrollment, pairing, wake, relay, framing, clipboard and telemetry, and states that its material is unofficial and derived from canary builds, schemas and service responses. This pass did not reproduce the author's observations.

The [constants file][lead-constants] distinguishes Phone Link and Cross Device application identities and associates a Cross Device version `1.26061.91.0` with its observations. It also lists platform-channel enums such as `namedStreams`, `videoStreaming` and `videoTarget`. These are **target-selection/search seeds**, not proof of connected-camera entry points, current installed versions, codec selection, control semantics or frame framing. No first-party identity or authorization values from these notes were used against a service.

A bounded term search of README, ARCHITECTURE, PROTOCOL, CONSTANTS and MESSAGE_FORMATS at that revision used `camera`, `webcam`, `video`, `media`, `webrtc`, `rtp`, `rtcp`, `codec`, `udp`, and `stream`. Relevant hits were the video/stream enum names, generic relay framing, and photo/media-sync configuration. This did not locate a camera session/codec contract within those five files. That is a limited negative result, not a claim that no public implementation or documentation exists. The source's title or broad protocol claims are not evidence that it supports Microsoft's connected-camera feature. [Repository at examined revision][lead-tree]

## Feature contract checklist

"Unknown" means not established by this bounded pass. It does not mean impossible or absent from the product.

| Question | Status and evidence boundary | Minimum evidence needed next |
| --- | --- | --- |
| Which components implement the feature? | Partly answered at product level above. Exact current package/process/service/virtual-camera ownership is unknown. | Exact Windows build, Android/LTW versions, relevant Windows package versions, and a feature-correlated ownership inventory on a working reference pair. |
| How is the camera capability advertised and enabled? | UI availability is documented; feature identifiers, messages, negotiation versions and permission states are unknown. Generic MS-CDP V3 and unofficial video enum names do not fill this gap. [Sources][cdp-version] [Lead][lead-constants] | A feature-specific entry point and version-scoped capability/state transition tied to the connected-camera UI. |
| What authorizes an independent peer? | Existing linking and phone consent are visible product behavior. Permitted independent-client enrollment, app identity, scopes, device trust and session authorization are not established. [Source][manage] | A legitimate independent-client path that works without copying first-party credentials or impersonating an allowed Microsoft client. Coordinate with the pairing/authentication investigation. |
| How are camera controls sent? | Front/back and pause are documented historically; wire messages, acknowledgments, errors, rotation metadata and effects negotiation remain unknown. [Source][announcement] | Correlated, version-scoped evidence for start/stop and one changed control, with request/result semantics. |
| Where does media travel? | Wireless use is documented historically. Discovery/signaling/media separation, endpoints, direct versus relay operation, carrier protocol, encryption binding and fallback remain unknown. [Source][announcement] | Attribute a media session to the feature and its owner; distinguish control traffic from encoded frames. A host name or connection spike alone is insufficient. |
| What are the media/decoder requirements? | Codec, profile/level, container or packetization, resolution/FPS negotiation, timestamps, orientation, color format, keyframes and recovery are unknown. | Negotiated format and frame boundaries supported by feature-specific evidence, then successful decode of the actual stream. Do not assume H.264, RTP, WebRTC, MJPEG or any fixed size/rate. |
| What is the session lifetime? | No tested state machine. Phone lock/background behavior, keepalive, restart/reconnect, concurrent consumers, grant persistence and revocation semantics remain unknown. | One controlled lifecycle sequence covering start, stop, interruption and revocation with named versions. |
| Can Linux receive and expose this camera? | Unresolved: no frames received by an independent Linux peer in this pass. | Complete authorization, feature control and actual Microsoft-stream reception before evaluating the Linux virtual-webcam adapter. |

## Smallest staged future validation

These are proposed gates, not work authorized or executed by this public-source pass. Each future runtime/artifact task must name its targets, versions, allowed observations and publication boundary before it starts. Keep private raw material outside the public repository and publish only original sanitized findings.

1. **Establish a working reference and exact owner.** On the owner's explicitly selected Windows/Android pair, record versions and enable the documented feature normally. Use a benign physical test pattern, with no recording required. Record which Windows packages/processes and camera device appear across idle, camera selected, and camera closed. Begin with existing UI and read-only metadata; no tools or tracing providers need installation for this gate. Pass when the correct connected-camera feature works and its current implementation owner is attributable. If it fails, resolve that baseline before interpreting protocol absence.

2. **Bind one feature hypothesis to one entry point.** If separately authorized, inspect only the named, owned package artifacts or narrowly scoped existing diagnostics for the camera owner's capability and start/stop route. Record artifact hashes, exact package versions and evidence coverage privately. Correlate idle -> select connected camera -> phone permission -> live preview -> pause/resume -> close, changing one state at a time. Pass when a specific control path and candidate media owner are tied to the UI transition. Strings, enums, imported libraries and generic CDP success are insufficient alone.

3. **Determine the real session and media contract.** Only after a concrete owner/route exists, authorize a separate narrowly scoped observation if required. Restrict it to the named devices and declared camera sequence; do not collect unrelated personal traffic. Establish discovery, authorization, control and media boundaries; negotiation fields; actual codec/framing; lifecycle and revocation. Encrypted traffic metadata may establish candidate endpoints and timing but cannot establish plaintext commands or codec contents. Do not extract token stores or keys to bridge missing evidence. Stop and report an unavailable lawful/authorized observation path instead of bypassing authentication.

4. **Prove independent reception before output integration.** A later implementation experiment may proceed only with a permitted independent-client authorization mechanism. Its acceptance condition is new frames from the existing Microsoft Android app and actual connected-camera protocol, received and decoded by the independent Linux client, with start/stop and consent respected. Windows screen/camera recapture, a replacement Android app and ADB/scrcpy input do not meet that condition. The Linux camera adapter is a subsequent consumer, with its own application compatibility tests.

The smallest useful next task is gate 1, followed by a narrowly scoped gate 2 target-selection task. It is premature to prescribe a socket protocol or codec implementation. If a legitimate independent-client authorization path cannot be established, record that as the blocker before broader media reverse engineering.

## Search and completion boundary

Read official camera and Mobile devices support, the original camera rollout announcement, the MS-CDP overview/common-header/app-control/transport/versioning sections, and the pinned unofficial notes identified above. Public-source searches were bounded to the feature/component and MS-CDP leads. Microsoft Q&A/community answers were not promoted to vendor specifications. No unknown scripts were executed; no Microsoft services were probed; no apps, packages, credentials, captures or installed-device state were inspected.

Answered: correct product feature, current documented prerequisites, historical component lead, limits of MS-CDP and the unofficial notes. Partially answered: product-level component roles and user-visible consent/controls. Unresolved: current implementation ownership and all independent-client camera transport/auth/media/lifecycle details. This supports a research plan, not a feasibility or interoperability claim; the issue remains open.

[camera]: https://support.microsoft.com/en-gb/windows/apps/phonelink/use-your-mobile-device-s-camera
[manage]: https://support.microsoft.com/en-au/windows/apps/phonelink/manage-mobile-devices-in-windows
[announcement]: https://blogs.windows.com/windows-insider/2024/02/29/ability-to-use-a-mobile-devices-camera-as-a-webcam-on-your-pc-begins-rolling-out-to-windows-insiders/
[cdp-root]: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cdp/f5a15c56-ac3a-48f9-8c51-07b2eadbe9b4
[cdp-overview]: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cdp/929c2238-6d49-4ba4-a36a-37e732c4f736
[cdp-header]: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cdp/00b14441-7879-483d-96e4-f21306296eab
[cdp-app]: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cdp/62c780c1-d5a3-4ee3-bd51-c82746c10bbb
[cdp-transport]: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cdp/3e6be5e8-e315-4f4b-97c7-ec0045d9bbb9
[cdp-version]: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cdp/6592cc03-1cf5-4a92-b9c1-97d57de9ccb9
[lead-tree]: https://github.com/ZonD80/msft-phone-link-docs/tree/243a16673475aec66e93a494e8c66c89b61c4b23
[lead-readme]: https://github.com/ZonD80/msft-phone-link-docs/blob/243a16673475aec66e93a494e8c66c89b61c4b23/README.md
[lead-constants]: https://github.com/ZonD80/msft-phone-link-docs/blob/243a16673475aec66e93a494e8c66c89b61c4b23/CONSTANTS.md
