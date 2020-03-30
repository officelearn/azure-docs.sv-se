---
title: 'Självstudiekurs: Konfigurera IDEO för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till IDEO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: de4f06a3-83e9-46ce-80ee-03d706b91c81
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2019
ms.author: Zhchia
ms.openlocfilehash: f5f163109d648a4fc021b41325c6d585a5a7a3e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057608"
---
# <a name="tutorial-configure-ideo-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera IDEO för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i IDEO och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och/eller grupper till IDEO.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En IDEO-klient](https://www.shape.space/product/pricing)
* Ett användarkonto på IDEO | Form med administratörsbehörigheter.

## <a name="assign-users-to-ideo"></a>Tilldela användare till IDEO

Azure Active Directory använder ett koncept som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till IDEO. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till IDEO genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-ideo"></a>Viktiga tips för att tilldela användare till IDEO

* Vi rekommenderar att en enda Azure AD-användare tilldelas IDEO för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till IDEO måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-ideo-for-provisioning"></a>Ställ in IDEO för etablering

Innan du konfigurerar IDEO för automatisk användaretablering med Azure AD måste du hämta viss etableringsinformation från IDEO.

1. För **Secret Token** kontakta IDEO supportteam på productsupport@ideo.com. Det här värdet anges i fältet **Hemlig token** på fliken Etablering i ditt IDEO-program i Azure-portalen. 

## <a name="add-ideo-from-the-gallery"></a>Lägg till IDEO från galleriet

Om du vill konfigurera IDEO för automatisk användaretablering med Azure AD måste du lägga till IDEO från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. Ange **IDEO**i sökrutan och välj **IDEO** på resultatpanelen. 

    ![IDEO i resultatlistan](common/search-new-app.png)

5. Välj **knappen Registrera dig för IDEO** som omdirigerar dig till IDEO:s inloggningssida. 

    ![IDEO OIDC Lägg till](media/ideo-provisioning-tutorial/signup.png)

6. Eftersom IDEO är en OpenIDConnect-app väljer du att logga in på IDEO med ditt Microsoft-arbetskonto.

    ![IDEO OIDC-inloggning](media/ideo-provisioning-tutorial/login.png)

7. Efter en lyckad autentisering godkänner du samtyckesprompten för samtyckessidan. Programmet läggs sedan automatiskt till din klient och du omdirigeras till ditt IDEO-konto.

    ![IDEO OIDc Samtycke](media/ideo-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-ideo"></a>Konfigurera automatisk användaretablering till IDEO 

Det här avsnittet hjälper dig att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i IDEO baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ideo-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för IDEO i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **IDEO**i programlistan .

    ![IDEO-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://profile.ideo.com/api/scim/v2` **klient-URL.** Ange värdet som du hämtade från IDEO-supportteamet i **Hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till IDEO. Om anslutningen misslyckas kontrollerar du att ditt IDEO-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till IDEO**.

    ![IDEO-användarmappningar](media/ideo-provisioning-tutorial/usermappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till IDEO i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i IDEO för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![IDEO-användarattribut](media/ideo-provisioning-tutorial/userattributes.png)

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för IDEO ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](media/ideo-provisioning-tutorial/groupmappings.png)

12. Definiera de användare och/eller grupper som du vill etablera till IDEO genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](media/ideo-provisioning-tutorial/groupattributes.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på IDEO.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)


