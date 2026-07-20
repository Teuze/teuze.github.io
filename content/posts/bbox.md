+++
title = "Documentation de l'API Bbox"
description = "Recherches en sources ouvertes"
date = 2026-07-19

[taxonomies]
tags = ["har", "openapi", "mitmproxy"]
+++

## Introduction

Dans le cadre d'un projet d'auto-hébergement, j'ai voulu étudier l'interface d'administration
du routeur que me loue Bouygues Telecom, mon fournisseur d'accès internet FttH depuis 2021.

Je suis tombé sur [cet article](https://developer.bouyguestelecom.fr/news/router-api-summary)
dans lequel il est mentionné une documentation à une adresse qui n'existe plus.
Elle semble également absente du [catalogue](https://developer.bouyguestelecom-entreprises.fr/api-catalog).

Puisqu'il n'existe plus aucune documentation officielle, je me suis mis en tête de creuser le sujet de mon côté.

## 1. Travaux précédents

Il existe plusieurs projets GitHub faisant usage de cette API routeur Bbox.

| Projet   | Dernière activité | Stars | Langage    |
|---------------------|------------|---|------------|
| [pybbox][1]         | 2016-10-12 | 8 | Python     |
| [go-bbox][2]        | 2017-05-11 | 3 | Golang     |
| [bboxapi-router][3] | 2018-11-08 | 9 | Java       |
| [bbox][4]           | 2023-03-20 | 4 | Python     |
| [bbox-exporter][5]  | 2026-06-21 | 3 | Typescript |

[1]: https://github.com/HydrelioxGitHub/pybbox
[2]: https://github.com/n0rad/go-bbox
[3]: https://github.com/bertrandmartel/bboxapi-router
[4]: https://github.com/fanfan42/bbox
[5]: https://github.com/LockBlock-dev/bbox-exporter

Notons que le seul projet encore actif à l'heure actuelle est `bbox-exporter`.
Dans ce projet, les routes utilisées sont décrites dans un objet dénommé `bboxApiRoutes` situé dans `src/bbox/constants.ts`.

Certains autres projets disposent d'une documentation plus ou moins fournie, et plus ou moins à jour.
Cela permet de se faire une première idée sur les catégories principales de l'API et sur les mécanismes d'authentification.

## 2. Analyse d'une session Web

Pour compléter et valider cet inventaire, j'ai effectué une capture de session HTTP en utilisant l'outil Réseau de Firefox.
J'ai exporté cette capture au format HAR, et converti le fichier en utilisant le projet [`har2openapi`](https://github.com/IDDQDonly/har2openapi).

Cela m'a donné une première version de spécification OpenAPI.

## 3. Exploitation des résultats

Une fois retravaillée, cette spécification peut être servie par `swagger-ui` pour servir de documentation interactive.
Il reste toutefois quelques points à régler pour pouvoir utiliser la page en tant que client d'API en direct :

 - Contourner la politique CORS car les deux services n'ont pas la même origine
 - Changer la méthode d'authentification car `swagger-ui` ne gère pas les cookies

On peut régler ces deux problèmes à la fois en plaçant l'API routeur Bbox derrière un reverse proxy.
Cette méthode est décrite dans [le projet associé](https://github.com/Teuze/bbox).

La documentation d'API est aussi disponible [ici](/bbox/) en lecture seule.

## Conclusion

A partir de cette spécification, il devrait être possible de générer automatiquement un client
avec [OpenAPI Generator](https://github.com/OpenAPITools/openapi-generator). Cool, non ? :slightly_smiling_face:


## Références

- [Swagger Editor](https://editor.swagger.io)
- [OpenAPI Specification](https://spec.openapis.org/oas/v3.1.0.html)
