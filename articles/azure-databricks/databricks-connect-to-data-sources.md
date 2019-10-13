---
title: 'Anslut till olika data källor från Azure Databricks '
description: Lär dig hur du ansluter till Azure SQL Database, Azure Data Lake Store, Blob Storage, Cosmos DB, Event Hubs och Azure SQL Data Warehouse från Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: c77d1d1a66d3ee92f5ad3f2016d2160831fa3ad9
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299311"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Anslut till data källor från Azure Databricks

Den här artikeln innehåller länkar till alla olika data källor i Azure som kan anslutas till Azure Databricks. Följ exemplen i dessa länkar om du vill extrahera data från Azure-datakällor (till exempel Azure Blob Storage, Azure Event Hubs osv.) till ett Azure Databricks kluster och köra analytiska jobb på dem. 

## <a name="prerequisites"></a>Krav

* Du måste ha en Azure Databricks-arbetsyta och ett Spark-kluster. Följ anvisningarna i [Kom igång med Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Data källor för Azure Databricks

I följande lista visas de data källor i Azure som du kan använda med Azure Databricks. En fullständig lista över data källor som kan användas med Azure Databricks finns i [data källor för Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Azure SQL Database](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Den här länken ger DataFrame-API: et för att ansluta till SQL-databaser med JDBC och hur du styr den parallella läsningen med hjälp av JDBC-gränssnittet. Det här avsnittet innehåller detaljerade exempel med Scala-API: et, med förkortade python-och Spark SQL-exempel i slutet.
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)

    Den här länken innehåller exempel på hur du använder Azure Active Directory tjänstens huvud namn för att autentisera med Data Lake Store. Den innehåller också anvisningar om hur du kommer åt data i Data Lake Store från Azure Databricks.

- [Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Den här länken innehåller exempel på hur du direkt får åtkomst till Azure-Blob Storage från Azure Databricks med hjälp av åtkomst nyckeln eller SAS för en specifik behållare. Länken ger också information om hur du kommer åt Azure-Blob Storage från Azure Databricks med RDD-API: et.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Den här länken innehåller instruktioner för hur du använder [Azure Cosmos DB Spark-anslutningsprogrammet](https://github.com/Azure/azure-cosmosdb-spark) från Azure Databricks för att komma åt data i Azure Cosmos dB.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Den här länken innehåller instruktioner för hur du använder [azure Event Hubs Spark-anslutningsprogrammet](https://github.com/Azure/azure-event-hubs-spark) från Azure Databricks för att få åtkomst till data i Azure-Event Hubs.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Den här länken innehåller instruktioner för hur du använder Azure SQL Data Warehouse anslutning för att ansluta från Azure Databricks.
    

## <a name="next-steps"></a>Nästa steg

Information om källor där du kan importera data till Azure Databricks finns i [data källor för Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


