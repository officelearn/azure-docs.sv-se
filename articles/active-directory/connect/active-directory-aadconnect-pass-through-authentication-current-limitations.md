---
title: 'Azure AD Connect: Direkt autentisering - aktuella begränsningar | Microsoft Docs'
description: Den här artikeln beskriver aktuella begränsningar i Azure Active Directory (AD Azure) direkt-autentisering
services: active-directory
keywords: Azure AD Connect direkt-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on
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
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ebfbb972d567963b6c302b7e6151f73165c4a87b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590682"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory direkt-autentisering: Aktuella begränsningar

>[!IMPORTANT]
>Azure Active Directory (AD Azure) direkt-autentisering är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den. Direkt-autentisering är endast tillgängligt i hela världen instans av Azure AD och inte på den [Tyskland för Microsoft Azure-molnet](http://www.microsoft.de/cloud-deutschland) eller [Microsoft Azure Government molnet](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scenarier som stöds

Följande scenarier stöds fullt ut:

- Användarinloggningar webbläsarbaserade program för alla webbprogram.
- Användarinloggningar till Office-program som stöder [modern autentisering](https://aka.ms/modernauthga): Office 2016 och Office 2013 _med_ modern autentisering.
- Användarinloggningar för Outlook-klienter med äldre protokoll, till exempel Exchange ActiveSync-, SMTP-, POP- och IMAP.
- Användarinloggningar till Skype för företag som stöd för modern autentisering, inklusive online och hybridtopologier. Mer information om topologier [här](https://technet.microsoft.com/library/mt803262.aspx).
- Azure AD-domän som kopplar ihop för Windows 10-enheter.
- Applösenord för Multifaktorautentisering.

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier är _inte_ stöds:

- Användarinloggningar till äldre Office-program, exklusive Outlook (se **stöds scenarier** ovan): Office 2010 och Office 2013 _utan_ modern autentisering. Organisationer uppmuntras att växla till modern autentisering, om möjligt. Modern autentisering tillåter stöd för direkt-autentisering. Du kan också skydda dina användarkonton med hjälp av [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) funktioner, till exempel Azure Multi-Factor Authentication.
- Åtkomst till delade kalendrar och ledig/upptagen-information i Exchange hybridmiljöer Office 2010 endast.
- Användarinloggningar till Skype för företag-klientprogram _utan_ modern autentisering.
- Användarinloggningar till PowerShell version 1.0. Vi rekommenderar att du använder PowerShell version 2.0.
- Identifiering av användare med [läcka ut autentiseringsuppgifter](../active-directory-reporting-risk-events.md#leaked-credentials).
- Azure AD Domain Services måste synkronisering av lösenords-Hash måste vara aktiverat på klienten. Därför klienter som använder direkt autentisering _endast_ fungerar inte för scenarier som kräver Azure AD Domain Services.
- Direkt-autentisering inte är integrerad med [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).
- Den Apple Device Enrollment Program (Apple DEP) med hjälp av Installationsassistenten för iOS har inte stöd för modern autentisering. Detta kan inte registrera Apple DEP-enheter i Intune för hanterade domäner som använder direkt-autentisering. Överväg att använda den [företagsportalappen](https://blogs.technet.microsoft.com/intunesupport/2018/02/08/support-for-multi-token-dep-and-authentication-with-company-portal/) som ett alternativ.

>[!IMPORTANT]
>Som en lösning för scenarier som inte stöds _endast_, aktivera synkronisering av lösenords-Hash för den [valfria funktioner](active-directory-aadconnect-get-started-custom.md#optional-features) sida i Azure AD Connect-guiden. När användare logga in på program som anges i den ”stöds inte scenarier” avsnittet, är dessa begäranden för specifika inloggning _inte_ hanteras av direkt autentisering agenter och därför inte registreras i [ Direkt-autentisering loggar](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs).

>[!NOTE]
Aktivera synkronisering av lösenords-hash kan du välja att redundans-autentisering om din lokala infrastruktur avbryts. Den här redundansen från direkt autentisering för Active Directory-lösenord hash-synkronisering sker inte automatiskt. Du måste växla inloggningsmetod manuellt med hjälp av Azure AD Connect. Om den server som kör Azure AD Connect kraschar, ska du behöver hjälp från Microsoft Support om du vill inaktivera direkt-autentisering.

## <a name="next-steps"></a>Nästa steg
- [Snabbstart](active-directory-aadconnect-pass-through-authentication-quick-start.md): komma igång med Azure AD direkt-autentisering.
- [Smartkort kontoutelåsning](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): Lär dig hur du konfigurerar Smart kontoutelåsning kapaciteten på din klient skydda användarkonton.
- [Tekniska ingående](active-directory-aadconnect-pass-through-authentication-how-it-works.md): Förstå hur funktionen direkt autentisering fungerar.
- [Vanliga frågor och svar](active-directory-aadconnect-pass-through-authentication-faq.md): få svar på vanliga frågor och svar om funktionen direkt-autentisering.
- [Felsöka](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Lär dig att lösa vanliga problem med funktionen direkt-autentisering.
- [Säkerhet ingående](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): avancerad teknisk information om funktionen direkt-autentisering.
- [Azure AD sömlös SSO](active-directory-aadconnect-sso.md): Lär dig mer om den här funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): använda Azure Active Directory-forumet till filen nya funktioner som efterfrågas.
