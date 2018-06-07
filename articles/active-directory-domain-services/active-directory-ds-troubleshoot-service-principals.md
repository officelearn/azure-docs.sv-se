---
title: 'Azure Active Directory Domain Services: Felsöka konfigurationen av tjänstens huvudnamn | Microsoft Docs'
description: Felsöka konfigurationen av tjänstens huvudnamn för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: ergreenl
ms.openlocfilehash: 18a09a22a5dfa4ed94627727cc844c6b7ffc11f6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589463"
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>Felsökning av ogiltig konfiguration för tjänstens huvudnamn för din hanterade domän

Den här artikeln hjälper dig att felsöka och lösa service principal-relaterade konfigurationsfel som resulterar i följande varningsmeddelande:

## <a name="alert-aadds102-service-principal-not-found"></a>Varning AADDS102: Tjänstens huvudnamn saknas

**Varningsmeddelande:** *A Service Principal krävs för Azure AD Domain Services ska fungera korrekt har tagits bort från Azure AD-katalogen. Den här konfigurationen påverkar Microsofts möjlighet att övervaka, hantera, korrigering, och synkronisera din hanterade domän.*

[Tjänsten säkerhetsobjekt](../active-directory/develop/active-directory-application-objects.md) är program som Microsoft använder för att hantera, uppdatera och underhålla din hanterade domän. Om de tas bort, bryts Microsofts möjlighet att underhålla din domän.


## <a name="check-for-missing-service-principals"></a>Sök efter tjänstens huvudnamn saknas
Använd följande steg för att fastställa vilken tjänst som säkerhetsobjekt måste återskapas:

1. Navigera till den [företagsprogram - alla program](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) sida i Azure-portalen.
2. I den **visa** listrutan **alla program** och på **tillämpa**.
3. Använda följande tabell anger du sökning för varje program-ID genom att klistra in det ID: T i sökrutan och trycka på. Om sökresultaten är tomma, måste du återskapa huvudnamn för tjänsten genom att följa stegen i kolumnen ”lösning”.

| Program-ID:t | Lösning |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Skapa en saknas tjänstens huvudnamn med PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Registrera till namnområdet Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Registrera till namnområdet Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Tjänstens huvudnamn self rätta](#service-principals-that-self-correct) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Skapa en saknas tjänstens huvudnamn med PowerShell
Följ dessa steg om ett huvudnamn för tjänsten med ID ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` saknas i Azure AD-katalogen.

**Lösning:** du behöver Azure AD PowerShell för att slutföra dessa steg. Information om hur du installerar Azure AD PowerShell finns i [i den här artikeln](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Skriv följande kommandon i PowerShell-fönstret för att lösa problemet:
1. Installera Azure AD PowerShell-modulen och importera den.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Kontrollera om tjänstens huvudnamn som krävs för Azure AD Domain Services saknas i din katalog genom att köra följande PowerShell-kommando:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Skapa tjänstens huvudnamn genom att skriva följande PowerShell-kommando:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. När du har skapat en saknad tjänst huvudnamn, vänta två timmar och kontrollera hälsan för din hanterade domän.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Registrera till Microsoft AAD-namnområdet med hjälp av Azure portal
Följ dessa steg om ett huvudnamn för tjänsten med ID ```443155a6-77f3-45e3-882b-22b3a8d431fb``` eller ```abba844e-bc0e-44b0-947a-dc74e5d09022``` saknas i Azure AD-katalogen.

**Lösning:** Använd följande steg för att återställa Domain Services på din katalog:

1. Navigera till den [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) sida i Azure-portalen.
2. Välj prenumerationen från tabellen som är associerad med din hanterade domän
3. Med hjälp av det vänstra navigeringsfönstret välja **Resursprovidrar**
4. Sök efter ”Microsoft.AAD” i tabellen och klickar på **registrera**
5. Säkerställ att aviseringen har lösts genom att visa sidan hälsa för din hanterade domän på två timmar.


## <a name="service-principals-that-self-correct"></a>Tjänstens huvudnamn self rätta
Följ dessa steg om ett huvudnamn för tjänsten med ID ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` saknas i Azure AD-katalogen.

**Lösning:** Azure AD Domain Services kan identifiera när den här specifika tjänstens huvudnamn saknas, är felkonfigurerad eller tas bort. Tjänsten skapar automatiskt den här tjänstens huvudnamn. Dock måste du ta bort programmet och objekt som har arbetat med borttagna programmet som när certifikatutfärdaren rullar över, program och objekt kommer inte längre att kunna ändras med den nya tjänsten huvudnamn. Detta leder till en ny fel på din domän. Följ stegen som beskrivs i den [avsnittet AADDS105](#alert-aadds105-password-synchronization-application-is-out-of-date) att förhindra det här problemet. När, kontrollera din hanterade domän hälsa efter två timmar att säkerställa att nytt huvudnamn för tjänsten har återskapats.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Varning AADDS105: Programmet för synkronisering av lösenord är inaktuell

**Varningsmeddelande:** tjänstens huvudnamn med program-ID ”d87dcbc6-a371-462e-88e3-28ad15ec4e64” har tagits bort och sedan återskapas. Återskapning lämnar bakom inkonsekvent behörigheter på Azure AD Domain Services-resurser som krävs för att underhålla din hanterade domän. Synkronisering av lösenord för din hanterade domän kan påverkas.


**Lösning:** du behöver Azure AD PowerShell för att slutföra dessa steg. Information om hur du installerar Azure AD PowerShell finns i [i den här artikeln](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Skriv följande kommandon i PowerShell-fönstret för att lösa problemet:
1. Installera Azure AD PowerShell-modulen och importera den.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Ta bort den gamla program och objekt med följande PowerShell-kommandon

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. När du har tagit bort båda kan reparera sig själv och återskapa de program som behövs för synkronisering av lösenord. För att säkerställa att aviseringen har åtgärdats, vänta två timmar och kontrollera hälsan för din domän.


## <a name="contact-us"></a>Kontakta oss
Kontakta produktteamet Azure Active Directory Domain Services för att [dela feedback eller support](active-directory-ds-contact-us.md).
