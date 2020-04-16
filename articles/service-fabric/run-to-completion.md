---
title: RunToCompletion semantik i servicetyg
description: Beskriver RunToCompletion-semantik i Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431298"
---
# <a name="runtocompletion"></a>RunToCompletion

Från och med version 7.1 stöder Service Fabric **Semantik** för [körbara behållare][containers-introduction-link] och [gästkörningsbara][guest-executables-introduction-link] program. Dessa semantik möjliggör program och tjänster som slutför en uppgift och avslutar, i motsats till, alltid kör program och tjänster.

Innan du fortsätter med den här artikeln rekommenderar vi att du bekantar dig med [programmodellen Service Fabric][application-model-link] och [service fabric-värdmodellen][hosting-model-link].

> [!NOTE]
> RunToCompletion semantics stöds för närvarande inte för tjänster som skrivits med hjälp av [programmeringsmodellen För tillförlitliga tjänster.][reliable-services-link]
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion semantik och specifikation
RunToCompletion semantics kan anges som en **ExecutionPolicy** när [servicemanifest importeras][application-and-service-manifests-link]. Den angivna principen ärvs av alla CodePackages som består av ServiceManifest. Följande ApplicationManifest.xml-kodavsnitt är ett exempel.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** tillåter följande två attribut:
* **Typ:** **RunToCompletion** är för närvarande det enda tillåtna värdet för det här attributet.
* **Starta om:** Det här attributet anger den omstartsprincip som tillämpas på CodePackages som består av ServicePackage vid fel. En CodePackage som avslutas med en **utgångskod som inte är noll** anses ha misslyckats. Tillåtna värden för det här attributet är **OnFailure** och **Never** with **OnFailure** är standard.

När omstartsprincipen är inställd **på OnFailure**, om något CodePackage misslyckas **(icke-noll exit-kod)** startas den om, med back-offs mellan upprepade fel. När omstartsprincipen är inställd **på Aldrig**, om något CodePackage misslyckas, markeras distributionsstatusen för DeploymentServicePackage som **Misslyckades** men andra CodePackages tillåts fortsätta körningen. Om alla CodePackages som omfattar ServicePackage-körningen ska slutföras **(avslutningskod 0)** markeras distributionsstatusen för DeploymentServicePackage som **RanToCompletion**. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Komplett exempel med RunToCompletion semantik

Låt oss titta på ett komplett exempel med RunToCompletion semantik.

> [!IMPORTANT]
> I följande exempel förutsätts förtrogenhet med att skapa [Windows-behållarprogram med Service Fabric och Docker][containers-getting-started-link].
>
> Det här exemplet refererar mcr.microsoft.com/windows/nanoserver:1809. Windows Server-behållare är inte kompatibla i alla versioner av ett värdoperativsystem. Mer information finns i [Kompatibiliteten för Windows Container Version](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

Följande ServiceManifest.xml beskriver ett ServicePackage bestående av två CodePackages som representerar behållare. *RunToCompletionCodePackage1* loggar bara ett meddelande till **stdout** och avslutar. *RunToCompletionCodePackage2* pingar loopback-adressen ett tag och avslutar sedan med en avslutningskod på antingen **0,** **1** eller **2**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

Följande ApplicationManifest.xml beskriver ett program baserat på Den ServiceManifest.xml som beskrivs ovan. Den anger **RunToCompletion** **ExecutionPolicy** för *WindowsRunToCompletionServicePackage* med en omstartsprincip för **OnFailure**. Vid aktivering av *WindowsRunToCompletionServicePackage*startas dess ingående CodePackages. *RunToCompletionCodePackage1* ska avslutas vid den första aktiveringen. *RunToCompletionCodePackage2* kan dock misslyckas **(icke-noll exit-kod)**, i vilket fall den kommer att startas om eftersom omstartsprincipen är **OnFailure**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Fråga distributionsstatus för ett DistribueratServicePaketage
Distributionsstatus för ett DeployedServicePackage kan efterfrågas från PowerShell med [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] eller från C# med [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync(String, Uri, String)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Överväganden vid användning av RunToCompletion-semantik

Följande punkter bör noteras för det aktuella RunToCompletion-stödet.
* Dessa semantik stöds endast för [behållare][containers-introduction-link] och [gäst körbara][guest-executables-introduction-link] program.
* Uppgraderingsscenarier för program med RunToCompletion-semantik är inte tillåtna. Användare bör ta bort och återskapa sådana program, om det behövs.
* Redundanshändelser kan orsaka att CodePackages körs igen efter att ha slutförts, på samma nod eller andra noder i klustret. Exempel på redundanshändelser är, nod startar om och Uppgraderingar av Service Fabric-körning på en nod.

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
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[fabric-client-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync

