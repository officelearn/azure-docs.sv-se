---
title: OpenAPI metadata i Azure Functions | Microsoft Docs
description: Översikt över OpenAPI stöd i Azure Functions
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: b6aacc536e589a2036aba5a0784a4ba71641a59e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Stöd för OpenAPI 2.0-metadata i Azure Functions (förhandsgranskning)
OpenAPI 2.0 (tidigare Swagger) stöd för metadata i Azure Functions är en förhandsfunktion som du kan använda för att skriva en OpenAPI 2.0-definition i en funktionsapp. Du kan sedan värd filen med hjälp av funktionen appen.

[OpenAPI metadata](http://swagger.io/) gör en funktion som är värd för en REST-API för att användas av en mängd annan programvara. Den här programvaran innehåller Microsoft-erbjudanden som PowerApps och [API Apps-funktionen i Azure App Service](../app-service/app-service-web-overview.md), utvecklingsverktyg från tredje part som [Postman](https://www.getpostman.com/docs/importing_swagger), och [många fler paket](http://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Vi rekommenderar att börja med den [komma igång-självstudiekurs](./functions-api-definition-getting-started.md) och sedan gå tillbaka till det här dokumentet för mer information om specifika funktioner.

## <a name="enable"></a>Aktivera stöd för OpenAPI definition
Du kan konfigurera alla inställningar för OpenAPI på den **API-Definition** sida i appen funktionen **plattformsfunktioner**.

Aktivera generering av en värdbaserad OpenAPI definition och en definition av Snabbstart för att ange **källa för API-definition** till **funktionen (förhandsgranskning)**. **Externa URL: en** gör att funktionen ska använda en OpenAPI definition som har hosted någon annanstans.

## <a name="generate-definition"></a>Generera en Swagger-stommen från din funktion metadata
Med hjälp av en mall kan du börja skriva din första OpenAPI definition. Funktionen definition mallen skapar en sparse OpenAPI definition med alla metadata i filen function.json för var och en av dina funktioner för HTTP-utlösare. Måste du fylla i mer information om din API från den [OpenAPI specifikationen](http://swagger.io/specification/), till exempel mallar för förfrågan och svar.

Stegvisa instruktioner finns i [komma igång-självstudiekurs](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Tillgängliga mallar

|Namn| Beskrivning |
|:-----|:-----|
|Genererade Definition|En definition av OpenAPI med maximal mängd information som kan härledas från befintliga metadata för den funktionen.|

### <a name="quickstart-details"></a>Med metadata i definitionen för genererade

I följande tabell representerar Azure portalinställningar och motsvarande data i function.json som är mappad till den genererade Swagger-stommen.

|Swagger.json|Portalens användargränssnitt|Function.json|
|:----|:-----|:-----|
|[Värd](http://swagger.io/specification/#fixed-fields-15)|**Funktionen appinställningar** > **Apptjänst inställningar** > **översikt** > **URL**|*Finns inte*
|[Sökvägar](http://swagger.io/specification/#paths-object-29)|**Integrera** > **valda http-metoder**|Bindningar: väg
|[Objektet i sökvägen](http://swagger.io/specification/#path-item-object-32)|**Integrera** > **flödesmallen**|Bindningar: metoder
|[Säkerhet](http://swagger.io/specification/#security-scheme-object-112)|**Nycklar**|*Finns inte*|
|operationID*|**Väg + tillåtna verb**|Väg + tillåtna verb|

\*Åtgärds-ID krävs endast för integrering med PowerApps och flöde.
> [!NOTE]
> X-ms-sammanfattning av tillägget ger ett namn i Logikappar, PowerApps och flöde.
>
> Läs mer i [anpassa din Swagger-definition för PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Använd CI/CD för att ange en API-definition

 Du måste aktivera API-definitions-värd i portalen innan du aktiverar källkontrollen att ändra din API-definition från källkontroll. Följ dessa anvisningar:

1. Bläddra till **API-Definition (förhandsgranskning)** i funktionen app-inställningarna.
  1. Ange **källa för API-definition** till **funktionen**.
  1. Klicka på **generera API-definition mallen** och sedan **spara** att skapa en definition för mallen för att ändra senare.
  1. Observera att din URL för API-definition och nyckel.
1. [Ställ in kontinuerlig integration/kontinuerlig distribution (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. Ändra swagger.json i källkontrollen på \site\wwwroot\.azurefunctions\swagger\swagger.json.

Nu kan ändringar av swagger.json i databasen hanteras av din funktionsapp på API: et definition URL: en och en nyckel som du antecknade i steg 1.c.

## <a name="next-steps"></a>Nästa steg
* [Komma igång-självstudiekurs](functions-api-definition-getting-started.md). Prova vår genomgång om du vill se en OpenAPI definition i åtgärden.
* [Azure Functions GitHub-lagringsplatsen](https://github.com/Azure/Azure-Functions/). Checka ut funktioner databasen för att ge oss feedback om API definition stöd för förhandsgranskning. Skapa ett GitHub-problem något du vill se uppdaterade.
* [Azure Functions för utvecklare](functions-reference.md). Läs mer om att koda funktioner och definiera utlösare och bindningar.
