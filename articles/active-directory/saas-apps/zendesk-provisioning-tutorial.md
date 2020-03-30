---
title: 'Självstudiekurs: Konfigurera Zendesk för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a480119ee88521b920be88669f6d80e3754d24d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062763"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Zendesk för automatisk etablering av användare

Den här självstudien visar stegen för att utföra i Zendesk och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och grupper till Zendesk.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som är byggd ovanpå Azure AD-användarens etableringstjänst. Information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program (Software-as-a-Service) med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du har:

* En Azure AD-klientorganisation.
* En Zendesk-klient med professional-planen eller bättre aktiverad.
* Ett användarkonto i Zendesk med administratörsbehörighet.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Lägg till Zendesk från Azure Marketplace

Innan du konfigurerar Zendesk för automatisk användaretablering med Azure AD lägger du till Zendesk från Azure Marketplace i listan över hanterade SaaS-program.

Så här lägger du till Zendesk från Marketplace.

1. I [Azure-portalen](https://portal.azure.com)väljer du **Azure Active Directory**i navigeringsfönstret till vänster .

    ![Ikon för Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Zendesk** och väljer **Zendesk** från resultatpanelen. Om du vill lägga till programmet väljer du **Lägg till**.

    ![Zendesk i resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Tilldela användare till Zendesk

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bestämmer du vilka användare eller grupper i Azure AD som behöver åtkomst till Zendesk. Om du vill tilldela dessa användare eller grupper till Zendesk följer du instruktionerna i [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Viktiga tips för att tilldela användare till Zendesk

* Idag fylls Zendesk-roller automatiskt och dynamiskt i Azure-portalgränssnittet. Innan du tilldelar Zendesk-roller till användare kontrollerar du att en första synkronisering har slutförts mot Zendesk för att hämta de senaste rollerna i Zendesk-klienten.

* Vi rekommenderar att du tilldelar en enda Azure AD-användare till Zendesk för att testa din första automatiska konfiguration för användaretablering. Du kan tilldela ytterligare användare eller grupper senare efter att testerna har slutförts.

* När du tilldelar en användare till Zendesk väljer du en giltig programspecifik roll, om sådan finns, i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Konfigurera automatisk användaretablering till Zendesk 

Det här avsnittet hjälper dig att konfigurera Azure AD-etableringstjänsten. Använd den för att skapa, uppdatera och inaktivera användare eller grupper i Zendesk baserat på användar- eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för Zendesk. Följ instruktionerna i [Zendesk enda sign-on tutorial](zendesk-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurera automatisk användaretablering för Zendesk i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** > **Alla program** > **Zendesk**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Zendesk**i programlistan .

    ![Zendesk-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Zendesk Etablering](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Zendesk-etableringsläge](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du administratörsanvändarnamnet, den hemliga token och domänen för ditt Zendesk-konto. Exempel på dessa värden är:

   * Fyll i användarnamnet **för** administratörskontot på din Zendesk-klient. Ett exempel är admin@contoso.com.

   * I rutan **Hemlig token** fyller du i den hemliga token som beskrivs i steg 6.

   * Fyll **Domain** i underdomänen för din Zendesk-klient. Till exempel för ett konto med `https://my-tenant.zendesk.com`en klient-URL till , din underdomän är **min-klient .**

6. Den hemliga token för ditt Zendesk-konto finns i > **Administratörs-API-inställningar** > **Settings**. **Admin** Kontrollera att **tokenåtkomst** är inställt **på Aktiverad**.

    ![Administratörsinställningar för Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk hemlig token](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. När du har fyllt i rutorna som visas i steg 5 väljer du **Testa anslutning** för att se till att Azure AD kan ansluta till Zendesk. Om anslutningen misslyckas kontrollerar du att Ditt Zendesk-konto har administratörsbehörighet och försöker igen.

    ![Zendesk testanslutning](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. I rutan **E-post för meddelanden** anger du den person eller grupps e-postadress för att få meddelanden om etableringsfel. Markera kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-postmeddelande för Zendesk-meddelande](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Välj **Spara**.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Zendesk**.

    ![Synkronisering av Zendesk-användare](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Granska användarattributen som synkroniseras från Azure AD till Zendesk i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Zendesk för uppdateringsåtgärder. Om du vill spara eventuella ändringar väljer du **Spara**.

    ![Zendesk-matchande användarattribut](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till Zendesk**.

    ![Synkronisering av Zendesk-gruppen](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Zendesk i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Zendesk för uppdateringsåtgärder. Om du vill spara eventuella ändringar väljer du **Spara**.

    ![Zendesk-matchande gruppattribut](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Om du vill konfigurera omfångsfilter följer du instruktionerna i [självstudiekursen för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD-etableringstjänsten för Zendesk ändrar **du etableringsstatus** till **På**i avsnittet **Inställningar.**

    ![Zendesk-etableringsstatus](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definiera de användare eller grupper som du vill etablera till Zendesk. I avsnittet **Inställningar** väljer du de värden du vill använda i **Scope**.

    ![Zendesk Räckvidd](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. När du är redo att etablera väljer du **Spara**.

    ![Zendesk Spara](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än senare synkroniseringar. De inträffar ungefär var 40:e minut så länge azure AD-etableringstjänsten körs. 

Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten. Rapporten beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Zendesk.

Information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* Zendesk stöder användningen av grupper **Agent** endast för användare med agentroller. Mer information finns i [Zendesk-dokumentationen](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* När en anpassad roll tilldelas en användare eller grupp tilldelar azure AD automatisk användaretableringstjänst även **standardrollagenten**. Endast agenter kan tilldelas en anpassad roll. Mer information finns i [Zendesk API-dokumentationen](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för företagsappar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
