
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Hämta anslutningssträngen från Azure-portalen
Använd den [Azure-portalen](https://portal.azure.com/) att hämta anslutningssträngen som krävs för att klienten ska interagera med Azure SQL Database. 

1. Välj **alla tjänster** > **SQL-databaser**.

2. Ange namnet på databasen i textrutan filter nära det övre vänstra hörnet av den **SQL-databaser** bladet.

3. Välj raden för databasen.

4. När bladet visas för din databas för visual bekvämlighet väljer den **minimera** knappar för att komprimera blad som du använde för sökning och filtrering av databasen. 
   
5. På bladet för din databas väljer **visa databasanslutningssträngar**.

6. Om du tänker använda anslutningsbibliotek ADO.NET kopiera strängen med etiketten **ADO**. 
   
    ![Kopiera ADO anslutningssträngen för databasen][20-CopyAdoConnectionString]
7. Klistra in informationen i anslutningssträngen i din programkod för klienten i ett format eller någon annan.

Mer information finns i [anslutningssträngar och konfigurationsfiler](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
