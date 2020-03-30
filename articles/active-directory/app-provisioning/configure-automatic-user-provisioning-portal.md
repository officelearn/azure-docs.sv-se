---
title: Hantering av användaretablering för företagsappar i Azure AD
description: Lär dig hur du hanterar etablering av användarkonton för företagsappar med Hjälp av Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f9bd1afeebedf4cbf37d75d9c57f3d8be0f288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264133"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Hantera etablering av användarkonton för företagsappar i Azure-portalen

I den här artikeln beskrivs de allmänna stegen för att hantera automatisk etablering av användarkonton och avetablering för program som stöder det. Etablering av *användarkonton* är handlingen att skapa, uppdatera och/eller inaktivera användarkontoposter i ett programs lokala användarprofilarkiv. De flesta moln- och SaaS-program lagrar användarrollen och behörigheterna i användarens egen lokala användarprofilbutik, och förekomsten av en sådan användarpost i användarens lokala butik *krävs* för enkel inloggning och åtkomst till arbete. Mer information om automatisk etablering av användarkonton finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](user-provisioning.md).

> [!IMPORTANT]
> Azure Active Directory (Azure AD) har ett galleri som innehåller tusentals förintegrerade program som är aktiverade för automatisk etablering med Azure AD. Du bör börja med att hitta den etableringsinställningshandledning som är specifik för ditt program i [listan över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Du hittar förmodligen steg-för-steg-vägledning för att konfigurera både appen och Azure AD för att skapa etableringsanslutningen.

## <a name="finding-your-apps-in-the-portal"></a>Hitta dina appar i portalen

Använd Azure Active Directory-portalen för att visa och hantera alla program som är konfigurerade för enkel inloggning i en katalog. Företagsappar är appar som distribueras och används inom organisationen. Så här visar och hanterar du företagsprogram:

1. Öppna [Azure Active Directory-portalen](https://aad.portal.azure.com).
1. Välj **Enterprise-program** i den vänstra rutan. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.
1. Välj en app om du vill läsa in resursfönstret, där du kan visa rapporter och hantera appinställningar.
1. Välj **Etablering** om du vill hantera etableringsinställningar för användarkonton för den valda appen.

   ![Etableringsskärmen för att hantera inställningar för etablering av användarkonton](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Etableringslägen

**Etableringsfönstret** börjar med en **lägesmeny** som visar de etableringslägen som stöds för ett företagsprogram och låter dig konfigurera dem. De tillgängliga alternativen är:

* **Automatisk** - Det här alternativet visas om Azure AD stöder automatisk API-baserad etablering eller avetablering av användarkonton till det här programmet. Välj det här läget om du vill visa ett gränssnitt som hjälper administratörer:

  * Konfigurera Azure AD för att ansluta till programmets ANVÄNDARHANTERINGS-API
  * Skapa kontomappningar och arbetsflöden som definierar hur användarkontodata ska flöda mellan Azure AD och appen
  * Hantera Azure AD-etableringstjänsten

* **Manuell** - Det här alternativet visas om Azure AD inte stöder automatisk etablering av användarkonton till det här programmet. I det här fallet måste användarkontoposter som lagras i programmet hanteras med hjälp av en extern process, baserat på användarhanterings- och etableringsfunktionerna som tillhandahålls av det programmet (som kan inkludera SAML Just-In-Time-etablering).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurera automatisk etablering av användarkonton

Välj alternativet **Automatisk** om du vill ange inställningar för administratörsautentiseringsuppgifter, mappningar, start och stopp samt synkronisering.

### <a name="admin-credentials"></a>Administratörsautentiseringsuppgifter

Expandera **administratörsautentiseringsuppgifter** för att ange de autentiseringsuppgifter som krävs för att Azure AD ska kunna ansluta till programmets API för användarhantering. Den ingång som krävs varierar beroende på programmet. Mer information om autentiseringsuppgifter och krav för specifika program finns i [konfigurationshandledningen för det specifika programmet](user-provisioning.md).

Välj **Testa anslutning** för att testa autentiseringsuppgifterna genom att låta Azure AD försöka ansluta till appens etableringsapp med hjälp av de angivna autentiseringsuppgifterna.

### <a name="mappings"></a>Mappningar

Expandera **mappningar** om du vill visa och redigera de användarattribut som flödar mellan Azure AD och målprogrammet när användarkonton etableras eller uppdateras.

Det finns en förkonfigurerad uppsättning mappningar mellan Azure AD-användarobjekt och varje SaaS-apps användarobjekt. Vissa appar hanterar andra typer av objekt, till exempel Grupper eller Kontakter. Välj en mappning i tabellen om du vill öppna mappningsredigeraren till höger, där du kan visa och anpassa dem.

![Visar skärmen Attributmappning](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Anpassningar som stöds omfattar:

* Aktivera och inaktivera mappningar för specifika objekt, till exempel Azure AD-användarobjektet till SaaS-appens användarobjekt.
* Redigera attributen som flödar från Azure AD-användarobjektet till appens användarobjekt. Mer information om attributmappning finns i [Förstå attributmappningstyper](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrera de etableringsåtgärder som Azure AD körs på det riktade programmet. I stället för att Azure AD ska synkronisera objekt fullt ut kan du begränsa vilka åtgärder som körs.

  Välj till exempel bara **Uppdatera** och Azure AD uppdaterar bara befintliga användarkonton i ett program men skapar inte nya. Välj bara **Skapa** och Azure skapar bara nya användarkonton men uppdaterar inte befintliga. Med den här funktionen kan administratörer skapa olika mappningar för att skapa och uppdatera konton.

* Lägga till en ny attributmappning. Välj **Lägg till ny mappning** längst ned i fönstret **Attributmappning.** Fyll i formuläret **Redigera attribut** och välj **Ok** om du vill lägga till den nya mappningen i listan.

### <a name="settings"></a>Inställningar

Du kan starta och stoppa Azure AD-etableringstjänsten för det valda programmet i området **Inställningar** på **etableringsskärmen.** Du kan också välja att rensa etableringscachen och starta om tjänsten.

Om etablering aktiveras för första gången för ett program aktiverar du tjänsten genom att ändra **etableringsstatusen** till **På**. Den här ändringen medför att Azure AD-etableringstjänsten kör en inledande cykel. Den läser de användare som tilldelats i avsnittet **Användare och grupper,** frågar målprogrammet för dem och kör sedan de etableringsåtgärder som definierats i avsnittet Azure **AD-mappningar.** Under den här processen lagrar etableringstjänsten cachelagrade data om vilka användarkonton den hanterar, så icke-hanterade konton i målprogrammen som aldrig fanns i omfånget för tilldelning påverkas inte av avetableringsåtgärder. Efter den inledande cykeln synkroniserar etableringstjänsten automatiskt användar- och gruppobjekt med ett fyrtiominutersintervall.

Ändra **etableringsstatusen** till **Av** för att pausa etableringstjänsten. I det här tillståndet skapar, uppdaterar eller tar inte Azure bort några användare eller gruppobjekt i appen. Ändra tillståndet tillbaka till **På** och tjänsten fortsätter där den slutade.

**Rensa aktuellt tillstånd och starta om synkroniseringen** utlöser en inledande cykel. Tjänsten utvärderar sedan alla användare i källsystemet igen och avgör om de är i omfång för etablering. Detta kan vara användbart när ditt program för närvarande är i karantän eller om du behöver göra en ändring av attributmappningarna. Observera att den inledande cykeln tar längre tid att slutföra än den typiska inkrementella cykeln på grund av antalet objekt som måste utvärderas. Du kan läsa mer om resultatet av inledande och inkrementella cykler [här](application-provisioning-when-will-provisioning-finish-specific-user.md). 
