---
title: 'Självstudie: Konfigurera Smartsheet för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Smartsheet.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063211"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Smartsheet för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Smartsheet och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Smartsheet.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Smartsheet-klient](https://www.smartsheet.com/pricing)
* Ett användar konto i ett Smartsheet Enterprise-eller Enterprise Premier-plan med system administratörs behörighet.

## <a name="assign-users-to-smartsheet"></a>Tilldela användare till Smartsheet

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Smartsheet. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Smartsheet genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Viktiga tips för att tilldela användare till Smartsheet

* Vi rekommenderar att en enda Azure AD-användare tilldelas Smartsheet för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Smartsheet måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

* För att säkerställa paritet i användar roll tilldelningar mellan Smartsheet och Azure AD, rekommenderar vi att du använder samma roll tilldelningar som är ifyllda i listan med fullständiga Smartsheet-användare. Om du vill hämta användar listan från Smartsheet navigerar du till **konto administratör > användar hantering > fler åtgärder > Ladda ned användar listan (CSV)** .

* För att få åtkomst till vissa funktioner i appen kräver Smartsheet att en användare har flera roller. Om du vill veta mer om användar typer och behörigheter i Smartsheet går du till [användar typer och behörigheter](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Om en användare har flera roller tilldelade i Smartsheet, **måste** du se till att roll tilldelningarna replikeras i Azure AD för att undvika ett scenario där användare kan förlora åtkomsten till Smartsheet-objekt permanent. Varje unik roll i Smartsheet **måste** tilldelas en annan grupp i Azure AD. Användaren **måste** sedan läggas till i var och en av de grupper som motsvarar de roller som önskas. 

## <a name="set-up-smartsheet-for-provisioning"></a>Konfigurera Smartsheet för etablering

Innan du konfigurerar Smartsheet för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på Smartsheet.

1. Logga in som **sysadmin** i Smartsheet- **[portalen](https://app.smartsheet.com/b/home)** och gå till **konto administratör**.

    ![Smartsheet-konto administratör](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Gå till **säkerhets kontroller > automatisk etablering av användare > redigera**.

    ![Smartsheet säkerhets kontroller](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Lägg till och validera e-postdomänerna för de användare som du planerar att etablera från Azure AD till Smartsheet. Välj **inte aktive rad** för att säkerställa att alla etablerings åtgärder endast kommer från Azure AD, och se till att din Smartsheet användar lista är synkroniserad med Azure AD-tilldelningar.

    ![Smartsheet användar etablering](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. När verifieringen är klar måste du aktivera domänen. 

    ![Smartsheet Aktivera domän](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Generera den **hemliga token** som krävs för att konfigurera automatisk användar etablering med Azure AD genom att navigera till **appar och integreringar**.

    ![Smartsheet-installation](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Välj **API-åtkomst**. Klicka på **Skapa ny**åtkomsttoken.

    ![Smartsheet-installation](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Definiera namnet på API-åtkomsttoken. Klicka på **OK**

    ![Smartsheet-installation](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Kopiera API-åtkomsttoken och spara den eftersom det är den enda gången du kan visa den. Detta krävs i fältet **hemligt token** i Azure AD.

    ![Smartsheet-token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Lägg till Smartsheet från galleriet

Om du vill konfigurera Smartsheet för automatisk användar etablering med Azure AD måste du lägga till Smartsheet från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Smartsheet**och väljer **Smartsheet** i panelen resultat. 

    ![Smartsheet i resultat listan](common/search-new-app.png)

5. Välj knappen **Registrera dig för Smartsheet** som kommer att omdirigera dig till inloggnings sidan för Smartsheet. 

    ![Smartsheet OIDC Lägg till](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Som Smartsheet är en OpenIDConnect-app väljer du att logga in på Smartsheet med ditt Microsoft Work-konto.

    ![Smartsheet OIDC-inloggning](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. När autentiseringen är klar godkänner du frågan om medgivande för sidan medgivande. Programmet läggs sedan till automatiskt i din klient organisation och du omdirigeras till ditt Smartsheet-konto.

    ![Smartsheet OIDc-medgivande](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Konfigurera automatisk användar etablering till Smartsheet 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Smartsheet baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Konfigurera automatisk användar etablering för Smartsheet i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Smartsheet**.

    ![Smartsheet-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , in`https://scim.smartsheet.com/v2/` i **klient-URL**. Mata in det värde som du hämtade och sparade tidigare från Smartsheet i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Smartsheet. Om anslutningen Miss lyckas kontrollerar du att ditt Smartsheet-konto har SysAdmin-behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Save** (Spara).

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Smartsheet**.

    ![Smartsheet användar mappningar](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Smartsheet i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Smartsheet för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Smartsheet-användarattribut](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för Smartsheet ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Smartsheet genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Smartsheet.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Smartsheet stöder inte mjuka borttagningar. När en användares **aktiva** attribut är inställt på false, tar Smartsheet bort användaren permanent.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
