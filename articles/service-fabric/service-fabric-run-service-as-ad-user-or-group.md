---
title: Köra en Azure Service Fabric-tjänst som en AD-användare eller AD-grupp
description: Lär dig hur du kör en tjänst som Active Directory-användare eller grupp i ett fristående service fabric-kluster.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: d440aadb66562e32331c9725a9367c12440a315d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464241"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Köra en tjänst som en Active Directory-användare eller active directory-grupp
I ett fristående Windows Server-kluster kan du köra en tjänst som active directory-användare eller -grupp med hjälp av en RunAs-princip.  Som standard körs Service Fabric-program under kontot som Fabric.exe-processen körs under. Att köra program under olika konton, även i en delad värdmiljö, gör dem säkrare från varandra. Observera att detta använder Active Directory lokalt inom din domän och inte Azure Active Directory (Azure AD).  Du kan också köra en tjänst som ett [gruppkonto för hanterade tjänster (gMSA).](service-fabric-run-service-as-gmsa.md)

Genom att använda en domänanvändare eller domängrupp kan du sedan komma åt andra resurser i domänen (till exempel filresurser) som har beviljats behörigheter.

I följande exempel visas en Active Directory-användare som heter *TestUser* med sitt domänlösenord krypterat med hjälp av ett certifikat som heter *MyCert*. Du kan `Invoke-ServiceFabricEncryptText` använda kommandot PowerShell för att skapa den hemliga chiffertexten. Mer information finns [i Hantera hemligheter i Tjänst fabric-program.](service-fabric-application-secret-management.md)

Du måste distribuera den privata nyckeln för certifikatet för att dekryptera lösenordet till den lokala datorn med hjälp av en out-of-band-metod (i Azure är detta via Azure Resource Manager). När Service Fabric sedan distribuerar servicepaketet till datorn kan det dekryptera hemligheten och (tillsammans med användarnamnet) autentisera med Active Directory för att köras under dessa autentiseringsuppgifter.

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
> Om du tillämpar en RunAs-princip på en tjänst och tjänstmanifestet deklarerar slutpunktsresurser med HTTP-protokollet, måste du också ange en **SecurityAccessPolicy**.  Mer information finns i [Tilldela en säkerhetsåtkomstprincip för HTTP- och HTTPS-slutpunkter](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Som ett nästa steg läser du följande artiklar:
* [Förstå programmodellen](service-fabric-application-model.md)
* [Ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
