---
title: 'Självstudie: Konfigurera SolarWinds Service Desk (tidigare Samanage) för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig att automatiskt etablera och avetablera användar konton från Azure AD till SolarWinds Service Desk (tidigare Samanage).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 0941c95ee6215a710ebb1bbc2fba9fae09ccf16d
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707274"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Självstudie: Konfigurera SolarWinds Service Desk (tidigare Samanage) för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både SolarWinds Service Desk (tidigare Samanage) och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [SolarWinds Service Desk](https://www.samanage.com/pricing/) med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Migrera till det nya SolarWinds Service Desk-programmet

Om du har en befintlig integrering med SolarWinds Service Desk, se följande avsnitt om kommande ändringar. Om du konfigurerar SolarWinds Service Desk för första gången kan du hoppa över det här avsnittet och flytta till funktioner som **stöds**.

#### <a name="whats-changing"></a>Vad förändras?

* Ändringar på Azure AD-sidan: autentiseringsmetoden för att etablera användare i Samange har tidigare varit **grundläggande autentisering**. Snart kommer du att se autentiseringsmetoden har ändrats till den **hemliga token för lång livs längd**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Vad behöver jag för att migrera min befintliga anpassade integrering till det nya programmet?

Om du har en befintlig SolarWinds för service skriv bord med giltiga administratörsautentiseringsuppgifter **krävs ingen åtgärd**. Vi migrerar automatiskt kunder till det nya programmet. Den här processen är helt klar bakom kulisserna. Om de befintliga autentiseringsuppgifterna upphör att gälla, eller om du behöver ge åtkomst till programmet igen, måste du skapa en hemlig token för lång livs längd. Om du vill generera en ny token, se steg 2 i den här artikeln.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Hur ser jag om mitt program har migrerats? 

När ditt program migreras ersätts fälten **Administratörs användar namn** och **Administratörs lösen ord** i avsnittet **admin-autentiseringsuppgifter** med ett enda **hemligt token** -fält.

## <a name="capabilities-supported"></a>Funktioner som stöds

> [!div class="checklist"]
> * Skapa användare i SolarWinds Service Desk
> * Ta bort användare i SolarWinds Service Desk när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och SolarWinds Service Desk
> * Etablera grupper och grupp medlemskap i SolarWinds Service Desk
> * [Enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) till SolarWinds Service Desk (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* En [SolarWinds Service Desk-klient](https://www.samanage.com/pricing/) med Professional-paketet.
* Ett användar konto i SolarWinds Service Desk med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och SolarWinds Service Desk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera SolarWinds Service Desk som stöd för etablering med Azure AD

Information om hur du skapar en hemlig token för autentisering finns i [självstudie-token-autentisering för API-integrering](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till SolarWinds Service Desk från Azure AD Application Gallery

Lägg till SolarWinds Service Desk från Azure AD-programgalleriet för att börja hantera etablering till SolarWinds Service Desk. Om du tidigare har konfigurerat SolarWinds-helpdesk för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till SolarWinds-helpdesk måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare och grupper innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Steg 5. Konfigurera automatisk användar etablering till SolarWinds-helpdesk 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Konfigurera automatisk användar etablering för SolarWinds Service Desk i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SolarWinds Service Desk**.

3. Välj fliken **etablering** .

    ![Skärm bild som visar fliken etablering vald.](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Skärm bild som visar etablerings läget inställt på automatiskt.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://api.samanage.com` i **klient-URL**.  Mata in det hemliga token-värdet som hämtades tidigare i **hemlig token**. Välj **Testa anslutning** för att se till att Azure AD kan ansluta till SolarWinds-helpdesk. Om anslutningen Miss lyckas kontrollerar du att ditt SolarWinds-konto har administratörs behörighet och försöker igen.

    ![Skärm bild som visar knappen Testa anslutning vald.](./media/samanage-provisioning-tutorial/provisioning.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till SolarWinds-supportavdelningen**.

9. Granska de användarattribut som synkroniseras från Azure AD till SolarWinds Service Desk i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i SolarWinds-helpdesk för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att SolarWinds Service Desk-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

      ![Samange användar mappningar](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper för att SolarWinds Service Desk**.

11. Granska gruppattributen som synkroniseras från Azure AD till SolarWinds Service Desk i avsnittet **attribut-mappning** . De attribut som väljs som **matchande** egenskaper används för att matcha grupperna i SolarWinds-helpdesk för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      ![Samange grupp mappningar](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för SolarWinds Service Desk ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till SolarWinds-helpdesk genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera väljer du **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfånget** i avsnittet **Inställningar** . Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

1. Använd [etablerings loggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller har misslyckats
2. Kontrol lera [förlopps indikatorn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etablerings cykeln och hur nära den är att slutföras
3. Om etablerings konfigurationen verkar vara i ett ohälsosamt tillstånd, kommer programmet att placeras i karantän. Lär dig mer om karantän tillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Kopplings begränsningar

Om du väljer alternativet **synkronisera alla användare och grupper** och konfigurerar ett värde för attributet SolarWinds Service Desk- **roller** , måste värdet under **standardvärdet om värdet är null (valfritt)** anges i följande format:

- {"displayName": "roll"}, där roll är det standardvärde som du vill använda.

## <a name="change-log"></a>Ändringslogg

* 09/14/2020 – ändra företags namnet i två SaaS-självstudier från Samanage till SolarWinds Service Desk (tidigare Samanage) per https://github.com/ravitmorales .
* 04/22/2020 – uppdaterad auktoriseringskod från grundläggande autentisering till lång livs längd på den hemliga token.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
