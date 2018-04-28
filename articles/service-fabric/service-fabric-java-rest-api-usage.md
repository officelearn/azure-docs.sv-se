---
title: 'Azure Service Fabric Java klienten API: er | Microsoft Docs'
description: 'Skapa och använda Service Fabric Java-klientens API: er med hjälp av REST API-specifikationen för Service Fabric-klienten'
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 9596e55c6c915461ef4d0bff0d7f9583aac18a1c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-service-fabric-java-client-apis"></a>Azure Service Fabric Java klienten API: er

Service Fabric-klienten API: er innebär att distribuera och hantera mikrotjänster baserade program och behållare i en Service Fabric-kluster i Azure, på plats, på lokal utvecklingsdator eller i andra moln. Den här artikeln beskriver hur du skapar och använda Service Fabric Java-klientens API: er ovanpå REST API: er för Service Fabric-klienten

## <a name="generate-the-client-code-using-autorest"></a>Generera klientkod med AutoRest

[AutoRest](https://github.com/Azure/autorest) är ett verktyg som genererar klientbibliotek för att komma åt RESTful-tjänster. Indata för AutoRest är en specifikation som beskriver REST-API med formatet OpenAPI-specifikationen. [Service Fabric-klienten REST API: er](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) följer den här specifikationen.

Följ anvisningarna nedan för att generera klientkod för Service Fabric Java med hjälp av verktyget AutoRest.

1. Installera nodejs och NPM på datorn

    Om du använder Linux sedan:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Om du använder Mac OS X sedan:
    ```bash
    brew install node
    ```

2. Installera AutoRest med NPM.
    ```bash
    npm install -g autorest
    ```

3. Förgrening och klona [azure-rest-api-specifikationerna](https://github.com/Azure/azure-rest-api-specs) databasen på din lokala dator och gå till den klonade platsen från terminal på din dator.


4. Gå till den plats som anges nedan i din klonade lagringsplatsen.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Om din version av klustret inte 6.0. * Gå sedan till den aktuella katalogen i mappen stabil.
    >   

5. Kör följande kommando för autorest att generera klientkod java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Nedan visas ett exempel som visar användningen av autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Följande kommando tar ``servicefabric.json`` specifikationen filen som indata och genererar klientkod för java i ``java-rest-api-     code`` mapp och omsluter koden i ``servicefabricrest`` namnområde. Efter det här steget finns två mappar ``models``, ``implemenation`` och två filer ``ServiceFabricClientAPIs.java`` och ``package-info.java`` genereras i den ``java-rest-api-code`` mapp.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Inkludera och använda den genererade klienten i ditt projekt

1. Lägg till den genererade koden korrekt i projektet. Vi rekommenderar att du skapar ett bibliotek med den genererade koden och inkludera det här biblioteket i projektet.
2. Om du skapar ett bibliotek inkludera följande beroende i bibliotekets projekt. Om du använder en annan metod innehålla beroendet på lämpligt sätt.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Om du använder Maven build-system omfattar följande i till exempel din ``pom.xml`` fil:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Skapa en RestClient med följande kod:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Använd klienten objektet och lämpliga-anrop som krävs. Här följer några exempel som demonstrerar användningen av objekt. Vi förutsätter att programpaketet är inbyggd och överförs till avbildningsarkivet innan du använder den under API: er.
    * Etablera ett program
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Skapa ett program

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>Förstå den genererade koden
För alla API: T hittar du fyra överlagringar av implementeringen. Om det finns valfria parametrar skulle du hitta fyra flera variationer, inklusive de valfria parametrarna. Till exempel vara API: et ``removeReplica``.
 1. **offentliga void removeReplica (sträng nodnamn, UUID partitionId, sträng replicaId, booleskt forceRemove, lång timeout)**
    * Det här är den synkrona varianten av removeReplica API-anrop
 2. **offentliga ServiceFuture<Void> removeReplicaAsync (String nodnamn, UUID partitionId, sträng replicaId, booleskt forceRemove, lång timeout, sista ServiceCallback<Void> serviceCallback)**
    * Den här variant av API-anrop kan användas om du vill använda framtida baserat asynkron programmering och använda återanrop
 3. **offentliga observeras<Void> removeReplicaAsync (sträng nodnamn, UUID partitionId, sträng replicaId)**
    * Den här variant av API-anrop kan användas om du vill använda reaktiv asynkron programmering
 4. **offentliga observerades < ServiceResponse<Void>> removeReplicaWithServiceResponseAsync (sträng nodnamn, UUID partitionId, sträng replicaId)**
    * Den här variant av API-anrop kan användas om du vill använda reaktiv asynkron programmering och hantera RÅDATA rest-svar

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Service Fabric REST API: er](https://docs.microsoft.com/rest/api/servicefabric/)

