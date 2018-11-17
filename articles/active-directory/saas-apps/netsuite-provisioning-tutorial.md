---
title: 'Självstudie: Konfigurera Netsuite OneWorld för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Netsuite OneWorld.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 81e590297283a8fe88d9257e201259056a3e3744
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823866"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Netsuite för automatisk användaretablering

Målet med den här självstudien är att visa dig de steg du måste utföra i Netsuite OneWorld och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till Netsuite.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   En Netsuite OneWorld-prenumeration. Observera att automatisk användaretablering är för närvarande stöds endast med NetSuite OneWorld.
*   Ett användarkonto i Netsuite med administratörsbehörighet.

## <a name="assigning-users-to-netsuite-oneworld"></a>Tilldela användare till Netsuite OneWorld

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din Netsuite-app. När du valt, kan du tilldela dessa användare till Netsuite appen genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Viktiga tips för att tilldela användare till Netsuite OneWorld

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats Netsuite att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Netsuite, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="enable-user-provisioning"></a>Aktivera etableringen av användare

Det här avsnittet hjälper dig att ansluta din Azure AD till Netsuites användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelade användarkonton i Netsuite baserat på användar- och grupptilldelningar i Azure AD.

> [!TIP] 
> Du kan också välja att aktiveras SAML-baserad enkel inloggning för Netsuite, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-user-account-provisioning"></a>Konfigurera etablering av användarkonto:

Målet med det här avsnittet som beskriver hur du aktiverar etableringen av användare i Active Directory-användarkonton till Netsuite.

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

1. Om du redan har konfigurerat Netsuite för enkel inloggning, söka efter din instans av Netsuite med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Netsuite** i programgalleriet. Välj Netsuite i sökresultatet och lägga till den i din lista över program.

1. Välj din instans av Netsuite och välj sedan den **etablering** fliken.

1. Ange den **Etableringsläge** till **automatisk**. 

    ![etablering](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Under den **administratörsautentiseringsuppgifter** avsnittet tillhandahåller följande konfigurationsinställningar:
   
    a. I den **administratörsanvändarnamn** textrutan typ som en Netsuite kontonamn som har den **systemadministratören** profil i Netsuite.com tilldelad.
   
    b. I den **adminlösenord** textrutan skriver du lösenordet för det här kontot.
      
1. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din Netsuite-app.

1. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden om etablering fel och markera kryssrutan.

1. Klicka på **spara.**

1. Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till Netsuite.**

1. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till Netsuite. Observera att attribut som är markerade som **matchande** egenskaper som används för att matcha användarkontona i Netsuite för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

1. Om du vill aktivera den Azure AD-etableringstjänsten för Netsuite, ändra den **Etableringsstatus** till **på** i avsnittet Inställningar

1. Klicka på **spara.**

Den startar den första synkroniseringen av användare och/eller grupper som tilldelats till Netsuite i avsnittet användare och grupper. Observera att den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten på Netsuite appen.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](netsuite-tutorial.md)
