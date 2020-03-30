---
title: 'Självstudiekurs: Konfigurera Dropbox för företag för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Dropbox för företag.
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
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 3acc2c271e590bddb13aaa01498f404da4340036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058467"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Dropbox för företag för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Dropbox för företag och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Dropbox för företag.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Dropbox för företag-klient](https://www.dropbox.com/business/pricing)
* Ett användarkonto i Dropbox för företag med administratörsbehörighet.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Lägg till Dropbox för företag från galleriet

Innan du konfigurerar Dropbox för företag för automatisk användaretablering med Azure AD måste du lägga till Dropbox för företag från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Dropbox för företag från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Dropbox för företag,** väljer **Dropbox för företag** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Dropbox for Business i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Tilldela användare till Dropbox för företag

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Dropbox för företag. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Dropbox för företag genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Viktiga tips för att tilldela användare till Dropbox för företag

* Vi rekommenderar att en enda Azure AD-användare tilldelas Dropbox för företag för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Dropbox för företag måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Konfigurera automatisk användaretablering till Dropbox för företag 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Dropbox för företag baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Dropbox för företag, i enlighet med instruktionerna i [dropbox för företag-självanmälan](dropboxforbusiness-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Dropbox för företag i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj Dropbox för **företag**i programlistan .

    ![Dropbox for Business-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Klicka på **Auktorisera**under avsnittet **Administratörsautentiseringsuppgifter** . Det öppnar en Dropbox för företag inloggningsdialogruta i ett nytt webbläsarfönster.

    ![Etablering ](common/provisioning-oauth.png)

6. Logga in på din Dropbox för **företag-klient** och verifiera din identitet i dialogrutan Logga in på Dropbox för företag för att länka till Azure AD.

    ![Inloggning för Dropbox för företag](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. När du har slutfört steg 5 och 6 klickar du på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Dropbox för företag. Om anslutningen misslyckas kontrollerar du att ditt Dropbox för företag-konto har administratörsbehörighet och försöker igen.

    ![Token](common/provisioning-testconnection-oauth.png)

8. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Dropbox**.

    ![Dropbox-användarmappningar](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Granska användarattributen som synkroniseras från Azure AD till Dropbox i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Dropbox för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Dropbox-användarattribut](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper till Dropbox**.

    ![Dropbox-gruppkartläggningar](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Dropbox i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Dropbox för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Attribut för Dropbox-grupp](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD-etableringstjänsten för Dropbox ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

16. Definiera de användare och/eller grupper som du vill etablera till Dropbox genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Dropbox.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning
 
* Dropbox stöder inte att inbjudna användare stängs av. Om en inbjuden användare stängs av tas användaren bort.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

