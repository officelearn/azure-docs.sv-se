---
title: Anpassning riktlinjer för program | Microsoft Docs
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
ms.openlocfilehash: 97ae16e21e0771c89b1c878c3cabaeab25bcee4b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318610"
---
# <a name="branding-guidelines-for-applications"></a>Riktlinjer för varumärkesanpassning för program

Den här artikeln beskriver riktlinjer för varumärkesanpassning bör du använda när du utvecklar program med Azure Active Directory (AD Azure). Dessa riktlinjer hjälper dig att dirigera dina kunder när de vill använda sina arbets- eller skolkonto konto hanteras i Azure AD eller deras personliga konto för registrering och logga in på ditt program.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Personliga konton eller arbets- eller skolkonton från Microsoft

Microsoft hanterar två typer av användarkonton:

* **Personliga konton** (tidigare kallade Windows Live ID). Dessa konton representera relationen mellan *enskilda* användare och Microsoft, och används för att komma åt konsumentenheter och tjänster från Microsoft. Dessa konton är avsedda för personligt bruk.
* **Arbets- eller skolkonto konton.** Dessa konton hanteras av Microsoft för organisationer som använder Azure Active Directory. Dessa konton som används för att logga in på Office 365 och andra företagstjänster från Microsoft.

Microsoft arbets-eller skolkonton tilldelas vanligtvis till slutanvändare (anställda, studenter, statligt anställda) av deras organisationer (företag, skola, myndighet). Dessa konton lärt dig direkt i molnet (Azure AD-plattformen) eller synkroniseras till Azure AD från en lokal katalog, t.ex Windows Server Active Directory. Microsoft är den *skyddar* för arbetet eller skolan konton, men konton som ägs och styrs av organisationen.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Refererar till Azure AD-konton i ditt program

Microsoft saknar en explicit få åtkomst till Azure eller märken Active Directory och inget bör du.

* När användare loggar in, Använd organisationens namn och logotyp så mycket som möjligt. Det här är bättre än att använda allmänna villkor som ”din organisation”.
* När användare inte är inloggad, referera till sina konton som ”arbets- eller skolkonton” och använder Microsoft-logotyp för att förmedla att Microsoft hanterar dessa konton. Använd inte termer som ”enterprise-konto”, ”företag-konto” eller ”företagets-konto”, som att skapa förvirring för användaren.

## <a name="user-account-pictogram"></a>Användarens konto grafik

I en tidigare version av dessa riktlinjer rekommenderar vi använder en ”blå Aktivitetsikon” grafik. Baserat på användare och utvecklare feedback nu rekommenderar vi användning av Microsoft-logotyp i stället. Logotypen Microsoft hjälper användarna att förstå att de kan återanvända det konto de använder med Office 365 eller andra Microsoft-företagstjänster att logga in på din app.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Logga och logga in med Azure AD

Din app kan utgöra separata sökvägar för registrering och inloggning och följande avsnitt innehåller visual vägledning för båda scenarierna.

**Om din app har stöd för registrering (till exempel kostnadsfri utvärderingsversion eller freemium modellen) slutanvändarens**: du kan visa en **inloggning** knapp som ger användare åtkomst till din app med sitt arbetskonto eller ett personligt konto. Azure AD visas en fråga om medgivande första gången de använder din app.

**Om din app krävs behörigheter som endast administratörer kan godkänna eller om din app kräver organisationens licensiering**: separata admin förvärv från användarens inloggning. Den **”hämta den här appen” knappen** omdirigerar administratörer för att logga in och be dem att ge medgivande åt användare i organisationen som har extra förmån för slutanvändaren medgivande anvisningarna för din app.

## <a name="visual-guidance-for-app-acquisition"></a>Visual vägledning för app

Länken ”Hämta appen” måste dirigerar användaren till Azure AD bevilja åtkomst (auktorisera) sida för att tillåta en administratör att godkänna din app ska ha åtkomst till organisationens data, som tillhandahålls av Microsoft. Information om hur du begär åtkomst beskrivs i den [integrera program med Azure Active Directory](active-directory-integrating-applications.md) artikel.

När administratörer samtycker till att din app, kan de välja att lägga till den i deras Office 365 app programstart användarupplevelsen (tillgänglig från waffle och [ https://portal.office.com/myapps ](https://portal.office.com/myapps)). Om du vill visa den här funktionen kan du använda villkor som ”Lägg till den här appen i din organisation” och visa en knapp som i följande exempel:

![Programtyper och scenarier](./media/active-directory-branding-guidelines/add-to-my-org.png)

Vi rekommenderar dock att du skriver förklarande text i stället för knappar. Exempel:

> *Om du redan använder Office 365 eller andra företagstjänst från Microsoft kan du bevilja < your_app_name > åtkomst till din organisations data. Detta kan användarna komma åt < your_app_name > med sina befintliga arbetskonton.*

Högerklicka på det du vill använda och spara den på datorn för att hämta officiella Microsoft-logotypen för användning i din app.

| Tillgång                                | PNG-format | SVG-format |
| ------------------------------------ | ---------- | ---------- |
| Microsoft-logotyp  | ![Microsoft-logotyp PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_MSSymbol_19.png) | ![Microsoft-logotyp SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_MSSymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>Visual vägledning för inloggning

Din app ska visa en knapp för inloggning som dirigerar om användare att logga in slutpunkten som motsvarar det protokoll som du använder för att integrera med Azure AD. Följande avsnitt innehåller information om hur knappen ska se ut.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Grafik och ”logga in med Microsoft”

Det är associering av Microsoft-logotyp och ”logga in med Microsoft” villkoren som unikt representerar Azure AD bland andra identitetsleverantörer din app kan stöda. Om du inte har tillräckligt med utrymme för ”logga in med Microsoft”, är det ok att förkorta i ”logga in”. Du kan använda ett ljust och mörkt färgschema för knapparna.

Följande diagram visar den Microsoft-rekommenderad redlines när tillgångar med din app. Den redlines gälla ”logga in med Microsoft” eller kortare ”logga in” version.

![Logga in med Microsoft redlines](./media/active-directory-branding-guidelines/Sign-in-with-Microsoft-redlines.png)

Högerklicka på det du vill använda och spara den på datorn för att ladda ned de officiella bilderna som ska användas i din app.

| Tillgång                                | PNG-format | SVG-format |
| ------------------------------------ | ---------- | ---------- |
| Logga in med Microsoft (mörkt tema)  | ![Logga in knappen mörka temat PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark.png) | ![Logga in med Microsoft knappen mörka temat SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark.svg) |
| Logga in med Microsoft (lätta tema) | ![Logga in knappen lätta tema PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light.png) | ![Logga in med Microsoft knappen lätta tema SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light.svg) |
| Logga in (mörkt tema)                 | ![Kort sagt logga knappen mörka temat PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark_short.png) | ![Kort sagt logga knappen mörka temat SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_dark_short.svg) |
| Logga in (lätta tema)                | ![Kort sagt logga knappen lätta tema PNG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light_short.png) | ![Kort sagt logga knappen lätta tema SVG](./media/active-directory-branding-guidelines/MS-SymbolLockup_SignIn_light_short.svg) |


## <a name="branding-dos-and-donts"></a>Anpassning bra att veta

**GÖR** använda ”arbets- eller skolkonto konto” i kombination med knappen ”Logga in med Microsoft” för att ge ytterligare förklaring som hjälper användarna att identifiera om de kan använda den. **Inte** använda andra villkor, till exempel ”enterprise-konto”, ”företag-konto” eller ”företagskonto”.

**Inte** använda ”Office 365-ID” eller ”Azure ID”. Office 365 är också namnet på en konsument erbjudande från Microsoft och som inte använder Azure AD för autentisering.

**Inte** alter Microsoft-logotypen.

**Inte** exponera slutanvändarna märken Azure eller Active Directory. Det är ok emellertid använda dessa villkor med utvecklare, IT-proffs och administratörer.

## <a name="navigation-dos-and-donts"></a>Navigering ska och tänka på

**GÖR** ge ett sätt för användarna att logga ut och växla till ett annat konto. Även om de flesta har ett enda personligt konto från Microsoft/Facebook/Google/Twitter, är personer ofta kopplade till mer än en organisation. Stöd för flera inloggade användare kommer snart.
