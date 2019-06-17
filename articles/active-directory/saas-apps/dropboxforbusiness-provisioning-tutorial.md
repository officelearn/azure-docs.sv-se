---
title: 'Självstudier: Konfigurera Dropbox for Business för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Dropbox för företag.
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
ms.date: 05/20/2019
ms.author: zchia
ms.openlocfilehash: c95346ff9026b7fc8c9141234fb3cde9c0809d23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053491"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Dropbox for Business för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Dropbox för företag och Azure Active Directory (Azure AD) att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Dropbox för företag.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Dropbox för företag-klient](https://www.dropbox.com/business/pricing)
* Ett användarkonto i Dropbox för företag med administratörsbehörighet.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Lägg till Dropbox för företag från galleriet

Du måste lägga till Dropbox för företag från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar Dropbox for Business för automatisk användarförsörjning med Azure AD.

**Utför följande steg för att lägga till Dropbox för företag från Azure AD-programgalleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Dropbox for Business**väljer **Dropbox for Business** i resultatrutan och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![Dropbox for Business i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Tilldela användare till Dropbox för företag

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Dropbox för företag. När du valt, kan du tilldela dessa användare och/eller grupper till Dropbox för företag genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Viktiga tips för att tilldela användare till Dropbox för företag

* Vi rekommenderar att en enda Azure AD-användare är tilldelad till Dropbox för företag för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Dropbox för företag, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Konfigurera automatisk användaretablering till Dropbox för företag 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Dropbox for Business baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Dropbox for Business, följa anvisningarna enligt den [Dropbox företag enkel inloggning genomgång](dropboxforbusiness-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Konfigurera automatisk användaretablering för Dropbox för företag i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Dropbox for Business**.

    ![Dropbox for Business-länken i programlistan](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering](common/provisioning.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering](common/provisioning-automatic.png)

5. Under den **administratörsautentiseringsuppgifter** klickar du på **auktorisera**. En Dropbox for Business inloggningsruta öppnas den i ett nytt webbläsarfönster.

    ![Etablering ](common/provisioning-oauth.png)

6. På den **logga in på Dropbox för företag att länka med Azure AD** dialogrutan Logga in på din Dropbox för företag-klient och verifiera din identitet.

    ![Dropbox för att logga in företag](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. När du slutfört steg 5 och 6, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till Dropbox för företag. Om anslutningen misslyckas, kontrollera din Dropbox för företag-konto har administratörsbehörighet och försök igen.

    ![Token](common/provisioning-testconnection-oauth.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Dropbox**.

    ![Dropbox Användarmappningar](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Granska användarattribut som synkroniseras från Azure AD till Dropbox i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Dropbox för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Dropbox användarattribut](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper till Dropbox**.

    ![Mappningar för dropbox-grupp](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Granska Gruppattribut som synkroniseras från Azure AD till Dropbox i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha grupper i Dropbox för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Attributen för dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera den Azure AD-etableringstjänsten för Dropbox, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Etableringsstatus aktivt](common/provisioning-toggle-on.png)

16. Ange användare och/eller grupper som du vill att etablera till Dropbox genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Etablering omfång](common/provisioning-scope.png)

17. När du är redo att etablera, klickar du på **spara**.

    ![Sparar Etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Dropbox.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen
 
* Dropbox har inte stöd för att pausa inbjudna användare. Om en inbjuden användare har pausats kan tas användaren bort.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

