---
title: Som beskriver Azure Service Fabric-appar och tjänster | Microsoft Docs
description: Beskriver hur du använder manifest för att beskriva Service Fabric-program och tjänster.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/19/2018
ms.author: atsenthi
ms.openlocfilehash: 5e93bb3b206fbef6beb09b7aca6df0742a80ccf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621521"
---
# <a name="service-fabric-application-and-service-manifests"></a>Service Fabric-program och tjänstmanifest
Den här artikeln beskriver hur Service Fabric-program och tjänster är definierad och version med hjälp av filen ApplicationManifest.xml och ServiceManifest.xml.  Mer detaljerade exempel finns i [och en tjänst manifest exempel](service-fabric-manifest-examples.md).  XML-schemat för dessa manifestfiler dokumenteras i [ServiceFabricServiceModel.xsd schemat dokumentation](service-fabric-service-model-schema.md).

> [!WARNING]
> XML-manifest filschemat framtvingar rätt ordning av underordnade element.  Som en partiell lösning kan du öppna ”C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd” i Visual Studio när redigering eller ändra någon av Service Fabric-manifest. Detta gör att du kan kontrollera sorteringen i underordnade element och ger tillgång.

## <a name="describe-a-service-in-servicemanifestxml"></a>Beskriv en tjänst i ServiceManifest.xml
Tjänstmanifestet definierar deklarativt tjänsttyp och version. Den anger tjänstens metadata, till exempel typ av tjänst, health egenskaper, belastningsutjämning mått, binärfiler och konfigurationsfiler.  Placera ett annat sätt, beskriver de kod, konfiguration och data paket som utgör inget tjänstepaket för att stödja en eller flera typer av tjänster. Ett tjänstmanifest kan innehålla flera kod, konfiguration och data-paket, vilka kan versionshanteras oberoende av varandra. Här är ett tjänstmanifest för ASP.NET Core webbtjänsten för klientdelen av den [rösta exempelprogrammet](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (och här är några [mer detaljerade exempel](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
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

**Version** attribut är Ostrukturerade strängar och inte parsa av systemet. Version attribut är används för varje komponent för uppgradering till version.

**ServiceTypes** deklarerar vilka typer av tjänster som stöds av **CodePackages** i manifestet. När en tjänst instantieras mot en av dessa typer av tjänster, aktiveras alla kodpaket som deklarerats i manifestet genom att köra sina startpunkter. De resulterande processerna förväntas registrera stöds tjänsttyper vid körning. Tjänsttyper deklareras i manifestet nivå och inte kodnivå för paketet. Så när det finns flera kodpaket, aktiveras de alla varje gång systemet söker efter någon av de deklarerade tjänsttyper.

Den körbara filen som anges av **EntryPoint** är vanligtvis tjänstevärden tidskrävande. **SetupEntryPoint** är en privilegierad startpunkt som körs med samma autentiseringsuppgifter som Service Fabric (vanligtvis den *LocalSystem* konto) innan andra startpunkt.  Förekomsten av en separat konfigurationsstartpunkten innebär att du slipper att köra tjänstevärden med höga privilegier för längre tid. Den körbara filen som anges av **EntryPoint** körs efter **SetupEntryPoint** avslutas har. Om processen skulle avslutas eller kraschar, resulterande processen övervakas och startas om (igen från och med **SetupEntryPoint**).  

Vanliga scenarier där **SetupEntryPoint** är när du kör en körbar fil innan du startar tjänsten eller du utföra en åtgärd med förhöjd behörighet. Exempel:

* Konfigurera och initiering av miljövariabler som behöver körbar fil. Detta är inte begränsad till endast körbara filer skrivs via programmeringsmodeller för Service Fabric. Till exempel måste npm.exe vissa miljövariabler som konfigurerats för att distribuera ett node.js-program.
* Konfigurera åtkomstkontroll genom att installera säkerhetscertifikat.

Läs mer om hur du konfigurerar SetupEntryPoint [konfigurera principen för en tjänstens konfigurationsstartpunkt](service-fabric-application-runas-security.md)

**EnvironmentVariables** (inte anges i föregående exempel) innehåller en lista över miljövariabler som som har angetts för det här kodpaketet. Miljövariabler kan åsidosättas i den `ApplicationManifest.xml` kan ange olika värden för olika tjänstinstanser. 

**DataPackage** (inte anges i föregående exempel) deklarerar en mapp med namnet genom att den **namn** attributet som innehåller godtyckliga statiska data som ska konsumeras av process under körning.

**ConfigPackage** deklarerar en mapp med namnet genom att den **namn** attributet som innehåller en *Settings.xml* fil. Filen innehåller avsnitt av inställningar för användardefinierade, nyckel / värde-par som processen läser tillbaka vid körning. Vid en uppgradering om bara den **ConfigPackage** **version** har ändrats, och sedan processen inte startas. I stället meddelar en motringning den process som konfigurationsinställningarna har ändrats så att de kan läsas dynamiskt. Här är ett exempel *Settings.xml* fil:

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

Ett Service Fabric-tjänst **Endpoint** är ett exempel på ett Service Fabric-resurs. En Service Fabric-resurs kan vara deklarerats/ändras utan att ändra den kompilerade koden. Åtkomst till Service Fabric-resurser som anges i tjänstmanifestet kan styras via den **Tilldelningsmodulen** i manifestet. När en Slutpunktsresurs har definierats i tjänstmanifestet tilldelar Service Fabric portar från portintervallet för reserverade programmet när en port inte anges explicit. Läs mer om [att ange eller åsidosätter slutpunkten resurser](service-fabric-service-manifest-resources.md).


<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Beskriv ett program i ApplicationManifest.xml
Applikationsmanifestet beskriver deklarativt programtypen och versionen. Den anger service sammansättning metadata, till exempel stabila namn, partitionering schema, instans antal/replikeringsfaktor, säkerhetsisolering/princip, placeringsbegränsningar, konfiguration av åsidosättningar och konstituerande tjänsttyper. Belastningsutjämning domänerna som programmet ska placeras beskrivs också.

Därför ett programmanifest beskriver elementen på programnivå och refererar till en eller flera tjänstmanifest om du ska skapa en typ av program. Här är manifestet för den [rösta exempelprogrammet](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (och här är några [mer detaljerade exempel](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

Som tjänstmanifest, **Version** attribut är Ostrukturerade strängar och inte parsas av systemet. Version attribut är också används för varje komponent för uppgradering till version.

**Parametrar** definierar de parametrar som används i manifestet. Värdena för dessa parametrar kan anges när programmet instantieras och kan åsidosätta programmet eller tjänsten konfigurationsinställningar.  Om värdet inte ändras när programmet en används standardvärdet för parametern. Om du vill lära dig att upprätthålla olika program och tjänstparametrar för enskilda miljöer, se [hantera programparametrar för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** innehåller referenser till tjänstmanifest som utgör den här typen. Ett programmanifest kan innehålla flera service manifest import, var och en kan versionshanteras oberoende av varandra. Importerade tjänstmanifest avgöra vilka typer av tjänster som är giltiga i den här typen. Inom ServiceManifestImport, kan du åsidosätta konfigurationsvärden i Settings.xml och miljövariabler i ServiceManifest.xml-filer. **Principer** (ej angivet i föregående exempel) för slutpunkt-bindning, säkerhet och åtkomst och paketet delning kan ställas in på importerade tjänstmanifest.  Mer information finns i [ställer in säkerhetsprinciper för ditt program](service-fabric-application-runas-security.md).

**DefaultServices** deklarerar tjänstinstanser som skapas automatiskt när ett program instantieras mot den här typen. Standardtjänster är bara för att underlätta och fungerar som vanligt tjänster i varje avseende när de väl har skapats. De uppgraderas tillsammans med andra tjänster i programinstansen och kan tas bort även. Ett programmanifest kan innehålla flera standardtjänster.

**Certifikat** (inte anges i föregående exempel) deklarerar de certifikat som används för att [konfigurera HTTPS-slutpunkter](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) eller [kryptera hemligheter i applikationsmanifestet](service-fabric-application-secret-management.md).

**Principer** (inte anges i föregående exempel) beskriver Logginsamling, [standard kör som-](service-fabric-application-runas-security.md), [hälsotillstånd](service-fabric-health-introduction.md#health-policies), och [säkerhetsåtkomst](service-fabric-application-runas-security.md) principer för att ställa in på den programnivå.

**Huvudnamn** (ej angivet i föregående exempel) beskriver de säkerhetsobjekt (användare eller grupper) som krävs för att [kör tjänster och säker tjänstresurser](service-fabric-application-runas-security.md).  Huvudnamn refereras till i den **principer** avsnitt.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Nästa steg
- [Paketera ett program](service-fabric-package-apps.md) och blir redo att distribuera.
- [Distribuera och ta bort program](service-fabric-deploy-remove-applications.md).
- [Konfigurera parametrar och miljövariabler för olika programinstanser](service-fabric-manage-multiple-environment-app-configuration.md).
- [Ställer in säkerhetsprinciper för ditt program](service-fabric-application-runas-security.md).
- [Konfigurera HTTPS-slutpunkter](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Kryptera hemligheter i applikationsmanifestet](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



