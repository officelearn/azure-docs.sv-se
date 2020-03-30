---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187094"
---
**1. Hur kommer kunderna att meddelas om den avgående SDK?**

Microsoft kommer att tillhandahålla 12 månaders förhandsmeddelande till slutet av stödet för den avgående SDK för att underlätta en smidig övergång till en SDK som stöds. Vidare meddelas kunder via olika kommunikationskanaler – Azure Management Portal, Developer Center, blogginlägg och direktkommunikation till tilldelade tjänstadministratörer.

**2. Kan kunder skapa program som använder en "att vara" pensionerad Azure Cosmos DB SDK under 12-månadersperioden?** 

Ja, kunder har fullständig åtkomst till författare, distribuera och ändra program med hjälp av "to-be" pensionerade Azure Cosmos DB SDK under 12 månaders respitperiod. Under respitperioden på 12 månader rekommenderas kunder att migrera till en nyare version av Azure Cosmos DB SDK som är lämplig.

**3. Kan kunder skapa och ändra program med hjälp av en pensionerad Azure Cosmos DB SDK efter 12 månaders meddelandeperiod?**

Efter 12 månaders anmälningsperiod kommer SDK att dras tillbaka. Åtkomst till Azure Cosmos DB av ett program som använder en pensionerad SDK tillåts inte av Azure Cosmos DB-plattformen. Dessutom kommer Microsoft inte att tillhandahålla kundsupport på den pensionerade SDK.

**4. Vad händer med kundens program som körs utan stöd azure cosmos DB SDK-version?**

Alla försök som görs för att ansluta till Azure Cosmos DB-tjänsten med en pensionerad SDK-version kommer att avvisas. 

**5. Kommer nya funktioner att tillämpas på alla icke-pensionerade SDK:er?**

Nya funktioner och funktioner kommer endast att läggas till i nya versioner. Om du använder en gammal, icke-pensionerad, version av SDK dina förfrågningar till Azure Cosmos DB kommer fortfarande att fungera som tidigare men du kommer inte att ha tillgång till några nya funktioner.  

**6. Vad ska jag göra om jag inte kan uppdatera min ansökan före ett mellanbeskärningsdatum?**

Vi rekommenderar att du uppgraderar till den senaste SDK så tidigt som möjligt. När en SDK har taggats för pensionering har du 12 månader på dig att uppdatera ditt program. Om du av någon anledning inte kan slutföra din programuppdatering inom den här tidsramen kontaktar du [Cosmos DB Team](mailto:askcosmosdb@microsoft.com) och begär deras hjälp före slutdatumet.

