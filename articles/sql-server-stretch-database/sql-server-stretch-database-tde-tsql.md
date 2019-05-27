---
title: Aktivera Transparent datakryptering för Stretch Database TSQL - Azure | Microsoft Docs
description: Aktivera Transparent datakryptering (TDE) för SQL Server Stretch Database på Azure TSQL
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
ms.openlocfilehash: 9718db18ea675fa744262f0736aff3c07732e1d1
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002878"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Aktivera Transparent datakryptering (TDE) för Stretch Database på Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent datakryptering (TDE) skyddar mot skadlig aktivitet genom att utföra i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet.

TDE krypterar lagring av en hel databas med hjälp av en symmetrisk nyckel som heter databaskrypteringsnyckeln. Databaskrypteringsnyckeln skyddas av ett certifikat för inbyggda. Inbyggda servercertifikatet är unikt för varje Azure-server. Microsoft roterar dessa certifikat automatiskt minst var 90: e dag. En allmän beskrivning av TDE Se [Transparent datakryptering (TDE)].

## <a name="enabling-encryption"></a>Aktivera kryptering
Om du vill aktivera transparent Datakryptering för en Azure-databas som lagrar data som har migrerats från en Stretch-aktiverade SQL Server-databas, gör du följande:

1. Ansluta till den *master* databas på Azure-servern som är värd för databasen med en inloggning som är en administratör eller medlem i den **dbmanager** roll i master-databasen
2. Kör följande instruktion för att kryptera databasen.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Inaktivering av kryptering
Om du vill inaktivera TDE för en Azure-databas som lagrar data som migrerats från en Stretch-aktiverade SQL Server-databas, gör du följande:

1. Ansluta till den *master* databasen med en inloggning som är en administratör eller medlem i den **dbmanager** roll i master-databasen
2. Kör följande instruktion för att kryptera databasen.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Verifiera kryptering
Kontrollera krypteringsstatus för en Azure-databas som lagrar data som har migrerats från en Stretch-aktiverade SQL Server-databas genom att göra följande:

1. Ansluta till den *master* eller instans-databas med en inloggning som är en administratör eller medlem i den **dbmanager** roll i master-databasen
2. Kör följande instruktion för att kryptera databasen.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Ett resultat av ```1``` anger en krypterad databas ```0``` anger en icke-krypterade databas.

<!--Anchors-->
[Transparent datakryptering (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
