---
title: Transparent datakryptering i SQL Data Warehouse (T-SQL) | Microsoft Docs
description: Transparent datakryptering (TDE) i SQL Data Warehouse (T-SQL)
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: d10b8f751d782f00cbc58274e4b48c501cea6f70
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Kom igång med Transparent Data kryptering (TDE)
> [!div class="op_single_selector"]
> * [Översikt över säkerheten](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>Nödvändig behörighet
Om du vill aktivera Transparent Data kryptering (TDE) måste du vara administratör eller medlem i rollen dbmanager.

## <a name="enabling-encryption"></a>Aktivera kryptering
Följ dessa steg om du vill aktivera TDE för ett SQL Data Warehouse:

1. Ansluta till den *master* databasen på den server som värd för databasen med en inloggning som administratör eller medlem i den **dbmanager** roll i master-databasen
2. Kör följande sats för att kryptera databasen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Om du inaktiverar kryptering
Följ dessa steg om du vill inaktivera TDE för ett SQL Data Warehouse:

1. Ansluta till den *master* databasen med en inloggning som är administratör eller medlem i den **dbmanager** roll i master-databasen
2. Kör följande sats för att kryptera databasen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Att måste återuppta ett pausat SQL Data Warehouse innan du ändrar inställningarna för TDE.
> 
> 

## <a name="verifying-encryption"></a>Verifiering av kryptering
Följ stegen nedan om du vill verifiera krypteringsstatus för en SQL Data Warehouse:

1. Ansluta till den *master* eller instans database med en inloggning som administratör eller medlem i den **dbmanager** roll i master-databasen
2. Kör följande sats för att kryptera databasen.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Ett resultat av ```1``` anger en krypterad databas ```0``` anger en icke-krypterad databas.

## <a name="encryption-dmvs"></a>Kryptering av DMV: er
* [sys.Databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
