---
title: Create an Azure Cosmos database from the Azure portal.
description: Create an Azure Cosmos database, container, and items by using the Azure portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/01/2019
ms.openlocfilehash: b42a442564812f4386eb94b9bd7b7c9aff9e9f29
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74220736"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Quickstart: Create an Azure Cosmos account, database, container, and items from the Azure portal

> [!div class="op_single_selector"]
> * [Azure-portalen](create-cosmosdb-resources-portal.md)
> * [NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. You can use Azure Cosmos DB to quickly create and query key/value databases, document databases, and graph databases, all of which benefit from the global distribution and horizontal scale capabilities at the core of Azure Cosmos DB. 

This quickstart demonstrates how to use the Azure portal to create an Azure Cosmos DB [SQL API](sql-api-introduction.md) account, create a document database and container, and add data to the container. 

## <a name="prerequisites"></a>Krav

An Azure subscription or free Azure Cosmos DB trial account
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Add a database and a container 

You can use the Data Explorer in the Azure portal to create a database and container. 

1.  Select **Data Explorer** from the left navigation on your Azure Cosmos DB account page, and then select **New Container**. 
    
    You may need to scroll right to see the **Add Container** window.
    
    ![The Azure portal Data Explorer, Add Container pane](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  In the **Add container** pane, enter the settings for the new container.
    
    |Inställning|Föreslaget värde|Beskrivning
    |---|---|---|
    |**Databas-ID**|ToDoList|Enter *ToDoList* as the name for the new database. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Check the **Provision database throughput** option, it allows you to share the throughput provisioned to the database across all the containers within the database. This option also helps with cost savings. |
    |**Dataflöde**|400|Leave the throughput at 400 request units per second (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.| 
    |**Container ID**|Objekt|Enter *Items* as the name for your new container. Container IDs have the same character requirements as database names.|
    |**Partition key**| /category| The sample described in this article uses */category* as the partition key.|

    
    Don't add **Unique keys** for this example. Unique keys let you add a layer of data integrity to the database by ensuring the uniqueness of one or more values per partition key. For more information, see [Unique keys in Azure Cosmos DB](unique-keys.md).
    
1.  Välj **OK**. The Data Explorer displays the new database and the container that you created.

## <a name="add-data-to-your-database"></a>Add data to your database

Add data to your new database using Data Explorer.

1. In **Data Explorer**, expand the **ToDoList** database, and expand the **Items** container. Next, select **Items**, and then select **New Item**. 
   
   ![Skapa nya dokument i datautforskaren i Azure-portalen](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Add the following structure to the document on the right side of the **Documents** pane:

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
   
   ![Copy in json data and select Save in Data Explorer in the Azure portal](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Select **New Document** again, and create and save another document with a unique `id`, and any other properties and values you want. Your documents can have any structure, because Azure Cosmos DB doesn't impose any schema on your data.

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

In this quickstart, you learned how to create an Azure Cosmos DB account, create a database and container using the Data Explorer. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)