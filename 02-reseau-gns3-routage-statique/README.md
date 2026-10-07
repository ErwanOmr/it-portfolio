# Réseau multi-sites Cisco sous GNS3 — Routage statique, SSH & analyse Wireshark

> Projet de fin de module ICND1 (*Mission d'Ingénieur Réseau*) — Geneva Institute of Technology, Bachelor IT 1re année.

**Contexte :** interconnecter trois sites (Genève, Lausanne, Nyon) avec trois routeurs Cisco, y raccorder de vrais postes Windows 11 virtualisés, valider la connectivité de bout en bout puis sécuriser l'administration à distance des routeurs.

**Technologies :** GNS3 · Cisco IOS (c7200) · VMware Workstation · Windows 11 · MobaXterm · Wireshark

**Compétences mises en œuvre :**
- Plan d'adressage IPv4 (un réseau par lien WAN et par LAN)
- Configuration d'interfaces Cisco IOS et routage statique dans les deux sens, sauvegarde en NVRAM
- Intégration GNS3 ↔ VMware Workstation (nœuds Cloud, réseaux VMnet host-only)
- Diagnostic méthodique (ping, tracert, TTL, ARP) et résolution d'un blocage ICMP par le pare-feu Windows
- Administration à distance : Telnet puis SSHv2 (comptes locaux, clé RSA 2048 bits)
- Analyse Wireshark : identifiants Telnet capturés en clair vs session SSH chiffrée

---

**Outils** : GNS3 2.2.61 (routeurs Cisco c7200, IOS 15.2(4)M11), VMware Workstation (VM Windows 11), MobaXterm, Wireshark.

## Sommaire

1. [Topologie GNS3](#1-topologie-gns3)
2. [Configuration des interfaces](#2-configuration-des-interfaces)
3. [Configuration des postes clients](#3-configuration-des-postes-clients)
4. [Tests de connectivité](#4-tests-de-connectivité)
5. [Routage statique](#5-routage-statique)
6. [Tests inter-sites et dépannage](#6-tests-de-connectivité-inter-sites-après-routage)
7. [Accès à distance : Telnet et SSH](#7-accès-à-distance--telnet-et-ssh)
8. [Conclusion](#8-conclusion)

## 1. Topologie GNS3

![Topologie GNS3 (version finale, liens actifs)](https://hackmd.io/_uploads/H1dUT-aqzx.png)

| Équipement | Interface | Relié à |
|---|---|---|
| PC-NYON (VM Windows 11, VMnet3) | — | R3-NYO g3/0 |
| R3-NYO | g1/0 | R1-GVA g1/0 |
| PC-LSN (VM Windows 11, VMnet4) | — | R2-LSN g1/0 |
| R2-LSN | g2/0 | R1-GVA g2/0 |
| R1-GVA | g3/0 | Cloud1 (VMnet8 / NAT → Internet, non configuré dans ce TP) |

Routeurs : Cisco c7200 (GNS3). Tous les liens sont verts (nœuds démarrés).

### Plan d'adressage

| Réseau | Rôle | Équipement / interface | IP |
|---|---|---|---|
| 192.168.1.0/24 | Lien R1-GVA ↔ R3-NYO | R1-GVA g1/0 | 192.168.1.10 |
| | | R3-NYO g1/0 | 192.168.1.11 |
| 192.168.2.0/24 | Lien R1-GVA ↔ R2-LSN | R1-GVA g2/0 | 192.168.2.10 |
| | | R2-LSN g2/0 | 192.168.2.12 |
| 192.168.3.0/24 | LAN Nyon (VMnet3) | R3-NYO g3/0 (passerelle) | 192.168.3.10 |
| | | PC-NYON | 192.168.3.20 |
| 192.168.4.0/24 | LAN Lausanne (VMnet4) | R2-LSN g1/0 (passerelle) | 192.168.4.10 |
| | | PC-LSN | 192.168.4.20 |

## 2. Configuration des interfaces

### 2.1 R1-GVA — g1/0 (lien vers R3-NYO)

![Config R1-GVA g1/0](https://hackmd.io/_uploads/ByvKa-T9Gl.png)

```
R1-GVA#conf t
R1-GVA(config)#interface g1/0
R1-GVA(config-if)#ip address 192.168.1.10 255.255.255.0
R1-GVA(config-if)#no shutdown
```

Erreurs rencontrées (et corrigées) :
- `ip add 192.168.1.10` → `% Incomplete command.` : le masque est obligatoire.
- `ip add 192.168.1.10 255.255.255.255` → `Bad mask /32` : un /32 n'est pas valide sur une interface Ethernet, remplacé par /24.

Résultat : `%LINK-3-UPDOWN ... GigabitEthernet1/0, changed state to up` puis `%LINEPROTO-5-UPDOWN ... up`.

### 2.2 R3-NYO — g1/0 (lien vers R1-GVA)

![show ip interface brief + config R3-NYO](https://hackmd.io/_uploads/SkdtpbT9Ml.png)

État initial (`show ip interface brief`) : toutes les interfaces `unassigned` et `administratively down` (comportement par défaut d'un routeur Cisco).

```
R3-NYO#conf t
R3-NYO(config)#interface g1/0
R3-NYO(config-if)#ip address 192.168.1.11 255.255.255.0
R3-NYO(config-if)#no shutdown
```

Résultat : interface GigabitEthernet1/0 up / line protocol up.

### 2.3 R1-GVA — g2/0 (lien vers R2-LSN)

![Config R1-GVA g2/0](https://hackmd.io/_uploads/H1u5pZp9ze.png)

```
R1-GVA#conf t
R1-GVA(config)#interface g2/0
R1-GVA(config-if)#ip address 192.168.2.10 255.255.255.0
R1-GVA(config-if)#no shutdown
```

Résultat : interface GigabitEthernet2/0 up / line protocol up.

### 2.4 R2-LSN — g2/0 (lien vers R1-GVA)

![Config R2-LSN g2/0](https://hackmd.io/_uploads/rk956Zp5Mx.png)

```
R2-LSN#conf t
R2-LSN(config)#interface g2/0
R2-LSN(config-if)#ip address 192.168.2.12 255.255.255.0
R2-LSN(config-if)#no shutdown
```

Résultat : interface GigabitEthernet2/0 up / line protocol up.

### 2.5 R3-NYO — g3/0 (LAN PC-NYON)

![Config R3-NYO g3/0 avec erreur overlap](https://hackmd.io/_uploads/SJj5pbT9fx.png)

```
R3-NYO(config)#interface g3/0
R3-NYO(config-if)#ip address 192.168.3.10 255.255.255.0
R3-NYO(config-if)#no shutdown
```

Erreur rencontrée (et corrigée) :
- `ip add 192.168.1.13 255.255.255.0` → `% 192.168.1.0 overlaps with GigabitEthernet1/0`.
  Le réseau 192.168.1.0/24 était déjà utilisé sur g1/0 (lien vers R1). Sur un routeur, **chaque interface doit appartenir à un réseau différent** : c'est justement son rôle de relier des réseaux distincts. Si deux interfaces étaient dans le même réseau, le routeur ne saurait pas par laquelle envoyer les paquets. Solution : utiliser un nouveau réseau, 192.168.3.0/24.

Résultat : interface GigabitEthernet3/0 up / line protocol up.

### 2.6 R2-LSN — g1/0 (LAN PC-LSN)

![Config R2-LSN g1/0](https://hackmd.io/_uploads/HJ3cTbaczl.png)

```
R2-LSN(config)#interface g1/0
R2-LSN(config-if)#ip address 192.168.4.10 255.255.255.0
R2-LSN(config-if)#no shutdown
```

Résultat : interface GigabitEthernet1/0 up / line protocol up.

## 3. Configuration des postes clients

Les deux postes sont des VM Windows 11 sous VMware Workstation, reliées à GNS3 via des réseaux virtuels VMware (nœuds Cloud dans GNS3). Configuration IP statique via *Centre Réseau et partage → Modifier les paramètres de la carte → Ethernet0 → Propriétés → Protocole Internet version 4 (TCP/IPv4)*.

La passerelle est l'adresse de l'interface du routeur dans le même réseau : c'est à elle que la VM envoie tout trafic destiné à un autre réseau.

Le pare-feu Windows 11 bloque le ping (ICMP Echo) par défaut. Sur PC-NYON, il a été désactivé. Sur PC-LSN, ce réglage n'avait pas été fait : cela a provoqué l'échec du ping inter-sites traité en 6.3.

### 3.1 Connexion des VM aux réseaux VMware (VMnet3 / VMnet4)

Pour que les VM soient reliées à la topologie GNS3, leur carte réseau doit être branchée sur le même réseau virtuel VMware que le nœud Cloud correspondant dans GNS3.

Dans **VMware Workstation**, pour chaque VM : *Edit virtual machine settings → Network Adapter → Network connection → Custom: Specific virtual network* :

| VM | Rôle | Réseau VMware |
|---|---|---|
| PC01_WIN11 | PC-NYON | **VMnet3** (Host-only) |
| PC03_WIN11 | PC-LSN | **VMnet4** (Host-only) |

![Carte réseau de PC01_WIN11 sur VMnet3](https://hackmd.io/_uploads/SJMVQQa9Gg.png)

Ainsi, PC-NYON se retrouve sur le même segment que R3-NYO g3/0 (via VMnet3), et PC-LSN sur le même segment que R2-LSN g1/0 (via VMnet4). La même manipulation a été faite sur PC03_WIN11 avec VMnet4.

### 3.2 PC-NYON (VM « PC01_WIN11 »)

Carte réseau VMware : **Custom → VMnet3**.

![Config IPv4 PC-NYON](https://hackmd.io/_uploads/BkA5pW65Me.png)

| Paramètre | Valeur |
|---|---|
| Adresse IP | 192.168.3.20 |
| Masque | 255.255.255.0 |
| Passerelle par défaut | 192.168.3.10 (R3-NYO g3/0) |
| DNS préféré | 8.8.8.8 |

### 3.3 PC-LSN (VM « PC03_WIN11 »)

Carte réseau VMware : **Custom → VMnet4**.

![Config IPv4 PC-LSN](https://hackmd.io/_uploads/BkkipZT9Ml.png)

| Paramètre | Valeur |
|---|---|
| Adresse IP | 192.168.4.20 |
| Masque | 255.255.255.0 |
| Passerelle par défaut | 192.168.4.10 (R2-LSN g1/0) |
| DNS préféré | 8.8.8.8 |

## 4. Tests de connectivité

### 4.1 Ping R1-GVA → R3-NYO (lien 192.168.1.0/24)

**Premier ping**

![Ping R1-GVA vers R3-NYO — 1er essai](https://hackmd.io/_uploads/HyM2p-p9Gg.png)

**Second ping**

![Ping R1-GVA vers R3-NYO — 2e essai](https://hackmd.io/_uploads/By73pZ6qfe.png)

✅ Connectivité R1-GVA ↔ R3-NYO validée (100 %). Au premier essai, le 1er paquet est perdu le temps de la résolution ARP ; l'entrée ARP étant ensuite en cache, aucun paquet n'est perdu.

### 4.2 Ping R1-GVA → R2-LSN (lien 192.168.2.0/24)

![Ping R1-GVA vers R2-LSN](https://hackmd.io/_uploads/HJHhaZ69zx.png)

✅ Connectivité R1-GVA ↔ R2-LSN validée. Même comportement que pour R3 : 1er paquet perdu le temps de la résolution ARP, puis 100 % au second essai.

### 4.3 Ping PC-NYON → R3-NYO (passerelle 192.168.3.10)

![Ping PC-NYON vers R3-NYO](https://hackmd.io/_uploads/rk8naba5Gx.png)

```
C:\Users\ADMIN>ping 192.168.3.10
Réponse de 192.168.3.10 : octets=32 temps=8 ms TTL=255
Réponse de 192.168.3.10 : octets=32 temps=7 ms TTL=255
Réponse de 192.168.3.10 : octets=32 temps=6 ms TTL=255
Réponse de 192.168.3.10 : octets=32 temps=18 ms TTL=255
Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%)
```

✅ La VM PC-NYON joint sa passerelle R3-NYO : 4/4 réponses, 0 % de perte. Cela valide toute la chaîne VM → carte VMware VMnet3 → nœud Cloud GNS3 → R3-NYO g3/0.

Le **TTL=255** correspond à la valeur initiale utilisée par les routeurs Cisco : la réponse n'a traversé aucun routeur intermédiaire, ce qui confirme que la VM et R3-NYO sont directement dans le même réseau.

### 4.4 Ping R3-NYO → PC-NYON (192.168.3.20)

![Ping R3-NYO vers PC-NYON](https://hackmd.io/_uploads/Sk_naWT5Gg.png)

```
R3-NYO#ping 192.168.3.20
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 12/16/24 ms
```

✅ Le routeur joint la VM : 100 % dès le premier essai. Pas de `.` initial cette fois, car R3-NYO a déjà appris l'adresse MAC de la VM (table ARP) lors du ping précédent de PC-NYON vers R3.

**Connectivité LAN Nyon validée dans les deux sens.**

### 4.5 Ping PC-LSN → R2-LSN (passerelle 192.168.4.10)

![Ping PC-LSN vers R2-LSN](https://hackmd.io/_uploads/Hk9hab65Mx.png)

```
C:\Users\ADMIN>ping 192.168.4.10
Réponse de 192.168.4.10 : octets=32 temps=24 ms TTL=255
Réponse de 192.168.4.10 : octets=32 temps=19 ms TTL=255
Réponse de 192.168.4.10 : octets=32 temps=4 ms TTL=255
Réponse de 192.168.4.10 : octets=32 temps=14 ms TTL=255
Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%)
```

✅ La VM PC-LSN joint sa passerelle R2-LSN : 4/4 réponses, 0 % de perte, TTL=255 (routeur Cisco directement connecté). Chaîne VM → VMnet4 → Cloud GNS3 → R2-LSN g1/0 validée.

### 4.6 Ping R2-LSN → PC-LSN (192.168.4.20)

![Ping R2-LSN vers PC-LSN](https://hackmd.io/_uploads/r1oh6b65Me.png)

✅ Connectivité LAN Lausanne validée dans les deux sens.

## 5. Routage statique

### 5.1 Pourquoi

Un routeur ne connaît au départ que ses **réseaux directement connectés** (lignes `C` dans `show ip route`). Par exemple, R3-NYO connaît 192.168.1.0/24 et 192.168.3.0/24, mais ignore l'existence de 192.168.2.0/24 et 192.168.4.0/24. Sans route, un ping de PC-NYON vers R2-LSN partirait, mais ni l'aller ni le **retour** ne seraient possibles.

On ajoute donc des routes statiques : `ip route <réseau> <masque> <next-hop>` = « pour joindre ce réseau, envoie au routeur voisin à cette adresse ». Il faut penser **aux deux sens** : chaque routeur doit savoir joindre les réseaux distants.

| Routeur | Réseau à joindre | Next-hop |
|---|---|---|
| R3-NYO | 192.168.2.0/24, 192.168.4.0/24 | 192.168.1.10 (R1-GVA) |
| R2-LSN | 192.168.1.0/24, 192.168.3.0/24 | 192.168.2.10 (R1-GVA) |
| R1-GVA | 192.168.3.0/24 | 192.168.1.11 (R3-NYO) |
| R1-GVA | 192.168.4.0/24 | 192.168.2.12 (R2-LSN) |

### 5.2 R3-NYO

![Routes statiques R3-NYO](https://hackmd.io/_uploads/rknnp-T9fg.png)

```
R3-NYO(config)#ip route 192.168.2.0 255.255.255.0 192.168.1.10
R3-NYO(config)#ip route 192.168.4.0 255.255.255.0 192.168.1.10
R3-NYO#write memory
```

### 5.3 R2-LSN

![Routes statiques R2-LSN](https://hackmd.io/_uploads/Ska3aZ65ze.png)

```
R2-LSN(config)#ip route 192.168.1.0 255.255.255.0 192.168.2.10
R2-LSN(config)#ip route 192.168.3.0 255.255.255.0 192.168.2.10
R2-LSN#write memory
```

### 5.4 R1-GVA

![Routes statiques R1-GVA](https://hackmd.io/_uploads/Bk0n6ba9Me.png)

```
R1-GVA(config)#ip route 192.168.3.0 255.255.255.0 192.168.1.11
R1-GVA(config)#ip route 192.168.4.0 255.255.255.0 192.168.2.12
R1-GVA#write memory
```

### 5.5 Sauvegarde de la configuration

`write memory` copie la configuration courante (running-config, en RAM) dans la NVRAM (startup-config) pour qu'elle survive à un redémarrage.

Au premier `write memory`, IOS affiche `Overwrite the previous NVRAM configuration?[confirm]`. On confirme avec **Entrée** → `Building configuration... [OK]`.

## 6. Tests de connectivité inter-sites (après routage)

### 6.1 PC-NYON → R2-LSN

![Ping PC-NYON vers R2-LSN](https://hackmd.io/_uploads/H1-Ap-acfg.png)

```
C:\Users\ADMIN> ping 192.168.2.12
Réponse de 192.168.2.12 : octets=32 temps=81 ms TTL=253
... perdus = 0 (perte 0%)

C:\Users\ADMIN> ping 192.168.4.10
Réponse de 192.168.4.10 : octets=32 temps=63 ms TTL=253
... perdus = 0 (perte 0%)
```

✅ PC-NYON joint les deux interfaces de R2-LSN (lien vers R1 et LAN Lausanne) : 4/4, 0 % de perte.

### 6.2 PC-LSN → R3-NYO

![Ping PC-LSN vers R3-NYO](https://hackmd.io/_uploads/r1m0aWpqfl.png)

```
C:\Users\ADMIN> ping 192.168.1.11
Réponse de 192.168.1.11 : octets=32 temps=56 ms TTL=253
... perdus = 0 (perte 0%)

C:\Users\ADMIN> ping 192.168.3.10
Réponse de 192.168.3.10 : octets=32 temps=71 ms TTL=253
... perdus = 0 (perte 0%)
```

✅ PC-LSN joint les deux interfaces de R3-NYO (lien vers R1 et LAN Nyon) : 4/4, 0 % de perte. Le TTL=253 (255 − 2) montre que la réponse a traversé 2 routeurs : R1-GVA et R2-LSN.

### 6.3 Ping PC-NYON → PC-LSN — échec puis dépannage

![Ping PC-NYON vers PC-LSN en échec](https://hackmd.io/_uploads/HkHAp-pcGg.png)

```
C:\Users\ADMIN> ping 192.168.4.20
Délai d'attente de la demande dépassé.
...
Paquets : envoyés = 4, reçus = 0, perdus = 4 (perte 100%)
```

Analyse (élimination) :
- PC-NYON joint 192.168.4.10 (passerelle de PC-LSN) → le routage aller jusqu'au LAN Lausanne fonctionne.
- R2-LSN joint 192.168.4.20 → PC-LSN est bien joignable dans son LAN.
- PC-LSN joint R3-NYO → le chemin retour Lausanne → Nyon fonctionne.

Le réseau est donc correct : le problème est sur **PC-LSN lui-même**, qui ne répond pas aux pings venant d'un **autre réseau**. Cause suspectée : le pare-feu Windows 11 (règle ICMP absente ou limitée au sous-réseau local).

**Vérification** : désactivation du pare-feu Windows Defender sur PC-LSN (profils privé et public) via *Pare-feu Windows Defender → Personnaliser les paramètres* :

![Pare-feu désactivé sur PC-LSN](https://hackmd.io/_uploads/HyIA6ZT5Ge.png)

Nouveau ping depuis PC-NYON :

![Ping PC-NYON vers PC-LSN réussi](https://hackmd.io/_uploads/BkuAaZa5Gg.png)

✅ **Cause confirmée : le pare-feu de PC-LSN** bloquait les requêtes ICMP venant d'un autre réseau (il acceptait celles de R2-LSN, dans son sous-réseau local).

> En production, on ne désactiverait pas le pare-feu : on ajouterait une règle ciblée autorisant l'ICMP Echo entrant depuis les réseaux de l'entreprise.

**Connectivité de bout en bout PC-NYON ↔ PC-LSN validée.**

### 6.4 Traceroute PC-NYON → PC-LSN

`tracert` affiche le chemin suivi par les paquets, routeur par routeur. Il envoie des paquets avec un TTL de 1, puis 2, puis 3… : chaque routeur qui fait tomber le TTL à 0 renvoie un message ICMP « Time Exceeded », ce qui révèle son adresse.

![Tracert PC-NYON vers PC-LSN](https://hackmd.io/_uploads/SyKR6Wa5ze.png)

```
C:\Users\ADMIN>tracert 192.168.4.20
Détermination de l'itinéraire vers PC01GIT [192.168.4.20]
  1     9 ms     9 ms     9 ms  192.168.3.10
  2   118 ms    45 ms    44 ms  192.168.1.10
  3    69 ms    75 ms    75 ms  192.168.2.12
  4    84 ms    91 ms    91 ms  PC01GIT [192.168.4.20]
Itinéraire déterminé.
```

| Saut | Adresse | Équipement |
|---|---|---|
| 1 | 192.168.3.10 | R3-NYO (passerelle de PC-NYON) |
| 2 | 192.168.1.10 | R1-GVA |
| 3 | 192.168.2.12 | R2-LSN |
| 4 | 192.168.4.20 | PC-LSN (destination) |

✅ Le trafic suit bien le chemin **Nyon → Genève → Lausanne**, conformément aux routes statiques configurées. (« PC01GIT » est le nom d'hôte Windows de la VM PC-LSN.)

## 7. Accès à distance : Telnet et SSH

### 7.1 Principe

Telnet et SSH permettent d'ouvrir le terminal d'un routeur **à distance, à travers le réseau**, au lieu de passer par la console (câble direct / console GNS3).

| | Telnet | SSH |
|---|---|---|
| Port | TCP 23 | TCP 22 |
| Sécurité | Tout circule **en clair** (identifiants compris) | Tout est **chiffré** |
| Usage | Obsolète, à éviter en production | Standard actuel |

### 7.2 Configuration de base sur R3-NYO

![Config accès distant R3-NYO](https://hackmd.io/_uploads/ByiApWa5Ml.png)

```
R3-NYO(config)#enable secret cisco
R3-NYO(config)#username admin privilege 15 secret admin
R3-NYO(config)#line vty 0 4
R3-NYO(config-line)#login local
R3-NYO(config-line)#transport input telnet ssh
R3-NYO#write memory
```

| Commande | Rôle |
|---|---|
| `enable secret cisco` | Mot de passe (chiffré) du mode privilégié `enable`. Obligatoire : sans lui, IOS refuse les accès à distance. |
| `username admin privilege 15 secret admin` | Crée un compte local `admin`. `privilege 15` = droits maximum : on arrive directement en mode `#`. |
| `line vty 0 4` | Les 5 lignes virtuelles (VTY) utilisées pour les connexions à distance. |
| `login local` | Authentification avec les comptes locaux du routeur. |
| `transport input telnet ssh` | Autorise Telnet et SSH sur ces lignes. |

> Identifiants de laboratoire uniquement. En production : mots de passe robustes et **Telnet désactivé** (`transport input ssh`).

### 7.3 Connexion Telnet depuis PC-NYON (MobaXterm)

MobaXterm → *Session → Telnet* → Remote host `192.168.3.10`, port 23.

![Connexion Telnet MobaXterm vers R3-NYO](https://hackmd.io/_uploads/Bk2RaZ65zg.png)

Le routeur affiche `User Access Verification` puis demande `Username:` : l'authentification locale est bien active.

![Connexion Telnet réussie sur R3-NYO](https://hackmd.io/_uploads/HyCCpZp9fx.png)

```
User Access Verification

Username:
% Username:  timeout expired!
Username: admin
Password:
% Login invalid

Username: admin
Password:
R3-NYO#
```

Les deux premières tentatives échouent : `timeout expired` (rien saisi dans le délai de 30 s) puis `Login invalid` (faute de frappe dans le mot de passe, qui ne s'affiche pas pendant la saisie). La troisième aboutit directement sur `R3-NYO#` grâce à `privilege 15`.

✅ Accès Telnet à R3-NYO depuis PC-NYON validé.

> ⚠️ En Telnet, le nom d'utilisateur et le mot de passe circulent **en clair** sur le réseau : une capture Wireshark sur le lien suffirait à les lire. D'où l'intérêt de SSH.

### 7.4 SSH

![Génération clé RSA et activation SSH](https://hackmd.io/_uploads/rJllAWT5fl.png)

```
R3-NYO(config)#ip domain-name tp.local
R3-NYO(config)#crypto key generate rsa modulus 2048
The name for the keys will be: R3-NYO.tp.local
% Generating 2048 bit RSA keys, keys will be non-exportable...
[OK] (elapsed time was 4 seconds)
%SSH-5-ENABLED: SSH 1.99 has been enabled
R3-NYO(config)#ip ssh version 2
```

| Commande | Rôle |
|---|---|
| `ip domain-name tp.local` | Domaine interne (fictif) nécessaire pour nommer la clé RSA : `hostname.domaine` → `R3-NYO.tp.local`. |
| `crypto key generate rsa modulus 2048` | Génère la paire de clés RSA 2048 bits utilisée pour chiffrer les sessions SSH. |
| `ip ssh version 2` | Force SSH v2 (la v1 est obsolète et vulnérable). |

Le message `SSH 1.99 has been enabled` signifie que le routeur acceptait v1 **et** v2 ; après `ip ssh version 2`, seule la v2 est autorisée.

Les lignes VTY acceptent déjà SSH (`transport input telnet ssh`) et le compte local `admin` sert aussi pour SSH.

Connexion depuis PC-NYON avec MobaXterm : *Session → SSH* → Remote host `192.168.3.10`, username `admin`, port 22. À la première connexion, MobaXterm demande d'accepter la clé publique du routeur (empreinte RSA), puis le mot de passe.

![Connexion SSH MobaXterm vers R3-NYO](https://hackmd.io/_uploads/S1Gx0b69fl.png)

```
SSH session to admin@192.168.3.10
 • Direct SSH      : ✔
 • SSH compression : ✘
 • SSH-browser     : ✘ (disabled for Cisco compatibility)
 • X11-forwarding  : ✘ (disabled for Cisco compatibility)

R3-NYO#
```

✅ Accès SSH à R3-NYO validé : connexion chiffrée, authentification avec le compte local, arrivée directe en mode privilégié. MobaXterm désactive automatiquement les options non supportées par IOS (SSH-browser, X11-forwarding).

### 7.5 Accès distant sur R1-GVA

Premier essai depuis R3-NYO, avant toute configuration sur R1 :

![Telnet R3 vers R1 refusé](https://hackmd.io/_uploads/B1mgRbp5fx.png)

```
R3-NYO#telnet 192.168.1.10
Trying 192.168.1.10 ... Open
Password required, but none set
[Connection to 192.168.1.10 closed by foreign host]
```

La connexion TCP aboutit, mais R1 coupe immédiatement : aucune authentification n'est configurée. Par sécurité, IOS refuse alors tout accès distant.

Configuration de R1-GVA (même principe que R3, avec des mots de passe propres à ce routeur) :

![Config Telnet/SSH R1-GVA](https://hackmd.io/_uploads/rkSl0ZT5Gx.png)

```
R1-GVA(config)#enable secret ********
R1-GVA(config)#username admin privilege 15 secret ********
R1-GVA(config)#ip domain-name tp.local
R1-GVA(config)#crypto key generate rsa modulus 2048
The name for the keys will be: R1-GVA.tp.local
[OK] (elapsed time was 1 seconds)
%SSH-5-ENABLED: SSH 1.99 has been enabled
R1-GVA(config)#ip ssh version 2
R1-GVA(config)#line vty 0 4
R1-GVA(config-line)#login local
R1-GVA(config-line)#transport input telnet ssh
R1-GVA#write memory
[OK]
```

Test Telnet depuis R3-NYO :

![Telnet R3 vers R1 réussi](https://hackmd.io/_uploads/S1IeCbpczg.png)

```
R3-NYO#telnet 192.168.1.10
Trying 192.168.1.10 ... Open
User Access Verification
Username: (mot de passe saisi par erreur)
Password:
% Login invalid
...
[Connection to 192.168.1.10 closed by foreign host]
R3-NYO#telnet 192.168.1.10
Username: admin
Password:
R1-GVA#
```

Observations :
- `% Login invalid` : le mot de passe avait été saisi dans le champ *Username* au lieu du nom d'utilisateur `admin`. Le mot de passe `enable` est inutile ici grâce à `privilege 15`.
- Après **3 échecs** (ou timeouts), le routeur ferme la session : `closed by foreign host`. C'est une protection contre les tentatives de mot de passe à répétition.

✅ Accès Telnet de R3-NYO vers R1-GVA validé, à travers le lien 192.168.1.0/24.

Test SSH depuis R3-NYO (client SSH intégré à IOS) :

![SSH R3 vers R1 réussi](https://hackmd.io/_uploads/BJde0b65zx.png)

```
R1-GVA#exit
[Connection to 192.168.1.10 closed by foreign host]
R3-NYO#ssh -l admin 192.168.1.10
Password:
R1-GVA#
```

✅ Accès SSH de R3-NYO vers R1-GVA validé. `-l admin` indique le nom d'utilisateur ; `exit` ferme la session distante et ramène sur R3.

### 7.6 Comparaison Telnet / SSH avec Wireshark

Capture lancée dans GNS3 sur le lien **R3-NYO g1/0 ↔ R1-GVA g1/0** (clic droit sur le lien → *Start capture*), puis connexion Telnet et SSH de R3 (192.168.1.11) vers R1 (192.168.1.10).

#### Telnet (filtre `telnet`)

![Capture Telnet — liste des paquets](https://hackmd.io/_uploads/HkKxAZ65Gl.png)

On voit la poignée de main TCP (`SYN`, `SYN-ACK`, `ACK`) vers le **port 23**, puis les négociations d'options Telnet (*Will Echo*, *Do Terminal Type*, *Negotiate About Window Size*…).

Clic droit → *Suivre → Flux TCP* :

![Capture Telnet — flux TCP en clair](https://hackmd.io/_uploads/S1ol0WT5Ge.png)

```
User Access Verification
Username: aaddmmiinn
Password: ci...
```

⚠️ **Tout est lisible en clair** : le nom d'utilisateur (`admin`, chaque lettre apparaît deux fois car le routeur renvoie l'écho de chaque caractère tapé), le début du mot de passe, les messages du routeur et toutes les commandes. Le mot de passe n'apparaît qu'une fois (le routeur ne fait pas d'écho dessus), mais il circule bien en clair dans le paquet envoyé par le client. N'importe qui capable d'écouter le lien récupère les identifiants.

#### SSH (filtre `ssh`)

![Capture SSH — liste des paquets](https://hackmd.io/_uploads/SyiW0-65Mg.png)

![Capture SSH — flux TCP chiffré](https://hackmd.io/_uploads/Hk6WAW65Mg.png)

Dans le flux TCP, seules les premières lignes sont lisibles :
- la bannière de version `SSH-2.0-Cisco-1.25` ;
- la liste des algorithmes négociés : échange de clés `diffie-hellman-group-exchange-sha1`, `diffie-hellman-group14-sha1`…, clé d'hôte `ssh-rsa`, chiffrement `aes128-cbc`, `aes256-cbc`, `3des-cbc`…, intégrité `hmac-sha1`, `hmac-md5`…

Tout le reste (identifiants, commandes, réponses du routeur) est **chiffré et illisible**.

#### Conclusion

| | Telnet | SSH |
|---|---|---|
| Port | TCP 23 | TCP 22 |
| Identifiants visibles dans la capture | ✅ oui, en clair | ❌ non |
| Commandes visibles | ✅ oui | ❌ non |
| Visible en clair | tout | uniquement version + algorithmes proposés |

SSH doit être utilisé à la place de Telnet. En production, on désactiverait Telnet sur les lignes VTY avec `transport input ssh`.

## 8. Conclusion

- Topologie GNS3 de 3 routeurs Cisco c7200 (R1-GVA, R2-LSN, R3-NYO) et 2 postes Windows 11 virtualisés sous VMware (VMnet3 / VMnet4).
- Adressage IP en /24 : un réseau par lien inter-routeurs et un réseau par LAN.
- Routage statique sur les 3 routeurs, configuration sauvegardée en NVRAM.
- Connectivité validée à chaque étape : liens directs, VM ↔ passerelle, VM ↔ routeur distant, puis PC-NYON ↔ PC-LSN de bout en bout (ping + tracert).
- Accès à distance configuré sur R3-NYO et R1-GVA (comptes locaux, Telnet et SSHv2 avec clé RSA 2048 bits), testé depuis la VM (MobaXterm) et entre routeurs.
- Analyse Wireshark : Telnet transmet identifiants et commandes en clair, SSH chiffre toute la session → SSH à privilégier.
- Problèmes rencontrés et résolus : erreurs de syntaxe IOS (masque manquant, masque /32), chevauchement de réseaux (`overlaps`), pare-feu Windows bloquant l'ICMP inter-réseaux, erreurs d'authentification Telnet.
