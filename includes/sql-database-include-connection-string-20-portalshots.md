
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Hämta anslutningssträngen från Azure-portalen
Använd den [Azure-portalen](https://portal.azure.com/) att hämta anslutningssträngen som behövs för att klienten ska interagera med Azure SQL Database: 

1. Klicka på **Bläddra** > **SQL-databaser**.
2. Ange namnet på databasen i textrutan filter nästan längst upp till vänster i den **SQL-databaser** bladet.
3. Klicka på raden för databasen.
4. När det visas i bladet för din databas, visual i informationssyfte du kan klicka på standard minimera kontroller om du vill komprimera blad som du använde för sökning och filtrering av databasen. 
   
    ![Filter för att isolera din databas][10-FilterDatabase]
5. Klicka på bladet för din databas **visa databasanslutningssträngar**.
6. Om du tänker använda anslutningsbibliotek ADO.NET kopiera strängen med etiketten **ADO**. 
   
    ![Kopiera ADO anslutningssträngen för databasen][20-CopyAdoConnectionString]
7. Klistra in informationen i anslutningssträngen i din programkod för klienten i ett format eller någon annan.

Mer information finns i:<br/>[Anslutningssträngar och konfigurationsfiler](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
