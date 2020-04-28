---
title: Azure Resource Manager-mallar för Azure Cosmos DB
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 14c3438c5d6e7bed110f9e0dbfcd04655c3e9121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193266"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-mallar för Azure Cosmos DB

Följande tabeller innehåller länkar till Azure Resource Manager mallar för Azure Cosmos DB:

## <a name="sql-core-api"></a>SQL-API (Core)

|**Mall**|**Beskrivning**|
|---|---|
|[Skapa ett Azure Cosmos-konto, en databas, en behållare](manage-sql-with-resource-manager.md#create-resource) | Den här mallen skapar ett SQL (Core) API-konto i två regioner med två behållare med data flöde för delad databas och en behållare med dedikerat data flöde. Data flödet kan uppdateras genom att skicka om mallen med uppdaterat egenskaps värde för genomflöde. |
|[Skapa ett Azure Cosmos-konto, en databas och en behållare med en lagrad procedur, utlösare och UDF](manage-sql-with-resource-manager.md#create-sproc) | Den här mallen skapar ett SQL (Core) API-konto i två regioner med en lagrad procedur, utlösare och UDF för en behållare. |
|[Skapa en privat slut punkt för ett befintligt Azure Cosmos-konto](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Den här mallen skapar en privat slut punkt för ett befintligt Azure Cosmos SQL API-konto i ett befintligt virtuellt nätverk. |
|[Skapa ett Azure Cosmos-konto på den kostnads fria nivån](manage-sql-with-resource-manager.md#free-tier) |  Den här mallen skapar ett Azure Cosmos DB-konto på den kostnads fria nivån. |

## <a name="mongodb-api"></a>MongoDB-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, databas, samling](manage-mongodb-with-resource-manager.md#create-resource) | Den här mallen skapar ett konto med hjälp av Azure Cosmos DB API för MongoDB i två regioner med stöd för flera huvud servrar. Azure Cosmos-kontot kommer att ha två behållare som delar data flöde på databas nivå. |

## <a name="cassandra-api"></a>Cassandra-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, ett tecken utrymme, en tabell](manage-cassandra-with-resource-manager.md#create-resource) | Den här mallen skapar ett API för Cassandra-konto i två regioner med stöd för flera huvud servrar. Azure Cosmos-kontot kommer att ha två tabeller som delar data flöde på grund av på grund av tecken utrymmes nivå. |

## <a name="gremlin-api"></a>Gremlin-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, en databas, ett diagram](manage-gremlin-with-resource-manager.md#create-resource) | Den här mallen skapar ett Gremlin API-konto i två regioner med aktiverat multi-master. Azure Cosmos-kontot kommer att ha två grafer som delar data flöde på databas nivå. |

## <a name="table-api"></a>Tabell-API

|**Mall**|**Beskrivning**|
|---| ---|
|[Skapa ett Azure Cosmos-konto, en tabell](manage-table-with-resource-manager.md#create-resource) | Den här mallen skapar ett Tabell-API-konto i två regioner med stöd för flera huvud servrar. Azure Cosmos-kontot har en enda tabell. |

> [!TIP]
> Aktivera delat data flöde när du använder Tabell-API genom att aktivera data flöde på konto nivå på Azure-portalen.

Se [Azure Resource Manager referens för Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) sidan för referens dokumentationen.
