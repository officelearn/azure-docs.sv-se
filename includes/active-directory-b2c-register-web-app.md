---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 4d5c8373f2a8f02ccfbe51cb970ab08744950e7e
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134451"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Du registrerar ditt webbprogram med hjälp av inställningarna i tabellen.

![Exempel på registreringsinställningar för ny webbapp](./media/active-directory-b2c-register-web-app/b2c-new-app-settings.png)

| Inställning      | Exempelvärde  | Beskrivning                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Namn** | Contoso B2C-app | Ange ett **namn** som beskriver programmet för konsumenterna. | 
| **Ta med webbapp/webb-API** | Ja | Välj **Ja** om det är ett webbprogram. |
| **Tillåt implicit flöde** | Ja | Välj **Ja** om [OpenID Connect-inloggning](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md) används för programmet |
| **Svarswebbadress** | `https://localhost:44316` | Svarswebbadresser är slutpunkter där Azure AD B2C returnerar de token som programmet begär. Ange [en korrekt](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url) **svarswebbadress**. I det här exemplet är din app lokal och lyssnar på port 44316. |

Klicka på **Skapa** för att registrera ditt program.

Ditt nyligen registrerade program visas i programlistan för B2C-klientorganisationen. Välj webbapp i listan. Webbprogrammet egenskapsruta visas.

![Webbappegenskaper](./media/active-directory-b2c-register-web-app/b2c-web-app-properties.png)

Anteckna det globala unika id:t **Programklients-id**. Använd det här id:t i programkoden.