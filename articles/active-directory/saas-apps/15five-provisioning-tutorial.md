---
title: 'Självstudie: Konfigurera 15Five för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till 15Five.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: f1f66a7b69048180bc41c8f2fa432598f00f7f09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77059326"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Självstudie: Konfigurera 15Five för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i 15Five och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till 15Five.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En 15Five-klient](https://www.15five.com/pricing/).
* Ett användar konto i 15Five med administratörs behörighet.

## <a name="assigning-users-to-15five"></a>Tilldela användare till 15Five

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till 15Five. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till 15Five genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-15five"></a>Viktiga tips för att tilldela användare till 15Five

* Vi rekommenderar att en enda Azure AD-användare tilldelas 15Five för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till 15Five måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-15five-for-provisioning"></a>Konfigurera 15Five för etablering

Innan du konfigurerar 15Five för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på 15Five.

1. Logga in på din [15Five-administratörs konsol](https://my.15five.com/). Navigera till **funktioner > integreringar**.

    ![15Five-administratörskonsolen](media/15five-provisioning-tutorial/integration.png)

2.  Klicka på **SCIM 2,0**.

    ![15Five-administratörskonsolen](media/15five-provisioning-tutorial/image00.png)

3.  Navigera till **scim-integration > generera OAuth-token**.

    ![15Five Lägg till SCIM](media/15five-provisioning-tutorial/image02.png)

4.  Kopiera värdena för **SCIM 2,0-bas-URL** och **åtkomsttoken**. Det här värdet anges i fältet **klient-URL** och **hemligt token** på fliken etablering i 15Five-programmet i Azure Portal.
    
    ![15Five Lägg till SCIM](media/15five-provisioning-tutorial/image03.png)

## <a name="add-15five-from-the-gallery"></a>Lägg till 15Five från galleriet

Om du vill konfigurera 15Five för automatisk användar etablering med Azure AD måste du lägga till 15Five från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till 15Five från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **15Five**, väljer **15Five** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![15Five i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-15five"></a>Konfigurera automatisk användar etablering till 15Five 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i 15Five baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för 15Five genom att följa anvisningarna i [självstudien om enkel inloggning med 15Five](15five-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Konfigurera automatisk användar etablering för 15Five i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **15Five**.

    ![Länken för 15Five i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5.  Under avsnittet admin credentials måste du skriva in **SCIM 2,0-bas-URL: en och få åtkomst till tokens** som hämtades tidigare i **klient-URL** respektive **hemlig token** . Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till 15Five. Om anslutningen Miss lyckas kontrollerar du att 15Five-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till 15Five**.

    ![15Five användar mappningar](media/15five-provisioning-tutorial/usermapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till 15Five i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i 15Five för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![15Five-användarattribut](media/15five-provisioning-tutorial/userattribute.png)

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till 15Five**.

    ![15Five grupp mappningar](media/15five-provisioning-tutorial/groupmapping.png)

11. Granska gruppattributen som synkroniseras från Azure AD till 15Five i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i 15Five för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![15Five grupp-attribut](media/15five-provisioning-tutorial/groupattribute.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för 15Five ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till 15Five genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på 15Five.

    Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Kopplings begränsningar

* 15Five stöder inte hårda borttagningar för användare.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md).
