---
title: Transparent datakryptering (portal)
description: Transparent datakryptering (TDE) i Azure Synapse Analytics
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
ms.openlocfilehash: 247691326e3aa2c8027dd0318b23a2cbfcba1efe
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745239"
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

Så här aktiverar du TDE nedan:

1. Öppna databasen i [Azure-portalen](https://portal.azure.com)
2. Klicka på knappen **Inställningar** i databasbladet
3. Välj portalinställningar för ![alternativet **Transparent datakryptering**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Välj inställningarna ![för **På** inställning av portal på](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Välj **Spara**
   ![portalinställningar spara](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Inaktivera kryptering

Så här inaktiverar du TDE nedan:

1. Öppna databasen i [Azure-portalen](https://portal.azure.com)
2. Klicka på knappen **Inställningar** i databasbladet
3. Välj portalinställningar för ![alternativet **Transparent datakryptering**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Välj **Off** avinställningsportalinställningarna ![av](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Välj **Spara**
   ![portalinställning spara 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>DVS för kryptering

Kryptering kan bekräftas med följande DMVs:

* [sys.databaser](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
