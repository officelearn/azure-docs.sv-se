---
title: 'Självstudiekurs: Konfigurera sele för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Harness.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 518d86fff04a23f1c1e63c44c53485b99f30637d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057846"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera sele för automatisk användaretablering

I den här artikeln får du lära dig hur du konfigurerar Azure Active Directory (Azure AD) för att automatiskt etablera och avetablera användare eller grupper till Harness.

> [!NOTE]
> I den här artikeln beskrivs en anslutningsapp som är byggd ovanpå Azure AD-användarens etableringstjänst. Viktig information om den här tjänsten och svar på vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i förhandsgranskning. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här artikeln förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En sele hyresgäst](https://harness.io/pricing/)
* Ett användarkonto i Harness med *administratörsbehörighet*

## <a name="assign-users-to-harness"></a>Tilldela användare till Harness

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bestämmer du vilka användare eller grupper i Azure AD som behöver åtkomst till Harness. Du kan sedan tilldela dessa användare eller grupper till Harness genom att följa instruktionerna i [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-harness"></a>Viktiga tips för att tilldela användare till Harness

* Vi rekommenderar att du tilldelar en enda Azure AD-användare till Harness för att testa konfigurationen för automatisk användaretablering. Ytterligare användare eller grupper kan tilldelas senare.

* När du tilldelar en användare till Harness måste du välja en giltig programspecifik roll (om sådan finns) i dialogrutan **Tilldelning.** Användare med rollen *Standardåtkomst* är undantagna från etablering.

## <a name="set-up-harness-for-provisioning"></a>Ställ in Harness för etablering

1. Logga in på [administratörskonsolen](https://app.harness.io/#/login)för Harness och gå sedan till Kontinuerlig hantering**av säkerhetsåtkomst** **Continuous Security** > .

    ![Utnyttja administratörskonsol](media/harness-provisioning-tutorial/admin.png)

1. Välj **API-nycklar**.

    ![Länken Utnyttja API-nycklar](media/harness-provisioning-tutorial/apikeys.png)

1. Välj **Lägg till API-nyckel**. 

    ![Länken Lägg till API-nyckel](media/harness-provisioning-tutorial/addkey.png)

1. Gör följande i fönstret **Lägg till api-nyckel:**

    ![Fönstret Lägg till api-nyckel genom att utnyttja](media/harness-provisioning-tutorial/title.png)
   
   a. Ange ett namn på nyckeln i rutan **Namn.**  
   b. Välj ett alternativ i listrutan **Behörigheter som ärvts från.** 
   
1. Välj **Skicka**.

1. Kopiera **nyckeln** för senare användning i den här självstudien.

    ![Utnyttja skapa token](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Lägg till sele från galleriet

Innan du konfigurerar Harness för automatisk användaretablering med Azure AD måste du lägga till Harness från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. I den vänstra rutan i [Azure-portalen](https://portal.azure.com) väljer du **Azure Active Directory**.

    ![Knappen "Azure Active Directory"](common/select-azuread.png)

1. Välj **Företagsprogram** > **Alla program**.

    ![Länken "Alla program"](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen "Ny ansökan"](common/add-new-app.png)

1. I sökrutan anger du **Harness,** väljer **Utnyttja** i resultatlistan och väljer sedan knappen **Lägg** till för att lägga till programmet.

    ![Utnyttja i resultatlistan](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Konfigurera automatisk användaretablering till Utnyttja 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare eller grupper i Utnyttja baserat på användar- eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Harness genom att följa instruktionerna i [Harness enda inloggningshandledning](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial). Du kan konfigurera enkel inloggning oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

> [!NOTE]
> Mer information om scim-slutpunkten har utnyttjats finns i artikeln Harness [API Keys.](https://docs.harness.io/article/smloyragsm-api-keys)

Så här konfigurerar du automatisk användaretablering för Harness i Azure AD:

1. Välj **Enterprise Applications** > **Alla program**i [Azure-portalen](https://portal.azure.com).

    ![Bladet Företagsprogram](common/enterprise-applications.png)

1. Välj **Utnyttja**i programlistan .

    ![Länken Harness i programlistan](common/all-applications.png)

1. Välj **Etablering**.

    ![Knappen Etablering](common/provisioning.png)

1. Välj **Automatisk**i listrutan **Etableringsläge** .

    ![Listrutan "Etableringsläge"](common/provisioning-automatic.png)

1. Gör följande under **Administratörsautentiseringsuppgifter:**

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Ange i rutan **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** **Klient-URL.**  
   b. I rutan **Hemlig token** anger du det SCIM-autentiseringstokenvärde som du sparade i steg 6 i avsnittet "Ställ in har utnyttja för etablering".  
   c. Välj **Testanslutning** för att säkerställa att Azure AD kan ansluta till Harness. Om anslutningen misslyckas kontrollerar du att Harness-kontot har *administratörsbehörighet* och försöker sedan igen.

1. I rutan **E-post för avisering** anger du e-postadressen till en person eller grupp som ska ta emot meddelanden om etableringsfel och markerar sedan kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![Rutan "E-post" för meddelanden](common/provisioning-notification-email.png)

1. Välj **Spara**.

1. Under **Mappningar**väljer du **Synkronisera Azure Active Directory-användare att utnyttja**.

    ![Sele "Synkronisera Azure Active Directory-användare att utnyttja" länk](media/harness-provisioning-tutorial/usermappings.png)

1. Granska användarattribut som synkroniseras från Azure AD till Harness under **Attributmappningar.** De attribut som valts *som Matchning* används för att matcha användarkontona i Harness för uppdateringsåtgärder. Välj **Spara** om du vill genomföra eventuella ändringar.

    ![Fönstret Utnyttja användare "Attributmappningar"](media/harness-provisioning-tutorial/userattributes.png)

1. Under **Mappningar**väljer du **Synkronisera Azure Active Directory-grupper att utnyttja**.

    ![Utnyttja länken "Synkronisera Azure Active Directory Groups to Harness"-länk](media/harness-provisioning-tutorial/groupmappings.png)

1. Granska gruppattribut som synkroniseras från Azure AD till Harness under **Attributmappningar.** De attribut som valts som *matchande* egenskaper används för att matcha grupperna i Harness för uppdateringsåtgärder. Välj **Spara** om du vill genomföra eventuella ändringar.

    ![Fönstret Utnyttja grupp "Attributmappningar"](media/harness-provisioning-tutorial/groupattributes.png)

1. Information om hur du konfigurerar omfångsfilter finns i [Attributbaserad programetablering med omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Under **Inställningar**, för att aktivera Azure AD-etableringstjänsten för Harness, växla **etableringsstatusväxeln** till **På**.

    ![Etablering status switch växla till "På"](common/provisioning-toggle-on.png)

1. Under Inställningar väljer du under **Inställningar**i listrutan **Scope** hur du vill synkronisera de användare eller grupper som du etablerar till Harness.

    ![Etableringsomfång](common/provisioning-scope.png)

1. När du är redo att etablera väljer du **Spara**.

    ![Knappen Etablera Spara](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av de användare eller grupper som du etablerar. Den första synkroniseringen tar längre tid att utföra än senare. Synkroniseringar inträffar ungefär var 40:e minut, så länge azure AD-etableringstjänsten körs. Om du vill övervaka förloppet går du till avsnittet **Synkroniseringsinformation.** Du kan också följa länkar till en etableringsaktivitetsrapport som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Harness.

Mer information om hur du läser Azure AD-etableringsloggarna finns i Rapportera om automatisk etablering av [användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för företagsappar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
