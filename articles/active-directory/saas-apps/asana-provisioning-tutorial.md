---
title: 'Självstudiekurs: Användaretablering för Asana - Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Asana.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: abeac030db419f7fb7d561df5dcd407684f20ca2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058939"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Asana för automatisk etablering av användare

Syftet med den här självstudien är att visa de steg du behöver för att utföra i Asana och Azure Active Directory (Azure AD) för att automatiskt etablera och avetableras användarkonton från Azure AD till Asana.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure AD-klient
* En Asana-klient med en [Enterprise-plan](https://www.asana.com/pricing) eller bättre aktiverad
* Ett användarkonto i Asana med administratörsbehörighet

> [!NOTE]
> Azure AD-etableringsintegrationen är beroende av [Asana-API:et](https://asana.com/developers/api-reference/users), som är tillgängligt för Asana.

## <a name="assign-users-to-asana"></a>Tilldela användare till Asana

Azure AD använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast de användare som tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare i Azure AD som behöver åtkomst till Asana-appen. Sedan kan du tilldela dessa användare till din Asana-app genom att följa instruktionerna här:

[Tilldela en användare till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Viktiga tips för att tilldela användare till Asana

Vi rekommenderar att du tilldelar en enda Azure AD-användare till Asana för att testa etableringskonfigurationen. Ytterligare användare kan tilldelas senare.

## <a name="configure-user-provisioning-to-asana"></a>Konfigurera användaretablering till Asana

Det här avsnittet vägleder dig genom att ansluta ditt Azure AD till Asana-api för etablering av användarkonton. Du kan också konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Asana baserat på användartilldelningar i Azure AD.

> [!TIP]
> Om du vill aktivera SAML-baserad enkel inloggning för Asana följer du instruktionerna i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Så här konfigurerar du automatisk etablering av användarkonton till Asana i Azure AD

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till avsnittet **Alla Azure Active Directory** > **Enterprise Apps-** > **program.**

1. Om du redan har konfigurerat Asana för enkel inloggning söker du efter din instans av Asana med hjälp av sökfältet. Annars väljer du **Lägg till** och sök efter **Asana** i programgalleriet. Välj **Asana** i sökresultaten och lägg till det i listan över program.

1. Markera din förekomst av Asana och välj sedan fliken **Etablering.**

1. Ange **etableringsläge** till **Automatiskt**.

    ![Asana Etablering](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. Under avsnittet **Administratörsautentiseringar** följer du dessa instruktioner för att generera token och anger den i **hemlig token:**

    a. Logga in på [Asana](https://app.asana.com) med ditt administratörskonto.

    b. Välj profilfotot i det övre fältet och välj dina aktuella inställningar för organisationsnamn.

    c. Gå till fliken **Tjänstkonton.**

    d. Välj **Lägg till tjänstkonto**.

    e. Uppdatera **namn** och **om** och profilfotot efter behov. Kopiera token i **Token**och välj den i **Spara ändringar**.

1. I Azure-portalen väljer du **Testa anslutning** för att säkerställa att Azure AD kan ansluta till din Asana-app. Om anslutningen misslyckas kontrollerar du att Asana-kontot har administratörsbehörighet och försöker med steget **Testa anslutning** igen.

1. Ange e-postadressen till en person eller grupp som du vill få etableringsfelmeddelanden i **e-postmeddelande .** Markera kryssrutan under.

1. Välj **Spara**.

1. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Asana**.

1. I avsnittet **Attributmappningar** granskar du de användarattribut som ska synkroniseras från Azure AD till Asana. De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Asana för uppdateringsåtgärder. Välj **Spara** om du vill genomföra eventuella ändringar. Mer information finns i [Anpassa attributmappningar för användaretableringar](../app-provisioning/customize-application-attributes.md).

1. Om du vill aktivera Azure AD-etableringstjänsten för Asana ändrar **du etableringsstatus** till **Settings** **På**.

1. Välj **Spara**.

Nu startar den första synkroniseringen för alla användare som tilldelats Asana i avsnittet **Användare.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs. Använd avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar. Granskningsloggarna beskriver alla åtgärder som utförs av etableringstjänsten i Asana-appen.

Mer information om hur du läser Azure AD-etableringsloggarna finns i Rapportera om automatisk etablering av [användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](asana-tutorial.md)
