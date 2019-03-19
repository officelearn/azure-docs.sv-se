---
title: Köra ett Azure Service Fabric-tjänsten under ett gMSA-konto | Microsoft Docs
description: Lär dig hur du kör en tjänst som ett gMSA på ett fristående Service Fabric Windows-kluster.
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
ms.openlocfilehash: 8d14d9191e74cb59c6696568ead425fca61d6f7c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57873911"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Köra tjänster som grupphanterade tjänstkonton
Du kan köra en tjänst som ett grupphanterat tjänstkonto (gMSA) med hjälp av en RunAs-princip på ett fristående kluster Windows Server.  Service Fabric-program körs under kontot som Fabric.exe processen körs under som standard. Köra program under olika konton, även i en delad miljö, gör dem säkrare från varandra. Observera att det använder Active Directory lokalt i din domän och inte Azure Active Directory (AD Azure). Genom att använda ett gMSA kan finns det inga lösenord eller ett krypterat lösenord som lagras i programmanifestet.  Du kan också köra en tjänst som en [Active Directory-användare eller grupp](service-fabric-run-service-as-ad-user-or-group.md).

I följande exempel visas hur du skapar en gMSA-konto med namnet *svc-Test$*; distribuera det hanterade tjänstkontot till noderna i klustret; och hur du konfigurerar huvudanvändaren.

Förutsättningar:
- Domänen måste en KDS-rotnyckel.
- Domänen måste vara på en Windows Server 2012 eller senare funktionsnivå.

1. Be en administratör för Active Directory-domän som skapar en hanterad tjänst konto med den `New-ADServiceAccount` kommandot och se till att den `PrincipalsAllowedToRetrieveManagedPassword` innehåller alla service fabric-klusternoder. `AccountName`, `DnsHostName`, och `ServicePrincipalName` måste vara unikt.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. På var och en av Service Fabric-klusternoder (till exempel `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), installera och testa gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Konfigurera huvudanvändaren och konfigurera RunAsPolicy för att referera till användaren.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
> Om du tillämpa en RunAs-princip till en tjänst och tjänstmanifestet deklarerar endpoint-resurser med HTTP-protokollet, måste du ange en **SecurityAccessPolicy**.  Mer information finns i [tilldela en säkerhetsåtkomstprincip för HTTP och HTTPS-slutpunkterna](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Som ett nästa steg kan du läsa följande artiklar:
* [Förstå programmodellen](service-fabric-application-model.md)
* [Ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
