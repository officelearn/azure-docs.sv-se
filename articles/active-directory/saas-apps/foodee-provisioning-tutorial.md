---
title: 'Självstudiekurs: Konfigurera Foodee för automatisk användaretablering med hjälp av Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Foodee.
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
ms.openlocfilehash: 2195056ec66550063aba5ce5e2b977b51a6dc5e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057836"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Foodee för automatisk etablering av användare

Den här artikeln visar hur du konfigurerar Azure Active Directory (Azure AD) i Foodee och Azure AD för att automatiskt etablera eller avetablera användare eller grupper till Foodee.

> [!NOTE]
> I artikeln beskrivs en anslutningsapp som är byggd ovanpå azure AD-tjänsten för användaretablering. Mer information om vad den här tjänsten gör och hur den fungerar och för att få svar på vanliga frågor finns i [Automatisera användaretablering och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i förhandsgranskning. Mer information om Azure-funktionen för användning för förhandsversionsfunktioner finns i [kompletterande användningsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Den här självstudien förutsätter att du har uppfyllt följande förutsättningar:

* En Azure AD-klient
* [En Foodee hyresgäst](https://www.food.ee/about/)
* Ett användarkonto i Foodee med administratörsbehörighet

## <a name="assign-users-to-foodee"></a>Tilldela användare till Foodee 

Azure AD använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare eller grupper i Azure AD som behöver åtkomst till Foodee. När du har gjort den här bestämningen kan du tilldela dessa användare eller grupper till Foodee genom att följa instruktionerna i [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-foodee"></a>Viktiga tips för att tilldela användare till Foodee 

När du tilldelar användare bör du tänka på följande:

* Vi rekommenderar att du bara tilldelar en enda Azure AD-användare till Foodee för att testa konfigurationen av automatisk användaretablering. Du kan tilldela ytterligare användare eller grupper senare.

* När du tilldelar en användare till Foodee väljer du en giltig programspecifik roll, om den är tillgänglig, i **fönstret Tilldelning.** Användare som har rollen *Standardåtkomst* är undantagna från etablering.

## <a name="set-up-foodee-for-provisioning"></a>Ställ in Foodee för etablering

Innan du konfigurerar Foodee för automatisk användaretablering med hjälp av Azure AD måste du aktivera SCIM-etablering (System for Cross-Domain Identity Management) i Foodee.

1. Logga in på [Foodee](https://www.food.ee/login/)och välj sedan ditt klient-ID.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. Under **Enterprise portal**väljer du Enkel **inloggning**.

    ![Menyn Foodee Enterprise Portal till vänsterruta](media/Foodee-provisioning-tutorial/scim.png)

1. Kopiera värdet i rutan **API Token** för senare användning. Du anger den i rutan **Hemlig token** på fliken **Etablering** i ditt Foodee-program i Azure-portalen.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>Lägg till Foodee från galleriet

Om du vill konfigurera Foodee för automatisk användaretablering med Hjälp av Azure AD måste du lägga till Foodee från Azure AD-programgalleriet i listan över hanterade SaaS-program.

Så här lägger du till Foodee från Azure AD-programgalleriet:

1. I den vänstra rutan i [Azure-portalen](https://portal.azure.com) väljer du **Azure Active Directory**.

    ![Kommandot Azure Active Directory](common/select-azuread.png)

1. Välj **Företagsprogram** > **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

1. I sökrutan anger du **Foodee**, väljer **Foodee** i resultatfönstret och väljer sedan **Lägg** till för att lägga till programmet.

    ![Foodee i resultatlistan](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Konfigurera automatisk användaretablering till Foodee 

I det här avsnittet konfigurerar du Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare eller grupper i Foodee baserat på användar- eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för Foodee genom att följa instruktionerna i [den ensidiga självstudien för enkel inloggning i Foodee](Foodee-tutorial.md). Du kan konfigurera enkel inloggning oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

Konfigurera automatisk användaretablering för Foodee i Azure AD genom att göra följande:

1. Välj **Enterprise Applications** > **Alla program**i [Azure-portalen](https://portal.azure.com).

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Välj **Foodee**i listan **Program** .

    ![Länken Foodee i listan Applikationer](common/all-applications.png)

1. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

1. Välj **Automatisk**i listrutan **Etableringsläge** .

    ![Fliken Etablering](common/provisioning-automatic.png)

1. Gör följande under **Administratörsautentiseringsuppgifter:**

   a. I rutan **Klient-URL** anger du värdet **\/https: /concierge.food.ee/scim/v2** som du hämtade tidigare.

   b. I rutan **Hemlig token** anger du **api-tokenvärdet** som du hämtade tidigare.
   
   c. Om du vill vara säkra på att Azure AD kan ansluta till Foodee väljer du **Testa anslutning**. Om anslutningen misslyckas kontrollerar du att Foodee-kontot har administratörsbehörighet och försöker sedan igen.

    ![Länken Testanslutning](common/provisioning-testconnection-tenanturltoken.png)

1. I rutan **E-post för avisering** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar sedan kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![Textrutan E-post för meddelanden](common/provisioning-notification-email.png)

1. Välj **Spara**.

1. Under **Mappningar**väljer du **Synkronisera Azure Active Directory-användare till Foodee**.

    ![Foodee användarkartor](media/Foodee-provisioning-tutorial/usermapping.png)

1. Granska användarattribut som synkroniseras från Azure AD till Foodee under **Attributmappningar.** De attribut som väljs som **matchande** egenskaper används för att matcha *användarkontona* i Foodee för uppdateringsåtgärder. 

    ![Foodee användarkartor](media/Foodee-provisioning-tutorial/userattribute.png)

1. Om du vill genomföra ändringarna väljer du **Spara**.
1. Under **Mappningar**väljer du **Synkronisera Azure Active Directory-grupper mot Foodee**.

    ![Foodee användarkartor](media/Foodee-provisioning-tutorial/groupmapping.png)

1. Granska användarattribut som synkroniseras från Azure AD till Foodee under **Attributmappningar.** Attributen som väljs som **matchande** egenskaper används för att matcha *gruppkontona* i Foodee för uppdateringsåtgärder.

    ![Foodee användarkartor](media/Foodee-provisioning-tutorial/groupattribute.png)

1. Om du vill genomföra ändringarna väljer du **Spara**.
1. Konfigurera omfångsfiltren. Mer information om hur du gör i instruktionerna i [självstudiefilatkursen För att visa omfiltning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Om du vill aktivera Azure AD-etableringstjänsten för Foodee ändrar du **etableringsstatusen** till **På**. **Settings**

    ![Växeln etableringsstatus](common/provisioning-toggle-on.png)

1. Under **Inställningar**i listrutan **Scope** definierar du de användare eller grupper som du vill etablera till Foodee.

    ![Listrutan Etableringsscope](common/provisioning-scope.png)

1. När du är redo att etablera väljer du **Spara**.

    ![Knappen Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Föregående åtgärd startar den första synkroniseringen av de användare eller grupper som du har definierat i listrutan **Scope.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information finns i [Hur lång tid tar det att etablera användare?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Du kan använda avsnittet **Aktuell status** för att övervaka förloppet och följa länkar till din etableringsaktivitetsrapport. Rapporten beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Foodee. Mer information finns [i Kontrollera status för användaretablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Information om hur du läser Azure AD-etableringsloggarna finns i Rapportera om automatisk etablering av [användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för företagsappar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
