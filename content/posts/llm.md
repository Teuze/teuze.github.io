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

## 1. Le moteur d'inférence

Pour faire tourner un LLM, de nombreuses solutions existent.
Les plus pertinentes pour un usage local sont [Ollama][1], [LMStudio][2] et [llama.cpp][3]
(notons aussi [vLLM][4], qui semble toutefois plutôt orienté production).
Toutes ces solutions sont disponibles sous la forme de conteneurs.

Chez moi, j'utilise Ollama en tant que service podman :

Une fois le service installé, il faut choisir un modèle.

| Modèle   | Variante | Entreprise | Date de sortie |
|---------------------|------------|---|------------|
| qwen3.5 | | | |
| gemma4 | | | |
| granite4 | | | |
| phi4-mini | | | |
| ministral-3 | | | |

[1]: https://ollama.com/
[2]: https://lmstudio.ai/
[3]: https://llama.app/
[4]: https://vllm.ai/

Ollama 64k context + qwen3.5

## 2. L'interface web

open-webui

## 3. L'agent

codex ou claude code

## Conclusion
