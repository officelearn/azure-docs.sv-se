---
title: 'Självstudier: Konfigurera GitHub för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till GitHub.
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
ms.openlocfilehash: d3b2f8860f5b8ddd2cc53d8805b7eace788e73a7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36230547"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Självstudier: Konfigurera GitHub för automatisk användaretablering


Syftet med den här kursen är att visa de steg som du behöver göra i GitHub och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till GitHub. 

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient
*   En Github-klient med den [företagsplan](https://help.github.com/articles/organization-billing-plans/#business-plan) eller bättre aktiverat 
*   Ett användarkonto i GitHub med administratörsbehörigheter 

> [!NOTE]
> Azure AD-etablering integration förlitar sig på den [GitHub SCIM API](https://developer.github.com/v3/scim/), som är tillgängliga för Github-team på Business-plan eller bättre.

## <a name="assigning-users-to-github"></a>Tilldela användare till GitHub

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare och grupper som har ”tilldelats” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till dina GitHub-app. När bestämt, kan du tilldela dessa användare till GitHub-app genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en enterprise-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Viktiga tips för att tilldela användare till GitHub

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats GitHub för att testa allokering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare GitHub, måste du välja någon av **användaren** roll eller en annan giltig programspecifika roll (om tillgängliga) i dialogrutan tilldelning. Den **standard åtkomst** roll fungerar inte för etablering och dessa användare hoppas över.


## <a name="configuring-user-provisioning-to-github"></a>Konfigurering av användarförsörjning i GitHub 

Det här avsnittet hjälper dig att ansluta din Azure AD till Githubs användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i GitHub baserat på tilldelning av användare och grupper i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för GitHub, följer du instruktionerna som anges i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.


### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Konfigurera automatisk konto användaretablering till GitHub i Azure AD


1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

2. Om du redan har konfigurerat GitHub för enkel inloggning söka efter din instans av GitHub med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **GitHub** i programgalleriet. Välj GitHub i sökresultatet och lägga till den i listan med program.

3. Välj din instans av GitHub och sedan den **etablering** fliken.

4. Ange den **Etableringsläge** till **automatisk**.

    ![GitHub-etablering](./media/github-provisioning-tutorial/GitHub1.png)

5. Under den **administratörsautentiseringsuppgifter** klickar du på **auktorisera**. Den här åtgärden öppnar en dialogruta för GitHub-auktorisering i ett nytt webbläsarfönster. 

6. Logga in på GitHub med ditt administratörskonto i nytt fönster. I dialogrutan resulterande auktorisering väljer du det GitHub-team som du vill aktivera etablering för och väljer sedan **auktorisera**. När klar, kan du gå tillbaka till Azure-portalen för att slutföra konfigurationen etablering.

    ![Dialogrutan för auktorisering](./media/github-provisioning-tutorial/GitHub2.png)

7. I Azure-portalen indata **klient URL** och klicka på **Testanslutningen** så Azure AD kan ansluta till dina GitHub-app. Om anslutningen misslyckas, se till att ditt GitHub-konto som har administratörsbehörigheter och **klient URl** inputted är korrekt och försök igen ”Godkänn” steg (du kan utgöra **klient URL** av regel ”:https://api.github.com/scim/v2/organizations/ + < Organizations_name > ”, du kan hitta din organisationer under GitHub-konto: **inställningar** > **organisationer**).

    ![Dialogrutan för auktorisering](./media/github-provisioning-tutorial/GitHub3.png)

8. Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fält och markera kryssrutan ”Skicka ett e-postmeddelande när ett fel uppstår”.

9. Klicka på **Spara**. 

10. Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare till GitHub**.

11. I den **attributmappning** avsnittet kan du granska användarattribut som synkroniseras från Azure AD till GitHub. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i GitHub för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

12. Om du vill aktivera Azure AD-tjänsten för GitHub-etablering, ändra den **Status för etablering** till **på** i den **inställningar** avsnitt

13. Klicka på **Spara**. 

Den här åtgärden startar den första synkroniseringen av användare och/eller grupper som tilldelas till GitHub i avsnittet användare och grupper. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var 40 minuter så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av tjänsten etablering.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](../active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granska loggarna och få rapporter om etablering aktivitet](../active-directory-saas-provisioning-reporting.md)
