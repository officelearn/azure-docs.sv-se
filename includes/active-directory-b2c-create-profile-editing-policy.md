---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 9ad715f47f2de9c6f9032ed07232f45fb33b0114
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133728"
---
Om du vill aktivera profilredigering i programmet kan du använda en **profilredigering** princip. Den här principen beskriver hur kunder går till profilredigering och innehållet i de token som programmet tar emot vid genomförda inloggningar.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

I principavsnittet i inställningar väljer du **Profilredigeringsprinciper** och klickar på **+ Lägg till**.

![Välj Profilredigeringsprinciper och klicka på Lägg till](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-policy.png)

Ange ett **namn** på principen för programmet. Ange till exempel `SiPe`.

Välj **Identitetsprovidrar** och markera **Inloggning från lokalt konto**. Du kan också välja leverantörer via sociala nätverk om det redan har konfigurerats. Klicka på **OK**.

![Välj Inloggning från lokalt konto som identitetsprovider och klicka på OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-identity-providers.png)

Välj **Profilattribut**. Välj attribut som användarna kan visa och redigera i sina profiler. Välj till exempel **land/region**, **visningsnamn** och **postnummer**. Klicka på **OK**.

![Välj några attribut och klicka på OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-attributes.png)

Välj **Programanspråk**. Välj anspråk som du vill ska returneras i de auktoriseringstoken som skickas tillbaka till programmet efter en slutförd redigering av profil. Välj till exempel **Visningsnamn**, **Postnummer**.

![Välj några programanspråk och klicka på OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-application-claims.png)

Klicka på **Skapa** för att lägga till principen. Principen visas i listan som **B2C_1_SiPe**. Prefixet **B2C_1_** läggs till i namnet.

Öppna principen genom att välja **B2C_1_SiPe**. Kontrollera inställningarna i tabellen och klicka på **Kör nu**.

![Välj princip och kör den](media/active-directory-b2c-create-profile-editing-policy/run-b2c-editing-policy.png)

| Inställning      | Värde  |
| ------------ | ------ |
| **Program** | Contoso B2C-app |
| **Välj svarswebbadress** | `https://localhost:44316/` |

En ny webbläsarflik öppnas och du kan kontrollera hur profilredigeringen går till.

> [!NOTE]
> Det kan ta någon minut att skapa en princip och innan uppdateringarna börjar gälla.
>