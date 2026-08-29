---

## Ce que j'ai appris et axes d'amélioration

### Difficultés rencontrées

- **Configuration réseau** : Comprendre la différence entre les modes VirtualBox (NAT, Host-Only, Pont) et choisir le bon pour un pentest isolé.
- **Gestion de session Metasploit** : L'erreur `RHOSTS failed to validate` m'a appris à vérifier méthodiquement mes paramètres avant de lancer un exploit.
- **Post-exploitation manuelle** : Trouver des fichiers sensibles sans script automatisé demande de la méthode et une bonne connaissance des chemins système Linux.
- **Post-exploitation** : Difficultés à savoir quoi faire une fois l'accès total obtenu, manque d'expérience.

### Ce que j'ai appris

- **La reconnaissance est la clé** : Un bon scan `nmap` (`-sV -sC -A`) donne 80% des informations nécessaires.
- **L'historique des commandes** (`.bash_history`) est une mine d'or pour trouver des erreurs ou des tentatives d'administrateur.
- **Documenter au fur et à mesure** évite d'oublier des détails cruciaux.

### Axes d'amélioration

- **Automatiser l'énumération** avec un script perso pour gagner du temps en post-exploitation.
- **Pratiquer sans Metasploit** en compilant manuellement l'exploit C (via `searchsploit` et `gcc`) pour mieux comprendre la faille.
- **Adopter une approche défensive** en analysant les logs générés par l'attaque (comme un analyste SOC).

### Pour la prochaine fois

- Préparer un **script de post-exploitation** avant de commencer pour ne rien oublier.
- Choisir une machine où l'élévation de privilèges est nécessaire pour pratiquer.
- Construire un SIEM et adopter une approche défensive.

---
