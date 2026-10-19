# SapkaLink

An independent open-source Linux client **intended to interoperate with the existing Android Microsoft Link to Windows stack**.

**Status: research and product planning. Compatibility is unproven. There is no working client or camera implementation in this repository.** SapkaLink is an independent project and is not affiliated with Microsoft.

The two product checkpoints are:

1. Pair and reconnect a phone using its existing Link to Windows software and legitimate user authorization.
2. Receive that stack's phone-camera stream and expose it as a **Linux virtual webcam selectable by other applications**.

A new Android companion, an ADB/scrcpy camera path, screen mirroring, or an in-app preview does not satisfy these checkpoints.

Start with the [product description](docs/PRODUCT.md), [proposed end-to-end flow](docs/FLOW.md), and [evidence and research plan](docs/RESEARCH.md). The [glossary](GLOSSARY.md) distinguishes pairing, camera sessions, and virtual webcams.

<!-- WAYFINDER-LINK -->
Canonical map: [Find the path to a Linux client for existing Link to Windows pairing and connected camera](https://github.com/SapkaLabs/sapkalink/issues/1). Its native child issues are the task list; native dependency links show what can be worked next.
<!-- /WAYFINDER-LINK -->

The initial research window is **19–20 October 2026**. It is a research window, not an implementation or delivery commitment. Research may conclude that legitimate independent pairing or camera interoperability is not currently feasible.

Original project documentation is [MIT licensed](LICENSE). This repository does not distribute Microsoft applications, binaries, decompiled proprietary code, credentials, or personal recordings.
