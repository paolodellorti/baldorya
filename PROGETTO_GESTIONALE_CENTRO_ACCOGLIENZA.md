# DOSSIER PROGETTO: GESTIONALE CENTRO D'ACCOGLIENZA

## 🎯 OBIETTIVO DEL PROGETTO
Sviluppare un'applicazione mobile-first per la gestione completa degli ospiti di un centro d'accoglienza, con tracking delle attività, gestione documenti e monitoraggio del percorso di integrazione.

---

## 📋 SPECIFICHE TECNICHE

### Stack Tecnologico
- **Frontend**: Vue 3 + Composition API + TypeScript
- **Mobile**: Ionic + Capacitor (deploy Android/iOS)
- **Backend**: Python (FastAPI)
- **Database**: MySQL 8.0
- **Autenticazione**: JWT + OAuth2
- **File Storage**: Local + Cloud backup
- **Deployment**: Docker + Docker Compose

---

## 🏗️ ARCHITETTURA DEL SISTEMA

### Frontend (Vue 3 + Ionic)
```
src/
├── components/           # Componenti riutilizzabili
├── views/               # Pagine principali
├── composables/         # Logica business riutilizzabile
├── services/            # API calls e servizi
├── stores/              # Pinia stores
├── types/               # TypeScript interfaces
├── utils/               # Utilities e helpers
└── assets/              # Risorse statiche
```

### Backend (Python FastAPI)
```
app/
├── api/                 # Endpoints API
├── core/                # Configurazioni e sicurezza
├── models/              # Modelli SQLAlchemy
├── schemas/             # Pydantic schemas
├── services/            # Logica business
├── utils/               # Utilities
└── migrations/          # Database migrations
```

---

## 📊 MODELLO DATI

### Tabelle Principali

#### 1. **ospiti** (Tabella centrale)
```sql
CREATE TABLE ospiti (
    id INT PRIMARY KEY AUTO_INCREMENT,
    codice_fiscale VARCHAR(16) UNIQUE,
    nome VARCHAR(100) NOT NULL,
    cognome VARCHAR(100) NOT NULL,
    data_nascita DATE,
    luogo_nascita VARCHAR(100),
    nazionalita VARCHAR(50),
    sesso ENUM('M', 'F', 'Altro'),
    telefono VARCHAR(20),
    email VARCHAR(100),
    foto_profilo VARCHAR(255),
    data_arrivo DATE NOT NULL,
    data_uscita DATE,
    stato ENUM('Attivo', 'Uscito', 'Trasferito', 'Sospeso') DEFAULT 'Attivo',
    vulnerabilita TEXT,
    note_mediche TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

#### 2. **documenti**
```sql
CREATE TABLE documenti (
    id INT PRIMARY KEY AUTO_INCREMENT,
    ospite_id INT,
    tipo_documento ENUM('Passaporto', 'Carta_Identita', 'Permesso_Soggiorno', 'Codice_Fiscale', 'Altro'),
    numero_documento VARCHAR(50),
    data_scadenza DATE,
    ente_rilascio VARCHAR(100),
    file_path VARCHAR(255),
    stato ENUM('Valido', 'Scaduto', 'In_Rinnovo') DEFAULT 'Valido',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (ospite_id) REFERENCES ospiti(id) ON DELETE CASCADE
);
```

#### 3. **alloggi**
```sql
CREATE TABLE alloggi (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(100) NOT NULL,
    tipo ENUM('Camera_Singola', 'Camera_Doppia', 'Dormitorio', 'Appartamento'),
    capienza_max INT NOT NULL,
    occupanti_attuali INT DEFAULT 0,
    piano INT,
    numero_stanza VARCHAR(10),
    stato ENUM('Disponibile', 'Occupato', 'Manutenzione') DEFAULT 'Disponibile',
    note TEXT
);
```

#### 4. **assegnazioni_alloggio**
```sql
CREATE TABLE assegnazioni_alloggio (
    id INT PRIMARY KEY AUTO_INCREMENT,
    ospite_id INT,
    alloggio_id INT,
    data_inizio DATE NOT NULL,
    data_fine DATE,
    attivo BOOLEAN DEFAULT TRUE,
    note TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (ospite_id) REFERENCES ospiti(id) ON DELETE CASCADE,
    FOREIGN KEY (alloggio_id) REFERENCES alloggi(id) ON DELETE CASCADE
);
```

#### 5. **attivita**
```sql
CREATE TABLE attivita (
    id INT PRIMARY KEY AUTO_INCREMENT,
    ospite_id INT,
    tipo_attivita ENUM('Colloquio', 'Visita_Medica', 'Corso_Italiano', 'Formazione_Professionale', 'Ricerca_Lavoro', 'Pratica_Burocratica', 'Altro'),
    titolo VARCHAR(200) NOT NULL,
    descrizione TEXT,
    data_attivita DATETIME NOT NULL,
    durata_minuti INT,
    operatore VARCHAR(100),
    esito ENUM('Completata', 'Rimandata', 'Annullata', 'In_Corso') DEFAULT 'In_Corso',
    note TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (ospite_id) REFERENCES ospiti(id) ON DELETE CASCADE
);
```

#### 6. **operatori**
```sql
CREATE TABLE operatori (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    nome VARCHAR(100) NOT NULL,
    cognome VARCHAR(100) NOT NULL,
    ruolo ENUM('Admin', 'Operatore', 'Responsabile', 'Volontario') NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    attivo BOOLEAN DEFAULT TRUE,
    ultimo_accesso TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### 7. **percorsi_integrazione**
```sql
CREATE TABLE percorsi_integrazione (
    id INT PRIMARY KEY AUTO_INCREMENT,
    ospite_id INT,
    obiettivo VARCHAR(200),
    descrizione TEXT,
    data_inizio DATE NOT NULL,
    data_target DATE,
    stato ENUM('Avviato', 'In_Corso', 'Completato', 'Sospeso') DEFAULT 'Avviato',
    progressione_percentuale INT DEFAULT 0,
    note TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (ospite_id) REFERENCES ospiti(id) ON DELETE CASCADE
);
```

---

## 🎨 INTERFACCIA UTENTE

### Pagine Principali

#### 1. **Dashboard** 📊
- Panoramica ospiti attivi
- Statistiche rapide (nuovi arrivi, uscite, occupazione)
- Attività del giorno
- Allarmi/scadenze importanti

#### 2. **Gestione Ospiti** 👥
- **Lista ospiti**: Ricerca, filtri, ordinamento
- **Profilo ospite**: Dati anagrafici, foto, documenti
- **Cronologia attività**: Timeline delle attività svolte
- **Percorso integrazione**: Obiettivi e progressi

#### 3. **Gestione Alloggi** 🏠
- **Mappa alloggi**: Visualizzazione piano/stanze
- **Assegnazioni**: Gestione occupazione stanze
- **Manutenzione**: Tracking problemi/riparazioni

#### 4. **Attività** 📅
- **Calendario**: Vista mensile/settimanale/giornaliera
- **Nuova attività**: Form creazione con reminder
- **Storico**: Archivio attività completate

#### 5. **Documenti** 📄
- **Archivio digitale**: Upload e categorizzazione
- **Scadenze**: Alert per documenti in scadenza
- **Backup**: Gestione copie di sicurezza

#### 6. **Report** 📈
- **Statistiche**: Grafici e metriche
- **Export**: PDF/Excel per enti esterni
- **Analytics**: Trend e indicatori

---

## 🔧 FUNZIONALITÀ DETTAGLIATE

### Core Features

#### 1. **Gestione Ospiti**
- [x] CRUD completo ospiti
- [x] Upload foto profilo
- [x] Gestione vulnerabilità e note mediche
- [x] Storico permanenze
- [x] Ricerca avanzata e filtri

#### 2. **Sistema Documenti**
- [x] Upload multiplo documenti
- [x] Categorizzazione automatica
- [x] OCR per estrazione dati
- [x] Alert scadenze (30/15/7 giorni)
- [x] Backup cloud automatico

#### 3. **Gestione Alloggi**
- [x] Mappa interattiva struttura
- [x] Assegnazione automatica/manuale
- [x] Tracking occupazione real-time
- [x] Gestione manutenzione

#### 4. **Sistema Attività**
- [x] Calendario integrato
- [x] Notifiche push
- [x] Template attività ricorrenti
- [x] Tracking tempo/esito

#### 5. **Percorsi Integrazione**
- [x] Definizione obiettivi personalizzati
- [x] Milestone e progressi
- [x] Report automatici
- [x] Integrazione con enti esterni

### Advanced Features

#### 1. **Sistema Notifiche**
- Push notifications mobile
- Email alerts per scadenze
- Dashboard alerts in tempo reale

#### 2. **Workflow Automatizzati**
- Auto-assegnazione alloggi
- Reminder attività programmate
- Backup automatico dati

#### 3. **Integrazione Esterna**
- API per enti pubblici
- Export dati statistici anonimi
- Sincronizzazione con altri centri

#### 4. **Sicurezza e Privacy**
- Crittografia dati sensibili
- Log accessi e modifiche
- Anonimizzazione per statistiche
- Compliance GDPR

---

## 📱 DESIGN MOBILE-FIRST

### Principi UX/UI

#### 1. **Layout Responsive**
```css
/* Breakpoints */
Mobile: 320px - 768px
Tablet: 768px - 1024px
Desktop: 1024px+
```

#### 2. **Navigazione**
- Bottom tab navigation (mobile)
- Sidebar navigation (tablet/desktop)
- Swipe gestures per navigazione rapida
- Pull-to-refresh per aggiornamenti

#### 3. **Componenti Chiave**
- **Cards**: Per profili ospiti e alloggi
- **Lists**: Per attività e documenti
- **Modals**: Per form e dettagli
- **Toast**: Per feedback immediato

#### 4. **Accessibilità**
- Alto contrasto per leggibilità
- Font size scalabile
- Touch targets ≥ 44px
- Screen reader friendly

---

## 🏃‍♂️ PIANO DI SVILUPPO

### Fase 1: Foundation (4 settimane)
**Settimane 1-2: Setup Progetto**
- [x] Setup repository Git
- [x] Configurazione environment Docker
- [x] Setup database MySQL
- [x] Struttura base frontend Vue3/Ionic
- [x] Setup backend FastAPI

**Settimane 3-4: Core Backend**
- [x] Modelli database SQLAlchemy
- [x] API endpoints base (CRUD ospiti)
- [x] Sistema autenticazione JWT
- [x] Middleware sicurezza e logging

### Fase 2: Core Features (6 settimane)
**Settimane 5-6: Gestione Ospiti**
- [x] Frontend: Pages ospiti (lista, profilo, form)
- [x] Backend: API complete ospiti
- [x] Upload e gestione foto profilo
- [x] Sistema ricerca e filtri

**Settimane 7-8: Gestione Alloggi**
- [x] Frontend: Visualizzazione alloggi
- [x] Backend: API alloggi e assegnazioni
- [x] Sistema assegnazione automatica
- [x] Dashboard occupazione

**Settimane 9-10: Sistema Attività**
- [x] Frontend: Calendario e form attività
- [x] Backend: API attività e timeline
- [x] Sistema notifiche base
- [x] Template attività ricorrenti

### Fase 3: Advanced Features (4 settimane)
**Settimane 11-12: Documenti e Sicurezza**
- [x] Sistema upload documenti
- [x] OCR e categorizzazione automatica
- [x] Crittografia dati sensibili
- [x] Backup automatico

**Settimane 13-14: Percorsi e Report**
- [x] Gestione percorsi integrazione
- [x] Sistema report e statistiche
- [x] Export dati (PDF/Excel)
- [x] Dashboard analytics

### Fase 4: Mobile e Deploy (3 settimane)
**Settimane 15-16: Mobile App**
- [x] Configurazione Capacitor
- [x] Build Android/iOS
- [x] Push notifications
- [x] Ottimizzazioni performance mobile

**Settimana 17: Deploy e Test**
- [x] Deploy produzione
- [x] Testing completo
- [x] Documentazione utente
- [x] Training operatori

---

## 🔒 SICUREZZA E PRIVACY

### Misure di Sicurezza

#### 1. **Autenticazione e Autorizzazione**
```python
# JWT con refresh token
# Ruoli: Admin, Responsabile, Operatore, Volontario
# Permissions granulari per risorsa
```

#### 2. **Crittografia Dati**
```python
# AES-256 per dati sensibili
# Hashing password con bcrypt
# SSL/TLS per comunicazioni
```

#### 3. **Audit Trail**
```python
# Log di tutti gli accessi
# Tracking modifiche dati
# Backup automatico criptato
```

#### 4. **Compliance GDPR**
- Consenso esplicito trattamento dati
- Right to be forgotten
- Data portability
- Privacy by design

---

## 📊 METRICHE E KPI

### Metriche Operative
- **Tasso occupazione**: % alloggi occupati
- **Tempo medio permanenza**: Giorni di soggiorno
- **Attività per ospite**: N° attività/mese
- **Tasso integrazione**: % obiettivi raggiunti

### Metriche Tecniche
- **Performance app**: Tempo caricamento < 2s
- **Uptime sistema**: > 99.5%
- **Backup success rate**: 100%
- **User satisfaction**: > 4.5/5

---

## 💰 STIMA COSTI SVILUPPO

### Risorse Umane (17 settimane)
- **1 Senior Full-Stack Developer**: €700/giorno × 85 giorni = €59,500
- **1 UI/UX Designer**: €400/giorno × 20 giorni = €8,000
- **1 Mobile Developer**: €600/giorno × 15 giorni = €9,000

### Infrastruttura Annuale
- **Server VPS**: €100/mese × 12 = €1,200
- **Database hosting**: €50/mese × 12 = €600
- **Storage cloud**: €30/mese × 12 = €360
- **SSL e domini**: €200/anno

### Totale Stimato: €78,860

---

## 🚀 ROADMAP FUTURA

### V2.0 (6 mesi post-lancio)
- [ ] App mobile nativa completa
- [ ] Integrazione AI per predizioni
- [ ] Multi-lingua (EN, FR, AR)
- [ ] API pubbliche per enti

### V3.0 (12 mesi post-lancio)
- [ ] Modulo contabilità integrato
- [ ] Sistema donazioni online
- [ ] Network multi-centro
- [ ] Analytics predittivi avanzati

---

## 📞 CONCLUSIONI

Questo progetto rappresenta un investimento significativo nello sviluppo di uno strumento digitale che può realmente migliorare la gestione dei centri d'accoglienza e, di conseguenza, la qualità di vita degli ospiti.

### Benefici Attesi:
1. **Efficienza operativa** +40%
2. **Riduzione errori** -70%
3. **Tracciabilità completa** 100%
4. **Soddisfazione operatori** +60%

### Rischi e Mitigazioni:
- **Cambio normative**: Architettura modulare per adattamenti rapidi
- **Resistance to change**: Training intensivo e change management
- **Data security**: Security-first approach e audit regolari

Il progetto è ambizioso ma fattibile con il team giusto e un approccio metodico. La tecnologia scelta è moderna, scalabile e perfettamente adatta alle esigenze del dominio.

**Ready to start? 🚀**