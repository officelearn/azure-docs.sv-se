---
title: 'Självstudie: Konfigurera iProva för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till iProva.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 4edba747-242d-4795-9539-649f33af4c13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 320a3178448da93a04a332183ad5d34453f702ef
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897122"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>Självstudie: Konfigurera iProva för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i iProva och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till iProva.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En iProva-klient](https://www.iProva.com/)
* Ett användar konto i iProva med administratörs behörighet.

## <a name="assigning-users-to-iprova"></a>Tilldela användare till iProva

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till iProva. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till iProva genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-iprova"></a>Viktiga tips för att tilldela användare till iProva

* Vi rekommenderar att en enda Azure AD-användare tilldelas iProva för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till iProva måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-iprova-for-provisioning"></a>Konfigurera iProva för etablering

1. Logga in på din [iProva-administratörs konsol](https://www.iProva.com/). Gå till **gå till > program hantering**.

    ![iProva-administratörskonsolen](media/iprova-provisioning-tutorial/admin.png)

2.  Klicka på **hantering av extern användare**.

    ![iProva Lägg till SCIM](media/iprova-provisioning-tutorial/external.png)

3. Om du vill lägga till en ny provider klickar du på **plus** ikonen. Ange en **rubrik**i dialog rutan ny **Lägg till provider** . Du kan välja att lägga till **IP-baserad åtkomst begränsning**. Klicka på **OK** -knappen.

    ![iProva Lägg till ny](media/iprova-provisioning-tutorial/add.png)

    ![iProva Lägg till Provider](media/iprova-provisioning-tutorial/addprovider.png)

4.  Klicka på **permanent token** -knapp. Kopiera den **permanenta token** och spara den eftersom det är den enda gången du kan visa den. Det här värdet anges i fältet Hemlig token på fliken etablering i ditt iProva-program i Azure Portal.

    ![Skapa token för iProva](media/iprova-provisioning-tutorial/token.png)

## <a name="add-iprova-from-the-gallery"></a>Lägga till iProva från galleriet

Innan du konfigurerar iProva för automatisk användar etablering med Azure AD måste du lägga till iProva från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till iProva från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **iProva**, väljer **iProva** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![iProva i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-iprova"></a>Konfigurera automatisk användar etablering till iProva 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i iProva baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för iProva genom att följa anvisningarna i [självstudien om enkel inloggning med iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iProva-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>Konfigurera automatisk användar etablering för iProva i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **iProva** i programlistan.

    ![iProva-länken i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , in`https://identitymanagement.services.iProva.nl/scim` i **klient-URL**. Mata in det **permanenta token** -värdet som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till iProva. Om anslutningen Miss lyckas kontrollerar du att iProva-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Save** (Spara).

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till iProva**.

    ![iProva användar mappningar](media/iprova-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till iProva i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i iProva för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![iProva-användarattribut](media/iprova-provisioning-tutorial/userattributes.png)

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till iProva**.

    ![iProva grupp mappningar](media/iprova-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till iProva i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i iProva för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![iProva grupp-attribut](media/iprova-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för iProva ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till iProva genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på iProva.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)

