---
title: Tillhandahåll appar med Omfångsfilter | Microsoft Docs
description: Lär dig hur du använder Omfångsfilter för att förhindra att objekt i appar som stöder automatisk användaretablering från håller på att etableras om ett objekt inte uppfyller företagets krav.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: barbkess
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b62679332a45127d7c1a45593c162182886a7150
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719366"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Attributbaserad programetablering med Omfångsfilter
Målet med den här artikeln är att förklara hur du använder Omfångsfilter för att definiera attributbaserade regler som bestämmer vilka användare som tilldelas ett program.

## <a name="scoping-filter-use-cases"></a>Ange omfång filter användningsfall

Ett Omfångsfilter kan Azure Active Directory (Azure AD) etableringstjänsten att inkludera eller exkludera alla användare som har ett attribut som matchar ett specifikt värde. Till exempel vid etablering av användare från Azure AD med ett SaaS-program som används av ett säljteam, kan du ange att endast användare med attributet ”avdelning” med ”försäljning” ska vara i omfånget för etablering.

Omfångsfilter kan användas på olika sätt beroende på vilken typ av etablering anslutningen:

* **Utgående etablering från Azure AD SaaS-program**. När Azure AD är källsystemet, [användar- och tilldelningar](assign-user-or-group-access-portal.md) är den vanligaste metoden för att bestämma vilka användare som är inom omfånget för etablering. Dessa tilldelningar också används för att aktivera enkel inloggning och ger en enkel metod för att hantera åtkomst och etablering. Omfångsfilter kan användas om du vill, förutom tilldelningar eller i stället för dem, för att filtrera användare baserat på attributvärden.

    >[!TIP]
    > Du kan inaktivera etablering baserat på tilldelningar för ett enterprise-program genom att ändra inställningarna i den [omfång](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) menyn under inställningarna för allokering till **synkronisera alla användare och grupper**. Med det här alternativet och attributbaserade Omfångsfilter erbjuder snabbare prestanda än att använda gruppbaserad tilldelningar.  

* **Inkommande etablering från HCM-program till Azure AD och Active Directory**. När en [HCM-programmet till exempel Workday](../saas-apps/workday-tutorial.md) är källsystemet, Omfångsfilter är den primära metoden för att bestämma vilka användare som ska etableras från HCM-programmet till Active Directory eller Azure AD.

Som standard har inte Azure AD-etablering anslutningsappar eventuella attributbaserade Omfångsfilter som konfigurerats. 

## <a name="scoping-filter-construction"></a>Ange omfång filter konstruktion

Ett Omfångsfilter består av en eller flera *satser*. Satser avgör vilka användare som tillåts att passera Omfångsfilter genom att utvärdera varje användares attribut. Du kan till exempel ha en sats som kräver att en användares ”State” attribut är lika med ”New York”, så att endast användare i New York etableras i programmet. 

En sats definierar ett enda villkor för en enda attribut-värde. Om flera satser skapas i ett enda Omfångsfilter, är de utvärderas tillsammans med hjälp av ”AND”-logik. Det innebär att alla satser måste utvärderas till ”true” i ordning för en användare som ska etableras.

Dessutom kan flera Omfångsfilter skapas för ett enda program. Om det finns flera Omfångsfilter, är de utvärderas tillsammans med hjälp av ”OR”-logik. Det innebär att om alla satser i någon av de konfigurerade Omfångsfilter utvärdera ”true”, användaren har etablerats.

Varje användare eller grupp som bearbetas av Azure AD-etableringstjänsten utvärderas alltid individuellt mot varje Omfångsfilter.

Överväg följande Omfångsfilter exempelvis:

![Omfångsfilter](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

Enligt den här Omfångsfilter måste användare uppfylla följande kriterier som ska etableras:

* De måste vara i New York.
* De måste arbeta i avdelningen tekniker.
* Deras företag anställnings-ID måste vara mellan 1 000 000 och 2 000 000.
* Deras befattning får inte vara null eller tomt.

## <a name="create-scoping-filters"></a>Skapa Omfångsfilter
Omfångsfilter konfigureras som en del av attributmappningar för varje Azure AD-användare som etablerar anslutningen. Följande procedur förutsätter att du redan har konfigurerat Automatisk etablering för [något av program som stöds](../saas-apps/tutorial-list.md) och lägger till ett Omfångsfilter till den.

### <a name="create-a-scoping-filter"></a>Skapa ett Omfångsfilter
1. I den [Azure-portalen](https://portal.azure.com)går du till den **Azure Active Directory** > **företagsprogram** > **alla program** avsnittet.

2. Välj det program som du har konfigurerat Automatisk etablering: till exempel ”ServiceNow”.

3. Välj den **etablering** fliken.

4. I den **mappningar** väljer mappningen som du vill konfigurera ett Omfångsfilter för: till exempel ”synkronisera Azure Active Directory-användare till ServiceNow”.

5. Välj den **Source omfång för källobjekt** menyn.

6. Välj **Lägg till Omfångsfilter**.

7. Definiera en sats genom att välja en källa **attributnamnet**, en **operatorn**, och en **attributvärdet** för matchning. Följande operatorer som stöds:

   a. **ÄR LIKA MED**. Satsen returnerar ”true” om attributet utvärderade matchar värdet som Indatasträngen exakt (skiftlägeskänsligt).

   b. **INTE LIKA MED**. Satsen returnerar ”true” om attributet utvärderade inte matchar värdet Indatasträngen (skiftlägeskänsligt).

   c. **STÄMMER**. Satsen returnerar ”true” om utvärderade attribut innehåller booleska värdet true.

   d. **ÄR FALSKT**. Satsen returnerar ”true” om utvärderade attribut innehåller booleska värdet false.

   e. **ÄR NULL**. Satsen returnerar ”true” om attributet utvärderade är tom.

   f. **INTE ÄR NULL**. Satsen returnerar ”true” om det utvärderade attributet inte är tom.

   g. **REGEX-MATCHNING**. Satsen returnerar ”true” om attributet utvärderade matchar ett mönster för reguljärt uttryck. Till exempel: ([1-9][0-9]) matchar alla tal mellan 10 och 99.

   h. **REGEX MATCHAR INTE**. Satsen returnerar ”true” om attributet utvärderade inte matchar ett mönster för reguljärt uttryck.

8. Välj **Lägg till ny målgrupp sats**.

9. Du kan också upprepa steg 7 – 8 för att lägga till fler målgrupp satser.

10. I **omfång rubrik**, lägga till ett namn för din Omfångsfilter.

11. Välj **OK**.

12. Välj **OK** igen på den **omfång filter** skärmen. Du kan också upprepa steg 6 – 11 för att lägga till en annan Omfångsfilter.

13. Välj **spara** på den **attributmappning** skärmen. 

>[!IMPORTANT] 
> Sparar ett nytt målgrupp filtret utlöses en ny fullständig synkronisering för programmet, där alla användare i källsystemet utvärderas igen mot den nya Omfångsfilter. Om en användare i programmet tidigare ingick i omfånget för etablering, men infaller under utanför är deras konto inaktiverat eller avetableras i programmet.


## <a name="related-articles"></a>Relaterade artiklar
* [Automatisera etablering och avetablering för SaaS-program](user-provisioning.md)
* [Anpassa attributmappningar för etableringen av användare](customize-application-attributes.md)
* [Skriva uttryck för attributmappningar](functions-for-customizing-application-data.md)
* [Kontoetableringsmeddelanden](user-provisioning.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md)
* [Lista över guider om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)

