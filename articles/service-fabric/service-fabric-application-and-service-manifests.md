---
title: Beskriva appar och tjänster i Azure Service Fabric
description: Beskriver hur manifest används för att beskriva Program och tjänster i Service Fabric.
ms.topic: conceptual
ms.date: 8/12/2019
ms.openlocfilehash: 6014ef6a9b6ec810aafd5e5be96223b8ed92d576
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349968"
---
# <a name="service-fabric-application-and-service-manifests"></a>Program- och tjänstmanifest för Service Fabric
I den här artikeln beskrivs hur tjänst fabric-program och tjänster definieras och versions med filerna ApplicationManifest.xml och ServiceManifest.xml.  Mer detaljerade exempel finns i [exempel på program- och tjänstmanifest](service-fabric-manifest-examples.md).  XML-schemat för dessa manifestfiler dokumenteras i [Schemadokumentationen ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

> [!WARNING]
> Manifestet XML-filschema framtvingar korrekt ordning av underordnade element.  Som en partiell lösning öppnar du "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" i Visual Studio medan du redigerar eller ändrar något av servicetygmanifesten. Detta gör att du kan kontrollera beställningen av underordnade element och ger intelli-känsla.

## <a name="describe-a-service-in-servicemanifestxml"></a>Beskriv en tjänst i ServiceManifest.xml
Tjänstmanifestet definierar deklarativt tjänsttypen och versionen. Den anger tjänstmetadata som tjänsttyp, hälsoegenskaper, belastningsutjämningsmått, tjänstbinärar och konfigurationsfiler.  Med andra ord beskriver den kod-, konfigurations- och datapaket som utgör ett servicepaket för att stödja en eller flera tjänsttyper. Ett tjänstmanifest kan innehålla flera kod-, konfigurations- och datapaket som kan versionsas oberoende av dem. Här är ett tjänstmanifest för ASP.NET Core-webbfrontdelstjänsten [för röstningsexempelprogrammet](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (och här är några [mer detaljerade exempel):](service-fabric-manifest-examples.md)

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

**Versionsattribut** är ostrukturerade strängar och tolkas inte av systemet. Versionsattribut används för att version av varje komponent för uppgraderingar.

**ServiceTypes** deklarerar vilka tjänsttyper som stöds av **CodePackages** i det här manifestet. När en tjänst instansieras mot en av dessa tjänsttyper aktiveras alla kodpaket som deklarerats i det här manifestet genom att köra sina startpunkter. De resulterande processerna förväntas registrera de tjänsttyper som stöds vid körning. Tjänsttyper deklareras på manifestnivå och inte kodpaketnivån. Så när det finns flera kodpaket aktiveras de alla när systemet letar efter någon av de deklarerade tjänsttyperna.

Den körbara filen som anges av **EntryPoint** är vanligtvis den tjänstvärd som körs. **SetupEntryPoint** är en privilegierad startpunkt som körs med samma autentiseringsuppgifter som Service Fabric (vanligtvis *LocalSystem-kontot)* före någon annan startpunkt.  Förekomsten av en separat inställningsanspunkt undviker att behöva köra tjänstvärden med hög behörighet under längre tidsperioder. Den körbara filen som anges av **EntryPoint** körs när **SetupEntryPoint** har avslutats. Om processen någonsin avslutas eller kraschar övervakas och startas om den resulterande processen (från och med **setupEntryPoint**).  

Typiska scenarier för att använda **SetupEntryPoint** är när du kör en körbar innan tjänsten startar eller om du utför en åtgärd med förhöjda privilegier. Ett exempel:

* Ställa in och initiera miljövariabler som tjänsten körsbara behov. Detta är inte begränsat till endast körbara filer som skrivs via Programmeringsmodellerna för Service Fabric. Npm.exe behöver till exempel vissa miljövariabler som konfigurerats för att distribuera ett node.js-program.
* Konfigurera åtkomstkontroll genom att installera säkerhetscertifikat.

Mer information om hur du konfigurerar SetupEntryPoint finns i [Konfigurera principen för en startpunkt för tjänstinställningar](service-fabric-application-runas-security.md)

**EnvironmentVariables** (anges inte i föregående exempel) innehåller en lista över miljövariabler som har angetts för det här kodpaketet. Miljövariabler kan åsidosättas `ApplicationManifest.xml` i att tillhandahålla olika värden för olika tjänstinstanser. 

**DataPackage** (som inte anges i föregående exempel) deklarerar en mapp, namngiven av **attributet Name,** som innehåller godtyckliga statiska data som ska förbrukas av processen vid körning.

**ConfigPackage** deklarerar en mapp, namngiven efter attributet **Name,** som innehåller filen *Settings.xml.* Inställningsfilen innehåller avsnitt med användardefinierade inställningar för nyckelvärdespar som processen läser upp vid körning. Under en uppgradering, om bara **ConfigPackage-versionen** **version** har ändrats, startas inte den pågående processen om. I stället meddelar en motringning processen att konfigurationsinställningarna har ändrats så att de kan laddas om dynamiskt. Här är ett exempel *på Filen Settings.xml:*

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

En service **fabric-tjänstslutpunkt** är ett exempel på en Service Fabric-resurs. En Service Fabric-resurs kan deklareras/ändras utan att den kompilerade koden ändras. Åtkomst till serviceinfrastrukturresurserna som anges i tjänstmanifestet kan styras via **SecurityGroup** i programmanifestet. När en slutpunktsresurs har definierats i tjänstmanifestet tilldelar Service Fabric portar från det reserverade programportområdet när en port inte uttryckligen anges. Läs mer om [hur du anger eller åsidosätter slutpunktsresurser](service-fabric-service-manifest-resources.md).

 
> [!WARNING]
> Av design bör statiska portar inte överlappa med programportintervallet som anges i ClusterManifest. Om du anger en statisk port tilldelar du den utanför programportintervallet, annars leder den till portkonflikter. Med release 6.5CU2 kommer vi att utfärda en **hälsovarning** när vi upptäcker en sådan konflikt men låter distributionen fortsätta synkroniseras med det levererade 6.5-beteendet. Vi kan dock förhindra programdistribution från nästa större versioner.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Beskriv ett program i ApplicationManifest.xml
Programmanifestet beskriver deklarativt programtypen och versionen. Den anger metadata för tjänstsammansättning, till exempel stabila namn, partitioneringsschema, instansantals-/replikeringsfaktor, säkerhets-/isoleringsprincip, placeringsbegränsningar, konfigurationsöverträdningar och komponenttjänsttyper. De belastningsutjämningsdomäner som programmet placeras i beskrivs också.

Ett programmanifest beskriver alltså element på programnivå och refererar till ett eller flera tjänstmanifest för att skriva en programtyp. Här är ansökan manifestet för [röstning exempel ansökan](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (och här är några mer [detaljerade exempel):](service-fabric-manifest-examples.md)

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
         <PlacementConstraints>(NodeType==NodeType0)</PlacementConstraints
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Precis som tjänstmanifest är **versionsattribut** ostrukturerade strängar och tolkas inte av systemet. Versionsattribut används också för att version av varje komponent för uppgraderingar.

**Parametrar** definierar de parametrar som används i hela programmanifestet. Värdena för dessa parametrar kan anges när programmet instansieras och kan åsidosätta inställningar för program- eller tjänstkonfiguration.  Standardparametervärdet används om värdet inte ändras under programmets instansiering. Mer information om hur du underhåller olika program- och tjänstparametrar för enskilda miljöer finns i [Hantera programparametrar för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** innehåller referenser till tjänstmanifest som utgör den här programtypen. Ett programmanifest kan innehålla flera tjänstmanifestimporter, var och en kan versionsas oberoende av varandra. Importerade tjänstmanifest avgör vilka tjänsttyper som är giltiga inom den här programtypen. I ServiceManifestImport åsidosätter du konfigurationsvärden i Settings.xml och miljövariabler i ServiceManifest.xml-filer. **Principer** (som inte anges i föregående exempel) för slutpunktsbindning, säkerhet och åtkomst och paketdelning kan anges på importerade tjänstmanifest.  Mer information finns i [Konfigurera säkerhetsprinciper för ditt program](service-fabric-application-runas-security.md).

**DefaultServices** deklarerar tjänstinstanser som skapas automatiskt när ett program instansieras mot den här programtypen. Standardtjänster är bara en bekvämlighet och beter sig som vanliga tjänster i alla avseenden efter att de har skapats. De uppgraderas tillsammans med andra tjänster i programinstansen och kan också tas bort. Ett programmanifest kan innehålla flera standardtjänster.

**Certifikat** (som inte anges i föregående exempel) deklarerar de certifikat som används för att [ställa in HTTPS-slutpunkter](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) eller [kryptera hemligheter i programmanifestet](service-fabric-application-secret-management.md).

**Placeringsbegränsningar** är de satser som definierar var tjänster ska köras. Dessa satser är kopplade till enskilda tjänster som du väljer för en eller flera nodegenskaper. Mer information finns i [Placeringsbegränsningar och nodgenskapssyntax](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#placement-constraints-and-node-property-syntax)

**Principer** (som inte anges i föregående exempel) beskriver loggsamlingen, [standardkörnings-,](service-fabric-application-runas-security.md) [hälso-](service-fabric-health-introduction.md#health-policies)och [säkerhetsåtkomstprinciper](service-fabric-application-runas-security.md) som ska anges på programnivå, inklusive om tjänsterna har åtkomst till service fabric-körningen.

> [!NOTE] 
> Som standard har Service Fabric-program åtkomst till Service Fabric-körningen, i form av en slutpunkt som accepterar programspecifika begäranden, och miljövariabler som pekar på filsökvägar på värden som innehåller Fabric och programspecifika filer . Överväg att inaktivera den här åtkomsten när programmet är värd för ej betrodd kod (dvs. kod vars härkomst är okänd eller som programägaren vet inte är säker att köra). Mer information finns [i metodtips för säkerhet i Service Fabric](service-fabric-best-practices-security.md#platform-isolation). 
>

**Huvudnamn** (som inte anges i föregående exempel) beskriver de säkerhetsobjekt (användare eller grupper) som krävs för att [köra tjänster och säkra tjänstresurser](service-fabric-application-runas-security.md).  Huvudmän refereras i avsnitten **Principer.**





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Nästa steg
- [Paketera ett program](service-fabric-package-apps.md) och gör det redo att distribuera.
- [Distribuera och ta bort program](service-fabric-deploy-remove-applications.md).
- [Konfigurera parametrar och miljövariabler för olika programinstanser](service-fabric-manage-multiple-environment-app-configuration.md).
- [Konfigurera säkerhetsprinciper för ditt program](service-fabric-application-runas-security.md).
- [Inställningar för HTTPS-slutpunkter](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Kryptera hemligheter i programmanifestet](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



