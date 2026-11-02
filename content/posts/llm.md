+++
title = "Écosystème d'inférence locale"
description = "Les LLMs sans Dario ni Sam"
date = 2026-10-08

[taxonomies]
tags = ["llm", "ops"]
+++

## Introduction

Au mois d'août 2026, j'ai rejoint chez un client une équipe de bêta-testeurs pour un service d'inférence externalisé. Concrètement, cela veut dire que j'ai reçu une clé d'API qui me permet d'interagir de façon programmatique avec des modèles de langages (les fameux LLM).

J'ai alors découvert une nouvelle façon de travailler : en effet, en connectant cette API à des logiciels appelés agents, il devient possible de déléguer une partie du travail de conception et de le rendre asynchrone. D'une certaine façon, c'est un peu comme devenir le chef de projet d'une petite équipe. C'est fascinant et déroutant à la fois !

Cela m'a donné envie de reproduire cet écosystème à la maison.

**Attention**: la configuration qui suit requiert une carte graphique avec au moins 8GB de VRAM pour être utilisable.
Même avec une telle configuration, les résultats ne seront pas à la hauteur d'un service professionnel.

## 1. Le moteur d'inférence

Pour faire tourner un LLM, de nombreuses solutions existent.
Les plus pertinentes pour un usage local sont [Ollama][1], [LMStudio][2] et [llama.cpp][3]
(notons aussi [vLLM][4], qui semble toutefois plutôt orienté production).
Toutes ces solutions sont disponibles sous la forme de conteneurs.

Chez moi, j'utilise `llama.cpp` en tant que service Docker Compose :

```yaml
services:

  llama:
    image: 'ghcr.io/ggml-org/llama.cpp:server-cuda'
    container_name: llama
    restart: unless-stopped
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

Une fois le service installé, il faut choisir un modèle.

| Modèle   | Variante | Entreprise | Date de sortie |
|---------------------|------------|---|------------|
| Qwen | `unsloth/Qwen3.5-9B-GGUF:Q6_K` | Alibaba | 2026-05-13 |
| Gemma | `unsloth/gemma-4-E4B-it-GGUF:Q6_K` | Google | 2026-07-17 |
| Granite | `ibm-granite/granite-4.2-8b-GGUF:Q6_K` | IBM | 2026-08-21 |
| LFM | `LiquidAI/LFM3-Omni-2B-GGUF:Q8_0` | LiquidAI | 2026-10-26 |

[1]: https://ollama.com/
[2]: https://lmstudio.ai/
[3]: https://llama.app/
[4]: https://vllm.ai/


## 2. L'interface web

llama-ui

## 3. L'agent

En ligne de commande : codex, clude, opencode, openhands, vibe, aider

Depuis VSCode : Continue, Cline, Claude Code, Codex, ou juste Copilat Chat en fait

## Conclusion
