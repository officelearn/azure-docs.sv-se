---
title: Hybrididentitetsdesign – krav på katalogsynkronisering Azure | Microsoft-dokument
description: Identifiera vilka krav som behövs för att synkronisera alla användare mellan on=premises och cloud för företaget.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21558c4eccf0cd1f4e9e1d630f0e89dbb6f01c51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381169"
---
# <a name="determine-directory-synchronization-requirements"></a>Fastställ krav på katalogsynkronisering
Synkronisering handlar om att ge användarna en identitet i molnet baserat på deras lokala identitet. Oavsett om de kommer att använda synkroniserat konto för autentisering eller federerad autentisering, måste användarna fortfarande ha en identitet i molnet.  Denna identitet måste underhållas och uppdateras regelbundet.  Uppdateringarna kan ta många former, från titeländringar till lösenordsändringar.  

Börja med att utvärdera organisationernas lokala identitetslösning och användarkrav. Den här utvärderingen är viktig för att definiera de tekniska kraven för hur användaridentiteter ska skapas och underhållas i molnet.  För en majoritet av organisationerna är Active Directory lokalt och kommer att vara den lokala katalog som användarna kommer att synkroniseras från, men i vissa fall kommer detta inte att vara fallet.  

Se till att svara på följande frågor:

* Har du en AD-skog, flera eller ingen?
  
  * Hur många Azure AD-kataloger synkroniseras du med?
    
    1. Använder du filtrering?
    2. Har du planerat flera Azure AD Connect-servrar?
* Har du för närvarande ett synkroniseringsverktyg lokalt?
  
  * Om ja, gör användarna om användarna har en virtuell katalog /integration av identiteter?
* Har du någon annan katalog lokalt som du vill synkronisera (t.ex.
  * Kommer du att göra någon GALSync?
  * Vad är det aktuella läget för UPN i din organisation? 
  * Har du en annan katalog som användare autentiserar mot?
  * Använder ditt företag Microsoft Exchange?
    * Planerar de att ha en hybridutbytesdistribution?

Nu när du har en uppfattning om dina synkroniseringskrav måste du bestämma vilket verktyg som är rätt för att uppfylla dessa krav.  Microsoft tillhandahåller flera verktyg för att utföra katalogintegration och synkronisering.  Mer information finns i [jämförelsetabellen för hybrididentitetskatalogintegrationsverktyg.](plan-hybrid-identity-design-considerations-tools-comparison.md) 

Nu när du har dina synkroniseringskrav och det verktyg som ska åstadkomma detta för ditt företag, måste du utvärdera de program som använder dessa katalogtjänster. Den här utvärderingen är viktig för att definiera de tekniska kraven för att integrera dessa program i molnet. Se till att svara på följande frågor:

* Kommer dessa program att flyttas till molnet och använda katalogen?
* Finns det speciella attribut som måste synkroniseras med molnet så att dessa program kan använda dem?
* Kommer dessa program måste skrivas om för att dra nytta av molnautentisering?
* Kommer dessa program att fortsätta att vara lokalt medan användare kommer åt dem med hjälp av molnidentiteten?

Du måste också fastställa säkerhetskrav och begränsningar katalog synkronisering. Den här utvärderingen är viktig för att få en lista över de krav som behövs för att skapa och underhålla användarens identiteter i molnet. Se till att svara på följande frågor:

* Var finns synkroniseringsservern?
* Kommer det att domänen gå?
* Kommer servern att finnas på ett begränsat nätverk bakom en brandvägg, till exempel en DMZ?
  * Kommer du att kunna öppna de brandväggsportar som krävs för synkronisering?
* Har du en plan för haveriberedskap för synkroniseringsservern?
* Har du ett konto med rätt behörighet för alla skogar som du vill synkronisera med?
  * Om ditt företag inte vet svaret på den här frågan läser du avsnittet Behörigheter för lösenordssynkronisering i artikeln [Installera Synkroniseringstjänsten](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) för Azure Active Directory och ta reda på om du redan har ett konto med dessa behörigheter eller om du behöver skapa ett.
* Om du har mutli-forest sync kan synkroniseringsservern komma till varje skog?

> [!NOTE]
> Skriv ner varje svar och försök förstå anledningen till svaret. [Bestäm att kraven](plan-hybrid-identity-design-considerations-incident-response-requirements.md) för incidenthantering överskrider de tillgängliga alternativen. Genom att ha svarat på dessa frågor väljer du vilket alternativ som bäst passar dina affärsbehov.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa autentiseringskrav för flera faktorer](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över designöverväganden](plan-hybrid-identity-design-considerations-overview.md)

