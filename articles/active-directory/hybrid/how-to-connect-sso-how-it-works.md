---
title: 'Azure AD Connect: Sömlös enkel inloggning – hur det fungerar | Microsoft Docs'
description: Den här artikeln beskrivs hur funktionen Azure Active Directory sömlös enkel inloggning fungerar.
services: active-directory
keywords: Vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 0d99bf8c869e60f9bcfaab96306807852676caa8
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54476129"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory sömlös enkel inloggning: Teknisk djupdykning

Den här artikeln ger teknisk information till hur Azure Active Directory sömlös enkel inloggning (sömlös SSO)-funktionen fungerar.

## <a name="how-does-seamless-sso-work"></a>Hur fungerar sömlös enkel inloggning?

Det här avsnittet har tre delar:

1. Inställningarna för sömlös SSO-funktionen.
2. Så här fungerar en enskild användare logga in transaktion i en webbläsare med sömlös enkel inloggning.
3. Så här fungerar en enskild användare logga in transaktion på en intern klient med sömlös enkel inloggning.

### <a name="how-does-set-up-work"></a>Hur ställer in arbeta?

Sömlös enkel inloggning har aktiverats med Azure AD Connect enligt [här](how-to-connect-sso-quick-start.md). När du aktiverar funktionen genomförs följande steg:

- Ett datorkonto med namnet `AZUREADSSOACC` (som representerar Azure AD) skapas i din lokala Active Directory (AD) i varje AD-skog.
- Krypteringsnyckel för det datorkonto Kerberos delas på ett säkert sätt med Azure AD. Om det finns flera AD-skogar, har var och en sin egen nyckel för dekryptering av Kerberos.
- Dessutom skapas två Kerberos tjänsternas huvudnamn (SPN) för att representera två URL: er som används under Azure AD-inloggningen.

>[!NOTE]
> Datorkontot och de Kerberos SPN skapas i varje AD-skog du synkroniserar till Azure AD (med Azure AD Connect) och vars användare du vill ha sömlös enkel inloggning. Flytta den `AZUREADSSOACC` datorkonto till en organisation enhet (OU) där andra konton lagras så att den hanteras på samma sätt och inte tas bort.

>[!IMPORTANT]
>Vi rekommenderar starkt att som du [förnyar Kerberos-krypteringsnyckel](how-to-connect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) av den `AZUREADSSOACC` datorkontot minst var 30: e dag.

När inställningarna har slutförts, fungerar sömlös SSO på samma sätt som alla andra logga in som använder integrerad Windows autentisering (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Hur logga in i en webbläsare med sömlös SSO arbete?

Logga in flödet i en webbläsare är följande:

1. Användaren försöker få åtkomst till ett webbprogram (till exempel Outlook Web App - https://outlook.office365.com/owa/) från en domänansluten företagsenheter i företagsnätverket.
2. Om du inte redan har signerats omdirigeras användaren till inloggningssidan för Azure AD.
3. Användaren skriver sitt användarnamn till inloggningssidan för Azure AD.

   >[!NOTE]
   >För [vissa program](./how-to-connect-sso-faq.md#what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso), steg 2 och 3 hoppas över.

4. Azure AD anropar med hjälp av JavaScript i bakgrunden, webbläsaren, via ett 401, obehörig svar, att tillhandahålla en Kerberos-biljett.
5. Webbläsaren kan i sin tur begär en biljett från Active Directory för den `AZUREADSSOACC` datorkontot (som representerar Azure AD).
6. Active Directory hittar datorkontot och returnerar en Kerberos-biljett till webbläsaren som krypterats med det datorkonto hemlighet.
7. Webbläsaren vidarebefordrar Kerberos-biljetten som det fått från Active Directory till Azure AD.
8. Azure AD dekrypterar Kerberos-biljetten som innehåller identiteten för den användare som loggat in på företagets enheter med hjälp av den tidigare delade nyckeln.
9. Efter utvärdering, att Azure AD antingen returnerar en token tillbaka till programmet eller uppmanar användaren att utföra ytterligare korrektur, till exempel Multi-Factor Authentication.
10. Om användaren-inloggningen lyckas kan användaren komma åt programmet.

Följande diagram illustrerar komponenterna och steg som ingår.

![Sömlös enkel inloggning på – Web app-flöde](./media/how-to-connect-sso-how-it-works/sso2.png)

Sömlös SSO är opportunistisk, vilket innebär att om det misslyckas inloggningen går faller tillbaka till dess vanligt beteende - dvs, användaren måste ange sitt lösenord för inloggning.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Hur logga in på en intern klient med sömlös SSO arbete?

Flödet logga in på en intern klient är följande:

1. Användaren försöker få åtkomst till ett internt program (till exempel Outlook-klienten) från en domänansluten företagsenheter i företagsnätverket.
2. Om du inte redan har signerats hämtar internt program användarnamnet för användaren från enhetens Windows-sessionen.
3. Appen skickar användarnamnet till Azure AD och hämtar WS-Trust MEX klientorganisationsslutpunkt. Den här WS-Trust-slutpunkten används uteslutande av funktionen sömlös SSO och är inte en allmän implementering av WS-Trust-protokollet på Azure AD.
4. Appen därefter beordrar WS-Trust MEX-slutpunkt för att se om integrerad autentisering slutpunkten är tillgänglig. Integrerad autentisering-slutpunkten används uteslutande av sömlös SSO-funktionen.
5. Om steg 4 lyckas, utfärdas en Kerberos-utmaning.
6. Om appen är att hämta Kerberos-biljetten, vidarebefordrar det upp till Azure AD-integrerad autentisering-slutpunkten.
7. Azure AD dekrypterar Kerberos-biljetten och validerar den.
8. Loggar in användaren i Azure AD och utfärdar en SAML-token till appen.
9. Appen skickar sedan SAML-token till Azure Active Directorys OAuth2 token för slutpunkt.
10. Azure AD verifierar SAML-token och skickar till appen en åtkomsttoken och en uppdateringstoken för den angivna resursen och ett id-token.
11. Användaren får åtkomst till appens resurs.

Följande diagram illustrerar komponenterna och steg som ingår.

![Sömlös enkel inloggning – inbyggd app-flöde](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart** ](how-to-connect-sso-quick-start.md) – komma igång och köra sömlös enkel inloggning för den Azure AD.
- [**Vanliga frågor och svar** ](how-to-connect-sso-faq.md) -svar på vanliga frågor och svar.
- [**Felsöka** ](tshoot-connect-sso.md) – Lär dig att lösa vanliga problem med funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att skicka in nya funktionbegäran.
