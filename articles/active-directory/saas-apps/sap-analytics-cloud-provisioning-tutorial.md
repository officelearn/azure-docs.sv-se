---
title: 'Självstudie: konfigurera SAP Analytics-moln för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig att automatiskt etablera och avetablera användar konton från Azure AD till SAP Analytics-molnet.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 27d12989-efa8-4254-a4ad-8cb6bf09d839
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2020
ms.author: Zhchia
ms.openlocfilehash: 03764dc9999b233153588f4908a2c10587697e40
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88861257"
---
# <a name="tutorial-configure-sap-analytics-cloud-for-automatic-user-provisioning"></a>Självstudie: konfigurera SAP Analytics-moln för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både SAP Analytics-molnet och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [SAP Analytics-molnet](https://www.sapanalytics.cloud/) med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i SAP Analytics-molnet
> * Ta bort användare i SAP Analytics-molnet när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och SAP Analytics-molnet
> * [Enkel inloggning](sapboc-tutorial.md) till SAP Analytics-moln (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t. ex. program administratör, moln program administratör, program ägare eller global administratör). 
* En SAP Analytics Cloud-klient
* Ett användar konto på en SAP Identity-etablerings administratörs konsol med administratörs behörighet. Se till att du har åtkomst till proxy-systemen i administratörs konsolen för identitets etablering. Om du inte ser panelen **proxyservrar** skapar du en incident för komponenten **BC-IAM-IP-adresser** för att begära åtkomst till den här panelen.
* En OAuth-klient med auktoriserings-klientautentiseringsuppgifter i SAP Analytics-molnet. Läs mer i: [Hantera OAuth-klienter och betrodda identitets leverantörer](https://help.sap.com/viewer/00f68c2e08b941f081002fd3691d86a7/release/en-US/4f43b54398fc4acaa5efa32badfe3df6.html)

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen

1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och SAP Analytics-molnet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-sap-analytics-cloud-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera SAP Analytics-molnet för att ge stöd för etablering med Azure AD

1. Logga in på [administrations konsolen för SAP Identity-etableringen](https://ips-xlnk9v890j.dispatcher.us1.hana.ondemand.com/) med ditt administratörs konto och välj sedan **proxyservrar**.

   ![SAP-proxyservrar](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems.png.png)

2. Välj **Egenskaper**.

   ![Egenskaper för SAP-proxy-system](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-properties.png)

3. Kopiera **URL: en** och Lägg till i `/api/v1/scim` URL: en. Spara detta för senare användning i fältet **klient-URL** .

   ![Egenskaper för SAP-proxy-system URL](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-url.png)

4. Använd [Postman](https://www.postman.com/) för att utföra ett post-https-anrop till adressen: `<Token URL>?grant_type=client_credentials` där `Token URL` är URL: en i fältet **OAuth2TokenServiceURL** . Det här steget krävs för att generera en åtkomsttoken som ska användas i fältet hemligt token när automatisk etablering konfigureras.

   ![Egenskaper för SAP IP-proxy-system OAuth](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-oauth.png)

5. I Postman använder du **grundläggande autentisering**och anger OAuth-klient-ID som användare och hemligheten som lösen ord. Anropet returnerar en åtkomsttoken. Behåll detta kopierat för senare användning i fältet **hemlig token** .

   ![Postman POST-begäran](./media/sap-analytics-cloud-provisioning-tutorial/postman-post-request.png)

## <a name="step-3-add-sap-analytics-cloud-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till SAP Analytics-moln från Azure AD-programgalleriet

Lägg till SAP Analytics-moln från Azure AD-programgalleriet för att börja hantera etablering till SAP Analytics-molnet. Om du tidigare har konfigurerat SAP Analytics-molnet för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till SAP Analytics-molnet måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare och grupper innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-sap-analytics-cloud"></a>Steg 5. Konfigurera automatisk användar etablering till SAP Analytics-molnet 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-sap-analytics-cloud-in-azure-ad"></a>Konfigurera automatisk användar etablering för SAP Analytics-moln i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SAP Analytics-moln**.

    ![Moln länken SAP Analytics i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** kan du mata in klient webb adressen som hämtades tidigare i **klient-URL: en**. Mata in värdet för åtkomsttoken som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till en insikt. Om anslutningen Miss lyckas kontrollerar du att ditt SAP Analytics Cloud-konto har administratörs behörighet och försöker igen.

    ![etablerings](./media/sap-analytics-cloud-provisioning-tutorial/provisioning.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **etablera Azure Active Directory användare**.

9. Granska de användarattribut som synkroniseras från Azure AD till SAP Analytics-molnet i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i SAP Analytics-molnet för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att moln-API: t SAP Analytics stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |Name. givenName|Sträng|
   |Name. familyName|Sträng|
   |aktiv|Boolesk|
   |e-postmeddelanden [typ EQ "Work"]. värde|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Sträng|

10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för SAP Analytics-molnet ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till SAP Analytics-molnet genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfånget** i avsnittet **Inställningar** . Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

1. Använd [etablerings loggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller har misslyckats
2. Kontrol lera [förlopps indikatorn](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etablerings cykeln och hur nära den är att slutföras
3. Om etablerings konfigurationen verkar vara i ett ohälsosamt tillstånd, kommer programmet att placeras i karantän. Lär dig mer om karantän tillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
