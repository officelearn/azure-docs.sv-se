---
title: 'Självstudiekurs: Konfigurera GoToMeeting för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0ac06fc3018b4230cbf32712067c48400599082
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058271"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera GoToMeeting för automatisk användaretablering

Syftet med den här självstudien är att visa de steg du behöver för att utföra i GoToMeeting och Azure AD för att automatiskt etablera och avetableras användarkonton från Azure AD till GoToMeeting.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active-katalogklient.
*   En GoToMeeting enkel inloggning aktiverad prenumeration.
*   Ett användarkonto i GoToMeeting med behörigheter för teamadministratör.

## <a name="assigning-users-to-gotomeeting"></a>Tilldela användare till GoToMeeting

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till din GoToMeeting-app. När du har bestämt dig kan du tilldela dessa användare till din GoToMeeting-app genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Viktiga tips för att tilldela användare till GoToMeeting

*   Vi rekommenderar att en enda Azure AD-användare tilldelas GoToMeeting för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till GoToMeeting måste du välja en giltig användarroll. Rollen "Standardåtkomst" fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk etablering av användare

Det här avsnittet hjälper dig att ansluta ditt Azure AD till GoToMeetings API för etablering av användarkonton och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i GoToMeeting baserat på användar- och grupptilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för GoToMeeting, enligt instruktionerna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning"></a>Så här konfigurerar du automatisk etablering av användarkonton:

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

1. Om du redan har konfigurerat GoToMeeting för enkel inloggning söker du efter din instans av GoToMeeting med hjälp av sökfältet. Annars väljer du **Lägg till** och söker efter **GoToMeeting** i programgalleriet. Välj GoToMeeting från sökresultaten och lägg till det i listan över program.

1. Välj din förekomst av GoToMeeting och välj sedan fliken **Etablering.**

1. Ställ in **etableringsläget** på **Automatiskt**. 

    ![Etableringen](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. Gör följande under avsnittet Administratörsautentiseringsuppgifter:
   
    a. Skriv användarnamnet för en administratör i textrutan **GoToMeeting Admin User Name.**

    b. I textrutan **GoToMeeting Admin Password,** administratörens lösenord.

1. Klicka på Testa **anslutning** i Azure-portalen för att säkerställa att Azure AD kan ansluta till din GoToMeeting-app. Om anslutningen misslyckas kontrollerar du att ditt GoToMeeting-konto har behörigheten Teamadministratör och försöker med steget **"Administratörsautentiseringsuppgifter"** igen.

1. Ange e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden i fältet **E-postmeddelande** och markera kryssrutan.

1. Klicka på **Spara.**

1. Under avsnittet Mappningar väljer du **Synkronisera Azure Active Directory-användare till GoToMeeting.**

1. I avsnittet **Attributmappningar** granskar du de användarattribut som synkroniseras från Azure AD till GoToMeeting. De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i GoToMeeting för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

1. Om du vill aktivera Azure AD-etableringstjänsten för GoToMeeting ändrar **du etableringsstatusen** till **På** i avsnittet Inställningar

1. Klicka på **Spara.**

Den startar den första synkroniseringen av alla användare och/eller grupper som tilldelats GoToMeeting i avsnittet Användare och grupper. Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar, som beskriver alla åtgärder som utförs av etableringstjänsten i gotomeeting-appen.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


