---
title: Azure Service Fabric Java-klient-API:er
description: Generera och använda Api:er för Service Fabric Java-klient med hjälp av REST API-specifikation för Service Fabric-klient
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 0a243c1cd0ab0dcb93a1cc6169c89ba18606f346
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451668"
---
# <a name="azure-service-fabric-java-client-apis"></a>Azure Service Fabric Java-klient-API:er

Service Fabric-klient-API:er gör det möjligt att distribuera och hantera mikrotjänstbaserade program och behållare i ett Service Fabric-kluster på Azure, lokalt, på lokal utvecklingsdator eller i andra moln. I den här artikeln beskrivs hur du skapar och använder Api:er för Service Fabric Java-klient ovanpå REST-API:erna för Service Fabric-klienten

## <a name="generate-the-client-code-using-autorest"></a>Generera klientkoden med AutoRest

[AutoRest](https://github.com/Azure/autorest) är ett verktyg som genererar klientbibliotek för åtkomst till RESTful-webbtjänster. Indata till AutoRest är en specifikation som beskriver REST API med openapi-specifikationsformatet. [REST-API:er för service fabric-klient följer](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) den här specifikationen .

Följ stegen nedan för att generera Service Fabric Java-klientkod med hjälp av autorest-verktyget.

1. Installera nodejs och NPM på datorn

    Om du använder Linux så:
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

3. Gaffel och [klona azure-rest-api-specs-databasen](https://github.com/Azure/azure-rest-api-specs) i din lokala dator och gå till den klonade platsen från terminalen på din dator.


4. Gå till den plats som nämns nedan i din klonade repo.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Om klusterversionen inte är 6.0.* går du till rätt katalog i den stabila mappen.
    >   

5. Kör följande autostödskommando för att generera java-klientkoden.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Nedan är ett exempel som visar användningen av autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Följande kommando ``servicefabric.json`` tar specifikationsfilen som indata ``java-rest-api-     code`` och genererar java-klientkod ``servicefabricrest`` i mappen och omsluter koden i namnområdet. Efter det här steget skulle ``models`` ``implementation`` du hitta ``ServiceFabricClientAPIs.java`` ``package-info.java`` två mappar ``java-rest-api-code`` och två filer och genereras i mappen.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Inkludera och använda den genererade klienten i projektet

1. Lägg till den genererade koden på rätt sätt i projektet. Vi rekommenderar att du skapar ett bibliotek med den genererade koden och inkluderar det här biblioteket i projektet.
2. Om du skapar ett bibliotek tar du med följande beroende i bibliotekets projekt. Om du följer en annan metod så inkludera beroendet på rätt sätt.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Om du till exempel använder Maven-byggsystemet ``pom.xml`` finns följande i filen:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Skapa en RestClient med hjälp av följande kod:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Använd klientobjektet och gör lämpliga anrop efter behov. Här är några exempel som visar användningen av klientobjekt. Vi antar att programpaketet är byggt och uppladdat i bildarkivet innan du använder nedanstående API:er.
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
För varje API hittar du fyra överbelastningar av genomförandet. Om det finns valfria parametrar skulle du hitta ytterligare fyra varianter, inklusive de valfria parametrarna. Tänk till exempel ``removeReplica``på API .
 1. **offentligt ogiltigt removeReplica(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout)**
    * Detta är den synkrona varianten av removeReplica API-anropet
 2. **public serviceFuture\<Void> removeReplicaAsync(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout, final ServiceCallback\<Void> serviceCallback)**
    * Den här varianten av API-anrop kan användas om du vill använda framtida baserad asynkron programmering och använda motringningar
 3. **offentlig observerbar annullering\<> ta bortReplicaAsync(String nodeName, UUID partitionId, String replicaId)**
    * Den här varianten av API-anrop kan användas om du vill använda reaktiv asynkron programmering
 4. **offentlig\<annullering\<av Observer-tjänstsvar>> ta bortReplicaWithServiceResponseAsync(String nodeName, UUID partitionId, String replicaId)**
    * Denna variant av API-anrop kan användas om du vill använda reaktiv asynkron programmering och hantera RAW-vila svar

## <a name="next-steps"></a>Nästa steg
* Läs mer om [REST-API:er för servicetygn](https://docs.microsoft.com/rest/api/servicefabric/)

