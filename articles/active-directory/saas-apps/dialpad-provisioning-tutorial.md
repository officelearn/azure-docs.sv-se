---
title: 'Självstudiekurs: Konfigurera knappsats för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Dialpad.
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
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058416"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera knappsats för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Dialpad och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Dialpad.

> [!NOTE]
>  Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

> Den här kopplingen är för närvarande i förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En Dialpad-klient](https://www.dialpad.com/pricing/).
* Ett användarkonto i Dialpad med administratörsbehörighet.

## <a name="assign-users-to-dialpad"></a>Tilldela användare till knappsats
Azure Active Directory använder ett koncept som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Dialpad. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Knappsatsen genom att följa instruktionerna här:
 
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Viktiga tips för att tilldela användare till Knappsats

 * Vi rekommenderar att en enda Azure AD-användare tilldelas Dialpad för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Knappsats måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen Standardåtkomst är undantagna från etablering.

## <a name="setup-dialpad-for-provisioning"></a>Konfigurera knappsats för etablering

Innan du konfigurerar Dialpad för automatisk användaretablering med Azure AD måste du hämta viss etableringsinformation från Dialpad.

1. Logga in på [administratörskonsolen](https://dialpadbeta.com/login) för knappsats och välj **Administratörsinställningar**. Se till att **Mitt företag** väljs från rullgardinsmenyn. Navigera till **Autentisering > API-nycklar**.

    ![Knappsats Lägg till SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Generera en ny nyckel genom att klicka på **Lägg till en nyckel** och konfigurera egenskaperna för din hemliga token.

    ![Knappsats Lägg till SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Knappsats Lägg till SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Klicka på knappen **Klicka för att visa värde** för den nyligen skapade API-nyckeln och kopiera värdet som visas. Det här värdet anges i fältet **Hemlig token** på fliken Etablering i ditt Dialpad-program i Azure-portalen. 

    ![Skapa token för knappsats](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Lägg till knappsats från galleriet

Om du vill konfigurera Dialpad för automatisk användaretablering med Azure AD måste du lägga till Dialpad från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till knappsats från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Knappsats**, välj **Knappsats** på resultatpanelen.
    ![Knappsats i resultatlistan](common/search-new-app.png)

5. Navigera till **webbadressen** som markeras nedan i en separat webbläsare. 

    ![Knappsats Lägg till SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. I det övre högra hörnet väljer **du Logga in > Använd knappsats online**.

    ![Knappsats Lägg till SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Eftersom Dialpad är en OpenIDConnect-app väljer du att logga in på Dialpad med ditt Microsoft-arbetskonto.

    ![Knappsats Lägg till SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Efter en lyckad autentisering godkänner du samtyckesprompten för samtyckessidan. Programmet läggs sedan automatiskt till din klient och du omdirigeras till ditt Dialpad-konto.

    ![Knappsats Lägg till SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Konfigurera automatisk användaretablering till knappsats

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Dialpad baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Dialpad i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Knappsats**i programlistan .

    ![Länken Knappsats i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://dialpad.com/scim` **klient-URL.** Ange värdet som du hämtade och sparade tidigare från Knappsats i **Hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Knappsats. Om anslutningen misslyckas kontrollerar du att dialpadkontot har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till dialpad**.

    ![Användarmappningar för knappsats](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Granska användarattributen som synkroniseras från Azure AD till Dialpad i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Dialpad för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Användarattribut för knappsats](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för Dialpad ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Knappsats genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Dialpad.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Begränsningar för anslutning
* Dialpad stöder inte gruppbyten idag. Det innebär att alla ändringar i **visningsnamnet för** en grupp i Azure AD inte kommer att uppdateras och återspeglas i Dialpad.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
