---
title: Azure Service Fabric-programnivå övervakning | Microsoft Docs
description: Läs mer om programmet och service händelser och loggar som används för att övervaka och diagnostisera Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: 258aac722aa1c94ecf2cbf0524a3e4b53b8a788c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="application-and-service-level-logging"></a>Programmet och service nivån loggning

Instrumentering koden är grunden för de flesta andra aspekter av övervaka dina tjänster. Instrumentation är det enda sättet du kan veta att något är fel, och diagnostisera vad som behöver åtgärdas. Även om det är tekniskt möjligt att ansluta en felsökare till en tjänst för produktion, men det är inte vanligt. Därför är har detaljerad instrumentationsdata viktigt.

Vissa produkter instrumentera automatiskt din kod. Även om dessa lösningar kan också fungera, krävs nästan alltid manuell instrumentation. Du måste ha tillräckligt med information för att felsöka kriminalteknikers programmet i slutet. Här beskrivs olika sätt att instrumentering koden och när du ska välja en metod över en annan.

## <a name="eventsource"></a>eventSource

När du skapar en Service Fabric-lösning från en mall i Visual Studio en **EventSource**-härledd klass (**ServiceEventSource** eller **ActorEventSource**) genereras. En mall skapas, där du kan lägga till händelser för programmet eller tjänsten. Den **EventSource** namn **måste** vara unikt och bör ändras från mallen standardsträngen mitt företag -&lt;lösning&gt;-&lt;projekt&gt;. Att flera **EventSource** definitioner som använder samma namn leder till problem vid körning. Varje definierad händelse måste ha en unik identifierare. Ett körningsfel inträffar om en identifierare som inte är unikt. Vissa organisationer preassign intervall med värden för identifierare för att undvika konflikter mellan separat utvecklingsgrupper. Mer information finns i [Vance's blog](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) eller [MSDN-dokumentationen](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>ASP.NET Core loggning

Det är viktigt att planera noggrant hur du kommer instrumentera din kod. Höger instrumentation planen kan hjälpa dig att undvika potentiell destabilizing din kodbas och sedan behöver reinstrument koden. För att minska risken kan du välja en instrumentation bibliotek som [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), vilket är en del av Microsoft ASP.NET Core. ASP.NET Core har en [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) gränssnitt som du kan använda med leverantören av ditt val när minimera effekten på befintlig kod. Du kan använda koden i ASP.NET Core på Windows och Linux och i fullständig .NET Framework, så instrumentation koden är standardiserad.

## <a name="choosing-a-logging-provider"></a>Att välja en provider för loggning

Om programmet är beroende av hög prestanda, **EventSource** vanligtvis är en bra metod. **EventSource** *vanligtvis* använder färre resurser och presterar bättre än ASP.NET Core loggning eller någon av de tillgängliga lösningarna från tredje part.  Detta är inte ett problem för många tjänster, men om tjänsten är inriktade på prestanda, med hjälp av **EventSource** kan vara ett bättre alternativ. Emellertid att hämta dessa fördelarna med strukturerade loggning, **EventSource** kräver en större från din teknikteamet. Om möjligt gör en snabb prototyp av några alternativ för loggning och sedan välja den som bäst uppfyller dina behov.

## <a name="next-steps"></a>Nästa steg

När du har valt loggning-providern kan instrumentera dina program och tjänster, måste din loggar och händelser ska aggregeras innan de kan skickas till alla analysplattform. Läs mer om [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) och [BOMULLSTUSS](service-fabric-diagnostics-event-aggregation-wad.md) att bättre förstå några av de rekommenderade alternativ.
