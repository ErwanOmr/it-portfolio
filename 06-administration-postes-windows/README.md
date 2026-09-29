# Administration de postes Windows — Mise en service et migration

> Projets 1 et 3 — module *IT Essentials*, Geneva Institute of Technology, Bachelor IT 1re année.

**Technologies :** Windows 11 Pro · VMware Workstation · PowerShell / cmd · Partages SMB

**Compétences mises en œuvre :**
- Installation et mise en service complète d'un poste (comptes, réseau, logiciels, imprimante, sécurité)
- Rédaction d'une fiche de mise en service
- Inventaire, sauvegarde et vérification d'intégrité des données
- Réinstallation d'un poste et restauration des données
- Rédaction d'une procédure de migration réutilisable

## Sommaire

- [Projet 1 — Poste de travail pour un cabinet comptable](#projet-1--poste-de-travail-pour-un-cabinet-comptable)
- [Projet 3 — Migration et sauvegarde d'un poste existant](#projet-3--migration-et-sauvegarde-dun-poste-existant)

---

## Projet 1 — Poste de travail pour un cabinet comptable

**Contexte :** livrer un poste prêt à l'emploi pour un comptable, de l'installation du système jusqu'à la fiche de mise en service.

**Étapes :** 
1. Installer le système d'exploitation depuis un support fourni.
2. Créer un compte administrateur local et un compte utilisateur standard.
3. Configurer le réseau (IP, nom de machine, groupe de travail).
4. Installer les logiciels métier demandés par le formateur.
5. Ajouter une imprimante réseau partagée et tester une impression.
6. Vérifier l'activation des mises à jour, de l'antivirus et du pare-feu.
7. Rédiger une fiche de mise en service.






---

**1. Installer le système d'exploitation depuis un support fourni. (VMware Workstation)**


![Capture d'écran 2026-09-08 152756](https://hackmd.io/_uploads/By5I9zrYMe.png)

![image](https://hackmd.io/_uploads/rJ12jzrKzx.png)

![image](https://hackmd.io/_uploads/BygN3MHKze.png)

![image](https://hackmd.io/_uploads/rkVD2GSFzx.png)
(Il faut réserver au minimum 64GB pour installer Windows 11)

![image](https://hackmd.io/_uploads/HyVYhMrFGl.png)

![image](https://hackmd.io/_uploads/SJfIpGBKGx.png)

![image](https://hackmd.io/_uploads/Hy3aafBYMe.png)

![image](https://hackmd.io/_uploads/ry53G7HYMe.png)

Pour passer les étapes qui correspondent à la connexion de votre compte Microsoft, faire Shift + F10 et entrer la commande : 
```
oobe\bypassnro
```

> Sur les builds récentes de Windows 11, cette commande peut ne plus fonctionner (Microsoft l'a retirée).


Le téléchargement continue : 
![image](https://hackmd.io/_uploads/HyaZ2QSYfx.png)


---

**2. Créer un compte administrateur local et un compte utilisateur standard.**


Le compte administrateur créé au lancement de windows 11 : 
![image](https://hackmd.io/_uploads/SJbyd4rKfl.png)

![image](https://hackmd.io/_uploads/S17Q_4rtfe.png)

On arrive sur le bureau Windows 11 :
![image](https://hackmd.io/_uploads/H1I6PuBYfl.png)


On peut consulter le compte dans Paramètres → Comptes
![image](https://hackmd.io/_uploads/HJ-fYEHYfg.png)


Aller dans Paramètres → Comptes → Autres utilisateurs pour créer un compte utilisateur.
![image](https://hackmd.io/_uploads/B1vbq4HYfl.png)

Puis cliquer sur ajouter un compte : 
![image](https://hackmd.io/_uploads/S1IH9NBKze.png)

Cliquer sur "Je ne dispose pas des informations de connexion de cette personne" : 
![Capture d'écran 2026-09-14 111300](https://hackmd.io/_uploads/HyFyiNSFMe.png)

Création du compte utilisateur : 
![image](https://hackmd.io/_uploads/r1283VBtfx.png)

Le compte a bien été créé : 
![image](https://hackmd.io/_uploads/B1vdhNrFfl.png)


---

**3. Configurer le réseau (IP, nom de machine, groupe de travail).**


Son nom :
![image](https://hackmd.io/_uploads/rkfRhVHYMl.png)

Groupe de travail : 
Faire Win + R et écrire : "sysdm.cpl"
![image](https://hackmd.io/_uploads/ry48TEHYzg.png)

Modifier si besoin : 
![image](https://hackmd.io/_uploads/HkaYTVHtGx.png)

Configurer l'adresse IP : 
Aller dans Paramètres → Réseau et Internet → Ethernet
Modifier l'attribution de l'adresse IP si besoin
ou laisser "Automatique", elle est indiquée un peu plus bas (Adresse IPv4)
![Capture d'écran 2026-09-14 112837](https://hackmd.io/_uploads/rklAC4rtzl.png)

Et autoriser le partage :
![Capture d'écran 2026-09-14 135323](https://hackmd.io/_uploads/ByzjgwBtzx.png)



---

**4. Installer les logiciels métier demandés par le formateur.**

Logiciel de bureautique de base : 

Sur le Microsoft Store :
![image](https://hackmd.io/_uploads/Bk_geSrYGx.png)
![image](https://hackmd.io/_uploads/H1NlWSBKzl.png)

Se connecter et c'est bon : 
![image](https://hackmd.io/_uploads/Sy9t-SBFGe.png)


![image](https://hackmd.io/_uploads/SJugfHSKGg.png)
![Capture d'écran 2026-09-14 115532](https://hackmd.io/_uploads/ByW0NSrFGg.png)


---
**5. Ajouter une imprimante réseau partagée et tester une impression.**


J'ajoute une imprimante (Fictive) : 
![image](https://hackmd.io/_uploads/SJZlaUrtze.png)

Choisir le pilote (Generic ou Microsoft) :
![image](https://hackmd.io/_uploads/ByF4TIBYfe.png)


Je lui donne un nom : 
![image](https://hackmd.io/_uploads/HJy_pLrYfx.png)

![image](https://hackmd.io/_uploads/B1MnaLHFze.png)

Et je la partage : 
![image](https://hackmd.io/_uploads/Hkee0UHKfe.png)


Connexion réussie : 
![image](https://hackmd.io/_uploads/HklNLDHKMg.png)



Test avec l'imprimante de base (Print to PDF) :

> L'imprimante partagée étant fictive dans ce lab, le test valide la procédure d'impression avec l'imprimante virtuelle « Microsoft Print to PDF ». Sur un vrai réseau, on testerait l'impression depuis un second poste via le chemin UNC.

![image](https://hackmd.io/_uploads/HJJ18HSFMe.png)

Cliquer sur "imprimer une page de test" : 
![image](https://hackmd.io/_uploads/H17tUBSYGe.png)

Je la retrouve ici :
![image](https://hackmd.io/_uploads/BJx6USrYGg.png)
![image](https://hackmd.io/_uploads/B1yZwSBtGg.png)


---



**6. Vérifier l'activation des mises à jour, de l'antivirus et du pare-feu.**


Des mises à jour sont disponibles : 
![image](https://hackmd.io/_uploads/HkCJULHKzl.png)
![image](https://hackmd.io/_uploads/Hk-auLrtMe.png)

Pare-feu et Antivirus : 
![image](https://hackmd.io/_uploads/rye0iYLBYfg.png)
Tout est bien activé et mis à jour :+1: 
Aucune action n'est requise.


---


**7. Rédiger une fiche de mise en service.**

### Fiche de mise en service

#### 1. Informations générales

|                           |                                        |
| ------------------------- | -------------------------------------- |
| Technicien                | Omorodion Erwan                     |
| Date de mise en service   | 14/09/2026                     |
| Référence du TP / dossier | https://github.com/Satom-IT-Learning-Solutions/E1A/blob/main/01-it-essentials-187/projet-1-poste-cabinet-comptable.md                       |
| Type de poste             | Machine virtuelle (VMware Workstation) |
| Rôle du poste             | Poste de bureautique pour un Comptable                       |

#### 2. Système d'exploitation

| Champ | Valeur |
|---|---|
| OS installé | Windows 11  |
| Édition | ☒ Pro   ☐ Famille   ☐ Entreprise |
| Méthode d'installation | Installation depuis ISO sur VM VMware Workstation |
| Contournement compte Microsoft | ☒ Oui (commande `oobe\bypassnro`)   ☐ Non |

#### 3. Comptes utilisateurs

| Type de compte | Nom du compte | Politique de mot de passe |
|---|---|---|
| Administrateur local | ![image](https://hackmd.io/_uploads/S1v-wDStMx.png) | Mot de passe simple (selon consigne du TP)
| Utilisateur standard | ![image](https://hackmd.io/_uploads/By-x_DBYGg.png) | Mot de passe simple (selon consigne du TP) |

#### 4. Configuration réseau

| Champ | Valeur |
|---|---|
| Nom de l'ordinateur | PC_Comptable_01 |
| Groupe de travail | GROUPE COMPTA|
| Mode réseau VMware | ☒ NAT   ☐  Bridged   ☐ Host-only |
| Type d'adressage IP | ☒ DHCP (automatique)   ☐ Statique |
| Adresse IP (si statique) | N/A (DHCP)— IP attribuée : 192.168.159.135 |
| Masque de sous-réseau | 255.255.255.0 |
| Passerelle par défaut | 	192.168.159.2 |
| Serveur DNS | 192.168.159.2|

> Amélioration : éviter le caractère « _ » dans les noms de machine (non conforme aux noms DNS) et préférer par exemple **PC-COMPTA-01**.

#### 5. Logiciels installés

| Logiciel       |
| -------------- |
| M365           |
| Microsoft edge |
| Adobe Acrobat  |

#### 6. Imprimante réseau

| Champ | Valeur |
|---|---|
| Nom de l'imprimante partagée | Imprimante_Compta|
| Chemin réseau (UNC) | `\\PC_Comptable_01\Imprimante_Compta` |
| Test d'impression | ☒ Réussi   ☐ Échoué |
| Remarque | —|

#### 7. Sécurité appliquée

| Élément | Statut | Remarque |
|---|---|---|
| Windows Update | ☒ Activé et à jour | —|
| Antivirus | ☒ Windows Defender   ☐ Autre : ……………… | —|
| Pare-feu Windows | ☒ Activé | —|


---

---

## Projet 3 — Migration et sauvegarde d'un poste existant

**Contexte :** réinstaller proprement un poste sans perdre les données et paramètres de l'utilisateur, puis formaliser une procédure réutilisable.

Étapes : 

1. Faire l'inventaire des données à conserver (documents, favoris navigateur, paramètres réseau, logiciels installés).
2. Sauvegarder ces données sur un support externe ou un espace réseau (clé USB, disque externe, ou partage réseau).
3. Vérifier l'intégrité de la sauvegarde (comparaison de tailles/nombre de fichiers).
4. Réinstaller proprement le système d'exploitation (formatage complet).
5. Réinstaller les logiciels nécessaires identifiés lors de l'inventaire.
6. Reconfigurer les paramètres essentiels (réseau, imprimante, comptes).
7. Rédiger une procédure de migration type, réutilisable pour un cas similaire.


---

**1. Faire l'inventaire des données à conserver (documents, favoris navigateur, paramètres réseau, logiciels installés).**

Documents : 

Bureau : (Aucun élément)

Téléchargements (Logiciels) :
![image](https://hackmd.io/_uploads/H1m1mpPtzg.png)

"Documents" + favoris edge : 
![image](https://hackmd.io/_uploads/SkgmsTDtze.png)

Images : Dossier "Capture d'écran"
![image](https://hackmd.io/_uploads/HyueBTPtMg.png)

Musique : (Aucun élément)
Vidéo : (Aucun élément)


Favoris Navigateur : 
![image](https://hackmd.io/_uploads/HJib8avYzx.png)


Paramètres réseau : 
Dans PowerShell : ```` ipconfig /all ````
![image](https://hackmd.io/_uploads/BJgX_6Dtze.png)
(Screen enregistré dans le dossier "Captures d'écran")


Espace utilisé :
![image](https://hackmd.io/_uploads/rJv8qTDKfg.png)

Logiciels installés : la liste peut être exportée avec `winget list > logiciels.txt` (ou relevée dans Paramètres → Applications → Applications installées) et conservée avec la sauvegarde.



---

**2. Sauvegarder ces données sur un support externe ou un espace réseau (clé USB, disque externe, ou partage réseau).**

Je vais éteindre la VM pour activer les dossiers partagés : 
![Capture d'écran 2026-09-16 100700](https://hackmd.io/_uploads/S1S9ATwYze.png)

Je vais d'abord créer un dossier de sauvegarde de la VM sur mon PC (PC Hôte) : 
![image](https://hackmd.io/_uploads/SktUxAvtMe.png)


Dans les Options de la VM je cherche "Shared Folders" et je clique sur "Always enabled" puis sur "Add" : 
![Capture d'écran 2026-09-16 101239](https://hackmd.io/_uploads/r1QhJ0vFMe.png)
Penser à **installer VMware Tools**

Je vais chercher le dossier sur le PC hôte : 
![image](https://hackmd.io/_uploads/rJxWZCPFfl.png)
![image](https://hackmd.io/_uploads/ryMQZAwKzg.png)

Le partage est bien activé : 
![image](https://hackmd.io/_uploads/ByXh-AwKzg.png)


On retrouve le dossier dans la VM :
![image](https://hackmd.io/_uploads/Bk_DLRwKGg.png)
(s'il ne s'affiche pas, taper : ```` \\vmware-host\Shared Folders\```` dans la barre de recherche de l'explorateur de fichiers)

Dans le dossier partagé, créer des sous-dossiers : 
![image](https://hackmd.io/_uploads/HyjcP0DYfg.png)

Copier les éléments dans ces dossiers : 
![image](https://hackmd.io/_uploads/rk37ORvKzx.png)


**3. Vérifier l'intégrité de la sauvegarde (comparaison de tailles/nombre de fichiers).**

Dans l'invite de commande (cmd) taper la commande :         
```` dir C:\Users\%USERNAME%\Documents /s```` pour vérifier ce que contient le dossier "Documents" par exemple.
![image](https://hackmd.io/_uploads/Bk9epADKzx.png)

Puis pareil pour le dossier partagé : 
```` dir "\\vmware-host\Shared Folders\NOMPARTAGE\Documents" /s ````
![Capture d'écran 2026-09-16 113445](https://hackmd.io/_uploads/H1DZm1dFfe.png)

On constate que le dossier est bien intégral sur la sauvegarde.

Répéter pour chaque dossier.


---


**4. Réinstaller proprement le système d'exploitation (formatage complet).**

On réinitialise le PC : 
Paramètres Windows → Système → Récupération
![image](https://hackmd.io/_uploads/BybbLkdYGe.png)

Sélectionner "Supprimer tout" : 
![image](https://hackmd.io/_uploads/HJUFI1_Yzg.png)
![image](https://hackmd.io/_uploads/Sy1iwk_Ffe.png)
![image](https://hackmd.io/_uploads/B1qiu1_Kfl.png)

Windows va se réinstaller automatiquement.

> « Réinitialiser ce PC → Supprimer tout » réinstalle Windows sans conserver aucune donnée. Pour un formatage complet au sens strict, on démarre sur un support d'installation (ISO / clé USB) et on supprime toutes les partitions avant de réinstaller.


On renomme le PC avec son nom de base (voir screen ```` ipconfig /all ```` )
![image](https://hackmd.io/_uploads/SyaQtguKMl.png)
![image](https://hackmd.io/_uploads/rklXxb_FMx.png)

On re-crée un profil (avec mot de passe) : 
![image](https://hackmd.io/_uploads/Bycc0b_YGg.png)

Nous voilà sur le bureau : 
![image](https://hackmd.io/_uploads/rytPyf_KMe.png)



---

**5. Réinstaller les logiciels nécessaires identifiés lors de l'inventaire, restaurer les données sauvegardées et vérifier leur intégrité après restauration.**

Il faut en premier temps réinstaller VMware Tools pour accéder au dossier partagé : 
![image](https://hackmd.io/_uploads/rJ6weG_Kzx.png)

On a de nouveau accès au dossier de sauvegarde : 
![image](https://hackmd.io/_uploads/BkzSMGdYGg.png)
![image](https://hackmd.io/_uploads/BJ5UMfOKfl.png)
![image](https://hackmd.io/_uploads/HyAtfG_Fzg.png)
![image](https://hackmd.io/_uploads/S1UNQzuFzg.png)

Plus qu'à transférer tous les fichiers, même manipulation que tout à l'heure : 
![image](https://hackmd.io/_uploads/r1g-XzdFzx.png)
Faire pareil avec les dossiers "Documents", "Favoris" et "Réseau".

Pour restaurer les favoris sur Microsoft Edge : 
![image](https://hackmd.io/_uploads/SkCGSMdtze.png)
Puis cliquer sur "Importer des favoris"

Ensuite cliquer sur "Importer" à "Autres emplacements d'importation"
![image](https://hackmd.io/_uploads/HJkKBMutzg.png)

Choisir "Fichier HTML" : 
![image](https://hackmd.io/_uploads/H1PcrfOKzx.png)

Sélectionner le fichier sauvegardé plus tôt : 
![image](https://hackmd.io/_uploads/SkpsSM_FGl.png)
![image](https://hackmd.io/_uploads/SyfJLfdKfg.png)


**6. Reconfigurer les paramètres essentiels (réseau, imprimante, comptes).**

Pour les paramètres réseau, plus qu'à ressortir le screen fait plus tôt : 
![image](https://hackmd.io/_uploads/SkhAUM_YMg.png)
On peut d'ailleurs voir que les fichiers sont donc bien intègres.

L'adressage IP étant en DHCP/NAT, aucune reconfiguration manuelle n'était nécessaire pour cette partie. Les éléments suivants ont en revanche dû être reconfigurés manuellement : 
1. Création des anciens comptes utilisateurs.
2. Nom de la machine (hostname)
3. Groupe de travail / domaine
4. Imprimante réseau (Si existante)



---

**7. Rédiger une procédure de migration type, réutilisable pour un cas similaire.**

### Procédure de migration type

1. **Inventaire** : documents, favoris (export HTML), paramètres réseau (`ipconfig /all`), logiciels (`winget list`), imprimantes, comptes.
2. **Sauvegarde** sur un support externe ou un partage réseau.
3. **Vérification de l'intégrité** : comparer nombre de fichiers et taille (`dir /s`).
4. **Réinstallation** du système.
5. **Reconfiguration** : nom du poste, groupe de travail/domaine, comptes utilisateurs.
6. **Réinstallation des logiciels** de l'inventaire.
7. **Restauration des données** puis nouvelle vérification.
8. **Tests** : réseau, imprimantes, accès utilisateur.

### Cas de deux machines physiques : partage réseau

Procédure légèrement différente pour créer un dossier partagé entre deux « vraies » machines.

Sur le **PC qui héberge** le dossier :

1. Clic droit sur le dossier → Propriétés → Partage → Partage avancé
2. Cocher "Partager ce dossier", donner un nom
3. Autorisations → retirer « Tout le monde » et n'accorder à l'utilisateur concerné que les droits nécessaires (Lecture, ou Modifier pour pouvoir y copier les sauvegardes)
4. OK partout
 
 
 Sur le **PC qui accède au partage** : 
 
Dans l'Explorateur de fichiers, taper : 
````
\\NOM-DU-PC-HOTE\NomDuPartage
````
ou avec l'IP si le nom ne marche pas.

**Pré-requis réseau :** 

1. Les deux PC doivent être sur le même réseau local (même Wi-Fi/switch)
2. Le Pare-feu Windows doit autoriser le partage de fichiers/imprimantes (normalement activé par défaut sur "Réseau privé", vérifier que le réseau est bien privé)
3. La découverte réseau doit être activée : Panneau de config → Centre Réseau et partage → Modifier les paramètres de partage avancés → activer "Activer la découverte de réseau" et "Activer le partage de fichiers et d'imprimantes"


---
