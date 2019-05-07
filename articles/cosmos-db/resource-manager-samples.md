---
title: Azure Resource Manager-mallar för Azure Cosmos DB
description: Använd Azure Resource Manager-mallar för att skapa och konfigurera Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: c907de019b64a6a9d03206514a1147fd43c78106
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078467"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-mallar för Azure Cosmos DB

I följande tabell innehåller länkar till Azure Resource Manager-mallar för Azure Cosmos DB:

|**API-typ** | **länka till exempel**| **Beskrivning** |
|---|---| ---|
|Coreapi (SQL)| [Skapa ett Azure Cosmos DB-konto (multimaster)](manage-sql-with-resource-manager.md) | Den här mallen skapar ett SQL API-konto i två regioner med Multi-Master aktiverat. Azure Cosmos-konto har två behållare som delar på databasnivå dataflöde. |
| MongoDB-API | [Skapa ett Azure Cosmos DB-konto (multimaster)](manage-mongodb-with-resource-manager.md) | Den här mallen skapar ett konto med hjälp av Azure Cosmos DB API för MongoDB i två regioner med Multi-Master aktiverat. Azure Cosmos-konto har två behållare som delar på databasnivå dataflöde. |
| Cassandra-API | [Skapa ett Azure Cosmos DB-konto (multimaster)](manage-cassandra-with-resource-manager.md) | Den här mallen skapar en Cassandra API-konto i två regioner med Multi-Master aktiverat. Azure Cosmos-kontot måste två tabeller som delar keyspace på servernivå dataflöde. |
| Gremlin-API| [Skapa ett Azure Cosmos DB-konto (multimaster)](manage-gremlin-with-resource-manager.md) | Den här mallen skapar ett Gremlin-API-konto i två regioner med Multi-Master aktiverat. Azure Cosmos-konto har två diagram som delar på databasnivå dataflöde. |
| Tabell-API | [Skapa ett Azure Cosmos DB-konto (multimaster)](manage-table-with-resource-manager.md) | Den här mallen skapar ett tabell-API-konto i två regioner med Multi-Master aktiverat. Azure Cosmos-kontot måste en enda tabell. |

> [!TIP]
> Om du vill aktivera delad dataflöde när du använder tabell-API, aktivera kontonivå dataflöde i Azure Portal.

Se [ARM-referens för Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) för referensdokumentationen.