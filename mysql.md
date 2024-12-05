<?php
$host = 'localhost'; // Datenbank-Host
$dbname = 'testing'; // Name der Datenbank
$username = 'root'; // Benutzername für die Datenbank
$password = ''; // Passwort für die Datenbank

try {
    // Verbindung zur Datenbank herstellen
    $pdo = new PDO("mysql:host=$host", $username, $password);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

    // Datenbank erstellen, falls sie noch nicht existiert
    $sqlDatabase = "CREATE DATABASE IF NOT EXISTS $dbname;";
    $pdo->exec($sqlDatabase);

    // Datenbank auswählen
    $pdo->exec("USE $dbname;");

    // SQL-Statements zur Erstellung der Tabellen
    $sqlKunde = "
        CREATE TABLE IF NOT EXISTS Kunde (
            KundenID INTEGER PRIMARY KEY AUTO_INCREMENT,
            Name TEXT NOT NULL
        );
    ";

    $sqlRechnung = "
        CREATE TABLE IF NOT EXISTS Rechnung (
            RechnungsID INTEGER PRIMARY KEY AUTO_INCREMENT,
            Datum TEXT,
            KundenID INTEGER,
            FOREIGN KEY (KundenID) REFERENCES Kunde(KundenID)
        );
    ";

    $sqlTyp = "
        CREATE TABLE IF NOT EXISTS Typ (
            TypID INTEGER PRIMARY KEY AUTO_INCREMENT,
            Name TEXT
        );
    ";

    $sqlArtikel = "
        CREATE TABLE IF NOT EXISTS Artikel (
            ArtikelID INTEGER PRIMARY KEY AUTO_INCREMENT,
            Nummer TEXT,
            Name TEXT,
            TypNr INTEGER,
            FOREIGN KEY (TypNr) REFERENCES Typ(TypID)
        );
    ";

    $sqlPosition = "
        CREATE TABLE IF NOT EXISTS Position (
            PosID INTEGER PRIMARY KEY AUTO_INCREMENT,
            RechnungsNr INTEGER,
            ArtikelNr INTEGER,
            Preis NUMERIC(10, 2),
            Anzahl INTEGER,
            FOREIGN KEY (RechnungsNr) REFERENCES Rechnung(RechnungsID),
            FOREIGN KEY (ArtikelNr) REFERENCES Artikel(ArtikelID)
        );
    ";

    $sqlKundeOrtAdd = "
        ALTER TABLE Kunde add Ort text
    ";

    $sqlKundeOrtModify = "
        ALTER TABLE Kunde MODIFY Ort VARCHAR(10)
    ";

    $sqlKundeOrtDrop = "
        ALTER TABLE Kunde drop Ort
    ";

    $sqlKundeDummy = "
        CREATE TABLE IF NOT EXISTS KundeDummy (
            KundenID INTEGER PRIMARY KEY AUTO_INCREMENT,
            Name TEXT NOT NULL
        );
    ";

    $sqlKundeDummyDrop = "
        DROP TABLE IF EXISTS KundeDummy;
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
    $pdo->exec($sqlKundeOrtAdd);
    $pdo->exec($sqlKundeOrtModify);
    $pdo->exec($sqlKundeOrtDrop);
    $pdo->exec($sqlKundeDummy);
    $pdo->exec($sqlKundeDummyDrop);
    $pdo->exec($sqlInsertTyp);

    echo "Datenbank und Tabellen wurden erfolgreich erstellt.";

} catch (PDOException $e) {
    // Fehlerbehandlung
    echo "Fehler bei der Erstellung der Datenbank oder Tabellen: " . $e->getMessage();
}
?>
