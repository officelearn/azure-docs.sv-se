---
title: Lägga till en app i din Azure Active Directory-klientorganisation | Microsoft Docs
description: I den här snabbstarten använder du Azure Portal för att lägga till ett galleriprogram i din Azure Active Directory-klientorganisation (Azure AD).
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 04/09/2019
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: b15842d7157e8e5a691f37f846dd424bf308eae3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60294174"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Snabbstart: Lägga till ett program i din Azure Active Directory-klientorganisation

Azure Active Directory (Azure AD) har ett galleri som innehåller tusentals förintegrerade program. Vissa av de program som din organisation använder finns förmodligen i galleriet. I den här snabbstarten använder du Azure Portal för att lägga till ett galleriprogram i din Azure Active Directory-klientorganisation (Azure AD).

När ett program har lagts till Azure AD-klienten kan du:

- Hantera användaråtkomst till programmet med en princip för villkorsstyrd åtkomst.
- Konfigurera användare för enkel inloggning till programmet med deras Azure AD-konton.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill lägga till ett program till din klientorganisation behöver du:

- En Azure AD-prenumeration
- En prenumeration som är aktiverad för enkel inloggning för ditt program

Logga in på [Azure-portalen](https://portal.azure.com) som global administratör för din Azure AD-klientorganisation, som administratör för molnprogram eller som programadministratör.

För testning av stegen i den här självstudien rekommenderar vi att du använder en icke-produktionsmiljö. Om du inte har en icke-produktionsmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Lägga till ett program till din Azure AD-klientorganisation

Så här lägger du till ett galleriprogram i din Azure AD-klientorganisation:

1. I den [Azure-portalen](https://portal.azure.com), på den vänstra navigeringspanelen väljer **Azure Active Directory**.

1. I den **Azure Active Directory** väljer **företagsprogram**.

    ![Öppna företagsprogram](media/add-application-portal/open-enterprise-apps.png)

1. Den **alla program** öppnas fönstret för att visa ett slumpmässigt urval av program i Azure AD-klienten. Välj **nytt program** överst i den **alla program** fönstret.

    ![Nytt program](media/add-application-portal/new-application.png)

1. I den **kategorier** fönstret visas ikonerna under de **aktuellt program** område som är ett slumpmässigt urval av galleriprogram.  Om du vill se fler program kan du välja **visa fler**. Men vi rekommenderar inte att söka på så sätt eftersom det finns tusentals program i galleriet.

    ![Sök efter namn eller kategori](media/add-application-portal/categories.png)

1. Att söka efter ett program under **Lägg till från galleriet**, anger du namnet på programmet som du vill lägga till. Markera programmet i resultaten och välj **Lägg till**. I följande exempel visas formuläret **Lägg till app**, som visas när du har sökt efter github.com.

    ![Lägga till ett program](media/add-application-portal/add-an-application.png)

1. I det programspecifika formuläret kan du ändra egenskapsinformation. Till exempel kan du redigera namnet på programmet så att det matchar behoven i din organisation. I det här exemplet används namnet **GitHub-test**.

1. När du är klar gör ändringar i egenskaperna, väljer **Lägg till**.

1. En komma igång-sida visas med alternativ för att konfigurera programmet för din organisation.

Du har lagt till ditt program. Passa på att ta en paus. Nästa avsnitt visar hur du ändrar logotypen och redigerar andra egenskaper för programmet.

## <a name="find-your-azure-ad-tenant-application"></a>Hitta ditt Azure AD-klientorganisationsprogram

Vi förutsätter att du behövde gå iväg en stund och nu har kommit tillbaka för att konfigurera programmet. Det första du ska göra är att hitta dina program.

1. I den  **[Azure-portalen](https://portal.azure.com)**, på den vänstra navigeringspanelen väljer **Azure Active Directory**.

1. I den **Azure Active Directory** väljer **företagsprogram**.

1. Från den **programtyp** nedrullningsbara menyn och välj **alla program**, och välj sedan **tillämpa**. Mer information om visningsalternativ finns på sidan om att [visa klientorganisationsprogram](view-applications-portal.md).

1. Du kan nu se en lista över alla program i Azure AD-klientorganisationen. Listan är ett slumpmässigt urval. Om du vill se fler program **visa fler** en eller flera gånger.

1. För att snabbt hitta ett program i din klient, ange namnet på programmet i sökrutan och välj **tillämpa**. Det här exemplet hittar GitHub-test-programmet har lagts till tidigare.

    ![Söka efter ett program](media/add-application-portal/find-application.png)


## <a name="configure-user-sign-in-properties"></a>Konfigurera egenskaper för användarinloggning

Nu när du har hittat programmet, kan du öppna den och konfigurera egenskaper för program.

Redigera egenskaper för program:

1. Välj program att öppna den.
1. Välj **egenskaper** att öppna egenskapsfönstret för redigering.

    ![Redigera egenskapsrutan](media/add-application-portal/edit-properties.png)

1. Läs igenom alternativen för inloggning. De fastställer hur användare som tilldelats eller otilldelade till programmet kan logga in i programmet. Och alternativen också bestämma om en användare kan se programmet i åtkomstpanelen.

    - **Aktiverad för användare att logga in** bestämmer huruvida användare som är tilldelade till programmet kan logga in.
    - **Användartilldelning krävs** styr huruvida användare som inte har tilldelats till programmet kan logga in.
    - **Synlig för användaren** bestämmer huruvida användare som är tilldelade till en app kan se den i åtkomstpanelen och O365-appfönstret.

1. Använd följande tabeller för att hjälpa dig att välja de bästa alternativen för dina behov.

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
       | Aktiverad för användare att logga in? | Användartilldelning krävs? | Synlig för användare? | Kan ej tilldelade användare logga in? | Kan ej tilldelade användare se programmet?* |
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
1. Eftersom du redan har hittat ditt program, Välj ett program.
1. I den vänstra rutan väljer **egenskaper**.
1. Ladda upp logotypen.
1. När du är klar väljer du **spara**.

    ![Ändra logotypen](media/add-application-portal/change-logo.png)


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du lägger till ett galleriprogram i din Azure AD-klientorganisation. Du har lärt dig hur du redigerar egenskaperna för ett program.

Nu är du redo att konfigurera programmet för enkel inloggning.

> [!div class="nextstepaction"]
> [Konfigurera enkel inloggning](configure-single-sign-on-portal.md)


