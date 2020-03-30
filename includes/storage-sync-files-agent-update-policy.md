---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123222"
---
Azure File Sync-agenten uppdateras regelbundet för att lägga till nya funktioner och åtgärda problem. Vi rekommenderar att du konfigurerar Microsoft Update för att hämta uppdateringar för Azure File Sync-agenten när de är tillgängliga.

#### <a name="major-vs-minor-agent-versions"></a>Huvudversioner jämfört med mindre agent
* Huvudagentversioner innehåller ofta nya funktioner och har ett ökande antal som den första delen av versionsnumret. Till exempel: \*2.\*.\*\*
* Minor agent versioner kallas också "patchar" och släpps oftare än större versioner. De innehåller ofta buggfixar och mindre förbättringar men inga nya funktioner. Till exempel: \* \*.3.\*\*

#### <a name="upgrade-paths"></a>Uppgradera sökvägar
Det finns fyra godkända och testade sätt att installera Azure File Sync-agentuppdateringarna. 
1. **(Föredras) Konfigurera Microsoft Update för att automatiskt hämta och installera agentuppdateringar.**  
    Vi rekommenderar alltid att du tar alla Azure File Sync-uppdateringar för att säkerställa att du har tillgång till de senaste korrigeringarna för serveragenten. Microsoft Update gör den här processen sömlös genom att automatiskt hämta och installera uppdateringar åt dig.
2. **Använd AfsUpdater.exe för att hämta och installera agentuppdateringar.**  
    AfsUpdater.exe finns i agentinstallationskatalogen. Dubbelklicka på den körbara filen för att hämta och installera agentuppdateringar. 
3. **Korrigering av en befintlig Azure File Sync-agent med hjälp av en Microsoft Update-korrigeringsfil eller en MSP-körbar. Det senaste uppdateringspaketet för Azure File Sync kan hämtas från [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Om du kör en MSP-körbar uppgraderas din Azure File Sync-installation med samma metod som används automatiskt av Microsoft Update i föregående uppgraderingssökväg. Om du installerar en Microsoft Update-korrigeringsfil utförs en uppgradering på plats av en Azure File Sync-installation.
4. **Hämta det senaste Azure File Sync-agentinstallationsprogrammet från [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Om du vill uppgradera en befintlig Azure File Sync-agentinstallation avinstallerar du den äldre versionen och installerar sedan den senaste versionen från den hämtade installationsprogrammet. Serverregistrering, synkroniseringsgrupper och andra inställningar underhålls av Installationsprogrammet för Azure File Sync.

#### <a name="automatic-agent-lifecycle-management"></a>Hantering av automatisk agentlivscykel
Med agent version 6 har filsynkroniseringsteamet infört en funktion för automatisk uppgradering av agenten. Du kan välja något av två lägen och ange ett underhållsfönster där uppgraderingen ska göras på servern. Den här funktionen är utformad för att hjälpa dig med agentens livscykelhantering genom att antingen tillhandahålla ett skyddsräcke som hindrar din agent från att gå ut eller med hänsyn till ett krångel, håll dig uppdaterad.
1. **Standardinställningen** försöker förhindra att agenten upphör att gälla. Inom 21 dagar efter ett agents bokförda utgångsdatum kommer agenten att försöka uppgradera sig själv. Det startar ett försök att uppgradera en gång i veckan inom 21 dagar före utgångsdatumet och i det valda underhållsfönstret. **Det här alternativet eliminerar inte behovet av att ta vanliga Microsoft Update-korrigeringar.**
1. Du kan också välja att agenten automatiskt uppgraderar sig själv så snart en ny agentversion blir tillgänglig (gäller för närvarande inte för klustrade servrar). Den här uppdateringen sker under det valda underhållsfönstret och gör det möjligt för servern att dra nytta av nya funktioner och förbättringar så snart de blir allmänt tillgängliga. Detta är den rekommenderade, bekymmersfria inställningen som kommer att ge stora agentversioner samt regelbundna uppdateringskorrigeringar till din server. Varje agent som släpps är på GA kvalitet. Om du väljer det här alternativet skickar Microsoft den senaste agentversionen till dig. Klustrade servrar är undantagna. När flightingen är klar blir agenten också tillgänglig på [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257) aka.ms/AFS/agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Ändra inställningen för automatisk uppgradering

I följande instruktioner beskrivs hur du ändrar inställningarna när du har slutfört installationsprogrammet, om du behöver göra ändringar.

Öppna en PowerShell-konsol och navigera till katalogen där du installerade synkroniseringsagenten och importera sedan server cmdlets. Som standard skulle detta se ut ungefär så här:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

Du kan `Get-StorageSyncAgentAutoUpdatePolicy` köra för att kontrollera den aktuella principinställningen och avgöra om du vill ändra den.

Om du vill ändra den aktuella principinställningen till det försenade uppdateringsspåret kan du använda:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Om du vill ändra den aktuella principinställningen till det omedelbara uppdateringsspåret kan du använda:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Agentens livscykel och ändringshanteringsgarantier
Azure File Sync är en molntjänst som kontinuerligt introducerar nya funktioner och förbättringar. Det innebär att en specifik Azure File Sync-agentversion endast kan stödjas under en begränsad tid. För att underlätta distributionen garanterar följande regler att du har tillräckligt med tid och meddelanden för att hantera agentuppdateringar/uppgraderingar i ändringshanteringsprocessen:

- Huvudagentversioner stöds i minst sex månader från det datum då den första versionen släpptes.
- Vi garanterar att det finns en överlappning på minst tre månader mellan stödet för större agentversioner. 
- Varningar utfärdas för registrerade servrar med hjälp av en snart utgången agent minst tre månader före utgången. Du kan kontrollera om en registrerad server använder en äldre version av agenten under avsnittet registrerade servrar i en storage sync-tjänst.
- Livslängden för en mindre agentversion är bunden till den associerade huvudversionen. Till exempel när agent version 3.0 släpps, agentversioner 2. \* kommer alla att vara inställda på att löpa ut tillsammans.

> [!Note]
> Om du installerar en agentversion med en förfallovarning visas en varning men lyckas. Försök att installera eller ansluta till en utgången agentversion stöds inte och kommer att blockeras.
