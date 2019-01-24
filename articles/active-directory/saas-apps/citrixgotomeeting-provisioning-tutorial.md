---
title: 'Självstudier: Konfigurera GoToMeeting för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 1368bccfd71013f4f3c84148be1e74ffc98e3a51
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815692"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Självstudier: Konfigurera GoToMeeting för automatisk användaretablering

Målet med den här självstudien är att visa dig de steg du måste utföra i GoToMeeting och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till GoToMeeting.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   Ett GoToMeeting enkel inloggning aktiverat prenumeration.
*   Ett användarkonto i GoToMeeting med administratörsbehörighet för teamet.

## <a name="assigning-users-to-gotomeeting"></a>Tilldela användare till GoToMeeting

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din GoToMeeting-app. När du valt, kan du tilldela dessa användare till GoToMeeting appen genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Viktiga tips för att tilldela användare till GoToMeeting

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats GoToMeeting att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till GoToMeeting, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk Användaretablering

Det här avsnittet hjälper dig att ansluta din Azure AD till Gotomeetings användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelade användarkonton i GoToMeeting baserat på användar- och grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktiveras SAML-baserad enkel inloggning för GoToMeeting, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarkontoetablering:

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

1. Om du redan har konfigurerat GoToMeeting för enkel inloggning, söka efter din instans av GoToMeeting med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **GoToMeeting** i programgalleriet. Välj GoToMeeting i sökresultatet och lägga till den i din lista över program.

1. Välj din instans av GoToMeeting och välj sedan den **etablering** fliken.

1. Ange den **etablering** läge till **automatisk**. 

    ![etablering](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. Under avsnittet autentiseringsuppgifter som administratör utför du följande steg:
   
    a. I den **GoToMeeting administratörsanvändarnamn** textrutan skriver du användarnamnet för en administratör.

    b. I den **GoToMeeting adminlösenord** textrutan administratörens lösenord.

1. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din GoToMeeting-app. Om anslutningen misslyckas, kontrollera din GoToMeeting-kontot har administratörsbehörighet för Team och försök på **”administratörsautentiseringsuppgifter”** steg igen.

1. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan.

1. Klicka på **spara.**

1. Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till GoToMeeting.**

1. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till GoToMeeting. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i GoToMeeting för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

1. Om du vill aktivera den Azure AD-etableringstjänsten för GoToMeeting, ändra den **Etableringsstatus** till **på** i avsnittet Inställningar

1. Klicka på **spara.**

Den startar den första synkroniseringen av användare och/eller grupper som tilldelats till GoToMeeting i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten på GoToMeeting appen.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


