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

### Install Ollama using this script:

```bash
#!/bin/bash

OLLAMA_URL="https://ollama.com/download/ollama-linux-amd64.tgz"

echo "🔌 Enter the path to your mounted external drive (e.g., /run/media/username/your-drive-name):"

read -rp "DRIVE_PATH: " DRIVE_PATH

echo

echo "Please make sure the following structure exists inside $DRIVE_PATH:"

echo "$DRIVE_PATH/local-models/"

echo "├── gguf/ ← for your .gguf model files"

echo "└── ollama/"

echo " ├── bin/ ← Ollama binary will be placed here"

echo " └── models/ ← For blobs and manifests"

echo

  

read -rp "Have you created this folder structure? (y/n): " confirm

if [[ "$confirm" != [Yy] ]]; then

echo "Please create the folder structure before proceeding. Exiting..."

exit 1

fi

  

OLLAMA_DIR="$DRIVE_PATH/local-models/ollama/bin"

OLLAMA_MODELS_DIR="$DRIVE_PATH/local-models/ollama/models"

GGUF_MODELS_DIR="$DRIVE_PATH/local-models/gguf"

OLLAMA_BIN="$OLLAMA_DIR/ollama"

  

# === Download and Extract Ollama Binary ===

mkdir -p "$OLLAMA_DIR"

cd "$OLLAMA_DIR" || { echo "Failed to enter $OLLAMA_DIR"; exit 1; }

  

if [ ! -f "$OLLAMA_BIN" ]; then

echo "[+] Downloading Ollama binary from $OLLAMA_URL..."

curl -L "$OLLAMA_URL" -o ollama.tgz

echo "[+] Extracting archive..."

tar -xzf ollama.tgz

  

# Detect and rename binary

if [ -f ollama-linux-amd64 ]; then

mv ollama-linux-amd64 ollama

chmod +x ollama

echo "Ollama binary installed at $OLLAMA_BIN"

else

echo "Extracted binary not found! Expected: ollama-linux-amd64"

exit 1

fi

else

echo "Ollama binary already exists at $OLLAMA_BIN"

fi

  

# === Set Environment Variable for Ollama Cache ===

export OLLAMA_MODELS="$OLLAMA_MODELS_DIR"

if ! grep -q "OLLAMA_MODELS=" ~/.bashrc; then

echo "export OLLAMA_MODELS=\"$OLLAMA_MODELS_DIR\"" >> ~/.bashrc

echo "[+] OLLAMA_MODELS exported and saved to ~/.bashrc"

fi

echo "OLLAMA_MODELS is set to: $OLLAMA_MODELS_DIR"

  

# === Start Ollama Server ===

echo "Starting Ollama server in background..."

"$OLLAMA_BIN" serve &

  
  

echo

echo "=============================="

echo "Ollama Setup Complete"

echo "=============================="

echo "Binary: $OLLAMA_BIN"

echo "Model Cache: $OLLAMA_MODELS_DIR"

echo "GGUF Models: $GGUF_MODELS_DIR"

echo "Modelfiles: \$HOME/modelfiles (create this if you haven't already)"

echo

echo "To use a GGUF model:"

echo " cd \$HOME/modelfiles/your-model"

echo " C#!/bin/bash

  

OLLAMA_URL="https://ollama.com/download/ollama-linux-amd64.tgz"

  

# Different distros might mount drive to different paths

echo "🔌 Enter the full path to your external drive mount point"

echo " (e.g., /run/media/username/your-drive-name)"

read -rp "DRIVE_PATH: " DRIVE_PATH

  
  

if [ ! -d "$DRIVE_PATH" ]; then

echo "Error: Path '$DRIVE_PATH' does not exist. Please mount the drive and try again."

exit 1

fi

  

LOCAL_MODELS_ROOT="$DRIVE_PATH/local-models"

GGUF_MODELS_DIR="$LOCAL_MODELS_ROOT/gguf"

OLLAMA_ROOT="$LOCAL_MODELS_ROOT/ollama"

  
  

OLLAMA_BIN_DIR="$OLLAMA_ROOT/bin"

OLLAMA_MODELS_DIR="$OLLAMA_ROOT/models"

OLLAMA_BIN="$OLLAMA_BIN_DIR/ollama"

  

echo "Creating required directories under $DRIVE_PATH..."

mkdir -p "$GGUF_MODELS_DIR" && echo "[+] GGUF folder: $GGUF_MODELS_DIR"

mkdir -p "$OLLAMA_BIN_DIR" && echo "[+] Binary folder: $OLLAMA_BIN_DIR"

mkdir -p "$OLLAMA_MODELS_DIR" && echo "[+] Model blob/manifest folder: $OLLAMA_MODELS_DIR"

mkdir -p "$HOME/modelfiles" && echo "[+] Modelfiles folder: $HOME/modelfiles"

  

# === Step 3: Ask user to place GGUF models ===

echo

echo "Please move your .gguf model files into:"

echo " $GGUF_MODELS_DIR"

echo "You can do this manually or with:"

echo " mv yourmodel.gguf \"$GGUF_MODELS_DIR\""

echo

  

# === Step 4: Download and install Ollama binary ===

cd "$OLLAMA_BIN_DIR" || { echo "Failed to enter $OLLAMA_BIN_DIR"; exit 1; }

  

if [ ! -f "$OLLAMA_BIN" ]; then

echo "[+] Downloading Ollama binary from $OLLAMA_URL..."

curl -L "$OLLAMA_URL" -o ollama.tgz

  

echo "[+] Extracting..."

tar -xzf ollama.tgz

  

if [ -f ollama-linux-amd64 ]; then

mv ollama-linux-amd64 ollama

chmod +x ollama

echo "Ollama binary installed at $OLLAMA_BIN"

else

echo "Error: Expected binary 'ollama-linux-amd64' not found."

exit 1

fi

else

echo "[✓] Ollama binary already exists at $OLLAMA_BIN"

fi

  

# === Step 5: Set environment variable ===

export OLLAMA_MODELS="$OLLAMA_MODELS_DIR"

if ! grep -q "OLLAMA_MODELS=" ~/.bashrc; then

echo "export OLLAMA_MODELS=\"$OLLAMA_MODELS_DIR\"" >> ~/.bashrc

echo "[+] OLLAMA_MODELS added to ~/.bashrc"

fi

echo "OLLAMA_MODELS is now set to: $OLLAMA_MODELS_DIR"

  

# === Step 6: Start Ollama Server ===

echo "Starting Ollama server in background..."

"$OLLAMA_BIN" serve &

  

# === Step 7: Final Instructions ===

echo

echo "=============================="

echo "Ollama Setup Complete"

echo "=============================="

echo "Your directory structure:"

echo " - GGUF Models: $GGUF_MODELS_DIR"

echo " - Ollama Bin: $OLLAMA_BIN"

echo " - Ollama Models: $OLLAMA_MODELS_DIR"

echo " - Modelfiles: $HOME/modelfiles"

echo

echo "To load a local GGUF model:"

echo " cd \$HOME/modelfiles/my-model"

echo " Create a Modelfile like:"

echo " FROM $GGUF_MODELS_DIR/YourModel.gguf"

echo " Then run:"

echo " $OLLAMA_BIN create mymodel -f Modelfile"

echo " $OLLAMA_BIN run mymodel"

echo

echo "You can edit ~/.bashrc to make OLLAMA_MODELS permanent"

$OLLAMA_BIN run mymodel"
```

Save this as install.sh and then: `chmod +x install.sh`

And then run Using Terminal
