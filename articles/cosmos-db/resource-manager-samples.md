---
title: Azure Resource Manager-mallar för Azure Cosmos DB
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: a98aee61c3a330344b5b7fa0a5f7f53a65e5ecdb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83586158"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-mallar för Azure Cosmos DB

Följande tabeller innehåller länkar till Azure Resource Manager mallar för Azure Cosmos DB:

## <a name="core-sql-api"></a>Core (SQL) API

|**Mall**|**Beskrivning**|
|---|---|
|[Skapa ett Azure Cosmos-konto, en databas, container med autoskalning av data flöde](manage-sql-with-resource-manager.md#create-autoscale) | Den här mallen skapar ett kärn-API-konto (SQL) i två regioner, en databas och container med autoskalning av data flöde. |
|[Skapa ett Azure Cosmos-konto, en databas, container med analys lager](manage-sql-with-resource-manager.md#create-analytical-store) | Den här mallen skapar ett huvud-API-konto i en region med en behållare som kon figurer ATS med analytiskt TTL aktiverat och alternativet för att använda manuell eller autoskalning av data flöde. |
|[Skapa ett Azure Cosmos-konto, en databas, behållare med standard (manuell) data flöde](manage-sql-with-resource-manager.md#create-manual) | Den här mallen skapar ett kärn-API-konto (SQL) i två regioner, en databas och en behållare med standard data flöde. |
|[Skapa ett Azure Cosmos-konto, en databas och en behållare med en lagrad procedur, utlösare och UDF](manage-sql-with-resource-manager.md#create-sproc) | Den här mallen skapar ett Core (SQL) API-konto i två regioner med en lagrad procedur, utlösare och UDF för en behållare. |
|[Skapa en privat slut punkt för ett befintligt Azure Cosmos-konto](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Den här mallen skapar en privat slut punkt för ett befintligt API-konto i Azure Cosmos Core (SQL) i ett befintligt virtuellt nätverk. |
|[Skapa ett Azure Cosmos-konto på den kostnads fria nivån](manage-sql-with-resource-manager.md#free-tier) |  Den här mallen skapar ett API-konto för Azure Cosmos DB Core (SQL) på den kostnads fria nivån. |

## <a name="mongodb-api"></a>MongoDB-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, databas, samling med autoskalning av data flöde](manage-mongodb-with-resource-manager.md#create-autoscale) | Den här mallen skapar ett konto med hjälp av Azure Cosmos DB API för MongoDB i två regioner med två behållare som delar data flöde på databas nivå. |
|[Skapa ett Azure Cosmos-konto, en databas, en samling med standard (manuell) data flöde](manage-mongodb-with-resource-manager.md#create-manual) | Den här mallen skapar ett konto med hjälp av Azure Cosmos DB API för MongoDB i två regioner med två behållare som delar databas nivåns standard data flöde. |

## <a name="cassandra-api"></a>Cassandra-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, nyckel utrymme, tabell med autoskalning av data flöde](manage-cassandra-with-resource-manager.md#create-autoscale) | Den här mallen skapar ett API för Cassandra konto i två regioner med ett nyckel utrymme och en tabell med autoskalning av data flöde. |
|[Skapa ett Azure Cosmos-konto, ett disk utrymme, en tabell med standard data flöde (manuell)](manage-cassandra-with-resource-manager.md#create-manual) | Den här mallen skapar ett API för Cassandra konto i två regioner med ett blank steg och en tabell med manuellt data flöde. |

## <a name="gremlin-api"></a>Gremlin-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, en databas, Graf med autoskalning av data flöde](manage-gremlin-with-resource-manager.md#create-autoscale) | Den här mallen skapar ett Gremlin API-konto i två regioner med en databas och en graf med autoskalning av data flöde. |
|[Skapa ett Azure Cosmos-konto, en databas, Graf med standard (manuell) data flöde](manage-gremlin-with-resource-manager.md#create-manual) | Den här mallen skapar ett Gremlin API-konto i två regioner med en databas och en graf med standard data flöde. |

## <a name="table-api"></a>Tabell-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, tabell med autoskalning av data flöde](manage-table-with-resource-manager.md#create-autoscale) | Den här mallen skapar ett Tabell-API-konto i två regioner och en enda tabell med autoskalning av data flödet. |
|[Skapa ett Azure Cosmos-konto, en tabell med standard data flöde (Manual)](manage-table-with-resource-manager.md#create-manual) | Den här mallen skapar ett Tabell-API konto i två regioner och en enda tabell med standard data flöde. |

Se [Azure Resource Manager referens för Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) sidan för referens dokumentationen.
