---
title: 'Självstudier: Konfigurera Leapsome för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Leapsome.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: e02deaa29b40e64b63d9afb471717feef78b3cee
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672683"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Leapsome för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Leapsome och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Leapsome.

> [!NOTE]
>  Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningsappen är för närvarande i förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* En [Leapsome](https://www.Leapsome.com/en/pricing) klient.
* Ett användarkonto i Leapsome med administratörsbehörighet.

## <a name="assigning-users-to-leapsome"></a>Tilldela användare till Leapsome

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Leapsome. När du valt, kan du tilldela dessa användare och/eller grupper till Leapsome genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Viktiga tips för att tilldela användare till Leapsome

* Vi rekommenderar att en enda Azure AD-användare har tilldelats Leapsome att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Leapsome, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.


## <a name="setup-leapsome-for-provisioning"></a>Konfigurera Leapsome för etablering

1. Logga in på din [Leapsome administratörskonsolen](https://www.Leapsome.com/app/#/login). Gå till **Inställningar > administratörsinställningar**.

    ![Leapsome-administratörskonsolen](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Gå till **integreringar > SCIM användaretablering**.

    ![Leapsome Lägg till SCIM](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Kopiera den **SCIM autentiseringstoken**. Det här värdet ska anges i fältet Hemlig Token på fliken etablering Leapsome program i Azure-portalen.

    ![Leapsome skapa Token](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Lägg till Leapsome från galleriet

Du måste lägga till Leapsome från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar Leapsome för automatisk användarförsörjning med Azure AD.

**Utför följande steg för att lägga till Leapsome från Azure AD-programgalleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Leapsome**väljer **Leapsome** i resultatrutan och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![Leapsome i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Konfigurera automatisk användaretablering för Leapsome 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Leapsome baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Leapsome, följa anvisningarna enligt den [Leapsome enkel inloggning för självstudien](Leapsome-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>Konfigurera automatisk användaretablering för Leapsome i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Leapsome**.

    ![Länken Leapsome i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering](common/provisioning.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering](common/provisioning-automatic.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet Ange `https://www.leapsome.com/api/scim` i **klient-URL**. Indata i **SCIM-autentiseringstoken** värdet som hämtades tidigare i **hemlighet Token**. Klicka på **Testanslutningen** att se till att Azure AD kan ansluta till Leapsome. Om anslutningen misslyckas, kontrollera Leapsome-kontot har administratörsbehörighet och försök igen.

    ![Klient-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Leapsome**.

    ![Leapsome Användarmappningar](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. Granska användarattribut som synkroniseras från Azure AD till Leapsome i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Leapsome för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Leapsome användarattribut](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper till Leapsome**.

    ![Leapsome grupp mappningar](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. Granska Gruppattribut som synkroniseras från Azure AD till Leapsome i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha grupper i Leapsome för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Attributen för Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera den Azure AD-etableringstjänsten för Leapsome, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Etableringsstatus aktivt](common/provisioning-toggle-on.png)

14. Ange användare och/eller grupper som du vill kan etableras på Leapsome genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Etablering omfång](common/provisioning-scope.png)

15. När du är redo att etablera, klickar du på **spara**.

    ![Sparar Etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Leapsome.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Leapsome kräver **användarnamn** vara unikt.
* Leapsome tillåter endast e-postadresser som ska sparas.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)
