# Malware Analysis

## Team
- Atharva Tikale (2022BCS-010)
- Utsav Jhanwar (2022BCS-069)
- Varun Kumar (2022BCS-071)

## Project Statement
This project aims to develop a malware sample from scratch, analyze it using various tools, and study the suspicious behavior it exhibits. The objective is to understand its execution and detection mechanisms.

## Software Used

### Oracle VirtualBox
- Used to create an isolated environment for running the malware safely.

## Step 1: Download & Install Oracle VirtualBox
1. Visit the [VirtualBox Downloads Page](https://www.virtualbox.org/wiki/Downloads).
2. Download the latest version of VirtualBox for your operating system (Windows, macOS, or Linux).
3. Run the installer and follow the on-screen instructions to complete the installation.
4. Once installed, launch VirtualBox.

## Step 2: Download Windows ISO File
1. Visit the official [Microsoft Windows Download Page](https://www.microsoft.com/en-us/software-download/windows10).
2. Select your preferred Windows version (Windows 10 or 11 recommended).
3. Choose the language and confirm.
4. Download the ISO file to your system.

## Step 3: Create a New Virtual Machine with Windows ISO
1. Open VirtualBox and click on "New".
2. Set the Name as Victim.
3. Choose Type: Microsoft Windows.
4. Select Version:
   - Windows 11 (64-bit) for Windows 11.
5. Click Next.

### Memory Allocation:
- Set RAM to at least 4GB (4096 MB), or **8GB (8192 MB) for better performance.
- Ensure it does not exceed 50% of your host system's RAM.

### Create a Virtual Hard Disk:
1. Select "Create a virtual hard disk now" and click Create.
2. Choose VDI (VirtualBox Disk Image) and click Next.
3. Select Dynamically Allocated.

### Set Storage Size:
- 50GB recommended (minimum 30GB).
- Click Create.

### Mount Windows ISO Before Booting:
1. In the Create Virtual Machine window, under ISO Image, click **Browse.
2. Select the downloaded Windows ISO file.
3. Click Create to finalize the VM setup.

## Step 4: Start the Virtual Machine and Install Windows
1. Click Start to boot the VM.
2. The Windows installation screen will appear.
3. Select Language, Time, and Keyboard Layout, then click **Next.
4. Click Install Now.
5. When prompted for a Product Key, click "I don't have a product key" to continue without activation.
6. Select the Windows version you want to install and click Next.
7. Accept the license agreement and click Next.
8. Choose Custom Installation.
9. Select the virtual hard disk and click Next.
10. Windows will begin installing. Wait for the process to complete.
11. Once installation is complete, follow the on-screen instructions to set up Windows.

## Sysinternals Suite

### Step 1: Install Sysinternals Suite (70 utilities)
1. Download Sysinternals Suite from [here](https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite).

   OR

- Download Process Monitor (If you only want Process Monitor tool, download from [here](https://learn.microsoft.com/en-us/sysinternals/downloads/procmon)).
- Download Process Explorer (If you only want Process Explorer tool, download from [here](https://learn.microsoft.com/en-us/sysinternals/downloads/process-explorer)).

2. Extract the downloaded ZIP file inside the VM.
3. We will use tools like Process Explorer and Process Monitor for system monitoring.

# Steps Followed To execute malicious program in VM

## Step 1: Install Python and Required Libraries
Before running the malware script, we need to install Python and the required dependencies.

### 1. Install Python
Download and install Python from the official website:  
🔗 [Python Download](https://www.python.org/downloads/)

Verify the installation by running the following command in Command Prompt (cmd):
cmd
python --version

### 2. Install Required Library (Pillow)
To use the ImageGrab function for image processing, install the Pillow library:
cmd
pip install pillow

---

## Step 2: Set Up the Project in Virtual Machine (VM)

### 1. Create Project Directory
Inside the VM, create a folder named *Project* in any location. For example, we create it inside the Downloads folder.
cmd
mkdir %USERPROFILE%\Downloads\Project

### 2. Place the Python Script
Move the malware.py script into the Project folder.

---

## Step 3: Convert Python Script to Executable (.exe)
To execute the malware without needing Python, we convert it into an .exe application.

### 1. Install PyInstaller
cmd
pip install pyinstaller

### 2. Convert the Python Script into an Executable
Navigate to the Project folder where malware.py is located and run:
cmd
cd %USERPROFILE%\Downloads\Project
pyinstaller --onefile --noconsole malware.py

### 3. Locate the Executable
After executing the command, PyInstaller generates multiple files:
- malware.spec
- build (folder)
- dist (folder) ← This contains our final executable

Navigate to the dist folder to find malware.exe:
cmd
cd dist

### 4. Move malware.exe to Temp Folder
Move the malware.exe file to the Temp folder for execution:
cmd
move malware.exe %TEMP%\malware.exe

---

## Step 4: Execution and Analysis

### 1. Open Analysis Tools
Before running the malware, launch the following tools for monitoring its behavior:
- Process Monitor
- Process Explorer

Both tools are part of the Sysinternals Suite and can be downloaded from:
🔗 [Sysinternals Suite](https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite)

Extract the tools inside the VM and launch:
cmd
start procmon.exe
start procexp.exe

### 2. Run the Malware
Now, execute the malware file inside the Temp folder:
cmd
start %TEMP%\malware.exe

### 3. Malware Behavior
- A Command Prompt will open for a second and then be hidden.
- A fake PDF will be opened inside the VM.

At this point, you can analyze the malware's behavior using Process Monitor and Process Explorer.

---

### 3. Execution and Persistence
Upon execution, the malware performs all the above actions and remains persistent even after a system reboot.

---

## Analysis Using Sysinternals Tools

## Step 5: Analysis Using Process Explorer

### 1. Check Malware Process in Process Tree
- Open Process Explorer.
- Locate malware.exe in the process tree.
- Observe that malware.exe is running under explorer.exe, which is responsible for running system processes from trusted locations. However, since our malware is running under explorer.exe, this can indicate suspicious activity.

### 2. Check Company Name, Description, and Verified Signature
- In Process Explorer, locate malware.exe.
- Check the Company Name, **Description, and **Verified Signature columns.
- If these fields are empty or missing, it suggests that the file may be a malicious sample.

### 3. View Detailed Process Information
- Double-click on malware.exe to open its properties.
- Navigate to the Image tab to see the file path and Autostart path (if present).
- Check if it is using the RUN key to persist in the system.

### 4. Access Process Properties for Further Analysis
To gather more details:
- Right-click on malware.exe.
- Select Properties.
- Navigate to the Stack tab to analyze all kernel processes related to the malware.

---

### Process Monitor
Applied various filters to detect suspicious activity:

#### 1. Process Execution Monitoring
- Filter: Process Name is malware.exe → Include

#### 2. Registry Modification (Persistence via Startup Keys)
- Filters:
  - Operation is RegSetValue → Include
  - Path contains Run → Include
  - Process Name is malware.exe → Include

#### 3. Security Evasion (Disabling Windows Defender)
- Filters:
  - Process Name is powershell.exe → Include
  - Operation is Process Create → Include

#### 4. Host File Modification (Blocking Security Updates)
- Filters:
  - Operation is WriteFile → Include
  - Path contains hosts → Include
  - Process Name is malware.exe → Include

#### 5. File Copying (Malware Replication)
- Filters:
  - Operation is CopyFile → Include
  - Process Name is malware.exe → Include

#### 6. Suspicious Thread Creation (Injection Attempts)
- Filters:
  - Operation is Thread Create → Include
  - Process Name is malware.exe → Include
  - Call Stack contains CreateRemoteThread → Include

#### 7. USB Spread (Creating an Autorun File)
- Filters:
  - Operation is WriteFile → Include
  - Path contains autorun.inf → Include

#### 8. Keylogging Detection (Credential Theft)
- Filters:
  - Operation is WriteFile → Include
  - Path contains log.txt → Include

#### 9. Screenshot Capture (Spying on User Activity)
- Filters:
  - Operation is WriteFile → Include
  - Path contains screenshot.png → Include

---

## Results

### Process Explorer Findings
- The malware process was detected as a child of explorer.exe.
- It lacked valid digital signatures, making it suspicious.
- The execution path was logged, helping in its identification.
- Multiple threads were created, revealing potential malicious behavior.

### Process Monitor Findings
- Successfully applied filters to track malicious activities.
- Detected file modifications, registry changes, and security evasion attempts.
- Identified keylogging, screenshot capture, and persistence mechanisms.

## Conclusion
This project successfully demonstrated the creation, execution, and detection of malware using Sysinternals Suite tools in a controlled VirtualBox environment. The study helped in understanding malware behavior and its impact on system security. -->





