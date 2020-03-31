---
title: Snabbstart – lägga till en app i din Azure Active Directory-klient
description: I den här snabbstarten använder du Azure Portal för att lägga till ett galleriprogram i din Azure Active Directory-klientorganisation (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 650a4f643f170fc64dd0e0643d645a7dcbffdfbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240494"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Snabbstart: Lägga till ett program i din Azure Active Directory-klientorganisation

Azure Active Directory (Azure AD) har ett galleri som innehåller tusentals förintegrerade program. Vissa av de program som din organisation använder finns förmodligen i galleriet. I den här snabbstarten använder du Azure Portal för att lägga till ett galleriprogram i din Azure Active Directory-klientorganisation (Azure AD).

När ett program har lagts till Azure AD-klienten kan du:

- Hantera användaråtkomst till programmet med en princip för villkorlig åtkomst.
- Konfigurera användare för enkel inloggning till programmet med deras Azure AD-konton.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill lägga till ett program till din klientorganisation behöver du:

- En Azure AD-prenumeration
- En prenumeration som är aktiverad för enkel inloggning för ditt program

Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din Azure AD-klientorganisation, som administratör för molnprogram eller som programadministratör.

För testning av stegen i den här självstudien rekommenderar vi att du använder en icke-produktionsmiljö. Om du inte har en icke-produktionsmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Lägga till ett program till din Azure AD-klientorganisation

Så här lägger du till ett galleriprogram i din Azure AD-klientorganisation:

1. Välj Azure Active Directory på den vänstra navigeringspanelen i [Azure-portalen](https://portal.azure.com). **Azure Active Directory**

2. Välj **Enterprise-program**i fönstret **Azure Active Directory** . Fönstret **Alla program** öppnas och visar ett slumpmässigt urval av programmen i din Azure AD-klientorganisation.

3. Om du vill lägga till en galleriapp i din klientorganisation väljer du **Nytt program**. 

    ![Välj Nytt program om du vill lägga till en galleriapp i din klientorganisation](media/add-application-portal/new-application.png)

 4. Växla till den nya förhandsgranskningsupplevelsen i galleriet: I banderollen högst upp på **sidan Lägg till ett program**väljer du länken som säger Klicka här för att prova det nya och förbättrade **appgalleriet**.

5. **Fönstret Bläddra i Azure AD Gallery (Förhandsversion)** öppnas och visar paneler för molnplattformar, lokala program och utvalda program. Observera att de program som anges i avsnittet **Utvalda program** har ikoner som anger om de stöder federerade enkel inloggning (SSO) och etablering.

    ![Söka efter en app efter namn eller kategori](media/add-application-portal/browse-gallery.png)

6. Du kan bläddra i galleriet efter det program du vill lägga till eller söka efter programmet genom att ange dess namn i sökrutan. Välj sedan programmet från resultaten. I formuläret kan du redigera namnet på programmet så att det matchar organisationens behov. I det här exemplet har vi ändrat namnet till **GitHub-test**.

    ![Visar hur du lägger till ett program från galleriet](media/add-application-portal/create-application.png)

7. Välj **Skapa**. En komma igång-sida visas med alternativ för att konfigurera programmet för din organisation.

Du har lagt till programmet. Nästa avsnitt visar hur du ändrar logotypen och redigerar andra egenskaper för programmet.

## <a name="find-your-azure-ad-tenant-application"></a>Hitta ditt Azure AD-klientorganisationsprogram

Vi förutsätter att du behövde gå iväg en stund och nu har kommit tillbaka för att konfigurera programmet. Det första du bör göra är att hitta din ansökan.

1. Välj Azure Active Directory på den vänstra navigeringspanelen i **[Azure-portalen](https://portal.azure.com)**. **Azure Active Directory**
1. Välj **Enterprise-program**i fönstret **Azure Active Directory** .
1. Välj **Alla program**på den nedrullningsbara menyn **Programtyp** och välj sedan **Använd**. Mer information om visningsalternativ finns på sidan om att [visa klientorganisationsprogram](view-applications-portal.md).
1. Du kan nu se en lista över alla program i Azure AD-klientorganisationen. Listan är ett slumpmässigt urval. Om du vill se fler program väljer du **Visa fler** gånger.
1. Om du snabbt vill hitta ett program i din klient anger du programnamnet i sökrutan och väljer **Använd**. I det här exemplet hittas GitHub-testprogrammet som lagts till tidigare.

    ![Visar hur du hittar ett program med hjälp av sökrutan](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Konfigurera egenskaper för användarinloggning

Nu när du har hittat programmet kan du öppna det och konfigurera programegenskaper.

Så här redigerar du programegenskaperna:

1. Välj det program som ska öppnas för att öppna det.
2. Välj **Egenskaper** om du vill öppna egenskapsfönstret för redigering.

    ![Visar egenskapsskärmen Egenskaper och de redigerbara appegenskaperna](media/add-application-portal/edit-properties.png)

3. Läs igenom alternativen för inloggning. Alternativen avgör hur användare som tilldelas eller inte tilldelas programmet kan logga in på programmet. Och alternativen avgör också om en användare kan se programmet i åtkomstpanelen.

    - **Är det aktiverat för användare att logga in?** avgör om användare som tilldelats programmet kan logga in.
    - **Användartilldelning krävs?** avgör om användare som inte är tilldelade till programmet kan logga in.
    - **Är du synlig för användarna?** avgör om användare som tilldelats en app kan se den i åtkomstpanelen och O365-startprogrammet.

4. Använd följande tabeller för att välja de bästa alternativen för dina behov.

   - Beteende för **tilldelade** användare:

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

   - Beteende för **ej tilldelade** användare:

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

## <a name="use-a-custom-logo"></a>Använda en anpassad logotyp

Så här använder du en anpassad logotyp:

1. Skapa en logotyp som är 215 x 215 bildpunkter och spara den i PNG-format.
1. Eftersom du redan har hittat programmet väljer du programmet.
1. Välj **Egenskaper**i den vänstra rutan .
1. Ladda upp logotypen.
1. När du är klar väljer du **Spara**. 

    ![Visar hur du ändrar logotypen från appens egenskaper](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > Miniatyrbilden som visas i den här **egenskapsfönstret** uppdateras inte direkt. Du kan stänga och öppna egenskaperna igen för att se den uppdaterade ikonen.

## <a name="next-steps"></a>Nästa steg

Nu när du har lagt till programmet i din Azure [AD-organisation väljer du en enda inloggningsmetod](what-is-single-sign-on.md#choosing-a-single-sign-on-method) som du vill använda och hänvisar till lämplig artikel nedan:

- [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurera enkel inloggning för lösenord](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurera länkad inloggning](configure-linked-sign-on.md)
