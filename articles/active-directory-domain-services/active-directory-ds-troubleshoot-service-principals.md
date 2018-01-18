---
title: "Azure Active Directory Domain Services: Felsöka konfigurationen av tjänstens huvudnamn | Microsoft Docs"
description: "Felsöka konfigurationen av tjänstens huvudnamn för Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: ergreenl
ms.openlocfilehash: e6144a4018f7fbe7dbf7b4693e3f41884e4a80a2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-service-principal-configuration"></a>Azure AD Domain Services - konfiguration för felsökning av tjänstens huvudnamn

Tjänsten, hantera och uppdatera din domän, Microsoft använder olika [tjänstens huvudnamn](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects) att kommunicera med din katalog. Om en är felkonfigurerad eller tas bort kan orsaka ett avbrott i tjänsten.

## <a name="aadds102-service-principal-not-found"></a>AADDS102: Tjänstens huvudnamn saknas

**Varningsmeddelande:**

*Ett huvudnamn för tjänsten som krävs för Azure AD Domain Services ska fungera korrekt har tagits bort från Azure AD-katalogen. Den här konfigurationen påverkar Microsofts möjlighet att övervaka, hantera, korrigering, och synkronisera din hanterade domän.*

Tjänstens huvudnamn är program som Microsoft använder för att hantera, uppdatera och underhålla din hanterade domän. Om de tas bort, bryts Microsofts möjlighet att underhålla din domän. Använd följande steg för att avgöra vilken tjänst som säkerhetsobjekt måste återskapas.

1. Navigera till den [företagsprogram - alla program](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) sida i Azure-portalen.
2. Använd listrutan Visa, markera **alla program** och på **tillämpa**.
3. Använda följande tabell anger du sökning för varje program-ID genom att klistra in det ID: T i sökrutan och trycka på. Om sökresultaten är tomma, måste du återskapa huvudnamn för tjänsten genom att följa stegen i kolumnen ”lösning”.

| Program-ID:t | Lösning |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Återskapa ett saknas huvudnamn för tjänsten med PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Registrera till namnområdet Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Registrera till namnområdet Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Tjänstens huvudnamn Self rätta](#service-principals-that-self-correct) |

### <a name="recreate-a-missing-service-principal-with-powershell"></a>Skapa en saknas tjänstens huvudnamn med PowerShell

*Följ dessa steg för ID: N som saknas: 2565bd9d-da50-47d4-8b85-4c97f669dc36*

**Reparation:**

Du behöver Azure AD PowerShell för att slutföra dessa steg. Information om hur du installerar Azure AD PowerShell finns i [i den här artikeln](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Skriv följande kommandon i PowerShell-fönstret för att lösa problemet:
1. Installera modulen AzureAD
2. Import-Module AzureAD
3. Kontrollera om tjänstens huvudnamn som krävs för Azure AD Domain Services saknas i din katalog genom att köra följande PowerShell-kommando:
      ```PowerShell
      Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
      ```
4. Skapa tjänstens huvudnamn genom att skriva följande PowerShell-kommando:
     ```PowerShell
     New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
     ```
5. När du har skapat saknas tjänstens huvudnamn vänta två timmar och kontrollera hälsan för din domän.


### <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Registrera till Microsoft AAD-namnområdet med hjälp av Azure portal

*Följ dessa steg för ID: N som saknas: 443155a6-77f3-45e3-882b-22b3a8d431fb och abba844e-bc0e-44b0-947a-dc74e5d09022*

**Reparation:**

Använd följande steg för att återställa Domain Services på din katalog:

1. Navigera till den [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) sida i Azure-portalen.
2. Välj prenumerationen från tabellen som är associerad med din hanterade domän
3. Med hjälp av det vänstra navigeringsfönstret välja **Resursprovidrar**
4. Sök efter ”Microsoft.AAD” i tabellen och klickar på **registrera**
5. Om du vill kontrollera att aviseringen har åtgärdats, att visa sidan hälsa avisering på två timmar.


### <a name="service-principals-that-self-correct"></a>Tjänstens huvudnamn self rätta

*Följ dessa steg för ID: N som saknas: d87dcbc6-a371-462e-88e3-28ad15ec4e64*

**Reparation:**

Microsoft kan identifiera när specifika tjänstens huvudnamn saknas, felkonfigurerad eller tas bort. Du kan åtgärda tjänsten snabbt återskapar Microsoft tjänstens huvudnamn sig själv. Kontrollera hälsotillståndet för din domän efter två timmar att se till att objektet har återskapats.

## <a name="contact-us"></a>Kontakta oss
Kontakta produktteamet Azure Active Directory Domain Services för att [dela feedback eller support](active-directory-ds-contact-us.md).
