---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: 4f59f68c1598f737ea7cb3a0e8046fc0779ed9d3
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60118892"
---
Azure File Sync-agenten uppdateras regelbundet att lägga till nya funktioner och för att lösa problem. Vi rekommenderar att du konfigurerar Microsoft Update för att hämta uppdateringar för Azure File Sync-agenten när de är tillgängliga.

#### <a name="major-vs-minor-agent-versions"></a>Större eller mindre agentversioner
* Större agentversioner ofta innehåller nya funktioner och har allt fler som den första delen av versionsnumret. Exempel: \*2.\*.\*\*
* Mindre agentversioner kallas även ”korrigeringar” och släpps oftare än högre versioner. De innehåller ofta felkorrigeringar och förbättringar av mindre men inga nya funktioner. Till exempel: \* \*.3.\*\*

#### <a name="upgrade-paths"></a>Sökvägar för uppgradering
Det finns fyra godkända och testats sätt att installera uppdateringar för Azure File Sync-agenten. 
1. **(Rekommenderas) Konfigurera Microsoft Update för att automatiskt hämta och installera agentuppdateringar.**  
    Vi rekommenderar alltid var Azure File Sync-update för att säkerställa att du har åtkomst till de senaste korrigeringarna för server-agenten. Microsoft Update är den här processen sömlös, genom att automatiskt hämta och installera uppdateringar åt dig.
2. **Använd AfsUpdater.exe att hämta och installera agentuppdateringar.**  
    AfsUpdater.exe finns i installationskatalogen för agenten. Dubbelklicka på den körbara filen för att ladda ned och installera agentuppdateringar. 
3. **Korrigera ett befintligt Azure File Sync-agenten med hjälp av en korrigeringsfil för Microsoft Update eller en körbar .msp. Det senaste uppdateringspaketet för Azure File Sync kan laddas ned från den [Microsoft Update-katalogen](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Köra en körbar .msp uppgraderar Azure File Sync-installationen med samma metod som används automatiskt av Microsoft Update i den föregående uppgraderingsvägen. Tillämpa en korrigering för Microsoft Update utför en uppgradering på plats av en Azure File Sync-installation.
4. **Hämta installationsprogrammet för den senaste Azure File Sync-agenten från den [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Uppgradera en befintlig installation av Azure File Sync-agenten genom avinstallera den äldre versionen och installera den senaste versionen från det nedladdade installationsprogrammet. Registrera servern synkroniseringsgrupper och eventuella andra inställningar underhålls av Azure File Sync-installationsprogrammet.

#### <a name="automatic-agent-lifecycle-management"></a>Livscykelhantering för automatisk agenthantering
Med agent-version 6, har file sync-teamet infört en funktion för automatisk uppgradering av agent. Du kan välja något av två lägen och ange en underhållsperiod som uppgraderingen ska göras på servern. Den här funktionen är avsedd att hjälpa dig med livscykelhantering agent med antingen en guardrail hindrar din agent från upphör att gälla eller vilket ger en smidig, stannar aktuella inställningen.
1. Den **standardinställningen** försöker att förhindra att agenten från upphör att gälla. Inom 21 dagar före bokförda utgångsdatumet för en agent försöker agenten lokal uppgradera. Ett försök att uppgradera en gång i veckan i 21 dagar före förfallodatum och i den valda underhållsperioden startas. **Det här alternativet inte behöver för att utföra regelbundna Microsoft Update-korrigeringar.**
2. Du kan också markera att agenten kommer att automatiskt uppgradera själva så fort en ny agentversion blir tillgänglig. Det även inträffa under det valda underhållsfönstret och tillåter du att din server kan dra nytta av nya funktioner och förbättringar så fort de blir allmänt tillgänglig. Detta är den rekommendera och problemfri inställningen som ger större agentversioner samt vanlig uppdatering korrigeringar till servern.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Livscykel och ändra agenthantering garanterar
Azure File Sync är en molntjänst som kontinuerligt introducerar nya funktioner och förbättringar. Det innebär att en specifik version av Azure File Sync-agenten kan bara användas under en begränsad tid. För att underlätta distributionen av garanterar följande regler att du har tillräckligt med tid och ett meddelande för agenten uppdateringar/uppgraderingar i din process för ändringshantering:

- Större agentversioner som stöds för minst sex månader från dagen för första versionen.
- Vi garanterar att det finns en överlappning av minst tre månader mellan stöd för större agentversioner. 
- Varningar har utfärdats för registrerade servrar med hjälp av en har upphört att gälla snart tas agenten minst tre månader före förfallodatumet. Du kan kontrollera om en registrerad server använder en äldre version av agenten under avsnittet registrerade servrar för en Lagringssynkroniseringstjänst.
- Livslängden för en mindre agentversionen är kopplad till den associera huvudversionen. Till exempel när agent-version 3.0 släpps, agentversioner 2. \* alla upphör att gälla tillsammans.

> [!Note]
> Om du installerar en agentversion med ett förfallodatum varning visas en varning, men lyckas. Försök att installera eller ansluta till en har upphört att gälla agentversion stöds inte och kommer att blockeras.
