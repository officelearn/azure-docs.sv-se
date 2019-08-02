---
title: 'Självstudier: Konfigurera Fuze för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Fuze.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 34718201-4f0e-4260-9af0-b3b70a1e8265
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 937ed160a6a4ee1e08070abd177bd5853f35e9e4
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602157"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Fuze för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Fuze och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Fuze.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En Fuze-klient](https://www.fuze.com/).
* Ett användar konto i Fuze med administratörs behörighet.

## <a name="assigning-users-to-fuze"></a>Tilldela användare till Fuze

Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Fuze. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Fuze genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-fuze"></a>Viktiga tips för att tilldela användare till Fuze

* Vi rekommenderar att en enda Azure AD-användare tilldelas Fuze för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Fuze måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-fuze-for-provisioning"></a>Konfigurera Fuze för etablering

Innan du konfigurerar Fuze för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på Fuze. 

1. Börja med att kontakta din Fuze-representant för följande nödvändiga information:

    * Lista över Fuze produkt-SKU: er som används i ditt företag.
    * Lista över plats koder för företagets platser.
    * Lista över avdelnings koder för ditt företag.

2. Du hittar dessa SKU: er och koder i ditt Fuze-kontrakt och konfigurations dokument, eller genom att kontakta din Fuze-representant.

3. När kraven har tagits emot tillhandahåller Fuze-representanten den Fuze som krävs för att aktivera integreringen. Det här värdet anges i fältet Hemlig token på fliken etablering i ditt Fuze-program i Azure Portal.

## <a name="add-fuze-from-the-gallery"></a>Lägg till FUZE från galleriet

Innan du konfigurerar Fuze för automatisk användar etablering med Azure AD måste du lägga till FUZE från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till FUZE från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Fuze**, väljer **Fuze** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Fuze i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-fuze"></a>Konfigurera automatisk användar etablering till Fuze 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Fuze baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Fuze genom att följa anvisningarna i självstudien om [enkel inloggning med Fuze](fuze-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Konfigurera automatisk användar etablering för Fuze i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Fuze**.

    ![Länken för Fuze i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://api.fuze.com/scim/v2` i klient- **URL**. Mata in värdet för **scim-autentiseringstoken** som hämtades tidigare från Fuze-representanten i den **hemliga token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Fuze. Om anslutningen Miss lyckas kontrollerar du att Fuze-kontot har administratörs behörighet och försöker igen.

    ![Klientens URL-token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-post för aviseringar](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Fuze**.

    ![Fuze användar mappningar](media/fuze-provisioning-tutorial/image01.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Fuze i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Fuze för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Fuze användar mappningar](media/fuze-provisioning-tutorial/image00.png)

10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för Fuze ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Fuze genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Fuze.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Fuze stöder anpassade SCIM-attribut som kallas **rättigheter**. Dessa attribut kan bara skapas och uppdateras inte. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md).