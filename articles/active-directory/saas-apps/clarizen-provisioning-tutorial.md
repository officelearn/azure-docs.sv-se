---
title: 'Självstudier: Konfigurera Clarizen för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Clarizen.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant-msft
ms.openlocfilehash: fcb9e8b33e6e3b278b4a429772c0a3b107d8eba9
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227497"
---
# <a name="tutorial-configure-clarizen-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Clarizen för automatisk användaretablering

Syftet med den här kursen är att ange vilka åtgärder som ska utföras i Clarizen och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper som ska Clarizen.

> [!NOTE]
> Den här självstudiekursen beskriver en koppling som bygger på Etableringstjänsten Azure AD-användare. Viktig information om vad tjänsten gör så här fungerar och vanliga frågor och svar finns [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande:

*   En Azure AD-klient
*   En Clarizen klient med den [Enterprise Edition](https://www.clarizen.com/product/pricing/) plan eller bättre aktiverad 
*   Ett användarkonto i Clarizen med administratörsbehörigheter

> [!NOTE]
> Azure AD-etablering integration förlitar sig på den [Clarizen API](https://api.clarizen.com/v2.0/services/), som är tillgängliga för Clarizen team på Enterprise Edition-plan eller bättre.

## <a name="adding-clarizen-from-the-gallery"></a>Att lägga till Clarizen från galleriet
Du måste lägga till Clarizen från Azure AD application gallery i listan över hanterade SaaS-program innan du konfigurerar Clarizen för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till Clarizen från Azure AD application gallery:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, i det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnitt][2]
    
3. Lägg till Clarizen, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Clarizen**.

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/AppSearch.png)

5. Välj i resultatpanelen **Clarizen**, och klicka sedan på den **Lägg till** för att lägga till Clarizen i listan med SaaS-program.

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/AppSearchResults.png)

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-clarizen"></a>Tilldela användare till Clarizen

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras bara den användare och/eller grupper som har ”tilldelats” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Clarizen. När du valt, kan du tilldela dessa användare och/eller grupper till Clarizen genom att följa anvisningarna här:

*   [Tilldela en användare eller grupp till en enterprise-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-clarizen"></a>Viktiga tips för att tilldela användare till Clarizen

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats Clarizen för att testa automatisk konfiguration för användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare Clarizen, måste du välja någon giltig programspecifika roll (om tillgängligt) i dialogrutan tilldelning. Användare med den **standard åtkomst** roll är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-clarizen"></a>Konfigurera automatisk användaretablering Clarizen 

Det här avsnittet hjälper dig att konfigurera Azure AD etableras för att skapa, uppdatera och inaktivera användare och/eller grupper i Clarizen baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Clarizen, följer du instruktionerna som anges i den [Clarizen enkel inloggning kursen](clarizen-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-in-azure-ad"></a>Konfigurera automatisk användaretablering för Clarizen i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj Clarizen från listan med SaaS-program.
 
    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/AppInstanceSearch.png)

3. Välj den **etablering** fliken.
    
    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/ProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/ProvisioningCredentials.png)

5. Under den **administratörsautentiseringsuppgifter** avsnitt, ange den **domän**, **Admin Username**, och **adminlösenord** för din Clarizen konto. Exempel på dessa värden är:

    *   I den **Admin Username** fältet, Fyll i användarnamnet för administratörskontot på din Clarizen-klient. Exempel: admin@contoso.com.

    *   I den **adminlösenord** fylla lösenordet för administratörskontot som motsvarar administratörsanvändarnamnet och fältet.

    *   I den **domän** fältet, fylla underdomän baserat på steg 6.
    
6. Hämta den **serverLocation** kontots Clarizen baserat på anvisningarna **autentisering** av [Clarizen's Guide för Rest-API](https://success.clarizen.com/hc/en-us/articles/205711828-REST-API-Guide-Version-2). Efter att serverLocation, Använd underdomänen i URL: en som markerade nedan för att fylla i **domän** fältet.

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/ClarizenDomain.png)

7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** så Azure AD kan ansluta till Clarizen. Om anslutningen misslyckas, kontrollera kontots Clarizen har administratörsbehörigheter och försök igen.

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/TestConnection.png)
    
8. I den **e-postmeddelande** anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan **skicka ett e-postmeddelande när ett fel uppstår**.

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/NotificationEmail.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare Clarizen**.

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/UserMapping.png)

11. Granska användarattribut som synkroniseras från Azure AD till Clarizen i den **attributmappning** avsnitt. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i Clarizen för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna.

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/UserMappingAttributes.png)

12. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper som Clarizen**.

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/GroupMapping.png)

13. Granska Gruppattribut som synkroniseras från Azure AD till Clarizen i den **attributmappning** avsnitt. De attribut som valts som **matchande** egenskaper som används för att matcha grupper i Clarizen för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna.

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/GroupMappingAttributes.png)

14. Om du vill konfigurera målgrupp filter, referera till följande instruktionerna i den [Scoping filter kursen](./../active-directory-saas-scoping-filters.md).

15. Om du vill aktivera Azure AD-tjänsten för Clarizen-etablering, ändra den **Status för etablering** till **på** i den **inställningar** avsnitt.

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/ProvisioningStatus.png)

16. Ange användare och/eller grupper som du vill att etablera till Clarizen genom att välja önskade värden i **omfång** i den **inställningar** avsnitt.

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/ScopeSelection.png)

17. När du är redo att etablera, klickar du på **spara**.

    ![Clarizen etablering](./media/clarizen-provisioning-tutorial/SaveProvisioning.png)


Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfång** i den **inställningar** avsnitt. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var 40 minuter som Azure AD Etablerar-tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapport som beskriver alla åtgärder som utförs av Azure AD-tjänsten på Clarizen-etablering.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granska loggarna och få rapporter om etablering aktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/clarizen-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/clarizen-tutorial/tutorial_general_02.png
[3]: ./media/clarizen-tutorial/tutorial_general_03.png
