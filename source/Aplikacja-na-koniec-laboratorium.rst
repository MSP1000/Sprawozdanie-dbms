Aplikacja na koniec laboratorium
---------------------------------

Student
~~~~~~~~~~~~~~~~
- Wincenty Wensker
- Michał Pawlica

Nr albumu
~~~~~~~~~~~~~~~~
- 272760
- 272758

Data sprawozdania
~~~~~~~~~~~~~~~~
- 3.10.2024

Wprowadzenie
-------------------

Celem projektu było zaprojektowanie i implementacja bazy danych w systemie SQLite oraz PostgreSQL która przechowuje informacje o zawodnikach oraz kategoriach sportowych do których należą. Projekt obejmował stworzenie odpowiednich tabel zdefiniowanie relacji między nimi oraz implementację podstawowych operacji na danych takich jak dodawanie modyfikowanie usuwanie i odczytywanie rekordów.

Założenia co do bazy danych
---------------------------------

Baza danych ma służyć jako archiwum wybranych sportowców do małego magazynu z rankingiem sportowców. Pozwala na zapis i modyfikacje sportowca oraz wszystkich kategorii w których sportowiec brał udział liczbę zdobytych medali wraz z datą rozpoczęcia i jeżeli zawodnik jest już na emeryturze końca kariery. Baza dodatkowo pozwala na usuwanie sportowców i kategorii choć ta operacja jako że działamy na archiwum wedle założeń nie powinna nigdy być przeprowadzona. Baza pozwala na zapis wszystkich danych w plikach csv oraz stosuje symbol zastępczy dla braku daty emerytury w postaci daty 2222-10-31. Oprócz bazy powstały dwie aplikacje. Interfejs służący do wprowadzania zebranych danych oraz analizator to podglądania z wykresami oraz generowania wykresów porównujące poszczególne kraje. Dodatkowo do bazy danych powstała seria funkcji w języku python pozwalających na szybkie tworzenie skryptów wykonujących operacje na bazie danych.

Projekt Modelu Koncepcyjnego
---------------------------------

Model koncepcyjny bazy danych jest abstrakcyjnym przedstawieniem danych oraz związku między nimi. W naszym przypadku baza danych składa się z dwóch tabel: Zawodnicy oraz Kategorie.

Zawodnicy:
~~~~~~~~~~~~~~~~
- key: unikalny identyfikator zawodnika.
- Imie: imię zawodnika.
- Nazwisko: nazwisko zawodnika.
- Pseudonim: pseudonim zawodnika.
- Plec: płeć zawodnika.
- Narodowsc: narodowość zawodnika.

Kategorie:
~~~~~~~~~~~~~~~~
- key: unikalny identyfikator kategorii.
- NazwaKategorii: nazwa kategorii sportowej.
- NumerZawodnika: numer zawodnika klucz obcy odnoszący się do klucza w tabeli Zawodnicy.
- LiczbaZlotychMedali: liczba złotych medali zdobytych przez zawodnika.
- LiczbaSrebnychMedali: liczba srebrnych medali zdobytych przez zawodnika.
- LiczbaBrazowychMedali: liczba brązowych medali zdobytych przez zawodnika.
- CzyAktynwy: informacja czy zawodnik jest aktywny sportowo.
- DataEmerytura: (Jeżeli zawodnik nieaktywny)data przejścia na emeryturę.
- DataStart: data rozpoczęcia kariery sportowej.

Związek między tymi dwoma tabelami jest taki że każda kategoria zawiera się w zawodniku co jest reprezentowane przez klucz obcy NumerZawodnika w tabeli Kategorie.

Projekt Modelu Fizycznego
---------------------------------

Model fizyczny bazy danych zawiera szczegółowe odwzorowanie tabel i relacji między nimi z uwzględnieniem specyfiki systemu zarządzania bazą danych SQLite. Oto szczegółowy opis tabel:

Tabela Zawodnicy (SQLite)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

   CREATE TABLE IF NOT EXISTS Zawodnicy(
      key INT AUTO_INCREMENT PRIMARY KEY,
      Imie VARCHAR(15),
      Nazwisko VARCHAR(15),
      Pseudonim VARCHAR(15),
      Plec BOOL,
      Narodowsc VARCHAR(15)
   );

Tabela Kategorie (SQLite)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

   CREATE TABLE IF NOT EXISTS Kategorie(
      key INT AUTO_INCREMENT PRIMARY KEY,
      NazwaKategorii VARCHAR(15),
      NumerZawodnika VARCHAR(15),
      LiczbaZlotychMedali INT,
      LiczbaSrebnychMedali INT,
      LiczbaBrazowychMedali INT,
      CzyAktynwy BOOL,
      DataEmerytura DATE,
      DataStart DATE
   );

Tabela Zawodnicy (PostgreSQL)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

   CREATE TABLE IF NOT EXISTS Zawodnicy(
      key INT PRIMARY KEY,
      Imie VARCHAR(15),
      Nazwisko VARCHAR(15),
      Pseudonim VARCHAR(15),
      Plec BOOL,
      Narodowsc VARCHAR(15)
   );

Tabela Kategorie (PostgreSQL)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

   CREATE TABLE IF NOT EXISTS Kategorie(
      key INT PRIMARY KEY,
      NazwaKategorii VARCHAR(30),
      NumerZawodnika VARCHAR(15),
      LiczbaZlotychMedali INT,
      LiczbaSrebnychMedali INT,
      LiczbaBrazowychMedali INT,
      CzyAktynwy BOOL,
      DataEmerytura DATE,
      DataStart DATE
   );

W powyższych tabelach zdefiniowano odpowiednie typy danych oraz relacje między tabelami. Klucz obcy NumerZawodnika w tabeli Kategorie odnosi się do klucza głównego w tabeli Zawodnicy.

Implementacja Operacji na Bazie Danych
-----------------------------------------

W projekcie zaimplementowano następujące funkcje do obsługi bazy danych SQLite:

- ``dbcreater()``: Tworzy połączenie z bazą danych SQLite ``bazasqliteWiM.db`` i dwie tabele: Zawodnicy i Kategorie jeśli nie istnieją.
- ``READERzawodnicyfromCSV()``: Odczytuje dane z pliku CSV ``dataZaw.csv`` i wyświetla je w konsoli.
- ``READERkategoriefromCSV()``: Odczytuje dane z pliku CSV ``dataZ.csv`` i wyświetla je w konsoli.
- ``DodajZawodnika(g, im, na, ps, plec, nar)``: Dodaje nowego zawodnika do tabeli Zawodnicy.
- ``ZmienZawodnika(g, im, na, ps, plec, nar)``: Aktualizuje dane zawodnika w tabeli Zawodnicy.
- ``UsunZawodnika(g)``: Usuwa zawodnika z tabeli Zawodnicy na podstawie klucza podstawowego.
- ``DodajKategorie(g, Ka, Nz, Lz, Ls, Lb, Za, De, DPz)``: Funkcja służy do dodawania nowego rekordu do tabeli Kategorie.
- ``ZmienKategorie(g, Ka, Nz, Lz, Ls, Lb, Za, De, DPz)``: Aktualizuje dane rekordu w tabeli Kategorie.
- ``UsunKategorie(g)``: Usuwa rekord z tabeli Kategorie na podstawie klucza podstawowego.
- ``ZRZUTZAW()``: Zapisuje/nadpisuje aktualny stan tabeli Zawodnicy do pliku csv.
- ``ZRZUTZKAT()``: Zapisuje/nadpisuje aktualny stan tabeli Kategorie do pliku csv.
- ``ZRZUTZALL()``: Zapisuje/nadpisuje aktualny stan obu tabel do pliku csv.
- ``dbclean()``: Usawa tablice i zastępuje je nowymi.
- ``GenerujDanePierwszaTablica()``: Generuje przykładowe dane do tablicy Zawodnicy.
- ``GenerujDaneDrugaTablica(int i)``: Generuje przykładowe dane do tablicy Kategorie.
- ``dblosowe()``: Korzysta z ``GenerujDaneDrugaTablica`` i ``GenerujDanePierwszaTablica`` do generowania wielu danych naraz.

W projekcie zaimplementowano następujące funkcje do obsługi bazy danych PostgreSQL:

- ``DBcreate(plikJSON)``: Tworzy połączenie z bazą danych postgres i dwie tabele: Zawodnicy i Kategorie jeśli nie istnieją.
- ``CCSVtoDB (dataZ, dataZaw, plikJSON)``: Przerzuca dane z plików csv do bazy danych.
- ``addZawodnicy (plikJSON, g, im, na, ps, plec, nar)``: Dodaje nowego zawodnika do tabeli Zawodnicy.
- ``updateZawodnicy (plikJSON, g, im, na, ps, plec, nar)``: Aktualizuje dane zawodnika o kluczu równym g w tabeli Zawodnicy.
- ``deleteZawodnicy (plikJSON, g)``: Usuwa zawodnika z tabeli Zawod
