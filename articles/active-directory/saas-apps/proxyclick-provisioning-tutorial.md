---
title: 'Självstudiekurs: Konfigurera Proxyclick för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Proxyclick.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 95cb0371c4b2181d8f09991fe6e652c0e939f3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063364"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Proxyclick för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Proxyclick och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Proxyclick.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Proxyclick-klient](https://www.proxyclick.com/pricing)
* Ett användarkonto i Proxyclick med administratörsbehörighet.

## <a name="add-proxyclick-from-the-gallery"></a>Lägg till Proxyclick från galleriet

Innan du konfigurerar Proxyclick för automatisk användaretablering med Azure AD måste du lägga till Proxyclick från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Proxyclick från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Proxyclick**, väljer **Proxyclick** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Proxyclick i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Tilldela användare till Proxyclick

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Proxyclick. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Proxyclick genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Viktiga tips för att tilldela användare till Proxyclick

* Vi rekommenderar att en enda Azure AD-användare tilldelas Proxyclick för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Proxyclick måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Konfigurera automatisk användaretablering till Proxyclick 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Proxyclick baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Proxyclick, enligt instruktionerna i [Proxyclick enda inloggningshandledning](proxyclick-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Proxyclick i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Proxyclick**i programlistan .

    ![Länken Proxyclick i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Om du vill hämta **klientadressen** och **den hemliga token för** ditt Proxyclick-konto följer du genomgången enligt beskrivningen i steg 6.

6. Logga in på [proxyclick-administratörskonsolen](https://app.proxyclick.com/login//?destination=%2Fdefault). Navigera till **Inställningar** > **Integrationer** > **Bläddra Marketplace**.

    ![Proxyclick-inställningar](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Proxyclick Integrationer](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marknadsplats](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Välj **Azure AD**. Klicka på **Installera nu**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Installera Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Välj **Användares etablering** och klicka på **Starta integrering**. 

    ![Etablera användare för proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    Det lämpliga konfigurationsgränssnittet för inställningar bör nu visas under **Inställningar** > **Integreringar**. Välj **Inställningar** under **Azure AD (Användaresetablering)**.

    ![Skapa proxyclick](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Du hittar **klientadressen** och **den hemliga token** här.

    ![Skapa token för Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. När du fyller i fälten som visas i steg 5 klickar du på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Proxyclick. Om anslutningen misslyckas kontrollerar du att proxyklickkontot har administratörsbehörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Proxyclick**.

    ![Proxyclick användarmappningar](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Granska användarattributen som synkroniseras från Azure AD till Proxyclick i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Proxyclick för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Proxyclick användarattribut](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Om du vill aktivera Azure AD-etableringstjänsten för Proxyclick ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

15. Definiera de användare och/eller grupper som du vill etablera till Proxyclick genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

16. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Proxyclick.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* Proxyclick kräver **e-postmeddelanden** och **användarnamn** för att ha samma källvärde. Alla uppdateringar av något av attributen ändrar det andra värdet.
* Proxyclick stöder inte etablering för grupper.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

