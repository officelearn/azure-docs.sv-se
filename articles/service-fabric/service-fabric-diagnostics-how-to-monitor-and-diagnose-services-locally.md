---
title: Felsöka Azure Service Fabric-appar i Windows
description: Lär dig hur du övervakar och diagnostiserar dina tjänster som skrivs med Microsoft Azure Service Fabric på en lokal utvecklings dator.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8435bb82afddd0070679768bb8d22ad9290f2279
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258517"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Övervaka och diagnostisera tjänster i en lokal dator utvecklings installation
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Övervakning, identifiering, diagnostisering och fel sökning av tjänster för att kunna fortsätta med minimalt avbrott i användar upplevelsen. Även om övervakning och diagnostik är kritiskt i en verklig distribuerad produktions miljö, beror effektiviteten på införandet av en liknande modell under utvecklingen av tjänster för att säkerställa att de fungerar när du går vidare till en verklig installation. Service Fabric gör det enkelt för tjänst utvecklare att implementera diagnostik som fungerar smidigt i både lokala utvecklings installationer med en enda dator och verkliga produktions kluster inställningar.

## <a name="event-tracing-for-windows"></a>ETW (Event Tracing for Windows)
[ETW (Event tracing for Windows)](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) är den rekommenderade tekniken för att spåra meddelanden i Service Fabric. Några fördelar med att använda ETW är:

* **ETW är snabbt.** Den har skapats som en spårnings teknik som har minimal påverkan på kod körnings tider.
* **ETW-spårning fungerar sömlöst i lokala utvecklings miljöer och även verkliga kluster inställningar.** Det innebär att du inte behöver skriva om spårnings koden när du är redo att distribuera din kod till ett verkligt kluster.
* **Service Fabric system kod använder också ETW för intern spårning.** På så sätt kan du Visa dina program spårningar som är överlagrade med Service Fabric system spårningar. Det hjälper dig också att lättare förstå sekvenser och relationer mellan program koden och händelser i det underliggande systemet.
* **Det finns inbyggt stöd i Service Fabric Visual Studio-verktyg för att Visa ETW-händelser.** ETW-händelser visas i vyn diagnostiska händelser i Visual Studio när Visual Studio är korrekt konfigurerad med Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Visa Service Fabric system händelser i Visual Studio
Service Fabric genererar ETW-händelser för att hjälpa utvecklare att förstå vad som händer i plattformen. Om du inte redan har gjort det går du vidare och följer stegen i [skapa ditt första program i Visual Studio](service-fabric-tutorial-create-dotnet-app.md). Den här informationen hjälper dig att komma igång med ett program med verktyget för diagnostik-händelser som visar spårnings meddelandena.

1. Om fönstret diagnostik-händelser inte visas automatiskt går du till fliken **Visa** i Visual Studio, väljer **andra fönster** och sedan **diagnostiska händelse visare**.
2. Varje händelse innehåller information om standardmetadata som visar vilken nod, vilket program och vilken tjänst händelsen kommer från. Du kan också filtrera listan över händelser genom att använda rutan **Filtrera händelser** överst i fönstret händelser. Du kan till exempel filtrera efter **nodnamn** eller **tjänst namn.** När du tittar på händelse information kan du också pausa med hjälp av knappen **pausa** överst i fönstret händelser och återuppta senare utan att några händelser går förlorade.
   
   ![Visnings program för Visual Studio Diagnostics-händelser](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Lägg till dina egna anpassade spårningar i program koden
Service Fabric Visual Studio-projektmallar innehåller exempel kod. Koden visar hur du lägger till anpassade ETW-spår för program kod som visas i Visual Studio ETW Viewer tillsammans med system spårningar från Service Fabric. Fördelen med den här metoden är att metadata läggs automatiskt till spårningar och av Visual Studio diagnostiska Loggboken har redan konfigurerats för att visa dem.

För projekt som skapats från **tjänstmallar** (tillstånds lös eller tillstånds känslig) söker du bara efter `RunAsync` implementering:

1. Anropet till `ServiceEventSource.Current.ServiceMessage` i `RunAsync`-metoden visar ett exempel på en anpassad ETW-spårning från program koden.
2. I **ServiceEventSource.cs** -filen hittar du en överlagring för den `ServiceEventSource.ServiceMessage` metod som ska användas för händelser med hög frekvens på grund av prestanda skäl.

För projekt som skapats från **aktörens mallar** (tillstånds lös eller tillstånds känslig):

1. Öppna filen **"ProjectName". CS "** där *ProjectName* är det namn som du valde för ditt Visual Studio-projekt.  
2. Hitta koden `ActorEventSource.Current.ActorMessage(this, "Doing Work");` i *DoWorkAsync* -metoden.  Detta är ett exempel på en anpassad ETW-spårning skriven från program kod.  
3. I filen **ActorEventSource.cs**hittar du en överlagring för den `ActorEventSource.ActorMessage` metod som ska användas för händelser med hög frekvens på grund av prestanda skäl.

När du har lagt till anpassad ETW-spårning till din service kod kan du skapa, distribuera och köra programmet igen för att se dina händelser i visnings programmet för diagnostiska händelser. Om du felsöker programmet med **F5**öppnas fönstret diagnostiska händelser automatiskt.

## <a name="next-steps"></a>Nästa steg
Samma spårnings kod som du har lagt till i programmet ovan för lokal diagnostik fungerar med verktyg som du kan använda för att visa dessa händelser när du kör programmet i ett Azure-kluster. Kolla in de här artiklarna som diskuterar de olika alternativen för verktygen och beskriver hur du kan ställa in dem.

* [Samla in loggar med Azure-diagnostik](service-fabric-diagnostics-how-to-setup-wad.md)
* [Händelse agg regering och insamling med EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

