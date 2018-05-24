---
title: Skapa en tillförlitlig tjänst för Azure Service Fabric med C#
description: Skapa, distribuera och felsöka ett tillförlitligt tjänstprogram som bygger på Azure Service Fabric med Visual Studio.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/14/2018
ms.author: ryanwi
ms.openlocfilehash: 7e64bc34f5c39edaf87cc732d7c4702655df0e3e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212678"
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>Skapa ditt första tillståndskänsliga tillförlitliga C# Service Fabric-program

Lär dig hur du distribuerar ditt första Azure Service Fabric-program för .NET i Windows på bara några minuter. När du är klar har du ett lokala kluster som körs med ett tillförlitligt tjänstprogram.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste ha [konfigurerat utvecklingsmiljön](service-fabric-get-started.md) innan du börjar. Processen innefattar hur du installerar Service Fabric-SDK och Visual Studio 2017 eller 2015.

## <a name="create-the-application"></a>Skapa programmet

1. Starta Visual Studio som administratör.

2. Skapa ett projekt genom att välja Ctrl+Skift+N.

3. I dialogrutan **Nytt projekt** väljer du **Moln** > **Service Fabric-program**.

4. Ge programmet namnet **MyApplication**. Välj sedan **OK**.

   ![Dialogrutan Nytt projekt i Visual Studio][1]

5. Du kan skapa alla typer av Service Fabric-program från nästa dialogruta. För den här snabbstarten väljer du **.Net Core 2.0** > **Tillståndskänslig tjänst**.

6. Namnge tjänsten **MyStatefulService**. Välj sedan **OK**.

    ![Dialogrutan Ny tjänst i Visual Studio][2]

    Visual Studio skapar programprojektet och det tillståndskänsliga tjänstprojektet. De visas sedan i Solution Explorer.

    ![Solution Explorer när ett program med en tillståndskänslig tjänst har skapats][3]

    Programprojektet (**MyApplication**) har ingen kod. I stället refererar det till en uppsättning tjänstprojekt. Det finns också tre typer av innehåll:

    * **Publicera profiler**  
    Profiler för att distribuera till olika miljöer.

    * **Skript**  
    Ett PowerShell-skript för distribution eller uppgradering av program.

    * **Programdefinition**  
Innehåller filen ApplicationManifest.xml under *ApplicationPackageRoot* som beskriver ditt programs sammansättning. Associerade programfiler för parametern är *ApplicationParameters*, som kan användas för att ange miljöspecifikt parametrar. Visual Studio väljer en programparameterfil som anges i den tillhörande publikationsprofilen.
    
En översikt över innehållet i tjänstprojektet finns i [Komma igång med Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Distribuera och felsöka programmet

Nu när du har ett program ska du köra, distribuera och felsöka det genom att utföra följande steg.

1. Tryck på F5 i Visual Studio för att distribuera programmet för felsökning.

    >[!NOTE]
    >Första gången du kör och distribuerar programmet lokalt, skapar Visual Studio ett lokalt kluster för felsökning. Det kan ta lite tid. Statusen för klustergenereringen visas i utdatafönstret i Visual Studio.

    När klustret är klart får du ett meddelande från programmet Local Cluster Manager i systemfältet som ingår i SDK.
    
    ![Meddelande från Local Cluster Manager i systemfältet][4]

    När programmet startar visas loggboken Diagnostik automatiskt, där du kan se spårningsinformation från tjänsten.
    
    ![Loggboken Diagnostik][5]

    >[!NOTE]
    >Händelser bör automatiskt börja spåra i loggboken Diagnostik. Om du behöver konfigurera loggboken Diagnostik manuellt öppnar du först filen `ServiceEventSource.cs`, som finns i projektet **MyStatefulService**. Kopiera värdet för attributet `EventSource` överst i klassen `ServiceEventSource`. I följande exempel benämns händelsekällan `"MyCompany-MyApplication-MyStatefulService"`. I ditt fall kanske den heter något annat.
>
    >![Hitta namnet på tjänstens händelsekälla][service-event-source-name]


2. Öppna sedan dialogrutan **ETW Providers** (ETW-leverantörer). Välj sedan kugghjulsikonen på fliken **Diagnostics Events** (Diagnostikhändelser). Kopiera och klistra in namnet på händelsekällan som du kopierade till textrutan **ETW-leverantörer**. Välj sedan knappen **Apply** (Använd). Då startas händelsespårningen automatiskt.

    ![Konfigurera händelsekällans namn för diagnostik][setting-event-source-name]

    Du bör nu se händelserna visas i fönstret Diagnostikhändelser.

    Med mallen för tillståndskänsliga tjänster visas en ökande räknare i metoden `RunAsync` i **MyStatefulService.cs**.

3. Expandera någon av händelserna om du vill visa mer information, inklusive noden där koden körs. I det här fallet är det **\_Node\_0,** men det kan skilja sig på din dator.
   
    ![Detaljer från loggboken Diagnostik][6]

4. Det lokala klustret innehåller fem noder som finns på en enda dator. Varje nod finns på en distinkt fysisk eller virtuell dator i en produktionsmiljö. Nu ska vi ta bort en av noderna i det lokala klustret för att simulera förlusten av en dator när du använder Visual Studio-felsökaren.

5. I fönster **Solution Explorer** öppnar du **MyStatefulService.cs**. 

6. Hitta `RunAsync`-metoden och ange en brytpunkt på den första raden i metoden.

    ![Brytpunkt i den tillståndskänsliga tjänstens RunAsync-metod][7]

7. Högerklicka på **Local Cluster Manager** i systemfältet och välj **Hantera lokalt kluster** för att starta verktyget Service Fabric Explorer.

    ![Starta Service Fabric Explorer från Local Cluster Manager][systray-launch-sfx]

    [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) visar en bild av ett kluster. Den innehåller en uppsättning program som distribueras till den och en uppsättning fysiska noder den består av.

8. I den vänstra rutan expanderar du **Kluster** > **Noder** och letar upp noden där koden körs. Klicka därefter på **Åtgärder** > **Inaktivera (omstart)** för att simulera omstarten av en dator.

    ![Stoppa en nod i Service Fabric Explorer][sfx-stop-node]

    Under ett ögonblick kan du se din brytpunkt i Visual Studio när beräkningen som du gjorde på en nod smidigt växlar över till en annan.

9. Gå sedan tillbaka till loggboken Diagnostik och notera meddelandena. Räknaren har fortsatt att öka, även om händelserna egentligen kommer från en annan nod.

    ![Loggboken Diagnostik efter en redundansväxling][diagnostic-events-viewer-detail-post-failover]

## <a name="clean-up-the-local-cluster-optional"></a>Rensa det lokala klustret (valfritt)

Kom ihåg att det här lokala klustret är verkligt. Om du stoppar felsökningen tar du bort din instans av programmet och avregistrerar programtypen. Klustret fortsätter dock att köras i bakgrunden. När du är redo att stoppa det lokala klustret finns det några alternativ.

### <a name="keep-application-and-trace-data"></a>Bibehåll program- och spårningsdata

Högerklicka på **Local Cluster Manager** i systemfältet och välj **Stäng lokalt kluster** för att stänga det lokala klustret.

### <a name="delete-the-cluster-and-all-data"></a>Ta bort klustret och alla data

Ta bort klustret genom att högerklicka på **den lokala klusterhanteraren** i systemfältet. Välj sedan **Ta bort lokalt kluster**. 

Om du väljer det här alternativet, distribuerar Visual Studio klustret nästa gång du kör programmet. Använd bara det här alternativet om du inte planerar att använda det lokala klustret under en tid eller om du behöver frigöra resurser.

## <a name="next-steps"></a>Nästa steg
Mer information om [Reliable Services](service-fabric-reliable-services-introduction.md).
<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
