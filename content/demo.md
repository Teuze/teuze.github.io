+++
title = "Typographie"
date = 2026-01-01
updated = 2026-01-09

[taxonomies]
tags = ["markdown"]
+++

Cet article sert d'exemple pour l'affichage d'éléments.<br/>
Il est fortement inspiré de l'article de démo du thème [terminus](https://eyalkalderon.com/terminus/).

## Titres

# H1

## H2

### H3

#### H4

##### H5

###### H6

## Paragraphes

Xerum, quo qui aut unt expliquam qui dolut labo. Aque venitatiusda cum,
voluptionse latur sitiae dolessi aut parist aut dollo enim qui voluptate ma
dolestendit peritin re plis aut quas inctum laceat est volestemque commosa as
cus endigna tectur, offic to cor sequas etum rerum idem sintibus eiur? Quianimin
porecus evelectur, cum que nis nust voloribus ratem aut omnimi, sitatur?
Quiatem. Nam, omnis sum am facea corem alique molestrunt et eos evelece arcillit
ut aut eos eos nus, sin conecerem erum fuga. Ri oditatquam, ad quibus unda
veliamenimin cusam et facea ipsamus es exerum sitate dolores editium rerore
eost, temped molorro ratiae volorro te reribus dolorer sperchicium faceata
tiustia prat.

Itatur? Quiatae cullecum rem ent aut odis in re eossequodi nonsequ idebis ne
sapicia is sinveli squiatum, core et que aut hariosam ex eat.

## Figures

![Python Logo](https://upload.wikimedia.org/wikipedia/commons/c/c3/Python-logo-notext.svg)

<figure>
    <!-- NOTE: Figure is only appropriate with full-width content -->
	<img src="/delacroix.jpg" alt="Bannière">
	<figcaption>La Liberté guidant le Peuple</figcaption>
</figure>

## Citations

> Ce qui se conçoit bien s'énonce clairement,
> et les mots pour le dire viennent aisément.
>
> — <cite>Nicolas Boileau</cite>


## Boutons et liens

<button>Bouton</button> [Lien](#)

## Tables

| Italique   | Gras     | Code   |
| ---------- | -------- | ------ |
| _italique_ | **gras** | `code` |

## Listes

1. Cochable
    - [x] Cochée
    - [ ] Décochée
2. Non-ordonnée
    - Pomme
    - Orange
    - Banane
3. Ordonnée
    1. Sucre
    2. Farine

## Blocs

```python,linenos,hl_lines=4
def factorielle(n: int) -> int:
    """
    Calcul récursif de factorielles d'entiers positifs
    Source: https://github.com/TheAlgorithms/Python
    """
    if not isinstance(n, int):
        raise ValueError("N'accepte que des nombres entiers.")
    if n < 0:
        raise ValueError("N'accepte que des nombres positifs.")
    return 1 if n in {0, 1} else n * factorielle(n - 1)
```

## Formulaires

<fieldset>
	<input type="text" placeholder="Champ textuel"/><br/>
	<input type="number" placeholder="Champ numérique"/><br/>
	<input type="text" value="Valeur par défaut"/><br/>
	<select>
		<option value="1">Option 1</option>
		<option value="2">Option 2</option>
		<option value="3">Option 3</option>
	</select><br/>
	<textarea placeholder="Champ libre..."></textarea><br/>
	<label><input type="checkbox"/> D'accord <br/></label>
	<button type="submit">Envoyer</button>
</fieldset>


## Autres éléments

C<sub>2</sub>H<sub>6</sub>O <=> H<sub>2</sub>O 

X<sup>n</sup> + Y<sup>n</sup> = Z<sup>n</sup>

<abbr title="Graphics Interchange Format">GIF</abbr> est un format d'image matriciel.

Tapez <kbd>CTRL</kbd>+<kbd>ALT</kbd>+<kbd>Suppr</kbd> pour fermer la session.
 
<details>
<summary>Attention, ce qui suit peut heurter la sensibilité des plus jeunes.</summary>

**Bouh !** :ghost:

</details>

Le processus itératif de minimisation de l'erreur à l'entraînement
d'un réseau de neurones s'appelle la <mark>descente de gradient</mark>.
