---
title: ta med fil
description: ta med fil
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 08/07/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 6656c374ae0ff4aae7f99fd340e9e25e5cbc67d1
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854154"
---
Du kan nu lägga till data till din nya behållare med hjälp av Datautforskaren.

1. Expandera tasks-databasen från **datautforskaren**, expandera behållaren **objekt** . Välj **objekt**och klicka sedan på **nytt objekt**.

   ![Skapa nya dokument i datautforskaren i Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Lägg nu till ett dokument i behållaren med följande struktur.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. När du har lagt till JSON på fliken **dokument** väljer du **Spara**.

    ![Kopiera i JSON-data och välj Spara i Datautforskaren i Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Skapa och spara ännu ett dokument där du lägger till ett unikt värde för egenskapen `id` och ändrar de andra egenskaperna som passar. Dina nya dokument kan ha vilken struktur du vill eftersom Azure Cosmos DB inte kräver något schema för dina data.