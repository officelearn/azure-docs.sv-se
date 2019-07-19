---
title: 'Azure Active Directory Domain Services: Felsöka tjänstens huvud namn | Microsoft Docs'
description: Fel sökning av tjänstens huvud namns konfiguration för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 9e5fa8c84f5e7ca58117666846b603a118826150
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234131"
---
# <a name="troubleshoot-invalid-service-principal-configurations-for-azure-active-directory-domain-services"></a>Felsök ogiltiga konfigurationer för tjänstens huvud namn för Azure Active Directory Domain Services

Den här artikeln hjälper dig att felsöka och lösa konfigurations fel för tjänstens huvud namn som resulterar i följande aviserings meddelande:

## <a name="alert-aadds102-service-principal-not-found"></a>Aviserings AADDS102: Tjänstens huvud namn hittades inte

**Aviserings meddelande:** *Ett huvud namn för tjänsten som krävs för att Azure AD Domain Services ska fungera korrekt har tagits bort från Azure AD-katalogen. Den här konfigurationen påverkar Microsofts förmåga att övervaka, hantera, uppdatera och synkronisera din hanterade domän.*

[Tjänstens huvud namn](../active-directory/develop/app-objects-and-service-principals.md) är program som Microsoft använder för att hantera, uppdatera och underhålla din hanterade domän. Om de tas bort bryts Microsofts möjlighet att underhålla din domän.


## <a name="check-for-missing-service-principals"></a>Sök efter saknade tjänst huvud namn
Använd följande steg för att avgöra vilka tjänst huvud namn som måste återskapas:

1. Gå till sidan [företags program – alla program](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) i Azure Portal.
2. I list rutan **Visa** väljer du **alla program** och klickar på **Använd**.
3. Använd följande tabell för att söka efter varje program-ID genom att klistra in ID: t i sökrutan och trycka på RETUR. Om Sök resultaten är tomma måste du återskapa tjänstens huvud namn genom att följa stegen i kolumnen lösning.

| Program-ID:t | Lösning |
| :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Återskapa ett tjänst huvud namn som saknas med PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Omregistrera till Microsoft. AAD-namnrymden](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Omregistrera till Microsoft. AAD-namnrymden](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Omregistrera till Microsoft. AAD-namnrymden](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Återskapa ett tjänst huvud namn som saknas med PowerShell
Följ dessa steg om ett huvud namn för tjänsten med ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` ID saknas i Azure AD-katalogen.

**Lösning** Du behöver Azure AD PowerShell för att slutföra de här stegen. Information om hur du installerar Azure AD PowerShell finns i [den här artikeln](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Du löser problemet genom att ange följande kommandon i ett PowerShell-fönster:
1. Installera Azure AD PowerShell-modulen och importera den.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Kontrol lera om tjänstens huvud namn som krävs för Azure AD Domain Services saknas i din katalog genom att köra följande PowerShell-kommando:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Skapa tjänstens huvud namn genom att skriva följande PowerShell-kommando:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. När du har skapat huvud namnet för tjänsten som saknas väntar du två timmar och kontrollerar din hanterade domäns hälsa.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Omregistrera till Microsoft AAD-namnrymden med hjälp av Azure Portal
Följ dessa steg om ett huvud namn för tjänsten med ```443155a6-77f3-45e3-882b-22b3a8d431fb``` ID ```abba844e-bc0e-44b0-947a-dc74e5d09022``` eller ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` eller saknas i Azure AD-katalogen.

**Lösning** Använd följande steg för att återställa domän tjänster på din katalog:

1. Gå till sidan [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure Portal.
2. Välj den prenumeration från tabellen som är kopplad till din hanterade domän
3. Med hjälp av den vänstra navigeringen väljer du **Resource providers**
4. Sök efter "Microsoft. AAD" i tabellen och klicka på **registrera igen**
5. För att säkerställa att aviseringen har lösts, se sidan hälso tillstånd för din hanterade domän på två timmar.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Aviserings AADDS105: Programmet för Lösenordssynkronisering är inaktuellt

**Aviserings meddelande:** Tjänstens huvud namn med program-ID: t "d87dcbc6-a371-462e-88e3-28ad15ec4e64" togs bort och återskapades. Rekreationet lämnar inkonsekventa behörigheter på Azure AD Domain Services resurser som behövs för att underhålla din hanterade domän. Synkronisering av lösen ord på din hanterade domän kan påverkas.


**Lösning** Du behöver Azure AD PowerShell för att slutföra de här stegen. Information om hur du installerar Azure AD PowerShell finns i [den här artikeln](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Du löser problemet genom att ange följande kommandon i ett PowerShell-fönster:
1. Installera Azure AD PowerShell-modulen och importera den.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Ta bort det gamla programmet och objektet med hjälp av följande PowerShell-kommandon

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. När du har tagit bort båda kommer systemet att åtgärda dem och återskapa de program som krävs för Lösenordssynkronisering. För att säkerställa att aviseringen har åtgärd ATS, väntar du två timmar och kontrollerar din domäns hälsa.


## <a name="contact-us"></a>Kontakta oss
Kontakta Azure Active Directory Domain Services produkt teamet för att [dela feedback eller för support](contact-us.md).
