---
title: 'Självstudier: Konfigurera Insight4GRC för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Insight4GRC.
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
ms.openlocfilehash: 7e8c6c2277f29fc114033aac24844e9e85816b78
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951041"
---
# <a name="tutorial-configure-insight4grc--for-automatic-user-provisioning"></a>Självstudier: Konfigurera Insight4GRC för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Insight4GRC och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Insight4GRC.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En Insight4GRC-klient](https://www.rsmuk.com/).
* Ett användar konto i Insight4GRC med administratörs behörighet.

## <a name="assigning-users-to-insight4grc"></a>Tilldela användare till Insight4GRC 

Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Insight4GRC. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Insight4GRC genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-insight4grc"></a>Viktiga tips för att tilldela användare till Insight4GRC 

* Vi rekommenderar att en enda Azure AD-användare tilldelas Insight4GRC för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Insight4GRC måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-insight4grc-for-provisioning"></a>Konfigurera Insight4GRC för etablering

Innan du konfigurerar Insight4GRC för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på Insight4GRC.

1. För att erhålla Bearer-token måste kunden kontakta [support teamet](mailto:support.ss@rsmuk.com) och tillhandahålla token-token till kunderna.

2. För att hämta slut punkts-URL: en för SCIM måste du ha ditt Insight4GRC-domännamn klart eftersom det används för att skapa din SCIM-slutpunkts-URL. Du kan hämta ditt Insight4GRC domän namn som en del av den första program varu köpet med Insight4GRC.


## <a name="add-insight4grc--from-the-gallery"></a>Lägg till Insight4GRC från galleriet

Om du vill konfigurera Insight4GRC för automatisk användar etablering med Azure AD måste du lägga till Insight4GRC från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Insight4GRC från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Insight4GRC**, väljer **Insight4GRC** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Insight4GRC i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-insight4grc"></a>Konfigurera automatisk användar etablering till Insight4GRC  

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Insight4GRC baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Insight4GRC genom att följa anvisningarna i självstudien om [Insight4GRC-enkel inloggning](insight4grc-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-insight4grc--in-azure-ad"></a>Konfigurera automatisk användar etablering för Insight4GRC i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Insight4GRC**.

    ![Insight4GRC-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5.  Under avsnittet admin credentials, inmatat `https://{Insight4GRC Domain Name}.insight4grc.com/public/api/scim/v2` i **klient webb adressen** med värdet {Insight4GRC Domain Name} som hämtades tidigare. Ange det **token-värde** som hämtades tidigare till **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Insight4GRC. Om anslutningen Miss lyckas kontrollerar du att Insight4GRC-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-post för aviseringar](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Insight4GRC**.

    ![Insight4GRC – användar mappningar](media/insight4grc-provisioning-tutorial/usermapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Insight4GRC i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Insight4GRC för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Insight4GRC – användar mappningar](media/insight4grc-provisioning-tutorial/userattribute.png)

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupp till Insight4GRC**

    ![Insight4GRC-grupp mappningar](media/insight4grc-provisioning-tutorial/groupmapping.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Insight4GRC i avsnittet **Mappning av attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha grupp kontona i Insight4GRC för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Insight4GRC-grupp mappningar](media/insight4grc-provisioning-tutorial/groupattribute.png)

10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för Insight4GRC ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Insight4GRC genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Insight4GRC.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md).