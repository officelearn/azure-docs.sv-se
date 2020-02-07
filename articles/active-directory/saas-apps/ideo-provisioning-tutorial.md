---
title: 'Självstudie: Konfigurera IDEO för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till IDEO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: de4f06a3-83e9-46ce-80ee-03d706b91c81
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2019
ms.author: Zhchia
ms.openlocfilehash: f5f163109d648a4fc021b41325c6d585a5a7a3e7
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057608"
---
# <a name="tutorial-configure-ideo-for-automatic-user-provisioning"></a>Självstudie: Konfigurera IDEO för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i IDEO och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till IDEO.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En IDEO-klient](https://www.shape.space/product/pricing)
* Ett användar konto på IDEO | Form med administratörs behörighet.

## <a name="assign-users-to-ideo"></a>Tilldela användare till IDEO

Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till IDEO. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till IDEO genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-ideo"></a>Viktiga tips för att tilldela användare till IDEO

* Vi rekommenderar att en enda Azure AD-användare tilldelas IDEO för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till IDEO måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-ideo-for-provisioning"></a>Konfigurera IDEO för etablering

Innan du konfigurerar IDEO för automatisk användar etablering med Azure AD måste du hämta viss etablerings information från IDEO.

1. Kontakta IDEO support team på productsupport@ideo.comför **hemliga token** . Det här värdet anges i fältet **hemlig token** på fliken etablering i ditt ideo-program i Azure Portal. 

## <a name="add-ideo-from-the-gallery"></a>Lägg till IDEO från galleriet

Om du vill konfigurera IDEO för automatisk användar etablering med Azure AD måste du lägga till IDEO från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **ideo**och väljer **ideo** i panelen resultat. 

    ![IDEO i resultat listan](common/search-new-app.png)

5. Välj knappen **Registrera dig för ideo** som kommer att omdirigera dig till inloggnings sidan för ideo. 

    ![IDEO OIDC Lägg till](media/ideo-provisioning-tutorial/signup.png)

6. Som IDEO är en OpenIDConnect-app väljer du att logga in på IDEO med ditt Microsoft Work-konto.

    ![IDEO OIDC-inloggning](media/ideo-provisioning-tutorial/login.png)

7. När autentiseringen är klar godkänner du frågan om medgivande för sidan medgivande. Programmet läggs sedan till automatiskt i din klient organisation och du omdirigeras till ditt IDEO-konto.

    ![IDEO OIDc-medgivande](media/ideo-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-ideo"></a>Konfigurera automatisk användar etablering till IDEO 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i IDEO baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ideo-in-azure-ad"></a>Konfigurera automatisk användar etablering för IDEO i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **ideo**.

    ![IDEO-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , in`https://profile.ideo.com/api/scim/v2` i **klient-URL**. Mata in det värde som du hämtade från IDEO support team i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till ideo. Om anslutningen Miss lyckas kontrollerar du att IDEO-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Save** (Spara).

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till ideo**.

    ![IDEO användar mappningar](media/ideo-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till IDEO i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i ideo för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![IDEO-användarattribut](media/ideo-provisioning-tutorial/userattributes.png)

10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för IDEO ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](media/ideo-provisioning-tutorial/groupmappings.png)

12. Definiera de användare och/eller grupper som du vill etablera till IDEO genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](media/ideo-provisioning-tutorial/groupattributes.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Ideo.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)


