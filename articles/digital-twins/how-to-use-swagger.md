---
title: Om du använder Azure Digital Twins Swagger | Microsoft Docs
description: Hur du använder Azure Digital Twins Swagger
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 3bc365c204ab75a2f136c3e26c4b598b25f66114
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093497"
---
# <a name="how-to-use-azure-digital-twins-swagger"></a>Hur du använder Azure Digital Twins Swagger

Varje etablerade Azure Digital Twins-instans innehåller en egen automatiskt genererade Swagger-referensdokumentation.

[Swagger](https://swagger.io/) (eller [OpenAPI](https://www.openapis.org/)) unites komplex API-information till en resurs för interaktiva och språkoberoende referens. Mer specifikt innehåller Swagger viktiga referensmaterial om vilken JSON-nyttolaster, HTTP-metoder och slutpunkter du använder för att utföra åtgärder mot ett API.

## <a name="swagger-summary"></a>Swagger-sammanfattning

Swagger innehåller en interaktiv sammanfattning av dina API: et, till exempel:

* Information om API: et och objektmodell.
* REST API-slutpunkter som du anger måste begäran nyttolaster, rubriker, parametrar, kontext sökvägar och HTTP-metoder.
* Testning av API-funktioner.
* Exempel Svarsinformation för validering och bekräftar HTTP-svar.
* Information om felet.

Swagger är därför ett praktiskt verktyg som hjälper till med utveckling och testning anrop till API Management.

> [!TIP]
> För referens tillhandahåller en Swagger-förhandstitt att demonstrera API-funktionen.
> Den finns på [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Du kan komma åt dina egna, genererade, Management API Swagger-dokumentationen på:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| *dittinstansnamn* | Namnet på din digitala Twins för Azure-instans |
| *yourLocation* | Vilken server-region som din instans är värd för |

## <a name="reference-material"></a>Referensmaterial

Automatiskt genererade referensmaterial förklarar viktiga begrepp och objektmodeller.

En kortfattad sammanfattning beskriver API: et:

![Swagger upp][1]

Core API-objektmodeller visas också:

![Swagger-modeller][2]

Du kan klicka i varje listad objektmodell för en mer detaljerad översikt över viktiga punkter:

![Swagger-modell][3]

Den genererade Swagger objektmodeller bekvämt att se alla tillgängliga Azure Digital Twins [objekt och API: er](./concepts-objectmodel-spatialgraph.md). Det är en fantastisk resurs för utvecklare att använda när du skapar lösningar på Azure Digital Twins.

## <a name="endpoint-summary"></a>Sammanfattning av slutpunkt

Swagger innehåller också en omfattande översikt över alla slutpunkter som ska utgöra API: et.

Varje listad slutpunkt innehåller också informationen krävs till exempel:

* Obligatoriska parametrar.
* Den begärda parameterdatatyper.
* HTTP-metoden för åtkomst till resursen.

![Swagger-slutpunkter][4]

Klicka på varje resurs om du vill se en mer detaljerad översikt.

## <a name="using-swagger-to-test-endpoints"></a>Använd Swagger för att testa slutpunkter

En av de kraftfulla funktioner som Swagger innehåller är möjligheten att **prova** eller testa en API-slutpunkt direkt via i UI-dokumentationen.

När du klickar på i en viss slutpunkt, ser du en **prova** knappen:

![Swagger-försök][5]

Expandera avsnittet öppnar inmatningsfält för alla obligatoriska och valfria parametrar. Därefter anger du värden och klickar på **kör**:

![Ett försök gjordes att swagger][6]

Du kan validera svarsdata efter att testet skulle köras.

## <a name="swagger-response-data"></a>Svarsdata för swagger

Varje listad slutpunkt innehåller också svarsdata för brödtexten för att verifiera din utveckling och tester. De här exemplen omfattar önskade statuskoder och JSON för lyckade HTTP-begäranden.

![Swagger-svar][7]

Exemplen omfattar också felkoder för att felsöka eller förbättra misslyckas tester.

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0-auktorisering

För att interaktivt testa förfrågningar mot API-resurserna som skyddas av OAuth 2.0, finns det [officiella dokumentationen](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Nästa steg

Läsa mer om Azure Digital Twins objektmodeller och rumsliga intelligence graph läsa [i den här artikeln](./concepts-objectmodel-spatialgraph.md).

Läs hur du autentiserar med ditt API Management, [autentisering med API: er](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.PNG
[2]: media/how-to-use-swagger/swagger_management_models.PNG
[3]: media/how-to-use-swagger/swagger_management_model.PNG
[4]: media/how-to-use-swagger/swagger_management_endpoints.PNG
[5]: media/how-to-use-swagger/swagger_management_try.PNG
[6]: media/how-to-use-swagger/swagger_management_tried.PNG
[7]: media/how-to-use-swagger/swagger_management_response.PNG
