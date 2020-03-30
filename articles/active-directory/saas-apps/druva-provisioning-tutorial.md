---
title: 'Självstudiekurs: Konfigurera Druva för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Druva.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 3d1bb0bcbc0df98d7a884004cf96fe9810589185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058118"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Druva för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Druva och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Druva.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En Druva hyresgäst](https://www.druva.com/products/pricing-plans/).
* Ett användarkonto i Druva med administratörsbehörighet.

## <a name="assigning-users-to-druva"></a>Tilldela användare till Druva

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Druva. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Druva genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>Viktiga tips för att tilldela användare till Druva

* Vi rekommenderar att en enda Azure AD-användare tilldelas Druva för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Druva måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-druva-for-provisioning"></a>Setup Druva för etablering

Innan du konfigurerar Druva för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på Druva.

1. Logga in på [din Druva Admin Console](https://console.druva.com). Navigera till **Druva > inSync**.

    ![Druva Admin Konsol](media/druva-provisioning-tutorial/menubar.png)

2. Navigera till **Hantera** > **distributioner** > **användare**.

    ![Druva Lägg TILL SCIM](media/druva-provisioning-tutorial/manage.png)

3.  Navigera till **Inställningar**. Klicka på **Generera token**.

    ![Druva Lägg TILL SCIM](media/druva-provisioning-tutorial/settings.png)

4.  Kopiera **värdet Auth-token.** Det här värdet anges i fältet **Hemlig token** på fliken Etablering i druva-programmet i Azure-portalen.
    
    ![Druva Lägg TILL SCIM](media/druva-provisioning-tutorial/auth.png)

## <a name="add-druva-from-the-gallery"></a>Lägg till Druva från galleriet

Om du vill konfigurera Druva för automatisk användaretablering med Azure AD måste du lägga till Druva från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Druva från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Druva**, väljer **Druva** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Druva i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Konfigurera automatisk användaretablering till Druva 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Druva baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Druva , enligt instruktionerna i [Druva Single sign-on tutorial](druva-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Druva i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Druva**.

    ![Länken för Druva i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5.  Under avsnittet Administratörsautentiseringsuppgifter anger du i `https://apis.druva.com/insync/scim` **klient-URL.** Ange **värdet Auth-token** i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Druva. Om anslutningen misslyckas kontrollerar du att ditt Druva-konto har administratörsbehörighet och försök igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och väljer **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Druva**.

    ![Druva-användarmappningar](media/druva-provisioning-tutorial/usermapping.png)

9. Granska användarattributen som synkroniseras från Azure AD till Druva i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Druva för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Druva-användarattribut](media/druva-provisioning-tutorial/userattribute.png)


10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för Druva ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Druva genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Druva.

    Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).
    
## <a name="connector-limitations"></a>Begränsningar för anslutning

* Druva kräver **e-post** som ett obligatoriskt attribut. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md).
