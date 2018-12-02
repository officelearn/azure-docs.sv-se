---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0d9f0a24d84bd18bdf1fac84c744cc34a7d89ab3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742496"
---
Om du vill aktivera profilredigering i programmet kan du använda en **profilredigering** användarflödet. Det här användarflödet beskriver hur kunder går till profilredigering och innehållet i de token som programmet tar emot vid genomförda inloggningar.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Under **hantera**väljer **användarflöden** och klicka på +**nytt användarflöde**.

![Välj nytt användarflöde](media/active-directory-b2c-create-profile-editing-policy/add-b2c-new-user-flow.png)

På den **rekommenderas** fliken **profilredigering**.

Ange ett användarflöde **namn** för programmet. Ange till exempel `SiPe`.

Under **identitetsprovidrar**, kontrollera **inloggning från lokalt konto**. Du kan också välja leverantörer via sociala nätverk om det redan har konfigurerats.

![Välj Inloggning från lokalt konto som identitetsprovider och klicka på OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-identity-providers.png)

Under **användarattribut**, klickar du på **visa fler**. I den **samla in attributet** kolumnen väljer attribut konsumenten kan visa och redigera i sina profiler. Välj till exempel **land/region**, **visningsnamn** och **postnummer**.

I den **returnerar anspråk** kolumn, Välj anspråk som du vill ska returneras i de auktoriseringstoken som skickas tillbaka till programmet efter en slutförd redigering av profil. Välj till exempel **Visningsnamn**, **Postnummer**.

Klicka på **OK**.

![Välj några programanspråk och klicka på OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-user-attributes.png)

Klicka på **skapa** att lägga till användarflödet. Användarflödet har listats som **B2C_1_SiPe**. Prefixet **B2C_1_** läggs till i namnet.

Välj **kör användarflödet**. Kontrollera inställningarna i tabellen och klicka sedan på **kör användarflödet**.

![Välj användarflödet och kör den](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-run-user-flow.png)

| Inställning      | Värde  |
| ------------ | ------ |
| **Programmet** | Contoso B2C-app |
| **Svarswebbadress** | `https://localhost:44316/` |

En ny webbläsarflik öppnas och du kan kontrollera hur profilredigeringen går till.

> [!NOTE]
> Det tar till en minut för användare kan du skapa flöden och uppdateringar ska börja gälla.
>