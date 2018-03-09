---
title: "Azure AD Connect: Sömlös Single Sign-On - hur det fungerar | Microsoft Docs"
description: "Den här artikeln beskriver hur Azure Active Directory sömlös Single Sign-On-funktionen fungerar."
services: active-directory
keywords: "Vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: 9d17a4038f2171b74c8ba1dbc21e8335e6893691
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory sömlös enkel inloggning: tekniska ingående

Den här artikeln får du teknisk information till hur funktionen Azure Active Directory sömlös enkel inloggning (SSO sömlös) fungerar.

## <a name="how-does-seamless-sso-work"></a>Hur fungerar sömlös SSO?

Det här avsnittet har tre delar:
1. Inställningarna för sömlös SSO-funktionen.
2. Hur fungerar en enskild användare logga in transaktion i en webbläsare med sömlös SSO.
3. Hur fungerar en enskild användare logga in transaktion på en intern klient med sömlös SSO.

### <a name="how-does-set-up-work"></a>Hur ställa in arbete?

Sömlös SSO aktiveras med Azure AD Connect enligt [här](active-directory-aadconnect-sso-quick-start.md). När du aktiverar funktionen utförs följande steg:
- Ett konto med namnet `AZUREADSSOACC` (som motsvarar Azure AD) skapas i din lokala Active Directory (AD).
- Krypteringsnyckel för det datorkonto Kerberos delas på ett säkert sätt med Azure AD.
- Dessutom skapas två Kerberos tjänstens huvudnamn (SPN) för att representera två URL: er som används under Azure AD-inloggning.

>[!NOTE]
> Datorkonto och Kerberos SPN skapas i varje AD-skog som synkroniseras till Azure AD (med Azure AD Connect) och vars användare du vill ha sömlös SSO. Flytta den `AZUREADSSOACC` datorkonto till en organisationsenhet (OU) där andra datorkonton lagras så att den hanteras på samma sätt och tas inte bort.

>[!IMPORTANT]
>Hög rekommenderar vi att du [rulla över krypteringsnyckel Kerberos](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) av den `AZUREADSSOACC` datorkonto minst var 30 dagar.

När inställningen är klar fungerar sömlös SSO på samma sätt som alla andra inloggning som använder integrerad Windows Windowsautentisering (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Hur logga in i en webbläsare med sömlös SSO arbete?

Flödet i en webbläsare är följande:

1. Användaren försöker komma åt ett webbprogram (till exempel Outlook Web App - https://outlook.office365.com/owa/) från en domänansluten företagets enheter i företagsnätverket.
2. Om användaren inte redan har signerats omdirigeras användaren till sidan för Azure AD.
3. Användaren skriver sitt användarnamn till inloggningssidan för Azure AD.

  >[!NOTE]
  >För [vissa program](./active-directory-aadconnect-sso-faq.md#what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso), steg 2 och 3 hoppas över.

4. Azure AD anropar med hjälp av JavaScript i bakgrunden, webbläsaren via ett 401 obehörig svar att tillhandahålla en Kerberos-biljett.
5. Webbläsaren kan i sin tur begär en tjänstbiljett från Active Directory för den `AZUREADSSOACC` datorkontot (som representerar Azure AD).
6. Active Directory hittar datorkontot och returnerar en Kerberos-biljett till webbläsaren som krypterats med det datorkonto hemlighet.
7. Webbläsaren vidarebefordrar Kerberos-biljetten som hämtades från Active Directory till Azure AD.
8. Azure AD dekrypterar Kerberos-biljett, som innehåller identiteten hos användaren loggat in på företagets enheter med tidigare delad nyckel.
9. Efter utvärderingen, Azure AD returnerar en token tillbaka till programmet eller ombeds användaren att utföra ytterligare bevis, till exempel Multi-Factor Authentication.
10. Om användaren loggar in lyckas kan användaren komma åt programmet.

Följande diagram illustrerar alla komponenter och steg som ingår.

![Sömlös enkel inloggning på - Web app-flöde](./media/active-directory-aadconnect-sso/sso2.png)

Sömlös SSO är opportunistisk, vilket innebär att om det misslyckas inloggningen faller tillbaka till sitt vanliga beteende - dvs, användaren måste ange sitt lösenord för inloggning.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Hur logga in på en intern klient med sömlös SSO arbete?

Flödet på en intern klient är följande:

1. Användaren försöker komma åt ett program (till exempel Outlook-klienten) som skapades från en domänansluten företagets enheter i företagsnätverket.
2. Om användaren inte redan har signerats hämtar det ursprungliga programmet användarnamnet för användaren från enhetens Windows-sessioner.
3. Appen skickar användarnamnet till Azure AD och hämtar din klient WS-Trust MEX-slutpunkt.
4. Appen frågar sedan WS-Trust MEX-slutpunkt för att se om integrerad autentisering slutpunkt är tillgänglig.
5. Om steg 4 lyckas, utfärdas en Kerberos-utmaning.
6. Om appen är att hämta Kerberos-biljetten vidarebefordrar det upp till Azure AD-integrerad autentisering slutpunkt.
7. Azure AD dekrypterar Kerberos-biljetten och validerar den.
8. Loggar användaren i Azure AD och utfärdar en SAML-token till appen.
9. Appen skickar sedan SAML-token till Azure AD OAuth2-token för slutpunkt.
10. Azure AD validerar SAML-token och skickar till appen med en åtkomst-token och en uppdateringstoken för den angivna resursen en id-token.
11. Användaren får åtkomst till appens resurs.

Följande diagram illustrerar alla komponenter och steg som ingår.

![Sömlös enkel inloggning - inbyggd app-flöde](./media/active-directory-aadconnect-sso/sso14.png)

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart** ](active-directory-aadconnect-sso-quick-start.md) – få och kör Azure AD sömlös SSO.
- [**Vanliga frågor och svar** ](active-directory-aadconnect-sso-faq.md) -svar på vanliga frågor och svar.
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-sso.md) – Lär dig att lösa vanliga problem med funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för arkivering av nya funktioner som efterfrågas.
