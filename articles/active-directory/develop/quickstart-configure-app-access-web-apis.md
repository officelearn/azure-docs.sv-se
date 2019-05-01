---
title: 'Konfigurera ett program att ha åtkomst till webb-API: er – Microsoft identity-plattformen'
description: Lär dig hur du konfigurerar en app som har registrerats på Microsoft Identity Platform att inkludera omdirigerings-URI, autentiseringsuppgifter eller behörigheter för åtkomst till webb-API:er.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e5adafc251735fd25b819921514bf6d1d3c3955
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918871"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Snabbstart: Konfigurera ett klientprogram för att komma åt webb-API:er

För att ett webbklientprogram eller ett konfidentiellt webbprogram ska kunna delta i ett flöde för auktoriseringsbeviljande (och hämta en åtkomsttoken) måste det kunna etablera säkra autentiseringsuppgifter. Standardmetoden för autentisering som stöds av Azure-portalen är klient-ID + hemlig nyckel.

Dessutom, innan en klient kan komma åt ett webb-API som görs tillgängligt av ett resursprogram (till exempel Microsoft Graph API) säkerställer ramverket för medgivande att klienten det behörighetsbeviljande som krävs, baserat på de begärda behörigheterna. Som standard kan alla appar välja behörigheter från Microsoft Graph API. [Graph API-behörigheten ”Logga in och läs användarprofil”](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) väljs som standard. Du kan välja mellan [två typer av behörigheter](developer-glossary.md#permissions) för varje önskat webb-API:

* **Programbehörigheter** – Klientprogrammet behöver komma åt webb-API:et direkt som sig självt (ingen användarkontext). Den här typen av behörighet kräver administratörens godkännande och är inte heller tillgängligt för offentliga klientprogram (dator eller mobil).
* **Delegerade behörigheter** – Klientprogrammet behöver komma åt webb-API:et som den inloggade användaren men med åtkomst som begränsas av den valda behörigheten. Den här typen av behörighet kan beviljas av en användare såvida inte behörigheten kräver administratörens godkännande.

  > [!NOTE]
  > Att lägga till en delegerad behörighet till ett program ger automatiskt medgivande till användare i klientorganisationen. Användarna måste fortfarande manuellt ge medgivande för de tillagda delegerade behörigheterna vid körning såvida inte administratören beviljas medgivande för alla användares räkning.

I den här snabbstarten visas hur du konfigurerar din app för att:

* [Lägga till omdirigerings-URI:er för appen](#add-redirect-uris-to-your-application)
* [Lägg till autentiseringsuppgifter i ditt webbprogram](#add-credentials-to-your-web-application)
* [Lägga till behörigheter för att få åtkomst till webb-API:er](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar kontrollerar du att följande krav är uppfyllda:

* Lär dig mer om [behörigheter och medgivande](v2-permissions-and-consent.md) som stöds, vilket är viktigt att förstå när du skapar appar som måste användas av andra användare eller appar.
* Ha en klientorganisation som har appar som är registrerade till den.
  * Om du inte har några registrerade appar kan du [få information om hur du registrerar appar på Microsoft Identity Platform](quickstart-register-app.md).
* Registrera dig för förhandsversionsfunktionerna appregistreringar i Azure-portalen. Stegen i den här snabbstarten motsvarar det nya användargränssnittet och fungerar bara om du använder förhandsversionsfunktionerna.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logga in på Azure-portalen och välj appen

Innan du kan konfigurera appen gör du följande:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller en personligt Microsoft-konto.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten och sedan **Appregistreringar (förhandsversion)**.
1. Leta reda på och välj den app du vill konfigurera. När du har valt appen ser du dess **översikt** eller huvudregistreringssida.
1. Följ stegen för att konfigurera appen för åtkomst till webb-API:er: 
    * [Lägga till omdirigerings-URI:er för appen](#add-redirect-uris-to-your-application)
    * [Lägg till autentiseringsuppgifter i ditt webbprogram](#add-credentials-to-your-web-application)
    * [Lägga till behörigheter för att få åtkomst till webb-API:er](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Lägga till omdirigerings-URI:er för appen

[![Lägga till anpassade omdirigerings-URI:er för webbappar och offentliga klientappar](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png#lightbox)

Lägga till en omdirigerings-URI för appen:

1. På appens **översiktssida** väljer du avsnittet **Autentisering**.

1. Gör följande för att lägga till en anpassad omdirigerings-URI för webbappar och offentliga klientprogram:

   1. Leta reda på avsnittet **Omdirigerings-URI**.
   1. Välj den typ av app du skapar **webb** eller **offentlig klient (mobil och dator)**.
   1. Ange omdirigerings-URI för appen.
      * För webbappar anger du grundläggande URL för appen. Till exempel kan `http://localhost:31544` vara URL för en webbapp som körs på din lokala dator. Användare skulle då använda den här URL:en för att logga in till ett webbklientprogram.
      * För offentliga appar anger du den URI som används av Azure AD för att returnera tokensvar. Ange ett värde som är specifikt för ditt program, till exempel https://MyFirstApp.

1. Gör följande för att välja mellan föreslagna omdirigerings-URI:er för offentliga klienter (mobil, dator):

    1. Leta reda på avsnittet för **föreslagna omdirigerings-URI:er för offentliga klienter (mobil, dator)**.
    1. Välj en eller flera lämpliga omdirigerings-URI:er med hjälp av kryssrutorna.

## <a name="add-credentials-to-your-web-application"></a>Lägga till autentiseringsuppgifter i webbappen

[![Lägga till certifikat och klienthemligheter](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png#lightbox)

Så här lägger du till en autentiseringsuppgift för webbappen:

1. På appens **översiktssida** väljer du avsnittet för **certifikat och hemligheter**.

1. Gör följande för att lägga till ett certifikat:

    1. Välj **Ladda upp certifikat**.
    1. Välj den fil som du vill ladda upp. Den måste vara någon av följande filtyper: .cer, .pem eller .crt.
    1. Välj **Lägg till**.

1. Gör följande för att lägga till en klienthemlighet:

    1. Välj **Ny klienthemlighet**.
    1. Lägg till en beskrivning för din klienthemlighet.
    1. Välj en varaktighet.
    1. Välj **Lägg till**.

> [!NOTE]
> Kolumnen längst till höger innehåller nyckelvärdet när du har sparat konfigurationsändringarna. **Se till att kopiera värdet** för användning i din klientprogramkod eftersom den inte är tillgänglig när du har lämnat den här sidan.

## <a name="add-permissions-to-access-web-apis"></a>Lägga till behörigheter för att få åtkomst till webb-API:er

[![Lägga till API-behörigheter](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png)](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png#lightbox)

Så lägger du till behörigheter för att komma åt resurs-API:er från klienten:

1. På appens **översiktssida** väljer du **API-behörigheter**.
1. Välj knappen **Lägg till en behörighet**.
1. Som standard kan du i vyn välja mellan **Microsoft-API:er**. Välj det avsnitt med API:er du är intresserad av:
    * **Microsoft-API:er** – Du kan välja behörigheter för Microsoft-API:er som Microsoft Graph.
    * **API:er som min organisation använder** – Du kan välja behörigheter för API:er som har exponerats av din organisation, eller API som din organisation har integrerats med.
    * **Mina API:er** – Du kan välja behörigheter för API:er som du har exponerat.
1. När du har valt API:er visas sidan **Begär API-behörigheter**. Om API:et exponerar både delegerade programbehörigheter. Välj vilken typ av behörighet som appen behöver.
1. Välj **Lägg till behörigheter** när du är klar. Du återgår till sidan **API-behörigheter**, där behörigheterna har sparats och lagts till i tabellen.

## <a name="next-steps"></a>Nästa steg

Läs mer om dessa andra relaterade snabbstarter för apphantering:

* [Registrera en app på Microsoft Identity Platform](quickstart-register-app.md)
* [Konfigurera en app att exponera webb-API:er](quickstart-configure-app-expose-web-apis.md)
* [Ändra konton som stöds av en app](quickstart-modify-supported-accounts.md)
* [Ta bort en app registrerad på Microsoft Identity Platform](quickstart-remove-app.md)

Mer information om de två Azure AD-objekt som representerar ett registrerat program och relationen mellan dem finns i [Programobjekt och tjänsthuvudnamnsobjekt](app-objects-and-service-principals.md).

Mer information om de varumärkesriktlinjer som du bör använda när du utvecklar program med Azure Active Directory finns i [Varumärkesriktlinjer för program](howto-add-branding-in-azure-ad-apps.md).
