---
title: 'Azure AD Connect: Direktautentisering – Aktuella begränsningar | Microsoft-dokument'
description: I den här artikeln beskrivs de aktuella begränsningarna i Azure Active Directory (Azure AD) direktautentisering
services: active-directory
keywords: Azure AD Connect Pass-through-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97dc67d46b08bf5765c59806b45edd82f38720cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347760"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory Direktautentisering: Aktuella begränsningar

>[!IMPORTANT]
>Azure Active Directory (Azure AD) Direktautentisering är en kostnadsfri funktion och du behöver inga betalda versioner av Azure AD för att använda den. Direktautentisering är endast tillgängligt i den världsomspännande instansen av Azure AD och inte i [Microsoft Azure Germany-molnet](https://www.microsoft.de/cloud-deutschland) eller [Microsoft Azure Government-molnet](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scenarier som stöds

Följande scenarier stöds:

- Användarloggningar till webbläsarbaserade program.
- Användarloggningar till Outlook-klienter med äldre protokoll som Exchange ActiveSync, EAS, SMTP, POP och IMAP.
- Användarinloggningar till äldre Office-klientprogram och Office-program som stöder [modern autentisering:](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview)Office 2013- och 2016-versioner.
- Användarloggningar till äldre protokollprogram som PowerShell version 1.0 och andra.
- Azure AD-kopplingar till Windows 10-enheter.
- Applösenord för multifaktorautentisering.

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier stöds _inte:_

- Identifiering av användare med [läckta autentiseringsuppgifter](../reports-monitoring/concept-risk-events.md#leaked-credentials).
- Azure AD Domain Services behöver lösenord hash-synkronisering aktiveras på klienten. Därför fungerar klienter som _använder_ direktautentisering bara för scenarier som behöver Azure AD Domain Services.
- Direktautentisering är inte integrerat med [Azure AD Connect Health](whatis-hybrid-identity-health.md).

> [!IMPORTANT]
> Som en lösning för scenarier som _inte_ stöds (förutom Azure AD Connect Health-integrering) aktiverar du synkronisering av lösenord hash-funktioner på sidan [Valfria funktioner](how-to-connect-install-custom.md#optional-features) i Azure AD Connect-guiden.
> 
> [!NOTE]
> Om du aktiverar synkronisering av lösenord hash-funktioner kan du välja att redundansautentisering om din lokala infrastruktur har avbrutits. Den här redundansen från direktautentisering till synkronisering av lösenord hash är inte automatisk. Du måste byta inloggningsmetod manuellt med Azure AD Connect. Om servern som kör Azure AD Connect går ned behöver du hjälp från Microsoft Support för att inaktivera direktautentisering.

## <a name="next-steps"></a>Nästa steg
- [Snabbstart:](how-to-connect-pta-quick-start.md)Kom igång med Azure AD Pass-through-autentisering.
- [Migrera från AD FS till Direktautentisering](https://aka.ms/ADFSTOPTADPDownload) – En detaljerad guide för att migrera från AD FS (eller annan federationsteknik) till direktautentisering.
- [Smart Utelåsning:](../authentication/howto-password-smart-lockout.md)Lär dig hur du konfigurerar smart utelåsningsfunktionen på din klient för att skydda användarkonton.
- [Teknisk djupdykning](how-to-connect-pta-how-it-works.md): Förstå hur funktionen Direktautentisering fungerar.
- [Vanliga frågor och svar](how-to-connect-pta-faq.md): Hitta svar på vanliga frågor om funktionen Direktautentisering.
- [Felsöka:](tshoot-connect-pass-through-authentication.md)Lär dig hur du löser vanliga problem med direktautentiseringsfunktionen.
- [Djupdykning i säkerhet:](how-to-connect-pta-security-deep-dive.md)Få djup teknisk information om funktionen Direktautentisering.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Läs mer om den här kompletterande funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Använd Azure Active Directory Forum för att fila nya funktionsbegäranden.
