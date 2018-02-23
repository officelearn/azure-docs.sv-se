---
title: Distribuera ett Spring Boot-program till Azure Service Fabric | Microsoft Docs
description: "Distribuera ett Spring Boot-program för Azure Service Fabric med Spring Boot Getting Started."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 8e57ed29560baeea6279a45e8769f4005c123b56
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-a-spring-boot-application"></a>Distribuera ett Spring Boot-program
Azure Service Fabric är en plattform för distribuerade system för distribution och hantering av mikrotjänster och behållare. 

Den här snabbstarten visar hur du distribuerar ett Spring Boot-program till Service Fabric. I den här snabbstarten används exemplet [Komma igång](https://spring.io/guides/gs/spring-boot/) från Spring-webbplatsen. Med bekanta kommandoradsverktyg guidar den här snabbstarten igenom distribuering av Spring Boot-exemplet som ett Service Fabric-program. När du är klar fungerar exemplet Spring Boot Getting Started på Service Fabric. 

![Skärmbild av programmet](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

I den här snabbstarten lär du dig att:

> [!div class="checklist"]
> * Distribuera ett Spring Boot-program till Service Fabric
> * Distribuera programmet till ditt lokala kluster 
> * Distribuera programmet till ett kluster i Azure
> * Skala ut programmet över flera noder
> * Utför redundans av tjänsten utan att påverka tillgängligheten

## <a name="prerequisites"></a>Nödvändiga komponenter
För att slutföra den här snabbstarten behöver du:
1. [Installera Service Fabric SDK och Service Fabric Command Line Interface (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Installera Git](https://git-scm.com/)
3. [Installera Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. [Konfigurera Java-miljö](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Hämta exemplet
Kör följande kommando i ett kommandofönster för att klona exempelappen Spring Boot Getting Started till den lokala datorn.
```
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>Paketera Spring Boot-programmet 
1. I katalogen `gs-spring-boot` som klonades kör du kommandot `yo azuresfguest`. 

2. Ange följande information för varje fråga. 

    ![Yeoman-poster](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. I mappen `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` skapar du en fil med namnet `entryPoint.sh`. Lägg till följande i filen. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

I det här skedet har du skapat ett Service Fabric-program för exemplet Spring Boot Getting Started som du kan distribuera till Service Fabric.

## <a name="run-the-application-locally"></a>Kör programmet lokalt
1. Starta ditt lokala kluster genom att köra följande kommando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Det kan ta lite tid att starta det lokala klustret. Om du vill kontrollera att klustret är fullständigt aktiverat går du till Service Fabric Explorer på **http://localhost:19080**. När du ser fem felfria noder vet du att det lokala klustret är igång och redo. 
    
    ![Felfritt lokalt kluster](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. Gå till mappen `gs-spring-boot/SpringServiceFabric`.
3. Kör följande kommando för att ansluta till det lokala klustret. 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. Kör skriptet `install.sh`. 

    ```bash
    ./install.sh
    ```

5. Öppna valfri webbläsare och hitta programmet genom att gå till **http://localhost: 8080**. 

    ![Programmets lokala klientdel](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)
    
Nu kan du komma åt Spring Boot-programmet som har distribuerats till ett Service Fabric-kluster.  

## <a name="deploy-the-application-to-azure"></a>Distribuera programmet till Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Konfigurera ett Azure Service Fabric-kluster
Om du vill distribuera programmet till ett kluster i Azure kan du skapa ett eget kluster.

Partykluster är kostnadsfria, tidsbegränsade Service Fabric-kluster som finns på Azure. De körs av Service Fabric-teamet. Där kan alla distribuera program och lära sig mer om plattformen. [Följ dessa instruktioner](http://aka.ms/tryservicefabric) för att få åtkomst till ett partykluster. 

Du kan använda Service Fabric Explorer, CLI eller Powershell för att utföra hanteringsåtgärder på det säkra partklustret. Om du vill använda Service Fabric Explorer behöver du ladda ned PFX-filen från webbplatsen med partklustret och importera certifikatet till certifikatarkivet (Windows eller Mac) eller till webbläsaren (Ubuntu). Det finns inget lösenord för självsignerade certifikat från partklustret. 

För att kunna utföra hanteringsåtgärder med Powershell eller CLI måste du ha PFX (Powershell) eller PEM (CLI). Om du vill konvertera PFX-filen till en PEM-fil kör du följande kommando:  

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

Information om hur du skapar ett eget kluster finns i [Skapa ditt första Service Fabric-kluster i Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

> [!Note]
> Spring Boot-tjänsten är konfigurerad för att lyssna efter inkommande trafik på port 8080. Se till att den porten är öppen i ditt kluster. Porten är öppen om du använder ett partykluster.
>

### <a name="deploy-the-application-using-cli"></a>Distribuera programmet med CLI
Nu när programmet och klustret är redo kan du distribuera programmet till klustret direkt från kommandoraden.

1. Gå till mappen `gs-spring-boot/SpringServiceFabric`.
2. Kör följande kommando för att ansluta till ett säkert Azure-kluster. 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    Om klustret är skyddat med ett självsignerat certifikat kör du kommandot: 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. Kör skriptet `install.sh`. 

    ```bash
    ./install.sh
    ```

4. Öppna valfri webbläsare och hitta programmet genom att gå till **http://\<ConnectionIPOrUrl>:8080**. 

    ![Programmets lokala klientdel](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
Nu kan du komma åt Spring Boot-programmet som har distribuerats till ett Service Fabric-kluster.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Skala program och tjänster i ett kluster
Tjänsterna kan enkelt skalas över ett kluster när belastningen på tjänsterna ändras. Du kan skala en tjänst genom att ändra antalet instanser som körs i klustret. Det går att skala tjänsterna på flera sätt, till exempel med skript eller kommandon från Service Fabric CLI (sfctl). I det här exemplet används Service Fabric Explorer.

Service Fabric Explorer körs i alla Service Fabric-kluster och kan nås från en webbläsare genom att bläddra till klustrets HTTP-hanteringsport (19080), till exempel `http://localhost:19080`.

Gör så här om du vill skala frontwebbtjänsten:

1. Öppna Service Fabric Explorer i ditt kluster, till exempel `http://localhost:19080`.
2. Klicka på ellipsknappen (tre punkter) bredvid noden **fabric:/SpringServiceFabric/SpringGettingStarted** i trädvyn och välj **Scale Service** (Skala tjänst).

    ![Skalningstjänst i Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Du kan nu välja att skala antalet instanser av tjänsten.

3. Ändra antalet till **3** och klicka på **Scale Service** (Skala tjänst).

    Ett annat sätt att skala tjänsten med hjälp av kommandoraden är som följer.

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint http://localhost:19080

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. Klicka på noden **fabric:/SpringServiceFabric/SpringGettingStarted** i trädvyn och utöka partitionsnoden (som representeras av en globalt unik identifierare).

    ![Skalningstjänsten i Service Fabric Explorer har slutförts](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    Tjänsten har tre instanser, och trädvyn visar vilka noder instanserna körs på.

Med den här enkla hanteringsåtgärden utökas resurserna för bearbetning av användarbelastning för Spring-tjänsten. Det är viktigt att veta att du inte behöver flera instanser av en tjänst för att den ska köras på ett tillförlitligt sätt. Om en tjänst misslyckas ser Service Fabric till att en ny tjänstinstans körs i klustret.

## <a name="fail-over-services-in-a-cluster"></a>Redundanstjänster i ett kluster 
För att demonstrera tjänstredundans simuleras en nodomstart med Service Fabric Explorer. Se till att endast en instans av din tjänst körs. 

1. Öppna Service Fabric Explorer i ditt kluster, till exempel `http://localhost:19080`.
2. Klicka på ellipsknappen (tre punkter) bredvid noden som kör instansen av din tjänst och starta om noden. 

    ![Omstartsnod för Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. Instansen av tjänsten flyttas till en annan nod och ditt program får inget avbrott. 

    ![Omstartsnod för Service Fabric Explorer lyckades](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du lärt dig att:

> [!div class="checklist"]
> * Distribuera ett Spring Boot-program till Service Fabric
> * Distribuera programmet till ditt lokala kluster 
> * Distribuera programmet till ett kluster i Azure
> * Skala ut programmet över flera noder
> * Utför redundans av tjänsten utan att påverka tillgängligheten

* Läs mer om att [skapa Java-mikrotjänster med programmeringsmodeller för Service Fabric](service-fabric-quickstart-java-reliable-services.md)
* Läs mer om hur du [konfigurerar kontinuerlig integration och distribution med Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
* Se andra [Java-exempel](https://github.com/Azure-Samples/service-fabric-java-getting-started)