### <a name="prerequisites"></a>Krav
* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [Azure SQL Database](../articles/sql-database/sql-database-get-started.md) dess anslutningsinformation, inklusive servernamnet, databasnamnet och användarnamn/lösenord. Den här informationen ingår i anslutningssträngen för SQL-databasen:
  
    Server = tcp:*yoursqlservername*. database.windows.net,1433;Initial katalog =*yourqldbname*; Spara säkerhetsinformation = False; Användar-ID = {your_username}; Lösenord = {your_password}; MultipleActiveResultSets = False; Kryptera = True; TrustServerCertificate = False; Timeout för anslutningen = 30.
  
    Läs mer om [Azure SQL-databaser](https://azure.microsoft.com/services/sql-database).

> [!NOTE]
> När du skapar en Azure SQL Database, kan du också skapa exempeldatabasen som ingår i SQL. 
> 
> 

Innan du använder Azure SQL Database i en logikapp, ansluta till SQL-databasen. Du kan göra detta enkelt i din logikapp på Azure-portalen.  

Anslut till din Azure SQL-databas med följande steg:  

1. Skapa en logikapp. Lägg till en utlösare i Logic Apps-designer och sedan lägga till en åtgärd. Välj **visa Microsoft hanterade API: er** i nedrullningsbara listan, och ange sedan ”sql” i sökrutan. Välj något av åtgärderna som:  
   
    ![Steg i att skapa SQL Azure-anslutning](./media/connectors-create-api-sqlazure/sql-actions.png)
2. Om du inte tidigare har skapat alla anslutningar till SQL-databas, tillfrågas om anslutningsinformationen:  
   
    ![Steg i att skapa SQL Azure-anslutning](./media/connectors-create-api-sqlazure/connection-details.png) 
3. Ange information om SQL-databas. Egenskaper med en asterisk krävs.
   
   | Egenskap | Information |
   | --- | --- |
   | Anslut via Gateway |Lämna det här alternativet är avmarkerat. Det här används vid anslutning till en lokal SQL Server. |
   | Anslutningsnamn * |Ange ett namn för anslutningen. |
   | SQL Server-namnet * |Ange namnet på servern; vilket är något som liknar *servername.database.windows.net*. Namnet på servern visas i egenskaperna för SQL-databas på Azure-portalen och visas också i anslutningssträngen. |
   | Databasnamn för SQL * |Ange det namn du gav din SQL-databas. Detta visas i Egenskaper för SQL-databas i anslutningssträngen: Initial Catalog =*yoursqldbname*. |
   | Användarnamn * |Ange användarnamnet som du skapade när SQL-databasen har skapats. Detta visas i egenskaperna för SQL-databas på Azure-portalen. |
   | Lösenord * |Ange lösenordet som du skapade när SQL-databasen har skapats. |
   
    Dessa autentiseringsuppgifter används för att verifiera din logikapp för att ansluta och komma åt SQL-data. När du är klar, likna din anslutningsinformation följande:  
   
    ![Steg i att skapa SQL Azure-anslutning](./media/connectors-create-api-sqlazure/sample-connection.png) 
4. Välj **Skapa**. 
5. Observera att anslutningen har skapats. Nu kan fortsätta med andra steg i din logikapp: 
   
    ![Steg i att skapa SQL Azure-anslutning](./media/connectors-create-api-sqlazure/table.png)

