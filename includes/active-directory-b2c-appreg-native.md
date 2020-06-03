---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 2981a46502ad7a7ab673e00bd1e8d2033ef2213d
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84317728"
---
Om du vill registrera ett program i din Azure AD B2C klient kan du använda vår nya enhetliga **Appregistreringar** upplevelse eller äldre **program (äldre)** . [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregtraining)

#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar**och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *nativeapp1*.
1. Under **konto typer som stöds**väljer du **konton i valfri organisations katalog eller någon identitets leverantör**.
1. Under **omdirigerings-URI**använder du List rutan för att välja **offentlig klient/ursprunglig (mobil & Desktop)**.
1. Ange en omdirigerings-URI med ett unikt schema. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Det finns två viktiga saker att tänka på när du väljer omdirigerings-URI:
    * **Unikt**: schemat för omdirigerings-URI: n måste vara unikt för varje program. I det här exemplet är `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` schemat. Du måste följa det här mönstret. Om två program delar samma schema får användaren välja program. Om användaren väljer felaktigt går det inte att logga in.
    * **Slutfört**: omdirigerings-URI: n måste ha både ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen. Till exempel `//oauth/` fungerar det `//oauth` inte. Ta inte med specialtecken i URI, till exempel under streck.
1. Under **behörigheter**markerar du kryss rutan *bevilja administratörs medgivande till OpenID och offline_access behörighet* .
1. Välj **Registrera**.

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **program (bakåtkompatibelt)** och välj sedan **Lägg till**.
1. Ange ett namn på programmet. Till exempel *nativeapp1*.
1. För **Native Client**väljer du **Ja**.
1. Ange en **anpassad omdirigerings-URI** med ett unikt schema. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Det finns två viktiga saker att tänka på när du väljer omdirigerings-URI:
    * **Unikt**: schemat för omdirigerings-URI: n måste vara unikt för varje program. I det här exemplet är `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` schemat. Du måste följa det här mönstret. Om två program delar samma schema får användaren välja program. Om användaren väljer felaktigt går det inte att logga in.
    * **Slutfört**: omdirigerings-URI: n måste ha både ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen. Till exempel `//oauth/` fungerar det `//oauth` inte. Ta inte med specialtecken i URI, till exempel under streck.
1. Välj **Skapa**.