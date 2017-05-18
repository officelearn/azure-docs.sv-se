1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt fönster.
2. Klicka på **Nytt** på menyn till vänster. Klicka på **Databaser** och sedan på **Azure Cosmos DB**.
   
   ![Skärmbild av Azure Portal med fokus på Fler tjänster och Azure Cosmos DB](./media/cosmosdb-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. På bladet **Nytt konto** anger du önskad konfiguration för Azure Cosmos DB-kontot. 

    Med Azure Cosmos DB kan du välja någon av fyra programmeringsmodeller: Gremlin (graf), MongoDB, SQL (DocumentDB) och Tabell (nyckelvärde).  
       
    I den här snabbstarten kommer vi att programmera mot ett Graph API så du väljer **Gremlin (graf)** när du fyller i formuläret. Men om du har dokumentdata från en katalogapp, nyckelvärdedata (tabell) eller data som har migrerats från en MongoDB-app ska du tänka på att Azure Cosmos DB kan tillhandahålla en mycket tillgänglig, globalt distribuerad databastjänstplattform för alla dina verksamhetskritiska program.

    Fyll i bladet Nytt konto med informationen i skärmbilden som vägledning. Du ska välja unika värden när du konfigurerar ditt konto, så dina värden kommer inte att matcha skärmbilden exakt. 
 
    ![Skärmbild av bladet Ny Azure Cosmos DB](./media/cosmosdb-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    ID|*Unikt värde*|Ett unikt namn du väljer för att identifiera Azure Cosmos DB-kontot. *documents.azure.com* läggs till det ID du anger för att skapa din URI, så använd ett unikt men identifierbart ID. Id:n får bara innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken.
    API|Gremlin (graf)|Vi kommer att programmera mot [Graph API](../articles/cosmos-db/graph-introduction.md) längre ned i den här artikeln.|
    Prenumeration|*Din prenumeration*|Den Azure-prenumeration du vill använda för Azure Cosmos DB-kontot. 
    Resursgrupp|*Samma värde som ID*|Namnet på den nya resursgruppen för kontot. För enkelhetens skull kan du använda samma namn som för ditt ID. 
    Plats|*Regionen som ligger närmast dina användare*|Den geografiska plats som ska vara värd för ditt Azure Cosmos DB-konto. Välj den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.
    App Service-plan för Graph API|Skapa ny – Standard S1|Välj beräkningsresurs för att köra Gremlin Graph-frågor.

4. Skapa kontot genom att klicka på **Skapa**.
5. Klicka på **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.

    ![Meddelande om att distributionen har påbörjats](./media/cosmosdb-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  När distributionen är färdig öppnar du det nya kontot från panelen Alla resurser. 

    ![DocumentDB-konto på panelen Alla resurser](./media/cosmosdb-create-dbaccount-graph/azure-documentdb-all-resources.png)