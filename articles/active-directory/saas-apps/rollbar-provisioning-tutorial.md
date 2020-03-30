---
title: 'Självstudiekurs: Konfigurera Rollbar för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Rollbar.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d737aa16-8ab4-4c0c-a68b-2911623b41eb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 27a26a0c8378f34794afd87cf11b6bb878f7b53c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78248440"
---
# <a name="tutorial-configure-rollbar-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera rollbar för automatisk etablering av användare

I den här självstudien beskrivs de steg du behöver utföra i både Rollbar och Azure Active Directory (Azure AD) för att konfigurera automatisk användaretablering. När azure AD är konfigurerat avsersättningar och avsersättningar avsättningar till [Rollbar](https://rollbar.com/pricing/) med hjälp av Azure AD-etableringstjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Rollbar
> * Ta bort användare i Rollbar när de inte längre behöver åtkomst
> * Synkronisera användarattribut mellan Azure AD och Rollbar
> * Etableringsgrupper och gruppmedlemskap i Rollbar
> * [Enkel inloggning till](https://docs.microsoft.com/azure/active-directory/saas-apps/rollbar-tutorial) Rollbar (rekommenderas)

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användarkonto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* [En Rollbar-klient](https://rollbar.com/pricing/) som har en enterprise-plan.
* Ett användarkonto i Rollbar med administratörsbehörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera distributionen av etableringen
1. Läs mer om [hur etableringstjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bestäm vem som ska vara i [omfång för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bestäm vilka data som ska [mappas mellan Azure AD och Rollbar](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-rollbar-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Rollbar för att stödja etablering med Azure AD

Innan du konfigurerar Rollbar för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på Rollbar.

1. Logga in på [rollbar-administratörskonsolen](https://rollbar.com/login/). Klicka på **Kontoinställningar**.

    ![Administratörskonsol för rollbar](media/rollbar-provisioning-tutorial/image00.png)

2. Navigera till **namn på rollbar-klienten > identitetsprovider**.

    ![Identitetsprovider för rollbar](media/rollbar-provisioning-tutorial/idp.png)

3. Bläddra ned till **Etableringsalternativ**. Kopiera åtkomsttoken. Det här värdet anges i fältet **Hemlig token** på etableringsfliken i rollbar-programmet i Azure-portalen. Markera kryssrutan Aktivera etablering av **användare och team** och klicka på **Spara**.

    ![Åtkomsttoken för rollbar](media/rollbar-provisioning-tutorial/token.png)


## <a name="step-3-add-rollbar-from-the-azure-ad-application-gallery"></a>Steg 3. Lägga till Rollbar från Azure AD-programgalleriet

Lägg till Rollbar från Azure AD-programgalleriet för att börja hantera etablering till Rollbar. Om du tidigare har konfigurerat Rollbar för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integrationen från början. Läs mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska vara i utrymme för etablering 

Azure AD-etableringstjänsten gör att du kan begränsa vem som ska etableras baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att begränsa vem som ska etableras i din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att begränsa vem som ska etableras enbart baserat på attribut för användaren eller gruppen kan du använda ett omfångsfilter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till Rollbar måste du välja en annan roll än **Standardåtkomst**. Användare med rollen Standardåtkomst är undantagna från etablering och markeras som inte effektivt berättigade i etableringsloggarna. Om den enda roll som är tillgänglig för programmet är standardåtkomstrollen kan du [uppdatera programmanifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Börja i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfång för etablering är inställt på tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När scopet är inställt på alla användare och grupper kan du ange ett [attributbaserat omfångsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-rollbar"></a>Steg 5. Konfigurera automatisk användaretablering till Rollbar 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-rollbar-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Rollbar i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Rollbar**i programlistan .

    ![Länken Rollbar i listan Program](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du åtkomsttokenvärdet som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Rollbar. Om anslutningen misslyckas kontrollerar du att Ditt Rollbar-konto har administratörsbehörighet och försöker igen.

    ![Etablering](./media/rollbar-provisioning-tutorial/admin.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Rollbar**.

9. Granska användarattributen som synkroniseras från Azure AD till Rollbar i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Rollbar för uppdateringsåtgärder. Om du väljer att ändra det [matchande målattributet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att Rollbar-API:et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** om du vill utföra eventuella ändringar.

   |Attribut|Typ|
   |---|---|
   |userName|String|
   |externt|String|
   |aktiv|Boolean|
   |name.familyName|String|
   |name.givenName|String|
   |e-postmeddelanden[typ eq "arbete"]|String|

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper mot Rolls .**

11. Granska gruppattributen som synkroniseras från Azure AD till Rollbar i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Rollbar för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

      |Attribut|Typ|
      |---|---|
      |displayName|String|
      |externt|String|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Rollbar ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Rollbar genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den inledande synkroniseringscykeln för alla användare och grupper som **definierats** i Scope i avsnittet **Inställningar.** Den inledande cykeln tar längre tid att utföra än efterföljande cykler, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

1. Använd [etableringsloggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller utan framgång
2. Kontrollera [förloppsindikatorn](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etableringscykeln och hur nära den är till slutförande
3. Om etableringskonfigurationen verkar vara i feltillstånd kommer programmet att placeras i karantän. Läs mer om karantäntillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../manage-apps/check-status-user-account-provisioning.md)
