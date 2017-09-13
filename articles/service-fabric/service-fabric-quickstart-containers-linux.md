---
title: "Skapa en Azure Service Fabric-behållarapp på Linux | Microsoft Docs"
description: "Skapa din första Linux-behållarapp på Azure Service Fabric.  Skapa en Docker-avbildning med din app, överför avbildningen till ett behållarregister och skapa och distribuera en Service Fabric-behållarapp."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 78306672e812745fd1902ae264c2adea196ab721
ms.contentlocale: sv-se
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-a-service-fabric-linux-container-application-on-azure"></a>Distribuera ett Service Fabric Linux-behållarprogram i Azure
Azure Service Fabric är en plattform för distribuerade system för distribution och hantering av skalbara och tillförlitliga mikrotjänster och behållare. 

Du behöver inga göra några ändringar i din app för att köra en befintlig app i en Linux-behållare i ett Service Fabric-kluster. Den här snabbstarten beskriver hur du distribuerar en fördefinierad Docker-behållaravbildning i ett Service Fabric-program. När du är klar har du en fungerande nginx-behållare.  Den här snabbstarten beskriver hur du distribuerar en Linux-behållare. Läs [den här snabbstarten](service-fabric-quickstart-containers.md) om du vill distribuera en Windows-behållare.

![Nginx][nginx]

I den här snabbstarten lär du dig att:
> [!div class="checklist"]
> * Paketera en Docker-avbildningsbehållare
> * Konfigurera kommunikation
> * Utveckla och distribuera ett Service Fabric-program
> * Distribuera behållarprogrammet till Azure

## <a name="prerequisites"></a>Krav
Installera [Service Fabric SDK, Service Fabric CLI och Yeoman-mallgeneratorerna för Service Fabric ](service-fabric-get-started-linux.md).
  
## <a name="package-a-docker-image-container-with-yeoman"></a>Paketera en Docker-avbildningsbehållare med Yeoman
I Service Fabric SDK för Linux finns en [Yeoman](http://yeoman.io/)-generator som gör det enkelt att skapa ditt program och lägga till en behållaravbildning. 

Om du vill skapa ett program för Service Fabric-behållare kan du öppna ett terminalfönster och köra `yo azuresfcontainer`.  

Ge programmet namnet ”MyFirstContainer” och ge programtjänsten namnet ”MyContainerService”.

Ge behållaravbildningen namnet ”nginx:latest” ([nginx-behållaravbildningen](https://hub.docker.com/r/_/nginx/) i Docker Hub). 

Eftersom den här avbildningen har en definierad startpunkt för arbetsbelastningen måste du uttryckligen ange indatakommandon. 

Ange ett instansantal på ”1”.

![Service Fabric Yeoman-generator för behållare][sf-yeoman]

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Konfigurera kommunikation och portmappning mellan behållare och värd
Konfigurera en HTTP-slutpunkt så att klienter kan kommunicera med din tjänst.  Öppna filen *./MyFirstContainer/MyContainerServicePkg/ServiceManifest.xml* och deklarera en slutpunktsresurs i elementet **ServiceManifest**.  Lägg till protokollet, porten och namnet. I den här snabbstarten lyssnar tjänsten på port 80: 

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
    <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
Genom att tillhandahålla `UriScheme` registreras automatiskt behållarslutpunkten med namngivningstjänsten för Service Fabric för identifiering. En fullständig ServiceManifest.xml-exempelfil finns i slutet av den här artikeln. 

Mappa en behållarport till en tjänst-`Endpoint` med hjälp av en `PortBinding`-princip i `ContainerHostPolicies` i filen ApplicationManifest.xml.  I den här snabbstarten är `ContainerPort` 80 (behållaren exponerar port 80) och `EndpointRef` är ”myserviceTypeEndpoint” (slutpunkten som tidigare definierats i tjänstmanifestet).  Inkommande begäranden till tjänsten på port 80 mappas till port 80 på behållaren.  

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Utveckla och distribuera ett Service Fabric-program
I Service Fabric Yeoman-mallarna ingår ett byggskript för [Gradle](https://gradle.org/) som du kan använda för att skapa programmet från terminalen. Spara alla ändringar.  När du ska bygga och paketera programmet kör du följande:

```bash
cd MyFirstContainer
gradle
```
## <a name="create-a-cluster"></a>Skapa ett kluster
Om du vill distribuera programmet till ett kluster i Azure kan du antingen välja att skapa ett eget kluster, eller använda ett partykluster.

Partykluster är kostnadsfria, tidsbegränsade Service Fabric-kluster i Azure som körs av Service Fabric-teamet där vem som helst kan distribuera program och lära sig mer om plattformen. [Följ dessa instruktioner](http://aka.ms/tryservicefabric) för att få åtkomst till ett partykluster.  

Information om hur du skapar ett eget kluster finns i [Skapa ditt första Service Fabric-kluster i Azure](service-fabric-get-started-azure-cluster.md).

Skriv ned anslutningsslutpunkten, som du ska använda i följande steg.

## <a name="deploy-the-application-to-azure"></a>Distribuera programmet till Azure
När du har skapat programmet kan du distribuera det till Azure-klustret med Service Fabric CLI.

Anslut till Service Fabric-klustret i Azure.

```bash
sfctl cluster select --endpoint http://lnxt10vkfz6.westus.cloudapp.azure.com:19080
```

Använd installationsskriptet som medföljer mallen för att kopiera programpaketet till klustrets avbildningsarkiv, registrera programtypen och skapa en instans av programmet.

```bash
./install.sh
```

Öppna en webbläsare och gå till Service Fabric Explorer på http://lnxt10vkfz6.westus.cloudapp.azure.com:19080/Explorer. Expandera programnoden och observera att det nu finns en post för din programtyp och en post för den första instansen av den typen.

![Service Fabric Explorer][sfx]

Anslut till den behållare som körs.  Öppna en webbläsare som pekar på IP-adressen som returneras på port 80, till exempel ”lnxt10vkfz6.westus.cloudapp.azure.com:80”. Välkomstsidan för nginx bör visas i webbläsaren.

![Nginx][nginx]

## <a name="clean-up"></a>Rensa
Använd avinstallationsskriptet som medföljer mallen för att ta bort programinstansen från klustret och avregistrera programtypen.

```bash
./uninstall.sh
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Komplett exempel på Service Fabric-app och tjänstmanifest
Här är de fullständiga tjänst- och programmanifesten som används i den här snabbstarten.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>nginx:latest</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>

```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest  ApplicationTypeName="MyFirstContainerType" ApplicationTypeVersion="1.0.0"
                      xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
   <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
   
   <DefaultServices>
      <Service Name="MyContainerService">
        <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
        cluster, set InstanceCount to -1 for the container service to run on every node in 
        the cluster.
        -->
        <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="1">
            <SingletonPartition />
        </StatelessService>
      </Service>
   </DefaultServices>
   
</ApplicationManifest>

```

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten lär du dig att:
> [!div class="checklist"]
> * Paketera en Docker-avbildningsbehållare
> * Konfigurera kommunikation
> * Utveckla och distribuera ett Service Fabric-program
> * Distribuera behållarprogrammet till Azure

* Mer information om hur du kör [behållare i Service Fabric](service-fabric-containers-overview.md).
* Läs kursen [Distribuera ett .NET-program i en behållare](service-fabric-host-app-in-a-container.md).
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).
* Se [kodexempel för Service Fabric-behållare](https://github.com/Azure-Samples/service-fabric-dotnet-containers) på GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/SFX.png
[nginx]: ./media/service-fabric-quickstart-containers-linux/nginx.png
[sf-yeoman]: ./media/service-fabric-quickstart-containers-linux/YoSF.png

