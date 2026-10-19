# Linux virtual-webcam output: options and acceptance gates

Research date: 2026-10-19. Acceptance-plan revision: `LINUX-VCAM-1.0`.

Context: [Compare Linux virtual-webcam output mechanisms and acceptance requirements](https://github.com/SapkaLabs/sapkalink/issues/6), part of [Find the path to a Linux client for existing Link to Windows pairing and connected camera](https://github.com/SapkaLabs/sapkalink/issues/1).

## Result and evidence boundary

Keep two output candidates open: a V4L2 capture device backed by v4l2loopback, and a PipeWire video source exposed through the selected desktop's camera policy. V4L2 is a sensible first candidate when conventional device discovery is required and the host permits its kernel module. PipeWire is a sensible candidate when the selected applications actually support its camera path and an extra kernel module is unacceptable. These are conditional planning judgments, not a selected architecture or measured compatibility result.

The input must come from the existing Microsoft Android Link to Windows connected-camera stack. That stack's Linux-usable transport, authorization, codec, framing, timestamps, and media contract remain **unknown**. This note does not replace it with ADB, scrcpy, screen capture, or a new phone companion. A synthetic producer could establish output behavior only; it could not establish Microsoft interoperability. A preview or screen-share success is also insufficient to establish webcam selection in an independent application.

This was public-documentation research. No Linux host, camera, or application runtime was tested; no software was installed, module loaded, capture started, or host policy changed. Every proposed matrix cell below is **NOT RUN**, and the upstream interoperability gate is **UNRESOLVED**.

## What the mechanisms actually expose

| Question | V4L2 virtual capture device | PipeWire source / camera portal |
|---|---|---|
| Object seen downstream | A kernel video-device interface, conventionally `/dev/videoX`; validate that the opened node advertises capture and the required I/O method. [K1] | A graph source; the upstream example publishes `media.class=Video/Source` and negotiates raw video. This does not create a universal `/dev/videoX` camera. [P1] |
| Producer-side work | A userspace producer supplies frames to a loopback device. [L1] | A userspace producer advertises formats, negotiates buffers, and supplies stream data. [P2] |
| Native-app discovery | The consumer needs a working V4L2 capture backend and permission to open the device. [K1][K2] | The consumer needs a compatible PipeWire camera backend, visible source objects, and appropriate session policy. [P3] |
| Sandboxed-app discovery | Direct device access depends on package permissions; native-app success does not establish Flatpak success. [F1] | The camera portal requests camera permission and returns a PipeWire connection; object visibility is then policy-controlled. [P3][P4] |
| Main deployment gate | Compatible module/package for the exact running kernel and an accepted loading/signing path. [L1][K7] | Compatible PipeWire/session-manager/portal/backend and application package versions; this source route does not itself require a v4l2loopback module. [P1][P3][P4] |

**V4L2 capability discovery.** `VIDIOC_QUERYCAP` distinguishes capture from output and reports I/O capabilities. Use `device_caps` when `V4L2_CAP_DEVICE_CAPS` is set; a file called `videoX` alone proves little. [K1] v4l2loopback's `exclusive_caps` option initially advertises output only, switching to capture only after a producer attaches; therefore explicitly test producer-before-consumer and consumer-before-producer discovery. [L1]

**PipeWire classification and visibility.** A `Video/Source` node is evidence of a graph source, not evidence that a browser will list it. Portal-managed clients initially have no visible objects; the session manager grants visibility using camera roles and policy. [P1][P3] `AccessCamera()` followed by `OpenPipeWireRemote()` is an application access path, not a public publishing API. [P4] WirePlumber documents V4L2 and libcamera monitors under video capture; their existence does not validate arbitrary source metadata or a particular portal backend. [W1] The exact camera classification required by the chosen stack must be established in a later, authorized output experiment.

**Compatibility wrapper.** `pw-v4l2` uses `LD_PRELOAD` for a command it launches, translating V4L2 library calls to PipeWire. It is a separate per-process compatibility candidate, not evidence that applications launched normally will enumerate every PipeWire source. Treat any wrapper-dependent result as such. [P5]

## Formats and camera-capability negotiation

V4L2 enumerates pixel formats, frame sizes, and frame intervals separately. `VIDIOC_S_FMT` can adjust requested parameters; acceptance must inspect the returned format instead of assuming the request was honored. Format changes may fail with `EBUSY` while I/O is active. [K3][K4][K5][K6] PipeWire separately negotiates formats and buffer requirements and reports stream-state transitions; a published node without successful negotiation is incomplete output evidence. [P2]

Proposed initial output profile **O1** is 1280×720, progressive, 30 frames/s, SDR, with one explicitly recorded raw pixel format accepted by both selected consumers. YUYV is an initial V4L2 candidate because OBS documents it as its V4L2 default; that is not a promise that a browser or a PipeWire consumer accepts it. [O1] A fallback profile **O0** is 640×480 at 30 frames/s. These are proposed test targets, not Microsoft source capabilities or guaranteed phone modes. Pin the actual negotiated pixel format, plane layout/stride, color range/color space, orientation, frame rate, and buffer transport for each run. Record conversions, resizing, and frame duplication explicitly.

Do not assume compressed Microsoft-origin packets can be handed to either output unchanged. First establish the real input contract, then decide whether decoding, color conversion, rotation, pacing, copies, or a different memory transport is needed. In particular, the PipeWire raw-video example proves a producer interface, not a decoder or Microsoft media adapter. [P1] Latency and CPU costs remain unmeasured; neither route earns a zero-copy or universal format-support claim from these documents.

## Permissions, consumers, and lifecycle

**Permissions.** V4L2 can fail with `EACCES`; the actual device permissions/ACLs and packaged application access must be recorded rather than assuming membership in a named group is sufficient. [K2] Flatpak documents webcam access through broad device permission and portals as distinct access approaches. A raw-USB permission is not proof of access to a virtual V4L2 device. [F1] Camera-portal permission is separate from a website's camera permission inside a browser. Firefox provides site-level allow/block controls. [P4][B1] Do not count sandbox success obtained only after an unrecorded blanket override as default compatibility.

**Multiple consumers.** Multiple opens do not imply simultaneous capture: generic V4L2 documentation describes ownership and `EBUSY` restrictions and recommends userspace mediation for shared streams. [K8] Determine the selected loopback driver's actual concurrent-reader behavior experimentally; do not infer it from generic V4L2. For PipeWire, test actual simultaneous links and each consumer's negotiated format rather than presuming automatic independent conversion. [P2] The bounded target is OBS plus one browser at the same time, in both opening orders, not unlimited consumers.

**Stop/disconnect.** v4l2loopback documents a missing-input timeout image, format locking, and optional frame duplication. A timeout must be selected and tested; a visible frozen frame is not evidence that the phone is still sending. Its module cannot simply be unloaded while applications access its devices. [L1] PipeWire provides stream state and disconnect operations; consumer UI behavior after producer loss is still an app-level test. [P2] Web camera enumeration and device-change notifications are permission/visibility dependent, so record observed selector refresh and recovery rather than requiring identical behavior from every browser. [B2][B3]

Proposed lifecycle target: an explicit stop stops delivery of new phone frames immediately; within 3 seconds, existing consumers show a documented neutral frame, an explicit unavailable state, or an ended stream. Unbounded replay of the last private frame is a failure. A static device entry may remain if it is clearly unavailable. A reconnect must resume only under the already-authorized session policy, without a duplicate selector entry. These are proposed product acceptance requirements, not defaults promised by either mechanism.

**Kernel and Secure Boot.** v4l2loopback is an additional kernel module; its build needs headers matching the exact kernel and a suitable toolchain. [L1] Kernel signature enforcement can reject unsigned or untrusted modules. [K7] Availability of a distro-maintained package, its update/rebuild path, and the distro's Secure Boot signing/trust procedure must be checked for the selected host. This research does not propose disabling Secure Boot or changing trust settings. If the required module cannot be supported within the host's accepted policy, that candidate fails its deployment gate.

## Bounded, versioned acceptance matrix

`LINUX-VCAM-1.0` is the plan version, not a claim of tested software versions. Select **one Linux host H1** first. Before a cell can pass, record distro/release, architecture, exact kernel, desktop/compositor and Wayland/X11 session, Secure Boot state, PipeWire and WirePlumber versions/config revision, portal and backend versions, and (for V4L2) loopback module/package version and parameters. Host and package versions are currently **TBD**. Do not silently expand this into cross-distro certification.

| Cell | Consumer candidate on H1 | Required proof | Current status |
|---|---|---|---|
| V1 | Native OBS Studio, exact version/package TBD, Video Capture Device (V4L2) | Named virtual device is selected as a capture source; O1 or explicitly accepted O0 delivers moving, correctly oriented video. | NOT RUN |
| V2 | Native Firefox, exact stable build/package and capture backend TBD | The named camera is selectable through a browser camera permission/selector flow; an owned, visible secure-context page obtains that camera with `getUserMedia()`. | NOT RUN |
| P1 | Native OBS Studio, exact version/package TBD, Video Capture Device (PipeWire) | Camera source type exists in that build and selects the intended source without substituting Screen Capture. | NOT RUN |
| P2 | Native Firefox, exact stable build/package and camera backend TBD | Its actual backend discovers and opens the source as a camera; record whether access uses the camera portal. | NOT RUN |
| S1 | One Flatpak consumer: OBS or Firefox, exact app ID/build/runtime TBD | Repeat the chosen candidate's selector, format, denial/regrant, and lifecycle checks with package-default permissions; record portal/backend and any required exception. | NOT RUN |
| M1 | OBS + the chosen native browser, same mechanism/profile | Both run for 60 seconds, in each opening order; stopping one leaves the other functional. | NOT RUN |
| L1 | All selected consumer cells | Producer starts late; explicit stop; simulated upstream loss; output-process crash; restart/reconnect; browser/portal permission denial and regrant; repeat three cycles. | NOT RUN |
| I1 | Existing authorized Microsoft connected-camera input → selected output → OBS and browser | Real Microsoft-origin moving frames, correct source identity and permissions, recorded end-to-end behavior. Synthetic/preview/screen-share evidence is inadmissible for this cell. | UNRESOLVED / NOT RUN |

OBS documents a Linux V4L2 capture source, and its 30.1 release added a PipeWire video-capture source. The latter is a historical feature floor, not proof that every package includes it. [O1][O2] Firefox is a browser candidate, not a declaration that every Firefox build uses PipeWire camera capture. Browser tests need a secure context and camera permission; enumeration may omit blocked devices or labels before permission, so capture evidence both before and after granting access. [B1][B2][B4]

For every positive cell, preserve a sanitized run record: exact versions, selected backend/device label, output profile requested and returned, discovery timing, moving-frame evidence, dropped/duplicated-frame counts where observable, stop behavior, recovery time, and a 60-second observation. Measure presentation latency and CPU use without setting an invented performance pass threshold; agree that budget after the real input is understood. Record unsupported format requests and consumer-open failures as failures or explicit exclusions. A rendered application preview is output-consumption evidence only for that exact cell.

## Selection gates and next decision

1. **Upstream gate:** determine whether the existing Microsoft authorization/media path can deliver a usable stream to Linux at all. If not, no downstream mechanism satisfies this project's destination. Preserve UNKNOWN until direct evidence exists.
2. **Host gate:** name H1 and its exact versions. Check module/package/signing acceptability for V4L2; check camera portal/backend and selected consumer support for PipeWire.
3. **Output-only gate:** in a separately authorized later experiment, use a labeled synthetic source to compare V1/V2 against P1/P2, plus S1/M1/L1. This can reject output candidates while the upstream gate remains open; it cannot close I1.
4. **Input-to-output gate:** after the input contract is established, choose the needed decode/conversion/pacing boundary and rerun I1 plus the lifecycle cells with real Microsoft-origin input. Document separate output-only and end-to-end results.
5. **Decision:** choose V4L2 if its required cells pass and host module policy is acceptable; choose PipeWire if its required cells pass with the intended app packages and portal policy. If only a wrapper, extra plugin, or nondefault package permission makes a cell pass, make that an explicit product constraint. If neither passes, retain the decision as open rather than declaring generic Linux webcam compatibility.

## Primary sources

All linked sources were consulted on 2026-10-19. Rolling documentation may change; runtime acceptance must pin actual installed versions. The consulted PipeWire pages displayed 1.6.9 and WirePlumber pages displayed 0.5.18; those are documentation versions, not a tested deployment or minimum-version recommendation.

- [K1: Linux V4L2 capability query](https://docs.kernel.org/userspace-api/media/v4l/vidioc-querycap.html)
- [K2: Linux V4L2 open and errors](https://docs.kernel.org/userspace-api/media/v4l/func-open.html)
- [K3: Linux V4L2 pixel-format enumeration](https://docs.kernel.org/userspace-api/media/v4l/vidioc-enum-fmt.html)
- [K4: Linux V4L2 frame-size enumeration](https://docs.kernel.org/userspace-api/media/v4l/vidioc-enum-framesizes.html)
- [K5: Linux V4L2 frame-interval enumeration](https://docs.kernel.org/userspace-api/media/v4l/vidioc-enum-frameintervals.html)
- [K6: Linux V4L2 get/set/try format negotiation](https://docs.kernel.org/userspace-api/media/v4l/vidioc-g-fmt.html)
- [K7: Linux kernel module signing](https://docs.kernel.org/admin-guide/module-signing.html)
- [K8: Linux V4L2 multiple opens and shared streams](https://docs.kernel.org/userspace-api/media/v4l/open.html#multiple-opens)
- [L1: v4l2loopback upstream README](https://github.com/v4l2loopback/v4l2loopback#readme)
- [P1: PipeWire upstream video-source example](https://pipewire.pages.freedesktop.org/pipewire/video-src_8c-example.html)
- [P2: PipeWire stream negotiation, lifecycle, and buffers](https://pipewire.pages.freedesktop.org/pipewire/page_streams.html)
- [P3: PipeWire portal access control](https://pipewire.pages.freedesktop.org/pipewire/page_portal.html)
- [P4: XDG Camera portal API](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.Camera.html)
- [P5: PipeWire V4L2 compatibility wrapper](https://pipewire.pages.freedesktop.org/pipewire/page_man_pw-v4l2_1.html)
- [W1: WirePlumber video-capture features](https://pipewire.pages.freedesktop.org/wireplumber/daemon/configuration/features.html#video)
- [F1: Flatpak sandbox and device permissions](https://docs.flatpak.org/en/latest/sandbox-permissions.html#device-access)
- [O1: OBS video-capture sources](https://obsproject.com/kb/video-capture-sources)
- [O2: OBS Studio 30.1 release notes](https://obsproject.com/blog/obs-studio-30-1-release-notes)
- [B1: Mozilla Firefox camera-permission controls](https://support.mozilla.org/en-US/kb/how-manage-your-camera-and-microphone-permissions)
- [B2: MDN device enumeration](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/enumerateDevices)
- [B3: W3C Media Capture and Streams, device enumeration/change](https://w3c.github.io/mediacapture-main/#enumerating-devices)
- [B4: MDN getUserMedia requirements](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia)

[K1]: https://docs.kernel.org/userspace-api/media/v4l/vidioc-querycap.html
[K2]: https://docs.kernel.org/userspace-api/media/v4l/func-open.html
[K3]: https://docs.kernel.org/userspace-api/media/v4l/vidioc-enum-fmt.html
[K4]: https://docs.kernel.org/userspace-api/media/v4l/vidioc-enum-framesizes.html
[K5]: https://docs.kernel.org/userspace-api/media/v4l/vidioc-enum-frameintervals.html
[K6]: https://docs.kernel.org/userspace-api/media/v4l/vidioc-g-fmt.html
[K7]: https://docs.kernel.org/admin-guide/module-signing.html
[K8]: https://docs.kernel.org/userspace-api/media/v4l/open.html#multiple-opens
[L1]: https://github.com/v4l2loopback/v4l2loopback#readme
[P1]: https://pipewire.pages.freedesktop.org/pipewire/video-src_8c-example.html
[P2]: https://pipewire.pages.freedesktop.org/pipewire/page_streams.html
[P3]: https://pipewire.pages.freedesktop.org/pipewire/page_portal.html
[P4]: https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.Camera.html
[P5]: https://pipewire.pages.freedesktop.org/pipewire/page_man_pw-v4l2_1.html
[W1]: https://pipewire.pages.freedesktop.org/wireplumber/daemon/configuration/features.html#video
[F1]: https://docs.flatpak.org/en/latest/sandbox-permissions.html#device-access
[O1]: https://obsproject.com/kb/video-capture-sources
[O2]: https://obsproject.com/blog/obs-studio-30-1-release-notes
[B1]: https://support.mozilla.org/en-US/kb/how-manage-your-camera-and-microphone-permissions
[B2]: https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/enumerateDevices
[B3]: https://w3c.github.io/mediacapture-main/#enumerating-devices
[B4]: https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia
