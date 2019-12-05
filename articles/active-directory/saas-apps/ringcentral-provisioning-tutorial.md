---
title: 'Självstudie: Konfigurera RingCentral för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till RingCentral.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 976ac071-fa7e-4f31-aed1-d174942860d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2019
ms.author: Zhchia
ms.openlocfilehash: d30195cb9aceac0e785f01ce8c2ce78d7a9d4da7
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805781"
---
# <a name="tutorial-configure-ringcentral-for-automatic-user-provisioning"></a>Självstudie: Konfigurera RingCentral för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i RingCentral och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till RingCentral.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En RingCentral-klient](https://www.ringcentral.com/office/plansandpricing.html)
* Ett användar konto i RingCentral med administratörs behörighet.

## <a name="assigning-users-to-ringcentral"></a>Tilldela användare till RingCentral

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till RingCentral. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till RingCentral genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ringcentral"></a>Viktiga tips för att tilldela användare till RingCentral

* Vi rekommenderar att en enda Azure AD-användare tilldelas RingCentral för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till RingCentral måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-ringcentral-for-provisioning"></a>Konfigurera RingCentral för etablering

1. Logga in på din [RingCentral-administratörs konsol](https://login.ringcentral.com/sw.html). Navigera till **verktyg > katalog integrering**.

    ![RingCentral-administratörskonsolen](media/ringcentral-provisioning-tutorial/admin.png)

2.  Välj **scim** under **Välj katalog leverantör**. (I framtiden kommer det att finnas ett alternativ som kallas Azure Active Directory). Klicka på **Aktivera scim-tjänst**.

    ![RingCentral Lägg till SCIM](media/ringcentral-provisioning-tutorial/scim.png)

3.  Kontakta RingCentral support team på matthew.hunt@ringcentral.com för en **scim-autentiseringstoken**. Det här värdet anges i fältet Hemlig token på fliken etablering i ditt RingCentral-program i Azure Portal.

> [!NOTE]
> Information om hur du tilldelar licenser till användare finns i video länken [här](https://support.ringcentral.com/s/article/5-10-Adding-Extensions-via-Web?language).

## <a name="add-ringcentral-from-the-gallery"></a>Lägg till RingCentral från galleriet

Innan du konfigurerar RingCentral för automatisk användar etablering med Azure AD måste du lägga till RingCentral från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till RingCentral från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **RingCentral**, väljer **RingCentral** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![RingCentral i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ringcentral"></a>Konfigurera automatisk användar etablering till RingCentral 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i RingCentral baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för RingCentral genom att följa anvisningarna i [självstudien om enkel inloggning med RingCentral](ringcentral-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

> [!NOTE]
> Mer information om RingCentral SCIM-slutpunkten finns i [RINGCENTRAL API Reference](https://developers.ringcentral.com/api-reference).

### <a name="to-configure-automatic-user-provisioning-for-ringcentral-in-azure-ad"></a>Konfigurera automatisk användar etablering för RingCentral i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **RingCentral**.

    ![RingCentral-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , in`https://platform.ringcentral.com/scim/v2` i **klient-URL**. Mata in **scim-autentiseringstoken** som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till RingCentral. Om anslutningen Miss lyckas kontrollerar du att RingCentral-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Save** (Spara).

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till RingCentral**.

    ![RingCentral användar mappningar](media/ringcentral-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till RingCentral i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i RingCentral för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![RingCentral-användarattribut](media/ringcentral-provisioning-tutorial/userattributes.png)

10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för RingCentral ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till RingCentral genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på RingCentral.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)

