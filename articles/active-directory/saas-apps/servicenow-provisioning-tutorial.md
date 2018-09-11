---
title: 'Självstudie: Konfigurera ServiceNow för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablera och avetablera användarkonton från Azure AD till ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: b3ef6e2a6b9b51c271372aa3c9342b52a4260788
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44348114"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Självstudie: Konfigurera ServiceNow för automatisk användaretablering med Azure Active Directory

Målet med den här självstudien är att visa dig de steg du måste utföra i ServiceNow och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till ServiceNow.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ServiceNow, behöver du följande objekt:

- En Azure AD-prenumeration
- För ServiceNow, en instans eller klient för ServiceNow Calgary versionen eller senare
- För ServiceNow Express, en instans av ServiceNow uttryckliga, Helsingfors versionen eller senare

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).


## <a name="assigning-users-to-servicenow"></a>Tilldela användare till ServiceNow

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din ServiceNow-app. När du valt, du kan tilldela dessa användare till din ServiceNow-app genom att följa instruktionerna här: [tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Vi rekommenderar att en enda Azure AD-användare är tilldelad till ServiceNow att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.
>*   När du tilldelar en användare till ServiceNow, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användaretablering

Det här avsnittet hjälper dig att ansluta din Azure AD till Servicenows användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelade användarkonton i ServiceNow baserat på användar- och grupptilldelningar i Azure AD.

> [!TIP]
>Du kan också välja att aktiveras SAML-baserad enkel inloggning för ServiceNow, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens kontoetablering

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

1. Om du redan har konfigurerat ServiceNow för enkel inloggning, söka efter din instans av ServiceNow med sökfältet. Annars väljer **Lägg till** och Sök efter **ServiceNow** i programgalleriet. Välj ServiceNow i sökresultatet och lägga till den i din lista över program.

1. Välj din ServiceNow-instans och välj sedan den **etablering** fliken.

1. Ange den **etablering** läge till **automatisk**. 

    ![etablering](./media/servicenow-provisioning-tutorial/provisioning.png)

1. Under avsnittet autentiseringsuppgifter som administratör utför du följande steg:
   
    a. I den **ServiceNow-instansnamn** textrutan skriver du namnet på ServiceNow-instansen.

    b. I den **ServiceNow-användarnamn för administratör** textrutan skriver du användarnamnet för en administratör.

    c. I den **ServiceNow adminlösenord** textrutan administratörens lösenord.

1. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din ServiceNow-app. Om anslutningen misslyckas, kontrollera din ServiceNow-kontot har administratörsbehörighet för Team och försök på **”administratörsautentiseringsuppgifter”** steg igen.

1. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan.

1. Klicka på **spara.**

1. Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till ServiceNow.**

1. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till ServiceNow. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i ServiceNow för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

1. Om du vill aktivera den Azure AD-etableringstjänsten för ServiceNow, ändra den **Etableringsstatus** till **på** i avsnittet Inställningar

1. Klicka på **spara.**

Den startar den första synkroniseringen av användare och/eller grupper som har tilldelats till ServiceNow i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten på din ServiceNow-app.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](servicenow-tutorial.md)


