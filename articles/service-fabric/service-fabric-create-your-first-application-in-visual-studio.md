<properties
   pageTitle="Skapa ditt första Service Fabric-program i Visual Studio | Microsoft Azure"
   description="Skapa, distribuera och felsöka ett Service Fabric-program med Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2016"
   ms.author="ryanwi"/>


# Skapa ditt första Azure Service Fabric-program i Visual Studio

Service Fabric SDK innehåller ett tillägg för Visual Studio som tillhandahåller mallar och verktyg som hjälper dig att skapa, distribuera och felsöka Service Fabric-program. Den här artikeln beskriver steg för steg hur du skapar ditt första program i Visual Studio.

## Krav

Du måste [konfigurera utvecklingsmiljön](service-fabric-get-started.md) innan du börjar.

## Videogenomgång

Följande videoklipp går igenom stegen i den här självstudiekursen:

>[AZURE.VIDEO creating-your-first-service-fabric-application-in-visual-studio]

## Skapa programmet

Ett Service Fabric-program kan innehålla en eller flera tjänster, som var och en ansvarar för att leverera programmets funktioner. I guiden Nytt projekt kan du skapa ett programprojekt tillsammans med ditt första tjänstprojekt. Du kan lägga till fler tjänster senare.

1. Starta Visual Studio som administratör.

2. Klicka på **Arkiv > Nytt projekt > Moln > Service Fabric-program**.

3. Ge programmet ett namn och klicka på **OK**.

    ![Dialogrutan Nytt projekt i Visual Studio][1]

4. På nästa sida uppmanas du att välja **Tillståndskänslig** som den första tjänsttypen som du vill ta med i programmet. Skriv ett namn och klicka på **OK**.

    ![Dialogrutan Ny tjänst i Visual Studio][2]

    >[AZURE.NOTE] Mer information om alternativen finns i [Översikt över Service Fabric-programmeringsmodell](service-fabric-choose-framework.md).

    Visual Studio skapar programprojektet och det tillståndskänsliga tjänstprojektet och visar dem i Solution Explorer.

    ![Solution Explorer när ett program med en tillståndskänslig tjänst har skapats][3]

    Programprojektet innehåller ingen direkt kod. I stället refererar det till en uppsättning tjänstprojekt. Dessutom innehåller det tre andra typer av innehåll:

    - **Publiceringsprofiler**: Används för att hantera verktygsinställningar för olika miljöer.

    - **Skript**: Här ingår bland annat ett PowerShell-skript för distribution/uppgradering av program. Skriptet körs i bakgrunden av Visual Studio. Det kan också anropas direkt på kommandoraden.

    - **Programdefinition**: innehåller programmanifestet under *ApplicationPackageRoot*. Associerade parameterfiler för programmet finns under *ApplicationParameters*, som definierar programmet och gör att du kan konfigurera det specifikt för en given miljö.

    En översikt över innehållet i tjänstprojektet finns i [Komma igång med Reliable Services](service-fabric-reliable-services-quick-start.md).

## Distribuera och felsöka programmet

Nu när du har ett program kan du prova att köra det.

1. Tryck på F5 i Visual Studio för att distribuera programmet för felsökning.

    >[AZURE.NOTE] Distributionen kan ta en liten stund första gången Visual Studio skapar ett lokalt kluster för utveckling. Ett lokalt kluster kör samma plattformskod som du skapar i ett kluster med flera datorer, men på en enda dator. Statusen för klustergenereringen visas i utdatafönstret i Visual Studio.

    När klustret är klart får du ett meddelande från programmet Local Cluster Manager i systemfältet som ingår i SDK.

    ![Meddelande från Local Cluster Manager i systemfältet][4]

2. När programmet startar visas loggboken Diagnostik automatiskt, där du kan se spårningsinformation från tjänsten.

    ![Loggboken Diagnostik][5]

    Om du använder mallen för tillståndskänsliga tjänster visar meddelandena bara antalet som ökar i metoden `RunAsync` i MyStatefulService.cs.

3. Expandera någon av händelserna om du vill visa mer information, inklusive noden där koden körs. I det här fallet är det _Node_2, men det kan skilja sig på din dator.

    ![Detaljer från loggboken Diagnostik][6]

    Det lokala klustret innehåller fem noder som finns på en enda dator. Det efterliknar ett kluster med fem noder, där noderna finns på olika datorer. Nu ska vi ta bort en av noderna i det lokala klustret för att simulera förlusten av en dator och passa på att använda Visual Studio-felsökaren.

    >[AZURE.NOTE] Diagnostikhändelserna för programmet som rapporteras av projektmallen använder den medföljande klassen `ServiceEventSource`. Mer information finns i [Övervaka och felsöka tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

4. Leta upp klassen i ditt tjänstprojekt som kommer från StatefulService (till exempel MyStatefulService) och ange en brytpunkt på den första raden i `RunAsync`-metoden.

    ![Brytpunkt i den tillståndskänsliga tjänstens RunAsync-metod][7]

5. Högerklicka på appen Local Cluster Manager i systemfältet och välj **Hantera lokalt kluster** för att starta Service Fabric Explorer.

    ![Starta Service Fabric Explorer från Local Cluster Manager][systray-launch-sfx]

    Service Fabric Explorer tillhandahåller en visuell representation av ett kluster, inklusive uppsättningen program som distribuerats till det och den samling fysiska noder som det består av. Mer information om Service Fabric Explorer finns i [Visualisera klustret](service-fabric-visualizing-your-cluster.md).

6. I den vänstra rutan expanderar du **Kluster > Noder** och letar upp noden där koden körs.

7. Klicka på **Åtgärder > Inaktivera (omstart)** för att simulera omstarten av en dator. (Observera att du också kan inaktivera snabbmenyn i nodlistvyn i det vänstra fönstret.)

    ![Stoppa en nod i Service Fabric Explorer][sfx-stop-node]

    Under ett ögonblick kan du se din brytpunkt i Visual Studio när beräkningen som du gjorde på en nod smidigt växlar över till en annan.

8. Gå tillbaka till loggboken Diagnostik och notera meddelandena. Observera att räknaren har fortsatt att öka, även om händelserna egentligen kommer från en annan nod.

    ![Loggboken Diagnostik efter en redundansväxling][diagnostic-events-viewer-detail-post-failover]

## Växla klusterläge

Som standard har det lokala utvecklingsklustret konfigurerats för att köras som ett kluster med fem noder, vilket är användbart för felsökning av tjänster som är distribuerade över flera noder. Det kan dock ta tid att distribuera ett program till utvecklingsklustret med fem noder. Om du vill iterera kodändringar snabbt, utan att köra ditt program på 5 noder, kan du växla utvecklingsklustret till ett läge med bara en nod. Om du vill köra din kod i ett kluster med en nod högerklickar du på den lokala klusterhanteraren i systemfältet och väljer **Växla klusterläge -> 1 nod**.  

![Växla klusterläge][switch-cluster-mode]

När du ändrar klusterläge återställs utvecklingsklustret och alla program som etablerats eller körs på klustret tas bort.

## Rensa

  Innan du avslutar är det viktigt att komma ihåg att det lokala klustret är verkligt. Om du stoppar felsökningen tar du bort din instans av programmet och avregistrerar programtypen. Klustret fortsätter dock att köras i bakgrunden. Du kan hantera klustret på flera sätt:

  1. Om du vill stänga av klustret, men behålla programdata och spårningar, klickar du på **Stoppa lokalt kluster** i appen i systemfältet.

  2. Om du vill ta bort klustret helt klickar du på **Ta bort lokalt kluster** i appen i systemfältet. Observera att det här alternativet resulterar i en till långsam distribution nästa gång du trycker på F5 i Visual Studio. Ta bara bort klustret om du inte planerar att använda det lokala klustret under en tid eller om du behöver frigöra resurser.

## Nästa steg

- Lär dig hur du skapar ett [kluster i Azure](service-fabric-cluster-creation-via-portal.md) eller ett [fristående kluster i Windows](service-fabric-cluster-creation-for-windows-server.md).
- Prova att skapa en tjänst med hjälp av programmeringsmodellerna [Reliable Services](service-fabric-reliable-services-quick-start.md) eller [Reliable Actors](service-fabric-reliable-actors-get-started.md).
- Lär dig hur du kan exponera dina tjänster för Internet med en [frontwebbtjänst](service-fabric-add-a-web-frontend.md).
- Gå igenom en [praktisk övning](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) och skapa en tillståndslös tjänst, konfigurera övervakning och hälsorapporter och utför en uppgradering av programmet.

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



<!--HONumber=Sep16_HO3-->


