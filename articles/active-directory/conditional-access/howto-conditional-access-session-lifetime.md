---
title: Konfigurera hantering av autentiseringssessioner – Azure Active Directory
description: Anpassa Azure AD-konfigurationen för autentiseringssession, inklusive användarloggningsfrekvens och webbläsarsessionsbeständighet.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9c0c88064c00c97de7dc58a500910e81c04eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263288"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Konfigurera hantering av autentiseringssessioner med villkorad åtkomst

I komplexa distributioner kan organisationer behöva begränsa autentiseringssessioner. Vissa scenarier kan vara:

* Resursåtkomst från en ohanterlig eller delad enhet
* Tillgång till känslig information från ett externt nätverk
* Användare med stor påverkan
* Viktiga affärsprogram

Med kontroller för villkorlig åtkomst kan du skapa principer som riktar sig till specifika användningsfall inom organisationen utan att påverka alla användare.

Innan du dyker in i information om hur du konfigurerar principen ska vi undersöka standardkonfigurationen.

## <a name="user-sign-in-frequency"></a>Inloggningsfrekvens för användare

Inloggningsfrekvensen definierar tidsperioden innan en användare uppmanas att logga in igen när han eller hon försöker komma åt en resurs.

Standardkonfigurationen för Azure Active Directory (Azure AD) för användarinloggningsfrekvens är ett rullande fönster på 90 dagar. Be användare om autentiseringsuppgifter ofta verkar vara en vettig sak att göra, men det kan slå tillbaka: användare som är utbildade för att ange sina autentiseringsuppgifter utan att tänka kan oavsiktligt leverera dem till en skadlig autentiseringsuppgifter prompt.

Det kan låta oroväckande att inte be om en användare att logga in igen, i verkligheten någon överträdelse av IT-principer kommer att återkalla sessionen. Några exempel är (men är inte begränsade till) en lösenordsändring, en inkompatibel enhet eller kontoaktivering. Du kan också uttryckligen [återkalla användarnas sessioner med PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Standardkonfigurationen för Azure AD beror på "be inte användarna att ange sina autentiseringsuppgifter om säkerhetspositionen för deras sessioner inte har ändrats".

Inställningen för inloggningsfrekvens fungerar med appar som har implementerat OAUTH2- eller OIDC-protokoll enligt standarderna. De flesta inbyggda Microsoft-appar för Windows, Mac och Mobile, inklusive följande webbprogram, följer inställningen.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- O365 Admin portal
- exchange online
- SharePoint och OneDrive
- Team webbklient
- Dynamics CRM Online
- Azure Portal

### <a name="user-sign-in-frequency-and-device-identities"></a>Användarens inloggningsfrekvens och enhetsidentiteter

Om du har Azure AD-ansluten, hybrid Azure AD-ansluten eller Azure AD-registrerade enheter, när en användare låser upp sin enhet eller loggar in interaktivt, kommer den här händelsen att uppfylla principen för inloggningsfrekvens också. I följande 2 exempel är användarloggningsfrekvensen inställd på 1 timme:

Exempel 1:

- Klockan 00:00 loggar en användare in på sin Windows 10 Azure AD-anslutna enhet och börjar arbeta med ett dokument som lagras på SharePoint Online.
- Användaren fortsätter att arbeta med samma dokument på sin enhet i en timme.
- Klockan 01:00 uppmanas användaren att logga in igen baserat på inloggningsfrekvenskravet i principen villkorlig åtkomst som konfigurerats av administratören.

Exempel 2:

- Klockan 00:00 loggar en användare in på sin Windows 10 Azure AD-anslutna enhet och börjar arbeta med ett dokument som lagras på SharePoint Online.
- Vid 00:30, användaren stiger upp och tar en paus låsa sin enhet.
- Klockan 00:45 återvänder användaren från rasten och låser upp enheten.
- Klockan 01:45 uppmanas användaren att logga in igen baserat på inloggningsfrekvenskravet i principen villkorlig åtkomst som konfigurerats av administratören sedan den senaste inloggningen inträffade klockan 00:45.

## <a name="persistence-of-browsing-sessions"></a>Persistens av webbläsarsessioner

En beständig webbläsarsession gör det möjligt för användare att förbli inloggade efter att ha stängt och öppnat sitt webbläsarfönster.

Azure AD-standard för webbläsarsessionsbeständighet gör det möjligt för användare på personliga enheter att välja om de vill behålla sessionen genom att visa en "Håll dig inloggad?" efter lyckad autentisering. Om webbläsarens persistens är konfigurerad i AD FS med hjälp av vägledningen i artikeln [AD FS Single Sign-On Settings](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)följer vi den principen och behåller Azure AD-sessionen också. Du kan också konfigurera om användare i din klientorganisation ska se "Håll dig inloggad?" genom att ändra lämplig inställning i företagets varumärkesfönster i Azure-portalen med hjälp av vägledningen i artikeln [Anpassa din Azure AD-inloggningssida](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Konfigurera kontroller för autentiseringssession

Villkorlig åtkomst är en Azure AD Premium-funktion och kräver en premiumlicens. Om du vill veta mer om villkorlig åtkomst läser du [Vad är villkorlig åtkomst i Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Om du använder den [konfigurerbara tokenlivstidsfunktionen](../develop/active-directory-configurable-token-lifetimes.md) som för närvarande är i offentlig förhandsversion kan du tänka på att vi inte stöder att skapa två olika principer för samma användar- eller appkombination: en med den här funktionen och en annan med konfigurerbar tokenlivstidsfunktion. Microsoft planerar att dra tillbaka den konfigurerbara tokenlivslängdsfunktionen den 1 maj 2020 och ersätta den med autentiseringshanteringsfunktionen för villkorlig åtkomst.  

### <a name="policy-1-sign-in-frequency-control"></a>Policy 1: Incheckningsfrekvenskontroll

1. Skapa ny princip
1. Välj alla nödvändiga villkor för kundens miljö, inklusive målmolnapparna.

   > [!NOTE]
   > Vi rekommenderar att du anger samma autentiseringsfrågafrekvens för viktiga Microsoft Office-appar som Exchange Online och SharePoint Online för bästa användarupplevelse.

1. Gå till **Access** > **Controls-sessionen** och klicka på **Inloggningsfrekvens**
1. Ange önskat värde för dagar och timmar i den första textrutan
1. Välj ett värde **för timmar** eller **dagar** i listrutan
1. Spara din policy

![Princip för villkorlig åtkomst konfigurerad för inloggningsfrekvens](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

På Azure AD-registrerade Windows-enheter logga in på enheten betraktas som en fråga. Om du till exempel har konfigurerat inloggningsfrekvensen till 24 timmar för Office-appar uppfyller användare på Azure AD-registrerade Windows-enheter principen Logga in genom att logga in på enheten och kommer inte att uppmanas igen när de öppnar Office-appar.

Om du har konfigurerat olika inloggningsfrekvens för olika webbappar som körs i samma webbläsarsession tillämpas den strängaste principen på båda apparna eftersom alla appar som körs i samma webbläsarsession delar en enda sessionstoken.

### <a name="policy-2-persistent-browser-session"></a>Princip 2: Beständig webbläsarsession

1. Skapa ny princip
1. Välj alla nödvändiga villkor.

   > [!NOTE]
   > Observera att den här kontrollen kräver att du väljer "Alla molnappar" som ett villkor. Webbläsarsessionens persistens styrs av autentiseringssessionstoken. Alla flikar i en webbläsarsession delar en enda sessionstoken och därför måste alla dela persistenstillstånd.

1. Gå till **Åtkomstkontrollsession** > **Session** och klicka på **Beständig webbläsarsession**
1. Välj ett värde i listrutan
1. Spara din princip

![Princip för villkorlig åtkomst konfigurerad för beständig webbläsare](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Beständig konfiguration av webbläsarsessioner i Azure AD Villkorlig åtkomst skriver över "Håll dig inloggad?" inställningen i företagets varumärkesfönster i Azure-portalen för samma användare om du har konfigurerat båda principerna.

## <a name="validation"></a>Validering

Använd verktyget Vad händer om för att simulera en inloggning från användaren till målprogrammet och andra villkor baserat på hur du konfigurerade principen. Kontrollerna för hantering av autentiseringssessioner visas i resultatet av verktyget.

![Villkorad åtkomst Vad händer om verktygsresultat](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Distribution av princip

För att se till att din princip fungerar som förväntat är den rekommenderade bästa metoden att testa den innan du distribuerar den till produktion. Använd helst en testklient för att kontrollera om den nya principen fungerar som avsett. Mer information finns i artikeln [Metodtips för villkorlig åtkomst i Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Nästa steg

* Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst läser du artikeln [Kräv MFA för specifika appar med Azure Active Directory Villkorlig åtkomst](app-based-mfa.md).
* Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö läser du artikeln [Metodtips för villkorlig åtkomst i Azure Active Directory](best-practices.md).
