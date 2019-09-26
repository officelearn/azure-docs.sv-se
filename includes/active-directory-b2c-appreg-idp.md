---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: 3b6f8ef5f2ec9f77dcba9c006fdd28b5ec668033
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315026"
---
Kommunikation med Azure AD B2C sker via ett program som du registrerar i B2C-klienten. Det här avsnittet innehåller valfria steg som du kan utföra för att skapa ett testprogram om du inte redan har gjort det.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter **Lägg till**.
1. Ange ett namn på programmet. Till exempel *testapp1*.
1. För **webbapp/webb-API**väljer du **Ja**.
1. För **svars-URL**anger du`https://jwt.ms`
1. Välj **Skapa**.
