---
title: 'Självstudier: Konfigurera Wrike för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Wrike.
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
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: f94c12ed355b25e6a10c8d71e176d9bf3bf78758
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70847106"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Wrike för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Wrike och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Wrike.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Wrike-klient](https://www.wrike.com/price/).
* Ett användar konto i Wrike med administratörs behörighet.

## <a name="assign-users-to-wrike"></a>Tilldela användare till Wrike
Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Wrike. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Wrike genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Viktiga tips för att tilldela användare till Wrike

* Vi rekommenderar att en enda Azure AD-användare tilldelas Wrike för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Wrike måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-wrike-for-provisioning"></a>Konfigurera Wrike för etablering

Innan du konfigurerar Wrike för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på Wrike.

1. Logga in på din [Wrike-administratörs konsol](https://www.Wrike.com/login/). Navigera till klient-ID: t. Välj **appar &-integreringar**.

    ![Wrike-administratörskonsolen](media/Wrike-provisioning-tutorial/admin.png)

2.  Gå till **Azure AD**. Markera det.

    ![Wrike-administratörskonsolen](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Välj SCIM. Kopiera **bas-URL: en**.

    ![Wrike-administratörskonsolen](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Klicka på **API** > Välj **Azure-scim**.

    ![Wrike Lägg till SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Ett popup-fönster öppnas. Ange samma lösen ord som du skapade tidigare för att skapa konto 

    ![Skapa token för Wrike](media/Wrike-provisioning-tutorial/password.png)

6.  Kopiera den **hemliga token** och klistra in den i Azure AD. Klicka på **Spara** för att slutföra etablerings konfigurationen på Wrike.

    ![Skapa token för Wrike](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Lägg till Wrike från galleriet

Innan du konfigurerar Wrike för automatisk användar etablering med Azure AD måste du lägga till Wrike från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Wrike från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Wrike**, väljer **Wrike** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Wrike i resultat listan](common/search-new-app.png)


## <a name="configuring-automatic-user-provisioning-to-wrike"></a>Konfigurera automatisk användar etablering till Wrike 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Wrike baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Wrike genom att följa anvisningarna i [självstudien om enkel inloggning med Wrike](wrike-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Konfigurera automatisk användar etablering för Wrike i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Wrike**.

    ![Wrike-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet admin credentials, skriver du in **bas-URL: en** och värdena för **permanent åtkomst-token** som hämtades tidigare i **klient-URL** respektive **hemlig token** Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Wrike. Om anslutningen Miss lyckas kontrollerar du att Wrike-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-post för aviseringar](common/provisioning-notification-email.png)

8. Klicka på **Spara**.

9. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Wrike**.

    ![Wrike användar mappningar](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Granska de användarattribut som synkroniseras från Azure AD till Wrike i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Wrike för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Wrike-användarattribut](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD Provisioning-tjänsten för Wrike ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

13. Definiera de användare och/eller grupper som du vill etablera till Wrike genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

14. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information om hur lång tid det tar för användare och/eller grupper att etablera finns i [hur lång tid det tar att etablera användare](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Du kan använda avsnittet **aktuell status** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Wrike. Mer information finns i [kontrol lera status för användar etablering](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). För att läsa Azure AD-etablerings loggarna, se [rapportering om automatisk etablering av användar konton](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
