---
title: 'Självstudiekurs: Konfigurera Elium för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Elium.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a3e1c936d46e0d9b0ad3b98dafeda7df0b4a23db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058520"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Elium för automatisk användaretablering

Den här självstudien visar hur du konfigurerar Elium och Azure Active Directory (Azure AD) för att automatiskt etablera och avetableras användare eller grupper till Elium.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som är byggd ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör och hur den fungerar och för vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i förhandsgranskning. De allmänna användningsvillkoren för Azure-funktioner i förhandsversionen finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Elium hyresgäst](https://www.elium.com/pricing/)
* Ett användarkonto i Elium, med administratörsbehörighet

## <a name="assigning-users-to-elium"></a>Tilldela användare till Elium

Azure AD använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som får åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bestämmer du vilka användare och grupper i Azure AD som behöver åtkomst till Elium. Tilldela sedan dessa användare och grupper till Elium genom att följa stegen i [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-elium"></a>Viktiga tips för att tilldela användare till Elium 

Vi rekommenderar att du tilldelar en enda Azure AD-användare till Elium för att testa konfigurationen för automatisk användaretablering. Fler användare och grupper kan tilldelas senare.

När du tilldelar en användare till Elium måste du välja en giltig, programspecifik roll (om någon är tillgänglig) i tilldelningsdialogrutan. Användare som har rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-elium-for-provisioning"></a>Ställ in Elium för etablering

Innan du konfigurerar Elium för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering (System for Cross-Domain Identity Management) på Elium. Följ de här stegen:

1. Logga in på Elium och gå till **Mina profilinställningar** > **Settings**.

    ![Menyalternativ för inställningar i Elium](media/Elium-provisioning-tutorial/setting.png)

1. Välj **Säkerhet**under **AVANCERAT**i det nedre vänstra hörnet.

    ![Säkerhetslänk i Elium](media/Elium-provisioning-tutorial/security.png)

1. Kopiera **klient-URL:en** och **hemliga tokenvärden.** Du använder dessa värden senare, i motsvarande fält på fliken **Etablering** i ditt Elium-program i Azure-portalen.

    ![Klient-URL och hemliga tokenfält i Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Lägg till Elium från galleriet

Om du vill konfigurera Elium för automatisk användaretablering med Azure AD måste du också lägga till Elium från Azure AD-programgalleriet i listan över hanterade SaaS-program (Software-as-a-Service). Följ de här stegen:

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i den vänstra navigeringspanelen .

    ![Menyalternativ för Azure Active Directory](common/select-azuread.png)

1. Gå till **Enterprise-program**och välj sedan **Alla program**.

     ![Azure AD Enterprise-programblad](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du **Nytt program** högst upp i fönstret.

    ![Ny programlänk](common/add-new-app.png)

1. Skriv **Elium**i sökrutan , välj **Elium** i resultatlistan och välj sedan **Lägg** till för att lägga till programmet.

    ![Sökruta för galleri](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Konfigurera automatisk användaretablering till Elium

Det här avsnittet guidar dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och grupper i Elium, baserat på användar- och grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera enkel inloggning för Elium baserat på Säkerhetspåslag Markup Language (SAML) genom att följa instruktionerna i [Elium enkel inloggningskurs .](Elium-tutorial.md) Du kan konfigurera enkel inloggning oberoende av automatisk användaretablering, även om de två funktionerna kompletterar varandra.

Så här konfigurerar du automatisk användaretablering för Elium i Azure AD:

1. Logga in på [Azure-portalen,](https://portal.azure.com)välj **Enterprise-program**och välj sedan **Alla program**.

    ![Azure AD Enterprise-programblad](common/enterprise-applications.png)

1. I listan med program väljer du **Elium**.

    ![Programlista i bladet Företagsapplikationer](common/all-applications.png)

1. Välj fliken **Etablering.**

    ![Etableringsfliken i bladet Företagsprogram](common/provisioning.png)

1. Ställ in **etableringsläget** på **Automatiskt**.

    ![Automatisk inställning för etableringsläge](common/provisioning-automatic.png)

1. Skriv ** \<klienturl\>/scim/v2** i fältet **Klient-URL** i avsnittet **Administratörsautentiseringsuppgifter.** **(TenantURL** är det värde som hämtats tidigare från Elium-administratörskonsolen.) Skriv även värdet För Elium **Secret** i fältet **Hemlig token.** Slutligen väljer du **Testa anslutning** för att kontrollera att Azure AD kan ansluta till Elium. Om anslutningen misslyckas kontrollerar du att ditt Elium-konto har administratörsbehörighet och försöker igen.

    ![Klient-URL- och hemliga tokenfält i administratörsautentiseringsuppgifter](common/provisioning-testconnection-tenanturltoken.png)

1. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-postmeddelande](common/provisioning-notification-email.png)

1. Klicka på **Spara**.

1. I avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Elium**.

    ![Synkronisera länk för mappning av Azure AD-användare till Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Granska användarattributen som synkroniseras från Azure AD till Elium i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Elium för uppdateringsåtgärder. Välj **Spara** om du vill genomföra eventuella ändringar.

    ![Attributmappningar mellan Azure AD och Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Om du vill konfigurera omfångsfilter följer du instruktionerna i [självstudiefilatkursen För att kunna konfigurera omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Om du vill aktivera Azure AD-etableringstjänsten för Elium ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus inställd på På](common/provisioning-toggle-on.png)

1. Definiera de användare och grupper som du vill etablera till Elium genom att välja de värden du vill använda i listrutan **Scope** i avsnittet **Inställningar.**

    ![Listruta Etablering av omfattning](common/provisioning-scope.png)

1. När du är redo att etablera väljer du **Spara**.

    ![Knappen Spara för etablering av konfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som **definierats** i Scope i avsnittet **Inställningar.** Den här inledande synkroniseringsprocessen tar längre tid än senare synkroniseringar. Mer information om den tid som krävs för etablering finns i [Hur lång tid tar det att etablera användare?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Använd avsnittet **Aktuell status** för att övervaka förloppet och följa länkar till din etableringsaktivitetsrapport. I verksamhetsrapporten för etablering beskrivs alla åtgärder som utförs av Azure AD-etableringstjänsten på Elium. Mer information finns [i Kontrollera status för användaretablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Information om hur du läser Azure AD-etableringsloggarna finns i Rapportera om automatisk etablering av [användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
