import sqlite3

# Datenbank-Datei
dbname = 'testing.db'

try:
    # Verbindung zur SQLite-Datenbank herstellen
    conn = sqlite3.connect(dbname)
    cursor = conn.cursor()

    # SQL-Statements zur Erstellung der Tabellen
    sql_kunde = """
        CREATE TABLE IF NOT EXISTS Kunde (
            KundenID INTEGER PRIMARY KEY AUTOINCREMENT,
            Name TEXT NOT NULL
        );
    """

    sql_rechnung = """
        CREATE TABLE IF NOT EXISTS Rechnung (
            RechnungsID INTEGER PRIMARY KEY AUTOINCREMENT,
            Datum TEXT,
            KundenID INTEGER,
            FOREIGN KEY (KundenID) REFERENCES Kunde(KundenID)
        );
    """

    sql_typ = """
        CREATE TABLE IF NOT EXISTS Typ (
            TypID INTEGER PRIMARY KEY AUTOINCREMENT,
            Name TEXT
        );
    """

    sql_artikel = """
        CREATE TABLE IF NOT EXISTS Artikel (
            ArtikelID INTEGER PRIMARY KEY AUTOINCREMENT,
            Nummer TEXT,
            Name TEXT,
            TypNr INTEGER,
            FOREIGN KEY (TypNr) REFERENCES Typ(TypID)
        );
    """

    sql_position = """
        CREATE TABLE IF NOT EXISTS Position (
            PosID INTEGER PRIMARY KEY AUTOINCREMENT,
            RechnungsNr INTEGER,
            ArtikelNr INTEGER,
            Preis NUMERIC(10, 2),
            Anzahl INTEGER,
            FOREIGN KEY (RechnungsNr) REFERENCES Rechnung(RechnungsID),
            FOREIGN KEY (ArtikelNr) REFERENCES Artikel(ArtikelID)
        );
    """

    # Spalte hinzufügen
    sql_kunde_ort_add = """
        ALTER TABLE Kunde ADD COLUMN Ort TEXT;
    """

    # Dummy-Tabelle erstellen und wieder löschen
    sql_kunde_dummy = """
        CREATE TABLE IF NOT EXISTS KundeDummy (
            KundenID INTEGER PRIMARY KEY AUTOINCREMENT,
            Name TEXT NOT NULL
        );
    """

    sql_kunde_dummy_drop = """
        DROP TABLE IF EXISTS KundeDummy;
    """

    # Beispiel-Daten einfügen
    sql_insert_typ = """
        INSERT INTO Typ (TypID, Name) VALUES (123, 'Mainboard');
    """

    # Tabellen erstellen
    cursor.execute(sql_kunde)
    cursor.execute(sql_rechnung)
    cursor.execute(sql_typ)
    cursor.execute(sql_artikel)
    cursor.execute(sql_position)

    # Spalte hinzufügen (falls sie noch nicht existiert)
    try:
        cursor.execute(sql_kunde_ort_add)
    except sqlite3.OperationalError as e:
        print(f"Spalte Ort konnte nicht hinzugefügt werden: {e}")

    # Dummy-Tabelle erstellen und löschen
    cursor.execute(sql_kunde_dummy)
    cursor.execute(sql_kunde_dummy_drop)

    # Beispiel-Daten einfügen
    cursor.execute(sql_insert_typ)

    # Änderungen speichern und Verbindung schließen
    conn.commit()
    conn.close()

    print("Datenbank und Tabellen wurden erfolgreich erstellt.")

except sqlite3.Error as e:
    print(f"Fehler bei der Erstellung der Datenbank oder Tabellen: {e}")
