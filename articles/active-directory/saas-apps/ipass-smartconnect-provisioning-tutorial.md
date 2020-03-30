---
title: 'Självstudiekurs: Konfigurera iPass SmartConnect för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till iPass SmartConnect.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: fb3a5d03f390b88f9856f03bdc97a35b845874ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057554"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera iPass SmartConnect för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i iPass SmartConnect och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och/eller grupper till iPass SmartConnect.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En iPass SmartConnect-klient](https://www.ipass.com/buy-ipass/).
* Ett användarkonto i iPass SmartConnect med administratörsbehörigheter.

## <a name="assigning-users-to-ipass-smartconnect"></a>Tilldela användare till iPass SmartConnect

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till iPass SmartConnect. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till iPass SmartConnect genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Viktiga tips för att tilldela användare till iPass SmartConnect

* Vi rekommenderar att en enda Azure AD-användare tilldelas iPass SmartConnect för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till iPass SmartConnect måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>Konfigurera iPass SmartConnect för etablering

Innan du konfigurerar iPass SmartConnect för automatisk användaretablering med Azure AD måste du hämta konfigurationsinformation från iPass SmartConnect-administratörskonsolen:

1. Om du vill hämta innehavartoken som behövs för att autentisera mot din iPass SmartConnect SCIM-slutpunkt, se första gången du ställer in iPass SmartConnect eftersom detta värde endast anges då. 
2. Om du inte har innehavartoken kan du kontakta [iPass SmartConnects supportteam](mailto:help@ipass.com) för att hämta en ny.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>Lägg till iPass SmartConnect från galleriet

Om du vill konfigurera iPass SmartConnect för automatisk användaretablering med Azure AD måste du lägga till iPass SmartConnect från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till iPass SmartConnect från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **iPass SmartConnect**, väljer **iPass SmartConnect** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![iPass SmartConnect i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Konfigurera automatisk användaretablering till iPass SmartConnect 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i iPass SmartConnect baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
>  Du kan också välja att aktivera SAML-baserade enkel inloggning för BitaBIZ , enligt instruktionerna i [iPass SmartConnect Single sign-on tutorial](ipasssmartconnect-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för iPass SmartConnect i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **iPass SmartConnect**i programlistan .

    ![Länken iPass SmartConnect i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://openmobile.ipass.com/moservices/scim/v1` **klient-URL.** Ange innehavartoken som hämtats tidigare i **Hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till iPass SmartConnect. Om anslutningen misslyckas kontrollerar du att ditt iPass SmartConnect-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till iPass SmartConnect**.

    ![iPass SmartConnect-användarmappningar](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. Granska användarattributen som synkroniseras från Azure AD till iPass SmartConnect i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i iPass SmartConnect för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![iPass SmartConnect-användarmappningar](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för iPass SmartConnect ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till iPass SmartConnect genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på iPass SmartConnect.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* iPass SmartConnect accepterar endast användarnamn som har sina domäner registrerade i iPass SmartConnect-administratörskonsolen.  

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
