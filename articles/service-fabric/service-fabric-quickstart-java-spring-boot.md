---
title: Skapa en Spring Boot-app för Service Fabric i Azure | Microsoft Docs
description: I den här snabbstarten distribuerar du ett Spring Boot-program för Azure Service Fabric med ett Spring Boot-exempelprogram.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: e4fde75aeaf86219518daf92b67434fe9fd63f86
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297421"
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-service-fabric"></a>Snabbstart: Distribuera ett Java Spring Boot-program till Service Fabric

Azure Service Fabric är en plattform för distribuerade system för distribution och hantering av mikrotjänster och containrar.

Den här snabbstarten visar hur du distribuerar ett Spring Boot-program till Service Fabric. I den här snabbstarten används exemplet [Komma igång](https://spring.io/guides/gs/spring-boot/) från Spring-webbplatsen. Med bekanta kommandoradsverktyg guidar den här snabbstarten igenom distribuering av Spring Boot-exemplet som ett Service Fabric-program. När du är klar fungerar exemplet Spring Boot Getting Started på Service Fabric.

![Skärmbild av programmet](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

I den här snabbstarten lär du dig att:

* Distribuera ett Spring Boot-program till Service Fabric
* Distribuera programmet till ditt lokala kluster
* Skala ut programmet över flera noder
* Utför redundans av tjänsten utan att påverka tillgängligheten

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten behöver du:

1. Installera Service Fabric SDK och Service Fabric Command Line Interface (CLI)

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
    
    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

1. [Installera Git](https://git-scm.com/)
1. Installera Yeoman

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
1. Konfigurera Java-miljö

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    b.  [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Hämta exemplet

Kör följande kommando i ett terminalfönster för att klona exempelappen Spring Boot Getting Started till den lokala datorn.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Skapa Spring Boot-programmet 
1. I katalogen `gs-spring-boot/complete` kör du kommandot nedan för att skapa programmet 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>Paketera Spring Boot-programmet 
1. I katalogen `gs-spring-boot` som klonades kör du kommandot `yo azuresfguest`. 

1. Ange följande information för varje fråga.

    ![Yeoman-poster](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

1. I mappen `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` skapar du en fil med namnet `entryPoint.sh`. Lägg till följande i filen `entryPoint.sh`. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

1. Lägg till resursen **Slutpunkter** i filen `gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml`

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    **ServiceManifest.xml** ser nu ut så här: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

I det här skedet har du skapat ett Service Fabric-program för exemplet Spring Boot Getting Started som du kan distribuera till Service Fabric.

## <a name="run-the-application-locally"></a>Kör programmet lokalt

1. Starta ditt lokala kluster på Ubuntu-datorer genom att köra följande kommando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Om du använder en Mac startar du det lokala klustret från Docker-avbildningen (detta antar att du har följt [förutsättningarna](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric) för att konfigurera det lokala klustret för Mac). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    Det kan ta lite tid att starta det lokala klustret. För att bekräfta att klustret är helt aktiverat öppnar du Service Fabric Explorer på **http://localhost:19080**. När du ser fem felfria noder vet du att det lokala klustret är igång och redo. 
    
    ![Felfritt lokalt kluster](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

1. Gå till mappen `gs-spring-boot/SpringServiceFabric`.
1. Kör följande kommando för att ansluta till det lokala klustret.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Kör skriptet `install.sh`.

    ```bash
    ./install.sh
    ```

1. Öppna valfri webbläsare och öppna programmet genom att gå till **http://localhost:8080**.

    ![Programmets lokala klientdel](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

Nu kan du komma åt Spring Boot-programmet som har distribuerats till ett Service Fabric-kluster.

## <a name="scale-applications-and-services-in-a-cluster"></a>Skala program och tjänster i ett kluster

Tjänsterna kan enkelt skalas över ett kluster om belastningen på tjänsterna ändras. Du kan skala en tjänst genom att ändra antalet instanser som körs i klustret. Det går att skala tjänsterna på flera sätt, till exempel med skript eller kommandon från Service Fabric CLI (sfctl). I följande steg använder du Service Fabric Explorer.

Service Fabric Explorer körs i alla Service Fabric-kluster och kan nås från en webbläsare genom att du bläddrar till klustrets HTTP-hanteringsport (19080), till exempel `http://localhost:19080`.

Så här skalar du webbklienttjänsten:

1. Öppna Service Fabric Explorer i ditt kluster, till exempel `http://localhost:19080`.
1. Klicka på ellipsknappen (tre punkter) bredvid noden **fabric:/SpringServiceFabric/SpringGettingStarted** i trädvyn och välj **Scale Service** (Skala tjänst).

    ![Skalningstjänst i Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Du kan nu välja att skala antalet instanser av tjänsten.

1. Ändra antalet till **3** och klicka på **Scale Service** (Skala tjänst).

    Ett annat sätt att skala tjänsten med hjälp av kommandoraden är som följer.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Klicka på noden **fabric:/SpringServiceFabric/SpringGettingStarted** i trädvyn och utöka partitionsnoden (som representeras av en globalt unik identifierare).

    ![Skalningstjänsten i Service Fabric Explorer har slutförts](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    Tjänsten har tre instanser, och trädvyn visar vilka noder instanserna körs på.

Med den här enkla hanteringsåtgärden har du dubblerat tillgängliga resurser för bearbetning av användarbelastningen i frontwebbtjänsten. Det är viktigt att förstå att du inte behöver flera instanser av en tjänst för att den ska kunna köras på ett tillförlitligt sätt. Om ett fel uppstår för en tjänst ser Service Fabric till att en ny tjänstinstans körs i klustret.

## <a name="fail-over-services-in-a-cluster"></a>Redundanstjänster i ett kluster

För att demonstrera tjänstredundans simuleras en nodomstart med Service Fabric Explorer. Se till att endast en instans av din tjänst körs.

1. Öppna Service Fabric Explorer i ditt kluster, till exempel `http://localhost:19080`.
1. Klicka på ellipsknappen (tre punkter) bredvid noden som kör instansen av din tjänst och starta om noden.

    ![Omstartsnod för Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
1. Instansen av tjänsten flyttas till en annan nod och ditt program får inget avbrott.

    ![Omstartsnod för Service Fabric Explorer lyckades](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Distribuera ett Spring Boot-program till Service Fabric
* Distribuera programmet till ditt lokala kluster
* Skala ut programmet över flera noder
* Utför redundans av tjänsten utan att påverka tillgängligheten

I självstudien för Java-appar finns mer information om hur du arbetar med Java-appar i Service Fabric.

> [!div class="nextstepaction"]
> [Distribuera en Java-app](./service-fabric-tutorial-create-java-app.md)
