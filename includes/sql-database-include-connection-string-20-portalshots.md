
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### Abrufen der Verbindungszeichenfolge aus dem Azure-Portal


Verwenden der [Azure Preview-Portal](http://portal.azure.com/) zum Abrufen der Verbindungszeichenfolge für das Clientprogramm für die Interaktion mit Azure SQL-Datenbank erforderlich sind: 


1. Klicken Sie auf **Durchsuchen** > **SQL-Datenbanken**.

2. Geben Sie den Namen der Datenbank in das Filtertextfeld in der Nähe der oberen linken Ecke des der **SQL-Datenbanken** Blatt.

3. Klicken Sie auf die Zeile für die Datenbank.

4. Nachdem das Blatt für Ihre Datenbank angezeigt wird, können der Einfachheit halber visual Sie klicken Sie auf die Steuerelemente standard minimieren, um Blätter zu reduzieren, die Sie durchsuchen und Filtern der Datenbank verwendet. 
 
    ![Filtern zum Isolieren der Datenbank][10-FilterDatabase]

5. Klicken Sie auf dem Blatt für Ihre Datenbank auf **Datenbankverbindungszeichenfolgen anzeigen**.

6. Wenn Sie beabsichtigen, die ADO.NET Verbindungsbibliothek verwenden, kopieren Sie die Zeichenfolge, die mit der Bezeichnung **ADO**. 
 
    ![Kopieren der ADO-Verbindungszeichenfolge für die Datenbank][20-CopyAdoConnectionString]
 
7. Fügen Sie in dem ein oder anderen Format die Informationen der Verbindungszeichenfolge in den Clientcode für die Anwendung ein.



Weitere Informationen finden Sie unter:<br/>[Verbindungszeichenfolgen und Konfigurationsdateien](http://msdn.microsoft.com/library/ms254494.aspx).



<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


