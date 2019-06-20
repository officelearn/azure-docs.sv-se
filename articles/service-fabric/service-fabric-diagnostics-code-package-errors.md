---
title: Azure Service Fabric diagnostisera paketet kodfel | Microsoft Docs
description: Lär dig att felsöka vanliga kod paket med Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: grzuber
manager: gkhanna
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 235952388d2c044cc141b3020c67944c4250ea3d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276956"
---
# <a name="diagnose-common-code-package-errors-with-service-fabric"></a>Diagnostisera vanliga kod paketfel med Service Fabric

Den här artikeln visar vad det innebär för ett kodpaket avslutas oväntat och ger information om möjliga orsaker och vanliga felkoder som felsökningsstegen som potentiellt kan åtgärda problemet.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>När en process eller behållare, avslutas oväntat?

När Service Fabric tar emot en begäran om att starta ett kodpaket, börjar förbereda miljön på det lokala systemet baserat på de konfigurationsalternativ som angetts i appen och tjänsten manifest. Dessa förberedelser kan omfatta reserverar nätverksslutpunkter eller resurser, att konfigurera brandväggsregler eller ställa in begränsningar för resurs-styrning. När miljön har konfigurerats korrekt, Service Fabric-försök att ta fram kodpaketet. Det här steget anses lyckas om OS- eller behållare runtime rapporterar att processer eller behållare har aktiverats. Om aktiveringen lyckades, bör du se ett meddelande om hälsotillståndet i SFX som säger

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

När kodpaketet har trätt, börjar Service Fabric monitoring dess livslängd. En process eller behållare kan nu Avsluta när som helst för en rad orsaker. Det kan ha misslyckats att initiera en DLL-filen Operativsystemet kan ha slut på skrivbordets heap-utrymme osv. Om din kodpaketet avslutas, bör du se ett meddelande för hälsotillstånd i SFX som säger

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Slutkoden anges i denna hälsa meddelandet är endast ledtråd som tillhandahålls av processen/behållare om varför det avslutas och det kan ha genererats av en nivå i stacken. Det är svårt att förstå om det här slutkoden var närstående, till exempel ett OS-fel, ett .NET-problem eller har aktiverats av din kod. Därför kan den här artikeln kan användas som utgångspunkt för att diagnostisera orsaken avslutning slutkoder för aktiviteter och möjliga lösningar, med tanke på att dessa är allmänna lösningar på vanliga scenarier och kanske inte gäller fel som du får.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Hur vet jag om min kodpaketet avslutades för Service Fabric?

Service Fabric som ansvarar för att avsluta ditt kod paket av olika skäl. Till exempel den besluta att placera paketets kod på en annan nod för Utjämning av nätverksbelastning syften. Berätta om din kodpaketet avbröts av Service Fabric om du ser något av de slutkoder för aktiviteter i följande tabell:

>[!NOTE]
> Om processen/behållaren avslutas med slutkoden än de som i följande tabell, ansvarar inte Service Fabric för att avsluta den.

Slutkod | Beskrivning
--------- | -----------
7147 | Dessa felkoder visar att Service Fabric stängs processen/behållare genom att skicka den till en signal Ctrl + C.
7148 | Dessa felkoder visar att Service Fabric avslutas processen/behållare. Den här felkoden kan ibland kan tyda på att processen/behållare inte svarade i tid när du har skickat en signal Ctrl + C, så att den var tvungen att avslutas.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Andra vanliga felkoder och deras potentiella korrigeringar

Slutkod | Hexadecimalt värde | Kort beskrivning | Rotorsak | Potentiella korrigering
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Det här felet kan potentiellt innebär att datorn har slut på utrymme för skrivbord heap. Den här orsak troligtvis särskilt om du har ett stort antal processer som hör till ditt program som körs på noden. | Om ditt program inte har utformats för att svara på Ctrl + C signaler kan aktivera du inställningen ”EnableActivateNoWindow” i klustret Manifest. Om du aktiverar den här inställningen skulle innebära din kodpaketet kördes utan ett GUI-fönster och skulle inte att ta emot signaler Ctrl + C, men skulle minska mängden utrymme för skrivbord heap varje process förbrukar. Om din kodpaketet måste ta emot signaler Ctrl + C, kan du öka storleken på din nod skrivbord heap.
3762504530 | 0xe0434352 | Gäller inte | Det här värdet är felkoden för ett ohanterat undantag från hanterad kod (det vill säga .NET). | Om du ser den här slutkod, innebär det att ditt program utlöste ett undantag som fortfarande ohanterad och avslutade processen. Felsöka ditt program loggar och minnesdumpar ska vara det första steget för att avgöra vad som orsakade felet.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [diagnostisering av andra vanliga scenarier](service-fabric-diagnostics-common-scenarios.md)
* Få en mer detaljerad översikt över Azure Monitor-loggar och den erbjuder genom att läsa [vad är Azure Monitor-loggar?](../operations-management-suite/operations-management-suite-overview.md)
* Läs mer om Azure Monitor-loggar [avisering](../log-analytics/log-analytics-alerts.md) att underlätta identifiering och diagnostik.
* Bekanta dig med den [loggsökning och frågor](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av Azure Monitor-loggar
