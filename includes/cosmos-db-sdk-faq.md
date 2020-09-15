---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068751"
---
**Hur kommer jag att meddelas om SDK: n som tas ur bruk?**

Microsoft tillhandahåller ett meddelande om 12 månader innan supporten för att dra tillbaka SDK för att under lätta en smidig över gång till en SDK som stöds. Vi meddelar dig genom olika kommunikations kanaler: Azure Portal, Azure-uppdateringar och direkt kommunikation till tilldelade tjänst administratörer.

**Kan jag skapa program med hjälp av en to-pensionerd Azure Cosmos DB SDK under 12-månaders perioden?** 

Ja, du kommer att kunna skapa, distribuera och ändra program genom att använda den för att bli tillbakadragen Azure Cosmos DB SDK under meddelande perioden på 12 månader. Vi rekommenderar att du migrerar till en senare version av Azure Cosmos DB SDK under 12-månaders meddelande perioden, efter vad som är tillämpligt. 

**Vad händer med program som använder Azure Cosmos DB SDK som inte stöds efter indragnings datumet?** 

Efter indragnings datumet kommer Azure Cosmos DB inte längre att göra fel korrigeringar, lägga till nya funktioner eller ge stöd till de indragna SDK-versionerna. Om du föredrar att inte uppgradera fortsätter de begär Anden som skickas från de tillbakadragna versionerna av SDK: n att betjänas av tjänsten Azure Cosmos DB. 

**Vilka SDK-versioner kommer att ha de senaste funktionerna och uppdateringarna?**

Nya funktioner och uppdateringar läggs bara till den senaste lägre versionen av den senaste, viktigaste SDK-versionen. Vi rekommenderar att du alltid använder den senaste versionen för att dra nytta av nya funktioner, prestanda förbättringar och fel korrigeringar. Om du använder en gammal, icke-tillbakadragen version av SDK, kommer dina begär anden till Azure Cosmos DB fortfarande att fungera, men du kommer inte att ha till gång till några nya funktioner.  

**Vad ska jag göra om jag inte kan uppdatera mitt program före ett sista datum?**

Vi rekommenderar att du uppgraderar till den senaste SDK: n så tidigt som möjligt. När ett SDK har taggats för pensionering har du 12 månader på dig att uppdatera ditt program. Om du inte kan uppdatera efter indragnings datumet fortsätter förfrågningar som skickas från de indragna versionerna av SDK: n att betjänas av Azure Cosmos DB, så att dina program som körs fortsätter att fungera. Men Azure Cosmos DB kommer inte längre att göra fel korrigeringar, lägga till nya funktioner eller ge stöd till de indragna SDK-versionerna. 

Om du har en Support plan och behöver teknisk support [kontaktar du oss](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) genom att skicka in ett support ärende.
    


