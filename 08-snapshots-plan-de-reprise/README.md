# Snapshots et plan de reprise (RTO / RPO) d'une VM critique

> Projet du module *Mettre en place et exploiter une plateforme de virtualisation* (Module 190) — Geneva Institute of Technology, Bachelor IT 1re année.

**Contexte :** le serveur applicatif virtualisé d'une entreprise doit pouvoir être restauré rapidement après une mauvaise mise à jour ou une panne. Mise en place de snapshots, simulation d'un incident, restauration chronométrée et rédaction d'un plan de reprise.

**Technologies :** VMware Workstation · Windows 11 · Snapshots · AutoProtect

**Compétences mises en œuvre :**
- Snapshots à chaud (disque + mémoire) et fonctionnement des disques delta (.vmdk / .vmsn)
- Simulation d'une panne applicative et restauration complète de la VM (≈ 18 s mesurées)
- Automatisation d'une politique de snapshots (AutoProtect, rotation quotidienne)
- Rédaction d'un plan de reprise simplifié : RTO 15 min / RPO 24 h, procédure, rôles
- Limites des snapshots et règle de sauvegarde 3-2-1

---

## 1. Contexte

Le serveur applicatif virtualisé d'une entreprise doit pouvoir être restauré rapidement en cas de mauvaise mise à jour ou de panne. L'objectif de ce projet est de mettre en place des snapshots (instantanés), de simuler un incident, de restaurer la VM et de mesurer le temps de reprise, puis de rédiger un plan de reprise simplifié (RTO/RPO).

## 2. Environnement de test

| Élément | Valeur |
|---|---|
| Hyperviseur | VMware Workstation |
| VM de test | `PC01_WIN11` |
| Système invité | Windows 11 (vTPM + chiffrement partiel VMware) |
| Application de test | MobaXterm (client SSH/terminal) |

## 3. État initial de la VM (avant snapshot)

La VM `PC01_WIN11` est démarrée et fonctionnelle. L'application de test **MobaXterm** est installée : son raccourci est présent sur le bureau (encadré en rouge). Cet état sert de référence : c'est celui que l'on doit retrouver après la restauration.

![image](https://hackmd.io/_uploads/Hkr-DOfife.png)
*Figure 1 — État initial : PC01_WIN11 avec MobaXterm installé.*

## 4. Prise du snapshot

Le snapshot est pris **VM allumée**, juste avant la modification volontaire. VMware enregistre alors l'état du disque **et de la mémoire vive** : à la restauration, la VM revient directement sur le bureau, sans redémarrage de Windows.

**Étape 1 — Ouvrir l'assistant :** menu `VM → Snapshot → Take Snapshot…`

![image](https://hackmd.io/_uploads/r14nKufjze.png)
*Figure 2 — Accès à la prise de snapshot (VM → Snapshot → Take Snapshot).*

**Étape 2 — Nommer et décrire le snapshot :**

| Champ | Valeur |
|---|---|
| Nom | `Snapshot_1` |
| Description | État de référence : Windows 11 fonctionnel, MobaXterm installé. |

![image](https://hackmd.io/_uploads/BJ4gc_GjMl.png)
*Figure 3 — Fenêtre « Take Snapshot » : nom et description.*

**Étape 3 — Vérification :** le snapshot apparaît dans le menu `VM → Snapshot` avec sa date de création (**06/10/2026 à 15:30:27**) et l'option *Revert to Snapshot: Snapshot_1* est désormais disponible.

![image](https://hackmd.io/_uploads/SJDI5ufjGx.png)
*Figure 4 — Snapshot_1 visible dans le menu, restauration disponible.*

**Étape 4 — Snapshot Manager :** l'arborescence montre `Snapshot_1` suivi de **« You Are Here »**, ce qui signifie que l'état actuel de la VM découle de ce point de restauration.

![image](https://hackmd.io/_uploads/BJ5_5_fiMe.png)
*Figure 5 — Snapshot Manager : Snapshot_1 → You Are Here.*

> 💡 **Mécanisme :** lors d'un snapshot, VMware fige le disque virtuel d'origine (`.vmdk`) en lecture seule et crée un **disque delta** (`-000001.vmdk`) dans lequel sont écrites toutes les modifications suivantes. Le fichier `.vmsn` conserve la configuration et la mémoire. Restaurer revient à supprimer le delta et repartir de l'état figé.

## 5. Simulation de l'incident (panne applicative)

Pour simuler une panne applicative, l'exécutable principal de l'application est supprimé, comme pourrait le faire une mauvaise manipulation, une mise à jour défaillante ou un logiciel malveillant.

**Étape 1 — Suppression de l'exécutable :** dans le dossier d'installation `C:\Program Files (x86)\Mobatek\MobaXterm`, le fichier `MobaXterm.exe` (version 26.5.0.5542, 15,8 Mo) est sélectionné puis supprimé.

![image](https://hackmd.io/_uploads/HJ0v2dfifx.png)
*Figure 6 — Sélection de MobaXterm.exe dans le dossier d'installation.*

**Étape 2 — Élévation des droits :** le dossier `Program Files` étant protégé, Windows demande des droits administrateur. La suppression est confirmée avec **Continuer**.

![image](https://hackmd.io/_uploads/H1nK3dzsGl.png)
*Figure 7 — Confirmation de la suppression avec les droits administrateur.*

**Étape 3 — Constat de la panne :** au lancement de MobaXterm depuis le bureau, Windows affiche **« Problème de raccourci »** : l'élément `MobaXterm.exe` auquel renvoie le raccourci a été supprimé. **L'application est indisponible.**

![image](https://hackmd.io/_uploads/S1GshOGizg.png)
*Figure 8 — Panne constatée : « Problème de raccourci », MobaXterm indisponible.*

| Élément | Détail |
|---|---|
| Type d'incident | Panne applicative (suppression de l'exécutable) |
| Symptôme | Raccourci invalide, application impossible à lancer |
| Impact | Service MobaXterm indisponible pour l'utilisateur |
| Solution retenue | Restauration de la VM depuis `Snapshot_1` |

> ⚠️ Ici le fichier est encore dans la Corbeille, mais dans un incident réel (corruption, suppression définitive, mise à jour ratée touchant plusieurs fichiers), une réparation manuelle serait longue et incertaine. La restauration du snapshot remet **l'ensemble du système** dans un état connu et fonctionnel en une seule opération.

## 6. Restauration à partir du snapshot

**Étape 1 — Lancer la restauration :** menu `VM → Snapshot → Revert to Snapshot: Snapshot_1`.

![image](https://hackmd.io/_uploads/r16_1tMszg.png)
*Figure 9 — Lancement de la restauration (Revert to Snapshot: Snapshot_1).*

**Étape 2 — Confirmation :** VMware avertit que **l'état actuel sera perdu** (toutes les modifications faites depuis le snapshot, y compris la suppression de l'exécutable). La restauration est confirmée avec **Yes**.

![image](https://hackmd.io/_uploads/rJNckYzoMe.png)
*Figure 10 — Confirmation de la restauration.*

**Étape 3 — Vérification :** la VM revient directement sur le bureau (snapshot pris VM allumée, mémoire incluse : pas de redémarrage de Windows). Un double-clic sur le raccourci lance **MobaXterm normalement** : l'application est de nouveau fonctionnelle.

![image](https://hackmd.io/_uploads/HkOo1tziMx.png)
*Figure 11 — MobaXterm fonctionne à nouveau : service rétabli.*

✅ **Restauration réussie** : la VM est revenue exactement à l'état de référence du 06/10/2026 à 15:30:27.

## 7. Mesure du temps de restauration

**Méthode :** chronomètre démarré au clic sur **Yes** (confirmation du *Revert*) et arrêté à l'**ouverture de la fenêtre MobaXterm**, c'est-à-dire au moment où le service est de nouveau utilisable.

| Mesure | Début | Fin | Durée |
|---|---|---|---|
| Restauration de Snapshot_1 | Clic sur *Yes* | Ouverture de MobaXterm | **≈ 18 secondes** |

**Analyse :**
- Le temps est très court car le snapshot contient **l'état de la mémoire** : VMware recharge la RAM au lieu de redémarrer Windows.
- Avec un snapshot pris **VM éteinte**, il faudrait ajouter le démarrage complet de Windows 11 (de l'ordre de 1 à 2 minutes).
- Ce temps correspond uniquement à l'opération technique. Dans une situation réelle, il faut y ajouter la **détection** de la panne et la **décision** de restaurer, d'où un RTO fixé plus large (voir plan de reprise).

## 8. Automatisation : politique de snapshots réguliers (AutoProtect)

Prendre les snapshots à la main dépend de la rigueur de l'administrateur. Pour garantir un point de restauration récent en permanence, la fonction **AutoProtect** de VMware Workstation est activée : elle prend des snapshots **automatiquement à intervalle régulier** et supprime les plus anciens.

**Configuration :** `VM → Settings → Options → AutoProtect`

| Paramètre | Valeur choisie |
|---|---|
| Enable AutoProtect | ✅ Activé |
| AutoProtect interval | **Daily** (quotidien) |
| Maximum AutoProtect snapshots | **3** |

![image](https://hackmd.io/_uploads/BkfTJYGoze.png)
*Figure 12 — Configuration d'AutoProtect : quotidien, 3 snapshots max.*

**Fonctionnement (rotation) :** VMware conserve un éventail de points de restauration — 1 snapshot du jour, 1 de la semaine et 1 du mois. Quand la limite de 3 est atteinte, le plus ancien est supprimé automatiquement, ce qui évite que la chaîne de disques delta ne grossisse indéfiniment.

**Points d'attention :**
- AutoProtect consomme au minimum **12,9 Go** d'espace disque sur l'hôte : à prévoir dans le dimensionnement du stockage.
- Les snapshots ne sont pris que lorsque **la VM est allumée**.
- Les snapshots automatiques sont visibles dans le *Snapshot Manager* en cochant **« Show AutoProtect snapshots »**.
- AutoProtect **complète** les snapshots manuels pris avant une modification, il ne les remplace pas.

## 9. Plan de reprise (RTO / RPO)

**Système concerné :** serveur applicatif virtualisé `PC01_WIN11` (VMware Workstation) — application MobaXterm.

### Définitions
- **RPO** (*Recovery Point Objective*) : perte de données maximale acceptable = temps écoulé depuis le dernier snapshot.
- **RTO** (*Recovery Time Objective*) : durée maximale d'interruption acceptable avant le retour du service.

### Objectifs fixés

| Indicateur | Objectif | Justification |
|---|---|---|
| **RPO** | **24 h** | Snapshot quotidien + snapshot avant chaque modification |
| **RTO** | **15 min** | Restauration mesurée ≈ 18 s + marge pour détection, décision et vérification |

### Politique de snapshots
| Quand | Nom | Conservation |
|---|---|---|
| Avant toute mise à jour / modification | `avant-maj-AAAA-MM-JJ` | Supprimé après 48 h si tout fonctionne |
| Tous les jours — **automatique via AutoProtect** | Nommé par VMware | 3 conservés (rotation : jour / semaine / mois) |

> Les snapshots ne sont **pas conservés longtemps** : la chaîne de disques delta grossit et dégrade les performances de la VM.

### Procédure de reprise
1. **Détecter** : constat de la panne (application indisponible, erreur, mauvaise mise à jour).
2. **Décider** : vérifier qu'une réparation simple n'est pas possible, identifier le snapshot sain le plus récent.
3. **Restaurer** : `VM → Snapshot → Revert to Snapshot` → *Yes*.
4. **Vérifier** : lancement de l'application, test de fonctionnement.
5. **Documenter** : date, cause, snapshot utilisé, temps de reprise, données perdues.

### Rôles
| Rôle | Responsabilité |
|---|---|
| Administrateur système | Prise des snapshots, restauration, vérification |
| Responsable / utilisateur | Signalement de la panne, validation du retour du service |

### Limites
⚠️ **Un snapshot n'est pas une sauvegarde.** Il dépend du disque d'origine et est stocké sur le même support : en cas de panne du disque de l'hôte, de corruption du `.vmdk` ou de perte de la VM, les snapshots sont perdus aussi. Ce plan doit être complété par une **sauvegarde externe** régulière de la VM (règle 3-2-1 : 3 copies, 2 supports, 1 hors site).

## 10. Conclusion

Ce projet a permis de mettre en œuvre le mécanisme des snapshots sous VMware Workstation : prise d'un snapshot de référence, simulation d'une panne applicative (suppression de `MobaXterm.exe`), puis restauration complète de la VM en **environ 18 secondes**. Une politique de snapshots réguliers a ensuite été automatisée avec **AutoProtect**.

Le snapshot est un outil de reprise **rapide et efficace** face à une mauvaise manipulation ou une mise à jour défaillante, ce qui permet de fixer un RTO court. En revanche, il ne protège pas contre la perte du stockage : il doit être intégré dans une politique plus large incluant des **sauvegardes externes**.
