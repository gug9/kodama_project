# Kodama

Kodama è un sistema generativo basato su DSL dichiarativa, simile a Terraform ma orientato alla generazione di codice software. L'utente definisce un progetto tramite un file `.kodama` (DSL proprietaria ispirata a YAML), specificando la struttura del progetto, classi, funzioni, moduli, API, database, relazioni e tipo di codice da generare.

## Caratteristiche principali

- **DSL dichiarativa**: Definisci il tuo progetto in un formato YAML-like semplice e intuitivo
- **Generazione completa di progetti**: Backend, frontend o fullstack
- **Supporto per diversi linguaggi**: Python, TypeScript, HTML, CSS, SQL
- **Integrazione con LLM locale**: Utilizza Ollama per la generazione di codice
- **Supporto database**: SQLite, PostgreSQL, MySQL
- **Generazione selettiva**: Genera solo moduli, funzioni, frontend o backend specifici
- **Approccio Terraform-like**: Comandi `plan` e `apply` per visualizzare e applicare i cambiamenti

## Installazione

### Prerequisiti

- Python 3.11+
- [Ollama](https://ollama.ai/) installato localmente

### Installazione da PyPI

```bash
pip install kodama
```

### Installazione da sorgente

```bash
git clone https://github.com/yourusername/kodama.git
cd kodama
pip install .
```

## Utilizzo

### Inizializzazione di un progetto

Per creare un nuovo file `.kodama` di base:

```bash
kodama init
```

Questo comando creerà un file `project.kodama` nella directory corrente con una struttura di base.

### Validazione di un file .kodama

Per verificare che un file `.kodama` sia valido:

```bash
kodama validate project.kodama
```

### Generazione di un progetto completo

Per generare un progetto completo a partire da un file `.kodama`:

```bash
kodama generate project.kodama
```

### Visualizzazione del piano di generazione

Per visualizzare un'anteprima di ciò che verrà generato:

```bash
kodama plan project.kodama
```

### Applicazione dei cambiamenti

Per applicare i cambiamenti al progetto:

```bash
kodama apply project.kodama
```

### Generazione selettiva

Per generare solo specifiche parti del progetto:

```bash
# Genera solo i moduli
kodama module project.kodama

# Genera solo le funzioni
kodama function project.kodama

# Genera solo il frontend
kodama frontend project.kodama

# Genera solo il backend
kodama backend project.kodama
```

### Gestione del modello LLM

Per scaricare e verificare il modello Ollama:

```bash
kodama pull-model
```

## Sintassi del file .kodama

Un file `.kodama` è strutturato in tre sezioni principali:

1. **project**: Definisce le informazioni di base del progetto
2. **modules**: Definisce i moduli, le classi e i metodi del progetto
3. **database**: Definisce il database, le tabelle e i campi

### Esempio di file .kodama

```yaml
project:
  name: "note-app"
  type: "backend"
  language: "python"

modules:
  - name: "note_handler"
    classes:
      - name: "NoteManager"
        methods:
          - name: "create_note"
            inputs: ["content"]
          - name: "get_notes"
            depends_on: ["create_note"]

database:
  engine: "sqlite"
  tables:
    - name: "notes"
      fields:
        - name: "id"
          type: "uuid"
        - name: "content"
          type: "text"
        - name: "created_at"
          type: "datetime"
```

### Sezione project

La sezione `project` definisce le informazioni di base del progetto:

- **name**: Nome del progetto
- **type**: Tipo di progetto (`backend`, `frontend`, `fullstack`)
- **language**: Linguaggio principale del progetto (`python`, `typescript`, `javascript`)

### Sezione modules

La sezione `modules` definisce i moduli, le classi e i metodi del progetto:

- **name**: Nome del modulo
- **classes**: Lista di classi nel modulo
  - **name**: Nome della classe
  - **methods**: Lista di metodi nella classe
    - **name**: Nome del metodo
    - **inputs**: Lista di parametri di input
    - **depends_on**: Lista di metodi da cui dipende

### Sezione database

La sezione `database` definisce il database, le tabelle e i campi:

- **engine**: Motore del database (`sqlite`, `postgresql`, `mysql`)
- **tables**: Lista di tabelle nel database
  - **name**: Nome della tabella
  - **fields**: Lista di campi nella tabella
    - **name**: Nome del campo
    - **type**: Tipo del campo (`text`, `integer`, `float`, `boolean`, `date`, `datetime`, `uuid`)

## Architettura

Kodama è composto da diversi moduli principali:

1. **DSL Parser**: Interpreta e valida i file `.kodama`
2. **CLI**: Fornisce l'interfaccia a riga di comando
3. **LLM Wrapper**: Gestisce l'interazione con il modello Ollama
4. **Planner**: Pianifica la generazione del codice
5. **Database Manager**: Gestisce la generazione di modelli e schemi di database

## Esempio completo

Ecco un esempio più completo di un file `.kodama` per un'applicazione di note:

```yaml
project:
  name: "note-app"
  type: "backend"
  language: "python"

modules:
  - name: "note_handler"
    classes:
      - name: "NoteManager"
        methods:
          - name: "create_note"
            inputs: ["content", "title"]
          - name: "get_notes"
            depends_on: ["create_note"]
          - name: "get_note_by_id"
            inputs: ["note_id"]
          - name: "update_note"
            inputs: ["note_id", "content", "title"]
            depends_on: ["get_note_by_id"]
          - name: "delete_note"
            inputs: ["note_id"]
            depends_on: ["get_note_by_id"]
  
  - name: "user_handler"
    classes:
      - name: "UserManager"
        methods:
          - name: "register_user"
            inputs: ["username", "email", "password"]
          - name: "login_user"
            inputs: ["email", "password"]
          - name: "get_user_profile"
            inputs: ["user_id"]
            depends_on: ["login_user"]
          - name: "update_user_profile"
            inputs: ["user_id", "username", "email"]
            depends_on: ["get_user_profile"]

database:
  engine: "sqlite"
  tables:
    - name: "notes"
      fields:
        - name: "id"
          type: "uuid"
        - name: "title"
          type: "text"
        - name: "content"
          type: "text"
        - name: "created_at"
          type: "datetime"
        - name: "updated_at"
          type: "datetime"
        - name: "user_id"
          type: "uuid"
    
    - name: "users"
      fields:
        - name: "id"
          type: "uuid"
        - name: "username"
          type: "text"
        - name: "email"
          type: "text"
        - name: "password_hash"
          type: "text"
        - name: "created_at"
          type: "datetime"
        - name: "last_login"
          type: "datetime"
```

## Estensioni future

- Supporto per container Docker
- Integrazione con vector DB (es. ChromeDB) per reasoning e memoria
- Logging semantico dei task
- File kodama.lock per snapshot dei modelli/generazioni

## Contribuire

Le contribuzioni sono benvenute! Per contribuire:

1. Fai un fork del repository
2. Crea un branch per la tua feature (`git checkout -b feature/amazing-feature`)
3. Committa i tuoi cambiamenti (`git commit -m 'Add some amazing feature'`)
4. Pusha al branch (`git push origin feature/amazing-feature`)
5. Apri una Pull Request

## Licenza

Questo progetto è distribuito sotto la licenza MIT. Vedi il file `LICENSE` per maggiori informazioni.
