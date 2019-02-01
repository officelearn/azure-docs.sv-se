---
title: Lägga till användare via självbetjäning eller utvärderingsversion registrering – Azure Active Directory | Microsoft Docs
description: Använd självbetjäningsregistrering i en Azure Active Directory (Azure AD)-klient
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: f02a628a55969dfbb883f53f005733482499a42b
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55506778"
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Vad är självbetjäningsregistrering för Azure Active Directory?

Den här artikeln förklarar självbetjäningsregistrering och ge stöd för den i Azure Active Directory (AD Azure). Om du vill ta över ett domännamn från en ohanterad Azure AD-klient, finns i [ta över en ohanterad katalog som administratör](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Varför använda självbetjäningsregistrering?
* Få kunder till tjänster snabbare resultat
* Skapa e-postbaserad erbjudanden för en tjänst
* Skapa e-postbaserad registreringen flöden som snabbt kan du skapa identiteter med hjälp av deras enkelt att komma ihåg work e-post-alias
* En klientportal-självservice skapade Azure AD-katalog kan omvandlas till en hanterad katalog som kan användas för andra tjänster

## <a name="terms-and-definitions"></a>Termer och definitioner
* **Självbetjäningsregistrering**: Det här är den metod som en användare registrerar sig för en molnbaserad tjänst och har en identitet som skapas automatiskt för dem i Azure AD baserat på e-postdomän.
* **Ohanterad Azure AD-katalog**: Det här är den katalog där den identiteten har skapats. En ohanterad katalog är en katalog som har ingen global administratör.
* **E-postkontrollerad användaren**: Det här är en typ av användarkonto i Azure AD. En användare som har en identitet som skapas automatiskt när du registrerar dig för ett erbjudande med självbetjäning kallas för en e-postkontrollerad användare. En e-postkontrollerad användare är en vanlig medlem i en katalog som taggats med creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Hur kan jag styra självbetjäingsinställningar?
Administratörer har två självbetjäning kontroller i dag. De kan kontrollera om:

* Användare kan ansluta till katalogen via e-post
* Användare kan licensiera själva för program och tjänster

### <a name="how-can-i-control-these-capabilities"></a>Hur kan jag styra dessa funktioner?
En administratör kan konfigurera de här funktionerna med följande cmdlet Set-MsolCompanySettings-parametrarna för Azure AD:

* **AllowEmailVerifiedUsers** styr om en användare kan skapa eller Anslut till en katalog. Om du ställer in parametern $false ingen e-postkontrollerad användare kan ansluta till katalogen.
* **AllowAdHocSubscriptions** styr möjligheten för användare att utföra registrering av självbetjäning. Om du ställer in parametern $false kan ingen användare utföra registrering av självbetjäning.
  
AllowEmailVerifiedUsers och AllowAdHocSubscriptions är hela katalogen inställningar som kan tillämpas på en hanterad eller en ohanterad katalog. Här är ett exempel där:

* Du administrerar en katalog med en verifierad domän, till exempel contoso.com
* Du använder B2B-samarbete från en annan katalog för att bjuda in användare som inte redan finns (userdoesnotexist@contoso.com) i arbetskatalogen för constoso.com
* Arbetskatalogen har AllowEmailVerifiedUsers aktiverat

Om föregående villkor är uppfyllda, sedan en medlemsanvändare skapas i arbetskatalogen och en B2B-gästanvändare skapas i katalogen bjuder in.

Flow och PowerApps utvärderingsversion registreringar kontrolleras inte av den **AllowAdHocSubscriptions** inställningen. Mer information finns i följande artiklar:

* [Hur kan jag förhindra Mina befintliga användare börjar använda Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Flow i din organisation frågor och svar](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hur fungerar kontrollerna tillsammans?
Dessa två parametrar kan användas tillsammans för att definiera mer exakt kontroll över självbetjäningsregistrering. Till exempel följande kommando kan användarna utföra självbetjänad registrering, men endast om dessa användare redan har ett konto i Azure AD (d.v.s. användare som behöver en e-postkontrollerad konto skapas först kan inte utföra självbetjänad registrering):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

I följande flödesschema beskriver olika kombinationer för dessa parametrar och de resulterande villkor katalog-och registrering av självbetjäning.

![registrera dig kontroller för självbetjäningsportalen](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Mer information och exempel på hur du använder dessa parametrar finns i [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Nästa steg

* [Lägga till ett anpassat domännamn i Azure AD](../fundamentals/add-custom-domain.md)
* [Installera och konfigurera Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet-referens](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
