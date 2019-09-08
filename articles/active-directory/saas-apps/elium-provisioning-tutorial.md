---
title: 'Självstudier: Konfigurera Elium för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Elium.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: 30f6f81234ce34e40a29a04f6b795aab991a8824
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802867"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Elium för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Elium och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Elium.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En Elium-klient](https://www.elium.com/pricing/)
* Ett användar konto i Elium med administratörs behörighet.

## <a name="assigning-users-to-elium"></a>Tilldela användare till Elium

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Elium. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Elium genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-elium"></a>Viktiga tips för att tilldela användare till Elium 

* Vi rekommenderar att en enda Azure AD-användare tilldelas Elium för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Elium måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-elium-for-provisioning"></a>Konfigurera Elium för etablering

Innan du konfigurerar Elium för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på Elium.

1. Logga in på Elium. Gå till **Mina profil** > **Inställningar**.

    ![Elium](media/Elium-provisioning-tutorial/setting.png)

2. I det nedre vänstra hörnet under avancerad val av **säkerhet**.

    ![Elium](media/Elium-provisioning-tutorial/security.png)

3. Kopiera den **hemliga token**. Det här värdet anges i fältet **hemlig token** på fliken etablering i ditt Elium-program i Azure Portal.

    ![Elium](media/Elium-provisioning-tutorial/token.png)


## <a name="add-elium--from-the-gallery"></a>Lägg till Elium från galleriet

Om du vill konfigurera Elium för automatisk användar etablering med Azure AD måste du lägga till Elium från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Elium från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Elium**, väljer **Elium** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Elium i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-elium"></a>Konfigurera automatisk användar etablering till Elium  

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Elium baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Elium genom att följa anvisningarna i [självstudien om enkel inloggning med Elium](Elium-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om de här två funktionerna är på varandra

### <a name="to-configure-automatic-user-provisioning-for-elium--in-azure-ad"></a>Konfigurera automatisk användar etablering för Elium i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Elium**.

    ![Elium-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet admin credentials (admin `<tenantURL>/scim/v2` credentials) är InInformationen där **{TeanantURL}** det värde som hämtades tidigare från administratörs konsolen för Elium. Mata in ett **hemligt token** -värde i **hemlig token** Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Elium. Om anslutningen Miss lyckas kontrollerar du att Elium har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-post för aviseringar](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Elium**.

    ![Elium användar mappningar](media/Elium-provisioning-tutorial/usermapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Elium i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Elium för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Elium-användarattribut](media/Elium-provisioning-tutorial/userattribute.png)


11. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD Provisioning-tjänsten för Elium ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

13. Definiera de användare och/eller grupper som du vill etablera till Elium genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

14. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information om hur lång tid det tar för användare och/eller grupper att etablera finns i [hur lång tid det tar att etablera användare](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Du kan använda avsnittet **aktuell status** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Elium. Mer information finns i [kontrol lera status för användar etablering](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). För att läsa Azure AD-etablerings loggarna, se [rapportering om automatisk etablering av användar konton](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
