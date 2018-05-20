---
title: Köra en Azure Service Fabric-tjänsten under ett konto för gMSA | Microsoft Docs
description: Lär dig hur du kör en tjänst som ett gMSA på ett fristående Service Fabric-Windows-kluster.
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
ms.openlocfilehash: 5f93285061708172b9b6ac40dc97fce08f7b2a86
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Köra tjänster som grupphanterade tjänstkonton
Du kan köra en tjänst som ett grupphanterat tjänstkonto (gMSA) med hjälp av en RunAs-princip på en fristående Windows Server-kluster.  Service Fabric-program körs under kontot som Fabric.exe-processen körs under som standard. Kör program under olika konton, även i en delad värdmiljö gör dem säkrare från varandra. Observera att detta använder Active Directory lokalt i din domän och inte Azure Active Directory (AD Azure). Genom att använda ett gMSA, finns det ingen lösenord eller krypterade lösenordet lagras i programmanifestet.  Du kan också köra en tjänst som en [Active Directory-användare eller grupp](service-fabric-run-service-as-ad-user-or-group.md).

I följande exempel visas hur du skapar ett konto för gMSA kallas *svc-Test$*; distribuera det hanterade tjänstkontot till klusternoderna, och hur du konfigurerar användarens huvudnamn.

Förutsättningar:
- Domänen måste en KDS-rotnyckel.
- Domänen måste vara på en Windows Server 2012 eller senare funktionsnivå.

1. Active Directory-domän som administratör skapa en grupp hanteras konto med hjälp av `New-ADServiceAccount` cmdleten igen och kontrollera att den `PrincipalsAllowedToRetrieveManagedPassword` innehåller alla klusternoder för service fabric. `AccountName`, `DnsHostName`, och `ServicePrincipalName` måste vara unika.

    ```poweshell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. På varje Service Fabric klusternoder (till exempel `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), installera och testa gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Konfigurera användarens huvudnamn och konfigurera RunAsPolicy för att referera till användaren.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE] 
> Om du använder en RunAs-princip till en tjänst och tjänstmanifestet deklarerar endpoint resurser med HTTP-protokollet, måste du ange en **SecurityAccessPolicy**.  Mer information finns i [tilldela en säkerhetsprincip åtkomst för HTTP och HTTPS-slutpunkter](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Läs följande artiklar som ett nästa steg:
* [Förstå programmet modellen](service-fabric-application-model.md)
* [Ange resurser i en tjänstmanifestet](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
