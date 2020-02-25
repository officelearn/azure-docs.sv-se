---
title: Snabb start – skapa Azure Cosmos DB resurser från Azure Portal
description: Den här snabb starten visar hur du skapar en Azure Cosmos-databas, behållare och objekt med hjälp av Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/05/2020
ms.openlocfilehash: bc7e77cc498958b2f8f0c5b2d5ab2d59db97a235
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560838"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Snabb start: skapa ett Azure Cosmos-konto, databas, behållare och objekt från Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](create-cosmosdb-resources-portal.md)
> * [NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan använda Azure Cosmos DB för att snabbt skapa och fråga nyckel/värde-databaser, dokument databaser och Graf-databaser, som alla drar nytta av den globala distributionen och den horisontella skalnings kapaciteten i kärnan av Azure Cosmos DB. 

Den här snabb starten visar hur du använder Azure Portal för att skapa ett Azure Cosmos DB [SQL API](sql-api-introduction.md) -konto, skapa en dokument databas och behållare och lägga till data i behållaren. 

## <a name="prerequisites"></a>Förutsättningar

En Azure-prenumeration eller ett kostnads fritt Azure Cosmos DB utvärderings konto
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Lägg till en databas och en behållare 

Du kan använda Datautforskaren i Azure Portal för att skapa en databas och behållare. 

1.  Välj **datautforskaren** i det vänstra navigerings fältet på ditt Azure Cosmos DB konto och välj sedan **ny behållare**. 
    
    Du kan behöva rulla åt höger för att se fönstret **Lägg till behållare** .
    
    ![Fönstret Azure Portal Datautforskaren, Lägg till behållare](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  I fönstret **Lägg till behållare** anger du inställningarna för den nya behållaren.
    
    |Inställning|Föreslaget värde|Beskrivning
    |---|---|---|
    |**Databas-ID**|ToDoList|Ange *ToDoList* som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Kontrol lera **data flödes alternativet etablera databas** så att du kan dela det data flöde som har etablerats till databasen över alla behållare i databasen. Det här alternativet hjälper också till med kostnads besparingar. |
    |**Dataflöde**|400|Lämna data flödet på 400 enheter för programbegäran per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.| 
    |**Container-ID**|Objekt|Ange *objekt* som namn på den nya behållaren. Container-ID: n har samma teckenuppsättnings krav som databas namn.|
    |**Partitionsnyckel**| /category| Exemplet som beskrivs i den här artikeln använder */Category* som partitionsnyckel.|

    
    Lägg inte till **unika nycklar** i det här exemplet. Med unika nycklar kan du lägga till ett lager med data integritet i databasen genom att se till att det är unikt för ett eller flera värden per partitionsnyckel. Mer information finns i [unika nycklar i Azure Cosmos DB](unique-keys.md).
    
1.  Välj **OK**. Datautforskaren visar den nya databasen och den behållare som du skapade.

## <a name="add-data-to-your-database"></a>Lägg till data i databasen

Lägg till data i den nya databasen med hjälp av Datautforskaren.

1. Expandera **ToDoList** -databasen i **datautforskaren**och expandera behållaren **objekt** . Välj sedan **objekt**och välj sedan **nytt objekt**. 
   
   ![Skapa nya dokument i datautforskaren i Azure-portalen](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Lägg till följande struktur till dokumentet till höger i fönstret **dokument** :

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
   
   ![Kopiera i JSON-data och välj Spara i Datautforskaren i Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Välj **nytt dokument** igen och skapa och spara ett annat dokument med ett unikt `id`och andra egenskaper och värden som du vill ha. Dokumenten kan ha vilken struktur som helst, eftersom Azure Cosmos DB inte har något schema för dina data.

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Om du bara vill ta bort databasen och använda Azure Cosmos-kontot i framtiden kan du ta bort databasen med följande steg:

* Fick ditt Azure Cosmos-konto.
* Öppna **datautforskaren**, högerklicka på den databas som du vill ta bort och välj **ta bort databas**.
* Ange databas-ID/databas namn för att bekräfta borttagnings åtgärden. 

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en databas och behållare med hjälp av Datautforskaren. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
