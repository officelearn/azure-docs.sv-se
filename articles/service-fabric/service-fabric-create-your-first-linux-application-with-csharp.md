---
title: "Skapa din första Azure-mikrotjänstapp i Linux med hjälp av C# | Microsoft Docs"
description: Skapa och distribuera ett Service Fabric-program med C#
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2016
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: c810f3e86ba582943e88f3085f6d9cff2496031c


---
# <a name="create-your-first-azure-service-fabric-application"></a>Skapa ditt första Azure Service Fabric-program
> [!div class="op_single_selector"]
> * [C# – Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java – Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

Service Fabric innehåller SDK:er för att skapa tjänster i Linux i både .NET Core och Java. I den här självstudien, visar vi hur man skapar ett program för Linux och en tjänst med C# (.NET Core).

## <a name="prerequisites"></a>Krav
Du måste [konfigurera Linux-utvecklingsmiljön](service-fabric-get-started-linux.md) innan du börjar. Om du använder Mac OS X kan du [konfigurera en Linux-miljö på en virtuell dator med hjälp av Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Skapa programmet
Ett Service Fabric-program kan innehålla en eller flera tjänster, som var och en ansvarar för att leverera programmets funktioner. I Service Fabric SDK för Linux finns en [Yeoman](http://yeoman.io/)-generator som gör det enkelt att skapa din första tjänst och lägga till fler senare. Använd Yeoman för att skapa ett program med en enskild tjänst.

1. Skriv följande kommando i en terminal, för att börja bygga ställningarna: `yo azuresfcsharp`
2. Namnge ditt program.
3. Välj vilken typ din första tjänst ska ha och namnge den. För den här självstudien väljer vi en Reliable Actor-tjänst.
   
   ![Service Fabric Yeoman-generator för C#][sf-yeoman]

> [!NOTE]
> Mer information om alternativen finns i [Översikt över Service Fabric-programmeringsmodell](service-fabric-choose-framework.md).
> 
> 

## <a name="build-the-application"></a>Skapa programmet
Service Fabric Yeoman-mallarna inkluderar ett byggskript som du kan använda för att skapa programmet från terminalen (efter att du navigerat till programmappen).

  ```sh
 cd myapp 
 ./build.sh 
  ```

## <a name="deploy-the-application"></a>Distribuera programmet
När du har skapat programmet kan du distribuera det till det lokala klustret med Azure CLI.

1. Anslut till det lokala Service Fabric-klustret.
   
    ```sh
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
3. Klicka på noden du hittade i föregående steg och välj sedan **Deactivate (restart)** (Inaktivera (omstart)) på menyn Actions (Åtgärder). Den här åtgärden startar om en nod i ditt lokala kluster, vilket framtvingar en redundansväxling till en sekundär replik som körs på en annan nod. När du utför åtgärden, ska du vara uppmärksam på utdata från testklienten och notera att räknaren fortsätter att öka trots redundansen.

## <a name="adding-more-services-to-an-existing-application"></a>Lägga till fler tjänster till ett befintligt program

Om du vill lägga till en till tjänst till ett program som redan har skapats med hjälp av `yo` utför du följande steg: 
1. Ändra katalogen till roten för det befintliga programmet.  Till exempel `cd ~/YeomanSamples/MyApplication` om `MyApplication` är programmet som skapats av Yeoman.
2. Kör `yo azuresfcsharp:AddService`

## <a name="next-steps"></a>Nästa steg
* [Läs mer om Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interagera med Service Fabric-kluster med Azure CLI](service-fabric-azure-cli.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png



<!--HONumber=Jan17_HO4-->


