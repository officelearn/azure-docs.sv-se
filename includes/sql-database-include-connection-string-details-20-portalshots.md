<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### Abrufen der Verbindungszeichenfolge aus dem Azure-Portal


Verwenden der [Azure Preview-Portal](http://portal.azure.com/) zum Abrufen der Verbindungszeichenfolge für das Clientprogramm für die Interaktion mit Azure SQL-Datenbank erforderlich sind:


1. Klicken Sie auf **Durchsuchen** > **SQL-Datenbanken**.

    ![SQL auswählen][1-select-sql]

2. Geben Sie den Namen der Datenbank in das Filtertextfeld in der Nähe der oberen linken Ecke des der **SQL-Datenbanken** Blatt.

    ![Datenbank auswählen][2-select-database]]

3. Klicken Sie auf die Zeile für die Datenbank.

4. Nachdem das Blatt für Ihre Datenbank angezeigt wird, können der Einfachheit halber visual Sie klicken Sie auf die Steuerelemente standard minimieren, um Blätter zu reduzieren, die Sie durchsuchen und Filtern der Datenbank verwendet.

5. Notieren Sie sich die **SQL-Datenbank** Name und die **Servername**.  Der Benutzername wird yourusername@yourserver sein.

    ![Verbindungsdetails abrufen][3-get-connection-details]

7.  Fügen Sie die Verbindungsdetails in den Code der Clientanwendung ein.  Ersetzen Sie {your_password_here} durch Ihr tatsächliches Kennwort.


<!--
Could not find a good link for PHP

For more information, see:<br/>[Connection Strings and Configuration Files](https://msdn.microsoft.com/library/ms378428.aspx).
-->


<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png

[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-details]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-details.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


