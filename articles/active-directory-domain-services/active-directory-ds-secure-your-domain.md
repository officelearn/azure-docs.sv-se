---
title: Skydda din hanterade domän i Azure Active Directory Domain Services | Microsoft Docs
description: Skydda din hanterade domän
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: maheshu
ms.openlocfilehash: 20579f7abd6cd815377c3e97d820a3e5490e0f95
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902527"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Skydda din Azure AD Domain Services-hanterad domän
Den här artikeln hjälper dig skydda din hanterade domän. Du kan inaktivera användningen av svaga krypteringssviter och inaktivera synkronisering av lösenordshash för NTLM autentiseringsuppgifter.

## <a name="install-the-required-powershell-modules"></a>Installera PowerShell-moduler som krävs

### <a name="install-and-configure-azure-ad-powershell"></a>Installera och konfigurera Azure AD PowerShell
Följ instruktionerna i artikeln om du vill [installerar Azure AD PowerShell-modulen och ansluter till Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Installera och konfigurera Azure PowerShell
Följ instruktionerna i artikeln om du vill [installera Azure PowerShell-modulen och ansluta till din Azure-prenumeration](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Inaktivera svaga chiffersviter och synkronisering av lösenordshash för NTLM autentiseringsuppgifter
Använd följande PowerShell-skript för att:
1. Inaktivera NTLM v1-stöd i den hanterade domänen.
2. Inaktiverar du synkroniseringen av NTLM-lösenordshashvärden från din lokala AD.
3. Inaktivera TLS v1 i den hanterade domänen.

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

## <a name="next-steps"></a>Nästa steg
* [Förstå synkronisering i Azure AD Domain Services](active-directory-ds-synchronization.md)
