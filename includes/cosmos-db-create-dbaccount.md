1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt fönster.
2. Klicka på **Nytt** på menyn till vänster. Klicka på **Databaser** och sedan på **Azure Cosmos DB**.
   
   ![Azure Portal-databasfönstret](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. På bladet **Nytt konto** anger du önskad konfiguration för Azure Cosmos DB-kontot. 

    Med Azure Cosmos DB kan du välja någon av fyra programmeringsmodeller: Gremlin (graf), MongoDB, SQL (DocumentDB) och Tabell (nyckelvärde). 
    
    I den här snabbstarten kommer vi att programmera mot DocumentDB-API:t, så du väljer **SQL (DocumentDB)** när du fyller i formuläret. Om du däremot har grafdata för en app för sociala medier, nyckel/värde-data (tabell) eller data som har migrerats från en MongoDB-app ska du tänka på att Azure Cosmos DB kan vara en mycket tillgänglig och globalt distribuerad plattform för databastjänster i alla dina verksamhetskritiska program.

    På bladet **Nytt konto** fyller du i fälten med informationen i följande skärmbild som vägledning. När du konfigurerar ditt konto ska du välja unika värden som inte matchar de som finns i skärmbilden. 
 
    ![Det nya Azure Cosmos DB-bladet](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    ID|*Unikt värde*|Ett unikt namn du väljer för att identifiera Azure Cosmos DB-kontot. Strängen *documents.azure.com* läggs till det ID du anger för att skapa din URI, så använd ett unikt men identifierbart ID. Ditt ID får bara innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken.
    API|SQL (DocumentDB)|Vi kommer att programmera mot [DocumentDB-API:t](../articles/documentdb/documentdb-introduction.md) längre ned i den här artikeln.|
    Prenumeration|*Din prenumeration*|Den Azure-prenumeration du vill använda för Azure Cosmos DB-kontot. 
    Resursgrupp|*Samma värde som ID*|Namnet på den nya resursgruppen för kontot. För enkelhetens skull kan du använda samma namn som för ditt ID. 
    Plats|*Regionen som ligger närmast dina användare*|Den geografiska plats som ska vara värd för ditt Azure Cosmos DB-konto. Välj den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.
4. Skapa kontot genom att klicka på **Skapa**.
5. Klicka på **Aviseringar** i det översta verktygsfältet för att övervaka distributionsprocessen.

    ![Meddelandefönstret i Azure-portalen](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  När distributionen är färdig öppnar du det nya kontot från panelen **Alla resurser**. 

    ![DocumentDB-konto på panelen Alla resurser](./media/cosmos-db-create-dbaccount/all-resources.png)
 
