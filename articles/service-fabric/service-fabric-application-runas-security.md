---
title: Köra en tjänst under system- och lokala säkerhetskonton
description: Lär dig hur du kör ett Service Fabric-program under system- och lokala säkerhetskonton.  Skapa säkerhetsobjekt och tillämpa run-as-principen för att säkert köra dina tjänster.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: 53212f8636602705899834b6db1d3f0d80b5fe4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610138"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Köra en tjänst som ett lokalt användarkonto eller ett lokalt systemkonto
Genom att använda Azure Service Fabric kan du skydda program som körs i klustret under olika användarkonton. Som standard körs Service Fabric-program under kontot som Fabric.exe-processen körs under. Service Fabric ger också möjlighet att köra program under ett lokalt användar- eller systemkonto. Lokala systemkontotyper som stöds är **LocalUser,** **NetworkService,** **LocalService**och **LocalSystem**.  Om du kör Service Fabric i ett fristående Windows-kluster kan du köra en tjänst under [Active Directory-domänkonton](service-fabric-run-service-as-ad-user-or-group.md) eller [grupphanterade tjänstkonton](service-fabric-run-service-as-gmsa.md).

I programmanifestet definierar du de användarkonton som krävs för att köra tjänster eller säkra resurser i avsnittet **Huvudnamn.** Du kan också definiera och skapa användargrupper så att en eller flera användare kan hanteras tillsammans. Detta är användbart när det finns flera användare för olika tjänststartpunkter och de behöver vanliga privilegier som är tillgängliga på gruppnivå.  Användarna refereras sedan i en RunAs-princip, som tillämpas på en viss tjänst eller alla tjänster i programmet. 

Som standard tillämpas runa-principen på huvudstartpunkten.  Du kan också använda en RunAs-princip på installationsanspunkten om du behöver [köra vissa konfigurationsåtgärder med hög behörighet under ett systemkonto](service-fabric-run-script-at-service-startup.md)eller både huvud- och inställningstransaktionspunkter.  

> [!NOTE] 
> Om du tillämpar en RunAs-princip på en tjänst och tjänstmanifestet deklarerar slutpunktsresurser med HTTP-protokollet måste du ange en **SecurityAccessPolicy**.  Mer information finns i [Tilldela en säkerhetsåtkomstprincip för HTTP- och HTTPS-slutpunkter](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Köra en tjänst som lokal användare
Du kan skapa en lokal användare som kan användas för att skydda en tjänst i programmet. När en **localuser-kontotyp** anges i huvudnamnsavsnittet i programmanifestet skapar Service Fabric lokala användarkonton på datorer där programmet distribueras. Som standard har dessa konton inte samma namn som de som anges i programmanifestet (till exempel *Customer3* i följande programmanifestexempel). I stället genereras de dynamiskt och har slumpmässiga lösenord.

I avsnittet **RunAsPolicy** för en **ServiceManifestImport**anger du användarkontot från avsnittet **Huvudnamn** för att köra servicekodpaketet.  I följande exempel visas hur du skapar en lokal användare och tillämpar en RunAs-princip på huvudstartpunkten:

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
Du kan skapa användargrupper och lägga till en eller flera användare i gruppen. Detta är användbart om det finns flera användare för olika tjänsttransaktionspunkter och de måste ha vissa vanliga privilegier som är tillgängliga på gruppnivå. I följande programmanifestexempel visas en lokal grupp med namnet *LocalAdminGroup* som har administratörsbehörighet. Två användare, *Customer1* och *Customer2,* är medlemmar i den här lokala gruppen. I avsnittet **ServiceManifestImport** tillämpas en RunAs-princip för att köra *stateful1Pkg-kodpaketet* som *Customer2*.  En annan RunAs-princip tillämpas för att köra *Web1Pkg-kodpaketet* som *Kund1*.

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

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Tillämpa en standardprincip på alla servicekodpaket
Du kan använda avsnittet **DefaultRunAsPolicy** för att ange ett standardanvändarkonto för alla kodpaket som inte har en specifik **RunAsPolicy** definierad. Om de flesta kodpaket som anges i tjänstmanifestet som används av ett program måste köras under samma användare, kan programmet bara definiera en standardprincip för RunAs med det användarkontot. I följande exempel anges att om ett kodpaket inte har ett **RunAsPolicy** angivet, ska kodpaketet köras under den **MyDefaultAccount-användare** som anges i avsnittet huvudnamn.  Kontotyper som stöds är LocalUser, NetworkService, LocalSystem och LocalService.  Om du använder en lokal användare eller tjänst anger du även kontonamnet och lösenordet.

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

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Felsöka ett kodpaket lokalt med hjälp av omdirigering av konsolen
Ibland är det användbart för felsökning för att se konsolens utdata från en tjänst som körs. Du kan ange en princip för omdirigering av konsolen på startpunkten i tjänstmanifestet, som skriver utdata till en fil. Filutdata skrivs till programmappen som kallas **logga** in på klusternoden där programmet distribueras och körs. 

> [!WARNING]
> Använd aldrig principen om omdirigering av konsolen i ett program som distribueras i produktion eftersom detta kan påverka programmets redundans. *Använd* endast detta för lokal utveckling och felsökning.  
> 
> 

I exemplet med följande tjänstmanifest visas aktivering av omdirigering av konsoler med värdet FileRetentionCount:

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
