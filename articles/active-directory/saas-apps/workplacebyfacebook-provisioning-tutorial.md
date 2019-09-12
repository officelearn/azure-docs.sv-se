---
title: 'Självstudier: Konfigurera arbets ytan efter Facebook för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f040ff4c8e59f764676aa6fdd9460ec94641684a
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881792"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Självstudier: Konfigurera arbets ytan efter Facebook för automatisk användar etablering

Syftet med den här självstudien är att visa de steg som du behöver utföra på arbets ytan av Facebook och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till arbets platsen av Facebook.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande saker för att konfigurera Azure AD-integrering med Workplace by Facebook:

- En Azure AD-prenumeration
- En arbets plats per Facebook-aktiverad prenumeration med enkel inloggning

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Tilldela användare till arbets ytan efter Facebook

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till din arbets plats av Facebook-appen. När du har bestämt dig kan du tilldela dessa användare till din arbets plats av Facebook-appen genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Viktiga tips för att tilldela användare till arbets ytan på Facebook

*   Vi rekommenderar att en enda Azure AD-användare tilldelas arbets platsen av Facebook för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till arbets ytan per Facebook måste du välja en giltig användar roll. Rollen "standard åtkomst" fungerar inte för etablering.

## <a name="enable-user-provisioning"></a>Aktivera användar etablering

Det här avsnittet vägleder dig genom att ansluta din Azure AD till arbets plats med hjälp av Facebooks etablerings-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i arbets platsen av Facebook baserat på användare och grupp tilldelning i Azure AD.

>[!Tip]
>Du kan också välja att aktivera SAML-baserad enkel inloggning för arbets ytan efter Facebook genom att följa anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Konfigurera användar konto etablering till arbets platsen av Facebook i Azure AD:

Syftet med det här avsnittet är att skapa en översikt över hur du aktiverar etablering av Active Directory användar konton till arbets ytan på Facebook.

Azure AD har stöd för att automatiskt synkronisera konto information för tilldelade användare till arbets platsen av Facebook. Med den här automatiska synkroniseringen kan arbets ytan efter Facebook hämta de data som krävs för att auktorisera användare för åtkomst innan de försöker logga in för första gången. De avetablerar även användare från arbets ytan på Facebook när åtkomst har återkallats i Azure AD.

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory** > **Enterprise Apps** > **alla program** .

2. Om du redan har konfigurerat arbets ytan efter Facebook för enkel inloggning söker du efter din arbets plats efter Facebook med Sök fältet. Annars väljer du **Lägg till** och Sök efter **arbets plats efter Facebook** i program galleriet. Välj arbets plats efter Facebook från Sök resultaten och Lägg till den i listan över program.

3. Välj din instans av arbets platsen efter Facebook och välj sedan fliken **etablering** .

4. Ställ in **etablerings läget** på **automatiskt**. 

    ![etablering](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. Under avsnittet **admin credentials** anger du åtkomsttoken från din arbets plats av Facebook-administratören och anger KLIENTens URL-värde `https://www.facebook.com/scim/v1/` till. Se de här [anvisningarna](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) för att skapa en åtkomsttoken för arbets platsen. 

6. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din arbets plats via Facebook-appen. Om anslutningen Miss lyckas ser du till att din arbets plats av Facebook-kontot har team administratörs behörighet.

7. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan.

8. Klicka på **Spara.**

9. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till arbets ytan efter Facebook.**

10. I avsnittet **mappningar för attribut** granskar du de användarattribut som synkroniseras från Azure AD till arbets ytan av Facebook. De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona på arbets platsen av Facebook för uppdaterings åtgärder. Välj knappen Spara för att genomföra ändringarna.

11. Om du vill aktivera Azure AD Provisioning-tjänsten för arbets ytan av Facebook ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar**

12. Klicka på **Spara.**

Mer information om hur du konfigurerar automatisk etablering finns i[https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Nu kan du skapa ett test konto. Vänta i upp till 20 minuter för att kontrol lera att kontot har synkroniserats till arbets platsen av Facebook.

> [!NOTE]
> Vi arbetar tätt med arbets ytan av Facebook-teamet för att se till att Azure AD-programmet är godkänt och uppfyller de nya rikt linjerna.   

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](workplacebyfacebook-tutorial.md)
