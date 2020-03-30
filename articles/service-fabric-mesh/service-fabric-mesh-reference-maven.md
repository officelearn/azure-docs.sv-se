---
title: Azure Service Fabric Mesh Maven-referens
description: Innehåller referensen för hur du använder Maven plugin för Service Fabric Mesh
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.openlocfilehash: bcc3fb7c6c3adce0997d0960c4d98227089b048b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75459018"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Maven Plugin för Service Fabric Mesh

## <a name="prerequisites"></a>Krav

- Java SDK
- Maven
- Azure CLI med mesh-tillägg
- Service Fabric CLI

## <a name="goals"></a>Mål

### `azure-sfmesh:init`
- Skapar en `servicefabric` mapp som `appresources` innehåller en `application.yaml` mapp som har filen. 

### `azure-sfmesh:addservice`
- Skapar en mapp `servicefabric` i mappen med tjänstnamnet och skapar tjänstens YAML-fil. 

### `azure-sfmesh:addnetwork`
- Genererar en `network` YAML med det angivna `appresources` nätverksnamnet i mappen 

### `azure-sfmesh:addgateway`
- Genererar en `gateway` YAML med det angivna `appresources` gatewaynamnet i mappen 

#### `azure-sfmesh:addvolume`
- Genererar en `volume` YAML med det angivna `appresources` volymnamnet i mappen.

### `azure-sfmesh:addsecret`
- Genererar en `secret` YAML med det angivna `appresources` hemliga namnet i mappen 

### `azure-sfmesh:addsecretvalue`
- Genererar en `secretvalue` YAML med det angivna hemliga `appresources` och hemliga värdenamnet i mappen 

### `azure-sfmesh:deploy`
- Sammanfogar yamls från `servicefabric` mappen och skapar en Azure Resource Manager-mall JSON i den aktuella mappen.
- Distribuerar alla resurser till Azure Service Fabric Mesh-miljön 

### `azure-sfmesh:deploytocluster`
- Skapar en mapp`meshDeploy`( ) som innehåller distributionenSOS som genereras från yamls som gäller för Service Fabric-kluster
- Distribuerar alla resurser till service fabric-klustret
 

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

## <a name="common-configuration"></a>Gemensam konfiguration

Maven-insticksprogrammet stöder för närvarande inte vanliga konfigurationer av Maven-insticksprogram för Azure.

## <a name="how-to"></a>Instruktioner

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Initiera Maven-projekt för Azure Service Fabric Mesh
Kör följande kommando för att skapa YAML-filen för programresursen.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Skapar en mapp `servicefabric->appresources` som anropas i rotmappen som innehåller ett program med namnet YAML`app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Lägga till resurs i ditt program

#### <a name="add-a-new-network-to-your-application"></a>Lägga till ett nytt nätverk i programmet
Kör kommandot nedan för att skapa en nätverksresurs yaml. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Skapar ett nätverk YAML `servicefabric->appresources` i mappen med namnet`network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Lägga till en ny tjänst i ditt program
Kör kommandot nedan för att skapa en tjänst yaml. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Skapar en tjänst-YAML `servicefabric->helloworldservice` `service_helloworldservice` i mappen `helloworldservicenetwork` som refererar `helloworldserver` & appen
- Tjänsten skulle lyssna på port 8080
- Tjänsten skulle använda ***helloworldserver: senaste*** eftersom det är container bild.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Lägga till en ny gatewayresurs i ditt program
Kör kommandot nedan för att skapa en gateway-resurs yaml. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Skapar en ny gateway YAML i mappen `servicefabric->appresources` med namnet`gateway_helloworldgateway`
- Referenser `helloworldservicelistener` som tjänstlyssnare som lyssnar på samtal från den här gatewayen. Refererar också `helloworldservice` till tjänsten `helloworldservicenetwork` som tjänst, som nätverk och `helloworldserver` som program. 
- Lyssnar på begäran om port 80

#### <a name="add-a-new-volume-to-your-application"></a>Lägga till en ny volym i programmet
Kör kommandot nedan för att skapa en volymresurs yaml. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Skapar en volym-YAML `servicefabric->appresources` i mappen med namnet`volume_vol1`
- Anger egenskaper för `volumeAccountKey`obligatoriska `volumeShareName` parametrar och som ovan
- Mer information om hur du refererar till den här skapade volymen finns i följande, [Distribuera app med Azure Files Volume](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Lägga till en ny hemlig resurs i ditt program
Kör kommandot nedan för att skapa en hemlig resurs yaml. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Skapar en hemlig YAML `servicefabric->appresources` i mappen med namnet`secret_secret1`
- Mer information om hur du refererar till den här skapade hemligheten finns i följande, [Hantera hemligheter](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Lägga till en ny hemligvärdesresurs i ditt program
Kör kommandot nedan för att skapa en secretvalue resurs yaml. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Skapa ett hemligt värde `servicefabric->appresources` YAML i mappen med namnet`secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Kör programmet lokalt

Med hjälp av `azure-sfmesh:deploytocluster`målet kan du köra programmet lokalt med kommandot nedan:

```cmd
mvn azure-sfmesh:deploytocluster
```

Som standard distribuerar detta mål resurser till lokalt kluster. Om du distribuerar till lokalt kluster förutsätts det att du har ett lokalt Service Fabric-kluster igång. Local Service Fabric-kluster för resurser stöds för närvarande endast i [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Skapar JSON från yamls som gäller för Service Fabric-kluster
- Distribuerar sedan till klusterslutpunkten

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Distribuera program till Azure Service Fabric Mesh

Med hjälp av `azure-sfmesh:deploy`målet kan du distribuera till Service Fabric Mesh Environment genom att köra kommandot nedan:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Skapar en resursgrupp `todoapprg` som anropas om den inte finns.
- Skapar en Azure Resource Manager-mall JSON genom att slå samman YAMLs. 
- Distribuerar JSON till Azure Service Fabric Mesh-miljön.
