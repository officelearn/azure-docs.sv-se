1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt fönster.
2. Klicka på **Nytt** på menyn till vänster. Klicka på **Databaser** och sedan på **Skapa** under **Azure Cosmos DB**.
   
   ![Azure Portal databasfönstret](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. På bladet **Nytt konto** anger du önskad konfiguration för det här Azure Cosmos DB-kontot. 

    Med Azure Cosmos DB kan du välja någon av fyra programmeringsmodeller: Gremlin (graf), MongoDB, SQL (DocumentDB) och Tabell (nyckelvärde), som var och en kräver ett separat konto.
       
    I den här snabbstarten kommer vi att programmera mot ett Graph API så du väljer **Gremlin (graf)** när du fyller i formuläret. Om du har dokumentdata från en katalogapp, nyckelvärdedata (tabell) eller data som har migrerats från en MongoDB-app ska du tänka på att Azure Cosmos DB kan tillhandahålla en mycket tillgänglig, globalt distribuerad databastjänstplattform för alla dina verksamhetskritiska program.

    På bladet **Nytt konto** fyller du i fälten med informationen i följande skärmbild som vägledning. Dina värden kan vara helt olika dem på skärmbilden.
 
    ![Det nya kontobladet för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    ID|*Unikt värde*|Ett unikt namn du väljer för att identifiera Azure Cosmos DB-kontot. Eftersom*documents.azure.com* läggs till det ID du anger för att skapa din URI ska du använda ett unikt men identifierbart ID. Ditt ID får bara innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken.
    API|Gremlin (graf)|Vi kommer att programmera mot [Graph API](../articles/cosmos-db/graph-introduction.md) längre ned i den här artikeln.|
    Prenumeration|*Din prenumeration*|Den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|*Samma värde som ID*|Namnet på den nya resursgruppen för kontot. För enkelhetens skull kan du använda samma namn som för ditt ID. 
    Plats|*Regionen som ligger närmast dina användare*|Den geografiska plats som ska vara värd för ditt Azure Cosmos DB-konto. Välj den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.

4. Skapa kontot genom att klicka på **Skapa**.
5. Klicka på **Aviseringar** ![aviseringssymbol](./media/cosmos-db-create-dbaccount-graph/notification-icon.png) i det översta verktygsfältet för att övervaka distributionsprocessen.

    ![Meddelandefönstret i Azure-portalen](./media/cosmos-db-create-dbaccount-graph/notification.png)

6.  När aviseringsfönstret visar att distributionen har slutförts stänger du fönstret och öppnar det nya kontot från panelen **Alla resurser** på instrumentpanelen. 

    ![DocumentDB-konto på panelen Alla resurser](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-all-resources.png)