## Esercizio 15 ER

### Istruzioni
1. Creare un diagramma ER delle classi utilizzando la sintassi di MermaidJS. Consegnare un file markdown con il diagramma ER in un blocco mermaid.

2. Creare l'applicazione Flask per la gestione dell'albergo

## Scenario
Il sistema permette di gestire le prenotazioni in un albergo. L'albergo ha diverse camere, e ogni camera ha un numero, un tipo (singola, doppia, suite) e una disponibilità (occupata o libera). Il sistema deve permettere di:

1. Visualizzare le camere.
@app.route("/")
def list_entries():
    conn = get_db_connection()
    cursor = conn.cursor(dictionary=True)
    cursor.execute("SELECT * FROM CAMERA")
    entries = cursor.fetchall()
    cursor.close()
    conn.close()
    return render_template("list.html", entries=entries)


2. Visualizzare le camere disponibili.
@app.route("/free")
def list_free():
    conn = get_db_connection()
    cursor = conn.cursor(dictionary=True)
    cursor.execute("SELECT * FROM CAMERA WHERE disponibile = 1")
    entries = cursor.fetchall()
    cursor.close()
    conn.close()
    return render_template("list.html", entries=entries)


3. Aggiungere nuove camere all'albergo.
@app.route("/add", methods=("GET", "POST"))
def add_entries():
    if request.method == "POST":
        conn = get_db_connection()
        cursor = conn.cursor()
        numero = request.form["numero"]
        tipo = request.form["tipo"]
        disponibile = request.form["disponibile"]
        prezzo = request.form["prezzo"]
        numero_posti = request.form["numero_posti"]
        cursor.execute(
            "INSERT INTO CAMERA (numero, tipo, disponibile, prezzo, numero_posti) VALUES (%s, %s, %s, %s, %s)",
            (numero, tipo, disponibile, prezzo, numero_posti),
        )
        conn.commit()
        cursor.close()
        conn.close()
        return redirect(url_for("list_entries"))
    return render_template("add.html")

4. Visualizzare le prenotazioni effettuate.
@app.route("/prenotations")
def list_prenotations():
    conn = get_db_connection()
    cursor = conn.cursor(dictionary=True)
    cursor.execute("SELECT * FROM PRENOTAZIONE")
    entries = cursor.fetchall()
    cursor.close()
    conn.close()
    return render_template("list_prenotations.html", entries=entries)


5. Prenotare una camera (verificando se è disponibile).
@app.route("/prenotation_room", methods=("GET", "POST"))
def prenotation():
    return render_template("new_prenotation.html")

@app.route("/prenotating", methods=("GET", "POST"))
def prenotation_room():
    if request.method == "POST":
        conn = get_db_connection()
        cursor = conn.cursor(dictionary=True)
        numero = request.form["numero"]
        data_arrivo = request.form["arrivo"]
        data_partenza = request.form["partenza"]
        nome_cliente = request.form["cliente"]

        cursor.execute("SELECT disponibile FROM CAMERA WHERE numero=%s", (numero,))
        camera = cursor.fetchone()

        if camera and camera["disponibile"]:
            cursor.execute(
                "INSERT INTO PRENOTAZIONE (data_arrivo, data_partenza, nome_cliente) VALUES (%s, %s, %s)",
                (data_arrivo, data_partenza, nome_cliente),
            )
            prenotazione_id = cursor.lastrowid

            cursor.execute(
                "INSERT INTO CAMERAPRENOTAZIONE (id_camera, id_prenotazione) VALUES (%s, %s)",
                (numero, prenotazione_id),
            )

            cursor.execute(
                "UPDATE CAMERA SET disponibile = 0 WHERE numero = %s", (numero,)
            )

            conn.commit()
            cursor.close()
            conn.close()
            return "<h1>Prenotazione Fatta!</h1>"
        else:
            cursor.close()
            conn.close()
            return "<h1>Camera non disponibile</h1>"


Il sistema deve includere due classi principali:
1. Camera: rappresenta una singola camera dell'albergo.
2. Albergo: rappresenta l'albergo che gestisce le camere e le prenotazioni.




# Hotel Management System

Questo progetto è un sistema di gestione alberghiera basato su Flask e MariaDB.

## Prerequisiti

- Python 3.6 o superiore
- MariaDB
- pip (Python package installer)

## Installazione

### 1. Installare MariaDB

```bash
sudo apt update
sudo apt install mariadb-server
```

### 2. Avviare e abilitare il servizio MariaDB

```bash
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

### 3. Configurare MariaDB

```bash
sudo mysql_secure_installation
```

Seguire le istruzioni per configurare la sicurezza di MariaDB.

### 4. Creare il database e l'utente

Accedi a MariaDB come utente root:

```bash
sudo mysql -u root -p
```

Esegui i seguenti comandi per creare il database e l'utente:

```sql
CREATE DATABASE uva_hotel;
CREATE USER 'uva'@'localhost' IDENTIFIED BY 'uva';
GRANT ALL PRIVILEGES ON uva_hotel.* TO 'uva'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### 5. Eseguire il file SQL per creare le tabelle e inserire i dati

```bash
mysql -u uva -p uva_hotel < /home/crisesy/Es15/test2/esercizi_flask/esercizi_flask/es15/es15.sql
```

### 6. Creare e attivare un ambiente virtuale

```bash

#Installare venv
sudo apt install python3-venv

python3 -m venv venv
source venv/bin/activate
```

### 7. Installare le dipendenze

```bash
pip install mysql-connector-python flask werkzeug
```

### 8. Avviare l'applicazione Flask

```bash
python /home/cris/EseciziInformmatica/Esercizio15Finito/Esercizio15Finito/esercizi_flask/esercizi_flask/es15/e15.py
```

### 9. Accedere all'applicazione

Apri il browser e vai a http://127.0.0.1:5908/ per vedere l'applicazione in esecuzione.

## Struttura del Progetto

- **es15.py**: File principale dell'applicazione Flask.
- **es15.sql**: File SQL per creare il database e le tabelle necessarie.

## Rotte Disponibili

- **/**: Elenca tutte le camere.
- **/free**: Elenca le camere disponibili.
- **/add**: Aggiungi una nuova camera.
- **/prenotation_room**: Visualizza il form per creare una nuova prenotazione.
- **/prenotating**: Crea una nuova prenotazione.
- **/prenotations**: Elenca tutte le prenotazioni.

## Note

Assicurati di cambiare **app.secret\_key** in **es15.py** con una chiave segreta sicura.

Salva questo contenuto in un file chiamato **README.md** nella directory principale del tuo progetto.

### 10 - Passaggi da ripetere per avviare gli esercizi successivamente.
```bash

#Attiva l'ambiente virtuale:

source venv/bin/activate

#Avvia l'applicazione Flask:
python /home/cris/EseciziInformmatica/Esercizio15Finito/Esercizio15Finito/esercizi_flask/esercizi_flask/es15/e15.py

#Assicurati che il servizio MariaDB sia in esecuzione:
sudo systemctl start mariadb

```
