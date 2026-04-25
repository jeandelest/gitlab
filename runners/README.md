# GitLab Runner – Docker Executor

Ce document décrit le déploiement et l’exploitation des **GitLab Runners** utilisés pour le CI/CD,
avec **executor Docker**, dans un environnement d’entreprise.

Les runners sont **séparés de GitLab** et déployés via Docker Compose.

---

## 🧠 Architecture CI/CD

```
Développeur
   ↓ push
GitLab
   ↓ jobs CI
GitLab Runner (Docker)
   ↓
Docker Engine (build, test, deploy)
```

- 🐳 Executor : Docker
- 🔐 Communication sécurisée via HTTPS
- 🔁 Isolation des jobs
- 🚀 Compatible build d’images Docker

---

## 📁 Structure

```text
gitlab-runner/
├── docker-compose.yml
├── .env
├── config/
└── README.md
```

---

## ⚙️ Prérequis

- Docker installé sur l’hôte
- Docker Compose v2
- Accès réseau vers GitLab
- Accès Docker socket (`/var/run/docker.sock`)

---

## 🔧 Configuration

### Fichier `.env`

```env
cp .env.example .env
```

### Récupérer le token d’enregistrement

Dans ton GitLab :

Va dans Admin Area (ou projet spécifique)
Puis :
Settings > CI/CD
Section Runners
Copie le registration token

---

## 📝 Enregistrement du Runner

```bash
docker compose up -d
docker exec -it gitlab-runner gitlab-runner register
```

Paramètres recommandés :
- URL : https://gitlab.mondomaine.fr
- Executor : docker
- Image par défaut : docker:26
- Mode privileged : yes

---

## 🧪 Exemple `.gitlab-ci.yml`

```yaml
image: docker:26

services:
  - docker:dind

variables:
  DOCKER_TLS_CERTDIR: ""

build:
  stage: build
  script:
    - docker build -t monapp .
```

---

## 🔐 Sécurité

- Limiter l’accès aux runners (tags)
- Ne pas utiliser privileged si inutile
- Runners dédiés pour projets sensibles
- Scanner les images Docker

---

## 🔄 Maintenance

### Mise à jour
```bash
docker compose pull
docker compose up -d
```

### Logs
```bash
docker logs gitlab-runner
```

---

## 📈 Scalabilité

- Plusieurs runners possibles
- Runners par équipe ou par projet
- Possibilité d’autoscaling (Docker Machine, Kubernetes)

---

## 📄 Notes

Les runners ont accès au Docker de l’hôte.
Traiter cette machine comme **sensible**.
