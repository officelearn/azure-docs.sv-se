---
title: Azure AD Connect Health - Diagnostisera dubblettattributsynkroniseringsfel | Microsoft-dokument
description: I det här dokumentet beskrivs diagnosprocessen för dubblettat attributsynkroniseringsfel och en potentiell korrigering av de överblivna objektscenarier direkt från Azure-portalen.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48ed9abf3e088e2581a3dd81b7c89e6b99da3ceb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897188"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnostisera och åtgärda synkroniseringsfel med duplicerade attribut

## <a name="overview"></a>Översikt
Azure Active Directory (Azure AD) Connect Health tar ett steg längre för att markera synkroniseringsfel och introducerar självbetjäningskorrigerande åtgärder. Den felsöker dubblerade attribut synkroniseringsfel och fixar objekt som är överblivna från Azure AD.
Diagnosfunktionen har följande fördelar:
- Det ger en diagnostisk procedur som begränsar dubbletter av attributsynkroniseringsfel. Och det ger specifika korrigeringar.
- Den tillämpar en korrigering för dedikerade scenarier från Azure AD för att lösa felet i ett enda steg.
- Ingen uppgradering eller konfiguration krävs för att aktivera den här funktionen.
Mer information om Azure AD finns i [Identitetssynkronisering och dubblettattributåterslutning](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Problem
### <a name="a-common-scenario"></a>Ett vanligt scenario
När **synkroniseringsfel i KarantänAttributeValueMustBeUnique** och **AttributeValueMustBeUnique** inträffar är det vanligt att du ser en **UserPrincipalName-** eller **Proxy Addresses-konflikt** i Azure AD. Du kan lösa synkroniseringsfelen genom att uppdatera källobjektet i konflikt från den lokala sidan. Synkroniseringsfelet kommer att lösas efter nästa synkronisering. Den här bilden anger till exempel att två användare har en konflikt mellan **användarens principnamn**. Båda är **Joe.J\@contoso.com.** De objekt som står i konflikt sätts i karantän i Azure AD.

![Diagnostisera vanligt scenario för synkroniseringsfel](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scenario för överblivna objekt
Ibland kan det hända att en befintlig användare förlorar **källankaret**. Borttagningen av källobjektet inträffade i lokal Active Directory. Men ändringen av borttagningssignalen synkroniserades aldrig till Azure AD. Den här förlusten inträffar av skäl som synkroniseringsmotorproblem eller domänmigrering. När samma objekt återställs eller återskapas logiskt sett bör en befintlig användare vara användaren som ska synkroniseras från **källankaret**. 

När en befintlig användare är ett objekt med endast molnet kan du även se den användare som står i konflikt synkroniserad med Azure AD. Användaren kan inte matchas synkroniserad med det befintliga objektet. Det finns inget direkt sätt att mappa om **källankaret**. Läs mer om den [befintliga kunskapsbasen](https://support.microsoft.com/help/2647098). 

Som ett exempel bevarar det befintliga objektet i Azure AD licensen för Joe. Ett nyligen synkroniserat objekt med ett annat **källankare** förekommer i ett duplicerat attributtillstånd i Azure AD. Ändringar för Joe i lokal Active Directory tillämpas inte på Joes ursprungliga användare (befintligt objekt) i Azure AD.  

![Diagnostisera ignorerat objektscenario för synkroniseringsfel](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Snabbsöknings- och felsökningssteg i Connect Health 
Diagnosfunktionen stöder användarobjekt med följande duplicerade attribut:

| Attributnamn | Feltyper för synkronisering|
| ------------------ | -----------------|
| UserPrincipalName | KarantänAttributeValueMustBeUnique eller AttributeValueMustBeUnique | 
| ProxyAddresses | KarantänAttributeValueMustBeUnique eller AttributeValueMustBeUnique | 
| SipProxyAddress | AttributVärdeMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributVärdeMustBeUnique |

>[!IMPORTANT]
> För att komma åt den här funktionen krävs **global administratörsbehörighet** eller **deltagarbehörighet** från RBAC-inställningarna.
>

Följ stegen från Azure-portalen för att begränsa synkroniseringsfelinformationen och tillhandahålla mer specifika lösningar:

![Steg för feldiagnos för synkronisering](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Från Azure-portalen bör du vidta några åtgärder för att identifiera specifika korrigeringsbara scenarier:  
1.  Kontrollera kolumnen **Diagnostisera status.** Statusen visar om det finns ett möjligt sätt att åtgärda ett synkroniseringsfel direkt från Azure Active Directory. Med andra ord finns det ett felsökningsflöde som kan begränsa felfallet och eventuellt åtgärda det.

| Status | Vad betyder det? |
| ------------------ | -----------------|
| Inte startat | Du har inte besökt den här diagnosprocessen. Beroende på det diagnostiska resultatet finns det ett potentiellt sätt att åtgärda synkroniseringsfelet direkt från portalen. |
| Manuell korrigering krävs | Felet passar inte kriterierna för tillgängliga korrigeringar från portalen. Antingen är objekttyper i konflikt inte användare eller så har du redan gått igenom diagnostikstegen och ingen korrigeringslösning var tillgänglig från portalen. I det senare fallet är en fix från den lokala sidan fortfarande en av lösningarna. [Läs mer om lokala korrigeringar](https://support.microsoft.com/help/2647098). | 
| Väntande synkronisering | En korrigering tillämpades. Portalen väntar på att nästa synkroniseringscykel ska rensa felet. |

  >[!IMPORTANT]
  > Kolumnen för diagnostikstatus återställs efter varje synkroniseringscykel. 
  >

1. Välj **knappen Diagnostisera** under felinformationen. Du svarar på några frågor och identifierar synkroniseringsfelinformationen. Svar på frågorna hjälper till att identifiera ett övergivet objektfall.

1. Om en **Stäng-knapp** visas i slutet av diagnostiken finns det ingen snabbkorrigering tillgänglig från portalen baserat på dina svar. Se lösningen som visas i det sista steget. Korrigeringar från lokala är fortfarande lösningarna. Välj knappen **Stäng.** Statusen för det aktuella synkroniseringsfelet växlar till **Manuell korrigering som krävs**. Statusen kvarstår under den aktuella synkroniseringscykeln.

1. När ett överblivet objektfall har identifierats kan du åtgärda de duplicerade attributens synkroniseringsfel direkt från portalen. Om du vill utlösa processen väljer du knappen **Använd korrigering.** Statusen för det aktuella synkroniseringsfelet uppdateras till **Väntande synkronisering**.

1. Efter nästa synkroniseringscykel ska felet tas bort från listan.

## <a name="how-to-answer-the-diagnosis-questions"></a>Hur man svarar på diagnosfrågor 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Finns användaren i din lokala Active Directory?

Den här frågan försöker identifiera källobjektet för den befintliga användaren från lokala Active Directory.  
1. Kontrollera om Azure Active Directory har ett objekt med det medföljande **UserPrincipalName**. Om inte, svara **nej**.
2. Om den gör det kontrollerar du om objektet fortfarande är i omfånget för synkronisering.  
   - Sök i Azure AD-anslutningsutrymmet med hjälp av DN.
   - Om objektet hittas i tillståndet **Väntande lägg** till svarar **du nej**. Azure AD Connect kan inte ansluta objektet till rätt Azure AD-objekt.
   - Om objektet inte hittas svarar **du Ja**.

I de här exemplen försöker frågan identifiera om **Joe Jackson** fortfarande finns i lokal Active Directory.
För det **gemensamma scenariot**finns både användarna **Joe Johnson** och **Joe Jackson** i lokala Active Directory. Objekten i karantän är två olika användare.

![Diagnostisera vanligt scenario för synkroniseringsfel](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

För **det överblivna objektscenariot**finns endast den enda användaren **Joe Johnson** i lokal Active Directory:

![Diagnostisera fel överblivna objekt *finns användaren* scenario](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Tillhör båda dessa konton samma användare?
Den här frågan kontrollerar en inkommande användare i konflikt och det befintliga användarobjektet i Azure AD för att se om de tillhör samma användare.  
1. Objektet i konflikt synkroniseras nyligen till Azure Active Directory. Jämför objektens attribut:  
   - Visningsnamn
   - User Principal Name
   - Objekt-ID
2. Om Azure AD inte kan jämföra dem kontrollerar du om Active Directory har objekt med de medföljande **UserPrincipalNames**. Svar **nej** om du hittar båda.

I följande exempel tillhör de två objekten samma användare **Joe Johnson**.

![Diagnostisera ignorerat synkroniseringsfel *samma användarscenario*](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Vad händer efter att korrigeringen har tillämpats i det överblivna objektscenariot
Baserat på svaren på föregående frågor visas knappen **Använd korrigering** när det finns en korrigering tillgänglig från Azure AD. I det här fallet synkroniseras det lokala objektet med ett oväntat Azure AD-objekt. De två objekten mappas med hjälp av **källankaret**. Ändringen **Använd korrigering** tar dessa eller liknande steg:
1. Uppdaterar **källankaret** till rätt objekt i Azure AD.
2. Tar bort det objekt som står i konflikt i Azure AD om det finns.

![Diagnostisera synkroniseringsfel efter korrigeringen](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> **Ändring av Använd korrigering** gäller endast för överblivna objektfall.
>

Efter föregående steg kan användaren komma åt den ursprungliga resursen, som är en länk till ett befintligt objekt. Statusvärdet **för diagnostisera** i listvyn uppdateras till **Väntande synkronisering**. Synkroniseringsfelet kommer att lösas efter nästa synkronisering. Connect Health visar inte längre det lösta synkroniseringsfelet i listvyn.

## <a name="failures-and-error-messages"></a>Fel och felmeddelanden
**Användare med attribut i konflikt tas bort mjukt i Azure Active Directory. Kontrollera att användaren har tagits bort hårt innan du försöker igen.**  
Användaren med attribut i konflikt i Azure AD bör rensas innan du kan använda korrigeringen. Kolla in [hur du tar bort användaren permanent i Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) innan du försöker korrigeringen igen. Användaren kommer också att raderas permanent efter 30 dagar i mjukt borttaget tillstånd. 

**Det går inte att uppdatera källankaret till molnbaserad användare i klienten.**  
Molnbaserad användare i Azure AD bör inte ha källankare. Det går inte att uppdatera källankaret i det här fallet. Manuell korrigering krävs från lokalt. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
**F.** Vad händer om körningen av **Apply Fix** misslyckas?  
**A.** Om körningen misslyckas är det möjligt att Azure AD Connect kör ett exportfel. Uppdatera portalsidan och försök igen efter nästa synkronisering. Standardsynkroniseringscykeln är 30 minuter. 


**F.** Vad händer om det **befintliga objektet** ska vara det objekt som ska tas bort?  
**A.** Om det **befintliga objektet** ska tas bort innebär processen inte en ändring av **källankretsen**. Vanligtvis kan du åtgärda det från lokala Active Directory. 


**F.** Vilken behörighet behöver en användare för att använda korrigeringen?  
**A.** **Global admin**, eller **deltagare** från RBAC-inställningarna, har behörighet att komma åt diagnostik- och felsökningsprocessen.


**F.** Måste jag konfigurera Azure AD Connect eller uppdatera Azure AD Connect Health-agenten för den här funktionen?  
**A.** Nej, diagnosprocessen är en komplett molnbaserad funktion.


**F.** Om det befintliga objektet är mjukt borttaget, kommer diagnosprocessen att göra objektet aktivt igen?  
**A.** Nej, korrigeringen uppdaterar inte andra objektattribut än **Källankring**.
