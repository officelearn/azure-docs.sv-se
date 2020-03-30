---
title: 'Självstudiekurs: Konfigurera Samanage för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060532"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Samanage för automatisk användaretablering

Den här självstudien visar stegen för att utföra i Samanage och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och grupper till Samanage.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som är byggd ovanpå Azure AD-användarens etableringstjänst. Information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program (Software-as-a-Service) med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du har:

* En Azure AD-klientorganisation.
* En [Samanage-hyresgäst](https://www.samanage.com/pricing/) med professional-paketet.
* Ett användarkonto i Samanage med administratörsbehörighet.

> [!NOTE]
> Azure AD-etableringsintegreringen är beroende av [Samanage Rest API](https://www.samanage.com/api/). Det här API:et är tillgängligt för Samanage-utvecklare för konton med professional-paketet.

## <a name="add-samanage-from-the-azure-marketplace"></a>Lägg till Samanage från Azure Marketplace

Innan du konfigurerar Samanage för automatisk användaretablering med Azure AD lägger du till Samanage från Azure Marketplace i listan över hanterade SaaS-program.

Så här lägger du till Samanage från Marketplace.

1. I [Azure-portalen](https://portal.azure.com)väljer du **Azure Active Directory**i navigeringsfönstret till vänster .

    ![Ikon för Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Samanage** och väljer **Samanage** på resultatpanelen. Om du vill lägga till programmet väljer du **Lägg till**.

    ![Samanage i resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Tilldela användare till Samanage

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bestämmer du vilka användare eller grupper i Azure AD som behöver åtkomst till Samanage. Om du vill tilldela dessa användare eller grupper till Samanage följer du instruktionerna i [Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Viktiga tips för att tilldela användare till Samanage

*    Idag fylls Samanage-roller automatiskt och dynamiskt i Azure-portalgränssnittet. Innan du tilldelar Samanage-roller till användare kontrollerar du att en första synkronisering har slutförts mot Samanage för att hämta de senaste rollerna i din Samanage-klient.

*    Vi rekommenderar att du tilldelar en enda Azure AD-användare till Samanage för att testa din första automatiska konfiguration för användaretablering. Du kan tilldela ytterligare användare och grupper senare efter att testerna har slutförts.

*    När du tilldelar en användare till Samanage väljer du en giltig programspecifik roll, om sådan finns, i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Konfigurera automatisk användaretablering till Samanage

Det här avsnittet hjälper dig att konfigurera Azure AD-etableringstjänsten. Använd den för att skapa, uppdatera och inaktivera användare eller grupper i Samanage baserat på användar- eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för Samanage. Följ instruktionerna i [Samanage enda inloggningskurs .](samanage-tutorial.md) Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurera automatisk användaretablering för Samanage i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** > **Alla program** > **Samanage**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Samanage**.

    ![Samanage-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Samanage etablering](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du din Samanage-klientadress och **hemlig token**. **Tenant URL** Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Samanage. Om anslutningen misslyckas kontrollerar du att Ditt Samanage-konto har administratörsbehörighet och försöker igen.

    ![Samanage testanslutning](./media/samanage-provisioning-tutorial/provisioning.png)

6. I rutan **E-post för meddelanden** anger du den person eller grupps e-postadress för att få meddelanden om etableringsfel. Markera kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-post med Samanage-meddelande](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Välj **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Samanage**.

    ![Synkronisering av Samanage-användare](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till Samanage i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Samanage för uppdateringsåtgärder. Om du vill spara eventuella ändringar väljer du **Spara**.

    ![Samanage matchande användarattribut](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Om du vill aktivera gruppmappningar väljer du **Synkronisera Azure Active Directory-grupper till Samanage**under avsnittet **Mappningar** .

    ![Synkronisering av Samanage-grupp](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. Ange **Aktiverad** till **Ja** för att synkronisera grupper. Granska gruppattributen som synkroniseras från Azure AD till Samanage i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Samanage för uppdateringsåtgärder. Om du vill spara eventuella ändringar väljer du **Spara**.

    ![Samanage matchande gruppattribut](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Om du vill konfigurera omfångsfilter följer du instruktionerna i [självstudiekursen för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Samanage ändrar **du etableringsstatus** till **På**i avsnittet **Inställningar.**

    ![Status för samanage-etablering](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Definiera de användare eller grupper som du vill etablera till Samanage. I avsnittet **Inställningar** väljer du de värden du vill använda i **Scope**. När du väljer alternativet **Synkronisera alla användare och grupper** bör du tänka på de begränsningar som beskrivs i följande avsnitt "Anslutningsbegränsningar".

    ![Samanage Räckvidd](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. När du är redo att etablera väljer du **Spara**.

    ![Samanage Spara](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än senare synkroniseringar. De inträffar ungefär var 40:e minut så länge azure AD-etableringstjänsten körs. 

Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten. Rapporten beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Samanage.

Information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

Om du väljer alternativet **Synkronisera alla användare och grupper** och konfigurerar ett värde för attributet Samanage **roller,** måste värdet under rutan **Standardvärde om null (är valfritt)** uttryckas i följande format:

- {"displayName":"role"}, där rollen är det standardvärde du vill använda.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för företagsappar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
