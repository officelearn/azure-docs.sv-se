---
title: 'Självstudiekurs: Konfigurera Looop för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Looop.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0efe2262-43c3-4e0c-97fa-9344385638e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: e3e25a8c27b9a5c1bc1e7673300ac8aca9377c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057455"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Looop för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Looop och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Looop.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Looop hyresgäst](https://www.looop.co/pricing/)
* Ett användarkonto på en Looop med administratörsbehörighet.

## <a name="assign-users-to-looop"></a>Tilldela användare till Looop

Azure Active Directory använder ett koncept som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Looop. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Looop genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Viktiga tips för att tilldela användare till Looop

* Vi rekommenderar att en enda Azure AD-användare tilldelas Looop för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Looop måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-looop-for-provisioning"></a>Ställ in Looop för etablering

Innan du konfigurerar Looop för automatisk användaretablering med Azure AD måste du hämta viss etableringsinformation från Looop.

1. Logga in på [din Looop Admin Console](https://app.looop.co/#/login) och välj **Konto**. Under **Kontoinställningar** väljer du **Autentisering**.

    ![Looop Lägg SCIM](media/looop-provisioning-tutorial/admin.png)

2. Generera en ny token genom att klicka på **Återställ token** under **SCIM-integrering**.

    ![Looop Lägg SCIM](media/looop-provisioning-tutorial/resettoken.png)

3. Kopiera **SCIM-slutpunkten** och **token**. Dessa värden anges i fälten **Klient-URL** och **Hemlig token** på fliken Etablering i looop-programmet i Azure-portalen. 

    ![Looop Skapa Token](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Lägg till Looop från galleriet

Om du vill konfigurera Looop för automatisk användaretablering med Azure AD måste du lägga till Looop från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Looop**, välj **Looop** i resultatpanelen. 

    ![Looop i resultatlistan](common/search-new-app.png)

5. Välj **knappen Registrera dig för Looop** som omdirigerar dig till Looops inloggningssida. 

    ![Looop OIDC Lägg till](media/looop-provisioning-tutorial/signup.png)

6. Eftersom Looop är en OpenIDConnect-app väljer du att logga in på Looop med ditt Microsoft-arbetskonto.

    ![Looop OIDC-inloggning](media/looop-provisioning-tutorial/msftlogin.png)

7. Efter en lyckad autentisering godkänner du samtyckesprompten för samtyckessidan. Ansökan kommer sedan automatiskt att läggas till din klient organisation och du kommer att omdirigeras till ditt Looop-konto.

    ![Looop OIDc Samtycke](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Konfigurera automatisk användaretablering till Looop 

Det här avsnittet guidar dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Looop baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Looop i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Looop**i programlistan .

    ![Den Looop länken i ansökan listan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://<organisation_domain>.looop.co/scim/v2` **klient-URL.** Till exempel `https://demo.looop.co/scim/v2`. Ange värdet som du hämtade och sparade tidigare från Looop i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Looop. Om anslutningen misslyckas kontrollerar du att ditt Looop-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Looop**.

    ![Looop-användarmappningar](media/looop-provisioning-tutorial/usermappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till Looop i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Looop för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Looop-användarattribut](media/looop-provisioning-tutorial/userattributes.png)

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till Meta Networks Connector**.

    ![Looop-gruppkartläggningar](media/looop-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Meta Networks Connector i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Meta Networks Connector för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Looop-gruppattribut](media/looop-provisioning-tutorial/groupattributes.png)

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för Looop ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Looop genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Looop.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)


