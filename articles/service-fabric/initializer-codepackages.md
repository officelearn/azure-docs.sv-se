---
title: Initializer CodePackages i servicetyg
description: Beskriver Initializer CodePackages i Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430635"
---
# <a name="initializer-codepackages"></a>Initializer CodePackages

Från och med version 7.1 stöder Service Fabric **Initializer CodePackages** för [behållare][containers-introduction-link] och [körbara gästprogram.][guest-executables-introduction-link] Initializer CodePackages ger möjlighet att utföra initieringar på ServicePackage-scopet innan andra CodePackages börjar körningen. Deras relation till ett ServicePackage är jämförbar med vad en [SetupEntryPoint][setup-entry-point-link] är för ett CodePackage.

Innan du fortsätter med den här artikeln rekommenderar vi att du bekantar dig med [programmodellen Service Fabric][application-model-link] och [service fabric-värdmodellen][hosting-model-link].

> [!NOTE]
> Initializer CodePackages stöds för närvarande inte för tjänster som skrivits med hjälp av [programmeringsmodellen Reliable Services.][reliable-services-link]
 
## <a name="semantics"></a>Semantics

En Initializer CodePackage förväntas **köras till slutförda (exit kod 0)**. En misslyckad Initializer CodePackage startas om tills den har slutförts. Flera Initializer CodePackages tillåts och körs för att **slutföras,** **sekventiellt**, **i en angiven ordning** innan andra CodePackages i ServicePackage börjar köras.

## <a name="specifying-initializer-codepackages"></a>Ange Initializer CodePackages
Du kan markera ett CodePackage som en Initializer genom att ange **initializer-attributet** till **true** i ServiceManifest. När det finns flera Initializer CodePackages kan deras körningsordning anges via **attributet ExecOrder.** **ExecOrder** måste vara ett icke-negativt heltal och är endast giltigt för Initializer CodePackages. Initializer CodePackages med lägre värden för **ExecOrder** körs först. Om **ExecOrder** inte har angetts för ett Initializer CodePackage antas ett standardvärde på 0. Relativ körningsordning för Initializer CodePackages med samma värde **som ExecOrder** är ospecificerat.

Följande ServiceManifest-utdrag beskriver tre CodePackages varav två är markerade som Initializers. När detta ServicePackage aktiveras körs *InitCodePackage0* först eftersom det har det lägsta värdet av **ExecOrder**. När du har slutfört (avsluta kod 0) *av InitCodePackage0*körs *InitCodePackage1.* Slutligen, när *InitCodePackage1*har slutförts körs *WorkloadCodePackage.*

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Komplett exempel med Initializer CodePackages

Låt oss titta på ett komplett exempel med Initializer CodePackages.

> [!IMPORTANT]
> I följande exempel förutsätts förtrogenhet med att skapa [Windows-behållarprogram med Service Fabric och Docker][containers-getting-started-link].
>
> Det här exemplet refererar mcr.microsoft.com/windows/nanoserver:1809. Windows Server-behållare är inte kompatibla i alla versioner av ett värdoperativsystem. Mer information finns i [Kompatibiliteten för Windows Container Version](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

Följande ServiceManifest.xml bygger på servicemanifest-kodavsnittet som beskrivits tidigare. *InitCodePackage0,* *InitCodePackage1* och *WorkloadCodePackage* är CodePackages som representerar behållare. Vid aktivering körs *InitCodePackage0* först. Den loggar ett meddelande till en fil och avslutas. Därefter körs *InitCodePackage1* som också loggar ett meddelande till en fil och avslutas. Slutligen börjar *WorkloadCodePackage* körningen. Det loggar också ett meddelande till en fil, utdata innehållet i filen till **stdout** och sedan pingar för evigt.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

Följande ApplicationManifest.xml beskriver ett program baserat på Den ServiceManifest.xml som beskrivs ovan. Observera att den anger samma **volymmontering** för alla behållare, dvs **C:\WorkspaceOnHost** är monterad på **C:\WorkspaceOnContainer** på alla tre behållarna. Nettoeffekten är att alla behållare skriver till samma loggfil i den ordning de aktiveras.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
När ServicePackage har aktiverats bör innehållet i **C:\WorkspaceOnHost\log.txt** vara följande.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för relaterad information.

* [Service tyg och behållare.][containers-introduction-link]
* [Service Fabric och gäst körbara filer.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

