---
title: 'Självstudie: användar etablering för LucidChart – Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till LucidChart.
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
ms.openlocfilehash: 3791992586edbdc5188c3078b1f1bb108ce580d7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276854"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Självstudie: Konfigurera LucidChart för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i LucidChart och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till LucidChart. 

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klient
* En LucidChart-klient med [företags planen](https://www.lucidchart.com/user/117598685#/subscriptionLevel) eller bättre aktive rad
* Ett användar konto i LucidChart med administratörs behörighet

## <a name="assigning-users-to-lucidchart"></a>Tilldela användare till LucidChart

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till LucidChart-appen. När du har bestämt dig kan du tilldela dessa användare till LucidChart-appen genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Viktiga tips för att tilldela användare till LucidChart

* Vi rekommenderar att en enda Azure AD-användare tilldelas LucidChart för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till LucidChart måste du välja antingen **användar** rollen eller en annan giltig programspecifik roll (om tillgänglig) i tilldelnings dialog rutan. **Standard åtkomst** rollen fungerar inte för etablering, och dessa användare hoppas över.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Konfigurera användar etablering till LucidChart

Det här avsnittet vägleder dig genom att ansluta din Azure AD till LucidChart-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i LucidChart baserat på användar-och grupp tilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för LucidChart enligt anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Konfigurera automatisk etablering av användar konton till LucidChart i Azure AD

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program** .

2. Om du redan har konfigurerat LucidChart för enkel inloggning söker du efter din instans av LucidChart med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **Lucidchart** i program galleriet. Välj LucidChart från Sök resultaten och Lägg till den i listan över program.

3. Välj din instans av LucidChart och välj sedan fliken **etablering** .

4. Ställ in **etablerings läget** på **automatiskt**.

    ![LucidChart-etablering](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. Under avsnittet **admin credentials** måste du skriva in den **hemliga token** som genererats av ditt Lucidchart-konto (du kan hitta token under ditt konto: **team** > **app integration** > **scim**).

    ![LucidChart-etablering](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din Lucidchart-app. Om anslutningen Miss lyckas kontrollerar du att LucidChart-kontot har administratörs behörighet och försöker sedan steg 5 igen.

7. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan "Skicka ett e-postmeddelande när ett fel inträffar".

8. Klicka på **Save** (Spara).

9. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till Lucidchart**.

10. I avsnittet **mappningar för attribut** granskar du de användarattribut som synkroniseras från Azure AD till Lucidchart. Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Lucidchart för uppdaterings åtgärder. Välj knappen Spara för att genomföra ändringarna.

11. Om du vill aktivera Azure AD Provisioning-tjänsten för LucidChart ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar**

12. Klicka på **Save** (Spara).

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som har tilldelats LucidChart i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
