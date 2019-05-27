---
title: Självbetjäningsregistrering för e-postkontrollerad användarkonton – Azure Active Directory | Microsoft Docs
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
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d949b746f05eb440f5ae28f683dfc838217ab47
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956510"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Vad är självbetjäning registrera dig för Azure Active Directory?

Den här artikeln förklarar hur du använder självbetjäningsregistrering för att fylla i en organisation i Azure Active Directory (AD Azure). Om du vill ta över ett domännamn från en ohanterad Azure AD-organisation finns i [ta över en ohanterad katalog som administratör](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Varför använda självanmälan?
* Få kunder till tjänster snabbare resultat
* Skapa e-postbaserad erbjudanden för en tjänst
* Skapa e-postbaserad registrering flöden som snabbt kan du skapa identiteter med hjälp av deras enkelt att komma ihåg work e-post-alias
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
* **AllowAdHocSubscriptions** styr möjligheten för användare att utföra registrering via självbetjäning. Om du ställer in parametern $false kan ingen användare utföra självbetjänad registrering.
  
AllowEmailVerifiedUsers och AllowAdHocSubscriptions är hela katalogen inställningar som kan tillämpas på en hanterad eller ohanterad katalog. Här är ett exempel där:

* Du administrerar en katalog med en verifierad domän, till exempel contoso.com
* Du använder B2B-samarbete från en annan katalog för att bjuda in användare som inte redan finns (userdoesnotexist@contoso.com) i arbetskatalogen för contoso.com
* Arbetskatalogen har AllowEmailVerifiedUsers aktiverat

Om föregående villkor är uppfyllda, sedan en medlemsanvändare skapas i arbetskatalogen och en B2B-gästanvändare skapas i katalogen bjuder in.

Flow och PowerApps utvärderingsversion registreringar kontrolleras inte av den **AllowAdHocSubscriptions** inställningen. Mer information finns i följande artiklar:

* [Hur kan jag förhindra Mina befintliga användare börjar använda Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Flow i din organisation frågor och svar](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hur fungerar kontrollerna tillsammans?
Dessa två parametrar kan användas tillsammans för att definiera mer exakt kontroll över registrering via självbetjäning. Till exempel följande kommando kan användarna utföra självbetjäning registrering, men bara om användarna redan har ett konto i Azure AD (d.v.s. användare som behöver en e-postkontrollerad konto skapas först kan inte utföra självbetjänad registrering):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

I följande flödesschema beskriver olika kombinationer för dessa parametrar och de resulterande villkor katalog-och självanmälan.

![Flödesschema för självbetjänad registrering kontroller](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Mer information och exempel på hur du använder dessa parametrar finns i [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Nästa steg

* [Lägga till ett anpassat domännamn i Azure AD](../fundamentals/add-custom-domain.md)
* [Installera och konfigurera Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet-referens](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Avsluta ditt arbets- eller skolkonto konto i en ohanterad katalog](users-close-account.md)
