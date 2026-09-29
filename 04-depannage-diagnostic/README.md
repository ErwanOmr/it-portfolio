# Dépannage et diagnostic d'un poste en panne

> Projet 4 — module *IT Essentials*, Geneva Institute of Technology, Bachelor IT 1re année.

**Contexte :** simuler des pannes courantes sur un poste Windows, les diagnostiquer avec les outils adaptés, les résoudre, puis rédiger un ticket d'intervention au format professionnel.

**Technologies :** Windows 11 · PowerShell (fsutil) · Gestionnaire de périphériques · Paramètres de stockage · Observateur d'événements

**Compétences mises en œuvre :**
- Méthodologie de dépannage : symptôme → diagnostic → action → vérification
- Simulation contrôlée de pannes (saturation disque, pilote manquant)
- Rédaction de tickets d'intervention (symptômes, cause, actions, résolution, recommandations)

---

## Démarche

1. Recevoir ou choisir un scénario de panne (pilote manquant, disque plein)
2. Reproduire ou simuler la panne sur un poste de test si nécessaire.
3. Utiliser les outils de diagnostic appropriés (observateur d'événements, gestionnaire de périphériques, moniteur de ressources, etc.) pour identifier la cause.
4. Résoudre le problème identifié et vérifier que le poste fonctionne normalement.
5. Rédiger un rapport d'intervention (ticket) au format professionnel : symptôme, diagnostic, actions, résolution, recommandations.

J'ai choisi de prendre un scénario de disque plein et de pilote manquant car elles couvrent deux couches différentes du dépannage informatique (système/stockage et matériel/logiciel) et elles sont représentatives de pannes réelles et fréquentes.

---

## Scénario 1 — Disque plein

**2. Reproduire ou simuler la panne sur un poste de test si nécessaire.**


Remplissage du disque via création d'un fichier volumineux : 
![image](https://hackmd.io/_uploads/HJ0cqGKtGg.png)



Je crée un dossier "test" : 
![image](https://hackmd.io/_uploads/BJntBzttze.png)


Puis je force la saturation du disque :

Commande à taper sur PowerShell : 
````
fsutil file createnew C:\test\rempli.tmp 50000000000
````
Là, le "50000000000" est égal à 50 GB (5 × 10¹⁰ octets)
(fsutil fonctionne uniquement avec une taille en octets entiers et 1 Go = 1 000 000 000 octets)

Dans mon cas, un fichier de 21 GB (21 000 000 000 octets) suffit à saturer le disque de la VM :
![image](https://hackmd.io/_uploads/S1GeizKYMx.png)

On peut facilement voir que le disque est saturé : 
![image](https://hackmd.io/_uploads/S1zsjGKYfl.png)

Constatation  des symptômes : 
si par exemple j'essaye de copier et de dupliquer le dossier "test2" (environ 1,5 GB), c'est impossible.
![image](https://hackmd.io/_uploads/H1jSzmFFGx.png)

Si j'essaye de faire la mise à jour Windows : 
![image](https://hackmd.io/_uploads/Bk9AmXYFfe.png)

Ou encore d'installer un quelconque logiciel (VScode par exemple) : 
![image](https://hackmd.io/_uploads/BJxu9XYtfx.png)
![image](https://hackmd.io/_uploads/SyWC5XttMx.png)


---

**3. Utiliser les outils de diagnostic appropriés pour identifier la cause.**

En tant que technicien, on va aller regarder dans les Paramètres → Système → Stockage et on regarde où le stockage est le plus utilisé : 
![image](https://hackmd.io/_uploads/ryreD7YKGe.png)

Par exemple dans la catégorie "Autre", on voit bien que le dossier "test" prend beaucoup de place : 
![image](https://hackmd.io/_uploads/Hy-X_XFFGg.png)
Alors que c'est un fichier non nécessaire ("test2" aussi)


Dans les "Fichiers temporaires", la majorité de l'espace utilisé vient de mises à jour Windows, donc à laisser.

Pour la catégorie "Applications installées", on peut défiler pour chercher des applications qu'on n'utilise plus ou qui sont obsolètes.

Autres outils utiles : l'**Observateur d'événements** (eventvwr.msc → Journaux Windows → Système : avertissements d'espace disque), le **Moniteur de ressources** (onglet Disque) ou un outil comme WinDirStat / TreeSize pour repérer les dossiers les plus volumineux.



---

**4. Résoudre le problème identifié et vérifier que le poste fonctionne normalement.**

On supprime le dossier "test" et "test2" : 
![image](https://hackmd.io/_uploads/B1iuZVFFGe.png)
![image](https://hackmd.io/_uploads/SJmq-EYYMx.png)

On constate que les fichiers étaient effectivement très volumineux et saturaient le disque dur : 
![image](https://hackmd.io/_uploads/B1WkGVtKzx.png)

Maintenant si j'essaye d'installer un logiciel (VScode) :
![image](https://hackmd.io/_uploads/Skewm4ttGx.png)
![image](https://hackmd.io/_uploads/H1Z57EKFzl.png)
On constate que l'installation s'est terminée sans problèmes.


On peut également supprimer les fichiers temporaires pour un peu plus d'espace : 
![image](https://hackmd.io/_uploads/BkPJYNttMg.png)



---

**5. Rédiger un rapport d'intervention (ticket) au format professionnel : symptôme, diagnostic, actions, résolution, recommandations.** 

| Champ | Détail |
|---|---|
| **Référence ticket** | EGIT-2026-001 |
| **Date d'ouverture** | 17/09/2026 |
| **Date de résolution** | 17/09/2026 |
| **Technicien** | Omorodion Erwan |
| **Poste concerné** | "DESKTOP-BQAM20M" |
| **Priorité** | Moyenne |
| **Catégorie** | Système / Stockage |

**Symptômes observés :**

- Échec de copie de fichier vers `C:\`
- Message d'erreur lors d'une tentative d'installation de logiciel
- Barre d'espace disque au rouge dans l'Explorateur de fichiers
- Notification système « Espace disque faible »

**Recherche de la cause :**

Analyse de la répartition de l'espace disque via Paramètres → Système → Stockage, puis investigation détaillée par dossier

**Cause identifiée :** 
Présence d'un fichier volumineux non nécessaire, saturant l'espace disque disponible sur le lecteur.

**Actions réalisées :**

| # | Action | Résultat |
|---|---|---|
| 1 | Suppression des dossiers de test volumineux (`C:\test` et `test2`) | Espace disque libéré |
| 2 | Nettoyage des fichiers temporaires système | Espace supplémentaire récupéré |
| 3 | Vérification de l'espace disque après le nettoyage | Retour à la normale |

**Résolution :** espace disque libéré ; copies de fichiers, installations de logiciels et mises à jour Windows de nouveau fonctionnelles.

**Recommandations :**
- Surveiller l'espace disque (alerte en dessous de 10-15 % d'espace libre)
- Activer l'Assistant de stockage (Storage Sense) pour supprimer automatiquement les fichiers temporaires
- Stocker les fichiers volumineux sur un partage réseau ou un disque de données plutôt que sur C:\
- Sensibiliser l'utilisateur au nettoyage régulier de ses dossiers (Téléchargements, etc.)



---

---

## Scénario 2 — Pilote audio manquant

**2. Reproduire ou simuler la panne sur un poste de test si nécessaire.**


On va désinstaller le pilote de la carte son : 

Aller dans Panneau de Configuration --> Matériel et Audio --> Gestionnaire de Périphériques : 
(Ou ```` devmgmt.msc ```` dans cmd)
![image](https://hackmd.io/_uploads/r1oY6SFFGg.png)

Dans "Contrôleurs audio, vidéo et jeu" désinstaller le pilote "Périphérique High Definition Audio" : 
![image](https://hackmd.io/_uploads/rytW0BYFzl.png)
![image](https://hackmd.io/_uploads/HJD9AStYfx.png)

On peut constater qu'il n'y a plus de périphérique audio disponible : 
![image](https://hackmd.io/_uploads/rkRHy8tYfe.png)
("Aucun périphérique de sortie n'a été trouvé")

Ou si j'essaye d'ajuster le volume du système la fenêtre m'affiche : "Aucun périphérique audio n'est installé" : 
![image](https://hackmd.io/_uploads/HyphJUtKMl.png)

De même si j'essaye de jouer un son (vidéo, musique), il n'y aura aucun son ou un message d'erreur.

On peut donc ici identifier le problème assez facilement.

---


**3. Utiliser les outils de diagnostic appropriés pour identifier la cause.**

En tant que technicien, on va aller regarder dans le Panneau de Configuration --> Matériel et Audio --> Gestionnaire de Périphériques, et constater qu'il n'y a aucune catégorie mentionnant l'audio : 
![image](https://hackmd.io/_uploads/S1KMB8Ytzx.png)
Donc le ou les pilotes audio ne sont pas installés

Ou encore dans les Paramètres --> Système --> Son : 
![image](https://hackmd.io/_uploads/HyVpB8YYGg.png)


---


**4. Résoudre le problème identifié et vérifier que le poste fonctionne normalement.**

Dans le gestionnaire de périphériques, on va cliquer sur le nom du PC (DESKTOP-BQAM20M), puis cliquer sur l'icône "Rechercher les modifications sur le matériel" :
![image](https://hackmd.io/_uploads/SJwtu8KFGg.png)
(Windows relance une détection complète du matériel présent, et le réinstalle automatiquement)

Le périphérique audio réapparaît et est fonctionnel : 
![image](https://hackmd.io/_uploads/rJAGYIYYze.png)
![image](https://hackmd.io/_uploads/B1Cyq8ttGl.png)


---

**5. Rédiger un rapport d'intervention (ticket) au format professionnel : symptôme, diagnostic, actions, résolution, recommandations.**

| Champ | Détail |
|---|---|
| **Référence ticket** | EGIT-2026-002 |
| **Date d'ouverture** | 17/09/2026 |
| **Date de résolution** | 17/09/2026 |
| **Technicien** | Omorodion Erwan |
| **Poste concerné** | "DESKTOP-BQAM20M" |
| **Priorité** | Basse |
| **Catégorie** | Matériel / Pilote |

**Symptômes observés :**
- Icône de haut-parleur avec croix dans la barre des tâches
- Message « Aucun périphérique audio n'est installé » au survol de l'icône
- Aucun son lors de la lecture d'un fichier audio ou vidéo de test

**Recherche de la cause :** 

Vérification dans le Gestionnaire de périphériques :  la carte son n'apparaît plus ni la catégorie "Contrôleurs Audio"

**Cause identifiée :**

Le périphérique audio et son pilote ont été désinstallés : Windows ne dispose plus d'aucun pilote pour la carte son.

**Actions réalisées :**

| # | Action | Résultat |
|---|---|---|
| 1 | Ouverture du Gestionnaire de périphériques | Constat : la catégorie audio a disparu |
| 2 | Réinstallation automatique du pilote | Pilote réinstallé avec succès |
| 3 | Vérification du statut du périphérique | Statut OK |
| 4 | Test fonctionnel (lecture d'un son) | Son rétabli |

**Résolution :** pilote réinstallé automatiquement par Windows, son de nouveau fonctionnel.

**Recommandations :**
- Créer un point de restauration avant toute modification de pilotes
- Limiter les droits administrateur des utilisateurs pour éviter les désinstallations accidentelles
- Si la détection automatique échoue, installer le pilote depuis le site du fabricant
- Consulter l'Observateur d'événements (journal Système) pour dater l'incident



---
