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
ms.openlocfilehash: 80ec9b2bbf10d8f500e7f9e0369faca3b9663633
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129382"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Anslut till data källor från Azure Databricks

Den här artikeln innehåller länkar till alla olika data källor i Azure som kan anslutas till Azure Databricks. Följ exemplen i dessa länkar om du vill extrahera data från Azure-datakällor (till exempel Azure Blob Storage, Azure Event Hubs osv.) till ett Azure Databricks kluster och köra analytiska jobb på dem. 

## <a name="prerequisites"></a>Krav

* Du måste ha en Azure Databricks-arbetsyta och ett Spark-kluster. Följ anvisningarna i [Kom igång med Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Data källor för Azure Databricks

I följande lista visas de data källor i Azure som du kan använda med Azure Databricks. En fullständig lista över data källor som kan användas med Azure Databricks finns i [data källor för Azure Databricks](/azure/databricks/data/data-sources/index).

- [Azure SQL Database](/azure/databricks/data/data-sources/sql-databases)

    Den här länken ger DataFrame-API: et för att ansluta till SQL-databaser med JDBC och hur du styr den parallella läsningen med hjälp av JDBC-gränssnittet. Det här avsnittet innehåller detaljerade exempel med Scala-API: et, med förkortade python-och Spark SQL-exempel i slutet.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Den här länken innehåller exempel på hur du använder Azure Active Directory tjänstens huvud namn för att autentisera med Azure Data Lake Storage. Den innehåller också anvisningar om hur du kommer åt data i Azure Data Lake Storage från Azure Databricks.

- [Azure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage)

    Den här länken innehåller exempel på hur du direkt får åtkomst till Azure-Blob Storage från Azure Databricks med hjälp av åtkomst nyckeln eller SAS för en specifik behållare. Länken ger också information om hur du kommer åt Azure-Blob Storage från Azure Databricks med RDD-API: et.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Den här länken innehåller instruktioner för hur du använder [Azure Cosmos DB Spark-anslutningsprogrammet](https://github.com/Azure/azure-cosmosdb-spark) från Azure Databricks för att komma åt data i Azure Cosmos dB.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    Den här länken innehåller instruktioner för hur du använder [azure Event Hubs Spark-anslutningsprogrammet](https://github.com/Azure/azure-event-hubs-spark) från Azure Databricks för att få åtkomst till data i Azure-Event Hubs.

- [Azure SQL Data Warehouse](/azure/databricks/data/data-sources/azure/sql-data-warehouse)

    Den här länken innehåller instruktioner för hur du använder Azure SQL Data Warehouse anslutning för att ansluta från Azure Databricks.
    

## <a name="next-steps"></a>Nästa steg

Information om källor där du kan importera data till Azure Databricks finns i [data källor för Azure Databricks](/azure/databricks/data/data-sources/index).


