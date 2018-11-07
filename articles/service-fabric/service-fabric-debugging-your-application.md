---
title: Felsöka programmet i Visual Studio | Microsoft Docs
description: Förbättra tillförlitlighet och prestanda för dina tjänster genom att utveckla och felsöka dem i Visual Studio på ett lokalt utvecklingskluster.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: f16bf06d03511223b54542ad7a389f78d37f3ea9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235079"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Felsöka Service Fabric-program med hjälp av Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Felsöka ett lokalt Service Fabric-program
Du kan spara tid och pengar genom att distribuera och felsöka ditt Azure Service Fabric-program i ett kluster för utveckling av lokala datorn. Visual Studio 2017 eller Visual Studio 2015 kan distribuera programmet till det lokala klustret och ansluter automatiskt felsökningsprogrammet till alla instanser av programmet.

1. Starta ett lokalt utvecklingskluster genom att följa stegen i [ställa in din utvecklingsmiljö för Service Fabric](service-fabric-get-started.md).
2. Tryck på **F5** eller klicka på **felsöka** > **Starta felsökning**.
   
    ![Starta felsökning av program][startdebugging]
3. Ange brytpunkter i din kod och gå igenom programmet genom att klicka på kommandon i den **felsöka** menyn.
   
   > [!NOTE]
   > Visual Studio kopplar till alla instanser av programmet. Medan du Stega dig igenom koden, kan du hämta träffa brytpunkter av flera processer, vilket resulterar i samtidiga sessioner. Prova att inaktivera brytpunkterna när de träffar, genom att göra varje brytpunkt villkorlig, baserat på tråd-ID eller genom att använda diagnostikhändelser.
   > 
   > 
4. Den **diagnostikhändelser** fönster öppnas automatiskt så att du kan visa diagnostikhändelser i realtid.
   
    ![Visa diagnostiska händelser i realtid][diagnosticevents]
5. Du kan också öppna den **diagnostikhändelser** fönster i Cloud Explorer.  Under **Service Fabric**, högerklicka på varje nod och välj **View Streaming spårningar**.
   
    ![Öppna fönstret diagnostikhändelser][viewdiagnosticevents]
   
    Om du vill filtrera dina spårningar till en viss tjänst eller program kan du bara aktivera strömmande spår på den specifika tjänsten eller programmet.
6. De diagnostiska händelserna kan ses i den automatiskt genererade **ServiceEventSource.cs** filen och anropas från programkod.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. Den **diagnostikhändelser** fönster stöder filtrering, pausar och granska händelser i realtid.  Filtret är en enkel sträng-sökning i händelsemeddelandet, inklusive dess innehåll.
   
    ![Filtrera, pausa och återuppta eller granska händelser i realtid][diagnosticeventsactions]
8. Felsökning av tjänster som är som felsökning alla andra program. Du kommer normalt ange brytpunkter via Visual Studio för enkel felsökning. Även om Reliable Collections replikeras över flera noder, implementerar de fortfarande IEnumerable. Detta innebär att du kan använda vyn resultat i Visual Studio när du felsöker för att se vad du har lagrat i. Konfigurera en brytpunkt helt enkelt, var som helst i din kod.
   
    ![Starta felsökning av program][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Felsök en fjärransluten Service Fabric-program
Om Service Fabric-program körs på ett Service Fabric-kluster i Azure kan kan du felsöka de direkt från Visual Studio.

> [!NOTE]
> Funktionen kräver [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) och [Azure SDK för .NET 2.9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> Fjärrfelsökning är avsedd för utveckling och testning och inte ska användas i produktionsmiljöer, på grund av effekten på program som körs.
> 
> 

1. Navigera till ditt kluster i **Cloud Explorer**, högerklicka och välj **aktivera felsökning**
   
    ![Aktivera fjärrfelsökning][enableremotedebugging]
   
    Det här startar processen för att aktivera tillägget för fjärranslutna felsökning på klusternoder, samt nödvändiga konfigurationer.
2. Högerklicka på noden i klustret i **Cloud Explorer**, och välj **bifoga felsökningsprogrammet**
   
    ![Koppla felsökare][attachdebugger]
3. I den **koppla till process** dialogrutan, Välj den process som du vill felsöka och klicka på **Attach**
   
    ![Välj process][chooseprocess]
   
    Namnet på processen som du vill koppla till, är lika med namnet på sammansättningen för tjänsten projektnamn.
   
    Felsökningsprogrammet ska kopplas till alla noder som kör processen.
   
   * I fall där du felsöker en tillståndslös tjänst ingår alla instanser av tjänsten på alla noder i felsökningssessionen.
   * Om du felsöker en tillståndskänslig tjänst, blir den primära repliken av någon partition active och därför fångats av felsökningsprogrammet. Om den primära repliken flyttar under felsökningssessionen, kan fortfarande en del av felsökningssessionen bearbetningen av den repliken.
   * För att fånga endast relevanta partitioner eller instanser av en viss tjänst kan använda du villkorlig brytpunkter bara dela en specifik partition eller instans.
     
     ![Villkorlig brytpunkt][conditionalbreakpoint]
     
     > [!NOTE]
     > Vi stöder för närvarande inte felsökning Service Fabric-kluster med flera instanser av samma körbara namn.
     > 
     > 
4. När du är klar med att felsöka ditt program kan du inaktivera fjärråtkomst felsökning tillägget genom att högerklicka på klustret i **Cloud Explorer** och välj **inaktivera felsökning**
   
    ![Inaktivera fjärrfelsökning][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Strömmande spårningar från en fjärransluten klusternod
Du kan också till stream spårningar direkt från en fjärransluten klusternod i Visual Studio. Den här funktionen kan du stream ETW spåra händelser som genereras på en Service Fabric-klusternod.

> [!NOTE]
> Den här funktionen kräver [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) och [Azure SDK för .NET 2.9](https://azure.microsoft.com/downloads/).
> Den här funktionen stöder bara kluster som körs i Azure.
> 
> 

<!-- -->
> [!WARNING]
> Strömning spårningar är avsedd för utveckling och testning och inte ska användas i produktionsmiljöer, på grund av effekten på program som körs.
> I ett produktionsscenario lita på vidarebefordran av händelser med hjälp av Azure Diagnostics.
> 
> 

1. Navigera till ditt kluster i **Cloud Explorer**, högerklicka och välj **aktivera strömning spårningar**
   
    ![Aktivera remote strömmande spårningar][enablestreamingtraces]
   
    Det här startar processen för att aktivera tillägget strömmande spår på klusternoder, samt nödvändiga konfigurationer.
2. Expandera den **noder** element i **Cloud Explorer**, högerklicka på noden som du vill strömma spårningar från och välj **View Streaming spårningar**
   
    ![Visa remote streaming spårningar][viewremotestreamingtraces]
   
    Upprepa steg 2 för så många noder som du vill se spårningar från. Varje noder stream visas i ett dedikerat fönster.
   
    Du kan nu att se spårningarna orsakats av Service Fabric och dina tjänster. Om du vill filtrera händelser för att endast visa ett visst program bara ange namnet på programmet i filtret.
   
    ![Visa spår för direktuppspelning][viewingstreamingtraces]
3. När du är klar strömmande spårningar från ditt kluster, du kan inaktivera fjärråtkomst strömmande spårningar, genom att högerklicka på klustret i **Cloud Explorer** och välj **inaktivera direktuppspelning spårningar**
   
    ![Inaktivera fjärråtkomst strömmande spårningar][disablestreamingtraces]

## <a name="next-steps"></a>Nästa steg
* [Testa en Service Fabric-tjänst](service-fabric-testability-overview.md).
* [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
