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
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927408"
---
Du kan använda frågor i Datautforskaren för att hämta och filtrera dina data.

1. I den övre delen av fliken **objekt** i datautforskaren granskar du standard frågan `SELECT * FROM c`. Den här frågan hämtar och visar alla dokument i samlingen i ID-ordning. 
   
   ![Standardfrågan i Datautforskaren är ”SELECT * FROM c”](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Om du vill ändra frågan väljer du **Redigera filter**, ersätter standard frågan med `ORDER BY c._ts DESC`och väljer sedan **Använd filter**.
   
   ![Ändra standardfrågan genom att lägga till ORDER BY c._ts DESC och klicka på Tillämpa filter](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   Den ändrade frågan visar dokumenten i fallande ordning baserat på deras tidstämpel, så nu visas det andra dokumentet först. 
   
   ![Ändrade frågan till ORDER BY c. _ts DESC och klicka på tillämpa filter](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Om du är bekant med SQL-syntax kan du ange alla [SQL-frågor](../articles/cosmos-db/sql-api-sql-query.md) som stöds i rutan fråga-predikat. Du kan också använda Datautforskaren för att skapa lagrade procedurer, UDF: er och utlösare för affärs logik på Server sidan. 

Datautforskaren ger enkel Azure Portal åtkomst till alla inbyggda programmerings bara data åtkomst funktioner i API: erna. Du kan också använda portalen för att skala data flöde, hämta nycklar och anslutnings strängar och granska mått och service avtal för ditt Azure Cosmos DB-konto. 

