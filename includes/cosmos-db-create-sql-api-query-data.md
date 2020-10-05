---
title: inkludera fil
description: inkludera fil
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 609b62312329b3a8f9f16d15458a0a47f5eb377f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "85115223"
---
Du kan använda frågor i Datautforskaren för att hämta och filtrera dina data.

1. Granska standard frågan överst på fliken **objekt** i datautforskaren `SELECT * FROM c` . Den här frågan hämtar och visar alla dokument från behållaren sorterade efter ID. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="Standard frågan i Datautforskaren är SELECT * FROM c":::
   
1. Om du vill ändra frågan väljer du **Redigera filter**, ersätter standard frågan med `ORDER BY c._ts DESC` och väljer sedan **Använd filter**.
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="Standard frågan i Datautforskaren är SELECT * FROM c":::

   Den ändrade frågan visar dokumenten i fallande ordning baserat på deras tidstämpel, så nu visas det andra dokumentet först. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="Standard frågan i Datautforskaren är SELECT * FROM c":::

Om du är bekant med SQL-syntax kan du ange alla [SQL-frågor](../articles/cosmos-db/sql-api-sql-query.md) som stöds i rutan fråga-predikat. Du kan också använda Datautforskaren för att skapa lagrade procedurer, UDF: er och utlösare för affärs logik på Server sidan. 

Datautforskaren ger enkel Azure Portal åtkomst till alla inbyggda programmerings bara data åtkomst funktioner i API: erna. Du kan också använda portalen för att skala data flöde, hämta nycklar och anslutnings strängar och granska mått och service avtal för ditt Azure Cosmos DB-konto. 

