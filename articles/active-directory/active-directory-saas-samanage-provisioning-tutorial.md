---
title: "Självstudier: Konfigurera Samanage för automatisk användaretablering med Azure Active Directory | Microsoft Docs"
description: "Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Samanage."
services: active-directory
documentationcenter: 
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
ms.openlocfilehash: 8f11beff2c78386f4c3e8130c8e5d72465b8fe87
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Samanage för automatisk användaretablering

Syftet med den här kursen är att ange vilka åtgärder som ska utföras i Samanage och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper som ska Samanage.

> [!NOTE]
> Den här självstudiekursen beskriver en koppling som bygger på Etableringstjänsten Azure AD-användare. Viktig information om vad tjänsten gör så här fungerar och vanliga frågor och svar finns [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande:

*   En Azure Active Directory-klient
*   En Samanage klient med den [Professional](https://www.samanage.com/pricing/) plan eller bättre aktiverad 
*   Ett användarkonto i Samanage med administratörsbehörigheter 

> [!NOTE]
> Azure AD-etablering integration förlitar sig på den [Samanage REST API](https://www.samanage.com/api/), som är tillgängliga för Samanage team Professional planen eller bättre.

## <a name="adding-samanage-from-the-gallery"></a>Att lägga till Samanage från galleriet
Du måste lägga till Samanage från Azure AD application gallery i listan över hanterade SaaS-program innan du konfigurerar Samanage för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till Samanage från Azure AD application gallery:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, i det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnitt][2]
    
3. Lägg till Samanage, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Samanage**.

    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage4.png)

5. Välj i resultatpanelen **Samanage**, och klicka sedan på den **Lägg till** för att lägga till Samanage i listan med SaaS-program.

    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage6.png)

    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage5.png)

## <a name="assigning-users-to-samanage"></a>Tilldela användare till Samanage

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras bara den användare och/eller grupper som har ”tilldelats” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Samanage. När du valt, kan du tilldela dessa användare och/eller grupper till Samanage genom att följa anvisningarna här:

*   [Tilldela en användare eller grupp till en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Viktiga tips för att tilldela användare till Samanage

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats Samanage för att testa automatisk konfiguration för användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare Samanage, måste du välja någon giltig programspecifika roll (om tillgängligt) i dialogrutan tilldelning. Användare med den **standard åtkomst** roll är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Konfigurera automatisk användaretablering Samanage

Det här avsnittet hjälper dig att konfigurera Azure AD etableras för att skapa, uppdatera och inaktivera användare och/eller grupper i Samanage baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Samanage, följer du instruktionerna som anges i den [Samanage enkel inloggning kursen](active-directory-saas-samanage-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner komplettera varandra. Mer information finns i [Samanage enkel inloggning kursen](active-directory-saas-samanage-tutorial.md).

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurera automatisk användaretablering för Samanage i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj Samanage från listan med SaaS-program.
 
    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage7.png)

3. Välj den **etablering** fliken.
    
    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage8.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage9.png)

5. Under den **administratörsautentiseringsuppgifter** avsnitt, ange den **Admin Username**, och **adminlösenord** för din Samanages konto. Exempel på dessa värden är:

    *   I den **Admin Username** fältet, Fyll i användarnamnet för administratörskontot på din Samanage-klient. Exempel: admin@contoso.com.

    *   I den **adminlösenord** fältet, fylla det lösenord som motsvarar administratörsanvändarnamnet angett tidigare.

6. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** så Azure AD kan ansluta till Samanage. Om anslutningen misslyckas, kontrollera kontots Samanage har administratörsbehörigheter och försök igen.

    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage10.png)

7. I den **e-postmeddelande** anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan **skicka ett e-postmeddelande när ett fel uppstår**.

    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage11.png)

8. Klicka på **Spara**.

9. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare Samanage**.

    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage12.png)

10. Granska användarattribut som synkroniseras från Azure AD till Samanage i den **attributmappning** avsnitt. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i Samanage för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna.

    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage13.png)

12. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper som Samanage**.

    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage14.png)

13. Granska Gruppattribut som synkroniseras från Azure AD till Samanage i den **attributmappning** avsnitt. De attribut som valts som **matchande** egenskaper som används för att matcha grupper i Samanage för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna.

    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage15.png)

14. Om du vill konfigurera målgrupp filter, referera till följande instruktionerna i den [Scoping filter kursen](./active-directory-saas-scoping-filters.md).

15. Om du vill aktivera Azure AD-tjänsten för Samanage-etablering, ändra den **Status för etablering** till **på** i den **inställningar** avsnitt.

    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage16.png)

16. Ange användare och/eller grupper som du vill att etablera till Samanage genom att välja önskade värden i **omfång** i den **inställningar** avsnitt.

    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage17.png)

17. När du är redo att etablera, klickar du på **spara**.

    ![Samanage etablering](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage18.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfång** i den **inställningar** avsnitt. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var 40 minuter som Azure AD Etablerar-tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapport som beskriver alla åtgärder som utförs av Azure AD-tjänsten på Samanage-etablering.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-enterprise-apps-manage-provisioning.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granska loggarna och få rapporter om etablering aktivitet](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png
