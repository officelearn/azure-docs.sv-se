---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: d3a323a28d92e2a5834b65316d61c7d53a42aacf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183794"
---
Om du vill registrera ett program i din Azure AD B2C-klient kan du använda den aktuella **programupplevelsen** eller vår nya upplevelse av enhetliga **appregistreringar (förhandsversion).** [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Program**och välj sedan **Lägg till**.
1. Ange ett namn på programmet. Till exempel *nativeapp1*.
1. För **Inbyggd klient**väljer du **Ja**.
1. Ange en **anpassad omdirigerings-URI** med ett unikt schema. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Det finns två viktiga saker att tänka på när du väljer omdirigerings-URI:
    * **Unik:** Schemat för omdirigerings-URI:n måste vara unikt för varje program. I det här exemplet är `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` schemat. Du måste följa det här mönstret. Om två program delar samma schema får användaren välja program. Om användaren väljer felaktigt misslyckas inloggningen.
    * **Slutfört**: Omdirigeringen URI måste ha både ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen. Till exempel `//oauth/` fungerar `//oauth` medan misslyckas. Ta inte med specialtecken i URI, till exempel understreck.
1. Välj **Skapa**.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Appregistreringar (förhandsgranskning)** och välj sedan **Ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *nativeapp1*.
1. Under **Kontotyper som stöds**väljer du Konton i en **organisationskatalog eller någon identitetsprovider**.
1. Under **Omdirigera URI**använder du listrutan för att välja **Offentlig klient/inbyggt (mobilt & skrivbord).**
1. Ange en omdirigerings-URI med ett unikt schema. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Det finns två viktiga saker att tänka på när du väljer omdirigerings-URI:
    * **Unik:** Schemat för omdirigerings-URI:n måste vara unikt för varje program. I det här exemplet är `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` schemat. Du måste följa det här mönstret. Om två program delar samma schema får användaren välja program. Om användaren väljer felaktigt misslyckas inloggningen.
    * **Slutfört**: Omdirigeringen URI måste ha både ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen. Till exempel `//oauth/` fungerar `//oauth` medan misslyckas. Ta inte med specialtecken i URI, till exempel understreck.
1. Markera kryssrutan Bevilja *administratörsgodkännande för att öppna och offline_access behörigheter* under **Behörigheter.**
1. Välj **Registrera**.