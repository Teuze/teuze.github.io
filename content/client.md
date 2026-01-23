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

<figure>
    <!-- NOTE: Figure is only appropriate with full-width content -->
	<img src="/crussol.jpg" alt="Crussol">
	<figcaption>Discussions à ce sujet au château de Crussol</figcaption>
</figure>

## La classe APIClient

Pour interagir avec une API REST, il nous faut pouvoir effectuer des requêtes et interpréter des réponses du protocole HTTP. En Python, de nombreuses options s'offrent à nous.
Inclus dans la bibliothèque standard, il y a urllib3, http, et sockets à disposition, mais il faut reconnaitre que leur interface publique est un peu austère. Plus accessibles, et disponibles sur PyPi, le célébrissime requests et son rival httpx sont les têtes de file d'une compétition acharnée.

Bien que je continue à utiliser requests pour des petits scripts ou des tests interactifs dans l'invite de commande Python, je préfère httpx dans le cas de projets plus structurés : je le trouve meilleur dans la gestion des paramètres de session, et puis il gère aussi les requêtes asynchrones.

Sans plus attendre, voilà le code :

```python,linenos,name=clients.py
import httpx
import pydantic

BASE_URL: str = "https://goweather.xyz/v2/weather"


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

La méthodologie est la suivante :
 
- les paramètres d'instanciation de la classe APIClient sont transmis au client HTTP sous-jacent (notamment l'url de base et l'authentification)

 - Chaque méthode qui sollicite le client HTTP inclut dans son nom la méthode et la ressource accédée. Si la ressource est un verbe (par exemple `/search`) on peut éventuellement l'utiliser sans la méthode si ça favorise la lisibilité.

 - Les paramètres de la méthode sont typées et incluent rarement de valeurs par défaut: on laisse ça aux options de configuration.

 - Une fois la requête effectuée, on valide la réponse au niveau réseau (`raise_for_status`) et données (`json` puis `model_validate`)

On peut directement utiliser l'un ou l'autre de ces clients depuis Python :

```python,linenos
from clients import APIClient, BASE_URL
client = APIClient(base_url=BASE_URL)
weather = client.get("Paris")
```

Comment faire pour rendre la fonctionnalité disponible au reste du système ?

## Utilisation depuis le shell


Python fire : direct sur la classe, ou depuis un objet instancié. Possibilité de chainer les opérations. Combiné à Pydantic, permet de récupérer du json en stdout. Encourage l'écriture des docstrings

## Du projet au module

Pydantic_settings et les variables d'environnement

Le fichier pyproject.toml

Intégration continue

## Méthodologie de test

Mockup du serveur

Variabilisation

## Conclusion

Résumé

Développements ultérieurs : API SOAP (xml2dict)

Lien vers le projet