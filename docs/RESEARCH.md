# Evidence and research plan

## Method

Planning follows **Wayfinder**: a canonical map issue, native child decision tickets, native blocking edges, and a queryable frontier. Research and manual prerequisites resolve decisions; implementation is outside this initial map. Refer to tickets by linked titles rather than bare issue numbers.

The installed **reverse-engineer-anything (REA)** workflow supplies the evidence discipline: bind exact target/version/digest, inventory before deep analysis, follow a specific feature through control/data flow, and distinguish observations, inferences, and unknowns. Installing skill instructions does not mean the analysis tools or engines are connected.

For future authorized artifact analysis, use the exact selected Windows Phone Link / Cross Device Experience Host and Android Link to Windows artifacts. Record relevant package versions and hashes and a narrow feature hypothesis. Use the advertised tools' actual schemas. Keep evidence references and coverage limitations; an ordinary shell observation is not REA Evidence.

For passive runtime analysis, observe only the user's authorized devices/software and the declared feature scenario. No authentication bypass, token-store extraction, privileged first-party-client impersonation, unsolicited service probing, unknown-script execution, or unrelated personal traffic collection. No tool installation, MCP configuration repair, kernel changes, or security-setting changes are part of the initial planning publication.

## Evidence classes

| Class | Meaning |
| --- | --- |
| Official documentation | Stated vendor behavior or a public specification; not a test of our client |
| Artifact observation | Version/digest-scoped static evidence; not proof of runtime execution |
| Runtime observation | A declared, authorized scenario on named versions; coverage remains bounded |
| Community research lead | A third party's claim to validate independently |
| Proposed behavior | Product intent or a design option; not observed compatibility |
| Unknown | Evidence does not yet establish the claim |

## Initial source leads

- [Microsoft: use your mobile device's camera](https://support.microsoft.com/en-gb/windows/apps/phonelink/use-your-mobile-device-s-camera) establishes the Windows connected-camera feature and its reference prerequisites; it is not a Linux protocol specification.
- [Microsoft: manage mobile devices](https://support.microsoft.com/en-au/windows/apps/phonelink/manage-mobile-devices-in-windows) is a source for current linking/permission behavior to inspect.
- [Microsoft's original connected-camera announcement](https://blogs.windows.com/windows-insider/2024/02/29/ability-to-use-a-mobile-devices-camera-as-a-webcam-on-your-pc-begins-rolling-out-to-windows-insiders/) is historical architecture context, not current minimum-version authority.
- [Microsoft MS-CDP specification](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cdp/da780a95-4b15-4300-8bb9-5da11b9514ab) and [channel scope](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cdp/00b14441-7879-483d-96e4-f21306296eab) are public protocol leads. Their applicability and inner camera-channel coverage require investigation.
- [ZonD80/msft-phone-link-docs](https://github.com/ZonD80/msft-phone-link-docs) is an unofficial, version-sensitive YPP/DCG documentation lead. Its README describes authentication/pairing/relay/clipboard/telemetry and does not demonstrate a working connected-camera Linux client. A listed MIT license is not evidence of Microsoft's endorsement or camera compatibility. Validate its source boundaries, exact revision, license, and claims before reuse; do not run its scripts or copy credentials.
- [Linux V4L2 video capture interface](https://docs.kernel.org/userspace-api/media/v4l/dev-capture.html) and [PipeWire overview](https://docs.pipewire.org/page_overview.html) inform the Linux output investigation. They do not establish that an arbitrary media source appears in every application.

KDE Connect and scrcpy belong in a clearly labeled **functional alternative/comparison** category. They cannot count as fulfillment of existing Link to Windows interoperability. ADB/scrcpy capture is not the proposed camera path.

## Research questions and required evidence

| Question | Evidence needed | Current state |
| --- | --- | --- |
| Can an independent Linux client pair legitimately? | Current vendor behavior, auth/client/scopes constraints, independently reproducible authorized pairing | Unknown |
| Which Microsoft components carry connected-camera control and media? | Exact artifact/feature identity, bounded static/runtime trace, media/session contract | Unknown |
| Is the camera path reusable from Linux? | Actual Microsoft-origin frames delivered to an independent Linux client with supported authorization | Unknown |
| Does existing OSS already provide relevant compatibility? | Primary repo/docs/code, pinned revision, reproducible feature evidence, license and maintenance | Not yet verified; no claim that none exists |
| Which Linux output satisfies the app matrix? | V4L2/PipeWire/portal documentation and authorized consumer tests against the actual format contract | Undecided |
| What can be published and reused? | Original/redacted findings, dependency license review, secrets/proprietary-material exclusion | Original planning docs only |

## Evidence publication

Publish original explanations, source links, synthetic examples and sufficiently redacted findings. Keep raw proprietary packages/decompiled code, private captures, personal device/account identifiers, camera recordings, credentials and tokens out of the public repository and issues. Original project documentation is MIT licensed; external work retains its own license and attribution.

The initial 19–20 October 2026 research window may produce bounded findings and explicit blockers. It does not require resolving every decision, running the Microsoft application, or implementing a client. Follow-up research branches are disposable `research/<topic>` branches, never merged automatically and with no implementation PR.
