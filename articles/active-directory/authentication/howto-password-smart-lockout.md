---
title: Det gick inte att brute force-attacker med hjälp av Azure AD smart kontoutelåsning
description: Azure Active Directory smart kontoutelåsning hjälper dig att skydda din organisation från brute force-attacker som försöker gissa lösenord
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: b0fded9f5543d151091955c0b0d645bf9db16b7d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158591"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory smart kontoutelåsning

Smart kontoutelåsning använder intelligenta molntjänster för att låsa ute illvilliga aktörer som försöker gissa användarnas lösenord eller använda brute force-metoder. Den intelligence kan identifiera inloggningar som kommer från giltiga användare och behandlas annorlunda än de som angripare och andra okända källor. Smart kontoutelåsning låser ut angripare, medan användarna fortfarande komma åt sina konton och vara produktiva.

Som standard låser smart kontoutelåsning konto från inloggningsförsök i en minut efter tio misslyckade försök. Konto-Lås igen efter varje efterföljande misslyckade inloggningsförsök, för en minut på första och längre på efterföljande försök.

Smart kontoutelåsning är alltid på för alla Azure AD-kunder med dessa standardinställningar som ger den rätta blandningen av säkerhet och användbarhet. Anpassning av inställningar för smart kontoutelåsning, med värden som är specifika för din organisation, kräver Azure AD Basic eller högre licenser för dina användare.

Smart kontoutelåsning kan integreras med hybriddistributioner, använder hash-synkronisering av lösenord eller direktautentisering för att förhindra att den lokala Active Directory-konton bli utelåsta av angripare. Genom att ange principer för smart kontoutelåsning i Azure AD på rätt sätt kan att attacker filtreras bort innan de når den lokala Active Directory.

När du använder [direktautentisering](../connect/active-directory-aadconnect-pass-through-authentication.md), måste du se till att:

   * Tröskelvärde för låsning av Azure AD är **mindre** än tröskelvärde för kontoutelåsning Active Directory. Ange värden så att Active Directory-kontoutelåsningströskelvärde är minst två eller tre gånger som är längre än tröskelvärde för låsning av Azure AD. 
   * Azure AD-utelåsningstid **sekundsnabbt** är **längre** än Active Directory Återställ räknaren för kontoutelåsning efter längd **minuter**.

> [!IMPORTANT]
> En administratör inte för närvarande inte låsa upp användarnas molnkonton om de har låsts ute för Smart kontoutelåsning kapaciteten. Administratören måste vänta tills utelåsningstid att upphöra att gälla.

## <a name="verify-on-premises-account-lockout-policy"></a>Kontrollera den lokala kontoutelåsning

Använd följande instruktioner för att verifiera din lokala Active Directory-kontoutelåsning:

1. Öppna verktyget Group Policy Management.
2. Redigera grupprincipen som innehåller din organisations kontoutelåsning, till exempel den **Standarddomänprincip**.
3. Bläddra till **Datorkonfiguration** > **principer** > **Windows-inställningar** > **säkerhetsinställningar**   >  **Kontoprinciper** > **konto kontoutelåsning princip**.
4. Kontrollera din **tröskelvärde för kontoutelåsning** och **Återställ räknaren för kontoutelåsning efter** värden.

![Ändra den lokala Active Directory kontoutelåsning med hjälp av ett grupprincipobjekt](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Hantera Azure AD smart kontoutelåsning värden

Baserat på din organisations krav, kan smart kontoutelåsning värden behöva anpassas. Anpassning av inställningar för smart kontoutelåsning, med värden som är specifika för din organisation, kräver Azure AD Basic eller högre licenser för dina användare.

För att kontrollera eller ändra smart kontoutelåsning värdena för din organisation, använder du följande steg:

1. Logga in på den [Azure-portalen](https://portal.azure.com), och klicka på **Azure Active Directory**, sedan **autentiseringsmetoder**.
1. Ange den **tröskelvärde för kontoutelåsning**, baserat på hur många misslyckade inloggningar tillåts för ett konto innan den första utelåsningen. Standardvärdet är 10.
1. Ange den **kontoutelåsning varaktighet i sekunder**, längden på några sekunder på varje kontoutelåsning. Standardvärdet är 60 sekunder (en minut).

> [!NOTE]
> Om den första inloggningen när en utelåsning genererar ett fel, kontot låser ut igen. Om ett konto har låsts upprepade gånger kan ökar utelåsningstiden.

![Anpassa Azure AD-princip i Azure-portalen för smart kontoutelåsning](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)
## <a name="next-steps"></a>Nästa steg

[Lär dig hur du förbjuda felaktiga lösenord i din organisation använder Azure AD.](howto-password-ban-bad.md)

[Konfigurera Självbetjäning för återställning av lösenord för att tillåta användare att låsa upp sina egna konton.](quickstart-sspr.md)