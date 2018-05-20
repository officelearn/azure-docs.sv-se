---
title: 'Självstudier: Konfigurera Zendesk för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Zendesk.
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
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 87ed987b53b45ef9dacb771d8dd057432712fb16
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Zendesk för automatisk användaretablering

Syftet med den här kursen är att ange vilka åtgärder som ska utföras i Zendesk och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper som ska Zendesk. 

> [!NOTE]
> Den här självstudiekursen beskriver en koppling som bygger på Etableringstjänsten Azure AD-användare. Viktig information om vad tjänsten gör så här fungerar och vanliga frågor och svar finns [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande krav:

*   En Azure AD-klient
*   En Zendesk-klient med den [Enterprise](https://www.zendesk.com/product/pricing/) plan eller bättre aktiverad 
*   Ett användarkonto i Zendesk med administratörsbehörigheter 

> [!NOTE]
> Azure AD-etablering integration förlitar sig på den [Zendesk Rest API](https://developer.zendesk.com/rest_api/docs/core/introduction), som är tillgängliga för Zendesk-team på Enterprise-plan eller bättre.

## <a name="adding-zendesk-from-the-gallery"></a>Att lägga till Zendesk från galleriet
Du måste lägga till Zendesk från Azure AD application gallery i listan över hanterade SaaS-program innan du konfigurerar Zendesk för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till Zendesk från Azure AD application gallery:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, i det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnitt][2]
    
3. Lägg till Zendesk, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Zendesk**.

    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk6.png)

5. Välj i resultatpanelen **Zendesk**, och klicka sedan på den **Lägg till** för att lägga till Zendesk i listan med SaaS-program.

    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk7.png)

    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk20.png)

## <a name="assigning-users-to-zendesk"></a>Tilldela användare till Zendesk

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras bara den användare och/eller grupper som har ”tilldelats” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Zendesk. När du valt, kan du tilldela dessa användare och/eller grupper till Zendesk genom att följa anvisningarna här:

*   [Tilldela en användare eller grupp till en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Viktiga tips för att tilldela användare till Zendesk

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats Zendesk för att testa automatisk konfiguration för användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare Zendesk, måste du välja någon giltig programspecifika roll (om tillgängligt) i dialogrutan tilldelning. Användare med den **standard åtkomst** roll är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Konfigurera automatisk användaretablering Zendesk 

Det här avsnittet hjälper dig att konfigurera Azure AD etableras för att skapa, uppdatera och inaktivera användare och/eller grupper i Zendesk baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Zendesk, följer du instruktionerna som anges i den [Zendesk enkel inloggning kursen](active-directory-saas-zendesk-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurera automatisk användaretablering för Zendesk i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj Zendesk från listan med SaaS-program.
 
    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk3.png)

3. Välj den **etablering** fliken.
    
    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk16.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk1.png)

5. Under den **administratörsautentiseringsuppgifter** avsnitt, ange den **Admin Username**, **hemlighet Token**, och **domän** för ditt Zendesk-konto. Exempel på dessa värden är:

    *   I den **Admin Username** fältet, Fyll i användarnamnet för administratörskontot på din Zendesk-klient. Exempel: admin@contoso.com.

    *   I den **hemlighet Token** fältet, Fyll i den hemliga token enligt beskrivningen i steg 6.

    *   I den **domän** fältet, fylla underdomänen Zendesk-klient.
    Exempel: för ett konto med ett klient-URL för https://my-tenant.zendesk.com, en underdomän skulle vara **min klient**.

6. Den **hemlighet Token** för ditt Zendesk kontot finns i **Admin > API > Inställningar**. 

    ![Etablering av Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk4.png) ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk2.png)

7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** så Azure AD kan ansluta till Zendesk. Om anslutningen misslyckas, kontrollera ditt Zendesk-konto som har administratörsbehörigheter och försök igen.

    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk19.png)
    
8. I den **e-postmeddelande** anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel uppstår**.

    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk9.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare Zendesk**.

    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk10.png)

11. Granska användarattribut som ska synkroniseras från Azure AD med Zendesk i den **attributmappning** avsnitt. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i Zendesk för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna.

    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk11.png)

12. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper som ZenDesk**.

    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk12.png)

13. Granska Gruppattribut som synkroniseras från Azure AD till Zendesk i den **attributmappning** avsnitt. De attribut som valts som **matchande** egenskaper som används för att matcha grupper i Zendesk för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna.

    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk13.png)

14. Om du vill konfigurera målgrupp filter, referera till följande instruktionerna i den [Scoping filter kursen](./active-directory-saas-scoping-filters.md).

15. Om du vill aktivera Azure AD-tjänsten för Zendesk-etablering, ändra den **Status för etablering** till **på** i den **inställningar** avsnitt.

    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk14.png)

16. Ange användare och/eller grupper som du vill att etablera till Zendesk genom att välja önskade värden i **omfång** i den **inställningar** avsnitt.

    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk15.png)

17. När du är redo att etablera, klickar du på **spara**.

    ![Zendesk-etablering](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk18.png)


Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfång** i den **inställningar** avsnitt. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var 40 minuter som Azure AD Etablerar-tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapport som beskriver alla åtgärder som utförs av Azure AD-tjänsten på Zendesk-etablering.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](./active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Begränsningar för kopplingen
* Zendesk stöder användning av grupper för användare med Agent-roller. Mer information finns i [Zendesks dokumentationen](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-enterprise-apps-manage-provisioning.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granska loggarna och få rapporter om etablering aktivitet](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png
