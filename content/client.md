+++
title = "Client d'API REST"
date = 2026-01-18

[taxonomies]
tags = ["python"]
+++

## Introduction

Aujourd'hui je vous propose une méthode de conception (un _blueprint_) concernant les clients d'API REST en Python.
Cela fait maintenant plusieurs fois que je dois en développer un nouveau à titre personnel ou professionnel,
et je pense avoir convergé vers une solution plutôt ergonomique, qui couvre de nombreux cas d'usage.

## 1. La classe APIClient

Pour interagir avec une API REST, il faut pouvoir effectuer des requêtes et interpréter des réponses du protocole HTTP.
En Python, de nombreuses options s'offrent à nous.
Inclus dans la bibliothèque standard, il y a urllib3, http, et sockets à disposition, mais il faut reconnaitre que leur interface publique est un peu austère.
Plus accessibles, et disponibles sur PyPi, le célébrissime requests et son rival httpx sont les têtes de file d'une compétition acharnée.

Bien que je continue à utiliser requests pour des petits scripts ou des tests interactifs dans l'invite de commande Python, je préfère httpx dans le cas de projets plus structurés : je le trouve meilleur dans la gestion des paramètres de session, et puis il gère aussi les requêtes asynchrones.

Sans plus attendre, voilà le code :

```python,linenos,name=clients.py
import httpx
import pydantic

BASE_URL: str = "https://goweather.xyz/v2/weather"


# Modèles de réponse

class Weather(pydantic.BaseModel):
   """Données météorologiques."""
   description: str
   temperature: str
   wind: str
   forecast: list["Forecast"]

   class Forecast(pydantic.BaseModel):
       """Prévisions météorologiques."""
       day: str
       temperature: str
       wind: str


# Clients

class APIClient:
   """Client d'API REST synchrone."""

   def __init__(self, **kwargs):
       self._client = httpx.Client(**kwargs)

   def get_weather(self, city: str) -> Weather:
       response = self._client.get(f"/{city}")
       response.raise_for_status()
       weather = response.json()
       return Weather.model_validate(weather)


class AsyncAPIClient:
   """Client d'API REST asynchrone."""

   def __init__(self, **kwargs):
       self._client = httpx.AsyncClient(**kwargs)

   async def get_weather(self, city: str) -> Weather:
       response = await self._client.get(f"/{city}")
       response.raise_for_status()
       weather = response.json()
       return Weather.model_validate(weather)

```

### 1.1 Usage

```python,linenos
from clients import APIClient, BASE_URL
client = APIClient(base_url=BASE_URL)
weather = client.get("Paris")
```

Tous les paramètres d'instanciation du client API sont relayés au client HTTP sous-jacent.
Le seul paramètre obligatoire est base_url, qui renseigne le préfixe à utiliser pour chaque requête,
mais il est également possible de d'ajouter des en-têtes, de l'authentification basique, ou des timeouts par exemple.

### 1.2 Fonctionnement

Chaque méthode du client d'API est associé à un couple (méthode, ressource).
Cette association donne le nom et les paramètres de la méthode. 

Une fois la requête formée, on peut l'envoyer.
Notre client httpx vérifie que le paquet est correctement routé (avec...?), que la session TCP est bien active (avec un timer interne qui expire au bout de 5 secondes par défaut), et que la réponse est au format HTTP.

La suite est du domaine métier : on vérifie que la réponse indique un succès (raise for status), que les données sont au bon format (json), et avec la bonne structure (model validate).

(Schéma du processus de validation)

On pourrait aller ici une étape plus loin en présentant les données de réponse dans un format plus adapté aux consommateurs de notre client d'API : en effet, WeatherAPI renvoie les températures sous la forme de chaînes de caractères, ce qui n'est pas pratique pour en faire des statistiques.
On peut passer par les validateurs Pydantic pour effectuer ce genre de conversion dynamique, voici le lien vers la documentation.

## 2. Une interface système

Pour exposer la classe `APIClient` au système, il faut tout d'abord créer une interface en ligne de commande, ou CLI.
Cette interface doit pouvoir interpréter les paramètres de la commande (quel methode/ressource avec quel paramètre/argument), et les variables de l'environnement d'exécution (la configuration du client HTTP en lui-même).

### 2.1 Interprétation des arguments

La première catégorie de modules sont les interpréteurs d'arguments, comme argparse, click, docopt et fire.
Comme leur nom l'indique, ils facilitent la transmission des paramètres de la commande aux composants internes du script.
Personnellement j'ai une préférence marquée pour fire, qui va un cran plus loin en systématisant la structure présentée en CLI (affichage contextuel des docstrings, chainage des paramètres, etc.)

Il suffit simplement d'ajouter à la fin du script le code suivant :

```python
if __name__ == "__main__":
   import fire
   fire.Fire(APIClient)
```

Python fire : direct sur la classe, ou depuis un objet instancié. 
Possibilité de chainer les opérations.
Combiné à Pydantic, permet de récupérer du json en stdout.
Encourage l'écriture des docstrings.

### 2.2 ingestion des variables d'environnement

Pydantic_settings et les variables d'environnement

## 3. Du script au module

Structure du projet

Le fichier pyproject.toml

Intégration continue

## Conclusion

Résumé

Méthodologie de test

Développements ultérieurs : API SOAP (xml2dict)

Lien vers le projet
