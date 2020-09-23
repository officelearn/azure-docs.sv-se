---
title: 'Självstudie: Konfigurera arbets ytan efter Facebook för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workplace by Facebook.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/28/2020
ms.author: jeedes
ms.openlocfilehash: ae13e062f50e1e8eefeaa886c67c636cf6230c18
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90973882"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Självstudie: Konfigurera arbets ytan efter Facebook för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i båda arbets platserna av Facebook och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [arbets ytan efter Facebook](https://work.workplace.com/) med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare på arbets ytan efter Facebook
> * Ta bort användare i arbets ytan efter Facebook när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och arbets platsen på Facebook
> * [Enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) till arbets plats efter Facebook (rekommenderas)

>[!VIDEO https://www.youtube.com/embed/oF7I0jjCfrY]

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t. ex. program administratör, moln program administratör, program ägare eller global administratör)
* En arbets plats per Facebook-aktiverad prenumeration med enkel inloggning

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en utvärderings miljö för Azure AD kan du få en månads utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som finns i [etableringsomfånget](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och arbets ytan på Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera arbets ytan efter Facebook för att stödja etablering med Azure AD

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till din arbets plats av Facebook-appen. När du har bestämt dig kan du tilldela dessa användare till din arbets plats av Facebook-appen genom att följa anvisningarna här:

*   Vi rekommenderar att en enda Azure AD-användare tilldelas arbets platsen av Facebook för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till arbets ytan per Facebook måste du välja en giltig användar roll. Rollen "standard åtkomst" fungerar inte för etablering.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till arbets plats från Facebook från Azure AD-programgalleriet

Lägg till arbets ytan från Facebook från Azure AD-programgalleriet för att börja hantera etablering till arbets platsen av Facebook. Om du tidigare har konfigurerat arbets ytan på Facebook för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till arbets ytan per Facebook måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **arbets plats efter Facebook**.

    ![Länken för Workplace by Facebook i listan med program](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** klickar du på **auktorisera**. Du kommer att omdirigeras till sidan för arbets plats på Facebook-sidan. Mata in din arbets plats efter Facebook-användarnamn och klicka på knappen **Fortsätt** . Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till arbets ytan på Facebook. Om anslutningen Miss lyckas ser du till att din arbets plats av Facebook-kontot har administratörs behörighet och försöker igen.

    ![Skärm bild som visar dialog rutan admin-autentiseringsuppgifter med ett auktorisera-alternativ.](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![auktorisera](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till arbets ytan efter Facebook**.

9. Granska de användarattribut som synkroniseras från Azure AD till arbets ytan efter Facebook i avsnittet **attribut-mappning** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona på arbets platsen av Facebook för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att arbets ytan av Facebook API stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |displayName|Sträng|
   |aktiv|Boolesk|
   |title|Boolesk|
   |emails[type eq "work"].value|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |namn. formaterad|Sträng|
   |adresser [Type EQ "Work"]. formaterad|Sträng|
   |adresser [Type EQ "Work"]. streetAddress|Sträng|
   |adresser [Type EQ "Work"]. plats|Sträng|
   |adresser [Type EQ "Work"]. region|Sträng|
   |adresser [Type EQ "Work"]. land|Sträng|
   |adresser [Type EQ "Work"]. Postnr|Sträng|
   |adresser [Type EQ "other"]. formaterad|Sträng|
   |phoneNumbers[type eq "work"].value|Sträng|
   |phoneNumbers[type eq "mobile"].value|Sträng|
   |phoneNumbers [Type EQ "fax"]. värde|Sträng|
   |externalId|Sträng|
   |preferredLanguage|Sträng|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Sträng|
   |urn:scim:schemas:extension:enterprise:1.0.department|Sträng|
   |urn:scim:schemas:extension:enterprise:1.0.division|Sträng|
   |urn:scim:schemas:extension:enterprise:1.0.organization|Sträng|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|Sträng|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|Sträng|
   |urn: scim: schemas: tillägg: Facebook: auth_method: 1.0: auth_method|Sträng|
   |urn: scim: schemas: tillägg: Facebook: Frontline: 1.0. is_frontline|Boolesk|
   |urn: scim: schemas: tillägg: Facebook: starttermdates: 1.0. StartDate|Heltal|


10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för arbets ytan av Facebook ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till arbets ytan på Facebook genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Felsökningstips
*  Om du ser en användare som inte har skapats utan problem och det finns en Gransknings logg händelse med koden "1789003" innebär det att användaren är från en overifierad domän.

## <a name="change-log"></a>Ändringslogg

* 09/10/2020 – stöd för företags-attribut "Division", "Organization", "costCenter" och "employeeNumber" har lagts till. Stöd har lagts till för anpassade attribut "StartDate", "auth_method" och "Frontline"

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../manage-apps/check-status-user-account-provisioning.md)
