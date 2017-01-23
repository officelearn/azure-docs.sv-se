---
title: "Skapa ditt första Service Fabric-program i Linux med Java | Microsoft Docs"
description: Skapa och distribuera ett Service Fabric-program med Java
services: service-fabric
documentationcenter: java
author: seanmck
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2cf98a0ef478a058c03122d3e027ef37e2404a09
ms.openlocfilehash: 8a7b100a531ea1dd5420451064fdfb1eb3f21782


---
# <a name="create-your-first-azure-service-fabric-application"></a>Skapa ditt första Azure Service Fabric-program
> [!div class="op_single_selector"]
> * [C# – Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java – Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

Service Fabric innehåller SDK:er för att skapa tjänster i Linux i både .NET Core och Java. I den här självstudiekursen skapar vi ett program för Linux och skapar en tjänst med hjälp av Java.  Följande Microsoft Virtual Academy-video vägleder dig genom processen för att skapa ett Java-program för Linux:  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-create-your-first-linux-application-with-java/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

> [!NOTE]
> Java som ett första klassens inbyggda programmeringsspråk, stöds enbart för Linux-förhandsvisningen (Windows-stöd planeras). Alla program, inklusive Java-program kan dock köras som körbara gästfiler eller inuti behållare i Windows- eller Linux. Mer information finns i [Distribuera en befintlig körbar fil till Azure Service Fabric](service-fabric-deploy-existing-app.md) och [Distribuera behållare till Service Fabric](service-fabric-deploy-container.md).
> 


## <a name="prerequisites"></a>Krav
Du måste [konfigurera Linux-utvecklingsmiljön](service-fabric-get-started-linux.md) innan du börjar. Om du använder Mac OS X kan du [konfigurera en Linux-miljö på en virtuell dator med hjälp av Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Skapa programmet
Ett Service Fabric-program kan innehålla en eller flera tjänster, som var och en ansvarar för att leverera programmets funktioner. I Service Fabric SDK för Linux finns en [Yeoman](http://yeoman.io/)-generator som gör det enkelt att skapa din första tjänst och lägga till fler senare. Använd Yeoman för att skapa ett program med en enskild tjänst.

1. I en terminal, skriver du in ``yo azuresfjava``.
2. Namnge ditt program.
3. Välj vilken typ din första tjänst ska ha och namnge den. För den här självstudien väljer vi en Reliable Actor-tjänst.
   
   ![Service Fabric Yeoman-generator för Java][sf-yeoman]

> [!NOTE]
> Mer information om alternativen finns i [Översikt över Service Fabric-programmeringsmodell](service-fabric-choose-framework.md).
> 

## <a name="build-the-application"></a>Skapa programmet
I Service Fabric Yeoman-mallarna ingår ett byggskript för [Gradle](https://gradle.org/), som du kan använda för att skapa appen från terminalen.

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Distribuera programmet
När du har skapat programmet kan du distribuera det till det lokala klustret med Azure CLI.

1. Anslut till det lokala Service Fabric-klustret.
   
    ```bash
    azure servicefabric cluster connect
    ```

2. Använd installationsskriptet som medföljer mallen för att kopiera programpaketet till klustrets avbildningsarkiv, registrera programtypen och skapa en instans av programmet.
   
    ```bash
    ./install.sh
    ```

3. Öppna en webbläsare och gå till Service Fabric Explorer på http://localhost:19080/Explorer (ersätt localhost med den virtuella datorns privata IP om du använder Vagrant på Mac OS X).

4. Expandera programnoden och observera att det nu finns en post för din programtyp och en post för den första instansen av den typen.

## <a name="start-the-test-client-and-perform-a-failover"></a>Starta testklienten och utför en redundansväxling
Aktörsprojekt gör ingenting på egen hand. Det behövs en annan tjänst eller klient för att skicka meddelanden till dem. Aktörsmallen innehåller ett enkelt testskript som du kan använda för att interagera med aktörstjänsten.

1. Kör skriptet med övervakningsverktyget för att se resultatet av aktörstjänsten.
   
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. I Service Fabric Explorer letar du reda på noden där den primära repliken för aktörstjänsten finns. På skärmbilden nedan är det nod 3.
   
    ![Hitta den primära repliken i Service Fabric Explorer][sfx-primary]

3. Klicka på noden du hittade i föregående steg och välj sedan **Deactivate (restart)** (Inaktivera (omstart)) på menyn Actions (Åtgärder). Den här åtgärden startar om en av de fem noderna i ditt lokala kluster och tvingar fram en redundansväxling till en av den sekundära replikerna som körs på en annan nod. När du utför åtgärden, ska du vara uppmärksam på utdata från testklienten och notera att räknaren fortsätter att öka trots redundansen.

## <a name="build-and-deploy-an-application-with-the-eclipse-neon-plugin"></a>Skapa och distribuera ett program med plugin-programmet Eclipse Neon

Om du har installerat [Service Fabric-plugin-programmet](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-linux#install-the-java-sdk-and-eclipse-neon-plugin-optional) för Eclipse Neon kan du använda det för att skapa och distribuera Service Fabric-program som skapats med Java.  Välj **Eclipse IDE för Java-utvecklare**, när du installerar Eclipse.

### <a name="create-the-application"></a>Skapa programmet

Service Fabric-plugin-programmet är tillgängligt via utökningsfunktionen i Eclipse.

1. Välj **File > Other > Service Fabric** (Arkiv > Annat > Service Fabric) i Eclipse. Olika alternativ visas, bland annat aktörer och behållare.
   
    ![Service Fabric-mallar i Eclipse][sf-eclipse-templates]

2. I det här fallet väljer du Stateless Service (Tillståndslös tjänst).

3. Du får bekräfta att du vill använda Service Fabric-perspektivet, vilket optimerar Eclipse för användning med Service Fabric-projekt. Välj Yes (Ja).

### <a name="deploy-the-application"></a>Distribuera programmet
I Service Fabric-mallarna ingår en uppsättning Gradle-aktiviteter för att skapa och distribuera program, som du kan köra via Eclipse. 

1. Välj **Run > Run Configurations** (Kör > Kör konfigurationer).
2. Ange **lokal** eller **moln**. Standardinställningen är **lokal**. Om du distribuerar till ett kluster väljer du **moln**.
3. Se till att rätt information visas i publiceringsprofilerna genom att redigera `local.json` eller `cloud.json`.
4. Klicka på **Run** (Kör).

Din app skapas och distribueras efter en liten stund. Du kan övervaka dess status från Service Fabric Explorer.

## <a name="adding-more-services-to-an-existing-application"></a>Lägga till fler tjänster till ett befintligt program

Om du vill lägga till en till tjänst till ett program som redan har skapats med hjälp av `yo` utför du följande steg: 
1. Ändra katalogen till roten för det befintliga programmet.  Till exempel `cd ~/YeomanSamples/MyApplication` om `MyApplication` är programmet som skapats av Yeoman.
2. Kör `yo azuresfjava:AddService`


## <a name="next-steps"></a>Nästa steg
* [Läs mer om Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interagera med Service Fabric-kluster med Azure CLI](service-fabric-azure-cli.md)
* [Felsökning av distribution](service-fabric-azure-cli.md#troubleshooting)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png



<!--HONumber=Jan17_HO1-->


