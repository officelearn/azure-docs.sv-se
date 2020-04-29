---
title: Lägg till en Galleri app – Azure Active Directory | Microsoft Docs
description: Lär dig hur du lägger till en app från Azure AD-galleriet till dina Azure Enterprise-program.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "73062591"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Lägg till en Galleri-app i Azure AD-organisationen

Azure Active Directory (Azure AD) har ett galleri som innehåller tusentals förintegrerade program som är aktiverade med enkel inloggning i företag. I den här artikeln beskrivs de allmänna stegen för att lägga till en app från galleriet till din Azure AD-organisation.

> [!IMPORTANT]
> Börja med att kontrol lera din app i [listan med självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Du kommer troligen att hitta steg för steg-anvisningar för att lägga till och konfigurera den Galleri app som du vill lägga till.

## <a name="add-a-gallery-application"></a>Lägg till ett galleri program

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din Azure AD-klientorganisation, som administratör för molnprogram eller som programadministratör.

1. Välj **Azure Active Directory**på den vänstra navigerings panelen i [Azure Portal](https://portal.azure.com).

1. I fönstret **Azure Active Directory** väljer du **företags program**.

    ![Öppna företagsprogram](media/add-gallery-app/open-enterprise-apps.png)


3. Om du vill lägga till en Galleri-app till klienten väljer du **nytt program**.

    ![Välj nytt program för att lägga till en Galleri-app till din klient organisation](media/add-gallery-app/new-application.png)

 4. Växla till den nya Galleri förhands granskningen: i banderollen högst upp på **sidan Lägg till ett program**väljer du den länk som visas **Klicka här för att prova det nya och förbättrade app-galleriet**.

5. Fönstret **Bläddra i Azure AD-Galleri** öppnas och visar paneler för moln plattform, lokala program och aktuella program. Observera att programmen som listas i avsnittet **aktuella program** innehåller ikoner som visar om de stöder federerad enkel inloggning (SSO) och etablering.

    ![Sök efter en app efter namn eller kategori](media/add-gallery-app/browse-gallery.png)

6. Bläddra i galleriet efter det program som du vill lägga till eller Sök efter programmet genom att ange dess namn i sökrutan. Välj sedan programmet från resultaten. Valfritt I formuläret kan du redigera program namnet så att det matchar organisationens behov.

    ![Visar hur du lägger till ett program från galleriet](media/add-gallery-app/create-application.png)

7. Välj **Skapa**. En komma igång-sida visas med alternativ för att konfigurera programmet för din organisation.

## <a name="configure-user-sign-in-properties"></a>Konfigurera egenskaper för användarinloggning

1. Välj **Egenskaper** för att öppna rutan Egenskaper för redigering.

    ![Rutan Redigera egenskaper](media/add-gallery-app/edit-properties.png)

1. Ange följande alternativ för att avgöra hur användare som är tilldelade eller otilldelade till programmet kan logga in på programmet och om en användare kan se programmet i åtkomst panelen.

    - **Aktiverad för användare att logga in** bestämmer huruvida användare som är tilldelade till programmet kan logga in.
    - **Användar tilldelning krävs** avgör om användare som inte är tilldelade till programmet kan logga in.
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
       | Aktiverat för användare att logga in? | Användartilldelning krävs? | Synlig för användare? | Kan ej tilldelade användare logga in? | Kan ej tilldelade användare se programmet?* |
       | ja | ja | ja | nej  | nej   |
       | ja | ja | nej  | nej  | nej   |
       | ja | nej  | ja | ja | nej   |
       | ja | nej  | nej  | ja | nej   |
       | nej  | ja | ja | nej  | nej   |
       | nej  | ja | nej  | nej  | nej   |
       | nej  | nej  | ja | nej  | nej   |
       | nej  | nej  | nej  | nej  | nej   |

     *Kan användaren se programmet i åtkomstpanelen och Office 365-appfönstret?

1. Om du vill använda en anpassad logo typ skapar du en logo typ som är 215 x 215 bild punkter och sparar den i PNG-format. Bläddra sedan till din logo typ och ladda upp den.

    ![Ändra logotypen](media/add-gallery-app/change-logo.png)

1. När du är klar väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

Nu när du har lagt till programmet i din Azure AD-organisation, [väljer du en enkel inloggnings metod](what-is-single-sign-on.md#choosing-a-single-sign-on-method) som du vill använda och refererar till lämplig artikel nedan:

- [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurera enkel inloggning för lösen ord](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurera länkad inloggning](configure-linked-sign-on.md)

