# 🎥 AI Video Assistant

A free, local-first AI meeting & video assistant that transcribes, summarizes, and lets you **chat with any YouTube video or local recording** — built as a privacy-friendly alternative to paid tools like Otter.ai or Fireflies.

Give it a YouTube URL or an audio/video file and it will:
- Transcribe it (English via local Whisper, Hindi/Hinglish via Sarvam AI)
- Generate a structured summary
- Extract action items, key decisions, and open questions
- Let you ask follow-up questions about the content via RAG
- Export the full report as PDF or TXT

---

##  Features

| Feature | Description |
|---|---|
| 🎙️ Flexible input | YouTube URL (via `yt-dlp`) or local MP4/MP3/WAV upload |
| 🗣️ Transcription | Local **OpenAI Whisper** for English; **Sarvam AI** for Hindi/Hinglish |
| 📋 Summarization | Structured bullet-point summaries via **Mistral AI** + LangChain LCEL |
| ✅ Action items | Extracted with owner and deadline where mentioned |
| 🔑 Key decisions | Auto-extracted from the transcript |
| ❓ Open questions | Surfaces unresolved follow-ups from the discussion |
| 💬 Chat with your meeting | RAG pipeline over **ChromaDB** + HuggingFace embeddings |
| 📄 Export | Download the full report as PDF or TXT |
| 🖥️ UI | Simple, interactive **Streamlit** interface |

---

## 🛠️ Tech Stack

- **Language:** Python
- **Speech-to-Text:** OpenAI Whisper (local, free), Sarvam AI (Hindi/Hinglish)
- **LLM Orchestration:** LangChain (LCEL — `RunnablePassthrough`, `RunnableLambda`)
- **LLM:** Mistral AI (free API)
- **Vector Database:** ChromaDB (persistent local store)
- **Embeddings:** HuggingFace Embeddings (local, free)
- **Audio Processing:** FFmpeg (16kHz mono-channel WAV conversion, chunking)
- **UI:** Streamlit

---

## 🏗️ Architecture

```
                ┌─────────────────┐
   YouTube URL  │                 │   Local File
  ────────────► │  audio_processor│ ◄────────────
                │  (FFmpeg + yt-dlp)
                └────────┬────────┘
                         │ 16kHz mono WAV, chunked (10 min segments)
                         ▼
                ┌─────────────────┐
                │   transcriber   │  Whisper (EN) / Sarvam AI (HI/Hinglish)
                └────────┬────────┘
                         ▼
                ┌─────────────────┐
                │   summarize /   │  Mistral AI via LangChain LCEL
                │   extractor     │  Structured Output Parsers
                └────────┬────────┘
                         ▼
          ┌──────────────┴───────────────┐
          ▼                               ▼
 ┌─────────────────┐            ┌──────────────────┐
 │  Summary /       │            │   rag_engine      │
 │  Actions /       │            │   ChromaDB +       │
 │  Decisions /     │            │   HF Embeddings     │
 │  Questions       │            │   (chat with video) │
 └─────────────────┘            └──────────────────┘
                         │
                         ▼
                 ┌───────────────┐
                 │   Streamlit    │
                 │   UI + Export  │
                 └───────────────┘
```

---

## 📂 Project Structure

```
ai-video-assistant/
├── core/
│   ├── transcriber.py      # Whisper / Sarvam AI transcription
│   ├── summarize.py        # LLM summarization + title generation
│   ├── extractor.py        # Action items, decisions, open questions
│   └── rag_engine.py       # ChromaDB + RAG chat pipeline
├── utils/
│   └── audio_processor.py  # YouTube download, FFmpeg conversion, chunking
├── main.py                 # CLI entry point
├── streamlit_app.py        # Streamlit UI
├── requirements.txt
├── .env.example
└── README.md
```

---

## ⚙️ Setup

### 1. Clone the repo
```bash
git clone https://github.com/<your-username>/ai-video-assistant.git
cd ai-video-assistant
```

### 2. Create a virtual environment
```bash
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```
Make sure **FFmpeg** is installed on your system and available on PATH.

### 4. Configure environment variables
Create a `.env` file in the project root:
```env
MISTRAL_API_KEY=your_mistral_api_key
SARVAM_API_KEY=your_sarvam_api_key
```

### 5. Run

**CLI:**
```bash
python main.py
```

**Streamlit UI:**
```bash
streamlit run streamlit_app.py
```

---

## 🚀 Usage

1. Paste a YouTube URL or upload a local audio/video file.
2. Choose the transcription language (English / Hinglish).
3. Click **Process** — the pipeline transcribes, summarizes, and extracts insights.
4. Browse the **Summary**, **Action Items**, **Decisions**, and **Questions** tabs.
5. Use the **chat panel** to ask specific questions about the meeting content.
6. Export the report as PDF or TXT.

---

## 🧩 How It Works (Pipeline Details)

1. **Audio pre-processing** — FFmpeg extracts and converts audio to 16kHz mono-channel WAV, the format Whisper expects.
2. **Chunking** — Long recordings are split into ~10-minute segments and stored in `downloads/`, so transcription scales to long meetings.
3. **Transcription** — English audio goes through local Whisper; Hindi/Hinglish audio is routed to Sarvam AI.
4. **Summarization & extraction** — Mistral AI, orchestrated with LangChain LCEL, produces a structured summary and uses Structured Output Parsers to reliably extract action items, decisions, and questions.
5. **RAG** — The transcript is embedded with HuggingFace embeddings and stored in a persistent ChromaDB (`vector_db/`) for similarity-search-based Q&A.

---

## 🗺️ Roadmap

- [ ] Speaker diarization
- [ ] Multi-file / batch processing
- [ ] Additional language support beyond Hindi/Hinglish
- [ ] Deployment guide (Docker / cloud hosting)

