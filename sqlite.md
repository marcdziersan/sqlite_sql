```
<?php
$dbname = 'testing.db'; // SQLite-Datenbank-Datei

try {
    // Verbindung zur SQLite-Datenbank herstellen
    $pdo = new PDO("sqlite:$dbname");
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

    // SQL-Statements zur Erstellung der Tabellen
    $sqlKunde = "
        CREATE TABLE IF NOT EXISTS Kunde (
            KundenID INTEGER PRIMARY KEY AUTOINCREMENT,
            Name TEXT NOT NULL
        );
    ";

    $sqlRechnung = "
        CREATE TABLE IF NOT EXISTS Rechnung (
            RechnungsID INTEGER PRIMARY KEY AUTOINCREMENT,
            Datum TEXT,
            KundenID INTEGER,
            FOREIGN KEY (KundenID) REFERENCES Kunde(KundenID)
        );
    ";

    $sqlTyp = "
        CREATE TABLE IF NOT EXISTS Typ (
            TypID INTEGER PRIMARY KEY AUTOINCREMENT,
            Name TEXT
        );
    ";

    $sqlArtikel = "
        CREATE TABLE IF NOT EXISTS Artikel (
            ArtikelID INTEGER PRIMARY KEY AUTOINCREMENT,
            Nummer TEXT,
            Name TEXT,
            TypNr INTEGER,
            FOREIGN KEY (TypNr) REFERENCES Typ(TypID)
        );
    ";

    $sqlPosition = "
        CREATE TABLE IF NOT EXISTS Position (
            PosID INTEGER PRIMARY KEY AUTOINCREMENT,
            RechnungsNr INTEGER,
            ArtikelNr INTEGER,
            Preis NUMERIC(10, 2),
            Anzahl INTEGER,
            FOREIGN KEY (RechnungsNr) REFERENCES Rechnung(RechnungsID),
            FOREIGN KEY (ArtikelNr) REFERENCES Artikel(ArtikelID)
        );
    ";

    // SQLite unterstützt keine direkte Änderung von Spalten, daher werden hier Workarounds benötigt.
    $sqlKundeOrtAdd = "
        ALTER TABLE Kunde ADD COLUMN Ort TEXT;
    ";

    $sqlInsertTyp = "
        INSERT INTO Typ (TypID, Name) VALUES (123, 'Mainboard');
    ";

    // Tabellen erstellen
    $pdo->exec($sqlKunde);
    $pdo->exec($sqlRechnung);
    $pdo->exec($sqlTyp);
    $pdo->exec($sqlArtikel);
    $pdo->exec($sqlPosition);

    // Spalte hinzufügen
    try {
        $pdo->exec($sqlKundeOrtAdd);
    } catch (PDOException $e) {
        // Falls Spalte bereits existiert
        echo "Spalte Ort konnte nicht hinzugefügt werden: " . $e->getMessage();
    }

    // Dummy-Tabelle erstellen und wieder löschen (SQLite spezifisch)
    $sqlKundeDummy = "
        CREATE TABLE IF NOT EXISTS KundeDummy (
            KundenID INTEGER PRIMARY KEY AUTOINCREMENT,
            Name TEXT NOT NULL
        );
    ";

    $sqlKundeDummyDrop = "
        DROP TABLE IF EXISTS KundeDummy;
    ";

    $pdo->exec($sqlKundeDummy);
    $pdo->exec($sqlKundeDummyDrop);

    // Beispiel-Daten einfügen
    $pdo->exec($sqlInsertTyp);

    echo "Datenbank und Tabellen wurden erfolgreich erstellt.";

} catch (PDOException $e) {
    // Fehlerbehandlung
    echo "Fehler bei der Erstellung der Datenbank oder Tabellen: " . $e->getMessage();
}
?>
```
