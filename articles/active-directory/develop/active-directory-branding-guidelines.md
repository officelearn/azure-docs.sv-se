---
title: Riktlinjer för program för anpassning | Microsoft Docs
description: En heltäckande handbok om utvecklarorienterade resurser för Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 72f4e464-1352-4a49-a18f-c37f58e7d5c4
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.reviewer: arielgo, skwan
ms.custom: aaddev
ms.openlocfilehash: a31af674a0a4000396cb1df2e520a651efc33946
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505924"
---
# <a name="branding-guidelines-for-applications"></a>Riktlinjer för varumärkesanpassning för program

Den här artikeln beskriver riktlinjer för varumärkesanpassning bör du använda när du utvecklar program med Azure Active Directory (AD Azure). Dessa riktlinjer hjälper dig att dirigera dina kunder när de vill använda sitt arbets- eller skolkonto konto hanteras i Azure AD eller deras personliga konto för registrering och inloggning i ditt program.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Personliga konton och arbets- eller skolkonton från Microsoft

Microsoft hanterar två typer av användarkonton:

* **Personliga konton** (kallades tidigare Windows Live ID). Dessa konton representera relationen mellan *enskilda* användare och Microsoft, och används för att komma åt konsumentenheter och tjänster från Microsoft. Dessa konton är avsedda för personligt bruk.
* **Konton för arbets- eller skolkonto.** Dessa konton hanteras av Microsoft för organisationer som använder Azure Active Directory. Dessa konton används för att logga in på Office 365 och andra företagstjänster från Microsoft.

Microsoft arbets-eller skolkonton vanligtvis är tilldelade till slutanvändare (anställda, studenter, federalt anställda) av sina organisationer (company, skola, myndighet). Dessa konton hanterat direkt i molnet (i Azure AD-plattformen) eller synkroniseras till Azure AD från en lokal katalog, t.ex Windows Server Active Directory. Microsoft är den *skyddar* för arbetet eller skolan konton, men konton som ägs och kontrolleras av organisationen.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Refererar till Azure AD-konton i ditt program

Microsoft exponera inte få åtkomst till Azure eller Active Directory-märken och ingen av dem ska du.

* När användare har loggat in, använder du organisationens namn och logotyp så mycket som möjligt. Detta är bättre än att använda allmänna villkor som ”din organisation”.
* När användare inte är inloggad, referera till sina konton som ”arbete eller skola konton” och använda Microsoft-logotypen för att förmedla att Microsoft hanterar dessa konton. Använd inte termer som ”enterprise-konto”, ”företag-konto” eller ”företagets-konto”, som skapar förvirring för användaren.

## <a name="user-account-pictogram"></a>Användare konto grafik

I en tidigare version av dessa riktlinjer rekommenderar vi en ”skylt blå” grafik. Baserat på feedback från användare och utvecklare kan rekommenderar vi nu användning av Microsoft-logotypen i stället. Microsoft-logotypen hjälper användarna att förstå att de kan återanvända det konto de använder med Office 365 eller andra Microsoft-företagstjänster att logga in på din app.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Registrera dig och logga in med Azure AD

Appen kan visa separata sökvägar för registrering och inloggning och följande avsnitt innehåller visual vägledning för båda scenarierna.

**Om din app har stöd för slutanvändare registrera dig (t.ex, kostnadsfri utvärderingsversion eller freemium-modellen)**: du kan visa en **inloggning** knapp som gör att användare åtkomst till din app med sitt arbetskonto eller ett personligt konto. Azure AD visas en medgivandetext för första gången som de har åtkomst till din app.

**Om din app kräver behörigheter som endast administratörer kan godkänna eller om din app kräver organisationens licensiering**: Separera admin förvärv från användarinloggning. Den **”hämta den här appen” knappen** omdirigerar administratörer för att logga in och be dem att ge samtycke åt användare i organisationen som har den ytterligare fördelen att utelämna slutanvändarprompt för godkännande till din app.

## <a name="visual-guidance-for-app-acquisition"></a>Visual vägledning för app-förvärv

Länken ”Hämta appen” omdirigera användaren till Azure AD bevilja åtkomst (auktorisera) att tillåta en administratör att godkänna din app ska ha åtkomst till organisationens data, som tillhandahålls av Microsoft. Information om hur du begär åtkomst beskrivs i den [integrera program med Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md) artikeln.

När administratörer godkänna din app, kan de välja att lägga till den i deras Office 365 app launcher användarupplevelsen (tillgänglig från waffle och [ https://portal.office.com/myapps ](https://portal.office.com/myapps)). Om du vill annonsera den här funktionen kan du använda termer som ”Lägg till den här appen i din organisation” och visa en knapp enligt följande exempel:

![Programtyper och scenarier](./media/active-directory-branding-guidelines/add-to-my-org.png)

Vi rekommenderar dock att du skriver förklarande text istället för att förlita dig på knappar. Exempel:

> *Om du redan använder Office 365 eller annan företagstjänst från Microsoft, kan du bevilja < your_app_name > åtkomst till data i din organisation. Detta kan användarna åtkomst till < your_app_name > med sina befintliga arbetskonton.*

För att hämta den officiella Microsoft-logotypen för användning i din app, högerklickar du på det du vill använda och spara det på din dator.

| Tillgång                                | PNG-format | SVG-format |
| ------------------------------------ | ---------- | ---------- |
| Microsoft-logotyp  | ![Microsoft-logotypen PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_MSSymbol_19.png) | ![Microsoft-logotypen SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_MSSymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>Visual vägledning för att logga in

Din app ska visa en knapp för inloggning som omdirigerar användarna till inloggning slutpunkten som motsvarar det protokoll som du använder för att integrera med Azure AD. Följande avsnitt innehåller information om hur knappen ska se ut.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Grafik och ”logga in med Microsoft”

Det är föreningen av Microsoft-logotypen och villkoren ”logga in med Microsoft” som representerar unikt Azure AD av andra identitetsleverantörer din app kan stöda. Om du inte har tillräckligt med utrymme för ”logga in med Microsoft”, är det ok att förkorta den till ”logga in”. Du kan använda ett ljust och mörkt färgschema för knapparna.

Följande diagram visar den Microsoft-rekommenderad redlines när du använder tillgångarna med din app. Den redlines gäller för ”logga in med Microsoft” eller kortare ”logga in”-versionen.

![Logga in med Microsoft redlines](./media/active-directory-branding-guidelines/Sign-in-with-Microsoft-redlines.png)

För att hämta de officiella avbildningarna för användning i din app, högerklickar du på det du vill använda och spara det på din dator.

| Tillgång                                | PNG-format | SVG-format |
| ------------------------------------ | ---------- | ---------- |
| Logga in med Microsoft (mörkt tema)  | ![Logga in knappen mörkt tema PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark.png) | ![Logga in med Microsoft knappen mörkt tema SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark.svg) |
| Logga in med Microsoft (ljust tema) | ![Logga in knappen ljusa temat PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light.png) | ![Logga in med Microsoft knappen ljusa temat SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light.svg) |
| Logga in (mörkt tema)                 | ![Logga i korthet knappen mörkt tema PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark_short.png) | ![Logga i korthet knappen mörkt tema SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark_short.svg) |
| Logga in (ljust tema)                | ![Logga i korthet knappen ljusa temat PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light_short.png) | ![Logga i korthet knappen ljusa temat SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light_short.svg) |


## <a name="branding-dos-and-donts"></a>Anpassning av bra att veta

**GÖR** använder ”arbets- eller skolkonto konto” i kombination med knappen ”Logga in med Microsoft” för att ge ytterligare förklaring för att hjälpa slutanvändarna att identifiera om de kan använda. **Inte** använder andra termer som ”enterprise-konto”, ”företag-konto” eller ”företagskonto”.

**Inte** använda ”Office 365-ID” eller ”Azure-ID” Office 365 är också namnet på en konsument erbjudandet från Microsoft, som inte använder Azure AD för autentisering.

**Inte** alter Microsoft-logotypen.

**Inte** exponerar användare till Azure eller Active Directory-varumärken. Det går bra men att använda dessa villkor med utvecklare, IT-proffs och administratörer.

## <a name="navigation-dos-and-donts"></a>Navigering göra och tänka på

**GÖR** gör det möjligt för användare att logga ut och växla till ett annat konto. Även om de flesta har en enda personliga konto från Microsoft/Facebook/Google/Twitter, är personer ofta kopplade mer än en organisation. Stöd för flera inloggade användare kommer snart.
