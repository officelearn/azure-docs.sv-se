---
title: ta med fil
description: ta med fil
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 92b739424d1d6f0e1eb89d5993718f5c36162204
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
Du kan nu lägga till data till din nya samling med datautforskaren.

1. Den nya databasen visas på panelen Samlingar i datautforskaren. Expandera databasen **Tasks** (Aktiviteter), expandera samlingen **Items** (Objekt), klicka på **Dokument** och klicka sedan på **Nya dokument**. 

   ![Skapa nya dokument i datautforskaren i Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Lägg nu till ett dokument i samlingen med följande struktur.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. När du har lagt till json på fliken **Dokument** klickar du på **Spara**.

    ![Kopiera in json-data och klicka på Spara i Datautforskaren i Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Skapa och spara ännu ett dokument där du lägger till ett unikt värde för egenskapen `id` och ändrar de andra egenskaperna som passar. Dina nya dokument kan ha vilken struktur du vill eftersom Azure Cosmos DB inte kräver något schema för dina data.