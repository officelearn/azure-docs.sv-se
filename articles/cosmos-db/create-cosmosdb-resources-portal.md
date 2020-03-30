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
ms.openlocfilehash: bc7e77cc498958b2f8f0c5b2d5ab2d59db97a235
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240410"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Snabbstart: Skapa ett Azure Cosmos-konto, databas, behållare och objekt från Azure-portalen

> [!div class="op_single_selector"]
> * [Azure-portal](create-cosmosdb-resources-portal.md)
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

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

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
