# Local Privacy-First AI Agent Stack Setup

A comprehensive guide and reference document detailing the deployment, configuration, and architectural design of a local, privacy-focused AI agent ecosystem using **Ollama**, **Open WebUI**, and custom distilled models across desktop (**Nvidia RTX 2060 SUPER 8GB**) and edge laptop hardware profiles.

## Tech Stack

**Ollama** - open-source local model manager that can serve AI access through multiple means (curl, CLI, API, etc).
**Open WebUi** - serves as a frontend for the running model, similar to a Gemini or ChatGPT interface. Installed by Snap.

## 📁 Custom Model Storage Architecture (`~/ai-models`)

By default, Ollama buries model weight blobs inside system directories (`/usr/share/ollama/.ollama/models`). I modified that to a dedicated user location: `~/ai-models`.

### Systemd Service Configuration

To ensure the Ollama system daemon loads models directly from `~/ai-models`, a systemd override was created via `sudo systemctl edit ollama.service`:

```ini
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
Environment="OLLAMA_MODELS=/home/user/ai-models"

```

Permissions and ownership were aligned with the daemon user:

```bash
sudo chown -R ollama:ollama ~/ai-models
chmod 755 /home/$USER
sudo systemctl daemon-reload
sudo systemctl restart ollama

```

---

## 🤖 Custom Agent Personas & Modelfiles

Two specialized agents were compiled into the local Ollama engine to address distinct workflows:

### 1. Linux System Administrator Agent (`Glitch Linux`)

* **Base Architecture:** `qwen2.5-coder:3b` / `7b`
* **Focus:** Bash, Zsh, kernel configuration, system troubleshooting, terminal automation.
* **Temperature:** `0.2` (Low creativity, high precision).

### 2. General Executive Assistant (`Glitch Daily`)

* **Base Architecture:** `llama3.2:3b`
* **Focus:** Reasoning, document summarization, drafting, general query assistance.
* **Temperature:** `0.7` (Balanced, natural conversational response).


## Open WebUI Setup & Network Integration

To provide a rich interface supporting document drag-and-drop (RAG), web search integrations, and model switching, **Open WebUI** was deployed alongside Ollama.

### Deployment & Configuration

1. **Snap / Container Setup:** Open WebUI was installed locally and bound to the host network.
2. **Ollama Network Bridge:** Configured `OLLAMA_HOST=0.0.0.0:11434` to enable local network socket binding between the UI container/snap and the background engine.
3. **Browser Integration:** Added a button to take me to `http://localhost:3000`.

---

## 🚀 Summary & Future Roadmap

* **Complete Privacy:** Zero external API dependencies or logging.
* **Storage Optimization:** Shared weight blobs managed through OCI-style manifest layers.
* **Multi-Device Support:** Shared agent personas exportable via Modelfiles across edge laptop and desktop environments.

```

```
