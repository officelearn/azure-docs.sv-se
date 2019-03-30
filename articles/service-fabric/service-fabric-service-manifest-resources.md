---
title: Att ange Service Fabric-tjänstslutpunkter | Microsoft Docs
description: Så här att beskriva slutpunkten resurser i ett tjänstmanifest, inklusive hur du konfigurerar HTTPS-slutpunkter
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 8707a9cb90afe1bf72f3aef6377f8ada409a1c64
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667131"
---
# <a name="specify-resources-in-a-service-manifest"></a>Ange resurser i ett tjänstmanifest
## <a name="overview"></a>Översikt
Tjänstmanifestet kan resurser som används av tjänsten vara deklarerats/ändras utan att ändra den kompilerade koden. Azure Service Fabric stöder konfiguration av resurser för slutpunkten för tjänsten. Åtkomst till resurser som anges i tjänstmanifestet kan styras via Tilldelningsmodulen i manifestet. Deklaration av resurser kan dessa resurser för att ändras vid tidpunkten för distribution, vilket innebär att tjänsten inte behöver införa en ny konfiguration mekanism. Schemadefinitionen för filen servicemanifest.XML installeras med Service Fabric SDK och verktyg att *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Slutpunkter
När en slutpunktsresurs har definierats i tjänstmanifestet tilldelar Service Fabric portar från portintervallet för reserverade programmet när en port inte anges explicit. Titta exempelvis på slutpunkten *ServiceEndpoint1* anges i manifestet kodfragmentet efter detta stycke. Tjänster kan dessutom också begära en viss port på en resurs. Tjänsten replikerna som körs på olika noder kan du tilldela olika portnummer, medan replikeringar av en tjänst som körs på samma nod dela porten. Tjänsten repliker kan sedan använda dessa portar som behövs för replikering och lyssna efter klientbegäranden.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Om det finns flera kodpaket i ett och samma tjänstepaket så kodpaketet måste också refereras i den **slutpunkter** avsnittet.  Till exempel om **ServiceEndpoint2a** och **ServiceEndpoint2b** är slutpunkter från samma tjänstepaketet refererar till olika kodpaket kodpaketet som motsvarar varje slutpunkt tydliggjorde på följande sätt:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Referera till [konfigurera tillståndskänsliga Reliable Services](service-fabric-reliable-services-configuration.md) att läsa mer om refererar till slutpunkter paketet delaktig filen (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exempel: Ange en HTTP-slutpunkt för din tjänst
Följande tjänstmanifestet definierar en TCP-slutpunktsresurs och två HTTP-slutpunkt-resurser i den &lt;resurser&gt; element.

HTTP-slutpunkter som är automatiskt ACL skulle av Service Fabric.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exempel: du anger en HTTPS-slutpunkt för din tjänst
HTTPS-protokollet ger server-autentisering och används också för att kryptera kommunikation mellan klient-server. Om du vill aktivera HTTPS på din Service Fabric-tjänst, ange protokollet i den *resurser -> slutpunkter -> Endpoint* avsnittet i tjänstmanifestet som visades tidigare för slutpunkten *ServiceEndpoint3*.

> [!NOTE]
> Protokollet för en tjänst kan inte ändras under uppgradering av programmet. Om det ändras under uppgraderingen, är en viktig ändring.
> 

> [!WARNING] 
> När du använder HTTPS, Använd inte samma port och certifikat för olika tjänstinstanser (oberoende av programmet) som distribuerats till samma nod. Uppgradera två olika tjänster som använder samma port i olika programinstanser resulterar i en uppgraderingen skulle misslyckas. Mer information finns i [uppgradera flera program med HTTPS-slutpunkter ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Här är ett exempel ApplicationManifest som du måste ange för HTTPS. Tumavtrycket för certifikatet måste anges. EndpointRef är en referens till EndpointResource i ServiceManifest, som du anger HTTPS-protokollet. Du kan lägga till fler än en EndpointCertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

För Linux-kluster i **MY** lagra standard mappen **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Åsidosätta slutpunkter i ServiceManifest.xml

Lägg till ett avsnitt för ResourceOverrides, som är på samma nivå till ConfigOverrides avsnitt i ApplicationManifest. I det här avsnittet kan du ange åsidosättningen för slutpunkter i resursavsnittet som anges i tjänstmanifestet. Åsidosätta slutpunkter stöds i runtime 5.7.217/SDK 2.7.217 och högre.

För att åsidosätta slutpunkten i ServiceManifest med ApplicationParameters ändring i ApplicationManifest som följande:

Lägg till ett nytt avsnitt ”ResourceOverrides” i avsnittet ServiceManifestImport.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

Lägg till nedan i parametrarna:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

När du distribuerar programmet kan du skicka in dessa värden som ApplicationParameters.  Exempel:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Obs! Om värdena tillhandahåller ApplicationParameters är tom, återgå vi till standardvärdet som angetts i ServiceManifest för motsvarande EndPointName.

Exempel:

Om de finns i ServiceManifest som du angav

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Och Port1 och Protocol1 värdet för programparametrar är null eller tomt. Porten är fortfarande fastställs av Service fabric. Och protokollet kommer tcp.

Anta att du anger ett felaktigt värde. T.ex. för Port du har angett ett strängvärde ”Foo” i stället för int.  Ny ServiceFabricApplication kommandot misslyckas med felmeddelandet: Åsidosättning-parametern med namnet 'ServiceEndpoint1' attribut Port1 om du i avsnittet ”ResourceOverrides' är ogiltig. Det angivna värdet är ”Foo” och krävs är 'int'.
