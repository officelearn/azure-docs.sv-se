---
title: Azure Resource Manager mallar för Azure Cosmos DB
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: ecdfe89e899e0d416784ae32a0d66b335c09e2b6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582815"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager mallar för Azure Cosmos DB

Följande tabeller innehåller länkar till Azure Resource Manager mallar för Azure Cosmos DB:

## <a name="sql-core-api"></a>SQL (Core) API

|**Mall**|**Beskrivning**|
|---|---|
|[Skapa ett Azure Cosmos-konto, en databas, en behållare](manage-sql-with-resource-manager.md#create-resource) | Den här mallen skapar ett SQL (Core) API-konto i två regioner med två behållare med data flöde för delad databas och en behållare med dedikerat data flöde. Data flödet kan uppdateras genom att skicka om mallen med uppdaterat egenskaps värde för genomflöde. |
|[Skapa ett Azure Cosmos-konto, en databas och en behållare med en lagrad procedur, utlösare och UDF](manage-sql-with-resource-manager.md#create-sproc) | Den här mallen skapar ett SQL (Core) API-konto i två regioner med en lagrad procedur, utlösare och UDF för en behållare. |

## <a name="mongodb-api"></a>MongoDB-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, databas, samling](manage-mongodb-with-resource-manager.md#create-resource) | Den här mallen skapar ett konto med hjälp av Azure Cosmos DB API för MongoDB i två regioner med stöd för flera huvud servrar. Azure Cosmos-kontot kommer att ha två behållare som delar data flöde på databas nivå. |
|[Uppdatera data flöde (RU/s) för en databas](manage-mongodb-with-resource-manager.md#database-ru-update) | Den här mallen uppdaterar data flödet för en databas i ett MongoDB API-konto. |
|[Uppdatera data flöde (RU/s) för en samling](manage-mongodb-with-resource-manager.md#collection-ru-update) | Den här mallen uppdaterar data flödet för en behållare i ett MongoDB API-konto. |

## <a name="cassandra-api"></a>Cassandra-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, ett tecken utrymme, en tabell](manage-cassandra-with-resource-manager.md#create-resource) | Den här mallen skapar ett API för Cassandra-konto i två regioner med stöd för flera huvud servrar. Azure Cosmos-kontot kommer att ha två tabeller som delar data flöde på grund av på grund av tecken utrymmes nivå. |
|[Uppdatera data flödet (RU/s) för ett tecken utrymme](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Den här mallen uppdaterar data flödet för ett disk utrymme i ett API för Cassandra-konto. |
|[Uppdatering av data flöde (RU/s) för en tabell](manage-cassandra-with-resource-manager.md#table-ru-update) | Den här mallen uppdaterar data flödet för en tabell i ett API för Cassandra-konto. |

## <a name="gremlin-api"></a>Gremlin-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, en databas, ett diagram](manage-gremlin-with-resource-manager.md#create-resource) | Den här mallen skapar ett Gremlin API-konto i två regioner med aktiverat multi-master. Azure Cosmos-kontot kommer att ha två grafer som delar data flöde på databas nivå. |
|[Uppdatera data flöde (RU/s) för en databas](manage-gremlin-with-resource-manager.md#database-ru-update) | Den här mallen uppdaterar data flödet för en databas i ett Gremlin API-konto. |
|[Uppdatera data flöde (RU/s) för en graf](manage-gremlin-with-resource-manager.md#graph-ru-update) | Den här mallen uppdaterar data flödet för en graf i ett Gremlin API-konto. |

## <a name="table-api"></a>Tabell-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, en tabell](manage-table-with-resource-manager.md#create-resource) | Den här mallen skapar ett Tabell-API-konto i två regioner med stöd för flera huvud servrar. Azure Cosmos-kontot har en enda tabell. |
|[Uppdatering av data flöde (RU/s) för en tabell](manage-table-with-resource-manager.md#table-ru-update) | Den här mallen uppdaterar data flödet för en tabell i ett Tabell-API-konto. |

> [!TIP]
> Aktivera delat data flöde när du använder Tabell-API genom att aktivera data flöde på konto nivå på Azure-portalen.

Se [arm-referens för Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) sida för referens dokumentationen.
