---
title: Felsöka Azure mikrotjänster i Windows | Microsoft Docs
description: Lär dig mer om att övervaka och diagnostisera dina tjänster som skrivits med Microsoft Azure Service Fabric på en dator för lokal utveckling.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 93cf4985e94c0af480d9f4e2e38b792cffe4df6e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206189"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Övervaka och diagnostisera tjänster i en inställning för utveckling av lokal dator
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Övervaka, identifiera, diagnostisera och felsöka Tillåt för tjänster att fortsätta med minimala störningar för användarupplevelsen. Övervakning och diagnostik är viktiga i en verklig distribuerade produktionsmiljön, beror effektiviteten på införandet av en liknande modell under utvecklingen av tjänster så att de fungerar när du flyttar till en verklig installation. Service Fabric gör det enkelt för tjänstutvecklare att implementera diagnostik fungerar sömlöst över både inställningar för enskild dator lokal utveckling och produktion verkliga klustret installationsprogram.

## <a name="event-tracing-for-windows"></a>Händelsespårning för Windows
[Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) är den rekommenderade tekniken för spårning av meddelanden i Service Fabric. Några av fördelarna med att använda ETW är:

* **ETW är snabbt.** Det har skapats som en spårning-teknik som har minimal inverkan på koden körningstider.
* **ETW-spårning fungerar sömlöst över lokala utvecklingsmiljöer och verkliga klustret inställningar.** Det innebär att du inte behöver skriva om koden och spårning när du är redo att distribuera din kod till verkliga klustret.
* **Service Fabric system koden används även ETW för interna spårning.** På så sätt kan du visa programspårningar överlagrat med Service Fabric system spårningar. Det hjälper dig också att lättare förstå sekvenser och relationer mellan din programkod och händelser i det underliggande systemet.
* **Det finns inbyggt stöd i Visual Studio för Service Fabric-verktyg för att visa ETW-händelser.** ETW-händelser som visas i vyn diagnostiska händelser i Visual Studio när Visual Studio är korrekt konfigurerad med Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Visa händelser för Service Fabric-system i Visual Studio
Service Fabric avger ETW-händelser för att förstå vad som händer i plattformen för programutvecklare. Om du inte redan gjort det. Gå vidare och följ stegen i [skapa ditt första program i Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md). Den här informationen hjälper dig att få ett program körs med diagnostik Loggboken visar trace-meddelanden.

1. Om diagnostik händelser fönster inte visas automatiskt, går till den **visa** i Visual Studio, Välj **andra fönster** och sedan **diagnostiska Loggboken**.
2. Varje händelse har standard metadatainformation om den noden, program och tjänsten händelsen kommer från. Du kan också filtrera listan över händelser med hjälp av den **Filtrera händelser** rutan längst upp i fönstret händelser. Du kan till exempel filtrera på **nodnamnet** eller **tjänstnamn.** Och när du tittar på händelseinformationen, du kan också pausa med hjälp av den **pausa** längst upp i fönstret händelser och återuppta senare utan att förlora händelser.
   
   ![Visual Studio diagnostik Loggboken](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Lägg till dina egna anpassade spår i programkoden
Service Fabric Visual Studio-projektmallar innehåller exempelkod. Koden visar hur du lägger till anpassad kod ETW programspårningar som visas i visningsprogrammet för Visual Studio ETW tillsammans med system spår från Service Fabric. Fördelen med den här metoden är att metadata läggs automatiskt till spårningar och av Visual Studio diagnostiska Loggboken har redan konfigurerats för att visa dem.

För projekt som skapas från den **tjänstmallar** (tillståndslösa och tillståndskänsliga) bara söka efter den `RunAsync` implementering:

1. Anropet till `ServiceEventSource.Current.ServiceMessage` i den `RunAsync` metoden visar ett exempel på en anpassad ETW-spårning från programkoden.
2. I den **ServiceEventSource.cs** filen hittar du en överlagring för den `ServiceEventSource.ServiceMessage` metod som ska användas för hög frekvens händelser på grund av prestandaskäl.

För projekt som skapas från den **aktören mallar** (tillståndslösa och tillståndskänsliga):

1. Öppna den **”projektnamn” .cs** filen var *projektnamn* är det namn du angav för Visual Studio-projekt.  
2. Sök efter koden `ActorEventSource.Current.ActorMessage(this, "Doing Work");` i den *DoWorkAsync* metod.  Detta är ett exempel på en anpassad ETW-spårning som skrivs från programkod.  
3. I filen **ActorEventSource.cs**, hittar du en överlagring för den `ActorEventSource.ActorMessage` metod som ska användas för hög frekvens händelser på grund av prestandaskäl.

När du lägger till anpassade ETW-spårning i koden för tjänsten, kan du skapa, distribuera och kör programmet igen för att se din händelse(r) i visningsprogrammet diagnostiska händelser. Om du felsöker programmet med **F5**, av diagnostiska Loggboken öppnas automatiskt.

## <a name="next-steps"></a>Nästa steg
Samma spårning kod som du lagt till ditt program ovan för lokala diagnostik fungerar med verktyg som du kan använda för att visa dessa händelser när du kör programmet på ett Azure-kluster. Gå igenom dessa artiklar som beskrivs de olika alternativen för verktyg och beskriver hur du kan konfigurera dem.

* [Hur du samlar in loggar med Azure-diagnostik](service-fabric-diagnostics-how-to-setup-wad.md)
* [Aggregering av händelse och med EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

