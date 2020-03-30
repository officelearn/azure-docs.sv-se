---
title: 'Självstudiekurs: Konfigurera G Suite för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du automatiskt etablerar och avserar användarkonton från Azure AD till G Suite.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057744"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera G Suite för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i G Suite och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till G Suite.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> G Suite-kontakten uppdaterades nyligen i oktober 2019. Ändringar som gjorts i G Suite-kontakten omfattar:
> - Lade till stöd för ytterligare G Suite-användar- och gruppattribut. 
> - Uppdaterade G Suite-målattributnamn för att matcha det som definieras [här](https://developers.google.com/admin-sdk/directory).
> - Uppdaterade standardattributmappningar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med G Suite, behöver du följande objekt:

- En Azure AD-klient
- [En G Suite-klient](https://gsuite.google.com/pricing.html)
- Ett användarkonto på en G Suite med administratörsbehörighet.

## <a name="assign-users-to-g-suite"></a>Tilldela användare till G Suite

Azure Active Directory använder ett koncept som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till G Suite. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till G Suite genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Viktiga tips för att tilldela användare till G Suite

* Vi rekommenderar att en enda Azure AD-användare tilldelas G Suite för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till G Suite måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-g-suite-for-provisioning"></a>Setup G Suite för etablering

Innan du konfigurerar G Suite för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på G Suite.

1. Logga in på [G Suite-administratörskonsolen](https://admin.google.com/) med ditt administratörskonto och välj sedan **Säkerhet**. Om länken inte visas kan den vara dold under menyn **Fler kontroller** längst ned på skärmen.

    ![Välj säkerhet.][10]

1. Välj **API-referens**på sidan **Säkerhet** .

    ![Välj API-referens.][15]

1. Välj **Aktivera API-åtkomst**.

    ![Välj API-referens.][16]

   > [!IMPORTANT]
   > För varje användare som du tänker etablera till G Suite **måste** deras användarnamn i Azure AD vara bundet till en anpassad domän. Användarnamn som bob@contoso.onmicrosoft.com ser ut accepteras till exempel inte av G Suite. Å andra sidan bob@contoso.com accepteras. Du kan ändra en befintlig användares domän genom att följa instruktionerna [här](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

1. När du har lagt till och verifierat önskade anpassade domäner med Azure AD måste du verifiera dem igen med G Suite. Information om hur du verifierar domäner i G Suite finns i följande steg:

    a. Välj Domäner i [G Suite-administratörskonsolen](https://admin.google.com/). **Domains**

    ![Välj domäner][20]

    b. Välj **Lägg till en domän eller ett domänalias**.

    ![Lägga till en ny domän][21]

    c. Välj **Lägg till en annan domän**och skriv sedan in namnet på den domän som du vill lägga till.

    ![Skriv in ditt domännamn][22]

    d. Välj **Fortsätt och verifiera domänägarskap**. Följ sedan stegen för att kontrollera att du äger domännamnet. Omfattande instruktioner om hur du verifierar din domän med Google finns i [Verifiera ditt ägarskap](https://support.google.com/webmasters/answer/35179)på webbplatsen .

    e. Upprepa föregående steg för eventuella ytterligare domäner som du tänker lägga till i G Suite.

1. Ta sedan reda på vilket administratörskonto du vill använda för att hantera användaretablering i G Suite. Navigera till **administratörsroller**.

    ![Välj Google Apps][26]

1. Redigera **behörigheten För** **administratörsrollen** för det kontot. Se till att aktivera alla **administratörs-API-privilegier** så att det här kontot kan användas för etablering.

    ![Välj Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>Lägg till G Suite från galleriet

Om du vill konfigurera G Suite för automatisk användaretablering med Azure AD måste du lägga till G Suite från Azure AD-programgalleriet i listan över hanterade SaaS-program. 

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

1. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

1. I sökrutan anger du **G Suite,** väljer **G Suite** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![G Suite i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Konfigurera automatisk användaretablering till G Suite 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i G Suite baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för G Suite, följ instruktionerna i [G Suite Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

> [!NOTE]
> Mer information om G Suites katalog-API-slutpunkt finns i [Katalog-API](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för G Suite i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

1. Välj **G Suite**i programlistan .

    ![G Suite-länken i programlistan](common/all-applications.png)

1. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

1. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

1. Under avsnittet **Administratörsautentiseringsuppgifter** väljer du **Auktorisera**. Den öppnar en Google-dialogrutan för auktorisering i ett nytt webbläsarfönster.

    ![G-sviten godkänner](media/google-apps-provisioning-tutorial/authorize.png)

1. Bekräfta att du vill ge Azure AD-behörigheter för att göra ändringar i din G Suite-klientorganisation. Välj **Acceptera**.

    ![Bekräfta behörigheter.][28]

1. I Azure-portalen väljer du **Testa anslutning** för att säkerställa att Azure AD kan ansluta till din app. Om anslutningen misslyckas kontrollerar du att ditt G Suite-konto har behörighet för teamadministratör. Försök sedan **att auktorisera** steget igen.

1. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

1. Klicka på **Spara**.

1. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till G Suite**.

    ![G Suite-användarmappningar](media/google-apps-provisioning-tutorial/usermappings.png)

1. Granska användarattributen som synkroniseras från Azure AD till G Suite i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i G Suite för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![G Suite-användarattribut](media/google-apps-provisioning-tutorial/userattributes.png)

1. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till G Suite**.

    ![G Suite Gruppmappningar](media/google-apps-provisioning-tutorial/groupmappings.png)

1. Granska gruppattributen som synkroniseras från Azure AD till G Suite i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i G Suite för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar. Användargränssnittet visar standarduppsättningen med attributmappningar mellan Azure AD och G Suite. Du kan välja att lägga till ytterligare attribut som orgenhet genom att klicka på Lägg till ny mappning.

    ![G Suite-gruppattribut](media/google-apps-provisioning-tutorial/groupattributes.png)

1. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Om du vill aktivera Azure AD-etableringstjänsten för G Suite ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

1. Definiera de användare och/eller grupper som du vill etablera till G Suite genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

1. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på G Suite.

> [!NOTE]
> Om användarna redan har ett befintligt personligt/konsumentkonto med hjälp av Azure AD-användarens e-postadress kan det orsaka vissa problem som kan lösas med hjälp av Google Transfer Tool innan katalogsynkroniseringen utförs.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>Vanliga problem
* Auktoriseringsfel kan uppstå när kontot som används för att upprätta en anslutning inte är för en administratör i GSuite. Kontrollera att kontot som används för att auktorisera åtkomst har administratörsbehörighet för **alla domäner** som användare måste etableras med. 
* Azure AD stöder inaktivera användare i GSuite så att de inte kan komma åt programmet, men det tar inte bort användare i GSuite.

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

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
