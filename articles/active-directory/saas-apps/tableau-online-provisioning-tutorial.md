---
title: 'Självstudier: Konfigurera Tableau Online för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Tableau Online.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dbebfa5fa7d9b255cc685696bfe8b3f61d5cf6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123762"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Tableau Online för automatisk användaretablering

Den här kursen visar hur du utför i Tableau Online och Azure Active Directory (AD Azure) att konfigurera Azure AD att automatiskt etablera och avetablera användare och grupper till Tableau Online.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-tjänst för användaretablering. Information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns [automatisera användaretablering och avetablering för software-as-a-service (SaaS)-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du har:

*   En Azure AD-klientorganisation.
*   En [Tableau Online klient](https://www.tableau.com/).
*   Ett användarkonto i Tableau Online med administratörsbehörighet.

> [!NOTE]
> Azure AD etablering integration förlitar sig på den [Tableau Online Rest API: T](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Detta API är tillgängliga för utvecklare som Tableau Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Lägg till Tableau Online från Azure Marketplace
Innan du konfigurerar Tableau Online för automatisk användarförsörjning med Azure AD lägger du till Tableau Online från Azure Marketplace till din lista över hanterade SaaS-program.

Följ dessa steg för att lägga till Tableau Online från Marketplace.

1. I den [Azure-portalen](https://portal.azure.com), i navigeringsfönstret till vänster, Välj **Azure Active Directory**.

    ![Azure Active Directory-ikonen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Tableau Online** och välj **Tableau Online** från panelen resultatet. Om du vill lägga till programmet, Välj **Lägg till**.

    ![Tableau Online i resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Tilldela användare till Tableau Online

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras de användare eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, kan du bestämma vilka användare eller grupper i Azure AD behöver åtkomst till Tableau Online. Om du vill tilldela dessa användare eller grupper till Tableau Online, följer du anvisningarna i [tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Viktiga tips för att tilldela användare till Tableau Online

*   Vi rekommenderar att du tilldelar en enda Azure AD-användare till Tableau Online för att testa konfigurationen för automatisk användaretablering. Du kan tilldela ytterligare användare eller grupper senare.

*   När du tilldelar en användare till Tableau Online, Välj en giltig programspecifika roll om det finns i dialogrutan tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Konfigurera automatisk användaretablering till Tableau Online

Det här avsnittet vägleder dig genom stegen för att konfigurera den Azure AD-etableringstjänsten. Du kan använda den för att skapa, uppdatera och inaktivera användare eller grupper i Tableau Online baserat på användar- eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för Tableau Online. Följ instruktionerna i den [Tableau enkel inloggning onlinesjälvstudien](tableauonline-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Konfigurera automatisk användaretablering för Tableau Online i Azure AD

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram** > **alla program** > **Tableau Online**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Tableau Online**.

    ![Länken Tableau Online i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Tableau Online Etableringsläge](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Under den **administratörsautentiseringsuppgifter** anger du domänen, administratörens användarnamn, lösenord för serveradministratören och innehålls-URL för ditt Tableau Online-konto:

   * I den **domän** kryssar i underdomänen baserat på steg 6.

   * I den **Admin Username** kryssar du i användarnamnet för administratörskontot på Clarizen-klienten. Ett exempel är admin@contoso.com.

   * I den **adminlösenord** kryssar du i lösenordet för administratörskontot som motsvarar administratörens användarnamn.

   * I den **Innehållswebbadress** kryssar i underdomänen baserat på steg 6.

6. När du loggar in på ditt administratörskonto för Tableau Online, kan du hämta värdena för **domän** och **Innehållswebbadress** från URL: en för sidan.

    * Den **domän** för Tableau Online konto kan kopieras från den här delen av URL: en:

        ![Tableau Online domän](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * Den **Innehållswebbadress** för Tableau Online konto kan kopieras från det här avsnittet. Det är ett värde som har definierats under kontoinstallationen. Värdet är ”contoso” i det här exemplet:

        ![Tableau Online innehålls-URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Din **domän** kan skilja sig från den som visas här.

7. När du har fyllt i rutorna som visas i steg 5 väljer **Testanslutningen** för att se till att Azure AD kan ansluta till Tableau Online. Om anslutningen misslyckas se till att ditt Tableau Online-konto har administratörsbehörighet och försök igen.

    ![Tableau Online Testa anslutning](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. I den **e-postmeddelande** anger du e-postadressen för personen eller grupp för att ta emot meddelanden etablering fel. Välj den **skicka ett e-postmeddelande när ett fel inträffar** markerar du kryssrutan.

    ![Tableau Online e-postmeddelandet](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Välj **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Tableau**.

    ![Tableau Online Användarsynkronisering](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Granska användarattribut som ska synkroniseras från Azure AD med Tableau Online i den **attributmappningar** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Tableau Online för uppdateringsåtgärder. Om du vill spara ändringarna, Välj **spara**.

    ![Tableau Online matchande användarattribut](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper som Tableau**.

    ![Tableau Online Gruppsynkronisering](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Granska Gruppattribut som synkroniseras från Azure AD till Tableau Online i den **attributmappningar** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Tableau Online för uppdateringsåtgärder. Om du vill spara ändringarna, Välj **spara**.

    ![Tableau Online matchande grupp-attribut](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Om du vill konfigurera Omfångsfilter, följer du anvisningarna i den [målgrupp filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Så här aktiverar du den Azure AD-etableringstjänsten för Tableau Online i den **inställningar** ändrar **Etableringsstatus** till **på**.

    ![Tableau Online Etableringsstatus](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definiera de användare eller grupper som du vill att etablera till Tableau Online. I den **inställningar** väljer du de värden som du vill ha i **omfång**.

    ![Tableau Online omfång](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. När du är redo att etablera väljer **spara**.

    ![Tableau Online spara](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än senare synkroniseringar. De inträffar ungefär var 40 minut så länge den Azure AD-etableringtjänsten körs. 

Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följa länkarna till aktivitetsrapporten för etablering. Rapporten beskriver de åtgärder som utförs av den Azure AD-etableringtjänsten på Tableau Online.

Information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
