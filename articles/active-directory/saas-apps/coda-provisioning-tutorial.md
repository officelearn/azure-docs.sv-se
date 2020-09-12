---
title: 'Självstudie: Konfigurera CODA för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till CODA.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: Zhchia
ms.openlocfilehash: d5748b9d374a33e0aa3c52d9623f4e73d3bace1c
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89303650"
---
# <a name="tutorial-configure-coda-for-automatic-user-provisioning"></a>Självstudie: Konfigurera CODA för automatisk användar etablering

I den här självstudien beskrivs de steg som du måste utföra i både CODA och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare i [CODA](https://coda.io/) med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i CODA
> * Ta bort användare i CODA när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och CODA
> * [Enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial) till CODA (rekommenderas)

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t. ex. program administratör, moln program administratör, program ägare eller global administratör).
* Ett [CODA företags](https://help.coda.io/en/articles/3520174-getting-started-with-sso) administratörs konto.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och CODA](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-coda-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera CODA för att stödja etablering med Azure AD

1. Öppna din organisations administratörs konsol genom att välja organisations inställningar under... -menyn under din arbets yta.

    ![CODA Enterprise Organization SCIM-inställningar](media/coda-provisioning-tutorial/coda-scim-enable.png)

2. Se till att etablera med SCIM har Aktiver ATS.
3. Observera SCIM bas-URL och SCIM Bearer-token. Om det inte finns någon Bearer-token klickar du på Skapa ny token.

## <a name="step-3-add-coda-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till CODA från Azure AD-programgalleriet

Lägg till CODA från Azure AD-programgalleriet för att börja hantera etablering till CODA. Om du tidigare har konfigurerat CODA för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på användarens attribut. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på användarens attribut kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

* När du tilldelar användare till CODA måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller.

* Starta litet. Testa med en liten uppsättning användare innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare kan du kontrol lera detta genom att tilldela en eller två användare till appen. När omfång är inställt på alla användare kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).


## <a name="step-5-configure-automatic-user-provisioning-to-coda"></a>Steg 5. Konfigurera automatisk användar etablering till CODA

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare i TestApp baserat på användar tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-coda-in-azure-ad"></a>Konfigurera automatisk användar etablering för CODA i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **CODA**.

    ![CODA-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin-autentiseringsuppgifter** skriver du in URL: en för CODA-klienten och den hemliga token som hämtades tidigare i steg 2. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till CODA. Om anslutningen Miss lyckas kontrollerar du att ditt CODA-konto har administratörs behörighet och försöker igen.

    ![etablerings](./media/coda-provisioning-tutorial/provisioning.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till CODA**.

9. Granska de användarattribut som synkroniseras från Azure AD till CODA i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i CODA för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att CODA-API: et stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |aktiv|Boolesk|
   |Name. givenName|Sträng|
   |Name. familyName|Sträng|


10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för CODA ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare som du vill etablera till CODA genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare som **Scope** definieras i området **Inställningar** . Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs.

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
