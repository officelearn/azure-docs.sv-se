---
title: 'Självstudie: Konfigurera DocuSign för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 6099c07a0f27966eb4c253b85d24afb0561a708b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44345548"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Självstudie: Konfigurera DocuSign för automatisk användaretablering

Målet med den här självstudien är att visa dig de steg du måste utföra i DocuSign och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till DocuSign.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   En DocuSign enkel inloggning aktiverat prenumeration.
*   Ett användarkonto i DocuSign Team administratörsbehörigheter.

## <a name="assigning-users-to-docusign"></a>Tilldela användare till DocuSign

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din DocuSign-app. När du bestämt dig kan tilldela du dessa användare till din DocuSign-app genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Viktiga tips för att tilldela användare till DocuSign

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats DocuSign att testa etablering konfigurationen. Ytterligare användare kan tilldelas senare.

*   När du tilldelar en användare till DocuSign, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

> [!NOTE]
> Azure AD stöder inte gruppetablering med Docusign-programmet, endast användare kan etableras.

## <a name="enable-user-provisioning"></a>Aktivera etableringen av användare

Det här avsnittet hjälper dig att ansluta din Azure AD till Docusigns användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelade användarkonton i DocuSign baserat på användar- och grupptilldelningar i Azure AD.

> [!Tip]
> Du kan också välja att aktiveras SAML-baserad enkel inloggning för DocuSign, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-user-account-provisioning"></a>Konfigurera etablering av användarkonto:

Målet med det här avsnittet som beskriver hur du aktiverar etableringen av användare i Active Directory-användarkonton till DocuSign.

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

1. Om du redan har konfigurerat DocuSign för enkel inloggning, söka efter din instans av DocuSign med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **DocuSign** i programgalleriet. Välj DocuSign i sökresultatet och lägga till den i din lista över program.

1. Välj din instans av DocuSign och välj sedan den **etablering** fliken.

1. Ange den **Etableringsläge** till **automatisk**. 

    ![etablering](./media/docusign-provisioning-tutorial/provisioning.png)

1. Under den **administratörsautentiseringsuppgifter** avsnittet tillhandahåller följande konfigurationsinställningar:
   
    a. I den **administratörsanvändarnamn** textrutan typ som en DocuSign kontonamn som har den **systemadministratören** profil i DocuSign.com tilldelad.
   
    b. I den **adminlösenord** textrutan skriver du lösenordet för det här kontot.

1. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din DocuSign-app.

1. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden om etablering fel och markera kryssrutan.

1. Klicka på **spara.**

1. Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till DocuSign.**

1. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till DocuSign. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i DocuSign för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

1. Om du vill aktivera den Azure AD-etableringstjänsten för DocuSign, ändra den **Etableringsstatus** till **på** i avsnittet Inställningar

1. Klicka på **spara.**

Den startar den första synkroniseringen av alla användare som tilldelats DocuSign i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten på DocuSign-app.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](docusign-tutorial.md)