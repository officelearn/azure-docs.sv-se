---
title: Azure Government databaser | Microsoft Docs
description: Detta ger en jämförelse av funktioner och vägledning om hur du utvecklar program för Azure Government
services: azure-government
cloud: gov
documentationcenter: ''
author: zakramer
manager: liki
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 03/06/2019
ms.author: zakramer
ms.openlocfilehash: cbf3d59bafa5b11be0fa037487dc64fdb9075619
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355864"
---
# <a name="azure-government-databases"></a>Azure Government databaser
## <a name="sql-database"></a>SQL Database
Mer information finns i<a href="https://msdn.microsoft.com/library/bb510589.aspx"> Microsoft Security Center för SQL Database motor</a> och [Azure SQL Database dokumentation](../sql-database/index.yml) för ytterligare vägledning om konfiguration av metadata synlighet och bästa metoder för skydd.

### <a name="variations"></a>Olika
SQL V12 Database är allmänt tillgänglig i Azure Government.

Adressen till SQL Azure servrar i Azure Government är annorlunda:

| Tjänsttyp | Azure Public | Azure Government |
| --- | --- | --- |
| SQL Database |*. database.windows.net |*. database.usgovcloudapi.net |

### <a name="considerations"></a>Överväganden
Följande information identifierar Azure Government gränser för Azure SQL:

| Reglerade/kontrollerade data tillåts | Reglerade/kontrollerade data tillåts inte |
| --- | --- |
| Alla data som lagras och bearbetas i Microsoft Azure SQL kan innehålla Azure Government-reglerade data. Använd databas verktyg för data överföring av Azure Government-reglerade data. |Azure SQL-metadata får inte innehålla exporterade data. Dessa metadata innehåller alla konfigurations data som angavs när du skapar och underhåller din lagrings produkt.  Ange inte reglerade/kontrollerade data i följande fält: databas namn, prenumerations namn, resurs grupper, Server namn, inloggning för Server administratör, distributions namn, resurs namn, resurs koder |

## <a name="sql-data-warehouse"></a>SQL Data Warehouse
Mer information om den här tjänsten och hur du använder den finns i [Azure SQL Data Warehouse-dokumentationen](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

## <a name="sql-server-stretch-database"></a>SQL Server Stretch Database
Mer information om den här tjänsten och hur du använder den finns i [Azure SQL Server Stretch Database-dokumentationen](../sql-server-stretch-database/index.md)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
Mer information om den här tjänsten och hur du använder den finns i [Azure Cosmos DB-dokumentationen](../cosmos-db/index.yml).

### <a name="variations"></a>Olika
Azure Cosmos DB är allmänt tillgänglig i Azure Government med paritet till den offentliga versionen. Ett undantag är att **lägga till Azure kognitiv sökning** -funktionen för närvarande, som inte är tillgänglig i Cosmos DB för Azure Government.

URL: er för att komma åt Cosmos DB i Azure Government är också olika:

| Tjänsttyp | Azure Public | Azure Government |
| --- | --- | --- |
| Cosmos DB | *. documents.azure.com | *. documents.azure.us |


### <a name="considerations"></a>Överväganden
Följande information identifierar Azure Government gränser för Azure Cosmos DB:

| Reglerade/kontrollerade data tillåts | Reglerade/kontrollerade data tillåts inte |
| --- | --- |
| Alla data som lagras och bearbetas i Azure Cosmos DB kan innehålla Azure Government-reglerade data. |Azure Cosmos DB metadata får inte innehålla exporterade data. Ange inte reglerade/kontrollerade data i följande fält: **db-namn, prenumerations namn, resurs grupper, resurs Taggar**. |


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Mer information om den här tjänsten och hur du använder den finns i [Azure cache för Redis-dokumentation](../azure-cache-for-redis/index.yml).

### <a name="variations"></a>Olika
URL: erna för åtkomst och hantering av Azure-cache för Redis i Azure Government skiljer sig åt:

| Tjänsttyp | Azure Public | Azure Government |
| --- | --- | --- |
| Cache-slutpunkt |*.redis.cache.windows.net |*.redis.cache.usgovcloudapi.net |

> [!NOTE]
> Alla skript och all kod måste beaktas för lämpliga slut punkter och miljöer. Mer information finns i [så här ansluter du till andra moln](../azure-cache-for-redis/cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).
>
>

### <a name="considerations"></a>Överväganden
Följande information identifierar Azure Government gränser för Azure cache för Redis:

| Reglerade/kontrollerade data tillåts | Reglerade/kontrollerade data tillåts inte |
| --- | --- |
| Alla data som lagras och bearbetas i Azure cache för Redis kan innehålla Azure Government-reglerade data. |Azure cache för Redis metadata får inte innehålla exporterade data. Ange inte reglerade/kontrollerade data i följande fält: **cache-namn, prenumerations namn, resurs grupper, resurs taggar, Redis-egenskaper**. |

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Mer information om den här tjänsten och hur du använder den finns i [Azure Database for PostgreSQL-dokumentationen](../postgresql/index.yml).

### <a name="variations"></a>Olika
Avancerat skydd, frågor om prestanda insikter och prestanda rekommendationer för Azure Database for PostgreSQL är **inte** tillgängliga i Azure Government.

URL: erna för åtkomst och hantering av Azure Database for PostgreSQL i Azure Government är olika:

| Tjänsttyp | Azure Public | Azure Government |
| --- | --- | --- |
| PostgreSQL-slutpunkt |*. postgres.database.azure.com |*. postgres.database.usgovcloudapi.net |

### <a name="considerations"></a>Överväganden
Följande information identifierar Azure Government gränser för Azure Database for PostgreSQL:

| Reglerade/kontrollerade data tillåts | Reglerade/kontrollerade data tillåts inte |
| --- | --- |
| Alla data som lagras och bearbetas i Azure Database for PostgreSQL kan innehålla Azure Government-reglerade data. Använd databas verktyg för data överföring av Azure Government-reglerade data. |Azure Database for PostgreSQL metadata får inte innehålla exporterade data. Dessa metadata innehåller alla konfigurations data som angavs när du skapar och underhåller din lagrings produkt.  Ange inte reglerade/kontrollerade data i följande fält: databas namn, prenumerations namn, resurs grupper, Server namn, inloggning för Server administratör, distributions namn, resurs namn, resurs koder. |

## <a name="azure-database-for-mariadb"></a>Azure-databas för MariaDB
Mer information om den här tjänsten och hur du använder den finns i [Azure Database for MariaDB-dokumentationen](../mariadb/index.yml).

### <a name="variations"></a>Olika
Fråga prestanda insikter och prestanda rekommendationer för Azure Database for MariaDB är **inte** tillgängliga i Azure Government.

URL: erna för åtkomst och hantering av Azure Database for MariaDB i Azure Government är olika:

| Tjänsttyp | Azure Public | Azure Government |
| --- | --- | --- |
| MariaDB-slutpunkt |*. mariadb.database.azure.com |*. mariadb.database.usgovcloudapi.net |

### <a name="considerations"></a>Överväganden
Följande information identifierar Azure Government gränser för Azure Database for MariaDB:

| Reglerade/kontrollerade data tillåts | Reglerade/kontrollerade data tillåts inte |
| --- | --- |
| Alla data som lagras och bearbetas i Azure Database for MariaDB kan innehålla Azure Government-reglerade data. Använd databas verktyg för data överföring av Azure Government-reglerade data. |Azure Database for MariaDB metadata får inte innehålla exporterade data. Dessa metadata innehåller alla konfigurations data som angavs när du skapar och underhåller din lagrings produkt.  Ange inte reglerade/kontrollerade data i följande fält: databas namn, prenumerations namn, resurs grupper, Server namn, inloggning för Server administratör, distributions namn, resurs namn, resurs koder. |

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Mer information om den här tjänsten och hur du använder den finns i [Azure Database for MySQL-dokumentationen](../mysql/index.yml).

### <a name="variations"></a>Olika
Avancerat skydd, frågor om prestanda insikter och prestanda rekommendationer för Azure Database for MySQL är **inte** tillgängliga i Azure Government.

URL: erna för åtkomst och hantering av Azure Database for MySQL i Azure Government är olika:

| Tjänsttyp | Azure Public | Azure Government |
| --- | --- | --- |
| MySQL-slutpunkt |*. mysql.database.azure.com |*. mysql.database.usgovcloudapi.net |

### <a name="considerations"></a>Överväganden
Följande information identifierar Azure Government gränser för Azure Database for MySQL:

| Reglerade/kontrollerade data tillåts | Reglerade/kontrollerade data tillåts inte |
| --- | --- |
| Alla data som lagras och bearbetas i Azure Database for MySQL kan innehålla Azure Government-reglerade data. Använd databas verktyg för data överföring av Azure Government-reglerade data. |Azure Database for MySQL metadata får inte innehålla exporterade data. Dessa metadata innehåller alla konfigurations data som angavs när du skapar och underhåller din lagrings produkt.  Ange inte reglerade/kontrollerade data i följande fält: databas namn, prenumerations namn, resurs grupper, Server namn, inloggning för Server administratör, distributions namn, resurs namn, resurs koder. |

## <a name="next-steps"></a>Nästa steg
För kompletterande information och uppdateringar prenumererar du på <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government blogg.</a>
