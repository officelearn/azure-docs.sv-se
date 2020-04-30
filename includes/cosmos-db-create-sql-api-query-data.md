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
ms.openlocfilehash: 408fb2c40e645d9a8b10f1e04d282e134c5489a1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80501636"
---
Du kan använda frågor i Datautforskaren för att hämta och filtrera dina data.

1. Granska standard frågan `SELECT * FROM c`överst på fliken **objekt** i datautforskaren. Den här frågan hämtar och visar alla dokument från behållaren sorterade efter ID. 
   
   ![Standardfrågan i Datautforskaren är ”SELECT * FROM c”](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Om du vill ändra frågan väljer du **Redigera filter**, ersätter standard frågan med `ORDER BY c._ts DESC`och väljer sedan **Använd filter**.
   
   ![Ändra standardfrågan genom att lägga till ORDER BY c._ts DESC och klicka på Tillämpa filter](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   Den ändrade frågan visar dokumenten i fallande ordning baserat på deras tidstämpel, så nu visas det andra dokumentet först. 
   
   ![Ändrade frågan till ORDER BY c. _ts DESC och klicka på tillämpa filter](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Om du är bekant med SQL-syntax kan du ange alla [SQL-frågor](../articles/cosmos-db/sql-api-sql-query.md) som stöds i rutan fråga-predikat. Du kan också använda Datautforskaren för att skapa lagrade procedurer, UDF: er och utlösare för affärs logik på Server sidan. 

Datautforskaren ger enkel Azure Portal åtkomst till alla inbyggda programmerings bara data åtkomst funktioner i API: erna. Du kan också använda portalen för att skala data flöde, hämta nycklar och anslutnings strängar och granska mått och service avtal för ditt Azure Cosmos DB-konto. 

