---
title: Azure AD Connect Health - diagnostisera dupliceras synkroniseringsfel för attributet | Microsoft Docs
description: Det här dokumentet beskriver hur diagnostisera Duplicerat attribut synkroniseringsfel och potentiella fixa överblivna objektet scenarier direkt från Azure-portalen.
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
ms.openlocfilehash: 0a345fd3443fb33d6f5912c8a04677091e20ecc8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="duplicate-attribute-sync-errors-diagnosis-and-remediation"></a>Duplicera attribut sync fel diagnos och reparation 

## <a name="overview"></a>Översikt
Tar ytterligare ett steg av markering synkroniseringsfel lanserar Azure Active Directory Connect Health Avbrottsfritt hjälpåtgärder i självbetjäningen om du vill felsöka Duplicerat attribut synkroniseringsfel och korrigera överblivna objekt från Azure AD. Den viktigaste fördelen från funktionen diagnos:
- Ange diagnostik proceduren för att begränsa Duplicerat attribut sync fel scenarier och ange specifika lösningar
- Tillämpa korrigering för dedikerade scenarier från Azure AD för att åtgärda felet i ett enda klick
- Ingen uppgradering eller konfiguration krävs för att aktivera den här funktionen.
Läs mer om Azure AD [dubbletter återhämtning](https://aka.ms/dupattributeresdocs).

## <a name="problems"></a>Problem
### <a name="common-scenario"></a>Vanligt scenario
När **QuarantinedAttributeValueMustBeUnique** och **AttributeValueMustBeUnique** synkroniseringsfel uppstår är det vanligt att UPN-namnet eller proxyadresser i Azure AD. Du kan lösa synkroniseringsfel genom att uppdatera objektet motstridiga källa på lokala sida. Fel vid synkronisering kommer att matcha efter följande synkroniseringen. Bilden nedan visar exempelvis att två användare har av sin UserPrincipalName som *Joe.J@contoso.com*. Motstridiga objekt är i karantän i Azure AD. 

![Diagnostisera fel vanligt scenario för Lösenordssynkronisering](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Överblivna objektet scenario
Ibland kan du hitta en befintlig användare förlorar Källfästpunkten. Borttagningen av källobjektet har inträffat i lokala AD, men ändringen av borttagning signal aldrig fick synkroniseras till Azure AD. Det kan inträffa av olika orsaker, till exempel sync motorn problemet eller domän migrering. När samma objekt har återställts eller återskapas, ska logiskt befintliga användaren vara användare att synkronisera från Källfästpunkten. För befintliga användare som endast molnobjekt kan också se motstridiga användare synkroniseras till Azure AD och går inte att matcha synkroniserade till det befintliga objektet. Det finns ingen direkt metod för att mappa om Källfästpunkten. Läs mer om den [befintliga KB](https://support.microsoft.com/help/2647098). Till exempel bevarar det befintliga objektet i Azure AD Joe licens. Nyligen synkroniserat objekt med olika källfästpunkten uppstod i duplicerade attributet status i Azure AD. Ändringar av Joe i lokala AD kommer inte att kunna tillämpas på Toms ursprungliga användare (befintliga objekt) i Azure AD.  

![Diagnostisera fel överblivna objektet scenario för Lösenordssynkronisering](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnostik- och felsökningssteg i Connect Health 
Diagnostisera funktionen stöder användarobjekt med följande Duplicerat attribut:

| Attributnamn | Synkronisering feltyper|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique eller AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique eller AttributeValueMustBeUnique | 
| sipProxyAddress | AttributeValueMustBeUnique | 
| onPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Det krävs **Global administratör** behörighet eller **deltagare** från RBAC-inställningar för att kunna använda den här funktionen. 
>

Följande steg från Azure-portalen, kommer du att kunna begränsa felinformationen synkronisering och ger mer specifika lösningar:

![Diagnostisera Sync fel diagnostisera steg](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

Du kommer att kunna gå igenom några steg för att identifiera specifika scenarier som går att åtgärda från Azure-portalen:  
1.  I kolumnen för diagnostisera status visas status om det finns en potentiell felsökning flödar till begränsa fallet felet och åtgärda direkt från Azure Active Directory.
| Status | Vad innebär det? |
| ------------------ | -----------------|
| Inte startad | Du har inte besökt diagnos processen. Beror på diagnostiska resultatet finns eventuellt ett sätt att åtgärda synkroniseringsfel från portalen direkt. |
| Manuell korrigering krävs | Felet ryms inte i kriterierna för tillgänglig lösning från portalen. Fallet kan vara (1) i konflikt objekt typer inte är användare (2) du redan har gått igenom diagnostiken och ingen åtgärda upplösning som är tillgängliga från portalen. I det här fallet är korrigering från lokala sida fortfarande ett av lösningarna. [Läs mer om lokala korrigering](https://support.microsoft.com/help/2647098) | 
| Väntar på synkronisering | Korrigera tillämpades. Väntar på nästa synkronisering cykel att rensa felet. |
>[!IMPORTANT]
> Kolumnen diagnostisk status kommer att återställas efter varje synkronisering cykel. 
>

2.  Genom att klicka på **diagnostisera** knappen i informationsbladet fel behöver du besvara några frågor och identifiera felinformationen synkronisering. Besvara frågor hjälper dig identifiera i fallet med överblivna objektet scenario. 

3.  Om det finns en **Stäng** knappen längst ned i diagnostiken innebär det inte finns några snabbkorrigering från portalen baserat på angivna svaren. Referera till den lösning som visas i det sista steget. Korrigering från lokalt kommer fortfarande att lösningarna. När du klickar på knappen Stäng hittar du statusen för det aktuella synkroniseringsfelet växlar för att vara **Manuell korrigering som krävs för**. Status behåller under den aktuella synkroniseringscykeln.

4.  När överblivna objektet fallet identifieras, kan du åtgärda Duplicerat attribut synkroniseringsfel direkt från portalen. Klicka på den **gäller åtgärda** för att starta processen. Status för det aktuella synkroniseringsfelet uppdateras så att **väntande sync**.

5.  Felet bör tas bort från listan efter följande sync-cykeln.

## <a name="how-to-answer-the-diagnosis-questions"></a>Hur du besvara frågor diagnos 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Finns användaren i din lokala Active Directory?

Frågan försöker identifiera källobjektet för den befintliga användaren från lokala Active Directory.  
1.  Kontrollera om din Active Directory har ett objekt med den angivna UserPrincipalName. Om Nej, svarar Nej.
2.  Om så är fallet, så kontrollera om objektet fortfarande ligger i synkroniseringsområdet.  
  - Sök med DN i Azure AD-anslutningsappens utrymme.
  - Om objektet finns med i **väntande lägga till** tillstånd, svarar Nej. Azure AD Connect kan inte ansluta objektet till höger AD-objekt.
  - Om objektet inte finns, svarar Ja.

> Med följande diagram, till exempel frågan försöker identifiera om *Joe Jackson* finns kvar i lokala Active Directory.
För **vanligt scenario**, både användaren *Joe Johnson* och *Joe Jackson* kommer att finnas i din lokala Active Directory. I karantän objekten är två olika användare.

![Diagnostisera fel vanligt scenario för Lösenordssynkronisering](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

> För **överblivna objektet scenariot**, enskild användare – *Joe Johnson* är tillgänglig från lokala Active Directory:

![Diagnostisera fel överblivna objektet scenario för Lösenordssynkronisering](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-these-accounts-belong-to-the-same-user"></a>Tillhör bägge dessa konton samma användare?
Frågan kontrollerar inkommande motstridiga användar- och befintliga användarobjektet i Azure AD för att se om de hör till samma användare.  
1.  Motstridiga objektet synkroniseras nyligen till Azure Active Directory. Jämför objektet från dess:  
  - Visningsnamn
  - Användarens huvudnamn
  - Objekt-ID
2.  Om det gick inte att jämföra dem, kontrollera din Active Directory har objekt med den angivna UserPrincipalNames. Svarar Nej om båda finns.  

> I fallet nedan två objekt som hör till samma användare *Joe Johnson*.

![Diagnostisera fel överblivna objektet scenario för Lösenordssynkronisering](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happened-after-fix-is-applied-for-orphaned-object-scenario"></a>Vad hände när används för överblivna objektet scenario
Utifrån svaren upphöjt frågor du kommer att kunna se **gäller åtgärda** knappen när det finns en korrigeringsfil från Azure AD. I det här fallet på lokala objektet synkroniseras med en oväntad Azure AD-objekt. De två objekten mappas med hjälp av ”Källfästpunkten”. Tillämpa ändringen utför steg som:
- Uppdatera Källfästpunkten till rätt objekt i Azure AD.
- Ta bort motstridiga objektet i Azure AD om det innehåller.

![Diagnostisera synkroniseringsfel efter korrigering](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> Tillämpa åtgärda ändringen gäller enbart för fallen överblivna objektet.
>

Användaren kommer att kunna åtkomst till ursprungliga resurs, vilket är en länk till befintligt objekt efter stegen ovan. Den **diagnostisera status** värdet i listan uppdateras så att **väntande Sync**. Fel vid synkronisering kommer att matcha efter följande synkroniseringen. Ansluta hälsotillstånd visas inte lösts synkroniseringsfel från listvyn längre. 


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
 -  Vad hände om körningen av gäller misslyckades?  
Om det går inte att köra, är det möjligt Azure AD Connect kör export fel vid tidpunkten. Uppdatera portalsidan och försök igen efter följande synkronisering. Standard-synkroniseringscykel är 30 minuter. 

 -  Vad händer om den **befintligt objekt** bör vara objekt som ska tas bort?  
Om det befintliga objektet ska tas bort i det här fallet, inbegriper inte ändring av Källfästpunkt i processen. Du bör kunna åtgärda från din lokala AD.  

 -  Vad är behörigheten för användaren för att kunna installera snabbkorrigeringen för?  
Global administratör eller deltagare från RBAC inställningar har behörighet att komma åt diagnostiken och felsökningsprocessen.

 -  Jag behöver config AAD Connect eller uppdaterar Azure AD Connect Health agent för den här funktionen?  
Nej, diagnos processen är en fullständig molnbaserade funktion.

 -  Om det befintliga objektet är ej permanent borttagen, diagnostisera processen återställs objekt om du vill vara aktiv igen?  
Nej, korrigering inte att uppdatera attributet än Källfästpunkt. 
