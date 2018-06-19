---
title: 'Självstudier: Konfigurera Slack för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avinstallation etablera användarkonton till Slack.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.reviewer: asmalser
ms.openlocfilehash: e00b9b73d7668ccae9cec85bbde30b18af2474e6
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35967997"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Slack för automatisk användaretablering


Syftet med den här kursen är att visa de steg som du behöver utföra i Slack och Azure AD för att automatiskt etablera och avinstallation etablera användarkonton från Azure AD till Slack. 

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active Active directory-klient
*   En Slack-klienten med den [Plus plan](https://aadsyncfabric.slack.com/pricing) eller bättre aktiverat 
*   Ett användarkonto i Slack med administratörsbehörigheter för Team 

Obs: Azure AD-etablering integration förlitar sig på den [Slack SCIM API](https://api.slack.com/scim) som är tillgängliga för Slack team på plustecknet plan eller bättre.

## <a name="assigning-users-to-slack"></a>Tilldela användare till Slack

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, kommer endast användare och grupper som har ”tilldelats” till ett program i Azure AD att synkroniseras. 

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till Slack-appen. När bestämt, kan du tilldela dessa användare till Slack appen genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en enterprise-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Viktiga tips för att tilldela användare till Slack

*   Vi rekommenderar att en enda Azure AD-användare tilldelas till Slack testa allokering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Slack, måste du välja den **användaren** eller ”grupp” roll i dialogrutan tilldelning. Rollen ”standard åtkomst” fungerar inte för etablering.


## <a name="configuring-user-provisioning-to-slack"></a>Konfigurering av användarförsörjning till Slack 

Det här avsnittet hjälper dig att ansluta din Azure AD till Slacks användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i Slack baserat på användare och grupptilldelning i Azure AD.

**Tips:** du kan också välja att aktivera SAML-baserade enkel inloggning för Slack, följa instruktionerna i (Azure portal) [https://portal.azure.com]. Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.


### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Konfigurera automatisk konto användaretablering till Slack i Azure AD:


1)  I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

2) Om du redan har konfigurerat Slack för enkel inloggning söka efter din instans av Slack med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Slack** i programgalleriet. Välj Slack i sökresultatet och lägga till den i listan med program.

3)  Välj din instans av Slack och sedan den **etablering** fliken.

4)  Ange den **Etableringsläge** till **automatisk**.

![Slack-etablering](./media/slack-provisioning-tutorial/Slack1.PNG)

5)  Under den **administratörsautentiseringsuppgifter** klickar du på **auktorisera**. En Slack auktorisering dialogruta öppnas i ett nytt webbläsarfönster. 

6) Logga in till Slack med ditt Team administratörskonto i nytt fönster. i dialogrutan resulterande auktorisering väljer du det Slack team som du vill aktivera etablering för och väljer sedan **auktorisera**. När klar, kan du gå tillbaka till Azure-portalen för att slutföra konfigurationen etablering.

![Dialogrutan för auktorisering](./media/slack-provisioning-tutorial/Slack3.PNG)

7) I Azure-portalen klickar du på **Testanslutningen** så Azure AD kan ansluta till Slack-appen. Om anslutningen misslyckas, kontrollera ditt Slack konto som har administratörsbehörigheter för teamet och försök steget ”Godkänn” igen.

8) Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fält och markera kryssrutan nedan.

9) Klicka på **Spara**. 

10) Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare till Slack**.

11) I den **attributmappning** avsnittet kan du granska användarattribut som ska synkroniseras från Azure AD till Slack. Observera att attribut som är markerade som **matchande** egenskaper som används för att matcha användarkonton i Slack för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

12) Om du vill aktivera Azure AD-tjänsten för Slack-etablering, ändra den **Status för etablering** till **på** i den **inställningar** avsnitt

13) Klicka på **Spara**. 

Detta startar den första synkroniseringen av användare och/eller grupper som tilldelas till Slack i avsnittet användare och grupper. Observera att den första synkroniseringen ta längre tid än efterföljande synkroniseringar som sker ungefär var tionde minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapporter som beskriver alla åtgärder som utförs av tjänsten etablering i appen Slack.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Valfritt] Konfigurera grupp objekt etablering till Slack 

Du kan också, aktivera etablering av gruppobjekt från Azure AD till Slack. Detta skiljer sig från ”tilldela grupper av användare”, i den faktiska gruppen objekt förutom dess medlemmar replikeras från Azure AD till Slack. Om du har en grupp med namnet ”min grupp” i Azure AD, till exempel skapas en identitical grupp med namnet ”min grupp” inuti Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Aktivera etablering av gruppobjekt:

1) Välj under avsnittet mappningar **synkronisera Azure Active Directory-grupper till Slack**.

2) Ange aktiverad i bladet attributmappning till Ja.

3) I den **attributmappning** avsnittet kan du granska Gruppattribut som ska synkroniseras från Azure AD till Slack. Observera att attribut som är markerade som **matchande** egenskaper som används för att matcha grupper i Slack för uppdateringsåtgärder. 

4) Klicka på **Spara**.

Det här resultatet i en gruppobjekt som tilldelats Slack i den **användare och grupper** avsnittet fullständigt synkroniseras från Azure AD till Slack. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av tjänsten etablering i appen Slack.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](../active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
