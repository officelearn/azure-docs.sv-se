---
title: 'Självstudier: Konfigurera Proxyclick för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Proxyclick.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: zchia
ms.openlocfilehash: 2117f481d213c14f8feeb23eb8af3670db81cab4
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276644"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Proxyclick för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Proxyclick och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Proxyclick.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningsappen är för närvarande i offentlig förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Proxyclick-klient](https://www.proxyclick.com/pricing)
* Ett användarkonto i Proxyclick med administratörsbehörighet.

## <a name="add-proxyclick-from-the-gallery"></a>Lägg till Proxyclick från galleriet

Du måste lägga till Proxyclick från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar Proxyclick för automatisk användarförsörjning med Azure AD.

**Utför följande steg för att lägga till Proxyclick från Azure AD-programgalleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Proxyclick**väljer **Proxyclick** i resultatrutan och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![Proxyclick i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Tilldela användare till Proxyclick

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Proxyclick. När du valt, kan du tilldela dessa användare och/eller grupper till Proxyclick genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Viktiga tips för att tilldela användare till Proxyclick

* Vi rekommenderar att en enda Azure AD-användare har tilldelats Proxyclick att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Proxyclick, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Konfigurera automatisk användaretablering för Proxyclick 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Proxyclick baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Proxyclick, följa anvisningarna enligt den [Proxyclick enkel inloggning för självstudien](proxyclick-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Konfigurera automatisk användaretablering för Proxyclick i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Proxyclick**.

    ![Länken Proxyclick i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering](common/provisioning.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering](common/provisioning-automatic.png)

5. Att hämta den **klient-URL** och **hemlighet Token** kontots Proxyclick följer den här genomgången enligt beskrivningen i steg 6.

6. Logga in på din [Proxyclick administratörskonsolen](https://app.proxyclick.com/login//?destination=%2Fdefault). Gå till **inställningar** > **integreringar** > **Bläddra Marketplace**.

    ![Proxyclick inställningar](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Proxyclick integreringar](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Välj **Azure AD**. Klicka på **installera nu**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick installation](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Välj **Användaretablering** och klicka på **starta integration**. 

    ![Proxyclick Användaretablering](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    Lämpliga inställningar konfigurationen Användargränssnittet visas nu under **inställningar** > **integreringar**. Välj **inställningar** under **Azure AD (etableringen av användare)** .

    ![Skapa Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Du hittar den **klient-URL** och **hemlighet Token** här.

    ![Proxyclick skapa Token](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till Proxyclick. Om anslutningen misslyckas, kontrollera Proxyclick-kontot har administratörsbehörighet och försök igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Proxyclick**.

    ![Proxyclick Användarmappningar](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Granska användarattribut som synkroniseras från Azure AD till Proxyclick i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Proxyclick för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Proxyclick användarattribut](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Om du vill aktivera den Azure AD-etableringstjänsten för Proxyclick, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Etableringsstatus aktivt](common/provisioning-toggle-on.png)

15. Ange användare och/eller grupper som du vill kan etableras på Proxyclick genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Etablering omfång](common/provisioning-scope.png)

16. När du är redo att etablera, klickar du på **spara**.

    ![Sparar Etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Proxyclick.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Proxyclick kräver **e-postmeddelanden** och **användarnamn** ha samma Källvärde. Eventuella uppdateringar till antingen attribut ska ändra andra värdet.
* Proxyclick har inte stöd för etablering för grupper.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

