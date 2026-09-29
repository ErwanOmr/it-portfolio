# Poste multi-utilisateurs pour salle informatique

> Projet 2 — module *IT Essentials*, Geneva Institute of Technology, Bachelor IT 1re année.

**Contexte :** préparer un poste Windows partagé par plusieurs utilisateurs dans une salle informatique : comptes séparés, cloisonnement des données, verrouillage automatique et politique de mots de passe.

**Technologies :** Windows 11 Pro · PowerShell · icacls · Stratégie de groupe locale (gpedit.msc) · Stratégie de sécurité locale (secpol.msc)

**Compétences mises en œuvre :**
- Gestion des comptes locaux et des groupes (administrateur de maintenance, utilisateurs standards)
- Analyse et vérification des permissions NTFS (icacls)
- Configuration de stratégies de sécurité : verrouillage après inactivité, politique de mots de passe
- Tests de cloisonnement entre utilisateurs

---

Étapes : 

1. Installer le système d'exploitation.
2. Créer un compte administrateur dédié à la maintenance.
3. Créer au moins 3 comptes utilisateurs standards.
4. Configurer les permissions pour cloisonner les fichiers personnels.
5. Mettre en place le verrouillage automatique après inactivité.
6. Définir une politique de mot de passe.
7. Tester le cloisonnement avec chaque compte.

---

**1. Installer le système d'exploitation.**

Lien HackMD vers comment installer le système d'exploitation (Windows 11) : 
[Fondamentaux — TP5 : Installation de Windows 11](../07-fondamentaux-hardware-os/README.md#tp5--installation-de-windows-11-vmware-workstation)


---

**2. Créer un compte administrateur dédié à la maintenance.**

Création du compte "administrateur" juste après l'installation de Windows 11 (Mettre un nom) :
![image](https://hackmd.io/_uploads/Hkt41uLYMl.png)

Définition du MDP de "l'administrateur" : 
![image](https://hackmd.io/_uploads/rkw91_UYzg.png)

Le compte "Admin" est créé : 
![image](https://hackmd.io/_uploads/Hk_rZ_Itzl.png)


Je vais créer un compte Administrateur à part dédié exclusivement à la maintenance : Paramètres → Comptes → Autres utilisateurs → Ajouter un compte : 
![image](https://hackmd.io/_uploads/r1a8X_IYfl.png)

Choisir le Nom et le Mot de passe : 
![image](https://hackmd.io/_uploads/SJC1Bd8Ffl.png)
(Ne pas oublier de remplir les questions de sécurité en dessous)

Le compte est créé : 
![image](https://hackmd.io/_uploads/By0SB_UFfg.png)

Ajout du Compte "ADMIN Maintenance" en tant qu'administrateur : 

Dans Powershell : 
````
net localgroup Administrateurs "ADMIN Maintenance" /add
````
![image](https://hackmd.io/_uploads/S1_g2_ItMg.png)

Vérification des droits administrateurs du compte : 

Dans Powershell  sur le compte "ADMIN Maintenance": 
````
whoami /groups
````
![Capture d'écran 2026-09-15 100727](https://hackmd.io/_uploads/BkzoaOUYfl.png)

Le compte "ADMIN Maintenance" est bien Administrateur.
![image](https://hackmd.io/_uploads/HkKYRYItfe.png)



Je peux même changer la nature du compte de base "admin (nom)" : 
![image](https://hackmd.io/_uploads/SyeJytItzg.png)



---


**3. Créer au moins 3 comptes utilisateurs standards distincts.**

Création des 3 comptes utilisateurs : 
Paramètres → Comptes → Autres utilisateurs → Ajouter un compte
![image](https://hackmd.io/_uploads/HJKMyFItfg.png)

Se connecter à Microsoft ou sélectionner "Je ne dispose pas des informations de connexion" : 
![image](https://hackmd.io/_uploads/SJOvkYIKfg.png)

Créer un compte Microsoft ou ajouter sans compte : 
![image](https://hackmd.io/_uploads/H1leeYIYzg.png)

Choisir le nom et le mot de passe : 
![image](https://hackmd.io/_uploads/Sy9EgF8KGx.png)
(Ne pas oublier de remplir les questions de sécurité en dessous)

Le compte utilisateur est créé : 
![image](https://hackmd.io/_uploads/rkp3xFItGl.png)

Répéter le processus pour le compte "User2" et "User3" : 
![image](https://hackmd.io/_uploads/rJI7-F8tfx.png)
![image](https://hackmd.io/_uploads/HyywbYIKfx.png)

Tous les comptes sont créés : 
![image](https://hackmd.io/_uploads/ByqeMYLFMx.png)

Je vais renommer le compte "Admin (nom)" pour éviter la confusion vu qu'il n'a plus les droits administrateurs     : 

Dans Powershell : 
````
Rename-LocalUser -Name "admin (nom)" -NewName "OOBE"
````
Ce compte s'appelle maintenant "OOBE" : 
![Capture d'écran 2026-09-15 103927](https://hackmd.io/_uploads/rkMdVY8YGe.png)

Une fois le compte de maintenance opérationnel, ce compte peut être désactivé : `net user OOBE /active:no`


---

**4. Configurer les permissions pour cloisonner les fichiers personnels.**

Bonne nouvelle Windows fait déjà ça par défaut, je vais quand même vérifier : 

Vérifier les permissions (je vais tester avec "User1") : 
Dans Powershell : 
````
icacls "C:\Users\User1"
````
![image](https://hackmd.io/_uploads/Skt_YtItGx.png)

Ce que ça montre : 

--> AUTORITE NT\Système:(F) → le système a accès complet (normal)
--> BUILTIN\Administrateurs:(F) → les admins ont accès complet
--> DESKTOP-BQMA20M\User1:(F) → User1 a accès à son propre dossier

Aucun autre compte standard n'a de droit sur le dossier de User1 : le cloisonnement est déjà mis en place par Windows.

Test depuis le compte "User1" vers "User2" : 
![Capture d'écran 2026-09-15 111607](https://hackmd.io/_uploads/rJX4TFItze.png)
L'accès est bien refusé.

Autre test : 

Je crée un dossier test chez "User1" : 
![image](https://hackmd.io/_uploads/S1b5-cUtze.png)

Depuis "User2" ou "User3" j'essaye d'accéder au dossier : 
![Capture d'écran 2026-09-15 114014](https://hackmd.io/_uploads/S1zxEcUKGe.png)


Et si on clique sur "Continuer" : 
![image](https://hackmd.io/_uploads/Bk0BXcUtGg.png)
Le mot de passe Administrateur nous est demandé

On peut aussi voir ici qui a accès au dossier (User2 dans ce cas) : 
![image](https://hackmd.io/_uploads/HknU8TUFMl.png)
![image](https://hackmd.io/_uploads/S1QFUTLKMx.png)

> ⚠️ **Point d'attention :** en validant cette demande avec le mot de passe administrateur, Windows ajoute **définitivement** User2 aux autorisations du dossier : le cloisonnement est rompu pour ce dossier. Il faut retirer cette autorisation (Propriétés → Sécurité) et ne jamais valider ce type de demande pour un utilisateur standard.


---

**5. Mettre en place le verrouillage automatique après inactivité.**

Depuis le bureau Administrateur faire WIN + R et taper : 
````
gpedit.msc
````

On arrive ici : 
![image](https://hackmd.io/_uploads/H1fo89UKGx.png)

On cherche : Configuration utilisateur → Modèles d'administration → Panneau de configuration → Personnalisation
![image](https://hackmd.io/_uploads/SJAGvqUtzl.png)


Activer ces paramètres (Clic-Droit puis "Modifier") : 
![Capture d'écran 2026-09-15 133725](https://hackmd.io/_uploads/B1oNAsUtzx.png)

![Capture d'écran 2026-09-15 134113](https://hackmd.io/_uploads/SJGW1nItMg.png)

 
Et configurer le délai (Clic-Droit puis "Modifier") : 
![Capture d'écran 2026-09-15 133945](https://hackmd.io/_uploads/B12oRoUKze.png)

Ces paramètres de stratégie locale (partie *Configuration utilisateur*) s'appliquent à tous les comptes de la machine : pas besoin de répéter l'opération, il suffit de vérifier sur chaque compte (User1, User2, User3) que le verrouillage fonctionne.

Alternative au niveau machine : Configuration ordinateur → Paramètres Windows → Paramètres de sécurité → Stratégies locales → Options de sécurité → « Ouverture de session interactive : limite d'inactivité de l'ordinateur ».

---


**6. Définir une politique de mot de passe minimale (longueur, complexité).**


Depuis le bureau Administrateur faire WIN + R et taper : 
````
secpol.msc
````
On arrive ici : 
![image](https://hackmd.io/_uploads/BkDg-n8Ffe.png)


On cherche : Stratégies de comptes → Stratégie de mot de passe
![image](https://hackmd.io/_uploads/HkdXZh8KGe.png)

On peut modifier les caractéristiques du mot de passe  (Activer ou désactiver): 
![Capture d'écran 2026-09-15 140204](https://hackmd.io/_uploads/SkYJ4nIFMl.png)
![image](https://hackmd.io/_uploads/HyI442UFfx.png)
(Sur ma capture de test : 5 caractères minimum. Politique recommandée en production : 12 caractères minimum et exigences de complexité activées.)

Ici pas besoin de répéter le processus pour chaque utilisateur, les modifications s'appliquent à toute la machine.


---

**7. Tester le cloisonnement avec chaque compte.**

Vidéo test : 
*(Vidéo de démonstration disponible sur demande)*


---
| Compte | Type | Rôle |
|---|---|---|
| `OOBE` |Ancien Administrateur local | Compte créé automatiquement lors de l'installation de Windows (OOBE). Neutralisé/renommé après mise en place du compte de maintenance dédié. |
| `ADMIN Maintenance` | Administrateur local | Compte administrateur **dédié à la maintenance**, non utilisé au quotidien. Ajouté explicitement au groupe `Administrateurs`. |
| `User1` | Utilisateur standard | Compte utilisateur distinct n°1 |
| `User2` | Utilisateur standard | Compte utilisateur distinct n°2 |
| `User3` | Utilisateur standard | Compte utilisateur distinct n°3 |



- Le compte **OOBE** ne doit plus être utilisé une fois `ADMIN Maintenance` opérationnel — il sert uniquement de compte de secours ou est désactivé.
- Le compte **ADMIN Maintenance** est réservé à l'administration (installation de logiciels, gestion des comptes, configuration système) et n'est pas utilisé quotidiennement.
- Les comptes **User1 / User2 / User3** sont des comptes standards, sans droits d'administration, destinés à l'usage courant.


Chaque profil utilisateur (`C:\Users\User1`, `C:\Users\User2`, `C:\Users\User3`) doit être accessible uniquement par :
- son propriétaire (l'utilisateur concerné),
- le groupe `Administrateurs`,
- le compte `SYSTEM`.
Aucun autre compte standard ne doit pouvoir consulter le contenu du profil d'un autre utilisateur.

---
