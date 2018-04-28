---
title: 'Självstudier: Konfigurera Replicon för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Replicon.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: v-ant
ms.openlocfilehash: c7ec58b0da3a918729733809d326e6f2e5916c9d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-configure-replicon-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Replicon för automatisk användaretablering

Syftet med den här kursen är att ange vilka åtgärder som ska utföras i Replicon och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper som ska Replicon.

> [!NOTE]
> Den här självstudiekursen beskriver en koppling som bygger på Etableringstjänsten Azure AD-användare. Viktig information om vad tjänsten gör så här fungerar och vanliga frågor och svar finns [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure AD-klient
*   En Replicon klient med den [Plus](https://www.replicon.com/time-bill-pricing/) plan eller bättre aktiverad
*   Ett användarkonto i Replicon med administratörsbehörigheter

> [!NOTE]
> Azure AD-etablering integration förlitar sig på den [Replicon API](https://www.replicon.com/help/developers), som är tillgängliga för Replicon team på plustecknet plan eller bättre.

## <a name="adding-replicon-from-the-gallery"></a>Att lägga till Replicon från galleriet
Du måste lägga till Replicon från Azure AD application gallery i listan över hanterade SaaS-program innan du konfigurerar Replicon för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till Replicon från Azure AD application gallery:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, i det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnitt][2]
    
3. Lägg till Replicon, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Replicon**.

    ![Replicon programmet Sök](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearch.png)

5. Välj i resultatpanelen **Replicon**, och klicka sedan på den **Lägg till** för att lägga till Replicon i listan med SaaS-program.

    ![Replicon sökresultat](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearchResults.png)

    ![Replicon skapa program](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppCreate.png)


## <a name="assigning-users-to-replicon"></a>Tilldela användare till Replicon

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras bara den användare och/eller grupper som har ”tilldelats” till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Replicon. När du valt, kan du tilldela dessa användare och/eller grupper till Replicon genom att följa anvisningarna här:

*   [Tilldela en användare eller grupp till en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-replicon"></a>Viktiga tips för att tilldela användare till Replicon

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats Replicon för att testa automatisk konfiguration för användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare Replicon, måste du välja någon giltig programspecifika roll (om tillgängligt) i dialogrutan tilldelning. Användare med den **standard åtkomst** roll är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-replicon"></a>Konfigurera automatisk användaretablering Replicon 

Det här avsnittet hjälper dig att konfigurera Azure AD etableras för att skapa, uppdatera och inaktivera användare och/eller grupper i Replicon baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Replicon, följer du instruktionerna som anges i den [Replicon enkel inloggning kursen](active-directory-saas-replicon-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-replicon-in-azure-ad"></a>Konfigurera automatisk användaretablering för Replicon i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj Replicon från listan med SaaS-program.

    ![Replicon etablering](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon2.png)

3. Välj den **etablering** fliken.

    ![Replicon etablering](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Replicon etablering](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon1.png)

5. Under den **administratörsautentiseringsuppgifter** avsnitt, ange den **Admin Username**, **adminlösenord**, **CompanyId**, och **domän**  för din Replicon konto. Exempel på dessa värden är:

    *   I den **Admin Username** fältet, Fyll i användarnamnet för administratörskontot på din Replicon-klient. Exempel: contosoadmin.

    *   I den **adminlösenord** och fylla det lösenord som motsvarar administratörsanvändarnamnet fältet.

    *   I den **CompanyId** fältet, fylla CompanyId för din Replicon-klient. Exempel: CompanyID baserat på Logga in nedan är Contoso.

    ![Replicon inloggning](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconLogin.png)

    *   I den **domän** fältet, Fyll i domänen, enligt beskrivningen i steg 6.
    
6. Hämta **serviceEndpointRootURL** för din Replicon klient konto baserat på stegen som anges i [Replicon hjälpen](https://www.replicon.com/help/determining-the-url-for-your-service-calls). Vid hämta URL-Adressen i **domän** är underdomänen **serviceEndpointRootURL** som är markerade. 

    ![Replicon etablering](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconEndpoint.png)

7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** så Azure AD kan ansluta till Replicon. Om anslutningen misslyckas, kontrollera kontots Replicon har administratörsbehörigheter och försök igen.

    ![Replicon etablering](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconTestConnection.png)

8. I den **e-postmeddelande** anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan **skicka ett e-postmeddelande när ett fel uppstår**.

    ![Replicon etablering](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconNotificationEmail.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare Replicon**.
    
    ![Replicon etablering](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMapping.png)

11. Granska användarattribut som synkroniseras från Azure AD till Replicon i den **attributmappning** avsnitt. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i Replicon för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna.

    ![Replicon etablering](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMappingAtrributes.png)

12. Om du vill konfigurera målgrupp filter, referera till följande instruktionerna i den [Scoping filter kursen](./active-directory-saas-scoping-filters.md).

13. Om du vill aktivera Azure AD-tjänsten för Replicon-etablering, ändra den **Status för etablering** till **på** i den **inställningar** avsnitt.

    ![Replicon etablering](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningStatus.png)

14. Ange användare och/eller grupper som du vill att etablera till Replicon genom att välja önskade värden i **omfång** i den **inställningar** avsnitt.

    ![Replicon etablering](./media/active-directory-saas-replicon-provisioning-tutorial/UserGroupSelection.png)

15. När du är redo att etablera, klickar du på **spara**.

    ![Replicon etablering](./media/active-directory-saas-replicon-provisioning-tutorial/SaveProvisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfång** i den **inställningar** avsnitt. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var 40 minuter som Azure AD Etablerar-tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapport som beskriver alla åtgärder som utförs av Azure AD-tjänsten på Replicon-etablering.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-enterprise-apps-manage-provisioning.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granska loggarna och få rapporter om etablering aktivitet](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
