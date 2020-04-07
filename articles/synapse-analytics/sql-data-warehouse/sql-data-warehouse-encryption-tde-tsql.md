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
ms.openlocfilehash: ae751cc5b8e3ab67f3e65757724d0ebae1c45e02
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745245"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Komma igång med transparent datakryptering (TDE)

> [!div class="op_single_selector"]
>
> * [Översikt över säkerhet](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

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

* [sys.databaser](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
