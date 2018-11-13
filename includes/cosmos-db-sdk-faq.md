---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572791"
---
**1. Hur kommer kunderna att meddelas om retiring SDK?**

Microsoft meddelar 12 månaders förhandsinformation i slutet av stödet för retiring SDK för att underlätta en smidig övergång till en SDK som stöds. Dessutom kan kunderna meddelas via olika kommunikation kanaler – hanteringsportalen för Azure Developer Center, blogginlägg, och direkt kommunikation till tilldelade tjänstadministratörer.

**2. Kunder kan skapa program med hjälp av en ”ska” dras tillbaka Azure Cosmos DB SDK under 12-månadersperioden?** 

Ja, kunder har fullständig åtkomst att skapa, distribuera och ändra de program som använder ”ska” dras tillbaka Azure Cosmos DB SDK under respittiden 12 månader. Under respittiden 12 månaders bör kunder migrera till en nyare version som stöds av Azure Cosmos DB SDK efter behov.

**3. Kan kunder skapa och ändra program som använder en pensionerad Azure Cosmos DB SDK efter den 12 månaders meddelande?**

SDK: N kommer att dras tillbaka efter meddelandeperiod 12 månader. Åtkomst till Azure Cosmos DB genom att ett program med hjälp av en pensionerad SDK tillåts inte av Azure Cosmos DB-plattformen. Dessutom kan tillhandahåller Microsoft inte kundsupport om tillbakadragna SDK.

**4. Vad händer med kunden kör program som använder Azure Cosmos DB SDK-versionen stöds inte?**

Alla försök att ansluta till Azure Cosmos DB-tjänsten med en pensionerad SDK-version har gjort avvisas. 

**5. Nya funktionerna och egenskaperna tillämpas på alla icke-dras tillbaka SDK: er?**

Nya funktioner läggs endast till nya versioner. Om du använder en äldre, icke-dras tillbaka, version av SDK: N dina förfrågningar till Azure Cosmos DB kommer fortfarande att fungera som tidigare, men du har inte åtkomst till alla nya funktioner.  

**6. Vad gör jag om jag inte kan uppdatera mitt program innan ett slutdatum?**

Vi rekommenderar att du uppgraderar till den senaste SDK så tidigt som möjligt. När en SDK har taggats för pensioner har du 12 månader att uppdatera ditt program. Om, oavsett orsak, du inte kan slutföra din programuppdateringen inom denna period och sedan kontakta den [Cosmos DB-teamet](mailto:askcosmosdb@microsoft.com) och begära deras hjälp före slutdatumet.

