---
title: 'Självstudiekurs: Konfigurera Peakon-automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Peakon .
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
ms.openlocfilehash: 0a67dc8069ee71305a47bd5d2a724a61cec234a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063420"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Peakon för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Peakon och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Peakon.

> [!NOTE]
>  Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar

* En Azure AD-klientorganisation.
* [En Peakon-klient](https://peakon.com/us/pricing/).
* Ett användarkonto i Peakon med administratörsbehörighet.

## <a name="assigning-users-to-peakon"></a>Tilldela användare till Peakon

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Peakon. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Peakon genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Viktiga tips för att tilldela användare till Peakon 

* Vi rekommenderar att en enda Azure AD-användare tilldelas Peakon för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Peakon måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-peakon-for-provisioning"></a>Ställ in Peakon för etablering

1.  Logga in på [topons administratörskonsol .](https://app.Peakon.com/login) Klicka på **Konfiguration**. 

    ![Peakon-administratörskonsol](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Välj **Integreringar**.
    
    ![Peakon-anställd-etablering](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Aktivera **etablering av medarbetare**.

    ![Peakon-anställd-etablering](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Kopiera värdena för **SCIM 2.0 URL** och **OAuth Bearer Token**. Dessa värden anges i fältet **Klient-URL** och **Hemlig token** på fliken Etablering i Peakon-programmet i Azure-portalen.

    ![Peakon Skapa token](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Lägg till Peakon från galleriet

Om du vill konfigurera Peakon för automatisk användaretablering med Azure AD måste du lägga till Peakon från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Peakon**, väljer **Peakon** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Peakon i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Konfigurera automatisk användaretablering till Peakon 

Det här avsnittet guidar dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Peakon baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Peakon, enligt instruktionerna i [Peakon Single sign-on tutorial](peakon-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Peakon i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Peakon**i programlistan .

    ![Peakon-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du värdena **SCIM 2.0 URL** och **OAuth Bearer Token** som hämtats tidigare i **klient-URL** respektive **Hemlig token.** Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Peakon. Om anslutningen misslyckas kontrollerar du att Ditt Peakon-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

7. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

8. Klicka på **Spara**.

9. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Peakon**.

    ![Peakon-användarmappningar](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Granska användarattributen som synkroniseras från Azure AD till Peakon i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Peakon för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Peakon-användarattribut](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Peakon.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* Alla anpassade användarattribut i Peakon måste utökas från Peakons `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`anpassade SCIM-användartillägg för .

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)