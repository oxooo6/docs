---
title: 'Utiliser un serveur VPN PPTP avec OverTheBox'
excerpt: "Découvrez comment ajouter un serveur VPN PPTP avec votre OverTheBox pour protéger et configurer vos connexions"
updated: 2024-05-06
---

## Objectif

L'ajout d'un serveur VPN PPTP à votre OverTheBox permet d'établir des connexions réseau sécurisées et privées, idéales pour des télétravailleurs ou des succursales éloignées. Ce guide vous explique comment configurer et utiliser un serveur VPN PPTP avec OverTheBox, vous offrant une méthode fiable et simple pour étendre votre réseau de manière sécurisée.

**Découvrez comment utiliser un serveur VPN PPTP avec OverTheBox.**

## Prérequis

- Disposer d'un service [OverTheBox](https://www.ovhtelecom.fr/overthebox/)
- Au moins un accès à Internet, fourni par [OVHcloud](https://www.ovhtelecom.fr/offre-internet/) ou un autre founisseur d'accès
- Un matériel OverTheBox fourni par OVHcloud ou une installation depuis le projet Open Source (consultez le guide « [Installer l'image overthebox sur votre materiel](/pages/web_cloud/internet/overthebox/advanced_installer_limage_overthebox_sur_votre_materiel) »)

## En pratique

Vous pouvez retrouver la marche à suivre pour effectuer une redirection de port standard en suivant [le guide dédié](/pages/web_cloud/internet/overthebox/middle_redirection_de_port).

Cependant, la configuration d'une redirection GRE sur OverTheBox est quelque peu spécifique. Veuillez suivre la procédure décrite ci-dessous.

- Rendez-vous sur [http://overthebox.ovh (192.168.100.1)](http://overthebox.ovh){.external} depuis votre ordinateur connecté au modem principal
- Cliquez sur `Network`{.action}
- Cliquez sur `Firewall`{.action}
- Cliquez sur `Port Forwards`{.action}
- Configurez une redirection vide :
    - **Name** : Inscrivez le nom de votre choix
    - **Protocol** : laissez **TCP+UDP**
    - **External zone** : indiquez **WAN**
    - **External port** : laissez vide
    - **Internal zone** : indiquez **LAN**
    - **Internal Ip** : choisissez l'IP locale de votre serveur PPTP
    - **Internal port** : laissez vide
- Cliquez sur `Add`{.action}

![overthebox](images/Forward1.png){.thumbnail}

- Cliquez ensuite sur `Edit`{.action}

![overthebox](images/Forward2.png){.thumbnail}

- Pour l'option `Protocol`, choisissez `Custom`{.action} puis inscrivez la valeur **47**

![overthebox](images/Forward3.png){.thumbnail}

- Cliquez enfin sur `Save & Apply`{.action}

Voici ce que vous devez obtenir :

![overthebox](images/Forward4.png){.thumbnail}

## Aller plus loin

- N'hésitez pas à échanger avec notre communauté d'utilisateurs sur vos produits Télécom sur notre site [OVHcloud Community](https://community.ovh.com/c/telecom)
- Consultez la [FAQ OverTheBox](/pages/web_cloud/internet/overthebox/install_faq)