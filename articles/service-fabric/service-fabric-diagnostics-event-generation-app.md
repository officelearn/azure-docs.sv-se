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
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: b8118d83e2be452c6aa5bbc8b7a3c220d26903a1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="application-and-service-level-logging"></a>Programmet och service nivån loggning

Instrumentering koden är grunden för de flesta andra aspekter av övervaka dina tjänster. Instrumentation är det enda sättet du kan veta att något är fel, och diagnostisera vad som behöver åtgärdas. Även om det är tekniskt möjligt att ansluta en felsökare till en tjänst för produktion, men det är inte vanligt. Därför är har detaljerad instrumentationsdata viktigt.

Vissa produkter instrumentera automatiskt din kod. Även om dessa lösningar kan också fungera, krävs nästan alltid manuell instrumentation. Du måste ha tillräckligt med information för att felsöka kriminalteknikers programmet i slutet. Här beskrivs olika sätt att instrumentering koden och när du ska välja en metod över en annan.

Exempel på hur du använder dessa förslag finns [lägga till loggning Service Fabric-programmet](service-fabric-how-to-diagnostics-log.md).

## <a name="eventsource"></a>eventSource

När du skapar en Service Fabric-lösning från en mall i Visual Studio en **EventSource**-härledd klass (**ServiceEventSource** eller **ActorEventSource**) genereras. En mall skapas, där du kan lägga till händelser för programmet eller tjänsten. Den **EventSource** namn **måste** vara unikt och bör ändras från mallen standardsträngen mitt företag -&lt;lösning&gt;-&lt;projekt&gt;. Att flera **EventSource** definitioner som använder samma namn leder till problem vid körning. Varje definierad händelse måste ha en unik identifierare. Ett körningsfel inträffar om en identifierare som inte är unikt. Vissa organisationer preassign intervall med värden för identifierare för att undvika konflikter mellan separat utvecklingsgrupper. Mer information finns i [Vance's blog](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) eller [MSDN-dokumentationen](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>ASP.NET Core loggning

Det är viktigt att planera noggrant hur du kommer instrumentera din kod. Höger instrumentation planen kan hjälpa dig att undvika potentiell destabilizing din kodbas och sedan behöver reinstrument koden. För att minska risken kan du välja en instrumentation bibliotek som [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), vilket är en del av Microsoft ASP.NET Core. ASP.NET Core har en [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) gränssnitt som du kan använda med leverantören av ditt val när minimera effekten på befintlig kod. Du kan använda koden i ASP.NET Core på Windows och Linux och i fullständig .NET Framework, så instrumentation koden är standardiserad.

## <a name="application-insights-sdk"></a>Application Insights SDK

Application Insights har en omfattande integrering med Service Fabric direkt. Användare kan lägga till nuget-paketen AI Service Fabric och ta emot data och loggfiler som skapas och samlas in på Azure-portalen. Dessutom kan bör användare lägga till egna telemetri för att diagnostisera och felsöka sina program och spåra som tjänster och delar av sina program används mest. Den [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) i SDK-klassen innehåller många sätt att spåra telemetri i dina program. Checka ut ett exempel på hur instrumentera och lägga till application insights i ditt program i våra självstudier för [övervakning och diagnos av ett .NET-program](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="next-steps"></a>Nästa steg

När du har valt loggning-providern kan instrumentera dina program och tjänster, måste din loggar och händelser ska aggregeras innan de kan skickas till alla analysplattform. Läs mer om [Programinsikter](service-fabric-diagnostics-event-analysis-appinsights.md), [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md), och [BOMULLSTUSS](service-fabric-diagnostics-event-aggregation-wad.md) att bättre förstå några av de rekommenderade alternativ.
