---
title: 'Självstudier: Konfigurera LucidChart för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till LucidChart.
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
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: f58c0906e97c28a0fc63eb50c7224063471a45f2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192340"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Självstudier: Konfigurera LucidChart för automatisk användaretablering


Målet med den här självstudien är att visa dig de steg du måste utföra i LucidChart och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till LucidChart. 

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient
*   En LucidChart klient med den [företagsplanen](https://www.lucidchart.com/user/117598685#/subscriptionLevel) eller bättre har aktiverats 
*   Ett användarkonto i LucidChart med administratörsbehörighet 

## <a name="assigning-users-to-lucidchart"></a>Tilldela användare till LucidChart

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din LucidChart-app. När du valt, kan du tilldela dessa användare till LucidChart appen genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Viktiga tips för att tilldela användare till LucidChart

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats LucidChart att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till LucidChart, måste du välja antingen den **användaren** roll eller en annan giltig programspecifika roll (om tillgängligt) i dialogrutan för tilldelning. Den **standard åtkomst** rollen fungerar inte för etablering och dessa användare hoppas över.


## <a name="configuring-user-provisioning-to-lucidchart"></a>Konfigurera användaretablering för LucidChart 

Det här avsnittet hjälper dig att ansluta din Azure AD till Lucidcharts användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelade användarkonton i LucidChart baserat på användar- och grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktiveras SAML-baserad enkel inloggning för LucidChart, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.


### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Konfigurera automatisk etablering av användarkonto till LucidChart i Azure AD


1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

2. Om du redan har konfigurerat LucidChart för enkel inloggning, söka efter din instans av LucidChart med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **LucidChart** i programgalleriet. Välj LucidChart i sökresultatet och lägga till den i din lista över program.

3. Välj din instans av LucidChart och välj sedan den **etablering** fliken.

4. Ange den **Etableringsläge** till **automatisk**.

    ![LucidChart etablering](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **hemlighet Token** genereras av din LucidChart konto (du hittar token under ditt konto: **Team** > **Appintegrering** > **SCIM**). 

    ![LucidChart etablering](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din LucidChart-app. Om anslutningen misslyckas, kontrollera LucidChart-kontot har administratörsbehörighet och försök steg 5 igen.

7. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan ”Skicka ett e-postmeddelande när ett fel uppstår”.

8. Klicka på **Spara**. 

9. Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till LucidChart**.

10. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till LucidChart. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i LucidChart för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

11. Om du vill aktivera den Azure AD-etableringstjänsten för LucidChart, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet

12. Klicka på **Spara**. 

Den här åtgärden startar den första synkroniseringen av användare och/eller grupper som tilldelats till LucidChart i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)
