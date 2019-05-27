---
title: Azure Resource Manager-mallar för Azure Cosmos DB
description: Använd Azure Resource Manager-mallar för att skapa och konfigurera Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 4a32b0497d2457a740e9c082f990bb9112208bfd
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969176"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-mallar för Azure Cosmos DB

Följande tabeller innehåller länkar till Azure Resource Manager-mallar för Azure Cosmos DB:

## <a name="sql-core-api"></a>SQL (Core) API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, databas, behållare](manage-sql-with-resource-manager.md#create-resource) | Den här mallen skapar en SQL (kärna) API-konto i två regioner med Multi-Master aktiverat. Azure Cosmos-konto har två behållare som delar på databasnivå dataflöde. |
|[Uppdatera dataflöde (RU/s) för en databas](manage-sql-with-resource-manager.md#database-ru-update) | Den här mallen uppdaterar dataflödet för en databas i en SQL (kärna) API-konto. |
|[Uppdatera dataflöde (RU/s) för en behållare](manage-sql-with-resource-manager.md#container-ru-update) | Den här mallen uppdaterar dataflödet för en behållare i ett SQL (kärna) API-konto. |

## <a name="mongodb-api"></a>MongoDB-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, databas och samling](manage-mongodb-with-resource-manager.md#create-resource) | Den här mallen skapar ett konto med hjälp av Azure Cosmos DB API för MongoDB i två regioner med Multi-Master aktiverat. Azure Cosmos-konto har två behållare som delar på databasnivå dataflöde. |
|[Uppdatera dataflöde (RU/s) för en databas](manage-mongodb-with-resource-manager.md#database-ru-update) | Den här mallen uppdaterar dataflödet för en databas i en MongoDB API-konto. |
|[Uppdatera dataflöde (RU/s) för en samling](manage-mongodb-with-resource-manager.md#collection-ru-update) | Den här mallen uppdaterar dataflödet för en behållare i ett MongoDB API-konto. |

## <a name="cassandra-api"></a>Cassandra-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, keyspace, tabell](manage-cassandra-with-resource-manager.md#create-resource) | Den här mallen skapar en Cassandra API-konto i två regioner med Multi-Master aktiverat. Azure Cosmos-kontot måste två tabeller som delar keyspace på servernivå dataflöde. |
|[Uppdatera dataflöde (RU/s) för ett keyspace](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Den här mallen uppdaterar dataflödet för ett keyspace i ett Cassandra API-konto. |
|[Uppdatera dataflöde (RU/s) för en tabell](manage-cassandra-with-resource-manager.md#table-ru-update) | Den här mallen uppdaterar dataflödet för en tabell i ett Cassandra-API-konto. |

## <a name="gremlin-api"></a>Gremlin-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, databas, graph](manage-gremlin-with-resource-manager.md#create-resource) | Den här mallen skapar ett Gremlin-API-konto i två regioner med Multi-Master aktiverat. Azure Cosmos-konto har två diagram som delar på databasnivå dataflöde. |
|[Uppdatera dataflöde (RU/s) för en databas](manage-gremlin-with-resource-manager.md#database-ru-update) | Den här mallen uppdaterar dataflödet för en databas i ett Gremlin-API-konto. |
|[Uppdatera dataflöde (RU/s) för ett diagram](manage-gremlin-with-resource-manager.md#graph-ru-update) | Den här mallen uppdaterar dataflödet för ett diagram i ett Gremlin-API-konto. |

## <a name="table-api"></a>Tabell-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, tabell](manage-table-with-resource-manager.md#create-resource) | Den här mallen skapar ett tabell-API-konto i två regioner med Multi-Master aktiverat. Azure Cosmos-kontot måste en enda tabell. |
|[Uppdatera dataflöde (RU/s) för en tabell](manage-table-with-resource-manager.md#table-ru-update) | Den här mallen uppdaterar dataflödet för en tabell i ett tabell-API-konto. |

> [!TIP]
> Om du vill aktivera delad dataflöde när du använder tabell-API, aktivera kontonivå dataflöde i Azure Portal.

Se [ARM-referens för Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) för referensdokumentationen.