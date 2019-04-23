---
title: 'Ansluta till olika datakällor från Azure Databricks '
description: Lär dig hur du ansluter till olika datakällor från Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: f2b7136ec21416e31c2af658974577023a4494de
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997942"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Ansluta till datakällor från Azure Databricks

Den här artikeln innehåller länkar till alla olika datakällor i Azure som kan anslutas till Azure Databricks. Följ exemplen i dessa länkar för att extrahera data från Azure-datakällor (till exempel Azure Blob Storage, Azure Event Hubs osv.) till ett Azure Databricks-kluster och köra analytiska på dem. 

## <a name="prerequisites"></a>Nödvändiga komponenter

* Du måste ha en Azure Databricks-arbetsyta och ett Spark-kluster. Följ anvisningarna på [Kom igång med Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Datakällor för Azure Databricks

Följande lista innehåller datakällor i Azure som du kan använda med Azure Databricks. En fullständig lista över datakällor som kan användas med Azure Databricks finns [datakällor för Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Azure SQL-databas](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Den här länken tillhandahåller DataFrame API för att ansluta till SQL-databaser med JDBC och hur du hanterar parallellitet läsningar genom JDBC-gränssnittet. Det här avsnittet innehåller detaljerade exempel med hjälp av Scala-API med förkortade Python och Spark SQL-exempel i slutet.
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)

    Den här länken innehåller exempel på hur du använder Azure Active Directory-tjänstens huvudnamn för att autentisera med Data Lake Store. Den innehåller också anvisningar för hur du kommer åt data i Data Lake Store från Azure Databricks.

- [Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Den här länken innehåller exempel på hur för direkt åtkomst till Azure Blob Storage från Azure Databricks med åtkomstnyckel eller SAS för en given behållare. Länken innehåller även information om hur du kommer åt Azure Blob Storage från Azure Databricks med hjälp av RDD-API.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Den här länken innehåller instruktioner om hur du använder den [Azure Cosmos DB Spark connector](https://github.com/Azure/azure-cosmosdb-spark) från Azure Databricks för att komma åt data i Azure Cosmos DB.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Den här länken innehåller instruktioner om hur du använder den [Azure Event Hubs Spark connector](https://github.com/Azure/azure-event-hubs-spark) från Azure Databricks för att komma åt data i Azure Event Hubs.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Den här länken innehåller instruktioner om hur du använder Azure SQL Data Warehouse-anslutningen för att ansluta från Azure Databricks.
    

## <a name="next-steps"></a>Nästa steg

Läs mer om datakällor där du kan importera data till Azure Databricks, i [datakällor för Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


