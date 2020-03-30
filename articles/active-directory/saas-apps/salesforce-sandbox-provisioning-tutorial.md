---
title: 'Självstudiekurs: Konfigurera Salesforce Sandbox för automatisk användaretablering med Azure Active Directory| Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48df954f680c6774b0aa189453615156f4f0b4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063279"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Salesforce Sandbox för automatisk etablering av användare

Syftet med den här självstudien är att visa de steg du behöver utföra i Salesforce Sandbox och Azure AD för att automatiskt etablera och avetableras användarkonton från Azure AD till Salesforce Sandbox.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active-katalogklient.
*   En giltig klient för Salesforce Sandbox for Work eller Salesforce Sandbox for Education. Du kan använda ett kostnadsfritt utvärderingskonto för båda tjänsterna.
*   Ett användarkonto i Salesforce Sandbox med behörigheten Teamadministratör.

## <a name="assigning-users-to-salesforce-sandbox"></a>Tilldela användare till Salesforce Sandbox

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare eller grupper i Azure AD som behöver åtkomst till din Salesforce Sandbox-app. När du har fattat det här beslutet kan du tilldela dessa användare till appen Salesforce Sandbox genom att följa instruktionerna i [Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Viktiga tips för att tilldela användare till Salesforce Sandbox

* Vi rekommenderar att en enda Azure AD-användare tilldelas Salesforce Sandbox för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Salesforce Sandbox måste du välja en giltig användarroll. Rollen "Standardåtkomst" fungerar inte för etablering.

> [!NOTE]
> Den här appen importerar anpassade roller från Salesforce Sandbox som en del av etableringsprocessen, som kunden kanske vill välja när användare tilldelas.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk etablering av användare

Det här avsnittet hjälper dig att ansluta din Azure AD till Salesforce Sandboxs API för etablering av användarkonton och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Salesforce Sandbox baserat på användare och grupp tilldelning i Azure AD.

>[!Tip]
>Du kan också välja att aktivera SAML-baserad enkel inloggning för Salesforce Sandbox, enligt instruktionerna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurera automatisk etablering av användarkonton

Syftet med det här avsnittet är att beskriva hur du aktiverar användaretablering av Active Directory-användarkonton i Salesforce Sandbox.

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

1. Om du redan har konfigurerat Salesforce Sandbox för enkel inloggning söker du efter din instans av Salesforce Sandbox med hjälp av sökfältet. Annars väljer du **Lägg till** och sök efter **Salesforce Sandbox** i programgalleriet. Välj Salesforce Sandbox i sökresultaten och lägg till den i listan över program.

1. Välj din instans av Salesforce Sandbox och välj sedan fliken **Etablering.**

1. Ställ in **etableringsläget** på **Automatiskt**.

    ![Etableringen](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. Ange följande konfigurationsinställningar under avsnittet **Administratörsautentiseringsuppgifter:**
   
    a. Skriv ett Salesforce Sandbox-kontonamn i textrutan **Admin Username** som har **systemadministratörsprofilen** i Salesforce.com tilldelad.
   
    b. Skriv lösenordet för det här kontot i textrutan **Admin Password.**

1. Om du vill hämta säkerhetstoken för Salesforce Sandbox öppnar du en ny flik och loggar in på samma Salesforce Sandbox-administratörskonto. Klicka på ditt namn längst upp till höger på sidan och klicka sedan på **Inställningar**.

     ![Aktivera automatisk etablering av användare](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Aktivera automatisk etablering av användare")

1. Klicka på **Min personliga information** i det vänstra navigeringsfönstret för att expandera det relaterade avsnittet och klicka sedan på Återställ min **säkerhetstoken**.
  
    ![Aktivera automatisk etablering av användare](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Aktivera automatisk etablering av användare")

1. Klicka på knappen **Återställ säkerhetstoken** på sidan Återställ **säkerhetstoken.**

    ![Aktivera automatisk etablering av användare](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Aktivera automatisk etablering av användare")

1. Kontrollera e-postkorgen som är kopplad till det här administratörskontot. Leta efter ett e-postmeddelande från Salesforce Sandbox.com som innehåller den nya säkerhetstoken.

1. Kopiera token, gå till ditt Azure AD-fönster och klistra in den i fältet **Hemlig token.**

1. Klicka på Testa **anslutning** i Azure-portalen för att säkerställa att Azure AD kan ansluta till din Salesforce Sandbox-app.

1. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden och markerar kryssrutan.

1. Klicka på **Spara.**  
    
1.  Under avsnittet Mappningar väljer du **Synkronisera Azure Active Directory-användare till Salesforce Sandbox.**

1. I avsnittet **Attributmappningar** granskar du de användarattribut som synkroniseras från Azure AD till Salesforce Sandbox. De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Salesforce Sandbox för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

1. Om du vill aktivera Azure AD-etableringstjänsten för Salesforce Sandbox ändrar **du etableringsstatusen** till **På** i avsnittet Inställningar

1. Klicka på **Spara.**

Den startar den första synkroniseringen av alla användare och/eller grupper som tilldelats Salesforce Sandbox i avsnittet Användare och grupper. Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar, som beskriver alla åtgärder som utförs av etableringstjänsten i Salesforce Sandbox-appen.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
