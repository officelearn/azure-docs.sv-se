---
title: Köra en Azure Service Fabric-tjänsten under system och för lokala säkerhetskonton | Microsoft Docs
description: Lär dig hur du kör ett Service Fabric-program under system och för lokala säkerhetskonton.  Skapa säkerhetsobjekt och använda Kör som-princip för att köras på ett säkert sätt dina tjänster.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 471e5a79600d6a963a4fa5b6cec8d2cc16137489
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Kör en tjänst som ett lokalt användarkonto eller kontot Lokalt system
Du kan skydda program som körs i kluster under olika användarkonton med hjälp av Azure Service Fabric. Service Fabric-program körs under kontot som Fabric.exe-processen körs under som standard. Service Fabric ger också möjlighet att köra program under ett lokalt konto för användaren eller systemet. Typer av lokala system som stöds är **Lokalanvändare**, **NetworkService**, **LocalService**, och **LocalSystem**.  Om du kör Service Fabric i ett fristående Windows-kluster, kan du köra en tjänst under [Active Directory-domänkonton](service-fabric-run-service-as-ad-user-or-group.md) eller [grupphanterade tjänstkonton](service-fabric-run-service-as-gmsa.md).

I programmanifestet, definierar du de användarkonton som krävs för att köra tjänster eller säkra resurser i den **säkerhetsobjekt** avsnitt. Du kan också definiera och skapa användargrupper så att en eller flera användare kan hanteras tillsammans. Detta är användbart när det finns flera användare för olika startpunkter och de måste vanliga behörigheter som är tillgängliga på gruppnivå.  Sedan referera till användarna i en RunAs-princip som tillämpas på en specifik tjänst eller alla tjänster i programmet. 

Som standard gäller RunAs-principen för den huvudsakliga startpunkten.  Du kan också använda en RunAs-princip till startpunkten installationsprogrammet om du behöver [kör vissa höga installationsprogrammet åtgärder under en systemkontot](service-fabric-run-script-at-service-startup.md), eller både och konfigurera startpunkter.  

> [!NOTE] 
> Om du använder en RunAs-princip till en tjänst och tjänstmanifestet deklarerar endpoint resurser med HTTP-protokollet, måste du ange en **SecurityAccessPolicy**.  Mer information finns i [tilldela en säkerhetsprincip åtkomst för HTTP och HTTPS-slutpunkter](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Kör en tjänst som en lokal användare
Du kan skapa en lokal användare som kan användas för att skydda en tjänst i programmet. När en **Lokalanvändare** kontotyp som anges i avsnittet säkerhetsobjekt i programmanifestet, Service Fabric skapar lokala användarkonton på datorer där programmet har distribuerats. Som standard dessa konton inte har samma namn som de som anges i programmanifestet (till exempel *Customer3* i application manifest exemplet). I stället de genereras dynamiskt och ha slumpmässiga lösenord.

I den **RunAsPolicy** avsnittet för en **ServiceManifestImport**, ange användarkontot från den **säkerhetsobjekt** avsnittet för att köra kod tjänstepaketet.  I följande exempel visas hur du skapar en lokal användare och tillämpa en RunAs-princip till den huvudsakliga startpunkten:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Du kan skapa användargrupper och lägga till en eller flera användare i gruppen. Detta är användbart om det finns flera användare för olika startpunkter och de måste ha vissa vanliga behörigheter som är tillgängliga på gruppnivå. Application manifest i exemplet nedan visas en lokal grupp med namnet *LocalAdminGroup* som har administratörsbehörighet. Två användare *Customer1* och *Customer2*, som blir medlemmar i den lokala gruppen. I den **ServiceManifestImport** avsnittet en RunAs princip används för att köra den *Stateful1Pkg* kodpaketet som *Customer2*.  En annan RunAs-principen används för att köra den *Web1Pkg* kodpaketet som *Customer1*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Gäller en standardprincip för alla service code-paket
Du använder den **DefaultRunAsPolicy** att ange ett användarkonto för standard för alla paket som inte har en specifik **RunAsPolicy** definieras. Om de flesta av de kod paket som har angetts i tjänstmanifestet som används av ett program måste köras under samma användare, kan programmet bara att definiera en standardprincip RunAs med användarkontot. I följande exempel anger att om en kodpaketet saknar en **RunAsPolicy** anges kodpaketet ska köras under den **MyDefaultAccount** användaren som anges i avsnittet säkerhetsobjekt.  Stöds kontotyperna är Lokalanvändare, NetworkService, LocalSystem och LocalService.  Om du använder en lokal användare eller tjänst, också ange kontonamn och lösenord.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Felsöka en kodpaketet lokalt med hjälp av konsolomdirigering
Ibland är det användbart för felsökning att se konsolens utdata från en aktiv tjänst. Du kan ange en princip för omdirigering av konsolen på startpunkten i tjänstmanifestet som skriver utdata till en fil. Filen utdata skrivs till programmappen kallas **loggen** på klusternoden där programmet har distribuerats och kör. 

> [!WARNING]
> Använd aldrig omdirigeringspolicyn konsolen i ett program som distribuerats i produktionsmiljön eftersom detta kan påverka program för växling vid fel. *Endast* använda detta för lokal utveckling och felsökning.  
> 
> 

Följande tjänstmanifestet exempel visar att aktivera omdirigering till konsolen med ett FileRetentionCount-värde:

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
* [Förstå programmet modellen](service-fabric-application-model.md)
* [Ange resurser i en tjänstmanifestet](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
