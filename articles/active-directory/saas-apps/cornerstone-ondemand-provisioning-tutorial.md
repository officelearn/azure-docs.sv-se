---
title: 'Självstudier: Konfigurera Hörnstenen OnDemand för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Hörnstenen OnDemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85ddcf3aff7d15c946230cedb0da190bca6aeab7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127505"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Hörnstenen OnDemand för automatisk användaretablering

Den här kursen visar hur du utför i Hörnstenen OnDemand och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare eller grupper till Hörnstenen OnDemand.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-tjänst för användaretablering. Information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns [automatisera användaretablering och avetablering för software-as-a-service (SaaS)-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du har:

* En Azure AD-klientorganisation.
* En hörnsten OnDemand-klient.
* Ett användarkonto i Hörnstenen OnDemand med administratörsbehörighet.

> [!NOTE]
> Azure AD etablering integration förlitar sig på den [Hörnstenen OnDemand webbtjänsten](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Den här tjänsten är tillgänglig för Hörnstenen OnDemand-team.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Lägg till Hörnstenen OnDemand från Azure Marketplace

Innan du konfigurerar Hörnstenen OnDemand för automatisk användarförsörjning med Azure AD kan du lägga till Hörnstenen OnDemand från Marketplace till din lista över hanterade SaaS-program.

Följ dessa steg för att lägga till Hörnstenen OnDemand från Marketplace.

1. I den [Azure-portalen](https://portal.azure.com), i navigeringsfönstret till vänster, Välj **Azure Active Directory**.

    ![Azure Active Directory-ikonen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Hörnstenen OnDemand** och välj **Hörnstenen OnDemand** från panelen resultatet. Om du vill lägga till programmet, Välj **Lägg till**.

    ![Cornerstone OnDemand i resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Tilldela användare till Hörnstenen OnDemand

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras de användare eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, kan du bestämma vilka användare eller grupper i Azure AD behöver åtkomst till Hörnstenen OnDemand. Om du vill tilldela dessa användare eller grupper Hörnstenen OnDemand, följer du anvisningarna i [tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Viktiga tips för att tilldela användare till Hörnstenen OnDemand

* Vi rekommenderar att du tilldelar en enda Azure AD-användare till Hörnstenen OnDemand att testa konfigurationen för automatisk användaretablering. Du kan tilldela ytterligare användare eller grupper senare.

* När du tilldelar en användare till Hörnstenen OnDemand Välj en giltig programspecifika roll om det finns i dialogrutan tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfigurera automatisk användaretablering till Hörnstenen OnDemand

Det här avsnittet vägleder dig genom stegen för att konfigurera den Azure AD-etableringstjänsten. Du kan använda den för att skapa, uppdatera och inaktivera användare eller grupper i Hörnstenen OnDemand baserat på användar- eller grupptilldelningar i Azure AD.

Följ dessa steg om du vill konfigurera automatisk användaretablering för OnDemand-Hörnstenen i Azure AD.

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram** > **alla program** > **Hörnstenen OnDemand**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Hörnstenen OnDemand**.

    ![Länken för OnDemand-Hörnstenen i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Ange **Etableringsläge** till **automatisk**.

    ![Hörnstenen OnDemand etablering läge](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Under den **administratörsautentiseringsuppgifter** Ange administratörens användarnamn, lösenord för serveradministratören och domänen för din Hörnstenen OnDemand konto:

    * I den **Admin Username** kryssar i domänen eller användarnamnet för administratörskontot på Hörnstenen OnDemand-klienten. Ett exempel är contoso\admin.

    * I den **adminlösenord** kryssar du i det lösenord som motsvarar administratörens användarnamn.

    * I den **domän** kryssar i webbtjänstens URL Hörnstenen OnDemand-klient. Till exempel tjänsten finns på `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, och för Contoso-domänen är `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Mer information om hur du hämtar webbtjänstens URL finns i [PDF-filen](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. När du har fyllt i rutorna som visas i steg 5 väljer **Testanslutningen** för att se till att Azure AD kan ansluta till Hörnstenen OnDemand. Om anslutningen misslyckas se till att din Hörnstenen OnDemand-kontot har administratörsbehörighet och försök igen.

    ![Hörnstenen OnDemand Testa anslutning](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. I den **e-postmeddelande** anger du e-postadressen för personen eller grupp för att ta emot meddelanden etablering fel. Välj den **skicka ett e-postmeddelande när ett fel inträffar** markerar du kryssrutan.

    ![Hörnstenen OnDemand e-postmeddelandet](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Välj **Spara**.

9. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Hörnstenen OnDemand**.

    ![Hörnstenen OnDemand synkronisering](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Granska användarattribut som synkroniseras från Azure AD till Hörnstenen OnDemand i den **attributmappningar** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Hörnstenen OnDemand för uppdateringsåtgärder. Om du vill spara ändringarna, Välj **spara**.

    ![Hörnstenen OnDemand attributmappningar](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Om du vill konfigurera Omfångsfilter, följer du anvisningarna i den [målgrupp filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Så här aktiverar du den Azure AD-etableringstjänsten för Hörnstenen OnDemand i den **inställningar** ändrar **Etableringsstatus** till **på**.

    ![Hörnstenen OnDemand-Etableringsstatus](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definiera de användare eller grupper som önskas kan etableras på Hörnstenen OnDemand. I den **inställningar** väljer du de värden som du vill ha i **omfång**.

    ![Hörnstenen OnDemand omfång](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. När du är redo att etablera väljer **spara**.

    ![Hörnstenen OnDemand spara](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än senare synkroniseringar. De inträffar ungefär var 40 minut så länge den Azure AD-etableringtjänsten körs. 

Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följa länkarna till aktivitetsrapporten för etablering. Rapporten beskriver de åtgärder som utförs av den Azure AD-etableringtjänsten på Hörnstenen OnDemand.

Information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

Hörnstenen OnDemand **Position** attributet måste ha ett värde som motsvarar roller på Hörnstenen OnDemand-portalen. Att hämta en lista över giltiga **Position** värden, gå till **redigera användarpost > organisationsstruktur > Position** Hörnstenen OnDemand-portalen.

![Hörnstenen OnDemand etablering redigera användarpost](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Hörnstenen OnDemand etablering Position](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Hörnstenen OnDemand etablering listan](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
