---
title: 'Azure AD Connect: Direktautentisering - aktuella begränsningar | Microsoft Docs'
description: Den här artikeln beskriver de aktuella begränsningarna för Azure Active Directory (Azure AD)-direktautentisering
services: active-directory
keywords: Azure AD Connect direktautentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 34b83c54e31ed73af3f776a6add8f218dda35cf7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918928"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory-direktautentisering: Aktuella begränsningar

>[!IMPORTANT]
>Azure Active Directory (Azure AD)-direktautentisering är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den. Direktautentisering är endast tillgänglig i hela världen instans av Azure AD och inte på den [Microsoft Azure Tyskland](http://www.microsoft.de/cloud-deutschland) eller [Microsoft Azure Government-molnet](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scenarier som stöds

Följande scenarier stöds fullt ut:

- Användarinloggningar till alla webbläsarbaserade webbprogram.
- Användarinloggningar till Office-program som stöder [modern autentisering](https://aka.ms/modernauthga): Office 2016 och Office 2013 _med_ modern autentisering.
- Användarinloggningar till Outlook-klienter med äldre protokoll, till exempel Exchange ActiveSync-, SMTP-, POP- och IMAP.
- Användarinloggningar till Skype för företag som stöd för modern autentisering, inklusive online och hybridtopologier. Mer information om topologier som stöds [här](https://technet.microsoft.com/library/mt803262.aspx).
- Azure AD-domän som kopplar ihop för Windows 10-enheter.
- Applösenord för Multifaktorautentisering.

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier är _inte_ stöds:

- Användarinloggningar till äldre Office-program, exklusive Outlook (se **stöds scenarier** ovan): Office 2010 och Office 2013 _utan_ modern autentisering. Organisationer uppmuntras att växla till modern autentisering, om det är möjligt. Modern autentisering möjliggör stöd för direktautentisering. Det hjälper dig skydda dina användarkonton med hjälp av också [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) funktioner, till exempel Azure Multi-Factor Authentication.
- Åtkomst till delade kalendrar och ledig eller upptagen i Exchange hybridmiljöer på Office 2010 endast.
- Användarinloggningar till Skype för företag-klientprogram _utan_ modern autentisering.
- Användarinloggningar till PowerShell version 1.0. Vi rekommenderar att du använder PowerShell version 2.0.
- Identifiering av användare med [läcka ut autentiseringsuppgifter](../active-directory-reporting-risk-events.md#leaked-credentials).
- Azure AD Domain Services måste synkronisering av Lösenordshash ska vara aktiverat på klienten. Därför klienter som använder direktautentisering _endast_ fungerar inte för scenarier som behöver Azure AD Domain Services.
- Direktautentisering är inte integrerat med [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).
- I Apples DEP (Apple DEP) med hjälp av Installationsassistenten för iOS har inte stöd för modern autentisering. Detta kommer inte att registrera Apple DEP-enheter i Intune för hanterade domäner med hjälp av direktautentisering. Överväg att använda den [företagsportalappen](https://blogs.technet.microsoft.com/intunesupport/2018/02/08/support-for-multi-token-dep-and-authentication-with-company-portal/) som ett alternativ.

>[!IMPORTANT]
>Som en lösning för scenarier som inte stöds _endast_, aktivera synkronisering av Lösenordshash på den [valfria funktioner](active-directory-aadconnect-get-started-custom.md#optional-features) sida i Azure AD Connect-guiden. När användarna loggar in på program i listan i den ”stöds inte scenarier” avsnittet dessa specifika inloggningsförfrågningar är _inte_ hanteras av direktautentisering Autentiseringsagenter och därför inte registreras i [ Direktautentisering loggar](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs).

>[!NOTE]
Aktiverar synkronisering av lösenordshash ger dig möjlighet att redundans-autentisering om din lokala infrastruktur avbryts. Den här redundansen från direktautentisering till Active Directory synkronisering av lösenordshash sker inte automatiskt. Du måste växla inloggningsmetod manuellt med hjälp av Azure AD Connect. Om den server som kör Azure AD Connect stängs av, kommer du behöver hjälp från Microsoft Support att stänga av direktautentisering.

## <a name="next-steps"></a>Nästa steg
- [Snabbstart](active-directory-aadconnect-pass-through-authentication-quick-start.md): komma igång med Azure AD-direktautentisering.
- [Smart kontoutelåsning](../authentication/howto-password-smart-lockout.md): Lär dig hur du konfigurerar funktionen för smarta kontoutelåsning på din klient för att skydda användarkonton.
- [Teknisk djupdykning](active-directory-aadconnect-pass-through-authentication-how-it-works.md): Förstå hur funktionen direktautentisering fungerar.
- [Vanliga frågor och svar](active-directory-aadconnect-pass-through-authentication-faq.md): få svar på vanliga frågor om direktautentisering-funktionen.
- [Felsöka](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Lär dig att lösa vanliga problem med funktionen direktautentisering.
- [Djupgående om säkerhet](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): få djupgående teknisk information om funktionen direktautentisering.
- [Azure AD sömlös SSO](active-directory-aadconnect-sso.md): Mer information om den här tilläggsfunktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): använda Azure Active Directory-forumet till filen nya funktionbegäran.
