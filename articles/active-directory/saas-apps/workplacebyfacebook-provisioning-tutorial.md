---
title: 'Självstudiekurs: Konfigurera Workplace by Facebook för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22576be8dec021f0f18a6e2dda16891ce70d4f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603218"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Workplace by Facebook för automatisk användaretablering

I den här självstudien beskrivs de steg du behöver utföra på både Workplace by Facebook och Azure Active Directory (Azure AD) för att konfigurera automatisk användaretablering. När azure AD är konfigurerat avsersättningar och avsersättningar av användare och grupper till [Workplace av Facebook](https://work.workplace.com/) med hjälp av Azure AD-etableringstjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Migrera till den nya Workplace by Facebook ansökan
Om du har en befintlig integrering med Workplace by Facebook läser du avsnittet nedan om kommande ändringar. Om du konfigurerar Workplace by Facebook för första gången kan du hoppa över det här avsnittet och gå vidare till de funktioner som stöds. 

#### <a name="whats-changing"></a>Vad förändras?
* Ändringar på Azure AD-sidan: Auktoriseringsmetoden för att etablera användare på Workplace har historiskt varit en långlivad hemlig token. Snart kommer du att se auktoriseringsmetoden ändras till OAuth-auktoriseringsbidraget. 
* Ändringar på workplace-sidan: Azure AD-appen var tidigare en anpassad integrering på Workplace by Facebook. Nu ser du Azure AD i katalogen Arbetsplatsintegreringar som ett tredjepartsprogram. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Vad behöver jag göra för att migrera min befintliga anpassade integrering till det nya programmet?
Om du har en befintlig Workplace-integrering med en giltig token **krävs ingen åtgärd**. Vi migrerar automatiskt kunder varje vecka till det nya programmet. Detta görs helt bakom kulisserna. Om du inte kan vänta och vill flytta till det nya programmet manuellt kan du lägga till en ny instans av Workplace från galleriet och konfigurera etableringen igen. Alla nya instanser av Workplace kommer automatiskt att använda den nya programversionen. 

 
Om din Workplace-integrering är i karantän måste du ange en giltig token igen för att vi ska kunna migrera dig. Avsnittet administratörsautentiseringsuppgifter kommer att vara nedtonat, men du kan lägga till följande (**? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true)** till webbadressen för att spara autentiseringsuppgifter igen. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Hur vet jag om mitt program har migrerats? 
När ditt program migreras tas banderollen i auktoriseringsavsnittet om omväljningsändringar bort och det hemliga tokenfältet ersätts med en blå auktoriseringsknapp. 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>Avsnittet administratörsautentiseringsuppgifter är nedtonat på mitt program och jag kan inte spara. Varför det?
Vi har låst avsnittet administratörsautentiseringsuppgifter för befintliga Workplace-kunder. När din klient har migrerats till det nya Workplace-programmet kan du uppdatera avsnittet administratörsautentiseringsuppgifter igen. Om du inte kan vänta kan du använda webbadressen ovan för att redigera programmet. 

 
#### <a name="when-will-these-changes-happen"></a>När kommer dessa förändringar att ske?
Alla nya instanser av Workplace kommer redan att använda den nya integrations-/auktoriseringsmetoden. Befintliga integrationer kommer att migreras gradvis i maj. Arbetsgruppen har gett en förlängning av tidsfristen från 28 februari till 1 maj. 

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare på Workplace by Facebook
> * Ta bort användare på Workplace by Facebook när de inte längre behöver åtkomst
> * Synkronisera användarattribut mellan Azure AD och Workplace by Facebook
> * [Enkel inloggning på](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) Workplace by Facebook (rekommenderas)

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användarkonto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör)
* En arbetsplats av Facebook enkel inloggning aktiverat prenumeration

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en utvärderingsmiljö för Azure AD kan du få en utvärderingsversion på en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera distributionen av etableringen
1. Läs mer om [hur etableringstjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bestäm vem som ska vara i [omfång för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bestäm vilka data som ska [mappas mellan Azure AD och Workplace by Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Workplace by Facebook för att stödja etablering med Azure AD

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till din Arbetsplats via Facebook-app. När du har bestämt dig kan du tilldela dessa användare till din Workplace via Facebook-appen genom att följa instruktionerna här:

*   Vi rekommenderar att en enda Azure AD-användare tilldelas Workplace av Facebook för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Workplace by Facebook måste du välja en giltig användarroll. Rollen "Standardåtkomst" fungerar inte för etablering.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Steg 3. Lägga till Arbetsplats av Facebook från Azure AD-programgalleriet

Lägg till Workplace by Facebook från Azure AD-programgalleriet för att börja hantera etablering på Workplace by Facebook. Om du tidigare har konfigurerat Workplace by Facebook för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integrationen från början. Läs mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska vara i utrymme för etablering 

Azure AD-etableringstjänsten gör att du kan begränsa vem som ska etableras baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att begränsa vem som ska etableras i din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att begränsa vem som ska etableras enbart baserat på attribut för användaren eller gruppen kan du använda ett omfångsfilter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till Workplace by Facebook måste du välja en annan roll än **Standardåtkomst**. Användare med rollen Standardåtkomst är undantagna från etablering och markeras som inte effektivt berättigade i etableringsloggarna. Om den enda roll som är tillgänglig för programmet är standardåtkomstrollen kan du [uppdatera programmanifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Börja i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfång för etablering är inställt på tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När scopet är inställt på alla användare och grupper kan du ange ett [attributbaserat omfångsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj Arbetsplats av **Facebook**i listan över applikationer .

    ![Länken för Workplace by Facebook i listan med program](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Klicka på **Auktorisera**under avsnittet **Administratörsautentiseringsuppgifter** . Du kommer att omdirigeras till Workplace av Facebooks auktoriseringssida. Mata in ditt användarnamn på Workplace efter Facebook och klicka på knappen **Fortsätt.** Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Workplace by Facebook. Om anslutningen misslyckas kontrollerar du att workplace by Facebook-kontot har administratörsbehörighet och försöker igen.

    ![Etableringen](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![auktorisera](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till arbetsplats av Facebook**.

9. Granska användarattributen som synkroniseras från Azure AD till Workplace av Facebook i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona på Workplace by Facebook för uppdateringsåtgärder. Om du väljer att ändra det [matchande målattributet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att Workplace by Facebook API stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** om du vill utföra eventuella ändringar.

   |Attribut|Typ|
   |---|---|
   |userName|String|
   |displayName|String|
   |aktiv|Boolean|
   |title|Boolean|
   |e-postmeddelanden[typ eq "arbete"].värde|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |adresser[skriv eq "work"].formaterad|String|
   |adresser[typ eq "work"].streetAddress|String|
   |adresser[typ eq "work"].locality|String|
   |adresser[typ eq "arbete"].region|String|
   |adresser[typ eq "arbete"].land|String|
   |adresser[typ eq "arbete"].postalCode|String|
   |adresser[skriv eq "other"].formaterad|String|
   |phoneNumbers[typ eq "arbete"].värde|String|
   |phoneNumbers[typ eq "mobil"].värde|String|
   |phoneNumbers[typ eq "fax"].värde|String|
   |externt|String|
   |preferredLanguage|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för Workplace by Facebook ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Workplace av Facebook genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den inledande synkroniseringscykeln för alla användare och grupper som **definierats** i Scope i avsnittet **Inställningar.** Den inledande cykeln tar längre tid att utföra än efterföljande cykler, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

1. Använd [etableringsloggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller utan framgång
2. Kontrollera [förloppsindikatorn](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etableringscykeln och hur nära den är till slutförande
3. Om etableringskonfigurationen verkar vara i feltillstånd kommer programmet att placeras i karantän. Läs mer om karantäntillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Felsökningstips
*  Om du ser en användare utan framgång skapas och det finns en granskningslogghändelse med koden "1789003" betyder det att användaren kommer från en overifierad domän.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../manage-apps/check-status-user-account-provisioning.md)
