# Getting Started with Clipscape

A quick walkthrough of how to install the app and start chatting with your videos.

## 1. Download and install

1. Download the latest installer for your platform from the releases page -> https://clipscape.app/downloads
    - **Windows** — `clipscape-<version>-setup.exe` (NSIS installer)
    - **macOS** — `clipscape-<version>-<arch>.dmg` (separate x64 and arm64 builds)
2. Run the installer and follow the prompts.
3. Launch Clipscape.

The first launch creates a local database and config file under your user data directory. No account or sign-up is required. Everything runs locally except the LLM calls, which go to your chosen model provider through OpenRouter.

## 2. Add a source folder

Clipscape doesn't move or copy your videos. It reads them and extracts individual frames and voice transcription and stores those in it's own folder.

1. In the main area, click **Add Folders** and pick a directory containing videos (or **Add Video** to pick individual files).
2. Clipscape previews the folder, warns about any overlap with folders you've already added, then recursively scans for supported files (`.mp4`, `.avi`, `.mkv`, `.mov`, `.wmv`) and adds them to your library.
3. Duplicate files (using the same path) are skipped automatically. Files with identical content at a different path are picked up later and flagged in the grid as duplicates.

The area in the middle of the window populates with cards, one per video, as soon as the scan finishes.

## 3. Add your OpenRouter API key

Clipscape routes all of its LLM features (video analysis, auto-tagging, and the chat assistant) through [OpenRouter](https://openrouter.ai), which lets you pick from many model providers (Anthropic Claude, Google Gemini, OpenAI, and more) with a single key. You'll need a key from [openrouter.ai/keys](https://openrouter.ai/keys).

1. Click the **gear icon** in the bottom-left to open **Settings**.
2. Go to the **Content Analysis** tab.
3. Under **OpenRouter API**, paste your key into the **API Key** field (it should start with `sk-or-v1-...`).
4. Turn on **Enable AI Features**. This is the master switch for the LLM-powered stages. The Video Analysis and Auto Tagging stages should be enabled by default. If not, flip them on if you want the full pipeline.
5. Optionally pick which model each stage uses (see below). The defaults work out of the box and are what I use locally. 
6. Click **Save**.

> Costs are billed directly to your OpenRouter account based on usage and the models you choose. Analysis and tagging run once per video; chat costs depend on how much you use it and how large a context you send (configurable in settings). The settings screen shows an icon ($) next to cost-affecting options and also shows live per-million-token prices next to each model. Once processed, each video will also show how much it cost to run through the analysis and tagging steps.

### Choosing models

The **Content Analysis** tab has three model pickers, each filtered to models capable of the task:

- **Video Analysis (multimodal)**: must be vision and tool-use-capable; used for the per-video content analysis.
- **Auto-tagging**: text-only, tool-use-capable; a cheaper/faster model usually works well here.
- **RAG Chat**: tool-use-capable; powers the chat assistant.

By default each picker shows a curated shortlist. Flip the **Advanced** toggle to browse the full live OpenRouter catalogue, and use **Refresh** to re-fetch the latest list and prices. A separate **Enhanced Analysis** tab lets you configure a higher-fidelity model and frame settings for re-running a single video on demand.

## 4. Let the pipeline process your videos

Once videos are added, Clipscape automatically processes each one through five stages:

| Stage | What it does |
| ----- | ------------ |
| Generate Thumbnails | Probes the file, extracts technical metadata (codec, resolution, camera, GPS, …), and generates a primary thumbnail plus scrub-bar frames |
| Transcribe Subtitles  | Transcribes audio with Whisper (downloads the model on first run) |
| Detect Scenes     | Extracts frames and detects significant scene changes |
| Analyse Content   | Sends scene frames + subtitles to your chosen LLM for content analysis |
| Semantic Tagging       | Asks your chosen LLM to auto-tag the video |

Each video card shows five coloured dots representing the stages. The **Progress** panel at the bottom of the window shows live status and a running log.

> The first time the subtitle stage runs, Clipscape downloads the Whisper model (the default is **Large v3 Turbo**), the matching `whisper-cli` binary (~300 MB), and, if Voice Activity Detection is enabled, the small Silero VAD model (~2 MB). These are one-off downloads; later videos reuse the cached files. On machines with an NVIDIA GPU, Clipscape auto-selects the CUDA backend on first run.

Thumbnails and subtitles run without any API key. The **Analysis** and **Tags** stages only run once you've added an OpenRouter key and enabled AI features. If the key is missing or rejected, those tasks park in a **Blocked** state and resume automatically once you fix the config. You don't need to wait for everything to finish before chatting, but a video must reach at least the **Analysis** stage before chat can answer questions about it meaningfully.

You can also re-run an individual stage, re-run the whole pipeline, or kick off a one-off **Enhanced Analysis** pass (higher-resolution frames / a stronger model) from a video's detail view.

## 5. Chat with a video

1. Click the **Assistant** strip at the bottom of the window to expand the chat panel.
2. Use the **Ask about** dropdown to choose a scope:
    - **Entire library** — chat across every analyzed video
    - **Current view** — limit to the videos matching within the currently selected project
    - **Selected video** — chat only about the video you have selected in the grid
3. Type a question — e.g. *"Which videos feature outdoor scenes at sunset?"* or *"Summarise what happens in this clip"* — and press **Enter**.

The assistant answers with a short intro followed by grouped video cards. Each card shows a thumbnail, title, an optional description, and (where relevant) start/end timestamp chips. Click a card or a citation to jump straight to that video and seek to the referenced moment. Expand the **sources** drawer under an answer to see exactly which transcript, moment, entity, scene, or analysis snippets the answer was grounded in. Use **Clear** to start a fresh conversation.

That's it! you're now chatting with your video library.

## Logging Issues and Feature Requests
If you find any bugs or have a feature you think would be useful, please add an issue here in Github -> https://github.com/ceebecee/ClipScape-App/issues

