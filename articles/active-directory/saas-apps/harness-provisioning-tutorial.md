---
title: 'Självstudie: Konfigurera ett nät för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till att utnyttja.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 34d05d6392e00757bf1e5562ffd8341ad04cc9dc
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807763"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Självstudie: Konfigurera ett nät för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i nät och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper för att utnyttja.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En nät klient](https://harness.io/pricing/)
* Ett användar konto i ett nät med administratörs behörighet.

## <a name="assigning-users-to-harness"></a>Tilldela användare till nät

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till-nät. När du har bestämt dig kan du tilldela dessa användare och/eller grupper för att få hjälp genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-harness"></a>Viktiga tips för att tilldela användare till-nätet

* Vi rekommenderar att en enda Azure AD-användare tilldelas till ett nät för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till att utnyttja måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-harness-for-provisioning"></a>Konfigurera ett nät för etablering

1. Logga in på din- [administrations konsol](https://app.harness.io/#/login). Navigera till **> åtkomst hantering för kontinuerlig säkerhet**.

    ![Nät administrations konsol](media/harness-provisioning-tutorial/admin.png)

2.  Klicka på **API-nycklar**.

    ![Lägg till SCIM](media/harness-provisioning-tutorial/apikeys.png)

3. Klicka på **Lägg till ny nyckel**. I dialog rutan **Lägg till API-nyckel** anger du ett **namn** och väljer ett alternativ från **behörigheter som ärvts från** List menyn. Klicka på knappen **Skicka** .

    ![Lägg till ny nyckel](media/harness-provisioning-tutorial/addkey.png)

    ![Dialog rutan Lägg till ny nyckel](media/harness-provisioning-tutorial/title.png)

3.  Kopiera **nyckeln**. Det här värdet anges i fältet Hemlig token på fliken etablering i ditt program Azure Portal i ditt program i programmet.

    ![Skapa token för nätet](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Lägg till ett nät från galleriet

Innan du konfigurerar ett nät för automatisk användar etablering med Azure AD måste du lägga till ett nät från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till ett nät från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **nät**, väljer **nät** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Nät i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-harness"></a>Konfigurera automatisk användar etablering för att utnyttja 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i ett nät baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för ett nät genom att följa anvisningarna i [självstudien om enkel inloggning med enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra

> [!NOTE]
> Mer information om nät SCIM-slutpunkten finns [i](https://docs.harness.io/article/smloyragsm-api-keys)

### <a name="to-configure-automatic-user-provisioning-for-harness-in-azure-ad"></a>Konfigurera automatisk användar etablering för nät i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **nät**.

    ![Länken nät i listan program](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , in`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw` i **klient-URL**. Mata in **scim-autentiseringstoken** som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till nätet. Om anslutningen Miss lyckas ser du till att ditt konto för ditt nätverkskort har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Save** (Spara).

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare att använda**.

    ![Användar mappningar för användare](media/harness-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till nät i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar konton i drift för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Använd användarattribut](media/harness-provisioning-tutorial/userattributes.png)

10. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper som ska**hanteras.

    ![Grupp mappningar för grupp](media/harness-provisioning-tutorial/groupmappings.png)

11. Granska de Gruppattribut som synkroniseras från Azure AD till-nät i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i-funktioner för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Attribut för att gruppera](media/harness-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för-nät ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera för att utnyttja genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på nätet.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
