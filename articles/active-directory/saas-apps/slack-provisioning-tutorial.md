---
title: 'Självstudie: Konfigurera Slack för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och användares användarkonton till Slack.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.reviewer: asmalser
ms.openlocfilehash: 9763c7a9e79f4c9e9d6296efb79e944205e8a99c
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264155"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Slack för automatisk användaretablering


Syftet med den här självstudien är att visa dig de steg som du behöver utföra i Slack och Azure AD för att automatiskt etablera och användares användarkonton från Azure AD till Slack. 

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active Directory-klient
*   En Slack-klienten med den [Plus plan](https://aadsyncfabric.slack.com/pricing) eller bättre har aktiverats 
*   Ett användarkonto i Slack med administratörsbehörighet för Team 

Obs: Azure AD etablering integration förlitar sig på den [Slack SCIM API](https://api.slack.com/scim) som är tillgängliga för Slack team på Plus planera eller bättre.

## <a name="assigning-users-to-slack"></a>Tilldela användare till Slack

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, kommer endast de användare och grupper som är ”kopplade” till ett program i Azure AD att synkroniseras. 

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din Slack-app. När du bestämt dig kan tilldela du dessa användare i din Slack-app genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Viktiga tips för att tilldela användare till Slack

*   Vi rekommenderar att en enda Azure AD-användare tilldelas till Slack att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Slack, måste du välja den **användaren** eller den roll som ”grupp” i dialogrutan för tilldelning. Rollen ”standard åtkomst” fungerar inte för etablering.


## <a name="configuring-user-provisioning-to-slack"></a>Konfigurera användaretablering för Slack 

Det här avsnittet hjälper dig att ansluta din Azure AD till Slacks användarkonto etablering API och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Slack baserat på användare och grupptilldelning i Azure AD.

**Tips:** du kan också välja att aktiverat SAML-baserad enkel inloggning för Slack, följa instruktionerna i (Azure-portalen) [https://portal.azure.com]. Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.


### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Konfigurera automatisk etablering av användarkonto till Slack i Azure AD:


1)  I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

2) Om du redan har konfigurerat Slack för enkel inloggning, söka efter din instans av Slack med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Slack** i programgalleriet. Välj Slack i sökresultatet och lägga till den i din lista över program.

3)  Välj din instans av Slack och välj sedan den **etablering** fliken.

4)  Ange den **Etableringsläge** till **automatisk**.

![Slack etablering](./media/slack-provisioning-tutorial/Slack1.PNG)

5)  Under den **administratörsautentiseringsuppgifter** klickar du på **auktorisera**. Då öppnas en dialogruta för Slack auktorisering i ett nytt webbläsarfönster. 

6) Logga in med ditt lags administratör Slack i nytt fönster. i den resulterande auktorisering väljer Slack teamet som du vill aktivera etablering för och välj sedan **auktorisera**. När klar gå tillbaka till Azure portal för att slutföra konfigurationen av etablering.

![Dialogrutan för auktorisering](./media/slack-provisioning-tutorial/Slack3.PNG)

7) I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din Slack-app. Om anslutningen misslyckas, kontrollera din Slack-kontot har administratörsbehörighet för Team och försök ”auktorisera” steg igen.

8) Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan nedan.

9) Klicka på **Spara**. 

10) Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till Slack**.

11) I den **attributmappningar** går du igenom användarattribut som ska synkroniseras från Azure AD till Slack. Observera att attribut som är markerade som **matchande** egenskaper som används för att matcha användarkontona i Slack för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

12) Om du vill aktivera den Azure AD-etableringstjänsten för Slack, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet

13) Klicka på **Spara**. 

Detta startar den första synkroniseringen av användare och/eller grupper som tilldelats till Slack i avsnittet användare och grupper. Observera att den inledande synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker cirka var tionde minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporter som beskriver alla åtgärder som utförs av etableringstjänsten på din Slack-app.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Valfritt] Konfigurera gruppetablering objekt till Slack 

Alternativt kan du aktivera etablering av gruppobjekt från Azure AD till Slack. Detta skiljer sig från ”tilldela grupper av användare”, i den gruppen faktiska objekt utöver dess medlemmar kommer att replikeras från Azure AD till Slack. Om du har en grupp med namnet ”min grupp” i Azure AD, till exempel skapas en identitical grupp med namnet ”min grupp” i Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Att aktivera etablering av gruppobjekt:

1) Under avsnittet mappningar väljer **synkronisera Azure Active Directory-grupper till Slack**.

2) På bladet attributmappning inställt aktiverad på Ja.

3) I den **attributmappningar** går du igenom de Gruppattribut som synkroniseras från Azure AD till Slack. Observera att attribut som är markerade som **matchande** egenskaper som används för att matcha grupper i Slack för uppdateringsåtgärder. 

4) Klicka på **Spara**.

Det här resultatet i någon grupp-objekt som tilldelats Slack i den **användare och grupper** avsnittet fullständigt som synkroniseras från Azure AD till Slack. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten på din Slack-app.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
