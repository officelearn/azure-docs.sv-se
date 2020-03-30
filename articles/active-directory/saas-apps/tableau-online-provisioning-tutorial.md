---
title: 'Självstudiekurs: Konfigurera Tableau Online för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
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
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fae770950810899f7c6583fa401110c3e85022b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064229"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Tableau Online för automatisk användaretablering

Den här självstudien visar stegen för att utföra i Tableau Online och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och grupper till Tableau Online.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som är byggd ovanpå Azure AD-användarens etableringstjänst. Information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program (Software-as-a-Service) med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du har:

*   En Azure AD-klientorganisation.
*   En [Tableau Online-klient](https://www.tableau.com/).
*   Ett användarkonto i Tableau Online med administratörsbehörighet.

> [!NOTE]
> Integreringen av Azure AD-etablering är beroende av Api:et [för tableau online-vila](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Det här API:et är tillgängligt för Tableau Online-utvecklare.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Lägga till Tableau Online från Azure Marketplace
Innan du konfigurerar Tableau Online för automatisk användaretablering med Azure AD lägger du till Tableau Online från Azure Marketplace i listan över hanterade SaaS-program.

Så här lägger du till Tableau Online från Marketplace.

1. I [Azure-portalen](https://portal.azure.com)väljer du **Azure Active Directory**i navigeringsfönstret till vänster .

    ![Ikon för Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Tableau Online** och väljer **Tableau Online** på resultatpanelen. Om du vill lägga till programmet väljer du **Lägg till**.

    ![Tableau Online i resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Tilldela användare till Tableau Online

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bestämmer du vilka användare eller grupper i Azure AD som behöver åtkomst till Tableau Online. Om du vill tilldela dessa användare eller grupper till Tableau Online följer du instruktionerna i [Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Viktiga tips för att tilldela användare till Tableau Online

*   Vi rekommenderar att du tilldelar en enda Azure AD-användare till Tableau Online för att testa konfigurationen för automatisk användaretablering. Du kan tilldela ytterligare användare eller grupper senare.

*   När du tilldelar en användare till Tableau Online väljer du en giltig programspecifik roll, om sådan finns, i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Konfigurera automatisk användaretablering till Tableau Online

Det här avsnittet hjälper dig att konfigurera Azure AD-etableringstjänsten. Använd den för att skapa, uppdatera och inaktivera användare eller grupper i Tableau Online baserat på användar- eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för Tableau Online. Följ instruktionerna i den [enda inloggningskursen i Tableau Online](tableauonline-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Konfigurera automatisk användaretablering för Tableau Online i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** > **Alla program** > **Tableau Online**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Tableau Online**i programlistan .

    ![Länken Tableau Online i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Tableau Online-etablering](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Etableringsläge för Tabellau online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du domänen, administratörsanvändarnamnet, administratörslösenordet och innehållsadressen för ditt Tableau Online-konto:

   * Fyll i underdomänen i rutan **Domän** baserat på steg 6.

   * Fyll i användarnamnet **för** administratörskontot på din Clarizen-klient. Ett exempel är admin@contoso.com.

   * I rutan **Admin Password** fyller du i lösenordet för administratörskontot som motsvarar administratörsanvändarnamnet.

   * Fyll i underdomänen i rutan **Innehålls-URL** baserat på steg 6.

6. När du har loggat in på ditt administrativa konto för Tableau Online kan du hämta värdena för **domän-** och **innehålls-URL:en** från url:en till administratörssidan.

    * **Domänen** för ditt Tableau Online-konto kan kopieras från den här delen av webbadressen:

        ![Tablå onlinedomän](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * **Innehållsadressen** för ditt Tableau Online-konto kan kopieras från det här avsnittet. Det är ett värde som definieras under kontoinställningarna. I det här exemplet är värdet "contoso":

        ![Url till tablåinnehåll online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Din **domän** kan skilja sig från den som visas här.

7. När du har fyllt i rutorna som visas i steg 5 väljer du **Testa anslutning** för att se till att Azure AD kan ansluta till Tableau Online. Om anslutningen misslyckas kontrollerar du att tableau Online-kontot har administratörsbehörighet och försöker igen.

    ![Tableau online testanslutning](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. I rutan **E-post för meddelanden** anger du den person eller grupps e-postadress för att få meddelanden om etableringsfel. Markera kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-postmeddelande för Tableau Online-meddelande](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Välj **Spara**.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Tableau**.

    ![Synkronisering av användare i Tableau Online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Granska användarattributen som synkroniseras från Azure AD till Tableau Online i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Tableau Online för uppdateringsåtgärder. Om du vill spara eventuella ändringar väljer du **Spara**.

    ![Tableau Online matchande användarattribut](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till Tableau**.

    ![Synkronisering av gruppsynkronisering av tabellau online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Tableau Online i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Tableau Online för uppdateringsåtgärder. Om du vill spara eventuella ändringar väljer du **Spara**.

    ![Tabellau Online matchande gruppattribut](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Om du vill konfigurera omfångsfilter följer du instruktionerna i [självstudiekursen för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD-etableringstjänsten för Tableau Online ändrar **du etableringsstatus** till **På**i avsnittet **Inställningar** .

    ![Status för etablering av Tabellau online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definiera de användare eller grupper som du vill etablera till Tableau Online. I avsnittet **Inställningar** väljer du de värden du vill använda i **Scope**.

    ![Tableau Online-scope](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. När du är redo att etablera väljer du **Spara**.

    ![Tableau Online Spara](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än senare synkroniseringar. De inträffar ungefär var 40:e minut så länge azure AD-etableringstjänsten körs. 

Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten. Rapporten beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Tableau Online.

Information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för företagsappar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
