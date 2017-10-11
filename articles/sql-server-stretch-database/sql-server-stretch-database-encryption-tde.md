---
title: "Aktivera Transparent datakryptering för Stretch Database – Azure | Microsoft Docs"
description: "Aktivera Transparent datakryptering (TDE) för SQL Server Stretch Database på Azure"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: barbkess
editor: 
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
ms.openlocfilehash: ceb355d2ba872ed5d3886c6dc82ca75b1854db0a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Aktivera Transparent datakryptering (TDE) för Stretch Database på Azure
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent Data kryptering (TDE) skyddar mot hot från skadlig aktivitet genom att utföra realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet.

TDE krypterar lagring av en hel databas med hjälp av en symmetrisk nyckel som heter databaskrypteringsnyckeln. Databaskrypteringsnyckeln skyddas av en inbyggd servercertifikat. Inbyggda certifikatet är unikt för varje Azure-servern. Microsoft roteras automatiskt dessa certifikat minst var 90: e dag. En allmän beskrivning av TDE finns [Transparent Data kryptering (TDE)].

## <a name="enabling-encryption"></a>Aktivera kryptering
Om du vill aktivera TDE för en Azure-databas som lagrar data som migrerats från en Stretch-aktiverade SQL Server-databas måste du göra följande:

1. Öppna databasen i den [Azure-portalen](https://portal.azure.com)
2. I databasbladet klickar du på den **inställningar** knappen
3. Välj den **Transparent datakryptering** alternativet![][1]
4. Välj den **på** inställningen och välj sedan **spara**
   ![][2]

## <a name="disabling-encryption"></a>Om du inaktiverar kryptering
Om du vill inaktivera TDE för en Azure-databas som lagrar data som migrerats från en Stretch-aktiverade SQL Server-databas måste du göra följande:

1. Öppna databasen i den [Azure-portalen](https://portal.azure.com)
2. I databasbladet klickar du på den **inställningar** knappen
3. Välj den **Transparent datakryptering** alternativet
4. Välj den **av** inställningen och välj sedan **spara**

<!--Anchors-->
[Transparent Data kryptering (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
