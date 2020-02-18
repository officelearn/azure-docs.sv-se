---
title: 'Självstudie: Konfigurera Azure Databricks SCIM-anslutning för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig att automatiskt etablera och avetablera användar konton från Azure AD till Azure Databricks SCIM-anslutning.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: fe1260982edc877c049716bd74f1bb3e90d33b0f
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370535"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Azure Databricks SCIM-anslutning för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både Azure Databricks SCIM-anslutning och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [Azure DATABRICKS scim-anslutning](https://databricks.com/) med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Azure Databricks SCIM Connector
> * Ta bort användare i Azure Databricks SCIM-anslutning när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Azure Databricks SCIM Connector
> * Etablera grupper och grupp medlemskap i Azure Databricks SCIM Connector

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t. ex. program administratör, moln program administratör, program ägare eller global administratör). 
* Ett Azure Databricks konto med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Azure DATABRICKS scim Connector](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Azure Databricks SCIM-anslutning som stöder etablering med Azure AD

1. Om du vill konfigurera Azure Databricks SCIM-etablering lägger du till den som en resurs i Azure Active Directory klienten och konfigurerar den med inställningarna nedan.

    ![Azure Databricks installation](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. Om du vill generera en personlig åtkomsttoken i Azure Databricks se [detta](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management).

3. Kopiera **token**. Det här värdet anges i fältet Hemlig token på fliken etablering i Azure Databricks SCIM Connector-programmet i Azure Portal.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Azure Databricks SCIM-koppling från Azure AD-programgalleriet

Lägg till Azure Databricks SCIM Connector från Azure AD-programgalleriet för att börja hantera etablering till Azure Databricks SCIM-anslutning. Om du tidigare har installerat Azure Databricks SCIM Connector för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till Azure Databricks SCIM Connector måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare och grupper innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>Steg 5. Konfigurera automatisk användar etablering till Azure Databricks SCIM Connector 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!NOTE]
> Läs mer om hur du Azure Databricks SCIM-slutpunkten i [detta](https://docs.databricks.com/dev-tools/api/latest/scim.html
).

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Konfigurera automatisk användar etablering för Azure Databricks SCIM Connector i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Azure DATABRICKS scim Connector**.

    ![Länken Azure Databricks SCIM-koppling i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** måste du skriva in värdet för scim-slutpunkt i **klient-URL: en**. Klient webb adressen ska vara i formatet `https://<region>.azuredatabricks.net/api/2.0/preview/scim` där **regionen** finns på din Azure Databricks start sidans URL. Till exempel kommer en SCIM-slutpunkt för regionen **Västeuropa** att `https://westus.azuredatabricks.net/api/2.0/preview/scim`. Mata in token-värdet som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Azure Databricks scim-anslutningen. Om anslutningen Miss lyckas kontrollerar du att Azure Databricks SCIM Connector-kontot har administratörs behörighet och försöker igen.

    ![etablerings](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Azure Databricks scim-anslutning**.

9. Granska de användarattribut som synkroniseras från Azure AD till Azure Databricks SCIM-anslutaren i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Azure Databricks scim Connector för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du kontrol lera att API: et för Azure Databricks scim Connector stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|String|
   |displayName|String|
   |aktiv|Boolean|

10. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper för att Azure Databricks scim Connector**.

11. Granska gruppattributen som synkroniseras från Azure AD till Azure Databricks SCIM-anslutaren i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Azure Databricks scim-koppling för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

     |Attribut|Typ|
     |---|---|
     |displayName|String|
     |medlemmar|Referens|

11. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper för att Azure Databricks scim Connector**.

12. Om du vill aktivera Azure AD Provisioning-tjänsten för Azure Databricks SCIM-anslutning ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

13. Definiera de användare och/eller grupper som du vill etablera för att Azure Databricks SCIM-anslutning genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

14. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfånget** i avsnittet **Inställningar** . Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

* Använd [etablerings loggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller har misslyckats
* Kontrol lera [förlopps indikatorn](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etablerings cykeln och hur nära den är att slutföras
* Om etablerings konfigurationen verkar vara i ett ohälsosamt tillstånd, kommer programmet att placeras i karantän. Lär dig mer om karantän tillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Felsökningstips
* Databricks konverterar alltid sina username-värden till gemener när de sparas i katalogen, oavsett vilken kapitalisering vi skickar till dem via SCIM.
* För närvarande GET-begäranden mot Azure Databricks "SCIM-API: n för användare är Skift läges känsliga, så om vi frågar efter USER@contoso.com det kommer upp med 0 resultat eftersom de lagrar den som user@contoso.com.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
