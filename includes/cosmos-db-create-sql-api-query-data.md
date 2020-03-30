---
title: ta med fil
description: ta med fil
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 64c900e01496dad0d5f6a96d8d790ae0c2a8c95a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927408"
---
Du kan använda frågor i Data Explorer för att hämta och filtrera data.

1. Läs standardfrågan `SELECT * FROM c`högst upp på fliken **Objekt** i Utforskaren . Den här frågan hämtar och visar alla dokument i samlingen i ID-ordning. 
   
   ![Standardfrågan i Datautforskaren är ”SELECT * FROM c”](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Om du vill ändra frågan väljer du `ORDER BY c._ts DESC`Redigera **filter**, ersätter standardfrågan med och väljer sedan Använd **filter**.
   
   ![Ändra standardfrågan genom att lägga till ORDER BY c._ts DESC och klicka på Tillämpa filter](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   Den ändrade frågan visar dokumenten i fallande ordning baserat på deras tidsstämpel, så nu visas det andra dokumentet först. 
   
   ![Ändrad fråga till ORDER BY c._ts DESC och klicka på Använd filter](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Om du är bekant med SQL-syntax kan du ange eventuella [SQL-frågor som](../articles/cosmos-db/sql-api-sql-query.md) stöds i frågepredikatrutan. Du kan också använda Data Explorer för att skapa lagrade procedurer, UDF:er och utlösare för affärslogik på serversidan. 

Data Explorer ger enkel Azure-portalåtkomst till alla inbyggda programmatiska dataåtkomstfunktioner som är tillgängliga i API:erna. Du använder också portalen för att skala dataflödet, hämta nycklar och anslutningssträngar och granska mått och SLA för ditt Azure Cosmos DB-konto. 

