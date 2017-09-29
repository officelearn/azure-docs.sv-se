---
title: "Skapa ett Azure Service Fabric tillförlitliga aktörer Java-program på Linux | Microsoft Docs"
description: "Lär dig hur du skapar och distribuerar err Java Service Fabric tillförlitliga aktörer-program på fem minuter."
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
ms.date: 09/20/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: 68f9492231d367b1ede6ab032ec1c66c75150957
ms.contentlocale: sv-se
ms.lasthandoff: 09/21/2017

---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Skapa ditt första Java Service Fabric Reliable Actors-program på Linux
> [!div class="op_single_selector"]
> * [C# – Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java – Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Den här snabbstartsguiden hjälper dig att skapa ditt första Azure Service Fabric Java-program i en Linux-utvecklingsmiljö på bara några minuter.  När du är klar har du ett enkelt Java-program för en tjänst som körs i klustret för lokal utveckling.  

## <a name="prerequisites"></a>Krav
Innan du börjar ska du installera Service Fabric SDK, Service Fabric CLI och konfigurera ett utvecklingskluster i [Linux-utvecklingsmiljön](service-fabric-get-started-linux.md). Om du använder Mac OS X kan du [konfigurera en Linux-utvecklingsmiljö på en virtuell dator med hjälp av Vagrant](service-fabric-get-started-mac.md).

Du bör även installera [Service Fabric CLI](service-fabric-cli.md).

### <a name="install-and-set-up-the-generators-for-java"></a>Installera och konfigurera generatorerna för Java
Service Fabric tillhandahåller ramverktyg som hjälper dig att skapa ett Service Fabric Java-program från terminalen med en Yeoman-mallgenerator. Följ stegen nedan för att se till att du har Service Fabric Yeoman-mallgeneratorn för Java på datorn.
1. Installera nodejs och NPM på datorn

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. Installera [Yeoman](http://yeoman.io/)-mallgeneratorn på datorn från NPM

  ```bash
  sudo npm install -g yo
  ```
3. Installera Service Fabric Yeo Java-programgeneratorn från NPM

  ```bash
  sudo npm install -g generator-azuresfjava
  ```

## <a name="create-the-application"></a>Skapa programmet
Ett Service Fabric-program innehåller en eller flera tjänster, som var och en ansvarar för att leverera programmets funktioner. Generatorn som du installerade i förra avsnittet gör det enkelt att skapa din första tjänst och lägga till fler senare.  Du kan också skapa, bygga och distribuera Service Fabric Java-program med ett plugin-program för Eclipse. Läs mer i [Service Fabric-plugin-program för utveckling av Java-program i Eclipse](service-fabric-get-started-eclipse.md). I den här snabbstartguiden använder vi Yeoman till att skapa ett program med en enda tjänst, som lagrar och hämtar ett värde.

1. I en terminal, skriver du in ``yo azuresfjava``.
2. Namnge ditt program.
3. Välj vilken typ din första tjänst ska ha och namnge den. I den här guiden väljer vi en Reliable Actor-tjänst. Mer information om andra typer av tjänster finns i [Service Fabric programming model overview](service-fabric-choose-framework.md) (Översikt över programmeringsmodellen i Service Fabric).
   ![Service Fabric Yeoman-generator för Java][sf-yeoman]

## <a name="build-the-application"></a>Skapa programmet
I Service Fabric Yeoman-mallarna ingår ett byggskript för [Gradle](https://gradle.org/) som du kan använda för att skapa programmet från terminalen.
Service Fabric Java-beroenden hämtas från Maven. Om du vill skapa och arbeta med Service Fabric Java-programmen måste du se till att du har JDK och Gradle installerade. Om de inte har installerats än kan du köra följande för att installera JDK (openjdk-8-jdk) och Gradle -

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

När du ska bygga och paketera programmet kör du följande:

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Distribuera programmet
När du har skapat programmet kan du distribuera det till det lokala klustret.

1. Anslut till det lokala Service Fabric-klustret.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Kör installationsskriptet som medföljer mallen för att kopiera programpaketet till klustrets avbildningsarkiv, registrera programtypen och skapa en instans av programmet.

    ```bash
    ./install.sh
    ```

Distributionen går till på samma sätt som för andra Service Fabric-program. Detaljerade instruktioner finns i dokumentationen om att [hantera ett Service Fabric-program med Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md).

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

## <a name="remove-the-application"></a>Ta bort programmet
Använd installationsskriptet som medföljer mallen för att ta bort programinstansen, avregistrera programpaketet och ta bort programpaketet från klustrets avbildningslager.

```bash
./uninstall.sh
```

I Service Fabric Explorer ser du att programmet och programtypen inte längre visas i noden **Program**.

## <a name="service-fabric-java-libraries-on-maven"></a>Service Fabric Java-bibliotek på Maven
Service Fabric Java-bibliotek har lagrats i Maven. Du kan lägga till beroendena i ``pom.xml`` eller ``build.gradle`` för dina projekt så att de använder Service Fabric Java-bibliotek från **mavenCentral**. 

### <a name="actors"></a>Aktörer

Service Fabric-stöd för tillförlitliga aktörer för ditt program.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.12.0'
  }
  ```

### <a name="services"></a>Tjänster

Service Fabric Reliable Services för ditt program.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.12.0'
  }
  ```

### <a name="others"></a>Andra
#### <a name="transport"></a>Transport

Transportnivåstöd för Service Fabric Java-program. Du behöver inte uttryckligen lägga till det här beroendet till tillförlitliga aktörer- eller tjänstprogram, om du inte programmerar på transportnivån.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.12.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric-stöd

Systemnivåstöd för Service Fabric, som kommunicerar med ursprunglig Service Fabric-körning. Du behöver inte uttryckligen lägga till det här beroendet till tillförlitliga aktörer- eller tjänstprogram. Det hämtas automatiskt från Maven när du inkluderar de andra beroendena ovan.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.12.0'
  }
  ```

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrera gamla Service Fabric Java-program som ska användas med Maven
Vi har nyligen flyttat Service Fabric Java-bibliotek från Service Fabric Java-SDK till Maven-centrallager. De nya program som du genererar med hjälp av Yeoman eller Eclipse genererar senast uppdaterade projekt (som fungerar med Maven), men du kan uppdatera dina befintliga Service Fabric Java tillståndslösa eller aktörsprogram, som tidigare använde Service Fabric Java SDK, för att använda Service Fabric Java-beroenden från Maven. Följ anvisningarna [här](service-fabric-migrate-old-javaapp-to-use-maven.md) för att se till att det äldre programmet fungerar med Maven.

## <a name="next-steps"></a>Nästa steg

* [Skapa ditt första Service Fabric Java-program för Linux med hjälp av Eclipse](service-fabric-get-started-eclipse.md)
* [Läs mer om Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interagera med Service Fabric-kluster med Service Fabric CLI](service-fabric-cli.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)
* [Kom igång med Service Fabric CLI](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

