---
title: Aktivera transparent datakryptering för Stretch Database
description: Aktivera transparent datakryptering (TDE) för SQL Server Stretch Database på Azure
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74034002"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Aktivera transparent datakryptering (TDE) för Stretch Database på Azure
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent datakryptering (TDE) skyddar mot hot mot skadlig aktivitet genom att utföra kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler i vila utan att det krävs några ändringar i programmet.

Transparent datakryptering (TDE) krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel kallad databaskrypteringsnyckeln. Databas krypterings nyckeln skyddas av ett inbyggt Server certifikat. Det inbyggda Server certifikatet är unikt för varje Azure-Server. Microsoft roterar dessa certifikat automatiskt minst var 90: e dag. En allmän beskrivning av TDE finns [Transparent datakryptering (TDE)].

## <a name="enabling-encryption"></a>Aktivera kryptering
Gör så här om du vill aktivera TDE för en Azure-databas som lagrar data som migrerats från en utsträckt SQL Server databas:

1. Öppna databasen i [Azure Portal](https://portal.azure.com)
2. Klicka på knappen **Inställningar** i bladet databas
3. Välj alternativet **transparent data kryptering**![][1]
4. Välj inställningen **på** och välj sedan **Spara**
   ![][2]

## <a name="disabling-encryption"></a>Inaktiverar kryptering
Om du vill inaktivera TDE för en Azure-databas som lagrar data som migrerats från en utsträckt SQL Server databas gör du följande:

1. Öppna databasen i [Azure Portal](https://portal.azure.com)
2. Klicka på knappen **Inställningar** i bladet databas
3. Välj alternativet **transparent data kryptering**
4. Välj **off** -inställningen och välj sedan **Spara**

<!--Anchors-->
[Transparent datakryptering (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
