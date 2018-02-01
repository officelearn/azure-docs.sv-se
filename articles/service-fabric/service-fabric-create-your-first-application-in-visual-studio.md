---
title: "Skapa en tillförlitlig tjänst för Azure Service Fabric med C#"
description: "Skapa, distribuera och felsöka ett tillförlitligt tjänstprogram som bygger på Azure Service Fabric med Visual Studio."
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
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: bdabdbbb3037f3325c107a4e6148873a923b4ded
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2018
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>Skapa ditt första tillståndskänsliga tillförlitliga C# Service Fabric-program

Lär dig hur du distribuerar ditt första Service Fabric-program för .NET i Windows på bara några minuter. När du är färdig har du ett lokalt kluster som körs med ett tillförlitligt tjänstprogram.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste [konfigurera utvecklingsmiljön](service-fabric-get-started.md) innan du börjar. Detta innefattar hur du installerar Service Fabric-SDK och Visual Studio 2017 eller 2015.

## <a name="create-the-application"></a>Skapa programmet

Starta Visual Studio som **administratör**.

Skapa ett projekt med `CTRL`+`SHIFT`+`N`

Klicka på **Moln > Service Fabric-program** i dialogrutan **Nytt projekt**.

Ge programmet namnet **MyApplication** och tryck på **OK**.

   
![Dialogrutan Nytt projekt i Visual Studio][1]

Du kan skapa alla typer av Service Fabric-program från nästa dialogruta. För den här snabbstarten, väljer du **Tillståndskänslig tjänst**.

Namnge tjänsten **MyStatefulService** och tryck på **OK**.

![Dialogrutan Ny tjänst i Visual Studio][2]


Visual Studio skapar programprojektet och det tillståndskänsliga tjänstprojektet och visar dem i Solution Explorer.

![Solution Explorer när ett program med en tillståndskänslig tjänst har skapats][3]

Programprojektet (**MyApplication**) innehåller ingen direkt kod. I stället refererar det till en uppsättning tjänstprojekt. Dessutom innehåller det tre andra typer av innehåll:

* **Publicera profiler**  
Profiler för att distribuera till olika miljöer.

* **Skript**  
PowerShell-skript för distribution/uppgradering av program.

* **Programdefinition**  
Innehåller filen ApplicationManifest.xml under *ApplicationPackageRoot* som beskriver ditt programs sammansättning. Associerade programfiler för parametern är *ApplicationParameters*, som kan användas för att ange miljöspecifikt parametrar. Visual Studio väljer en programparameterfil som anges i den tillhörande publikationsprofilen vid distribution till en specifik miljö.
    
En översikt över innehållet i tjänstprojektet finns i [Komma igång med Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Distribuera och felsöka programmet

Nu när du har ett program kan du prova att köra det.

Tryck på `F5` i Visual Studio för att distribuera programmet för felsökning.

>[!NOTE]
>Första gången du kör och distribuerar programmet lokalt, skapar Visual Studio ett lokalt kluster för felsökning. Det kan ta lite tid. Statusen för klustergenereringen visas i utdatafönstret i Visual Studio.

När klustret är klart får du ett meddelande från programmet Local Cluster Manager i systemfältet som ingår i SDK.
   
![Meddelande från Local Cluster Manager i systemfältet][4]

När programmet startar visas loggboken **Diagnostik automatiskt**, där du kan se spårningsinformation från tjänsten.
   
![Loggboken Diagnostik][5]

Med mallen för tillståndskänsliga tjänster visas en ökande räknare i metoden `RunAsync` i **MyStatefulService.cs**.

Expandera någon av händelserna om du vill visa mer information, inklusive noden där koden körs. I det här fallet är det \_Node\_0, men det kan skilja sig på din dator.
   
![Detaljer från loggboken Diagnostik][6]

Det lokala klustret innehåller fem noder som finns på en enda dator. Varje nod finns på en distinkt fysisk eller virtuell dator i en produktionsmiljö. Nu ska vi ta bort en av noderna i det lokala klustret för att simulera förlusten av en dator och passa på att använda Visual Studio-felsökaren.

I fönster **Solution Explorer** öppnar du **MyStatefulService.cs**. 

Hitta `RunAsync`-metoden och ange en brytpunkt på den första raden i metoden.

![Brytpunkt i den tillståndskänsliga tjänstens RunAsync-metod][7]

Högerklicka på **Local Cluster Manager** i systemfältet och välj **Hantera lokalt kluster** för att starta verktyget **Service Fabric Explorer**.

![Starta Service Fabric Explorer från Local Cluster Manager][systray-launch-sfx]

[**Service Fabric Explorer**](service-fabric-visualizing-your-cluster.md) visar en bild av ett kluster. Den innehåller en uppsättning program som distribueras till den och en uppsättning fysiska noder den består av.

I den vänstra rutan expanderar du **Kluster > Noder** och letar upp noden där koden körs.

Klicka på **Åtgärder > Inaktivera (omstart)** för att simulera omstarten av en dator.

![Stoppa en nod i Service Fabric Explorer][sfx-stop-node]

Under ett ögonblick kan du se din brytpunkt i Visual Studio när beräkningen som du gjorde på en nod smidigt växlar över till en annan.


Gå sedan tillbaka till loggboken Diagnostik och notera meddelandena. Räknaren har fortsatt att öka, även om händelserna egentligen kommer från en annan nod.

![Loggboken Diagnostik efter en redundansväxling][diagnostic-events-viewer-detail-post-failover]

## <a name="cleaning-up-the-local-cluster-optional"></a>Rensa det lokala klustret (valfritt)

Kom ihåg att det här lokala klustret är verkligt. Om du stoppar felsökningen tar du bort din instans av programmet och avregistrerar programtypen. Klustret fortsätter dock att köras i bakgrunden. När du är redo att stoppa det lokala klustret finns det några alternativ.

### <a name="keep-application-and-trace-data"></a>Bibehåll program- och spårningsdata

Högerklicka på **Local Cluster Manager** i systemfältet och välj **Stäng lokalt kluster** för att stänga det lokala klustret.

### <a name="delete-the-cluster-and-all-data"></a>Ta bort klustret och alla data

Högerklicka på **Local Cluster Manager** i systemfältet och välj **Ta bort lokalt kluster** för att ta bort det lokala klustret. 

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
