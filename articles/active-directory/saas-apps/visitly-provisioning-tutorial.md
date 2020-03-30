---
title: 'Självstudiekurs: Konfigurera Visitly för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Visitly.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 73cc1a58689db7902843f222aa4874a5e188be44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063180"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Visitly för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg du utför i Visitly och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare eller grupper till Visitly.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som bygger på azure AD-användarens etableringstjänst. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program (Software-as-a-Service) med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Visitly-klient](https://www.visitly.io/pricing/)
* Ett användarkonto i Visitly med administratörsbehörighet

## <a name="assign-users-to-visitly"></a>Tilldela användare till Visitly 

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bestämmer du vilka användare eller grupper i Azure AD som behöver åtkomst till Visitly. Tilldela sedan dessa användare eller grupper till Visitly genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Viktiga tips för att tilldela användare till Visitly 

* Vi rekommenderar att du tilldelar en enda Azure AD-användare till Visitly för att testa konfigurationen för automatisk användaretablering. Ytterligare användare eller grupper kan tilldelas senare.

* När du tilldelar en användare till Visitly måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen Standardåtkomst är undantagna från etablering.

## <a name="set-up-visitly-for-provisioning"></a>Konfigurera Visitly för etablering

Innan du konfigurerar Visitly för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering (System for Cross-Domain Identity Management) på Visitly.

1. Logga in på [Visitly](https://app.visitly.io/login). Välj **Integreringar** > **värdsynkronisering**.

    ![Värdsynkronisering](media/Visitly-provisioning-tutorial/login.png)

2. Välj avsnittet **Azure AD.**

    ![Azure AD-avsnittet](media/Visitly-provisioning-tutorial/integration.png)

3. Kopiera **API-nyckeln**. Dessa värden anges i rutan **Hemlig token** på fliken **Etablering** i visitly-programmet i Azure-portalen.

    ![API-nyckel](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Lägg till Visitly från galleriet

Om du vill konfigurera Visitly för automatisk användaretablering med Azure AD lägger du till Visitly från Azure AD-programgalleriet i listan över hanterade SaaS-program.

Så här lägger du till Visitly från Azure AD-programgalleriet.

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i det vänstra navigeringsfönstret .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Visitly,** väljer **Visitly** på resultatpanelen och väljer sedan **Lägg till** för att lägga till programmet.

    ![Visitly i resultatlistan](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Konfigurera automatisk användaretablering till Visitly 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare eller grupper i Visitly baserat på användar- eller grupptilldelningar i Azure AD.

> [!TIP]
> Om du vill aktivera SAML-baserad enkel inloggning för Visitly följer du instruktionerna i [den enstaka inloggningshandledningen visitly](Visitly-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Konfigurera automatisk användaretablering för Visitly i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** > **Alla program**.

    ![Alla program](common/enterprise-applications.png)

2. I programlistan väljer du **Visitly**.

    ![Visitly-länk i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Etableringsläge inställt på Automatiskt](common/provisioning-automatic.png)

5. Under avsnittet Administratörsautentiseringsuppgifter `https://api.visitly.io/v1/usersync/SCIM` anger du värdena och **API-nyckelvärdena** som hämtats tidigare i **klient-URL** och **Hemlig token.** Välj **Testanslutning** för att säkerställa att Azure AD kan ansluta till Visitly. Om anslutningen misslyckas kontrollerar du att Visitly-kontot har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I rutan **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel. Markera kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Visitly**.

    ![Visitly användarmappningar](media/visitly-provisioning-tutorial/usermapping.png)

9. Granska användarattributen som synkroniseras från Azure AD till Visitly i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Visitly för uppdateringsåtgärder. Välj **Spara** om du vill genomföra eventuella ändringar.

    ![Visitly-användarattribut](media/visitly-provisioning-tutorial/userattribute.png)

10. Om du vill konfigurera omfångsfilter följer du instruktionerna i [självstudiefilatkursen För att kunna konfigurera omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för Visitly ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare eller grupper som du vill etablera till Visitly genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera väljer du **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information om hur lång tid det tar för användare eller grupper att etablera finns i [Hur lång tid tar det att etablera användare?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Du kan använda avsnittet **Aktuell status** för att övervaka förloppet och följa länkar till din etableringsaktivitetsrapport, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Visitly. Mer information finns [i Kontrollera status för användaretablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Information om hur du läser Azure AD-etableringsloggarna finns i Rapportera om automatisk etablering av [användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

Visitly stöder inte hårda borttagningar. Allt är mjukt ta bort bara.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för företagsappar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
