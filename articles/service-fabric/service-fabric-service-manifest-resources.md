---
title: Ange tjänstslutpunkter för service fabric
description: Så här beskriver du slutpunktsresurser i ett tjänstmanifest, inklusive hur du ställer in HTTPS-slutpunkter
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cc4eedf5e5fee0bbfa0a763e9b9ec0dd25409afa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282164"
---
# <a name="specify-resources-in-a-service-manifest"></a>Ange resurser i ett tjänstmanifest
## <a name="overview"></a>Översikt
Servicemanifestet gör att resurser som används av tjänsten kan deklareras/ändras utan att den kompilerade koden ändras. Azure Service Fabric stöder konfiguration av slutpunktsresurser för tjänsten. Åtkomsten till de resurser som anges i tjänstmanifestet kan styras via SecurityGroup i programmanifestet. Deklarationen av resurser gör att dessa resurser kan ändras vid distributionen, vilket innebär att tjänsten inte behöver införa en ny konfigurationsmekanism. Schemadefinitionen för filen ServiceManifest.xml installeras med Service Fabric SDK och verktygen till *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Slutpunkter
När en slutpunktsresurs har definierats i tjänstmanifestet tilldelar Service Fabric portar från det reserverade programportområdet när en port inte uttryckligen anges. Titta till exempel på slutpunkten *ServiceEndpoint1* som anges i manifestkodavsnittet som angavs efter det här stycket. Dessutom kan tjänster också begära en viss port i en resurs. Tjänstrepliker som körs på olika klusternoder kan tilldelas olika portnummer, medan repliker av en tjänst som körs på samma nod delar porten. Tjänstrepliker kan sedan använda dessa portar efter behov för replikering och lyssning efter klientbegäranden.

> [!WARNING] 
> Av design bör statiska portar inte överlappa med programportintervallet som anges i ClusterManifest. Om du anger en statisk port tilldelar du den utanför programportintervallet, annars leder den till portkonflikter. Med release 6.5CU2 kommer vi att utfärda en **hälsovarning** när vi upptäcker en sådan konflikt men låter distributionen fortsätta synkroniseras med det levererade 6.5-beteendet. Vi kan dock förhindra programdistribution från nästa större versioner.
>
> Med release 7.0 kommer vi att utfärda en **hälsovarning** när vi upptäcker användning av programportintervall går utöver HostingConfig::ApplicationPortExhaustThresholdPercentage (standard 80%).
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Om det finns flera kodpaket i ett enda servicepaket måste kodpaketet också refereras i avsnittet **Slutpunkter.**  Om Till exempel **ServiceEndpoint2a** och **ServiceEndpoint2b** är slutpunkter från samma servicepaket som refererar till olika kodpaket, förtydligas kodpaketet som motsvarar varje slutpunkt på följande sätt:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Mer information om refererande av tillståndskänsliga tillförlitliga tjänster finns i Konfigurera [tillståndskänsliga tillförlitliga tjänster](service-fabric-reliable-services-configuration.md) om du vill läsa mer om refererande slutpunkter från filen config-paketinställningar (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exempel: ange en HTTP-slutpunkt för tjänsten
Följande tjänstmanifest definierar en TCP-slutpunktsresurs och &lt;&gt; två HTTP-slutpunktsresurser i elementet Resurser.

HTTP-slutpunkter ACL'd automatiskt av Service Fabric.

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

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exempel: ange en HTTPS-slutpunkt för tjänsten
HTTPS-protokollet tillhandahåller serverautentisering och används även för kryptering av klientserverkommunikation. Om du vill aktivera HTTPS på tjänsten Service Fabric anger du protokollet i avsnittet *Resurser -> slutpunkter -> slutpunkt* i tjänstmanifestet, som tidigare för slutpunkten *ServiceEndpoint3*.

> [!NOTE]
> Det går inte att ändra en tjänsts protokoll under programuppgraderingen. Om det ändras under uppgraderingen är det en brytande förändring.
> 

> [!WARNING] 
> När du använder HTTPS ska du inte använda samma port och certifikat för olika tjänstinstanser (oberoende av programmet) som distribueras till samma nod. Om du uppgraderar två olika tjänster med samma port i olika programinstanser kan det leda till ett uppgraderingsfel. Mer information finns i [Uppgradera flera program med HTTPS-slutpunkter ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Här är ett exempel ApplicationManifest som du måste ange för HTTPS. Tumavtrycket för ditt certifikat måste anges. EndpointRef är en referens till EndpointResource i ServiceManifest, som du anger HTTPS-protokollet för. Du kan lägga till mer än ett EndpointCertificate.  

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

För Linux-kluster är **MY-butiken** som standard mappen **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Åsidosätta slutpunkter i ServiceManifest.xml

I avsnittet ApplicationManifest lägger du till en ResourceOverrides, som kommer att vara ett syskon till configOverrides. I det här avsnittet kan du ange åsidosättning för avsnittet Slutpunkter i avsnittet resurser som anges i servicemanifestet. Övergripande slutpunkter stöds i körning 5.7.217/SDK 2.7.217 och högre.

För att åsidosätta EndPoint i ServiceManifest med ApplicationParameters ändra ApplicationManifest enligt följande:

Lägg till ett nytt avsnitt "ResourceOverrides" i avsnittet ServiceManifestImport.

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

I parametrarna lägg nedan:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

När du distribuerar programmet kan du skicka in dessa värden som ApplicationParameters.  Ett exempel:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Om värdena för ApplicationParameters är tomma går vi tillbaka till standardvärdet som anges i ServiceManifest för motsvarande EndPointName.

Ett exempel:

Om du i ServiceManifest du angav

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Och port1- och protokoll1-värdet för programparametrar är null eller tomt. Hamnen avgörs fortfarande av ServiceFabric. Och protokollet kommer tcp.

Anta att du anger ett fel värde. Precis som för Port du angav ett strängvärde "Foo" i stället för en int.  Kommandot New-ServiceFabricApplication misslyckas med ett fel: Åsidosättparametern med namnet "ServiceEndpoint1" attribut 'Port1' i avsnittet "ResourceOverrides" är ogiltig. Det angivna värdet är "Foo" och krävs är "int".
