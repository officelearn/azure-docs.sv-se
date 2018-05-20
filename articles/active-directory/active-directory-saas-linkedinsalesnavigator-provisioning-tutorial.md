---
title: 'Självstudier: Konfigurera LinkedIn försäljning Navigator för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till LinkedIn försäljning Navigator.
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
ms.openlocfilehash: 56060d11373f767b023b092cf01ceb894f70efed
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Självstudier: Konfigurera LinkedIn försäljning Navigator för automatisk användaretablering


Syftet med den här kursen är att visa de steg som du behöver göra i LinkedIn försäljning Navigator och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till LinkedIn försäljning Navigator. 

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active Directory-klient
*   En LinkedIn försäljning Navigator-klient 
*   Ett administratörskontot i LinkedIn försäljning Navigator med åtkomst till LinkedIn Account Center

> [!NOTE]
> Azure Active Directory kan integreras med LinkedIn försäljning Navigator med hjälp av den [SCIM](http://www.simplecloud.info/) protokoll.

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Tilldela användare till LinkedIn försäljning Navigator

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, kommer endast användare och grupper som har ”tilldelats” till ett program i Azure AD att synkroniseras. 

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till LinkedIn försäljning Navigator. När bestämt, kan du tilldela dessa användare till LinkedIn försäljning Navigator genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Viktiga tips för att tilldela användare till LinkedIn försäljning Navigator

*   Vi rekommenderar att en enda Azure AD-användare tilldelas LinkedIn försäljning Navigator testa allokering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare LinkedIn försäljning Navigator, måste du välja den **användaren** roll i dialogrutan tilldelning. Rollen ”standard åtkomst” fungerar inte för etablering.


## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Konfigurering av användarförsörjning LinkedIn försäljning Navigator

Det här avsnittet visar hur du ansluter din Azure AD till LinkedIn försäljning Navigator SCIM användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i LinkedIn försäljning Navigator baserat på användare och grupptilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för LinkedIn försäljning Navigator, följer du instruktionerna som anges i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner kompletterar varandra.


### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Konfigurera automatisk konto användaretablering LinkedIn försäljning Navigator i Azure AD:


Det första steget är att hämta LinkedIn-åtkomsttoken. Om du är företagsadministratör kan etablera du själv en åtkomst-token. Gå till i din kontocenter **inställningar &gt; globala inställningar** och öppna den **SCIM installationsprogrammet** panelen.

> [!NOTE]
> Om du ansluter till mitt konto direkt i stället för via en länk, kan du nå den med hjälp av följande steg.

1)  Logga in på kontot Center.

2)  Välj **Admin &gt; administrationsinställningar** .

3)  Klicka på **avancerade integreringar** på vänster sidopanelen. Du dirigeras till mitt konto.

4)  Klicka på **+ Lägg till ny SCIM konfiguration** och följer du proceduren genom att fylla i varje fält.

> När autoassign licenser inte är aktiverad, innebär det att endast användardata är synkroniserad.

![LinkedIn försäljning Navigator etablering](./media/active-directory-saas-linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

> När autolicense tilldelning har aktiverats måste du Observera programinstansen och licenstypen. Licenser är kopplade till en första komma, först hantera bas tills alla licenser tas.

![LinkedIn försäljning Navigator etablering](./media/active-directory-saas-linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5)  Klicka på **skapa token**. Du bör se din token visas under den **åtkomsttoken** fältet.

6)  Spara åtkomst-token till Urklipp eller en dator innan du lämnar sidan.

7) Logga sedan in till den [Azure-portalen](https://portal.azure.com), och bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

8) Om du redan har konfigurerat LinkedIn försäljning Navigator för enkel inloggning söka efter din instans av LinkedIn försäljning Navigator med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **LinkedIn försäljning Navigator** i programgalleriet. Välj LinkedIn försäljning Navigator i sökresultatet och lägga till den i listan med program.

9)  Välj din instans av LinkedIn försäljning Navigator och sedan den **etablering** fliken.

10) Ange den **Etableringsläge** till **automatisk**.

![LinkedIn försäljning Navigator etablering](./media/active-directory-saas-linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11)  Fyll i följande fält under **administratörsautentiseringsuppgifter** :

* I den **klient URL** anger https://api.linkedin.com.

* I den **hemlighet Token** skriver den åtkomst-token som du genererade i steg 1 och klicka på **Testanslutningen** .

* Du bör se ett meddelande om lyckade på upperright sida av portalen.

12) Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fält och markera kryssrutan nedan.

13) Klicka på **Spara**. 

14) I den **attributmappning** avsnittet kan du granska de användar- och attribut som ska synkroniseras från Azure AD LinkedIn försäljning Navigator. Observera att attribut som är markerade som **matchande** egenskaper som används för att matcha användarkonton och grupper i LinkedIn försäljning Navigator för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

![LinkedIn försäljning Navigator etablering](./media/active-directory-saas-linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15) Om du vill aktivera Azure AD etableras för LinkedIn försäljning Navigator ändra den **Status för etablering** till **på** i den **inställningar** avsnitt

16) Klicka på **Spara**. 

Detta startar den första synkroniseringen av användare och/eller grupper som tilldelas till LinkedIn försäljning Navigator i avsnittet användare och grupper. Observera att den första synkroniseringen ta längre tid än efterföljande synkroniseringar som sker ungefär var 40 minuter så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av tjänsten etablering i appen LinkedIn försäljning Navigator.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-enterprise-apps-manage-provisioning.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
