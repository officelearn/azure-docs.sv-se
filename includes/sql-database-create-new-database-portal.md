
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## Skapa en ny Azure SQL-databas

Använd följande steg i Azure-portalen om du vill skapa en ny Azure SQL-databas på en ny eller befintlig logisk Azure SQL Database-server.

1. Om du inte är ansluten ansluter du till [Azure-portalen](http://portal.azure.com).
2. Klicka på **Ny**, skriv **SQL Database** och klicka sedan på **SQL Database (ny databas)**

     ![ny databas](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)

3. Klicka på SQL Database (ny databas).

     ![ny databas](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)
   
4. Klicka på **Skapa** för att skapa en ny databas i SQL Database-tjänsten.

     ![ny databas](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)

5. Ange värden för följande serveregenskaper:

 - Databasnamn
 - Prenumeration (bara om du har flera prenumerationer)
 - Resursgrupp (om du precis kommit igång kan du använda resursgruppen för den logiska servern)
 - Välj källa (du kan välja en tom databas, exempeldata eller säkerhetskopia av databas i Azure – för att migrera en lokal SQL Server-databas eller läsa in data med hjälp av BCP, se länkarna i slutet av den här artikeln)
 - Server (en ny eller befintlig logisk server)
 - Administratörslösenord för servern
 - Lösenord
 - Prisnivå (om du precis kommit igång, kan du använda standardvärdet S0)
 - Sortering (bara om du väljer en tom databas)

        ![new database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)

6.  Klicka på **Skapa**. I meddelandefältet ser du att distributionen har startats.

     ![ny databas](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)

7. Vänta tills distributionen har slutförts innan du fortsätter till nästa steg.

     ![ny databas](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)



<!--HONumber=sep16_HO1-->


