---
title: 'Självstudie: Konfigurera Samanage för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060532"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Samanage för automatisk användar etablering

Den här självstudien visar de steg som du utför i Samanage och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och grupper till Samanage.

> [!NOTE]
> I den här självstudien beskrivs en koppling som är byggd ovanpå Azure AD-tjänsten för användar etablering. Information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering av SaaS-program (Software-as-a-Service) med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du har:

* En Azure AD-klientorganisation.
* En [Samanage-klient](https://www.samanage.com/pricing/) med Professional-paketet.
* Ett användar konto i Samanage med administratörs behörighet.

> [!NOTE]
> Azure AD Provisioning-integrationen är beroende av [Samanage REST API](https://www.samanage.com/api/). Detta API är tillgängligt för Samanage-utvecklare för konton med Professional-paketet.

## <a name="add-samanage-from-the-azure-marketplace"></a>Lägg till Samanage från Azure Marketplace

Innan du konfigurerar Samanage för automatisk användar etablering med Azure AD lägger du till Samanage från Azure Marketplace till din lista över hanterade SaaS-program.

Följ dessa steg om du vill lägga till Samanage från Marketplace.

1. I navigerings fönstret till vänster i [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**.

    ![Ikonen Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Samanage** och väljer **Samanage** i resultat panelen. Om du vill lägga till programmet väljer du **Lägg till**.

    ![Samanage i resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Tilldela användare till Samanage

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användar etablering synkroniseras endast de användare eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering ska du bestämma vilka användare eller grupper i Azure AD som behöver åtkomst till Samanage. Om du vill tilldela dessa användare eller grupper till Samanage följer du instruktionerna i [tilldela en användare eller grupp till en Enterprise-App](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Viktiga tips för att tilldela användare till Samanage

*    Idag fylls Samanage-roller i automatiskt och dynamiskt i Azure Portal gränssnittet. Innan du tilldelar Samanage roller till användare ser du till att en inledande synkronisering har slutförts mot Samanage för att hämta de senaste rollerna i Samanage-klienten.

*    Vi rekommenderar att du tilldelar en enda Azure AD-användare till Samanage för att testa den första konfigurationen för automatisk användar etablering. Du kan tilldela ytterligare användare och grupper senare när testerna har slutförts.

*    När du tilldelar en användare till Samanage väljer du en giltig programspecifik roll, om sådan finns, i dialog rutan tilldelning. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Konfigurera automatisk användar etablering till Samanage

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten. Använd den för att skapa, uppdatera och inaktivera användare eller grupper i Samanage baserat på användar-eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för Samanage. Följ anvisningarna i [självstudien om Samanage enkel inloggning](samanage-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurera automatisk användar etablering för Samanage i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program** > **alla program** > **Samanage**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Samanage**.

    ![Samanage-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Samanage-etablering](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , skriver du in din Samanage- **klient-URL och en** **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Samanage. Om anslutningen Miss lyckas kontrollerar du att Samanage-kontot har administratörs behörighet och försöker igen.

    ![Samanage test anslutning](./media/samanage-provisioning-tutorial/provisioning.png)

6. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot etablerings fel meddelanden. Markera kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-Samanage e-postmeddelande](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Samanage**.

    ![Samanage-synkronisering av användare](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Samanage i avsnittet **mappningar för attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Samanage för uppdaterings åtgärder. Välj **Spara**om du vill spara ändringarna.

    ![Samanage matchning av användarattribut](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Om du vill aktivera grupp mappningar går du till avsnittet **mappningar** och väljer **Synkronisera Azure Active Directory grupper till Samanage**.

    ![Synkronisering av Samanage-grupp](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. Ställ in **aktive rad** på **Ja** för att synkronisera grupper. Granska gruppattributen som synkroniseras från Azure AD till Samanage i avsnittet **mappningar för attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Samanage för uppdaterings åtgärder. Välj **Spara**om du vill spara ändringarna.

    ![Samanage matchning av Gruppattribut](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Följ anvisningarna i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)för att konfigurera omfångs filter.

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Samanage går du till avsnittet **Inställningar** och ändrar **etablerings statusen** till **på**.

    ![Etablerings status för Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Definiera de användare eller grupper som du vill etablera till Samanage. I avsnittet **Inställningar** väljer du de värden som du vill ha i **omfånget**. När du väljer alternativet **synkronisera alla användare och grupper** bör du tänka på begränsningarna som beskrivs i följande avsnitt "kopplings begränsningar".

    ![Samanage-omfång](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. När du är redo att etablera väljer du **Spara**.

    ![Spara Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än senare synkroniseringar. De inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. 

Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten. Rapporten beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Samanage.

Information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

Om du väljer alternativet **synkronisera alla användare och grupper** och konfigurerar ett värde för attributet Samanage **roles** måste värdet under **standardvärdet om null (är valfritt)** anges i följande format:

- {"displayName": "roll"}, där roll är det standardvärde som du vill använda.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
