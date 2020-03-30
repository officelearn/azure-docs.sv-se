---
title: Lös tjänsthuvudvarningar i Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du felsöker konfigurationsaviseringar för tjänstens huvudnamn för Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: 175bfe63176b78c5aeafc7147c46dd5ab1110325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257969"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Kända problem: Tjänsthuvudvarningar i Azure Active Directory Domain Services

[Tjänsthuvudnamn](../active-directory/develop/app-objects-and-service-principals.md) är program som Azure-plattformen använder för att hantera, uppdatera och underhålla en Azure AD DS-hanterad domän. Om ett tjänsthuvudnamn tas bort påverkas funktionaliteten i Azure AD DS-hanterad domän.

Den här artikeln hjälper dig att felsöka och lösa tjänsthuvudrelaterat konfigurationsaviseringar.

## <a name="alert-aadds102-service-principal-not-found"></a>Avisering AADDS102: Tjänstens huvudnamn hittades inte

### <a name="alert-message"></a>Varningsmeddelande

*Ett tjänsthuvudnamn som krävs för att Azure AD Domain Services ska fungera korrekt har tagits bort från din Azure AD-katalog. Den här konfigurationen påverkar Microsofts förmåga att övervaka, hantera, korrigera och synkronisera din hanterade domän.*

Om ett obligatoriskt tjänsthuvudnamn tas bort kan Azure-plattformen inte utföra automatiserade hanteringsuppgifter. Den hanterade Azure AD DS-domänen kanske inte korrekt tillämpar uppdateringar eller tar säkerhetskopior.

### <a name="check-for-missing-service-principals"></a>Kontrollera om tjänsten saknas

Så här kontrollerar du vilket tjänsthuvudnamn som saknas och behöver återskapas:

1. I Azure-portalen väljer du **Azure Active Directory** på menyn för vänster navigering.
1. Välj **Företagsprogram**. Välj *Alla program* på den nedrullningsbara menyn **Programtyp** och välj sedan **Använd**.
1. Sök efter var och en av program-ID: erna. Om inget befintligt program hittas följer du stegen *Lösning* för att skapa tjänstens huvudnamn eller registrera namnområdet igen.

    | Program-ID:t | Lösning |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Återskapa ett saknat tjänsthuvudnamn](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Registrera namnområdet Microsoft.AAD igen](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Registrera namnområdet Microsoft.AAD igen](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Registrera namnområdet Microsoft.AAD igen](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Återskapa en saknad tjänsthuvudnamn

Om program-ID *2565bd9d-da50-47d4-8b85-4c97f669dc36* saknas i din Azure AD-katalog använder du Azure AD PowerShell för att slutföra följande steg. Mer information finns i [installera Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Installera Azure AD PowerShell-modulen och importera den på följande sätt:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Återskapa nu tjänstens huvudnamn med cmdleten [New-AzureAdServicePrincipal:][New-AzureAdServicePrincipal]

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Azure AD DS-hanterade domänens hälsa uppdaterar sig automatiskt inom två timmar och tar bort aviseringen.

### <a name="re-register-the-microsoft-aad-namespace"></a>Registrera namnområdet Microsoft AAD igen

Om ansökan ID *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-29947a-dc74e5d09022*eller *d87dcbc6-a371-462e-88e3-28ad15ec4e64* saknas i din Azure AD-katalog. gör så här för att registrera om *Microsoft.AAD-resursleverantören:*

1. Sök efter och välj **Prenumerationer**i Azure-portalen .
1. Välj den prenumeration som är associerad med din Azure AD DS-hanterade domän.
1. Välj Resursleverantörer i den vänstra **navigeringen**.
1. Sök efter *Microsoft.AAD*och välj sedan **Registrera om**.

Azure AD DS-hanterade domänens hälsa uppdaterar sig automatiskt inom två timmar och tar bort aviseringen.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Varning AADDS105: Programmet för lösenordssynkronisering är inaktuella

### <a name="alert-message"></a>Varningsmeddelande

*Tjänstens huvudnamn med program-ID "d87dcbc6-a371-462e-88e3-28ad15ec4e64" togs bort och återskapades sedan. Återskapandet lämnar inkonsekventa behörigheter för Azure AD Domain Services-resurser som behövs för att hantera din hanterade domän. Synkronisering av lösenord på den hanterade domänen kan påverkas.*

Azure AD DS synkroniserar automatiskt användarkonton och autentiseringsuppgifter från Azure AD. Om det finns ett problem med Azure AD-programmet som används för den här processen misslyckas synkroniseringen mellan Azure AD DS och Azure AD.

### <a name="resolution"></a>Lösning

Om du vill återskapa Azure AD-programmet som används för synkronisering av autentiseringsuppgifter använder du Azure AD PowerShell för att slutföra följande steg. Mer information finns i [installera Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Installera Azure AD PowerShell-modulen och importera den på följande sätt:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Ta nu bort det gamla programmet och objektet med följande PowerShell-cmdlets:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

När du har tagit bort båda programmen återskapas de automatiskt i Azure-plattformen och försöker återuppta lösenordssynkronisering. Azure AD DS-hanterade domänens hälsa uppdaterar sig automatiskt inom två timmar och tar bort aviseringen.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem [öppnar du en Azure-supportbegäran för][azure-support] ytterligare felsökningshjälp.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
