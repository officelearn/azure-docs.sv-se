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
ms.openlocfilehash: 9971b16da42cdf1de0464857291c74a947535735
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59805203"
---
Du kan använda frågor i Datautforskaren för att hämta och filtrera dina data.

1. Överst på den **dokument** fliken i Datautforskaren kan du granska standardfrågan `SELECT * FROM c`. Den här frågan hämtar och visar alla dokument i samlingen i ID ordning. 
   
   ![Standardfrågan i Datautforskaren är ”SELECT * FROM c”](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. För att ändra frågan väljer **Redigera Filter**, Ersätt standardfrågan med `ORDER BY c._ts DESC`, och välj sedan **tillämpa Filter**.
   
   ![Ändra standardfrågan genom att lägga till ORDER BY c._ts DESC och klicka på Tillämpa filter](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   De ändrade frågan visar dokumenten i fallande ordning baserat på deras tidsstämpel, så nu ditt andra dokument visas först. 
   
   ![Ändrade frågan till ORDER BY c._ts DESC och klicka på tillämpa Filter](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Om du är bekant med SQL-syntax kan du ange någon stöds [SQL-frågor](../articles/cosmos-db/sql-api-sql-query.md) i frågepredikatrutan. Du kan även använda Datautforskaren för att skapa lagrade procedurer, UDF: er och utlösare för affärslogik på serversidan. 

Datautforskaren ger enkel åtkomst med Azure portal till alla data med inbyggda programmässig åtkomst tillgängliga funktioner i API: erna. Du kan också använda portalen för att skala dataflöde, hämta nycklar och anslutningssträngar och granska mått och serviceavtal för Azure Cosmos DB-kontot. 

