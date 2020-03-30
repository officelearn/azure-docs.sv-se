---
title: Etablera appar med omfångsfilter | Microsoft-dokument
description: Lär dig hur du använder omfångsfilter för att förhindra att objekt i appar som stöder automatisk användaretablering etableras om ett objekt inte uppfyller dina affärskrav.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c667409f2abb9f1cf89ae3b34f08e0f9eec067e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138543"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Attributbaserad programetablering med omfångsfilter
Syftet med den här artikeln är att förklara hur du använder omfångsfilter för att definiera attributbaserade regler som avgör vilka användare som etableras i ett program.

## <a name="scoping-filter-use-cases"></a>Omfångsfilteranvändningsfall

Med ett omfångsfilter kan etableringstjänsten Azure Active Directory (Azure AD) inkludera eller utesluta användare som har ett attribut som matchar ett visst värde. När du till exempel etablerar användare från Azure AD till ett SaaS-program som används av ett säljteam kan du ange att endast användare med attributet "Avdelning" i "Försäljning" ska vara i omfånget för etablering.

Omfångsfilter kan användas på olika sätt beroende på vilken typ av etableringskoppling:

* **Utgående etablering från Azure AD till SaaS-program**. När Azure AD är källsystemet är [användar- och grupptilldelningar](../manage-apps/assign-user-or-group-access-portal.md) den vanligaste metoden för att avgöra vilka användare som omfattas av etablering. Dessa tilldelningar används också för att aktivera enkel inloggning och tillhandahålla en enda metod för att hantera åtkomst och etablering. Omfångsfilter kan användas som tillval, förutom tilldelningar eller i stället för dem, för att filtrera användare baserat på attributvärden.

    >[!TIP]
    > Du kan inaktivera etablering baserat på tilldelningar för ett företagsprogram genom att ändra inställningarna på [Scope-menyn](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) under etableringsinställningarna för att **synkronisera alla användare och grupper**. Om du använder det här alternativet plus attributbaserade omfångsfilter får du snabbare prestanda än att använda gruppbaserade tilldelningar.  

* **Inkommande etablering från HCM-program till Azure AD och Active Directory**. När ett [HCM-program som Workday](../saas-apps/workday-tutorial.md) är källsystemet är omfångsfilter den primära metoden för att avgöra vilka användare som ska etableras från HCM-programmet till Active Directory eller Azure AD.

Som standard har Azure AD-etableringsappar inga attributbaserade omfångsfilter konfigurerade. 

## <a name="scoping-filter-construction"></a>Omfång filterkonstruktion

Ett omfångsfilter består av en eller flera *satser*. Satser avgör vilka användare som får passera genom omfångsfiltret genom att utvärdera varje användares attribut. Du kan till exempel ha en sats som kräver att en användares "State"-attribut är lika med "New York", så att endast New York-användare etableras i programmet. 

En enda sats definierar ett enda villkor för ett enda attributvärde. Om flera satser skapas i ett enda omfångsfilter utvärderas de tillsammans med hjälp av "OCH"-logik. Detta innebär att alla klausuler måste utvärderas till "true" för att en användare ska kunna etableras.

Slutligen kan flera omfångsfilter skapas för ett enda program. Om det finns flera omfångsfilter utvärderas de tillsammans med hjälp av "ELLER"-logik. Detta innebär att om alla satser i något av de konfigurerade omfångsfiltren utvärderas till "true", etableras användaren.

Varje användare eller grupp som bearbetas av Azure AD-etableringstjänsten utvärderas alltid individuellt mot varje omfångsfilter.

Tänk på följande omfångsfilter:

![Omfångsfilter](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

Enligt detta omfångsfilter måste användarna uppfylla följande kriterier som ska etableras:

* De måste vara i New York.
* De måste arbeta på maskinavdelningen.
* Deras företagsanställd id måste vara mellan 1 000 000 och 2 000 000.
* Deras befattning får inte vara null eller tom.

## <a name="create-scoping-filters"></a>Skapa omfångsfilter
Omfångsfilter konfigureras som en del av attributmappningarna för varje Azure AD-användaretableringskoppling. Följande procedur förutsätter att du redan har konfigurerat automatisk etablering för [ett av de program som stöds](../saas-apps/tutorial-list.md) och lägger till ett omfångsfilter i det.

### <a name="create-a-scoping-filter"></a>Skapa ett omfångsfilter
1. Gå till avsnittet Azure **Active Directory** > **Enterprise Applications** > **I** Azure [Portal](https://portal.azure.com).

2. Välj det program som du har konfigurerat automatisk etablering för: till exempel "ServiceNow".

3. Välj fliken **Etablering.**

4. I avsnittet **Mappningar** väljer du den mappning som du vill konfigurera ett omfångsfilter för: till exempel "Synkronisera Azure Active Directory Users to ServiceNow".

5. Markera menyn **Källobjektomfattning.**

6. Välj **Lägg till omfångsfilter**.

7. Definiera en sats genom att välja ett **källattributnamn**, en **operator**och ett **attributvärde** som ska matchas mot. Följande operatorer stöds:

   a. **LIKA MED**. Satsen returnerar "sant" om det utvärderade attributet matchar indatasträngvärdet exakt (skiftlägeskänsligt).

   b. **INTE LIKA**MED . Satsen returnerar "sant" om det utvärderade attributet inte matchar indatasträngvärdet (skiftlägeskänsligt).

   c. **ÄR SANT**. Satsen returnerar "sant" om det utvärderade attributet innehåller ett booleskt värde av sant.

   d. **ÄR FALSKT**. Satsen returnerar "sant" om det utvärderade attributet innehåller ett booleskt värde av false.

   e. **ÄR NULL**. Satsen returnerar "sant" om det utvärderade attributet är tomt.

   f. **ÄR INTE NULL**. Satsen returnerar "sant" om det utvärderade attributet inte är tomt.

   g. **REGEX MATCH**. Satsen returnerar "sant" om det utvärderade attributet matchar ett mönster för reguljära uttryck. Till exempel: ([1-9][0-9]) matchar valfritt tal mellan 10 och 99.

   h. **INTE REGEX MATCH**. Satsen returnerar "sant" om det utvärderade attributet inte matchar ett mönster för reguljära uttryck.
   
   i. **Greater_Than.** Satsen returnerar "sant" om det utvärderade attributet är större än värdet. Värdet som anges i omfångsfiltret måste vara ett heltal och attributet för användaren måste vara ett heltal [0,1,2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** Satsen returnerar "sant" om det utvärderade attributet är större än eller lika med värdet. Värdet som anges i omfångsfiltret måste vara ett heltal och attributet för användaren måste vara ett heltal [0,1,2,...]. 
   
   k. **Innehåller.** Satsen returnerar "sant" om det utvärderade attributet innehåller strängvärdet (skiftlägeskänsligt) enligt beskrivningen [här](https://docs.microsoft.com/dotnet/api/system.string.contains?view=netframework-4.8). 


>[!IMPORTANT] 
> - Filtret IsMemberOf stöds inte för tillfället.
> - EQUALS och NOT EQUALS stöds inte för attribut med flera värden

9. Upprepa eventuellt steg 7-8 för att lägga till fler omfångssatser.

10. Lägg till ett namn på filtret i **Omfångsfilterrubrik.**

11. Välj **OK**.

12. Välj **OK** igen på skärmen **Omfångsfilter.** Upprepa också steg 6-11 för att lägga till ytterligare ett omfångsfilter.

13. Välj **Spara** på skärmen **Attributmappning.** 

>[!IMPORTANT] 
> Om du sparar ett nytt omfångsfilter utlöses en ny fullständig synkronisering för programmet, där alla användare i källsystemet utvärderas igen mot det nya omfångsfiltret. Om en användare i programmet tidigare var i omfång för etablering, men faller utanför omfånget, inaktiveras eller avetableras deras konto i programmet. Om du vill åsidosätta det här standardbeteendet läser du [Hoppa över borttagning för användarkonton som inte omfattas](../app-provisioning/skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Vanliga omfångsfilter
| Målattribut| Operator | Värde | Beskrivning|
|----|----|----|----|
|userPrincipalName|REGEX MATCH|.\*@domain.com |Alla användare med userPrincipal @domain.com som har domänen kommer att vara i utrymme för etablering|
|userPrincipalName|INTE REGEX MATCH|.\*@domain.com|Alla användare med userPrincipal @domain.com som har domänen kommer att vara utanför omfånget för etablering|
|avdelning|Motsvarar|Försäljning|Alla användare från försäljningsavdelningen är i utrymme för etablering|
|workerID (arbetarID)|REGEX MATCH|(1[0-9][0-9][0-9][0-9][0-9][0-9])| Alla anställda med arbetar-ID mellan 1000000 och 2000000 är i utrymme för etablering.|

## <a name="related-articles"></a>Relaterade artiklar
* [Automatisera etablering av användare och avetablering till SaaS-program](../app-provisioning/user-provisioning.md)
* [Anpassa attributmappningar för användaretablering](../app-provisioning/customize-application-attributes.md)
* [Skriva uttryck för attributmappningar](functions-for-customizing-application-data.md)
* [Meddelanden om kontoetablering](../app-provisioning/user-provisioning.md)
* [Använd SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)

