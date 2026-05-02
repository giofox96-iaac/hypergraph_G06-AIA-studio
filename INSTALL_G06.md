### 📝 INSTALL_G06.md (Draft for Validation)

#### Step 1: Python Environment Setup
We use Conda to ensure a clean, isolated environment to avoid conflicts with other MACAD projects.

**1. Open VS Code Terminal (`Ctrl + ò`) and navigate to the project folder:**
```bash
cd "C:\Users\giofo\Desktop\ARCHIVIO\MASTERS\MACAD\2_MACAD\0_MACAD DRIVE\AIA\STUDIO\hypergraph_G06-AIA-studio"
```

**2. Create and activate the Conda environment:**
```bash
# Create environment forcing Python 3.9 (required for compatibility)
conda create -n aia_hypergraph python=3.9 -y

# Activate the environment
conda activate aia_hypergraph
```

#### ⚠️ Troubleshooting: "conda is not recognized as an internal or external command"
If you have Anaconda/Miniconda installed but VS Code's PowerShell terminal doesn't recognize the `conda` command, or if it throws a red error when trying to activate the environment, you need to initialize Conda for PowerShell and allow script execution.

**Fix Step 1: Initialize Conda**
1. Close VS Code.
2. Open the Windows Start Menu, search for **"Anaconda Prompt"** (do NOT use the standard cmd or PowerShell yet), and open it.
3. Run this command to configure PowerShell:
```bash
# Initialize Conda for Windows PowerShell
conda init powershell
```
4. Close the Anaconda Prompt.

**Fix Step 2: Allow Script Execution (Windows Only)**
Windows often blocks the `activate.ps1` script required to switch Conda environments.
1. Open Windows **PowerShell** as **Administrator** (Right-click > Run as Administrator).
2. Run this command and press `Y` when prompted:
```powershell
# Change the execution policy to allow local scripts to run
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

**Fix Step 3: Restart and Verify**
1. Re-open **VS Code** and open a new terminal (`Ctrl + ò`).
2. You should now see `(base)` appearing on the left side of your terminal prompt. This means Conda is successfully hooked to VS Code! You can now proceed with Step 1 of the installation.

---

> **✅ Validation Check 1:** Run `python --version` in the terminal. It MUST return `Python 3.9.x`. If it returns a different version, the environment is not active.

#### Step 2: Installing Dependencies
Now we install the required packages. This is where your colleague might have faced issues (e.g., missing C++ build tools for some geometric libraries).

```bash
# Install base and developer requirements
pip install -r requirements.txt -r requirements-dev.txt
```
> **✅ Validation Check 2:** Let's test if the core libraries installed correctly. Run this in your terminal:
> ```bash
> python -c "import networkx; import uvicorn; print('Dependencies installed successfully!')"
> ```
> If it prints the message without errors, you are good to go.

#### Step 3: The C# DLLs Configuration (CRITICAL)
Rhino and Grasshopper require these compiled libraries to run the hypergraph algorithms. Windows blocks downloaded `.dll` files by default, which causes Grasshopper components to turn red and fail.

**1. Create the target folder:**
Ensure the folder `C:\geolib` exists on your C: drive.

**2. Copy and Unblock:**
Instead of unblocking them one by one manually (which is prone to human error), use this PowerShell command inside VS Code to copy and unblock everything automatically:

```powershell
# Create the directory if it doesn't exist
New-Item -ItemType Directory -Force -Path "C:\geolib"

# Copy all DLLs from the repository to C:\geolib
Copy-Item -Path ".\samples\_requiredDLLs\*" -Destination "C:\geolib" -Recurse

# Unblock all copied DLLs (Crucial step for Rhino/Grasshopper)
Get-ChildItem -Path "C:\geolib" -Recurse | Unblock-File
```
> **✅ Validation Check 3:** Go to `C:\geolib`, right-click on one of the `.dll` files, select "Properties". If you **do not** see the "Unblock" (Annulla blocco) checkbox at the bottom, it means the script worked perfectly and the files are safe to use.

#### Step 4: Rhino/Grasshopper Validation
Now we verify if the CAD software correctly reads our setup.

1. Open **Rhino 7**.
2. Open the file: `samples\Hypergraph Reference Script 1 Transfer Layout via Hypergraph.3dm`
3. Launch **Grasshopper** and open the corresponding `.gh` file.
> **✅ Validation Check 4:** Look at the Grasshopper canvas. Are there any **Red components** (especially the C# script nodes)? 
> - If **NO**: The installation is 100% successful! 🎉
> - If **YES**: The C# component failed to load the DLLs. Double-check that the files are actually in `C:\geolib` and properly unblocked.