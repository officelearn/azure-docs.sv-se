---
title: Aktivera transparent datakryptering för stretchdatabas
description: Aktivera TDE (Transparent Data Encryption) för SQL Server Stretch Database på Azure
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
ms.custom: seo-lt-2019
ms.openlocfilehash: 465338bb793ad7b4fc406e9d1c073b9dc0dc06c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034002"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Aktivera transparent datakryptering (TDE) för stretchdatabas på Azure
> [!div class="op_single_selector"]
> * [Azure-portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL (TSQL)](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent datakryptering (TDE) hjälper till att skydda mot hotet om skadlig aktivitet genom att utföra kryptering i realtid och dekryptering av databasen, associerade säkerhetskopior och transaktionsloggfiler i vila utan att kräva ändringar i programmet.

Transparent datakryptering (TDE) krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel kallad databaskrypteringsnyckeln. Krypteringsnyckeln för databasen skyddas av ett inbyggt servercertifikat. Det inbyggda servercertifikatet är unikt för varje Azure-server. Microsoft roterar automatiskt dessa certifikat minst var 90:e dag. En allmän beskrivning av TDE finns i [Transparent datakryptering (TDE)].

## <a name="enabling-encryption"></a>Aktivera kryptering
Så här aktiverar du TDE för en Azure-databas som lagrar data som migreras från en Stretch-aktiverad SQL Server-databas:

1. Öppna databasen i [Azure-portalen](https://portal.azure.com)
2. Klicka på knappen **Inställningar** i databasbladet
3. Välj alternativet **Transparent datakryptering**![][1]
4. Välj inställningen **På** och välj sedan **Spara**
   ![][2]

## <a name="disabling-encryption"></a>Inaktivera kryptering
Så här inaktiverar du TDE för en Azure-databas som lagrar data som migreras från en Stretch-aktiverad SQL Server-databas:

1. Öppna databasen i [Azure-portalen](https://portal.azure.com)
2. Klicka på knappen **Inställningar** i databasbladet
3. Välj alternativet **Transparent datakryptering**
4. Välj inställningen **Av** och välj sedan **Spara**

<!--Anchors-->
[Transparent datakryptering (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
