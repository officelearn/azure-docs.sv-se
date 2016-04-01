
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### Vergleichen der Verbindungszeichenfolge


In der folgenden Tabelle werden die Verbindungszeichenfolgen verglichen, die das C#-Programm zum Herstellen einer Verbindung zum lokalen SQL Server im Vergleich zu Azure SQL-Datenbank in der Cloud benötigt. Die Unterschiede werden in Fettschrift angezeigt.


| Verbindungszeichenfolge für<br/>Azure SQL-Datenbank | Verbindungszeichenfolge für<br/>Microsoft SQL Server |
| :-- | :-- |
| Server =**Tcp:**{Your_serverName_here}**. database.windows.net,1433**;<br/>Benutzer-ID = {Your_loginName_here}**@{Your_serverName_here}**;<br/>Kennwort={Your_password_here};<br/>**Datenbank = {Your_databaseName_here};**<br/>**Connection Timeout = 30**;<br/>**Verschlüsseln = True**;<br/>**TrustServerCertificate = False**; | Server = {Your_serverName_here};<br/>Benutzer-ID = {Your_loginName_here};<br/>Kennwort={Your_password_here}; |


Die **Datenbank =** ist optional für SQL Server, jedoch ist für die SQL-Datenbank erforderlich.


[Eigenschaften von .NET ADO SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) -alle Parameter im Detail erläutert.


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


