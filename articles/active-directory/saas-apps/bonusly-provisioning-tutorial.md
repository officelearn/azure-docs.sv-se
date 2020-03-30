---
title: 'Självstudiekurs: Konfigurera bonus för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Bonusly.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7694e441a59680a9b9544d3479100c1f779964ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058963"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera bonus för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Bonusly och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Bonusly.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

* En Azure AD-klient
* En [bonusklient](https://bonus.ly/pricing)
* Ett användarkonto i Bonusly med administratörsbehörighet

> [!NOTE]
> Azure AD-etableringsintegrationen är beroende av [Bonusly Rest API](https://konghq.com/solutions/gateway/), som är tillgänglig för Bonusly-utvecklare.

## <a name="adding-bonusly-from-the-gallery"></a>Lägga till Bonusly från galleriet

Innan du konfigurerar Bonusly för automatisk användaretablering med Azure AD måste du lägga till Bonusly från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Bonusly från Azure AD-programgalleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Bonus**i sökrutan och välj **Bonus från** resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![Bonusly i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Tilldela användare till Bonusly

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har "tilldelats" till ett program i Azure AD. 

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Bonusly. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Bonusly genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Viktiga tips för att tilldela användare till Bonusly

* Vi rekommenderar att en enda Azure AD-användare tilldelas Bonusly för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Bonusly måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Konfigurera automatisk användaretablering till Bonusly

Det här avsnittet guidar dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Bonusly baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Bonusly, enligt instruktionerna i [Bonusly enda sign-on tutorial](bonus-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Bonusly i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com) och välj **Enterprise Applications**, välj **Alla program**och välj sedan **Bonus .**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Bonus**i programlistan .

    ![Länken Bonusly i listan Program](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du den **hemliga token** för ditt Bonusly-konto enligt beskrivningen i steg 6.

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. Den **hemliga token** för ditt Bonusly-konto finns i **Admin > Company > Integrations**. I avsnittet **Om du vill koda** klickar du på **API > Skapa ny API Access-token** för att skapa en ny hemlig token.

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Skriv ett namn på åtkomsttoken i den angivna textrutan på följande skärm och tryck sedan på **Skapa api-tangent**. Den nya åtkomsttoken visas i några sekunder i ett popup-fönster.

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/Token02.png)

8. När du fyller i fälten som visas i steg 5 klickar du på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Bonus. Om anslutningen misslyckas kontrollerar du att ditt Bonusly-konto har administratörsbehörighet och försök igen.

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Klicka på **Spara**.

11. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Bonus .**

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Granska användarattributen som synkroniseras från Azure AD till Bonusly i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Bonusly för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Om du vill aktivera Azure AD-etableringstjänsten för Bonusly ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Definiera de användare och/eller grupper som du vill etablera till Bonusly genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. När du är redo att etablera klickar du på **Spara**.

    ![Bonusavsättningar](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Bonusly.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
