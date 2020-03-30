---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 03329252c0ed4231585d1717d9361a2aef35b36f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187020"
---
Om du vill registrera ett program i din Azure AD B2C-klient kan du använda den aktuella **programupplevelsen** eller vår nya upplevelse av enhetliga **appregistreringar (förhandsversion).** [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Program**och välj sedan **Lägg till**.
1. Ange ett namn på programmet. Till exempel *ROPC_Auth_app*.
1. För **Inbyggd klient**väljer du **Ja**.
1. Lämna de andra värdena som de är och välj sedan **Skapa**.
1. Registrera **PROGRAM-ID:et** för användning i ett senare steg.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Appregistreringar (förhandsgranskning)** och välj sedan **Ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *ROPC_Auth_app*.
1. Lämna de andra värdena som de är och välj sedan **Registrera**.
1. Registrera **program-ID:t (klient)** för användning i ett senare steg.
1. Välj **Autentisering**under **Hantera**.
1. Välj **Prova den nya upplevelsen** (om den visas).
1. Under **Standardklienttyp**väljer du **Ja** för att behandla programmet som en offentlig klient. Den här inställningen krävs för ROPC-flödet.
1. Välj **Spara**.