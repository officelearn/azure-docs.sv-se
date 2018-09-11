---
title: 'Självstudie: Konfigurera ThousandEyes för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till ThousandEyes.
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
ms.openlocfilehash: d2912c687d4968a239d5af747df4115ffd71bbeb
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44345768"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Självstudie: Konfigurera ThousandEyes för automatisk användaretablering


Målet med den här självstudien är att visa dig de steg du måste utföra i ThousandEyes och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till ThousandEyes. 

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient
*   En ThousandEyes klient med den [standardprenumerationen](https://www.thousandeyes.com/pricing) eller bättre har aktiverats 
*   Ett användarkonto i ThousandEyes med administratörsbehörighet 

> [!NOTE]
> Azure AD etablering integration förlitar sig på den [ThousandEyes SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), som är tillgängliga för ThousandEyes team på Standard-avtalet eller bättre.

## <a name="assigning-users-to-thousandeyes"></a>Tilldela användare till ThousandEyes

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din ThousandEyes-app. När du valt, kan du tilldela dessa användare till ThousandEyes appen genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Viktiga tips för att tilldela användare till ThousandEyes

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats ThousandEyes att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till ThousandEyes, måste du välja antingen den **användaren** roll eller en annan giltig programspecifika roll (om tillgängligt) i dialogrutan för tilldelning. Den **standard åtkomst** rollen fungerar inte för etablering och dessa användare hoppas över.


## <a name="configuring-user-provisioning-to-thousandeyes"></a>Konfigurera användaretablering för ThousandEyes 

Det här avsnittet hjälper dig att ansluta din Azure AD till Thousandeyess användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelade användarkonton i ThousandEyes baserat på användar- och grupptilldelningar i Azure AD .

> [!TIP]
> Du kan också välja att aktiveras SAML-baserad enkel inloggning för ThousandEyes, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.


### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Konfigurera automatisk etablering av användarkonto till ThousandEyes i Azure AD


1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

2. Om du redan har konfigurerat ThousandEyes för enkel inloggning, söka efter din instans av ThousandEyes med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **ThousandEyes** i programgalleriet. Välj ThousandEyes i sökresultatet och lägga till den i din lista över program.

3. Välj din instans av ThousandEyes och välj sedan den **etablering** fliken.

4. Ange den **Etableringsläge** till **automatisk**.

    ![ThousandEyes etablering](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **hemlighet Token** genereras av din ThousandEyes konto (du hittar token under kontot ThousandEyes: **Security & Autentisering**). 

    ![ThousandEyes etablering](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din ThousandEyes-app. Om anslutningen misslyckas, kontrollera ThousandEyes-kontot har administratörsbehörighet och försök steg 5 igen.

7. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan ”Skicka ett e-postmeddelande när ett fel uppstår”.

8. Klicka på **Spara**. 

9. Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till ThousandEyes**.

10. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till ThousandEyes. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i ThousandEyes för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

11. Om du vill aktivera den Azure AD-etableringstjänsten för ThousandEyes, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet

12. Klicka på **Spara**. 

Den här åtgärden startar den första synkroniseringen av användare och/eller grupper som tilldelats till ThousandEyes i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)
