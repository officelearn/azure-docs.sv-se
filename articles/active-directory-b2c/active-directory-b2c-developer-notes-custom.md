---
title: "Azure Active Directory B2C: Kommentarer för utvecklare om hur du använder anpassade principer | Microsoft Docs"
description: "Information för utvecklare på Konfigurera och underhålla Azure AD B2C med anpassade principer"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/05/2017
ms.author: joroja
ms.openlocfilehash: a5f222e5b11e05286152a9f1cc55d2c3fc27a9dc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Viktig information för en förhandsversion av Azure Active Directory B2C anpassad princip
Funktionsuppsättningen anpassad princip är nu tillgänglig för utvärdering under förhandsversion för alla Azure Active Directory B2C (Azure AD B2C) kunder. Den här funktionsuppsättningen är inriktad på avancerade identitet utvecklare som bygger mest komplexa identitetslösningar.  

Idag kräver den här funktionsuppsättningen konfigurera identitet upplevelse Framework direkt via redigering av XML-filen. Den här metoden i konfigurationen är kraftfulla och komplexa. Avancerade identitet utvecklare som använder identitet upplevelse Framework bör planera att investera lite tid att slutföra genomgångar och läsa referensdokument. 

## <a name="features-included-in-this-public-preview"></a>Funktioner som ingår i denna förhandsversion
Med de nya funktionerna som introducerades i public preview kan utvecklare göra följande:<br>

* Författare och överföra anpassad autentisering användaren transporter med hjälp av anpassade principer. 
   * Beskriv användaren resor stegvisa som utbyten mellan Anspråksproviders. 
   * Definiera villkorliga grenar i resor för användaren. 
* Integrera REST API-aktiverade tjänster i din användare resor för anpassad autentisering.  
* Lägg till federation med identitetsleverantörer som är kompatibla med OpenIDConnect standard. <br>
* Lägg till federation med identitetsleverantörer som följer SAML 2.0-protokollet. 

## <a name="terms-of-the-public-preview"></a>Villkoren i public preview

* Vi rekommenderar att du kan använda de nya funktionerna i utvärderingssyfte.<br>
* Nya funktioner är inte avsedd att användas i en produktionsmiljö.<br>
* Servicenivåavtal (SLA) gäller inte för de nya funktionerna. <br>
* Supportärenden kan registreras via vanlig supportkanaler. <br>
* Det finns inga framtida datum för allmän tillgänglighet.<br>
* Vår gottfinnande och av någon anledning, kan Microsoft flagga och avvisa eller begränsa scenarier och användaren resor som överskrider omfånget för Azure AD B2C produkten auktoriserad som fungerar som en kund identitets- och (CIAM) plattform.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Ansvaret för anpassad princip funktionsuppsättningen utvecklare
Manuell konfiguration ger lågnivå-åtkomst till den underliggande plattformen för Azure AD B2C och resulterar i att skapa ett unikt, helt anpassningsbar förtroende ramverk. Möjliga varianter av anpassade identitetsleverantörer, Betrodda relationer integration med externa tjänster och stegvisa arbetsflöden Placera större krav på avancerade utvecklare som använder dem.

Om du vill utnyttja alla fördelar som förhandsversion, föreslår vi att utvecklare förbrukar funktionsuppsättningen anpassad princip följer riktlinjerna nedan:
* Bekanta dig med Identity Experience-motorn och nyckel/hemligheter management configuration språk.
* Bli ägare till scenarier och anpassad integrering.
* Utföra metodisk scenariotestning.
* Följ programvaruutveckling och mellanlagring metodtips med minst en utvecklings- och testmiljö och en produktionsmiljö.
* Håll dig informerad om förändringar från leverantörer och tjänster som du integrerar med. Till exempel hålla koll på ändringar i hemligheter och planerade och oplanerade ändringar av tjänsten.
* Konfigurera active övervakning och övervaka svarstiderna hos produktionsmiljöer.
* Behåll det aktuella kontakta e-postadresser och håll kan svara på e-postmeddelanden Microsoft live-plats-teamet.
* Vidta lämpliga åtgärder när du gör detta genom att Microsoft live-plats-teamet. 


>[!NOTE]
>Dessa funktioner kan slutligen ingå i Azure AD inbyggda principer, vilket gör dem mer tillgänglig för alla utvecklare.

## <a name="next-steps"></a>Nästa steg
[Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md).
