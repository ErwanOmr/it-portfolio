# Déploiement standardisé d'un poste de travail (image système)

> Projet 5 — module *IT Essentials*, Geneva Institute of Technology, Bachelor IT 1re année.

**Contexte :** standardiser l'installation des postes de travail d'une entreprise : définir un poste type, préparer un poste de référence, créer une image, la déployer et mesurer le gain de temps par rapport à une installation manuelle.

**Technologies :** Windows 11 Pro · VMware Workstation (clonage complet) · Sysprep

**Compétences mises en œuvre :**
- Rédaction d'un cahier des charges de poste standard (OS, logiciels, comptes, réseau)
- Configuration d'un poste de référence (master)
- Création d'une image système et déploiement
- Contrôle de conformité et mesure du gain de temps

---

1. Définir un cahier des charges simple du poste standard (OS, logiciels, comptes, paramètres réseau).
2. Installer et configurer un poste de référence conforme à ce cahier des charges.
3. Créer une image système du poste de référence (outil au choix : imagerie disque, ou fichier de réponse pour installation automatisée) ou, à défaut de matériel suffisant, un script d'installation automatisée (ex. script PowerShell/Bash post-installation) qui reproduit la configuration standard.
4. Tester le déploiement de cette image/script sur une seconde machine (physique ou VM) et vérifier la conformité au cahier des charges.
5. Mesurer et comparer le temps nécessaire entre une installation manuelle complète et le déploiement via l'image/script.


---

**1. Définir un cahier des charges simple du poste standard (OS, logiciels, comptes, paramètres réseau).**

# Cahier des charges — Poste de travail standard

**1. Système d'exploitation**

| Élément | Spécification |
|---|---|
| OS | Windows 11 Pro |
| Langue | Français |
| Mises à jour | Windows Update activé, dernières mises à jour installées avant création de l'image |
| Activation | Pas de licence |

**2. Logiciels installés**

| Catégorie | Logiciel |
|---|---|
| Navigateur | Google Chrome |
| Suite bureautique | M365 (connexion requise)|
| Lecteur PDF | Adobe Acrobat Reader |
| Antivirus | Windows Defender (natif) |

**3. Comptes utilisateurs**

| Compte | Type | Détails |
|---|---|---|
| "ADMIN" | Administrateur local | Mot de passe fort défini |
| "User1" | Utilisateur standard | Droits limités (non admin) |
| Invité | Désactivé | Bonne pratique de sécurité |

**4. Paramètres réseau**

| Paramètre | Valeur |
|---|---|
| Configuration IP | DHCP (automatique) |
| Nom de machine | PCO1GIT |
| Groupe de travail | WORKGROUP |
| Pare-feu Windows | Activé (tous les profils) |
| Partage fichiers/imprimantes | Activé |

**5. Paramètres système généraux**

| Paramètre | Valeur |
|---|---|
| Fuseau horaire | Paris |
| Résolution d'écran | Par défaut VMware |
| Plan d'énergie | Équilibré |


---

**2. Installer et configurer un poste de référence conforme à ce cahier des charges.**

Lien HackMD vers comment installer Windows 11 : [Fondamentaux — TP5 : Installation de Windows 11](../07-fondamentaux-hardware-os/README.md#tp5--installation-de-windows-11-vmware-workstation)

Compte "ADMIN" + nom du PC : 
![image](https://hackmd.io/_uploads/BJlctwqtzg.png)

Logiciels installés : 
![image](https://hackmd.io/_uploads/Sy990DcKfe.png)

Compte utilisateur : 
![image](https://hackmd.io/_uploads/r1aQ1u5tze.png)
![image](https://hackmd.io/_uploads/HkbIJu5Kzg.png)

Groupe de travail : 
![image](https://hackmd.io/_uploads/H1xUeOqFMg.png)

Activation du Pare-feu Windows :
![image](https://hackmd.io/_uploads/S1BjlucKMg.png)

Activation du partage de fichiers/imprimantes : 
![image](https://hackmd.io/_uploads/BJoCbu9Ffe.png)
(Paramètres --> Réseau et Ethernet --> Paramètres réseau avancés --> Paramètres de partage avancés)

Fuseau horaire : 
![image](https://hackmd.io/_uploads/rkSufOcKGl.png)

Plan d'énergie
![image](https://hackmd.io/_uploads/By2x7d9FMx.png)


---

**3. Créer une image système du poste de référence (outil au choix : imagerie disque, ou fichier de réponse pour installation automatisée)**
 
**Préparation : généraliser le poste avec Sysprep**

Avant de capturer l'image, on généralise Windows pour que chaque poste déployé obtienne un SID et un nom de machine uniques (sinon, conflits sur le réseau) :

~~~
C:\Windows\System32\Sysprep\sysprep.exe /generalize /oobe /shutdown
~~~

La VM s'éteint ; au premier démarrage de chaque clone, Windows repasse par l'assistant de première configuration, tandis que les logiciels installés sont conservés.

> Remarque : les captures ci-dessous montrent un clonage réalisé sans Sysprep, c'est pourquoi le clone conserve le même nom de PC. En production, la généralisation (Sysprep, ou un outil de déploiement comme MDT/WDS ou Clonezilla) est indispensable.

Arrêter proprement la VM de référence : 
![image](https://hackmd.io/_uploads/r1J27_9FGe.png)

Lancer l'assistant de clonage : 
![image](https://hackmd.io/_uploads/BJJ44Oqtfe.png)

Sélectionner "The current state in the virtual machine" : 
![image](https://hackmd.io/_uploads/HJtO4uqKfe.png)

Sélectionner "Create a full clone" : 
![image](https://hackmd.io/_uploads/BkYa4uqYzg.png)

Choisir un nom pour le clone de la VM et choisir son emplacement sur l'ordinateur hôte : 
![image](https://hackmd.io/_uploads/H1LOr_9YGg.png)

Le clonage se lance : 
![image](https://hackmd.io/_uploads/HyisBd5Yzl.png)
(l'action peut être assez longue)

![image](https://hackmd.io/_uploads/Sy1pvd9tfx.png)

Elle apparaît à côté : 
![image](https://hackmd.io/_uploads/SydGOd9Yzx.png)

Le clonage VMware constitue un "outil d'imagerie" dans un contexte virtualisé (sinon utiliser Clonezilla par exemple)

**4. Tester le déploiement de cette image/script sur une seconde machine (physique ou VM) et vérifier la conformité au cahier des charges.**

On voit déjà que le bureau de la VM clonée est identique : 
![image](https://hackmd.io/_uploads/Hkgidu5Kze.png)
(Avec les mêmes logiciels installés)

Avec les mêmes comptes utilisateurs : 
![image](https://hackmd.io/_uploads/SJhzKdcKGl.png)

Le même nom de PC et le même groupe de travail (le nom devra ensuite être changé : deux postes ne peuvent pas avoir le même nom sur le réseau) : 
![image](https://hackmd.io/_uploads/BkwRYO9YMx.png)

Ou encore le partage de fichiers activé par exemple: 
![image](https://hackmd.io/_uploads/HJAvKu9Fzg.png)


---

**5. Mesurer et comparer le temps nécessaire entre une installation manuelle complète et le déploiement via l'image/script.**

Installation Manuelle : 

| Étape | Temps estimé |
|---|---|
| Installation Windows 11 (ISO → bureau) | 20–25 min |
| Premières mises à jour Windows | 15–20 min |
| Création comptes (admin + standard) | 5 min |
| Installation Chrome | 3 min |
| Installation M365 | 3 min |
| Installation Adobe Acrobat Reader | 3 min |
| Configuration réseau (groupe de travail, partage, pare-feu) | 5 min |
| **Total installation manuelle** | **~55-65 min** |


Déploiement via image clonée : 

| Étape | Temps estimé |
|---|---|
| Clonage VMware (full clone) | 8–10 min |
| Démarrage du clone + vérifications post-clone | 4–5 min |
| Renommage de la machine (obligatoire, nom unique par poste) | 2 min |
| **Total déploiement via image** | **~14–17 min** |

**Bilan :** environ 55-65 min en installation manuelle contre 14-17 min par déploiement d'image, soit un gain d'environ 4×. La préparation du poste de référence n'est faite qu'une seule fois : l'image devient rentable dès le 2e poste, et garantit que tous les postes sont identiques.


---
