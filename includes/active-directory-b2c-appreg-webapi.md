---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9367719b1a1050ce8abf36804b3d0d0d58c7484b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183401"
---
Webb-API-resurser måste vara registrerade i klientorganisationen innan de kan godkänna och svara på en begäran från en skyddad resurs från klientprogram som använder en åtkomsttoken.

Om du vill registrera ett program i din Azure AD B2C-klient kan du använda den aktuella **programupplevelsen** eller vår nya upplevelse av enhetliga **appregistreringar (förhandsversion).** [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Program**och välj sedan **Lägg till**.
1. Ange ett namn på programmet. Till exempel *webapi1*.
1. För **Webb-/webb-API**väljer du **Ja**.
1. För **Tillåt implicit flöde**väljer du **Ja**.
1. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. I den här självstudien körs exemplet lokalt och lyssnar på `https://localhost:5000`.
1. För **App ID URI**lägger du till en API-slutpunktsidentifierare i URI som visas. För den här `api`självstudien anger du , `https://contosob2c.onmicrosoft.com/api`så att hela URI liknar .
1. Välj **Skapa**.
1. Registrera **PROGRAM-ID:et** för användning i ett senare steg.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Appregistreringar (förhandsgranskning)** och välj sedan **Ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *webapi1*.
1. Under **Omdirigera URI**väljer du **Webb**och anger sedan en slutpunkt där Azure AD B2C ska returnera alla token som ditt program begär. I den här självstudien körs exemplet lokalt och lyssnar på `http://localhost:5000`.
1. Välj **Registrera**.
1. Registrera **program-ID:t (klient)** för användning i ett senare steg.

Aktivera sedan det implicita bidragsflödet:

1. Välj **Autentisering**under **Hantera**.
1. Välj **Prova den nya upplevelsen** (om den visas).
1. Markera **kryssrutorna Access-token** och **ID-token** under **Implicit tilldelning.**
1. Välj **Spara**.