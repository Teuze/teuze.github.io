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

**Attention**: la configuration qui suit requiert une carte graphique avec au moins 10GB de VRAM pour être utilisable.
Même avec une telle configuration, les résultats ne seront pas à la hauteur d'un service professionnel.

## 1. Le moteur d'inférence

Pour faire tourner un LLM, de nombreuses solutions existent.
Les plus pertinentes pour un usage local sont [Ollama][1], [LMStudio][2], [vLLM][4] et [llama.cpp][3]
Toutes ces solutions sont disponibles sous la forme de conteneurs.

Chez moi, j'utilise `llama.cpp` en tant que service Docker Compose :

```yaml
services:

  llama:
    image: 'ghcr.io/ggml-org/llama.cpp:server-cuda'
    container_name: llama
    restart: unless-stopped
    ports:
      - 8080:8080
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

Une fois le service actif (`docker compose up -d`), vous pouvez passer par l'interface web pour choisir un modèle.
Activez dans les paramètres l'option "Model Discovery" puis passez dans l'onglet "Models" pour commencer les recherches.

Voici les modèles qui m'ont semblé les plus adaptés pour ma config au moment d'écrire cet article.

| Modèle        | Variante                               | Entreprise | Date de sortie |
|---------------|----------------------------------------|------------|----------------|
| [Qwen][5]     | `unsloth/Qwen3.5-9B-GGUF:Q6_K`         | Alibaba    | 2026-05-13     |
| [Gemma][6]    | `unsloth/gemma-4-E4B-it-GGUF:Q6_K`     | Google     | 2026-07-17     |
| [Granite][7]  | `ibm-granite/granite-4.2-3b-GGUF:Q8_0` | IBM        | 2026-08-21     |
| [LFM][8]      | `LiquidAI/LFM3-Omni-2B-GGUF:Q8_0`      | LiquidAI   | 2026-10-26     |
| [Nemotron][9] | `unsloth/NVIDIA-Nemotron-3.5-Bolt-E4B-GGUF:Q6_K_XL`| NVIDIA | 2026-10-24 |

Tous ces modèles sont disponibles sur HuggingFace.

[1]: https://ollama.com/
[2]: https://lmstudio.ai/
[3]: https://llama.app/
[4]: https://vllm.ai/
[5]: https://qwen.ai/
[6]: https://deepmind.google/models/gemma/
[7]: https://www.ibm.com/granite
[8]: https://www.liquid.ai/models
[9]: https://research.nvidia.com/labs/nemotron/Nemotron-3.5-Bolt-E4B-4GB/

## 2. L'interface web

Une fois le ou les modèles téléchargés, prenez un moment pour en observer les caractéristiques :
 - **Nombre de paramètres**
 - **Échantillonnage**
 - **Modalité**
 - **Contexte**
 - **Environnement**

Vous pouvez maintenant charger un modèle et interagir avec dans la fenêtre de chat.
En plus de la réponse qui s'affiche au fur et à mesure qu'elle est calculée,
l'interface donne des statistiques sur la vitesse d'inférence notamment. 

## 3. L'agent

En plus de l'interface web, llama fournit une API compatible OpenAI et Anthropic.

C'est sur cette interface que se connectent nos agents.

| Agent     | CLI | GUI | VSCode | Licence | Note | Remarques |
|-----------|-----|-----|--------|---------|------|-----------|
| Codex     | oui | oui | oui | | 4/5 | L'extension VSCode est un peu en-dessous |
| Claude    | oui | oui | oui | | 4/5 | Très efficace mais trop intrusif |
| Cline     | oui | oui | oui | | 4/5 | L'outil CLI requiert Node.js |
| Copilot   | oui | non | oui | | 4/5 | Pas encore testé la CLI |
| Aider     | oui | non | non | | 4/5 | Full CLI |
| OpenCode  | oui | non | oui | | 3/5 | Full Node.js |
| OpenHands | oui | non | non | | 2/5 | Long à démarrer |
| Vibe      | oui | non | oui | | 2/5 | Compliqué à configurer |
| Continue  | non | non | oui | | 1/5 | Inutilisable |

D'après ce comparatif parfaitement impartial, je suggère donc Claude Code ou Codex
si vous privilégiez l'expérience utilisateur à la vie privée.
Sinon, Copilot pour VSCode et Aider pour la CLI.

Exemple de configuration Copilot pour VSCode :

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
				"contextWindow": 128000,
				"maxOutputTokens": 4000
			}
    ]
  }
]
```

Reportage photo concernant l'utilisation en pratique :

TODO

## Conclusion
