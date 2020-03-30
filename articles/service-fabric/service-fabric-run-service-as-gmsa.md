---
title: Köra en Azure Service Fabric-tjänst under ett gMSA-konto
description: Lär dig hur du kör en tjänst som ett grupphanterad tjänstkonto (gMSA) i ett fristående service fabric Windows-kluster.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988404"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Köra tjänster som grupphanterade tjänstkonton

I ett fristående Windows Server-kluster kan du köra en tjänst som ett *grupphanterad tjänstkonto* (gMSA) med hjälp av en *RunAs-princip.*  Som standard körs Service Fabric-program `Fabric.exe` under det konto som processen körs under. Att köra program under olika konton, även i en delad värdmiljö, gör dem säkrare från varandra. Genom att använda en gMSA finns det inget lösenord eller krypterat lösenord lagrat i programmanifestet.  Du kan också köra en tjänst som [en Active Directory-användare eller active directory-grupp](service-fabric-run-service-as-ad-user-or-group.md).

I följande exempel visas hur du skapar ett gMSA-konto som heter *svc-Test$*, hur du distribuerar det hanterade tjänstkontot till klusternoderna och hur du konfigurerar användarens huvudnamn.

> [!NOTE]
> Om du använder en gMSA med ett fristående Service Fabric-kluster krävs Active Directory lokalt inom domänen (i stället för Azure Active Directory (Azure AD)).

Förutsättningar:

- Domänen behöver en KDS-rotnyckel.
- Det måste finnas minst en Windows Server 2012 (eller R2) DC i domänen.

1. Låt en Active Directory-domänadministratör skapa ett `New-ADServiceAccount` grupphanterat tjänstkonto `PrincipalsAllowedToRetrieveManagedPassword` med hjälp av cmdlet och se till att inkluderar alla Service Fabric-klusternoder. `AccountName`, `DnsHostName`och `ServicePrincipalName` måste vara unik.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Installera och testa gMSA på var `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`och en av klusternoderna Service Fabric (till exempel , installera och testa gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Konfigurera användarens huvudnamn `RunAsPolicy` och konfigurera för att referera till [användaren](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas).
    
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
> Om du tillämpar en RunAs-princip på en tjänst och tjänstmanifestet deklarerar slutpunktsresurser med HTTP-protokollet måste du ange en **SecurityAccessPolicy**.  Mer information finns i [Tilldela en säkerhetsåtkomstprincip för HTTP- och HTTPS-slutpunkter](service-fabric-assign-policy-to-endpoint.md).
>

Följande artiklar guidar dig genom nästa steg:

- [Förstå programmodellen](service-fabric-application-model.md)
- [Ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md)
- [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
