---
title: Felsöka ditt program i Visual Studio
description: Förbättra tillförlitligheten och prestandan hos dina tjänster genom att utveckla och felsöka dem i Visual Studio i ett lokalt utvecklingskluster.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: fff8a19d5643f7ce866c9eb9c57486340b6f8a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624141"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Felsöka ditt Service Fabric-program med Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/ Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Felsöka ett lokalt Service Fabric-program
Du kan spara tid och pengar genom att distribuera och felsöka ditt Azure Service Fabric-program i ett lokalt datorutvecklingskluster. Visual Studio 2019 eller 2015 kan distribuera programmet till det lokala klustret och automatiskt ansluta felsökningaren till alla instanser av ditt program. Visual Studio måste köras som administratör för att kunna ansluta felsökningen.

1. Starta ett lokalt utvecklingskluster genom att följa stegen i [Konfigurera utvecklingsmiljön för serviceinfrastruktur](service-fabric-get-started.md).
2. Tryck på **F5** eller klicka på **Felsökning** > **Avsöka**.
   
    ![Börja felsöka ett program][startdebugging]
3. Ange brytpunkter i koden och gå igenom programmet genom att klicka på kommandon på **Felsökningsmenyn.**
   
   > [!NOTE]
   > Visual Studio bifogar alla instanser av ditt program. Medan du går igenom kod kan brytpunkter träffas av flera processer som resulterar i samtidiga sessioner. Försök att inaktivera brytpunkterna när de har träffats, genom att göra varje brytpunkt beroende av tråd-ID eller med hjälp av diagnostiska händelser.
   > 
   > 
4. Fönstret **Diagnostiska händelser** öppnas automatiskt så att du kan visa diagnostikhändelser i realtid.
   
    ![Visa diagnostiska händelser i realtid][diagnosticevents]
5. Du kan också öppna fönstret **Diagnostikhändelser** i Cloud Explorer.  Högerklicka på en nod under **Service Fabric**och välj **Visa strömmande spårningar**.
   
    ![Öppna fönstret för diagnostiska händelser][viewdiagnosticevents]
   
    Om du vill filtrera dina spårningar till en viss tjänst eller ett visst program aktiverar du direktuppspelade spårningar på den specifika tjänsten eller programmet.
6. Diagnostikhändelserna kan ses i den automatiskt genererade **ServiceEventSource.cs** filen och anropas från programkoden.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. Fönstret **Diagnostiska händelser** stöder filtrering, pausning och granskning av händelser i realtid.  Filtret är en enkel strängsökning av händelsemeddelandet, inklusive dess innehåll.
   
    ![Filtrera, pausa och återuppta eller inspektera händelser i realtid][diagnosticeventsactions]
8. Felsökningstjänster är som att felsöka alla andra program. Du anger normalt brytpunkter via Visual Studio för enkel felsökning. Även om tillförlitliga samlingar replikeras över flera noder implementerar de fortfarande IEnumerable. Den här implementeringen innebär att du kan använda resultatvyn i Visual Studio medan du felsöker för att se vad du har lagrat inuti. Om du vill göra det anger du en brytpunkt var som helst i koden.
   
    ![Börja felsöka ett program][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Köra ett skript som en del av felsökning
I vissa fall kan du behöva köra ett skript som en del av att starta en felsökningssession (t.ex. när du inte använder Standardtjänster).

I Visual Studio kan du lägga till en fil som heter **Start-Service.ps1** i mappen **Skript** i serviceprogrammet (.sfproj). Skriptet anropas när programmet har skapats i det lokala klustret.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Felsöka ett fjärrprogram för tjänstinfrastruktur
Om dina Service Fabric-program körs på ett Service Fabric-kluster i Azure kan du fjärrsã¶ja dessa program direkt från Visual Studio.

> [!NOTE]
> Funktionen kräver [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) och [Azure SDK för .NET 2.9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [!WARNING]
> Fjärrfelsökning är avsedd för utvecklings-/testscenarier och inte användas i produktionsmiljöer, på grund av påverkan på program som körs.

1. Navigera till klustret i **Cloud Explorer**. Högerklicka och välj **Aktivera felsökning**
   
    ![Aktivera fjärrfelsökning][enableremotedebugging]
   
    Den här åtgärden startar processen med att aktivera fjärrfelsökningstillägget på klusternoderna och nödvändiga nätverkskonfigurationer.
2. Högerklicka på klusternoden i **Cloud Explorer**och välj **Bifoga felsökningsmedel**
   
    ![Bifoga felsökning][attachdebugger]
3. I dialogrutan **Bifoga till process** väljer du den process du vill felsöka och klickar på **Bifoga**
   
    ![Välj process][chooseprocess]
   
    Namnet på den process som du vill bifoga till är lika med namnet på ditt monteringsnamn för serviceprojekt.
   
    Felsökningsfelet kommer att bifoga till alla noder som kör processen.
   
   * Om du felsöker en tillståndslös tjänst är alla instanser av tjänsten på alla noder en del av felsökningssessionen.
   * Om du felsöker en tillståndskänslig tjänst är endast den primära repliken för en partition aktiv och fångas därför av felsökningen. Om den primära repliken flyttas under felsökningssessionen kommer bearbetningen av repliken fortfarande att vara en del av felsökningssessionen.
   * Om du bara vill fånga upp relevanta partitioner eller instanser av en viss tjänst kan du använda villkorliga brytpunkter för att bara bryta en viss partition eller instans.
     
     ![Villkorlig brytpunkt][conditionalbreakpoint]
     
     > [!NOTE]
     > För närvarande stöder vi inte felsökning av ett Service Fabric-kluster med flera instanser av samma körbara tjänstnamn.
     > 
     > 
4. När du har felsökt programmet kan du inaktivera fjärrfelsökningstillägget genom att högerklicka på klustret i **Cloud Explorer** och välja **Inaktivera felsökning**
   
    ![Inaktivera felsökning av fjärrfelsökning][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Strömmande spår från en fjärrklusternod
Du kan också strömma spår direkt från en fjärrklusternod till Visual Studio. Med den här funktionen kan du strömma ETW-spårningshändelser som produceras på en klusternod för Service Fabric.

> [!NOTE]
> Den här funktionen kräver [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) och [Azure SDK för .NET 2.9](https://azure.microsoft.com/downloads/).
> Den här funktionen stöder bara kluster som körs i Azure.
> 
> 

<!-- -->
> [!WARNING]
> Strömmande spårningar är avsedda för utvecklings-/testscenarier och inte användas i produktionsmiljöer, på grund av påverkan på program som körs.
> I ett produktionsscenario bör du förlita dig på vidarebefordran av händelser med Azure Diagnostics.

1. Navigera till klustret i **Cloud Explorer**. Högerklicka och välj **Aktivera strömningsspårningar**
   
    ![Aktivera spårningar för fjärrströmning][enablestreamingtraces]
   
    Den här åtgärden startar processen för att aktivera tillägget strömmande spårningar på klusternoderna samt nödvändiga nätverkskonfigurationer.
2. Expandera **noderelementet** i **Cloud Explorer**, högerklicka på den nod som du vill strömma spår från och välj **Visa strömmande spårningar**
   
    ![Visa spårningar för fjärrströmning][viewremotestreamingtraces]
   
    Upprepa steg 2 för så många noder som du vill se spår från. Varje noder ström kommer att dyka upp i ett dedikerat fönster.
   
    Du kan nu se spåren som avges av Service Fabric och dina tjänster. Om du vill filtrera händelserna så att de bara visar ett visst program skriver du bara in namnet på programmet i filtret.
   
    ![Visa strömmande spår][viewingstreamingtraces]
3. När du är klar med strömmande spårningar från klustret kan du inaktivera fjärrströmningsspårningar genom att högerklicka på klustret i **Cloud Explorer** och välja **Inaktivera strömningsspårningar**
   
    ![Inaktivera spårningar för fjärrströmning][disablestreamingtraces]

## <a name="next-steps"></a>Nästa steg
* [Testa en service i Service Fabric](service-fabric-testability-overview.md).
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
