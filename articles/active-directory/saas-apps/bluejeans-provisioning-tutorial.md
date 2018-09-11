---
title: 'Självstudie: Konfigurera BlueJeans för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: v-ant
ms.openlocfilehash: ce27a6f78dfdeb00e1e7b2c82c928d28f1504a1d
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347530"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Självstudie: Konfigurera BlueJeans för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i BlueJeans och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till BlueJeans.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

*   En Azure AD-klient
*   En BlueJeans klient med den [mitt företag](https://www.BlueJeans.com/pricing) planera eller bättre aktiverat
*   Ett användarkonto i BlueJeans med administratörsbehörighet

> [!NOTE]
> Azure AD etablering integration förlitar sig på den [BlueJeans API](https://BlueJeans.github.io/developer), som är tillgängliga för BlueJeans team på Standard-avtalet eller bättre.

## <a name="adding-bluejeans-from-the-gallery"></a>Att lägga till BlueJeans från galleriet
Du måste lägga till BlueJeans från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar BlueJeans för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till BlueJeans från Azure AD-programgalleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, på den vänstra navigeringspanelen klickar du på den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnittet][2]
    
3. Lägg till BlueJeans, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **BlueJeans**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansAppSearch.png)

5. I resultatpanelen väljer **BlueJeans**, och klicka sedan på den **Lägg till** vill lägga till BlueJeans i din lista över SaaS-program.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansAppSearchResults.png)

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansAppCreate.png)
    
## <a name="assigning-users-to-bluejeans"></a>Tilldela användare till BlueJeans

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till BlueJeans. När du valt, kan du tilldela dessa användare och/eller grupper till BlueJeans genom att följa instruktionerna här:

*   [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Viktiga tips för att tilldela användare till BlueJeans

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats BlueJeans att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till BlueJeans, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Konfigurera automatisk användaretablering för BlueJeans

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i BlueJeans baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för BlueJeans, följa anvisningarna enligt den [BlueJeans enkel inloggning för självstudien](bluejeans-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Konfigurera automatisk användaretablering för BlueJeans i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj BlueJeans från din lista över SaaS-program.
 
    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/Bluejeans2.png)

3. Välj den **etablering** fliken.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **Admin Username**, och **adminlösenord** för BlueJeans-kontot. Exempel på dessa värden är:

    *   I den **Admin Username** fältet, fylla i användarnamnet för administratörskontot på BlueJeans-klienten. Exempel: admin@contoso.com.

    *   I den **adminlösenord** fältet, fylla det lösenord som motsvarar administratörens användarnamn.

6. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till BlueJeans. Om anslutningen misslyckas, kontrollera BlueJeans-kontot har administratörsbehörighet och försök igen.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Klicka på **Spara**.

9. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till BlueJeans**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Granska användarattribut som synkroniseras från Azure AD till BlueJeans i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i BlueJeans för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera den Azure AD-etableringstjänsten för BlueJeans, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Ange användare och/eller grupper som du vill kan etableras på BlueJeans genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. När du är redo att etablera, klickar du på **spara**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på BlueJeans.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Bluejeans tillåter inte användarnamn som överskrider 30 tecken.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
