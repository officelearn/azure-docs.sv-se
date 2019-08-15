---
title: Transparent datakryptering i SQL Data Warehouse (portal) | Microsoft Docs
description: Transparent datakryptering (TDE) i SQL Data Warehouse
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: kavithaj
ms.reviewer: rortloff
ms.openlocfilehash: e756049110f7d4a81950abf6ebbe73edb3e3ca0a
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "65143167"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Kom igång med transparent datakryptering (TDE) i SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Säkerhets översikt](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Nödvändiga behörigheter
Om du vill aktivera transparent datakryptering (TDE) måste du vara administratör eller medlem i DBManager-rollen.

## <a name="enabling-encryption"></a>Aktivera kryptering
Följ stegen nedan om du vill aktivera TDE för en SQL Data Warehouse:

1. Öppna databasen i [Azure Portal](https://portal.azure.com)
2. Klicka på knappen **Inställningar** i bladet databas
3. Välj alternativet **transparent data kryptering**![][1]
4. Välj inställningen **vid**![][2]
5. Välj **Spara**
   ![][3]  

## <a name="disabling-encryption"></a>Inaktiverar kryptering
Om du vill inaktivera TDE för en SQL Data Warehouse följer du stegen nedan:

1. Öppna databasen i [Azure Portal](https://portal.azure.com)
2. Klicka på knappen **Inställningar** i bladet databas
3. Välj alternativet **transparent data kryptering**![][1]
4. Välj **off** -inställningen![][4]
5. Välj **Spara**
   ![][5]  

## <a name="encryption-dmvs"></a>Kryptering DMV: er
Kryptering kan bekräftas med följande DMV: er:

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
