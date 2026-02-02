# RUNBOOK – GitLab Docker (Exploitation)

Ce document décrit les procédures d’exploitation, de maintenance et de gestion d’incidents
pour l’instance GitLab déployée avec Docker Compose.

---

## 🔧 Informations générales

- Service : GitLab CE
- Déploiement : Docker Compose
- Reverse proxy : Caddy (hôte)
- HTTPS : Let’s Encrypt
- CI/CD : GitLab Runner Docker

---

## 🚀 Démarrage / Arrêt

### Démarrer
```bash
docker compose up -d
```

### Arrêter
```bash
docker compose down
```

### Redémarrer
```bash
docker compose restart
```

---

## 🔍 Vérifications de santé

- Accès web : https://gitlab.mondomaine.fr
- SSH GitLab :
```bash
ssh -T git@gitlab.mondomaine.fr -p 2222
```

- Logs GitLab :
```bash
docker logs gitlab
```

---

## 🔄 Mise à jour GitLab

```bash
docker compose pull
docker compose up -d
```

⚠️ Toujours effectuer un backup avant mise à jour.

---

## 💾 Sauvegardes

### Backup manuel
```bash
docker exec gitlab gitlab-backup create
```

### Emplacement
```text
data/backups/
```

### Restauration (résumé)
```bash
docker exec gitlab gitlab-backup restore BACKUP=timestamp
```

---

## 🚨 Incidents courants

### GitLab inaccessible
- Vérifier Caddy
- Vérifier ports 80/443
- Vérifier container GitLab

### CI bloquée
- Vérifier runners enregistrés
- Vérifier Docker socket
- Vérifier quotas disque

### Disque plein
- Nettoyer anciennes backups
- Nettoyer images Docker
```bash
docker system prune
```

---

## 🔐 Sécurité

- Forcer 2FA
- Désactiver inscriptions publiques
- Restreindre accès admin
- Firewall : 80 / 443 / 2222 uniquement

---

## 📈 Supervision recommandée

- Espace disque
- Charge CPU / RAM
- Taille des backups
- Files Sidekiq

---

## 🧯 Procédures d’urgence

### Mot de passe root perdu
```bash
docker exec -it gitlab gitlab-rake gitlab:password:reset
```

### Arrêt immédiat
```bash
docker stop gitlab
```

---

## 📄 Historique des changements

À maintenir par l’équipe d’exploitation.
