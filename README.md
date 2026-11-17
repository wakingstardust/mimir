# mimir, no-AVX builds

This branch holds only a build workflow. It rebuilds upstream [MakerViking/mimir](https://github.com/MakerViking/mimir) releases for x86-64 CPUs without AVX (an Intel Celeron N5095 NAS), because the stock release statically links an onnxruntime compiled for AVX2 and crashes with an illegal instruction.

The one change is in the root `Cargo.toml`: fastembed drops its default features and uses `ort-load-dynamic`, so the binary loads Microsoft's official onnxruntime from a file named by `ORT_DYLIB_PATH`. It also carries `patches/mcp-recall-json.patch`, which adds an optional `json` flag to the MCP `recall` tool so a caller gets each hit's path, project and score (the command line already prints this). Releases are tagged `nas-<upstream tag>-r<PATCH_REV>` and list the onnxruntime version they were tested with.

The workflow runs weekly and on demand (Actions, nas-build, Run workflow, optional tag). It skips tags already released and stops if upstream changes the `ort` pin, since that needs a matching onnxruntime version chosen by hand.

The `main` branch mirrors upstream and is not used.
