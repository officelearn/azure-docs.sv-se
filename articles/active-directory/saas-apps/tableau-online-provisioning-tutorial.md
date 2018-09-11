---
title: 'Självstudie: Konfigurera Tableau Online för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Tableau Online.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: v-wingf-msft
ms.openlocfilehash: 4f6297fa8477ff4794bee589737e047993427c06
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44345870"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Tableau Online för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Tableau Online och Azure Active Directory (AD Azure) att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Tableau Online.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

*   En Azure AD-klient
*   En [Tableau Online-klient](https://www.tableau.com/)
*   Ett användarkonto i Tableau Online med administratörsbehörighet

> [!NOTE]
> Azure AD etablering integration förlitar sig på den [Tableau Online Rest API: T](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm), som är tillgängliga för utvecklare som Tableau Online.

## <a name="adding-tableau-online-from-the-gallery"></a>Att lägga till Tableau Online från galleriet
Innan du konfigurerar Tableau Online för automatisk användaretablering med Azure AD, som du behöver lägga till Tableau Online från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Om du vill lägga till Tableau Online från Azure AD-programgalleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, på den vänstra navigeringspanelen klickar du på den **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnittet][2]

3. Lägg till Tableau Online, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Tableau Online**.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/AppSearch.png)

5. I resultatpanelen väljer **Tableau Online**, och klicka sedan på den **Lägg till** vill lägga till Tableau Online i din lista över SaaS-program.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/AppSearchResults.png)

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-tableau-online"></a>Tilldela användare till Tableau Online

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Tableau Online. När du valt, kan du tilldela dessa användare och/eller grupper till Tableau Online genom att följa instruktionerna här:

*   [Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Viktiga tips för att tilldela användare till Tableau Online

*   Vi rekommenderar att en enda Azure AD-användare är tilldelad till Tableau Online att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Tableau Online, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-tableau-online"></a>Konfigurera automatisk användaretablering till Tableau Online

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Tableau Online baserat på användare och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Tableau Online, följa anvisningarna enligt den [Tableau enkel inloggning onlinesjälvstudien](tableauonline-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Konfigurera automatisk användaretablering för Tableau Online i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj Tableau Online från din lista över SaaS-program.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/AppInstanceSearch.png)

3. Välj den **etablering** fliken.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **domän**, **Admin Username**, **adminlösenord**, och **innehåll URL: en** din Tableau Online-konto:

    *   I den **domän** fältet, fylla underdomän baserat på steg 6.

    *   I den **Admin Username** fältet, fylla i användarnamnet för administratörskontot på Clarizen-klienten. Exempel: admin@contoso.com.

    *   I den **adminlösenord** fältet, Fyll i lösenord för administratörskontot som motsvarar administratörens användarnamn.

    *   I den **Innehållswebbadress** fältet, fylla underdomän baserat på steg 6.

6. När du loggar in till ditt administratörskonto för Online Tableau värdena för **domän** och **Innehållswebbadress** kan extraheras från URL: en för sidan.

    *   Den **domän** för Tableau Online konto kan kopieras från den här delen av URL: en: ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    *   Den **Innehållswebbadress** för Tableau Online konto kan kopieras från det här avsnittet och har ett värde som definierats under installation av kontot. I det här exemplet är värdet ”contoso”: ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Din **domän** kan skilja sig från den som visas här. 


7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till Tableau Online. Om anslutningen misslyckas se till att ditt Tableau Online-konto har administratörsbehörighet och försök igen.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan **skicka ett e-postmeddelande när ett fel inträffar**.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

10. Klicka på **Spara**.

11. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Tableau**.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/UserMappings.png)

12. Granska användarattribut som ska synkroniseras från Azure AD med Tableau Online i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Tableau Online för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

13. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper som Tableau**.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

14. Granska Gruppattribut som synkroniseras från Azure AD till Tableau Online i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Tableau Online för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

15. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

16. Om du vill aktivera den Azure AD-etableringstjänsten för Tableau Online, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

17. Ange användare och/eller grupper som du vill att etablera till Tableau Online genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

18. När du är redo att etablera, klickar du på **spara**.

    ![Tableau Online etablering](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Tableau Online.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
