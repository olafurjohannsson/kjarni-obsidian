# Kjarni - Related Notes & Search

Find related notes as you write. Search your vault by meaning, not just keywords.

Everything runs locally on your machine. No API key. No cloud. No account. Your notes never leave your device.

## What it does

**Similar Notes sidebar** - Open any note and the sidebar shows which other notes are related. Scores update as you switch between notes. Useful for discovering connections you forgot about.

**Semantic search** - Press `Cmd+Shift+K` to search your vault. Finds notes by meaning, so searching for "how to manage memory" finds your note about Rust ownership even if it never uses the word "manage."

**Reranker** - After the initial search, a cross-encoder reads your query against each result to refine the ranking. You see results immediately, then they quietly re-sort as the reranker finishes.

## How it works

The plugin runs a transformer model (MiniLM-L6-v2) compiled to WebAssembly directly inside Obsidian. Your notes are split into chunks, encoded into vectors, and stored in a local index file. Search and similarity use a combination of BM25 keyword matching and semantic vector similarity.

Indexing runs in parallel background workers. Obsidian never freezes while the plugin is working.

The index is saved to disk and only changed files are re-indexed on startup. You don't re-embed your entire vault every time you open Obsidian.

## Install

### With BRAT (recommended for now)

1. Install the [BRAT plugin](https://github.com/TfTHacker/obsidian42-brat)
2. In BRAT settings, click "Add Beta Plugin"
3. Enter: `olafurjohannsson/kjarni-obsidian`
4. Enable "Kjarni" in Settings > Community plugins

### Manual

1. Download the latest release from [GitHub Releases](https://github.com/olafurjohannsson/kjarni-obsidian/releases)
2. Extract to `your-vault/.obsidian/plugins/kjarni-search/`
3. Enable "Kjarni" in Settings > Community plugins

Models download automatically on first launch (~22 MB for the encoder, ~22 MB for the reranker).

## Usage

**Similar Notes:** Open the command palette (`Cmd+P`) and run "Kjarni: Show similar notes." The sidebar opens on the right and updates as you navigate between notes.

**Search:** Press `Cmd+Shift+K` or click the search icon in the ribbon. Start typing and results appear instantly.

**Reindex:** If something seems off, open the command palette and run "Kjarni: Reindex vault" to rebuild from scratch.

## Settings

- **Search results limit** - How many results to show (5-30)
- **Reranker** - Toggle cross-encoder reranking on or off
- **Chunk size** - Characters per chunk when indexing (default 1000)
- **Debug logging** - Logs timing info to the developer console

## Performance

Indexing speed depends on vault size and your hardware. Rough numbers on a modern laptop:

- 100 notes: ~5 seconds
- 1,000 notes: ~30 seconds
- 5,000 notes: ~2 minutes

Search takes 15-30ms. Reranking adds 200-1500ms depending on result count.

After first indexing, only changed files are re-indexed. Opening Obsidian with an existing index takes less than a second.

## Privacy

No network requests except the one-time model download from kjarni.ai on first launch. After that, everything is local. No telemetry, no analytics, no accounts.

The models are standard open-source sentence transformers (all-MiniLM-L6-v2 for encoding, ms-marco-MiniLM-L-6-v2 for reranking). The WASM binary is compiled from the Kjarni inference engine source.

## Technical details

- Inference engine: [Kjarni](https://github.com/olafurjohannsson/kjarni) (Rust, compiled to WebAssembly with SIMD)
- Embedding model: all-MiniLM-L6-v2 (384 dimensions, quantized to ~22 MB)
- Reranker model: ms-marco-MiniLM-L-6-v2 cross-encoder (quantized to ~22 MB)
- Search: hybrid BM25 + cosine similarity
- Indexing: parallel Web Workers, incremental updates, persisted to disk
- Platform: Desktop only (requires Web Workers and WASM SIMD)

## License

MIT

## Links

- [Kjarni](https://kjarni.ai) - The inference engine behind this plugin
- [GitHub](https://github.com/olafurjohannsson/kjarni) - Kjarni source code
- [Report an issue](https://github.com/olafurjohannsson/kjarni-obsidian/issues)
