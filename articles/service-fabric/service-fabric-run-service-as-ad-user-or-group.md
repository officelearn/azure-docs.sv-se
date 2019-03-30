---
title: Kör en Azure Service Fabric-tjänst som en AD-användare eller en grupp | Microsoft Docs
description: Lär dig hur du kör en tjänst som en Active Directory-användare eller grupp på en fristående Service Fabric Windows-kluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 3e0bb62609f13430bd2beab2332a31983874eb8e
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664751"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Köra tjänster som en Active Directory-användare eller grupp
Du kan köra en tjänst som en Active Directory-användare eller en grupp med hjälp av en RunAs-princip på ett fristående kluster Windows Server.  Service Fabric-program körs under kontot som Fabric.exe processen körs under som standard. Köra program under olika konton, även i en delad miljö, gör dem säkrare från varandra. Observera att det använder Active Directory lokalt i din domän och inte Azure Active Directory (AD Azure).  Du kan också köra en tjänst som en [grupphanterat tjänstkonto (gMSA)](service-fabric-run-service-as-gmsa.md).

Du kan sedan komma åt andra resurser i domänen (till exempel filresurser) som har behörighet genom att använda en domänanvändare eller grupp.

I följande exempel visas en Active Directory-användare som kallas *TestUser* med sin domän lösenord krypteras med hjälp av ett certifikat kallas *MyCert*. Du kan använda den `Invoke-ServiceFabricEncryptText` PowerShell-kommando för att skapa den hemliga chiffertexten. Se [hantera hemligheter i Service Fabric-program](service-fabric-application-secret-management.md) mer information.

Du måste distribuera den privata nyckeln för certifikatet att dekryptera lösenordet till den lokala datorn med hjälp av en out-of-band-metoden (i Azure, är detta via Azure Resource Manager). När Service Fabric distribuerar tjänstpaketet till datorn, är det sedan kunna dekryptera hemligheten och autentisera med Active Directory för att köras under autentiseringsuppgifterna (tillsammans med användarnamnet).

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
> Om du tillämpa en RunAs-princip till en tjänst och tjänstmanifestet deklarerar endpoint-resurser med HTTP-protokollet, måste du även ange en **SecurityAccessPolicy**.  Mer information finns i [tilldela en säkerhetsåtkomstprincip för HTTP och HTTPS-slutpunkterna](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Som ett nästa steg kan du läsa följande artiklar:
* [Förstå programmodellen](service-fabric-application-model.md)
* [Ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
