---
title: 'Självstudier: Konfigurera Samanage för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Samanage.
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
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: d474d9bfd6016885eaa21afcea5d44d39c624084
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104637"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Samanage för automatisk användaretablering

Den här kursen visar hur du utför i Samanage och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och grupper till Samanage.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-tjänst för användaretablering. Information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns [automatisera användaretablering och avetablering för software-as-a-service (SaaS)-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du har:

* En Azure AD-klientorganisation.
* En [Samanage klient](https://www.samanage.com/pricing/) med professionella paketet.
* Ett användarkonto i Samanage med administratörsbehörighet.

> [!NOTE]
> Azure AD etablering integration förlitar sig på den [Samanage Rest API](https://www.samanage.com/api/). Detta API är tillgängliga för Samanage utvecklare för konton med professionella paketet.

## <a name="add-samanage-from-the-azure-marketplace"></a>Lägg till Samanage från Azure Marketplace

Innan du konfigurerar Samanage för automatisk användarförsörjning med Azure AD kan du lägga till Samanage från Azure Marketplace till din lista över hanterade SaaS-program.

Följ dessa steg för att lägga till Samanage från Marketplace.

1. I den [Azure-portalen](https://portal.azure.com), i navigeringsfönstret till vänster, Välj **Azure Active Directory**.

    ![Azure Active Directory-ikonen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Samanage** och välj **Samanage** från panelen resultatet. Om du vill lägga till programmet, Välj **Lägg till**.

    ![Samanage i resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Tilldela användare till Samanage

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras de användare eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, kan du bestämma vilka användare eller grupper i Azure AD behöver åtkomst till Samanage. Om du vill tilldela dessa användare eller grupper Samanage, följer du anvisningarna i [tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Viktiga tips för att tilldela användare till Samanage

*    Idag, fylls Samanage roller i Azure-portalens användargränssnitt automatiskt och dynamiskt. Innan du tilldelar Samanage roller till användare, se till att en inledande synkronisering slutförs mot Samanage att hämta de senaste rollerna i din Samanage-klient.

*    Vi rekommenderar att du tilldelar en enda Azure AD-användare till Samanage att testa din första automatisk användarförsörjning konfiguration. Du kan tilldela ytterligare användare och grupper senare när testerna har lyckats.

*    När du tilldelar en användare till Samanage, Välj en giltig programspecifika roll om det finns i dialogrutan tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Konfigurera automatisk användaretablering till Samanage

Det här avsnittet vägleder dig genom stegen för att konfigurera den Azure AD-etableringstjänsten. Du kan använda den för att skapa, uppdatera och inaktivera användare eller grupper i Samanage baserat på tilldelningar för användare eller grupp i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för Samanage. Följ instruktionerna i den [Samanage enkel inloggning för självstudien](samanage-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurera automatisk användaretablering för Samanage i Azure AD

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram** > **alla program** > **Samanage**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Samanage**.

    ![Länken Samanage i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Samanage etablering](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Samanage etablering läge](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. Under den **administratörsautentiseringsuppgifter** anger administratörsanvändarnamn och administratörslösenord för Samanage-kontot. Exempel på dessa värden är:

   * I den **Admin Username** kryssar du i användarnamnet för administratörskontot på Samanage-klienten. Ett exempel är admin@contoso.com.

   * I den **adminlösenord** kryssar du i lösenordet för administratörskontot som motsvarar administratörens användarnamn.

6. När du har fyllt i rutorna som visas i steg 5 väljer **Testanslutningen** för att se till att Azure AD kan ansluta till Samanage. Om anslutningen misslyckas se till att Samanage-kontot har administratörsbehörighet och försök igen.

    ![Samanage Testa anslutning](./media/samanage-provisioning-tutorial/TestConnection.png)

7. I den **e-postmeddelande** anger du e-postadressen för personen eller grupp för att ta emot meddelanden etablering fel. Välj den **skicka ett e-postmeddelande när ett fel inträffar** markerar du kryssrutan.

    ![E-postmeddelande Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Välj **Spara**.

9. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Samanage**.

    ![Samanage Användarsynkronisering](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Granska användarattribut som synkroniseras från Azure AD till Samanage i den **attributmappningar** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Samanage för uppdateringsåtgärder. Om du vill spara ändringarna, Välj **spara**.

    ![Samanage matchande användarattribut](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Aktivera gruppmappningar under den **mappningar** väljer **synkronisera Azure Active Directory-grupper till Samanage**.

    ![Samanage Gruppsynkronisering](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Ange **aktiverad** till **Ja** att synkronisera grupper. Granska Gruppattribut som synkroniseras från Azure AD till Samanage i den **attributmappningar** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Samanage för uppdateringsåtgärder. Om du vill spara ändringarna, Välj **spara**.

    ![Samanage matchande grupp-attribut](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Om du vill konfigurera Omfångsfilter, följer du anvisningarna i den [målgrupp filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Så här aktiverar du den Azure AD-etableringstjänsten för Samanage, i den **inställningar** ändrar **Etableringsstatus** till **på**.

    ![Samanage Etableringsstatus](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Definiera de användare eller grupper som önskas kan etableras på Samanage. I den **inställningar** väljer du de värden som du vill ha i **omfång**. När du väljer den **synkronisera alla användare och grupper** alternativet bör du överväga att begränsningarna enligt beskrivningen i avsnittet ”Connector begränsningar”.

    ![Samanage omfång](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. När du är redo att etablera väljer **spara**.

    ![Samanage spara](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än senare synkroniseringar. De inträffar ungefär var 40 minut så länge den Azure AD-etableringtjänsten körs. 

Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följa länkarna till aktivitetsrapporten för etablering. Rapporten beskriver de åtgärder som utförs av den Azure AD-etableringtjänsten på Samanage.

Information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

Om du väljer den **synkronisera alla användare och grupper** och konfigurera ett värde för Samanage **roller** attributet värde under den **standardvärde om null (är valfri)** box måste anges i följande format:

- {”displayName”: ”roll”}, där rollen är standardvärdet som du vill.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
