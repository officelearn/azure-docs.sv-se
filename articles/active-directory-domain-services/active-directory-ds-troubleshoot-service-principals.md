---
title: 'Azure Active Directory Domain Services: Felsöka Service Principal configuration | Microsoft Docs'
description: Felsöka konfigurationen av tjänstens huvudnamn för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ergreenl
ms.openlocfilehash: 2c39e8f172283f512037e0d991b2c22eb816c8f6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191337"
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>Felsökning av ogiltig konfiguration för tjänstens huvudnamn för din hanterade domän

Den här artikeln hjälper dig att felsöka och lösa tjänstens huvudnamn-relaterade konfigurationsfel som resulterar i följande varningsmeddelande:

## <a name="alert-aadds102-service-principal-not-found"></a>Varning AADDS102: Tjänstens huvudnamn hittades inte

**Varningsmeddelande:** *Ett huvudnamn för tjänsten som krävs för Azure AD Domain Services ska fungera korrekt har tagits bort från Azure AD-katalogen. Den här konfigurationen påverkar Microsofts förmåga att övervaka, hantera, korrigera och synkronisera din hanterade domän.*

[Tjänsthuvudnamn](../active-directory/develop/app-objects-and-service-principals.md) program som Microsoft använder för att hantera, uppdatera och underhålla din hanterade domän. Om de tas bort bryts Microsofts förmåga att hantera din domän.


## <a name="check-for-missing-service-principals"></a>Kontrollera om tjänstens huvudnamn som saknas
Använd följande steg för att avgöra vilken tjänst som huvudnamn måste återskapas:

1. Navigera till den [företagsprogram – alla program](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) sidan på Azure portal.
2. I den **visa** listrutan **alla program** och klicka på **tillämpa**.
3. Med hjälp av tabellen nedan, Sök efter varje program-ID genom att klistra in ID: T i sökrutan och trycka på Ange. Om sökresultaten är tom, måste du återskapa tjänstens huvudnamn genom att följa stegen i kolumnen ”lösning”.

| Program-ID:t | Lösning |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Återskapa saknas tjänstens huvudnamn med PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Registrera till Microsoft.AAD-namnområde](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Registrera till Microsoft.AAD-namnområde](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Registrera till Microsoft.AAD-namnområde](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Återskapa saknas tjänstens huvudnamn med PowerShell
Följ stegen nedan om ett huvudnamn för tjänsten med ID ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` saknas från Azure AD-katalogen.

**Lösning:** Du behöver Azure AD PowerShell för att slutföra de här stegen. Information om hur du installerar Azure AD PowerShell finns i [i den här artikeln](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Skriv följande kommandon i ett PowerShell-fönster för att åtgärda problemet:
1. Installera Azure AD PowerShell-modulen och importera den.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Kontrollera om tjänstens huvudnamn krävs för Azure AD Domain Services är saknas i din katalog genom att köra följande PowerShell-kommando:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Skapa tjänstens huvudnamn genom att skriva följande PowerShell-kommando:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. När du har skapat en saknad tjänst huvudnamn, vänta två timmar och kontrollera hälsan hos din hanterade domän.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Registrera till Microsoft AAD-namnområde med Azure portal
Följ stegen nedan om ett huvudnamn för tjänsten med ID ```443155a6-77f3-45e3-882b-22b3a8d431fb``` eller ```abba844e-bc0e-44b0-947a-dc74e5d09022``` eller ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` saknas från Azure AD-katalogen.

**Lösning:** Använd följande steg för att återställa Domain Services för din katalog:

1. Navigera till den [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) sidan på Azure portal.
2. Välj prenumerationen från tabellen som är associerad med din hanterade domän
3. Med hjälp av det vänstra navigeringsfältet väljer **Resursprovidrar**
4. Sök efter ”Microsoft.AAD” i tabellen och klicka på **Omregistrera**
5. Visa sidan hälsotillståndet för din hanterade domän för att säkerställa att aviseringen har lösts, på två timmar.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Varning AADDS105: Program för synkronisering av lösenord är inaktuell

**Varningsmeddelande:** Tjänstens huvudnamn med program-ID ”d87dcbc6-a371-462e-88e3-28ad15ec4e64” har tagits bort och sedan återskapas. Återskapning lämnas bakom inkonsekvent behörigheter på Azure AD Domain Services-resurser som behövs för att underhålla din hanterade domän. Synkronisering av lösenord på den hanterade domänen kan påverkas.


**Lösning:** Du behöver Azure AD PowerShell för att slutföra de här stegen. Information om hur du installerar Azure AD PowerShell finns i [i den här artikeln](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Skriv följande kommandon i ett PowerShell-fönster för att åtgärda problemet:
1. Installera Azure AD PowerShell-modulen och importera den.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Ta bort gamla program och -objektet med följande PowerShell-kommandon

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. När du tar bort båda, åtgärda själva och återskapa de program som behövs för Lösenordssynkronisering. För att säkerställa att aviseringen har reparerats, vänta två timmar och kontrollera hälsan för domänen.


## <a name="contact-us"></a>Kontakta oss
Kontakta Azure Active Directory Domain Services-produktteamet att [dela feedback eller support](active-directory-ds-contact-us.md).
