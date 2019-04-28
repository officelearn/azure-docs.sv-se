---
title: 'Självstudier: Konfigurera Pingboard för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Pingboard.
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
ms.openlocfilehash: d2ab7f58c3061044583baf9db73e193966d7d4eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60902760"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Pingboard för automatisk användaretablering

Syftet med den här självstudien är att visa dig de steg du måste följa för att aktivera automatisk aktivering och inaktivering av användarkonton från Azure Active Directory (AD Azure) till Pingboard.

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure AD-klient
* En Pingboard klient [Pro-konto](https://pingboard.com/pricing)
* Ett användarkonto i Pingboard med administratörsbehörighet

> [!NOTE]
> Azure AD etablering integration förlitar sig på den [Pingboard API](https://pingboard.docs.apiary.io/#), som är tillgängliga för ditt konto.

## <a name="assign-users-to-pingboard"></a>Tilldela användare till Pingboard

Azure AD använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda program. I samband med automatisk användarkontoetablering, synkroniseras de användare som tilldelats till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare i Azure AD behöver åtkomst till din Pingboard-app. Du kan sedan tilldela dessa användare till Pingboard appen genom att följa instruktionerna här:

[Tilldela en användare till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Viktiga tips för att tilldela användare till Pingboard

Vi rekommenderar att du tilldelar en enda Azure AD-användare till Pingboard att testa etablering konfigurationen. Ytterligare användare kan tilldelas senare.

## <a name="configure-user-provisioning-to-pingboard"></a>Konfigurera användaretablering till Pingboard 

Det här avsnittet hjälper dig att ansluta din Azure AD till användarkontot Pingboard etablering API. Du kan också konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Pingboard, som baseras på användartilldelningar i Azure AD.

> [!TIP]
> Om du vill aktivera SAML-baserad enkel inloggning för Pingboard, följer du instruktionerna i den [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Konfigurera automatisk etablering av användarkonto till Pingboard i Azure AD

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory** > **Företagsappar** > **alla program** avsnittet.

1. Om du redan har konfigurerat Pingboard för enkel inloggning kan du söka efter din instans av Pingboard med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Pingboard** i programgalleriet. Välj **Pingboard** i sökresultatet och lägga till den i din lista över program.

1. Välj din instans av Pingboard och välj sedan den **etablering** fliken.

1. Ange **Etableringsläge** till **automatisk**.

    ![Pingboard etablering](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. Under den **administratörsautentiseringsuppgifter** använder du följande steg:

    a. I **klient-URL**, ange `https://your_domain.pingboard.com/scim/v2`, och Ersätt ”domän” med din verkliga domän.

    b. Logga in på [Pingboard](https://pingboard.com/) med ditt administratörskonto.

    c. Välj **tillägg** > **integreringar** > **Azure Active Directory**.

    d. Gå till den **konfigurera** och sedan **aktivera etableringen av användare från Azure**.

    e. Kopiera token i **OAuth ägar-Token**, och ange den i **hemlighet Token**.

1. I Azure-portalen väljer du **Testanslutning** att testa Azure AD kan ansluta till din Pingboard-app. Om anslutningen misslyckas, testa att Pingboard-kontot har administratörsbehörighet och försök på **Testanslutning** steg igen.

1. Ange e-postadress för en person eller grupp som du vill ta emot meddelanden om etablering fel i **e-postmeddelande**. Markera kryssrutan under.

1. Välj **Spara**.

1. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Pingboard**.

1. I den **attributmappningar** går du igenom användarattribut som ska synkroniseras från Azure AD med Pingboard. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Pingboard för uppdateringsåtgärder. Välj **spara** att genomföra ändringarna. Mer information finns i [anpassa attributmappningar för användaretablering](../manage-apps/customize-application-attributes.md).

1. Så här aktiverar du den Azure AD-etableringstjänsten för Pingboard, i den **inställningar** ändrar **Etableringsstatus** till **på**.

1. Välj **spara** att starta den första synkroniseringen av användare som tilldelats Pingboard.

Den första synkroniseringen tar längre tid än följande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Använd den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar. Loggarna beskrivs alla åtgärder som vidtas av etableringstjänsten på Pingboard appen.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportera om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](pingboard-tutorial.md)
