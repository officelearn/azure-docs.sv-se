---
title: 'Självstudiekurs: Användaretablering för Cerner Central - Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera användare till en lista i Cerner Central.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ed04d8fdcc2d79c66e2ebc53c737c78664e4621
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058324"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Cerner Central för automatisk användaretablering

Syftet med den här självstudien är att visa de steg du behöver för att utföra i Cerner Central och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till en användarlista i Cerner Central.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klientorganisation
* En hyresgäst i Cerner Central

> [!NOTE]
> Azure Active Directory integreras med Cerner Central med [SCIM-protokollet.](http://www.simplecloud.info/)

## <a name="assigning-users-to-cerner-central"></a>Tilldela användare till Cerner Central

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etableringstjänsten bör du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till Cerner Central. När du har bestämt dig kan du tilldela dessa användare till Cerner Central genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Viktiga tips för att tilldela användare till Cerner Central

* Vi rekommenderar att en enda Azure AD-användare tilldelas Cerner Central för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När den första testningen är klar för en enskild användare rekommenderar Cerner Central att tilldela hela listan över användare som är avsedda att komma åt en Cerner-lösning (inte bara Cerner Central) som ska etableras i Cerners användarlista.  Andra Cerner-lösningar utnyttjar den här listan över användare i användarlistan.

* När du tilldelar en användare till Cerner Central måste du välja **användarrollen** i tilldelningsdialogrutan. Användare med rollen "Standardåtkomst" är undantagna från etablering.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Konfigurera användaretablering till Cerner Central

Det här avsnittet hjälper dig att ansluta din Azure AD till Cerner Centrals användarlista med Cerners SCIM-användarkonto som etablerar API och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Cerner Central baserat på användar- och grupptilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Cerner Central, enligt instruktionerna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra. Mer information finns i [självstudiekursen cerner central med enkel inloggning](cernercentral-tutorial.md).

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Så här konfigurerar du automatisk etablering av användarkonton till Cerner Central i Azure AD:

För att kunna etablera användarkonton till Cerner Central måste du begära ett Cerner Central-systemkonto från Cerner och generera en OAuth-bäraretoken som Azure AD kan använda för att ansluta till Cerners SCIM-slutpunkt. Det rekommenderas också att integrationen utförs i en Cerner sandlåda miljö innan de distribueras till produktion.

1. Det första steget är att se till att de personer som hanterar Cerner- och Azure AD-integreringen har ett CernerCare-konto, vilket krävs för att komma åt den dokumentation som krävs för att slutföra instruktionerna. Om det behövs kan du använda webbadresserna nedan för att skapa CernerCare-konton i varje tillämplig miljö.

   * Sandlåda:https://sandboxcernercare.com/accounts/create

   * Produktion:https://cernercare.com/accounts/create  

2. Därefter måste ett systemkonto skapas för Azure AD. Använd instruktionerna nedan för att begära ett systemkonto för din sandlåda och dina produktionsmiljöer.

   * Instruktioner:https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Sandlåda:https://sandboxcernercentral.com/system-accounts/

   * Produktion:https://cernercentral.com/system-accounts/

3. Skapa sedan en OAuth-innehavartoken för vart och ett av dina systemkonton. För att göra detta, följ instruktionerna nedan.

   * Instruktioner:https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Sandlåda:https://sandboxcernercentral.com/system-accounts/

   * Produktion:https://cernercentral.com/system-accounts/

4. Slutligen måste du skaffa Sfär-ID:er för användarlista för både sandlådan och produktionsmiljöerna i Cerner för att slutföra konfigurationen. Information om hur du skaffar https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIMdetta finns i: . 

5. Nu kan du konfigurera Azure AD för att etablera användarkonton till Cerner. Logga in på [Azure-portalen](https://portal.azure.com)och bläddra till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

6. Om du redan har konfigurerat Cerner Central för enkel inloggning söker du efter din instans av Cerner Central med hjälp av sökfältet. Annars väljer du **Lägg till** och sök efter **Cerner Central** i programgalleriet. Välj Cerner Central från sökresultaten och lägg till den i listan över program.

7. Välj din instans av Cerner Central och välj sedan fliken **Etablering.**

8. Ställ in **etableringsläget** på **Automatiskt**.

   ![Cerner central etablering](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Fyll i följande fält under **Administratörsautentiseringsuppgifter:**

   * I fältet **Klient-URL** anger du en URL i formatet nedan och ersätter "User-Roster-Realm-ID" med det sfär-ID som du fick i steg #4.

    > Sandlåda:https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Produktion:https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * I fältet **Hemlig token** anger du den OAuth-innehavartoken som du skapade i steg #3 och klickar på **Testa anslutning**.

   * Du bör se en framgångsmeddelande på den övre sidan av portalen.

1. Ange e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden i fältet **E-postmeddelande** och markera kryssrutan nedan.

1. Klicka på **Spara**.

1. I avsnittet **Attributmappningar** granskar du de användar- och gruppattribut som ska synkroniseras från Azure AD till Cerner Central. De attribut som valts som **matchande** egenskaper används för att matcha användarkonton och grupper i Cerner Central för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

1. Om du vill aktivera Azure AD-etableringstjänsten för Cerner Central ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar**

1. Klicka på **Spara**.

Detta startar den första synkroniseringen av alla användare och/eller grupper som tilldelats Cerner Central i avsnittet Användare och grupper. Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar, som beskriver alla åtgärder som utförs av etableringstjänsten i din Cerner Central-app.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Cerner Central: Publicera identitetsdata med Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Självstudiekurs: Konfigurera Cerner Central för enkel inloggning med Azure Active Directory](cernercentral-tutorial.md)
* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
