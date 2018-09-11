---
title: 'Självstudie: Konfigurera Hörnstenen OnDemand för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Hörnstenen OnDemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 6a6cfb2cb1fd6b70be0437c8b6fa62f50e76e53b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44345421"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Hörnstenen OnDemand för automatisk användaretablering


Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Hörnstenen OnDemand och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Hörnstenen OnDemand.


> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

*   En Azure AD-klient
*   En hörnsten OnDemand-klient
*   Ett användarkonto i Hörnstenen OnDemand med administratörsbehörighet


> [!NOTE]
> Azure AD etablering integration förlitar sig på den [Hörnstenen OnDemand webbtjänsten](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf), som är tillgängliga för Hörnstenen OnDemand-team.

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Att lägga till Hörnstenen OnDemand från galleriet
Du måste lägga till Hörnstenen OnDemand från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar Hörnstenen OnDemand för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till Hörnstenen OnDemand från Azure AD-programgalleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, på den vänstra navigeringspanelen klickar du på den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnittet][2]
    
3. Lägg till Hörnstenen OnDemand, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Hörnstenen OnDemand**.

    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/AppSearch.png)

5. I resultatpanelen väljer **Hörnstenen OnDemand**, och klicka sedan på den **Lägg till** för att lägga till OnDemand Hörnstenen i listan med SaaS-program.

    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cornerstone-ondemand"></a>Tilldela användare till Hörnstenen OnDemand

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som är ”kopplade” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Hörnstenen OnDemand. När du valt, kan du tilldela dessa användare och/eller grupper till Hörnstenen OnDemand genom att följa instruktionerna här:

*   [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Viktiga tips för att tilldela användare till Hörnstenen OnDemand

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats Hörnstenen OnDemand att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Hörnstenen OnDemand, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfigurera automatisk användaretablering för Hörnstenen OnDemand

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Hörnstenen OnDemand baserat på användare och/eller grupptilldelningar i Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cornerstone-ondemand-in-azure-ad"></a>Konfigurera automatisk användaretablering för OnDemand-Hörnstenen i Azure AD:


1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj Hörnstenen OnDemand från din lista över SaaS-program.
 
    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/Successcenter2.png)

3. Välj den **etablering** fliken.

    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **Admin Username**, **adminlösenord**, och **domän** av din Hörnstenen OnDemand konto.

    *   I den **Admin Username** fältet, fylla domän\användarnamn för administratörskontot på Hörnstenen OnDemand-klienten. Exempel: contoso\admin.

    *   I den **adminlösenord** fältet, fylla det lösenord som motsvarar administratörens användarnamn.

    *   I den **domän** fältet, fylla i webbtjänstens URL Hörnstenen OnDemand-klient. Exempel: Tjänsten finns på `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`för Contoso-domänen är `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Mer information om hur du hämtar webbtjänstens URL finns i [här](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till Hörnstenen OnDemand. Om anslutningen misslyckas, kontrollera Hörnstenen OnDemand-kontot har administratörsbehörighet och försök igen.

    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Klicka på **Spara**.

9. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Hörnstenen OnDemand**.

    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Granska användarattribut som synkroniseras från Azure AD till Hörnstenen OnDemand i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Hörnstenen OnDemand för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera den Azure AD-etableringstjänsten för Hörnstenen OnDemand, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Ange användare och/eller grupper som du vill kan etableras på Hörnstenen OnDemand genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. När du är redo att etablera, klickar du på **spara**.

    ![Hörnstenen OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)


Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Hörnstenen OnDemand.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).
## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Hörnstenen OnDemand **Position** attributet måste ha ett värde som motsvarar roller på Hörnstenen OnDemand-portalen. Lista över giltiga **Position** värden kan hämtas genom att gå till **redigera användarpost > organisationsstruktur > Position** Hörnstenen OnDemand-portalen.
    ![Hörnstenen OnDemand etablering Redigera användare](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png) ![Hörnstenen OnDemand etablering Position](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png) ![Hörnstenen OnDemand etablering positioner lista](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)
    
## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
