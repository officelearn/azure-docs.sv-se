---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: fcd71f74e0b00934958828024094773e42496b66
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51017278"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Du registrerar ditt webb-API med hjälp av inställningarna i tabellen.

![Exempel på registreringsinställningar för nytt webb-api](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| Inställning      | Exempelvärde  | Beskrivning                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Namn** | Contoso B2C-API | Ange ett **namn** som beskriver API:t för konsumenterna. | 
| **Ta med webbapp/webb-API** | Ja | Välj **Ja** om det är ett webb-API. |
| **Tillåt implicit flöde** | Ja | Välj **Ja** om [OpenID Connect-inloggning](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md) används för programmet |
| **Svarswebbadress** | `https://localhost:44316/` | Svarswebbadresser är slutpunkter där Azure AD B2C returnerar de token som programmet begär. I det här exemplet är ditt webb-API lokalt och lyssnar på port 44316. |
| **URI för app-id** | api | URI för app-id är identifieraren som används för ditt webb-API. Den fullständiga URI-identifieraren inklusive domänen skapas åt dig. |

Klicka på **Skapa** för att registrera ditt program.

Ditt nyligen registrerade program visas i programlistan för B2C-klientorganisationen. Välj webb-API i listan. API:ets egenskapsruta visas.

![Egenskaper för webb-API](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

Anteckna det globala unika id:t **Programklients-id**. Använd det här id:t i programkoden.