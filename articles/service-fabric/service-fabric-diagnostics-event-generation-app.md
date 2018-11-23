---
title: Azure Service Fabric-programnivå övervakning | Microsoft Docs
description: Läs mer om programmet och tjänsten på händelser och loggar som används för att övervaka och diagnostisera Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: e26dbc037c206635cfb92ea49f28d0f891e53ef8
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291274"
---
# <a name="application-logging"></a>Programloggning

Instrumentera din kod är inte bara ett sätt att få insikter om dina användare, utan också det enda sättet som du får kunskap om något är fel i ditt program och diagnostisera vad som behöver åtgärdas. Även om det är tekniskt möjligt att ansluta en felsökare till en tjänst för produktion, men det är inte vanligt. Därför är har detaljerad instrumentationsdata viktigt.

Vissa produkter instrumentera automatiskt din kod. Även om dessa lösningar kan fungera, är manuell instrumentation nästan alltid måste vara specifika för din affärslogik. Du måste ha tillräckligt med information för att felsöka kriminalteknikers programmet i slutändan. Service Fabric-program kan vara utrustade med valfritt loggningsramverk. Det här dokumentet beskriver några olika sätt att arrangera din kod och när du ska välja en metod framför en annan. 

Exempel på hur du använder dessa förslag finns [Lägg till loggning i Service Fabric-programmet](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>Application Insights SDK

Application Insights har en omfattande integrering med Service Fabric direkt ur lådan. Användare kan lägga till nuget-paket AI Service Fabric och ta emot data och loggfiler som skapas och som samlas in som kan visas i Azure-portalen. Dessutom kan uppmanas användarna att lägga till egna telemetri för att diagnostisera och felsöka sina program och spåra som tjänster och program används mest. Den [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) klass i SDK finns många sätt att spåra telemetri i dina program. Se ett exempel på hur du instrumenterar och Lägg till application insights i ditt program i våra självstudier för [övervakning och diagnos av ett .NET-program](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>Händelsekälla

När du skapar ett Service Fabric-lösning från en mall i Visual Studio, en **EventSource**-härledda klassen (**ServiceEventSource** eller **ActorEventSource**) genereras . En mall skapas, där du kan lägga till händelser för programmet eller tjänsten. Den **EventSource** namn **måste** vara unikt och bör ändras från mallen standardsträngen mitt företag -&lt;lösning&gt;-&lt;projekt &gt;. Att ha flera **EventSource** definitioner som använder samma namn leder till problem vid körning. Varje definierad händelse måste ha en unik identifierare. Ett körningsfel inträffar om en identifierare som inte är unikt. Vissa organisationer preassign intervall med värden för identifierare för att undvika konflikter mellan separat utvecklingsteam. Mer information finns i [Vance's blog](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) eller [MSDN-dokumentationen](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>ASP.NET Core-loggning

Det är viktigt att planera noggrant hur du kommer instrumenterar koden. Planen rätt instrumentation kan hjälpa dig att undvika potentiellt destabilizing din kodbas och sedan behöva reinstrument koden. För att minska risken kan du välja ett bibliotek med instrumentation som [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), vilket är en del av Microsoft ASP.NET Core. ASP.NET Core har en [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) gränssnitt som du kan använda med önskat provideralternativ, samtidigt som minimeras effekten på befintlig kod. Du kan använda koden i ASP.NET Core i Windows och Linux och i fullständiga .NET Framework, så koden instrumentation är standardiserat.

## <a name="next-steps"></a>Nästa steg

När du har valt loggning leverantören för att instrumentera dina program och tjänster, måste dina loggar och händelser ska aggregeras innan de kan skickas till alla analysis-plattformar. Läs mer om [Programinsikter](service-fabric-diagnostics-event-analysis-appinsights.md) och [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) att bättre förstå några av de Azure Monitor rekommenderade alternativ.
