---
title: "Azure AD Connect: Direkt autentisering - aktuella begränsningar | Microsoft Docs"
description: "Den här artikeln beskriver aktuella begränsningar i Azure Active Directory (AD Azure) direkt-autentisering"
services: active-directory
keywords: "Azure AD Connect direkt-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: billmath
ms.openlocfilehash: 0b86be1c1b26079aee33bcf8e861f679f4cbe11e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory direkt-autentisering: Aktuella begränsningar

>[!IMPORTANT]
>Azure Active Directory (AD Azure) direkt-autentisering är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den. Direkt-autentisering är endast tillgängligt i hela världen instans av Azure AD och inte på den [Tyskland för Microsoft Azure-molnet](http://www.microsoft.de/cloud-deutschland) eller [Microsoft Azure Government molnet](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scenarier som stöds

Följande scenarier stöds fullt ut:

- Användarinloggningar webbläsarbaserade program för alla webbprogram
- Användarinloggningar för Office 365-klientprogram som stöder [modern autentisering](https://aka.ms/modernauthga)
- Office 2016 och Office 2013 _med_ modern autentisering
- Azure AD-domän som ansluter till för Windows 10-enheter
- Stöd för Exchange ActiveSync

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier är _inte_ stöds:

- Användarinloggningar till äldre Office-program: Office 2010 och Office 2013 _utan_ modern autentisering. Organisationer uppmuntras att växla till modern autentisering, om möjligt. Modern autentisering tillåter stöd för direkt-autentisering. Du kan också skydda dina användarkonton med hjälp av [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) funktioner, till exempel Azure Multi-Factor Authentication.
- Användarinloggningar till Skype för företag-klientprogram, inklusive Skype för företag 2016.
- Användarinloggningar till PowerShell version 1.0. Vi rekommenderar att du använder PowerShell version 2.0.
- Azure Active Directory Domain Services.
- Applösenord för Multifaktorautentisering.
- Identifiering av användare med [läcka ut autentiseringsuppgifter](../active-directory-reporting-risk-events.md#leaked-credentials).

>[!IMPORTANT]
>Som en lösning för scenarier som inte stöds _endast_, aktivera synkronisering av lösenords-hash för den [valfria funktioner](active-directory-aadconnect-get-started-custom.md#optional-features) sida i Azure AD Connect-guiden.

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

