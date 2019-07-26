---
title: Lägg till ett program som inte är ett galleri program – Microsoft Identity Platform | Microsoft Docs
description: Lägg till ett program som inte är ett galleri till din Azure AD-klient.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: db8d8d6df16c5df7e29d8bb870c5d5eda6d8a2d3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477266"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Lägg till ett ej listat program (ej Galleri) i din Azure AD-organisation

Förutom valen i [Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)-programgalleriet har du möjlighet att lägga till ett **program som inte är ett galleri program**. Du kan lägga till alla program som redan finns i din organisation eller program från tredje part från en leverantör som inte redan ingår i Azure AD-galleriet. Beroende på ditt [licens avtal](https://azure.microsoft.com/pricing/details/active-directory/)är följande funktioner tillgängliga:

- Självbetjänings integrering av alla program som stöder [Security Assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) identitets leverantörer (SP-initierad eller IDP-initierad)
- Självbetjänings integrering av alla webb program som har en HTML-baserad inloggnings sida med [LÖSENORDSBASERAD SSO](what-is-single-sign-on.md#password-based-sso)
- Anslutning via självbetjäning för program som använder [systemet för scim-protokoll (Cross-Domain Identity Management) för användar etablering](use-scim-to-provision-users-and-groups.md)
- Möjlighet att lägga till länkar till ett program i [Office 365-appens start](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) -eller [Azure AD Access-panel](what-is-single-sign-on.md#linked-sign-on)

I den här artikeln beskrivs hur du lägger till ett program som inte är ett galleri program till **företags program** i Azure Portal utan att skriva kod. Om du i stället vill lära dig mer om hur du integrerar anpassade appar med Azure AD, se [autentiserings scenarier för Azure AD](../develop/authentication-scenarios.md). När du utvecklar en app som använder ett modernt protokoll som [OpenID Connect/OAuth](../develop/active-directory-v2-protocols.md) för att autentisera användare, kan du registrera den med Microsoft Identity Platform genom att använda [Appregistreringar](../develop/quickstart-register-app.md) upplevelse i Azure Portal.

## <a name="add-a-non-gallery-application"></a>Lägg till ett program som inte är ett galleri program

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com/) med ditt administratörs konto för Microsoft Identity Platform.
1. Välj **företags program** > **nytt program**.
2. (Valfritt men rekommenderas) I sökrutan **Lägg till** i galleriet, anger du visnings namnet för programmet. Om programmet visas i Sök resultaten väljer du det och hoppar över resten av den här proceduren.
3. Välj **program som inte är Galleri**. Sidan **Lägg till ett eget program** visas.

   ![Lägg till program](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Ange visnings namnet för det nya programmet.
6. Välj **Lägg till**. Sidan program **Översikt** öppnas.

## <a name="configure-user-sign-in-properties"></a>Konfigurera egenskaper för användarinloggning

1. Välj **Egenskaper** för att öppna rutan Egenskaper för redigering.

    ![Rutan Redigera egenskaper](media/add-application-portal/edit-properties.png)

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

    ![Ändra logotypen](media/add-application-portal/change-logo.png)

1. När du är klar väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

Nu när du har lagt till programmet i din Azure AD-organisation, [väljer du en enkel inloggnings metod](what-is-single-sign-on.md#choosing-a-single-sign-on-method) som du vill använda och refererar till lämplig artikel nedan:

- [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurera enkel inloggning för lösen ord](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurera länkad inloggning](configure-linked-sign-on.md)
