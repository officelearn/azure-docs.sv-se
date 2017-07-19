---
title: "Skapa din första Azure-mikrotjänstapp i Linux med hjälp av Java | Microsoft Docs"
description: Skapa och distribuera ett Service Fabric-program med Java
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/02/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: e229602b4bfa72977c9b15e854d796ed09fa55d2
ms.contentlocale: sv-se
ms.lasthandoff: 07/01/2017


---
# <a name="create-your-first-service-fabric-java-application-on-linux"></a>Skapa ditt första Service Fabric Java-program för Linux
> [!div class="op_single_selector"]
> * [C# – Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java – Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Den här snabbstartsguiden hjälper dig att skapa ditt första Azure Service Fabric Java-program i en Linux-utvecklingsmiljö på bara några minuter.  När du är färdig har du ett enkelt Java-program med en tjänst som körs i det lokala utvecklingsklustret.  

## <a name="prerequisites"></a>Krav
Innan du börjar ska du installera Service Fabric SDK, Azure CLI och konfigurera ett utvecklingskluster i [Linux-utvecklingsmiljön](service-fabric-get-started-linux.md). Om du använder Mac OS X kan du [konfigurera en Linux-utvecklingsmiljö på en virtuell dator med hjälp av Vagrant](service-fabric-get-started-mac.md).

Du bör även konfigurera [Azure CLI 2.0](service-fabric-azure-cli-2-0.md) (rekommenderas) eller [XPlat CLI](service-fabric-azure-cli.md) för distribution av programmet.

## <a name="create-the-application"></a>Skapa programmet
Ett Service Fabric-program innehåller en eller flera tjänster, som var och en ansvarar för att leverera programmets funktioner. I Service Fabric SDK för Linux finns en [Yeoman](http://yeoman.io/)-generator som gör det enkelt att skapa din första tjänst och lägga till fler senare.  Du kan också skapa, bygga och distribuera Service Fabric Java-program med ett plugin-program för Eclipse. Läs mer i [Service Fabric-plugin-program för utveckling av Java-program i Eclipse](service-fabric-get-started-eclipse.md). I den här snabbstartguiden använder vi Yeoman till att skapa ett program med en enda tjänst, som lagrar och hämtar ett värde.

1. I en terminal, skriver du in ``yo azuresfjava``.
2. Namnge ditt program. 
3. Välj vilken typ din första tjänst ska ha och namnge den. I den här guiden väljer vi en Reliable Actor-tjänst. Mer information om andra typer av tjänster finns i [Service Fabric programming model overview](service-fabric-choose-framework.md) (Översikt över programmeringsmodellen i Service Fabric).
   ![Service Fabric Yeoman-generator för Java][sf-yeoman]

## <a name="build-the-application"></a>Skapa programmet
I Service Fabric Yeoman-mallarna ingår ett byggskript för [Gradle](https://gradle.org/), som du kan använda för att skapa programmet från terminalen. När du ska bygga och paketera programmet kör du följande:

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Distribuera programmet
När du har skapat programmet kan du distribuera det till det lokala klustret.

### <a name="using-xplat-cli"></a>Med XPlat CLI

1. Anslut till det lokala Service Fabric-klustret.

    ```bash
    azure servicefabric cluster connect
    ```

2. Kör installationsskriptet som medföljer mallen för att kopiera programpaketet till klustrets avbildningsarkiv, registrera programtypen och skapa en instans av programmet.

    ```bash
    ./install.sh
    ```

### <a name="using-azure-cli-20"></a>Med Azure CLI 2.0

Distributionen går till på samma sätt som för andra Service Fabric-program. Detaljerade instruktioner finns i dokumentationen om att [hantera ett Service Fabric-program med Azure CLI](service-fabric-application-lifecycle-azure-cli-2-0.md).

Du hittar parametrarna till de här kommandona i de genererade manifesten i programpaketet.

När programmet har distribuerats öppnar du en webbläsare och går till [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) på adressen [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Expandera sedan noden **Program** och observera att det nu finns en post för din programtyp och en post för den första instansen av den typen.

## <a name="start-the-test-client-and-perform-a-failover"></a>Starta testklienten och utför en redundansväxling
Aktörer gör ingenting på egen hand, det behövs en annan tjänst eller klient för att skicka meddelanden till dem. Aktörsmallen innehåller ett enkelt testskript som du kan använda för att interagera med aktörstjänsten.

1. Kör skriptet med övervakningsverktyget för att se resultatet av aktörstjänsten.  Testskriptet anropar metoden `setCountAsync()` hos aktören för att öka en räknare, anropar metoden `getCountAsync()` hos aktören för att hämta det nya räknarvärdet och visar värdet på konsolen.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Leta rätt på noden i Service Fabric Explorer som är värd för aktörstjänstens primära replik. På skärmbilden nedan är det nod 3. Den primära tjänsterepliken hanterar läs- och skrivåtgärder.  Ändringar i tjänstens tillstånd replikeras sedan ut till de sekundära replikerna som körs på noderna 0 och 1 i skärmbilden nedan.

    ![Hitta den primära repliken i Service Fabric Explorer][sfx-primary]

3. Klicka på noden du hittade i föregående steg under **Noder** och välj sedan **Inaktivera (starta om)** på menyn Åtgärder. Den här åtgärden startar om noden som kör den primära tjänsterepliken och tvingar fram en redundansväxling till en av de sekundära replikerna som körs på en annan nod.  Den sekundära repliken befordras till primär, en annan sekundär replik skapas på en annan nod och den primära repliken börjar hantera läs- och skrivåtgärder. Titta på resultatet från testklienten när noden startas om, och observera att räknaren fortsätter att räkna upp trots redundansväxlingen.

## <a name="add-another-service-to-the-application"></a>Lägga till en annan tjänst i programmet
Om du vill lägga till en till tjänst i ett befintligt program som har skapats med hjälp av `yo` utför du följande steg:
1. Ändra katalogen till roten för det befintliga programmet.  Till exempel `cd ~/YeomanSamples/MyApplication` om `MyApplication` är programmet som skapats av Yeoman.
2. Kör `yo azuresfjava:AddService`
3. Skapa och distribuera programmet som i föregående steg.

## <a name="remove-the-application"></a>Ta bort programmet
Använd installationsskriptet som medföljer mallen för att ta bort programinstansen, avregistrera programpaketet och ta bort programpaketet från klustrets avbildningslager.

```bash
./uninstall.sh
```

I Service Fabric Explorer ser du att programmet och programtypen inte längre visas i noden **Program**.

## <a name="next-steps"></a>Nästa steg
* [Skapa ditt första Service Fabric Java-program för Linux med hjälp av Eclipse](service-fabric-get-started-eclipse.md)
* [Läs mer om Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interagera med Service Fabric-kluster med Azure CLI](service-fabric-azure-cli.md)
* [Felsökning av distribution](service-fabric-azure-cli.md#troubleshooting)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

## <a name="related-articles"></a>Relaterade artiklar

* [Kom igång med Service Fabric och Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Kom igång med Service Fabric XPlat CLI](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

