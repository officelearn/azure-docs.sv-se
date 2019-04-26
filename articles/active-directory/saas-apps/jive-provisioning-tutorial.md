---
title: 'Självstudier: Konfigurera Jive för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
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
ms.openlocfilehash: 607d538a2a2636e17265e95195000a777f162dc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60263381"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Jive för automatisk användaretablering

Målet med den här självstudien är att visa dig vad du behöver för att utföra i Jive och Azure AD att automatiskt etablera och användares användarkonton från Azure AD till Jive.

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   En Jive enkel inloggning aktiverad prenumeration.
*   Ett användarkonto i Jive Team administratörsbehörigheter.

## <a name="assigning-users-to-jive"></a>Tilldela användare till Jive

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din Jive-app. När du valt, kan du tilldela dessa användare till Jive appen genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Viktiga tips för att tilldela användare till Jive

*   Vi rekommenderar att en enda Azure AD-användare tilldelas till Jive att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Jive, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="enable-user-provisioning"></a>Aktivera etableringen av användare

Det här avsnittet hjälper dig att ansluta din Azure AD till Jives användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelade användarkonton i Jive baserat på användar- och grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktiverat SAML-baserad enkel inloggning för Jive, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-user-account-provisioning"></a>Konfigurera etablering av användarkonto:

Målet med det här avsnittet som beskriver hur du aktiverar etableringen av användare i Active Directory-användarkonton till Jive.
Som en del av den här proceduren måste måste du tillhandahålla en säkerhetstoken för användaren som du måste begära från Jive.com.

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

1. Om du redan har konfigurerat Jive för enkel inloggning, söka efter din instans av Jive med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Jive** i programgalleriet. Välj Jive i sökresultatet och lägga till den i din lista över program.

1. Välj din instans av Jive och välj sedan den **etablering** fliken.

1. Ange den **Etableringsläge** till **automatisk**. 

    ![etablering](./media/jive-provisioning-tutorial/provisioning.png)

1. Under den **administratörsautentiseringsuppgifter** avsnittet tillhandahåller följande konfigurationsinställningar:
   
    a. I den **Jive administratörsanvändarnamn** textrutan typ som en Jive kontonamn som har den **systemadministratören** profil i Jive.com tilldelad.
   
    b. I den **Jive adminlösenord** textrutan skriver du lösenordet för det här kontot.
   
    c. I den **Jive klient-URL** textrutan skriver Jive klient-URL.
      
      > [!NOTE]
      > Jive klient-URL är Webbadressen som används av din organisation för att logga in på Jive.  
      > Normalt URL: en har följande format: **www.\< organisation\>. jive.com**.          

1. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din Jive-app.

1. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan nedan.

1. Klicka på **spara.**

1. Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till Jive.**

1. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till Jive. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Jive för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

1. Om du vill aktivera den Azure AD-etableringstjänsten för Jive, ändra den **Etableringsstatus** till **på** i avsnittet Inställningar

1. Klicka på **spara.**

Den startar den första synkroniseringen av användare och/eller grupper som tilldelats till Jive i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten på din Jive-app.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](jive-tutorial.md)
