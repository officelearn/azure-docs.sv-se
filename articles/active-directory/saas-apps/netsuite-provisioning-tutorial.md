---
title: 'Självstudiekurs: Konfigurera Netsuite OneWorld för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Netsuite OneWorld.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c9a823e6515c2bfe09e1ab7bcef471eb8169e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063303"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Netsuite för automatisk användaretablering

Syftet med den här självstudien är att visa de steg du behöver för att utföra i Netsuite OneWorld och Azure AD för att automatiskt etablera och avetableras användarkonton från Azure AD till Netsuite.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active-katalogklient.
*   En Netsuite OneWorld-prenumeration. Observera att automatisk användaretablering för närvarande endast stöds med NetSuite OneWorld.
*   Ett användarkonto i Netsuite med administratörsbehörighet.

## <a name="assigning-users-to-netsuite-oneworld"></a>Tilldela användare till Netsuite OneWorld

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till din Netsuite-app. När du har bestämt dig kan du tilldela dessa användare till din Netsuite-app genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Viktiga tips för att tilldela användare till Netsuite OneWorld

*   Vi rekommenderar att en enda Azure AD-användare tilldelas Netsuite för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Netsuite måste du välja en giltig användarroll. Rollen "Standardåtkomst" fungerar inte för etablering.

## <a name="enable-user-provisioning"></a>Aktivera etablering av användare

Det här avsnittet hjälper dig att ansluta din Azure AD till Netsuites API för etablering av användarkonton och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Netsuite baserat på användar- och grupptilldelning i Azure AD.

> [!TIP] 
> Du kan också välja att aktivera SAML-baserade Single Sign-On for Netsuite, enligt instruktionerna i [Azure portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-user-account-provisioning"></a>Så här konfigurerar du etablering av användarkonton:

Syftet med det här avsnittet är att beskriva hur du aktiverar användaretablering av Active Directory-användarkonton till Netsuite.

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

1. Om du redan har konfigurerat Netsuite för enkel inloggning söker du efter din instans av Netsuite med hjälp av sökfältet. Annars väljer du **Lägg till** och sök efter **Netsuite** i programgalleriet. Välj Netsuite från sökresultaten och lägg till den i listan över program.

1. Välj din instans av Netsuite och välj sedan fliken **Etablering.**

1. Ställ in **etableringsläget** på **Automatiskt**. 

    ![Etableringen](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Ange följande konfigurationsinställningar under avsnittet **Administratörsautentiseringsuppgifter:**
   
    a. Skriv ett Netsuite-kontonamn i textrutan **Administratörsanvändarnamn** som har **systemadministratörsprofilen** i Netsuite.com tilldelad.
   
    b. Skriv lösenordet för det här kontot i textrutan **Admin Password.**
      
1. Klicka på Testa **anslutning** i Azure-portalen för att säkerställa att Azure AD kan ansluta till din Netsuite-app.

1. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden och markerar kryssrutan.

1. Klicka på **Spara.**

1. Under avsnittet Mappningar väljer du **Synkronisera Azure Active Directory-användare till Netsuite.**

1. I avsnittet **Attributmappningar** granskar du de användarattribut som synkroniseras från Azure AD till Netsuite. Observera att de attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Netsuite för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

1. Om du vill aktivera Azure AD-etableringstjänsten för Netsuite ändrar **du etableringsstatusen** till **På** i avsnittet Inställningar

1. Klicka på **Spara.**

Den startar den första synkroniseringen av alla användare och/eller grupper som tilldelats Netsuite i avsnittet Användare och grupper. Observera att den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar, som beskriver alla åtgärder som utförs av etableringstjänsten i Netsuite-appen.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](netsuite-tutorial.md)
