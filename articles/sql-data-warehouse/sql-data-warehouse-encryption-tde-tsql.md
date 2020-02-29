---
title: Transparent data kryptering (T-SQL)
description: Transparent data kryptering (TDE) i Azure Synapse Analytics (T-SQL)
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: bd6f40b8389284c1932a2f16a70060cd56e412fb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195813"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Kom igång med transparent datakryptering (TDE)
> [!div class="op_single_selector"]
> * [Säkerhets översikt](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Behörigheter som krävs
Om du vill aktivera transparent datakryptering (TDE) måste du vara administratör eller medlem i DBManager-rollen.

## <a name="enabling-encryption"></a>Aktivera kryptering
Följ de här stegen för att aktivera TDE:

1. Anslut till *huvud* databasen på den server som är värd för databasen med en inloggning som är administratör eller medlem i **DBManager** -rollen i huvud databasen
2. Kör följande instruktion för att kryptera-databasen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Inaktiverar kryptering
Följ dessa steg om du vill inaktivera TDE:

1. Anslut till *huvud* databasen med en inloggning som är administratör eller medlem i **DBManager** -rollen i huvud databasen
2. Kör följande instruktion för att kryptera-databasen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> En pausad SQL-pool måste återupptas innan du gör ändringar i TDE-inställningarna.
> 
> 

## <a name="verifying-encryption"></a>Verifiera kryptering
Följ stegen nedan för att kontrol lera krypterings status:

1. Anslut till *huvud* -eller instans databasen med hjälp av en inloggning som är administratör eller medlem i **DBManager** -rollen i huvud databasen
2. Kör följande instruktion för att kryptera-databasen.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Ett resultat av ```1``` indikerar en krypterad databas, ```0``` anger en icke-krypterad databas.

## <a name="encryption-dmvs"></a>Kryptering DMV: er
* [sys. databases][sys.databases] 
* [sys. dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
