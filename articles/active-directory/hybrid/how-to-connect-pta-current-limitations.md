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
ms.date: 09/04/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: fabefcffdd7ee9a23d8989f897f30cbf027f42af
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426425"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory-direktautentisering: Aktuella begränsningar

>[!IMPORTANT]
>Azure Active Directory (Azure AD)-direktautentisering är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den. Direktautentisering är endast tillgänglig i hela världen instans av Azure AD och inte på den [Microsoft Azure Tyskland](https://www.microsoft.de/cloud-deutschland) eller [Microsoft Azure Government-molnet](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scenarier som stöds

Följande scenarier stöds:

- Användarinloggningar till webbläsarbaserade webbprogram.
- Användarinloggningar till Outlook-klienter med äldre protokoll, till exempel Exchange ActiveSync-, EAS-, SMTP-, POP- och IMAP.
- Användarinloggningar till äldre Office-program och Office-program som stöder [modern autentisering](https://aka.ms/modernauthga): Office 2010, 2013 och 2016-versioner.
- Användarinloggningar till äldre protokoll program, till exempel PowerShell version 1.0 och andra.
- Azure AD kopplar för Windows 10-enheter.
- Applösenord för Multifaktorautentisering.

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier är _inte_ stöds:

- Identifiering av användare med [läcka ut autentiseringsuppgifter](../reports-monitoring/concept-risk-events.md#leaked-credentials).
- Azure AD Domain Services måste synkronisering av Lösenordshash ska vara aktiverat på klienten. Därför klienter som använder direktautentisering _endast_ fungerar inte för scenarier som behöver Azure AD Domain Services.
- Direktautentisering är inte integrerat med [Azure AD Connect Health](whatis-hybrid-identity-health.md).

>[!IMPORTANT]
>Som en lösning för scenarier som inte stöds _endast_ (med undantag för Azure AD Connect Health-integration), aktivera synkronisering av Lösenordshash på den [valfria funktioner](how-to-connect-install-custom.md#optional-features) sida i Azure AD Connect-guiden.

>[!NOTE]
Aktiverar synkronisering av Lösenordshash ger dig möjlighet att redundans-autentisering om din lokala infrastruktur avbryts. Den här redundansen från direktautentisering till synkronisering av Lösenordshash sker inte automatiskt. Du måste växla inloggningsmetod manuellt med hjälp av Azure AD Connect. Om den server som kör Azure AD Connect stängs av, kommer du behöver hjälp från Microsoft Support att stänga av direktautentisering.

## <a name="next-steps"></a>Nästa steg
- [Snabbstart](how-to-connect-pta-quick-start.md): komma igång med Azure AD-direktautentisering.
- [Migrera från AD FS till direktautentisering](https://aka.ms/ADFSTOPTADPDownload) -en detaljerad vägledning för att migrera från AD FS (eller andra tekniker för federation) till direktautentisering.
- [Smart kontoutelåsning](../authentication/howto-password-smart-lockout.md): Lär dig hur du konfigurerar funktionen för smarta kontoutelåsning på din klient för att skydda användarkonton.
- [Teknisk djupdykning](how-to-connect-pta-how-it-works.md): Förstå hur funktionen direktautentisering fungerar.
- [Vanliga frågor och svar](how-to-connect-pta-faq.md): få svar på vanliga frågor om direktautentisering-funktionen.
- [Felsöka](tshoot-connect-pass-through-authentication.md): Lär dig att lösa vanliga problem med funktionen direktautentisering.
- [Djupgående om säkerhet](how-to-connect-pta-security-deep-dive.md): få djupgående teknisk information om funktionen direktautentisering.
- [Azure AD sömlös SSO](how-to-connect-sso.md): Mer information om den här tilläggsfunktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): använda Azure Active Directory-forumet till filen nya funktionbegäran.
