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
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 62917a1d342158ec2114a9204ee1ca9e447284fa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Kör en tjänst som ett lokalt användarkonto eller kontot Lokalt system
Du kan skydda program som körs i kluster under olika användarkonton med hjälp av Azure Service Fabric. Service Fabric-program körs under kontot som Fabric.exe-processen körs under som standard. Service Fabric ger också möjlighet att köra program under ett lokalt användarkonto eller lokala systemkontot, vilket görs genom att ange en RunAs-policy i programmanifestet. Typer av lokala system som stöds är **Lokalanvändare**, **NetworkService**, **LocalService**, och **LocalSystem**.

Du kan också definiera och skapa användargrupper så att en eller flera användare kan läggas till varje grupp som ska hanteras tillsammans. Detta är användbart när det finns flera användare för olika startpunkter och de måste ha vissa vanliga behörigheter som är tillgängliga på gruppnivå.

> [!NOTE] 
> Om du använder en RunAs-princip till en tjänst och tjänstmanifestet deklarerar endpoint resurser med HTTP-protokollet, måste du ange en **SecurityAccessPolicy**.  Mer information finns i [tilldela en säkerhetsprincip åtkomst för HTTP och HTTPS-slutpunkter](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="create-local-user-groups"></a>Skapa lokala användargrupper
Du kan definiera och skapa användargrupper som tillåter att en eller flera användare som ska läggas till en grupp. Detta är användbart om det finns flera användare för olika startpunkter och de måste ha vissa vanliga behörigheter som är tillgängliga på gruppnivå. I följande exempel visas en lokal grupp som kallas **LocalAdminGroup** som har administratörsbehörighet. Två användare, Customer1 och Customer2, läggs till som medlemmar i den lokala gruppen i det här programmets manifest exemplet:

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
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
```

## <a name="create-local-users"></a>Skapa lokala användare
Du kan skapa en lokal användare som kan användas för att skydda en tjänst i programmet. När en **Lokalanvändare** kontotyp som anges i avsnittet säkerhetsobjekt i programmanifestet, Service Fabric skapar lokala användarkonton på datorer där programmet har distribuerats. Som standard har dessa konton inte samma namn som de som anges i programmanifestet (till exempel Customer3 i application manifest exemplet). I stället de genereras dynamiskt och ha slumpmässiga lösenord.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Om ett program kräver att användarkonto och lösenord vara samma på alla datorer (till exempel, om du vill aktivera NTLM-autentisering), ange klustermanifestet **NTLMAuthenticationEnabled** till true. Klustermanifestet måste också ange en **NTLMAuthenticationPasswordSecret** som används för att generera samma lösenord på alla datorer.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

## <a name="assign-policies-to-the-service-code-packages"></a>Tilldela principer till kod servicepaket
Den **RunAsPolicy** avsnittet för en **ServiceManifestImport** anger kontot från avsnittet säkerhetsobjekt som ska användas för att köra en kodpaketet. Koden paket från service manifest associerar även med användarkonton i avsnittet säkerhetsobjekt. Du kan ange detta för installations- eller huvudsakliga startpunkter eller ange `All` ska gälla för båda. I följande exempel visas olika principer tillämpas:

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Om **EntryPointType** har inte angetts, standard är inställd på `EntryPointType=”Main”`. Ange **SetupEntryPoint** är särskilt användbar när du vill köra vissa åtgärder för höga installationsprogrammet under en system-kontot. Mer information finns i [köra ett skript för start av tjänsten som ett lokalt konto för användaren eller systemet](service-fabric-run-script-at-service-startup.md). Den faktiska kod kan köras under ett konto med lägre behörighet.

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Gäller en standardprincip för alla service code-paket
Du använder den **DefaultRunAsPolicy** att ange ett användarkonto för standard för alla paket som inte har en specifik **RunAsPolicy** definieras. Om de flesta av de kod paket som har angetts i tjänstmanifestet som används av ett program måste köras under samma användare, kan programmet bara att definiera en standardprincip RunAs med användarkontot. I följande exempel anger att om en kodpaketet saknar en **RunAsPolicy** anges kodpaketet ska köras under den **MyDefaultAccount** anges i avsnittet säkerhetsobjekt.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Förstå programmet modellen](service-fabric-application-model.md)
* [Ange resurser i en tjänstmanifestet](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
