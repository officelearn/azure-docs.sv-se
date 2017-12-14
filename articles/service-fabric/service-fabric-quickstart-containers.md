---
title: "Skapa ett Windows-behållarprogram i Azure Service Fabric | Microsoft Docs"
description: "Skapa din första Windows-behållarapp på Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 9d3d15c63055f3eeb0e6cb292d75a8c42b33f7fe
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="deploy-a-service-fabric-windows-container-application-on-azure"></a>Distribuera ett Service Fabric Windows-behållarprogram i Azure
Azure Service Fabric är en plattform för distribuerade system för distribution och hantering av skalbara och tillförlitliga mikrotjänster och behållare. 

Du behöver inga göra några ändringar i din app för att köra en befintlig app i en Windows-behållare i ett Service Fabric-kluster. Den här snabbstarten beskriver hur du distribuerar en fördefinierad Docker-behållaravbildning i ett Service Fabric-program. När du har slutfört kursen har du en fungerande Windows Server 2016-baserad Nano Server- och IIS-behållare. Den här snabbstarten beskriver hur du distribuerar en Windows-behållare. Läs [den här snabbstarten](service-fabric-quickstart-containers-linux.md) om du vill distribuera en Linux-behållare.

![IIS-standardwebbsidan][iis-default]

I den här snabbstarten lär du dig att:
> [!div class="checklist"]
> * Paketera en Docker-avbildningsbehållare
> * Konfigurera kommunikation
> * Utveckla och distribuera ett Service Fabric-program
> * Distribuera behållarprogrammet till Azure

## <a name="prerequisites"></a>Krav
* En Azure-prenumeration (du kan skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* En utvecklingsdator som kör:
  * Visual Studio 2015 eller Visual Studio 2017.
  * [Service Fabric SDK och verktyg](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Paketera en Docker-avbildningsbehållare med Visual Studio
Service Fabric SDK och verktygen innehåller en tjänstmall som hjälper dig att distribuera en behållare till ett Service Fabric-kluster.

Starta Visual Studio som Administratör.  Välj **Arkiv** > **Nytt** > **Projekt**.

Välj **Service Fabric-programmet**, ge det namnet "MyFirstContainer" och klicka på **OK**.

Välj **Behållare** i listan med **tjänstmallar**.

I **Avbildningsnamn** anger du "microsoft/iis:nanoserver", [Windows Server Nano Server och IIS-avbildningsnamn](https://hub.docker.com/r/microsoft/iis/). 

Ge tjänsten namnet ”MyContainerService” och klicka på **OK**.

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Konfigurera kommunikation och portmappning mellan behållare och värd
Tjänsten behöver en slutpunkt för kommunikation.  Nu kan du lägga till protokollet, porten och typen för en `Endpoint` i filen ServiceManifest.xml. I den här snabbstarten lyssnar tjänsten på port 80: 

```xml
<Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
```
Genom att tillhandahålla `UriScheme` registreras automatiskt behållarslutpunkten med namngivningstjänsten för Service Fabric för identifiering. En fullständig ServiceManifest.xml-exempelfil finns i slutet av den här artikeln. 

Konfigurera behållarens portmappning (port till värd) genom att ange en `PortBinding`-princip i `ContainerHostPolicies` för filen ApplicationManifest.xml.  I den här snabbstarten är `ContainerPort` 80 och `EndpointRef` ”MyContainerServiceTypeEndpoint” (slutpunkten som definierats i tjänstmanifestet).  Inkommande begäranden till tjänsten på port 80 mappas till port 80 på behållaren.  

```xml
<ServiceManifestImport>
...
  <ConfigOverrides />
  <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
    </ContainerHostPolicies>
  </Policies>
</ServiceManifestImport>
```

En fullständig ApplicationManifest.xml -exempelfil finns i slutet av den här artikeln.

## <a name="create-a-cluster"></a>Skapa ett kluster
Om du vill distribuera programmet till ett kluster i Azure kan du antingen välja att skapa ett eget kluster, eller använda ett partykluster.

Partykluster är kostnadsfria, tidsbegränsade Service Fabric-kluster i Azure som körs av Service Fabric-teamet där vem som helst kan distribuera program och lära sig mer om plattformen. [Följ dessa instruktioner](http://aka.ms/tryservicefabric) för att få åtkomst till ett partykluster.  

Information om hur du skapar ett eget kluster finns i [Skapa ditt första Service Fabric-kluster i Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Skriv ned anslutningsslutpunkten, som du ska använda i följande steg.  

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Distribuera programmet till Azure med hjälp av Visual Studio
Nu när programmet är redo kan du distribuera det till ett kluster direkt från Visual Studio.

Högerklicka på **MyFirstContainer** i Solution Explorer och välj **Publicera**. Dialogrutan Publicera visas.

![Dialogrutan Publicera](./media/service-fabric-quickstart-dotnet/publish-app.png)

Ange anslutningsslutpunkten för klustret i fältet **Connection Endpoint** (Anslutningsslutpunkt). När du registrerar dig för partyklustret anger du anslutningsslutpunkten i webbläsaren, t.ex. `winh1x87d1d.westus.cloudapp.azure.com:19000`.  Klicka på **Publicera** så distribueras programmet.

Öppna en webbläsare och gå till http://winh1x87d1d.westus.cloudapp.azure.com:80. Du bör se IIS-standardwebbsidan: ![IIS-standardwebbsidan][iis-default]

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Komplett exempel på Service Fabric-app och tjänstmanifest
Här är de fullständiga tjänst- och programmanifesten som används i den här snabbstarten.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>microsoft/iis:nanoserver</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="MyContainerService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="MyContainerService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="[MyContainerService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du lärt dig att:
> [!div class="checklist"]
> * Paketera en Docker-avbildningsbehållare
> * Konfigurera kommunikation
> * Utveckla och distribuera ett Service Fabric-program
> * Distribuera behållarprogrammet till Azure

* Mer information om hur du kör [behållare i Service Fabric](service-fabric-containers-overview.md).
* Läs kursen [Distribuera ett .NET-program i en behållare](service-fabric-host-app-in-a-container.md).
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).
* Se [kodexempel för Service Fabric-behållare](https://github.com/Azure-Samples/service-fabric-containers) på GitHub.

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
