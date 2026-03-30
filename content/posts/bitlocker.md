+++
title = "BitLocker sur Windows et Ubuntu"
description = "Chiffrement de stockage externe"
date = 2026-03-28

[taxonomies]
tags = ["sysadmin", "crypto"]
+++

## Introduction

Mardi 14 octobre 2025 au soir, je suis allé voir le film **_Tron: Ares_** au cinéma avec d'anciens collègues et amis.
Ce soir-là, je me suis fait voler mon sac, dans lequel se trouvait notamment mon disque dur externe.
Le disque n'était pas protégé, et contenait des informations personnelles qui auraient pu être utilisées pour usurper mon identité.

Par chance, ça ne s'est pas produit, mais ça m'a motivé à chercher une solution pour chiffrer ces données.
La seule qui soit prise en charge de façon native sur Windows et Linux semble être Bitlocker.
Voici comment la mettre en oeuvre.

## 1. Initialisation

L'activation de Bitlocker se fait depuis Windows 10 ou ultérieur et requiert un périphérique amovible.
Ce périphérique doit contenir une partition NTFS active [d'au moins 250 Mo][bitlocker-requirements].

Pour que le chiffrement soit compatible entre systèmes, il faut sélectionner :
 - Déverrouillage par mot de passe
 - Enregistrer la clé de récupération dans un fichier
 - Chiffrer tout le lecteur
 - Mode de chiffrement compatible

<figure>
    <div class="chiffrement">
        <img src="/bitlocker/chiffrement/01.png" style="width: 100%" alt="Sélection du disque à chiffrer">
        <img src="/bitlocker/chiffrement/02.png" style="width: 100%" alt="Choix du mode de déverrouillage">
        <img src="/bitlocker/chiffrement/03.png" style="width: 100%" alt="Choix du mode de récupération">
        <img src="/bitlocker/chiffrement/04.png" style="width: 100%" alt="Enregistrement de la clé de récupération">
        <img src="/bitlocker/chiffrement/05.png" style="width: 100%" alt="Choix de la méthode de chiffrement">
        <img src="/bitlocker/chiffrement/06.png" style="width: 100%" alt="Choix du mode de chiffrement">
        <img src="/bitlocker/chiffrement/07.png" style="width: 100%" alt="Confirmation du chiffrement">
        <img src="/bitlocker/chiffrement/08.png" style="width: 100%" alt="Chiffrement en cours">
        <img src="/bitlocker/chiffrement/09.png" style="width: 100%" alt="Chiffrement terminé">
    </div>
</figure>

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@nikba/easy-carousel/dist/easy-carousel.min.css">
<script src="https://cdn.jsdelivr.net/npm/@nikba/easy-carousel/dist/easy-carousel.min.js"></script>
<script>
    const carouselParams = {items: 1, loop: true, nav: true, dots: false}
    const alignments = new EasyCarousel('.chiffrement', carouselParams);
</script>

Le chiffrement initial peut être un peu long, pour un disque dur de 250 Go c'était environ 45 minutes si je me souviens bien.

Si vous séchez sur le choix du mot de passe, je vous conseille [diceware][diceware].

## 2. Utilisation

### 2.1 Sur Windows

Sur Windows 10 et ultérieur, le disque est visible depuis l'explorateur de fichiers.
Au moment de brancher le périphérique, le système détecte une partition chiffrée et demande le mot de passe.
Il y a aussi une fenêtre d'erreur qui s'affiche, avertissant l'utilisateur que l'ouverture automatique a échoué.

Sans le mot de passe, ça paraît normal.

<figure>
	<img src="/bitlocker/usage/windows-01.png" alt="Erreur bénigne de déverrouillage">
	<figcaption>Vous pouvez ignorer l'erreur</figcaption>
</figure>

Apparemment, il y aurait aussi une commande pour contrôler le chiffrement bitlocker depuis le terminal : [manage-bde][manage-bde]

### 2.2 Sur Ubuntu

Sur Ubuntu 22.04 et ultérieur, le processus est globalement le même.
La différence principale c'est qu'il n'y a pas d'autorun sur Ubuntu, et donc pas de message d'erreur.

<figure>
	<img src="/bitlocker/usage/ubuntu-01.png" alt="Invite d'authentification">
	<figcaption>L'invite d'authentification</figcaption>
</figure>

<br/>

<figure>
	<img src="/bitlocker/usage/ubuntu-05.png" alt="Disques">
	<figcaption>L'utilitaire des disques</figcaption>
</figure>

Le dispositif qui permet le déchiffrement en coulisses s'apelle [`cryptsetup`][cryptsetup].

## Conclusion

Avec cette méthode, on utilise Windows pour le chiffrement initial du disque, puis Windows ou Linux pour l'usage au quotidien.
Le plus important c'est qu'il n'y a rien besoin d'installer pour que cela fonctionne, tout est natif. 

Il reste toutefois quelques limitations :

1. Le chiffrement n'est pas disponible sur Linux
2. La technologie de chiffrement est propriétaire
3. Les autres systèmes (OSX, BSD) ne sont pas pris en charge

Ces limitations pourraient facilement être contournées si on s'autorisait l'installation d'un logiciel tiers sur le système hôte.
Dans un tel scénario, [VeraCrypt][veracrypt] semble être une alternative pertinente.

[bitlocker-requirements]: https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/bitlocker-drive-encryption?view=windows-11#bitlocker-drive-encryption-partitioning-requirements
[diceware]: https://fr.wikipedia.org/wiki/Diceware
[manage-bde]: https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/manage-bde
[cryptsetup]: https://www.man7.org/linux/man-pages/man8/cryptsetup.8.html
[veracrypt]: https://veracrypt.jp/en/Home.html
