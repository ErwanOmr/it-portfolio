# Portfolio IT — Erwan Omorodion

Étudiant en **1re année de Bachelor IT** au **Geneva Institute of Technology** (région de Genève, Suisse), orienté **infrastructure, réseau, virtualisation et cybersécurité**.

Ce dépôt rassemble la documentation technique de mes projets de formation. Chaque projet présente le contexte, les technologies utilisées, les compétences mises en œuvre et la procédure complète avec captures d'écran.

> Projets réalisés en environnement de lab (VMware Workstation, GNS3, Proxmox VE) dans le cadre de ma formation.

---

## Projets

### 🖧 Virtualisation

| # | Projet | Description | Technologies |
|---|---|---|---|
| 01 | [Virtualisation & cluster Proxmox HA](01-virtualisation-proxmox-ha/README.md) | Dimensionnement d'une plateforme (CPU, RAM, RAID 10, N+1), POC ESXi + Proxmox, cluster 3 nœuds avec HA, migration à chaud et sauvegardes | Proxmox VE, VMware ESXi, Corosync, NFS |
| 08 | [Snapshots et plan de reprise (RTO / RPO)](08-snapshots-plan-de-reprise/README.md) | Snapshot de référence, simulation de panne, restauration chronométrée (≈ 18 s), snapshots automatiques, plan de reprise RTO 15 min / RPO 24 h | VMware Workstation, AutoProtect, Windows 11 |
| 09 | [Réseau virtuel isolé (labo de test)](09-reseau-virtuel-isole/README.md) | LAN Segment sans passerelle ni DNS, plan de tests prouvant l'isolation (Internet, DNS, réseau physique), passerelle filtrante en option | VMware LAN Segment, Windows 11, Ubuntu, nftables |
| 10 | [Infrastructure virtuelle pour une PME](10-infrastructure-virtuelle-pme/README.md) | Consolidation de 3 serveurs (fichiers SMB, impression, web Apache) sur un hôte, dimensionnement par rôle, réseau NAT, fiche d'architecture | VMware Workstation, Windows 11, Ubuntu, Apache2 |

### 🌐 Réseau & sécurité

| # | Projet | Description | Technologies |
|---|---|---|---|
| 02 | [Réseau multi-sites Cisco sous GNS3](02-reseau-gns3-routage-statique/README.md) | 3 routeurs / 3 sites, routage statique, postes Windows 11 raccordés via VMware, dépannage (pare-feu, ARP, TTL), Telnet vs SSHv2 analysés avec Wireshark | GNS3, Cisco IOS, SSH, Wireshark |

### 🛠️ Systèmes & support

| # | Projet | Description | Technologies |
|---|---|---|---|
| 03 | [Déploiement par image système](03-deploiement-image-systeme/README.md) | Cahier des charges, poste de référence, généralisation, clonage et mesure du gain de temps (~4×) | Windows 11, Sysprep, VMware |
| 04 | [Dépannage et diagnostic](04-depannage-diagnostic/README.md) | Simulation et résolution de pannes (disque plein, pilote manquant) avec tickets d'intervention | Windows 11, PowerShell, outils de diagnostic |
| 05 | [Poste multi-utilisateurs](05-poste-multi-utilisateurs/README.md) | Comptes et groupes locaux, permissions NTFS, verrouillage automatique, politique de mots de passe | Windows 11, icacls, gpedit, secpol |
| 06 | [Administration de postes Windows](06-administration-postes-windows/README.md) | Mise en service d'un poste (fiche de mise en service), migration et sauvegarde d'un poste existant | Windows 11, partages SMB, PowerShell |
| 07 | [Fondamentaux hardware & OS](07-fondamentaux-hardware-os/README.md) | Montage PC, configurations compatibles, BIOS/UEFI, clé bootable, installations Windows/Ubuntu | Hardware, UEFI, Rufus, VMware |

---

## Compétences

- **Virtualisation :** Proxmox VE (cluster, HA, migration à chaud, sauvegardes), VMware ESXi, VMware Workstation (snapshots, réseaux virtuels NAT / Host-only / LAN Segment)
- **Continuité de service :** snapshots, plan de reprise (RTO / RPO), règle de sauvegarde 3-2-1
- **Réseau :** adressage IPv4, routage statique, configuration Cisco IOS, GNS3, diagnostic (ping, tracert, ARP)
- **Sécurité :** SSHv2 vs Telnet, analyse de trafic Wireshark, isolation réseau, pare-feu Windows, permissions NTFS
- **Systèmes :** Windows 10/11 (comptes, NTFS, stratégies locales, déploiement), Ubuntu (Apache2)
- **Support & exploitation :** diagnostic de pannes, tickets d'intervention, sauvegarde/migration, documentation technique
- **Hardware :** assemblage, compatibilité des composants, BIOS/UEFI
- **Outils :** PowerShell, cmd, Bash, MobaXterm, Wireshark, Markdown, Git/GitHub

---

## Contact

**En recherche de stage** en informatique (infrastructure, réseau, support) dans la région de Genève.

- LinkedIn : [linkedin.com/in/erwan-omorodion](https://www.linkedin.com/in/erwan-omorodion)
- GitHub : [@ErwanOmr](https://github.com/ErwanOmr)
