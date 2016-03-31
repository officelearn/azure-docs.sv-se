
# Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio 

Sie können SQL Server Management Studio (SSMS) verwenden, um logische Server und Datenbanken der Azure SQL-Datenbank zu verwalten. Dieses Thema enthält Anleitungen für häufige Aufgaben mit SSMS. Sie sollten bereits einen logischen Server und eine logische Datenbank in der Azure SQL-Datenbank erstellt haben, bevor Sie beginnen. Um zu beginnen, lesen [Erstellen Ihrer ersten Azure SQL-Datenbank](sql-database-get-started.md) und kehren dann zurück.

Es wird empfohlen, dass Sie die neueste Version von SSMS verwenden, wenn Sie mit der Azure SQL-Datenbank arbeiten. Besuchen Sie [Herunterladen von SQL Server Management Studio](https://msdn.microsoft.com/en-us/library/mt238290.aspx) abrufen. 


## Herstellen einer Verbindung mit einem logischen SQL-Datenbankserver

Um eine Verbindung mit der SQL-Datenbank herstellen zu können, müssen Sie den Servernamen auf Azure kennen. Eventuell müssen Sie sich beim Portal anmelden, um diese Information zu erhalten.

1.  Melden Sie sich bei der [Azure-Verwaltungsportal](http://manage.windowsazure.com).

2.  Klicken Sie im linken Bereich auf **SQL-Datenbanken**.

3.  Klicken Sie auf der Seite SQL-Datenbanken auf **Server** am oberen Rand der Seite, um eine Liste aller Server, die Ihrem Abonnement zugeordnet. Suchen Sie den Namen des gewünschten Servers und kopieren Sie ihn in die Zwischenablage.

    Als Nächstes konfigurieren Sie die SQL-Datenbank-Firewall,
sodass Verbindungen von Ihrem lokalen Computer zugelassen werden. Geben Sie hierzu die IP-Adresse Ihres lokalen Computers in die Ausnahmeliste der Firewall ein.

1.  Klicken Sie auf der Startseite der SQL-Datenbanken auf **Server** und klicken Sie dann auf den Server, zu dem Sie eine Verbindung herstellen möchten.

2.  Klicken Sie auf **konfigurieren** am oberen Rand der Seite.

3.  Kopieren Sie die IP-Adresse in das Feld CURRENT CLIENT IP ADDRESS.

4.  Klicken Sie auf der Seite konfigurieren **zugelassene IP-Adressen** enthält die drei Felder in dem Sie einen Regelnamen und einen Bereich von IP-Adressen und die Start-und Endwerte angeben können. Als Regelnamen können Sie den Namen Ihres Computers wählen. Kopieren Sie für Bereichsanfang und -ende die IP-Adresse Ihres Computers in beide Felder und aktivieren Sie das Kontrollkästchen, das anschließend angezeigt wird.

    Der Regelname muss eindeutig sein. Wenn es sich um Ihren Entwicklungscomputer handelt, können Sie seine IP-Adresse in beide Felder eingeben und als Anfangs- und Endwert des IP-Adressbereichs nutzen. Andernfalls kann es erforderlich sein, einen größeren IP-Adressbereich zu wählen, um Verbindungen von weiteren Computern in Ihrer Organisation zu ermöglichen.
 
5. Klicken Sie auf **Speichern** am unteren Rand der Seite.

    **Hinweis:** Es kann von eine Verzögerung von fünf Minuten für Änderungen an
    der Firewall-Einstellungen wirksam werden.

    Sie können nun über Management Studio eine Verbindung zur SQL-Datenbank aufbauen.

1.  Klicken Sie auf der Taskleiste auf **Start**, zeigen Sie auf **Programme**, zeigen Sie auf
    **Microsoft SQL Server 2014**, und klicken Sie dann auf **SQL Server
    Management Studio**.

2.  In **mit Server verbinden**, geben Sie den vollqualifizierten
    Servernamen als *ServerName*..Database.Windows.NET ein. In Azure ist der Servername eine automatisch generierte Zeichenfolge aus alphanumerischen Zeichen.

3.  Wählen Sie **SQL Server-Authentifizierung**.

4.  In der **Anmeldung** Geben Sie die SQL Server-Administrator-Anmeldung, die Sie
    im Portal angegeben, bei der Erstellung Ihres Servers.

5.  In der **Kennwort** Geben Sie das Kennwort, das im angegebenen
    Das Portal bei der Erstellung Ihres Servers.

8.  Klicken Sie auf **Verbinden** zum Herstellen der Verbindung.

SQL Server 2014 SSMS mit den neuesten Updates bietet erweiterte Unterstützung für Aufgaben wie das
Erstellen und Ändern von Azure SQL-Datenbanken. Darüber hinaus können Sie auch
Aufgaben unter Verwendung von Transact-SQL-Anweisungen erledigen. Die nachfolgenden Schritte
enthalten Beispiele für diese Anweisungen. Weitere Informationen zur Verwendung von
Transact-SQL mit SQL-Datenbanken und Einzelheiten dazu, welche Befehle
unterstützt, finden Sie unter [Transact-SQL-Referenz (SQL-Datenbank)](http://msdn.microsoft.com/library/bb510741.aspx).

## Erstellen und Verwalten von Azure SQL-Datenbanken

Während der Verbindung mit dem **master** Datenbank, Sie können neu erstellen
Datenbanken auf dem Server erstellen oder vorhandene Datenbanken ändern oder löschen. In den nachfolgenden Schritten
wird beschrieben, wie Sie einige allgemeine Datenbankverwaltungsaufgaben
in Management Studio ausführen. Um diese Aufgaben auszuführen, stellen Sie eine Verbindung zur
**Master** Datenbank mit der hauptanmeldung auf Serverebene, die Sie
die Sie bei der Einrichtung des Servers erstellt haben.

Um ein Abfragefenster in Management Studio zu öffnen, öffnen Sie den Ordner Datenbanken, erweitern Sie die **Systemdatenbanken** Ordner mit der rechten Maustaste auf **master**, und klicken Sie dann auf **neue Abfrage**.

-   Verwenden der **CREATE DATABASE** Anweisung, um eine neue Datenbank erstellen. Weitere
    Weitere Informationen finden Sie unter [CREATE DATABASE (SQL-Datenbank)](https://msdn.microsoft.com/library/dn268335.aspx). Die folgende Anweisung erstellt eine neue Datenbank namens **MyTestDB** und gibt an, dass es eine Standard-S0-Edition-Datenbank mit einer maximalen Standardgröße von 250 GB handelt.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Klicken Sie auf **Execute** zum Ausführen der Abfrage.

-   Verwenden der **ALTER DATABASE** Anweisung, um eine vorhandene Datenbank ändern
    wenn Sie z. B. den Namen oder die Edition
    der Datenbank ändern möchten. Weitere Informationen finden Sie unter [ALTER DATABASE (SQL-Datenbank)](https://msdn.microsoft.com/library/ms174269.aspx). Die
    folgende Anweisung ändert die maximale Größe der im vorherigen Schritt
    um die Edition in "Standard S1" zu ändern.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   Verwendung **DROP DATABASE** Anweisung, um eine bestehende Datenbank löschen.
    Weitere Informationen finden Sie unter [DROP DATABASE (SQL-Datenbank)](https://msdn.microsoft.com/library/ms178613.aspx). Die folgende Anweisung löscht die **MyTestDB** Datenbank jedoch nicht es jetzt, da Sie verwenden, werden im nächsten Schritt Anmeldungen erstellen.

        DROP DATABASE myTestBase;

-   Die master-Datenbank hat die **sys.databases** anzeigen, die Sie verwenden können
    zum Anzeigen von Details zu allen Datenbanken verwenden können. Um alle vorhandenen Datenbanken anzuzeigen,
    führen Sie die folgende Anweisung aus:

        SELECT * FROM sys.databases;

-   In SQL-Datenbank die **verwenden** Anweisung ist nicht zum Wechseln
    zwischen Datenbanken verwendet werden. Stattdessen müssen Sie eine direkte Verbindung
    mit der Datenbank herstellen.

>[AZURE.NOTE] Viele der Transact-SQL-Anweisungen, die eine Datenbank erstellen oder modifizieren, müssen in ihrem eigenen Batch ausgeführt werden und nicht mit anderen Transact-SQL-Anweisungen gruppiert werden. Weitere Informationen finden Sie in den anweisungsspezifischen Artikeln, die über die oben aufgeführten Links verfügbar sind.

## Erstellen und Verwalten von Anmeldungen

Die **master** Datenbank verfolgt des Anmeldungen und welche Anmeldungen
zum Erstellen von Datenbanken oder anderer Anmeldungen berechtigt sind. Verwalten Sie Anmeldungen, indem
Herstellen einer Verbindung mit der **master** Datenbank mit dem Prinzipal auf Serverebene
Serverebene herstellen, die Sie beim Einrichten des Servers erstellt haben. Sie können mit den Anweisungen
**CREATE LOGIN**, **ALTER LOGIN**, oder **DROP LOGIN** -Anweisungen
Abfragen für die master-Datenbank ausführen, mit denen Anmeldungen
serverweit verwaltet werden. Weitere Informationen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in SQL-Datenbank](http://msdn.microsoft.com/library/azure/ee336235.aspx). 


-   Verwenden der **CREATE LOGIN** -Anweisung zum Erstellen einer neuen Server-Ebene
    zu erstellen. Weitere Informationen finden Sie unter [CREATE LOGIN (SQL-Datenbank)](https://msdn.microsoft.com/library/ms189751.aspx). Die folgende Anweisung erstellt eine neue Anmeldung
    namens **login1**. Ersetzen Sie **Kennwort1** mit dem Kennwort Ihrer
    Wahl.

        CREATE LOGIN login1 WITH password='password1';

-   Verwenden der **CREATE USER** Anweisung auf Datenbankebene gewähren
    erforderlich. Alle Anmeldungen müssen erstellt werden, der **master** -Datenbank
    damit mit einem Anmeldenamen eine Verbindung mit einer anderen Datenbank hergestellt werden kann, müssen
    müssen Berechtigungen auf Datenbankebene mit der **Benutzer erstellen**
    Berechtigungen auf Datenbankebene für die betreffende Datenbank erteilen. Weitere Informationen finden Sie unter [CREATE USER (SQL-Datenbank)](https://msdn.microsoft.com/library/ms173463.aspx). 

-   Um login1
    Berechtigungen für eine Datenbank namens **MyTestDB**, führen Sie die folgenden
    folgt vor:

 1.  So aktualisieren Sie den Objekt-Explorer zum Anzeigen der **MyTestDB** Datenbank, die Sie gerade erstellt haben, mit der rechten Maustaste im Objekt-Explorer des Servernamens, und klicken Sie dann auf **Aktualisieren**.  

     Wenn Sie die Verbindung geschlossen haben, können Sie durch Auswahl wiederherstellen **Objekt-Explorer verbinden** im Menü Datei.

 2. Mit der rechten Maustaste **MyTestDB** Datenbank, und wählen Sie **neue Abfrage**.

    3.  Führen Sie die folgende Anweisung für die Datenbank "myTestDB" aus,
        Erstellen Sie einen Datenbankbenutzer namens **login1User** entspricht
        die Anmeldung auf Serverebene **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Verwenden der **Sp\_addrolemember** gespeicherte Prozedur für den Benutzer
    die erforderlichen Berechtigungen für die Datenbank zu erteilen. Weitere
    Weitere Informationen finden Sie unter [Sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). Die folgende Anweisung erteilt **login1User**
    schreibgeschützte Berechtigungen für die Datenbank, indem **login1User** an
    die **Db\_datareader** Rolle.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Verwenden der **ALTER LOGIN** Anweisung für einen vorhandenen Benutzernamen verwenden, ändern
    wenn Sie das Kennwort für die Anmeldung ändern möchten. Weitere
    Weitere Informationen finden Sie unter [ALTER LOGIN (SQL-Datenbank)](https://msdn.microsoft.com/library/ms189828.aspx). Die **ALTER LOGIN** Anweisung ausgeführt werden soll, für die **master** Datenbank. Wechseln Sie wieder zurück zu dem Abfragefenster, das mit dieser Datenbank verbunden ist. 

    Die folgende Anweisung ändert die **login1** Anmeldung das Kennwort zurückzusetzen.
    Ersetzen Sie **NewPassword** mit dem Kennwort Ihrer Wahl, und
    **OldPassword** durch das aktuelle Kennwort für die Anmeldung.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Verwendung **DROP LOGIN** Anweisung, um eine bestehende Anmeldung löschen.
    Wenn Sie eine Anmeldung auf Serverebene löschen, werden auch alle zugehörigen
    Datenbankbenutzerkonten gelöscht. Weitere Informationen
    finden Sie unter [DROP DATABASE (SQL-Datenbank)](https://msdn.microsoft.com/library/ms178613.aspx). Die **Anweisung DROP LOGIN**
    Anweisung ausgeführt werden soll, für die **master** Datenbank. Die
    folgende Anweisung löscht die **login1** Anmeldung.

        DROP LOGIN login1;

-   Die master-Datenbank hat die **sichtsys** anzeigen, die Sie
    zum Anzeigen der Anmeldungen nutzen können. Führen Sie zum Anzeigen aller vorhandenen Anmeldungen die
    folgende Anweisung aus:

        SELECT * FROM sys.sql_logins;

## Überwachen von SQL-Datenbank mit Dynamic Management Views</h2>

SQL-Datenbank unterstützt einige dynamische Verwaltungssichten, mit
denen Sie einzelne Datenbanken überwachen können. Nachfolgend finden Sie einige Beispiele für
die Art von Überwachungsdaten, die Sie mit diesen Sichten abrufen können. Weitere
Details und weitere Anwendungsbeispiele, finden Sie unter [Überwachen der SQL-Datenbank mit Dynamic Management Views](https://msdn.microsoft.com/library/azure/ff394114.aspx).

-   Abfragen einer dynamischen Verwaltungsansicht erfordert **VIEW DATABASE STATE**
    erforderlich. Gewähren Sie der **VIEW DATABASE STATE** Berechtigung für ein
    zu gewähren, stellen Sie eine Verbindung mit der Datenbank her, die Sie
    mit der Prinzipalanmeldung auf Serverebene verwalten möchten, und führen Sie die
    Anweisung für die Datenbank aus:

        GRANT VIEW DATABASE STATE TO login1User;

-   Berechnen Sie die Größe mithilfe der **sys.dm\_db\_partition\_stats**
    gerendert. Die **sys.dm\_db\_partition\_stats** Ansicht Seite zurück und
    Zeilenanzahl jeder Partition der Datenbank zurück, die Sie
    zum Berechnen der Datenbankgröße verwenden können. Die folgende Abfrage gibt
    die Größe der Datenbank in MB zurück:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Verwenden der **sys.dm\_exec\_connections** und **sys.dm\_exec\_sessions**
    dienen zum Abrufen von Informationen über aktuelle Benutzerverbindungen und
    interne im Zusammenhang mit der Datenbank stehende Aufgaben. Die folgende Abfrage
    liefert Informationen über die aktuelle Verbindung:

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   Verwenden der **sys.dm\_exec\_query\_stats** Ansicht zum Abrufen aggregierter
    Leistungsstatistiken für zwischengespeicherte Abfragepläne. Die folgende Abfrage
    gibt Informationen über die „Top-Fünf“-Abfragen gemessen an durchschnittlicher CPU-Zeit
    zurück.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;


