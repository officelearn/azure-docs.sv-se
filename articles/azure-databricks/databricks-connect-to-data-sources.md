---
title: Ansluta till olika datakällor från Azure Databricks | Microsoft Docs
description: Lär dig mer om att ansluta till olika datakällor från Azure Databricks.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: nitinme
ms.openlocfilehash: 865313a7c6eabd847529b88ff5fff0b7db438fa5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30174279"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Ansluta till datakällor från Azure Databricks

Den här artikeln innehåller länkar till alla olika datakällor i Azure som kan anslutas till Azure Databricks. Följ exemplen i dessa länkar för att extrahera data från Azure-datakällor (till exempel Azure Blob Storage, Azure Event Hubs, etc.) i ett kluster med Azure Databricks och köra analytiska på dem. 

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha en Azure Databricks arbetsyta och ett Spark-kluster. Följ anvisningarna på [Kom igång med Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Datakällor för Azure Databricks

Följande lista innehåller datakällor i Azure som du kan använda med Azure Databricks. En fullständig lista över datakällor som kan användas med Azure Databricks finns [datakällor för Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Azure SQL-databas](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Den här länken innehåller DataFrame-API för att ansluta till SQL-databaser med JDBC och hur du styr parallellitet läsningar genom JDBC-gränssnitt. Det här avsnittet innehåller exempel på detaljerade med hjälp av Scala-API med förkortade Python och Spark SQL-exempel i slutet.
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html)

    Den här länken innehåller exempel på hur du använder Azure Active Directory-tjänstens huvudnamn för att autentisera med Data Lake Store. Det ger också instruktioner om hur du åt data i Data Lake Store från Azure Databricks.

- [Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Den här länken innehåller exempel på hur direkt åtkomst till Azure Blob Storage från Azure Databricks med åtkomst till nyckeln eller SAS för en viss behållare. Länken innehåller också information om hur du åtkomst till Azure Blob Storage från Azure Databricks med RDD-API.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Den här länken innehåller instruktioner om hur du använder den [Azure Cosmos DB Spark connector](https://github.com/Azure/azure-cosmosdb-spark) från Azure Databricks att komma åt data i Azure Cosmos-databasen.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Den här länken innehåller instruktioner om hur du använder den [Azure Event Hubs Spark connector](https://github.com/Azure/azure-event-hubs-spark) från Azure Databricks att komma åt data i Händelsehubbar i Azure.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Den här länken innehåller instruktioner om hur du använder Azure SQL Data Warehouse connector för att ansluta från Azure Databricks.
    

## <a name="next-steps"></a>Nästa steg

Läs om datakällor från där du kan importera data till Azure Databricks i [datakällor för Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


