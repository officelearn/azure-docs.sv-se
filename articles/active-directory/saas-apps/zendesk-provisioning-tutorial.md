---
title: 'Självstudier: Konfigurera Zendesk för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
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
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: f559d2c2398998ba590419758de559f21d9b65f5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114673"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Zendesk för automatisk användaretablering

Den här kursen visar hur du utför i Zendesk och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och grupper till Zendesk.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-tjänst för användaretablering. Information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns [automatisera användaretablering och avetablering för software-as-a-service (SaaS)-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du har:

* En Azure AD-klientorganisation.
* Ett Zendesk-klient med den [Enterprise](https://www.zendesk.com/product/pricing/) planera eller bättre aktiverat.
* Ett användarkonto i Zendesk med administratörsbehörighet.

> [!NOTE]
> Azure AD etablering integration förlitar sig på den [Zendesk Rest API](https://developer.zendesk.com/rest_api/docs/core/introduction). Detta API är tillgängliga för Zendesk-team på Enterprise-avtalet eller bättre.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Lägg till Zendesk från Azure Marketplace

Innan du konfigurerar Zendesk för automatisk användarförsörjning med Azure AD kan du lägga till Zendesk från Azure Marketplace till din lista över hanterade SaaS-program.

Följ dessa steg för att lägga till Zendesk från Marketplace.

1. I den [Azure-portalen](https://portal.azure.com), i navigeringsfönstret till vänster, Välj **Azure Active Directory**.

    ![Azure Active Directory-ikonen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Zendesk** och välj **Zendesk** från panelen resultatet. Om du vill lägga till programmet, Välj **Lägg till**.

    ![Zendesk i resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Tilldela användare till Zendesk

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras de användare eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, kan du bestämma vilka användare eller grupper i Azure AD behöver åtkomst till Zendesk. Om du vill tilldela dessa användare eller grupper till Zendesk, följer du anvisningarna i [tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Viktiga tips för att tilldela användare till Zendesk

* Idag, fylls Zendesk roller i Azure-portalens användargränssnitt automatiskt och dynamiskt. Innan du tilldelar Zendesk-roller till användare, se till att en inledande synkronisering har utförts mot Zendesk att hämta de senaste rollerna i din Zendesk-klient.

* Vi rekommenderar att du tilldelar en enda Azure AD-användare till Zendesk att testa din första automatisk användarförsörjning konfiguration. Du kan tilldela ytterligare användare eller grupper senare när testerna har lyckats.

* När du tilldelar en användare till Zendesk, Välj en giltig programspecifika roll om det finns i dialogrutan tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Konfigurera automatisk användaretablering till Zendesk 

Det här avsnittet vägleder dig genom stegen för att konfigurera den Azure AD-etableringstjänsten. Du kan använda den för att skapa, uppdatera och inaktivera användare eller grupper i Zendesk baserat på tilldelningar för användare eller grupp i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för Zendesk. Följ instruktionerna i den [Zendesk enkel inloggning för självstudien](zendesk-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurera automatisk användaretablering för Zendesk i Azure AD

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram** > **alla program** > **Zendesk**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Zendesk**.

    ![Zendesk-länk i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Zendesk etablering läge](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Under den **administratörsautentiseringsuppgifter** anger administratörens användarnamn, hemlig token och domänen för ditt Zendesk-konto. Exempel på dessa värden är:

   * I den **Admin Username** kryssar du i användarnamnet för administratörskontot på din Zendesk-klient. Ett exempel är admin@contoso.com.

   * I den **hemlighet Token** kryssar i hemlig token enligt beskrivningen i steg 6.

   * I den **domän** kryssar i underdomänen för din Zendesk-klient. Till exempel för ett konto med ett klient-URL för `https://my-tenant.zendesk.com`, din underdomän är **min klient**.

6. Hemlig token för ditt Zendesk-konto finns i **Admin** > **API** > **inställningar**. Se till att **Tokenåtkomst** är inställd på **aktiverad**.

    ![Zendesk administratörsinställningar](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk hemlig token](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. När du har fyllt i rutorna som visas i steg 5 väljer **Testanslutningen** för att se till att Azure AD kan ansluta till Zendesk. Om anslutningen misslyckas se till att ditt Zendesk-konto har administratörsbehörighet och försök igen.

    ![Zendesk Testa anslutning](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. I den **e-postmeddelande** anger du e-postadressen för personen eller grupp för att ta emot meddelanden etablering fel. Välj den **skicka ett e-postmeddelande när ett fel inträffar** markerar du kryssrutan.

    ![E-postmeddelande i Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Välj **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Zendesk**.

    ![Zendesk Användarsynkronisering](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Granska användarattribut som synkroniseras från Azure AD till Zendesk i den **attributmappningar** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Zendesk för uppdateringsåtgärder. Om du vill spara ändringarna, Välj **spara**.

    ![Zendesk matchande användarattribut](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper till Zendesk**.

    ![Zendesk Gruppsynkronisering](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Granska Gruppattribut som synkroniseras från Azure AD till Zendesk i den **attributmappningar** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha grupper i Zendesk för uppdateringsåtgärder. Om du vill spara ändringarna, Välj **spara**.

    ![Zendesk matchande grupp-attribut](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Om du vill konfigurera Omfångsfilter, följer du anvisningarna i den [målgrupp filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Så här aktiverar du den Azure AD-etableringstjänsten för Zendesk, i den **inställningar** ändrar **Etableringsstatus** till **på**.

    ![Zendesk-Etableringsstatus](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definiera de användare eller grupper som önskas kan etableras på Zendesk. I den **inställningar** väljer du de värden som du vill ha i **omfång**.

    ![Zendesk-omfång](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. När du är redo att etablera väljer **spara**.

    ![Zendesk spara](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än senare synkroniseringar. De inträffar ungefär var 40 minut så länge den Azure AD-etableringtjänsten körs. 

Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följa länkarna till aktivitetsrapporten för etablering. Rapporten beskriver de åtgärder som utförs av den Azure AD-etableringtjänsten på Zendesk.

Information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Zendesk stöder användning av grupper för användare med **agenten** endast roller. Mer information finns i den [Zendesk dokumentation](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* När en anpassad roll har tilldelats en användare eller grupp kan den automatiska Azure AD-användare också etableringstjänsten tilldelar standardroll **agenten**. Endast agenter kan du tilldela en anpassad roll. Mer information finns i den [Zendesk API-dokumentation](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
