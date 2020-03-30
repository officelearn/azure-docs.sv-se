---
title: 'Självstudiekurs: Konfigurera prioritetsmatris för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Prioritetsmatris.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a4598a99-3c98-4c14-86c2-95cc562e2439
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: 80ffaba6713027d216958e0be2cd4ae35a8d2d70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063483"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera prioritetsmatris för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Prioritetsmatris och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Prioritetsmatris.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En prioriterad matrisklient](https://appfluence.com/pricing/)
* Ett användarkonto på en prioritetsmatris med administratörsbehörighet.

## <a name="assign-users-to-priority-matrix"></a>Tilldela användare till Prioritetsmatris

Azure Active Directory använder ett koncept som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Prioritetsmatris. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Prioritetsmatris genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Viktiga tips för att tilldela användare till Prioritetsmatris

* Vi rekommenderar att en enda Azure AD-användare tilldelas Prioritetsmatris för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Prioritetsmatris måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-priority-matrix-for-provisioning"></a>Ställ in prioritetsmatris för etablering

Innan du konfigurerar Prioritetsmatris för automatisk användaretablering med Azure AD måste du hämta viss etableringsinformation från Prioritetsmatris.

1. Logga in på [administratörskonsolen Prioritet Matrix](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Klicka på **Oauth-inloggningstoken** för Prioritetsmatris

    ![Prioriterad matris Lägg till SCIM](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Klicka på knappen **HÄMTA NY TOKEN.** Kopiera **tokensträngen**. Det här värdet anges i fältet **Hemlig token** på fliken Etablering i ditt Prioritetsmatrisprogram i Azure-portalen. 

    ![Skapa token för prioritetsmatris](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Lägg till prioritetsmatris från galleriet

Om du vill konfigurera Prioritetsmatris för automatisk användaretablering med Azure AD måste du lägga till Prioritetsmatris från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Prioritetsmatris**och väljer **Prioritetsmatris** på resultatpanelen. 

    ![Prioritetsmatris i resultatlistan](common/search-new-app.png)

5. Välj knappen **Registrera dig för Prioritetsmatris** som omdirigerar dig till Prioritetsmatris inloggningssida. 

    ![Oidc-tillägg för prioritetsmatris](media/priority-matrix-provisioning-tutorial/signup.png)

6. Eftersom Prioritetsmatris är en OpenIDConnect-app väljer du att logga in på Prioritetsmatris med ditt Microsoft-arbetskonto.

    ![Prioriterad MatrisOIDC-inloggning](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Efter en lyckad autentisering godkänner du samtyckesprompten för samtyckessidan. Programmet läggs sedan automatiskt till din klient och du omdirigeras till ditt Prioritetsmatriskonto.

    ![Prioriterat OIDc-samtycke](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Konfigurera automatisk användaretablering till Prioritetsmatris 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Prioritetsmatris baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!NOTE]
> Mer information om Prioritetsmatris SCIM-slutpunkt finns i [Användaretablering och Prioritetsmatris](https://appfluence.com/help/article/user-provisioning/).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Prioritetsmatris i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Prioritetsmatris**i programlistan .

    ![Länken Prioritetsmatris i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://sync.appfluence.com/scim/v2/` **klient-URL.** Ange värdet som du hämtade och sparade tidigare från Prioritetsmatris i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Prioritetsmatris. Om anslutningen misslyckas kontrollerar du att ditt Prioritetsmatriskonto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till prioritetsmatris**.

    ![Användarmappningar för prioritetsmatris](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till Prioritetsmatris i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Prioritetsmatris för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Användarattribut för prioritetsmatris](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för Prioritetsmatris ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Prioritetsmatris genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Prioritetsmatris.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)


