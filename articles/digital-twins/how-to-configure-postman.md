---
title: Så här konfigurerar du Postman – Azure Digitals flätar | Microsoft Docs
description: 'Lär dig hur du konfigurerar och använder Postman för att testa Azure Digitals dubbla API: er.'
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80297158"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Så här konfigurerar du Postman för digital Azures dubbla

Den här artikeln beskriver hur du konfigurerar Postman REST-klienten att interagera med och testa Azure Digitals hanterings-API: er. Mer specifikt beskrivs:

* Så här konfigurerar du ett Azure Active Directory program att använda det implicita tilldelnings flödet för OAuth 2,0.
* Använda Postman REST-klienten för att skapa token HTTP-begäranden till dina hanterings-API: er.
* Hur du använder Postman för att göra flerdelade POST-förfrågningar till dina hanterings-API: er.

## <a name="postman-summary"></a>Postman-Sammanfattning

Kom igång med Azure Digitals dubbla med ett REST-klient verktyg som [Postman](https://www.getpostman.com/) för att förbereda din lokala test miljö. Postman-klienten hjälper dig att snabbt skapa komplexa HTTP-begäranden. Ladda ned Skriv bords versionen av Postman-klienten genom att gå till [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) är ett rest-testverktyg som söker efter funktioner för nyckel-http-begäran i ett användbart Skriv bords-och plugin-baserade användar gränssnitt.

Med Postman-klienten kan Solutions-utvecklare ange typen av HTTP-begäran (*post*, *Get*, *Update*, *patch*och *Delete*), API-slutpunkt för anrop och användning av TLS. Postman stöder även tillägg av HTTP-begärandehuvuden, parametrar, formulär data och organ.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurera Azure Active Directory att använda det implicita tilldelnings flödet för OAuth 2,0

1. Följ stegen i [snabb](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) starten för att skapa och konfigurera ett Azure Active Directory-program. Du kan också återanvända en befintlig app-registrering.

    [![Konfigurera en ny Postman omdirigerings-URI](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Lägg nu till en **omdirigerings-URI** till `https://www.getpostman.com/oauth2/callback`.

1. Markera kryss rutan **implicit bevilja** > **åtkomst-token** för att tillåta att det implicita tilldelnings flödet för OAuth-2,0 används. Välj **Konfigurera**och sedan **Spara**.

1. Kopiera **klient-ID** för din Azure Active Directory-app.

## <a name="obtain-an-oauth-20-token"></a>Hämta en OAuth 2,0-token

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Konfigurera och konfigurera Postman för att hämta en Azure Active Directory-token. Därefter gör du en autentiserad HTTP-begäran till Azure Digitals med den hämtade token:

1. Kontrol lera att **behörighets webb adressen** är korrekt. Det bör ha formatet:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name  | Ersätt med | Exempel |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Namnet på din klient organisation eller organisation. Använd det människo egna namnet i stället för det alfanumeriska **klient-ID: t** för din Azure Active Directory app-registrering. | `microsoft` |

1. Gå till [www.getpostman.com](https://www.getpostman.com/) för att ladda ned appen.

1. Vi vill bli GET-begäran. Välj fliken **auktorisering** , välj OAuth 2,0 och välj sedan **Hämta ny**åtkomsttoken.

    | Field  | Värde |
    |---------|---------|
    | Grant Type (Typ av beviljande) | `Implicit` |
    | Callback URL (Webbadress för återanrop) | `https://www.getpostman.com/oauth2/callback` |
    | Auth URL (Auktoriseringswebbadress) | Använd **URL:** en för auktorisering från **steg 1** |
    | Klientorganisations-ID | Använd **program-ID:** t för den Azure Active Directory-app som skapades eller återanvändas från föregående avsnitt |
    | Omfång | Lämna tomt |
    | Status | Lämna tomt |
    | Klientautentisering | `Send as Basic Auth header` |

1. Klienten bör nu visas som:

    [![Exempel på Postman client-token](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Välj **Request Token** (Begär token).
  
1. Rulla nedåt och välj **Använd token**.

## <a name="make-a-multipart-post-request"></a>Gör en flerdelade POST-begäran

När du har slutfört de föregående stegen konfigurerar du Postman att göra en autentiserad HTTP multipart-begäran:

1. På fliken **sidhuvud** lägger du till en http-begäran rubrik nyckel **innehåll-typ** med `multipart/mixed`värde.

   [![Ange innehålls typ för multipart/blandat](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Serialisera icke-text data i filer. JSON-data sparas som en JSON-fil.
1. Under fliken **brödtext** väljer `form-data`du. 
1. Lägg till varje fil genom att tilldela ett **nyckel** namn `File`och välja.
1. Välj sedan varje fil via knappen **Välj fil** .

   [![Exempel på Postman-klientens formulär text](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Postman-klienten kräver inte att flerdelade segment har en manuellt tilldelad **innehålls typ** eller **innehålls-disposition**.
   > * Du behöver inte ange dessa rubriker för varje del.
   > * Du måste välja `multipart/mixed` eller en annan lämplig **innehålls typ** för hela begäran.

1. Slutligen väljer du **Skicka** för att skicka din multipart HTTP POST-begäran. Status koden för `200` eller `201` anger en lyckad begäran. Lämpligt svars meddelande visas i klient gränssnittet.

1. Verifiera dina HTTP POST-begär ande data genom att anropa API-slutpunkten: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om digitala dubbla hanterings-API: er och hur du använder dem läser [du så här använder du Azure Digitals hanterings-API: er](how-to-navigate-apis.md).

- Använd multipart-begäranden för att [lägga till blobar i entiteter i Azure Digitals dubbla enheter](./how-to-add-blobs.md).

- Läs [autentisera med API: er](./security-authenticating-apis.md)om du vill lära dig mer om att autentisera med hanterings-API: er.
