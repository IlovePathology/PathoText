# PathoText
Lightweight Tool for Efficient Structured Reporting in Surgical Pathology.

PathoText is a keyboard-driven text module and structured reporting tool for surgical pathology. It was developed to simplify repetitive reporting tasks, speed up access to diagnostic text modules, and provide a consistent structure for pathology reports.

The program is intended to complement existing Laboratory Information Systems (LIS) workflows rather than replace them.

## Key Features

- Keyboard-based navigation using configurable shortcuts
- Fuzzy search for medical terms and abbreviations
- Grouping of specimen fractions and report components
- Dynamic text fields with conditional logic
- Optional local AI support for text correction and variable extraction
- Direct access to linked PDF references and guidelines
- CSV import and export of diagnostic modules

## Installation & Usage

PathoText is designed as a **portable application** and does not require a traditional installation or Windows Registry modifications.

### Directory Structure

The directory layout is essential for proper operation, particularly for loading the `sqlite3.dll`, language files, and the optional local AI components.

#### A) Compiled Mode (Recommended for End Users)

In the compiled version, only `PathoText.exe` needs to be launched. All required dependencies must reside in the expected directory structure:

```text
PathoText/
├── PathoText.exe
├── lib/
│   ├── sqlite3.dll         # Required for SQLite database access
│   ├── engine/             # Optional: llama.cpp runtime for local AI
│   │   ├── llama-completion.exe   # Used by AI mode "Use local AI"
│   │   ├── llama-server.exe       # Used by AI mode "Host a local server"
│   │   └── ...                    # Supporting DLLs shipped with llama.cpp
│   └── models/             # Optional: .gguf language models
│       └── models.json     # Model catalog for the built-in AI Manager
├── lang/          
│   └── xx.lang             # Language files
├── <your-database>.db      # SQLite database (name/location set on first launch)
├── backup/                 # Automatic database backups
└── pdf/                    # Linked guideline and reference PDFs
```

#### B) Source Mode (For Developers & Customization)

In source mode, the script is executed directly using the **AutoHotkey v2** interpreter. Ensure that AutoHotkey v2 is installed before launching the application.

```text
PathoText/
├── PathoText.ahk           # Application entry point
├── lib/
│   ├── SQLight.ahk         # AutoHotkey SQLite wrapper
│   └── sqlite3.dll
├── ...                     # Remaining structure identical to compiled mode
```

> **Note:** The `lib/engine/` and `lib/models/` folders are entirely optional. Without them, PathoText runs as a pure text-module/reporting tool; the AI features simply stay unavailable until a model is installed via the AI Manager (see below).

---

## First Launch & Initialization

When PathoText is started for the first time, it automatically detects that no `settings.ini` exists and launches the initial configuration wizard.

### Initial Setup

1. **Launch the application**
   - Run `PathoText.exe` (or `PathoText.ahk` in source mode).

2. **Configuration Wizard**
   - A guided setup dialog will prompt you to configure the core application paths and defaults (interface language, global hotkey, section labels, database location, PDF directory, and your signature).

3. **Database Location**
   - Select the location of your SQLite database file (created automatically if it doesn't exist yet).
   - The database may reside on a local drive or a shared network location.

4. **PDF Directory**
   - Specify the folder containing diagnostic guidelines, WHO classifications, or other reference PDFs.
   - These documents can then be opened directly from within PathoText.

5. **Signature**
   - Enter your name or initials.
   - This signature can be appended automatically to generated reports.

6. **Configuration File**
   - After completing the wizard, PathoText creates a `settings.ini` file in the application directory.
   - This file serves as the **single source of truth** for all application settings.

No pre-existing `settings.ini`, `error.log`, or database is required to roll out PathoText on a new machine — all three are created from scratch on first launch, and the internal numbering scheme for departments/organs/modules is collision-free by construction from an empty database.

---

## Configuration via `settings.ini`

The `settings.ini` file controls nearly every aspect of PathoText's behavior.

### Paths

- Database and PDF locations can be changed at any time.
- Updates take effect automatically the next time the application starts.

### Language

- Under `[Settings] -> Language`, you can switch between any available `.lang` files (for example, `en`, `de`, `es`, `fr`, `it`, or `pl`).
- Languages can be added individually at any time. Existing languages can be used as templates for this purpose. The script then recognizes them automatically.

### Default Report Sections

- Settings in the `[Checkboxes]` section define which report components (e.g., **Microscopy**, **Immunohistochemistry**, **Clinical Information**) are enabled by default when the application starts.

### Global Hotkeys

- The Master Toggle via Ctrl + Alt + F5 (`hotkeyEnabled`) option enables or disables PathoText's global keyboard shortcuts.
- This is useful for preventing conflicts with other Laboratory Information Systems (LIS) or specialized medical software.

### Advanced AI Settings

- A collapsible **"Advanced AI Features"** section lets you choose between three AI modes — running the model locally, connecting to a remote `llama-server`, or hosting a local server yourself for other workstations on the network — pick which downloaded model is active, save individual prompt templates, and decide whether multiple report sections are reviewed one at a time or together in a single comparison window.
- All of the above is written to and read back from `settings.ini` immediately as it changes, so it survives restarts without any extra "Save" step.

---

## ⌨️ Keyboard Shortcuts & Navigation

PathoText is built for absolute keyboard efficiency. The application relies on global and context-specific hotkeys to ensure hands never have to leave the keyboard during microscopic evaluation.

### Global & Launch
| Shortcut | Action |
| :--- | :--- |
| `Ctrl + Alt + F5` | **Master Toggle:** First run initializes the setup. Subsequently toggles all application hotkeys on/off. |
| `F5` | **Launch/Cycle:** Opens the GUI if closed. If open, acts as the shortcut for the 4th configured department. |

### Navigation & Search
| Shortcut | Action |
| :--- | :--- |
| `[Text] + Space` | **Fuzzy Search:** Type in the main field to instantly filter modules using the custom Levenshtein algorithm. |
| `Tab` / `Shift + Tab` | **Focus Toggle:** Switch focus directly between the search field and the results list. |

### Department & Organ Selection
| Shortcut | Action |
| :--- | :--- |
| `F1` | **Reset Filter:** Shows all departments and organs. |
| `F2` - `F9` | **Select Department:** Rapidly select departments 1 through 8. |
| `F2` - `F9` *(Repeated)* | **Cycle Organs:** Pressing the same F-key again cycles through the organs within that department. |
| `F10` | **Select 9th Department:** Assigned to the 9th department if configured. |

### Specimen Assembly (Fractions)
| Shortcut | Action |
| :--- | :--- |
| `Enter` | **Add New Specimen:** Adds the selected module as a *new* specimen fraction (increments grouping number). |
| `Alt + Enter` | **Combine Specimen:** Appends the selected module to the *currently active* specimen fraction (shares grouping number). |
| `Double Click` | **Remove Specimen:** Removes the clicked fraction from the drafted report list. |
| `Ctrl + Z` | **Undo:** Removes the most recently added specimen fraction. |

### Report Configuration Toggles
| Shortcut | Action |
| :--- | :--- |
| `Ctrl + Alt + M` | **Malignancy Mode:** Filters list strictly for malignant/metastatic diagnoses (highlights list red). |
| `Ctrl + U` | **Toggle Clinical Info:** Includes/excludes the "Übersandt/Clinical Info" section. |
| `Ctrl + M` | **Toggle Microscopy:** Includes/excludes the "Mikroskopie/Microscopic" section. |
| `Ctrl + I` | **Toggle IHC:** Includes/excludes the "Immunohistochemistry" section. |
| `Ctrl + S` | **Toggle Signature:** Appends the configured pathologist's signature block. |
| `Ctrl + D` | **Toggle Dynamic Fields:** Enables/disables the resolution of dynamic form fields. |

### Finalizing & Submission
| Shortcut | Action |
| :--- | :--- |
| `Ctrl + Enter` | **Submit Report:** Generates the final text, resolves variables, and pastes it into the LIS. |
| `Ctrl + Alt + Enter` | **Manual Labeling:** Opens the batch-editing dialog to define sizes, block numbers, and stains for all fractions. |
| `Esc` | **Close/Abort:** Sequentially closes active sub-dialogs, then the main GUI. |

---

## ⚙️ Technical Details and Functionalities

PathoText is written in AutoHotkey v2 (AHK v2) and is a portable application. No installer or Windows Registry modification is required.

### 1. Security notice (first launch)

PathoText is an open-source project and the current Windows release is distributed as a freshly compiled executable that is **not digitally signed**.

Because of this, Windows SmartScreen or some antivirus programs may display a warning when starting `PathoText.exe` for the first time. This does **not necessarily indicate a problem with the application**; such warnings are common for newly released executables from smaller open-source projects that do not yet have a reputation history with Microsoft or antivirus providers.

PathoText includes the complete source code used to build the application. Institutions, laboratories, or organizations that require internally trusted software distribution can review the source code, compile the application themselves, and apply their own digital signature or certificate in accordance with their internal security policies.

If you downloaded PathoText from the official GitHub repository, you can verify the included source code and build files yourself. If you have concerns, please review the source code or report any suspicious behavior through the GitHub issue tracker.

Future releases may include additional verification or signing mechanisms as the project matures.

### 2. Intelligent Specimen Grouping (`GroupLinesIndexed`)
Pathology reports often contain multiple blocks or biopsies belonging to the same anatomical site.
* **Technical Implementation:** The script maintains an array of `fractions`. When users press `Alt+Enter`, the new module is assigned the `GroupNum` of the preceding entry. The `GroupLinesIndexed` function dynamically condenses these entries (e.g., merging identical texts across multiple block numbers into ranges like "1. - 3. Adenocarcinoma") and applies a dense numbering map (`_DenseGroupNumMap`) so the final report always outputs sequential numbers, even if intermediate fractions were deleted.

### 3. Unified Dynamic Variables & Conditional Logic
Standard text modules frequently require situational adjustments (e.g., grading, resection margins).
* **Technical Implementation:** A custom Regex parser (`ExtractUnifiedVariables`) scans texts for a unified syntax:
  * `[Variable]` (Free text)
  * `[Variable=Default]` (Pre-filled default)
  * `[Variable|Opt1§Opt2]` (Dropdown)
  * `[Name=Fallback|IF:Source=Value:Text]` (Conditional logic)
* **Real-time Evaluation:** The `ShowGroupedUnifiedForm` builds a dynamic GUI on the fly. Event listeners (`UpdateConditionals`) recalculate conditional fields instantaneously when their source fields change, preventing illogical diagnostic combinations before the text is even inserted.

### 4. Optional offline AI processing
To assist with stylistic corrections and structured data extraction without violating HIPAA/GDPR constraints, PathoText supports 100% offline LLM integration built entirely on [llama.cpp](https://github.com/ggml-org/llama.cpp) — no request ever leaves the local machine (or, at most, the local network in "hosted server" mode).

* **Three selectable AI modes** (under Settings → *Advanced AI Features*):
  1. **Use local AI** — runs `llama-completion.exe` directly on the workstation for each request.
  2. **Use a remote AI server** — talks to an already-running `llama-server` elsewhere on the network (e.g. a more powerful shared machine).
  3. **Host a local server** — starts and stops `llama-server.exe` on the current machine at the click of a button, showing the model name and the network address other workstations can connect to (mode 2, from their point of view).
* **AI Manager:** A built-in model catalog (`lib/models/models.json`) lets you install, update, remove, and switch between multiple GGUF models without touching a config file by hand. Downloads run through `curl.exe` (bundled with Windows 10 1803+/11) with automatic resume support; an optional SHA-256 check (via `certutil`) and an ETag-based safety check together guard against resuming a download whose remote file changed in the meantime.
* **Individual, savable prompts:** Both AI actions ("Style & Spelling" and "Dynamic Elements") start from a sensible default prompt, but any edited prompt can be saved, updated, or deleted as a named personal template and is remembered per action for next time.
* **Section-aware & batch processing:** The AI can be pointed at specific report sections (Clinical Information, Microscopy, Diagnosis, Immunohistochemistry) instead of the whole text, and multiple selected sections can either be reviewed one dialog at a time or, optionally, together in a single side-by-side comparison window.
* **Safety mechanisms:** Prompts are sent via a temporary UTF-8 file to avoid character corruption over legacy Windows ANSI pipes. A hard watchdog timer kills the inference process tree after 180 seconds if it hangs, preventing the main application thread from locking up. AI-generated edits are **never** applied automatically — a side-by-side diff dialog always requires the pathologist to review and explicitly accept (or reject) every suggested change before it is committed, section by section.

### 5. Multi-User Database Safety & Schema Migration
PathoText is works in networked environments where multiple pathologists might share the same underlying module database.
* **Technical Implementation:** It utilizes a local SQLite engine (`SQLight.ahk`) running in Write-Ahead Logging (`WAL`) mode with a 10-second `busy_timeout`. This prevents database locking errors when multiple users access the file simultaneously.
* **Alphanumeric Scaling:** To prevent exhausting code IDs, `_NextAlphaNumericSuffix` queries the DB directly for the next available ID using a Base35 encoding system, allowing up to 1,225 unique modules per organ/department combination.
* **Data Integrity:** Routine backups (`_DoDbBackup`) are automatically rotated, and schema updates (`_MigrateDbSchema`) are performed silently on startup to ensure backward compatibility with older database files.

### 6. Atomic Target Window Pasting
Pasting text into third-party Laboratory Information Systems can be prone to focus-stealing errors.
* **Technical Implementation:** The `SubmitText` function verifies the Target Window's Title and Class against the ones captured at launch. It utilizes `ClipboardAll()` to backup the user's existing clipboard, uses `ClipWait` deterministically, and forces atomic pasting via `SendInput "^v"`, completely restoring the user's clipboard state milliseconds later.

### 7. Database & Multi-User Capacity

PathoText utilizes `SQLight.ahk` for efficient local data storage and institute-wide scaling.

- **WAL-Mode:** The database operates in Write-Ahead Logging mode to enable safe, concurrent access by multiple pathologists on a shared network drive.
- **Network Integrity:** Implements a `10,000 ms busy_timeout` to handle network latency without locking the GUI.
- **Centralization:** Database paths and PDF reference folders can be configured to shared UNC paths via `settings.ini` for institute-wide uniformity.

### 8. IT Security & Privacy

PathoText is designed for environments where patient data must remain under local control.

- Local AI processing does not send patient information to external services.
- Database operations use prepared statements.
- No cloud service is required for the core functionality.

### 9. Malignancy Mode (`Ctrl + Alt + M`)

This feature provides a focused diagnostic view for oncological reporting.

- **Technical Implementation:** Activating this mode applies a bitmask-based filter (`AND IsMalignant = 1`) to the SQL query, isolating malignant entries.
- **Visual Feedback:** The GUI triggers a color shift to a red-tinted schema via `GuiControl`, providing immediate visual confirmation that the malignancy filter is active.

### 10. Internationalization & Extension

PathoText is built to be language-agnostic for global medical application.

- **Language Support:** All UI text resides in external `*.lang` files (key-value pairs). All six bundled languages (`de`, `en`, `es`, `fr`, `it`, `pl`) are kept in lockstep — automated checks ensure every key exists in every language and that placeholders (`{0}`, `{1}`, ...) line up.
- **Dynamic Loading:** The `T()` translation function fetches strings upon startup based on the `Language` setting in `settings.ini`. Adding a new language simply requires creating a new `.lang` file, which the system automatically detects.

### 11. Customization & Module Management

PathoText provides granular control over the report output and database structure.

- **Configuration:** The `settings.ini` is initialized upon first launch, capturing environmental variables and setting default paths for headers and signatures.
- **Text Management:** Supports full CSV import/export for batch managing diagnostic modules.
- **Validation:** Import routines include hash-based (`ModuleUID`) validation to prevent duplicate entries and maintain schema integrity.

### 12. Backup & Recovery

PathoText prioritizes data resilience and disaster recovery.

PathoText creates automatic database backups to reduce the risk of data loss. Rollback can be initialized in the settings menu.

### 13. Advanced Fuzzy Search & Medical Abbreviation Engine
This section documents some internal implementation choices for developers and interested users. Unlike standard substring filters, the search engine is built specifically for medical terminology.
* **Technical Implementation Details:** Uses a custom, heavily optimized Levenshtein distance algorithm (`_Levenshtein`) with an early-exit threshold to maintain O(n) performance on keystrokes.
* **Caching (`_GetNormCache`):** Normalization (lowercasing, umlaut replacement, special character stripping) is computationally expensive. The script calculates this once per database entry upon loading and caches it, ensuring that rapid typing (`NameFilterChanged` with a 150ms debounce) remains completely lag-free.
* **Bidirectional Expansion:** The `_FuzzyTokenMatch` function utilizes a predefined abbreviation map (`_FuzzyAbbrevMapNorm`). Searching for "bcc" automatically matches "basalzellkarzinom" and vice versa, seamlessly bridging the gap between quick shorthand and official diagnostic nomenclature.

---

## Acknowledgments

PathoText builds on the excellent work of several open-source projects and, for its optional AI features, publicly available language models:

- **[AutoHotkey v2](https://www.autohotkey.com/)** — the scripting language and runtime PathoText is written in.
- **[SQLite](https://www.sqlite.org/)** — the embedded, serverless database engine used for all local data storage, and the **[SQLight.ahk](https://github.com/Nachtgigerbyte/SQLight)**-style AutoHotkey wrapper used to access it.
- **[llama.cpp](https://github.com/ggml-org/llama.cpp)** (and the underlying **[ggml](https://github.com/ggml-org/ggml)** tensor library) by Georgi Gerganov and contributors — the local inference engine (`llama-completion.exe` / `llama-server.exe`) that powers every offline AI feature in PathoText (MIT licence).
- **[curl](https://curl.se/)** — used by the built-in AI Manager to download and resume model files.
- **MedicalWP** — the icon used by the compiled script, designed by MadOyster (CC BY 3.0 US).
- The GGUF model files distributed for use with PathoText are quantized/republished by their respective communities, building on the original model weights released by their creators, including:
  - **[Microsoft Phi-4-mini-instruct](https://huggingface.co/microsoft/Phi-4-mini-instruct)**, quantized by **[bartowski](https://huggingface.co/bartowski)** (MIT licence).
  - **[Qwen3-30B-A3B-Instruct-2507](https://huggingface.co/Qwen/Qwen3-30B-A3B-Instruct-2507)** by the Qwen team (Alibaba), quantized by **[unsloth](https://huggingface.co/unsloth)** (Apache-2.0 licence).
