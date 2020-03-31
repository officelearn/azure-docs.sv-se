---
title: 'Självstudiekurs: Konfigurera Cornerstone OnDemand för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Cornerstone OnDemand.
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
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058455"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Cornerstone OnDemand för automatisk användaretablering

Den här självstudien visar stegen för att utföra i Cornerstone OnDemand och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare eller grupper till Cornerstone OnDemand.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som är byggd ovanpå Azure AD-användarens etableringstjänst. Information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program (Software-as-a-Service) med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du har:

* En Azure AD-klientorganisation.
* En hörnsten OnDemand hyresgäst.
* Ett användarkonto i Cornerstone OnDemand med administratörsbehörighet.

> [!NOTE]
> Azure AD-etableringsintegrationen är beroende av [webbtjänsten Cornerstone OnDemand](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Den här tjänsten är tillgänglig för Cornerstone OnDemand-team.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Lägg till Hörnsten OnDemand från Azure Marketplace

Innan du konfigurerar Cornerstone OnDemand för automatisk användaretablering med Azure AD lägger du till Cornerstone OnDemand från Marketplace i listan över hanterade SaaS-program.

Så här lägger du till Cornerstone OnDemand från Marketplace.

1. I [Azure-portalen](https://portal.azure.com)väljer du **Azure Active Directory**i navigeringsfönstret till vänster .

    ![Ikon för Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Cornerstone OnDemand** och väljer **Cornerstone OnDemand** från resultatpanelen. Om du vill lägga till programmet väljer du **Lägg till**.

    ![Cornerstone OnDemand i resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Tilldela användare till Cornerstone OnDemand

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bestämmer du vilka användare eller grupper i Azure AD som behöver åtkomst till Cornerstone OnDemand. Om du vill tilldela dessa användare eller grupper till Cornerstone OnDemand följer du instruktionerna i [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Viktiga tips för att tilldela användare till Cornerstone OnDemand

* Vi rekommenderar att du tilldelar en enda Azure AD-användare till Cornerstone OnDemand för att testa konfigurationen för automatisk användaretablering. Du kan tilldela ytterligare användare eller grupper senare.

* När du tilldelar en användare till Cornerstone OnDemand väljer du en giltig programspecifik roll, om sådan finns, i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfigurera automatisk användaretablering till Cornerstone OnDemand

Det här avsnittet hjälper dig att konfigurera Azure AD-etableringstjänsten. Använd den för att skapa, uppdatera och inaktivera användare eller grupper i Cornerstone OnDemand baserat på användar- eller grupptilldelningar i Azure AD.

Så här konfigurerar du automatisk användaretablering för Cornerstone OnDemand i Azure AD.

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** > **Alla program** > Hörnsten**OnDemand**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Cornerstone OnDemand**i programlistan .

    ![Länken Cornerstone OnDemand i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Hörnsten onDemand etablering](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Ange **etableringsläge** till **Automatiskt**.

    ![Etableringsläge för hörnstenen onDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du administratörsanvändarnamnet, administratörslösenordet och domänen för ditt Cornerstone OnDemands konto:

    * I rutan **Administratörsanvändarnamn** fyller du i domänen eller användarnamnet för administratörskontot på din Cornerstone OnDemand-klient. Ett exempel är contoso\admin.

    * Fyll i lösenordet som motsvarar administratörsanvändarnamnet i rutan **Admin Password.**

    * I rutan **Domän** fyller du i webbtjänst-URL:en för Cornerstone OnDemand-klienten. Tjänsten finns till exempel `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`på och för Contoso `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`är domänen . Mer information om hur du hämtar webbadressen till webbtjänsten finns i [den här pdf-filen](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. När du har fyllt i rutorna som visas i steg 5 väljer du **Testa anslutning** för att se till att Azure AD kan ansluta till Cornerstone OnDemand. Om anslutningen misslyckas kontrollerar du att ditt Cornerstone OnDemand-konto har administratörsbehörighet och försöker igen.

    ![Hörnsten onDemand testanslutning](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. I rutan **E-post för meddelanden** anger du den person eller grupps e-postadress för att få meddelanden om etableringsfel. Markera kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-post för Hörnsten onDemand-meddelande](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Välj **Spara**.

9. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Hörnstenen OnDemand**.

    ![Hörnsten onDemand synkronisering](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Granska användarattributen som synkroniseras från Azure AD till Cornerstone OnDemand i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Cornerstone OnDemand för uppdateringsåtgärder. Om du vill spara eventuella ändringar väljer du **Spara**.

    ![Hörnsten onDemand attributmappningar](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Om du vill konfigurera omfångsfilter följer du instruktionerna i [självstudiekursen för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD-etableringstjänsten för Cornerstone OnDemand ändrar **du etableringsstatus** till **På**i avsnittet **Inställningar.**

    ![Status för etablering av hörnstenar på 100 000](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definiera de användare eller grupper som du vill etablera till Cornerstone OnDemand. I avsnittet **Inställningar** väljer du de värden du vill använda i **Scope**.

    ![Hörnsten onDemand Omfattning](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. När du är redo att etablera väljer du **Spara**.

    ![Hörnsten OnDemand Spara](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än senare synkroniseringar. De inträffar ungefär var 40:e minut så länge azure AD-etableringstjänsten körs. 

Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten. Rapporten beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Cornerstone OnDemand.

Information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

Attributet Cornerstone OnDemand **Position** förväntar sig ett värde som motsvarar rollerna på Portalen Cornerstone OnDemand. Om du vill hämta en lista över giltiga **befattningsvärden** går du till **Redigera användarpost > organisationsstruktur > position** i portalen Cornerstone OnDemand.

![Hörnstenen för att etablera redigera användarpost för hörnstenar](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Hörnsten pådemand etablering position](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Positioneringslista för hörnstenen på enhantering](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för företagsappar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
