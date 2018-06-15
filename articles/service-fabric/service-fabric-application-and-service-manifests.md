---
title: Beskrivning av Azure Service Fabric-appar och tjänster | Microsoft Docs
description: Beskriver hur du använder manifest för att beskriva Service Fabric-program och tjänster.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: ryanwi
ms.openlocfilehash: b79206b9d456226d14984e8a1c1002c07c4f626a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208479"
---
# <a name="service-fabric-application-and-service-manifests"></a>Service Fabric-programmet och service manifest
Den här artikeln beskrivs hur Service Fabric-program och tjänster som är definierat och en version med ApplicationManifest.xml och ServiceManifest.xml-filer.  XML-schemat för dessa manifestfiler dokumenteras i [ServiceFabricServiceModel.xsd schemadokumentationen](service-fabric-service-model-schema.md).

## <a name="describe-a-service-in-servicemanifestxml"></a>Beskriv en tjänst i ServiceManifest.xml
Service manifest definieras deklarativt service-typen och versionen. Det anger tjänstmetadata som typ av tjänst, hälsa egenskaper, belastningsutjämning mått, binärfiler och konfigurationsfiler.  Placera ett annat sätt beskriver koden, konfiguration och data paket som ska utgöra ett servicepaket för att stödja en eller flera typer av tjänster. Ett service manifest kan innehålla flera koden, konfiguration och data paket, som kan vara en ny version oberoende av varandra. Här är ett service manifest för ASP.NET Core Frontend-webbtjänsten för den [röstning exempelprogrammet](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

**Version** attribut är Ostrukturerade strängar och inte tolkas av systemet. Version-attribut kan användas för varje komponent för uppgradering till version.

**ServiceTypes** deklarerar vilka typer av tjänster som stöds av **CodePackages** i manifestet. När en tjänst instansieras mot en av dessa tjänsttyper, aktiveras alla kod paket som deklarerats i manifestet genom att köra deras startpunkter. De resulterande processerna förväntas registrera stöds tjänsttyper vid körning. Tjänsttyper deklarerats i manifestet nivå och inte koden package-nivå. Så när det finns flera paket i koden, aktiveras de alla varje gång systemet söker efter en deklarerad tjänsttyp.

Den körbara filen som anges av **EntryPoint** är vanligtvis tjänstvärden tidskrävande. **SetupEntryPoint** är en privilegierade startpunkt som körs med samma autentiseringsuppgifter som Service Fabric (vanligtvis den *LocalSystem* konto) innan andra startpunkt.  Förekomsten av en separat installationsprogrammet startpunkt slipper du köra tjänstvärden med höga privilegier för längre tid. Den körbara filen som anges av **EntryPoint** körs efter **SetupEntryPoint** avslutas korrekt. Om processen någonsin avslutar eller kraschar, resulterande processen övervakas och startas om (med början igen **SetupEntryPoint**).  

Vanliga scenarier där **SetupEntryPoint** är när du kör en körbar fil innan du startar tjänsten eller du utföra en åtgärd med förhöjda privilegier. Exempel:

* Ställa in och initierar miljövariabler som behöver körbar fil. Detta är inte begränsad till endast körbara filer skrivs via Service Fabric programmeringsmodeller. Exempelvis måste npm.exe miljövariabler som konfigurerats för att distribuera ett node.js-program.
* Konfigurera åtkomstkontroll genom att installera security-certifikat.

Mer information om hur du konfigurerar den **SetupEntryPoint**, se [konfigurera principen för en startpunkt för installationen av tjänsten](service-fabric-application-runas-security.md)

**EnvironmentVariables** (inte anges i föregående exempel) innehåller en lista över miljövariabler som ställs in för den här koden paketet. Miljövariabler kan åsidosättas i den `ApplicationManifest.xml` att ange olika värden för olika tjänstinstanser. 

**DataPackage** (inte anges i föregående exempel) deklarerar en mapp med namnet med den **namn** attributet som innehåller godtyckliga statiska data som ska konsumeras av processen vid körning.

**ConfigPackage** deklarerar en mapp med namnet med den **namn** attributet som innehåller en *Settings.xml* fil. Filen innehåller avsnitt av inställningar för användardefinierade, nyckel / värde-par som processen läser tillbaka vid körning. Under en uppgradering, om det bara den **ConfigPackage** **version** har ändrats sedan processen inte startas om. I stället meddelar ett återanrop processen som konfigurationsinställningarna har ändrats så att de kan läsas dynamiskt. Här är ett exempel *Settings.xml* fil:

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

**Resurser**, till exempel slutpunkter, som används av tjänsten ska deklareras eller har ändrats utan att ändra den kompilerade koden.  Åtkomst till resurser som har angetts i tjänstmanifestet kan styras via den **säkerhetsgruppen** i programmanifestet.  När en **Endpoint** resurs som definieras i service manifest, Service Fabric tilldelar portar från portintervall reserverade programmet när en port inte är anges explicit.  Läs mer om [att ange eller åsidosätta endpoint resurser](service-fabric-service-manifest-resources.md).


<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Beskriv ett program i ApplicationManifest.xml
Applikationsmanifestet beskriver deklarativt programtypen och versionen. Den anger service sammansättning metadata, till exempel stabila namn, instans antal/replikering faktor, säkerhetsisolering/princip, placeringen, configuration åsidosättningar, och partitionsschemat ingående tjänsttyper. Belastningsutjämning domänerna som programmet ska placeras beskrivs också.

Därför ett programmanifest beskrivs element på programnivå och refererar till en eller flera service manifest för att skapa en typ av program. Här är programmanifestet för den [röstning exempelprogrammet](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Som service manifest **Version** attribut är Ostrukturerade strängar och inte tolkas av systemet. Version-attribut kan också användas för varje komponent för uppgradering till version.

**Parametrarna** definierar de parametrar som används i programmanifestet. Värdena för dessa parametrar kan anges när programmet instatiated och kan åsidosätta programmet eller tjänsten konfigurationsinställningar.  Om värdet inte ändras under programmet instansiering används standardvärdet för parametern. Information om hur du underhåller olika program och tjänstparametrar för enskilda miljöer finns [hantera programparametrar för miljöer med flera](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** innehåller referenser till service manifest som ska utgöra den här tillämpningstypen. Ett programmanifest kan innehålla flera service manifest import, var och en kan vara en ny version oberoende av varandra. Importerade service manifest avgör vilka typer av tjänster är giltiga i den här tillämpningstypen. Inom ServiceManifestImport, kan du åsidosätta konfigurationsvärden i Settings.xml och miljövariabler i ServiceManifest.xml filer. **Principer för** (inte anges i föregående exempel) för slutpunkt-bindningen, säkerhet och åtkomst och paketet delar kan anges för importerade service manifest.  Mer information finns i [konfigurera säkerhetsprinciper för ditt program](service-fabric-application-runas-security.md).

**DefaultServices** deklarerar tjänstinstanser som skapas automatiskt när ett program instansieras mot den här tillämpningstypen. Standardtjänsterna bara i informationssyfte och fungerar som vanligt tjänster i varje avseende när de har skapats. De uppgraderas tillsammans med andra tjänster i programinstansen och kan också tas bort. Ett programmanifest kan innehålla flera standardtjänster.

**Certifikat** (inte anges i föregående exempel) deklarerar de certifikat som används för att [konfigurera HTTPS-slutpunkter](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) eller [kryptera hemligheter i programmanifestet](service-fabric-application-secret-management.md).

**Principer** (inte anges i föregående exempel) beskriver Logginsamling, [standard kör som-](service-fabric-application-runas-security.md), [hälsa](service-fabric-health-introduction.md#health-policies), och [säkerhetsåtkomst](service-fabric-application-runas-security.md) principer för att ställa in på den programnivå.

**Säkerhetsobjekt** (inte anges i föregående exempel) beskriver de säkerhetsobjekt (användare eller grupper) som krävs för att [kör tjänster och resurser på säkra tjänsten](service-fabric-application-runas-security.md).  Säkerhetsobjekt som refereras till i den **principer** avsnitt.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Nästa steg
- [Paketerar ett program](service-fabric-package-apps.md) och gör den redo att distribuera.
- [Distribuera och ta bort program](service-fabric-deploy-remove-applications.md).
- [Konfigurera parametrar och miljövariabler för olika programinstanser](service-fabric-manage-multiple-environment-app-configuration.md).
- [Konfigurera säkerhetsprinciper för ditt program](service-fabric-application-runas-security.md).
- [Konfigurera HTTPS-slutpunkter](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Kryptera hemligheter i programmanifestet](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



