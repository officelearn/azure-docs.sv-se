---
title: Felsöka Azure Service Fabric-appar i Windows
description: Lär dig hur du övervakar och diagnostiserar dina tjänster som skrivits med Microsoft Azure Service Fabric på en dator för lokal utveckling.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8435bb82afddd0070679768bb8d22ad9290f2279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258517"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Övervaka och diagnostisera tjänster i en konfiguration för utveckling lokalt
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Övervakning, identifiering, diagnos och felsökning gör det möjligt för tjänster att fortsätta med minimala störningar i användarupplevelsen. Även om övervakning och diagnostik är avgörande i en verklig distribuerad produktionsmiljö, kommer effektiviteten att bero på att anta en liknande modell under utveckling av tjänster för att säkerställa att de fungerar när du flyttar till en verklig installation. Service Fabric gör det enkelt för tjänstutvecklare att implementera diagnostik som sömlöst kan fungera både för lokala utvecklingsinställningar för en enda dator och verkliga produktionskluster.

## <a name="event-tracing-for-windows"></a>Händelsespårning för Windows
[Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) är den rekommenderade tekniken för att spåra meddelanden i Service Fabric. Några fördelar med att använda ETW är:

* **ETW är snabb.** Den byggdes som en spårningsteknik som har minimal inverkan på kodkörningstider.
* **ETW-spårning fungerar sömlöst i lokala utvecklingsmiljöer och även verkliga klusterkonfigurationer.** Det innebär att du inte behöver skriva om spårningskoden när du är redo att distribuera koden till ett riktigt kluster.
* **Service Fabric systemkod använder också ETW för intern spårning.** På så sätt kan du visa dina programspårningar interfolierade med Service Fabric-systemspårningar. Det hjälper dig också att lättare förstå sekvenser och samband mellan din programkod och händelser i det underliggande systemet.
* **Det finns inbyggt stöd i Service Fabric Visual Studio-verktyg för att visa ETW-händelser.** ETW-händelser visas i vyn Diagnostiska händelser i Visual Studio när Visual Studio har konfigurerats korrekt med Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Visa systemhändelser för Service Fabric i Visual Studio
Service Fabric avger ETW-händelser för att hjälpa programutvecklare att förstå vad som händer i plattformen. Om du inte redan har gjort det följer du stegen i [Skapa ditt första program i Visual Studio](service-fabric-tutorial-create-dotnet-app.md). Den här informationen hjälper dig att få igång ett program med Diagnostikhändelsevisaren som visar spårningsmeddelandena.

1. Om diagnostikhändelserfönstret inte visas automatiskt går du till fliken **Visa** i Visual Studio, väljer **Andra Fönster** och sedan **Diagnostikhändelsevisaren**.
2. Varje händelse har standardmetadatainformation som talar om för dig om noden, programmet och tjänsten som händelsen kommer från. Du kan också filtrera listan över händelser med hjälp av rutan **Filterhändelser** högst upp i händelsefönstret. Du kan till exempel filtrera på **Nodnamn** eller **Servicenamn.** Och när du tittar på händelseinformation kan du också pausa genom att använda **pausknappen** högst upp i händelsefönstret och återuppta senare utan att förlora händelser.
   
   ![Visual Studio Diagnostik Händelser Viewer](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Lägga till egna anpassade spårningar i programkoden
Projektmallarna För Service Fabric Visual Studio innehåller exempelkod. Koden visar hur du lägger till anpassade programkod ETW-spår som visas i Visual Studio ETW-visningen tillsammans med systemspårningar från Service Fabric. Fördelen med den här metoden är att metadata automatiskt läggs till i spårningar och Visual Studio Diagnostic Events Viewer är redan konfigurerad för att visa dem.

För projekt som skapats från **tjänstmallarna (tillståndslösa** eller tillståndskänsliga) sök bara efter implementeringen: `RunAsync`

1. Anropet `ServiceEventSource.Current.ServiceMessage` `RunAsync` i metoden visar ett exempel på en anpassad ETW-spårning från programkoden.
2. I **ServiceEventSource.cs-filen** hittar du en överbelastning `ServiceEventSource.ServiceMessage` för den metod som ska användas för högfrekventa händelser på grund av prestandaskäl.

För projekt som skapats från **aktörsmallarna (tillståndslösa** eller tillståndskänsliga):

1. Öppna filen **"ProjectName".cs** där *ProjectName* är det namn du valde för Visual Studio-projektet.  
2. Leta reda `ActorEventSource.Current.ActorMessage(this, "Doing Work");` på koden i metoden *DoWorkAsync.*  Detta är ett exempel på en anpassad ETW-spårning skriven från programkod.  
3. I fil **ActorEventSource.cs**hittar du en överbelastning för den `ActorEventSource.ActorMessage` metod som ska användas för högfrekventa händelser på grund av prestandaskäl.

När du har lagt till anpassad ETW-spårning i tjänstkoden kan du skapa, distribuera och köra programmet igen för att se dina händelser i Diagnostikhändelsevisaren. Om du felsöker programmet med **F5**öppnas Diagnostikhändelsevisaren automatiskt.

## <a name="next-steps"></a>Nästa steg
Samma spårningskod som du har lagt till i ditt program ovan för lokal diagnostik fungerar med verktyg som du kan använda för att visa dessa händelser när du kör ditt program i ett Azure-kluster. Kolla in de här artiklarna som beskriver de olika alternativen för verktygen och beskriver hur du kan ställa in dem.

* [Samla in loggar med Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md)
* [Händelseaggregering och samling med EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

