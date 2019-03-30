---
title: Felsöka Azure Service Fabric-appar i Windows | Microsoft Docs
description: Lär dig mer om att övervaka och diagnostisera dina tjänster som skrivits med Microsoft Azure Service Fabric på en dator för lokal utveckling.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 31c559c1ab314b7e1f29bd96f74d6d82cfcc0420
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670055"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Övervaka och diagnostisera tjänster i en inställning för utveckling av lokal dator
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Övervakning, identifiera, diagnostisera och felsöka kan efter tjänster att fortsätta med minimala störningar för användarupplevelsen. Övervakning och diagnostik är avgörande i ett faktiskt distribuerade produktionsmiljö, beror effektiviteten på att börja använda en liknande modell under utvecklingen av tjänster så att de fungerar när du flyttar till en verklig installation. Service Fabric gör det enkelt för tjänstutvecklare att implementera diagnostik som fungerar sömlöst både enkel dator lokal utveckling inställningar och inställningar för verklig produktion-kluster.

## <a name="event-tracing-for-windows"></a>Event Tracing for Windows
[Event Tracing för Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) är den rekommendera tekniken för spårning av meddelanden i Service Fabric. Vissa fördelarna med att använda ETW är:

* **ETW går snabbt.** Det har skapats som en spårning av teknik som har minimal inverkan på kod körningstider.
* **ETW-spårning fungerar sömlöst över lokala utvecklingsmiljö och verkliga kluster inställningar.** Det innebär att du inte behöver skriva om spårning av koden när du är redo att distribuera koden till ett verkligt kluster.
* **Service Fabric system koden använder också ETW för interna spårning.** På så sätt kan du visa programspårningar överlagrad med Service Fabric system spårningar. Det hjälper dig också att lättare förstå sekvenser och relationer mellan din programkod och händelser i det underliggande systemet.
* **Det finns inbyggt stöd i Visual Studio för Service Fabric-verktyg för att visa ETW-händelser.** ETW-händelser som visas i vyn diagnostikhändelser av Visual Studio när Visual Studio är korrekt konfigurerad med Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Visa händelser för Service Fabric-system i Visual Studio
Service Fabric genererar ETW-händelser för att hjälpa programutvecklare att förstå vad som händer i plattformen. Om du inte redan gjort det, gå vidare och följ stegen i [skapar ditt första program i Visual Studio](service-fabric-tutorial-create-dotnet-app.md). Den här informationen hjälper dig att komma igång ett program med diagnostik Loggboken visar spårningsmeddelanden.

1. Om diagnostiken händelser fönstret inte visar automatiskt, går till den **visa** fliken i Visual Studio **andra Windows** och sedan **Loggboken diagnostik**.
2. Varje händelse har standard metadatainformation om den noden, program och tjänsten som händelsen kommer från. Du kan också filtrera listan över händelser med hjälp av den **Filterhändelser** högst upp i fönstret händelser. Du kan till exempel filtrera på **nodnamnet** eller **tjänstnamn.** Och när du granskar informationen om händelsen, du kan också pausa med hjälp av den **pausa** längst upp i fönstret händelser och återuppta senare utan att förlora händelser.
   
   ![Loggboken diagnostik för Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Lägg till dina egna anpassade spårningar till programkoden
Service Fabric Visual Studio-projektmallar innehåller exempelkoden. Koden visar hur du lägger till anpassade program kod ETW-spårning som visas i visningsprogrammet för Visual Studio-ETW tillsammans med system spårningar från Service Fabric. Fördelen med den här metoden är att metadata läggs automatiskt till spårningar och av Visual Studio diagnostiska Loggboken har redan konfigurerats för att visa dem.

För projekt som skapas från den **tjänstmallar** (tillståndslösa och tillståndskänsliga) bara söka efter den `RunAsync` implementering:

1. Anropet till `ServiceEventSource.Current.ServiceMessage` i den `RunAsync` metoden visar ett exempel på en anpassad ETW-spårning från programkoden.
2. I den **ServiceEventSource.cs** -fil hittar du en överlagring för den `ServiceEventSource.ServiceMessage` metod som ska användas för hög frekvens händelser på grund av prestandaskäl.

För projekt som skapats från den **aktören mallar** (tillståndslösa eller tillståndskänsliga):

1. Öppna den **”ProjectName” .cs** filen var *ProjectName* är det namn som du valde för Visual Studio-projektet.  
2. Koden `ActorEventSource.Current.ActorMessage(this, "Doing Work");` i den *DoWorkAsync* metod.  Det här är ett exempel på en anpassad ETW-spårning som skrivs från programkod.  
3. I filen **ActorEventSource.cs**, hittar du en överlagring för den `ActorEventSource.ActorMessage` metod som ska användas för hög frekvens händelser på grund av prestandaskäl.

När du lägger till anpassade ETW-spårning i koden för tjänsten måste du bygga, distribuera och köra programmet igen för att se dina händelser i Loggboken diagnostik. Om du felsöker programmet med **F5**, Loggboken diagnostik öppnas automatiskt.

## <a name="next-steps"></a>Nästa steg
Samma spårning av kod som du lade till ditt program ovan för lokal diagnostik fungerar med verktyg som du kan använda för att visa dessa händelser när du kör programmet på ett Azure-kluster. Läs dessa artiklar som beskrivs de olika alternativen för verktyg och beskriver hur du kan konfigurera dem.

* [Samla in loggar med Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md)
* [Händelsen aggregering och samling med EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

