---
title: Ange Service Fabric tjänst slut punkter
description: Så här beskriver du slut punkts resurser i ett tjänst manifest, inklusive hur du konfigurerar HTTPS-slutpunkter
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cc4eedf5e5fee0bbfa0a763e9b9ec0dd25409afa
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382839"
---
# <a name="specify-resources-in-a-service-manifest"></a>Ange resurser i ett tjänst manifest
## <a name="overview"></a>Översikt
Tjänst manifestet gör det möjligt för resurser som används av tjänsten att deklareras/ändras utan att den kompilerade koden ändras. Azure Service Fabric stöder konfiguration av slut punkts resurser för tjänsten. Åtkomst till de resurser som anges i tjänst manifestet kan styras via SecurityGroup i applikations manifestet. Resurs deklarationen gör att dessa resurser kan ändras vid distributions tillfället, vilket innebär att tjänsten inte behöver införa en ny konfigurations funktion. Schema definitionen för filen ServiceManifest. XML installeras med Service Fabric SDK och verktyg för *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Slutpunkter
När en slut punkts resurs definieras i tjänst manifestet tilldelar Service Fabric portar från det reserverade programmets port intervall när en port inte anges explicit. Titta till exempel på slut punkts *ServiceEndpoint1* som anges i manifestet som anges efter detta stycke. Dessutom kan tjänster även begära en speciell port i en resurs. Tjänst repliker som körs på olika klusternoder kan tilldelas olika port nummer, medan repliker av en tjänst som körs på samma nod delar porten. Tjänste replikerna kan sedan använda dessa portar vid behov för replikering och lyssna efter klient begär Anden.

> [!WARNING] 
> Genom att definiera statiska portar ska inte överlappa med det applikations port intervall som anges i ClusterManifest. Om du anger en statisk port tilldelar du den utanför program port intervallet, annars leder det till port konflikter. Med version 6.5 CU2 kommer vi att utfärda en **hälso varning** när vi identifierar en sådan konflikt men låter distributionen fortsätta att synkroniseras med den levererade 6,5 beteendet. Vi kan dock förhindra program distributionen från nästa större versioner.
>
> Med version 7,0 kommer vi att utfärda en **hälso varning** när vi identifierar användningen av användnings port intervallet går utöver HostingConfig:: ApplicationPortExhaustThresholdPercentage (standard 80%).
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

Om det finns flera kod paket i ett enda tjänst paket måste kod paketet också refereras i avsnittet **slut punkter** .  Om till exempel **ServiceEndpoint2a** och **ServiceEndpoint2b** är slut punkter från samma tjänst paket som refererar till olika kod paket, så klargörs det kod paket som motsvarar varje slut punkt enligt följande:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Läs mer om att [Konfigurera tillstånds känsliga Reliable Services](service-fabric-reliable-services-configuration.md) för att läsa mer om att referera till slut punkter från konfigurations filen för konfigurations paket (Settings. xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exempel: Ange en HTTP-slutpunkt för din tjänst
Följande tjänst manifest definierar en resurs för TCP-slutpunkten och två HTTP-slutpunkts resurser i &lt;resurser&gt;-elementet.

HTTP-slutpunkter är automatiskt ACL med Service Fabric.

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

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exempel: Ange en HTTPS-slutpunkt för din tjänst
HTTPS-protokollet tillhandahåller serverautentisering och används även för kryptering av klient-server-kommunikation. Om du vill aktivera HTTPS på din Service Fabric-tjänst anger du protokollet i avsnittet *resurser-> slut punkter-> slut* punkt i tjänst manifestet, som du ser tidigare för slut punkten *ServiceEndpoint3*.

> [!NOTE]
> Det går inte att ändra tjänstens protokoll under program uppgraderingen. Om den ändras under uppgraderingen, är det en större ändring.
> 

> [!WARNING] 
> När du använder HTTPS ska du inte använda samma port och certifikat för olika tjänst instanser (oberoende av programmet) som distribueras till samma nod. Uppgradering av två olika tjänster med samma port i olika program instanser leder till ett uppgraderings haveri. Mer information finns i [uppgradera flera program med https-slutpunkter ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Här är ett exempel på en ApplicationManifest som du måste ange för HTTPS. Tumavtryck för ditt certifikat måste anges. EndpointRef är en referens till EndpointResource i ServiceManifest som du ställer in HTTPS-protokollet för. Du kan lägga till fler än en EndpointCertificate.  

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

För Linux-kluster är **mitt** Arkiv standardvärdet för mappen **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Åsidosätt slut punkter i ServiceManifest. XML

I ApplicationManifest lägger du till en ResourceOverrides-sektion, som kommer att vara en syskon till ConfigOverrides-avsnittet. I det här avsnittet kan du ange åsidosättningen för avsnittet slut punkter i avsnittet resurser som anges i tjänst manifestet. Det finns stöd för att åsidosätta slut punkter i runtime 5.7.217/SDK 2.7.217 och högre.

För att åsidosätta slut punkten i ServiceManifest med ApplicationParameters ändrar du ApplicationManifest enligt följande:

I avsnittet service manifest import lägger du till ett nytt avsnitt "ResourceOverrides".

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

I parametrarna lägger du till nedan:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

När du distribuerar programmet kan du överföra dessa värden som ApplicationParameters.  Exempel:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Obs! Om värdena för ApplicationParameters är tomma går vi tillbaka till det standardvärde som anges i ServiceManifest för motsvarande EndPointName.

Exempel:

Om du har angett i ServiceManifest

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Och värdet PORT1 och Protocol1 för program parametrarna är null eller tomt. Porten bestäms fortfarande av ServiceFabric. Och protokollet kommer att TCP.

Anta att du anger ett felaktigt värde. Precis som för porten angav du ett sträng värde "foo" i stället för en int.  New-ServiceFabricApplication-kommandot Miss fungerar med ett fel: parametern override med namnet ServiceEndpoint1 PORT1 i avsnittet ResourceOverrides är ogiltig. Det angivna värdet är ' foo ' och måste vara ' int '.
