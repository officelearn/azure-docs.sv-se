---
title: Lär dig hur du flyttar ett Azure Cosmos DB-konto till en annan region
description: Lär dig hur du flyttar ett Azure Cosmos DB-konto till en annan region
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 60c28a96008355491c058cd08dbbb3a1cbffad98
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059454"
---
# <a name="how-to-move-an-azure-cosmos-db-account-to-another-region"></a>Flytta ett Azure Cosmos DB-konto till en annan region

Den här artikeln beskriver hur du antingen flyttar en region där data replikeras i Azure Cosmos DB eller hur du migrerar kontots (Azure Resource Manager) metadata och data från en region till en annan.

## <a name="move-data-from-one-region-to-another"></a>Flytta data från en region till en annan

Azure Cosmos DB stöder datareplikering internt så att det går att flytta data från en region till en annan, och det kan utföras med hjälp av Azure Portal, Azure PowerShell eller Azure CLI och omfattar följande steg:

1. Lägg till en ny region i kontot

    Om du vill lägga till en ny region i ett Azure Cosmos DB konto, [Lägg till/ta bort regioner i ett Azure Cosmos DB konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

1. Utföra en manuell redundansväxling till den nya regionen

    I situationer där den region som tas bort för närvarande är Skriv region för kontot måste du initiera en redundansväxling till den nya region som lagts till ovan. Detta är en avbrotts åtgärd som är noll. Om du flyttar en Läs region i ett konto med flera regioner kan du hoppa över det här steget. Om du vill starta en redundansväxling kan du [utföra manuell redundans på ett Azure Cosmos-konto](how-to-manage-database-account.md#manual-failover)

1. Ta bort den ursprungliga regionen

    Om du vill ta bort en region från ett Azure Cosmos DB konto, se [Lägg till/ta bort regioner i ett Azure Cosmos DB konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

## <a name="migrate-azure-cosmos-db-account-meta-data"></a>Migrera metadata för Azure Cosmos DBs konto

Azure Cosmos DB stöder inte internt migrering av konto-meta-data från en region till en annan. Om du vill migrera både kontots metadata och kund information från en region till en annan måste ett nytt konto skapas i önskad region och data måste kopieras manuellt. En nära noll stillestånds tids migrering för SQL API kräver att [ChangeFeed](change-feed.md) eller ett verktyg som utnyttjar det används. Om du migrerar MongoDB API, Cassandra eller annat API eller om du vill lära dig mer om alternativ när du migrerar data mellan konton ser du [alternativ för att migrera dina lokala eller molnbaserade data till Azure Cosmos DB](cosmosdb-migrationchoices.md). Stegen nedan visar hur du migrerar ett Azure Cosmos DB-konto för SQL-API och dess data från en region till en annan:

1. Skapa ett nytt Azure Cosmos DB konto i önskad region

    Om du vill skapa ett nytt konto via Azure Portal, PowerShell eller CLI ser du [skapa ett Azure Cosmos DB konto](how-to-manage-database-account.md#create-an-account).

1. Skapa en ny databas och container

    Om du vill skapa en ny databas och behållare ser du [skapa en Azure Cosmos-behållare](how-to-create-container.md)

1. Migrera data med verktyget Azure Cosmos DB Live data Migrator

    Om du vill migrera data med nästan noll stillestånd ser [Azure Cosmos DB Live data Migrator-verktyget](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)

1. Uppdatera program anslutnings sträng

    Med verktyget Live Migrator fortfarande igång uppdaterar du anslutnings informationen i den nya distributionen av dina program. Du kan hämta slut punkter och nycklar för programmet från Azure Portal.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Åtkomst kontroll (IAM) i Azure Portal – demonstrera NoSQL Database-säkerhet":::

1. Omdirigera begär anden till nytt program

    När det nya programmet är anslutet till Azure Cosmos DB kan du omdirigera klient begär anden till din nya distribution.

1. Ta bort alla resurser som inte längre behövs

    Med begär Anden som omdirigeras fullständigt till den nya instansen kan du ta bort det gamla Azure Cosmos DB-kontot och verktyget live data Migrator.

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du hanterar Azure Cosmos-kontot samt databas och behållare finns i följande artiklar:

* [Hantera ett Azure Cosmos-konto](how-to-manage-database-account.md)
* [Ändra feed i Azure Cosmos DB](change-feed.md)
