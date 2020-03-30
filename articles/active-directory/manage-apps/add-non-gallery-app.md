---
title: Lägga till ett program som inte är galleri - Microsoft identity platform | Microsoft-dokument
description: Lägg till ett program som inte är galleri i din Azure AD-klientorganisation.
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
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Lägga till ett olistat (icke-galleri) program till din Azure AD-organisation

Förutom alternativen i [Azure AD-programgalleriet](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)har du möjlighet att lägga till ett **program som inte är ett galleri**. Du kan lägga till alla program som redan finns i din organisation, eller ett program från tredje part från en leverantör som inte redan ingår i Azure AD-galleriet. Beroende på [licensavtalet](https://azure.microsoft.com/pricing/details/active-directory/)är följande funktioner tillgängliga:

- Integrering av självbetjäning av alla program som stöder [säkerhetspåläggsmarkeringsspråk (SAML) 2.0-identitetsleverantörer](https://wikipedia.org/wiki/SAML_2.0) (SP-initierade eller IdP-initierade)
- Självbetjäningsintegrering av alla webbprogram som har en HTML-baserad inloggningssida med [lösenordsbaserad SSO](what-is-single-sign-on.md#password-based-sso)
- Självbetjäningsanslutning av program som använder [SCIM-protokollet (System for Cross-Domain Identity Management) för användaretablering](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Möjlighet att lägga till länkar till alla program i [startprogrammet för Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD-åtkomstpanelen](what-is-single-sign-on.md#linked-sign-on)

I den här artikeln beskrivs hur du lägger till ett program som inte är galleri **i Enterprise Applications** i Azure-portalen utan att skriva kod. Om du i stället letar efter utvecklarvägledning om hur du integrerar anpassade appar med Azure AD läser du [Autentiseringsscenarier för Azure AD](../develop/authentication-scenarios.md). När du utvecklar en app som använder ett modernt protokoll som [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) för att autentisera användare kan du registrera den med Microsofts identitetsplattform med hjälp av [appregistreringsupplevelsen](../develop/quickstart-register-app.md) i Azure-portalen.

## <a name="add-a-non-gallery-application"></a>Lägga till ett program som inte är galleri

1. Logga in på [Azure Active Directory-portalen](https://aad.portal.azure.com/) med ditt Microsoft identity platform-administratörskonto.

2. Välj **Företagsprogram** > **Nytt program**.

3. (Valfritt men rekommenderat) I sökrutan **Bläddra i Azure AD Gallery** anger du programmets visningsnamn. 

4. Välj **Skapa ett eget program**. Sidan **Skapa ett eget program** visas.

   ![Lägga till ett program](media/add-non-gallery-app/create-your-own-application.png)

5. Börja skriva visningsnamnet för det nya programmet. Om det finns några galleriprogram med liknande namn visas de i en sökresultatlista.

   > [!NOTE]
   > Vi rekommenderar att du använder galleriversionen av ditt program när det är möjligt. Om programmet som du vill lägga till visas i sökresultaten markerar du programmet och hoppar över resten av den här proceduren.

6. Under Vad vill du göra med **Integrate any other application you don't find in the gallery** **din ansökan?** Det här alternativet används vanligtvis för SAML- och WS-Fed-program.

   > [!NOTE]
   > De andra två alternativen används i följande scenarier:
   >* **Konfigurera programproxy för säker fjärråtkomst till ett lokalt program** öppnar konfigurationssidan för Azure AD Application Proxy och kopplingar.
   >* **Registrera ett program som du arbetar med för att integrera med Azure AD** öppnar sidan **Appregistreringar.** Det här alternativet används vanligtvis för OpenID Connect-program.

7. Välj **Skapa**. **Sidan** Översikt för programmet öppnas.

## <a name="configure-user-sign-in-properties"></a>Konfigurera egenskaper för användarinloggning

1. Välj **Egenskaper** om du vill öppna egenskapsfönstret för redigering.

    ![Fönstret Redigera egenskaper](media/add-non-gallery-app/edit-properties.png)

2. Ange följande alternativ för att avgöra hur användare som tilldelas eller inte tilldelats programmet kan logga in på programmet och om en användare kan se programmet på åtkomstpanelen.

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

3. Om du vill använda en anpassad logotyp skapar du en logotyp som är 215 x 215 pixlar och sparar den i PNG-format. Bläddra sedan till din logotyp och ladda upp den.

    ![Ändra logotypen](media/add-non-gallery-app/change-logo.png)

4. När du är klar väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

Nu när du har lagt till programmet i din Azure [AD-organisation väljer du en enda inloggningsmetod](what-is-single-sign-on.md#choosing-a-single-sign-on-method) som du vill använda och hänvisar till lämplig artikel nedan:

- [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurera enkel inloggning för lösenord](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurera länkad inloggning](configure-linked-sign-on.md)
