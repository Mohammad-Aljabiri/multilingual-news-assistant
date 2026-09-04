# Multilingual News Assistant

A conversational AI assistant that translates Arabic news articles into English and summarizes them. It uses a hybrid architecture: a hosted LLM (Gemini, via the OpenAI-compatible API) handles orchestration and tool-calling decisions, while local open-source Hugging Face models do the actual translation and summarization work.

## How it works

- The user sends news text (or a general question) through a Gradio chat interface.
- Gemini decides, based on the input language, what needs to happen:
  - **Arabic input** → the `translator` tool runs first ([Helsinki-NLP/opus-mt-ar-en](https://huggingface.co/Helsinki-NLP/opus-mt-ar-en)), then its English output is passed to the `summarizer` tool.
  - **English input** → the `summarizer` tool ([sshleifer/distilbart-cnn-12-6](https://huggingface.co/sshleifer/distilbart-cnn-12-6)) runs directly.
  - **A conversational question** → Gemini answers directly, without calling any tool.
- The LLM never translates or summarizes using its own internal knowledge — that work is always delegated to the local models, keeping output deterministic and inference cheap.
- All responses to the user are in English by design, regardless of the source language.

## Tech stack

- **Gemini API** (OpenAI-compatible endpoint) — orchestration and tool-calling
- **Hugging Face `transformers`** — local translation and summarization models
- **Gradio** — chat interface

## Setup

1. Clone the repo:
   ```bash
   git clone https://github.com/Mohammad-Aljabiri/multilingual-news-assistant.git
   cd <repo-name>
   ```

2. Create and activate a virtual environment:
   ```bash
   python -m venv .venv
   .venv\Scripts\activate      # Windows
   source .venv/bin/activate   # macOS/Linux
   ```

3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

4. Copy `.env.example` to `.env` and fill in your own keys:
   ```
   GOOGLE_API_KEY=
   HF_TOKEN=
   ```
   - `GOOGLE_API_KEY`: from [Google AI Studio](https://aistudio.google.com/)
   - `HF_TOKEN`: from your [Hugging Face account settings](https://huggingface.co/settings/tokens)

5. Open `multilingual_news_assistant.ipynb` and run all cells. The final cell launches a Gradio chat window in your browser.

## Notes

- Local models download automatically on first run (a few hundred MB total).
- A GPU is not required — both models run fine on CPU, just slower.