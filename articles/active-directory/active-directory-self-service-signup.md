---
title: Självbetjäning eller utvärderingsversioner registreringen i Azure Active Directory | Microsoft Docs
description: Använd självbetjäningsregistrering i en Azure Active Directory (Azure AD)-klient
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 01/28/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 67fd5ba9be2de1f79511c806ffaa0ae3001bfdcf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33760297"
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Vad är självbetjäningsregistrering för Azure Active Directory?
Den här artikeln förklarar självbetjäningsregistrering och hur du stöd i Azure Active Directory (AD Azure). Om du vill ta över ett domännamn från en ohanterad Azure AD-klient, se [ta över en ohanterad katalog som administratör](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Varför använda självbetjäningsregistrering?
* Hämta kunder till tjänster som de vill snabbare
* Skapa e-postbaserad erbjudanden för en tjänst
* Skapa e-postbaserad signup flöden som snabbt ger användarna möjlighet att skapa identiteter med hjälp av deras arbete lätt att komma ihåg e-alias
* En egen-service skapade Azure AD-katalog kan omvandlas till en hanterad katalog som kan användas för andra tjänster

## <a name="terms-and-definitions"></a>Termer och definitioner
* **Självbetjäningsregistrering**: det här är den metod som en användare som registrerar sig för en tjänst i molnet och har en identitet skapas automatiskt för dem i Azure AD utifrån deras e-postdomän.
* **Ohanterad Azure AD-katalog**: det här är den katalog där identitet skapas. En ohanterad katalog är en katalog som har ingen global administratör.
* **E-post verifierade användaren**: Detta är en typ av konto i Azure AD. En användare som har en identitet skapas automatiskt när du registrerar dig för ett erbjudande för självbetjäning kallas en e-post verifierade användare. Ett e-post verifierade användaren är medlem reguljära i en katalog som är märkta med creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Hur kan jag styra självbetjäning inställningar?
Administratörer har två självbetjäning kontroller idag. De kan styra om:

* Användare kan ansluta till katalogen via e-post.
* Användare kan licensiera själva för program och tjänster.

### <a name="how-can-i-control-these-capabilities"></a>Hur kan du styra dessa funktioner?
En administratör kan konfigurera de här funktionerna med följande cmdlet Set-MsolCompanySettings-parametrarna för Azure AD:

* **AllowEmailVerifiedUsers** styr om en användare kan skapa eller Anslut till en ohanterad katalog. Om du anger parametern $false inga e-post verifierade användare kan ansluta till katalogen.
* **AllowAdHocSubscriptions** styr möjligheten för användare att utföra självbetjäningsregistrering. Om du anger parametern $false kan ingen användare utföra självbetjäningsregistrering. 
  
  > [!NOTE]
  > Trafikflöde och PowerApps utvärderingsversion signups inte styrs av den **AllowAdHocSubscriptions** inställningen. Mer information finns i följande artiklar:
  > * [Hur kan jag förhindra att Mina befintliga användare börjar använda Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
  > * [Flöda i din organisation frågor och svar](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hur fungerar kontrollerna som tillsammans?
Dessa två parametrar kan användas tillsammans för att definiera mer exakt kontroll över självbetjäningsregistrering. Till exempel följande kommando gör att användarna kan utföra självbetjäningsregistrering, men endast om de användarna som redan har ett konto i Azure AD (med andra ord användare som behöver en verifierad e-post konto skapas först kan inte utföra självbetjäningsregistrering):

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
I följande flödesschema förklaras olika kombinationer för dessa parametrar och de resulterande villkor katalog-och självbetjäningsregistrering.

![][1]

Mer information och exempel på hur du använder dessa parametrar finns [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Nästa steg
* [Lägga till ett anpassat domännamn i Azure AD](add-custom-domain.md)
* [Installera och konfigurera Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet-referens](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
