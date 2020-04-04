---
title: Säkra Azure AD-domäntjänster | Microsoft-dokument
description: Lär dig hur du inaktiverar svaga chiffer, gamla protokoll och NTLM-lösenordshã¤för en hanterad Azure Active Directory Domain Services-hanterad domän.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 581963c94129c36acbd8761d93e369281797fa9f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654723"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Inaktivera svag chiffer- och lösenordshh-synkronisering för att skydda en hanterad Azure AD Domain Services-domän

Som standard möjliggör Azure Active Directory Domain Services (Azure AD DS) användning av chiffer som NTLM v1 och TLS v1. Dessa chiffer kan krävas för vissa äldre program, men anses svaga och kan inaktiveras om du inte behöver dem. Om du har lokal hybridanslutning med Azure AD Connect kan du också inaktivera synkroniseringen av NTLM-lösenordsh hashar.

Den här artikeln visar hur du inaktiverar NTLM v1 och TLS v1 chiffer och inaktivera NTLM lösenord hash synkronisering.

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här artikeln behöver du följande resurser:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs [kan du skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* Installera och konfigurera Azure PowerShell.
    * Om det behövs följer du instruktionerna för att [installera Azure PowerShell-modulen och ansluta till din Azure-prenumeration](/powershell/azure/install-az-ps).
    * Se till att du loggar in på din Azure-prenumeration med cmdlet [connect-AzAccount.][Connect-AzAccount]
* Installera och konfigurera Azure AD PowerShell.
    * Om det behövs följer du instruktionerna för att [installera Azure AD PowerShell-modulen och ansluter till Azure AD](/powershell/azure/active-directory/install-adv2).
    * Se till att du loggar in på din Azure AD-klient med hjälp av [Cmdlet Connect-AzureAD.][Connect-AzureAD]

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Inaktivera svaga chiffer och NTLM-lösenordsh hash-synkronisering

Om du vill inaktivera svaga chiffersviter och NTLM-autentiseringsuppgifter hash-synkronisering loggar du in på ditt Azure-konto och hämtar sedan Azure AD DS-resursen med cmdleten [Get-AzResource:][Get-AzResource]

> [!TIP]
> Om du får ett felmeddelande med kommandot [Get-AzResource][Get-AzResource] som *Microsoft.AAD/DomainServices-resursen* inte finns, [höjer du din åtkomst till att hantera alla Azure-prenumerationer och hanteringsgrupper][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Definiera sedan *DomainSecuritySettings* för att konfigurera följande säkerhetsalternativ:

1. Inaktivera NTLM v1-stöd.
2. Inaktivera synkroniseringen av NTLM-lösenordshiseringar från din lokala AD.
3. Inaktivera TLS v1.

> [!IMPORTANT]
> Användare och tjänstkonton kan inte utföra LDAP-enkla bindningar om du inaktiverar NTLM-lösenordsh hash-synkronisering i azure AD DS-hanterade domänen. Om du behöver utföra enkla LDAP-bindningar ska du inte ange *alternativet "SyncNtlmPasswords"="Disabled";* säkerhetskonfigurationsalternativet i följande kommando.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Slutligen tillämpa de definierade säkerhetsinställningarna på Azure AD DS-hanterad domän med hjälp av [cmdlet Set-AzResource.][Set-AzResource] Ange Azure AD DS-resursen från det första steget och säkerhetsinställningarna från föregående steg.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Det tar en stund innan säkerhetsinställningarna tillämpas på azure AD DS-hanterade domänen.

## <a name="next-steps"></a>Nästa steg

Mer information om synkroniseringsprocessen finns [i Hur objekt och autentiseringsuppgifter synkroniseras i en Azure AD DS-hanterad domän][synchronization].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD