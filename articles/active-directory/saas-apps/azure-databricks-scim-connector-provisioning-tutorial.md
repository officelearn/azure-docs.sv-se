---
title: 'Självstudiekurs: Konfigurera Azure Databricks SCIM Connector för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du automatiskt etablerar och avoretrar användarkonton från Azure AD till Azure Databricks SCIM Connector.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: fe1260982edc877c049716bd74f1bb3e90d33b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370535"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Azure Databricks SCIM Connector för automatisk etablering av användare

I den här självstudien beskrivs de steg du behöver utföra i både Azure Databricks SCIM Connector och Azure Active Directory (Azure AD) för att konfigurera automatisk användaretablering. När azure AD är konfigurerat avsersättningar och avsersättningar avsorberar och avsättningar automatiskt till [Azure Databricks SCIM Connector](https://databricks.com/) med hjälp av Azure AD-etableringstjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Azure Databricks SCIM Connector
> * Ta bort användare i Azure Databricks SCIM Connector när de inte längre kräver åtkomst
> * Synkronisera användarattribut mellan Azure AD och Azure Databricks SCIM Connector
> * Etablera grupper och gruppmedlemskap i Azure Databricks SCIM Connector

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användarkonto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* Ett Azure Databricks-konto med administratörsbehörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera distributionen av etableringen
1. Läs mer om [hur etableringstjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bestäm vem som ska vara i [omfång för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bestäm vilka data som ska [mappas mellan Azure AD och Azure Databricks SCIM Connector](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Azure Databricks SCIM Connector för att stödja etablering med Azure AD

1. Om du vill konfigurera Azure Databricks SCIM-etablering lägger du till den som en resurs i din Azure Active Directory-klient och konfigurerar den med inställningarna nedan.

    ![Installation av Azure Databricks](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. Om du vill generera en personlig åtkomsttoken i Azure Databricks finns [detta](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management).

3. Kopiera **token**. Det här värdet anges i fältet Hemlig token på fliken Etablering i ditt Azure Databricks SCIM Connector-program i Azure-portalen.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>Steg 3. Lägga till Azure Databricks SCIM Connector från Azure AD-programgalleriet

Lägg till Azure Databricks SCIM Connector från Azure AD-programgalleriet för att börja hantera etablering till Azure Databricks SCIM Connector. Om du tidigare har konfigurerat Azure Databricks SCIM Connector för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integrationen från början. Läs mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska vara i utrymme för etablering 

Azure AD-etableringstjänsten gör att du kan begränsa vem som ska etableras baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att begränsa vem som ska etableras i din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att begränsa vem som ska etableras enbart baserat på attribut för användaren eller gruppen kan du använda ett omfångsfilter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till Azure Databricks SCIM Connector måste du välja en annan roll än **Standardåtkomst**. Användare med rollen Standardåtkomst är undantagna från etablering och markeras som inte effektivt berättigade i etableringsloggarna. Om den enda roll som är tillgänglig för programmet är standardåtkomstrollen kan du [uppdatera programmanifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Börja i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfång för etablering är inställt på tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När scopet är inställt på alla användare och grupper kan du ange ett [attributbaserat omfångsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>Steg 5. Konfigurera automatisk användaretablering till Azure Databricks SCIM Connector 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!NOTE]
> Mer information om Azure Databricks SCIM-slutpunkt finns [i detta](https://docs.databricks.com/dev-tools/api/latest/scim.html
).

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Azure Databricks SCIM Connector i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Azure Databricks SCIM Connector**i programlistan .

    ![Azure Databricks SCIM Connector-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du scim-slutpunktsvärdet i **klient-URL.** Klient-URL:en ska `https://<region>.azuredatabricks.net/api/2.0/preview/scim` vara i det format där **regionen** finns i url:en för startsidan för Azure Databricks. Till exempel kommer en SCIM-slutpunkt `https://westus.azuredatabricks.net/api/2.0/preview/scim`för **westus-regionen** att vara . Mata in tokenvärdet som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Azure Databricks SCIM Connector. Om anslutningen misslyckas kontrollerar du att ditt Azure Databricks SCIM Connector-konto har administratörsbehörighet och försök igen.

    ![Etableringen](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Azure Databricks SCIM Connector**.

9. Granska användarattributen som synkroniseras från Azure AD till Azure Databricks SCIM Connector i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Azure Databricks SCIM Connector för uppdateringsåtgärder. Om du väljer att ändra [det matchande målattributet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att Azure Databricks SCIM Connector API stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** om du vill utföra eventuella ändringar.

   |Attribut|Typ|
   |---|---|
   |userName|String|
   |displayName|String|
   |aktiv|Boolean|

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper till Azure Databricks SCIM Connector**.

11. Granska gruppattributen som synkroniseras från Azure AD till Azure Databricks SCIM Connector i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Azure Databricks SCIM Connector för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

     |Attribut|Typ|
     |---|---|
     |displayName|String|
     |medlemmar|Referens|

11. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper till Azure Databricks SCIM Connector**.

12. Om du vill aktivera Azure AD-etableringstjänsten för Azure Databricks SCIM Connector ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

13. Definiera de användare och/eller grupper som du vill etablera till Azure Databricks SCIM Connector genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

14. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den inledande synkroniseringscykeln för alla användare och grupper som **definierats** i Scope i avsnittet **Inställningar.** Den inledande cykeln tar längre tid att utföra än efterföljande cykler, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

* Använd [etableringsloggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller utan framgång
* Kontrollera [förloppsindikatorn](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etableringscykeln och hur nära den är till slutförande
* Om etableringskonfigurationen verkar vara i feltillstånd kommer programmet att placeras i karantän. Läs mer om karantäntillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Felsökningstips
* Databricks konverterar alltid sina användarnamnsvärden till gemener när de sparar till sin katalog oavsett hur stort vi skickar till dem via SCIM.
* För närvarande FÅ förfrågningar mot Azure Databricks SCIM API för USER@contoso.com användare är skiftlägeskänsliga, så user@contoso.comom vi fråga efter det kommer att komma med 0 resultat som de lagrar det som .

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../manage-apps/check-status-user-account-provisioning.md)
