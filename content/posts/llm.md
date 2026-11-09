+++
title = "Écosystème d'inférence locale"
description = "Les LLM sans Dario ni Sam"
date = 2026-11-08

[taxonomies]
tags = ["llm", "ops"]
+++

## Introduction

Au mois d'août 2026, j'ai rejoint chez un client une équipe de bêta-testeurs pour un service d'inférence externalisé.
Concrètement, cela veut dire que j'ai reçu une clé d'API qui me permet d'interagir de façon programmatique
avec des modèles de langages (les fameux LLM).

J'ai alors découvert une nouvelle façon de travailler : en effet, en connectant cette API à des logiciels appelés agents,
il devient possible de déléguer une partie du travail de conception et de le rendre asynchrone.
D'une certaine façon, c'est un peu comme devenir le chef de projet d'une petite équipe.
C'est fascinant et déroutant à la fois !

Cela m'a donné envie de reproduire cet écosystème à la maison.

<figure>
	<img src="/llm/placeholder.png" alt="Change me">
	<figcaption>Caractéristiques de mon poste de travail</figcaption>
</figure>

**Attention**: la configuration qui suit requiert une carte graphique avec au moins 10GB de VRAM pour être utilisable.
Même avec une telle configuration, les résultats ne seront pas à la hauteur d'un service professionnel.

## 1. Le coeur : service d'inférence

Pour faire tourner un LLM, de nombreuses solutions existent.
Les plus pertinentes pour un usage local sont [Ollama][1], [LM Studio][2], [vLLM][4] et [llama.cpp][3].
Elles ont toutes leurs atouts : Ollama dispose d'une très grande communauté, LM Studio propose une interface de bureau,
vLLM est orienté production, et llama.cpp est léger et configurable.

Chez moi, j'utilise donc `llama.cpp` en tant que service Docker Compose :

```yaml
services:

  llama:
    image: 'ghcr.io/ggml-org/llama.cpp:server-cuda'
    container_name: llama
    restart: unless-stopped
    ports:
      - 8080:8080
    environment:
      LLAMA_CACHE: /models
    volumes:
      - llama-data:/models
    deploy:
      resources:
        reservations:
          devices:
            - capabilities: ["gpu"]
              driver: nvidia
              count: all

volumes:
  llama-data:
```

Si vous n'avez pas de carte graphique, remplacez le tag `server-cuda` par `server` et retirez la section `deploy`.

Une fois le service actif (`docker compose up -d`), passez par l'interface web (`http://localhost:8080`) pour choisir un modèle.
Activez dans les paramètres l'option « Model Discovery », puis rendez-vous dans l'onglet « Models » pour lancer les recherches.

## 2. Le cerveau : modèles

Voici ceux qui m'ont semblé les plus adaptés au moment d'écrire cet article :

| Modèle        | Variante                                             | Entreprise | Date de sortie |
|---------------|------------------------------------------------------|------------|----------------|
| [Qwen][5]     | `unsloth/Qwen3.5-9B-GGUF:Q6_K`                       | Alibaba    | 2026-05-13     |
| [Gemma][6]    | `unsloth/gemma-4-E4B-it-GGUF:Q6_K`                   | Google     | 2026-07-17     |
| [Granite][7]  | `ibm-granite/granite-4.2-3b-GGUF:Q8_0`               | IBM        | 2026-08-21     |
| [LFM][8]      | `LiquidAI/LFM3-Omni-2B-GGUF:Q8_0`                    | LiquidAI   | 2026-10-26     |
| [Nemotron][9] | `unsloth/NVIDIA-Nemotron-3.5-Bolt-E4B-GGUF:Q6_K_XL`  | NVIDIA     | 2026-10-24     |
| [Mellum][0]   | `JetBrains/Mellum2.1-12B-A2.5B-Thinking-GGUF:Q4_K_M` | JetBrains  | 2026-09-20     | 

Tous ces modèles sont disponibles sur [HuggingFace](https://hf.co).

[1]: https://ollama.com/
[2]: https://lmstudio.ai/
[3]: https://llama.app/
[4]: https://vllm.ai/
[5]: https://qwen.ai/
[6]: https://deepmind.google/models/gemma/
[7]: https://www.ibm.com/granite
[8]: https://www.liquid.ai/models
[9]: https://research.nvidia.com/labs/nemotron/Nemotron-3.5-Bolt-E4B-4GB/
[0]: https://www.jetbrains.com/mellum/

La variante se lit `editeur/modele-version-taille-format:quantisation`.

- **Éditeur** : L'entité qui a publié le modèle sur HuggingFace.
- **Modèle** : Le nom du modèle.
- **Version** : La version du modèle.
- **Taille** : Le nombre de paramètres en millions (M) ou milliards (B).
- **Format** : Pour `llama.cpp`, le format est forcément [GGUF](https://en.wikipedia.org/wiki/GGUF).
- **Quantisation** : Compression des paramètres pour accélérer l'inférence.
- **Échantillonnage** : Les variables qui contrôlent le caractère plus ou moins déterministe des réponses (`temperature`, `top-p`, `top-k`).
- **Modalité** : Les capacités du modèle (images, audio, réflexion, outillage).
- **Contexte** : La mémoire du modèle pour une conversation donnée.
- **Template** (gabarit) : Le format attendu pour la réponse du LLM.

<figure>
    <div class="llama-webui">
        <img src="/llm/llama/models.png" style="width: 100%" alt="Modèles disponibles en local">
        <img src="/llm/llama/discover.png" style="width: 100%" alt="Téléchargement de modèles">
        <img src="/llm/llama/loaded-model.png" style="width: 100%" alt="Paramètres du modèle chargé">
        <img src="/llm/llama/chat.png" style="width: 100%" alt="Discussion">
        <img src="/llm/llama/statistics.png" style="width: 100%" alt="Statistiques">
    </div>
    <figcaption>Interface web de llama.cpp</figcaption>
</figure>

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@nikba/easy-carousel/dist/easy-carousel.min.css">
<script src="https://cdn.jsdelivr.net/npm/@nikba/easy-carousel/dist/easy-carousel.min.js"></script>
<script>
    const carouselParams = {items: 1, loop: true, nav: true, dots: false}
    const alignments = new EasyCarousel('.llama-webui', carouselParams);
</script>

| Modèle        | Taille sur disque | Taille en RAM | Contexte  | Débit |
|---------------|-------------------|---------------|-----------|-------|
| Qwen          | 7.9GB | ??? | 80k/262k  | ??? |
| Gemma         | 7.7GB | ??? | 131k/131k | ??? |
| Granite       | 3.7GB | ??? | 80k/131k  | ??? |
| LFM           | 2.2GB | ??? | 128k/128k | ??? |
| Nemotron      | 7.7GB | ??? | 131k/131k | ??? |
| Mellum        | 9.2GB | ??? | ??k/131k  | ??? |

## 3. Les mains: agents

En plus de l'interface web, llama.cpp fournit une [API](https://llama.app/docs/api) sur laquelle se connectent nos agents.

| Agent           | CLI | GUI | VSCode | Note | Remarques |
|-----------------|-----|-----|--------|------|-----------|
| [Codex][10]     | oui | oui | oui    | 4/5  | L'extension VSCode est un peu en-dessous |
| [Claude][11]    | oui | oui | oui    | 4/5  | Très efficace mais trop intrusif |
| [Cline][12]     | oui | oui | oui    | 4/5  | L'outil CLI requiert Node.js |
| [Copilot][13]   | oui | non | oui    | 4/5  | Pas encore testé la CLI |
| [Aider][14]     | oui | non | non    | 4/5  | Full CLI |
| [OpenCode][15]  | oui | non | oui    | 3/5  | Full Node.js |
| [OpenHands][16] | oui | non | non    | 2/5  | Long à démarrer |
| [Vibe][17]      | oui | non | oui    | 2/5  | Compliqué à configurer |
| [Continue][18]  | non | non | oui    | 1/5  | Inutilisable |

[10]: https://openai.com/codex/
[11]: https://code.claude.com
[12]: https://cline.bot
[13]: https://code.visualstudio.com/blogs/2026/06/18/byok-vscode
[14]: https://aider.chat
[15]: https://opencode.ai
[16]: https://www.openhands.dev/
[17]: https://mistral.ai/products/vibe/
[18]: https://continue.dev

D'après ce comparatif parfaitement impartial, je suggère donc Claude Code ou Codex
si vous privilégiez l'expérience utilisateur à la vie privée.
Sinon, Copilot pour VSCode et Aider pour la CLI.

### 3.1 En ligne de commande

**Claude Code** (via l'API compatible Anthropic) :

```bash
export ANTHROPIC_BASE_URL="http://localhost:8080"
export ANTHROPIC_AUTH_TOKEN="local"
export ANTHROPIC_MODEL="unsloth/Qwen3.5-9B-GGUF:Q6_K"
claude
```

**Codex** (`~/.codex/config.toml`) :

```toml
model = "unsloth/Qwen3.5-9B-GGUF:Q6_K"
model_provider = "llama"

[model_providers.llama]
name = "llama.cpp"
base_url = "http://localhost:8080/v1"
```

**Aider :**

```bash
aider --model openai/unsloth/Qwen3.5-9B-GGUF:Q6_K \
      --openai-api-base http://localhost:8080/v1 \
      --openai-api-key local
```

### 3.2 Depuis Visual Studio Code

```json
[
  {
    "name": "Inférence locale",
    "vendor": "customendpoint",
    "apiKey": "${input:chat.lm.secret.206f505}",
    "apiType": "chat-completions",
    "models": [
      {
        "id": "LiquidAI/LFM3-Omni-2B-GGUF:Q8_0",
        "name": "lfm-3",
        "url": "http://localhost:8080/v1/chat/completions",
        "toolCalling": true,
        "vision": true,
        "contextWindow": 32768,
        "maxOutputTokens": 4000
      }
    ]
  }
]
```

<figure>
	<img src="/llm/messages.png" alt="Messages">
	<figcaption>Déroulé de la conversation</figcaption>
</figure>

<br/>

<figure>
	<img src="/llm/session.png" alt="Session">
	<figcaption>Statistiques sur le contexte</figcaption>
</figure>

<br/>

<figure>
	<img src="/llm/models.png" alt="Modèles">
	<figcaption>Caractéristiques des modèles</figcaption>
</figure>

## Conclusion
