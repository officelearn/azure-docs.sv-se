---
title: 'Snabb start: konfigurera egenskaper för ett program i din Azure Active Directory (Azure AD)-klient'
description: Den här snabb starten använder Azure Portal för att konfigurera ett program som har registrerats med din Azure Active Directory (Azure AD)-klient.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82a3310f6fc2169a515b4b13d81c88d187bd0f9c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956160"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Snabb start: konfigurera egenskaper för ett program i din Azure Active Directory (Azure AD)-klient

I den tidigare snabb starten lade du till ett program i Azure AD-klienten. När du lägger till ett program kan du låta din Azure AD-klient veta det är identitets leverantören för appen. Nu ska du konfigurera några av egenskaperna för appen.
 
## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera egenskaperna för ett program i din Azure AD-klient behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En av följande roller: global administratör, moln program administratör, program administratör eller ägare av tjänstens huvud namn.
- (Valfritt: slut för ande av [Visa dina appar](view-applications-portal.md)).
- (Valfritt: slut för ande av [Lägg till en app](add-application-portal.md)).

>[!IMPORTANT]
>Vi rekommenderar att du använder en icke-produktions miljö för att testa stegen i den här snabb starten.

## <a name="configure-app-properties"></a>Konfigurera app-egenskaper

När du har lagt till ett program i Azure AD-klienten visas den direkt på sidan Översikt för det. Om du konfigurerar ett program som redan har lagts till kan du titta på den första snabb starten, så vägleder dig genom att visa de program som har lagts till i din klient. 

Redigera program egenskaperna:

1. I Azure AD-portalen väljer du **företags program** och sedan söker du efter och väljer det program som du vill konfigurera.
2. I avsnittet hantera väljer du **Egenskaper** för att öppna fönstret Egenskaper för redigering.
    ![Visar egenskaper för skärm och redigerbara appar](media/add-application-portal/edit-properties.png)
3. Ta en stund att förstå de alternativ som är tillgängliga för konfigurering.
    - **Aktiverat för användare att logga in?** Anger om användare som är tilldelade till programmet kan logga in.
    - **Krävs användar tilldelning?** Anger om användare som inte är tilldelade till programmet kan logga in.
    - **Synlig för användarna?** bestämmer om användare som är tilldelade till en app kan se den i åtkomst panelen ( https://myapps.microsoft.com) och O365 app-start (rutmärket-menyn längst upp till vänster i en Office 365-webbplats eller Microsoft 365 webbplats).
4. Använd följande tabeller för att hjälpa dig att välja de bästa alternativen för dina behov.

   - Beteende för **tilldelade** användare:

       | Program egenskap | Program egenskap | Program egenskap | Upplevelse för tilldelad användare | Upplevelse för tilldelad användare |
       |---|---|---|---|---|
       | Aktiverat för användare att logga in? | Användartilldelning krävs? | Synlig för användare? | Kan tilldelade användare logga in? | Kan tilldelade användare se programmet?* |
       | ja | ja | ja | ja | ja  |
       | ja | ja | nej  | ja | nej   |
       | ja | nej  | ja | ja | ja  |
       | ja | nej  | nej  | ja | nej   |
       | nej  | ja | ja | nej  | nej   |
       | nej  | ja | nej  | nej  | nej   |
       | nej  | nej  | ja | nej  | nej   |
       | nej  | nej  | nej  | nej  | nej   |

   - Beteende för **ej tilldelade** användare:

       | Program egenskap | Program egenskap | Program egenskap | Upplevelse för ej tilldelade användare | Upplevelse för ej tilldelade användare |
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

## <a name="use-a-custom-logo"></a>Använda en anpassad logotyp

Så här använder du en anpassad logotyp:

1. Skapa en logotyp som är 215 x 215 bildpunkter och spara den i PNG-format.
2. I Azure AD-portalen väljer du **företags program** och sedan söker du efter och väljer det program som du vill konfigurera.
3. I avsnittet hantera väljer du **Egenskaper** för att öppna fönstret Egenskaper för redigering. 
4. Välj ikonen för att ladda upp logo typen.
5. När du är klar väljer du **Spara**. 
    ![Visar hur du ändrar logo typen från appens egenskaps sida](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > Miniatyr bilden som visas i det här **egenskaps** fönstret uppdateras inte omedelbart. Du kan stänga och öppna egenskaperna igen för att se den uppdaterade ikonen.

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat egenskaperna för ett program kan du fortsätta med att konfigurera enkel inloggning.

- [Konfigurera enkel inloggning](add-application-portal-setup-sso.md)
- [Ta bort en app](delete-application-portal.md)