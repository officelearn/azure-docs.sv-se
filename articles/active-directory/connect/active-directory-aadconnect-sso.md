---
title: 'Azure AD Connect: Sömlös enkel inloggning | Microsoft Docs'
description: Det här avsnittet beskriver Azure Active Directory (AD Azure) sömlös enkel inloggning och hur du kan ange SANT enkel inloggning för fjärrskrivbord företagsanvändare i företagsnätverket.
services: active-directory
keywords: Vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: billmath
ms.openlocfilehash: b1c82727e97b85fae5f315ceb1cd79cfdd111b45
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory sömlös enkel inloggning

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Vad är Azure Active Directory sömlös enkel inloggning?

Azure Active Directory sömlös enkel inloggning (Azure AD sömlös SSO) automatiskt loggar användarna när de är på sina enheter anslutna till företagsnätverket. När aktiverad behöver användare inte ange sina lösenord för att logga in till Azure AD och normalt även ange sina användarnamn. Den här funktionen ger dina användare enkel åtkomst till dina molnbaserade program utan att behöva ytterligare lokala komponenter.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Sömlös SSO kan kombineras med antingen den [synkronisering av lösenords-hash-](active-directory-aadconnectsync-implement-password-synchronization.md) eller [direkt autentisering](active-directory-aadconnect-pass-through-authentication.md) inloggning metoder.

![Sömlös enkel inloggning](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>Sömlös SSO är _inte_ tillämpliga till Active Directory Federation Services (ADFS).

## <a name="key-benefits"></a>Viktiga fördelar

- *Bättre användarupplevelse*
  - Användarna loggas automatiskt i både lokala och molnbaserade program.
  - Användarna behöver inte ange sina lösenord flera gånger.
- *Enkelt att distribuera och administrera*
  - Inga ytterligare komponenter behövs lokalt till att det ska fungera.
  - Fungerar med valfri metod för autentisering i molnet - [synkronisering av lösenords-hash-](active-directory-aadconnectsync-implement-password-synchronization.md) eller [direkt autentisering](active-directory-aadconnect-pass-through-authentication.md).
  - Kan rullas till vissa eller alla användare med hjälp av en Grupprincip.
  - Registrera Windows 10-enheter med Azure AD utan behov av AD FS-infrastruktur. Den här funktionen måste du använda version 2.1 eller senare av den [till arbetsplatsen klienten](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Funktionen visar

- Logga in användarnamnet kan vara antingen lokalt Standardanvändarnamnet (`userPrincipalName`) eller ett annat attribut som konfigurerats i Azure AD Connect (`Alternate ID`). Båda använder fall arbete eftersom sömlös SSO använder den `securityIdentifier` anspråk i Kerberos-biljetten att leta upp motsvarande användarobjekt i Azure AD.
- Sömlös SSO är en opportunistisk funktion. Om det misslyckas av någon anledning går inloggning för användaren tillbaka till sitt vanliga beteende - dvs, användaren måste ange sina lösenord på sidan logga in.
- Om ett program (till exempel https://myapps.microsoft.com/contoso.com) vidarebefordrar en `domain_hint` (OpenID Connect) eller `whr` (SAML) parameter - identifierar din klient eller `login_hint` parameter - identifierar användaren användare finns i Azure AD-inloggning begäran Du loggas in automatiskt utan dem anger användarnamn eller lösenord.
- Användarna får också en tyst inloggning om ett program (till exempel https://contoso.sharepoint.com) skickar inloggningsförfrågningar till Azure AD-innehavare slutpunkter – det vill säga https://login.microsoftonline.com/contoso.com/<..> eller https://login.microsoftonline.com/<tenant_ID>/<..> – i stället för Azure AD vanliga endpoint – det vill säga https://login.microsoftonline.com/common/<...>.
- Logga ut stöds. Detta ger användarna att välja en annan Azure AD-konto för att logga in med i stället för att automatiskt loggas in automatiskt med sömlös SSO.
- Office 365-klienter (16.0.8730.xxxx och senare) stöds med hjälp av en icke-interaktiv flöde.
- Den kan aktiveras via Azure AD Connect.
- Det är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den.
- Den stöds på web webbläsarbaserad och Office-klienter som stöder [modern autentisering](https://aka.ms/modernauthga) på plattformar och webbläsare som stöder Kerberos-autentisering:

| OS\Browser |Internet Explorer|Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ja|Nej|Ja|Ja\*|Gäller inte
|Windows 8.1|Ja|Gäller inte|Ja|Ja\*|Gäller inte
|Windows 8|Ja|Gäller inte|Ja|Ja\*|Gäller inte
|Windows 7|Ja|Gäller inte|Ja|Ja\*|Gäller inte
|Mac OS X|Gäller inte|Gäller inte|Ja\*|Ja\*|Ja\*

\*Kräver [ytterligare konfiguration](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>För Windows 10, rekommenderar vi att du använder [Azure AD Join](../active-directory-azureadjoin-overview.md) för den optimala enkel inloggning med Azure AD.

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart** ](active-directory-aadconnect-sso-quick-start.md) – få och kör Azure AD sömlös SSO.
- [**Tekniska ingående** ](active-directory-aadconnect-sso-how-it-works.md) -förstå hur funktionen fungerar.
- [**Vanliga frågor och svar** ](active-directory-aadconnect-sso-faq.md) -svar på vanliga frågor och svar.
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-sso.md) – Lär dig att lösa vanliga problem med funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för arkivering av nya funktioner som efterfrågas.
