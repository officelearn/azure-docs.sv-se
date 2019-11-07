---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: a286f8d401ec98a5069c07b1a82acdf4af2b8185
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641859"
---
Webb-API-resurser måste vara registrerade i klientorganisationen innan de kan godkänna och svara på en begäran från en skyddad resurs från klientprogram som använder en åtkomsttoken.

Om du vill registrera ett program i din Azure AD B2C klient kan du använda den aktuella **program** upplevelsen eller vår nya enhetliga **Appregistreringar (förhands granskning)** . [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applicationstabapplications"></a>[Program](#tab/applications/)

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

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar (för hands version)** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *webapi1*.
1. Under **omdirigerings-URI**väljer du **webb**och anger sedan en slut punkt där Azure AD B2C ska returnera de token som programmet begär. I den här självstudien körs exemplet lokalt och lyssnar på `http://localhost:5000`.
1. Välj **Registrera**.
1. Registrera **program-ID: t (Client)** för användning i ett senare steg.

Aktivera sedan det implicita tilldelnings flödet:

1. Under **Hantera**väljer du **autentisering**.
1. Välj **testa den nya upplevelsen** (om den visas).
1. Under **implicit beviljande**väljer du kryss rutorna för **åtkomst-tokens** och **ID-token** .
1. Välj **Spara**.