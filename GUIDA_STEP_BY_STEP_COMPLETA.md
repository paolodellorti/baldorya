# 📋 GUIDA STEP-BY-STEP COMPLETA - GESTIONALE CENTRO ACCOGLIENZA

## 🎯 FASE 1: SETUP AMBIENTE DI SVILUPPO (Giorno 1-2)

### **PASSO 1.1: Installazione Software Base**

#### A. Node.js (per Vue.js)
```bash
# Windows: Scarica da https://nodejs.org (LTS version)
# macOS: 
brew install node
# Linux Ubuntu:
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# Verifica installazione
node --version  # Deve essere >= 18.0.0
npm --version   # Deve essere >= 9.0.0
```

#### B. Python (per FastAPI)
```bash
# Windows: Scarica da https://python.org (3.11+)
# macOS:
brew install python@3.11
# Linux Ubuntu:
sudo apt update
sudo apt install python3.11 python3.11-venv python3-pip

# Verifica installazione
python3 --version  # Deve essere >= 3.11
pip --version
```

#### C. Git
```bash
# Windows: Scarica da https://git-scm.com
# macOS:
brew install git
# Linux Ubuntu:
sudo apt install git

# Configurazione iniziale
git config --global user.name "Il Tuo Nome"
git config --global user.email "tua.email@example.com"
```

#### D. Docker Desktop
```bash
# Windows/macOS: Scarica da https://docker.com/products/docker-desktop
# Linux Ubuntu:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Verifica installazione
docker --version
docker-compose --version
```

#### E. VS Code + Extensions
```bash
# Scarica VS Code da https://code.visualstudio.com

# Extensions da installare:
# - Vue Language Features (Volar)
# - TypeScript Vue Plugin (Volar)
# - Python
# - Python Debugger
# - Docker
# - GitLens
# - Prettier
# - ESLint
# - Auto Rename Tag
# - Material Icon Theme
```

### **PASSO 1.2: Creazione Repository GitHub**

```bash
# 1. Vai su https://github.com
# 2. Clicca "New repository"
# 3. Nome: "gestionale-centro-accoglienza"
# 4. Descrizione: "Sistema di gestione per centro d'accoglienza"
# 5. Private: ✓
# 6. Add README: ✓
# 7. .gitignore: None (lo faremo custom)
# 8. License: MIT

# Clone del repository
git clone https://github.com/TUO_USERNAME/gestionale-centro-accoglienza.git
cd gestionale-centro-accoglienza
```

---

## 🖥️ FASE 2: SETUP SERVER VPS (Giorno 2-3)

### **PASSO 2.1: Scelta e Acquisto Server**

#### Opzione Consigliata: Hetzner Cloud
```bash
# 1. Vai su https://console.hetzner.cloud
# 2. Registrati/Login
# 3. Crea nuovo progetto: "Centro Accoglienza"
# 4. Add Server:
#    - Location: Falkenstein (Germania) o Ashburn (USA)
#    - Image: Ubuntu 22.04
#    - Type: CX21 (2 vCPU, 4GB RAM, 40GB SSD) - €4.51/mese
#    - SSH Key: Crea/carica la tua chiave pubblica
#    - Name: "centro-accoglienza-main"
# 5. Create & Boot

# Salva l'IP del server: 
SERVER_IP=XXX.XXX.XXX.XXX
```

### **PASSO 2.2: Configurazione SSH Key**

```bash
# Se non hai già una SSH key, creala:
ssh-keygen -t ed25519 -C "tua.email@example.com"
# Premi Enter per tutti i prompt (default)

# Visualizza la chiave pubblica
cat ~/.ssh/id_ed25519.pub
# Copia tutto l'output e incollalo in Hetzner durante la creazione server
```

### **PASSO 2.3: Primo Accesso e Configurazione Server**

```bash
# Connessione iniziale
ssh root@YOUR_SERVER_IP

# Una volta connesso al server:

# 1. Aggiornamento sistema
apt update && apt upgrade -y

# 2. Installazione utility essenziali
apt install -y curl wget git unzip software-properties-common apt-transport-https ca-certificates gnupg lsb-release

# 3. Creazione utente deployer
adduser deployer
# Inserisci password sicura, altri campi opzionali

# 4. Aggiungi deployer ai sudoers
usermod -aG sudo deployer

# 5. Configurazione SSH per deployer
mkdir -p /home/deployer/.ssh
cp /root/.ssh/authorized_keys /home/deployer/.ssh/
chown -R deployer:deployer /home/deployer/.ssh
chmod 700 /home/deployer/.ssh
chmod 600 /home/deployer/.ssh/authorized_keys

# 6. Test connessione con deployer
exit  # Disconnetti da root
ssh deployer@YOUR_SERVER_IP  # Connetti come deployer
```

### **PASSO 2.4: Installazione Docker su Server**

```bash
# Connesso come deployer:

# 1. Installazione Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 2. Aggiungi deployer al gruppo docker
sudo usermod -aG docker deployer

# 3. Ricarica i gruppi (o riconnettiti)
newgrp docker

# 4. Installazione Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# 5. Verifica installazione
docker --version
docker-compose --version
docker run hello-world  # Test Docker
```

### **PASSO 2.5: Configurazione Firewall**

```bash
# Setup UFW (Ubuntu Firewall)
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 80/tcp   # HTTP
sudo ufw allow 443/tcp  # HTTPS
sudo ufw status verbose
```

---

## 🌐 FASE 3: DOMINIO E SSL (Giorno 3)

### **PASSO 3.1: Acquisto Dominio**

```bash
# Opzioni provider:
# - Namecheap.com (consigliato, ~€10/anno)
# - Cloudflare.com (~€8/anno)
# - Gandi.net (~€15/anno)

# Esempio: centro-accoglienza.com
# Salva il dominio scelto: DOMAIN_NAME=centro-accoglienza.com
```

### **PASSO 3.2: Configurazione DNS**

```bash
# Nel pannello del tuo provider DNS:
# Crea questi record:

# A record: @ (root) → YOUR_SERVER_IP
# A record: www → YOUR_SERVER_IP  
# A record: api → YOUR_SERVER_IP
# A record: admin → YOUR_SERVER_IP

# Verifica propagazione DNS (può richiedere fino a 24h):
nslookup your-domain.com
dig your-domain.com
```

---

## 🏗️ FASE 4: STRUTTURA PROGETTO (Giorno 4-5)

### **PASSO 4.1: Struttura Directory**

```bash
# Nella cartella del progetto locale:
cd gestionale-centro-accoglienza

# Crea struttura directory
mkdir -p {backend,frontend,docker,docs,scripts}
mkdir -p docker/{traefik,mysql,redis}
mkdir -p docs/{api,user-guide,deployment}
mkdir -p scripts/{setup,deploy,backup}

# Struttura finale:
# gestionale-centro-accoglienza/
# ├── backend/          # FastAPI application
# ├── frontend/         # Vue 3 + Ionic application  
# ├── docker/          # Docker configurations
# ├── docs/            # Documentation
# ├── scripts/         # Automation scripts
# ├── docker-compose.yml
# ├── .env.example
# ├── .gitignore
# └── README.md
```

### **PASSO 4.2: File di Configurazione Base**

#### A. .gitignore
```bash
cat > .gitignore << 'EOF'
# Environment files
.env
.env.local
.env.*.local

# Dependencies
node_modules/
__pycache__/
*.pyc
*.pyo
*.pyd
.Python
env/
venv/
.venv/

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Database
*.db
*.sqlite

# Docker
.docker/

# Build outputs
dist/
build/
*.egg-info/

# Uploads
uploads/
media/

# Certificates
*.pem
*.key
*.crt

# Backup files
*.bak
*.backup
EOF
```

#### B. .env.example
```bash
cat > .env.example << 'EOF'
# Environment
NODE_ENV=development
ENVIRONMENT=development

# Domain
DOMAIN_NAME=localhost
FRONTEND_URL=http://localhost:8100
BACKEND_URL=http://localhost:8000

# Database
MYSQL_ROOT_PASSWORD=your_secure_root_password
MYSQL_DATABASE=centro_accoglienza
MYSQL_USER=app_user
MYSQL_PASSWORD=your_secure_app_password
DB_HOST=mysql
DB_PORT=3306

# JWT
JWT_SECRET_KEY=your_super_secret_jwt_key_here
JWT_ALGORITHM=HS256
JWT_ACCESS_TOKEN_EXPIRE_MINUTES=30

# Redis
REDIS_URL=redis://redis:6379

# Email (opzionale per notifiche)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASSWORD=your_app_password

# File Upload
MAX_FILE_SIZE=10485760  # 10MB
UPLOAD_FOLDER=uploads

# Security
ALLOWED_ORIGINS=http://localhost:8100,http://localhost:3000
CORS_ORIGINS=["http://localhost:8100", "http://localhost:3000"]
EOF
```

#### C. README.md
```bash
cat > README.md << 'EOF'
# Gestionale Centro d'Accoglienza

Sistema di gestione completo per centri d'accoglienza con tracking ospiti, gestione alloggi, attività e documenti.

## Stack Tecnologico

- **Frontend**: Vue 3 + Ionic + TypeScript
- **Backend**: FastAPI + SQLAlchemy + MySQL
- **Mobile**: Capacitor (Android/iOS)
- **Deploy**: Docker + Docker Compose

## Quick Start

1. Copia le variabili d'ambiente:
```bash
cp .env.example .env
```

2. Avvia i servizi:
```bash
docker-compose up -d
```

3. Installa dipendenze frontend:
```bash
cd frontend && npm install
```

4. Avvia il frontend:
```bash
npm run dev
```

## Struttura Progetto

```
├── backend/          # FastAPI application
├── frontend/         # Vue 3 + Ionic application  
├── docker/          # Docker configurations
├── docs/            # Documentation
└── scripts/         # Automation scripts
```

## Sviluppo

- Frontend: http://localhost:8100
- Backend API: http://localhost:8000
- API Docs: http://localhost:8000/docs

## Deploy

Vedi [docs/deployment/README.md](docs/deployment/README.md)
EOF
```

---

## 🐳 FASE 5: CONFIGURAZIONE DOCKER (Giorno 5-6)

### **PASSO 5.1: Docker Compose Principale**

```bash
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  # Reverse Proxy
  traefik:
    image: traefik:v3.0
    container_name: traefik
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./docker/traefik:/etc/traefik
      - traefik-certificates:/certificates
    networks:
      - web
    environment:
      - TRAEFIK_API_DASHBOARD=true
      - TRAEFIK_API_INSECURE=true
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.traefik.rule=Host(`traefik.${DOMAIN_NAME}`)"
      - "traefik.http.routers.traefik.tls.certresolver=letsencrypt"

  # Database
  mysql:
    image: mysql:8.0
    container_name: mysql
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    volumes:
      - mysql_data:/var/lib/mysql
      - ./docker/mysql/init:/docker-entrypoint-initdb.d
    ports:
      - "3306:3306"
    networks:
      - backend
    command: --default-authentication-plugin=mysql_native_password

  # Redis Cache
  redis:
    image: redis:7-alpine
    container_name: redis
    restart: unless-stopped
    volumes:
      - redis_data:/data
    networks:
      - backend
    command: redis-server --appendonly yes

  # Backend API
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: backend
    restart: unless-stopped
    environment:
      - DB_HOST=mysql
      - DB_PORT=3306
      - DB_NAME=${MYSQL_DATABASE}
      - DB_USER=${MYSQL_USER}
      - DB_PASSWORD=${MYSQL_PASSWORD}
      - REDIS_URL=redis://redis:6379
      - JWT_SECRET_KEY=${JWT_SECRET_KEY}
    volumes:
      - ./backend:/app
      - backend_uploads:/app/uploads
    depends_on:
      - mysql
      - redis
    networks:
      - web
      - backend
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.backend.rule=Host(`api.${DOMAIN_NAME}`)"
      - "traefik.http.routers.backend.tls.certresolver=letsencrypt"
      - "traefik.http.services.backend.loadbalancer.server.port=8000"

  # Frontend
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: frontend
    restart: unless-stopped
    volumes:
      - ./frontend:/app
      - /app/node_modules
    networks:
      - web
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.frontend.rule=Host(`${DOMAIN_NAME}`)"
      - "traefik.http.routers.frontend.tls.certresolver=letsencrypt"
      - "traefik.http.services.frontend.loadbalancer.server.port=80"

  # Database Admin (sviluppo)
  adminer:
    image: adminer
    container_name: adminer
    restart: unless-stopped
    depends_on:
      - mysql
    networks:
      - web
      - backend
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.adminer.rule=Host(`db.${DOMAIN_NAME}`)"
      - "traefik.http.routers.adminer.tls.certresolver=letsencrypt"

volumes:
  mysql_data:
  redis_data:
  backend_uploads:
  traefik-certificates:

networks:
  web:
    external: true
  backend:
    external: false
EOF
```

### **PASSO 5.2: Configurazione Traefik**

```bash
# Crea directory configurazione Traefik
mkdir -p docker/traefik

cat > docker/traefik/traefik.yml << 'EOF'
# Traefik Configuration
global:
  checkNewVersion: false
  sendAnonymousUsage: false

api:
  dashboard: true
  insecure: true

entryPoints:
  web:
    address: ":80"
    http:
      redirections:
        entrypoint:
          to: websecure
          scheme: https
  websecure:
    address: ":443"

providers:
  docker:
    endpoint: "unix:///var/run/docker.sock"
    exposedByDefault: false
    network: web

certificatesResolvers:
  letsencrypt:
    acme:
      email: your-email@example.com
      storage: /certificates/acme.json
      httpChallenge:
        entryPoint: web
EOF
```

### **PASSO 5.3: Script di Setup Docker**

```bash
cat > scripts/setup/docker-setup.sh << 'EOF'
#!/bin/bash

# Setup Docker Networks
echo "Creating Docker networks..."
docker network create web 2>/dev/null || true

# Create directories
echo "Creating required directories..."
mkdir -p docker/traefik/certificates
mkdir -p uploads
mkdir -p logs

# Set permissions
echo "Setting permissions..."
chmod 600 docker/traefik/certificates
touch docker/traefik/certificates/acme.json
chmod 600 docker/traefik/certificates/acme.json

echo "Docker setup completed!"
EOF

chmod +x scripts/setup/docker-setup.sh
```

---

Questo è solo l'inizio! La guida completa continua con:

## 🔜 PROSSIMI PASSAGGI DETTAGLIATI:

- **FASE 6**: Setup Backend FastAPI (giorno 6-8)
- **FASE 7**: Setup Frontend Vue+Ionic (giorno 8-10)  
- **FASE 8**: Database e Modelli (giorno 10-12)
- **FASE 9**: API Endpoints (giorno 12-15)
- **FASE 10**: Frontend Components (giorno 15-20)
- **FASE 11**: Mobile con Capacitor (giorno 20-25)
- **FASE 12**: Deploy e Testing (giorno 25-30)

**Vuoi che continui con la FASE 6 (Backend FastAPI setup completo)?** 

Ogni fase avrà lo stesso livello di dettaglio - nessun passaggio saltato! 🚀