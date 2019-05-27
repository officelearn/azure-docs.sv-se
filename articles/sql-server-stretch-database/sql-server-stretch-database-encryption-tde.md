---
title: Aktivera Transparent datakryptering för Stretch Database – Azure | Microsoft Docs
description: Aktivera Transparent datakryptering (TDE) för SQL Server Stretch Database på Azure
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.openlocfilehash: 61f556476958484b78b9c3dff2583eb6db043637
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003045"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Aktivera Transparent datakryptering (TDE) för Stretch Database på Azure
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent datakryptering (TDE) skyddar mot skadlig aktivitet genom att utföra i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet.

TDE krypterar lagring av en hel databas med hjälp av en symmetrisk nyckel som heter databaskrypteringsnyckeln. Databaskrypteringsnyckeln skyddas av ett certifikat för inbyggda. Inbyggda servercertifikatet är unikt för varje Azure-server. Microsoft roterar dessa certifikat automatiskt minst var 90: e dag. En allmän beskrivning av TDE Se [Transparent datakryptering (TDE)].

## <a name="enabling-encryption"></a>Aktivera kryptering
Om du vill aktivera transparent Datakryptering för en Azure-databas som lagrar data som har migrerats från en Stretch-aktiverade SQL Server-databas, gör du följande:

1. Öppna databasen i den [Azure-portalen](https://portal.azure.com)
2. Databasbladet klickar du på den **inställningar** knappen
3. Välj den **Transparent datakryptering** alternativet ![][1]
4. Välj den **på** inställning och välj sedan **spara**
   ![][2]

## <a name="disabling-encryption"></a>Inaktivering av kryptering
Om du vill inaktivera TDE för en Azure-databas som lagrar data som migrerats från en Stretch-aktiverade SQL Server-databas, gör du följande:

1. Öppna databasen i den [Azure-portalen](https://portal.azure.com)
2. Databasbladet klickar du på den **inställningar** knappen
3. Välj den **Transparent datakryptering** alternativet
4. Välj den **av** inställning och välj sedan **spara**

<!--Anchors-->
[Transparent datakryptering (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
