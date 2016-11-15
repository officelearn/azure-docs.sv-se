---
title: "Komma igång med att distribuera och uppgradera appar i det lokala klustret | Microsoft Docs"
description: Konfigurera ett lokalt Service Fabric-kluster, distribuera ett befintligt program till det och uppgradera sedan programmet.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 60a1f6a5-5478-46c0-80a8-18fe62da17a8
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/09/2016
ms.author: ryanwi;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 515daddf2c118f26721a557b0caf5d5415cb22c5


---
# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Komma igång med att distribuera och uppgradera program i det lokala klustret
Azure Service Fabric SDK innehåller en fullständig lokal utvecklingsmiljö som du kan använda för att snabbt komma igång med att distribuera och hantera program i ett lokalt kluster. I den här artikeln skapar du ett lokalt kluster, distribuerar ett befintligt program till det och uppgraderar sedan programmet till en ny version, allt från Windows PowerShell.

> [!NOTE]
> I den här artikeln förutsätter vi att du redan har [konfigurerat utvecklingsmiljön](service-fabric-get-started.md).
> 
> 

## <a name="create-a-local-cluster"></a>Skapa ett lokalt kluster
Ett Service Fabric-kluster representerar en uppsättning maskinvaruresurser som du kan distribuera program till. Vanligtvis består ett kluster av allt från fem till flera tusen datorer. Service Fabric SDK innehåller dock en klusterkonfiguration som kan köras på en enskild dator.

Det är viktigt att förstå att det lokala Service Fabric-klustret inte är en emulator eller simulator. Det kör samma plattformskod som finns i kluster med flera datorer. Den enda skillnaden är att det kör plattformsprocesserna som normalt är fördelade mellan fem datorer på en enda dator.

Med SDK kan du konfigurera ett lokalt kluster på två sätt: Windows PowerShell-skript och appen Local Cluster Manager i systemfältet. I den här självstudien använder vi PowerShell-skriptet.

> [!NOTE]
> Om du redan har skapat ett lokalt kluster genom att distribuera ett program från Visual Studio kan du hoppa över det här avsnittet.
> 
> 

1. Starta ett nytt PowerShell-fönster som administratör.
2. Kör installationsskriptet för klustret från SDK-mappen:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```
   
    Klusterinstallationen tar en stund. När installationen är klar bör skärmen visa något som liknar detta:
   
    ![Utdata efter klusterinstallationen][cluster-setup-success]
   
    Nu kan du prova att distribuera ett program i klustret.

## <a name="deploy-an-application"></a>Distribuera ett program
Service Fabric SDK har en omfattande uppsättning ramverk och utvecklingsverktyg som hjälper dig att skapa program. Om du vill lära dig hur du skapar program i Visual Studio läser du [Skapa ditt första Service Fabric-program i Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

I den här självstudiekursen använder vi ett befintligt exempelprogram (kallat WordCount) så att vi kan fokusera på hanteringsaspekterna för plattformen, t.ex. distribution, övervakning och uppgradering.

1. Starta ett nytt PowerShell-fönster som administratör.
2. Importera PowerShell-modulen för Service Fabric SDK.
   
    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```
3. Skapa en katalog för att lagra programmet som du hämtar och distribuerar, till exempel C:\ServiceFabric.
   
    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```
4. [Ladda ned programmet WordCount](http://aka.ms/servicefabric-wordcountapp) till den plats som du har skapat.  Obs! Microsoft Edge-webbläsaren sparar filen med ett *.zip*-tillägg.  Ändra filnamnstillägget till *.sfpkg*.
5. Anslut till det lokala klustret:
   
    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```
6. Skapa ett nytt program med SDK:s distributionskommando med ett namn och en sökväg till programpaketet.
   
    ```powershell  
   Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```
   
    Om allt går bra bör du se följande utdata:
   
    ![Distribuera ett program till det lokala klustret][deploy-app-to-local-cluster]
7. Om du vill se programmet startar du webbläsaren och går till [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Du bör se:
   
    ![Distribuerat programgränssnitt][deployed-app-ui]
   
    Programmet WordCount är väldigt enkelt. Det innehåller JavaScript-kod på klientsidan för att generera slumpmässiga ”ord” med fem tecken, som sedan vidarebefordras till programmet via ASP.NET Web API. En tillståndskänslig tjänst spårar antalet ord som räknats. De partitioneras baserat på det första tecknet i ordet. Du hittar källkoden för WordCount-appen bland [exemplen för att komma igång](https://azure.microsoft.com/documentation/samples/service-fabric-dotnet-getting-started/).
   
    Programmet som vi distribuerade innehåller fyra partitioner. Så ord som börjar med A till och med G lagras i den första partitionen, ord som börjar med H till och med N lagras i den andra partitionen och så vidare.

## <a name="view-application-details-and-status"></a>Visa programinformation och programstatus
Nu när vi har distribuerat programmet ska vi titta på några appdetaljer i PowerShell.

1. Fråga alla distribuerade program i klustret:
   
    ```powershell
    Get-ServiceFabricApplication
    ```
   
    Om vi antar att du endast har distribuerat WordCount-appen visas något som liknar följande:
   
    ![Fråga alla distribuerade program i PowerShell][ps-getsfapp]
2. Gå till nästa nivå genom att fråga uppsättningen med tjänster som ingår i WordCount-programmet.
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Visa en lista över tjänsterna för programmet i PowerShell][ps-getsfsvc]
   
    Programmet består av två tjänster: frontwebbtjänsten och den tillståndskänsliga tjänsten som hanterar orden.
3. Ta också en titt på listan över partitioner för WordCountService:
   
    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```
   
    ![Visa tjänstpartitionerna i PowerShell][ps-getsfpartitions]
   
    Den uppsättning kommandon som du använde är, precis som alla PowerShell-kommandon för Service Fabric, tillgänglig för alla kluster som du ansluter till, både lokala och fjärranslutna.
   
    Ett mer visuellt sätt att interagera med klustret är att använda det webbaserade verktyget Service Fabric Explorer som du hittar på [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
   
    ![Visa programinformation i Service Fabric Explorer][sfx-service-overview]
   
   > [!NOTE]
   > Mer information om Service Fabric Explorer finns i [Visualisera klustret med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
   > 
   > 

## <a name="upgrade-an-application"></a>Uppgradera ett program
Service Fabric tillhandahåller uppgraderingar utan någon nedtid genom att övervaka programmets hälsa medan det distribueras i klustret. Vi ska utföra en enkel uppgradering av WordCount-programmet.

Den nya versionen av programmet räknar bara ord som börjar med en vokal. När uppgraderingen distribueras ser vi två ändringar i programmets beteende. För det första bör antalet växa långsammare eftersom färre ord räknas. För det andra bör den första partitionen så småningom gå om de andra eftersom den har två vokaler (A och E) medan alla andra partitioner endast innehåller en var.

1. [Ladda ned WordCount v2-paketet](http://aka.ms/servicefabric-wordcountappv2) till samma plats som du laddade ned v1-paketet till.
2. Gå tillbaka till PowerShell-fönstret och använd SDK-uppgraderingskommandot för att registrera den nya versionen i klustret. Börja sedan uppgradera fabric:/WordCount-programmet.
   
    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```
   
    Du bör se utdata i PowerShell som liknar följande när uppgraderingen börjar.
   
    ![Uppgraderingsförlopp i PowerShell][ps-appupgradeprogress]
3. När uppgraderingen körs kan det vara lättare att övervaka dess status från Service Fabric Explorer. Öppna ett webbläsarfönster och gå till [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Expandera **Program** i trädet till vänster, välj **WordCount** och slutligen **fabric:/WordCount**. På fliken Essentials visas statusen för uppgraderingen när den fortsätter genom klustrets uppgraderingsdomäner.
   
    ![Uppgraderingsförlopp i Service Fabric Explorer][sfx-upgradeprogress]
   
    Allteftersom uppgraderingen fortsätter genom domänerna utförs hälsokontroller för att säkerställa att programmet fungerar korrekt.
4. Om du kör om den tidigare frågan för tjänsterna i fabric:/WordCount-programmet ser du att versionen av WordCountService ändras, men inte versionen av WordCountWebService:
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Fråga programtjänster efter uppgraderingen][ps-getsfsvc-postupgrade]
   
    Här ser du hur Service Fabric hanterar programuppgraderingar. Service Fabric rör endast den uppsättning tjänster (eller kod/konfigurationspaket i dessa tjänster) som har ändrats, vilket gör uppgraderingsprocessen snabbare och mer tillförlitlig.
5. Gå tillbaka till webbläsaren och observera hur den nya programversionen fungerar. Som förväntat växer antalet långsammare och den första partitionen har något mer av volymen när allt är klart.
   
    ![Visa den nya versionen av programmet i webbläsaren][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Rensa
Innan du avslutar är det viktigt att komma ihåg att det lokala klustret är verkligt. Programmen fortsätter att köras i bakgrunden tills du tar bort dem.  Beroende på typen av program kan ett program som körs ta betydande resurser i anspråk på datorn. Du kan hantera program och klustret på flera sätt:

1. Ta bort ett enskilt program och dess data genom att köra följande:
   
    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```
   
    Du kan även ta bort programmet från menyn **ÅTGÄRDER** i Service Fabric Explorer eller snabbmenyn i programmets listvy i den vänstra rutan.
   
    ![Ta bort ett program i Service Fabric Explorer][sfe-delete-application]
2. När du har tagit bort programmet från klustret kan du avregistrera versionerna 1.0.0 och 2.0.0 av WordCount-programtypen. Raderingen ta bort programpaket, inklusive dess kod och konfiguration, från klustrets avbildningsarkiv.
   
    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```
   
    Du kan även välja att **avetablera typen** för programmet i Service Fabric Explorer.
3. Om du vill stänga av klustret, men behålla programdata och spårningar, klickar du på **Stoppa lokalt kluster** i appen i systemfältet.
4. Om du vill ta bort klustret helt klickar du på **Ta bort lokalt kluster** i appen i systemfältet. Alternativet resulterar i en till långsam distribution nästa gång du trycker på F5 i Visual Studio. Ta bara bort det lokala klustret om du inte avser att använda det under en tid eller om du behöver frigöra resurser.

## <a name="1-node-and-5-node-cluster-mode"></a>Klusterläge med 1 nod och 5 noder
När du arbetar med det lokala klustret för att utveckla program kan det hända att du gör snabba iterationer när du skriver kod, vid felsökning, ändring av kod etc. För att optimera den här processen kan det lokala klustret köras i två lägen: 1 nod eller 5 noder. Båda klusterlägena har sina fördelar.
I klustret med 5 noder kan du arbeta med ett verkligt kluster. Du kan testa redundansscenarier, samt arbeta med flera instanser och repliker av dina tjänster.
Klusterläget med 1 nod är optimerat för snabb distribution och registrering av tjänster, vilket hjälper dig att snabbt verifiera kod med Service Fabric-körningen.

Varken klusterläget med 1 nod och med 5 noder är ett emuleringsprogram eller en simulator. Det kör samma plattformskod som finns i kluster med flera datorer.

> [!NOTE]
> Den här funktionen finns i SDK-version 5.2 och senare.
> 
> 

För att ändra klusterläget till ett kluster med 1 nod använder du Local Cluster Manager i Service Fabric eller PowerShell på följande sätt:

1. Starta ett nytt PowerShell-fönster som administratör.
2. Kör installationsskriptet för klustret från SDK-mappen:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```
   
    Klusterinstallationen tar en stund. När installationen är klar bör skärmen visa något som liknar detta:
   
    ![Utdata efter klusterinstallationen][cluster-setup-success-1-node]

Om du använder Local Cluster Manager i Service Fabric:

![Växla klusterläge][switch-cluster-mode]

> [!WARNING]
> När du ändrar klusterläge tas det aktuella klustret bort från datorn och ett nytt kluster skapas. Data som du har lagrat i klustret tas bort när du ändrar klusterläge.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Nu när du har distribuerat och uppgraderat vissa fördefinierade program kan du [skapa ett eget program i Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
* Alla åtgärder som utförts i det lokala klustret i den här artikeln kan även utföras i ett [Azure-kluster](service-fabric-cluster-creation-via-portal.md).
* Uppgraderingen som vi utförde i den här artikeln var grundläggande. Mer information om kraften och flexibiliteten i Service Fabric-uppgraderingar finns i [uppgraderingsdokumentationen](service-fabric-application-upgrade.md).

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png



<!--HONumber=Nov16_HO2-->


