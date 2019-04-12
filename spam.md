Campagne de spams de février 2019 -- Ressentis et analyse technique
Louis Caravelle, le 06 février 2019

## Introduction

Je suis plutôt précautionneux dans la gestion de mes traces informatiques. Sensibilisé très tôt aux dangers d'Internet, tant vis-à-vis des accidents de fournisseurs de services en ligne (REF MISSING) qu'aux actes de malveillance de hackers aux chapeaux de diverses couleurs (REF HATS, REF CYBERTHREATS) je me suis mis à la recherche d'outils matériels et logiciels pour gérer mes mots de passe et sécuriser l'accès à mes terminaux informatiques.

Ces outils seront détaillés dans un autre article, mais on compte parmi eux gpg, pass, et la Yubikey NEO certifiée FIPS.

Une des conséquences de cette configuration est l'emploi de mots de passe aléatoires sur tous mes services en ligne, ce qui empêche toute possibilité d'effraction par recherche exhaustive (ou brute-force).

Faisons le calcul :

 - Mon mot de passe aléatoire contient au mois un caractère
   alphabétique miniscule ou majuscule, un chiffre, et un symbole.
   J'ai donc une plage de 26x2 + 10 + 12 = 74 symboles disponibles :

   abcdefghijklmnopqrstuvwxyz
   ABCDEFGHIJKLMNOPQRSTUVWXYZ
   & ~ # - @ + * $ % ! : ?
   0123456789 

 - Mes mots de passe font tous huit caractères ou plus, et j'ai 74
   symboles disponibles à chaque caractère. Il existe donc au total
   74 puissance 8 mots de passe différents, que l'on peut énumérer :
   
   aaaaaaaa
   aaaaaaab
   aaaaaaac
   ... (74)
   aaaaaaba
   aaaaaaca
   ...
   ???????:
   ????????
   
 - Sachant que 74 puissance 8 = 899194740203776, que le temps
   d'une requête de connexion est au mieux d'environ 1ms (REF)
   et qu'en recherchant au hasard, on peut espérer tomber sur
   le bon mot de passe en essayant en moyenne la moitié
   de l'ensemble des clés (REF), notre temps moyen avant
   connexion réussie est de
   
   Tconnexion = 449597370101888 * 0,001 secondes
   Tconnexion = 449597370101 secondes
   Tconnexion = 7493289501 minutes
   Tconnexion = 124888158 heures
   Tconnexion = 5203673 jours
   Tconnexion = 14454 ans.
   
   Bien sûr, la recherche pourrait aboutir plus vite sur un
   coup de chance, mais en moyenne, le temps de recherche est
   suffisamment déraisonnable pour dissuader ce genre d'attaques.

Pourtant, les 26 et 27 janvier 2019, puis le 1er février, j'ai reçu
sur ma messagerie étudiante des messages de chantage à la webcam.
L'expéditeur (qui semblait être mon propre compte) m'écrivait qu'il s'était infiltré après avoir retrouvé mon mot de passe, qu'il avait compromis tout le reste de mon infrastructure et qu'il demandait rançon contre la publication en ligne de ma vie privée et le chiffrement des données de mon disque dur.

[Mail Body Example]


## 1. Premières réactions

J'ai tout d'abord pris ces menaces avec le plus grand sérieux.
C'était la première fois que j'étais victime de cybermalveillance,
et mon premier réflexe a été de demander conseil à mes proches.
J'ai beau avoir fait mes études dans le domaine de la sécurité,
aucun cours ne m'avait préparé à une attaque directe de la sorte.

J'ai fini par prendre les deux résolutions suivantes.
 - Ne pas céder au maître chanteur et considérer mes données perdues
 - Construire un dossier de preuves pour obtenir justice

## 2. Comment a-t-il obtenu mon mot de passe ?

Tout d'abord j'ai tenté d'énumérer les (nombreuses) techniques
qu'un malfrat aurait pu utiliser pour récupérer mes identifiants.

 - Les attaques en dictionnaire (REF) n'affectent pas les mots de
   passe suffisament aléatoires (REF). Mon mot de passe ne provenait
   pas non plus d'une base de mots de passe fuités, à priori (REF).

 - Je n'ai pas à ma connaissance été victime de phishing (REF)
   car je vérifie toujours le certificat lors d'une connexion
   HTTPS sur un portail en ligne (REF) et que je n'ai pas non
   plus reçu précédemment de mail de changement d'identifiants.

 - Je fais attention aux réseaux auxquels je me connecte (REF)
   pour éviter les attaques de l'homme du milieu (REF).

 - J'utilise des logiciels et des services à jour pour éviter
   de rester vulnérable à des failles de sécurité découvertes (REF).
   
Toutes ces précautions et quelques recherches ont fini par me
convaincre que ces messages étaient une arnaque visant à enrichir
des malfrats par le biais d'une campagne de mensonges, propageant
le doute et la peur.

Mais comment a-t-il pu réussir à faire afficher mon nom de compte ?
Et comment m'assurer, s'il s'agit de mensonges, que je n'ai pas été compromis ?

## 3. Analyse du message

Les trois mails reçus étaient écrits dans un anglais un peu approximatif (REF) et présentaient les incohérences techniques suivantes.

 - 
 - 
 - 

De plus, les heures de réception n'étaient pas les mêmes suivant
la façon dont je les regardais. Ce point en particulier m'a incité
à afficher le code source complet du message.

[Ensemble du message source]

A la lecture du code source on peut noter les différents agents de
transfert du message (REF). C'est un peu comme si dans un relai de
courrier international, chaque pays traversé avait daté et tamponné
le colis concerné.

 - L'expéditeur affiché (moi) n'est pas dans le même domaine que le
   premier serveur de transfert. En fait, il n'est même pas dans le
   même fuseau horaire !
   [Details]
   
 - On peut voir d'autres infos intéressantes, notamment le score du
   message pour l'analyse antivirale et antispam.

On a donc dorénavant suffisamment de preuves pour affirmer que cet email n'a pas été envoyé depuis ma boîte de messagerie, et donc que
mon compte tout comme mon ordinateur ne sont pas compromis par cet
individu.

Il ne me reste plus qu'à signaler cet incident aux autorités
compétentes pour officialiser un retour à la normale et aider
la justice à coincer les responsables.


## 4. Signalement d'incident

Le signalement se fait à plusieurs niveaux : 

 - Dans un premier temps, on avertit les responsables techniques.
   C'est particulièrement vrai dans un cadre scolaire ou professionnel,
   mais reste également valable dans un cadre personnel, en appelant
   le service technique du FAI (Fournisseur d'Accès Internet) ou en
   demandant à des voisins et/ou des proches. On prendra bien garde
   à ne pas employer de moyens de communication suspectés compromis !

 - Ensuite, signaler l'incident auprès des autorités judiciaires.
   En France (REF) en Europe (REF) ou dans le monde entier (REF).

## Conclusion

TODO
