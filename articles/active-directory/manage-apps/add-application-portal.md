---
title: Lägga till en app i din Azure Active Directory-klientorganisation | Microsoft Docs
description: I den här snabbstarten använder du Azure Portal för att lägga till ett galleriprogram i din Azure Active Directory-klientorganisation (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 04/09/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466660a1e064ef41eb330b36107dbdcb1d097498
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477311"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Snabbstart: Lägga till ett program i din Azure Active Directory-klientorganisation

Azure Active Directory (Azure AD) har ett galleri som innehåller tusentals förintegrerade program. Vissa av de program som din organisation använder finns förmodligen i galleriet. I den här snabbstarten använder du Azure Portal för att lägga till ett galleriprogram i din Azure Active Directory-klientorganisation (Azure AD).

När ett program har lagts till Azure AD-klienten kan du:

- Hantera användar åtkomst till programmet med en princip för villkorlig åtkomst.
- Konfigurera användare för enkel inloggning till programmet med deras Azure AD-konton.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill lägga till ett program till din klientorganisation behöver du:

- En Azure AD-prenumeration
- En prenumeration som är aktiverad för enkel inloggning för ditt program

Logga in på [Azure-portalen](https://portal.azure.com) som global administratör för din Azure AD-klientorganisation, som administratör för molnprogram eller som programadministratör.

För testning av stegen i den här självstudien rekommenderar vi att du använder en icke-produktionsmiljö. Om du inte har en icke-produktionsmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Lägga till ett program till din Azure AD-klientorganisation

Så här lägger du till ett galleriprogram i din Azure AD-klientorganisation:

1. Välj **Azure Active Directory**på den vänstra navigerings panelen i [Azure Portal](https://portal.azure.com).
1. I fönstret **Azure Active Directory** väljer du **företags program**.
1. Fönstret **alla program** öppnas och visar ett slumpmässigt exempel på programmen i din Azure AD-klient. Välj **nytt program** överst i fönstret **alla program** för att lägga till en Galleri-app till din klient organisation.

    ![Välj nytt program för att lägga till en Galleri-app till din klient organisation](media/add-application-portal/new-application.png)

1. I fönstret **Kategorier** ser du ikoner under avsnittet **aktuella program** som är ett slumpmässigt exempel på Galleri program. Om du vill se fler program kan du välja **Visa mer** men vi rekommenderar inte att du söker på det här sättet eftersom det finns tusentals program i galleriet.

    ![Sök efter en app efter namn eller kategori](media/add-application-portal/categories.png)

1. Om du vill söka efter ett program går du till **Lägg till från galleriet**och anger namnet på det program som du vill lägga till. Välj programmet från resultaten och välj **Lägg till**. I följande exempel visas formuläret **Lägg till app**, som visas när du har sökt efter github.com.

    ![Visar hur du lägger till ett program från galleriet](media/add-application-portal/add-an-application.png)

1. I det programspecifika formuläret kan du ändra egenskapsinformation. Till exempel kan du redigera namnet på programmet så att det matchar behoven i din organisation. I det här exemplet används namnet **GitHub-test**.
1. När du är klar med att ändra egenskaperna väljer du **Lägg till**.
1. En komma igång-sida visas med alternativ för att konfigurera programmet för din organisation.

Du har lagt till ditt program. Passa på att ta en paus. Nästa avsnitt visar hur du ändrar logotypen och redigerar andra egenskaper för programmet.

## <a name="find-your-azure-ad-tenant-application"></a>Hitta ditt Azure AD-klientorganisationsprogram

Vi förutsätter att du behövde gå iväg en stund och nu har kommit tillbaka för att konfigurera programmet. Det första du ska göra är att hitta ditt program.

1. Välj **Azure Active Directory**på den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .
1. I fönstret **Azure Active Directory** väljer du **företags program**.
1. Välj **alla program**i list rutan **program typ** och välj sedan **Använd**. Mer information om visningsalternativ finns på sidan om att [visa klientorganisationsprogram](view-applications-portal.md).
1. Du kan nu se en lista över alla program i Azure AD-klientorganisationen. Listan är ett slumpmässigt urval. Om du vill se fler program väljer du **Visa fler** en eller flera gånger.
1. För att snabbt hitta ett program i din klient, ange program namnet i sökrutan och välj **Använd**. I det här exemplet hittas GitHub-testprogrammet som har lagts till tidigare.

    ![Visar hur du hittar ett program med hjälp av sökrutan](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Konfigurera egenskaper för användarinloggning

Nu när du har hittat programmet kan du öppna det och konfigurera program egenskaper.

Redigera program egenskaperna:

1. Välj programmet för att öppna det.
1. Välj **Egenskaper** för att öppna rutan Egenskaper för redigering.

    ![Visar egenskaper för skärm och redigerbara appar](media/add-application-portal/edit-properties.png)

1. Läs igenom alternativen för inloggning. Alternativen avgör hur användare som är tilldelade eller otilldelade till programmet kan logga in på programmet. Dessutom avgör alternativen om en användare kan se programmet i åtkomst panelen.

    - **Aktiverad för användare att logga in** bestämmer huruvida användare som är tilldelade till programmet kan logga in.
    - **Användar tilldelning krävs** avgör om användare som inte är tilldelade till programmet kan logga in.
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
1. Eftersom du redan har hittat ditt program väljer du programmet.
1. I det vänstra fönstret väljer du **Egenskaper**.
1. Ladda upp logotypen.
1. När du är klar väljer du **Spara**.

    ![Visar hur du ändrar logo typen från appens egenskaps sida](media/add-application-portal/change-logo.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lagt till programmet i din Azure AD-organisation, [väljer du en enkel inloggnings metod](what-is-single-sign-on.md#choosing-a-single-sign-on-method) som du vill använda och refererar till lämplig artikel nedan:

- [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurera enkel inloggning för lösen ord](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurera länkad inloggning](configure-linked-sign-on.md)
