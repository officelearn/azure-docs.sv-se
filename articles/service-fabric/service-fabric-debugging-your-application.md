---
title: "Felsöka ditt program i Visual Studio | Microsoft Docs"
description: "Förbättra tillförlitligheten och prestandan hos dina tjänster genom att utveckla och felsökning i Visual Studio dem på ett kluster för lokal utveckling."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: vturecek;mikhegn
ms.openlocfilehash: 2459025899a7f5ffebf44fa104ed112c0eb99dfa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Felsöka Service Fabric-program med hjälp av Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Felsöka ett lokala Service Fabric-program
Du kan spara tid och pengar genom att distribuera och felsöka ditt Azure Service Fabric-program i ett kluster för utveckling av lokal dator. Visual Studio 2017 eller Visual Studio 2015 kan distribuera programmet till det lokala klustret och felsökningsprogrammet ansluta automatiskt till alla instanser av programmet.

1. Starta en lokal utveckling klustret genom att följa stegen i [ställa in din utvecklingsmiljö för Service Fabric](service-fabric-get-started.md).
2. Tryck på **F5** eller klicka på **felsöka** > **Start Debugging**.
   
    ![Starta felsökning av ett program][startdebugging]
3. Ange brytpunkter i koden och stega igenom programmet genom att klicka på kommandon i den **felsöka** menyn.
   
   > [!NOTE]
   > Visual Studio bifogar till alla instanser av programmet. När du stega igenom koden hämta brytpunkter nådde av flera processer som resulterar i samtidiga sessioner. Prova att inaktivera brytpunkter när de träffar, genom att göra varje brytpunkt villkorlig på tråd-ID eller med hjälp av diagnostiska händelser.
   > 
   > 
4. Den **diagnostiska händelser** öppnas automatiskt så att du kan visa diagnostik händelser i realtid.
   
    ![Visa diagnostik händelser i realtid][diagnosticevents]
5. Du kan också öppna den **diagnostiska händelser** fönster i Cloud Explorer.  Under **Service Fabric**, högerklicka på en nod och välj **visa strömning spårningar**.
   
    ![Öppna fönstret diagnostiska händelser][viewdiagnosticevents]
   
    Om du vill filtrera dina spår till en specifik tjänst eller ett program kan bara aktivera strömmande spårningar på den specifika tjänsten eller programmet.
6. Diagnostiska händelser kan ses i den automatiskt genererade **ServiceEventSource.cs** filen och anropas från programkod.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. Den **diagnostiska händelser** fönstret stöder filtrering, pausa och undersökning av händelser i realtid.  Filtret är en sträng sökning i händelsemeddelandet, inklusive dess innehåll.
   
    ![Filtrera, pausa och återuppta eller granska händelser i realtid][diagnosticeventsactions]
8. Tjänster-felsökning är precis som du felsöker andra program. Du kommer normalt ange brytpunkter via Visual Studio för enkelt felsökning. Även om tillförlitliga samlingar replikeras över flera noder, implementerar de fortfarande IEnumerable. Detta innebär att du kan använda vyn resultat i Visual Studio när du felsöker för att se vad du har lagrat i. Ange en brytpunkt bara, var som helst i koden.
   
    ![Starta felsökning av ett program][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Felsöka fjärrprogram Service Fabric
Om Service Fabric-program körs på ett Service Fabric-kluster i Azure, kan du felsöka de direkt från Visual Studio.

> [!NOTE]
> Funktionen kräver [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) och [Azure SDK för .NET 2.9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> Fjärrfelsökning är avsedd för utveckling och testning scenarier och inte ska användas i produktionsmiljöer, på grund av påverkan på program som körs.
> 
> 

1. Navigera till klustret i **Cloud Explorer**, högerklicka och välj **aktivera felsökning**
   
    ![Aktivera fjärrfelsökning][enableremotedebugging]
   
    Detta kommer startar processen för att aktivera tillägget för fjärranslutna felsökning på klusternoder, samt nödvändiga nätverkskonfigurationer.
2. Högerklicka på noden i klustret i **Cloud Explorer**, och välj **koppla felsökare**
   
    ![Koppla felsökare][attachdebugger]
3. I den **koppla till process** dialogrutan, Välj den process som du vill felsöka och klicka på **bifoga**
   
    ![Välj process][chooseprocess]
   
    Namnet på processen du vill koppla till, lika med namnet på sammansättningen för tjänsten projektnamn.
   
    Felsökningsprogrammet ska kopplas till alla noder som kör processen.
   
   * I de fall där du felsöker en tillståndslös service, tillhör alla instanser av tjänsten på alla noder felsökningssessionen.
   * Om du felsöker en tillståndskänslig service blir den primära repliken av någon partition active och därför fångats av felsökningsprogrammet. Om den primära repliken flyttas under felsökningssessionen, bearbetning av repliken fortfarande att vara en del av felsökningssessionen.
   * För att fånga endast relevanta partitioner eller instanser av en viss tjänst kan använda du villkorlig brytpunkter bryta endast en specifik partition eller instansen.
     
     ![Villkorlig brytpunkt][conditionalbreakpoint]
     
     > [!NOTE]
     > Vi stöder för närvarande inte felsökning ett Service Fabric-kluster med flera instanser av samma körbara namn.
     > 
     > 
4. När du är klar med att felsöka ditt program kan du inaktivera fjärråtkomst felsökning tillägget genom att högerklicka på klustret i **Cloud Explorer** och välj **inaktivera felsökning**
   
    ![Inaktivera fjärrfelsökning][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Strömning spår från en fjärransluten klusternod
Du kan också att dataströmmen spårningar direkt från en fjärransluten klusternod till Visual Studio. Den här funktionen kan du dataströmmen ETW spåra händelser som genereras på en klusternod för Service Fabric.

> [!NOTE]
> Den här funktionen kräver [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) och [Azure SDK för .NET 2.9](https://azure.microsoft.com/downloads/).
> Den här funktionen har endast stöd för kluster som körs i Azure.
> 
> 

<!-- -->
> [!WARNING]
> Strömning spårningar är avsedd för utveckling och testning scenarier och inte ska användas i produktionsmiljöer, på grund av påverkan på program som körs.
> I ett produktionsscenario ska lita på vidarebefordran av händelser med hjälp av Azure-diagnostik.
> 
> 

1. Navigera till klustret i **Cloud Explorer**, högerklicka och välj **aktivera strömning spårningar**
   
    ![Aktivera remote strömmande spårningar][enablestreamingtraces]
   
    Detta kommer startar processen för att aktivera tillägget strömmande spårningar på klusternoder, samt nödvändiga nätverkskonfigurationer.
2. Expandera den **noder** element i **Cloud Explorer**, högerklicka på den nod som du vill strömma spår från och välj **visa strömning spårningar**
   
    ![Visa remote strömning spårningar][viewremotestreamingtraces]
   
    Upprepa steg 2 för så många noder som du vill visa spårningsinformation från. Varje noder dataström kommer att visas i ett dedikerat fönster.
   
    Du är nu kunna se spårningen sänds av Service Fabric och dina tjänster. Om du vill filtrera händelser så att endast ett visst program bara ange namnet på programmet i filtret.
   
    ![Visa strömning spårar][viewingstreamingtraces]
3. När du är klar strömmande spår från klustret, du kan inaktivera fjärråtkomst strömmande spårning, genom att högerklicka på klustret i **Cloud Explorer** och välj **inaktivera strömning spårningar**
   
    ![Inaktivera fjärråtkomst strömmande spårningar][disablestreamingtraces]

## <a name="next-steps"></a>Nästa steg
* [Testa en tjänst för Service Fabric](service-fabric-testability-overview.md).
* [Hantera Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).

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
