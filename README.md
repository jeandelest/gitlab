# GitLab – Déploiement Docker avec Caddy (Reverse Proxy)

Ce dépôt contient la configuration nécessaire pour déployer une instance **GitLab CE** en production à l’aide de **Docker Compose**, avec :
- **Caddy** comme reverse proxy HTTPS (installé sur l’hôte)
- **GitLab Runner** pour le CI/CD avec executor Docker
- Une configuration basée sur un fichier `.env`

Cette architecture est adaptée à une **petite/moyenne entreprise (~20 utilisateurs)**.

---

## 🧠 Architecture

```
Internet
   ↓ HTTPS (Caddy)
Serveur (hôte)
   ├── Caddy (TLS / Let's Encrypt)
   ├── GitLab (Docker)
   └── GitLab Runner(s) (Docker)
```

- 🔐 HTTPS est **terminé par Caddy**
- 🐳 GitLab fonctionne en HTTP interne
- 🔑 SSH GitLab est **distinct du SSH du serveur**
- 📦 Données persistantes via volumes Docker

---

## 📁 Structure du dépôt

```text
.
├── docker-compose.yml
├── .env.example
├── config/
├── data/
├── logs/
└── README.md
```

---

## ⚙️ Prérequis

- Linux (serveur cloud)
- Docker ≥ 20.x
- Docker Compose v2
- Caddy installé sur l’hôte
- 4 Go de RAM minimum (8 Go recommandé)
- Nom de domaine pointant vers le serveur

---

## 🔧 Configuration

### 1️⃣ Variables d’environnement

```bash
cp .env.example .env
```

Éditer `.env` selon l’environnement.

---

### 2️⃣ Configuration Caddy

Exemple `/etc/caddy/Caddyfile` :

```caddyfile
caddy.conf
```

---

## 🚀 Déploiement

```bash
docker compose up -d
```

Premier démarrage : plusieurs minutes.

---

## 🔐 Accès

- URL : `https://gitlab.mondomaine.fr`
- Utilisateur initial : `root`

Mot de passe initial :

```bash
docker exec gitlab grep 'Password:' /etc/gitlab/initial_root_password
```

---

## 🔑 SSH GitLab

GitLab dispose de son **propre serveur SSH**.

| Service | Port |
|------|------|
| SSH serveur | 22 |
| SSH GitLab | 2222 |

```bash
git clone ssh://git@gitlab.mondomaine.fr:2222/groupe/projet.git
```

---

## 🧪 CI/CD – GitLab Runner

Les runners utilisent l’executor Docker.

---

## 💾 Sauvegardes

```bash
docker exec gitlab gitlab-backup create
```

Les sauvegardes sont stockées dans `data/backups/`.

---

## 🔐 Bonnes pratiques

- Désactiver les inscriptions publiques
- Activer le 2FA
- Sauvegardes automatiques
- Firewall : ouvrir uniquement 80, 443, 2222

---

## 📈 Scalabilité

Adapté pour ~20 utilisateurs. Possibilité d’évolution avec runners dédiés, SSO, monitoring, backups S3.