---
title: ta med fil
description: ta med fil
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 0f471c5aefdb39396ea189984d32a9e8f5419182
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84200473"
---
Du kan nu använda Datautforskaren-verktyget i Azure Portal för att skapa ett Azure Cosmos DBs API för MongoDB Database och container. 

1. Välj **datautforskaren**  >  **ny behållare**. 
    
    Avsnittet **Lägg till behållare** visas längst till höger. du kan behöva rulla åt höger för att se det.

    ![Datautforskaren på Azure-portalen, fönstret Lägg till container](./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png)

2. På sidan **Lägg till behållare** anger du inställningarna för den nya behållaren.

    |Inställning|Föreslaget värde|Description
    |---|---|---|
    |**Databas-ID**|databasen|Ange *db* som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Kontrol lera **data flödes alternativet etablera databas** så att du kan dela det data flöde som har etablerats till databasen över alla behållare i databasen. Det här alternativet hjälper också till med kostnads besparingar. |
    |**Dataflöde**|400|Lämna data flödet på 400 enheter för programbegäran per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden. Du kan också välja [autoskalning-läge](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale), vilket ger dig en rad ru/s-intervall som kan öka och minska dynamiskt efter behov.| 
    |**Samlings-ID**|Coll|Ange `coll` som namn på den nya behållaren. För container-ID:n gäller samma teckenkrav som för databasnamn.|
    |**Lagrings kapacitet**|Fast (10 GB)|Ange *fast (10 GB)* för det här programmet. Om du väljer *obegränsat*måste du skapa en `Shard Key` , vilket innebär att alla objekt som infogas krävs.|
    |**Shard-nyckel**| /_id| Exemplet som beskrivs i den här artikeln använder inte någon Shard-nyckel, så om du anger den */_ID* används fältet automatiskt genererat ID som Shard-nyckel. Lär dig mer om horisontell partitionering, även kallat partitionering, i [partitionering i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)|
        
    Välj **OK**. Datautforskaren visar den nya databasen och containern.
