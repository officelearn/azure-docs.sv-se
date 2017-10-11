---
title: "Azure Active Directory hybrid identity designöverväganden - fastställa kraven för directory-synkronisering | Microsoft Docs"
description: "Identifiera vilka krav som behövs för att synkronisera alla användare mellan on = lokala och molnbaserade för företaget."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 5ef87e606f055359ca325befd6048353ce57ca2b
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="determine-directory-synchronization-requirements"></a>Ange krav för directory-synkronisering
Synkronisering är att tillhandahålla användare en identitet i molnet baserat på deras lokala identitet. Oavsett om de ska använda synkroniserade kontot för autentisering eller federerad autentisering, behöver användarna fortfarande ha en identitet i molnet.  Den här identiteten behöver underhålls och uppdateras regelbundet.  Uppdateringarna kan ta många formulär från titel ändras till ändring av lösenord.  

Börja med att utvärdera organisationer lokalt identitet lösningen och användaren kraven. Denna utvärdering är viktigt att definiera de tekniska kraven för hur användaridentiteter skapas och underhålls i molnet.  Active Directory är lokala för en majoriteten av organisationer och lokala katalogen som användare kommer att synkroniseras från, men i vissa fall det inte är fallet.  

Se till att besvara följande frågor:

* Har du en AD-skog, flera eller ingen?
  
  * Hur många Azure AD-kataloger kommer du att synkroniseras till?
    
    1. Använder du filtrering?
    2. Har du flera Azure AD Connect-servrar som är planerat?
* Har du en synkronisering verktyget lokalt?
  
  * Om Ja, har du dina användare om användarna har en virtuell katalog/integrering av identiteter?
* Har du alla andra directory lokalt som du vill synkronisera (t.ex. LDAP-katalogen, HR-databasen, osv)?
  * Ska du göra alla GALSync
  * Vad är det aktuella tillståndet för UPN-namn i din organisation? 
  * Har du en annan katalog som användare autentiseras mot?
  * Använder företaget Microsoft Exchange?
    * Kommer de att en exchange-hybridinstallation?

Nu när du har en uppfattning om synkroniseringskrav för måste fastställa vilket verktyg som är korrekt så att de uppfyller dessa krav.  Microsoft erbjuder flera verktyg för att åstadkomma katalogintegrering och synkronisering.  Finns det [Hybrididentitet directory integration verktyg jämförelsetabell](active-directory-hybrid-identity-design-considerations-tools-comparison.md) för mer information. 

Nu när du har synkroniseringskrav för och verktyg som gör det för ditt företag, som du behöver utvärdera de program som använder dessa katalogtjänster. Denna utvärdering är viktigt att definiera de tekniska kraven för att integrera dessa program till molnet. Se till att besvara följande frågor:

* Dessa program flyttas till molnet och använda katalogen?
* Finns det särskilda attribut som måste synkroniseras till molnet så att programmen kan använda dem korrekt?
* Dessa program måste skrivas igen för att utnyttja fördelarna med molnet auth?
* Fortsätter programmen live lokalt medan användare komma åt dem med hjälp av molnidentiteten?

Du måste också bestämma säkerhet kraven och begränsningarna katalogsynkronisering. Denna utvärdering är viktigt att hämta en lista över de krav som behövs för att skapa och underhålla användaridentiteter i molnet. Se till att besvara följande frågor:

* Där kommer att hitta synkroniseringsservern?
* Kommer det att vara ansluten till en domän?
* Servern finns på ett begränsat nätverk bakom en brandvägg, till exempel en DMZ?
  * Kommer du att kunna öppna de nödvändiga Brandväggsportarna att stödja synkronisering?
* Har du en återställningsplan för av synkroniseringsservern?
* Har du ett konto med rätt behörigheter för alla skogar som du vill synkronisera med?
  * Om företaget inte känner till svaret på den här frågan, läser du avsnittet ”behörigheter för Lösenordssynkronisering” i artikeln [installera Azure Active Directory Sync Service](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) och ta reda på om du redan har ett konto med dessa behörigheter eller om du behöver skapa en.
* Om du har mutli-skog synkronisering kan synkroniseringsservern för varje skog?

> [!NOTE]
> Se till att varje svar och försök förstå anledningen till svaret. [Fastställa kraven på incidentsvar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) kommer att överskrida de tillgängliga alternativen. Har besvarat frågorna väljer du vilket alternativ som bäst passar dina behöver.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Ange krav för multifaktorautentisering](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](active-directory-hybrid-identity-design-considerations-overview.md)

