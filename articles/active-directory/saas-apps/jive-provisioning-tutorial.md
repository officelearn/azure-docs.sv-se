---
title: 'Självstudiekurs: Konfigurera Jive för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6fbfdbe7-d66c-4305-9fea-76d6a6a92830
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602eed65745eea1fd9096508c442a27ea79bcba9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057742"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Jive för automatisk etablering av användare

Syftet med den här självstudien är att visa de steg du behöver för att utföra i Jive och Azure AD för att automatiskt etablera och avetableras användarkonton från Azure AD till Jive.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active-katalogklient.
*   En Jive enkel inloggning aktiverad prenumeration.
*   Ett användarkonto i Jive med behörigheten Teamadministratör.

## <a name="assigning-users-to-jive"></a>Tilldela användare till Jive

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till din Jive-app. När du har bestämt dig kan du tilldela dessa användare till din Jive-app genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Viktiga tips för att tilldela användare till Jive

*   Vi rekommenderar att en enda Azure AD-användare tilldelas Jive för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Jive måste du välja en giltig användarroll. Rollen "Standardåtkomst" fungerar inte för etablering.

## <a name="enable-user-provisioning"></a>Aktivera etablering av användare

Det här avsnittet hjälper dig att ansluta din Azure AD till Jives API för etablering av användarkonton och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Jive baserat på användar- och grupptilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Jive, enligt instruktionerna i [Azure portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-user-account-provisioning"></a>Så här konfigurerar du etablering av användarkonton:

Syftet med det här avsnittet är att beskriva hur du aktiverar användaretablering av Active Directory-användarkonton till Jive.
Som en del av den här proceduren måste du ange en säkerhetstoken för användare som du måste begära från Jive.com.

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

1. Om du redan har konfigurerat Jive för enkel inloggning söker du efter din förekomst av Jive med hjälp av sökfältet. Annars väljer du **Lägg till** och söker efter **Jive** i programgalleriet. Välj Jive från sökresultaten och lägg till det i listan över program.

1. Välj din förekomst av Jive och välj sedan fliken **Etablering.**

1. Ställ in **etableringsläget** på **Automatiskt**. 

    ![Etableringen](./media/jive-provisioning-tutorial/provisioning.png)

1. Ange följande konfigurationsinställningar under avsnittet **Administratörsautentiseringsuppgifter:**
   
    a. Skriv ett Jive-kontonamn i textrutan Användarnamn för **Jive Admin** som har **systemadministratörsprofilen** i Jive.com tilldelad.
   
    b. Skriv lösenordet för det här kontot i textrutan Lösenord för **Jive Admin.**
   
    c. Skriv url:en för Jive Tenant URL i textrutan **Jive Tenant.**
      
      > [!NOTE]
      > Jive-klientadressen är URL som används av din organisation för att logga in på Jive.  
      > Vanligtvis har webbadressen följande format: **www.\< organisation\>.jive.com**.          

1. Klicka på Testa **anslutning** i Azure-portalen för att säkerställa att Azure AD kan ansluta till din Jive-app.

1. Ange e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden i fältet **E-postmeddelande** och markera kryssrutan nedan.

1. Klicka på **Spara.**

1. Under avsnittet Mappningar väljer du **Synkronisera Azure Active Directory-användare till Jive.**

1. I avsnittet **Attributmappningar** granskar du de användarattribut som synkroniseras från Azure AD till Jive. De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Jive för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

1. Om du vill aktivera Azure AD-etableringstjänsten för Jive ändrar **du etableringsstatusen** till **På** i avsnittet Inställningar

1. Klicka på **Spara.**

Den startar den första synkroniseringen av alla användare och/eller grupper som tilldelats Jive i avsnittet Användare och grupper. Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar, som beskriver alla åtgärder som utförs av etableringstjänsten i din Jive-app.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](jive-tutorial.md)
