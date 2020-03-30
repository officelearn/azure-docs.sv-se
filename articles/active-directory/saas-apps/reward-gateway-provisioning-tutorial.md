---
title: 'Självstudiekurs: Konfigurera Belöningsgateway för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Reward Gateway.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 928d48907e43de5e65ca5604ff878bfb83d5e95b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061019"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera belöningsgateway för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Reward Gateway och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Reward Gateway.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* En [reward gateway-klientorganisation](https://www.rewardgateway.com/).
* Ett användarkonto i Reward Gateway med administratörsbehörighet.

## <a name="assigning-users-to-reward-gateway"></a>Tilldela användare till Reward Gateway 

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Reward Gateway. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Reward Gateway genom att följa instruktionerna i [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md).


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Viktiga tips för att tilldela användare till Reward Gateway 

* Vi rekommenderar att en enda Azure AD-användare tilldelas Reward Gateway för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Reward Gateway måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-reward-gateway--for-provisioning"></a>Konfigurera belöningsgateway för etablering
Innan du konfigurerar Reward Gateway för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på Reward Gateway.

1. Logga in på [administratörskonsolen för Reward Gateway](https://rewardgateway.photoshelter.com/login/). Klicka på **Integrationer**.

    ![Administratörskonsol för belöningsgateway](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Välj **Min integration**.

    ![Administratörskonsol för belöningsgateway](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Kopiera värdena för **SCIM-URL (v2)** och **OAuth Bearer Token**. Dessa värden anges i fältet Klient-URL och Hemlig token på fliken Etablering i ditt Reward Gateway-program i Azure-portalen.

    ![Administratörskonsol för belöningsgateway](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Lägg till belöningsgateway från galleriet

Om du vill konfigurera Reward Gateway för automatisk användaretablering med Azure AD måste du lägga till Reward Gateway från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Reward Gateway från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Belöningsgateway,** väljer **Belöningsgateway** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Reward Gateway i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Konfigurera automatisk användaretablering till Reward Gateway  

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Reward Gateway baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Reward Gateway, enligt instruktionerna i [reward gateway enkel inloggningshandledning](reward-gateway-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Reward Gateway i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Reward Gateway**i programlistan .

    ![Länken Reward Gateway i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du värdena **SCIM URL (v2)** och **OAuth Bearer Token** som hämtats tidigare i **klient-URL** respektive **Hemlig token.** Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till belöningsgatewayen. Om anslutningen misslyckas kontrollerar du att ditt belöningsgatewaykonto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till belöningsgateway**.

    ![Administratörskonsol för belöningsgateway](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till Reward Gateway i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Reward Gateway för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Administratörskonsol för belöningsgateway](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för Reward Gateway ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Reward Gateway genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Reward Gateway.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

Reward Gateway stöder inte gruppetablering för närvarande.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

[Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
