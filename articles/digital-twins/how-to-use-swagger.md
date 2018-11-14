---
title: Förstå hur du använder Azure Digital Twins Swagger | Microsoft Docs
description: Använd Azure Digital Twins Swagger
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 737c33f6b8cdf9bcb2530816601ff9b5eb994087
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624258"
---
# <a name="use-azure-digital-twins-swagger"></a>Använd Azure Digital Twins Swagger

Varje etablerade Azure Digital Twins-instans innehåller en egen automatiskt genererade Swagger-referensdokumentation.

[Swagger](https://swagger.io/), eller [OpenAPI](https://www.openapis.org/), unites komplex API-information till en resurs för interaktiva och språkoberoende referens. Swagger innehåller viktiga referensmaterial om vilken JSON-nyttolaster, HTTP-metoder och slutpunkter du använder för att utföra åtgärder mot ett API.

> [!IMPORTANT]
> Stöd för Swagger-autentisering är tillfälligt inaktiverad under den allmänna förhandsvisningen.

## <a name="swagger-summary"></a>Swagger-sammanfattning

Swagger innehåller en interaktiv sammanfattning av ditt API, vilket innefattar:

* Information om modellen API och objekt.
* REST API-slutpunkter som anger nödvändiga begäran nyttolaster, rubriker, parametrar, kontext sökvägar och HTTP-metoder.
* Testning av API-funktioner.
* Exempel Svarsinformation som används för att validera och bekräfta HTTP-svar.
* Information om felet.

Swagger är ett praktiskt verktyg som hjälper till med utveckling och testning anrop till API Management.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Referensmaterial

Automatiskt genererade referensmaterial förklarar viktiga begrepp och objektmodeller.

En kortfattad sammanfattning beskriver API: et.

![Swagger upp][1]

Core API-objektmodeller visas också.

![Swagger-modeller][2]

Du kan välja varje listad objektmodell för en mer detaljerad översikt över nyckelattribut.

![Swagger-modell][3]

De genererade Swagger-objektmodellerna bekvämt att se alla tillgängliga Azure Digital Twins [objekt och API: er](./concepts-objectmodel-spatialgraph.md). Utvecklare kan tillgängliggöra användning av den här resursen när de skapar lösningar på Azure Digital Twins.

## <a name="endpoint-summary"></a>Sammanfattning av slutpunkt

Swagger innehåller också en omfattande översikt över alla slutpunkter som ska utgöra API: et.

Varje listad slutpunkt innehåller också nödvändiga informationen, till exempel den:

* Obligatoriska parametrar.
* Den begärda parameterdatatyper.
* HTTP-metoden för åtkomst till resursen.

![Swagger-slutpunkter][4]

Välj varje resurs om du vill se en mer detaljerad översikt.

## <a name="use-swagger-to-test-endpoints"></a>Använd Swagger för att testa slutpunkter

En av kraftfulla funktioner som innehåller Swagger är möjligheten att testa en API-slutpunkt direkt via i UI-dokumentationen.

När du har valt en viss slutpunkt kan du se **prova**.

![Swagger-försök][5]

Expandera avsnittet att ta fram inmatningsfält för alla obligatoriska och valfria parametrar. Ange värden i enlighet med detta och väljer **kör**.

![Ett försök gjordes att swagger][6]

När du kör testet kan verifiera du svarsdata.

## <a name="swagger-response-data"></a>Svarsdata för swagger

Varje listad slutpunkt innehåller också svarsdata för brödtexten för att verifiera din utveckling och tester. De här exemplen omfattar statuskoder och JSON som du vill se för lyckade HTTP-begäranden.

![Swagger-svar][7]

Exemplen omfattar också felkoder för att felsöka eller förbättra misslyckas tester.

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0-auktorisering

Mer information om hur du testar interaktivt begäranden som skyddas av OAuth 2.0 finns i [officiella dokumentationen](https://swagger.io/docs/specification/authentication/oauth2/).

> [!NOTE]
> Stöd för OAuth 2.0-autentisering är tillfälligt inaktiverad under den allmänna förhandsvisningen.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Digital Twins objektmodeller och rumsliga intelligence diagrammet, läsa [förstå Azure Digital Twins objekt modeller](./concepts-objectmodel-spatialgraph.md).

Läs hur du autentiserar med ditt API Management, [autentisera med API: er](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.PNG
[2]: media/how-to-use-swagger/swagger_management_models.PNG
[3]: media/how-to-use-swagger/swagger_management_model.PNG
[4]: media/how-to-use-swagger/swagger_management_endpoints.PNG
[5]: media/how-to-use-swagger/swagger_management_try.PNG
[6]: media/how-to-use-swagger/swagger_management_tried.PNG
[7]: media/how-to-use-swagger/swagger_management_response.PNG
