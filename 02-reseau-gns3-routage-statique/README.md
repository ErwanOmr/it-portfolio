# GNS3 — Routage statique entre deux réseaux (2 routeurs + VPCS)

> Documentation réalisée dans le cadre du cours Réseau — Geneva Institute of Technology, Bachelor IT 1re année.

**Technologies :** GNS3 · Cisco IOS · VPCS

**Compétences mises en œuvre :** adressage IPv4 et masques, passerelle par défaut, configuration d'interfaces Cisco, routage statique, tests de connectivité et dépannage.

**Objectif :** relier deux réseaux locaux différents (192.168.1.0/24 et 192.168.2.0/24) grâce à deux routeurs Cisco, configurer des routes statiques, puis vérifier que les PC des deux côtés peuvent se pinger.

**Outils :** GNS3, routeurs Cisco (IOS), PC virtuels VPCS, switch Ethernet GNS3 (bonus)

---

## Sommaire

1. Topologie et plan d'adressage
2. Mise en place du lab dans GNS3
3. Configuration des routeurs (R1 et R2)
4. Ajout des routes statiques
5. Configuration des PC (VPCS)
6. Vérification et tests
7. Sauvegarde de la configuration
8. Bonus : ajouter un switch et d'autres PC
9. Dépannage et récapitulatif des commandes

---

## 1. Topologie et plan d'adressage

Topologie finale :
![image](https://hackmd.io/_uploads/Hk0GhyKqMl.png)

Les 2 routeurs sont reliés entre eux par leur port **FastEthernet0/0** :
![image](https://hackmd.io/_uploads/BJSvI1K5Mx.png)

| Équipement | Interface | Adresse IP | Masque | Passerelle | Réseau |
|---|---|---|---|---|---|
| R1 | FastEthernet0/0 (vers R2) | 192.168.4.11 | 255.255.255.0 | — | 192.168.4.0/24 (liaison routeurs) |
| R1 | GigabitEthernet3/0 (vers LAN 1) | 192.168.1.1 | 255.255.255.0 | — | 192.168.1.0/24 |
| R2 | FastEthernet0/0 (vers R1) | 192.168.4.12 | 255.255.255.0 | — | 192.168.4.0/24 (liaison routeurs) |
| R2 | GigabitEthernet1/0 (vers LAN 2) | 192.168.2.1 | 255.255.255.0 | — | 192.168.2.0/24 |
| PC1 | eth0 | 192.168.1.11 | /24 | 192.168.1.1 | LAN 1 |
| PC4 (bonus) | eth0 | 192.168.1.12 | /24 | 192.168.1.1 | LAN 1 |
| PC2 | eth0 | 192.168.2.11 | /24 | 192.168.2.1 | LAN 2 |

:::info
**Règle à retenir :** la passerelle d'un PC, c'est toujours l'IP de l'interface du routeur qui est dans **son** réseau.
:::

---

## 2. Mise en place du lab dans GNS3

1. Créer un nouveau projet : **File → New blank project**.
2. Glisser-déposer 2 routeurs (R1, R2) et 2 VPCS (PC1, PC2) depuis la liste des équipements.
3. Relier les équipements avec l'outil **Add a link** (icône du câble) :
    - R1 **Fa0/0** ↔ R2 **Fa0/0**
    - R1 **Gi3/0** ↔ PC1 **eth0**
    - R2 **Gi1/0** ↔ PC2 **eth0**
4. Démarrer tous les équipements avec le bouton **▶ Start** (les points passent au vert).
5. Ouvrir la console de chaque équipement : clic droit → **Console**.

---

## 3. Configuration des routeurs

### 3.1 Voir l'état des interfaces

~~~
show ip interface brief
~~~
![image](https://hackmd.io/_uploads/Bka2MNYcGg.png)

Par défaut, les interfaces n'ont pas d'IP (**unassigned**) et sont éteintes (**administratively down**).

### 3.2 Passer en mode configuration

~~~
enable
conf t
~~~
![image](https://hackmd.io/_uploads/B17xmEKqGe.png)

Le prompt devient **R1(config)#**.

### 3.3 Nommer le routeur

~~~
hostname R1_EO
~~~

### 3.4 Configurer une interface

On sélectionne l'interface, on lui donne une IP, puis on l'active :
![image](https://hackmd.io/_uploads/r1A4XNK5Gg.png)

~~~
interface GigabitEthernet3/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
 exit
~~~

:::warning
Sur un routeur Cisco, les interfaces sont **éteintes par défaut**. Sans **no shutdown**, l'interface reste en **administratively down** et rien ne passe.
:::

### 3.5 Configuration complète de R1

~~~
enable
conf t
hostname R1_EO
interface FastEthernet0/0
 ip address 192.168.4.11 255.255.255.0
 no shutdown
 exit
interface GigabitEthernet3/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
 exit
~~~

### 3.6 Configuration complète de R2

~~~
enable
conf t
hostname R2_EO
interface FastEthernet0/0
 ip address 192.168.4.12 255.255.255.0
 no shutdown
 exit
interface GigabitEthernet1/0
 ip address 192.168.2.1 255.255.255.0
 no shutdown
 exit
~~~

### 3.7 Vérifier

~~~
show ip interface brief
~~~

Les interfaces configurées doivent être **up / up** avec la bonne IP :

R1 :
![image](https://hackmd.io/_uploads/BkTC-4F5Ml.png)

R2 :
![image](https://hackmd.io/_uploads/r1j6bVF9Gx.png)

---

## 4. Ajout des routes statiques

Un routeur connaît automatiquement les réseaux **directement branchés** sur ses interfaces. Pour joindre un réseau plus loin, il faut lui indiquer le chemin avec une **route statique**.

Syntaxe :
~~~
ip route <réseau de destination> <masque> <IP du prochain routeur>
~~~

**Sur R1** (pour joindre le LAN de PC2) :
~~~
ip route 192.168.2.0 255.255.255.0 192.168.4.12
~~~
![image](https://hackmd.io/_uploads/HkFir1K9fg.png)

Se lit : « Pour joindre le réseau 192.168.2.0/24 (celui de PC2), envoie les paquets vers 192.168.4.12 (l'IP de R2, mon voisin direct). »

**Sur R2** (pour joindre le LAN de PC1) :
~~~
ip route 192.168.1.0 255.255.255.0 192.168.4.11
~~~

:::warning
Il faut une route **dans les deux sens**. Si seul R1 a sa route, le ping part vers PC2 mais la réponse ne sait pas revenir vers PC1.
:::

Vérifier la table de routage :
~~~
show ip route
~~~
Les routes statiques apparaissent avec la lettre **S**, les réseaux directement connectés avec **C**.

---

## 5. Configuration des PC (VPCS)

Syntaxe : **ip <IP du PC>/<masque> <passerelle>**

~~~
PC1> ip 192.168.1.11/24 192.168.1.1
PC2> ip 192.168.2.11/24 192.168.2.1
~~~

![image](https://hackmd.io/_uploads/BJGowJKcze.png)
![image](https://hackmd.io/_uploads/SJqFPkKczl.png)

- **192.168.1.11/24** → l'adresse IP du PC avec son masque (/24 = 255.255.255.0)
- **192.168.1.1** → la passerelle, c'est-à-dire l'IP de R1 côté LAN 1

Vérifier la configuration du PC :
~~~
show ip
~~~

---

## 6. Vérification et tests

Depuis PC1 :
~~~
PC1> ping 192.168.1.1     (passerelle R1)
PC1> ping 192.168.4.12    (R2, de l'autre côté de la liaison)
PC1> ping 192.168.2.11    (PC2, dans l'autre réseau)
PC1> trace 192.168.2.11   (voir le chemin : R1 → R2 → PC2)
~~~

:::info
Il est normal que le **premier** ping affiche un ou deux « timeout » : le temps que les équipements résolvent les adresses MAC (ARP). Les suivants doivent répondre.
:::

---

## 7. Sauvegarde de la configuration

Sur chaque routeur (sinon la configuration est perdue au redémarrage) :
~~~
end
write memory
~~~

Sur chaque VPCS :
~~~
save
~~~

Et dans GNS3 : **File → Save project**.

---

## 8. Bonus : ajouter un switch et d'autres PC

Il suffit de placer un switch entre le routeur et les PC, puis de configurer les nouveaux PC avec des adresses différentes :
![image](https://hackmd.io/_uploads/Hk0GhyKqMl.png)

**Règle : même réseau, même passerelle, IP finale différente pour chaque PC.**

Exemple PC4 (sur le LAN 1) :
~~~
PC4> ip 192.168.1.12/24 192.168.1.1
~~~
![image](https://hackmd.io/_uploads/S1YNh1FcGe.png)

.12 au lieu de .11, la passerelle ne change pas. Aucune modification n'est nécessaire sur les routeurs : le réseau 192.168.1.0/24 est déjà connu.

---

## 9. Dépannage et récapitulatif des commandes

### Problèmes fréquents

| Symptôme | Cause probable | Solution |
|---|---|---|
| Interface en **administratively down** | Interface pas activée | **no shutdown** sur l'interface |
| Interface **up / down** | Câble absent ou mauvais port relié | Vérifier les liens dans GNS3 |
| PC1 ping R1 mais pas PC2 | Route manquante sur R1 ou R2 | Vérifier **show ip route** des deux côtés |
| « host (x.x.x.x) not reachable » sur le PC | Mauvaise passerelle sur le VPCS | Refaire **ip** avec la bonne passerelle |
| Tout est perdu après redémarrage | Configuration non sauvegardée | **write memory** / **save** |

### Récapitulatif

| Commande | Où | Rôle |
|---|---|---|
| enable | Routeur | Passer en mode privilégié |
| conf t | Routeur | Entrer en mode configuration |
| hostname NOM | Routeur | Nommer le routeur |
| interface NOM | Routeur | Sélectionner une interface |
| ip address IP MASQUE | Routeur | Donner une IP à l'interface |
| no shutdown | Routeur | Activer l'interface |
| ip route RÉSEAU MASQUE PASSERELLE | Routeur | Ajouter une route statique |
| show ip interface brief | Routeur | État et IP des interfaces |
| show ip route | Routeur | Table de routage |
| write memory | Routeur | Sauvegarder la configuration |
| ip IP/MASQUE PASSERELLE | VPCS | Configurer l'IP du PC |
| show ip | VPCS | Afficher l'IP du PC |
| ping / trace | VPCS | Tester la connectivité / le chemin |
| save | VPCS | Sauvegarder la configuration du PC |

---

**Exemple du professeur :**

![Routage statique](https://hackmd.io/_uploads/SJ7a_eOcGg.png)
