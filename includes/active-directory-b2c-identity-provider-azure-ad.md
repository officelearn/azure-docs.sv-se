---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 818f33abfdb0655d96c0a8873a43903ee972b3bf
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82925393"
---
## <a name="register-an-azure-ad-app"></a>Registrera en Azure AD-app

Om du vill aktivera inloggning för användare från en specifik Azure AD-organisation måste du registrera ett program inom organisationens Azure AD-klient.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din organisations Azure AD-klient (till exempel contoso.com). Välj **filtret katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller din Azure AD-klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
1. Välj **ny registrering**.
1. Ange ett **namn** för ditt program. Till exempel `Azure AD B2C App`.
1. Godkänn standard valet av **konton endast i den här organisations katalogen** för det här programmet.
1. För **omdirigerings-URI: n**, godkänn värdet för **webb**och ange följande URL i gemener, där `your-B2C-tenant-name` ersätts med namnet på din Azure AD B2C-klient.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Till exempel `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Välj **Registrera**. Registrera **program-ID: t (Client)** för användning i ett senare steg.
1. Välj **certifikat & hemligheter**och välj sedan **ny klient hemlighet**.
1. Ange en **Beskrivning** av hemligheten, Välj förfallo datum och välj sedan **Lägg till**. Registrera **värdet** för hemligheten som ska användas i ett senare steg.

### <a name="configuring-optional-claims"></a>Konfigurera valfria anspråk

Om du vill hämta `family_name` och `given_name` anspråk från Azure AD kan du konfigurera valfria anspråk för programmet i Azure Portal användar gränssnitt eller applikations manifest. Mer information finns i [så här ger du valfria anspråk till din Azure AD-App](/azure/active-directory/develop/active-directory-optional-claims).

1. Logga in på [Azure-portalen](https://portal.azure.com). Sök efter och välj **Azure Active Directory**.
1. I avsnittet **Hantera** väljer du **Appregistreringar**.
1. Välj det program som du vill konfigurera valfria anspråk för i listan.
1. I avsnittet **Hantera** väljer du **token-konfiguration**.
1. Välj **Lägg till valfritt anspråk**.
1. I **tokentyp**väljer du **ID**.
1. Välj de valfria anspråk som ska läggas `family_name` till `given_name`och.
1. Klicka på **Lägg till**.