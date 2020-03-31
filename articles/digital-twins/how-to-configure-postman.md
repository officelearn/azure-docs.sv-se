---
title: Konfigurerar Du Postman - Azure Digital Twins | Microsoft-dokument
description: Lär dig hur du konfigurerar och använder Postman för att testa Azure Digital Twins API:er.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297158"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Konfigurerar du Postman för Azure Digital Twins

I den här artikeln beskrivs hur du konfigurerar Rest-klienten för Postman att interagera med och testa Azure Digital Twins Management API:er. Närmare bestämt beskrivs det:

* Konfigurera ett Azure Active Directory-program för att använda OAuth 2.0 implicita bidragsflödet.
* Så här använder du Rest-klienten för Postman för att göra tokenbärande HTTP-begäranden till dina hanterings-API:er.
* Så här använder du Postman för att göra post-förfrågningar med flera delar till dina hanterings-API:er.

## <a name="postman-summary"></a>Sammanfattning av Brevbäraren

Kom igång med Azure Digital Twins med hjälp av ett REST-klientverktyg som [Postman](https://www.getpostman.com/) för att förbereda din lokala testmiljö. Postman-klienten hjälper till att snabbt skapa komplexa HTTP-begäranden. Ladda ner skrivbordsversionen av Postman-klienten genom att gå till [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) är en REST testverktyg som lokaliserar viktiga HTTP-begäran funktioner i ett användbart skrivbord och plugin-baserade GUI.

Genom Postman-klienten kan lösningsutvecklare ange vilken typ av HTTP-begäran (*POST*, *GET*, *UPDATE*, *PATCH*och *DELETE*), API-slutpunkt att anropa och använda TLS. Postman stöder också att lägga till HTTP-begäranden, parametrar, formulärdata och organ.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurera Azure Active Directory för att använda implicit tilldelningsflöde OAuth 2.0

1. Följ stegen i [snabbstarten](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) för att skapa och konfigurera ett Azure Active Directory-program. Du kan också återanvända en befintlig appregistrering.

    [![Konfigurera en ny URI för efterhandsomdirigering](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Lägg nu till en `https://www.getpostman.com/oauth2/callback` **Redirect URI** till .

1. Markera kryssrutan **Implicit bevilja** > **åtkomsttoken** om du vill att det implicita beviljandeflödet för OAuth 2.0 ska användas. Välj **Konfigurera**och **spara**sedan .

1. Kopiera **klient-ID:et** för Azure Active Directory-appen.

## <a name="obtain-an-oauth-20-token"></a>Skaffa en OAuth 2.0-token

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Konfigurera och konfigurera Postman för att hämta en Azure Active Directory-token. Gör sedan en autentiserat HTTP-begäran till Azure Digital Twins med den förvärvade token:

1. Kontrollera att **url:en för auktorisering** är korrekt. Det bör ta formatet:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Namn  | Ersätt med | Exempel |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Namnet på din klient eller organisation. Använd det människovänliga namnet i stället för det alfanumeriska **klient-ID:n** för registreringen av Azure Active Directory-appar. | `microsoft` |

1. Gå till [www.getpostman.com](https://www.getpostman.com/) för att ladda ner appen.

1. Vi vill göra GET begäran. Välj fliken **Auktorisering,** välj OAuth 2.0 och välj sedan **Hämta ny åtkomsttoken**.

    | Field  | Värde |
    |---------|---------|
    | Grant Type (Typ av beviljande) | `Implicit` |
    | Callback URL (Webbadress för återanrop) | `https://www.getpostman.com/oauth2/callback` |
    | Auth URL (Auktoriseringswebbadress) | Använda **URL:en auktorisering** från **steg 1** |
    | Klientorganisations-ID | Använda **program-ID:t** för Azure Active Directory-appen som skapades eller återanvänddes från föregående avsnitt |
    | Omfång | Lämna tomt |
    | Status | Lämna tomt |
    | Klientautentisering | `Send as Basic Auth header` |

1. Klienten ska nu visas som:

    [![Exempel på postmanklienttoken](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Välj **Request Token** (Begär token).
  
1. Bläddra nedåt och välj **Använd token**.

## <a name="make-a-multipart-post-request"></a>Gör en postbegäran för flera delar

När du har slutfört föregående steg konfigurerar du Postman så att en autentrad HTTP-multipart POST-begäran har slutförts:

1. Lägg till en **innehållstyp** för HTTP-begärandens `multipart/mixed` **huvudnyckel** under fliken Rubriker med värde .

   [![Ange innehållstyp med flera delar/blandad](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Serialisera icke-textdata till filer. JSON-data skulle sparas som en JSON-fil.
1. Välj `form-data`Under fliken **Brödtext.** 
1. Lägg till varje fil genom att `File`tilldela ett **nyckelnamn** och välja .
1. Markera sedan varje fil via knappen **Välj fil.**

   [![Exempel på postmanklientformulär](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Postman-klienten kräver inte att segment med flera delar har en manuellt tilldelad **innehållstyp** eller **contentdisposition**.
   > * Du behöver inte ange dessa rubriker för varje del.
   > * Du måste `multipart/mixed` välja eller annan lämplig **innehållstyp** för hela begäran.

1. Välj slutligen **Skicka** för att skicka din HTTP POST-begäran i flera delar. En statuskod `200` `201` för eller anger en lyckad begäran. Rätt svarsmeddelande visas i klientgränssnittet.

1. Validera dina HTTP POST-begärandedata genom att anropa API-slutpunkten: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Digital Twins management API:er och hur du använder dem läser du [Så här använder du Azure Digital Twins management API:er](how-to-navigate-apis.md).

- Använd flerdelade begäranden om att lägga till [blobbar i Azure Digital Twins entiteter](./how-to-add-blobs.md).

- Om du vill veta mer om hur du autentiserar med api:erna för hantering läser du [Autentisera med API:er](./security-authenticating-apis.md).
