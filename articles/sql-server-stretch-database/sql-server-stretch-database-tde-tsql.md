---
title: Aktivera transparent datakryptering för T-SQL (Transparent datakryptering)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033961"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Aktivera transparent datakryptering (TDE) för Stretch Database på Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure-portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL (TSQL)](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent datakryptering (TDE) hjälper till att skydda mot hotet om skadlig aktivitet genom att utföra kryptering i realtid och dekryptering av databasen, associerade säkerhetskopior och transaktionsloggfiler i vila utan att kräva ändringar i programmet.

Transparent datakryptering (TDE) krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel kallad databaskrypteringsnyckeln. Krypteringsnyckeln för databasen skyddas av ett inbyggt servercertifikat. Det inbyggda servercertifikatet är unikt för varje Azure-server. Microsoft roterar automatiskt dessa certifikat minst var 90:e dag. En allmän beskrivning av TDE finns i [Transparent datakryptering (TDE)].

## <a name="enabling-encryption"></a>Aktivera kryptering
Så här aktiverar du TDE för en Azure-databas som lagrar data som migreras från en Stretch-aktiverad SQL Server-databas:

1. Ansluta till *huvuddatabasen* på Azure-servern som är värd för databasen med hjälp av en inloggning som är administratör eller medlem i **dbmanager-rollen** i huvuddatabasen
2. Kör följande utdrag för att kryptera databasen.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Inaktivera kryptering
Så här inaktiverar du TDE för en Azure-databas som lagrar data som migreras från en Stretch-aktiverad SQL Server-databas:

1. Ansluta till *huvuddatabasen* med hjälp av en inloggning som är administratör eller medlem i **rollen dbmanager** i huvuddatabasen
2. Kör följande utdrag för att kryptera databasen.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Verifiera kryptering
Så här verifierar du krypteringsstatus för en Azure-databas som lagrar data som migreras från en Stretch-aktiverad SQL Server-databas:

1. Ansluta till *huvuddatabasen* eller instansdatabasen med hjälp av en inloggning som är administratör eller medlem i **dbmanager-rollen** i huvuddatabasen
2. Kör följande utdrag för att kryptera databasen.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Ett resultat ```1``` av indikerar en ```0``` krypterad databas, indikerar en icke-krypterad databas.

<!--Anchors-->
[Transparent datakryptering (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
