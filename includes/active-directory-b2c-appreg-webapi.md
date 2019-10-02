---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/30/2019
ms.author: marsma
ms.openlocfilehash: 4ac69b51e69031a1a4ce83a2c09dc6cc010ccd62
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720265"
---
Webb-API-resurser måste vara registrerade i klientorganisationen innan de kan godkänna och svara på en begäran från en skyddad resurs från klientprogram som använder en åtkomsttoken.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter **Lägg till**.
1. Ange ett namn på programmet. Till exempel *webapi1*.
1. För **webbapp/webb-API**väljer du **Ja**.
1. Välj **Ja**för **Tillåt implicit flöde**.
1. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. I den här självstudien körs exemplet lokalt och lyssnar på `https://localhost:5000`.
1. För **app-ID-URI**lägger du till API-slutpunkt-ID: t till den URI som visas. I den här självstudien anger du `api`, så att hela URI: n liknar `https://contosob2c.onmicrosoft.com/api`.
1. Välj **Skapa**.
1. Registrera **program-ID** för användning i ett senare steg.
