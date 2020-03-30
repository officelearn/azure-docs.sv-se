---
title: 'Självstudiekurs: Användaretablering för LucidChart - Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till LucidChart.
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
ms.openlocfilehash: c5d946c6e257c7676178f9bc3c234f66ba6fe622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057336"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera LucidChart för automatisk etablering av användare

Syftet med den här självstudien är att visa de steg du behöver för att utföra i LucidChart och Azure AD för att automatiskt etablera och avetableras användarkonton från Azure AD till LucidChart. 

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klient
* En LucidChart-klient med [Enterprise-planen](https://www.lucidchart.com/user/117598685#/subscriptionLevel) eller bättre aktiverad
* Ett användarkonto i LucidChart med administratörsbehörighet

## <a name="assigning-users-to-lucidchart"></a>Tilldela användare till LucidChart

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till lucidchart-appen. När du har bestämt dig kan du tilldela dessa användare till din LucidChart-app genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Viktiga tips för att tilldela användare till LucidChart

* Vi rekommenderar att en enda Azure AD-användare tilldelas LucidChart för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till LucidChart måste du välja antingen **användarrollen** eller en annan giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. **Rollen Standardåtkomst** fungerar inte för etablering och dessa användare hoppas över.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Konfigurera användaretablering till LucidChart

Det här avsnittet hjälper dig att ansluta din Azure AD till LucidCharts API för etablering av användarkonton och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i LucidChart baserat på användar- och grupptilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för LucidChart, enligt instruktionerna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Konfigurera automatisk etablering av användarkonton till LucidChart i Azure AD

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

2. Om du redan har konfigurerat LucidChart för enkel inloggning söker du efter din instans av LucidChart med hjälp av sökfältet. Annars väljer du **Lägg till** och sök efter **LucidChart** i programgalleriet. Välj LucidChart från sökresultaten och lägg till det i listan över program.

3. Välj din förekomst av LucidChart och välj sedan fliken **Etablering.**

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![LucidChart etablering](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du den **hemliga token** som genereras av luciddiagrammets konto (du hittar token under ditt konto: **Team** > **App Integration** > **SCIM**).

    ![LucidChart etablering](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. Klicka på Testa **anslutning** i Azure-portalen för att säkerställa att Azure AD kan ansluta till lucidchart-appen. Om anslutningen misslyckas kontrollerar du att lucidchartkontot har administratörsbehörighet och försök med steg 5 igen.

7. Ange e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden i fältet **E-postmeddelande** och markera kryssrutan "Skicka ett e-postmeddelande när ett fel inträffar".

8. Klicka på **Spara**.

9. Under avsnittet Mappningar väljer du **Synkronisera Azure Active Directory-användare till LucidChart**.

10. I avsnittet **Attributmappningar** granskar du de användarattribut som synkroniseras från Azure AD till LucidChart. De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i LucidChart för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

11. Om du vill aktivera Azure AD-etableringstjänsten för LucidChart ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar**

12. Klicka på **Spara**.

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som tilldelats LucidChart i avsnittet Användare och grupper. Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar, som beskriver alla åtgärder som utförs av etableringstjänsten.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
