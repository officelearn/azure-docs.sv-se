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
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5a5f100dbe09c3b82f58183a118ee3bf455f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063619"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Lägg till ett ej listat program (ej Galleri) i din Azure AD-organisation

Förutom valen i [Azure AD-programgalleriet](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)har du möjlighet att lägga till ett **program som inte är ett galleri program**. Du kan lägga till alla program som redan finns i din organisation eller program från tredje part från en leverantör som inte redan ingår i Azure AD-galleriet. Beroende på ditt [licens avtal](https://azure.microsoft.com/pricing/details/active-directory/)är följande funktioner tillgängliga:

- Självbetjänings integrering av alla program som stöder [Security Assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) identitets leverantörer (SP-initierad eller IDP-initierad)
- Självbetjänings integrering av alla webb program som har en HTML-baserad inloggnings sida med [LÖSENORDSBASERAD SSO](what-is-single-sign-on.md#password-based-sso)
- Anslutning via självbetjäning för program som använder [systemet för scim-protokoll (Cross-Domain Identity Management) för användar etablering](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Möjlighet att lägga till länkar till ett program i [Office 365-appens start](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) -eller [Azure AD Access-panel](what-is-single-sign-on.md#linked-sign-on)

I den här artikeln beskrivs hur du lägger till ett program som inte är ett galleri program till **företags program** i Azure Portal utan att skriva kod. Om du i stället vill lära dig mer om hur du integrerar anpassade appar med Azure AD, se [autentiserings scenarier för Azure AD](../develop/authentication-scenarios.md). När du utvecklar en app som använder ett modernt protokoll som [OpenID Connect/OAuth](../develop/active-directory-v2-protocols.md) för att autentisera användare, kan du registrera den med Microsoft Identity Platform genom att använda [Appregistreringar](../develop/quickstart-register-app.md) upplevelse i Azure Portal.

## <a name="add-a-non-gallery-application"></a>Lägg till ett program som inte är ett galleri program

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com/) med ditt administratörs konto för Microsoft Identity Platform.

2. Välj **företags program** > **nytt program**.

3. (Valfritt men rekommenderas) I sökrutan Sök **efter Azure AD-Galleri** anger du visnings namnet för programmet. 

4. Välj **skapa ett eget program**. Sidan **skapa ett eget program** visas.

   ![Lägga till ett program](media/add-non-gallery-app/create-your-own-application.png)

5. Börja skriva visnings namnet för det nya programmet. Om det finns några Galleri program med liknande namn visas de i en lista med Sök resultat.

   > [!NOTE]
   > Vi rekommenderar att du använder Galleri versionen av programmet när det är möjligt. Om det program som du vill lägga till visas i Sök resultaten väljer du programmet och hoppar över resten av den här proceduren.

6. Under **vad vill du göra med ditt program?** Välj **integrera andra program som du inte hittar i galleriet**. Det här alternativet används vanligt vis för SAML-och WS-utfodras program.

   > [!NOTE]
   > De andra två alternativen används i följande scenarier:
   >* **Konfigurera programproxy för säker fjärråtkomst till ett lokalt program** öppnar konfigurations sidan för Azure AD-programproxy och anslutningar.
   >* **Registrera ett program som du arbetar med för att integrera med Azure AD** öppnar sidan **Appregistreringar** . Det här alternativet används vanligt vis för OpenID Connect-program.

7. Välj **Skapa**. Sidan program **Översikt** öppnas.

## <a name="configure-user-sign-in-properties"></a>Konfigurera egenskaper för användarinloggning

1. Välj **Egenskaper** för att öppna rutan Egenskaper för redigering.

    ![Rutan Redigera egenskaper](media/add-non-gallery-app/edit-properties.png)

2. Ange följande alternativ för att avgöra hur användare som är tilldelade eller otilldelade till programmet kan logga in på programmet och om en användare kan se programmet i åtkomst panelen.

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

3. Om du vill använda en anpassad logo typ skapar du en logo typ som är 215 x 215 bild punkter och sparar den i PNG-format. Bläddra sedan till din logo typ och ladda upp den.

    ![Ändra logotypen](media/add-non-gallery-app/change-logo.png)

4. När du är klar väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

Nu när du har lagt till programmet i din Azure AD-organisation, [väljer du en enkel inloggnings metod](what-is-single-sign-on.md#choosing-a-single-sign-on-method) som du vill använda och refererar till lämplig artikel nedan:

- [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurera enkel inloggning för lösen ord](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurera länkad inloggning](configure-linked-sign-on.md)
