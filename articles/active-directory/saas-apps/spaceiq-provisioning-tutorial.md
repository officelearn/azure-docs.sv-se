---
title: 'Självstudiekurs: Konfigurera SpaceIQ för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till SpaceIQ.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 207c8214-6304-4687-afc6-61562f0041a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: e59e9d86f394104752ef966b2a9cad2b78a1bc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062788"
---
# <a name="tutorial-configure-spaceiq-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera SpaceIQ för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i SpaceIQ och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till SpaceIQ.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En SpaceIQ hyresgäst](https://spaceiq.com/)
* Ett användarkonto i SpaceIQ med administratörsbehörighet.

## <a name="assigning-users-to-spaceiq"></a>Tilldela användare till SpaceIQ

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till SpaceIQ. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till SpaceIQ genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-spaceiq"></a>Viktiga tips för att tilldela användare till SpaceIQ

* Vi rekommenderar att en enda Azure AD-användare tilldelas SpaceIQ för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till SpaceIQ måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-spaceiq-for-provisioning"></a>Ställ in SpaceIQ för etablering

1. Logga in på [spaceiq-administratörskonsolen](https://main.spaceiq.com/login/). Navigera till **Inställningar** genom att välja den på rullgardinsmenyn längst upp till höger på skärmen.

    ![SpaceIQ-administratörskonsol](media/spaceiq-provisioning-tutorial/admin.png)

2.  Välj **integreringar från tredje part**på sidan **Inställningar** .

    ![SpaceIQ Lägg till SCIM](media/spaceiq-provisioning-tutorial/thirdparty.png)

3.  Navigera till **fliken Etablering och SSO.** **Azure** Klicka på **Aktivera**.

    ![SpaceIQ-etablering och SSO](media/spaceiq-provisioning-tutorial/provisioning.png)

    ![SpaceIQ Aktivera Azure ](media/spaceiq-provisioning-tutorial/azure.png)

3.  Kopiera **SCIM-bärare token**. Det här värdet anges i fältet Hemlig token på fliken Etablering i ditt SpaceIQ-program i Azure-portalen. Klicka på **Aktivera**

    ![SpaceIQ Skapa token](media/spaceiq-provisioning-tutorial/token.png)

## <a name="add-spaceiq-from-the-gallery"></a>Lägg till SpaceIQ från galleriet

Innan du konfigurerar SpaceIQ för automatisk användaretablering med Azure AD måste du lägga till SpaceIQ från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till SpaceIQ från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **SpaceIQ**, väljer **SpaceIQ** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![SpaceIQ i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-spaceiq"></a>Konfigurera automatisk användaretablering till SpaceIQ 

Det här avsnittet guidar dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i SpaceIQ baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för SpaceIQ , enligt instruktionerna i [SpaceIQ Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra

### <a name="to-configure-automatic-user-provisioning-for-spaceiq-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för SpaceIQ i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **SpaceIQ**i programlistan .

    ![Länken SpaceIQ i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://api.spaceiq.com/scim` **klient-URL.** Ange värdet **för SCIM-autentiseringstoken** som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till SpaceIQ. Om anslutningen misslyckas kontrollerar du att ditt SpaceIQ-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till SpaceIQ**.

    ![SpaceIQ-användarmappningar](media/spaceiq-provisioning-tutorial/usermapping.png)

9. Granska användarattributen som synkroniseras från Azure AD till SpaceIQ i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i SpaceIQ för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![SpaceIQ-användarattribut](media/spaceiq-provisioning-tutorial/userattributes.png)

11. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD-etableringstjänsten för SpaceIQ ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

13. Definiera de användare och/eller grupper som du vill etablera till SpaceIQ genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

14. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på SpaceIQ.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)