---
title: 'Självstudiekurs: Konfigurera Salesforce för automatisk användaretablering med Azure Active Directory| Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b8038896a11b65e835ce71f5fc34e85723cc91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060529"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Salesforce för automatisk etablering av användare

Syftet med den här självstudien är att visa de steg som krävs för att utföra i Salesforce och Azure AD för att automatiskt etablera och avetableras användarkonton från Azure AD till Salesforce.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klient
* En Salesforce.com hyresgäst

> [!IMPORTANT]
> Om du använder ett Salesforce.com utvärderingskonto kan du inte konfigurera automatisk användaretablering. Utvärderingskonton har inte nödvändig API-åtkomst aktiverad förrän de har köpts. Du kan komma runt denna begränsning genom att använda ett gratis [utvecklarkonto](https://developer.salesforce.com/signup) för att slutföra den här självstudien.

Om du använder en Salesforce Sandbox-miljö läser du [självstudien för Salesforce Sandbox-integrering](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Tilldela användare till Salesforce

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare eller grupper i Azure AD som behöver åtkomst till din Salesforce-app. När du har fattat det här beslutet kan du tilldela dessa användare till din Salesforce-app genom att följa instruktionerna i [Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Viktiga tips för att tilldela användare till Salesforce

* Vi rekommenderar att en enda Azure AD-användare tilldelas Salesforce för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Salesforce måste du välja en giltig användarroll. Rollen "Standardåtkomst" fungerar inte för etablering

    > [!NOTE]
    > Den här appen importerar profiler från Salesforce som en del av etableringsprocessen, som kunden kanske vill välja när du tilldelar användare i Azure AD. Observera att de profiler som importeras från Salesforce visas som Roller i Azure AD.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk etablering av användare

Det här avsnittet hjälper dig att ansluta din Azure AD till [Salesforces API för etablering av användarkonton – v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Salesforce baserat på användar- och grupptilldelning i Azure AD.

> [!Tip]
> Du kan också välja att aktivera SAML-baserade Single Sign-On for Salesforce, enligt instruktionerna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurera automatisk etablering av användarkonton

Syftet med det här avsnittet är att beskriva hur du aktiverar användaretablering av Active Directory-användarkonton till Salesforce.

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

2. Om du redan har konfigurerat Salesforce för enkel inloggning söker du efter din instans av Salesforce med hjälp av sökfältet. Annars väljer du **Lägg till** och sök efter **Salesforce** i programgalleriet. Välj Salesforce i sökresultaten och lägg till den i listan över program.

3. Välj din instans av Salesforce och välj sedan fliken **Etablering.**

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Etableringen](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Ange följande konfigurationsinställningar under avsnittet **Administratörsautentiseringsuppgifter:**

    a. Skriv ett **Salesforce-kontonamn** som har **systemadministratörsprofilen** i Salesforce.com tilldelat i textrutan Administratörsanvändar.

    b. Skriv lösenordet för det här kontot i textrutan **Admin Password.**

6. Om du vill hämta din Salesforce-säkerhetstoken öppnar du en ny flik och loggar in på samma Salesforce-administratörskonto. Klicka på ditt namn längst upp till höger på sidan och klicka sedan på **Inställningar**.

    ![Aktivera automatisk etablering av användare](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Aktivera automatisk etablering av användare")

7. Klicka på **Min personliga information** i det vänstra navigeringsfönstret för att expandera det relaterade avsnittet och klicka sedan på Återställ min **säkerhetstoken**.
  
    ![Aktivera automatisk etablering av användare](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Aktivera automatisk etablering av användare")

8. Klicka på Knappen Återställ **säkerhetstoken** på sidan Återställ **säkerhetstoken.**

    ![Aktivera automatisk etablering av användare](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Aktivera automatisk etablering av användare")

9. Kontrollera e-postkorgen som är kopplad till det här administratörskontot. Leta efter ett e-postmeddelande från Salesforce.com som innehåller den nya säkerhetstoken.

10. Kopiera token, gå till ditt Azure AD-fönster och klistra in den i fältet **Hemlig token.**

11. **Klient-URL:en** ska anges om instansen av Salesforce finns i Salesforce Government Cloud. Annars är det valfritt. Ange klient-URL:en med formatet\<"https://\>din instans .my.salesforce.com" \<och\> ersätt din instans med namnet på Salesforce-instansen.

12. Klicka på Testa **anslutning** i Azure-portalen för att säkerställa att Azure AD kan ansluta till din Salesforce-app.

13. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden och markerar kryssrutan nedan.

14. Klicka på **Spara.**  

15. Under avsnittet Mappningar väljer du **Synkronisera Azure Active Directory-användare till Salesforce.**

16. I avsnittet **Attributmappningar** granskar du de användarattribut som synkroniseras från Azure AD till Salesforce. Observera att de attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Salesforce för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

17. Om du vill aktivera Azure AD-etableringstjänsten för Salesforce ändrar **du etableringsstatusen** till **På** i avsnittet Inställningar

18. Klicka på **Spara.**

> [!NOTE]
> När användarna har etablerats i Salesforce-programmet måste administratören konfigurera språkspecifika inställningar för dem. Se [den här](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) artikeln för mer information om språkkonfiguration.

Detta startar den första synkroniseringen av alla användare och/eller grupper som tilldelats Salesforce i avsnittet Användare och grupper. Observera att den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar, som beskriver alla åtgärder som utförs av etableringstjänsten i din Salesforce-app.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Vanliga problem
* Om du har problem med att tillåta åtkomst till Salesforce, säkerställer du följande:
    * De autentiseringsuppgifter som används har administratörsåtkomst till Salesforce.
    * Den version av Salesforce som du använder stöder Web Access (t.ex. utvecklare, enterprise, sandbox och obegränsade utgåvor av Salesforce.)
    * Webb-API-åtkomst är aktiverad för användaren.
* Azure AD-etableringstjänsten stöder etablering av språk, språk och timeZone för en användare. Dessa attribut finns i standardattributmappningarna men har inget standardtällattribut. Kontrollera att du väljer standardkällaattributet och att källattributet är i det format som förväntas av SalesForce. Till exempel är localeSidKey för engelska (USA) en_US. Granska vägledningen [här](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) för att bestämma rätt språkSidKey-format. SpråkLokalaleKey-formaten hittar du [här](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5). Förutom att se till att formatet är korrekt kan du behöva se till att språket är aktiverat för användarna enligt beskrivningen [här](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5). 
* **SalesforceLicenseLimitExceed:** Det gick inte att skapa användaren i målprogrammet eftersom det inte finns några tillgängliga licenser för den här användaren. Antingen skaffa ytterligare licenser för målprogrammet eller granska dina användartilldelningar och attributmappningskonfiguration för att säkerställa att rätt användare tilldelas med rätt attribut.
* **SalesforceDuplicateUserName:** Användaren kan inte etableras eftersom den har ett Salesforce.com användarnamn som dupliceras i en annan Salesforce.com klient.I Salesforce.com måste värdena för attributet Användarnamn vara unika för alla Salesforce.com klienter.Som standard blir en användares userPrincipalName i Azure Active Directory deras användarnamn i Salesforce.com.Du har två alternativ.Ett alternativ är att hitta och byta namn på användaren med dubbletten "Användarnamn" i den andra Salesforce.com klienten, om du administrerar den andra klienten också.Det andra alternativet är att ta bort åtkomst från Azure Active Directory-användaren till den Salesforce.com klient som katalogen är integrerad med. Vi kommer att försöka den här åtgärden på nästa synkroniseringsförsök. 
* **SalesforceRequiredFieldMissing:** Salesforce kräver att vissa attribut finns på användaren för att kunna skapa eller uppdatera användaren. Den här användaren saknar ett av de obligatoriska attributen. Kontrollera att attribut som e-post och alias fylls i på alla användare som du vill ska etableras i Salesforce. Du kan begränsa användare som inte har dessa attribut ut med hjälp av [attributbaserade omfångsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 
* Standardattributmappningen för etablering till Salesforce innehåller uttrycket SingleAppRoleAssignments för att mappa appRoleAstilldelningar i Azure AD till ProfileName i Salesforce. Se till att användarna inte har flera approlltilldelningar i Azure AD eftersom attributmappningen endast stöder etablering av en roll. 
* Salesforce kräver att e-postuppdateringar godkänns manuellt innan de ändras. Därför kan du se flera poster i etableringsloggarna för att uppdatera användarens e-post (tills e-poständringen har godkänts).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
