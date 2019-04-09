---
title: 'Självstudier: Konfigurera Bonusly för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Bonusly.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad0ee590572dbc92e67be9f84ffc65afc3e8473
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278742"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Bonusly för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Bonusly och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Bonusly.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

* En Azure AD-klient
* En [Bonusly klient](https://bonus.ly/pricing)
* Ett användarkonto i Bonusly med administratörsbehörighet

> [!NOTE]
> Azure AD etablering integration förlitar sig på den [Bonusly Rest API](https://bonusly.gelato.io/reference), som är tillgängliga för Bonusly utvecklare.

## <a name="adding-bonusly-from-the-gallery"></a>Att lägga till Bonusly från galleriet

Du måste lägga till Bonusly från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar Bonusly för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till Bonusly från Azure AD-programgalleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Bonusly**väljer **Bonusly** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Bonusly i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Tilldela användare till Bonusly

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som är ”kopplade” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Bonusly. När du valt, kan du tilldela dessa användare och/eller grupper till Bonusly genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Viktiga tips för att tilldela användare till Bonusly

* Vi rekommenderar att en enda Azure AD-användare har tilldelats Bonusly att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Bonusly, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Konfigurera automatisk användaretablering för Bonusly

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Bonusly baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Bonusly, följa anvisningarna enligt den [Bonusly självstudien för enkel inloggning](bonus-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Konfigurera automatisk användaretablering för Bonusly i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och välj **företagsprogram**väljer **alla program**och välj sedan **Bonusly**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Bonusly**.

    ![Bonusly länken i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **hemlighet Token** för din Bonusly kontot enligt beskrivningen i steg 6.

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. Den **hemlighet Token** för din Bonusly kontot finns i **Admin > företag > integreringar**. I den **om du vill kod** klickar du på **API > Skapa åtkomsttoken för nya API: et** att skapa en ny hemlighet-Token.

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Ange ett namn på följande skärm för åtkomsttoken i textrutan, tryck sedan på **skapa Api-nyckel**. Ny åtkomsttoken visas under några sekunder i ett popup-fönster.

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/Token02.png)

8. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till Bonusly. Om anslutningen misslyckas, kontrollera din Bonusly kontot har administratörsbehörighet och försök igen.

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan **skicka ett e-postmeddelande när ett fel inträffar**.

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Klicka på **Spara**.

11. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Bonusly**.

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Granska användarattribut som synkroniseras från Azure AD till Bonusly i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Bonusly för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Om du vill aktivera den Azure AD-etableringstjänsten för Bonusly, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Ange användare och/eller grupper som du vill kan etableras på Bonusly genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. När du är redo att etablera, klickar du på **spara**.

    ![Bonusly etablering](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Bonusly.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
