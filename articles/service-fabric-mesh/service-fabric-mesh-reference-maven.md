---
title: Referens för Azure Service Fabric nät Maven | Microsoft Docs
description: Innehåller referens för hur du använder Maven-pluginprogrammet för Service Fabric-nät
services: service-fabric-mesh
keywords: maven, java, cli
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.service: service-fabric-mesh
manager: subramar
ms.openlocfilehash: 08e842f5b91bd0ca5f8e8b2a7866f3f9a689ac28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811627"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Maven-pluginprogrammet för Service Fabric-nät

## <a name="prerequisites"></a>Nödvändiga komponenter

- Java SDK
- Maven
- Azure CLI med nät-tillägg
- Service Fabric CLI

## <a name="goals"></a>Mål

### `azure-sfmesh:init`
- Skapar en `servicefabric` mapp som innehåller en `appresources` mapp som innehåller den `application.yaml` filen. 

### `azure-sfmesh:addservice`
- Skapar en mapp inuti `servicefabric` mapp med namnet på tjänsten och skapar tjänstens YAML-fil. 

### `azure-sfmesh:addnetwork`
- Genererar en `network` YAML till det angivna nätverksnamnet i den `appresources` mapp 

### `azure-sfmesh:addgateway`
- Genererar en `gateway` YAML med angivna gatewaynamn i den `appresources` mapp 

### `azure-sfmesh:addsecret`
- Genererar en `secret` YAML med angivna hemliga namn i den `appresources` mapp 

### `azure-sfmesh:addsecretvalue`
- Genererar en `secretvalue` YAML med det angivna värde för hemliga och hemliga namnet i den `appresources` mapp 

### `azure-sfmesh:deploy`
- Sammanfogar yamls från den `servicefabric` mappen och skapar en Azure Resource Manager-mallens JSON i den aktuella mappen.
- Distribuerar alla resurser i Azure Service Fabric nät miljön 

### `azure-sfmesh:deploytocluster`
- Skapar en mapp (`meshDeploy`) som innehåller distributionen för som genereras från yamls som kan användas för Service Fabric-kluster
- Distribuerar alla resurser till Service Fabric-kluster
 

## <a name="usage"></a>Användning

Om du vill använda Maven-pluginprogrammet i Maven Java-appen lägger du till följande kodavsnitt i filen pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Vanlig konfiguration

Maven-pluginprogrammet stöder för närvarande inte vanliga konfigurationer för Maven-pluginprogram för Azure.

## <a name="how-to"></a>Instruktioner

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Initiera Maven-projekt för Azure Service Fabric nät
Kör följande kommando för att skapa resursen YAML-fil för programmet.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Skapar en mapp med namnet `servicefabric->appresources` i rotmappen som innehåller ett program YAML med namnet `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Lägg till resurs i ditt program

#### <a name="add-a-new-network-to-your-application"></a>Lägg till ett nytt nätverk i ditt program
Kör kommandot nedan för att skapa en resurs yaml för nätverket. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.4/22
```

- Skapar ett nätverk YAML i mappen `servicefabric->appresources` med namnet `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Lägga till en ny tjänst i ditt program
Kör kommandot nedan för att skapa en yaml för tjänsten. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Skapar en tjänst YAML i mappen `servicefabric->helloworldservice` med namnet `service_helloworldservice` som refererar till `helloworldservicenetwork` & den `helloworldserver` app
- Tjänsten skulle lyssna på port 8080
- Tjänsten skulle använda ***helloworldserver:latest*** är behållaravbildning.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Lägg till en ny gateway-resurs i ditt program
Kör kommandot nedan för att skapa en yaml för gateway-resursen. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Skapar en ny gateway YAML i mappen `servicefabric->appresources` med namnet `gateway_helloworldgateway`
- Referenser `helloworldservicelistener` som service-lyssnare som lyssnar på anrop från den här gatewayen. Även hänvisar till den `helloworldservice` som tjänst, `helloworldservicenetwork` som nätverket och `helloworldserver` som programmet. 
- Lyssnar efter förfrågningar på port 80

#### <a name="add-a-new-volume-to-your-application"></a>Lägga till en ny volym i ditt program
Kör kommandot nedan för att skapa en volym resource yaml. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Skapar en volym YAML i mappen `servicefabric->appresources` med namnet `volume_vol1`
- Anger egenskaperna för obligatoriska parametrar `volumeAccountKey`, och `volumeShareName` som ovan
- Mer information om hur du refererar till volymen du skapade finns följande, [distribuera appen med hjälp av Azure filer volym](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Lägg till en ny hemlig resurs i ditt program
Kör kommandot nedan för att skapa en hemlig resource yaml. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Skapar en hemlig YAML i mappen `servicefabric->appresources` med namnet `secret_secret1`
- Mer information om hur du refererar du skapade hemliga finns följande, [hantera hemligheter](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Lägg till en ny secretvalue resurs i ditt program
Kör kommandot nedan för att skapa en secretvalue resurs yaml. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Skapa en secretvalue YAML i mappen `servicefabric->appresources` med namnet `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Kör programmet lokalt

Med hjälp av målet `azure-sfmesh:deploytocluster`, du kan köra programmet lokalt med hjälp av kommandot nedan:

```cmd
mvn azure-sfmesh:deploytocluster
```

Som standard distribuerar det här målet resurser till det lokala klustret. Om du distribuerar till det lokala klustret, förutsätts det att du har ett lokalt Service Fabric-kluster och drift. Lokalt Service Fabric-kluster för resurser stöds för närvarande bara på [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Skapar för från yamls som kan användas för Service Fabric-kluster
- Distribuerar sedan till klusterslutpunkten

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Distribuera program till Azure Service Fabric nät

Med hjälp av målet `azure-sfmesh:deploy`, du kan distribuera till Service Fabric-nät miljö genom att köra kommandot nedan:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Skapar en resursgrupp med namnet `todoapprg` om den inte finns.
- Skapar en Azure Resource Manager-mallens JSON genom att koppla YAMLs. 
- Distribuerar JSON till Azure Service Fabric nät-miljö.