---
title: 'Självstudie: Konfigurera G Suite för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969a2fb5444ae8ece2aa302c04a5bbb85dcca917
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057744"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Självstudie: Konfigurera G Suite för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som utförs i G Suite och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper i G Suite.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> G Suite Connector uppdaterades nyligen den 2019 oktober. Ändringar som görs i G Suite Connector inkluderar:
> - Stöd har lagts till för ytterligare användar-och Gruppattribut i G Suite. 
> - Uppdaterade G Suite-målattribut för att matcha vad som definieras [här](https://developers.google.com/admin-sdk/directory).
> - Uppdaterade mappningar av standardattribut.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med G Suite, behöver du följande objekt:

- En Azure AD-klient
- [En G Suite-klient](https://gsuite.google.com/pricing.html)
- Ett användar konto i en G-Svit med administratörs behörighet.

## <a name="assign-users-to-g-suite"></a>Tilldela användare G Suite

Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver ha till gång till G Suite. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till G Suite genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Viktiga tips för att tilldela användare G Suite

* Vi rekommenderar att en enda Azure AD-användare tilldelas G Suite för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till G Suite måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-g-suite-for-provisioning"></a>Konfigurera G Suite för etablering

Innan du konfigurerar G Suite för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på G Suite.

1. Logga in på administratörs [konsolen för G Suite](https://admin.google.com/) med ditt administratörs konto och välj sedan **säkerhet**. Om du inte ser länken kan den vara dold under menyn **fler kontroller** längst ned på skärmen.

    ![Välj säkerhet.][10]

1. På sidan **säkerhet** väljer du **API-referens**.

    ![Välj API-referens.][15]

1. Välj **Aktivera API-åtkomst**.

    ![Välj API-referens.][16]

   > [!IMPORTANT]
   > För varje användare som du tänker etablera till G Suite **måste** deras användar namn i Azure AD vara knutet till en anpassad domän. Till exempel godkänns inte användar namn som liknar bob@contoso.onmicrosoft.com av G Suite. Å andra sidan godkänns bob@contoso.com. Du kan ändra en befintlig användares domän genom att följa instruktionerna [här](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

1. När du har lagt till och verifierat önskade anpassade domäner med Azure AD måste du verifiera dem igen med G Suite. Se följande steg för att verifiera domäner i G Suite:

    a. I [Administratörs konsolen för G Suite](https://admin.google.com/)väljer du **domäner**.

    ![Välj domäner][20]

    b. Välj **Lägg till en domän eller ett domän Ali Aset**.

    ![Lägg till en ny domän][21]

    c. Välj **Lägg till en annan domän**och skriv sedan namnet på den domän som du vill lägga till.

    ![Ange ditt domän namn][22]

    d. Välj **Fortsätt och verifiera domän ägarskap**. Följ sedan stegen för att kontrol lera att du äger domän namnet. Omfattande instruktioner för hur du verifierar din domän med Google finns i [Verifiera din webbplats ägarskap](https://support.google.com/webmasters/answer/35179).

    e. Upprepa föregående steg för eventuella ytterligare domäner som du vill lägga till i G Suite.

1. Bestäm sedan vilket administratörs konto som du vill använda för att hantera användar etablering i G Suite. Gå till **Administratörs roller**.

    ![Välj Google Apps][26]

1. Redigera **behörigheterna** för den rollen för **Administratörs rollen** för kontot. Se till att aktivera alla **Administratörs-API-privilegier** så att det här kontot kan användas för etablering.

    ![Välj Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>Lägg till G Suite från galleriet

Om du vill konfigurera G Suite för automatisk användar etablering med Azure AD måste du lägga till G Suite från Azure AD-programgalleriet i listan över hanterade SaaS-program. 

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

1. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

1. I rutan Sök anger du **g Suite**, väljer **g Suite** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![G Suite i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Konfigurera automatisk användar etablering i G Suite 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i G Suite utifrån användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för G Suite genom att följa anvisningarna i [självstudien g Suite enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

> [!NOTE]
> Om du vill veta mer om G Suites katalog-API-slutpunkt läser du [katalog-API](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Konfigurera automatisk användar etablering för G Suite i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

1. I listan program väljer du **G Suite**.

    ![G Suite-länken i programlistan](common/all-applications.png)

1. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

1. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

1. Under avsnittet **admin credentials** väljer du **auktorisera**. Den öppnar dialog rutan Google Authorization i ett nytt webbläsarfönster.

    ![G Suite-auktorisering](media/google-apps-provisioning-tutorial/authorize.png)

1. Bekräfta att du vill ge Azure AD-behörighet för att göra ändringar i G Suite-klienten. Välj **Acceptera**.

    ![Bekräfta behörigheter.][28]

1. I Azure Portal väljer du **Testa anslutning** för att se till att Azure AD kan ansluta till din app. Om anslutningen Miss lyckas kontrollerar du att ditt G Suite-konto har team administratörs behörighet. Försök sedan att **godkänna** steget igen.

1. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

1. Klicka på **Save** (Spara).

1. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till G Suite**.

    ![G användar mappningar för Suite](media/google-apps-provisioning-tutorial/usermappings.png)

1. Granska de användarattribut som synkroniseras från Azure AD till G Suite i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i G Suite för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![G Suite-användarattribut](media/google-apps-provisioning-tutorial/userattributes.png)

1. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till G Suite**.

    ![G grupp mappningar för Suite](media/google-apps-provisioning-tutorial/groupmappings.png)

1. Granska gruppattributen som synkroniseras från Azure AD till G Suite i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i G Suite för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna. Användar gränssnittet visar standard uppsättningen mappningar mellan Azure AD och G Suite. Du kan välja att lägga till ytterligare attribut som org Unit genom att klicka på Lägg till ny mappning.

    ![Attribut för G Suite-grupp](media/google-apps-provisioning-tutorial/groupattributes.png)

1. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Om du vill aktivera Azure AD Provisioning-tjänsten för G Suite ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

1. Definiera de användare och/eller grupper som du vill etablera till G Suite genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

1. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på G Suite.

> [!NOTE]
> Om användarna redan har ett befintligt personligt/konsument konto med hjälp av e-postadressen för Azure AD-användaren kan det orsaka problem som kan lösas med hjälp av Google Transfer-verktyget innan katalogens synkronisering utförs.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>Vanliga problem
* Auktoriseringsfel kan uppstå när kontot som används för att upprätta en anslutning inte är för en administratör i GSuite. Kontrol lera att det konto som används för att auktorisera åtkomst har administratörs behörighet för **alla domäner** som användare måste tillhandahålla. 
* Azure AD stöder inaktive ring av användare i GSuite så att de inte kan komma åt programmet, men inte ta bort användare i GSuite.

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
