---
title: 'Självstudie: Konfigurera snö för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till snö flingor.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2c5d91894ba35233f3fbebffdff9104edcfdd27b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063172"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Självstudie: Konfigurera snö för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i snö och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till snö flingor.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En snö flingor-klient](https://www.Snowflake.com/pricing/).
* Ett användar konto i snö med administratörs behörighet.

## <a name="assigning-users-to-snowflake"></a>Tilldela användare till snö flingor

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till snö flingor. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till snö flingor genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Viktiga tips för att tilldela användare till snö flingor

* Vi rekommenderar att en enda Azure AD-användare tilldelas till snö för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till snö flingor måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-snowflake-for-provisioning"></a>Konfigurera snö flingor för etablering

Innan du konfigurerar snö flingor för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på snö flingor.

1. Logga in på din snö-administratörskonsolen. Ange frågan som visas nedan i kalkyl bladet markerat och klicka på **Kör**.

    ![Snö administrations konsol](media/Snowflake-provisioning-tutorial/image00.png)

2.  En SCIM-åtkomsttoken skapas för din snö flingor-klient. Hämta det genom att klicka på länken som marker ATS nedan.

    ![Snö, Lägg till SCIM](media/Snowflake-provisioning-tutorial/image01.png)

3. Kopiera det genererade värdet för token och klicka på **Slutför**. Det här värdet anges i fältet **hemlig token** på fliken etablering i ditt snö flingor-program i Azure Portal.

    ![Snö, Lägg till SCIM](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Lägg till snö flingor från galleriet

Om du vill konfigurera snö för automatisk användar etablering med Azure AD måste du addSnowflake från Azure AD-programgalleriet till listan över hanterade SaaS-program.

**Utför följande steg för att lägga till snö flingor från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök anger du **snö**, väljer **snö** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Snowflake i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Konfigurera automatisk användar etablering till snö flingor 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i snö flingor baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för snö flingor genom att följa anvisningarna i [själv studie kursen om enkel inloggning i snö](Snowflake-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Konfigurera automatisk användar etablering för snö flingor i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **snö flingor**.

    ![Snowflake-länken i listan med program](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet admin credentials, in`https://<Snowflake Account URL>/scim/v2` i klient-URL. Ett exempel på klient-URL: en: `https://acme.snowflakecomputing.com/scim/v2`

6. Mata in **scim-autentiseringstoken** som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till snö flingor. Om anslutningen Miss lyckas kontrollerar du att ditt snö-konto har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

8. Klicka på **Save** (Spara).

9. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till snö flingor**.

    ![Användar mappningar för snö flingor](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Granska de användarattribut som synkroniseras från Azure AD till snö flingor i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i snö för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Snö användarattribut](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till snö flingor**.

    ![Mappningar av snö grupper](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Granska gruppattributen som synkroniseras från Azure AD till snö flingor i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i snö för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Egenskaper för snö och samma grupp](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Om du vill aktivera Azure AD Provisioning-tjänsten för snö, ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

15. Definiera de användare och/eller grupper som du vill etablera till snö flingor genom att välja önskade värden i **omfång** i avsnittet **Inställningar** . Om det här alternativet inte är tillgängligt konfigurerar du de obligatoriska fälten under admin-autentiseringsuppgifter, klickar på **Spara** och uppdaterar sidan. 

    ![Etablerings omfång](common/provisioning-scope.png)

16. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på snö flingor.

    Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Kopplings begränsningar

* De snö SCIM token som skapats upphör att gälla om 6 månader. Tänk på att dessa måste uppdateras innan de upphör att gälla för att slutföra etableringen av synkroniseringen. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md).
