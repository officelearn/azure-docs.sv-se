---
title: Etablera appar med omfångs filter | Microsoft Docs
description: Lär dig hur du använder omfångs filter för att förhindra att objekt i appar som har stöd för automatisk användar etablering är etablerade om ett objekt inte uppfyller dina affärs krav.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82360dacd68de512bc12ff5d39ddbd3a21578aa7
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74120112"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Attributbaserade program etablering med omfångs filter
Syftet med den här artikeln är att förklara hur du använder omfångs filter för att definiera attributbaserade regler som avgör vilka användare som ska tillhandahållas till ett program.

## <a name="scoping-filter-use-cases"></a>Användnings fall för omfattnings filter

Ett scope-filter gör att Azure Active Directory (Azure AD) etablerings tjänsten kan ta med eller undanta användare som har ett attribut som matchar ett angivet värde. När du till exempel konfigurerar användare från Azure AD till ett SaaS-program som används av ett försäljnings team kan du ange att endast användare med ett "avdelning"-attribut för "försäljning" ska omfattas av etableringen.

Omfångs filter kan användas på olika sätt beroende på typ av etablerings koppling:

* **Utgående etablering från Azure AD till SaaS-program**. När Azure AD är käll systemet är [användar-och grupp tilldelningar](assign-user-or-group-access-portal.md) den vanligaste metoden för att avgöra vilka användare som omfattas av etableringen. Dessa tilldelningar används också för att aktivera enkel inloggning och tillhandahålla en enda metod för att hantera åtkomst och etablering. Omfångs filter kan användas valfritt, förutom tilldelningar eller i stället för dem, för att filtrera användare baserat på attributvärden.

    >[!TIP]
    > Du kan inaktivera etablering baserat på tilldelningar för ett företags program genom att ändra inställningarna i menyn [omfång](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) under etablerings inställningarna för att **synkronisera alla användare och grupper**. Med det här alternativet plus attributbaserade omfångs filter får du snabbare prestanda än att använda gruppbaserade tilldelningar.  

* **Inkommande etablering från HCM-program till Azure AD och Active Directory**. När ett [HCM-program som Workday](../saas-apps/workday-tutorial.md) är käll systemet är definitions områdes filter den primära metoden för att avgöra vilka användare som ska tillhandahållas från HCM-programmet till Active Directory eller Azure AD.

Som standard har inte Azure AD Provisioning-kopplingar några attribut-baserade omfångs filter konfigurerade. 

## <a name="scoping-filter-construction"></a>Uppförande av omfattnings filter

Ett omfångs filter består av en eller flera *satser*. -Satser avgör vilka användare som tillåts passera genom omfångs filtret genom att utvärdera varje användares attribut. Du kan till exempel ha en-sats som kräver att användarens "State"-attribut är lika med "New York", så att endast nya användare i Göteborg är etablerade i programmet. 

En enskild sats definierar ett enskilt villkor för ett enda attributvärde. Om flera satser skapas i ett enda omfångs filter utvärderas de tillsammans med hjälp av "och"-logik. Det innebär att alla satser måste utvärderas till "true" för att en användare ska kunna tillhandahållas.

Slutligen kan flera omfångs filter skapas för ett enda program. Om det finns flera omfångs filter utvärderas de tillsammans med hjälp av "OR"-logik. Det innebär att om alla satser i något av de konfigurerade omfångs filtren utvärderas till "true", är användaren etablerade.

Varje användare eller grupp som bearbetas av Azure AD Provisioning-tjänsten utvärderas alltid individuellt mot varje omfångs filter.

Anta till exempel följande scope-filter:

![Omfångs filter](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

Enligt det här omfångs filtret måste användarna uppfylla följande kriterier som ska tillhandahållas:

* De måste vara i New York.
* De måste arbeta på teknik avdelningen.
* Företagets anställnings-ID måste vara mellan 1 000 000 och 2 000 000.
* Deras befattning får inte vara null eller tom.

## <a name="create-scoping-filters"></a>Skapa omfångs filter
Definitions områdes filter konfigureras som en del av mappningar av mappar för varje Azure AD-anslutning för användar etablering. Följande procedur förutsätter att du redan har konfigurerat automatisk etablering för [ett av de program som stöds](../saas-apps/tutorial-list.md) och lägger till ett omfångs filter till det.

### <a name="create-a-scoping-filter"></a>Skapa ett omfångs filter
1. I [Azure Portal](https://portal.azure.com)går du till avsnittet **Azure Active Directory** > **Enterprise-program** > **alla program** .

2. Välj det program som du har konfigurerat automatisk etablering för: till exempel "ServiceNow".

3. Välj fliken **etablering** .

4. I avsnittet **mappningar** väljer du den mappning som du vill konfigurera ett omfångs filter för: till exempel "synkronisera Azure Active Directory användare till ServiceNow".

5. Välj menyn **käll objekt omfånget** .

6. Välj **Lägg till omfångs filter**.

7. Definiera en sats genom att välja ett källattribut **namn**, en **operator**och ett **Attributvärde** som ska matchas mot. Följande operatorer stöds:

   a. **Lika med**. Sats returnerar true om det utvärderade attributet matchar värdet för Indatasträngen exakt (Skift läges känsligt).

   b. **inte lika med**. Sats returnerar true om det utvärderade attributet inte matchar värdet för Indatasträngen (Skift läges känsligt).

   c. **är sant**. Sats returnerar true om det utvärderade attributet innehåller det booleska värdet true.

   d. **är falskt**. Sats returnerar true om det utvärderade attributet innehåller ett booleskt värde falskt.

   e. **är null**. Sats returnerar true om det utvärderade attributet är tomt.

   f. **är inte null**. Sats returnerar true om det utvärderade attributet inte är tomt.

   g. **regex-matchning**. Sats returnerar true om det utvärderade attributet matchar ett mönster för reguljära uttryck. Exempel: ([1-9] [0-9]) matchar alla siffror mellan 10 och 99.

   h. **ingen regex-matchning**. Sats returnerar true om det utvärderade attributet inte matchar ett mönster för reguljära uttryck.

8. Välj **Lägg till ny omfångs sats**.

9. Du kan också upprepa steg 7-8 om du vill lägga till fler omfångs satser.

10. I **omfångs filter rubrik**, Lägg till ett namn för ditt omfångs filter.

11. Välj **OK**.

12. Välj **OK** igen på skärmen **omfångs filter** . Du kan också upprepa steg 6-11 om du vill lägga till ett annat omfångs filter.

13. Välj **Spara** på skärmen **Mappning av attribut** . 

>[!IMPORTANT] 
> Om du sparar ett nytt omfångs filter utlöses en ny fullständig synkronisering för programmet, där alla användare i käll systemet utvärderas igen mot det nya omfångs filtret. Om en användare i programmet tidigare fanns inom omfånget för etablering, men faller utanför omfattningen, så inaktive ras eller avetableras kontot i programmet. Om du vill åsidosätta det här standard beteendet går du till [hoppa över borttagning för användar konton som omfattas av omfånget](skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Vanliga omfångs filter
| Target-attribut| Operator | Värde | Beskrivning|
|----|----|----|----|
|userPrincipalName|REGEX-MATCHNING|.\*@domain.com |Alla användare med userPrincipal som har den domän som @domain.com behålls inom omfånget för etablering|
|userPrincipalName|INGEN REGEX-MATCHNING|.\*@domain.com|Alla användare med userPrincipal som har domänen @domain.com omfattas inte av omfånget för etablering|
|avdelning|ÄR lika med|försäljning|Alla användare från försäljnings avdelningen är inom omfånget för etablering|
|workerID|REGEX-MATCHNING|(1[0-9][0-9][0-9][0-9][0-9][0-9])| Alla anställda med workerIDs mellan 1000000 och 2000000 finns inom omfånget för etablering.|

## <a name="related-articles"></a>Relaterade artiklar
* [Automatisera användar etablering och avetablering för SaaS-program](user-provisioning.md)
* [Anpassa mappningar av attribut för användar etablering](customize-application-attributes.md)
* [Skriva uttryck för attributmappningar](functions-for-customizing-application-data.md)
* [Meddelanden om konto etablering](user-provisioning.md)
* [Använd SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)

