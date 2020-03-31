---
title: 'Självstudiekurs: Konfigurera Smartsheet för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Smartsheet.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063211"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Smartsheet för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Smartsheet och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Smartsheet.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Smartsheet-klient](https://www.smartsheet.com/pricing)
* Ett användarkonto på ett Smartsheet Enterprise- eller Enterprise Premier-abonnemang med behörigheten Systemadministratör.

## <a name="assign-users-to-smartsheet"></a>Tilldela användare till Smartsheet

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Smartsheet. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Smartsheet genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Viktiga tips för att tilldela användare till Smartsheet

* Vi rekommenderar att en enda Azure AD-användare tilldelas Smartsheet för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Smartsheet måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

* För att säkerställa paritet i användarrolltilldelningar mellan Smartsheet och Azure AD rekommenderar vi att du använder samma rolltilldelningar som fylls i i den fullständiga Smartsheet-användarlistan. Om du vill hämta den här användarlistan från Smartsheet navigerar du till **Kontoadministratör > Användarhantering > Fler åtgärder > Hämta användarlista (csv)**.

* För att komma åt vissa funktioner i appen kräver Smartsheet att en användare har flera roller. Mer information om användartyper och behörigheter i Smartsheet finns i [Användartyper och behörigheter](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Om en användare har flera roller som tilldelats i Smartsheet **måste** du se till att dessa rolltilldelningar replikeras i Azure AD för att undvika ett scenario där användare kan förlora åtkomsten till Smartsheet-objekt permanent. Varje unik roll i Smartsheet **MÅSTE** tilldelas en annan grupp i Azure AD. Användaren **måste** sedan läggas till i var och en av de grupper som motsvarar önskade roller. 

## <a name="set-up-smartsheet-for-provisioning"></a>Ställ in Smartsheet för etablering

Innan du konfigurerar Smartsheet för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på Smartsheet.

1. Logga in som en **SysAdmin** i **[Smartsheet-portalen](https://app.smartsheet.com/b/home)** och navigera till **Kontoadministratör**.

    ![Administratör för Smartsheet-konto](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Gå till **säkerhetskontroller > Automatisk etablering av användare > Redigera**.

    ![Säkerhetskontroller för Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Lägg till och validera e-postdomäner för de användare som du planerar att etablera från Azure AD till Smartsheet. Välj **Inte aktiverat** för att säkerställa att alla etableringsåtgärder endast kommer från Azure AD och för att säkerställa att smartsheet-användarlistan är synkroniserad med Azure AD-tilldelningar.

    ![Etablerar för smartsheet-användare](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. När valideringen är klar måste du aktivera domänen. 

    ![Aktivera domän för Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Generera den **hemliga token** som krävs för att konfigurera automatisk användaretablering med Azure AD genom att navigera till **appar och integrationer**.

    ![Installation av Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Välj **API Access**. Klicka på **Generera ny åtkomsttoken**.

    ![Installation av Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Definiera namnet på API Access-token. Klicka på **OK**.

    ![Installation av Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Kopiera API Access Token och spara den eftersom detta kommer att vara den enda gången du kan visa den. Detta krävs i fältet **Hemlig token** i Azure AD.

    ![Smartsheet-token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Lägga till Smartsheet från galleriet

Om du vill konfigurera Smartsheet för automatisk användaretablering med Azure AD måste du lägga till Smartsheet från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. Ange **Smartsheet**i sökrutan och välj **Smartsheet** på resultatpanelen. 

    ![Smartsheet i resultatlistan](common/search-new-app.png)

5. Välj knappen **Registrera dig för Smartsheet** som omdirigerar dig till Smartsheets inloggningssida. 

    ![Smartsheet OIDC Lägg till](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Eftersom Smartsheet är en OpenIDConnect-app väljer du att logga in på Smartsheet med ditt Microsoft-arbetskonto.

    ![Smartsheet OIDC-inloggning](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Efter en lyckad autentisering godkänner du samtyckesprompten för samtyckessidan. Programmet läggs sedan automatiskt till din klient och du omdirigeras till ditt Smartsheet-konto.

    ![Samtycke till Smartsheet OIDc](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Konfigurera automatisk användaretablering till Smartsheet 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Smartsheet baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Smartsheet i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Smartsheet**i programlistan .

    ![Länken Smartsheet i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://scim.smartsheet.com/v2/` **klient-URL.** Ange värdet som du hämtade och sparade tidigare från Smartsheet i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Smartsheet. Om anslutningen misslyckas kontrollerar du att Smartsheet-kontot har SysAdmin-behörigheter och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Smartsheet**.

    ![Smartsheet-användarmappningar](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till Smartsheet i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Smartsheet för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Användarattribut för Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för Smartsheet ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Smartsheet genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Smartsheet.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* Smartsheet stöder inte mjuka borttagningar. När en användares **aktiva** attribut är inställt på Falskt tas användaren bort permanent i Smartsheet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
