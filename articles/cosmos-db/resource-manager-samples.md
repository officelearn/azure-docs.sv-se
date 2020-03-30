---
title: Azure Resource Manager-mallar för Azure Cosmos DB
description: Använd Azure Resource Manager-mallar för att skapa och konfigurera Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961862"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-mallar för Azure Cosmos DB

Följande tabeller innehåller länkar till Azure Resource Manager-mallar för Azure Cosmos DB:

## <a name="sql-core-api"></a>SQL-API (Core)

|**Mall**|**Beskrivning**|
|---|---|
|[Skapa ett Azure Cosmos-konto, databas, behållare](manage-sql-with-resource-manager.md#create-resource) | Den här mallen skapar ett SQL-API-konto (Core) i två regioner med två behållare med delat databasdataflöde och en behållare med dedikerat dataflöde. Dataflöde kan uppdateras genom att skicka in mallen igen med ett uppdaterat egenskapsvärde för dataflöde. |
|[Skapa ett Azure Cosmos-konto, databas och behållare med en lagrad procedur, utlösare och UDF](manage-sql-with-resource-manager.md#create-sproc) | Den här mallen skapar ett SQL-API-konto (Core) i två regioner med en lagrad procedur, utlösare och UDF för en behållare. |

## <a name="mongodb-api"></a>MongoDB-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, databas, samling](manage-mongodb-with-resource-manager.md#create-resource) | Den här mallen skapar ett konto med Azure Cosmos DB API för MongoDB i två regioner med multi-master aktiverat. Azure Cosmos-kontot har två behållare som delar dataflöde på databasnivå. |

## <a name="cassandra-api"></a>Cassandra-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, keyspace, tabell](manage-cassandra-with-resource-manager.md#create-resource) | Den här mallen skapar ett Cassandra API-konto i två regioner med multi-master aktiverat. Azure Cosmos-kontot har två tabeller som delar dataflöde på keyspace-nivå. |

## <a name="gremlin-api"></a>Gremlin-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, databas, diagram](manage-gremlin-with-resource-manager.md#create-resource) | Den här mallen skapar ett Gremlin API-konto i två regioner med multi-master aktiverat. Azure Cosmos-kontot har två diagram som delar dataflöde på databasnivå. |

## <a name="table-api"></a>Tabell-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, tabell](manage-table-with-resource-manager.md#create-resource) | Den här mallen skapar ett tabell-API-konto i två regioner med multi-master aktiverat. Azure Cosmos-kontot har en enda tabell. |

> [!TIP]
> Om du vill aktivera delat dataflöde när du använder Tabell-API aktiverar du dataflöde på kontonivå i Azure Portal.

Se [Azure Resource Manager-referens för Azure Cosmos DB-sidan](/azure/templates/microsoft.documentdb/allversions) för referensdokumentationen.
