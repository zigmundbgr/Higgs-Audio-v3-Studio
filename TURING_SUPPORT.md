# Experimental NVIDIA Turing (sm_75) support for Higgs Audio v3 Studio 0.3.0

**Status:** An `sm_75` Windows CUDA engine has been compiled and *real-device tested successfully* on an **NVIDIA GeForce RTX 2080 Ti (11 GB)**: the normal v0.3.0 engine loaded the model but generation stalled, while the custom engine generated audio successfully.

This is a **community build**, not an official release or endorsement by [Saganaki22](https://github.com/Saganaki22/Higgs-Audio-v3-Studio).

## Download

**[Download the Windows Turing / RTX 2080 Ti engine from this fork's Releases page](../../releases)**.

The release contains the replacement `audiocpp_engine.dll` plus the original licence and this readme. Install the original Higgs Audio v3 Studio **v0.3.0** from the [upstream releases](https://github.com/Saganaki22/Higgs-Audio-v3-Studio/releases/tag/v0.3.0) first. This is **not a standalone application**.

## Installation (Windows x64)

1. Close Higgs Audio Studio completely, including any unresponsive processes.
2. Extract the community release ZIP. Keep a backup of the original `audiocpp_engine.dll`.
3. Replace **only** `audiocpp_engine.dll` in the engine folder:
   - Installed NSIS/MSI: `%LOCALAPPDATA%\Higgs Audio v3 Studio\engine\`
   - Portable: `resources\engine\` beside the portable app (as appropriate for your installation).
4. **Do not** replace `cublas64_13.dll`, `cublasLt64_13.dll`, the NVIDIA graphics driver, or any model files.
5. Launch Studio, load the engine and a suitable quantised model (e.g. `q4_k_m` for sufficient VRAM), and test a short text-to-speech generation. Restore the backup DLL if the test fails.

If an older temporary engine file exists, do not treat it as the new DLL; only replace the active `audiocpp_engine.dll` after backing it up.

## GPU compatibility

| GPU class | Status |
| --- | --- |
| GeForce RTX 2080 Ti (Turing, compute capability 7.5, 11 GB VRAM) | **Confirmed:** successful audio generation with this community DLL |
| Other GeForce RTX 20-series models using `sm_75` | **Unverified:** likely binary architecture compatibility; VRAM and runtime operations may still limit operation |
| Some GTX 16-series Turing devices | **Unverified:** some share `sm_75`, but have inadequate VRAM for the documented 8 GB `q4_k_m` recommendation |
| RTX 30/40/50 series | Use upstream's official engine; this build is unnecessary |

A compatible compute architecture does **not** guarantee sufficient video memory, suitable NVIDIA driver, or full support for all workflows.

## Reproducing the engine

See [the build workflow](.github/workflows/compile-higgs-rtx2080ti.yml) in this fork. It performs:

- Source checkout: `Saganaki22/Higgs-Audio-v3-Studio`, tag `v0.3.0`, with submodules.
- Compiler: Microsoft Visual Studio 2022 on `windows-2022`.
- NVIDIA Toolkit: **CUDA 13.2**.
- Developer-provided script:
  ```powershell
  ./scripts/build_windows.ps1 -Preset windows-cuda-release -Target audiocpp_engine -CudaArchitectures '75-real' -Jobs 4
  ```
- Artifact: `source/build/windows-cuda-release/bin/audiocpp_engine.dll`.

[The first verified build run](https://github.com/zigmundbgr/Higgs-Audio-v3-Studio/actions/runs/41904732532) completed on 4 November 2026; raw engine DLL SHA-256:

```text
e18f96ab3d6cf7ed76ac179c0be56db5a3a72f7d62dbb609f1a0984960bb4c92
```

The initial engine was successfully compiled but could not be tested on GitHub's runner (no RTX 2080 Ti); the real GPU test was reported by the Windows user afterward. No inference source-code changes were required for this successful test.

## Licence and credits

Original software © 2026 **Saganaki22** and its contributors, distributed under the **Apache License 2.0**; see [LICENSE](LICENSE) in this repository. This community build changes the CUDA compilation architecture to `sm_75`. Preserve applicable third-party licensing and attribution requirements when redistributing.

