---
title: 'Självstudier: Konfigurera en Zscaler för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till en Zscaler.
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
ms.author: v-ant-msft
ms.openlocfilehash: 5319b0ac06c4ddf1a7627a4e7fe0bfb2694f79f6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114621"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Självstudier: Konfigurera en Zscaler för automatisk användaretablering

Den här kursen visar hur du utför i en Zscaler och Azure Active Directory (Azure AD) att konfigurera Azure AD att automatiskt etablera och avetablera användare och grupper till ett Zscaler.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-tjänst för användaretablering. Information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns [automatisera användaretablering och avetablering för software-as-a-service (SaaS)-program med Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Den här anslutningsappen är för närvarande finns som förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du har:

* En Azure AD-klientorganisation.
* En Zscaler en klient.
* Ett användarkonto i en Zscaler med administratörsbehörighet.

> [!NOTE]
> Azure AD etablering integration är beroende av Zscaler-API: et för en SCIM. Detta API är tillgängliga för Zscaler en utvecklare för konton med Enterprise-paketet.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Lägg till Zscaler en från Azure Marketplace

Innan du konfigurerar en Zscaler för automatisk användarförsörjning med Azure AD lägger du till en Zscaler från Azure Marketplace till din lista över hanterade SaaS-program.

Följ dessa steg för att lägga till en Zscaler från Marketplace.

1. I den [Azure-portalen](https://portal.azure.com), i navigeringsfönstret till vänster, Välj **Azure Active Directory**.

    ![Azure Active Directory-ikonen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Zscaler en** och välj **Zscaler en** från panelen resultatet. Om du vill lägga till programmet, Välj **Lägg till**.

    ![En Zscaler i resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Tilldela användare till en Zscaler

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras de användare eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, kan du bestämma vilka användare eller grupper i Azure AD behöver åtkomst till en Zscaler. Om du vill tilldela dessa användare eller grupper till en Zscaler, följer du anvisningarna i [tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Viktiga tips för att tilldela användare till en Zscaler

* Vi rekommenderar att du tilldelar en enda Azure AD-användare till en Zscaler att testa konfigurationen för automatisk användaretablering. Du kan tilldela ytterligare användare eller grupper senare.

* När du tilldelar en användare till en Zscaler, Välj en giltig programspecifika roll om det finns i dialogrutan tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Konfigurera automatisk användaretablering för ett Zscaler

Det här avsnittet vägleder dig genom stegen för att konfigurera den Azure AD-etableringstjänsten. Du kan använda den för att skapa, uppdatera och inaktivera användare eller grupper i Zscaler en baserat på användar- eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för en Zscaler. Följ instruktionerna i den [Zscaler en självstudie för enkel inloggning](zscaler-One-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Konfigurera automatisk användaretablering för ett Zscaler i Azure AD

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram** > **alla program** > **Zscaler en**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Zscaler en**.

    ![Zscaler en länk i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Zscaler en etablering](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Zscaler en Etableringsläge](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, fyller du i den **klient-URL** och **hemlighet Token** rutor med inställningarna för ditt Zscaler-One-konto som beskrivs i steg 6.

6. Om du vill hämta klientens URL och Hemlig token, gå till **Administration** > **autentiseringsinställningar** i en Zscaler portalens användargränssnitt. Under **autentiseringstyp**väljer **SAML**.

    ![Zscaler en autentiseringsinställningar](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Välj **konfigurera SAML** att öppna den **konfigurera SAML** alternativ.

    ![Zscaler som konfigurerar du SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Välj **Enable SCIM-Based etablering** att hämta inställningarna i **bas-URL** och **ägar-Token**. Spara inställningarna. Kopiera den **bas-URL** att ställa in **klient-URL** i Azure-portalen. Kopiera den **ägar-Token** att ställa in **hemlighet Token** i Azure-portalen.

7. När du har fyllt i rutorna som visas i steg 5 väljer **Testanslutningen** för att se till att Azure AD kan ansluta till en Zscaler. Om anslutningen misslyckas se till att din Zscaler ett konto som har administratörsbehörighet och försök igen.

    ![Zscaler en Testa anslutning](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. I den **e-postmeddelande** anger du e-postadressen för personen eller grupp för att ta emot meddelanden etablering fel. Välj den **skicka ett e-postmeddelande när ett fel inträffar** markerar du kryssrutan.

    ![Zscaler en e-postmeddelandet](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Välj **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till en Zscaler**.

    ![Zscaler en synkronisering av användare](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Granska användarattribut som ska synkroniseras från Azure AD med en Zscaler i den **attributmappningar** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i en Zscaler för uppdateringsåtgärder. Om du vill spara ändringarna, Välj **spara**.

    ![Zscaler en matchande användarattribut](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper till en Zscaler**.

    ![Synkronisering av Zscaler 1 grupp](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Granska Gruppattribut som synkroniseras från Azure AD till en Zscaler i den **attributmappningar** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha grupper i en Zscaler för uppdateringsåtgärder. Om du vill spara ändringarna, Välj **spara**.

    ![Zscaler en matchande grupp-attribut](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Om du vill konfigurera Omfångsfilter, följer du anvisningarna i den [målgrupp filter självstudien](./../active-directory-saas-scoping-filters.md).

15. Så här aktiverar du den Azure AD-etableringstjänsten för en Zscaler i den **inställningar** ändrar **Etableringsstatus** till **på**.

    ![Zscaler en Etableringsstatus](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definiera de användare eller grupper som önskas kan etableras på en Zscaler. I den **inställningar** väljer du de värden som du vill ha i **omfång**.

    ![Zscaler One Scope](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. När du är redo att etablera väljer **spara**.

    ![Zscaler One Save](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än senare synkroniseringar. De inträffar ungefär var 40 minut så länge den Azure AD-etableringtjänsten körs. 

Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följa länkarna till aktivitetsrapporten för etablering. Rapporten beskriver de åtgärder som utförs av den Azure AD-etableringtjänsten på Zscaler One.

Information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
