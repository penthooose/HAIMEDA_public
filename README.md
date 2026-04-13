# HAIMEDA (Anonymized)

This repository contains an anonymized version of **HAIMEDA** (Hybrid AI for Medical Device Assessment), an expert system for creating medical device damage reports.

The interface and core workflow are in German language, since this system was customized for expert use in Germany.

Because sensitive project data, custom environment setup, and fine-tuned models were removed, some functionality may be limited out of the box.

## Documentation

- `HAIMEDA_documentation.md` — functional/system documentation
- `HAIMEDA_technical_documentation.pdf` — fine-tuning and technical system notes

## Prerequisites

To run the app locally, you need:

- **Elixir** `~> 1.14` (see `mix.exs`)
- A compatible **Erlang/OTP** version
- **Node.js + npm** (for frontend package installation)
- **MongoDB**
- **Ollama** (local or remote, depending on your configuration)

## Configuration you should review first

Most runtime behavior is configured in:

- `config/application_properties.yaml`

Important keys to check before first run:

- `General.local_OS` (`Win` or `LX`)
- `LLMs.use_remote_ollama_models`
- `LLMs.ollama_server_url`
- `LLMs.path_to_local_LLM_models`
- `RAG.mdb_files_path`
- `RAG.path_to_parent_folder_for_RAG_files`
- `RAG.model_name_general` / `RAG.model_name_rag`
- `LLM_Config.*.model_name`

> **Important:** model names in YAML must match models that actually exist in Ollama (local or remote).

MongoDB connection defaults are configured in:

- `config/config.exs` → `mongodb://localhost:27017/haimeda_db`

If your MongoDB runs elsewhere, update this value.

## Ollama setup (required for AI features)

You have two supported modes:

### 1) Local Ollama + local GGUF model files

Use this if `LLMs.use_remote_ollama_models: false`.

1. Install Ollama and make sure the `ollama` CLI is available in your `PATH`.
2. Ensure Ollama is running and reachable (default: `http://localhost:11434`).
3. Place your `.gguf` model files in the folder configured by `LLMs.path_to_local_LLM_models`.
4. Keep model names in `application_properties.yaml` aligned with available/integrated models.

On startup, HAIMEDA scans that folder and integrates found `.gguf` files into Ollama.

### 2) Remote Ollama server

Use this if `LLMs.use_remote_ollama_models: true`.

1. Set `LLMs.ollama_server_url` to your remote Ollama endpoint.
2. Set `LLMs.remote_models` to model names available on that server.
3. Ensure all configured models (`RAG.*` and `LLM_Config.*`) exist on the remote server.

## Local setup and run

Run all commands from the **repository root**.

1. Install frontend dependencies:

   `cd assets && npm install`

2. Return to project root and install/build Elixir + assets dependencies:

   `mix setup`

3. Start Phoenix:

   `mix phx.server`

   (or `iex -S mix phx.server`)


## Notes / Troubleshooting

- The app expects `config/application_properties.yaml` to be present and readable.
- The UI and messages are in German language, since this is a customized expert system for use in Germany.
- For first-time runs, model integration can take a while.
- If no local GGUF files are found in `LLMs.path_to_local_LLM_models`, LLM features may remain unavailable.
- RAG-related paths should exist and be valid for your system if you enable local file-based workflows.
