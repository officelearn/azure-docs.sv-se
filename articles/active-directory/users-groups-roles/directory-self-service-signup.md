---
title: Självbetjäning eller utvärderingsversion registreringen i Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 99c5e99fa3bd33ef42e8df6ceba5be4be2cd1249
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37872273"
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Vad är självbetjäningsregistrering för Azure Active Directory?
Den här artikeln förklarar självbetjäningsregistrering och ge stöd för den i Azure Active Directory (AD Azure). Om du vill ta över ett domännamn från en ohanterad Azure AD-klient, finns i [ta över en ohanterad katalog som administratör](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Varför använda självbetjäningsregistrering?
* Få kunder till tjänster snabbare resultat
* Skapa e-postbaserad erbjudanden för en tjänst
* Skapa e-postbaserad registreringen flöden som snabbt kan du skapa identiteter med hjälp av deras enkelt att komma ihåg work e-post-alias
* En klientportal-självservice skapade Azure AD-katalog kan omvandlas till en hanterad katalog som kan användas för andra tjänster

## <a name="terms-and-definitions"></a>Termer och definitioner
* **Självbetjäningsregistrering**: det här är den metod som en användare registrerar sig för en molnbaserad tjänst och har en identitet som skapas automatiskt för dem i Azure AD baserat på e-postdomän.
* **Ohanterad Azure AD-katalog**: det här är den katalog där den identiteten har skapats. En ohanterad katalog är en katalog som har ingen global administratör.
* **E-postkontrollerad användaren**: det här är en typ av användarkonto i Azure AD. En användare som har en identitet som skapas automatiskt när du registrerar dig för ett erbjudande med självbetjäning kallas för en e-postkontrollerad användare. En e-postkontrollerad användare är en vanlig medlem i en katalog som taggats med creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Hur kan jag styra självbetjäingsinställningar?
Administratörer har två självbetjäning kontroller i dag. De kan kontrollera om:

* Användare kan ansluta till katalogen via e-post.
* Användare kan licensiera själva för program och tjänster.

### <a name="how-can-i-control-these-capabilities"></a>Hur kan jag styra dessa funktioner?
En administratör kan konfigurera de här funktionerna med följande cmdlet Set-MsolCompanySettings-parametrarna för Azure AD:

* **AllowEmailVerifiedUsers** styr om en användare kan skapa eller Anslut till en ohanterad katalog. Om du ställer in parametern $false inga e-postkontrollerad användare kan ansluta till katalogen.
* **AllowAdHocSubscriptions** styr möjligheten för användare att utföra registrering av självbetjäning. Om du anger parametern $false kan kan inga användare utföra registrering av självbetjäning. 
  
  > [!NOTE]
  > Flow och PowerApps utvärderingsversion registreringar kontrolleras inte av den **AllowAdHocSubscriptions** inställningen. Mer information finns i följande artiklar:
  > * [Hur kan jag förhindra Mina befintliga användare börjar använda Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
  > * [Flow i din organisation frågor och svar](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hur fungerar kontrollerna tillsammans?
Dessa två parametrar kan användas tillsammans för att definiera mer exakt kontroll över självbetjäningsregistrering. Till exempel följande kommando kan användarna utföra självbetjänad registrering, men endast om dessa användare redan har ett konto i Azure AD (d.v.s. användare som behöver en e-postkontrollerad konto skapas först kan inte utföra självbetjänad registrering):

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
I följande flödesschema beskriver olika kombinationer för dessa parametrar och de resulterande villkor katalog-och registrering av självbetjäning.

![][1]

Mer information och exempel på hur du använder dessa parametrar finns i [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Nästa steg
* [Lägga till ett anpassat domännamn i Azure AD](../fundamentals/add-custom-domain.md)
* [Installera och konfigurera Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet-referens](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/directory-self-service-signup/SelfServiceSignUpControls.png
