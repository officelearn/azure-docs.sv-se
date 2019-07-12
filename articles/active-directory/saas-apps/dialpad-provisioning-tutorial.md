---
title: 'Självstudier: Konfigurera Dialpad för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Dialpad.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 32e634bc089417aaa8080b30a5f77f663a3d8b33
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611769"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Dialpad för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Dialpad och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Dialpad.

> [!NOTE]
>  Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

> Den här anslutningsappen är för närvarande i förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En klient Dialpad](https://www.dialpad.com/pricing/).
* Ett användarkonto i Dialpad med administratörsbehörighet.

## <a name="assign-users-to-dialpad"></a>Tilldela användare till Dialpad
Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Dialpad. När du valt, kan du tilldela dessa användare och/eller grupper till Dialpad genom att följa instruktionerna här:
 
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Viktiga tips för att tilldela användare till Dialpad

 * Vi rekommenderar att en enda Azure AD-användare har tilldelats Dialpad att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Dialpad, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med rollen som standard åtkomst undantas från etablering.

## <a name="setup-dialpad-for-provisioning"></a>Konfigurera Dialpad för etablering

Innan du konfigurerar Dialpad för automatisk användarförsörjning med Azure AD behöver att hämta vissa etableringsinformationen från Dialpad.

1. Logga in på din [Dialpad administratörskonsolen](https://dialpadbeta.com/login) och välj **administratörsinställningar**. Se till att **mitt företag** väljs i listrutan. Gå till **autentisering > API-nycklar**.

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Skapa en ny nyckel genom att klicka på **lägga till en nyckel** och konfigurera egenskaperna för din hemliga token.

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Klicka på den **Klicka om du vill visa värdet** för din nyligen skapade API-nyckel och kopiera värdet som visas. Det här värdet ska anges i den **hemlighet Token** i fliken etablering Dialpad program i Azure-portalen. 

    ![Dialpad skapa Token](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Lägg till Dialpad från galleriet

Du måste lägga till Dialpad från Azure AD-programgalleriet i listan över hanterade SaaS-program för att konfigurera Dialpad för automatisk användarförsörjning med Azure AD.

**Utför följande steg för att lägga till Dialpad från Azure AD-programgalleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Dialpad**väljer **Dialpad** i resultatrutan.
    ![Dialpad i resultatlistan](common/search-new-app.png)

5. Navigera till den **URL** markerade nedan i en separat webbläsare. 

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. I det övre högra hörnet väljer **logga In > Använd Dialpad online**.

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Eftersom Dialpad är en OpenIDConnect app, väljer du att logga in med ditt Microsoft-arbetskonto Dialpad.

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Acceptera medgivande för medgivande sidan efter en lyckad autentisering. Programmet sedan läggs automatiskt till din klient och du kommer att omdirigeras till ditt Dialpad-konto.

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Konfigurera automatisk användaretablering till Dialpad

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Dialpad baserat på användare och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Konfigurera automatisk användaretablering för Dialpad i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Dialpad**.

    ![Länken Dialpad i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering](common/provisioning.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering](common/provisioning-automatic.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet Ange `https://dialpad.com/scim` i **klient-URL**. Indata det värde som du hämtade och sparat tidigare från Dialpad i **hemlighet Token**. Klicka på **Testanslutningen** att se till att Azure AD kan ansluta till Dialpad. Om anslutningen misslyckas, kontrollera Dialpad-kontot har administratörsbehörighet och försök igen.

    ![Klient-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Dialpad**.

    ![Dialpad Användarmappningar](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Granska användarattribut som synkroniseras från Azure AD till Dialpad i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Dialpad för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Dialpad användarattribut](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera den Azure AD-etableringstjänsten för Dialpad, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Etableringsstatus aktivt](common/provisioning-toggle-on.png)

12. Ange användare och/eller grupper som du vill kan etableras på Dialpad genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Etablering omfång](common/provisioning-scope.png)

13. När du är redo att etablera, klickar du på **spara**.

    ![Sparar Etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Dialpad.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarens kontoetablering](../manage-apps/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Begränsningar för anslutningen
* Dialpad stöder inte byter namn på gruppen i dag. Detta innebär att ändringar av den **displayName** för en grupp i Azure AD inte uppdateras och visas i Dialpad.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)
