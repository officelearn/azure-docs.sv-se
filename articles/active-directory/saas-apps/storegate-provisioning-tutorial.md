---
title: 'Självstudiekurs: Konfigurera Storegate för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Storegate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 72903a36f88f9092ce1d203b557003083407320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064265"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Storegate för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Storegate och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Storegate.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Storegate-klient](https://www.storegate.com)
* Ett användarkonto på en Storegate med administratörsbehörighet.

## <a name="assign-users-to-storegate"></a>Tilldela användare till Storegate

Azure Active Directory använder ett koncept som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Storegate. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Storegate genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Viktiga tips för att tilldela användare till Storegate

* Vi rekommenderar att en enda Azure AD-användare tilldelas Storegate för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Storegate måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-storegate-for-provisioning"></a>Ställ in Storegate för etablering

Innan du konfigurerar Storegate för automatisk användaretablering med Azure AD måste du hämta viss etableringsinformation från Storegate.

1. Logga in på [Storegates administratörskonsol](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) och navigera till inställningarna genom att klicka på användarikonen i det övre högra hörnet och välj **Kontoinställningar**.

    ![Storegate Lägg till SCIM](media/storegate-provisioning-tutorial/admin.png)

2. I inställningarna navigerar du till **Inställningar för Team >** och kontrollerar att växlingsknappen är påslagen i avsnittet Enkel **inloggning.**

    ![Storegate-inställningar](media/storegate-provisioning-tutorial/team.png)

    ![Knappen Storegate-växlingsknapp](media/storegate-provisioning-tutorial/sso.png)

3. Kopiera **klient-URL:en** och **Token**. Dessa värden anges i fälten **Klient-URL** och **Hemlig token** på fliken Etablering i ditt Storegate-program i Azure-portalen. 

    ![Skapa token för Storegate](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Lägg till Storegate från galleriet

Om du vill konfigurera Storegate för automatisk användaretablering med Azure AD måste du lägga till Storegate från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. Ange **Storegate**i sökrutan och välj **Storegate** på resultatpanelen. 

    ![Storegate i resultatlistan](common/search-new-app.png)

5. Välj knappen **Registrera dig för Storegate** som omdirigerar dig till Storegates inloggningssida. 

    ![Storegate OIDC Lägg till](media/storegate-provisioning-tutorial/signup.png)

6.  Logga in på [Storegates administratörskonsol](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) och navigera till inställningarna genom att klicka på användarikonen i det övre högra hörnet och välj **Kontoinställningar**.

    ![Storegate-inloggning](media/storegate-provisioning-tutorial/admin.png)

7. Inom inställningar navigera till **Team > Inställningar** och klicka på växla växla i avsnittet Enkel inloggning, kommer detta att starta medgivande-flödet. Klicka på **Aktivera**.

    ![Storegate-teamet](media/storegate-provisioning-tutorial/team.png)

    ![Storegate sso](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate aktivera](media/storegate-provisioning-tutorial/activate.png)

8. Eftersom Storegate är en OpenIDConnect-app väljer du att logga in på Storegate med ditt Microsoft-arbetskonto.

    ![Storegate OIDC-inloggning](media/storegate-provisioning-tutorial/login.png)

9. Efter en lyckad autentisering godkänner du samtyckesprompten för samtyckessidan. Programmet läggs sedan automatiskt till din klient och du omdirigeras till ditt Storegate-konto.

    ![Storegate OIDc Samtycke](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Konfigurera automatisk användaretablering till Storegate 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Storegate baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!NOTE]
> Mer information om Storegates SCIM-slutpunkt finns [i detta](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/).

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Storegate i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Storegate**i programlistan .

    ![Länken Storegate i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://dialpad.com/scim` **klient-URL.** Ange värdet som du hämtade och sparade tidigare från Storegate i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Storegate. Om anslutningen misslyckas kontrollerar du att ditt Storegate-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Storegate**.

    ![Storegate-användarmappningar](media/storegate-provisioning-tutorial/usermappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till Storegate i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Storegate för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Storegate-användarattribut](media/storegate-provisioning-tutorial/userattributes.png)

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för Storegate ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Storegate genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Storegate.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
