---
title: "Skapa en funktion som utlöses av Azure Cosmos DB | Microsoft Docs"
description: "Använd Azure Functions till att skapa en serverfri funktion som anropas när data läggs till i en databas i Azure Cosmos DB."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/07/2017
ms.author: glenga
ms.custom: 
ms.openlocfilehash: 9ee3eddda53052c47f2cecff80f971f5eadac414
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Skapa en funktion som utlöses av Azure Cosmos DB

Lär dig hur du skapar en funktion som utlöses när data läggs till eller ändras i Azure Cosmos DB. Läs mer om Azure Cosmos DB i [Azure Cosmos DB: Serverless database computing using Azure Functions](..\cosmos-db\serverless-computing-database.md) (Azure Cosmos DB: Serverfria databasberäkningar med Azure Functions).

![Visa meddelande i loggarna.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

+ Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Skapa en Azure Functions-app

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Därefter skapar du en funktion i den nya funktionsappen.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Skapa en Azure Cosmos DB-utlösare

1. Expandera funktionsappen och klicka på knappen **+** bredvid **Funktioner**. Om det är den första funktionen i din funktionsapp väljer du **Anpassad funktion**. Detta visar en fullständig uppsättning med funktionsmallar.

    ![Sidan snabbstart för funktioner i Azure Portal](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

2. Skriv `cosmos` i sökfältet och välj sedan önskat språk för utlösarmallen för Azure Cosmos DB.

    ![Välj Azure Cosmos DB-utlösaren](./media/functions-create-cosmos-db-triggered-function/select-cosmos-db-trigger-portal.png)

3. Konfigurera den nya utlösaren med inställningar enligt tabellen under bilden.

    ![Skapa en funktion som utlöses av Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)
    
    | Inställning      | Föreslaget värde  | Beskrivning                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Namn** | Standard | Använd det standardfunktionsnamn som föreslås av mallen. |
    | **Samlingsnamn** | Objekt | Namnet på samlingen som ska övervakas. |
    | **Skapa en lånsamling om ingen finns** | Markerad | Samlingen finns inte redan, så du måste skapa den. |
    | **Databasnamn** | Uppgifter | Namnet på databasen med den samling som ska övervakas. |

4. Välj **Nytt** bredvid etiketten **Azure Cosmos DB-kontoanslutning** och välj ett befintligt Cosmos DB-konto eller **+ Skapa nytt**. 
 
    ![Konfigurera Azure Cosmos DB-anslutning](./media/functions-create-cosmos-db-triggered-function/functions-create-CosmosDB.png)

6. När du skapar ett nytt Cosmos DB-konto kan du använda inställningarna för **nytt konto** som anges i tabellen.

    | Inställning      | Föreslaget värde  | Beskrivning                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **ID** | Namnet på databasen | Unikt ID för Azure Cosmos DB-databasen  |
    | **API** | SQL (DocumentDB) | Här används dokumentdatabasens API.  |
    | **Prenumeration** | Azure-prenumeration | Prenumerationen som det nya Cosmos DB-kontot skapas under.  |
    | **Resursgrupp** | myResourceGroup |  Använd den befintliga resursgruppen som innehåller din funktionsapp. |
    | **Plats**  | Västeuropa | Välj en plats nära funktionsappen eller nära andra appar som använder de lagrade dokumenten.  |

6. Skapa databasen genom att klicka på **OK**. Det kan ta några minuter att skapa databasen. När databasen har skapats lagras databasanslutningssträngen som en funktionsappsinställning. Namnet på den här appinställningen infogas i **Azure Cosmos DB-kontoanslutningen**. 

7. Klicka på **Skapa** för att skapa en funktion som utlöses av Azure Cosmos DB. När funktionen har skapats visas den mallbaserade funktionskoden.  

    ![Cosmos DB – funktionsmall i C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Den här funktionsmallen skriver antalet dokument och ID:t för det första dokumentet till loggarna. 

Anslut ditt Azure Cosmos DB-konto och skapa samlingen **Tasks** (Uppgifter) i databasen. 

## <a name="create-the-items-collection"></a>Skapa objektsamlingen

1. Öppna en andra instans av [Azure-portalen](https://portal.azure.com) på en ny flik i webbläsaren. 

2. Expandera ikonfältet till vänster på portalen, skriv `cosmos` i sökfältet och välj **Azure Cosmos DB**.

    ![Sök efter Azure Cosmos DB-tjänsten](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

2. Välj ditt Azure Cosmos DB-konto och välj sedan **Datautforskaren**. 
 
3. Gå till **Samlingar**, välj **taskDatabase** och välj **Ny samling**.

    ![Skapa en samling](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

4. Gå till **Lägg till samling** och använd de inställningar som visas i tabellen nedanför bilden. 
 
    ![Definiera taskCollection](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)
 
    | Inställning|Föreslaget värde|Beskrivning |
    | ---|---|--- |
    | **Databas-ID** | Uppgifter |Namnet på din nya databas. Måste matcha namnet som definierats i funktionsbindningen. |
    | **Samlings-ID** | Objekt | Namnet på den nya samlingen. Måste matcha namnet som definierats i funktionsbindningen.  |
    | **Lagringskapacitet** | Fast (10 GB)|Använd standardvärdet. Det här värdet är databasens lagringskapacitet. |
    | **Dataflöde** |400 RU| Använd standardvärdet. Du kan skala upp dataflödet senare om du vill minska svarstiden. |
    | **[Partitionsnyckel](../cosmos-db/partition-data.md#design-for-partitioning)** | /category|En partitionsnyckel som distribuerar data jämnt till varje partition. Det är viktigt att välja rätt partitionsnyckel när man skapar en performant samling. | 

1. Klicka på **OK** för att skapa samlingen **Tasks** (Uppgifter). Det kan ta en stund att skapa samlingen.

När den samling som har angetts i funktionsbindningen väl har skapats kan du testa funktionen genom att lägga till dokument i den nya samlingen.

## <a name="test-the-function"></a>Testa funktionen

1. Expandera den nya samlingen **taskCollection** i Datautforskaren, välj **Dokument** och välj **Nytt dokument**.

    ![Skapa ett dokument i taskCollection](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

2. Ersätt innehållet i det nya dokumentet med följande innehåll och välj sedan **Spara**.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Växla till den första webbläsarfliken som innehåller din funktion i portalen. Utöka funktionsloggarna och kontrollera att det nya dokumentet har utlöst funktionen. Se efter så att dokumentets ID-värde `task1` skrivs till loggarna. 

    ![Visa meddelande i loggarna.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

4. (Valfritt) Gå tillbaka till dokumentet, gör en ändring och klicka på **Uppdatera**. Gå tillbaka till funktionsloggarna och kontrollera att uppdateringen också har utlöst funktionen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har nu skapat en funktion som körs när ett dokument läggs till eller ändras i Azure Cosmos DB.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Mer information om Cosmos DB-utlösare finns i [Azure Cosmos DB-bindningar för Azure Functions](functions-bindings-cosmosdb.md).
