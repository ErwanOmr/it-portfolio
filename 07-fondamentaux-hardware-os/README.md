# Fondamentaux — Hardware, BIOS/UEFI et systèmes d'exploitation

> TP1 à TP7 et épreuve finale *Technicien IT* — module *IT Essentials*, Geneva Institute of Technology, Bachelor IT 1re année.

**Technologies :** matériel PC (CPU, RAM, stockage, alimentation) · BIOS/UEFI · Rufus · VMware Workstation · Windows 10/11 · Ubuntu

**Compétences mises en œuvre :**
- Démontage / remontage d'un PC et identification des composants
- Conception de configurations compatibles selon un usage et un budget (gaming, bureautique, étudiant)
- Configuration BIOS/UEFI (boot, mot de passe, Clear CMOS)
- Création de supports d'installation et installation de Windows et Linux en VM
- Configuration post-installation (mises à jour, sécurité, réseau) et tests de connectivité

## Sommaire

- [TP1 — Montage / démontage d'un PC](#tp1--montage--démontage-dun-pc)
- [TP2 — Étude et validation de compatibilité d'une configuration PC](#tp2--étude-et-validation-de-compatibilité-dune-configuration-pc)
- [TP3 — Configuration du BIOS/UEFI : mot de passe et Clear CMOS](#tp3--configuration-du-biosuefi--mot-de-passe-et-clear-cmos)
- [TP4 — Création d'une clé USB bootable](#tp4--création-dune-clé-usb-bootable)
- [TP5 — Installation de Windows 11 (VMware Workstation)](#tp5--installation-de-windows-11-vmware-workstation)
- [TP6 — Installation d'Ubuntu (VMware Workstation)](#tp6--installation-dubuntu-vmware-workstation)
- [TP7 — Test de connectivité réseau VM ↔ machine physique](#tp7--test-de-connectivité-réseau-vm--machine-physique)
- [Épreuve finale — Configurations hardware et déploiement des OS](#épreuve-finale--configurations-hardware-et-déploiement-des-os)

---

## TP1 — Montage / démontage d'un PC

**Fiche d'identification des composants :** 

![image](https://hackmd.io/_uploads/HkhcHp0uzx.png)

**Caractéristiques** 
CPU : Intel Core i5-3470 @ 3.20 GHz, 4 cœurs, socket LGA1155
Carte Mère : Lenovo IS7XM Rev 1.0 / LGA1155 / SFF
RAM : 8 Go annoncés, DDR3
Stockage : HDD WDC WD5000AAKX-08ERMA0 / 500 Go / SATA
GPU : aucune carte dédiée, GPU intégré au CPU
Alimentation : Liteon PS-4241-01, 240 W max, 80 PLUS
Boitier : ThinkCentre M92p type 2988D6G, S/N S4MXA4
Carte Réseau : Ethernet intégré à la carte mère + carte Wi-Fi Intel Centrino Advanced-N 6205





---


**1. Photo du PC hors tension et débranché avant :** 

![image](https://hackmd.io/_uploads/By5rIsAOze.png)

**2. Ouverture du capot :** 
Avec le bouton latéral bleu
![image](https://hackmd.io/_uploads/rkmBOoROGg.png)

**3. Débranchement du Disque dur HDD :** 
![image](https://hackmd.io/_uploads/BJwi_s0OGg.png)
![image](https://hackmd.io/_uploads/BJQaOsCOMx.png)

**4. Débranchement du lecteur DVD :**
![image](https://hackmd.io/_uploads/HkEWtsC_zx.png)


**5. Dévisser le Ventirad du CPU**
![image](https://hackmd.io/_uploads/HJincj0dMg.png)
![image](https://hackmd.io/_uploads/SkyZii0OGe.png)
![image](https://hackmd.io/_uploads/SJWwaj0dzg.png)

**6. Retirer le CPU du socket :** 
![image](https://hackmd.io/_uploads/HyrIMaROMl.png)
![image](https://hackmd.io/_uploads/r1eqMpC_Me.png)

**7.  Puis retirer les barrettes de RAM :**
Déclipser les attaches blanches de chaque cotés
![image](https://hackmd.io/_uploads/H18WQpAufx.png)
![image](https://hackmd.io/_uploads/rkuG1nRufx.png)

**8. Dévisser l'alimentation :**
![image](https://hackmd.io/_uploads/HksZzT0_Gx.png)
![image](https://hackmd.io/_uploads/HkcaJ30dMl.png)

**9.  Dévisser la carte mère :**
![image](https://hackmd.io/_uploads/rkaQf3COfl.png)

Carte mère hors du boitier :
![image](https://hackmd.io/_uploads/S1ZIM2AuGe.png)


---


### Remontage du PC

Remontage du PC en suivant les étapes dans le sens inverse : 

(Vidéos de documentation)
*(Vidéo de démonstration disponible sur demande)*

*(Vidéo de démonstration disponible sur demande)*

Démarrage de l'ordinateur : 
![image](https://hackmd.io/_uploads/SJreFpRuMg.png)
![image](https://hackmd.io/_uploads/ByQJYTAuMe.png)

---

## TP2 — Étude et validation de compatibilité d'une configuration PC

***Contexte.***  Un client donne un budget et un usage cible  (gaming/bureau/serveur). Vous devez proposer une configuration cohérente et compatible.

Je choisis de configurer un PC Gamer pour un joueur désirant devenir professionnel avec un budget max de 2500$.

**Choix et Vérification de compatibilité**
![image](https://hackmd.io/_uploads/rySfyCR_Ge.png)



| Composant              | Choix                                  | Compatibilité                               |
| ---------------------- | -------------------------------------- | ------------------------------------------- |
| CPU                    | AMD Ryzen 7 7700X                      | Socket AM5, 142W PPT max                    |
| Refroidissement du CPU | Corsair Nautilus 240 RS (AIO 240mm)    | Suffisant pour 142W PPT max, compatible AM5 |
| Carte Mère             | MSI MAG B850 Tomahawk Max WiFi ATX AM5 | Socket AM5 correspond au CPU                |
| Mémoire RAM            | Corsair Vengeance 32 Go DDR5-6000 CL36 | DDR5 compatible AM5                         |
| Stockage               | Samsung 990 Pro 1 To NVMe PCIe 4.0     | Compatible slot M.2                         |
| Carte Graphique        | Asus PRIME OC RX 9070 XT 16 Go         | Compatible PCIe x16                         |
| Boitier                | Phanteks XT Pro ATX Mid Tower          | Compatible ATX + radiateur 240mm            |
| Système d'exploitation | Windows 11 Home 64-bit                 | Compatible (TPM 2.0, UEFI présents)         |


---

### Justification des choix

**CPU :** 8 cœurs/16 threads à 4.5 GHz suffisent largement pour la quasi-totalité des jeux actuels

**Refroidissement du CPU :** Le Ryzen 7 7700X a un TDP de base de 105W, avec un pic (PPT) à 142W maximum.
Or, le Corsair Nautilus 240 RS offre une dissipation fiable pour des CPU jusqu'à environ 150W de TDP.

**Carte Mère** : Chipset B850 donc récent, supporte le PCIe 5.0 pour la carte graphique et le stockage rapide, WiFi intégré pour le jeu en ligne sans câble Ethernet.

**RAM :** 32 Go = confortable pour jouer tout en ayant plusieurs applications lancées en simultané.
La fréquence 6000 MHz est le point optimal pour les CPU AMD.

**Stockage :** Vitesses de lecture/écriture très élevées → temps de chargement des jeux réduits, Samsung est aussi réputé pour ses bons SSD.

**Carte Graphique :** La 9070 XT est l'une des plus puissantes d'aujourd'hui, 16 Go de VRAM permet de jouer confortablement en 1440p voire 4K avec des textures de très bonne qualité.

**Boitier :** Bonne circulation d'air pour garder tous les composants au frais, espace suffisant pour la carte graphique et le radiateur 240mm.

**Alimentation :** Fournit une alimentation stable et suffisante pour le CPU + GPU sous charge maximale.

**Système d'exploitation :** Meilleure compatibilité avec les jeux récents grâce à DirectX 12 Ultimate, DirectStorage et le Auto HDR



---


**TDP total :** 
![image](https://hackmd.io/_uploads/rkxbIRAuGg.png)

---

## TP3 — Configuration du BIOS/UEFI : mot de passe et Clear CMOS

**Étape 1 :** Accéder au BIOS/UEFI, configurer la date/l'heure et l'ordre de démarrage (boot order).

![image](https://hackmd.io/_uploads/BJHGsRAufx.png)




**Étape 2 :** Définir un mot de passe superviseur, redémarrer et constater la demande de mot de passe.
![image](https://hackmd.io/_uploads/SJfC3RRuzl.png)
![image](https://hackmd.io/_uploads/S1SWpR0Ofe.png)


**Étape 3 :** Réaliser un Clear CMOS (pile ou cavalier) et vérifier que les réglages sont revenus par défaut (mot de passe supprimé).
![image](https://hackmd.io/_uploads/r1eg000Ofx.png)
![image](https://hackmd.io/_uploads/ryruaAAuMe.png)

Même après avoir réalisé le Clear CMOS (pile), le BIOS me demandait encore le mot de passe : 
![image](https://hackmd.io/_uploads/BJx7A0C_Ge.png)

**Analyse :** sur ce poste (Lenovo ThinkCentre), le mot de passe superviseur n'est pas stocké dans la mémoire CMOS mais dans une mémoire non volatile : retirer la pile réinitialise les réglages, mais pas ce mot de passe. Il faut alors suivre la procédure du constructeur (cavalier dédié selon le modèle ; sur certains modèles, seul le support Lenovo peut l'effacer). D'où l'importance de conserver ce mot de passe en lieu sûr.

---

## TP4 — Création d'une clé USB bootable

1. Télécharger les ISO Windows 11 / Ubuntu 
 ![image](https://hackmd.io/_uploads/Hk0dsYeKMl.png)
 Ubuntu : https://ubuntu.com/download/desktop
 Windows : https://www.microsoft.com/en-us/software-download/windows11
 

2. Créer la clé bootable Windows 11 (GPT/UEFI).
![image](https://hackmd.io/_uploads/ryonoKlYfl.png)
Installer le logiciel **"Rufus"**

Puis insérer la clé USB 

La clé est bien reconnue : 
![image](https://hackmd.io/_uploads/S1YOxcgYMl.png)

Insertion du fichier ISO windows 11 : 
![image](https://hackmd.io/_uploads/r1PXbqeFMe.png)

Cliquer sur "Démarrer" : 
![image](https://hackmd.io/_uploads/Sk9dZ5xYGg.png)

Personnaliser l'installation de Windows : 
![image](https://hackmd.io/_uploads/SkfTWqeYGe.png)

Attendre la fin du processus : 
![image](https://hackmd.io/_uploads/SJGyGqltMe.png)

La clé est prête à être utilisée pour installer Windows 11 sur une machine : 
![image](https://hackmd.io/_uploads/ry5eS5gFfe.png)


---

Malheureusement je n'ai qu'une clé USB utilisable à disposition donc je ne peux pas faire l'installation avec Ubuntu.
Cependant, le processus est presque identique (sauf la personnalisation de Windows)


---

---

## TP5 — Installation de Windows 11 (VMware Workstation)

![wp10070815](https://hackmd.io/_uploads/ry4vNtpdzl.webp)



---


1. Installer le fichier ISO Windows 11
![image](https://hackmd.io/_uploads/r1kOStTdGl.png)
Trouvable sur le site officiel de Microsoft : https://www.microsoft.com/en-us/software-download/windows11


---

2. Créer une machine virtuelle sur VMware Workstation :
![image](https://hackmd.io/_uploads/SJgg8taufg.png)
Sélectionner "Typical"
![image](https://hackmd.io/_uploads/ryC4IF6_Mg.png)
 Chercher le fichier ISO Windows sur votre ordinateur :
![image](https://hackmd.io/_uploads/SJMd6Ya_zg.png)
Vérifier si le système d'exploitation correspond :
![image](https://hackmd.io/_uploads/rJV0pKp_Gx.png)


Renommer la VM si souhaité : 
Faire attention à l'emplacement de la VM (local pour de meilleures performances)
![image](https://hackmd.io/_uploads/HJ3PAtp_Ml.png)
Générer un mot de passe pour Windows : 
![image](https://hackmd.io/_uploads/r1420YTdMg.png)


Réserver un minimum de 64GB de stockage pour windows 11 PRO et séléctionner "Split" : 
![image](https://hackmd.io/_uploads/Hkr5kcTdGe.png)



Cliquer sur "Finish" ou customizer le hardware si besoin (Augmenter la RAM, le processeur, etc...) : 
![image](https://hackmd.io/_uploads/Bybxx9Tuzg.png)

---

3. Configurer Windows 11
Sélectionner la langue voulue :
![image](https://hackmd.io/_uploads/rkAtbqTOfg.png)

Sélectionner les paramètres de langue du clavier :
![image](https://hackmd.io/_uploads/HJEeMqTOMg.png)

Rentrer une clé Windows si possédée ou cliquer sur "je n'ai pas de produit" : 
![image](https://hackmd.io/_uploads/H1t7f96uGe.png)

Sélectionner la version Windows voulue :
![image](https://hackmd.io/_uploads/r1FFGq6OGg.png)


Voici l'emplacement de stockage qu'on a dédié à cette VM, cliquer sur "Suivant" : 
![image](https://hackmd.io/_uploads/rykf7cadzx.png)

Finaliser l'installation et cliquer sur "Installer" :
![image](https://hackmd.io/_uploads/r1EvQcTdGx.png)


Pour passer les étapes qui suivent, qui correspondent à la 1re configuration de Windows et à la connexion de votre compte Microsoft, faire Shift + F10 et entrer la commande : 
```
oobe\bypassnro
```

> Sur les builds récentes de Windows 11, cette commande peut ne plus fonctionner (Microsoft l'a retirée) : il faut alors passer par un compte Microsoft ou par un fichier de réponse.



 Nous voici à présent sur le Bureau de la VM "PCO1_WIN11" :+1: 
![image](https://hackmd.io/_uploads/B1N0O0ROMe.png)



---


**✅ L'installation de Windows 11 Pro est terminée.**

---

## TP6 — Installation d'Ubuntu (VMware Workstation)

![image](https://hackmd.io/_uploads/SyP0kJktGe.png)

---
1. Installer le fichier ISO Ubuntu Desktop :
 https://ubuntu.com/download/desktop
 ![image](https://hackmd.io/_uploads/ByDEe1yFMe.png)

2. Créer une machine virtuelle sur VMware Workstation : 
 ![image](https://hackmd.io/_uploads/SJgg8taufg.png)
Sélectionner "Typical"
![image](https://hackmd.io/_uploads/ryC4IF6_Mg.png)

Chercher le fichier ISO Ubuntu sur votre Ordinateur : 
![image](https://hackmd.io/_uploads/HyiVW11tMg.png)

Personnaliser Linux : 
![image](https://hackmd.io/_uploads/rJQT-1kYGx.png)

Renommer la VM si souhaité :
Faire attention à l'emplacement de la VM (local pour de meilleures performances)
![image](https://hackmd.io/_uploads/rJErM11Ffg.png)

Réserver un minimum de 20GB de stockage pour Ubuntu et séléctionner "Split" :
![image](https://hackmd.io/_uploads/HJqaGy1YGe.png)

Cliquer sur "Finish" ou customizer le hardware si besoin (Augmenter la RAM, le processeur, etc…) :
![image](https://hackmd.io/_uploads/HkFXXJyYze.png)


---

3. Configurer Ubuntu : 

Choisir la langue : 
![image](https://hackmd.io/_uploads/SyouEkkKGl.png)

Les différents paramètres d'accessibilité : 
![image](https://hackmd.io/_uploads/ryNOBkytMl.png)

Choisir la langue du clavier : 
![image](https://hackmd.io/_uploads/ryrork1KMg.png)

Choisir le moyen de connexion : 
![image](https://hackmd.io/_uploads/SJpTSyyKfl.png)

Faire la mise à jour : 
![image](https://hackmd.io/_uploads/S1n-8kJYzx.png)

Et installer Ubuntu : 
![image](https://hackmd.io/_uploads/By3V8ykFMg.png)
(J'ai choisi « Try Ubuntu » pour ce test : Ubuntu tourne alors en session live. Pour l'installer réellement sur le disque de la VM, il faut choisir « Install Ubuntu ».)



Nous voici à présent sur le Bureau de la VM "Ubuntu_PC03" :+1: 
![image](https://hackmd.io/_uploads/Sy3PIykKMl.png)



---



**✅ Ubuntu est opérationnel dans la VM.**

---

## TP7 — Test de connectivité réseau VM ↔ machine physique

**Principe :** vérifier qu'une machine virtuelle et le PC hôte communiquent sur le réseau.

1. Choisir le mode réseau de la VM dans VMware (NAT, Bridged ou Host-only).
2. Relever les adresses IP : `ipconfig` sous Windows, `ip a` sous Linux.
3. Vérifier que les deux machines sont dans le même réseau (ou reliées par une passerelle).
4. Autoriser l'ICMP si besoin : le pare-feu Windows bloque par défaut les ping entrants (règle « Partage de fichiers et d'imprimantes (Demande d'écho - ICMPv4 entrant) »).
5. Tester dans les deux sens avec `ping <adresse IP>`.

*(Vidéo de démonstration disponible sur demande)*

---

## Épreuve finale — Configurations hardware et déploiement des OS

### 1. Conception de configurations hardware
**Profil 1 — PC Gamer**
![image](https://hackmd.io/_uploads/H1xR2BWtMl.png)

![image](https://hackmd.io/_uploads/ByDHZDWtMg.png)


Pour un total de 1691.54$ ce qui donne 1379,36 CHF
![image](https://hackmd.io/_uploads/B1lCYZvZKMg.png)


| Composant       | Choix                                                         | Compatibilité |
| --------------- | ------------------------------------------------------------- | ----------- |
| CPU             | AMD Ryzen 7 5800XT 3.8 GHz 8-Core Processor      |    Socket AM4         |
| Refroidissement | Thermalright Phantom Spirit 120 SE ARGB 66.17 CFM CPU Cooler  |     Compatible socket AM4, environ 155-157mm de haut        |
| Carte Mère      | MSI B550-A PRO ATX AM4 Motherboard             |      Format ATX standard       |
| RAM             | Corsair Vengeance LPX 32 GB (2 x 16 GB) DDR4-3200 CL16 Memory |  La fréquence (3200 MHz) correspond au sweet spot du CPU           |
| Stockage        | Crucial P310 2 TB M.2-2280 PCIe 4.0 X4 NVME Solid State Drive |  2 slots M.2           |
| GPU             | ASRock Challenger OC Radeon RX 7700 XT 12 GB      |   PCIe x16 Slots, 27-28cm de long, le boitier en fait 36cm         |
| Boitier         | Corsair FRAME 4000D RS ARGB ATX Mid Tower Case         |  format ATX standard, Clearance CPU cooler d'environ 170mm      |
|  Alimentation    |      	MSI MAG A650BN 650 W 80+ Bronze Certified ATX Power Supply         |    Compatible ATX         |
| OS       |  	Microsoft Windows 11 Home             |         UEFI présent    |

           
**Justification des choix :**

CPU : 3.8 GHz et 8 cœurs, c'est suffisant pour du gaming en 1440p à 60fps
Cooler : Assez puissant pour un CPU a 105W (TDP)
Carte mère : PCIe 4.0 utile pour le SSD et le GPU
RAM : 32 Go sont nécessaires pour les jeux actuels avec d'autres appli qui tournent en fond (discord, youtube, ...)
Stockage : 2 To sont nécessaires pour installer plusieurs jeux, le SSD réduit fortement les temps de chargement.
GPU : La Radeon RX 7700 XT 12 GB est largement assez puissante pour jouer en 1440p à 60fps
Boitier : Bon airflow
Alimentation : ATX, 80+ Bronze Certified, 650W couvrent le total que j'estime d'environ 400W de TDP.
           
           
           
**Profil 2 — PC Administration (bureautique)**
![image](https://hackmd.io/_uploads/rJVOtvWtMl.png)

![image](https://hackmd.io/_uploads/SyFcXuZtMl.png)

Pour un total de 618,35 $ ce qui donne 504,23 CHF
![image](https://hackmd.io/_uploads/Hyd8Eu-tGe.png)

| Composant       | Choix                                                         | Compatibilité |
| --------------- | ------------------------------------------------------------- | ----------- |
| CPU             | AMD Ryzen 5 5600G 3.9 GHz 6-Core Processor | Socket AM4, supporté par le chipset A520 |
| Refroidissement |	be quiet! Pure Rock Pro 3 59.6 CFM CPU Cooler | Socket AM4    |
| Carte Mère      |    	Gigabyte A520M S2H Micro ATX AM4 Motherboard        |Socket AM4, DDR4        |
| RAM             |	Silicon Power SP016GBLFU320B22 16 GB (2 x 8 GB) DDR4-3200 CL22 Memory |  DDR4, 3200 = vitesse native Ryzen, AM4   |
| Stockage        |	Patriot P300 512 GB M.2-2280 PCIe 3.0 X4 NVME Solid State Drive |     Slot M.2 carte mère       |
| Boitier         | 	Zalman S4 ATX Mid Tower Case | Accueille Micro-ATX, Alim ATX |
|  Alimentation    |   	Thermaltake Smart 500 W 80+ Certified ATX Power Supply     | ATX standard
| OS       |  	Microsoft Windows 11 Home             |         UEFI présent    |

> **Correction :** le Ryzen 3 3200G choisi initialement n'est pas compatible avec les cartes mères A520 ; il est remplacé par un Ryzen 5 5600G (même socket AM4, GPU intégré, DDR4-3200 native). Le total indiqué correspond à la configuration initiale.

**Justification des Choix :**
CPU : possède un GPU intégré (pas besoin de carte graphique), 6 cœurs : largement suffisant pour de la bureautique.
CPU Cooler: La marque be quiet! est spécifiquement reconnue pour la faible nuisance sonore, suffisant pour un CPU à seulement 65W de TDP. 
RAM : 16GB c'est confortable pour du multi-fenêtre + suite Office + navigateur avec plusieurs onglets.
Stockage : SSD donc fiable et silencieux, 512 Go suffisant pour de la bureautique.
Alimentation : 500W est largement suffisant pour ce PC avec un TDP total estimé d'environ 105W.



**Profil 3 — PC Étudiant (informatique)**
![image](https://hackmd.io/_uploads/HkDtsu-YGg.png)
![image](https://hackmd.io/_uploads/BJOS5KbKzg.png)


Pour un total de 1084,30 $ ce qui donne 884,19 CHF
![image](https://hackmd.io/_uploads/Bk5wcKbKMg.png)



| Composant       | Choix | Compatibilité |
| --------------- | ----- | ------------- |
| CPU             |  	AMD Ryzen 7 8700G 4.2 GHz 8-Core Processor     |   Socket AM5            |
| Refroidissement | Ventirad AMD fourni avec le CPU | Compatible AM5 |
| Carte Mère      | Asus TUF GAMING B650E-PLUS WIFI ATX AM5 Motherboard      |      Socket AM5, DDR5, Slots PCIe 4.0, Format ATX  |
| RAM             |   	Kingston FURY Beast 32 GB (2 x 16 GB) DDR5-6000 CL36 Memory    |   DDR5            |
| Stockage        |  	Kingston NV3 1 TB M.2-2280 PCIe 4.0 X4 NVME Solid State Drive     |   Slots PCIe 4.0            |
| Boitier         | 	Phanteks XT PRO ATX Mid Tower Case      |     Format ATX           |
| Alimentation    |  	MSI MAG A650BN 650 W 80+ Bronze Certified ATX Power Supply     |   Format ATX             |
| OS              |  	Microsoft Windows 11 Pro      |               |

**Justification des choix :**
> **Corrections :** RAM passée à 32 Go (virtualisation), alimentation remplacée par une marque reconnue, ventirad fourni avec le Ryzen 7 8700G ajouté au tableau. Le total indiqué correspond à la configuration initiale.

CPU : Possède un bon GPU intégré, 8 cœurs, très bien pour de la virtualisation.
Carte mère : Compatible avec tout, elle intègre également le Wi-Fi et le Bluetooth
RAM : 32 Go de DDR5, nécessaires pour faire tourner plusieurs machines virtuelles en parallèle
Stockage : 1 To est nécessaire pour créer des VM
Alimentation : Alimentation OK, rentre dans les prix, TDP max d'environ 150W donc largement suffisante.

### 2. Déploiement des systèmes d'exploitation

**Partie A — Windows 10 Pro**

> Windows 10 n'est plus pris en charge par Microsoft depuis le 14 octobre 2025 : en production, on déploierait Windows 11.

Installer Windows 10 Pro : ![image](https://hackmd.io/_uploads/HJBz3W4Fzx.png) 13h45

Pour créer la VM : [Fondamentaux — TP5 : Installation de Windows 11](../07-fondamentaux-hardware-os/README.md#tp5--installation-de-windows-11-vmware-workstation)
![image](https://hackmd.io/_uploads/B1mXRWEKGg.png)
13h51

![image](https://hackmd.io/_uploads/H1yaCbVtzg.png)
13h52

![image](https://hackmd.io/_uploads/BJO4yfEKfl.png)
13h55




**Configuration minimale du système :**

Sélectionner la langue : 
![image](https://hackmd.io/_uploads/BJr7xzVYzg.png)
13h59


Installer Windows 10 : 
![image](https://hackmd.io/_uploads/B1UDlfNKzl.png)
14h00

Cliquer sur "Je n'ai pas de clé produit" si vous n'avez pas de licence Windows 10
![image](https://hackmd.io/_uploads/ryd5lMEFMl.png)
14h03

Sélectionner la version Windows voulue (Pro dans ce cas) : 
![image](https://hackmd.io/_uploads/Bkaq-f4FGe.png)
14h05

Sélectionner l'installation personnalisée :
![image](https://hackmd.io/_uploads/By1P7zVtfe.png)
14h12

Attendre la fin de l'installation : 
![image](https://hackmd.io/_uploads/rkF3QGEtMg.png)
14h14

L'ordinateur va ensuite redémarrer et vous montrer ceci : 
![image](https://hackmd.io/_uploads/SyWeHM4Kfx.png)
14h20
Cliquer sur "Oui"

Choisir la bonne langue de clavier : 
![image](https://hackmd.io/_uploads/S1U8HM4tzg.png)
14h21

Sélectionner "Utilisation personnelle"
![image](https://hackmd.io/_uploads/r1oABzEKzx.png)
14h24

Connecter votre compte Microsoft ou cliquer sur "Compte hors connexion"
![image](https://hackmd.io/_uploads/Sku28fNYfe.png)
14h28

Saisir un nom d'utilisateur : 
![image](https://hackmd.io/_uploads/SykbDMVFGe.png)
14h29

Saisir un mot de passe :
![image](https://hackmd.io/_uploads/HkMLDzEKzg.png)
14h30

Créer vos questions de sécurité : 
![image](https://hackmd.io/_uploads/HkE5wMVYfx.png)
14h31

Importer ou non vos données Microsoft Edge : 
![image](https://hackmd.io/_uploads/ry7xdfEKfl.png)
14h32

Une série de questions comme celle-ci va apparaître, choisir selon vos préférences : 
![image](https://hackmd.io/_uploads/r1ihOMNYMg.png)
14h36

Sélectionner selon vos préférences : 
![image](https://hackmd.io/_uploads/BJswKGVtGg.png)
14h37

Attendre la fin de la configuration : 
![image](https://hackmd.io/_uploads/rJ3iFGNFzl.png)
14h39


Nous voici sur le bureau de la VM "PC01_EO" :
![image](https://hackmd.io/_uploads/ryGycz4YGx.png)


 Fuseau Horaire :
![image](https://hackmd.io/_uploads/BJlcjMVYze.png)
14h47

Connecté au réseau :
![image](https://hackmd.io/_uploads/H1deRzEYfg.png)
14h58

Applications au démarrage : 
![image](https://hackmd.io/_uploads/HJEjAM4Kfx.png)
15H01


Sécurité Windows : ![image](https://hackmd.io/_uploads/BkdU1QNFGl.png)
15h03

Installation des mises à jour : 
![image](https://hackmd.io/_uploads/ryrCG7NKfe.png)
15h19

Nettoyage de disque : 
![image](https://hackmd.io/_uploads/S1xiE7EFze.png)
15h25

Création d'un point de restauration : 
![image](https://hackmd.io/_uploads/rybFH7Vtfe.png)
15h30

Winver : ![image](https://hackmd.io/_uploads/r1w0BQNKMl.png)
15h32

Ipconfig : 
![image](https://hackmd.io/_uploads/rJ2lUQVFGe.png)
15h32



---


**Partie B — Ubuntu**

 Installer Ubuntu : 
 ![image](https://hackmd.io/_uploads/HkNHtmVYMg.png)
15h45

Pour créer la VM et configurer Ubuntu : [Fondamentaux — TP6 : Installation d'Ubuntu](../07-fondamentaux-hardware-os/README.md#tp6--installation-dubuntu-vmware-workstation)
 
 
Mettre à jour Ubuntu : 
 ![image](https://hackmd.io/_uploads/ryMn5XEFzx.png)
15h52

Avec la commande :
````
sudo apt update && sudo apt upgrade
````

![image](https://hackmd.io/_uploads/HJw8s74tfg.png)
15h55

Suppression des paquets inutiles : 
````
sudo apt autoremove
````

Connecté au réseau :
![image](https://hackmd.io/_uploads/B1dG6mVtGg.png)
16h01


Fuseau horaire : 
![image](https://hackmd.io/_uploads/ryv8pmVtfg.png)
16h04


Langue Clavier : 
![image](https://hackmd.io/_uploads/HJ2gAQ4Yfx.png)
16h06


Activation du Pare-Feu : 
![image](https://hackmd.io/_uploads/HJWvDVVFGg.png)
16h46

Vérification finale à documenter : ip a, lsb_release -a
![image](https://hackmd.io/_uploads/SJgQRNNtGe.png)
17h15

![image](https://hackmd.io/_uploads/S1UdkrNKGg.png)
17h17



| Nom du Poste | OS             | Adresse IP      | Date d'installation | Technicien responsable |
| ------------ | -------------- | --------------- | ------------------- | ---------------------- |
| PC01_EO      | Windows 10 Pro | 192.168.159.134 | 13/09/2026          | Omorodion Erwan        |
|     PC02_EO         |        Ubuntu        |  192.168.1.28     |       13/09/2026  |     Omorodion Erwan     |

> Remarque : PC01 (mode NAT, réseau 192.168.159.0/24) et PC02 (mode Bridged, réseau 192.168.1.0/24) ne sont pas dans le même réseau. Pour la partie Interconnexion, les deux VM doivent utiliser le même mode réseau VMware (par exemple NAT pour les deux).


---


### 3. Interconnexion des machines

*(Vidéo de démonstration disponible sur demande)*


---
