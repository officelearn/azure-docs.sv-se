---
title: Diagnostisera vanliga kodpaketfel med hjälp av Service Fabric
description: Lär dig hur du felsöker vanliga kodpaketfel med Azure Service Fabric
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 344fef70522240da2236a020c96308c472c9c545
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463111"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Diagnostisera vanliga kodpaketfel med hjälp av Service Fabric

I den här artikeln beskrivs vad det innebär för ett kodpaket att avsluta oväntat. Det ger insikt i möjliga orsaker till vanliga felkoder, tillsammans med felsökningssteg.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>När avslutas en process eller behållare oväntat?

När Azure Service Fabric tar emot en begäran om att starta ett kodpaket börjar den förbereda miljön på det lokala systemet enligt alternativen i App- och tjänstmanifesten. Dessa förberedelser kan omfatta att reservera nätverksslutpunkter eller resurser, konfigurera brandväggsregler eller ställa in resursstyrningsbegränsningar. 

När miljön har konfigurerats korrekt försöker Service Fabric att hämta kodpaketet. Det här steget anses vara framgångsrikt om operativsystemet eller behållaren runtime rapporterar att processen eller behållaren har aktiverats. Om aktiveringen misslyckas bör du se ett hälsomeddelande i SFX som liknar följande:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

När kodpaketet har aktiverats börjar Service Fabric övervaka dess livstid. Vid denna punkt kan en process eller behållare avslutas när som helst av flera skäl. Det kan till exempel ha misslyckats med att initiera en DLL, eller så kan operativsystemet ha på skrivbordshögutrymme. Om kodpaketet har avslutats bör du se följande hälsomeddelande i SFX:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Avslutningskoden i det här hälsomeddelandet är den enda ledtråden som processen eller behållaren ger om varför den avslutades. Det kan genereras av någon nivå i stacken. Den här avslutningskoden kan till exempel vara relaterad till ett OS-fel eller ett .NET-problem, eller så kan den ha tagits upp av koden. Använd den här artikeln som utgångspunkt för att diagnostisera källan till avslutningskoder och möjliga lösningar. Men tänk på att det här är allmänna lösningar på vanliga scenarier och kanske inte gäller för felet du ser.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Hur vet jag om Service Fabric har avslutat mitt kodpaket?

Service Fabric kan vara ansvarig för att avsluta ditt kodpaket av olika skäl. Det kan till exempel besluta att placera kodpaketet på en annan nod för belastningsutjämning. Du kan kontrollera att Service Fabric har avslutat kodpaketet om du ser någon av stängningskoderna i följande tabell.

>[!NOTE]
> Om din process eller behållare avslutas med en annan avslutningskod än koderna i följande tabell ansvarar inte Service Fabric för att avsluta den.

Slutkod | Beskrivning
--------- | -----------
7147 | Anger att Service Fabric på ett smidigt sätt stänger av processen eller behållaren genom att skicka en Ctrl+C-signal.
7148 | Anger att Service Fabric har avslutat processen eller behållaren. Ibland anger den här felkoden att processen eller behållaren inte svarade i tid efter att ha skickat en Ctrl+C-signal och den måste avslutas.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Andra vanliga felkoder och deras potentiella korrigeringar

Slutkod | Hexadecimalt värde | Kort beskrivning | Rotorsak | Potentiell korrigering
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Det här felet innebär ibland att datorn har på skrivbordshögutrymme. Den här orsaken är särskilt troligt om du har många processer som tillhör ditt program som körs på noden. | Om programmet inte har byggts för att svara på Ctrl+C-signaler kan du aktivera inställningen **AktiveraActivateNoWindow** i klustermanifestet. Om du aktiverar den här inställningen körs kodpaketet utan guidningsfönster och Ctrl+C-signaler inte. Den här åtgärden minskar också mängden skrivbordshögutrymme som varje process förbrukar. Om kodpaketet behöver ta emot Ctrl+C-signaler kan du öka storleken på nodens skrivbordshög.
3762504530 | 0xe0434352 | Ej tillämpligt | Det här värdet representerar felkoden för ett ohanterat undantag från hanterad kod (det vill ha .NET). | Den här avslutningskoden anger att ditt program har tagit upp ett undantag som förblir ohanterat och som avslutade processen. Som det första steget för att avgöra vad som utlöste det här felet, felsöka programmets loggar och dumpa filer.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [att diagnostisera andra vanliga scenarier](service-fabric-diagnostics-common-scenarios.md).
* Få en mer detaljerad översikt över Azure Monitor-loggar och vad de erbjuder genom att läsa [Azure Monitor översikt](../operations-management-suite/operations-management-suite-overview.md).
* Läs mer om Azure Monitor-loggar [som varnar](../log-analytics/log-analytics-alerts.md) för hjälp med identifiering och diagnostik.
* Bekanta dig med [loggsöknings- och frågefunktionerna](../log-analytics/log-analytics-log-searches.md) som erbjuds som en del av Azure Monitor-loggar.
