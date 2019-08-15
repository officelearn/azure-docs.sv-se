---
title: 'Självstudier: Konfigurera ServiceNow för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85783339c7d1348f598f924f14d9b40cd0c8cd22
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967170"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Självstudier: Konfigurera ServiceNow för automatisk användar etablering med Azure Active Directory

Syftet med den här självstudien är att visa de steg du behöver utföra i ServiceNow och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till ServiceNow.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med ServiceNow behöver du följande:

- En Azure AD-prenumeration
- För ServiceNow, en instans eller klientorganisation för ServiceNow, Calgary-versionen eller senare
- För ServiceNow Express, en instans av ServiceNow Express, Helsingfors-versionen eller senare

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en utvärderings miljö för Azure AD kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

## <a name="assigning-users-to-servicenow"></a>Tilldela användare till ServiceNow

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till ServiceNow-appen. När du har bestämt dig kan du tilldela dessa användare till ServiceNow-appen genom att följa anvisningarna här: [Tilldela en användare eller grupp till en företags app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Vi rekommenderar att en enda Azure AD-användare tilldelas ServiceNow för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.
>*   När du tilldelar en användare till ServiceNow måste du välja en giltig användar roll. Rollen "standard åtkomst" fungerar inte för etablering.
>*   Mer information om hur du skapar och konfigurerar roller i Azure AD finns i den här [länken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användar etablering

Det här avsnittet vägleder dig genom att ansluta din Azure AD till ServiceNow-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i ServiceNow baserat på användar-och grupp tilldelning i Azure AD.

> [!TIP]
>Du kan också välja att aktivera SAML-baserad enkel inloggning för ServiceNow enligt anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurera automatisk etablering av användar konto

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program** .

1. Om du redan har konfigurerat ServiceNow för enkel inloggning söker du efter din instans av ServiceNow med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **ServiceNow** i program galleriet. Välj ServiceNow från Sök resultaten och Lägg till den i listan över program.

1. Välj din instans av ServiceNow och välj sedan fliken **etablering** .

1. Ställ in **etablerings** läget på **automatiskt**. 

    ![etablering](./media/servicenow-provisioning-tutorial/provisioning.png)

1. Under avsnittet admin credentials, utför följande steg:
   
    a. I text rutan **instans namn för ServiceNow** anger du namnet på ServiceNow-instansen.

    b. Skriv användar namnet för en administratör i text rutan **ServiceNow admin-användarnamn** .

    c. Administratörens lösen ord i text rutan administratörs **lösen ord för ServiceNow** .

1. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din ServiceNow-app. Om anslutningen Miss lyckas kontrollerar du att ditt ServiceNow-konto har team administratörs behörighet och försöker sedan igen med **"admin credentials"** .

1. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan.

1. Klicka på **Spara.**

1. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till ServiceNow.**

1. I avsnittet **mappningar för attribut** granskar du de användarattribut som synkroniseras från Azure AD till ServiceNow. Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i ServiceNow för uppdaterings åtgärder. Välj knappen Spara för att genomföra ändringarna.

1. Om du vill aktivera Azure AD Provisioning-tjänsten för ServiceNow ändrar du **etablerings statusen** till **på** i avsnittet Inställningar

1. Klicka på **Spara.**

Den första synkroniseringen av alla användare och/eller grupper som är kopplade till ServiceNow startas i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten i ServiceNow-appen.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](servicenow-tutorial.md)


