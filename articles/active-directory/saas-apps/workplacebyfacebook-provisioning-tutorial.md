---
title: 'Självstudier: Konfigurera arbetsplats av Facebook för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
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
ms.openlocfilehash: 72c2e23b0d60ca242549ebf2c058ea8f44f2b1c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60520137"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Självstudier: Konfigurera arbetsplats av Facebook för automatisk användaretablering

Målet med den här självstudien är att visa dig de steg som du behöver utföra på arbetsplats Facebook och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till arbetsplats med Facebook.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande saker för att konfigurera Azure AD-integrering med Workplace by Facebook:

- En Azure AD-prenumeration
- En arbetsplats av Facebook enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Tilldela användare till arbetsplats med Facebook

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din arbetsplats av Facebook-app. När du bestämt dig kan tilldela du dessa användare till din arbetsplats av Facebook-app genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Viktiga tips för att tilldela användare till arbetsplats med Facebook

*   Vi rekommenderar att en enda Azure AD-användare tilldelas till arbetsplatsen av Facebook att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till arbetsplatsen som Facebook, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="enable-user-provisioning"></a>Aktivera etableringen av användare

Det här avsnittet hjälper dig att ansluta din Azure AD till arbetsplats med Facebooks användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelade användarkonton i arbetsplats av Facebook baserat på användare och grupper tilldelning i Azure AD.

>[!Tip]
>Du kan också välja att aktivera SAML-baserad enkel inloggning för arbetsplats som Facebook, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Konfigurera etablering av användarkonto till arbetsplatsen av Facebook i Azure AD:

Målet med det här avsnittet som beskriver hur du aktivera etablering av Active Directory-användarkonton till arbetsplats med Facebook.

Azure AD stöder möjligheten att automatiskt synkronisera information om kontot för tilldelade användare till arbetsplats med Facebook. Den här automatisk synkronisering kan arbetsplatsen av Facebook och hämta data som behövs för att auktorisera användare för åtkomst, innan de försöker logga in för första gången. Den också etablerar ta bort användare från arbetsplats av Facebook när åtkomst har återkallats i Azure AD.

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory** > **Företagsappar** > **alla program** avsnittet.

2. Om du redan har konfigurerat arbetsplats av Facebook för enkel inloggning, söka efter din arbetsplats genom att använda sökfältet Facebook-instans. Annars väljer **Lägg till** och Sök efter **arbetsplats av Facebook** i programgalleriet. Välj arbetsplats av Facebook i sökresultatet och lägga till den i din lista över program.

3. Välj din arbetsplats med Facebook-instans och välj sedan den **etablering** fliken.

4. Ange den **Etableringsläge** till **automatisk**. 

    ![etablering](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange åtkomst-Token från din arbetsplats av Facebook-administratör och anger du klient-URL-värdet till `https://www.facebook.com/scim/v1/` . Se dessa [instruktioner](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) om hur du skapar en åtkomsttoken för arbetsplats. 

6. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din arbetsplats med Facebook-app. Om anslutningen misslyckas se till att din arbetsplats av Facebook-kontot har administratörsbehörighet för teamet.

7. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan.

8. Klicka på **spara.**

9. Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till arbetsplats med Facebook.**

10. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till arbetsplatsen som Facebook. Attribut som har markerats som **matchande** egenskaper som används så att de matchar användarkontona i arbetsplats av Facebook för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

11. Om du vill aktivera den Azure AD-etableringstjänsten för arbetsplats som Facebook, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet

12. Klicka på **spara.**

Läs mer om hur du konfigurerar automatisk etablering [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Du kan nu skapa ett testkonto. Vänta i upp till 20 minuter att verifiera att kontot har synkroniserats till arbetsplats med Facebook.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](workplacebyfacebook-tutorial.md)
