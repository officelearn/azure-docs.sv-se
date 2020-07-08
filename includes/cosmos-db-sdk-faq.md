---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187094"
---
**1. Hur kommer kunder att meddelas om att ta bort SDK?**

Microsoft kommer att tillhandahålla 12 månaders förvarning till slutet av stödet för att dra tillbaka SDK för att under lätta en smidig över gång till en SDK som stöds. Dessutom kommer kunderna att meddelas via olika kommunikations kanaler – Azure Hanteringsportal, Developer Center, blogg inlägg och direkt kommunikation till tilldelade tjänst administratörer.

**2. kan kunder skapa program med hjälp av en "att bli"-föråldrad Azure Cosmos DB SDK under 12 månaders perioden?** 

Ja, kunderna får fullständig åtkomst till författare, distribution och ändring av program med hjälp av "to-to-repensioned Azure Cosmos DB SDK under perioden på 12 månader. Under perioden på 12 månader uppmanas kunderna att migrera till en nyare version av Azure Cosmos DB SDK efter behov.

**3. kan kunder redigera och ändra program med hjälp av en tillbakadragen Azure Cosmos DB SDK efter meddelande perioden på 12 månader?**

Efter aviserings perioden på 12 månader kommer SDK att dras tillbaka. Åtkomst till Azure Cosmos DB av ett program som använder en tillbakadragen SDK tillåts inte av Azure Cosmos DBs plattformen. Dessutom ger Microsoft inte kund support på den tillbakadragna SDK: n.

**4. Vad händer med kundens program som använder Azure Cosmos DB SDK-version som inte stöds?**

Alla försök som görs för att ansluta till Azure Cosmos DB tjänsten med en inaktive rad SDK-version avvisas. 

**5. kommer nya funktioner och funktioner att tillämpas på alla icke-pensionerade SDK: er?**

Nya funktioner och funktioner kommer bara att läggas till i nya versioner. Om du använder en äldre, icke-omsluten version av SDK som dina begär anden till Azure Cosmos DB fortfarande fungerar som tidigare men du kommer inte att ha åtkomst till några nya funktioner.  

**6. Vad ska jag göra om jag inte kan uppdatera mitt program innan ett avhuggs datum?**

Vi rekommenderar att du uppgraderar till den senaste SDK: n så tidigt som möjligt. När ett SDK har taggats för pensionering, kommer du att ha 12 månader på dig att uppdatera ditt program. Om du av någon anledning inte kan slutföra program uppdateringen inom denna tidsram kan du kontakta [Cosmos DB-teamet](mailto:askcosmosdb@microsoft.com) och be om hjälp före slutdatumet.

