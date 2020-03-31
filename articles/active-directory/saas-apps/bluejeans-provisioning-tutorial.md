---
title: 'Självstudiekurs: Konfigurera BlueJeans för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcb3fe009a6482c8e512899a952694beaed361a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059111"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera BlueJeans för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i BlueJeans och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till BlueJeans.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

* En Azure AD-klient
* En BlueJeans-hyresgäst med [planen Mitt företag](https://www.BlueJeans.com/pricing) eller bättre aktiverad
* Ett användarkonto i BlueJeans med administratörsbehörighet

> [!NOTE]
> Azure AD-etableringsintegrationen är beroende av [BlueJeans API](https://BlueJeans.github.io/developer), som är tillgängligt för BlueJeans-team på standardplanen eller bättre.

## <a name="adding-bluejeans-from-the-gallery"></a>Lägga till BlueJeans från galleriet

Innan du konfigurerar BlueJeans för automatisk användaretablering med Azure AD måste du lägga till BlueJeans från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till BlueJeans från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **BlueJeans**, väljer **BlueJeans** på resultatpanelen och väljer sedan knappen **Lägg** till för att lägga till programmet.

    ![BlueJeans i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Tilldela användare till BlueJeans

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till BlueJeans. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till BlueJeans genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Viktiga tips för att tilldela användare till BlueJeans

* Vi rekommenderar att en enda Azure AD-användare tilldelas BlueJeans för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till BlueJeans måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Konfigurera automatisk användaretablering till BlueJeans

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i BlueJeans baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enda sign-on för BlueJeans, enligt instruktionerna i [BlueJeans enda inloggningshandledning](bluejeans-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för BlueJeans i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com) och välj **Enterprise Applications**, välj **Alla program**och välj sedan **BlueJeans**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **BlueJeans**.

    ![Länken BlueJeans i listan med program](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du **administratörsanvändarnamnet**och **administratörslösenordet för** ditt BlueJeans-konto. Exempel på dessa värden är:

   * I fältet **Administratörsanvändarnamn** fyller du i användarnamnet för administratörskontot på din BlueJeans-klient. Exempel: admin@contoso.com.

   * Fyll i lösenordet som motsvarar administratörsanvändarnamnet i fältet **Admin Password.**

6. När du fyller i fälten som visas i steg 5 klickar du på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till BlueJeans. Om anslutningen misslyckas kontrollerar du att ditt BlueJeans-konto har administratörsbehörighet och försöker igen.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Klicka på **Spara**.

9. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till BlueJeans**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Granska användarattributen som synkroniseras från Azure AD till BlueJeans i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i BlueJeans för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD-etableringstjänsten för BlueJeans ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Definiera de användare och/eller grupper som du vill etablera till BlueJeans genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. När du är redo att etablera klickar du på **Spara**.

    ![BlueJeans etablering](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på BlueJeans.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* Bluejeans tillåter inte användarnamn som överstiger 30 tecken.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
