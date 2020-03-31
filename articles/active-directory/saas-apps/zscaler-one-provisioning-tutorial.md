---
title: 'Självstudiekurs: Konfigurera Zscaler One för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Zscaler One.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 87413932acee576934ee50b59546371b03ceaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064180"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Zscaler One för automatisk etablering av användare

Den här självstudien visar stegen för att utföra i Zscaler One och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och grupper till Zscaler One.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som är byggd ovanpå Azure AD-användarens etableringstjänst. Information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program (Software-as-a-Service) med Azure Active Directory](../active-directory-saas-app-provisioning.md).


## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du har:

* En Azure AD-klientorganisation.
* En Zscaler En hyresgäst.
* Ett användarkonto i Zscaler One med administratörsbehörighet.

> [!NOTE]
> Azure AD-etableringsintegreringen är beroende av Zscaler One SCIM API. Det här API:et är tillgängligt för Zscaler One-utvecklare för konton med Enterprise-paketet.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Lägg till Zscaler One från Azure Marketplace

Innan du konfigurerar Zscaler One för automatisk användaretablering med Azure AD lägger du till Zscaler One från Azure Marketplace i listan över hanterade SaaS-program.

Så här lägger du till Zscaler One från Marketplace.

1. I [Azure-portalen](https://portal.azure.com)väljer du **Azure Active Directory**i navigeringsfönstret till vänster .

    ![Ikon för Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Zscaler One** och väljer **Zscaler One** på resultatpanelen. Om du vill lägga till programmet väljer du **Lägg till**.

    ![Zscaler En i resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Tilldela användare till Zscaler One

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bestämmer du vilka användare eller grupper i Azure AD som behöver åtkomst till Zscaler One. Om du vill tilldela dessa användare eller grupper till Zscaler One följer du instruktionerna i [Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Viktiga tips för att tilldela användare till Zscaler One

* Vi rekommenderar att du tilldelar en enda Azure AD-användare till Zscaler One för att testa konfigurationen för automatisk användaretablering. Du kan tilldela ytterligare användare eller grupper senare.

* När du tilldelar en användare till Zscaler One väljer du en giltig programspecifik roll, om sådan finns, i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Konfigurera automatisk användaretablering till Zscaler One

Det här avsnittet hjälper dig att konfigurera Azure AD-etableringstjänsten. Använd den för att skapa, uppdatera och inaktivera användare eller grupper i Zscaler One baserat på användar- eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för Zscaler One. Följ instruktionerna i [Zscaler One enda inloggningskurs .](zscaler-One-tutorial.md) Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Konfigurera automatisk användaretablering för Zscaler One i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** > **Alla program** > **Zscaler One**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Zscaler One**i programlistan .

    ![Zscaler One-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Zscaler En etablering](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Etablerarläge för Zscaler one](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** fyller du i **rutorna Klient-URL** och **Hemlig token** med inställningarna för ditt Zscaler One-konto enligt beskrivningen i steg 6.

6. Om du vill hämta klient-URL:en och den hemliga token går du till Inställningar för **administrationsautentisering** > **Authentication Settings** i portalgränssnittet Zscaler One. Under **Autentiseringstyp**väljer du **SAML**.

    ![Zscaler One autentiseringsinställningar](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Välj **Konfigurera SAML** för att öppna **alternativen Konfigurera SAML.**

    ![Zscaler One Konfigurera SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Välj **Aktivera SCIM-baserad etablering** för att hämta inställningarna i **bas-URL** och **innehavartoken**. Spara sedan inställningarna. Kopiera inställningen **Bas-URL** till **klient-URL:en** i Azure-portalen. Kopiera inställningen **Bärare token** till **hemlig token** i Azure-portalen.

7. När du har fyllt i rutorna som visas i steg 5 väljer du **Testa anslutning** för att se till att Azure AD kan ansluta till Zscaler One. Om anslutningen misslyckas kontrollerar du att ditt Zscaler One-konto har administratörsbehörighet och försöker igen.

    ![Zscaler One testanslutning](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. I rutan **E-post för meddelanden** anger du den person eller grupps e-postadress för att få meddelanden om etableringsfel. Markera kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-postmeddelande till Zscaler Ett](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Välj **Spara**.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Zscaler One**.

    ![Zscaler En användarsynkronisering](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Granska användarattributen som synkroniseras från Azure AD till Zscaler One i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Zscaler One för uppdateringsåtgärder. Om du vill spara eventuella ändringar väljer du **Spara**.

    ![Zscaler Ett matchande användarattribut](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till Zscaler One**.

    ![Zscaler En gruppsynkronisering](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Zscaler One i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Zscaler One för uppdateringsåtgärder. Om du vill spara eventuella ändringar väljer du **Spara**.

    ![Zscaler Ett matchande gruppattribut](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Om du vill konfigurera omfångsfilter följer du instruktionerna i [självstudiekursen för omfångsfilter](./../active-directory-saas-scoping-filters.md).

15. Om du vill aktivera Azure AD-etableringstjänsten för Zscaler One ändrar **du etableringsstatus** till **På**i avsnittet **Inställningar.**

    ![Zscaler One-etableringsstatus](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definiera de användare eller grupper som du vill etablera till Zscaler One. I avsnittet **Inställningar** väljer du de värden du vill använda i **Scope**.

    ![Zscaler Ett-scope](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. När du är redo att etablera väljer du **Spara**.

    ![Zscaler En Spara](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än senare synkroniseringar. De inträffar ungefär var 40:e minut så länge azure AD-etableringstjänsten körs. 

Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten. Rapporten beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Zscaler One.

Information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för företagsappar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
