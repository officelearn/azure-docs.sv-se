---
title: 'Självstudier: Konfigurera BlueJeans för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: v-ant
ms.openlocfilehash: f66576946559caf99d9d9c5cd8590b3b43900d65
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35964113"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Självstudier: Konfigurera BlueJeans för automatisk användaretablering

Syftet med den här kursen är att ange vilka åtgärder som ska utföras i BlueJeans och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper som ska BlueJeans.

> [!NOTE]
> Den här självstudiekursen beskriver en koppling som bygger på Etableringstjänsten Azure AD-användare. Viktig information om vad tjänsten gör så här fungerar och vanliga frågor och svar finns [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande:

*   En Azure AD-klient
*   En BlueJeans klient med den [mitt företag](https://www.BlueJeans.com/pricing) plan eller bättre aktiverad
*   Ett användarkonto i BlueJeans med administratörsbehörigheter

> [!NOTE]
> Azure AD-etablering integration förlitar sig på den [BlueJeans API](https://BlueJeans.github.io/developer), som är tillgängliga för BlueJeans team på en Standard-plan eller bättre.

## <a name="adding-bluejeans-from-the-gallery"></a>Att lägga till BlueJeans från galleriet
Du måste lägga till BlueJeans från Azure AD application gallery i listan över hanterade SaaS-program innan du konfigurerar BlueJeans för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till BlueJeans från Azure AD application gallery:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, i det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnitt][2]
    
3. Lägg till BlueJeans, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **BlueJeans**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansAppSearch.png)

5. Välj i resultatpanelen **BlueJeans**, och klicka sedan på den **Lägg till** för att lägga till BlueJeans i listan med SaaS-program.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansAppSearchResults.png)

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansAppCreate.png)
    
## <a name="assigning-users-to-bluejeans"></a>Tilldela användare till BlueJeans

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras bara den användare och/eller grupper som har ”tilldelats” till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till BlueJeans. När du valt, kan du tilldela dessa användare och/eller grupper till BlueJeans genom att följa anvisningarna här:

*   [Tilldela en användare eller grupp till en enterprise-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Viktiga tips för att tilldela användare till BlueJeans

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats BlueJeans för att testa automatisk konfiguration för användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare BlueJeans, måste du välja någon giltig programspecifika roll (om tillgängligt) i dialogrutan tilldelning. Användare med den **standard åtkomst** roll är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Konfigurera automatisk användaretablering BlueJeans

Det här avsnittet hjälper dig att konfigurera Azure AD etableras för att skapa, uppdatera och inaktivera användare och/eller grupper i BlueJeans baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för BlueJeans, följer du instruktionerna som anges i den [BlueJeans enkel inloggning kursen](bluejeans-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Konfigurera automatisk användaretablering för BlueJeans i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj BlueJeans från listan med SaaS-program.
 
    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/Bluejeans2.png)

3. Välj den **etablering** fliken.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Under den **administratörsautentiseringsuppgifter** avsnitt, ange den **Admin Username**, och **adminlösenord** för BlueJeans-konto. Exempel på dessa värden är:

    *   I den **Admin Username** fältet, Fyll i användarnamnet för administratörskontot på din BlueJeans-klient. Exempel: admin@contoso.com.

    *   I den **adminlösenord** och fylla det lösenord som motsvarar administratörsanvändarnamnet fältet.

6. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** så Azure AD kan ansluta till BlueJeans. Om anslutningen misslyckas, kontrollera kontots BlueJeans har administratörsbehörigheter och försök igen.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. I den **e-postmeddelande** anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel uppstår**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Klicka på **Spara**.

9. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare BlueJeans**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Granska användarattribut som synkroniseras från Azure AD till BlueJeans i den **attributmappning** avsnitt. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i BlueJeans för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Om du vill konfigurera målgrupp filter, referera till följande instruktionerna i den [Scoping filter kursen](./../active-directory-saas-scoping-filters.md).

12. Om du vill aktivera Azure AD-tjänsten för BlueJeans-etablering, ändra den **Status för etablering** till **på** i den **inställningar** avsnitt.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Ange användare och/eller grupper som du vill att etablera till BlueJeans genom att välja önskade värden i **omfång** i den **inställningar** avsnitt.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. När du är redo att etablera, klickar du på **spara**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfång** i den **inställningar** avsnitt. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var 40 minuter som Azure AD Etablerar-tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapport som beskriver alla åtgärder som utförs av Azure AD-tjänsten på BlueJeans-etablering.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](../active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Begränsningar för kopplingen

* Bluejeans tillåter inte användarnamn som överskrider 30 tecken.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granska loggarna och få rapporter om etablering aktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
