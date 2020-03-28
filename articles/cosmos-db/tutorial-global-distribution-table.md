---
title: Självstudie om global distribution i Azure Cosmos DB för tabell-API
description: Lär dig hur global distribution fungerar i Azure Cosmos DB Table API-konton och hur du konfigurerar den önskade listan över regioner
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 627086bdb13acdd29821af399f90fee8deaae432
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76900177"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Konfigurera global distribution i Azure Cosmos DB med tabell-API

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med [tabell-API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Ansluta till en önskad region med hjälp av tabell-API

För att kunna dra nytta av den [globala distributionen](distribute-data-globally.md)bör klientprogram ange den aktuella platsen där deras program körs. Detta görs genom `CosmosExecutorConfiguration.CurrentRegion` att ange egenskapen. Egenskapen `CurrentRegion` ska innehålla en enda plats. Varje klientinstans kan ange sin egen region för låg latensläsning. Regionen måste namnges med hjälp av deras [visningsnamn](https://msdn.microsoft.com/library/azure/gg441293.aspx) som "Västra USA". 

Azure Cosmos DB Table API SDK väljer automatiskt den bästa slutpunkten att kommunicera med baserat på kontokonfigurationen och aktuell regional tillgänglighet. Den prioriterar den närmaste regionen för att ge bättre svarstid till klienter. När du har `CurrentRegion` angett den aktuella egenskapen dirigeras läs- och skrivbegäranden på följande sätt:

* **Läs förfrågningar:** Alla läsbegäranden skickas till `CurrentRegion`den konfigurerade . Baserat på närheten väljer SDK automatiskt en reservgeod replikerad region för hög tillgänglighet.

* **Skriv förfrågningar:** SDK skickar automatiskt alla skrivbegäranden till den aktuella skrivregionen. I ett multi master-konto kommer den aktuella regionen att tjäna skrivbegäranden också. Baserat på närheten väljer SDK automatiskt en reservgeod replikerad region för hög tillgänglighet.

Om du inte anger `CurrentRegion` egenskapen använder SDK det aktuella skrivområdet för alla operationer.

Om ett Azure Cosmos-konto till exempel finns i regioner i västra USA och östra USA. Om "Västra USA" är skrivregionen och ansökan finns i "Östra USA". Om egenskapen CurrentRegion inte är konfigurerad dirigeras alltid alla läs- och skrivbegäranden till regionen "Västra USA". Om egenskapen CurrentRegion är konfigurerad visas alla läsbegäranden från regionen "Östra USA".

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med tabell-API:n i Azure Cosmos DB

