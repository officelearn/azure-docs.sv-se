---
title: 'Självstudier: Konfigurera xMatters OnDemand för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till xMatters OnDemand.
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
ms.date: 04/19/2018
ms.author: v-ant-msft
ms.openlocfilehash: 0f1cadfb7c78c32071083d5e2efa3299ba08fb3a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-configure-xmatters-ondemand-for-automatic-user-provisioning"></a>Självstudier: Konfigurera xMatters OnDemand för automatisk användaretablering

Syftet med den här kursen är att ange vilka åtgärder som ska utföras i xMatters OnDemand och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och/eller grupper för att xMatters OnDemand.

> [!NOTE]
> Den här självstudiekursen beskriver en koppling som bygger på Etableringstjänsten Azure AD-användare. Viktig information om vad tjänsten gör så här fungerar och vanliga frågor och svar finns [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande:

*   En Azure AD-klient
*   En xMatters OnDemand-klient med den [Starter](https://www.xmatters.com/pricing) plan eller bättre aktiverad 
*   Ett användarkonto i xMatters OnDemand med administratörsbehörigheter

> [!NOTE]
> Azure AD-etablering integration förlitar sig på den [xMatters OnDemand API](https://help.xmatters.com/xmAPI), som är tillgängliga för xMatters OnDemand-team på Start-plan eller bättre.

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Att lägga till xMatters OnDemand från galleriet

Du måste lägga till xMatters OnDemand från Azure AD application gallery i listan över hanterade SaaS-program innan du konfigurerar xMatters OnDemand för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till xMatters OnDemand från Azure AD application gallery:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, i det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnitt][2]
    
3. Lägg till xMatters OnDemand, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **xMatters OnDemand**.

    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearch.png)

5. Välj i resultatpanelen **xMatters OnDemand**, och klicka sedan på den **Lägg till** för att lägga till xMatters OnDemand i listan med SaaS-program.

    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-xmatters-ondemand"></a>Tilldela användare till xMatters OnDemand

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras bara den användare och/eller grupper som har ”tilldelats” till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till xMatters OnDemand. När du valt, kan du tilldela dessa användare och/eller grupper till xMatters OnDemand genom att följa anvisningarna här:

*   [Tilldela en användare eller grupp till en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-xmatters-ondemand"></a>Viktiga tips för att tilldela användare till xMatters OnDemand

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats xMatters OnDemand för att testa automatisk konfiguration för användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare xMatters OnDemand, måste du välja någon giltig programspecifika roll (om tillgängligt) i dialogrutan tilldelning. Användare med den **standard åtkomst** roll är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-xmatters-ondemand"></a>Konfigurera automatisk användaretablering xMatters OnDemand 

Det här avsnittet hjälper dig igenom stegen för att konfigurera Azure AD etableras för att skapa, uppdatera och inaktivera användare och/eller grupper i xMatters OnDemand baserat på användare och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för xMatters OnDemand, följer du instruktionerna som anges i den [xMatters OnDemand enkel inloggning kursen](active-directory-saas-xmatters-ondemand-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-xmatters-ondemand-in-azure-ad"></a>Konfigurera automatisk användaretablering för xMatters OnDemand i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj xMatters OnDemand från listan med SaaS-program.
 
    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ApplicationInstanceSearch.png)

3. Välj den **etablering** fliken.
    
    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Under den **administratörsautentiseringsuppgifter** avsnitt, ange den **Admin Username**, **adminlösenord**, och **domän** av din xMatters OnDemand konto.

    *   I den **Admin Username** fältet, Fyll i användarnamnet för administratörskontot på din xMatters OnDemand-klient. Exempel: admin@contoso.com.

    *   I den **adminlösenord** och fylla det lösenord som motsvarar administratörsanvändarnamnet fältet.

    *   I den **domän** fältet, fylla underdomänen för din xMatters OnDemand-klient.
    Exempel: för ett konto med ett klient-URL för https://my-tenant.xmatters.com, en underdomän skulle vara **min klient**.

6. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** så Azure AD kan ansluta till xMatters OnDemand. Om anslutningen misslyckas, kontrollera din xMatters OnDemand-konto som har administratörsbehörigheter och försök igen.

    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/TestConnection.png)
    
7. I den **e-postmeddelande** anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel uppstår**.

    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/EmailNotification.png)

8. Klicka på **Spara**.

9. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare xMatters OnDemand**.

    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncUsers.png)

10. Granska användarattribut som synkroniseras från Azure AD till xMatters OnDemand i den **attributmappning** avsnitt. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i xMatters OnDemand för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna.

    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/UserAttributes.png)

11. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper som xMatters OnDemand**.

    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncGroups.png)

12. Granska Gruppattribut som synkroniseras från Azure AD till xMatters OnDemand i den **attributmappning** avsnitt. De attribut som valts som **matchande** egenskaper som används för att matcha grupper i xMatters OnDemand för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna.

    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/GroupAttributes.png)

13. Om du vill konfigurera målgrupp filter, referera till följande instruktionerna i den [Scoping filter kursen](./active-directory-saas-scoping-filters.md).

14. Om du vill aktivera Azure AD-tjänsten för xMatters OnDemand-etablering, ändra den **Status för etablering** till **på** i den **inställningar** avsnitt.

    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningStatus.png)

15. Definiera användare och/eller grupper som du vill att etablera till xMatters OnDemand genom att välja önskade värden i **omfång** i den **inställningar** avsnitt.

    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ScopeSelection.png)

16. När du är redo att etablera, klickar du på **spara**.

    ![xMatters OnDemand-etablering](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SaveProvisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfång** i den **inställningar** avsnitt. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var 40 minuter som Azure AD Etablerar-tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapport som beskriver alla åtgärder som utförs av Azure AD-tjänsten på xMatters OnDemand-etablering.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-enterprise-apps-manage-provisioning.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granska loggarna och få rapporter om etablering aktivitet](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_03.png
