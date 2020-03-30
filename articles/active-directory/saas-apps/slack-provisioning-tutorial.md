---
title: 'Självstudiekurs: Användaretablering för Slack - Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Slack.
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
ms.openlocfilehash: cdc912c2df435f9b7e591d7c5475e126e6b0aeb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062837"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Slack för automatisk användaretablering

Syftet med den här självstudien är att visa de steg du behöver för att utföra i Slack och Azure AD för att automatiskt etablera och avetableras användarkonton från Azure AD till Slack.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klientorganisation
* En Slack-klient med [Plus-planen](https://aadsyncfabric.slack.com/pricing) eller bättre aktiverad
* Ett användarkonto i Slack med behörigheter för teamadministratör

Azure AD-etableringsintegreringen är beroende av [Slack SCIM API](https://api.slack.com/scim), som är tillgängligt för Slack-team på Plus-planen eller bättre.

## <a name="assigning-users-to-slack"></a>Tilldela användare till Slack

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till din Slack-app. När du har bestämt dig kan du tilldela dessa användare till din Slack-app genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Viktiga tips för att tilldela användare till Slack

* Vi rekommenderar att en enda Azure AD-användare tilldelas Slack för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Slack måste du välja rollen **Användare** eller Grupp i tilldelningsdialogrutan. Rollen "Standardåtkomst" fungerar inte för etablering.

## <a name="configuring-user-provisioning-to-slack"></a>Konfigurera användares etablering till Slack 

Det här avsnittet hjälper dig att ansluta din Azure AD till Slacks API för etablering av användarkonton och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Slack baserat på användar- och grupptilldelning i Azure AD.

**Tips:** Du kan också välja att aktivera SAML-baserade Single Sign-On for Slack, enligt instruktionerna i [Azure portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Så här konfigurerar du automatisk etablering av användarkonton till Slack i Azure AD:

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

2. Om du redan har konfigurerat Slack för enkel inloggning söker du efter din instans av Slack med hjälp av sökfältet. Annars väljer du **Lägg till** och söker efter **Slack** i programgalleriet. Välj Slack i sökresultaten och lägg till det i listan över program.

3. Välj din förekomst av Slack och välj sedan fliken **Etablering.**

4. Ställ in **etableringsläget** på **Automatiskt**.

   ![Slack-etablering](./media/slack-provisioning-tutorial/slack1.png)

5. Klicka på **Auktorisera**under avsnittet **Administratörsautentiseringsuppgifter** . Då öppnas en slackauktoriseringsdialogruta i ett nytt webbläsarfönster.

6. Logga in på Slack i det nya fönstret med ditt teamadministratörskonto. I den resulterande auktoriseringsdialogrutan väljer du det Slack-team som du vill aktivera etablering för och väljer sedan **Auktorisera**. När du är klar går du tillbaka till Azure-portalen för att slutföra etableringskonfigurationen.

    ![Dialogrutan Auktorisering](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Klicka på Testa **anslutning** i Azure-portalen för att säkerställa att Azure AD kan ansluta till din Slack-app. Om anslutningen misslyckas kontrollerar du att ditt Slack-konto har behörigheten Teamadministratör och försöker igen.

8. Ange e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden i fältet **E-postmeddelande** och markera kryssrutan nedan.

9. Klicka på **Spara**.

10. Under avsnittet Mappningar väljer du **Synkronisera Azure Active Directory-användare till Slack**.

11. I avsnittet **Attributmappningar** granskar du de användarattribut som ska synkroniseras från Azure AD till Slack. Observera att de attribut som valts som **matchande** egenskaper kommer att användas för att matcha användarkontona i Slack för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

12. Om du vill aktivera Azure AD-etableringstjänsten för Slack ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar**

13. Klicka på **Spara**.

Då startas den första synkroniseringen av alla användare och/eller grupper som tilldelats Slack i avsnittet Användare och grupper. Observera att den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var tionde minut så länge tjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsrapporter, som beskriver alla åtgärder som utförs av etableringstjänsten i slackappen.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Valfritt] Konfigurera gruppobjektetablering till Slack

Du kan också aktivera etablering av gruppobjekt från Azure AD till Slack. Detta skiljer sig från att "tilldela grupper av användare", eftersom det faktiska gruppobjektet utöver sina medlemmar kommer att replikeras från Azure AD till Slack. Om du till exempel har en grupp med namnet "Min grupp" i Azure AD skapas en identisk grupp med namnet "Min grupp" inuti Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Så här aktiverar du etablering av gruppobjekt:

1. Under avsnittet Mappningar väljer du **Synkronisera Azure Active Directory-grupper mot Slack**.

2. Ange Aktiverad till Ja i bladet Attributmappning.

3. I avsnittet **Attributmappningar** granskar du de gruppattribut som ska synkroniseras från Azure AD till Slack. Observera att de attribut som valts som **matchande** egenskaper kommer att användas för att matcha grupperna i Slack för uppdateringsåtgärder. 

4. Klicka på **Spara**.

Detta resulterar i att alla gruppobjekt som tilldelats Slack i avsnittet **Användare och grupper** synkroniseras fullständigt från Azure AD till Slack. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar, som beskriver alla åtgärder som utförs av etableringstjänsten i slackappen.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* När du konfigurerar Slacks **displayName-attribut** bör du tänka på följande:

  * Värdena är inte helt unika (t.ex. 2 användare kan ha samma visningsnamn)

  * Stöder icke-engelska tecken, blanksteg, versaler. 
  
  * Tillåtna skiljetecken omfattar punkter, understreck, bindestreck, apostrofer, hakparenteser **, / ;**(t.ex. **( [ { } ] )**
  
  * Endast uppdateringar om dessa två inställningar är konfigurerade på Slacks arbetsplats/organisation - **Profilsynkronisering är aktiverat** och **användare kan inte ändra sitt visningsnamn**.
  
* Slacks **userName-attribut** måste vara under 21 tecken och ha ett unikt värde.

* Slack tillåter endast matchning med attributen **användarnamn** och **e-post**.  

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
