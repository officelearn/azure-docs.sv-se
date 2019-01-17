---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: f23d2b02bc2a23c5333a48a50532c03f3aa6a031
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355758"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Under **hantera**väljer **användarflöden** och klicka på +**nytt användarflöde**.

![Välj nytt användarflöde](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow.png)

På den **rekommenderas** fliken **logga och logga in**.

![Välj logga in och logga in användarflödet](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow-type.png)

Ange ett användarflöde **namn** för programmet. Ange till exempel `SiUpIn`.

Under **identitetsprovidrar** och kontrollera **e-postregistrering**. Du kan också välja leverantörer via sociala nätverk om det redan har konfigurerats.

Under **multifaktorautentisering**, väljer du antingen **aktiverad** eller **inaktiverad**.

![Ange ett namn och välj e-postregistrering som identitetsprovider](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-name-identity-providers.png)

Under **användarattribut och anspråk**väljer **visa fler** att se en fullständig lista över attribut och anspråk som du kan välja bland.

I den **samla in attributet** kolumn, Välj de attribut som du vill samla in från konsumenten under registreringen. Välj till exempel **land/region**, **visningsnamn** och **postnummer**.

I den **returnerar anspråk** kolumn, Välj anspråk som du vill ska returneras i de auktoriseringstoken som skickas tillbaka till programmet efter en lyckad registrering eller inloggning upplevelse. Välj till exempel **visningsnamn**, **identitetsprovidrar**, **postnummer**, **ny användare** och **användarobjekt-id**.

Klicka på **OK**.

![Välj vissa användarattribut och anspråk och klicka på OK-knapp](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-all-attributes.png)

Klicka på **skapa** att lägga till användarflödet. Användarflödet har listats som **B2C_1_SiUpIn**. Prefixet **B2C_1_** läggs till i namnet.

Välj **kör användarflödet**. Kontrollera inställningarna i tabellen och klicka sedan på **kör användarflödet**.

![Välj Kör användarflödet](media/active-directory-b2c-create-sign-in-sign-up-policy/run-user-flow-b2c-signup-signin.png)

| Inställning      | Värde  |
| ------------ | ------ |
| **Programmet** | Contoso B2C-app |
| **Svarswebbadress** | `https://localhost:44316/` |

En ny webbläsarflik öppnas och du kan kontrollera hur registreringen eller inloggningen går till.

> [!NOTE]
> Det tar till en minut för användare kan du skapa flöden och uppdateringar ska börja gälla.
>