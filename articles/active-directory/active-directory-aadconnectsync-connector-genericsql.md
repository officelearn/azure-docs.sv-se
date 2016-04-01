<properties
   pageTitle="Azure AD Connect-Synchronisierung: generischer SQL-Connector"
   description="Dieser Artikel beschreibt die Konfiguration des generischen SQL-Connectors von Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="12/11/2015"
   ms.author="andkjell"/>

# Technische Referenz für den generischen SQL-Connector

Dieser Artikel beschreibt den generischen SQL-Connector. Der Artikel bezieht sich auf folgende Produkte:

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Hotfix 4.1.3461.0 müssen verwenden oder höher [KB2870703](https://support.microsoft.com/kb/2870703).

Der Connector steht als Download für MIM2016 und FIM2010R2 der [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

## Übersicht über den generischen SQL-Connector

Der generische SQL-Connector ermöglicht die Integration des Synchronisierungsdiensts in ein Datenbanksystem mit ODBC-Konnektivität.  

Im Anschluss finden Sie einen allgemeinen Überblick über die von der aktuellen Connectorversion unterstützten Features:

| Funktion | Support |
| --- | --- |
| Verbundene Datenquelle | Der Connector wird mit allen 64-Bit-ODBC-Treibern unterstützt. Es wurde mit den folgenden getestet: <li>Microsoft SQL Server und SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 und 11 g</li><li>MySQL 5.x</li>
| Szenarios | <li>Objektlebenszyklusverwaltung</li><li>Kennwortverwaltung</li> |
| Vorgänge | <li>Vollständiger Import und Deltaimport, Export</li><li>Fügen Sie für den Export: hinzu, löschen Sie, aktualisieren und ersetzen</li><li>Legen Sie das Kennwort, ändern Kennwort</li>
| Schema | <li>Dynamische Erkennung von Objekten und Attributen</li>

### Voraussetzungen

Zur Verwendung des Connectors muss auf dem Synchronisierungsserver neben den weiter oben ggf. erwähnten Hotfixes Folgendes vorhanden sein:

- Microsoft .NET 4.5.2 Framework oder eine höhere Version
- 64-Bit-ODBC-Clienttreiber

### Berechtigungen für die verbundene Datenquelle

Zum Erstellen des generischen SQL-Connectors sowie zum Ausführen der unterstützten Aufgaben benötigen Sie Folgendes:

- db_datareader
- db_datawriter

### Ports und Protokolle

Welche Ports für die ordnungsgemäße Verwendung des ODBC-Treibers benötigt werden, können Sie der Dokumentation des Datenbankanbieters entnehmen.

## Erstellen eines neuen Connectors

Erstellen Sie einen generischen SQL-Connector in **Synchronisierungsdienst** wählen **Management Agent** und **Erstellen**. Wählen Sie die **generischer SQL (Microsoft)** Connectors.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### Konnektivität

Der Connector verwendet eine ODBC-DSN-Datei für die Konnektivität. Erstellen Sie die DSN-Datei mit **ODBC-Datenquellen** finden Sie im Startmenü unter **Verwaltung**. Erstellen Sie im Verwaltungstool, eine **Datei-DSN** sodass sie den Connector bereitgestellt werden kann.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

Der Konnektivitätsbildschirm ist der erste, der beim Erstellen eines neuen generischen SQL-Connectors angezeigt wird. Beginnen Sie mit der Angabe folgender Informationen:

- DSN-Dateipfad
- Authentifizierung
    - Benutzername
    - Kennwort

Die Datenbank muss eine der folgenden Authentifizierungsmethoden unterstützen:

- **Windows-Authentifizierung**: die Datenbank zum Authentifizieren verwendet die Windows-Anmeldeinformationen den Benutzer überprüft. In diesem Fall wird das vom Synchronisierungsdienst verwendete Dienstkonto verwendet. Dieses Konto benötigt Berechtigungen für die Datenbank.
- **SQL-Authentifizierung**: verwenden die Datenbank zum Authentifizieren den Benutzer und Kennwort definiert die Konnektivität-Bildschirm für die Verbindung mit der Datenbank. Wenn Sie Benutzername und Kennwort in der DSN-Datei speichern, haben die im Konnektivitätsbildschirm angegebenen Anmeldeinformationen Vorrang.
- **Azure SQL-Datenbank-Authentifizierung**: Weitere Informationen finden Sie unter [Verbinden mit SQL-Datenbank durch Verwenden von Azure Active Directory-Authentifizierung](sql-database-aad-authentication.md)

**DN ist Anker**: Wenn Sie diese Option auswählen, der DN wird auch als verwendet das Ankerattribut. Die Option kann bei einer einfachen Implementierung verwendet werden, es gelten jedoch folgende Einschränkungen:

-   Der Connector unterstützt nur einen einzelnen Objekttyp. Daher müssen alle Verweisattribute auf den gleichen Objekttyp verweisen.

**: Export Objekttyp ersetzen**: während des Exports Wenn nur einige Attribute geändert wurden, das gesamte Objekt mit allen Attributen exportiert werden und ersetzt das vorhandene Objekt.

### Schema 1 (Objekttyperkennung)

Auf dieser Seite wird für den Connector die Suche nach den verschiedenen Objekttypen in der Datenbank konfiguriert.

Jeder Objekttyp wird als eine Partition und konfiguriert Weiter **Konfigurieren von Partitionen und Hierarchien**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Objekt-Typ Erkennungsmethode**: der Connector unterstützt diese Erkennungsmethoden des Objekt-Typ.

- **Fester Wert**: Sie stellen die Liste der Objekttypen mit einer durch Trennzeichen getrennte Liste bereit. Beispiel: Benutzer, Gruppe, Abteilung.  
![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **Tabelle/Sicht/gespeicherte Prozedur**: der Name der Tabelle, Sicht, gespeicherte Prozedur und dann auf den Namen der Spalte daraufhin wird die Liste von Objekttypen bereitstellen. Wenn Sie eine gespeicherte Prozedur verwenden Sie auch Parameter für die es im Format bieten **[Name]: [Richtung]: [Wert]**. Geben Sie die Parameter jeweils in einer separaten Zeile an. (Drücken Sie STRG+EINGABETASTE, um eine neue Zeile zu erhalten.)  
![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **SQL-Abfrage**: Diese Option ermöglichen eine SQL-Abfrage gibt eine einzelne Spalte mit Objekttypen, z. B. zurück, `SELECT [Column Name] FROM TABLENAME`. Die zurückgegebene Spalte muss vom Typ „String“ (varchar) sein.

### Schema 2 (Attributtyperkennung)

Auf dieser Seite wird die Erkennung der Attributnamen und -typen konfiguriert. Die Konfigurationsoptionen werden für jeden auf der vorherigen Seite ermittelten Objekttyp aufgeführt.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**-Attribut Erkennungsmethode Typ**: der Connector unterstützt diese Attribut Typ von Methoden mit jedem erkannten Objekt im Schema 1 Bildschirm.

- **Tabelle/Sicht/gespeicherte Prozedur**: Geben Sie den Namen der Tabelle, Sicht, gespeicherte Prozedur die verwendet werden sollte, um die Attributnamen zu suchen. Wenn Sie eine gespeicherte Prozedur verwenden Sie auch Parameter für die es im Format bieten **[Name]: [Richtung]: [Wert]**. Geben Sie die Parameter jeweils in einer separaten Zeile an. (Drücken Sie STRG+EINGABETASTE, um eine neue Zeile zu erhalten.) Wenn Sie Attributnamen in einem mehrwertigen Attribut ermitteln möchten, geben Sie eine kommagetrennte Tabellen- oder Sichtenliste an. Mehrwertige Szenarien, bei denen die übergeordnete und die untergeordnete Tabelle die gleichen Spaltennamen enthalten, werden nicht unterstützt.
- **SQL-Abfrage** diese Option ermöglichen eine SQL-Abfrage gibt z. B. eine einzelne Spalte mit Attributnamen zurück, `SELECT [Column Name] FROM TABLENAME`. Die zurückgegebene Spalte muss vom Typ „String“ (varchar) sein.

### Schema 3 (Definieren von Anker und DN)

Auf dieser Seite können Sie jeweils das Anker- und DN-Attribut für die erkannten Objekttypen konfigurieren. Sie können mehrere Attribute auswählen, um einen eindeutigen Anker zu erhalten.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- Mehrwertige und boolesche Attribute werden nicht aufgeführt.
- Dasselbe Attribut kann nicht für DN und Anker verwenden, es sei denn, **DN ist Anker** auf der Seite Konnektivität ausgewählt ist.
- Wenn **DN ist Anker** ausgewählt ist auf der Seite Konnektivität auf dieser Seite nur das DN-Attribut ist erforderlich. Dieses Attribut wird auch als Ankerattribut verwendet werden.
![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### Schema 4 (Definieren von Attributtyp, Verweis und Richtung)

Auf dieser Seite können Sie für die Attribute jeweils den Attributtyp (beispielsweise ganze Zahl, Verweis, Zeichenfolge, Binärwert oder boolescher Wert) und die Richtung konfigurieren. Alle Attribute aus der Seite **Schema 2** werden aufgeführt, auch mehrwertige Attribute.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **Datentyp**: verwendet, um diese bekannten durch das Synchronisierungsmodul den Attributtyp zuzuordnen. Standardmäßig wird der im SQL-Schema erkannte Typ verwendet, „Datum/Uhrzeit“ und „Verweis“ sind jedoch nicht so einfach erkennbar. Für die Sie angeben müssen **DateTime** oder **Verweis**.
- **Richtung**: Sie können die Richtung des Attributs auf Importieren, exportieren oder ImportExport festlegen. ImportExport ist die Standardeinstellung.
![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Hinweise:

- Falls ein Attributtyp nicht vom Connector erkannt werden kann, wird der Datentyp „Zeichenfolge“ verwendet.
- **Geschachtelte Tabellen** Tabellen eine Spalte betrachtet werden kann. Oracle speichert die Zeilen einer geschachtelten Tabelle in keiner bestimmten Reihenfolge. Beim Abrufen der geschachtelten Tabelle in eine PL/SQL-Variable werden die Zeilen jedoch mit fortlaufenden tiefgestellten Zeichen (beginnend mit 1) versehen. Dies ermöglicht einen arrayähnlichen Zugriff auf einzelne Zeilen.
- **VARRYS** werden in den Connector nicht unterstützt.

### Schema 5 (Definieren der Partition für Verweisattribute)

Auf dieser Seite wird für alle Referenzattribute konfiguriert, auf welche Partition (also auf welchen Objekttyp) das jeweilige Attribut verweist.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Wenn Sie verwenden **DN ist Anker** verweisen Sie aus müssen Sie denselben Objekttyp wie verwenden. Sie können nicht auf einen anderen Objekttyp verweisen.

### Globale Parameter

Auf der Seite für globale Parameter können Sie den Deltaimport, das Datums-/Uhrzeitformat sowie die Kennwortmethode konfigurieren.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

Der generische SQL-Connector unterstützt folgende Methoden für den Deltaimport:

- **Trigger**: finden Sie unter [Generieren von Delta-Sichten, die mithilfe von Triggern](https://technet.microsoft.com/library/cc708665.aspx).
- **Wasserzeichen**: Dies ist eine generische Methode und kann mit jeder Datenbank verwendet werden. Die Wasserzeichenabfrage wird auf der Grundlage des Datenbankanbieters vorab aufgefüllt. Jede verwendete Tabelle/Sicht muss über eine Wasserzeichenspalte verfügen. Damit müssen Einfüge- und Aktualisierungsvorgänge für Tabellen sowie für abhängige (mehrwertige oder untergeordnete) Tabellen nachverfolgt werden. Die Uhren zwischen Synchronisierungsdienst und Datenbankserver müssen synchronisiert werden. Andernfalls werden unter Umständen einige Einträge im Deltaimport ausgelassen.  
Einschränkung:
    - Die Wasserzeichenstrategie unterstützt keine gelöschten Objekte.
- **Snapshot** (funktioniert nur mit Microsoft SQL Server) [Delta-Sichten, die mithilfe von Momentaufnahmen generieren](https://technet.microsoft.com/library/cc720640.aspx)
- **Nachverfolgen von Änderungsdaten** (funktioniert nur mit Microsoft SQL Server) [zum Nachverfolgen von Änderungen](https://msdn.microsoft.com/library/bb933875.aspx)  
Einschränkungen:
    - Anker- und DN-Attribut müssen Teil des Primärschlüssels für das ausgewählte Objekt in der Tabelle sein.
    - Die SQL-Abfrage wird beim Importieren und Exportieren mit Änderungsnachverfolgung nicht unterstützt.

**Zusätzliche Parameter**: Geben Sie die Zeitzone des Datenbank-Servers, der angibt, wo sich der Datenbankserver befindet. Dieser Wert dient zur Unterstützung der verschiedenen Formate von Datums-/Uhrzeitattributen.

Der Connector speichert Datum und Datum/Uhrzeit immer im UTC-Format. Zur ordnungsgemäßen Konvertierung von Datums- und Uhrzeitangaben müssen die Zeitzone des Datenbankservers und das verwendete Format angegeben werden. Das Format muss im .NET-Format angegeben werden.

Beim Exportieren muss jedes Datums-/Uhrzeitattribut für den Connector im UTC-Format angegeben werden.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Kennwort-Konfiguration**: der Connector bietet Funktionen für die Synchronisierung von Kennwort und unterstützt und das Kennwort ändern.

Der Connector stellt zwei Methoden zur Unterstützung der Kennwortsynchronisierung bereit:

- **Gespeicherte Prozedur**: Diese Methode benötigt zwei gespeicherte Prozeduren zur festlegen und ändern das Kennwort. Geben Sie alle Parameter für das Hinzufügen und ändern Sie des Kennwort-Vorgangs in Kennwort SP festlegen und ändern das Kennwort SP bzw. gemäß folgenden Beispiel.
![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **Kennworterweiterung**: Diese Methode erfordert die Kennwort-Erweiterung DLL (Sie müssen dem Namen der DLL angeben, die implementiert [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) Schnittstelle). Kennwort Erweiterungsassembly muss in den Erweiterungsordner platziert werden, damit der Connector die DLL zur Laufzeit geladen werden kann.
![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Müssen Sie auch die Kennwort-Verwaltung zu ermöglichen, auf die **Konfigurieren Erweiterung** Seite.
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### Konfigurieren von Partitionen und Hierarchien

Wählen Sie auf der Seite mit den Partitionen und Hierarchien alle Objekttypen aus. Jeder Objekttyp befindet sich in einer eigenen Partition.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

Sie können auch auf definierten Werte überschreiben die **Konnektivität** oder **globaler Parameter** Seite.

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### Konfigurieren von Ankern

Diese Seite ist schreibgeschützt, da der Anker bereits definiert wurde. Dem ausgewählten Ankerattribut wird immer der Objekttyp angefügt, um sicherzustellen, dass er objekttypübergreifend eindeutig ist.

![anchors](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## Konfigurieren des Ausführungsschrittparameters

Diese Schritte werden für die connectorspezifischen Ausführungsprofile konfiguriert. Die Konfigurationen übernehmen das eigentliche Importieren und Exportieren von Daten.

### Vollständiger Import und Deltaimport

Der generische SQL-Connector unterstützt vollständige Importe und Deltaimporte mit folgenden Methoden:

- Tabelle
- Sicht
- Gespeicherte Prozedur
- SQL-Abfrage

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Tabelle/Sicht**

Um Attribute für ein Objekt mit mehreren Werten zu importieren, Sie müssen durch Trennzeichen getrennten Tabellen-/Sichtname in **Name des für mehrwertige Tabellenansichten** und entsprechenden Joinbedingungen in der **Join-Bedingung** mit der übergeordneten Tabelle.

Ein Beispiel: Sie möchten das Employee-Objekt und alle dazugehörigen mehrwertigen Attribute importieren. Es gibt zwei Tabellen: die Haupttabelle „Employee“ und die mehrwertige Tabelle „Department“.
Gehen Sie wie folgt vor:

- Typ **Mitarbeiter** in **Tabelle/Sicht/SP**.
- Abteilung in **Name des für mehrwertige Tabellenansichten**.
- Geben Sie die Join-Bedingung zwischen Mitarbeiter und Abteilung in **Join-Bedingung**, zum Beispiel:
`Employee.DEPTID=Department.DepartmentID`.
![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Gespeicherte Prozeduren**

![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- Bei großen Datenmengen empfiehlt sich die Implementierung der Paginierung für gespeicherte Prozeduren.
- Damit Ihre gespeicherte Prozedur die Paginierung unterstützt, müssen Sie den Start- und den Endindex angeben. Siehe: [effizient Paging durch große Datenmengen](https://msdn.microsoft.com/library/bb445504.aspx).
- @StartIndex und @EndIndex ersetzt werden zum Zeitpunkt der Ausführung mit entsprechenden Seitengrößenwert konfiguriert **Schritt konfigurieren** Seite. Ein Beispiel: Angenommen, der Connector ruft die erste Seite ab, und die Seitengröße ist auf 500 festgelegt. In diesem Fall wird „@StartIndex“ auf 1 und „@EndIndex“ auf 500 festgelegt. Dieser Wert nimmt zu, wenn der Connector weitere Seiten abruft, was die Änderung von „@StartIndex“ und „@EndIndex“ nach sich zieht.
- Geben Sie zum Ausführen einer parametrisierten gespeicherten Prozedur die Parameter im Format `[Name]:[Direction]:[Value]` an. Geben Sie die Parameter jeweils in eine separate Zeile ein. (Drücken Sie STRG+EINGABETASTE, um eine neue Zeile zu erhalten.)
- Der generische SQL-Connector unterstützt auch Importvorgänge aus einer verteilten Umgebung (etwa Verbindungsserver in Microsoft SQL Server). Falls Informationen aus einer Tabelle auf einem Verbindungsserver abgerufen werden sollen, muss die Tabelle im folgenden Format angegeben werden: `[ServerName].[Database].[Schema].[TableName]`
    - In verteilten Umgebungen unterstützt der Connector nur Microsoft-Verbindungsserver.
- Der generische SQL-Connector unterstützt nur Objekte mit ähnlicher Struktur (sowohl Aliasname als auch Datentyp) zwischen Ausführungsschrittinformationen und Schemaerkennung. Wenn sich bei dem ausgewählten Objekt das Schema und die für den Ausführungsschritt angegebenen Informationen unterscheiden, unterstützt der SQL-Connector das entsprechende Szenario nicht.

**SQL-Abfrage**

![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- Abfragen mit mehreren Resultsets werden nicht unterstützt.
- Die SQL-Abfrage unterstützt die Paginierung und stellt Start- und Endindex als Variable zur Verfügung.

### Deltaimport

![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Die Deltaimportkonfiguration erfordert einige weitere Konfigurationsschritte sowie eine andere unterstützte Methode (wie etwa beim vollständigen Import).

- Wenn Benutzer entscheidet, den Trigger oder eine Momentaufnahme Ansatz zum Nachverfolgen von deltaänderungen, erhalten Benutzer Verlaufstabelle oder eine Momentaufnahme der Datenbank in **Verlaufstabelle oder Snapshot Datenbankname** Feld.
- Darüber hinaus muss der Benutzer Verknüpfungsbedingungen für die Verlaufstabelle und die übergeordnete Tabelle angeben.
Beispiel: `Employee.ID=History.EmployeeID`
- Um die Transaktion der übergeordneten Tabelle anhand der Verlaufstabelle nachverfolgen zu können, muss der Benutzer den Namen der Spalte mit den Vorgangsinformationen (Hinzufügung/Aktualisierung/Löschung) angeben.
- Benutzer Wasserzeichen beim Verfolgen der deltaänderungen auswählt, muss die Benutzer angeben den Namen der Spalte mit den Vorgangsinformationen in **Wasser Mark Spaltennamen**. So kann der Connector diese Spalte dann bei Deltaimporten berücksichtigen.
- Die **Ändern Typattribut** Spalte für den Änderungstyp erforderlich ist. Diese Spalte ordnet eine Änderung aus der primären oder mehrwertigen Tabelle einem Änderungstyp in der Deltasicht zu. Diese Spalte kann für Änderungen auf Attributebene den Änderungstyp „Attribut ändern“ und für Änderungen auf Objektebene den Änderungstyp „Hinzufügen“, „Ändern“ oder „Löschen“ enthalten. Sollte es sich um einen anderen Wert als den Standardwert „Hinzufügen“, „Ändern“ oder „Löschen“ handeln, kann dieser mithilfe dieser Option definiert werden.

### Export

![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Der generische SQL-Connector unterstützt vier Methoden für den Export:

- Tabelle
- Sicht
- Gespeicherte Prozedur
- SQL-Abfrage

**Tabelle/Sicht**

Bei Verwendung der Option „Tabelle“ oder „Sicht“ generiert der Connector die entsprechenden Abfragen und führt den Export aus.

**Gespeicherte Prozeduren**

![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Bei Verwendung der Option „Gespeicherte Prozedur“ werden drei verschiedene gespeicherte Prozeduren für verschiedene Einfüge-/Aktualisierungs-/Löschvorgänge benötigt:

- **Hinzufügen von SP-Namen**, diese SP ausgeführt werden, wenn alle Objekt Connector für das Einfügen in der jeweiligen Tabelle stammt.
- **SP-Name aktualisieren**, This SP ausgeführt werden, wenn alle Objekt Connector für die Aktualisierung in der entsprechenden Tabelle stammt.
- **SP-Name löschen**, This SP ausgeführt werden, wenn der Connector in der jeweiligen Tabelle zum Löschen jedes Objekt ist.
- Attribut aus dem Schema, das als Parameterwert für die gespeicherte Prozedur verwendet wird. Beispiel: „EmployeeName: INPUT: @EmployeeName“ („EmployeeName“ wird im Connectorschema ausgewählt, und der Connector ersetzt den entsprechenden Wert beim Export.)
- Geben Sie beim Ausführen parametrisierter gespeicherter Prozeduren alle Parameter im Format `[Name]:[Direction]:[Value]` an. Geben Sie die Parameter jeweils in eine separate Zeile ein. (Drücken Sie STRG+EINGABETASTE, um eine neue Zeile zu erhalten.)

**SQL-Abfrage**

![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Bei Verwendung der Option „SQL-Abfrage“ werden drei verschiedene Abfragen für verschiedene Einfüge-/Aktualisierungs-/Löschvorgänge benötigt:

- **Legen Sie die Abfrage**: Diese Abfrage auszuführen, wenn ein Objekt mit Connector für das Einfügen in der jeweiligen Tabelle stammen.
- **Aktualisieren der Abfrage**: Diese Abfrage auszuführen, wenn ein Objekt mit Connector für die Aktualisierung in der entsprechenden Tabelle stammen.
- **Löschabfrage**: Diese Abfrage ausgeführt werden soll, wenn der Connector in der jeweiligen Tabelle zum Löschen jedes Objekt ist.
- Attribut aus dem Schema, das als Parameterwert für die Abfrage verwendet wird. Beispiel: `Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## Problembehandlung

-   Informationen zum Aktivieren der Protokollierung, um den Connector zu beheben, finden Sie unter der [How to Enable ETW-Tracing für Connectors](http://go.microsoft.com/fwlink/?LinkId=335731).


