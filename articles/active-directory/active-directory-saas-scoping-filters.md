---
title: "Tillhandahåll appar med Omfångsfilter | Microsoft Docs"
description: "Lär dig hur du använder målgrupp filter för att förhindra att objekt i appar som stöder automatisk användaretablering från tillhandahålls om ett objekt inte uppfyller dina affärsbehov."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e7a2322239945a529a544054c2273e37a3d65abf
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Attributbaserad programmet etablering med Omfångsfilter
Syftet med den här artikeln är som förklarar hur du använder målgrupp filter för att definiera attributbaserade regler som bestämmer vilka användare som har etablerats till ett program.

## <a name="scoping-filter-use-cases"></a>Målgrupp filter användningsområden

En målgrupp filtret kan Azure Active Directory (AD Azure) etableras inkludera eller exkludera användare som har ett attribut som matchar ett specifikt värde. Etablera användare från Azure AD till ett SaaS-program som används av en säljgrupp ange du till exempel att endast användare med attributet ”avdelning” med ”försäljning” ska vara i omfånget för etablering.

Omfångsfilter kan användas på olika sätt beroende på vilken typ av etablering connector:

* **Utgående etablering från Azure AD till SaaS-program**. När Azure AD är källsystemet, [användar- och tilldelningar](active-directory-coreapps-assign-user-azure-portal.md) är den vanligaste metoden för att avgöra vilka användare som ingår i omfattningen för etablering. Tilldelningarna också används för att aktivera enkel inloggning och ger en enkel metod för att hantera åtkomst och etablering. Omfångsfilter kan användas (valfritt) utöver tilldelningar eller i stället för dem för att filtrera användare baserat på attributvärden.

    >[!TIP]
    > Du kan inaktivera etablering baserat på tilldelningar för ett företagsprogram genom att ändra inställningarna i den [omfång](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) menyn under etablering inställningarna **synkroniseras alla användare och grupper**. Med det här alternativet plus attributbaserad målgrupp filter ger snabbare prestanda än att använda gruppbaserade tilldelningar.  

* **Inkommande från HCM program till Azure AD-etablering och Active Directory**. När en [HCM-program, till exempel Workday](active-directory-saas-workday-tutorial.md) är källsystemet, målgrupp filter är den primära metoden för att avgöra vilka användare som ska etableras från HCM-program till Active Directory eller Azure AD.

Azure AD-etablering kopplingar har inte någon attributbaserad målgrupp filter som har konfigurerats som standard. 

## <a name="scoping-filter-construction"></a>Målgrupp filter konstruktion

En målgrupp filter består av en eller flera *satser*. Satser avgör vilka användare som får passera filtret målgrupp genom utvärdering av attribut för varje användare. Du kan till exempel ha en sats som kräver att en användare ”tillstånd” attribut är lika med ”New York”, så att endast användare i New York tillhandahålls av programmet. 

En sats definierar ett enda villkor för ett enda attribut-värde. Om flera satser skapas i ett enda målgrupp filter, är de utvärderas tillsammans med ”AND”-Logiken. Det innebär att alla satser måste utvärderas till ”true” i ordning för en användare som ska etableras.

Slutligen kan flera målgrupp filter skapas för ett program. Om det finns flera målgrupp filter, är de utvärderas tillsammans med ”eller” logik. Det innebär att användaren etableras om alla satser i de konfigurerade målgrupp filter utvärdera ”true”.

Varje användare eller grupp som bearbetas av Azure AD-etablering tjänsten utvärderas alltid enskilt mot varje målgrupp filter.

Överväg följande målgrupp filter som ett exempel:

![Omfångsfilter](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

Användarna måste uppfylla följande kriterier som ska etableras med enligt det här målgrupp filtret:

* De måste vara i New York.
* De måste arbeta i avdelningen tekniker.
* Sina företag anställnings-ID måste vara mellan 1 000 000 och 2 000 000.
* Deras befattning får inte vara null eller tomt.

## <a name="create-scoping-filters"></a>Skapa målgrupp filter
Målgrupp filter konfigureras som en del av attributmappning för varje Azure AD-användare som etablerar anslutningen. Följande procedur förutsätter att du redan har konfigurerat Automatisk etablering för [ett program som stöds](active-directory-saas-tutorial-list.md) och lägger till en målgrupp filter till den.

### <a name="create-a-scoping-filter"></a>Skapa en målgrupp filter
1. I den [Azure-portalen](https://portal.azure.com), gå till den **Azure Active Directory** > **företagsprogram** > **alla program** avsnitt.

2. Välj det program som du har konfigurerat Automatisk etablering: till exempel ”ServiceNow”.

3. Välj den **etablering** fliken.

4. I den **mappningar** avsnittet, Välj den avbildning som du vill konfigurera en målgrupp filter för: till exempel ”synkronisera Azure Active Directory-användare till ServiceNow”.

5. Välj den **datakällan Objektområde** menyn.

6. Välj **lägga till målgrupp filter**.

7. Definiera en instruktion genom att välja en källa **attributnamn**, en **operatorn**, och en **attributvärdet** för matchning. Följande operatorer stöds:

   a. **ÄR LIKA MED**. Satsen returnerar ”true” om attributet utvärderade matchar strängvärdet inkommande exakt (skiftlägeskänsligt).

   b. **ÄR INTE LIKA MED**. Satsen returnerar ”true” om attributet utvärderade inte matchar värdet Indatasträngen (skiftlägeskänsligt).

   c. **STÄMMER**. Satsen returnerar ”true” om utvärderade attributet innehåller booleska värdet true.

   d. **ÄR FALSE**. Satsen returnerar ”true” om utvärderade attributet innehåller ett booleskt värde false.

   e. **ÄR NULL**. Satsen returnerar ”true” om det utvärderade attributet är tom.

   f. **INTE ÄR NULL**. Satsen returnerar ”true” om det utvärderade attributet inte är tom.

   g. **REGEX MATCHAR**. Satsen returnerar ”true” om attributet utvärderade matchar mönstret för reguljära uttryck. Till exempel: ([1-9][0-9]) matchar alla tal mellan 10 och 99.

   h. **REGEX MATCHAR INTE**. Satsen returnerar ”true” om attributet utvärderade inte matchar mönstret för reguljära uttryck.

8. Välj **Lägg till ny målgrupp instruktion**.

9. Upprepa eventuellt steg 7 – 8 för att lägga till flera målgrupp satser.

10. I **scope Filter rubrik**, lägga till ett namn för din målgrupp filter.

11. Välj **OK**.

12. Välj **OK** igen på den **scope filter** skärmen. Upprepa eventuellt steg 6-11 för att lägga till ett annat målgrupp filter.

13. Välj **spara** på den **attributmappning** skärmen. 

>[!IMPORTANT] 
> Sparar ett nytt målgrupp filtret utlöses en ny fullständig synkronisering för programmet, där alla användare i källsystemet utvärderas igen mot det nya målgrupp filtret. Om en användare i programmet tidigare ingick i omfånget för etablering, men faller utanför är sitt konto Inaktiverad eller avetableras i programmet.


## <a name="related-articles"></a>Relaterade artiklar
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Automatisera användaren etablering och avetablering för SaaS-program](active-directory-saas-app-provisioning.md)
* [Anpassa attributmappning för användaretablering](active-directory-saas-customizing-attribute-mappings.md)
* [Skriva uttryck för attributmappning](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Kontot etablering meddelanden](active-directory-saas-account-provisioning-notifications.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](active-directory-scim-provisioning.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](active-directory-saas-tutorial-list.md)

