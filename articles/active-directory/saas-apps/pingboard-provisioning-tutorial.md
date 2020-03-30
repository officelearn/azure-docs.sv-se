---
title: 'Självstudiekurs: Användaretablering för Pingboard - Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Pingboard.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: c64c40d93f9b525ac6adeca276797df65f32ef3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061283"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Pingboard för automatisk användaretablering

Syftet med den här självstudien är att visa de steg du behöver följa för att aktivera automatisk etablering och avetablering av användarkonton från Azure Active Directory (Azure AD) till Pingboard.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure AD-klient
* Ett [Pingboard-klientpro-konto](https://pingboard.com/pricing)
* Ett användarkonto i Pingboard med administratörsbehörighet

> [!NOTE]
> Azure AD-etableringsintegrationen är beroende av [Pingboard-API:et](https://pingboard.docs.apiary.io/#), som är tillgängligt för ditt konto.

## <a name="assign-users-to-pingboard"></a>Tilldela användare pingboard

Azure AD använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda program. I samband med automatisk etablering av användarkonto synkroniseras endast de användare som tilldelats ett program i Azure AD. 

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare i Azure AD som behöver åtkomst till Pingboard-appen. Sedan kan du tilldela dessa användare till din Pingboard-app genom att följa instruktionerna här:

[Tilldela en användare till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Viktiga tips för att tilldela användare till Pingboard

Vi rekommenderar att du tilldelar en enda Azure AD-användare till Pingboard för att testa etableringskonfigurationen. Ytterligare användare kan tilldelas senare.

## <a name="configure-user-provisioning-to-pingboard"></a>Konfigurera användaretablering till Pingboard 

Det här avsnittet hjälper dig att ansluta din Azure AD till Pingboard-användarkontots etablerings-API. Du kan också konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Pingboard som baseras på användartilldelningar i Azure AD.

> [!TIP]
> Om du vill aktivera SAML-baserad enkel inloggning för Pingboard följer du instruktionerna i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Så här konfigurerar du automatisk etablering av användarkonton till Pingboard i Azure AD

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till avsnittet **Alla Azure Active Directory** > **Enterprise Apps-** > **program.**

1. Om du redan har konfigurerat Pingboard för enkel inloggning söker du efter din instans av Pingboard med hjälp av sökfältet. Annars väljer du **Lägg till** och sök efter **Pingboard** i programgalleriet. Välj **Pingboard** från sökresultaten och lägg till det i listan över program.

1. Välj din instans av Pingboard och välj sedan fliken **Etablering.**

1. Ange **etableringsläge** till **Automatiskt**.

    ![Etablering av pingboard](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. Gör så här under avsnittet **Administratörsautentiseringsuppgifter:**

    a. I **Klient-URL**anger du `https://your_domain.pingboard.com/scim/v2`och ersätter "your_domain" med din riktiga domän.

    b. Logga in på [Pingboard](https://pingboard.com/) med ditt administratörskonto.

    c. Välj > **Tilläggsintegreringar** > **Azure Active Directory**. **Add-Ons**

    d. Gå till fliken **Konfigurera** och välj **Aktivera användaretablering från Azure**.

    e. Kopiera token i **OAuth Bearer Token**och ange den i **hemlig token**.

1. I Azure-portalen väljer du **Testa anslutning** för att testa Azure AD kan ansluta till din Pingboard-app. Om anslutningen misslyckas testar du att Pingboard-kontot har administratörsbehörighet och försöker med steget **Testa anslutning** igen.

1. Ange e-postadressen till en person eller grupp som du vill få etableringsfelmeddelanden i **e-postmeddelande .** Markera kryssrutan under.

1. Välj **Spara**.

1. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Pingboard**.

1. I avsnittet **Attributmappningar** granskar du de användarattribut som ska synkroniseras från Azure AD till Pingboard. De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Pingboard för uppdateringsåtgärder. Välj **Spara** om du vill genomföra eventuella ändringar. Mer information finns i [Anpassa attributmappningar för användare.](../app-provisioning/customize-application-attributes.md)

1. Om du vill aktivera Azure AD-etableringstjänsten för Pingboard ändrar **du etableringsstatus** till **På**i avsnittet **Inställningar.**

1. Välj **Spara** om du vill starta den första synkroniseringen av användare som tilldelats Pingboard.

Den första synkroniseringen tar längre tid att köra än följande synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs. Använd avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar. Loggarna beskriver alla åtgärder som vidtas av etableringstjänsten i Pingboard-appen.

Mer information om hur du läser Azure AD-etableringsloggarna finns i Rapportera om automatisk etablering av [användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för företagsappar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](pingboard-tutorial.md)
