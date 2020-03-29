---
title: Sessionskontroller i principen villkorlig åtkomst - Azure Active Directory
description: Vad är sessionskontroller i en Azure AD-princip för villkorlig åtkomst
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99b9b87f939d614679fdecf24c9d36d99bf2938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671888"
---
# <a name="conditional-access-session"></a>Villkorlig åtkomst: Session

I en princip för villkorlig åtkomst kan en administratör använda sessionskontroller för att aktivera begränsade upplevelser inom specifika molnprogram.

![Princip för villkorlig åtkomst med en bidragskontroll som kräver multifaktorautentisering](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Begränsningar för tillämpad tillämpning

Organisationer kan använda den här kontrollen för att kräva att Azure AD skickar enhetsinformation till de valda molnapparna. Enhetsinformationen gör det möjligt för molnapparna att veta om en anslutning initieras från en kompatibel eller domänansluten enhet. Den här kontrollen stöder endast SharePoint Online och Exchange Online som valda molnappar. När det här alternativet är markerat använder molnappen enhetsinformationen för att ge användarna en begränsad eller fullständig upplevelse, beroende på enhetens tillstånd.

Mer information om användning och konfiguration av appbegränsningar finns i följande artiklar:

- [Aktivera begränsad åtkomst med SharePoint Online](/sharepoint/control-access-from-unmanaged-devices)
- [Aktivera begränsad åtkomst med Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Programkontroll för villkorlig åtkomst

Conditional Access App Control använder en omvänd proxyarkitektur och är unikt integrerad med Azure AD Villkorlig åtkomst. Med Azure AD-villkorlig åtkomst kan du tillämpa åtkomstkontroller för organisationens appar baserat på vissa villkor. Villkoren definierar vem (användare eller grupp av användare) och vilka (vilka molnappar) och var (vilka platser och nätverk) en princip för villkorlig åtkomst tillämpas på. När du har fastställt villkoren kan du dirigera användare till [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) där du kan skydda data med Appkontroll för villkorlig åtkomst genom att använda åtkomst- och sessionskontroller.

Med Appkontroll för villkorlig åtkomst kan åtkomst till användarappar och sessioner övervakas och kontrolleras i realtid baserat på åtkomst- och sessionsprinciper. Åtkomst- och sessionsprinciper används i Cloud App Security-portalen för att ytterligare förfina filter och ange åtgärder som ska vidtas för en användare. Med åtkomst- och sessionsprinciperna kan du:

- Förhindra dataexfiltration: Du kan blockera hämtning, klippning, kopiering och utskrift av känsliga dokument på till exempel ohanterade enheter.
- Skydda vid hämtning: I stället för att blockera hämtningen av känsliga dokument kan du kräva att dokument ska märkas och skyddas med Azure Information Protection. Den här åtgärden säkerställer att dokumentet är skyddat och att användaråtkomsten begränsas i en potentiellt riskfylld session.
- Förhindra uppladdning av omärkta filer: Innan en känslig fil laddas upp, distribueras och används av andra är det viktigt att se till att filen har rätt etikett och skydd. Du kan se till att omärkta filer med känsligt innehåll blockeras från att laddas upp tills användaren klassificerar innehållet.
- Övervaka användarsessioner för efterlevnad: Riskfyllda användare övervakas när de loggar in på appar och deras åtgärder loggas inifrån sessionen. Du kan undersöka och analysera användarbeteende för att förstå var och under vilka villkor sessionsprinciper som ska tillämpas i framtiden.
- Blockera åtkomst: Du kan blockera åtkomst för specifika appar och användare på ett detaljerat sätt beroende på flera riskfaktorer. Du kan till exempel blockera dem om de använder klientcertifikat som en form av enhetshantering.
- Blockera anpassade aktiviteter: Vissa appar har unika scenarier som medför risker, till exempel att skicka meddelanden med känsligt innehåll i appar som Microsoft Teams eller Slack. I den här typen av scenarier kan du söka igenom meddelanden efter känsligt innehåll och blockera dem i realtid.

Mer information finns i artikeln [Distribuera Appkontroll för villkorlig åtkomst för utvalda appar](/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency-preview"></a>Inloggningsfrekvens (förhandsgranskning)

Inloggningsfrekvensen definierar tidsperioden innan en användare uppmanas att logga in igen när han eller hon försöker komma åt en resurs.

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

Mer information finns i artikeln Konfigurera hantering av [autentiseringssessioner med villkorlig åtkomst](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session-preview"></a>Beständig webbläsarsession (förhandsgranskning)

En beständig webbläsarsession gör det möjligt för användare att förbli inloggade efter att ha stängt och öppnat sitt webbläsarfönster.

Mer information finns i artikeln Konfigurera hantering av [autentiseringssessioner med villkorlig åtkomst](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Nästa steg

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

- [Läget Endast rapport](concept-conditional-access-report-only.md)
