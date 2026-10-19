# Product description — planning draft

## Product intent

Let a Linux user connect an Android phone through the phone's existing Microsoft Link to Windows integration and choose the phone camera in Linux applications as a normal webcam. The goal is interoperability with that existing phone stack, rather than a replacement Android companion.

The immediate deliverable is an evidence-backed product description, end-to-end flow, and decision/task map. This repository does not promise a complete Phone Link port or a working camera client.

## Confirmed requirements

- Public, independent, open-source SapkaLabs project.
- Linux desktop client; Android-first because the specified phone-side software is Link to Windows.
- Reuse the existing Microsoft Android stack for both the pairing investigation and the camera investigation.
- A real virtual webcam usable by other applications is the second checkpoint. Preview, screen mirroring, recording playback, or screen sharing is insufficient.
- Product planning and research first; no application implementation in the initial planning phase.
- Existing open-source work must be evaluated for actual Microsoft-stack compatibility, evidence, license, maintenance, and reusable parts.
- Routine research questions should be answered from evidence, with assumptions and unknowns recorded explicitly.

## Checkpoint 1 — phone pairing and connection

Success would mean a Linux client can establish a legitimate, user-approved association with an unchanged Link to Windows installation, reconnect, select the correct phone, and revoke the association. It must not depend on copied Windows token stores, first-party client impersonation, access bypass, or extracted credentials.

The investigation must establish discovery, supported authentication/client registration, required consent and scopes, enrollment/pairing lifecycle, local versus cloud dependencies, reconnect, expiry, and revocation. These are questions, not selected protocols or demonstrated capabilities.

An official public transport specification or an unofficial protocol description does not by itself establish independent-client authorization or feature interoperability.

## Checkpoint 2 — Microsoft camera stream to Linux virtual webcam

After the pairing gate, establish whether the *actual Microsoft connected-camera feature* can supply authorized frames to an independent Linux client. Identify the responsible phone/desktop components, control and media channels, capability negotiation, codecs, permissions, session lifetime, and disconnect behavior before choosing an implementation.

The Windows reference is **Settings → Bluetooth & devices → Mobile devices → Use as connected camera**. Microsoft's current support page lists Windows 11+, Android 10+, and Link to Windows 1.24022.0+ for that Windows feature. These are reference requirements, not a promised SapkaLink support matrix. The feature's presence does not prove Linux interoperability. [Microsoft camera reference](https://support.microsoft.com/en-gb/windows/apps/phonelink/use-your-mobile-device-s-camera).

The downstream output must become a Linux camera source that independent applications can discover and select. Investigate V4L2 and PipeWire/app-portal behavior, native and sandboxed applications, format negotiation, device naming, and multiple consumers. Do not assume that a PipeWire node alone is discoverable by every webcam application.

Candidate acceptance examples are OBS and a browser's camera selector, subject to a documented Linux/app/version matrix. The final matrix and performance thresholds remain decisions. Validation must use a live authorized Microsoft-origin camera stream; a synthetic source can test the output adapter but cannot establish end-to-end compatibility.

Initial camera scope is video only: no microphone forwarding or recording by default. If the phone or desktop denies camera permission, show the reason and stop the camera session without bypassing consent.

## Gates and decision policy

1. Establish an authorized, reproducible reference environment and artifact versions.
2. Determine whether legitimate Linux pairing is feasible.
3. Independently determine whether the Microsoft camera channel is available and reusable.
4. Choose Linux webcam exposure against the actual media contract and app matrix.
5. Record a go/no-go or constrained scope before selecting implementation work.

If a gate fails, document exactly what was established and what remains unknown. Do not silently replace the requirement with KDE Connect, scrcpy, ADB capture, or a new phone app. A change of product scope requires the owner's decision.

## Current exclusions and later fog

No application code, installers, kernel/module changes, new phone companion, binary redistribution, credential extraction, or capture of unrelated personal traffic in this planning phase. No promise of Microsoft service access or protocol stability.

Notifications, SMS, calls, clipboard, files, app streaming, iOS, distributions, packaging, background service design, and broader Phone Link parity remain later scope decisions. They are not implicitly accepted for the first two checkpoints.

The independent Linux implementation's feasibility, effort, and maintenance cost remain unknown until the decision tickets are resolved.
