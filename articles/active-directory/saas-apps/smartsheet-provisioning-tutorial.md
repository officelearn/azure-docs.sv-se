---
title: 'Självstudier: Konfigurera Smartsheet för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Smartsheet.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: zchia
ms.openlocfilehash: e9d0d996b961c31732642c9476f529bfb7561fbe
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453403"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Smartsheet för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Smartsheet och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Smartsheet.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningsappen är för närvarande i offentlig förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Smartsheet-klient](https://www.smartsheet.com/pricing)
* Ett användarkonto på en Smartsheet Enterprise eller Enterprise Premier-plan med systemadministratören behörigheter.

## <a name="assign-users-to-smartsheet"></a>Tilldela användare till Smartsheet

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Smartsheet. När du valt, kan du tilldela dessa användare och/eller grupper till Smartsheet genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Viktiga tips för att tilldela användare till Smartsheet

* Vi rekommenderar att en enda Azure AD-användare har tilldelats Smartsheet för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Smartsheet, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

* För att säkerställa paritet i rolltilldelningar för användare mellan Smartsheet och Azure AD, rekommenderar vi att du använder samma rolltilldelningar som fylls i fullständiga Smartsheet-användarlistan. Om du vill hämta den här användarlistan från Smartsheet, gå till **kontoadministratören > Användarhantering > fler åtgärder > hämta användarlistan (csv)** .

* Om du vill komma åt vissa funktioner i appen, kräver Smartsheet att användaren har flera roller. Mer information om användaren skriver och behörigheter i Smartsheet går du till [användartyper och behörigheter](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Om en användare har flera roller i Smartsheet, du **måste** se till att de här rolltilldelningarna replikeras i Azure AD för att undvika ett scenario där användare kan förlora åtkomsten till Smartsheet objekt permanent. Varje unik roll i Smartsheet **måste** tilldelas till en annan grupp i Azure AD. Användaren **måste** och sedan läggas till var och en av de grupper som motsvarar roller som önskas. 

## <a name="set-up-smartsheet-for-provisioning"></a>Konfigurera Smartsheet för etablering

Innan du konfigurerar Smartsheet för automatisk användarförsörjning med Azure AD, kommer du behöva aktivera SCIM-etablering i Smartsheet.

1. Logga in som en **SysAdmin** i den **[Smartsheet portal](https://app.smartsheet.com/b/home)** och gå till **kontoadministratören**.

    ![Smartsheet kontoadministratören](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Gå till **säkerhetskontroller > användare Automatisk etablering > Redigera**.

    ![Smartsheet säkerhetskontroller](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Lägg till och verifiera e-postdomäner för de användare som du planerar att etablera från Azure AD till Smartsheet. Välj **inte aktiverat** så att alla etablering åtgärder endast komma från Azure AD och även säkerställa att dina Smartsheet-användarlistan är synkroniserad med Azure AD-tilldelningar.

    ![Smartsheet Användaretablering](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. När verifieringen är klar, kommer du behöva aktivera domänen. 

    ![Smartsheet aktivera domän](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Generera den **hemlighet Token** krävs för att konfigurera automatisk användaretablering med Azure AD genom att gå till **appar och integreringar**.

    ![Smartsheet-installation](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Välj **API-åtkomst**. Klicka på **Generera ny åtkomsttoken**.

    ![Smartsheet-installation](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Ange namnet på API-åtkomsttoken. Klicka på **OK**.

    ![Smartsheet-installation](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Kopiera API-åtkomsttoken och spara den som det här är den enda gången som du kan visa den. Detta krävs i den **hemlighet Token** i Azure AD.

    ![Smartsheet-token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Lägg till Smartsheet från galleriet

Om du vill konfigurera Smartsheet för automatisk användarförsörjning med Azure AD, som du behöver lägga till Smartsheet från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. I den  **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Smartsheet**väljer **Smartsheet** i resultatrutan. 

    ![Smartsheet i resultatlistan](common/search-new-app.png)

5. Välj den **registrera dig för Smartsheet** knappen som kommer att omdirigera dig till inloggningssidan för Smartsheet's. 

    ![Lägg till Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Eftersom Smartsheet är en OpenIDConnect app, väljer du att logga in på Smartsheet med ditt Microsoft-arbetskonto.

    ![Smartsheet OIDC-inloggning](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Acceptera medgivande för medgivande sidan efter en lyckad autentisering. Programmet sedan läggs automatiskt till din klient och du kommer att omdirigeras till ditt Smartsheet-konto.

    ![Smartsheet OIDc medgivande](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Konfigurera automatisk användaretablering till Smartsheet 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Smartsheet baserat på användare och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Konfigurera automatisk användaretablering för Smartsheet i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Smartsheet**.

    ![Smartsheet-länk i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering](common/provisioning.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering](common/provisioning-automatic.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet Ange `https://scim.smartsheet.com/v2/` i **klient-URL**. Indata det värde som du hämtade och sparat tidigare från Smartsheet i **hemlighet Token**. Klicka på **Testanslutningen** att se till att Azure AD kan ansluta till Smartsheet. Om anslutningen misslyckas se till att ditt Smartsheet-konto har SysAdmin-behörighet och försök igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Smartsheet**.

    ![Smartsheet Användarmappningar](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Granska användarattribut som synkroniseras från Azure AD till Smartsheet i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Smartsheet för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Smartsheet användarattribut](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera den Azure AD-etableringstjänsten för Smartsheet, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Etableringsstatus aktivt](common/provisioning-toggle-on.png)

12. Ange användare och/eller grupper som du vill att etablera till Smartsheet genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Etablering omfång](common/provisioning-scope.png)

13. När du är redo att etablera, klickar du på **spara**.

    ![Sparar Etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Smartsheet.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Smartsheet har inte stöd för mjuk-borttagningar. När en användare **active** attributet är inställt på FALSKT, Smartsheet tar bort användaren permanent.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)
