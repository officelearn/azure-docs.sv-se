---
title: Självbetjäningsanmälan för e-postverifierade användare – Azure AD | Microsoft-dokument
description: Använda självbetjäningsanmälan i en Azure Active Directory-klientorganisation (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 953837e22cdd3ba8a54d702eac61461739db82d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74027638"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Vad är självbetjäningsanmäla för Azure Active Directory?

I den här artikeln beskrivs hur du använder självbetjäningsanmälan för att fylla i en organisation i Azure Active Directory (Azure AD). Om du vill ta över ett domännamn från en ohanterat Azure AD-organisation läser du [Ta över en ohanterat katalog som administratör](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Varför använda självbetjäningsanmälning?
* Få kunder till tjänster som de vill ha snabbare
* Skapa e-postbaserade erbjudanden för en tjänst
* Skapa e-postbaserade registreringsflöden som snabbt gör det möjligt för användare att skapa identiteter med hjälp av sina lätt att komma ihåg arbetsspråk för arbete
* En Azure AD-katalog med självbetjäning kan omvandlas till en hanterad katalog som kan användas för andra tjänster

## <a name="terms-and-definitions"></a>Villkor och definitioner
* **Självbetjäningsregistrering:** Det här är den metod med vilken en användare registrerar sig för en molntjänst och har en identitet som automatiskt skapas för dem i Azure AD baserat på deras e-postdomän.
* **Ohanterat Azure AD-katalog:** Det här är katalogen där identiteten skapas. En ohanterat katalog är en katalog som inte har någon global administratör.
* **E-postverifierad användare**: Det här är en typ av användarkonto i Azure AD. En användare som har en identitet som skapas automatiskt efter att ha registrerat sig för ett självbetjäningserbjudande kallas en e-postverifierad användare. En e-postverifierad användare är en vanlig medlem i en katalog som taggats med creationmethod=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Hur kontrollerar jag självbetjäningsinställningarna?
Administratörer har två självbetjäningskontroller idag. De kan kontrollera om:

* Användare kan gå med i katalogen via e-post
* Användare kan licensiera sig för program och tjänster

### <a name="how-can-i-control-these-capabilities"></a>Hur kan jag kontrollera dessa funktioner?
En administratör kan konfigurera dessa funktioner med hjälp av följande Azure AD cmdlet Set-MsolCompanySettings parametrar:

* **AllowEmailVerifiedUsers** styr om en användare kan skapa eller ansluta till en katalog. Om du anger att parametern ska $false kan ingen e-postverifierad användare ansluta till katalogen.
* **AllowAdHocSubscriptions** styr möjligheten för användare att utföra självbetjäningsanmälning. Om du ställer in parametern på $false kan ingen användare utföra självbetjäningsanmälning.
  
AllowEmailVerifiedUsers och AllowAdHocSubscriptions är katalogomfattande inställningar som kan tillämpas på en hanterad eller ohanterad katalog. Här är ett exempel där:

* Du administrerar en katalog med en verifierad domän, till exempel contoso.com
* Du använder B2B-samarbete från en annan katalog föruserdoesnotexist@contoso.comatt bjuda in en användare som inte redan finns ( ) i arbetskatalogen för contoso.com
* Arbetskatalogen har AllowEmailVerifiedUsers aktiverat

Om föregående villkor är sanna skapas en medlemsanvändare i arbetskatalogen och en B2B-gästanvändare skapas i den inbjudande katalogen.

Testanmälningar för Flow och PowerApps styrs inte av inställningen **AllowAdHocSubscriptions.** Mer information finns i följande artiklar:

* [Hur förhindrar jag att mina befintliga användare börjar använda Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Vanliga frågor och svar om flöden i din organisation](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hur fungerar kontrollerna tillsammans?
Dessa två parametrar kan användas tillsammans för att definiera mer exakt kontroll över självbetjäningsanmälning. Med följande kommando kan användarna till exempel utföra självbetjäningsannons, men endast om dessa användare redan har ett konto i Azure AD (med andra ord kan användare som behöver ett e-postverifierat konto skapas först inte utföra självbetjäningsannons):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

Följande flödesschema förklarar de olika kombinationerna för dessa parametrar och de resulterande villkoren för katalog- och självbetjäningsanmälningen.

![flödesschema för självbetjäningskontroller](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Mer information och exempel på hur du använder dessa parametrar finns i [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Nästa steg

* [Lägga till ett anpassat domännamn i Azure AD](../fundamentals/add-custom-domain.md)
* [Hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet-referens](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Stänga ditt arbets- eller skolkonto i en ohanterad katalog](users-close-account.md)
