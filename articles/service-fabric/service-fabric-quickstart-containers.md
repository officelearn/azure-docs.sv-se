---
title: Skapa en Windows-containerapp för Service Fabric i Azure | Microsoft Docs
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
ms.date: 04/30/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 95877f8b81641dd70434fc167003cfcce07d9e84
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110818"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Snabbstart: Distribuera Windows-containers till Service Fabric

Azure Service Fabric är en plattform för distribuerade system för distribution och hantering av skalbara och tillförlitliga mikrotjänster och behållare.

Du behöver inga göra några ändringar i din app för att köra en befintlig app i en Windows-behållare i ett Service Fabric-kluster. Den här snabbstarten beskriver hur du distribuerar en fördefinierad Docker-behållaravbildning i ett Service Fabric-program. När du har slutfört kursen har du en fungerande Windows Server 2016-baserad Nano Server- och IIS-behållare. Den här snabbstarten beskriver hur du distribuerar en Windows-behållare. Läs [den här snabbstarten](service-fabric-quickstart-containers-linux.md) om du vill distribuera en Linux-behållare.

![IIS-standardwebbsidan][iis-default]

I den här snabbstarten lär du dig att:

* Paketera en Docker-avbildningsbehållare
* Konfigurera kommunikation
* Utveckla och distribuera ett Service Fabric-program
* Distribuera behållarprogrammet till Azure

## <a name="prerequisites"></a>Förutsättningar

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

Konfigurera behållarens portmappning från port till värd så att inkommande begäranden till tjänsten på port 80 mappas till port 80 i behållaren.  Ge **Behållareport** värdet 80 och **Värdport** värdet 80.  

Ge tjänsten namnet ”MyContainerService” och klicka på **OK**.

<<<<<<< Uppdateras uppströms ![Dialogrutan Ny tjänst][new-service]
=======
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
>>>>>>> Staplade ändringar

## <a name="specify-the-os-build-for-your-container-image"></a>Ange operativsystemets version för behållaravbildningen
Behållare som skapats med en viss Windows Server-version kan kanske inte köras på en värd som kör en annan Windows Server-version. Behållare som t.ex. har skapats med version 1709 av Windows Server kan inte köras på värdar som kör Windows Server 2016. Mer information finns i [Kompatibilitet mellan operativsystem för Windows Server-behållare och värdoperativsystem](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). 

Med version 6.1 och senare av Service Fabric Runtime kan du ange flera operativsystemsavbildningar per behållare och tagga var och en med den version av operativsystemet som de ska distribueras till. Detta hjälper dig att säkerställa att programmet kan köras på värdar som kör olika versioner av Windows operativsystem. Mer information finns i [Ange specifika behållaravbildningar för operativsystemet](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

Microsoft publicerar olika avbildningar för de olika versioner av IIS som har skapats på olika versioner av Windows Server. Om du vill säkerställa att Service Fabric distribuerar en behållare som är kompatibel med den version av Windows Server som körs på de klusternoder där dina program distribueras, så lägger du till följande rader i filen *ApplicationManifest.xml*. Versionen för Windows Server 2016 är 14393, och versionen för Windows Server version 1709 är 16299. 

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="microsoft/iis:nanoserverDefault" /> 
          <Image Name= "microsoft/iis:nanoserver" Os="14393" /> 
          <Image Name="microsoft/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

Tjänstmanifestet fortsätter att ange endast en avbildning för nanoservern, `microsoft/iis:nanoserver`. 

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
<<<<<<< Updated upstream
``` 
=======
```

Remember the thumbprint for the following step.
>>>>>>> Stashed changes

## Deploy the application to Azure using Visual Studio

Now that the application is ready, you can deploy it to a cluster directly from Visual Studio.

Right-click **MyFirstContainer** in the Solution Explorer and choose **Publish**. The Publish dialog appears.

<<<<<<< Updated upstream
Copy the **Connection Endpoint** from the Party cluster page into the **Connection Endpoint** field. For example, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. 
=======
Copy the **Connection Endpoint** from the Party cluster page into the **Connection Endpoint** field. For example, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Click **Advanced Connection Parameters** and verify the connection parameter information.  *FindValue* and *ServerCertThumbprint* values must match the thumbprint of the certificate installed in the previous step.

![Publish Dialog](./media/service-fabric-quickstart-containers/publish-app.png)
>>>>>>> Stashed changes

Click **Publish**.

Each application in the cluster must have a unique name.  Party clusters are a public, shared environment however and there may be a conflict with an existing application.  If there is a name conflict, rename the Visual Studio project and deploy again.

Open a browser and navigate to the **Connection endpoint** specified in the Party cluster page. You can optionally prepend the scheme identifier, `http://`, and append the port, `:80`, to the URL. For example, http://zwin7fh14scd.westus.cloudapp.azure.com:80. You should see the IIS default web page:
![IIS default web page][iis-default]

<<<<<<< Updated upstream
=======
## Complete example Service Fabric application and service manifests

Here are the complete service and application manifests used in this quickstart.

### ServiceManifest.xml

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

>>>>>>> Staplade ändringar
## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Paketera en Docker-avbildningsbehållare
* Konfigurera kommunikation
* Utveckla och distribuera ett Service Fabric-program
* Distribuera behållarprogrammet till Azure

Om du vill veta mer om att arbeta med Windows-behållare i Service Fabric, kan du fortsätta till självstudien för appar i Windows-behållaren.

> [!div class="nextstepaction"]
> [Skapa en app för Windows-behållare](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
