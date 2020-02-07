---
title: 'Självstudie: Konfigurera Rollbar för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Rollbar.
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
ms.author: zhchia
ms.openlocfilehash: d9720ca769eab8cf0e4ee763c720f6ba12ebb1d9
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063313"
---
# <a name="tutorial-configure-rollbar-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Rollbar för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Rollbar och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Rollbar.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En Rollbar-klient](https://rollbar.com/pricing/) som har en företags plan.
* Ett användar konto i Rollbar med administratörs behörighet.

## <a name="assigning-users-to-rollbar"></a>Tilldela användare till Rollbar

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Rollbar. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Rollbar genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rollbar"></a>Viktiga tips för att tilldela användare till Rollbar

* Vi rekommenderar att en enda Azure AD-användare tilldelas Rollbar för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Rollbar måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-rollbar-for-provisioning"></a>Konfigurera Rollbar för etablering

Innan du konfigurerar Rollbar för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på Rollbar.

1. Logga in på din [Rollbar-administratörs konsol](https://rollbar.com/login/). Klicka på **konto inställningar**.

    ![Rollbar-administratörskonsolen](media/rollbar-provisioning-tutorial/image00.png)

2. Navigera till **Rollbar-klientens namn > kontots**åtkomsttoken.

    ![Rollbar-administratörskonsolen](media/rollbar-provisioning-tutorial/account.png)

3. Kopiera värdet för **scim**. Det här värdet anges i fältet Hemlig token på fliken etablering i ditt Rollbar-program i Azure Portal.

    ![Rollbar-administratörskonsolen](media/rollbar-provisioning-tutorial/scim.png)

## <a name="add-rollbar-from-the-gallery"></a>Lägg till Rollbar från galleriet

Om du vill konfigurera Rollbar för automatisk användar etablering med Azure AD måste du lägga till Rollbar Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Rollbar från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Rollbar**, väljer **Rollbar** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Rollbar i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rollbar"></a>Konfigurera automatisk användar etablering till Rollbar 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Rollbar baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Rollbar genom att följa anvisningarna i [självstudien om enkel inloggning med Rollbar](rollbar-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-rollbar-in-azure-ad"></a>Konfigurera automatisk användar etablering för Rollbar i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Rollbar**.

    ![Rollbar-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** måste du skriva in värdet för **token för konto åtkomst** som hämtades tidigare till en **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Rollbar. Om anslutningen Miss lyckas kontrollerar du att Rollbar-kontot har administratörs behörighet och försöker igen.

    ![Rollbar-administratörskonsolen](media/rollbar-provisioning-tutorial/admin.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Save** (Spara).

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Rollbar**.

    ![Rollbar användar mappningar](media/rollbar-provisioning-tutorial/usermapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Rollbar i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Rollbar för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Rollbar-användarattribut](media/rollbar-provisioning-tutorial/userattribute.png)

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Rollbar**.

    ![Rollbar grupp mappningar](media/rollbar-provisioning-tutorial/groupmapping.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Rollbar i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Rollbar för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Rollbar grupp-attribut](media/rollbar-provisioning-tutorial/groupattribute.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Rollbar ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Rollbar genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Rollbar.

    Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
