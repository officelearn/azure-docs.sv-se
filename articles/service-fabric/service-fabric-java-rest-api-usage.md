---
title: 'Azure Service Fabric Java-klient API: er | Microsoft Docs'
description: 'Skapa och använda Service Fabric Java-klientens API: er med hjälp av REST API-specifikationen för Service Fabric-klienten'
services: service-fabric
documentationcenter: java
author: rapatchi
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 97bba87331965b0f7ce20ec2ee089e0e18f72457
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720288"
---
# <a name="azure-service-fabric-java-client-apis"></a>Azure Service Fabric Java-klient API: er

Service Fabric-klientens API: er kan distribuera och hantera mikrotjänster baserat program och behållare i Service Fabric-kluster på Azure, lokalt, på lokala utvecklingsdator eller i andra moln. Den här artikeln beskrivs hur du skapar och använder Service Fabric Java-klientens API: er ovanpå REST API: er för Service Fabric-klienten

## <a name="generate-the-client-code-using-autorest"></a>Generera klientkod med AutoRest

[AutoRest](https://github.com/Azure/autorest) är ett verktyg som genererar-klientbibliotek för att komma åt RESTful-webb-tjänster. Indata till AutoRest är en specifikation som beskriver REST-API i formatet för OpenAPI-specifikation. [Service Fabric-klientens REST API: er](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) följer den här specifikationen.

Följ stegen nedan för att generera klientkod för Service Fabric Java med hjälp av verktyget AutoRest.

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

3. Förgrening och kloning [azure-rest-api-specifikationer](https://github.com/Azure/azure-rest-api-specs) lagringsplatsen i den lokala datorn och gå till den klonade platsen från terminalen på din dator.


4. Gå till den plats som nämns i den klonade lagringsplatsen.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Om klustret-versionen inte är 6.0. * Gå sedan till den aktuella katalogen i mappen stabil.
    >   

5. Kör följande autorest-kommando för att generera klientkod för java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Nedan visas ett exempel som demonstrerar användningen av autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Följande kommando tar ``servicefabric.json`` specifikationen fil som indata och genererar klientkod för java i ``java-rest-api-     code`` mapp och omsluter koden i ``servicefabricrest`` namnområde. Efter det här steget kan du hitta två mappar ``models``, ``implementation`` och två filer ``ServiceFabricClientAPIs.java`` och ``package-info.java`` genererats i den ``java-rest-api-code`` mapp.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Inkludera och använda den genererade klienten i ditt projekt

1. Lägg till den genererade koden på rätt sätt i ditt projekt. Vi rekommenderar att du skapar ett bibliotek med den genererade koden och inkludera det här biblioteket i projektet.
2. Om du skapar ett bibliotek omfattar följande beroende på din biblioteksprojekt. Om du använder en annan metod sedan inkludera beroenden på rätt sätt.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Exempel: Om du använder Maven build-system omfattar följande i din ``pom.xml`` fil:

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
4. Använd klientobjektet och göra lämplig anrop efter behov. Här följer några exempel som demonstrerar användningen av klientobjektet. Vi antar att programpaketet är inbyggd och har överförts till avbildningsarkivet innan du använder den under API: er.
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
Du kommer märka fyra överlagringar av implementering för alla API: er. Om det finns valfria parametrar kan du hitta fyra mer variationer, inklusive de valfria parametrarna. Överväg till exempel att API: et ``removeReplica``.
 1. **offentliga void removeReplica (sträng nodnamn, UUID partitionId, sträng replicaId, booleskt forceRemove, lång timeout)**
    * Det här är den synkrona varianten av removeReplica API-anrop
 2. **offentliga ServiceFuture\<Void > removeReplicaAsync (String nodnamn, UUID partitionId, sträng replicaId, booleskt forceRemove, lång timeout, sista ServiceCallback\<Void > serviceCallback)**
    * Den här variant av API-anrop som kan användas om du vill använda framtida baserat asynkron programmering och använda återanrop
 3. **offentliga övervakas\<Void > removeReplicaAsync (sträng nodnamn, UUID partitionId, sträng replicaId)**
    * Den här variant av API-anrop som kan användas om du vill använda reaktiva asynkron programmering
 4. **offentliga övervakas\<ServiceResponse\<Void >> removeReplicaWithServiceResponseAsync (sträng nodnamn, UUID partitionId, sträng replicaId)**
    * Den här variant av API-anrop som kan användas om du vill använda reaktiva asynkron programmering och hantera RAW rest-svar

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Service Fabric REST API: er](https://docs.microsoft.com/rest/api/servicefabric/)

