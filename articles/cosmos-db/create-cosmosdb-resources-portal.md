---
title: Snabbstart - Skapa Azure Cosmos DB-resurser från Azure-portalen
description: Den här snabbstarten visar hur du skapar en Azure Cosmos-databas, behållare och objekt med hjälp av Azure-portalen.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/05/2020
ms.openlocfilehash: 79deb2f33a11e8ccb6f059bde7590b7cc0fe20c0
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521097"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Snabbstart: Skapa ett Azure Cosmos-konto, databas, behållare och objekt från Azure-portalen

> [!div class="op_single_selector"]
> * [Azure Portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan använda Azure Cosmos DB för att snabbt skapa och fråga efter nyckel-/värdedatabaser, dokumentdatabaser och grafdatabaser, som alla drar nytta av de globala distributions- och vågräta skalfunktionerna i kärnan av Azure Cosmos DB. 

Den här snabbstarten visar hur du använder Azure-portalen för att skapa ett Azure Cosmos DB [SQL API-konto,](sql-api-introduction.md) skapa en dokumentdatabas och behållare och lägga till data i behållaren. 

## <a name="prerequisites"></a>Krav

En Azure-prenumeration eller ett kostnadsfritt utvärderingskonto för Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Gå till [Microsoft Azure-portalen](https://portal.azure.com/) för att skapa ett Azure Cosmos DB-konto. Sök efter och välj **Azure Cosmos DB**.

   ![Azure Portal-databasfönstret](./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png)

1. Välj **Lägg till**.
1. Ange de grundläggande inställningarna för det nya Azure Cosmos-kontot på sidan **Skapa Azure Cosmos DB-konto**. 

    |Inställning|Värde|Beskrivning |
    |---|---|---|
    |Prenumeration|Prenumerationens namn|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos-kontot. |
    |Resursgrupp|Namn på resursgrupp|Välj en resursgrupp eller välj **Skapa ny** och ange sedan ett unikt namn för den nya resursgruppen. |
    |Kontonamn|Ett unikt namn|Ange ett namn som identifierar ditt Azure Cosmos-konto. Eftersom*documents.azure.com* läggs till det namn du anger för att skapa din URI måste du använda ett unikt namn.<br><br>Namnet får endast innehålla gemener, siffror och bindestreck (-). Det måste innehålla 3–31 tecken.|
    |API|Typ av konto som skapas|Välj **Core (SQL)** för att skapa en dokumentdatabas och kör frågor med hjälp av SQL-syntax. <br><br>API:n avgör vilken typ av konto som skapas. Azure Cosmos DB innehåller fem API:er: Core (SQL) och MongoDB för dokumentdata, Gremlin för diagramdata, Azure Table och Cassandra. För närvarande måste du skapa ett separat konto för varje API. <br><br>[Läs mer om SQL API](introduction.md).|
    |Tillämpa rabatt på kostnadsfri nivå|Ansök eller Gäller inte|Med Azure Cosmos DB-kostnadsfri nivå får du de första 400 RU/s och 5 GB lagringsutrymme som är gratis på ett konto. Läs mer om [den kostnadsfria nivån](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Location|Den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare för att ge dem så snabb åtkomst till data som möjligt.|
    |Typ av konto|Produktion eller icke-produktion|Välj **Produktion** om kontot ska användas för en produktionsarbetsbelastning. Välj **Icke-produktion** om kontot ska användas för icke-produktion, t.ex. Det här är en Azure-resurstagginställning som trimmar portalupplevelsen men inte påverkar det underliggande Azure Cosmos DB-kontot. Du kan ändra det här värdet när som helst.|


> [!NOTE]
> Du kan ha upp till en kostnadsfri nivå Azure Cosmos DB-konto per Azure-prenumeration och måste anmäla dig när du skapar kontot. Om du inte ser alternativet att tillämpa rabatten på den kostnadsfria nivån innebär det att ett annat konto i prenumerationen redan har aktiverats med den kostnadsfria nivån.
   
   ![Den nya kontosidan för Azure Cosmos DB](./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail.png)

1. Välj **Granska + skapa**. Du kan hoppa över avsnitten **Nätverk** och **Taggar**.

1. Granska kontoinställningarna och välj sedan **Skapa**. Det tar några minuter att skapa kontot. Vänta tills portalsidan visar meddelandet **Distributionen är klar**. 

    ![Meddelandefönstret i Azure-portalen](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png)

1. Välj **Gå till resurs** för att gå till sidan för Azure Cosmos DB-kontot. 

    ![Sidan för Azure Cosmos DB-kontot](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png)

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Lägga till en databas och en behållare 

Du kan använda Data Explorer i Azure-portalen för att skapa en databas och behållare. 

1.  Välj **Data Explorer** från den vänstra navigeringen på din Azure Cosmos DB-kontosida och välj sedan Ny **behållare**. 
    
    Du kan behöva rulla åt höger för att se fönstret **Lägg till behållare.**
    
    ![Datautforskaren på Azure-portalen, fönstret Lägg till container](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  Ange inställningarna för den nya behållaren i fönstret **Lägg till behållare.**
    
    |Inställning|Föreslaget värde|Beskrivning
    |---|---|---|
    |**Databas-ID**|ToDoList|Ange *ToDoList* som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Kontrollera **alternativet Etablera databasdataflöde,** det låter dig dela dataflödet som etablerats i databasen över alla behållare i databasen. Det här alternativet hjälper också till med kostnadsbesparingar. |
    |**Dataflöde**|400|Lämna dataflödet på 400 begäranheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.| 
    |**Container-ID**|Objekt|Ange *objekt* som namn på den nya behållaren. För container-ID:n gäller samma teckenkrav som för databasnamn.|
    |**Partitionsnyckeln**| /category| I exemplet som beskrivs i den här artikeln används */category* som partitionsnyckel.|

    
    Lägg inte till **unika nycklar** för det här exemplet. Med unika nycklar kan du lägga till ett lager av dataintegritet i databasen genom att säkerställa att ett eller flera värden per partitionsnyckel är unika. Mer information finns [i Unika nycklar i Azure Cosmos DB](unique-keys.md).
    
1.  Välj **OK**. Datautforskaren visar den nya databasen och behållaren som du skapade.

## <a name="add-data-to-your-database"></a>Lägga till data i databasen

Lägg till data i den nya databasen med Data Explorer.

1. Expandera **ToDoList-databasen** i **Data Explorer**och expandera behållaren **Objekt.** Välj sedan **Objekt**och välj sedan **Nytt objekt**. 
   
   ![Skapa nya dokument i datautforskaren i Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Lägg till följande struktur i dokumentet till höger i **dokumentfönstret:**

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Välj **Spara**.
   
   ![Kopiera i json-data och välj Spara i Utforskaren i Azure-portalen](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Välj **Nytt dokument** igen och skapa och `id`spara ett annat dokument med ett unikt och andra egenskaper och värden som du vill använda. Dina dokument kan ha vilken struktur som helst, eftersom Azure Cosmos DB inte medför något schema för dina data.

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Om du bara vill ta bort databasen och använda Azure Cosmos-kontot i framtiden kan du ta bort databasen med följande steg:

* Kom till ditt Azure Cosmos-konto.
* Öppna **Data Explorer,** högerklicka på den databas som du vill ta bort och välj **Ta bort databas**.
* Ange databas-ID/databasnamn för att bekräfta borttagningen. 

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar ett Azure Cosmos DB-konto, skapar en databas och behållare med datautforskaren. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
