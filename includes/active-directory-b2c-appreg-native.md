---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: b83a6b9185eb4ef127da1dd3b55aba4e8b2945f9
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326286"
---
1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter **Lägg till**.
1. Ange ett namn på programmet. Till exempel *nativeapp1*.
1. För **Native Client**väljer du **Ja**.
1. Ange en **anpassad omdirigerings-URI** med ett unikt schema. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Det finns två viktiga saker att tänka på när du väljer omdirigerings-URI:
    1. **Unikt**: Schemat för omdirigerings-URI: n måste vara unikt för varje program. I det här exemplet är `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` schemat. Du måste följa det här mönstret. Om två program delar samma schema får användaren välja program. Om användaren väljer felaktigt går det inte att logga in.
    1. **Slutför**: Omdirigerings-URI: n måste ha både ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen. Till exempel fungerar `//oauth/` medan `//oauth` Miss lyckas. Ta inte med specialtecken i URI, till exempel under streck.
1. Välj **Skapa**.
