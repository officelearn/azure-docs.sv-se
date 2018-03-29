---
title: Kör ett Azure Service Fabric-tjänsten som en AD-användare eller grupp | Microsoft Docs
description: Lär dig hur du kör en tjänst som en Active Directory-användare eller grupp på en fristående Service Fabric-Windows-kluster.
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
ms.openlocfilehash: 1cf23a8f564553e65ac2c0fd34d44d81fe2327ea
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Kör en tjänst som en Active Directory-användare eller grupp
Du kan skydda program som körs i kluster under olika användarkonton med hjälp av Azure Service Fabric. Det gör att program som körs, även i en delad värdmiljö säkrare från varandra. Service Fabric-program körs under kontot som Fabric.exe-processen körs under som standard. För ett fristående Windows Server-kluster kan du köra en tjänst som en [grupphanterat tjänstkonto (gMSA)](service-fabric-run-service-as-gmsa.md) eller en Active Directory-användare eller en grupp med en RunAs-princip. Observera att detta använder Active Directory lokalt i din domän och inte Azure Active Directory (AD Azure).

Genom att använda en domänanvändare eller grupp kan du sedan komma åt andra resurser i domänen (till exempel filresurser) som har behörighet.

I följande exempel visas en Active Directory-användare som kallas *TestUser* med sin domän lösenord krypteras med hjälp av ett certifikat kallas *MyCert*. Du kan använda den `Invoke-ServiceFabricEncryptText` PowerShell-kommando för att skapa den hemliga chiffertext. Se [hantera hemligheter i Service Fabric program](service-fabric-application-secret-management.md) mer information.

Du måste distribuera den privata nyckeln för certifikat för att dekryptera lösenordet till den lokala datorn med hjälp av en out-of-band-metoden (i Azure, är detta via Azure Resource Manager). Sedan när Service Fabric distribuerar tjänstepaketet till datorn, är så möjligt att dekryptera hemligheten och autentisera med Active Directory för att köras under autentiseringsuppgifterna (tillsammans med användarnamnet).

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Om du använder en RunAs-princip till en tjänst och tjänstmanifestet deklarerar endpoint resurser med HTTP-protokollet, måste du också ange en **SecurityAccessPolicy**.  Mer information finns i [tilldela en säkerhetsprincip åtkomst för HTTP och HTTPS-slutpunkter](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Läs följande artiklar som ett nästa steg:
* [Förstå programmet modellen](service-fabric-application-model.md)
* [Ange resurser i en tjänstmanifestet](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
