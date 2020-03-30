---
title: Transparent datakryptering (T-SQL)
description: Transparent datakryptering (TDE) i Azure Synapse Analytics (T-SQL)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 14058a91ee21bd792f9be1a64d554092c4f5aedd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351512"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Komma igång med transparent datakryptering (TDE)
> [!div class="op_single_selector"]
> * [Översikt över säkerhet](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Nödvändiga behörigheter
Om du vill aktivera Transparent datakryptering (TDE) måste du vara administratör eller medlem i rollen dbmanager.

## <a name="enabling-encryption"></a>Aktivera kryptering
Så här aktiverar du TDE:

1. Ansluta till *huvuddatabasen* på servern som är värd för databasen med hjälp av en inloggning som är administratör eller medlem i **dbmanager-rollen** i huvuddatabasen
2. Kör följande utdrag för att kryptera databasen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Inaktivera kryptering
Så här inaktiverar du TDE:

1. Ansluta till *huvuddatabasen* med hjälp av en inloggning som är administratör eller medlem i **rollen dbmanager** i huvuddatabasen
2. Kör följande utdrag för att kryptera databasen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> En pausad SQL-pool måste återupptas innan du gör ändringar i TDE-inställningarna.
> 
> 

## <a name="verifying-encryption"></a>Verifiera kryptering
Så här verifierar du krypteringsstatus:

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

## <a name="encryption-dmvs"></a>DVS för kryptering
* [sys.databaser][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
