---
title: 'Självstudier: Konfigurera LinkedIn höjer för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till LinkedIn höjer.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2018
ms.author: asmalser-msft
ms.openlocfilehash: bff7b819bfe0d0a2bd62b4b6452fe64b6c12beee
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182633"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Självstudier: Konfigurera LinkedIn höjer för automatisk användaretablering


Målet med den här självstudien är att visa dig vad du behöver för att utföra i LinkedIn höjer och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD att upphöja LinkedIn. 

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active Directory-klientorganisation
*   En LinkedIn höjer klient 
*   Ett administratörskonto i LinkedIn höjer med åtkomst till Kontocenter LinkedIn

> [!NOTE]
> Azure Active Directory kan integreras med LinkedIn höjer med hjälp av den [SCIM](http://www.simplecloud.info/) protokoll.

## <a name="assigning-users-to-linkedin-elevate"></a>Tilldela användare till LinkedIn höjer

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, kommer endast de användare och grupper som är ”kopplade” till ett program i Azure AD att synkroniseras. 

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till LinkedIn höjer. När du bestämt dig kan tilldela du dessa användare att upphöja LinkedIn genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Viktiga tips för att tilldela användare till LinkedIn höjer

*   Vi rekommenderar att en enda Azure AD-användare tilldelas till LinkedIn höjer att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare att upphöja LinkedIn, måste du välja den **användaren** roll i dialogrutan för tilldelning. Rollen ”standard åtkomst” fungerar inte för etablering.


## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Konfigurera användaretablering för LinkedIn höjer

Det här avsnittet hjälper dig att ansluta din Azure AD till LinkedIn höjer SCIM användarkonto etablering API och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i LinkedIn höjer baserat på användare och grupptilldelning i Azure AD.

**Tips:** Du kan också välja att aktiveras SAML-baserad enkel inloggning för LinkedIn höjer, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna kompletterar varandra.


### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Konfigurera automatisk etablering av användarkonto att LinkedIn upphöja i Azure AD:


Det första steget är att hämta din LinkedIn-åtkomsttoken. Om du är en Enterprise-administratör kan själv du en åtkomst-token. I din kontocenter, går du till **inställningar &gt; globala inställningar** och öppna den **SCIM installationsprogrammet** panelen.

> [!NOTE]
> Om du kommer åt kontocentret direkt i stället för via en länk, kan du nå den med hjälp av följande steg.

1)  Logga in på kontot Center.

2)  Välj **Admin &gt; administratörsinställningar** .

3)  Klicka på **avancerade integreringar** i vänster Sidopanel. Du dirigeras till kontocenter.

4)  Klicka på **+ Lägg till ny SCIM konfiguration** , och följa genom att fylla i fälten.

> När autoassign licenser inte är aktiverad, betyder det att endast användardata som är synkroniserad.

![LinkedIn höjer etablering](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

> När autolicense tilldelning har aktiverats kan behöva du Observera programinstans och licenstyp. Licenser har tilldelats på en första kommer kan först fungerar basis tills kommer alla licenser.

![LinkedIn höjer etablering](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5)  Klicka på **generera token**. Du bör se din token visas under den **åtkomsttoken** fält.

6)  Spara ditt åtkomst-token till Urklipp eller datorn innan de lämnar sidan.

7) Logga sedan in på den [Azure-portalen](https://portal.azure.com), och bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

8) Om du redan har konfigurerat LinkedIn höjer för enkel inloggning, söka efter din instans av LinkedIn höjer med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **LinkedIn höjer** i programgalleriet. Välj LinkedIn höjer i sökresultatet och lägga till den i din lista över program.

9)  Välj din instans av LinkedIn höjer och välj sedan den **etablering** fliken.

10) Ange den **Etableringsläge** till **automatisk**.

![LinkedIn höjer etablering](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11)  Fyll i följande fält under **administratörsautentiseringsuppgifter** :

* I den **klient-URL** anger https://api.linkedin.com.

* I den **hemlighet Token** fältet, ange den åtkomsttoken som du genererade i steg 1 och klicka på **Testanslutningen** .

* Du bör se ett meddelande om lyckad på upperright sida av portalen.

12) Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan nedan.

13) Klicka på **Spara**. 

14) I den **attributmappningar** går du igenom användar- och attribut som synkroniseras från Azure AD till LinkedIn höjer. Observera att attribut som är markerade som **matchande** egenskaper som används för att matcha de användarkonton och grupper i LinkedIn höjer för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

![LinkedIn höjer etablering](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15) Om du vill aktivera den Azure AD-etableringstjänsten för LinkedIn höjer, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet

16) Klicka på **Spara**. 

Detta startar den första synkroniseringen av användare och/eller grupper som tilldelats till LinkedIn höjer i avsnittet användare och grupper. Observera att den inledande synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten på LinkedIn höjer appen.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
