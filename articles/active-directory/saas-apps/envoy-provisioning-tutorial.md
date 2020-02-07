---
title: 'Självstudie: konfigurera mottagare för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till mottagare.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 30faae80f1af4ff63924a76b26a03b8fe354a7df
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058033"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Självstudie: konfigurera mottagare för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i mottagare och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till mottagare.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En mottagare-klient](https://envoy.com/pricing/)
* Ett användar konto i mottagare med administratörs behörighet.

## <a name="add-envoy-from-the-gallery"></a>Lägg till mottagare från galleriet

Innan du konfigurerar mottagare för automatisk användar etablering med Azure AD måste du lägga till mottagare från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till mottagare från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **mottagare**, väljer **mottagare** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Envoy i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-envoy"></a>Tilldela användare till mottagare

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till mottagare. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till mottagare genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-envoy"></a>Viktiga tips för att tilldela användare till mottagare

* Vi rekommenderar att en enda Azure AD-användare tilldelas mottagare för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till mottagare måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-envoy"></a>Konfigurera automatisk användar etablering till mottagare 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i mottagare baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för mottagare genom att följa anvisningarna i [självstudien om enkel inloggning med mottagare](envoy-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Konfigurera automatisk användar etablering för mottagare i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Envoy**.

    ![Länken för Envoy i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , in`https://app.envoy.com/scim/v2` i **klient-URL**. Om du vill hämta den **hemliga token** för ditt mottagare-konto följer du genom gången enligt beskrivningen i steg 6.

6. Logga in på din [mottagare-administratörs konsol](https://dashboard.envoy.com/login). Klicka på **integreringar**.

    ![Mottagare-integreringar](media/envoy-provisioning-tutorial/envoy01.png)

    Klicka på **Installera** för **Microsoft Azure scim-integrering**.

    ![Mottagare-installation](media/envoy-provisioning-tutorial/envoy02.png)

    Klicka på **Spara** för att **synkronisera alla användare**. 

    ![Spara mottagare](media/envoy-provisioning-tutorial/envoy03.png)

    Hämta den hemliga token som är ifylld.
    
    ![Mottagare OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

7. När du fyller i fälten som visas i steg 5, klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till mottagare. Om anslutningen Miss lyckas kontrollerar du att mottagare-kontot har administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Save** (Spara).

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till mottagare**.
    
    ![Mottagare-användarattribut](media/envoy-provisioning-tutorial/envoy-user-mappings.png)
    
11. Granska de användarattribut som synkroniseras från Azure AD till mottagare i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i mottagare för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Mottagare-användarattribut](media/envoy-provisioning-tutorial/envoy-user-attribute.png)

12. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till mottagare**.

    ![Mottagare användar mappningar](media/envoy-provisioning-tutorial/envoy-group-mapping.png)

13. Granska gruppattributen som synkroniseras från Azure AD till mottagare i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i mottagare för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Mottagare användar mappningar](media/envoy-provisioning-tutorial/envoy-group-attributes.png)
    
14. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD Provisioning-tjänsten för mottagare ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

16. Definiera de användare och/eller grupper som du vill etablera till mottagare genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på mottagare.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

