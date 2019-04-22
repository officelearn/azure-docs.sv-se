---
title: 'Självstudier: Konfigurera Samanage för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca43b62e66e3a736aa52fdd10fe36e635daba245
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59280357"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Samanage för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Samanage och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Samanage.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

* En Azure AD-klient
* En [Samanage klient](https://www.samanage.com/pricing/) med professionella paketet
* Ett användarkonto i Samanage med administratörsbehörighet

> [!NOTE]
> Azure AD etablering integration förlitar sig på den [Samanage Rest API](https://www.samanage.com/api/), som är tillgängliga för Samanage utvecklare för konton med professionella paketet.

## <a name="adding-samanage-from-the-gallery"></a>Lägga till Samanage från galleriet

Du måste lägga till Samanage från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar Samanage för automatisk användarförsörjning med Azure AD.

**Utför följande steg för att lägga till Samanage från Azure AD-programgalleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Samanage**, väljer **Samanage** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

    ![Samanage i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-samanage"></a>Tilldela användare till Samanage

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Samanage. När du valt, kan du tilldela dessa användare och/eller grupper till Samanage genom att följa instruktionerna här:

*   [Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Viktiga tips för att tilldela användare till Samanage

*    Samanage roller fylls automatiskt och dynamiskt i Azure-portalens användargränssnitt idag. Se till att en initial synkronisering är slutfört mot Samanage att hämta de senaste rollerna i Samanage klienten innan du tilldelar Samanage roller till användare.

*    Vi rekommenderar att en enda Azure AD-användare har tilldelats Samanage att testa din första automatisk användarförsörjning konfiguration. Ytterligare användare och/eller grupper kan tilldelas senare när testerna har lyckats.

*   När du tilldelar en användare till Samanage, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Konfigurera automatisk användaretablering för Samanage

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Samanage baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Samanage, följa anvisningarna enligt den [Samanage enkel inloggning för självstudien](samanage-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurera automatisk användaretablering för Samanage i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och välj **företagsprogram**väljer **alla program**och välj sedan **Samanage**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Samanage**.

    ![Samanage-länken i programlistan](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Samanage etablering](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Samanage etablering](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **Admin Username** och **adminlösenord** för Samanage-kontot. Exempel på dessa värden är:

   * I den **Admin Username** fältet, fylla i användarnamnet för administratörskontot på Samanage-klienten. Exempel: admin@contoso.com.

   * I den **adminlösenord** fältet, Fyll i lösenord för administratörskontot som motsvarar administratörens användarnamn.

6. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till Samanage. Om anslutningen misslyckas, kontrollera Samanage-kontot har administratörsbehörighet och försök igen.

    ![Samanage etablering](./media/samanage-provisioning-tutorial/TestConnection.png)

7. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan **skicka ett e-postmeddelande när ett fel inträffar**.

    ![Samanage etablering](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Klicka på **Spara**.

9. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Samanage**.

    ![Samanage etablering](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Granska användarattribut som synkroniseras från Azure AD till Samanage i den **attributmappningar** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Samanage för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Samanage etablering](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Aktivera gruppmappningar under den **mappningar** väljer **synkronisera Azure Active Directory-grupper till Samanage**.

    ![Samanage etablering](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Ange **aktiverad** till **Ja** att synkronisera grupper. Granska Gruppattribut som synkroniseras från Azure AD till Samanage i den **attributmappningar** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Samanage för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Samanage etablering](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Om du vill aktivera den Azure AD-etableringstjänsten för Samanage, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Samanage etablering](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Ange användare och/eller grupper som du vill kan etableras på Samanage genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet. När du väljer den **synkronisera alla användare och grupper** alternativet bör du överväga att begränsningarna enligt beskrivningen i den **Connector begränsningar** nedan.

    ![Samanage etablering](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. När du är redo att etablera, klickar du på **spara**.

    ![Samanage etablering](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Samanage.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Om den **synkronisera alla användare och grupper** är markerat och ett standardvärde har konfigurerats för Samanage **roller** attributet, se till att det önskade värdet under den **standardvärde om null (är valfritt)** fältet uttrycks i formatet **{”displayName”: ”roll”}** där roll är önskat standardvärde.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
