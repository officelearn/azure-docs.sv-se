---
title: Kör en Azure Service Fabric-tjänst under system- och lokala säkerhetskonton | Microsoft Docs
description: Lär dig mer om att köra ett Service Fabric-program under system- och lokala säkerhetskonton.  Skapa säkerhetsobjekt och tillämpa kör som-principen för att köra dina tjänster på ett säkert sätt.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: f454ec7805db0a79f9346f252809c9d7f6869734
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57871019"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Köra tjänster som ett lokalt användarkonto eller kontot Lokalt system
Med hjälp av Azure Service Fabric kan skydda du program som körs i klustret under olika användarkonton. Service Fabric-program körs under kontot som Fabric.exe processen körs under som standard. Service Fabric ger också möjlighet att köra program under ett lokalt konto för användaren eller systemet. Typer av lokala system som stöds är **Lokalanvändare**, **NetworkService**, **LocalService**, och **LocalSystem**.  Om du använder Service Fabric på ett fristående kluster för Windows, kan du köra en tjänst under [Active Directory-domänkonton](service-fabric-run-service-as-ad-user-or-group.md) eller [gruppen hanterade tjänstkonton](service-fabric-run-service-as-gmsa.md).

I applikationsmanifestet, definierar du de användarkonton som krävs för att köra tjänster eller säkra resurser i den **huvudkonton** avsnittet. Du kan också definiera och skapa användargrupper så att en eller flera användare kan hanteras tillsammans. Detta är användbart när det finns flera användare för olika startpunkter och de behöver vanliga behörigheter som är tillgängliga på gruppnivå.  Användarna sedan refereras till i en RunAs-princip som tillämpas på en specifik tjänst eller alla tjänster i programmet. 

Som standard tillämpas RunAs-principen på den huvudsakliga startpunkten.  Du kan också använda en RunAs-princip för konfigurationsstartpunkten, om du behöver [kör vissa åtgärder med hög behörighetsnivå installationsprogrammet under ett systemkonto](service-fabric-run-script-at-service-startup.md), eller båda main och konfigurera startpunkter.  

> [!NOTE] 
> Om du tillämpa en RunAs-princip till en tjänst och tjänstmanifestet deklarerar endpoint-resurser med HTTP-protokollet, måste du ange en **SecurityAccessPolicy**.  Mer information finns i [tilldela en säkerhetsåtkomstprincip för HTTP och HTTPS-slutpunkterna](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Köra tjänster som en lokal användare
Du kan skapa en lokal användare som kan användas för att skydda en tjänst i programmet. När en **Lokalanvändare** typ har angetts i avsnittet säkerhetsobjekt i applikationsmanifestet, Service Fabric skapar lokala användarkonton på datorer där programmet har distribuerats. Som standard dessa konton inte har samma namn som de som anges i applikationsmanifestet (till exempel *Customer3* i följande exempel för program-manifest). I stället de genereras dynamiskt och ha slumpmässiga lösenord.

I den **RunAsPolicy** för en **ServiceManifestImport**, ange användarkontot från den **huvudkonton** avsnitt för att köra kod tjänstpaketet.  I följande exempel visar hur du skapar en lokal användare och tillämpa en RunAs-principen på huvudsakliga startpunkt:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Skapa en lokal användargrupp
Du kan skapa användargrupper och lägga till en eller flera användare i gruppen. Detta är användbart om det finns flera användare för olika startpunkter och de måste ha vissa vanliga behörigheter som är tillgängliga på gruppnivå. I följande application manifest exempel visas en lokal grupp med namnet *LocalAdminGroup* som har administratörsbehörighet. Två användare *Customer1* och *Customer2*, läggs till som medlemmar i den här lokala gruppen. I den **ServiceManifestImport** avsnittet, en RunAs som principen tillämpas för att köra den *Stateful1Pkg* kodpaketet som *Customer2*.  En annan RunAs-principen har tillämpats för att köra den *Web1Pkg* kodpaketet som *Customer1*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Gäller en standardprincip för alla kodpaket för tjänsten
Du använder den **DefaultRunAsPolicy** att ange en standardanvändarkontot för all kod paket som inte har en specifik **RunAsPolicy** definierats. Om de flesta av kodpaket som anges i tjänstmanifestet som används av ett program behöver köras under samma användare kan definiera programmet kan bara en standardprincip för RunAs till det aktuella användarkontot. I följande exempel anger att om ett kodpaket inte har en **RunAsPolicy** anges kodpaketet ska köras under den **MyDefaultAccount** användare som anges i avsnittet säkerhetsobjekt.  Stöds kontotyperna är Lokalanvändare, NetworkService, LocalSystem och LocalService.  Om du använder en lokal användare eller tjänst kan du även ange kontonamn och lösenord.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Felsöka ett kodpaket lokalt med hjälp av omdirigering av konsol
Ibland kan är det användbart för felsökning i konsolens utdata från en pågående tjänst. Du kan ange en princip för omdirigering av konsol på startpunkten i tjänstmanifestet som skriver utdata till en fil. Filen utdata skrivs till programmappen kallas **log** till klusternoden där programmet har distribuerats och körs. 

> [!WARNING]
> Använd aldrig omdirigeringspolicyn konsolen i ett program som har distribuerats i produktionsmiljön, eftersom detta kan påverka program redundans. *Endast* använda detta för lokal utveckling och felsökning.  
> 
> 

Följande tjänst manifest exempel visas att aktivera omdirigering av konsol med ett FileRetentionCount-värde:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Förstå programmodellen](service-fabric-application-model.md)
* [Ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
