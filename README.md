<h1 align="center">STM32 VS Code Setup</h1>

<p align="center">
  <img src="https://img.shields.io/badge/MCU-STM32G431CBT6-03234B?style=for-the-badge&logo=stmicroelectronics&logoColor=white" />
  <img src="https://img.shields.io/badge/Editor-VS%20Code-007ACC?style=for-the-badge&logo=visualstudiocode&logoColor=white" />
  <img src="https://img.shields.io/badge/Build-CMake-064F8C?style=for-the-badge&logo=cmake&logoColor=white" />
  <img src="https://img.shields.io/badge/Toolchain-ARM%20GNU-6C4C96?style=for-the-badge&logo=arm&logoColor=white" />
</p>

<p align="center">
  A step-by-step guide to setting up <b>Visual Studio Code</b> for <b>STM32 bare-metal development</b><br/>
  using <b>CMSIS</b>, <b>CMake</b>, and the <b>ARM GNU Toolchain</b>.
</p>

<p align="center">
  <a href="#step-1"><img src="https://img.shields.io/badge/1-Install%20Software-blue?style=flat-square" /></a>
  <a href="#step-5"><img src="https://img.shields.io/badge/2-Create%20Project-blue?style=flat-square" /></a>
  <a href="#step-11"><img src="https://img.shields.io/badge/3-Build%20%26%20Flash-blue?style=flat-square" /></a>
  <a href="#step-15"><img src="https://img.shields.io/badge/4-Debug-blue?style=flat-square" /></a>
</p>

---

## 📋 Table of Contents

- [Overview](#overview)
- [Step 1 — Install Required Software](#step-1)
- [Step 2 — Install and Configure VS Code](#step-2)
- [Step 3 — Add Tools to Environment Variables](#step-3)
- [Step 4 — Install VS Code Extensions](#step-4)
- [Step 5 — Create an STM32 Project](#step-5)
- [Step 6 — Build the Project](#step-6)
- [Step 7 — Check the Build Output](#step-7)
- [Step 8 — Add the SVD File](#step-8)
- [Step 9 — Add CMSIS Files](#step-9)
- [Step 10 — Link CMSIS with CMake](#step-10)
- [Step 11 — Configure Build & Flash](#step-11)
- [Step 12 — Configure launch.json](#step-12)
- [Step 13 — Add a Flash Button](#step-13)
- [Step 14 — Build and Flash](#step-14)
- [Step 15 — Start Debugging](#step-15)
- [Step 16 — Reconfigure After Moving a Project](#step-16)
- [Step 17 — Avoid Spaces in Paths](#step-17)
- [Step 18 — Changing the Project Name](#step-18)
- [Preferred Project Structure](#preferred-structure)
- [Final Workflow](#final-workflow)

---

<a id="overview"></a>
## Overview

This setup enables STM32 development using:

| Tool | Purpose |
|---|---|
| Visual Studio Code | Editor / IDE |
| STM32CubeIDE for VS Code (extension) | Project creation & STM32Cube integration |
| CMSIS | MCU core & device headers |
| CMake | Build system |
| ARM GNU Toolchain | Compiler / linker |
| Cortex-Debug | Debugging front-end |
| ST-LINK | Programmer / debug probe |
| STM32CubeProgrammer | Flashing STM32 devices |

The approach here is **bare-metal / CMSIS-based programming** — STM32 HAL is *not* used in the application code.

---

<a id="step-1"></a>
## 🧩 Step 1 — Install Required Software

<p>
  <a href="https://www.st.com/en/development-tools/stm32cubemx.html"><img src="https://img.shields.io/badge/Download-STM32CubeMX-03234B?style=for-the-badge&logo=stmicroelectronics&logoColor=white" /></a>
  <a href="https://www.st.com/en/development-tools/stm32cubeide.html"><img src="https://img.shields.io/badge/Download-STM32CubeIDE-03234B?style=for-the-badge&logo=stmicroelectronics&logoColor=white" /></a>
</p>
<p>
  <a href="https://www.st.com/en/development-tools/stm32cubeprog.html"><img src="https://img.shields.io/badge/Download-STM32CubeProgrammer-03234B?style=for-the-badge&logo=stmicroelectronics&logoColor=white" /></a>
  <a href="https://www.voidtools.com/downloads/"><img src="https://img.shields.io/badge/Download-Everything-1E90FF?style=for-the-badge&logo=windows&logoColor=white" /></a>
</p>

| # | Software | Purpose |
|---|---|---|
| 1 | **STM32CubeMX** | Device info, pin/peripheral config reference, software packages |
| 2 | **STM32CubeIDE** | Required — the VS Code extension depends on STM32Cube components |
| 3 | **STM32CubeProgrammer** | Programming & debugging STM32 devices |
| 4 | **Everything** | Fast search for executables, CMSIS files, SVD files, etc. |

---

<a id="step-2"></a>
## 💻 Step 2 — Install and Configure VS Code

<p>
  <a href="https://code.visualstudio.com/download"><img src="https://img.shields.io/badge/Download-VS%20Code-007ACC?style=for-the-badge&logo=visualstudiocode&logoColor=white" /></a>
</p>

1. Download and install VS Code if not already installed.
2. Create a **dedicated VS Code profile** for STM32 work, e.g. `STM32`.

This keeps STM32-related extensions and settings separated from other development environments.

---

<a id="step-3"></a>
## 🛠 Step 3 — Add Required Tools to Environment Variables

Add the folders containing these executables to the **Windows PATH**:

- `STM32_Programmer_CLI.exe`
- `arm-none-eabi-gdb.exe`
- `ST-LINK_gdbserver.exe`

**Finding the executables:** use *Everything* to locate each `.exe`.

> ⚠️ **Important:** Add the **folder** containing the executable, not the `.exe` itself.
>
> ✅ `C:\...\STM32CubeProgrammer\bin`
> ❌ `C:\...\STM32CubeProgrammer\bin\STM32_Programmer_CLI.exe`

### Adding to PATH

1. Press <kbd>Win</kbd> → search **Environment Variables**
2. Open **Edit the system environment variables**
3. Click **Environment Variables...**
4. Under *User variables* or *System variables*, select **Path**
5. **Edit → New** → add the required folder paths
6. Click **OK** on all dialogs

### Verify

Open a **new** VS Code terminal (required — old terminals won't have the updated PATH) and run:

```bash
STM32_Programmer_CLI.exe
arm-none-eabi-gdb.exe --version
ST-LINK_gdbserver.exe
```

---

<a id="step-4"></a>
## 🧱 Step 4 — Install Required VS Code Extensions

<p>
  <a href="https://marketplace.visualstudio.com/items?itemName=stmicroelectronics.stm32-vscode-extension"><img src="https://img.shields.io/badge/Install-STM32CubeIDE%20Extension-C160EF?style=for-the-badge&logo=visualstudiocode&logoColor=white" /></a>
</p>
<p>
  <a href="https://marketplace.visualstudio.com/items?itemName=spencerwmiles.vscode-task-buttons"><img src="https://img.shields.io/badge/Install-Task%20Buttons-C160EF?style=for-the-badge&logo=visualstudiocode&logoColor=white" /></a>
  <a href="https://marketplace.visualstudio.com/items?itemName=marus25.cortex-debug"><img src="https://img.shields.io/badge/Install-Cortex--Debug-C160EF?style=for-the-badge&logo=visualstudiocode&logoColor=white" /></a>
</p>

| Extension | Publisher | Purpose |
|---|---|---|
| **STM32CubeIDE for VS Code** | STMicroelectronics | STM32 project creation & tool integration |
| **Task Buttons** | spencerwmiles | Custom task buttons (e.g. a Flash button) |
| **Cortex-Debug** | marus25 | ARM Cortex-M debugging via ST-LINK/SWD |

> Installing the STM32 extension may take time and requires an internet connection.

---

<a id="step-5"></a>
## 🚀 Step 5 — Create an STM32 Project

1. Open VS Code → click the **STM32** icon in the Activity Bar.
2. Select **Create Empty Project**.
3. Enter the project name.
4. Go to **Hardware → Device** and select your MCU (e.g. `STM32G431CBT6`).
5. Choose the project folder → **Create Project**.
6. Select **Open in This Window**.
7. If prompted, click **Yes, I trust the authors**.

---

<a id="step-6"></a>
## 🔨 Step 6 — Build the Project

Open the project → click **Build** in the bottom-left CMake area → wait for it to finish.

A successful build confirms the basic setup is working.

---

<a id="step-7"></a>
## 📦 Step 7 — Check the Build Output

Open `build/Debug/` and check the generated output file (e.g. `MyProject.elf` or `MyProject.hex`).

> The `.elf` file is normally used for debugging. Always confirm the actual filename before configuring the flash task.

---

<a id="step-8"></a>
## 🔍 Step 8 — Add the STM32 SVD File

**SVD** = *System View Description* — gives the debugger info about the MCU's peripherals/registers.

1. Find the matching SVD file (e.g. `STM32G431.svd`) using *Everything*.
2. Copy it into the **project root**:

```text
MyProject/
├── STM32G431.svd
├── CMakeLists.txt
└── ...
```

> The filename in `launch.json` **must match** the actual SVD filename.

---

<a id="step-9"></a>
## 📚 Step 9 — Add Required CMSIS Files

Create a `CMSIS/` folder in the project root. For **STM32G431CBT6**:

```text
CMSIS/
├── m-profile/
│   ├── armv7m_mpu.h
│   └── cmsis_gcc_m.h
│
├── cmsis_compiler.h
├── cmsis_gcc.h
├── cmsis_version.h
├── core_cm4.h
├── stm32g4xx.h
├── stm32g431xx.h
└── system_stm32g4xx.h

Src/
└── system_stm32g4xx.c
```

<p>
  <a href="https://github.com/STMicroelectronics/STM32CubeG4"><img src="https://img.shields.io/badge/GitHub-STM32CubeG4-181717?style=for-the-badge&logo=github&logoColor=white" /></a>
  <a href="https://github.com/STMicroelectronics/cmsis-device-g4"><img src="https://img.shields.io/badge/GitHub-cmsis--device--g4-181717?style=for-the-badge&logo=github&logoColor=white" /></a>
</p>

> The exact files depend on your STM32 family/MCU — swap in the corresponding headers for another family.

---

<a id="step-10"></a>
## 🔗 Step 10 — Link CMSIS with CMake

Add the CMSIS include path in `CMakeLists.txt`:

```cmake
target_include_directories(${PROJECT_NAME} PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/CMSIS
)
```

> The exact configuration may vary depending on how the extension generated your project.

---

<a id="step-11"></a>
## ⚙️ Step 11 — Configure VS Code Build and Flash

Ensure `.vscode/` contains `tasks.json` and `launch.json`.

**`.vscode/tasks.json`:**

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Flash Program",
            "type": "shell",
            "command": "STM32_Programmer_CLI",
            "args": [
                "-c",
                "port=SWD",
                "-w",
                "${workspaceFolder}/build/${command:cmake.buildType}/${workspaceFolderBasename}.elf",
                "-rst"
            ]
        }
    ]
}
```

> ⚠️ Make sure this path matches your actual generated output. If your project produces a `.hex` file instead, change `.elf` → `.hex`, and verify `${workspaceFolderBasename}` matches your project name.

---

<a id="step-12"></a>
## 🐞 Step 12 — Configure launch.json

**`.vscode/launch.json`:**

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Debug STM32G431",
            "type": "cortex-debug",
            "request": "launch",

            "servertype": "stlink",
            "device": "STM32G431CB",
            "interface": "swd",

            "cwd": "${workspaceFolder}",
            "executable": "${workspaceFolder}/build/${command:cmake.buildType}/${workspaceFolderBasename}.elf",

            "runToEntryPoint": "main",

            "svdFile": "${workspaceFolder}/STM32G431.svd"
        }
    ]
}
```

| Field | Meaning |
|---|---|
| `servertype` | Uses ST-LINK as the debug server |
| `device` | Target MCU |
| `interface` | Uses SWD for programming/debugging |
| `executable` | Path to the generated ELF file |
| `runToEntryPoint` | Stops execution at `main()` |
| `svdFile` | Peripheral/register info for the debugger |

---

<a id="step-13"></a>
## ⚡ Step 13 — Add a Flash Button

In `.vscode/settings.json`, add:

```json
"VsCodeTaskButtons.showCounter": false,
"VsCodeTaskButtons.tasks": [
    {
        "label": "$(arrow-circle-right) Flash",
        "alignment": "left",
        "task": "Flash Program",
        "tooltip": "Flash the program to the connected STM32 device"
    }
]
```

> If `settings.json` already has other entries above this block, add a comma before it. The `"task"` value must match the `"label"` used in `tasks.json`.

This adds a **Flash** button to the VS Code UI:

<p>
  <img src="https://img.shields.io/badge/▶-Flash-2ea44f?style=for-the-badge" />
</p>

---

<a id="step-14"></a>
## 🔥 Step 14 — Build and Flash

1. Connect the STM32 board via an **ST-LINK** debugger over **SWD**.
2. Click **Build** → verify the output file exists in `build/Debug/`.
3. Click the **Flash** button — this runs `STM32_Programmer_CLI` and programs the MCU over SWD.

---

<a id="step-15"></a>
## 🎯 Step 15 — Start Debugging

1. Press <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>D</kbd> to open **Run and Debug**.
2. Select **Debug STM32G431**.
3. Start the debugger.

```text
Cortex-Debug → ST-LINK → SWD → STM32G431
```

The SVD file lets the debugger display MCU peripherals and registers live.

---

<a id="step-16"></a>
## 📂 Step 16 — Reconfigure CMake After Moving the Project

If a project is moved to a new folder, CMake's cache may still hold the **old path**.

**Fix:**

1. Open the CMake panel in VS Code.
2. Click **Delete Cache and Reconfigure**.
3. **Build** again.

---

<a id="step-17"></a>
## 🚫 Step 17 — Avoid Spaces in Project Paths

| ❌ Avoid | ✅ Prefer |
|---|---|
| `C:\Users\YourName\My STM32 Projects\G431 Project` | `C:\Projects\STM32\G431_Project` |

Spaces in the path can cause issues with CMake, toolchain commands, the debugger, flashing tools, and build scripts.

---

<a id="step-18"></a>
## ✏️ Step 18 — Changing the Project Name

Renaming the root folder alone is **not enough** — update `CMakeLists.txt` too:

```cmake
# Before
project(test)
set(PROJECT_NAME test)

# After
project(MyProject)
set(PROJECT_NAME MyProject)
```

**Then:**

1. Save `CMakeLists.txt`
2. Rename the project folder (`test` → `MyProject`)
3. Open the renamed folder in VS Code
4. Open the CMake panel → **Delete Cache and Reconfigure**
5. **Build** again → check `build/Debug/`

> Keep the project name and root folder name consistent.

---

<a id="preferred-structure"></a>
## 🗂 Preferred Project Structure

For larger STM32 bare-metal projects, a modular structure is recommended:

<p align="center">
  <a href="https://github.com/shohanur00/G4_Core_Lib"><img src="https://img.shields.io/badge/Reference%20Project-G4__Core__Lib-181717?style=for-the-badge&logo=github&logoColor=white" /></a>
</p>

```text
MyProject/
│
├── .settings/
├── .vscode/
│
├── App/
├── BSP/
├── CMSIS/
├── Config/
├── Drivers/
│
├── Services/
│   └── Debug/
│
├── Src/
├── cmake/
│
├── .clangd
├── .gitignore
├── CMakeLists.txt
├── CMakePresets.json
│
├── STM32G431.svd
├── project-description.json
└── stm32g431xb_flash.ld
```

| Folder | Responsibility |
|---|---|
| `App/` | Application-level logic |
| `BSP/` | Board-specific hardware configuration |
| `CMSIS/` | CMSIS Core and STM32 device headers |
| `Config/` | Project and module configuration |
| `Drivers/` | Hardware/peripheral drivers |
| `Services/` | Reusable higher-level services |
| `Src/` | Main application/source files |
| `cmake/` | CMake configuration and modules |
| `.vscode/` | Build, flash, and debug configuration |

> `.settings/` and other project metadata can be kept or dropped depending on your environment.

---

<a id="final-workflow"></a>
## 🔁 Final Workflow

```text
VS Code
   │
   ├── CMake
   │     └── Build
   │
   ├── Flash Button
   │     └── STM32_Programmer_CLI
   │             └── SWD
   │
   └── Cortex-Debug
         └── ST-LINK
               └── SWD
                     └── STM32
```

This results in a clean, repeatable workflow for **STM32 bare-metal development** using VS Code, CMSIS, CMake, the ARM GNU Toolchain, ST-LINK, and Cortex-Debug.

---

<p align="center">
  Made with 🔧 for embedded developers &nbsp;|&nbsp;
  <a href="https://github.com/shohanur00/G4_Core_Lib">Reference: G4_Core_Lib</a>
</p>
