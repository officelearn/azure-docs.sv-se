---
title: 'Självstudier: Konfigurera Figma automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Figma.
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
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: b71aa6709b1c93688ea3eece4ce3f4066f9a6b7a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673176"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Figma för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Figma och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Figma.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningsappen är för närvarande i offentlig förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En klient Figma](https://www.figma.com/pricing/).
* Ett användarkonto i Figma med administratörsbehörighet.

## <a name="assign-users-to-figma"></a>Tilldela användare till Figma.
Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Figma. När du valt, kan du tilldela dessa användare och/eller grupper till Figma genom att följa instruktionerna här:
 
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Viktiga tips för att tilldela användare till Figma

 * Vi rekommenderar att en enda Azure AD-användare har tilldelats Figma att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Figma, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med rollen som standard åtkomst undantas från etablering.

## <a name="set-up-figma-for-provisioning"></a>Konfigurera Figma för etablering

Innan du konfigurerar Figma för automatisk användarförsörjning med Azure AD behöver att hämta vissa etableringsinformationen från Figma.

1. Logga in på din [Figma administratörskonsolen](https://www.Figma.com/). Klicka på kugghjulsikonen bredvid din klient.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/image0.png)

2. Gå till **Allmänt > Uppdatera Log i inställningarna för**.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/figma03.png)

3. Kopiera den **klient-ID**. Det här värdet används för att konstruera SCIM slutpunkts-URL som ska anges i den **klient-URL** i fliken etablering Figma program i Azure-portalen.

    ![Figma skapa Token](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Rulla nedåt och klicka på **generera API-Token**.

    ![Figma skapa Token](media/Figma-provisioning-tutorial/token.png)

5. Kopiera den **API-Token** värde. Det här värdet ska anges i den **hemlighet Token** i fliken etablering Figma program i Azure-portalen. 

    ![Figma skapa Token](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Lägg till Figma från galleriet

Om du vill konfigurera Figma för automatisk användarförsörjning med Azure AD, som du behöver lägga till Figma från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. I den  **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Figma**väljer **Figma** i resultatrutan och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![Figma i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Konfigurera automatisk användaretablering för Figma 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Figma baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Figma, följa anvisningarna enligt den [Figma enkel inloggning för självstudien](figma-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Konfigurera automatisk användaretablering för Figma i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Figma**.

    ![Länken Figma i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering](common/provisioning.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering](common/provisioning-automatic.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet Ange `https://www.figma.com/scim/v2/<TenantID>` i **klient-URL** där **TenantID** är det värde som du tidigare hämtade från Figma. Indata i **API-Token** värde i **hemlighet Token**. Klicka på **Testanslutningen** att se till att Azure AD kan ansluta till Figma. Om anslutningen misslyckas, kontrollera Figma-kontot har administratörsbehörighet och försök igen.

    ![Klient-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Figma**.

    ![Figma Användarmappningar](media/Figma-provisioning-tutorial/figma05.png)

11. Granska användarattribut som synkroniseras från Azure AD till Figma i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Figma för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Figma användarattribut](media/Figma-provisioning-tutorial/figma06.png)

12. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera den Azure AD-etableringstjänsten för Figma, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Etableringsstatus aktivt](common/provisioning-toggle-on.png)

14. Ange användare och/eller grupper som du vill kan etableras på Figma genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Etablering omfång](common/provisioning-scope.png)

15. När du är redo att etablera, klickar du på **spara**.

    ![Sparar Etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Figma.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)