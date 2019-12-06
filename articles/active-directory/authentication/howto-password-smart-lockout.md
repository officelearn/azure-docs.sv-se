---
title: Förhindra attacker med Smart utelåsning – Azure Active Directory
description: Azure Active Directory Smart utelåsning hjälper till att skydda din organisation från brute force-attacker som försöker gissa lösen ord
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30006d0dcccdd1b160289c72769340d141198d3c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847600"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory Smart utelåsning

Smart utelåsning hjälper dig att låsa upp dåliga aktörer som försöker gissa dina användares lösen ord eller använda råa metoder för att komma igång. Den kan identifiera inloggningar som kommer från giltiga användare och behandla dem annorlunda än för angripare och andra okända källor. Smart utelåsning låser angriparna och låter användarna fortsätta att komma åt sina konton och vara produktiva.

Som standard låser Smart utelåsning kontot från inloggnings försök i en minut efter 10 misslyckade försök. Kontot låser sig igen efter varje efterföljande misslyckad inloggnings försök, i en minut vid första och längre vid efterföljande försök.

Smart utelåsning spårar de tre senaste Felaktiga hasharna för lösen ord för att undvika ökning av utelåsnings räknaren för samma lösen ord. Om någon anger samma Felaktiga lösen ord flera gånger, kommer det här beteendet inte att orsaka att kontot kan låsas.

 > [!NOTE]
 > Funktionen för hash-spårning är inte tillgänglig för kunder med direkt autentisering aktive rad eftersom autentisering sker lokalt, inte i molnet.

Federerade distributioner som använder AD FS 2016 och AF FS 2019 kan möjliggöra liknande förmåner med [AD FS extra näts utelåsning och extra näts Smart utelåsning](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

Smart utelåsning är alltid aktiverat för alla Azure AD-kunder med dessa standardinställningar som ger rätt blandning av säkerhet och användbarhet. Anpassning av inställningarna för smart utelåsning, med värden som är speciella för din organisation, kräver betalda Azure AD-licenser för dina användare.

Att använda Smart utelåsning garanterar inte att en äkta användare aldrig blir utelåst. När Smart utelåsning låser ett användar konto, försöker vi med vårt bästa att inte låsa den äkta användaren. Utelåsnings tjänsten försöker se till att felaktiga aktörer inte får åtkomst till ett äkta användar konto.  

* Varje Azure Active Directory data Center spårar utelåsning separat. En användare får (threshold_limit * datacenter_count) antal försök om användaren träffar varje data Center.
* Smart utelåsning använder bekant plats kontra osäker plats för att skilja mellan en felaktig aktör och den äkta användaren. Okända och välbekanta platser kommer båda att ha separata utelåsnings räknare.

Smart utelåsning kan integreras med hybrid distributioner, med hjälp av hash-synkronisering av lösen ord eller genom strömning för att skydda lokala Active Directory-konton från att låsas av angripare. Genom att ange principer för smart utelåsning i Azure AD kan angrepp filtreras ut innan de når lokala Active Directory.

När du använder [direktautentisering](../hybrid/how-to-connect-pta.md)måste du se till att:

* Tröskelvärdet för Azure AD-utelåsning är **mindre** än tröskelvärdet för Active Directory konto utelåsning. Ange värdena så att tröskelvärdet för Active Directory konto utelåsning är minst två eller tre gånger längre än tröskelvärdet för Azure AD-utelåsning. 
* Azure AD-utelåsningsens varaktighet måste vara längre än Active Directory Återställ räknaren för konto utelåsning efter varaktighet. Tänk på att Azure AD-varaktigheten anges i sekunder, medan AD-varaktigheten anges i minuter. 

Om du till exempel vill att din Azure AD-räknare ska vara högre än AD blir Azure AD 120 sekunder (2 minuter) medan din lokala AD har angetts till 1 minut (60 sekunder).

> [!IMPORTANT]
> För närvarande kan en administratör inte låsa upp användarnas moln konton om de har låsts av funktionen Smart utelåsning. Administratören måste vänta tills utelåsnings tiden upphör att gälla. Användaren kan dock låsa upp med hjälp av självbetjäning för återställning av lösen ord (SSPR) från en betrodd enhet eller plats.

## <a name="verify-on-premises-account-lockout-policy"></a>Verifiera den lokala konto utelåsnings principen

Använd följande instruktioner för att verifiera din lokala princip för Active Directory konto utelåsning:

1. Öppna verktyget grupprincip hantering.
2. Redigera grup principen som innehåller din organisations princip för konto utelåsning, till exempel **standard domän principen**.
3. Bläddra till **dator konfiguration** > **principer** > **Windows-inställningar** > **säkerhets inställningar** > **konto principer** > **konto utelåsnings princip**.
4. Verifiera ditt **konto utelåsnings tröskelvärde** och **Återställ räknaren för konto utelåsning efter** värden.

![Ändra principen för den lokala Active Directory konto utelåsning](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Hantera Azure AD Smart utelåsnings värden

Med hjälp av dina organisations krav kan smarta utelåsnings värden behöva anpassas. Anpassning av inställningarna för smart utelåsning, med värden som är speciella för din organisation, kräver betalda Azure AD-licenser för dina användare.

Använd följande steg för att kontrol lera eller ändra de smarta utelåsnings värdena för din organisation:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj *Azure Active Directory*. Välj **autentiseringsmetoder** > **lösen ords skydd**.
1. Ange **tröskelvärdet för utelåsning**, baserat på hur många misslyckade inloggningar som tillåts på ett konto före det första utelåsning. Standardvärdet är 10.
1. Ange **utelåsnings tiden i sekunder**till längden i sekunder för varje utelåsning. Standardvärdet är 60 sekunder (en minut).

> [!NOTE]
> Om den första inloggningen efter en utelåsning också Miss lyckas, låser kontot ut igen. Om ett konto låser sig upprepade gånger ökar utelåsnings varaktigheten.

![Anpassa Azure AD Smart utelåsnings-principen i Azure Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Så här avgör du om funktionen för smart utelåsning fungerar eller inte

När tröskelvärdet för smart utelåsning utlöses, får du följande meddelande när kontot är låst:

**Ditt konto är tillfälligt låst för att förhindra obehörig användning. Försök igen senare och kontakta administratören om du fortfarande har problem.**

## <a name="next-steps"></a>Nästa steg

* [Ta reda på hur du kan förbjuda Felaktiga lösen ord i din organisation med hjälp av Azure AD.](howto-password-ban-bad.md)
* [Konfigurera lösen ords återställning via självbetjäning så att användarna kan låsa upp sina egna konton.](quickstart-sspr.md)
