---
title: Skydda din Azure Active Directory Domain Services hanterade domän | Microsoft Docs
description: Skydda din hanterade domän
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 923ecae9dc649b8f5cdcfd447b78fdec0805927a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879155"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Skydda din Azure AD Domain Services hanterade domän
Den här artikeln hjälper dig att skydda din hanterade domän. Du kan inaktivera användningen av svaga chiffersviter och inaktivera hash-synkronisering av NTLM-autentiseringsuppgifter.

## <a name="install-the-required-powershell-modules"></a>Installera de PowerShell-moduler som krävs

### <a name="install-and-configure-azure-ad-powershell"></a>Installera och konfigurera Azure AD PowerShell
Följ anvisningarna i artikeln för att [Installera Azure AD PowerShell-modulen och ansluta till Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Installera och konfigurera Azure PowerShell
Följ anvisningarna i artikeln för att [installera Azure PowerShell-modulen och ansluta till din Azure-prenumeration](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Inaktivera svaga chiffersviter och hash-synkronisering av NTLM-autentiseringsuppgifter
Använd följande PowerShell-skript för att:

1. Inaktivera NTLM v1-support på den hanterade domänen.
2. Inaktivera synkroniseringen av NTLM-lösenords-hashvärden från din lokala AD.
3. Inaktivera TLS v1 på den hanterade domänen.

Om du får ett fel meddelande med `Get-AzResource` kommandot som *Microsoft. AAD/DomainServices-* resursen inte finns kan du [öka din åtkomst för att hantera alla Azure-prenumerationer och hanterings grupper](../role-based-access-control/elevate-access-global-admin.md).

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

> [!IMPORTANT]
> Användare (och tjänst konton) kan inte utföra enkla LDAP-bindningar om du har inaktiverat NTLM Password hash-synkronisering på din Azure AD Domain Services-instans.  Mer information om hur du inaktiverar hash-synkronisering för NTLM-lösenord finns i [skydda din Azure AD DOmain Services-hanterade domän](secure-your-domain.md).
>
>

## <a name="next-steps"></a>Nästa steg
* [Förstå synkronisering i Azure AD Domain Services](synchronization.md)
