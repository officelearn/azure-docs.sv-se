---
title: 'Azure AD Connect: Sömlös enkel inloggning – så fungerar det | Microsoft-dokument'
description: I den här artikeln beskrivs hur Azure Active Directory Seamless Single Sign-On-funktionen fungerar.
services: active-directory
keywords: vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4743bc38c3b2b4b9495b33535b4b73f48d1372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71176668"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory Sömlös enkel inloggning: Teknisk djupdykning

Den här artikeln ger dig teknisk information om hur Azure Active Directory Seamless Single Sign-On (Seamless SSO) fungerar.

## <a name="how-does-seamless-sso-work"></a>Hur fungerar Seamless SSO?

Detta avsnitt har tre delar till det:

1. Installationen av Seamless SSO-funktionen.
2. Hur en enskild användares inloggningstransaktion i en webbläsare fungerar med Seamless SSO.
3. Hur en enda användares inloggningstransaktion på en inbyggd klient fungerar med Seamless SSO.

### <a name="how-does-set-up-work"></a>Hur fungerar det?

Sömlös SSO är aktiverat med Azure AD Connect som visas [här](how-to-connect-sso-quick-start.md). När funktionen aktiveras uppstår följande steg:

- Ett datorkonto`AZUREADSSOACC`( ) skapas i din lokala Active Directory (AD) i varje AD-skog som du synkroniserar med Azure AD (med Azure AD Connect).
- Dessutom skapas ett antal Kerberos-tjänsthuvudnamn (SPN) för att användas under Azure AD-inloggningsprocessen.
- Datorkontots Kerberos-dekrypteringsnyckel delas säkert med Azure AD. Om det finns flera AD-skogar har varje datorkonto en egen unik Kerberos-dekrypteringsnyckel.

>[!IMPORTANT]
> Datorkontot `AZUREADSSOACC` måste skyddas starkt av säkerhetsskäl. Endast domänadministratörer bör kunna hantera datorkontot. Kontrollera att Kerberos-delegering på datorkontot är inaktiverat och att `AZUREADSSOACC` inget annat konto i Active Directory har delegeringsbehörighet för datorkontot.. Lagra datorkontot i en organisationsenhet (OU) där de är säkra från oavsiktliga borttagningar och där endast domänadministratörer har åtkomst. Kerberos-dekrypteringsnyckeln på datorkontot bör också behandlas som känslig. Vi rekommenderar starkt att du [rullar över Kerberos dekrypteringsnyckeln](how-to-connect-sso-faq.md) för `AZUREADSSOACC` datorkontot minst var 30:e dag.

När uppsättningen är klar fungerar Seamless SSO på samma sätt som alla andra inloggningar som använder integrerad Windows-autentisering (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Hur fungerar inloggning i en webbläsare med Seamless SSO?

Inloggningsflödet i en webbläsare är följande:

1. Användaren försöker komma åt ett webbprogram (till exempel https://outlook.office365.com/owa/) Outlook Web App – från en domänansluten företagsenhet i företagets nätverk.
2. Om användaren inte redan är inloggad omdirigeras användaren till inloggningssidan för Azure AD.
3. Användaren skriver i sitt användarnamn på inloggningssidan för Azure AD.

   >[!NOTE]
   >För [vissa program](./how-to-connect-sso-faq.md)hoppas steg 2 & 3 över.

4. Med JavaScript i bakgrunden utmanar Azure AD webbläsaren, via ett 401 Obehörigt svar, för att tillhandahålla en Kerberos-biljett.
5. Webbläsaren begär i sin tur en biljett `AZUREADSSOACC` från Active Directory för datorkontot (som representerar Azure AD).
6. Active Directory lokaliserar datorkontot och returnerar en Kerberos-biljett till webbläsaren krypterad med datorkontots hemlighet.
7. Webbläsaren vidarebefordrar Kerberos-biljetten som den har köpt från Active Directory till Azure AD.
8. Azure AD dekrypterar Kerberos-biljetten, som innehåller identiteten på den användare som är inloggad på företagsenheten med den tidigare delade nyckeln.
9. Efter utvärderingen returnerar Azure AD antingen en token tillbaka till programmet eller ber användaren att utföra ytterligare korrektur, till exempel multifaktorautentisering.
10. Om användarloggningen lyckas kan användaren komma åt programmet.

Följande diagram illustrerar alla komponenter och steg inblandade.

![Sömlös enkel inloggning – webbappflöde](./media/how-to-connect-sso-how-it-works/sso2.png)

Sömlös SSO är opportunistisk, vilket innebär att om den misslyckas, faller inloggningsupplevelsen tillbaka till sitt vanliga beteende - dvs användaren måste ange sitt lösenord för att logga in.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Hur fungerar inloggning på en inbyggd klient med Sömlös SSO?

Inloggningsflödet för en inbyggd klient är följande:

1. Användaren försöker komma åt ett inbyggt program (till exempel Outlook-klienten) från en domänansluten företagsenhet i företagets nätverk.
2. Om användaren inte redan är inloggad hämtar det inbyggda programmet användarens användarnamn från enhetens Windows-session.
3. Appen skickar användarnamnet till Azure AD och hämtar klientens WS-Trust MEX-slutpunkt. Den här WS-Trust-slutpunkten används uteslutande av seamless SSO-funktionen och är inte en allmän implementering av WS-Trust-protokollet på Azure AD.
4. Appen frågar sedan WS-Trust MEX-slutpunkten för att se om integrerad autentiseringslutpunkt är tillgänglig. Slutpunkten för integrerad autentisering används uteslutande av seamless SSO-funktionen.
5. Om steg 4 lyckas utfärdas en Kerberos-utmaning.
6. Om appen kan hämta Kerberos-biljetten vidarebefordras den till Azure AD:s integrerade autentiseringsslutpunkt.
7. Azure AD dekrypterar Kerberos-biljetten och validerar den.
8. Azure AD loggar in användaren och utfärdar en SAML-token till appen.
9. Appen skickar sedan SAML-token till Azure AD:s OAuth2-tokenslutpunkt.
10. Azure AD validerar SAML-token och utfärdar till appen en åtkomsttoken och en uppdateringstoken för den angivna resursen och en id-token.
11. Användaren får åtkomst till appens resurs.

Följande diagram illustrerar alla komponenter och steg inblandade.

![Sömlös enkel inloggning – inbyggt appflöde](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart**](how-to-connect-sso-quick-start.md) – kom igång med Azure AD Seamless SSO.
- [**Vanliga frågor**](how-to-connect-sso-faq.md) och svar - Svar på vanliga frågor.
- [**Felsöka**](tshoot-connect-sso.md) - Lär dig hur du löser vanliga problem med funktionen.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - För att lämna in nya funktionsförfrågningar.
