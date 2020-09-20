---
title: 'Självstudie: Konfigurera Apple Business Manager för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till Apple Business Manager.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4ad30031-9904-4ac3-a4d2-e8c28d44f319
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: c4ac4a17e577ea69f4359e1e9b321e5fcc839697
ms.sourcegitcommit: 4ce82b6df65ebd81157b6168d3aa4e7323355022
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90761592"
---
# <a name="tutorial-configure-apple-business-manager-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Apple Business Manager för automatisk användar etablering



I den här självstudien beskrivs de steg du behöver utföra i både Apple Business Manager och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare till [Apple Business Manager](https://business.apple.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Apple Business Manager
> * Ta bort användare i Apple Business Manager när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Apple Business Manager

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör).
* Ett Apple Business Manager-konto med rollen som administratör eller personal Manager.

> [!NOTE]
> Att överföra token till Azure AD och upprätta en lyckad anslutning måste slutföras inom 4 Kalender dagar eller så måste processen startas igen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Apple Business Manager](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-apple-business-manager-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Apple Business Manager för att ge stöd för etablering med Azure AD

1. Logga in med ett konto som har rollen administratör eller person ansvarig i Apple Business Manager.
2. Klicka på Inställningar längst ned på sid panelen, klicka på data källa under organisations inställningar och klicka sedan på Anslut till data källa.
3. Klicka på Anslut bredvid SCIM, Läs varningen noggrant och klicka på Kopiera. Klicka sedan på Stäng.
[Fönstret Anslut till SCIM som innehåller en token och en kopierings knapp under den.] Lämna fönstret öppet för att kopiera klient webb adressen från Apple Business Manager till Azure AD, som är: https://federation.apple.com/feeds/business/scim

    ![Apple Business Manager](media/applebusinessmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> Den hemliga token får inte delas med någon annan än Azure AD-administratören.

## <a name="step-3-add-apple-business-manager-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Apple Business Manager från Azure AD-programgalleriet

Lägg till Apple Business Manager från Azure AD-programgalleriet för att börja hantera etablering till Apple Business Manager. Om du tidigare har installerat Apple Business Manager för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare till Apple Business Manager måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare och grupper innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-business-manager"></a>Steg 5. Konfigurera automatisk användar etablering till Apple Business Manager

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Apple Business Manager**.

    ![Apple Business Manager i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering automatiskt](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** måste du skriva in **bas-URL: en för scim 2,0 och få åtkomst till token** som hämtats från Apple Business Manager i **klient-URL** och **hemlig token** ... Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Apple Business Manager. Om anslutningen Miss lyckas kontrollerar du att ditt Apple Business Manager-konto har administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Om anslutningen lyckas, visar Apple Business Manager SCIM-anslutningen som aktiv. Den här processen kan ta upp till 60 sekunder innan Apple Business Manager visar den senaste anslutnings statusen.

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Apple Business Manager**.

9. Granska de användarattribut som synkroniseras från Azure AD till Apple Business Manager i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Apple Business Manager för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |aktiv|Boolesk|
   |userName|Sträng|
   |Name. givenName|Sträng|
   |Name. familyName|Sträng|
   |Name. givenName|Sträng|
   |externalId|Sträng|
   |locale|Sträng|
   |informationen|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: costCenter|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Division|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|

10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för Apple Business Manager ändrar du **etablerings statusen** till **på** i avsnittet Inställningar.

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Apple Business Manager genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

1. Använd [etablerings loggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller har misslyckats
2. Kontrol lera [förlopps indikatorn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etablerings cykeln och hur nära den är att slutföras
3. Om etablerings konfigurationen verkar vara i ett ohälsosamt tillstånd, kommer programmet att placeras i karantän. Lär dig mer om karantän tillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Granska SCIM-krav för Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apdd88331cd6)
* [Hur ett person-ID används i Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apd69e1e48e9)
* [Använd SCIM för att importera användare till Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apd3ec7b95ad)
* [Lösa SCIM användar konto konflikter i Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apd313013d12)
* [Ta bort Azure AD-konton som visas i Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apdaa5798fbe)
* [Visa SCIM-aktivitet i Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apd1bfd8dfde)
* [Hantera befintliga SCIM-token och anslutningar i Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apdc9a823611)
* [Koppla från scim-anslutningen i Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apd609be3a61) *  [Hantera befintliga scim-token och anslutningar i Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apdc9a8236e9)
* [Felsöka SCIM-anslutningen i Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apd403a0f3bd)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)

