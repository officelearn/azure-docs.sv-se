---
title: 'Självstudier: Konfigurera Asana för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Asana.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 2abd10f90d2e6d86d7f82aa43016a31bfdbd324b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231431"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Asana för automatisk användaretablering

Syftet med den här kursen är att visa de steg som du behöver göra i Asana och Azure Active Directory (Azure AD) för att automatiskt etablera och avetablera användarkonton från Azure AD till Asana.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure AD-klient
*   En Asana-klient med en [Enterprise](https://www.asana.com/pricing) plan eller bättre aktiverad 
*   Ett användarkonto i Asana med administratörsbehörigheter 

> [!NOTE] 
> Azure AD-etablering integration förlitar sig på den [Asana API](https://app.asana.com/api/1.0/scim/Users), som är tillgänglig för Asana.

## <a name="assign-users-to-asana"></a>Tilldela användare till Asana

Azure AD använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare som tilldelats ett program i Azure AD. 

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare i Azure AD behöver åtkomst till appen Asana. Du kan sedan tilldela dessa användare i appen Asana genom att följa anvisningarna här:

[Tilldela en användare till en enterprise-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Viktiga tips för att tilldela användare till Asana

Vi rekommenderar att du ger en enda Azure AD-användare till Asana att testa allokering konfigurationen. Ytterligare användare kan tilldelas senare.

## <a name="configure-user-provisioning-to-asana"></a>Konfigurera användaretablering till Asana 

Det här avsnittet hjälper dig att ansluta din Azure AD till Asana användarkonto etablering API. Du kan också konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i Asana baserat på användare i Azure AD.

> [!TIP]
> Om du vill aktivera SAML-baserade enkel inloggning för Asana, följ instruktionerna i den [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Så här konfigurerar du automatiska konto användaretablering till Asana i Azure AD

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory** > **Företagsappar** > **alla program** avsnitt.

2. Om du redan har konfigurerat Asana för enkel inloggning kan du söka efter din instans av Asana med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Asana** i programgalleriet. Välj **Asana** i sökresultatet och lägga till den i listan över program.

3. Välj din instans av Asana och välj sedan den **etablering** fliken.

4. Ange **Etableringsläge** till **automatisk**.

    ![Asana etablering](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

5. Under den **administratörsautentiseringsuppgifter** , följer du dessa instruktioner för att skapa token och ange den i **hemlighet Token**:

    a. Logga in på [Asana](https://app.asana.com) med ditt administratörskonto.

    b. Välj profilfoto från det översta fältet och väljer de aktuella inställningarna för organisationens namn.

    c. Gå till den **tjänstkonton** fliken.

    d. Välj **lägga till tjänstkontot**.

    e. Uppdatera **namn** och **om** och profilfoto efter behov. Kopiera token i **Token**, och markera den i **spara ändringar**.

6. I Azure portal, väljer **Testanslutningen** så att Azure AD kan ansluta till din Asana app. Om anslutningen misslyckas, kontrollera att ditt konto för Asana har administratörsbehörigheter och försök den **Testanslutningen** steg igen.

7. Ange e-postadressen för en person eller grupp som du vill ta emot meddelanden om etablering fel i **e-postmeddelande**. Markera kryssrutan under.

8. Välj **Spara**. 

9. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare Asana**.

10. I den **attributmappning** avsnittet kan du granska användarattribut ska synkroniseras från Azure AD med Asana. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i Asana för uppdateringsåtgärder. Välj **spara** att genomföra ändringarna. Mer information finns i [anpassa användaren etablera attributmappning](../active-directory-saas-customizing-attribute-mappings.md).

11. Aktivera Azure AD etableras för Asana, i den **inställningar** ändrar **Status för etablering** till **på**.

12. Välj **Spara**. 

Nu den första synkroniseringen startar för alla användare som tilldelats Asana i den **användare** avsnitt. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var 40 minuter så länge som tjänsten körs. Använd den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsloggar. Granskningsloggarna beskrivs alla åtgärder som utförs av tjänsten etablering Asana appen.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapport om automatisk konto användaretablering](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](asana-tutorial.md)
