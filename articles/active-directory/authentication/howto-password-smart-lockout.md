---
title: Det gick inte att brute force-attacker med hjälp av Azure AD smart kontoutelåsning
description: Azure Active Directory smart kontoutelåsning hjälper dig att skydda din organisation från brute force-attacker som försöker gissa lösenord
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67f2af94f32d5439585ad4d727fd2b1bd80fc41b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431713"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory smart kontoutelåsning

Smart kontoutelåsning hjälper utelåsning illvilliga aktörer som försöker gissa användarnas lösenord eller använda brute force-metoder. Den kan identifiera inloggningar som kommer från giltiga användare och behandlas annorlunda än de som angripare och andra okända källor. Smart kontoutelåsning låser ut angripare, medan användarna fortfarande komma åt sina konton och vara produktiva.

Som standard låser smart kontoutelåsning konto från inloggningsförsök i en minut efter 10 misslyckade försök. Konto-Lås igen efter varje efterföljande misslyckade inloggningsförsök, för en minut på första och längre på efterföljande försök.

Smart kontoutelåsning spårar tre sista felaktiga lösenords-hash för att undvika räknaren för kontoutelåsning för samma lösenord som ökar. Om någon anger samma felaktiga lösenord flera gånger, orsakar det här beteendet inte kontot som ska kontoutelåsning.

 > [!NOTE]
 > Hash spåra funktioner är inte tillgänglig för kunder med direktautentisering aktivera eftersom autentiseringen sker lokalt ej i molnet.

Smart kontoutelåsning är alltid på för alla Azure AD-kunder med dessa standardinställningar som ger den rätta blandningen av säkerhet och användbarhet. Anpassning av inställningar för smart kontoutelåsning, med värden som är specifika för din organisation, kräver Azure AD Basic eller högre licenser för dina användare.

Med hjälp av smart kontoutelåsning garanterar inte att en äkta användare kommer aldrig att låsas. När smart kontoutelåsning låser ett användarkonto, testa vi våra bästa inte låsas äkta användaren. Kontoutelåsning-tjänsten försöker se till att obehöriga inte kan komma åt ett äkta användarkonto.  

* Alla Datacenter för Azure Active Directory spårar kontoutelåsning oberoende av varandra. En användare har (threshold_limit * datacenter_count) antal försök, om användaren når varje datacenter.
* Smart kontoutelåsning använder välbekanta plats jämfört med okänd plats för att skilja mellan en obehörig och äkta användaren. Okänd och välbekanta platser har både separat kontoutelåsning räknare.

Smart kontoutelåsning kan integreras med hybriddistributioner, använder hash-synkronisering av lösenord eller direktautentisering för att förhindra att den lokala Active Directory-konton bli utelåsta av angripare. Genom att ange principer för smart kontoutelåsning i Azure AD på rätt sätt kan att attacker filtreras bort innan de når den lokala Active Directory.

När du använder [direktautentisering](../hybrid/how-to-connect-pta.md), måste du se till att:

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

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Så här kontrollerar du om funktionen Smart kontoutelåsning fungerar eller inte

När smart utelåsningströskeln utlöses får du följande meddelande när kontot är låst:

**Ditt konto har låsts temporärt för att förhindra obehörig användning. Försök igen senare och kontakta administratören om du fortfarande har problem**


## <a name="next-steps"></a>Nästa steg

[Lär dig hur du förbjuda felaktiga lösenord i din organisation använder Azure AD.](howto-password-ban-bad.md)

[Konfigurera Självbetjäning för återställning av lösenord för att tillåta användare att låsa upp sina egna konton.](quickstart-sspr.md)
