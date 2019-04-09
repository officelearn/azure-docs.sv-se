---
title: 'Självstudier: Konfigurera Asana för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton i Asana.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: asmalser
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: a763b2516f88e8c92efc321db50dc15881f54c9b
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057854"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Asana för automatisk användaretablering

Målet med den här självstudien är att visa dig de steg som du behöver utföra i Asana och Azure Active Directory (Azure AD) för att automatiskt etablera och avetablera användarkonton från Azure AD till Asana.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure AD-klient
* En Asana-klient med en [Enterprise](https://www.asana.com/pricing) planera eller bättre aktiverat
* Ett användarkonto i Asana med administratörsbehörighet

> [!NOTE]
> Azure AD etablering integration förlitar sig på den [Asana API](https://asana.com/developers/api-reference/users), som är tillgänglig för Asana.

## <a name="assign-users-to-asana"></a>Tilldela användare till Asana

Azure AD använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare som tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare i Azure AD behöver åtkomst till din app i Asana. Du kan sedan tilldela dessa användare till din Asana-app genom att följa instruktionerna här:

[Tilldela en användare till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Viktiga tips för att tilldela användare till Asana

Vi rekommenderar att du tilldelar en enda Azure AD-användare till Asana att testa etablering konfigurationen. Ytterligare användare kan tilldelas senare.

## <a name="configure-user-provisioning-to-asana"></a>Konfigurera användaretablering till Asana

Det här avsnittet hjälper dig att ansluta din Azure AD till användarkontot i Asana etablering API. Du kan också konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Asana baserat på användare i Azure AD.

> [!TIP]
> Om du vill aktivera SAML-baserad enkel inloggning för Asana, följer du instruktionerna i den [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Konfigurera automatisk etablering av användarkonto till Asana i Azure AD

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory** > **Företagsappar** > **alla program** avsnittet.

1. Om du redan har konfigurerat Asana för enkel inloggning kan du söka efter din instans av Asana med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Asana** i programgalleriet. Välj **Asana** i sökresultatet och lägga till den i din lista över program.

1. Välj din instans av Asana och välj sedan den **etablering** fliken.

1. Ange **Etableringsläge** till **automatisk**.

    ![Asana etablering](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. Under den **administratörsautentiseringsuppgifter** , följer du dessa instruktioner för att generera en token och ange den i **hemlighet Token**:

    a. Logga in på [Asana](https://app.asana.com) med ditt administratörskonto.

    b. Välj av profilfoto från den översta raden och välj de aktuella inställningarna för organisationens namn.

    c. Gå till den **tjänstkonton** fliken.

    d. Välj **lägger du till tjänstkontot**.

    e. Uppdatera **namn** och **om** och profilfoto efter behov. Kopiera token i **Token**, och markera den i **spara ändringar**.

1. I Azure-portalen väljer du **Testanslutningen** så att Azure AD kan ansluta till din app i Asana. Om anslutningen misslyckas, kontrollera att din Asana-kontot har administratörsbehörighet och försök på **Testanslutningen** steg igen.

1. Ange e-postadress för en person eller grupp som du vill ta emot meddelanden om etablering fel i **e-postmeddelande**. Markera kryssrutan under.

1. Välj **Spara**.

1. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Asana**.

1. I den **attributmappningar** går du igenom användarattribut som ska synkroniseras från Azure AD med Asana. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Asana för uppdateringsåtgärder. Välj **spara** att genomföra ändringarna. Mer information finns i [anpassa attributmappningar för användare etablera](../manage-apps/customize-application-attributes.md).

1. Så här aktiverar du den Azure AD-etableringstjänsten för Asana, i den **inställningar** ändrar **Etableringsstatus** till **på**.

1. Välj **Spara**.

Nu den första synkroniseringen startar för alla användare som tilldelats Asana i den **användare** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Använd den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar. Granskningsloggarna beskrivs alla åtgärder som utförs av etableringstjänsten i Asana-app.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportera om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](asana-tutorial.md)
