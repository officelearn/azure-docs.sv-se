---
title: "Azure Service Fabric tillståndskänsliga Reliable Services diagnostik | Microsoft Docs"
description: "Diagnostikfunktion för tillståndskänsliga Reliable Services i Azure Service Fabric"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: edcaaaf8f1619082b33195aedf1fb1abf32e85b1
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Diagnostisk funktionalitet för tillståndskänsliga Reliable Services
Klassen Azure Service Fabric Stateful Reliable Services StatefulServiceBase avger [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) händelser som kan användas vid felsökning av tjänsten, som ger insikter om hur körningsmiljön drift och underlätta felsökningen.

## <a name="eventsource-events"></a>EventSource händelser
EventSource namnet för klassen Stateful Reliable Services StatefulServiceBase är ”Microsoft-ServiceFabric-tjänster”. Händelser från den här händelsekällan visas i den [diagnostikhändelser](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) fönstret när tjänsten används [felsökas i Visual Studio](service-fabric-debugging-your-application.md).

Exempel på Verktyg och tekniker som hjälper till att samla in och/eller visa EventSource händelser är [förhandsgranskning](http://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), och [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Händelser
| händelsenamnet | Händelse-ID | Nivå | Händelsebeskrivning |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Information |Orsakat när aktiviteten RunAsync tjänst har startats |
| StatefulRunAsyncCancellation |2 |Information |Orsakat när tjänsten RunAsync uppgiften har avbrutits |
| StatefulRunAsyncCompletion |3 |Information |Orsakat när tjänsten RunAsync uppgiften har slutförts |
| StatefulRunAsyncSlowCancellation |4 |Varning |Orsakat när aktiviteten tjänst RunAsync tar för lång tid att slutföra annullering |
| StatefulRunAsyncFailure |5 |Fel |Orsakat när aktiviteten tjänst RunAsync genererar ett undantag |

## <a name="interpret-events"></a>Tolka händelser
StatefulRunAsyncInvocation och StatefulRunAsyncCompletion StatefulRunAsyncCancellation händelser är användbara för service-skrivaren för att förstå livscykeln för en tjänst, samt tiden för när en tjänst startar, avbryter eller har slutförts. Den här informationen kan vara användbar vid felsökning av problem med tjänsten eller förstå livscykeln för tjänsten.

Tjänsten skrivare bör uppmärksam på StatefulRunAsyncSlowCancellation och StatefulRunAsyncFailure händelser eftersom de visar på problem med tjänsten.

StatefulRunAsyncFailure genereras när aktiviteten tjänst RunAsync() genererar ett undantag. Normalt anger ett undantag uppstod ett fel eller ett fel i tjänsten. Dessutom gör undantaget tjänsten misslyckas, så flyttas den till en annan nod. Den här åtgärden kan vara dyrt och kan fördröja inkommande begäranden när tjänsten flyttas. Tjänsten skrivare bör ta reda på orsaken för undantaget och minimera om möjligt den.

StatefulRunAsyncSlowCancellation genereras när en begäran om att avbryta för RunAsync aktiviteten tar längre tid än fyra sekunder. När en tjänst tar för lång tid att slutföra annullering, påverkar möjligheten för tjänsten att startas snabbt på en annan nod. Det här scenariot kan påverka den allmänna tillgängligheten för tjänsten.

## <a name="next-steps"></a>Nästa steg
[EventSource providrar på förhandsgranskning](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
