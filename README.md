# Running GGUF Models locally and without Root Privileges (Linux)

This script lets you run Ollama from an external or secondary drive without needing root access. It builds on the approach shared in this  <a href="https://github.com/zherenz/Ollama-installation-without-root-privilege/tree/main?tab=readme-ov-file">repository</a> by [zherenz](https://github.com/zherenz) and offers a more flexible, user-controlled setup. In contrast, the official script at ollama.com/install.sh installs Ollama system-wide, requires sudo or root privileges, creates a dedicated system user (ollama), and sets up a systemd service—all within root-owned directories.

### Why run Ollama from other drive?

Running Ollama from another drive allows you to save space on your main system drive, avoid needing root access, and keep your models and binaries in a portable or user-controlled location. It's especially useful on shared systems, external storage setups, or when managing large model files. 

Basically this setup will run locally but we will be storing gguf files, blob and manifest files from other drive saving space on root drive.

### What will be the folder structure on the external/other drive:

<pre>
/run/media/username/your-drive-name/
├── local-models/
│   ├── gguf/        ← for your .gguf model files
│   └── ollama/
│       ├── bin/     ← Ollama binary
│       └── models/  ← blobs + manifests (OLLAMA_MODELS)
</pre>

Also `~/modelfiles` is created for model definitions, in the home directory.

### Clone this repo using:

<pre>git clone https://github.com/shery7310/Install-Ollama-Without-Root-And-Run-From_External-Drive.git</pre>

Then open that directory in terminal and then make install.sh executable using:

<pre>chmod +x install.sh</pre>

Then run <pre>./install.sh</pre>

### Running Ollama Without Root Access

Make sure the drive is mounted and then run:

<pre>/mounted-drive/local-models/ollama/bin/bin/ollama serve &</pre>

Replace the path with your username and the name of the drive where you created the directory structure

### Make sure to move any GGUF model to this directory:

<pre>/run/media/your-username/your-drive-name/local-models/gguf</pre>

This can be different for different linux distributions

### Then create a model file to create and run blob and manifest files for the Model:

<pre>mkdir -p ~/modelfiles/mymodel</pre>

Here `mymodel` is the name of the model we are going to run for example:

DeepSeek-Coder-V2-Lite-Base.Q8_0.gguf

Then we will run:

<pre>mkdir -p ~/modelfiles/DeepSeek-Coder</pre>

You can give Modelfile any name you like

Then cd into the directory we created using:

<pre>cd DeepSeek-Coder</pre>

Then run:

<pre>nano Modelfile</pre>

and write this to the nano file:

<pre>FROM /run/media/your-username/your-drive-name/local-models/gguf/DeepSeek-Coder-V2-Lite-Base.Q8_0.gguf</pre>

Then press ctrl + x and then type y and press Enter, changes to the nano file will be saved

Ensure that you use correct paths.

Then register your model using:

<pre>/path/to/ollama create DeepSeek-Coder-V2-Lite-Base.Q8_0 -f Modelfile</pre>

Again make sure to use correct path

### Running The Model:

Then from within the same directory run:

<pre>path/to/ollama/local-models/ollama/bin/bin/ollama create DeepSeek-Coder -f Modelfile</pre>

This uses the Modelfile to create required files to run the model and this might take some Time.

For example in my case I ran:

`/run/media/cdev/data-storage/local-models/ollama/bin/bin/ollama create llama3.2 -f Modelfile`

Then run the model using:

<pre>/path/to/ollama/local-models/ollama/bin/bin/ollama run DeepSeek-Coder</pre>

### Stopping Ollama Service:

Just type:

<pre>ps aux | grep ollama</pre>

This will list two ollama related processes, for example:

<pre>
❯ ps aux | grep ollama
cdev       35097  0.0  0.7 7009596 113248 pts/3  Sl   19:34   0:04 /run/media/cdev/data-storage/local-models/ollama/bin/bin/ollama serve
cdev       37664  0.0  0.0   6720  4152 pts/3    S+   23:46   0:00 grep --color=auto ollama</pre>


Just see the process number in grep column and type:

<pre>kill process number</pre>

i.e. 

kill 35097
kill 37664
