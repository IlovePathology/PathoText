# PathoText
Lightweight Tool for Efficient Structured Reporting in Surgical Pathology.

PathoText is a keyboard-driven text module and structured reporting tool for surgical pathology. It was developed to simplify repetitive reporting tasks, speed up access to diagnostic text modules, and provide a consistent structure for pathology reports.

The program is intended to complement existing Laboratory Information Systems (LIS) workflows rather than replace them.

## Key Features

- **Keyboard-Driven Workflow:** Ultra-fast, keyboard-only navigation and report generation using configurable hotkeys and a master toggle (`Ctrl + Alt + F5`).
- **Advanced Fuzzy Search:** Instant medical term and abbreviation matching via optimized Levenshtein distance with normalization caching.
- **Dual Execution Modes:** Full operational support with dynamic mode switching between Core Mode (lightweight, zero-dependency single EXE) and Full Mode.
- **Intelligent Specimen Grouping:** Dynamic fraction assembly (`GroupLinesIndexed`) with automatic index dense-mapping and number range consolidation (e.g., "1. - 3. Adenocarcinoma").
- **Unified Dynamic Form Fields:** Custom dynamic variable parsing (`ExtractUnifiedVariables`) supporting default values, dropdowns, and real-time conditional logic.
- **Integrated Offline Speech-to-Text (STT):** Local voice dictation powered by whisper.cpp with push-to-talk workflow, configurable beam size, and optional VAD silence trimming via SoX.
- **Persistent STT Server Mode:** High-performance HTTP-based speech recognition routing (`_RunWhisperViaServer`) with lazy lifecycle management and process tree recovery.
- **Automated Text Replacement Engine:** Custom post-speech/AI word replacements with literal symbol support (`p53+`), German umlaut boundary detection, and long-phrase prioritization.
- **Target Window Safety Guard:** Three-tier target window validation (`IsValidTargetWindow`) preventing accidental text injection into wrong LIS applications.
- **100% Offline LLM Processing:** Optional AI-powered text refinement, stylistic correction, and variable extraction running locally via llama.cpp (CLI, remote, or self-hosted server).
- **AI Snippet Capture:** Smart shortcut-driven (`Ctrl + Alt + B`) clipboard extraction to analyze selection and pre-fill new text module creation.
- **Multi-User SQLite Architecture:** Concurrent multi-user access via WAL-mode database operations with automatic daily rolling backups and schema migration.
- **Direct PDF Reference Access:** Quick access to linked diagnostic guidelines, WHO classification manuals, and institutional standards.
- **CSV Data Exchange & Security:** Full CSV import/export for diagnostic modules with hash-based UID duplicate prevention and standard proxy network support.
- **Interactive AI Query Engine (`Ctrl + Alt + Q`):** Floating prompt interface for instant local LLM queries with custom system prompts and direct text insertion into active target windows.
- **Dedicated Word Library & Punctuation Manager:** Full-featured GUI for managing custom speech-to-text replacement rules, multi-line substitutions (`\n`), and localized default quote styles.
- **Unified Live AI Wait Indicator:** Real-time visual overlay providing dynamic elapsed-second tracking for all local AI background tasks (STT, grammar cleanup, snippets, and queries).
- **Target Field Capture:** (Settings → Section Labels → Target Fields): An optional feature that lets you map each report section (Macroscopy, Microscopy, Diagnosis, IHC) to a specific text field in your LIS by simply clicking on it — PathoText will then inject each section's text directly into the correct field when submitting, without relying on coordinates or clipboard focus.
---

## Architecture & Modes

PathoText operates in two distinct execution modes depending on system requirements and environment:

- **Full Mode:** Includes all features, including local Speech-to-Text (whisper.cpp / SoX) and local LLM-assisted text refinement.
- **Core Mode:** A lightweight, pure text-module and reporting setup with zero external binary dependencies for AI/STT.
- **Automatic Detection:** The application dynamically detects its running mode via `data\build.ini` (`[Build] Mode=core|full`), compiled resource checks, or binary presence heuristics. In Core Mode, AI/STT subsystems degrade gracefully without false warnings or missing resource errors.

## Installation & Usage

PathoText is designed as a **portable application** and does not require a traditional installation or Windows Registry modifications.

### Deployment Modes & Directory Structure

#### A) Compiled Mode (Recommended for End Users)

In the compiled version, PathoText runs out-of-the-box as a **single-file executable**. Language resources (`.lang` files) are directly embedded into `PathoText.exe` via Win32 resources, eliminating any requirement for external runtime folders.

- **Core Mode (Zero Setup):** You only need `PathoText.exe`. All required internal folders (`data/`, `data/backup/`), local databases, and configuration files (`settings.ini`) are automatically created on first launch if they do not exist.
- **Full Mode (Optional Extensions):** Advanced offline AI and Speech-to-Text capabilities require placing their respective engine binaries and model files into the `lib/` directory structure.

```text
PathoText/
├── PathoText.exe                # Single executable (All Core resources embedded)
├── diagnosen.db                 # SQLite database (created automatically on first launch)
├── data/                        # Application data directory (created automatically)
│   ├── settings.ini             # User settings and preferences
│   ├── tts_replacements.txt     # Custom post-STT automated text replacements
│   ├── error.log                # Application log
│   └── backup/                  # Automatic daily database backups
│       ├── diagnosen.db.bak.1
│       └── ...
├── pdf/                         # Linked guideline and reference PDFs (optional)
└── lib/                         # Optional extensions directory (Full Mode only)
    ├── sqlite3.dll              # Optional: SQLite fallback library
    ├── lang/                    # Optional: External .lang files for dynamic overrides
    ├── engine/                  # Optional (Full Mode): llama.cpp runtime for local AI
    │   ├── llama-completion.exe
    │   └── llama-server.exe
    ├── models/                  # Optional (Full Mode): AI model catalog (models.json)
    │   └── whisper-models.json  # STT model catalog for Speech Manager
    └── whisper/                 # Optional (Full Mode): whisper.cpp & SoX runtime
        ├── whisper-cli.exe
        ├── whisper-server.exe
        └── sox.exe
```

#### B) Source Mode (For Developers & Customization)

In source mode, the script is executed directly using the **AutoHotkey v2** interpreter. Ensure that AutoHotkey v2 is installed before launching the application.

```text
PathoText/
├── PathoText.ahk                # Application entry point
├── lib/
│   ├── SQLight.ahk              # AutoHotkey SQLite wrapper
│   └── sqlite3.dll
├── ...                          # Remaining structure identical to compiled mode
```

> **Note:** The `lib/engine/`, `lib/models/`, and `lib/whisper/` folders are entirely optional. In Core Mode or without these folders, PathoText runs as a high-performance text-module/reporting tool.

---

## First Launch & Initialization

When PathoText is started for the first time, it automatically detects that no `data/settings.ini` exists and launches the initial configuration wizard.

### Initial Setup

1. **Launch the application**
   - Run `PathoText.exe` (or `PathoText.ahk` in source mode with installed **Autohotkey v2**).

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
   - After completing the wizard, PathoText creates a `settings.ini` file inside the `data/` directory.
   - This file serves as the **single source of truth** for all application settings.

No pre-existing `data/settings.ini`, `data/error.log`, or database is required to roll out PathoText on a new machine—all are created from scratch on first launch, and legacy root configurations are automatically migrated.

---

## Configuration via `settings.ini`

The `data/settings.ini` file controls nearly every aspect of PathoText's behavior.

### Paths

- Database and PDF locations can be changed at any time.
- Updates take effect automatically the next time the application starts.

### Language

- Under `[Settings] -> Language`, you can switch between any available localization resources (for example, `en`, `de`, `es`, `fr`, `it`, or `pl`).
- External `.lang` files can be placed in `lib/lang/` for dynamic overrides or additions.

### Target Window Safety Guard

- Target window validation enforces a three-tier safety check (process whitelist → control class check → main window fallback) during text insertion.
- The target window warning dialog includes an optional "Do not show this message again" toggle (`SuppressWinWarn=1`).

### Default Report Sections

- Settings in the `[Checkboxes]` section define which report components (e.g., **Microscopy**, **Immunohistochemistry**, **Clinical Information**) are enabled by default when the application starts.

### Global Hotkeys

- The Master Toggle via `Ctrl + Alt + F5` (`hotkeyEnabled`) enables or disables PathoText's global keyboard shortcuts to avoid conflicts with LIS software.

### Advanced AI & Speech Settings

- A collapsible **"Advanced AI Features"** section lets you choose between local AI execution, remote `llama-server` connection, or self-hosting a local server.
- The Speech-to-Text configuration exposes beam size selection (1–20), greedy decoding parameters, optional server mode execution, and optional audio feedback (`SttRecordingSound`).

---

## Keyboard Shortcuts & Navigation

PathoText is built for absolute keyboard efficiency.

### Global & Launch
| Shortcut | Action |
| :--- | :--- |
| `Ctrl + Alt + F5` | **Master Toggle:** Toggles all application hotkeys on/off. |
| `F5` | **Launch/Cycle:** Opens the GUI if closed. If open, acts as shortcut for the 4th department. |
| `Ctrl + Alt + B` | **AI Snippet Capture:** Reads selection from clipboard, analyzes with LLM, and opens pre-filled "Add text snippet" dialog. |
| `Ctrl + Alt + R` | **Add Text Replacement:** Reads highlighted text and opens the automated STT/text replacement setup dialog. |
| `Ctrl + Alt + Q` | **AI Query Engine:** Opens the floating AI prompt window (Full Mode) or displays a status notice (Core Mode). |

### Navigation & Search
| Shortcut | Action |
| :--- | :--- |
| `[Text] + Space` | **Fuzzy Search:** Type in the main field to instantly filter modules (requires ≥ 2 characters for fuzzy matching). |
| `Tab` / `Shift + Tab` | **Focus Toggle:** Switch focus directly between search field and results list. |

### Department & Organ Selection
| Shortcut | Action |
| :--- | :--- |
| `F1` | **Reset Filter:** Shows all departments and organs. |
| `F2` - `F9` | **Select Department:** Rapidly select departments 1 through 8. |
| `F2` - `F9` *(Repeated)* | **Cycle Organs:** Pressing the same F-key again cycles through organs within that department. |
| `F10` | **Select 9th Department:** Assigned to the 9th department if configured. |

### Specimen Assembly (Fractions)
| Shortcut | Action |
| :--- | :--- |
| `Enter` | **Add New Specimen:** Adds selected module as a *new* specimen fraction (increments grouping number). |
| `Alt + Enter` | **Combine Specimen:** Appends selected module to *currently active* specimen fraction. |
| `Double Click` | **Remove Specimen:** Removes clicked fraction from drafted report. |
| `Ctrl + Z` | **Undo:** Removes most recently added specimen fraction. |

### Report Configuration Toggles
| Shortcut | Action |
| :--- | :--- |
| `Ctrl + Alt + M` | **Malignancy Mode:** Filters list strictly for malignant diagnoses (highlights red). |
| `Ctrl + U` | **Toggle Clinical Info:** Includes/excludes "Clinical Info" section. |
| `Ctrl + M` | **Toggle Microscopy:** Includes/excludes "Microscopic" section. |
| `Ctrl + I` | **Toggle IHC:** Includes/excludes "Immunohistochemistry" section. |
| `Ctrl + S` | **Toggle Signature:** Appends pathologist's signature block. |
| `Ctrl + D` | **Toggle Dynamic Fields:** Enables/disables resolution of dynamic form fields. |

### Finalizing & Submission
| Shortcut | Action |
| :--- | :--- |
| `Ctrl + Enter` | **Submit Report:** Generates final text, resolves variables, and pastes into LIS target window. |
| `Ctrl + Alt + Enter` | **Manual Labeling:** Opens batch-editing dialog for sizes, block numbers, and stains. |
| `Esc` | **Close/Abort:** Sequentially closes active sub-dialogs, then main GUI. |

---

## Technical Details and Functionalities

PathoText is written in AutoHotkey v2 (AHK v2) and operates as a portable application with zero reliance on traditional installation routines or Windows Registry modifications.

### 1. Security & Target Window Safety Guard
- **Unsigned Executable:** Distributed as an open-source project with compiled executables. Organizations requiring trusted local deployments can review source code, build locally, and apply internal certificates.
- **Target Window Verification (`IsValidTargetWindow`):** To prevent accidental text injection into incorrect windows, text insertion triggers a mandatory three-tier validation check (process whitelist → control class check → main window fallback). 
- **User Preference:** Warning dialogs support direct suppression (`SuppressWinWarn=1`), persisted automatically in `data/settings.ini`.

### 2. Core vs. Full Mode Architecture
- **Dynamic Mode Detection (`_DetectAppMode`):** Automatically determines whether the app runs in lightweight Core Mode or feature-complete Full Mode via `data/build.ini` (`[Build] Mode=core|full`), compiled resource inspection, or binary heuristics.
- **Embedded Resources:** Compiled binaries embed `.lang` files via `@Ahk2Exe-AddResource` directives, loaded using Win32 API routines (`_LoadLangFromResource`), eliminating external folder requirements while preserving dynamic overrides via `lib/lang/`.
- **Graceful Feature Degradation:** In Core Mode, missing AI/STT binaries automatically disable advanced menu items (`.Enabled := false`) with clear tooltips instead of throwing startup errors.

### 3. Intelligent Specimen Grouping (`GroupLinesIndexed`)
Pathology reports frequently handle multiple biopsy blocks for identical anatomical locations.
- **Technical Implementation:** Maintains an internal array of `fractions`. Appending with `Alt+Enter` assigns the preceding entry's `GroupNum`.
- **Dynamic Formatting:** The `GroupLinesIndexed` function condenses matching entries (e.g., merging identical diagnoses into ranges like `"1. - 3. Adenocarcinoma"`) and applies a dense numbering map (`_DenseGroupNumMap`) to enforce sequential output even if intermediate fractions are deleted.

### 4. Unified Dynamic Variables & Conditional Logic
- **Regex Parsing (`ExtractUnifiedVariables`):** Evaluates inline dynamic syntax including free text (`[Variable]`), pre-filled defaults (`[Variable=Default]`), dropdown menus (`[Variable|Opt1§Opt2]`), and conditional evaluation (`[Name=Fallback|IF:Source=Value:Text]`).
- **Real-Time Dynamic GUI (`ShowGroupedUnifiedForm`):** Event listeners (`UpdateConditionals`) dynamically recalculate conditional fields on keystroke/selection changes before final text construction.

### 5. High-Performance Fuzzy Search & Abbreviation Engine
- **Optimized Matching:** Custom Levenshtein distance algorithm (`_Levenshtein`) with early-exit thresholds and a Min-Length Guard (triggers only for inputs ≥ 2 characters) to maintain O(n) performance.
- **Normalization Cache (`_GetNormCache`):** Pre-computes lowercasing, umlaut expansion, and symbol stripping per database entry to support debounced (150 ms) lag-free search.
- **Bidirectional Abbreviation Mapping (`_FuzzyAbbrevMapNorm`):** Token matching automatically expands clinical shorthand (e.g., matching `"bcc"` to `"basalzellkarzinom"` and vice versa).

### 6. Automated Post-STT & Custom Text Replacements
- **Automated Replacement Engine:** Custom user replacements are stored locally in `data/tts_replacements.txt` and processed immediately prior to final output typing.
- **Quick Creation (`Ctrl + Alt + R`):** Selection-driven popup pre-fills misrecognized terms while safely capturing and restoring user clipboard contents.
- **Smart Rule Matching:** Handles literal special characters and biological terms (e.g., `p53+`), full German umlaut boundary detection, and prioritizes longer phrases over single words to prevent broken partial substitutions.

### 7. Integrated Speech-to-Text (STT) & Audio Pipeline
- **Offline Dictation (whisper.cpp & SoX):** Offline voice recognition with audio capture and VAD (Voice Activity Detection) silence trimming via SoX (`silence 1 0.3 1% reverse...`).
- **CLI & HTTP Server Modes:** Supports execution via direct CLI or local HTTP server (`_RunWhisperViaServer` via `curl`) with lazy lifecycle management, dynamic startup timeouts, and process-tree teardown on exit.
- **Decoding & UI Controls:** Features configurable beam sizes (1–20), zero-temperature greedy decoding to minimize silence hallucinations, optional start/end audio feedback (880 Hz / 440 Hz), and fixed top-left dictation status indicators (`x10 y10`).

### 8. Offline AI Processing & Refinement
- **100% Local LLM Integration:** Powered by **llama.cpp** in three operational modes (Local CLI, Remote Server, or Self-Hosted Server).
- **Prompt & Catalog Management:** Built-in AI Manager handles model downloads via `curl` with resume support, SHA-256 validation (`certutil`), custom prompt template storage, 180-second watchdog execution timers, and side-by-side diff acceptance dialogs.

### 9. Atomic Target Window Pasting (`SubmitText`)
- **Focus Safety:** Validates target window parameters against captured handles prior to injection.
- **Clipboard Management:** Backs up clipboard data via `ClipboardAll()`, verifies payload synchronization via `ClipWait`, forces atomic injection (`SendInput "^v"`), and restores historical clipboard contents within milliseconds.

### 10. Multi-User Database Capacity & Schema Migration
- **Concurrent Network Access:** Utilizes `SQLight.ahk` running in Write-Ahead Logging (`WAL`) mode with a `10,000 ms busy_timeout` to prevent database lock conflicts across shared network drives.
- **Alphanumeric ID Scaling:** `_NextAlphaNumericSuffix` generates Base35 code IDs, allowing up to 1,225 unique modules per organ/department combination.
- **Data Resilience:** Silent schema migrations (`_MigrateDbSchema`) and automated daily backup rotation (`_DoDbBackup`) retain historical snapshots in `data/backup/`.

### 11. Centralized Data Architecture & Auto-Migration
- **Centralized `data/` Pathing:** Internal configuration, logs, and database backups reside within `data/` (`settings.ini`, `error.log`, `tts_replacements.txt`).
- **Startup Migration (`_MigrateRootFilesToData`):** Legacy configuration files and root backups are automatically moved to their central locations on launch.

### 12. Malignancy Filter Mode (`Ctrl + Alt + M`)
- **Diagnostic Isolation:** Applies a bitmask SQL filter (`AND IsMalignant = 1`) to instantly constrain search results strictly to malignant/metastatic diagnoses.
- **Visual Feedback:** Triggers a dynamic UI theme shift to a red-tinted color schema, providing immediate visual status confirmation.

### 13. Internationalization & String Safety
- **Localization Files:** All user-facing strings are decoupled into `.lang` key-value files (`de`, `en`, `es`, `fr`, `it`, `pl`), loaded dynamically via `T()` translation wrappers.
- **String Integrity Audits:** Translation keys across all languages are kept strictly aligned with identical placeholder counts (`{0}`, `{1}`) to prevent formatting runtime crashes.

### 14. Module Management, CSV & IT Security
- **Data Exchange:** Full CSV import and export capabilities with SHA/UID validation (`ModuleUID`) to ensure catalog integrity.
- **Hospital Network Compliance:** Encrypted local proxy storage inside `settings.ini` guarantees compatibility with restricted clinical IT network infrastructure.
- **Data Privacy:** Local execution ensures zero patient data or medical report text leaves the institutional network boundaries.

### 15. Context-Aware AI Snippet Capture (`Ctrl + Alt + B`)
Creating new diagnostic modules from recurring report fragments usually requires manual copy-pasting and formatting.
- **Workflow & Clipboard Interception:** Pressing `Ctrl + Alt + B` captures the active text selection from any target application into the clipboard, backs up existing clipboard contents, and opens the "Add Text Snippet" dialog.
- **Automated Text Refinement & LLM Structuring:** In Full Mode, the highlighted text is automatically passed to the local LLM. The AI strips case-specific patient details, optimizes formatting, and suggests an appropriate title, target organ, and department for the new text module.
- **Fallback Behavior:** In Core Mode (or when local AI processing is disabled), the system bypasses LLM inference and populates the module creation form directly with the raw selected text, preserving zero-latency manual editing.

### 16. Word Library & Punctuation Defaults
- **Centralized Rule Management:** Replaces manual editing of `data/tts_replacements.txt` with a debounced search-enabled GUI, supporting full CRUD operations for phonetic and misrecognition corrections.
- **Multi-line & Escape Parsing:** Preserves single-line file structure while parsing escaped line breaks (`\n`) and paragraph breaks (`\n\n`), visually represented as `⏎` and `⏎⏎` within the rule management list.
- **Dynamic Localized Defaults (`_WlGetPunctDefaults`):** Automatically injects 15 standard punctuation rules adapted to the active UI language (e.g., German/Polish `„ ... “`, French/Italian/Spanish `« ... »`, or English `“ ... ”`).

### 17. Asynchronous AI Query Engine (`Ctrl + Alt + Q`)
- **Non-Blocking Execution:** Operates as a top-left anchored (`x10 y10`), single-instance floating utility (`+AlwaysOnTop +ToolWindow`). Uses asynchronous `SetTimer` routines during LLM execution to prevent UI freezing.
- **Workflow & Insertion:** Features collapsible system prompt presets tailored for pathology queries. Generated answers can be copied or pasted directly into the validated LIS target window via `SendText()`.
---

## Acknowledgments

PathoText builds on the excellent work of several open-source projects:

- **[AutoHotkey v2](https://www.autohotkey.com/)** - Scripting language and runtime environment.
- **[SQLite](https://www.sqlite.org/)** & **[SQLight.ahk](https://github.com/Nachtgigerbyte/SQLight)** - Embedded database engine and AHK wrapper interface.
- **[llama.cpp](https://github.com/ggml-org/llama.cpp)** / **[ggml](https://github.com/ggml-org/ggml)** - Local LLM inference runtime (MIT licence).
- **[whisper.cpp](https://github.com/ggml-org/whisper.cpp)** - High-performance local speech recognition engine.
- **[SoX (Sound eXchange)](https://sox.sourceforge.net/)** - Command-line audio processing utility.
- **[curl](https://curl.se/)** - File transfer and HTTP API utility.
- **MedicalWP** - Script icon by MadOyster (CC BY 3.0 US).
- **Language Models:**
  - **[Microsoft Phi-4-mini-instruct](https://huggingface.co/microsoft/Phi-4-mini-instruct)**, quantized by **[bartowski](https://huggingface.co/bartowski)** (MIT licence).
  - **[Qwen3-30B-A3B-Instruct-2507](https://huggingface.co/Qwen/Qwen3-30B-A3B-Instruct-2507)**, quantized by **[unsloth](https://huggingface.co/unsloth)** (Apache-2.0 licence).
