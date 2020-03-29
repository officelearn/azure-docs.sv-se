---
title: 'Självstudiekurs: Konfigurera Wrike för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Wrike.
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
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 5dd4f5ac6152c22b5e2a84ecc0774672bcd5590b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064206"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Wrike för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg du utför i Wrike och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare eller grupper till Wrike.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som bygger på azure AD-användarens etableringstjänst. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program (Software-as-a-Service) med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Wrike hyresgäst](https://www.wrike.com/price/)
* Ett användarkonto i Wrike med administratörsbehörighet

## <a name="assign-users-to-wrike"></a>Tilldela användare till Wrike
Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bestämmer du vilka användare eller grupper i Azure AD som behöver åtkomst till Wrike. Tilldela sedan dessa användare eller grupper till Wrike genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Viktiga tips för att tilldela användare till Wrike

* Vi rekommenderar att du tilldelar en enda Azure AD-användare till Wrike för att testa konfigurationen för automatisk användaretablering. Ytterligare användare eller grupper kan tilldelas senare.

* När du tilldelar en användare till Wrike måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen Standardåtkomst är undantagna från etablering.

## <a name="set-up-wrike-for-provisioning"></a>Ställ in Wrike för etablering

Innan du konfigurerar Wrike för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering (System for Cross-Domain Identity Management) på Wrike.

1. Logga in på [administratörskonsolen Wrike](https://www.Wrike.com/login/). Gå till ditt klient-ID. Välj **Appar &-integreringar**.

    ![Appar & integrationer](media/Wrike-provisioning-tutorial/admin.png)

2.  Gå till **Azure AD** och välj det.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Välj SCIM. Kopiera **bas-URL:en**.

    ![Grundläggande URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Välj **API** > **Azure SCIM**.

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Ett popup-fönster öppnas. Ange samma lösenord som du skapade tidigare för att skapa ett konto.

    ![Wrike Skapa token](media/Wrike-provisioning-tutorial/password.png)

6.  Kopiera den **hemliga token**och klistra in den i Azure AD. Välj **Spara** för att slutföra etableringsinställningarna på Wrike.

    ![Permanent åtkomsttoken](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Lägg till Wrike från galleriet

Innan du konfigurerar Wrike för automatisk användaretablering med Azure AD lägger du till Wrike från Azure AD-programgalleriet i listan över hanterade SaaS-program.

Så här lägger du till Wrike från Azure AD-programgalleriet.

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i det vänstra navigeringsfönstret .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Wrike**, väljer **Wrike** på resultatpanelen och väljer sedan **Lägg till** för att lägga till programmet.

    ![Wrike i resultatlistan](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Konfigurera automatisk användaretablering till Wrike 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare eller grupper i Wrike baserat på användar- eller grupptilldelningar i Azure AD.

> [!TIP]
> Om du vill aktivera SAML-baserad enkel inloggning för Wrike följer du instruktionerna i [wrike-självstudien](wrike-tutorial.md)för enkel inloggning . Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Konfigurera automatisk användaretablering för Wrike i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** > **Alla program**.

    ![Alla program](common/enterprise-applications.png)

2. Välj **Wrike**i programlistan .

    ![Länken Wrike i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Etableringsläge inställt på Automatiskt](common/provisioning-automatic.png)

5. Under avsnittet Administratörsautentiseringsuppgifter anger du **grundläggande URL-** och **permanent åtkomsttokenvärden** som hämtats tidigare i **klient-URL** respektive **Hemlig token.** Välj **Testanslutning** för att säkerställa att Azure AD kan ansluta till Wrike. Om anslutningen misslyckas kontrollerar du att ditt Wrike-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

7. I rutan **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel. Markera kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-postmeddelande](common/provisioning-notification-email.png)

8. Välj **Spara**.

9. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Wrike**.

    ![Wrike-användarmappningar](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Granska användarattributen som synkroniseras från Azure AD till Wrike i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Wrike för uppdateringsåtgärder. Välj **Spara** om du vill genomföra eventuella ändringar.

    ![Wrike-användarattribut](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Om du vill konfigurera omfångsfilter följer du instruktionerna i [självstudiefilatkursen För att kunna konfigurera omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD-etableringstjänsten för Wrike ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

13. Definiera de användare eller grupper som du vill etablera till Wrike genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

14. När du är redo att etablera väljer du **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information om hur lång tid det tar för användare eller grupper att etablera finns i [Hur lång tid tar det att etablera användare?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Du kan använda avsnittet **Aktuell status** för att övervaka förloppet och följa länkar till din etableringsaktivitetsrapport, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Wrike. Mer information finns [i Kontrollera status för användaretablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Information om hur du läser Azure AD-etableringsloggarna finns i Rapportera om automatisk etablering av [användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för företagsappar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
