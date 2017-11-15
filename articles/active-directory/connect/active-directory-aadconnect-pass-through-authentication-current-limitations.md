---
title: "Azure AD Connect: Direkt autentisering - aktuella begränsningar | Microsoft Docs"
description: "Den här artikeln beskriver aktuella begränsningar i Azure Active Directory (AD Azure) direkt-autentisering."
services: active-directory
keywords: "Azure AD Connect direkt-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 4a33df43ca218545d6c684103a64f2cd1460913b
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory direkt-autentisering: Aktuella begränsningar

>[!IMPORTANT]
>Azure AD direkt-autentisering är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den. Direkt-autentisering är endast tillgängligt i hela världen instans av Azure AD och inte på [Microsoft Cloud Tyskland](http://www.microsoft.de/cloud-deutschland) och [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scenarier som stöds

Följande scenarier stöds fullt ut:

- Användarinloggningar i alla webbläsarbaserad webbprogram.
- Användarinloggningar i Office 365-klientprogram som stöder [modern autentisering](https://aka.ms/modernauthga) -Office 2016 och Office 2013 _med_ modern autentisering.
- Azure AD-anslutning för Windows 10-enheter.
- Stöd för Exchange ActiveSync.

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier är _inte_ stöds:

- Användarinloggningar till äldre Office-klientprogram - Office 2010 och Office 2013 _utan_ modern autentisering). Organisationer uppmuntras att växla till modern autentisering, om möjligt. Modern autentisering tillåter stöd för direkt-autentisering, men också kan du skydda din användare användarkonton med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) funktioner, till exempel Multi-Factor Authentication (MFA).
- Användarinloggningar till Skype för företag-klientprogram, inklusive Skype för företag 2016.
- Användarinloggningar i PowerShell v1.0. Det rekommenderas att du använder PowerShell version 2.0 i stället.
- Azure AD Domain Services.
- Applösenord för MFA.
- Identifiering av användare med [läcka ut autentiseringsuppgifter](../active-directory-reporting-risk-events.md#leaked-credentials).

>[!IMPORTANT]
>Som en lösning för scenarier som inte stöds _endast_, aktivera synkronisering av lösenords-Hash för den [valfria funktioner](active-directory-aadconnect-get-started-custom.md#optional-features) sida i Azure AD Connect-guiden. Aktivera synkronisering av lösenords-Hash ger dig också alternativet att redundans-autentisering om din lokala infrastruktur avbryts helt. Den här redundansen från direkt autentisering till hash-Lösenordssynkronisering sker inte automatiskt, men har att göra med hjälp av Microsoft-supporten.

## <a name="next-steps"></a>Nästa steg
- [**Snabbstart** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) – komma igång och körs direkt i Azure AD-autentisering.
- [**Smartkort kontoutelåsning** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -konfigurerar Smart kontoutelåsning kapaciteten på din klient för att skydda användarkonton.
- [**Tekniska ingående** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -förstå hur funktionen fungerar.
- [**Vanliga frågor och svar** ](active-directory-aadconnect-pass-through-authentication-faq.md) -svar på vanliga frågor och svar.
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Lär dig att lösa vanliga problem med funktionen.
- [**Säkerhet ingående** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -ytterligare djupt teknisk information om funktionen.
- [**Azure AD sömlös SSO** ](active-directory-aadconnect-sso.md) -mer information om den här funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för arkivering av nya funktioner som efterfrågas.
