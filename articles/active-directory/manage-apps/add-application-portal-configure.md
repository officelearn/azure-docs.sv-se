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
ms.openlocfilehash: eb4a150a1680dd4101249458894ded652cde15a0
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338052"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Snabb start: konfigurera egenskaper för ett program i din Azure Active Directory (Azure AD)-klient

I den tidigare snabb starten lade du till ett program till din Azure Active Directory-klient (Azure AD). När du lägger till ett program kan du låta din Azure AD-klient veta det är identitets leverantören för appen. Nu ska du konfigurera några av egenskaperna för appen.
 
## <a name="prerequisites"></a>Krav

Om du vill konfigurera egenskaperna för ett program i din Azure AD-klient behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En av följande roller: global administratör, moln program administratör, program administratör eller ägare av tjänstens huvud namn.
- Valfritt: Slutför [visningen av Visa dina appar](view-applications-portal.md).
- Valfritt: slut för ande av [Lägg till en app](add-application-portal.md).

>[!IMPORTANT]
>Använd en miljö som inte är en produktions miljö för att testa stegen i den här snabb starten.

## <a name="configure-app-properties"></a>Konfigurera app-egenskaper

När du har lagt till ett program i Azure AD-klienten visas sidan Översikt. Om du konfigurerar ett program som redan har lagts till tittar du på den första snabb starten. Den vägleder dig genom att visa de program som har lagts till i din klient organisation. 

Redigera program egenskaperna:

1. I Azure AD-portalen väljer du **företags program**. Hitta och välj sedan det program som du vill konfigurera.
2. I avsnittet **Hantera** väljer du **Egenskaper** för att öppna fönstret **Egenskaper** för redigering.

    ![Skärm bild av sidan egenskaper som visar egenskaper för redigerbara appar.](media/add-application-portal/edit-properties.png)

3. Ägna en stund åt att förstå alternativen som är tillgängliga för konfiguration:
    - **Aktiverat för användare att logga in?** Anger om användare som är tilldelade till programmet kan logga in.
    - **Krävs användar tilldelning?** Anger om användare som inte är tilldelade till programmet kan logga in.
    - **Synlig för användarna?** Anger om användare som är tilldelade till en app kan se den i [åtkomst panelen](https://myapps.microsoft.com) och Office 365 App Launcher. (Se rutmärket-menyn i det övre vänstra hörnet på en Office 365-webbplats eller på en Microsoft 365 webbplats.)
4. Använd följande tabeller för att hjälpa dig att välja de bästa alternativen för dina behov.

   - Beteende för *tilldelade* användare:

       | Program egenskap | Program egenskap | Program egenskap | Upplevelse för tilldelad användare | Upplevelse för tilldelad användare |
       |---|---|---|---|---|
       | Aktiverat för användare att logga in? | Användartilldelning krävs? | Synlig för användare? | Kan tilldelade användare logga in? | Kan tilldelade användare se programmet?* |
       | Ja | Ja | Ja | Ja | Ja  |
       | Ja | Ja | Nej  | Ja | Nej   |
       | Ja | Nej  | Ja | Ja | Ja  |
       | Ja | Nej  | Nej  | Ja | Nej   |
       | Nej  | Ja | Ja | Nej  | Nej   |
       | Nej  | Ja | Nej  | Nej  | Nej   |
       | Nej  | Nej  | Ja | Nej  | Nej   |
       | Nej  | Nej  | Nej  | Nej  | Nej   |

   - Beteende för *ej tilldelade* användare:

       | Program egenskap | Program egenskap | Program egenskap | Upplevelse för ej tilldelade användare | Upplevelse för ej tilldelade användare |
       |---|---|---|---|---|
       | Aktiverat för användare att logga in? | Användartilldelning krävs? | Synlig för användare? | Kan ej tilldelade användare logga in? | Kan ej tilldelade användare se programmet?* |
       | Ja | Ja | Ja | Nej  | Nej   |
       | Ja | Ja | Nej  | Nej  | Nej   |
       | Ja | Nej  | Ja | Ja | Nej   |
       | Ja | Nej  | Nej  | Ja | Nej   |
       | Nej  | Ja | Ja | Nej  | Nej   |
       | Nej  | Ja | Nej  | Nej  | Nej   |
       | Nej  | Nej  | Ja | Nej  | Nej   |
       | Nej  | Nej  | Nej  | Nej  | Nej   |

     *Kan användaren se programmet i åtkomstpanelen och Office 365-appfönstret?

## <a name="use-a-custom-logo"></a>Använda en anpassad logotyp

Så här använder du en anpassad logotyp:

1. Skapa en logo typ som är 215 x 215 pixlar och spara den i PNG-format.
2. I Azure AD-portalen väljer du **företags program**. Hitta och välj sedan det program som du vill konfigurera.
3. I avsnittet **Hantera** väljer du **Egenskaper** för att öppna fönstret **Egenskaper** för redigering. 
4. Välj ikonen för att ladda upp logo typen.
5. När du är klar väljer du **Spara**.

    ![Skärm bild av skärmen egenskaper som visar hur du ändrar logo typen.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > Miniatyr bilden som visas i det här **egenskaps** fönstret uppdateras inte omedelbart. Du kan stänga och öppna fönstret **Egenskaper** för att se den uppdaterade ikonen.

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat egenskaperna för ett program kan du fortsätta med att konfigurera enkel inloggning.

- [Konfigurera enkel inloggning](add-application-portal-setup-sso.md)
- [Ta bort en app](delete-application-portal.md)