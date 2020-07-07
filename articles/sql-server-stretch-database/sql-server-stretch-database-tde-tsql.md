---
title: Aktivera transparent datakryptering för Stretch Database (T-SQL)
description: Aktivera transparent datakryptering (TDE) för SQL Server Stretch Database på Azure TSQL
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6f1f5f55348069dbfe11b4d5857d93f8ba8c9b19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74033961"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Aktivera transparent datakryptering (TDE) för Stretch Database på Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent datakryptering (TDE) skyddar mot hot mot skadlig aktivitet genom att utföra kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler i vila utan att det krävs några ändringar i programmet.

Transparent datakryptering (TDE) krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel kallad databaskrypteringsnyckeln. Databas krypterings nyckeln skyddas av ett inbyggt Server certifikat. Det inbyggda Server certifikatet är unikt för varje Azure-Server. Microsoft roterar dessa certifikat automatiskt minst var 90: e dag. En allmän beskrivning av TDE finns [Transparent datakryptering (TDE)].

## <a name="enabling-encryption"></a>Aktivera kryptering
Gör så här om du vill aktivera TDE för en Azure-databas som lagrar data som migrerats från en utsträckt SQL Server databas:

1. Anslut till *huvud* databasen på den Azure-server som är värd för databasen med hjälp av en inloggning som är administratör eller medlem i **DBManager** -rollen i huvud databasen
2. Kör följande instruktion för att kryptera-databasen.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Inaktiverar kryptering
Om du vill inaktivera TDE för en Azure-databas som lagrar data som migrerats från en utsträckt SQL Server databas gör du följande:

1. Anslut till *huvud* databasen med en inloggning som är administratör eller medlem i **DBManager** -rollen i huvud databasen
2. Kör följande instruktion för att kryptera-databasen.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Verifiera kryptering
Gör så här för att kontrol lera krypterings status för en Azure-databas som lagrar data som migrerats från en utsträckt SQL Server databas:

1. Anslut till *huvud* -eller instans databasen med hjälp av en inloggning som är administratör eller medlem i **DBManager** -rollen i huvud databasen
2. Kör följande instruktion för att kryptera-databasen.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Resultatet av en ```1``` krypterad databas ```0``` indikerar en icke-krypterad databas.

<!--Anchors-->
[Transparent datakryptering (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
