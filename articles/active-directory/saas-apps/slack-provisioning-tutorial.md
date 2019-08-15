---
title: 'Självstudier: Konfigurera slack för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till slack.
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
ms.openlocfilehash: 4a294254bd52db89179c5644ea7a0f0f04027f30
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932476"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Självstudier: Konfigurera slack för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i slack och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till slack.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klientorganisation
* En slack-klient med [plus](https://aadsyncfabric.slack.com/pricing) -eller bättre aktiverat
* Ett användar konto i slack med team administratörs behörighet

Anteckning: Azure AD Provisioning-integrationen är beroende av [SLACK scim-API: et](https://api.slack.com/scim), som är tillgängligt för slack-teamen på plus-eller-planen.

## <a name="assigning-users-to-slack"></a>Tilldela användare till slack

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som ska representera de användare som behöver åtkomst till din slack-app. När du har bestämt dig kan du tilldela dessa användare till din slack-app genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Viktiga tips för att tilldela användare till slack

* Vi rekommenderar att en enda Azure AD-användare tilldelas till slack för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till slack måste du välja rollen **användare** eller grupp i dialog rutan tilldelning. Rollen "standard åtkomst" fungerar inte för etablering.

## <a name="configuring-user-provisioning-to-slack"></a>Konfigurera användar etablering till slack 

I det här avsnittet får du hjälp med att ansluta Azure AD till slack-API: et för användar konto etablering och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i slack baserat på användar-och grupp tilldelning i Azure AD.

**Tips:** Du kan också välja att aktivera SAML-baserad enkel inloggning för slack genom att följa anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Konfigurera automatisk användar konto etablering till slack i Azure AD:

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program** .

2. Om du redan har konfigurerat slack för enkel inloggning söker du efter din instans av slack med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **slack** i program galleriet. Välj slack från Sök resultaten och Lägg till det i listan över program.

3. Välj din instans av slack och välj fliken **etablering** .

4. Ställ in **etablerings läget** på **automatiskt**.

   ![Slacktid-etablering](./media/slack-provisioning-tutorial/Slack1.PNG)

5. Under avsnittet **admin credentials** klickar du på **auktorisera**. Då öppnas en dialog ruta för slack-auktorisering i ett nytt webbläsarfönster.

6. I det nya fönstret loggar du in på slack med ditt team administratörs konto. i dialog rutan resulterande auktorisering väljer du det slack-team som du vill aktivera etablering för och väljer sedan **auktorisera**. När du är klar återgår du till Azure Portal för att slutföra etablerings konfigurationen.

    ![Dialog rutan auktorisering](./media/slack-provisioning-tutorial/Slack3.PNG)

7. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din slack-app. Om anslutningen Miss lyckas kontrollerar du att ditt slack-konto har team administratörs behörighet och försöker sedan igen.

8. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan nedan.

9. Klicka på **Spara**.

10. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till slack**.

11. I avsnittet **mappningar för attribut** granskar du de användarattribut som kommer att synkroniseras från Azure AD till slack. Observera att attributen som har valts som **matchande** egenskaper kommer att användas för att matcha användar kontona i slack för uppdaterings åtgärder. Välj knappen Spara för att genomföra ändringarna.

12. Om du vill aktivera Azure AD Provisioning-tjänsten för slack ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar**

13. Klicka på **Spara**.

Detta startar den inledande synkroniseringen av alla användare och/eller grupper som har tilldelats slack i avsnittet användare och grupper. Observera att den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var tionde minut så länge tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporter som beskriver alla åtgärder som utförs av etablerings tjänsten i din slack-app.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>Valfritt Konfigurera grupp objekts etablering till slack

Alternativt kan du aktivera etableringen av grupp objekt från Azure AD till slack. Detta skiljer sig från "tilldela grupper av användare", där det faktiska gruppobjektet förutom dess medlemmar kommer att replikeras från Azure AD till slack. Om du till exempel har en grupp med namnet "min grupp" i Azure AD kommer en identisk grupp med namnet "min grupp" att skapas i slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Så här aktiverar du etablering av grupp objekt:

1. Under avsnittet mappningar väljer **du synkronisera Azure Active Directory grupper till slack**.

2. I bladet attributmappning väljer du aktive rad till Ja.

3. I avsnittet **mappningar för attribut** granskar du de Gruppattribut som ska synkroniseras från Azure AD till slack. Observera att attributen som har valts som **matchande** egenskaper kommer att användas för att matcha grupperna i slack för uppdaterings åtgärder. 

4. Klicka på **Spara**.

Detta resulterar i att alla grupp objekt som tilldelats slack i avsnittet **användare och grupper** är fullständigt synkroniserade från Azure AD till slack. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten i din slack-app.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* När du konfigurerar Slackets **DisplayName** -attribut bör du vara medveten om följande beteenden:

  * Värdena är inte helt unika (t. ex. 2 användare kan ha samma visnings namn)

  * Har stöd för icke-engelska tecken, blank steg och Skift läge. 
  
  * Tillåten interpunktion innehåller punkter, under streck, bindestreck, apostrofer, hakparenteser (t. ex. **([{}])** ) och avgränsare (t. **ex.,/;** ).
  
  * Endast uppdateringar om de här två inställningarna har kon figurer ATS i slack s arbets plats/organisation – **profil synkronisering är aktiverat** och **användarna inte kan ändra sina visnings namn**.
  
* Slackets **username** -attribut måste vara under 21 tecken och ha ett unikt värde.

* Slack tillåter endast matchning med attributen **användar namn** och **e-post**.  

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
