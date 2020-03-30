---
title: Så här använder du referensdokumentation för Swagger - Azure Digital Twins | Microsoft-dokument
description: Förstå hur du använder Azure Digital Twins Swagger referensdokumentation.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023301"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Referensdokumentation för Azure Digital Twins Swagger

Varje etablerad Azure Digital Twins-instans innehåller sin egen automatiskt genererade Swagger-referensdokumentation.

[Swagger](https://swagger.io/)eller [OpenAPI](https://www.openapis.org/)– förenar komplex API-information till en interaktiv och språkoberoende referensresurs. Swagger tillhandahåller kritiskt referensmaterial som JSON-nyttolaster, HTTP-metoder och specifika slutpunkter som ska användas för att utföra åtgärder mot ett API.

## <a name="swagger-summary"></a>Swagger sammanfattning

Swagger ger en interaktiv sammanfattning av ditt API, som innehåller:

* API- och objektmodellinformation.
* REST API-slutpunkter som anger nödvändiga begäran nyttolaster, rubriker, parametrar, kontextsökvägar och HTTP-metoder.
* Testning av API-funktioner.
* Exempel på svarsinformation som används för att validera och bekräfta HTTP-svar.
* Information om felkod.

Swagger är ett praktiskt verktyg för att hjälpa till med utveckling och testning av samtal till Azure Digital Twins Management API:er.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Referensmaterial

Det automatiskt genererade Swagger-referensmaterialet ger en snabb översikt över viktiga begrepp, tillgängliga hanterings-API-slutpunkter och en beskrivning av varje objektmodell för att underlätta utveckling och testning.

En kortfattad sammanfattning beskriver API.

[![Swagger sammanfattning och API översiktsinformation](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Api-objektmodeller för hantering visas också.

[![Swagger modeller listade längst ner i Swagger UI](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Du kan välja varje listad objektmodell för en mer detaljerad sammanfattning av nyckelattribut.

[![Swagger modeller expanderas för att läsa innehållet i modeller](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

De genererade Swagger-objektmodellerna är praktiska att läsa alla tillgängliga Azure Digital [Twins-objekt och API:er](./concepts-objectmodel-spatialgraph.md). Utvecklare kan använda den här resursen när de bygger lösningar på Azure Digital Twins.

## <a name="endpoint-summary"></a>Sammanfattning av slutpunkt

Swagger ger också en grundlig översikt över alla slutpunkter som utgör hanterings-API:er.

Varje listad slutpunkt innehåller också den begärda information som krävs, till exempel:

* Nödvändiga parametrar.
* Obligatoriska parameterdatatyper.
* HTTP-metod för att komma åt resursen.

[![Swagger-slutpunkter som visas i Swagger UI](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Välj varje resurs om du vill visa deras ytterligare innehåll för att få en mer detaljerad översikt.

## <a name="use-swagger-to-test-endpoints"></a>Använda Swagger för att testa slutpunkter

En av de kraftfulla funktioner Swagger ger är möjligheten att testa en API-slutpunkt direkt via dokumentationen användargränssnittet.

När du har valt en viss slutpunkt visas knappen **Prova den.**

[![Swagger Prova knappen](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Expandera det avsnittet om du vill visa indatafält för varje obligatorisk och valfri parameter. Ange rätt värden och välj **Kör**.

[![Swagger Prova resultat exempel](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

När du har testat testet kan du validera svarsdata.

## <a name="swagger-response-data"></a>Svarsdata för Swagger

Varje listad slutpunkt innehåller också svarstextdata för att validera din utveckling och dina tester. Dessa exempel inkluderar statuskoder och JSON för lyckade HTTP-begäranden.

[![Exempel på Swagger JSON-svar](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Exemplen innehåller också felkoder för att felsöka eller förbättra misslyckade tester.

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 tillstånd

> [!NOTE]
> * Användarens huvudnamn som skapade Azure Digital Twins-resursen har en rolltilldelning för utrymmesadministratör och kan skapa ytterligare rolltilldelningar för andra användare. Dessa användare och deras roller kan ha behörighet att anropa API:erna.

1. Följ stegen i [snabbstarten](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) för att skapa och konfigurera ett Azure Active Directory-program. Du kan också återanvända en befintlig appregistrering.

1. Lägg till följande **Omdirigera URI** i registreringen av Azure Active Directory-appar:

    [![Registrera Swagger omdirigera url i AAD](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Namn  | Ersätt med | Exempel |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | URL:en för rest-API-dokumentation för hantering som finns i portalen  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Markera kryssrutan **Implicit bevilja** > **åtkomsttoken** om du vill att det implicita beviljandeflödet för OAuth 2.0 ska användas. Välj **Konfigurera**och **spara**sedan .

1. Kopiera **klient-ID:et** för Azure Active Directory-appen.

När du har slutfört Azure Active Directory-registreringen:

1. Välj knappen **Auktorisera** på skrytsidan.

    [![Välj knappen Swagger authorize](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Klistra in program-ID:t i **fältet client_id.**

    [![Swagger client_id fältet](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Du kommer då att omdirigeras till följande framgång modal.

    [![Swagger omdirigera modal](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Mer information om hur du testar begäranden som skyddas av OAuth 2.0 interaktivt läser du den [officiella dokumentationen](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Nästa steg

- Om du vill läsa mer om Azure Digital Twins-objektmodeller och spatial intelligence-diagrammet läser du [Förstå Azure Digital Twins-objektmodeller](./concepts-objectmodel-spatialgraph.md).

- Om du vill veta hur du autentiserar med ditt hanterings-API läser du [Autentisera med API: er](./security-authenticating-apis.md).
