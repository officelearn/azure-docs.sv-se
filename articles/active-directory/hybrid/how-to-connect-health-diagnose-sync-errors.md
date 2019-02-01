---
title: Azure AD Connect Health - diagnostisera Duplicerat attribut synkroniseringsfel | Microsoft Docs
description: Det här dokumentet beskriver processen för diagnos av Duplicerat attribut synkroniseringsfel och en potentiell korrigering av överblivna objektet scenarier direkt från Azure-portalen.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 7f367049cda76eea2dfb3040f714a7050a26520e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495912"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnostisera och åtgärda synkroniseringsfel Duplicerat attribut

## <a name="overview"></a>Översikt
Azure Active Directory (Azure AD) Connect Health introducerar tar ett steg längre för att markera synkroniseringsfel kan hjälpåtgärder. Den felsöker Duplicerat attribut synkroniseringsfel och korrigeringar objekt som kopplas från Azure AD.
Funktionen diagnos har följande fördelar:
- Det ger en diagnostisk procedur som begränsar Duplicerat attribut synkroniseringsfel. Och det ger specifik korrigeringar.
- Det gäller en korrigering för dedikerade scenarier från Azure AD för att åtgärda felet i ett enda steg.
- Ingen uppgradering eller konfiguration krävs för att aktivera den här funktionen.
Läs mer om Azure AD, [identitet identitetssynkronisering och duplicerad attributåterhämtning](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Problem
### <a name="a-common-scenario"></a>Ett vanligt scenario
När **QuarantinedAttributeValueMustBeUnique** och **AttributeValueMustBeUnique** synkroniseringsfel uppstår är det vanligt att se en **UserPrincipalName** eller **Proxyadresser** konflikten i Azure AD. Du kan lösa synkroniseringsfel genom att uppdatera motstridiga källobjektet från den lokala sidan. Fel vid synkronisering kommer att matcha efter nästa synkronisering. Den här bilden anger till exempel att två användare har en konflikt mellan sina **UserPrincipalName**. Båda är **Joe.J@contoso.com**. Motstridiga objekt har satts i karantän i Azure AD.

![Diagnostisera fel vanligt scenario för Lösenordssynkronisering](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Överblivna objektet scenario
Ibland kanske du upptäcker att en befintlig användare förlorar den **Källfästpunkt**. Borttagningen av källobjektet inträffade i den lokala Active Directory. Men ändringen av borttagning av signalen aldrig fick synkroniseras till Azure AD. Den här förlusten sker av skäl som motorn synkroniseringsproblem eller domän migrering. När samma objekt hämtar återställts eller återskapas, logiskt, en befintlig användare ska vara användare ska synkroniseras från den **Källfästpunkt**. 

När en befintlig användare är en molnbaserad objekt, kan du också se motstridiga användaren synkroniseras till Azure AD. Användaren kan inte matchas synkroniserade till det befintliga objektet. Det finns inget enkelt sätt att mappa om den **Källfästpunkt**. Läs mer om den [befintliga kunskapsbas](https://support.microsoft.com/help/2647098). 

Till exempel bevarar det befintliga objektet i Azure AD en licens för Joe. Ett nyligen synkroniserade objekt med en annan **Källfästpunkt** sker i ett tillstånd med Duplicerat attribut i Azure AD. Ändringar för Joe i den lokala Active Directory tillämpas inte på Josefs ursprungliga användaren (befintliga objekt) i Azure AD.  

![Diagnostisera fel överblivna objektet scenario för Lösenordssynkronisering](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnostik och felsökning i Connect Health 
Diagnostisera-funktionen stöder användarobjekt med följande duplicerade attribut:

| Attributnamn | Typ av synkronisering|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique eller AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique eller AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Till den här funktionen **Global administratör** behörighet, eller **deltagare** behörighet från RBAC-inställningar.
>

Följ stegen från Azure portal för att begränsa felinformation synkronisering och tillhandahålla mer specifika lösningar:

![Synkronisera fel diagnos steg](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Vidta några åtgärder för att identifiera specifika scenarier som går att åtgärda från Azure-portalen  
1.  Kontrollera den **diagnostisera status** kolumn. Statusen visar om det finns ett sätt att åtgärda ett synkroniseringsfel direkt från Azure Active Directory. Med andra ord en felsökning flödet finns som kan begränsa felsituation och åtgärda den.
| Status | Vad betyder det? |
| ------------------ | -----------------|
| Inte startad | Du har inte besökt diagnos processen. Beroende på diagnostiska resultatet är det en potentiell sättet att åtgärda synkroniseringsfel direkt från portalen. |
| Manuell korrigering krävs | Felet passar inte villkor korrigeringsfiler från portalen. Antingen motstridiga objekt av typen inte användare, eller du har gått redan igenom stegen för diagnostik och det fanns ingen korrigering-lösning från portalen. I det senare fallet finns en korrigering från den lokala sidan fortfarande lösningarna. [Läs mer om lokala korrigeringar](https://support.microsoft.com/help/2647098). | 
| Väntar på synkronisering | En korrigering har tillämpats. Portalen väntar på nästa synkroniseringscykel att rensa felet. |
  >[!IMPORTANT]
  > Diagnostisk status-kolumnen återställs efter varje synkroniseringscykel. 
  >

2.  Välj den **diagnostisera** knappen under felinformationen. Du besvara några frågor och identifiera felinformation synkronisering. Svar på frågor hjälper dig att identifiera ett fall med överblivna objektet.

3.  Om en **Stäng** knappen visas i slutet av diagnostik, det finns inga snabbkorrigering från portalen utifrån dina svar. Referera till lösningen som visas i det sista steget. Korrigeringar från den lokala är fortfarande lösningarna. Välj den **Stäng** knappen. Status för det aktuella synkroniseringsfelet växlar till **Manuell korrigering krävs**. Statusen kvar under den aktuella synkroniseringscykel.

4.  Efter ett fall med överblivna objektet identifieras kan du åtgärda duplicerade attribut synkroniseringsfel direkt från portalen. Om du vill starta processen, Välj den **åtgärda problem** knappen. Status för de aktuella synkronisering fel uppdateringarna till **väntar på synkronisering**.

5.  Felet ska tas bort från listan efter nästa synkroniseringscykel.

## <a name="how-to-answer-the-diagnosis-questions"></a>Hur de ska svara på frågor om diagnostik 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Så att användaren finns i din lokala Active Directory?

Den här frågan försöker identifiera källobjektet för den befintliga användaren från en lokal Active Directory.  
1.  Kontrollera om Azure Active Directory har ett objekt med angiven **UserPrincipalName**. Om den inte svarar **nr**.
2.  I annat fall måste du kontrollera om objektet är fortfarande i omfånget för synkronisering.  
  - Sök i anslutarplatsen för Azure AD med hjälp av det unika namnet.
  - Om objektet finns i den **väntande Lägg till** tillstånd, besvara **nr**. Azure AD Connect kan inte ansluta objektet till rätt Azure AD-objekt.
  - Om objektet inte hittas besvara **Ja**.

I det här frågan försöker identifiera om **Joe Jackson** fortfarande finns i den lokala Active Directory.
För den **vanligt scenario**, både användare **Joe Johnson** och **Joe Jackson** finns i den lokala Active Directory. I karantän objekten är två olika användare.

![Diagnostisera fel vanligt scenario för Lösenordssynkronisering](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

För den **överblivna objektet scenariot**, endast enanvändarläge **Joe Johnson** finns i den lokala Active Directory:

![Diagnostisera synkronisering överblivna felobjektet * finns användare * scenario](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Båda kontona hör till samma användare?
Den här frågan kontrollerar en inkommande motstridiga användare och Dete befintliga användarobjekt i Azure AD för att se om de hör till samma användare.  
1.  Objektet i konflikt har nyligen synkroniserats till Azure Active Directory. Jämför objektets attribut:  
  - Visningsnamn
  - User Principal Name
  - Objekt-ID
2.  Om Azure AD inte kan jämföra dem, kontrollerar om Active Directory innehåller objekt med de angivna **UserPrincipalNames**. Svar **nr** om du hittar båda.

I följande exempel visas de två objekten som hör till samma användare **Joe Johnson**.

![Diagnostisera synkronisering överblivna felobjektet * samma användare * scenario](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Vad händer när den används i scenariot överblivna objekt
Utifrån svaren på föregående frågorna visas den **åtgärda problem** knappen när en korrigering är tillgänglig från Azure AD. I det här fallet synkroniserar lokala objektet med ett oväntat Microsoft Azure AD-objekt. De två objekten mappas med hjälp av den **Källfästpunkt**. Den **åtgärda problem** träder dessa eller liknande steg:
1. Uppdateringar i **Källfästpunkt** till rätt objekt i Azure AD.
2. Tar bort objektet i konflikt i Azure AD om den finns.

![Diagnostisera synkroniseringsfel efter korrigering](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> Den **åtgärda problem** ändringen gäller bara överblivna objektet.
>

Användaren kan komma åt den ursprungliga resursen är en länk till ett befintligt objekt efter föregående steg. Den **diagnostisera status** uppdateras värdet i listvyn till **väntar på synkronisering**. Fel vid synkronisering kommer att matcha efter nästa synkronisering. Connect Health kommer kom inte längre löst sync-fel i listvyn.

## <a name="failures-and-error-messages"></a>Fel- och felmeddelanden
**Användare med attribut i konflikt är Mjuk tas bort i Azure Active Directory. Se till att användaren är hårda borttagen innan du kan utföra.**  
Användaren med attribut som är i konflikt i Azure AD ska rensas innan du kan tillämpa korrigeringen. Kolla in [hur du tar bort användaren permanent i Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) innan du försöker korrigeringen. Användaren tas också automatiskt bort permanent efter 30 dagar i ej permanent Borttaget tillstånd. 

**Uppdatera källfästpunkt till molnbaserade användare i din klientorganisation stöds inte.**  
Molnbaserad användaren i Azure AD ska inte ha källfästpunkt. Uppdatera källfästpunkt stöds inte i det här fallet. Manuell korrigering krävs från lokalt. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
**F.** Vad händer om körningen av den **åtgärda problem** misslyckas?  
**S.** Om det går inte att köra, är det möjligt att Azure AD Connect körs ett exportfel. Uppdatera portalsidan och försök igen efter nästa synkronisering. Synkroniseringscykel standard är 30 minuter. 


**F.** Vad händer om den **befintligt objekt** bör vara objekt som ska tas bort?  
**S.** Om den **befintligt objekt** ska tas bort, inte processen innebär en ändring av **Källfästpunkt**. Du kan vanligtvis åtgärda det från den lokala Active Directory. 


**F.** Vilka behörigheter behöver en användare att tillämpa korrigeringen?  
**S.** **Global administratör**, eller **deltagare** från RBAC-inställningar har behörighet att komma åt diagnostiken och felsökning av processen.


**F.** Måste jag konfigurera Azure AD Connect eller uppdatera Azure AD Connect Health-agenten för den här funktionen?  
**S.** Nej, diagnos processen är en fullständig molnbaserad funktion.


**F.** Om det befintliga objektet är ej permanent borttagen, diagnos processen gör objektet active igen?  
**S.** Nej, korrigeringen inte uppdatera objektattribut än **Källfästpunkt**.
