---
title: Förhindra attacker med smart utelåsning - Azure Active Directory
description: Azure Active Directory smart lockout hjälper till att skydda din organisation från brute-force attacker försöker gissa lösenord
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
ms.openlocfilehash: bfd49a4429dc0d7f5db07a577016c21de8fc58d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75762883"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory – Smart utlåsning

Smart lockout hjälper till att låsa ute dåliga aktörer som försöker gissa dina användares lösenord eller använda brute-force metoder för att komma in. Den kan känna igen inloggningar som kommer från giltiga användare och behandla dem på ett annat sätt än de av angripare och andra okända källor. Smart utelåsning låser bort angriparna, samtidigt som användarna kan fortsätta att komma åt sina konton och vara produktiva.

Som standard låser smart utelåsning kontot från inloggningsförsök i en minut efter 10 misslyckade försök. Kontot låsas igen efter varje efterföljande misslyckat inloggningsförsök, för en minut i början och längre i efterföljande försök.

Smart utelåsning spårar de tre senaste felaktiga lösenordsharna för att undvika att låsa in spärrräknaren för samma lösenord. Om någon anger samma felaktiga lösenord flera gånger kommer det här beteendet inte att leda till att kontot utestrar.

 > [!NOTE]
 > Hash-spårningsfunktioner är inte tillgängliga för kunder med direktautentisering aktiverad när autentisering sker lokalt inte i molnet.

Federerade distributioner med AD FS 2016 och AF FS 2019 kan aktivera liknande fördelar med [AD FS Extranet Lockout och Extranet Smart Lockout](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

Smart utelåsning är alltid aktiverat för alla Azure AD-kunder med dessa standardinställningar som erbjuder rätt blandning av säkerhet och användbarhet. Anpassning av inställningar för smart utelåsning, med värden som är specifika för din organisation, kräver betalda Azure AD-licenser för dina användare.

Att använda smart utelåsning garanterar inte att en äkta användare aldrig kommer att låsas ute. När smart lockout låser ett användarkonto gör vi vårt bästa för att inte låsa ute den äkta användaren. Lockout-tjänsten försöker se till att felaktiga aktörer inte kan få åtkomst till ett äkta användarkonto.  

* Varje Azure Active Directory datacenter spårar utelåsning oberoende av varandra. En användare kommer att ha (threshold_limit * datacenter_count) antal försök, om användaren träffar varje datacenter.
* Smart Lockout använder välbekant plats kontra okänd plats för att skilja mellan en dålig aktör och den äkta användaren. Okända och välbekanta platser kommer båda att ha separata lockout räknare.

Smart utelåsning kan integreras med hybriddistributioner, med hjälp av synkronisering av lösenord hash-enheter eller direktautentisering för att skydda lokala Active Directory-konton från att låsas ute av angripare. Genom att ange smarta utelåsningsprinciper i Azure AD på rätt sätt kan attacker filtreras bort innan de når lokala Active Directory.

När du använder [direktautentisering](../hybrid/how-to-connect-pta.md)måste du se till att:

* Tröskelvärdet för Azure AD-utelåsning är **mindre** än tröskelvärdet för utelåsning av Active Directory-konto. Ange värden så att tröskelvärdet för utelåsning av Active Directory-konto är minst två eller tre gånger längre än tröskelvärdet för Azure AD-utelåsning. 
* Azure AD-utelåsningstiden måste anges längre än räknaren för återställning av active directory-konto efter varaktighet. Tänk på att Azure AD-varaktigheten är inställd på några sekunder, medan AD-varaktigheten anges i minuter. 

Om du till exempel vill att azure AD-räknaren ska vara högre än AD, skulle Azure AD vara 120 sekunder (2 minuter) medan din lokala AD är inställd på 1 minut (60 sekunder).

> [!IMPORTANT]
> För närvarande kan en administratör inte låsa upp användarnas molnkonton om de har låsts ut av smart utelåsning. Administratören måste vänta tills utelåsningstiden har gått ut. Användaren kan dock låsa upp genom att använda självbetjäningslösenordsåterställning (SSPR) från en betrodd enhet eller plats.

## <a name="verify-on-premises-account-lockout-policy"></a>Verifiera lokal princip för kontoutelåsning

Använd följande instruktioner för att verifiera din lokala policy för utelåsning av Active Directory-konto:

1. Öppna verktyget Grupprinciphantering.
2. Redigera grupprincipen som innehåller organisationens princip för kontoutelåsning, till exempel **standarddomänprincipen**.
3. Bläddra till principer för **datorkonfiguration** > **Policies** > **Windows Settings** > **Security Settings** > **Account Policies** > **Account Lockout Policy**.
4. Verifiera **tröskelvärdet för kontoutelåsning** och **återställ kontoutelåsningsräknaren efter** värden.

![Ändra den lokala principen för utelåsning av Active Directory-konto](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Hantera smarta utelåsningsvärden för Azure AD

Baserat på dina organisationskrav kan smarta utelåsningsvärden behöva anpassas. Anpassning av inställningar för smart utelåsning, med värden som är specifika för din organisation, kräver betalda Azure AD-licenser för dina användare.

Så här kontrollerar eller ändrar du de smarta utelåsningsvärdena för din organisation:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj *Azure Active Directory*. Välj > **säkerhetsautentiseringsmetoder** > **Lösenordsskydd**. **Security**
1. Ange **tröskelvärdet För utelåsning**– baserat på hur många misslyckade inloggningar som tillåts på ett konto före den första utelåsningen. Standardvärdet är 10.
1. Ställ in **längden på lockouten i sekunder**, till längden i sekunder för varje utelåsning. Standardvärdet är 60 sekunder (en minut).

> [!NOTE]
> Om den första inloggningen efter en utelåsning också misslyckas låser kontot ut igen. Om ett konto låses upprepade gånger ökar utelåsningstiden.

![Anpassa Azure AD-principen för smart utelåsning i Azure-portalen](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Så här tar du reda på om funktionen Smart-utelåsning fungerar eller inte

När tröskelvärdet för smart utelåsning utlöses får du följande meddelande medan kontot är låst:

**Ditt konto är tillfälligt låst för att förhindra obehörig användning. Försök igen senare, och om du fortfarande har problem kontaktar du administratören.**

## <a name="next-steps"></a>Nästa steg

* [Ta reda på hur du förbjuder felaktiga lösenord i din organisation med Azure AD.](howto-password-ban-bad.md)
* [Konfigurera återställning av lösenord för självbetjäning så att användarna kan låsa upp sina egna konton.](quickstart-sspr.md)
