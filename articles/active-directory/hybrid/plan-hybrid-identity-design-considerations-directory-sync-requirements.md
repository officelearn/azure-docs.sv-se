---
title: Hybrid identity design - krav för katalogsynkronisering Azure | Microsoft Docs
description: Identifiera vilka krav behövs för att synkronisera alla användare mellan = på plats och molnet för företag.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: d3ded429e960679b6bb252bdcec67c5a1d833ac1
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55496014"
---
# <a name="determine-directory-synchronization-requirements"></a>Fastställa katalogsynkroniseringskrav
Synkronisering handlar om och ger användarna en identitet i molnet baserat på deras lokala identitet. Oavsett om de använda synkroniserade kontot för autentisering eller federerad autentisering, måste användarna fortfarande att ha en identitet i molnet.  Den här identiteten måste bibehållas och uppdateras regelbundet.  Uppdateringarna kan olika former, från titel ändras till lösenordsändringar.  

Starta genom att utvärdera organisationer lokala identitetskrav för lösningen och användare. Den här utvärderingsversionen är viktigt att definiera de tekniska kraven för hur användaridentiteter skapas och underhålls i molnet.  Active Directory är på plats för en majoritet av organisationer och den lokala katalogen som användare får som synkroniseras från, men i vissa fall det här inte är fallet.  

Se till att besvara följande frågor:

* Har du en AD-skog, flera eller ingen?
  
  * Hur många Azure AD-kataloger kommer du att synkroniseras till?
    
    1. Använder du filtrering?
    2. Har du flera Azure AD Connect-servrar som planerat?
* Har du en synkronisering verktyget lokalt?
  
  * Om Ja, gör du användarna om användarna har en virtuell/katalogintegrering identiteter?
* Har du några andra directory lokalt som du vill synkronisera (t.ex. LDAP-katalogen, HR-databasen, osv)?
  * Ska du göra alla GALSync?
  * Vad är det aktuella tillståndet för UPN-namn i din organisation? 
  * Har du en annan katalog som användare autentiseras mot?
  * Använder företaget Microsoft Exchange?
    * De planerar för en exchange-hybridinstallation?

Nu när du har en idé om din synkroniseringskrav måste fastställa vilket verktyg är korrekt som uppnådde dessa krav.  Microsoft tillhandahåller flera verktyg för att åstadkomma katalogintegrering och synkronisering.  Se den [jämförelsetabell för Hybrididentitet directory integration verktyg](plan-hybrid-identity-design-considerations-tools-comparison.md) för mer information. 

Nu när du har din synkroniseringskrav och verktyg som gör det för ditt företag kan behöver du utvärdera de program som använder dessa katalogtjänster. Den här utvärderingsversionen är viktigt att definiera de tekniska kraven för att integrera dessa program till molnet. Se till att besvara följande frågor:

* Dessa program flyttas till molnet och använda katalogen?
* Finns det särskilda attribut som måste synkroniseras till molnet så att programmen kan använda dem har?
* Dessa program måste skrivas igen för att dra nytta av molnet auth?
* Fortsätter programmen att live lokalt medan användarna komma åt dem med hjälp av cloud-identitet?

Du måste också bestämma katalogsynkronisering security krav och begränsningar. Den här utvärderingsversionen är viktigt att hämta en lista över de krav som krävs för att skapa och underhålla användaridentiteter i molnet. Se till att besvara följande frågor:

* Där kommer synkronisering att hitta servern?
* Kommer det att finnas domänansluten?
* Servern finns på ett begränsat nätverk bakom en brandvägg, till exempel en DMZ?
  * Kommer du att kunna öppna brandväggsportar som krävs för att stödja synkronisering?
* Har du en återställningsplan för synkronisering av servern?
* Har du ett konto med rätt behörigheter för alla skogar som du vill synkronisera med?
  * Om ditt företag inte känner till svaret för den här frågan, kan du läsa avsnittet ”behörigheter för Lösenordssynkronisering” i artikeln [installera Azure Active Directory Sync Service](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) och avgör om du redan har ett konto med de här behörigheterna eller om du vill skapa en.
* Om du har flera skogar använda synkroniseringen har synkroniseringsservern kunna gå till varje skog?

> [!NOTE]
> Se till att ner varje svar och försök förstå anledningen till svaret. [Fastställa kraven på incidentsvar](plan-hybrid-identity-design-considerations-incident-response-requirements.md) kommer att överskrida de tillgängliga alternativen. Har besvarat frågor väljer du vilket alternativ som bäst passar din verksamhet behöver du.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa krav för multifaktorautentisering](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Se också
[Översikt över design-överväganden](plan-hybrid-identity-design-considerations-overview.md)

