---
title: Köra en Azure Service Fabric-tjänst under ett gMSA-konto
description: Lär dig hur du kör en tjänst som en gMSA i ett Service Fabric fristående Windows-kluster.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 99d8089bd12d05e46f91e55c933d58d50baa92f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464271"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Köra tjänster som grupphanterade tjänstkonton
På ett fristående Windows Server-kluster kan du köra en tjänst som ett grupphanterat tjänst konto (gMSA) med hjälp av en RunAs-princip.  Som standard körs Service Fabric-program under det konto som Fabric. exe-processen körs under. Att köra program under olika konton, även i en delad värd miljö, gör dem säkrare från varandra. Observera att detta använder Active Directory lokalt i din domän och inte Azure Active Directory (Azure AD). Genom att använda en gMSA finns det inget lösen ord eller krypterat lösen ord som lagras i applikations manifestet.  Du kan också köra en tjänst som [Active Directory användare eller grupp](service-fabric-run-service-as-ad-user-or-group.md).

I följande exempel visas hur du skapar ett gMSA-konto med namnet *SVC-test $* ; så här distribuerar du det hanterade tjänst kontot till klusternoderna. och hur du konfigurerar användarens huvud namn.

Krav:
- Domänen behöver en KDS-rot nyckel.
- Det måste finnas minst en DOMÄNKONTROLLANT med Windows Server 2012 (eller R2) i domänen.

1. Be en Active Directory domän administratör skapa ett grupphanterat tjänst konto med hjälp av `New-ADServiceAccount`-kommandot och se till att `PrincipalsAllowedToRetrieveManagedPassword` innehåller alla noder i Service Fabric-klusternoderna. `AccountName`, `DnsHostName`och `ServicePrincipalName` måste vara unika.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. På var och en av Service Fabric klusternoder (till exempel `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`) installerar och testar du gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Konfigurera användarens huvud namn och konfigurera runas policy så att den refererar till användaren.
    
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
> Om du använder en RunAs-princip för en tjänst och tjänst manifestet deklarerar slut punkts resurser med HTTP-protokollet måste du ange en **SecurityAccessPolicy**.  Mer information finns i [tilldela en säkerhets åtkomst princip för HTTP-och https-slutpunkter](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
I nästa steg ska du läsa följande artiklar:
* [Förstå program modellen](service-fabric-application-model.md)
* [Ange resurser i ett tjänst manifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
