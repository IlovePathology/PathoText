# PathoText
Lightweight Tool for Efficient Structured Reporting in Surgical Pathology

PathoText is a fast, keyboard-driven text module and structured reporting system designed specifically for the daily diagnostic workflow in surgical pathology. Developed to seamlessly integrate into existing Laboratory Information Systems (LIS), it eliminates repetitive typing and standardizes complex diagnostic reports. The tool utilizes a lightweight local SQLite database for rapid data retrieval and features an optional, privacy-safe local AI integration for intelligent text processing.  

## Key Features
- 	Lightning-Fast Navigation: A fully keyboard-optimized interface allows pathologists to navigate departments, organs, and modules using F-keys and dedicated shortcuts without breaking visual focus.  
- 	Smart Medical Search: Built-in fuzzy search algorithms with Levenshtein distance automatically expand common medical abbreviations (e.g., "bcc" to "basalzellkarzinom", "pe" to "probeexzision").  
- 	Structured Specimen Grouping: Seamlessly handle multiple specimen fractions, logically grouping macroscopic, microscopic, and diagnostic texts into a cohesive final report.  
- 	Dynamic & Conditional Fields: Advanced form generation supporting free-text, defaults, dropdowns, randomized variants, and conditional logic.  
- 	Privacy-First Local AI: Optional integration with local Large Language Models (via llama.cpp and models like Phi-4-mini-instruct) for stylistic text correction and automated variable extraction, guaranteeing that zero patient data leaves the local machine.  
- 	Guideline Integration: Instant access to linked PDF guidelines, WHO classifications, and IHC references directly from the diagnostic entry interface.  
- 	Data Portability: Full support for CSV import and export to easily manage and share diagnostic text modules.  
- 	Core Workflow & ShortcutsPathoText is designed to operate unobtrusively in the background, invoked only when needed to construct a report.  

## Installation & Usage

PathoText is designed as a **portable application** and does not require a traditional installation or Windows Registry modifications.

### Directory Structure

The directory layout is essential for proper operation, particularly for loading the `sqlite3.dll`, language files, and optional AI components.

#### A) Compiled Mode (Recommended for End Users)

In the compiled version, only `PathoText.exe` needs to be launched. All required dependencies must reside in the expected directory structure:

```text
PathoText/
├── PathoText.exe
├── lib/
│   ├── sqlite3.dll         # Required for SQLite database access
│   ├── engine/             # Optional: llama.cpp runtime for local AI
│   └── models/             # Optional: .gguf language models
├── lang/          
│   └── xx.lang             # Language files
├── db/                     # Database directory (PathoText.db)
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

---

## First Launch & Initialization

When PathoText is started for the first time, it automatically detects that no `settings.ini` exists and launches the initial configuration wizard.

### Initial Setup

1. **Launch the application**
   - Run `PathoText.exe` (or `PathoText.ahk` in source mode).

2. **Configuration Wizard**
   - A guided setup dialog will prompt you to configure the core application paths.

3. **Database Location**
   - Select the location of your `PathoText.db` file.
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

---

## Configuration via `settings.ini`

The `settings.ini` file controls nearly every aspect of PathoText's behavior.

### Paths

- Database and PDF locations can be changed at any time.
- Updates take effect automatically the next time the application starts.

### Language

- Under `[General] -> Language`, you can switch between any available `.lang` files (for example, `en`, `de`, or other installed languages).

### Default Report Sections

- Settings in the `[Checkboxes]` section define which report components (e.g., **Microscopy**, **Immunohistochemistry**, **Clinical Information**) are enabled by default when the application starts.

### Global Hotkeys

- The Master Toggle via Ctrl + Alt + F5 (`hotkeyEnabled`) option enables or disables PathoText's global keyboard shortcuts.
- This is useful for preventing conflicts with other Laboratory Information Systems (LIS) or specialized medical software.


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

## ⚙️ Technical Architecture & Core Functionalities

PathoText is written in AutoHotkey v2 (AHK v2) and is designed to handle high-throughput diagnostic reporting with maximum performance and minimal memory footprint. 

### 1. Advanced Fuzzy Search & Medical Abbreviation Engine
Unlike standard substring filters, the search engine is built specifically for medical terminology.
* **Technical Implementation:** Uses a custom, heavily optimized Levenshtein distance algorithm (`_Levenshtein`) with an early-exit threshold to maintain O(n) performance on keystrokes. 
* **Caching (`_GetNormCache`):** Normalization (lowercasing, umlaut replacement, special character stripping) is computationally expensive. The script calculates this once per database entry upon loading and caches it, ensuring that rapid typing (`NameFilterChanged` with a 150ms debounce) remains completely lag-free.
* **Bidirectional Expansion:** The `_FuzzyTokenMatch` function utilizes a predefined abbreviation map (`_FuzzyAbbrevMapNorm`). Searching for "bcc" automatically matches "basalzellkarzinom" and vice versa, seamlessly bridging the gap between quick shorthand and official diagnostic nomenclature.

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

### 4. Privacy-First Local AI Processing (`RunLlamaCLI`)
To assist with stylistic corrections and complex data extraction without violating HIPAA/GDPR constraints, PathoText supports 100% offline LLM integration.
* **Technical Implementation:** The script acts as a robust wrapper for `llama.cpp`. It bypasses the interactive REPL mode by constructing a strict prompt file and executing `llama-completion.exe` via `RunWait`. 
* **Safety Mechanisms:** Outputs and errors are piped to temporary UTF-8 files to prevent character encoding destruction via standard Windows ANSI pipes. A hard `SetTimer` watchdog kills the child process tree after 180 seconds if inference hangs, preventing the main application thread from locking up. A side-by-side diff dialog (`_ShowAiDiffDialog`) forces the pathologist to verify any AI-generated changes before committing them.

### 5. Multi-User Database Safety & Schema Migration
PathoText is designed to work in networked environments where multiple pathologists might share the same underlying module database.
* **Technical Implementation:** It utilizes a local SQLite engine (`SQLight.ahk`) running in Write-Ahead Logging (`WAL`) mode with a 10-second `busy_timeout`. This prevents database locking errors when multiple users access the file simultaneously.
* **Alphanumeric Scaling:** To prevent exhausting code IDs, `_NextAlphaNumericSuffix` queries the DB directly for the next available ID using a Base35 encoding system, allowing up to 1,225 unique modules per organ/department combination.
* **Data Integrity:** Routine backups (`_DoDbBackup`) are automatically rotated, and schema updates (`_MigrateDbSchema`) are performed silently on startup to ensure backward compatibility with older database files.

### 6. Atomic Target Window Pasting
Pasting text into third-party Laboratory Information Systems can be prone to focus-stealing errors.
* **Technical Implementation:** The `SubmitText` function verifies the Target Window's Title and Class against the ones captured at launch. It utilizes `ClipboardAll()` to backup the user's existing clipboard, uses `ClipWait` deterministically, and forces atomic pasting via `SendInput "^v"`, completely restoring the user's clipboard state milliseconds later.

### 7. Database & Multi-User Capacity

PathoText utilizes `SQLight.ahk` for efficient local data storage and institute-wide scaling.

- **WAL-Mode:** The database operates in Write-Ahead Logging mode to enable safe, concurrent access by multiple pathologists on a shared network drive.
- **Network Integrity:** Implements a `10,000 ms busy_timeout` to handle network latency without locking the GUI, ensuring smooth performance even in high-traffic environments.
- **Centralization:** Database paths and PDF reference folders can be configured to shared UNC paths via `settings.ini` for institute-wide uniformity.

### 8. IT Security & Privacy

The tool is designed with **Security by Design** to ensure strict adherence to medical data regulations.

- **Local-Only Processing:** Zero cloud reliance; no patient data leaves the local machine.
- **Hardened Inputs:** Uses prepared statements for all database interactions to prevent SQL injection and ensure data integrity.
- **Safety Mechanisms:** Input sanitization and routine schema migrations are performed silently on startup to maintain database consistency.

### 9. Malignancy Mode (`Ctrl + Alt + M`)

This feature provides a focused diagnostic view for oncological reporting.

- **Technical Implementation:** Activating this mode applies a bitmask-based filter (`AND IsMalignant = 1`) to the SQL query, isolating malignant entries.
- **Visual Feedback:** The GUI triggers a color shift to a red-tinted schema via `GuiControl`, providing immediate visual confirmation that the malignancy filter is active.

### 10. Privacy-First Local AI Processing (`RunLlamaCLI`)

To assist with stylistic corrections and complex data extraction without violating HIPAA/GDPR constraints, PathoText supports **100% offline LLM integration**.

- **Technical Implementation:** The script acts as a robust wrapper for `llama.cpp`. It bypasses the interactive REPL mode by constructing a strict prompt file and executing `llama-completion.exe` via `RunWait`.
- **Safety Mechanisms:** Outputs and errors are piped to temporary UTF-8 files to prevent character encoding destruction via standard Windows ANSI pipes. A hard `SetTimer` watchdog kills the child process tree after 180 seconds if inference hangs, preventing the main application thread from locking up. A side-by-side diff dialog (`_ShowAiDiffDialog`) forces the pathologist to verify any AI-generated changes before committing them.

### 11. Internationalization & Extension

PathoText is built to be language-agnostic for global medical application.

- **Language Support:** All UI text resides in external `*.lang` files (key-value pairs).
- **Dynamic Loading:** The `T()` translation function fetches strings upon startup based on the `Language` setting in `settings.ini`. Adding a new language simply requires creating a new `.lang` file, which the system automatically detects.

### 12. Customization & Module Management

PathoText provides granular control over the report output and database structure.

- **Configuration:** The `settings.ini` is initialized upon first launch, capturing environmental variables and setting default paths for headers and signatures.
- **Text Management:** Supports full CSV import/export for batch managing diagnostic modules.
- **Validation:** Import routines include hash-based (`ModuleUID`) validation to prevent duplicate entries and maintain schema integrity.

### 13. Backup & Reliability

PathoText prioritizes data resilience and disaster recovery.

- **Automated Backups:** `_DoDbBackup` verifies the existence of a daily backup at startup; if missing, the `.db` file is timestamped and archived.
- **Rollback:** The system provides an automated recovery path, allowing users to restore from the last clean backup if a write corruption is detected during a session.
