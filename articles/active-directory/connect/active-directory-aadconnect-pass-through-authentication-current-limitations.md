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
ms.date: 07/23/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2fff52a7909a1f3c59ebe4944386e096bd1a8d95
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213426"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory-direktautentisering: Aktuella begränsningar

>[!IMPORTANT]
>Azure Active Directory (Azure AD)-direktautentisering är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den. Direktautentisering är endast tillgänglig i hela världen instans av Azure AD och inte på den [Microsoft Azure Tyskland](http://www.microsoft.de/cloud-deutschland) eller [Microsoft Azure Government-molnet](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scenarier som stöds

Följande scenarier stöds:

- Användarinloggningar till webbläsarbaserade webbprogram.
- Användarinloggningar till Outlook-klienter med äldre protokoll, till exempel Exchange ActiveSync-, EAS-, SMTP-, POP- och IMAP.
- Användarinloggningar till äldre Office-program och Office-program som stöder [modern autentisering](https://aka.ms/modernauthga): Office 2010, 2013 och 2016-versioner.
- Användarinloggningar till äldre protokoll program, till exempel PowerShell version 1.0 och andra.
- Azure AD-domän som kopplar ihop för Windows 10-enheter.
- Applösenord för Multifaktorautentisering.

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier är _inte_ stöds:

- Identifiering av användare med [läcka ut autentiseringsuppgifter](../active-directory-reporting-risk-events.md#leaked-credentials).
- Azure AD Domain Services måste synkronisering av Lösenordshash ska vara aktiverat på klienten. Därför klienter som använder direktautentisering _endast_ fungerar inte för scenarier som behöver Azure AD Domain Services.
- Direktautentisering är inte integrerat med [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).

>[!IMPORTANT]
>Som en lösning för scenarier som inte stöds _endast_, aktivera synkronisering av Lösenordshash på den [valfria funktioner](active-directory-aadconnect-get-started-custom.md#optional-features) sida i Azure AD Connect-guiden. När användarna loggar in på program i listan i den ”stöds inte scenarier” avsnittet dessa specifika inloggningsförfrågningar är _inte_ hanteras av direktautentisering Autentiseringsagenter och därför inte registreras i [ Direktautentisering loggar](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs).

>[!NOTE]
Aktiverar synkronisering av Lösenordshash ger dig möjlighet att redundans-autentisering om din lokala infrastruktur avbryts. Den här redundansen från direktautentisering till synkronisering av Lösenordshash sker inte automatiskt. Du måste växla inloggningsmetod manuellt med hjälp av Azure AD Connect. Om den server som kör Azure AD Connect stängs av, kommer du behöver hjälp från Microsoft Support att stänga av direktautentisering.

## <a name="next-steps"></a>Nästa steg
- [Snabbstart](active-directory-aadconnect-pass-through-authentication-quick-start.md): komma igång med Azure AD-direktautentisering.
- [Migrera från AD FS till direktautentisering](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx) -en detaljerad vägledning för att migrera från AD FS (eller andra tekniker för federation) till direktautentisering.
- [Smart kontoutelåsning](../authentication/howto-password-smart-lockout.md): Lär dig hur du konfigurerar funktionen för smarta kontoutelåsning på din klient för att skydda användarkonton.
- [Teknisk djupdykning](active-directory-aadconnect-pass-through-authentication-how-it-works.md): Förstå hur funktionen direktautentisering fungerar.
- [Vanliga frågor och svar](active-directory-aadconnect-pass-through-authentication-faq.md): få svar på vanliga frågor om direktautentisering-funktionen.
- [Felsöka](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Lär dig att lösa vanliga problem med funktionen direktautentisering.
- [Djupgående om säkerhet](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): få djupgående teknisk information om funktionen direktautentisering.
- [Azure AD sömlös SSO](active-directory-aadconnect-sso.md): Mer information om den här tilläggsfunktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): använda Azure Active Directory-forumet till filen nya funktionbegäran.
