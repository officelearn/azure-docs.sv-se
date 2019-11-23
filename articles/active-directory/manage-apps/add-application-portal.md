---
title: Quickstart - Add an app to your Azure Active Directory tenant
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
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420476"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Snabbstart: Lägga till ett program i din Azure Active Directory-klientorganisation

Azure Active Directory (Azure AD) har ett galleri som innehåller tusentals förintegrerade program. Vissa av de program som din organisation använder finns förmodligen i galleriet. I den här snabbstarten använder du Azure Portal för att lägga till ett galleriprogram i din Azure Active Directory-klientorganisation (Azure AD).

När ett program har lagts till Azure AD-klienten kan du:

- Manage user access to the application with a Conditional Access policy.
- Konfigurera användare för enkel inloggning till programmet med deras Azure AD-konton.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill lägga till ett program till din klientorganisation behöver du:

- En Azure AD-prenumeration
- En prenumeration som är aktiverad för enkel inloggning för ditt program

Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din Azure AD-klientorganisation, som administratör för molnprogram eller som programadministratör.

För testning av stegen i den här självstudien rekommenderar vi att du använder en icke-produktionsmiljö. Om du inte har en icke-produktionsmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Lägga till ett program till din Azure AD-klientorganisation

Så här lägger du till ett galleriprogram i din Azure AD-klientorganisation:

1. In the [Azure portal](https://portal.azure.com), on the left navigation panel, select **Azure Active Directory**.

2. In the **Azure Active Directory** pane, select **Enterprise applications**. The **All applications** pane opens and displays a random sample of the applications in your Azure AD tenant.

3. To add a gallery app to your tenant, select **New application**. 

    ![Select New application to add a gallery app to your tenant](media/add-application-portal/new-application.png)

 4. Switch to the new gallery preview experience: In the banner at the top of the **Add an application page**, select the link that says **Click here to try out the new and improved app gallery**.

5. The **Browse Azure AD Gallery (Preview)** pane opens and displays tiles for cloud platforms, on-premises applications, and featured applications. Note that the applications listed in the **Featured applications** section have icons indicating whether they support federated single sign-on (SSO) and provisioning.

    ![Search for an app by name or category](media/add-application-portal/browse-gallery.png)

6. You can browse the gallery for the application you want to add, or search for the application by entering its name in the search box. Then select the application from the results. In the form, you can edit the name of the application to match the needs of your organization. In this example we've changed the name to **GitHub-test**.

    ![Shows how to add an application from the gallery](media/add-application-portal/create-application.png)

7. Välj **Skapa**. En komma igång-sida visas med alternativ för att konfigurera programmet för din organisation.

You've finished adding your application. Nästa avsnitt visar hur du ändrar logotypen och redigerar andra egenskaper för programmet.

## <a name="find-your-azure-ad-tenant-application"></a>Hitta ditt Azure AD-klientorganisationsprogram

Vi förutsätter att du behövde gå iväg en stund och nu har kommit tillbaka för att konfigurera programmet. The first thing to do is find your application.

1. In the **[Azure portal](https://portal.azure.com)** , on the left navigation panel, select **Azure Active Directory**.
1. In the **Azure Active Directory** pane, select **Enterprise applications**.
1. From the **Application Type** drop-down menu, select **All Applications**, and then select **Apply**. Mer information om visningsalternativ finns på sidan om att [visa klientorganisationsprogram](view-applications-portal.md).
1. Du kan nu se en lista över alla program i Azure AD-klientorganisationen. Listan är ett slumpmässigt urval. To see more applications, select **Show more** one or more times.
1. To quickly find an application in your tenant, enter the application name in the search box and select **Apply**. This example finds the GitHub-test application added previously.

    ![Shows how to find an application using the search box](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Konfigurera egenskaper för användarinloggning

Now that you've found the application, you can open it and configure application properties.

To edit the application properties:

1. Select the application to open it.
2. Select **Properties** to open the properties pane for editing.

    ![Shows the Properties screen and editable app properties](media/add-application-portal/edit-properties.png)

3. Läs igenom alternativen för inloggning. The options determine how users who are assigned or unassigned to the application can sign into the application. And, the options also determine if a user can see the application in the access panel.

    - **Enabled for users to sign-in?** determines whether users assigned to the application can sign in.
    - **User assignment required?** determines whether users who aren't assigned to the application can sign in.
    - **Visible to users?** determines whether users assigned to an app can see it in the access panel and O365 launcher.

4. Use the following tables to help you choose the best options for your needs.

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
       | Enabled for users to sign in? | Användartilldelning krävs? | Synlig för användare? | Kan ej tilldelade användare logga in? | Kan ej tilldelade användare se programmet?* |
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
1. Since you've already found your application, select the application.
1. In the left pane, select **Properties**.
1. Ladda upp logotypen.
1. When you're finished, select **Save**. 

    ![Shows how to change the logo from the app's Properties page](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > The thumbnail displayed on this **Properties** pane doesn't update right away. You can close and reopen the properties to see the updated icon.

## <a name="next-steps"></a>Nästa steg

Now that you've added the application to your Azure AD organization, [choose a single sign-on method](what-is-single-sign-on.md#choosing-a-single-sign-on-method) you want to use and refer to the appropriate article below:

- [Configure SAML-based single sign-on](configure-single-sign-on-non-gallery-applications.md)
- [Configure password single sign-on](configure-password-single-sign-on-non-gallery-applications.md)
- [Configure linked sign-on](configure-linked-sign-on.md)
