---
title: 'Självstudiekurs: Konfigurera ServiceNow för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du automatiskt etablerar och avetableringar användarkonton från Azure AD till ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 9e93d4b3f1880f2ac56a32a7b85aa6801fb7c14e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205149"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera ServiceNow för automatisk etablering av användare

I den här självstudien beskrivs de steg du behöver utföra i både ServiceNow och Azure Active Directory (Azure AD) för att konfigurera automatisk användaretablering. När azure AD är konfigurerat avsersättningar och avsersättningar avsättningar till [ServiceNow](https://www.servicenow.com/) med hjälp av Azure AD-etableringstjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i ServiceNow
> * Ta bort användare i ServiceNow när de inte längre behöver åtkomst
> * Synkronisera användarattribut mellan Azure AD och ServiceNow
> * Etableringsgrupper och gruppmedlemskap i ServiceNow
> * [Enkel inloggning till](servicenow-tutorial.md) ServiceNow (rekommenderas)

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användarkonto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* En [ServiceNow-instans](https://www.servicenow.com/) av Calgary eller senare
* En [ServiceNow Express-instans](https://www.servicenow.com/) i Helsingfors eller senare
* Ett användarkonto i ServiceNow med administratörsrollen

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera distributionen av etableringen
1. Läs mer om [hur etableringstjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bestäm vem som ska vara i [omfång för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bestäm vilka data som ska [mappas mellan Azure AD och ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera ServiceNow för att stödja etablering med Azure AD

1. Identifiera ditt ServiceNow-förekomstnamn. Du hittar förekomstnamnet i webbadressen som du använder för att komma åt ServiceNow. I exemplet nedan är förekomstnamnet dev35214.

![ServiceNow-instans](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Hämta autentiseringsuppgifter för en administratör i ServiceNow. Navigera till användarprofilen i ServiceNow och kontrollera att användaren har administratörsrollen. 

![Administrationsrollen ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till ServiceNow från Azure AD-programgalleriet

Lägg till ServiceNow från Azure AD-programgalleriet för att börja hantera etablering till ServiceNow. Om du tidigare har konfigurerat ServiceNow för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integrationen från början. Läs mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska vara i utrymme för etablering 

Azure AD-etableringstjänsten gör att du kan begränsa vem som ska etableras baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att begränsa vem som ska etableras i din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att begränsa vem som ska etableras enbart baserat på attribut för användaren eller gruppen kan du använda ett omfångsfilter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till ServiceNow måste du välja en annan roll än **Standardåtkomst**. Användare med rollen Standardåtkomst är undantagna från etablering och markeras som inte effektivt berättigade i etableringsloggarna. Om den enda roll som är tillgänglig för programmet är standardåtkomstrollen kan du [uppdatera programmanifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Börja i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfång för etablering är inställt på tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När scopet är inställt på alla användare och grupper kan du ange ett [attributbaserat omfångsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Steg 5. Konfigurera automatisk användaretablering till ServiceNow 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för ServiceNow i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **ServiceNow**.

    ![ServiceNow-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du dina ServiceNow-administratörsautentiseringsuppgifter och användarnamn. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till ServiceNow. Om anslutningen misslyckas kontrollerar du att ditt ServiceNow-konto har administratörsbehörighet och försöker igen.

    ![Etableringen](./media/servicenow-provisioning-tutorial/provisioning.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till ServiceNow**.

9. Granska användarattributen som synkroniseras från Azure AD till ServiceNow i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i ServiceNow för uppdateringsåtgärder. Om du väljer att ändra [det matchande målattributet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att ServiceNow-API:et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** om du vill utföra eventuella ändringar.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till ServiceNow**.

11. Granska gruppattributen som synkroniseras från Azure AD till ServiceNow i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i ServiceNow för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för ServiceNow ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till ServiceNow genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den inledande synkroniseringscykeln för alla användare och grupper som **definierats** i Scope i avsnittet **Inställningar.** Den inledande cykeln tar längre tid att utföra än efterföljande cykler, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

1. Använd [etableringsloggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller utan framgång
2. Kontrollera [förloppsindikatorn](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etableringscykeln och hur nära den är till slutförande
3. Om etableringskonfigurationen verkar vara i feltillstånd kommer programmet att placeras i karantän. Läs mer om karantäntillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Felsökningstips
* **Ogiltigt Lyssning:** När vissa attribut etableras, till exempel Avdelning och Plats i ServiceNow, måste värdena redan finnas i en referenstabell i ServiceNow. Du kan till exempel ha två platser (Seattle, Los Angeles) och tre avdelningar (Försäljning, Ekonomi, Marknadsföring) i tabellen **Infoga tabellnamn** i ServiceNow. Om du försöker etablera en användare där hans avdelning är "Försäljning" och platsen är "Seattle" kommer han att etableras. Om du försöker etablera en användare med avdelningen "Försäljning" och plats "LA" kommer användaren inte att etableras. Plats LA måste antingen läggas till i referenstabellen i ServiceNow eller så måste användarattributet i Azure AD uppdateras för att matcha formatet i ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** Granska [attributmappningarna](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) för att identifiera det matchande attributet. Det här värdet måste finnas på den användare eller grupp som du försöker etablera. 
* Granska [ServiceNow SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) för att förstå eventuella krav eller begränsningar (till exempel format för att ange landskod för en användare)
* Vissa ServiceNow-distributioner kräver att IP-intervall tillåts för Azure AD-etableringstjänsten. De reserverade IP-intervallen för Azure AD-etableringstjänsten finns [här](https://www.microsoft.com/download/details.aspx?id=56519) under "AzureActiveDirectoryDomainServices".
* Etableringsbegäranden skickas som standard till https://{your-instance-name}.service-now.com/{table-name} . Om du behöver en anpassad klient-URL kan du ange hela URL:en i instansnamnsfältet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
