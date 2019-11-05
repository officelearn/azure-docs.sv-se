---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 560e29be308277aedf8ac4e438f73d4ac5bc417f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475342"
---
Om du vill registrera ett program i din Azure AD B2C klient kan du använda den aktuella **program** upplevelsen eller vår nya enhetliga **Appregistreringar (förhands granskning)** . [Läs mer om för hands](https://aka.ms/b2cappregintro)versionen.

#### <a name="applicationstabapplications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter **Lägg till**.
1. Ange ett namn på programmet. Till exempel *testapp1*.
1. För **webbapp/webb-API**väljer du **Ja**.
1. Ange `https://jwt.ms` för **svars-URL**
1. Välj **Skapa**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar (för hands version)** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *testapp1*.
1. Välj **konton i en organisations katalog eller en identitets leverantör**.
1. Under **omdirigerings-URI**väljer du **webb**och anger sedan `https://jwt.ms` i text rutan URL.
1. Under **behörigheter**markerar du kryss rutan *bevilja administratörs medgivande till OpenID och offline_access behörigheter* .
1. Välj **Registrera**.

När program registreringen är klar aktiverar du det implicita tilldelnings flödet:

1. Under **Hantera**väljer du **autentisering**.
1. Välj **testa den nya upplevelsen** (om den visas).
1. Under **implicit beviljande**väljer du kryss rutorna för **åtkomst-tokens** och **ID-token** .
1. Välj **Spara**.