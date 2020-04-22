---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 31a6d116ae1afce9afdd3786747490980963d823
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678049"
---
## <a name="register-an-azure-ad-app"></a>Registrera en Azure AD-app

Om du vill aktivera inloggning för användare från en viss Azure AD-organisation måste du registrera ett program inom den organisatoriska Azure AD-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrollera att du använder katalogen som innehåller din organisation Azure AD-klientorganisation (till exempel contoso.com). Välj **katalog + prenumerationsfilter** i den övre menyn och välj sedan den katalog som innehåller din Azure AD-klientorganisation.
1. Välj **Alla tjänster** i det övre vänstra hörnet på Azure-portalen och sök sedan efter och välj **Appregistreringar**.
1. Välj **Ny registrering**.
1. Ange ett **namn** för ditt program. Till exempel `Azure AD B2C App`.
1. Acceptera standardvalet av **konton i den här organisationskatalogen endast** för det här programmet.
1. För **Omdirigera URI**godkänner du värdet **för webben**och anger följande `your-B2C-tenant-name` URL i alla gemener, där ersätts med namnet på din Azure AD B2C-klient.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Till exempel `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Välj **Registrera**. Registrera **program-ID:t (klient)** för användning i ett senare steg.
1. Välj **Certifikat & hemligheter**och välj sedan Ny **klienthemlighet**.
1. Ange en **beskrivning** för hemligheten, välj en förfallodatum och välj sedan **Lägg till**. Registrera **värdet** för hemligheten för användning i ett senare steg.

### <a name="configuring-optional-claims"></a>Konfigurera valfria anspråk

Om du vill `family_name` hämta `given_name` och anspråk från Azure AD kan du konfigurera valfria anspråk för ditt program i Azure-portalens användargränssnitt eller programmanifest. Mer information finns i [Så här anger du valfria anspråk till din Azure AD-app](/active-directory/develop/active-directory-optional-claims.md).

1. Logga in på [Azure-portalen](https://portal.azure.com). Sök efter och välj **Azure Active Directory**.
1. Välj **Appregistreringar**i avsnittet **Hantera** .
1. Välj det program som du vill konfigurera valfria anspråk för i listan.
1. Välj **Tokenkonfiguration**i avsnittet **Hantera** .
1. Välj **Lägg till valfritt anspråk**.
1. För **tokentypen**väljer du **ID**.
1. Välj valfria anspråk `family_name` att `given_name`lägga till och .
1. Klicka på **Lägg till**.