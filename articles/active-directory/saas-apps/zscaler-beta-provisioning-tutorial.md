---
title: 'Självstudiekurs: Konfigurera Zscaler Beta för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Zscaler Beta.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 83db6b8d-503b-48f3-b918-f9fba1369d53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 505cd7a3350c937885f0f03268ef326a4f784258
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062760"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Zscaler Beta för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Zscaler Beta och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Zscaler Beta.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../active-directory-saas-app-provisioning.md).
>


## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

* En Azure AD-klient
* En Zscaler Beta-klient
* Ett användarkonto i Zscaler Beta med administratörsbehörighet

> [!NOTE]
> Azure AD-etableringsintegreringen är beroende av Zscaler Beta SCIM API, som är tillgängligt för Zscaler Beta-utvecklare för konton med Enterprise-paketet.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Lägga till Zscaler Beta från galleriet

Innan du konfigurerar Zscaler Beta för automatisk användaretablering med Azure AD måste du lägga till Zscaler Beta från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Zscaler Beta från Azure AD-programgalleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Zscaler Beta**, väljer **Zscaler Beta** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

    ![Zscaler Beta i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Tilldela användare till Zscaler Beta

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Zscaler Beta. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Zscaler Beta genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Viktiga tips för att tilldela användare till Zscaler Beta

* Vi rekommenderar att en enda Azure AD-användare tilldelas Zscaler Beta för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Zscaler Beta måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Konfigurera automatisk användaretablering till Zscaler Beta

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Zscaler Beta baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Zscaler Beta, enligt instruktionerna i [Zscaler Beta enda inloggningskurs .](zscaler-beta-tutorial.md) Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Zscaler Beta i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com) och välj **Enterprise Applications**, välj **Alla program**och välj sedan **Zscaler Beta**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Zscaler Beta**i programlistan .

    ![Zscaler Beta-länk i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Zscaler Beta-etablering](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Zscaler Beta-etablering](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du **klient-URL:en** och **hemlig token** för ditt Zscaler Beta-konto enligt beskrivningen i steg 6.

6. Om du vill hämta **klient-URL:en** och **hemlig token**navigerar du till Administration **> autentiseringsinställningar** i Zscaler Beta-portalens användargränssnitt och klickar på **SAML** under **Autentiseringstyp**.

    ![Zscaler Beta-etablering](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Klicka på **Konfigurera SAML** för att öppna **saml-alternativen för konfiguration.**

    ![Zscaler Beta-etablering](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Välj **Aktivera SCIM-baserad etablering** för att hämta **bas-URL** och **bärare token**och spara sedan inställningarna. Kopiera **bas-URL:en** till **klient-URL**och **innehavartoken** till **hemlig token** i Azure-portalen.

7. När du fyller i fälten som visas i steg 5 klickar du på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Zscaler Beta. Om anslutningen misslyckas kontrollerar du att ditt Zscaler Beta-konto har administratörsbehörighet och försöker igen.

    ![Zscaler Beta-etablering](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![Zscaler Beta-etablering](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Klicka på **Spara**.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Zscaler Beta**.

    ![Zscaler Beta-etablering](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Granska användarattributen som synkroniseras från Azure AD till Zscaler Beta i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Zscaler Beta för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Zscaler Beta-etablering](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till Zscaler Beta**.

    ![Zscaler Beta-etablering](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Zscaler Beta i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Zscaler Beta för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Zscaler Beta-etablering](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](./../active-directory-saas-scoping-filters.md).

15. Om du vill aktivera Azure AD-etableringstjänsten för Zscaler Beta ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Zscaler Beta-etablering](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Definiera de användare och/eller grupper som du vill etablera till Zscaler Beta genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Zscaler Beta-etablering](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Zscaler Beta-etablering](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Zscaler Beta.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
