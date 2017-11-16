---
title: "Azure DB Cosmos global distributionsplatsen självstudier för tabellen API | Microsoft Docs"
description: "Lär dig hur du ställer in Azure Cosmos DB global distributionsplatsen med tabell-API."
services: cosmos-db
keywords: Global distributionsplatsen, tabell
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 93e75429d66a30bfc4588a3070e32d58eec0df4b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Hur du konfigurerar Azure Cosmos DB global distributionsplatsen med tabell-API

Den här artikeln omfattar följande aktiviteter: 

> [!div class="checklist"]
> * Konfigurera distributionslistor med Azure-portalen
> * Konfigurera distributionslistor med hjälp av den [tabellen API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Ansluter till en önskad region med tabell-API

För att kunna dra nytta av [global distributionsplatsen](distribute-data-globally.md), klientprogram kan ange beställda inställningar listan över regioner som används för att utföra åtgärder för dokumentet. Detta kan göras genom att ange den `TablePreferredLocations` Konfigurationsvärdet i app.config för Azure Cosmos DB tabell API SDK. Azure Cosmos DB tabell API SDK hämtar bästa slutpunkten ska kunna kommunicera med baserat på konfigurationen av kontot, regional tillgänglighet och listan med angivna inställningar.

Den `TablePreferredLocations` ska innehålla en kommaavgränsad lista över önskade (flera homing) platser för läsning. Varje klientinstans kan ange en delmängd av dessa regioner i önskad ordning för låg latens läsningar. Regionerna måste namnges med deras [visningsnamn](https://msdn.microsoft.com/library/azure/gg441293.aspx), till exempel `West US`.

Alla Läs skickas till den första tillgängliga regionen i den `TablePreferredLocations` lista. Om begäran inte klienten misslyckas nedåt i listan till nästa region, och så vidare.

SDK försöker läsa från de regioner som anges i `TablePreferredLocations`. I så fall till exempel om det konto som är tillgänglig i tre regioner, men klienten anger endast två av regionerna som icke-och skrivbehörighet för `TablePreferredLocations`, och sedan utan läsning som inte hanteras out-of-skrivåtgärder region, även i händelse av växling vid fel.

SDK skickar automatiskt alla skrivningar till aktuellt skriva region.

Om den `TablePreferredLocations` egenskapen har inte angetts, alla begäranden hanteras från det aktuella området för skrivning.

```xml
    <appSettings>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>           
    </appSettings>
```

Det är den som Slutför den här kursen. Du kan lära dig hur du hanterar konsekvensen för globalt replikerade kontot genom att läsa [konsekvensnivåer i Azure Cosmos DB](consistency-levels.md). Och för mer information om hur globala databasreplikering fungerar i Azure Cosmos DB, se [distribuera data globalt med Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer du har gjort följande:

> [!div class="checklist"]
> * Konfigurera distributionslistor med Azure-portalen
> * Konfigurera distributionslistor med API: er Azure Cosmos DB tabell

