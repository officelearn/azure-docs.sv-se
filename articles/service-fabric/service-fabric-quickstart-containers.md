---
title: Skapa ett Windows-behållarprogram i Azure Service Fabric | Microsoft Docs
description: I den här snabbstarten skapar du ditt första Windows-behållarprogram i Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/18
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: eb4de9d7781ae355e42a6fec9f7732ad67228e70
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-deploy-a-service-fabric-windows-container-application-on-azure"></a>Snabbstart: Distribuera ett Windows-behållarprogram för Service Fabric i Azure
Azure Service Fabric är en plattform för distribuerade system för distribution och hantering av skalbara och tillförlitliga mikrotjänster och behållare. 

Du behöver inga göra några ändringar i din app för att köra en befintlig app i en Windows-behållare i ett Service Fabric-kluster. Den här snabbstarten beskriver hur du distribuerar en fördefinierad Docker-behållaravbildning i ett Service Fabric-program. När du har slutfört kursen har du en fungerande Windows Server 2016-baserad Nano Server- och IIS-behållare. Den här snabbstarten beskriver hur du distribuerar en Windows-behållare. Läs [den här snabbstarten](service-fabric-quickstart-containers-linux.md) om du vill distribuera en Linux-behållare.

![IIS-standardwebbsidan][iis-default]

I den här snabbstarten lär du dig att:
> [!div class="checklist"]
> * Paketera en Docker-avbildningsbehållare
> * Konfigurera kommunikation
> * Utveckla och distribuera ett Service Fabric-program
> * Distribuera behållarprogrammet till Azure

## <a name="prerequisites"></a>Nödvändiga komponenter
* En Azure-prenumeration (du kan skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* En utvecklingsdator som kör:
  * Visual Studio 2015 eller Visual Studio 2017.
  * [Service Fabric SDK och verktyg](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Paketera en Docker-avbildningsbehållare med Visual Studio
Service Fabric SDK och verktygen innehåller en tjänstmall som hjälper dig att distribuera en behållare till ett Service Fabric-kluster.

Starta Visual Studio som Administratör.  Välj **Arkiv** > **Nytt** > **Projekt**.

Välj **Service Fabric-programmet**, ge det namnet "MyFirstContainer" och klicka på **OK**.

Välj **Behållare** från mallarna **Hosted Containers and Applications** (Värdbaserade behållare och program).

I **Avbildningsnamn** anger du "microsoft/iis:nanoserver", [Windows Server Nano Server och IIS-avbildningsnamn](https://hub.docker.com/r/microsoft/iis/). 

Ge tjänsten namnet ”MyContainerService” och klicka på **OK**.

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Konfigurera kommunikation och portmappning mellan behållare och värd
Tjänsten behöver en slutpunkt för kommunikation.  I den här snabbstarten lyssnar behållartjänsten på port 80.  I Solution Explorer öppnar du *MyFirstContainer/ApplicationPackageRoot/MyContainerServicePkg/ServiceManifest.xml*.  Uppdatera den befintliga filen `Endpoint` i ServiceManifest.xml och lägg till protokoll, port och URI-schema: 

```xml
<Resources>
    <Endpoints>
        <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
   </Endpoints>
</Resources>
```
Genom att tillhandahålla `UriScheme` registreras automatiskt behållarslutpunkten med namngivningstjänsten för Service Fabric för identifiering. En fullständig ServiceManifest.xml-exempelfil finns i slutet av den här artikeln. 

Konfigurera behållarens portmappning från port till värd så att inkommande begäranden till tjänsten på port 80 mappas till port 80 i behållaren.  I Solution Explorer öppnar du *MyFirstContainer/ApplicationPackageRoot/ApplicationManifest.xml* och anger en `PortBinding`-princip i `ContainerHostPolicies`.  I den här snabbstarten är `ContainerPort` 80 och `EndpointRef` ”MyContainerServiceTypeEndpoint” (slutpunkten som definierats i tjänstmanifestet).    

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
Om du vill distribuera programmet till ett kluster i Azure, kan du ansluta till ett partykluster. Partykluster är kostnadsfria, tidsbegränsade Service Fabric-kluster i Azure som körs av Service Fabric-teamet där vem som helst kan distribuera program och lära sig mer om plattformen.  Klustret använder ett enda självsignerade certifikat för nod-till nod- samt klient-till-nod-säkerhet. Partykluster stöder behållare. Om du vill ställa in och använda ditt eget kluster, måste klustret köras på en SKU som stöder behållare (till exempel Windows Server 2016 Datacenter med behållare).

Logga in och [ansluta till ett Windows-kluster](http://aka.ms/tryservicefabric). Hämta PFX-certifikatet till datorn genom att klicka på **PFX**-länken. Klicka på länken **Hur ansluter man till ett säkert partkluster?** och kopiera lösenordet för certifikatet. Certifikatet, certifikatlösenordet och värdet **Anslutningsslutpunkt** används i följande steg.

![PFX och klientanslutningsslutpunkt](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> Det finns ett begränsat antal tillgängliga partkluster per timme. Om du får ett felmeddelande när du försöker registrera dig för ett partkluster, kan du vänta en stund och försöka igen, eller följa stegen i självstudien [Distribuera en .NET-app](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application) som hjälper dig att skapa ett Service Fabric-kluster i din Azure-prenumeration och distribuera programmet till den. Klustret som skapats med Visual Studio stöder behållare. När du har distribuerat och verifierat programmet i ditt kluster kan du hoppa vidare till [Fullständigt Service Fabric-exempelprogram och tjänstmanifest](#complete-example-service-fabric-application-and-service-manifests) i denna snabbstart. 
>

På en Windows-dator ska du installera PFX i certifikatarkivet *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

Kom ihåg tumavtrycket för följande steg.  

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Distribuera programmet till Azure med hjälp av Visual Studio
Nu när programmet är redo kan du distribuera det till ett kluster direkt från Visual Studio.

Högerklicka på **MyFirstContainer** i Solution Explorer och välj **Publicera**. Dialogrutan Publicera visas.

Kopiera **Anslutningsslutpunkten** för partyklustret till fältet **Anslutningsslutpunkt**. Till exempel `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Klicka på **Avancerade anslutningsparametrar** och kontrollera anslutningsinformationen för parametern.  Värdena *FindValue* och *ServerCertThumbprint* måste matcha tumavtrycket för certifikatet som installerades i föregående steg. 

![Dialogrutan Publicera](./media/service-fabric-quickstart-containers/publish-app.png)

Klicka på **Publicera**.

Varje program i klustret måste ha ett unikt namn.  Partkluster är en offentlig, delad miljö men det kan finnas en konflikt med ett befintligt program.  Om det finns en namnkonflikt byter du namn på Visual Studio-projektet och distribuerar igen.

Öppna en webbläsare och gå till den **Anslutningsslutpunkt** som anges på partyklustersidan. Alternativt kan du lägga till schemaidentifierare `http://` innan och lägga till porten `:80` efter i URL:en. Till exempel http://zwin7fh14scd.westus.cloudapp.azure.com:80. Du bör se IIS-standardwebbsidan: ![IIS-standardwebbsidan][iis-default]

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
