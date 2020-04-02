---
title: 'Ansluta till olika datakällor från Azure Databricks '
description: Lär dig hur du ansluter till Azure SQL Database, Azure Data Lake Store, blob storage, Cosmos DB, Event Hubs och Azure SQL Data Warehouse från Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 79a821a4c8fe4cb2d048f0dcb0a6e091462a1779
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548796"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Ansluta till datakällor från Azure Databricks

Den här artikeln innehåller länkar till alla olika datakällor i Azure som kan anslutas till Azure Databricks. Följ exemplen i dessa länkar för att extrahera data från Azure-datakällor (till exempel Azure Blob Storage, Azure Event Hubs, etc.) i ett Azure Databricks-kluster och kör analytiska jobb på dem. 

## <a name="prerequisites"></a>Krav

* Du måste ha en Azure Databricks-arbetsyta och ett Spark-kluster. Följ instruktionerna på [Kom igång med Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Datakällor för Azure Databricks

Följande lista innehåller datakällor i Azure som du kan använda med Azure Databricks. En fullständig lista över datakällor som kan användas med Azure Databricks finns i [Datakällor för Azure Databricks](/azure/databricks/data/data-sources/index).

- [Azure SQL-databas](/azure/databricks/data/data-sources/sql-databases)

    Den här länken innehåller DataFrame API för anslutning till SQL-databaser med JDBC och hur du styr parallellismen av läsningar via JDBC-gränssnittet. Det här avsnittet innehåller detaljerade exempel med Scala API, med förkortade Python och Spark SQL-exempel i slutet.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Den här länken innehåller exempel på hur du använder huvudhuvudet för Azure Active Directory-tjänsten för att autentisera med Azure Data Lake Storage. Den innehåller också instruktioner om hur du kommer åt data i Azure Data Lake Storage från Azure Databricks.

- [Azure Blob-lagring](/azure/databricks/data/data-sources/azure/azure-storage)

    Den här länken innehåller exempel på hur du direkt kommer åt Azure Blob Storage från Azure Databricks med åtkomstnyckeln eller SAS för en viss behållare. Länken innehåller också information om hur du kommer åt Azure Blob Storage från Azure Databricks med hjälp av RDD API.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Den här länken innehåller instruktioner om hur du använder [Azure Cosmos DB Spark-kopplingen](https://github.com/Azure/azure-cosmosdb-spark) från Azure Databricks för att komma åt data i Azure Cosmos DB.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    Den här länken innehåller instruktioner om hur du använder [Azure Event Hubs Spark-kopplingen](https://github.com/Azure/azure-event-hubs-spark) från Azure Databricks för att komma åt data i Azure Event Hubs.

- [Azure SQL-datalager](/azure/synapse-analytics/sql-data-warehouse/)

    Den här länken innehåller instruktioner om hur du använder Azure SQL Data Warehouse-kopplingen för att ansluta från Azure Databricks.
    

## <a name="next-steps"></a>Nästa steg

Mer information om källor där du kan importera data till Azure Databricks finns i [Datakällor för Azure Databricks](/azure/databricks/data/data-sources/index).


