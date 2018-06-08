---
title: Azure AD Connect Health - diagnostisera dupliceras synkroniseringsfel för attributet | Microsoft Docs
description: Det här dokumentet beskriver diagnos av Duplicerat attribut synkroniseringsfel och en möjlig lösning överblivna objektet scenarier direkt från Azure-portalen.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 4a6e0924492c26c9bad4ed0af207ad9764c3cc5c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831905"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnostisera och åtgärda synkroniseringsfel Duplicerat attribut

## <a name="overview"></a>Översikt
Azure Active Directory (AD Azure) Connect Health introducerar tar längre ett steg om du vill markera synkroniseringsfel hjälpåtgärder i självbetjäningen. Den felsöker Duplicerat attribut synkroniseringsfel och korrigeringar objekt som är frånkopplade från Azure AD.
Funktionen diagnos har följande fördelar:
- Det ger en diagnostiska procedur som begränsas av Duplicerat attribut synkroniseringsfel. Och det ger specifika korrigeringar.
- Det gäller en korrigering för dedikerade scenarier från Azure AD för att åtgärda felet i ett enda steg.
- Ingen uppgradering eller konfiguration krävs för att aktivera den här funktionen.
Läs mer om Azure AD [identitet synkronisering och dubblett attributet återhämtning](https://aka.ms/dupattributeresdocs).

## <a name="problems"></a>Problem
### <a name="a-common-scenario"></a>Ett vanligt scenario
När **QuarantinedAttributeValueMustBeUnique** och **AttributeValueMustBeUnique** synkroniseringsfel uppstår är det vanligt att en **UserPrincipalName** eller **Proxyadresser** konflikt i Azure AD. Du kan lösa synkroniseringsfel genom att uppdatera motstridiga källobjektet från den lokala sidan. Fel vid synkronisering kommer att matcha efter nästa synkronisering. Den här bilden anger till exempel att två användare har en konflikt mellan sina **UserPrincipalName**. Båda är **Joe.J@contoso.com**. Motstridiga objekt är i karantän i Azure AD.

![Diagnostisera fel vanligt scenario för Lösenordssynkronisering](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Överblivna objektet scenario
Ibland kan det hända att en befintlig användare förlorar den **Källfästpunkten**. Borttagningen av källobjektet har inträffat i lokala Active Directory. Men ändringen av borttagning signal aldrig fick synkroniseras till Azure AD. Den här förlusten sker av skäl som motorn synkroniseringsproblem eller domän migrering. När samma objekt hämtar återställas eller återskapas logiskt, en befintlig användare vara användare att synkronisera från den **Källfästpunkten**. 

När en befintlig användare är en molnbaserad objekt kan se du också motstridiga användaren synkroniseras till Azure AD. Användaren kan inte matchas synkroniserade till det befintliga objektet. Det finns ingen direkt metod för att mappa om den **Källfästpunkten**. Läs mer den [befintliga kunskapsbas](https://support.microsoft.com/help/2647098). 

Exempelvis bevarar det befintliga objektet i Azure AD Joe licens. En nyligen synkroniserade objekt med en annan **Källfästpunkten** sker i ett duplicerat attributtillstånd i Azure AD. Ändringar för Joe i lokala Active Directory tillämpas inte på Toms ursprungliga användare (befintliga objekt) i Azure AD.  

![Diagnostisera fel överblivna objektet scenario för Lösenordssynkronisering](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnostik- och felsökningssteg i Connect Health 
Diagnostisera-funktionen stöder användarobjekt med följande Duplicerat attribut:

| Attributets namn | Synkronisering feltyper|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique eller AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique eller AttributeValueMustBeUnique | 
| sipProxyAddress | AttributeValueMustBeUnique | 
| onPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Åtkomst till den här funktionen **Global administratör** behörighet, eller **deltagare** krävs behörighet från RBAC-inställningarna.
>

Följ instruktionerna från Azure portal för att begränsa felinformationen synkronisering och ger mer specifika lösningar:

![Synkronisera fel diagnos steg](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

Vidta några åtgärder för att identifiera specifika scenarier som går att åtgärda från Azure-portalen:  
1.  Kontrollera den **diagnostisera status** kolumn. Status visas om det finns ett sätt att åtgärda ett synkroniseringsfel direkt från Azure Active Directory. Med andra ord ett felsökning flöde finns som kan begränsa fallet felet och åtgärda den.
| Status | Vad innebär det? |
| ------------------ | -----------------|
| Inte startad | Du inte har besökt diagnos processen. Beroende på resultatet av diagnostiska och finns det en möjliga sättet att åtgärda synkroniseringsfel direkt från portalen. |
| Manuell korrigering krävs | Felet uppfyller inte villkoren för korrigeringsfiler från portalen. Antingen motstridiga objekttyper inte användare, eller du har gått redan igenom stegen för diagnostik och det fanns ingen korrigering-lösning från portalen. I det senare fallet är en korrigeringsfil från den lokala sidan fortfarande ett av lösningarna. [Läs mer om lokala korrigeringar](https://support.microsoft.com/help/2647098). | 
| Väntar på synkronisering | En korrigering har tillämpats. Portalen väntar på nästa synkronisering cykel att rensa felet. |
  >[!IMPORTANT]
  > Kolumnen diagnostisk status återställs efter varje synkronisering cykel. 
  >

2.  Välj den **diagnostisera** knappen under felinformationen. Du besvara några frågor och identifiera felinformationen synkronisering. Svar på frågor att identifiera ett ärende överblivna objektet.

3.  Om en **Stäng** knappen visas i slutet av diagnostik, det finns ingen snabbkorrigering från portalen baserat på dina svar. Referera till den lösning som visas i det sista steget. Korrigeringar från lokala är fortfarande lösningarna. Välj den **Stäng** knappen. Status för det aktuella synkroniseringsfelet växlar till **Manuell korrigering som krävs för**. Status förblir under den aktuella sync-cykeln.

4.  Efter ett ärende överblivna objektet identifieras kan du åtgärda Duplicerat attribut synkroniseringsfel direkt från portalen. För att starta processen, Välj den **gäller åtgärda** knappen. Status för aktuella sync fel uppdateringarna **väntande sync**.

5.  Efter nästa synkronisering cykeln bör felet tas bort från listan.

## <a name="how-to-answer-the-diagnosis-questions"></a>Hur du besvara frågor diagnos 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Finns det användaren i din lokala Active Directory?

Den här frågan försöker identifiera källobjektet för den befintliga användaren från lokala Active Directory.  
1.  Kontrollera om Azure Active Directory har ett objekt med angiven **UserPrincipalName**. Om du inte svara **nr**.
2.  Om den finns, kontrollera om objektet är kvar i omfånget för synkronisering.  
  - Sök i Azure AD-anslutningsplatsen med hjälp av det unika namnet.
  - Om objektet finns i den **väntande lägga till** tillstånd, besvara **nr**. Azure AD Connect kan inte ansluta objektet till höger Azure AD-objekt.
  - Om det inte att hitta objektet, svara **Ja**.

I det här exemplet frågan försöker identifiera om **Joe Jackson** fortfarande finns i lokala Active Directory.
För den **vanligt scenario**, både användare **Joe Johnson** och **Joe Jackson** finns i lokala Active Directory. I karantän objekten är två olika användare.

![Diagnostisera fel vanligt scenario för Lösenordssynkronisering](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

För den **överblivna objektet scenariot**, endast enanvändarläge **Joe Johnson** finns i lokala Active Directory:

![Diagnostisera sync fel överblivna objektet * finns användaren * scenario](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Båda dessa konton hör till samma användare?
Den här frågan kontrollerar en inkommande motstridiga användare och befintliga användarobjektet i Azure AD för att se om de hör till samma användare.  
1.  Motstridiga objektet synkroniseras nyligen till Azure Active Directory. Jämför objektens attribut:  
  - Visningsnamn
  - Användarens huvudnamn
  - Objekt-ID
2.  Om Azure AD inte jämföra dem, kontrollera om Active Directory har objekt med angiven **UserPrincipalNames**. Svaret **nr** om du hittar båda.

I följande exempel visas två objekt som hör till samma användare **Joe Johnson**.

![Diagnostisera sync fel överblivna objektet * samma användare * scenario](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Vad händer när den används i scenariot överblivna objekt
Utifrån svaren på ovanstående frågor, ser du den **gäller åtgärda** knappen när det finns en korrigeringsfil från Azure AD. I det här fallet synkroniserar lokalt objekt med en oväntad Azure AD-objekt. De två objekten mappas med hjälp av den **Källfästpunkten**. Den **gäller åtgärda** ändringen träder dessa eller liknande steg:
1. Uppdateringar av **Källfästpunkten** till rätt objekt i Azure AD.
2. Om den finns tar du bort det motstridiga objektet i Azure AD.

![Diagnostisera synkroniseringsfel efter korrigering](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> Den **gäller åtgärda** ändringen gäller bara överblivna objektet.
>

Användaren kan komma åt den ursprungliga resursen är en länk till ett befintligt objekt efter föregående steg. Den **diagnostisera status** uppdateras värdet i listan till **väntande Sync**. Fel vid synkronisering kommer att matcha efter nästa synkronisering. Ansluta hälsotillstånd kommer inte längre visa löst sync-fel i listvyn.


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
**F.** Vad händer om körningen av den **gäller åtgärda** misslyckas?  
**S.** Om det går inte att köra, är det möjligt att Azure AD Connect körs ett export-fel. Uppdatera portalsidan och försök igen efter nästa synkronisering. Standard-synkronisering cykel är 30 minuter. 


**F.** Vad händer om den **befintligt objekt** bör vara objekt som ska tas bort?  
**S.** Om den **befintligt objekt** ska tas bort, inte processen innebär en ändring av **Källfästpunkten**. Vanligtvis kan du åtgärda det från lokala Active Directory. 


**F.** Vilken behörighet för en användare behöver installera snabbkorrigeringen för?  
**S.** **Global administratör**, eller **deltagare** från RBAC-inställningarna har behörighet att komma åt diagnostiken och felsökningsprocessen.


**F.** Måste jag konfigurera Azure AD Connect eller uppdatera Azure AD Connect Health agent för den här funktionen?  
**S.** Nej, diagnos processen är en fullständig molnbaserade funktion.


**F.** Om det befintliga objektet är ej permanent borttagen diagnos processen gör objektet active igen?  
**S.** Nej, korrigering inte uppdatera objektattribut än **Källfästpunkten**.
