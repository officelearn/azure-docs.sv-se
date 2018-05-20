---
title: 'Självstudier: Konfigurera hörnstenarna OnDemand för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till hörnstenarna OnDemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 203bc0934b8db15478e78cbc64e463eafdf83fa9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Självstudier: Konfigurera hörnstenarna OnDemand för automatisk användaretablering


Syftet med den här kursen är att ange vilka åtgärder som ska utföras i hörnstenarna OnDemand och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper som ska hörnstenarna OnDemand.


> [!NOTE]
> Den här självstudiekursen beskriver en koppling som bygger på Etableringstjänsten Azure AD-användare. Viktig information om vad tjänsten gör så här fungerar och vanliga frågor och svar finns [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande krav:

*   En Azure AD-klient
*   En hörnsten OnDemand-klient
*   Ett användarkonto i hörnstenarna OnDemand med administratörsbehörigheter


> [!NOTE]
> Azure AD-etablering integration förlitar sig på den [hörnstenarna OnDemand Webservice](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf), som är tillgängliga för hörnstenarna OnDemand team.

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Att lägga till hörnstenarna OnDemand från galleriet
Du måste lägga till hörnstenarna OnDemand från Azure AD application gallery i listan över hanterade SaaS-program innan du konfigurerar hörnstenarna OnDemand för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till hörnstenarna OnDemand från Azure AD application gallery:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, i det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnitt][2]
    
3. Lägg till hörnstenarna OnDemand, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **hörnstenarna OnDemand**.

    ![Hörnstenarna OnDemand-etablering](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/AppSearch.png)

5. Välj i resultatpanelen **hörnstenarna OnDemand**, och klicka sedan på den **Lägg till** för att lägga till hörnstenarna OnDemand i listan med SaaS-program.

    ![Hörnstenarna OnDemand-etablering](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![Hörnstenarna OnDemand-etablering](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cornerstone-ondemand"></a>Tilldela användare till hörnstenarna OnDemand

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras bara den användare och/eller grupper som har ”tilldelats” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till hörnstenarna OnDemand. När du valt, kan du tilldela dessa användare och/eller grupper hörnstenarna OnDemand genom att följa anvisningarna här:

*   [Tilldela en användare eller grupp till en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Viktiga tips för att tilldela användare till hörnstenarna OnDemand

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats hörnstenarna OnDemand för att testa automatisk konfiguration för användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar hörnstenarna OnDemand en användare måste du välja någon giltig programspecifika roll (om tillgängligt) i dialogrutan tilldelning. Användare med den **standard åtkomst** roll är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfigurera automatisk användaretablering hörnstenarna OnDemand

Det här avsnittet hjälper dig igenom stegen för att konfigurera Azure AD etableras för att skapa, uppdatera och inaktivera användare och/eller grupper i hörnstenarna OnDemand baserat på användare och/eller grupptilldelningar i Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cornerstone-ondemand-in-azure-ad"></a>Konfigurera automatisk användaretablering för hörnstenarna OnDemand i Azure AD:


1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj hörnstenarna OnDemand från listan med SaaS-program.
 
    ![Hörnstenarna OnDemand-etablering](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/Successcenter2.png)

3. Välj den **etablering** fliken.

    ![Hörnstenarna OnDemand-etablering](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Hörnstenarna OnDemand-etablering](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Under den **administratörsautentiseringsuppgifter** avsnitt, ange den **Admin Username**, **adminlösenord**, och **domän** av din hörnstenarna OnDemand konto.

    *   I den **Admin Username** fältet, fylla domän\användarnamn för administratörskonto på din hörnstenarna OnDemand-klient. Exempel: contoso\admin.

    *   I den **adminlösenord** och fylla det lösenord som motsvarar administratörsanvändarnamnet fältet.

    *   I den **domän** fältet, fylla webbtjänstens URL för hörnstenarna OnDemand-klienten. Exempel: Tjänsten finns på `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`för Contoso-domänen är `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Mer information om hur du hämtar webbtjänstens URL finns [här](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** så Azure AD kan ansluta till hörnstenarna OnDemand. Om anslutningen misslyckas, kontrollera din hörnstenarna OnDemand-kontot har administratörsbehörighet och försök igen.

    ![Hörnstenarna OnDemand-etablering](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. I den **e-postmeddelande** anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel uppstår**.

    ![Hörnstenarna OnDemand-etablering](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Klicka på **Spara**.

9. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare hörnstenarna OnDemand**.

    ![Hörnstenarna OnDemand-etablering](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Granska användarattribut som synkroniseras från Azure AD till OnDemand hörnstenarna i den **attributmappning** avsnitt. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i hörnstenarna OnDemand för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna.

    ![Hörnstenarna OnDemand-etablering](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Om du vill konfigurera målgrupp filter, referera till följande instruktionerna i den [Scoping filter kursen](./active-directory-saas-scoping-filters.md).

12. Aktivera Azure AD etablerar hörnstenarna OnDemand-tjänsten genom att ändra den **Status för etablering** till **på** i den **inställningar** avsnitt.

    ![Hörnstenarna OnDemand-etablering](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Ange användare och/eller grupper som du vill att etablera till hörnstenarna OnDemand genom att välja önskade värden i **omfång** i den **inställningar** avsnitt.

    ![Hörnstenarna OnDemand-etablering](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. När du är redo att etablera, klickar du på **spara**.

    ![Hörnstenarna OnDemand-etablering](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/Save.png)


Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfång** i den **inställningar** avsnitt. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var 40 minuter som Azure AD Etablerar-tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapport som beskriver alla åtgärder som utförs av Azure AD-tjänsten på hörnstenarna OnDemand-etablering.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](./active-directory-saas-provisioning-reporting.md).
## <a name="connector-limitations"></a>Begränsningar för kopplingen

* Hörnstenarna OnDemand **Position** attributet förväntar ett värde som motsvarar roller på hörnstenarna OnDemand-portalen. Listan över giltiga **Position** värden kan hämtas genom att gå till **redigera användarpost > organisationsstruktur > Position** hörnstenarna OnDemand-portalen.
    ![Etablering av hörnstenarna OnDemand Redigera användare](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/UserEdit.png) ![hörnstenarna OnDemand etablering Position](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/UserPosition.png) ![hörnstenarna OnDemand etablering positioner lista](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/PostionId.png)
    
## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-enterprise-apps-manage-provisioning.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granska loggarna och få rapporter om etablering aktivitet](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
