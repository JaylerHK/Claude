# Local LLM Setup Guide for MacBook Air M5

## 1. Current Setup Assessment

### Your Current Architecture
```
Claude Code (MacBook Air)
        │
        ▼
    LiteLLM
    ┌────┴────┬─────────────┐
    ▼         ▼             ▼
GLM-5     Qwen3.5-plus   M2.5
(Nvidia   (AlibabaCloud  (MiniMax VPS)
 Cloud)    SAS)
```

### Assessment: Is This Optimal?

**Current Routing:**
| Model | Provider | Purpose | Status |
|-------|----------|---------|--------|
| GLM-5 | Nvidia Cloud | Primary | ✅ Good |
| Qwen3.5-plus | AlibabaCloud SAS | Backup/Failover | ✅ Good |
| M2.5 | MiniMax VPS (me) | Tertiary | ✅ Good |

**Question:** Why route through LiteLLM if you already have external VPS?

**Potential Improvement:**
- If GLM-5 on Nvidia is fast/reliable → use directly without LiteLLM overhead
- LiteLLM adds latency (~50-100ms per request)
- Consider: Claude Code → Direct API calls to GLM-5 (if supported)

---

## 2. Adding Local GLM-4.7 Flash

### Should You Add Local GLM-4.7?

**Pros:**
- ✅ Offline capability (no internet needed)
- ✅ No API costs (after model download)
- ✅ Fast for simple tasks (40-64 tok/s)
- ✅ Best agent capability in 9GB class

**Cons:**
- ❌ 16GB RAM is tight (~9GB for model + ~4GB for macOS = borderline)
- ❌ Weaker than GLM-5 (744B vs 30B)
- ❌ Slightly complex setup

**Recommendation:** Add as **fallback** for offline/simple tasks, keep GLM-5 as primary.

---

## 3. LMStudio vs Ollama

### Comparison

| Feature | LMStudio | Ollama |
|---------|----------|--------|
| **Ease of Use** | ⭐⭐⭐⭐⭐ GUI + CLI | ⭐⭐⭐⭐ CLI-first |
| **GLM-4.7 Support** | ✅ Native | ✅ Native |
| **Server Mode** | ✅ Built-in | ✅ Built-in |
| **Model Management** | ⭐⭐⭐⭐⭐ Visual | ⭐⭐⭐ Basic |
| **Memory Efficiency** | ⭐⭐⭐⭐ Good | ⭐⭐⭐⭐ Good |
| **Context Length** | 128K | 32K-128K (varies) |
| **GPU Offload** | ✅ Auto | ✅ Auto |

### 🎯 Winner: **LMStudio**

**Why LMStudio is better for you:**
1. **GUI** — Visual model management, easier to switch models
2. **Built-in server** — One-click "Start Server" on port 1234
3. **Better context** — Supports 128K for GLM-4.7
4. **Auto GPU offload** — Optimizes for M5 Neural Engine

---

## 4. Step-by-Step Installation Guide

### Prerequisites
- [ ] macOS 14+ (Sonoma or later)
- [ ] 16GB RAM (minimum 8GB free recommended)
- [ ] ~10GB free disk space

### Option A: LMStudio (Recommended)

#### Step 1: Download LM Studio
```bash
# Option 1: Direct download
# Visit: https://lmstudio.ai/

# Option 2: Homebrew (if installed)
brew install lmstudio
```

#### Step 2: Launch LM Studio
```bash
# Open from Applications
open -a "LM Studio"
```

#### Step 3: Download GLM-4.7 Flash Model
1. In LM Studio, click **🔍 Discover** (left sidebar)
2. Search: `glm-4.7-flash`
3. Select **GLM-4.7-Flash** (by zai-org or lmstudio-community)
4. Choose quantization: **8-bit** (recommended for your 16GB)
   - ⚠️ Avoid 4-bit MLX (~17GB, won't fit)
   - ✅ 8-bit GGUF: ~9GB
5. Click **Download** ⬇️
6. Wait for download (~5-10 minutes, ~9GB)

#### Step 4: Load the Model
1. After download, click **👈 My Models** (left sidebar)
2. Click on **GLM-4.7-Flash-8bit** or similar
3. Wait for "Loaded" status (shows in top bar)

#### Step 5: Start Local Server
1. Click **💻 Server** (left sidebar)
2. Configure:
   - **Port**: `1234` (default)
   - **Host**: `0.0.0.0`
   - **Model**: GLM-4.7-Flash (should auto-select)
3. Click **Start Server** ▶️
4. Verify: Visit `http://localhost:1234/v1/models` — should return model info

#### Step 6: Connect Claude Code (Optional)

**Method 1: LiteLLM Config**
```bash
# Add to LiteLLM config
model_list:
  - model_name: glm-4.7-local
    litellm_params:
      model: openai/glm-4.7-flash
      api_base: http://localhost:1234/v1
      api_key: "dummy-key"
```

**Method 2: Direct in Claude Code**
```json
// In Claude Code config
{
  "models": {
    "providers": {
      "local": {
        "baseUrl": "http://localhost:1234/v1",
        "apiKey": "dummy-key",
        "api": "openai-completions",
        "models": [{
          "id": "glm-4.7-flash",
          "name": "GLM-4.7 Flash Local",
          "contextWindow": 128000
        }]
      }
    }
  }
}
```

---

### Option B: Ollama (Alternative)

#### Step 1: Install Ollama
```bash
# macOS
curl -fsSL https://ollama.com/install.sh | sh
```

#### Step 2: Download Model
```bash
# Note: Ollama may not have GLM-4.7 Flash official build
# Check available: ollama list

# If available:
ollama pull glm-4.7-flash

# Alternative: Use llama.cpp directly
```

#### Step 3: Start Server
```bash
# Start in server mode
OLLAMA_HOST=0.0.0.0:1234 ollama serve
```

---

### Option C: llama.cpp (CLI, Most Control)

#### Step 1: Install llama.cpp
```bash
# Clone and build
git clone https://github.com/ggerganov/llama.cpp.git
cd llama.cpp
make -j$(nproc)

# Or use Homebrew
brew install llama.cpp
```

#### Step 2: Download GLM-4.7 Flash GGUF
```bash
# Download 8-bit GGUF from HuggingFace
# https://huggingface.co/lmstudio-community/GLM-4.7-Flash-MLX-8bit
# OR use GGUF version

# Example with wget
wget https://huggingface.co/lmstudio-community/GLM-4.7-Flash-MLX-8bit/resolve/main/glm-4.7-flash-8bit.gguf
```

#### Step 3: Run Server
```bash
# Start OpenAI-compatible server
./server -m glm-4.7-flash-8bit.gguf \
  -c 32768 \
  --host 0.0.0.0 \
  --port 1234 \
  -ngl 999  # Use all GPU cores
```

---

## 5. Recommended Final Setup

### Optimal Configuration

```
┌──────────────────────────────────────────────────────────────────┐
│                    MacBook Air M5 (16GB)                        │
├──────────────────────────────────────────────────────────────────┤
│  Claude Code                                                      │
│       │                                                           │
│       ▼                                                           │
│  LiteLLM (Router)                                                 │
│       │                                                           │
│  ┌─────┴─────┬──────────────┬──────────────┐                    │
│  ▼           ▼              ▼              ▼                     │
│ GLM-4.7    GLM-5         Qwen3.5       M2.5                    │
│ (Local)    (Nvidia)      (Alibaba)     (MiniMax)              │
│ Fallback   Primary        Backup        Backup                  │
└──────────────────────────────────────────────────────────────────┘
```

### LiteLLM Config

```yaml
model_list:
  # Primary - GLM-5 on Nvidia Cloud
  - model_name: glm-5
    litellm_params:
      model: glm-4-5/GLM-5
      api_key: ${GLM_API_KEY}
      api_base: https://api.example.com/v1

  # Local Fallback - GLM-4.7 Flash
  - model_name: glm-4.7-local
    litellm_params:
      model: openai/glm-4.7-flash
      api_base: http://localhost:1234/v1
      api_key: "local"

  # Backup - Qwen3.5-plus
  - model_name: qwen3.5
    litellm_params:
      model: qwen/Qwen-3.5-Plus
      api_key: ${QWEN_API_KEY}

  # Backup - M2.5
  - model_name: m2.5
    litellm_params:
      model: minimax/M2.2
      api_key: ${MINIMAX_API_KEY}
```

---

## 6. Troubleshooting

### Issue: Model Won't Load (Out of Memory)
**Solution:**
1. Close other apps (Chrome, Safari tabs)
2. Use 4-bit quantization (if available) - but ~17GB MLX won't fit
3. Use llama.cpp with aggressive GPU offload

### Issue: Slow Inference
**Solution:**
1. Check GPU usage: Activity Monitor → GPU
2. Reduce context length (--ctx 8192)
3. Switch to 4-bit if using 8-bit

### Issue: Server Not Starting
**Solution:**
```bash
# Check port availability
lsof -i :1234

# Kill existing process
kill -9 <PID>
```

---

## 7. Summary

| Task | Tool | Command |
|------|------|---------|
| Install | LMStudio | Download from https://lmstudio.ai |
| Download Model | LMStudio GUI | Search "glm-4.7-flash", download 8-bit |
| Start Server | LMStudio GUI | Click Server → Start |
| Test | curl | `curl http://localhost:1234/v1/models` |
| Connect Claude | LiteLLM | Add to config.yaml |

---

**End of Document**
