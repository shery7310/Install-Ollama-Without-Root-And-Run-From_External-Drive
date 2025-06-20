# Running GGUF Models locally and without Root Privileges (Linux)

This script lets you run Ollama from an external or secondary drive without needing root access. It builds on the approach shared in this  <a href="https://github.com/zherenz/Ollama-installation-without-root-privilege/tree/main?tab=readme-ov-file">repository</a> by [zherenz](https://github.com/zherenz) and offers a more flexible, user-controlled setup. In contrast, the official script at ollama.com/install.sh installs Ollama system-wide, requires sudo or root privileges, creates a dedicated system user (ollama), and sets up a systemd service—all within root-owned directories.

### Why run Ollama from other drive?

Running Ollama from another drive allows you to save space on your main system drive, avoid needing root access, and keep your models and binaries in a portable or user-controlled location. It's especially useful on shared systems, external storage setups, or when managing large model files. 

Basically this setup will run locally but we will be storing gguf files, blob and manifest files from other drive saving space on root drive.

### What will be the folder structure on the external/other drive:

```bash
/run/media/username/your-drive-name/
├── local-models/
│   ├── gguf/        ← for your .gguf model files
│   └── ollama/
│       ├── bin/     ← Ollama binary
│       └── models/  ← blobs + manifests (OLLAMA_MODELS)
```

Also `~/modelfiles` is created for model definitions, in the home directory.

### Clone this repo using:

`git clone https://github.com/shery7310/Install-Ollama-Without-Root-And-Run-From_External-Drive.git`

Then open that directory in terminal and then make install.sh executable using:
`chmod +x install.sh`

Then run `./install.sh`
