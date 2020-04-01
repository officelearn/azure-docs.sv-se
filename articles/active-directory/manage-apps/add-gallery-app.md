---
title: Lägga till en galleriapp - Azure Active Directory | Microsoft-dokument
description: Lär dig hur du lägger till en app från Azure AD-galleriet i dina Azure-företagsprogram.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0a826c499790c86a20ea7061075a6c3e66b3a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73062591"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Lägga till en galleriapp i din Azure AD-organisation

Azure Active Directory (Azure AD) har ett galleri som innehåller tusentals förintegrerade program som är aktiverade med enstaka inloggning i Företag. I den här artikeln beskrivs de allmänna stegen för att lägga till en app från galleriet i din Azure AD-organisation.

> [!IMPORTANT]
> Kontrollera först om det finns någon app i [listan över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Du hittar förmodligen steg-för-steg-vägledning för att lägga till och konfigurera den galleriapp som du vill lägga till.

## <a name="add-a-gallery-application"></a>Lägga till ett galleriprogram

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din Azure AD-klientorganisation, som administratör för molnprogram eller som programadministratör.

1. Välj Azure Active Directory på den vänstra navigeringspanelen i [Azure-portalen](https://portal.azure.com). **Azure Active Directory**

1. Välj **Enterprise-program**i fönstret **Azure Active Directory** .

    ![Öppna företagsprogram](media/add-gallery-app/open-enterprise-apps.png)


3. Om du vill lägga till en galleriapp i din klientorganisation väljer du **Nytt program**.

    ![Välj Nytt program om du vill lägga till en galleriapp i din klientorganisation](media/add-gallery-app/new-application.png)

 4. Växla till den nya förhandsgranskningsupplevelsen i galleriet: I banderollen högst upp på **sidan Lägg till ett program**väljer du länken som säger Klicka här för att prova det nya och förbättrade **appgalleriet**.

5. **Bläddra Azure AD Gallery-fönstret** öppnas och visar paneler för molnplattformar, lokala program och utvalda program. Observera att de program som anges i avsnittet **Utvalda program** har ikoner som anger om de stöder federerade enkel inloggning (SSO) och etablering.

    ![Söka efter en app efter namn eller kategori](media/add-gallery-app/browse-gallery.png)

6. Bläddra i galleriet efter det program du vill lägga till eller sök efter programmet genom att ange dess namn i sökrutan. Välj sedan programmet från resultaten. (Valfritt) I formuläret kan du redigera programnamnet så att det matchar organisationens behov.

    ![Visar hur du lägger till ett program från galleriet](media/add-gallery-app/create-application.png)

7. Välj **Skapa**. En komma igång-sida visas med alternativ för att konfigurera programmet för din organisation.

## <a name="configure-user-sign-in-properties"></a>Konfigurera egenskaper för användarinloggning

1. Välj **Egenskaper** om du vill öppna egenskapsfönstret för redigering.

    ![Fönstret Redigera egenskaper](media/add-gallery-app/edit-properties.png)

1. Ange följande alternativ för att avgöra hur användare som tilldelas eller inte tilldelats programmet kan logga in på programmet och om en användare kan se programmet på åtkomstpanelen.

    - **Aktiverad för användare att logga in** bestämmer huruvida användare som är tilldelade till programmet kan logga in.
    - **Användartilldelning krävs** avgör om användare som inte är tilldelade till programmet kan logga in.
    - **Synlig för användaren** bestämmer huruvida användare som är tilldelade till en app kan se den i åtkomstpanelen och O365-appfönstret.

      Beteende för **tilldelade** användare:

       | Egenskapsinställningar för program | | | Upplevelse för tilldelad användare | |
       |---|---|---|---|---|
       | Aktiverad för användare att logga in? | Användartilldelning krävs? | Synlig för användare? | Kan tilldelade användare logga in? | Kan tilldelade användare se programmet?* |
       | ja | ja | ja | ja | ja  |
       | ja | ja | nej  | ja | nej   |
       | ja | nej  | ja | ja | ja  |
       | ja | nej  | nej  | ja | nej   |
       | nej  | ja | ja | nej  | nej   |
       | nej  | ja | nej  | nej  | nej   |
       | nej  | nej  | ja | nej  | nej   |
       | nej  | nej  | nej  | nej  | nej   |

      Beteende för **ej tilldelade** användare:

       | Egenskapsinställningar för program | | | Upplevelse för ej tilldelade användare | |
       |---|---|---|---|---|
       | Är det aktiverat för användare att logga in? | Användartilldelning krävs? | Synlig för användare? | Kan ej tilldelade användare logga in? | Kan ej tilldelade användare se programmet?* |
       | ja | ja | ja | nej  | nej   |
       | ja | ja | nej  | nej  | nej   |
       | ja | nej  | ja | ja | nej   |
       | ja | nej  | nej  | ja | nej   |
       | nej  | ja | ja | nej  | nej   |
       | nej  | ja | nej  | nej  | nej   |
       | nej  | nej  | ja | nej  | nej   |
       | nej  | nej  | nej  | nej  | nej   |

     *Kan användaren se programmet i åtkomstpanelen och Office 365-appfönstret?

1. Om du vill använda en anpassad logotyp skapar du en logotyp som är 215 x 215 pixlar och sparar den i PNG-format. Bläddra sedan till din logotyp och ladda upp den.

    ![Ändra logotypen](media/add-gallery-app/change-logo.png)

1. När du är klar väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

Nu när du har lagt till programmet i din Azure [AD-organisation väljer du en enda inloggningsmetod](what-is-single-sign-on.md#choosing-a-single-sign-on-method) som du vill använda och hänvisar till lämplig artikel nedan:

- [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurera enkel inloggning för lösenord](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurera länkad inloggning](configure-linked-sign-on.md)

