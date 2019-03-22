---
title: Transparent datakryptering i SQL Data Warehouse (Portal) | Microsoft Docs
description: Transparent datakryptering (TDE) i SQL Data Warehouse
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 477123ec6696ea44b1fea16689d7f1961bde4b61
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840587"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Kom igång med Transparent datakryptering (TDE) i SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Säkerhetsöversikt](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Nödvändiga behörigheter
Om du vill aktivera Transparent datakryptering (TDE), måste du vara administratör eller medlem i rollen dbmanager.

## <a name="enabling-encryption"></a>Aktivera kryptering
Följ stegen nedan om du vill aktivera transparent Datakryptering för ett SQL Data Warehouse:

1. Öppna databasen i den [Azure-portalen](https://portal.azure.com)
2. Databasbladet klickar du på den **inställningar** knappen
3. Välj den **Transparent datakryptering** alternativet ![][1]
4. Välj den **på** inställningen ![][2]
5. Välj **spara**
   ![][3]  

## <a name="disabling-encryption"></a>Inaktivering av kryptering
Följ stegen nedan om du vill inaktivera TDE för ett SQL Data Warehouse:

1. Öppna databasen i den [Azure-portalen](https://portal.azure.com)
2. Databasbladet klickar du på den **inställningar** knappen
3. Välj den **Transparent datakryptering** alternativet ![][1]
4. Välj den **av** inställningen ![][4]
5. Välj **spara**
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
