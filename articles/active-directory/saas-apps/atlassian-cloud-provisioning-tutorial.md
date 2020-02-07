---
title: 'Självstudie: Konfigurera Atlassian Cloud för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Atlassian-molnet.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059376"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Atlassian Cloud för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Atlassian-molnet och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Atlassian-molnet.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Atlassian Cloud-klient](https://www.atlassian.com/licensing/cloud)
* Ett användar konto i Atlassian-moln med administratörs behörighet.

> [!NOTE]
> Azure AD Provisioning-integreringen är beroende av **Atlassian Cloud scim-API: et**, som är tillgängligt för Atlassian moln team.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Lägg till Atlassian-moln från galleriet

Innan du konfigurerar Atlassian Cloud för automatisk användar etablering med Azure AD måste du lägga till Atlassian-molnet från Azure AD-programgalleriet till listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Atlassian-moln från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Atlassian Cloud**, väljer **Atlassian Cloud** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Atlassian Cloud i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Tilldela användare till Atlassian-molnet

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Atlassian-molnet. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Atlassian-molnet genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Viktiga tips för att tilldela användare till Atlassian-molnet

* Vi rekommenderar att en enda Azure AD-användare tilldelas Atlassian-molnet för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Atlassian-molnet måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Konfigurera automatisk användar etablering till Atlassian-molnet 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Atlassian-molnet baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Atlassian-molnet genom att följa anvisningarna i [självstudien om Atlassian Cloud Single Sign-on](atlassian-cloud-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Konfigurera automatisk användar etablering för Atlassian Cloud i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **företags program**, Välj **alla program**och välj sedan **Atlassian Cloud**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Atlassian Cloud**.

    ![Atlassian Cloud-länken i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Atlassian Cloud-etablering](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Atlassian Cloud-etablering](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Navigera till [Atlassian Organization Manager](https://admin.atlassian.com) **> Välj org >-katalogen**.

    ![Atlassian Cloud-etablering](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Klicka på **användar etablering** och klicka på **skapa en katalog**. Kopiera **URL: en för katalog basen** och **Bearer-token** till **klient-URL:** en och fältet för **hemliga token** .

    ![Atlassian Cloud etablering](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud etableringen](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Atlassian Cloud etableringen](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Under avsnittet **admin credentials** måste du skriva in **klient-URL: en** och den **hemliga token** för ditt Atlassian-molns konto. Exempel på dessa värden är:

   * I fältet **klient-URL** fyller du i den angivna klient slut punkten som du tar emot från Atlassian, enligt beskrivningen i steg 6. Exempel: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * I fältet **hemlig token** fyller du i den hemliga token enligt beskrivningen i steg 6.

8. När du fyller i fälten som visas i steg 7 klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till Atlassian-molnet. Om anslutningen Miss lyckas kontrollerar du att ditt Atlassian Cloud-konto har administratörs behörighet och försöker igen.

    ![Atlassian Cloud-etablering](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![Atlassian Cloud-etablering](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Klicka på **Save** (Spara).

11. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Atlassian-molnet**.

    ![Atlassian Cloud-etablering](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Granska de användarattribut som synkroniseras från Azure AD till Atlassian-molnet i avsnittet **Mappning av attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona i Atlassian-molnet för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Atlassian Cloud-etablering](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Atlassian-molnet**.

    ![Atlassian Cloud-etablering](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Granska gruppattributen som synkroniseras från Azure AD till Atlassian-molnet i avsnittet **Mappning av attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha grupperna i Atlassian-molnet för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Atlassian Cloud-etablering](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

16. Om du vill aktivera Azure AD Provisioning-tjänsten för Atlassian-molnet ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Atlassian Cloud-etablering](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Definiera de användare och/eller grupper som du vill etablera till Atlassian Cloud genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Atlassian Cloud-etablering](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. När du är redo att etablera klickar du på **Spara**.

    ![Atlassian Cloud-etablering](./media/atlassian-cloud-provisioning-tutorial/save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Atlassian-molnet.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Atlassian-molnet tillåter endast etablering av användare från [verifierade domäner](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian-molnet har inte stöd för grupp namn idag. Det innebär att alla ändringar av displayName för en grupp i Azure AD inte kommer att uppdateras och avspeglas i Atlassian-molnet.
* Värdet för attributet **e-** postanvändare i Azure AD är bara ifyllt om användaren har en Microsoft Exchange-postlåda. Om användaren inte har en, rekommenderar vi att du mappar ett annat önskat attribut till attributet **e-post** i Atlassian-molnet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png