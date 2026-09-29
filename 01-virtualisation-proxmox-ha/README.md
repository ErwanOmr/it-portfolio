# Virtualisation — Dimensionnement, POC multi-hyperviseurs et cluster Proxmox HA

> Projet de fin de module *Mettre en place et exploiter une plateforme de virtualisation* (Module 190) — Geneva Institute of Technology, Bachelor IT 1re année.

**Contexte :** concevoir la future plateforme de virtualisation d'une PME de transport et logistique (cas d'étude *Léman Transports & Logistique SA*) : dimensionner les serveurs, comparer deux hyperviseurs, puis monter un cluster haute disponibilité.

**Technologies :** Proxmox VE · VMware ESXi · VMware Workstation · Corosync · NFS · vzdump

**Compétences mises en œuvre :**
- Dimensionnement CPU / RAM / stockage (overcommit vCPU, marge de croissance, N+1, RAID 10)
- Choix d'un processeur serveur (threads, sockets, TDP, coût, architecture x86 vs ARM)
- Installation d'hyperviseurs de type 1 en virtualisation imbriquée (ESXi, Proxmox VE)
- Cluster Proxmox : quorum, Corosync, séparation des réseaux MGMT / CLUSTER / STORAGE
- Stockage partagé NFS, haute disponibilité (HA), migration à chaud, sauvegarde

---

### CAHIER DES CHARGES 1 — Dimensionnement de la plateforme et choix du processeur


### **1.4 Travail demandé**
**Étape 1 — Besoin CPU**

1. vCPU Critiques : 2+2+8+8 = 20 vCPUs
   vCPU standard : 4+8+8+2+2+4+4+2+2+2+2 = 40 vCPUs
   
2. vCPU Critiques : (1:1) --> 20/1 = 20 threads physiques nécessaires
   vCPU standard : (3:1) --> 40/3 = 13,3333... donc 14 threads physiques nécessaires
   
3. vCPU Critiques : 20 x 1,30 = 26
   vCPU standard : 14 x 1,30 = 18,2 donc 19
   
**Étape 2 — Besoin RAM**

4. RAM total : 8+8+64+32+16+48+48+4+4+8+16+4+4+4+4 = 272 Go, 272 x 1,30 = 353,6 Go

**Étape 3 — Besoin stockage**

5. Disque total : 100+100+800+300+2000+300+300+80+80+200+200+80+60+60+60 = 4720 Go, 4720 x 1,30 = 6 136

Volume utile = Total avec croissance / 0,80 (20%)
Volume utile = 6136/0,80 = 7 670 Go soit 7,67 To

6. RAID 10 = Volume utile × 2 donc 7670 x 2 = 15 340 Go soit 15,34 To

**Étape 4 — Dimensionnement par hôte (N+1)**

 7. Chaque hôte doit être dimensionné pour 100% de la charge donc 
Nombre minimum de threads : 26 + 19 = 45 threads --> 45 + 4 = 49 threads
RAM Minimale : 353,6 Go + 8 Go = 361,6 Go

8. Plus grosse VM : ![image](https://hackmd.io/_uploads/SkHkBomczx.png)
8 vCPUs<<49 threads et 64 Go<<361,6 Go  de RAM donc elle peut largement tourner sur un des 2 hôtes.

9. (barrettes 32/64 Go) donc on arrondit 361,6 Go au multiple de 32 ou 64 supérieur :
Avec barrettes de 64 Go : 6 × 64 = 384 Go
Avec barrettes de 32 Go : 12 × 32 = 384 Go

**Choix retenu : 12 × 32 Go.** L'EPYC 9354 possède 12 canaux mémoire DDR5 : avec 12 barrettes, tous les canaux sont remplis, ce qui maximise la bande passante mémoire (avec 6 × 64 Go, seule la moitié des canaux serait utilisée).

**Étape 5 — Choix du processeur**

10. 

| REF  | Compatibilité | Pourquoi |
| ---- | ------------- | -------- |
| P1   |    NON           |   24 threads < 49 requis |
| P2   | Oui, uniquement en bi-socket (2 × 32 = 64 threads) | 32 threads < 49 requis en mono-socket |
| P3   | Oui, uniquement en bi-socket (2 × 32 = 64 threads) | 32 threads < 49 requis en mono-socket |
| P4  |       OUI    |   64 threads > 49 requis |
| P5 | NON    |   Architecture ARM     |


11. Je recommande le P4, il offre 64 threads en mono-socket, ce qui couvre le besoin avec une seule puce par hôte
Niveau coût : 2 x 3400 = 6800 
Avec le P3 par exemple : 4 × 1 400 CHF = 5 600 
C'est moins cher avec le P3 mais il y aura deux fois plus de composants physiques par hôte donc plus de risques de panne ou de refroidissements

TDP : P4 --> 280 x 2 = 560w 
      P3 --> 2 × 200 W = 400 W par hôte donc 800 W pour       2 hôtes
      
Il y a aussi 64 threads disponibles pour 49 requis donc 15 threads de marge

12. Le P5 est incompatible malgré son prix : son architecture ARM ne supporte pas Windows Server 2022 qui est nécessaire ici. Un processeur moins cher mais inutilisable pour faire tourner les machines virtuelles n'apporte aucune valeur, peu importe son nombre de cœurs.



| Ressources    | Besoin Total avec croissance | Besoin par hôte | Config retenue |
| ------------- | ---------------------------- | --------------- | -------------- |
| Threads       |   45 threads (26 critiques + 19 standard)   |     45 + 4 (réserve) = 49 threads                |     AMD EPYC 9354, 64 threads/hôte           |
| RAM           |    353,6 Go  |   353,6 + 8 (réserve) = 361,6 Go   |   384 Go (12 × barrettes 32 Go)             |
| Stockage      |      6 136 Go   |    (partagé)    |   Volume utile : 7,67 To |
| Nombre d'hôtes |      -----    |        -----         |         2       |
| Stockage brut RAID 10 | — | — | 15,34 To |


---



### CAHIER DES CHARGES 2 — POC multi-hyperviseurs : ESXi + Proxmox



1. Installer ESXi et Proxmox sur VMware Workstation

Installation à partir des images ISO d'ESXi et de Proxmox VE, dans deux VM VMware Workstation :

> ⚠️ **Prérequis :** activer l'option « Virtualize Intel VT-x/EPT or AMD-V/RVI » dans les paramètres processeur de chaque VM (virtualisation imbriquée), sinon les hyperviseurs ne peuvent pas lancer de VM.

![image](https://hackmd.io/_uploads/B1cupC79fg.png)

![image](https://hackmd.io/_uploads/SJdFk1E9Mg.png)

![image](https://hackmd.io/_uploads/r1bRJ14cMg.png)

![image](https://hackmd.io/_uploads/B1pQlyNqGe.png)

![image](https://hackmd.io/_uploads/HkjgbJ45ze.png)

IP configurée : 
![image](https://hackmd.io/_uploads/ByZO1-EcGe.png)



Proxmox installé : ![image](https://hackmd.io/_uploads/HkQokZE5Gx.png)



Pour ping les 2 VM : 

Je les mets sur "VMnet1"
![image](https://hackmd.io/_uploads/SyJcVbE5fl.png)

Puis dans l'ESXI : 
![image](https://hackmd.io/_uploads/HJWwgWEcMg.png)

Rentrer l'IP de la VM Proxmox : 
![image](https://hackmd.io/_uploads/Bk15eZNcGe.png)

Le ping fonctionne :
![image](https://hackmd.io/_uploads/Byzpl-V9zl.png)
Donc les 2 VM ping entre elles.

J'ai dû changer l'adresse ipv4 de la carte réseau sur mon PC hôte pour que les 2 vm puissent se ping et pour pouvoir accéder a l'interface web des deux hyperviseurs : 
![image](https://hackmd.io/_uploads/r1PrbbN9Ml.png)
(Changé le .132 en .159 : l'adaptateur VMnet1 de l'hôte doit être dans le même sous-réseau que les deux hyperviseurs.)


---




## CAHIER DES CHARGES 3 — Cluster de virtualisation haute disponibilité




## Choix de la voie

Proxmox VE : moins gourmand en ressources (pas de VCSA), gestion cluster/HA/backup intégrée nativement, open source. Convient à un lab léger tout en couvrant les exigences CDC 1/CDC 2.

## Réseau

3 interfaces par nœud :

- **MGMT** : `192.168.50.0/24`
- **CLUSTER** (Corosync) : `10.10.10.0/24`
- **STORAGE** (NFS) : `10.20.20.0/24`

Prérequis : hostnames + fichier `hosts`, NTP synchro, même version PVE partout.

## Créer le cluster

```bash
# Sur NODE1
pvecm create CLUSTER-LTL --link0 10.10.10.31

# Sur NODE2 et NODE3
pvecm add 192.168.50.31 --link0 10.10.10.32   # (10.10.10.33 pour NODE3)

# Vérification
pvecm status
pvecm nodes
```

Doit afficher `Quorate: Yes` et 3 nœuds.

## Stockage NFS

- NAS : export `/srv/nfs/ltl-vms` limité à `10.20.20.0/24`.
- Proxmox : **Datacenter → Stockage → Ajouter → NFS**
  - Serveur : `10.20.20.50`
  - Export : `/srv/nfs/ltl-vms`
  - ID : `NFS-LTL`

## VM de test + HA

- Créer **VM03-APP** (1 vCPU, 1 Go RAM, 10 Go disque) sur `NFS-LTL`, IP `192.168.50.60`.
  > Lab simplifié : en production, les VM seraient placées sur un réseau/VLAN dédié, séparé du réseau d'administration.
- HA : **Datacenter → HA → Groupe** (3 nœuds) → ajouter la VM :

```bash
ha-manager add vm:<VMID> --group <groupe> --state started
```

> Syntaxe Proxmox VE 8. Depuis Proxmox VE 9, les groupes HA sont remplacés par des *HA rules* (affinité de nœuds).

## Migration à chaud

Ping continu vers la VM, puis clic droit → **Migrer** (mode en ligne) NODE1 → NODE2.
Quasi 0 paquet perdu (la RAM est copiée avant la bascule).

## Panne simulée

Couper brutalement le nœud hôte de VM03-APP → HA la redémarre sur un autre nœud après environ 2 à 3 minutes (détection de la panne, isolation/fencing du nœud, puis redémarrage à froid : coupure bien plus longue qu'une migration à chaud).

## Sauvegarde

**Datacenter → Sauvegarde** (tâche planifiée, cible `NFS-LTL`) ou en manuel :

```bash
vzdump <VMID> --storage NFS-LTL --mode snapshot
```

## Analyse

1. **3 nœuds vs 2** : le quorum est la majorité nécessaire pour agir sans risque de split-brain. Avec 2 nœuds, une coupure du lien laisse chacun à 1 voix sur 2 → blocage/risque.
2. **Séparation des réseaux** : isoler Corosync (sensible à la latence) et le trafic NFS (volumineux) du trafic admin, pour fiabilité et sécurité.
3. **Migration à chaud vs HA** : migration = transfert RAM en direct, quasi 0 coupure ; HA = redémarrage à froid après panne, coupure de l'ordre de 2 à 3 minutes.
4. **SPOF restant** : le NAS unique → solution prod : Ceph répliqué ou NAS redondant.
5. **Capacité après perte d'un hôte** : avec 3 nœuds dimensionnés comme au CDC 1 (64 threads et 384 Go chacun), la perte d'un nœud laisse 128 threads et 768 Go, pour un besoin total de 45 threads et 353,6 Go (croissance incluse) : toutes les VM peuvent redémarrer sur les 2 nœuds restants.





---
