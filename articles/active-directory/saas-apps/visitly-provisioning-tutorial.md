---
title: 'Självstudier: Konfigurera besök för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton för att gå vidare.
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
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 381f9cb2cb9ef196149144d40332a1de3b90f188
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802763"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Självstudier: Konfigurera besök för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och/eller grupper till gång.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En besöks klient](https://www.visitly.io/pricing/)
* Ett användar konto i på gång med administratörs behörighet.

## <a name="assigning-users-to-visitly"></a>Tilldela användare till gång 

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver ha åtkomst till på en gång. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till dem genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Viktiga tips för att tilldela användare till gång 

* Vi rekommenderar att en enda Azure AD-användare tilldelas för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till gång måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-visitly-for-provisioning"></a>Installations programmet för etablering

Innan du konfigurerar för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på gång.

1. Logga in i [på](https://app.visitly.io/login)gång. Klicka på synkronisering av **integrerings** > **värden**.

    ![Visitly](media/Visitly-provisioning-tutorial/login.png)

2. Rulla nedåt Välj **Azure AD-avsnitt**

    ![Visitly](media/Visitly-provisioning-tutorial/integration.png)

3. Kopiera **API-nyckeln**. De här värdena anges i fältet **hemligt token** på fliken etablering i appens besöks program i Azure Portal.

    ![Visitly](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Lägg till besök från galleriet

Om du vill konfigurera för automatisk användar etablering med Azure AD måste du lägga till besök från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till besök från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan går du till **besök**, väljer **besök** i panelen resultat och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Finns i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-visitly"></a>Konfigurera automatisk användar etablering till att gå på gång  

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i på gång baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för i förväg genom att följa anvisningarna i [själv studie kursen om enkel inloggning](Visitly-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om de här två funktionerna är på varandra

### <a name="to-configure-automatic-user-provisioning-for-visitly--in-azure-ad"></a>Konfigurera automatisk användar etablering för besök i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Visitly**.

    ![Länken på gång i listan program](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet admin ` https://api.visitly.io/v1/usersync/SCIM` credentials måste du skriva in **API-nyckelvärdena** som hämtades tidigare i klient- **URL** respektive **hemlig token** . Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till den. Om anslutningen Miss lyckas ser du till att ditt konto för besöks kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-post för aviseringar](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare att besöka**.

    ![Användar mappningar på användar Sidan](media/visitly-provisioning-tutorial/usermapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD och finns i avsnittet mappning av **attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona i för gång för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Besöks användar-attribut](media/visitly-provisioning-tutorial/userattribute.png)



10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för besök kan du ändra **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information om hur lång tid det tar för användare och/eller grupper att etablera finns i [hur lång tid det tar att etablera användare](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Du kan använda avsnittet **aktuell status** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på besök. Mer information finns i [kontrol lera status för användar etablering](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). För att läsa Azure AD-etablerings loggarna, se [rapportering om automatisk etablering av användar konton](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

Det finns inte stöd för hårda borttagningar på sidan, allt är endast mjuk borttagning.  

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
